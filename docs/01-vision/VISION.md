# Change Advisor: Vision

**A multi-lens decision advisor that helps project maintainers make better decisions about changes to their product — starting with PRs, expanding to any decision where alignment matters.**

_(Change Advisor | amplifier-change-advisor)_

**Owner:** Chris Park
**Contributors:** Chris Park

**Last Updated:** 2026-03-05

---

## Summary

AI-powered development amplifies individual velocity but fragments collective coherence. Contributors arrive with polished, well-tested PRs that are fundamentally misaligned with the project's philosophy — because AI optimized for their task, not your product. Change Advisor investigates the true scope of proposed changes, evaluates them through executive lenses (CTO, CPO, COO, CFO) the reviewer may not naturally have, and structures an interactive discussion that surfaces the decision-maker's real-world context. The tool doesn't decide — it makes you a better decision-maker.

---

## Table of Contents

1. [The Problems We're Solving](#1-the-problems-were-solving)
2. [Strategic Positioning](#2-strategic-positioning)
3. [Philosophy](#3-philosophy)
4. [Who This Is For](#4-who-this-is-for)
5. [The Sequence](#5-the-sequence)
6. [Related Documentation](#6-related-documentation)

---

## 1. The Problems We're Solving

**Solve the alignment problem before the quality problem.**

### Problem 1: AI Amplifies Velocity but Fragments Alignment

#### The collaboration gap in AI-powered development

**Current Reality:**
- AI-powered development means contributors go from idea to polished PR without discussing approach, philosophy, or fit with the project
- Each contributor's AI optimizes for their immediate task — not for the project's coherence
- Design conversations that used to happen in hallways, pair programming, and design reviews get skipped entirely
- Changes arrive fully formed, well-tested, well-documented — and potentially misaligned

**The Impact:**
- Conflicting philosophies ship as merge conflicts of intent, not code
- A memory system designed for invisible capture arrives at a project built on visible evidence chains — both are good engineering, together they're incoherent
- Project maintainers approve changes they don't fully understand because the surface looks professional

**Why This Matters:**
AI didn't remove the need for alignment conversations — it made them more important by removing the natural friction that used to force them. When building is fast and talking is slow, people stop talking. Change Advisor forces the alignment conversation that velocity skipped.

**Who this affects:** Project maintainers and leads who accept contributions from others — especially in AI-accelerated teams where contributors can produce substantial work independently.

---

### Problem 2: Changes Hide Their True Scope

#### The blast radius problem

**Current Reality:**
- A PR shows a diff — lines added, lines removed, files changed
- The actual impact is often far larger than the diff suggests: a 1-line dependency addition can bring in 68 files, 9 tools, 3 hooks, and 200MB of dependencies
- Reviewers assess what's visible and approve based on the surface, not the substance
- Dependency additions, bundle includes, and config changes are especially deceptive

**The Impact:**
- Teams approve changes they don't fully understand
- Problems surface after merge — in production, in degraded quality, in unexpected behavior
- The cost of fixing post-merge is dramatically higher than catching pre-merge

**Why This Matters:**
The diff is an interface, not the implementation. Reviewing only the diff is like reading a function signature without reading the function body. The blast radius is the real change; the diff is just the entry point.

**Who this affects:** Anyone who reviews and approves changes — especially dependency additions, integrations, and external contributions.

---

### Problem 3: No Single Reviewer Has All the Lenses

#### The perspective gap

**Current Reality:**
- A good decision about a change needs technical judgment (CTO), product judgment (CPO), operational judgment (COO), and sometimes financial/resource judgment (CFO)
- Most reviewers are strong in one, maybe two of these lenses
- Standard code review focuses almost entirely on the technical lens: "Does it work? Does it follow standards?"
- The higher-value questions — "Should this exist in this product? Is this the right time? Is this the right integration pattern?" — rarely get asked systematically

**The Impact:**
- Reviews are systematically biased toward whichever lens the reviewer is strongest in
- A senior engineer catches the security risk but misses the product misalignment
- A product lead catches the UX issue but misses the dependency problem
- Critical concerns from missing perspectives go unraised — not because they don't matter, but because no one in the review is wired to see them

**Why This Matters:**
The lenses aren't just "different perspectives" — they're alignment checks against the project's philosophy from angles the reviewer might not think to check themselves. Missing a lens doesn't mean the concern doesn't exist; it means no one asked.

**Who this affects:** Decision-makers who are strong in their domain but need coverage in others.

---

### Problem 4: The Decision-Maker's Context Never Enters the Review

#### The dialogue gap

**Current Reality:**
- Automated analysis can surface risks and assess architecture
- But the decision-maker has context no tool can know: their workflow, testing patterns, product vision, team dynamics, current priorities
- Most review tools produce a verdict (approve/reject) without creating space for the reviewer's context to reshape the analysis
- The most valuable insights emerge only through structured dialogue between analysis and human knowledge

**The Impact:**
- Reviews miss critical issues that only surface through conversation
- A testing workflow insight can completely change a verdict — but only if the conversation happens
- Automated verdicts create false confidence: "the tool approved it" replaces actual understanding

**Why This Matters:**
The analysis is the starting point, not the answer. The decision-maker's context is the missing variable that turns generic risk assessment into a good decision for this specific project at this specific moment.

**Who this affects:** Anyone who has tried to use automated review tools and found the output too generic or disconnected from their actual situation.

---

## 2. Strategic Positioning

### The Core Insight

**What is everyone else doing?**

Review tools — GitHub Actions, code quality bots, security scanners, AI code reviewers — produce automated verdicts. They check code quality, find bugs, flag security issues, and output approve/reject signals. They're optimized for "does this code work?" at scale.

**Why are they wrong or incomplete?**

They answer the wrong question. "Does this code work?" is necessary but insufficient. The hard decisions aren't about code quality — they're about whether a well-built change belongs in your product, at this time, integrated this way. No linter catches a philosophical misalignment. No security scanner flags "this integration pattern conflicts with your product's transparency model."

**Our contrarian position:**

The tool doesn't decide. It investigates what you're actually approving, sees it through lenses you don't naturally have, and structures a conversation that makes *you* a better decision-maker. The most valuable output isn't the analysis document — it's the dialogue that follows it.

**The difference:**
- **Review tools**: Produce verdicts about code quality
- **Change Advisor**: Structures alignment conversations about product coherence

Change Advisor is built for decision quality, not review throughput.

---

### The 4 Strategic Pillars

#### 1. Investigate the Blast Radius, Not Just the Diff

**The old way:** Read the diff, check for code quality issues, approve or reject.
**The Change Advisor way:** Investigate what the change actually brings into the project — download dependencies, audit tools and hooks, map filesystem changes, assess security surface. The diff is the entry point; the blast radius is the real change.

#### 2. Multi-Lens Analysis Over Single-Perspective Review

**The old way:** One reviewer with one perspective checks the change against their expertise.
**The Change Advisor way:** Systematically evaluate through CTO (technical risk, architecture, security), CPO (product fit, user impact, timing), COO (operational impact, process), and CFO (resource, cost, investment) lenses. Each lens is an alignment check against the project's philosophy from an angle the reviewer might not naturally check.

#### 3. Structured Dialogue Over Automated Verdicts

**The old way:** Tool produces a verdict. Reviewer acts on it. No conversation.
**The Change Advisor way:** Analysis produces numbered recommendations with rationale. Each recommendation is discussed one-by-one with the decision-maker. The decision-maker's real-world context reshapes the analysis. The conversation is the product.

#### 4. Alignment Checks, Not Quality Checks

**The old way:** "Does this code follow our standards? Does it have tests? Are there security issues?"
**The Change Advisor way:** "Does this change align with our product's philosophy? Is this the right integration pattern for us? Is this the right time? Does this maintain the coherence of what we're building?"

---

### What We're NOT Building

Clear boundaries help AI make correct decisions:

- :x: An auto-approver or CI gate (we structure conversations, not produce verdicts)
- :x: A code quality checker (linters, formatters, and type checkers do that)
- :x: A security scanner (dedicated security tools do that better)
- :x: A replacement for human judgment (we amplify it, not replace it)
- :x: A review throughput optimizer (we optimize for decision quality, not speed)

---

## 3. Philosophy

**The principles that drive every design decision.**

### Principle 1: The Conversation Is the Product

The analysis document is not the deliverable — the structured dialogue between analysis and human context is. Every design decision should optimize for dialogue quality, not document quality. This rules out: automated verdicts, approve/reject signals, CI integration that skips the conversation.

### Principle 2: Show What the Diff Hides

If a change has impact beyond what's visible in the diff, the tool must surface it. A 1-line change that adds 68 files should be presented as a 68-file change, not a 1-line change. This rules out: shallow diff-only analysis, treating dependency additions as simple line changes.

### Principle 3: Lenses Over Opinions

The tool provides structured perspectives (CTO, CPO, COO, CFO), not unstructured opinions. Each lens has defined evaluation criteria. Recommendations are tagged by lens, severity, and rationale. This rules out: vague "this looks risky" assessments, unlabeled concerns, opinions without evidence.

### Principle 4: The Decision-Maker Decides

The tool never recommends APPROVE or REJECT as a final answer. It recommends DISCUSS with structured starting points. The decision-maker's context — which only they have — is always the deciding factor. This rules out: automated merge gates, confidence scores that imply certainty, "safe to merge" signals.

---

**Test:** If someone read only the Philosophy section, would they make correct design decisions? Does each principle explicitly rule something out? Yes — each principle ends with what it rules out.

---

## 4. Who This Is For

### Primary: Project Maintainers in Collaborative AI-Powered Teams

People who approve changes to their systems that they didn't author, often under time pressure, often outside their strongest lens.

- Technical leads receiving PRs from contributors who used AI to build substantial features independently
- Product owners evaluating whether proposed changes align with product vision
- Solo maintainers of projects that accept external contributions

**Why they're underserved:**
- GitHub's review tools: Focus on code quality, not product alignment
- AI code reviewers (Copilot, CodeRabbit): Optimize for finding bugs, not evaluating strategic fit
- Manual review: Limited by the reviewer's own lenses and available time

### Secondary: Team Leads Building Review Culture

- Engineering managers who want to elevate review beyond "does it compile?"
- Teams transitioning to AI-powered development who are losing alignment conversations
- Open source maintainers evaluating external contributions

**What they need:**
- A repeatable process for evaluating changes through multiple lenses
- A framework that creates alignment conversations, not just approval checkboxes
- A way to communicate decisions constructively to contributors

---

## 5. The Sequence

**Scenarios expand, core stays the same: investigate, analyze through lenses, structure the conversation.**

### V1: PR Review (Current)

**Focus:** GitHub Pull Requests — the most common trigger for "should I approve this change?"

**Core capabilities:**
- Automated PR discovery and diff fetching via GitHub CLI
- Blast radius investigation — audit what the PR actually brings in beyond the diff
- Multi-lens analysis — CTO and CPO perspectives with numbered, severity-tagged recommendations
- Assembled review document designed as a starting point for interactive discussion

**V1 validates:** Does the investigate → analyze → discuss workflow produce better decisions than standard PR review? Does the structured conversation surface context that automated review misses?

**V1 reality:** Recipe built and working. Tested on a real PR (engram-lite memory system integration). The interactive discussion surfaced critical insights (testing workflow, product philosophy conflict) that the automated analysis alone would not have caught. Name and vision being formalized.

---

### V2: Expanded Scenarios (Next)

**Focus:** Apply the same multi-lens, investigate-then-discuss framework to other decision types.

**Core capabilities:**
- RFC and architecture proposal review
- Dependency upgrade evaluation
- "Should we build this?" assessment for feature proposals
- Configurable lens selection (not every decision needs all 4 lenses)

**V2 goal:** The framework works for any "should we accept this change?" decision, not just PRs.

---

### V3: Organizational Memory (Future)

**Focus:** Learn from past decisions to improve future ones.

**Core capabilities:**
- Decision history — "last time we added a global hook system, here's what happened"
- Pattern recognition — "this integration pattern has caused problems in 3 previous decisions"
- Team-specific lens calibration — learn which lenses matter most for this project

**V3 goal:** Decisions get better over time because the tool learns from the project's history.

---

## 6. Related Documentation

**Vision folder (strategic context):**
- docs/01-vision/VISION.md - This document

**Current features and roadmap:**
- [docs/README.md](../README.md) - Documentation navigation
- [Epics](../02-requirements/epics/) - Feature requirements

**Implementation:**
- recipes/pr-review.yaml - V1 PR review recipe
- context/ - Review lens definitions and instructions
- behaviors/ - Composable behavior for inclusion in other bundles

---

## Change History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| v1.0 | 2026-03-05 | Chris Park | Initial vision document |

