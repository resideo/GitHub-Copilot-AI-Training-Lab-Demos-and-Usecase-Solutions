---
title: Demo 4 - Shot Prompting & Chain-of-Thought
layout: default
parent: Demos
grand_parent: Prompt Engineering
nav_order: 4
permalink: /prompt-engg/demos/demo-4.html
description: "Zero-shot, few-shot, and plan-first prompting patterns across C++ and Java."
---

# 🎯 Demo 4 — Shot Prompting & Chain-of-Thought
{: .no_toc }

**Deck checkpoint:** Techniques demo (slide 64 — *"Zero-shot, one-shot, and few-shot on the same task"*)

**Languages:** **C++** & **Java**

**Duration:** ~10–12 minutes · **Prompts:** 7

**Covers:** Zero-shot · one-shot · few-shot · pattern locking · chain-of-thought (plan-first)

{: .highlight }
> **No clone required.** Copy the three files below into a folder named
> `demo-4-shot-and-chain-of-thought`, then follow the steps.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Files

| File | Purpose |
|---|---|
| `alert-format-examples.md` | The few-shot pattern reference used in Part A |
| `string_utils.cpp` | `to_kebab_case` — implemented from examples in Part B |
| `LruCache.java` | Design-heavy problem solved plan-first in Part C |

### `alert-format-examples.md`

````markdown
# Alert Format Examples

Use these as one-shot and few-shot examples.

## Example 1

Input reading:

```json
{"zoneId":"Basement","temperatureF":57.0,"humidity":60,"occupied":false}
```

Output line:

```text
CRITICAL | Basement | FREEZE_RISK
```

## Example 2

Input reading:

```json
{"zoneId":"LivingRoom","temperatureF":81.5,"humidity":45,"occupied":true}
```

Output line:

```text
CRITICAL | LivingRoom | HIGH_HEAT
```

## Example 3

Input reading:

```json
{"zoneId":"Lab","temperatureF":74.0,"humidity":72,"occupied":true}
```

Output line:

```text
INFO | Lab | AIR_QUALITY_CHECK
```

## Negative example

Input reading:

```json
{"zoneId":"Office","temperatureF":76.0,"humidity":48,"occupied":true}
```

Output lines:

```text
<no alerts>
```
````

### `string_utils.cpp`

```cpp
// DEMO 4 — Few-Shot / Example-Driven Prompting  (Language: C++)
// -------------------------------------------------------------
// Goal: Give Copilot a few input/output EXAMPLES so it infers the rule,
//       then let it complete the implementation.
//
// HOW TO RUN THE DEMO
// 1. Keep the example comments above the function.
// 2. Place the cursor in the function body and accept Copilot's suggestion.
// 3. Point out that no rule was written in prose — only examples.
//
// See demo.md -> "Demo 4" for the exact prompts.

#include <iostream>
#include <string>
#include <cctype>

// Examples (few-shot):
//   to_kebab_case("HelloWorld")      => "hello-world"
//   to_kebab_case("myVariableName")  => "my-variable-name"
//   to_kebab_case("ParseHTMLString") => "parse-html-string"
//   to_kebab_case("already-kebab")   => "already-kebab"
std::string to_kebab_case(const std::string& input) {
	// TODO (live demo): let Copilot infer the rule from the examples above.
	return input;
}

int main() {
	std::cout << to_kebab_case("ParseHTMLString") << "\n";
	return 0;
}
```

### `LruCache.java`

```java
/*
 * DEMO 6 — Chain-of-Thought ("outline, then implement")  (Language: Java)
 * -----------------------------------------------------------------------
 * Goal: For a non-trivial algorithm, ask Copilot to OUTLINE the approach in
 * comments first, then implement each step. This produces better logic than a
 * single "write an LRU cache" prompt.
 *
 * HOW TO RUN THE DEMO
 * 1. Use the Chat prompt in demo.md -> "Demo 6" to generate the plan as comments.
 * 2. Then let Copilot fill in each method from the plan.
 * 3. Discuss why the O(1) get/put design (HashMap + doubly linked list) emerges.
 *
 * See demo.md -> "Demo 6" for the exact prompts.
 */

public class LruCache<K, V> {

	private final int capacity;

	public LruCache(int capacity) {
		this.capacity = capacity;
		// TODO (live demo): outline the data structures first, then implement.
	}

	/** Return the value for key, or null if absent. Marks the key as most-recently-used. */
	public V get(K key) {
		// TODO (live demo)
		return null;
	}

	/** Insert/update key. Evicts the least-recently-used entry when over capacity. */
	public void put(K key, V value) {
		// TODO (live demo)
	}
}
```

