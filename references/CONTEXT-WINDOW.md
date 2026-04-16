# Context Window Management

## The Budget

Claude Code has a context window (200K tokens for Opus 4.6). Every file read,
every command output, every conversation turn consumes tokens. When you hit the
limit, Claude compacts (summarizes and forgets details).

## Token Costs (Approximate)

| Action | Tokens |
|--------|--------|
| CLAUDE.md (200 lines) | ~500 |
| Skill SKILL.md loaded | ~1,000-3,000 |
| File read (500-line source file) | ~2,000 |
| `git diff` output (typical) | ~1,000-5,000 |
| `npm test` output (passing) | ~200-500 |
| `npm test` output (failing with stack traces) | ~2,000-10,000 |
| Large file read (2000+ lines) | ~8,000-15,000 |
| `git log` (50 commits) | ~2,000 |
| Directory listing | ~200-500 |
| Each conversation turn | ~100-500 |

## Budget Management Rules

### 1. Compact at 50%

Run `/compact` manually when context reaches ~50%. Don't wait for automatic compaction.
Automatic compaction loses more detail than manual compaction at 50%.

### 2. Use /clear Between Unrelated Tasks

Switching from "fix the auth bug" to "redesign the dashboard"? Run `/clear`.
Carrying the old context wastes tokens and confuses Claude.

### 3. Read Specific Lines, Not Whole Files

```
// Bad — reads entire 2000-line file (8K tokens)
Read src/big-file.ts

// Good — reads only the relevant section (500 tokens)
Read src/big-file.ts lines 45-80
```

### 4. Limit Command Output

```
// Bad — dumps 500 lines of test output
npm test

// Good — shows only failures
npm test 2>&1 | grep -A 5 "FAIL"
```

### 5. Keep Skills Small

A skill that loads 5,000 tokens of instructions every time it fires is burning
context. Keep SKILL.md under 500 lines. Use progressive disclosure (sub-files).

### 6. Use Subagents for Independent Tasks

Subagents get their own context window. Use them for tasks that don't need
the main conversation's history: code review, test generation, documentation.

## Context Compaction Strategy

When context is getting full:
1. `/compact` — summarizes conversation, keeps key decisions
2. Continue working — Claude has the summary + key state
3. If quality drops after compaction → `/clear` and restart with fresh context

## Signs of Context Pressure

| Symptom | Cause | Fix |
|---------|-------|-----|
| Claude forgets instructions from CLAUDE.md | File got compacted out | `/clear` and restart |
| Claude re-reads files it already read | Lost track of what it knows | `/compact` then continue |
| Claude gives shorter, less thoughtful answers | Token budget pressure | `/clear` or use subagent |
| Claude stops following code conventions | CLAUDE.md got deprioritized | Make conventions shorter, stronger |
| Claude asks questions it already asked | Conversation too long | `/compact` at 50% |
