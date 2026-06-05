# Interaction Intelligence Audit — Output Template

Produce the report in **exactly** this structure and order. Lead with the highest-risk issues. Every issue uses the `Issue / Severity / Impact / Recommendation` shape. Do not invent findings to fill a section — if a severity tier is empty, say "None found."

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
- <highest-risk issue, one line>
- <second>
- <third>

<2–4 sentence narrative: is this shippable, what's the headline concern, and what would most move the needle.>

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

A summary **rollup of the 21-category framework** in [CHECKLIST.md](CHECKLIST.md): each score aggregates the framework section(s) named in parentheses, so every section `01`–`21` is covered. Score each `/10` with a one-line rationale; mark a bucket `N/A` (rather than guessing) if its sections could not be assessed.

- **Preconditions & Entry States** (01): X/10 — <rationale>
- **User Flows & Continuity** (02): X/10 — <rationale>
- **Information Architecture** (03): X/10 — <rationale>
- **Page & Data States** (04, 05): X/10 — <rationale>
- **Interaction & Feedback** (06, 07, 09, 10): X/10 — <rationale>
- **Destructive Actions & Error Handling** (08, 11): X/10 — <rationale>
- **Accessibility** (13): X/10 — <rationale>
- **Responsive & Internationalization** (14, 15): X/10 — <rationale>
- **Permissions, Security & Privacy** (12, 19): X/10 — <rationale>
- **Analytics** (16): X/10 — <rationale>
- **Performance** (17): X/10 — <rationale>
- **Versioning** (18): X/10 — <rationale>
- **AI Experience** (21): X/10 — <rationale; `N/A` if not an AI product>
- **Release Readiness** (20): X/10 — <rationale>

---

# Recommended Next Sprint

The three things most worth doing next, in order. Each should tie back to a Critical/High issue above.

**Priority 1:** <what + why it's first>
**Priority 2:** <what + why>
**Priority 3:** <what + why>
