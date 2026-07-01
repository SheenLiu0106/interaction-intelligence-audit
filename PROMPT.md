# Interaction Intelligence Audit — Default Invocation Prompt

Use this prompt to run the audit with **any repository-capable coding agent** (Claude Code, OpenAI Codex, Cursor, Windsurf, Gemini CLI, GitHub Copilot coding agent, or equivalent). It depends on no vendor-specific feature. Adapt the bracketed inputs; keep the personas and priorities intact.

This audit is **logic-first**: it audits *how a product behaves* — interaction logic, user-flow integrity, product-state handling, action safety, permissions, AI-native behavior, cross-page consistency, and regression/release readiness. **This skill focuses on how a product behaves, not how it looks.**

> Do not perform subjective visual-design critique.
> Only report visual issues when they create functional, accessibility, comprehension, or workflow risk.

There are five modes — pick one at the bottom of this file:

- **Mode A — One-shot Audit:** report only, in [OUTPUT_TEMPLATE.md](OUTPUT_TEMPLATE.md) format.
- **Mode B — Persistent Remediation:** audit *and* fix one issue at a time under human review, per [WORKFLOW.md](WORKFLOW.md).
- **Mode C — Regression Re-Audit:** compare current behavior against the target repo's historical records and report regressions, per [WORKFLOW.md](WORKFLOW.md).
- **Mode D — Targeted Runtime-Assisted Flow Audit:** validate one named critical flow with the strongest available runtime evidence, per [WORKFLOW.md](WORKFLOW.md).
- **Mode E — Scoped Static Flow Audit:** statically analyze one named flow in a large repo without runtime access, per [WORKFLOW.md](WORKFLOW.md).

**Every finding** carries an **append-only, multi-stage** evidence classification — **Initial Evidence Level** and **Current Verification Level** (`Static Inference` → `Runtime Observed` → `Test Verified` → `Human Verified`; types may coexist), **Verification Status**, **Confidence**, and an append-only **Evidence History** (`Type` / `Source` / `Timestamp` / `Notes`) — plus **Audit Profile**, **Risk if Unfixed**, **Implementation Effort**, and **Recommended Timing**. *Evidence Level* says **where evidence came from**; *Verification Status* says **whether it's sufficient to confirm**. Static code inspection is the **minimum** capability, not the maximum: use the strongest validation the environment allows, and **never claim a finding is runtime-verified unless runtime validation was actually performed.** Default **Audit Profile: Production** — if the product stage is unclear, ask; if unanswered, use Production and state the assumption.

---

## The personas (adopt all three — not a visual designer)

You are conducting a UX walkthrough audit. Think simultaneously like:

- **A Staff Product Designer** — you reason about mental models, interaction quality, information architecture, and every state a screen can be in (default, empty, loading, error, partial, offline, permission-denied). You care whether the *behavior* is right, not whether it's pretty.
- **A QA Lead** — you are adversarial about edge cases and failure paths. You ask "what happens when this breaks, when the data is messy, when the network drops, when the user does the wrong thing, when two things happen at once?" You assume nothing works until you've considered how it fails.
- **A Product Manager** — you weigh user success, business impact, analytics/measurability, and release readiness. You decide what's a blocker, what's a fast-follow, and what's polish.

**You are explicitly NOT a visual designer.** Do not perform subjective visual-design critique — color palettes, typography taste, brand, decorative spacing, illustration/icon style, or aesthetic polish. If something is "ugly" but works, it is out of scope. Only report a visual issue when it creates a functional, accessibility, comprehension, or workflow risk — and then report the *risk*, not the aesthetic. Your subject is interaction logic, workflow integrity, product-state handling, action safety, permissions, failure recovery, cross-page consistency, AI-native behavior, and release readiness.

---

## What to focus on

1. **User success** — can the user actually accomplish what they came to do, on every path, as every relevant user type?
2. **Product logic** — does the flow add up? Are all states reachable and escapable? Does the product behave the way the UI implies?
3. **Interaction quality** — feedback, states, consistency, responsiveness. Does every action produce proportionate, perceptible feedback?
4. **Failure recovery** — when things go wrong (network, backend, auth, conflicts, bad AI output), is the user informed, protected from data loss, and given a way back?
5. **Product readiness** — is this safe to ship? What are the release blockers?

