---
title: 'Audit Log'
description: 'Which activities the audit log records, how to filter and export it as CSV, how to turn it off, retention per plan, and who can access it.'
category: 'developers'
slug: 'audit-log'
locale: 'en'
apiVersion: 'v1'
order: 6
updatedAt: '2026-11-12'
---

# Audit Log

The **audit log** is a record of **who changed what, when, and from where** for changes that affect security — members, API keys, Projects, domains, and billing. You find it under **Workspace → Security**. Use it as your team grows, when outside collaborators join, and during security reviews or incident investigations.

By the end of this guide, you will know which activities are recorded, how to find and export them, and how long they are kept on each plan.

---

## Who can see it

The audit log lives in the [Security](/docs/developers/security) menu, so only Workspace **Owners** and **Admins** can see it.

The audit log is available **from the Pro plan**. On the Free plan nothing is recorded and the Security page shows an upgrade notice. Recording starts when the Workspace upgrades, and the [retention period](#retention) depends on the plan.

---

## What is recorded

Each entry has an **actor** (a member or the system), an **activity**, a **target**, the **IP address and browser information**, and activity-specific **details**. The actor's name and email are stored as they were at the time, so entries stay readable after a member renames their account or leaves.

| Category | Recorded activities |
| -------- | ------------------- |
| Workspace | Settings updated, deleted, audit log exported as CSV |
| Member | Invited, existing account added, joined via invitation, role changed, removed, left |
| Invitation | Invitation role changed, invitation cancelled |
| Project | Created, settings updated, deleted |
| Project member | Added, role changed, removed, left |
| API key | Created, updated, enabled, disabled, deleted |
| Domain | Custom domain added or removed, primary domain changed |
| Theme | Theme updated |
| Subscription | Plan changed, cancellation scheduled, cancellation reverted |
| Payment method | Added, default changed, removed |
| Security policy | IP allow / block lists updated, allowed email domains updated, audit log turned on / off |

Details only hold what is needed to understand the activity: the previous and new role of a role change, the type and prefix of an API key, the target plan of a plan change. **Secrets such as API key values, passwords, and card numbers are never recorded.**

> Edit history of the data itself — contents, translations, variables — lives in each Project's version history, not in the audit log. For public API calls, see [Usage and Logs](/docs/usage-and-billing/usage-and-logs).

---

## Turning the audit log off

The **Audit log** switch in the top-right corner of the audit log card turns recording off and on for the Workspace. Owner or Admin permission is required.

- While off, new activity is not recorded. Entries already recorded stay readable until the retention period ends.
- The moment it is turned off is recorded as `Audit log turned off`, and turning it back on as `Audit log turned on`, so gaps are visible.
- Turning it back on records from that moment; activity during the gap is not recovered.

---

## Finding entries

The list shows the newest entries first. Click a row to expand the action code, target ID, browser information (User-Agent), and details.

| Filter | Description |
| ------ | ----------- |
| Period | Last 24 hours, 7 days, 30 days, 90 days, or exact dates |
| Category | One of the categories in the table above |
| Actor | One of the current Workspace members |

Filters are kept in the page URL, so you can share a link to exactly what you are looking at. Use **Load more** to fetch older entries.

### CSV export

**Export CSV** downloads the entries matching the current filters, newest first, up to **5,000 rows**. The file contains the time (UTC), action code, actor, target, Project, IP, browser information, and details (JSON).

- The export itself is recorded as a `Workspace → Audit log exported` activity.
- Cells that could be read as formulas are neutralised so the file opens safely in spreadsheets.

---

## Retention

Entries older than the plan's retention period are deleted automatically every night (KST). The current plan's retention is shown at the top of the Security page.

| Plan | Retention |
| ---- | --------- |
| Free | Not available |
| Pro | 7 days |
| Pro+ | 30 days |
| Ultra | 1 year |
| Enterprise | Unlimited |

- Browsing and exporting only cover entries within the retention period. Even with exact dates selected, nothing before the retention start is returned.
- Upgrading keeps entries longer from that point on. Entries that were already deleted are not restored, so export CSVs regularly if you need long-term records.
- After a downgrade, entries beyond the new retention period are deleted at the next nightly run. Downgrading to Free stops recording and removes the remaining entries.

---

## Recommended practice

- **Review regularly**: pay particular attention to member role changes, Secret API key creation, and payment method changes.
- **When a collaboration ends**: after a collaborator leaves, filter by that member as the actor and review the changes they left behind.
- **Long-term records**: if policy requires more than a year of history, export a CSV each month and keep it in your own storage.

---

## Next Steps

- [Security](/docs/developers/security) — Review API key status, IP allow / block lists and allowed email domains.
- [Workspaces and Members](/docs/getting-started/workspaces-and-members) — Review role permissions and member management.
- [REST API Authentication](/docs/rest-api/authentication) — Compare Publishable and Secret keys and Origin restrictions.
- [Plans and Limits](/docs/usage-and-billing/plans-and-limits) — Compare audit log availability and retention per plan.
