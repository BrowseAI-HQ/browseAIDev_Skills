---
name: browse-deep-dive
description: Multi-step agentic deep research with reasoning chains and confidence progression. Use when the user needs a comprehensive, thorough investigation into a complex topic.
---

# BrowseAI Dev — Deep Dive Research

Use this skill when the user needs a comprehensive, multi-step investigation into a complex topic — not a quick answer, but a thorough analysis with reasoning chains and progressive confidence building.

## When to Use

- User says "deep dive into", "research thoroughly", "investigate", "I need a comprehensive analysis"
- User asks about a complex topic that requires multiple angles of research
- User needs to understand nuances, tradeoffs, or evolving situations
- Topic is controversial, technical, or requires cross-referencing multiple domains
- User explicitly wants the most thorough answer possible

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

Or set `BROWSE_API_KEY=bai_xxx` for full premium pipeline (NLI reranking, multi-provider search, counter-query verification).

## Workflow

### Step 1: Run Deep Research

Use `browse_answer` with `depth: "deep"` for the full agentic research pipeline:

```
browse_answer({
  query: "What are the real-world performance implications of adopting microservices vs monoliths in 2025?",
  depth: "deep"
})
```

Deep mode runs an iterative confidence-gated loop (up to 3 passes) with per-claim evidence retrieval and counter-query adversarial verification. Expect 15-30 seconds for results.

### Step 2: Interpret the Reasoning Chain

The response includes a `trace[]` array showing each pipeline step. Present the reasoning chain:

- **Search passes**: How many search iterations were needed
- **Claim count progression**: How claims were refined across passes
- **Confidence progression**: How confidence improved (e.g., 0.45 -> 0.62 -> 0.78)
- **Counter-queries**: What adversarial queries were used to stress-test claims

### Step 3: Present Claims and Evidence

For each claim in the response:

1. Show the claim text with its verification status and consensus level
2. Link to supporting sources with domain authority scores
3. Highlight any claims that were boosted or penalized during multi-pass consistency checks
4. Surface contradictions explicitly — deep mode is designed to find them

### Step 4: Summarize the Analysis

Present a structured summary:

1. **Key findings**: The strongest, most well-supported conclusions
2. **Contested points**: Where sources disagree and why
3. **Confidence assessment**: Overall confidence with explanation of what drove it up or down
4. **Evidence gaps**: What the research could not definitively answer

### Example

User: "I need a comprehensive analysis of whether remote work actually hurts productivity."

```
browse_answer({
  query: "Does remote work reduce productivity compared to in-office work? What does the research evidence show across different industries and roles?",
  depth: "deep"
})
```

Present the reasoning chain:
> Pass 1: Found 12 claims from 8 sources (confidence: 0.52)
> Pass 2: Targeted weak claims — retrieved 4 new sources (confidence: 0.68)
> Pass 3: Counter-query "evidence remote work increases productivity" — 2 claims penalized (confidence: 0.71)

Then present findings grouped by strength of evidence, with contradictions surfaced.

## Tips

- Frame queries as specific, answerable research questions — not vague topics
- Include temporal context: "in 2025" or "since GPT-4 launched" helps retrieve current evidence
- Deep mode costs 3x premium quota — use it for questions that genuinely need depth
- Expect 15-30 seconds — set user expectations accordingly
- If confidence remains below 50% after deep research, the topic likely has genuinely limited or conflicting evidence — say so
- For follow-up questions on the same topic, consider using sessions (`browse_session_create`) to build on prior findings

## Links

- [BrowseAI Dev](https://browseai.dev)
- [Documentation](https://browseai.dev/docs)
- [MCP Server](https://www.npmjs.com/package/browseai-dev)
- [GitHub](https://github.com/BrowseAI-HQ/BrowseAI-Dev)
