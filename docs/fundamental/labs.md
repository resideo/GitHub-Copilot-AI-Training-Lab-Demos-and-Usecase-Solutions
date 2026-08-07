---
title: Labs
parent: Fundamental
nav_order: 1
permalink: /labs/fundamental/
description: "GitHub Copilot Fundamental hands-on labs."
---


<h1 align="center">🎯 GitHub Copilot Fundamentals — Hands-on Labs</h1>

<p align="center">
  <img src="https://img.shields.io/badge/C%2B%2B-00599C?style=for-the-badge&logo=cplusplus&logoColor=white" alt="C++" />
  <img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white" alt="Python" />
  <img src="https://img.shields.io/badge/C%23-239120?style=for-the-badge&logo=csharp&logoColor=white" alt="C#" />
  <img src="https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black" alt="JavaScript" />
  <img src="https://img.shields.io/badge/Java-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white" alt="Java" />
</p>

<p align="center">
  <img src="https://img.shields.io/badge/GitHub%20Copilot-000000?style=for-the-badge&logo=githubcopilot&logoColor=white" alt="Fundamental" />
  <img src="https://img.shields.io/badge/Hands--on-Labs-2088FF?style=for-the-badge" alt="Hands-on Labs" />
</p>

<hr>

A single set of **language-neutral** labs you can complete in **any one** of five languages:
**C++, Python, C#, JavaScript, or Java**. Every task is described by *intent* — you tell Copilot
what you want and it generates the code in the language you picked. **Copilot writes the code,
you review and verify it.**

{: .note }
> New here? Do the [Setup]({{ '/setup/' | relative_url }}) once, then work through the labs **in order** — each builds a
> skill the next one uses. Budget ~5 min setup + ~20 min per lab. Labs 1–4 are the core.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## How to use these labs

1. **Pick one language** and do the [Setup]({{ '/setup/' | relative_url }}) once.
2. Work through the labs **in order**.
3. For every step, the guide gives you the **exact prompt to type** and **which Copilot mode** to use.
4. **Try it yourself first**, then read what Copilot produced before accepting it.
5. **Run & verify** at the end of each lab — the program must print all `PASS` lines.

---

## Lab 1 — Code completions & comment-driven completion

**Time:** ~15 min · **Mode:** code completions · **Model:** GPT-5.4

**Learning objectives**
- Describe intent in a comment and let Copilot's code completions write the function body.
- Compare alternative completions and accept the best one with confidence.

**Prompts to give:**
```text
// TODO: validate that a string is a well-formed email address -> returns true/false
// TODO: check a password is strong (>= 8 chars, upper, lower, digit) -> true/false
```

1. Create the scratch file for your language (see [Setup]({{ '/setup/' | relative_url }})) and add the two `TODO` comments above.
2. Put your cursor under the first comment and start typing the function signature. Watch for
   **grey ghost text**. Press **Alt+]** to compare an alternative, then **Tab** to accept.
   *Do not type the regex yourself* — let Copilot suggest it, then read it before accepting.
3. Repeat for the password check.
4. Below them, let code completions help you write print/assert checks that exercise a valid and an
   invalid input for each function.
5. **Run & verify** — all checks print `PASS`.

**Language note:** name functions idiomatically — `validate_email` / `is_strong_password` (Python),
`validateEmail` / `isStrongPassword` (C++, C#, JavaScript, Java).

**Checkpoints**
- ✅ You saw code completions and accepted a suggestion with **Tab**.
- ✅ Both functions were generated from comments, not hand-typed.
- ✅ The program runs and prints only `PASS`.

**Stretch:** add a `validate_phone` helper from a comment; use **Alt+]** to compare two regexes.

---

## Lab 2 — Chat modes: Ask · Plan · Edit · Agent (+ inline chat + NES)

**Time:** ~25 min · **Mode:** code completions · inline chat · Ask · Plan · Edit · Agent · NES · **Model:** GPT-5.4

**Learning objectives**
- Grow a small module using the right Copilot mode at each step.
- Know when to use code completions, inline chat, Ask, Edit, Agent, and NES.

