# Skills Architecture

## What a Skill Is

A skill is a `SKILL.md` file in `.claude/skills/<name>/` that teaches Claude a
specific workflow, domain knowledge, or methodology. Claude loads it on demand
when relevant — not every session.

## Skill Anatomy

```yaml
---
name: skill-name
description: "When to use this skill. Include trigger keywords."
argument-hint: "[optional-args]"
disable-model-invocation: false
user-invocable: true
allowed-tools: Read, Grep, Glob, Edit, Write, Bash
model: inherit
---

# Skill Title

## When to Use
- Trigger condition 1
- Trigger condition 2

## Process
1. Step 1
2. Step 2
3. Step 3

## Examples
[Show good and bad patterns]

## Anti-Patterns
[What NOT to do]
```

## Frontmatter Fields

| Field | Type | Default | Purpose |
|-------|------|---------|---------|
| `name` | string | Directory name | Display name and `/slash-command` |
| `description` | string | — | When to auto-invoke (CRITICAL for discovery) |
| `argument-hint` | string | — | Autocomplete hint: `[issue-number]` |
| `disable-model-invocation` | bool | `false` | Prevent auto-invocation (manual only) |
| `user-invocable` | bool | `true` | Show in `/` menu |
| `allowed-tools` | string | All | Tools permitted without prompting |
| `model` | string | `inherit` | Model override: haiku, sonnet, opus |
| `context` | string | — | `fork` to run in isolated subagent |
| `agent` | string | `general-purpose` | Subagent type for `context: fork` |

## The Description Is Everything

Claude uses the description to decide when to auto-load a skill. A bad description
means the skill never fires.

| Bad Description | Good Description |
|----------------|-----------------|
| "API skill" | "REST API design patterns: URL structure, status codes, pagination, error responses. Use when building or reviewing API endpoints." |
| "Testing" | "Jest testing patterns for React components. Use when writing tests, creating mocks, or debugging test failures." |
| "Database" | "PostgreSQL query optimization. Use when queries are slow, explaining query plans, or designing indexes." |

**Include trigger keywords** that a user would naturally say: "test", "API", "database",
"deploy", "style", "lint", "review".

## Progressive Disclosure

Keep SKILL.md under 500 lines. For deeper reference:

```
skill-name/
├── SKILL.md              # Entry point (< 500 lines)
├── patterns.md           # Detailed patterns (loaded on demand)
├── examples.md           # Extended examples
├── scripts/
│   └── helper.py         # Executable scripts
└── resources/
    └── template.json     # Template files
```

In SKILL.md, reference deeper files:
```markdown
For detailed migration patterns, read `patterns.md`.
For example configurations, read `examples.md`.
```

Claude loads the deeper files ONLY when needed, keeping context clean.

## Skill Categories

| Category | Example Skills | Description Pattern |
|----------|---------------|-------------------|
| Domain knowledge | `api-conventions`, `db-patterns` | "Use when working with [domain]" |
| Workflow | `fix-issue`, `create-pr`, `deploy` | "Use when [action]" |
| Quality | `code-review`, `security-audit` | "Use when reviewing or auditing" |
| Generation | `component-gen`, `test-gen` | "Use when creating [thing]" |
| Background | `style-guide` (user-invocable: false) | Passive knowledge, auto-loaded |

## Skill vs Agent vs Command

| Feature | Skill | Agent | Command |
|---------|-------|-------|---------|
| What it is | Knowledge + workflow | Specialized Claude instance | User-triggered action |
| Context | Same conversation | Forked (isolated) | Same conversation |
| Invocation | Auto or `/name` | `Agent()` call or auto | `/name` only |
| Best for | Domain patterns, workflows | Independent tasks, review | Quick actions, multi-step flows |
| State | Shares conversation | Own context window | Shares conversation |
| Tools | Configurable | Configurable | Inherits all |
