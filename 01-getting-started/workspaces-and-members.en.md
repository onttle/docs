---
title: 'Workspaces and Members'
description: 'Learn how to create and configure a Workspace, invite members and change their roles, transfer ownership, manage Project members, and delete a Workspace.'
category: 'getting-started'
slug: 'workspaces-and-members'
locale: 'en'
apiVersion: 'v1'
order: 5
updatedAt: '2026-10-30'
---

# Workspaces and Members

A Workspace is the space that holds your Projects and is also **the unit of plans and billing**. You create multiple Projects inside one Workspace and invite teammates to manage them together.

```
Workspace (plan, billing, credits, members)
 ├── Project A (tables, translations, variables, files, API Keys …)
 └── Project B
```

By the end of this guide, you will know how to create a Workspace, invite teammates, change roles, and transfer ownership.

---

## Creating a Workspace

A personal Workspace is created automatically when you first sign in. To create more Workspaces, select **Create a new workspace** in the Workspace list.

| Field | Description |
| ----- | ----------- |
| Name | Workspace name |
| Icon | Image file (up to 1 MB, optional) |
| Description | Optional |
| Type | Personal, Startup, Agency, Company, Educational Institution, Art, Non-profit Organization, Not Applicable |
| Size | 1, 2-10, 11-24, 25-100, More than 100 |
| Plan | The plan to use |

- **Free plan** Workspaces can be created immediately without a payment method. There is a limit on how many Free Workspaces one account can create.
- If you choose a **paid plan**, you register a card during creation and the first month is charged immediately. If the payment fails, the Workspace is not created.
- Billing occurs on the same day each month as the creation date, at 9:00 AM (KST). If you created the Workspace on the 29th–31st, you are billed on the 28th of each month.

You can change the plan at any time after creation. See [Plans and Limits](/docs/usage-and-billing/plans-and-limits) for details.

---

## Workspace Settings

In **Workspace → Settings**, you can change the name, type, and size. Owner or Admin permission is required. On Pro and above, the **Domains** and **Theme** menus let you set up a [custom domain](/docs/core-features/custom-domains) and a [custom theme](/docs/core-features/custom-themes).

---

## Roles

Workspaces and Projects use the same four roles.

| Role | Description |
| ---- | ----------- |
| Owner (OWNER) | All permissions. Only Owners can delete a Workspace. |
| Admin (ADMIN) | Most permissions, including managing members, billing, and settings |
| Editor (EDITOR) | Can create Projects and edit content. Cannot manage members or billing. |
| Guest (GUEST) | Read-only |

The main Workspace-level permissions are as follows.

| Action | Owner | Admin | Editor | Guest |
| ------ | :---: | :---: | :----: | :---: |
| Add a new Project | ✓ | ✓ | ✓ | |
| View the member list | ✓ | ✓ | ✓ | ✓ |
| Invite members, change roles, remove members, cancel invitations | ✓ | ✓ | | |
| View usage | ✓ | ✓ | ✓ | |
| View credit balance | ✓ | ✓ | ✓ | ✓ |
| Payment methods, buy additional credits, change or cancel plan | ✓ | ✓ | | |
| View invoices | ✓ | ✓ | | |
| Edit Workspace information | ✓ | ✓ | | |
| Delete the Workspace | ✓ | | | |

See [Member Roles](/docs/developers/member-roles) for Project-level permissions in detail.

---

## Inviting Members

Invite members from **Workspace → Members → Invite Members**. Owner or Admin permission is required.

1. Enter the email addresses to invite. Separate multiple addresses with commas (`,`).
2. Select a role.
3. Optionally add an invitation message, then send the invitation.

Invitations are handled differently depending on whether the invitee already has an account.

| Invitee | Result |
| ------- | ------ |
| Already signed up for Onttle | Added to the Workspace immediately, and a notification email is sent. |
| Not signed up yet | An invitation email is sent and the invitee appears in the **Pending Members** tab. They join automatically when they sign up with the invited email. |

- Invitations expire after **7 days**. Expired invitations must be sent again.
- **Pending invitations count toward the plan's member limit.** If you reach the limit, cancel unused invitations or upgrade your plan.
- You cannot invite an email that is already a member or has a pending invitation.

---

## Changing Roles and Removing Members

In the **Workspace → Members → Joined Members** tab, you can change a member's role or remove them from the Workspace. You can also leave the Workspace yourself.

A Workspace must have **at least one Owner**. The last Owner cannot change their role or leave.

### Transferring Ownership

There is no dedicated ownership transfer feature. Transfer ownership in the following order.

1. Change the role of the member taking over to **Owner**. A Workspace can have multiple Owners.
2. If needed, change your own role to Admin or lower, or leave the Workspace.

> If you plan to delete your account, you must complete step 2 for the Workspace to be kept. If you remain an Owner, the Workspace is deleted along with your account.

---

## Project Members

Only members of the Workspace can be added to its Projects. To bring an outside user into a Project, invite them to the Workspace first.

1. Go to **Project → Members**.
2. Select a Workspace member, assign a Project role, and add them.

Project members can be managed by the Project's Owners or Admins. A Project must also have at least one Owner.

---

## Deleting a Workspace

Go to **Workspace → Settings → Danger Zone → Delete Workspace** and enter the Workspace name to delete it. Only Owners can delete a Workspace.

- All Projects, contents, and files in the Workspace are deleted with it.
- Paid plan subscriptions are canceled immediately. Fees for the remaining period are not refunded.

---

## Next Steps

- [Plans and Limits](/docs/usage-and-billing/plans-and-limits) — Check member, Project, and storage limits for each plan.
- [Member Roles](/docs/developers/member-roles) — Review Project-level permissions in detail.
- [Security](/docs/developers/security) and [Audit Log](/docs/developers/audit-log) — Review and export the record of member, API key, and billing changes.
