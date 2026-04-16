# Git Strategy — What to Commit

## .gitignore Additions for Claude Code

```gitignore
# Claude Code personal settings (never commit)
.claude/settings.local.json

# Claude Code conversation state
.claude/conversations/
.claude/memory/

# Claude auto-generated
*.claude-backup
```

## What to Commit

| File | Commit? | Why |
|------|---------|-----|
| `CLAUDE.md` | ✓ | Team-shared project context |
| `.claude/settings.json` | ✓ | Team-shared hooks and permissions |
| `.claude/settings.local.json` | ✗ | Personal overrides |
| `.claude/skills/*/SKILL.md` | ✓ | Team-shared knowledge |
| `.claude/agents/*.md` | ✓ | Team-shared agent definitions |
| `.claude/commands/*.md` | ✓ | Team-shared workflows |
| `.claude/rules/*.md` | ✓ | Team-shared rules |
| `.claude/hooks/scripts/*.sh` | ✓ | Team-shared hook scripts |
| `.claude/hooks/config/hooks-config.json` | ✓ | Team hook config |
| `.claude/hooks/config/hooks-config.local.json` | ✗ | Personal hook overrides |
| `.claude/conversations/` | ✗ | Private, session-specific |

## Branch Strategy with Claude Code

1. Never edit on `main` — use a PreToolUse hook to enforce this
2. Create feature branches: `git checkout -b feature/[description]`
3. One feature per branch — keeps Claude's context focused
4. Commit frequently — Claude can always `git diff` to see what changed
5. Use conventional commits: `feat:`, `fix:`, `docs:`, `test:`, `refactor:`
