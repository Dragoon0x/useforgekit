# Plugins — Extending Claude Code

## What Plugins Are

Plugins are installable packages of skills, commands, and agents from the
community. They extend Claude Code's capabilities without manual file copying.

## Installing Plugins

```bash
# From marketplace
claude plugin install skill-name@author

# From GitHub
claude plugin install github:user/repo

# From local directory
claude plugin add /path/to/skill-directory

# List installed
claude plugin list

# Remove
claude plugin remove skill-name
```

## Plugin vs Manual Skill Install

| Method | Updates | Team Sharing | Customization |
|--------|---------|-------------|---------------|
| Plugin install | Auto-updates | Each dev installs | Fork to customize |
| Manual git clone | Manual pull | Committed to repo | Full control |
| Copy files | Never updates | Committed to repo | Full control |

## Recommended Plugins

For each project type, consider:

### All Projects
- `skill-creator` — Build your own skills interactively
- `systematic-debugging` — Structured debugging methodology
- `test-driven-development` — TDD workflow enforcement

### Web Projects
- `frontend-design` — Anti-AI-slop design patterns
- `webapp-testing` — Playwright browser testing

### API Projects
- `owasp-security` — Security review checklist
- `api-design-patterns` — REST/GraphQL conventions

## Creating a Plugin

Your skill directory IS a plugin. To share it:

1. Create a GitHub repo with SKILL.md
2. Others install: `claude plugin install github:you/your-skill`
3. Or submit to the marketplace

Structure:
```
your-skill/
├── SKILL.md              # Required
├── scripts/              # Optional
├── resources/            # Optional
└── README.md             # Recommended
```
