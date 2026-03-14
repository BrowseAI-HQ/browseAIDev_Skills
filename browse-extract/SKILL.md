---
name: browse-extract
description: Extract structured claims, quotes, and knowledge from any URL. Use when the user shares a link and wants key points, pricing, specs, or a focused summary.
---

# BrowseAI Dev — Structured Data Extraction

Use this skill when the user has a URL and wants structured claims, quotes, or knowledge extracted from it.

## When to Use

- User shares a URL and says "summarize this", "what does this say about X?", "extract the key points"
- User wants structured data from a web page (pricing, specs, features, arguments)
- User wants to pull specific information from an article or documentation page
- User needs clean text from a web page (stripping ads, nav, boilerplate)

## Prerequisites

Install BrowseAI Dev MCP server:

```json
{
  "mcpServers": {
    "browse-ai": {
      "command": "npx",
      "args": ["-y", "browse-ai"]
    }
  }
}
```

## Workflow

### Option A: Extract Structured Claims from a URL

Use `browse_extract` when the user wants knowledge extracted from a specific page:

```
browse_extract({
  url: "https://example.com/article",
  query: "What are the pricing tiers?"
})
```

The response contains:
- **answer**: Synthesized answer from the page content
- **claims[]**: Structured claims with source attribution
- **sources[]**: Source details with exact quotes from the page
- **confidence**: How well the page content answers the query

The `query` parameter focuses extraction — use it to target specific information rather than getting a generic summary.

### Option B: Fetch Clean Text from a URL

Use `browse_open` when the user just needs the page content as clean text:

```
browse_open({ url: "https://example.com/docs" })
```

Returns:
- **title**: Page title
- **content**: Clean text content (ads, nav, boilerplate stripped via Readability)
- **url**: The fetched URL

Use this when you need raw text for your own processing, or when the user wants to read a page without noise.

### Step: Present Extracted Information

When presenting extracted data:

1. Lead with the structured claims, not a wall of text
2. Include direct quotes from `sources[].quote` for credibility
3. Note the source domain and its authority level
4. If the query didn't match the page well (low confidence), say so

### Example

User: "What are the key arguments in this article? https://example.com/ai-regulation"

```
browse_extract({
  url: "https://example.com/ai-regulation",
  query: "What are the main arguments for and against AI regulation?"
})
```

Present each claim with its supporting quote from the page.

## Tips

- Always include a `query` parameter with `browse_extract` — it focuses the extraction and produces better results
- Use `browse_open` for documentation pages where you need the full text
- If the URL is behind a paywall or requires auth, the extraction may fail — let the user know
- For extracting from multiple URLs, call `browse_extract` for each one separately
- Combine with `browse_search` to first find relevant URLs, then extract from the best ones

## Links

- [BrowseAI Dev](https://browseai.dev)
- [Documentation](https://browseai.dev/docs)
- [MCP Server](https://www.npmjs.com/package/browse-ai)
- [GitHub](https://github.com/BrowseAI-HQ/BrowseAI-Dev)
