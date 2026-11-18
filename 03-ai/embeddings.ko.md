---
title: 'Embeddings 검색'
description: 'Table별 embeddings 엔드포인트로 시맨틱(벡터) 검색을 사용하는 방법을 설명합니다.'
category: 'ai'
slug: 'embeddings'
locale: 'ko'
apiVersion: 'v1'
order: 4
updatedAt: '2026-10-30'
---

# Embeddings 검색

Embeddings 검색은 Table별 `embeddings` 엔드포인트로 시맨틱(의미 기반) 검색을 실행하는 기능입니다. 이 가이드를 완료하면 자연어 쿼리로 의미적으로 유사한 Contents를 유사도 점수와 함께 조회할 수 있습니다.

**사전 조건:**

- 검색할 Table의 [Vector 동기화](/docs/ai/vector)가 완료되어 있어야 합니다.
- **시크릿** Project API Key(`sk-proj_…`)가 필요합니다. 검색은 `POST` 요청이라 게시용 키로는 호출할 수 없습니다. 브라우저가 아닌 서버에서 호출하세요.
- 검색 요청마다 쿼리 텍스트를 임베딩하므로 `API.EMBEDDINGS.TOKENS`가 소비됩니다.

---

## 시맨틱 검색 vs. 키워드 검색

| 항목      | 키워드 검색               | 시맨틱 검색                  |
| --------- | ------------------------- | ---------------------------- |
| 동작 방식 | 정확한 단어 일치          | 의미적 유사성으로 검색       |
| 결과 품질 | 단어가 포함된 경우만 반환 | 다른 표현도 관련 결과로 반환 |
| 활용 사례 | 빠른 필터링               | 자연어 검색, 추천            |
| 비용      | 낮음                      | 임베딩 토큰 소비             |

키워드 검색은 REST API의 `q` 파라미터, 또는 점수와 모델 재정렬이 필요하면 [BM25 검색](/docs/ai/bm25)으로 사용합니다. 의미 기반으로 더 유연한 결과가 필요할 때 Embeddings 검색을 사용합니다.

---

## 엔드포인트

```
POST /v1/{projectId}/tables/{tableSlug}/embeddings
```

`{tableSlug}`에는 Table의 slug를 사용합니다. slug가 없는 Table은 Table ID를 사용합니다.

**요청 본문**

| 필드        | 타입      | 필수 | 기본값 | 설명                                             |
| ----------- | --------- | ---- | ------ | ------------------------------------------------ |
| `query`     | `string`  | 필수 | -      | 의미적으로 유사한 콘텐츠를 찾을 검색 쿼리 텍스트 |
| `threshold` | `number`  | -    | `0.7`  | 결과에 포함할 최소 유사도 점수 (`0`–`1`)         |
| `count`     | `integer` | -    | `5`    | 반환할 최대 결과 개수 (`1`–`100`)                |

---

## 요청 예제

```bash
curl -X POST "https://api.onttle.com/v1/{projectId}/tables/posts/embeddings" \
  -H "Authorization: Bearer $ONTTLE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "query": "프론트엔드 개발자를 위한 CMS",
    "threshold": 0.7,
    "count": 5
  }'
```

**TypeScript 예제**

```typescript
const res = await fetch(
  'https://api.onttle.com/v1/{projectId}/tables/posts/embeddings',
  {
    method: 'POST',
    headers: {
      Authorization: `Bearer ${process.env.ONTTLE_API_KEY}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      query: '프론트엔드 개발자를 위한 CMS',
      threshold: 0.7,
      count: 5,
    }),
  },
)

const { list } = await res.json()
// list: 의미적으로 유사한 Contents 목록 (유사도 점수 포함)
```

---

## 응답 형식

결과는 유사도가 높은 순으로 정렬된 `list` 배열로 반환됩니다. 각 항목에는 Contents 필드와 함께 유사도 점수 `score`가 포함됩니다.

```json
{
  "list": [
    {
      "id": "cnt_01hxk2",
      "slug": "hello-onttle",
      "score": 0.92,
      "data": {
        "title": "Onttle 소개"
      }
    }
  ]
}
```

| 필드    | 타입       | 설명                                                          |
| ------- | ---------- | ------------------------------------------------------------- |
| `list`  | `object[]` | 유사한 Contents 목록                                          |
| `score` | `number`   | 유사도 점수 (`0`–`1`). 값이 높을수록 의미적으로 더 유사합니다 |

---

## 엔드포인트 활성화 관리

Table을 생성하면 embeddings 엔드포인트는 기본으로 활성화됩니다. Project 좌측 메뉴의 **API**에서 해당 Table의 embeddings 엔드포인트 문서 페이지로 이동해 우측 상단 토글로 활성/비활성을 변경할 수 있습니다.

---

## 문제 해결

| 증상             | 확인 사항                                                             |
| ---------------- | --------------------------------------------------------------------- |
| 검색 결과가 없음 | Vector 동기화 상태가 `SUCCESS`인지 확인, `threshold`를 낮춰 범위 확대 |
| 예상과 다른 결과 | 동기화 이후 변경된 Contents가 있는지 확인하고 재동기화                |
| 요청이 거부됨    | 엔드포인트 활성화 토글과 시크릿 키(`sk-proj_…`) 사용 여부 확인          |

---

## 다음 단계

- [Vector](/docs/ai/vector) — 검색 대상 콘텐츠를 벡터로 동기화합니다.
- [BM25 검색](/docs/ai/bm25) — 정확한 단어가 중요한 쿼리에 키워드 검색 + 재정렬을 사용합니다.
- [Chats](/docs/ai/chats) — 시맨틱 검색 결과를 컨텍스트로 사용하는 채팅 API를 알아봅니다.
- [사용량 및 과금](/docs/usage-and-billing/metric-types) — `API.EMBEDDINGS.TOKENS` 메트릭 기록 방식을 확인합니다.
