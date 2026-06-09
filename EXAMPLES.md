# Interaction Intelligence Audit — Examples

Worked example findings. These are **illustrative**, fabricated to calibrate the *style,
specificity, and severity* of findings — they do not describe any real product. Notice that every
finding names a concrete state/flow/component and follows the `Issue / Severity / Impact /
Recommendation` shape from [OUTPUT_TEMPLATE.md](OUTPUT_TEMPLATE.md), and that every finding is
**behavioral** — about how the product *behaves*, not how it looks.

The first set demonstrates the **logic-first** scope directly (including how to handle a visual
observation). The later sets show full findings across five product types.

---

## Logic-first findings (and the visual-observation judgment call)

### Example 1 — Missing Loading State

**Issue:** When the user submits a "Generate report" request, the button click produces no progress feedback — the screen is unchanged for ~4s until the result appears.
**Severity:** High
**Impact:** Users can't tell whether the click registered, so they click again (triggering duplicate requests) or assume the product is broken and abandon the task.
**Recommendation:** Acknowledge the submission immediately (button → loading state, skeleton/progress on the target area) and disable re-submit until the request resolves. (Checklist 09 Feedback; 04 Page States — Loading)

### Example 2 — Duplicate Submission Risk

**Issue:** The submit button stays active and clickable while the request is in flight; rapid or repeated clicks each fire a separate create request.
**Severity:** Critical
**Impact:** Users create duplicate records (or double-charge a payment) through ordinary impatience on a slow network — a data-integrity failure.
**Recommendation:** Disable the button on first click, show an in-progress state, and guard the mutation server-side with idempotency. (Checklist 06 Forms — duplicate submission prevention; 08 Destructive Actions — race conditions)

### Example 3 — State Loss After Refresh

**Issue:** In a multi-step setup wizard, a browser refresh on step 4 returns the user to step 1 with all prior input discarded.
**Severity:** High
**Impact:** Any accidental refresh, tab reload, or session blip throws away substantial work; users re-enter everything or give up, hurting completion.
**Recommendation:** Persist wizard progress (autosave per step) and restore to the last completed step on reload. (Checklist 02 User Flows — resume/draft recovery; 04 Page States — refresh recovery)

### Example 4 — Permission Failure Without Recovery

**Issue:** A member-role user opens a shared admin link and sees a bare "403 — Not authorized" with no further information or action.
**Severity:** High
**Impact:** The user is stranded: they don't know *why* they're blocked, what access they'd need, or how to proceed — a dead end on a real path.
**Recommendation:** Explain the constraint ("This area requires Admin access") and offer a next step (request access, switch account, contact workspace owner). (Checklist 12 Permissions & Access Control — authorization failure recovery)

### Example 5 — AI Context Loss

**Issue:** In a multi-turn AI assistant, after a tool call the assistant silently drops the earlier conversation context and answers a follow-up as if starting fresh — contradicting its prior answer.
**Severity:** High
**Impact:** Users get inconsistent, lower-trust answers with no signal that context was lost, and no way to see or restore what the model is using.
**Recommendation:** Preserve and surface active context, signal when context is reset, and let users inspect/restore it. (Checklist 21 AI Experience — AI Memory and Context; context loss)

### Example 6 — Visual Observation That Should NOT Be Logged

**Observation:** A card in the dashboard grid has ~2px more internal padding than its neighbors.
**Verdict:** **Do not log.** This is a pure visual-polish detail. The cards are still clearly distinct, every action is reachable and unambiguous, and nothing about task completion, comprehension, accessibility, data integrity, or workflow safety is affected. Under the logic-first scope, an aesthetic inconsistency with **no material behavioral impact is not a bug** — logging it would drift the audit toward visual-design critique.

### Example 7 — Visual Observation That SHOULD Be Logged

**Issue:** The "Delete project" (destructive) and "Duplicate project" (non-destructive) buttons are visually identical — same color, size, and weight — and sit adjacent in the row actions.
**Severity:** High
**Impact:** Because the destructive and non-destructive actions are indistinguishable and adjacent, users select the wrong one and delete projects by accident — a workflow-safety and data-integrity risk.
**Recommendation:** Differentiate the destructive action (distinct treatment + separation) and add a confirmation/undo for delete. **This is logged not as a styling note but because the lack of visual distinction creates a measurable accidental-deletion risk** — exactly the kind of visual observation that *does* belong in this audit. (Checklist 08 Destructive Actions; 22 Cross-Page & Cross-Component Consistency)

