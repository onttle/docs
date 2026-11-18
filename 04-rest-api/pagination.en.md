---
title: 'Pagination'
description: 'How to use page and size parameters in list APIs, and how to iterate through all data sequentially.'
category: 'rest-api'
slug: 'pagination'
locale: 'en'
apiVersion: 'v1'
order: 5
updatedAt: '2026-08-30'
---

# Pagination

Onttle list endpoints support offset-based pagination using `page` and `size` parameters.

---

## Parameters

| Parameter | Type     | Default | Max    | Description                               |
| --------- | -------- | ------- | ------ | ----------------------------------------- |
| `page`    | `number` | `1`     | —      | Page number to retrieve (starting from 1) |
| `size`    | `number` | `20`    | `1000` | Number of items to return at once         |

---

## The pagination Object in Responses

List responses include both `list` and `pagination`.

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

| Field   | Type     | Description                            |
| ------- | -------- | -------------------------------------- |
| `page`  | `number` | Current page number                    |
| `size`  | `number` | Requested items per page               |
| `total` | `number` | Total number of items across all pages |

---

## Checking if Another Page Exists

```typescript
function hasNextPage(pagination: {
  page: number
  size: number
  total: number
}): boolean {
  return pagination.page * pagination.size < pagination.total
}

// Usage
const { list, pagination } = await res.json()

if (hasNextPage(pagination)) {
  // Fetch next page: page + 1
}
```

---

## Fetching All Data Sequentially

When you need to retrieve all data, iterate through pages sequentially.

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

When iterating through large datasets, set `size` as high as possible to minimize API requests. The maximum value is `1000`.

---

## Cautions When Iterating During Data Changes

With offset-based pagination, adding or removing items while iterating can cause the following issues.

- **Duplicate items:** The last item from the previous page may appear again on the next page.
- **Missing items:** Deleted items cause subsequent items to shift forward, resulting in skipped entries.

If you must iterate through all data in an environment with frequent changes, keep sorting conditions consistent and, if possible, run the iteration during a period with no data changes.

---

## Next Steps

- [Response Format](/docs/rest-api/response-format) — Understand the structure of list and single-item responses in detail.
- [Request Examples](/docs/rest-api/request-examples) — Learn how to combine `page`, `size`, `fields`, `sort`, `filter`, and `op`.
