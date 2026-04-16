# Decision Tree — What Goes Where

## The Fundamental Question

"Should this be in CLAUDE.md, a skill, an agent, a command, a hook, or a rule?"

## Decision Matrix

| The information is... | Put it in... | Example |
|-----------------------|-------------|---------|
| Needed every session, < 5 lines | **CLAUDE.md** | Test command, package manager |
| Needed every session, conventions | **CLAUDE.md** | Code style rules, naming conventions |
| Domain knowledge, loaded on demand | **Skill** | API patterns, DB optimization |
| A repeatable workflow | **Command** | `/deploy`, `/fix-issue`, `/create-pr` |
| An independent review/task | **Agent** | Code reviewer, QA tester |
| Must happen every time, zero exceptions | **Hook** | Auto-format, branch protection |
| Background knowledge, not invocable | **Rule** (`.claude/rules/`) | Style guide, architecture decisions |

## Detailed Decision Flow

```
Is this needed EVERY session?
├── YES → Is it < 5 lines?
│         ├── YES → CLAUDE.md
│         └── NO  → Is it > 50 lines?
│                   ├── YES → Skill (with user-invocable: false)
│                   └── NO  → CLAUDE.md (but keep it tight)
│
└── NO  → Does it need its own context window?
          ├── YES → Agent
          └── NO  → Is it a repeatable workflow?
                    ├── YES → Command
                    └── NO  → Is it domain knowledge?
                              ├── YES → Skill
                              └── NO  → Does it MUST happen automatically?
                                        ├── YES → Hook
                                        └── NO  → Rule (.claude/rules/)
```

## Common Mistakes

| Mistake | Why It's Bad | Correct Approach |
|---------|-------------|-----------------|
| 500-line CLAUDE.md | Claude ignores most of it | Move 80% to skills |
| API docs in CLAUDE.md | Wastes tokens every session | Skill with on-demand loading |
| Review checklist in CLAUDE.md | Only needed during review | Agent: `code-reviewer` |
| Deploy steps in CLAUDE.md | Only needed when deploying | Command: `/deploy` |
| "Please format code" in CLAUDE.md | Advisory, gets ignored | Hook: auto-format PostToolUse |
| Entire style guide in CLAUDE.md | Too long | Rule: `.claude/rules/style.md` |

## Rules (.claude/rules/)

Rules are markdown files in `.claude/rules/` that Claude loads contextually.
They're like skills but simpler — no frontmatter, no slash command.

```
.claude/rules/
├── style.md          # Code style conventions
├── architecture.md   # Architecture decisions and rationale
├── testing.md        # Testing patterns and requirements
└── security.md       # Security requirements
```

Rules are loaded based on filename matching and content relevance.
Use rules for guidelines that are too long for CLAUDE.md but don't need
the full skill structure.

## Sizing Guide

| Component | Max Size | If Over, Then... |
|-----------|---------|-----------------|
| CLAUDE.md | 200 lines | Split into rules or skills |
| Skill SKILL.md | 500 lines | Split into sub-files (patterns.md, examples.md) |
| Agent .md | 200 lines | Add skills to the agent instead |
| Command .md | 100 lines | Break into steps, use skills for knowledge |
| Rule .md | 300 lines | Split into multiple rule files |
| settings.json | No hard limit | But keep hooks < 20 |
