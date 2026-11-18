---
title: 'Members (Sign-up & Login)'
description: 'How to turn on the member feature so the end users of your service can sign up and log in with email, and how to set table permission rules and cookie mode.'
category: 'core-features'
slug: 'members'
locale: 'en'
apiVersion: 'v1'
order: 10
updatedAt: '2026-11-13'
---

# Members (Sign-up & Login)

With the member feature on, the service you build with an Onttle Project gets **email sign-up and login**. Sign-up, email verification, login, token refresh, password reset and withdrawal are all handled by the Onttle API, so you do not need an auth server of your own.

A **member** here is an end user of your service. Members are not the [Workspace and Project members](/docs/getting-started/workspaces-and-members) who share the Onttle dashboard with you.

| Item               | Details                                                                                  |
| ------------------ | ---------------------------------------------------------------------------------------- |
| Login method       | Email + password (social login comes in a later version)                                 |
| Settings           | Auth settings and permission rules: Project OWNER or ADMIN                               |
| Management         | Viewing, suspending and withdrawing members: Project EDITOR and above. Adding members directly: OWNER / ADMIN |
| Billing            | Credits are deducted for **each sign-up that completes email verification**. Nothing else is charged |
| Isolation          | Members belong to one Project. The same email is a separate member in each Project       |

---

## Turning the member feature on

1. Open **End users** in the Project's left menu.
2. On the **Auth settings** tab, switch **Enable the member feature** on.
3. The first time, read and agree to the personal data processing notice. With the feature on, Onttle becomes a processor of personal data on your behalf, so your privacy policy must reflect the outsourcing and the cross-border transfer.
4. Fill in the settings below and **Save**.

| Setting                         | Description                                                                                             |
| ------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Allowed origins                 | The addresses of your service (`https://www.example.com`). One per line; wildcards are not allowed      |
| Email verification redirect URL | Where the button in the verification email leads. Must be inside an allowed origin; `?token=…` is appended |
| Password reset redirect URL     | Where the button in the password reset email leads. Same rule                                           |
| Sender name (optional)          | The sender name of verification and reset emails. Empty uses the Project name (up to 40 characters)     |
| Reply-To (optional)             | The address that receives replies to auth emails                                                        |
| Access token lifetime           | 5–60 minutes (default 15)                                                                               |
| Refresh token lifetime          | 1–365 days (default 30)                                                                                 |
| Monthly sign-up cap (optional)  | Verified sign-ups allowed per month. Only new sign-ups stop when it is reached                          |
| Member roles                    | The roles members can be given. Just `member` by default; see "Member roles" below                     |
| File upload permission          | Which members may upload files with a publishable key: Secret key only (default) / Signed-in members / A role. See "Member file uploads" below |
| Token mode                      | Bearer only (default) / Bearer + cookie / Cookie only. See "Cookie mode" below                          |

Setting changes are recorded in the [audit log](/docs/developers/audit-log).

---

## From sign-up to login

The member API is called directly from a browser or app with a **publishable key** (`pk-proj_…`) in the `X-Onttle-Key` header. Every endpoint with its request and response is in [Members API](/docs/rest-api/members-api).

```text
1. POST /auth/signup         sign-up → the member is "pending", a verification email is sent
2. (member) clicks the button → lands on <redirect URL>?token=…
3. POST /auth/email/verify   confirms the token → the member is "active"; credits are deducted here
4. POST /auth/login          returns accessToken + refreshToken
5. GET  /me                  Authorization: Bearer <accessToken>
6. POST /auth/token/refresh  new tokens when the access token expires
```

```typescript
const API = `https://api.onttle.com/v1/${projectId}`
const headers = {
  'X-Onttle-Key': process.env.NEXT_PUBLIC_ONTTLE_PUBLISHABLE_KEY!,
  'Content-Type': 'application/json',
}

// Sign up
await fetch(`${API}/auth/signup`, {
  method: 'POST',
  headers,
  body: JSON.stringify({
    email: 'user@example.com',
    password: 'at-least-8-chars',
    profile: { name: 'Jane Doe' },
    consents: [
      { type: 'terms', version: '2026-11', agreed: true },
      { type: 'privacy', version: '2026-11', agreed: true },
    ],
  }),
})

