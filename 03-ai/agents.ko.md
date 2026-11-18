---
title: 'Agents'
description: 'Chats API에서 답변을 생성하는 AI Agent를 설정하는 방법을 설명합니다.'
category: 'ai'
slug: 'agents'
locale: 'ko'
apiVersion: 'v1'
order: 1
updatedAt: '2026-11-18'
---

# Agents

Agent는 [Chats API](/docs/ai/chats)에서 답변을 생성하는 AI 어시스턴트 구성입니다. 지시문(Instructions)과 응답 파라미터를 미리 정의해 두면, Chats API가 [Vector 동기화](/docs/ai/vector)된 콘텐츠를 검색해 Agent 설정에 따라 답변을 생성합니다. 답변 생성에는 기본으로 **GPT-6.0 Luna** 모델을 사용합니다.

**사전 조건:** Agent를 생성하고 수정하려면 Project의 `OWNER` 또는 `ADMIN` 역할이 필요합니다.

---

## Agent의 역할

Chats API 요청에는 `agent_id`가 필수로 포함됩니다. 요청이 들어오면 다음 순서로 동작합니다.

1. 사용자 메시지를 임베딩해 Vector 동기화된 콘텐츠에서 관련 Contents를 검색합니다.
2. 검색된 콘텐츠와 대화 이력을 컨텍스트로 구성합니다.
3. Agent에 설정된 Instructions와 응답 파라미터에 따라 답변을 생성합니다.

즉, **무엇을 검색할지**는 Vector 동기화가, **어떻게 답변할지**는 Agent가 결정합니다.

---

## 대시보드에서 Agent 설정

1. Project 좌측 메뉴에서 **Agents**를 클릭합니다.
2. **새 Agent** 버튼을 클릭합니다.
3. 아래 설정 항목을 입력하고 저장합니다.

### 설정 항목

| 항목                  | 필수 | 설명                                                                   |
| --------------------- | ---- | ---------------------------------------------------------------------- |
| **Name**              | 필수 | Agent 이름                                                             |
| **Description**       | -    | Agent의 용도를 설명하는 메모                                           |
| **Instructions**      | -    | AI에 전달할 시스템 지시문. 역할, 어조, 답변 규칙을 정의합니다          |
| **Max Tokens**        | -    | 답변의 최대 토큰 수. 비워 두면 제한하지 않습니다                       |
| **Knowledge Files**   | -    | Agent가 항상 참고할 파일. Project에 업로드된 파일 중 최대 5개까지 선택 |
| **Creativity**        | -    | 답변의 창의성(temperature). `0`–`2`, 기본값 `1`                        |
| **Top P**             | -    | 토큰 샘플링 범위. `0`–`1`, 기본값 `1`                                  |
| **Presence Penalty**  | -    | 새로운 주제 언급 유도 정도. `-2`–`2`, 기본값 `0`                       |
| **Frequency Penalty** | -    | 같은 표현의 반복 억제 정도. `-2`–`2`, 기본값 `0`                       |

---

## Instructions 작성 예시

**고객 지원 어시스턴트**

```
당신은 우리 서비스의 고객 지원 어시스턴트입니다.
- 제공된 콘텐츠에 근거해서만 답변합니다.
- 근거가 없는 질문에는 모른다고 답변하고 지원 채널을 안내합니다.
- 답변은 정중한 존댓말로 3문장 이내로 작성합니다.
```

**제품 문서 안내**

```
당신은 개발자 문서 안내 도우미입니다.
- 코드 예제가 있으면 함께 제시합니다.
- 답변 마지막에 관련 문서 제목을 나열합니다.
```

---

## Agent ID 확인

Chats API 요청의 `agent_id`에 사용할 ID는 **Agents** 페이지의 Agent 목록에서 확인합니다.

```json
{
  "agent_id": "Ag3ntId_ExAmPle123",
  "message": "환불 정책이 어떻게 되나요?"
}
```

---

## 다음 단계

- [Chats](/docs/ai/chats) — 설정한 Agent로 Chats API를 호출합니다.
- [Vector](/docs/ai/vector) — Agent가 검색할 콘텐츠를 벡터로 동기화합니다.
