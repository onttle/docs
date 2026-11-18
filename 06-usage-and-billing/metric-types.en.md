---
title: 'Metric Types'
description: 'The types of usage metrics Onttle measures, when they are recorded, and the conditions for inclusion.'
category: 'usage-and-billing'
slug: 'metric-types'
locale: 'en'
apiVersion: 'v1'
order: 1
updatedAt: '2026-11-12'
---

# Metric Types

Onttle measures service usage through multiple metrics to calculate credit consumption. Understanding what each metric measures and when it is recorded makes it easier to predict and manage costs.

---

## Metric List

### Data Storage

| Metric Code        | Metric Name | Unit  | Description                                                                                                               |
| ------------------ | ----------- | ----- | ------------------------------------------------------------------------------------------------------------------------- |
| `DATA_STORE_BYTES` | Data Stored | Bytes | Data size added when contents, translations, and variables are saved. Recorded once at write time; only growth is counted |

### API Calls

| Metric Code          | Metric Name        | Unit  | Description                                                                                                                                |
| -------------------- | ------------------ | ----- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| `API_READ_CALLS`     | API Read Requests  | Count | Number of GET requests that query Contents, Translations, Variables, etc.                                                                  |
| `API_WRITE_CALLS`    | API Write Requests | Count | Number of POST/PUT/DELETE requests for creating, updating, or deleting Contents                                                            |
| `API_WEBHOOK_CALLS`  | Webhook Requests   | Count | Number of events Onttle sent to registered Webhook URLs. The balance is checked before delivery; when credit is short, delivery is skipped |
| `API_TRANSFER_BYTES` | API Transfer       | Bytes | Total data size transferred in API responses                                                                                               |

### Files

| Metric Code                     | Metric Name                 | Unit  | Description                                                                                                                     |
| ------------------------------- | --------------------------- | ----- | ------------------------------------------------------------------------------------------------------------------------------- |
| `FILE_UPLOAD_BYTES`             | Upload Transfer             | Bytes | Total size of uploaded files. Recorded once at upload                                                                           |
| `FILE_UPLOAD_CALLS`             | Upload Requests             | Count | Number of file upload requests. No credits are deducted                                                                         |
| `FILE_IMAGE_OPTIMIZATION_CALLS` | Image Optimization Requests | Count | Number of image optimization operations (resize, format conversion, etc.). No credits are deducted                              |
| `FILE_OCR_CALLS`                | OCR Requests                | Pages | Number of pages processed by OCR (Optical Character Recognition). An image counts as 1 page; a PDF counts its actual page count |

### AI

| Metric Code             | Metric Name       | Unit   | Description                                                                                                            |
| ----------------------- | ----------------- | ------ | ---------------------------------------------------------------------------------------------------------------------- |
| `AI_GENERATION_TOKENS`  | Generative Tokens | Tokens | Tokens used by generative AI features such as suggestions, auto-fill, sample data, translation, and keyword generation |
| `AI_ASK_TOKENS`         | AI Ask Tokens     | Tokens | Tokens used by the dashboard AI assistant, Ask AI, public API chats, and the search planner / rerank                   |
| `API_EMBEDDINGS_TOKENS` | Embedding Tokens  | Tokens | Tokens used to generate embeddings for Vector search                                                                   |

### Members

| Metric Code      | Metric Name    | Unit  | Description                                                                                                  |
| ---------------- | -------------- | ----- | ------------------------------------------------------------------------------------------------------------ |
| `MEMBER_SIGNUPS` | Member Signups | Count | Sign-ups that completed email verification in the [member feature](/docs/core-features/members). Logins, token refreshes, withdrawals and auth emails are not counted |

---

## Rates by Metric

Usage is rounded up to the metric's unit size and multiplied by the credits per unit. 1 credit is 1 KRW (VAT included), and the same rates apply to every plan.

