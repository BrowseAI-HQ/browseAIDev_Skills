---
name: browse-clarity
description: Anti-hallucination answer engine — generates answers with reduced hallucinations. Fast LLM-only mode or verified mode with web fusion. Use when accuracy matters.
---

# BrowseAI Dev — Clarity Anti-Hallucination Answer Engine

Use this skill when the user needs answers with reduced hallucinations. Clarity is an answer engine — it calls the LLM with anti-hallucination techniques and returns structured answers with claims, confidence, and risk analysis.

## When to Use

- User wants accurate, grounded answers with reduced hallucinations
- User says "make this more accurate", "reduce hallucinations", "stop making things up"
- User is building an AI agent or pipeline that needs factual accuracy
- User wants to verify LLM-generated content before publishing
- User wants a fast answer without internet (LLM-only mode)
- User wants the best of both LLM reasoning and web evidence (verified mode)
- Any task where factual accuracy is more important than creativity

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

## Two Modes

### Mode 1: Fast LLM-Only (`verify: false`)

No internet required. Uses anti-hallucination grounding techniques to get a better answer from the LLM alone.

```
browse_clarity({
  prompt: "What are the main causes of antibiotic resistance?",
  verify: false
})
```

**When to use:** Speed matters, offline is fine, you just want a more grounded LLM answer.

**Response includes:**
- `answer` — The LLM's grounded answer
- `claims[]` — Individual claims (all with `origin: "llm"`)
- `confidence` — Heuristic score (0.45–0.65 range, since no web verification)
- `techniques` — Which anti-hallucination techniques were applied
- `risks` — Identified hallucination risks for this query
- `verified: false`

### Mode 2: Verified with Web Fusion (`verify: true`)

Runs LLM answer + BrowseAI Dev's browse pipeline **in parallel**, then fuses the best of both.

```
browse_clarity({
  prompt: "What are the main causes of antibiotic resistance?",
  verify: true
})
```

**When to use:** Accuracy matters, you want source-backed claims, you need to know which claims are confirmed by evidence.

**Response includes:**
- `answer` — Fused answer combining LLM reasoning with web evidence
- `claims[]` — Each claim tagged with origin:
  - `"confirmed"` — Both LLM and web sources agree (highest confidence)
  - `"source"` — Only found in web sources (LLM missed it)
  - `"llm"` — Only from LLM (no web confirmation — treat with caution)
- `sources[]` — Web sources with URLs, quotes, authority scores
- `confidence` — Evidence-based score adjusted by confirmation/penalty
- `contradictions[]` — Any conflicting claims found
- `verified: true`

## Anti-Hallucination Techniques

Clarity applies these techniques automatically based on query analysis:

1. **Explicit Uncertainty Permission** — LLM is told it's OK to say "I don't know"
2. **Direct Quote Grounding** — Forces citations of specific evidence
3. **Citation-Then-Verify Loop** — Cite first, then check if citation is real
4. **Chain-of-Verification (CoVe)** — Generate, then self-verify each claim
5. **Step-Back Abstraction** — Answer the general principle before the specific
6. **Source Attribution** — Every claim must name its source
7. **External Knowledge Restriction** — Only use what's in the provided context

## Workflow

### Option A: Quick Grounded Answer (No Internet)

```
browse_clarity({ prompt: "Explain quantum entanglement", verify: false })
```

Present the answer with techniques used and any identified risks.

### Option B: Verified Answer (Best Accuracy)

```
browse_clarity({ prompt: "Explain quantum entanglement", verify: true })
```

Present the fused answer. Highlight:
- **Confirmed claims** (green) — both LLM and sources agree
- **Source-only claims** (blue) — evidence the LLM missed
- **LLM-only claims** (amber) — unverified, treat with caution
- Any contradictions between LLM and sources

### Option C: Verify Your Own Output

Before presenting generated content to the user, run it through Clarity:

```
browse_clarity({
  prompt: "Is it true that [your generated claim]? What does the evidence say?",
  verify: true
})
```

## Interpreting Results

| Claim Origin | Meaning | Trust Level |
|-------------|---------|-------------|
| `confirmed` | Both LLM and web sources agree | High |
| `source` | Found in web sources, LLM didn't mention | High (web-backed) |
| `llm` | LLM generated, no web confirmation | Low (may be hallucinated) |

| Confidence Range | Meaning |
|-----------------|---------|
| 80-100% | Strong evidence, multiple corroborating sources |
| 60-79% | Good evidence, some gaps |
| 40-59% | Mixed — some confirmed, some unverified |
| 0-39% | Weak evidence, mostly LLM-only claims |

## Tips

- Use `verify: false` for speed, `verify: true` for accuracy
- In verified mode, focus on `confirmed` claims — they're the most reliable
- `llm`-origin claims without web backing may be hallucinated — caveat them
- Low confidence doesn't mean wrong — it may mean limited web evidence available
- For controversial topics, expect contradictions — surface them rather than hiding
- Combine with `browse_answer` for a full research pipeline + Clarity cross-check

## Links

- [BrowseAI Dev](https://browseai.dev)
- [Documentation](https://browseai.dev/docs)
- [MCP Server](https://www.npmjs.com/package/browseai-dev)
- [GitHub](https://github.com/BrowseAI-HQ/BrowseAI-Dev)
