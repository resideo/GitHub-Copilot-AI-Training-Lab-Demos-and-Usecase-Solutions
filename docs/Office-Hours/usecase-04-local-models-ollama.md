---
title: Use Case 4 - Local Models with Ollama
layout: default
parent: Use Cases
grand_parent: Office Hours
nav_order: 4
permalink: /office-hours/use-cases/usecase-04-local-models-ollama.html
description: "Practical guidance for local-model developer workflows using Ollama and Copilot CLI."
---

# 🧠 Local Models with Ollama in the Developer Workflow

## 🎯 Objective

This note describes a practical way to implement an agentic developer workflow
with locally hosted models through Ollama, and where GitHub Copilot CLI and the
Copilot Extensions SDK fit.

## ✅ Recommended Solution

The most practical starting point is:

1. Use Ollama as the local model runtime.
2. Use GitHub Copilot CLI as the developer-facing agent experience.
3. Use GitHub MCP Server for repository, issue, pull request, and workflow context.
4. Model the workflow as three stages: triage, fix, and validation.
5. Use the Copilot Extensions SDK only if the goal is to turn the workflow into
   a reusable Copilot integration for multiple users.

This is the recommended path because GitHub Copilot CLI already supports custom
providers, including locally running models such as Ollama, and it supports MCP
servers for tool and context integration.

## 🧭 Where Copilot CLI Fits

GitHub Copilot CLI is the fastest way to prove the workflow end to end.

It is a good fit when the goal is to:

- run the workflow from the terminal in a real repository
- use a local model through Ollama
- add GitHub and tool context through MCP
- iterate quickly without building a custom application first

For most proof-of-concept and evaluation scenarios, Copilot CLI should be the first
implementation choice.

## 🧰 Where the Copilot Extensions SDK Fits

The Copilot Extensions SDK is appropriate when the goal is to build a reusable
Copilot experience rather than a local proof of concept.

It is a good fit when the goal is to:

- expose the workflow as a reusable Copilot-integrated agent
- connect Copilot to internal APIs, platforms, or remediation services
- provide a governed experience for multiple teams

It is usually not the first step for a local Ollama-based implementation. In most cases,
the better sequence is to validate the workflow with Copilot CLI first, then
package it as an extension if the use case proves valuable.

## 🏗️ Recommended Architecture

The recommended implementation pattern is:

1. The engineer launches Copilot CLI from the repository root.
2. Copilot CLI uses Ollama as the model provider.
3. GitHub MCP Server supplies repository and GitHub context.
4. Copilot CLI executes the workflow in three stages: triage, fix, and validate.

This gives a practical agentic workflow without requiring a custom orchestration
application at the start.

## ⚡ Implementation Option 1: Fastest Setup

The fastest way to get started is the Ollama integration for Copilot CLI.

