<!--
  TEMPLATE (Mode E) — copy into the TARGET product repository (e.g. as
  `docs/interaction-audit/reports/scoped-static-flow-report.md`) and fill it in for ONE named flow. Ships blank
  with the skill; do not fill it in here. See ../../WORKFLOW.md (Mode E). Replace every
  <placeholder>. Evidence is append-only and multi-stage. Mode E performs NO runtime validation:
  default findings to Static Inference + Runtime Verification Recommended unless existing tests
  confirm otherwise. Inspect only files relevant to the flow; do not scan the whole repository by
  default. Do not claim cross-page consistency for pages you did not inspect. If the flow cannot be
  confidently mapped, state the uncertainty and ask to clarify.
-->

# Scoped Static Flow Audit

## Target Flow

<one flow — e.g. authentication | onboarding | checkout | document upload | AI memory reset |
agent approval flow | permission downgrade | dashboard refresh recovery>

## Audit Profile

<MVP | Production | High-Risk / Regulated>

## Flow Map

Flow Entry: <...>
Relevant Routes: <...>
Components: <...>
State Stores: <...>
API Calls: <...>
Permission Rules: <...>
Failure Paths: <...>
Recovery Paths: <...>
Related Existing Bugs: <BUG-NNN, or "none found in scope">
Related Approved Decisions: <DECISION-NNN, or "none found in scope">

## Findings

### BUG-XXX — <title>

Severity: <Critical | High | Medium | Low>
Audit Profile: <MVP | Production | High-Risk / Regulated>
Initial Evidence Level: Static Inference
Current Verification Level: Static Inference
Verification Status: <Runtime Verification Recommended | Manual Verification Required | Not Yet Verified>
Confidence: <High | Medium | Low>
Evidence History:
  - Type: Static Inference  Source: <file path / component / state store / API endpoint>  Timestamp: <YYYY-MM-DD HH:mm>  Notes: <what the code shows>
Risk if Unfixed: <...>
Implementation Effort: <Low | Medium | High>
Recommended Timing: <Blocker | Current Sprint | Backlog | Defer>

Issue: <use cautious language — "appears to", "may", "could">
Impact: <...>
Recommendation: <...>
Manual Verification Steps: <exact reviewer / runtime steps to confirm>

<repeat per finding>

## Inspected Files

- <path>

## Uninspected Areas

- <area intentionally out of scope for this flow>

## Known Limitations

- No runtime access: dynamic behavior was not observed.
- <other limitations, e.g. cross-page consistency not claimed beyond inspected files>

## Static Inferences

- <finding refs that are code-level inferences>

## Runtime Verification Recommended

- <finding refs that need a running app / tests to confirm, with how to confirm>
