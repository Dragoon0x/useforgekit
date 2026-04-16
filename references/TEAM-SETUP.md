# Team Setup — Multi-Developer Configuration

## What to Share vs Keep Personal

| File | Share (commit) | Personal (gitignore) |
|------|---------------|---------------------|
| CLAUDE.md | ✓ | |
| .claude/settings.json | ✓ | |
| .claude/settings.local.json | | ✓ |
| .claude/skills/ | ✓ | |
| .claude/agents/ | ✓ | |
| .claude/commands/ | ✓ | |
| .claude/rules/ | ✓ | |

## Onboarding New Team Members

1. They install Claude Code
2. They clone the repo (includes CLAUDE.md, settings, skills, agents, commands)
3. They create `.claude/settings.local.json` for personal preferences
4. Run `/onboard` command to learn the codebase
5. They're ready

### /onboard Command

```markdown
# Onboard

Explore this codebase and explain the architecture to a new team member.

1. Read CLAUDE.md and README.md
2. List the directory structure
3. Identify the tech stack from package.json/pyproject.toml
4. Find the entry point and trace a typical request
5. List available Claude Code skills, agents, and commands
6. Summarize: what this project does, how it's organized, where to start
```

## Conflicts Between Team Members

When two developers have different preferences:

| Preference | Where It Goes |
|-----------|-------------|
| Code style (team agreement) | `.claude/settings.json` (shared) |
| Model preference | `.claude/settings.local.json` (personal) |
| Permission level | `.claude/settings.local.json` (personal) |
| Hook behavior | `.claude/hooks/config/hooks-config.local.json` |
| Thinking mode | `.claude/settings.local.json` (personal) |
