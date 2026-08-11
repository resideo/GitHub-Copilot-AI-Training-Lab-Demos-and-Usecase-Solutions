---
title: Use Case 12 - Large PR Review Reliability and Spec Validation
layout: default
parent: Use Cases
grand_parent: Office Hours
nav_order: 12
permalink: /office-hours/use-cases/usecase-12-large-pr-spec-validation-ai-review-reliability.html
description: "Layered AI review and specification validation patterns for large pull requests."
---

# 🧪 Large PR Review Reliability and Spec Validation

## ❓ Question This File Answers

*If a developer produced several thousand lines of code for PR, it is impossible to code review manually. We will have to resort to AI to help review. There are 2 issues here. First is that the spec used to produce the code might have false assumptions or is incorrect in some requirements. Second is sometimes I feel AI code review misses bugs and requires several rounds to find an acceptable amount. Are there any good articles on how to deal with this to increase the reliability of AI catching bugs and also to find out whether the code produced actually does what the spec says?*

## 📝 Summary

Treat this as two separate controls:

1. Validate the specification before deep code review.
2. Run layered review for correctness, risk, and coverage instead of a single AI pass.

Make the review **repeatable** by encoding your standards where Copilot code review reads them:

- **Repository custom instructions** (`.github/copilot-instructions.md`) — repo-wide context and
  standards applied automatically to every Copilot code review.
- **Code review instructions / coding guidelines** — natural-language review rules configured in
  repository settings that Copilot code review enforces on each pull request.


## ✅ Recommended Review Model

Use a staged pipeline for large pull requests:

0. Encode standards once:
   - Add **repository custom instructions** (`.github/copilot-instructions.md`) with architecture,
     coding standards, security rules, testing expectations, and known high-risk modules.
   - Add **code review instructions (coding guidelines)** in repository settings so every
     Copilot code review applies the same rules automatically.
1. Spec sanity pass:
   - Ask AI to identify ambiguous, conflicting, or missing requirements.
   - Resolve issues in the spec before finalizing implementation review.
2. Change decomposition:
   - Split the PR into logical slices (feature, data model, API, tests, migrations).
   - Review each slice with targeted prompts.
3. Risk-focused AI review:
   - Separate prompts for correctness, security, performance, and test adequacy.
   - Let Copilot code review run against the repo instructions and coding guidelines.
4. Evidence checks:
   - Require traceability from requirement to code to tests.
5. Human gate:
   - Keep final human approval for merge decisions.

```markdown
<!-- .github/copilot-instructions.md -->
# Review standards

- Enforce the secure-coding checklist; never accept hard-coded secrets.
- Every new public API must be documented and versioned.
- Prefer standard-library solutions before adding third-party dependencies.
- Require negative-path and boundary tests for new logic.
- High-risk modules: `auth/`, `payments/`, `migrations/` — demand extra scrutiny.
```

## 📋 Practical Checklist

| Control | What to verify |
| --- | --- |
| Repository instructions | `.github/copilot-instructions.md` exists and captures standards, security, and high-risk modules. |
| Code review instructions | Coding guidelines are configured in repo settings and enabled for Copilot code review. |
| Spec quality | No ambiguous terms, no contradictory requirements, explicit non-goals. |
| Requirement traceability | Each major requirement maps to code and tests. |
| Test strength | Negative paths and boundary cases are covered. |
| Regression risk | High-impact modules have focused review and validation runs. |
| Residual risk log | Known assumptions and deferred items are documented. |

## 💬 Example Prompts

```text
You are reviewing the specification only.
List ambiguous requirements, hidden assumptions, and missing acceptance criteria.
Return a prioritized list of questions that must be answered before implementation sign-off.
```

```text
Review this PR slice for requirement conformance.
Map each requirement ID to changed files and tests.
Call out any requirement that is partially implemented, untested, or implemented with a conflicting assumption.
```

## 🧠 Reliability Tactics

- Encode standards in **repository instructions** and **code review instructions** so every
  PR is reviewed against the same rules instead of ad-hoc prompts.
- Keep coding guidelines specific and testable (what to flag, why, and where) to cut false
  negatives; refine them as reviews miss or over-report issues.
- Run two independent review prompts with different focus to reduce blind spots.
- Run review with **more than one model** where possible; different models miss different bugs.
- Provide AI with explicit risk areas and known historical defects.
- Require concrete evidence in findings (file paths, behavior impact, test gap).
- Use smaller, scoped diffs where possible instead of monolithic PRs.
- Gate merges in **CI**: run linters, type checks, tests, and coverage as objective checks
  that AI review cannot fake or overlook.
- Iterate deliberately: re-run review after each fix round and stop at an agreed exit
  criterion (e.g., no high-severity findings for one full pass).

## 🔗 Suggested Reading Topics

- Spec-by-example and acceptance criteria quality patterns.
- Risk-based code review strategies for large changes.
- Prompt patterns for traceability and evidence-based AI review.
- GitHub Docs: adding repository custom instructions for Copilot.
- GitHub Docs: configuring coding guidelines for Copilot code review.
- GitHub Docs: using Copilot code review on pull requests.

## 🔑 Key Takeaway

For large PRs, reliability comes from process design plus **encoded standards**: repository
instructions and code review instructions, spec validation first, sliced and multi-model
review, evidence-driven prompts, CI gates, and a human merge gate.
