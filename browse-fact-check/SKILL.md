---
name: browse-fact-check
description: Fact-check claims and detect hallucinations by comparing raw LLM answers against evidence-backed research. Use when the user wants to verify claims or check accuracy.
---

# BrowseAI Dev — Fact Check & Hallucination Detection

Use this skill when the user wants to verify claims, detect hallucinations, or compare what an LLM says vs what evidence actually shows.

## When to Use

- User says "is this true?", "verify this", "fact check", "can you confirm?"
- User questions the accuracy of something an AI said
- User wants to compare a claim against real sources
- User is debugging misinformation or checking controversial statements
- You want to verify your own output before presenting it to the user

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

### Option A: Side-by-Side Comparison

Use `browse_compare` to show raw LLM answer vs evidence-backed answer:

```
browse_compare({ query: "Is nuclear energy the safest form of energy?" })
```

The response contains:
- **raw_llm**: What an LLM says without any sources (0 citations, no confidence)
- **evidence_backed**: Researched answer with sources, claims, confidence score, and citations

Present both side-by-side so the user can see the difference.

### Option B: Verify a Specific Claim

Use `browse_answer` with a targeted verification query:

```
browse_answer({
  query: "Is it true that [specific claim]? What does the evidence say?",
  depth: "thorough"
})
```

Check the response:
- **claims[].verified**: Whether each claim was verified against source text
- **claims[].verificationScore**: How strongly the source text supports the claim (0-1)
- **claims[].consensusLevel**: "strong", "moderate", "weak", or "none"
- **contradictions[]**: Any sources that directly contradict the claim

### Step: Present Findings

When fact-checking for the user:

1. State whether the claim is **supported**, **partially supported**, **contradicted**, or **unverifiable**
2. Cite the specific sources that support or contradict
3. If `browse_compare` was used, highlight the key differences between raw LLM and evidence-backed answers
4. Note the confidence score and number of corroborating sources
5. Surface any contradictions explicitly

### Example

User: "I read that drinking 8 glasses of water a day is medically necessary. Is that true?"

```
browse_answer({
  query: "Is the recommendation to drink 8 glasses of water per day supported by medical evidence?",
  depth: "thorough"
})
```

If contradictions are found, present them:
> "Sources disagree: Mayo Clinic recommends adequate hydration but notes individual needs vary, while a 2002 study in AJP found no scientific basis for the '8 glasses' rule."

## Tips

- Frame verification queries as specific questions, not statements
- Use `depth: "thorough"` for fact-checking — it auto-retries with rephrased queries to find more evidence
- For nuanced topics, `browse_compare` is the most effective tool — it shows the gap between hallucination-prone and evidence-backed answers
- Low confidence (< 50%) doesn't mean the claim is false — it may mean there's limited evidence available

## Links

- [BrowseAI Dev](https://browseai.dev)
- [Documentation](https://browseai.dev/docs)
- [MCP Server](https://www.npmjs.com/package/browseai-dev)
- [GitHub](https://github.com/BrowseAI-HQ/BrowseAI-Dev)
