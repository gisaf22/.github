---
name: Item (full)
about: Standard item — why it matters, how it passes, how it fails.
title: ''
---

## Feature
<!-- "As <consumer>, I want <capability>, so that <outcome>."
     The consumer is a system or repo, not a person. -->
As <consumer>, I want <capability>, so that <outcome>.

## Context
<!-- Links to related issues, PRs, files, ADRs. -->
-

## Scenarios
<!-- One row per scenario. Scenario is Given / When / Then.
     Tier: unit / integration / e2e / manual. Replace the example row. -->
| # | Scenario | Why it matters | Fails if | Tier |
|---|---|---|---|---|
| S1 | Given <state>, when <action>, then <result> | <why> | <observable failure> | unit / integration / e2e / manual |

## Edge cases
<!-- Bullet list, each with its expected behaviour. -->
- <edge case>: <expected behaviour>

## Out of scope
<!-- Bullet list. -->
-

## Observability
<!-- What is logged or alerts on failure; "N/A because…" if none. -->

## Done when
<!-- Mark any baseline item that does not apply as "N/A because…". -->
- [ ] All scenarios pass
- [ ] Tests written first
- [ ] CI green
- [ ] Failure path observable
- [ ] Docs/ADR updated if behaviour or a decision changed
- [ ] No contract change without a compatibility note
