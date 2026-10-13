# Forensic audit — hard invariants for Engine V2

Filed 2026-10-14. Reading copy of the pass over the 60-day corpus, `evolution/engine_v2/DoD.md`, and the live engine git history. Sources and verbatim excerpts are in `sources/`. This file does not change `OUTCOME.md`.

The 88–92% correction, scope-drift, and time-sink rates are session-affected rates over 73 scored sessions in `audit/corpus-deep-dive/out/report.md`. The same four rates sat at 59–61% (time-sink 44%) in the 120-file baseline. Rework-loop (42%) and frustration (53%) barely moved. The sessions were re-deriving facts that had no single stored form.

## Top 5 root causes of rework

### 1. A horse had several keys, and the maps that joined them went stale

`evolution/engine_v2/DoD.md` (the 2026-10-10 audit) names this as why v2 exists: the same horse is the DB row, a hand-kept slug→uuid map, and a CDN path derived from the slug. `evolution/engine/website/apps/web/src/lib/inventory-ids.ts` still holds both. `INVENTORY_UUID_BY_SLUG` hardcodes six uuids. `LEGACY_SLUG_ALIASES` maps `nellie`, `almanzor-x-night-danza`, `tml-x-yearn`, `turn-me-loose-x-yearn`, and `turn-me-loose-x-yearn-2023`.

`apps/web/src/lib/horses-data.ts` records the failure: the map still said `lady-ketchikan` after the row was `nellie`, and MyStable dropped the holding. `apps/web/src/app/mystable/page.tsx` lines 90–93 key by `row.id` for that reason.

`evolution/engine/SSOT/doc/HORSE-IDENTITY-NAMING-LAW.md` records Lady Ketchikan as three names and two folders on 2026-09-16, and says the slug in prod, `engine/content`, media, the ship tree, `cdn.ts`, and `inventory-ids.ts` must be identical. `docs/flow-spec/PURCHASE-FLOW-BIBLE.md` P016: Stripe metadata from 2026-09-14 still has `horse_slug: "tml-x-yearn"` while later sessions have `mulan-the-brave`. The 2026-09-12 corpus session was still pasting `loveracing.nz/Breeding/460867/Yearn-NZ-2013-2023.aspx` plus the horse's microchip to identify Mulan. `supabase/seed.sql` lines 80–82 still write the investor split onto slug `nellie` on 2026-10-01.

### 2. Price, stake, and the investor split were prose plus duplicated numbers

`inventory` stores `cost_monthly_nzd`, `list_price_nzd`, `monthly_lease_payment_unit_nzd`, and `join_float_unit_nzd` as four independent `NUMERIC` columns (`packages/db_models/src/schema/00001_initial_schema.sql` lines 127–130). Each is only `CHECK (> 0)`. Nothing checks `list_price = ceil(cost × 1.05 × 1.03)`. The 2026-09-10 session had to resync because the local seed said wholesale 6500 → $71 / $355 and prod said 7000 → $76 / $380.

`distribution_split` is nullable `TEXT` (`supabase/migrations/20260909100728_00011_distribution_fields.sql`). `supabase/migrations/20261001120000_00024_terms_disclosure.sql` lines 90–91 say the return is not guaranteed because that column has no constraint. `supabase/seed.sql` lines 67–82 set every open horse to the string `75% Investor Return / 25% Owner Retention` because no migration writes the column, and a 2026-10-01 reset made the return vanish.

`packages/legal_engine/src/commercial-rules.ts` then parses that string with a regex (`investorReturnPctFrom`). `sandbox/rebuild/src/lib/pricing.ts` lines 46–49 return `'75'` when the string is missing. The legal-engine copy returns `null` (`commercial-rules.ts` lines 103–106) and comments that substituting 75 overrides the owner. The 2026-09-09 DSL session could not pick 75/25 until 80/20 had been chosen.

