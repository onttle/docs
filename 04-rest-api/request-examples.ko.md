---
title: '요청 예시'
description: '모든 HTTP 메서드에 대한 REST API 요청 예제와 응답 파싱 방법을 설명합니다.'
category: 'rest-api'
slug: 'request-examples'
locale: 'ko'
apiVersion: 'v1'
order: 3
popular: true
updatedAt: '2026-10-30'
---

# 요청 예시

이 문서는 Onttle REST API의 공통 요청 형식을 설명합니다. 모든 엔드포인트에 적용되는 헤더, 메서드별 요청 형태, 응답 파싱 방법을 다룹니다.

---

## 기본 요청 형식

**Base URL:** `https://api.onttle.com/v1`

모든 요청에 다음 헤더를 포함합니다.

| 헤더            | 값                 | 필수                  |
| --------------- | ------------------ | --------------------- |
| `Authorization` | `Bearer {API_KEY}` | 항상                  |
| `Content-Type`  | `application/json` | POST, PUT, PATCH 요청 |

---

## GET 요청 — 목록 조회

Contents 목록을 조회합니다.

**curl**

```bash
curl "https://api.onttle.com/v1/{projectId}/tables/posts/contents?page=1&size=20" \
  -H "Authorization: Bearer $ONTTLE_API_KEY"
```

**TypeScript**

```typescript
const res = await fetch(
  `https://api.onttle.com/v1/${projectId}/tables/posts/contents?page=1&size=20`,
  {
    headers: {
      Authorization: `Bearer ${process.env.ONTTLE_API_KEY}`,
    },
  },
)

if (!res.ok) throw new Error(`HTTP ${res.status}`)

const { list, pagination } = await res.json()
```

### 목록 조회 쿼리 파라미터

목록 조회 API는 아래 쿼리 파라미터를 지원합니다.

| 파라미터 | 타입     | 설명                                              |
| -------- | -------- | ------------------------------------------------- |
| `page`   | `number` | 페이지 번호 (기본값: `1`)                         |
| `size`   | `number` | 페이지당 항목 수 (기본값: `20`, 최대값: `1000`)   |
| `fields` | `string` | 반환 필드 목록 (쉼표 구분)                        |
| `sort`   | `string` | 정렬 조건 배열(JSON 문자열)                       |
| `filter` | `string` | 필터 조건 배열(JSON 문자열)                       |
| `op`     | `string` | 필터 결합 연산자 (`and` 또는 `or`, 기본값: `and`) |

- `fields`, `sort`, `filter`는 Table 스키마에 따라 허용 값이 달라집니다.
- 즉, Table마다 사용 가능한 필드/정렬/필터 대상이 다릅니다.
- `sort`, `filter`는 JSON 객체 배열을 URL 인코딩해 전달합니다.

**TypeScript (URLSearchParams 예시)**

```typescript
const sort = [
  { i: 'created_at', o: 'desc' },
  { i: 'title', o: 'asc', l: 'ko' },
]

const filter = [
  { i: 'slug', w: 'contains', v: 'guide' },
  { i: 'title', w: 'contains', v: 'onttle', l: 'ko' },
]

const params = new URLSearchParams({
  page: '1',
  size: '20',
  fields: 'id,slug,title,created_at',
  sort: JSON.stringify(sort),
  filter: JSON.stringify(filter),
  op: 'and',
})

const res = await fetch(
  `https://api.onttle.com/v1/${projectId}/tables/posts/contents?${params.toString()}`,
  {
    headers: {
      Authorization: `Bearer ${process.env.ONTTLE_API_KEY}`,
    },
  },
)
```

---

## GET 요청 — 단일 항목 조회

특정 Contents를 ID 또는 slug로 조회합니다.

**curl**

```bash
curl "https://api.onttle.com/v1/{projectId}/tables/posts/contents/{contentsId}" \
  -H "Authorization: Bearer $ONTTLE_API_KEY"
```

**TypeScript — Next.js 캐시 옵션 포함**

```typescript
const res = await fetch(
  `https://api.onttle.com/v1/${projectId}/tables/posts/contents/${contentsId}`,
  {
    headers: {
      Authorization: `Bearer ${process.env.ONTTLE_API_KEY}`,
    },
    next: { revalidate: 60 }, // ISR: 60초마다 재검증
  },
)

if (res.status === 404) return null
if (!res.ok) throw new Error(`HTTP ${res.status}`)

const contents = await res.json()
```

---

## POST 요청 — 생성

새 Contents를 생성합니다. 시크릿 API Key(`sk-proj_…`)가 필요합니다. 게시용 키는 읽기 전용입니다.

**curl**

```bash
curl -X POST "https://api.onttle.com/v1/{projectId}/tables/posts/contents" \
  -H "Authorization: Bearer $ONTTLE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "title": "새 게시물"
    }
  }'
```

**TypeScript**

```typescript
const res = await fetch(
  `https://api.onttle.com/v1/${projectId}/tables/posts/contents`,
  {
    method: 'POST',
    headers: {
      Authorization: `Bearer ${process.env.ONTTLE_API_KEY}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      data: {
        title: '새 게시물',
      },
    }),
  },
)

if (!res.ok) {
  const error = await res.json()
  throw new Error(error.error?.message ?? `HTTP ${res.status}`)
}

const created = await res.json()
```

---

## PUT 요청 — 수정

기존 Contents를 수정합니다.

**curl**

```bash
curl -X PUT "https://api.onttle.com/v1/{projectId}/tables/posts/contents/{contentsId}" \
  -H "Authorization: Bearer $ONTTLE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "title": "수정된 제목"
    }
  }'
```

---

## DELETE 요청 — 삭제

Contents를 삭제합니다.

**curl**

```bash
curl -X DELETE "https://api.onttle.com/v1/{projectId}/tables/posts/contents/{contentsId}" \
  -H "Authorization: Bearer $ONTTLE_API_KEY"
```

**TypeScript**

```typescript
const res = await fetch(
  `https://api.onttle.com/v1/${projectId}/tables/posts/contents/${contentsId}`,
  {
    method: 'DELETE',
    headers: {
      Authorization: `Bearer ${process.env.ONTTLE_API_KEY}`,
    },
  },
)

if (!res.ok) throw new Error(`HTTP ${res.status}`)
// 204 No Content 또는 200 응답
```

---

## 환경 변수 관리

Next.js에서 환경 변수를 읽는 패턴입니다.

```typescript
// lib/onttle.ts
const API_BASE = 'https://api.onttle.com/v1'
const PROJECT_ID = process.env.ONTTLE_PROJECT_ID!
const API_KEY = process.env.ONTTLE_API_KEY!

export function onttleFetch(path: string, init?: RequestInit) {
  return fetch(`${API_BASE}/${PROJECT_ID}${path}`, {
    ...init,
    headers: {
      Authorization: `Bearer ${API_KEY}`,
      'Content-Type': 'application/json',
      ...init?.headers,
    },
  })
}
```

```typescript
// 사용 예
const res = await onttleFetch('/tables/posts/contents?page=1&size=20')
const { list } = await res.json()
```

---

## 다음 단계

- [페이지네이션](/docs/rest-api/pagination) — 여러 페이지의 데이터를 순차 조회하는 방법을 확인합니다.
- [응답 형식](/docs/rest-api/response-format) — 목록 응답과 단일 응답의 구조를 이해합니다.
