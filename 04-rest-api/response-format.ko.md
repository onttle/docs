---
title: '응답 형식'
description: '목록 응답과 단일 응답의 구조, 날짜 형식, null과 빈 값의 의미를 설명합니다.'
category: 'rest-api'
slug: 'response-format'
locale: 'ko'
apiVersion: 'v1'
order: 4
updatedAt: '2026-08-30'
---

# 응답 형식

Onttle REST API는 모든 응답에 일관된 JSON 형식을 사용합니다. 목록과 단일 항목 응답의 구조 차이, 날짜 형식, 빈 값 처리를 이해하면 예측 가능한 방식으로 응답을 파싱할 수 있습니다.

---

## 목록 응답

목록을 반환하는 GET 요청의 응답 형식입니다.

```json
{
  "list": [
    {
      "id": "cnt_01hxk2",
      "slug": "hello-onttle",
      "data": {
        "title": "Hello Onttle"
      },
      "created_at": "2026-08-30T09:00:00.000Z",
      "updated_at": "2026-08-30T09:00:00.000Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 42
  }
}
```

| 필드               | 타입     | 설명                                         |
| ------------------ | -------- | -------------------------------------------- |
| `list`             | `array`  | 조회된 항목 배열. 결과가 없으면 빈 배열 `[]` |
| `pagination.page`  | `number` | 현재 페이지 번호                             |
| `pagination.limit` | `number` | 요청한 페이지당 항목 수                      |
| `pagination.total` | `number` | 필터 조건에 맞는 전체 항목 수                |

---

## 단일 항목 응답

특정 리소스를 ID로 조회하는 GET 요청의 응답 형식입니다.

```json
{
  "id": "cnt_01hxk2",
  "slug": "hello-onttle",
  "data": {
    "title": "Hello Onttle",
    "is_featured": false,
    "thumbnail": null
  },
  "created_at": "2026-08-30T09:00:00.000Z",
  "updated_at": "2026-08-30T09:00:00.000Z"
}
```

단일 항목은 래핑 객체 없이 리소스 직접 반환합니다.

---

## 날짜와 시간

모든 날짜와 시간 값은 **ISO 8601 UTC** 형식을 사용합니다.

```
2026-08-30T09:00:00.000Z
```

JavaScript에서 파싱하는 방법입니다.

```typescript
const contents = await res.json()

// Date 객체로 변환
const createdAt = new Date(contents.created_at)

// 날짜 포맷 (Intl)
const formatted = new Intl.DateTimeFormat('ko-KR', {
  year: 'numeric',
  month: 'long',
  day: 'numeric',
}).format(publishedAt)
}).format(createdAt)
// 예: "2026년 8월 30일"
```

---

## null, 빈 배열, 생략 필드

| 상황                                          | 응답                                                 |
| --------------------------------------------- | ---------------------------------------------------- |
| 선택적 필드에 값이 없음                       | `null`                                               |
| FILE, CATEGORY Column에서 선택된 값이 없음    | `null` 또는 `[]` (multiple 설정 시)                  |
| 목록 조회 결과가 없음                         | `{ "list": [], "pagination": { ... , "total": 0 } }` |
| `localization` Column에 특정 locale 값이 없음 | 해당 locale 키가 객체에서 생략될 수 있음             |

존재하지 않는 리소스에 접근하면 `null`이 아닌 `404` 오류가 반환됩니다.

---

## 다국어 필드 구조

`localization` 옵션이 설정된 Column은 locale별 값을 객체로 반환합니다.

```json
// locale 파라미터 없을 때
{
  "data": {
    "title": {
      "ko": "Onttle 소개",
      "en": "Hello Onttle"
    }
  }
}

// ?locale=ko 지정 시
{
  "data": {
    "title": "Onttle 소개"
  }
}
```

---

## TypeScript 타입 정의 예제

```typescript
interface Pagination {
  page: number
  limit: number
  total: number
}

interface ContentsData {
  title?: string | Record<string, string>
  is_featured?: boolean
  [key: string]: unknown
}

interface Contents {
  id: string
  slug: string
  data: ContentsData
  created_at: string
  updated_at: string
}

interface ContentsListResponse {
  list: Contents[]
  pagination: Pagination
}
```

---

## 성공 응답과 오류 응답 구분

```typescript
const res = await fetch(url, { headers })

if (res.ok) {
  // 2xx: 성공 응답
  const data = await res.json()
  return data
} else {
  // 4xx, 5xx: 오류 응답
  const error = await res.json()
  // error.error.code, error.error.message 확인
  throw new Error(error.error?.message ?? `HTTP ${res.status}`)
}
```

---

## 다음 단계

- [에러 코드](/docs/api-reference/error-codes) — 오류 응답 구조와 상태 코드별 해결 방법을 확인합니다.
- [페이지네이션](/docs/rest-api/pagination) — `pagination` 객체를 사용해 여러 페이지를 순차 조회합니다.
