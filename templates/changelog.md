<!--
  TEMPLATE — copy this file into the TARGET product repository as `changelog.md` alongside
  `bug.md`, then append one record per fix. Append-only; never delete prior records.
  See ../WORKFLOW.md. Replace every <placeholder>.
-->

# Interaction Intelligence Audit — Change Log

## <YYYY-MM-DD>

### BUG-001 — <short descriptive title>

Status: Fixed — Awaiting Human Review
Audit Profile: <MVP | Production | High-Risk / Regulated>
Current Verification Level: <strongest evidence reached; may combine, e.g. "Runtime Observed + Test Verified">
Verification Status: <Verified | Runtime Verification Recommended | Manual Verification Required | Not Yet Verified>

#### Objective

<The issue being addressed.>

#### Files Modified

- <path/to/file — only the files necessary for this issue>

#### Changes Applied

- <change 1>
- <change 2>

#### Behavior Before

<Original behavior.>

#### Behavior After

<Updated behavior.>

#### Validation Performed

- <checks actually run: tests / typecheck / lint / build / runtime — with results>

#### Validation Not Available

- <checks that could not be run in this environment>

#### Manual Verification Steps

- <exact steps for the reviewer to confirm the fix>

#### Regression Risks

- <related flows that could be affected>

#### Regression Checks

- [ ] Primary flow verified.
- [ ] Error path verified.
- [ ] Existing interaction pattern preserved.
- [ ] Human review pending.

#### Review Status

Pending Human Review

---
