---
title: 'Embeddings Search'
description: 'How to run semantic (vector) search using the per-table embeddings endpoint.'
category: 'ai'
slug: 'embeddings'
locale: 'en'
apiVersion: 'v1'
order: 4
updatedAt: '2026-10-30'
---

# Embeddings Search

Embeddings Search runs semantic (meaning-based) search through each Table's `embeddings` endpoint. By the end of this guide, you will be able to retrieve semantically similar Contents with similarity scores using natural language queries.

**Prerequisites:**

- [Vector sync](/docs/ai/vector) must be completed for the Table you want to search.
- A **secret** Project API Key (`sk-proj_…`) is required: the search is a `POST` request, which publishable keys cannot make. Call it from your server, not from the browser.
- Each search request embeds the query text, consuming `API.EMBEDDINGS.TOKENS`.

---

## Semantic Search vs. Keyword Search

| Item           | Keyword Search                                 | Semantic Search                                       |
| -------------- | ---------------------------------------------- | ----------------------------------------------------- |
| How it works   | Exact word matching                            | Semantic similarity matching                          |
| Result quality | Only returns results containing the exact word | Returns relevant results even with different phrasing |
| Use cases      | Fast filtering                                 | Natural language search, recommendations              |
| Cost           | Low                                            | Consumes embedding tokens                             |

Use keyword search with the `q` parameter in the REST API, or with [BM25 Search](/docs/ai/bm25) when you need scores and model reranking. Use Embeddings Search when you need more flexible, meaning-based results.

---

## Endpoint

```
POST /v1/{projectId}/tables/{tableSlug}/embeddings
```

Use the Table's slug for `{tableSlug}`. If the Table has no slug, use its Table ID.

**Request Body**

| Field       | Type      | Required | Default | Description                                                     |
| ----------- | --------- | -------- | ------- | --------------------------------------------------------------- |
| `query`     | `string`  | Yes      | -       | The search query text used to find semantically similar content |
| `threshold` | `number`  | -        | `0.7`   | Minimum similarity score to include in results (`0`–`1`)        |
| `count`     | `integer` | -        | `5`     | Maximum number of results to return (`1`–`100`)                 |

---

## Request Examples

```bash
curl -X POST "https://api.onttle.com/v1/{projectId}/tables/posts/embeddings" \
  -H "Authorization: Bearer $ONTTLE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "query": "CMS for frontend developers",
    "threshold": 0.7,
    "count": 5
  }'
```

**TypeScript Example**

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
      query: 'CMS for frontend developers',
      threshold: 0.7,
      count: 5,
    }),
  },
)

const { list } = await res.json()
// list: semantically similar Contents (includes similarity score)
```

---

## Response Format

Results are returned in a `list` array ordered by similarity. Each item contains the Contents fields plus a similarity score `score`.

```json
{
  "list": [
    {
      "id": "cnt_01hxk2",
      "slug": "hello-onttle",
      "score": 0.92,
      "data": {
        "title": "Hello Onttle"
      }
    }
  ]
}
```

| Field   | Type       | Description                                                                    |
| ------- | ---------- | ------------------------------------------------------------------------------ |
| `list`  | `object[]` | List of similar Contents                                                       |
| `score` | `number`   | Similarity score (`0`–`1`). Higher values indicate greater semantic similarity |

---

## Managing Endpoint Availability

The embeddings endpoint is enabled by default when a Table is created. To enable or disable it, open the Table's embeddings endpoint documentation page under **API** in the left menu of your Project and use the toggle at the top right.

> **[IMAGE AREA]** API endpoint documentation page — embeddings endpoint enable toggle

---

## Troubleshooting

| Symptom            | What to Check                                                              |
| ------------------ | -------------------------------------------------------------------------- |
| No search results  | Verify Vector sync status is `SUCCESS`; lower `threshold` to widen results |
| Unexpected results | Check whether Contents changed after the last sync, then re-sync           |
| Request rejected   | Check the endpoint enable toggle and that you use a secret key (`sk-proj_…`) |

---

## Next Steps

- [Vector](/docs/ai/vector) — Sync the contents to be searched into vectors.
- [BM25 Search](/docs/ai/bm25) — Use keyword search with reranking for queries where exact terms matter.
- [Chats](/docs/ai/chats) — Learn about the chat API that uses semantic search results as context.
- [Usage & Billing](/docs/usage-and-billing/metric-types) — See how the `API.EMBEDDINGS.TOKENS` metric is recorded.
