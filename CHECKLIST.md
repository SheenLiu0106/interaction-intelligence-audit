# Interaction Intelligence Audit — Checklist

The audit framework. It is **logic-first**: it audits *how a product behaves* — interaction
logic, user-flow integrity, product-state handling, action safety, permissions, AI-native
behavior, cross-page consistency, and regression/release readiness. **This skill focuses on how
a product behaves, not how it looks.**

Every item is phrased as a **check** — something to look for and verify, not just a noun. For
each item, ask: *does this exist, is it correct, and does it help the user succeed safely?*
Record concrete evidence (what you saw, where). Skip a section only if it is genuinely not
applicable, and say so.

Prioritize the **highest-risk behavioral failures first** — a workflow that loses user work, a
destructive action with no confirmation, or a missing error state on the core flow far outranks
any visual nit.

The framework has two tiers:

- **Tier 1 — Primary Logic Audit** (Domains 1–7, sections `01`–`23`). Always in scope. Audit
  these first.
- **Tier 2 — Conditional / Secondary** (sections `10`, `13`, `14`, `15`, `16`, `17`, `19`).
  Evaluate **only** when an issue creates a functional, accessibility, comprehension, data
  integrity, or workflow-safety impact. Do not report these as findings on aesthetic grounds
  alone.

---

## Logic-first domain crosswalk (Tier 1)

The seven primary domains organize the detailed numbered sections:

| Domain | Sections |
|---|---|
| 1 — User-Flow Integrity | `02` User Flows, `03` Information Architecture |
| 2 — Product-State Logic | `01` Preconditions, `04` Page States, `05` Data States, `09` Feedback, `11` Error Handling |
| 3 — Action Safety & Data Integrity | `06` Forms, `07` Components, `08` Destructive Actions |
| 4 — Cross-Page & Cross-Component Consistency | `22` Consistency |
| 5 — Permissions & Access Control | `12` Permissions & Access Control |
| 6 — AI-Native Interaction Logic | `21` AI Experience |
| 7 — Regression & Release Readiness | `18` Versioning, `20` Release Readiness, `23` Regression & Release Readiness Re-Audit |

---

# Tier 1 — Primary Logic Audit

## Domain 1 — User-Flow Integrity

Audit whether users can successfully **complete important tasks**, end to end, as every relevant
user type. Walk the actual journeys, not just screens.

### 02 User Flows

- **Happy path:** Can a user complete the core task with no surprises? Is each step's purpose and next action obvious?
- **Success path:** When the task completes, is success clearly confirmed *and* does it lead somewhere (next action / result), not a dead end?
- **Failure path:** When a step fails, is the failure explained, attributable, and recoverable in place — not a restart-from-scratch?
- **Interrupted path:** If the user navigates away, refreshes, loses connection, or is logged out mid-flow, what happens to their progress?

#### Flow-integrity checks

- **Dead ends:** Does every screen offer a clear next action or exit — no states the user can enter but not leave?
- **Unclear next actions:** Is the primary next step always obvious, or does the user have to guess what to do next?
- **Unnecessary steps:** Are there redundant steps, confirmations, or screens that add friction without protecting the user?
- **Back-navigation:** Can the user reliably go back without losing data or breaking the flow? Is back-navigation present where expected?
- **Invalid transitions:** Are impossible/illegal state transitions blocked (e.g. submitting an already-submitted form, advancing past an incomplete step)?
- **Unclear completion states:** Does the user always know whether a task is done, in progress, or failed?
- **Inconsistent entry/exit points:** Do the same task's entry and exit points behave consistently wherever they appear?
- **Workflow loops:** Are there flows that send the user in a circle with no progress or escape?
- **Abandonment risk:** Are there steps where users predictably drop off (too long, unclear value, blocked on missing input) without a recovery or save path?

#### Continuity checks

- **Draft recovery:** Is unsaved work recoverable after a crash, refresh, or accidental navigation?
- **Autosave:** Is in-progress work saved automatically, and is save state visible (saving / saved / failed)?
- **Session restore:** After re-login or app reopen, is the user returned to a sensible place with state intact?
- **Resume workflow:** For multi-step or long-running tasks, can the user leave and resume without losing position or context?

### 03 Information Architecture

