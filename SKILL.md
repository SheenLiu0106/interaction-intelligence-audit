---
name: interaction-intelligence-audit
description: Audit digital products for interaction-design gaps, missing states, broken user flows, AI-experience risks, accessibility issues, and release-readiness blockers.
version: 0.1.0
---

# Interaction Intelligence Audit

A diagnostic skill that performs a comprehensive **UX, interaction-design, product-readiness, and release audit** of a web application, SaaS product, AI product, workflow builder, or dashboard. It systematically hunts for UX gaps, missing states, broken mental models, interaction inconsistencies, edge cases, accessibility issues, error-handling weaknesses, product-logic flaws, missing analytics, performance risks, and release blockers — then returns a severity-ordered, actionable report.

This is an **audit, not a redesign**, and it is explicitly **not a visual-style critique**. It evaluates whether users can succeed, whether the product logic holds up under stress, and whether the product is ready to ship.

---

## When to use

- You want a structured pre-release or pre-launch review of a product surface.
- You are reviewing a **dashboard**, **workflow/agent builder**, **AI chat or AI-native product**, **onboarding flow**, **settings area**, or any **SaaS or web app** screen/flow.
- You want to find what breaks before users do: empty states, error paths, edge cases, permission denials, offline behavior, destructive actions, accessibility gaps.
- You need a prioritized issue list (Critical → Low) plus category scores you can take into sprint planning.
- The product has AI behavior (generation, agents, tool calls, memory) and you want to audit trust, explainability, recovery, and user control.

## When NOT to use

- You want a **visual / brand / aesthetic critique** (color, typography, spacing taste). This skill deliberately avoids style opinions.
- You want the product **rebuilt or redesigned**. This skill diagnoses and recommends; it does not produce production code or a redesign.
- You want a pure **accessibility certification** (WCAG conformance audit with formal pass/fail per criterion). A11y is one of 21 categories here, not the whole scope — use a dedicated a11y audit for certification.
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
Capture the inputs above. State plainly at the top of the report: what is being audited, which platforms and user roles are in scope, the release stage, and any declared constraints. If the target is AI-native, flag that Section 21 (AI Experience) is in primary scope.

### Step 2 — Adopt the audit lens
Adopt the three personas defined in [PROMPT.md](PROMPT.md): a **Staff Product Designer**, a **QA Lead**, and a **Product Manager** — *not* a visual designer. Every finding must trace back to user success, product logic, interaction quality, failure recovery, or release readiness.

### Step 3 — Walk the framework
Walk the 21-category framework in [CHECKLIST.md](CHECKLIST.md), from `01 Preconditions` through `21 AI Experience`. For each relevant item, look for the failure mode the question implies, and record concrete evidence (what you saw, where). Skip categories that are genuinely not applicable and say so — do not pad. **Prioritize the highest-risk user-experience issues first**; do not get lost in low-severity polish before the critical paths are covered.

### Step 4 — Severity & evidence
Assign each finding a severity (Critical / High / Medium / Low) using the rubric in [OUTPUT_TEMPLATE.md](OUTPUT_TEMPLATE.md). Every finding needs an Impact (who is affected and how) and a concrete Recommendation. No generic observations — tie each one to a specific state, flow, component, or screen.

### Step 5 — Output report
Produce the report exactly in the format defined by [OUTPUT_TEMPLATE.md](OUTPUT_TEMPLATE.md): Executive Summary (Overall Score /100 + Top Risks), then Critical → High → Medium → Low issues, then Category Scores (including **AI Experience**), then a Recommended Next Sprint (P1/P2/P3). Use [EXAMPLES.md](EXAMPLES.md) to calibrate finding quality and severity.

---

## Rules & constraints

- **Audit only.** Diagnose and recommend; do not redesign or write production code.
- **Not visual critique.** Do not score color/typography/brand taste. Interaction, logic, states, recovery, accessibility, analytics, performance, and readiness are the subject.
- **Evidence-based.** Every finding references a specific state, flow, component, or screen. No vague "could be better."
- **Severity-ordered.** Always lead with the highest-risk issues. Critical issues are release blockers.
- **Reusable across product types.** The same framework applies to dashboards, workflow builders, AI products, web apps, and mobile — emphasize the categories that fit the target.
- **Honest scoring.** Per-category scores plus an overall score. If a category can't be assessed (no access, out of scope), mark it `N/A` rather than guessing.
- **AI products get Section 21.** When the target is AI-native, treat `21 AI Experience` as primary scope and include it in Category Scores.

---

## Files

- [README.md](README.md) — purpose, when (not) to use, inputs/outputs, example invocations
- [CHECKLIST.md](CHECKLIST.md) — the 21-category audit framework (actionable checks)
- [OUTPUT_TEMPLATE.md](OUTPUT_TEMPLATE.md) — the mandatory report format + severity rubric
- [EXAMPLES.md](EXAMPLES.md) — worked example findings for five product types
- [PROMPT.md](PROMPT.md) — the default invocation prompt and audit personas
