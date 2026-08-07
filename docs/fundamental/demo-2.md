---
title: Demo 2 — Getting started in your IDE
layout: default
parent: Demos
grand_parent: Fundamental
nav_order: 2
permalink: /demos/demo-2.html
description: "Give every Copilot surface a real job on a C++ tip calculator: code completions, inline chat, NES, Ask, Plan, and / commands."
---

# 💻 Demo 2 — Getting Started in Your IDE
{: .no_toc }

**Quick info**

- **Language:** C++
- **File:** `tip_calculator.cpp`
- **Build:** `g++ -std=c++17 tip_calculator.cpp -o tip && ./tip`

You're building a small **tip calculator / bill summary** and using every Copilot surface:
code completions, inline chat (**Ctrl+I**), Next Edit Suggestions, quick chat, Ask, Plan, and `/` commands.
Everything is stubbed or buggy so the file runs but **FAILs** until you demo.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Setup

| Setting | Value |
|---|---|
| **Modes shown** | Code completions · **Inline chat** (Ctrl+I) · **NES** · **Quick chat** (Ctrl+Shift+I) · **Ask** · **Plan** |
| **Model** | GPT-5.4 for chat; completion model (auto) for code completions & NES |
| **File** | `tip_calculator.cpp` |
| **Prep** | Enable NES: Settings → `github.copilot.nextEditSuggestions.enabled` → **On**. Have `g++` ready. Run once — several checks **FAIL** until you demo. |

## Starter code — create `tip_calculator.cpp`

```cpp
// ============================================================
//  Demo 2 - Getting Started in Your IDE
//  Build & run: g++ -std=c++17 tip_calculator.cpp -o tip && ./tip
// ============================================================

#include <iostream>
#include <iomanip>
#include <sstream>
#include <string>

// ---- Code completions target -------------------------------
// Return the tip: amount * percent / 100
double calculate_tip(double amount, double percent) {
    return 0.0; // let code completions complete this
}

// ---- Inline chat target (Ctrl+I) ---------------------------
// Split the bill (amount + its tip) evenly between `people`.
// Right now it divides by zero when people == 0. Harden it with inline chat.
double split_bill(double amount, double percent, int people) {
    double tip = calculate_tip(amount, percent);
    return (amount + tip) / people;
}

// ---- Inline chat target (Ctrl+I) - harder, multi-branch ----
// Apply a loyalty discount to `amount` based on `loyalty_years`:
//   0 years -> 0% off, 1-2 -> 5% off, 3-4 -> 10% off, 5+ -> 15% off.
// Return the amount AFTER the discount. Stub returns it unchanged.
double apply_discount(double amount, int loyalty_years) {
    return amount; // build the tiered discount with inline chat (Ctrl+I)
}

// ---- / commands target: /fix -------------------------------
// A service fee that is `percent` of the amount.
// BUG (fix with /fix): it divides by 100 twice, so the fee is 100x too small.
double service_fee(double amount, double percent) {
    return amount * percent / 100 / 100;
}

// ---- / commands target: /doc -------------------------------
// (Undocumented on purpose - use /doc to add a doc comment here.)
std::string format_currency(double value) {
    std::ostringstream out;
    out << "$" << std::fixed << std::setprecision(2) << value;
    return out.str();
}

// ---- Ask / #symbol / /tests target -------------------------
double grand_total(double amount, double percent, int loyalty_years) {
    double discounted = apply_discount(amount, loyalty_years);
    double tip        = calculate_tip(discounted, percent);
    double fee        = service_fee(discounted, 5);
    return discounted + tip + fee;
}

int main() {
    bool c1 = calculate_tip(200, 10) == 20.0;
    bool c2 = split_bill(200, 10, 4) == 55.0;

    // After the inline-chat edit, zero people should return 0 (not inf/nan).
    bool c3 = split_bill(200, 10, 0) == 0.0;

    // After /fix, service_fee(200, 5) should be 10.0 (5% of 200), not 0.1.
    bool c4 = service_fee(200, 5) == 10.0;

    bool c5 = format_currency(90.0) == "$90.00";

    // After the harder inline-chat edit, the loyalty tiers should match:
    //   0y -> 0% off, 1-2y -> 5%, 3-4y -> 10%, 5+y -> 15%.
    bool c6 = apply_discount(100, 0)  == 100.0
           && apply_discount(100, 2)  == 95.0
           && apply_discount(100, 4)  == 90.0
           && apply_discount(100, 10) == 85.0;

    // grand_total composes discount + tip + service fee (needs c1, c4, c6).
    bool c7 = grand_total(200, 10, 0) == 230.0;

    std::cout << "calculate_tip  : " << (c1 ? "PASS" : "FAIL") << "\n";
    std::cout << "split_bill     : " << (c2 ? "PASS" : "FAIL") << "\n";
    std::cout << "guard          : " << (c3 ? "PASS" : "FAIL (add the zero-people guard with Ctrl+I)") << "\n";
    std::cout << "service_fee    : " << (c4 ? "PASS" : "FAIL (fix the double /100 bug with /fix)") << "\n";
    std::cout << "format_currency: " << (c5 ? "PASS" : "FAIL") << "\n";
    std::cout << "apply_discount : " << (c6 ? "PASS" : "FAIL (build the tiered discount with Ctrl+I)") << "\n";
    std::cout << "grand_total    : " << (c7 ? "PASS" : "FAIL (finish discount + /fix first)") << "\n";

    if (c1 && c2 && c3 && c4 && c5 && c6 && c7) {
        double bill = 200.0, pct = 10.0;
        int people = 4;
        std::cout << "\n----- Bill Summary -----\n";
        std::cout << "Bill        : " << format_currency(bill) << "\n";
        std::cout << "Tip (" << pct << "%) : " << format_currency(calculate_tip(bill, pct)) << "\n";
        std::cout << "Service fee : " << format_currency(service_fee(bill, 5)) << "\n";
        std::cout << "Per person  : " << format_currency(split_bill(bill, pct, people)) << "\n";
        // NES demo (Step 3b): add a "Discount" line here, then Tab the predicted "Grand total" line.
        std::cout << "ALL PASS\n";
    } else {
        std::cout << "\nSOME FAILED - finish the steps with Copilot, then re-run.\n";
    }
    return 0;
}
```

