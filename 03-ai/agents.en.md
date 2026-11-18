---
title: 'Agents'
description: 'How to configure the AI Agents that generate responses for the Chats API.'
category: 'ai'
slug: 'agents'
locale: 'en'
apiVersion: 'v1'
order: 1
updatedAt: '2026-11-18'
---

# Agents

An Agent is an AI assistant configuration that generates responses for the [Chats API](/docs/ai/chats). Once you define the instructions and response parameters, the Chats API searches your [Vector-synced](/docs/ai/vector) contents and generates answers according to the Agent settings. Responses are generated with **GPT-6.0 Luna** by default.

**Prerequisites:** Creating and editing Agents requires the `OWNER` or `ADMIN` role in the Project.

---

## What an Agent Does

Every Chats API request requires an `agent_id`. When a request arrives, the following happens:

1. The user message is embedded and semantically matched against Vector-synced contents.
2. The retrieved contents and conversation history are assembled into context.
3. A response is generated according to the Agent's Instructions and response parameters.

In short, Vector sync decides **what to search**, and the Agent decides **how to answer**.

---

## Configuring an Agent in the Dashboard

1. Click **Agents** in the left menu of your Project.
2. Click the **New Agent** button.
3. Fill in the settings below and save.

> **[IMAGE AREA]** Agent configuration screen — name, instructions, and response parameter UI

### Settings

| Field                 | Required | Description                                                                      |
| --------------------- | -------- | -------------------------------------------------------------------------------- |
| **Name**              | Yes      | Agent name                                                                       |
| **Description**       | -        | A note describing what the Agent is for                                          |
| **Instructions**      | -        | System instructions passed to the AI. Define the role, tone, and answering rules |
| **Max Tokens**        | -        | Maximum tokens per response. Leave empty for no limit                            |
| **Knowledge Files**   | -        | Files the Agent always references. Select up to 5 files uploaded to the Project  |
| **Creativity**        | -        | Response creativity (temperature). `0`–`2`, default `1`                          |
| **Top P**             | -        | Token sampling range. `0`–`1`, default `1`                                       |
| **Presence Penalty**  | -        | Encourages mentioning new topics. `-2`–`2`, default `0`                          |
| **Frequency Penalty** | -        | Discourages repeating the same phrases. `-2`–`2`, default `0`                    |

---

## Instructions Examples

**Customer support assistant**

```
You are a customer support assistant for our service.
- Answer only based on the provided contents.
- If there is no supporting content, say you don't know and point to the support channel.
- Keep answers polite and within 3 sentences.
```

**Developer docs guide**

```
You are a developer documentation guide.
- Include code examples when available.
- List related document titles at the end of each answer.
```

---

## Finding the Agent ID

Get the ID to use as `agent_id` in Chats API requests from the Agent list on the **Agents** page.

```json
{
  "agent_id": "Ag3ntId_ExAmPle123",
  "message": "What is the refund policy?"
}
```

---

## Next Steps

- [Chats](/docs/ai/chats) — Call the Chats API with your configured Agent.
- [Vector](/docs/ai/vector) — Sync the contents your Agent will search into vectors.
