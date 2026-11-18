---
title: 'Chats'
description: 'Vector 동기화된 콘텐츠와 Agent를 이용해 RAG 기반 채팅을 제공하는 Chats API 사용 방법을 설명합니다.'
category: 'ai'
slug: 'chats'
locale: 'ko'
apiVersion: 'v1'
order: 6
updatedAt: '2026-11-18'
---

# Chats

Chats API는 [Vector 동기화](/docs/ai/vector)된 콘텐츠를 시맨틱 검색으로 찾아 [Agent](/docs/ai/agents)가 답변을 생성하는 RAG(Retrieval-Augmented Generation) 채팅 API입니다. 이 가이드를 완료하면 채팅 세션을 생성하고, 메시지를 주고받고, 대화 이력을 조회할 수 있습니다.

**사전 조건:**

- 검색 대상 Table의 [Vector 동기화](/docs/ai/vector)가 완료되어 있어야 합니다.
- [Agent](/docs/ai/agents)가 생성되어 있어야 합니다.
- **시크릿** Project API Key(`sk-proj_…`)가 필요합니다. 채팅 생성과 메시지 전송은 `POST` 요청이라 게시용 키로는 호출할 수 없습니다. 브라우저가 아닌 서버에서 호출하세요. 게시용 키로는 메시지 이력 조회(`GET`)만 가능합니다.

---

## 동작 방식

1. 요청의 `message`로 Vector 동기화된 콘텐츠에서 관련 Contents를 검색합니다. 기본(`hybrid`)은 메시지를 임베딩한 의미 검색과 구조화 쿼리를 조합하며, `search_type`으로 BM25 키워드 검색 등 다른 방식을 선택할 수 있습니다. `threshold` 이상의 점수를 가진 항목을 최대 `count`개까지 사용합니다.
2. 검색된 콘텐츠와 대화 이력을 컨텍스트로 구성합니다.
3. `agent_id`로 지정한 Agent의 Instructions와 응답 파라미터에 따라 답변을 스트리밍으로 생성합니다.

`tableIds`를 지정하면 특정 Table로 검색 범위를 제한할 수 있습니다. 생략하면 Project의 모든 Table을 검색합니다.

### 검색 방식

`search_type`으로 1단계에서 콘텐츠를 찾는 방식을 선택합니다.

| 값                | 동작                                                                                                                                                                                                                                                                                       |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `vector`          | Vector 동기화된 콘텐츠를 임베딩 기반 의미 검색으로 찾습니다. 기존 RAG 동작과 동일하며 `threshold`가 적용됩니다.                                                                                                                                                                            |
| `query`           | 메시지를 Table 컬럼에 대한 구조화 필터(대시보드 필터와 같은 연산자: 숫자 범위, 날짜, 카테고리, 텍스트 일치, 정렬, 개수)로 변환해 발행된 콘텐츠에서 조회합니다. 대상 Table은 `tableIds`, 생략 시 목록 API가 활성화된 모든 Table입니다.                                                       |
| `hybrid`          | 메시지를 먼저 분류합니다. "가격이 1만원 이하인 상품"처럼 필드 조건이 있으면 구조화 쿼리, "환불 정책이 어떻게 돼?"처럼 열린 질문이면 의미 검색, "최근 발행된 글 중 결제 관련"처럼 섞여 있으면 둘 다 사용합니다. 병합 결과에서 구조화 쿼리 결과가 앞에 오고 `count` 한도로 잘립니다. |
| `bm25`            | 메시지의 단어(한국어는 글자 2개 단위도 포함)로 Vector 동기화된 텍스트를 BM25 키워드 검색한 뒤, 상위 후보를 모델이 질문과의 관련도로 재정렬(Rerank)합니다. `threshold`는 재정렬 점수(`0`–`1`)에 적용됩니다. 상품명·코드·고유 명사처럼 정확한 단어가 중요한 질문에 적합합니다. 자세한 내용은 [BM25 검색](/docs/ai/bm25)을 참고하세요. |
| 생략 또는 `null` | `hybrid`와 동일합니다.                                                                                                                                                                                                                                                                      |

분류와 쿼리 생성, BM25 재정렬에 사용된 토큰은 답변과 같이 AI Ask 사용량으로 집계됩니다.

---

## 1. 새 채팅 세션 시작

```
POST /v1/{projectId}/chats
```

**요청 본문**

