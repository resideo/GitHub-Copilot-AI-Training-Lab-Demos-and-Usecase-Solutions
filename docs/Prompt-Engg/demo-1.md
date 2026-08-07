---
title: Demo 1 - Specificity & Quality Escalation
layout: default
parent: Demos
grand_parent: Prompt Engineering
nav_order: 1
permalink: /prompt-engg/demos/demo-1.html
description: "Prompt specificity, quality escalation, and contract-driven coding in Java."
---

# ☕ Demo 1 — Prompt Specificity & Quality Escalation
{: .no_toc }

**Deck checkpoint:** Introduction demo (slide 10 — *"Weak prompt to strong prompt"*)

**Language:** **Java**

**Duration:** ~10–12 minutes · **Prompts:** 6

**Covers:** Weak vs strong prompts · the comment/Javadoc *is* the prompt · the four-level quality ladder

---

## Why this demo

The foundational "prompting is a skill" demo. Same model, same file — output quality is a function
of *prompt* quality. We prove it two ways in Java: a documented contract that writes its own code,
and a four-level escalation ladder where quality visibly climbs.

{: .highlight }
> **No clone required.** Copy the two files below into a folder named
> `demo-1-specificity-and-quality`, then follow the steps.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Files

| File | Purpose |
|---|---|
| `DateParser.java` | `parseIsoDate` has only a **Javadoc contract** — the body is a TODO |
| `OrderProcessor.java` | `calculateTotal` is a stub — implemented four ways via escalating prompts |

### `DateParser.java`

```java
/*
 * DEMO 2 — Comment-Driven Development  (Language: Java)
 * -----------------------------------------------------
 * Goal: Write a rich Javadoc describing intent, then let Copilot generate the body.
 *
 * HOW TO RUN THE DEMO
 * 1. Show the empty method with only the Javadoc.
 * 2. Place the cursor inside the method body and accept Copilot's suggestion.
 * 3. Point out how the documented contract (exceptions, formats) drives the code.
 *
 * See demo.md -> "Demo 2" for the exact prompts.
 */

import java.time.OffsetDateTime;

public class DateParser {

	/**
	 * Parse an ISO-8601 date-time string into an OffsetDateTime.
	 *
	 * <p>Supported examples:
	 * <ul>
	 *   <li>"2026-07-12T10:15:30+02:00"</li>
	 *   <li>"2026-07-12T10:15:30Z"</li>
	 * </ul>
	 *
	 * @param value the ISO-8601 string to parse; must not be null or blank
	 * @return the parsed OffsetDateTime
	 * @throws IllegalArgumentException if value is null, blank, or not valid ISO-8601
	 */
	public static OffsetDateTime parseIsoDate(String value) {
		// TODO (live demo): let Copilot implement this from the Javadoc above.
		return null;
	}

	public static void main(String[] args) {
		System.out.println(parseIsoDate("2026-07-12T10:15:30Z"));
	}
}
```

### `OrderProcessor.java`

```java
/*
 * DEMO 9 — Prompt Quality Escalation  (Language: Java)
 * ----------------------------------------------------
 * Goal: Show the SAME task solved with four escalating prompt levels and compare
 * the output quality:
 *   Level 1: Vague          -> low quality
 *   Level 2: Structured     -> medium quality
 *   Level 3: Role-based     -> high quality
 *   Level 4: Multi-step     -> production quality
 *
 * TASK USED FOR THE COMPARISON:
 *   Calculate the final price for an order (subtotal, discount tiers, tax).
 *
 * HOW TO RUN THE DEMO
 * 1. Run each prompt from demo.md -> "Demo 9" in Copilot Chat, in order.
 * 2. Keep each generated version visible so attendees see the quality climb.
 *
 * See demo.md in this folder for the exact prompts.
 */

import java.math.BigDecimal;
import java.util.List;

public class OrderProcessor {

	public record LineItem(String sku, BigDecimal unitPrice, int quantity) {}

	// TODO (live demo): implement calculateTotal by escalating the prompt
	// through the four levels described in this folder's demo.md.
	public BigDecimal calculateTotal(List<LineItem> items) {
		return BigDecimal.ZERO;
	}
}
```

## Setup

| Setting | Value |
|---|---|
| **Modes** | Ask + Inline Chat (Ctrl+I) |
| **Model** | Auto (or GPT-5.4) |
| **Prep** | Copy both files from this page into a local folder and open them in the editor group |

---

## Part A — The comment *is* the prompt (≈3 min)

Open `DateParser.java`. The Javadoc on `parseIsoDate` already documents `@param`, `@return`, and the
`@throws IllegalArgumentException` cases.

**Prompt 1 — let the contract drive it.** Place the cursor in the method body and accept the ghost
text, or in Inline Chat:
```
Implement parseIsoDate strictly according to its Javadoc contract, including the null/blank and invalid-format IllegalArgumentException cases.
```

**Talking point:** a good `@param` / `@return` / `@throws` block is documentation *and* prompt. Teach
the habit: **write the contract first, implementation second.**

**Prompt 2 — prove the contract held:**
```
List which Javadoc guarantees your implementation satisfies, and add one JUnit 5 test per @throws case.
```

---

## Part B — The four-level quality ladder (≈6–7 min)

Open `OrderProcessor.java`. Same task each time — implement `calculateTotal(List<LineItem> items)`.
Run all four prompts in order and **keep every result visible** so the room watches quality climb.

**Prompt 3 — Level 1 · Vague → low quality:**
```
calculate the order total
```
Ask the room what the model had to guess: money type, discounts, tax, rounding, edge cases.

**Prompt 4 — Level 2 · Structured → medium:**
```
Implement calculateTotal(List<LineItem> items). Sum unitPrice * quantity for each item to get the subtotal, then return the subtotal as a BigDecimal.
```

**Prompt 5 — Level 3 · Role-based → high:**
```
You are a senior Java engineer building billing software. Implement calculateTotal with correct BigDecimal money handling (no float), rounding to 2 decimal places with HALF_UP. Apply a 10% discount when the subtotal is >= 100 and 15% when >= 500, then add 8% tax on the discounted amount.
```

**Prompt 6 — Level 4 · Multi-step → production:**
```
1. First, list the edge cases calculateTotal must handle (empty list, null items, negative quantity, zero price).
2. Then implement it defensively, throwing IllegalArgumentException on invalid input.
3. Use BigDecimal throughout with HALF_UP rounding to 2 decimals.
4. Finally, generate JUnit 5 tests covering every edge case and each discount tier.
```

| Level | Style | Copilot produces | Quality |
|---|---|---|---|
| 1 | Vague | Guesses domain; likely `double`, no discounts/tax | Low |
| 2 | Structured | Correct subtotal, no discounts/tax/rounding | Medium |
| 3 | Role-based | Proper `BigDecimal`, discount tiers, tax, rounding | High |
| 4 | Multi-step | Above + input validation + full test suite | Production |

Each level adds exactly one lever: **specificity → role → constraints → process.**

---

## Verify

```bash
# From your demo folder (requires a JDK):
javac OrderProcessor.java DateParser.java
```

## Takeaway

A documented contract and a staged, specific prompt turn the *same model* from a guesser into a
production-grade pair programmer. Specificity, a role, constraints, and process are the four dials
you control.
