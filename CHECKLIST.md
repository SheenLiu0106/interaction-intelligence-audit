# Interaction Intelligence Audit — Checklist

The audit framework. Walk each section in order, `01` → `21`. Every item is phrased as a **check** — something to look for and verify, not just a noun. For each item, ask: *does this exist, is it correct, and does it help the user succeed?* Record concrete evidence (what you saw, where). Skip a section only if it is genuinely not applicable, and say so.

Prioritize the **highest-risk** failures first — a missing error state on the core flow outranks a hover-state nit.

---

# 01 Preconditions

The entry conditions the product must handle before the "real" UI even matters.

### Auth state
- **Logged in:** Does the authenticated experience load cleanly and land the user somewhere useful?
- **Logged out:** Is there a clear, non-dead-end path to sign in or sign up? Are protected screens gated (not flashing private data first)?
- **Guest mode:** If guests are allowed, is it obvious what they can/can't do and how to upgrade to an account? If not allowed, are they redirected cleanly?

### Account states
- **Active:** Full experience works as expected.
- **Suspended:** Is the user told *why*, *what they can still do*, and *how to resolve it* — not just locked out silently?
- **Frozen / read-only:** Are write actions clearly disabled (not just failing on submit) with an explanation?
- **Permission-restricted:** Do restricted users see a coherent reduced experience (hidden or disabled, with reason) rather than broken/empty screens or errors?

### User types
- **New user:** Is there a first-run / empty-state experience that teaches and gives a first action? No "blank product" on day one.
- **Returning user:** Does the product restore context (last view, in-progress work) rather than dumping them at a generic home?
- **Premium / paid user:** Are paid capabilities visibly available; are upgrade prompts absent where already entitled?

### Network
- **Online:** Baseline.
- **Slow network:** Are there loading/skeleton states and no indefinite spinners? Does the UI stay responsive while data trickles in?
- **Offline:** Is offline detected and communicated? Is queued/cached behavior sensible, and is the user prevented from losing work?

### Backend
- **Healthy:** Baseline.
- **Maintenance mode:** Is there a graceful maintenance state with expectation-setting, not a raw error?
- **Service unavailable (5xx/timeouts):** Is there a clear failure state with retry, rather than a blank page, infinite spinner, or cryptic error?

---

# 02 User Flows

Walk the actual journeys, not just screens.

- **Happy path:** Can a user complete the core task with no surprises? Is each step's purpose and next action obvious?
- **Success path:** When the task completes, is success clearly confirmed *and* does it lead somewhere (next action / result), not a dead end?
- **Failure path:** When a step fails, is the failure explained, attributable, and recoverable in place — not a restart-from-scratch?
- **Interrupted path:** If the user navigates away, refreshes, loses connection, or is logged out mid-flow, what happens to their progress?

### Continuity checks
- **Draft recovery:** Is unsaved work recoverable after a crash, refresh, or accidental navigation?
- **Autosave:** Is in-progress work saved automatically, and is save state visible (saving / saved / failed)?
- **Session restore:** After re-login or app reopen, is the user returned to a sensible place with state intact?
- **Resume workflow:** For multi-step or long-running tasks, can the user leave and resume without losing position or context?

---

# 03 Information Architecture

- **Navigation:** Is primary navigation consistent, predictable, and always showing where the user is? Can they always get back/home?
- **Discoverability:** Can users find key features without prior knowledge? Are important actions visible rather than buried in menus/overflow?
- **Information hierarchy:** Does the layout reflect actual importance — primary action prominent, secondary actions subordinate, noise minimized?
- **Mental model alignment:** Do labels, groupings, and behaviors match how users think about the domain? Are there terms or structures that will surprise them?

---

# 04 Page States

For every meaningful screen, verify all four states exist and are intentional.

- **Default:** The normal, populated state — correct and complete.
- **Empty:** Is there a purposeful empty state (explanation + primary CTA), not a blank screen or a lone "no data"?
- **Loading:** Is there a skeleton/placeholder/spinner with no layout shift, and a sane timeout/failure fallback?
- **Error:** Is there a specific error state (what went wrong + how to recover/retry), not a generic crash or silent blank?

