# Subagent Patterns — Advanced Orchestration

## The Command → Agent → Skill Pattern

The most powerful Claude Code architecture. Three layers, each with a specific role:

```
Command (entry point, user-facing)
  └── Agent (isolated context, specialized)
        └── Skill (domain knowledge, preloaded)
```

### How It Works

1. **Command** (`/review`): User invokes it. Command defines the WORKFLOW.
2. **Agent** (`code-reviewer`): Command spawns agent. Agent has its own context window, tools, and permissions.
3. **Skill** (`testing-patterns`): Preloaded into agent via `skills:` field. Agent uses skill knowledge without loading it manually.

### Implementation

```markdown
# /review command (.claude/commands/review.md)

Review the current branch changes.

1. Run `git diff main..HEAD --stat` to summarize
2. Invoke the code-reviewer agent:
   Agent(subagent_type="code-reviewer", prompt="Review all changes on this branch. Run git diff main..HEAD to see the diff. Report findings as Critical/Warning/Suggestion with file:line references.")
3. After agent completes, summarize findings
```

```yaml
# code-reviewer agent (.claude/agents/code-reviewer.md)
---
name: code-reviewer
description: "Reviews code changes for bugs and conventions."
model: opus
tools: Read, Grep, Glob, Bash(git diff *), Bash(git log *)
disallowedTools: Edit, Write
maxTurns: 15
skills:
  - testing-patterns
  - api-conventions
---

# Code Reviewer
[Instructions...]
```

```yaml
# testing-patterns skill (.claude/skills/testing-patterns/SKILL.md)
---
name: testing-patterns
description: "Testing patterns for this project."
user-invocable: false
---

# Testing Patterns
[Domain knowledge, auto-loaded into the agent's context]
```

## Parallel Agent Execution

Run multiple agents simultaneously for independent tasks:

```markdown
# /full-review command

Perform a comprehensive review with three independent agents.

Launch these agents in parallel:
1. Agent(subagent_type="code-reviewer", prompt="Review the diff for bugs and logic errors")
2. Agent(subagent_type="security-auditor", prompt="Audit the diff for security vulnerabilities")
3. Agent(subagent_type="test-generator", prompt="Generate tests for all new code in the diff")

After all complete, synthesize findings.
```

Each agent gets its own context window. They can't see each other's work.
The parent conversation collects all results.

## Agent Teams

For complex projects, define a team of agents with distinct roles:

| Agent | Model | Tools | Role |
|-------|-------|-------|------|
| `code-reviewer` | opus | Read only | Find bugs, logic errors |
| `security-auditor` | opus (max effort) | Read only | Find security vulnerabilities |
| `test-generator` | sonnet | Read + Write (tests/ only) | Write test files |
| `doc-writer` | sonnet | Read + Write (docs/ only) | Update documentation |
| `performance-analyst` | opus | Read + Bash(profiling tools) | Find performance issues |

### Tool Scoping Per Agent

```yaml
# test-generator — can only write in tests/
---
tools: Read, Grep, Glob, Write
hooks:
  PreToolUse:
    - matcher: "Write"
      hooks:
        - type: command
          command: "echo \"$CLAUDE_TOOL_INPUT_FILE_PATH\" | grep -q '^tests/' || { echo '{\"block\": true, \"message\": \"Test generator can only write to tests/\"}' >&2; exit 2; }"
          timeout: 5
---
```

## Git Worktree Isolation

Agents can run in isolated git worktrees — separate working directories that
don't affect the main checkout:

```yaml
---
name: experimental-refactor
isolation: worktree
---
```

The agent gets a fresh checkout. If it breaks things, your main working tree is safe.
Accept or discard the worktree after review.

## Agent Memory

Agents can persist knowledge across sessions:

```yaml
---
name: project-expert
memory: project
---
```

| Memory Scope | Persists Across | Shared With |
|-------------|----------------|-------------|
| `user` | All projects | Only you |
| `project` | This project | All team members |
| `local` | This machine | Only you, this project |

## Background Agents

Run agents as background tasks that don't block your conversation:

```yaml
---
name: continuous-linter
background: true
---
```

Background agents run independently. Results appear when they finish.
Good for long-running tasks (security audit, comprehensive testing).

## Anti-Patterns

| Pattern | Problem | Fix |
|---------|---------|-----|
| Agent calls agent via Bash | Fails — wrong context | Use `Agent()` tool |
| Agent reviews its own output | Conflict of interest | Separate reviewer from writer |
| Agent with no maxTurns | Runs indefinitely | Set maxTurns: 10-20 |
| All agents use Opus | Expensive, slow | Sonnet for mechanical tasks |
| Agent writes + reviews | No separation of concerns | One agent writes, another reviews |
| Vague agent prompt | Inconsistent results | Specific numbered instructions |
