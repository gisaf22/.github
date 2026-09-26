# Agent workflow — FPL Platform board items

The one procedure for building an item from the
[FPL Platform board](https://github.com/users/gisaf22/projects/3), in every `gisaf22` repo.
"Pick up #N" means: run steps 1–5 below for issue #N, in order.

Each repo's `CLAUDE.md` has a short "Working on board items" section that points here and
repeats the must-follow rules. Where this file and a repo's `CLAUDE.md` disagree on
repo-specific matters (test commands, branch naming, PR title style), the repo wins; on the
procedure itself, this file wins.

Board fields: **Status** (Todo · In Progress · Blocked · Done), **Work Item Type**, **Epic**,
**Size** (XS · S · Split needed), **Priority** (P0 Now · P1 Next · P2 Later · P3 Someday).
Dependencies are GitHub's native *blocked by* / *blocking* issue links, not text in the body.

---

## 1. Pick up

1. Read the item and its parent feature (`gh issue view N --repo gisaf22/<repo>`; the parent
   is in the item's sidebar and its Context section).
2. If the item has no acceptance criteria table, stop: add the `needs-spec` label and ask.
   Do not start work.
3. Move the item to **In Progress** on the board (commands under *Board commands*).
4. If the work looks bigger than its Size, stop and propose a split (which new items, each
   with its own acceptance criteria). Do not start the larger version.

## 2. Tests

1. Implement each acceptance criterion as tests at its test tier.
   - Mark each test with `covers("#<issue> AC<n>")`.
   - Test names are plain English and carry no IDs; the ID lives in the marker.
   - An AC may have several test cases (boundaries, negatives, parametrized variants), all
     marked with that AC's `covers` marker.
   - Write tests for the acceptance criteria only. A test for behaviour no AC describes is
     not allowed: if you think one is missing, flag it in your report; do not add it.
   - Test tier `manual` or `e2e (manual)`: no automated test. Record the result on the PR
     or issue instead (step 4).
2. Commit the tests first, while they still fail.
3. Stop and report for approval: which ACs the tests cover, how they fail, and any flagged
   gaps. Do not start implementing until approved.

When to pause at step 3:

- **Size XS, every AC manual:** skip the pause and go straight to step 3 (Implement); the
  PR is the review point.
- **Size S:** always pause. If every AC is manual there are no failing tests to commit, so
  report a verification plan instead: what will change, and how each AC will be checked.

## 3. Implement

1. Make the tests pass.
2. Stay inside the item's Out of scope. Anything listed there belongs to another item.
3. If the spec is wrong or ambiguous, stop and ask. Do not improvise around it.

## 4. PR

1. Title per the repo's convention (see its `CLAUDE.md` and recent PRs).
2. Body says `Closes #N`. For an item in another repo, use `Closes gisaf22/<repo>#N`; when
   one item spans several PRs, only the PR expected to merge last says `Closes`, the others
   say `Part of gisaf22/<repo>#N`.
3. Post the AC results as a PR comment: pass/fail per AC, with evidence (test output, a
   command and its result, a link).
4. Tick the Definition of Done in the PR body. Mark any item that doesn't apply
   "N/A because…".
5. Never merge. The human merges.

## 5. Close

After the human merges:

1. Confirm the item is closed and its Status is **Done** (the board's *Item closed*
   workflow sets this; if it didn't, set it).
2. Unblock dependents: for each item the closed item was blocking, if it now has no other
   open blockers and its Status is **Blocked**, move it to **Todo**. An item that still has
   an open blocker stays Blocked.
3. Clean up local state in every repo you worked in: remove the item's worktrees
   (`git worktree remove <path>`) and delete its merged local branches
   (`git branch -d <branch>`).

---

## Creating items

- Use the templates in [`gisaf22/.github`](.github/ISSUE_TEMPLATE/): User Story (full) for
  anything larger than XS, User Story (light) for XS.
- Create with `--body-file`: copy the template body (without its front matter) to a file,
  fill it in, then `gh issue create --repo gisaf22/<repo> --title "…" --body-file <file>`.
- Then set, on the board: Work Item Type, Epic, Size, Priority, Status, and the parent
  issue; add blocked-by links where the item depends on another.
- **Every new item gets a Priority**, P0–P3 (each option's description on the board says
  when it applies). At most 3 P0 items may be open at once: to add a fourth, lower one
  first or ask.
- **"What's next" means the highest-priority Todo item.** Among equal priorities, take the
  one highest in the board's Todo column.

Writing acceptance criteria:

- **Given / When / Then, always.** Every AC names its trigger in When. For a static check
  (a doc, a config), When is the act of inspecting it ("when the section is read").
- **Behaviour, not mechanism.** An AC states the required behaviour, never the
  implementation: "a misspelled marker fails collection", not "set `--strict-markers` in
  `addopts`". An AC that prescribes a mechanism can be unmeetable while the behaviour is
  fine (#22 AC2: `--strict-markers` in `addopts` only warns on pytest 9).
- **Every edge case is tested.** Each entry in a spec's Edge cases section is either its
  own AC or listed as an example under an existing AC. An edge case never stands alone.

## Public board

The board and all repos are public. No account IDs, ARNs or secrets in any issue, PR or
comment. Describe the resource ("the capture bucket", "the deploy role") instead.

---

## Board commands

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

Dependents of a closed item, with their open blockers and Status (step 5):

```sh
gh api graphql -f query='{repository(owner:"gisaf22",name:"<repo>"){issue(number:<N>){
  blocking(first:50){nodes{number repository{name}
    blockedBy(first:50){nodes{number state}}
    projectItems(first:10){nodes{project{number}
      fieldValueByName(name:"Status"){... on ProjectV2ItemFieldSingleSelectValue{name}}}}}}}}}'
```

A dependent is ready when every `blockedBy` node is `CLOSED`; if its Status on project 3 is
`Blocked`, run `set_status <its repo> <its number> "Todo"`.
