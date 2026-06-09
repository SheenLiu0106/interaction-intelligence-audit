# Interaction Intelligence Audit

**Interaction Intelligence Audit is a coding-agent-agnostic workflow for auditing and remediating interaction logic, user-flow integrity, product-state failures, and AI-native interaction risks.**

> **This skill focuses on how a product behaves, not how it looks.**

It is **logic-first**: it detects and remediates **behavioral failures** in software products — issues that affect task completion, data integrity, recoverability, trust, and product readiness — then returns a severity-ordered, evidence-based report you can take straight into sprint planning. It deliberately distinguishes itself from visual UI-review tools.

It is designed to work with repository-capable coding agents — [Claude Code](https://claude.com/claude-code), OpenAI Codex, Cursor, Windsurf, Gemini CLI, the GitHub Copilot coding agent, and equivalent tools — because it relies only on generic repository operations and a set of Markdown instructions.

The skill works in five modes:

- **Mode A — One-shot Audit** *(default)*: walk the framework and return a single severity-ordered report ([OUTPUT_TEMPLATE.md](OUTPUT_TEMPLATE.md)). Nothing is written to the target unless you ask.
- **Mode B — Persistent Remediation**: audit *and* fix issues **one at a time, under human review**, keeping an append-only history (`bug.md` / `changelog.md` / decision log) **inside the target repository**. Fix one → stop → await explicit human review → continue. See [WORKFLOW.md](WORKFLOW.md); scaffolds live in [templates/](templates/).
- **Mode C — Regression Re-Audit**: compare *current* behavior against the target repo's historical records, re-check the workflows behind previously closed issues, and report reopened issues, new regressions, still-open issues, verified-closed issues, and approved decisions no longer respected. Regressions are logged as **new** entries (`Type: Regression`, `Related Bug: BUG-XXX`). It stops after the regression report unless explicitly asked to enter Mode B. See [WORKFLOW.md](WORKFLOW.md).
- **Mode D — Targeted Runtime-Assisted Flow Audit**: validate **one named critical flow** using the strongest available runtime evidence (dev server, existing E2E suite, browser preview, screenshots), falling back to flagged static inference when no runtime tooling exists. See [WORKFLOW.md](WORKFLOW.md).
- **Mode E — Scoped Static Flow Audit**: statically analyze **one named flow** in a large repo without runtime access, inspecting only the relevant files to reduce context overload and false positives. See [WORKFLOW.md](WORKFLOW.md).

In Modes B, C, D, and E, **all runtime records are created inside the product repository being audited — never inside this skill repository.** A mandatory preflight confirms the target repository is not the skill repository before anything is written.

**Recommended use:** A — broad initial audit · B — controlled remediation · C — regression re-audit · D — targeted runtime validation · E — targeted static analysis for large repos.

---

## What This Skill Audits

The framework is **logic-first**, organized as seven primary domains (Tier 1), with a smaller set of conditional/secondary checks (Tier 2). See [CHECKLIST.md](CHECKLIST.md) for the full, actionable framework (sections `01`–`23`).

**Tier 1 — Primary Logic Audit (always in scope):**

1. **User-Flow Integrity** — can users complete important tasks? Broken workflows, dead ends, unclear next actions, missing back-navigation, interrupted flows, lost progress, invalid transitions, unclear completion, workflow loops, abandonment risk.
2. **Product-State Logic** — loading, empty, error, success, partial-success, retry, disabled, and stale states; optimistic updates and rollback; refresh/session recovery; persistence after navigation; conflict handling; state synchronization across pages.
3. **Action Safety & Data Integrity** — duplicate submission, silent mutations, accidental/destructive actions without confirmation, missing undo, save failures, unsaved-change risk, data loss, late or missing validation, irreversible-action clarity, race conditions, permission failures without recovery.
4. **Cross-Page & Cross-Component Consistency** — equivalent actions behaving consistently: button outcomes, validation, save/retry/navigation/permission behavior, object status, duplicated logic, loading/error behavior, modal/confirmation rules.
5. **Permissions & Access Control** — inaccessible actions still appearing actionable, missing permission explanations, authorization failure without recovery guidance, inconsistent role behavior, hidden permission-driven state changes, unsafe fallbacks.
6. **AI-Native Interaction Logic** *(first-class)* — AI processing/progress states, failure recovery, context loss, memory overwrite, stale context, provenance/evidence, confidence, generated-vs-verified distinction, human override, unsafe auto-apply, multi-agent coordination, escalation paths, transparency, auditability.
7. **Regression & Release Readiness** — reopened workflow failures, reappearing bugs, violated approved decisions, incompatible cross-flow changes, missing validation coverage, release-blocking interaction risks, migration-related state failures, version compatibility.

**Tier 2 — Conditional / Secondary (audited only when an issue creates a functional, accessibility, comprehension, data-integrity, or workflow-safety impact):** Motion, Accessibility, Responsive Design, Internationalization, Analytics, Performance, Security & Privacy.

---

## What This Skill Does Not Audit

This skill is **not** a visual-design review tool. It does **not** evaluate:

- visual aesthetics, brand styling, color-palette quality
- typography taste, decorative spacing polish, illustration style
- icon-style consistency, visual-trend alignment, subjective design preferences
- Figma craftsmanship or pixel-perfect visual review

Visual observations are reported **only** when they create a measurable functional, accessibility, comprehension, or workflow problem — and then they are reported as that problem, not as an aesthetic note:

```text
Do not report:
"The button color is not visually polished."

Report:
"The primary action has insufficient contrast, making the action difficult to identify and
increasing task-completion risk."
```

```text
Do not report:
"The spacing between cards feels inconsistent."

Report:
"The spacing causes unrelated actions to appear grouped together, increasing the risk of
selecting the wrong action."
```

---

## Setup

The skill is just Markdown + generic repository operations, so any repository-capable agent can run it. Clone it once:

```bash
git clone https://github.com/SheenLiu0106/interaction-intelligence-audit.git
```

### Universal fallback (works with any agent)

Regardless of agent, you can always run the skill by either:

- **providing the agent with [`PROMPT.md`](PROMPT.md)** (paste it, or point the agent at the file), or
- **instructing the agent to read [`SKILL.md`](SKILL.md), [`WORKFLOW.md`](WORKFLOW.md) (for Mode B), and the relevant files in [`templates/`](templates/) directly.**

This path depends on no vendor-specific feature and is the recommended default if anything below doesn't apply to your setup.

### Example integration paths (illustrative, not guaranteed)

> These are **recommended setup patterns**, not contracts. **Agent-specific configuration may vary by version and environment** — treat the file names and mechanisms below as examples and confirm them against your agent's current docs. When in doubt, use the universal fallback above.

| Agent | Example integration path |
|---|---|
| **Claude Code** | Cloning into a skills directory (e.g. `~/.claude/skills/…`) typically enables slash-style invocation; a restart may be needed after the first install. |
| **OpenAI Codex** | Often references `SKILL.md` / `WORKFLOW.md` from an `AGENTS.md`-style context file. |
| **Cursor / Windsurf** | Commonly add the folder to the workspace and reference the files from a project rule. |
| **Gemini CLI** | Usually references the files from a context file (e.g. a `GEMINI.md`-style file). |
| **GitHub Copilot coding agent** | Often references the files from a repo-level instructions file (e.g. under `.github/`). |
| **Any other repo-capable agent** | Give it read access to this folder and use the universal fallback. |

The frontmatter in `SKILL.md` is optional metadata used only by some agents (e.g. Claude Code / the Agent SDK) for auto-discovery; other agents read `SKILL.md` as plain instructions.

---

## Usage

Describe what to audit and which mode you want. Mode A can begin from a described surface, URL, screenshots, design frames, or a step-by-step flow. Deeper validation of state transitions, persistence, permissions, and regressions may require repository access, runtime inspection, or manual verification.

**Mode A — report only:**

```text
Run the Interaction Intelligence Audit (SKILL.md / PROMPT.md) on this project.
Start with the primary user journey. Do not modify code.
Produce an evidence-based report ranked by severity, per OUTPUT_TEMPLATE.md.
```

Scope it narrowly, or focus on AI-native behavior:

```text
Audit the onboarding flow for first-time users. Report only.
```

```text
Audit the AI workflow builder. Focus on agent handoffs, failure recovery,
human override, and auditability (CHECKLIST.md section 21). Report only.
```

**Mode B — audit and fix under review:**

```text
Run the Interaction Intelligence Audit in Persistent Remediation mode (WORKFLOW.md).
Confirm the target repo (not the skill repo), scaffold bug.md / changelog.md in the
TARGET repo from templates/, record findings, then fix only the highest-priority issue,
stop, and wait for my review.
```

**Mode C — regression re-audit:**

```text
Run the Interaction Intelligence Audit in Regression Re-Audit mode (Mode C, WORKFLOW.md).
Read the target repo's existing bug.md / changelog.md / interaction decisions, re-check the
workflows behind previously closed issues, and produce a regression report (reopened, new
regressions, still-open, verified-closed, and violated approved decisions). Log regressions
as new Type: Regression entries referencing the original bug IDs, then stop.
```

See [PROMPT.md](PROMPT.md) for the full fill-in templates and audit personas.

---

## Output Format

**Mode A** returns a single structured audit report based on [OUTPUT_TEMPLATE.md](OUTPUT_TEMPLATE.md).

**Mode B** maintains persistent remediation records inside the target repository, including `bug.md`, `changelog.md`, and interaction-decision history.

**Mode C** returns a regression report and logs newly identified regressions as new bug entries referencing the original issue IDs.

Mode A findings are grouped by severity, highest-risk first:

- Critical
- High
- Medium
- Low

Each finding identifies a concrete state, flow, component, or screen and follows an Issue / Severity / Impact / Recommendation structure.

The report also includes:

- an Executive Summary with top behavioral risks and an optional heuristic risk score
- category-level risk indicators mapped to the framework
- a Recommended Next Sprint (Priority 1 / 2 / 3)

See [EXAMPLES.md](EXAMPLES.md) for worked example findings, including logic-first examples and per-product-type findings.

---

## AI-native product coverage

When the target is an AI or agent product, the skill treats its dedicated **AI-Native Interaction Logic** section as primary scope. It covers:

- **Model transparency** — what the AI is doing, and whether output was generated, retrieved, inferred, or entered.
- **Confidence and evidence** — uncertainty signals, citations, verifiable sources.
- **Failure recovery** — retry/regenerate/edit, distinguishing model vs. tool vs. data failures, protecting work.
- **Human override** — editing, rejecting, approving, and intervening before irreversible actions.
- **Multi-agent coordination** — agent roles, handoffs, task status and ownership.
- **AI memory and context** — what context is in use, and whether users can inspect, edit, or reset it.
- **Trust and auditability** — explainability, audit trails, traceability to sources and tools.

---

## Evidence classification

Every finding states **how it was identified and how strongly it is verified**, so you can tell a confirmed defect from a code-level hypothesis. Evidence is **append-only and multi-stage** — a finding can progress `Static Inference → Runtime Observed → Test Verified → Human Verified`, and types may coexist. Each finding carries an **Initial Evidence Level**, a **Current Verification Level**, a **Verification Status** (`Verified` / `Runtime Verification Recommended` / `Manual Verification Required` / `Not Yet Verified`), **Confidence**, and an append-only **Evidence History**. *Evidence Level* says **where the evidence came from**; *Verification Status* says **whether it's sufficient to confirm**. A static-only finding uses cautious language and is **never** presented as confirmed runtime fact. Full field definitions: [WORKFLOW.md](WORKFLOW.md) and [OUTPUT_TEMPLATE.md](OUTPUT_TEMPLATE.md).

## Audit profiles

Pick a profile at intake — **MVP**, **Production**, or **High-Risk / Regulated** — and it tunes severity, recommendation depth, and timing so the audit neither over- nor under-engineers. **Default: Production** (if the stage is unclear the skill asks; if unanswered it uses Production and states the assumption). Findings also carry **Risk if Unfixed**, **Implementation Effort**, and **Recommended Timing**, prioritized by probability, user impact, product stage, data-integrity risk, and recovery difficulty — not theoretical possibility alone. Profile details: [SKILL.md](SKILL.md) / [WORKFLOW.md](WORKFLOW.md).

## Runtime-aware validation & limitations

**Static code inspection is the minimum capability, not the maximum.** The skill uses the strongest validation the current coding-agent environment allows — inspect code, run validation commands, start a dev server, use existing browser/E2E tooling — and clearly distinguishes inference from verified behavior when runtime validation is unavailable. It **never** claims a finding is runtime-verified unless runtime validation was actually performed.

It does **not** ship a built-in browser runner, E2E framework, or usability-testing harness, and it **complements, rather than replaces,** runtime E2E testing, accessibility validation, visual QA, and human usability review.

**Mode D** validates one named flow with the strongest available runtime evidence; **Mode E** statically analyzes one named flow in a large repo without runtime access (inspecting only relevant files to cut context overload and false positives). Both stop after their report unless you ask to enter Mode B. Procedures and report schemas: [WORKFLOW.md](WORKFLOW.md); scaffolds: [templates/audit/targeted-runtime-flow-report.md](templates/audit/targeted-runtime-flow-report.md) and [templates/audit/scoped-static-flow-report.md](templates/audit/scoped-static-flow-report.md).

---

## Contributing

Contributions are welcome — improvements to the checklist, example findings, and missing audit dimensions especially. See [CONTRIBUTING.md](CONTRIBUTING.md).

---

## License

Released under the [MIT License](LICENSE).
