# CI/CD — Claude Code in Pipelines

## Non-Interactive Mode

Run Claude Code in CI/CD with the `-p` flag (non-interactive):

```bash
claude -p "Run the test suite and report results" --output-format json
```

### Flags for CI

| Flag | Purpose |
|------|---------|
| `-p "prompt"` | Non-interactive mode with prompt |
| `--output-format json` | Machine-parseable output |
| `--model sonnet` | Use cheaper model for CI tasks |
| `--max-turns 5` | Limit iterations |
| `--no-input` | Prevent stdin reads (hangs in CI) |

## CI Use Cases

### Automated Code Review

```yaml
# .github/workflows/review.yml
name: AI Code Review
on: [pull_request]

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      - name: Claude Review
        run: |
          claude -p "Review the diff between main and HEAD.
          Report: Critical/Warning/Suggestion with file:line.
          Output JSON format." --output-format json > review.json
```

### Automated Test Generation

```yaml
      - name: Generate Tests
        run: |
          claude -p "Read the files changed in this PR.
          Generate tests for any new exported functions
          that don't have tests yet.
          Write tests to the appropriate test directories."
```

### Documentation Check

```yaml
      - name: Check Docs
        run: |
          claude -p "Check if README.md and API docs are
          up to date with the code changes in this PR.
          Report any documentation that needs updating."
```

## Cost Control in CI

| Strategy | How |
|----------|-----|
| Use Sonnet (not Opus) | `--model sonnet` |
| Limit turns | `--max-turns 5` |
| Only run on PRs (not every commit) | `on: [pull_request]` |
| Cache Claude responses | Store review results, skip if diff unchanged |
| Skip for small changes | `if: github.event.pull_request.changed_files > 3` |
