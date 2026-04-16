# Advanced Hooks

## Chained Hooks

Multiple hooks can fire on the same event. They run in order:

```json
{
  "PostToolUse": [
    {
      "matcher": "Edit|Write",
      "hooks": [
        {"type": "command", "command": "prettier --write \"$CLAUDE_TOOL_INPUT_FILE_PATH\"", "timeout": 10},
        {"type": "command", "command": "eslint --fix \"$CLAUDE_TOOL_INPUT_FILE_PATH\"", "timeout": 15}
      ]
    }
  ]
}
```

First prettier formats, then eslint fixes. Order matters.

## Conditional Hooks

Use shell conditionals for context-dependent behavior:

### Only Format TypeScript Files
```json
{
  "command": "[[ \"$CLAUDE_TOOL_INPUT_FILE_PATH\" == *.ts ]] && npx prettier --write \"$CLAUDE_TOOL_INPUT_FILE_PATH\" || true",
  "timeout": 10
}
```

### Only Lint Source Files (Not Tests)
```json
{
  "command": "echo \"$CLAUDE_TOOL_INPUT_FILE_PATH\" | grep -qv '__tests__' && npx eslint --fix \"$CLAUDE_TOOL_INPUT_FILE_PATH\" 2>/dev/null || true",
  "timeout": 15
}
```

### Only on Specific Branches
```json
{
  "command": "[ \"$(git branch --show-current)\" = \"release\" ] && npm run typecheck 2>/dev/null || true",
  "timeout": 30
}
```

## Stop Hook — End-of-Turn Verification

The Stop hook fires when Claude finishes a turn. Use it to verify work:

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [{
          "type": "command",
          "command": "npm test --silent 2>&1 | tail -5 | grep -q 'Tests:.*failed' && echo '{\"feedback\": \"Tests are failing. Please fix before continuing.\"}' || true",
          "timeout": 30
        }]
      }
    ]
  }
}
```

## SessionStart Hook — Initialization

Run setup tasks when Claude Code starts:

```json
{
  "hooks": {
    "SessionStart": [
      {
        "hooks": [{
          "type": "command",
          "command": "git fetch origin --quiet 2>/dev/null; echo '{\"feedback\": \"Synced with remote.\"}'",
          "timeout": 15
        }]
      }
    ]
  }
}
```

## PreCompact Hook — Save State Before Compaction

Save critical context before Claude compresses the conversation:

```json
{
  "hooks": {
    "PreCompact": [
      {
        "hooks": [{
          "type": "command",
          "command": "echo '{\"feedback\": \"Context is being compacted. Current branch: '$(git branch --show-current)'. Modified files: '$(git diff --name-only | tr '\\n' ', ')'\"}' ",
          "timeout": 5
        }]
      }
    ]
  }
}
```

## Hook Debugging

When a hook isn't working:

1. **Test the command manually** in your terminal
2. **Check the matcher** — `"Edit|Write"` not `"edit|write"` (case-sensitive)
3. **Check timeout** — if the command takes longer, it's silently killed
4. **Check exit codes** — exit 2 = process JSON stderr, exit 0 = pass, exit 1 = fail without blocking
5. **Check JSON format** — stderr must be valid JSON for blocking
6. **Test with `"*"` matcher** first to confirm the hook fires at all
