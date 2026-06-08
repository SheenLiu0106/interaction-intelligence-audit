# Interaction Intelligence Audit — Output Template

Produce the report in **exactly** this structure and order. This is a **logic-first behavioral
audit, not a visual-design scorecard** — findings concern workflow integrity, product-state
logic, recovery behavior, action safety, permissions, cross-page consistency, AI-native
interaction risks, and regression/release readiness. Lead with the highest-risk behavioral
issues. Every issue uses the `Issue / Severity / Impact / Recommendation` shape. Do not invent
findings to fill a section — if a severity tier is empty, say "None found."

**Visual observations are not findings on their own.** Include a visual detail only as
*supporting evidence* for a behavioral problem (functional, accessibility, comprehension,
data-integrity, or workflow-safety impact), and describe the impact — not the aesthetic.

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

**Overall Score: X/100**

**Top Risks:**
- <highest-risk behavioral issue, one line — workflow / state-logic / recovery / action-safety / permission / cross-page / AI-native / regression>
- <second>
- <third>

<2–4 sentence narrative: is this safe to ship, what's the headline behavioral concern (task completion, data integrity, recoverability, trust, or readiness), and what would most move the needle.>

---

# Critical Issues

> Release blockers. Each must be resolved (or explicitly accepted) before ship.

**Issue:** <specific, evidence-based — name the state/flow/component/screen>
**Severity:** Critical
**Impact:** <who is affected and what it costs them / the business>
**Recommendation:** <concrete, actionable fix>

<repeat per issue, or "None found.">

---

# High Priority Issues

**Issue:**
**Severity:** High
**Impact:**
**Recommendation:**

<repeat per issue, or "None found.">

---

# Medium Priority Issues

**Issue:**
**Severity:** Medium
**Impact:**
**Recommendation:**

<repeat per issue, or "None found.">

---

# Low Priority Improvements

**Issue:**
**Severity:** Low
**Impact:**
**Recommendation:**

<repeat per issue, or "None found.">

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
