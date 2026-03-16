---
name: browse-sessions
description: Deep multi-query research with persistent knowledge accumulation. Use when the user is researching a topic across multiple questions and wants each query to build on prior findings.
---

# BrowseAI Dev — Research Sessions

Use this skill when the user is doing deep, multi-query research on a topic. Sessions persist knowledge across queries — each new question builds on prior findings.

## When to Use

- User is researching a topic across multiple questions
- User says "let's deep dive into", "I'm researching", "continue researching"
- User wants to build up knowledge incrementally (e.g., comparing frameworks, exploring a domain)
- User wants to share research findings with teammates
- User wants to fork and continue someone else's published research

## Prerequisites

**Sessions require a BrowseAI Dev API key.** BYOK mode does not support sessions.

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

### Step 1: Create a Session

```
browse_session_create({ name: "react-vs-vue-2025" })
```

Returns a `session_id` — use it for all subsequent queries.

### Step 2: Research Within the Session

```
browse_session_ask({
  session_id: "sess_abc123",
  query: "What are the performance benchmarks for React 19 vs Vue 3.5?",
  depth: "thorough"
})
```

Each `session_ask` call:
1. Recalls relevant knowledge from prior queries in this session
2. Searches the web for new information
3. Extracts and verifies claims
4. Stores new verified claims in the session knowledge base

### Step 3: Recall Without Searching

Use `browse_session_recall` to query accumulated knowledge without making new web searches:

```
browse_session_recall({
  session_id: "sess_abc123",
  query: "What did we find about React performance?"
})
```

This is fast and free — it just queries the session's knowledge base.

### Step 4: Export Knowledge

Get all accumulated claims from the session:

```
browse_session_knowledge({
  session_id: "sess_abc123",
  limit: 50
})
```

### Step 5: Share (Optional)

Share the session publicly for teammates or other agents:

```
browse_session_share({ session_id: "sess_abc123" })
```

Returns a shareable URL. Anyone can view the research at `browseai.dev/session/share/...`

### Step 6: Fork (Optional)

Continue someone else's research:

```
browse_session_fork({ share_id: "share_xyz789" })
```

Creates a copy of all their knowledge in your own session.

## Example: Deep Dive Research

User: "I'm evaluating state management libraries for our React app. Help me research."

```
# Create session
browse_session_create({ name: "react-state-mgmt-eval" })

# Research each option
browse_session_ask({ session_id: "...", query: "What are the pros and cons of Redux Toolkit in 2025?" })
browse_session_ask({ session_id: "...", query: "How does Zustand compare to Redux for large apps?" })
browse_session_ask({ session_id: "...", query: "What about Jotai vs Recoil for atomic state?" })

# Recall cross-cutting findings
browse_session_recall({ session_id: "...", query: "Which library has the best TypeScript support?" })

# Share findings with the team
browse_session_share({ session_id: "..." })
```

Each successive query benefits from prior knowledge — the session remembers what was found before and builds on it.

## Tips

- Name sessions descriptively — it helps when reviewing later
- Use `recall` before `ask` to check if the session already has relevant knowledge
- Use `depth: "thorough"` for the first few queries to build a strong knowledge base, then `fast` for follow-ups
- Sessions are persistent — you can come back to them across conversations
- Share sessions to collaborate: teammates can fork and continue the research

## Links

- [BrowseAI Dev](https://browseai.dev)
- [Documentation](https://browseai.dev/docs)
- [MCP Server](https://www.npmjs.com/package/browseai-dev)
- [GitHub](https://github.com/BrowseAI-HQ/BrowseAI-Dev)
