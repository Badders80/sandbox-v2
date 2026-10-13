# Paths opened or measured

2026-10-14. A later session can re-open these. Line numbers are as read that day. Files in `evolution/` move. The excerpts in the other source notes are the snapshot.

## Already in this repo

| Path | Role |
|---|---|
| `audit/corpus-deep-dive/out/report.md` | Consolidated 60-day report. 78 lines. The 88–92% table. |
| `audit/corpus-deep-dive/out/findings.json` | 73 scored sessions. 426 KB. Signal shape is `{type, quote, chunk}`. |
| `audit/corpus-deep-dive/out/gate.json` | Scorer gate. |
| `audit/corpus-deep-dive/out/manifest60.json` | Session manifest. |
| `audit/corpus-deep-dive/out/selected.json` | Selected set. |
| `docs/plans/RUNBOOK.md` | Six guardrails written from this audit on 2026-10-08. Acceptance target is the 59–61% baseline. |
| `CONTINUE.md` | Ledger. Corpus marked done 2026-10-08. Orca removed 2026-10-13. |
| `v2/OUTCOME.md`, `v2/PLAN.md`, `v2/OPERATIONS.md` | The lock this audit was checking against. |
| `rebuild/src/lib/pricing.ts` | 50 lines. The file `PLAN.md` step 3 says to copy. The empty-split fallback is the trap. |

## Brief, not in this repo

| Path | Role |
|---|---|
| `/home/evo/evolution/engine_v2/DoD.md` | Previous architecture brief, locked 2026-10-10, 342 lines. Stop line says the plan is `OUTCOME.md`. |
| `/home/evo/evolution/engine_v2/OUTCOME.md` | Product law. Compared with `v2/OUTCOME.md` on 2026-10-14 and treated as the same lock. |
| `/home/evo/evolution/engine_v2/CONTINUE.md` | Pickup pointer beside the brief. |

`DoD.md` contains email addresses and Stripe object ids in the A9/A10 correction. Those lines are not copied into this folder. The retention rule is already in `v2/PLAN.md`.

## Live engine, website repo

Root: `/home/evo/evolution/engine/website`. 1028 commits on 2026-10-14. 219 subjects start with `fix`. 8 subjects start with `revert`.

| Path | Role |
|---|---|
| `apps/web/src/lib/inventory-ids.ts` | `INVENTORY_UUID_BY_SLUG`, `LEGACY_SLUG_ALIASES`, `canonicalSlug`, `getInventoryId` |
| `apps/web/src/lib/horses-data.ts` | Stale-map comment on `id`. `slugCandidates`. `Number(row.min_stake_pct ?? 1.0)`. Derived `fully_subscribed`. |
| `apps/web/src/lib/nellie-loop.ts` | `stakePctToStepUnits`. Step became required 2026-09-24 after a 0.5% default. |
| `apps/web/src/app/mystable/page.tsx` lines 90–93 | Holdings keyed by row uuid after the slug map dropped a buyer. |
| `apps/web/src/components/horse/purchase-flow-modal.tsx` | 135 commits. Hottest file in the repo. |
| `packages/db_models/src/schema/00001_initial_schema.sql` | `inventory` money columns, `campaign_status`, `close_style`, `payment_style`. |
| `packages/db_models/src/schema/00003_seed_live_horses_and_investors.sql` | One of the two `00003` seeds. |
| `supabase/migrations/20260908001313_00003_seed_inventory.sql` | The other `00003`. `ON CONFLICT DO UPDATE`. |
| `supabase/migrations/20260909100728_00011_distribution_fields.sql` | `distribution_split TEXT`. |
| `supabase/migrations/20260918142320_00016_notifications.sql` | `00016` number one. |
| `supabase/migrations/20260921000000_00016_p022_reservation_leak.sql` | `00016` number two. The 1% → 0.10% incident. |
| `supabase/migrations/20261001120000_00024_terms_disclosure.sql` | Admits `distribution_split` is unconstrained text. |
| `supabase/seed.sql` lines 67–128 | Paints the 75% label onto `nellie`. |
| `packages/legal_engine/src/commercial-rules.ts` | The locked numbers, and the comment that 75 must not be substituted. |
| `packages/legal_engine/src/soft-prefill.ts` | Absolute `CONTENT_DIR`, `resolveHorseSlug`. |
| `packages/storage/src/cdn.ts` | `DEFAULT_CDN_HOST`, `HORSE_STILLS`. |
| `.github/workflows/check.yml` lines 171–194 | `@evo/legal_engine` excluded. 26 MB registry named. |
| `.github/workflows/ci.yml` | Same exclusion. |
| `docs/flow-spec/PURCHASE-FLOW-SSOT.md` §3.6 | Silent defaults: `?? 1.0` and `DEFAULT_STAKE_STEP_PCT`. |
| `docs/flow-spec/PURCHASE-FLOW-BIBLE.md` P016 | Two slugs, one horse, dead slug left in Stripe metadata. |
| `AGENTS.md` | "values PERCENT everywhere". Design tokens from `apps/web/src/dna/`. |

## Sibling repos under `/home/evo/evolution/engine`

Measured with `git rev-list --all --count` on 2026-10-14.

| Directory | Commits | DoD name |
|---|---|---|
| `website` | 1028 | `evo_02` |
| `SSOT` | 104 | `evo_00` |
| `comms` | 36 | `evo_06` |
| `brand` | 29 | `evo_05` |
| `studio` | 18 | `evo_03` |
| `content` | 7 | `evo_04-content` |
| `infra` | 4 | `evo_07` |

`content` is 52 MB (`du -sh`). Last commit that day: `f9da7c2` 2026-09-30. The CI comment and `DoD.md` O6 still say ~26 MB. Both figures are recorded. The Sep-14 "15 of 20 public" count is the stale one `DoD.md` A11 says not to quote.

`SSOT/doc/HORSE-IDENTITY-NAMING-LAW.md` is the naming law, absorbed 2026-09-21. The known-delta table is the Lady Ketchikan / Mulan / unversioned-content record.

`/home/evo/AGENTS.md` lines 66–76 are the build-economics gates, including the design-token gate. `v2/README.md` says that file is noise for this build.

## Git heat, website repo

Files with the most commits, from `git log --name-only`:

| Commits | Path |
|---|---|
| 135 | `apps/web/src/components/horse/purchase-flow-modal.tsx` |
| 128 | `CONTINUE.md` |
| 70 | `apps/web/src/components/horse/right-rail.tsx` |
| 69 | `apps/web/package.json` |
| 44 | `docs/wire/purchase-flow.html` |
| 41 | `apps/web/src/app/marketplace/[slug]/page.tsx` |
| 37 | `apps/web/src/lib/horses-data.ts` |
| 36 | `apps/web/src/components/mystable-dashboard.tsx` |
| 36 | `apps/web/src/app/api/webhooks/stripe/route.ts` |
| 30 | `apps/web/src/lib/nellie-loop.ts` |
| 28 | `packages/legal_engine/tests/legal_engine.test.ts` |
| 28 | `apps/web/src/app/api/checkout/create-session/route.ts` |
| 27 | `packages/legal_engine/scripts/dsl-workflow.ts` |
| 25 | `packages/legal_engine/src/pds.ts` |
| 24 | `packages/legal_engine/src/sa.ts` |
| 23 | `apps/web/src/components/pricing-card.tsx` |
| 22 | `apps/web/src/app/horses/[slug]/page.tsx` |

`/horses/[slug]` still exists beside `/marketplace/[slug]`. `PLAN.md` Search says the live structured data points at `/horses/[slug]` and that mistake stays behind.
