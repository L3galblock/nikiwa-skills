---
name: nikiwa-setup
description: >
  Install and configure the Nikiwa MCP server for blockchain forensics
  and investigation. Provides 50 tools for wallet risk scoring, address
  profiling, counterparty mapping, token risk traits, contract analysis,
  DeFi market data, Hyperliquid activity, and Polymarket positions.
  Supports OAuth login with a Nikiwa account or a Bearer API key for
  headless agents. Use when the user wants to set up Nikiwa MCP, connect
  to on-chain forensics data, or install blockchain investigation tools
  in Claude, ChatGPT, OpenClaw, Cursor, Codex, Gemini CLI, VS Code, or
  any other MCP client.
version: 1.0.1
author: nikiwa
---

# Nikiwa MCP Server Setup

Connect an AI agent to Nikiwa's blockchain forensics database: 50 tools
covering wallet risk and compliance, address profiling, counterparty
analysis, token and contract risk, on-chain forensics, DeFi market data,
Hyperliquid activity, and Polymarket positions.

**Server details:**

| | |
|---|---|
| Endpoint | `https://pro-api.nikiwa.com/mcp` |
| Transport | Streamable HTTP |
| Server name | `nikiwa-tools` |
| Auth | OAuth 2.0 (PKCE, dynamic client registration) or Bearer API key |

## Prerequisites

- A Nikiwa account with an active API subscription. Sign up or upgrade
  at https://nikiwa.com

## Step 1: Identify the client and auth method

First determine which MCP client the user wants to connect. If you are
running inside one (e.g. you are Claude Code), default to configuring
yourself unless the user says otherwise. If it's unclear, ask.

**Check for an existing install first.** If a `nikiwa` MCP server is
already configured (for example, this skill arrived via the `nikiwa`
Claude Code plugin, which registers the server itself), do not add it
again. Skip straight to Step 3 (authenticate) or Step 4 (verify).

Then pick the auth method:

- **OAuth (recommended)**: for interactive clients with a browser
  (Claude, ChatGPT, Cursor, Windsurf, VS Code, Codex, Gemini CLI).
  The user signs in once; no key to copy, tokens refresh automatically.
- **API key**: for headless or self-hosted agents (servers, bots,
  scripts) where a browser consent flow is not available. The user
  creates a key at nikiwa.com (profile → **MCP Keys**; keys start with
  `nkw_live_`) and it is sent as `Authorization: Bearer <key>`.

## Step 2: Add the server

Use the section matching the client. When editing a JSON config file,
read it first and merge the `nikiwa` entry into the existing structure;
never overwrite other servers.

### Claude Code

Run in a terminal:

```bash
claude mcp add --transport http nikiwa https://pro-api.nikiwa.com/mcp
```

Add `--scope user` to make it available in all projects. For API-key
auth instead of OAuth, append:
`--header "Authorization: Bearer nkw_live_YOUR_KEY"`.

### Claude.ai (web) and Claude Desktop

This cannot be done programmatically. Instruct the user to:

1. Open **Settings → Connectors → Add custom connector**
2. Name: `Nikiwa`
3. URL: `https://pro-api.nikiwa.com/mcp`
4. Click **Add**, then **Connect** to open the Nikiwa login and consent
   screen

Note: Claude Desktop's `claude_desktop_config.json` does not accept
remote `url` servers directly. If the Connectors UI is unavailable,
bridge via `mcp-remote` (see the stdio-only section below).

### ChatGPT

Instruct the user to enable **Settings → Apps & Connectors → Advanced →
Developer mode**, then add a connector with MCP server URL
`https://pro-api.nikiwa.com/mcp` and OAuth authentication.

### Cursor

Add to `~/.cursor/mcp.json` (global) or `.cursor/mcp.json` (project):

```json
{
  "mcpServers": {
    "nikiwa": {
      "url": "https://pro-api.nikiwa.com/mcp"
    }
  }
}
```

For API-key auth, add
`"headers": {"Authorization": "Bearer nkw_live_YOUR_KEY"}` to the entry.
Otherwise the user clicks **Needs login** next to the server in
Cursor Settings → MCP to run the OAuth flow.

### Windsurf

Add to `~/.codeium/windsurf/mcp_config.json`. Note that Windsurf uses
`serverUrl`, not `url`:

```json
{
  "mcpServers": {
    "nikiwa": {
      "serverUrl": "https://pro-api.nikiwa.com/mcp"
    }
  }
}
```

### VS Code (GitHub Copilot)

Run in a terminal:

```bash
code --add-mcp '{"name":"nikiwa","type":"http","url":"https://pro-api.nikiwa.com/mcp"}'
```

Or add to `.vscode/mcp.json`:

```json
{
  "servers": {
    "nikiwa": {
      "type": "http",
      "url": "https://pro-api.nikiwa.com/mcp"
    }
  }
}
```

### Codex CLI

Run in a terminal:

```bash
codex mcp add nikiwa --url https://pro-api.nikiwa.com/mcp
codex mcp login nikiwa
```

