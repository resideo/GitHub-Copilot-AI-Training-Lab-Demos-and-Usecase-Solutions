---
title: Demo 1 — Copilot overview
layout: default
parent: Demos
grand_parent: Fundamental
nav_order: 1
permalink: /demos/demo-1.html
description: "Complete five stubbed Python functions with Code completions, then print a formatted receipt."
---

# 🐍 Demo 1 — GitHub Copilot Overview
{: .no_toc }

**Quick info**

- **Language:** Python
- **File:** `orders.py`
- **Mode:** Code completions (no chat panel needed)

The goal is to complete the 5 stubbed functions in `orders.py` using **Code completions** (inline
suggestions). Once all five are filled in, run the file and Copilot's code prints a formatted
store receipt.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Setup

| Setting | Value |
|---|---|
| **Mode** | **Code completions** (inline suggestions; no chat panel) |
| **Model** | Completion model (auto) |
| **File** | `orders.py` |
| **Prep** | Create `orders.py` from the code below. Make sure Copilot is signed in. Run it once — everything is `FAIL` until you fill the functions in. |

## Starter code — create `orders.py`

Copy this into a new file named `orders.py`:

```python
# ============================================================
#  Demo 1 - GitHub Copilot Overview
#  Feature: Code completions - "AI assistance in action"
# ============================================================
#
#  HOW TO PRESENT:
#    1. Delete the `pass` under a function.
#    2. Type the first token (e.g. `ret`) and PAUSE - gray "ghost text" (inline suggestion) appears.
#    3. Press Tab to accept, Alt+] / Alt+[ to cycle, Esc to dismiss.

STORE_NAME = "InfoMagnus Tech Store"

ITEMS = [
    {"name": "Mechanical Keyboard", "price": 45.0, "qty": 2},
    {"name": "Wireless Mouse", "price": 25.0, "qty": 1},
    {"name": "4K Monitor", "price": 210.0, "qty": 1},
    {"name": "USB-C Cable", "price": 12.5, "qty": 4},
]

# Orders at or above this subtotal earn a loyalty discount.
DISCOUNT_THRESHOLD = 300.0
DISCOUNT_RATE = 10   # percent
TAX_RATE = 8         # percent


# Return the subtotal: sum of price * qty for every item in the list
def order_total(items):
    pass  # code completion -> return sum(i["price"] * i["qty"] for i in items)


# Return the discount amount (not the new total): DISCOUNT_RATE% of subtotal
# when subtotal >= DISCOUNT_THRESHOLD, otherwise 0.0
def loyalty_discount(subtotal):
    pass  # code completion completes this


# Return amount plus tax, where rate is a percentage (e.g. 8 means 8%)
def apply_tax(amount, rate):
    pass  # code completion completes this


# Return a receipt line like "Mechanical Keyboard   x2   $90.00" for one item dict
def receipt_line(item):
    pass  # code completion completes this


# Return the final amount the customer pays:
#   subtotal minus loyalty_discount, then apply_tax at TAX_RATE, rounded to 2 decimals
def grand_total(items):
    pass  # code completion completes this


def print_receipt(items):
    """Pretty, boxed receipt - assembled from the functions above."""
    width = 44
    line = "+" + "-" * width + "+"
    print(line)
    print("|" + STORE_NAME.center(width) + "|")
    print(line)
    for item in items:
        print("| " + receipt_line(item).ljust(width - 1) + "|")
    print(line)

    subtotal = order_total(items)
    discount = loyalty_discount(subtotal)
    taxed = apply_tax(subtotal - discount, TAX_RATE)
    tax_amount = taxed - (subtotal - discount)
    print("| " + f"Subtotal:  ${subtotal:>28.2f}".ljust(width - 1) + "|")
    print("| " + f"Discount: -${discount:>28.2f}".ljust(width - 1) + "|")
    print("| " + f"Tax ({TAX_RATE}%):  ${tax_amount:>28.2f}".ljust(width - 1) + "|")
    print("| " + f"TOTAL:     ${grand_total(items):>28.2f}".ljust(width - 1) + "|")
    print(line)
    print("|" + "Thank you for shopping with us!".center(width) + "|")
    print(line)


def _check(name, actual, expected):
    ok = actual == expected
    print(f"{name:<16}: {'PASS' if ok else f'FAIL (got {actual!r}, want {expected!r})'}")
    return ok


if __name__ == "__main__":
    print("=== Self-checks ===")
    sub = order_total(ITEMS)
    checks = [
        _check("order_total", sub, 375.0),
        _check("loyalty_discount", loyalty_discount(375.0), 37.5),
        _check("apply_tax", apply_tax(100.0, 8), 108.0),
        _check("receipt_line", receipt_line(ITEMS[0]), "Mechanical Keyboard   x2   $90.00"),
        _check("grand_total", grand_total(ITEMS), 364.5),
    ]

    if all(checks):
        print("\nALL PASS - here's the receipt:\n")
        print_receipt(ITEMS)
    else:
        print("\nSOME FAILED - finish the functions with code completions, then re-run.")
```

