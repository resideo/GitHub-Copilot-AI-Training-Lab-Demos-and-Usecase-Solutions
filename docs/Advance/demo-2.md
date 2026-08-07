---
title: Demo 2 — PRs with @copilot, Copilot CLI & the Issues API
layout: default
parent: Demos
grand_parent: Advanced
nav_order: 2
permalink: /advance/demos/demo-2.html
description: "Move beyond the IDE: drive a pull request with @copilot mentions, automate terminal work with the Copilot CLI, and assign Copilot to an issue with the REST/GraphQL API."
---

# 🔀 Demo 2 — Pull Requests with `@copilot`, Copilot CLI & the Issues API
{: .no_toc }

**Agenda section:** Copilot Across Surfaces — CLI, GitHub.com, PR Workflow
**Deck "Demo Time" slide:** `d2` — Topic 2: CLI, GitHub.com, Mobile, PR Workflow
**Surfaces:** **GitHub.com** (coding agent) · **Terminal** (Copilot CLI) · **REST/GraphQL API**

> **Advanced focus:** Move beyond the IDE. Show how the **Copilot coding agent**
> works a **pull request** when you mention `@copilot`, how the **GitHub Copilot
> CLI** automates real terminal use cases, and how to **assign Copilot to an
> issue programmatically** with the REST and GraphQL APIs.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## What this demo shows

| Part | Surface | You demonstrate |
|---|---|---|
| **A** | GitHub.com | Assigning an issue to Copilot and driving a PR with `@copilot` mentions |
| **B** | Copilot CLI | Advanced terminal commands for git, diagnostics, and GitHub.com tasks |
| **C** | REST / GraphQL API | Adding Copilot as an **assignee** to an issue to kick off the coding agent |

---

## Setup

| Requirement | Detail |
|---|---|
| **Copilot subscription** | Any paid plan; coding agent enabled for the repo/org |
| **Coding agent** | Enabled for the target repository (org policy + repo access) |
| **Copilot CLI** | Installed and authenticated (see below) |
| **GitHub CLI** | `gh` installed and logged in (`gh auth login`) for the API parts |
| **A demo repo** | A repo you have **write** access to, with a couple of open issues |

### Install & authenticate the Copilot CLI

```powershell
# Windows (PowerShell)
winget install GitHub.Copilot
# or, all platforms (needs Node.js 22+)
npm install -g @github/copilot
```

```bash
# macOS / Linux
brew install --cask copilot-cli
```

Start it and log in:

```bash
copilot          # launches the interactive session
/login           # run inside the session if prompted
```

> **Trusted directory:** The CLI asks you to confirm you trust the folder you
> launched it from. Launch it from your **project** folder, never your home
> directory.

---

## Pre-flight self-check (do this BEFORE running any prompt)

Most prompts below only work once a few things are true for **this** repo
(`InfoMCopilot-KR/Resedio-Advance-demo`). Tick every box first — if one is
unchecked, do the **Fix** before you demo.

| # | Check | How to verify | Fix if missing |
|---|---|---|---|
| 1 | You have **write access** (or a fork) | `gh repo view InfoMCopilot-KR/Resedio-Advance-demo --json viewerPermission` | Fork it: `gh repo fork InfoMCopilot-KR/Resedio-Advance-demo --clone`, then demo against your fork |
| 2 | **Coding agent** is enabled for the repo/org | Open any issue → **Assignees** → is **Copilot** listed? | Enable in **Org → Settings → Copilot → Coding agent** (admin needed) |
| 3 | **`gh` CLI** installed and logged in | `gh auth status` | `gh auth login` |
| 4 | **Copilot CLI** installed and logged in | `copilot --version` | See *Install & authenticate the Copilot CLI* above |
| 5 | A **real open PR** exists (Part A needs one) | `gh pr list` shows at least one PR | Complete a TODO and open a PR — steps below |
| 6 | You replaced **`OWNER/REPO`** + issue/PR numbers | Re-read the prompt before you send it | Use `InfoMCopilot-KR/Resedio-Advance-demo` and real numbers |

