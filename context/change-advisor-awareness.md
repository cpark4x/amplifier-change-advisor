# Change Advisor

You have access to a **multi-lens decision advisor** that helps evaluate changes to your
product through structured investigation and dialogue.

## When to Use

- User asks to review a PR, RFC, or proposed change
- User wants to understand the full blast radius of a change before approving
- User wants CTO/CPO perspective on a proposed change
- User asks about risk assessment, alignment, or dependency analysis
- External contribution arrives that needs alignment evaluation

## How to Run

```
operation: execute
recipe_path: @change-advisor:recipes/pr-review.yaml
context:
  pr_number: "10"      # optional — defaults to latest open PR
  repo: "owner/repo"   # optional — defaults to current repo
```

The recipe auto-loads project context from the working directory (vision doc, backlog,
test logs, git history). No manual `project_context` parameter needed for most runs.

## Context Variables

| Variable | Required | Default | Purpose |
|----------|----------|---------|---------|
| `pr_number` | No | Latest open PR | Which PR to review |
| `repo` | No | Current repo | Target repository (owner/repo format) |
| `project_context` | No | (auto-loaded) | Override or supplement auto-loaded context |

## What It Produces

A 7-step pipeline that produces a discussion agenda review saved to `docs/pr-reviews/`:

1. **PR Discovery** — Fetches metadata, files, and full diff
2. **Load Project Context** — Auto-loads vision, backlog, test logs from working directory
3. **Load Contributor Context** — Fetches contributor PR history, characterizes familiarity
4. **Blast Radius Audit** — Investigates what the PR actually brings in beyond the diff
5. **Multi-Lens Analysis** — CTO and CPO assessments with Ask: questions per finding
6. **Assembled Review** — Discussion agenda, one finding at a time
7. **Save** — Full review written to `docs/pr-reviews/` — no truncation

## Philosophy

- **The conversation is the product** — the document starts the dialogue, it does not end it
- **Show what the diff hides** — blast radius, not just code changes
- **CTO + CPO lenses** — technical excellence and user/product strategy
- **The decision-maker decides** — the tool amplifies judgment, never replaces it