- **Navigation:** Is primary navigation consistent, predictable, and always showing where the user is? Can they always get back/home?
- **Discoverability:** Can users find key features without prior knowledge? Are important actions visible rather than buried in menus/overflow?
- **Information hierarchy:** Does the layout reflect actual importance — primary action prominent, secondary actions subordinate, noise minimized?
- **Mental model alignment:** Do labels, groupings, and behaviors match how users think about the domain? Are there terms or structures that will surprise them?

---

## Domain 2 — Product-State Logic

Audit **state transitions and state visibility**: every state the product can be in, whether it
is reachable, communicated, and recoverable, and whether state stays consistent over time and
across surfaces.

### 01 Preconditions

The entry conditions the product must handle before the "real" UI even matters.

#### Auth state
- **Logged in:** Does the authenticated experience load cleanly and land the user somewhere useful?
- **Logged out:** Is there a clear, non-dead-end path to sign in or sign up? Are protected screens gated (not flashing private data first)?
- **Guest mode:** If guests are allowed, is it obvious what they can/can't do and how to upgrade to an account? If not allowed, are they redirected cleanly?

#### Account states
- **Active:** Full experience works as expected.
- **Suspended:** Is the user told *why*, *what they can still do*, and *how to resolve it* — not just locked out silently?
- **Frozen / read-only:** Are write actions clearly disabled (not just failing on submit) with an explanation?
- **Permission-restricted:** Do restricted users see a coherent reduced experience (hidden or disabled, with reason) rather than broken/empty screens or errors?

#### User types
- **New user:** Is there a first-run / empty-state experience that teaches and gives a first action? No "blank product" on day one.
- **Returning user:** Does the product restore context (last view, in-progress work) rather than dumping them at a generic home?
- **Premium / paid user:** Are paid capabilities visibly available; are upgrade prompts absent where already entitled?

#### Network
- **Online:** Baseline.
- **Slow network:** Are there loading/skeleton states and no indefinite spinners? Does the UI stay responsive while data trickles in?
- **Offline:** Is offline detected and communicated? Is queued/cached behavior sensible, and is the user prevented from losing work?

#### Backend
- **Healthy:** Baseline.
- **Maintenance mode:** Is there a graceful maintenance state with expectation-setting, not a raw error?
- **Service unavailable (5xx/timeouts):** Is there a clear failure state with retry, rather than a blank page, infinite spinner, or cryptic error?

### 04 Page States

For every meaningful screen, verify all the meaningful states exist and are intentional.

- **Default:** The normal, populated state — correct and complete.
- **Empty:** Is there a purposeful empty state (explanation + primary CTA), not a blank screen or a lone "no data"?
- **Loading:** Is there a skeleton/placeholder/spinner with no layout shift, and a sane timeout/failure fallback?
- **Error:** Is there a specific error state (what went wrong + how to recover/retry), not a generic crash or silent blank?
- **Success:** Is a successful outcome clearly represented as a state (not just a transient toast that can be missed)?
- **Partial success:** When some work succeeds and some fails, is the mixed state shown explicitly rather than reported as all-or-nothing?
- **Disabled:** Are unavailable states shown as intentionally disabled (with reason), not broken or missing?
- **Optimistic updates & rollback:** If the UI updates optimistically before the server confirms, does it roll back clearly and inform the user when the operation actually fails?
- **State synchronization across pages:** When an object's state changes, do all pages/components showing that object reflect the new truth (no stale duplicates across views)?
- **Refresh recovery:** After a browser/app refresh, is the relevant state restored or recoverable rather than silently reset?
- **Session recovery:** After a session expiry/re-auth, is the user returned to the same state with work intact?

### 05 Data States

The product must survive real, messy data.

