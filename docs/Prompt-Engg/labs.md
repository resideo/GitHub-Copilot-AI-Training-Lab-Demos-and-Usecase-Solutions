---
title: Labs
parent: Prompt Engineering
nav_order: 1
permalink: /prompt-engg/labs/
description: "GitHub Copilot Prompt Engineering hands-on labs."
---

<h1 align="center">🎯 GitHub Copilot Prompt Engineering — Hands-on Labs</h1>

<p align="center">
   <img src="https://img.shields.io/badge/C%2B%2B-00599C?style=for-the-badge&logo=cplusplus&logoColor=white" alt="C++" />
   <img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white" alt="Python" />
   <img src="https://img.shields.io/badge/C%23-239120?style=for-the-badge&logo=csharp&logoColor=white" alt="C#" />
   <img src="https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black" alt="JavaScript" />
   <img src="https://img.shields.io/badge/Java-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white" alt="Java" />
</p>

<p align="center">
   <img src="https://img.shields.io/badge/GitHub%20Copilot-000000?style=for-the-badge&logo=githubcopilot&logoColor=white" alt="Prompt Engineering" />
   <img src="https://img.shields.io/badge/Support%20Ticket-Triage%20Toolkit-2088FF?style=for-the-badge" alt="Support Ticket Triage Toolkit" />
</p>

<hr>

This is the ~1-hour guided hands-on exercise for the ~3-hour **GitHub Copilot Prompt
Engineering** training. You practise the **prompt-engineering techniques** from the session —
specificity, prompt-quality escalation, persona/role prompting, context control, few-shot,
chain-of-thought, iterative development, choosing the right model, and custom instructions —
by building one cohesive **Support Ticket Triage Toolkit** in **any one** language you choose —
**C++, Python, C#, JavaScript, or Java**. **GitHub Copilot writes the code from your intent**
while you review, refine, and verify it. Each lab section maps to a technique from the Prompt
Engineering agenda and adds the next piece of the toolkit: **ticket model → model comparison &
token cost → persona review + context → few-shot routing + chain-of-thought priority → iterative
pipeline with tests → custom instructions + a wired-up guided run that prints `PASS` checks.**

{: .note }
> This lab is **prompt-first**: the same toolkit can be built by any model, but *how you phrase
> the prompt* — its specificity, context, examples, and persona — decides how good, cheap, and
> correct the result is. You will feel that difference directly.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## How to use these labs

