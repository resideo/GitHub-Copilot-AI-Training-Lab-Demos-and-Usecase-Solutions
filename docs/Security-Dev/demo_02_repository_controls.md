---
title: Demo 2 — Repository Controls as Guardrails
layout: default
parent: Demos
grand_parent: Secure Development
nav_order: 2
permalink: /security-dev/demos/demo-2.html
description: "Client-facing guide for configuring rulesets, branch protection, status checks, CODEOWNERS, and pull request review gates in GitHub."
---

# 🛡️ Demo 2 — Repository Controls as Guardrails
{: .no_toc }

**Language:** **Platform policy + GitHub settings**
**Duration:** 8-10 minutes.
**Covers:** rulesets · branch protection · required status checks · CODEOWNERS · pull request approvals

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Goal

Use this guide to configure repository controls in GitHub so your team can enforce safe pull
request workflows in a large codebase. By the end, you should know how to create the controls and
how they appear during pull request review.

## Scenario

This guide is intentionally generic. You do not need to map every folder in the repository.
Instead, apply the control model to your protected branches and sensitive areas, then verify the
behavior in a pull request.

{: .highlight }
> **No clone required.** You only need GitHub repository settings access and one pull request where checks and reviewers are visible.

## What this guide helps you do

- How do you create a ruleset for protected branches?
- When should you use branch protection instead of rulesets?
- How do required status checks get added and enforced?
- How does `CODEOWNERS` change the pull request review flow?
- Which review settings matter most for secure AI-assisted development?
- Where does automatic code review fit alongside human approval?

## Setup

| Setting | Value |
|---|---|
| **Mode** | GitHub repository settings + one open pull request |
| **Model** | Auto |
| **Prep** | Open repository Settings, Rules, Branches, and one pull request with checks visible |

---

## Step-by-step guide

### Step 1 - Create or review rulesets

Open repository settings and go to **Rules**.

Create a new branch ruleset and configure it for the branches that need protection, such as
`main`, `release/*`, or another protected pattern.

Set the ruleset to include the controls your team needs:

- create a new branch ruleset that targets `main`, `release/*`, or another protected pattern
- choose whether the rules apply broadly or to a subset of branches
- require pull requests before merge
- require a minimum number of approvals
- dismiss stale approvals when new commits are pushed
- require code owner review for sensitive paths
- restrict force pushes and direct pushes
- require linear history, if your team uses it
- require status checks to pass before merging

Rulesets scale better than path-specific documentation when the repository is large. They let the
platform enforce policy without depending on people to remember it.

**Expected result:** the target branches now have enforced merge requirements that apply to every
pull request.

### Step 2 - Create branch protection if your repository still uses it

If your repository is not using rulesets yet, create a branch protection rule instead.

Branch protection is older than rulesets, but it still supports the same core controls.

- rulesets are the newer, more flexible control model
- branch protection still enforces the same core ideas
- teams often have a mix during migration

The key idea is the same: merges are controlled by repository policy, not by trust in generated
code.

Configure the branch protection rule with the same baseline controls:

- create a branch protection rule for `main` or another protected branch
- require pull requests before merging
- require approvals before merging
- set the minimum number of approvals
- require code owner review for sensitive paths
- dismiss stale approvals when new commits are pushed
- require status checks to pass before merging

**Expected result:** protected branches cannot be merged without satisfying the configured review
and check requirements.

### Step 3 - Configure required reviews

In the pull request protection settings, configure the review controls that matter most:

- require a pull request before merging
- require approvals before merging
- set the minimum number of approvals
- dismiss stale approvals when new commits are pushed
- require review from code owners
- optionally require conversation resolution before merge

If your organization uses automatic code review, treat it as an additional review signal rather
than a replacement for human approval:

- automated review can flag issues early
- it is additive, not a replacement for branch protections or human approval
- repository policy decides whether the PR can merge

**Expected result:** pull requests show review requirements clearly, and merge remains blocked
until the approval policy is satisfied.

### Step 4 - Add required status checks

Add required status checks in your ruleset or branch protection configuration, then make sure the
repository's CI workflows publish those checks to pull requests.

Typical required checks include:

- build or compile validation
- unit or integration tests
- code scanning
- secret scanning or secret detection
- dependency review
- linting or policy validation

After the checks are configured, open a pull request and confirm they appear as required merge
checks. A failed or missing required check should block the merge.

Say explicitly: "Copilot-authored code is not special here. It must pass the same required checks as any other code."

**Expected result:** pull requests display required checks, and GitHub prevents merging until all
required checks pass.

### Step 5 - Create or update CODEOWNERS

Create or update the repository's `CODEOWNERS` file so sensitive paths automatically request review
from the right teams. You do not need to cover every path in a large repository. Start with the
most sensitive areas.

Use patterns like this:

```text
/sensitive-area/ @team-name
/.github/workflows/ @platform-team
/deploy/ @release-team
```

How this affects pull requests:

- when a changed path matches an entry, GitHub requests review from the listed owners
- if repository settings require code owner review, those approvals become part of the merge gate
- this is how sensitive areas get domain review without maintaining manual reviewer lists on each PR

**Expected result:** when someone changes a protected path, GitHub automatically requests the right
reviewers and, if configured, blocks merge until code owner review is complete.

### Step 6 - Verify the pull request experience end to end

Open one active pull request and verify that contributors can see the protections working:

- requested reviewers, including code owners
- status checks in progress, passed, or failed
- approval count and whether more reviews are required
- merge blocking messages when protections are unmet

This is the most important verification step: repository policy becomes visible as concrete pull
request gates.

**Expected result:** the pull request clearly shows who must review, which checks must pass, and
why merge is blocked if any requirement is missing.

---

## Recommended baseline controls

Use this as a secure default baseline:

- require pull requests before merge
- require at least 1-2 approvals based on team size and risk
- require code owner review for sensitive paths
- dismiss stale approvals on new commits
- require conversation resolution before merge
- require status checks to pass before merge
- block direct pushes to protected branches
- block force pushes except for tightly controlled admins, if allowed at all
- add automated code review and scanning as early warning layers

## How to use this

- share this page as a setup and validation guide, not as a presenter script
- ask the client to configure one protected branch first, then verify behavior in a pull request
- start CODEOWNERS with only sensitive areas if the repository is too large to map fully
- expand required checks over time, but keep build, tests, and security scanning in the first pass

## What to remember

- The repository is the enforcement layer, not the model.
- Rulesets and branch protections make secure review repeatable at scale.
- Required checks turn quality and security expectations into merge blockers.
- CODEOWNERS maps sensitive areas to human accountability.
- Automatic review helps, but repository policy is what actually stops unsafe merges.

## Land the line

For large codebases, do not try to document every path in the guidance. Apply the control model:
rulesets, branch protections, required checks, CODEOWNERS, and pull request approvals.

## Fallback

If you cannot use a live repository, use screenshots from repository settings, a sample
`CODEOWNERS` excerpt, and one pull request screenshot that shows reviewers and required checks.
