---
title: 'Managing Content'
description: 'Learn how to create and publish content in the dashboard, use locking, history and rollback, filter and export content, and add content with AI.'
category: 'core-features'
slug: 'contents'
locale: 'en'
apiVersion: 'v1'
order: 2
updatedAt: '2026-11-11'
---

# Managing Content

When you create a Table, a content list and input form matching its Columns are generated automatically. You can create and publish content on this screen without building a separate admin interface.

By the end of this guide, you will know how to create content and make it available through the API, and how to use management features such as locking, history, and export.

> Creating and publishing content requires the Owner, Admin, or Editor role in the Project. Guests can only view content.

---

## Creating Content

1. In **Project → Tables**, select a Table.
2. Click the add content button to open the input form.
3. Enter values in the input UI for each Column type. Content that does not satisfy Column options such as required values, length, or range cannot be saved.
4. Choose **Save as draft** or **Save and publish**.

Depending on the Table settings, the following fields are also shown.

| Table setting | Added fields |
| ------------- | ------------ |
| Content Slug support | `slug` — a unique address you can use instead of the ID in the API |
| SEO support | `seo_title`, `seo_description`, `seo_image` |
| Multilingual Columns | A tab for entering a value in each language configured for the Project |

> If a Table is set to **Read only**, its content cannot be edited in the dashboard, and data can only be added through the API.

---

## Publishing

**Only published content can be fetched through the REST API.** Content in draft is visible only in the dashboard.

| Action | Result |
| ------ | ------ |
| Publish | The content becomes public and its publish date (`published_at`) is recorded. |
| Unpublish | The content can no longer be fetched through the API. The data is kept. |
| Save as draft | Saves your changes. Content that has never been published stays private. |

You can select multiple items in the list to publish or unpublish them at once.

### Publication Stages

The publication stage shown in the list tells you the public state of each item.

| Stage | Meaning |
| ----- | ------- |
| Pending publication | Content that has never been published |
| Out of date | Content that has been edited since it was published |
| Up to date | Content whose current state matches what was last published |

> **Be careful when editing published content.** For content that is already published, changes saved as a draft may be reflected in API responses immediately. To work on changes that should not be public, unpublish the content first.

### Status

You can assign each item a status that reflects your workflow.

| Status | Description |
| ------ | ----------- |
| Draft | Content being written |
| In Review | Content awaiting review |
| Approved | Approved content |
| Published | Published content |
| Archived | Archived content |

---

## Locking

Locking content protects it from accidental changes or deletion.

- To edit locked content in the dashboard, you must **Unlock** it first.
- Locked content cannot be updated or deleted through the API. Locked items are skipped in requests that delete multiple items at once.
- You can select multiple items in the list and use **Change lock status** to change them all at once.
- Locking is available on the **Pro plan and above**. On the Free plan you cannot lock content, but you can always unlock content that is already locked.

---

## History and Rollback

In the **History** panel of the content form, you can view previous versions and compare them with the current version.

- Select a previous version and **Roll back** to restore the content as it was at that point. The current version before the rollback also remains in the history.
- Version history and rollback are available on the **Pro plan and above**. See [Plans and Limits](/docs/usage-and-billing/plans-and-limits).

---

## Finding Content in the List

Use filter, sort, and search in the toolbar above the content list.

- **Filter**: Filter by conditions suited to each Column type. For example, text supports contains, starts with, and ends with; numbers support comparisons; dates support before, after, and between; categories support inclusion. You can add multiple conditions and combine them with AND or OR.
- **Sort**: Sort by creation date, update date, publish date, and sortable Columns. File, editor, and reference Columns, and Columns that store multiple values, cannot be sorted.
- **Search**: Find keywords in content values.

---

## Exporting

Use **Export** in the list to download content as a CSV file.

---

## Adding Content with AI

| Feature | Description |
| ------- | ----------- |
| Add from text | AI analyzes pasted text or a file (txt, md, json, csv, xlsx, xls, xml, yaml, html, and more) and turns it into content matching your Columns. |
| Add sample content | AI creates example content that fits your Table structure. Useful for previewing your screens. |
| Auto-fill | AI fills empty values per cell, row, or Column. |

AI features use credits based on generative AI tokens. See [Metric types](/docs/usage-and-billing/metric-types) for how credits are deducted and [Ask AI (Assistant)](/docs/ai/ask-ai) for doing the same through the assistant.

---

## Deleting

Select content and delete it. Locked content must be unlocked first.

---

## Next Steps

- [Table Design](/docs/core-features/table-design) — Learn how to choose Column types and options.
- [Request Examples](/docs/rest-api/request-examples) — Fetch published content through the API.
- [Webhooks](/docs/core-features/webhooks) — Notify external systems of content changes.
