---
title: 'Members API'
description: 'Requests, responses and error codes of the member sign-up, email verification, login, token refresh, profile and withdrawal endpoints.'
category: 'rest-api'
slug: 'members-api'
locale: 'en'
apiVersion: 'v1'
order: 7
updatedAt: '2026-11-12'
---

# Members API

The auth API for the end users (members) of your service. It works once the [member feature](/docs/core-features/members) is turned on for the Project.

```
https://api.onttle.com/v1/{projectId}
```

---

## Authentication

The member API takes the API Key in the **`X-Onttle-Key` header**, not in `Authorization`. The `Authorization` header carries the access token of the signed-in member.

| Credential                 | Sent as                               | Used for                                                              |
| -------------------------- | ------------------------------------- | --------------------------------------------------------------------- |
| Publishable key `pk-proj_…` | `X-Onttle-Key` header                 | Browsers and apps. Can call `/auth/*` and `/me`                       |
| Member access token        | `Authorization: Bearer {accessToken}` | The signed-in member (`/me`, `/auth/logout`, Tables with permission rules) |
| Secret key `sk-proj_…`     | `X-Onttle-Key` header                 | Servers only. Always allowed, whatever the Table permission rules      |

```bash
curl https://api.onttle.com/v1/{projectId}/me \
  -H "X-Onttle-Key: $ONTTLE_PUBLISHABLE_KEY" \
  -H "Authorization: Bearer $ACCESS_TOKEN"
```

Publishable keys are normally GET only, but on `/auth/*` and `/me` they may also POST, PATCH, PUT and DELETE. In cookie mode cookies replace `Authorization`; see cookie mode in the [Members guide](/docs/core-features/members).

---

## Endpoint summary

| Method   | Path                           | Auth            | Description                                              |
| -------- | ------------------------------ | --------------- | -------------------------------------------------------- |
| `POST`   | `/auth/signup`                 | Publishable key | Sign up, send the verification email (member is pending) |
| `POST`   | `/auth/email/verify`           | Publishable key | Confirm the token → active; credits are deducted         |
| `POST`   | `/auth/email/resend`           | Publishable key | Resend the verification email                            |
| `POST`   | `/auth/login`                  | Publishable key | Log in (`?mode=cookie` supported)                        |
| `POST`   | `/auth/token/refresh`          | Publishable key | Refresh tokens (the refresh token rotates)               |
| `POST`   | `/auth/logout`                 | Member token    | Log out (revokes the refresh token)                      |
| `POST`   | `/auth/password/reset-request` | Publishable key | Send a password reset email                              |
| `POST`   | `/auth/password/reset`         | Publishable key | Set a new password; every session is revoked             |
| `GET`    | `/me`                          | Member token    | Get my profile                                           |
| `PATCH`  | `/me`                          | Member token    | Update profile / password                                |
| `DELETE` | `/me`                          | Member token    | Withdraw (deleted at once, 90-day re-sign-up block)      |
| `GET`    | `/me/consents`                 | Member token    | Get consent history                                      |
| `PUT`    | `/me/consents`                 | Member token    | Append consent decisions                                 |

`GET /auth/oauth/{provider}` is reserved for social login and currently returns `501`.

Listing members, changing roles, suspending, forced withdrawal and adding members directly are not available through the API. They are done in the dashboard's **End users** menu.

---

## The member object

```json
{
  "id": "0b6f2c1e-7c3a-4d0e-9a51-2f6f5e1c9b10",
  "email": "user@example.com",
  "role": "member",
  "status": "active",
  "emailVerified": true,
  "profile": { "name": "Jane Doe" },
  "createdAt": "2026-11-12T03:00:00.000Z",
  "lastLoginAt": "2026-11-12T03:05:00.000Z"
}
```

| Field     | Description                                                              |
| --------- | ------------------------------------------------------------------------ |
| `role`    | Role name, `member` by default. Used by the `role:<name>` permission rule |
| `status`  | `pending` / `active` / `suspended`                                       |
| `profile` | A free-form object defined by your service (up to 8 KB)                  |

---

## Sign-up and email verification

### POST /auth/signup

```json
{
  "email": "user@example.com",
  "password": "at-least-8-chars",
  "profile": { "name": "Jane Doe" },
  "consents": [
    { "type": "terms", "version": "2026-11", "agreed": true },
    { "type": "privacy", "version": "2026-11", "agreed": true },
    { "type": "marketing", "version": "2026-11", "agreed": false }
  ]
}
```

