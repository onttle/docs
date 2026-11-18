---
title: 'Translations'
description: "Manage translation resources and connect them to your application's i18n library."
category: 'core-features'
slug: 'translations'
locale: 'en'
apiVersion: 'v1'
order: 2
updatedAt: '2026-10-30'
---

# Translations

By the end of this guide, you will be able to manage translation key-value pairs in Onttle and fetch locale-specific translations via the API in your front-end application.

**Prerequisites:** A Project must be created. An API Key is required; a publishable key (`pk-proj_…`) is enough because translations are read-only.

---

## Translation Resource Structure

Onttle translations are managed as **key-value** pairs. A single key stores a separate value for each supported locale.

```
Key: "common.save"
  ko: "저장"
  en: "Save"

Key: "common.cancel"
  ko: "취소"
  en: "Cancel"

Key: "common.delete"
  ko: "삭제"
  en: "Delete"

Key: "common.confirm"
  ko: "확인"
  en: "Confirm"

Key: "common.close"
  ko: "닫기"
  en: "Close"

Key: "nav.home"
  ko: "홈"
  en: "Home"

Key: "nav.settings"
  ko: "설정"
  en: "Settings"

Key: "nav.profile"
  ko: "프로필"
  en: "Profile"

Key: "button.submit"
  ko: "제출"
  en: "Submit"

Key: "button.next"
  ko: "다음"
  en: "Next"

Key: "button.previous"
  ko: "이전"
  en: "Previous"

Key: "error.required"
  ko: "필수 입력 항목입니다"
  en: "This field is required"

Key: "error.invalid"
  ko: "유효하지 않은 값입니다"
  en: "Invalid value"

Key: "message.success"
  ko: "성공적으로 처리되었습니다"
  en: "Successfully processed"

Key: "message.welcome"
  ko: "환영합니다"
  en: "Welcome"
```

---

## Step 1: Configure Supported Languages

1. Navigate to Project settings.
2. In the **Languages** menu, add the languages you want to support.
3. Select the default language. The default language is used for API requests that do not specify a locale.

---

## Step 2: Add Translation Keys

1. Click **Translations** in the Project menu.
2. Click **New Translation**.
3. Enter a key name. Use dot (`.`) separators to represent hierarchy.
   - Examples: `nav.home`, `button.submit`, `error.required`
4. Enter the corresponding value for each locale.
5. Click **Save**.

> **[IMAGE AREA]** New translation key modal

---

## Step 3: Fetch Translations via API

Retrieve the full translation list or fetch translations for a specific locale.

**Fetch all translations**

```bash
curl https://api.onttle.com/v1/{projectId}/translations \
  -H "Authorization: Bearer $ONTTLE_API_KEY"
```

**Response Example**

```json
[
  {
    "key": "common.save",
    "values": {
      "ko": "저장",
      "en": "Save"
    }
  },
  {
    "key": "common.cancel",
    "values": {
      "ko": "취소",
      "en": "Cancel"
    }
  }
]
```

---

## Integration Example with next-intl

Connect the Onttle Translations API to a Next.js project using `next-intl`.

```typescript
// i18n/request.ts
import { getRequestConfig } from 'next-intl/server'

export default getRequestConfig(async ({ locale }) => {
  // Fetch translations from Onttle API
  const res = await fetch(
    `https://api.onttle.com/v1/${process.env.ONTTLE_PROJECT_ID}/translations`,
    {
      headers: {
        Authorization: `Bearer ${process.env.ONTTLE_API_KEY}`,
      },
      next: { revalidate: 3600 }, // Cache for 1 hour
    },
  )

  const { list } = await res.json()

  // Convert to a flat key-value object
  const messages = list.reduce(
    (
      acc: Record<string, string>,
      item: { key: string; values: Record<string, string> },
    ) => {
      acc[item.key] = item.values[locale] ?? item.values['en'] ?? item.key
      return acc
    },
    {},
  )

  return { messages }
})
```

---

## Handling Missing Translations

Use a fallback strategy when a specific locale value is absent from the API response.

```typescript
function getTranslation(
  values: Record<string, string>,
  locale: string,
  defaultLocale = 'en',
): string {
  return values[locale] ?? values[defaultLocale] ?? ''
}
```

Choose between displaying the key name itself or substituting the default language value, based on your service requirements.

---

## Usage Tracking

Translation data is tracked under the `DATA.STORE.BYTES` metric.

- Creating a new translation key records the size of the stored data.
- Updating a translation value records the difference in data size before and after the change.

---

## Next Steps

- [Table Design](/docs/guides/table-design) — Use the `localization` option on TEXT and EDITOR Columns for per-content multilingual support.
- [REST API Usage](/docs/rest-api/query-parameters) — Learn how to use the locale query parameter.
- [Usage & Billing](/docs/usage-and-billing/metric-types) — Understand how DATA.STORE.BYTES is recorded.
