# Interaction Intelligence Audit — Bug Tracker

> Audit target: this repository (`interaction-intelligence-audit`) — a Markdown-only
> documentation/skill product. There is no runtime UI, so findings concern the
> **documentation product**: contract consistency between files, structural/markdown
> correctness, cross-reference integrity, and reader cognitive load. "Interaction quality"
> here = how reliably a human/agent can follow these docs to produce a correct audit.

## Summary

Last Updated: 2026-06-05 16:13

| Status | Count |
|---|---:|
| Open | 4 |
| In Progress | 0 |
| Fixed — Awaiting Human Review | 0 |
| Needs Revision | 0 |
| Closed | 1 |
| Deferred | 0 |

---

## BUG-001 — Category Scores section contradicts the "maps to the framework" claim

Status: Closed
Severity: High
Category: Design Consistency / Cognitive Load
Location: `OUTPUT_TEMPLATE.md` (Category Scores) — asserted in `README.md`, `SKILL.md`
Detected: 2026-06-05 15:31
Last Updated: 2026-06-05 16:13
Reviewer: Approved (human)
Related Files: `OUTPUT_TEMPLATE.md`, `README.md`, `SKILL.md`, `CHECKLIST.md`

### Issue

The product's primary deliverable is a report whose **Category Scores** section is
documented (in three files) as mapping to the 21-category framework in `CHECKLIST.md`:

- `README.md:111` — "**Category scores** mapped to the framework"
- `SKILL.md:65` / `SKILL.md:81` — "Category Scores ... These map to the framework in CHECKLIST.md"
- `OUTPUT_TEMPLATE.md:81` — "These map to the framework in [CHECKLIST.md]"

But the actual Category Scores list (`OUTPUT_TEMPLATE.md:83-89`) has only **7 buckets**, and
they do **not** map cleanly to the framework:

- One bucket, **"Interaction Design,"** is **not a framework section at all** — no `01`–`21`
  heading by that name exists in `CHECKLIST.md`.
- **14 of the 21 framework categories** have no corresponding score line (Preconditions,
  User Flows, Page States, Data States, Forms, Components, Destructive Actions, Feedback,
  Motion, Permissions, Responsive Design, Internationalization, Analytics, Versioning,
  Security & Privacy).
- Two report buckets ("Information Architecture", "Accessibility", "Error Handling",
  "Performance", "AI Experience", "Release Readiness") happen to match section names, so a
  reader reasonably infers a 1:1 mapping that does not hold.

### User Impact

An agent following `OUTPUT_TEMPLATE.md` literally will emit a "Interaction Design: X/10"
score that traces to no framework section, and will silently omit honest scores for 14
categories it was told to walk. This directly conflicts with the skill's "Honest scoring —
per-category" rule (`SKILL.md:76`) and undermines the report's credibility — the one
artifact the skill exists to produce.

### Evidence

- Component: report "Category Scores" block.
- Triggering action: agent reaches Step 5 / report output and fills Category Scores.
- Current result: 7 scores, one of which ("Interaction Design") names no framework category;
  14 framework categories unscored despite docs claiming a framework mapping.
- Expected result: every score bucket traces to one or more real framework sections, and the
  "maps to the framework" claim is accurate.

### Reproduction Steps

1. Open `CHECKLIST.md` and list the 21 section names (`01`–`21`).
2. Open `OUTPUT_TEMPLATE.md:83-89` and list the 7 Category Scores buckets.
3. Compare: "Interaction Design" has no matching section; 14 sections have no bucket.

### Expected Behavior

The Category Scores section is internally consistent with the framework: either it scores all
21 categories, or it is explicitly defined as a curated rollup with each bucket's framework
sections named — and no bucket invents a category that does not exist in `CHECKLIST.md`.

### Recommended Fix

Smallest effective change, in `OUTPUT_TEMPLATE.md` only: redefine the Category Scores block as
an explicit **rollup of the 21 framework sections**, naming the sections each bucket covers,
and replace the orphan "Interaction Design" label with buckets that actually correspond to
framework sections. Soften the "These map to the framework" line to state it is a rollup, not
a 1:1 list, so the README/SKILL claims become true. Do not change the framework itself.

### Acceptance Criteria

- [x] Every Category Scores bucket traces to one or more real `CHECKLIST.md` sections.
- [x] No bucket names a category that does not exist in the framework.
- [x] All 21 framework sections are accounted for (verified: 01–21 each covered exactly once).
- [x] The "maps to the framework" wording is accurate after the change.
- [x] `README.md` / `SKILL.md` claims remain consistent with the revised template (no edits needed).
- [x] Human review is completed.

### History

