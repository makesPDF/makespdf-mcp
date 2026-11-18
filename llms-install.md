# makesPDF MCP server - installation guide for AI agents

makesPDF is a **remote** MCP server. There is no package to download, build, or run - you connect your MCP client directly to the hosted endpoint.

## Requirements

1. A makesPDF account. The human you are assisting can create one at https://makespdf.com.
2. An MCP client that supports remote servers over streamable HTTP. Most clients sign in by URL with OAuth (Gemini CLI, Antigravity, Claude Code, claude.ai, Claude Desktop, Cursor, VS Code 1.106+). Clients without OAuth send an API key, which the human creates at Settings -> API keys (it looks like `mpdf_...`).

## Installation

**Gemini CLI.** Install this repo as an extension, then have the human run `/mcp auth makespdf` once to sign in through their browser:

```sh
gemini extensions install https://github.com/makesPDF/makespdf-mcp
```

**Antigravity.** Install this repo as a plugin. Antigravity opens the makesPDF sign-in in the browser when the server first connects:

```sh
agy plugin install https://github.com/makesPDF/makespdf-mcp
```

**Clients that sign in by URL.** Add `https://makespdf.com/api/v1/mcp` with no headers. The client opens the makesPDF sign-in page the first time a tool needs it. Per-client configs: https://makespdf.com/docs/mcp

**API key.** Ask the human for their key, or have them set it as the environment variable `MAKESPDF_API_KEY`. Never hard-code the key into shared or committed files.

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

## Verify

List tools in your client. You should see 12 tools, including `validate_markdown`, `render_markdown`, `validate_dsl`, `render_dsl_preview` and `render_template`.

A good first call is `render_dsl_preview`: it is free and needs no saved state.

## Important notes for agents

- `render_markdown` and `render_template` are **billed** at 1 credit per 10 pages. Confirm with the human before calling them; every failure path bills zero credits.
- `render_dsl_preview` and both `validate_*` tools are free - use them to iterate.
- The full DSL authoring reference is available as an MCP resource on the server, and at https://makespdf.com/skills/pdf-template-author.md - read it before writing DSL.
