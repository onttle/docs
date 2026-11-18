---
title: 'Security'
description: 'What the Workspace Security menu is for, the expired / unused API key status, IP allow / block lists and allowed email domains (Pro+), and who can access it.'
category: 'developers'
slug: 'security'
locale: 'en'
apiVersion: 'v1'
order: 5
updatedAt: '2026-11-12'
---

# Security

**Workspace → Security** gathers the Workspace's security features in one place: an **API key status** overview of expired or long-unused keys, and **IP allow / block lists** and **allowed email domains** on Pro+ and above. The [audit log](/docs/developers/audit-log) in the same menu records who changed what, when, and from where.

By the end of this guide, you will know how to find API keys that need attention and how to restrict API requests and member invitations.

---

## Who can see it

The Security menu is available to Workspace **Owners** and **Admins** only. Editors and Guests see it disabled in the sidebar, and direct API requests are rejected.

---

## API key status

The top of the Security page gathers the API keys across **every project** of the Workspace that need attention. It is available on every plan.

| Status | Condition |
| ------ | --------- |
| Expired | The expiry has passed and requests are rejected |
| Expires in N days | Expires within 7 days |
| Unused for N days | Enabled but not used for 90+ days |

**Manage keys** opens the project's API Keys page, where you can extend the expiry or delete / disable the key. See [REST API Authentication](/docs/rest-api/authentication) for how to set an expiry.

---

## IP allow / block lists

On **Pro+ and above**, restrict requests made with this Workspace's API keys by client IP. Dashboard access is not affected.

| List | Behaviour |
| ---- | --------- |
| Block list (blacklist) | Requests from listed IPs are always rejected. Applied before the allow list. |
| Allow list (whitelist) | When not empty, only requests from listed IPs are accepted; everything else is rejected. |

- One IPv4 / IPv6 address or CIDR range per line (`203.0.113.9`, `10.0.0.0/8`, `2001:db8::/32`), up to 100 per list.
- Rejected requests receive `403 ipNotAllowed`. Saved lists apply to the API within a minute.
- With an allow list, make sure every server, CI runner and partner with a fixed IP is listed. Requests whose IP cannot be determined are rejected too.
- After a downgrade below Pro+ the lists are kept but not applied; they apply again after an upgrade.

---

## Allowed email domains

On **Pro+ and above**, only addresses on the listed domains can be invited to this Workspace. Useful for teams that want company accounts only.

- One domain per line (`example.com`). Domains must match exactly; list subdomains separately. Up to 50 domains.
- Inviting an address on another domain fails with `emailDomainNotAllowed`. When several addresses are invited at once, one disallowed address rejects the whole batch.
- Existing members and pending invitations are not affected.

Changes to both policies are recorded in the audit log under the `Security policy` category.

---

## Next Steps

- [Audit Log](/docs/developers/audit-log) — See which activities are recorded, how to filter and export them, and retention per plan.
- [Workspaces and Members](/docs/getting-started/workspaces-and-members) — Review role permissions and member management.
- [REST API Authentication](/docs/rest-api/authentication) — Compare Publishable and Secret keys and Origin restrictions.
