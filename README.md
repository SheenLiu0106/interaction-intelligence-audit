# Interaction Intelligence Audit

A reusable, **coding-agent-agnostic** skill that performs a comprehensive **interaction-design, product-readiness, and release audit** of web apps, SaaS products, AI products, workflow builders, dashboards, and mobile products — then returns a severity-ordered, evidence-based report you can take straight into sprint planning.

It runs on any repository-capable coding agent — [Claude Code](https://claude.com/claude-code), OpenAI Codex, Cursor, Windsurf, Gemini CLI, the GitHub Copilot coding agent, and equivalent tools — because it relies only on generic repository operations and a set of Markdown instructions. It systematically hunts for the things that make users fail, the product logic break, or the release slip.

> This is **not** a visual-style critique tool. It does not score color, typography, or brand taste. Its subject is interaction quality, product logic, missing states, edge cases, failure recovery, accessibility, analytics, performance, and release readiness.

---

## What it audits

The skill walks a 21-category framework, from entry conditions through release readiness and AI experience:

1. **Preconditions** — auth, account, user-type, network, and backend states before the "real" UI matters.
2. **User flows** — happy, success, failure, and interrupted paths; draft recovery, autosave, session restore, resume.
3. **Information architecture** — navigation, discoverability, hierarchy, mental-model alignment.
4. **Page states** — default, empty, loading, and error states for every meaningful screen.
5. **Data states** — empty/missing/long/short data, large numbers, decimals, timezones, refresh, pagination, cache.
6. **Forms** — validation, error messaging, submission states, duplicate-submission prevention.
7. **Components** — hover, focus, selected, disabled, and loading states for interactive elements.
8. **Destructive actions** — delete, cancel, logout, payment; confirmation, undo, recovery.
9. **Feedback** — warnings, loading, progress, success, failure, partial success.
10. **Motion** — transitions, gestures, animation consistency, reduced-motion.
11. **Error handling** — network, backend, authentication, and data-conflict failures.
12. **Permissions** — in-context requests, denial flows, re-enable guidance.
13. **Accessibility** — contrast, keyboard, screen reader, alt text, semantic structure.
14. **Responsive design** — mobile, tablet, desktop, ultrawide, dark mode, landscape.
15. **Internationalization** — language expansion, currency, dates, RTL.
16. **Analytics** — exposure, click, conversion, and funnel instrumentation.
17. **Performance** — first load, navigation speed, image loading, smoothness, interaction latency.
18. **Versioning** — forced/optional updates, rollback, data compatibility.
19. **Security & privacy** — minimal requests, disclosures, data protection, abuse prevention (UX-facing).
20. **Release readiness** — design fidelity, functional completeness, analytics readiness, rollback readiness.
21. **AI experience** — transparency, confidence/evidence, failure recovery, human override, multi-agent coordination, memory/context, trust and auditability.

See [CHECKLIST.md](CHECKLIST.md) for the full, actionable framework.

---

## Two modes

- **Mode A — One-shot Audit** *(default)*: walk the framework and return a single severity-ordered report ([OUTPUT_TEMPLATE.md](OUTPUT_TEMPLATE.md)). Nothing is written to the target unless you ask.
- **Mode B — Persistent Remediation**: audit *and* fix issues **one at a time, under human review**, keeping an append-only history (`bug.md` / `changelog.md` / decision log) **inside the target repository**. Fix one → stop → await explicit human review → continue. See [WORKFLOW.md](WORKFLOW.md); scaffolds live in [templates/](templates/).

In Mode B, **all runtime records are created inside the product repository being audited — never inside this skill repository.**

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

Describe what to audit and which mode you want. The skill works from a described surface, a URL, screenshots/design frames, or a step-by-step flow.

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
Scaffold bug.md / changelog.md in THIS repo from templates/, record findings,
then fix only the highest-priority issue, stop, and wait for my review.
```

See [PROMPT.md](PROMPT.md) for the full fill-in templates and audit personas.

---

## Output format

The skill returns a single structured report (see [OUTPUT_TEMPLATE.md](OUTPUT_TEMPLATE.md)). Findings are grouped by severity, highest-risk first:

```text
Critical
High
Medium
Low
```

Each finding follows an `Issue / Severity / Impact / Recommendation` shape and names a concrete state, flow, component, or screen. The report also includes:

- an **Executive Summary** with an overall score (`X/100`) and top risks,
- **Category scores** mapped to the framework, and
- a **Recommended Next Sprint** (Priority 1 / 2 / 3).

See [EXAMPLES.md](EXAMPLES.md) for worked example findings across five product types.

---

## AI-native product coverage

When the target is an AI or agent product, the skill treats its dedicated **AI Experience** section as primary scope. It covers:

- **Model transparency** — what the AI is doing, and whether output was generated, retrieved, inferred, or entered.
- **Confidence and evidence** — uncertainty signals, citations, verifiable sources.
- **Failure recovery** — retry/regenerate/edit, distinguishing model vs. tool vs. data failures, protecting work.
- **Human override** — editing, rejecting, approving, and intervening before irreversible actions.
- **Multi-agent coordination** — agent roles, handoffs, task status and ownership.
- **AI memory and context** — what context is in use, and whether users can inspect, edit, or reset it.
- **Trust and auditability** — explainability, audit trails, traceability to sources and tools.

---

## Contributing

Contributions are welcome — improvements to the checklist, example findings, and missing audit dimensions especially. See [CONTRIBUTING.md](CONTRIBUTING.md).

---

## License

Released under the [MIT License](LICENSE).
