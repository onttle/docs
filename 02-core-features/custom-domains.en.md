---
title: 'Custom Domains'
description: 'How to serve a Workspace CMS on a customer domain such as cms.onttle.com, set up and verify DNS, and how login works on a custom domain.'
category: 'core-features'
slug: 'custom-domains'
locale: 'en'
apiVersion: 'v1'
order: 8
updatedAt: '2026-11-03'
---

# Custom Domains

With a custom domain, a Workspace's CMS is reached on **your customer's or your team's domain** (for example `cms.onttle.com`) instead of `app.onttle.com`. Agencies use it to hand a CMS to a client, and teams use it to run an internal service on their own domain. A connected domain also gets the Workspace's [custom theme](/docs/core-features/custom-themes), so the logo and colors change with it.

| Item       | Details                                                                      |
| ---------- | ---------------------------------------------------------------------------- |
| Plan       | **Pro and above** (on Free the menu is disabled and shows an upgrade notice) |
| Permission | Owner (OWNER) or Admin (ADMIN)                                               |
| Limit      | Up to **20** domains per Workspace                                           |
| Format     | **Subdomains only** (`cms.onttle.com` ✓, `acme.com` ✗)                       |

---

## Adding a Domain

1. Go to **Workspace → Domains**.
2. Enter the hostname and click **Add**. The value is lowercased; do not include `https://` or a path.
3. Create the **DNS records** shown on the screen at your DNS provider (Cloudflare, Route 53, GoDaddy, and so on).
4. Once DNS has propagated, Onttle verifies the domain automatically and the status becomes **Active**. To check right away, click **Check after DNS setup**.

### DNS Records

| Type  | Name                          | Value                         | Notes                                                  |
| ----- | ----------------------------- | ----------------------------- | ------------------------------------------------------ |
| CNAME | `cms.onttle.com`              | The value shown on the screen | Always required.                                       |
| TXT   | `_vercel.acme.com` or similar | The value shown on the screen | Shown only when domain ownership needs to be verified. |

- Use the **Copy** button on each row to copy the exact value. You do not need a trailing dot (`.`).
- Remove any existing A record or other CNAME with the same name. A CNAME cannot coexist with other records on the same name.
- On Cloudflare, turn the proxy (orange cloud) **off** for the record and leave it as DNS only.

### Verification Schedule

After a domain is added, Onttle checks it after 1 minute, 5 minutes, 15 minutes and 1 hour, then every 6 hours for up to **72 hours**. Active domains are re-checked once a day. DNS propagation usually takes a few minutes and up to 48 hours.

---

## Statuses

| Status          | Meaning                                                                                                                              |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| Waiting for DNS | The records have not propagated or have not been created yet. Check the records shown.                                               |
| Active          | Verification succeeded and the domain is live. The HTTPS certificate is issued automatically.                                        |
| Error           | The domain was not verified within 72 hours, or a check failed. Fix the records and click **Check after DNS setup** to verify again. |

The notice under the status means the following.

| Notice                                        | Cause and fix                                                                                          |
| --------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| The CNAME record does not point to Onttle yet | The CNAME is missing or has a different value. Create it with the value shown and check again shortly. |
| TXT record missing / does not match           | Create the ownership TXT record with the exact name and value shown.                                   |
| DNS is misconfigured                          | Look for conflicting records on the same name.                                                         |
| Not verified within 72 hours                  | Check the records, then restart with **Check after DNS setup**.                                        |
| The domain provider is unavailable            | Try again in a moment; if it persists, contact [support](/support).                                    |

---

## Primary Domain

When a Workspace has several domains, one of them is the **primary** domain. The first domain you add becomes primary automatically, and only active domains can be made primary.

---

## Behaviour on a Custom Domain

- A custom domain is **bound to its Workspace**. The Workspace list, Workspace creation and other Workspaces are not reachable; they redirect to this Workspace.
- An account that is not a member of the Workspace sees an access-denied screen after signing in.
- The login screen and the header show the Workspace's [custom theme](/docs/core-features/custom-themes) (logo, colors, title).

### Login

- **Email login** works on the custom domain directly.
- **Google and GitHub login** briefly go through `app.onttle.com` for authentication and then return to the custom domain, where you are signed in automatically. This must complete within one minute; after that the login screen asks you to try again.
- Sessions are **separate per domain**. Being signed in on `app.onttle.com` does not sign you in on the custom domain.
- **Links in emails** (password reset, sign-up confirmation) open on `app.onttle.com`. After completing them, sign in on the custom domain.

---

## Removing a Domain

Click **Remove** and the domain stops serving immediately. A removed hostname **cannot be used by another Workspace for 30 days**; the same Workspace can add it again right away.

---

## Hostnames That Cannot Be Added

| Input                                                                       | Result                                                                 |
| --------------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| `acme.com` (apex domain)                                                    | Only subdomains can be connected. Use something like `cms.onttle.com`. |
| Subdomains of `onttle.com`, `localhost`, hosting providers' default domains | Reserved hostnames.                                                    |
| IP addresses, hostnames with a port                                         | Not a valid hostname.                                                  |
| A hostname connected to another Workspace                                   | Already in use, including hostnames removed less than 30 days ago.     |
| A 21st domain                                                               | A Workspace can have up to 20 domains. Remove one you no longer use.   |
