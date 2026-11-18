---
title: '회원 API'
description: '회원 가입·이메일 인증·로그인·토큰 갱신·내 정보·탈퇴 엔드포인트의 요청, 응답, 에러 코드를 설명합니다.'
category: 'rest-api'
slug: 'members-api'
locale: 'ko'
apiVersion: 'v1'
order: 7
updatedAt: '2026-11-12'
---

# 회원 API

내 서비스의 최종 사용자(회원)를 위한 인증 API입니다. Project에서 [회원 기능](/docs/core-features/members)을 켜야 동작합니다.

```
https://api.onttle.com/v1/{projectId}
```

---

## 인증 방법

회원 API는 API Key를 `Authorization`이 아니라 **`X-Onttle-Key` 헤더**로 보냅니다. `Authorization` 헤더는 로그인한 회원의 access 토큰에 씁니다.

| 자격 증명            | 전달                                  | 용도                                             |
| -------------------- | ------------------------------------- | ------------------------------------------------ |
| 게시용 키 `pk-proj_…` | `X-Onttle-Key` 헤더                   | 브라우저·앱. `/auth/*`, `/me`를 호출할 수 있습니다 |
| 회원 access 토큰     | `Authorization: Bearer {accessToken}` | 로그인한 회원 (`/me`, `/auth/logout`, 권한 규칙이 걸린 Table) |
| 시크릿 키 `sk-proj_…` | `X-Onttle-Key` 헤더                   | 서버 전용. Table 권한 규칙과 관계없이 항상 허용됩니다 |

```bash
curl https://api.onttle.com/v1/{projectId}/me \
  -H "X-Onttle-Key: $ONTTLE_PUBLISHABLE_KEY" \
  -H "Authorization: Bearer $ACCESS_TOKEN"
```

게시용 키는 원래 GET 전용이지만 `/auth/*`와 `/me`에서는 POST·PATCH·PUT·DELETE도 허용됩니다. 쿠키 모드에서는 `Authorization` 대신 쿠키를 쓰며, [회원 문서의 쿠키 모드](/docs/core-features/members)를 참고하세요.

---

## 엔드포인트 요약

| 메서드   | 경로                           | 인증         | 설명                                           |
| -------- | ------------------------------ | ------------ | ---------------------------------------------- |
| `POST`   | `/auth/signup`                 | 게시용 키    | 가입 요청, 인증 메일 발송 (회원은 인증 대기)   |
| `POST`   | `/auth/email/verify`           | 게시용 키    | 인증 토큰 확인 → 활성, 크레딧 차감             |
| `POST`   | `/auth/email/resend`           | 게시용 키    | 인증 메일 재발송                               |
| `POST`   | `/auth/login`                  | 게시용 키    | 로그인 (`?mode=cookie` 지원)                   |
| `POST`   | `/auth/token/refresh`          | 게시용 키    | 토큰 갱신 (refresh 토큰 회전)                  |
| `POST`   | `/auth/logout`                 | 회원 토큰    | 로그아웃 (refresh 토큰 폐기)                   |
| `POST`   | `/auth/password/reset-request` | 게시용 키    | 비밀번호 재설정 메일 발송                      |
| `POST`   | `/auth/password/reset`         | 게시용 키    | 새 비밀번호 설정, 기존 세션 전부 폐기          |
| `GET`    | `/me`                          | 회원 토큰    | 내 정보 조회                                   |
| `PATCH`  | `/me`                          | 회원 토큰    | 프로필·비밀번호 수정                           |
| `DELETE` | `/me`                          | 회원 토큰    | 탈퇴 (즉시 삭제, 90일 재가입 제한)             |
| `GET`    | `/me/consents`                 | 회원 토큰    | 동의 이력 조회                                 |
| `PUT`    | `/me/consents`                 | 회원 토큰    | 동의·철회 기록 추가                            |

`GET /auth/oauth/{provider}`는 소셜 로그인용으로 예약된 경로이며 현재는 `501`을 반환합니다.

회원 목록 조회, 역할 변경, 정지, 강제 탈퇴, 회원 직접 추가는 API로 제공하지 않습니다. 대시보드의 **회원** 메뉴에서 관리합니다.

