---
title: 'Error Codes'
description: 'REST API error response structure and the cause and resolution for each HTTP status code.'
category: 'rest-api'
slug: 'error-codes'
locale: 'en'
apiVersion: 'v1'
order: 6
updatedAt: '2026-11-12'
---

# Error Codes

When an API request fails, a JSON response containing error information is returned along with an HTTP status code.

---

## Error Response Structure

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "The title field is required.",
    "details": [
      {
        "field": "title",
        "message": "This field is required."
      }
    ]
  }
}
```

| Field           | Type            | Description                                                                 |
| --------------- | --------------- | --------------------------------------------------------------------------- |
| `error.code`    | `string`        | A code identifying the error                                                |
| `error.message` | `string`        | A message describing the error                                              |
| `error.details` | `array \| null` | List of per-field validation errors (only included for validation failures) |

---

## HTTP Status Code Reference

### 400 Bad Request

The request format is invalid or a required parameter is missing.

**Causes**

- Malformed JSON body
- Missing required query parameter or request body field
- Unsupported value

**Resolution**

Verify that the `Content-Type: application/json` header is included. Validate that the request body is valid JSON, and compare parameter names and types against the reference.

---

### 401 Unauthorized

Authentication failed.

**Causes**

- Missing `Authorization` header
- Key provided without the `Bearer` prefix
- API Key value is incorrect or does not exist
- API Key has been deleted or disabled

**Resolution**

Confirm the header format is `Authorization: Bearer {API_KEY}`. Check that no leading or trailing whitespace was included when copying the Key.

```bash
# Correct format
curl -H "Authorization: Bearer abc123xyz"

# Incorrect format (missing Bearer)
curl -H "Authorization: abc123xyz"
```

---

### 403 Forbidden

Authentication succeeded but you do not have permission to perform the operation.

**Causes**

- Attempting a write request (POST, PUT, PATCH, DELETE), a file upload, or a chat or embeddings call with a publishable API Key (`pk-proj_…`)
- Request from a domain not in the allowed hosts list
- Attempting to access data from a different Project

**Resolution**

Check the API Key's type and allowed hosts. Publishable keys are read-only; if write access is needed, use a secret key (`sk-proj_…`) from your server. The response message says when a secret key is required:

```json
{
  "type": "forbidden",
  "title": "Forbidden",
  "message": "This endpoint requires a secret API key (sk-proj_…). Publishable keys (pk-proj_…) are read-only."
}
```

---

### 404 Not Found

The requested resource does not exist.

**Causes**

- An identifier such as `{projectId}`, `{tableId}`, or `{contentsId}` is incorrect
- The resource has already been deleted
- Typo in the path

**Resolution**

Copy each resource ID from the dashboard and compare against the request path. Check for typos in the path.

---

### 409 Conflict

A resource conflict occurred.

**Causes**

- Duplicate value entered in a `unique` column
- Attempted state transition not allowed from the current state

**Resolution**

Check for existing duplicate values, or fetch the current resource state before sending the request.

---

### 422 Unprocessable Entity

The request structure is correct but failed validation.

**Causes**

- Missing value in a `required` column
- Number column value exceeds `min`/`max` range
- Text column value violates `minText`/`maxText` length constraint

**Error Response Example**

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Validation failed.",
    "details": [
      {
        "field": "data.title",
        "message": "The title field is required."
      },
      {
        "field": "data.price",
        "message": "price must be 0 or greater."
      }
    ]
  }
}
```

**Resolution**

Check each item in the `error.details` array for the problematic field and reason, then correct the request data.

---

### 429 Too Many Requests

Too many requests were sent in a short period.

**Resolution**

Wait briefly before retrying. Check the `Retry-After` header in the response to determine the appropriate wait time.

---

### 500 Internal Server Error

An internal server error occurred.

**Resolution**

Retry the same request after a short wait. If the problem persists, contact the [support channel](/support) with the request ID (which may be included in the response headers).

---

## Member API Error Codes

The [member API](/docs/rest-api/members-api) (`/auth/*`, `/me`), Table permission rule and member file upload errors return only `code` and `message` instead of the structure above.

```json
{ "code": "AUTH_EMAIL_WITHDRAWN", "message": "This e-mail address was withdrawn and cannot sign up again for 90 days after withdrawal." }
```

| Code                       | HTTP | When                                                   |
| -------------------------- | ---- | ------------------------------------------------------ |
| `AUTH_INVALID_REQUEST`     | 400  | Invalid request body                                   |
| `AUTH_INVALID_CREDENTIALS` | 401  | Wrong email or password (it does not say which)        |
| `AUTH_TOKEN_EXPIRED`       | 401  | Token expired                                          |
| `AUTH_TOKEN_INVALID`       | 401  | Token missing, forged or reused                        |
| `AUTH_SIGNUP_PAUSED`       | 402  | Out of credits, or the monthly sign-up cap was reached |
| `AUTH_EMAIL_NOT_VERIFIED`  | 403  | Login before email verification                        |
| `AUTH_MEMBER_SUSPENDED`    | 403  | The member is suspended                                |
| `AUTH_FORBIDDEN`           | 403  | Permission rule violation, failed CSRF check           |
| `AUTH_NOT_ENABLED`         | 403  | The member feature is off for the Project              |
| `AUTH_EMAIL_EXISTS`        | 409  | The email is already registered                        |
| `AUTH_EMAIL_WITHDRAWN`     | 409  | Re-sign-up within 90 days of withdrawal                |
| `AUTH_RATE_LIMITED`        | 429  | Too many requests                                      |

**Resolution**

- `AUTH_TOKEN_EXPIRED`: refresh the tokens with `POST /auth/token/refresh`, then retry.
- `AUTH_TOKEN_INVALID`: log in again. Refresh tokens are single use, so store the new one after every refresh.
- `AUTH_SIGNUP_PAUSED`: check the Workspace's credits and the Project's monthly sign-up cap. Logins of existing members are not affected.
- `AUTH_EMAIL_WITHDRAWN`: the same email can sign up again 90 days after the withdrawal. An admin can lift the block in the dashboard.

---

## TypeScript Error Handling Example

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
      throw new Error('Unauthorized: Check your API Key.')
    case 403:
      throw new Error('Forbidden: Check your API Key permissions.')
    case 404:
      throw new Error('Resource not found.')
    case 422:
      console.error('Validation errors:', error.error.details)
      throw new Error('Check your request data.')
    default:
      throw new Error(`API error: ${res.status}`)
  }
}

const data = await res.json()
```

---

## Next Steps

- [Authentication](/docs/api-reference/authentication) — Learn the correct authentication method to prevent 401 and 403 errors.
- [REST API Usage](/docs/rest-api/request-examples) — See complete request examples including error handling.
