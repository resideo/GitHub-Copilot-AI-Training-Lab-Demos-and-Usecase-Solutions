---
title: Demo 1 — Planning → Code → Test → Debug → Review
layout: default
parent: Demos
grand_parent: Intermediate
nav_order: 1
permalink: /intermediate/demos/demo-1.html
description: "Walk the full Copilot-assisted lifecycle on a Python Order Processing System: Ask, Plan, Edit, Agent, and #file debugging."
---

# 🐍 Demo 1 — Planning → Code → Test → Debug → Review with GitHub Copilot
{: .no_toc }

**Agenda section:** Planning, Code Creation, Testing, Debugging, Review
**Deck demo:** Full Copilot Workflow — Brainstorm · Plan · Code · Test · Debug · Review · Right Tool Recap
**Language:** **Python**

Walk through the **complete Copilot-assisted development lifecycle** on a small
Order Processing System. Each step maps to a Copilot mode so attendees see
*when* and *why* to reach for each surface.

{: .highlight }
> **No clone required.** Copy the two files below into a folder named
> `demo-1-choosing-tools-modes`, then follow the steps.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Files

| File | Purpose |
|---|---|
| `order_service.py` | Core order functions — has **1 bug** and **3 missing functions** |
| `order_processor.py` | Batch processor — imports from `order_service.py`, has **1 NameError bug** |

### `order_service.py`

```python
"""Demo 1 — Planning → Code → Test → Debug → Review with GitHub Copilot
Language: Python   |   Run: python order_service.py

Scenario: Build an Order Processing System step-by-step using every
Copilot mode — Ask, Plan, Edit, Agent, and Inline Chat.

Demo flow:
  1. Brainstorm  (Ask)    — what should an order system include?
  2. Plan        (Plan)   — plan the implementation steps
  3. Complete    (Edit)   — fill in missing functions
  4. Test        (Agent)  — generate and run pytest tests
  5. Debug       (# cmd)  — fix crash in order_processor.py
  6. Review      (Ask)    — review both scripts
  7. Recap       (all)    — right tool for the right job
"""

ITEMS = [
    {"name": "Keyboard", "price": 45.0, "qty": 2},
    {"name": "Mouse",    "price": 25.0, "qty": 1},
    {"name": "Monitor",  "price": 30.0, "qty": 1},
]

# Orders at or above this subtotal get a 10 % discount.
DISCOUNT_THRESHOLD = 100.0
DISCOUNT_RATE = 0.10
TAX_RATE = 0.08


def order_total(items):
    """Sum of price * qty for every item."""
    return sum(item["price"] * item["qty"] for item in items)


def apply_discount(subtotal):
    """Apply a 10 % discount when the order qualifies.

    BUG (fix live):
      - uses > instead of >= so an order of exactly 100.00 misses the discount
      - does not round to 2 decimals
    """
    if subtotal > DISCOUNT_THRESHOLD:          # bug: should be >=
        return subtotal * (1 - DISCOUNT_RATE)
    return subtotal


# ----- TODO: add these with Edit / Ghost-text mode -----

# def line_items_summary(items):
#     """Return list of strings like 'Keyboard x2 = $90.00'."""
#     ...

# def calculate_tax(amount):
#     """Return tax rounded to 2 decimals."""
#     ...

# def generate_invoice(items):
#     """Return a dict with subtotal, discount, tax, and total."""
#     ...


# --------------- self-checks ---------------

def _check(name, actual, expected):
    ok = actual == expected
    tag = "PASS" if ok else "FAIL"
    detail = "" if ok else f"  (got {actual!r}, want {expected!r})"
    print(f"{name:<16}: {tag}{detail}")
    return ok


if __name__ == "__main__":
    subtotal = order_total(ITEMS)                       # 45*2 + 25 + 30 = 145.0
    c1 = _check("order_total", subtotal, 145.0)

    # Exactly-100 order should be discounted to 90.00 (FAILS until bug is fixed).
    c2 = _check("apply_discount", apply_discount(100.0), 90.0)

    # Passes only after line_items_summary is added.
    c3 = False
    try:
        summary = line_items_summary(ITEMS)             # noqa: F821
        c3 = summary[0] == "Keyboard x2 = $90.00"
    except NameError:
        pass
    _check("summary", c3, True)

    # Passes only after calculate_tax is added.
    c4 = False
    try:
        c4 = calculate_tax(100.0) == 8.0                # noqa: F821
    except NameError:
        pass
    _check("calculate_tax", c4, True)

    # Passes only after generate_invoice is added.
    c5 = False
    try:
        inv = generate_invoice(ITEMS)                   # noqa: F821
        c5 = "total" in inv and "tax" in inv
    except NameError:
        pass
    _check("generate_invoice", c5, True)

    all_pass = c1 and c2 and c3 and c4 and c5
    print("\nALL PASS" if all_pass else "\nSOME FAILED")
```

