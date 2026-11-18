---
title: '페이지네이션'
description: '목록 API의 page, size 파라미터 사용법과 전체 데이터를 순차 조회하는 방법을 설명합니다.'
category: 'rest-api'
slug: 'pagination'
locale: 'ko'
apiVersion: 'v1'
order: 5
updatedAt: '2026-08-30'
---

# 페이지네이션

Onttle의 목록 조회 엔드포인트는 `page`와 `size` 파라미터를 사용한 오프셋 기반 페이지네이션을 지원합니다.

---

## 파라미터

| 파라미터 | 타입     | 기본값 | 최대값 | 설명                            |
| -------- | -------- | ------ | ------ | ------------------------------- |
| `page`   | `number` | `1`    | —      | 조회할 페이지 번호 (1부터 시작) |
| `size`   | `number` | `20`   | `1000` | 한 번에 반환할 항목 수          |

---

## 응답의 pagination 객체

목록 응답에는 `list`와 `pagination`이 포함됩니다.

```json
{
  "list": [ ... ],
  "pagination": {
    "page": 1,
    "size": 20,
    "total": 85
  }
}
```

| 필드    | 타입     | 설명                            |
| ------- | -------- | ------------------------------- |
| `page`  | `number` | 현재 페이지 번호                |
| `size`  | `number` | 요청한 페이지당 항목 수         |
| `total` | `number` | 전체 항목 수 (모든 페이지 합산) |

---

## 다음 페이지 존재 여부 확인

```typescript
function hasNextPage(pagination: {
  page: number
  size: number
  total: number
}): boolean {
  return pagination.page * pagination.size < pagination.total
}

// 사용 예
const { list, pagination } = await res.json()

if (hasNextPage(pagination)) {
  // 다음 페이지 요청: page + 1
}
```

---

## 전체 데이터 순차 조회

모든 데이터를 가져와야 할 때 페이지를 순차적으로 조회합니다.

```typescript
async function fetchAllContents(projectId: string): Promise<unknown[]> {
  const all: unknown[] = []
  let page = 1
  const size = 100

  while (true) {
    const res = await fetch(
      `https://api.onttle.com/v1/${projectId}/tables/posts/contents?page=${page}&size=${size}`,
      {
        headers: {
          Authorization: `Bearer ${process.env.ONTTLE_API_KEY}`,
        },
      },
    )

    if (!res.ok) throw new Error(`HTTP ${res.status}`)

    const { list, pagination } = await res.json()
    all.push(...list)

    if (page * size >= pagination.total) break
    page += 1
  }

  return all
}
```

대량 데이터를 순차 조회할 때는 API 요청 횟수를 최소화하기 위해 `size`를 가능한 크게 설정합니다. 최대값은 `1000`입니다.

---

## 데이터 변경 중 페이지 순회 시 주의 사항

오프셋 기반 페이지네이션에서는 데이터를 순회하는 동안 아이템이 추가되거나 삭제되면 다음 문제가 발생할 수 있습니다.

- **항목 중복:** 이전 페이지의 마지막 항목이 다음 페이지에 다시 등장합니다.
- **항목 누락:** 삭제된 항목으로 인해 다음 페이지 데이터가 앞으로 당겨져 특정 항목이 누락됩니다.

데이터가 자주 변경되는 환경에서 전체 데이터를 순회해야 한다면 정렬 조건을 일정하게 유지하고, 가능하면 데이터 변경이 없는 시간대에 실행합니다.

---

## 다음 단계

- [응답 형식](/docs/rest-api/response-format) — 목록과 단일 항목 응답 구조를 자세히 이해합니다.
- [요청 예시](/docs/rest-api/request-examples) — `page`, `size`와 `fields`, `sort`, `filter`, `op` 조합 예시를 확인합니다.