| Metric                                               | Rate                                                             |
| ---------------------------------------------------- | ---------------------------------------------------------------- |
| `AI_GENERATION_TOKENS`                               | 500 credits per 1M tokens                                        |
| `AI_ASK_TOKENS`                                      | 500 credits per 1M tokens                                        |
| `API_EMBEDDINGS_TOKENS`                              | 100 credits per 1M tokens                                        |
| `API_READ_CALLS`                                     | 10 credits per 1,000 calls                                       |
| `API_WRITE_CALLS`                                    | 30 credits per 1,000 calls                                       |
| `API_WEBHOOK_CALLS`                                  | 300 credits per 1,000 calls                                      |
| `API_TRANSFER_BYTES`                                 | 1,000 credits per 10 GB                                          |
| `DATA_STORE_BYTES`                                   | 1,000 credits per 1 GB (charged once at write time, growth only) |
| `FILE_UPLOAD_BYTES`                                  | 1,000 credits per 10 GB (charged once at upload)                 |
| `FILE_OCR_CALLS`                                     | 10 credits per page                                              |
| `FILE_UPLOAD_CALLS`, `FILE_IMAGE_OPTIMIZATION_CALLS` | Free                                                             |

The live price list and a monthly cost calculator based on your expected usage are on the [`/pricing`](/pricing) page.

### From December 1, 2026

Starting December 1, 2026 at 00:00 KST, AI tokens and storage are calculated as follows. Until then, the rates above apply as is.

**AI tokens (`AI_GENERATION_TOKENS`, `AI_ASK_TOKENS`)**

Usage is recorded as **input-equivalent weighted tokens**, with a weight per token type, and priced at **700 credits** per 1M weighted tokens.

| Token type                  | Weight | Credits per 1M raw tokens |
| --------------------------- | ------ | ------------------------- |
| Input                       | ×1     | 700                       |
| Cached input                | ×0.1   | 70                        |
| Cache write                 | ×1.25  | 875                       |
| Output (reasoning included) | ×5     | 3,500                     |

**Storage**

Instead of a one-time charge at write time, a monthly storage fee is deducted daily based on the bytes stored that day (bytes × days).

| Metric Code                             | Metric Name                  | Rate                                            |
| --------------------------------------- | ---------------------------- | ----------------------------------------------- |
| `DATA_STORE_GB_DAYS`                    | Data Storage                 | 800 credits per GB-month                        |
| `FILE_STORAGE_GB_DAYS`                  | File Storage                 | 200 credits per GB-month                        |
| `DATA_STORE_BYTES`, `FILE_UPLOAD_BYTES` | Data Stored, Upload Transfer | 0 credits (still shown in usage for statistics) |

---

## When Metrics Are Recorded

| Metric               | When Recorded                                                                  |
| -------------------- | ------------------------------------------------------------------------------ |
| `DATA_STORE_BYTES`   | Immediately after contents, translations, or variables are saved (growth only) |
| `API_*_CALLS`        | Immediately after each request is processed (webhooks: right after delivery)   |
| `API_TRANSFER_BYTES` | After each response transmission completes                                     |
| `FILE_*`             | Immediately after upload/processing completes                                  |
| `AI_*`               | Immediately after AI processing completes                                      |

Requests that consume credits check the balance before running, and usage is recorded after the work finishes. When actual usage is larger than expected, such as a long AI answer, the balance can therefore drop below zero. See [Subscription and Credits](/docs/usage-and-billing/subscription-and-credits) for details.

---

## Where to View Metrics

- **Dashboard**: Project → **Usage** tab to view period-based metric graphs.
- **Logs**: Project → **Logs** tab to view individual API call records.
- **Pricing**: Visit the [`/pricing`](/pricing) page to see the credit cost per metric and simulate your estimated monthly cost based on expected usage.

> **[IMAGE AREA]** Usage dashboard — period graph by metric type

---

## Next Steps

- [Subscription and Credits](/docs/usage-and-billing/subscription-and-credits) — Learn how usage is converted into credits and what happens when they run out.
- [Usage and Logs](/docs/usage-and-billing/usage-and-logs) — Check the usage dashboards and API request logs.
