# Enhanciar MCP server

**Ask it anything. Hand it the follow-up.**

Enhanciar is a company brain. It reads your Slack, Gmail, Google Drive, Notion, Jira, Linear, GitHub, Zendesk and Postgres, answers any question with a link to where the answer came from, then drafts the reply, ticket or doc for a person to approve.

This repo documents the **hosted remote MCP server**, so Claude, Cursor, VS Code or any MCP client can use the same brain your team uses in the app. There is nothing to install or run: you point your client at a URL and add an API key.

- Website: https://enhanciar.in
- MCP docs: https://enhanciar.in/mcp
- Official MCP Registry: `in.enhanciar/enhanciar`

## Endpoint

| | |
|---|---|
| Transport | Streamable HTTP (MCP 2025-11-25) |
| URL | `https://enhanciar.in/enhanciar/mcp/` |
| Auth header | `Authorization: Bearer dh_<your key>` |
| Workspace | Chosen when you create the key. One key = one workspace. |

## Get an API key

1. Sign in at https://enhanciar.in (paid plans: Solo $24/mo, Team $100/mo; you bring your own AI key).
2. Open **Settings → API keys** and create a key. It starts with `dh_`.
3. **Settings → Connections** also has copy-paste snippets for each client with your key filled in.

## Set-up

### Claude Code

```bash
claude mcp add --transport http --scope user enhanciar \
  https://enhanciar.in/enhanciar/mcp/ \
  --header "Authorization: Bearer dh_your_key"
```

### Claude Desktop

Claude Desktop's Connectors UI can't send a custom header, so bridge it with `mcp-remote` in `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "enhanciar": {
      "command": "npx",
      "args": [
        "-y", "mcp-remote",
        "https://enhanciar.in/enhanciar/mcp/",
        "--header", "Authorization: Bearer dh_your_key"
      ]
    }
  }
}
```

### Cursor

**Settings → Tools & Integrations → New MCP server**, or add to `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "enhanciar": {
      "url": "https://enhanciar.in/enhanciar/mcp/",
      "headers": { "Authorization": "Bearer dh_your_key" }
    }
  }
}
```

### VS Code (GitHub Copilot agent mode)

Add to `.vscode/mcp.json`:

```json
{
  "inputs": [
    { "type": "promptString", "id": "enhanciar-key", "description": "Enhanciar API key (dh_...)", "password": true }
  ],
  "servers": {
    "enhanciar": {
      "type": "http",
      "url": "https://enhanciar.in/enhanciar/mcp/",
      "headers": { "Authorization": "Bearer ${input:enhanciar-key}" }
    }
  }
}
```

## Tools

| Tool | What it does |
|---|---|
| `query` | Ask a question in plain language. Returns an answer with a citation on every claim (Slack permalink, doc paragraph, file:line). |
| `search_wiki` | Search the workspace wiki. |
| `get_page` / `list_pages` | Read one wiki page in full; list every page the key can see. |
| `impact` | Blast radius of changing a file, function or module: callers, callees, affected tests and pages. |
| `get_graph` | The entity / community graph manifest. |
| `get_process_map` | How a process actually runs across code, people and tools. |
| `list_repos` | Repositories the workspace has ingested. |
| `list_skills` / `get_skill` | Reusable skills distilled from the codebase. |
| `propose_action` / `list_proposed_actions` | Draft a Jira, Linear, GitHub, Slack, Gmail or calendar action into the approval queue, and list what's waiting. |

## What it can never do

- **Execute an action.** The server can only propose. A person approves or rejects in the app.
- **Reach beyond you.** A key is tied to you and one workspace, and you only see what you already have access to. Leave the workspace and the key stops working on the next request.
- **Mark up your tokens.** Model calls run on your own AI key (Gemini, OpenAI or Anthropic).

## Example prompts

- "Can this customer still get a refund? Draft the reply."
- "What did we promise Acme in the last call?"
- "Who owns billing, and where is that written down?"
- "What breaks if I change `payments/webhook.py`?"
- "Turn yesterday's meeting notes into Linear tickets."

## Registry entry

[`server.json`](server.json) is the entry published to the [Official MCP Registry](https://registry.modelcontextprotocol.io) as `in.enhanciar/enhanciar`.

## Support

support@enhanciar.in

## License

MIT for the contents of this repo (docs and config). The Enhanciar service itself is a hosted product.
