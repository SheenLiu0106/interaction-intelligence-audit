<!--
  TEMPLATE (Mode D) — copy into the TARGET product repository (e.g. as
  `docs/audit/reports/targeted-runtime-flow-report.md`) and fill it in for ONE named flow. Ships
  blank with the skill; do not fill it in here. See ../../WORKFLOW.md (Mode D). Replace every
  <placeholder>. Evidence is append-only and multi-stage. Do not claim a scenario was tested unless
  it was actually executed. Do not install new dependencies unless the user explicitly approves.
-->

# Targeted Runtime-Assisted Flow Audit

## Target Flow

<one flow — e.g. authentication | onboarding | checkout | document upload | dashboard loading |
permission downgrade | AI-agent execution | AI memory reset | approval workflow | form submission |
state recovery after refresh>

## Audit Profile

<MVP | Production | High-Risk / Regulated>

## Environment Capabilities

| Capability | Status |
|---|---|
| Repository Access | Available / Unavailable |
| Terminal Execution | Available / Unavailable |
| Dev Server | Available / Unavailable |
| Browser Preview | Available / Unavailable |
| Playwright | Available / Unavailable |
| Cypress | Available / Unavailable |
| Existing E2E Suite | Available / Unavailable |
| Manual Verification | Required / Optional |

## Flow Map

Entry Point: <...>
Routes: <...>
Components: <...>
State Stores: <...>
API Calls: <...>
Failure Paths: <...>
Recovery Paths: <...>

## Findings

### BUG-XXX — <title>

Severity: <Critical | High | Medium | Low>
Audit Profile: <MVP | Production | High-Risk / Regulated>
Initial Evidence Level: <Static Inference | Runtime Observed | Test Verified | Human Verified>
Current Verification Level: <strongest evidence reached; may combine, e.g. "Runtime Observed + Test Verified">
Verification Status: <Verified | Runtime Verification Recommended | Manual Verification Required | Not Yet Verified>
Confidence: <High | Medium | Low>
Evidence History:
  - Type: <...>  Source: <file | route | component | state store | API | test | observation | screenshot | note>  Timestamp: <YYYY-MM-DD HH:mm>  Notes: <...>
Risk if Unfixed: <...>
Implementation Effort: <Low | Medium | High>
Recommended Timing: <Blocker | Current Sprint | Backlog | Defer>

Issue: <...>
Impact: <...>
Recommendation: <...>
Manual Verification Steps: <exact reviewer steps when not "Verified">

<repeat per finding>

## Tested Scenarios

<!-- Mark [x] only for scenarios actually executed. Leave others [ ] with their status. -->

- [ ] Loading
- [ ] Empty
- [ ] Success
- [ ] Error
- [ ] Partial success
- [ ] Retry
- [ ] Disabled state
- [ ] Duplicate click
- [ ] Slow network
- [ ] Timeout
- [ ] API error
- [ ] Permission denied
- [ ] Stale state
- [ ] Refresh recovery
- [ ] Session recovery
- [ ] Optimistic-update rollback
- [ ] Unsaved changes
- [ ] AI context loss
- [ ] Human override
- [ ] Unsafe AI auto-apply
- [ ] Multi-agent handoff failure

## Summary

Verified Findings: <count + refs>
Static Inferences: <count + refs>
Runtime Verification Recommended: <count + refs>
Manual Verification Required: <count + refs>

Runtime tooling used: <what was actually run, or "none — static only">
Could not validate: <what, and why>
