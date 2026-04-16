# Hook Recipes — 25 Copy-Paste Configurations

## PreToolUse Hooks (prevent bad actions)

### 1. Branch Protection
```json
{"matcher": "Edit|Write", "hooks": [{"type": "command", "command": "[ \"$(git branch --show-current)\" != \"main\" ] || { echo '{\"block\": true, \"message\": \"Cannot edit on main. Create a branch: git checkout -b feature/name\"}' >&2; exit 2; }", "timeout": 5}]}
```

### 2. Protected File Guard
```json
{"matcher": "Edit|Write", "hooks": [{"type": "command", "command": "echo \"$CLAUDE_TOOL_INPUT_FILE_PATH\" | grep -qE '(package-lock|pnpm-lock|yarn\\.lock|\\.env$|\\.env\\.local|generated/)' && { echo '{\"block\": true, \"message\": \"This file should not be edited directly.\"}' >&2; exit 2; } || true", "timeout": 5}]}
```

### 3. Destructive Command Block
```json
{"matcher": "Bash", "hooks": [{"type": "command", "command": "echo \"$CLAUDE_TOOL_INPUT\" | grep -qiE '(rm -rf|drop table|delete from|truncate|git push.*--force|git reset --hard|npm publish|npx rimraf)' && { echo '{\"block\": true, \"message\": \"Destructive command blocked. Confirm with user.\"}' >&2; exit 2; } || true", "timeout": 5}]}
```

### 4. Secret Leak Prevention
```json
{"matcher": "Bash", "hooks": [{"type": "command", "command": "echo \"$CLAUDE_TOOL_INPUT\" | grep -qiE '(cat \\.env|echo \\$.*KEY|echo \\$.*SECRET|echo \\$.*TOKEN|echo \\$.*PASSWORD|cat.*credentials)' && { echo '{\"block\": true, \"message\": \"Cannot read secrets directly.\"}' >&2; exit 2; } || true", "timeout": 5}]}
```

### 5. Dependency Install Guard
```json
{"matcher": "Bash", "hooks": [{"type": "command", "command": "echo \"$CLAUDE_TOOL_INPUT\" | grep -qE '^(npm install|pnpm add|yarn add|pip install) ' && { echo '{\"block\": true, \"message\": \"New dependency detected. Confirm: package name, reason, and size impact.\"}' >&2; exit 2; } || true", "timeout": 5}]}
```

### 6. Production Database Guard
```json
{"matcher": "Bash", "hooks": [{"type": "command", "command": "echo \"$CLAUDE_TOOL_INPUT\" | grep -qiE '(production|prod)' && echo \"$CLAUDE_TOOL_INPUT\" | grep -qiE '(psql|mysql|mongo|redis-cli|supabase db)' && { echo '{\"block\": true, \"message\": \"Production database command detected. Are you sure?\"}' >&2; exit 2; } || true", "timeout": 5}]}
```

### 7. Large File Read Warning
```json
{"matcher": "Read", "hooks": [{"type": "command", "command": "[ -f \"$CLAUDE_TOOL_INPUT_FILE_PATH\" ] && [ $(wc -l < \"$CLAUDE_TOOL_INPUT_FILE_PATH\" 2>/dev/null || echo 0) -gt 500 ] && echo '{\"feedback\": \"Large file (500+ lines). Consider reading specific line ranges.\"}' || true", "timeout": 5}]}
```

## PostToolUse Hooks (auto-actions after tools)

### 8. Prettier (JavaScript/TypeScript)
```json
{"matcher": "Edit|Write", "hooks": [{"type": "command", "command": "[[ \"$CLAUDE_TOOL_INPUT_FILE_PATH\" == *.ts || \"$CLAUDE_TOOL_INPUT_FILE_PATH\" == *.tsx || \"$CLAUDE_TOOL_INPUT_FILE_PATH\" == *.js || \"$CLAUDE_TOOL_INPUT_FILE_PATH\" == *.jsx ]] && npx prettier --write \"$CLAUDE_TOOL_INPUT_FILE_PATH\" 2>/dev/null || true", "timeout": 10}]}
```

### 9. Ruff (Python)
```json
{"matcher": "Edit|Write", "hooks": [{"type": "command", "command": "[[ \"$CLAUDE_TOOL_INPUT_FILE_PATH\" == *.py ]] && ruff format \"$CLAUDE_TOOL_INPUT_FILE_PATH\" 2>/dev/null && ruff check --fix \"$CLAUDE_TOOL_INPUT_FILE_PATH\" 2>/dev/null || true", "timeout": 10}]}
```

### 10. rustfmt (Rust)
```json
{"matcher": "Edit|Write", "hooks": [{"type": "command", "command": "[[ \"$CLAUDE_TOOL_INPUT_FILE_PATH\" == *.rs ]] && rustfmt \"$CLAUDE_TOOL_INPUT_FILE_PATH\" 2>/dev/null || true", "timeout": 10}]}
```

### 11. gofmt (Go)
```json
{"matcher": "Edit|Write", "hooks": [{"type": "command", "command": "[[ \"$CLAUDE_TOOL_INPUT_FILE_PATH\" == *.go ]] && gofmt -w \"$CLAUDE_TOOL_INPUT_FILE_PATH\" 2>/dev/null || true", "timeout": 10}]}
```

