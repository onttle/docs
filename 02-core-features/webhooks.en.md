---
title: 'Webhook'
description: 'Onttle data change events delivered to external systems in real time — event types, payload structure, and integration examples.'
category: 'core-features'
slug: 'webhooks'
locale: 'en'
apiVersion: 'v1'
order: 5
updatedAt: '2026-08-30'
---

# Webhook

Webhooks deliver Onttle data change events to external systems in real time via HTTP POST requests sent to the registered URL.

---

## Event Types

Onttle supports three events following the Contents lifecycle.

| Event Code | When It Fires                                                         |
| ---------- | --------------------------------------------------------------------- |
| `CREATED`  | When new Contents are created                                         |
| `UPDATED`  | When existing Contents are modified (data changes and status changes) |
| `DELETED`  | When Contents are deleted                                             |

### Subscription Model

Webhooks subscribe to events on a **per-Table** basis. When creating a Webhook, select which event codes to receive for each Table.

For example, you can subscribe to only `CREATED` and `UPDATED` events from the `posts` table, while subscribing to all events from the `products` table.

> **[IMAGE AREA]** Webhook creation screen — table selection and event code selection UI

### When Events Are Not Fired

Webhook events are **not** sent for the following operations.

- Adding, updating, or deleting Columns
- Creating, updating, or deleting Tables
- Changes to Translations, Variables, Categories, or Files
- Dashboard configuration changes (API Keys, Webhook settings, etc.)

Webhook events are triggered only for **Contents** changes.

### Delivery Guarantees

| Item               | Description                                                                    |
| ------------------ | ------------------------------------------------------------------------------ |
| Delivery semantics | At-least-once. Duplicate deliveries may occur rarely                           |
| Ordering           | Not guaranteed. Rapid successive changes may arrive out of order               |
| Retry              | The system retries if the response is not `2xx`                                |
| Timeout            | If no response is received within a set time, the delivery is marked as failed |

To guard against duplicate events, use the event ID in the payload to ensure idempotency.

---

## Payload Structure

When a Webhook event fires, an HTTP POST request is sent to the registered URL with a JSON body.

### Payload Example

**`UPDATED` event when Contents status changes to `PUBLISHED`**

```json
{
  "id": "evt_01hxk2abc",
  "code": "UPDATED",
  "occurredAt": "2026-08-30T09:00:00.000Z",
  "workspaceId": "ws_01hxk2",
  "projectId": "proj_01hxk2",
  "table": {
    "id": "tbl_01hxk2",
    "name": "posts"
  },
  "contents": {
    "id": "cnt_01hxk2",
    "slug": "hello-onttle",
    "status": "PUBLISHED"
  }
}
```

### Field Reference

| Field             | Type                            | Description                                                  |
| ----------------- | ------------------------------- | ------------------------------------------------------------ |
| `id`              | `string`                        | Unique event identifier. Use to prevent duplicate processing |
| `code`            | `CREATED \| UPDATED \| DELETED` | Event type                                                   |
| `occurredAt`      | `string`                        | Time the event occurred (ISO 8601 UTC)                       |
| `workspaceId`     | `string`                        | Workspace identifier                                         |
| `projectId`       | `string`                        | Project identifier                                           |
| `table.id`        | `string`                        | Identifier of the Table where the event occurred             |
| `table.name`      | `string`                        | Name of the Table where the event occurred                   |
| `contents.id`     | `string`                        | Identifier of the changed Contents                           |
| `contents.slug`   | `string`                        | Slug of the changed Contents                                 |
| `contents.status` | `string`                        | Contents status at the time of the event                     |

The payload only includes the `id`, `slug`, and `status` of the changed Contents. A before/after diff is not included. If you need the actual changed data, fetch the Contents via the REST API after receiving the event.

