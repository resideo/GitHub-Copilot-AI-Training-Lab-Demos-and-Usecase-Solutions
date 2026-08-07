---
title: Demo 2 — Agent HQ and Cloud Agent Surfaces
layout: default
parent: Demos
grand_parent: "Agents & MCP"
nav_order: 2
permalink: /agents-mcp/demos/demo-2.html
description: "Tour Agent HQ, launch the cloud agent from multiple surfaces, and navigate repository session logs."
---

# Demo 2 — Agent HQ: Ways to Use the Cloud Agent & Navigating the Agents Tabs
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

**Course:** GitHub Copilot Agents & MCP
**Deck slides:** _Agent HQ & Agent Sessions_ (Slide 19), _Meet Mission Control_ (Slide 20),
_Set Context Once — Spaces_ (Slide 21), _Agents in the Repository_ (Slide 22),
_Session Logs_ (Slide 30)
**Feature focus:** A **guided tour of Agent HQ** — every surface you can launch the
**Copilot Cloud Agent** from, and how to navigate the **Agents tabs** (Mission Control,
the repository Agents tab, and session logs).
**Language:** **Neutral** — this is a UI/navigation demo, no code is written.

---

## Goal

Show attendees that the Cloud Agent isn't a single button in one place — it's a
capability you can reach from **many surfaces**, all funneling into the **same reviewable
session + PR**. By the end, they should be able to:

- Find **Agent HQ / Mission Control** at `github.com/copilot` and read its layout.
- Launch cloud agent work in **five different ways** (web, issue, PR, IDE, CLI).
- Navigate the **Agents tab inside a repository** and read a **session log**.
- Know where to **monitor** a running session from wherever they work.

```
Agent HQ (github.com/copilot)
   │
   ├─ Central prompt bar ──► launch & steer a session
   ├─ Left nav ───────────► New chat · Agents · Spaces · Spark
   │
   ▼
One session  ──►  one branch  ──►  one pull request  ──►  human review
```

> **The one thing to teach:** _"Many doors, one room."_ However you start the Cloud
> Agent, it runs in an isolated environment, opens a PR, and every launch shows up as a
> trackable **session** you can follow from the web, the CLI, or your IDE.

---

## Setup (1 min)

| Setting | Value |
|---|---|
| **Accounts** | Signed in to `github.com` with a Copilot plan that includes the Cloud Agent |
| **Demo repo** | `https://github.com/ps-copilot-sandbox/copilot-agent-and-mcp` (or your fork) |
| **IDE** | VS Code with the latest GitHub Copilot extension, signed in |
| **CLI (optional)** | `gh` installed and authenticated (`gh auth status`) for the CLI launch |
| **Tabs open** | `github.com/copilot`, the demo repo's **Issues** tab, and VS Code |

> **If the Agents surface is missing:** confirm the org/enterprise has **Copilot coding
> agent** enabled for the user, and that the repo allows it. It's an org policy toggle.

---

## Part A — Meet Agent HQ / Mission Control (3 min)

**Where:** `github.com/copilot`

Mission Control is the **single place to launch, monitor, and steer** agent work while
keeping full oversight. Walk the two regions on screen:

### 1. The central prompt bar

- **Prompt entry point** — type a task here to start and steer work.
- **Scope & mode control** — pick the repo and how the agent should operate.
- **Actions shortcuts row** — quick ways to kick off common work.
- **Recent agent sessions** appear right under the bar — your running/finished tasks.

### 2. The left navigation

| Item | What it's for |
|---|---|
| **New chat** | Home base — quick questions and steering |
| **Agents** | Delegate tasks to the Cloud Agent (this is the launch pad) |
| **Spaces** | A reusable **context layer** (repos, files, notes) — _not_ an agent |
| **Spark** | Full-stack app building |

**Talking point:** _"This is the control room. The prompt bar is where intent goes in;
the left nav is how you add context and delegate. Notice Spaces sits here too — it's not
an agent, it's the context you set once and reuse (Slide 21)."_

---

## Part B — Five ways to launch the Cloud Agent (7 min)

The whole point of this demo: **the same agent, many entry points.** Show at least three
of these live; mention the rest.

### Way 1 — From Mission Control (the web) ⭐ recommended first

1. On `github.com/copilot`, open **Agents** in the left nav.
2. In the task box, describe the work and **pick the repository** from the picker.
3. Check the **model picker** — leave it on **Auto** (recommended): Copilot picks the
   best available model, lowers rate-limit risk, and includes discounted multipliers on
   paid plans.