### `order_processor.py`

```python
"""Order Processor — batch-processes orders using order_service.
Run:  python order_processor.py

This script imports from order_service and processes a list of customer
orders.  It contains TWO bugs that surface only at runtime:

  BUG 1 — NameError: references 'total' instead of 'discounted'
  BUG 2 — Calls generate_invoice() which may not exist yet

Use the #file command in Copilot Chat to reference both files and fix.
"""

from order_service import order_total, apply_discount


def process_orders(order_list):
    """Process multiple customer orders and return a results list."""
    results = []
    for order in order_list:
        subtotal = order_total(order["items"])
        discounted = apply_discount(subtotal)
        tax = round(discounted * 0.08, 2)

        # BUG 1: 'total' is not defined — should be 'discounted + tax'
        results.append({
            "customer": order["customer"],
            "subtotal": subtotal,
            "discounted": discounted,
            "tax": tax,
            "total": total,                       # NameError
        })
    return results


def print_report(results):
    """Print a formatted report of all processed orders."""
    print("=" * 50)
    print(f"{'CUSTOMER':<15} {'SUBTOTAL':>10} {'DISCOUNT':>10} {'TAX':>8} {'TOTAL':>10}")
    print("-" * 50)
    for r in results:
        saving = r["subtotal"] - r["discounted"]
        print(f"{r['customer']:<15} ${r['subtotal']:>8.2f} "
              f"  -${saving:>6.2f}  ${r['tax']:>6.2f} ${r['total']:>8.2f}")
    grand = sum(r["total"] for r in results)
    print("-" * 50)
    print(f"{'GRAND TOTAL':<15} {'':>10} {'':>10} {'':>8} ${grand:>8.2f}")
    print("=" * 50)


# ---------- sample data ----------

ORDERS = [
    {
        "customer": "Alice",
        "items": [
            {"name": "Keyboard", "price": 45.0, "qty": 2},
            {"name": "Mouse",    "price": 25.0, "qty": 1},
        ],
    },
    {
        "customer": "Bob",
        "items": [
            {"name": "Monitor", "price": 250.0, "qty": 1},
            {"name": "Cable",   "price": 10.0,  "qty": 3},
        ],
    },
    {
        "customer": "Carol",
        "items": [
            {"name": "USB Hub", "price": 20.0, "qty": 5},
        ],
    },
]


if __name__ == "__main__":
    results = process_orders(ORDERS)
    print_report(results)
```

---

## Setup

| Setting | Value |
|---|---|
| **Modes shown** | Ask · Plan · Edit / Ghost text · Agent · Inline Chat (Ctrl+I) |
| **Model** | GPT-4.1 for Ask/Inline/Edit; a reasoning model (o3 / Claude Sonnet 4) for Agent & Plan |
| **Prep** | Open both `.py` files in the editor. Run `python order_service.py` to show initial failures. |

---

## Demo Flow (8 Steps)

### Step 1 — Brainstorm Ideas (Ask Mode + Auto Model + `#folder`)

**Goal:** Show how Ask mode helps explore a problem space *before writing any
code* — and how `#folder` gives Copilot full context of the project at once.

**1a. Add the folder for context:**

In the Copilot Chat input, type `#` and select **folder**, then pick the
`demo-1-choosing-tools-modes` folder. This attaches *every file* in the folder
to the conversation so Copilot can see the full picture.

> **Tip:** `#folder:demo-1-choosing-tools-modes` is shorthand — you can also
> click the 📎 (attach) icon → **Folder** and browse to it.

