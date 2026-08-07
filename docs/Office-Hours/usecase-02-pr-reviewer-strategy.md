---
title: Use Case 2 - PR Reviewer Strategy
layout: default
parent: Use Cases
grand_parent: Office Hours
nav_order: 2
permalink: /office-hours/use-cases/usecase-02-pr-reviewer-strategy.html
description: "Recommended GitHub Copilot pull-request review strategy for enterprise teams."
---

# 🧾 Pull Request Review Strategy with GitHub Copilot

## ❓ Question This File Answers

*When using GitHub Copilot as a PR reviewer, which approach is recommended — adding Copilot as a default reviewer on all PRs, or using it on a need basis? Since each review consumes tokens/requests, would it be more cost-effective to use Copilot reviews selectively (e.g., for larger/complex PRs) rather than on every PR?*

## 📝 Summary

Your proposed approach is the right enterprise default: scoped automation for
high-risk pull requests, and on-demand Copilot review for everything else.

## ✅ Recommended Approach

Use a two-option policy.

1. Automatically request Copilot review only for critical repositories and protected
  target branches (for example `main`, `release/*`, hotfix branches). Implement
  this with GitHub rulesets and branch protection where needed so auto-review is
  enforced only on targeted critical branches.
2. Keep non-critical repositories on manual, on-demand Copilot review, with
   reviewers adding a focused prompt when they request review.

## 🧠 Why This Approach Fits

- It balances review value against usage.
- It avoids noise on routine, low-risk pull requests.
- It gives teams a governance model that can evolve after a pilot.
- It keeps human reviewer attention focused on the pull requests that matter most.

## 💸 Cost Principle

Review frequency usually matters more than pull-request size alone. Automatic re-review
on every new push can multiply usage quickly, especially in active pull requests.

Also note: Copilot code review usage can include both AI credits and GitHub Actions
minutes, so broad automatic policies can increase costs in two dimensions.

## ⚙️ Policy Options

| Strategy | Relative usage | Best fit |
| --- | --- | --- |
| Automatic review on every push | Highest | Rare cases where every update must be screened immediately. |
| One automatic review on every PR | High | Small environments or short pilot periods. |
| Scoped ruleset by branch, repo, or path | Balanced | Recommended default for most enterprise teams. |
| On-demand only | Lowest | Low-risk repositories or very early adoption. |

## 🧭 Rulesets And Branch Protection Notes

- Branch protection rules: enforce required pull request reviews and required status
  checks for protected branches.
- Rulesets: define layered governance by branch/tag patterns, enable active/disabled
  enforcement modes, and combine multiple rules with most-restrictive behavior.
- Scope recommendation: start with a small set of critical repositories and expand
  only after pilot metrics look healthy.
- Re-review caution: only enable review on every push where risk justifies higher
  usage and additional comment noise.

## 🚀 Suggested Starting Policy

1. Auto-review pull requests into default or release branches for critical repositories.
2. Skip draft pull requests.
3. Do not enable automatic re-review on every push by default.
4. Keep non-critical repos as on-demand review, initiated by human reviewers.
5. Add repository instructions so reviews focus on correctness, regression risk, and
   security concerns.
6. Revisit the policy after a pilot instead of locking in a blanket rule on day one.

## 💬 Example Prompt

```text
Review this pull request as an expert in <|domain|>. Focus on correctness, regression risk,
missing tests, and maintainability. Ignore formatting unless it affects readability.
```

## 🔗 Reference Documentation

- Protected branches: https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches
- Rulesets overview: https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets
- Copilot code review (manual request and re-review behavior): https://docs.github.com/en/copilot/how-tos/use-copilot-agents/request-a-code-review/use-code-review
- Copilot code review on GitHub (web flow): https://docs.github.com/en/copilot/how-tos/copilot-on-github/use-copilot-agents/copilot-code-review
- Enterprise enablement and automatic review configuration: https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/enable-copilot-code-review
- Billing and usage considerations for code review: https://docs.github.com/en/copilot/reference/copilot-billing/models-and-pricing#pricing-and-usage-cost-considerations-for-copilot-code-review

## 🛡️ Guardrails

- Set usage budgets and monitor consumption during the pilot.
- Treat Copilot review as a first pass, not as a replacement for human approval.
- Tune the review policy by repository tier rather than applying one setting
  everywhere.
- Remember that Copilot comments do not count as required approvals and do not block
  merges.

## 🔑 Key Takeaway

Selective or scoped review is the better enterprise default because it improves signal
quality and keeps usage aligned with actual risk.
