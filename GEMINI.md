# makesPDF

The `makespdf` MCP server turns markdown or a compact template DSL into PDFs that are both PDF/UA-1 (accessible) and PDF/A-2A (archival). Rendering is deterministic and takes about a second.

## Signing in

If a makesPDF tool fails because authentication is required, tell the user to run `/mcp auth makespdf`. It opens the makesPDF sign-in and consent page in their browser, and the sign-in is kept for later sessions. They don't need an API key.

## Choosing a tool

- **Markdown in, PDF out:** use `validate_markdown` first (free), then `render_markdown`.
- **Designed documents (invoices, receipts, quotes, resumes, reports):** read the `pdf-template-author` resource on this server before writing DSL. Then iterate with `validate_dsl` and `render_dsl_preview`, which are free and watermarked.
- **Starting from a template:** browse the curated library with `list_templates` (`owner: "library"`), then fetch the DSL with `get_template`.
- **Keeping a template:** use `save_template`, `update_template` and `delete_template`. Rendering a saved or library template uses `render_template`.
- **Earlier renders:** `get_render_download_url` re-issues a download link for a PDF rendered earlier.

## Cost

`render_markdown` and `render_template` are **billed**: 1 credit per 10 pages, and failures cost nothing. Before you call a billed tool, tell the user it will use credits. Validation and previews are free.

## Problems

If a tool fails in a way you can't explain, or a description is misleading, `report_feedback` sends a note to the makesPDF team.