If the installed Codex version lacks `--url`, add to
`~/.codex/config.toml` instead:

```toml
[mcp_servers.nikiwa]
url = "https://pro-api.nikiwa.com/mcp"
```

### Gemini CLI

Run in a terminal:

```bash
gemini mcp add --transport http nikiwa https://pro-api.nikiwa.com/mcp
```

Or add to `~/.gemini/settings.json`:

```json
{
  "mcpServers": {
    "nikiwa": {
      "httpUrl": "https://pro-api.nikiwa.com/mcp"
    }
  }
}
```

### OpenCode

Add to `opencode.json` (project) or `~/.config/opencode/opencode.json`:

```json
{
  "mcp": {
    "nikiwa": {
      "type": "remote",
      "url": "https://pro-api.nikiwa.com/mcp",
      "enabled": true
    }
  }
}
```

### OpenClaw

Run on the host where the OpenClaw gateway runs:

```bash
openclaw mcp set nikiwa '{"url":"https://pro-api.nikiwa.com/mcp","transport":"streamable-http"}'
```

OpenClaw typically runs headless on a server, so prefer an API key over
OAuth. Pass it as a header:

```bash
openclaw mcp set nikiwa '{"url":"https://pro-api.nikiwa.com/mcp","transport":"streamable-http","headers":{"Authorization":"Bearer nkw_live_YOUR_KEY"}}'
```

Other self-hosted assistants (NanoClaw, ZeroClaw, and similar) follow
the same pattern: native remote MCP config with an `Authorization`
header if supported, otherwise the stdio bridge below.

### Stdio-only clients and headless agents

For clients that only speak stdio, bridge with `mcp-remote`
(requires Node.js):

```json
{
  "mcpServers": {
    "nikiwa": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://pro-api.nikiwa.com/mcp"]
    }
  }
}
```

On a headless machine (no browser), prefer an API key, which avoids
the OAuth browser flow entirely:

```json
{
  "mcpServers": {
    "nikiwa": {
      "command": "npx",
      "args": [
        "-y", "mcp-remote", "https://pro-api.nikiwa.com/mcp",
        "--header", "Authorization: Bearer ${NIKIWA_API_KEY}"
      ],
      "env": {
        "NIKIWA_API_KEY": "nkw_live_YOUR_KEY"
      }
    }
  }
}
```

If OAuth must be used headless: `mcp-remote` prints an OAuth URL on
startup. Send that URL to the user through whatever channel is
available. After they log in, the browser redirects to a
`http://localhost:...` URL that won't load. Have them copy that full
URL from the address bar and send it back, then complete the callback
with it.

## Step 3: Authenticate (OAuth)

Skip this step if using an API key; the Bearer header authenticates
every request.

- **Claude Code**: run `/mcp`, select the Nikiwa server, choose
  **Authenticate**. A browser opens to Nikiwa's login and consent page.
- **Claude.ai / Claude Desktop / ChatGPT**: the connector prompts for
  login when added.
- **Cursor / Windsurf / VS Code**: click the login/authorize prompt next
  to the server in the client's MCP settings.
- **Codex**: `codex mcp login nikiwa`.
- **Other clients**: any client implementing MCP authorization works.
  Nikiwa uses standard OAuth 2.0 authorization code with PKCE and
  supports dynamic client registration, so no pre-registration is
  needed.

After the user clicks **Allow** on the consent screen, the subscription
is verified automatically and tokens refresh on their own. If the
subscription lapses, the next refresh prompts renewal at
https://nikiwa.com.

## Step 4: Verify

Ask the agent:

> "Resolve vitalik.eth and check its wallet risk score"

If it calls `resolve_ens` and `get_wallet_risk_score` and returns data,
the connection works. Also confirm the client lists the server's
resources: `nikiwa://chains` (valid networks) and `nikiwa://usage`
(composition recipes).

## Available tools (50)