- **Empty data:** Zero items handled gracefully with guidance.
- **Missing fields:** Null/absent fields render cleanly (no "undefined", "null", or broken layout).
- **Missing images:** Broken/absent images have fallbacks (placeholder/initials), not broken icons or collapsed layout.
- **Long text:** Long names/titles/descriptions truncate or wrap predictably without breaking layout; full value is recoverable (tooltip/expand).
- **Short text:** Minimal content doesn't leave awkward gaps or imply something is broken.
- **Large numbers:** Big values format readably (grouping, abbreviation like 1.2M) and don't overflow containers.
- **Decimals:** Precision is consistent and appropriate; no floating-point noise (e.g. `0.30000000004`).
- **Timezones:** Dates/times are unambiguous about timezone; relative times ("2h ago") are correct and refresh sensibly.
- **Refresh logic:** Is data kept fresh, and is **stale data** indicated? Is there manual refresh where auto-refresh isn't enough?
- **Pagination:** Is paging clear, with current position, total context, and no lost scroll/selection on page change?
- **Infinite scroll:** Does it load reliably, indicate loading/end, preserve position on back-navigation, and offer a way to reach the footer?
- **Cache invalidation:** After a mutation, does the UI reflect the new truth (no stale cached values, no need to manually refresh)?

### 09 Feedback

Every user action should produce perceptible, proportionate feedback.

- **Warnings:** Risky states/actions are flagged *before* the consequence.
- **Loading:** Async work is acknowledged immediately (no "did my click register?").
- **Progress:** Long/multi-step operations show real progress, not an indefinite spinner.
- **Success:** Completed actions are confirmed (toast/inline/state change) — the user is never left guessing.
- **Failure:** Failures are surfaced, explained, and recoverable — never silent.
- **Partial success:** When some items succeed and some fail (bulk actions, multi-step), is the mixed result clearly itemized with next steps?

### 11 Error Handling

- **Network failures:** Detected, explained, and retryable; work is not lost on a dropped request.
- **Backend failures:** 5xx/timeouts produce a clear state with retry and, ideally, a support/error reference — not a raw stack trace or blank screen.
- **Authentication failures:** Expired/invalid sessions are handled gracefully (re-auth in place, preserve intended destination), not an abrupt dump to a generic error.
- **Data conflicts:** Concurrent edits / version conflicts are detected and the user is given a clear resolution path (merge / overwrite / reload), not a silent overwrite or data loss.
- **Retry states:** Is a failed operation retryable in place, with the prior input preserved and clear indication of what is being retried?

---

## Domain 3 — Action Safety & Data Integrity

Audit whether user actions are **safe and reversible where appropriate**, and whether the product
protects the user's data and work.

### 06 Forms

- **Validation:** Is validation inline and timely (on blur/submit, not punishing keystrokes)? Are requirements stated up front?
- **Validation timing:** Does validation appear early enough to prevent wasted effort — not only *after* submit, when the user has already invested the work?
- **Missing validation:** Are inputs that need validation actually validated (required fields, formats, ranges), rather than accepted silently and failing downstream?
- **Error messaging:** Are errors specific, human, and adjacent to the offending field — saying how to fix, not just "invalid"?
- **Submission states:** Is the submit button disabled/loading during submission with clear progress, then a clear success/failure result?
- **Duplicate submission prevention:** Are double-clicks / double-submits prevented (debounce, disable, idempotency) so the user can't create duplicates or double-charge?
- **Save failures:** When a save fails, is the user clearly told, and is their input preserved so it isn't lost?
- **Unsaved-change risk:** Is the user warned before navigating away from or closing unsaved work?

### 07 Components

For interactive components, verify each state is present, distinct, and correct.

- **Hover:** Affordance is clear that an element is interactive (where pointers exist).
- **Focus:** Visible focus indicator on keyboard navigation — never suppressed.
- **Selected / active:** Current selection/active state is unambiguous.
- **Disabled:** Disabled state is visually distinct *and* explains why (or is obviously contextually disabled), not just a dead control.
- **Loading:** Per-component loading (button spinner, inline skeleton) where the component triggers async work.

### 08 Destructive Actions

- **Delete:** Is deletion confirmed (especially for high-value/irreversible data), and is the consequence/scope spelled out?
- **Accidental deletion:** Is it hard to delete by accident (no destructive action on a misclick, no destructive default button)?
- **Cancel:** Does "cancel" reliably discard without side effects, and is discarding unsaved work itself confirmed?
- **Logout:** Is logout intentional (no accidental trigger), and does it warn about unsaved work?
- **Payment:** Are charges, amounts, and recurrence shown before commit? Is accidental/duplicate payment prevented?
- **Silent mutations:** Does any action change or delete data without the user clearly intending or being informed of it?
- **Irreversible-action clarity:** Are irreversible actions clearly marked as permanent *before* the user commits?
- **Race conditions from repeated actions:** Do rapid/repeated clicks or concurrent triggers avoid creating duplicate, conflicting, or corrupt results?

