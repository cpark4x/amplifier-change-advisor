# amplifier-pr-review

Structured multi-lens PR review workflow for Amplifier.

Goes beyond "does this code work?" to answer **"should this change exist in this product, and is this the right way to do it?"**

## What It Does

A 4-step recipe pipeline:

1. **PR Discovery** - Fetches metadata, files, and full diff via GitHub CLI
2. **Blast Radius Audit** - Investigates what the PR actually brings in beyond the diff (dependencies, tools, hooks, security surface)
3. **Multi-Lens Analysis** - CTO perspective (technical risk, architecture, security) + CPO perspective (product fit, user impact, timing)
4. **Assembled Review** - Clean document with executive summary, numbered recommendations, verdict, and discussion starters

The output is a starting point for interactive discussion, not an automated approval gate.

## Usage

### Standalone

```bash
amplifier run --bundle git+https://github.com/cpark4x/amplifier-pr-review@main
```

Then in session:
```
run the pr-review recipe on PR #10
```

### As a dependency in another project

Add to your project's `bundle.md` includes:

```yaml
includes:
  - bundle: git+https://github.com/cpark4x/amplifier-pr-review@main#subdirectory=behaviors/pr-review.yaml
```

Then in any session:
```
run the pr-review recipe on PR #10
```

### Recipe context variables

| Variable | Required | Default | Purpose |
|----------|----------|---------|---------|
| `pr_number` | No | Latest open PR | Which PR to review |
| `repo` | No | Current repo | Target repository (owner/repo) |
| `project_context` | No | (none) | Project description for reviewer context |

## Requirements

- [Amplifier](https://github.com/microsoft/amplifier) with the recipes bundle
- GitHub CLI (`gh`) authenticated

## License

MIT