Stake units diverged the same way. `supabase/migrations/20260921000000_00016_p022_reservation_leak.sql` records a 1% reservation silently settling as 0.10% (100 units matched an old 10-unit hold). `apps/web/src/lib/nellie-loop.ts` used to default the step to 0.5% when Mulan's step is 0.10% (`docs/flow-spec/PURCHASE-FLOW-SSOT.md` §3.6). `horses-data.ts` still does `Number(row.min_stake_pct ?? 1.0)`.

### 3. The PDS and SA had no in-repo source, so every compile was a fresh reading

`packages/legal_engine/src/soft-prefill.ts` lines 15–16 default `CONTENT_DIR` to `/home/evo/evolution/engine/content`. `resolveHorseSlug` scans every horse's `profile.md` front matter, including an `aliases:` list, because the prod slug, the barn name, and the folder name disagree.

`.github/workflows/check.yml` lines 171–194 and `ci.yml` exclude `@evo/legal_engine`. The comment says the registry is 26 MB, outside the repo, and three assertions used to pass against an empty directory. On disk on 2026-10-14 that tree is 52 MB and 7 commits.

`DoD.md` A1: porting `legal_engine` frozen would port that coupling. A3: "files are fixtures, never authority" contradicts the `.docx` files kept in the package. A8: "do not rewrite" contradicts "prune what is not needed".

The 2026-09-25 session (`3b93ad5c`, 62 signals) patched a prose-matching guard three times and still had the rule open. `commercial-rules.ts` lines 30–41 and 133–147 show the same class of bug in constants: `REFUND_WINDOW_DAYS = 14` was reused as a payout promise faster than NZTR pays, and `SPECIAL_RESOLUTION_PCT = 75` is a voting threshold that was one edit away from being the investor split.

### 4. Seven repos and two migration trees, so "pushed" and "migrated" were not facts

`DoD.md` lines 278–289: the engine is `evo_00` SSOT, `evo_02` website, `evo_03` studio, `evo_04-content`, `evo_05-brand`, `evo_06-comms`, `evo_07-infra`, plus a second naming set (`01_evolution`, `02_website`, `SSOT`, `evo_01`, and others). Commit counts measured 2026-10-14: website 1028, SSOT 104, comms 36, brand 29, studio 18, content 7, infra 4.

The 2026-09-13 corpus correction: "Everything pushed" was true for two of four trees; SSOT was 20 commits ahead and studio 7.

Migration numbers collide. `00003` is both `packages/db_models/src/schema/00003_seed_live_horses_and_investors.sql` and `supabase/migrations/20260908001313_00003_seed_inventory.sql` (`ON CONFLICT DO UPDATE`, last writer wins). `00016` is both notifications and the P022 reservation fix. `00017` is both highlights and holdings documents. `00001`, `00002`, and `00004` are duplicated across the package schema and `supabase/migrations`.

CI also skips three suites unless `SSOT_TOKEN` can check out `evo_00` (`check.yml` lines 177–190). `DoD.md` A11 records an audit artefact claiming `apps/mission_control` exists. `apps/` contains only `web/`. The CI comment says that app was deleted 2026-09-24 and CI kept provisioning it.

### 5. "Listed", "coming soon", and "buyable" were one enum, and design tokens were a gate that did not gate

`campaign_status` is `draft | coming_soon | coming_soon_details | listed | fully_subscribed | completed` (`00001_initial_schema.sql` lines 25–35). Published is not its own flag. `horses-data.ts` lines 43–49 then derive a second status in memory when shares hit zero, and the webhook must read the raw column or a charged purchase is rejected. Commit `1648293` (2026-09-10) reverted the Manolo buyable guard back to brochure mode. The 2026-09-10 session found the marketplace still `coming_soon` because the flip wrote to prod while the dev server read `127.0.0.1:54321`.

Design tokens were a precondition in two places that agents load first: `/home/evo/AGENTS.md` gate 2 ("zero React until tokens are locked", citing 110M tokens of visual thrash) and `DoD.md` line 80 (`packages/brand_dna`, tokens before any UI). The website repo then took 135 commits on `purchase-flow-modal.tsx`. On 2026-10-02, `main` absorbed a chain of one-line visual merges (stepper, 16:9 crop, green price label). On 2026-09-13, `7a19edc`, `fad83cf`, and `ce866ac` reverted design WIP that had ridden onto the ship branch.

