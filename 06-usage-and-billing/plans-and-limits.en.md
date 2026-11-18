---
title: 'Plans and Limits'
description: 'Compare member, Project, and Table counts, storage, file upload limits, and features by plan, and learn what happens when you reach a limit.'
category: 'usage-and-billing'
slug: 'plans-and-limits'
locale: 'en'
apiVersion: 'v1'
order: 1
updatedAt: '2026-11-11'
---

# Plans and Limits

Onttle plans are subscribed **per Workspace**. Every plan includes **all Project features** — tables, translations, variables, categories, files, Vector, Agents, Webhooks, the REST API, and more. Plans differ in their limits and some additional features.

All prices **include 10% VAT**, and 1 credit is 1 KRW. See the [`/pricing`](/pricing) page for the live prices and per-metric rates.

---

## Plans

| Plan | Monthly price | Monthly credits | Best for |
| ---- | ------------- | --------------- | -------- |
| Free | ₩0 | 1,000 | Personal projects and simple experiments |
| Pro | ₩10,000 | 10,000 | Small teams that need API logs, custom domains and themes |
| Pro+ | ₩30,000 | 40,000 | Growing teams that need unlimited storage |
| Ultra | ₩100,000 | 150,000 | Teams that operate without limits on projects or members |
| Enterprise | Custom | Unlimited | Large teams that need custom limits, dedicated support, and tax invoices (contact sales) |

- Monthly credits are reset to the plan's quota on each billing date; leftover credits do not roll over. If you run short, you can buy add-on credits (1 credit = 1 KRW).
- See [Subscription and Credits](/docs/usage-and-billing/subscription-and-credits) for credit deduction and billing cycles.

---

## Limits by Plan

| Item | Free | Pro | Pro+ | Ultra | Enterprise |
| ---- | ---- | --- | ---- | ----- | ---------- |
| Members (including pending invitations) | 1 | 5 | 25 | Unlimited | Unlimited |
| Projects | 1 | 5 | 20 | Unlimited | Unlimited |
| Tables per Project | 10 | 50 | 200 | Unlimited | Unlimited |
| Max upload size per file | 10 MB | 100 MB | 500 MB | 2 GB | Custom |
| Table data storage | 100 MB | 1 GB | Unlimited | Unlimited | Unlimited |
| File storage | 1 GB | 20 GB | Unlimited | Unlimited | Unlimited |
| API rate limit (per api key, requests / s) | 5 | 20 | 50 | 100 | 100 |

- Storage is measured across the **entire Workspace**. Usage from all Projects in the Workspace is added together.
- The maximum size of a single file depends on the plan (see the table above).
- You can check your current storage usage in the Storage section of **Workspace → Billing**.

---

## Features by Plan

| Feature | Free | Pro | Pro+ | Ultra | Enterprise |
| ------- | :--: | :-: | :--: | :---: | :--------: |
| All Project features | ✓ | ✓ | ✓ | ✓ | ✓ |
| API request logs | | ✓ | ✓ | ✓ | ✓ |
| Audit log (retention) | | 7 days | 30 days | 1 year | Unlimited |
| IP allow / block lists | | | ✓ | ✓ | ✓ |
| Allowed email domains | | | ✓ | ✓ | ✓ |
| Content version history and rollback | | ✓ | ✓ | ✓ | ✓ |
| Content lock | | ✓ | ✓ | ✓ | ✓ |
| Custom domains (20 per Workspace) | | ✓ | ✓ | ✓ | ✓ |
| Custom theme | | ✓ | ✓ | ✓ | ✓ |
| Support | Community | Email | Email | Priority | Priority |

> On plans without API logs, request details are not stored. Even if you upgrade later, you cannot view logs for requests made before the upgrade.

---

## When You Reach a Limit

Actions that exceed a limit are not performed and return an error. Data you have already stored is kept as is.

| Situation | Error | HTTP status |
| --------- | ----- | ----------- |
| Project, Table, or member limit reached | `planProjectLimitReached`, etc. | 403 |
| File size or storage exceeded | `planFileSizeLimitExceeded`, etc. | 413 |

In the dashboard, an upgrade prompt appears next to features that have reached their limit. Resolve it in one of these ways.

- Clean up unused Projects, Tables, files, and invitations.
- Upgrade to a higher plan. Upgrades take effect immediately.

---

## When You Move to a Lower Plan

Even if a downgrade or cancellation moves you to a lower plan, **your data is not deleted**. However, if you exceed the new plan's limits, creating or uploading new items is restricted until you are back within the limits.

For example, if a Pro Workspace with 3 Projects moves to Free, you can keep using the existing 3 Projects, but you cannot create a new one.

---

## Enterprise

Pricing and limits for the Enterprise plan are set by contract.

- You are billed according to your contract instead of automatic card payments, and a tax invoice is issued.
- Usage is measured, but credits are not deducted. Add-on credits cannot be purchased.
- Limits such as members, Projects, and storage are set by contract.
- For on-premises installation, corporate SSO integration, and more, see the [Enterprise](/enterprise) page.

Plan changes are handled through your sales representative, not the dashboard. Leave a business inquiry on the [Contact](/contact) page.

---

## Next Steps

- [Subscription and Credits](/docs/usage-and-billing/subscription-and-credits) — Review billing cycles, credit deduction, and plan changes.
- [Metric Types](/docs/usage-and-billing/metric-types) — See which items use credits.
