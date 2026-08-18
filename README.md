# makesPDF MCP server

Generate compliant PDFs (PDF/A-2A + PDF/UA-1) from markdown or a compact DSL - about a second end to end, no headless browser.

This repo documents the **hosted (remote) MCP server** at `https://makespdf.com/api/v1/mcp`. There is nothing to install or run locally - point your MCP client at the endpoint with an API key and you get schema-validated tools for the full author -> render loop.

- Docs: https://makespdf.com/docs/mcp?ref=gh-makespdf-mcp
- Skill file (also exposed as an MCP resource): https://makespdf.com/skills/pdf-template-author.md
- CLI (REST wrapper, npm): https://github.com/makesPDF/makespdf-cli

## What it does

**makesPDF** is a deterministic PDF rendering API built for AI agents. Every PDF is PDF/A-2A archival + PDF/UA-1 accessibility dual-compliant out of the box, validated against veraPDF (the ISO reference validator) on every release. Rendering is pure computation - no AI call, no headless browser - so cold and warm renders both land near a second.

The MCP server exposes:

- Validation of markdown or DSL templates (catalog + accessibility checks) before rendering.
- Free, watermarked previews of DSL templates with sample data.
- Template persistence: save, list, fetch, update, delete.
- Billed rendering of saved templates (1 credit per 10 pages); the result reports `creditsDeducted` + `creditsRemaining` so the agent can surface cost back to the human.
- Browsing a curated template library (invoices, receipts, quotes, resumes, etc.) and forking its DSL.

## Connect

Auth is a Bearer API key - the same key as the REST API. Create one at https://makespdf.com (Settings -> API keys).

### Claude Code

```sh
claude mcp add --transport http makespdf \
  https://makespdf.com/api/v1/mcp \
  --header "Authorization: Bearer $MAKESPDF_API_KEY"
```

### Claude Desktop / generic `.mcp.json`

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

### Cursor (`~/.cursor/mcp.json` or `.cursor/mcp.json`)

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

### Raw HTTP (any client)

```sh
curl -X POST https://makespdf.com/api/v1/mcp \
  -H "Authorization: Bearer $MAKESPDF_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'
```

## Tools

| Tool                 | Purpose                                                      |
| -------------------- | ------------------------------------------------------------ |
| `validate_dsl`       | Catalog + accessibility check of a DSL template, no render.  |
| `validate_markdown`  | Heading hierarchy + alt-text check for markdown.             |
| `render_dsl_preview` | Preview inline DSL with sample data; free, watermarked.      |
| `render_template`    | **Billed** render of a saved template (1 credit / 10 pages). |
| `save_template`      | Persist a user-owned DSL template.                           |
| `list_templates`     | List the caller's templates (or the curated library with `owner: "library"`). |
| `get_template`       | Fetch one template (DSL + metadata).                         |
| `update_template`    | Update DSL or name on an owned template.                     |
| `delete_template`    | Delete an owned template.                                    |

Nine tools total. Library browsing goes through `list_templates` with `owner: "library"` plus `get_template` (library rows are world-readable).

## License

The contents of this repository (documentation and configuration snippets) are MIT-licensed. makesPDF itself is a hosted, closed-source service - see https://makespdf.com for terms.
