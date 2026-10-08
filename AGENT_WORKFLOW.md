# Agent workflow

The one procedure for building a work item, in every `gisaf22` repo. "Pick up #N" means: run
stages 1–5 below for issue #N, in order. Stage 0 runs earlier, when the story is scoped, and
stage 6 runs when a Feature closes.

The core (sections 1–7) is domain-free. A **domain playbook** adds design checklist items for
one kind of work; it never overrides the core. Playbooks:

- [`playbooks/data-dbt.md`](playbooks/data-dbt.md) — data models, captures, dbt.

Where this file and a repo's `CLAUDE.md` disagree on repo-specific matters (test commands,
branch naming, PR title style), the repo wins; on the procedure itself, this file wins. Board
fields, board commands and other FPL Platform specifics are in the
[project section](#project-fpl-platform-board) at the end.

---

## 1. Principles

- **Decisions before acceptance criteria.** ACs state the behaviour a decision implies, so the
  decision comes first.
- **Vertical slices.** Each story delivers usable value end to end on its own.
- **Tests come from acceptance criteria.** Every test traces to an AC; every AC has a test or
  a recorded manual result.
- **Behaviour, not mechanism.** ACs and checks state what must be true, not how it is built.
- **Verify outcomes, not mechanisms.** Evidence is the observed result (a run, an object, a
  query), not that the code or config looks right.
- **Fail closed or fail loud.** A failure either stops the work safely or is visible to
  someone who will act on it; never silent.
- **One source of truth per fact.** A fact lives in one place and others link to it.
- **Built-in or package before custom.** Use what the platform or an established package
  already does before writing custom logic.
- **Stay inside Out of scope; ask, don't improvise.** A wrong or ambiguous spec is a stop.
- **The record is the issue, not the session.** See *Durability*.
- **The human merges.**

## 2. Lifecycle

Each stage lists when it starts (Entry), what it does, when it is finished (Exit), and when to
stop and ask (Stop).

### Stage 0. Design

**Entry:** a story is being scoped, before its acceptance criteria are written.

1. Search existing issues and PRs, in every repo the story touches, for prior decisions on
   the same topic, plus each repo's `CLAUDE.md` (search commands in the project section).
   Cite each one found next to the decision it settles; a prior decision is settled unless
   the story says why to reopen it.
2. List every contract or schema the change touches and how strict each is (closed schemas,
   required fields, enum values, enforced contracts). A new field in a strict schema is a
   breaking change for that schema.
3. List the story's open design decisions against this checklist, then **apply the playbook
   for the domain**, which adds its own items. Include decisions the spec does not mention;
   an omission is not a decision.
   - **What belongs:** what this change owns, and what belongs to another component.
   - **Missing and edge data:** nulls, absent entities, empty inputs, first and last
     periods, duplicates.
   - **Placement:** where it lives; reuse of something existing rather than a new one.
   - **Contract impact:** does a shape or its values change for a consumer; additive or
     breaking.
   - **Failure visibility:** how a failure of this change shows up, to whom, and whether it
     fails closed or loud. "Nobody would notice" is an open decision. Include the change
     being silently switched off: a missing or default config value that skips a check is a
     failure, not a valid "off" state.
   - **Test tiers and mutations:** which tier each check runs at, and what mutation or
     fixture proves each test can fail.
   - **Infrastructure:** permissions, CI, secrets, inputs.
   - **Existing solution before custom logic:** check for a built-in feature, then an
     established package, and only then write custom logic. Record what exists and why it
     was chosen or rejected (fit, platform support, dependency cost).
4. For each open decision, give the options, the evidence from code or data, and a
   recommendation.
5. Record the decisions in the issue under **Design decisions**, before **Acceptance
   criteria**, with the citations and contract list from points 1–2. **Each decision also
   records the options rejected and why.** A decision the human has not yet made stays
   marked open.

**Exit:** every checklist item is decided or marked open in the issue.
**Stop:** any decision that is open, or that reopens a prior one.

#### Story splitting

Split a Feature into stories that are vertical slices, not layers.

- **Slice 1 is the thinnest end-to-end path that delivers usable value**, even if narrow or
  rough: e.g. one endpoint through detect → record → notify.
- **Later slices widen or deepen:** widen with more cases; deepen with hardening,
  performance or edge cases.
- **Each slice states the value it delivers on its own.** A story that delivers nothing
  usable alone needs a written reason.
- **Plan contract and schema changes across the slices** so that each version bump reflects
  a real, shipped change.
- **After each slice merges, re-check the remaining slices** against what was learned
  before starting the next.

### Stage 1. Pick up

**Entry:** the human says "pick up #N".

1. Read the item and its parent Feature (`gh issue view N --repo gisaf22/<repo>`; the parent
   is in the item's sidebar and its Context section).
2. If the item has no acceptance criteria table, stop: add the `needs-spec` label and ask.
3. Check the item against the stage 0 checklist and the domain playbook. Decisions recorded
   under **Design decisions** are settled; do not reopen them. Ask only on a decision that
   is new: open in the issue, or missing from it. Once answered, add it to the issue's
   Design decisions before continuing.
4. Move the item to **In Progress** (project section).
5. If the work looks bigger than its Size, stop and propose a split: which new items, each
   with its own acceptance criteria. Do not start the larger version.

**Exit:** item In Progress, no open decisions, fits its Size.
**Stop:** no AC table; a new or open decision; bigger than its Size.

### Stage 2. Tests

**Entry:** stage 1 exited.

1. Implement each acceptance criterion as tests at its test tier.
   - Mark each test with `covers("#<issue> AC<n>")`.
   - Test names are plain English and carry no IDs; the ID lives in the marker.
   - An AC may have several test cases (boundaries, negatives, parametrized variants), all
     marked with that AC's `covers` marker.
   - Write tests for the acceptance criteria only. No test for behaviour no AC describes.
   - Test tier `manual` or `e2e (manual)`: no automated test. Record the result on the PR or
     issue instead (stage 4).
2. **Untested branch.** If the implementation needs a branch no AC covers, propose an AC for
   it on the issue (Given / When / Then, test tier) and add its test in the same pass, marked
   with the proposed AC's number. Flag it in the report as proposed; the human removes the AC
   and its test at review if rejected.
3. Commit the tests first, while they still fail.
4. If *Acceleration rules* call for a pause, stop and report for approval: which ACs the
   tests cover, how they fail, and any proposed ACs. Do not implement until approved.

**Exit:** failing tests committed; pause cleared or not required.
**Stop:** a pause applies.

### Stage 3. Implement

**Entry:** stage 2 exited.

1. Make the tests pass.
2. Stay inside the item's Out of scope. Anything listed there belongs to another item.
3. If the spec is wrong or ambiguous, stop and ask. Do not improvise around it.

**Exit:** all automated tests pass.
**Stop:** spec wrong or ambiguous; the work needs something Out of scope.

### Stage 4. PR

**Entry:** stage 3 exited.

1. Title per the repo's convention (its `CLAUDE.md` and recent PRs).
2. Body says `Closes #N`. For an item in another repo, use `Closes gisaf22/<repo>#N`. When
   one item spans several PRs, only the PR expected to merge last says `Closes`; the others
   say `Part of gisaf22/<repo>#N`.
3. Post the AC results as a PR comment: pass/fail per AC, with evidence (test output, a
   command and its result, a link). Proposed ACs are listed separately as proposed.
4. Tick the Definition of Done in the PR body. Mark any item that doesn't apply
   "N/A because…".
5. **Stacked PRs.** When PRs build on each other, each PR's base is the PR below it and its
   body names the stack in merge order. The stack gets **one verification for the stack**:
   the checks and AC evidence run once against the top branch and are posted on the top PR;
   lower PRs link to it. The `Closes` rule in point 2 applies across the stack. The domain
   playbook may say what that verification is.
6. Never merge. The human merges.

**Exit:** PR open with AC results and DoD.
**Stop:** always — the human merges.

### Stage 5. Close

**Entry:** the human has merged.

1. Confirm the item is closed and its Status is **Done**; set it if the board didn't.
2. Unblock dependents: for each item the closed item was blocking, if it now has no other
   open blockers and its Status is **Blocked**, move it to **Todo**. An item with another
   open blocker stays Blocked.
3. Post-merge checks (a scheduled run to watch, an output to inspect, a field to confirm) go
   on the PR or issue as a Markdown checklist: what to check, when it can first be checked,
   and how. Any later session picks it up, runs the checks (read-only, per *Boundaries*),
   ticks each item with its evidence, and closes the loop.
   - **Too early to check:** report it in one line — what, and the earliest time it can be
     checked (absolute, with time zone). Don't wait, poll, or set timers.
4. Clean up local state in every repo you worked in: remove the item's worktrees
   (`git worktree remove <path>`) and delete its merged local branches
   (`git branch -d <branch>`).
5. If the item is a slice of a Feature, re-check the Feature's remaining slices per
   *Story splitting*, and record any changes on the Feature.

**Exit:** Done, dependents unblocked, checklist posted, local state clean.

### Stage 6. Learn

**Entry:** a Feature closes.

1. Post a short retro as a comment on the Feature:
   - **Round trips:** where work waited on the human, and why.
   - **Rework:** what was redone, and what caused it.
   - **Surprises:** what the spec, a design decision or the code got wrong.
2. Turn each finding into one of:
   - a **rule change PR** to this repo, when it applies to any work;
   - a **fact in the repo's `CLAUDE.md`**, when it is about one repo.
   Link each PR or commit from the retro comment. A finding that becomes neither says why.

**Exit:** retro posted; every finding linked to its change or reason.

## 3. Boundaries

**The agent does alone:** read-only checks, run and reported rather than handed to the human.
That covers `gh` queries, `grep` and reading code, reading workflow runs and their logs,
listing or reading objects, and dry runs. Report the command and what it showed.

**Hard gates — only these come to the human:**

- merges;
- live writes (to a bucket, a warehouse, a board in bulk, or any production data);
- permission and access changes;
- workflow dispatches that write (anything that captures, builds or deploys);
- changes to a contract consumers depend on.

## 4. Reporting

- Lead with the result: what is done, what failed, what is blocked.
- AC results are pass/fail per AC with evidence.
- **Every command for the human is runnable as written:** real repo names, issue numbers,
  paths and values, no placeholders. The human should be able to paste it.
- Name any hard gate waiting on the human.
- **End every report with a 3-line plain-language summary, then "Decision needed: …"** (or
  "Decision needed: None").

## 5. Durability

- State lives on issues and PRs — design decisions, AC results, post-merge checklists,
  retros — never in session memory, timers or reminders. Any later session must be able to
  continue from them alone.
- Commit failing tests before implementing, so they survive the session.

## 6. Acceleration rules

When stage 2's pause applies:

- **Size XS, every AC manual:** no pause; go straight to stage 3. The PR is the review point.
- **Size S:** pause only if a design decision was open or new at pick-up (stage 1, point 3),
  even once the human has answered it. Otherwise no pause: commit the failing tests,
  implement, and open the PR.
- **When a pause applies and every AC is manual**, there are no failing tests to commit, so
  report a verification plan instead: what will change, and how each AC will be checked.

## 7. Creating items

- Run stage 0 before writing the acceptance criteria.
- Use the templates in [`gisaf22/.github`](.github/ISSUE_TEMPLATE/): User Story (full) for
  anything larger than XS, User Story (light) for XS.
- Create with `--body-file`: copy the template body (without its front matter) to a file,
  fill it in, then `gh issue create --repo gisaf22/<repo> --title "…" --body-file <file>`.
- Set the board fields and dependencies (project section).

Writing acceptance criteria:

- **Given / When / Then, always.** Every AC names its trigger in When. For a static check
  (a doc, a config), When is the act of inspecting it ("when the section is read").
- **Behaviour, not mechanism.** An AC states the required behaviour, never the
  implementation: "a misspelled marker fails collection", not "set `--strict-markers` in
  `addopts`". An AC that prescribes a mechanism can be unmeetable while the behaviour is
  fine (fpl-ingest#22 AC2: `--strict-markers` in `addopts` only warns on pytest 9).
- **Every edge case is tested.** Each entry in a spec's Edge cases section is either its
  own AC or listed as an example under an existing AC. An edge case never stands alone.

---

## Project: FPL Platform board

Everything below is specific to the
[FPL Platform board](https://github.com/users/gisaf22/projects/3) and the `gisaf22` repos.

### Board fields

**Status** (Todo · In Progress · Blocked · Done), **Work Item Type**, **Epic**,
**Size** (XS · S · Split needed), **Priority** (P0 Now · P1 Next · P2 Later · P3 Someday).
Dependencies are GitHub's native *blocked by* / *blocking* issue links, not text in the body.

- New items: set Work Item Type, Epic, Size, Priority, Status, and the parent issue; add
  blocked-by links where the item depends on another.
- **Every new item gets a Priority**, P0–P3 (each option's description on the board says
  when it applies). At most 3 P0 items may be open at once: to add a fourth, lower one first
  or ask.
- **"What's next" means the highest-priority Todo item.** Among equal priorities, take the
  one highest in the board's Todo column.

### Prior-decision search (stage 0)

```sh
gh search issues "<topic>" --owner gisaf22
gh search prs "<topic>" --owner gisaf22
```

Cite each hit as `gisaf22/<repo>#N`.

### Public board

The board and all repos are public. No account IDs, ARNs or secrets in any issue, PR or
comment. Describe the resource ("the capture bucket", "the deploy role") instead.

### Board commands

Look IDs up by name each time; don't hard-code them.

```sh
PROJECT_ID=$(gh project view 3 --owner gisaf22 --format json --jq .id)
STATUS_FIELD=$(gh project field-list 3 --owner gisaf22 --format json \
  --jq '.fields[] | select(.name=="Status") | .id')
status_option() {  # status_option "In Progress"
  gh project field-list 3 --owner gisaf22 --format json \
    --jq ".fields[] | select(.name==\"Status\") | .options[] | select(.name==\"$1\") | .id"
}
item_id() {        # item_id <repo> <number> -> the issue's board item ID
  gh api graphql -f query="{repository(owner:\"gisaf22\",name:\"$1\"){issue(number:$2){
    projectItems(first:10){nodes{id project{number}}}}}}" \
    --jq '.data.repository.issue.projectItems.nodes[] | select(.project.number==3) | .id'
}
set_status() {     # set_status <repo> <number> "Todo"
  gh project item-edit --project-id "$PROJECT_ID" --id "$(item_id "$1" "$2")" \
    --field-id "$STATUS_FIELD" --single-select-option-id "$(status_option "$3")"
}
```

Dependents of a closed item, with their open blockers and Status (stage 5):

```sh
gh api graphql -f query='{repository(owner:"gisaf22",name:"<repo>"){issue(number:<N>){
  blocking(first:50){nodes{number repository{name}
    blockedBy(first:50){nodes{number state}}
    projectItems(first:10){nodes{project{number}
      fieldValueByName(name:"Status"){... on ProjectV2ItemFieldSingleSelectValue{name}}}}}}}}}'
```

A dependent is ready when every `blockedBy` node is `CLOSED`; if its Status on project 3 is
`Blocked`, run `set_status <its repo> <its number> "Todo"`.
