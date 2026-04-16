# Health Check — Validate Your Setup

Run this after `/forge` generates your configuration, or anytime to audit.

## Checklist

### CLAUDE.md
- [ ] Exists in project root
- [ ] Under 200 lines
- [ ] Has Quick Facts section (stack, test/lint/build commands)
- [ ] Has Architecture section (< 5 sentences + directory listing)
- [ ] Has Conventions section (specific, testable rules)
- [ ] No paragraphs (bullet points only)
- [ ] No external links (Claude can't browse)
- [ ] No duplicate of README content
- [ ] No project history or changelog
- [ ] Uses "MUST" and "NEVER" for critical rules

### settings.json
- [ ] Exists at `.claude/settings.json`
- [ ] Permissions: common dev commands allowed (test, lint, build, git)
- [ ] Permissions: destructive commands denied (rm -rf, force-push)
- [ ] Branch protection hook configured
- [ ] Auto-format hook configured (if formatter exists)
- [ ] Thinking mode enabled
- [ ] Model set appropriately

### Settings Local
- [ ] `.claude/settings.local.json` exists for personal overrides
- [ ] Added to `.gitignore`

### Skills
- [ ] Each skill has YAML frontmatter with name and description
- [ ] Descriptions include trigger keywords
- [ ] SKILL.md files under 500 lines
- [ ] Deeper content in sub-files (progressive disclosure)

### Agents
- [ ] Review agents have Edit/Write in disallowedTools
- [ ] maxTurns set on all agents
- [ ] Model specified (not relying on inherit for expensive tasks)
- [ ] Domain skills preloaded where relevant

### Commands
- [ ] Common workflows have commands (fix-issue, create-pr, deploy)
- [ ] Commands use `$ARGUMENTS` for user input
- [ ] Commands are self-contained (don't require reading docs)

### Git
- [ ] `.claude/settings.local.json` in .gitignore
- [ ] Team-shared files committed
- [ ] Personal files excluded

## Quick Validation Commands

```bash
# Check CLAUDE.md length
wc -l CLAUDE.md
# Should be < 200

# Check for settings files
ls -la .claude/settings*.json

# Check for skills
find .claude/skills -name "SKILL.md" 2>/dev/null

# Check for agents
ls .claude/agents/*.md 2>/dev/null

# Check for commands
ls .claude/commands/*.md 2>/dev/null

# Check gitignore
grep -q "settings.local.json" .gitignore && echo "✓ Local settings gitignored" || echo "✗ Add settings.local.json to .gitignore"
```
