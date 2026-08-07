---
title: Demo 3 — Prompt Files, Custom Instructions, Agents, Skills & MCP
layout: default
parent: Demos
grand_parent: Intermediate
nav_order: 3
permalink: /intermediate/demos/demo-3.html
description: "Build a complete Java Order Management app from a stub using every Copilot customization surface: instructions, prompt files, agents, skills, model picker, and MCP."
---

# ☕ Demo 3 — Prompt Files, Custom Instructions, Custom Agents, Skills & Model Picker
{: .no_toc }

**Agenda section:** Customizing Copilot for your team
**Deck demo:** Prompt Files · Custom Instructions · Custom Agents · Agent Skills · Model Picker · MCP
**Language:** **Java 17+**

Build a complete **Java Order Management application** from a stub, using every
Copilot customization surface. Each step introduces one `.github/` config file
so attendees see how teams standardize Copilot behavior across a project.

{: .highlight }
> **No clone required.** Copy the Java files into a folder, and the `.github/`
> config files into the matching paths shown below.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Files Overview

| File | Location | Purpose |
|---|---|---|
| `OrderRecord.java` | this folder | Data model (complete) |
| `OrderService.java` | this folder | Service class (**stub — all TODOs**) |
| `Main.java` | this folder | Driver with self-checks |
| `copilot-instructions.md` | `.github/` | Custom instructions (Java conventions) |
| `generate-java-service.prompt.md` | `.github/prompts/` | Reusable prompt file → `/generate-java-service` |
| `java-architect.md` | `.github/agents/` | Custom agent `@java-architect` |
| `generateJavaService.md` | `.github/skills/` | Agent skill for service scaffolding |
| `mcp.json` | `.vscode/` | GitHub MCP server config |

---

## Starter code

### `OrderRecord.java`

```java
/**
 * Demo 3 — Order record (data model).
 * 
 * A simple Java 17 record representing an order in the system.
 * This is the starting point — the service class will be generated
 * by Copilot using the prompt file and custom agent.
 *
 * Compile: javac *.java
 * Run:     java Main
 */
public record OrderRecord(int id, String customerName, String product, int quantity, double unitPrice) {

    /**
     * Compact constructor — validates inputs.
     */
    public OrderRecord {
        if (id <= 0) throw new IllegalArgumentException("ID must be positive");
        if (customerName == null || customerName.isBlank()) throw new IllegalArgumentException("Customer name required");
        if (product == null || product.isBlank()) throw new IllegalArgumentException("Product name required");
        if (quantity <= 0) throw new IllegalArgumentException("Quantity must be positive");
        if (unitPrice < 0) throw new IllegalArgumentException("Unit price cannot be negative");
    }

    /**
     * Calculate the line total for this order.
     * @return quantity * unitPrice rounded to 2 decimals
     */
    public double lineTotal() {
        return Math.round(quantity * unitPrice * 100.0) / 100.0;
    }
}
```

### `OrderService.java` (stub)

```java
import java.util.*;

/**
 * Demo 3 — Order Service (STUB).
 *
 * This class is intentionally incomplete. Copilot will fill in the
 * missing methods using:
 *   - The reusable prompt file  (/generate-java-service)
 *   - The custom agent          (@java-architect)
 *   - The generateJavaService skill
 *
 * Compile: javac *.java
 * Run:     java Main
 */
public class OrderService {

    private final Map<Integer, OrderRecord> store = new HashMap<>();
    private int nextId = 1;

    // TODO: implement findAll()
    // Should return all orders as an unmodifiable list.

    // TODO: implement findById(int id)
    // Should return Optional<OrderRecord>.

    // TODO: implement create(String customerName, String product, int quantity, double unitPrice)
    // Should auto-assign an ID, store the order, and return it.

    // TODO: implement update(int id, String customerName, String product, int quantity, double unitPrice)
    // Should return Optional<OrderRecord> — empty if ID not found.

    // TODO: implement delete(int id)
    // Should return true if removed, false if not found.

    // TODO: implement summary()
    // Should return a formatted string with all orders and a grand total.
}
```

### `Main.java`

