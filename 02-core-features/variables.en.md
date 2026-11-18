---
title: 'Variables'
description: 'How to manage project-wide global variables and retrieve them via the API.'
category: 'core-features'
slug: 'variables'
locale: 'en'
apiVersion: 'v1'
order: 4
updatedAt: '2026-08-30'
---

# Variables

A Variable is a global key-value store shared across a Project. Use it to manage configuration values that change frequently during operations — such as API base URLs, announcement strings, or feature flags — without code deployments.

---

## Use Cases

| Use Case                          | Examples                                               |
| --------------------------------- | ------------------------------------------------------ |
| Site-wide configuration           | Site name, default language, contact email             |
| Banner / announcement strings     | Event banner message, maintenance notice               |
| Feature flags                     | `maintenance_mode: true`, `new_feature_enabled: false` |
| Public keys for external services | Maps API public key, analytics script ID               |

Variables are not suitable for storing sensitive secrets. Manage secret keys as server environment variables instead.

---

## Managing Variables in the Dashboard

1. Click **Variables** in the left menu of your Project.
2. Click the **New Variable** button.
3. Enter a **Key** and **Value**, then click **Save**.

> **[IMAGE AREA]** Variables list screen — key, value, edit UI

The `snake_case` or `SCREAMING_SNAKE_CASE` format is recommended for Variable keys.

---

## Using Variables in Content and Translations

For text-type Variables, typing `{{` in a Contents or Translation text field opens an autocomplete list of the Variables registered in the Project. Selecting one inserts a token in the `{{var_key}}` format, which is replaced with the Variable's actual value wherever it is displayed.

```
{{var_site_name}} welcomes you. → My Blog welcomes you.
```

- The Rich Text editor supports the same insertion flow via a variable-insert button in its toolbar.
- When a linked Variable's value changes, the rendered content automatically reflects the latest value without any manual edits.
- `FILE`-type Variables are not eligible for text replacement, so they are excluded from the autocomplete list.

---

## Querying Variables via API

### List All Variables

```bash
curl https://api.onttle.com/v1/{projectId}/variables \
  -H "Authorization: Bearer $ONTTLE_API_KEY"
```

```json
{
  "list": [
    { "key": "site_name", "value": "My Blog" },
    { "key": "maintenance_mode", "value": "false" }
  ],
  "pagination": { "page": 1, "limit": 20, "total": 2 }
}
```

### Get a Specific Variable

```bash
curl https://api.onttle.com/v1/{projectId}/variables/{key} \
  -H "Authorization: Bearer $ONTTLE_API_KEY"
```

```json
{
  "key": "site_name",
  "value": "My Blog"
}
```

---

## Variables and Multilingual Content

Variable values are single strings. If you need different values per language, the recommended approach is to append the locale as a suffix to the key.

```
site_description_ko → "Onttle 기반 블로그입니다."
site_description_en → "A blog powered by Onttle."
```

Alternatively, use the Translation feature to manage locale-specific text.

---

## Using Variables in TypeScript

```typescript
// Load all Variables as an object
async function loadVariables(
  projectId: string,
): Promise<Record<string, string>> {
  const res = await fetch(
    `https://api.onttle.com/v1/${projectId}/variables?limit=1000`,
    { headers: { Authorization: `Bearer ${process.env.ONTTLE_API_KEY}` } },
  )
  const { list } = await res.json()
  return Object.fromEntries(
    list.map((v: { key: string; value: string }) => [v.key, v.value]),
  )
}

// Usage
const vars = await loadVariables('proj_01hxk2')
console.log(vars.site_name) // "My Blog"
```

---

## Next Steps

- [Translation Integration](/docs/core-features/translations) — Use the Translation feature to manage language-specific text.
- [REST API Request Examples](/docs/rest-api/request-examples) — Review common rules for Variable API requests.
