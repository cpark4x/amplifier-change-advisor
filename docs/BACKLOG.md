# Change Advisor — Backlog

**Last Updated:** 2026-03-11

## Current Status

V2.0.0 shipped. 7-step pipeline with auto-loaded project context, contributor history, reworked CPO lens (8 dimensions), discussion agenda format with Ask: questions, and review saved to docs/pr-reviews/. Two lenses: CTO + CPO (COO/CFO removed in v2). Bundle available as global app bundle.

## Active Work

None — V2 shipped.

## Recently Completed

| Item | Date | Notes |
|------|------|-------|
| Vision document (VISION.md) | 2026-03-05 | 4 problems, 4 pillars, 4 principles, V1-V3 roadmap |
| PR Review recipe | 2026-03-05 | 4-step pipeline: discover, blast radius, multi-lens, assemble |
| All 4 executive lenses | 2026-03-05 | CTO, CPO, COO, CFO in recipe |
| Verdict fix (DISCUSS only) | 2026-03-05 | Aligns with Principle 4: Decision-Maker Decides |
| Global app bundle install | 2026-03-05 | Available in all Amplifier sessions |
| V2.0.0 release | 2026-03-11 | Auto-load context, contributor history, CPO 8-dimension rework, discussion agenda, save-to-file, COO/CFO removed |

## Prioritized Backlog

### This Sprint

| # | Item | Effort | Impact | Description |
|---|------|--------|--------|-------------|
| ✅ 1 | Alignment checking against project philosophy | M | H | Add a `project_vision` context variable that feeds the project's VISION.md/PRINCIPLES.md into the analysis. Add an explicit alignment check: "does this change align with the project's documented philosophy?" Currently the lenses check for generic risk, not alignment with your specific product. The engram-lite review caught the philosophy conflict by luck, not by design. **Shipped in v2.0.0** |
| 2 | Encode the conversation walk-through | M | H | The recipe produces a document, but the structured 1-by-1 discussion (the actual product per Principle 1) isn't guaranteed. Either strengthen coordinator instructions with a concrete protocol, or build a staged recipe with approval gates between recommendations. |

### Next 1-2 Sprints

| # | Item | Effort | Impact | Description |
|---|------|--------|--------|-------------|
| ✅ 3 | Lens selection mechanism | S | M | Not every review needs all 4 lenses. Add an optional `lenses` context variable (e.g., `lenses: "CTO,CPO"`) with a smart default that picks relevant lenses based on what the blast radius audit finds. Reduces noise for simple changes. **Partially addressed — COO/CFO removed, CTO/CPO deepened** |
| ✅ 4 | Reshape output for conversation, not reading | S | M | The assembled document reads like a report. Reshape the assembly step to produce a numbered discussion agenda with explicit questions per recommendation — designed to be walked through, not read as a wall of text. **Shipped in v2.0.0** |

### Future (V2/V3)

| # | Item | Effort | Impact | Description |
|---|------|--------|--------|-------------|
| 5 | Expanded scenarios: RFC and architecture review | L | H | Apply the same multi-lens framework to RFCs, architecture proposals, dependency upgrades, and "should we build this?" evaluations. Same workflow, different input sources. |
| 6 | Decision history and pattern memory | L | H | Learn from past decisions. "Last time we added a global hook system, here's what happened." Enables pattern recognition across reviews. V3 vision. |
| 7 | Configurable lens definitions | M | M | Let projects define custom lenses or modify existing ones. A security-focused project might want a deeper security lens; a startup might not need CFO. |

## Epic Status

| Epic | Status | Description |
|------|--------|-------------|
| V1: PR Review | Shipped | Blast radius + 4 lenses + assembled review for GitHub PRs |
| V2: Self-Loading + CPO Rework | Shipped | Auto-load context, contributor history, 2-lens CTO/CPO, discussion agenda, save-to-file |
| V3: Expanded Scenarios | Not started | RFCs, architecture proposals, dependency upgrades |
| V4: Organizational Memory | Not started | Decision history and pattern recognition |
