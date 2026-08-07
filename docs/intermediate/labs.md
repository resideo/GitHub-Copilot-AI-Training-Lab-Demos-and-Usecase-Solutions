---
title: Labs
parent: Intermediate
nav_order: 1
permalink: /intermediate/labs/
description: "GitHub Copilot Intermediate hands-on labs."
---

<h1 align="center">🎯 GitHub Copilot Intermediate — Hands-on Labs</h1>

<p align="center">
   <img src="https://img.shields.io/badge/C%2B%2B-00599C?style=for-the-badge&logo=cplusplus&logoColor=white" alt="C++" />
   <img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white" alt="Python" />
   <img src="https://img.shields.io/badge/C%23-239120?style=for-the-badge&logo=csharp&logoColor=white" alt="C#" />
   <img src="https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black" alt="JavaScript" />
   <img src="https://img.shields.io/badge/Java-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white" alt="Java" />
</p>

<p align="center">
   <img src="https://img.shields.io/badge/GitHub%20Copilot-000000?style=for-the-badge&logo=githubcopilot&logoColor=white" alt="Intermediate" />
   <img src="https://img.shields.io/badge/Warehouse-Fulfillment%20Labs-2088FF?style=for-the-badge" alt="Warehouse Fulfillment Labs" />
</p>

<hr>

Build one project end-to-end — a **Warehouse Fulfillment Service** — in **any one** of five
languages: **C++, Python, C#, JavaScript, or Java**. Every task is described by *intent* — you tell
GitHub Copilot what you want and it generates the code in the language you picked. There are **no
per-language starter files**: Copilot writes the code, you review, run, and verify it.

This lab is the Intermediate-level continuation of the [Fundamental lab](./Fundamental.md). Where
Fundamental focused on *invoking* Copilot, this lab focuses on **workflow**: brainstorm with **Ask**,
outline with **Plan**, and build with **Agent**.

{: .note }
> New here? Do setup first, then progress through Labs 1–6 in order. You will configure customization
> before Lab 1, build the core in Labs 1–3, then drive extension and review in Labs 4–6.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

**You set up customization first, then build in two parts:**

- **Set up first (before Lab 1).** Add a `.github/` folder containing **`copilot-instructions.md`**, a
   **prompt file** (`/add-fulfillment-rule`), and a **custom agent** (`fulfillment-engineer`) so your
   standards and invariants apply from the very first prompt.
- **Part 1 (Labs 1–3) — design & build the core.** Brainstorm with **Ask**, plan with **Plan**, and
   build with **Agent** (driven by `/detailed`), steering the work with plain prompts.
- **Part 2 (Labs 4–6) — drive with your customization.** Actively **select the custom agent** and
   **run the prompt file** to extend, test, and review — shorter prompts, consistent results.

> Use this guide when a room has mixed language preferences. Each participant picks their own
> language, follows the exact same steps, and ends with a working, verified service.

> Ready-made customization files are provided in [`Intermediate/.github/`](./Intermediate/) — you copy
> them into your project in the setup step below, **before** Lab 1.

---

## What you'll build

A **Warehouse Fulfillment Service** — the core logic that turns a customer order into a shipment:

```
Receive stock  →  Create order  →  Allocate / Pick  →  Pack  →  Ship
                                         │
                                         └─ Backorder when stock is insufficient
```

Domain in plain words:

- **Inventory** — SKUs with on-hand quantity per warehouse location.
- **Order** — a customer request for one or more SKUs and quantities.
- **Allocation** — reserve stock for an order; if stock is short, create a **backorder**.
- **Pick / Pack / Ship** — move an allocated order through fulfillment states.
- **Rules** — never over-allocate, never ship an unpaid/unallocated order, keep an audit trail.

Everything runs **locally** — no servers, paid APIs, or network calls required.

---

## How to use this lab

