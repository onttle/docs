---
title: '에러 코드'
description: 'REST API 오류 응답 구조와 HTTP 상태 코드별 원인, 해결 방법을 설명합니다.'
category: 'rest-api'
slug: 'error-codes'
locale: 'ko'
apiVersion: 'v1'
order: 6
updatedAt: '2026-11-12'
---

# 에러 코드

API 요청이 실패하면 HTTP 상태 코드와 함께 오류 정보를 담은 JSON을 반환합니다.

---

## 오류 응답 구조

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "title 필드는 필수입니다.",
    "details": [
      {
        "field": "title",
        "message": "필수 항목입니다."
      }
    ]
  }
}
```

| 필드            | 타입            | 설명                                                 |
| --------------- | --------------- | ---------------------------------------------------- |
| `error.code`    | `string`        | 오류를 식별하는 코드                                 |
| `error.message` | `string`        | 오류 내용을 설명하는 메시지                          |
| `error.details` | `array \| null` | 필드별 유효성 오류 목록 (유효성 검사 실패 시만 포함) |

---

## HTTP 상태 코드 목록

### 400 Bad Request

요청 형식이 잘못되었거나 필수 파라미터가 누락된 경우입니다.

**원인**

- JSON 형식 오류
- 필수 쿼리 파라미터 또는 요청 본문 필드 누락
- 지원하지 않는 값

**해결 방법**

`Content-Type: application/json` 헤더가 포함되었는지 확인합니다. 요청 본문이 유효한 JSON인지 검증하고, 파라미터 이름과 타입을 레퍼런스와 비교합니다.

---

### 401 Unauthorized

인증이 실패한 경우입니다.

**원인**

- `Authorization` 헤더 누락
- `Bearer` 접두사 없이 Key만 전달
- API Key 값이 잘못되었거나 존재하지 않음
- API Key가 삭제되었거나 비활성화됨

**해결 방법**

헤더 형식이 `Authorization: Bearer {API_KEY}`인지 확인합니다. API Key를 복사할 때 앞뒤 공백이 포함되지 않았는지 확인합니다.

```bash
# 올바른 형식
curl -H "Authorization: Bearer abc123xyz"

# 잘못된 형식 (Bearer 없음)
curl -H "Authorization: abc123xyz"
```

---

### 403 Forbidden

인증은 성공했지만 해당 작업을 수행할 권한이 없는 경우입니다.

**원인**

- 게시용 API Key(`pk-proj_…`)로 쓰기(POST, PUT, PATCH, DELETE), 파일 업로드, Chat·Embeddings 요청
- 허용 호스트 외 도메인에서 요청
- 다른 Project의 데이터에 접근 시도

**해결 방법**

API Key의 유형과 허용 호스트를 확인합니다. 게시용 키는 읽기 전용이므로 쓰기가 필요하면 서버에서 시크릿 키(`sk-proj_…`)를 사용합니다. 시크릿 키가 필요한 경우 응답 메시지로 알려줍니다.

```json
{
  "type": "forbidden",
  "title": "Forbidden",
  "message": "This endpoint requires a secret API key (sk-proj_…). Publishable keys (pk-proj_…) are read-only."
}
```

---

### 404 Not Found

요청한 리소스가 존재하지 않는 경우입니다.

**원인**

- `{projectId}`, `{tableId}`, `{contentsId}` 등의 식별자가 잘못됨
- 이미 삭제된 리소스에 접근
- 경로 오타

**해결 방법**

대시보드에서 각 리소스의 ID를 복사해 요청 경로와 비교합니다. 경로에 오타가 없는지 확인합니다.

---

### 409 Conflict

리소스 충돌이 발생한 경우입니다.

**원인**

- `unique` 설정된 컬럼에 중복 값 입력
- 현재 상태에서 허용되지 않는 상태 전환 시도

**해결 방법**

중복 값이 있는지 확인하거나 현재 리소스 상태를 먼저 조회한 후 요청합니다.

---

### 422 Unprocessable Entity

요청 구조는 올바르지만 유효성 검사를 통과하지 못한 경우입니다.

**원인**

- `required` 컬럼에 값 누락
- 숫자 컬럼의 `min`/`max` 범위 초과
- 텍스트 컬럼의 `minText`/`maxText` 길이 위반

**오류 응답 예시**

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "유효성 검사에 실패했습니다.",
    "details": [
      {
        "field": "data.title",
        "message": "title 필드는 필수입니다."
      },
      {
        "field": "data.price",
        "message": "price는 0 이상이어야 합니다."
      }
    ]
  }
}
```