1. **Create the file and start with code completions (`factorial`).**
   New file. Type a `factorial` signature and let code completions complete it. Compare an alternative with **Alt+]**, then accept.

2. **Inline chat — validate input in `factorial`.**
   Cursor inside `factorial`, press **Ctrl+I**, then use:
   ```text
   Return/throw a clear error if n is negative.
   ```
   Review the diff and accept.

3. **Add `gcd` and use Ask mode to understand it.**
   Add a `gcd` function with code completions. Then open Chat → **Ask**, select it, and use:
   ```text
   Explain how this works and its time complexity.
   ```
   This should explain only (no file change).

4. **Plan mode — decide the refactor before editing.**
   Open Chat → **Plan** and use:
   ```text
   Plan the steps to refactor an average/mean function so it safely handles empty input and has clear documentation. Steps only.
   ```

5. **Edit / Agent mode — apply the refactor plan.**
   Add an `average` function. Chat → **Edit/Agent**, select it, then use:
   ```text
   Refactor to handle an empty input safely and add a doc comment.
   ```
   Review and apply the diff.

6. **NES — propagate a rename.**
   Rename `average` to `mean` at its definition. Copilot shows a **Next Edit Suggestion** at the call site — press **Tab** to jump, **Tab** to accept.

7. **Agent mode — generate tests and notes.**
   Chat → **Agent**, then use:
   ```text
   Add unit tests for all functions in this file and a short README section explaining them.
   ```
   Review each change before accepting.

8. **Run & verify.**

**Picking a mode:** Ask = "explain / what is", Plan = "outline approach before edits", Edit = "change this selection", Agent = "do a multi-step task". Use the smallest mode that fits.

**Checkpoints**
- ✅ `factorial` came from code completions and rejects negative input.
- ✅ Ask explained `gcd` **without** editing the file.
- ✅ Edit added empty-input handling via a reviewed diff.
- ✅ NES finished the rename at the call site.
- ✅ Agent added tests you reviewed before accepting.

---

## Lab 3 — Writing effective prompts

**Time:** ~20 min · **Mode:** Chat (Ask / Plan / Edit / Agent) with context variables · **Model:** GPT-5.4

**Learning objectives**
- Feel the difference between weak and strong prompts on the same task.
- Steer Copilot with context variables (`#file`).

1. **Create sample data with code completions.**
   Create a small list/array of users with `name`, `age`, and `active`.

2. **Weak prompt first (show the gap).**
   In Chat → Edit / Agent, select the data and use:
   ```text
   sort this
   ```
   Note how much Copilot has to guess.

3. **Strong prompt second (improve precision).**
   Undo, then use:
   ```text
   Sort the users by active first (active before inactive), then by age ascending, then by
   name A–Z. Do not mutate the original; return a new sorted collection.
   ```
   Compare the result — specificity wins.

4. **Add context references.**
   Reference `#<filename>` then ask a context-aware follow-up question.

5. **Run & verify.**
   Confirm ties on age are resolved by name order.

**Checkpoints**
- ✅ You produced a multi-key sort from a single precise prompt.
- ✅ The original collection is unchanged (immutability respected).
- ✅ You used at least one context reference (`#file`).

**Stretch:** ask Copilot to add a secondary prompt that groups users by `active` and counts each.

---

## Lab 4 — Practical scenarios: generate, refactor, debug, document

**Time:** ~25 min · **Mode:** Ask · Edit / Agent · inline chat · **Model:** GPT-5.4

**Learning objectives**
- Run the everyday loop: generate, refactor, debug, and document code with Copilot.
- Use Ask mode to find the root cause of a real bug.

1. **Generate the parser.**
   In Chat → Edit / Agent, use:
   ```text
   Create a function that parses a "key=value" config string into a map/dictionary/object,
   ignoring blank lines and lines starting with #.
   ```

2. **Introduce a bug on purpose.**
   By hand, change the split to keep only the first character of the value. Run it and confirm a failing case.

