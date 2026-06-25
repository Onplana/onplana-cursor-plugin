# Onplana for Cursor

> Stay on Plan A.

Connect Cursor to **[Onplana](https://onplana.com)** , an AI-native project & portfolio management (PPM) platform and the successor to **Microsoft Project Online** (which retires Sept 30, 2026). This plugin bundles the remote Onplana MCP server with a skill that teaches Cursor how to drive real project work: create and break down projects, plan sprints/epics/milestones, track status and Earned Value (EVM), run risk analysis, log time, and move proposals through governance.

The plugin is the **MCP server + skill** combination Cursor recommends, not a bare server: the skill gives the agent the product model and the common workflows so it reaches for the right tools without trial and error.

## What's inside

| File | Purpose |
| --- | --- |
| `mcp.json` | Registers the remote Onplana MCP server (`https://mcp.onplana.com/mcp`). |
| `skills/onplana/SKILL.md` | Teaches Cursor what Onplana is, when to use it, the tool families, and the common workflows + example prompts. |
| `.cursor-plugin/plugin.json` | Plugin manifest (name, version, author, license, logo, keywords). |
| `assets/logo.svg` | The Onplana chevron mark (#2563EB). |

## Install

1. Install the plugin from the Cursor Marketplace (in-editor **Browse plugins**), or add the MCP server directly with the `mcp.json` above.
2. On first use, Cursor runs the **OAuth sign-in** flow and you authorize Onplana in the browser. No client id/secret to configure , see Authentication below.
3. Ask the agent something like *"Create a project in Onplana for the website redesign and break this spec into tasks and a 2-week sprint."*

You need an Onplana account (free to start at [onplana.com](https://onplana.com)). MCP is available on every plan; the set of tools and the concurrent-connection count scale with your plan tier.

## Tool count

This plugin exposes **247 tools** , verified against the live server's `tools/list` (the manifest the Cursor Marketplace review pulls), as of v0.1.0 (2026-06-25). The number is the full toolset available to a complete-feature (Enterprise) org; plan-gated capabilities (e.g. Gantt, sprints, governance, EVM) are hidden on lower tiers, so a free-tier connection sees a subset. Tool families: projects, tasks, dependencies, sprints, epics, milestones, risks, issues, governance/proposals, timesheets & compliance, resource capacity, portfolios, EVM/finance, reporting, and workspace documents.

> The plugin description, this README, and the live MCP server all state the same number (247). If you see an older figure (e.g. "29 tools") on a third-party listing, it is stale.

## Authentication

OAuth 2.1 with **Dynamic Client Registration (DCR)** and **PKCE (S256)**. There are no static client credentials: Cursor's "Add to Cursor" + OAuth flow registers a client dynamically against Onplana's DCR endpoint and completes the authorization code exchange, so `mcp.json` only needs the server URL. Access tokens are scoped to your org and revocable from Onplana's Connected Apps settings.

## Versioning

This is the Cursor **plugin** (a new artifact), starting at `0.1.0`. It points at the remote Onplana MCP server, which is versioned independently in the [`onplana-mcp-server`](https://github.com/Onplana/onplana-mcp-server) repo (server manifest currently `0.2.4`). The plugin version tracks changes to the bundle (skill, manifest, logo), not the server.

## Links

- Product: https://onplana.com
- Docs: https://docs.onplana.com
- MCP server + TypeScript client: https://github.com/Onplana/onplana-mcp-server
- MCP endpoint: https://mcp.onplana.com/mcp

## Author

Onplana (Devsoft Solutions) , support@onplana.com

## License

MIT , see [LICENSE](LICENSE).
