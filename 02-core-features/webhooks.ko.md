---
title: 'Webhook'
description: 'Onttle 데이터 변경 이벤트를 외부 시스템에 실시간으로 전달하는 Webhook의 이벤트 종류, 페이로드 구조, 연동 예시를 설명합니다.'
category: 'core-features'
slug: 'webhooks'
locale: 'ko'
apiVersion: 'v1'
order: 5
updatedAt: '2026-08-30'
---

# Webhook

Webhook은 Onttle의 데이터 변경 이벤트를 외부 시스템에 실시간으로 전달합니다. 등록된 URL로 HTTP POST 요청을 발송합니다.

---

## 이벤트 종류

Onttle은 Contents의 생명주기에 따라 세 가지 이벤트를 지원합니다.

| 이벤트 코드 | 발생 시점                                               |
| ----------- | ------------------------------------------------------- |
| `CREATED`   | 새 Contents가 생성될 때                                 |
| `UPDATED`   | 기존 Contents가 수정될 때 (데이터 변경, 상태 변경 포함) |
| `DELETED`   | Contents가 삭제될 때                                    |

### 이벤트 구독 방식

Webhook은 **Table 단위**로 이벤트를 구독합니다. Webhook 생성 시 각 Table에서 수신할 이벤트 코드를 선택합니다.

예를 들어 `posts` 테이블의 `CREATED`와 `UPDATED` 이벤트만 구독하고, `products` 테이블의 모든 이벤트를 구독하는 것이 가능합니다.

### 이벤트가 발생하지 않는 경우

다음 작업에서는 Webhook 이벤트가 발송되지 않습니다.

- Column 추가, 수정, 삭제
- Table 생성, 수정, 삭제
- Translation, Variable, Category, File 변경
- 대시보드 설정 변경 (API Key, Webhook 설정 등)

Webhook 이벤트는 **Contents** 변경에만 발생합니다.

### 전달 보장 수준

| 항목      | 설명                                                           |
| --------- | -------------------------------------------------------------- |
| 전달 방식 | 최소 1회 전달 (At-least-once). 드물게 중복 전달될 수 있습니다  |
| 전달 순서 | 보장하지 않습니다. 빠른 연속 변경 시 순서가 달라질 수 있습니다 |
| 재시도    | 응답이 `2xx`가 아닌 경우 재시도합니다                          |
| 타임아웃  | 응답이 일정 시간 내에 오지 않으면 실패로 처리합니다            |

중복 이벤트를 방지하려면 페이로드의 이벤트 ID를 사용해 멱등성을 보장하세요.

---

## 페이로드 구조

Webhook 이벤트가 발생하면 등록된 URL로 HTTP POST 요청을 발송합니다. 요청 본문(body)은 JSON 형식입니다.

### 페이로드 예시

**Contents가 발행(`PUBLISHED`)으로 상태 변경될 때의 `UPDATED` 이벤트**

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

### 필드 설명

| 필드              | 타입                            | 설명                                        |
| ----------------- | ------------------------------- | ------------------------------------------- |
| `id`              | `string`                        | 이벤트의 고유 식별자. 중복 수신 방지에 사용 |
| `code`            | `CREATED \| UPDATED \| DELETED` | 이벤트 종류                                 |
| `occurredAt`      | `string`                        | 이벤트 발생 시각 (ISO 8601 UTC)             |
| `workspaceId`     | `string`                        | Workspace 식별자                            |
| `projectId`       | `string`                        | Project 식별자                              |
| `table.id`        | `string`                        | 이벤트가 발생한 Table의 식별자              |
| `table.name`      | `string`                        | 이벤트가 발생한 Table의 이름                |
| `contents.id`     | `string`                        | 변경된 Contents의 식별자                    |
| `contents.slug`   | `string`                        | 변경된 Contents의 slug                      |
| `contents.status` | `string`                        | 이벤트 발생 시점의 Contents 상태            |

