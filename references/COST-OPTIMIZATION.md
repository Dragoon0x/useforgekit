# Cost Optimization — Managing API Spend

## Token Costs (Approximate, 2026)

| Model | Input (per 1M tokens) | Output (per 1M tokens) |
|-------|----------------------|----------------------|
| Claude Opus 4.6 | $15 | $75 |
| Claude Sonnet 4.6 | $3 | $15 |
| Claude Haiku 4.5 | $0.80 | $4 |

## Cost Reduction Strategies

### 1. Model Routing

| Task | Model | Savings vs Opus |
|------|-------|----------------|
| Simple edits | Sonnet | 80% |
| Test generation | Sonnet | 80% |
| Formatting | Haiku | 95% |
| Code review | Opus | 0% (worth it) |
| Architecture | Opus | 0% (worth it) |
| Debugging | Opus | 0% (worth it) |
| Subagent tasks | Haiku or Sonnet | 80-95% |

### 2. Context Efficiency

| Action | Token Cost | Optimization |
|--------|-----------|-------------|
| Read whole file (2000 lines) | ~8,000 | Read specific lines: ~500 |
| Full git diff | ~5,000 | `git diff --stat` first: ~200 |
| Full test output | ~5,000 | Filter to failures: ~500 |
| Full npm install output | ~3,000 | Suppress with `--silent`: ~0 |
| Skill loaded (large) | ~5,000 | Progressive disclosure: ~1,000 |

### 3. Session Management

| Strategy | Savings |
|----------|---------|
| `/clear` between unrelated tasks | 30-50% |
| `/compact` at 50% (not 90%) | 20-30% |
| Subagents for independent tasks | 40-60% |
| Avoid re-reading files already in context | 10-20% |

### 4. Agent Model Selection

```yaml
# Expensive (every task uses Opus)
---
model: opus  # $15/M input, $75/M output
---

# Optimized
---
model: sonnet  # $3/M input, $15/M output — 80% cheaper
---

# For mechanical tasks
---
model: haiku  # $0.80/M input, $4/M output — 95% cheaper
---
```

## Monthly Budget Estimation

| Usage Level | Daily Sessions | Avg Tokens/Session | Monthly Cost |
|------------|---------------|-------------------|-------------|
| Light | 2-3 | 50K | $15-30 |
| Moderate | 5-8 | 100K | $50-120 |
| Heavy | 10-15 | 200K | $150-400 |
| Power user | 20+ | 300K+ | $400+ |

Most of the cost is in output tokens (Claude writing code).
Reading files is input tokens (cheaper).