// On the redirect URL page: finish verification with the token of the email link
await fetch(`${API}/auth/email/verify`, {
  method: 'POST',
  headers,
  body: JSON.stringify({ token: new URLSearchParams(location.search).get('token') }),
})

// Log in
const session = await fetch(`${API}/auth/login`, {
  method: 'POST',
  headers,
  body: JSON.stringify({ email: 'user@example.com', password: 'at-least-8-chars' }),
}).then((res) => res.json())

// Call as the signed-in member
const me = await fetch(`${API}/me`, {
  headers: { ...headers, Authorization: `Bearer ${session.accessToken}` },
}).then((res) => res.json())
```

### Good to know

- **Auth emails** are sent by Onttle. The sender name is the **Sender name** of the auth settings (the Project name when empty). It cannot contain line breaks, angle brackets, quotes and similar characters, or words that could look like impersonation (support desks, banks, large platforms). If your custom theme has a logo, it is shown at the top of the email. Verification links are valid for 24 hours and password reset links for 30 minutes; each works once.
- **Resend limits:** one email per minute and five per day to the same address.
- **Refresh tokens are single use.** Every refresh returns a new token and revokes the previous one. If a used token comes back, it is treated as stolen and every session of that login is ended. Always store the new token for the next refresh.
- **Changing or resetting the password** signs the member out everywhere.
- **Login errors and password reset requests never reveal whether an email is registered.** A wrong email and a wrong password get the same error.
- **Profile:** `profile` holds whatever your service needs (name, phone …), up to 8 KB.

---

## Withdrawal and the 90-day re-sign-up block

When a member withdraws (`DELETE /me`) or an admin withdraws them, the member's data is **deleted immediately**.

> The email of a withdrawn member is kept in encrypted form for 90 days and then deleted automatically. During that time the same email cannot sign up again.

- Only an irreversible value (an HMAC) is kept, not the address itself.
- Signing up with the same email within 90 days returns `409 AUTH_EMAIL_WITHDRAWN`. Tell your users about this on the sign-up screen.
- The block applies per Project.
- An admin can lift it for one email under **End users → Members → Lift re-signup block**.
- Content created by a withdrawn member is kept; only its owner is cleared.
- Files **uploaded** by a withdrawn member are deleted with the account.

---

## Table permission rules

With members, each Table can say **who may do what**. In the screen where you create or edit a Table (the table form), **Permission rules** sits below the **API endpoints** switches: pick one rule each for read, create, update and delete, then save. **Set permission rules** in the "Member access" note of an API endpoint page opens the same table form. Project OWNERs and ADMINs can change them.

| Rule              | Meaning                                              | When not allowed      |
| ----------------- | ---------------------------------------------------- | --------------------- |
| Public            | Allowed with just a publishable key                  | —                     |
| Signed-in members | Allowed for any signed-in member                     | `401`                 |
| Owner only        | Allowed only for the member who created the content  | `403 AUTH_FORBIDDEN`  |
| A role            | Allowed only for members with that role (`role:admin`) | `403 AUTH_FORBIDDEN` |
| Secret key only   | Cannot be called with a publishable key              | `403`                 |

- **The defaults are the previous behavior:** read is public; create, update and delete are secret key only. A Table whose rules you never change behaves exactly as before.
- **Requests with a secret key (`sk-proj_…`) are always allowed,** whatever the rules.
- **The server decides the owner.** Content created under "Signed-in members" or a stricter rule is owned by that member. The request body cannot change it. Only a secret key can set an owner, with `owner_member_id`.
- **Lists under "Owner only"** do not fail: they return only the member's own content, and `pagination.total` counts only that.
- Content created by a member also needs `is_publish: true` to appear in read APIs.
- The roles "A role" can pick from are the **Member roles** of the auth settings. A member's role is changed under **End users → Members**.

Example: a board Table with "read = Public, create = Signed-in members, update / delete = Owner only" lets anyone read, members write, and each member edit or delete only their own posts.

```typescript
// Create a post as the signed-in member (rule: create = Signed-in members)
await fetch(`${API}/tables/posts`, {
  method: 'POST',
  headers: { ...headers, Authorization: `Bearer ${session.accessToken}` },
  body: JSON.stringify({ title: 'First post', body: '…', is_publish: true }),
})
```

---

## Member roles

Roles are a list of names defined per Project. Project OWNERs and ADMINs add and remove them under **End users → Auth settings → Member roles**.

- The default is just `member`. **`member` cannot be removed,** and every member starts as `member`.
- A role name starts with a lower-case letter and uses lower-case letters, digits, `_` and `-` only (e.g. `seller`, `vip`). Up to 20.
- Member role changes, "A role" in table permission rules and "A role" in the file upload permission all pick from this list. A role that is not in the list cannot be used.

**When a role is removed**

- Members with that role **automatically become `member`.** They are not withdrawn or suspended.
- Table permission rules and the file upload permission that used the role change to "Secret key only".
- Removing a role opens a confirmation that shows how many members and rules are affected; it is applied **when the auth settings are saved**. It cannot be undone after saving, and creating a role with the same name again does not restore the members or the rules.

---

## Member file uploads

The file upload API (`POST /files`) is secret key only by default. Change **File upload permission** in the auth settings and signed-in members can upload directly with a publishable key — for profile pictures or post attachments.

| Permission        | Meaning                                                  |
| ----------------- | -------------------------------------------------------- |
| Secret key only   | No uploads with a publishable key (default)              |
| Signed-in members | Any signed-in member may upload                          |
| A role            | Only members with that role (`role:seller`) may upload   |

```typescript
const form = new FormData()
form.append('file', file)