#### Safety nets

- **Confirmation:** Right-sized friction — confirm the dangerous, don't nag on the trivial. Confirmation rules are consistent across equivalent actions.
- **Undo:** Is there an undo / grace window for reversible-feeling actions (e.g. "Deleted — Undo")?
- **Recovery:** For truly destructive actions, is there a recovery path (trash, soft-delete, restore) or is the user clearly warned it's permanent?

---

## Domain 4 — Cross-Page & Cross-Component Consistency

Audit whether **equivalent actions behave consistently** everywhere they appear. Inconsistency is
a behavioral defect: it breaks the user's learned model and causes wrong-action errors.

### 22 Cross-Page & Cross-Component Consistency

- **Button outcomes:** Do buttons with the same label/role produce the same outcome across pages?
- **Form validation:** Are the same fields validated by the same rules and messages wherever they appear?
- **Save behavior:** Does saving behave the same way (autosave vs explicit, confirmation, feedback) across equivalent surfaces?
- **Retry behavior:** Is failure-and-retry handled consistently across flows, not retryable in one place and dead-ends in another?
- **Navigation:** Do equivalent navigation controls (back, breadcrumbs, close) behave the same everywhere?
- **Permission handling:** Are access constraints applied and communicated consistently for the same capability across pages?
- **Object status:** Is an object's status (e.g. "draft", "archived", "failed") shown consistently across every view of that object?
- **Duplicated logic:** Do duplicated implementations of the same behavior produce the same result, or can they conflict / drift?
- **Loading & error behavior:** Do equivalent components show loading and error states the same way, not skeleton here and spinner-then-blank there?
- **Modal & confirmation rules:** Are modal, dialog, and confirmation conventions consistent (when they appear, how they're dismissed, what they protect)?

---

## Domain 5 — Permissions & Access Control

Audit whether the product **communicates access constraints correctly** — both OS/browser
permissions and in-app authorization — and recovers gracefully when access is denied.

### 12 Permissions & Access Control

#### OS / browser permissions

For each OS/browser permission the product requests:

- **Camera / Microphone / GPS / Notifications:** Is the request made *in context* with a rationale (priming), not on first load with no explanation?

##### Denial flows (the part usually missed)
- Is **denial** handled gracefully — the feature degrades with an explanation, not a broken or stuck UI?
- Is there guidance to **re-enable** a permission the user previously denied (since the browser/OS won't re-prompt)?
- Does the product avoid **repeatedly** prompting and respect the user's choice?

#### Application access control / authorization

- **Actionable-but-inaccessible:** Do actions the user can't perform appear disabled with a reason, rather than looking actionable and then failing?
- **Missing permission explanations:** When access is restricted, is the user told *why* and *what they'd need* — not just blocked?
- **Authorization failure recovery:** When an authorization check fails, is there a clear next step (request access, switch account, contact admin) rather than a dead error?
- **Inconsistent role behavior:** Do the same role's permissions behave consistently across pages and actions?
- **Hidden state changes from permissions:** Are permission-driven changes to what the user sees/can do made visible, not silent?
- **Unsafe fallbacks:** Does a permission/authorization failure ever fail *open* (granting more than intended) or strand the user in a broken state?
- **Inaccessible content with no path forward:** When content is gated, is there a clear next step rather than a blank or generic "forbidden"?

---

## Domain 6 — AI-Native Interaction Logic

A **first-class audit category.** Review AI-native product behavior, trust, explainability,
recovery, and user control. Treat as **primary scope** when the target is an AI/agent product.

### 21 AI Experience

#### Model Transparency
- Is the AI behavior understandable and reasonably predictable?
- Can users tell what the AI is doing at each stage (clear **AI processing states**)?
- Is there **progress feedback** during AI work, not an indefinite "thinking" with no signal?
- Is it clear whether a result was generated, retrieved, inferred, or manually entered?
- Are tool calls, agent actions, and system decisions visible when appropriate (no **non-transparent agent actions**)?

#### Confidence and Evidence
- Are confidence levels communicated when uncertainty matters?
- Are citations, supporting evidence, or source references (**provenance**) available when needed?
- Can users distinguish **verified information from generated suggestions**?
- Are unsupported claims, ambiguous outputs, or hallucination-sensitive steps clearly surfaced?

#### Failure Recovery
- Is there a clear recovery path when the AI output is incorrect, incomplete, or irrelevant?
- Can users retry, regenerate, edit, or refine the request?
- Are model failures, tool failures, and data failures distinguished clearly (**weak failure recovery** is a finding)?
- Is the user protected from losing work after a failed generation?

#### Human Override
- Can users manually edit, reject, approve, or replace AI-generated output?
- Is there a manual fallback when automation fails (a **human override** path always exists)?
- Can users intervene before irreversible actions, and is **unsafe auto-apply** behavior avoided (no high-impact action applied without a review/confirm step)?
- Are approval gates present for high-risk operations?

#### Multi-Agent Coordination
- Are agent roles and responsibilities clear?
- Can users understand which agent is currently acting?
- Are agent handoffs understandable, and are **multi-agent coordination failures** surfaced rather than silent?
- Are task status, ownership, and dependencies visible?
- Is there an **escalation path** when an agent is blocked, looping, or out of its depth?
- Are parallel tasks and blocked tasks represented clearly?

#### AI Memory and Context
- Is session memory visible or understandable?
- Can users tell what context is currently being used (no silent **context loss**)?
- Can users inspect, edit, remove, or reset context when appropriate?
- Is state persistence reliable across sessions, and are **memory overwrite risks** (newer context silently destroying older context) avoided?
- Are **stale or conflicting memories** handled clearly?

#### AI Trust and Auditability
- Can users understand why an output was produced?
- Is there an audit trail for important actions (no **auditability gaps**)?
- Are generated outputs traceable to sources, tools, agents, and user inputs?
- Are irreversible or high-impact AI actions reviewable before execution?

---

## Domain 7 — Regression & Release Readiness

Audit whether the product is **safe to ship**, whether previously fixed issues or approved
decisions have **regressed**, and whether changes remain compatible across related flows and
versions.

### 18 Versioning

- **Forced updates:** When a breaking update ships, are users prompted/forced gracefully with an explanation, not silently broken?
- **Optional updates:** Are non-breaking updates offered without disrupting in-progress work?
- **Rollback strategy:** If a release is bad, can it be rolled back without corrupting user data or stranding clients on an incompatible version?
- **Data compatibility:** Do older clients and newer servers (and vice versa) interoperate, and are stored documents/drafts forward/backward compatible?

### 20 Release Readiness

The go/no-go view.

- **Behavioral fidelity:** Does the built product match the intended *behavior* (states, flows, interactions, recovery) — not just static layout?
- **Functional completeness:** Are all states and paths from the primary domains actually implemented, including the unglamorous error/empty/edge/partial ones?
- **Analytics readiness:** Is the instrumentation from Section 16 in place and verified *before* launch (not bolted on after)?
- **Rollback readiness:** Is there a tested rollback/kill-switch path and data-compatibility confidence (Section 18) if the release goes wrong?
- **Release-blocking interaction risks:** Are there open Critical/High interaction findings that should block ship?

### 23 Regression & Release Readiness Re-Audit

Compare current behavior against historical records (used directly by **Mode C — Regression
Re-Audit**; see [`WORKFLOW.md`](WORKFLOW.md)).

- **Reopened workflow failures:** Have previously working flows broken again?
- **Previously closed bugs reappearing:** Do any `Closed`/`Approved` issues now reproduce?
- **Violated approved decisions:** Are previously approved interaction decisions no longer respected?
- **Incompatible cross-flow changes:** Has a change in one flow broken a related flow?
- **Missing validation coverage:** Are there changes that lack validation where prior fixes added it?
- **Migration-related state failures:** Do data/schema migrations leave state inconsistent, stranded, or unrecoverable?
- **Version compatibility:** Do mixed client/server versions or upgraded data still interoperate (cross-reference Section 18)?

> When re-auditing under Mode C, log each regression as a **new** bug entry typed
> `Type: Regression` with `Related Bug: BUG-XXX` referencing the original — never rewrite the
> closed entry.

---

# Tier 2 — Conditional / Secondary

> **Gating rule for every section below:** Evaluate only when the issue affects task completion,
> comprehension, accessibility, data integrity, or workflow safety. Do not report these as
> findings on aesthetic or stylistic grounds alone. When such an issue *does* create a material
> impact, describe the impact — not the aesthetic.

## 10 Motion

> Evaluate only when the issue affects task completion, comprehension, accessibility, data integrity, or workflow safety.

- **Page transitions:** Are transitions purposeful and quick, aiding orientation rather than adding delay or hiding state changes?
- **Component transitions:** Do open/close/expand/collapse animations clarify state change without jank or obscuring what changed?
- **Gestures:** (Touch) Are gestures discoverable, with non-gesture fallbacks for the same action so the task stays completable?
- **Reduced motion:** Is motion reduced when the user prefers reduced motion (an accessibility/comprehension requirement)?

## 13 Accessibility

> Evaluate only when the issue affects task completion, comprehension, accessibility, data integrity, or workflow safety. (For genuine accessibility barriers, that impact is inherent — log them.)

(One of the framework's categories — not a formal WCAG certification, but the high-impact basics that determine whether users can actually operate the product.)

- **Contrast:** Do text and essential UI meet sufficient contrast against their backgrounds — including disabled/placeholder/secondary states — so actions remain identifiable?
- **Keyboard support:** Can the entire core flow be completed with keyboard only? Logical tab order, visible focus, no traps, accessible custom controls?
- **Screen reader support:** Are controls, images, and dynamic updates announced meaningfully (labels, roles, live regions)? Are icon-only buttons labeled?
- **Alt text:** Do meaningful images have alt text and decorative ones are hidden from AT?
- **Semantic structure:** Is the markup semantic (headings, landmarks, lists, buttons-vs-links) so AT and keyboard work naturally — not a wall of generic divs?

## 14 Responsive Design

> Evaluate only when the issue affects task completion, comprehension, accessibility, data integrity, or workflow safety.

- **Mobile:** Tap targets sized, no horizontal scroll, content reflows, nothing critical clipped or unreachable.
- **Tablet:** Sensible use of the mid-size canvas (not just stretched mobile or cramped desktop) so the task stays completable.
- **Desktop:** Primary target — no overflowing or unusable space that blocks actions.
- **Ultrawide:** Content has max-width / sensible behavior; doesn't stretch into unreadable line lengths that harm comprehension.
- **Dark mode:** If supported, every surface is themed so nothing becomes invisible or illegible (broken contrast, invisible borders).
- **Landscape mode:** (Mobile/tablet) Layout adapts; nothing critical is cut off or unreachable below the fold.

## 15 Internationalization

> Evaluate only when the issue affects task completion, comprehension, accessibility, data integrity, or workflow safety.

- **Language expansion:** Does the layout survive longer translated strings (German/Finnish) without clipping/overflowing controls so actions stay usable?
- **Currency formatting:** Are amounts formatted per locale (symbol position, grouping, decimals) and is the currency unambiguous (data-integrity/comprehension risk)?
- **Date formatting:** Are dates locale-aware (no ambiguous `03/04/05`) and timezone-correct?
- **RTL support:** If RTL locales are in scope, does the layout mirror correctly (direction, alignment, icons) rather than breaking the flow?

## 16 Analytics

> Evaluate only when the issue affects task completion, comprehension, accessibility, data integrity, or workflow safety. (Treat missing instrumentation as a release-readiness gap, not a visual issue.)

You can't improve what you can't measure — verify the flow is instrumented.

- **Exposure events:** Are key screens/components firing impression/exposure events so you know what users actually see?
- **Click events:** Are primary and secondary actions tracked with enough context to be useful?
- **Conversion events:** Are the moments that matter (signup, activation, purchase, task completion) tracked explicitly?
- **Funnel tracking:** Can you reconstruct the full funnel and identify the exact step where users drop off?

## 17 Performance

> Evaluate only when the issue affects task completion, comprehension, accessibility, data integrity, or workflow safety.

- **First load:** Is initial load acceptably fast with perceived-performance techniques (skeletons, progressive render), not a long blank/spinner that reads as broken?
- **Navigation speed:** Are in-app transitions snappy (cached/prefetched where sensible), not a full reload each time?
- **Image loading:** Are images sized/compressed/lazy-loaded, with reserved space to prevent layout shift that causes misclicks?
- **FPS / smoothness:** Do scroll, animation, and drag stay smooth (no jank) on realistic data volumes and mid-tier devices?
- **Interaction latency:** Do interactions respond immediately (optimistic UI / instant acknowledgment), so users don't retry and double-trigger?

## 19 Security & Privacy

> Evaluate only when the issue affects task completion, comprehension, accessibility, data integrity, or workflow safety. (UX-facing, not a full security audit.)

- **Permission requests:** Are data/permission requests minimal, in-context, and justified — not over-asking?
- **Privacy disclosures:** Is it clear what data is collected/used (especially for AI features that may use user content), with accessible controls?
- **Data protection:** Is sensitive data masked/handled appropriately in the UI (no secrets in plaintext, no PII in URLs/logs surfaced to the user)?
- **Abuse prevention:** Are there guardrails against abuse/misuse (rate limits surfaced gracefully, destructive/automation actions gated, content limits explained)?

---

# Cross-Cutting Audit Practices (apply to every finding)

These are not new framework sections and they do **not** renumber or replace `01`–`23`. They
apply across all sections and modes.

## Interaction-Observable Visual Blockers

A **narrow** classification for visual conditions that materially **block or distort interaction
behavior**. This is the *only* visual category in scope — it is not a doorway to visual-design
critique.

**Log these (they create functional / accessibility / workflow-safety impact):**

- modal hidden behind another layer
- CTA clipped outside the viewport
- sticky header blocking a required control
- mobile layout preventing task completion
- disabled state visually indistinguishable from enabled state when it creates action risk
- destructive and non-destructive actions visually indistinguishable when it creates deletion risk
- toast or error feedback hidden from view
- focus indicator missing when it prevents keyboard navigation
- overlay preventing interaction
- layout shift causing wrong-action clicks

**Do not log these (no material impact — out of scope):**

- typography preference
- color-palette taste
- decorative spacing polish
- illustration style
- visual-trend alignment
- subjective brand styling

```text
Before logging a visual observation, determine whether it creates a measurable
functional, accessibility, comprehension, data-integrity, or workflow-safety impact.

If no material impact exists, do not log it as a bug.
```

## Evidence quality

- **State your evidence.** Every finding records its **Evidence Level** (`Static Inference` |
  `Runtime Observed` | `Test Verified` | `Human Verified`), **Confidence** (`High` | `Medium` |
  `Low`), **Verification Status** (`Verified` | `Runtime Verification Recommended` |
  `Manual Verification Required` | `Not Yet Verified`), and **Evidence Source** (file/route/
  component/state store/API endpoint/test/observation/screenshot/note).
- **Don't overstate.** Never present a `Static Inference` as a confirmed runtime fact; use cautious
  language (*"may," "appears to," "could," "requires runtime verification"*).

## Runtime verification needs

- **Static inspection is the floor, not the ceiling.** Where the issue depends on runtime behavior
  (timing, async, race conditions, network failure, optimistic rollback, session/refresh recovery,
  AI context loss), prefer the strongest available evidence — run tests, start the dev server,
  exercise the flow (see **Mode D** in [`WORKFLOW.md`](WORKFLOW.md)).
- **Flag what you couldn't run.** When runtime tooling is unavailable, mark the finding
  `Runtime Verification Recommended` or `Manual Verification Required` and give exact reviewer
  steps — never silently assume the runtime behavior.

## Profile-sensitive prioritization

- **Score against the Audit Profile** (`MVP` | `Production` | `High-Risk / Regulated`). The same
  observation can be a Blocker under High-Risk/Regulated and a deferrable Backlog item under MVP.
- **Don't over-engineer.** Do not recommend complex remediation solely because an edge case is
  theoretically possible. Prioritize by probability, user impact, product stage, data-integrity
  risk, and recovery difficulty. Record **Risk if Unfixed**, **Implementation Effort**, and
  **Recommended Timing** on each finding.

---

When the walk is complete, produce the report using [OUTPUT_TEMPLATE.md](OUTPUT_TEMPLATE.md),
leading with the highest-risk behavioral findings. Calibrate against [EXAMPLES.md](EXAMPLES.md).
