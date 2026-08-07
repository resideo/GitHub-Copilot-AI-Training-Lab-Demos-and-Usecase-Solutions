---
title: Demos
parent: Fundamental
nav_order: 2
has_children: true
permalink: /demos/fundamental/
description: "Four presenter-led walkthroughs with complete runnable code embedded on each page."
---

# 🎬 Fundamental Guided Demos
{: .no_toc }

Presenter-led walkthroughs, one per Copilot skill area. **Every demo page contains the full
starter code inline** — copy it into a file named as shown, follow the steps, and run.
A collapsible **solution** is included on each page so you can verify or unblock instantly.

{: .highlight }
> **No clone required.** Copy the code straight from the page — every demo embeds
> its full starter code inline.

## Demo map

| Demo | Area | Language | File to create |
|---|---|---|---|
| [Demo 1 — Copilot overview]({{ '/demos/demo-1.html' | relative_url }}) | Code completions | Python | `orders.py` |
| [Demo 2 — Getting started in your IDE]({{ '/demos/demo-2.html' | relative_url }}) | Inline chat, NES, Ask, Plan, `/` commands | C++ | `tip_calculator.cpp` |
| [Demo 3 — Writing effective prompts]({{ '/demos/demo-3.html' | relative_url }}) | Prompt crafting, `@`/`#` refs, refinement | Java | `Catalog.java` |
| [Demo 4 — Tips and techniques]({{ '/demos/demo-4.html' | relative_url }}) | Refactor, docs, convert, `/tests`, MCP intro | C# → Dart | `Inventory.cs` |

## Which model / which mode (quick reference)

| Feature | Mode in VS Code | Recommended model |
|---|---|---|
| Code completions | Inline (just type) | Completion model (automatic) |
| Next Edit Suggestions (NES) | Inline (Tab to jump) | Completion model (automatic) |
| Inline chat | **Ctrl+I** in the editor | GPT-5.4 (fast base model) |
| Quick chat | **Ctrl+Shift+I** | GPT-5.4 |
| Ask mode | Chat panel → **Ask** | GPT-5.4 |
| Plan mode | Chat panel → **Plan** | A reasoning model (optional; GPT-5.4 is fine) |
| Edit (refactor / convert / docs) | Chat panel → **Edit** | GPT-5.4 |
