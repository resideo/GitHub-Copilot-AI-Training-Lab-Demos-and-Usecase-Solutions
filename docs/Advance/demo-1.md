---
title: Demo 1 — Custom Instructions, AGENTS.md & Memory
layout: default
parent: Demos
grand_parent: Advanced
nav_order: 1
permalink: /advance/demos/demo-1.html
description: "Author and generate every layer of custom instructions (copilot-instructions.md, AGENTS.md, path-based, org, settings), resolve precedence, then teach Copilot with user/session/repo memory."
---

# 🐍 Demo 1 — Custom Instructions, AGENTS.md & Memory
{: .no_toc }

**Agenda section:** Custom Instructions and Reusable Prompt Files
**Deck "Demo Time" slide:** `d1` — Topic 1: Custom Instructions and Prompt Files
**Language:** **Python**

> **Advanced focus:** Attendees already know how to *use* an existing
> `copilot-instructions.md`. This session shows how to **create and generate**
> instruction files from scratch — with `/init`, `/create-instructions`, and the
> **Agent Customizations** editor — how to add an **`AGENTS.md`** file, and how
> **instruction priority** resolves conflicts across all the layers. Part B then
> shows how **Copilot Memory** teaches conventions that persist across chats.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# Part A — Custom Instructions & AGENTS.md

Show how the layers of custom instructions — **repository** (`copilot-instructions.md`
or `AGENTS.md`), **path-based**, **organization**, and **personal** — stack
together, how VS Code applies them automatically to every chat request, and how
**precedence** decides the winner when they conflict.

## Files in this demo

| File | Location | Scope | Purpose |
|---|---|---|---|
| `device_registry.py` | this folder | — | Python service with **6 TODO functions** |
| `copilot-instructions.md` | `.github/` | **Repository** | Project-wide standards (all files) |
| `AGENTS.md` | workspace root | **Repository** | Agent-agnostic baseline auto-detected by VS Code |
| `python.instructions.md` | `.github/instructions/` | **Path-based** | Python-only rules (`applyTo: **/*.py`) |
| `ORG_INSTRUCTIONS_REFERENCE.md` | this folder | **Organization** | Text to paste into GitHub org settings |
| `commit-message-instructions.md` | `.github/` | **Settings** | Commit message rules (referenced by setting) |
| `pr-description-instructions.md` | `.github/` | **Settings** | PR description structure (referenced by setting) |
| `code-review-instructions.md` | `.github/` | **Settings** | Code review focus (referenced by setting) |
| `settings.json` | `.vscode/` | **Settings** | Wires the three files to Copilot settings |

> **Tip for delivery:** To demo *creating* files live, temporarily rename or
> move `.github/copilot-instructions.md` and `AGENTS.md` aside, generate them
> on stage, then compare against the versions shipped in this folder.

### Starter code & instruction files
{: .no_toc }

Copy each block into the path shown in the table above — no clone required.

#### `device_registry.py`

```python
"""Smart-home device registry.

A small in-memory service that tracks connected devices (thermostats,
sensors, cameras) for a home. Used in the Custom Instructions demo to show
how repository, path-based, and organization instructions shape the code
Copilot generates.

Run: python device_registry.py
"""

# In-memory store: device_id -> device dict
_devices = {}


def register_device(device_id, name, kind, firmware):
    # TODO: add a new device to the registry.
    # Should reject duplicate device_id and validate inputs.
    pass


def get_device(device_id):
    # TODO: return the device for device_id, or None if not found.
    pass


def list_devices(kind=None):
    # TODO: return all devices, optionally filtered by kind
    # (e.g. "thermostat", "sensor", "camera").
    pass


def update_firmware(device_id, firmware):
    # TODO: update the firmware version for an existing device.
    # Should fail clearly if the device does not exist.
    pass


def remove_device(device_id):
    # TODO: remove a device from the registry.
    pass


def registry_summary():
    # TODO: return a summary: total device count and a count per kind.
    pass


if __name__ == "__main__":
    # Simple self-checks — these should pass once the TODOs are implemented.
    register_device("t-001", "Living Room", "thermostat", "1.0.0")
    register_device("s-101", "Front Door", "sensor", "2.3.1")
    register_device("c-900", "Garage", "camera", "4.1.0")

    assert get_device("t-001")["name"] == "Living Room"
    assert get_device("missing") is None
    assert len(list_devices()) == 3
    assert len(list_devices(kind="thermostat")) == 1

    update_firmware("t-001", "1.0.1")
    assert get_device("t-001")["firmware"] == "1.0.1"

    remove_device("c-900")
    assert get_device("c-900") is None

    summary = registry_summary()
    assert summary["total"] == 2

    print("All device registry self-checks passed.")
    print(summary)
```

#### `.github/copilot-instructions.md` (Repository)