## Setup

| Setting | Value |
|---|---|
| **Surface** | Ask mode in VS Code (Part A can also run in chat on GitHub.com) |
| **Prep** | Copy all three files from this page and open them in the editor |

---

## Part A — Zero → one → few-shot on the same task (≈4–5 min)

Same job every time: turn a sensor reading into alert output lines. Watch the format tighten as you
add examples.

**Prompt 1 — Zero-shot (no examples):**
```
A new reading arrived: {"zoneId":"ServerRoom","temperatureF":83.0,"humidity":77,"occupied":true}. Return the alert output lines for it.
```
Often the right idea, but the separator, severity labels, or multi-alert behavior may drift.

**Prompt 2 — One-shot (one example):**
```
Use this formatting example.

Input: {"zoneId":"Basement","temperatureF":57.0,"humidity":60,"occupied":false}
Output: CRITICAL | Basement | FREEZE_RISK

Now return the alert output lines for: {"zoneId":"LivingRoom","temperatureF":81.5,"humidity":45,"occupied":true}
Return only the output lines.
```
One example usually tightens the format immediately.

**Prompt 3 — Few-shot (a pattern file):** attach `alert-format-examples.md`:
```
Using #alert-format-examples.md as the pattern, return only the alert output lines for these readings:

- {"zoneId":"ServerRoom","temperatureF":83.0,"humidity":77,"occupied":true}
- {"zoneId":"Atrium","temperatureF":59.0,"humidity":68,"occupied":true}
- {"zoneId":"Lab","temperatureF":74.0,"humidity":72,"occupied":true}
```
Expected pattern:
```text
CRITICAL | ServerRoom | HIGH_HEAT
INFO | ServerRoom | AIR_QUALITY_CHECK
CRITICAL | Atrium | FREEZE_RISK
INFO | Lab | AIR_QUALITY_CHECK
```

**Talking point:** few-shot isn't "more text" — it's **locking the model onto the exact pattern** you want.

---

## Part B — Few-shot inside real code (≈2–3 min)

Open `string_utils.cpp`. Four input/output examples sit in the comment above `to_kebab_case` — no
rule is written in prose.

**Prompt 4 — inline:** put the cursor in the function body and accept the suggestion, or in Ask:
```
Implement to_kebab_case so it satisfies all four example cases in the comment, including consecutive uppercase runs like "HTML".
```

**Prompt 5 — probe the tricky case:**
```
Show which of the four example cases would fail with a naive implementation, then confirm your version passes all four.
```

**Talking point:** examples beat prose for pattern-heavy utilities, parsers, and formatters — the
model **infers the transformation** from the pairs.

---

## Part C — Chain-of-thought: plan first, then build (≈3–4 min)

Open `LruCache.java`. Hard problem → ask for the approach before any code.

**Prompt 6 — Step 1 · plan only (no code yet):**
```
Before writing any code, outline as comments the design for an O(1) get and put LRU cache: which data structures you'll use and why. Do not implement yet.
```
This surfaces the **HashMap + doubly linked list** design instead of a naive list scan.

**Prompt 7 — Step 2 · implement from the plan:**
```
Now implement the LruCache class based on that plan, filling in the constructor, get, and put methods.
```

**Talking point:** for algorithmic or design-heavy work, separating **reasoning** from **generation**
produces better logic — and it's easy to review the plan before you commit to code.

---

## Verify

```bash
g++ -std=c++17 string_utils.cpp -o string_utils && ./string_utils   # bash
g++ -std=c++17 .\string_utils.cpp -o string_utils.exe; .\string_utils.exe   # PowerShell
javac LruCache.java
```

## Takeaway

- **Zero → one → few-shot** is a dial: add examples until the format is locked.
- Examples in comments are few-shot prompts embedded in your code.
- **Chain-of-thought** (plan first) wins on hard, design-heavy problems.