### Create the PR that Part A needs (from Demo-1 code)

This repo ships `Demo-1/Custom-instructions/device_registry.py` with **6 TODO
functions** whose self-checks currently **fail**. Implementing them is the
simplest way to produce a real, reviewable PR:

```powershell
# 1. Branch off main
git switch -c demo/device-registry-register

# 2. Implement the TODOs in Demo-1/Custom-instructions/device_registry.py
#    (register_device, get_device, list_devices, update_firmware,
#     remove_device, registry_summary)

# 3. Verify the self-checks pass
python Demo-1/Custom-instructions/device_registry.py
#    -> All device registry self-checks passed.

# 4. Commit and push
git add Demo-1/Custom-instructions/device_registry.py
git commit -m "feat(registry): implement device registry functions"
git push -u origin demo/device-registry-register

# 5. Open the PR that the Part A @copilot prompts run against
gh pr create --fill --base main
```

> **What does NOT exist in this repo:** there is **no** web app, `content.js`,
> `CHANGELOG.md`, `src/app/file.py`, or `http://localhost:3002`. Generic
> tutorial prompts reference those — the Part B prompts below have been
> rewritten to use **real files** in this repo so they run as-is.

---

## Part A — Pull Request work with `@copilot` (coding agent)

**Goal:** Show that Copilot can act as a teammate on a PR — you delegate, review,
and iterate entirely through issue assignment and PR comments.

### Drive the PR by mentioning `@copilot`

On the pull request Copilot opened (or any PR), leave a comment:

```
@copilot the error messages in device_registry.py should name the offending
device id and field. Please update register_device and add a unit test.
```

- An **eyes emoji (👀)** appears on your comment and a *"Copilot started work"*
  event shows in the PR timeline.
- By default Copilot **pushes commits to the same PR branch**. Ask it to open a
  separate PR if you'd rather.
- Copilot only responds to comments from users with **write access**.
- It **remembers context** from earlier sessions on the same PR.

---

## Part B — GitHub Copilot CLI: advanced commands by use case

**Goal:** Show the CLI as an agent in the terminal — not just autocomplete.

### B1 — Useful in-session slash commands

| Command | Use |
|---|---|
| `/login` | Authenticate |
| `/model` | Switch model (or `--model` flag) |
| `/mcp` | List / manage configured MCP servers |
| `/context` | See token-usage breakdown of the context window |
| `/compact` | Manually compress history (auto-compacts near 95%) |
| `/feedback` | Send a bug report or feature request |

### B2 — Controlling tool approvals (the advanced part)

```bash
# Allow everything EXCEPT destructive commands
copilot --allow-all-tools --deny-tool='shell(rm)' --deny-tool='shell(git push)'

# Allow only git and gh, nothing else, without prompts
copilot --allow-tool='shell(git)' --allow-tool='shell(gh)'

# Allow file writes without individual approval
copilot --allow-tool='write'
```

- `--deny-tool` **always wins** over `--allow-*`.
- For `git`/`gh` you can scope to a subcommand: `shell(git push)`.

> **Safety:** `--allow-all-tools` gives Copilot the same access you have. Use it
> only in a sandbox, container, or throwaway VM.

### B3 — Real use cases to demo live

Launch `copilot` from your **project** folder, or run one-shot with
`copilot -p "<prompt>"` plus the relevant `--allow-tool` flag. Replace
`OWNER/REPO` and issue/PR numbers with your demo values. Pick two or three of
these live:

**Local code tasks** (real files in this repo)
```text
Suggest improvements to Demo-1/Custom-instructions/device_registry.py.
Rewrite README.md to be friendlier to newcomers and describe both demos.
Add module-level docstrings to Demo-1/Memory/watchdog_timer.py.
```

