---
title: Demo 1 — Prompt Hygiene in Practice
layout: default
parent: Demos
grand_parent: Secure Development
nav_order: 1
permalink: /security-dev/demos/demo-1.html
description: "Replace one over-shared prompt with staged, narrow prompts on a small Python retry helper: least context, least privilege, least persistence."
---

# 🔐 Demo 1 — Prompt Hygiene in Practice
{: .no_toc }

**Deck checkpoint:** Demo 1 in Section 01 - Secure Prompt Patterns
**Language:** **Python** · **File:** `demo_01_retry_helper.py`
**Duration:** 8-10 minutes · **Prompts:** 4
**Covers:** unsafe vs safe context sharing · least context / least privilege / least persistence · staged follow-up prompts

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Why this demo

This is the security version of a prompt-quality escalation demo. The room first sees an unsafe,
over-shared prompt. Then you replace it with a series of smaller, safer prompts against one tiny
Python file. The point is not just “write a better prompt.” The point is that security improves
when the work is broken into narrow, reviewable steps.

## File

| File | Purpose |
| --- | --- |
| `demo_01_retry_helper.py` | Small retry helper with a docstring contract and a TODO implementation for live prompting |

### `demo_01_retry_helper.py`

```python
"""Demo 01 starter file for prompt hygiene in practice.

Goal: keep the code small so the presenter can show that useful Copilot prompts do not require
sharing broad repository context, secrets, or customer-specific details.
"""


def should_retry(status_code: int, retry_count: int, max_retries: int = 3) -> bool:
	"""Return whether a request should be retried.

	Rules for the live demo:
	- Retry status 429 while retries remain.
	- Retry 5xx responses while retries remain.
	- Do not retry other 4xx responses.
	- Stop retrying when retry_count reaches max_retries.
	"""

	# TODO (live demo): implement with a narrow, safe prompt.
	return False


def run_demo() -> None:
	scenarios = [
		(429, 0),
		(503, 1),
		(400, 0),
		(500, 3),
	]

	for status_code, retry_count in scenarios:
		decision = should_retry(status_code, retry_count)
		print(
			f"status={status_code} retry_count={retry_count} should_retry={decision}"
		)


if __name__ == "__main__":
	run_demo()
```

## Setup

| Setting | Value |
| --- | --- |
| **Modes** | Ask + Inline Chat |
| **Model** | Auto (or GPT-5.4) |
| **Prep** | Open `demo_01_retry_helper.py` in the editor |

Run from `courses/CP-Security-Dev/demos/`:

```powershell
python demo_01_retry_helper.py
```

---

## Part A - Show the unsafe prompt first

Do not submit this. Read it out and ask the room what is unsafe or unnecessary.

```text
Please fix this production retry bug for customer site 10427. The outage started during incident
INC-4421. Our current auth header is Bearer [NEVER_PASTE_REAL_VALUES]. Here is the full sync module,
our regional config, and the current failure payload. Rewrite everything so retries work again.
```

Ask the room:

1. What should never be pasted here?
2. What context is larger than the task really needs?
3. Which details can be replaced with placeholders or removed entirely?

---

## Part B - Replace one risky prompt with four safe prompts

Open `demo_01_retry_helper.py` and keep the file visible throughout the sequence.

**Prompt 1 - minimal implementation prompt**

```text
Implement should_retry in demo_01_retry_helper.py.

Rules:
- The function receives an HTTP status code and current retry count.
- Return False for client errors except 429.
- Return True for 429 and any 5xx status while retry_count < max_retries.
- Keep the change local to this function.

Do not invent secrets, tokens, customer identifiers, or production payloads.
```

**Prompt 2 - narrow validation prompt**

```text
Add four focused assertions to the demo runner in demo_01_retry_helper.py.

Cover:
- 429 with retries remaining
- 503 with retries remaining
- 400 as non-retryable
- retry cutoff reached

Keep everything placeholder-only and local to this file.
```

**Prompt 3 - explanation prompt**

```text
Explain which details from the earlier unsafe prompt were risky or unnecessary.

Return:
- removed sensitive details
- the minimum context needed for this task
- one rule to follow for future prompts
```

**Prompt 4 - safe extension prompt**

```text
Suggest one low-risk improvement to demo_01_retry_helper.py that stays within the same file.

Constraints:
- no network calls
- no secrets or customer data
- no new dependencies
- explain why the suggestion is low risk
```

---

## What to point out

- Multiple prompts can still be safe if each one stays narrow and local.
- The model gets enough context to help without seeing sensitive operational details.
- The follow-up prompts are separated by intent: implement, validate, explain, improve.
- Each prompt is written as if it might be retained, audited, or reviewed later.

## Verify

```powershell
python demo_01_retry_helper.py
```

## Takeaway

Prompt quality and context hygiene are security controls.

## Fallback

If Copilot is unavailable, walk the room through the unsafe prompt, rewrite it into the four safe
prompts live, and run the Python file only as a simple local example.
