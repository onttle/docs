---
title: 'Request Examples'
description: 'REST API request examples for all HTTP methods and how to parse responses.'
category: 'rest-api'
slug: 'request-examples'
locale: 'en'
apiVersion: 'v1'
order: 3
popular: true
updatedAt: '2026-10-30'
---

# Request Examples

This document explains the common request format for the Onttle REST API. It covers the required headers for all endpoints, request patterns per HTTP method, and response parsing.

---

## Basic Request Format

**Base URL:** `https://api.onttle.com/v1`

Include the following headers in every request.

| Header          | Value              | Required             |
| --------------- | ------------------ | -------------------- |
| `Authorization` | `Bearer {API_KEY}` | Always               |
| `Content-Type`  | `application/json` | For POST, PUT, PATCH |

---

## GET Request — List

Fetch a list of Contents.

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

### List Query Parameters

List APIs support the following query parameters.

| Parameter | Type     | Description                                                      |
| --------- | -------- | ---------------------------------------------------------------- |
| `page`    | `number` | Page number (default: `1`)                                       |
| `size`    | `number` | Items per page (default: `20`, max: `1000`)                      |
| `fields`  | `string` | Returned field projection (comma-separated)                      |
| `sort`    | `string` | Sort conditions array (JSON-encoded string)                      |
| `filter`  | `string` | Filter conditions array (JSON-encoded string)                    |
| `op`      | `string` | Logical operator between filters (`and` or `or`, default: `and`) |

- `fields`, `sort`, and `filter` are schema-dependent.
- That means each Table exposes different available fields for projection, sorting, and filtering.
- `sort` and `filter` should be sent as URL-encoded JSON arrays.

**TypeScript (URLSearchParams example)**

```typescript
const sort = [
  { i: 'created_at', o: 'desc' },
  { i: 'title', o: 'asc', l: 'en' },
]

const filter = [
  { i: 'slug', w: 'contains', v: 'guide' },
  { i: 'title', w: 'contains', v: 'onttle', l: 'en' },
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

## GET Request — Single Item

Fetch a specific Contents item by ID or slug.

**curl**

```bash
curl "https://api.onttle.com/v1/{projectId}/tables/posts/contents/{contentsId}" \
  -H "Authorization: Bearer $ONTTLE_API_KEY"
```

**TypeScript — with Next.js cache options**

```typescript
const res = await fetch(
  `https://api.onttle.com/v1/${projectId}/tables/posts/contents/${contentsId}`,
  {
    headers: {
      Authorization: `Bearer ${process.env.ONTTLE_API_KEY}`,
    },
    next: { revalidate: 60 }, // ISR: revalidate every 60 seconds
  },
)

if (res.status === 404) return null
if (!res.ok) throw new Error(`HTTP ${res.status}`)

const contents = await res.json()
```

---

## POST Request — Create

Create new Contents. A secret API Key (`sk-proj_…`) is required; publishable keys are read-only.

**curl**

```bash
curl -X POST "https://api.onttle.com/v1/{projectId}/tables/posts/contents" \
  -H "Authorization: Bearer $ONTTLE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "title": "New Post"
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
        title: 'New Post',
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

## PUT Request — Update

Update existing Contents.

**curl**

```bash
curl -X PUT "https://api.onttle.com/v1/{projectId}/tables/posts/contents/{contentsId}" \
  -H "Authorization: Bearer $ONTTLE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "title": "Updated Title"
    }
  }'
```

---

## DELETE Request — Delete

Delete a Contents item.

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
// Returns 204 No Content or 200
```

---

## Managing Environment Variables

A pattern for reading environment variables in Next.js.

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
// Usage example
const res = await onttleFetch('/tables/posts/contents?page=1&size=20')
const { list } = await res.json()
```

---

## Next Steps

- [Pagination](/docs/rest-api/pagination) — Understand how to iterate through multiple pages of data.
- [Response Format](/docs/rest-api/response-format) — Understand the structure of list and single-item responses.
