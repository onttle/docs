---
title: 'Vector'
description: 'Table별 콘텐츠를 벡터 데이터로 동기화해 Embeddings 검색과 Chats에서 사용하는 방법을 설명합니다.'
category: 'ai'
slug: 'vector'
locale: 'ko'
apiVersion: 'v1'
order: 3
updatedAt: '2026-09-01'
---

# Vector

Vector 동기화는 Table의 콘텐츠를 벡터 데이터로 변환해 벡터 DB에 저장하는 기능입니다. 동기화된 데이터는 [Embeddings 검색](/docs/ai/embeddings), [BM25 검색](/docs/ai/bm25), [Chats](/docs/ai/chats)에서 RAG(Retrieval-Augmented Generation) 컨텍스트로 사용됩니다.

**사전 조건:** Project와 Table이 생성되어 있고 `PUBLISHED` 상태의 Contents가 있어야 합니다. 동기화에는 `API.EMBEDDINGS.TOKENS` 메트릭이 소비됩니다.

---

## 동기화 대상

- 동기화는 **Table 단위**로 실행합니다.
- `PUBLISHED` 상태의 Contents만 동기화 대상에 포함됩니다.
- 동기화된 벡터 데이터는 Embeddings 검색과 Chats의 시맨틱 검색에 사용됩니다.

---

## 1단계: Table 동기화 실행

1. Project 좌측 메뉴에서 **Vector**를 클릭합니다.
2. Table 목록에서 동기화할 Table의 **Sync** 버튼을 클릭합니다.
3. 동기화 대상 Contents 수를 확인하고, 필요하면 이번 동기화를 설명하는 메모를 입력합니다.
4. **Sync**를 클릭해 동기화를 시작합니다.

초기 동기화는 Table의 Contents 수에 따라 수 분이 소요될 수 있습니다.

---

## 2단계: 동기화 상태 확인

Vector 페이지의 Table 목록에서 각 Table의 진행 상황을 확인할 수 있습니다.

| 항목                    | 설명                              |
| ----------------------- | --------------------------------- |
| **Published Contents**  | 동기화 대상인 발행된 Contents 수  |
| **Status**              | 현재 동기화 상태                  |
| **Vectorized Contents** | 성공한 Contents 수 / 전체 대상 수 |
| **Memo**                | 동기화 요청 시 입력한 메모        |
| **Synced By**           | 동기화를 요청한 멤버              |
| **Last Updated**        | 마지막 상태 변경 시각             |

**동기화 상태**

| 상태             | 설명                      |
| ---------------- | ------------------------- |
| `READY`          | 동기화 준비 완료          |
| `PROCESSING`     | 동기화 진행 중            |
| `SUCCESS`        | 동기화 완료               |
| `FAIL`           | 동기화 실패               |
| `FAIL_PARTIALLY` | 일부 Contents 동기화 실패 |

`FAIL` 또는 `FAIL_PARTIALLY` 상태라면 Logs 메뉴에서 실패 원인을 확인할 수 있습니다.

---

## 동기화 취소와 삭제

- **취소:** `READY` 또는 `PROCESSING` 상태에서는 **Cancel Generation** 버튼으로 진행 중인 동기화를 취소할 수 있습니다. 취소해도 이전에 동기화된 벡터 데이터는 유지되며, 나중에 다시 요청할 수 있습니다.
- **삭제:** **Delete** 버튼으로 Table의 벡터 데이터를 삭제할 수 있습니다. 삭제하면 해당 Table은 시맨틱 검색 대상에서 제외됩니다.

---

## 콘텐츠 변경 후 재동기화

Contents를 추가, 수정, 삭제하거나 발행 상태를 변경해도 벡터 데이터가 자동으로 업데이트되지 않습니다. 변경 사항을 검색 결과에 반영하려면 Vector 페이지에서 해당 Table의 **Sync**를 다시 실행합니다.

---

## 임베딩 토큰 사용량

동기화 시마다 Contents 텍스트를 임베딩 모델에 전달해 벡터를 생성합니다. 이때 소비된 토큰은 `API.EMBEDDINGS.TOKENS` 메트릭에 기록됩니다. 재동기화에도 동일하게 토큰이 소비되므로, 대량 Table은 변경 사항이 쌓였을 때 재동기화하는 것을 권장합니다.

---

## 문제 해결

| 증상                          | 확인 사항                                                           |
| ----------------------------- | ------------------------------------------------------------------- |
| 동기화 실패                   | Logs에서 오류 원인 확인                                             |
| Vectorized 수가 대상보다 적음 | 상태가 `FAIL_PARTIALLY`인지 확인하고 Logs에서 실패한 Contents 확인  |
| 검색에 최신 콘텐츠가 없음     | 콘텐츠 변경 후 재동기화했는지, 해당 Contents가 `PUBLISHED`인지 확인 |

---

## 다음 단계

- [Embeddings 검색](/docs/ai/embeddings) — 동기화된 데이터로 시맨틱 검색 API를 사용합니다.
- [BM25 검색](/docs/ai/bm25) — 동기화된 데이터로 키워드 검색 + 재정렬 API를 사용합니다.
- [Chats](/docs/ai/chats) — 동기화된 데이터를 컨텍스트로 사용하는 채팅 API를 알아봅니다.
- [사용량 및 과금](/docs/usage-and-billing/metric-types) — `API.EMBEDDINGS.TOKENS` 메트릭 기록 방식을 확인합니다.
