# makesPDF MCP server

Generate compliant PDFs (PDF/A-2A + PDF/UA-1) from markdown or a compact DSL - about a second end to end, no headless browser.

This repo documents the **hosted (remote) MCP server** at `https://makespdf.com/api/v1/mcp`. There is nothing to install or run locally - point your MCP client at the endpoint, sign in with your makesPDF account (or send an API key), and you get schema-validated tools for the full author -> render loop.

- Docs: https://makespdf.com/docs/mcp?ref=gh-makespdf-mcp
- Skill file (also exposed as an MCP resource): https://makespdf.com/skills/pdf-template-author.md
- CLI (REST wrapper, npm): https://github.com/makesPDF/makespdf-cli

## What it does

**makesPDF** is a deterministic PDF rendering API built for AI agents. Every PDF is PDF/A-2A archival + PDF/UA-1 accessibility dual-compliant out of the box, validated against veraPDF (the ISO reference validator) on every release. Rendering is pure computation - no AI call, no headless browser - so cold and warm renders both land near a second.

The MCP server exposes:

- Validation of markdown or DSL templates (catalog + accessibility checks) before rendering.
- Free, watermarked previews of DSL templates with sample data.
- Template persistence: save, list, fetch, update, delete.
- Billed rendering of markdown and of saved or library templates (1 credit per 10 pages); the result reports `creditsDeducted` + `creditsRemaining` so the agent can surface cost back to the human.
- Browsing a curated template library (invoices, receipts, quotes, resumes, etc.) and forking its DSL.

## Connect

Most clients sign in by URL with your makesPDF account (OAuth), so there's no key to copy. An API key sent as a Bearer header is the alternative for CI, scripts and clients without OAuth. Create a key at https://makespdf.com (Settings -> API keys). Full setup for every client: https://makespdf.com/docs/mcp

### Gemini CLI

This repo is a Gemini CLI extension (users on a personal Google account have moved to Antigravity, below):

```sh
gemini extensions install https://github.com/makesPDF/makespdf-mcp
```

Then run `/mcp auth makespdf` once in a Gemini CLI session to sign in through your browser.

### Antigravity

This repo is also an Antigravity plugin:

```sh
agy plugin install https://github.com/makesPDF/makespdf-mcp
```

Antigravity opens the makesPDF sign-in in your browser when the server first connects. You can also sign in yourself: open `/plugin`, expand makespdf under Installed, and choose **Sign in**. In the Antigravity app, add the plugin from Customizations.

### Claude Code

```sh
claude mcp add --transport http makespdf https://makespdf.com/api/v1/mcp
```

Run `/mcp` in a session and choose **Authenticate**. To use a key instead, add `--header "Authorization: Bearer $MAKESPDF_API_KEY"`.

### claude.ai and Claude Desktop

Customize -> Connectors -> Add custom connector, with `https://makespdf.com/api/v1/mcp` as the URL and no headers. Claude shows a **Connect** button the first time it uses makesPDF.

### Cursor (`~/.cursor/mcp.json` or `.cursor/mcp.json`)

```json
{
  "mcpServers": {
    "makespdf": {
      "url": "https://makespdf.com/api/v1/mcp",
      "auth": {
        "CLIENT_ID": "https://cursor.com/oauth/mcp-client.json"
      }
    }
  }
}
```

### VS Code 1.106+ (`.vscode/mcp.json`)

```json
{
  "servers": {
    "makespdf": {
      "type": "http",
      "url": "https://makespdf.com/api/v1/mcp"
    }
  }
}
```

### API key (any client)

```json
{
  "mcpServers": {
    "makespdf": {
      "url": "https://makespdf.com/api/v1/mcp",
      "headers": {
        "Authorization": "Bearer ${env:MAKESPDF_API_KEY}"
      }
    }
  }
}
```

```sh
curl -X POST https://makespdf.com/api/v1/mcp \
  -H "Authorization: Bearer $MAKESPDF_API_KEY" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json, text/event-stream" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'
```

## Tools

| Tool                      | Purpose                                                                         |
| ------------------------- | ------------------------------------------------------------------------------- |
| `validate_markdown`       | Heading hierarchy + alt-text check for markdown. Free.                          |
| `render_markdown`         | **Billed** render of markdown to PDF (1 credit / 10 pages).                     |
| `validate_dsl`            | Catalog + accessibility check of a DSL template, no render. Free.               |
| `render_dsl_preview`      | Preview inline DSL with sample data. Free, watermarked.                         |
| `render_template`         | **Billed** render of a saved or library template (1 credit / 10 pages).         |
| `save_template`           | Save a DSL template you own.                                                    |
| `list_templates`          | List your templates, or the curated library with `owner: "library"`.            |
| `get_template`            | Fetch one template (DSL + metadata).                                            |
| `update_template`         | Update the DSL, name, description or sample data of a template you own.         |
| `delete_template`         | Delete a template you own.                                                      |
| `get_render_download_url` | Re-issue a download link for a PDF you rendered earlier.                        |
| `report_feedback`         | Send feedback about the server to the makesPDF team.                            |

Twelve tools. Listing tools needs no sign-in, so you can check what's on offer before connecting. Failed renders cost nothing.

## License

The contents of this repository (documentation and configuration snippets) are MIT-licensed. makesPDF itself is a hosted, closed-source service - see https://makespdf.com for terms.