1. **Pick one language** and do the [Shared setup](#shared-setup-do-once) once.
2. Work through the labs **in order** — each builds on the previous one's output.
3. For every step, the guide gives you the **exact prompt to type**, the **Copilot mode**, and the
   **recommended model**.
4. **Try it yourself first**, then read what Copilot produced before accepting it.
5. **Run & verify** at the end of each lab — the program must print all `PASS` lines.

Budget: ~5 min setup + ~20–30 min per lab. You set up customization first; Labs 1–3 design & build
the core; Labs 4–5 drive features and tests with the custom agent & prompt file; Lab 6 reviews the
result with Copilot Code Review.

---

## Shared setup (do once)

You need an editor with Copilot, a GitHub Copilot licence, and **one** language toolchain.

### Editor + Copilot (any one editor)

| Requirement | Check it works |
|---|---|
| **VS Code** (recommended), **Visual Studio 2022**, or a **JetBrains IDE** | Editor opens |
| **GitHub Copilot** + **GitHub Copilot Chat** extensions, signed in | Copilot status bar shows **Ready** |
| GitHub account with an active **Copilot licence** | No "no access" warning in the Copilot panel |

Supported IDEs and per-IDE features: the
[Copilot feature matrix](https://docs.github.com/en/copilot/reference/copilot-feature-matrix).

### Pick ONE language toolchain

| Language | Install (on PATH) | Verify | Editor extension |
|---|---|---|---|
| **C++** | C++17 compiler — `g++`, `clang++`, or MSVC `cl` | `g++ --version` | C/C++ (ms-vscode.cpptools) |
| **Python** | **Python 3.10+** | `python --version` | Python (ms-python.python) |
| **C#** | **.NET SDK 8.0+ (LTS)** | `dotnet --version` | C# Dev Kit (ms-dotnettools.csdevkit) |
| **JavaScript** | **Node.js LTS** + npm | `node -v` && `npm -v` | (built-in JS support) |
| **Java** | **JDK 17+** | `java -version` && `javac -version` | Extension Pack for Java (vscjava.vscode-java-pack) |

> No paid services, servers, or network calls are required — everything runs locally.

### Create a project folder

Create an empty folder, e.g. `warehouse-fulfillment/`, and open it in your editor. Let Copilot
create the files it needs (Agent mode does this for you in Lab 3). To run the service, use the
command for your language:

| Language | Run |
|---|---|
| **C++** | `g++ -std=c++17 main.cpp -o app && ./app` (bash) · `g++ -std=c++17 .\main.cpp -o app.exe; .\app.exe` (PowerShell) |
| **Python** | `python main.py` |
| **C#** | `dotnet new console -o app` → edit `Program.cs` → `dotnet run` |
| **JavaScript** | `node main.js` |
| **Java** | `javac Main.java && java Main` |

### Add your customization (do this first)

**Before Lab 1**, add the customization files so your standards apply from the very first prompt.

1. Create a `.github/` folder at the root level of your project.
2. Inside `.github/`, create `copilot-instructions.md`, `prompts/add-fulfillment-rule.prompt.md`, and
   `agents/fulfillment-engineer.agent.md`.

**`.github/copilot-instructions.md`**

```markdown
# Copilot Instructions — Warehouse Fulfillment Service

These repository-wide instructions apply to **every** Copilot request in this project
(Ask, Agent, prompt files, and custom agents). Keep them concise and language-agnostic —
the service may be written in C++, Python, C#, JavaScript, or Java.

## Domain rules (invariants — never violate)

- **Never over-allocate stock.** Allocated quantity for a SKU can never exceed on-hand quantity.
- **Never ship an order that is not fully allocated.** Shipping requires state `allocated` → `picked` → `packed` → `shipped`.
- **Insufficient stock creates a backorder** for the shortfall — never a partial silent success.
- **Cancelling an order releases its reserved stock** back to inventory.
- **Keep an audit trail.** Every state transition records: timestamp, order id, from-state, to-state.

## Coding standards

- Prefer **pure functions** for allocation and backorder logic (no hidden global state).
- Model order status as a **strict state machine** and reject invalid transitions.
- Every new business rule ships with **unit tests** using the language's idiomatic framework.
- Validate inputs at boundaries (unknown SKU, zero/negative quantity, empty order).
- Keep functions small and named by intent (`allocateOrder`, `createBackorder`, `cancelOrder`).

## Security & privacy

- **Never log customer PII** (names, addresses, contact details) — log order ids only.
- Never hard-code secrets or credentials.
- Treat all Copilot output as a **draft**: review, test, then accept.

## Verification

- Every runnable demo prints `PASS` / `FAIL` checks.
- A change is "done" only when the demo and unit tests both pass.
```

**`.github/prompts/add-fulfillment-rule.prompt.md`**

```markdown
---
mode: agent
model: Claude Sonnet 4.5
description: Add a new business rule to the Warehouse Fulfillment Service — implement it, enforce it as an invariant, add unit tests, and update the fulfillment summary.
---

# Add a Warehouse Fulfillment Rule

You are extending the **Warehouse Fulfillment Service**. Follow the repository
`copilot-instructions.md` (invariants, coding standards, never log PII).

The new business rule to add is:

> ${input:rule:Describe the new business rule (e.g. "express orders are allocated before standard orders")}

Do all of the following, in order, and stop for review after presenting the plan:

1. **Plan** — briefly list the files/functions you will change before editing.
2. **Implement** the rule in the service using the existing domain model.
3. **Enforce it as an invariant** — reject any operation that would violate it.
4. **Add unit tests** for the rule using the idiomatic test framework for this project's language,
   including at least one boundary case.
5. **Update the fulfillment summary/report** if the rule affects reported totals.
6. **Run** the demo and tests, and show that all `PASS` checks and tests pass.

Keep the change small and reviewable. Do not weaken any existing invariant.
```

**`.github/agents/fulfillment-engineer.agent.md`**

```markdown
---
description: Warehouse fulfillment engineer — builds and extends the Warehouse Fulfillment Service while strictly honoring domain invariants and always adding tests.
model: Claude Sonnet 4.5
tools: ['codebase', 'editFiles', 'search', 'runCommands', 'runTests']
---

# Fulfillment Engineer

You are a senior engineer specializing in the **Warehouse Fulfillment Service** in this repository.
You work in any of the project's languages (C++, Python, C#, JavaScript, Java) and always match the
existing language and style.

## Operating rules

- **Always follow** `.github/copilot-instructions.md` — the domain invariants are non-negotiable.
- **Refuse to weaken an invariant.** If a request would over-allocate stock, ship an unallocated
  order, or drop the audit trail, explain why and propose a safe alternative instead.
- **Always add unit tests** for any new or changed behavior — never ask whether tests are wanted.
- **Explain trade-offs before large changes**, then proceed once the approach is clear.
- **Never log customer PII.** Reference orders by id only.

## Workflow for every task

1. State a short plan (files/functions to touch).
2. Make the smallest change that satisfies the request.
3. Add or update unit tests and the fulfillment summary as needed.
4. Run the demo and tests; report `PASS`/`FAIL` results.
5. Summarize what changed and any invariant you protected.
```

This adds three assets (the prompt file and custom agent are pinned to **Claude Sonnet 4.5**):

| File | Invoked by | What it enforces |
|---|---|---|
| `.github/copilot-instructions.md` | Automatic | Domain invariants, coding standards, never log PII |
| `.github/prompts/add-fulfillment-rule.prompt.md` | `/add-fulfillment-rule` | A rule + invariant + tests + summary |
| `.github/agents/fulfillment-engineer.agent.md` | Pick **fulfillment-engineer** in the mode dropdown | A role that always adds tests & guards invariants |

Reload the Copilot Chat view (or reopen the folder) so it picks up the new files. Verify: typing `/`
in Chat shows `add-fulfillment-rule`, and the mode dropdown lists **fulfillment-engineer**.

> **When to use which:** custom instructions = standards for *everything*; prompt file = a *repeatable
> task* run on demand; custom agent = a *role* for a class of tasks. You'll create one more prompt
> file — `/detailed` — as the output of Lab 2, and actively use these starting in Lab 4.

### Copilot modes you'll use (cheat sheet)

| Mode | What it's for | How to invoke |
|---|---|---|
| **Ask** | Explain, brainstorm, answer questions — **never edits files** | Chat view → **Ask** |
| **Plan** | Produce a step-by-step plan before any code changes | Chat view → **Plan** (or Copilot CLI `/plan`) |
| **Agent** | Multi-file, multi-step tasks — creates and runs code | Chat view → **Agent** |
| **Custom agent** | An Agent with a saved role & rules (used in the 2nd half) | Mode dropdown → **fulfillment-engineer** |
| **Prompt file** | A saved, repeatable task you run by name | Type `/add-fulfillment-rule` in Chat |

> **Choosing a mode:** Ask = "explain / brainstorm", Plan = "outline before building",
> Agent = "do the whole multi-step task". Use the smallest mode that fits. This lab does **not**
> use Edit mode — all code changes go through **Agent** (or the custom agent) so every change is a
> reviewable, multi-step action.

---

# Part 1 — Design & build the core

*Labs 1–3 design and build the core service with Ask, Plan, and Agent (driven by `/detailed`). Your
customization from the setup step already applies — you just don't invoke the custom agent yet.*

## Lab 1 — Brainstorm the domain with **Ask mode**

**Time:** ~20 min · **Mode:** Ask · **Model:** reasoning model (e.g. Claude Sonnet 5 / GPT-5.4)

**# Learning objectives**
- Use **Ask mode** to explore requirements, entities, states, and edge cases *before* writing code.
- Turn a vague idea into a concrete, agreed domain model.

Ask mode **never edits files** — it's your whiteboard. Iterate here until the design is clear.

**Prompt to give (brainstorm the model):**
```
I'm building a Warehouse Fulfillment Service. In plain language, list the core entities
> (inventory, order, allocation, shipment), their key fields, and the lifecycle states an order
> moves through from creation to shipment. Keep it language-agnostic.
```

**Follow-up prompts (iterate):**
```
What are the important edge cases and failure modes? Consider insufficient stock, partial
allocation, backorders, cancellations, and double-shipping.
```
```
Propose the business rules that must never be violated (invariants) for this service.
```
```
Suggest 6–8 verification checks I can run to prove the service works, each as a one-line PASS/FAIL assertion.
```

1. Open Chat → **Ask** and run the brainstorm prompt. Read the entity list critically.
2. Run the follow-ups. Capture the **invariants** and the **verification checks** — you'll reuse
   them in Plan and Tests.
3. Ask mode makes **no file changes**; this is intentional. You're producing shared understanding.

**Checkpoints**
- ✅ You have a written list of entities, fields, and order lifecycle states.
- ✅ You captured at least 4 edge cases and the invariants that guard them.
- ✅ You have 6–8 candidate verification checks to drive the build.

**Stretch:** ask Copilot to compare storing inventory as a flat list vs. per-location map, and when
each is better.

---

## Lab 2 — Get an implementation plan with **Plan mode**

**Time:** ~20 min · **Mode:** Plan · **Model:** reasoning model (e.g. Claude Sonnet 5 / GPT-5.4)

**# Learning objectives**
- Turn the Lab 1 design into an ordered, reviewable **implementation plan** before coding.
- Learn to review, correct, and constrain a plan so the build stays on track.

**Plan mode** outlines the steps and files it *intends* to create/modify — but does not write the
final code yet. This is where you catch scope and sequencing problems cheaply.

**Prompt to give:**
```
Based on this domain (inventory, order, allocation, backorder, pick/pack/ship), produce a
step-by-step implementation plan for a Warehouse Fulfillment Service in <YOUR LANGUAGE>.
Include the modules/functions to create, the order to build them, the invariants to enforce, and where verification checks will run. Do not write the full code yet — just the plan.
```

**Refine the plan:**
```
Reorder the plan so I always have a runnable program after each step, and call out where a
backorder is created when stock is insufficient.
```

1. Open Chat → **Plan** and run the prompt with your language substituted.
2. **Review the plan like a PR.** Is anything missing (cancellation? audit trail?)? Ask Copilot to
   adjust until each step leaves you with a runnable program.
3. Save the final plan as a **prompt file** (e.g. `.github/prompts/detailed.prompt.md`) so you can rerun
   it anytime by typing **`/detailed`** — Lab 3 executes it.

**Checkpoints**
- ✅ You have an ordered plan naming the modules/functions to build.
- ✅ Each step leaves a runnable program (incremental, not big-bang).
- ✅ The plan explicitly handles the insufficient-stock → backorder path.

---

## Lab 3 — Build the service with **Agent mode** (driven by `/detailed`)

**Time:** ~30 min · **Mode:** Ask · Agent · **Model:** GPT-5.4

**# Learning objectives**
- Reuse the plan you saved as the **`/detailed` prompt file** to drive the build — no retyping.
- Run the **same prompt file in both Ask and Agent mode**, and feel the difference.
- Compare running the **whole plan** vs. **one step at a time** for control.

Because you saved your Lab 2 plan as a prompt file, you can rerun it anytime by typing **`/detailed`**.
A prompt file works in **any** chat mode — the mode decides what happens to your files.

### Step 1 — Dry-run the plan in **Ask mode**

Switch Chat to **Ask** and run the prompt file:

```
/detailed
```

Ask mode **explains and validates** the plan without touching files — a safe dry run. Read it back,
confirm the steps and invariants look right, and refine `/detailed` if anything is missing.

### Step 2 — Execute the plan in **Agent mode**

Switch Chat to **Agent** (**GPT-5.4**) and run the **same** prompt file:

```
/detailed
```

Agent mode **executes** the plan: it creates files, writes code, and can run it. Review every
proposed change before accepting.

### Prefer more control? Run one step at a time

Instead of the whole `/detailed` at once, pass a single step to the prompt file (or type it directly in
**Agent** mode) — run these one at a time, reviewing each result:

```
/detailed just the inventory module: add stock for a SKU, query on-hand quantity, and prevent negative stock.
```
```
Add an order model and a function to create an order with one or more SKU/quantity line items.
```
```
Add allocation: reserve stock for an order. If stock is insufficient, create a backorder for the shortfall.
```
```
Add pick, pack, and ship state transitions. Reject shipping an order that isn't fully allocated.
```
```
Add a runnable demo in main that exercises a normal order and a backorder, printing PASS/FAIL checks.
```
```
Run the application and show me the output.
```

1. **Ask first, then Agent** — dry-run `/detailed` in Ask, then execute it in Agent.
2. After each Agent step, **read the diff** before accepting. Reject anything that over-reaches.
3. **Run & verify** — the demo prints `PASS` for both the normal order and the backorder path.

**Whole plan vs. one step at a time:** running the entire `/detailed` is fast but harder to review;
passing one step keeps each change small, reviewable, and aligned to your plan. Prefer step-by-step
for anything you'll maintain.

**Checkpoints**
- ✅ You ran `/detailed` in **Ask** mode (dry run, no file changes).
- ✅ You ran `/detailed` in **Agent** mode to build the service.
- ✅ Insufficient stock produces a **backorder** (not an over-allocation).
- ✅ Shipping is rejected unless the order is fully allocated.
- ✅ The demo runs and prints only `PASS`.

---

# Part 2 — Drive the rest **with your customization**

*Labs 4–6 actively use the `/add-fulfillment-rule` **prompt file** and the `fulfillment-engineer`
**custom agent** — on **Claude Sonnet 5** — to extend, test, and review the service. You set these
up before Lab 1, so they're ready to go.*

## Lab 4 — Build the second half **using customization**

**Time:** ~30 min · **Mode:** Custom agent · Prompt file · **Model:** Claude Sonnet 5

**# Learning objectives**
- Build the remaining features by **selecting the custom agent** and **running the prompt file** —
  not by writing long prompts.
- Feel how custom instructions make standards, invariants, and tests automatic.

In Lab 3 you built the core by hand. Now build the rest with customization doing the work. Notice
you no longer restate the rules — `copilot-instructions.md` already carries them.

### 5a — Extend with the custom agent

Select **fulfillment-engineer** in the chat mode dropdown, keep **Claude Sonnet 5**, then run
these one at a time (review each result):

```
Add the customer name and order priority (standard/express) to each order.
```
```
Make order status a strict state machine: created → allocated → picked → packed → shipped.
```
```
Add a cancel-order operation that releases any reserved stock back to inventory.
```
```
Add a fulfillment summary: orders shipped, units shipped, and open backorders.
```

The agent adds tests and guards invariants **without being asked** — that behavior comes from its
`.agent.md` role plus the repo instructions.

### 5b — Add a rule with the prompt file

Run the prompt file by name and give it a rule as input:

> ```
> /add-fulfillment-rule express orders are allocated before standard orders
> ```

It plans, implements the rule, enforces the invariant, adds unit tests, and updates the summary —
the whole repeatable workflow in one command.

1. Use the **custom agent** for 5a and the **prompt file** for 5b.
2. Review every change; **run & verify** after each — the demo still prints only `PASS`.
3. Compare with Lab 3: the prompts are shorter, yet standards and tests stay consistent.

**Checkpoints**
- ✅ Second-half features were built via the **custom agent** and **prompt file** (no hand-written standards).
- ✅ Cancelling an order returns reserved stock; invalid transitions are rejected.
- ✅ Copilot added tests automatically because of the customization.

---

## Lab 5 — Round out **unit tests**

**Time:** ~25 min · **Mode:** Custom agent · Ask · **Model:** Claude Sonnet 4.5

**# Learning objectives**
- Generate meaningful **unit tests** with Copilot using your language's idiomatic test framework.
- Cover the invariants and edge cases from Lab 1 — especially the backorder and cancellation paths.

Use your language's standard test tooling (Copilot picks the idiomatic one):

| Language | Typical test framework |
|---|---|
| **C++** | GoogleTest, or simple `assert`-based checks |
| **Python** | `pytest` or `unittest` |
| **C#** | xUnit or MSTest (`dotnet test`) |
| **JavaScript** | Jest or Node's built-in `node:test` |
| **Java** | JUnit 5 |

**Prompt to give (fulfillment-engineer agent):**
```
Add unit tests for the Warehouse Fulfillment Service using the idiomatic test framework for
<YOUR LANGUAGE>. Cover: adding stock, creating an order, full allocation, partial allocation
that creates a backorder, rejecting over-allocation, rejecting shipping an unallocated order,
and cancelling an order to release stock.
```

**Deepen coverage:**
```
Add boundary tests: allocating exactly the on-hand quantity, ordering zero units, and a SKU
that doesn't exist. List the cases before writing them.
```
```
Review the tests for gaps against the invariants we defined. What's missing?
```

1. Run the Agent prompt, review the generated tests, and run them with your framework's command.
2. Expect at least one failure to reveal a real gap — **fix it with the fulfillment-engineer agent**, then re-run.
3. Use **Ask** to review the test suite for missing invariants; add the gaps.

**Checkpoints**
- ✅ Tests exist for allocation, backorder, over-allocation rejection, and cancellation.
- ✅ Boundary cases (exact stock, zero units, unknown SKU) are covered.
- ✅ All tests pass after your fixes.

---

## Lab 6 — Review your changes with **Copilot Code Review**

**Time:** ~20 min · **Mode:** Copilot Code Review · **Model:** reasoning model (e.g. Claude Sonnet 4.5)

**# Learning objectives**
- Run **Copilot Code Review** on your changes before committing, straight from the Source Control view.
- Review a **single file or selection** in the editor, then triage and apply the suggested fixes.

Now that the service and tests are built, review everything as a reviewer would — without leaving
your editor. Copilot Code Review flags bugs, missing edge cases, and style issues as inline comments
you can accept, dismiss, or discuss.

### 6a — Review changes in Source Control

Open the **Source Control** view (the branch icon in the Activity Bar). With your Warehouse changes
listed, click the **Copilot Code Review** action (the review icon at the top of the **Changes**
header) and pick the scope:

| Option | What it reviews |
|---|---|
| **Copilot: Review Unstaged Changes** | Only the changes you have **not** staged yet |
| **Copilot: Review Uncommitted Changes** | **All** staged + unstaged changes (your whole in-progress diff) |

1. Choose **Review Uncommitted Changes** to review the full diff from this lab.
2. Wait for Copilot to post **inline review comments** on the changed lines.
3. For each comment, read it and choose **Apply** (accept the suggested fix), **Dismiss**, or refine
   it in Chat. Re-run your demo and tests after applying any fix.

### 6b — Review a single file or selection

For a focused review of one file:

1. Open a file (e.g. the allocation module). To review just part of it, **select the code** first
   (in Windows, `Ctrl+A` selects the whole file).
2. **Right-click** the selection/editor and choose **Copilot → Review and Comment**.
3. Copilot adds inline comments for that file/selection only. Triage each one as in 6a.

> **Tip:** use 6b (file/selection) for a deep review of risky logic like allocation and backorders,
> and 6a (Source Control) for a broad pass over the whole change set before you commit.

**Ask Copilot to summarize the review:**
> ```
> Summarize the review comments by severity and list which invariants (never over-allocate, never
> ship an unallocated order, always keep an audit trail) each one protects.
> ```

1. Run both review scopes at least once.
2. Apply the fixes that are correct; **re-run the demo and unit tests** — everything still prints `PASS`.
3. Note one issue Copilot caught in review that the tests did **not**.

**Checkpoints**
- ✅ You ran **Review Uncommitted Changes** from the Source Control view.
- ✅ You ran **Review and Comment** on a file/selection via right-click.
- ✅ You applied at least one suggested fix and tests still pass.
- ✅ Copilot Code Review is a **draft reviewer** — you accepted only the fixes you verified.

---

## Wrap-up

You **set up customization first** — `copilot-instructions.md`, a **prompt file**
(`/add-fulfillment-rule`), and a **custom agent** (`fulfillment-engineer`) — then built a
**Warehouse Fulfillment Service** from scratch: the **core** with **Ask**, **Plan**, and **Agent**
(driven by `/detailed`), and the **rest** by actively driving the custom agent and prompt file on
**Claude Sonnet 4.5** — finally reviewing the whole change set with **Copilot Code Review** (Source
Control scopes + right-click *Review and Comment*) — all in **your own language**. Because the
standards were in place from the first prompt, the later labs needed shorter prompts yet produced
consistent results — the payoff of customization across a 200-developer org.

**Responsible-usage reminders**
- **Review everything** Copilot generates — you own the code you accept.
- **Never paste secrets, credentials, or customer PII** into prompts, and never log PII.
- **Test generated code** and check edge cases (especially backorders and cancellations) before
  trusting it.
- **Review before committing.** Copilot is a fast pair programmer, not an authority.

These habits carry directly into the Advanced and Agents & MCP sessions.
