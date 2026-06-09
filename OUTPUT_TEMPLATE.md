# Interaction Intelligence Audit — Output Template

Produce the report in **exactly** this structure and order. This is a **logic-first behavioral
audit, not a visual-design scorecard** — findings concern workflow integrity, product-state
logic, recovery behavior, action safety, permissions, cross-page consistency, AI-native
interaction risks, and regression/release readiness. Lead with the highest-risk behavioral
issues. Every issue uses the **finding shape** below. Do not invent findings to fill a section —
if a severity tier is empty, say "None found."

**Visual observations are not findings on their own.** Include a visual detail only as
*supporting evidence* for a behavioral problem (functional, accessibility, comprehension,
data-integrity, or workflow-safety impact), and describe the impact — not the aesthetic.

**Do not overstate certainty.** Every finding states how it was identified and how strongly it is
verified. Never present a `Static Inference` as confirmed runtime fact; use cautious language
(*"may," "appears to," "could," "requires runtime verification"*) for anything not actually
observed, tested, or human-confirmed.

---

## Finding shape (every issue uses these fields)

Evidence is **append-only and multi-stage** — keep `Evidence History`, don't overwrite a single
level; types may coexist (e.g. `Runtime Observed + Test Verified`).

```text
Issue:                      <specific, evidence-based — name the state/flow/component/screen>
Severity:                   Critical | High | Medium | Low
Audit Profile:              MVP | Production | High-Risk / Regulated
Initial Evidence Level:     Static Inference | Runtime Observed | Test Verified | Human Verified
Current Verification Level: strongest evidence reached (may combine, e.g. "Runtime Observed + Test Verified")
Verification Status:        Verified | Runtime Verification Recommended | Manual Verification Required | Not Yet Verified
Confidence:                 High | Medium | Low
Evidence History:           append-only — one entry per evidence addition:
  - Type: <...>  Source: <file / route / component / state store / API / test / observation / screenshot / note>  Timestamp: <YYYY-MM-DD HH:mm>  Notes: <...>
Risk if Unfixed:            <what happens to the user / business if shipped as-is>
Implementation Effort:      Low | Medium | High
Recommended Timing:         Blocker | Current Sprint | Backlog | Defer
Impact:                     <who is affected and what it costs them / the business>
Recommendation:             <concrete, actionable fix>
Manual Verification Steps:  <exact reviewer steps when Verification Status is not "Verified">
```

**Evidence Level vs. Verification Status — different axes:** *Evidence Level* describes **where the
evidence came from**; *Verification Status* describes **whether the available evidence is sufficient
to confirm** the issue or fix. (e.g. `Static Inference` → `Runtime Verification Recommended`;
`Runtime Observed + Test Verified` → `Verified`.)

---

## Severity rubric (use consistently)

- **Critical** — Blocks the core task, loses user work/data, exposes sensitive data, or breaks a primary flow for a real user segment. A release blocker.
- **High** — Significantly degrades success or trust on a common path; no acceptable workaround. Should be fixed before/right after launch.
- **Medium** — Noticeable friction, an unhandled secondary state, or a gap that affects some users or edge cases. Fix soon.
- **Low** — Polish, minor inconsistency, or rare edge case. Improves quality but not blocking.

Scores are honest, not generous. Mark a category `N/A` if it could not be assessed (no access / out of scope) rather than guessing.

---

# Executive Summary

**Audited:** <what was audited — surface, platforms, roles, release stage>

**Audit Profile:** <MVP | Production | High-Risk / Regulated>

**Overall Score: X/100** <optional heuristic risk score — not a precision metric>

**Top Risks:**
- <highest-risk behavioral issue, one line — workflow / state-logic / recovery / action-safety / permission / cross-page / AI-native / regression>
- <second>
- <third>

<2–4 sentence narrative: is this safe to ship, what's the headline behavioral concern (task completion, data integrity, recoverability, trust, or readiness), and what would most move the needle.>

---

# Critical Issues

> Release blockers. Each must be resolved (or explicitly accepted) before ship.

