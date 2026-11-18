---
title: 'Subscription and Credits'
description: 'Monthly subscription plans, billing cycles, monthly credit deduction and add-on credits, plan changes, and failed payments.'
category: 'usage-and-billing'
slug: 'subscription-and-credits'
locale: 'en'
apiVersion: 'v1'
order: 2
popular: true
updatedAt: '2026-11-09'
---

# Subscription and Credits

Onttle bills on a **monthly subscription plan plus credits**. Each Workspace subscribes to a plan, receives the plan's monthly credits at the start of every billing cycle, and service usage is aggregated into metrics and deducted from those credits at the per-metric rate.

After reading this document you will know when a billing cycle renews, how credits are deducted and what happens when they run out, and how plan changes, cancellations, and failed payments are handled.

> The **prepaid credit top-ups** from before the subscription model are no longer available. Auto recharge is offered as an automatic purchase of add-on credits (see [Auto Recharge](#auto-recharge)). See [Migration of existing Workspaces](#migration-of-existing-workspaces) for how older Workspaces were moved over.

---

## Plans and Monthly Credits

A plan is subscribed per Workspace and billed monthly. Each plan defines the following.

| Item            | Description                                                                                     |
| --------------- | ----------------------------------------------------------------------------------------------- |
| Monthly price   | Charged automatically to the default card on each billing date. The Free plan is not charged.   |
| Monthly credits | Credits granted when a billing cycle starts. Unlimited plans have no credit quota.               |
| Plan limits     | Seats, projects, tables per project, file upload size, storage, and so on                       |
| Features        | Whether request logs, version history, custom domains and themes are available, and the support level  |

All prices **include 10% VAT**. The price, monthly credits, and limits of each plan are listed in [Plans and Limits](/docs/usage-and-billing/plans-and-limits), on the [`/pricing`](/pricing) page, and under **Workspace → Billing** in the dashboard.

You choose a plan when creating a Workspace. A paid plan requires registering a card during creation and charges the first month immediately; the Free plan starts without a payment method.

> **Unlimited plans (such as Enterprise)**: usage is still metered for reporting, but no credits are deducted and requests are never rejected for insufficient credits. Add-on credits cannot be purchased.

---

## Billing Cycle

| Item             | Description                                                                                          |
| ---------------- | ---------------------------------------------------------------------------------------------------- |
| Billing day      | The day of the month you subscribed. If you subscribed on the 29th to 31st, it is fixed to the **28th**. |
| Billing time     | **09:00 KST** on each billing day                                                                     |
| Cycle range      | From this billing time until just before the next one                                                |
| On renewal       | Charge the paid plan → start a new cycle → grant the plan's monthly credits                          |

When a new cycle starts, the Workspace balance is **reset** to the plan's monthly credits. Credits left over from the previous cycle, including add-on credits, do not roll over and expire.

After a successful charge, Workspace owners and admins receive a confirmation email with the amount, billing period, and granted credits.

---

## Deduction Flow

```
Service usage
    ↓
Usage metric recorded (DATA_STORE_BYTES, API_READ_CALLS, ...)
    ↓
Per-metric cost (usage rounded up to whole units × credits per unit)
    ↓
Deducted from the Workspace credit balance
```

- Credits are deducted at the **Workspace level**. Every Project in the Workspace shares the same balance.
- Usage is rounded up to the metric's unit size and multiplied by the credits per unit.
- Requests that consume credits check the balance first. If the balance is short, the request is not executed. Webhooks also check the balance before delivery and are skipped when it is short.
- Usage is recorded after the work finishes, so when actual usage is larger than estimated at check time (for example a long AI answer), the balance can drop **below zero**. That deduction is not reversed.
- While the balance is negative, every request that consumes credits is rejected. The next monthly grant or an add-on purchase first offsets the negative balance, and the remainder becomes available.
- Unlimited plans (such as Enterprise) are metered only; no credits are deducted.

See [Metric Types](/docs/usage-and-billing/metric-types) for which metrics affect deduction, and the [`/pricing`](/pricing) page for per-metric rates and a monthly cost simulation.

---

## When Credits Run Out

If the balance is lower than the credits a request needs, the following happens.

| Item              | Description                                                                                                   |
| ----------------- | ------------------------------------------------------------------------------------------------------------- |
| Request           | Rejected with an `insufficientCredit` error (HTTP 400). Your data and settings are kept intact.               |
| Notification      | Workspace owners and admins receive an insufficient-credit email, at most once a day per Workspace.           |
| Automatic top-up  | With [auto recharge](#auto-recharge) on, add-on credits are bought with the default card before the request is rejected. If it is off or the charge fails, the balance is not refilled. |
| To continue       | Wait for the next billing cycle, purchase add-on credits, turn on auto recharge, or upgrade the plan.         |

```json
{
  "title": "Insufficient Credit",
  "message": "The workspace has insufficient credit for this billing cycle. Credits renew with the next cycle; you can also purchase add-on credits or upgrade the plan."
}
```

---

## Add-on Credits

When this cycle's credits are not enough, you can purchase add-on credits.

- Purchase them under **Workspace → Billing**. This requires the owner or admin role and a registered default payment method.
- Pick 1,000 / 3,000 / 5,000 / 10,000 / 30,000 credits or enter a custom amount, up to 100,000 credits per purchase.
- The default card is **charged immediately** and the credits are added to the balance right away. A purchase confirmation email is sent.
- Add-on credits are valid for the **current billing cycle only**. Any remaining add-on credits expire when the cycle ends.
- Not available on unlimited plans (`addonNotAvailable`).

Add-on credits cost the same as monthly credits.

| Currency | Conversion       |
| -------- | ---------------- |
| KRW      | 1 KRW = 1 Credit |

Only KRW billing is currently available, and the amount charged includes 10% VAT.

---

## Auto Recharge

With auto recharge turned on, add-on credits are bought automatically with the default card when the balance falls below a threshold, so work is not interrupted by a credit shortage.

Set it up under **Auto recharge** in **Workspace → Billing**. This requires the owner or admin role, and a payment method must be registered before it can be turned on.

| Setting                  | Description                                                                                                    |
| ------------------------ | -------------------------------------------------------------------------------------------------------------- |
| When balance falls below | Auto recharge starts when the balance falls below this value.                                                  |
| Buy credits              | The credits bought on each recharge.                                                                           |
| Monthly limit (optional) | The most credits bought automatically in a calendar month (1st to last day, KST). Leave it empty for no limit. |

- The balance is checked when a request that uses credits arrives. If it is below the threshold, or lower than the credits the request needs, the default card is charged, the credits are added to the balance, and the request is then processed. A confirmation email is sent.
- Pricing and validity are the same as add-on credits. Auto-recharged credits are valid for the **current billing cycle only** and expire when the cycle ends.
- Once the monthly limit is reached, auto recharge stops until the next month.
- If the charge fails, the same card is not tried again for 6 hours. Changing the default payment method makes it retry right away. Until then, requests the balance cannot cover are rejected with `insufficientCredit`.
- It does not run on Enterprise or unlimited plans, and it is paused while the subscription is past due.

---

## From December 1, 2026

Starting December 1, 2026 at 00:00 KST, the credit calculation for AI tokens and storage changes. Until then, the current rates apply as is.

| Item                                                | Today                                                    | From December 1, 2026                                                                                              |
| --------------------------------------------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| AI tokens (`AI_GENERATION_TOKENS`, `AI_ASK_TOKENS`) | 500 credits per 1M total tokens                          | 700 credits per 1M weighted tokens (input ×1, cached input ×0.1, cache write ×1.25, output including reasoning ×5) |
| Data storage                                        | 1,000 credits per 1 GB at write time (growth only, once) | 800 credits per GB-month of stored data, deducted daily (`DATA_STORE_GB_DAYS`)                                     |
| File storage                                        | 1,000 credits per 10 GB at upload (once)                 | 200 credits per GB-month of stored files, deducted daily (`FILE_STORAGE_GB_DAYS`)                                  |

- Storage fees are deducted automatically every day based on the bytes stored that day × days. `DATA_STORE_BYTES` and `FILE_UPLOAD_BYTES` are still recorded for statistics but no longer cost credits.
- See [Metric Types](/docs/usage-and-billing/metric-types#from-december-1-2026) for the per-weight rates and details.

---

## Changing Plans

| Change     | Takes effect        | Charge                                                                                                                                  |
| ---------- | ------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| Upgrade    | Immediately         | The prorated price difference for the rest of the current cycle is charged to the default card. The same share of the monthly credit difference is granted right away. |
| Downgrade  | Next billing date   | No charge. You keep the current plan and credits until the next billing date.                                                            |
| Cancel     | Next billing date   | No charge. The Workspace moves to the Free plan when the current cycle ends. No data is deleted.                                          |

- A scheduled downgrade or cancellation can be undone under **Workspace → Billing** before it takes effect. The current plan then renews as usual.
- A Workspace already on the Free plan cannot be cancelled (`nothingToCancel`).
- Plans cannot be changed while a payment is past due (`subscriptionPastDue`). Fix the payment method first.

---

## Payment Methods

- Register a card under **Workspace → Billing**. Payments go through KG Inicis (via PortOne); once a card is registered, renewals, upgrades, add-on purchases, and auto recharges are charged to it.
- Up to **3** cards can be registered per Workspace, and one of them is set as the default payment method.
- Only owners and admins can manage payment methods.

---

## Failed Payments and Past Due

When a renewal charge fails, it is handled in this order.

1. The subscription becomes **past due (PAST_DUE)** and owners and admins receive a payment-failed email.
2. The same invoice is retried **1 day** and then **3 days** after the original billing date.
3. If a retry succeeds, a new cycle starts and the monthly credits are granted.
4. If every retry fails, the Workspace moves to the **Free plan** for the new cycle and you are notified by email.

Plans cannot be changed while past due, so replace the card or change the default payment method and wait for the retry.

---

## Invoices and Payment History

- **Workspace → Invoices** lists an invoice for every charge: monthly renewals, prorated upgrades, add-on purchases, and auto recharges. Only owners and admins can view them.
- The invoice detail shows the line items, payment method, payment status, and a receipt link.
- The **Upcoming invoice** view previews the next billing date, the plan that will apply, and the amount due.
- **Workspace → Billing** shows this cycle's included credits, add-on credits, usage, remaining balance, and credit grant history. **Workspace → Usage** breaks usage down by metric and by project.

---

## Migration of Existing Workspaces

Workspaces created before the subscription model are migrated automatically the first time a billing feature is used.

- They are placed on the **Free plan** and receive its monthly credits.
- Any credit balance bought under the previous model is kept as **add-on credits** of the first billing cycle. Like other add-on credits, it expires when that first cycle ends.
- Upgrade to a paid plan or buy add-on credits when you need more. To use auto recharge, review its settings under **Workspace → Billing** (see [Auto Recharge](#auto-recharge)).

---

## Common Errors

| Error code                        | Status | Cause and resolution                                                                   |
| --------------------------------- | ------ | -------------------------------------------------------------------------------------- |
| `insufficientCredit`              | 400    | Not enough credits this cycle. Buy add-on credits, upgrade, or wait for the next cycle |
| `paymentMethodRequired`           | 422    | No default payment method. Register a card and retry                                   |
| `paymentFailed`                   | 402    | The card charge failed. Check the payment method and retry                              |
| `subscriptionPastDue`             | 409    | A renewal is past due. Fix the payment method before changing plans                     |
| `addonNotAvailable`               | 400    | Add-on credits cannot be purchased on an unlimited plan                                 |
| `nothingToCancel`                 | 400    | Already on the Free plan                                                                |
| `planProjectLimitReached`, etc.   | 403    | A plan limit was reached. Free up resources or upgrade                                  |
| `planFileSizeLimitExceeded`, etc. | 413    | File size or storage exceeds the plan limit                                             |

---

## Next Steps

- [Metric Types](/docs/usage-and-billing/metric-types) — Check which metrics affect credit deduction.
- [`/pricing`](/pricing) — Plan prices, monthly credits, per-metric rates, and a monthly cost simulation.
