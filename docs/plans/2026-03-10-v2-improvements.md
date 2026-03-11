# Change Advisor v2.0 — Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Transform the change advisor from a context-dependent document generator into a self-loading, deeply CPO-aware review tool that works across any project — without manual scaffolding.

**Architecture:** Add 2 new bash steps (auto-load project context, contributor history); rework the CPO lens around genuine product leadership philosophy; reformat the assembled review as a discussion agenda with explicit "Ask:" questions per finding; add a file-save step; update both context files to remove COO/CFO and deepen CTO/CPO.

**Repo:** `cpark4x/amplifier-change-advisor` — work directly on main
**Version bump:** `1.0.0` → `2.0.0`

---

## What We're Changing and Why

### The 3 Core Problems This Plan Fixes

**Problem 1 — Context required manual scaffolding.**
The `project_context` parameter had to be written by hand before running the recipe. Without it, the review was generic. With it, it was powerful. The recipe should load project context automatically from the working directory.

**Problem 2 — The CPO lens was CTO-lite.**
The current CPO questions ("Does this align with the value proposition?") produce findings that are just softer CTO findings. A real CPO asks fundamentally different questions — about user value, product completeness, backlog promises, timing, and opportunity cost. Product people don't look at PRs. This lens should produce output they can act on.

**Problem 3 — The output was a document, not a conversation.**
The assembled review reads like a report. The tool's own philosophy says "the conversation is the product." The output should be a discussion agenda with explicit questions the reviewer poses to the PR author — one per finding.

---

## New Recipe Step Order

```
1. discover_pr               (existing — unchanged)
2. load_project_context      (NEW bash step)
3. load_contributor_context  (NEW bash step)
4. blast_radius_audit        (existing — prompt updated)
5. multi_lens_analysis       (existing — CPO lens major rework)
6. assemble_review           (existing — discussion agenda format)
7. save_review               (NEW bash step)
```

---

## Task 1: Add `load_project_context` step to recipe

**File:** `recipes/pr-review.yaml`

Insert this step after `discover_pr` (after the existing step, before `blast_radius_audit`):

```yaml
  - id: load_project_context
    type: bash
    timeout: 30
    output: project_context_data
    command: |
      set -euo pipefail

      CONTEXT=""

      # Vision doc — try common locations
      for VISION_PATH in "docs/01-vision/VISION.md" "docs/VISION.md" "VISION.md"; do
        if [ -f "$VISION_PATH" ]; then
          CONTEXT="${CONTEXT}## PRODUCT VISION\n$(cat $VISION_PATH)\n\n"
          break
        fi
      done

      # Backlog
      for BACKLOG_PATH in "docs/BACKLOG.md" "BACKLOG.md"; do
        if [ -f "$BACKLOG_PATH" ]; then
          CONTEXT="${CONTEXT}## PRODUCT BACKLOG\n$(cat $BACKLOG_PATH)\n\n"
          break
        fi
      done

      # Most recent test log
      LATEST_LOG=$(find docs/test-log -name "*.md" ! -name "README.md" 2>/dev/null | sort | tail -1)
      if [ -n "$LATEST_LOG" ]; then
        CONTEXT="${CONTEXT}## LATEST TEST LOG ($(basename $LATEST_LOG))\n$(cat $LATEST_LOG)\n\n"
      fi

      # README as fallback if neither vision nor backlog found
      if [ -z "$CONTEXT" ] && [ -f "README.md" ]; then
        CONTEXT="${CONTEXT}## README\n$(cat README.md)\n\n"
      fi

      # Recent git history — always useful
      GIT_LOG=$(git log --oneline -20 2>/dev/null || echo "No git history available")
      CONTEXT="${CONTEXT}## RECENT COMMITS (last 20)\n${GIT_LOG}\n\n"

      # Merge with manually provided project_context if any (backwards compatibility)
      if [ -n "{{project_context}}" ]; then
        CONTEXT="${CONTEXT}## ADDITIONAL CONTEXT (provided manually)\n{{project_context}}\n\n"
      fi

      # If nothing found at all
      if [ -z "$CONTEXT" ]; then
        CONTEXT="No project documentation found in working directory. Review will proceed without project context."
      fi

      printf '%s' "$CONTEXT"
```

