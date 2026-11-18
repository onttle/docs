---
title: 'Changelog'
description: 'Version-by-version change history for the Onttle API and services.'
category: 'changelog'
slug: 'changelog'
locale: 'en'
apiVersion: null
order: 1
updatedAt: '2026-11-03'
---

# Changelog

The most recent changes appear at the top. Change types are classified as **Added**, **Changed**, **Fixed**, and **Deprecated**.

---

## v1.1.0 <span>2026-11-03</span>

### Added

- **Custom domains**: serve a Workspace CMS on a customer domain such as `cms.onttle.com`. Add a subdomain under **Workspace → Domains**, create the DNS records shown, and verification runs automatically. Pro and above, up to 20 per Workspace. [Docs](/docs/core-features/custom-domains)
- **Custom themes**: set colors (light and dark), corner radius, font, density, plus the logo, favicon and tab title under **Workspace → Theme**. Applies on `app.onttle.com` and on custom domains. Pro and above. [Docs](/docs/core-features/custom-themes)

### Changed

- The plan feature table now lists custom domains and custom themes. Custom domains start at **Pro**, not Pro+.

---

## v1.0.0 <span>2026-10-30</span>

### Added

- **Onttle API general availability**: REST API v1 is now generally available.
- Supported features: Workspace, Project, Table, Column, Contents, Translation, Variable, Category, File, API Key, Metrics, Logs
- **API Key types**: keys are issued as **publishable** (`pk-proj_…`, read-only, safe for browsers) or **secret** (`sk-proj_…`, read and write, server-side only). Writes, file uploads, chat and embeddings calls with a publishable key return `403`.
- **Hashed key storage**: the full key is shown once at creation and only its hash is stored afterwards; the dashboard shows the prefix and last 4 characters and records when each key was last used.

---

## Change Type Reference

| Type           | Meaning                                                                  |
| -------------- | ------------------------------------------------------------------------ |
| **Added**      | A new feature or endpoint has been added                                 |
| **Changed**    | Existing behavior or spec has changed (check for backward compatibility) |
| **Fixed**      | A bug fix or performance improvement                                     |
| **Deprecated** | Scheduled for future removal. Refer to the migration guide               |