---

## Evidence- and profile-aware findings (v0.4.0)

These show the v0.4.0 finding fields — **Evidence Level, Confidence, Verification Status, Evidence
Source, Audit Profile, Risk if Unfixed, Implementation Effort, Recommended Timing** — and the Mode
D / Mode E report shapes. They are illustrative and fabricated.

### Example 8 — Static inference requiring runtime verification

**Issue:** The "Save" handler **appears to** fire the mutation without awaiting the prior request, so a quick second click *could* submit twice — inferred from the click handler in `CheckoutForm.tsx`, not yet reproduced.
**Severity:** High · **Audit Profile:** Production
**Initial Evidence Level:** Static Inference · **Current Verification Level:** Static Inference · **Verification Status:** Runtime Verification Recommended · **Confidence:** Medium
**Evidence History:** Static Inference — `src/components/CheckoutForm.tsx:88` (onClick → `submitOrder()` not awaited; button not disabled), 2026-06-08 10:12 — "code shows no in-flight guard"
**Risk if Unfixed:** Possible duplicate orders on a slow network. **Implementation Effort:** Low · **Recommended Timing:** Current Sprint
**Impact:** If confirmed, users double-submit orders. **Recommendation:** Disable the button on first click and guard the mutation with idempotency.
**Manual Verification Steps:** Run the app, open checkout on a throttled network, double-click Save, and confirm whether two orders are created. (Note the cautious "appears to / could" language — this is not presented as confirmed runtime fact.)

### Example 9 — Runtime-observed duplicate submission

**Issue:** Double-clicking "Place order" creates two orders — **reproduced** in the running app on a throttled connection.
**Severity:** Critical · **Audit Profile:** Production
**Initial Evidence Level:** Static Inference · **Current Verification Level:** Runtime Observed · **Verification Status:** Verified · **Confidence:** High
**Evidence History:** (1) Static Inference — `OrderButton.tsx` stays enabled during request, 2026-06-08 10:20; (2) Runtime Observed — dev server `/checkout`, Network shows two `POST /orders` (201) from one rage-click, screenshot attached, 2026-06-08 11:05 — "reproduced; static inference confirmed"
**Risk if Unfixed:** Confirmed duplicate charges and duplicate fulfillment. **Implementation Effort:** Low · **Recommended Timing:** Blocker
**Impact:** Real users are double-charged. **Recommendation:** Disable on submit, show in-progress state, and enforce server-side idempotency keys.

### Example 10 — Test-verified refresh recovery

**Issue:** Wizard progress now survives a mid-flow refresh — **confirmed by an automated test**.
**Severity:** N/A (verification of a fix) · **Audit Profile:** Production
**Initial Evidence Level:** Runtime Observed · **Current Verification Level:** Runtime Observed + Test Verified · **Verification Status:** Verified · **Confidence:** High
**Evidence History:** (1) Runtime Observed — manual reload on step 4 restored state, 2026-06-08 14:02; (2) Test Verified — Playwright spec `e2e/wizard-refresh.spec.ts` ("restores step 4 after reload") passing, 2026-06-08 14:30 — "locked in against regression"
**Risk if Unfixed:** N/A — verified working. **Implementation Effort:** — · **Recommended Timing:** —
**Impact:** Refresh no longer discards multi-step input. **Recommendation:** Keep the test in CI to prevent regression (cross-reference Section 23).

### Example 11 — MVP-profile issue intentionally deferred

**Issue:** On a triple-rapid tab-switch during upload, a transient flicker shows a stale count for ~200ms before correcting.
**Severity:** Low · **Audit Profile:** MVP
**Initial Evidence Level:** Runtime Observed · **Current Verification Level:** Runtime Observed · **Verification Status:** Verified · **Confidence:** High
**Evidence History:** Runtime Observed — `/uploads`, rapid tab toggle shows ~200ms stale count, 2026-06-08 09:40 — "self-corrects"
**Risk if Unfixed:** Brief cosmetic-adjacent flicker; self-corrects; no data loss, no wrong action. **Implementation Effort:** Medium · **Recommended Timing:** Defer
**Impact:** Negligible at MVP stage. **Recommendation:** Defer — under the MVP profile this low-probability, self-correcting edge case is **intentionally not** worth complex synchronization work now. Revisit at Production profile.

### Example 12 — Production-profile issue prioritized