1. Install Ollama:
   [https://ollama.com/download](https://ollama.com/download)
2. Install GitHub Copilot CLI:
   [https://docs.github.com/en/copilot/how-tos/copilot-cli/set-up-copilot-cli/install-copilot-cli](https://docs.github.com/en/copilot/how-tos/copilot-cli/set-up-copilot-cli/install-copilot-cli)
3. Start Copilot CLI through Ollama:

```text
ollama launch copilot
```

4. Optionally select a specific model:

```text
ollama launch copilot --model qwen3.5
```

5. Launch the workflow from the repository root and prompt it to inspect an
   error, propose a fix, and validate the change.

For scripted or non-interactive scenarios, Ollama also supports headless mode:

```text
ollama launch copilot --model qwen3.5 --yes -- -p "how does this repository work?"
```

## ⚙️ Implementation Option 2: Manual Provider Setup

For teams that want explicit configuration, Copilot CLI can connect to Ollama
through its OpenAI-compatible endpoint.

```text
COPILOT_PROVIDER_BASE_URL=http://localhost:11434/v1
COPILOT_PROVIDER_API_KEY=
COPILOT_PROVIDER_WIRE_API=responses
COPILOT_MODEL=qwen3.5
copilot
```

Implementation notes:

- Use a model that supports tool calling and streaming.
- Use a model with a large context window for repository-scale tasks.
- Start from a repository root so Copilot CLI can reason over the local project.

## 🔌 Add GitHub Context with MCP

GitHub MCP Server is the recommended way to give the workflow repository and
GitHub context.

Key points:

- GitHub MCP Server is built into Copilot CLI and is available without
  additional configuration.
- Additional MCP servers can be added when the workflow needs more tools or
  external context.
- MCP is the preferred integration pattern for issues, pull requests, workflows,
  and related GitHub context.

Example command for adding another MCP server:

```text
copilot mcp add context7 -- npx -y @upstash/context7-mcp
```

Useful management commands:

```text
copilot mcp list
copilot mcp get SERVER-NAME
```

## 🔄 Suggested 3-Stage Workflow

For the scenario "log error -> investigate -> propose fix," use a simple
three-stage pattern.

### 1️⃣ Stage 1: Triage

Goal: identify the most likely root cause and the files to inspect first.

Example prompt:

```text
Review this error log, identify the most likely root cause, and list the files
that should be inspected first.
```

### 2️⃣ Stage 2: Fix

Goal: propose or apply the smallest safe code change.

Example prompt:

```text
Based on the root cause, propose the smallest safe code change, explain the
tradeoffs, and update the affected files.
```

### 3️⃣ Stage 3: Validate

Goal: confirm the fix with the narrowest reasonable validation step.

Example prompt:

```text
Validate the proposed fix, recommend the narrowest test or command to confirm
it, and summarize any remaining risk.
```

This structure is easy to explain, easy to validate, and easy to operationalize.

## 🧪 Recommended Proof-of-Concept Sequence

1. Prove the workflow with Copilot CLI and Ollama.
2. Add GitHub MCP Server and any additional MCP tools needed.
3. Standardize the prompts for triage, fix, and validation.
4. Evaluate whether the workflow should remain CLI-based or be packaged as a
   Copilot Extension.

This keeps the first phase lightweight while leaving room for a more governed
integration later.

## 📚 Official Documentation

- GitHub Copilot CLI overview:
  [https://docs.github.com/en/copilot/concepts/agents/copilot-cli/about-copilot-cli](https://docs.github.com/en/copilot/concepts/agents/copilot-cli/about-copilot-cli)
- Install GitHub Copilot CLI:
  [https://docs.github.com/en/copilot/how-tos/copilot-cli/set-up-copilot-cli/install-copilot-cli](https://docs.github.com/en/copilot/how-tos/copilot-cli/set-up-copilot-cli/install-copilot-cli)
- Using GitHub Copilot CLI:
  [https://docs.github.com/en/copilot/how-tos/copilot-cli/use-copilot-cli/overview](https://docs.github.com/en/copilot/how-tos/copilot-cli/use-copilot-cli/overview)
- Using your own model provider in Copilot CLI:
  [https://docs.github.com/en/copilot/concepts/agents/copilot-cli/about-copilot-cli#using-your-own-model-provider](https://docs.github.com/en/copilot/concepts/agents/copilot-cli/about-copilot-cli#using-your-own-model-provider)
- Ollama integration for Copilot CLI:
  [https://docs.ollama.com/integrations/copilot-cli](https://docs.ollama.com/integrations/copilot-cli)
- Adding MCP servers for GitHub Copilot CLI:
  [https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-mcp-servers](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-mcp-servers)
- Model Context Protocol in GitHub Copilot:
  [https://docs.github.com/en/copilot/concepts/context/mcp](https://docs.github.com/en/copilot/concepts/context/mcp)
- About building Copilot Extensions:
  [https://docs.github.com/en/copilot/building-copilot-extensions/about-building-copilot-extensions](https://docs.github.com/en/copilot/building-copilot-extensions/about-building-copilot-extensions)

## 🔗 Public Repositories and Examples

- GitHub Copilot CLI:
  [https://github.com/github/copilot-cli](https://github.com/github/copilot-cli)
- GitHub MCP Server:
  [https://github.com/github/github-mcp-server](https://github.com/github/github-mcp-server)
- Ollama:
  [https://github.com/ollama/ollama](https://github.com/ollama/ollama)
- Copilot Extensions SDK:
  [https://github.com/copilot-extensions/preview-sdk.js](https://github.com/copilot-extensions/preview-sdk.js)
- Copilot Extension hello-world example:
  [https://github.com/copilot-extensions/blackbeard-extension](https://github.com/copilot-extensions/blackbeard-extension)
- Copilot Extension function-calling example:
  [https://github.com/copilot-extensions/function-calling-extension](https://github.com/copilot-extensions/function-calling-extension)

## 🏁 Final Recommendation

If the objective is to show how the workflow can be implemented quickly, the
recommended answer is: use Ollama for local model execution, GitHub Copilot CLI
for the agent experience, and GitHub MCP Server for repository and GitHub
context.

If the workflow needs to become a reusable product or governed integration,
validate it first with Copilot CLI, then package it with the Copilot Extensions
SDK.
