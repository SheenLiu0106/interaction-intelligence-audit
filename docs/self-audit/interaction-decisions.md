# Interaction Design Decisions

## DECISION-001 — Category Scores as an explicit rollup of all 21 framework sections

Date: 2026-06-05
Related Bug: BUG-001
Status: Approved (human review, 2026-06-05 16:13)

### Problem

The report's Category Scores section listed 7 buckets, one of which ("Interaction Design")
matched no framework section, while 14 of the 21 framework categories had no score — yet
`README.md`, `SKILL.md`, and `OUTPUT_TEMPLATE.md` all state the scores "map to the framework."

### Options Considered

#### Option A — Rollup buckets that each name their framework sections (cover all 21)

Pros:
- Keeps a digestible scorecard (not 21 separate scores).
- Makes the "maps to the framework" claim literally true.
- Single-file change to `OUTPUT_TEMPLATE.md`; preserves product direction.

Cons:
- Slightly more buckets than before (~14 vs 7).

#### Option B — Score all 21 categories individually

Pros:
- Maximally faithful 1:1 mapping.

Cons:
- Heavy, repetitive report section; reduces the at-a-glance value of a scorecard.

#### Option C — Drop the "maps to the framework" claim and keep 7 ad-hoc buckets

Pros:
- Smallest edit.

Cons:
- Leaves the orphan "Interaction Design" bucket and 14 silently-unscored categories; weakens
  the skill's "honest, per-category scoring" promise.

### Selected Approach

Option A — rollup buckets, each naming the framework section number(s) it aggregates, covering
all 21 sections.

### Reason

It resolves the contradiction (true mapping, no invented category, full coverage) while keeping
the scorecard digestible and the change minimal and reversible. It honors the author's evident
intent to provide a summary scorecard rather than 21 line-item scores.

### Review Status

**Approved** — 2026-06-05 16:13. The change remains uncommitted until staging/commit is
separately and explicitly approved.
