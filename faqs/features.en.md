---
category: 'features'
locale: 'en'
updatedAt: '2026-11-11'
---

# FAQ — Features

## I saved my content, but it doesn't show up in the API.

The REST API returns **only published content**. Make sure you used **Save and publish** or clicked **Publish** in the list. Also check the following.

- Whether the list and item APIs for the Table are enabled in the **API Endpoints** menu
- Whether the Project ID and API Key in your request belong to the same Project
- If you set accepted hosts on the API Key, whether the site sending the request is in the list

## What is the difference between Publishable and Secret keys?

You choose a type when you create an API Key.

- **Publishable (`pk-proj_…`)**: A read-only key that can only make GET requests. Combined with accepted hosts, it can be used in browsers or on public websites.
- **Secret (`sk-proj_…`)**: Can create, update, and delete content, upload files, and call the Chat and Embeddings APIs. Keep it only in server environment variables and never put it in client code.

Sending a write request with a Publishable key returns `403 Forbidden` with a message saying a Secret key is required. The type cannot be changed later, so create a new key if needed. See [Authentication](/docs/rest-api/authentication) for details.

## Can I see the full API Key value again?

No. The full key is shown **only once, right after it is created**. Onttle stores only a hash of the key, so afterward you can see only its prefix and last 4 characters (`pk-proj_abcd…wxyz`) in the list. If you lose a key, create a new one and deactivate or delete the old one. You can check whether a key is actually in use in the **Last Used** column of the **API Keys** list.

## Which Column types are available?

Text, number, date, timestamp, boolean, email, URL, color, file, editor, category, and reference types. Text and editor can store multilingual values, and most types can store multiple values as an array.

Column types cannot be changed after creation, so choose carefully. See [Column Types](/docs/developers/column-types) for details.

## How do I manage multilingual content?

In Project settings, choose the languages to use (Korean, English, Japanese, Chinese, Spanish, French, German) and a default language. Turning on the **Localization** option for a Column lets you enter a value for each language, and the API returns an object keyed by language code.

For key-value text such as UI strings, use the [Translations](/docs/core-features/translations) feature. You can also use **Fill with AI** to fill in values for other languages automatically.

> Removing a language from a Project also deletes the content entered in that language.

## How are images served?

Uploaded files are kept in storage and provided in API responses as signed URLs (`presigned_url`) that are valid for a limited time. When an image is uploaded, thumbnails of several sizes are created automatically. You can change thumbnail sizes in **Files → Image thumbnail settings**, and the new settings apply to files uploaded afterward.

URLs in API responses expire after a short time, so don't store them; fetch them again through the API when needed.

## How large can uploaded files be?

The maximum size per file depends on your plan: Free 10 MB, Pro 100 MB, and Pro+ and above 500 MB. Total file storage for the Workspace is also set per plan. In the dashboard, you can upload up to 200 files at once.

## Can I extract text from images or PDFs?

Yes. Select **Extract OCR text** when uploading to extract and store text from images and PDFs. If you also select **Generate keywords and description with AI**, keywords and a summary are created for the file. Extracted text is also used for Vector search. OCR and AI generation use credits.

## When should I use Variables?

Use them to manage settings you want to change without deploying code, such as a contact email, announcement text, or feature flags. Fetch them with `GET /variables`, or put them inside content or translations as `{{var_key}}` and they are replaced with the actual value in API responses. Inactive variables are not returned by the API.

## When are Webhooks sent?

They are sent when an event selected for the Table (create, update, delete) occurs **through the REST API**. They are not sent when you edit content in the dashboard. Failed deliveries are not retried automatically, so your receiving server should respond quickly with `2xx` and handle heavy work separately.

## What do I need to use Vector search?

1. **Sync** the Table in the **Vector** menu. Text, categories, file descriptions, and OCR text from published content are embedded.
2. Enable the embedding search API for the Table in **API Endpoints**.
3. Search with `POST /tables/{tableId}/embeddings` for semantic search, or `POST /tables/{tableId}/bm25` for keyword (BM25) search with reranking.

If you edit or newly publish content, sync again for the changes to appear in search results. See [Vector](/docs/ai/vector) for details.

## Can I build a chatbot that answers from my data?

Yes. Create an Agent with instructions and a response style in the **Agents** menu, then send the Agent ID and a question to the Chat API (`POST /chats`). It finds relevant data in your synced Tables and answers. You can choose the search type: semantic search (`vector`), condition-based search (`query`), `hybrid` (default), which uses both, or `bm25`, keyword search reranked by a model. See [Chats](/docs/ai/chats) for details.

## Can TypeScript types be generated automatically?

Yes. In the **TypeScript** menu, you can view and copy request and response types for each Table, as well as common types for translations, variables, files, and categories. Types update when you change Columns. If you need an OpenAPI spec, download the YAML file or turn on the public OpenAPI URL in Project settings.

## Do you support an MCP server?

Yes. Register `https://api.onttle.com/mcp` in an MCP client such as Claude, Claude Code or Cursor, and the AI can look up your project and, after you confirm, change contents, tables and settings. You connect with your Onttle account or an API key; see [MCP Server](/docs/ai/mcp) for details. Inside the dashboard, [Ask AI](/docs/ai/ask-ai) does the same work.

## Can I restore deleted content?

Deleted content cannot be restored from the dashboard. You can roll back unwanted edits to a previous version in **History**, but version history and locking are available on the Pro plan and above. Protect important content with **Lock**, or export it to CSV regularly.

## Can we use the CMS on our own domain?

Yes. Under **Workspace → Domains**, add a subdomain such as `cms.onttle.com` and create the CNAME (and TXT, if shown) records at your DNS provider. Once verified, the CMS is served on that address. Apex domains (`acme.com`) cannot be connected, and Pro and higher plans can connect up to 20 domains per Workspace. See [Custom Domains](/docs/core-features/custom-domains).

## Can we change the CMS colors and logo?

Yes. Under **Workspace → Theme**, pick a preset or set your own light and dark colors, corner radius, font and density, and upload a logo, favicon and tab title. Once saved, the theme applies both when the Workspace is opened on `app.onttle.com` and on custom domains. Owners and admins on Pro and higher plans can edit it. See [Custom Themes](/docs/core-features/custom-themes).
