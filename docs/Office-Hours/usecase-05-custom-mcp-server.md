---
title: Use Case 5 - Custom MCP Server
layout: default
parent: Use Cases
grand_parent: Office Hours
nav_order: 5
permalink: /office-hours/use-cases/usecase-05-custom-mcp-server.html
description: "A ready-made custom MCP server demo pattern for office-hours sessions."
---

# 🧩 Custom MCP Server Demo

## ❓ Question This File Answers

*Create and consume a custom MCP server. A ready-made sample if we can demo during the call.*

## 📝 Summary

For initial adoption, the most reliable MCP example is a small incident-helper server
backed by static demo data.

## ✅ Recommended Approach

Use a file-backed server with two or three clear business tools instead of a live
enterprise dependency. This proves the MCP pattern end to end without making the session
fragile.

## 🧠 Why This Approach Fits

- It is easy to explain in a few minutes.
- It avoids authentication and environment risk during the call.
- It can be reused across troubleshooting, multi-agent, and workflow demos.

## 🛠️ Suggested Tool Contract

| Tool | Purpose |
| --- | --- |
| `search_sample_logs(service, window)` | Return matching sample incidents or log lines. |
| `get_runbook(error_code)` | Return the relevant remediation notes. |
| `lookup_service_owner(service)` | Return the owning team or escalation contact. |

## ⚙️ Example Client Configuration

```json
{
	"servers": {
		"incident-helper": {
			"command": "python",
			"args": ["./mcp/incident_helper.py"]
		}
	}
}
```

## 💬 Example Prompt

```text
Use the available MCP tools to investigate the timeout problem in checkout-service.
Find the relevant log pattern, return the matching runbook, and tell me who owns the
service.
```

## 🧪 Implementation Guidance

1. Show the small server and the tools it exposes.
2. Connect the server to the client workflow.
3. Ask a question that requires at least two tool calls.
4. Let Copilot assemble the answer from the tool outputs.
5. Reuse the same server in another scenario to show the pattern is extensible.

## 🛟 Fallback Plan

- Pre-record the tool outputs if the environment is unstable.
- Keep the server local and file-backed.
- Focus the conversation on tool contracts and orchestration rather than infrastructure.

## 🔑 Key Takeaway

The fastest successful MCP demo is a small, local, file-backed server with clear
business tools. It proves the pattern without making the session fragile.
