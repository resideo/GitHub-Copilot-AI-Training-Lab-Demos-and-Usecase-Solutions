---
title: Labs
parent: "Agents & MCP"
nav_order: 1
permalink: /agents-mcp/labs/
description: "Hands-on lab for custom sub-agents, GitHub MCP, and the Copilot cloud agent."
---

# GitHub Copilot Advanced — Hands-on Lab: Custom Agents, GitHub MCP & the Copilot Cloud Agent (1 hour)
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

This is the guided hands-on exercise for the **GitHub Copilot Agents & MCP** (Advanced) training.
You build a complete **Expenses Tracker** with a pipeline of **custom sub-agents** that hand off to
each other, push it to a **remote GitHub repository**, then use the **GitHub MCP server** to open a
well-scoped **issue** and **assign it to the Copilot coding (cloud) agent** so Copilot finishes the
next feature for you — on GitHub, in the background — and opens a pull request you review and merge.

You may build in **any one** language — **Java, Python, JavaScript, C#, or C++**. GitHub Copilot
writes the code from your intent; you review, refine, and verify.

```
Lab 1  Build with custom sub-agents (Plan → Design → Develop → Testing → review)
Lab 2  Push to GitHub → create a perfect issue with the GitHub MCP server
Lab 3  Assign the issue to the Copilot cloud agent → review & merge its PR
```

> This lab is **agent- and platform-driven**: custom agents (`*.agent.md`), the GitHub MCP server,
> and the Copilot coding agent on github.com. Editor Chat basics (Ask · Edit · Agent), prompt
> engineering, and secure coding are covered in the Fundamentals and Intermediate labs.

---

## How to use these labs

