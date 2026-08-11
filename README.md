<p align="center">
  <img src="assets/infomagnus-banner.svg" alt="InfoMagnus" height="56" />
</p>

<h1 align="center">🤖 copilot-labs</h1>

<p align="center">
  <img src="https://img.shields.io/badge/GitHub%20Copilot-000000?style=for-the-badge&logo=githubcopilot&logoColor=white" alt="GitHub Copilot" />
  <img src="https://img.shields.io/badge/Hands--on%20Labs-2088FF?style=for-the-badge&logo=readthedocs&logoColor=white" alt="Hands-on Labs" />
  <img src="https://img.shields.io/badge/Level-Fundamentals-4CAF50?style=for-the-badge&logo=bookstack&logoColor=white" alt="Fundamentals" />
</p>

<hr>

## 📚 Course content

All labs and demos are authored and maintained in the **[`docs/`](docs/) site** — a
participant-facing GitHub Pages site (built with the [just-the-docs](https://just-the-docs.com)
Jekyll theme). Every demo page embeds its full starter code **inline**, so participants
copy, paste, and run with **no clone required** — which scales cleanly to a room of 300+.

The content is organized by course track:

| Module | Labs | Demos / Use Cases |
|---|---|---|
| **Fundamental** | [docs/fundamental/labs.md](docs/fundamental/labs.md) | [docs/fundamental/demos.md](docs/fundamental/demos.md) |
| **Intermediate** | [docs/intermediate/labs.md](docs/intermediate/labs.md) | [docs/intermediate/demos.md](docs/intermediate/demos.md) |
| **Advanced** | [docs/Advance/labs.md](docs/Advance/labs.md) | [docs/Advance/demos.md](docs/Advance/demos.md) |
| **Prompt Engineering** | [docs/Prompt-Engg/labs.md](docs/Prompt-Engg/labs.md) | [docs/Prompt-Engg/demos.md](docs/Prompt-Engg/demos.md) |
| **Secure Development** | N/A | [docs/Security-Dev/demos.md](docs/Security-Dev/demos.md) |
| **Office Hours** | N/A | [docs/Office-Hours/usecases.md](docs/Office-Hours/usecases.md) |
| **Agents & MCP** | [docs/Agents-MCP/labs.md](docs/Agents-MCP/labs.md) | [docs/Agents-MCP/demos.md](docs/Agents-MCP/demos.md) |

---

## 🌐 Hosted docs site (GitHub Pages)

The site lives in the [docs](docs/) folder and is the **single source of truth** for all lab and
demo content.

### One-time repository settings (repo admin)

1. Open repository Settings -> Pages.
2. Under Build and deployment, set Source to GitHub Actions.
3. Confirm the workflow file exists at [.github/workflows/pages.yml](.github/workflows/pages.yml).
4. Optional: configure Custom domain if needed.
5. Keep Enforce HTTPS enabled.

### Publish flow

1. Commit and push changes to main.
2. The workflow [.github/workflows/pages.yml](.github/workflows/pages.yml) runs automatically.
3. Wait for the Deploy GitHub Pages workflow to complete.
4. Open the published site URL from Settings -> Pages.

Current published URL:
https://kalpanareddyc.github.io/copilot-labs-content/

### Rebuild or republish manually

1. Go to Actions -> Deploy GitHub Pages.
2. Select the latest run (or trigger a new run if manual dispatch is enabled).
3. Verify deployment is successful, then refresh the site URL.

### Troubleshooting checklist

1. Ensure GitHub Actions are enabled for the repository.
2. Ensure Source remains GitHub Actions in Settings -> Pages.
3. Review Actions logs for workflow or Jekyll build errors.
4. Confirm docs changes were pushed to main.
5. If using a custom domain, validate DNS records and HTTPS certificate status.

### Optional: repository About settings

These are optional quality-of-life settings in the repository home page About panel.

1. Open the repository home page and click the `About` gear icon.
2. Add a short Description, for example: GitHub Copilot hands-on labs and demos with copy-paste-run starter code.
3. For Website, either:
  - check `Use your GitHub Pages website`
4. Add Topics (recommended): github-copilot, jekyll, github-pages, just-the-docs, labs, demos.
5. Under Include in the home page, enable items you want visible (commonly Releases, Deployments, Packages).
6. Click Save changes.

**Preview locally:**

```bash
cd docs
bundle install
bundle exec jekyll serve   # http://localhost:4000
```
