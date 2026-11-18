---
category: 'billing'
locale: 'en'
updatedAt: '2026-11-09'
---

# FAQ — Billing & Payments

## How is pricing calculated?

Onttle uses **a monthly subscription plan plus credits**. Each Workspace subscribes to a plan, and the plan's monthly credits are granted on every billing date. Credits are then deducted based on actual usage, such as data storage, API transfer, file uploads, and AI tokens. See the [Pricing](/pricing) page for per-item rates and a monthly cost simulator.

## How much is a credit worth?

1 credit equals 1 KRW. Currently, only Korean won (KRW) is supported as a payment currency. Credits cannot be exchanged for cash or moved to another Workspace.

## Do unused credits roll over to the next month?

No. Monthly credits are refilled on each billing date and can only be used in that period. When a new billing cycle starts, your balance is reset to the plan's monthly credits, and any credits left from the previous cycle (including purchased add-on credits) expire.

## What happens when I run out of credits?

Requests that require credits are rejected with an `insufficientCredit` error. Your stored data and settings are kept, and you are not charged automatically. Workspace Owners and Admins receive a low-credit email (at most once a day).

To continue using the service, choose one of the following.

- **Buy add-on credits**: Added to your balance immediately.
- **Upgrade your plan**: Takes effect immediately, and the difference in monthly credits is granted.
- **Wait for the next billing cycle**: Monthly credits are granted again on your billing date.

## How do I buy add-on credits?

In **Workspace → Billing → Buy add-on credits**, choose 1,000 / 3,000 / 5,000 / 10,000 / 30,000 credits, or enter a custom amount of up to 100,000 credits. Your default card is charged immediately, and add-on credits can be used **only in the current billing cycle**. Only Owners and Admins can buy credits.

## Is there auto recharge or a monthly limit?

Yes. Turn it on in **Workspace → Billing → Auto recharge** and add-on credits are bought automatically with your default card when the balance falls below the threshold you set. You choose how many credits each recharge buys and, optionally, the most credits to buy automatically in a month (monthly limit). With a monthly limit set, you are never charged beyond it. Only Owners and Admins can set it up, and a payment method must be registered. Like add-on credits, auto-recharged credits can be used **only in the current billing cycle**. If you often run short of credits, we also recommend upgrading to a higher plan.

## When is my billing date?

The day you subscribed to a plan becomes your monthly billing date, and your default card is charged at **9:00 AM (KST)** on that day. If you subscribed on the 29th–31st, you are billed on the 28th of each month. You can see your next scheduled charge in **Workspace → Invoices → View upcoming invoice**.

## Which payment methods can I use?

You can register credit and debit cards. Payments are processed by KG Inicis (via PortOne), and Onttle does not store card numbers directly. Each Workspace can register up to **3** cards, one of which is set as the default payment method. If you delete the default card, one of the remaining cards automatically becomes the default.

## What happens if a payment fails?

The subscription moves to **Payment failed** status, and Owners and Admins receive an email. Payment is retried 1 day and 3 days after the original billing date, and if all attempts fail, the Workspace is switched to the Free plan. Your data is not deleted. In the meantime, replace the card or change the default payment method. You cannot change plans while a payment has failed.

## When do plan changes and cancellations take effect?

| Change | Takes effect | Billing |
| ------ | ------------ | ------- |
| Upgrade | Immediately | The price difference is prorated for the remaining period and charged, and the credit difference is granted at the same rate. |
| Downgrade | Next billing date | You keep using the current plan until the next billing date. |
| Cancellation | Next billing date | The Workspace switches to the Free plan when the current cycle ends. |

Scheduled downgrades and cancellations can be reverted with **Undo scheduled change** before they take effect. Moving to a lower plan does not delete your data, but new actions that exceed the limits are restricted.

## Can I get a refund?

If you cancel or downgrade in the middle of a billing cycle, the remaining period is not refunded, and you can keep using the service until the cycle ends. Used or expired credits are not refunded either.

You can request a refund in the following cases.

- Within **7 days** of payment, if no credits were used in that cycle
- When there is a payment error, such as a duplicate charge
- When you could not use the service due to the company's fault
- When a refund is required by applicable law

Request refunds at [support@onttle.com](mailto:support@onttle.com). Refunds are made to the original payment method, and credits equal to the refunded amount are recovered. See the [Terms of Service](/terms) for detailed criteria.

## Can I get receipts or tax invoices?

All payment history is available in **Workspace → Invoices**. In the invoice details, you can **Download** a PDF or view the card **Receipt**. Only Owners and Admins can view invoices.

Tax invoices are supported on the Enterprise plan. If you need one, leave a billing inquiry on the [Contact](/contact) page.
