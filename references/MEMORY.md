# Memory — Cross-Session Persistence

## How Claude Code Memory Works

Claude Code can persist information across sessions via auto-memory.
This means Claude remembers patterns, preferences, and learnings from
previous sessions.

## Memory Types

| Type | Scope | Shared | Use Case |
|------|-------|--------|----------|
| Auto-memory | Per-project | No | Learned patterns, preferences |
| CLAUDE.md | Per-project | Yes (committed) | Explicit project knowledge |
| Agent memory | Per-agent | Configurable | Agent-specific learnings |

## Managing Memory

### View What Claude Remembers
```
/memory
```

### Prune Outdated Memories
```
"Review your memories about this project.
Delete anything that's outdated or wrong.
Keep only things that are still true."
```

### Teach Claude Explicitly
```
"Remember: in this project, we always use date-fns for date formatting,
never moment.js. The utility function is in src/utils/dates.ts."
```

### Clear All Memories
```
/memory --clear
```

## Memory Anti-Patterns

| Pattern | Problem | Fix |
|---------|---------|-----|
| Relying on memory for critical info | Memory can be pruned or wrong | Put critical info in CLAUDE.md |
| Never pruning memory | Stale info causes bad decisions | Review monthly |
| Contradicting CLAUDE.md | Memory overrides explicit rules | Keep CLAUDE.md authoritative |
| Storing secrets in memory | Security risk | Never teach Claude secrets |

## Memory vs CLAUDE.md

| Information | Memory | CLAUDE.md |
|------------|--------|-----------|
| Team conventions | | ✓ (authoritative) |
| Personal preferences | ✓ | |
| Learned gotchas | ✓ (then promote to CLAUDE.md if important) | |
| Build commands | | ✓ |
| "Last time this broke because..." | ✓ | |
| Architecture decisions | | ✓ |
