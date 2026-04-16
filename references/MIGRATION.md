# Migration — From Other AI Tools to Claude Code

## From Cursor

| Cursor Feature | Claude Code Equivalent |
|---------------|----------------------|
| `.cursorrules` | `CLAUDE.md` |
| Cursor Settings | `.claude/settings.json` |
| AI Chat | Claude Code conversation |
| Cmd+K inline edit | Claude Code file edits |
| Tab completion | Not available (Claude Code is command-based) |
| Composer | Skills + Commands |
| Cursor MCP | Claude MCP (same protocol) |
| Custom rules | `.claude/rules/*.md` |

### Migration Steps
1. Copy `.cursorrules` content into `CLAUDE.md` (trim to < 200 lines)
2. Set up `.claude/settings.json` with your permissions
3. Convert any custom Cursor rules to `.claude/rules/` files
4. Install Claude Code: `npm install -g @anthropic-ai/claude-code`

## From GitHub Copilot

| Copilot Feature | Claude Code Equivalent |
|----------------|----------------------|
| `.github/copilot-instructions.md` | `CLAUDE.md` |
| Inline suggestions | Not available (Claude Code is command-based) |
| Copilot Chat | Claude Code conversation |
| /fix, /explain, /tests | Skills or Commands |
| Copilot Workspace | Commands + Agents |

### Migration Steps
1. Copy relevant parts of `copilot-instructions.md` into `CLAUDE.md`
2. Convert `/fix` and `/explain` workflows into `.claude/commands/`
3. Set up permissions and hooks

## From Windsurf / Aider / Other

### General Migration Pattern
1. Find the "rules file" → convert to `CLAUDE.md`
2. Find the "settings" → convert to `.claude/settings.json`
3. Find "custom workflows" → convert to `.claude/commands/`
4. Find "knowledge bases" → convert to `.claude/skills/`
5. Find "review tools" → convert to `.claude/agents/`
6. Install Claude Code and run `/forge` to fill gaps

## What Claude Code Has That Others Don't

| Capability | Cursor | Copilot | Claude Code |
|-----------|--------|---------|-------------|
| Deterministic hooks | ✗ | ✗ | ✓ |
| Subagent orchestration | ✗ | ✗ | ✓ |
| Isolated agent context | ✗ | ✗ | ✓ |
| Git worktree isolation | ✗ | ✗ | ✓ |
| Progressive disclosure skills | ✗ | ✗ | ✓ |
| Plugin marketplace | ✗ | ✗ | ✓ |
| Non-interactive CI mode | ✗ | ✗ | ✓ |
| Background agents | ✗ | ✗ | ✓ |
| Cross-session memory | ✗ | ✗ | ✓ |
| Per-agent permissions | ✗ | ✗ | ✓ |
