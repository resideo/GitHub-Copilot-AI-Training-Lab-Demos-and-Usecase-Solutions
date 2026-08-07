---
title: Demo 6 — GitHub Copilot CLI
layout: default
parent: Demos
grand_parent: Intermediate
nav_order: 6
permalink: /intermediate/demos/demo-6.html
description: "Install, authenticate, and use the GitHub Copilot CLI to explain and suggest commands in terminal workflows."
---

# 🖥️ Demo 6 — GitHub Copilot CLI (Install, Authenticate, Use)
{: .no_toc }

**Agenda section:** Copilot productivity in terminal workflows
**Deck demo:** Copilot CLI setup + practical command usage
**Audience:** Intermediate developers
**Estimated time:** 12 to 15 minutes

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Demo Goal

By the end of this demo, attendees will be able to:

- Install the GitHub Copilot CLI (a GitHub-native AI agent in the terminal)
- Authenticate correctly for Copilot usage
- Run 3 quick agentic prompts from the terminal

---

## What Is the GitHub Copilot CLI?

The GitHub Copilot CLI brings a powerful AI agent directly into your terminal. Unlike
command-suggestion tools, it can work on your behalf: answer questions, write and debug
code, run shell/Git commands, and interact with GitHub.com (issues, branches, and pull
requests) through native MCP support.

Two interfaces:

1. Interactive - start a conversation and steer the work (`copilot`)
2. Programmatic - pass one prompt, get the task done, then exit (`copilot -p "..."`)

Included in Copilot Free, Pro, Pro+, Max, Business, and Enterprise subscriptions.

---

## Step 1 - Install GitHub Copilot CLI

The CLI is distributed via npm and package managers. Pick one method.

```bash
npm install -g @github/copilot        # or: winget install GitHub.Copilot
```

Verify install:

```bash
copilot --version
```

---

## Step 2 - Authenticate

Launch the CLI and complete the interactive login on first run:

```bash
copilot
```

Inside the session you can manage authentication with `/login` and `/logout`.
On first launch you will also be asked to confirm you trust the directory you
started from. Only launch Copilot CLI from directories you trust.

---

## Step 3 - Use Copilot CLI (3 Prompts)

Start an interactive session, then try these three easy prompts.

```bash
copilot
```

```text
Suggest improvements to OrderService.java
```

```text
Summarize the last 3 commits in this repo
```

```text
List my open PRs
```

The first two are safe, read-only local tasks; the third uses GitHub's native MCP
server to fetch your pull requests. None of them modify files.

Trainer callout:

- Copilot CLI is an agent, not just a command suggester
- Review each proposed action before approving the tool

---

## Step 4 - Work in the Workspace (Make a Change)

Now let Copilot make a real edit in this repository. From an interactive session,
ask it to improve a file, then review the diff before approving.

```bash
copilot
```

```text
Add Javadoc to every public method in OrderService.java, then show me the diff
```

What to point out:

- Copilot locates the file, proposes the edit, and asks for approval before writing
- Approve the `write` tool only after reviewing the change
- Use `/diff` at any time to see exactly what changed in the workspace

---

## Step 5 - Work in the Workspace (Commit and Open a PR)

Once you are happy with the change, ask Copilot to handle Git and GitHub for you.

```text
Create a branch, commit the OrderService.java changes, and open a pull request
```

What to point out:

- Copilot runs the Git operations and uses native MCP to open the PR on GitHub.com
- Approve each `shell(git)` step; deny anything you do not expect
- You are marked as the pull request author
- Branch protections, required checks, and org policies still apply

---

## Command Cheat Sheet

```bash
# Install
npm install -g @github/copilot        # or: winget install GitHub.Copilot

# Version
copilot --version

# Interactive session
copilot
```

---

## Demo Completion Checklist

- [ ] Installed GitHub Copilot CLI (npm or winget)
- [ ] Authenticated and confirmed the trusted directory
- [ ] Ran the 3 prompts in an interactive session
- [ ] Made a workspace change and reviewed the diff
- [ ] Committed and opened a pull request from the CLI

---

## Suggested Talk Track (60 seconds)

The GitHub Copilot CLI puts a GitHub-native agent right in your terminal. Instead of
just suggesting commands, it can write and debug code, run Git operations, and work
with your issues and pull requests through native MCP - without leaving the shell.
Install it, launch `copilot`, and run a few natural-language prompts to see the agent
suggest improvements, summarize commits, and list your PRs - reviewing each proposed
action before you approve it.

