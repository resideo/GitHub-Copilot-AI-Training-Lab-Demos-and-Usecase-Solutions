---
title: Labs
parent: Advanced
nav_order: 1
permalink: /advance/labs/
description: "GitHub Copilot Advanced hands-on lab: customize Copilot and build a Smart-Home Device Registry."
---

# GitHub Copilot Advanced — Hands-on Lab: Customize Copilot & Build a Smart-Home Device Registry (1 hour)
{: .no_toc }

This is the ~1-hour guided hands-on exercise for the **GitHub Copilot Advanced** training. It
mirrors the four instructor demos. You will **author every kind of custom instruction**
(`copilot-instructions.md`, a path-scoped `*.instructions.md`, and an `AGENTS.md`), **use those
instructions to complete code**, **teach Copilot with persistent memory**, **drive tasks from the
Copilot CLI** with slash commands, and finally **package a reusable Skill and finish in Agent
mode**. You build one cohesive **Smart-Home Device Registry** in **any one** language you choose —
**C++, Python, C#, JavaScript, or Java**. **GitHub Copilot writes the code from your intent** while
you review, refine, and verify it.

> This lab is about **customizing Copilot**: instead of re-typing the same guidance every prompt,
> you encode it once — in instructions, an `AGENTS.md`, memory, and a skill — so Copilot behaves
> like a specialist on *your* project, then you let those customizations build the registry for you.
---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## How to use these labs

