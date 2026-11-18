---
title: 'Chats'
description: 'How to use the Chats API for RAG-based chat powered by Vector-synced contents and Agents.'
category: 'ai'
slug: 'chats'
locale: 'en'
apiVersion: 'v1'
order: 6
updatedAt: '2026-11-18'
---

# Chats

The Chats API is a RAG (Retrieval-Augmented Generation) chat API: it finds [Vector-synced](/docs/ai/vector) contents through semantic search and lets an [Agent](/docs/ai/agents) generate the answer. By the end of this guide, you will be able to create chat sessions, exchange messages, and retrieve conversation history.

**Prerequisites:**

- [Vector sync](/docs/ai/vector) must be completed for the Tables you want to search.
- An [Agent](/docs/ai/agents) must be created.
- A **secret** Project API Key (`sk-proj_…`) is required: creating a chat and sending a message are `POST` requests, which publishable keys cannot make. Call these endpoints from your server, not from the browser. A publishable key can only read message history (`GET`).

---

## How It Works

1. The request `message` is used to retrieve relevant Contents from Vector-synced contents. The default (`hybrid`) combines embedding-based semantic search with structured queries; `search_type` selects other strategies such as BM25 keyword search. Up to `count` items with a score of at least `threshold` are used.
2. The retrieved contents and conversation history are assembled into context.
3. A response is streamed according to the Instructions and response parameters of the Agent specified by `agent_id`.

Use `tableIds` to restrict the search scope to specific Tables. If omitted, all Tables in the Project are searched.

### Search types

`search_type` selects how the contents in step 1 are retrieved:

| Value             | Behavior                                                                                                                                                                                                                                                                                                                                 |
| ----------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `vector`          | Semantic (embedding) search over Vector-synced contents. This is the original RAG behavior; `threshold` applies.                                                                                                                                                                                                                         |
| `query`           | The message is converted into a structured filter on the Table columns (the same operators as the dashboard filters: number ranges, dates, categories, text matches, sorting, limits) and matched against published contents. Tables come from `tableIds`, or every Table with the list API enabled.                                     |
| `hybrid`          | The message is classified first. Field constraints such as "products under 10,000 won" use the structured query, open questions such as "what is the refund policy?" use vector search, and mixed requests such as "recent posts about payments" use both. Structured-query hits come first in the merged result, truncated to `count`. |
| `bm25`            | The words of the message (plus two-character bigrams for Korean) are matched against the Vector-synced text with BM25, and the top candidates are reranked by a model according to how well they answer the message. `threshold` applies to the rerank score (`0`–`1`). Best for questions where exact terms matter: product names, codes, proper nouns. See [BM25 Search](/docs/ai/bm25). |
| omitted or `null` | Same as `hybrid`.                                                                                                                                                                                                                                                                                                                        |

The tokens spent on classification, query generation, and BM25 reranking are billed as AI Ask usage, like the answer itself.

---

## 1. Start a New Chat Session

```
POST /v1/{projectId}/chats
```

**Request Body**

