---
title: 'TypeScript'
description: 'How Onttle automatically generates type-safe TypeScript definitions for every Table schema.'
category: 'developers'
slug: 'typescript'
locale: 'en'
apiVersion: 'v1'
order: 3
updatedAt: '2026-11-16'
---

# TypeScript Definitions

Onttle automatically generates TypeScript type definitions from your Table schema. Whenever you add or change a Column, the definitions are regenerated instantly, so your frontend code can consume Contents in a type-safe way without writing or maintaining types by hand.

---

## Where to Find It

Open **TypeScript** in the left menu of a Project to see the definitions for every Table in that Project.

```
/workspaces/{workspaceId}/projects/{projectId}/typescript
```

You can also jump directly to a specific Table's definition from the **Contents** toolbar by clicking **TypeScript definitions**, which scrolls to that Table's section.

> **[IMAGE AREA]** TypeScript page — Tables / Predefined tabs and a generated definition block

The page has two tabs:

| Tab            | Description                                                                           |
| -------------- | ------------------------------------------------------------------------------------- |
| **Tables**     | `Request` / `Response` interfaces generated per Table, based on its Columns           |
| **Predefined** | Shared types referenced by every Table, such as `ProjectFile`, `Category`, `Variable` |

Each definition block provides a **Copy** button to copy the code, and Table blocks provide shortcuts to **go to Contents** and **go to the API Endpoint** for that Table.

---

## Tables Tab

For each Table, two interfaces are generated: a `Request` interface for the request body used when creating or updating Contents, and a `Response` interface for the shape of data returned by the API.

### Type Name

The Table name is converted to `PascalCase` to build the type name. If the name starts with a number, `Table` is prefixed.

| Table Name   | Generated Type Name Prefix |
| ------------ | -------------------------- |
| `blog-post`  | `BlogPost`                 |
| `2024_promo` | `Table2024Promo`           |

### Request Interface

```typescript
export interface BlogPostRequest {
  slug?: string
  seo_title?: string
  seo_description?: string
  seo_image?: string
  is_publish?: boolean
  title: string
  body?: string
}
```

| Field                                         | Included When                                      |
| --------------------------------------------- | -------------------------------------------------- |
| `slug`                                        | The Table has Slug enabled                         |
| `seo_title` / `seo_description` / `seo_image` | The Table has SEO fields enabled                   |
| `is_publish`                                  | Always included                                    |
| Column fields                                 | Every editable Column, using its `name` as the key |

A field is optional (`?`) unless its Column has the **Required** option enabled. Columns with type `SEPARATOR`, `LABEL`, or `UNKNOWN` are layout-only and are excluded from the generated interfaces.

### Response Interface

```typescript
export interface BlogPostResponse {
  id: string
  slug: string
  created_at: string
  updated_at: string
  published_at: string | null
  data: {
    title: string
    body: string
  }
}
```

| Field                                            | Included When                                               |
| ------------------------------------------------ | ----------------------------------------------------------- |
| `id`, `created_at`, `updated_at`, `published_at` | Always included                                             |
| `slug`                                           | The Table has Slug enabled                                  |
| `seo_title` / `seo_description` / `seo_image`    | The Table has SEO fields enabled (`seo_image: ProjectFile`) |
| `data`                                           | All Columns except `SEPARATOR`, `LABEL`, and `UNKNOWN`      |

A Column's key is quoted as a string literal if its `name` is not a valid TypeScript identifier (for example, a name containing spaces or starting with a digit).

---

## Column Type Mapping

Each Column type maps to a TypeScript type as follows. The `Request` column shows the type used for input; the `Response` column shows the type returned by the API.

| Column Type                                          | Request Type                                                                   | Response Type             |
| ---------------------------------------------------- | ------------------------------------------------------------------------------ | ------------------------- |
| `NUMBER`                                             | `number`                                                                       | `number`                  |
| `BOOLEAN`                                            | `boolean`                                                                      | `boolean`                 |
| `DATE`, `TIMESTAMP`, `TEXT`, `EMAIL`, `URL`, `COLOR` | `string`                                                                       | `string`                  |
| `JSON`                                               | `unknown` (any JSON value or its JSON text) | `unknown` |
| `MAP`                                                | `Record<string, unknown>`                                                      | `Record<string, unknown>` |
| `EDITOR`                                             | `string \| { type: 'doc'; content?: unknown[] }` (Markdown text or a TipTap document) | `{ json: Record<string, unknown>; html: string; markdown: string; plain: string }` |
| `CATEGORY`                                           | `string`                                                                       | `Category`                |
| `FILE`                                               | `string`                                                                       | `ProjectFile`             |
| `REFERENCE`                                          | `string`                                                                       | `Record<string, unknown>` |
| Other / unrecognized types                           | `unknown`                                                                      | `unknown`                 |

### Array and Localization Modifiers

Two Column options change the generated type further, and can be combined:

| Option         | Effect                                                  |
| -------------- | ------------------------------------------------------- |
| `multiple`     | Wraps the type in `Array<T>`                            |
| `localization` | Wraps the type in `Partial<Record<ProjectLanguage, T>>` |

```typescript
// TEXT column with multiple + localization enabled
tags?: Partial<Record<ProjectLanguage, Array<string>>>
```

---

## Predefined Tab

The **Predefined** tab contains the shared types referenced across every Table's `Response` interface.

```typescript
export type ProjectLanguage = 'en' | 'ko'

export interface Translation {
  key: string
  value: Partial<Record<ProjectLanguage, string>>
  description: string | null
}

export interface Variable {
  key: string
  value: unknown
  description: string | null
}

export interface FileMeta {
  name: string
  extension: string
  lastModified: number
  size: number
  type: string
  width: number | null
  height: number | null
  exif: Record<string, string> | null
}

export interface FileThumbnail {
  path: string
  presigned_url: string
  size: string
  width: number
  height: number
}

export interface ProjectFile {
  id: string
  name: string
  description: string | null
  meta: FileMeta
  s3_path: string
  presigned_url: string
  thumbnails: FileThumbnail[]
  text_vision: string | null
  description_generated: string | null
  keywords: string[]
}

export interface Category {
  id: string
  name: string
}
```

`ProjectLanguage` is generated as a union of the languages configured for the current Project, so it always reflects the Project's actual language settings.

---

## Hovering Over Referenced Types

In the **Tables** tab, referenced type names such as `ProjectFile`, `Category`, and `ProjectLanguage` are underlined. Hover (or focus) over one to see its definition in a tooltip without switching to the **Predefined** tab.

---

## Using the Generated Types

Copy the definitions into your project and use them to type the response of a `fetch` call to the REST API.

```typescript
const res = await fetch(
  'https://api.onttle.com/v1/{projectId}/tables/blog-post/contents',
  { headers: { Authorization: `Bearer ${process.env.ONTTLE_API_KEY}` } },
)

const { list }: { list: BlogPostResponse[] } = await res.json()
console.log(list[0].data.title)
```

---

## Next Steps

- [Table Design](/docs/core-features/table-design) — Learn how Column configuration affects the generated definitions.
- [Response Format](/docs/rest-api/response-format) — See the full shape of list and single-item API responses.
- [OpenAPI Specification](/docs/developers/openapi) — Generate types for the entire API surface, not just Contents.
