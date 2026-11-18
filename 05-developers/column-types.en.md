---
title: 'Column Types'
description: 'The properties, options, and behavior of all Column types supported by Onttle.'
category: 'developers'
slug: 'column-types'
locale: 'en'
apiVersion: 'v1'
order: 1
updatedAt: '2026-11-16'
---

# Column Types

A Column defines the data type for a field in a Table. The type determines the input method, storage format, and shape of the value in API responses.

---

## Type List

### TEXT

Enter text data.

| Item       | Value |
| ---------- | ----- |
| Searchable | Yes   |
| Sortable   | Yes   |

Example format

```json
"Hello Onttle"
```

Options

| Option Key     | Option Name    | Description                                                     |
| -------------- | -------------- | --------------------------------------------------------------- |
| `description`  | Description    | Enter field description (optional)                              |
| `placeholder`  | Placeholder    | Enter field placeholder (optional)                              |
| `required`     | Required       | Set whether this field is required.                             |
| `localization` | Localization   | Set whether this field allows localized input.                  |
| `unique`       | Unique         | Set whether this field's value must be unique within the table. |
| `multiple`     | Array          | Set whether multiple text values can be entered as an array.    |
| `textarea`     | Textarea       | Set whether this field allows multiline input.                  |
| `allowHtml`    | Allow HTML     | Set whether HTML tags are allowed in this field.                |
| `defaultValue` | Default Value  | Set the default value for this text field.                      |
| `minText`      | Minimum Length | Set the minimum length of text that can be entered.             |
| `maxText`      | Maximum Length | Set the maximum length of text that can be entered.             |

### NUMBER

Enter numeric data.

| Item       | Value |
| ---------- | ----- |
| Searchable | Yes   |
| Sortable   | Yes   |

Example format

```json
123
```

Options

| Option Key      | Option Name    | Description                                                                                                             |
| --------------- | -------------- | ----------------------------------------------------------------------------------------------------------------------- |
| `description`   | Description    | Enter field description (optional)                                                                                      |
| `placeholder`   | Placeholder    | Enter field placeholder (optional)                                                                                      |
| `autoIncrement` | Auto Increment | Set this field to use an automatically increasing number. It cannot be entered manually and is unique within the table. |
| `required`      | Required       | Set whether this field is required.                                                                                     |
| `multiple`      | Array          | Set whether multiple numeric values can be entered.                                                                     |
| `defaultValue`  | Default Value  | Set the default value for this number field.                                                                            |
| `min`           | Minimum Value  | Set the minimum numeric value that can be entered.                                                                      |
| `max`           | Maximum Value  | Set the maximum numeric value that can be entered.                                                                      |
| `step`          | Step           | Set the increment or decrement step for numeric input.                                                                  |

### BOOLEAN

Enter a true or false value.

| Item       | Value |
| ---------- | ----- |
| Searchable | Yes   |
| Sortable   | Yes   |

Example format

```json
true
```

Options

| Option Key     | Option Name   | Description                                   |
| -------------- | ------------- | --------------------------------------------- |
| `description`  | Description   | Enter field description (optional)            |
| `placeholder`  | Placeholder   | Enter field placeholder (optional)            |
| `defaultValue` | Default Value | Set the default value for this Boolean field. |

### EMAIL

Enter an email address.

| Item       | Value |
| ---------- | ----- |
| Searchable | Yes   |
| Sortable   | Yes   |

Example format

```json
"dev@onttle.com"
```

Options

| Option Key    | Option Name | Description                                          |
| ------------- | ----------- | ---------------------------------------------------- |
| `description` | Description | Enter field description (optional)                   |
| `placeholder` | Placeholder | Enter field placeholder (optional)                   |
| `required`    | Required    | Set whether this field is required.                  |
| `multiple`    | Array       | Set whether multiple email addresses can be entered. |

### URL

Enter a web address (URL).

| Item       | Value |
| ---------- | ----- |
| Searchable | Yes   |
| Sortable   | Yes   |

Example format

```json
"https://onttle.com"
```

Options

| Option Key     | Option Name   | Description                               |
| -------------- | ------------- | ----------------------------------------- |
| `description`  | Description   | Enter field description (optional)        |
| `placeholder`  | Placeholder   | Enter field placeholder (optional)        |
| `required`     | Required      | Set whether this field is required.       |
| `multiple`     | Array         | Set whether multiple URLs can be entered. |
| `defaultValue` | Default Value | Enter default value                       |

### FILE

Upload a file or asset.

| Item       | Value |
| ---------- | ----- |
| Searchable | No    |
| Sortable   | No    |

Example format

```json
"550e8400-e29b-41d4-a716-446655440000"
```

Options

| Option Key    | Option Name | Description                                         |
| ------------- | ----------- | --------------------------------------------------- |
| `description` | Description | Enter field description (optional)                  |
| `placeholder` | Placeholder | Enter field placeholder (optional)                  |
| `required`    | Required    | Set whether the file upload field is required.      |
| `multiple`    | Array       | Set whether multiple files can be uploaded at once. |

### REFERENCE

References data from another table.

| Item       | Value |
| ---------- | ----- |
| Searchable | No    |
| Sortable   | No    |

Example format

```json
"550e8400-e29b-41d4-a716-446655440001"
```

Options

| Option Key | Option Name | Description                                              |
| ---------- | ----------- | -------------------------------------------------------- |
| `required` | Required    | Set whether this field is required.                      |
| `multiple` | Array       | Set whether multiple references can be selected at once. |

