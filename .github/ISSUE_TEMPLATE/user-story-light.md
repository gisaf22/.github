---
name: User Story (light)
about: XS user story — quick but traceable.
title: ''
---

## User story
<!-- "As <consumer>, I want <capability>, so that <outcome>."
     The consumer is a system or repo, not a person. -->
As <consumer>, I want <capability>, so that <outcome>.

## Acceptance criteria
<!-- 1–3 rows, numbered AC1, AC2, …
     Test tier: unit / integration / e2e / manual. Replace the example row. -->
| # | Given / When / Then | Why it matters | Fails if | Test tier |
|---|---|---|---|---|
| AC1 | Given <state>, when <action>, then <result> | <why> | <observable failure> | unit / integration / e2e / manual |

## Out of scope
<!-- Bullet list. -->
-

## Definition of Done
<!-- Mark any baseline item that does not apply as "N/A because…". -->
- [ ] All acceptance criteria pass
- [ ] Tests written first
- [ ] CI green
- [ ] Failure path observable
- [ ] Docs/ADR updated if behaviour or a decision changed
- [ ] No contract change without a compatibility note
