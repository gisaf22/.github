# Analysis workflow

The procedure for an analysis (a decision to evaluate), in every `gisaf22` repo. Building the
pieces still follows [AGENT_WORKFLOW.md](AGENT_WORKFLOW.md).

**The rule.** Every analysis is one `CARD.md` in its decision folder, opening with the status line
`Stage N — <name>. Gates 0..N-1 passed.` Decisions and their reasons live as comments on the
Epic/Feature issue; the card links them and never copies them. A superseded doc moves to
`archive/` with the header `Superseded by CARD.md on <date>.`

**Board mapping.** Epic = one decision (one card). Feature = one stage, with a done-check.
Story = a buildable piece, size ≤ S, with ACs.

## Stages

| # | Stage | Question | Must answer | Gate |
|---|---|---|---|---|
| 0 | Decision | What are we deciding? | Who decides; the action; chosen from what; when, and what is known then; goal; why it matters / cost of being wrong; constraints; in/out of scope; assumptions; frequency | Goal and scope stated |
| 1 | Metric | How do we score it? | Primary, secondary, sensitivity; how results combine; what is good / acceptable; options considered and why rejected; why it equals the goal | One primary metric equal to the goal |
| 2 | Data | What can we use? | Source and version; known-when (leakage); population | Leakage rules and population defined |
| 3 | Explore | What do simple rules do? | Simple unfitted baselines chosen from the decision's levers, one idea per rule; EDA on the exploration data only | Rule definitions frozen before running; claims descriptive only |
| 4 | Decide | Is there room to improve? | Headroom (best baseline vs ideal); failure patterns → hypotheses | Go/no-go with reason, against a threshold set before exploring |
| 5 | Confirm | Does it hold on unseen data? | Unseen = later data, by time. Pre-registered = written and committed before looking: hypothesis, metric, comparison, threshold, sample size, method, action per outcome | Pre-registered; separate data |
| 6 | Promote | What ships to the warehouse? | Confirmed winners only; features and raw facts go to the warehouse, models stay in the consumer | Parity shown |
| 7 | Model (optional) | Can a model do better? | Only if confirmed headroom remains; simplest first | Beats the best baseline on unseen data |
| 8 | Deliver | How is it used and watched? | Output; live monitoring; feedback into the next confirmation; records (card, issues, `results/`) | Lessons recorded |

## Always

- Run only committed code, and record which code and which data each run used.
- Write the rules before looking at the results.
- Test on data you did not explore.
- Build test samples from real data.

## Worked example — Starting XI

0. Pick the legal 11 from a settled 15 before the deadline, to maximise expected points; captaincy and Free Hit out.
1. Primary: XI regret (chosen XI with auto-subs vs best legal 11 without subs); secondary: bench-order regret.
2. Warehouse served v2; each rule sees only data known at the deadline; exploration season 2025-26.
3. Grid of points-averages (four denominators × season / last 3) on synthetic squads.
4. Best baseline's regret vs zero; go only if headroom clears the pre-set threshold.
5. Pre-register, then run on 2026-27 real managers' squads, against their own XI choices.
6. Promote the winning feature to the warehouse; show parity with the analysis copy.
7. Skip unless confirmed headroom remains.
8. Weekly XI recommendation; track live regret; record lessons in the card.