```markdown
# Copilot Custom Instructions — Advanced Demo (Repository-wide)

These are **repository instructions**. VS Code auto-detects
`.github/copilot-instructions.md` at the workspace root and applies it to
**every** chat request in this workspace.

## Project

- This is a smart-home **device registry** service.
- Keep the code simple, readable, and dependency-free (standard library only).

## Coding Style (all languages)

- Use clear, descriptive names — no single-letter variables except loop counters.
- Prefer small functions with a single responsibility.
- Every public function must have a docstring/comment describing what it does.
- Validate inputs at the boundary; fail early with a clear message.

## Error Handling

- Never return `None` to signal an error when a value is expected — raise instead.
- Error messages must be specific (say *which* device id, *which* field).

## Security

- Never log PII (device owner names, addresses, emails).
- Always use our internal authentication library for any auth operations.
```

#### `AGENTS.md` (Repository)

```markdown
# AGENTS.md — Agent-Agnostic Repository Instructions

VS Code auto-detects this `AGENTS.md` at the workspace root and applies it to
**every** chat request. It is part of the **repository** instruction layer —
the same tier as `.github/copilot-instructions.md` — so any AI coding agent
that reads `AGENTS.md` gets the same baseline.

> **When to use `AGENTS.md`:**
> - You work with multiple AI coding agents and want one shared instruction file.
> - You want subfolder-level instructions for specific parts of a monorepo
>   (enable `chat.useNestedAgentsMdFiles` and place an `AGENTS.md` per folder).

## Project

- This is a smart-home **device registry** service.
- Keep the code simple, readable, and dependency-free (standard library only).

## Working agreements (all agents)

- Explain the *why* in comments only when a rule is non-obvious.
- Validate inputs at the boundary and fail early with a specific message.
- Never log PII (device owner names, addresses, emails).
- Always use our internal authentication library for any auth operations.

## Precedence

- Priority order on conflict: **Personal → Repository (this file /
  `copilot-instructions.md`) → Organization**.
- All applicable instructions are sent to the model; priority only decides the
  winner when two rules contradict each other.
```

#### `.github/instructions/python.instructions.md` (Path-based)

