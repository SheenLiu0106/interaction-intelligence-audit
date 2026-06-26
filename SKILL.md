---
name: interaction-intelligence-audit
description: Coding-agent-agnostic, logic-first workflow to audit and remediate interaction logic, user-flow integrity, product-state failures, action safety, permissions, and AI-native interaction risks — with evidence classification, audit profiles, and runtime-aware validation across five modes.
version: 0.4.0
---

> **The YAML frontmatter above is optional metadata.** Claude Code and the Claude Agent SDK use
> `name`/`description`/`version` for auto-discovery; other agents simply read this file as plain
> Markdown instructions and ignore it. **No part of this skill depends on it** — or on any
> vendor-specific tool, memory system, planning mode, or proprietary feature.

# Interaction Intelligence Audit

Interaction Intelligence Audit is a coding-agent-agnostic workflow for auditing and remediating
**interaction logic, user-flow integrity, product-state failures, and AI-native interaction
risks.**

**This skill focuses on how a product behaves, not how it looks.** It is **logic-first**: it
detects and remediates **behavioral failures** in software products — especially issues that
affect task completion, data integrity, recoverability, trust, and product readiness — then
returns a severity-ordered, evidence-based report.

It prioritizes, in order: **workflow integrity, state transitions, recovery behavior, action
safety, permissions, cross-page consistency, AI-native interaction logic, and regression risks.**
Accessibility, responsive behavior, internationalization, motion, analytics, performance, and
security/privacy are audited **conditionally** — only when an issue creates a functional,
accessibility, comprehension, data-integrity, or workflow-safety impact.

This is an **audit, not a redesign**, and it is explicitly **not a visual-style critique**. It
evaluates whether users can succeed, whether the product logic holds up under stress, and whether
the product is safe to ship.

**Scope boundary:** Do not report visual-polish issues unless they materially affect
functionality, accessibility, comprehension, or workflow safety.

---

## Agent compatibility

This skill is **coding-agent agnostic**. It runs on any repository-capable coding agent —
**Claude Code, OpenAI Codex, Cursor, Windsurf, Gemini CLI, the GitHub Copilot coding agent**, and
equivalent future tools — because it relies only on generic repository operations (inspect/search
files, read docs, edit source, create/update Markdown, run available validation, inspect diffs).
If a capability is unavailable, use the closest equivalent, document the limitation, and mark
unverifiable checks **`Manual Verification Required`**. See [`README.md`](README.md) for per-agent
setup.

## Five modes

- **Mode A — One-shot Audit.** Walk the framework and return a single severity-ordered report.
  Nothing is written to the target unless asked. Format: [`OUTPUT_TEMPLATE.md`](OUTPUT_TEMPLATE.md).
  This is the default, and the rest of this file describes it.
- **Mode B — Persistent Remediation.** Audit *and* fix issues **one at a time, under human
  review**, keeping an append-only history (`bug.md` / `changelog.md` / decision log) **inside the
  target repository**. Fix one → stop → await explicit human review → continue. Full lifecycle:
  [`WORKFLOW.md`](WORKFLOW.md). Reusable scaffolds: [`templates/`](templates/).
- **Mode C — Regression Re-Audit.** Compare *current* product behavior against the target repo's
  **historical records** (`bug.md`, `changelog.md`, interaction decisions). Re-check the
  workflows affected by previously closed issues and report reopened issues, new regressions,
  still-open issues, verified-closed issues, and approved decisions that are no longer respected.
  Regressions are logged as **new** bug entries typed `Type: Regression` with
  `Related Bug: BUG-XXX`. Mode C **stops after producing the regression report** unless explicitly
  asked to enter Mode B, and it avoids broad re-auditing unless requested. It reuses Mode B's
  preflight, append-only history, and human-review rules. Full lifecycle: [`WORKFLOW.md`](WORKFLOW.md);
  re-audit checks: [`CHECKLIST.md`](CHECKLIST.md) section `23`.
- **Mode D — Targeted Runtime-Assisted Flow Audit** *(optional).* Validate **one explicitly named
  critical flow** (e.g. authentication, checkout, refresh recovery, AI-agent execution) using the
  **strongest available runtime evidence** — dev server, existing E2E suite (Playwright / Cypress),
  browser preview, screenshots — falling back to static inference with `Runtime Verification
  Recommended` when no runtime tooling exists. It does **not** attempt broad full-product
  automation, installs no new dependencies without explicit approval, and **stops after producing
  its targeted report** unless asked to enter Mode B. Full procedure: [`WORKFLOW.md`](WORKFLOW.md);
  report scaffold: [`templates/audit/targeted-runtime-flow-report.md`](templates/audit/targeted-runtime-flow-report.md).
  *Optional Playwright / runtime enhancement:* if a Playwright install (or a configured Playwright
  MCP / browser agent), an existing E2E suite, or a runnable dev server is present, Mode D
  **detects and uses it automatically** to raise findings toward `Runtime Observed` / `Test
  Verified`. This is **opt-in capability detection, not a required pairing** — with no runtime
  tooling Mode D degrades to static inference flagged `Runtime Verification Recommended`. Enable
  browser validation where wanted with `npx playwright install`; the skill never installs it on
  its own without explicit approval.