### 12. Auto-Sort Imports (TypeScript)
```json
{"matcher": "Edit|Write", "hooks": [{"type": "command", "command": "[[ \"$CLAUDE_TOOL_INPUT_FILE_PATH\" == *.ts || \"$CLAUDE_TOOL_INPUT_FILE_PATH\" == *.tsx ]] && npx eslint --fix --rule '{\"import/order\": \"error\"}' \"$CLAUDE_TOOL_INPUT_FILE_PATH\" 2>/dev/null || true", "timeout": 15}]}
```

### 13. Type Check After Edit
```json
{"matcher": "Edit|Write", "hooks": [{"type": "command", "command": "[[ \"$CLAUDE_TOOL_INPUT_FILE_PATH\" == *.ts || \"$CLAUDE_TOOL_INPUT_FILE_PATH\" == *.tsx ]] && npx tsc --noEmit 2>&1 | head -20 | grep -q 'error' && echo '{\"feedback\": \"TypeScript errors detected. Run: npx tsc --noEmit\"}' || true", "timeout": 30}]}
```

### 14. Auto-Generate Types (Supabase)
```json
{"matcher": "Edit|Write", "hooks": [{"type": "command", "command": "echo \"$CLAUDE_TOOL_INPUT_FILE_PATH\" | grep -q 'supabase/migrations/' && echo '{\"feedback\": \"Migration changed. Run: supabase gen types typescript --local > src/types/supabase.ts\"}' || true", "timeout": 5}]}
```

## Stop Hooks (end-of-turn verification)

### 15. Test Runner
```json
{"hooks": [{"type": "command", "command": "npm test --silent 2>&1 | tail -3 | grep -qiE '(fail|error)' && echo '{\"feedback\": \"Tests are failing. Please fix before continuing.\"}' || true", "timeout": 60}]}
```

### 16. Lint Check
```json
{"hooks": [{"type": "command", "command": "npx eslint src/ --quiet 2>&1 | grep -c 'error' | grep -qv '^0$' && echo '{\"feedback\": \"Lint errors found. Run: npx eslint src/ --fix\"}' || true", "timeout": 30}]}
```

## SessionStart Hooks (initialization)

### 17. Git Fetch
```json
{"hooks": [{"type": "command", "command": "git fetch origin --quiet 2>/dev/null; echo '{\"feedback\": \"Synced with remote. Current branch: '$(git branch --show-current 2>/dev/null)'\"}' ", "timeout": 15}]}
```

### 18. Check Node Version
```json
{"hooks": [{"type": "command", "command": "required=22; current=$(node -v 2>/dev/null | sed 's/v//;s/\\..*//'); [ \"$current\" -lt \"$required\" ] 2>/dev/null && echo '{\"feedback\": \"Node.js '$current' detected. This project requires '$required'+.\"}' || true", "timeout": 5}]}
```

### 19. Check for Uncommitted Changes
```json
{"hooks": [{"type": "command", "command": "[ -n \"$(git status --porcelain 2>/dev/null)\" ] && echo '{\"feedback\": \"Uncommitted changes detected. Consider committing or stashing before starting.\"}' || true", "timeout": 5}]}
```

## Notification Hooks

### 20. Sound on Completion (macOS)
```json
{"hooks": [{"type": "command", "command": "afplay /System/Library/Sounds/Glass.aiff 2>/dev/null || true", "timeout": 3}]}
```

### 21. Desktop Notification (macOS)
```json
{"hooks": [{"type": "command", "command": "osascript -e 'display notification \"Claude finished a task\" with title \"Claude Code\"' 2>/dev/null || true", "timeout": 3}]}
```

### 22. Desktop Notification (Linux)
```json
{"hooks": [{"type": "command", "command": "notify-send 'Claude Code' 'Task completed' 2>/dev/null || true", "timeout": 3}]}
```

## PreCompact Hooks

### 23. Save Context Summary
```json
{"hooks": [{"type": "command", "command": "echo '{\"feedback\": \"CONTEXT SAVE — Branch: '$(git branch --show-current 2>/dev/null)' | Modified: '$(git diff --name-only 2>/dev/null | tr '\\n' ', ')' | Last commit: '$(git log -1 --format='%s' 2>/dev/null)'\"}' ", "timeout": 5}]}
```

## Custom Compound Hooks

### 24. Format + Lint Chain (TypeScript)
```json
{"matcher": "Edit|Write", "hooks": [
  {"type": "command", "command": "[[ \"$CLAUDE_TOOL_INPUT_FILE_PATH\" == *.ts ]] && npx prettier --write \"$CLAUDE_TOOL_INPUT_FILE_PATH\" 2>/dev/null || true", "timeout": 10},
  {"type": "command", "command": "[[ \"$CLAUDE_TOOL_INPUT_FILE_PATH\" == *.ts ]] && npx eslint --fix \"$CLAUDE_TOOL_INPUT_FILE_PATH\" 2>/dev/null || true", "timeout": 15}
]}
```

### 25. Full Quality Gate (Stop Hook)
```json
{"hooks": [{"type": "command", "command": "cd $(git rev-parse --show-toplevel 2>/dev/null || echo .) && result=''; npm test --silent 2>&1 | grep -qiE 'fail' && result=\"$result Tests failing.\"; npx tsc --noEmit 2>&1 | grep -q 'error' && result=\"$result Type errors.\"; [ -n \"$result\" ] && echo '{\"feedback\": \"Quality issues:'$result'\"}' || true", "timeout": 60}]}
```
