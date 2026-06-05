---
name: interaction-intelligence-audit
description: Audit digital products for interaction-design gaps, missing states, broken user flows, AI-experience risks, accessibility issues, and release-readiness blockers.
version: 0.2.0
---

> **The YAML frontmatter above is optional metadata.** Claude Code and the Claude Agent SDK use
> `name`/`description`/`version` for auto-discovery; other agents simply read this file as plain
> Markdown instructions and ignore it. **No part of this skill depends on it** — or on any
> vendor-specific tool, memory system, planning mode, or proprietary feature.

# Interaction Intelligence Audit

A diagnostic skill that performs a comprehensive **UX, interaction-design, product-readiness, and release audit** of a web application, SaaS product, AI product, workflow builder, or dashboard. It systematically hunts for UX gaps, missing states, broken mental models, interaction inconsistencies, edge cases, accessibility issues, error-handling weaknesses, product-logic flaws, missing analytics, performance risks, and release blockers — then returns a severity-ordered, actionable report.

This is an **audit, not a redesign**, and it is explicitly **not a visual-style critique**. It evaluates whether users can succeed, whether the product logic holds up under stress, and whether the product is ready to ship.

---

## Agent compatibility

This skill is **coding-agent agnostic**. It runs on any repository-capable coding agent —
**Claude Code, OpenAI Codex, Cursor, Windsurf, Gemini CLI, the GitHub Copilot coding agent**, and
equivalent future tools — because it relies only on generic repository operations (inspect/search
files, read docs, edit source, create/update Markdown, run available validation, inspect diffs).
If a capability is unavailable, use the closest equivalent, document the limitation, and mark
unverifiable checks **`Manual Verification Required`**. See [`README.md`](README.md) for per-agent
setup.

## Two modes

- **Mode A — One-shot Audit.** Walk the framework and return a single severity-ordered report.
  Nothing is written to the target unless asked. Format: [`OUTPUT_TEMPLATE.md`](OUTPUT_TEMPLATE.md).
  This is the default, and the rest of this file describes it.
- **Mode B — Persistent Remediation.** Audit *and* fix issues **one at a time, under human
  review**, keeping an append-only history (`bug.md` / `changelog.md` / decision log) **inside the
  target repository**. Fix one → stop → await explicit human review → continue. Full lifecycle:
  [`WORKFLOW.md`](WORKFLOW.md). Reusable scaffolds: [`templates/`](templates/).

**Audit-records location:** in Mode B, all runtime records are created **inside the target product
repository being audited — never inside this skill repository.**

**Target-repository confirmation (mandatory, Mode B).** Before creating or updating *any* runtime
audit file, the agent must identify and confirm:

1. the **reusable skill repository** path (where this `SKILL.md` lives),
2. the **target product repository** path, and
3. that **the two paths are not the same directory**.

If the target product repository has not been explicitly provided or cannot be confidently
identified, the agent must **stop and ask the user for the target repository path**. The agent
must **never assume the reusable skill repository itself is the target product repository.** The
full preflight is specified in [`WORKFLOW.md`](WORKFLOW.md).

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

- [README.md](README.md) — purpose, when (not) to use, inputs/outputs, per-agent setup, example invocations
- [CHECKLIST.md](CHECKLIST.md) — the 21-category audit framework (actionable checks)
- [OUTPUT_TEMPLATE.md](OUTPUT_TEMPLATE.md) — the mandatory Mode A report format + severity rubric
- [EXAMPLES.md](EXAMPLES.md) — worked example findings for five product types
- [PROMPT.md](PROMPT.md) — the default invocation prompts (Mode A and Mode B) and audit personas
- [WORKFLOW.md](WORKFLOW.md) — Mode B persistent-remediation lifecycle (statuses, one-fix-at-a-time, human-review gate)
- [templates/](templates/) — reusable scaffolds (`bug.md`, `changelog.md`, `audit/`) copied into the target repo for Mode B
