---
title: Demo 4 — Bring Your Own Model (BYOK)
layout: default
parent: Demos
grand_parent: Advanced
nav_order: 4
permalink: /advance/demos/demo-4.html
description: "Add the Marketplace openai/o3 model into VS Code's model picker using a GitHub token, then use it in chat and Agent mode."
---

# 🔌 Demo 4 — Bring Your Own Model (BYOK) with GitHub Models `o3`
{: .no_toc }

**Surface:** VS Code Copilot Chat — **model picker / Manage Language Models**
**Theme:** Add the Marketplace **`openai/o3`** model into VS Code's own model
picker using an **API key (a GitHub token)** — *not* the Node.js playground.

> **Focus:** Show that "Bring Your Own Key" (BYOK) lets you use *any* compatible
> model inside the normal VS Code chat/agent experience. We connect GitHub
> Models' OpenAI-compatible endpoint and pick `o3` from the model dropdown.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Why NOT the Marketplace code snippet

The snippet on the Marketplace page (`package.json` + `sample.js` + `node
sample.js`) runs the model in a **standalone script**. That is fine for a REST
call, but it does **not** put the model inside VS Code chat.

For a live demo we instead register the same endpoint through VS Code's
**Manage Language Models** so `o3` appears in the chat model picker and can drive
Agent mode, edits, and tools.

Both paths use the **same two facts** from the Marketplace page:

| Fact | Value |
|------|-------|
| Endpoint | `https://models.github.ai/inference` |
| Model name | `openai/o3` |
| Auth | a **GitHub token** with **`models:read`** sent as the API key |

---

## Prerequisites

- VS Code with GitHub Copilot Chat (recent version — has the **Custom Endpoint**
  provider).
- Individual Copilot plan, OR (Business/Enterprise) your admin has enabled the
  **"Bring Your Own Language Model Key in VS Code"** policy at
  <https://github.com/settings/copilot/features>.

> Note: the old **"OpenAI Compatible"** provider is **deprecated**. Use the
> **Custom Endpoint** provider (it supports Chat Completions / Responses /
> Messages API types).

---

## Step 1 — Create the API key (a GitHub token)

The "API key" for GitHub Models is just a GitHub Personal Access Token with the
models permission.

1. Open <https://github.com/settings/personal-access-tokens/new>.
2. Create a **fine-grained** token.
3. Under **Permissions → Account permissions** (or the models section), grant
   **Models = Read** (`models:read`). *Without this you get `unauthorized`.*
4. Generate and **copy** the token (starts with `github_pat_...`).

> Security: treat the token like a password. Do not commit it, do not paste it
> into source files, and never log it. VS Code stores it in the OS secret store.

---

## Step 2 — Add `o3` via Manage Language Models (the demo core)

This is the heart of the demo. Follow it click-by-click.

### 2a. Open the Language Models editor

There are two ways — use either:

- **From chat:** In the Chat view, click the **model name** at the bottom of the
  chat input box (the model picker dropdown). At the bottom of that dropdown,
  click **Manage Language Models…** (gear icon).
- **From the Command Palette:** Press `Ctrl+Shift+P`, type
  `Chat: Manage Language Models`, and press Enter.

A **Language Models** editor opens (a panel listing every model you have).

### 2b. Start adding a model

1. Click the **Add Models** button (top of the Language Models editor).
2. A dropdown (Quick Pick) appears listing providers: *Anthropic, Azure, Gemini,
   OpenAI, **Custom Endpoint**,* and others.
3. Select **Custom Endpoint**.

   > Why Custom Endpoint? GitHub Models speaks the OpenAI **Chat Completions**
   > API, and Custom Endpoint is the provider that connects to any such
   > endpoint. (It replaces the old, now-deprecated "OpenAI Compatible" option.)

### 2c. Answer the prompts VS Code shows

VS Code asks you a short series of questions, one at a time:

