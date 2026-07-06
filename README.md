# Nikiwa Skills & Plugin

Official agent integrations for [Nikiwa](https://nikiwa.com) — blockchain forensics and investigation. Connects your AI agent to the Nikiwa MCP server: 50 tools for wallet risk scoring, address profiling, counterparty mapping, token and contract risk, DeFi market data, Hyperliquid activity, and Polymarket positions.

## Install

### Claude Code (plugin — recommended)

Registers the Nikiwa MCP server and the setup skill in one step:

```
/plugin marketplace add L3galblock/nikiwa-skills
/plugin install nikiwa@nikiwa
```

On first use, Claude Code prompts you to authenticate with your Nikiwa account via OAuth.

### Any agent (skill only)

Installs the `nikiwa-setup` skill, which walks your agent through adding and authenticating the MCP server in whatever client it runs in:

```bash
npx skills add L3galblock/nikiwa-skills
```

### Manual

Per-client setup instructions (Claude.ai, ChatGPT, Cursor, VS Code, Codex, Gemini CLI, Windsurf, OpenCode, headless agents) live in the [installation docs](https://docs.nikiwa.com/mcp/install).

## What's inside

| Path | Purpose |
| --- | --- |
| `.claude-plugin/plugin.json` | Claude Code plugin manifest — registers the MCP server (`https://pro-api.nikiwa.com/mcp`) |
| `.claude-plugin/marketplace.json` | Marketplace manifest for `/plugin marketplace add` |
| `skills/nikiwa-setup/SKILL.md` | Agent skill: guided install, authentication, and verification of the Nikiwa MCP server |

## Requirements

A Nikiwa account with an active API subscription — sign up at [nikiwa.com](https://nikiwa.com). Authentication is OAuth (sign in once) or a Bearer [API key](https://docs.nikiwa.com/mcp/api-keys) for headless agents.

## Documentation

- [MCP server overview](https://docs.nikiwa.com/mcp/overview)
- [Installation](https://docs.nikiwa.com/mcp/install)
- [Tool catalog](https://docs.nikiwa.com/mcp/tools)

## License

MIT