#### 2026-06-05 15:31 — Issue Detected
- Status set to `Open`.
- Initial issue recorded from code-level audit.

#### 2026-06-05 15:31 — Fix Started
- Status changed to `In Progress`.
- Selected as highest-priority unresolved issue (High severity, affects primary deliverable).
- Files under review:
  - `OUTPUT_TEMPLATE.md`

#### 2026-06-05 15:31 — Fix Implemented
- Status changed to `Fixed — Awaiting Human Review`.
- Modified files:
  - `OUTPUT_TEMPLATE.md` (Category Scores block only)
- Summary:
  - Removed the orphan "Interaction Design" bucket (matched no framework section).
  - Redefined Category Scores as a rollup of all 21 sections, each naming its section number(s).
  - Reworded the intro so the "maps to the framework" claim is accurate (rollup, not 1:1).
- Verification:
  - Enumerated section numbers in the new block: 01–21 each present exactly once, no duplicates.
  - Confirmed 0 remaining occurrences of "Interaction Design".
  - `git diff` confirms the change is scoped to the Category Scores block only.
  - `npx markdownlint-cli2` run: no new error class introduced; flagged items are pre-existing
    project conventions (MD033 `<placeholder>`, MD013 long lines) and MD025, which corroborates
    BUG-002. No project lint config exists yet (see BUG-003).
- Regression checks:
  - `README.md:111`, `SKILL.md:65,81` claims remain true without edits.
  - Heading level intentionally left unchanged (BUG-002 owns heading structure).
- Human review required.

#### 2026-06-05 15:31 — Human Review
- Reviewer decision: Pending
- Reviewer notes: Pending

#### 2026-06-05 16:13 — Human Review Approved
- Reviewer decision: **Approved**.
- Status changed to `Closed`.
- Reviewer notes: Category Scores rollup (DECISION-001) approved without further changes. The
  `OUTPUT_TEMPLATE.md` change is now an approved (no longer pending) self-audit change. Not yet
  committed — staging/commit awaits separate explicit approval.

---

## BUG-002 — Multiple H1 headings per document break the document outline

Status: Open
Severity: Medium
Category: Information Architecture / Accessibility (semantic structure)
Location: `CHECKLIST.md`, `OUTPUT_TEMPLATE.md`
Detected: 2026-06-05 15:31
Last Updated: 2026-06-05 15:31
Reviewer: Pending
Related Files: `CHECKLIST.md`, `OUTPUT_TEMPLATE.md`

### Issue

Both files use a top-level `#` (H1) for the document title **and** another `#` (H1) for every
section. `CHECKLIST.md` emits 22 H1s (title + `# 01`…`# 21`); `OUTPUT_TEMPLATE.md` emits H1s
for `# Executive Summary`, `# Critical Issues`, etc. after its own `#` title.

### User Impact

Multiple H1s flatten the rendered outline / table of contents on GitHub and in doc tooling, so
readers and assistive tech lose the title-vs-section hierarchy. It also violates the common
`markdownlint` rule MD025 (single H1), so any future lint gate would fail. The skill's own
framework flags exactly this failure mode (`CHECKLIST.md:184` — semantic heading structure).

### Evidence

- Files: `CHECKLIST.md` (sections `# 01`–`# 21`), `OUTPUT_TEMPLATE.md` (`# Executive Summary`…).
- Expected: one H1 title per file; sections demoted to `##` (and sub-items to `###`).

### Reproduction Steps

1. Open `CHECKLIST.md`; observe `#` on both the title and each `01`–`21` section.
2. Render on GitHub or run `markdownlint`; observe MD025 / flattened outline.

### Expected Behavior

One H1 per document (the title); all sections are `##`, sub-sections `###`.

### Recommended Fix

Demote section headings to `##`/`###` in `CHECKLIST.md` and `OUTPUT_TEMPLATE.md`. Update any
internal anchor references if affected (none currently use heading anchors).

### Acceptance Criteria

- [ ] Each file has exactly one H1.
- [ ] Section nesting is correct (`##` sections, `###` sub-items).
- [ ] `markdownlint` MD025 passes (or is documented as unavailable).
- [ ] No cross-reference links broken by the change.
- [ ] Human review is completed.

### History

#### 2026-06-05 15:31 — Issue Detected
- Status set to `Open`. Recorded from code-level audit.

---

## BUG-003 — No automated validation despite a manual cross-reference requirement

Status: Open
Severity: Medium
Category: Error Prevention / Release Readiness
Location: repository root (no lint config / CI)
Detected: 2026-06-05 15:31
Last Updated: 2026-06-05 15:31
Reviewer: Pending
Related Files: `CONTRIBUTING.md`, (absent) lint/CI config

### Issue