`packages/storage/src/cdn.ts` keeps a second hand map, `HORSE_STILLS`, and `DEFAULT_CDN_HOST = 'https://cdn.evolutionstables.nz'`, which `DoD.md` says does not resolve. Of 1028 website commits, 219 subjects start with `fix`.

The corpus mechanism on top of these five is report §3. From 2026-09-11, agents built checks, then found the checks were wrong ("Three verifier failures were my own bad regexes"; "the regex is matching my own explanatory comment"). The 2026-09-12 auditor marked `b08ffec` PASS. That commit broke settlement. Report §2: about 10 of 26 redo signals in one batch were re-runs of gates that had already passed.

## Fragile assumptions in V1

These were prose, a seed file, or a comment, and the code treated them as optional.

- A barn name is a display nickname and also a legal lookup key. The naming law says both, in §§5–6 of `HORSE-IDENTITY-NAMING-LAW.md`. The lookup half is what grew `LEGACY_SLUG_ALIASES` and `resolveHorseSlug`.
- `distribution_split TEXT` plus a regex is the owner's commercial term. The only guaranteed writer was a seed update that paints 75% onto three slugs, one of which is the stale barn slug `nellie`.
- An empty split may render as 75%. That is `INVESTOR_RETURN_DEFAULT_PCT` in `commercial-rules.ts` line 52, the seed string, and `investorReturnPctFrom` in `sandbox/rebuild/src/lib/pricing.ts` line 47. `PLAN.md` step 3 currently says to copy that file.
- Derived money can be stored next to its inputs and kept aligned by hand. The four price columns on `inventory` have no equality check. Local seed and prod both looked valid at different wholesale numbers.
- `min_stake_pct ?? 1.0` and `stepPct = 0.5` are safe defaults. Mulan's prod step is 0.10% and the minimum is 0.10% (`PURCHASE-FLOW-SSOT.md` lines 127–133). The P022 downgrade is that assumption reaching Stripe.
- `REFUND_WINDOW_DAYS = 14` is the refund rule. `OUTCOME.md` Refund, and `sandbox/CONTINUE.md` on 2026-10-14, is a pro-rata of unused full months, with no day count. The pro-forma's 14-day remedy then sale at Fair Value less 20% is a different instrument (`PLAN.md` Legal authority).
- 75 means one thing. It is the empty-form split, the special-resolution threshold (`SPECIAL_RESOLUTION_PCT`), and the figure an empty NZTR form prints. `commercial-rules.ts` lines 37–40 already warn that collapsing the first two silently ties a vote to a negotiated split.
- Soft PDS copy lives in markdown outside the website repo, and `soft_legal JSONB` on the row wins when present (`soft-prefill.ts` lines 4–5). Two authorities, and CI tests neither.
- `image_count` can be replaced by `hero_image_url TEXT NOT NULL` plus `HORSE_STILLS`. `DoD.md` lines 27–29: a hero requested under a stale slug returned a placeholder and CI passed.
- Sire and dam are either text or rows in the same table. `DoD.md` lines 58–59 require self-referential pedigree rows. `OUTCOME.md` says sire and dam are names on the horse. Both documents were in force together until `OUTCOME.md` was declared the lock.
- `listed` means published and buyable. The enum also contains `coming_soon_details` and `draft`, and a memory flag folds zero shares into `fully_subscribed` after the card is charged.
- Design tokens in `packages/brand_dna` (`scripts/generate-theme.ts`) are a gate that runs before the page. The page that shipped is `apps/web`, and parent `AGENTS.md` still orders every session to block React until tokens lock.
- One horse's facts fit in one repo. `legal_engine` reads `engine/content`, CI checks out SSOT with a token, and media keys live in `cdn.ts`.
- A migration number is an ordering hint. Two files numbered `00003` both seed inventory. Two files numbered `00016` are unrelated repairs.
- The old Supabase project holds only test trades and can be deleted. `DoD.md` A9–A10, restated without personal identifiers: the old project holds real personal data for 10 people (emails, phones, KYC session ids, audit digests) plus signed PDS/SA hashes and two storage buckets, and the founder's live Mulan purchase of $42 was refunded with `cancel_at` 2028-05-01. `PLAN.md` already locks the retention rule.

