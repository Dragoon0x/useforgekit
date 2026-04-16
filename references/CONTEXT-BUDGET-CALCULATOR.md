# Context Budget Calculator — Exact Token Costs

## Token Costs by Action

| Action | Input Tokens | Output Tokens | Total |
|--------|-------------|---------------|-------|
| Load CLAUDE.md (100 lines) | 250 | 0 | 250 |
| Load CLAUDE.md (200 lines) | 500 | 0 | 500 |
| Load Skill SKILL.md (200 lines) | 600 | 0 | 600 |
| Load Skill SKILL.md (500 lines) | 1,500 | 0 | 1,500 |
| Load Agent definition | 200-400 | 0 | 200-400 |
| Read file (100 lines) | 400 | 0 | 400 |
| Read file (500 lines) | 2,000 | 0 | 2,000 |
| Read file (2000 lines) | 8,000 | 0 | 8,000 |
| `git diff` (small PR, ~50 lines) | 200 | 0 | 200 |
| `git diff` (medium PR, ~300 lines) | 1,200 | 0 | 1,200 |
| `git diff` (large PR, ~1000 lines) | 4,000 | 0 | 4,000 |
| `git log` (20 commits) | 800 | 0 | 800 |
| `npm test` (all pass, brief output) | 200 | 0 | 200 |
| `npm test` (failures with traces) | 2,000-10,000 | 0 | 2,000-10,000 |
| `npm run build` (success) | 100-300 | 0 | 100-300 |
| `npm run build` (errors) | 500-3,000 | 0 | 500-3,000 |
| Directory listing | 200-500 | 0 | 200-500 |
| User message (average) | 50-200 | 0 | 50-200 |
| Claude response (short) | 0 | 100-300 | 100-300 |
| Claude response (medium, with code) | 0 | 500-2,000 | 500-2,000 |
| Claude response (long, full file) | 0 | 2,000-8,000 | 2,000-8,000 |

## Budget Planning

### Context Window Sizes

| Model | Context Window | Usable (before compaction) |
|-------|---------------|--------------------------|
| Opus 4.6 | 200K tokens | ~160K (compact at 80%) |
| Sonnet 4.6 | 200K tokens | ~160K (compact at 80%) |
| Haiku 4.5 | 200K tokens | ~160K (compact at 80%) |

### Example Session Budget

**Simple bug fix session (~15K tokens total):**
```
CLAUDE.md load:                500 tokens
Read 3 source files:         3,000 tokens
git diff:                      500 tokens
Claude analysis + fix:       3,000 tokens
Write test:                  1,500 tokens
Run tests (pass):              200 tokens
Claude commit message:         300 tokens
Conversation overhead:       1,000 tokens
─────────────────────────────────────────
Total:                      ~10,000 tokens
Budget remaining:           ~150,000 tokens ✓
```

**Complex feature session (~80K tokens total):**
```
CLAUDE.md load:                500 tokens
2 skills loaded:             3,000 tokens
Read 10 source files:       15,000 tokens
git diff (large):            4,000 tokens
Claude analysis + planning:  8,000 tokens
Write 5 new files:          15,000 tokens
Edit 8 existing files:      12,000 tokens
Run tests (multiple times):  3,000 tokens
Claude conversation:        15,000 tokens
─────────────────────────────────────────
Total:                      ~75,000 tokens
Budget remaining:           ~85,000 tokens ⚠ (compact soon)
```

**Multi-agent session (~120K tokens total):**
```
Main conversation:          40,000 tokens
Code reviewer agent:        30,000 tokens (own context)
Test generator agent:       25,000 tokens (own context)
Doc writer agent:           15,000 tokens (own context)
─────────────────────────────────────────
Total across all contexts: ~110,000 tokens
Main context remaining:    ~120,000 tokens ✓
```

Note: Subagent tokens don't count against the main context window.
This is why subagents are powerful — they don't consume your main budget.

## Optimization Strategies

| Strategy | Tokens Saved | When |
|----------|-------------|------|
| Read lines 10-50 vs full file | 1,500-7,000 | Large files |
| `git diff --stat` before full diff | 800-3,800 | Before reviewing |
| Suppress build output (`--silent`) | 100-3,000 | When build succeeds |
| Use subagent for review | 30,000 (from main) | Every review |
| `/compact` at 50% vs auto at 100% | Preserves ~20% more context | Always |
| `/clear` between tasks | Full budget reset | Task switching |
| Smaller SKILL.md with sub-files | 500-4,000 per skill | Progressive disclosure |
