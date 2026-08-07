---
title: Demo 2 — Engineering Practices
layout: default
parent: Demos
grand_parent: Intermediate
nav_order: 2
permalink: /intermediate/demos/demo-2.html
description: "Get comfortable with professional Copilot tools — debug the decision process, share chats, and use GitHub.com collaboration features."
---

# 🛠️ Demo 2 — Engineering Practices with GitHub Copilot
{: .no_toc }

**Agenda section:** Debugging, Collaboration, Team Productivity
**Deck demo:** Engineering Practices — Debug Decisions · Share Chats · GitHub.com Collaboration
**Surfaces:** **VS Code IDE** & **GitHub.com**

Welcome to the GitHub Copilot engineering practices demo! Here we're going to get comfortable with the professional tools and features in GitHub Copilot. We'll start in the VS Code IDE and then move to github.com for additional collaboration features.

{: .highlight }
> **No clone required.** Follow the steps below in VS Code and on
> [github.com/copilot](https://github.com/copilot) — nothing to install.


## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 🔧 IDE Features

GitHub Copilot has many features designed to help engineering teams understand AI decision-making and collaborate effectively on code generation.

### 🐛 Step 1: Inspect Copilot's Decision Process

**Why this matters:** Understanding how Copilot makes suggestions helps you write better prompts and trust the AI's recommendations.

#### Method 1: Using Keyboard Shortcut
1. Press `Ctrl + Shift + P` (Windows/Linux) or `Cmd + Shift + P` (Mac)
2. Type "Copilot Chat Debug"
3. Select **"Copilot Chat Debug: Focus on Copilot Chat Debug View"**

#### Method 2: Using the Menu
1. Go to **View** → **Command Palette**
2. Type "Copilot Chat Debug"
3. Select **"Copilot Chat Debug: Focus on Copilot Chat Debug View"**

#### What You'll See
Once the debug panel opens, you can explore:
- **Prompts:** The actual prompts sent to the AI
- **System Prompts:** Background instructions given to Copilot
- **Metadata:** Context information and settings
- **Response Details:** How Copilot formulated its suggestions

**💡 Pro Tip:** Use this when Copilot's suggestions seem unexpected - you can see exactly what context it's using!

---

### 💬 Step 2: Share Chat Conversations with Your Team

**Why this matters:** Sharing successful prompts and conversations helps your team learn effective AI collaboration patterns.

#### Export a Chat Conversation

**Method 1: Keyboard Shortcut**
1. Press `Ctrl + Shift + P` (Windows/Linux) or `Cmd + Shift + P` (Mac)
2. Type "Chat: Export"
3. Select **"Chat: Export Chat..."**

**Method 2: Menu Navigation**
1. Go to **View** → **Command Palette**
2. Type "Chat: Export"
3. Select **"Chat: Export Chat..."**

**What happens:** This creates a file containing your entire chat history that you can share with teammates.

#### Import a Chat Conversation

**Method 1: Keyboard Shortcut**
1. Press `Ctrl + Shift + P` (Windows/Linux) or `Cmd + Shift + P` (Mac)
2. Type "Chat: Import"
3. Select **"Chat: Import Chat..."**

**Method 2: Menu Navigation**
1. Go to **View** → **Command Palette**
2. Type "Chat: Import"
3. Select **"Chat: Import Chat..."**

**Use case:** Import conversations shared by teammates to see their successful prompting strategies.

---

## 🌐 GitHub.com Collaboration Features

GitHub.com provides additional tools for managing Copilot at the organizational level and sharing knowledge across teams.

### 🎛️ Step 3: Explore Prompts

**Why this matters:** Prompts define how Copilot behaves across your organization. Understanding these helps ensure consistent, high-quality code generation that follows your team's standards.

#### Personal Instructions
1. **Navigate to GitHub Copilot:** Go to [https://github.com/copilot](https://github.com/copilot)
2. **Access settings:** Click on your user icon on the bottom-left corner
3. **Open personal instructions:** Select **"Personal instructions"**
4. **Add in instructions:** You can add customize guidelines into how Copilot responds to your prompts

#### What to Explore
- **Personal instructions:** Are there any preferences you'd like in the instructions? Test it out!



