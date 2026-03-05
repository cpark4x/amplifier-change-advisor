# PR Review Workflow

You have access to a **structured PR review recipe** that performs deep investigation and multi-lens analysis of pull requests.

## When to Use

- User asks to review a PR, code review, or change review
- User wants to understand the full impact of a PR before approving
- User wants CTO/CPO perspective on a proposed change
- User asks about blast radius, risk assessment, or dependency analysis of a PR

## How to Run

Execute the recipe via the recipes tool:

```
operation: execute
recipe_path: @pr-review:recipes/pr-review.yaml
context:
  pr_number: "10"
  project_context: "Brief description of the project for reviewer context"
```

### Context Variables

| Variable | Required | Default | Purpose |
|----------|----------|---------|---------|
| `pr_number` | No | Latest open PR | Which PR to review |
| `repo` | No | Current repo | Target repository (owner/repo format) |
| `project_context` | No | (none) | Project-specific context for the reviewer |

## What It Produces

The recipe runs a 4-step pipeline and produces a structured review document:

1. **PR Discovery** - Fetches metadata, files, and full diff
2. **Blast Radius Audit** - Investigates what the PR actually brings in beyond the diff
3. **Multi-Lens Analysis** - CTO and CPO perspective assessments with numbered recommendations
4. **Assembled Review** - Clean document with executive summary, recommendations, verdict, and discussion starters

The output is designed as a **starting point for interactive discussion** - walk through recommendations one by one with the PR owner.
