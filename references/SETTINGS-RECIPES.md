# Settings Recipes — Complete Configurations

## Solo Developer (Open, Fast)

```json
{
  "model": "opus",
  "alwaysThinkingEnabled": true,
  "maxTurnTokens": 64000,
  "effortLevel": "high",
  "permissions": {
    "allow": [
      "Read", "Grep", "Glob", "Edit", "Write",
      "Bash(npm *)", "Bash(npx *)", "Bash(node *)", "Bash(pnpm *)",
      "Bash(git *)", "Bash(gh *)",
      "Bash(cat *)", "Bash(ls *)", "Bash(find *)", "Bash(grep *)",
      "Bash(head *)", "Bash(tail *)", "Bash(wc *)", "Bash(echo *)",
      "Bash(mkdir *)", "Bash(cp *)", "Bash(mv *)"
    ],
    "deny": [
      "Bash(rm -rf *)", "Bash(sudo *)", "Bash(curl * | bash)"
    ]
  },
  "hooks": {
    "PreToolUse": [
      {"matcher": "Edit|Write", "hooks": [{"type": "command", "command": "[ \"$(git branch --show-current)\" != \"main\" ] || { echo '{\"block\": true, \"message\": \"Create a branch first.\"}' >&2; exit 2; }", "timeout": 5}]}
    ],
    "PostToolUse": [
      {"matcher": "Edit|Write", "hooks": [{"type": "command", "command": "npx prettier --write \"$CLAUDE_TOOL_INPUT_FILE_PATH\" 2>/dev/null || true", "timeout": 10}]}
    ]
  }
}
```

## Team Project (Guarded)

```json
{
  "model": "sonnet",
  "alwaysThinkingEnabled": true,
  "maxTurnTokens": 32000,
  "permissions": {
    "allow": [
      "Read", "Grep", "Glob", "Edit", "Write",
      "Bash(npm run *)", "Bash(npx *)",
      "Bash(git status)", "Bash(git diff *)", "Bash(git log *)",
      "Bash(git branch *)", "Bash(git checkout *)",
      "Bash(git add *)", "Bash(git commit *)"
    ],
    "deny": [
      "Bash(rm -rf *)", "Bash(git push --force *)", "Bash(git reset --hard *)",
      "Bash(npm publish *)", "Bash(npm install *)", "Bash(pnpm add *)"
    ]
  },
  "hooks": {
    "PreToolUse": [
      {"matcher": "Edit|Write", "hooks": [{"type": "command", "command": "[ \"$(git branch --show-current)\" != \"main\" ] || { echo '{\"block\": true, \"message\": \"No edits on main.\"}' >&2; exit 2; }", "timeout": 5}]},
      {"matcher": "Bash", "hooks": [{"type": "command", "command": "echo \"$CLAUDE_TOOL_INPUT\" | grep -qiE '(rm -rf|drop |delete from|truncate)' && { echo '{\"block\": true, \"message\": \"Destructive command blocked.\"}' >&2; exit 2; } || true", "timeout": 5}]}
    ],
    "PostToolUse": [
      {"matcher": "Edit|Write", "hooks": [{"type": "command", "command": "npx prettier --write \"$CLAUDE_TOOL_INPUT_FILE_PATH\" 2>/dev/null || true", "timeout": 10}]}
    ]
  }
}
```

## CI/CD Pipeline (Locked)

```json
{
  "model": "sonnet",
  "maxTurnTokens": 16000,
  "permissions": {
    "allow": [
      "Read", "Grep", "Glob",
      "Bash(npm test *)", "Bash(npm run lint *)", "Bash(npm run build *)",
      "Bash(git diff *)", "Bash(git log *)"
    ],
    "deny": [
      "Edit", "Write",
      "Bash(rm *)", "Bash(git push *)", "Bash(git commit *)",
      "Bash(npm install *)", "Bash(npm publish *)"
    ]
  }
}
```

## Python Project

```json
{
  "model": "opus",
  "alwaysThinkingEnabled": true,
  "permissions": {
    "allow": [
      "Read", "Grep", "Glob", "Edit", "Write",
      "Bash(uv run *)", "Bash(python *)", "Bash(pytest *)",
      "Bash(ruff *)", "Bash(mypy *)",
      "Bash(git *)", "Bash(gh *)"
    ],
    "deny": [
      "Bash(rm -rf *)", "Bash(pip install *)", "Bash(sudo *)"
    ]
  },
  "hooks": {
    "PostToolUse": [
      {"matcher": "Edit|Write", "hooks": [{"type": "command", "command": "[[ \"$CLAUDE_TOOL_INPUT_FILE_PATH\" == *.py ]] && ruff format \"$CLAUDE_TOOL_INPUT_FILE_PATH\" 2>/dev/null && ruff check --fix \"$CLAUDE_TOOL_INPUT_FILE_PATH\" 2>/dev/null || true", "timeout": 10}]}
    ]
  }
}
```
