---
title: 'Response Format'
description: 'The structure of list and single-item responses, date formats, and the meaning of null and empty values.'
category: 'rest-api'
slug: 'response-format'
locale: 'en'
apiVersion: 'v1'
order: 4
updatedAt: '2026-08-30'
---

# Response Format

The Onttle REST API uses a consistent JSON format for all responses. Understanding the structural difference between list and single-item responses, date formats, and empty value handling allows you to parse responses in a predictable way.

---

## List Response

The response format for GET requests that return a list.

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

| Field              | Type     | Description                                                         |
| ------------------ | -------- | ------------------------------------------------------------------- |
| `list`             | `array`  | Array of retrieved items. Returns an empty array `[]` if no results |
| `pagination.page`  | `number` | Current page number                                                 |
| `pagination.limit` | `number` | Requested items per page                                            |
| `pagination.total` | `number` | Total number of items matching the filter                           |

---

## Single Item Response

The response format for GET requests that retrieve a specific resource by ID.

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

Single items are returned directly without a wrapping object.

---

## Dates and Times

All date and time values use **ISO 8601 UTC** format.

```
2026-08-30T09:00:00.000Z
```

How to parse it in JavaScript.

```typescript
const contents = await res.json()

// Convert to a Date object
const createdAt = new Date(contents.created_at)

// Format the date (Intl)
const formatted = new Intl.DateTimeFormat('en-US', {
  year: 'numeric',
  month: 'long',
  day: 'numeric',
}).format(publishedAt)
}).format(createdAt)
// e.g. "August 30, 2026"
```

---

## null, Empty Arrays, and Omitted Fields

| Situation                                                | Response                                             |
| -------------------------------------------------------- | ---------------------------------------------------- |
| Optional field has no value                              | `null`                                               |
| FILE or CATEGORY Column with no selection                | `null` or `[]` (when multiple is set)                |
| List query returns no results                            | `{ "list": [], "pagination": { ... , "total": 0 } }` |
| A localization Column has no value for a specific locale | The locale key may be omitted from the object        |

Accessing a non-existent resource returns a `404` error, not `null`.

---

## Multilingual Field Structure

Columns with the `localization` option return per-locale values as an object.

```json
// Without locale parameter
{
  "data": {
    "title": {
      "ko": "Onttle 소개",
      "en": "Hello Onttle"
    }
  }
}

// With ?locale=en
{
  "data": {
    "title": "Hello Onttle"
  }
}
```

---

## TypeScript Type Definitions

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

## Distinguishing Success and Error Responses

```typescript
const res = await fetch(url, { headers })

if (res.ok) {
  // 2xx: Success response
  const data = await res.json()
  return data
} else {
  // 4xx, 5xx: Error response
  const error = await res.json()
  // Check error.error.code and error.error.message
  throw new Error(error.error?.message ?? `HTTP ${res.status}`)
}
```

---

## Next Steps

- [Error Codes](/docs/api-reference/error-codes) — Understand the error response structure and resolutions per status code.
- [Pagination](/docs/rest-api/pagination) — Use the `pagination` object to iterate through multiple pages.
