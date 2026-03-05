# PR Review System

You have access to a structured, multi-step PR review workflow that goes far beyond typical code review.

## Philosophy

Most PR reviews answer "does this code work?" This workflow answers **"should this change exist in this product, and is this the right way to do it?"**

The key insight: a 1-line diff can have massive hidden scope. A dependency addition, a bundle include, a config change - these can bring in entire systems. The blast radius audit catches what the diff hides.

## The Workflow

### Phase 1: Investigation (Automated via Recipe)

The recipe handles deep investigation:

| Step | What It Does |
|------|-------------|
| **PR Discovery** | Fetches full PR metadata, file list, and diff via GitHub CLI |
| **Blast Radius Audit** | Investigates what the PR actually brings in - downloads dependencies, audits tools/hooks/files/security surface |
| **Multi-Lens Analysis** | CTO assessment (technical risk, architecture, security) + CPO assessment (product fit, user impact, timing) |
| **Assembly** | Produces structured review document with numbered recommendations |

### Phase 2: Interactive Discussion (Conversation)

After the recipe completes, walk through recommendations with the user:

1. Present each recommendation one at a time with full context
2. Let the user respond - their domain knowledge reshapes the analysis
3. Track decisions in a todo list
4. The conversation often changes the conclusion

### Phase 3: Action (On Request)

When the user is ready:
- Draft a PR comment based on the decisions made
- Post it via git-ops

## Review Lenses

### CTO Lens

Evaluates technical excellence and operational risk:

- **Architecture**: Does this fit the project's technical architecture? Unnecessary coupling?
- **Security**: Attack surface, prompt injection vectors, data persistence, privacy
- **Dependencies**: Pinned versions? Trusted sources? Size and maintenance status?
- **Integration pattern**: Is this the right way to integrate this capability?
- **Technical debt**: Does this add debt? Is it justified?
- **Operational risk**: What can go wrong at runtime?

### CPO Lens

Evaluates product fit and user impact:

- **Product fit**: Does this capability align with the product's value proposition?
- **User impact**: How does this change the user experience?
- **Timing**: Is this the right time to add this capability?
- **Design alignment**: Does the integration approach match how the product should work?
- **Trust & transparency**: Does this maintain or undermine user trust?
- **Opportunity cost**: What are we NOT doing by investing in this?

## Recommendation Severity Levels

| Severity | Meaning | Action |
|----------|---------|--------|
| **BLOCKING** | Must fix before merge | PR cannot be approved until addressed |
| **IMPORTANT** | Should address | Strong recommendation to fix, but not a hard gate |
| **ADVISORY** | Consider for future | Good practice suggestion, won't block merge |

## Running the Recipe

```
recipes tool:
  operation: execute
  recipe_path: @pr-review:recipes/pr-review.yaml
  context:
    pr_number: "10"
    repo: "owner/repo"
    project_context: "This is a research pipeline focused on trustworthiness"
```

All context variables are optional. Without `pr_number`, it reviews the most recent open PR. Without `repo`, it uses the current repository.

## After the Recipe

The recipe output is a starting point, not the final word. The interactive discussion phase is where the real value emerges - the user's domain knowledge (workflow patterns, product vision, testing habits) reshapes the analysis in ways the automated review cannot anticipate.