| Tool | Description |
|------|-------------|
| `resolve_ens` | Resolve an ENS name (e.g. vitalik.eth) to an Ethereum address |
| `resolve_token_symbol` | Resolve a token symbol/name to contract addresses across networks |
| `analyze_transaction` | Decode and classify a single transaction (participants, value flow, risk flags) |
| `get_wallet_risk_overview` | Address risk overview (label/profile/exposure summary) for a wallet |
| `get_wallet_risk_score` | Risk/security snapshot for a wallet (sanctions, scam, mixer, behavioral signals) |
| `get_wallet_address_profile` | Address profile: platform usage + malicious-event history (MistTrack) |
| `get_wallet_osint_record` | OSINT records linked to a wallet (Nikiwa address book) |
| `get_wallet_stablecoin_compliance` | Full stablecoin (USDT/USDC) compliance history for an address: every blacklist event |
| `is_address_blacklisted_by_stablecoin` | Is an address currently blacklisted by Tether (USDT) or Circle (USDC) on a given chain |
| `get_wallet_stats` | Activity stats for a wallet: per-token sent/received, first/last activity, labels |
| `get_wallet_transactions` | Recent transactions for a wallet from chain-native sources |
| `get_wallet_transaction_patterns` | Behavioral transaction patterns for a wallet (activity cadence, flow patterns) |
| `get_wallet_pnl` | Detailed realized/unrealized PnL for a wallet (per-token, with totals) |
| `get_wallet_portfolio_breakdown` | Portfolio breakdown: major/top holdings, total value, and composition analysis |
| `get_wallet_token_holdings` | Token holdings (symbol, balance, USD value) for a wallet on one network or all |
| `get_wallet_account_meta` | Account metadata for a wallet (account type, label) |
| `get_wallet_deployed_contracts` | Contracts deployed by a wallet |
| `get_wallet_counterparties` | Top counterparties a wallet transacts with, ranked by volume |
| `get_wallet_counterparties_analysis` | Counterparty analysis: most-interacted + top inflow/outflow addresses for a wallet |
| `get_token_info` | Token metadata (name, symbol, supply, decimals, basic profile) for a contract |
| `get_token_market_analysis` | Market analysis for a token: liquidity, volume, supply, price signals |
| `get_token_risk_traits` | Token contract risk traits: taxes, ownership, liquidity, honeypot/security flags |
| `get_token_holders_traders` | Top holders + trader PnL/concentration for a token |
| `get_token_flows` | Token flows by cohort |
| `get_token_flow_intelligence` | Smart-money / exchange flow intelligence for a token |
| `analyze_token_developer` | Deployer/developer analysis for a token contract (creator history, related deployments) |
| `analyze_token_website` | Token website/domain analysis: whois age/privacy/registry + blocklist signals |
| `get_contract_creation` | Deployer address + creation transaction for a contract |
| `get_contract_source_code` | Verified contract source code + ABI (EVM chains) |
| `get_protocols_overview` | DeFi protocol rankings by TVL or movers (change_1d/7d/1m), optionally filtered |
| `get_protocol_details` | One protocol's TVL history + latest inflow snapshot |
| `get_categories_tvl` | DeFi TVL aggregated by protocol category (Lending, DEX, Liquid Staking, ...) |
| `get_chains_tvl` | Chain TVL leaderboard (or historical series) |
| `get_dex_volume` | DEX trading volume: global overview, per-chain, or per-protocol summary |
| `get_perps_volume` | Derivatives/perps volume: overview or per-protocol summary |
| `get_fees_revenue` | Protocol/chain fees or revenue |
| `get_yields_pools` | Screen yield/APY pools, ranked by APY |
| `get_stablecoins` | Stablecoin supply overview, optionally drilled to one chain and/or one asset |
| `get_stablecoin_dominance` | Per-stablecoin dominance on one chain, ranked by USD circulating supply |
| `get_bridges_overview` | Bridges ranked by recent volume with share % |
| `get_coin_prices` | Current + 24h-ago prices for DeFiLlama coin ids in chain:address or coingecko format |
| `get_narratives_performance` | FDV-weighted narrative/category performance (AI, RWA, L2s, memes, ...) |
| `get_raises` | Crypto funding rounds, newest first, optional exact category filter |
| `get_hacks` | Recent protocol hack/exploit incidents, newest first |
| `get_hyperliquid_activity` | Hyperliquid trading activity (positions, fills, PnL) |
| `search_polymarket` | Search Polymarket events/markets by free-text query |
| `get_polymarket_user` | Polymarket user profile + portfolio value for a wallet address |
| `get_polymarket_open_bets` | Open Polymarket positions for a wallet |
| `get_polymarket_closed_bets` | Closed Polymarket positions for a wallet |
| `get_polymarket_activities` | Polymarket trade/activity history for a wallet |

## Troubleshooting

- **401 Unauthorized / auth loop**: for OAuth, re-run the client's
  authenticate flow. For API keys, check the header is exactly
  `Authorization: Bearer nkw_live_...` and the key has not been revoked.
- **"API Subscription Required"**: the account needs an active plan at
  https://nikiwa.com.
- **Browser doesn't open**: the client may not support OAuth for remote
  MCP servers. Use an API key, or bridge with `mcp-remote` (which
  handles OAuth itself).
- **429 Too Many Requests**: API keys are rate-limited per key
  (60 requests/minute by default). Slow down or batch queries.
- **Tool returns `{status: no_data}`**: often the wrong network. Most
  wallet and token tools take a `network` param
  (eth/btc/trx/base/arbitrum/optimism/sol/avalanche/bnb/polygon).
  Read `nikiwa://chains` for the authoritative list and double-check
  the chain before assuming there is no data.
- **Connection refused / 404**: verify the URL is exactly
  `https://pro-api.nikiwa.com/mcp` (note the `/mcp` path).

## More documentation

- MCP overview: https://docs.nikiwa.com/mcp/overview
- Installation (all clients): https://docs.nikiwa.com/mcp/install
- OAuth flow details: https://docs.nikiwa.com/mcp/connect-oauth
- API keys: https://docs.nikiwa.com/mcp/api-keys
- Tool catalog: https://docs.nikiwa.com/mcp/tools
