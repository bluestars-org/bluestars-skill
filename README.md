# Bluestars agent skill & plugins

Connect Claude, ChatGPT, Microsoft 365 Copilot — or any MCP client — to a
**Bluestars** customer dashboard: conversation analytics, usage/billing, BSR
customer & visitor segmentation insights, and the public BSR API.

> This repository is generated and published automatically from
> [`bluestars-org/bluestars`](https://github.com/bluestars-org/bluestars)
> (`apps/sdk/plugin`). Do not edit it by hand — changes are overwritten on the
> next release.

## Install the skill (`npx skills add`)

```bash
npx skills add bluestars-org/bluestars-skill
```

This installs the shared `SKILL.md` (Agent Skills open standard) into any agent
that supports it — Claude Code, Cursor, Codex, Gemini CLI, and others.

## Install a platform plugin

Each folder under [`plugins/`](./plugins) is a ready-to-install bundle that
also wires up the Bluestars **MCP server** (`https://bluestars.app/api/mcp`,
OAuth 2.1 with CIMD + Dynamic Client Registration):

- [`plugins/claude`](./plugins/claude) — Claude Code / claude.ai plugin
  (`.claude-plugin` + `.mcp.json` + skill). Test locally with
  `claude --plugin-dir plugins/claude`.
- [`plugins/chatgpt`](./plugins/chatgpt) — ChatGPT Apps/Codex plugin
  (`.codex-plugin` + `.mcp.json` + skill).
- [`plugins/copilot`](./plugins/copilot) — Microsoft 365 Copilot Cowork
  bundle (`manifest.json` with a DCR MCP connector + skill).

On first use you authenticate with your Bluestars dashboard account (magic
link) and approve access; the token carries exactly your dashboard permissions.

Version: see [`VERSION`](./VERSION).