**해결 방법**

`error.details` 배열의 각 항목에서 문제 필드와 원인을 확인하고 요청 데이터를 수정합니다.

---

### 429 Too Many Requests

단시간에 너무 많은 요청을 보낸 경우입니다.

**해결 방법**

재시도 전 잠시 대기합니다. 응답 헤더의 `Retry-After` 값을 확인해 대기 시간을 조정합니다.

---

### 500 Internal Server Error

서버 내부 오류가 발생한 경우입니다.

**해결 방법**

동일한 요청을 잠시 후 재시도합니다. 문제가 지속되면 요청 ID(응답 헤더에 포함될 수 있음)와 함께 [지원 채널](/support)에 문의합니다.

---

## 회원 API 에러 코드

[회원 API](/docs/rest-api/members-api)(`/auth/*`, `/me`)와 Table 권한 규칙·회원 파일 업로드 오류는 위 구조 대신 `code`와 `message`만 담은 응답을 반환합니다.

```json
{ "code": "AUTH_EMAIL_WITHDRAWN", "message": "This e-mail address was withdrawn and cannot sign up again for 90 days after withdrawal." }
```

| 코드                       | HTTP | 상황                                               |
| -------------------------- | ---- | -------------------------------------------------- |
| `AUTH_INVALID_REQUEST`     | 400  | 요청 본문이 올바르지 않음                          |
| `AUTH_INVALID_CREDENTIALS` | 401  | 이메일·비밀번호 불일치 (어느 쪽인지 구분하지 않음) |
| `AUTH_TOKEN_EXPIRED`       | 401  | 토큰 만료                                          |
| `AUTH_TOKEN_INVALID`       | 401  | 토큰 누락·위조·재사용                              |
| `AUTH_SIGNUP_PAUSED`       | 402  | 크레딧 소진 또는 월 가입 상한 도달                 |
| `AUTH_EMAIL_NOT_VERIFIED`  | 403  | 이메일 인증 전 로그인                              |
| `AUTH_MEMBER_SUSPENDED`    | 403  | 정지된 회원                                        |
| `AUTH_FORBIDDEN`           | 403  | 권한 규칙 위반, CSRF 검사 실패                     |
| `AUTH_NOT_ENABLED`         | 403  | Project에서 회원 기능이 꺼져 있음                  |
| `AUTH_EMAIL_EXISTS`        | 409  | 이미 가입된 이메일                                 |
| `AUTH_EMAIL_WITHDRAWN`     | 409  | 탈퇴 후 90일 이내 재가입                           |
| `AUTH_RATE_LIMITED`        | 429  | 요청 횟수 초과                                     |

**해결 방법**

- `AUTH_TOKEN_EXPIRED`: `POST /auth/token/refresh`로 토큰을 갱신한 뒤 다시 요청합니다.
- `AUTH_TOKEN_INVALID`: 다시 로그인합니다. refresh 토큰은 1회용이므로 갱신할 때마다 새 토큰을 저장해야 합니다.
- `AUTH_SIGNUP_PAUSED`: Workspace의 크레딧과 Project의 월 가입 상한을 확인합니다. 기존 회원의 로그인은 영향을 받지 않습니다.
- `AUTH_EMAIL_WITHDRAWN`: 탈퇴 후 90일이 지나야 같은 이메일로 가입할 수 있습니다. 관리자가 대시보드에서 제한을 풀 수 있습니다.

---

## TypeScript 오류 처리 예제

```typescript
const res = await fetch(
  `https://api.onttle.com/v1/{projectId}/tables/posts/contents`,
  {
    headers: {
      Authorization: `Bearer ${process.env.ONTTLE_API_KEY}`,
    },
  },
)

if (!res.ok) {
  const error = await res.json()

  switch (res.status) {
    case 401:
      throw new Error('인증 실패: API Key를 확인하세요.')
    case 403:
      throw new Error('권한 없음: API Key 권한을 확인하세요.')
    case 404:
      throw new Error('리소스를 찾을 수 없습니다.')
    case 422:
      console.error('유효성 오류:', error.error.details)
      throw new Error('요청 데이터를 확인하세요.')
    default:
      throw new Error(`API 오류: ${res.status}`)
  }
}

const data = await res.json()
```

---

## 다음 단계

- [인증 방식](/docs/api-reference/authentication) — 401, 403 오류를 방지하는 올바른 인증 방법을 확인합니다.
- [REST API 사용법](/docs/rest-api/request-examples) — 오류 처리를 포함한 완전한 요청 예제를 확인합니다.
