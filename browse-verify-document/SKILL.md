---
name: browse-verify-document
description: Fact-check an entire document (AI-generated report, competitive analysis, news article, market summary) by extracting every atomic claim and verifying each against live web sources. Use when the user wants to audit the accuracy of a report rather than ask a single question.
---

# BrowseAI Dev — Verify Document

Audit the factual accuracy of any long-form document. Built for verifying outputs from OTHER AI agents (competitive analysis reports, market research, deep-research summaries, news articles, etc).

## When to Use

- User pastes a report and asks "is this accurate?", "verify this", "fact-check this"
- User wants to audit the output of another AI agent or skill
- User has a competitive analysis, market research, or research summary to vet
- User says "how much of this is true?", "grade this report"
- You generated a long answer and want to self-audit before delivering it

## What it Does

1. Extracts every atomic factual claim from the document via LLM
2. Runs each claim through the Evidence Engine pipeline (search → fetch → NLI verify)
3. Returns a per-claim verification status with source citations
4. Provides an overall grade (A-F) based on verification rate

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

## Tool

Use `browse_verify_document` (added in MCP v0.4+):

```
browse_verify_document({
  text: "<the full document text>",
  title: "Q4 2025 Competitive Analysis Report",  // optional
  depth: "fast",         // "fast" or "thorough" — thorough does multi-pass per claim
  maxClaims: 20          // cap to control cost (default 20, max 50)
})
```

## Response Shape

```json
{
  "title": "Q4 2025 Competitive Analysis Report",
  "documentLength": 4823,
  "summary": {
    "totalClaims": 18,
    "verified": 14,
    "contradicted": 2,
    "unverified": 2,
    "verificationRate": 0.78,
    "overallConfidence": 0.71,
    "grade": "B"
  },
  "claims": [
    {
      "text": "OpenAI raised $40B at a $300B valuation in Q1 2025",
      "status": "verified",
      "confidence": 0.92,
      "topSource": {
        "url": "...",
        "title": "OpenAI Funding Round",
        "domain": "ft.com",
        "quote": "OpenAI completed a $40 billion funding round at a $300 billion valuation..."
      },
      "nli": { "label": "entailment", "entailment": 0.96, "contradiction": 0.01, "neutral": 0.03 },
      "reason": "Verified by 3 sources (strong consensus)"
    },
    {
      "text": "Anthropic has more ARR than OpenAI",
      "status": "contradicted",
      "confidence": 0.18,
      "topSource": { ... },
      "nli": { "label": "contradiction", ... },
      "reason": "Sources contradict this claim (NLI contradiction score: 87%)"
    }
  ],
  "durationMs": 12450
}
```

## Workflow

1. **Receive document** — paste from clipboard, file upload, or earlier conversation
2. **Call `browse_verify_document`** with the text and an optional title
3. **Present the report card**:
   - Lead with the **grade** (A-F) and verification rate ("This report is **78% verified** — Grade B")
   - List the **contradicted claims** prominently — these are the dangerous ones
   - List **unverified claims** with a note that absence of evidence isn't evidence of absence
   - Show top sources for the strongest verified claims
4. **Highlight surprises** — claims where the report disagrees with current evidence

## Example

User: "Sierra Ventures sent me this competitive analysis report. Can you verify how much of it is accurate?"

```
browse_verify_document({
  text: "<paste report>",
  title: "Sierra Ventures - AI Tooling Landscape Q4 2025",
  depth: "thorough",
  maxClaims: 25
})
```

Then present:

> **Grade: B (78% verified)** — 14 of 18 claims supported by current evidence
>
> **⚠️ Contradicted claims (verify with source):**
> 1. "Anthropic has more ARR than OpenAI" — sources show OpenAI ARR is roughly 2x Anthropic's
> 2. "GPT-4o was released in 2023" — actually released May 2024
>
> **❓ Unverified (no strong evidence either way):**
> - "Cohere raised Series D in October 2025"
> - "Mistral has 200 employees"
>
> **✅ Verified highlights:** OpenAI's $40B Q1 2025 funding round, Anthropic's $61B valuation, Vercel AI SDK adoption metrics — all confirmed across multiple authoritative sources.
>
> Full per-claim breakdown attached.

## Tips

- Use `depth: "thorough"` for high-stakes audits (legal, investment, medical) — slower but catches more
- Use `depth: "fast"` for quick triage of long documents
- Set `maxClaims` lower (10-15) for very long documents to control latency
- Contradicted claims are the highest-value finding — surface them prominently
- Don't equate "unverified" with "false" — it just means web search couldn't find evidence

## Use Cases

- **VCs:** audit AI-generated competitive analysis reports
- **Researchers:** verify deep-research outputs from other agents
- **Editors:** fact-check articles before publication
- **Compliance:** audit AI-generated reports for misinformation risk
- **Self-audit:** verify your own output before delivering to a user

## Links

- [BrowseAI Dev](https://browseai.dev)
- [Documentation](https://browseai.dev/docs)
- [MCP Server](https://www.npmjs.com/package/browseai-dev)
- [GitHub](https://github.com/BrowseAI-HQ/BrowseAI-Dev)
