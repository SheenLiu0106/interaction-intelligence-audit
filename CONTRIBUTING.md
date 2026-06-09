# Contributing

Thanks for helping improve the Interaction Intelligence Audit skill. This project is a **coding-agent-agnostic, Markdown-only framework**, so most contributions are edits to the checklist, examples, templates, or the remediation workflow.

## Ways to contribute

### Propose checklist improvements
The audit framework lives in [CHECKLIST.md](CHECKLIST.md): a **logic-first** structure with Tier 1 (seven primary interaction-logic domains over sections `01`–`23`) and Tier 2 (conditional/secondary sections gated by material impact). To refine a check, phrase it as something to **look for and verify** (a behavioral failure mode), not just a noun. Keep checks generic enough to apply across product types, and keep visual concerns conditional — only relevant when they affect functionality, accessibility, comprehension, data integrity, or workflow safety.

### Improve example findings
Worked examples live in [EXAMPLES.md](EXAMPLES.md). Good findings are specific, name a concrete state/flow/component/screen, and follow the `Issue / Severity / Impact / Recommendation` shape. Examples are illustrative and fabricated — do not describe a real product.

### Report a missing UX audit dimension
If the framework is missing a category or check, open an issue describing the gap: what failure mode is uncovered, which product types it affects, and a proposed check or category.

### Improve the remediation workflow or templates
The Mode B (persistent remediation) and Mode C (regression re-audit) lifecycles live in [WORKFLOW.md](WORKFLOW.md) and the reusable scaffolds in [templates/](templates/). Keep both **agent-agnostic** — generic repository operations only, no vendor-specific tool, command, or feature. Template files ship blank (placeholders only); never commit a filled-in runtime `bug.md`/`changelog.md` into this repo.

## Submitting a pull request

1. Fork the repository and create a branch.
2. Make your change, keeping the existing tone and structure.
3. Verify cross-file references (e.g. `Checklist 04 Page States`) stay consistent.
4. Open a pull request with a clear description of what changed and why.

## Guidelines

- **Keep the scope logic-first.** Do not expand the project into a visual-design auditor, E2E framework, or full browser-testing platform. Contributions should strengthen:
  - interaction-logic auditing
  - evidence quality
  - runtime-aware validation
  - scoped flow analysis
  - remediation safety
  - profile-sensitive prioritization
- **Keep it generic.** This framework is meant to work across web apps, SaaS, AI products, workflow builders, dashboards, and mobile. Avoid tying checks or examples to one specific product or company.
- **Stay agent-agnostic.** The skill must run on any repository-capable coding agent. Don't introduce a dependency on a vendor-specific tool, slash command, memory system, or planning mode.
- **No private or sensitive content.** Do not add proprietary, internal, confidential, or personally identifying information, secrets, tokens, or private paths.
- **Audit, not redesign.** The skill diagnoses and recommends; it is not a visual-style critique or a redesign tool. Keep contributions aligned with that scope.