**1b. Prompt (Ask Mode + Auto Model):**
```
#folder:demo-1-choosing-tools-modes
I'm building an Order Processing System in Python. Look at all the files
in this folder. It currently has order_total() and apply_discount(). What
other functions and features should I add to make it production-ready?
Consider tax, invoicing, reporting, and edge cases.
```

**What happens:** Because the whole folder is attached, Copilot reads both
`order_service.py` and `order_processor.py` together. It suggests features like
tax calculation, invoice generation, line-item summaries, input validation,
etc. — and it can also flag the existing bugs across files.

**Talking point:** *"Use `#folder` when you want Copilot to see everything in a
directory — not just one file. Ask is your brainstorming partner. No files
change — it's a safe space to think out loud before touching code."*

---

### Step 2 — Plan the Implementation (Plan Mode + Auto Model + `#file`)

**Goal:** Show Plan mode creating a step-by-step implementation plan.

**Prompt (Plan Mode + Auto Model):**
```
Look at #file:order_service.py — it has TODO stubs for
line_items_summary, calculate_tax, and generate_invoice. Also look at
#file:order_processor.py which imports from it. Create a step-by-step plan
to complete all missing functions, fix any bugs, add tests, and make
order_processor.py run successfully.
```

**What happens:** Copilot generates a numbered plan: fix `apply_discount` bug →
add `line_items_summary` → add `calculate_tax` → add `generate_invoice` → fix
`order_processor.py` → add tests.

**Talking point:** *"Plan mode gives you a roadmap you can review before any code
is generated. It's especially useful for multi-file tasks."*

---

### Step 3 — Complete the Code (Edit / Agent Mode)

**Goal:** Fill in the three missing functions using Edit mode.

**Prompt (Edit / Agent Mode — reference `order_service.py`):**
```
In #file:order_service.py complete the three TODO functions:
1. line_items_summary(items) — return a list of strings like "Keyboard x2 = $90.00"
2. calculate_tax(amount) — return amount * TAX_RATE rounded to 2 decimals
3. generate_invoice(items) — return a dict with keys: subtotal, discount,
   tax, total (all rounded to 2 decimals)
Do not change existing functions.
```

**What happens:** Edit mode adds all three functions in place. Self-checks for
`summary`, `calculate_tax`, and `generate_invoice` now pass.

**Verify:**
```bash
python order_service.py    # apply_discount still FAILS (bug not fixed yet)
```

**Talking point:** *"Edit mode is your multi-function code generator — you give a
spec, it writes. Ghost text (tab-completion) is great for filling in one line at
a time."*

---

### Step 4 — Generate Tests (Agent Mode)

**Goal:** Let the Agent create a test file and run it.

**Prompt (Agent — reasoning model):**
```
Create pytest tests for #file:order_service.py covering:
- order_total with empty list and normal items
- apply_discount at boundary (exactly 100), below, and above
- line_items_summary format
- calculate_tax rounding
- generate_invoice end-to-end
```

Save as `test_order_service.py` in the same folder. Run `python -m pytest -v`
and fix anything that fails.


**What happens:** Agent creates `test_order_service.py`, runs pytest, discovers
the `apply_discount` boundary bug (exactly 100.0 returns 100.0 instead of
90.0), and may auto-fix it or flag it.

**Talking point:** *"Agent owns the full loop — write tests, run them, read
output, iterate. It's the only mode that can execute code."*

---

### Step 5 — Debug with `#file` (Inline Chat / Ask)

**Goal:** Show how to reference multiple files with `#file` to diagnose and fix
a crash in `order_processor.py`.

**5a. Reproduce the crash:**
```bash
python order_processor.py
# → NameError: name 'total' is not defined
```

**5b. Prompt (Ask or Inline Chat — reference both files):**
```
#file:order_processor.py crashes with NameError: name 'total' is not defined.
Also check #file:order_service.py for the apply_discount >= bug.
Find and fix all bugs in both files.
```

**What happens:** Copilot identifies:
- **order_processor.py line 27:** `total` should be `discounted + tax`
- **order_service.py `apply_discount`:** `>` should be `>=`, result needs `round(..., 2)`