4. Submit. A **new session** starts; the agent plans, works in an isolated environment,
   and will open a **PR** when done.

**Talking point:** _"You can start several of these — sessions run in parallel, each ends
in its own PR to review and merge."_

### Way 2 — Assign a GitHub Issue to Copilot

1. Go to the repo's **Issues** tab → open (or create) a clearly scoped issue.
2. In the **Assignees** panel, assign **Copilot** just like you'd assign a teammate.
3. Watch for the 👀 reaction — the agent has picked it up and started a session.

**Talking point:** _"Treat the issue like a prompt — good acceptance criteria = good
result. This is the most natural fit for existing team workflows."_

### Way 3 — Iterate from the Pull Request

1. Open the **PR** the agent created.
2. Leave **review comments** (anyone with write access can steer it); batch them with
   **"Start a review."**
3. The agent reads the comments and pushes follow-up commits to the same branch.

**Talking point:** _"The PR is the conversation. You don't restart — you comment, and the
same session iterates."_

### Way 4 — Delegate from VS Code / Copilot Chat

1. In VS Code, open **Copilot Chat**.
2. From the chat, **delegate a task to the Cloud Agent** (hand off long-running work to
   run in the background on GitHub while you keep coding).
3. The session shows up in Mission Control and the repo Agents tab just like the others.

### Way 5 — From the GitHub CLI (terminal-first)

```bash
# start a task
gh agent-task create

# follow a running session live
gh agent-task view --follow
```

**Talking point:** _"Same agent, from your terminal. And from a session in the repo you
can click 'Continue in Copilot CLI' to resume it exactly where it left off."_

> **Also available:** launch/monitor from **Raycast** and **GitHub Mobile** — handy for
> kicking off or checking on work away from your desk.

---

## Part C — Navigate the Agents tab inside a repository (4 min)

**Where:** the demo repo → **Agents** tab (next to Code, Issues, Pull requests)

This is where sessions live **next to your code**. Show the three things that matter
(Slide 22):

1. **Centralized sessions** — every agent session in one list. **Jump to the linked PR**,
   and **archive** finished sessions to stay organized.
2. **Resume in terminal** — **"Continue in Copilot CLI"** copies a command so you can pick
   up a session locally.
3. **Clearer session logs** — open a session and show:
   - **Grouped tool calls** (less noise),
   - **inline previews** and **expandable diffs**,
   - **visible bash commands** for full transparency into what the agent ran.

**Talking point:** _"Everything the agent did is here and auditable — the commands, the
diffs, the reasoning under 'View session'. And remember: it works on its own branch and
**cannot merge its own PR** — a human always approves."_

---

## Part D — Monitor from anywhere (2 min)

Reinforce that a running session is trackable from wherever you already work (Slide 30):

| Surface | How you watch |
|---|---|
| **Agents tab (web)** | Full log, token usage, runtime |
| **GitHub CLI** | `gh agent-task view --follow` |
| **Raycast** | Launch and monitor from the launcher |
| **VS Code / JetBrains** | Track sessions from the IDE extension |

**Talking point:** _"Pick whichever fits your flow — the session is the same object
everywhere."_

---

## Wrap-up (1 min)

- **Agent HQ / Mission Control** (`github.com/copilot`) = launch, monitor, steer.
- **Five doors to the Cloud Agent:** web, issue, PR, IDE, CLI (plus Raycast & mobile).
- **Repository Agents tab** = sessions next to your code, with transparent logs.
- **One outcome every time:** an isolated run → a branch → a **PR you review and merge**.

> **Close with:** _"You don't have to remember one 'right' place to start the agent. Start
> where you already are — an issue, a PR, your IDE, your terminal — and it all lands in the
> same reviewable session."_

---

## Quick reference — where things live

| I want to… | Go here |
|---|---|
| Launch from the web | `github.com/copilot` → **Agents** |
| Assign existing work | Repo → **Issues** → assign **Copilot** |
| Steer the result | The **Pull Request** → review comments |
| Delegate while coding | **VS Code** → Copilot Chat |
| Work terminal-first | `gh agent-task create` / `--follow` |
| See all my sessions | Repo → **Agents** tab (or Mission Control) |
| Reuse context | `github.com/copilot` → **Spaces** |
