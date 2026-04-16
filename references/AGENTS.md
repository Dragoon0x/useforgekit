# Agents — Specialized Claude Instances

## What Agents Are

Agents are isolated Claude instances with their own context window, tools, and
instructions. They run independently and return results to the parent conversation.

## Agent Anatomy

```yaml
---
name: code-reviewer
description: "Reviews code changes for bugs, security issues, and conventions. PROACTIVELY invoke on any PR or branch diff."
model: opus
tools: Read, Grep, Glob, Bash(git diff *)
disallowedTools: Edit, Write
permissionMode: plan
maxTurns: 15
skills:
  - testing-patterns
  - api-conventions
---

# Code Reviewer

You are a senior engineer reviewing code changes.

## Process
1. Run `git diff main..HEAD` to see changes
2. Read each modified file
3. Check for: bugs, security issues, missing tests, convention violations
4. Report findings with file:line references

## Output Format
### Critical (must fix)
- [file:line] [issue]

### Warning (should fix)
- [file:line] [issue]

### Suggestion (nice to have)
- [file:line] [suggestion]
```

## Frontmatter Fields

| Field | Type | Default | Purpose |
|-------|------|---------|---------|
| `name` | string | Filename | Agent identifier |
| `description` | string | — | When to auto-invoke (use "PROACTIVELY") |
| `model` | string | `inherit` | haiku, sonnet, opus, inherit |
| `tools` | string | All | Comma-separated allowlist |
| `disallowedTools` | string | — | Tools to explicitly deny |
| `permissionMode` | string | — | plan, acceptEdits, bypassPermissions |
| `maxTurns` | int | — | Max agentic turns |
| `skills` | list | — | Skills to preload |
| `mcpServers` | list | — | MCP servers for this agent |
| `hooks` | object | — | Lifecycle hooks scoped to agent |
| `memory` | string | — | user, project, or local |
| `background` | bool | false | Run as background task |
| `effort` | string | inherit | low, medium, high, max |
| `isolation` | string | — | "worktree" for git worktree isolation |

## Essential Agents

### Code Reviewer

```yaml
---
name: code-reviewer
description: "PROACTIVELY review code changes on any branch diff."
model: opus
tools: Read, Grep, Glob, Bash(git diff *), Bash(git log *)
disallowedTools: Edit, Write
maxTurns: 15
---
```

Read-only. Cannot modify code. Reports findings.

### Test Generator

```yaml
---
name: test-generator
description: "Generate tests for modified files."
model: sonnet
tools: Read, Grep, Glob, Write
maxTurns: 20
skills:
  - testing-patterns
---
```

Can write test files. Cannot modify source code.

### Documentation Writer

```yaml
---
name: doc-writer
description: "Update documentation to match code changes."
model: sonnet
tools: Read, Grep, Glob, Edit, Write
maxTurns: 10
---
```

### Security Auditor

```yaml
---
name: security-auditor
description: "Audit code for security vulnerabilities. Use when reviewing auth, crypto, or user input handling."
model: opus
effort: max
tools: Read, Grep, Glob, Bash(npm audit *), Bash(git log *)
disallowedTools: Edit, Write
maxTurns: 20
---
```

## When to Use Agents vs Skills

| Situation | Use Agent | Use Skill |
|-----------|----------|----------|
| Task needs its own context | ✓ | |
| Task should NOT modify source code | ✓ (deny Edit/Write) | |
| Knowledge Claude should have in-context | | ✓ |
| Independent review/audit | ✓ | |
| Domain patterns for current work | | ✓ |
| Parallel work (multiple tasks at once) | ✓ | |
| Simple workflow steps | | ✓ |

## Invoking Agents

From Claude Code or from another agent:
```
Agent(subagent_type="code-reviewer", prompt="Review the changes on this branch")
```

From a command:
```markdown
# /review command
Run the code-reviewer agent on the current branch diff.
Agent(subagent_type="code-reviewer", prompt="Review git diff main..HEAD")
```

**Agents CANNOT invoke other agents via Bash.** Use the `Agent()` tool.