```typescript
async function handleWebhook(payload: WebhookPayload) {
  const { projectId, table, contents } = payload

  const res = await fetch(
    `https://api.onttle.com/v1/${projectId}/tables/${table.id}/contents/${contents.id}`,
    { headers: { Authorization: `Bearer ${process.env.ONTTLE_API_KEY}` } },
  )

  if (!res.ok) return // Returns 404 for DELETED events
  const data = await res.json()
  // Update cache, trigger redeploy, etc.
}
```

### TypeScript Type Definitions

```typescript
type WebhookEventCode = 'CREATED' | 'UPDATED' | 'DELETED'

interface WebhookPayload {
  id: string
  code: WebhookEventCode
  occurredAt: string
  workspaceId: string
  projectId: string
  table: {
    id: string
    name: string
  }
  contents: {
    id: string
    slug: string
    status: string
  }
}
```

### Request Headers

| Header         | Value                |
| -------------- | -------------------- |
| `Content-Type` | `application/json`   |
| `User-Agent`   | `Onttle-Webhook/1.0` |

A signature header (`X-Onttle-Signature`) is planned for a future release. For now, security is managed by keeping the receiving URL private.

---

## Integration Examples

### Step 1: Register a Webhook

1. Click **Webhooks** in the left menu of your Project.
2. Click the **New Webhook** button.
3. Enter the **URL** of the endpoint that will receive events.
   - Example: `https://my-app.vercel.app/api/webhooks/onttle`
4. Select the **Table** and **event codes** you want to subscribe to.
5. Click **Create**.

> **[IMAGE AREA]** Webhook registration screen (URL input, table/event selection UI)

### Step 2: Send a Test Event

After saving the Webhook, click the **Send Test Event** button. A sample payload will be sent to the registered URL. You can check the result in the **Logs** tab of the Webhook list.

### Step 3: Receive Events in a Next.js Route Handler

```typescript
// app/api/webhooks/onttle/route.ts
import { NextRequest, NextResponse } from 'next/server'

// Simple cache to store processed event IDs (use Redis, etc. in production)
const processedEventIds = new Set<string>()

export async function POST(req: NextRequest) {
  const payload = (await req.json()) as WebhookPayload

  // 1. Prevent duplicate events
  if (processedEventIds.has(payload.id)) {
    return NextResponse.json({ ok: true, skipped: true })
  }
  processedEventIds.add(payload.id)

  // 2. Respond with 200 immediately to avoid timeout
  processEvent(payload).catch(console.error)

  return NextResponse.json({ ok: true })
}

async function processEvent(payload: WebhookPayload) {
  if (payload.code === 'CREATED' || payload.code === 'UPDATED') {
    await revalidateContent(payload)
  }
  if (payload.code === 'DELETED') {
    await removeFromCache(payload.contents.id)
  }
}
```

### Step 4: Trigger a Vercel Deployment

**Prerequisite:** Generate a Hook URL in your Vercel project settings → Git → Deploy Hooks.

```typescript
async function triggerVercelDeploy() {
  const deployHookUrl = process.env.VERCEL_DEPLOY_HOOK_URL
  if (!deployHookUrl) return
  await fetch(deployHookUrl, { method: 'POST' })
}

// Trigger only on publish events
async function processEvent(payload: WebhookPayload) {
  if (payload.code === 'UPDATED' && payload.contents.status === 'PUBLISHED') {
    await triggerVercelDeploy()
  }
}
```

### Step 5: Check Results in Logs

Go to **Webhooks** → **Logs** tab in your Project to view the response status and payload for sent events.

> **[IMAGE AREA]** Webhook Logs screen (sent time, response status, payload details)

---

## Best Practices

| Item             | Recommendation                                                       |
| ---------------- | -------------------------------------------------------------------- |
| Response speed   | Respond with `200` immediately and run follow-up work asynchronously |
| Deduplication    | Ensure idempotency using the event `id`                              |
| Failure handling | Log errors on follow-up task failures and retry                      |
| Security         | Do not expose the Webhook URL publicly                               |
