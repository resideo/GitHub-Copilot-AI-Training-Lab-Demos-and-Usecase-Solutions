---
title: Demo 3 - Persona & Context Control
layout: default
parent: Demos
grand_parent: Prompt Engineering
nav_order: 3
permalink: /prompt-engg/demos/demo-3.html
description: "Use persona prompting and context variables to improve grounded output."
---

# 🛡️ Demo 3 — Persona & Context Control
{: .no_toc }

**Deck checkpoint:** Foundations / Best Practices (pairs with slide 14 and "provide references")

**Language:** **C#**

**Duration:** ~10–12 minutes · **Prompts:** 7

**Covers:** Persona / role prompting · security review depth · the context ladder (`#file` → `#selection` → `#problems` / `#terminal`)

> The code is **intentionally vulnerable / buggy** for training. Do not treat it as reference code.

{: .highlight }
> **No clone required.** Copy the two files below into a folder named
> `demo-3-persona-and-context`, then follow the steps.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Files

| File | Purpose |
|---|---|
| `UserController.cs` | Intentional SQL injection, plaintext password, PII logging |
| `InvoiceService.cs` | Intentional bug in `CalculateAverage` (division on empty list) |

### `UserController.cs`

```csharp
// DEMO 3 — Persona / Role Prompting for Security Review  (Language: C#)
// ---------------------------------------------------------------------
// Goal: Show how giving Copilot Chat a PERSONA produces a sharper review.
//
// !!! THIS CODE IS INTENTIONALLY VULNERABLE FOR TRAINING PURPOSES !!!
// It contains: SQL injection, plaintext password handling, and PII logging.
// Use Copilot Chat to find and fix these issues live.
//
// HOW TO RUN THE DEMO
// 1. Select the whole class.
// 2. In Chat, first try a plain prompt: "check this code".
// 3. Then try the persona prompt (see demo.md -> "Demo 3") and compare depth.
//
// See demo.md -> "Demo 3" for the exact prompts.

using System;
using System.Data.SqlClient;

public class UserController
{
    private readonly string _connectionString;

    public UserController(string connectionString)
    {
        _connectionString = connectionString;
    }

    // Intentionally insecure: SQL injection + PII logging + plaintext password.
    public bool Login(string username, string password)
    {
        Console.WriteLine($"Login attempt for {username} with password {password}");

        using var conn = new SqlConnection(_connectionString);
        conn.Open();

        var query = "SELECT COUNT(*) FROM Users WHERE Username = '" + username +
                    "' AND Password = '" + password + "'";

        using var cmd = new SqlCommand(query, conn);
        var count = (int)cmd.ExecuteScalar();

        return count > 0;
    }
}
```

### `InvoiceService.cs`

```csharp
// DEMO 11 — Context Control  (Language: C#)
// -----------------------------------------
// Goal: Show how the ANSWER quality changes as you add context to Copilot Chat:
//   1. No context        -> generic result
//   2. #file             -> targeted to this file
//   3. #file + #selection -> precise, focused on the selected method
//   4. #problem / #terminalLastCommand / #terminalSelection -> error-driven fix
//
// This file contains an intentional bug so the #problem / #terminal steps have
// something real to react to (see CalculateAverage: divides by Count on an empty
// list -> DivideByZeroException).
//
// HOW TO RUN THE DEMO
// 1. Build/run so the error appears in the Problems panel and terminal.
// 2. Walk through the four context levels using this folder's demo.md.

using System.Collections.Generic;

public class InvoiceService
{
    // Intentional bug for the error-driven step: no guard for an empty list.
    public double CalculateAverage(List<double> amounts)
    {
        double sum = 0;
        foreach (var a in amounts)
        {
            sum += a;
        }
        return sum / amounts.Count; // DivideByZeroException when amounts is empty
    }

    public double CalculateTotal(List<double> amounts)
    {
        double sum = 0;
        foreach (var a in amounts)
        {
            sum += a;
        }
        return sum;
    }
}
```

## Setup

| Setting | Value |
|---|---|
| **Mode** | Ask / Chat |
| **Org rules to reinforce** | Never log PII · always use the internal authentication library |
| **Prep** | Copy both files from this page and open them in the editor |

---

## Part A — Persona turns a shrug into a security review (≈4–5 min)

Open `UserController.cs`.

**Prompt 1 — weak (run first):**
```
check this code
```
You'll get a shallow, unstructured reply.

**Prompt 2 — persona (run second, compare depth):**
```
You are a senior application security engineer. Review the selected C# code for OWASP Top 10 vulnerabilities. List each issue, its severity, and the exact fix. Focus on SQL injection, credential handling, and logging of sensitive data.
```
**Talking point:** the persona forces **structure** — issue · severity · fix — and pulls the model
toward the OWASP framing you want.

**Prompt 3 — follow-up fix:**
```
Rewrite the Login method to use parameterized queries, stop logging the password, and never log PII. Assume passwords are stored as salted hashes.
```

**Prompt 4 — tie it to org policy:**
```
Now make it use our internal authentication library instead of hand-rolled checks, and confirm no PII (email, password, card data) is written to any log statement.
```

---

## Part B — The context ladder: four levels of grounding (≈5–6 min)

Open `InvoiceService.cs`. Run these in order and compare how targeted each answer is.

**Prompt 5 — Level 1 · No context → generic:**
```
    How do I calculate an average safely?
```
A textbook answer, not tied to your code.

**Prompt 6 — Level 2 · `#file` → targeted:**
```
#file:InvoiceService.cs Are there any bugs in this file?
```
Now Copilot inspects the real file and can spot the empty-list division.

**Prompt 7 — Level 3 · `#file` + `#selection` → precise:** select the `CalculateAverage` method, then:
```
#file:InvoiceService.cs #selection Fix the bug in the selected method only and keep the public signature unchanged.
```

**Level 4 · Error-driven with `#problems` / `#terminal` → grounded in the real failure.** Run the code
so it throws, then use whichever variable your VS Code build exposes:
```
#problems Explain and fix the error shown in the Problems panel.
```
```
#terminalLastCommand Why did the last run fail and how do I fix it?
```

| Level | Context attached | Result | Why |
|---|---|---|---|
| 1 | None | Generic | Model can't see your code |
| 2 | `#file` | Targeted | Sees the whole file's logic |
| 3 | `#file` + `#selection` | Precise | Focused on the exact method |
| 4 | `#problems` / `#terminal*` | Error-driven | Grounded in the real stack trace |

---

## Talking points

- Persona changes **tone and structure**; context changes **accuracy and scope**.
- More *relevant* context = less guessing — but don't dump *irrelevant* context.
- `#selection` stops Copilot from rewriting unrelated code.
- `#problems` / `#terminal*` turn a real exception into a targeted fix.

> Tip: exact context-variable names vary slightly by VS Code version — type `#` in Chat to see the
> current picker (file, selection, problems, terminalLastCommand, terminalSelection).

## Takeaway

Give the model a **role** to shape the answer and the **right context** to ground it. Together they
convert "check this code" into a precise, policy-aware fix.
