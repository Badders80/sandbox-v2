# Legal copy and the content registry

## Why CI does not test the compiler

`evolution/engine/website/.github/workflows/check.yml` lines 171–194, as read 2026-10-14:

> `@evo/legal_engine` is excluded for one reason, and it is named here rather than left as a quiet red: its `soft-prefill` suite reads the content registry at `engine/content`, which is 26MB and lives OUTSIDE this repo (unversioned). CI has no checkout of it, so three of its assertions passed vacuously against an empty registry. That is worse than not running, so it is not run.

The same job warns `P023 partial gate` and runs:

```
pnpm turbo run test --filter='!@evo/legal_engine'
```

`ci.yml` has the same filter. Three further suites (`vocabulary_lock_copy.test.ts`, `purchase_operator_alert.test.ts`, `send_surface_register.test.ts`) are skipped unless `SSOT_TOKEN` checks out `evo_00`. A missing token keeps the job green and prints a warning.

On 2026-10-14, `/home/evo/evolution/engine/content` is 52 MB and a git repo with 7 commits. Head: `f9da7c2` 2026-09-30 `feat(prudentia): bind Q1/Q2 report fields on the 2026-q2 distribution`. The CI comment, `DoD.md` O6, and `HORSE-IDENTITY-NAMING-LAW.md` still say ~26 MB and, in the law's original table, "in no git repo." The size and the git-ness both drifted. The coupling did not. The compiler's default path is still a directory outside the website repo.

## The default path

`packages/legal_engine/src/soft-prefill.ts` lines 1–16:

```
Soft-content pre-fill — composes the 4 PDS soft sections from engine/content
(same registries that feed the website) + inventory hard facts.

Rule: DB (soft_legal) always wins when present. This only drafts blanks.
Never invents: unknown horse/section → '' (blank placeholder downstream).

export const CONTENT_DIR =
  process.env.SOFT_CONTENT_DIR || '/home/evo/evolution/engine/content';
```

`SOFT_CONTENT_MIN` requires at least 5 horse folders, 1 stable, 2 trainers, and 5 pedigrees. `assertContentPresent` throws if a directory is missing or short. The CI comment says that before the exclusion, three assertions passed against an empty registry anyway. An empty-directory pass is the failure mode `seed.sql` lines 84–86 also describe for an `update` that matches zero rows.

The four sections this file composes: `aboutHorse`, `trainerBio`, `racingOutlookAndPedigree`, `raceExpectation`. `OUTCOME.md` keeps three of those as columns on the horse (`aboutHorse`, `racingOutlookAndPedigree`, `raceExpectation`). The trainer bio is the trainer's bio. The SA reads no soft prose.

## Slug resolution by scanning markdown

`resolveHorseSlug` (lines 89–140) exists because of G018 on 2026-09-16. The comment:

> the locked convention says the folder and slug are the OFFICIAL name, and that a barn name is a nickname — display-only, never a folder. But people type the barn name, and the app's own slug (`inventory.slug`) does not always match the content folder. Before this, the lookup was a bare path join, so a barn name or a prod slug simply produced a MISSING folder and the caller got silent blanks.

The scan reads every `horses/*/profile.md`, compares folder name plus front matter `slug`, `official_name`, `barn_name`, `breeding_name`, and `name`, and also an `aliases:` list. The comment's example is `aliases: [tml-x-yearn]`, "because nothing about Turn Me Loose x Yearn 2023 produces the string tml." Unknown horses return the input unchanged, so a blank and a typo look the same. The comment calls silent blank legal copy the failure that should never be quiet, then preserves the blank fallback for an unknown horse.

That is a third alias map, stored in markdown front matter, outside the website repo, unread by CI.

## DoD open items this closes

