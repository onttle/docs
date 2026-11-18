---
title: 'MCP Server'
description: 'How to connect AI clients such as Claude, Claude Code and Cursor to the Onttle MCP server to read and change a project.'
category: 'ai'
slug: 'mcp'
locale: 'en'
apiVersion: 'v1'
order: 8
updatedAt: '2026-11-13'
---

# MCP Server

The Onttle MCP server is a [Model Context Protocol](https://modelcontextprotocol.io) server that lets AI clients work with an Onttle project directly. Once connected, the AI can read table structure and contents and, after you confirm, change contents, tables and settings.

```
https://api.onttle.com/mcp
```

The transport is Streamable HTTP. There is nothing to install.

---

## Ways to connect

Connect with one of these credentials.

| Credential                        | Access                                                                    | Good for                                        |
| --------------------------------- | ------------------------------------------------------------------------- | ----------------------------------------------- |
| **Onttle account sign-in**        | Every project the account belongs to, with your role in each project      | Claude apps, work across several projects       |
| **Publishable key (`pk-proj_…`)** | The key's project. Reads only what the public API serves                  | Reading the schema and published contents       |
| **Secret key (`sk-proj_…`)**      | The key's project. Reads drafts, files and logs, and writes contents      | CI and agents that run on a server              |

Creating and changing tables, permission rules, issuing API keys and managing members require the account sign-in. An API key cannot name the person behind a change in the [Audit log](/docs/developers/audit-log).

---

## Connecting

### Claude Code

**Account sign-in**

```bash
claude mcp add --transport http onttle https://api.onttle.com/mcp
```

Start Claude Code, enter `/mcp`, then choose `onttle` → **Authenticate**. Sign in to Onttle in the browser and select **Approve** on the consent screen.

**API key**

```bash
claude mcp add --transport http onttle https://api.onttle.com/mcp --header "X-Onttle-Key: $ONTTLE_API_KEY"
```

### Claude connector (web, desktop and mobile)

In the Claude apps you add Onttle as a **custom connector**. Once added, it is available in the web, desktop and mobile apps of the same account. Custom connectors are part of Claude's paid plans.

**1. Add the connector**

1. In Claude, open **Settings > Connectors**.
2. Select **Add custom connector**.
3. Enter `Onttle` as the name and the address below as the remote MCP server URL, then select **Add**.

```
https://api.onttle.com/mcp
```

On Team and Enterprise plans an organization owner adds it first, under **Organization settings > Connectors**. Members then connect it from their own settings.

**2. Connect your Onttle account**

1. Select **Connect** next to Onttle in the connector list.
2. Sign in when the Onttle sign-in page opens in the browser.
3. On the consent screen, check that the requesting app is Claude and select **Approve**.

The connection has the permissions of the Onttle account that signed in. Each member connects with their own account, so people in one organization only see the projects they can access.

**3. Use it in a chat**

1. In a new chat, open the **Search and tools** menu of the message box and turn Onttle on.
2. Ask for something like "show my Onttle projects". Claude calls `onttle_list_workspaces` and `onttle_list_projects`.
3. The first time a tool is called, Claude asks whether to allow it. Read tools can be set to **Always allow**; for write tools we recommend keeping the prompt.

Separately from Claude's prompt, write tools [take two calls](#changes-take-two-calls). Look at the preview Claude shows, then tell it to go ahead.

**Good to know**

- Claude apps connect with the account sign-in only; an API key header cannot be set.
- If your workspace has IP access rules, [allow Anthropic's outbound range](#workspaces-with-ip-access-rules).
- To disconnect, disconnect Onttle or remove the connector under **Settings > Connectors**.

### Other clients

Any client that supports remote MCP servers (Streamable HTTP) can connect. Clients configured with a file (for example Cursor) register the server like this.

```json
{
  "mcpServers": {
    "onttle": {
      "url": "https://api.onttle.com/mcp",
      "headers": {
        "X-Onttle-Key": "sk-proj_..."
      }
    }
  }
}
```

Without `headers`, a client that supports OAuth connects through the account sign-in. An API key can also be sent as `Authorization: Bearer <API key>` instead of the `X-Onttle-Key` header.

---

## Permissions

Every connection sees the same tool list. Access is checked on each call.

- **Account sign-in:** project tools take a `project_id`. A call is refused when your role in that project (`OWNER`, `ADMIN`, `EDITOR`, `GUEST`) is below the tool's minimum role. See [Member roles](/docs/developers/member-roles).
- **Publishable key:** acts as `GUEST` and returns only what the public API serves, such as published contents.
- **Secret key:** acts as `EDITOR`. No `project_id` is needed.

API key expiry, allowed hosts and workspace IP access rules apply as they do on the REST API.

---

## Changes take two calls

A tool that changes data does not run on a single call.

1. The first call changes nothing. It returns a **preview** of the change and a `confirm_token`.
2. The AI shows you the preview.
3. After you agree, the AI calls the tool again with **the same arguments** plus `confirm_token`. This call applies the change.

A `confirm_token` is valid for 10 minutes and is bound to the connection, tool, project and arguments it was issued for. If any argument changes, the token no longer matches and the call is refused, so a change other than the one in the preview cannot be applied.

---

## Tools

There are 23 read tools and 20 write tools. **Minimum role** is the project role an account needs. **API key** is the key type that may call the tool; `-` means account sign-in only.

### Workspaces and projects

| Tool                      | Kind  | What it does                          | Minimum role | API key     |
| ------------------------- | ----- | ------------------------------------- | ------------ | ----------- |
| `onttle_list_workspaces`  | Read  | Workspaces the account belongs to     | -            | -           |
| `onttle_list_projects`    | Read  | Projects of a workspace               | -            | -           |
| `onttle_get_project`      | Read  | Project details                       | GUEST        | Publishable |
| `onttle_get_schema`       | Read  | Tables and columns                    | GUEST        | Publishable |
| `onttle_get_openapi_spec` | Read  | The project's OpenAPI document        | GUEST        | Publishable |
| `onttle_create_table`     | Write | Create a table                        | ADMIN        | -           |
| `onttle_update_table`     | Write | Change a table and its columns        | ADMIN        | -           |
| `onttle_set_table_rules`  | Write | Set a table's permission rules        | ADMIN        | -           |

### Contents

| Tool                          | Kind  | What it does                      | Minimum role | API key     |
| ----------------------------- | ----- | --------------------------------- | ------------ | ----------- |
| `onttle_search_contents`      | Read  | Search, filter and sort contents  | GUEST        | Publishable |
| `onttle_get_contents`         | Read  | Get one item                      | GUEST        | Publishable |
| `onttle_create_contents`      | Write | Create contents                   | EDITOR       | Secret      |
| `onttle_update_contents`      | Write | Update contents                   | EDITOR       | Secret      |
| `onttle_set_contents_status`  | Write | Publish or unpublish              | EDITOR       | Secret      |
| `onttle_delete_contents`      | Write | Delete contents                   | EDITOR       | Secret      |
| `onttle_get_contents_history` | Read  | Version history                   | EDITOR       | Secret      |
| `onttle_rollback_contents`    | Write | Roll back to an earlier version   | EDITOR       | Secret      |

With a publishable key, content tools return published contents only.

- **Keys of values:** `data` in read results and `data` in write tools are both keyed by column name, so values read can be written back as they are.
- **EDITOR columns:** send **a Markdown string** or **a TipTap document** (a JSON object or its JSON string). The dashboard and the REST API take the same formats. A localized column takes one value per language: `{ "ko": value, "en": value }`. Read results return the body as plain text.
- **Preview validation:** create and update previews check column constraints such as required values, lengths and ranges, and list failures in `invalid_rows`. When every row fails, no `confirm_token` is issued.
- **Updates and rollback:** they follow the dashboard's logic. An updated row is saved as DRAFT unless `publish: true` is passed, and a rolled-back row takes the values and the status of the revision. Rolling a published row back to a revision that was a draft makes it DRAFT, and the preview shows the status after the rollback.

### Resources

| Tool                                                       | Kind  | What it does                 | Minimum role | API key |
| ---------------------------------------------------------- | ----- | ---------------------------- | ------------ | ------- |
| `onttle_list_categories`                                   | Read  | List categories              | GUEST        | Secret  |
| `onttle_create_categories`, `onttle_update_categories`     | Write | Create and update categories | EDITOR       | Secret  |
| `onttle_list_translations`                                 | Read  | List translations            | GUEST        | Secret  |
| `onttle_create_translations`, `onttle_update_translations` | Write | Create and update translations | EDITOR     | Secret  |
| `onttle_list_variables`                                    | Read  | List variables               | GUEST        | Secret  |
| `onttle_create_variables`, `onttle_update_variables`       | Write | Create and update variables  | EDITOR       | Secret  |
| `onttle_search_files`                                      | Read  | Search uploaded files        | GUEST        | Secret  |
| `onttle_list_webhooks`                                     | Read  | List webhooks                | GUEST        | Secret  |
| `onttle_create_webhook`                                    | Write | Create a webhook             | EDITOR       | Secret  |
| `onttle_get_webhook_logs`                                  | Read  | Webhook delivery log         | EDITOR       | Secret  |

### Members

| Tool                            | Kind  | What it does                        | Minimum role | API key |
| ------------------------------- | ----- | ----------------------------------- | ------------ | ------- |
| `onttle_get_member_settings`    | Read  | Member feature settings             | GUEST        | Secret  |
| `onttle_update_member_settings` | Write | Change member feature settings      | ADMIN        | -       |
| `onttle_get_member_stats`       | Read  | Sign-up and active member figures   | EDITOR       | -       |
| `onttle_list_members`           | Read  | List members                        | EDITOR       | -       |
| `onttle_update_member`          | Write | Change a member's details or status | EDITOR       | -       |
| `onttle_create_member`          | Write | Add a member directly               | ADMIN        | -       |

`onttle_create_member` is billed as one sign-up and counts toward the monthly sign-up limit. See [Members](/docs/core-features/members).

### API and operations

| Tool                         | Kind  | What it does                              | Minimum role | API key |
| ---------------------------- | ----- | ----------------------------------------- | ------------ | ------- |
| `onttle_list_api_keys`       | Read  | List API keys (full values are not shown) | ADMIN        | -       |
| `onttle_create_api_key`      | Write | Issue a publishable API key               | ADMIN        | -       |
| `onttle_get_api_settings`    | Read  | API endpoint settings                     | GUEST        | Secret  |
| `onttle_set_api_settings`    | Write | Change API endpoint settings              | ADMIN        | -       |
| `onttle_analyze_api_logs`    | Read  | Analyze API request logs                  | EDITOR       | Secret  |
| `onttle_get_billing_summary` | Read  | Plan and usage summary                    | EDITOR       | -       |

`onttle_create_api_key` issues publishable keys only. The full key is shown once, in the result of the confirmed call. Secret keys are issued in the dashboard only.

### Documentation

| Tool                       | Kind | What it does                                  | Minimum role | API key     |
| -------------------------- | ---- | --------------------------------------------- | ------------ | ----------- |
| `onttle_search_docs`       | Read | Search the Onttle documentation and FAQs      | -            | Publishable |
| `onttle_get_api_reference` | Read | REST API reference tailored to the project    | GUEST        | Publishable |

`onttle_search_docs` can be called by any connection, whatever its role.

---

## Prompts and resources

Clients that support prompts can start these tasks directly.

| Prompt            | Purpose                                        |
| ----------------- | ---------------------------------------------- |
| `onttle-setup`    | Add Onttle integration code to this codebase   |
| `onttle-add-auth` | Add member sign-up and sign-in                 |
| `onttle-seed`     | Import local data as draft contents            |
| `onttle-debug`    | Find the cause of an Onttle API integration problem |

Three resources are available.

| Resource URI                                 | Contents                             |
| -------------------------------------------- | ------------------------------------ |
| `onttle://docs/get-started`                  | Getting started guide for AI agents  |
| `onttle://projects/{projectId}/schema`       | The project's tables and columns     |
| `onttle://projects/{projectId}/openapi.json` | The project's OpenAPI document       |

---

## Usage and limits

- **API key connections:** each tool call is recorded as one API read, and each confirmed write as one API write. They appear in usage under `/mcp/tools/<tool name>`.
- **Account connections:** not charged. Calls are recorded in the project's **API logs** under `/mcp/tools/<tool name>`, with "Account connection (MCP)" in the API key column. Calls that belong to no project, such as listing workspaces and projects or searching the docs, are not recorded. Changes made with management tools are also recorded in the Audit log.
- **Rate limit:** your plan's API rate limit applies. Confirmed writes are limited to 30 per connection per minute.
- **Result size:** a tool result over 48,000 characters is answered with `result_too_large` and how to narrow the call. Add a filter or lower the page size and call again.

---

## Workspaces with IP access rules

Requests from Claude apps (web, desktop, mobile) come from Anthropic's servers, not from your computer. If your workspace has IP access rules, add Anthropic's outbound range to the allow list before connecting from a Claude app.

```
160.79.104.0/21
```

Clients that run on your computer, such as Claude Code, send requests from that computer's IP.

An API key whose allowed hosts are domains only cannot be used with MCP, because MCP clients do not send an `Origin` header. Use a key without allowed hosts, a key that allows an IP, or the account sign-in.

---

## Records

Changes made through MCP are recorded in the [Audit log](/docs/developers/audit-log) like changes made in the dashboard. Management actions run through an account connection are recorded as `mcp.tool_called`, and the change rows carry `mcp` as their channel.

---

## Disconnecting

- **Claude Code:** `claude mcp remove onttle`
- **Claude apps:** remove the Onttle connector in **Settings > Connectors**.
- **API key:** delete the key under **API Keys** in the dashboard. Every connection that uses it stops working at once.

---

## Common errors

| Error                          | Cause                                                   | What to do                                                    |
| ------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------- |
| `401 Unauthorized`             | No credential, or it expired                            | Authenticate again in the client, or check the API key        |
| `forbidden`                    | Role too low, or the key type may not call the tool     | Check the Minimum role and API key columns above              |
| `confirm_token_expired`        | More than 10 minutes since the preview                  | Call without `confirm_token` to get a new preview             |
| `confirm_token_invalid`        | Arguments differ from the preview                       | Repeat the call with the same arguments, or get a new preview |
| `result_too_large`             | The result is over 48,000 characters                    | Add a filter or lower the page size                           |
| A message that the IP is not allowed | Workspace IP access rules                         | Allow the request IP, or `160.79.104.0/21` for Claude apps    |
| `429 Too Many Requests`        | Rate limit exceeded                                     | Wait for the time in the `Retry-After` header, then retry     |

---

## Next steps

- [Authentication](/docs/rest-api/authentication) — issue API keys and set allowed hosts and expiry.
- [Member roles](/docs/developers/member-roles) — what each role may do.
- [Ask AI (Assistant)](/docs/ai/ask-ai) — the AI assistant inside the dashboard, built on the same tools.