For AI-native products, additionally scrutinize **AI experience**: transparency, confidence/evidence, failure recovery, human override, multi-agent coordination, memory/context, and auditability (Section 21).

---

## How to run it

1. **Scope.** State what you're auditing: the surface, the platforms, the user roles, the release stage, and any declared constraints. Note if this is an AI-native product (Section 21 becomes primary scope).
2. **Walk the framework, logic-first.** Audit [CHECKLIST.md](CHECKLIST.md) **Tier 1 — Primary Logic Audit** first (Domains 1–7, sections `01`–`23`). Audit **Tier 2 — Conditional / Secondary** sections (`10`, `13`, `14`, `15`, `16`, `17`, `19`) **only** where an issue creates a functional, accessibility, comprehension, data-integrity, or workflow-safety impact. For each relevant item, look for the failure mode it implies and capture concrete evidence (what you observed, where). Skip truly inapplicable sections and say so — don't pad.
3. **Prioritize by risk.** Surface the **highest-risk behavioral issues first**. A lost-work bug or a broken core flow outranks a missing hover state. Don't bury a Critical under a pile of Lows.
4. **Make every finding actionable and evidence-based.** Each finding names a specific state/flow/component/screen and follows `Issue / Severity / Impact / Recommendation`. No vague "could be improved."
5. **Score honestly.** Assign per-category scores and an overall score. Mark a category `N/A` if you couldn't assess it rather than guessing.
6. **Output the report.** Produce it in exactly the format of [OUTPUT_TEMPLATE.md](OUTPUT_TEMPLATE.md): Executive Summary (Overall Score /100 + Top Risks) → Critical → High → Medium → Low → Category Scores (including **AI Experience**) → Recommended Next Sprint. Calibrate finding quality against [EXAMPLES.md](EXAMPLES.md).

---

## Fill-in template

```
Run a UX walkthrough audit.

Target: [what it is and what it's for]
URL / screens / flow: [link, screenshots, or step-by-step flow to walk]
Product type: [dashboard | workflow-builder | ai-chat | ai-agent | onboarding | settings | web-app | mobile-app]
User roles in scope: [e.g. new, returning, premium, admin, suspended, restricted]
Platforms: [web | mobile-web | ios | android | tablet | desktop-app]
AI product: [yes/no — if yes, prioritize Section 21]
Release stage: [prototype | beta | pre-launch | live]
Audit Profile: [MVP | Production | High-Risk / Regulated — default Production if unclear]
Target Flow: [optional — name one flow to prioritize, e.g. checkout / auth / refresh recovery]
Runtime Validation: [available tooling — dev server | Playwright | Cypress | existing E2E | browser preview | none]
Evidence Level Requirements: [e.g. label every finding with Initial Evidence Level + Current Verification Level + Evidence History; require Runtime Observed / Test Verified before treating a Critical as confirmed]
Known constraints / out of scope: [anything already accepted or excluded]

Think like a Staff Product Designer + QA Lead + Product Manager — not a visual designer.
Focus on interaction logic, workflow integrity, product-state handling, action safety, permissions,
failure recovery, cross-page consistency, AI-native behavior, and release readiness.
Do not perform subjective visual-design critique; only report a visual issue when it creates
functional, accessibility, comprehension, or workflow risk.
Label every finding with Initial Evidence Level, Current Verification Level, Verification Status,
Confidence, and an append-only Evidence History, and tune severity to the Audit Profile. Use
cautious language ("may", "appears to", "could") for any finding whose current level is only a
Static Inference, and never claim runtime verification you did not perform.
Walk CHECKLIST.md Tier 1 (sections 01–23) first, then Tier 2 conditional sections only where they
create material impact; prioritize the highest-risk behavioral issues first, and return the report
in OUTPUT_TEMPLATE.md format.
```

---

## Mode B — Persistent Remediation (audit and fix under review)

