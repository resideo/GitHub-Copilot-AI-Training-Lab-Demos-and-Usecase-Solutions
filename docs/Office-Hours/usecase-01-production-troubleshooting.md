---
title: Use Case 1 - Production Troubleshooting
layout: default
parent: Use Cases
grand_parent: Office Hours
nav_order: 1
permalink: /office-hours/use-cases/usecase-01-production-troubleshooting.html
description: "How to use GitHub Copilot for production troubleshooting and root cause analysis."
---

# 🚨 Production Troubleshooting and Root Cause Analysis with GitHub Copilot

## ❓ Question This File Answers

*For production issue troubleshooting, log analysis, and RCA, which Copilot tool do you recommend and why?*

## 📝 Summary

When an engineer needs to move from a log, stack trace, or failing test to the likely
code path and validation steps, GitHub Copilot Chat in VS Code is the best starting
point.

## ✅ Recommended Approach

Use GitHub Copilot Chat in VS Code, ideally in Agent Mode, as the primary investigation
surface. Add MCP only when the relevant logs, telemetry, or operational context lives
outside the repository.

## 🧠 Why This Approach Fits

- It helps engineers connect the failing signal to the owning code in one workflow.
- Agent Mode supports stepwise investigation, including code inspection and validation.
- It keeps root cause analysis focused on evidence before code changes are proposed.

## 🛠️ Best-Fit Copilot Surface

| Copilot surface | Best fit | Why |
| --- | --- | --- |
| Copilot Chat in Agent Mode | Full investigation | Best when the engineer needs code, logs, and validation steps in the same session. |
| Copilot Chat in Ask Mode | Focused analysis | Best for a pasted stack trace, failing test, or targeted RCA question. |
| Copilot on GitHub Mobile | Fast triage | Useful for a quick summary of a failing workflow or incident clue while away from a laptop. |
| Copilot on GitHub.com | Change correlation | Useful for connecting a failure to a recent pull request, issue, or workflow run. |


## 🔄 Suggested Workflow

1. Capture the failing signal: stack trace, log excerpt, failing test, or workflow
	output.
2. Ground the analysis in the real repository or affected files.
3. Ask for root cause analysis before asking for a fix.
4. Validate the hypothesis with a test, trace, or code-path confirmation.
5. Implement the fix and document the RCA.

## 💬 Example Prompt

```text
Act as an on-call engineer. Analyze this incident summary and the attached logs.
Identify the most likely root cause, the code areas I should inspect next, and the
minimum validation steps to confirm the hypothesis. Do not change code yet.
```

## 📌 Example Outcome

> The most likely cause is a null value returned from a cache lookup and dereferenced
> without a guard in the discount path. The issue appears intermittent because it occurs
> only on cache misses after eviction. Validate by reproducing the cache-miss path in a
> unit test before implementing the fallback.

## 🛡️ Guardrails

- Remove secrets, credentials, and PII from logs before prompting.
- Require a reproducing test or equivalent validation before shipping the fix.
- Keep a human in the decision loop for production remediation.

## 🔑 Key Takeaway

Copilot is strongest in troubleshooting when it helps engineers connect the failing
signal to the owning code and validate the hypothesis before changing production
behavior.