**Steps:**
1. Read `recipes/pr-review.yaml` in full
2. Insert `load_project_context` step after `discover_pr`, before `blast_radius_audit`
3. Verify step order with `cat -n`

---

## Task 2: Add `load_contributor_context` step to recipe

**File:** `recipes/pr-review.yaml`

Insert after `load_project_context`:

```yaml
  - id: load_contributor_context
    type: bash
    timeout: 30
    output: contributor_context
    command: |
      set -euo pipefail

      # Write pr_data to temp file to avoid shell quoting issues with large JSON
      echo '{{pr_data}}' > /tmp/pr_data_contrib.json

      AUTHOR=$(jq -r '.author.login // .author.name // "unknown"' /tmp/pr_data_contrib.json)

      REPO_FLAG=""
      if [ -n "{{repo}}" ]; then
        REPO_FLAG="--repo {{repo}}"
      fi

      CONTEXT="## CONTRIBUTOR: ${AUTHOR}\n\n"

      # Get their full PR history on this repo
      PR_HISTORY=$(gh pr list $REPO_FLAG --author "$AUTHOR" --state all --limit 20 \
        --json number,title,state,mergedAt 2>/dev/null || echo "[]")

      TOTAL=$(echo "$PR_HISTORY" | jq 'length')
      MERGED=$(echo "$PR_HISTORY" | jq '[.[] | select(.state == "MERGED")] | length')
      CLOSED=$(echo "$PR_HISTORY" | jq '[.[] | select(.state == "CLOSED")] | length')
      OPEN=$(echo "$PR_HISTORY" | jq '[.[] | select(.state == "OPEN")] | length')

      if [ "$TOTAL" -eq "1" ]; then
        CONTRIBUTOR_TYPE="First PR on this repo — treat as external contributor unfamiliar with project conventions."
      elif [ "$TOTAL" -le "3" ]; then
        CONTRIBUTOR_TYPE="New contributor (${TOTAL} PRs, ${MERGED} merged) — may not know all architectural patterns yet."
      else
        CONTRIBUTOR_TYPE="Regular contributor (${TOTAL} PRs, ${MERGED} merged) — familiar with the codebase."
      fi

      CONTEXT="${CONTEXT}${CONTRIBUTOR_TYPE}\n"
      CONTEXT="${CONTEXT}PR record: ${TOTAL} total · ${MERGED} merged · ${CLOSED} closed · ${OPEN} open\n\n"
      CONTEXT="${CONTEXT}PR history:\n"
      CONTEXT="${CONTEXT}$(echo "$PR_HISTORY" | jq -r '.[] | "- PR #\(.number): \(.title) [\(.state)]"')\n"

      printf '%s' "$CONTEXT"
```

**Steps:**
1. Insert after `load_project_context`, before `blast_radius_audit`
2. Confirm step order: discover_pr → load_project_context → load_contributor_context → blast_radius_audit

---

## Task 3: Update `blast_radius_audit` prompt

**File:** `recipes/pr-review.yaml`

Two additions to the `blast_radius_audit` prompt:

**Addition 1** — Add project context after PR Information block:
```
      ## Project Context

      {{project_context_data}}
```

**Addition 2** — Add 6th investigation category after existing 5:
```
      **6. Project Alignment**
      Does this PR silently remove, override, or contradict established project patterns?
      - Architecture patterns or conventions defined in the project vision/docs
      - Components, agents, or capabilities registered in the project that are now missing
      - Rules, gates, or enforced conventions the project has established
      - Active backlog items this PR affects (positively or negatively)
      - Anything that would surprise the project owner who only read the diff description
```

**Steps:**
1. Locate `blast_radius_audit` prompt section
2. Add `## Project Context\n\n{{project_context_data}}` after PR Information block
3. Add 6th category to the investigation list

---

## Task 4: Major rework of `multi_lens_analysis` — CPO lens and CTO sharpening

**File:** `recipes/pr-review.yaml`

Replace the entire `multi_lens_analysis` prompt. Key changes:
- CTO lens: remove product/user questions (CPO owns these), sharpen to pure technical concerns
- CPO lens: replace generic questions with philosophy-driven evaluation across 8 dimensions
- Add contributor context for tone calibration
- Add `Ask:` field to the output format per recommendation

New prompt:

