---
name: browse-cite
description: Generate properly formatted citations with source authority scores. Use when the user needs references, bibliographies, or sourced quotes for academic or professional writing.
---

# BrowseAI Dev — Citations & Source References

Use this skill when the user needs properly formatted citations, source references, or a bibliography backed by real web sources with authority scores.

## When to Use

- User says "cite this", "get citations for", "find sources for", "I need references"
- User is writing a paper, report, or article and needs sourced claims
- User wants to build a bibliography or reference list on a topic
- User needs to quote specific passages from authoritative sources
- User asks for APA, MLA, Chicago, or inline citation formatting

## Prerequisites

Install BrowseAI Dev MCP server:

```json
{
  "mcpServers": {
    "browseai-dev": {
      "command": "npx",
      "args": ["-y", "browseai-dev"]
    }
  }
}
```

Or set `BROWSE_API_KEY=bai_xxx` for full premium pipeline with domain authority scoring.

## Workflow

### Step 1: Research with Thorough Depth

Use `browse_answer` with `depth: "thorough"` to maximize source quality and claim verification:

```
browse_answer({
  query: "What are the cognitive benefits of bilingualism according to peer-reviewed research?",
  depth: "thorough"
})
```

Thorough mode auto-retries with rephrased queries if confidence is low, yielding more diverse and higher-quality sources.

### Step 2: Extract Citation Data

From the response, extract citation-relevant fields:

- **sources[]**: Each source contains `url`, `title`, `domain`, `quote`, and `authorityScore`
- **claims[]**: Each claim contains `text`, `sources` (URLs), `verified`, `verificationScore`, `consensusLevel`
- **confidence**: Overall confidence score for the answer

Prioritize sources with:
- High `authorityScore` (0.8+ indicates authoritative domains like .edu, .gov, major publications)
- `verified: true` claims with `consensusLevel: "strong"` or `"moderate"`
- Direct quotes in the `quote` field

### Step 3: Format Citations

Format based on the user's requested style. Use the source data to build proper citations:

**APA Style:**
> Author/Organization. (Year). *Title*. Site Name. URL
>
> National Institutes of Health. (2024). *Cognitive effects of bilingualism: A systematic review*. NIH.gov. https://...

**MLA Style:**
> "Title." *Site Name*, Publisher, Date. URL.
>
> "Cognitive Effects of Bilingualism." *NIH.gov*, National Institutes of Health, 2024. https://...

**Inline Citations:**
> Bilingual individuals show enhanced executive function (NIH, 2024; authorityScore: 0.92) and delayed onset of dementia symptoms by 4-5 years (Bialystok et al., via Psychology Today; authorityScore: 0.71).

### Step 4: Present the Cited Research

Structure the output for the user:

1. **Summary**: The researched answer with inline citations
2. **Key claims with sources**: Each claim paired with its supporting source, verification status, and authority score
3. **Source quality assessment**: Note which sources are most authoritative and why
4. **Bibliography**: Formatted reference list in the requested citation style
5. **Quoted passages**: Direct quotes from sources (from the `quote` field) that the user can use verbatim

### Example

User: "I need cited sources about the environmental impact of fast fashion for a research paper."

```
browse_answer({
  query: "What is the environmental impact of fast fashion? Include statistics on water usage, carbon emissions, and textile waste from authoritative sources.",
  depth: "thorough"
})
```

Present as:
> **Cited findings:**
>
> 1. The fashion industry accounts for 10% of global carbon emissions (UNEP, 2024; authority: 0.95) [1]
> 2. A single cotton t-shirt requires 2,700 liters of water to produce (WWF, 2023; authority: 0.91) [2]
> 3. 85% of textiles end up in landfills annually (EPA, 2024; authority: 0.93) [3]
>
> **References (APA):**
> [1] United Nations Environment Programme. (2024). *Fashion's Environmental Impact*. UNEP.org. https://...
> [2] World Wildlife Fund. (2023). *Cotton: A Water-Intensive Crop*. WWF.org. https://...
> [3] Environmental Protection Agency. (2024). *Textiles: Material-Specific Data*. EPA.gov. https://...
>
> **Source quality:** All three primary sources are from high-authority domains (0.91+). Confidence: 82%.

## Tips

- Always ask the user which citation format they need (APA, MLA, Chicago, inline) — default to inline if not specified
- Use `depth: "thorough"` for citation work — it yields more sources and higher verification rates
- Prioritize sources with high authority scores (0.8+) for academic writing
- The `quote` field contains verbatim text from the source — safe to use as direct quotes
- For comprehensive bibliographies, run multiple queries on subtopics of the same research area
- Pair with sessions (`browse_session_create`) when building citations across multiple research questions
- Low authority scores (< 0.5) should be flagged — suggest the user find a more authoritative source

## Links

- [BrowseAI Dev](https://browseai.dev)
- [Documentation](https://browseai.dev/docs)
- [MCP Server](https://www.npmjs.com/package/browseai-dev)
- [GitHub](https://github.com/BrowseAI-HQ/BrowseAI-Dev)
