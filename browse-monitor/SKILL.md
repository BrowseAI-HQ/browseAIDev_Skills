---
name: browse-monitor
description: Track evolving topics over time using persistent research sessions. Use when the user wants to monitor news, product launches, policy changes, or any topic that changes.
---

# BrowseAI Dev — Monitor & Track Changes

Use this skill when the user wants to monitor an evolving topic over time — tracking what's new, what changed, and what's still the same since last check.

## When to Use

- User says "watch for", "monitor", "alert me if", "track changes on", "keep me updated"
- User wants to follow a developing story, product launch, or policy change
- User needs periodic updates on a topic with diff-style "what's new" reporting
- User is tracking competitor activity, regulatory changes, or market developments

## Prerequisites

**Monitoring requires a BrowseAI Dev API key** for persistent sessions.

```json
{
  "mcpServers": {
    "browseai-dev": {
      "command": "npx",
      "args": ["-y", "browseai-dev"],
      "env": {
        "BROWSE_API_KEY": "bai_xxx"
      }
    }
  }
}
```

Get a free API key at [browseai.dev/dashboard](https://browseai.dev/dashboard).

## Workflow

### Step 1: Create a Monitoring Session

Create a dedicated session for the topic to track:

```
browse_session_create({ name: "openai-product-launches-2025" })
```

Save the returned `session_id` — it persists across conversations.

### Step 2: Run Initial Research

Establish the baseline — what is currently known about this topic:

```
browse_session_ask({
  session_id: "sess_abc123",
  query: "What are OpenAI's most recent product announcements and launches in 2025?",
  depth: "thorough"
})
```

Present this as the **baseline snapshot**. The session now stores all verified claims as its knowledge base.

### Step 3: Run Follow-Up Checks

In subsequent conversations (hours, days, or weeks later), query the same session:

```
browse_session_ask({
  session_id: "sess_abc123",
  query: "What are the latest OpenAI product announcements? Any new launches or updates?",
  depth: "fast"
})
```

The session automatically recalls prior knowledge and compares against new findings.

### Step 4: Diff Against Prior Knowledge

Use `browse_session_recall` to check what was already known:

```
browse_session_recall({
  session_id: "sess_abc123",
  query: "What did we already know about OpenAI's 2025 launches?"
})
```

Compare the recall results with the latest `session_ask` results to identify:

1. **New information**: Claims in the latest results that are not in the recall
2. **Confirmed information**: Claims that appear in both — still accurate
3. **Changed information**: Claims where new evidence contradicts or updates prior findings
4. **No longer mentioned**: Claims from prior research that no longer appear in current sources

### Step 5: Present the Update

Structure the update as a change report:

- **New since last check**: Bullet list of newly discovered facts with sources
- **Still current**: Key facts that remain accurate
- **Updated**: Facts where the details have changed, with before/after
- **Watch items**: Topics with weak evidence that may evolve

### Example

User: "Track the EU AI Act enforcement for me."

```
# Initial setup
browse_session_create({ name: "eu-ai-act-enforcement" })

# Baseline research
browse_session_ask({
  session_id: "...",
  query: "What is the current status of EU AI Act enforcement? What deadlines and requirements are active?",
  depth: "thorough"
})

# Later follow-up
browse_session_recall({ session_id: "...", query: "EU AI Act deadlines and enforcement status" })

browse_session_ask({
  session_id: "...",
  query: "Any new EU AI Act enforcement actions, deadline changes, or compliance updates?",
  depth: "fast"
})
```

Present: "Since your last check: Two new compliance guidelines were published by the EU AI Office (source: ...). The prohibited AI practices deadline remains Feb 2025 as previously noted. New: Member state enforcement agencies have begun formal investigations (source: ...)."

## Tips

- Name sessions descriptively with the topic and year for easy retrieval
- Use `thorough` depth for the initial baseline, then `fast` for follow-up checks to save quota
- The `session_recall` tool is free (no web search) — use it to check existing knowledge before searching again
- For high-frequency monitoring, keep queries focused on "what's new" to avoid re-retrieving known information
- Share the session (`browse_session_share`) to let teammates follow the same topic
- Sessions persist indefinitely — you can monitor a topic over weeks or months

## Links

- [BrowseAI Dev](https://browseai.dev)
- [Documentation](https://browseai.dev/docs)
- [MCP Server](https://www.npmjs.com/package/browseai-dev)
- [GitHub](https://github.com/BrowseAI-HQ/BrowseAI-Dev)