```yaml
  - id: multi_lens_analysis
    agent: foundation:zen-architect
    timeout: 900
    output: analysis
    prompt: |
      REVIEW MODE.

      You are performing a two-lens analysis of a PR. You have the PR details, blast radius
      audit, project context, and contributor history.

      ## PR Information

      {{pr_data}}

      ## Blast Radius Audit

      {{blast_radius}}

      ## Project Context

      {{project_context_data}}

      ## Contributor Context

      {{contributor_context}}

      ## Your Task

      Analyze this PR from two executive perspectives. These lenses ask fundamentally different
      questions. Do not let them overlap — the CTO asks HOW, the CPO asks WHY and FOR WHOM.

      ---

      ### CTO Lens — Technical Excellence and Risk

      Evaluate with ruthless technical honesty:

      - **Architecture**: Does this fit the established technical architecture? Does it introduce
        unnecessary coupling or violate patterns the project has committed to? Use the project
        context to check against documented conventions.
      - **Security**: What risks does the blast radius audit reveal? Prompt injection vectors,
        filesystem access, data persistence, authentication changes.
      - **Integration pattern**: Is this the right way to integrate this capability? Does it
        follow the patterns already established in this codebase?
      - **Technical debt**: Does this add debt? Is it justified by the value delivered?
      - **Operational risk**: What can go wrong at runtime? What is the blast radius of a failure?
      - **Correctness**: Does it do what it claims? Are there edge cases or failure modes the
        diff introduces that are not addressed?

      ---

      ### CPO Lens — User Value and Product Strategy

      You are evaluating this PR as a Chief Product Officer. Your north star is user value and
      product strategy — not code quality. You ask fundamentally different questions than the CTO.

      **Your philosophy as a CPO:**
      - Every PR either advances the product's promise to users or it does not. There is no neutral.
      - A half-shipped feature is worse than no feature. Completeness is non-negotiable.
      - The backlog is a set of promises made to users and the team. Deviations need justification.
      - Opportunity cost is real. Every yes is a no to something else currently in the backlog.
      - User trust is the product's most valuable asset. Every change either builds or spends it.
      - "Does it work?" is the CTO's question. "Should it exist, for whom, and is it complete?" is yours.

      **Evaluate using the loaded project context (vision + backlog):**

      1. **Strategic alignment**: Does this PR deliver on a commitment in the backlog or vision?
         If it is not in the backlog — why is it being built now, and what does that say about
         prioritization discipline? If it is in the backlog, was it built the way the product
         intended? Does it match the stated goal?

      2. **User value**: What specific user problem does this solve? Who is the user — not
         "users in general" but the specific persona this serves. Describe the concrete moment
         when a real user's work gets better because of this PR. If you cannot answer this
         concretely, that is itself a finding.

      3. **Product completeness**: Is this a complete experience a user can extract value from
         today? Or does it depend on other unshipped work to be useful? Half-features create
         confusion, erode trust, and accumulate as product debt. Name any incompleteness.

      4. **User-facing failure mode**: When this breaks (not if — when), what does a user
         experience? Not the technical failure mode — the user-facing failure mode. Is it
         silent or visible? Recoverable or destructive? Does the user know what happened?

      5. **Quality bar**: Does this meet the quality standard the product has established for
         itself? Would you be comfortable putting this in front of a new user on day one?
         Does the level of testing and documentation match the responsibility this feature takes on?

      6. **Timing and priority**: Given the current sprint and backlog, is this the right work
         right now? What sprint commitments does accepting this PR affect? Is the team and user
         base ready for this capability?

      7. **Opportunity cost**: What is explicitly not being shipped, reviewed, or prioritized
         because of this PR? Name the specific backlog items that move further away by accepting
         this work now.

      8. **Product debt introduced**: Does this create product debt — features that technically
         work but users cannot find, understand, or use without workarounds? Does it require
         documentation, onboarding, or support investment not included in this PR?

      **Write in language a product person can act on without reading the code.** Avoid
      implementation details. Focus on user outcomes, strategic fit, and product quality signals.

      ---

      ### Tone Calibration

      Use the contributor context to calibrate tone. First-time contributors get more explanatory
      context — assume they do not know the project's architectural patterns yet. Regular
      contributors get more direct, peer-level feedback.

      ---

      ### Output Format

      Produce numbered recommendations. For each:

      1. **Severity**: BLOCKING, IMPORTANT, or ADVISORY
      2. **Lens**: CTO or CPO (not both — pick the primary lens)
      3. **Finding**: One-sentence description
      4. **Rationale**: 2-3 sentences with specific evidence from the blast radius audit or
         project context
      5. **Ask**: The specific question to pose to the PR author in the review discussion.
         Conversational, genuine, answerable. Something that opens a productive conversation.
      6. **Resolution**: What specifically should change — one sentence

      Order: BLOCKING first, then IMPORTANT, then ADVISORY.

      End with an **Overall Verdict**: DISCUSS — one paragraph naming the 1-2 most important
      things to resolve.
```

