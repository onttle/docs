---
category: 'start'
locale: 'en'
updatedAt: '2026-10-30'
---

# FAQ — Getting Started

## What is Onttle?

Onttle is lightweight content infrastructure for front-end developers. Once you define a table schema, a CMS screen for managing content and a REST API are created right away, and you can use translations, variables, file storage, and Vector search (RAG) all in one place. You can focus on front-end development without running a separate back end or database.

See [Introduction](/docs/getting-started/introduction) for details.

## Can I use it for free?

Yes. You can start on the Free plan without a credit card, and the Free plan includes all Project features. The Free plan includes a set amount of credits each month and has limits on members, Projects, and storage.

See [Plans and Limits](/docs/usage-and-billing/plans-and-limits) for each plan's limits.

## How do I sign up?

You can sign up with email, a Google account, or a GitHub account. If you sign up with email, you must click the link in the verification email before you can sign in. A personal Workspace is created automatically when you first sign in.

## I didn't receive the verification email.

Check your spam folder first. If the email isn't there, try signing in with your email and password on the sign-in screen, then click **Resend confirmation email** to receive the verification email again.

## I forgot my password.

On the sign-in screen, click **Forgot Password** and enter the email you signed up with to receive a reset link. Open the link in the same browser you used to request the reset. If you signed up with Google or GitHub, just sign in with that account.

## What is the difference between a Workspace and a Project?

- A **Workspace** is the unit for teams and billing. Plans, credits, and members are managed per Workspace.
- A **Project** corresponds to a single service or website. Tables, translations, variables, files, and API Keys are managed per Project.

You can create multiple Projects in one Workspace, and all Projects in a Workspace share its credits.

## How do I make my first API call?

1. Create a Project and add a Table and Columns.
2. Write content and **Publish** it. Only published content can be fetched through the API.
3. Create an API Key in the **API Keys** menu. If you only need to read data, a **Publishable** key (`pk-proj_…`) is enough. Copy the full key right away, as it is shown only once after creation.
4. Call `https://api.onttle.com/v1/{projectId}/tables/{tableId}` with the `Authorization: Bearer <API Key>` header.

See [Quick Start](/docs/getting-started/quick-start) for step-by-step instructions.

## Can I change the dashboard language?

Yes. Choose between 한국어 and English under **Language** in the user menu at the top right of the dashboard. If you change it while signed in, emails sent by Onttle are also delivered in the selected language. The languages used for your content are set separately in Project settings.
