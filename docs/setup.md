---
title: Setup
permalink: /setup/
nav_order: 1
description: "Install one language toolchain, confirm Copilot, and learn the modes you'll use."
---

# Shared setup (do once)
{: .no_toc }

You need an editor with Copilot, a GitHub Copilot licence, and **one** language toolchain.
Everything runs **locally** — no paid services, servers, or network calls.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Editor + Copilot (any one editor)

| Requirement | Check it works |
|---|---|
| **VS Code** (recommended), **Visual Studio 2022**, or a **JetBrains IDE** | Editor opens |
| **GitHub Copilot** + **GitHub Copilot Chat** extensions, signed in | Copilot status bar shows **Ready** |
| GitHub account with an active **Copilot licence** | No "no access" warning in the Copilot panel |

Supported IDEs and per-IDE features: the
[Copilot feature matrix](https://docs.github.com/en/copilot/reference/copilot-feature-matrix){: target="_blank" }.

---

## Pick ONE language toolchain

Install the toolchain for the language you'll use, plus its editor extension.
**Any of the five is fully supported** — pick what you know best.

| Language | Install (on PATH) | Verify | Editor extension |
|---|---|---|---|
| **C++** | C++17 compiler — `g++`, `clang++`, or MSVC `cl` | `g++ --version` | C/C++ (`ms-vscode.cpptools`) |
| **Python** | **Python 3.10+** | `python --version` | Python (`ms-python.python`) |
| **C#** | **.NET SDK 8.0+ (LTS)** | `dotnet --version` | C# Dev Kit (`ms-dotnettools.csdevkit`) |
| **JavaScript** | **Node.js LTS** + npm | `node -v` && `npm -v` | (built-in JS support) |
| **Java** | **JDK 17+** | `java -version` && `javac -version` | Extension Pack for Java (`vscjava.vscode-java-pack`) |

---

## Create & run a scratch project (your language)

Use this to create the file(s) each lab or demo asks for, and to run them.

### C++
```bash
# create main.cpp, then:
g++ -std=c++17 main.cpp -o app && ./app          # bash
g++ -std=c++17 .\main.cpp -o app.exe; .\app.exe  # PowerShell
```

### Python
```bash
# create main.py, then:
python main.py
```

### C#
```bash
dotnet new console -o lab
cd lab
# replace Program.cs with the demo code, then:
dotnet run
```

### JavaScript
```bash
# create main.js, then:
node main.js
```

### Java
```bash
# create Main.java (or the class name the demo uses), then:
javac Main.java
java Main
```

---

## Copilot features cheat sheet

| Feature | What it is | How to invoke |
|---|---|---|
| **Code completions** | Grey inline completion as you type | Just type; **Tab** accepts, **Esc** dismisses |
| **Cycle suggestions** | Alternative completions | **Alt+]** / **Alt+[** |
| **Inline chat** | Ask/edit inside the file | **Ctrl+I** (VS Code) |
| **Quick chat** | Fast one-off question | **Ctrl+Shift+I** |
| **Ask mode** | Explain code, answer questions (no edits) | Chat view → **Ask** |
| **Plan mode** | Outline steps before making changes | Chat view → **Plan** |
| **Edit mode** | Targeted edits with a reviewable diff | Chat view → **Edit** |
| **Agent mode** | Multi-file, multi-step tasks | Chat view → **Agent** |
| **Next Edit Suggestions (NES)** | Predicts your *next* edit | Enable in settings; **Tab** to jump/accept |

{: .note }
> **Enable NES in VS Code:** Settings → search `github.copilot.nextEditSuggestions.enabled` → **On**.

### Keyboard shortcuts (Windows / Linux)

| Action | Shortcut |
|---|---|
| Accept ghost text | **Tab** |
| Dismiss suggestion | **Esc** |
| Next / previous suggestion | **Alt+]** / **Alt+[** |
| Accept word only | **Ctrl+→** |
| Inline chat | **Ctrl+I** |
| Quick chat | **Ctrl+Shift+I** |
| Open Chat view | **Ctrl+Alt+I** |
