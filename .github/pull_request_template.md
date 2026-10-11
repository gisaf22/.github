Closes #

## Merge danger
<!-- Required. See AGENT_WORKFLOW.md, stage 4.
     Door: one-way (hard or impossible to undo once merged or published, e.g. a served
     contract, a published object, a schema consumers read) or two-way (a revert undoes it).
     For a one-way door, say when it closes: at merge, or at a later event such as the
     first publish.
     Blast radius: who or what breaks if this is wrong, at merge and later. -->
- **Door:** one-way / two-way.
- **Blast radius:**

## What

## Tests

## Definition of Done
<!-- Mark any item that does not apply as "N/A because…". -->
- [ ] All acceptance criteria pass
- [ ] Tests written first
- [ ] CI green
- [ ] Failure path observable
- [ ] Docs/ADR updated if behaviour or a decision changed
- [ ] No contract change without a compatibility note
- [ ] Merge danger stated: door, when it closes, and blast radius
