# Model Comparison — When to Use Which

## Model Capabilities

| Capability | Haiku 4.5 | Sonnet 4.6 | Opus 4.6 |
|-----------|-----------|-----------|----------|
| Speed | ★★★★★ | ★★★☆☆ | ★★☆☆☆ |
| Cost | ★★★★★ | ★★★☆☆ | ★☆☆☆☆ |
| Code quality | ★★★☆☆ | ★★★★☆ | ★★★★★ |
| Reasoning | ★★☆☆☆ | ★★★★☆ | ★★★★★ |
| Following instructions | ★★★☆☆ | ★★★★☆ | ★★★★★ |
| Architecture decisions | ★☆☆☆☆ | ★★★☆☆ | ★★★★★ |
| Bug detection | ★★☆☆☆ | ★★★☆☆ | ★★★★★ |
| Creative solutions | ★☆☆☆☆ | ★★★☆☆ | ★★★★★ |
| Mechanical tasks | ★★★★★ | ★★★★★ | ★★★★★ |

## Task-to-Model Mapping

| Task | Model | Why |
|------|-------|-----|
| Architecture planning | **Opus** | Needs deep reasoning about trade-offs |
| Complex debugging | **Opus** | Must trace data flow, form hypotheses |
| Code review | **Opus** | Must catch subtle bugs, security issues |
| Security audit | **Opus** (max effort) | Must think adversarially |
| Feature implementation | **Sonnet** | Good code quality at 5x speed |
| Test generation | **Sonnet** | Pattern-based, well-defined output |
| Refactoring | **Sonnet** | Mechanical transformation |
| Documentation writing | **Sonnet** | Clear writing, fast |
| Simple edits (rename, format) | **Haiku** | Mechanical, no reasoning needed |
| Commit message generation | **Haiku** | Fast, formulaic |
| File/directory listing | **Haiku** | Simple lookup |
| Subagent: formatting | **Haiku** | Cheapest for mechanical tasks |
| Subagent: review | **Opus** | Review needs deep analysis |
| Subagent: test writing | **Sonnet** | Good balance |

## Cost Comparison Per Task

| Task | Opus Cost | Sonnet Cost | Haiku Cost | Recommendation |
|------|-----------|-------------|------------|----------------|
| Review 500-line diff | $1.50 | $0.30 | $0.08 | Opus (worth it) |
| Write 200-line feature | $3.00 | $0.60 | $0.16 | Sonnet (good enough) |
| Generate 10 tests | $1.00 | $0.20 | $0.05 | Sonnet |
| Format 50 files | $0.50 | $0.10 | $0.03 | Haiku |
| Debug complex issue | $5.00 | $1.00 | $0.25 | Opus (Sonnet misses root cause) |
| Write README | $0.75 | $0.15 | $0.04 | Sonnet |

## Model Routing in Settings

### Per-Session
```json
{ "model": "opus" }
```

### Per-Agent
```yaml
---
model: sonnet  # Fast agent
---
```

### Dynamic Switching
```
"Switch to Sonnet for this task"
"Use Opus for the review"
"/model haiku"
```

## The 80/20 Rule

80% of your tasks are well-served by Sonnet. 20% genuinely need Opus.
Use Opus as default ONLY if you're doing architecture, debugging, or review.
For feature implementation, Sonnet is the right choice.