Apply the fixes (inline or via Edit).

**5c. Verify:**
```bash
python order_processor.py   # prints the formatted report
python order_service.py     # ALL PASS
```

**Talking point:** *"The `#file` command is your cross-file debugger. Reference
as many files as you need — Copilot reads them all in context."*

---

### Step 6 — Review the Scripts (Ask Mode)

**Goal:** Use Ask mode for a code review of both completed scripts.

**Prompt (Ask):**
```
Review #file:order_service.py and #file:order_processor.py for:
- Code quality and readability
- Edge cases or potential bugs
- Security concerns (e.g., injection, type safety)
- Suggestions for improvement
```

**What happens:** Copilot provides a code review — might suggest input
validation, type hints, or error handling.

**Talking point:** *"Ask mode is your always-available code reviewer. Use it
before every PR to catch issues early."*

---

### Step 7 — Generate a Commit Message (Source Control + Copilot)

**Goal:** Show how Copilot writes a clear, conventional commit message from your
staged changes so you never stare at a blank commit box.

**7a. Stage your changes:**
```bash
git add order_service.py order_processor.py test_order_service.py
```

**7b. Generate the message:**

Open the **Source Control** view (`Ctrl+Shift+G`). In the commit message box,
click the ✨ **Generate Commit Message with Copilot** (sparkle) icon. Copilot
reads the staged diff and drafts a summary.

> **Tip:** You can also refine it in Copilot Chat:
> ```
> Write a Conventional Commits message for my staged changes:
> summarize the fixes to apply_discount and the NameError in
> order_processor.py, plus the new tests.
> ```

**What happens:** Copilot produces something like:

```
fix: correct discount boundary and order total calculation

- Use >= in apply_discount so orders of exactly 100.00 get the discount
- Round discounted amounts to 2 decimals
- Fix NameError in order_processor.py (total = discounted + tax)
- Add pytest coverage for order_service functions
```

Review, tweak if needed, then commit.

**Talking point:** *"Copilot turns your diff into a reviewable, conventional
commit message in one click — consistent history with zero effort."*

---

### Step 8 — Choosing the Right Tool (Recap)

**Goal:** Summarize which Copilot mode was used and why.

| Step | Task | Mode Used | Why This Mode |
|---|---|---|---|
| 1 | Brainstorm features | **Ask** | Read-only exploration, no edits |
| 2 | Plan implementation | **Plan** | Multi-step roadmap before coding |
| 3 | Complete functions | **Edit** | Write code to a spec, scoped to one file |
| 4 | Generate & run tests | **Agent** | Multi-step: create file → run → iterate |
| 5 | Debug cross-file crash | **Inline / Ask + `#file`** | Reference multiple files, targeted fix |
| 6 | Code review | **Ask** | Read-only analysis, no edits |
| 7 | Generate commit message | **Source Control + Copilot** | Summarize staged diff into a clean commit |
| 8 | — | **All** | Right tool = smallest surface that does the job |

**Key takeaway:** *"Start with Ask to understand, Plan to organize, Edit/Ghost
to write, Agent to orchestrate, and `#file` to debug across boundaries.
Always pick the **smallest tool** that gets the job done."*

---

## Quick-Run Commands

```bash
# Baseline (before fixes):
python order_service.py        # some checks FAIL
python order_processor.py      # crashes with NameError

# After all fixes:
python order_service.py        # ALL PASS
python order_processor.py      # prints formatted report
python -m pytest -v            # all tests green
```

---

## Expected Final Output

**`python order_service.py`:**
```
order_total     : PASS
apply_discount  : PASS
summary         : PASS
calculate_tax   : PASS
generate_invoice: PASS

ALL PASS
```

**`python order_processor.py`:**
```
==================================================
CUSTOMER         SUBTOTAL   DISCOUNT      TAX      TOTAL
--------------------------------------------------
Alice            $ 115.00    -$ 11.50  $  8.28 $  111.78
Bob              $ 280.00    -$ 28.00  $ 20.16 $  272.16
Carol            $ 100.00    -$ 10.00  $  7.20 $   97.20
--------------------------------------------------
GRAND TOTAL                                    $  481.14
==================================================
```