---

## Prompts to type

For each function: **delete the `pass` line**, put the cursor on the empty indented line, then type
the seed below and **pause** (don't keep typing). Copilot shows gray **ghost text** (the inline suggestion), and pressing **Tab** accepts it.

<table class="demo-prompts-table">
    <thead>
        <tr>
            <th>Function</th>
            <th>Type this seed (and pause)</th>
            <th>Code completion should produce</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td><code>order_total</code></td>
            <td><code>    ret</code></td>
            <td><code>return sum(i["price"] * i["qty"] for i in items)</code></td>
        </tr>
        <tr>
            <td><code>loyalty_discount</code></td>
            <td><code>    ret</code></td>
            <td><code>return subtotal * DISCOUNT_RATE / 100 if subtotal &gt;= DISCOUNT_THRESHOLD else 0.0</code></td>
        </tr>
        <tr>
            <td><code>apply_tax</code></td>
            <td><code>    ret</code></td>
            <td><code>return amount + amount * rate / 100</code></td>
        </tr>
        <tr>
            <td><code>receipt_line</code></td>
            <td><code>    ret</code></td>
            <td><code>return f"{item['name']}   x{item['qty']}   ${item['price'] * item['qty']:.2f}"</code></td>
        </tr>
        <tr>
            <td><code>grand_total</code></td>
            <td><code>    sub</code></td>
            <td>subtotal − discount, then <code>apply_tax(..., TAX_RATE)</code></td>
        </tr>
    </tbody>
</table>

{: .warning }
> **`receipt_line` — exact spacing matters.** Its self-check compares against a fixed string, so use
> **three literal spaces** as separators — not `:<20` column padding. Target:
> `f"{item['name']}   x{item['qty']}   ${item['price'] * item['qty']:.2f}"`.
> Padding to a width (e.g. `:<20`) makes the gap depend on the name length and FAILs for
> `Mechanical Keyboard`.

---

## Run

```bash
python orders.py
```

Expected output:

```text
=== Self-checks ===
order_total     : PASS
loyalty_discount: PASS
apply_tax       : PASS
receipt_line    : PASS
grand_total     : PASS

ALL PASS - here's the receipt:

+--------------------------------------------+
|           InfoMagnus Tech Store            |
+--------------------------------------------+
| Mechanical Keyboard   x2   $90.00          |
| Wireless Mouse   x1   $25.00               |
| 4K Monitor   x1   $210.00                  |
| USB-C Cable   x4   $50.00                  |
+--------------------------------------------+
| Subtotal:  $                      375.00   |
| Discount: -$                       37.50   |
| Tax (8%):  $                       27.00   |
| TOTAL:     $                      364.50   |
+--------------------------------------------+
|      Thank you for shopping with us!       |
+--------------------------------------------+
```

---

## Solution (completed functions)

<details markdown="block">
<summary>Click to reveal the five completed function bodies</summary>

```python
def order_total(items):
    return sum(i["price"] * i["qty"] for i in items)


def loyalty_discount(subtotal):
    return subtotal * DISCOUNT_RATE / 100 if subtotal >= DISCOUNT_THRESHOLD else 0.0


def apply_tax(amount, rate):
    return amount + amount * rate / 100


def receipt_line(item):
    return f"{item['name']}   x{item['qty']}   ${item['price'] * item['qty']:.2f}"


def grand_total(items):
    subtotal = order_total(items)
    discount = loyalty_discount(subtotal)
    return round(apply_tax(subtotal - discount, TAX_RATE), 2)
```

</details>
