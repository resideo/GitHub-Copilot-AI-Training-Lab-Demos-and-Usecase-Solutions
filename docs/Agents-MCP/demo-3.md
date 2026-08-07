---
title: Demo 3 — Figma MCP to HTML
layout: default
parent: Demos
grand_parent: "Agents & MCP"
nav_order: 3
permalink: /agents-mcp/demos/demo-3.html
description: "Generate HTML and CSS from a Figma design by using Copilot Chat with Figma MCP."
---

# Demo 4 — Generate HTML from a Figma Design with Figma MCP
{: .no_toc }

{: .highlight }
> **No clone required.** Copy the `.vscode/mcp.json` file below into the workspace root,
> start the MCP server, then use the prompt exactly as written.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Files overview

| File | Location | Purpose |
|---|---|---|
| `mcp.json` | `.vscode/` | Configures the Figma MCP server and prompts for the API key at runtime |

---

## Supporting file to copy

### `.vscode/mcp.json`

```json
{
	"servers": {
		"Framelink Figma MCP": {
			"command": "npx",
			"args": [
				"-y",
				"figma-developer-mcp",
				"--figma-api-key=${input:figmaApiKey}",
				"--stdio"
			],
			"type": "stdio"
		}
	},
	"inputs": [
    {
        "id": "figmaApiKey",
        "type": "promptString",
        "description": "Enter your Figma API key",
        "password": true
    }
]
}
```

---

## Prerequisites

- Install Figma MCP in Visual Studio Code.
- Create a Figma API key and provide it when the MCP config prompts for `figmaApiKey`.
- Ensure the Figma MCP server is started and running before moving to the prompt.
- If you need a new key, create one at [Figma MCP](https://www.figma.com/mcp).

---

## Steps

### Step 1

Open Copilot Chat in Visual Studio Code.

- Use **Agent Mode** and select the **Claude Sonnet 3.7** model.

### Step 2

In the chat, enter the following prompt to generate HTML and CSS for a Figma design using Figma MCP:

```text
Based on this Figma design https://www.figma.com/design/VSf4Q1h17D0PWq8svyly2w/Travling----Travel-Login-Screen--Community-?node-id=6-2686&p=f&t=Y1zDQFWrkAX5H6dw-0 create a webpage: html and css only, no javascript. Download all needed images and put them into "images" folder and put them on the webpage.
```

### Step 3

Review the generated HTML and CSS files.

- Ensure that the structure matches the Figma design.
- Check that all images are correctly downloaded and placed in the `images` folder.
- Verify that the styles are applied as per the design specifications.

### Step 4

Make any necessary adjustments to the HTML and CSS files.

- You may need to tweak styles or structure to better fit your needs or to correct any discrepancies.

### Step 5

Save your changes and test the webpage in a browser to ensure everything displays correctly.

### Step 6

If you encounter any issues or need further modifications, continue to use Copilot Chat to refine your HTML and CSS.

### Step 7

Once satisfied with the output, you can proceed to add javascript functionality using the below prompt:

```text
Update the HTML file to add javascript functionality to the webpage.
```

---

## Expected output

- An `images` folder containing the downloaded design assets.
- An HTML file with structure and styling based on the referenced Figma design.
