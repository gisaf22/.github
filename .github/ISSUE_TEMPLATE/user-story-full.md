---
name: User Story (full)
about: Standard user story — why it matters, how it passes, how it fails.
title: ''
---

## User story
<!-- "As <consumer>, I want <capability>, so that <outcome>."
     The consumer is a system or repo, not a person. -->
As <consumer>, I want <capability>, so that <outcome>.

## Context
<!-- Parent epic and feature, then links to related issues, PRs, files, ADRs. -->
- Epic: #
- Feature: #
-

## Design decisions
<!-- From AGENT_WORKFLOW.md step 0, written before the acceptance criteria.
     One bullet per decision: the choice and why, with the options and evidence
     where it was a real choice. Prefix an undecided one with "OPEN:".
     Pickup treats every decision recorded here as settled. -->
- **<decision>:** <choice>, because <reason>

## Acceptance criteria
<!-- One row per criterion, numbered AC1, AC2, …
     Test tier: unit / integration / e2e / manual. Replace the example row. -->
| # | Given / When / Then | Why it matters | Fails if | Test tier |
|---|---|---|---|---|
| AC1 | Given <state>, when <action>, then <result> | <why> | <observable failure> | unit / integration / e2e / manual |

## Edge cases
<!-- Bullet list, each with its expected behaviour. -->
- <edge case>: <expected behaviour>

## Out of scope
<!-- Bullet list. -->
-

## Observability
<!-- What is logged or alerts on failure; "N/A because…" if none. -->

## Definition of Done
<!-- Mark any baseline item that does not apply as "N/A because…". -->
- [ ] All acceptance criteria pass
- [ ] Tests written first
- [ ] CI green
- [ ] Failure path observable
- [ ] Docs/ADR updated if behaviour or a decision changed
- [ ] No contract change without a compatibility note