- **Mode E — Scoped Static Flow Audit** *(optional).* Statically analyze **one explicitly named
  flow** in a large repository without runtime access — map its entry point, routes, components,
  state stores, API calls, permissions, and failure/recovery paths, then inspect **only** the
  relevant files (expanding only when evidence requires). It exists to **reduce context-window
  overload and false positives**, states `Inspected Files` / `Uninspected Areas` / `Known
  Limitations`, avoids cross-page consistency claims about pages it did not inspect, and **stops
  after the scoped report** unless asked to enter Mode B. Report scaffold:
  [`templates/audit/scoped-static-flow-report.md`](templates/audit/scoped-static-flow-report.md).

**Recommended use:** **A** — broad initial audit · **B** — controlled remediation · **C** —
regression re-audit · **D** — targeted runtime validation · **E** — targeted static analysis for
large repos.

**Audit-records location:** in Mode B, Mode C, Mode D, and Mode E, all runtime records are created
**inside the target product repository being audited — never inside this skill repository.**

**Target-repository confirmation (mandatory, Modes B, C, D, and E).** Before creating or updating
*any* runtime audit file, the agent must identify and confirm:

1. the **reusable skill repository** path (where this `SKILL.md` lives),
2. the **target product repository** path, and
3. that **the two paths are not the same directory**.

If the target product repository has not been explicitly provided or cannot be confidently
identified, the agent must **stop and ask the user for the target repository path**. The agent
must **never assume the reusable skill repository itself is the target product repository.** The
full preflight is specified in [`WORKFLOW.md`](WORKFLOW.md).

---

## Static inspection is the floor, not the ceiling

**Static code inspection is the minimum capability, not the maximum capability.** Use the
strongest validation the current environment allows, and escalate as capability allows:

- When **repository access** is available, inspect code.
- When **terminal execution** is available, run validation commands.
- When a **dev server** is available, inspect runtime behavior.
- When **browser automation or E2E tools** are available, use them for targeted validation.
- When **runtime validation is unavailable**, clearly distinguish inference from verified behavior.

**Never claim a finding is runtime-verified unless runtime validation was actually performed.**
This skill **complements, not replaces** E2E testing, browser-based runtime inspection, human
usability review, accessibility validation, and visual QA. It ships no browser runner, E2E
framework, or usability harness of its own.

## Evidence classification (required on every finding)

Evidence is **append-only and multi-stage**. A finding can progress through —

```text
Static Inference → Runtime Observed → Test Verified → Human Verified
```

— and more than one evidence type may coexist. Do **not** model the evidence level as a single
overwritable value; keep the history. Every finding records:

- **Initial Evidence Level:** how the finding was first identified (one of the four levels).
- **Current Verification Level:** the strongest evidence reached so far; may combine types,
  e.g. `Runtime Observed + Test Verified`.
- **Verification Status:** `Verified` | `Runtime Verification Recommended` |
  `Manual Verification Required` | `Not Yet Verified`.
- **Confidence:** `High` | `Medium` | `Low`.
- **Evidence History:** an append-only log; one entry per evidence addition, each preserving
  `Type:` / `Source:` / `Timestamp:` / `Notes:`.

The four evidence types:

- *Static Inference* — inferred from code, docs, or configuration without runtime confirmation.
- *Runtime Observed* — reproduced in a running app, browser preview, emulator, or device.
- *Test Verified* — confirmed via automated tests (unit, integration, Playwright, Cypress,
  existing E2E suites, API contract tests).
- *Human Verified* — confirmed only after explicit human confirmation.

**Evidence Level vs. Verification Status — they are different axes:**

```text
Evidence Level describes where the evidence came from.
Verification Status describes whether the available evidence is sufficient to confirm the issue or fix.
```

```text
Evidence Level: Static Inference
Verification Status: Runtime Verification Recommended
```

```text
Evidence Level: Runtime Observed + Test Verified
Verification Status: Verified
```

**Never present a Static Inference as confirmed runtime fact.** A finding whose current level is
only Static Inference must use cautious language — *"may," "appears to," "could,"* or *"requires
runtime verification."*

## Audit profiles (prevent over- and under-engineering)

Select an **Audit Profile** at intake; it drives severity, recommendation depth, and remediation
priority. **Default Audit Profile: Production.** If the product stage is unclear, ask the user to
confirm the audit profile; if no answer is provided, use **Production** and state the assumption
explicitly in the report.

