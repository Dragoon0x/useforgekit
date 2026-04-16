# Performance — Speed & Token Optimization

## Speed Tips

| Tip | Impact | How |
|-----|--------|-----|
| Use Sonnet for simple tasks | 3-5x faster | `/model sonnet` or per-agent `model: sonnet` |
| Pre-allow common commands | Eliminates approval prompts | settings.json permissions.allow |
| Auto-format via hook | Eliminates "please format" prompts | PostToolUse hook |
| Use subagents for parallel work | Run tasks simultaneously | Multiple `Agent()` calls |
| `/compact` at 50% | Prevents auto-compaction (lossy) | Manual discipline |
| Keep CLAUDE.md under 200 lines | Faster session start, better adherence | Prune regularly |
| Use targeted file reads | Fewer tokens consumed | `Read file lines 10-30` |
| `/clear` between unrelated tasks | Fresh context = better results | Discipline |

## Token Efficiency

| Optimization | Tokens Saved |
|-------------|-------------|
| Short CLAUDE.md (200 vs 500 lines) | ~750 per session |
| Progressive disclosure in skills | ~2,000-5,000 per skill load |
| Subagent for review (own context) | ~10,000-50,000 |
| Targeted reads vs whole files | ~1,000-5,000 per read |
| Filtered command output | ~500-5,000 per command |
| Manual compact at 50% | Preserves ~20% more detail |

## Model Selection by Task

| Task | Best Model | Why |
|------|-----------|-----|
| Architecture decisions | Opus | Deepest reasoning |
| Complex debugging | Opus | Best at tracing data flow |
| Code review | Opus | Catches subtle bugs |
| Simple refactoring | Sonnet | Fast, accurate for patterns |
| Test generation | Sonnet | Mechanical, pattern-based |
| Documentation | Sonnet | Clear writing, fast |
| Formatting / linting | Haiku | Mechanical task |
| Simple lookups | Haiku | Fast, cheap |

## The 50% Rule

Monitor context usage. When you're at ~50% of the context window:
1. Run `/compact` manually
2. If the task is done, `/clear` before starting the next
3. If quality drops post-compact, `/clear` and restart with fresh context

Never let context hit 100%. Auto-compaction at 100% is aggressive and loses
more detail than manual compaction at 50%.
