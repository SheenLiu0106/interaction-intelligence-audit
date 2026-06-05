# Interaction Intelligence Audit — Change Log

## 2026-06-05

### BUG-001 — Category Scores section contradicts the "maps to the framework" claim

Status: Fixed — Awaiting Human Review

#### Objective

Make the report's Category Scores section internally consistent with the 21-category framework
in `CHECKLIST.md`, so the documented "maps to the framework" claim is true and no score bucket
names a category that does not exist.

#### Files Modified

- `OUTPUT_TEMPLATE.md`

#### Changes Applied

- Replaced the orphan **"Interaction Design"** bucket (which matched no `01`–`21` framework
  section) with buckets that map to real sections.
- Redefined Category Scores as an explicit **rollup of all 21 framework sections**, naming the
  section number(s) each score aggregates.
- Reworded the intro line so it states the scores are a rollup of (not a 1:1 list of) the
  framework, with `N/A` allowed per bucket.

#### Behavior Before

7 score buckets; one ("Interaction Design") traced to no framework section; 14 of 21 framework
categories had no score line — despite three files claiming the scores map to the framework.

#### Behavior After

Every score bucket names the real framework section(s) it covers; all 21 sections are
accounted for; the "maps to the framework" claim in `README.md`/`SKILL.md`/`OUTPUT_TEMPLATE.md`
is now accurate.

#### Regression Checks

- [x] All `01`–`21` sections covered by exactly one bucket (verified by enumeration).
- [x] No bucket names a non-existent category.
- [x] `README.md:111` and `SKILL.md:65,81` claims remain true (no edits needed there).
- [x] Heading level left unchanged (BUG-002 owns heading structure — not touched here).
- [x] Human review completed.

#### Review Status

**Approved** — 2026-06-05 16:13.

---

### BUG-001 — Human Review Approved

Date: 2026-06-05 16:13
Decision: **Approved** (human reviewer)
Status: Closed

- The Category Scores rollup change in `OUTPUT_TEMPLATE.md` is approved without further changes.
- DECISION-001 marked Approved.
- The change is no longer a "pending" self-audit change; it remains **uncommitted** until
  staging/commit is separately and explicitly approved.

---