| Item | What it said | Where the outcome put it |
|---|---|---|
| O6 / A1 | Porting `legal_engine` frozen ports the 26 MB registry (`soft-prefill.ts`). Resolve before PDS/SA render. | `PLAN.md` ruling 2 and the parked table: not a requirement. Soft text is three columns on the horse. |
| A3 | "Files are fixtures, never authority" contradicts the legal `.docx` kept in-repo. | `PLAN.md` Legal authority: the PDF, the three pro-formas, and a principles note are the read-only set. Horse markdown is not authority. The Word files are not filled in. |
| A6 | The horse-content seam (updates, highlights, media, audience scope) is under-specified, so every update relitigates its shape. | `PLAN.md` ruling 6: the first slice stores the three soft columns and the race record as one jsonb blob. No second content stream. |
| A8 | "Ported frozen, do not rewrite" conflicts with "prune what is not needed." | `PLAN.md` step 7: do not port `legal_engine`. The compiler writes markdown from the horse, the trainer, and the DSL, and self-audits against the principles note. |

`DoD.md` line 79 lists what "frozen" included: compiler, validator, pds, sa, term_sheet, commercial-rules, soft-prefill, pricing, settlement, pdf. Pricing numbers are copied as a small module. The package is not.

## The reference set, from PLAN.md

Not copied into this folder. Paths and the rule:

1. Rules of Racing PDF, effective 21 September 2026. URL in `PLAN.md`. The check uses the Second Appendix, Bloodstock Syndication Code of Practice, made under Rule 432. The Code text is marked amended 1 January 2016. The other pages of the 209-page PDF are not part of the compiler check.
2. `evolution/engine/website/packages/legal_engine/reference/nztr-pro-forma/` — three `.docx` files. Fixed Monthly Contribution matches this product. Monthly Invoiced stays so the $365 flat manager fee stays visible as a difference.
3. A principles note written beside those files at build. Each line cites the clause or the form it came from.

Four locked differences, so a later session does not "correct" the compiler back to the form:

- Stakes are 1% with the owner's minimum and step. The pro-forma's 5% example is not this product.
- Forfeiture is the printed procedure in the outcome. The form's 14-day remedy, then sale at Fair Value less 20%, is not this product.
- The fee is 5% and 3% inside the listed price. The Monthly Invoiced form's $365 flat manager fee is not this product.
- The investor split is the number on that DSL. An empty form shows 75%. That is not a horse's split. The 16% statutory deduction is not the investor's return math.

Disclosure sections the note must carry, or dropping one is a red flag, are listed in `PLAN.md` Legal authority. They are not restated here so the list cannot fork.

## Hashes and skipValidation

Issued documents in V1 are frozen by hash (`DoD.md`: `legal_engine` SHA-256). `inventory` requires `pds_hash`, `sa_hash`, `pds_url`, and `sa_url` as `NOT NULL` even for a horse that is not yet sold (`00001` lines 143–146). A draft row cannot exist without document URLs. `OUTCOME.md` moves the frozen markdown and both hashes onto the sale, written by the webhook. A horse edit does not change a document already sold.

`packages/legal_engine/tests/p049_buyer_term.test.ts` compiles with `{ skipValidation: true }`. The 2026-10-03 corpus session confirmed a `skipValidation` bug. Website commit `9996e33` the same day: `fix(audit): Chunks 1, 2D, 4 — draft filter, validation enabled, internal checkout fn`. The flag is a way for a test, or a caller, to ship a document the validator did not see.

## Corpus

2026-09-15 rework-loop: "Path resolution bug in my own test — `engine/content` sits outside the worktree."

2026-09-09: the user had to re-state that the walk is DSL → PDS → SA, and that the 3x payout mechanic has to come from the generator rather than from an older PDS.

2026-09-10: two turns asking the agent to lock a format pass (KIS + bold rule) across PDS and SA before applying it. Format was being relitigated as if it were a commercial term.

2026-09-25 `3b93ad5c`: a prose-matching guard failed three times in one day. The report's line is "Third prose-matching miss today." The legal contract cannot be a regex over the generated paragraph. It is the principles note plus the column checks.
