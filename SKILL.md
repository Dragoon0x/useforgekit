---
name: useforgekit
description: "Generate optimal Claude Code configuration for any project. Creates CLAUDE.md, settings.json, skills, hooks, agents, and commands tailored to your stack. Run /forge to start."
version: 1.0.0
---

# useForgeKit

## When to Use

Run `/forge` when starting a new project or improving an existing Claude Code setup.
This skill generates the complete configuration: CLAUDE.md, settings.json, skills structure,
hooks, agents, and commands — all tailored to your specific stack and workflow.

Also use when:
- Setting up Claude Code for the first time on a project
- Onboarding a team to Claude Code
- Auditing an existing setup for gaps
- Migrating from another AI coding tool

## Quick Start

```
/forge
```

Claude will:
1. Detect your project type (or ask)
2. Generate CLAUDE.md tailored to your stack
3. Generate settings.json with appropriate permissions and hooks
4. Create skill stubs for your domain
5. Set up hooks for quality enforcement
6. Configure agents if your project needs them
7. Run a health check on the complete setup

## The Setup Process

### Step 1: Project Detection

Read the project root. Look for:

| File | Indicates |
|------|----------|
| `package.json` | Node.js / JavaScript / TypeScript |
| `tsconfig.json` | TypeScript specifically |
| `next.config.*` | Next.js |
| `vite.config.*` | Vite |
| `requirements.txt` / `pyproject.toml` | Python |
| `Cargo.toml` | Rust |
| `go.mod` | Go |
| `Gemfile` | Ruby |
| `pom.xml` / `build.gradle` | Java / Kotlin |
| `.swift` files | Swift / iOS |
| `docker-compose.yml` | Containerized |
| `supabase/` | Supabase backend |
| `.github/workflows/` | GitHub Actions CI/CD |

If none found, ask: "What are you building?"

### Step 2: Generate Configuration

Load the appropriate reference module based on detection:

| Reference | When to Load |
|-----------|-------------|
| references/CLAUDE-MD.md | Always — CLAUDE.md structure and content |
| references/SETTINGS.md | Always — settings.json configuration |
| references/SKILLS-ARCHITECTURE.md | When creating skills |
| references/HOOKS.md | Always — hook setup |
| references/AGENTS.md | When project needs subagents |
| references/COMMANDS.md | Always — slash command setup |
| references/PERMISSIONS.md | Always — permission strategy |
| references/CONTEXT-WINDOW.md | Always — token budget management |
| references/PROJECT-TEMPLATES.md | Based on detected stack |
| references/ANTI-PATTERNS.md | Always — what to avoid |
| references/DECISION-TREE.md | Always — what goes where |
| references/WORKFLOWS.md | Based on project complexity |
| references/GIT-STRATEGY.md | Always — what to commit |
| references/TEAM-SETUP.md | When team size > 1 |
| references/PERFORMANCE.md | Always — speed optimization |
| references/MCP-GUIDE.md | When external tools needed |
| references/TROUBLESHOOTING.md | When auditing existing setup |
| references/HEALTH-CHECK.md | Always — final validation |
| references/PROMPTING.md | Prompt patterns, challenge prompts, voice triggers, anti-patterns |
| references/SUBAGENT-PATTERNS.md | Command→Agent→Skill, parallel agents, teams, worktrees, memory |
| references/DEBUGGING-WITH-CLAUDE.md | Iron law, 3-strike rule, systematic methodology, scope freezing |
| references/TESTING-STRATEGY.md | Test priority, AAA pattern, edge cases, coverage targets, TDD |
| references/SECURITY.md | Secret management, code security checklist, supply chain, git security |
| references/RULES-GUIDE.md | .claude/rules/ files, when rules vs skills vs CLAUDE.md |
| references/MEMORY.md | Auto-memory, managing memories, memory vs CLAUDE.md |
| references/PLUGINS.md | Installing, creating, recommending plugins |
| references/ENVIRONMENT.md | .env hierarchy, env var strategy, secrets |
| references/ADVANCED-HOOKS.md | Chained hooks, conditional hooks, Stop/SessionStart/PreCompact hooks |
| references/MONOREPO.md | CLAUDE.md hierarchy, workspace commands, package-level skills |
| references/CI-CD.md | Non-interactive mode, CI workflows, cost control |
| references/COST-OPTIMIZATION.md | Token costs, model routing, budget estimation |
| references/ERROR-RECOVERY.md | Recovery playbook, safety stack, post-incident checklist |
| references/MIGRATION.md | From Cursor, Copilot, Windsurf to Claude Code

### Step 3: Health Check

After generating, run the health check (references/HEALTH-CHECK.md) to verify
everything is correctly configured.

## Output Structure

```
your-project/
├── CLAUDE.md                         # Project config for Claude Code
├── .claude/
│   ├── settings.json                 # Hooks, permissions, env
│   ├── settings.local.json           # Personal overrides (gitignored)
│   ├── skills/
│   │   └── [project-specific]/
│   │       └── SKILL.md
│   ├── agents/
│   │   └── [role-specific].md
│   └── commands/
│       └── [workflow].md
└── .gitignore                        # Updated with Claude-specific entries
```

---
*useForgeKit by Dragoon0x. Educational and experimental. DYOR.*