`CONTRIBUTING.md:20` instructs contributors to manually "verify cross-file references … stay
consistent," but there is no `markdownlint` config, link checker, or CI workflow to enforce it.
The defects in BUG-002 and BUG-004 are exactly what such tooling would catch mechanically.

### User Impact

Documentation drift (broken links, heading regressions, inconsistent cross-refs) ships
unnoticed because the only safeguard is manual reviewer diligence. For a skill whose value is
rigor, this is a release-readiness gap.

### Evidence

- No `.markdownlint*`, `.github/workflows/*`, or link-check config present (verified at root).
- `CONTRIBUTING.md:20` places the burden entirely on manual review.

### Reproduction Steps

1. `ls -a` the repo root and `.github/`; observe no lint/CI config.
2. Note `CONTRIBUTING.md:20` relies on manual verification.

### Expected Behavior

A lightweight, dependency-free-ish check (e.g. a `markdownlint` config + a CI step, or a
documented `npx markdownlint-cli2 "**/*.md"` command) enforces structure and link integrity.

### Recommended Fix

Add a minimal `markdownlint` config and a short CI workflow (or at least a documented command
in `CONTRIBUTING.md`) so structural/link checks run automatically.

### Acceptance Criteria

- [ ] A documented, runnable validation command exists for contributors.
- [ ] (Optional) CI runs it on PRs.
- [ ] Human review is completed.

### History

#### 2026-06-05 15:31 — Issue Detected
- Status set to `Open`. Recorded from code-level audit.

---

## BUG-004 — Inconsistent checklist cross-reference labels in EXAMPLES.md

Status: Open
Severity: Low
Category: Design Consistency
Location: `EXAMPLES.md`
Detected: 2026-06-05 15:31
Last Updated: 2026-06-05 15:31
Reviewer: Pending
Related Files: `EXAMPLES.md`, `CHECKLIST.md`

### Issue

Several `(Checklist …)` references in `EXAMPLES.md` use abbreviated/altered section names that
don't match `CHECKLIST.md` headings:

- "14 Responsive" (`EXAMPLES.md:19,112`) vs framework "14 Responsive Design".
- "03 IA" (`EXAMPLES.md:133`) vs "03 Information Architecture".
- "Confidence & Evidence" / "AI Trust & Auditability" (`EXAMPLES.md:71,91`) vs the checklist's
  "Confidence and Evidence" / "AI Trust and Auditability".

`CONTRIBUTING.md:20` explicitly requires cross-file references to stay consistent.

### User Impact

Minor: a reader cross-referencing examples to the framework hits slightly different names,
adding small friction and weakening the "rigorous, consistent" impression. No task blockage.

### Evidence

- `EXAMPLES.md:19,112,133,71,91` vs `CHECKLIST.md` section headings and sub-headings.

### Reproduction Steps

1. Grep `EXAMPLES.md` for `(Checklist`.
2. Compare each label to the exact `CHECKLIST.md` heading text.

### Expected Behavior

Cross-references use the exact framework section names (numbers + full names; ampersand vs
"and" consistent).

### Recommended Fix

Normalize the `(Checklist …)` labels in `EXAMPLES.md` to match `CHECKLIST.md` exactly.

### Acceptance Criteria

- [ ] All `EXAMPLES.md` cross-refs match `CHECKLIST.md` heading text.
- [ ] Human review is completed.

### History

#### 2026-06-05 15:31 — Issue Detected
- Status set to `Open`. Recorded from code-level audit.

---

## BUG-005 — README opens with two redundant one-line descriptions

Status: Open
Severity: Low
Category: Cognitive Load / Microcopy
Location: `README.md:3,5`
Detected: 2026-06-05 15:31
Last Updated: 2026-06-05 15:31
Reviewer: Pending
Related Files: `README.md`

### Issue

`README.md:3` and `README.md:5` both state essentially the same one-line description of the
skill back-to-back, with the title in between — a redundant double intro.

### User Impact

Minor reader friction; the page feels slightly padded before delivering new information.

### Evidence

- `README.md:3` — "A reusable Claude Code skill for systematic … audits."
- `README.md:5` — "**Interaction Intelligence Audit** is a Claude Code skill that performs a
  comprehensive … audit of web apps…"

### Reproduction Steps

1. Open `README.md`; read lines 3 and 5.

### Expected Behavior

A single, non-redundant lead description.

### Recommended Fix

Merge lines 3 and 5 into one lead sentence, or drop the weaker of the two.

### Acceptance Criteria

- [ ] No duplicated lead description.
- [ ] Human review is completed.

### History

#### 2026-06-05 15:31 — Issue Detected
- Status set to `Open`. Recorded from code-level audit.
