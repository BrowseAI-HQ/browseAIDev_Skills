---
name: browse-compare-claims
description: Settle factual disputes by comparing evidence-backed answers against raw LLM output. Use when the user wants to know which of competing claims is correct.
---

# BrowseAI Dev — Compare Claims & Settle Disputes

Use this skill when the user wants to settle a factual dispute, compare competing claims, or see where evidence-backed research differs from what an LLM would say unprompted.

## When to Use

- User says "is X true or Y?", "which is correct", "settle this debate", "compare these claims"
- User presents two conflicting statements and wants to know which has more evidence
- User wants to see how much an LLM hallucination-prone answer differs from sourced research
- User is evaluating competing narratives on a topic

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

## Workflow

### Step 1: Run the Comparison

Use `browse_compare` to get both a raw LLM answer and an evidence-backed answer:

```
browse_compare({
  query: "Is coffee good or bad for heart health?"
})
```

The response contains:
- **raw_llm**: What an LLM says without sources — no citations, no confidence score
- **evidence_backed**: Researched answer with claims, sources, confidence, and contradictions

### Step 2: Build the Comparison Table

Present findings as a structured side-by-side comparison:

| Aspect | Raw LLM | Evidence-Backed |
|--------|---------|-----------------|
| Main claim | [summary] | [summary] |
| Sources cited | 0 | [count] |
| Confidence | N/A | [score]% |
| Nuance | [level] | [level] |
| Contradictions noted | [yes/no] | [yes/no] |

### Step 3: Highlight Agreement and Disagreement

Walk through the key points:

1. **Where they agree**: Claims present in both answers — these are likely well-established facts
2. **Where they diverge**: Claims only in one answer — the evidence-backed version is more reliable here
3. **What the LLM missed**: Nuances, contradictions, or caveats that only appeared with real source verification
4. **Confidence delta**: How much the evidence actually supports the LLM's confident-sounding claims

### Step 4: Deliver the Verdict

Based on the comparison:

- If evidence strongly supports one side: state it clearly with source count and confidence
- If evidence is mixed: present both sides with their relative strength
- If the LLM was broadly correct but missed nuance: acknowledge accuracy while highlighting gaps
- Always let the evidence speak — cite specific sources for key claims

### Example

User: "People say you should stretch before exercise, but I heard that's wrong. Which is correct?"

```
browse_compare({
  query: "Should you stretch before exercise? Is static stretching before workouts helpful or harmful?"
})
```

Present the comparison:
> **Raw LLM**: "Stretching before exercise is generally recommended to prevent injury..."
> **Evidence-Backed** (74% confidence, 6 sources): "Research distinguishes between static and dynamic stretching. Static stretching before exercise may reduce performance (source: ACSM), while dynamic stretching improves outcomes (source: BJSM). The blanket advice to 'stretch before exercise' oversimplifies the evidence."
>
> The LLM missed the critical static vs. dynamic distinction that sources consistently highlight.

## Tips

- Frame the dispute clearly — include both sides in the query for best results
- Works best for factual disputes, not opinion or preference questions
- The raw LLM answer exposes common misconceptions — useful for educational content
- For deeper investigation of the winning claim, follow up with `browse_answer` using `depth: "thorough"`
- Low confidence on both sides means the topic is genuinely unsettled — say so

## Links

- [BrowseAI Dev](https://browseai.dev)
- [Documentation](https://browseai.dev/docs)
- [MCP Server](https://www.npmjs.com/package/browseai-dev)
- [GitHub](https://github.com/BrowseAI-HQ/BrowseAI-Dev)
