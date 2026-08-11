---
title: Use Case 11 - Token Optimization Demos
layout: default
parent: Use Cases
grand_parent: Office Hours
nav_order: 11
permalink: /office-hours/use-cases/usecase-11-token-optimization-demos.html
description: "Demo patterns for showing token-efficiency improvements without sacrificing result quality."
---

# 📉 Token Optimization Demo Playbook

## ❓ Question This File Answers

*We need some demos for token optimization.*

## 📝 Summary

The strongest token-optimization demo compares a naive workflow against a context-efficient
workflow on the same task, then shows equal or better engineering output with lower usage.

## ✅ Recommended Demo Set

Run three short demos on the same repository.

1. Prompt quality optimization:
   - Baseline: broad, vague request.
   - Optimized: scoped role, target files, constraints, and acceptance criteria.
2. Context packaging optimization:
   - Baseline: repeatedly pasting large logs/spec chunks.
   - Optimized: repository instructions + linked files + concise evidence blocks.
3. Workflow optimization:
   - Baseline: one giant task in a single chat thread.
   - Optimized: split into small stages with clear checkpoints and validation commands.

## 📊 Metrics To Track

| Metric | Why it matters |
| --- | --- |
| Tokens or request usage | Direct efficiency signal. |
| Time to accepted change | Business productivity signal. |
| Rework rounds | Indicates clarity and quality of first output. |
| Defect escape in review | Confirms optimization did not degrade quality. |

## 🔄 Suggested 20-Minute Demo Flow

1. Pick one realistic task (for example, add validation + tests).
2. Run a baseline prompt and capture usage and outcomes.
3. Run optimized prompt and workflow on the same task.
4. Compare usage, time, review findings, and output correctness.
5. Summarize a repeatable team playbook.

## 💬 Example Optimized Prompt

```text
Act as a senior maintainer for this repository.
Goal: add input validation for order quantity in the API handler.
Scope: only files in src/api and tests/api.
Constraints: no new dependencies, keep public API behavior unchanged except for invalid input handling.
Deliver: minimal diff, tests for invalid cases, and a short validation command list.
```

## 🛡️ Guardrails

- Optimize for outcome quality first, then usage.
- Do not hide context that is required for correctness or safety.
- Keep prompts explicit about non-goals to reduce unnecessary generation.
- Reuse validated prompts and repository instructions across teams.

## 🔑 Key Takeaway

Token optimization is not only about reducing tokens. It is about reducing waste while
maintaining or improving correctness, reviewability, and delivery speed.