```java
/**
 * Demo 3 — Main driver to verify OrderService works.
 *
 * Run after Copilot completes OrderService:
 *   javac *.java && java Main
 */
public class Main {

    public static void main(String[] args) {
        OrderService svc = new OrderService();
        boolean allPass = true;

        // 1. Create orders
        var o1 = svc.create("Alice", "Keyboard", 2, 45.00);
        var o2 = svc.create("Bob", "Monitor", 1, 250.00);
        var o3 = svc.create("Carol", "Mouse", 5, 12.50);
        allPass &= check("create", o1 != null && o2 != null && o3 != null);

        // 2. Find all
        allPass &= check("findAll", svc.findAll().size() == 3);

        // 3. Find by ID
        allPass &= check("findById_exists", svc.findById(1).isPresent());
        allPass &= check("findById_missing", svc.findById(999).isEmpty());

        // 4. Update
        var updated = svc.update(1, "Alice", "Keyboard Pro", 2, 55.00);
        allPass &= check("update_exists", updated.isPresent() && updated.get().product().equals("Keyboard Pro"));
        allPass &= check("update_missing", svc.update(999, "X", "Y", 1, 1.0).isEmpty());

        // 5. Delete
        allPass &= check("delete_exists", svc.delete(3));
        allPass &= check("delete_missing", !svc.delete(999));
        allPass &= check("after_delete", svc.findAll().size() == 2);

        // 6. Summary
        String summary = svc.summary();
        allPass &= check("summary", summary != null && summary.contains("Alice"));

        System.out.println("\n" + (allPass ? "ALL PASS" : "SOME FAILED"));

        if (summary != null) {
            System.out.println("\n--- Order Summary ---");
            System.out.println(summary);
        }
    }

    private static boolean check(String name, boolean pass) {
        System.out.printf("%-20s: %s%n", name, pass ? "PASS" : "FAIL");
        return pass;
    }
}
```

---

## Copilot customization config

Create these under your workspace root so `.github/` sits at the top level.

### `.github/copilot-instructions.md`

```markdown
# Copilot Custom Instructions — Intermediate Demo

## Language & Style

- **Language:** Java 17+ (use records, sealed interfaces, pattern matching).
- **Naming:** `camelCase` for methods/variables, `PascalCase` for classes, `UPPER_SNAKE` for constants.
- **Methods:** Keep under 20 lines. Extract helpers for complex logic.
- **No raw types:** Always parameterize generics (`List<Order>`, not `List`).

## Error Handling

- Validate inputs at public API boundaries. Throw `IllegalArgumentException` for bad input.
- Never return `null` from a collection method — return an empty collection.
- Use `Optional<T>` for single-value lookups that may not find a match.

## Documentation

- Every public class and method must have a Javadoc comment.
- Include `@param`, `@return`, and `@throws` tags.

## Testing

- Use JUnit 5 (`org.junit.jupiter`).
- Test method names: `methodName_condition_expectedResult` (e.g., `findById_invalidId_returnsEmpty`).
- Each test method should have exactly one assertion concept.

## Security

- Never log PII (names, emails, addresses).
- Sanitize all string inputs before processing.
- Use our internal authentication library for any auth operations.
```

### `.github/prompts/generate-java-service.prompt.md`

```markdown
---
mode: agent
description: Generate a Java REST service class from a specification.
tools: ['terminal', 'editFiles']
---

# Generate Java REST Service

You are a senior Java developer. Generate a production-quality REST service.

## Task

Create or update the Java file specified by the user with:

1. A Spring Boot-style **REST controller** or plain Java HTTP handler class.
2. Proper **CRUD methods**: list, get by ID, create, update, delete.
3. Use an in-memory `ArrayList` or `HashMap` as a data store (no database).
4. Add **input validation** — reject null/empty fields, return proper error messages.
5. Include **Javadoc** on every public method.

## Constraints

- Java 17+ syntax (records, text blocks, pattern matching where appropriate).
- No external dependencies beyond the JDK.
- Follow the project's custom instructions from `copilot-instructions.md`.
- After generating, compile with `javac *.java` and report any errors.

## Output Format

Return the complete Java source file(s). Do not truncate or use placeholders.
```

### `.github/agents/java-architect.md`