| Field      | Required | Description                                             |
| ---------- | -------- | ------------------------------------------------------- |
| `email`    | Yes      | Trimmed and lower-cased before it is stored             |
| `password` | Yes      | 8–128 characters                                        |
| `profile`  |          | Free-form object                                        |
| `consents` |          | Consent items. `type` is `terms`, `privacy` or `marketing` |

Returns `201` with `{ "member": { … "status": "pending" } }` and sends the verification email.

An email that was withdrawn less than 90 days ago cannot sign up again; the API returns `409 AUTH_EMAIL_WITHDRAWN`. During that time only an encrypted value of the email is kept, and it is deleted automatically afterwards.

Errors: `400 AUTH_INVALID_REQUEST`, `402 AUTH_SIGNUP_PAUSED`, `403 AUTH_NOT_ENABLED`, `409 AUTH_EMAIL_EXISTS`, `409 AUTH_EMAIL_WITHDRAWN`, `429 AUTH_RATE_LIMITED`

### POST /auth/email/verify

Send the `token` that the email link appended to your redirect URL.

```json
{ "token": "…" }
```

Returns `200` with `{ "member": { … "status": "active" } }`. The credits for one sign-up are deducted here. Calling it again with the same token succeeds and deducts nothing more.

Errors: `401 AUTH_TOKEN_INVALID`, `401 AUTH_TOKEN_EXPIRED` (links are valid for 24 hours)

### POST /auth/email/resend

```json
{ "email": "user@example.com" }
```

Returns `{ "success": true }`, whether or not the email belongs to a pending member. One email per minute and five per day go to the same address; beyond that the API returns `429 AUTH_RATE_LIMITED`.

---

## Login and tokens

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

- `accessToken` is a JWT that expires after `expiresIn` seconds (15 minutes by default).
- In cookie mode (`?mode=cookie`, or the "Cookie only" mode) the tokens are not in the body: they are set as `httpOnly` cookies and the body is `{ "expiresIn", "member", "csrfToken" }`. In cookie mode `remember: false` asks for session cookies.

Errors: `401 AUTH_INVALID_CREDENTIALS` (it does not say whether the email or the password was wrong), `403 AUTH_EMAIL_NOT_VERIFIED`, `403 AUTH_MEMBER_SUSPENDED`, `429 AUTH_RATE_LIMITED`

### POST /auth/token/refresh

```json
{ "refreshToken": "q1W8…" }
```

Returns the same shape as login. **Refresh tokens are single use:** store the new `refreshToken` of the response. Sending a token that was already used returns `401 AUTH_TOKEN_INVALID` and revokes every session of that login. In cookie mode, call it with an empty body (`{}`).

Errors: `401 AUTH_TOKEN_INVALID`, `401 AUTH_TOKEN_EXPIRED`, `403 AUTH_MEMBER_SUSPENDED`

### POST /auth/logout

```json
{ "refreshToken": "q1W8…" }
```

Ends the login of that refresh token. With `{ "all": true }`, or without a token, every session of the member ends.

---

## Password reset

### POST /auth/password/reset-request

```json
{ "email": "user@example.com" }
```

Always returns `{ "success": true }`. It does not reveal whether the email is registered.

### POST /auth/password/reset

```json
{ "token": "…", "password": "new-password" }
```

Sets a new password with the token of the reset link (valid for 30 minutes, single use). On success every existing session of the member is revoked.

Errors: `401 AUTH_TOKEN_INVALID`, `401 AUTH_TOKEN_EXPIRED`

---

## Me

### GET /me

Returns `{ "member": { … } }`.

### PATCH /me

```json
{
  "profile": { "name": "Jane Doe", "phone": "010-0000-0000" },
  "currentPassword": "old-password",
  "newPassword": "new-password"
}
```

Send only `profile`, or only the password fields. Changing the password needs `currentPassword` and signs every session out.

### DELETE /me

Withdrawal. The member's data is deleted immediately and `{ "success": true }` is returned.

A withdrawn email cannot sign up to the same Project again for 90 days. During that time only an encrypted value of the email is kept, and it is deleted automatically afterwards.

### GET /me/consents, PUT /me/consents

```json
{ "consents": [{ "type": "marketing", "version": "2026-11", "agreed": false }] }
```

`PUT` **appends** an agreement or a withdrawal to the history; earlier rows are never overwritten. Both endpoints return the history, newest first.

