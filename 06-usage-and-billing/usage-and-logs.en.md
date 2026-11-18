---
title: 'Usage and Logs'
description: 'Learn how to check credit usage on the Workspace and Project usage dashboards and how to view API request logs.'
category: 'usage-and-billing'
slug: 'usage-and-logs'
locale: 'en'
apiVersion: 'v1'
order: 4
updatedAt: '2026-10-30'
---

# Usage and Logs

Onttle records service usage by metric and deducts credits based on it. On the usage screen, you can see how many credits each feature used, and on the logs screen, you can view API requests one by one.

| Screen | Location | Required role |
| ------ | -------- | ------------- |
| Workspace usage | **Workspace → Usage** | Owner, Admin, Editor |
| Project usage | **Project → Usage** | Owner, Admin, Editor |
| Logs | **Project → Logs** | Owner, Admin, Editor (Pro plan and above) |

---

## Usage

### Range

| Option | Range |
| ------ | ----- |
| This cycle | From the start of the current billing cycle until now |
| Last 7 Days | 7 days including today |
| Last 30 Days | 30 days including today |
| Exact Dates | Up to 100 days |

### Views

- **Group by**: View usage By Product (by metric) or By Project. The By Project view is available in Workspace usage.
- **Chart interval**: Daily, Weekly, Monthly, Cumulative

### Summary

| Item | Description |
| ---- | ----------- |
| Total Usage Events | Number of usage events recorded in the range |
| Total Used Credit | Credits deducted in the range |
| Used this cycle | Credits used out of those granted for this billing cycle, and the percentage |

### Metrics

| Item | Recorded when |
| ---- | ------------- |
| Data Stored | Contents, translations, and variables are stored |
| API Read Requests | GET requests |
| API Write Requests | POST, PUT, and DELETE requests |
| API Transfer | Size of data sent in API responses |
| Webhook Requests | Webhooks are delivered |
| Upload Requests, Upload Transfer | Files are uploaded |
| Image Optimization Requests | Image thumbnails are generated |
| OCR Requests | Text is extracted from files |
| Generative Tokens | AI generation features such as Auto-fill, suggestions, and Add from text |
| Ask AI Tokens | The dashboard AI assistant and Chat search |
| Embedding Tokens | Vector sync and embedding search |

See the [`/pricing`](/pricing) page for the credit rate of each item, and [Metric Types](/docs/usage-and-billing/metric-types) for the definition of each metric.

### Credit Balance

Your credit balance is always visible in the Workspace sidebar. When your balance gets low, **Your credits are running low.** is shown, and when it is used up, **You have run out of credits.** is shown. You can view the credits granted for each billing cycle in **Workspace → Billing**.

---

## Logs

In **Project → Logs**, view REST API requests made with API Keys. Use it to find out why a request failed or to see which key is calling which path.

| Column | Description |
| ------ | ----------- |
| Time | When the request was made |
| Status | HTTP status code |
| Host | Host that sent the request |
| Path | API path that was called |
| Messages | Error messages, etc. |
| API Key | API Key used for the request |

### Filters

- **Range**: From the last 30 minutes up to the last week, or exact dates
- **Status**: 2xx, 4xx, 5xx
- **Method**: GET, POST, PUT, DELETE
- **Keyword**: Search in paths or messages

> Logs are available on the **Pro plan and above**. On the Free plan, request details are not stored, so you can only view requests made after you upgrade.

---

## Next Steps

- [Subscription and Credits](/docs/usage-and-billing/subscription-and-credits) — Learn what happens when credits run out and how to buy additional credits.
- [Error Codes](/docs/rest-api/error-codes) — Look up the meaning of error codes shown in the logs.
