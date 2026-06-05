# Persistent Remediation Workflow (Mode B)

This is the **incremental audit-and-fix workflow** for the Interaction Intelligence Audit
skill. Use it when you want the agent not just to *report* issues (that is Mode A — see
[`OUTPUT_TEMPLATE.md`](OUTPUT_TEMPLATE.md)) but to **fix them one at a time, under human
review**, while keeping a complete, auditable history in the target repository.

It is **coding-agent agnostic**: it assumes only generic repository operations and works with
Claude Code, OpenAI Codex, Cursor, Windsurf, Gemini CLI, the GitHub Copilot coding agent, and
any equivalent repository-capable agent. It must **not** depend on any vendor-specific tool,
memory system, planning mode, or proprietary feature.

---

## Where records live

All runtime records are created **inside the target product repository being audited** — never
inside this skill repository. Default locations in the target repo:

```text
<target-repo>/bug.md
<target-repo>/changelog.md
<target-repo>/docs/audit/interaction-decisions.md
<target-repo>/docs/audit/regression-checklist.md   (optional)
```

Scaffold them by copying from this skill's [`templates/`](templates/) directory and filling in
the placeholders. If those paths conflict with the target repo's conventions, nest them under a
dedicated folder (e.g. `<target-repo>/docs/audit/`) and say so in `bug.md`.

---

## Preflight (mandatory, before scaffolding or writing any record)

The agent **must** complete this preflight before creating or updating any runtime audit file:

```text
Preflight:
- Resolve the skill repository path.
- Resolve the target product repository path.
- Confirm the paths are different.
- Check whether the target already contains bug.md, changelog.md, or docs/audit/.
- Preserve and update existing records rather than overwriting them.
- Copy blank templates only when the corresponding target files do not already exist.
```

Confirmation rule:

- The agent must identify and confirm (1) the **reusable skill repository** path, (2) the
  **target product repository** path, and (3) that **the two are not the same directory**.
- If the target product repository was **not explicitly provided** or **cannot be confidently
  identified**, the agent must **stop and ask the user for the target repository path** — do not
  guess.
- The agent must **never assume the reusable skill repository itself is the target product
  repository.** If the resolved target path equals the skill path, treat it as an error and stop.

---

## Generic repository operations only

Use the closest available equivalent of: inspect files, search the codebase, read docs, inspect
routes/components, edit source files, create/update Markdown, run available validation commands,
inspect diffs. If a capability (terminal, dev server, browser preview, tests) is unavailable, do
not fail silently — use the strongest available alternative, **document the limitation**, and
mark anything you could not verify as **`Manual Verification Required`** with exact reviewer steps.

---

## Status lifecycle

```text
Detected → Open → In Progress → Fixed — Awaiting Human Review → Approved → Closed
```

Alternate review outcomes from `Fixed — Awaiting Human Review`:

```text
→ Rejected
→ Needs Revision → In Progress → …
→ Deferred
```

Valid statuses: `Open`, `In Progress`, `Fixed — Awaiting Human Review`, `Needs Revision`,
`Approved`, `Closed`, `Deferred`, `Rejected`, `Duplicate`, `Won't Fix`.

**Never skip the human-review stage. Never mark an issue `Closed` without explicit human
approval. Never start the next fix before the current one is reviewed.**

---

## The loop

0. **Preflight (mandatory).** Run the [Preflight](#preflight-mandatory-before-scaffolding-or-writing-any-record)
   above: resolve the skill and target repository paths, confirm they differ, and stop to ask the
   user if the target is unknown. Do not proceed to any write until this passes.
1. **Inspect the environment.** Identify the target repo's structure, entry points, routes,
   components, state, styles, and the available validation commands. Record which capabilities
   are unavailable.
2. **Read existing records.** Read the target repo's `bug.md` / `changelog.md` / decision log if
   they exist. Preserve and update them; do not overwrite. Do not duplicate existing issues; do
   not revert approved decisions.
3. **Audit** using [`CHECKLIST.md`](CHECKLIST.md). Record every finding in the target's `bug.md`
   (copy [`templates/bug.md`](templates/bug.md) if absent). Assign ID, title, severity,
   category, location, impact, evidence, reproduction, expected behavior, recommended fix,
   acceptance criteria.
4. **Prioritize** by risk (Critical core-flow/data-loss/accessibility blockers first). Select
   **one** issue.
5. **Fix one issue.** Set it `In Progress`. Make the **smallest effective change**. No unrelated
   refactors or UI changes. Reuse existing shared components/conventions where they exist.
6. **Verify.** Re-check the reproduction; confirm loading/success/error/disabled/keyboard/
   responsive behavior as relevant; check related flows for regressions (see
   [`templates/audit/regression-checklist.md`](templates/audit/regression-checklist.md)); run the
   strongest available validation; inspect the diff. Mark unrunnable checks
   `Manual Verification Required`.
7. **Record.** Set the issue `Fixed — Awaiting Human Review`. Append (never overwrite) history in
   `bug.md`; add a record to `changelog.md`; add a decision entry if the fix involved a real
   design choice.
8. **Stop and request human review.** Present what changed, files modified, validation performed,
   manual checks required, and how to review. Wait.

### Validation order (use the strongest available)

```text
automated tests → type checking → linting → production build → runtime inspection
→ browser/interaction testing → screenshot inspection → source-code reasoning
→ manual-review instructions
```

Never claim a validation step passed unless it was actually performed.

---

## Handling the review decision

- **Approve BUG-NNN** → set `Approved`, record reviewer notes, set `Closed`, append history, add
  the approval to `changelog.md`. Continue to the next issue **only if explicitly instructed**.
- **Reject BUG-NNN** → set `Rejected`, preserve the fix history, record feedback, propose next
  action, stop.
- **Revise BUG-NNN: [feedback]** → set `Needs Revision`, apply **only** the requested change,
  re-validate, return to `Fixed — Awaiting Human Review`, request review again, stop.
- **Defer BUG-NNN** → set `Deferred`, record the reason, keep it in the backlog, stop.

---

## History-preservation rules

- Append-only. Never delete or rewrite a previous `bug.md` entry, `changelog.md` record, or
  decision.
- Preserve the original issue description after resolution.
- For a returned issue, create a new entry typed `Regression` that references the original; never
  rewrite the closed one.
- For a duplicate, set `Status: Duplicate` and link `Related Bug: BUG-NNN`.

---

## Constraints

Do **not**: fix multiple issues in one pass; make unrelated changes; close without approval; skip
regression/accessibility checks where relevant; claim unperformed validation; continue past the
review gate. Do: keep each fix narrow and traceable, prefer minimal targeted changes, document
every change and limitation, and stop after each fix.