---

# 05 Data States

The product must survive real, messy data.

- **Empty data:** Zero items handled gracefully with guidance.
- **Missing fields:** Null/absent fields render cleanly (no "undefined", "null", or broken layout).
- **Missing images:** Broken/absent images have fallbacks (placeholder/initials), not broken icons or collapsed layout.
- **Long text:** Long names/titles/descriptions truncate or wrap predictably without breaking layout; full value is recoverable (tooltip/expand).
- **Short text:** Minimal content doesn't leave awkward gaps or imply something is broken.
- **Large numbers:** Big values format readably (grouping, abbreviation like 1.2M) and don't overflow containers.
- **Decimals:** Precision is consistent and appropriate; no floating-point noise (e.g. `0.30000000004`).
- **Timezones:** Dates/times are unambiguous about timezone; relative times ("2h ago") are correct and refresh sensibly.
- **Refresh logic:** Is data kept fresh, and is stale data indicated? Is there manual refresh where auto-refresh isn't enough?
- **Pagination:** Is paging clear, with current position, total context, and no lost scroll/selection on page change?
- **Infinite scroll:** Does it load reliably, indicate loading/end, preserve position on back-navigation, and offer a way to reach the footer?
- **Cache invalidation:** After a mutation, does the UI reflect the new truth (no stale cached values, no need to manually refresh)?

---

# 06 Forms

- **Validation:** Is validation inline and timely (on blur/submit, not punishing keystrokes)? Are requirements stated up front?
- **Error messaging:** Are errors specific, human, and adjacent to the offending field — saying how to fix, not just "invalid"?
- **Submission states:** Is the submit button disabled/loading during submission with clear progress, then a clear success/failure result?
- **Duplicate submission prevention:** Are double-clicks / double-submits prevented (debounce, disable, idempotency) so the user can't create duplicates or double-charge?

---

# 07 Components

For interactive components, verify each state is present, distinct, and correct.

- **Hover:** Affordance is clear that an element is interactive (where pointers exist).
- **Focus:** Visible focus indicator on keyboard navigation — never suppressed.
- **Selected / active:** Current selection/active state is unambiguous.
- **Disabled:** Disabled state is visually distinct *and* explains why (or is obviously contextually disabled), not just a dead control.
- **Loading:** Per-component loading (button spinner, inline skeleton) where the component triggers async work.

---

# 08 Destructive Actions

- **Delete:** Is deletion confirmed (especially for high-value/irreversible data), and is the consequence/scope spelled out?
- **Cancel:** Does "cancel" reliably discard without side effects, and is discarding unsaved work itself confirmed?
- **Logout:** Is logout intentional (no accidental trigger), and does it warn about unsaved work?
- **Payment:** Are charges, amounts, and recurrence shown before commit? Is accidental/duplicate payment prevented?

### Safety nets
- **Confirmation:** Right-sized friction — confirm the dangerous, don't nag on the trivial.
- **Undo:** Is there an undo / grace window for reversible-feeling actions (e.g. "Deleted — Undo")?
- **Recovery:** For truly destructive actions, is there a recovery path (trash, soft-delete, restore) or is the user clearly warned it's permanent?

---

# 09 Feedback

Every user action should produce perceptible, proportionate feedback.

- **Warnings:** Risky states/actions are flagged *before* the consequence.
- **Loading:** Async work is acknowledged immediately (no "did my click register?").
- **Progress:** Long/multi-step operations show real progress, not an indefinite spinner.
- **Success:** Completed actions are confirmed (toast/inline/state change) — the user is never left guessing.
- **Failure:** Failures are surfaced, explained, and recoverable — never silent.
- **Partial success:** When some items succeed and some fail (bulk actions, multi-step), is the mixed result clearly itemized with next steps?

---

# 10 Motion

