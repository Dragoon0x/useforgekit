# Hooks — Deterministic Automation

## What Hooks Are

Hooks are scripts that run automatically at specific points in Claude's workflow.
Unlike CLAUDE.md instructions (advisory), hooks are deterministic — they ALWAYS run.
Use hooks for things that must happen every time with zero exceptions.

## Hook Events

| Event | When It Fires | Common Uses |
|-------|-------------|-------------|
| `PreToolUse` | Before Claude uses any tool | Block edits on main, validate paths |
| `PostToolUse` | After Claude uses any tool | Auto-format, auto-lint, log actions |
| `UserPromptSubmit` | When user sends a message | Transform input, inject context |
| `Notification` | When Claude sends a notification | Sound alerts, desktop notifications |
| `Stop` | When Claude finishes a turn | Verify work, nudge continuation |
| `SubagentStart` | When a subagent launches | Log, inject context |
| `SubagentStop` | When a subagent finishes | Collect results, verify |
| `PreCompact` | Before context compaction | Save critical state |
| `SessionStart` | When session begins | Initialize, check env |
| `SessionEnd` | When session ends | Cleanup, save state |

## Hook Structure

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "your-script.sh",
            "timeout": 10
          }
        ]
      }
    ]
  }
}
```

### Matcher Patterns

| Matcher | Matches |
|---------|---------|
| `"Edit"` | File edit operations |
| `"Write"` | File write operations |
| `"Edit\|Write"` | Either edit or write |
| `"Bash"` | Any bash command |
| `"Bash(npm *)"` | Bash commands starting with npm |
| `"*"` | Everything |

## Essential Hooks Every Project Needs

### 1. Branch Protection (PreToolUse)

```json
{
  "matcher": "Edit|Write",
  "hooks": [{
    "type": "command",
    "command": "[ \"$(git branch --show-current)\" != \"main\" ] || { echo '{\"block\": true, \"message\": \"Create a branch first.\"}' >&2; exit 2; }",
    "timeout": 5
  }]
}
```

### 2. Auto-Format (PostToolUse)

```json
{
  "matcher": "Edit|Write",
  "hooks": [{
    "type": "command",
    "command": "npx prettier --write \"$CLAUDE_TOOL_INPUT_FILE_PATH\" 2>/dev/null || true",
    "timeout": 10
  }]
}
```

### 3. Dangerous Command Warning (PreToolUse)

```json
{
  "matcher": "Bash",
  "hooks": [{
    "type": "command",
    "command": "echo \"$CLAUDE_TOOL_INPUT\" | grep -qiE '(rm -rf|drop table|delete from|force-push|reset --hard)' && { echo '{\"block\": true, \"message\": \"Destructive command detected. Confirm with user.\"}' >&2; exit 2; } || true",
    "timeout": 5
  }]
}
```

## Hook Output Protocol

Hooks communicate with Claude via stderr JSON:

| Output | Effect |
|--------|--------|
| `{"block": true, "message": "..."}` | Block the action, show message |
| `{"feedback": "..."}` | Non-blocking info to Claude |
| `{"suppressOutput": true}` | Hide hook output from user |
| Exit code 2 | Process the stderr JSON |
| Exit code 0 | Hook passed, continue |
| Exit code 1 | Hook failed but don't block |

## Hook Environment Variables

| Variable | Available In | Content |
|----------|-------------|---------|
| `CLAUDE_TOOL_INPUT` | PreToolUse | The command/content Claude is about to execute |
| `CLAUDE_TOOL_INPUT_FILE_PATH` | Pre/PostToolUse | File being edited/written |
| `CLAUDE_TOOL_OUTPUT` | PostToolUse | Tool execution result |
| `CLAUDE_SESSION_ID` | All | Current session ID |

## Hooks by Project Type

### JavaScript / TypeScript
```json
{
  "PostToolUse": [
    {"matcher": "Edit|Write", "hooks": [{"type": "command", "command": "npx prettier --write \"$CLAUDE_TOOL_INPUT_FILE_PATH\" 2>/dev/null || true", "timeout": 10}]}
  ]
}
```

### Python
```json
{
  "PostToolUse": [
    {"matcher": "Edit|Write", "hooks": [{"type": "command", "command": "ruff format \"$CLAUDE_TOOL_INPUT_FILE_PATH\" 2>/dev/null && ruff check --fix \"$CLAUDE_TOOL_INPUT_FILE_PATH\" 2>/dev/null || true", "timeout": 10}]}
  ]
}
```

### Rust
```json
{
  "PostToolUse": [
    {"matcher": "Edit|Write", "hooks": [{"type": "command", "command": "rustfmt \"$CLAUDE_TOOL_INPUT_FILE_PATH\" 2>/dev/null || true", "timeout": 10}]}
  ]
}
```

### Go
```json
{
  "PostToolUse": [
    {"matcher": "Edit|Write", "hooks": [{"type": "command", "command": "gofmt -w \"$CLAUDE_TOOL_INPUT_FILE_PATH\" 2>/dev/null || true", "timeout": 10}]}
  ]
}
```
