# Slidegen Agent by Nova (CIVAI)

I do everything related to image and slide asset generation

**Keywords:** mcp-server, model-context-protocol, nova-by-civai, remote-mcp, streamable-http, civai, cursor, claude-desktop, slides, image-generation, presentations

Remote **Model Context Protocol (MCP)** server from **Nova by CIVAI**. Connect from **Cursor**, **Claude Desktop**, **Claude Code**, or any MCP client that supports **Streamable HTTP**.

## Links

| Resource | URL |
|----------|-----|
| MCP endpoint | https://nova.civai.co/mcp/agents/slidegen-agent |
| Agent page | https://nova.civai.co/agent/slidegen-agent |
| API keys | https://nova.civai.co/api-keys |
| Official Registry | `co.civai.nova/slidegen-agent` |

## Prerequisites

1. Sign in at [nova.civai.co/api-keys](https://nova.civai.co/api-keys)
2. Create a **programmatic Nova API key** (JWT)
3. Add the server to your MCP client with `Authorization: Bearer <your_nova_jwt>`

## Cursor Marketplace plugin

This repository ships a **Cursor Plugin** manifest (`.cursor-plugin/plugin.json` + root `mcp.json`) for one-click install after marketplace review. Users configure `NOVA_API_KEY` in Cursor **Plugins → Configure** (never commit secrets).

Local test: copy this folder to `~/.cursor/plugins/local/slidegen-agent-by-nova`, reload the window, set `NOVA_API_KEY`, verify MCP tools load.

## Cursor (manual MCP)

Add to `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "slidegen-agent-by-nova": {
      "url": "https://nova.civai.co/mcp/agents/slidegen-agent",
      "headers": {
        "Authorization": "Bearer YOUR_NOVA_API_KEY"
      }
    }
  }
}
```

## Claude Code

```bash
claude mcp add --transport http slidegen-agent-by-nova \
  https://nova.civai.co/mcp/agents/slidegen-agent \
  --header "Authorization: Bearer $NOVA_API_KEY"
```

## Transport

- **Type:** Streamable HTTP (stateless)
- **Auth:** Bearer JWT or `X-API-Key` header
- **Tools:** 4 (bundled catalog)

## About Nova by CIVAI

**Nova by CIVAI** is a platform for running, building, and deploying AI agents. This MCP server exposes **Slidegen Agent** tools to external MCP hosts without sharing upstream credentials.

| | |
|---|---|
| **Company** | CIVAI TECHNOLOGIES |
| **Creator** | [Kayode Femi Amoo](https://x.com/usecodenaija) |
| **Product** | [Nova by CIVAI](https://nova.civai.co) |

## License

MIT. See [LICENSE](LICENSE).
