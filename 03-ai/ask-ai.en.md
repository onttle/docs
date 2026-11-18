---
title: 'Ask AI (Assistant)'
description: 'How to use the Ask AI assistant in the dashboard to look things up in a project and, after approval, change contents, schema and settings.'
category: 'ai'
slug: 'ask-ai'
locale: 'en'
apiVersion: 'v1'
order: 7
updatedAt: '2026-11-02'
---

# Ask AI (Assistant)

Ask AI is the assistant docked to the right of the Project dashboard. Ask in plain language and it reads the current Project's Tables, Contents, settings and usage to answer. Anything that changes data is shown as a preview card first and runs **only after you approve it**.

**Prerequisites:** You must be a member of the Project. Conversations use credits under the `AI.ASK.TOKENS` metric. Changes go through the same permission checks as doing them in the dashboard yourself.

---

## Opening the panel and managing chats

1. Click **Ask AI** at the top of a Project page. The panel opens on the right and the page shrinks beside it.
2. Pick a **quick prompt** on the empty screen, or type a question and press Enter. You can attach files (images, CSV, text).
3. **New chat** in the panel header starts a fresh conversation. **Chat history** reopens earlier chats and lets you rename, archive or delete them.

The assistant knows which Table you are looking at and which **rows you have checked**. Check rows in the Contents list and say "publish the selected rows" to target them.

---

## Two kinds of actions

| Kind | What happens | Example |
| ---- | ------------ | ------- |
| Read immediately | Data is read and answered right away. Nothing changes. | "Which tables are writable through the API?" |
| Needs approval | The change is shown as a card; it runs when you click **Approve**. **Reject** leaves everything as it is. | "Shorten the titles of the selected rows to 30 characters" |

Small markers above an answer show what the assistant looked up (for example `Searching contents · 31 rows`). A red marker means that lookup failed; the assistant usually retries another way.

---

## What you can look up right away

### Contents and Tables

| What | Example question |
| ---- | ---------------- |
| Tables, columns and categories | "Summarize the tables in this project" |
| Rows matching a condition, full values of a row | "Find posts published in the last 7 days" |
| Change history of a row and a diff of two versions | "Compare version 1 of this row with the current one and show only changed fields" |
| Variables and translations (including missing languages) | "Which translation keys are missing a language?" |
| Project health check | "Check the project and list the problems as a checklist" |

### Settings and operations

| What | Example question |
| ---- | ---------------- |
| Public API settings (endpoints per table, embeddings/BM25, OpenAPI URL) | "Show which tables are readable or writable through the API" |
| OpenAPI spec URL and operations | "Show only the blogs table operations from the OpenAPI spec" |
| API reference and client code | "Write a TypeScript example that lists this table with pagination" |
| API keys (masked), members and roles, webhooks | "Summarize the API keys and members in one line each" |
| Webhook delivery logs | "Show the last 5 failed deliveries of the inquiry webhook and guess why" |
| Agents and their settings | "Which model and instructions does each agent use?" |
| Vector sync status | "What is the vector sync status per table?" |

### Files

| What | Example question |
| ---- | ---------------- |
| Search files (kind, size, upload date) | "Show the images uploaded last week" |
| Temporary download link (expires after 5 minutes by default) | "Give me a link to the latest logo file" |
| Where a file is used, and unused files | "How many files are not used by any row, and how big are they?" |
| Text extraction from images and PDFs (OCR) | "Extract the text of the scanned contract file" |

OCR returns text already extracted at upload time for free; otherwise it runs on the spot and records one `FILE.OCR.CALLS`.

### Credits, logs and docs

| What | Example question |
| ---- | ---------------- |
| Credit balance and usage for the billing cycle | "Summarize credit usage for this billing cycle" |
| Cost estimates | "How many credits would it cost to embed all 31 rows of Docs?" |
| API log analysis (requests, errors, paths, per key, vs. previous period) | "Analyze the API logs of the last 7 days" |
| Search the official Onttle docs | "Find how to set up a webhook in the docs" |

Answers from the docs show a numbered **Sources** list under the answer; each entry links to the matching docs section, and `[1]`, `[2]` in the text refer to it.

---

## Actions that need approval

| Action | Description |
| ------ | ----------- |
| List filter | Applies filters and sorting to the Contents list you are viewing. No data changes, so it applies immediately. |
| Table design, add or edit columns | Propose a new Table, add columns to an existing one, rename columns or change options. A column's type cannot change. |
| Edit a Table | Name, slug, description, read-only |
| Create, edit, delete rows | Import pasted text or CSV, bulk-edit the selected rows, delete rows. Locked rows are skipped. |
| Delete all contents of a Table | Deletes every unlocked row of one Table (truncate). The card shows how many rows will be deleted; owners and admins only, and it cannot be undone. |
| Publish, lock, restore | Publish / unpublish / archive, lock / unlock, restore an earlier version |
| Publish or unpublish all contents of a Table | Publishes every unpublished row of one Table, or sets every published row back to draft. The card shows how many rows will change; locked rows are skipped and archived rows are not published. |
| Categories, variables, translations | Sample data, value changes, add / rename / delete category values, restore a variable |
| API settings | Turn table endpoints on or off, embeddings / BM25, the OpenAPI URL |
| Create a webhook | URL, target Tables, events (CREATED, UPDATED, DELETED) |

Cards show before and after values, the number of rows affected, and a warning for irreversible actions such as deletion. When the action has run, the applied and failed counts stay on the same card.

### AI generation is approved in two steps

When you let AI fill empty cells, generate sample rows or fill translations:

1. **Approve generation**: a card says that AI credits will be used. Approving only generates the values; nothing is saved.
2. **Approve saving**: the generated values appear in the usual edit-rows, create-rows or edit-translations card with before and after values. Approving here saves them.

For a handful of cells the assistant writes the values itself and shows the save card directly. For many rows, or when you say "fill with AI", it goes through both steps. The generation step uses `AI.GENERATION.TOKENS`.

---

## Quick prompts

The empty screen of a new chat offers common requests as buttons. With rows checked, the row-related prompts come first.

- List filter, table design, bulk edit, row import
- Sample categories / variables / translations, fill missing translations, fill SEO fields
- API code, explain logs, credits and usage, project health check
- Ask the docs
- (with rows selected) fill empty cells, edit, publish, delete, change history of the selected rows

---

## Tips

- **Name the Table or its slug.** "In the docs table…" is enough; with several candidates the assistant asks which one.
- **Check rows before asking.** "The selected rows" is the most precise target.
- **One topic per message.** Mixing a lookup and a change in one question can push the change card down.
- **Read the card before approving.** The assistant never says something was changed until the approval result comes back.

---

## Usage and limits

| Use | Metric |
| --- | ------ |
| Ask AI conversations | `AI.ASK.TOKENS` |
| AI generation (empty cells, sample rows, translations) | `AI.GENERATION.TOKENS` |
| OCR runs | `FILE.OCR.CALLS` |

When credits are insufficient the conversation does not start and a notice is shown. Change history and restore work only on plans that include version history.

---

## Cautions

- AI answers can contain mistakes. Verify numbers and settings against the cards and the dashboard screens.
- Questions and lookup results are sent to an AI model. Keep columns with personal data, payment details, passwords or tokens out of your questions.
- API keys are always shown masked (prefix and last four characters); the assistant never reveals a full key.

---

## Next steps

- [Contents](/docs/core-features/contents) — Adding content with AI and Auto-fill directly on the screens
- [Agents](/docs/ai/agents) — Agents that answer through the Chats API
- [Metric types](/docs/usage-and-billing/metric-types) — How AI token and OCR metrics deduct credits
