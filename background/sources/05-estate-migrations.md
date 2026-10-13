# The seven repos and the duplicate migrations

## The estate, from DoD.md lines 278–293

The engine is not one repository. It is seven sibling repos, one per folder:

| DoD name | Folder | Commits on 2026-10-14 |
|---|---|---|
| `evo_00` SSOT | `evolution/engine/SSOT` | 104 |
| `evo_02` website, the live app | `evolution/engine/website` | 1028 |
| `evo_03` studio | `evolution/engine/studio` | 18 |
| `evo_04-content` | `evolution/engine/content` | 7 |
| `evo_05-brand` | `evolution/engine/brand` | 29 |
| `evo_06-comms` | `evolution/engine/comms` | 36 |
| `evo_07-infra` | `evolution/engine/infra` | 4 |

On top of those, `DoD.md` records a second GitHub naming set that duplicates the first: `01_evolution`, `02_website`, `03_studio`, `04_comms`, `06_content-pipeline`, `SSOT`, plus `Evolution-3.1`, `workspace`, `html`, `prototype`, `tik_tok`, `05_industry-data`, and `evo_01` (the old live prod source). The visibility count "15 of 20 public" is dated 2026-09-14 in that file and is marked stale. A11 says re-verify it. Do not quote it as current.

The decision in that section: v2 is one repo, root `engine_v2/website`. The empty `engine_v2/{brand,infra,SSOT}` directories are placeholders. `OUTCOME.md` closed the leftover option (O7) of putting the git root at `engine_v2/` level. `v2/README.md`: do not `git init` the parent. The parent keeps `OUTCOME.md`, `CONTINUE.md`, and `DoD.md`.

A12 in `DoD.md` is the same point, resolved the same way. A11 also records that one audit artefact, `t7_github.json`, claims `apps/mission_control` exists. It does not. `apps/` contains only `web/`. The `check.yml` comment says CI kept writing an env file for that app until it was deleted 2026-09-24.

## What "pushed" meant in a session

2026-09-13 corpus correction: "Everything pushed" was true for two of four trees. `engine/SSOT` (`evo_00`) was 20 commits ahead with 2 dirty. `engine/studio` (`evo_03`) was 7 ahead with 1 dirty. The same session asked whether `evo_00` and `evo_03` were old.

2026-09-16: a sibling session took packet `G020` 68 minutes earlier. A commit swept in that sibling's `G022-website-polish` files because the pathspec did not hold. Report §4 ranks concurrent-session work on shared repos as the third-highest cost shape, after in-session guard construction and long background audits.

2026-09-23 `h6-design-pass` summary: the wrap completed across three repos.

## Migration numbers that collide

Listed from `packages/db_models/src/schema` and `supabase/migrations` on 2026-10-14. The prefix count:

| Prefix | Files |
|---|---|
| `00001` | package `00001_initial_schema.sql` and `supabase/migrations/20260908001110_00001_initial_schema.sql` |
| `00002` | package `00002_cap_table_and_reservations.sql`, `supabase/migrations/20260908001203_00002_cap_table_and_reservations.sql`, and `20260908001246_00002b_reservation_rpcs.sql` |
| `00003` | package `00003_seed_live_horses_and_investors.sql` and `supabase/migrations/20260908001313_00003_seed_inventory.sql` |
| `00004` | package `00004_handle_new_user.sql` and `supabase/migrations/20260908001326_00004_handle_new_user.sql` |
| `00016` | `20260918142320_00016_notifications.sql` and `20260921000000_00016_p022_reservation_leak.sql` |
| `00017` | `20260919001240_00017_highlights_fact_budget.sql` and `20260921010000_00017_p021_holdings_documents.sql` |

`DoD.md` on `00003`: both seed the same inventory rows. The supabase file uses `ON CONFLICT DO UPDATE`. Last writer wins.

`00016` is two different repairs sharing a number. One creates `public.notifications`. The other is the P022 reservation leak (the 1% → 0.10% downgrade). A reader who hears "00016" does not know which repair is meant. `PLAN.md` acceptance: no duplicate migration numbers.

`20260924030000_00022_lease_payment_naming.sql` comments that `00003_seed_inventory` and `00010_dsl_term_dates_and_manolo_monthly` inserted through the old column names. Renames of commercial columns had to chase the seeds.

## Local and prod were two databases

2026-09-10 rework-loop quote: the marketplace still showed `coming_soon` because the dev server reads local Supabase at `127.0.0.1:54321` (`.env.local`) and the status flip wrote to prod.

2026-09-13 correction: a local fixture password against prod Supabase returned 401.

2026-09-13 scope-drift: prod Supabase was 6 migrations behind.

`PURCHASE-FLOW-SSOT.md`: the local seed's Mulan step was 0.50% while prod was 0.10%. `docs/flow-spec/PURCHASE-FLOW-LOCK-PLAN.md` is a plan to patch `20260908001313_00003_seed_inventory.sql` so Mulan's `min_stake_pct` and `stake_step_pct` become 0.10. The seed file was being edited to chase prod. That is the downstream patch `/home/evo/AGENTS.md` gate 6 says cost 10× the source fix, citing `00003_seed_live_horses_and_investors.sql` taking 74 patches and being read 61 times.

`OUTCOME.md` stack rule, which `DoD.md` also reached after the audit: v2 gets a new empty Supabase project. No business data is copied. The two horses that go live are created through the v2 entry flow. The old project is retained, not deleted.

## Website commit shape

1028 commits. 219 subjects start with `fix`. Subjects that start with `revert` or `Revert`:

| Commit | Date | Subject |
|---|---|---|
| `3ec16a0` | 2026-10-01 | Revert "docs(wire): arrows back to the left of the figure…" |
| `31dbc9d` | 2026-10-01 | docs(wire): revert the box removal — the ask was the text, not the box |
| `3277934` | 2026-10-01 | Revert "fix(web): /horses/[slug] gets the notify switch — the second money door" |
| `3d7005c` | 2026-09-23 | revert: remove the flow-spec docs I swept in — same mistake, one commit later |
| `a51ea44` | 2026-09-23 | revert: remove check-tailwind-classes.py — I committed it by accident |
| `7a19edc` | 2026-09-13 | Revert "merge: CTA confirmation copy fix from design/visual" |
| `fad83cf` | 2026-09-13 | revert(ui): drop the design WIP that rode in with the cherry-pick |
| `ce866ac` | 2026-09-13 | revert(ui): drop the design WIP that rode in on a cherry-pick |
| `969570c` | 2026-09-09 | revert(visual): undo HeroSection pillar-ize |

`1648293` (2026-09-10) does not start with `revert`. Its subject is `test(web): revert Manolo buyable guard — flip reverted, all campaigns brochure mode`. The buyable flag and the visual branch both got rolled back inside a few days of landing.

`3277934` reverts a second money door on `/horses/[slug]`. That route is the one `PLAN.md` Search says the current structured data points at, and which v2 does not keep.

Hottest paths are in `00-paths.md`. The purchase modal, the legal pack (`pds.ts`, `sa.ts`, `dsl-workflow.ts`, `legal_engine.test.ts`), and `horses-data.ts` are the files the five root causes kept reopening.