```markdown
---
name: java-architect
description: A Java architect agent that designs, builds, tests, and reviews Java applications following team conventions.
tools: ['terminal', 'editFiles', 'readFiles']
---

# Java Architect Agent

You are **java-architect**, a senior Java developer and software architect.

## Responsibilities

1. **Design** — Propose class structures, interfaces, and data models.
2. **Implement** — Write clean, idiomatic Java 17+ code following the project's `copilot-instructions.md`.
3. **Test** — Generate JUnit 5 tests and run them via `javac` / `java`.
4. **Review** — Audit code for bugs, style violations, and security issues.

## Workflow

When asked to build a Java application:

1. Read the project's custom instructions from `.github/copilot-instructions.md`.
2. Plan the class structure (models, services, utilities).
3. Implement each class one at a time.
4. Compile with `javac *.java` after each file.
5. Write and run JUnit tests.
6. Perform a final code review and suggest improvements.

## Conventions

- Always follow the naming, style, and documentation rules in `copilot-instructions.md`.
- Use the `#generateJavaService` skill when creating REST service classes.
- Prefer composition over inheritance.
- Keep classes focused — single responsibility principle.

## Model Recommendations

| Task | Recommended Model |
|---|---|
| Code generation, boilerplate | GPT-4.1 |
| Architecture decisions, design review | Claude Sonnet 4 / o3 |
| Test generation, refactoring | GPT-4.1 |
| Complex debugging, multi-file reasoning | Claude Sonnet 4 / o3 |
```

### `.github/skills/generateJavaService.md`

```markdown
---
name: generateJavaService
description: Skill to scaffold a Java service class with CRUD operations, validation, and Javadoc.
---

# Generate Java Service Skill

## When to Use

Use this skill when the user asks to:
- Create a new Java service or repository class
- Add CRUD operations to an existing class
- Scaffold a data model with a service layer

## Instructions

1. **Create a Java record** for the data model (e.g., `Order`, `Product`, `Customer`).
   - Use Java 17 record syntax: `public record Order(int id, String name, double total) {}`
   - Add a compact constructor for validation.

2. **Create a service class** with these methods:
   - `List<T> findAll()` — return all items
   - `Optional<T> findById(int id)` — return item by ID
   - `T create(T item)` — add and return the item
   - `Optional<T> update(int id, T item)` — update if exists
   - `boolean delete(int id)` — remove by ID

3. **Use an in-memory store** (`Map<Integer, T>`) — no database required.

4. **Add input validation:**
   - Reject null items → `IllegalArgumentException`
   - Reject negative or zero IDs → `IllegalArgumentException`
   - Reject empty/blank string fields → `IllegalArgumentException`

5. **Add Javadoc** on every public method with `@param`, `@return`, `@throws`.

6. **Compile and verify** with `javac` after generation.

## Example Output Structure

​```
OrderRecord.java      ← data model (record)
OrderService.java     ← service with CRUD + validation
OrderServiceTest.java ← JUnit 5 tests
​```
```

---

## Setup

| Setting | Value |
|---|---|
| **Java** | JDK 17+ on PATH (`javac --version` to verify) |
| **VS Code** | Open the folder so `.github/` is at the workspace root |
| **Prep** | `javac *.java` — compiles `OrderRecord` and `Main` but **fails** on `OrderService` (methods missing) |

---

## Demo Flow (7 Steps)

### Step 1 — Custom Instructions (`.github/copilot-instructions.md`)

**Goal:** Show how custom instructions shape *all* Copilot responses in the workspace.

**What to show:**
1. Open `.github/copilot-instructions.md` in the editor.
2. Walk through the rules: Java 17, naming conventions, Javadoc, JUnit 5, security.
3. Point out: *"Every Copilot response in this workspace will now follow these rules
   automatically — no need to repeat them in every prompt."*

**Quick proof — Ask mode:**
```
Explain the coding standards this project uses. Where are they defined?
```

Copilot reads `copilot-instructions.md` and summarizes the rules.

**Talking point:** *"Custom instructions are the team's coding standards, checked
into the repo. Every developer gets the same Copilot behavior."*

---

### Step 2 — Prompt File (`/generate-java-service`)

**Goal:** Show how a reusable prompt file turns a complex task into a one-command action.

**What to show:**
1. Open `.github/prompts/generate-java-service.prompt.md` — walk through the YAML
   front-matter (`mode: agent`, `tools`) and the task description.
2. In Chat, type `/` — the prompt appears as `/generate-java-service`.

**Prompt (type `/` and pick `generate-java-service`):**
```
/generate-java-service

