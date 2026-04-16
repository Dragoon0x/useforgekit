# Anti-Patterns — What Goes Wrong

## CLAUDE.md Anti-Patterns

| # | Pattern | Symptom | Fix |
|---|---------|---------|-----|
| 1 | 500+ line CLAUDE.md | Claude ignores half your rules | Cut to 200 lines, move rest to skills |
| 2 | Paragraph instructions | Claude misses specific rules | Use bullet points, one rule per line |
| 3 | "Please try to..." | Weak compliance | "MUST" or "NEVER" |
| 4 | Duplicate of README | Wasted tokens on non-actionable text | CLAUDE.md is for Claude, README is for humans |
| 5 | Full API docs inline | Burns 5K+ tokens every session | Move to a skill |
| 6 | Project history/changelog | Irrelevant to coding | Delete |
| 7 | Links to external docs | Claude can't browse them | Inline the key points |
| 8 | "Be careful with..." | Vague, unenforceable | Hook that blocks the dangerous action |
| 9 | Contradictory rules | Claude picks whichever is convenient | Remove the contradiction |
| 10 | Rules Claude always breaks | Rule is too long or buried | Move to top, add "IMPORTANT:" prefix, or use a hook |

## Skills Anti-Patterns

| # | Pattern | Symptom | Fix |
|---|---------|---------|-----|
| 11 | No description in frontmatter | Skill never auto-fires | Write a trigger-rich description |
| 12 | 2000+ line SKILL.md | Context bloat when loaded | Split into sub-files (progressive disclosure) |
| 13 | Skill duplicates CLAUDE.md content | Token waste, contradictions | Single source of truth |
| 14 | Skill with no examples | Claude interprets rules differently than intended | Add good/bad examples |
| 15 | Every skill set to `disable-model-invocation: true` | Skills never auto-load | Enable auto for high-value skills |

## Hook Anti-Patterns

| # | Pattern | Symptom | Fix |
|---|---------|---------|-----|
| 16 | Hook with no timeout | Blocks Claude indefinitely | Always set `timeout` |
| 17 | Hook that modifies code AND validates | Hard to debug failures | Separate: one hook formats, another validates |
| 18 | PreToolUse hook on `*` (everything) | Runs on every single action, slows down | Use specific matchers |
| 19 | Hook that exits 1 on expected conditions | Misleading error messages | Use exit 2 + JSON for intentional blocks |
| 20 | Hook that depends on global state | Breaks in different environments | Use project-relative paths |

## Agent Anti-Patterns

| # | Pattern | Symptom | Fix |
|---|---------|---------|-----|
| 21 | Agent that can Edit AND review | Conflict of interest (marks own work as good) | Read-only for reviewers |
| 22 | Agent invoked via Bash | Fails silently, wrong context | Use `Agent()` tool |
| 23 | Agent with no maxTurns | Runs forever on complex tasks | Set maxTurns: 10-20 |
| 24 | General-purpose agent | Does everything poorly | Feature-specific agents |
| 25 | Agent without skill preloads | Reinvents patterns each time | Preload domain skills |

## Permission Anti-Patterns

| # | Pattern | Symptom | Fix |
|---|---------|---------|-----|
| 26 | Allow `Bash(*)` | Any command runs without approval | Allowlist specific commands |
| 27 | Deny nothing | Destructive commands run freely | Deny rm -rf, force-push, reset --hard |
| 28 | Over-locked permissions | Constant approval prompts, slow | Allow read + common dev commands |
| 29 | Permissions in CLAUDE.md | Advisory, gets ignored | Enforce via settings.json + hooks |
| 30 | Same permissions for all agents | Security-critical agents can write | Per-agent tool restrictions |
