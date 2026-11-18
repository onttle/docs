---
title: 'Categories'
description: 'Learn how to manage lists of options as categories, connect them to CATEGORY Columns, and fetch categories through the API.'
category: 'core-features'
slug: 'categories'
locale: 'en'
apiVersion: 'v1'
order: 3
updatedAt: '2026-10-30'
---

# Categories

Categories let you manage **lists of options** that content can choose from, at the Project level. Use them for fields with a fixed set of values, such as blog post topics, product colors, or a document's target audience.

Managing options as a category instead of writing them directly into Column options lets multiple Tables share the same list and lets you edit it in one place.

---

## Creating a Category

1. In **Project → Categories**, click **Create Category**.
2. Enter a category name and description. Names must be unique within a Project.
3. Use **Add Value** to add options. Values must be unique within a category.
4. Click **Create**.

Values are stored in the order you enter them, and this order is used as-is in the input form and API responses.

### Suggesting Values

After entering a category name, click **Suggest values** to have AI propose fitting values. Select the ones you want and click **Add**. Value suggestions use credits based on generative AI tokens.

---

## Connecting to a Table

1. When adding a Column to a Table, select the category to use from the **Category** group in the Column type list.
2. Choose whether to allow one value or multiple values (multiple).

In the content input form, the category's values appear as a selection list. Using **Suggest category values** on a content cell lets AI pick values that match the content.

In API responses, CATEGORY Column values are returned in the following format.

```json
{
  "category": { "id": "8b0c…", "name": "Tutorial" }
}
```

When creating or updating content through the API, send the **value's ID (UUID)**. For Columns that allow multiple values, send an array of IDs.

---

## Editing and Deleting Categories

- Select a category in the list to edit its name, description, and values.
- Deleting a category **deletes all data related to that category**. If any Columns use this category, check them before deleting.

---

## Fetching Through the API

Use `GET /categories` to fetch all categories and their values in a Project. You must enable the categories API under **API Endpoints → Pre-defined API**.

```bash
curl https://api.onttle.com/v1/{projectId}/categories \
  -H "Authorization: Bearer $ONTTLE_API_KEY"
```

```json
[
  {
    "id": "c1f2…",
    "name": "Docs Category",
    "description": null,
    "categories_values": [
      { "id": "a1…", "name": "getting-started" },
      { "id": "a2…", "name": "core-features" }
    ]
  }
]
```

Categories are returned in name order, and values are returned in the order they were saved in the category. You can use this order directly when building filter tabs or menus in your front end.

---

## Next Steps

- [Table Design](/docs/core-features/table-design) — See a schema example that includes a category Column.
- [Column Types](/docs/developers/column-types) — Review CATEGORY Column options and response format.