---

## 회원 객체

```json
{
  "id": "0b6f2c1e-7c3a-4d0e-9a51-2f6f5e1c9b10",
  "email": "user@example.com",
  "role": "member",
  "status": "active",
  "emailVerified": true,
  "profile": { "name": "홍길동" },
  "createdAt": "2026-11-12T03:00:00.000Z",
  "lastLoginAt": "2026-11-12T03:05:00.000Z"
}
```

| 필드            | 설명                                                        |
| --------------- | ----------------------------------------------------------- |
| `role`          | 역할 이름. 기본값 `member`. 테이블 권한 규칙 `role:<이름>`에 쓰입니다 |
| `status`        | `pending`(인증 대기) / `active`(활성) / `suspended`(정지)   |
| `profile`       | 서비스가 정한 자유 형식 객체 (8KB 이내)                     |

---

## 가입과 이메일 인증

### POST /auth/signup

```json
{
  "email": "user@example.com",
  "password": "at-least-8-chars",
  "profile": { "name": "홍길동" },
  "consents": [
    { "type": "terms", "version": "2026-11", "agreed": true },
    { "type": "privacy", "version": "2026-11", "agreed": true },
    { "type": "marketing", "version": "2026-11", "agreed": false }
  ]
}
```

| 필드       | 필수 | 설명                                                    |
| ---------- | ---- | ------------------------------------------------------- |
| `email`    | O    | 앞뒤 공백을 지우고 소문자로 바꿔 저장합니다             |
| `password` | O    | 8~128자                                                 |
| `profile`  |      | 자유 형식 객체                                          |
| `consents` |      | 동의 항목. `type`은 `terms`, `privacy`, `marketing`     |

`201`과 함께 `{ "member": { … "status": "pending" } }`을 반환하고 인증 메일을 보냅니다.

탈퇴한 지 90일이 지나지 않은 이메일은 다시 가입할 수 없으며 `409 AUTH_EMAIL_WITHDRAWN`이 반환됩니다. 이 기간에는 이메일의 암호화된 값만 보관되고 이후 자동 삭제됩니다.

오류: `400 AUTH_INVALID_REQUEST`, `402 AUTH_SIGNUP_PAUSED`, `403 AUTH_NOT_ENABLED`, `409 AUTH_EMAIL_EXISTS`, `409 AUTH_EMAIL_WITHDRAWN`, `429 AUTH_RATE_LIMITED`

### POST /auth/email/verify

메일 링크가 리다이렉트 URL에 붙여 준 `token`을 보냅니다.

```json
{ "token": "…" }
```

`200`과 함께 `{ "member": { … "status": "active" } }`을 반환합니다. 이 시점에 가입 1건의 크레딧이 차감됩니다. 같은 토큰으로 다시 호출해도 성공하며 추가로 차감되지 않습니다.

오류: `401 AUTH_TOKEN_INVALID`, `401 AUTH_TOKEN_EXPIRED` (링크는 24시간 유효)

### POST /auth/email/resend

```json
{ "email": "user@example.com" }
```

`{ "success": true }`를 반환합니다. 인증 대기 중인 회원이 아니어도 응답은 같습니다. 같은 이메일에는 1분에 한 번, 하루 5번까지만 보내며 초과하면 `429 AUTH_RATE_LIMITED`입니다.

---

## 로그인과 토큰

### POST /auth/login

```json
{ "email": "user@example.com", "password": "at-least-8-chars" }
```

```json
{
  "accessToken": "eyJhbGciOiJFZERTQSIs…",
  "refreshToken": "q1W8…",
  "expiresIn": 900,
  "member": { "id": "…", "email": "user@example.com", "role": "member", "status": "active" }
}
```

- `accessToken`은 JWT이며 `expiresIn`초 뒤 만료됩니다(기본 15분).
- 쿠키 모드(`?mode=cookie` 또는 "쿠키만" 모드)에서는 토큰을 본문에 넣지 않고 `httpOnly` 쿠키로 내려보내며, 본문은 `{ "expiresIn", "member", "csrfToken" }`입니다. 쿠키 모드에서는 `remember: false`로 세션 쿠키를 요청할 수 있습니다.

