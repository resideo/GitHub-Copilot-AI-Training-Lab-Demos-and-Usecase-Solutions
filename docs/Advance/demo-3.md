---
title: Demo 3 — Agent Skills (SKILL.md) with Java
layout: default
parent: Demos
grand_parent: Advanced
nav_order: 3
permalink: /advance/demos/demo-3.html
description: "Package domain knowledge and a bundled script in a SKILL.md that Copilot auto-invokes the moment a task involves a .java file."
---

# ☕ Demo 3 — Agent Skills (`SKILL.md`) with Java
{: .no_toc }

**Surfaces:** VS Code Copilot Chat — **Agent mode**
**Theme:** A custom **Skill** that is **auto-invoked** whenever you work on Java.
**File:** `DeviceMonitor.java`

> **Focus:** Show that a `SKILL.md` file packages domain knowledge (plus a
> bundled script) that Copilot loads **on demand** — automatically — the moment
> a task involves a `.java` file. You never have to say "use the skill."

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Objective

Demonstrate **Agent Skills**:

- A skill is a folder with a `SKILL.md` (and optional `scripts/`, `references/`,
  `assets/`).
- The agent reads only the skill **`name` + `description`** up front (~100
  tokens). When your request matches those trigger words, it loads the full
  `SKILL.md` body and applies it.
- Because our skill's `description` is full of **Java** keywords, selecting or
  editing a `.java` file and asking Copilot to work on it **auto-invokes** the
  skill.

This is different from instructions:

| | Custom Instructions (Demo 1) | Skills (this demo) |
|---|---|---|
| Loaded | Always (or by `applyTo` glob) | **On demand**, by `description` match |
| Can bundle scripts/assets | No | **Yes** (`scripts/`, `references/`, `assets/`) |
| Invocation | Passive | Auto **and** `/slash` command |

---

## Files

| File | Purpose |
|------|---------|
| `Demo-3/DeviceMonitor.java` | Java sample with **intentional style violations** |
| `.github/skills/java-smart-home/SKILL.md` | The skill — Java conventions + when to use |
| `.github/skills/java-smart-home/scripts/build-and-run.ps1` | Bundled asset: compile + run the sample |

### `DeviceMonitor.java` (starter with style violations)

```java
import java.util.HashMap;
import java.util.Map;

/**
 * Smart-home device battery monitor (Demo 3 — Skills).
 *
 * This starter version has INTENTIONAL style violations so the demo can show
 * how a custom Copilot Skill teaches the agent to fix them automatically:
 *   - raw {@code Map} instead of generics
 *   - magic numbers (20, 50) instead of named constants
 *   - {@code System.out.println} instead of java.util.logging
 *   - returns {@code null} / {@code Object} instead of throwing on bad input
 *   - no input validation
 *   - terse names ({@code add}, {@code batt}, {@code b})
 *
 * Run: see Demo-3/demo.md (compile with javac, run with java).
 */
public class DeviceMonitor {

    // Style issue: raw Map (no generics).
    static Map devices = new HashMap();

    // Style issue: terse names, no validation.
    static void add(String id, int b) {
        devices.put(id, b);
    }

    // Style issue: returns Object, returns null when missing.
    static Object batt(String id) {
        return devices.get(id);
    }

    // Style issue: magic numbers 20 and 50, unchecked cast.
    static String status(String id) {
        int b = (int) devices.get(id);
        if (b < 20) {
            return "LOW";
        } else if (b < 50) {
            return "MEDIUM";
        }
        return "OK";
    }

    public static void main(String[] args) {
        add("sensor-1", 15);
        add("thermostat-1", 80);
        // Style issue: System.out instead of a logger.
        System.out.println("sensor-1: " + status("sensor-1"));
        System.out.println("thermostat-1: " + status("thermostat-1"));
    }
}
```

### `.github/skills/java-smart-home/SKILL.md` (the skill)

```markdown
---
name: java-smart-home
description: 'Use when writing, implementing, reviewing, refactoring, or fixing Java (.java) code in this smart-home device repository. Applies the project Java conventions: use java.util.logging instead of System.out.println, use generics instead of raw types, replace magic numbers with named constants, throw IllegalArgumentException or NoSuchElementException with a specific message instead of returning null, add Javadoc to every public method, and validate inputs at the boundary. Also compiles and runs the Demo-3 Java sample.'
---

# Java Smart-Home Conventions

On-demand knowledge for working with **Java** in this repository. Because the
`description` above lists Java trigger words, the agent loads this skill
automatically whenever a task involves a `.java` file — no need to mention it.

## When to Use

- Implementing, refactoring, or fixing any `.java` file (e.g. `DeviceMonitor.java`)
- Reviewing Java code for the smart-home device services
- Compiling and running the Demo-3 Java sample

## Conventions to Apply

| # | Avoid | Prefer |
|---|-------|--------|
| 1 | `System.out.println(...)` | `java.util.logging.Logger` |
| 2 | Raw types (`Map devices`) | Generics (`Map<String, Integer> devices`) |
| 3 | Magic numbers (`20`, `50`) | `private static final int` named constants |
| 4 | `return null` / `Object` on error | Throw `IllegalArgumentException` / `NoSuchElementException` with a specific message |
| 5 | No validation | Validate inputs at the boundary, fail early |
| 6 | Terse names (`add`, `batt`, `b`) | Descriptive names (`registerDevice`, `batteryLevel`, `level`) |
| 7 | Missing docs | Javadoc on every public method (`@param`, `@return`, `@throws`) |

## Procedure

1. Read the target `.java` file and identify which conventions above are violated.
2. Apply every applicable convention. Keep the code **dependency-free** (JDK
   standard library only) to match the repository rules.
3. Preserve the public behavior and the `main` self-output unless asked otherwise.
4. Build and run to verify it still compiles and behaves the same:
   run the bundled build-and-run script from the repository root.

## Error Handling Rules (repository-specific)

- Never return `null` to signal an error when a value is expected — throw instead.
- Error messages must name the offending **device id** and **field**.
- Never log PII (device owner names, addresses, emails).

## Notes

- This skill is **auto-invoked** by the model for Java tasks. It also appears as
  a `/java-smart-home` slash command for manual invocation.
- Keep changes minimal and idiomatic; do not add frameworks or external
  dependencies.
```