**Issue:** After an optimistic "Archive," a failed server call leaves the row hidden locally but still active on the server; a refresh resurrects it with no error shown.
**Severity:** High · **Audit Profile:** Production
**Initial Evidence Level:** Runtime Observed · **Current Verification Level:** Runtime Observed · **Verification Status:** Verified · **Confidence:** High
**Evidence History:** Runtime Observed — `/projects`, archive with API forced to 500; state store `projectsStore` keeps row hidden, refresh resurrects it with no error, 2026-06-08 13:15
**Risk if Unfixed:** Users believe items are archived when they are not — state desync and lost trust. **Implementation Effort:** Medium · **Recommended Timing:** Current Sprint
**Impact:** Optimistic update never rolls back on failure. **Recommendation:** Roll back the optimistic change and surface a retryable error (Section 04 — optimistic updates & rollback).

### Example 13 — High-Risk / Regulated issue treated as a blocker

**Issue:** A clinician can finalize and lock a patient record with no audit-trail entry recording who approved it or when.
**Severity:** Critical · **Audit Profile:** High-Risk / Regulated
**Initial Evidence Level:** Static Inference · **Current Verification Level:** Static Inference · **Verification Status:** Manual Verification Required · **Confidence:** High
**Evidence History:** Static Inference — `services/records/finalize.ts` writes no `audit_log` row on finalize; no approver field persisted, 2026-06-08 15:50 — "needs staging confirmation"
**Risk if Unfixed:** Irreversible, compliance-sensitive action with no traceability — fails audit and accountability requirements. **Implementation Effort:** Medium · **Recommended Timing:** Blocker
**Impact:** No provenance for an irreversible regulated action. **Recommendation:** Require explicit approver + immutable audit-trail entry before finalize; block finalize until both are recorded.
**Manual Verification Steps:** In a staging record, finalize and confirm an `audit_log` row with approver identity and timestamp is created. (Under High-Risk/Regulated this is a blocker even though probability per-action is low.)

### Example 14 — Visual blocker logged because a modal is hidden

**Issue:** The "Confirm deletion" modal renders **behind** the sticky page header (lower z-index); its confirm button is covered and cannot be clicked, so deletion can neither be confirmed nor cancelled — the user is trapped.
**Severity:** Critical · **Audit Profile:** Production
**Initial Evidence Level:** Runtime Observed · **Current Verification Level:** Runtime Observed · **Verification Status:** Verified · **Confidence:** High
**Evidence History:** Runtime Observed — `/files`, click Delete; modal `z-index: 10` < header `z-index: 50` covers the confirm button; screenshot attached, 2026-06-08 16:10
**Risk if Unfixed:** Core destructive flow is unusable and the user is stuck behind an un-dismissable overlay. **Implementation Effort:** Low · **Recommended Timing:** Blocker
**Impact:** Task completion is blocked. **Recommendation:** Raise modal/overlay stacking above the header and trap focus in the dialog. **This is logged as an Interaction-Observable Visual Blocker — a visual condition that materially blocks interaction — not as a styling note.**

### Example 15 — Visual polish ignored because it has no workflow impact

**Observation:** The modal's title uses a slightly heavier font weight than the page headings, and its corners are a touch more rounded than the cards behind it.
**Verdict:** **Do not log.** This is pure visual polish: the modal is fully visible and operable, every control is reachable and unambiguous, and nothing about task completion, comprehension, accessibility, data integrity, or workflow safety is affected. Logging it would drift the audit toward subjective visual-design critique, which is out of scope.

---

## 1. SaaS Dashboard

*Support-analytics dashboard, web + tablet, roles: agent and manager.*

**Issue:** The dashboard renders a blank white panel when a team has no tickets in the selected range, with no message or CTA.
**Severity:** High
**Impact:** New teams and quiet periods look like a broken product; users assume data failed to load and churn or file support tickets.
**Recommendation:** Add a purposeful empty state: "No tickets in this range" + a control to widen the range or change filters. (Checklist 04 Page States, 05 Data States)

**Issue:** KPI cards show raw integers like `1340567` with no grouping, overflowing the card on tablet.
**Severity:** Medium
**Impact:** Numbers are misread at a glance and the layout breaks at narrow widths.
**Recommendation:** Format large numbers (`1.34M` with full value on hover) and reserve card width for the largest expected value. (Checklist 05 Data States, 14 Responsive)

**Issue:** Data can be hours stale (cached) with no indication, and there's no manual refresh.
**Severity:** High
**Impact:** Managers make staffing decisions on stale numbers, eroding trust in the product.
**Recommendation:** Show "Updated X ago," auto-refresh on focus, and add a manual refresh control. (Checklist 05 Data States — refresh & cache invalidation)