1. **Pick one language** and do the [Shared setup](#shared-setup-do-once) once.
2. Work through the labs **in order** — each one builds on the previous (Lab 1 authors the rules,
   Labs 2–5 use them).
3. For every section, use the stated **Mode** (Ask / Edit / Agent), paste the exact **Prompt to
   give**, and pick the recommended **Model**.
4. **Try it yourself first**, then read what Copilot produced before accepting it.
5. **Run & verify** after each lab — by the end the program prints only `PASS` lines.

Budget: ~5 min setup + ~55 min across the five labs + ~5 min wrap-up. Mark stretch goals as optional
so the whole room stays in sync.

---

## Learning objectives

By the end of this lab you will be able to:

- **Author every instruction type** — a repo-wide `.github/copilot-instructions.md`, a path-scoped
  `*.instructions.md` with `applyTo`, and an agent-agnostic `AGENTS.md` — using the manual, `/`
  command, and gear-icon methods, and explain how **precedence** resolves conflicts.
- **Build with your instructions** — implement the registry's functions and watch Copilot apply
  your rules automatically (type hints/validation, raise-don't-return, naming) without re-stating
  them.
- **Teach Copilot with memory** — store conventions in **user**, **session**, and **repository**
  memory and complete steps that reuse those facts automatically.
- **Drive tasks from the Copilot CLI** — install and authenticate the CLI and use its **slash
  commands** to commit, diagnose, and improve code from the terminal.
- **Package a Skill and finish in Agent mode** — create a `SKILL.md` that auto-invokes on your
  language, then let Agent mode complete and self-verify the whole registry.

---

## Shared setup (do once)

You need an editor with Copilot, a GitHub Copilot licence, and **one** language toolchain.

### Editor + Copilot (any one editor)

| Requirement | Check it works |
|---|---|
| **VS Code** (recommended — Advanced customizations are richest here), signed in | Editor opens |
| **GitHub Copilot** + **GitHub Copilot Chat** extensions | Copilot status bar shows **Ready** |
| GitHub account with an active **Copilot licence** | No "no access" warning in the Copilot panel |

Supported IDEs and per-IDE features: the
[Copilot feature matrix](https://docs.github.com/en/copilot/reference/copilot-feature-matrix).

> **Note:** custom instructions, `AGENTS.md`, skills, and the memory tool are most fully supported
> in **VS Code**. If you use another IDE you can still author the files, but do the gear-icon and
> memory steps in VS Code.

### Pick ONE language toolchain

Install the toolchain for the language you'll use, plus its editor extension. **Any of the five is
fully supported** — pick what you know best.

| Language | Install (on PATH) | Verify | Editor extension |
|---|---|---|---|
| **C++** | C++17 compiler — `g++`, `clang++`, or MSVC `cl` | `g++ --version` | C/C++ (ms-vscode.cpptools) |
| **Python** | **Python 3.10+** | `python --version` | Python (ms-python.python) |
| **C#** | **.NET SDK 8.0+ (LTS)** | `dotnet --version` | C# Dev Kit (ms-dotnettools.csdevkit) |
| **JavaScript** | **Node.js LTS** + npm | `node -v` && `npm -v` | (built-in JS support) |
| **Java** | **JDK 17+** | `java -version` && `javac -version` | Extension Pack for Java (vscjava.vscode-java-pack) |

> Standard library only — no paid services, servers, or network calls are required.

### Create & run a scratch project (your language)

Build the whole registry in one file (e.g. `registry.*`) and run it after each lab.

**C++**
```bash
# create registry.cpp, then:
g++ -std=c++17 registry.cpp -o registry && ./registry         # bash
g++ -std=c++17 .\registry.cpp -o registry.exe; .\registry.exe # PowerShell
```

**Python**
```bash
python registry.py
```

**C#**
```bash
dotnet new console -o registry
cd registry
# edit Program.cs, then:
dotnet run
```

**JavaScript**
```bash
node registry.js
```

**Java**
```bash
# create Registry.java, then:
javac Registry.java
java Registry
```

### Copilot customization surfaces you'll use (cheat sheet)

This lab creates and then *uses* Copilot customizations. Each lives in a specific file/location:

| Surface | File / location | What it does |
|---|---|---|
| **Repo custom instructions** | `.github/copilot-instructions.md` | Repo-wide standards every prompt inherits |
| **Folder/file instructions** | `.github/instructions/<name>.instructions.md` (`applyTo` glob) | Rules that auto-apply to matching files |
| **Agent instructions** | `AGENTS.md` (workspace root) | Agent-agnostic baseline auto-detected by VS Code |
| **Memory** | Copilot **memory** — `/memories/`, `/memories/session/`, `/memories/repo/` | Persistent notes reused across prompts |
| **Skill** | `.github/skills/<name>/SKILL.md` | Reusable domain know-how Copilot applies on demand |

> Open the Chat view, click the **gear / settings icon → Configure Chat → Instructions** to open
> the **Agent Customizations** editor. It lists every active instruction file and its
> **Generate Instructions ▾** dropdown creates new ones. Reload the window if a new customization
> doesn't show up immediately.

---

## Lab 1 — Author all your instruction files

**Time:** ~11 min · **Mirrors Demo 1 (Custom Instructions & AGENTS.md)**

**# Learning objectives**
- Create a repo-wide `copilot-instructions.md`, a path-scoped `*.instructions.md`, and an
  `AGENTS.md` — using all three creation methods.
- See how **precedence** (Personal → Repository → Organization) resolves conflicts.

**Mode:** Ask / Edit + the gear-icon **Agent Customizations** editor
**Model:** base model (e.g. GPT-4.1)

### Step 1 — Repository instructions (three ways to create the same file)

You'll create `.github/copilot-instructions.md`. Try all three methods so you know your options:

1. **Manually:** create the file by hand and type a rule — proof it's just a Markdown file VS Code
   auto-detects.
2. **`/` command (bootstrap):** in the chat input type **`/init`** (it scans the workspace) *or*
   describe the rules directly:
   > `/create-instructions Repository standards for a smart-home device-registry service:`
   > `descriptive names, small single-responsibility functions, a docstring on every public`
   > `function, validate inputs at the boundary, never log customer PII, and always use our`
   > `internal authentication library for credentialed access.`
3. **Gear icon:** Chat → **gear** → **Configure Chat → Instructions** → **Generate Instructions ▾
   → New Instructions (Workspace)**.

Verify detection (Ask mode):
> `What coding standards apply to this workspace, and where are they defined?`

### Step 2 — Path-scoped instructions (`applyTo`)

Create a language-specific file that layers **on top of** the repo rules but only for your files:
> `/create-instructions Language-only conventions for my registry file: use type hints/strong`
> `types, raise a specific typed error (e.g. ValueError) with a clear message on bad input, and`
> `use named constants instead of magic numbers. Set applyTo to match my source file (e.g.`
> `"**/*.py" or "**/Registry.java").`

Open the generated `.github/instructions/<name>.instructions.md` and confirm the YAML header has
`applyTo` and a `description`.

### Step 3 — Author an `AGENTS.md`

In the **Agent Customizations** editor choose **Generate Instructions ▾ → New AGENTS.md**. VS Code
scaffolds `AGENTS.md` at the workspace root. Add a short agent-agnostic baseline (project purpose,
the same core rules). Confirm the enabling setting is on:
```jsonc
"chat.useAgentsMdFile": true
```

**Checkpoints**
- ✅ `.github/copilot-instructions.md`, a path-scoped `*.instructions.md` (with `applyTo`), and
  `AGENTS.md` all exist.
- ✅ Ask mode correctly lists which instruction files apply.

**Stretch:** create the same repo instructions with a *second* method and diff the two outputs.

---

## Lab 2 — Build with your instructions

**Time:** ~11 min · **Mirrors Demo 1 (using instructions)**

**# Learning objectives**
- Use the instruction files from Lab 1 to generate code — and confirm the rules apply *without*
  re-stating them.

**Mode:** Edit → Agent
**Model:** base model for edits; a reasoning model (Claude Sonnet 4.5 / GPT-5 mini) for the finish

### Step 1 — Scaffold the registry with TODOs

> `Create my registry file with a DeviceRegistry that stores devices keyed by id, plus these`
> `functions left as TODO with self-checks in a main block: register_device, get_device,`
> `list_devices, update_firmware, remove_device, and registry_summary. Follow the project`
> `instructions.`

### Step 2 — Implement one function and inspect the layers

> `Implement register_device in my registry file.`

Review the diff — **without you asking**, Copilot should apply:
- **type hints / strong types** and a **docstring** → from your path-scoped rules,
- **input validation + a specific error message** → from your repo rules,
- **raise on a duplicate id instead of returning null** → repo + path rules combined.

### Step 3 — Implement the rest

> `Implement the remaining TODO functions in my registry file so every self-check passes. Follow`
> `the project and language instructions.`

**Run & verify** — run your file (see Shared setup); the implemented self-checks pass.

**Checkpoints**
- ✅ Generated code shows type hints/validation/raise-don't-return **without** you re-typing the
  rules.
- ✅ All six functions are implemented and the self-checks pass.

---

## Lab 3 — Teach Copilot with persistent memory

**Time:** ~11 min · **Mirrors Demo 1 (Copilot Memory)**

**# Learning objectives**
- Store facts in the three memory scopes — **user** (`/memories/`), **session**
  (`/memories/session/`), and **repository** (`/memories/repo/`).
- Complete registry steps that **reuse** those facts automatically, so you stop re-reminding the AI.

**Mode:** Agent
**Model:** base model (e.g. GPT-4.1)

**Prep:** enable the memory tool —
```jsonc
"github.copilot.chat.tools.memory.enabled": true
```

### Step 1 — User memory (personal style that follows you)

> `Create a user memory at /memories/registry-personal-style.md with this content:`
> `- Prefer short comments above complex device logic.`
> `- Keep helper function names action-oriented (register/get/list/update/remove).`

Then complete a step using it:
> `Apply my personal style memory while improving the naming and comments in my registry file.`

### Step 2 — Session memory (temporary focus for this chat only)

> `Create a session memory at /memories/session/demo-focus.md with this content:`
> `This session should prioritize one-shot device checks over recurring monitors.`

Then complete a step using it:
> `Generate one new registry usage example for this session.`

The example should favor a one-shot check — and this preference should **not** persist later.

### Step 3 — Repository memory (a durable project convention)

> `Create a repository memory at /memories/repo/registry-conventions.json using this JSON:`
> ```json
> {
>   "subject": "Smart-home device registry conventions",
>   "fact": "Registry functions must validate inputs at the boundary and raise a typed error (e.g. ValueError) instead of returning null/None, and must use bounded collections rather than unbounded growth.",
>   "citations": ["registry.*"],
>   "reason": "Keeps the registry aligned with the project's instructions (validate, raise-don't-return, bounded state).",
>   "category": "conventions"
> }
> ```

Then complete a step using it:
> `Implement a new helper that lists the active devices.`

It should apply the remembered convention (validate, raise a typed error, bounded state) **without
you re-stating it**.

### Step 4 — Verify persistence in a fresh chat

Start a **new chat** and ask:
> `Write a new registry helper for device lookup.`

Confirm the **repository** convention still applies, while the **session** one-shot hint does **not**
(it was scoped to the previous chat). Optionally verify repo memory on GitHub at
`https://github.com/<owner>/<repo>/settings/copilot/memory`.

**Checkpoints**
- ✅ You created **user**, **session**, and **repository** memory entries.
- ✅ A step completed **using memory** applied the remembered convention without you re-typing it.
- ✅ In a fresh chat, repository memory persisted while the session hint did not.

**Stretch:** change the threshold/convention in repo memory, re-run a helper prompt, and confirm the
output changes accordingly.

---

## Lab 4 — Drive tasks from the Copilot CLI

**Time:** ~11 min · **Mirrors Demo 2 (Copilot CLI)**

**# Learning objectives**
- Install and authenticate the **Copilot CLI**.
- Use **slash commands** and tool-approval flags to complete real tasks from the terminal.

**Mode:** Terminal (Copilot CLI)
**Model:** switch with `/model` inside the session

### Step 1 — Install & authenticate

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
Launch it from your **project** folder (never your home directory) and log in:
```bash
copilot          # interactive session; confirm you trust the folder
/login           # if prompted
```

### Step 2 — Complete a few tasks from the terminal

Pick two or three (interactive, or one-shot with `copilot -p "<prompt>"`):

**Diagnostics (read-only first)**
> `Explain why running my registry file fails and propose a fix. Do not change files yet.`

**Improve code**
> `Suggest improvements to my registry file and apply the safe ones.`

**Git operation** (scope tools to git only):
```bash
copilot -p "Commit the staged changes with a Conventional Commit message" --allow-tool='shell(git)'
```

### Step 3 — Control tool approvals (the advanced part)

```bash
# Allow everything EXCEPT destructive commands
copilot --allow-all-tools --deny-tool='shell(rm)' --deny-tool='shell(git push)'
```
- `--deny-tool` **always wins** over `--allow-*`; you can scope to a subcommand like `shell(git push)`.
- **Safety:** `--allow-all-tools` gives Copilot your access — use it only in a sandbox or throwaway VM.

**Checkpoints**
- ✅ `copilot --version` works and you completed a `/login`.
- ✅ You used at least three slash commands and completed one code task from the terminal.
- ✅ You generated a commit message while allowing **only** `shell(git)`.

**Stretch:** run a task headless with `-p` and pipe its output into another command.

---

## Lab 4 — Package a Skill and finish in Agent mode

**Time:** ~13 min · **Mirrors Demo 3 (Skills + Agent mode)**

### Learning objectives
- Create a `SKILL.md` whose keyword-rich `description` makes it **auto-invoke** on your language.
- Let **Agent mode** apply the skill to complete and self-verify the whole registry.

**Mode:** Edit (author the skill) → Agent (apply it)
**Model:** base model to author; reasoning model for the guided finish

### Step 1 — Author the skill

> `Create a skill at .github/skills/device-monitor/SKILL.md. The name must match the folder. Give`
> `it a keyword-rich description that says to use it whenever working on my language's registry/`
> `device-monitor code (name the language and file extension). In the body, document our`
> `conventions: use the logging framework not print, strong types/type hints, named constants for`
> `thresholds, validate inputs, and raise typed errors that name the offending device id.`

Reload the window / start a new chat so the skill is discovered.

### Step 2 — Add a monitor function that triggers the skill

In **Agent mode**, add a monitor to the registry **without mentioning the skill**:
> `Add a check_devices function that flags any device whose battery is below a LOW_BATTERY`
> `threshold, returning a list of {device_id, reason, severity}. Refactor to production quality.`

Prove it fired:
> `Which skills did you use for that, and why?`

The agent should cite `device-monitor`, triggered by your language/file keywords, and the code
should follow the skill's conventions (logging, named threshold constant, typed errors, validation).

### Step 3 — Invoke it manually as a slash command

> `/device-monitor review my registry file and list any remaining convention gaps`

### Step 4 — Finish everything in Agent mode with self-checks

> `Complete any remaining functions and wire a main routine that registers sample devices, prints`
> `the registry summary, and runs check_devices. Add deterministic assertions that print PASS/FAIL`
> `for: a duplicate registration raises, a missing device raises, and a low-battery device is`
> `flagged. End by printing "Registry complete." only if all checks PASS. Follow the project and`
> `language instructions, the remembered conventions, and the device-monitor skill.`

**Run & verify** — run your file; every check prints `PASS` and the final line reads
`Registry complete.`

**Checkpoints**
- ✅ `.github/skills/device-monitor/SKILL.md` exists and the name matches the folder.
- ✅ The skill **auto-invoked** (the agent cited it) and the code follows its conventions.
- ✅ All assertions print `PASS` and the completion line appears.

**Stretch — Bring Your Own Model (Demo 4):** open the model picker → **Manage Language Models**,
add your own model with a key (or a local **Ollama** model), select it, and re-run the Step 4
prompt with it. Hover a response to confirm which model answered.

---

## Run & verify

Run your finished registry with the command for your chosen language:

| Language | Command |
|---|---|
| **C++** | `g++ -std=c++17 registry.cpp -o registry && ./registry` (PowerShell: `g++ -std=c++17 .\registry.cpp -o registry.exe; .\registry.exe`) |
| **Python** | `python registry.py` |
| **C#** | `dotnet run` (from the project folder) |
| **JavaScript** | `node registry.js` |
| **Java** | `javac Registry.java && java Registry` |

You are done when every assertion prints `PASS` and the program ends with `Registry complete.`

---

## Feature → lab map

| Advanced feature | Where you built & used it |
|---|---|
| Repo custom instructions | Lab 1 — `.github/copilot-instructions.md` |
| Folder/file instructions | Lab 1 — path-scoped `*.instructions.md` (`applyTo`) |
| Agent instructions | Lab 1 — `AGENTS.md` at the workspace root |
| Instruction precedence | Lab 1 — personal rule overrides repo/org |
| Using instructions to build | Lab 2 — registry functions inherit the rules |
| User / session / repo memory | Lab 3 — remembered facts reused across chats |
| Copilot CLI + slash commands | Lab 4 — terminal tasks with scoped tool approvals |
| Skills | Lab 5 — `device-monitor/SKILL.md` auto-invokes |
| Multi-step assembly (Agent) | Lab 5 — guided run with PASS/FAIL self-checks |
| Bring Your Own Model | Lab 5 stretch — add a model in VS Code |

---

## Wrap-up

You took a Smart-Home Device Registry from an empty file to a validated program — while
**customizing Copilot itself**: a repo-wide `copilot-instructions.md`, a path-scoped
`*.instructions.md`, an `AGENTS.md`, persistent **memory** (user / session / repo), terminal
automation with the **Copilot CLI**, and a reusable **skill** — **in your own language**. These
customizations turn Copilot from a generic assistant into a specialist that already knows your
standards, thresholds, and conventions on every future prompt.

---

## Responsible usage

- **Review everything** Copilot generates — it can produce subtle bugs, insecure patterns, and
  outdated APIs. You own the code you accept.
- **Never paste secrets, credentials, or real customer PII** into prompts, and never log PII in the
  code you ship — encode that rule in your instructions.
- **Customizations amplify** — a wrong instruction, memory, or skill misleads *every* future
  prompt, so review them as carefully as code.
- **Scope CLI tools** with `--allow-tool` / `--deny-tool`; never use `--allow-all-tools` outside a
  sandbox or disposable environment.
- **Review before committing.** Copilot is a fast pair programmer, not an authority.