---

## Step 1 — Code completions (`calculate_tip`)

Inside `calculate_tip`, delete `return 0.0; // let code completions complete this`, then type the seed and pause:

```cpp
    ret
```

Code completions complete it to `return amount * percent / 100;`. Press **Tab**.

## Step 2 — Inline chat, Ctrl+I

### 2a · Guard `split_bill`
Click inside `split_bill`, press **Ctrl+I**:
```text
Return 0 when people is zero or negative; otherwise split amount plus the tip evenly. Keep the signature the same.
```

### 2b · Implement `apply_discount` (tiered)
Click inside `apply_discount`, press **Ctrl+I**:
```text
Implement a loyalty discount on `amount` based on `loyalty_years`: 0 years = 0% off, 1-2 years = 5% off, 3-4 years = 10% off, 5 or more years = 15% off. Return the amount AFTER the discount. Use clean branching and keep the signature unchanged.
```

### 2c · Refactor a selection (bonus)
**Select** the body of `apply_discount`, press **Ctrl+I**:
```text
Refactor this to use a lookup table or early returns so new tiers are easy to add later. Do not change the behavior.
```

## Step 3 — Next Edit Suggestions

### 3a · A rename that ripples
On the **definition** of `calculate_tip`, change the name to `tip_amount`. As you type, NES lights up
every call site — inside `split_bill`, `grand_total`, and twice in `main`. Press **Tab** to accept each.

### 3b · Predict a repetitive edit
In `main`, inside the `----- Bill Summary -----` block, add one new discount line:
```cpp
        std::cout << "Discount    : " << format_currency(apply_discount(bill, 3)) << "\n";
```
NES predicts the **next** line (a `Grand total` line using `grand_total(...)`). Press **Tab** to accept.

## Step 4 — Quick chat (Ctrl+Shift+I)

```text
In this tip calculator, what edge cases could make grand_total return a wrong or surprising value? Think negative amounts, huge loyalty_years, and floating-point rounding on money. Answer briefly.
```

## Step 5 — Ask mode (read-only)

```text
Explain #selection step by step: what it computes, and how it handles zero or negative people.
```
```text
Trace grand_total(200, 10, 3) call by call through #tip_calculator.cpp. Show the intermediate value after apply_discount, calculate_tip, and service_fee, then the final number.
```

## Step 6 — Plan mode

```text
Plan the steps to add a "round the per-person amount up to the nearest dollar" option to this tip calculator. List the steps only — do not write code yet.
```

## Step 7 — `/` commands

### 7a · `/fix` the `service_fee` bug
Select the `service_fee` function → Chat → `/fix`:
```text
/fix service_fee returns a value 100x too small. It should be percent% of amount (e.g. 5% of 200 = 10.00). Fix it.
```

---

## Run

```bash
g++ -std=c++17 tip_calculator.cpp -o tip && ./tip
```

All seven checks should print `PASS`, then a bill summary followed by `ALL PASS`.

---

## Solution (completed / fixed functions)

<details markdown="block">
<summary>Click to reveal the completed C++ functions</summary>

```cpp
double calculate_tip(double amount, double percent) {
    return amount * percent / 100;
}

double split_bill(double amount, double percent, int people) {
    if (people <= 0) return 0.0;
    double tip = calculate_tip(amount, percent);
    return (amount + tip) / people;
}

double apply_discount(double amount, int loyalty_years) {
    double rate;
    if (loyalty_years <= 0)        rate = 0.0;
    else if (loyalty_years <= 2)   rate = 0.05;
    else if (loyalty_years <= 4)   rate = 0.10;
    else                           rate = 0.15;
    return amount * (1.0 - rate);
}

double service_fee(double amount, double percent) {
    return amount * percent / 100; // removed the extra / 100
}
```

</details>