3. **Debug with Ask mode.**
   Select the failing function, Chat → Ask, then use:
   ```text
   This returns the wrong value for keys whose value has more than one character. Why, and what's the fix?
   ```
   Apply the fix and re-run.

4. **Refactor with inline chat.**
   Use **Ctrl+I** and prompt:
   ```text
   Refactor for readability and handle a missing '=' gracefully.
   ```

5. **Document the function.**
   Ask Copilot to add a doc comment covering parameters, return value, and edge cases.

6. **Run & verify.**
   Confirm all checks pass, including multi-character values and `#comment` lines.

**Checkpoints**
- ✅ Copilot generated a working parser.
- ✅ You used Ask mode to locate the root cause of a real bug.
- ✅ The refactor handles a malformed line without crashing.
- ✅ A clear doc comment now describes the function.

---

## Lab 5 — Introduction to MCP (conceptual)

**Time:** ~20 min · **Mode:** Ask · Agent · **Model:** reasoning model (e.g. Claude Sonnet 4 / GPT-5 mini)

**Learning objectives**
- Explain what the Model Context Protocol (MCP) is in your own words.
- Simulate a tool registry and dispatcher locally, with no servers.

1. **Learn MCP concepts first.**
   Chat → Ask and use:
   ```text
   In 5 bullet points, what is MCP (Model Context Protocol), and how do MCP servers give an
   AI agent access to tools and data? Keep it tool-agnostic.
   ```

2. **Model a tool registry.**
   Create a small data structure with three imaginary tools (`name`, `description`, `input` schema).

3. **Write a dispatcher.**
   Ask Copilot for a dispatcher function that routes by tool name and returns a simulated result.

4. **Extend with Agent mode.**
   Chat → Agent and use:
   ```text
   Add a new "weather" tool to the registry and a test that dispatches a call to it.
   ```
   Review before accepting.

5. **Discuss production usage (no code).**
   Ask when a real MCP server is better than hard-coded tools and what security checks to do first.

**Checkpoints**
- ✅ You can explain MCP in your own words (client, server, tools, context).
- ✅ Your dispatcher routes a named call to the right simulated tool.
- ✅ Agent extended the registry and you reviewed the change.

---

## Lab 6 — Responsible usage: review, validate, fix

**Time:** ~20 min · **Mode:** Ask · Edit / Agent · **Model:** reasoning model

**Learning objectives**
- Treat Copilot output as a draft: review critically, validate with tests, and fix.
- Catch a subtle boundary/operator bug that only review or tests reveal.

1. **Generate a draft implementation.**
   Ask Copilot in Edit / Agent mode to create a discount-percentage function with tiers at 100 / 500 / 1000, then accept the first draft as-is.

2. **Review critically before editing.**
   Chat → Ask and use:
   ```text
   Review this for boundary bugs, wrong comparison operators, and unhandled negative or zero
   input. List issues before changing anything.
   ```

3. **Add boundary and invalid-input tests.**
   Ask Copilot to add tests for exact boundaries (100, 500, 1000) and negative/zero input. Run tests and expect at least one failure.

4. **Fix with Edit / Agent mode.**
   Use Edit / Agent to correct the boundary/operator issue and invalid-input handling.

5. **Re-run until green.**
   Re-run tests until all pass.

6. **Reflect.**
   Note one thing Copilot got wrong that only review/testing caught.

**Responsible-usage reminders**
- **Review everything** Copilot generates — you own the code you accept.
- **Never paste secrets, credentials, or PII** into prompts.
- **Test generated code** and check edge cases before trusting it.
- **Review before committing.** Copilot is a fast pair programmer, not an authority.

**Checkpoints**
- ✅ You found at least one real issue by reviewing, not running.
- ✅ Boundary tests exist and pass after your fix.
- ✅ Invalid input is handled safely.

---

## Wrap-up

You practised the full Copilot workflow — code completions, inline chat, Ask / Plan / Edit / Agent, NES,
effective prompting, MCP concepts, and responsible review — **in your own language**. The same habits
carry over to the Intermediate, Prompt Engineering, Advanced, and Agents & MCP sessions.
