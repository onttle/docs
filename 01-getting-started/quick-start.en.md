---
title: 'Quick Start'
description: 'Step-by-step guide from workspace creation to your first API call.'
category: 'getting-started'
slug: 'quick-start'
locale: 'en'
apiVersion: 'v1'
order: 2
popular: true
updatedAt: '2026-11-12'
---

# Quick Start

By the end of this guide, you will have created content in Onttle and fetched it via the REST API. This takes about 10 minutes.

**Prerequisites:** You need an Onttle account. If you don't have one, [sign up](/signup) first.

---

## Set Up with an AI Agent

Using an AI coding agent such as Claude Code, Cursor or Codex? Click **Onboard Your Agent** at the top of this page (also in the dashboard header) to copy the prompt below, then paste it into your agent.

```text
Set up Onttle for me. Fetch https://onttle.com/get-started.md and follow it
```

The agent reads the [agent guide](/get-started.md), proposes a table design for your codebase, writes the API client and tells you which steps to do in the dashboard. To do it yourself, follow the steps below.

> **You can also connect over MCP.** Register `https://api.onttle.com/mcp` in your agent and it reads your project's table structure and contents directly, and changes them after you confirm. See [MCP Server](/docs/ai/mcp) for how to connect.

---

## Step 1: Create a Workspace

A Workspace is the top-level unit for managing members, billing, and usage.

1. Log in to the dashboard.
2. Click the **Workspace selector** in the top left → **Create new Workspace**.
3. Enter a name and click **Create**.

> **[IMAGE AREA]** Workspace creation modal

---

## Step 2: Create a Project

A Project separates API Keys and table data by application or domain.

1. From the Workspace home, click the **New Project** button.
2. Enter a project name (e.g., `my-blog`).
3. Select the members to invite and the language.
4. In the panel on the right, you can use AI to generate the project's table structure. To build it yourself, select **Create manually**.
5. Click **Create Project**.

> **[IMAGE AREA]** Project creation screen

---

## Step 3: Create a Table and Add Columns

A Table defines the content schema. Creating the table and adding its columns both happen on the same table creation screen. In this example, we create a blog posts table.

1. From the Project home, click **New Table**.
2. Enter the table name: `posts`.
3. Click **Add Column** and add the following columns in order.

| Column Name      | Type   | Options  |
| ---------------- | ------ | -------- |
| `title`          | TEXT   | required |
| `body`           | EDITOR | —        |
| `published_date` | DATE   | —        |

4. Click **Create**.

> **[IMAGE AREA]** Table creation screen (including column setup)

---

## Step 4: Write Your First Content

1. Click the **Contents** tab.
2. Click **New Content**.
3. Fill in the `title`, `body`, and `published_date` fields.
4. Change the status from **DRAFT** to **PUBLISHED** in the top right.
5. Click **Save**.

Content must be in `PUBLISHED` status to appear in external API responses by default.

> **[IMAGE AREA]** Content editing and status change screen

---

## Step 5: Issue an API Key

1. Go to **API Keys** in the Project settings.
2. Click **New API Key**.
3. Enter a name and choose the key type. For this guide pick **Publishable** (`pk-proj_…`): it can only read (GET), so it is safe to use from a browser. Choose **Secret** (`sk-proj_…`) only when your server needs to create, update or delete data.
4. Click **Create**, then copy the generated key.

> **[IMAGE AREA]** API Key creation screen

The full API Key value is only shown once immediately after creation. Onttle keeps only a hash of the key, so it cannot be displayed again: the list shows just the prefix and last 4 characters. Store it somewhere safe. The key type cannot be changed later; issue a new key instead.

---

## Step 6: Set an Environment Variable

Manage your API Key as an environment variable. Never hard-code it in your source code.

```bash
# .env.local
ONTTLE_API_KEY=your_api_key_here
```

---

## Step 7: Fetch Contents

Now fetch your content via the REST API. Find your `{projectId}` in the Project settings.

**curl**

```bash
curl https://api.onttle.com/v1/{projectId}/tables/posts/contents \
  -H "Authorization: Bearer $ONTTLE_API_KEY"
```

**TypeScript (fetch)**

```typescript
const res = await fetch(
  'https://api.onttle.com/v1/{projectId}/tables/posts/contents?status=PUBLISHED',
  {
    headers: {
      Authorization: `Bearer ${process.env.ONTTLE_API_KEY}`,
    },
  },
)

const { list, pagination } = await res.json()
```

**Success Response Example**

```json
{
  "list": [
    {
      "id": "cnt_01hxk2",
      "slug": "hello-onttle",
      "status": "PUBLISHED",
      "data": {
        "title": "Hello Onttle",
        "published_date": "2026-08-30T09:00:00.000Z"
      },
      "created_at": "2026-08-30T09:00:00.000Z",
      "updated_at": "2026-08-30T09:00:00.000Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 1
  }
}
```

---

## Troubleshooting

| Symptom            | What to Check                                                                |
| ------------------ | ---------------------------------------------------------------------------- |
| `401 Unauthorized` | Verify the Authorization header is included and the API Key value is correct |
| `403 Forbidden`    | A publishable key (`pk-proj_…`) was used for a write; use a secret key (`sk-proj_…`). Also check allowed hosts and Project scope |
| Empty `list` array | Confirm the content status is `PUBLISHED`                                    |
| `404`              | Verify `{projectId}` and the table name (`posts`)                            |

---

## Next Steps

- [Concepts](/docs/getting-started/concepts) — Deepen your understanding of Workspaces, Projects, and Tables.
- [Table Design](/docs/guides/table-design) — Learn how to design more complex schemas.
- [Authentication](/docs/api-reference/authentication) — Understand API Key permissions and security.
