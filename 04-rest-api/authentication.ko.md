---
title: '인증'
description: 'API Key를 발급하고 REST API 요청을 인증하는 방법과 보안 권장 사항을 설명합니다.'
category: 'rest-api'
slug: 'authentication'
locale: 'ko'
apiVersion: 'v1'
order: 2
popular: true
updatedAt: '2026-11-12'
---

# 인증 방식

Onttle REST API는 **Bearer Token** 방식으로 인증합니다. Project에서 발급한 API Key를 모든 요청의 `Authorization` 헤더에 포함해야 합니다.

---

## API Key란?

API Key는 외부 애플리케이션이 특정 Project의 데이터에 접근할 수 있는 인증 수단입니다.

- **Project 범위:** API Key는 특정 Project에 소속됩니다. 해당 Project의 데이터만 접근할 수 있습니다.
- **키 유형:** **게시용(publishable)** 키(`pk-proj_…`)는 읽기 전용이고, **시크릿(secret)** 키(`sk-proj_…`)는 쓰기도 할 수 있습니다.
- **허용 호스트:** 특정 도메인 또는 IP 주소(CIDR 대역 포함)에서만 요청을 허용하도록 제한할 수 있습니다.

---

## API Key 발급

1. 대시보드에서 API Key를 발급할 **Project**를 엽니다.
2. 좌측 메뉴에서 **API Keys**를 클릭합니다.
3. **새 API Key** 버튼을 클릭합니다.
4. 이름을 입력합니다 (예: `production-read`).
5. 키 유형을 선택합니다.
   - **게시용 (`pk-proj_…`):** GET 요청만 허용합니다. 허용 호스트와 함께 브라우저·공개 웹사이트에서 사용할 수 있습니다.
   - **시크릿 (`sk-proj_…`):** POST, PUT, PATCH, DELETE 요청과 파일 업로드, 채팅, 임베딩까지 허용합니다. 서버에서만 사용하세요.
6. 허용 호스트를 입력합니다 (선택 사항). 한 줄에 하나씩 도메인(`https://example.com`, `*.example.com`) 또는 IP(`203.0.113.10`, `203.0.113.0/24`)를 입력하며, 하나라도 일치하면 요청이 허용됩니다. 서버에서 호출할 때는 IP를 사용합니다.
7. 만료를 선택합니다. 만료 없음, 30일·90일·180일·1년 후, 또는 날짜 지정 중 하나입니다.
8. **생성**을 클릭합니다.

API Key는 생성 직후 한 번만 전체 값이 표시됩니다. 창을 닫기 전에 반드시 복사하세요. Onttle은 키의 해시만 저장하므로 전체 값을 다시 보여줄 수 없습니다. 목록에는 키의 접두사와 마지막 4자리(`sk-proj_abcd…wxyz`)만 표시됩니다. 키 유형은 생성 후 변경할 수 없으며, 필요하면 새 키를 발급하세요.

---

## 인증 헤더 사용

모든 API 요청에 다음 헤더를 포함합니다.

```
Authorization: Bearer {API_KEY}
```

**curl 예제**

```bash
curl https://api.onttle.com/v1/{projectId}/tables/posts/contents \
  -H "Authorization: Bearer $ONTTLE_API_KEY"
```

**TypeScript 예제**

```typescript
const res = await fetch(
  `https://api.onttle.com/v1/${projectId}/tables/posts/contents`,
  {
    headers: {
      Authorization: `Bearer ${process.env.ONTTLE_API_KEY}`,
      'Content-Type': 'application/json',
    },
  },
)
```

---

## X-Onttle-Key 헤더

API Key는 `Authorization` 대신 **`X-Onttle-Key` 헤더**로도 보낼 수 있습니다.

```bash
curl https://api.onttle.com/v1/{projectId}/tables/posts \
  -H "X-Onttle-Key: $ONTTLE_API_KEY"
```

[회원 기능](/docs/core-features/members)을 쓸 때는 이 헤더가 필요합니다. `Authorization` 헤더를 로그인한 회원의 access 토큰에 쓰기 때문입니다.

```bash
curl https://api.onttle.com/v1/{projectId}/me \
  -H "X-Onttle-Key: $ONTTLE_PUBLISHABLE_KEY" \
  -H "Authorization: Bearer $MEMBER_ACCESS_TOKEN"
