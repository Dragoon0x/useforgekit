# Daily Routines — Productive Claude Code Workflows

## Morning Startup (5 minutes)

```
1. Open project
2. git fetch origin && git status
3. Check for new issues: gh issue list
4. Review yesterday's PR status: gh pr status
5. Plan today's focus: "Today I'm working on [X]"
```

### Morning Prompt
```
"Good morning. I'm working on [project].
Today's focus: [feature/bug/task].
Read CLAUDE.md and the relevant source files.
Let's start with [specific first step]."
```

## Mid-Session Hygiene

| Context Level | Action |
|--------------|--------|
| < 30% | Keep going |
| 30-50% | Consider `/compact` if switching subtasks |
| 50-70% | `/compact` now |
| 70-90% | `/compact` immediately, or `/clear` if switching tasks |
| > 90% | `/clear` — auto-compaction is too aggressive |

## End of Day (3 minutes)

```
1. Commit all work: /commit
2. Push branch: git push
3. If feature is done: /pr
4. Note what's next: leave a comment in the issue or a TODO
5. /clear
```

### End-of-Day Prompt
```
"Let's wrap up. Commit everything with a proper message.
Push to the remote. If there are any TODOs or FIXMEs I added today, list them."
```

## Weekly Review (15 minutes)

```
1. git log --since="7 days ago" --oneline — what shipped
2. gh issue list --state closed — what got resolved
3. gh pr list --state merged — what got merged
4. Review: am I spending time on the right things?
5. Plan next week's priorities
```
