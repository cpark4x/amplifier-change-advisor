# Change Advisor

**Goes beyond "does this code work?" to answer "should this change exist in this product, and is this the right way to do it?"**

A multi-lens decision advisor for evaluating changes. Built for the age of AI-generated PRs — where individual velocity is high but collective coherence is fragile.

## The Problem

AI-powered development amplifies individual velocity but fragments collective coherence. Contributors arrive with polished, AI-generated PRs that may be fundamentally misaligned with your project's philosophy — because their AI optimized for their task, not your product's coherence.

Change Advisor forces the alignment conversation that velocity skipped.

## How It Works

1. **Investigate the blast radius** — what does this change actually bring in beyond the diff?
2. **Analyze through multiple lenses** — CTO, CPO, COO, CFO perspectives you might not naturally have
3. **Structure the conversation** — numbered recommendations discussed one-by-one with the decision-maker
4. **The decision-maker decides** — the tool amplifies judgment, never replaces it

## Usage

### V1: PR Review

```bash
amplifier run --bundle git+https://github.com/cpark4x/amplifier-change-advisor@main
```

Then in session:
```
run the pr-review recipe on PR #10
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
| `project_context` | No | (none) | Project description for reviewer context |

## Philosophy

- **The conversation is the product** — the analysis starts a dialogue, it doesn't end it
- **Show what the diff hides** — investigate blast radius, not just code changes
- **Lenses over opinions** — structured perspectives, not vague concerns
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