```

| 헤더            | 값                    | 용도                                   |
| --------------- | --------------------- | -------------------------------------- |
| `X-Onttle-Key`  | API Key               | Project 인증 (모든 엔드포인트)         |
| `Authorization` | `Bearer {API Key}`    | Project 인증 (기존 방식, 계속 지원)    |
| `Authorization` | `Bearer {accessToken}` | 로그인한 회원 (`X-Onttle-Key`와 함께) |

---

## 환경 변수로 관리하기

API Key는 코드에 직접 포함하지 말고 환경 변수로 관리합니다.

```bash
# .env.local (Next.js)
ONTTLE_API_KEY=your_api_key_here
ONTTLE_PROJECT_ID=your_project_id
```

서버 환경 변수는 클라이언트 번들에 포함되지 않습니다. 브라우저에서 직접 API를 호출할 때는 서버 경유 방식을 사용하세요.

---

## 게시용 vs. 시크릿 키

| 작업                                   | 게시용 (`pk-proj_…`) | 시크릿 (`sk-proj_…`) |
| -------------------------------------- | -------------------- | -------------------- |
| GET 요청                               | O                    | O                    |
| POST / PUT / PATCH / DELETE            | X                    | O                    |
| 파일 업로드, 채팅 메시지, 임베딩 검색 | X                    | O                    |
| 회원 API (`/auth/*`, `/me`)의 POST·PATCH·PUT·DELETE | O           | O                    |
| Table 권한 규칙이 허용한 콘텐츠 생성·수정·삭제 | O (회원 토큰 필요 여부는 규칙에 따름) | O |
| 파일 업로드 권한이 허용한 회원의 파일 업로드 | O (회원 토큰 필요) | O |

퍼블릭 웹사이트처럼 콘텐츠를 조회만 하는 경우에는 게시용 Key를 사용하세요. 시크릿 Key는 서버 환경에서만 사용하세요.

게시용 Key의 쓰기는 세 경우에만 허용됩니다. 가입·로그인 같은 [회원 API](/docs/rest-api/members-api), Table의 [권한 규칙](/docs/core-features/members)이 허용한 콘텐츠 쓰기, 그리고 파일 업로드 권한이 허용한 회원의 파일 업로드입니다. 기본값은 모두 "비밀 키 전용"이므로, 설정을 바꾸지 않은 Project는 지금까지처럼 게시용 Key로 쓸 수 없습니다.

---

## 보안 권장 사항

**절대 하지 말아야 할 것**

- 소스 코드에 API Key를 하드코딩하는 것
- API Key를 Git에 커밋하는 것
- 시크릿 Key(`sk-proj_…`)를 브라우저 클라이언트 코드에 포함하는 것
- API 응답 로그에 Key를 기록하는 것

**권장 사항**

- 환경 변수로 관리하고 `.env.local`을 `.gitignore`에 추가합니다.
- 서비스별로 별도의 API Key를 발급합니다 (운영/개발 분리).
- 쓰기 작업은 서버 측 API Route에서만 실행합니다.
- 노출이 의심되면 즉시 Key를 삭제하고 새 Key를 발급합니다.

---

## 만료일과 미사용 경고

**만료일**

키를 만들거나 수정할 때 만료를 정할 수 있습니다. 만료일이 지나면 그 키로 보내는 모든 요청은 `401 apiKeyExpired`로 거부되며, 키는 목록에 **만료됨**으로 남아 있어 원인을 확인할 수 있습니다. 계속 쓰려면 키를 수정해 만료일을 연장하거나 새 키를 발급하세요. 외부 협업자나 단기 캠페인에 발급하는 키에는 만료일을 두는 것을 권장합니다.

**경고**

API Keys 목록과 **Workspace → 보안**의 API 키 상태에 다음 키가 표시됩니다.

| 상태 | 조건 |
| ---- | ---- |
| 만료됨 | 만료일이 지난 키 |
| N일 후 만료 | 7일 안에 만료되는 키 |
| N일 미사용 | 활성화되어 있지만 90일 이상 사용되지 않은 키 (한 번도 쓰지 않은 키는 생성일 기준) |

미사용 키는 쓰이지 않는데도 유출되면 그대로 악용될 수 있으므로, 필요 없다면 삭제하거나 비활성화하세요.

---

## API Key 교체 및 삭제

**비활성화**

일시적으로 Key를 차단하려면 API Keys 목록에서 해당 Key를 비활성화합니다. 비활성화된 Key로는 API 요청이 거부됩니다.

**삭제**

Key를 삭제하면 해당 Key로 이루어진 모든 요청이 즉시 거부됩니다. 삭제된 Key는 복구할 수 없습니다.

---

## 인증 오류 처리

| 오류               | 원인                                                        |
| ------------------ | ----------------------------------------------------------- |
| `401 Unauthorized` | Authorization 헤더 누락, Key 값이 잘못됨, Key가 삭제됨      |
| `401 apiKeyExpired` | Key의 만료일이 지남 (만료일 연장 또는 새 Key 발급)       |
| `403 Forbidden`    | Key 권한 부족 (게시용 Key로 쓰기 시도), 비활성화된 엔드포인트 |

---

## 다음 단계

- [에러 코드](/docs/api-reference/error-codes) — 오류 상태 코드별 원인과 해결 방법을 확인합니다.
- [회원 API](/docs/rest-api/members-api) — 최종 사용자의 가입·로그인과 회원 토큰 사용법을 확인합니다.
- [REST API 사용법](/docs/rest-api/request-examples) — 인증 헤더를 포함한 완전한 요청 예제를 확인합니다.