**Issue:** Date-range and team filters are mouse-only; they can't be reached or operated by keyboard.
**Severity:** High
**Impact:** Keyboard and screen-reader users cannot scope the data at all — the core task is impossible for them.
**Recommendation:** Make filters native/focusable controls with visible focus and proper labels. (Checklist 13 Accessibility)

---

## 2. Workflow Builder (Dify / LangGraph style)

*Node-based workflow/agent builder. Flow: create → add nodes → connect → run → inspect output.*

**Issue:** There is no autosave and no draft recovery; a refresh or crash mid-build loses the entire canvas.
**Severity:** Critical
**Impact:** Users lose substantial work with no recovery — a top-severity trust and retention killer.
**Recommendation:** Autosave the canvas with a visible save state (saving / saved / failed) and restore on reload. (Checklist 02 User Flows — autosave/draft recovery)

**Issue:** When a run fails, the error says only "Run failed" — it doesn't distinguish a model error, a tool/API error, or bad input data.
**Severity:** High
**Impact:** Users can't tell what to fix, so they retry blindly or abandon the workflow.
**Recommendation:** Attribute failures to the specific node and failure class (model / tool / data) with the node highlighted on the canvas. (Checklist 11 Error Handling, 21 AI Experience — Failure Recovery)

**Issue:** Deleting a node is immediate with no undo and no confirmation, even when it has downstream connections.
**Severity:** High
**Impact:** One misclick destroys connected logic; rebuilding is tedious and error-prone.
**Recommendation:** Add undo ("Node deleted — Undo") and confirm deletion of connected nodes. (Checklist 08 Destructive Actions)

**Issue:** Invalid node configuration (missing required field) is only surfaced at run time, not on the node itself.
**Severity:** Medium
**Impact:** Users build a whole flow before discovering it can't run, late in the loop.
**Recommendation:** Validate inline and badge invalid nodes on the canvas before run. (Checklist 06 Forms, 07 Components)

**Issue:** During a long run, the canvas shows a single spinner — you can't tell which node is executing or which agent is acting.
**Severity:** Medium
**Impact:** Long runs feel frozen; users can't reason about progress or where it's stuck.
**Recommendation:** Show per-node run status and the currently-active agent/step. (Checklist 09 Feedback, 21 AI Experience — Multi-Agent Coordination)

---

## 3. AI Chat Product

*Chat product with tool use and citations. `is_ai_product: true` — Section 21 in primary scope.*

**Issue:** Generated answers and retrieved/cited facts look visually identical; users can't tell what's grounded vs. invented.
**Severity:** High
**Impact:** Users over-trust hallucinated content or under-trust accurate content — directly undermines the product's value.
**Recommendation:** Visually distinguish retrieved/cited content from generated content and make citations clickable to the source. (Checklist 21 — Model Transparency, Confidence & Evidence)

**Issue:** If a generation fails midway, the partial/failed message disappears and the user's prompt is lost from the input.
**Severity:** Critical
**Impact:** Users lose their composed prompt and must retype; on a flaky network this is repeated, infuriating data loss.
**Recommendation:** Preserve the prompt on failure, keep the message with a retry affordance, and never clear the composer until success. (Checklist 21 — Failure Recovery; 11 Error Handling)

**Issue:** There's no way to regenerate, edit, or refine a bad answer — only to type a new message.
**Severity:** High
**Impact:** Users can't steer the model efficiently; the core interaction loop is weaker than competitors'.
**Recommendation:** Add regenerate, edit-prompt, and "refine" affordances on each AI response. (Checklist 21 — Failure Recovery, Human Override)

**Issue:** The model uses prior conversation and uploaded files as context, but the user can't see, edit, or clear what's in context.
**Severity:** Medium
**Impact:** Stale or wrong context silently skews answers and users have no lever to fix it.
**Recommendation:** Surface active context with controls to inspect, remove, or reset it. (Checklist 21 — AI Memory and Context)

**Issue:** Tool calls (web search, code execution) happen invisibly; the user only sees the final answer.
**Severity:** Medium
**Impact:** Users can't audit how an answer was produced or trust high-stakes outputs.
**Recommendation:** Show tool actions inline (collapsible) so the reasoning trail is auditable. (Checklist 21 — Model Transparency, AI Trust & Auditability)

---

## 4. Mobile App

*Consumer mobile app, iOS + Android, uses camera and notifications.*

