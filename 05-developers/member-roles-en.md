---
title: 'Member Roles'
description: 'How member roles (Role) in a Workspace and Project determine which features and permissions are available.'
category: 'developers'
slug: 'member-roles'
locale: 'en'
apiVersion: 'v1'
order: 4
updatedAt: '2026-09-07'
---

# Member Roles

Access to features is restricted based on a member's role within a Workspace or a Project. Permission checks are enforced on both the Front-end and the Back-end.

The Front-end disables buttons and hides sidebar menu items, while the Back-end returns an error JSON response for requests that lack the required permission.

---

## Workspace Permissions

### Workspace Member Role Types

| Role   | Description |
| ------ | ----------- |
| OWNER  | Owner       |
| ADMIN  | Admin       |
| EDITOR | Editor      |
| GUEST  | Guest       |

### Feature Restrictions by Workspace Role

| Feature                      | OWNER | ADMIN | EDITOR | GUEST |
| ---------------------------- | :---: | :---: | :----: | :---: |
| **[Projects]**               |       |       |        |       |
| Add new project              |   O   |   O   |   O    |   -   |
| **[Members]**                |       |       |        |       |
| View member list             |   O   |   O   |   O    |   O   |
| View pending invitation list |   O   |   O   |   -    |   -   |
| Invite member                |   O   |   O   |   -    |   -   |
| Change member role           |   O   |   O   |   -    |   -   |
| Remove member                |   O   |   O   |   -    |   -   |
| Cancel invitation            |   O   |   O   |   -    |   -   |
| **[Usage]**                  |       |       |        |       |
| View usage statistics        |   O   |   O   |   O    |   -   |
| **[Billing]**                |       |       |        |       |
| View credit balance          |   O   |   O   |   O    |   O   |
| Set currency                 |   O   |   O   |   -    |   -   |
| Add payment method           |   O   |   O   |   -    |   -   |
| Remove payment method        |   O   |   O   |   -    |   -   |
| Set default payment method   |   O   |   O   |   -    |   -   |
| One-time credit top-up       |   O   |   O   |   -    |   -   |
| Configure auto-reload        |   O   |   O   |   -    |   -   |
| Set monthly top-up limit     |   O   |   O   |   -    |   -   |
| **[Invoices]**               |       |       |        |       |
| View invoice list            |   O   |   O   |   -    |   -   |
| **[Settings]**               |       |       |        |       |
| Edit workspace information   |   O   |   O   |   -    |   -   |
| Delete workspace             |   O   |   -   |   -    |   -   |

---

## Project Permissions

### Project Member Role Types

| Role   | Description |
| ------ | ----------- |
| OWNER  | Owner       |
| ADMIN  | Admin       |
| EDITOR | Editor      |
| GUEST  | Guest       |

### Feature Restrictions by Project Role

| Feature                   | OWNER | ADMIN | EDITOR | GUEST |
| ------------------------- | :---: | :---: | :----: | :---: |
| **[Home]**                |       |       |        |       |
| View home dashboard       |   O   |   O   |   O    |   O   |
| **[Tables]**              |       |       |        |       |
| View table list           |   O   |   O   |   O    |   O   |
| Add table                 |   O   |   O   |   -    |   -   |
| Edit table                |   O   |   O   |   -    |   -   |
| Duplicate table           |   O   |   O   |   -    |   -   |
| Delete table              |   O   |   O   |   -    |   -   |
| Add/edit column           |   O   |   O   |   -    |   -   |
| Delete column             |   O   |   O   |   -    |   -   |
| View content (data)       |   O   |   O   |   O    |   O   |
| Add/edit content (data)   |   O   |   O   |   O    |   -   |
| Delete content (data)     |   O   |   O   |   O    |   -   |
| Publish content           |   O   |   O   |   O    |   -   |
| **[Translations]**        |       |       |        |       |
| View translation list     |   O   |   O   |   O    |   O   |
| Add/edit translation      |   O   |   O   |   O    |   -   |
| Delete translation        |   O   |   O   |   O    |   -   |
| **[Variables]**           |       |       |        |       |
| View variable list        |   O   |   O   |   O    |   O   |
| Add/edit variable         |   O   |   O   |   O    |   -   |
| Enable/disable variable   |   O   |   O   |   O    |   -   |
| Delete variable           |   O   |   O   |   O    |   -   |
| **[Categories]**          |       |       |        |       |
| View category list        |   O   |   O   |   O    |   O   |
| Add/edit category         |   O   |   O   |   O    |   -   |
| Delete category           |   O   |   O   |   O    |   -   |
| **[Files]**               |       |       |        |       |
| View file list            |   O   |   O   |   O    |   O   |
| Upload file               |   O   |   O   |   O    |   -   |
| Delete file               |   O   |   O   |   O    |   -   |
| **[Vector]**              |       |       |        |       |
| View vector sync status   |   O   |   O   |   O    |   O   |
| Configure/run vector sync |   O   |   O   |   O    |   -   |
| **[Agents]**              |       |       |        |       |
| View agent list           |   O   |   O   |   O    |   O   |
| Add/edit agent            |   O   |   O   |   O    |   -   |
| Delete agent              |   O   |   O   |   O    |   -   |
| **[Webhooks]**            |       |       |        |       |
| View webhook list         |   O   |   O   |   O    |   O   |
| Add/edit webhook          |   O   |   O   |   O    |   -   |
| Delete webhook            |   O   |   O   |   O    |   -   |
| **[API Endpoints]**       |       |       |        |       |
| View API specification    |   O   |   O   |   O    |   O   |
| Enable/disable API        |   O   |   O   |   -    |   -   |
| **[API Keys]**            |       |       |        |       |
| View API key list         |   O   |   O   |   O    |   O   |
| Create/delete API key     |   O   |   O   |   -    |   -   |
| **[TypeScript]**          |       |       |        |       |
| View TypeScript types     |   O   |   O   |   O    |   O   |
| **[Usage]**               |       |       |        |       |
| View usage statistics     |   O   |   O   |   O    |   -   |
| **[Logs]**                |       |       |        |       |
| View logs                 |   O   |   O   |   O    |   -   |
| **[Settings]**            |       |       |        |       |
| Edit project information  |   O   |   O   |   -    |   -   |
| Delete project            |   O   |   -   |   -    |   -   |
| **[Members]**             |       |       |        |       |
| View member list          |   O   |   O   |   O    |   O   |
| Invite member             |   O   |   O   |   -    |   -   |
| Change member role        |   O   |   O   |   -    |   -   |
| Remove member             |   O   |   O   |   -    |   -   |
