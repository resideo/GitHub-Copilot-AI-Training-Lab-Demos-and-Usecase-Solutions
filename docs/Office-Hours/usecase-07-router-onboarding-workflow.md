---
title: Use Case 7 - Router Onboarding Workflow
layout: default
parent: Use Cases
grand_parent: Office Hours
nav_order: 7
permalink: /office-hours/use-cases/usecase-07-router-onboarding-workflow.html
description: "Persistent Copilot context and reusable assets for repeatable router onboarding workflows."
---

# 🛜 Router Onboarding Workflow with Persistent Copilot Context

## ❓ Question This File Answers

*I'm currently working on onboarding new routers into the WiFi IOP Test Framework. For every new router, I have to manually log in, explore its supported configurations, send cURL or REST API requests to understand its parameters and payloads, and then update our common `router_configuration.json` accordingly. Since this process is repeated for every router, I also end up explaining the same project context to GitHub Copilot each time before it can assist me. I'm looking for a way to make Copilot understand our router onboarding workflow permanently, using repository instructions, custom prompts, agents, or any other approach, so it can help onboard new routers with minimal manual guidance.*

## 📝 Summary

Treat this as a repeatable repository workflow, not as a fresh chat problem every time.
The best starting pattern is to combine repository instructions, checked-in prompt assets,
and one structured onboarding template. Add an agent or MCP only if router discovery must
call external tools or systems directly.

## ✅ Recommended Approach

Create a permanent Copilot enablement layer inside the repository:

- Put stable project context in repository instructions so Copilot starts with the WiFi IOP
  onboarding workflow, expected inputs, and output format already in scope.
- Check in a reusable prompt file for new-router onboarding so engineers do not restate the
  same context every time.
- Add a router onboarding checklist or template markdown file that captures the exact facts to
  collect: login path, auth method, REST endpoints, payload shapes, supported bands, security
  modes, and any vendor quirks.
- Define the target schema and update rules for `router_configuration.json`, including required
  fields, naming rules, defaults, and validation expectations.
- If the workflow later needs live interrogation of routers or internal systems, expose those
  steps through MCP tools rather than long manual chat instructions.

## 🧱 Recommended Repository Assets

| Asset | Purpose |
| --- | --- |
| `copilot-instructions.md` or scoped instructions file | Store stable domain context, workflow rules, schema expectations, and guardrails. |
| `prompts/router-onboarding.prompt.md` | Reusable prompt that tells Copilot how to analyze evidence and prepare the configuration update. |
| `templates/router-onboarding-checklist.md` | Structured intake form for each new router. |
| `docs/router_configuration_schema.md` | Human-readable explanation of the shared JSON fields and examples. |
| Sample router evidence folder | Store sanitized cURL examples, payloads, screenshots, or notes from one completed onboarding. |

## 🔄 Suggested Workflow

1. Capture router evidence in a standard checklist or intake document.
2. Ask Copilot to summarize the supported configuration surface from the captured evidence.
3. Ask Copilot to draft the new `router_configuration.json` entry using the repository schema rules.
4. Review the generated config against one known-good example.
5. Run a validation step or test for the updated JSON before merging.
6. Save any newly discovered vendor-specific rules back into the repository instructions or template.

## 💬 Example Prompt

```text
You are helping onboard a new router into the WiFi IOP Test Framework.

Use the repository onboarding rules, the router configuration schema, and the attached
router evidence to do three things:
1. Summarize the router's supported configuration options.
2. Draft the new router_configuration.json entry.
3. List any missing details or ambiguities that still require manual verification.

Do not invent unsupported fields. Reuse existing naming conventions and defaults from the
repository examples.
```

## 🧠 When To Use Instructions, Prompts, Agents, And MCP

| Mechanism | Best use in this scenario |
| --- | --- |
| Repository instructions | Stable project context, schema rules, naming conventions, and onboarding workflow steps. |
| Prompt files | Reusable task framing for each new router onboarding run. |
| Agent | Useful when you want a repeatable multi-step flow such as read evidence, compare examples, update JSON draft, and propose validation. |
| MCP | Useful only when Copilot must query live router APIs, internal knowledge bases, or test systems through approved tool contracts. |

## 🛠️ Practical Implementation Guidance

- Start with instructions plus prompt files before introducing custom agents.
- Keep the repository examples sanitized and representative so Copilot can pattern-match safely.
- Document one or two completed router onboardings as canonical examples.
- Explicitly tell Copilot what must never be guessed, such as unsupported bands, auth flows,
  or vendor-specific payload semantics.
- If the JSON file is sensitive to ordering or required fields, document that in the schema notes.
- If the workflow becomes highly repetitive, create a dedicated onboarding agent prompt that
  always asks for missing evidence before drafting the final JSON.

## 🛡️ Guardrails

- Do not rely on Copilot to infer undocumented router capabilities.
- Keep credentials, live device secrets, and internal endpoints out of checked-in prompt assets.
- Require a validation step before accepting any generated `router_configuration.json` change.
- Prefer sanitized captured API samples over live sessions when building reusable context.

## 🔑 Key Takeaway

The fastest way to reduce repeated onboarding effort is to move stable router-onboarding
knowledge into repository instructions, reusable prompts, and checked-in examples. Add
agents or MCP only after the repository itself gives Copilot enough persistent context to
draft accurate configuration updates with minimal re-explanation.