**Steps:**
1. Locate `multi_lens_analysis` step
2. Replace the entire step with the new version
3. Verify YAML indentation is consistent

---

## Task 5: Rework `assemble_review` to discussion agenda format

**File:** `recipes/pr-review.yaml`

Replace the `assemble_review` prompt:

```yaml
  - id: assemble_review
    agent: foundation:zen-architect
    timeout: 600
    output: final_review
    prompt: |
      ANALYZE MODE.

      Assemble the final PR review as a **discussion agenda** — not a report.
      Philosophy: the conversation is the product. Each finding ends with the "Ask:" question
      the reviewer poses to the PR author. Walk through this document one item at a time.

      ## PR Information

      {{pr_data}}

      ## Blast Radius Audit

      {{blast_radius}}

      ## Multi-Lens Analysis

      {{analysis}}

      ## Project Context

      {{project_context_data}}

      ## Contributor Context

      {{contributor_context}}

      ---

      ## Output Format

      ### 1. Executive Summary
      3 sentences: what this PR does, the overall blast radius signal, and the single most
      important conversation to have.

      ### 2. PR Overview
      Title, author, branch, files changed, additions/deletions.
      Plain-English summary of what the diff does (2-3 sentences).
      Contributor profile: one line (first PR / new contributor / regular contributor + record).

      ### 3. What This PR Actually Brings In
      A tight table of what this PR changes beyond the diff description.
      Focus on what would surprise someone who only read the PR title.
      If nothing surprising: state "Blast radius matches PR description."

      ### 4. The Conversation You Need To Have

      Structure as a numbered agenda. For each BLOCKING issue:

      ---
      **[B{n}] {Title}** · {CTO/CPO}

      {2-3 sentence rationale. No jargon. A product person should understand this without
      reading the diff.}

      **Ask:** "{The exact question to pose to the PR author. Specific, conversational,
      genuinely answerable — not rhetorical.}"

      **Resolution:** {One sentence on what the fix looks like.}

      ---

      Then IMPORTANT issues in the same format (prefix [I{n}]).
      ADVISORY items (prefix [A{n}]) can be shorter — no Ask: required.

      ### 5. Open Questions
      3-5 genuine questions the analysis could not answer — things only the author or
      decision-maker can resolve. Not rhetorical. Not covered above.

      ### 6. Verdict: DISCUSS
      One paragraph. What is the core tension? What needs to resolve for this to become
      a clear decision?

      ---

      Tone: direct but constructive. Acknowledge what works. Be specific about what does not.
      Leave the door open.
```

**Steps:**
1. Read current `assemble_review` step
2. Replace prompt block with new version
3. Confirm YAML is valid

---

## Task 6: Add `save_review` step

**File:** `recipes/pr-review.yaml`

Add as the final step after `assemble_review`:

```yaml
  - id: save_review
    type: bash
    timeout: 30
    output: saved_path
    command: |
      set -euo pipefail

      echo '{{pr_data}}' > /tmp/pr_data_save.json

      PR_NUM=$(jq -r '.number' /tmp/pr_data_save.json)
      AUTHOR=$(jq -r '.author.login // .author.name // "unknown"' /tmp/pr_data_save.json)
      DATE=$(date +%Y-%m-%d)

      REPO_SLUG=$(echo "{{repo}}" | tr '/' '-' | tr '[:upper:]' '[:lower:]' | tr ' ' '-')
      if [ -z "$REPO_SLUG" ]; then
        REPO_SLUG=$(basename "$(git rev-parse --show-toplevel 2>/dev/null)" || echo "repo")
      fi

      OUTDIR="docs/pr-reviews"
      mkdir -p "$OUTDIR"

      FILENAME="${OUTDIR}/${DATE}-${REPO_SLUG}-PR${PR_NUM}-${AUTHOR}.md"

      printf '%s' "{{final_review}}" > "$FILENAME"

      echo "Review saved to: $FILENAME"
      echo "$FILENAME"
```