Each issue uses the full **finding shape** above (Issue · Severity · Audit Profile · Evidence
Level · Confidence · Verification Status · Evidence Source · Risk if Unfixed · Implementation
Effort · Recommended Timing · Impact · Recommendation · Manual Verification Steps).

```text
Issue:                      <name the state/flow/component/screen>
Severity:                   Critical
Audit Profile:              <MVP | Production | High-Risk / Regulated>
Initial Evidence Level:     <Static Inference | Runtime Observed | Test Verified | Human Verified>
Current Verification Level: <strongest evidence reached; may combine, e.g. "Runtime Observed + Test Verified">
Verification Status:        <Verified | Runtime Verification Recommended | Manual Verification Required | Not Yet Verified>
Confidence:                 <High | Medium | Low>
Evidence History:
  - Type: <...>  Source: <...>  Timestamp: <YYYY-MM-DD HH:mm>  Notes: <...>
Risk if Unfixed:            <consequence if shipped as-is>
Implementation Effort:      <Low | Medium | High>
Recommended Timing:         <Blocker | Current Sprint | Backlog | Defer>
Impact:                     <who is affected and what it costs>
Recommendation:             <concrete, actionable fix>
Manual Verification Steps:  <exact reviewer steps when not "Verified">
```

<repeat per issue, or "None found.">

---

# High Priority Issues

Use the full **finding shape** above. (For a Static Inference, use cautious language and set
Verification Status to `Runtime Verification Recommended` or `Manual Verification Required`.)

<repeat per issue, or "None found.">

---

# Medium Priority Issues

Use the full **finding shape** above.

<repeat per issue, or "None found.">

---

# Low Priority Improvements

Use the full **finding shape** above.

<repeat per issue, or "None found.">

---

# Verification & Evidence Summary

Group every finding above by how strongly it is verified, so the reader can separate confirmed
defects from code-level hypotheses. (Counts + the BUG/issue references in each bucket.)

- **Verified Findings** (Runtime Observed / Test Verified / Human Verified): <count> — <refs>
- **Static Inferences** (code-level, not yet runtime-confirmed): <count> — <refs>
- **Runtime Verification Recommended:** <count> — <refs + the tooling that would confirm them>
- **Manual Verification Required:** <count> — <refs + exact reviewer steps>

State the **runtime tooling actually used** (if any) and what could not be validated in this
environment. Do not claim a step passed unless it was actually performed.

---

# Category Scores

A summary **rollup of the logic-first framework** in [CHECKLIST.md](CHECKLIST.md): each score aggregates the framework section(s) named in parentheses, so every section `01`–`23` is covered. Score each `/10` with a one-line rationale; mark a bucket `N/A` (rather than guessing) if its sections could not be assessed. **Lead with the primary logic domains** — the conditional/secondary buckets are scored only where an issue created a functional, accessibility, comprehension, data-integrity, or workflow-safety impact (otherwise `N/A`).

### Primary — Interaction Logic (Tier 1)

- **User-Flow Integrity** (02, 03): X/10 — <rationale>
- **Product-State Logic** (01, 04, 05, 09, 11): X/10 — <rationale>
- **Action Safety & Data Integrity** (06, 07, 08): X/10 — <rationale>
- **Cross-Page & Cross-Component Consistency** (22): X/10 — <rationale>
- **Permissions & Access Control** (12): X/10 — <rationale>
- **AI-Native Interaction Logic** (21): X/10 — <rationale; `N/A` if not an AI product>
- **Regression & Release Readiness** (18, 20, 23): X/10 — <rationale>

### Conditional / Secondary (Tier 2 — score only where it materially affected behavior; else `N/A`)

- **Accessibility** (13): X/10 — <rationale>
- **Responsive & Internationalization** (14, 15): X/10 — <rationale>
- **Motion** (10): X/10 — <rationale>
- **Analytics** (16): X/10 — <rationale>
- **Performance** (17): X/10 — <rationale>
- **Security & Privacy** (19): X/10 — <rationale>

---

# Recommended Next Sprint

The three things most worth doing next, in order. Each should tie back to a Critical/High issue above.

**Priority 1:** <what + why it's first>
**Priority 2:** <what + why>
**Priority 3:** <what + why>
