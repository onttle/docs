---
title: 'Authentication'
description: 'How to issue API Keys, authenticate REST API requests, and follow security best practices.'
category: 'rest-api'
slug: 'authentication'
locale: 'en'
apiVersion: 'v1'
order: 2
popular: true
updatedAt: '2026-11-12'
---

# Authentication

The Onttle REST API uses **Bearer Token** authentication. You must include the API Key issued from your Project in the `Authorization` header of every request.

---

## What is an API Key?

An API Key is the credential that allows an external application to access data in a specific Project.

- **Project scope:** An API Key belongs to a specific Project and can only access that Project's data.
- **Key type:** A **publishable** key (`pk-proj_…`) is read-only; a **secret** key (`sk-proj_…`) can also write.
- **Allowed hosts:** Optionally restrict requests to specific domains or IP addresses (CIDR ranges included).

---

## Issuing an API Key

1. Open the **Project** in the dashboard where you want to issue a key.
2. Click **API Keys** in the left menu.
3. Click **New API Key**.
4. Enter a name (e.g., `production-read`).
5. Select the key type.
   - **Publishable (`pk-proj_…`):** Allows GET requests only. Safe for browsers and public websites, ideally together with allowed hosts.
   - **Secret (`sk-proj_…`):** Also allows POST, PUT, PATCH, and DELETE requests, file uploads, chat and embeddings. Server-side only.
6. Optionally enter allowed hosts, one per line: domains (`https://example.com`, `*.example.com`) or IPs (`203.0.113.10`, `203.0.113.0/24`). A request is accepted when any entry matches. Use an IP for calls made from a server.
7. Choose an expiry: never, in 30 / 90 / 180 days or 1 year, or an exact date.
8. Click **Create**.

> **[IMAGE AREA]** API Key creation screen (name, permission, allowed hosts input UI)

The full API Key value is only shown once immediately after creation. Copy it before closing the window: Onttle stores only a hash of the key, so it cannot be displayed again. The list shows the key's prefix and last 4 characters (`sk-proj_abcd…wxyz`). The key type cannot be changed later; issue a new key instead.

---

## Using the Authorization Header

Include the following header in every API request.

```
Authorization: Bearer {API_KEY}
```

**curl Example**

```bash
curl https://api.onttle.com/v1/{projectId}/tables/posts/contents \
  -H "Authorization: Bearer $ONTTLE_API_KEY"
```

**TypeScript Example**

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

## The X-Onttle-Key Header

The API Key can also be sent in the **`X-Onttle-Key` header** instead of `Authorization`.

```bash
curl https://api.onttle.com/v1/{projectId}/tables/posts \
  -H "X-Onttle-Key: $ONTTLE_API_KEY"
```

The [member feature](/docs/core-features/members) needs this header, because `Authorization` then carries the access token of the signed-in member.

```bash
curl https://api.onttle.com/v1/{projectId}/me \
  -H "X-Onttle-Key: $ONTTLE_PUBLISHABLE_KEY" \
  -H "Authorization: Bearer $MEMBER_ACCESS_TOKEN"
```

| Header          | Value                  | Used for                                          |
| --------------- | ---------------------- | ------------------------------------------------- |
| `X-Onttle-Key`  | API Key                | Project authentication (every endpoint)           |
| `Authorization` | `Bearer {API Key}`     | Project authentication (original form, still supported) |
| `Authorization` | `Bearer {accessToken}` | The signed-in member (together with `X-Onttle-Key`) |

---

## Managing Keys with Environment Variables

Never hard-code API Keys in source code. Manage them as environment variables.

```bash
# .env.local (Next.js)
ONTTLE_API_KEY=your_api_key_here
ONTTLE_PROJECT_ID=your_project_id
```

Server-side environment variables are not included in the client bundle. If you need to call the API from the browser, route requests through a server-side handler.

---

## Publishable vs. Secret Keys

| Operation                                               | Publishable (`pk-proj_…`) | Secret (`sk-proj_…`) |
| ------------------------------------------------------- | ------------------------- | -------------------- |
| GET requests                                            | Yes                       | Yes                  |
| POST / PUT / PATCH / DELETE                             | No                        | Yes                  |
| File uploads, chat messages, embeddings search          | No                        | Yes                  |
| Member API (`/auth/*`, `/me`): POST / PATCH / PUT / DELETE | Yes                    | Yes                  |
| Content create / update / delete allowed by a Table's permission rule | Yes (whether a member token is needed depends on the rule) | Yes |
| File uploads by members the file upload permission allows | Yes (member token required) | Yes |

For public-facing websites that only display content, use a publishable Key. Secret Keys should only be used in server environments.

A publishable Key may write in three cases only: the [member API](/docs/rest-api/members-api) (sign-up, login …), content writes that a Table's [permission rule](/docs/core-features/members) allows, and file uploads by members that the file upload permission allows. The defaults are all "Secret key only", so a Project whose settings were never changed still cannot be written with a publishable Key.

---

## Security Best Practices

**Never do the following**

- Hard-code an API Key in source code
- Commit an API Key to Git
- Include a secret Key (`sk-proj_…`) in browser client code
- Log API Keys in response logs

**Recommended practices**

- Manage Keys as environment variables and add `.env.local` to `.gitignore`
- Issue separate API Keys per service (separate production and development)
- Perform write operations only from server-side API Routes
- If exposure is suspected, immediately delete the Key and issue a new one

---

## Expiry and Unused-Key Warnings

**Expiry**

You can set an expiry when creating or editing a key. After it passes, every request made with the key is rejected with `401 apiKeyExpired`; the key stays in the list marked **Expired** so the cause is visible. To keep using it, edit the key to extend the expiry or issue a new key. An expiry is recommended for keys handed to outside collaborators or used in short campaigns.

**Warnings**

The API Keys list and **Workspace → Security → API key status** flag the following keys.

| Status | Condition |
| ------ | --------- |
| Expired | The expiry has passed |
| Expires in N days | Expires within 7 days |
| Unused for N days | Enabled but not used for 90+ days (never-used keys count from creation) |

An unused key is still a liability if it leaks, so delete or disable keys you no longer need.

---

## Rotating and Deleting Keys

**Disabling**

To temporarily block a Key, disable it from the API Keys list. Requests using a disabled Key will be rejected.

**Deleting**

Deleting a Key immediately rejects all requests using it. Deleted Keys cannot be recovered.

---

## Authentication Error Handling

| Error              | Cause                                                                                                  |
| ------------------ | ------------------------------------------------------------------------------------------------------ |
| `401 Unauthorized` | Missing Authorization header, incorrect Key value, or deleted Key                                      |
| `401 apiKeyExpired` | The Key's expiry has passed (extend it or issue a new Key)                                          |
| `403 Forbidden`    | Insufficient Key permissions (attempting write with a publishable Key), or the endpoint is disabled    |

---

## Next Steps

- [Error Codes](/docs/api-reference/error-codes) — Understand the cause and resolution for each error status code.
- [Members API](/docs/rest-api/members-api) — Sign-up and login for your end users, and how member tokens are used.
- [REST API Usage](/docs/rest-api/request-examples) — See complete request examples including the authentication header.