**Issue:** The app requests camera and notification permissions on first launch, before the user understands why.
**Severity:** High
**Impact:** Users deny by default; because the OS won't re-prompt, the dependent features are then permanently broken with no guidance.
**Recommendation:** Request permissions in context with a priming explanation, and add a re-enable path (deep link to settings) for previously-denied permissions. (Checklist 12 Permissions)

**Issue:** When offline, actions appear to succeed but silently fail when connectivity returns.
**Severity:** Critical
**Impact:** Users believe data is saved when it isn't — silent data loss.
**Recommendation:** Detect offline, queue actions with clear pending state, and confirm or surface failures on reconnect. (Checklist 01 Preconditions — offline; 09 Feedback)

**Issue:** In landscape orientation, the bottom action bar is cut off and the primary CTA is unreachable.
**Severity:** High
**Impact:** Landscape users cannot complete the core action at all.
**Recommendation:** Make the layout adapt to landscape and keep primary actions in the safe area. (Checklist 14 Responsive — landscape)

**Issue:** Tapping a list row gives no immediate feedback; the next screen takes ~1s to appear, so users double-tap.
**Severity:** Medium
**Impact:** Double-taps trigger duplicate navigation/actions and the app feels laggy.
**Recommendation:** Acknowledge taps instantly (pressed state + skeleton on the next screen) and guard against double-trigger. (Checklist 09 Feedback, 17 Performance)

---

## 5. Onboarding Flow

*New-user onboarding, SaaS web app, signup → first value.*

**Issue:** If a user drops off mid-onboarding and returns, they restart from step 1 with all prior input lost.
**Severity:** High
**Impact:** Drop-off becomes permanent abandonment; activation rate suffers.
**Recommendation:** Persist progress and resume at the last completed step. (Checklist 02 User Flows — resume/session restore)

**Issue:** Onboarding is mandatory with no skip or "do this later," and one step requires data the user doesn't have yet.
**Severity:** High
**Impact:** Users hit a wall on a step they can't complete and cannot proceed to value.
**Recommendation:** Allow skip/defer for non-essential steps and never hard-block on optional data. (Checklist 02 User Flows, 03 IA)

**Issue:** The final "You're all set!" screen has no next action — it just sits there.
**Severity:** Medium
**Impact:** Users reach the end of onboarding without being delivered to first value, blunting the activation moment.
**Recommendation:** Route the success state straight into the first real task with a clear primary CTA. (Checklist 02 User Flows — success path; 09 Feedback)

**Issue:** None of the onboarding steps fire exposure or completion events, so step-level drop-off is invisible.
**Severity:** High
**Impact:** The team can't see where users abandon onboarding and can't prioritize fixes — flying blind on the most important funnel.
**Recommendation:** Instrument exposure, step-complete, and overall-complete events to reconstruct the funnel. (Checklist 16 Analytics)

---

## 6. Mode D — Targeted Runtime-Assisted Flow Audit (example report)

*Target flow: checkout. Production profile. Dev server + existing Playwright suite available.*

