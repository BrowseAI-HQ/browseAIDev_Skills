# BrowseAI Dev Skills

Agent skills for [BrowseAI Dev](https://browseai.dev) — evidence-backed web research for AI coding agents.

Works with **Claude Code, Codex CLI, Gemini CLI, Cursor, Copilot, Windsurf**, and any tool that supports the [Agent Skills specification](https://agentskills.io/specification).

## Quick Install

```bash
npx skills add BrowseAI-HQ/browseAIDev_Skills
```

Or manually copy any skill folder into your agent's skills directory (e.g., `~/.claude/skills/`).

## Available Skills

| Skill | What it does | Key tool |
|-------|-------------|----------|
| [browse-research](browse-research/) | Evidence-backed answers with citations and confidence scores | `browse_answer` |
| [browse-fact-check](browse-fact-check/) | Compare raw LLM vs evidence-backed answers, verify claims | `browse_compare` |
| [browse-extract](browse-extract/) | Extract structured claims and quotes from any URL | `browse_extract` |
| [browse-sessions](browse-sessions/) | Multi-query research with persistent knowledge accumulation | `browse_session_*` |

## Prerequisites

These skills use BrowseAI Dev's MCP server. Add it to your MCP config:

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

For session features (persistent research memory, sharing, forking), set a BrowseAI Dev API key:

```json
{
  "mcpServers": {
    "browse-ai": {
      "command": "npx",
      "args": ["-y", "browse-ai"],
      "env": {
        "BROWSE_API_KEY": "bai_xxx"
      }
    }
  }
}
```

Get a free API key at [browseai.dev/dashboard](https://browseai.dev/dashboard).

## How Skills Work

Each skill is a `SKILL.md` file that teaches AI coding agents **when and how** to use BrowseAI Dev tools. Unlike the MCP server (which provides the tools), skills provide the **judgment layer** — they tell agents when to trigger, what parameters to use, and how to interpret results.

```
User asks a question
     ↓
Agent reads SKILL.md → knows when to trigger
     ↓
Agent calls MCP tools (browse_answer, browse_compare, etc.)
     ↓
Agent interprets results using skill guidance
     ↓
User gets cited, evidence-backed answer
```

## What is BrowseAI Dev?

Open-source research infrastructure for AI agents. Every answer goes through a verification pipeline:

1. **Web Search** — Tavily API finds relevant pages
2. **Page Fetch** — Downloads and parses pages into clean text
3. **Claim Extraction** — AI extracts structured claims with source attribution
4. **BM25 Verification** — Sentence-level matching verifies each claim
5. **Cross-Source Consensus** — Claims in multiple sources get higher scores
6. **Contradiction Detection** — Conflicting claims are flagged
7. **Domain Authority** — 10,000+ domains scored with Bayesian dynamic blending
8. **Confidence Score** — 7-factor evidence-based score (not LLM self-assessed)

Available as [MCP server](https://www.npmjs.com/package/browse-ai), [REST API](https://browseai.dev/docs), and [Python SDK](https://pypi.org/project/browseai/).

## Contributing

Want to add a new skill? PRs welcome!

1. Create a new folder with a descriptive name (e.g., `browse-competitive-analysis/`)
2. Add a `SKILL.md` following the format of existing skills
3. Include: when to trigger, prerequisites, step-by-step workflow, examples, tips
4. Submit a PR to the `shreyas` branch

## Links

- [BrowseAI Dev](https://browseai.dev)
- [Documentation](https://browseai.dev/docs)
- [Main Repo](https://github.com/BrowseAI-HQ/BrowseAI-Dev)
- [MCP Server (npm)](https://www.npmjs.com/package/browse-ai)
- [Python SDK (PyPI)](https://pypi.org/project/browseai/)
- [Discord](https://discord.gg/ubAuT4YQsT)

## License

MIT
