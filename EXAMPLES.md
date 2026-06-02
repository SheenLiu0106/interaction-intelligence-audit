# Interaction Intelligence Audit — Examples

Worked example findings for five common product types. These are **illustrative**, fabricated to calibrate the *style, specificity, and severity* of findings — they do not describe any real product. Notice that every finding names a concrete state/flow/component and follows the `Issue / Severity / Impact / Recommendation` shape from [OUTPUT_TEMPLATE.md](OUTPUT_TEMPLATE.md).

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

These examples are intentionally partial — a real audit produces the full report per [OUTPUT_TEMPLATE.md](OUTPUT_TEMPLATE.md), including Executive Summary, Category Scores (with **AI Experience** when applicable), and Recommended Next Sprint.