Complete all TODO methods in #file:OrderService.java using the OrderRecord
model from #file:OrderRecord.java. The #file:Main.java self-checks must all pass.
```

**What happens:** Copilot follows the prompt file's spec to implement `findAll`,
`findById`, `create`, `update`, `delete`, and `summary` in `OrderService.java`.

**Verify:**
```bash
javac *.java && java Main
```

**Talking point:** *"Prompt files are reusable recipes committed to the repo. The
whole team gets the same `/command` — consistent output, no re-typing."*

---

### Step 3 — Model Picker (Choosing the Right Model)

**Goal:** Show the model picker and when to switch models.

**What to show:**
1. Click the **model dropdown** in the Chat panel.
2. Explain the tiers:

| Model | Best For |
|---|---|
| **GPT-5.4** | Fast code generation, boilerplate, completions |
| **Claude Sonnet 4.5** | Architecture decisions, nuanced reviews |
| **Claude Opus 4.8** | Complex multi-step reasoning, debugging |

See the [model comparison reference](https://docs.github.com/en/copilot/reference/ai-models/model-comparison).

3. Switch to **Claude Opus 4.8** for the next step (agent needs reasoning).

**Talking point:** *"Model choice is task-dependent. Use fast models for
mechanical work, reasoning models when the task needs judgment."*

---

### Step 4 — Custom Agent (`@java-architect`)

**Goal:** Show how a custom agent combines instructions + personality + tool access.

**What to show:**
1. Open `.github/agents/java-architect.md` — walk through the YAML
   (`name`, `tools`) and the responsibilities section.
2. In Chat, type `@java-architect` to invoke the custom agent.

**Prompt (Agent mode, using `@java-architect`):**
```
Review #file:OrderService.java — check that it follows our
copilot-instructions.md conventions. If anything violates the rules (Javadoc,
naming, validation, Optional usage), fix it. Then compile and run Main.
```

**What happens:** The agent reads `copilot-instructions.md`, audits the service
class, fixes any violations, compiles, and runs `Main` — all in one loop.

**Talking point:** *"Custom agents are specialists with a defined role. They
combine instructions, tool access, and model recommendations into a persona
your team can invoke by name."*

---

### Step 5 — MCP Integration (GitHub MCP Server)

**Goal:** Show how the GitHub MCP server provides model recommendations and enforces project-wide settings.

**How to add GitHub MCP to your project:**

1. Create `.vscode/mcp.json` in the repo root with:

```json
{
  "servers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/",
      "headers": {
        "Authorization": "Bearer ${input:github_mcp_pat}"
      }
    }
  },
  "inputs": [
    {
      "type": "promptString",
      "id": "github_mcp_pat",
      "description": "GitHub Personal Access Token",
      "password": true
    }
  ]
}
```

2. Run the MCP server.

**How to use it in the demo after the server is running:**
1. Keep Chat in **Agent mode** so Copilot can call MCP-backed tools.
2. Confirm the GitHub MCP server is available in the Chat tools list.
3. Ask Copilot to work against a real repository on GitHub.com instead of only local files.
4. Point out that MCP lets Copilot fetch live repo context such as issues, pull requests, search results, and repository metadata.

**Example prompts to show:**
```text
List the open issues in OWNER/REPO and summarize the top 3 by priority.
```

```text
Show the latest pull requests in OWNER/REPO and tell me which ones are ready for review.
```

```text
Find issues in OWNER/REPO related to authentication failures and summarize common causes.
```

```text
Summarize pull request #123 in OWNER/REPO and highlight any risky file changes.
```

```text
Search OWNER/REPO for references to OrderService and explain which files depend on it.
```

**Talking point:** *"MCP turns Copilot into a connected agent. It can move beyond local code and pull in live GitHub context like issues, PRs, and repo search results as part of the same workflow."*

---

## Quick-Run Commands

```bash
# Baseline (before Copilot completes OrderService):
javac *.java                   # compile error — OrderService methods missing

# After Step 2 (prompt file completes the service):
javac *.java && java Main      # ALL PASS
```

---

## Expected Final Output

**`java Main`:**
```
create              : PASS
findAll             : PASS
findById_exists     : PASS
findById_missing    : PASS
update_exists       : PASS
update_missing      : PASS
delete_exists       : PASS
delete_missing      : PASS
after_delete        : PASS
summary             : PASS

ALL PASS

--- Order Summary ---
#1  Alice        | Keyboard Pro     x2  $110.00
#2  Bob          | Monitor          x1  $250.00
Grand Total: $360.00
```
