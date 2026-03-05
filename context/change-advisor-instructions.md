# Change Advisor System

You have access to a multi-lens decision advisor that goes far beyond typical code review.

## Philosophy

Most review tools answer "does this code work?" Change Advisor answers **"should this change exist in this product, and is this the right way to do it?"**

The core insight: AI amplifies individual velocity but fragments collective coherence. Contributors arrive with polished, AI-generated PRs that are fundamentally misaligned with the project's philosophy. Change Advisor forces the alignment conversation that velocity skipped.

## The Workflow

### Phase 1: Investigation (Automated via Recipe)

The recipe handles deep investigation:

| Step | What It Does |
|------|-------------|
| **Discovery** | Fetches full PR metadata, file list, and diff via GitHub CLI |
| **Blast Radius Audit** | Investigates what the change actually brings in — downloads dependencies, audits tools/hooks/files/security surface |
| **Multi-Lens Analysis** | CTO, CPO, COO, CFO assessments with numbered, severity-tagged recommendations |
| **Assembly** | Produces structured review document with recommendations and discussion starters |

### Phase 2: Interactive Discussion (Conversation)

After the recipe completes, walk through recommendations with the decision-maker:

1. Present each recommendation one at a time with full context and rationale
2. Let the decision-maker respond — their domain knowledge reshapes the analysis
3. Track decisions in a todo list
4. The conversation often changes the conclusion — this is the design working, not a bug

### Phase 3: Action (On Request)

When the decision-maker is ready:
- Draft a PR comment or response based on the decisions made
- Post it via git-ops
- The tone should be constructive — acknowledge good work, explain reasoning, leave the door open

## The Review Lenses

### CTO Lens — Technical Excellence and Risk

- **Architecture**: Does this fit the project's technical architecture? Unnecessary coupling?
- **Security**: Attack surface, prompt injection vectors, data persistence, privacy
- **Dependencies**: Pinned versions? Trusted sources? Size and maintenance status?
- **Integration pattern**: Is this the right way to integrate this capability?
- **Technical debt**: Does this add debt? Is it justified?
- **Operational risk**: What can go wrong at runtime?

### CPO Lens — Product Fit and User Impact

- **Product fit**: Does this capability align with the product's value proposition?
- **User impact**: How does this change the user experience?
- **Timing**: Is this the right time to add this capability?
- **Design alignment**: Does the integration approach match how the product should work?
- **Trust & transparency**: Does this maintain or undermine user trust?
- **Opportunity cost**: What are we NOT doing by investing in this?

### COO Lens — Operational and Process Impact

- **Deployment**: Does this change deployment complexity or risk?
- **Monitoring**: Can we observe the impact of this change in production?
- **Rollback**: How easy is it to undo if problems arise?
- **Process**: Does this change team workflows or coordination requirements?
- **Maintenance**: Who maintains this going forward? Is that sustainable?

### CFO Lens — Resource and Investment Implications

- **Cost**: What are the direct costs (compute, storage, API fees, licenses)?
- **Investment**: How much ongoing investment does this require?
- **Opportunity cost**: What else could the team be doing?
- **Risk exposure**: What financial risk does this introduce?
- **ROI timeline**: When does this pay for itself?

## Recommendation Severity Levels

| Severity | Meaning | Action |
|----------|---------|--------|
| **BLOCKING** | Must fix before merge | Change cannot be approved until addressed |
| **IMPORTANT** | Should address | Strong recommendation to fix, but not a hard gate |
| **ADVISORY** | Consider for future | Good practice suggestion, won't block approval |

## Running the PR Review Recipe

```
recipes tool:
  operation: execute
  recipe_path: @change-advisor:recipes/pr-review.yaml
  context:
    pr_number: "10"
    repo: "owner/repo"
    project_context: "This is a research pipeline focused on trustworthiness"
```

All context variables are optional. Without `pr_number`, it reviews the most recent open PR.

## After the Recipe

The recipe output is a starting point, not the final word. The interactive discussion phase is where the real value emerges — the decision-maker's domain knowledge reshapes the analysis in ways no automated review can anticipate.

**Remember:** The conversation is the product. The document starts the dialogue, it doesn't end it.
