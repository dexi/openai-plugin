# Dexi plugin for ChatGPT and Codex

Connects ChatGPT and Codex to your [Dexi](https://dexi.net) notes via the Dexi MCP server (`https://mcp.dexi.net/mcp`). On first use, Dexi's OAuth consent page opens in your browser — sign in, choose what the connection can access (everything, or a single folder/tag), and you're connected.

This is the [Agent Plugins](https://agent-plugins.org) packaging of the same skills that ship in the [Claude Code plugin](https://github.com/dexi/claude-plugin); the three `SKILL.md` files are kept in sync by hand (provider-neutral wording, `name:` frontmatter, no Claude-only keys).

## Layout

```
plugin.json          Agent Plugins manifest + OpenAI listing metadata (extensions.com.openai)
mcp.json             the remote Dexi MCP server (streamable HTTP, OAuth 2.1)
skills/<name>/       SKILL.md + agents/openai.yaml (which MCP tools the skill depends on)
assets/              icon-128.png (composer icon), logo-512.png (listing logo)
build.sh             zips the package for the OpenAI plugin portal → dist/
marketplace.json     Codex marketplace manifest for the public mirror (not part of the ZIP)
sync-mirror.sh       lays the package out as the public mirror repo (maintainers)
```

In the public mirror ([github.com/dexi/openai-plugin](https://github.com/dexi/openai-plugin)) the package sits under `plugins/dexi/`, with the Codex marketplace manifest at `.agents/plugins/marketplace.json`.

## What you get

- **The Dexi MCP server** — search, semantic search, note CRUD, tags, folders, bases, reviews, documented at [docs.dexi.net](https://docs.dexi.net/mcp/tools). The model uses them automatically once connected.
- **Capture** — save a finding, decision, or snippet from the current conversation as a Dexi note ("save that to my notes").
- **Recall** — search your notes (keyword + semantic) and answer from what you've saved ("did I save anything about X?").
- **Review** — a spaced-repetition review session: question-first quizzing on your due cards, grades recorded back to Dexi. Only runs when you ask for it.

## Install

**ChatGPT**: open **Plugins** in the ChatGPT sidebar, find **Dexi** (or go straight to the [listing](https://chatgpt.com/plugins/plugin_asdk_app_6ac01d33dd60819180f383d3c94a9546)), select **Install plugin**, then **Connect** and approve on Dexi's page. On a plan or workspace without the directory, add `https://mcp.dexi.net/mcp` as a custom MCP connector in developer mode — see [dexi.net/mcp/chatgpt](https://dexi.net/mcp/chatgpt).

**Codex**: install from the directory as above and pick Dexi under **Sources → Use plugins** in a task view. The public mirror is also a Codex plugin marketplace:

```
codex plugin marketplace add dexi/openai-plugin
```

then install Dexi from that marketplace in the Plugins Directory.

Docs: [docs.dexi.net/mcp/openai-plugin](https://docs.dexi.net/mcp/openai-plugin) · Overview: [dexi.net/chatgpt](https://dexi.net/chatgpt)

## Publishing (maintainers)

1. `./build.sh` → `dist/dexi-openai-plugin-<version>.zip` (bump `version` in `plugin.json` first; metadata, asset, and skill changes all need a new ZIP — MCP tool changes are re-scanned daily without one).
2. Upload at [platform.openai.com/plugins](https://platform.openai.com/plugins) → the Dexi plugin (or **Create plugin → With MCP** the first time), resolve the automated scan findings.
3. MCP tab → Connect: the portal issues a domain-verification token; set it as `OPENAI_APPS_CHALLENGE_TOKEN` on the `dexi-backend` Railway service so `https://mcp.dexi.net/.well-known/openai-apps-challenge` returns it, then verify.
4. Submit for review with the test cases, test account, and walkthrough video listed in the repo's `LISTINGS.md`.
5. Re-sync the public mirror: `./sync-mirror.sh <path-to-a-clone-of-dexi/openai-plugin>`, then commit and push there.

## Access control

Manage or revoke the connection in Dexi under **Settings → Connected apps**, where you can also restrict it to one folder or tag after the fact.

## License

MIT — see [LICENSE](LICENSE).
