# Leads Discovery Agent by Nova (CIVAI)

I discover new leads from WhatsApp/Telegram groups, Instagram post commenters, and X thread repliers, then import them into your Support Agent CRM

**Keywords:** mcp-server, model-context-protocol, nova-by-civai, remote-mcp, streamable-http, civai, cursor, claude-desktop, lead-generation, prospecting, x-scraper, sales-leads

Remote **Model Context Protocol (MCP)** server from **Nova by CIVAI**. Connect from **Cursor**, **Claude Desktop**, **Claude Code**, or any MCP client that supports **Streamable HTTP**.

## Links

| Resource | URL |
|----------|-----|
| MCP endpoint | https://nova.civai.co/mcp/agents/leads-discovery-agent |
| Agent page | https://nova.civai.co/agent/leads-discovery-agent |
| API keys | https://nova.civai.co/api-keys |
| Official Registry | `co.civai.nova/leads-discovery-agent` |

## Prerequisites

1. Sign in at [nova.civai.co/api-keys](https://nova.civai.co/api-keys)
2. Create a **programmatic Nova API key** (JWT)
3. Add the server to your MCP client with `Authorization: Bearer <your_nova_jwt>`

## Cursor Marketplace plugin

This repository ships a **Cursor Plugin** manifest (`.cursor-plugin/plugin.json` + root `mcp.json`) for one-click install after marketplace review. Users configure `NOVA_API_KEY` in Cursor **Plugins → Configure** (never commit secrets).

Local test: copy this folder to `~/.cursor/plugins/local/leads-discovery-agent-by-nova`, reload the window, set `NOVA_API_KEY`, verify MCP tools load.

## Cursor (manual MCP)

Add to `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "leads-discovery-agent-by-nova": {
      "url": "https://nova.civai.co/mcp/agents/leads-discovery-agent",
      "headers": {
        "Authorization": "Bearer YOUR_NOVA_API_KEY"
      }
    }
  }
}
```

## Claude Code

Install as a plugin from the Nova marketplace, which prompts for your API key on first enable:

```bash
/plugin marketplace add civai-technologies/nova-catalog-mcp-plugins-by-nova-civai
/plugin install leads-discovery-agent-by-nova --marketplace civai-technologies/nova-catalog-mcp-plugins-by-nova-civai
```

Or add the MCP server directly:

```bash
claude mcp add --transport http leads-discovery-agent-by-nova \
  https://nova.civai.co/mcp/agents/leads-discovery-agent \
  --header "Authorization: Bearer $NOVA_API_KEY"
```

Setting this up on someone's behalf? [`llms-install.md`](./llms-install.md) is written for that.

## Transport

- **Type:** Streamable HTTP (stateless)
- **Auth:** Bearer JWT or `X-API-Key` header
- **Tools:** 22 (bundled catalog)

## About Nova by CIVAI

**Nova by CIVAI** is a platform for running, building, and deploying AI agents. This MCP server exposes **Leads Discovery Agent** tools to external MCP hosts without sharing upstream credentials.

| | |
|---|---|
| **Company** | CIVAI TECHNOLOGIES |
| **Creator** | [Kayode Femi Amoo](https://x.com/usecodenaija) |
| **Product** | [Nova by CIVAI](https://nova.civai.co) |

## License

MIT. See [LICENSE](LICENSE).