페이로드에는 변경된 Contents의 `id`, `slug`, `status`만 포함됩니다. 변경 전후 데이터(diff)는 포함되지 않습니다. 실제 변경된 데이터가 필요하면 이벤트 수신 후 REST API로 해당 Contents를 직접 조회합니다.

```typescript
async function handleWebhook(payload: WebhookPayload) {
  const { projectId, table, contents } = payload

  const res = await fetch(
    `https://api.onttle.com/v1/${projectId}/tables/${table.id}/contents/${contents.id}`,
    { headers: { Authorization: `Bearer ${process.env.ONTTLE_API_KEY}` } },
  )

  if (!res.ok) return // DELETED 이벤트의 경우 404 반환
  const data = await res.json()
  // 변경된 데이터로 캐시 갱신, 재배포 트리거 등
}
```

### TypeScript 타입 정의

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

### 요청 헤더

| 헤더           | 값                   |
| -------------- | -------------------- |
| `Content-Type` | `application/json`   |
| `User-Agent`   | `Onttle-Webhook/1.0` |

서명 헤더(`X-Onttle-Signature`)는 향후 지원 예정입니다. 현재는 수신 URL 자체의 비공개 유지로 보안을 관리합니다.

---

## 연동 예시

### 1단계: Webhook 등록

1. Project 좌측 메뉴에서 **Webhooks**를 클릭합니다.
2. **새 Webhook** 버튼을 클릭합니다.
3. **URL**에 이벤트를 수신할 엔드포인트를 입력합니다.
   - 예: `https://my-app.vercel.app/api/webhooks/onttle`
4. 이벤트를 수신할 **Table**과 **이벤트 코드**를 선택합니다.
5. **생성**을 클릭합니다.

### 2단계: 테스트 이벤트 발송

Webhook을 저장한 후 **테스트 이벤트 전송** 버튼을 클릭합니다. 등록된 URL로 샘플 페이로드가 전송됩니다. 결과는 Webhook 목록의 **Logs** 탭에서 확인할 수 있습니다.

### 3단계: Next.js Route Handler로 이벤트 수신

```typescript
// app/api/webhooks/onttle/route.ts
import { NextRequest, NextResponse } from 'next/server'

// 처리한 이벤트 ID를 저장하는 간단한 캐시 (프로덕션에서는 Redis 등 사용 권장)
const processedEventIds = new Set<string>()

export async function POST(req: NextRequest) {
  const payload = (await req.json()) as WebhookPayload

  // 1. 중복 이벤트 방지
  if (processedEventIds.has(payload.id)) {
    return NextResponse.json({ ok: true, skipped: true })
  }
  processedEventIds.add(payload.id)

  // 2. 즉시 200 응답 (타임아웃 방지)
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

### 4단계: Vercel 배포 트리거

**사전 조건:** Vercel 프로젝트 설정 → Git → Deploy Hooks에서 Hook URL을 생성합니다.

```typescript
async function triggerVercelDeploy() {
  const deployHookUrl = process.env.VERCEL_DEPLOY_HOOK_URL
  if (!deployHookUrl) return
  await fetch(deployHookUrl, { method: 'POST' })
}

// processEvent에서 발행 이벤트에만 트리거
async function processEvent(payload: WebhookPayload) {
  if (payload.code === 'UPDATED' && payload.contents.status === 'PUBLISHED') {
    await triggerVercelDeploy()
  }
}
```

### 5단계: Logs에서 결과 확인

Project의 **Webhooks** → **Logs** 탭에서 발송된 이벤트의 응답 상태와 페이로드를 확인합니다.

---

## 모범 사례

| 항목      | 권장 방법                                         |
| --------- | ------------------------------------------------- |
| 응답 속도 | 즉시 `200`으로 응답하고 후속 작업은 비동기로 실행 |
| 중복 방지 | 이벤트 `id`를 기반으로 멱등성 보장                |
| 실패 처리 | 후속 작업 실패 시 로그 기록 후 재시도             |
| 보안      | Webhook URL을 외부에 노출하지 않음                |