Then bump `version:` from `"1.0.0"` to `"2.0.0"`.

**Steps:**
1. Add `save_review` step at end of `steps:` list
2. Bump version to `2.0.0`
3. Validate YAML: `python3 -c "import yaml; yaml.safe_load(open('recipes/pr-review.yaml'))" && echo VALID`

---

## Task 7: Replace `context/change-advisor-instructions.md`

Remove COO/CFO lens definitions. Update CPO lens to match new philosophy. Update workflow table to show 7 steps.

**New file content:**

```markdown
# Change Advisor System

You have access to a multi-lens decision advisor that goes far beyond typical code review.

## Philosophy

Most review tools answer "does this code work?" Change Advisor answers **"should this change
exist in this product, and is this the right way to do it?"**

The core insight: AI amplifies individual velocity but fragments collective coherence.
Contributors arrive with polished, AI-generated PRs that are fundamentally misaligned with
the project's philosophy. Change Advisor forces the alignment conversation that velocity skipped.

## The Workflow

### Phase 1: Investigation (Automated via Recipe)

| Step | What It Does |
|------|-------------|
| **PR Discovery** | Fetches full PR metadata, file list, and diff via GitHub CLI |
| **Load Project Context** | Auto-loads vision doc, backlog, latest test log, and recent commits from the working directory |
| **Load Contributor Context** | Fetches the contributor's PR history — characterizes them as first-time, new, or regular contributor |
| **Blast Radius Audit** | Investigates what the change actually brings in — downloads dependencies, audits tools/hooks/files/security surface, checks project alignment |
| **Multi-Lens Analysis** | CTO and CPO assessments with numbered, severity-tagged recommendations and explicit Ask: questions |
| **Assembly** | Produces a discussion agenda structured for walking through with the PR author |
| **Save** | Writes the full review to `docs/pr-reviews/` — no truncation |

### Phase 2: Interactive Discussion (Conversation)

After the recipe completes, walk through recommendations with the decision-maker:

1. Present each finding one at a time — use the **Ask:** question to open the conversation
2. Let the decision-maker and PR author respond — their domain knowledge reshapes the analysis
3. Track decisions in a todo list
4. The conversation often changes the conclusion — this is the design working, not a bug

### Phase 3: Action (On Request)

When the decision-maker is ready:
- Draft a PR comment based on the decisions made
- Post it via git-ops
- Tone: constructive — acknowledge good work, explain reasoning, leave the door open

## The Review Lenses

### CTO Lens — Technical Excellence and Risk

- **Architecture**: Does this fit the established technical architecture? Does it violate patterns the project has committed to?
- **Security**: Attack surface, prompt injection vectors, data persistence, authentication changes
- **Integration pattern**: Is this the right way to integrate this capability?
- **Technical debt**: Does this add debt? Is it justified?
- **Operational risk**: What can go wrong at runtime?
- **Correctness**: Does it do what it claims? Edge cases, failure modes?

### CPO Lens — User Value and Product Strategy

A great CPO asks fundamentally different questions than the CTO. Where the CTO asks *how*, the CPO asks *why* and *for whom*.

**CPO philosophy:**
- Every PR either advances the product's promise to users or it does not. There is no neutral.
- A half-shipped feature is worse than no feature. Completeness is non-negotiable.
- The backlog is a set of promises. Deviations need explicit justification.
- Opportunity cost is real. Every yes is a no to something else in the backlog.
- User trust is the product's most valuable asset. Every change either builds or spends it.

**CPO evaluation dimensions:**
1. **Strategic alignment** — Is this in the backlog? Built as intended? If not, why now?
2. **User value** — What specific user, what specific problem, what specific moment of value?
3. **Product completeness** — Complete experience today, or fragment depending on other work?
4. **User-facing failure mode** — When it breaks, what does the user experience?
5. **Quality bar** — Does this meet the standard the product has established for itself?
6. **Timing** — Right work, right now, given the sprint and backlog?
7. **Opportunity cost** — What specific backlog items move further away by accepting this?
8. **Product debt** — Works technically but requires workarounds users have to discover?

## Recommendation Severity Levels

| Severity | Meaning | Action |
|----------|---------|--------|
| **BLOCKING** | Must fix before merge | Cannot approve until addressed |
| **IMPORTANT** | Should address | Strong recommendation, not a hard gate |
| **ADVISORY** | Consider for future | Good practice suggestion, won't block |

## Running the PR Review Recipe

```
recipes tool:
  operation: execute
  recipe_path: @change-advisor:recipes/pr-review.yaml
  context:
    pr_number: "10"       # optional — defaults to latest open PR
    repo: "owner/repo"    # optional — defaults to current repo
