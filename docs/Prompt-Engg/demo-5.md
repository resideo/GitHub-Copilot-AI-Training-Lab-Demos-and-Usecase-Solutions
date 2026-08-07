---
title: Demo 5 - Iterative Building, Tests & Refactoring
layout: default
parent: Demos
grand_parent: Prompt Engineering
nav_order: 5
permalink: /prompt-engg/demos/demo-5.html
description: "Build in small steps, generate tests, and refactor safely with Copilot."
---

# 🔁 Demo 5 — Iterative Building, Tests & Refactoring
{: .no_toc }

**Deck checkpoint:** Best Practices (pairs with "split complex tasks" + "test changes systematically")

**Languages:** **Python** · **C#** · **C++**

**Duration:** ~11–13 minutes · **Prompts:** 8

**Covers:** Prompting as a dialogue · small verifiable steps · `/tests` generation · refactoring & language translation

{: .highlight }
> **No clone required.** Copy the three files below into a folder named
> `demo-5-iterative-tests-refactor`, then follow the steps.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Files

| File | Purpose |
|---|---|
| `user_api.py` | FastAPI app — built up feature-by-feature in Part A |
| `Calculator.cs` | Class under test — `/tests` then edge-case refinement in Part B |
| `legacy_callbacks.cpp` | Callback-hell code refactored and translated in Part C |

### `user_api.py`

```python
"""
DEMO 5 — Iterative Refinement in Copilot Chat  (Language: Python)
----------------------------------------------------------------
Goal: Show that prompting is a DIALOGUE. Build a feature in steps, each Chat
turn adding a requirement, instead of one giant prompt.

HOW TO RUN THE DEMO
1. Start from this near-empty FastAPI app.
2. In Chat, run the prompts from demo.md -> "Demo 5" one at a time:
   step 1: create the endpoint
   step 2: add validation
   step 3: add error handling
   step 4: add a unit test
3. Emphasise reviewing each diff before accepting the next step.

See demo.md -> "Demo 5" for the exact prompts.
"""

from fastapi import FastAPI

app = FastAPI()

# In-memory store for the demo (replace with a real DB in production).
users: dict[int, dict] = {}


# TODO (live demo, step 1): create POST /users to add a user.
# Then refine it turn-by-turn using the Chat prompts in demo.md.
```

### `Calculator.cs`

```csharp
// DEMO 7 — Test Generation with /tests  (Language: C#)
// ----------------------------------------------------
// Goal: Select finished code, run /tests, then REFINE the prompt to add edge
// cases (division by zero, negatives, overflow).
//
// HOW TO RUN THE DEMO
// 1. Select the Calculator class.
// 2. In Chat run: /tests
// 3. Then refine with the follow-up prompt in demo.md -> "Demo 7".
//
// See demo.md -> "Demo 7" for the exact prompts.

using System;

public class Calculator
{
	public int Add(int a, int b) => a + b;

	public int Subtract(int a, int b) => a - b;

	public int Multiply(int a, int b) => a * b;

	// Throws DivideByZeroException when b == 0.
	public int Divide(int a, int b)
	{
		if (b == 0)
		{
			throw new DivideByZeroException("Cannot divide by zero.");
		}
		return a / b;
	}
}
```

### `legacy_callbacks.cpp`

```cpp
// DEMO 8 — Refactoring & Translation  (Language: C++)
// ---------------------------------------------------
// Goal: Ask Copilot to refactor callback-style code into a cleaner design and,
// as a bonus, translate the logic to another language.
//
// HOW TO RUN THE DEMO
// 1. Select the fetchUser function.
// 2. Use the refactor prompt in demo.md -> "Demo 8".
// 3. Bonus: ask Copilot to translate it to Python or C#.
//
// See demo.md -> "Demo 8" for the exact prompts.

#include <functional>
#include <string>
#include <iostream>

struct User {
	int id;
	std::string name;
};

// Callback-heavy style ("callback hell"). Ripe for refactoring.
void fetchUser(int id,
			   std::function<void(const User&)> onSuccess,
			   std::function<void(const std::string&)> onError) {
	if (id <= 0) {
		onError("Invalid id");
		return;
	}
	User u{id, "User" + std::to_string(id)};
	onSuccess(u);
}

int main() {
	fetchUser(
		42,
		[](const User& u) { std::cout << "Got " << u.name << "\n"; },
		[](const std::string& err) { std::cout << "Error: " << err << "\n"; });
	return 0;
}
```

## Setup

| Setting | Value |
|---|---|
| **Mode** | Chat |
| **Golden rule** | Review **each diff before accepting** — that's the lesson of Part A |
| **Prep** | Copy all three files from this page and open them in the editor |

---

## Part A — Build a feature as a dialogue, not one giant prompt (≈5 min)

Open `user_api.py`. Run these one at a time, reviewing the diff after each turn.

**Prompt 1 — endpoint:**
```
Create a POST /users endpoint on the FastAPI app in this file. It accepts a JSON body with name and email and stores the user in the users dict with an auto-incremented id. Return the created user.
```

**Prompt 2 — validation:**
```
Add input validation using a Pydantic model: name is required and non-empty, email must be a valid email address.
```

**Prompt 3 — error handling:**
```
Add error handling: return 409 if the email already exists, and 422 for invalid input.
```

**Prompt 4 — tests:**
```
Write pytest tests for the endpoint covering success, duplicate email, and invalid input.
```

**Talking point:** four small prompts you can verify beat one giant prompt you have to debug. Each
turn is reviewable, and the model keeps the growing context.

---

## Part B — Generate tests, then refine for edge cases (≈3–4 min)

Open `Calculator.cs`. First pass = happy path; refinement drives the bug-catching cases.

**Prompt 5 — generate:** select the `Calculator` class, then:
```
/tests
```

**Prompt 6 — refine to force the hard cases:**
```
Add edge-case tests: division by zero should throw DivideByZeroException, plus tests for negative numbers and integer overflow on Add and Multiply. Use xUnit and Theory/InlineData where appropriate.
```

**Talking point:** `/tests` gets you a running suite fast; the *refinement* prompt is where you get
the coverage that actually catches bugs.

---

## Part C — Refactor, then translate across languages (≈3–4 min)

Open `legacy_callbacks.cpp`. Copilot excels at mechanical transformations.

**Prompt 7 — refactor away callback hell:**
```
Refactor fetchUser to avoid callback hell. Return a std::optional<User> or throw on error instead of using onSuccess/onError callbacks, and update main accordingly.
```

**Prompt 8 — translate (bonus):**
```
Translate the refactored fetchUser logic to idiomatic Python, keeping the same behavior and error handling.
```

**Talking point:** modernizing legacy patterns and cross-language ports are high-confidence Copilot
tasks — the behavior is fixed, only the shape changes.

---

## Verify

```bash
python user_api.py
g++ -std=c++17 legacy_callbacks.cpp -o legacy && ./legacy             # bash
g++ -std=c++17 .\legacy_callbacks.cpp -o legacy.exe; .\legacy.exe    # PowerShell
```

## Takeaway

- **Build in small, reviewable steps** — prompting is a conversation.
- **Generate then refine** tests: happy path first, edge cases second.
- Refactors and translations are **mechanical wins** — let Copilot do the heavy lifting, you verify.
