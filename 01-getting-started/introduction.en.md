---
title: 'Introduction'
description: 'A brief overview of what Onttle provides and how to get started.'
category: 'getting-started'
slug: 'introduction'
locale: 'en'
apiVersion: null
order: 1
updatedAt: '2026-10-30'
---

# Introduction to Onttle

Onttle is a content infrastructure platform built for front-end developers. It combines schema management, an auto-generated CMS, and a REST API into a single service.

With a single Onttle API Key, you can fetch all the content your application needs — no back-end server required.

```
┌──────────────┐     ┌─────────────┐     ┌──────────────┐     ┌─────────────┐
│  Workspace   │ ──▶ │   Project   │ ──▶ │    Table     │ ──▶ │  REST API   │
│Team · Billing│     │Service unit │     │Content schema│     │Data delivery│
└──────────────┘     └─────────────┘     └──────────────┘     └─────────────┘
```

---

## Key Features

| Feature                | Description                                                                       |
| ---------------------- | --------------------------------------------------------------------------------- |
| **Tables & Contents**  | Define data schemas with typed columns and manage content                         |
| **Auto-generated CMS** | Defining a table schema automatically creates input, edit, and publishing screens |
| **REST API**           | Instantly access published content via standard HTTP requests                     |
| **Translations**       | Manage key-value translation resources and query by locale                        |
| **File Storage**       | Upload images and documents with optimization and OCR extraction                  |
| **Variables**          | Serve per-environment configuration values and feature flags via API              |
| **Webhooks**           | Deliver content create, update, and delete events to external systems             |
| **AI Features**        | Automate content work with Auto-fill, column suggestions, and AI Ask              |
| **Vector Search**      | Sync table data into vectors to enable semantic search                            |

---

## Basic Workflow

When starting with Onttle, follow these steps in order.

1. **Create a Workspace** — The top-level unit for managing teams and billing.
2. **Create a Project** — Separate data by service or domain.
3. **Design Tables** — Define content schemas with typed columns.
4. **Write Content** — Enter data matching the schema and publish it.
5. **Issue an API Key** — Generate a publishable (read-only) or secret (read and write) key within the project scope.
6. **Call the API** — Make REST API requests using `Authorization: Bearer` header.

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ 1. Create    │ ──▶ │ 2. Create    │ ──▶ │ 3. Design    │
│  Workspace   │     │   Project    │     │   Tables     │
└──────────────┘     └──────────────┘     └──────┬───────┘
                                                   │
                                                   ▼
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ 6. Call the  │ ◀── │ 5. Issue API │ ◀── │ 4. Write     │
│    API       │     │     Key      │     │   Content    │
└──────────────┘     └──────────────┘     └──────────────┘
```

---

## Dashboard Users vs. External API Users

There are two types of principals in Onttle.

**Dashboard Users**

- Create and manage Workspaces and Projects.
- Directly edit table schemas and contents.
- Invite members and configure roles.
- Monitor usage and billing.

**External API Users (Applications)**

- Access the REST API using an issued API Key.
- Can only read data with a publishable key, and read or write data with a secret key.
- Can receive change events via Webhooks.

---

## Next Steps

- [Quick Start](/docs/getting-started/quick-start) — Follow the steps from Workspace creation to your first API call.
- [Concepts](/docs/getting-started/concepts) — Understand the relationship between Workspaces, Projects, and Tables.
- [API Reference](/docs/api-reference/endpoints) — Browse all available endpoints.
