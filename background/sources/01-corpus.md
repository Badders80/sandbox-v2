# Corpus — rates, decay, domain clusters

Source: `audit/corpus-deep-dive/out/report.md` (scored 2026-10-08) and `findings.json` (73 sessions). The report's percentage column is sessions affected. The raw signal totals inside `findings.json` are higher because one session carries many signals. Both are recorded. Do not mix them.

## Report table (sessions affected)

73 scored sessions. Two zero-signal Codex plan-review sessions sit in the denominator. The report's own sum of the nine sub-report batches is 71 scored plus those two.

| Antipattern | Baseline | Observed | Delta | Report count |
|---|---|---|---|---|
| correction | 60% | 89% | +29 | 238 |
| redo | 61% | 92% | +31 | 251 |
| scope-drift | 59% | 88% | +29 | 248 |
| rework-loop | 38% | 42% | +4 | 82 |
| frustration | 49% | 53% | +4 | 112 |
| time-sink | 44% | 91% | +47 | 279 |

Report §1: time-sink and redo are 530 of 1,371 signals in the sub-report tables (39%). The fourth sub-report says all 9 of its rework-loop instances were genuine defect catches (dead tests, fixture realism, animation class omission). The structural rates moved. The symptom rates did not.

Report §2 mechanism, in the report's words: redo clusters around verification. About 10 of 26 redo signals in the fourth sub-report are re-runs of already-passing gates. The third sub-report: across 5 sessions, agents burned turns fixing their own test harnesses.

`docs/plans/RUNBOOK.md` acceptance test, written the same day: correction / redo / scope-drift / time-sink should fall from 88–92% toward 59–61%. Rework-loop 42% and frustration 53% should hold or improve. `CONTINUE.md` schedules the re-score for about 2026-10-22. Orca, which the report's §5 verdict was amending, was removed 2026-10-13.

## Raw signals in findings.json

Counted 2026-10-14 by walking every `signals[]` entry. These are occurrences, not sessions affected.

| Type | Occurrences |
|---|---|
| time-sink | 507 |
| scope-drift | 459 |
| correction | 453 |
| redo | 430 |
| orchestration-failure | 255 |
| frustration | 164 |
| rework-loop | 130 |

Each signal is `{type, quote, chunk}`.

Domain keyword hits inside those quotes (a quote can hit more than one theme):

| Theme | Hits |
|---|---|
| verification harness (regex, gate, auditor, false pass) | 103 |
| scope / parking / stale CONTINUE | 93 |
| identity, slug, alias, microchip, rename | 51 |
| price, float, split, stake, 75% | 49 |
| concurrency (index.lock, sibling, port 3000, .next) | 44 |
| tokens / theme | 34 |
| PDS / SA / legal_engine | 32 |
| migration / 00003 / supabase | 31 |
| seven-repo / evo_0 / package | 16 |
| content registry, by that name | 1 |

The registry barely appears under its own name. It shows up as a path bug: "Path resolution bug in my own test — `engine/content` sits outside the worktree" (2026-09-15, rework-loop).

## Decay quotes the report pins (2026-09-08 → 09-28)

- `hermes/2026-09-09/decide-pds-voice-paragraph-caps` — "you've jsut cost me an hour or so fucking around." 36 signals, 15 redo, 8 frustration, 3 rework-loop.
- `hermes/2026-09-11/file-hermes-attachments-step-6-holding-html` — quality 2. The attachment path was outside the workspace, so the agent refused and the user re-attached.
- `hermes/2026-09-11/hello-5` — "I've been thrashing." Kill-relaunch emptied port 3000.
- `hermes/2026-09-12/locked-pushed-464a70d` — the auditor rated `b08ffec` PASS. That commit broke settlement.
- `hermes/2026-09-15/bump-claude-cap-and-run-harvest` — "muse audits, then muse executes." All 291 outcomes came back `unknown`.
- `hermes/2026-09-16/review-parking-folder-and-group-items` — a sibling session took `G020` 68 minutes earlier. The commit swept in the sibling's `G022-website-polish` files.
- `hermes/2026-09-18/run-morning-review-and-alignment-protocols` — "the discipline was dormant — zero kimi hits." "if we audit, the same model that did the execution can NEVER be the auditor."
- `hermes/2026-09-20/wrap-session-and-assess-toolbox-memory` — "Three FAILs during this run were all my harness's errors, not the code's." The harvester dirties the tree it is checking.

