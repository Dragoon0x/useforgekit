# MCP Servers — External Tool Integration

## What MCP Is

MCP (Model Context Protocol) connects Claude Code to external services:
databases, issue trackers, design tools, monitoring systems.

## Essential MCP Servers

| Server | Purpose | Install |
|--------|---------|---------|
| GitHub | Issues, PRs, repos | `claude mcp add github` |
| Figma | Design files, components | `claude mcp add figma` |
| Notion | Documents, databases | `claude mcp add notion` |
| Supabase | Database, auth | `claude mcp add supabase` |
| Sentry | Error tracking | `claude mcp add sentry` |
| Linear | Issue tracking | `claude mcp add linear` |
| Slack | Team messaging | `claude mcp add slack` |
| PostgreSQL | Direct DB queries | `claude mcp add postgres` |

## Configuration (.mcp.json)

```json
{
  "mcpServers": {
    "github": {
      "command": "gh",
      "args": ["mcp"]
    },
    "supabase": {
      "command": "npx",
      "args": ["-y", "@supabase/mcp-server"],
      "env": {
        "SUPABASE_URL": "your-url",
        "SUPABASE_SERVICE_KEY": "your-key"
      }
    }
  }
}
```

## When to Use MCP vs CLI

| Need | Use MCP | Use CLI |
|------|---------|---------|
| Browse GitHub issues | ✓ (structured data) | `gh issue list` also works |
| Query database | ✓ (schema-aware) | Raw SQL via `psql` |
| Read Figma designs | ✓ (only option) | — |
| Post to Slack | ✓ (native integration) | `curl` to webhook |
| Complex multi-step API | ✓ (session state) | Multiple curl commands |
| Simple one-off command | — | ✓ (faster, no setup) |

## MCP in Agents

Agents can have their own MCP servers:

```yaml
---
name: db-analyst
mcpServers:
  - postgres
tools: Read, Grep, mcp__postgres__query
---
```

This scopes database access to the analyst agent only.