1. **Pick one language** and do the [Shared setup](#shared-setup-do-once) once.
2. Work through the labs **in order** — each section adds the next module of the same toolkit.
3. For every section, use the stated **Mode** (Ask / Edit / Agent), paste the exact **Prompt to
   give**, and pick the recommended **Model**.
4. **Try it yourself first**, then read what Copilot produced before accepting it.
5. **Run & verify** after each lab — by the end the program prints only `PASS` lines.

Budget: ~5 min setup + ~45 min across the six labs + ~10 min wrap-up. Mark stretch goals as
optional so the whole room stays in sync.

---

## Learning objectives

By the end of this lab you will be able to:

- **Write specific, high-quality prompts** — turn vague asks into precise, reviewable
  instructions, and escalate prompt quality through a repeatable ladder.
- **Choose the right model for the job** — send the *same* prompt to Auto, a fast model, and a
  reasoning model, compare answers, and weigh quality against token cost.
- **Steer Copilot with persona and context** — use role prompting and `#file` / `#selection` /
  `#problems` to get expert, grounded output instead of generic guesses.
- **Apply few-shot and chain-of-thought** — teach behaviour by example and force plan-first
  reasoning for algorithmic decisions.
- **Drive multi-step work and reuse** — build iteratively, generate tests, and encode standards
  as custom instructions so every prompt inherits them.

---

## Shared setup (do once)

You need an editor with Copilot, a GitHub Copilot licence, and **one** language toolchain.

### Editor + Copilot (any one editor)

| Requirement | Check it works |
|---|---|
| **VS Code** (recommended), **Visual Studio 2022** (17.10+), or a **JetBrains IDE** | Editor opens |
| **GitHub Copilot** + **GitHub Copilot Chat** extensions, signed in | Copilot status bar shows **Ready** |
| GitHub account with an active **Copilot licence** | No "no access" warning in the Copilot panel |

Supported IDEs and per-IDE features: the
[Copilot feature matrix](https://docs.github.com/en/copilot/reference/copilot-feature-matrix).

### Pick ONE language toolchain

Install the toolchain for the language you'll use, plus its editor extension. **Any of the five
is fully supported** — pick what you know best.

| Language | Install (on PATH) | Verify | Editor extension |
|---|---|---|---|
| **C++** | C++17 compiler — `g++`, `clang++`, or MSVC `cl` | `g++ --version` | C/C++ (ms-vscode.cpptools) |
| **Python** | **Python 3.10+** | `python --version` | Python (ms-python.python) |
| **C#** | **.NET SDK 8.0+ (LTS)** | `dotnet --version` | C# Dev Kit (ms-dotnettools.csdevkit) |
| **JavaScript** | **Node.js LTS** + npm | `node -v` && `npm -v` | (built-in JS support) |
| **Java** | **JDK 17+** | `java -version` && `javac -version` | Extension Pack for Java (vscjava.vscode-java-pack) |

> Standard library only — no paid services, servers, or network calls are required.

### Create & run a scratch project (your language)

Build the whole toolkit in one file (e.g. `triage.*`) and run it after each lab.

> **Tell Copilot your language.** Every **Prompt to give** below contains a `<YOUR_LANGUAGE>`
> placeholder — replace it with the language you picked above (e.g. `Python`, `Java`, `C#`,
> `C++`, or `JavaScript`) so Copilot generates the function in that language.

**C++**
```bash
# create triage.cpp, then:
g++ -std=c++17 triage.cpp -o triage && ./triage         # bash
g++ -std=c++17 .\triage.cpp -o triage.exe; .\triage.exe # PowerShell
```

**Python**
```bash
python triage.py
```

**C#**
```bash
dotnet new console -o triage
cd triage
# edit Program.cs, then:
dotnet run
```

**JavaScript**
```bash
node triage.js
```

**Java**
```bash
# create Triage.java, then:
javac Triage.java
java Triage
```

### Copilot Chat modes you'll use (cheat sheet)

This lab is Chat-driven and prompt-focused.

| Mode | What it is | When to use it |
|---|---|---|
| **Ask mode** | Explain code / answer questions — **no edits** | Review for bugs or security flaws, compare model answers |
| **Edit mode** | Targeted edits with a reviewable diff | Change a selection: add a function, apply a fix, refactor |
| **Agent mode** | Multi-step, multi-file tasks | Build the pipeline, generate tests, wire the guided run |
| **Model picker** | Choose which model answers | Compare Auto vs a fast vs a reasoning model on one prompt |
| **Context variables** | Point Chat at the right scope | `#<filename>` for a file, `#selection`, `#problems`, `@workspace` |

> Open the Chat view, pick **Ask / Edit / Agent** from the mode dropdown, and use the **model
> picker** to switch models. Add `#<filename>`, `#selection`, `#problems`, or `@workspace` so
> Copilot reuses your existing code and errors instead of guessing.

---

## Lab 1 — Specificity & prompt-quality escalation

**Time:** ~8 min

**# Learning objectives**
- Turn a vague ask into a precise, constrained prompt and see the quality jump.
- Seed the toolkit with a `Ticket` model and a parser generated from intent.

**Mode:** Edit
**Model:** base model (e.g. GPT-5.4)

**Prompt to give (weak → strong):**

#### Weak prompt (first pass):
```text
make a ticket parser in <YOUR_LANGUAGE>
```

#### Replace with the strong prompt:
```text
In <YOUR_LANGUAGE>, create a support-ticket model with fields: id (string), subject (string), body (string),
customer (string), product_area (string), and created_at (ISO-8601 string). Add a function parse_tickets(rawList) that takes an array of raw records, validates that id/subject/body are present and non-empty, trims whitespace, defaults a missing product_area to "unknown", and returns {tickets, errors} where errors lists each rejected record with a reason. Seed 5 sample tickets covering different product areas, including one clearly urgent outage report.
```

**Steps**
1. Create the scratch file for your language (see setup) and confirm the Copilot status bar shows
   **Ready**. Open the **Chat** view and check the mode dropdown shows Ask / Edit / Agent.
2. In Chat → **Edit**, first paste the **weak** prompt and note how much Copilot must guess
   (field names, validation, output shape). Undo it.
3. Paste the **strong** prompt. Review the diff — are the fields, validation, and `{tickets,
   errors}` shape exactly as described? Accept only after it matches the intent.
4. **Escalate quality on one function:** select `parse_tickets`, Chat → **Edit**:
   > `Improve this: add clear guard clauses, keep it a pure function (no I/O), and document the`
   > `validation rules in a short comment block above it.`
5. Print the count of parsed tickets and the count of errors so you can see it run.

**Run & verify** — the program prints 5 parsed tickets and 0 errors for the clean seed data.

**Checkpoints**
- ✅ You saw the weak-vs-strong prompt difference on the same task.
- ✅ The ticket model and `parse_tickets` match the specified fields and `{tickets, errors}` shape.
- ✅ A malformed record (try one with a blank subject) lands in `errors`, not `tickets`.

**Stretch:** ask Copilot (Ask mode) to critique your strong prompt and suggest one way to make it
even more specific.

---

## Lab 2 — Choose the right model: same prompt, different answers & token cost

**Time:** ~10 min · **flagship exercise**

**# Learning objectives**
- Send one identical prompt to **Auto**, a **fast** model, and a **reasoning** model.
- Compare correctness, style, and **token cost** — and see how good context lets a cheaper model win.

**Mode:** Ask (compare) → Edit (apply the winner)
**Model:** compare **Auto** vs a **fast** model (e.g. GPT-4.1) vs a **reasoning** model (e.g. Claude Sonnet 4 / GPT-5 mini)

**Prompt to give (run the *same* one on each model):**
> `In <YOUR_LANGUAGE>, add classify_severity(ticket) that returns one of "critical", "high", "medium", or "low".`
> `Rules: "critical" if the body mentions an outage, data loss, security breach, or "production`
> `down"; "high" if it mentions many/all users or a payment failure; "medium" for a single-user`
> `bug; "low" for questions or cosmetic issues. Return the level plus a short reason string`
> `explaining which rule matched. Keep it deterministic and case-insensitive.`

**Steps**
1. In Chat → **Ask**, select the **model picker** and run the prompt on **Auto** first. Read the
   answer but do not apply it yet.
2. Switch the model picker to a **fast** model and run the *exact same* prompt. Then switch to a
   **reasoning** model and run it once more.
3. Compare the three answers: Which handled the case-insensitive match? Which added the reason
   string cleanly? Which over- or under-engineered it? Note the rough **token cost** each shows.
4. **Make context do the work:** add `#<your file>` to the prompt and re-run on the *fast* model —
   grounded in your existing `Ticket` model it often matches the reasoning model's quality for a
   fraction of the tokens.
5. Pick the best result, apply it via **Edit**, and print the severity + reason for each seeded
   ticket.

**Run & verify** — the urgent outage ticket prints `critical`; a cosmetic/question ticket prints
`low`; each line includes a reason.

**Checkpoints**
- ✅ You ran the **same** prompt across Auto, a fast model, and a reasoning model.
- ✅ You compared answers *and* token cost, and can say which model you'd choose for this task.
- ✅ Adding `#file` context improved the cheaper model's answer.

**Responsible-usage note:** more expensive models are not automatically "more correct." Good
context and a specific prompt often make a fast, cheap model the right choice.

---

## Lab 3 — Persona/role prompting & context control

**Time:** ~9 min

**# Learning objectives**
- Use a **role/persona** prompt to get an expert-level security review.
- Steer Copilot with `#file`, `#selection`, and `#problems` to fix a grounded bug.

**Mode:** Edit (add code) → Ask (persona review) → Edit (remediate)
**Model:** reasoning model (e.g. Claude Sonnet 4 / GPT-5 mini)

**Prompt to give:**
> `In <YOUR_LANGUAGE>, add extract_contact(ticket) that pulls any email address and any order number (format ORD-###)`
> `from the ticket body and returns them as structured fields. Then, acting as a senior security`
> `and privacy engineer, review the whole file: flag any place we might log or expose customer PII`
> `(names, emails) and list each issue with severity before proposing fixes.`

**Steps**
1. In Chat → **Edit**, generate `extract_contact` from the first half of the prompt.
2. **Introduce a bug/leak on purpose:** by hand, add a debug line that prints the customer's full
   email to the console during triage.
3. Switch to Chat → **Ask** with a reasoning model and run the **persona** review (second half of
   the prompt). Add `#<your file>` so it reviews your real code.
4. Select the offending line and use `#selection` (or fix the reported error via `#problems`) to
   scope the remediation, then apply the fix via **Edit** so PII is masked or not logged.
5. Re-run and confirm no raw email is printed.

**Run & verify** — `extract_contact` returns the email + order number; no raw customer email is
logged anywhere in the run.

**Checkpoints**
- ✅ The **persona** prompt produced a security/privacy review, not just generic feedback.
- ✅ You used `#file` / `#selection` / `#problems` to ground the fix in real code.
- ✅ Customer PII is no longer written to the console.

**Responsible-usage note:** this maps to the org rule **"Never log PII"** — keep it in mind for
Lab 6's custom instructions.

---

## Lab 4 — Few-shot examples & chain-of-thought reasoning

**Time:** ~9 min

**# Learning objectives**
- Teach routing behaviour by **example** (few-shot) instead of by long rules.
- Force **plan-first** (chain-of-thought) reasoning for a priority algorithm.

**Mode:** Edit (few-shot) → Ask/Plan (chain-of-thought) → Edit (implement)
**Model:** base model for few-shot; reasoning model for chain-of-thought

**Prompt to give (few-shot routing):**
> `In <YOUR_LANGUAGE>, add route_ticket(ticket) that returns the owning team. Learn from these examples and`
> `generalise — do not hard-code only these:`
> `- body mentions "invoice"/"refund"/"charge" → "Billing"`
> `- body mentions "login"/"password"/"2FA" → "Identity"`
> `- body mentions "API"/"webhook"/"integration" → "Platform"`
> `- body mentions "app crash"/"UI"/"screen" → "Client Apps"`
> `- anything else → "General Support"`
> `Match case-insensitively and return the team plus the keyword that decided it.`

**Prompt to give (chain-of-thought priority):**
> `Before writing code, outline the steps to compute a priority score (1–100) from a ticket's`
> `severity (Lab 2) and how many users it affects, then wait. After I confirm the plan, implement`
> `compute_priority(ticket) in <YOUR_LANGUAGE> following it exactly.`

**Steps**
1. In Chat → **Edit**, paste the **few-shot** prompt and confirm Copilot *generalises* (e.g. a
   ticket mentioning "billing statement" should still route to Billing).
2. Switch to a reasoning model, Chat → **Ask** (or **Plan**), and paste the **chain-of-thought**
   prompt. Read the plan; correct it if the scoring is off *before* any code is written.
3. Reply to confirm the plan, then let Copilot implement `compute_priority` and review the diff.
4. Print the team + priority score for each seeded ticket.

**Run & verify** — the outage ticket routes to the right team and scores highest priority; a
cosmetic question scores lowest.

**Checkpoints**
- ✅ Few-shot routing **generalised** beyond the literal example keywords.
- ✅ Copilot produced a **plan first**, which you reviewed before it wrote `compute_priority`.
- ✅ Priority scores rank the tickets sensibly (critical/outage highest).

---

## Lab 5 — Iterative development: build, test, refactor

**Time:** ~8 min

**# Learning objectives**
- Drive multi-step work with **Agent** mode and strong prompts + context.
- Generate unit tests and refactor safely while behaviour stays identical.

**Mode:** Agent (build + tests) → Edit (refactor)
**Model:** base model (e.g. GPT-4.1)

**Prompt to give:**
> `In <YOUR_LANGUAGE>, add triage(ticket) that reuses the existing functions — parse validation, classify_severity,`
> `route_ticket, compute_priority, and extract_contact — to return one structured triage record:`
> `{id, team, severity, priority, contact, summary}. Do not re-implement those functions; reuse`
> `them. Then generate unit tests covering: a critical outage ticket, a billing question, and a`
> `ticket with a missing product_area. Reference #<your file> for context.`

**Steps**
1. In Chat → **Agent**, paste the prompt so Copilot assembles `triage` from your existing pieces
   **and** generates tests across the file. Add `#<filename>` so it reuses, not duplicates.
2. Review each proposed change — confirm `triage` calls the real functions rather than
   re-implementing severity/routing logic.
3. **Refactor** — Chat → **Edit**, select `triage`:
   > `Extract the summary-building into a small helper and keep behaviour identical.`
   Review the diff and confirm the tests still pass.
4. Re-run the tests.

**Run & verify** — all `triage` tests print `PASS`, including the missing-`product_area` case
(defaults to `unknown`).

**Checkpoints**
- ✅ `triage` **reuses** the earlier functions instead of duplicating logic.
- ✅ Agent generated tests for the outage, billing, and missing-field cases.
- ✅ The **Edit** refactor kept every test green.

---

## Lab 6 — Custom instructions & the guided finish

**Time:** ~7 min

**# Learning objectives**
- Encode team standards as **custom instructions** so every prompt inherits them.
- Use a **meta-prompt** + Agent to assemble and self-verify the complete toolkit.

**Mode:** Edit (with custom instructions) → Agent (guided run)
**Model:** reasoning model (e.g. Claude Sonnet 4 / GPT-5 mini)

**Prompt to give (custom instructions):**
> Create a workspace **custom instructions** file (e.g. `.github/copilot-instructions.md`) stating
> 2–3 standards — for example: **"Never log customer PII (names, emails). Use our internal auth
> library for any credential handling. Prefer pure functions and return new collections."** Then
> ask Copilot to regenerate the triage report formatter and confirm it obeys the instructions.

**Prompt to give (meta-prompt + guided run):**
> `In <YOUR_LANGUAGE>, implement a main routine that: parses the 5 seed tickets, runs triage on each, and prints a`
> `clean triage report (id, team, severity, priority) sorted by priority descending. Add`
> `deterministic assertions that print PASS/FAIL for: the outage ticket is "critical" and highest`
> `priority, a billing ticket routes to "Billing", a malformed record is rejected, and no raw`
> `customer email is printed. End by printing "Triage toolkit complete." only if all checks PASS.`

**Steps**
1. Add the **custom instructions** file, then in Chat → **Edit** regenerate the report formatter.
   Confirm it does **not** log PII and follows your conventions — proof the instructions applied.
2. Chat → **Agent** (reasoning model): paste the **meta-prompt** guided run. Review the plan of
   edits before accepting each change.
3. Ensure the assertions cover all four checks and print an explicit `PASS`/`FAIL` per check.
4. Ask Copilot (Ask mode) to double-check any assertion it thinks is weak, then run the whole
   program.

**Run & verify** — run your file with the command for your language (see Shared setup); every
check prints `PASS` and the final line reads `Triage toolkit complete.`

**Checkpoints**
- ✅ A custom instructions file exists and **visibly changed** Copilot's output (no PII logging).
- ✅ Agent wired parsing → triage → sorted report → assertions together.
- ✅ All four assertions print `PASS` and the completion line appears.

**Stretch:** add a scoped `.github/instructions/*.instructions.md` with an `applyTo` glob so a rule
applies only to your triage file, and confirm Copilot respects the narrower scope.

---

## Run & verify

Run your finished toolkit with the command for your chosen language:

| Language | Command |
|---|---|
| **C++** | `g++ -std=c++17 triage.cpp -o triage && ./triage` (PowerShell: `g++ -std=c++17 .\triage.cpp -o triage.exe; .\triage.exe`) |
| **Python** | `python triage.py` |
| **C#** | `dotnet run` (from the project folder) |
| **JavaScript** | `node triage.js` |
| **Java** | `javac Triage.java && java Triage` |

You are done when every assertion prints `PASS` and the program ends with
`Triage toolkit complete.`

---

## Technique → lab map

| Technique | Where you practised it |
|---|---|
| Specificity | Lab 1 — weak→strong ticket parser prompt |
| Prompt-quality escalation | Lab 1 — quality ladder on `parse_tickets` |
| Choosing the right model | Lab 2 — same prompt across Auto / fast / reasoning |
| Token-cost awareness | Lab 2 — compare cost; context makes a cheap model win |
| Persona / role prompting | Lab 3 — senior security & privacy engineer review |
| Context control (`#file`/`#selection`/`#problems`) | Lab 3 — grounded PII fix |
| Few-shot prompting | Lab 4 — example-driven `route_ticket` |
| Chain-of-thought | Lab 4 — plan-first `compute_priority` |
| Iterative development & test generation | Lab 5 — build `triage`, generate tests, refactor |
| Custom instructions (repo-wide + scoped) | Lab 6 — no-PII / internal-auth standards |
| Meta-prompting & validation loop | Lab 6 — guided run with PASS/FAIL self-checks |

---

## Wrap-up

You took the Support Ticket Triage Toolkit from an empty file to a validated, tested toolkit —
practising **every core prompt-engineering technique**: specificity and quality escalation,
choosing the right model by comparing answers and token cost, persona prompting with grounded
context, few-shot and chain-of-thought, iterative Agent-driven building with tests, and custom
instructions — **in your own language**. These are the exact skills that make Copilot faster,
cheaper, and more correct in the **Advanced** and **Agentic** sessions next.

---

## Responsible usage

- **Review everything** Copilot generates — it can produce subtle bugs, insecure patterns, and
  outdated APIs. You own the code you accept.
- **Never paste secrets, credentials, or real customer PII** into prompts, and never log PII in
  the code you ship.
- **A pricier model is not automatically more correct** — a specific prompt with good context
  often makes a fast, cheap model the right choice.
- **Test generated code**, especially the boundary and security cases, before trusting it.
- **Review before committing.** Copilot is a fast pair programmer, not an authority.