```md
# Targeted Runtime-Assisted Flow Audit

## Target Flow
Checkout (cart → address → payment → confirmation)

## Audit Profile
Production

## Environment Capabilities
| Capability | Status |
|---|---|
| Repository Access | Available |
| Terminal Execution | Available |
| Dev Server | Available |
| Browser Preview | Available |
| Playwright | Available |
| Cypress | Unavailable |
| Existing E2E Suite | Available (checkout.spec.ts) |
| Manual Verification | Optional |

## Flow Map
Entry Point: /cart → CheckoutWizard
Routes: /checkout/address, /checkout/payment, /checkout/confirm
Components: CheckoutWizard, AddressForm, PaymentForm, PlaceOrderButton
State Stores: cartStore, checkoutStore
API Calls: POST /orders, POST /payments
Failure Paths: payment decline, network timeout on POST /orders
Recovery Paths: retry payment, resume wizard after refresh

## Findings

### BUG-101 — Double-click on Place Order creates duplicate orders
Severity: Critical
Audit Profile: Production
Initial Evidence Level: Static Inference
Current Verification Level: Runtime Observed
Verification Status: Verified
Confidence: High
Evidence History:
  - Type: Static Inference  Source: PlaceOrderButton.tsx (stays enabled during request)  Timestamp: 2026-06-08 10:20  Notes: suspected double-submit
  - Type: Runtime Observed  Source: dev server /checkout/confirm; Network shows two POST /orders (201) from one rage-click  Timestamp: 2026-06-08 11:05  Notes: reproduced
Risk if Unfixed: Duplicate charges and fulfillment
Implementation Effort: Low
Recommended Timing: Blocker

Issue: PlaceOrderButton stays enabled during the request.
Impact: Real users are double-charged on slow networks.
Recommendation: Disable on submit + server-side idempotency key.
Manual Verification Steps: n/a (runtime-verified).

### BUG-102 — Payment timeout shows no retry
Severity: High
Audit Profile: Production
Initial Evidence Level: Static Inference
Current Verification Level: Static Inference
Verification Status: Runtime Verification Recommended
Confidence: Medium
Evidence History:
  - Type: Static Inference  Source: PaymentForm.tsx — catch block swallows timeout, no retry surfaced  Timestamp: 2026-06-08 11:20  Notes: not yet reproduced at runtime
Risk if Unfixed: Users stranded after a timeout, may re-pay elsewhere
Implementation Effort: Medium
Recommended Timing: Current Sprint

Issue: A timed-out payment appears to leave the form in a dead state.
Impact: If confirmed, the user cannot retry in place.
Recommendation: Surface a retryable error preserving entered data.
Manual Verification Steps: Force POST /payments to time out; confirm whether a retry affordance appears.

## Tested Scenarios
- [x] Loading
- [x] Success
- [x] Duplicate click
- [ ] Timeout — Runtime Verification Recommended
- [ ] Permission denied — Manual Verification Required

## Summary
Verified Findings: 1 (BUG-101)
Static Inferences: 1 (BUG-102)
Runtime Verification Recommended: 1 (BUG-102 timeout)
Manual Verification Required: 1 (permission-denied scenario)
```

Mode D stops here unless explicitly asked to enter Mode B.

---

## 7. Mode E — Scoped Static Flow Audit (example report)

*Target flow: AI memory reset, in a large monorepo. No runtime access. Production profile.*

```md
# Scoped Static Flow Audit

## Target Flow
AI memory reset ("Clear context" in the assistant)

## Audit Profile
Production

## Flow Map
Flow Entry: AssistantPanel → ClearContextButton
Relevant Routes: /assistant
Components: AssistantPanel, ClearContextButton, ContextChips
State Stores: conversationStore (messages, contextRefs), memoryStore
API Calls: DELETE /sessions/:id/context
Permission Rules: any signed-in user may reset own context
Failure Paths: DELETE fails; partial clear (messages cleared, contextRefs retained)
Recovery Paths: none found in scope
Related Existing Bugs: BUG-044 (stale context) — Closed
Related Approved Decisions: DECISION-007 (context must be user-resettable) — Approved

## Findings

### BUG-201 — Reset clears messages but not pinned context refs
Severity: High
Audit Profile: Production
Initial Evidence Level: Static Inference
Current Verification Level: Static Inference
Verification Status: Runtime Verification Recommended
Confidence: Medium
Evidence History:
  - Type: Static Inference  Source: conversationStore.ts resetContext() clears `messages` but not `contextRefs`  Timestamp: 2026-06-08 12:40  Notes: no runtime access in Mode E
Risk if Unfixed: Stale pinned context silently influences later answers after a "reset"
Implementation Effort: Low
Recommended Timing: Current Sprint

Issue: resetContext() appears to leave contextRefs populated.
Impact: If confirmed, "Clear context" does not fully clear context — contradicts DECISION-007.
Recommendation: Clear contextRefs (and confirm DELETE success) within resetContext().
Manual Verification Steps: Pin a doc, Clear context, ask a question referencing it; confirm it is no longer used.

## Inspected Files
- src/assistant/AssistantPanel.tsx
- src/state/conversationStore.ts
- src/state/memoryStore.ts

## Uninspected Areas
- Server-side DELETE /sessions/:id/context handler (out of scoped flow)
- Other entry points that may mutate conversationStore

## Known Limitations
- No runtime access: dynamic store behavior not observed.
- Cross-page consistency NOT claimed — only the assistant flow's files were inspected.

## Static Inferences
- BUG-201 (contextRefs not cleared)

## Runtime Verification Recommended
- BUG-201 — reproduce reset behavior in a running session.
```

Mode E stops here unless explicitly asked to enter Mode B.

---

These examples are intentionally partial — a real audit produces the full report per [OUTPUT_TEMPLATE.md](OUTPUT_TEMPLATE.md), including Executive Summary, Category Scores (with **AI Experience** when applicable), and Recommended Next Sprint.