1. **Pick one language** and do the [Shared setup](#shared-setup-do-once) once.
2. Work through the labs **in order** — each builds on the previous one (build → publish → delegate).
3. For every section use the stated **Mode/Surface**, paste the exact **Prompt** or **issue body**,
   and pick the recommended **Model**.
4. **Try it yourself first**, then review what Copilot produced before accepting it.
5. **Verify** after each lab using the **Checkpoints**.

Budget: ~5 min setup + ~45 min across the three labs + ~10 min wrap-up.

---

## Learning objectives

By the end of this lab you will be able to:

- **Create and use custom agents** (`*.agent.md`) with front-matter and a `handoffs` block.
- **Chain agents into a pipeline** and watch the automatic **handoff** transition between them.
- **Configure and use the GitHub MCP server** from your editor to act on GitHub (create issues).
- **Write a delegation-ready issue** — scoped, testable, unambiguous — that an autonomous agent can
  complete without you.
- **Delegate work to the Copilot coding (cloud) agent** and review/merge the pull request it opens.

---

## Shared setup (do once)

You need an editor with Copilot, a **GitHub Copilot licence that includes the coding agent**, one
language toolchain, and a **GitHub account you can push to**.

### Editor + Copilot

| Requirement | Check it works |
|---|---|
| **VS Code** (latest) with **GitHub Copilot** + **GitHub Copilot Chat**, signed in | Copilot status bar shows **Ready** |
| **Git** installed and configured (`user.name`, `user.email`) | `git --version` |
| **GitHub CLI** *(optional, handy for auth)* | `gh --version` |
| A GitHub account with **Copilot** enabled, incl. the **coding agent** | You can pick **Copilot** as an assignee on an issue (Lab 3) |

> The **Copilot coding agent** must be enabled for your account/org. If you cannot assign an issue
> to **Copilot** in Lab 3, ask your admin to enable it, or use the fallback in that lab.

### Pick ONE language toolchain

| Language | Install (on PATH) | Verify |
|---|---|---|
| **Java** | JDK 17+ | `java -version` |
| **Python** | Python 3.10+ | `python --version` |
| **JavaScript** | Node.js LTS + npm | `node -v` && `npm -v` |
| **C#** | .NET SDK 8.0+ (LTS) | `dotnet --version` |
| **C++** | C++17 compiler (`g++` / `clang++` / MSVC) | `g++ --version` |

> Standard library only — no paid services, servers, or databases required.

### The custom agents you'll use

This lab ships five custom agents in `.github/agents/`. Copy them into your own project so your
editor discovers them:

| File | Agent | Job | Output |
|---|---|---|---|
| `Plan.agent.md` | **1.Plan Agent** | Scope the app | `/docs/PLAN.md` |
| `Design.agent.md` | **2.Design Agent** | Interface + skeleton | `/docs/DESIGN.md` + stub files |
| `Develop.agent.md` | **3.Develop Agent** | Implement logic, run app | `/docs/ImplementationNotes.md` |
| `Testing.agent.md` | **4.Testing Agent** | Write & run tests | `/docs/TestReport.md` |
| `review.agent.md` | **5.review Agent** | Audit & sign-off | `/docs/ReviewReport.md` |

> Custom agents live in **`.github/agents/*.agent.md`** and are picked up from the open workspace.
> Each file's YAML front-matter + Markdown body **is** the agent's system prompt; the `handoffs`
> block names the next agent (`send: true` auto-advances).

---

## Lab 1 — Build the Expenses Tracker with custom sub-agents & handoff

**Time:** ~20 min · **Surface:** Copilot Chat (Agent mode) · **Model:** reasoning model for
Plan/Design/review (e.g. Claude Sonnet 4.5 / GPT-5), base or reasoning for Develop/Testing.

**# Learning objectives**
- Create a custom agent from scratch, then run a five-agent pipeline that builds a full app.
- Observe the **handoff** transition carrying context from one agent to the next through `/docs`.

### 1a. Create a fresh project and add the agents

1. Create an empty folder and open it in VS Code, then initialise git:
   ```bash
   git init
   ```
2. Create `.github/agents/` and copy the five agent files from
   [Demo 1]({{ '/agents-mcp/demos/demo-1.html' | relative_url }})
   (`Plan.agent.md`, `Design.agent.md`, `Develop.agent.md`, `Testing.agent.md`, `review.agent.md`)
   into it.
3. Reload the window if needed, open **Chat**, and confirm **1.Plan … 5.review** appear in the
   **Agent picker**.

### 1b. (Show the skill) Create one custom agent yourself

1. Command Palette (`Ctrl+Shift+P`) → **Chat: New Custom Agent…** → location **`.github/agents`** →
   name it `Scratch`.
2. Paste this minimal template, then **save** and confirm it shows in the Agent picker:
   ```markdown
   ---
   name: Scratch Agent
   description: "Use when: demonstrating how a custom agent is defined."
   model: Claude Sonnet 4.5 (copilot)
   handoffs:
     - label: "➡ Hand to Plan"
       agent: Plan
       prompt: "Start the Expenses Tracker plan."
       send: true
   ---

   # Scratch Agent
   You are a throwaway agent used to show that a custom agent is just a Markdown file.
   ```
3. You've now **created** a custom agent by hand — delete `Scratch.agent.md` afterwards to keep the
   pipeline clean.

### 1c. Run the pipeline (Plan → Design → Develop → Testing → review)

Select **1.Plan Agent** and give it the idea (pick any language):

> `Build me an Expenses Tracker. Target language: Python.`
> `(Java / JavaScript / C# / C++ also fine — say the word and it adapts.)`

Then follow each handoff. The MVP Copilot builds should support: **add / list / delete expense,
filter by category & date, total and per-category summary, and persistence to a local file**, plus
a small CLI and unit tests.

**Steps**
1. **1.Plan** → review `/docs/PLAN.md` (target stack, features, data model, backlog, acceptance
   criteria). This is the human gate — edit a bullet if needed.
2. Accept the handoff to **2.Design** → it creates language-specific skeleton files (declarations +
   `TODO` stubs) and `/docs/DESIGN.md`.
3. Handoff to **3.Develop** → it implements every stub, adds persistence + CLI, and **runs the app
   once**.
4. Handoff to **4.Testing** → it writes and **runs** unit tests (pytest / JUnit / node:test / xUnit /
   GoogleTest) and writes `/docs/TestReport.md`.
5. Handoff to **5.review** → it re-runs tests, audits correctness/style/security, and writes
   `/docs/ReviewReport.md` with a verdict.

**Run & verify** — the app runs and a basic **add → list → summary** flow works; the test suite
passes; `/docs/ReviewReport.md` reads **Approved**.

**Checkpoints**
- ✅ You created a custom agent from scratch (1b) and saw it in the Agent picker.
- ✅ The five agents ran in order and each **handoff** carried context via `/docs`.
- ✅ The Expenses Tracker runs, tests pass, and review is **Approved**.

**Stretch:** re-run the whole pipeline in a **different language** — only Design/Develop/Testing
change; Plan and review stay the same.

---

## Lab 2 — Publish to GitHub and open a perfect issue with the GitHub MCP server

**Time:** ~20 min · **Surface:** terminal + Copilot Chat (Agent mode) with the **GitHub MCP server**
· **Model:** reasoning model (e.g. Claude Sonnet 4.5 / GPT-5).

**# Learning objectives**
- Push your Expenses Tracker to a new remote repository.
- Configure the **GitHub MCP server** in VS Code and use it from Chat to **create a GitHub issue**.
- Write a **delegation-ready** issue that the Copilot cloud agent can complete in Lab 3.

### 2a. Push the tracker to a new remote repo

1. Add a `.gitignore` (ask Copilot: *"Generate a .gitignore for a `<language>` CLI project"*),
   then commit:
   ```bash
   git add .
   git commit -m "Expenses Tracker MVP built with custom agents"
   ```
2. Create the remote and push. With the GitHub CLI:
   ```bash
   gh repo create expenses-tracker --private --source . --remote origin --push
   ```
   *(No CLI? Create an empty repo on github.com, then `git remote add origin <url>` and
   `git push -u origin main`.)*
3. Confirm the code is on GitHub and note your **`owner/repo`** (e.g. `your-name/expenses-tracker`).

### 2b. Add the GitHub MCP server to VS Code

You'll use the **remote GitHub MCP server** (no Docker needed).

1. Create **`.vscode/mcp.json`** in your project:
   ```jsonc
   {
     "servers": {
       "github": {
         "type": "http",
         "url": "https://api.githubcopilot.com/mcp/"
       }
     }
   }
   ```
2. Open the file — VS Code shows a **Start** action above the server entry. Click **Start** and
   complete the **GitHub OAuth** sign-in when prompted.
3. In Chat (**Agent** mode), open the **Tools** picker and confirm the **GitHub** MCP tools are
   listed (e.g. *create issue*, *get issue*, *list issues*, *assign Copilot to issue*).

> **Alternative (local, PAT):** run the containerised server
> `ghcr.io/github/github-mcp-server` with a fine-grained **Personal Access Token** (scopes: *repo*,
> *issues: read/write*). Use this only if your org blocks the remote server. Never paste the token
> into Chat — put it in the MCP server config's env, and never commit it.

### 2c. Create the issue from Chat (via MCP)

In Chat → **Agent**, with the GitHub tools available, paste this — replace `OWNER/REPO`:

> `Using the GitHub MCP server, create an issue in OWNER/REPO. Use the exact title and body below`
> `verbatim. Add labels: enhancement, good-first-issue, copilot. Do not change scope.`
>
> ```
> Title: Add monthly budgets with over-budget warnings and CSV export
>
> ## Summary
> Extend the existing Expenses Tracker with (1) per-category **monthly budgets**, (2) an
> **over-budget warning** in the summary, and (3) a **CSV export** of expenses. Keep the current
> language, structure, and standard-library-only approach. Do not add third-party dependencies or a
> database.
>
> ## Context
> - The app is a small CLI Expenses Tracker with add/list/delete/filter/total/summary and local
>   file persistence. See `/docs/PLAN.md` and `/docs/DESIGN.md` for the data model and interfaces.
> - An `Expense` has: id, amount, category, date, note.
>
> ## Scope of work
> 1. **Budgets:** allow setting a monthly budget per category (e.g. `set-budget <category> <amount>`),
>    persisted alongside expenses. A category with no budget is treated as "no limit".
> 2. **Over-budget warning:** in the per-category summary, for the current month mark any category
>    whose spend exceeds its budget with a clear `OVER BUDGET (spent X of Y)` note.
> 3. **CSV export:** add an `export <path>` command that writes all expenses to a CSV with a header
>    row `id,amount,category,date,note`, correctly escaping commas/quotes.
>
> ## Acceptance criteria
> - [ ] Setting a budget persists and survives a restart (save→load round-trip).
> - [ ] The summary flags a category as OVER BUDGET only when current-month spend > its budget.
> - [ ] Categories without a budget never show a warning.
> - [ ] `export <path>` produces a valid CSV that re-imports to the same rows.
> - [ ] Invalid input is rejected (negative/zero budget, missing category, unwritable path) with a
>       clear message and no crash.
> - [ ] New unit tests cover budgets, the over-budget boundary (spend == budget is NOT over), and
>       CSV round-trip. All existing and new tests pass.
>
> ## Out of scope
> - No web/GUI, no cloud sync, no third-party libraries, no schema/DB server.
>
> ## Definition of done
> - All acceptance criteria met, tests green, and a short note added to `/docs/ImplementationNotes.md`
>   describing the new commands.
> ```

**Steps**
1. Run the prompt; Copilot calls the MCP **create issue** tool. Approve the tool call.
2. Open the issue on github.com and confirm the **title, body, and labels** match exactly.
3. Sanity-check the issue against the "perfect issue" rubric below — fix any gap now, before Lab 3.

**A delegation-ready issue is:**
- **Self-contained** — states context, files, and the data model so the agent needs nothing else.
- **Scoped** — an explicit *Scope of work* and *Out of scope*.
- **Testable** — checkbox **acceptance criteria** with boundary cases (e.g. spend == budget).
- **Unambiguous** — one clear outcome, no open questions.
- **Constrained** — "standard library only, no new deps" keeps the agent on rails.

**Run & verify** — the issue exists in your repo with the exact body and the `enhancement`,
`good-first-issue`, `copilot` labels.

**Checkpoints**
- ✅ The tracker is pushed to a remote repo you own.
- ✅ The GitHub MCP server is connected and its tools are usable from Chat.
- ✅ A single, well-scoped issue was created **via MCP** with clear acceptance criteria.

---

## Lab 3 — Delegate the issue to the Copilot cloud agent and review its PR

**Time:** ~15 min · **Surface:** github.com (Issues, **Agents** page / Mission Control) — optionally
started from Chat via MCP · **Model:** the coding agent picks its own; you choose at handoff where
offered.

**# Learning objectives**
- Hand the issue to the **Copilot coding (cloud) agent** so it implements the feature autonomously.
- Monitor the session and **review & merge** the pull request it opens.

### 3a. Assign the issue to Copilot

**Option A — on github.com (recommended):**
1. Open the issue from Lab 2.
2. In the **Assignees** panel, assign **Copilot**.
3. Copilot reacts with 👀 and starts a background session; a **draft pull request** appears shortly.

**Option B — from Chat via MCP:**
> `Using the GitHub MCP server, assign issue #<number> in OWNER/REPO to the Copilot coding agent so`
> `it implements the requested feature.`
Approve the tool call.

### 3b. Watch the agent work

1. Go to the **Agents** page (github.com → your avatar/agents, or **github.com/copilot/agents** —
   *Mission Control*). You'll see the running session for your repo.
2. Open the **draft PR** the agent created and watch it push commits as it works through the
   acceptance criteria. The agent reads your repo (and any `.github/copilot-instructions.md`/
   `AGENTS.md`), edits code, adds tests, and runs them in its own environment.

### 3c. Review, iterate, merge

1. When the agent requests review, open the PR **Files changed** tab and review the diff like any
   teammate's PR.
2. If something is off, **comment on the PR** (e.g. *"spend == budget must NOT be flagged as over
   budget — fix the boundary and add a test"*). The agent responds to review comments and pushes a
   fix.
3. Pull the branch locally and run the app + tests to confirm the acceptance criteria:
   ```bash
   git fetch origin
   git switch <copilot-branch-name>
   ```
   Run with your language's command (see setup) and run the tests.
4. When green and satisfied, **approve and merge** the PR. The issue closes automatically.

**Run & verify** — the merged code adds budgets, the OVER BUDGET warning, and CSV export; the
boundary case (spend == budget is **not** over) is covered by a passing test; all tests pass.

**Checkpoints**
- ✅ The issue was assigned to the **Copilot coding agent** (UI or MCP) and a session started.
- ✅ You monitored the session in the **Agents / Mission Control** view and opened its draft PR.
- ✅ You reviewed (and if needed, commented to iterate on) the PR, then **merged** it.

**Fallback (coding agent not enabled):** open the issue in the editor's **Agent** mode instead
(`#<issue-url>` or paste the body), let local Agent mode implement it, then commit and open the PR
yourself with `gh pr create`. The reviewing/merging steps are the same.

**Responsible-usage note:** the cloud agent runs autonomously — **you still own the review**. Read
the diff, run the tests, and never merge unreviewed changes. Keep secrets out of issues, prompts,
and MCP configs.

---

## Run & verify (end to end)

You are done when:

1. **Lab 1** — the Expenses Tracker runs locally, tests pass, `/docs/ReviewReport.md` = **Approved**.
2. **Lab 2** — the repo is on GitHub with one well-scoped issue created **via the GitHub MCP server**.
3. **Lab 3** — the **Copilot cloud agent** implemented the feature, you reviewed its PR, and it's
   **merged** with budgets + over-budget warning + CSV export all working and tested.

---

## Wrap-up

You built a complete app with a pipeline of **custom sub-agents** and their **handoffs**, published
it, drove **GitHub from your editor with the MCP server** to open a delegation-ready **issue**, and
handed that issue to the **Copilot coding agent** to finish the next feature on its own — then
reviewed and merged its PR. That's the full advanced loop: **author agents → orchestrate handoffs →
integrate tools via MCP → delegate to autonomous agents → review the results.**

---

## Responsible usage

- **Review everything** — local agents and the cloud agent can produce subtle bugs and insecure
  patterns. You own the code you merge.
- **Never paste secrets, tokens, or real PII** into prompts, issues, or MCP configs. Keep PATs in
  server config env, never in the repo.
- **Scope tightly.** A narrow, testable issue is what makes autonomous delegation safe and reliable.
- **Test before merging** — run the app and the tests on the agent's branch, especially the boundary
  and security cases.
