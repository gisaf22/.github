# .github

Default community health files for `gisaf22` repositories.

## Issue templates

`.github/ISSUE_TEMPLATE/` holds two markdown templates, offered in the "New issue"
chooser of every `gisaf22` repo that has no `.github/ISSUE_TEMPLATE/` of its own:

| Template | Use for | Sections |
|---|---|---|
| **Item (full)** | Anything larger than XS | Feature · Context · Scenarios · Edge cases · Out of scope · Observability · Done when |
| **Item (light)** | XS items | Feature · Scenarios (1–3 rows) · Out of scope · Done when |

- **Scenarios** is a table with columns `#` · Scenario · Why it matters · Fails if · Tier
  (unit / integration / e2e / manual). Each scenario is Given / When / Then.
- **Done when** is "all scenarios pass" plus the baseline checklist: tests written first ·
  CI green · failure path observable · docs/ADR updated if behaviour or a decision changed ·
  no contract change without a compatibility note. Mark any item that doesn't apply
  "N/A because…".
- Templates are markdown, not YAML forms: agents create issues with `gh`, which skips forms.
  Example: `gh issue create --repo gisaf22/fpl-ingest --template "Item (full)"`.
- Templates set no labels; add the phase label per item.
- A repo that adds its own `.github/ISSUE_TEMPLATE/` stops receiving these defaults and
  needs its own copies.

## Facts

- All repos are public; GitHub Actions is free on standard runners. Revisit if any repo goes private.