Report §3 causal story, condensed: verification becomes the work, audit discipline collapses into self-review, orchestration collisions compound it. User frustration is the terminal symptom. It spikes where scope-drift and time-sink co-occur (09-09, 09-13, 09-20).

The clean counter-example in the report is `grok-cli/2026-09-21/01a0c3d0`: a pinned kimi plan-audit caught four real blockers before coding.

## Domain quotes pulled from findings.json

Identity:

- 2026-09-10 correction: Manolo is already flipped live. The remaining `coming_soon` are nellie and mulan.
- 2026-09-12 scope-drift: a loveracing breeding URL for Yearn-NZ-2013-2023, plus a microchip, offered as Mulan's identity.
- 2026-09-16 correction: "My alias check was scoped wrong — it flags targets in other projects." The same session: the alias grouping substring-matched `evolution-3-0`.

Price and split:

- 2026-09-09 scope-drift: the DSL entry window would not allow 75/25 until 80/20 had been chosen.
- 2026-09-10 redo: "Local DB is a stale mirror (old seed: 6500 → $71/$355) vs prod's DSL values (7000 → $76/$380)."
- 2026-09-10 redo: whole-pack compile at a stake breaks the test that `pdsHash` is identical across stakes.
- 2026-09-10 rework-loop: marketplace still `coming_soon` because the dev server reads local Supabase and the flip wrote to prod.

Legal:

- 2026-09-09 correction: walk the DSL generator through PDS and SA.
- 2026-09-09 scope-drift: the original PDS has the 3x payout, and both mechanics have to come from the DSL generator.
- 2026-09-10 scope-drift: "add each horse's hero image from the website to the PDS" — asked as if it were a local edit.

Harness, which multiplied the five domain causes:

- 2026-09-18 rework-loop: "my own nav guard was a denylist with a blind spot — its regex refused any match containing `$`."
- 2026-09-25, session `3b93ad5c` summary: three open audit items, a retry patched three times, repeated prose-matching guard failures. "~400k for 2 tiny commits + 1 dead walk" is the report's cost line for that session.
- 2026-09-23 `h6-design-pass`: "I got the branch guard wrong five times" is the neighbouring session the report cites (`2026-09-22/hermes-watcher-and-wt-folder`). The design-pass summary: duplicate audit launches, `pkill -f` matching its own shell, "the gate's closing line I shipped is false."

## Highest correction+redo+rework sessions

Score is correction + redo + rework-loop occurrence counts.

| Score | Date | Source | Title | Summary line |
|---|---|---|---|---|
| 61 | 2026-09-22 | claude | hermes-watcher-and-wt-folder | Branch guard wrong five times. Report: a `git stash` deleted uninspected. |
| 45 | 2026-09-18 | claude | 04879d39 | Shipped the 6-step flow, then a production KYC regression the user caught. |
| 35 | 2026-09-23 | claude | visual-and-token-system-audits | Five admitted over-claims. A background re-audit was killed. |
| 34 | 2026-09-23 | claude | h6-design-pass | Wrap across three repos. A kimi audit killed after ~25 minutes with zero output. |
| 33 | 2026-09-25 | claude | 3b93ad5c | Prose-matching guard patched three times. The underlying rule stayed open. |
| 28 | 2026-09-30 | claude | account-gate-planned-jobs | A background inspection was invalidated because the host edited the repo mid-run. |
| 26 | 2026-10-03 | claude | audit 5a3266e3 | Confirmed a `skipValidation` bug, then a context-window error stopped the session. |

`skipValidation` is a real product hole, not only a harness hole. `packages/legal_engine/tests/p049_buyer_term.test.ts` calls `compileLegalPack(..., { skipValidation: true })`. The 2026-10-03 website commit `9996e33` is `fix(audit): Chunks 1, 2D, 4 — draft filter, validation enabled, internal checkout fn`.