- **MVP** — prioritize core workflow completion, dead ends, obvious data loss, severe state
  failures, missing critical recovery, duplicate-submission on important actions, and major
  AI-context / human-override failures. Use pragmatic recommendations; do not demand complex
  handling for extremely low-probability edge cases unless they create serious risk.
- **Production** — prioritize complete state handling, recovery paths, permissions, cross-page
  consistency, regression risk, validation coverage, session recovery, optimistic-update rollback,
  error differentiation, and release readiness.
- **High-Risk / Regulated** — prioritize irreversible actions, audit trails, access control,
  permission isolation, evidence/provenance, strict validation, recovery guarantees, human
  approval, traceability, data integrity, model-output verification, and compliance-sensitive
  workflows.

Each finding additionally records: **Audit Profile**, **Risk if Unfixed**, **Implementation
Effort** (`Low` | `Medium` | `High`), and **Recommended Timing** (`Blocker` | `Current Sprint` |
`Backlog` | `Defer`).

**Prioritization rule:** Do not recommend complex remediation solely because an edge case is
theoretically possible. Prioritize by probability, user impact, product stage, data-integrity
risk, and recovery difficulty.

## Mode B change-control (per fix)

Mode B fixes **one issue at a time and stops for human review**. Each fix follows a strict
change-control contract — **Pre-Fix Baseline**, **Change Budget**, and **Post-Fix Validation** —
specified in [`WORKFLOW.md`](WORKFLOW.md). In short: capture the baseline (git status, current
diff, affected files, expected behavior, acceptance criteria, regression risks, available
validation commands); modify **only** the files necessary for the selected issue (no unrelated
refactors, no dependency upgrades without explicit approval, no broad style changes, no
batch-fixing adjacent issues); then validate (diff, tests, typecheck, lint, build, runtime where
available), record every unverified check, and provide exact manual-review steps. **Never close an
issue or proceed to the next without explicit human approval.**

---

## When to use

- You want to find **behavioral failures** before users do: broken workflows, dead ends, missing states, error/empty/partial paths, lost progress, permission denials, destructive actions, silent data loss.
- You are reviewing a **dashboard**, **workflow/agent builder**, **AI chat or AI-native product**, **onboarding flow**, **settings area**, or any **SaaS or web app** screen/flow.
- You want a structured pre-release or pre-launch review focused on task completion, data integrity, recoverability, trust, and readiness.
- You need a prioritized issue list (Critical → Low) plus category scores you can take into sprint planning.
- The product has AI behavior (generation, agents, tool calls, memory) and you want to audit trust, explainability, recovery, and user control.
- You want to confirm previously fixed interaction issues haven't regressed (Mode C).

## When NOT to use

- You want a **visual / brand / aesthetic critique** (color, typography, spacing taste, illustration/icon style, Figma craftsmanship, pixel-perfect review). This skill deliberately avoids style opinions and reports visual observations only when they create a measurable functional, accessibility, comprehension, data-integrity, or workflow-safety problem.
- You want the product **rebuilt or redesigned**. This skill diagnoses and recommends (Mode A/C) or fixes one issue at a time under review (Mode B); it does not produce a redesign.
- You want a pure **accessibility certification** (WCAG conformance audit with formal pass/fail per criterion). Accessibility is audited here as a behavioral/comprehension concern, not as formal certification — use a dedicated a11y audit for that.
- You have nothing concrete to audit. The skill needs a described surface, screenshots, a URL, a flow description, or access to the running product.

---

## Inputs

### Required (at least one)
- `target_description` — what the product/surface is and what it's for (e.g. "B2B analytics dashboard for support managers")
- `target_url` — URL of the running product or surface (if reachable)
- `screens` — screenshots, recordings, or Figma frames of the surface
- `flows` — description of the key user flow(s) to walk (e.g. "create workflow → add nodes → run → view results")

### Optional (sharpen the audit)
- `product_type` — `dashboard` | `workflow-builder` | `ai-chat` | `ai-agent` | `onboarding` | `settings` | `web-app` | `mobile-app`
- `user_roles` — the user types/permissions that matter (new, returning, premium, admin, suspended, restricted)
- `platforms` — `web` | `mobile-web` | `ios` | `android` | `tablet` | `desktop-app`
- `is_ai_product` — whether the product has AI/agent behavior (drives emphasis on Section 21)
- `release_stage` — `prototype` | `beta` | `pre-launch` | `live`
- `known_constraints` — anything out of scope or already known/accepted

---

## Workflow

