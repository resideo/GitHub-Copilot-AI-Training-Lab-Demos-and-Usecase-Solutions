---
title: Demo 3 — Responsible AI Review
layout: default
parent: Demos
grand_parent: Secure Development
nav_order: 3
permalink: /security-dev/demos/demo-3.html
description: "Client-facing guide for setting up Copilot review instructions, path-based review rules, and content exclusion in GitHub and VS Code."
---

# 🔎 Demo 3 — Responsible AI Review
{: .no_toc }

**Language:** **Markdown + YAML + Diff**
**Duration:** 8-10 minutes
**Covers:** repository instructions · path-based review rules · content exclusion

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Goal

Use this guide to set up responsible AI review controls so Copilot follows the
same review standards in the IDE and on GitHub.com, while excluded content stays
out of Copilot context and human reviewers remain the final release gate.

## Scenario

This guide is designed to be shared directly with a client. It explains which
instruction files to create, how to scope them, how to exclude sensitive
content, and how to verify the behavior during local review and pull request
review.

{: .highlight }
> **No clone required.** You only need access to a repository, one changed file, and one pull request if you want to verify the full workflow.

## What this guide helps you do

- Create repository-wide Copilot review instructions
- Add path-based instructions for specific languages or folders
- Configure content exclusion for sensitive files
- Verify that Copilot follows the same rules in VS Code and on GitHub.com
- Confirm that human review and repository policy remain the final merge gate

> Replace the example paths in this guide with the folders, file types, and
> sensitive files from your own repository.

## Configuration files

All file names and paths below are examples. Replace them with the equivalent
locations used in your repository.

| File | Purpose |
|---|---|
| Example: `.github/copilot-instructions.md` | Repository-wide review policy used in chat and code review |
| Example: `.github/instructions/review.instructions.md` | Path-based review rules for a language or folder |
| Example: `.vscode/settings.json` | Copilot content exclusion example for local development |
| `src/example-module/example_file.py` | Example application file to test review behavior |

### Example: `.github/copilot-instructions.md`

```markdown
# Copilot Repository Instructions

Review every change in this order:

1. Security first: reject fail-open auth or unsafe defaults.
2. Secrets and excluded content: no hardcoded secrets, no .env in diff.
3. Correctness: ensure behavior matches PR intent.
4. Negative tests: require failure-case coverage.
5. Policy fit: confirm repo and path rules are followed.
6. Summary verdict: what must change before merge.
```

### Example: `.github/instructions/review.instructions.md`

```markdown
---
applyTo: "src/**/*.py,app/**/*.py,tests/**/*.py"
---

- Fail closed for auth and permission checks.
- No hardcoded credentials or secret material.
- Require type annotations and clear docstrings.
- Require negative tests for each new operation.
```

### Example: `.vscode/settings.json`

```json
{
   "github.copilot.chat.codeGeneration.instructions": [
      {
         "file": ".github/copilot-instructions.md"
      }
   ],
   "github.copilot.contentExclusion": {
      "enabled": true,
      "repositories": {
         "*": [
            "/.env",
            "**/.env",
            "**/*.env",
            "/requirements.txt"
         ]
      }
   }
}
```

---

## Setup

| Setting | Value |
|---|---|
| **Mode** | Ask + PR Copilot review |
| **Model** | Auto |
| **Prep** | Keep one changed Python file and one PR open |

---

## Optional review prompts

**Prompt 1 — local IDE review**

```text
Review this change for security, correctness, and policy risks.
Call out auth bypass, missing negative tests, and unsafe defaults.
```

**Prompt 2 — PR review summary**

```text
Summarize this pull request against our repository and path-based review instructions.
List only must-fix issues before merge.
```

---

## Step-by-step guide

### Step 1 - Create repository-wide review instructions

Create a repository-wide instruction file, for example
`.github/copilot-instructions.md`, and define the review order you want
Copilot to follow for every chat request and every review in the repository.

Use this file for project-wide standards such as:

- security-first review order
- secret handling expectations
- correctness checks
- test expectations
- summary format for must-fix findings

The repository-wide instruction file applies across the entire repository. You
do not need globs for this file.

**Expected result:** Copilot has a single project-wide review policy that can be
reused in both local review and pull request review.

### Step 2 - Add path-based instructions

Create one or more path-based instruction files, for example under
`.github/instructions/`, for language-specific or folder-specific review rules.

For example, this file applies only to Python files under common source and
test folders:

- front matter scopes the instruction file with `applyTo`
- the rules load only when a matching file is being edited or reviewed
- this is where you add language-specific requirements such as fail-closed
   auth, type annotations, docstrings, or negative tests

