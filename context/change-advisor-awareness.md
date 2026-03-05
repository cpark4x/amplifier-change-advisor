# Change Advisor

You have access to a **multi-lens decision advisor** that helps evaluate changes to your product through structured investigation and dialogue.

## When to Use

- User asks to review a PR, RFC, or proposed change
- User wants to understand the full blast radius of a change before approving
- User wants CTO/CPO/COO/CFO perspective on a proposed change
- User asks about risk assessment, alignment, or dependency analysis
- External contribution arrives that needs alignment evaluation

## How to Run

### PR Review (V1 Scenario)

Execute the recipe via the recipes tool:

```
operation: execute
recipe_path: @change-advisor:recipes/pr-review.yaml
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

The output is designed as a **starting point for interactive discussion** — walk through recommendations one by one with the decision-maker. The conversation is the product, not the document.

## Philosophy

- **The conversation is the product** — the document starts the dialogue, it doesn't end it
- **Show what the diff hides** — investigate blast radius, not just code changes
- **Lenses over opinions** — structured CTO/CPO/COO/CFO perspectives, not vague concerns
- **The decision-maker decides** — the tool amplifies judgment, never replaces it
