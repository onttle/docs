---
title: '빠른 시작'
description: '워크스페이스 생성부터 첫 번째 API 호출까지 단계별로 따라합니다.'
category: 'getting-started'
slug: 'quick-start'
locale: 'ko'
apiVersion: 'v1'
order: 2
popular: true
updatedAt: '2026-11-12'
---

# 빠른 시작

이 가이드를 완료하면 Onttle에 콘텐츠를 등록하고 REST API로 조회하는 전체 흐름을 경험할 수 있습니다. 약 10분이 소요됩니다.

**사전 조건:** Onttle 계정이 필요합니다. 없다면 [회원가입](/signup)을 먼저 완료하세요.

---

## AI 에이전트로 시작하기

Claude Code, Cursor, Codex 같은 AI 코딩 에이전트를 사용한다면 이 페이지 상단(또는 대시보드 헤더)의 **Onboard Your Agent** 버튼으로 아래 프롬프트를 복사해 에이전트에 붙여 넣으세요.

```text
Set up Onttle for me. Fetch https://onttle.com/get-started.md and follow it
```

에이전트가 [에이전트 가이드](/get-started.md)를 읽고 코드베이스에 맞는 테이블 구조를 제안하고, API 클라이언트를 작성하고, 대시보드에서 직접 해야 할 작업을 알려 줍니다. 직접 진행하려면 아래 단계를 따르세요.

> **MCP 서버로도 연결할 수 있습니다.** `https://api.onttle.com/mcp`를 에이전트에 등록하면 에이전트가 Project의 테이블 구조와 콘텐츠를 직접 읽고, 확인을 받은 뒤 변경합니다. 연결 방법은 [MCP 서버](/docs/ai/mcp)를 참고하세요.

---

## 1단계: Workspace 생성

Workspace는 멤버, 결제, 사용량을 관리하는 최상위 단위입니다.

1. 대시보드에 로그인합니다.
2. 좌측 상단의 **Workspace 선택기** → **새 Workspace 만들기**를 클릭합니다.
3. Workspace 이름을 입력하고 **생성**을 클릭합니다.

---

## 2단계: Project 생성

Project는 API Key와 테이블 데이터를 분리하는 애플리케이션 단위입니다.

1. Workspace 홈에서 **새 Project** 버튼을 클릭합니다.
2. Project 이름(예: `my-blog`)을 입력합니다.
3. 초대할 멤버와 언어를 선택합니다.
4. 오른쪽 영역에서 AI를 이용해 프로젝트의 테이블 구조를 생성할 수 있습니다. 직접 만들려면 '직접 만들기'를 선택합니다.
5. **프로젝트 만들기**를 클릭합니다.

---

## 3단계: Table 생성 및 Column 추가

Table은 콘텐츠 스키마를 정의하는 단위입니다. Table 생성과 Column 추가는 하나의 Table 생성 화면에서 함께 진행됩니다. 이 예제에서는 블로그 게시물 테이블을 만듭니다.

1. Project 홈에서 **새 Table** 버튼을 클릭합니다.
2. Table 이름을 `posts`로 입력합니다.
3. **컬럼 추가** 버튼을 클릭하고 아래 컬럼을 순서대로 추가합니다.

| 컬럼 이름        | 타입   | 옵션     |
| ---------------- | ------ | -------- |
| `title`          | TEXT   | required |
| `body`           | EDITOR | —        |
| `published_date` | DATE   | —        |

4. **만들기**를 클릭합니다.

---

## 4단계: 첫 콘텐츠 작성

1. **Contents** 탭을 클릭합니다.
2. **새 콘텐츠** 버튼을 클릭합니다.
3. `title`, `body`, `published_date` 필드를 채웁니다.
4. 우측 상단의 상태를 **DRAFT** → **PUBLISHED**로 변경합니다.
5. **저장**을 클릭합니다.

콘텐츠 상태가 `PUBLISHED`여야 외부 API에서 기본적으로 조회됩니다.

---

## 5단계: API Key 발급

1. Project 설정의 **API Keys** 메뉴를 클릭합니다.
2. **새 API Key** 버튼을 클릭합니다.
3. 이름을 입력하고 키 유형을 선택합니다. 이 가이드에서는 **게시용(Publishable)** 키(`pk-proj_…`)를 선택합니다. 조회(GET)만 가능해 브라우저에서 사용해도 안전합니다. 서버에서 데이터를 생성·수정·삭제해야 할 때만 **시크릿(Secret)** 키(`sk-proj_…`)를 선택하세요.
4. **생성**을 클릭하고 발급된 키를 복사합니다.

API Key는 생성 직후에만 전체 값이 표시됩니다. Onttle은 키의 해시만 저장하므로 전체 값을 다시 보여줄 수 없고, 목록에는 접두사와 마지막 4자리만 표시됩니다. 반드시 안전한 곳에 저장하세요. 키 유형은 나중에 바꿀 수 없으므로 필요하면 새 키를 발급하세요.

---

## 6단계: 환경 변수 설정

발급한 API Key를 환경 변수로 관리합니다. 코드에 직접 입력하지 마세요.

```bash
# .env.local
ONTTLE_API_KEY=your_api_key_here
```

---

## 7단계: Contents 조회

이제 REST API로 콘텐츠를 조회합니다. `{projectId}`는 Project 설정에서 확인할 수 있습니다.

**curl**

```bash
curl https://api.onttle.com/v1/{projectId}/tables/posts/contents \
  -H "Authorization: Bearer $ONTTLE_API_KEY"
```

**TypeScript (fetch)**

```typescript
const res = await fetch(
  'https://api.onttle.com/v1/{projectId}/tables/posts/contents?status=PUBLISHED',
  {
    headers: {
      Authorization: `Bearer ${process.env.ONTTLE_API_KEY}`,
    },
  },
)

const { list, pagination } = await res.json()
```

**성공 응답 예시**

```json
{
  "list": [
    {
      "id": "cnt_01hxk2",
      "slug": "hello-onttle",
      "status": "PUBLISHED",
      "data": {
        "title": "Hello Onttle",
        "published_date": "2026-08-30T09:00:00.000Z"
      },
      "created_at": "2026-08-30T09:00:00.000Z",
      "updated_at": "2026-08-30T09:00:00.000Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 1
  }
}
```

---

## 문제 해결

| 증상               | 확인 사항                                      |
| ------------------ | ---------------------------------------------- |
| `401 Unauthorized` | Authorization 헤더 포함 여부와 API Key 값 확인 |
| `403 Forbidden`    | 게시용 키(`pk-proj_…`)로 쓰기 요청을 보냈는지 확인하고 시크릿 키(`sk-proj_…`)를 사용. 허용 호스트와 Project 범위도 확인 |
| `list`가 빈 배열   | 콘텐츠 상태가 `PUBLISHED`인지 확인             |
| `404`              | `{projectId}`와 테이블 이름(`posts`) 확인      |

---

## 다음 단계

- [개념 설명](/docs/getting-started/concepts) — Workspace, Project, Table의 관계를 더 깊이 이해합니다.
- [테이블 설계](/docs/guides/table-design) — 더 복잡한 스키마를 설계하는 방법을 알아봅니다.
- [인증 방식](/docs/api-reference/authentication) — API Key 권한과 보안 관리 방법을 확인합니다.
