# Nova by CIVAI plugin marketplace

Official multi-plugin marketplace for **24** Nova catalog MCP agents, for both Cursor and Claude Code.

Each plugin folder contains `.cursor-plugin/plugin.json`, `.claude-plugin/plugin.json`, `mcp.json`, `llms-install.md`, and `assets/logo.*`.

## Install in Claude Code

```bash
/plugin marketplace add civai-technologies/nova-catalog-mcp-plugins-by-nova-civai
/plugin install research-agent-by-nova@nova-by-civai
```

Each plugin prompts for a Nova API key on first enable; create one at https://nova.civai.co/api-keys.

| Resource | URL |
|----------|-----|
| Product | https://nova.civai.co |
| API keys | https://nova.civai.co/api-keys |
| Publisher | CIVAI TECHNOLOGIES |

## Plugins

See `.cursor-plugin/marketplace.json` or `.claude-plugin/marketplace.json` for the full list. In Cursor, configure `NOVA_API_KEY` per plugin under **Plugins, Configure**.

## License

MIT. See [LICENSE](LICENSE).
