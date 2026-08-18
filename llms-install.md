# makesPDF MCP server - installation guide for AI agents

makesPDF is a **remote** MCP server. There is no package to download, build, or run - you connect your MCP client directly to the hosted endpoint.

## Requirements

1. A makesPDF account and API key. The human you are assisting can create both at https://makespdf.com (sign up, then Settings -> API keys). The key looks like `mpdf_...`.
2. An MCP client that supports remote servers over streamable HTTP with custom headers (Claude Code, Claude Desktop, Cline, Cursor, and most others).

## Installation

Ask the human for their makesPDF API key (or have them set it as the environment variable `MAKESPDF_API_KEY`). Never hard-code the key into shared or committed files.

Then add this server configuration:

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

For Claude Code specifically:

```sh
claude mcp add --transport http makespdf \
  https://makespdf.com/api/v1/mcp \
  --header "Authorization: Bearer $MAKESPDF_API_KEY"
```

## Verify

Call the `tools/list` method (or simply list tools in your client). You should see 9 tools: `validate_dsl`, `validate_markdown`, `render_dsl_preview`, `render_template`, `save_template`, `list_templates`, `get_template`, `update_template`, `delete_template`.

A good first call is `render_dsl_preview` - it is free and needs no saved state.

## Important notes for agents

- `render_template` is **billed** at 1 credit per 10 pages. Confirm with the human before calling it; every failure path bills zero credits.
- `render_dsl_preview` and both `validate_*` tools are free - use them to iterate.
- The full DSL authoring reference is available as an MCP resource on the server, and at https://makespdf.com/skills/pdf-template-author.md - read it before writing DSL.
- Rate limits match the REST API (renders share a 200/hour bucket).
