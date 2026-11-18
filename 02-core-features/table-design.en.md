---
title: 'Table Design'
description: 'Convert content requirements into Columns and design a Table schema suited for production.'
category: 'core-features'
slug: 'table-design'
locale: 'en'
apiVersion: 'v1'
order: 1
updatedAt: '2026-11-12'
---

# Table Design

By the end of this guide, you will be able to translate real-world content requirements into Onttle Table schemas and understand publishing status management and API response formats.

**Prerequisites:** A Workspace and Project must already be created.

---

## Choosing a Column Type

Select the appropriate Column type based on the nature of the content field.

| Data Nature                           | Recommended Type                     |
| ------------------------------------- | ------------------------------------ |
| Title, summary, short text            | `TEXT`                               |
| Body, long text                       | `TEXT` (textarea option) or `EDITOR` |
| Rich content with HTML                | `EDITOR`                             |
| Numbers, prices, order                | `NUMBER`                             |
| Dates, publish dates                  | `DATE`                               |
| Active/inactive flags                 | `BOOLEAN`                            |
| Predefined choices (tags, categories) | `CATEGORY`                           |
| Images, attachments                   | `FILE`                               |
| Reference to another Table's Contents | `REFERENCE`                          |
| Unstructured JSON                     | `JSON`                               |

---

## Column Options

Key options available per type.

### required

Use for fields that must have a value. Saving is rejected if the value is empty.

### unique

Prevents duplicate values within the same Table. Use for fields that must be unique, like `slug`.

### localization

Available on `TEXT` and `EDITOR` types. Allows storing different values per locale in the same field. Request a specific locale via `?locale=ko` in the API.

### autoIncrement

On `NUMBER` type, automatically increments the value by 1 each time a content item is created. Useful for ordering fields.

### multiple

Stores multiple values in a single field. Use with `FILE` for multi-file attachments, or with `CATEGORY` for multi-select.

### defaultValue

Sets the initial value of the field when a content item is created.

---

## Sample Schema: Blog Post

```
Table: posts
├── title         TEXT        (required, localization)
├── slug          TEXT        (required, unique)
├── summary       TEXT        (textarea, localization)
├── body          EDITOR      (localization)
├── thumbnail     FILE
├── tags          CATEGORY    (multiple)
├── published_at  DATE
└── is_featured   BOOLEAN     (defaultValue: false)
```

**API Response Example**

```json
{
  "id": "cnt_01hxk2",
  "slug": "hello-onttle",
  "status": "PUBLISHED",
  "data": {
    "title": { "ko": "Onttle 소개", "en": "Hello Onttle" },
    "summary": { "ko": "첫 번째 글입니다", "en": "This is the first post" },
    "published_at": "2026-08-30T09:00:00.000Z",
    "is_featured": false
  }
}
```

> **[IMAGE AREA]** Dashboard screen for designing a blog posts Table schema

---

## Sample Schema: Product

```
Table: products
├── name          TEXT        (required, localization)
├── sku           TEXT        (required, unique)
├── description   EDITOR      (localization)
├── price         NUMBER      (required, min: 0)
├── currency      CATEGORY    (required)
├── images        FILE        (multiple)
├── stock         NUMBER      (defaultValue: 0)
└── is_available  BOOLEAN     (defaultValue: true)
```

---

## Publishing Status and API Integration

Default external API inclusion by content status.

| Status      | Included by Default | Description          |
| ----------- | ------------------- | -------------------- |
| `DRAFT`     | No                  | Work in progress     |
| `REVIEW`    | No                  | Submitted for review |
| `APPROVED`  | No                  | Review complete      |
| `PUBLISHED` | Yes                 | Published            |
| `ARCHIVED`  | No                  | Archived             |

You can explicitly request a specific status with `?status=DRAFT`. However, a secret API Key (`sk-proj_…`) is required to access non-published statuses.

---

## API Endpoints and Permission Rules

The lower part of the screen where you create or edit a Table sets up the Table's API.

- **API endpoints:** switch the list, single item, create, update, delete and search endpoints on or off. A disabled endpoint returns `403`.
- **Permission rules:** who may read, create, update and delete with a publishable key. The defaults are "Public" for reads and "Secret key only" for writes. With "Signed-in members" or "Owner only", [members](/docs/core-features/members) can read and write contents directly.

---

## Cautions When Changing Columns

Keep the following in mind when modifying a schema for a Table that already has data.

| Operation          | Caution                                                                |
| ------------------ | ---------------------------------------------------------------------- |
| Add Column         | Existing Contents will have `null` for the new field                   |
| Change Column type | Data may be lost if existing values are incompatible with the new type |
| Delete Column      | All values stored in that column are permanently deleted               |
| Add required       | Existing Contents with no value in that field will fail to save        |

Always export your Contents before deleting a Column from a Table with production data.

---

## Next Steps

- [API Reference - Tables](/docs/api-reference/endpoints) — Browse Table-related endpoints.
- [Translations](/docs/guides/translations) — Set up multilingual content using localization Columns.
- [File Upload](/docs/guides/file-upload) — Learn how to link files to FILE Columns.
