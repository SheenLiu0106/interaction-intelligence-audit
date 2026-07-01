# Remediation, Regression, Runtime & Scoped-Static Workflows (Modes B–E)

This file specifies the four non-report modes of the Interaction Intelligence Audit skill:

- **Mode B — Persistent Remediation:** fix issues **one at a time, under human review**, keeping a
  complete, auditable history in the target repository. (Mode A — report only — is in
  [`OUTPUT_TEMPLATE.md`](OUTPUT_TEMPLATE.md).)
- **Mode C — Regression Re-Audit:** when records already exist, detect whether previously closed
  issues or approved decisions have regressed (see [Mode C](#mode-c--regression-re-audit)).
- **Mode D — Targeted Runtime-Assisted Flow Audit:** validate **one named critical flow** with the
  strongest available runtime evidence (see [Mode D](#mode-d--targeted-runtime-assisted-flow-audit)).
- **Mode E — Scoped Static Flow Audit:** statically analyze **one named flow** in a large repo
  without runtime access, inspecting only the relevant files (see [Mode E](#mode-e--scoped-static-flow-audit)).

It is **coding-agent agnostic**: it assumes only generic repository operations and works with
Claude Code, OpenAI Codex, Cursor, Windsurf, Gemini CLI, the GitHub Copilot coding agent, and
any equivalent repository-capable agent. It must **not** depend on any vendor-specific tool,
memory system, planning mode, or proprietary feature. **Static code inspection is the minimum
capability, not the maximum** — every mode uses the strongest validation the environment allows
and complements, rather than replaces, E2E testing, runtime inspection, accessibility validation,
visual QA, and human usability review.

---

## Logic-first classification rule (applies to every mode)

Before logging a visual observation, determine whether it creates a functional, accessibility,
comprehension, or workflow-safety impact.

```text
If no material impact exists, do not log it as a bug.
```

When such an impact *does* exist, log the **impact** (the behavioral, accessibility,
comprehension, data-integrity, or workflow-safety problem) — not the aesthetic. This skill audits
how a product behaves, not how it looks.

---

## Evidence classification (every finding, every mode)

Evidence is **append-only and multi-stage**. A finding can progress
`Static Inference → Runtime Observed → Test Verified → Human Verified`, and more than one type may
coexist. Do **not** overwrite a single evidence value — keep the history. Every finding (in
`AUDIT_BUGS.md`, a Mode A report, a Mode D runtime report, or a Mode E scoped report) records:

```text
Initial Evidence Level:     Static Inference | Runtime Observed | Test Verified | Human Verified
Current Verification Level: strongest evidence reached (may combine, e.g. "Runtime Observed + Test Verified")
Verification Status:        Verified | Runtime Verification Recommended | Manual Verification Required | Not Yet Verified
Confidence:                 High | Medium | Low
Evidence History:           append-only; one entry per evidence addition:
  - Type: <Static Inference | Runtime Observed | Test Verified | Human Verified>
    Source: <file path | route | component | state store | API endpoint | test name | runtime observation | screenshot | manual-review note>
    Timestamp: <YYYY-MM-DD HH:mm>
    Notes: <what this evidence confirmed or changed>
```

- **Static Inference** — inferred from code, docs, or configuration without runtime confirmation.
- **Runtime Observed** — reproduced in a running app, browser preview, emulator, or device.
- **Test Verified** — confirmed via automated tests (unit, integration, Playwright, Cypress,
  existing E2E suites, API contract tests).
- **Human Verified** — confirmed only after explicit human confirmation.

**Evidence Level and Verification Status are different axes:**

```text
Evidence Level describes where the evidence came from.
Verification Status describes whether the available evidence is sufficient to confirm the issue or fix.
```

```text
Evidence Level: Static Inference          | Verification Status: Runtime Verification Recommended
Evidence Level: Runtime Observed + Test Verified | Verification Status: Verified
```

**Never present a Static Inference as confirmed runtime fact.** A finding whose current level is
only static must use cautious language — *"may," "appears to," "could,"* or *"requires runtime
verification."* **Never claim a finding is runtime-verified unless runtime validation was actually
performed.**

## Runtime-capability detection (before validating)

Detect what the environment supports and record it, so evidence levels are honest:

```text
- Repository access?        → inspect code
- Terminal execution?       → run validation commands
- Dev server?               → inspect runtime behavior
- Browser automation / E2E? → Playwright | Cypress | existing E2E | browser preview
- Component preview?        → Storybook | component harness
- API mocks?                → exercise failure/edge responses
- None of the above?        → static inference + Manual Verification Required (give exact steps)
```

Use existing project tooling first; **do not install new dependencies unless the user explicitly
approves.** When a capability is unavailable, mark affected findings `Runtime Verification
Recommended` or `Manual Verification Required` with exact reviewer steps — never fail silently and
never assume the runtime behavior.

**Playwright / browser automation is opt-in, not a required pairing.** The browser-automation row
above is satisfied by *whatever the environment already provides* — a Playwright install, a
configured Playwright MCP / browser agent, an existing Cypress or E2E suite, or a runnable dev
server. When such tooling is present, use it to raise findings toward `Runtime Observed` / `Test
Verified`; when it is absent, degrade to static inference flagged `Runtime Verification
Recommended`. Installers are **not** required to set up Playwright alongside this skill. Where
browser validation is wanted and approved, enable it with `npx playwright install` — the skill
never installs it on its own.

## Audit profiles (severity & timing)

Each run selects an **Audit Profile** (`MVP` | `Production` | `High-Risk / Regulated`) that tunes
severity, recommendation depth, and remediation priority (defined in [`SKILL.md`](SKILL.md)). Every
finding additionally records **Audit Profile**, **Risk if Unfixed**, **Implementation Effort**
(`Low` | `Medium` | `High`), and **Recommended Timing** (`Blocker` | `Current Sprint` | `Backlog` |
`Defer`). Do not recommend complex remediation solely because an edge case is theoretically
possible — prioritize by probability, user impact, product stage, data-integrity risk, and recovery
difficulty.

---

## Where records live

All runtime records are created **inside the target product repository being audited** — never
inside this skill repository. **Target-repository record handling is the target project's
concern and is independent of this skill repo's `.gitignore`** — do not assume the skill repo's
ignore rules cover (or should cover) records written into a target repo, and **do not modify this
skill repo's `.gitignore` unless required.** Each target project decides what it tracks and what
it ignores.

Recommended target-repo policy:

```text
Tracked target-repository records:
- AUDIT_BUGS.md
- changelog.md
- docs/interaction-audit/interaction-decisions.md
- docs/interaction-audit/reports/*.md            (Mode A / Mode D / Mode E reports)

Optional transient runtime artifacts (the target project may .gitignore these):
- docs/interaction-audit/runtime-artifacts/
- screenshots/
- traces/
- videos/
```

Scaffold the tracked records by copying from this skill's [`templates/`](templates/) directory and
filling in the placeholders. The optional regression checklist
([`templates/audit/regression-checklist.md`](templates/audit/regression-checklist.md)) and the
Mode D / Mode E report scaffolds also live under `templates/audit/`. If these paths conflict with
the target repo's conventions, nest them under a dedicated folder and say so in `AUDIT_BUGS.md`. Transient
artifacts (screenshots, traces, videos, scratch runtime output) may be ignored by the target
project if it chooses; the durable records above should be tracked.

---

## Preflight (mandatory, before scaffolding or writing any record)

The agent **must** complete this preflight before creating or updating any runtime audit file:

```text
Preflight:
- Resolve the skill repository path.
- Resolve the target product repository path.
- Confirm the paths are different.
- Check whether the target already contains AUDIT_BUGS.md, changelog.md, or docs/interaction-audit/.
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
2. **Read existing records.** Read the target repo's `AUDIT_BUGS.md` / `changelog.md` / decision log if
   they exist. Preserve and update them; do not overwrite. Do not duplicate existing issues; do
   not revert approved decisions.
3. **Audit** using [`CHECKLIST.md`](CHECKLIST.md). Record every finding in the target's `AUDIT_BUGS.md`
   (copy [`templates/AUDIT_BUGS.md`](templates/AUDIT_BUGS.md) if absent). Assign ID, title, severity,
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
   `AUDIT_BUGS.md`; add a record to `changelog.md`; add a decision entry if the fix involved a real
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

## Mode B change-control contract (per fix)

Mode B already fixes **one issue at a time and stops for human review**. This contract makes the
blast radius of each fix explicit and auditable.

**Before each fix — Pre-Fix Baseline:**

```text
Pre-Fix Baseline:
- identify the target bug
- capture current git status
- inspect the current diff
- list affected files
- define expected behavior
- define acceptance criteria
- identify regression risks
- identify available validation commands
```

**During each fix — Change Budget:**

```text
Change Budget:
- modify only files necessary for the selected issue
- avoid unrelated refactors
- avoid dependency upgrades unless explicitly approved
- avoid broad style changes
- do not batch-fix adjacent issues
```

**After each fix — Post-Fix Validation:**

```text
Post-Fix Validation:
- inspect git diff
- run available tests
- run typecheck when available
- run lint when available
- run build when available
- run runtime verification when available
- record all unverified checks
- provide exact manual-review steps
```

**Remediation record fields** (append to the issue's `AUDIT_BUGS.md` history and the `changelog.md`
entry, in addition to the evidence-classification fields):

```text
Files Modified:
Validation Performed:
Validation Not Available:
Manual Verification Steps:
Regression Risks:
```

**Never close the issue without explicit human approval. Never proceed to the next issue without
explicit human instruction.**

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

## Mode C — Regression Re-Audit

Mode C compares **current product behavior against historical records** to detect regressions.
It is **not** a fresh full audit — it re-checks only what history says was already addressed,
unless you explicitly ask for broader coverage. It reuses everything above: the mandatory
[Preflight](#preflight-mandatory-before-scaffolding-or-writing-any-record), generic-operations
rule, status lifecycle, append-only history, and the human-review gate. Its checks come from
[`CHECKLIST.md`](CHECKLIST.md) section `23` (Regression & Release Readiness Re-Audit).

### The Mode C procedure

0. **Preflight (mandatory).** Resolve the skill and target repository paths, confirm they differ,
   and stop to ask the user if the target is unknown — exactly as in Mode B.
1. **Read existing `AUDIT_BUGS.md`** in the target repo.
2. **Read existing `changelog.md`** in the target repo.
3. **Read existing interaction decisions** (e.g. `docs/interaction-audit/interaction-decisions.md`).
4. **Identify previously closed issues** (`Closed` / `Approved`) and the approved interaction
   decisions.
5. **Re-check the affected workflows** — the flows, states, and components those issues and
   decisions touched. Do **not** broadly re-audit unless the user asks.
6. **Report**, in a Regression Report, each of:
   - **reopened issues** — previously closed/approved issues that now reproduce,
   - **newly discovered regressions** — new breakage in a re-checked workflow,
   - **still-open issues** — previously recorded issues that remain open,
   - **verified closed issues** — confirmed still fixed,
   - **approved decisions that are no longer respected.**
7. **Avoid broad re-auditing** unless explicitly requested.
8. **Preserve historical records** — append only; never rewrite or delete a closed entry.
9. **Log regressions as new `AUDIT_BUGS.md` entries** that reference the original bug ID. Use the
   classification:

   ```text
   Type: Regression
   Related Bug: BUG-XXX
   ```

10. **Stop after generating the regression report** unless explicitly asked to enter Mode B and
    fix an issue.

If the user then says to fix a regression, switch to **Mode B** and run the standard one-fix
loop on that new `Type: Regression` entry (set `In Progress` → smallest effective fix → validate
→ `Fixed — Awaiting Human Review` → stop for review).

---

## Mode D — Targeted Runtime-Assisted Flow Audit

Mode D validates **one explicitly named critical user flow** using the **strongest available
runtime evidence**. It does **not** attempt broad full-product automation by default — it focuses
on a single flow, such as: `authentication`, `onboarding`, `checkout`, `document upload`,
`dashboard loading`, `permission downgrade`, `AI-agent execution`, `AI memory reset`,
`approval workflow`, `form submission`, `state recovery after refresh`. Report scaffold:
[`templates/audit/targeted-runtime-flow-report.md`](templates/audit/targeted-runtime-flow-report.md).

### Mode D preflight

```text
1. Confirm the reusable skill repository path.
2. Confirm the target product repository path.
3. Confirm the two paths are different.
4. Identify the requested target flow.
5. Inspect available scripts and runtime capabilities.
6. Determine whether a dev server can be started.
7. Detect available validation tools: Playwright, Cypress, existing E2E tests, browser preview,
   screenshots, API mocks, manual verification.
8. State what can and cannot be validated.
9. Do not install new dependencies unless the user explicitly approves.
```

### Mode D flow

```text
Select one target flow
→ Map entry point, routes, components, state stores, API calls, failure paths, and recovery paths
→ Perform static inspection
→ Start runtime environment when available
→ Exercise the target flow
→ Test relevant state transitions
→ Simulate failure conditions when tooling allows
→ Capture evidence
→ Log findings with Evidence Level and Verification Status
→ Produce targeted runtime report
→ Stop
```

### Runtime scenarios (test where relevant and available)

`loading` · `empty` · `success` · `error` · `partial success` · `retry` · `disabled state` ·
`duplicate click` · `slow network` · `timeout` · `API error` · `permission denied` ·
`stale state` · `refresh recovery` · `session recovery` · `optimistic-update rollback` ·
`unsaved changes` · `AI context loss` · `human override` · `unsafe AI auto-apply` ·
`multi-agent handoff failure`.

**Do not claim a scenario was tested unless it was actually executed.** Use existing project tools
first (Playwright, Cypress, existing E2E tests, browser automation, dev server, Storybook,
component preview, API mock layer, manual browser inspection). If no runtime tooling exists, set
`Verification Status: Runtime Verification Recommended` and provide manual verification steps.

**Stop after producing the targeted runtime report** unless the user explicitly asks to enter
Mode B for remediation.

---

## Mode E — Scoped Static Flow Audit

Mode E analyzes **one explicitly named flow** statically, **without** requiring runtime access. Its
purpose is to **reduce context-window overload and false positives in large repositories**. Example
flows: `authentication`, `onboarding`, `checkout`, `document upload`, `AI memory reset`,
`agent approval flow`, `permission downgrade`, `dashboard refresh recovery`. Report scaffold:
[`templates/audit/scoped-static-flow-report.md`](templates/audit/scoped-static-flow-report.md).

### Mode E preflight & flow map

Run the standard [Preflight](#preflight-mandatory-before-scaffolding-or-writing-any-record) (target
≠ skill), then **map the flow before auditing**:

```text
Flow Entry:
Relevant Routes:
Components:
State Stores:
API Calls:
Permission Rules:
Failure Paths:
Recovery Paths:
Related Existing Bugs:
Related Approved Decisions:
```

Then inspect **only** the files relevant to that flow unless evidence requires expansion.

### Mode E rules

```text
Do not scan the entire repository by default when the requested audit target is narrow.
Prefer scoped evidence collection over broad context loading.
When the relevant flow cannot be confidently mapped, state the uncertainty and ask for clarification.
Avoid making cross-page consistency claims unless the relevant pages and shared logic were actually inspected.
```

### Mode E output (must distinguish)

```text
Inspected Files:
Uninspected Areas:
Known Limitations:
Static Inferences:
Runtime Verification Recommended:
```

Because Mode E performs no runtime validation, its findings default to `Initial Evidence Level:
Static Inference` / `Current Verification Level: Static Inference` with `Verification Status:
Runtime Verification Recommended` (or `Manual Verification Required`) unless existing tests confirm
otherwise. **Stop after generating the scoped report** unless the user explicitly asks to enter
Mode B.

---

## History-preservation rules

- Append-only. Never delete or rewrite a previous `AUDIT_BUGS.md` entry, `changelog.md` record, or
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