> Rule of thumb: repository-wide file = project-wide standards; path-based
> `*.instructions.md` = rules for specific file types or folders.

**Expected result:** Copilot applies the repository-wide policy everywhere, then
adds extra rules only for matching files.

---

### Step 3 - Verify how instructions apply in the IDE

Open a changed file that matches the path-based instructions, such as the
example file `src/example-module/example_file.py`, and ask Copilot to review it.

What should happen:

- repository-wide instructions always apply
- path-based instructions apply in addition to the repository-wide file when the
   file path matches the `applyTo` pattern
- review comments should reflect both sets of rules

If your environment shows active instructions in chat references or used
context, verify that both instruction files are active.

**Expected result:** the review output reflects project-wide standards and the
extra rules for the matching file type.

### Step 4 - Verify how instructions apply on GitHub.com

Open a pull request that includes files matching your instruction scope and
request Copilot code review.

What should happen:

- Copilot uses the repository-wide instruction file for the full review
- for matching files in the diff, Copilot also applies the relevant path-based
   instruction file
- inline review comments should follow the same logic used in the IDE

**Expected result:** one set of instructions produces consistent guidance across
VS Code and GitHub.com.

---

### Step 5 - Test a dangerous-default review scenario

Use a small auth change that weakens failure behavior to confirm the review
policy catches it.

### Sample diff

```diff
 bool isAuthorized(const Request& request) {
     auto header = request.headers.find("X-Api-Key");
-    if (header == request.headers.end()) {
-        return false;
-    }
+    if (header == request.headers.end()) {
+        return true;
+    }
     return header->second == config.apiKey;
 }
```

Review the change with both Copilot and a human checklist:

1. Does the logic now default to allow?
2. What negative tests are missing?
3. Would this violate repo instructions or secure-coding guidance?
4. Would you approve this change if the AI review missed it?

The repository-wide and path-based instructions should both push the review
toward the same result: flag the allow-by-default bug and require negative test
coverage.

**Expected result:** both local review and PR review identify the auth change as
high risk and block approval until it is corrected.

---

### Step 6 - Configure content exclusion in VS Code

Configure content exclusion in **Settings -> Copilot -> Content exclusion** or
in an editor settings file such as `.vscode/settings.json`. This example excludes:

```yaml
- "/.env"
- "**/.env"
- "**/*.env"
- "/requirements.txt"
```

Then verify the behavior in VS Code:

1. Create a scratch sensitive file, for example `.env`, at the repo root with
   fake content:

   ```text
   API_KEY=sk-demo-not-a-real-secret
   DB_PASSWORD=demo-password
   ```

2. Confirm exclusion status. With the example `.env` file open, the Copilot
   status should show that Copilot is disabled for this file because of content
   exclusion.

3. Confirm that inline completions are blocked in the excluded file.

4. Confirm that chat ignores the file. Open another excluded file, for example
   `requirements.txt`, and ask:

   ```text
   What are the contents of this file?
   ```

   Copilot will not use the excluded file's contents as context.

5. Contrast that with an allowed file such as the example
   `src/example-module/example_file.py`. Copilot should respond normally there.

> Note: exclusion applies to Copilot context and suggestions. It does not
> encrypt or hide the file on disk or in Git - use `.gitignore` / secret
> management for that. Remove the scratch example `.env` file after the demo.

**Expected result:** excluded files are not used for Copilot context or
suggestions, while allowed files continue to work normally.

---

## Recommended baseline

- keep one repository-wide instruction file for project-level review policy
- add path-based instruction files only where extra rules are needed
- require security, correctness, and negative-test review criteria in the
  instruction set
- exclude secret-bearing or sensitive local files from Copilot context
- treat AI review as an additional reviewer, not the final approval gate

## How to use this

- share this page as a setup and validation guide
- start with one repository-wide instruction file before adding path-based rules
- add path-based instructions only for high-value languages or folders first
- test the behavior with one changed file locally and one pull request remotely

## What to remember

- One instruction set = consistent review in the IDE and on GitHub.com.
- Repository-wide vs path-based instructions give you project-wide standards
  plus targeted rules for specific files.
- The most important security question is often the default behavior on failure.
- Content exclusion keeps secrets/config out of Copilot context and completions.
- AI review is an extra reviewer, not the approver. Human engineering judgment
  is still the release gate.

## Land the line

Responsible AI review works best when review policy is written once, scoped
clearly, and verified in the same places developers already work: the editor
and the pull request.

## Fallback

If PR review is unavailable, create the instruction files, review the sample
diff in VS Code, and verify content exclusion locally. That still proves the
policy and exclusion model.
