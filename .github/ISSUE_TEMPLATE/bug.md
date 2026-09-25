---
name: Bug
about: Defect — how to reproduce it and how the fix is proven.
title: ''
---

## Summary
<!-- One or two sentences: what is broken, where, and who or what it affects. -->

## Steps to reproduce
<!-- Numbered steps, starting from a known state. Include commands, inputs, versions. -->
1.

## Expected vs actual
<!-- What should happen, then what happens instead. -->
- Expected:
- Actual:

## Evidence
<!-- Logs, error output, failing run links, object keys, screenshots. No account IDs,
     ARNs or secrets. -->

## Acceptance criteria for the fix
<!-- One row per criterion, numbered AC1, AC2, … AC1 should reproduce the bug.
     Test tier: unit / integration / e2e / manual. Replace the example row. -->
| # | Given / When / Then | Why it matters | Fails if | Test tier |
|---|---|---|---|---|
| AC1 | Given <state>, when <action>, then <result> | <why> | <observable failure> | unit / integration / e2e / manual |

## Definition of Done
<!-- Mark any baseline item that does not apply as "N/A because…". -->
- [ ] All acceptance criteria pass
- [ ] Tests written first
- [ ] CI green
- [ ] Failure path observable
- [ ] Docs/ADR updated if behaviour or a decision changed
- [ ] No contract change without a compatibility note