## Recommended hard invariants for V2

These are the mechanical form of rules already in `evolution/engine_v2/OUTCOME.md` and `sandbox/v2/PLAN.md`. Where those two disagree with `DoD.md`, the outcome already won.

### Tier 1 — Postgres constraints

One migration series. A check fails the build if two files share a numeric prefix. That is acceptance item 3 in `DoD.md`, which V1 failed on `00003`, `00016`, and `00017`.

`horses`

- `id uuid` primary key. `microchip text` unique. `slug text` unique, not null. A second insert with the same slug fails. No `slug_aliases` table, no `hero_image_url`.
- `sire_name text`, `dam_name text`. They are names.
- `image_count integer not null default 0 check (image_count >= 0)`.
- `about_horse`, `racing_outlook_and_pedigree`, `race_expectation` text. `race_record jsonb`. The three soft columns are the only prose on the horse.
- `registered boolean not null`.

`dsls`

- `unique (horse_id, end_date)`.
- `payment_model` enum `subscription_float | upfront`. `close_style` enum `fourteen_day | three_x_remaining`.
- `status` enum `coming_soon | available | fully_allocated | completed`. `published boolean not null default false`. Status and published are different columns.
- `investor_split numeric(5,2)` with no default. `check (investor_split is null or investor_split between 50 and 85)`.
- `wholesale_monthly numeric(10,2)` is the only money input. `retail_monthly_per_percent` is a generated column: `ceil(ceil(wholesale_monthly * 1.05 * 1.03) / 100)`. Writers cannot store a second price. This is the constraint the four V1 price columns did not have. Wholesale 6500 must yield 71 per percent. Wholesale 7000 must yield 76 per percent.
- `listed_stake`, `min_stake`, `step` are percent. `check (min_stake > 0 and step > 0 and min_stake <= listed_stake and listed_stake % step = 0)`. No `?? 1.0`, no default step of 0.5.
- A constraint trigger: `status = 'available'` requires `published`, `investor_split` not null, `wholesale_monthly > 0`, the horse `registered`, and `horses.image_count >= 3`. Coming Soon stays insertable while those are null. An unregistered horse cannot pass Coming Soon.

`sales`

- `stripe_session_id text unique`. `stake numeric`, `amount numeric`, `pds_markdown`, `sa_markdown`, `pds_hash`, `sa_hash` all not null.
- No trigger on `horses` or `dsls` updates those four document columns. A later edit of `about_horse` leaves the sold markdown byte-for-byte.
- `amount` is checked at insert against the stake and that DSL's `retail_monthly_per_percent` times 5 (3 months reserve plus 2 months prepaid). The first payment is the only payment the webhook writes.

`accounts`

- `full_name text not null`, `email text unique`, `kyc_status` enum. No NZTR licence column. KYC is Stripe Identity on this row.

`trainers` and `owners` as `OUTCOME.md` lists them: one bio, one profile paragraph. No race results on the owner.

### Tier 2 — TypeScript pricing and float contracts

Put the shared numbers in the engine rules module. The DSL row stores the owner's terms only.

Copy `retailFromWholesale` from `packages/legal_engine/src/commercial-rules.ts` lines 127–130: `Math.ceil(cost * 1.05 * 1.03)`. Copy `FLOAT_DEPOSIT_MONTHS = 3`, `FLOAT_PREPAID_MONTHS = 2`, `QUALIFICATION_PAID_UP_MONTHS = 2`, `INVESTOR_RETURN_MIN_PCT = 50`, `INVESTOR_RETURN_MAX_PCT = 85`, `CLOSE_STYLES`. Keep `SPECIAL_RESOLUTION_PCT = 75` as its own constant.

