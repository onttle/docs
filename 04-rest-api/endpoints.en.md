---
title: 'Endpoints'
description: 'Every endpoint of the Onttle REST API: table contents, AI, pre-defined APIs and the member API at a glance.'
category: 'rest-api'
slug: 'endpoints'
locale: 'en'
apiVersion: 'v1'
order: 1
popular: true
updatedAt: '2026-11-12'
---

# Endpoint List

Every endpoint is scoped to a Project. Append the path to this base URL:

```
https://api.onttle.com/v1/{projectId}
```

The Project's **API Endpoints** page shows the same list with per-Table examples, and lets you switch each endpoint on or off. A disabled endpoint returns `403`.

---

## Authentication

Every request needs an API Key, sent in one of two ways:

```
X-Onttle-Key: {API_KEY}
Authorization: Bearer {API_KEY}
```

| Key             | Prefix      | Requests it can make                                                  |
| --------------- | ----------- | --------------------------------------------------------------------- |
| Publishable key | `pk-proj_…` | Reads (GET), the member API, member writes / uploads allowed by rules |
| Secret key      | `sk-proj_…` | Every request. Servers only                                           |

To call as a signed-in member, send the API Key in `X-Onttle-Key` and the member's access token in `Authorization: Bearer`. See [Authentication](/docs/rest-api/authentication).

The **Key** column below says which key can call the endpoint:

- **Publishable**: publishable and secret keys
- **Secret**: secret keys only
- **Rule**: secret keys always; publishable keys when the Table's [permission rule](/docs/core-features/members) allows it (secret key only by default). Permission rules are set below **API endpoints** in the screen where you create or edit a Table.

---

## Table API

Read and change a Table's contents. `{table}` is the Table ID or slug; `{id}` is the content ID or slug.

| Method   | Path                         | Description               | Key                     |
| -------- | ---------------------------- | ------------------------- | ----------------------- |
| `GET`    | `/tables/{table}`            | List contents             | Publishable (read rule) |
| `GET`    | `/tables/{table}/{id}`       | Get one content           | Publishable (read rule) |
| `POST`   | `/tables/{table}`            | Create content            | Rule                    |
| `PUT`    | `/tables/{table}/{id}`       | Update content            | Rule                    |
| `DELETE` | `/tables/{table}/{id}`       | Delete content            | Rule                    |
| `POST`   | `/tables/{table}/embeddings` | Embeddings (semantic) search | Secret               |
| `POST`   | `/tables/{table}/bm25`       | Keyword (BM25) search     | Secret                  |

- Reads return **published** contents only.
- The read rule is "Public" by default. With "Signed-in members" or "Owner only" a member token is needed.
- With a create / update / delete rule of "Signed-in members", "Owner only" or "A role", **members can write contents directly with a publishable key.** Content a member creates is owned by that member.

See [Request Examples](/docs/rest-api/request-examples) for request and response formats, and [Embeddings Search](/docs/ai/embeddings) and [BM25 Search](/docs/ai/bm25) for search.

---

## AI API

Chat sessions with an Agent.

| Method | Path                       | Description                          | Key         |
| ------ | -------------------------- | ------------------------------------ | ----------- |
| `POST` | `/chats`                   | Start a chat session                 | Secret      |
| `POST` | `/chats/{chatId}/messages` | Send a message to a session          | Secret      |
| `GET`  | `/chats/{chatId}/messages` | Get the messages of a session        | Publishable |

See [Chats](/docs/ai/chats).

---

## Pre-defined API

Read Project resources and upload files.

| Method | Path            | Description          | Key                        |
| ------ | --------------- | -------------------- | -------------------------- |
| `GET`  | `/translations` | Get all translations | Publishable                |
| `GET`  | `/variables`    | Get all variables    | Publishable                |
| `GET`  | `/categories`   | Get all categories   | Publishable                |
| `POST` | `/files`        | Upload a file        | Secret (upload permission) |

- File upload is secret key only by default. Set **File upload permission** in the member auth settings to "Signed-in members" or "A role" and **members can upload files directly with a publishable key.** Files a member uploads are deleted when that member withdraws.

---

## Member API

Sign-up and login for the end users of your service. The [member feature](/docs/core-features/members) must be on for the Project, and the API Key goes in the `X-Onttle-Key` header. See [Members API](/docs/rest-api/members-api) for requests and responses.

### Auth

| Method | Path                           | Description                          | Key          |
| ------ | ------------------------------ | ------------------------------------ | ------------ |
| `POST` | `/auth/signup`                 | Sign up, send the verification email | Publishable  |
| `POST` | `/auth/email/verify`           | Complete email verification          | Publishable  |
| `POST` | `/auth/email/resend`           | Resend the verification email        | Publishable  |
| `POST` | `/auth/login`                  | Log in                               | Publishable  |
| `POST` | `/auth/token/refresh`          | Refresh tokens                       | Publishable  |
| `POST` | `/auth/logout`                 | Log out                              | Member token |
| `POST` | `/auth/password/reset-request` | Send a password reset email          | Publishable  |
| `POST` | `/auth/password/reset`         | Set a new password                   | Publishable  |

### Me

| Method   | Path           | Description                                           | Key          |
| -------- | -------------- | ----------------------------------------------------- | ------------ |
| `GET`    | `/me`          | Get my profile                                        | Member token |
| `PATCH`  | `/me`          | Update profile / password                             | Member token |
| `DELETE` | `/me`          | Withdraw (the email cannot sign up again for 90 days) | Member token |
| `GET`    | `/me/consents` | Get consent history                                   | Member token |
| `PUT`    | `/me/consents` | Record consent decisions                              | Member token |

"Member token" means the request carries `Authorization: Bearer {accessToken}` together with a publishable key.

Listing members, changing roles, suspending, forced withdrawal and adding members directly are done in the dashboard's **End users** menu, not through the API.

---

## Next Steps

- [Authentication](/docs/rest-api/authentication) — Issue API Keys and learn the headers.
- [Request Examples](/docs/rest-api/request-examples) — Request examples and response parsing.
- [Members API](/docs/rest-api/members-api) — Requests and responses of the member sign-up and login endpoints.
- [Error Codes](/docs/rest-api/error-codes) — Error causes and resolutions by HTTP status code.
