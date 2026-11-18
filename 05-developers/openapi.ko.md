---
title: 'OpenAPI 명세'
description: 'Onttle REST API의 OpenAPI 3.0 명세를 다운로드하고 활용하는 방법을 설명합니다.'
category: 'developers'
slug: 'openapi'
locale: 'ko'
apiVersion: 'v1'
order: 2
updatedAt: '2026-11-12'
---

# OpenAPI 명세

Onttle REST API는 OpenAPI 3.0 형식의 명세를 제공합니다. 명세를 사용하면 타입 안전한 클라이언트 코드를 자동 생성하고, Postman이나 Swagger UI로 API를 탐색할 수 있습니다.

---

## 명세 파일 다운로드

| 형식 | URL                                                                                         |
| ---- | ------------------------------------------------------------------------------------------- |
| JSON | `https://api.onttle.com/workspaces/{{workspaceId}}/projects/{{projectId}}/api/openapi.json` |

명세는 현재 배포된 API 버전을 기준으로 자동 생성됩니다. JSON URL은 API 엔드포인트 페이지에서 활성화를 한 후 접근이 가능합니다.

YAML 파일은 API 엔드포인트 페이지에서 직접 다운로드가 가능합니다.

---

## Bruno로 탐색

Bruno에서 명세를 가져와 API를 탐색할 수 있습니다. JSON URL을 바로 입력하거나, API 엔드포인트 페이지에서 다운로드한 YAML 파일을 가져와 사용할 수 있습니다.

1. Bruno → **Import** → **OpenAPI** 선택
2. **URL** 탭에서 `https://api.onttle.com/workspaces/{{workspaceId}}/projects/{{projectId}}/api/openapi.json`을 입력하거나, **File** 탭에서 다운로드한 YAML 파일을 선택
3. **Import** 클릭하여 Collection 생성
4. 생성된 Collection의 설정에서 API Key를 `Bearer Token`으로 등록

---

## Swagger UI로 탐색

브라우저에서 API를 탐색하려면 공식 대시보드의 **API 엔드포인트** 또는 별도 Swagger UI 인스턴스에 명세 URL을 입력합니다.

```
https://api.onttle.com/workspaces/{{workspaceId}}/projects/{{projectId}}/api/openapi.json
```

---

## Postman에서 사용하기

1. Postman → **Import** → **URL** 탭 선택
2. `https://api.onttle.com/workspaces/{{workspaceId}}/projects/{{projectId}}/api/openapi.json` 입력
3. **Continue** → **Import** 클릭
4. Collection이 생성되면 **Authorization** 탭에서 `Bearer Token`으로 API Key를 설정

---

## TypeScript 클라이언트 자동 생성

`openapi-typescript`를 사용해 명세에서 TypeScript 타입을 생성합니다.

```bash
npx openapi-typescript https://api.onttle.com/v1/openapi.json -o src/types/onttle-api.d.ts
```

생성된 타입을 `openapi-fetch`와 함께 사용하면 완전한 타입 안전 클라이언트를 구성할 수 있습니다.

```typescript
import createClient from 'openapi-fetch'
import type { paths } from './types/onttle-api'

const client = createClient<paths>({
  baseUrl: 'https://api.onttle.com/v1',
  headers: {
    Authorization: `Bearer ${process.env.ONTTLE_API_KEY}`,
  },
})

// 타입 안전 요청
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

## SDK에서의 OpenAPI 활용

`@openapitools/openapi-generator-cli`를 사용하면 JavaScript, Python, Go 등 다양한 언어의 SDK를 생성할 수 있습니다.

```bash
npx @openapitools/openapi-generator-cli generate \
  -i https://api.onttle.com/v1/openapi.json \
  -g typescript-fetch \
  -o ./sdk/onttle
```

---

## 회원 기능과 권한 규칙

Project에서 [회원 기능](/docs/core-features/members)을 켜면 명세에 다음 내용이 추가됩니다. 명세는 요청할 때마다 새로 만들어지므로, 인증 설정이나 Table 권한 규칙을 바꾸면 바로 반영됩니다.

| 항목            | 내용                                                                                          |
| --------------- | --------------------------------------------------------------------------------------------- |
| 보안 스킴       | `publishableKey`(`X-Onttle-Key` 헤더), `memberAuth`(회원 Bearer 토큰). 쿠키 모드를 켜면 `cookieAuth`도 추가됩니다 |
| 회원 엔드포인트 | `/auth/*`, `/me` — "Member API" 그룹                                                          |
| 스키마          | `Member`, `MemberSession`, `MemberConsent`, `MemberAuthError`(에러 코드 enum)                 |
| Table 권한 규칙 | 각 Table 경로의 `security`와 확장 필드 `x-onttle-rule`(`public`, `member`, `owner`, `role:<이름>`, `none`) |
| 파일 업로드 권한 | `POST /files`의 `security`와 `x-onttle-rule`(`none`, `member`, `role:<이름>`)                 |

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

회원 기능을 켜지 않은 Project의 명세는 이전과 같습니다.

---

## AI 코딩 에이전트와 MCP

Claude Code, Cursor, Codex 같은 AI 코딩 에이전트에는 OpenAPI 명세 URL(또는 YAML 파일)과 [에이전트 가이드](/get-started.md)를 전달해 연동 코드를 작성하게 할 수 있습니다. 시작 방법은 [빠른 시작](/docs/getting-started/quick-start)의 "AI 에이전트로 시작하기"를 참고하세요.

에이전트가 Project를 직접 조회하고 변경하게 하려면 [MCP 서버](/docs/ai/mcp)를 연결하세요. MCP 서버의 `onttle_get_openapi_spec` 도구와 `onttle://projects/{projectId}/openapi.json` 리소스로도 같은 명세를 받을 수 있습니다.

---

## 명세 변경 알림

API 명세 변경은 [변경 이력](/docs/changelog)에서 확인할 수 있습니다. 호환성을 깨는 변경이 포함된 경우 별도 마이그레이션 가이드가 함께 제공됩니다.

---

## 다음 단계

- [TypeScript](/docs/developers/typescript) — Onttle TypeScript 사용 방법을 알아봅니다.
- [엔드포인트 명세](/docs/rest-api/endpoints) — 리소스별 엔드포인트 목록을 확인합니다.
