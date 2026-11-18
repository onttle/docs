---
title: 'OpenAPI Specification'
description: 'How to download and use the OpenAPI 3.0 specification for the Onttle REST API.'
category: 'developers'
slug: 'openapi'
locale: 'en'
apiVersion: 'v1'
order: 2
updatedAt: '2026-11-12'
---

# OpenAPI Specification

The Onttle REST API provides a specification in OpenAPI 3.0 format. You can use the spec to auto-generate type-safe client code and explore the API with Postman or Swagger UI.

---

## Download the Spec

| Format | URL                                                                                         |
| ------ | ------------------------------------------------------------------------------------------- |
| JSON   | `https://api.onttle.com/workspaces/{{workspaceId}}/projects/{{projectId}}/api/openapi.json` |

The spec is auto-generated based on the currently deployed API version. The JSON URL becomes accessible after it is activated on the API Endpoints page.

The YAML file can be downloaded directly from the API Endpoints page.

---

## Explore with Bruno

You can also import the spec into Bruno to explore the API. Enter the JSON URL directly, or import the YAML file downloaded from the API Endpoints page.

1. Bruno → **Import** → Select **OpenAPI**
2. In the **URL** tab, enter `https://api.onttle.com/workspaces/{{workspaceId}}/projects/{{projectId}}/api/openapi.json`, or in the **File** tab, select the downloaded YAML file
3. Click **Import** to create the Collection
4. In the created Collection's settings, register the API Key as a `Bearer Token`

> **[IMAGE AREA]** Exploring the Onttle API in Bruno

---

## Explore with Swagger UI

To explore the API in a browser, enter the spec URL in the **API Endpoints** page on the official dashboard or in a separate Swagger UI instance.

```
https://api.onttle.com/workspaces/{{workspaceId}}/projects/{{projectId}}/api/openapi.json
```

> **[IMAGE AREA]** Exploring the Onttle API in Swagger UI

---

## Using with Postman

1. Postman → **Import** → Select the **URL** tab
2. Enter `https://api.onttle.com/workspaces/{{workspaceId}}/projects/{{projectId}}/api/openapi.json`
3. Click **Continue** → **Import**
4. Once the Collection is created, set the API Key as a `Bearer Token` in the **Authorization** tab

---

## Auto-generate TypeScript Client

Use `openapi-typescript` to generate TypeScript types from the spec.

```bash
npx openapi-typescript https://api.onttle.com/v1/openapi.json -o src/types/onttle-api.d.ts
```

Combine the generated types with `openapi-fetch` to build a fully type-safe client.

```typescript
import createClient from 'openapi-fetch'
import type { paths } from './types/onttle-api'

const client = createClient<paths>({
  baseUrl: 'https://api.onttle.com/v1',
  headers: {
    Authorization: `Bearer ${process.env.ONTTLE_API_KEY}`,
  },
})

// Type-safe request
const { data, error } = await client.GET(
  '/{projectId}/tables/{tableId}/contents',
  {
    params: {
      path: { projectId: 'proj_01hxk2', tableId: 'tbl_01hxk2' },
      query: { status: 'PUBLISHED' },
    },
  },
)
```

---

## Generating SDKs with OpenAPI

Use `@openapitools/openapi-generator-cli` to generate SDKs for various languages including JavaScript, Python, and Go.

```bash
npx @openapitools/openapi-generator-cli generate \
  -i https://api.onttle.com/v1/openapi.json \
  -g typescript-fetch \
  -o ./sdk/onttle
```

---

## Members and Permission Rules

When the [member feature](/docs/core-features/members) is on for a Project, the spec gains the items below. The spec is built on every request, so a change to the auth settings or to a Table's permission rules shows up immediately.

| Item                   | Details                                                                                                  |
| ---------------------- | -------------------------------------------------------------------------------------------------------- |
| Security schemes       | `publishableKey` (`X-Onttle-Key` header), `memberAuth` (member Bearer token). Cookie mode adds `cookieAuth` |
| Member endpoints       | `/auth/*`, `/me` — the "Member API" group                                                                |
| Schemas                | `Member`, `MemberSession`, `MemberConsent`, `MemberAuthError` (error code enum)                          |
| Table permission rules | `security` and the extension field `x-onttle-rule` (`public`, `member`, `owner`, `role:<name>`, `none`) on each Table path |
| File upload permission | `security` and `x-onttle-rule` (`none`, `member`, `role:<name>`) on `POST /files`                        |

```yaml
'/tables/posts':
  post:
    summary: 'Create Posts'
    security:
      - publishableKey: []
        memberAuth: []
      - bearerAuth: []
    x-onttle-rule: 'member'
```

The spec of a Project without the member feature is unchanged.

---

## AI Coding Agents and MCP

You can hand the OpenAPI spec URL (or the YAML file) and the [agent guide](/get-started.md) to an AI coding agent such as Claude Code, Cursor or Codex and have it write the integration code. See "Set Up with an AI Agent" in the [Quick Start](/docs/getting-started/quick-start) to begin.

To let the agent read and change the project directly, connect the [MCP server](/docs/ai/mcp). Its `onttle_get_openapi_spec` tool and the `onttle://projects/{projectId}/openapi.json` resource return the same specification.

---

## Spec Change Notifications

API spec changes are documented in the [Changelog](/docs/changelog). Migration guides are provided alongside any breaking changes.

---

## Next Steps

- [TypeScript](/docs/developers/typescript) — Learn how to use the Onttle TypeScript.
- [Endpoints Reference](/docs/rest-api/endpoints) — View the endpoint list by resource.