- **Page transitions:** Are transitions purposeful and quick, aiding orientation rather than adding delay?
- **Component transitions:** Do open/close/expand/collapse animations clarify state change without jank?
- **Gestures:** (Touch) Are gestures discoverable, with non-gesture fallbacks for the same action?
- **Animation consistency:** Are timing, easing, and direction consistent across the product? Is motion reduced when the user prefers reduced motion?

---

# 11 Error Handling

- **Network failures:** Detected, explained, and retryable; work is not lost on a dropped request.
- **Backend failures:** 5xx/timeouts produce a clear state with retry and, ideally, a support/error reference — not a raw stack trace or blank screen.
- **Authentication failures:** Expired/invalid sessions are handled gracefully (re-auth in place, preserve intended destination), not an abrupt dump to a generic error.
- **Data conflicts:** Concurrent edits / version conflicts are detected and the user is given a clear resolution path (merge / overwrite / reload), not a silent overwrite or data loss.

---

# 12 Permissions

For each OS/browser permission the product requests:

- **Camera / Microphone / GPS / Notifications:** Is the request made *in context* with a rationale (priming), not on first load with no explanation?

### Denial flows (the part usually missed)
- Is **denial** handled gracefully — the feature degrades with an explanation, not a broken or stuck UI?
- Is there guidance to **re-enable** a permission the user previously denied (since the browser/OS won't re-prompt)?
- Does the product avoid **repeatedly** prompting and respect the user's choice?

---

# 13 Accessibility

(One of 21 categories — not a formal WCAG certification, but the high-impact basics.)

- **Contrast:** Do text and essential UI meet sufficient contrast against their backgrounds, including in disabled/placeholder/secondary states?
- **Keyboard support:** Can the entire core flow be completed with keyboard only? Logical tab order, visible focus, no traps, accessible custom controls?
- **Screen reader support:** Are controls, images, and dynamic updates announced meaningfully (labels, roles, live regions)? Are icon-only buttons labeled?
- **Alt text:** Do meaningful images have alt text and decorative ones are hidden from AT?
- **Semantic structure:** Is the markup semantic (headings, landmarks, lists, buttons-vs-links) so AT and keyboard work naturally — not a wall of generic divs?

---

# 14 Responsive Design

Verify layout and interaction hold across:

- **Mobile:** Tap targets sized, no horizontal scroll, content reflows, nothing clipped.
- **Tablet:** Sensible use of the mid-size canvas (not just stretched mobile or cramped desktop).
- **Desktop:** Primary target — no wasted/overflowing space.
- **Ultrawide:** Content has max-width / sensible behavior; doesn't stretch into unreadable line lengths.
- **Dark mode:** If supported, every surface is themed (no blinding white panels, broken contrast, or invisible borders).
- **Landscape mode:** (Mobile/tablet) Layout adapts; nothing critical is cut off or unreachable below the fold.

---

# 15 Internationalization

- **Language expansion:** Does the layout survive longer translated strings (German/Finnish), not clip or overflow buttons and labels?
- **Currency formatting:** Are amounts formatted per locale (symbol position, grouping, decimals) and is the currency unambiguous?
- **Date formatting:** Are dates locale-aware (no ambiguous `03/04/05`) and timezone-correct?
- **RTL support:** If RTL locales are in scope, does the layout mirror correctly (direction, alignment, icons) rather than breaking?

---

# 16 Analytics

You can't improve what you can't measure — verify the flow is instrumented.

- **Exposure events:** Are key screens/components firing impression/exposure events so you know what users actually see?
- **Click events:** Are primary and secondary actions tracked with enough context to be useful?
- **Conversion events:** Are the moments that matter (signup, activation, purchase, task completion) tracked explicitly?
- **Funnel tracking:** Can you reconstruct the full funnel and identify the exact step where users drop off?

---

# 17 Performance

- **First load:** Is initial load acceptably fast with perceived-performance techniques (skeletons, progressive render), not a long blank/spinner?
- **Navigation speed:** Are in-app transitions snappy (cached/prefetched where sensible), not a full reload each time?
- **Image loading:** Are images sized/compressed/lazy-loaded, with reserved space to prevent layout shift?
- **FPS / smoothness:** Do scroll, animation, and drag stay smooth (no jank) on realistic data volumes and mid-tier devices?
- **Interaction latency:** Do interactions respond immediately (optimistic UI / instant acknowledgment), so the product never feels laggy?

---

# 18 Versioning

- **Forced updates:** When a breaking update ships, are users prompted/forced gracefully with an explanation, not silently broken?
- **Optional updates:** Are non-breaking updates offered without disrupting in-progress work?
- **Rollback strategy:** If a release is bad, can it be rolled back without corrupting user data or stranding clients on an incompatible version?
- **Data compatibility:** Do older clients and newer servers (and vice versa) interoperate, and are stored documents/drafts forward/backward compatible?

---

# 19 Security & Privacy

(UX-facing, not a full security audit.)

- **Permission requests:** Are data/permission requests minimal, in-context, and justified — not over-asking?
- **Privacy disclosures:** Is it clear what data is collected/used (especially for AI features that may use user content), with accessible controls?
- **Data protection:** Is sensitive data masked/handled appropriately in the UI (no secrets in plaintext, no PII in URLs/logs surfaced to the user)?
- **Abuse prevention:** Are there guardrails against abuse/misuse (rate limits surfaced gracefully, destructive/automation actions gated, content limits explained)?

---

# 20 Release Readiness

The go/no-go view.

- **Design fidelity:** Does the built product match the intended design *behavior* (states, flows, interactions) — not just static pixels?
- **Functional completeness:** Are all states and paths from sections 01–19 actually implemented, including the unglamorous error/empty/edge ones?
- **Analytics readiness:** Is the instrumentation from Section 16 in place and verified *before* launch (not bolted on after)?
- **Rollback readiness:** Is there a tested rollback/kill-switch path and data-compatibility confidence (Section 18) if the release goes wrong?

---

# 21 AI Experience

Review AI-native product behavior, trust, explainability, recovery, and user control. Treat as **primary scope** when the target is an AI/agent product.

## Model Transparency
- Is the AI behavior understandable and reasonably predictable?
- Can users tell what the AI is doing at each stage?
- Is it clear whether a result was generated, retrieved, inferred, or manually entered?
- Are tool calls, agent actions, and system decisions visible when appropriate?

## Confidence and Evidence
- Are confidence levels communicated when uncertainty matters?
- Are citations, supporting evidence, or source references available when needed?
- Can users distinguish verified information from generated suggestions?
- Are unsupported claims or ambiguous outputs clearly surfaced?

## Failure Recovery
- Is there a clear recovery path when the AI output is incorrect, incomplete, or irrelevant?
- Can users retry, regenerate, edit, or refine the request?
- Are model failures, tool failures, and data failures distinguished clearly?
- Is the user protected from losing work after a failed generation?

## Human Override
- Can users manually edit, reject, approve, or replace AI-generated output?
- Is there a manual fallback when automation fails?
- Can users intervene before irreversible actions?
- Are approval gates present for high-risk operations?

## Multi-Agent Coordination
- Are agent roles and responsibilities clear?
- Can users understand which agent is currently acting?
- Are agent handoffs understandable?
- Are task status, ownership, and dependencies visible?
- Are parallel tasks and blocked tasks represented clearly?

## AI Memory and Context
- Is session memory visible or understandable?
- Can users tell what context is currently being used?
- Can users inspect, edit, remove, or reset context when appropriate?
- Is state persistence reliable across sessions?
- Are stale or conflicting memories handled clearly?

## AI Trust and Auditability
- Can users understand why an output was produced?
- Is there an audit trail for important actions?
- Are generated outputs traceable to sources, tools, agents, and user inputs?
- Are irreversible or high-impact AI actions reviewable before execution?

---

When the walk is complete, produce the report using [OUTPUT_TEMPLATE.md](OUTPUT_TEMPLATE.md), leading with the highest-risk findings. Calibrate against [EXAMPLES.md](EXAMPLES.md).