오류: `401 AUTH_INVALID_CREDENTIALS`(이메일·비밀번호 중 어느 쪽이 틀렸는지 구분하지 않음), `403 AUTH_EMAIL_NOT_VERIFIED`, `403 AUTH_MEMBER_SUSPENDED`, `429 AUTH_RATE_LIMITED`

### POST /auth/token/refresh

```json
{ "refreshToken": "q1W8…" }
```

로그인과 같은 형태의 응답을 반환합니다. **refresh 토큰은 1회용**이라 응답의 새 `refreshToken`을 저장해야 합니다. 이미 사용한 토큰을 다시 보내면 `401 AUTH_TOKEN_INVALID`가 반환되고 그 로그인의 세션이 모두 폐기됩니다. 쿠키 모드에서는 빈 본문(`{}`)으로 호출합니다.

오류: `401 AUTH_TOKEN_INVALID`, `401 AUTH_TOKEN_EXPIRED`, `403 AUTH_MEMBER_SUSPENDED`

### POST /auth/logout

```json
{ "refreshToken": "q1W8…" }
```

해당 refresh 토큰의 로그인을 끊습니다. `{ "all": true }`를 보내거나 토큰을 생략하면 그 회원의 모든 세션을 끊습니다.

---

## 비밀번호 재설정

### POST /auth/password/reset-request

```json
{ "email": "user@example.com" }
```

항상 `{ "success": true }`를 반환합니다. 가입된 이메일인지 알려 주지 않습니다.

### POST /auth/password/reset

```json
{ "token": "…", "password": "new-password" }
```

재설정 링크의 토큰(30분 유효, 1회용)으로 새 비밀번호를 설정합니다. 성공하면 그 회원의 기존 세션이 모두 폐기됩니다.

오류: `401 AUTH_TOKEN_INVALID`, `401 AUTH_TOKEN_EXPIRED`

---

## 내 정보

### GET /me

`{ "member": { … } }`을 반환합니다.

### PATCH /me

```json
{
  "profile": { "name": "홍길동", "phone": "010-0000-0000" },
  "currentPassword": "old-password",
  "newPassword": "new-password"
}
```

`profile`만, 또는 비밀번호만 보낼 수 있습니다. 비밀번호를 바꾸려면 `currentPassword`가 필요하며, 바꾸면 모든 세션이 로그아웃됩니다.

### DELETE /me

회원 탈퇴입니다. 회원 정보는 즉시 삭제되고 `{ "success": true }`를 반환합니다.

탈퇴한 이메일은 90일 동안 같은 Project에 다시 가입할 수 없습니다. 이 기간에는 이메일의 암호화된 값만 보관되고 이후 자동 삭제됩니다.

### GET /me/consents, PUT /me/consents

```json
{ "consents": [{ "type": "marketing", "version": "2026-11", "agreed": false }] }
```

`PUT`은 동의 또는 철회를 이력에 **추가**합니다. 이전 기록은 덮어쓰지 않습니다. 두 엔드포인트 모두 최신순 이력을 반환합니다.

```json
{
  "consents": [
    { "type": "marketing", "version": "2026-11", "agreed": false, "agreedAt": "2026-11-12T04:00:00.000Z" },
    { "type": "terms", "version": "2026-11", "agreed": true, "agreedAt": "2026-11-12T03:00:00.000Z" }
  ]
}
```

---

## 테이블 권한 규칙과 콘텐츠 API

Table에 [권한 규칙](/docs/core-features/members)을 정하면, 게시용 키 요청에 회원 토큰을 함께 보내 콘텐츠 API를 호출할 수 있습니다.

```bash
curl -X POST https://api.onttle.com/v1/{projectId}/tables/posts \
  -H "X-Onttle-Key: $ONTTLE_PUBLISHABLE_KEY" \
  -H "Authorization: Bearer $ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{ "title": "첫 글", "is_publish": true }'
```

| 규칙          | 회원 토큰 없음 | 권한 없는 회원       |
| ------------- | -------------- | -------------------- |
| `public`      | 허용           | 허용                 |
| `member`      | `401`          | —                    |
| `owner`       | `401`          | `403 AUTH_FORBIDDEN` (목록은 본인 것만 반환) |
| `role:<이름>` | `401`          | `403 AUTH_FORBIDDEN` |
| `none`        | `403`          | `403`                |

