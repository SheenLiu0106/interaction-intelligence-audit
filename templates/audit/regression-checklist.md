<!--
  TEMPLATE — optional. Copy into the TARGET product repository (e.g. `docs/audit/regression-checklist.md`)
  and run the relevant items per fix. Items that cannot be verified automatically must be marked
  "Manual Verification Required" with exact steps for the reviewer. See ../../WORKFLOW.md.
-->

# Regression Checklist — BUG-<NNN>

## Functional

- [ ] Original issue no longer reproduces.
- [ ] Core workflow still works.
- [ ] Error path still works.
- [ ] Loading state works.
- [ ] Success state works.
- [ ] Disabled state works.
- [ ] Duplicate action is prevented where relevant.

## Interaction

- [ ] CTA behavior remains clear.
- [ ] Feedback appears at the correct time.
- [ ] User can recover from failure.
- [ ] Navigation remains predictable.
- [ ] No unexpected layout shift introduced.

## Accessibility

- [ ] Keyboard navigation works.
- [ ] Focus states are visible.
- [ ] Accessible labels are present.
- [ ] Alerts use appropriate ARIA behavior.
- [ ] Reduced-motion behavior is respected where relevant.

## Responsive

- [ ] Desktop layout works.
- [ ] Tablet layout works.
- [ ] Mobile layout works.
- [ ] No clipped or overflowing content.

## Evidence & Runtime Verification

- [ ] Evidence Level recorded for the verified fix (Static Inference / Runtime Observed / Test Verified / Human Verified).
- [ ] Runtime behavior exercised where available (dev server / browser / E2E).
- [ ] Automated test added or updated where feasible.
- [ ] Checks that could not be run are marked "Manual Verification Required" with exact steps.
- [ ] No finding is claimed runtime-verified unless runtime validation was actually performed.

## Historical Compatibility

- [ ] Previously approved interaction decisions remain intact.
- [ ] Previously closed issues have not reappeared.

<!-- For any item not verifiable in this environment, replace the checkbox with:
     Manual Verification Required — <exact steps for the reviewer>. -->