Do not copy `investorReturnPctFrom` from `sandbox/rebuild/src/lib/pricing.ts`. That function returns `'75'` on an empty split. The contract is the legal-engine function: empty input returns null, and a known horse that renders 75 from that null fails the test. The empty-form display of 75 lives in the form component, not in the pricing function and not as a column default.

Tests, failing the build:

- `retailFromWholesale(6500) === 7030` and the per-percent figure is 71. `retailFromWholesale(7000)` yields 76 per percent. Float for that percent is 5 times the monthly figure (355 and 380 in the 2026-09-10 session).
- `isInvestorReturnPctInRange` rejects 49 and 86. It accepts 50 and 85.
- A stake that is not a multiple of that DSL's `step` throws. The function takes the step as a required argument, as `stakePctToStepUnits` does after 2026-09-24. There is no default step.
- Rendering a horse with a null split produces a blank, never the characters `75%`.
- The refund sentence in the buy screen, the PDS, and the SA is the Refund section of `OUTCOME.md`. A test fails if the documents contain a day count.

`PLAN.md` step 3 should be read with that one substitution. The ceil formula in the rebuild file matches. The empty-split fallback does not.

### Tier 3 — read-only legal baselines

Copied into the new repo at build, not compiled against, and not fetched from `/home/evo/evolution/engine/content`.

1. The Rules of Racing PDF, effective 21 September 2026, from the URL in `PLAN.md`. The check uses the Second Appendix (Bloodstock Syndication Code of Practice, Rule 432). The other pages of the 209-page PDF are not part of the compiler check.
2. The three NZTR pro-forma `.docx` files at `evolution/engine/website/packages/legal_engine/reference/nztr-pro-forma/`. Fixed Monthly Contribution is the matching form. The Monthly Invoiced form stays so the $365 flat manager fee stays visible as a difference.
3. A principles note. Each line cites the PDF clause or the Fixed Monthly form it came from. The compiler's self-audit checks generated markdown against the note.

Four differences the audit keeps, from `PLAN.md`:

- Stakes are 1%, with that DSL's minimum and step. The pro-forma's 5% example is not this product.
- Forfeiture is the printed procedure in the outcome. The pro-forma's 14-day remedy, then sale at Fair Value less 20%, is not this product.
- The fee is the 5% and the 3% inside the listed-price formula. The $365 flat manager fee is not this product.
- The investor split is the number on that DSL. An empty form's 75%, and a 16% statutory deduction, are not the investor's return.

The compiler writes markdown. It does not fill the Word files and it does not port `legal_engine`. Promoter line on every document: Alex Baddeley, Evolution Stables Ltd. The SA prints hard facts and commercial terms. The PDS is what reads `about_horse`, the outlook, the expectation, and the trainer bio.

This is the resolution of `DoD.md` A3: those three files are the authority, they are read-only, and horse markdown is not.

### Tier 4 — `OUTCOME.md` spec rules

These stay in the outcome because a column check cannot see them. `PLAN.md` and `OPERATIONS.md` point at them. A rule is written once.

- One repo at `evolution/engine_v2/website`. The parent is not initialised. The engine prices, compiles, migrates, and holds the webhook. The website reads Supabase and never prices, compiles, or holds a Stripe secret.
- One operator screen, the DSL page. Horse, trainer, and owner are written from that page.
- A registered horse starts from a loveracing.nz link. An unregistered horse is typed by hand and cannot be published past Coming Soon.
- Prudentia, Hottathanafantasy, and First Gear stay static pages. I Stole A Manolo stays Coming Soon. Lady Ketchikan and Mulan The Brave are the two that can become Available.
- Images live in the new R2 bucket, folder named from the slug, files `01` upward. The database stores `image_count` only. A rename moves the folder. There is no redirect table. The legal-name slug is chosen before the first publish.
- Coming Soon is visible and has no Buy Now. Available means Buy Now. Unpublished is off the marketplace and out of the sitemap.
- The sale exists when the webhook writes the row. MyStable shows pending until that row exists. A card decline writes no sale and sends no founder email. Cancelling Checkout writes no sale.
- Four emails, sent by the engine: sale written, KYC failed, sale succeeded, our-fault checkout or webhook failure.
- "Billed monthly on the 1st" is a label. Later monthly charges, defaults, prize freeze, and forfeiture are printed procedures. No job runs them.
- The public host is `https://www.evolutionstables.nz`. Horse pages are `/marketplace/[slug]`. Each horse page has one `h1` and that horse's description.
- The live site stays on `Badders80/evo_02` and Vercel `evolution-3-0` until cutover. The new build does not call it. The old Supabase project is retained under the NZ Privacy Act 2020. The Mulan subscription is cancelled and the refund confirmed before anything on that Stripe account is removed.

