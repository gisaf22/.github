# Playbook: data and dbt

Applies to work that captures, models or serves data: raw captures, manifests and sidecars,
dbt models, served tables. It adds to [AGENT_WORKFLOW.md](../AGENT_WORKFLOW.md); it never
overrides it.

## Stage 0 checklist additions

Add these to the core checklist when listing open design decisions.

- **Grain and keys:** one row per what; natural or surrogate key; what makes it unique.
- **Which capture wins, per column:** latest, first, last before an event; how ties break.
- **Change over time:** Type 1 (overwrite) or Type 2 (history); what a change should do
  (overwrite, version, or fail the build).
- **Which attributes belong:** what goes on this model, and what belongs at another grain or
  in another model. For each new column, ask whether it varies across the model's full key.
  If it doesn't, it belongs at a coarser grain: a value that is the same for every player in
  a capture is a capture attribute, not a player attribute.
- **Placement:** layer; private or served; reuse of an existing model rather than a new one.
- **Controls:** fail or warn; no-shrink rules; row-count floors.
- **Contract strictness:** served models, raw contracts, JSON schemas, manifests, sidecars;
  e.g. `additionalProperties: false`, enforced dbt contracts, required fields, enum values.
- **Existing solution before custom logic:** check, in this order, for a built-in dbt
  feature, then an established package (dbt_utils, dbt_expectations, audit_helper,
  Elementary), and only then write custom logic. Record what exists and why it was chosen or
  rejected: fit, adapter support (e.g. DuckDB), dependency cost.

## Contracts consumers depend on

The core hard gate "changes to a contract consumers depend on" means, here, **served models
and their contracts** (and raw contracts read downstream). Changing their shape or values
comes to the human.

## Stacked PRs

The stack's one verification is **one served diff against the top branch**: build the top
branch and compare its served models with `main` (e.g. with audit_helper), posted on the top
PR. Lower PRs link to it rather than posting their own.
