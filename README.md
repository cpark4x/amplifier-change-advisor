# Change Advisor

**Goes beyond "does this code work?" to answer "should this change exist in this product, and is this the right way to do it?"**

A multi-lens decision advisor for evaluating changes. Built for the age of AI-generated PRs — where individual velocity is high but collective coherence is fragile.

## The Problem

AI-powered development amplifies individual velocity but fragments collective coherence. Contributors arrive with polished, AI-generated PRs that may be fundamentally misaligned with your project's philosophy — because their AI optimized for their task, not your product's coherence.

Change Advisor forces the alignment conversation that velocity skipped.

## How It Works

1. **Auto-load project context** — reads your vision doc, backlog, test logs, and git history from the working directory
2. **Profile the contributor** — fetches PR history to calibrate tone (first-time vs. regular contributor)
3. **Investigate the blast radius** — what does this change actually bring in beyond the diff?
4. **Analyze through two lenses** — CTO (technical excellence) and CPO (user value and product strategy)
5. **Structure the conversation** — discussion agenda with explicit Ask: questions per finding
6. **Save the full review** — written to `docs/pr-reviews/` with no truncation

## Usage

### As a global app bundle (recommended)

```bash
amplifier run --bundle git+https://github.com/cpark4x/amplifier-change-advisor@main
```

Then in session:
```
run the change advisor on PR #10
```

Or from any project directory with the bundle already installed:
```
run the pr-review recipe on PR #5
```

### As a dependency in another project

```yaml
includes:
  - bundle: git+https://github.com/cpark4x/amplifier-change-advisor@main#subdirectory=behaviors/change-advisor.yaml
```

### Recipe context variables

| Variable | Required | Default | Purpose |
|---|---|---|---|
| `pr_number` | No | Latest open PR | Which PR to review |
| `repo` | No | Current repo | Target repository (owner/repo) |
| `project_context` | No | (auto-loaded) | Override or supplement auto-loaded context |

The recipe auto-loads project context from your working directory. Projects with `docs/01-vision/VISION.md`, `docs/BACKLOG.md`, and test logs get richer reviews. Projects without them still work.

### Output

The full review is saved to `docs/pr-reviews/` in the working directory — no truncation, no lost output.

After the recipe completes, walk through findings one at a time using each finding's **Ask:** question to open the conversation with the PR author.

## The Two Lenses

### CTO — Technical Excellence and Risk

Architecture fit, security surface, integration patterns, technical debt, operational risk, correctness.

### CPO — User Value and Product Strategy

Strategic alignment with backlog, specific user value, product completeness, user-facing failure modes, quality bar, timing, opportunity cost, product debt.

The CPO lens asks fundamentally different questions than the CTO. Where the CTO asks *how*, the CPO asks *why* and *for whom*.

## Philosophy

- **The conversation is the product** — the analysis starts a dialogue, it doesn't end it
- **Show what the diff hides** — investigate blast radius, not just code changes
- **Two lenses, not four** — CTO + CPO cover technical and product; COO/CFO added noise without signal
- **The decision-maker decides** — amplify judgment, never replace it

## Requirements

- [Amplifier](https://github.com/microsoft/amplifier) with the recipes bundle
- GitHub CLI (`gh`) authenticated

---

## Built by

**Chris Park** — Senior PM, Microsoft Office of the CTO, AI Incubation group.
Engineering degree from Waterloo. 17 years shipping product.

[LinkedIn](https://www.linkedin.com/in/chrispark1/) · [GitHub](https://github.com/cpark4x)

---

MIT License