## Anti-pattern kill list

Do not let these back into the new repo or into markdown that a session will treat as instructions.

- `LEGACY_SLUG_ALIASES`, `INVENTORY_UUID_BY_SLUG`, `slugCandidates`, and any slug→uuid file. The row's `id` is the join. `inventory-ids.ts` and the MyStable comment are the record of what the map did.
- `HORSE_STILLS` and `DEFAULT_CDN_HOST = 'https://cdn.evolutionstables.nz'`. `image_count` plus `{slug}/01` is the image fact.
- `resolveHorseSlug`, `aliases:` in front matter, and `CONTENT_DIR = '/home/evo/evolution/engine/content'`. The registry stays in the archive. CI must not exclude the compiler with `--filter='!@evo/legal_engine'`.
- A second migration tree, a repeated numeric prefix, and `ON CONFLICT DO UPDATE` seeds that race. `00003` is the specimen.
- `distribution_split text`, a regex that reads the first percentage, and a seed that writes `75% Investor Return / 25% Owner Retention` because nothing else sets it. The column is `numeric(5,2)` with no default.
- `investorReturnPctFrom` returning `'75'`. That is `sandbox/rebuild/src/lib/pricing.ts` lines 46–49.
- Four stored prices. `list_price_nzd`, `monthly_lease_payment_unit_nzd`, and `join_float_unit_nzd` as columns beside wholesale.
- `min_stake_pct ?? 1.0` and a default step of 0.5%. Mulan is 0.10%.
- `REFUND_WINDOW_DAYS`, `PAYOUT_WINDOW_DAYS`, and any 14-day refund or forfeiture sentence. Removed in `commercial-rules.ts` lines 133–147 and again in the outcome on 2026-10-14.
- One constant shared by the special resolution and the investor split.
- `campaign_status` values `coming_soon_details` and `listed`, and a derived `fully_subscribed` that the webhook reads after charging the card.
- Sire and dam as foreign keys to `horses`. Shoulder brand, family number, DNA Typed, PV, and BT as columns (`DoD.md` O9, parked in `PLAN.md` ruling 5).
- Four entry screens (horse, owner, trainer, DSL). `DoD.md` lines 69–74. The outcome is one DSL page.
- A design-token gate before the page exists. `/home/evo/AGENTS.md` gate 2 and `DoD.md` line 80. Tokens are copied once into the website theme while the landing page is built.
- `packages/brand_dna` as a package the website must import, and `generate-theme.ts` as a step that runs before UI.
- Porting `legal_engine` frozen, filling the NZTR Word files, or a horse-content stream for updates and highlights (`DoD.md` A6).
- An `AGENTS.md`, a `CLAUDE.md`, or a skill file in `sandbox/v2` or `engine_v2`. `sandbox/v2/README.md` already forbids them.
- `git init` of `evolution/engine_v2`. A slug redirect table. A second canonical on the apex host. Structured data pointing at `/horses/[slug]`.
- Prose-matching tests as the price or legal contract. The 2026-09-25 session patched one three times. The contract is the generated column, the range check, and the principles note.
- A check that passes when the fixture is empty. That is why `legal_engine` was removed from CI, and why `seed.sql` lines 84–86 say an `update` of zero rows stays green.
- Quoting the Sep-14 "15 of 20 repos are public" count, or `apps/mission_control`, as current (`DoD.md` A11).
- Orca, and any note that says to reinstall it. Removed 2026-10-13 (`sandbox/CONTINUE.md`).