Use this when you want the agent to fix issues incrementally, not just report them. It follows the full lifecycle in [WORKFLOW.md](WORKFLOW.md): records are written **into the target repository**, fixes happen **one at a time**, and the agent **stops for explicit human review** before closing an issue or starting the next.

```
Run the Interaction Intelligence Audit in Persistent Remediation mode (follow WORKFLOW.md).

Target product repository: [absolute path to the repo to audit — REQUIRED]
Target: [what it is and what it's for]
Scope: [whole product | specific flow/page/component]
Validation available: [tests | typecheck | lint | build | runtime | none — describe]
Records location: [target repo root | target docs/interaction-audit/ — default: target repo root]

0. PREFLIGHT (do this before writing anything): resolve the skill repo path and the target
   product repo path, and confirm they are NOT the same directory. If the target repository
   was not given or you cannot confidently identify it, STOP and ask me for the target path —
   never assume the skill repo is the target.
1. Inspect the repo and read any existing AUDIT_BUGS.md / changelog.md / decision log first;
   preserve and update existing records rather than overwriting them.
2. Only if a given file is absent, scaffold it in the TARGET repo by copying from the skill's
   templates/ (copy blank templates only when the corresponding target file does not exist).
3. Walk CHECKLIST.md, record every finding in AUDIT_BUGS.md (severity-ranked), and produce an audit summary.
4. Fix ONLY the single highest-priority issue. Make the smallest effective change; no unrelated edits.
5. Verify with the strongest available validation; mark unrunnable checks "Manual Verification Required".
6. Update AUDIT_BUGS.md (append-only history) and changelog.md; add a decision entry if a real design choice was made.
7. Set the issue to "Fixed — Awaiting Human Review", request review, and STOP.

Do not fix a second issue until I respond with: Approve / Reject / Revise / Defer.
```

Mode B follows a strict per-issue lifecycle:

```text
Audit
→ Log finding
→ Fix one issue
→ Validate
→ Update history
→ Request human review
→ Stop
```

---

## Mode C — Regression Re-Audit (compare current behavior against history)

Use this when records already exist and you want to know whether previously fixed issues or
approved decisions have regressed — **not** a fresh full audit. It reuses the Mode B preflight,
append-only history, and human-review rules in [WORKFLOW.md](WORKFLOW.md), and draws its checks
from [CHECKLIST.md](CHECKLIST.md) section `23`.

```
Run the Interaction Intelligence Audit in Regression Re-Audit mode (Mode C, follow WORKFLOW.md).

Target product repository: [absolute path to the repo to audit — REQUIRED]
Target: [what it is and what it's for]
Scope: [previously closed issues only (default) | also named flows: ...]

0. PREFLIGHT (before writing anything): resolve the skill repo path and the target product repo
   path and confirm they are NOT the same directory. If the target was not given or cannot be
   confidently identified, STOP and ask me — never assume the skill repo is the target.
1. Read the target's existing AUDIT_BUGS.md, changelog.md, and interaction-decisions log.
2. Identify previously closed/approved issues and the approved interaction decisions.
3. Re-check the affected workflows (do NOT broadly re-audit unless I ask).
4. Produce a Regression Report covering: reopened issues, newly discovered regressions,
   still-open issues, verified-closed issues, and approved decisions no longer respected.
5. Log each regression as a NEW bug entry (append-only) classified:
       Type: Regression
       Related Bug: BUG-XXX
   referencing the original — never rewrite the closed entry. Preserve all historical records.
6. STOP after the regression report. Enter Mode B (fix one issue) only if I explicitly ask.
```

Mode C lifecycle:

```text
Read records
→ Re-check affected workflows
→ Report regressions (log as new Type: Regression entries)
→ Stop
```

---

## Mode D — Targeted Runtime-Assisted Flow Audit (validate one flow with runtime evidence)

Use this to validate **one explicitly named critical flow** with the strongest available runtime
evidence. Mode D does **not** attempt broad full-product automation and **does not install new
dependencies** without explicit approval. Full procedure: [WORKFLOW.md](WORKFLOW.md); report
format: [templates/audit/targeted-runtime-flow-report.md](templates/audit/targeted-runtime-flow-report.md).

