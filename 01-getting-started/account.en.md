---
title: 'Account and Sign-in'
description: 'Learn how to sign up, verify your email, sign in, reset your password, manage profile, notification, and language settings, and delete your account.'
category: 'getting-started'
slug: 'account'
locale: 'en'
apiVersion: 'v1'
order: 4
updatedAt: '2026-10-30'
---

# Account and Sign-in

Your Onttle account is used to sign in to the dashboard and manage Workspaces and Projects. When calling the REST API from outside, you use an [API Key](/docs/rest-api/authentication), not your account.

By the end of this guide, you will know how to create an account and sign in, how to change your account information, and which data is deleted along with your account.

---

## Signing Up

You can sign up in one of three ways.

| Method | Description |
| ------ | ----------- |
| Email | Sign up with your name, email, and password. Email verification is required after signing up. |
| Google | Sign up and sign in directly with your Google account. |
| GitHub | Sign up and sign in directly with your GitHub account. |

Passwords must be **at least 8 characters** long and include an uppercase letter, a lowercase letter, a number, and a special character.

By signing up, you agree to the Terms of Service and Privacy Policy.

> Signing up again with an email that is already registered shows an error. If you have forgotten your password, use [Resetting Your Password](#resetting-your-password).

### Email Verification

When you sign up with email, a verification email is sent.

1. Click the verification link in the email.
2. The **Welcome to ONTTLE** screen opens and you are signed in automatically.
3. Click **Get started** to go to your Workspace list.

If you try to sign in before verifying, you will see a notice that verification is required, along with a **Resend confirmation email** button. If you cannot find the email, check your spam folder and then request the verification email again.

### Your First Sign-in

When you sign in for the first time, the following are set up automatically.

- **Personal Workspace**: A Free plan Workspace named `{name}'s Workspace` is created.
- **Welcome email**: A welcome email is sent to the email you signed up with.
- **Pending invitations**: If you received Workspace invitations before signing up, you automatically join those Workspaces. Expired invitations are excluded.

---

## Signing In

On the sign-in screen, enter your email and password, or use the **Log in with Google** or **Log in with GitHub** button.

Sign in with the same method you used to sign up.

---

## Resetting Your Password

1. On the sign-in screen, click **Forgot Password**.
2. Enter the email you signed up with and click **Send Email**.
3. Open the link in the email and enter a new password on the **Reset Password** screen.

- Entering an email that is not registered shows an error.
- We recommend opening the reset link in the same browser you used to request the reset. The link may not work if opened in a different browser or device.

If you are already signed in, you can change your password right away under **Account Settings → Change Password** by entering your current password.

---

## Account Settings

Open the account screen from the user menu in the top-right corner of the dashboard. The account screen has three menus.

| Menu | Available settings |
| ---- | ------------------ |
| Profile | Name, bio, profile photo (up to 1 MB) |
| Notifications | Whether to receive newsletter and product update emails |
| Account Settings | Change password, delete account |

> Your email address is used for sign-in and notifications and cannot be changed in the dashboard. To change your email, contact [Support](/support).

### Language and Theme

You can change the dashboard language and theme from the user menu.

- **Language**: 한국어, English. When changed while signed in, it is saved to your account, and emails sent by Onttle are also delivered in this language.
- **Theme**: Light mode, Dark mode, System mode

> The dashboard language is separate from a Project's content languages. Content languages are managed in [Translations](/docs/core-features/translations) and Project settings.

---

## Deleting Your Account

Go to **Account Settings → Danger Zone → Delete Account** and enter your email address to delete your account. This action cannot be undone.

Deleting your account does the following.

| Target | Result |
| ------ | ------ |
| Account and sign-in information | Deleted. You can no longer sign in. |
| Workspace and Project memberships | You are removed from all memberships. |
| Workspaces where you are an **Owner (OWNER)** | The Workspace and its Projects, contents, and files are deleted, and the subscription is canceled. |
| Workspaces where you are not an Owner | Kept as they are. |

When deletion is complete, an account deactivation email is sent.

> **To keep a Workspace**, before deleting your account, change another member's role to Owner, then either change your own role to one below Owner or leave the Workspace. Any Workspace where you remain an Owner is deleted along with your account, even if it has other Owners. See [Workspaces and Members](/docs/getting-started/workspaces-and-members#transferring-ownership) for details.

---

## Next Steps

- [Workspaces and Members](/docs/getting-started/workspaces-and-members) — Create a Workspace and invite teammates.
- [Quick Start](/docs/getting-started/quick-start) — Create your first Project and fetch content through the API.
