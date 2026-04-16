# settings.json — Complete Configuration

## File Locations & Priority (highest wins)

| Level | Path | Scope |
|-------|------|-------|
| 1. Managed | Org-managed policy | Cannot override |
| 2. CLI args | Command line flags | This session |
| 3. Project local | `.claude/settings.local.json` | This machine (gitignored) |
| 4. Project shared | `.claude/settings.json` | All team members |
| 5. User global | `~/.claude/settings.json` | All your projects |

## Essential Settings

### Permissions (Most Important Decision)

```json
{
  "permissions": {
    "allow": [
      "Bash(npm run test)",
      "Bash(npm run lint)",
      "Bash(npm run build)",
      "Bash(npx prettier --write *)",
      "Bash(git status)",
      "Bash(git diff *)",
      "Bash(git log *)",
      "Bash(git branch *)",
      "Bash(git checkout *)",
      "Bash(git add *)",
      "Bash(git commit *)",
      "Read",
      "Grep",
      "Glob",
      "Edit",
      "Write"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push --force *)",
      "Bash(git reset --hard *)",
      "Bash(npm publish *)",
      "Bash(DROP *)",
      "Bash(DELETE FROM *)"
    ]
  }
}
```

### Permission Strategies

| Strategy | Allow | Deny | Best For |
|----------|-------|------|----------|
| **Locked** | Specific commands only | Everything else | Production, security-critical |
| **Guarded** | Read + build + test + lint | Destructive commands | Most projects (recommended) |
| **Open** | All tools | Explicit destructive only | Personal projects, solo dev |
| **Full trust** | `"bypassPermissions": true` | Nothing | Experienced users, sandboxed envs |

### Thinking Mode

```json
{
  "alwaysThinkingEnabled": true
}
```

Always enable. Thinking mode produces noticeably better results on complex tasks.
The token cost is worth it. Disable only if you're doing simple, repetitive edits.

### Model Selection

```json
{
  "model": "opus"
}
```

| Model | When |
|-------|------|
| `opus` | Complex architecture, debugging, code review, planning |
| `sonnet` | Fast iteration, simple edits, refactoring, tests |
| `haiku` | Subagent tasks, simple lookups, formatting |

Use Opus as default. Switch to Sonnet for speed when you know the task is simple.
Use Haiku only in subagents for mechanical tasks.

### Max Output Tokens

```json
{
  "maxTurnTokens": 64000
}
```

Default is 32K. For Opus 4.6, increase to 64K (128K max available). More output
tokens = Claude can write more code before stopping mid-file.

### Effort Level

```json
{
  "effortLevel": "high"
}
```

| Level | Tokens | When |
|-------|--------|------|
| `low` | Fewer thinking tokens | Simple renames, formatting |
| `medium` | Default | Most tasks |
| `high` | More thinking tokens | Architecture, debugging, complex logic |
| `max` | Maximum (Opus only) | Hardest problems, security review |

## Hooks Configuration

### Pre-Flight Hook (Prevent Edits on Main)

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "[ \"$(git branch --show-current)\" != \"main\" ] || { echo '{\"block\": true, \"message\": \"Cannot edit on main branch. Create a feature branch first.\"}' >&2; exit 2; }",
            "timeout": 5
          }
        ]
      }
    ]
  }
}
```

### Auto-Format on Save

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "npx prettier --write \"$CLAUDE_TOOL_INPUT_FILE_PATH\" 2>/dev/null || true",
            "timeout": 10
          }
        ]
      }
    ]
  }
}
```

### Auto-Lint After Edit

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "cd $(git rev-parse --show-toplevel) && npx eslint \"$CLAUDE_TOOL_INPUT_FILE_PATH\" --fix --quiet 2>/dev/null || true",
            "timeout": 15
          }
        ]
      }
    ]
  }
}
```

## Complete Recommended settings.json

```json
{
  "model": "opus",
  "alwaysThinkingEnabled": true,
  "maxTurnTokens": 64000,
  "effortLevel": "high",
  "permissions": {
    "allow": [
      "Read", "Grep", "Glob", "Edit", "Write",
      "Bash(npm run *)", "Bash(npx *)",
      "Bash(git status)", "Bash(git diff *)", "Bash(git log *)",
      "Bash(git branch *)", "Bash(git checkout *)",
      "Bash(git add *)", "Bash(git commit *)"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push --force *)",
      "Bash(git reset --hard *)"
    ]
  },
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "[ \"$(git branch --show-current)\" != \"main\" ] || { echo '{\"block\": true, \"message\": \"Cannot edit on main. Create a branch.\"}' >&2; exit 2; }",
            "timeout": 5
          }
        ]
      }
    ]
  }
}
```
