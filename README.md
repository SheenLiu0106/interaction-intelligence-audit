# Interaction Intelligence Audit for Claude Code

A reusable Claude Code skill for systematic interaction-design, AI-experience, and product-readiness audits.

**Interaction Intelligence Audit** is a [Claude Code](https://claude.com/claude-code) skill that performs a comprehensive **interaction-design, product-readiness, and release audit** of web apps, SaaS products, AI products, workflow builders, dashboards, and mobile products.

It systematically hunts for the things that make users fail, the product logic break, or the release slip — then returns a severity-ordered, evidence-based report you can take straight into sprint planning.

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

## Installation

Install the skill globally by cloning it into your Claude Code skills directory:

```bash
git clone https://github.com/SheenLiu0106/interaction-intelligence-audit.git \
  ~/.claude/skills/interaction-intelligence-audit
```

If the `~/.claude/skills/` directory did not previously exist, **restart Claude Code** after this first install so it picks up the new skill. On subsequent updates a restart is not required.

To update later:

```bash
cd ~/.claude/skills/interaction-intelligence-audit
git pull
```

---

## Usage

Invoke the skill with `/interaction-intelligence-audit` and describe what to audit:

```text
/interaction-intelligence-audit

Audit the current project.
Start with the primary user journey.
Do not modify code yet.
Produce an evidence-based report ranked by severity.
```

You can also scope it narrowly to a single flow:

```text
/interaction-intelligence-audit

Audit the onboarding flow for first-time users.
```

Or focus it on AI-native behavior:

```text
/interaction-intelligence-audit

Audit the AI workflow builder.
Focus on agent handoffs, failure recovery, human override, and auditability.
```

The skill works from a described surface, a URL, screenshots/design frames, or a step-by-step flow. See [PROMPT.md](PROMPT.md) for the full fill-in template and audit personas.

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
