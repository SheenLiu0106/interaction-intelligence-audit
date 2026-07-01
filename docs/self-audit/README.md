# Self-Audit Records (internal, one-time)

These files are the **internal audit of this skill repository**, produced once while the
Interaction Intelligence Audit skill was (temporarily) pointed at its own repo. They are
preserved here for history — they are **not** part of the skill's runtime contract and are
**not** templates.

When the skill runs against a real target product, its runtime records (`AUDIT_BUGS.md`,
`changelog.md`, decision logs) are created **inside that target repository**, never here. See
[`WORKFLOW.md`](../../WORKFLOW.md) and the reusable scaffolds in [`templates/`](../../templates/).

## Status of this directory (read before using or copying)

- This directory contains a **one-time internal audit history for the reusable skill itself**.
- It is **retained long-term for traceability** of how the skill was reviewed and changed.
- It is **not a runtime scaffold** — do not treat it as a template.
- It **must not be copied into target product repositories**. Use [`templates/`](../../templates/)
  for that.
- Its findings (BUG-001 … BUG-005, DECISION-001) **must not be automatically treated as any
  audited product's backlog**. They concern this skill repo only.

## Contents

- `AUDIT_BUGS.md` — BUG-001 … BUG-005 from the self-audit. BUG-001 is **Approved / Closed** (its fix is
  the Category Scores change in `OUTPUT_TEMPLATE.md`, approved 2026-06-05); BUG-002 … BUG-005
  remain `Open` (not yet addressed).
- `changelog.md` — implementation + approval history for the BUG-001 fix.
- `interaction-decisions.md` — DECISION-001 (Category Scores rollup), **Approved**.

## Pending items

- **BUG-002 … BUG-005** — recorded but **not yet scheduled or addressed**. They are not part of
  any product backlog; they are skill-repo findings awaiting a future, separately-reviewed pass.
