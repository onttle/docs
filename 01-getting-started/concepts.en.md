---
title: 'Concepts'
description: 'Understand the relationship between Workspaces, Projects, Tables, and other core concepts.'
category: 'getting-started'
slug: 'concepts'
locale: 'en'
apiVersion: null
order: 3
updatedAt: '2026-10-30'
---

# Concepts

Onttle data is organized in a **Workspace → Project → Table → Contents** hierarchy. Understanding the role of each concept makes it easier to design schemas efficiently.

```text
Workspace   (team / organization)
   │
   │ 1 : N
   ▼
Project     (a single service / domain)
   │
   │ 1 : N
   ▼
Table       (content schema definition)
   │
   │ 1 : N
   ▼
Contents    (actual data rows)
```

---

## Workspace

A Workspace is the top-level unit for all resources, corresponding to a team or organization.

- **Member management:** Members invited to a Workspace can access the Projects they are assigned to.
- **Billing and usage:** Credit balance, auto-reload settings, and monthly usage limits are managed per Workspace.
- **Multiple Projects:** Multiple Projects can be created under a single Workspace.

**When should you create a new Workspace?**

Typically, a single Workspace per team or company is sufficient. Create a separate Workspace for an independent business unit that requires its own billing.

---

## Project

A Project corresponds to a single service or domain. API Keys and Table data are isolated within a Project's scope.

- **API Key scope:** An API Key belongs to a specific Project and can only access that Project's data.
- **Multiple Tables:** Multiple Tables can be created under a single Project.
- **Member roles:** Workspace members can be assigned per-Project roles (OWNER, ADMIN, EDITOR, GUEST).

**Workspace vs. Project**

| Item              | Workspace       | Project                     |
| ----------------- | --------------- | --------------------------- |
| Billing & credits | Workspace level | —                           |
| API Keys          | —               | Project level               |
| Member roles      | Workspace level | Project level (overridable) |
| Data isolation    | —               | Isolated per Project        |

---

## Table

A Table defines the content schema, analogous to a table in a relational database.

- **Column definitions:** Each Table has multiple Columns. Columns specify type, required status, default values, and more.
- **Contains Contents:** Contents are actual data rows that match the Table's schema.
- **Auto-generated CMS:** Creating a Table automatically generates input, edit, and publishing screens in the dashboard.

---

## Column

A Column defines a data field within a Table. The type determines the dashboard input UI and the API response format.

| Type        | Description                           | Key Options                                                |
| ----------- | ------------------------------------- | ---------------------------------------------------------- |
| `TEXT`      | Short or long text                    | required, localization, unique, textarea, minText, maxText |
| `NUMBER`    | Integer or decimal                    | autoIncrement, min, max, step                              |
| `DATE`      | Date and time                         | required, multiple                                         |
| `BOOLEAN`   | true / false                          | defaultValue                                               |
| `CATEGORY`  | Predefined option list                | required, multiple                                         |
| `FILE`      | Image or file                         | required, multiple                                         |
| `EDITOR`    | Rich text editor                      | localization                                               |
| `JSON`      | Unstructured JSON data                | defaultValue                                               |
| `REFERENCE` | Reference to another Table's Contents | required                                                   |

**The localization option**

Setting `localization` on a TEXT or EDITOR column allows storing different values per locale. You can then query by locale using `?locale=ko` in the API.

---

## Contents

Contents are the actual data rows created according to a Table's schema.

**Publishing Status**

**Status**: The publishing status can only be `DRAFT` or `PUBLISHED`. It is only visible in the CMS, and external API access is only possible for `PUBLISHED` content.

**Stage**: Based on the publishing status and whether the content has been published before, each content item is marked as either up to date or in need of an update.

| Stage            | Description                                                                           |
| ---------------- | ------------------------------------------------------------------------------------- |
| Awaiting publish | Not yet accessible via the API.                                                       |
| Up to date       | `PUBLISHED` and reflects the latest content.                                          |
| Update needed    | Previously `PUBLISHED`, but a different version is currently being edited as `DRAFT`. |

**Version History**

Open the Contents Form and use the "History" section to view, compare, and roll back versions. Versions increase in the form `v1`, `v2`, and so on.

**slug**

A `slug` is not generated automatically. It is only generated automatically for new Contents when the slug option is enabled on the Table.

**SEO**

When SEO support is enabled on a Table, the SEO title and description tabs become available on Contents.

**Read-only**

When read-only is enabled on a Table, data cannot be edited in the CMS and can only be controlled through API calls. This is mainly used for logs, payment history, and similar data.

---

## API Key

An API Key is the authentication credential used by external applications to access the REST API. It is issued within a Project's scope.

- **Key type:** A **publishable** key (`pk-proj_…`) is read-only (GET) and can be used in browsers or public websites. A **secret** key (`sk-proj_…`) can also create, update and delete data, upload files, and call the chat and embeddings APIs; use it on servers only. The type is fixed when the key is issued.
- **Allowed hosts:** Restrict requests to specific domains or IP addresses (CIDR ranges included).
- **Security:** The full API Key value is only visible immediately after creation. Onttle stores only a hash of the key, so the list shows just the prefix and last 4 characters. Manage it as an environment variable and never include a secret key in client-side code.

---

## Cautions When Deleting Data

- **Deleting a Column:** All data stored in that column within every Contents row is permanently removed.
- **Deleting a Table:** All Columns and Contents belonging to the Table are permanently removed.
- **Deleting a Project:** All Tables, Contents, and API Keys in the Project are permanently removed.
- **Deleting a Workspace:** All Projects, associated data, and billing information are permanently removed.

Deletion cannot be undone. Always export data before deleting anything in a production environment.

---

## Next Steps

- [Table Design](/docs/guides/table-design) — Design a Table schema suited to your real service.
- [Authentication](/docs/api-reference/authentication) — Learn about API Key permissions and security settings.
- [REST API Usage](/docs/rest-api/request-examples) — Learn how to make API calls step by step.
