---
title: 'Vector'
description: 'How to sync table contents into vector data for use in Embeddings Search and Chats.'
category: 'ai'
slug: 'vector'
locale: 'en'
apiVersion: 'v1'
order: 3
updatedAt: '2026-09-01'
---

# Vector

Vector sync converts a Table's contents into vector data and stores it in a vector database. The synced data is used as RAG (Retrieval-Augmented Generation) context by [Embeddings Search](/docs/ai/embeddings), [BM25 Search](/docs/ai/bm25), and [Chats](/docs/ai/chats).

**Prerequisites:** A Project and Table must be created, with Contents in the `PUBLISHED` state. Syncing consumes the `API.EMBEDDINGS.TOKENS` metric.

---

## What Gets Synced

- Sync runs **per Table**.
- Only Contents in the `PUBLISHED` state are included in the sync.
- Synced vector data powers semantic search in Embeddings Search and Chats.

---

## Step 1: Run a Table Sync

1. Click **Vector** in the left menu of your Project.
2. Click the **Sync** button for the Table you want to sync in the Table list.
3. Review the number of Contents to be synced, and optionally enter a memo describing this sync.
4. Click **Sync** to start.

> **[IMAGE AREA]** Vector page — per-table sync status list with the Sync button

The initial sync may take several minutes depending on the number of Contents in the Table.

---

## Step 2: Check Sync Status

You can track each Table's progress in the Table list on the Vector page.

| Column                  | Description                                |
| ----------------------- | ------------------------------------------ |
| **Published Contents**  | Number of published Contents to be synced  |
| **Status**              | Current sync status                        |
| **Vectorized Contents** | Succeeded Contents / total target Contents |
| **Memo**                | Memo entered when requesting the sync      |
| **Synced By**           | The member who requested the sync          |
| **Last Updated**        | Time of the last status change             |

**Sync Statuses**

| Status           | Description                  |
| ---------------- | ---------------------------- |
| `READY`          | Ready to sync                |
| `PROCESSING`     | Sync in progress             |
| `SUCCESS`        | Sync complete                |
| `FAIL`           | Sync failed                  |
| `FAIL_PARTIALLY` | Some Contents failed to sync |

If the status is `FAIL` or `FAIL_PARTIALLY`, check the Logs menu for the cause of failure.

---

## Canceling and Deleting

- **Cancel:** While the status is `READY` or `PROCESSING`, click **Cancel Generation** to cancel the sync in progress. Previously synced vector data is preserved, and you can request a sync again later.
- **Delete:** Click **Delete** to remove the Table's vector data. Once deleted, the Table is excluded from semantic search.

---

## Re-syncing After Content Changes

Vector data is not automatically updated when Contents are added, modified, deleted, or change publish status. To reflect changes in search results, run **Sync** again for the Table on the Vector page.

---

## Embedding Token Usage

Each sync sends Contents text to an embedding model to generate vectors. Tokens consumed are recorded under the `API.EMBEDDINGS.TOKENS` metric. Re-syncing consumes tokens the same way, so for large Tables it's recommended to re-sync after changes have accumulated.

---

## Troubleshooting

| Symptom                             | What to Check                                                                  |
| ----------------------------------- | ------------------------------------------------------------------------------ |
| Sync failure                        | Check the Logs for error details                                               |
| Vectorized count lower than target  | Check if the status is `FAIL_PARTIALLY` and review the failed Contents in Logs |
| Latest contents missing from search | Verify you re-synced after changes and that the Contents are `PUBLISHED`       |

---

## Next Steps

- [Embeddings Search](/docs/ai/embeddings) — Use the semantic search API with your synced data.
- [BM25 Search](/docs/ai/bm25) — Use the keyword search + rerank API with your synced data.
- [Chats](/docs/ai/chats) — Learn about the chat API that uses synced data as context.
- [Usage & Billing](/docs/usage-and-billing/metric-types) — See how the `API.EMBEDDINGS.TOKENS` metric is recorded.