| Prompt | What to type |
|--------|--------------|
| **Group name** | `GitHub Models` (a label for this provider group) |
| **Display name** | `o3` (the name you'll see in the picker) |
| **API key** | paste your **GitHub token** from Step 1 (`github_pat_...`) |
| **API type** | choose **Chat Completions** |

After the last prompt, VS Code opens a file called **`chatLanguageModels.json`**.

### 2d. Fill in `chatLanguageModels.json`

Replace the file's contents with **exactly** this, paste your token in place of
`PASTE_YOUR_GITHUB_TOKEN_HERE`, then **save** (`Ctrl+S`):

```jsonc
[
  {
    "name": "GitHub Models",
    "vendor": "customendpoint",
    "apiKey": "PASTE_YOUR_GITHUB_TOKEN_HERE",
    "apiType": "chat-completions",
    "models": [
      {
        "id": "openai/o3",
        "name": "o3 (GitHub Models)",
        "url": "https://models.github.ai/inference/chat/completions",
        "toolCalling": true,
        "vision": false,
        "thinking": true,
        "maxInputTokens": 12000,
        "maxOutputTokens": 4000,
        "supportsReasoningEffort": ["low", "medium", "high"],
        "reasoningEffortFormat": "chat-completions"
      }
    ]
  }
]
```

**What each line means:**

- `name` / `vendor` — the provider group. `vendor` must be `customendpoint`.
- `apiKey` — your GitHub token; VS Code moves it into the OS secret store on save.
- `apiType: "chat-completions"` — tells VS Code to use the OpenAI Chat
  Completions request shape.
- `models[].id` — the **exact** model name sent to GitHub Models: `openai/o3`.
  Do not shorten it to just `o3`.
- `models[].name` — the friendly label shown in the picker (`o3 (GitHub Models)`).
- `url` — the **full** endpoint URL. It is the Marketplace endpoint
  `https://models.github.ai/inference` **plus** `/chat/completions`.
- `toolCalling: true` — **required** so the model shows up and works in **Agent
  mode**. Without it the model is hidden from agent chats.

### 2e. Confirm it registered

Go back to the **Language Models** editor. You should now see a **GitHub Models**
group with **o3 (GitHub Models)** listed under it. If it isn't there yet, save the
JSON again or **restart VS Code**.

---

## Step 3 — Select and use `o3`

### 3a. Pick the model

1. Go to the Chat view and click the **model picker** (the model name at the
   bottom of the chat input).
2. Scroll to the **GitHub Models** group and click **o3 (GitHub Models)**.
   - If it's not listed, **restart VS Code** (`Developer: Reload Window` or fully
     reopen), then reopen the picker.
3. The model name at the bottom of the chat box now reads **o3 (GitHub Models)**.

### 3b. Send a first test message (proves the key works)

Type a simple prompt and press Enter:

```
What is the capital of France? Answer in one word.
```

You should get back `Paris`.

### 3c. Try it in Agent mode (proves tool calling works)

1. Switch the chat mode selector to **Agent**.
2. Ask a task that requires reading the repo, for example:

   ```
   List the public functions in device_registry.py
   ```

3. Because `toolCalling: true` is set, o3 can call VS Code's file tools and
   answer from the actual file — confirming BYOK works with agents and tools,
   not just plain chat.

### 3d. (Optional) Adjust Thinking Effort

Open the model picker again and click the **`>`** arrow next to
**o3 (GitHub Models)**. Choose **Low**, **Medium**, or **High**. Higher effort =
more reasoning (and more tokens). The label updates, e.g. *"o3 (GitHub Models) ·
High"*.

### 3e. Update the token later

If the token expires or you rotate it: **Manage Language Models** → click the
**gear icon** next to the **GitHub Models** provider → update the API key.

---

## Talking points (BYOK vs the built-in models)

- **Same UI, your key:** BYOK reuses the full VS Code chat/agent/tools
  experience but routes requests to *your* provider and *your* token.
- **What still needs Copilot:** semantic search, inline code completions, and
  embeddings still require a GitHub account/Copilot plan — BYOK covers **chat**
  and utility tasks only.
- **Reconfigure anytime:** Manage Language Models → gear next to the provider →
  update the token or endpoint.
- **Utility models:** if you sign out of GitHub entirely, set
  `chat.utilityModel` / `chat.utilitySmallModel` to your BYOK model so title and
  commit-message generation keep working.

---

## Troubleshooting

| Symptom | Fix |
|--------|-----|
| `unauthorized` | Token is missing **`models:read`**; regenerate with that permission. |
| Model not in picker | Ensure `toolCalling: true`; **restart VS Code**. |
| BYOK option missing (Business/Enterprise) | Admin must enable the BYOK policy in Copilot settings. |
| 404 / wrong route | `url` must end with `/chat/completions`. |
| `400 ... 'temperature' does not support 0.1` | o3 is a reasoning model and only allows the default temperature. Add `"thinking": true` (and `supportsReasoningEffort`) so VS Code stops sending a custom temperature. |
| `413 tokens_limit_reached` | Free-tier o3 caps requests at **16,000 tokens**. Keep `maxInputTokens + maxOutputTokens ≤ 16000`; use **Ask mode** to shrink context; or enable paid billing. |
| Rate limited | GitHub Models free tier has rate limits; enable billing for higher limits. |
