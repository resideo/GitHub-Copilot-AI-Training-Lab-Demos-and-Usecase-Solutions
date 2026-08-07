---
title: Demo 6 - Custom Instructions & Validation
layout: default
parent: Demos
grand_parent: Prompt Engineering
nav_order: 6
permalink: /prompt-engg/demos/demo-6.html
description: "Show how custom instructions change output and why validation closes the loop."
---

# ⚙️ Demo 6 — Custom Instructions & Validation
{: .no_toc }

**Deck checkpoint:** Final demo (slide 69 — *"Prompt engineering in action with GitHub Copilot"*)

**Language:** **Python**

**Duration:** ~11–13 minutes · **Prompts:** 5 (same task at 3 instruction levels + meta-prompt + validation)

**Covers:** Custom instruction files (baseline → project-wide → scoped) · `applyTo` scoping · meta-prompting · the validation loop

> The finale: instruction files steer *every* prompt automatically, then we compose the strongest
> workflow — context → plan → apply → validate.

{: .highlight }
> **No clone required.** Copy the three files below into a folder named
> `demo-6-custom-instructions`, preserving the `.github/` and `src/` paths, then follow the steps.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Files

| File | Purpose |
|---|---|
| `src/payment.py` | `process_payment` — the same request produces different code as rules switch on |
| `.github/copilot-instructions.md.disabled` | Project-wide guardrails (rename to enable) |
| `.github/instructions/src.instructions.md.disabled` | Scoped rules via `applyTo` (rename to enable) |

### `src/payment.py`

```python
# A simple payment helper used by the Custom Instructions demo.
#
# During the demo you will ask Copilot to add logging and authentication to
# process_payment, and observe how the answer changes as you add instructions:
#   1. Baseline    -> no instruction files active
#   2. Project-wide -> .github/copilot-instructions.md
#   3. Scoped      -> a *.instructions.md with applyTo targeting src/**
#
# See demo.md in this folder for the exact prompts and instruction file contents.


def process_payment(user_email: str, amount: float, card_number: str) -> bool:
		# TODO (live demo): ask Copilot to "add logging and authentication here".
		# Baseline: it may log the email/card and roll its own auth.
		# With instructions active: it should avoid logging PII and use the
		# internal authentication library.
		charged = amount > 0
		return charged
```

### `.github/copilot-instructions.md.disabled`

````markdown
# Project-wide Copilot instructions (DEMO — DISABLED BY DEFAULT)
#
# ------------------------------------------------------------------
# HOW TO ENABLE FOR THE DEMO
#   Rename this file to remove the ".disabled" suffix:
#     .github/copilot-instructions.md.disabled  ->  .github/copilot-instructions.md
#   VS Code auto-loads .github/copilot-instructions.md for EVERY Copilot request
#   in this workspace. Rename it back to ".disabled" to turn it off again.
# ------------------------------------------------------------------
#
# These rules apply repo-wide once enabled.

- Never log personally identifiable information (PII) such as emails or card numbers.
- Always use our internal authentication library `internal_auth` for authentication checks.
- Prefer explicit type hints on all public functions.
- Write clear, self-documenting names; avoid abbreviations.
````

### `.github/instructions/src.instructions.md.disabled`

````markdown
---
applyTo: "**/demo-6-custom-instructions/src/**/*.py"
---
# Scoped instructions for payment source (DEMO — DISABLED BY DEFAULT)
#
# ------------------------------------------------------------------
# HOW TO ENABLE FOR THE DEMO
#   Rename this file to remove the ".disabled" suffix:
#     .github/instructions/src.instructions.md.disabled
#       ->  .github/instructions/src.instructions.md
#   The YAML `applyTo` glob above limits these rules to the demo's src files only.
#   Rename it back to ".disabled" to turn it off again.
# ------------------------------------------------------------------

- All monetary amounts must use `decimal.Decimal`, never `float`.
- Every payment function must validate the amount is greater than zero and raise
	`ValueError` otherwise.
- Return structured results (e.g., a dataclass or dict with a status and reason),
	not bare booleans, so callers get a reason on failure.
````

> **How to toggle:** both instruction files ship with a `.disabled` suffix so they're inert. Remove
> `.disabled` to ENABLE, add it back to DISABLE. Reset both to `.disabled` when the demo ends.

## Setup

| Setting | Value |
|---|---|
| **Mode** | Ask, then Edit |
| **Model** | Auto or a reasoning model |
| **Prep** | Copy all three files from this page, preserve the folder paths, then open `src/payment.py` and confirm both instruction files still end in `.disabled` |

---

## Part A — Instructions steer every prompt (≈6 min)

Open `src/payment.py`. Same request all three levels — watch the generated code change as rules
switch on.

**The task prompt (identical for all three levels):**
```
Add logging and an authentication check to process_payment.
```

**Level 1 — Baseline (both files still `.disabled`):** run the task prompt.
*Expected:* Copilot may log the email/card (PII) and invent its own auth. Point this out against the
org rules.

**Level 2 — Project-wide:** rename `.github/copilot-instructions.md.disabled` →
`.github/copilot-instructions.md`, then re-run the **same** task prompt.
*Expected:* no PII in logs, uses the internal auth library, adds type hints. Applies repo-wide.

**Level 3 — Scoped:** rename `.github/instructions/src.instructions.md.disabled` →
`.github/instructions/src.instructions.md` (note its `applyTo` glob targets this demo's `src/**`),
then re-run the **same** prompt.
*Expected:* now also uses `decimal.Decimal`, validates `amount > 0`, and returns a structured
result — but only because the file matches the `applyTo` path.

| Level | Active | Effect | Scope |
|---|---|---|---|
| 1 Baseline | Nothing | May log PII, ad-hoc auth | — |
| 2 Project-wide | `copilot-instructions.md` | No PII, internal auth, type hints | Whole repo |
| 3 Scoped | `+ src.instructions.md` | Above **plus** Decimal money, validation, structured result | Only `src/**` |

**Talking point:** **project-wide** = team guardrails; **scoped** = per-folder/per-language rules via
`applyTo`. Instructions cut repetitive prompting — the rules travel with the repo.

> Reset: rename both files back to `.disabled` so the demo starts clean next time.

---

## Part B — Meta-prompt, then validate (≈5 min)

Stay in `src/payment.py` with the instructions still enabled.

**Prompt — meta-prompt (ask Copilot to write the prompt first):**
```
Before editing code, write the exact production-grade prompt you would give GitHub Copilot to harden process_payment safely. The prompt must include the goal, inputs, exact return contract, constraints, validation steps, and a short do-not-change list.
```
**Talking point:** **meta-prompting** — use Copilot to design the prompt, not just the code.

**Prompt — apply with validation (switch to Edit):** use the drafted prompt or this fallback:
```
Harden process_payment and add a __main__ block with three lightweight self-checks that print "valid_charge : PASS/FAIL", "rejects_zero : PASS/FAIL", and "no_pii_logged : PASS/FAIL". Keep it Python 3.11, standard-library only, and easy to read.
```

---

## Verify

```bash
python src/payment.py
```

Expected (shape):
```text
valid_charge : PASS
rejects_zero : PASS
no_pii_logged : PASS
```

## Takeaway

The strongest workflow in this course:

1. **Attach the right context** (files + policy) and let **custom instructions** carry your standards.
2. **Ask for the plan or prompt first** (meta-prompting).
3. **Apply** the change.
4. **Validate immediately.**