const uploaded = await fetch(`${API}/files`, {
  method: 'POST',
  headers: {
    'X-Onttle-Key': process.env.NEXT_PUBLIC_ONTTLE_PUBLISHABLE_KEY!,
    Authorization: `Bearer ${session.accessToken}`,
  },
  body: form,
}).then((res) => res.json())

// Use the returned file id as the value of a FILE column
await fetch(`${API}/tables/posts`, {
  method: 'POST',
  headers: { ...headers, Authorization: `Bearer ${session.accessToken}` },
  body: JSON.stringify({ title: 'A photo', image: uploaded.id, is_publish: true }),
})
```

- **Size limit:** set the largest file a member may upload (default 10 MB, up to 500 MB). The plan's file size and storage limits still apply.
- **Uploads without a signed-in member are never allowed,** because uploads use storage and credits.
- The OCR and AI keyword options (`is_ocr`, `is_generate`) are ignored on member uploads.
- One member can upload a limited number of files in a short time (30 per 10 minutes); beyond that the API returns `429 AUTH_RATE_LIMITED`.
- Files uploaded by a member are **deleted when that member withdraws**.
- Uploads are billed by the usual [file upload metrics](/docs/usage-and-billing/metric-types).
- Uploads with a secret key work exactly as before, whatever the setting.

---

## Cookie mode

In the default **Bearer mode** tokens come in the response body and your app stores them. In **cookie mode** tokens are sent as `httpOnly` cookies instead, so JavaScript cannot read them.

Cookie mode is available to Workspaces with a connected [custom domain](/docs/core-features/custom-domains): browsers only keep the cookie when it belongs to the same site as your service.

| Setting       | Description                                                                                                         |
| ------------- | ------------------------------------------------------------------------------------------------------------------- |
| Token mode    | **Bearer + cookie:** cookies only for logins with `?mode=cookie`. **Cookie only:** Bearer tokens are refused (no mobile apps) |
| Cookie domain | One of the connected custom domains (e.g. `cms.onttle.com`). It cannot be typed in                                  |
| SameSite      | Lax (default) / Strict / None. None does not work in browsers that block third-party cookies                        |

In cookie mode, call the API on the **cookie domain**, not on `api.onttle.com`.

```typescript
const API = `https://cms.onttle.com/v1/${projectId}` // the cookie domain

// Login: tokens arrive as cookies; the body carries csrfToken
const { csrfToken } = await fetch(`${API}/auth/login?mode=cookie`, {
  method: 'POST',
  credentials: 'include',
  headers,
  body: JSON.stringify({ email, password, remember: true }),
}).then((res) => res.json())