| 필드        | 타입       | 필수 | 기본값  | 설명                                                         |
| ----------- | ---------- | ---- | ------- | ------------------------------------------------------------ |
| `agent_id`  | `string`   | 필수 | -       | 사용할 Agent의 ID. Agents 페이지에서 확인합니다              |
| `message`   | `string`   | 필수 | -       | 사용자 메시지 (1자 이상)                                     |
| `threshold` | `number`   | -    | `0.7`   | 검색 결과에 포함할 최소 점수 (`0`–`1`). `vector`는 유사도, `bm25`는 재정렬 점수 |
| `count`     | `integer`  | -    | `5`     | 컨텍스트에 포함할 관련 콘텐츠 최대 개수 (`1`–`100`)          |
| `tableIds`  | `string[]` | -    | -       | 검색 범위를 제한할 Table ID 목록. 생략하면 모든 Table을 검색 |
| `is_plain`  | `boolean`  | -    | `false` | `true`이면 Markdown 없이 일반 텍스트로 답변하도록 지시       |
| `search_type` | `'vector' \| 'query' \| 'hybrid' \| 'bm25' \| null` | - | `hybrid` | 콘텐츠 검색 방식. [검색 방식](#검색-방식) 참고 |

**요청 예제**

```bash
curl -X POST "https://api.onttle.com/v1/{projectId}/chats" \
  -H "Authorization: Bearer $ONTTLE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "agent_id": "Ag3ntId_ExAmPle123",
    "message": "환불 정책이 어떻게 되나요?",
    "search_type": "hybrid"
  }'
```

**응답**

응답은 `text/plain` 스트리밍으로 전달되며, 전체를 이어 붙이면 하나의 JSON 객체가 됩니다.

```json
{
  "chatId": "chat_xyz789",
  "message": "환불 정책은 구매 후 30일 이내 반품을 허용합니다...",
  "contents": [{ "id": "cont_abc", "table_id": "tbl_abc123" }]
}
```

`chatId`는 이후 메시지 전송과 대화 이력 조회에 사용합니다. `contents`는 답변에 참조된 콘텐츠 목록으로, 출처 표시에 활용할 수 있습니다.

---

## 2. 기존 세션에 메시지 전송

```
POST /v1/{projectId}/chats/{chatId}/messages
```

요청 본문은 새 채팅 세션 시작과 동일합니다. 서버가 기존 대화 이력을 함께 구성해 문맥을 유지한 답변을 생성합니다.

**요청 예제**

```bash
curl -X POST "https://api.onttle.com/v1/{projectId}/chats/{chatId}/messages" \
  -H "Authorization: Bearer $ONTTLE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "agent_id": "Ag3ntId_ExAmPle123",
    "message": "30일이 지나면 환불이 안 되나요?"
  }'
```

**응답 (스트리밍, 조립 후)**

```json
{
  "chatMessageId": "msg_ai002",
  "message": "30일이 지난 경우 제품 하자가 아니라면 환불이 어렵습니다...",
  "contents": [{ "id": "cont_abc", "table_id": "tbl_abc123" }]
}
```

---

## 3. 대화 이력 조회

```
GET /v1/{projectId}/chats/{chatId}/messages
```

세션의 메시지를 생성 순서(오래된 순)로 최대 20개까지 반환합니다.

| Query 파라미터 | 타입     | 필수 | 설명                                                                  |
| -------------- | -------- | ---- | --------------------------------------------------------------------- |
| `lastId`       | `string` | -    | 마지막으로 받은 메시지 ID. 지정하면 이후에 생성된 메시지만 반환합니다 |

**요청 예제**

```bash
curl "https://api.onttle.com/v1/{projectId}/chats/{chatId}/messages?lastId=msg_ai001" \
  -H "Authorization: Bearer $ONTTLE_API_KEY"
```

**응답 예시**

```json
{
  "data": [
    {
      "id": "msg_user001",
      "created_at": "2026-01-01T00:00:00.000Z",
      "updated_at": "2026-01-01T00:00:00.000Z",
      "agent_id": null,
      "message": "환불 정책이 어떻게 되나요?",
      "chat_messages_contents_connections": []
    },
    {
      "id": "msg_ai001",
      "created_at": "2026-01-01T00:00:02.000Z",
      "updated_at": "2026-01-01T00:00:05.000Z",
      "agent_id": "Ag3ntId_ExAmPle123",
      "message": "환불 정책은 구매 후 30일 이내 반품을 허용합니다...",
      "chat_messages_contents_connections": [
        {
          "id": "conn_001",
          "order": 1,
          "score": 0.92,
          "contents": {
            "id": "cont_abc",
            "title": "환불 정책",
            "data": { "body": "구매 후 30일 이내 반품 가능..." },
            "slug": "refund-policy",
            "icon": "📄",
            "table_id": "tbl_abc123"
          }
        }
      ]
    }
  ]
}
```

`agent_id`가 `null`이면 사용자 메시지, 값이 있으면 AI 답변입니다. `chat_messages_contents_connections`에는 답변에 참조된 콘텐츠와 유사도 점수(`score`)가 포함됩니다.

---

## TypeScript 스트리밍 예제

```typescript
const res = await fetch('https://api.onttle.com/v1/{projectId}/chats', {
  method: 'POST',
  headers: {
    Authorization: `Bearer ${process.env.ONTTLE_API_KEY}`,
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    agent_id: 'Ag3ntId_ExAmPle123',
    message: '환불 정책이 어떻게 되나요?',
  }),
})

const reader = res.body!.getReader()
const decoder = new TextDecoder()
let raw = ''

while (true) {
  const { done, value } = await reader.read()
  if (done) break
  raw += decoder.decode(value, { stream: true })
}

const { chatId, message, contents } = JSON.parse(raw)
// chatId: 이후 메시지 전송에 사용
// message: AI 답변
// contents: 참조된 콘텐츠 목록
```

---

## 문제 해결

| 증상                    | 확인 사항                                                                            |
| ----------------------- | ------------------------------------------------------------------------------------ |
| 콘텐츠와 무관한 답변    | 대상 Table의 Vector 동기화 상태가 `SUCCESS`인지 확인, `threshold`를 높여 정확도 개선, 필드 조건 질문이면 `search_type: "query"`, 정확한 이름·코드 질문이면 `search_type: "bm25"` 시도 |
| 참조 콘텐츠가 비어 있음 | `threshold`를 낮추거나 `count`를 늘려 검색 범위 확대, `tableIds` 지정 여부 확인      |
| `agent_id` 오류         | Agents 페이지에서 Agent ID를 다시 확인                                               |

---

## 다음 단계

- [Agents](/docs/ai/agents) — 답변 스타일과 응답 파라미터를 조정합니다.
- [Vector](/docs/ai/vector) — 검색 대상 콘텐츠를 동기화합니다.
- [Embeddings 검색](/docs/ai/embeddings) — 채팅 없이 시맨틱 검색만 사용합니다.
- [BM25 검색](/docs/ai/bm25) — 채팅 없이 키워드 검색 + 재정렬만 사용합니다.
