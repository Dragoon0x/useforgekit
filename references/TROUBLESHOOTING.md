# Troubleshooting

## Common Issues

### Claude ignores CLAUDE.md instructions

| Cause | Fix |
|-------|-----|
| CLAUDE.md too long (500+ lines) | Cut to < 200 lines |
| Rule buried in paragraph | Use bullet points, one rule per line |
| Vague language ("try to", "please") | Use "MUST" and "NEVER" |
| Contradictory rules | Remove the contradiction |
| Context window full | `/compact` or `/clear` |

### Skill never auto-fires

| Cause | Fix |
|-------|-----|
| No description in frontmatter | Write a trigger-rich description |
| Description doesn't match user language | Add more keywords |
| `disable-model-invocation: true` | Set to false (or remove) |
| Skill in wrong directory | Must be `.claude/skills/<name>/SKILL.md` |

### Hook not running

| Cause | Fix |
|-------|-----|
| Wrong matcher pattern | Test with `"*"` first, then narrow |
| Script not executable | `chmod +x script.sh` |
| No timeout set | Add `"timeout": 10` |
| Script path wrong | Use absolute path or project-relative |
| JSON syntax error in settings.json | Validate JSON |

### Agent runs forever

| Cause | Fix |
|-------|-----|
| No maxTurns | Add `maxTurns: 10-20` |
| Task too vague | More specific prompt |
| Agent loops on errors | Add `effort: medium` to limit thinking |

### Permission prompts won't stop

| Cause | Fix |
|-------|-----|
| Command not in allow list | Add exact command pattern |
| Wildcard mismatch | `Bash(npm run *)` not `Bash(npm *)` |
| Edit/Write not allowed | Add `Edit` and `Write` to allow |

### /doctor

Run `/doctor` for built-in diagnostics. It checks:
- Claude Code version
- Settings file validity
- Skill discovery
- MCP server health
- Hook configuration
- Permission status