```

The recipe auto-loads project context from the working directory. The `project_context`
parameter is an optional override — not required for most runs.

The full review is saved to `docs/pr-reviews/` in the working directory.

## After the Recipe

Walk through findings one at a time using the **Ask:** question per finding to open the
discussion. The decision-maker's domain knowledge will reshape findings in ways no automated
review can anticipate.

**Remember:** The conversation is the product. The document starts the dialogue, it doesn't end it.
```

**Steps:**
1. Read current `change-advisor-instructions.md`
2. Replace entire file with new content

---

## Task 8: Update `context/change-advisor-awareness.md`

Remove COO/CFO references. Update context variables table. Update step count to 7.

**New file content:**

```markdown
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
```

**Steps:**
1. Read current `change-advisor-awareness.md`
2. Replace entire file with new content

---

## Task 9: Update `docs/BACKLOG.md`

Mark completed backlog items:
- Item #1 (alignment checking against project philosophy) → **Shipped in v2.0.0**
- Item #3 (lens selection) → **Partially addressed — COO/CFO removed, CTO/CPO deepened**
- Item #4 (reshape output for conversation) → **Shipped in v2.0.0**

**Steps:**
1. Read `docs/BACKLOG.md`
2. Apply status updates

---

## Task 10: Validate and commit

**Step 1:** Validate YAML (run from change-advisor repo):
```bash
python3 -c "import yaml; yaml.safe_load(open('recipes/pr-review.yaml'))" && echo "YAML valid"
```

**Step 2:** Smoke test — run against Gurkaran's PR from the canvas-specialists directory:
```bash
cd /Users/chrispark/Projects/canvas-specialists
amplifier tool invoke recipes operation=execute \
  recipe_path=change-advisor:recipes/pr-review.yaml \
  context='{"pr_number": "17", "repo": "cpark4x/canvas-specialists"}'
```

**Validate:**
- [ ] `load_project_context` output contains vision + backlog content
- [ ] `load_contributor_context` shows Gurkaran's PR history (3 PRs, 2 merged)
- [ ] Blast radius audit references project context in findings
- [ ] CPO findings are genuinely about user value and backlog alignment — not CTO-lite
- [ ] Each BLOCKING/IMPORTANT finding has an Ask: question
- [ ] Review saved to `docs/pr-reviews/` — full content, no truncation

**Step 3:** Commit:
```bash
git add -A
git commit -m "feat: v2.0.0 — auto-load context, contributor history, CPO lens rework, discussion agenda

- load_project_context: auto-reads vision, backlog, test logs, git history from CWD
- load_contributor_context: fetches PR history, characterizes contributor familiarity
- blast_radius_audit: add project alignment as 6th investigation category
- multi_lens_analysis: CPO lens reworked across 8 dimensions of product leadership philosophy
- assemble_review: discussion agenda format with Ask: questions per finding
- save_review: writes full review to docs/pr-reviews/ (fixes output truncation)
- Remove COO/CFO from context files, deepen CTO/CPO
- Bump to v2.0.0

Closes backlog #1, #4. Partially addresses #3."
git push origin main
```

---

## Effort Estimate

| Task | Effort |
|---|---|
| Tasks 1–2: Context loading steps | 20 min |
| Task 3: Blast radius update | 10 min |
| Task 4: CPO lens rework | 30 min |
| Task 5: Discussion agenda format | 20 min |
| Task 6: Save step + version bump | 10 min |
| Tasks 7–8: Context file updates | 20 min |
| Task 9: Backlog update | 5 min |
| Task 10: Validate + commit | 20 min |
| **Total** | **~2.5 hours** |