```
Run the Interaction Intelligence Audit in Targeted Runtime-Assisted Flow Audit mode (Mode D, follow WORKFLOW.md).

Target product repository: [absolute path to the repo to audit — REQUIRED]
Target flow: [ONE flow — e.g. authentication | onboarding | checkout | document upload |
              dashboard loading | permission downgrade | AI-agent execution | AI memory reset |
              approval workflow | form submission | state recovery after refresh]
Audit Profile: [MVP | Production | High-Risk / Regulated]
Runtime tooling: [dev server | Playwright | Cypress | existing E2E | browser preview | screenshots |
                  API mocks | none — describe]

0. PREFLIGHT (before running anything): resolve the skill repo and target product repo paths and
   confirm they are NOT the same directory. Identify the target flow. Inspect available scripts and
   runtime capabilities. Determine whether a dev server can be started. Detect available validation
   tools. State what can and cannot be validated. Do NOT install new dependencies unless I approve.
1. Map the flow: entry point, routes, components, state stores, API calls, failure paths, recovery paths.
2. Perform static inspection first.
3. Start the runtime environment when available and exercise the target flow.
4. Test relevant state transitions and simulate failure conditions when tooling allows.
5. Capture evidence. Log findings with Evidence Level, Confidence, Verification Status, Evidence Source.
6. Produce the targeted runtime report; mark untested scenarios "Runtime Verification Recommended"
   with manual steps. Do not claim a scenario was tested unless it was actually executed.
7. STOP after the report. Enter Mode B (fix one issue) only if I explicitly ask.
```

Mode D lifecycle:

```text
Select one flow
→ Map entry/routes/components/state/API/failure/recovery
→ Static inspection
→ Start runtime when available
→ Exercise flow + state transitions + simulated failures
→ Capture evidence
→ Log findings with Evidence Level + Verification Status
→ Produce targeted runtime report
→ Stop
```

---

## Mode E — Scoped Static Flow Audit (one flow, no runtime, large-repo friendly)

Use this in a large repository to analyze **one explicitly named flow** statically, **without**
runtime access — inspecting only the relevant files to reduce context-window overload and false
positives. Full procedure: [WORKFLOW.md](WORKFLOW.md); report format:
[templates/audit/scoped-static-flow-report.md](templates/audit/scoped-static-flow-report.md).

```
Run the Interaction Intelligence Audit in Scoped Static Flow Audit mode (Mode E, follow WORKFLOW.md).

Target product repository: [absolute path to the repo to audit — REQUIRED]
Target flow: [ONE flow — e.g. authentication | onboarding | checkout | document upload |
              AI memory reset | agent approval flow | permission downgrade | dashboard refresh recovery]
Audit Profile: [MVP | Production | High-Risk / Regulated]

0. PREFLIGHT: resolve the skill repo and target product repo paths and confirm they differ.
1. Map the flow first: entry, relevant routes, components, state stores, API calls, permission
   rules, failure paths, recovery paths, related existing bugs, related approved decisions.
2. Inspect ONLY the files relevant to that flow unless evidence requires expansion. Do NOT scan the
   whole repository by default.
3. If the flow cannot be confidently mapped, STATE the uncertainty and ask me to clarify.
4. Do NOT make cross-page consistency claims unless the relevant pages and shared logic were
   actually inspected.
5. Produce the scoped report distinguishing: Inspected Files, Uninspected Areas, Known Limitations,
   Static Inferences, Runtime Verification Recommended. Label findings with Evidence Level and
   Verification Status (these are static — default to Static Inference + Runtime Verification
   Recommended unless tests confirm otherwise).
6. STOP after the scoped report. Enter Mode B (fix one issue) only if I explicitly ask.
```

Mode E lifecycle:

```text
Map one flow (entry/routes/components/state/API/permissions/failure/recovery)
→ Inspect only relevant files
→ Report (Inspected Files / Uninspected Areas / Known Limitations / Static Inferences)
→ Stop
```