// Writes: send csrfToken in a header
await fetch(`${API}/me`, {
  method: 'PATCH',
  credentials: 'include',
  headers: { ...headers, 'X-CSRF-Token': csrfToken },
  body: JSON.stringify({ profile: { name: 'Jane Doe' } }),
})
```

- **CSRF protection is always on.** Cookie-authenticated writes (POST, PUT, PATCH, DELETE) must come from an allowed origin and carry the `X-CSRF-Token` header. Reads (GET) do not need it.
- After a page load, call `POST /auth/token/refresh` with an empty body to get a fresh `csrfToken`.
- At least one allowed origin must be on the same site as the cookie domain (e.g. `www.onttle.com` and `cms.onttle.com`).
- A login with `remember: false` gets session cookies that end when the browser closes.
- **Changing the token mode can sign members out.** Switching to "Cookie only", turning cookie mode off or changing the cookie domain ends every member session. The number of members currently signed in is shown before you save.
- A local development site (`localhost`) is not on the same site as the cookie domain: develop in Bearer mode.

With a framework that has a server, such as Next.js, you can also skip cookie mode and let **your server receive the tokens and store them in a cookie of its own domain** (the BFF pattern), calling the Onttle API from the server in Bearer mode.

---

## Managing members

**End users → Members** lists the members who signed up.

| Action                 | Description                                                                                   |
| ---------------------- | --------------------------------------------------------------------------------------------- |
| Add member             | An OWNER or ADMIN registers a member at once with an email and a password. See "Adding a member directly" below |
| Search                 | By **full** email address. Emails are stored encrypted, so a partial address cannot be searched |
| Change role            | The role name used by the "A role" permission rule                                            |
| Suspend / reactivate   | A suspended member is signed out on every device at once and cannot log in                    |
| Force withdrawal       | Deletes the member's data immediately. The 90-day block applies as usual                      |
| Lift re-signup block  | Removes the 90-day block of a withdrawn email                                                  |

Members are managed in the dashboard. The REST API has no endpoints for listing members or changing roles. With the [MCP server](/docs/ai/mcp) connected through an account sign-in, an AI client can list members, change roles, suspend or reactivate members and add members directly. Forced withdrawal is done in the dashboard only.

### Adding a member directly

Project OWNERs and ADMINs can register a member at once with the **Add member** button: an email, a password and a role.

- **No verification email is sent to a member added this way, and the email is treated as verified.** Before registering you have to turn on the "Treat this email as verified" switch as a second confirmation. Turn it on after checking that the address is correct.
- The member is active immediately and can log in right away. Pass the password on yourself; the member can change it after logging in.
- It counts as one sign-up: **credits are deducted**, and the monthly sign-up cap and the 90-day re-sign-up block apply as usual.
- Who added the member and when is recorded in the [audit log](/docs/developers/audit-log).

---

## Billing

| Action                                          | Credits                  |
| ----------------------------------------------- | ------------------------ |
| Sign-up (when email verification completes)     | Deducted per sign-up     |
| Member added directly in the dashboard          | Deducted per member      |
| Login, token refresh, logout, failed logins     | None                     |
| Verification and password reset emails          | None                     |
| Withdrawal                                      | None                     |

- A sign-up that is never verified is not charged. Clicking the same verification link several times charges once.
- When credits run out or the monthly sign-up cap is reached, **only new sign-ups** stop, with `402 AUTH_SIGNUP_PAUSED`. Existing members can still log in, refresh and withdraw.
- Content API calls made with a member token count toward the usual [API call metrics](/docs/usage-and-billing/metric-types).
- Sign-up counts are under "Member signups" on the **Usage** page.

---

## Security

- Passwords are hashed with argon2id. The minimum length is 8.
- Emails are stored encrypted; lookups use a hash made with a per-Project key.
- Refresh tokens and email link tokens are never stored in plain form.
- Sign-up, login and email sending are rate limited by IP and email. Beyond the limit the API returns `429 AUTH_RATE_LIMITED`.

---

## Next steps

- [Members API](/docs/rest-api/members-api) — requests, responses and error codes per endpoint.
- [Authentication](/docs/rest-api/authentication) — the `X-Onttle-Key` header and key types.
- [Custom domains](/docs/core-features/custom-domains) — connect the domain cookie mode needs.