```json
{
  "consents": [
    { "type": "marketing", "version": "2026-11", "agreed": false, "agreedAt": "2026-11-12T04:00:00.000Z" },
    { "type": "terms", "version": "2026-11", "agreed": true, "agreedAt": "2026-11-12T03:00:00.000Z" }
  ]
}
```

---

## Table permission rules and the content API

Once a Table has [permission rules](/docs/core-features/members), the content API can be called with a publishable key plus a member token.

```bash
curl -X POST https://api.onttle.com/v1/{projectId}/tables/posts \
  -H "X-Onttle-Key: $ONTTLE_PUBLISHABLE_KEY" \
  -H "Authorization: Bearer $ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{ "title": "First post", "is_publish": true }'
```

| Rule          | No member token | Member without permission                         |
| ------------- | --------------- | ------------------------------------------------- |
| `public`      | Allowed         | Allowed                                           |
| `member`      | `401`           | —                                                 |
| `owner`       | `401`           | `403 AUTH_FORBIDDEN` (lists return own rows only) |
| `role:<name>` | `401`           | `403 AUTH_FORBIDDEN`                              |
| `none`        | `403`           | `403`                                             |

---

## Member file uploads

When the Project's **File upload permission** is "Signed-in members" or "A role", files can be uploaded with a member token. The default is secret key only.

```bash
curl -X POST https://api.onttle.com/v1/{projectId}/files \
  -H "X-Onttle-Key: $ONTTLE_PUBLISHABLE_KEY" \
  -H "Authorization: Bearer $ACCESS_TOKEN" \
  -F "file=@./photo.jpg"
```

The response is the same file object as for a secret-key upload. Use the returned `id` as the value of a FILE column.

| Situation                                   | Response                     |
| ------------------------------------------- | ---------------------------- |
| The permission is "Secret key only"         | `403`                        |
| No member token                             | `401 AUTH_TOKEN_INVALID`     |
| The member's role does not match            | `403 AUTH_FORBIDDEN`         |
| The file exceeds the member upload limit    | `400 AUTH_INVALID_REQUEST`   |
| More than 30 uploads in 10 minutes          | `429 AUTH_RATE_LIMITED`      |

`is_ocr` and `is_generate` are ignored on member uploads, and the file is deleted when the member withdraws.

---

## Error codes

Errors of the member API have this shape:

```json
{ "code": "AUTH_INVALID_CREDENTIALS", "message": "The e-mail or password is incorrect." }
```

| Code                          | HTTP | When                                                          |
| ----------------------------- | ---- | ------------------------------------------------------------- |
| `AUTH_INVALID_REQUEST`        | 400  | Invalid request body (`message` names the field)              |
| `AUTH_INVALID_CREDENTIALS`    | 401  | Wrong email or password (it does not say which)               |
| `AUTH_TOKEN_EXPIRED`          | 401  | Token expired                                                 |
| `AUTH_TOKEN_INVALID`          | 401  | Token missing, forged or reused                               |
| `AUTH_SIGNUP_PAUSED`          | 402  | Out of credits, or the monthly sign-up cap was reached        |
| `AUTH_EMAIL_NOT_VERIFIED`     | 403  | Login before email verification                               |
| `AUTH_MEMBER_SUSPENDED`       | 403  | The member is suspended                                       |
| `AUTH_FORBIDDEN`              | 403  | Permission rule violation, failed CSRF check, key not allowed |
| `AUTH_NOT_ENABLED`            | 403  | The member feature is off for the Project                     |
| `AUTH_EMAIL_EXISTS`           | 409  | The email is already registered                               |
| `AUTH_EMAIL_WITHDRAWN`        | 409  | Re-sign-up within 90 days of withdrawal                       |
| `AUTH_RATE_LIMITED`           | 429  | Too many requests                                             |
| `AUTH_PROVIDER_NOT_SUPPORTED` | 501  | Social login is not available yet                             |

On `AUTH_TOKEN_EXPIRED`, refresh the tokens with `/auth/token/refresh` and retry the request. If the refresh fails too, the member has to log in again.

---

## Next steps

- [Members (Sign-up & Login)](/docs/core-features/members) — settings, permission rules and cookie mode.
- [OpenAPI Spec](/docs/developers/openapi) — the spec of a Project with the member feature on includes the member endpoints and permission rules.
- [Authentication](/docs/rest-api/authentication) — issuing API Keys and key types.
