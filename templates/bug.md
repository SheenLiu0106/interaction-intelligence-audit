<!--
  TEMPLATE — copy this file into the ROOT of the TARGET product repository as `bug.md`
  (or into the target's `docs/audit/`), then fill it in. Do not edit this template in place
  while auditing; it ships blank with the skill. See ../WORKFLOW.md for the lifecycle rules.
  Replace every <placeholder>. Use 24-hour timestamps. Never delete history — append only.
-->

# Interaction Intelligence Audit — Bug Tracker

> Target: <name / path of the product repository being audited>
> Records live in the TARGET repo, never in the audit skill repo.

## Summary

Last Updated: <YYYY-MM-DD HH:mm>

| Status | Count |
|---|---:|
| Open | 0 |
| In Progress | 0 |
| Fixed — Awaiting Human Review | 0 |
| Needs Revision | 0 |
| Approved | 0 |
| Closed | 0 |
| Deferred | 0 |

---

## BUG-001 — <short descriptive title>

Status: Open
Type: <Bug | Regression>
Related Bug: <BUG-NNN if Type: Regression or Duplicate; else "—">
Severity: <Critical | High | Medium | Low>
Audit Profile: <MVP | Production | High-Risk / Regulated>
Category: <e.g. Interaction Feedback | Accessibility | Design Consistency | Error Handling>
Location: <page / component / file>
Detected: <YYYY-MM-DD HH:mm>
Last Updated: <YYYY-MM-DD HH:mm>
Reviewer: Pending
Related Files: <paths, or "Pending investigation">

### Evidence Classification

<!-- Evidence is append-only and multi-stage; types may coexist. Do not overwrite — append to
     Evidence History. Evidence Level = where evidence came from; Verification Status = whether it
     is sufficient to confirm the issue/fix. -->

Initial Evidence Level: <Static Inference | Runtime Observed | Test Verified | Human Verified>
Current Verification Level: <strongest evidence reached; may combine, e.g. "Runtime Observed + Test Verified">
Verification Status: <Verified | Runtime Verification Recommended | Manual Verification Required | Not Yet Verified>
Confidence: <High | Medium | Low>

#### Evidence History (append-only)

- Type: <Static Inference | Runtime Observed | Test Verified | Human Verified>
  Source: <file path | route | component | state store | API endpoint | test name | runtime observation | screenshot | manual-review note>
  Timestamp: <YYYY-MM-DD HH:mm>
  Notes: <what this evidence confirmed or changed>

### Prioritization

Risk if Unfixed: <consequence if shipped as-is>
Implementation Effort: <Low | Medium | High>
Recommended Timing: <Blocker | Current Sprint | Backlog | Defer>

### Issue

<Clear, objective description of the problem.>

### User Impact

<Who is affected and how it degrades their experience.>

### Evidence

- Page / component:
- Triggering action:
- Current result:
- Expected result:

### Reproduction Steps

1. <step>
2. <step>
3. <observe the issue>

### Expected Behavior

<The desired behavior.>

### Recommended Fix

<The smallest effective change.>

### Acceptance Criteria

- [ ] Required behavior is implemented.
- [ ] Loading / success / error states handled where relevant.
- [ ] Keyboard behavior verified where relevant.
- [ ] Responsive behavior verified where relevant.
- [ ] Existing approved workflows remain unaffected.
- [ ] Human review is completed.

### History

#### <YYYY-MM-DD HH:mm> — Issue Detected
- Status set to `Open`.
- Initial issue recorded.

<!-- Append further history entries below as the issue moves through its lifecycle.
     Never overwrite or delete earlier entries.
#### <YYYY-MM-DD HH:mm> — Fix Started
- Status changed to `In Progress`.
- Files under review: <paths>

#### <YYYY-MM-DD HH:mm> — Fix Implemented
- Status changed to `Fixed — Awaiting Human Review`.
- Files Modified: <paths — only the files necessary for this issue>
- Summary: <what changed>
- Validation Performed: <checks actually run: tests / typecheck / lint / build / runtime>
- Validation Not Available: <checks that could not be run in this environment>
- Manual Verification Steps: <exact steps for the reviewer>
- Regression Risks: <related flows that could be affected>
- Evidence History: append a new entry (Type / Source / Timestamp / Notes) for the post-fix verification; update Current Verification Level and Verification Status accordingly.
- Human review required.

#### <YYYY-MM-DD HH:mm> — Human Review
- Reviewer decision: <Approved | Rejected | Needs Revision | Deferred>
- Reviewer notes: <...>
-->
