---
title: 'MCP 서버'
description: 'Claude, Claude Code, Cursor 같은 AI 클라이언트를 Onttle MCP 서버에 연결해 Project를 조회하고 변경하는 방법을 설명합니다.'
category: 'ai'
slug: 'mcp'
locale: 'ko'
apiVersion: 'v1'
order: 8
updatedAt: '2026-11-13'
---

# MCP 서버

Onttle MCP 서버는 AI 클라이언트가 Onttle Project를 직접 다룰 수 있게 해 주는 [Model Context Protocol](https://modelcontextprotocol.io) 서버입니다. 연결하면 AI가 테이블 구조와 콘텐츠를 읽고, 사용자의 확인을 받은 뒤 콘텐츠·테이블·설정을 변경할 수 있습니다.

```
https://api.onttle.com/mcp
```

전송 방식은 Streamable HTTP이며, 별도로 설치할 프로그램은 없습니다.

---

## 연결 방식

두 가지 자격 증명 중 하나로 연결합니다.

| 방식                         | 접근 범위                                                    | 적합한 경우                                |
| ---------------------------- | ------------------------------------------------------------ | ------------------------------------------ |
| **Onttle 계정 로그인**       | 계정이 속한 모든 Project. Project마다 본인 역할이 적용됩니다 | Claude 앱, 여러 Project를 오가는 작업      |
| **게시용 키 (`pk-proj_…`)**  | 키가 속한 Project 하나. 공개 API가 제공하는 데이터만 읽기    | 스키마와 발행된 콘텐츠만 읽으면 되는 경우  |
| **시크릿 키 (`sk-proj_…`)**  | 키가 속한 Project 하나. 초안·파일·로그 읽기와 콘텐츠 쓰기    | CI, 서버에서 실행하는 에이전트             |

테이블 생성·수정, 권한 규칙, API Key 발급, 회원 관리는 계정 로그인으로만 할 수 있습니다. API Key는 누가 변경했는지를 [Audit 로그](/docs/developers/audit-log)에 남길 수 없기 때문입니다.

---

## 연결하기

### Claude Code

**계정 로그인**

```bash
claude mcp add --transport http onttle https://api.onttle.com/mcp
```

Claude Code를 실행하고 `/mcp`를 입력한 뒤 `onttle` → **Authenticate**를 선택합니다. 브라우저에서 Onttle에 로그인하고 동의 화면에서 **승인**을 누르면 연결됩니다.

**API Key**

```bash
claude mcp add --transport http onttle https://api.onttle.com/mcp --header "X-Onttle-Key: $ONTTLE_API_KEY"
```

### Claude 커넥터 (웹·데스크톱·모바일)

Claude 앱에서는 Onttle을 **커스텀 커넥터**로 추가합니다. 한 번 추가하면 같은 계정의 웹, 데스크톱, 모바일 앱에서 모두 쓸 수 있습니다. 커스텀 커넥터는 Claude 유료 플랜에서 제공됩니다.

**1. 커넥터 추가**

1. Claude에서 **설정 > 커넥터**를 엽니다.
2. **커스텀 커넥터 추가**를 누릅니다.
3. 이름에 `Onttle`, 원격 MCP 서버 URL에 아래 주소를 입력하고 **추가**를 누릅니다.

```
https://api.onttle.com/mcp
```

Team·Enterprise 플랜에서는 조직 소유자가 **조직 설정 > 커넥터**에서 먼저 추가해야 합니다. 그 뒤 구성원이 각자 자신의 설정에서 연결합니다.

**2. Onttle 계정 연결**

1. 커넥터 목록의 Onttle에서 **연결**을 누릅니다.
2. 브라우저에 Onttle 로그인 화면이 열리면 로그인합니다.
3. 동의 화면에서 요청한 앱 이름이 Claude인지 확인하고 **승인**을 누릅니다.

연결은 로그인한 Onttle 계정의 권한을 그대로 따릅니다. 구성원마다 자기 계정으로 연결하므로, 같은 조직이라도 각자 접근할 수 있는 Project만 보입니다.

**3. 대화에서 사용**

1. 새 대화의 입력창에서 **검색 및 도구** 메뉴를 열고 Onttle을 켭니다.
2. "내 Onttle 프로젝트 목록을 보여 줘"처럼 요청합니다. Claude가 `onttle_list_workspaces`와 `onttle_list_projects`를 호출합니다.
3. 도구를 처음 호출할 때 Claude가 허용 여부를 묻습니다. 읽기 도구는 **항상 허용**으로 두어도 되고, 쓰기 도구는 매번 확인하도록 두는 것을 권합니다.

쓰기 도구는 Claude의 확인과 별개로 [두 번 호출해야 적용](#변경은-두-번-호출해야-적용됩니다)됩니다. Claude가 보여 주는 미리보기를 확인한 뒤 진행하라고 답하면 됩니다.

**알아 둘 점**

- Claude 앱은 계정 로그인으로만 연결됩니다. API Key 헤더는 설정할 수 없습니다.
- Workspace에 IP 접근 규칙이 있으면 [Anthropic의 발신 대역을 허용](#ip-접근-규칙을-쓰는-workspace)해야 합니다.
- 연결을 끊으려면 **설정 > 커넥터**에서 Onttle의 연결을 해제하거나 커넥터를 제거합니다.

### 그 밖의 클라이언트

원격 MCP 서버(Streamable HTTP)를 지원하는 클라이언트라면 연결할 수 있습니다. 설정 파일을 쓰는 클라이언트(예: Cursor)는 다음과 같이 등록합니다.

```json
{
  "mcpServers": {
    "onttle": {
      "url": "https://api.onttle.com/mcp",
      "headers": {
        "X-Onttle-Key": "sk-proj_..."
      }
    }
  }
}
```

`headers`를 빼면 OAuth를 지원하는 클라이언트는 계정 로그인 흐름으로 연결합니다. API Key는 `X-Onttle-Key` 헤더 대신 `Authorization: Bearer <API Key>`로 보내도 됩니다.

---

## 권한

도구 목록은 모든 연결에 똑같이 보이고, 권한은 호출할 때마다 확인합니다.

- **계정 로그인:** Project 도구를 호출할 때 `project_id`로 대상을 지정합니다. 그 Project에서의 본인 역할(`OWNER`, `ADMIN`, `EDITOR`, `GUEST`)이 도구의 최소 역할보다 낮으면 거부됩니다. 역할별 권한은 [멤버 권한](/docs/developers/member-roles)을 참고하세요.
- **게시용 키:** `GUEST` 수준으로 동작하며, 발행된 콘텐츠처럼 공개 API가 제공하는 데이터만 반환합니다.
- **시크릿 키:** `EDITOR` 수준으로 동작합니다. `project_id`를 지정할 필요가 없습니다.

API Key의 만료, 허용 호스트, Workspace IP 접근 규칙은 REST API와 똑같이 적용됩니다.

---

## 변경은 두 번 호출해야 적용됩니다

데이터를 변경하는 도구는 한 번의 호출로 실행되지 않습니다.

1. 처음 호출하면 아무것도 바꾸지 않고, 바뀔 내용의 **미리보기**와 `confirm_token`을 돌려줍니다.
2. AI가 미리보기를 사용자에게 보여 줍니다.
3. 사용자가 동의하면 **같은 인자**에 `confirm_token`을 더해 다시 호출합니다. 이때 변경이 적용됩니다.

`confirm_token`은 10분 동안 유효하고, 발급받은 연결·도구·Project·인자에 묶여 있습니다. 인자를 하나라도 바꾸면 토큰이 맞지 않아 거부되므로, 미리보기에서 본 내용과 다른 변경이 적용될 수 없습니다.

---

## 도구

도구는 읽기 23개, 쓰기 20개입니다. **최소 역할**은 계정 로그인일 때 필요한 Project 역할이고, **API Key**는 그 도구를 호출할 수 있는 키 유형입니다. `-`는 계정 로그인으로만 호출할 수 있다는 뜻입니다.

### Workspace와 Project

| 도구                      | 종류 | 설명                                 | 최소 역할 | API Key |
| ------------------------- | ---- | ------------------------------------ | --------- | ------- |
| `onttle_list_workspaces`  | 읽기 | 계정이 속한 Workspace 목록           | -         | -       |
| `onttle_list_projects`    | 읽기 | Workspace의 Project 목록             | -         | -       |
| `onttle_get_project`      | 읽기 | Project 정보                         | GUEST     | 게시용  |
| `onttle_get_schema`       | 읽기 | 테이블과 Column 구조                 | GUEST     | 게시용  |
| `onttle_get_openapi_spec` | 읽기 | Project의 OpenAPI 명세               | GUEST     | 게시용  |
| `onttle_create_table`     | 쓰기 | 테이블 생성                          | ADMIN     | -       |
| `onttle_update_table`     | 쓰기 | 테이블과 Column 수정                 | ADMIN     | -       |
| `onttle_set_table_rules`  | 쓰기 | 테이블 권한 규칙 설정                | ADMIN     | -       |

### 콘텐츠

| 도구                          | 종류 | 설명                         | 최소 역할 | API Key |
| ----------------------------- | ---- | ---------------------------- | --------- | ------- |
| `onttle_search_contents`      | 읽기 | 콘텐츠 검색·필터·정렬        | GUEST     | 게시용  |
| `onttle_get_contents`         | 읽기 | 콘텐츠 한 건 조회            | GUEST     | 게시용  |
| `onttle_create_contents`      | 쓰기 | 콘텐츠 생성                  | EDITOR    | 시크릿  |
| `onttle_update_contents`      | 쓰기 | 콘텐츠 수정                  | EDITOR    | 시크릿  |
| `onttle_set_contents_status`  | 쓰기 | 발행 / 발행 취소             | EDITOR    | 시크릿  |
| `onttle_delete_contents`      | 쓰기 | 콘텐츠 삭제                  | EDITOR    | 시크릿  |
| `onttle_get_contents_history` | 읽기 | 버전 기록 조회               | EDITOR    | 시크릿  |
| `onttle_rollback_contents`    | 쓰기 | 이전 버전으로 되돌리기       | EDITOR    | 시크릿  |

게시용 키로 콘텐츠를 조회하면 발행된 콘텐츠만 반환합니다.

- **값의 키:** 조회 결과의 `data`와 쓰기 도구의 `data`는 모두 Column 이름을 키로 씁니다. 읽은 값을 그대로 쓰기에 넣을 수 있습니다.
- **EDITOR Column:** **Markdown 문자열** 또는 **TipTap 문서**(JSON 객체나 JSON 문자열)를 보냅니다. 대시보드와 REST API도 같은 형식을 받습니다. 다국어 Column은 `{ "ko": 값, "en": 값 }`처럼 언어별로 보냅니다. 조회 결과에는 본문이 일반 텍스트로 나옵니다.
- **미리보기 검증:** 생성·수정 미리보기가 필수값, 길이·범위 같은 Column 제약을 미리 검사해 `invalid_rows`로 알려 줍니다. 모든 행이 실패하면 `confirm_token`을 발급하지 않습니다.
- **수정과 롤백:** 대시보드와 같은 로직으로 동작합니다. 수정한 행은 `publish: true`를 주지 않으면 DRAFT로 저장되고, 롤백한 행은 되돌린 버전의 값과 상태를 그대로 가져옵니다. 발행된 행을 초안이던 버전으로 되돌리면 상태가 DRAFT가 되며, 미리보기에 롤백 후 상태가 표시됩니다.

### 리소스

| 도구                                                     | 종류 | 설명                 | 최소 역할 | API Key |
| -------------------------------------------------------- | ---- | -------------------- | --------- | ------- |
| `onttle_list_categories`                                 | 읽기 | 카테고리 목록        | GUEST     | 시크릿  |
| `onttle_create_categories`, `onttle_update_categories`   | 쓰기 | 카테고리 생성·수정   | EDITOR    | 시크릿  |
| `onttle_list_translations`                               | 읽기 | 번역 목록            | GUEST     | 시크릿  |
| `onttle_create_translations`, `onttle_update_translations` | 쓰기 | 번역 생성·수정     | EDITOR    | 시크릿  |
| `onttle_list_variables`                                  | 읽기 | 변수 목록            | GUEST     | 시크릿  |
| `onttle_create_variables`, `onttle_update_variables`     | 쓰기 | 변수 생성·수정       | EDITOR    | 시크릿  |
| `onttle_search_files`                                    | 읽기 | 업로드한 파일 검색   | GUEST     | 시크릿  |
| `onttle_list_webhooks`                                   | 읽기 | Webhook 목록         | GUEST     | 시크릿  |
| `onttle_create_webhook`                                  | 쓰기 | Webhook 생성         | EDITOR    | 시크릿  |
| `onttle_get_webhook_logs`                                | 읽기 | Webhook 발송 기록    | EDITOR    | 시크릿  |

### 회원

| 도구                            | 종류 | 설명                         | 최소 역할 | API Key |
| ------------------------------- | ---- | ---------------------------- | --------- | ------- |
| `onttle_get_member_settings`    | 읽기 | 회원 기능 설정 조회          | GUEST     | 시크릿  |
| `onttle_update_member_settings` | 쓰기 | 회원 기능 설정 변경          | ADMIN     | -       |
| `onttle_get_member_stats`       | 읽기 | 가입·활성 회원 통계          | EDITOR    | -       |
| `onttle_list_members`           | 읽기 | 회원 목록                    | EDITOR    | -       |
| `onttle_update_member`          | 쓰기 | 회원 정보·상태 수정          | EDITOR    | -       |
| `onttle_create_member`          | 쓰기 | 회원 직접 추가               | ADMIN     | -       |

`onttle_create_member`는 가입 1건으로 과금되고 월간 가입 한도에 포함됩니다. 회원 기능은 [회원](/docs/core-features/members) 문서를 참고하세요.

### API와 운영

| 도구                         | 종류 | 설명                                   | 최소 역할 | API Key |
| ---------------------------- | ---- | -------------------------------------- | --------- | ------- |
| `onttle_list_api_keys`       | 읽기 | API Key 목록 (전체 값은 표시하지 않음) | ADMIN     | -       |
| `onttle_create_api_key`      | 쓰기 | 게시용 API Key 발급                    | ADMIN     | -       |
| `onttle_get_api_settings`    | 읽기 | API 엔드포인트 설정 조회               | GUEST     | 시크릿  |
| `onttle_set_api_settings`    | 쓰기 | API 엔드포인트 설정 변경               | ADMIN     | -       |
| `onttle_analyze_api_logs`    | 읽기 | API 요청 로그 분석                     | EDITOR    | 시크릿  |
| `onttle_get_billing_summary` | 읽기 | 플랜과 사용량 요약                     | EDITOR    | -       |

`onttle_create_api_key`는 게시용 키만 발급합니다. 전체 키 값은 확인된 호출의 결과에 한 번만 표시됩니다. 시크릿 키는 대시보드에서만 발급할 수 있습니다.

### 문서

| 도구                       | 종류 | 설명                              | 최소 역할 | API Key |
| -------------------------- | ---- | --------------------------------- | --------- | ------- |
| `onttle_search_docs`       | 읽기 | Onttle 공식 문서와 FAQ 검색       | -         | 게시용  |
| `onttle_get_api_reference` | 읽기 | Project에 맞춘 REST API 레퍼런스  | GUEST     | 게시용  |

`onttle_search_docs`는 연결만 되어 있으면 역할과 관계없이 호출할 수 있습니다.

---

## 프롬프트와 리소스

프롬프트를 지원하는 클라이언트에서는 다음 작업을 바로 시작할 수 있습니다.

| 프롬프트          | 용도                                              |
| ----------------- | ------------------------------------------------- |
| `onttle-setup`    | 현재 코드베이스에 Onttle 연동 코드를 추가합니다   |
| `onttle-add-auth` | 회원 가입·로그인을 추가합니다                     |
| `onttle-seed`     | 로컬 데이터를 초안 콘텐츠로 가져옵니다            |
| `onttle-debug`    | Onttle API 연동 문제의 원인을 찾습니다            |

리소스로는 다음 세 가지를 제공합니다.

| 리소스 URI                                        | 내용                         |
| ------------------------------------------------- | ---------------------------- |
| `onttle://docs/get-started`                       | AI 에이전트용 시작 가이드    |
| `onttle://projects/{projectId}/schema`            | Project의 테이블·Column 구조 |
| `onttle://projects/{projectId}/openapi.json`      | Project의 OpenAPI 명세       |

---

## 사용량과 제한

- **API Key 연결:** 도구 호출 한 번이 API 읽기 1건으로, 확인된 쓰기 한 번이 API 쓰기 1건으로 기록됩니다. 사용량 화면에는 `/mcp/tools/<도구 이름>` 경로로 표시됩니다.
- **계정 연결:** 과금하지 않습니다. 호출은 Project의 **API 로그**에 `/mcp/tools/<도구 이름>` 경로로 기록되고, API 키 열에는 "계정 연결 (MCP)"로 표시됩니다. Workspace·Project 목록 조회와 문서 검색처럼 특정 Project에 속하지 않는 호출은 기록하지 않습니다. 관리 도구로 한 변경은 Audit 로그에도 남습니다.
- **요청 한도:** 플랜의 API 요청 한도가 그대로 적용됩니다. 확인된 쓰기는 연결당 분당 30회로 제한됩니다.
- **결과 크기:** 도구 결과가 48,000자를 넘으면 `result_too_large` 오류와 함께 범위를 좁히는 방법을 안내합니다. 필터를 추가하거나 페이지 크기를 줄여 다시 호출하세요.

---

## IP 접근 규칙을 쓰는 Workspace

Claude 앱(웹·데스크톱·모바일)의 요청은 사용자의 컴퓨터가 아니라 Anthropic의 서버에서 나옵니다. Workspace에 IP 접근 규칙이 있다면 Anthropic의 발신 대역을 허용 목록에 추가해야 Claude 앱에서 연결할 수 있습니다.

```
160.79.104.0/21
```

Claude Code처럼 사용자의 컴퓨터에서 실행되는 클라이언트는 그 컴퓨터의 IP로 요청합니다.

허용 호스트를 도메인으로만 제한한 API Key는 MCP에서 쓸 수 없습니다. MCP 클라이언트는 `Origin` 헤더를 보내지 않기 때문입니다. 허용 호스트가 없는 키나 IP를 허용한 키를 쓰거나, 계정 로그인으로 연결하세요.

---

## 기록

MCP로 실행한 변경은 대시보드에서 한 변경과 똑같이 [Audit 로그](/docs/developers/audit-log)에 남습니다. 계정 연결로 실행한 관리 작업은 `mcp.tool_called` 이벤트로 기록되고, 변경 행에는 경로가 `mcp`로 표시됩니다.

---

## 연결 해제

- **Claude Code:** `claude mcp remove onttle`
- **Claude 앱:** **설정 > 커넥터**에서 Onttle 커넥터를 제거합니다.
- **API Key:** 대시보드의 **API Keys**에서 키를 삭제하면 그 키를 쓰는 모든 연결이 즉시 끊깁니다.

---

## 자주 만나는 오류

| 오류                                      | 원인                                              | 해결                                                         |
| ----------------------------------------- | ------------------------------------------------- | ------------------------------------------------------------ |
| `401 Unauthorized`                        | 자격 증명이 없거나 만료됨                         | 클라이언트에서 다시 인증하거나 API Key를 확인합니다          |
| `forbidden`                               | 역할이 부족하거나 그 키 유형으로 호출할 수 없음   | 도구 표의 최소 역할과 API Key 열을 확인합니다                |
| `confirm_token_expired`                   | 미리보기 후 10분이 지남                           | `confirm_token` 없이 다시 호출해 새 미리보기를 받습니다      |
| `confirm_token_invalid`                   | 미리보기와 인자가 다름                            | 같은 인자로 다시 호출하거나 새 미리보기를 받습니다           |
| `result_too_large`                        | 결과가 48,000자를 넘음                            | 필터를 추가하거나 페이지 크기를 줄입니다                     |
| IP가 허용되지 않았다는 메시지             | Workspace IP 접근 규칙                            | 요청 IP 또는 `160.79.104.0/21`을 허용 목록에 추가합니다      |
| `429 Too Many Requests`                   | 요청 한도 초과                                    | `Retry-After` 헤더의 시간만큼 기다린 뒤 다시 시도합니다      |

---

## 다음 단계

- [인증](/docs/rest-api/authentication) — API Key를 발급하고 허용 호스트와 만료를 설정합니다.
- [멤버 권한](/docs/developers/member-roles) — 역할별로 할 수 있는 작업을 확인합니다.
- [Ask AI (Assistant)](/docs/ai/ask-ai) — 대시보드 안에서 같은 도구를 쓰는 AI 어시스턴트입니다.