| Field       | Type       | Required | Default | Description                                                             |
| ----------- | ---------- | -------- | ------- | ----------------------------------------------------------------------- |
| `agent_id`  | `string`   | Yes      | -       | ID of the Agent to use. Get it from the Agents page                     |
| `message`   | `string`   | Yes      | -       | The user message (at least 1 character)                                 |
| `threshold` | `number`   | -        | `0.7`   | Minimum score for retrieved content (`0`–`1`): similarity for `vector`, rerank score for `bm25` |
| `count`     | `integer`  | -        | `5`     | Maximum number of relevant content items to include (`1`–`100`)         |
| `tableIds`  | `string[]` | -        | -       | Table IDs to scope the search. If omitted, all Tables are searched      |
| `is_plain`  | `boolean`  | -        | `false` | When `true`, instructs the AI to respond in plain text without Markdown |
| `search_type` | `'vector' \| 'query' \| 'hybrid' \| 'bm25' \| null` | - | `hybrid` | How contents are retrieved. See [Search types](#search-types) |

**Request Example**

```bash
curl -X POST "https://api.onttle.com/v1/{projectId}/chats" \
  -H "Authorization: Bearer $ONTTLE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "agent_id": "Ag3ntId_ExAmPle123",
    "message": "What is the refund policy?",
    "search_type": "hybrid"
  }'
```

**Response**

The response is streamed as `text/plain`; when fully assembled, it forms a single JSON object.

```json
{
  "chatId": "chat_xyz789",
  "message": "Our refund policy allows returns within 30 days of purchase...",
  "contents": [{ "id": "cont_abc", "table_id": "tbl_abc123" }]
}
```

Use `chatId` for follow-up messages and history retrieval. `contents` lists the contents referenced by the answer, useful for displaying sources.

---

## 2. Send a Message in an Existing Session

```
POST /v1/{projectId}/chats/{chatId}/messages
```

The request body is the same as when starting a new session. The server reconstructs the conversation history so the answer stays in context.

**Request Example**

```bash
curl -X POST "https://api.onttle.com/v1/{projectId}/chats/{chatId}/messages" \
  -H "Authorization: Bearer $ONTTLE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "agent_id": "Ag3ntId_ExAmPle123",
    "message": "Can I get a refund after 30 days?"
  }'
```

**Response (streamed, after assembly)**

```json
{
  "chatMessageId": "msg_ai002",
  "message": "Unfortunately, refunds are not available after the 30-day window unless the item is defective...",
  "contents": [{ "id": "cont_abc", "table_id": "tbl_abc123" }]
}
```

---

## 3. Get Conversation History

```
GET /v1/{projectId}/chats/{chatId}/messages
```

Returns up to 20 messages for the session in creation order (oldest first).

| Query Parameter | Type     | Required | Description                                                                      |
| --------------- | -------- | -------- | -------------------------------------------------------------------------------- |
| `lastId`        | `string` | -        | ID of the last message received. When provided, only newer messages are returned |

**Request Example**

```bash
curl "https://api.onttle.com/v1/{projectId}/chats/{chatId}/messages?lastId=msg_ai001" \
  -H "Authorization: Bearer $ONTTLE_API_KEY"
```

**Response Example**

```json
{
  "data": [
    {
      "id": "msg_user001",
      "created_at": "2026-01-01T00:00:00.000Z",
      "updated_at": "2026-01-01T00:00:00.000Z",
      "agent_id": null,
      "message": "What is the refund policy?",
      "chat_messages_contents_connections": []
    },
    {
      "id": "msg_ai001",
      "created_at": "2026-01-01T00:00:02.000Z",
      "updated_at": "2026-01-01T00:00:05.000Z",
      "agent_id": "Ag3ntId_ExAmPle123",
      "message": "Our refund policy allows returns within 30 days of purchase...",
      "chat_messages_contents_connections": [
        {
          "id": "conn_001",
          "order": 1,
          "score": 0.92,
          "contents": {
            "id": "cont_abc",
            "title": "Refund Policy",
            "data": { "body": "Returns accepted within 30 days..." },
            "slug": "refund-policy",
            "icon": "📄",
            "table_id": "tbl_abc123"
          }
        }
      ]
    }
  ]
}
```

Messages with `agent_id: null` are user messages; those with a value are AI answers. `chat_messages_contents_connections` contains the contents referenced by the answer along with the similarity score (`score`).

---

## TypeScript Streaming Example

```typescript
const res = await fetch('https://api.onttle.com/v1/{projectId}/chats', {
  method: 'POST',
  headers: {
    Authorization: `Bearer ${process.env.ONTTLE_API_KEY}`,
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    agent_id: 'Ag3ntId_ExAmPle123',
    message: 'What is the refund policy?',
  }),
})

const reader = res.body!.getReader()
const decoder = new TextDecoder()
let raw = ''

while (true) {
  const { done, value } = await reader.read()
  if (done) break
  raw += decoder.decode(value, { stream: true })
}

const { chatId, message, contents } = JSON.parse(raw)
// chatId: use for follow-up messages
// message: the AI answer
// contents: referenced contents
```

---

## Troubleshooting

| Symptom                       | What to Check                                                                              |
| ----------------------------- | ------------------------------------------------------------------------------------------ |
| Answers unrelated to contents | Verify the target Table's Vector sync status is `SUCCESS`; raise `threshold` for precision; for field-based questions try `search_type: "query"`, for exact names or codes try `search_type: "bm25"` |
| Referenced contents are empty | Lower `threshold` or raise `count` to widen the search; check the `tableIds` scope         |
| `agent_id` errors             | Re-check the Agent ID on the Agents page                                                   |

---

## Next Steps

- [Agents](/docs/ai/agents) — Tune the answer style and response parameters.
- [Vector](/docs/ai/vector) — Sync the contents to be searched.
- [Embeddings Search](/docs/ai/embeddings) — Use semantic search on its own, without chat.
- [BM25 Search](/docs/ai/bm25) — Use keyword search with reranking on its own, without chat.