**Git operations**
```text
Commit the staged changes with a Conventional Commit message, then show me the diff.
Revert the last commit, leaving the changes unstaged.
Show me the last 5 changes to Demo-1/Memory/watchdog_timer.py: who, when, and a brief summary.
```
Run headless (allow only git):
```bash
copilot -p "Commit the staged changes with a Conventional Commit message" --allow-tool='shell(git)'
```

**Diagnostics & debugging** (real, reproducible in this repo)
```text
Explain why `python Demo-1/Custom-instructions/device_registry.py` fails and propose a fix. Do not change files yet.
Run the self-checks in device_registry.py and summarize which assertions fail and why.
```

**Talking point:** *"Same agent, different surface. In the terminal it can run
git, read logs, and reach GitHub.com — so it fits automation and CI where the
IDE can't go."*

---

## Part C — Assign Copilot to an issue via the API

**Goal:** Automate what Part A did in the UI — so issue triage tools, bots, or
scripts can hand work to the coding agent.

> **Preview:** The issues assignment API is in public preview and subject to
> change. The Copilot bot login is **`copilot-swe-agent`** (REST assignee value
> `copilot-swe-agent[bot]`).

### C1 — Start a task directly (Agent Tasks API)

Skip issues entirely and start a coding-agent task from a prompt.
Docs: <https://docs.github.com/en/rest/agent-tasks/agent-tasks?apiVersion=2026-03-10#start-a-task>

```bash
curl -L \
  -X POST \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer <YOUR-TOKEN>" \
  -H "X-GitHub-Api-Version: 2026-03-10" \
  https://api.github.com/agents/repos/OWNER/REPO/tasks \
  -d '{"prompt":"Fix the login button on the homepage","base_ref":"main"}'
```

### C2 — Add assignees to an issue

Add one or more assignees to an existing issue. To hand the work to the coding
agent, add **`copilot-swe-agent[bot]`** (the Copilot coding agent) as an
assignee — on assignment it starts a session and opens a PR.
Docs: <https://docs.github.com/en/rest/issues/assignees?apiVersion=2026-03-10#add-assignees-to-an-issue>

```bash
curl -L \
  -X POST \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer <YOUR-TOKEN>" \
  -H "X-GitHub-Api-Version: 2026-03-10" \
  https://api.github.com/repos/OWNER/REPO/issues/ISSUE_NUMBER/assignees \
  -d '{"assignees":["copilot-swe-agent[bot]"]}'
```

**Talking point:** *"UI, CLI, and API all start the same coding agent. The API
lets you wire Copilot into your issue triage, chatops, or scheduled automations
— assign work programmatically and review the PR like any other."*

---

## Recap — What attendees should take away

| Capability | How you trigger it | Where |
|---|---|---|
| Delegate an issue to Copilot | Assign **Copilot** in the Assignees menu | GitHub.com |
| Iterate on a PR | Comment **`@copilot ...`** (write access only) | GitHub.com |
| Resolve conflicts | **Fix with Copilot** or `@copilot resolve...` | GitHub.com |
| Agentic terminal work | `copilot` / `copilot -p "..."` with `--allow-tool` | Copilot CLI |
| Scoped automation safety | `--deny-tool='shell(git push)'`, `/sandbox enable` | Copilot CLI |
| Assign Copilot from code | REST assignees / GraphQL `replaceActorsForAssignable` | API |
| Start a task from a prompt | `POST /agents/repos/{owner}/{repo}/tasks` | API |

> **Rule of thumb:** Use the **UI** to delegate ad-hoc, the **CLI** for terminal
> and CI automation, and the **API** to embed Copilot into your own tools. In
> every case a human stays the reviewer before merge.

---

## Responsible usage reminder

- Copilot's PRs are a **first draft** — always review, especially security- and
  workflow-related changes.
- Never grant `--allow-all-tools` outside a sandbox or disposable environment.
- Use **fine-grained**, least-privilege tokens for the API and never commit them.
- The coding agent and issue-assignment APIs are in **public preview** and may
  change.
