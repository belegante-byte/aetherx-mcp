# Installing `aetherx-mcp`

MCP server that exposes the **Aether-X Port Congestion Oracle**: congestion score,
ETA delay, waiting vessels and freight volatility for 15 global ports.

- Repository: https://github.com/belegante-byte/aetherx-mcp
- PyPI: `aetherx-mcp` · Official MCP Registry: `io.github.belegante-byte/aetherx-mcp`

## Prerequisites

- Python 3.10+, **or** [`uv`](https://docs.astral.sh/uv/) (recommended — nothing to install globally)
- No API key required: the server calls the public production API directly.

## Install (stdio, recommended)

Nothing to build. Add the block below to the MCP client configuration and restart the client.

```json
{
  "mcpServers": {
    "aetherx-oracle": {
      "command": "uvx",
      "args": ["aetherx-mcp"]
    }
  }
}
```

Config file locations:

| Client | Path |
|---|---|
| Claude Desktop (macOS) | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| Claude Desktop (Windows) | `%APPDATA%\Claude\claude_desktop_config.json` |
| Cursor | `~/.cursor/mcp.json` |
| VS Code (Cline, Continue) | the client's own MCP settings JSON |

If `uvx` is unavailable, install the package and use the console script instead:

```bash
pip install aetherx-mcp
aetherx-mcp
```

## Alternative: remote endpoint (no install)

If the client supports Streamable HTTP, point it at:

```
https://aether-x-oracle-production.up.railway.app/mcp
```

No authentication required.

## Verify the installation

1. Call `list_supported_ports`. A successful response returns 15 objects with
   `port_id`, `port_name` and `country`.
2. Call `get_port_risk` with `{"port_id": "BRSSZ"}`. Expect `congestion_score`
   around `0.78`, `port_name` `"Santos"`.

## Tools

| Tool | Arguments | Returns |
|---|---|---|
| `get_port_risk` | `port_id` (UN/LOCODE string, e.g. `BRSSZ`) | One congestion payload |
| `get_ports_risk` | `port_ids` (array of strings) | One payload per port |
| `list_supported_ports` | none | The 15 pre-seeded ports |

## Optional configuration

| Variable | Default | Description |
|---|---|---|
| `RAPIDAPI_KEY` | unset | Route requests through the RapidAPI gateway (metered billing) |
| `RAPIDAPI_HOST` | `aether-x-port-congestion-oracle.p.rapidapi.com` | RapidAPI host |
| `AETHERX_BASE_URL` | production URL | Point the server at a different API base |

## Troubleshooting

- **`uvx: command not found`** → run `pip install uv`, or use `pip install aetherx-mcp`.
- **Empty tool list** → restart the MCP client after editing the config file.
- **Network error** → confirm the API is reachable:
  `curl "https://aether-x-oracle-production.up.railway.app/v1/port-risk?port_id=BRSSZ"`.