```markdown
---
description: Python-specific conventions for this project. Applied to all .py files.
applyTo: "**/*.py"
---

# Python Conventions (Path-based Instructions)

These are **path-based instructions**. They apply **only** to files matching
the `applyTo` glob above (`**/*.py`). They layer on top of the repository
instructions in `.github/copilot-instructions.md`.

## Language & Style

- Target **Python 3.11+**.
- Use **snake_case** for functions and variables, **PascalCase** for classes.
- Add **type hints** to every function signature (params and return type).
- Use f-strings for string formatting — never `%` or `.format()`.

## Data & Errors

- Raise `ValueError` for invalid input and `KeyError`-style lookups should
  raise `KeyError` with the offending id in the message.
- Collection functions must return an empty `list`/`dict` (never `None`).
- Use `Optional[...]` in type hints for values that may be absent.

## Documentation

- Every function gets a docstring in **triple double-quotes** with a one-line
  summary, then `Args:` and `Returns:` / `Raises:` sections.

## Testing

- When asked for tests, use **pytest** with `test_<function>_<condition>` names.
```

#### `.github/commit-message-instructions.md` (Settings)

```markdown
# Commit Message Instructions (settings-based)

Referenced from `.vscode/settings.json` via
`github.copilot.chat.commitMessageGeneration.instructions`.

- Use **Conventional Commits**: `type(scope): summary` (e.g. `feat(registry): add firmware update`).
- Keep the summary line under **72 characters**, imperative mood ("add", not "added").
- Add a blank line, then a short body explaining **why** the change was made.
- Reference the device/module affected when relevant.
- Never include secrets, tokens, or PII in the message.
```

#### `.github/pr-description-instructions.md` (Settings)

```markdown
# Pull Request Description Instructions (settings-based)

Referenced from `.vscode/settings.json` via
`github.copilot.chat.pullRequestDescriptionGeneration.instructions`.

Structure every PR description with these sections:

- **## Summary** — what changed, in 1–2 sentences.
- **## Why** — the motivation or issue being addressed.
- **## Changes** — a bullet list of the key changes.
- **## Testing** — how it was verified (commands run, self-checks passing).
- **## Risk** — anything reviewers should watch for.

Keep it concise. Never expose PII or credentials in the description.
```

#### `.github/code-review-instructions.md` (Settings)

```markdown
# Code Review Instructions (settings-based)

Referenced from `.vscode/settings.json` via
`github.copilot.chat.reviewSelection.instructions`.

When reviewing selected code, focus on:

- **Correctness** — logic errors, off-by-one, wrong return types.
- **Input validation** — are boundaries checked? Are errors raised, not swallowed?
- **Security** — no logged PII, no hard-coded secrets, safe input handling.
- **Style** — type hints present, docstrings on public functions, f-strings used.
- **Tests** — is the change covered? Suggest missing pytest cases.

Be specific and actionable. Point to the exact line and suggest a concrete fix.
```

#### `.vscode/settings.json` (Settings wiring)

```jsonc
{
  // Enable discovery of organization-level custom instructions
  "github.copilot.chat.organizationInstructions.enabled": true,

  // ---- Settings-based custom instructions ----
  // Each entry is an object with either a "text" (inline) or "file" (path) property.

  // Code review (Copilot "Review selection")
  "github.copilot.chat.reviewSelection.instructions": [
    { "file": ".github/code-review-instructions.md" },
    { "text": "Flag any function missing a docstring or type hints." }
  ],

  // Commit message generation (Source Control ✨ button)
  "github.copilot.chat.commitMessageGeneration.instructions": [
    { "file": ".github/commit-message-instructions.md" }
  ],

  // Pull request description generation
  "github.copilot.chat.pullRequestDescriptionGeneration.instructions": [
    { "file": ".github/pr-description-instructions.md" }
  ]
}
```

#### `ORG_INSTRUCTIONS_REFERENCE.md` (Organization — paste into GitHub org settings)

````markdown
# Organization Instructions — Reference Content

> **Important:** Organization instructions are **not** a file committed to the
> repo. They are configured once in **GitHub.com → your organization →
> Settings → Copilot → Custom instructions**, and then apply to every
> repository your account can access.
>
> This file only holds the **text you would paste** into that setting, so the
> demo can show all three layers side by side.

## To enable org instructions in VS Code

Set this in VS Code settings so org-level instructions are discovered:

```jsonc
// settings.json
"github.copilot.chat.organizationInstructions.enabled": true
```

Org instructions then appear in the **Chat → Instructions** menu alongside
your personal and workspace instructions.

---

## Example organization-wide instruction text

- All code must include a license header comment referencing the company.
- Public APIs must be documented and versioned.
- Follow the company secure-coding checklist; never hard-code secrets.
- Prefer standard-library solutions before adding third-party dependencies.

> **Precedence reminder:** Organization instructions are the **lowest**
> priority. If they conflict with repository or personal instructions, the
> higher-priority instruction wins.
````

---

## Setup

| Setting | Value |
|---|---|
| **VS Code** | Open **this demo folder** so `.github/` and `AGENTS.md` sit at the workspace root |
| **Mode** | Copilot Chat — **Agent** mode |
| **Model** | GPT-4.1 for edits; a reasoning model (Claude Sonnet 4.5 / GPT-5) for the multi-step step |
| **Prep** | Run `python device_registry.py` — the self-checks **fail** (TODOs not implemented) |
| **Settings** | Ensure `chat.useAgentsMdFile` is **on** so `AGENTS.md` is detected |

> **Instruction priority** (highest wins on conflict):
> **1. Personal** (user-level) → **2. Repository** (`.github/copilot-instructions.md` *or* `AGENTS.md`) → **3. Organization** (lowest).
> When multiple instructions exist, **all** of them are sent to the model —
> priority only decides the winner when they *contradict* each other.
> Path-based `.instructions.md` files are part of the repository layer and only
> apply to files matching their `applyTo` glob.

### Three ways to create instruction files from Copilot Chat

Show attendees there is more than one path to the same result — start manual,
then get progressively more automated.

**Option 1 — Manually create the files and folder structure**
- Create the folders and files by hand and type the rules yourself:
  - `.github/copilot-instructions.md` (repository)
  - `.github/instructions/<name>.instructions.md` with an `applyTo` header (path-based)
  - `AGENTS.md` at the workspace root (agent-agnostic repository baseline)
- **Use when:** you know exactly what rules you want, or you're copying a
  team standard into a new repo.

**Option 2 — Use `/` commands in the chat input**

| Command | How | Produces |
|---|---|---|
| **`/init`** | Type `/init` and press Enter | Analyzes the workspace and generates a tailored `.github/copilot-instructions.md` |
| **`/create-instructions`** | Type `/create-instructions <describe the rules>` | A new instructions file from your description |

- **Use when:** you want Copilot to bootstrap the file from your workspace or a
  short description, then you refine it.

**Exact prompts to type for each command (copy/paste on stage):**

1. **`/init` — no extra prompt needed.** Just type the command and press Enter:
   ```
   /init
   ```
   VS Code scans the workspace itself, so you do **not** append a description.
   After it finishes, verify with a follow-up prompt (Ask mode):
   ```
   What coding standards apply to this workspace, and where are they defined?
   ```

2. **`/create-instructions` — the command *is* the prompt.** Put the full
   description of the rules on the same line, right after the command:
   ```
   /create-instructions Repository standards for a Python device-registry service: descriptive names, small single-responsibility functions, docstrings on every public function, validate inputs at the boundary, never log PII, always use the internal auth library.
   ```
   For a **path-based** (Python-only) file, describe the scope so Copilot adds
   the `applyTo` header:
   ```
   /create-instructions Python-only conventions for .py files: use type hints, f-strings, and raise ValueError with a specific message on bad input. Set applyTo to **/*.py.
   ```

3. **`/instructions` (via the Instructions editor / Configure Chat).** If your
   build exposes it, type the command to open the instructions manager:
   ```
   /instructions
   ```
   Then use **Generate Instructions ▾** and pick the target (Workspace, User, or
   AGENTS.md). This is the same result as Option 3 below — a guided UI instead of
   a one-line prompt.

**Option 3 — Use the settings (gear) icon → Agent Customizations editor**
- In the Chat view, click the **settings / gear icon** → **Configure Chat** →
  **Instructions** to open the **Agent Customizations** editor.
- Click the **Generate Instructions ▾** dropdown and pick one (see screenshot):

  | Menu item | Creates |
  |---|---|
  | **New Instructions (Workspace)** | A workspace `.instructions.md` file (set `applyTo` for path-based rules) |
  | **New Instructions (User)** | A personal (user-level) instructions file |
  | **New AGENTS.md** | An `AGENTS.md` at the workspace root |

- The editor also **lists every instruction file already applied** (e.g.
  `copilot-instructions.md` under *Agent Instructions*, plus *Built-In*
  defaults), so it doubles as a way to *see* what's active.
- **Use when:** you want a guided UI, or you want to create personal/`AGENTS.md`
  files without remembering paths.

> All three options produce the **same kind of files** — the difference is
> only *how* you create them. The rest of this demo uses whichever is fastest
> to show each layer.

---

## Demo Flow (8 Steps)

### Step 1 — Generate Repository Instructions (`.github/copilot-instructions.md`)

**Goal:** Show that you don't have to hand-write instructions — VS Code can
**generate** them — and that a repo-root file then shapes **every** response.

**What to show (create it live — all three options):**
1. **Option 1 (manual):** Create `.github/copilot-instructions.md` by hand and
   type a rule or two, to prove there's no magic — it's just a Markdown file VS
   Code auto-detects.
2. **Option 3 (settings icon):** Open the Chat view → **settings / gear icon** →
   **Configure Chat** → **Instructions** to open the **Agent Customizations**
   editor. Select **Generate Instructions ▾ → New Instructions (Workspace)**.
3. **Option 2 (`/` command):** Type in the chat input:
   ```
   /init
   ```
   VS Code discovers existing conventions, analyzes the project structure, and
   generates a tailored `.github/copilot-instructions.md`.
4. *Or* describe exactly what you want:
   ```
   /create-instructions Repository standards for a Python device-registry service: descriptive names, small single-responsibility functions, docstrings on every public function, validate inputs at the boundary, never log PII, always use the internal auth library.
   ```
5. Compare the generated file to the shipped `.github/copilot-instructions.md`.
   Walk through the rules (naming, docstrings, validate-at-boundary, never log
   PII). Point out: *"VS Code auto-detects this file — no configuration needed."*

**Prompt (Ask mode — proves detection):**
```
What coding standards apply to this workspace, and where are they defined?
```
Copilot summarizes the repo instructions and cites the file.

**Talking point:** *"You generate the baseline once; every developer who opens
the repo gets identical Copilot behavior — the standards travel with the code."*

---

### Step 2 — Path-based Instructions (`.github/instructions/python.instructions.md`)

**Goal:** Show file-scoped rules that only apply to matching files — and how to
**create** them from the Agent Customizations editor.

**What to show:**
1. In the **Agent Customizations** editor select **Generate Instructions ▾ →
   New Instructions (Workspace)** to scaffold a `.instructions.md` file, or run
   `/create-instructions Python-only conventions for .py files`. VS Code creates
   the file with a YAML header — set `applyTo: "**/*.py"`.
2. Open `.github/instructions/python.instructions.md`. Highlight the YAML
   header: `applyTo: "**/*.py"` and the `description`.
3. Explain: *"These Python-only rules (type hints, f-strings, raise ValueError)
   layer **on top of** the repository rules — but only for `.py` files."*

**Prompt (Agent mode — implement one function):**
```
Implement the register_device function in #file:device_registry.py.
```
**Watch for the layered result:**
- **Type hints** on the signature → from *path-based* Python rules
- **Triple-quote docstring with Args/Raises** → from *path-based* rules
- **Input validation + specific error message** → from *repository* rules
- **Raises instead of returning None on duplicate** → repository + path rules

**Talking point:** *"Same chat, two files applied automatically. Copilot merged
the repository baseline with the Python-specific layer — I never re-typed either."*

---

### Step 3 — Create & Use an `AGENTS.md` File

**Goal:** Show the agent-agnostic instructions file that any AI coding agent can
read — and how VS Code auto-detects it at the workspace root.

**What to show (create it live):**
1. In the **Agent Customizations** editor select **Generate Instructions ▾ →
   New AGENTS.md**. VS Code scaffolds `AGENTS.md` at the workspace root.
2. Open the shipped `AGENTS.md` in this demo folder and walk through it. Explain
   it is **auto-detected** and applied to **every** chat request — the same
   repository layer as `copilot-instructions.md`.
3. Point out the enabling setting:
   ```jsonc
   "chat.useAgentsMdFile": true
   ```

**When to reach for `AGENTS.md` instead of `copilot-instructions.md`:**
- You work with **multiple AI agents** and want **one** file all of them read.
- You want **subfolder-level** instructions for specific parts of a **monorepo**.

**Multiple `AGENTS.md` files (experimental):**
- Enable `chat.useNestedAgentsMdFiles` so VS Code searches subfolders
  recursively and adds each `AGENTS.md` path to the chat context (e.g. one for
  `frontend/`, one for `backend/`). The agent picks the relevant one based on the
  files being edited.
- For folder-specific rules you can also use multiple `.instructions.md` files
  with `applyTo` globs matching the folder structure.

**Prompt (Ask mode — proves detection):**
```
Which instruction files apply here, and what does AGENTS.md add on top of copilot-instructions.md?
```

**Talking point:** *"`AGENTS.md` is the vendor-neutral baseline — one file every
agent honors. In a monorepo, drop one per package so each area gets its own
rules without touching the others."*

---

### Step 4 — Show Precedence with a Conflict

**Goal:** Make precedence visible, not just theoretical.

**What to show:**
1. In VS Code, open **Personal instructions** (Chat → **Configure Chat** →
   **Instructions** → user profile) and temporarily add one line:
   ```
   Prefix every generated function's docstring with "OWNER-APPROVED:".
   ```
2. Re-run a generation prompt:
```
Implement the get_device function in #file:device_registry.py.
```
3. The docstring now starts with `OWNER-APPROVED:` — the **personal** instruction
   won over repository and org rules.

**Talking point:** *"When instructions conflict, priority decides:
**Personal → Repository → Organization**. Personal is the tie-breaker; org is
the fallback baseline. Repository covers both `copilot-instructions.md` and
`AGENTS.md`."* Remove the personal line before continuing.

---

### Step 5 — Organization Instructions (GitHub.com)

**Goal:** Show the third layer and where it lives.

**What to show:**
1. Open `ORG_INSTRUCTIONS_REFERENCE.md` — explain org instructions are set in
   **GitHub.com → Org → Settings → Copilot → Custom instructions**, *not* a
   repo file.
2. In VS Code settings, show the toggle:
   ```jsonc
   "github.copilot.chat.organizationInstructions.enabled": true
   ```
3. Open **Chat → Instructions** menu and point out that personal, workspace,
   and organization instructions all appear together.

**Talking point:** *"Org instructions give every repo a shared baseline — license
headers, secure-coding rules — without copying files into each project. They're
lowest priority, so teams can still specialize per repo."*

---

### Step 6 — Settings-based Instructions (Review · Commits · PRs)

**Goal:** Show the instruction types that live in **VS Code settings** instead
of auto-detected files — for **code review**, **commit messages**, and **pull
request descriptions**.

> **Note:** Settings-based *code generation* and *test generation* instructions
> are **deprecated** as of VS Code 1.102 — use file-based instructions for those.
> Review, commit, and PR-description instructions are **still** configured in
> settings. Each setting takes an array of objects with either a `text` (inline)
> or `file` (path to a Markdown file) property.

**What to show:**
1. Open `.vscode/settings.json`. Walk through the three settings and how they
   point at Markdown files (and one inline `text` entry):

   | Scenario | Setting |
   |---|---|
   | Code review | `github.copilot.chat.reviewSelection.instructions` |
   | Commit messages | `github.copilot.chat.commitMessageGeneration.instructions` |
   | PR descriptions | `github.copilot.chat.pullRequestDescriptionGeneration.instructions` |

2. Open the three referenced files under `.github/` so attendees see the content.

**Demo A — Commit message:**
- Stage your changes in the **Source Control** panel.
- Click the **✨ (Generate Commit Message)** button.
- The result follows `commit-message-instructions.md` — Conventional Commits
  format, imperative summary under 72 chars, a "why" body.

**Demo B — Code review:**
- Select a completed function in `device_registry.py`.
- Right-click → **Copilot → Review and Comment** (or Command Palette →
  *Copilot: Review and Comment*).
- Review comments follow `code-review-instructions.md` — correctness, input
  validation, security, missing tests — plus the inline rule flagging any
  function missing a docstring or type hints.

**Demo C — PR description:**
- With the **GitHub Pull Requests** extension, start **Create Pull Request**.
- Use the ✨ generate action for the description.
- The output follows `pr-description-instructions.md` — Summary / Why / Changes
  / Testing / Risk sections.

**Talking point:** *"These three surfaces don't read `copilot-instructions.md`
automatically — they're wired through settings. Use the `file` property to keep
the rules in version control and share them with the team, or `text` for quick
inline rules."*

---

### Step 7 — Finish the File (Agent, all layers together)

**Goal:** Let all layers drive a full multi-function implementation.

**Prompt (Agent mode — reasoning model):**
```
Complete all remaining TODO functions in #file:device_registry.py so the
self-checks in the __main__ block pass. Follow the project and Python
instructions.
```

**Verify:**
```bash
python device_registry.py
# → All device registry self-checks passed.
```

Confirm every function has type hints, Args/Returns docstrings, f-string error
messages, and raises on bad input — proof that both instruction layers applied
consistently across the whole file.

---

## Recap — What attendees should take away

| Layer | Where it lives | Applies to | Priority |
|---|---|---|---|
| **Personal** | VS Code user profile | your chats everywhere | **Highest** |
| **Repository** | `.github/copilot-instructions.md` **or** `AGENTS.md` | whole workspace | Middle |
| **Path-based** | `.github/instructions/*.instructions.md` (`applyTo`) | matching files only | part of repo layer |
| **Organization** | GitHub.com org settings | all your repos | **Lowest** |
| **Settings-based** | `.vscode/settings.json` (`text`/`file`) | code review · commits · PR descriptions | per-scenario |

### Three ways to create instruction files (recap)

| Option | How | Prompt to type |
|---|---|---|
| **1. Manually** | Create the file/folder yourself and type the rules | *(none — you write the file)* |
| **2. `/` commands** | `/init` or `/create-instructions <describe rules>` in chat | `/init` *(no args)* · `/create-instructions <rules>` · `/instructions` *(opens editor)* |
| **3. Settings icon** | Chat **gear** → **Configure Chat** → **Instructions** → **Generate Instructions ▾** | *(none — guided UI)* |

### How to create each one

| Want to create… | Fastest way |
|---|---|
| Repository instructions | `/init` · `/create-instructions` · **Generate Instructions → New Instructions (Workspace)** |
| An `AGENTS.md` | **Generate Instructions → New AGENTS.md** |
| Personal instructions | **Generate Instructions → New Instructions (User)** |
| Path-based `.instructions.md` | **New Instructions (Workspace)**, then set `applyTo` |

### Tips for writing effective instructions

- Keep each instruction **short and self-contained** — one simple statement per rule.
- Explain the **why** ("use `date-fns` instead of `moment.js` because moment.js is deprecated and bloats the bundle") so the AI handles edge cases well.
- Show **preferred vs. avoided** patterns with concrete code examples.
- Focus on **non-obvious** rules — skip what linters/formatters already enforce.
- Split language/task rules into multiple `*.instructions.md` files with `applyTo`.
- **Commit** instruction files so the whole team shares them.

> **Rule of thumb:** Generate one `.github/copilot-instructions.md` (or an
> `AGENTS.md` if you use multiple agents) as the baseline. Add `.instructions.md`
> files when different file types need different rules. Use org instructions for
> company-wide baselines. Personal instructions win on conflict.

---

# Part B — Copilot Memory (Python Watchdog Timer Manager)

**Files:** `watchdog_timer.py` · `watchdog_timer_fixed.py` · `test_watchdog_timer.py`

### Objective
Demonstrate how **Copilot Memory** (both local and GitHub-hosted) improves agent output
when working with **Python** code that must follow specific project conventions.

The agent learns project conventions through memory — so it stops repeating the same
mistakes across sessions and applies learned patterns automatically.

This demo explicitly differentiates three memory levels:
- **User memory** (`/memories/`): personal preferences that carry across chats
- **Session memory** (`/memories/session/`): temporary context for only this chat session
- **Repo memory** (`/memories/repo/`): repository facts visible at
	`https://github.com/<repo>/settings/copilot/memory`

### Scenario
You're developing a **watchdog timer manager** that monitors smart-home devices.
The starter code has several style violations and bugs. Through iterative prompts,
Copilot learns your project's Python conventions via memory.

### Prerequisites
- VS Code with GitHub Copilot (Agent Mode)
- Python 3.11+ with the Python extension (Pylance)
- Memory Tool enabled (`github.copilot.chat.tools.memory.enabled: true`)
- Optionally: Copilot Memory enabled on GitHub

### Files
| File | Purpose |
|------|---------|
| `watchdog_timer.py` | Watchdog timer manager — **has intentional style violations** |
| `watchdog_timer_fixed.py` | Reference: corrected version following the project's Python conventions |
| `test_watchdog_timer.py` | Unit tests (pytest-style names, stdlib-only standalone harness) |

#### `watchdog_timer.py` (buggy starter)

```python
"""Smart-home watchdog timer manager — BUGGY VERSION.

INTENTIONAL BUGS & STYLE VIOLATIONS — for the Copilot Memory demo.

This module manages watchdog timers that monitor smart-home devices. It
tracks multiple timer registrations and their callbacks. The Python version
mirrors the original UEFI demo, but the conventions worth teaching are now
Python conventions instead of EDK2/ETL ones.

BUGS TO FIND:
1. Uses print() instead of the logging module
2. Missing type hints on function signatures
3. Uses an unbounded list instead of a fixed-capacity collections.deque
4. Returns None / error strings instead of raising exceptions
5. Missing input validation on critical parameters
6. Uses camelCase instead of snake_case (PEP 8)
7. Magic numbers instead of named constants
(and: missing docstrings on public functions)

Demo 1: Copilot Memory — Python Watchdog Timer
"""

# BUG #3: unbounded list — should be a fixed-capacity deque
gTimerRegistry = []          # BUG #6: camelCase, should be snake_case

gNextTimerId = 1             # BUG #6: camelCase
gDriverInitialized = False   # BUG #6: camelCase


def registerWatchdog(timeoutMs, recurring, callback, label):   # BUG #2, #6
    # BUG: missing docstring
    global gNextTimerId

    # BUG #5: no validation on callback / label
    # BUG #7: magic number 0
    if timeoutMs == 0:
        return None          # BUG #4: returns None instead of raising

    # BUG #3 + #7: magic number 16, unbounded list
    if len(gTimerRegistry) >= 16:
        return "ERROR_FULL"  # BUG #4: returns an error string

    entry = {
        "timerId": gNextTimerId,
        "timeoutMs": timeoutMs,
        "isActive": True,
        "isRecurring": recurring,
        "callback": callback,
        "label": label if label else "",
    }
    gNextTimerId += 1
    gTimerRegistry.append(entry)   # BUG #3: unbounded growth

    return entry["timerId"]


def cancelWatchdog(timerId):     # BUG #2, #6
    # BUG #7: magic number 0
    if timerId == 0:
        return None              # BUG #4

    for entry in gTimerRegistry:
        if entry["timerId"] == timerId:
            entry["isActive"] = False
            return True
    return False                 # BUG #4: returns False instead of raising


def getWatchdogStatus(timerId):  # BUG #2, #6
    # BUG #5: no validation
    for entry in gTimerRegistry:
        if entry["timerId"] == timerId:
            return entry
    return None                  # BUG #4: returns None on not found


def periodicTimerCheck():        # BUG #2, #6
    expiredIds = []
    for entry in gTimerRegistry:
        if entry["isActive"]:
            # BUG #7: magic number 100
            if entry["timeoutMs"] <= 100:
                expiredIds.append(entry["timerId"])
                if entry["callback"] is not None:
                    entry["callback"](entry["timerId"])
                if not entry["isRecurring"]:
                    entry["isActive"] = False
    print("Expired timers:", expiredIds)   # BUG #1: print instead of logging
    return expiredIds


def initWatchdogDriver():        # BUG #2, #6
    global gDriverInitialized
    if gDriverInitialized:
        return "ALREADY_STARTED"  # BUG #4
    gDriverInitialized = True
    print("WatchdogTimer: Driver initialized")  # BUG #1
    return True


def shutdownWatchdogDriver():    # BUG #2, #6
    global gDriverInitialized
    if not gDriverInitialized:
        return False             # BUG #4
    gTimerRegistry.clear()
    gDriverInitialized = False
    print("WatchdogTimer: Driver shutdown")  # BUG #1
    return True
```


### The conventions this demo teaches
These are the Python analogs of the original firmware rules — they map directly
to this repository's `.github/instructions/python.instructions.md`:

| # | Buggy pattern | Convention to learn |
|---|---------------|---------------------|
| 1 | `print()` for status output | Use the `logging` module |
| 2 | No type hints | Type hints on every function signature |
| 3 | Unbounded `list` growth | Fixed-capacity `collections.deque(maxlen=...)` |
| 4 | Returns `None` / error strings | Raise exceptions (`ValueError`, custom `WatchdogError`) |
| 5 | No input validation | Validate inputs at the boundary, fail early |
| 6 | `camelCase` names | `snake_case` (PEP 8) |
| 7 | Magic numbers (`0`, `16`, `100`) | Named constants |

### Demo Steps (15 minutes)

#### Step 1: Show the Buggy Code (2 min)
Open `watchdog_timer.py` and point out:
- Uses `print()` instead of the `logging` module
- No type hints on any function
- Uses an unbounded `list` instead of a fixed-capacity `deque`
- Returns `None`/`"ERROR_FULL"` instead of raising exceptions
- No input validation (missing `callback`/`label` checks)
- `camelCase` names instead of `snake_case`
- Magic numbers instead of named constants

#### Step 2: Ask the Agent to Fix — First Pass (2 min)
```
Fix the bugs in watchdog_timer.py. This is a Python watchdog timer manager.
Key rules:
- Use the logging module instead of print()
- Add type hints to every function signature
- Use a fixed-capacity collections.deque(maxlen=...) instead of an unbounded list
- Raise exceptions (ValueError / a custom WatchdogError) instead of returning None
- Validate inputs at the boundary and fail early with a specific message
- Use snake_case (PEP 8) and named constants instead of magic numbers
```

#### Step 3: Create User Memory (3 min)
Use user-level memory to store a personal coding preference.

```
Create a user memory at /memories/python-personal-style.md with this content:
- Prefer short comments above complex timer logic.
- Keep helper function names action-oriented (start/stop/register/cancel).
```

Then ask:

```
Apply my personal style memory while improving naming/comments in watchdog_timer.py.
```

Expected behavior:
- It applies your personal style hints (comment style and naming tone)
- This preference can carry to future chats for you

#### Step 4: Create Session Memory (3 min)
Now store temporary context that should only matter for this live demo session.

```
Create a session memory at /memories/session/demo-focus.md with this content:
This session should prioritize one-shot watchdog examples over recurring timers.
```

Then ask:

```
Generate one new watchdog usage example for this session.
```

Expected behavior:
- It prioritizes one-shot usage examples
- This should be session-scoped and not a long-term repository rule

#### Step 5: Create Repo Memory (3 min)
Store a stable project convention in repo memory.

```
Create a repository memory entry at /memories/repo/python-watchdog-conventions.json
using this JSON:
{
	"subject": "Python watchdog manager conventions",
	"fact": "In this repository, watchdog demos should use a fixed-capacity collections.deque(maxlen=...) and raise exceptions (ValueError / WatchdogError) instead of using an unbounded list or returning None to signal errors.",
	"citations": [
		"Demo-1/Memory/watchdog_timer_fixed.py",
		"Demo-1/Memory/test_watchdog_timer.py"
	],
	"reason": "This keeps demos aligned with the project's Python conventions (type hints, snake_case, raise-don't-return, bounded collections).",
	"category": "conventions"
}
```

Then validate with:

```
Implement a new watchdog helper that tracks timers and returns their active state.
```

Expected behavior:
- Uses a fixed-capacity `deque`, not an unbounded `list`
- Raises exceptions instead of returning `None`/error strings
- Uses type hints and `snake_case`

#### Step 6: Verify Repo Memory on GitHub (2 min)
Open:
`https://github.com/<repo>/settings/copilot/memory`

Confirm the memory entry is visible with:
- Subject: `Python watchdog manager conventions`
- Category: `conventions`
- Fact matching your stored rule

#### Step 7: Fresh Chat Validation (optional)
Start a new chat and ask:

```
Write a new Python watchdog helper for timer registration.
```

Expected behavior:
- Repo convention is still applied
- Session-only hint (one-shot preference) should not be treated as permanent

### Running the code and tests
```bash
# The buggy module has no __main__ block — it is imported by the tests.
# Run the tests (standalone, no framework needed)
python test_watchdog_timer.py

# Or with pytest, if installed
python -m pytest test_watchdog_timer.py
```

### Memory Level Differentiation (Talk Track)
- **User memory (`/memories/`)**: personal style/preferences for you
- **Session memory (`/memories/session/`)**: temporary, specific to this active conversation
- **Repo memory (`/memories/repo/`)**: stable project facts for repository-wide Copilot behavior

### What to Highlight
- **Without memory**: Agent may default to generic Python (`print`, unbounded `list`, return `None`)
- **With user memory**: Personal output style becomes consistent
- **With session memory**: Temporary demo constraints are followed quickly
- **With repo memory**: Project conventions persist and are visible in GitHub Copilot Memory settings

### Troubleshooting
- If repo memory does not appear on GitHub immediately, refresh after a short delay
- Confirm repo memory path is exactly `/memories/repo/<name>.json`
- Ensure JSON contains `subject`, `fact`, `citations`, `reason`, and `category`
- Verify Copilot memory is enabled for the repository/org

### Key Teaching Points
1. Memory eliminates repetitive "remind the AI" prompts
2. Domain-specific conventions benefit enormously from memory
3. The "raise-don't-return" and "bounded collections" rules must be explicitly taught — memory retains this
4. Combining `copilot-instructions.md` (explicit rules) + memory (learned patterns) = best results