---

## 회원 파일 업로드

Project의 **파일 업로드 권한**이 "로그인한 회원" 또는 "특정 역할"이면 회원 토큰으로 파일을 올릴 수 있습니다. 기본값은 시크릿 키 전용입니다.

```bash
curl -X POST https://api.onttle.com/v1/{projectId}/files \
  -H "X-Onttle-Key: $ONTTLE_PUBLISHABLE_KEY" \
  -H "Authorization: Bearer $ACCESS_TOKEN" \
  -F "file=@./photo.jpg"
```

응답은 시크릿 키로 업로드할 때와 같은 파일 객체입니다. 반환된 `id`를 FILE 컬럼 값으로 씁니다.

| 상황                                 | 응답                          |
| ------------------------------------ | ----------------------------- |
| 권한이 "비밀 키 전용"                | `403`                         |
| 회원 토큰 없음                       | `401 AUTH_TOKEN_INVALID`      |
| 역할이 맞지 않음                     | `403 AUTH_FORBIDDEN`          |
| 파일이 회원 업로드 최대 크기를 넘음  | `400 AUTH_INVALID_REQUEST`    |
| 10분에 30회 초과                     | `429 AUTH_RATE_LIMITED`       |

회원 업로드에서는 `is_ocr`, `is_generate`가 무시되며, 올린 파일은 그 회원이 탈퇴하면 삭제됩니다.

---

## 에러 코드

회원 API의 오류 응답은 다음 형태입니다.

```json
{ "code": "AUTH_INVALID_CREDENTIALS", "message": "The e-mail or password is incorrect." }
```

| 코드                       | HTTP | 상황                                                 |
| -------------------------- | ---- | ---------------------------------------------------- |
| `AUTH_INVALID_REQUEST`     | 400  | 요청 본문이 올바르지 않음 (`message`에 필드가 표시됨) |
| `AUTH_INVALID_CREDENTIALS` | 401  | 이메일·비밀번호 불일치 (어느 쪽인지 구분하지 않음)   |
| `AUTH_TOKEN_EXPIRED`       | 401  | 토큰 만료                                            |
| `AUTH_TOKEN_INVALID`       | 401  | 토큰 누락·위조·재사용                                |
| `AUTH_SIGNUP_PAUSED`       | 402  | 크레딧 소진 또는 월 가입 상한 도달                   |
| `AUTH_EMAIL_NOT_VERIFIED`  | 403  | 이메일 인증 전 로그인                                |
| `AUTH_MEMBER_SUSPENDED`    | 403  | 정지된 회원                                          |
| `AUTH_FORBIDDEN`           | 403  | 권한 규칙 위반, CSRF 검사 실패, 허용되지 않은 키     |
| `AUTH_NOT_ENABLED`         | 403  | Project에서 회원 기능이 꺼져 있음                    |
| `AUTH_EMAIL_EXISTS`        | 409  | 이미 가입된 이메일                                   |
| `AUTH_EMAIL_WITHDRAWN`     | 409  | 탈퇴 후 90일 이내 재가입                             |
| `AUTH_RATE_LIMITED`        | 429  | 요청 횟수 초과                                       |
| `AUTH_PROVIDER_NOT_SUPPORTED` | 501 | 소셜 로그인 미지원                                 |

`AUTH_TOKEN_EXPIRED`를 받으면 `/auth/token/refresh`로 토큰을 갱신한 뒤 요청을 다시 보냅니다. 갱신도 실패하면 다시 로그인해야 합니다.

---

## 다음 단계

- [회원 (가입·로그인)](/docs/core-features/members) — 회원 기능 설정, 권한 규칙, 쿠키 모드를 확인합니다.
- [OpenAPI 명세](/docs/developers/openapi) — 회원 기능을 켠 Project의 명세에는 회원 엔드포인트와 권한 규칙이 포함됩니다.
- [인증](/docs/rest-api/authentication) — API Key 발급과 키 유형을 확인합니다.
