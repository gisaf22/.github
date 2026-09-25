# .github

Default community health files for `gisaf22` repositories.

## Issue templates

`.github/ISSUE_TEMPLATE/` holds two markdown templates, offered in the "New issue"
chooser of every `gisaf22` repo that has no `.github/ISSUE_TEMPLATE/` of its own:

| Template | Use for | Sections |
|---|---|---|
| **User Story (full)** | Any story larger than XS | User story · Context · Acceptance criteria · Edge cases · Out of scope · Observability · Definition of Done |
| **User Story (light)** | XS stories | User story · Acceptance criteria (1–3 rows) · Out of scope · Definition of Done |

- **User story** is "As <consumer>, I want <capability>, so that <outcome>"; the consumer is a system or repo.
- **Context** names the parent Epic and Feature issues, then related links.
- **Acceptance criteria** is a table with columns `#` (AC1, AC2, …) · Given / When / Then ·
  Why it matters · Fails if · Test tier (unit / integration / e2e / manual).
- **Definition of Done** is "all acceptance criteria pass" plus the baseline checklist:
  tests written first · CI green · failure path observable · docs/ADR updated if behaviour or
  a decision changed · no contract change without a compatibility note. Mark any item that
  doesn't apply "N/A because…".
- Templates are markdown, not YAML forms: agents create issues with `gh`, which skips forms.
  Agents copy the template body to a file, fill it in, and create with `--body-file`
  (see [AGENT_WORKFLOW.md](AGENT_WORKFLOW.md#creating-items)).
- Templates set no labels; set the Epic and Work Item Type fields on the board per item.
- A repo that adds its own `.github/ISSUE_TEMPLATE/` stops receiving these defaults.

## Agent workflow

[AGENT_WORKFLOW.md](AGENT_WORKFLOW.md) is the procedure agents follow for every FPL Platform
board item: pick up → tests → implement → PR → close. Each repo's `CLAUDE.md` links to it.

## Facts

- All repos are public; GitHub Actions is free on standard runners. Revisit if any repo goes private.