---

## Prerequisites

- VS Code with GitHub Copilot (**Agent mode**)
- **JDK 17+** installed and on `PATH` (`javac -version`, `java -version`)
- The repo opened at its **root** so `.github/skills/` is discovered

---

## How skills are structured (talk track)

```
.github/skills/java-smart-home/
├── SKILL.md                    # Required — name MUST match the folder name
└── scripts/
    └── build-and-run.ps1       # Bundled asset, referenced from SKILL.md
```

Valid skill locations (project scope): `.github/skills/<name>/`,
`.agents/skills/<name>/`, `.claude/skills/<name>/`. Personal scope:
`~/.copilot/skills/<name>/`.

`SKILL.md` frontmatter (the part that drives auto-invocation):

```yaml
---
name: java-smart-home          # 1–64 chars, lowercase + hyphens, MUST match folder
description: 'Use when ... Java (.java) ...'   # keyword-rich = discoverable
# user-invocable: true         # (default) also show as /java-smart-home
# disable-model-invocation: false  # (default) allow automatic loading
---
```

> **Key idea:** the `description` is the trigger. Keyword-rich → the model finds
> and loads it automatically. Vague ("a helpful skill") → it never fires.

---

## Demo Steps (10 minutes)

### Step 1 — Show the skill (2 min)

Open `.github/skills/java-smart-home/SKILL.md`. Walk through:

- the `name` matching the folder,
- the **Java-keyword-rich** `description` (this is what makes it auto-invoke),
- the conventions table and the reference to the bundled build/run script.

### Step 2 — Trigger the skill automatically (3 min)

In **Agent mode**, open `DeviceMonitor.java` and ask a plain Java request —
**do NOT mention the skill**:

```
Refactor DeviceMonitor.java to production quality.
```

Expected behavior — the agent silently loads the `java-smart-home` skill and:

- replaces `System.out.println` with a `java.util.logging.Logger`,
- adds generics (`Map<String, Integer>`),
- extracts magic numbers into named constants (e.g. `LOW_BATTERY_THRESHOLD`),
- throws `IllegalArgumentException` / `NoSuchElementException` with a message
  that names the offending **device id** instead of returning `null`,
- validates inputs at the boundary,
- renames `add`/`batt`/`b` to descriptive names,
- adds Javadoc to public methods.

> **Prove it fired:** ask `Which skills did you use for that, and why?` — the
> agent should cite `java-smart-home`, triggered by the `.java` task.

### Step 3 — Verify it still runs (1 min)

```powershell
pwsh .github/skills/java-smart-home/scripts/build-and-run.ps1
# Same behavior — sensor-1 LOW, thermostat-1 OK
```

### Step 4 — Invoke it manually as a slash command (2 min, optional)

Because `user-invocable` defaults to `true`, the skill also appears after `/`:

```
/java-smart-home review DeviceMonitor.java and list any remaining convention gaps
```

---

## How to create your own skill (recap)

1. Create the folder: `.github/skills/<skill-name>/`.
2. Add `SKILL.md` with `name` (matching the folder) and a **keyword-rich**
   `description` that says *what* and *when*.
3. (Optional) Add `scripts/`, `references/`, or `assets/` and link them from
   `SKILL.md` with relative `./` paths.
4. Keep `SKILL.md` under ~500 lines; push detail into `references/` so it loads
   progressively.
5. Reload VS Code / start a new chat so the skill is discovered.

---

## Troubleshooting

- **Skill never fires:** the `description` is too vague or missing Java trigger
  words. Add concrete keywords (`Java`, `.java`, method/behavior names).
- **Skill not listed:** folder name must equal the `name` field; the file must
  be named exactly `SKILL.md`; the repo root must contain `.github/skills/`.
- **`/java-smart-home` missing:** ensure `user-invocable` is not `false`.
- **No auto-loading:** ensure `disable-model-invocation` is not `true`.
- **`javac` not found:** install a JDK 17+ and reopen the terminal.

---

## Key Teaching Points

1. Skills package **procedural knowledge + assets** the agent loads **on demand**.
2. The **`description` is the trigger** — keyword-rich descriptions get invoked.
3. Progressive loading keeps context small: name/description first, body when
   relevant, referenced files only when needed.
4. Skills complement instructions: instructions are always-on rules; skills are
   on-demand workflows that can also ship scripts.
