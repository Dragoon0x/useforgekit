# YAML Frontmatter Reference — Every Field

## Skill Frontmatter (.claude/skills/*/SKILL.md)

| Field | Type | Default | Required | Purpose |
|-------|------|---------|----------|---------|
| `name` | string | dir name | No | Display name and `/slash-command` trigger |
| `description` | string | — | **Yes** | When to auto-invoke. Include trigger keywords. CRITICAL for discovery. |
| `argument-hint` | string | — | No | Autocomplete hint shown after `/name`. Example: `[issue-number]` |
| `disable-model-invocation` | bool | `false` | No | If `true`, Claude never auto-loads this skill. Manual `/name` only. |
| `user-invocable` | bool | `true` | No | If `false`, hidden from `/` menu. Background knowledge only. |
| `allowed-tools` | string | All | No | Comma-separated tool allowlist when skill is active. Example: `Read, Grep, Glob` |
| `model` | string | `inherit` | No | Model override when skill is active: `haiku`, `sonnet`, `opus` |
| `context` | string | — | No | `fork` to run in an isolated subagent context |
| `agent` | string | `general-purpose` | No | Subagent type for `context: fork` |
| `hooks` | object | — | No | Lifecycle hooks scoped to this skill |

## Agent Frontmatter (.claude/agents/*.md)

| Field | Type | Default | Required | Purpose |
|-------|------|---------|----------|---------|
| `name` | string | filename | No | Agent identifier |
| `description` | string | — | **Yes** | When to invoke. Use "PROACTIVELY" for auto-invocation. |
| `model` | string | `inherit` | No | `haiku`, `sonnet`, `opus`, `inherit` |
| `tools` | string | All (inherited) | No | Comma-separated allowlist. Supports `Agent(type)` syntax. |
| `disallowedTools` | string | — | No | Tools to explicitly deny (removed from inherited/specified list) |
| `permissionMode` | string | — | No | `plan`, `acceptEdits`, `bypassPermissions` |
| `maxTurns` | int | — | **Recommended** | Max agentic turns before stopping. Set this. |
| `skills` | list | — | No | Skill names to preload into agent context |
| `mcpServers` | list | — | No | MCP servers available to this agent |
| `hooks` | object | — | No | Lifecycle hooks scoped to this agent |
| `memory` | string | — | No | Persistent memory scope: `user`, `project`, `local` |
| `background` | bool | `false` | No | Run as background task (non-blocking) |
| `effort` | string | inherit | No | `low`, `medium`, `high`, `max` |
| `isolation` | string | — | No | `worktree` for git worktree isolation |
| `color` | string | — | No | CLI output color for visual distinction |

## Command Frontmatter (.claude/commands/*.md)

Commands are simpler — just markdown files with `$ARGUMENTS` support.
No YAML frontmatter required (but can use `---` for name/description).

| Field | Type | Default | Required | Purpose |
|-------|------|---------|----------|---------|
| `name` | string | filename | No | Display name for `/` menu |
| `description` | string | — | No | When to suggest this command |
| `allowed-tools` | string | All | No | Tool restrictions for this command |

## Settings.json Fields

| Field | Type | Default | Scope |
|-------|------|---------|-------|
| `model` | string | `sonnet` | Model for the session |
| `alwaysThinkingEnabled` | bool | `false` | Show thinking in interactive mode |
| `maxTurnTokens` | int | 32000 | Max output tokens per response |
| `effortLevel` | string | `auto` | `low`, `medium`, `high`, `max`, `auto` |
| `permissions.allow` | string[] | [] | Tools/commands allowed without prompting |
| `permissions.deny` | string[] | [] | Tools/commands always blocked |
| `hooks` | object | {} | Hook configurations per event |
| `env` | object | {} | Environment variables for Bash |
| `disableAllHooks` | bool | `false` | Turn off all hooks |

## Tool Names for Permissions

| Tool Name | What It Does |
|-----------|-------------|
| `Read` | Read file contents |
| `Write` | Write new files |
| `Edit` | Edit existing files |
| `Grep` | Search file contents |
| `Glob` | List files matching patterns |
| `Bash(command)` | Run a shell command |
| `Agent(type)` | Invoke a subagent |
| `Skill` | Load/invoke a skill |
| `mcp__server__tool` | MCP server tool |
