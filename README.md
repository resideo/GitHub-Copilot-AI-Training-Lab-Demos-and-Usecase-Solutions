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

The content is organized by course, each with **Labs** and **Demos**:

| Module | Labs | Demos |
|---|---|---|
| **Fundamental** | [docs/fundamental/labs.md](docs/fundamental/labs.md) | [docs/fundamental/demos.md](docs/fundamental/demos.md) (Demos 1–4) |
| **Intermediate** | [docs/intermediate/labs.md](docs/intermediate/labs.md) | [docs/intermediate/demos.md](docs/intermediate/demos.md) (Demos 1–6) |

---

## 🌐 Hosted docs site (GitHub Pages)

The site lives in [`docs/`](docs/) and is the **single source of truth** for all lab and
demo content.

**Enable it once (repo admin):**

1. Push to `main`. The workflow [`.github/workflows/pages.yml`](.github/workflows/pages.yml) builds the site.
2. Repo **Settings → Pages → Build and deployment → Source → GitHub Actions**.
3. The site publishes at `https://<owner>.github.io/copilot-labs/`.

**Preview locally:**

```bash
cd docs
bundle install
bundle exec jekyll serve   # http://localhost:4000
```