### EDITOR

Enter HTML or Markdown content with the editor.

| Item       | Value |
| ---------- | ----- |
| Searchable | Yes   |
| Sortable   | No    |

Example format

```json
{ "type": "doc", "content": [] }
```

Two formats can be written, and the REST API, the dashboard and the [MCP server](/docs/ai/mcp) all take the same ones.

| Write format    | Example                                                                        |
| --------------- | ------------------------------------------------------------------------------ |
| Markdown string | `"# Title\n\nBody in **bold**"`                                                |
| TipTap document | The `{ "type": "doc", "content": [] }` object, or that object as a JSON string |

```json
{
  "title": "New post",
  "body": "# Title\n\nBody in **bold**"
}
```

Markdown is converted to a TipTap document when it is saved. Plain text is read as Markdown too, so it can be sent as it is. A localized column takes one value per language in the same format: `{ "ko": value, "en": value }`.

Responses return the stored document together with its rendered forms.

```json
{ "json": { "type": "doc", "content": [] }, "html": "…", "markdown": "…", "plain": "…" }
```

Options

| Option Key     | Option Name  | Description                                    |
| -------------- | ------------ | ---------------------------------------------- |
| `description`  | Description  | Enter field description (optional)             |
| `placeholder`  | Placeholder  | Enter field placeholder (optional)             |
| `localization` | Localization | Set whether this field allows localized input. |

### JSON

Stores arbitrary JSON data. Suited to data without a fixed shape, such as settings or metadata.

| Item       | Value |
| ---------- | ----- |
| Searchable | Yes   |
| Sortable   | No    |

Example Format

```json
{ "theme": "dark", "tags": ["cms", "api"], "limit": 10 }
```

Writes accept two forms, and the REST API, the dashboard and the [MCP server](/docs/ai/mcp) all take the same ones.

| Write form | Example                                                          |
| ---------- | ---------------------------------------------------------------- |
| JSON value | `{ "theme": "dark" }`, `[1, 2, 3]`, `10`, `true`                 |
| JSON text  | `"{\"theme\": \"dark\"}"` — a string is read as JSON text    |

A string is read as JSON text, so text that is not valid JSON is not saved and an error is returned. To store a JSON string value itself (`"hello"`), send it as JSON text including the quotes: `"\"hello\""`.

Responses return the parsed value of the stored JSON text.

```json
{ "settings": { "theme": "dark", "tags": ["cms", "api"], "limit": 10 } }
```

Options

| Option Key     | Option Name   | Description                                      |
| -------------- | ------------- | ------------------------------------------------ |
| `description`  | Description   | Enter field description (optional)               |
| `placeholder`  | Placeholder   | Enter field placeholder (optional)               |
| `required`     | Required      | Set whether this field is required.              |
| `defaultValue` | Default Value | Set the default JSON value. It must be valid JSON. |

### COLOR

Select a color with the color picker.

| Item       | Value |
| ---------- | ----- |
| Searchable | Yes   |
| Sortable   | Yes   |

Example format

```json
"#4F46E5"
```

Options

| Option Key    | Option Name | Description                                       |
| ------------- | ----------- | ------------------------------------------------- |
| `description` | Description | Enter field description (optional)                |
| `placeholder` | Placeholder | Enter field placeholder (optional)                |
| `required`    | Required    | Set whether this field is required.               |
| `multiple`    | Array       | Set whether multiple color values can be entered. |

### DATE

Enter a date.

| Item       | Value |
| ---------- | ----- |
| Searchable | Yes   |
| Sortable   | Yes   |

Example format

```json
"2026-08-30"
```

Options

| Option Key     | Option Name   | Description                                      |
| -------------- | ------------- | ------------------------------------------------ |
| `description`  | Description   | Enter field description (optional)               |
| `placeholder`  | Placeholder   | Enter field placeholder (optional)               |
| `required`     | Required      | Set whether this field is required.              |
| `multiple`     | Array         | Set whether multiple date values can be entered. |
| `defaultValue` | Default Value | Set the default value for this date field.       |

### TIMESTAMP

Enter a timestamp.

| Item       | Value |
| ---------- | ----- |
| Searchable | Yes   |
| Sortable   | Yes   |

Example format

```json
"2026-08-30T09:00:00.000Z"
```

Options

| Option Key     | Option Name   | Description                                           |
| -------------- | ------------- | ----------------------------------------------------- |
| `description`  | Description   | Enter field description (optional)                    |
| `placeholder`  | Placeholder   | Enter field placeholder (optional)                    |
| `required`     | Required      | Set whether this field is required.                   |
| `multiple`     | Array         | Set whether multiple timestamp values can be entered. |
| `defaultValue` | Default Value | Set the default value for this timestamp field.       |

---

## Column Value Shape in API Responses

```json
{
  "data": {
    "title": "Introducing Onttle",
    "view_count": 1024,
    "is_featured": true,
    "created_at": "2026-08-30T09:00:00.000Z",
    "thumbnail": {
      "id": "file_01hxk2",
      "url": "https://file.onttle.com/...",
      "name": "thumbnail.jpg",
      "size": 204800,
      "mimeType": "image/jpeg"
    }
  }
}
```

---

## Next Steps

- [Table Design Guide](/docs/core-features/table-design) — Combine Column types to design your content model.
- [OpenAPI Specification](/docs/developers/openapi) — Review the JSON Schema for each Column type.