### Step 1 — Intake & scope
Capture the inputs above and **select the Audit Profile** (`MVP` | `Production` | `High-Risk / Regulated`; default `Production` — if the stage is unclear, ask, and if unanswered use `Production` and state the assumption). State plainly at the top of the report: what is being audited, which platforms and user roles are in scope, the release stage, the selected profile, and any declared constraints. If the target is AI-native, flag that Section 21 (AI Experience) is in primary scope.

### Step 2 — Adopt the audit lens
Adopt the three personas defined in [PROMPT.md](PROMPT.md): a **Staff Product Designer**, a **QA Lead**, and a **Product Manager** — *not* a visual designer. Every finding must trace back to user success, product logic, state handling, action safety, failure recovery, or release readiness.

### Step 3 — Walk the framework (logic-first)
Walk the framework in [CHECKLIST.md](CHECKLIST.md). Audit **Tier 1 — Primary Logic Audit** first (Domains 1–7: user-flow integrity, product-state logic, action safety & data integrity, cross-page consistency, permissions & access control, AI-native interaction logic, regression & release readiness). Audit **Tier 2 — Conditional / Secondary** sections (`10`, `13`, `14`, `15`, `16`, `17`, `19`) **only** where an issue creates a functional, accessibility, comprehension, data-integrity, or workflow-safety impact. For each relevant item, look for the failure mode the question implies, and record concrete evidence (what you saw, where). Skip sections that are genuinely not applicable and say so — do not pad. **Prioritize the highest-risk behavioral issues first**; do not get lost in low-severity polish before the critical paths are covered.

### Step 4 — Severity & evidence
Assign each finding a severity (Critical / High / Medium / Low) using the rubric in [OUTPUT_TEMPLATE.md](OUTPUT_TEMPLATE.md), adjusted for the selected Audit Profile. Every finding needs an Impact (who is affected and how), a concrete Recommendation, and the **evidence classification** (Evidence Level, Confidence, Verification Status, Evidence Source) plus **Risk if Unfixed**, **Implementation Effort**, and **Recommended Timing**. No generic observations — tie each one to a specific state, flow, component, or screen, and use cautious language for anything that is only a Static Inference.

### Step 5 — Output report
Produce the report exactly in the format defined by [OUTPUT_TEMPLATE.md](OUTPUT_TEMPLATE.md): Executive Summary (Overall Score /100 + Top Risks), then Critical → High → Medium → Low issues, then Category Scores (including **AI Experience**), then a Recommended Next Sprint (P1/P2/P3). Use [EXAMPLES.md](EXAMPLES.md) to calibrate finding quality and severity.

---

## Rules & constraints

- **Audit only.** Diagnose and recommend; do not redesign or write production code.
- **Logic-first, not visual critique.** The subject is interaction logic, workflow integrity, state handling, action safety, recovery, permissions, cross-page consistency, AI-native behavior, and regression/release readiness. **Do not report visual-polish issues unless they materially affect functionality, accessibility, comprehension, or workflow safety.** When a visual observation *does* cause such an impact, report the impact, not the aesthetic.
- **Evidence-based.** Every finding references a specific state, flow, component, or screen. No vague "could be better."
- **Severity-ordered.** Always lead with the highest-risk behavioral issues. Critical issues are release blockers.
- **Reusable across product types.** The same framework applies to dashboards, workflow builders, AI products, web apps, and mobile — emphasize the domains that fit the target.
- **Honest scoring.** Per-category scores plus an overall score. If a category can't be assessed (no access, out of scope), mark it `N/A` rather than guessing.
- **AI products get Section 21.** When the target is AI-native, treat `21 AI Experience` (Domain 6) as primary scope and include it in Category Scores.

---

## Files

- [README.md](README.md) — purpose, what it does (and doesn't) audit, inputs/outputs, per-agent setup, example invocations
- [CHECKLIST.md](CHECKLIST.md) — the logic-first audit framework: Tier 1 (7 primary domains, sections `01`–`23`) + Tier 2 (conditional/secondary)
- [OUTPUT_TEMPLATE.md](OUTPUT_TEMPLATE.md) — the mandatory Mode A report format + severity rubric
- [EXAMPLES.md](EXAMPLES.md) — worked, logic-first example findings (including visual-observation judgment calls)
- [PROMPT.md](PROMPT.md) — the default invocation prompts (Modes A–E) and audit personas
- [WORKFLOW.md](WORKFLOW.md) — Mode B remediation, Mode C regression re-audit, Mode D runtime-assisted, and Mode E scoped-static lifecycles (statuses, change-control, one-fix-at-a-time, human-review gate, runtime-capability detection, evidence classification)
- [templates/](templates/) — reusable scaffolds copied into the target repo: `bug.md`, `changelog.md`, `audit/interaction-decisions.md`, `audit/regression-checklist.md`, `audit/targeted-runtime-flow-report.md` (Mode D), `audit/scoped-static-flow-report.md` (Mode E)
