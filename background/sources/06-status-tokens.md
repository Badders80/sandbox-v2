# Status, the token gate, and the visual reverts

## One enum doing three jobs

`00001_initial_schema.sql` lines 25–35:

```
CREATE TYPE campaign_status AS ENUM (
    'draft',
    'coming_soon',
    'coming_soon_details',
    'listed',
    'fully_subscribed',
    'completed'
);
```

There is no `published` column. `listed` means "on the site" and "can be bought" at once. `coming_soon_details` is a third state between coming soon and listed. `horses-data.ts` lines 43–49 add another one in memory: `rawListingStatus` is the column, and `listingStatus` folds zero availability into `fully_subscribed` before render. The comment says the webhook must settle on the raw value plus the legal-docs lock, or a purchase that took the last stake is rejected as fully subscribed after the card is charged.

`supabase/seed.sql` asserts Mulan `status = 'listed'` and Nellie plus Manolo `status = 'coming_soon'`. Commit `1648293` (2026-09-10) reverted the Manolo buyable guard so every campaign went back to brochure mode. The 2026-09-10 corpus correction then said Manolo was already flipped and the remaining coming-soon horses were nellie and mulan. The enum, the seed, and the session were describing three different buyabilities.

`OUTCOME.md` splits this. Status on the DSL is `Coming Soon | Available | Fully Allocated | Completed`. `published` is a separate flag. Coming Soon may be incomplete, is visible, and cannot be bought. Available must be complete and means Buy Now. An unpublished DSL stays off the marketplace. The three finished horses (Prudentia, Hottathanafantasy, First Gear) are static pages, not rows.

The legal-doc gate that V1 bolted onto `listed` is a stack of status columns the seed still asserts together: `term_sheet_status`, `pds_status`, `sa_status`, `soft_content_status`, all `'approved'`, plus `price_disclosure` and `return_disclosure` equal to `'advised'` (`00024`). A horse becomes buyable only when every one of those strings matches. `OUTCOME.md` replaces that stack with the Available trigger in the audit: registered horse, three images, investor split set, wholesale set, published.

## The token gate, and what it did not stop

`/home/evo/AGENTS.md` lines 66–76, "Build Economics Gates (calibrated from corpus audit 2026-08-31)":

> Design Token Gate — Zero React UI coding until design tokens (colors, typography, grid, spacing) are locked in code. "Make it look like the screenshot" without a token contract is a token bonfire (observed: 110M tokens in 3010/Pass-2 visual thrashing).

The same section says `00003_seed_live_horses_and_investors.sql` was read 61 times and took 74 downstream patches, and `marketplace/[id]/page.tsx` was read 131 times.

`DoD.md` line 80 puts `packages/brand_dna` in the v2 surface and says tokens are loaded before any UI work. Line 197: `brand_dna` already has `scripts/generate-theme.ts` → `theme.generated.css`. `packages/ui` defines no tokens.

`evolution/engine/website/AGENTS.md` then points a website session at a different token home: `apps/web/src/dna/` plus `@theme` v4, and visual patterns from `../SSOT/doc/STYLE_GUIDE.md` via `@evo/ui`. Three token authorities: the parent gate, `brand_dna`, and `apps/web/src/dna`.

`v2/README.md` names the parent `AGENTS.md` as noise for this build. `PLAN.md` "How the build session behaves": tokens are copied once into one theme file while the landing page is built. A short `DESIGN.md` is written from that file and the finished page before Marketplace starts. There is no token pass before the landing page and no token pass after it. `evolution/engine/brand` is the asset cupboard. The generator, the shadcn slots, and the Python gates stay behind. Where `DESIGN.md` in the brand folder and the live landing page disagree, the landing page wins.

## What the gate did not stop

`purchase-flow-modal.tsx` has 135 commits. On 2026-10-02, `main` took a chain of visual merges in one day: Step 2 stepper, syndicate-governance header, explanatory copy, a green `text-status-active` restore on the price label, a 16:9 marketplace crop, Step 5 tabular numbers. Each one is a merge commit plus a feat or fix commit. The token gate was already months old.

Reverts that are the design branch leaking onto the ship branch:

- `7a19edc` 2026-09-13 — Revert "merge: CTA confirmation copy fix from design/visual"
- `fad83cf` and `ce866ac` 2026-09-13 — drop design WIP (`StatRow valueClassName`) that rode in on a cherry-pick. The subject says it belongs to `design/visual`, not `main`.
- `969570c` 2026-09-09 — revert the HeroSection pillar-ize (`e6b4ed1`)
- `a51ea44` 2026-09-23 — `check-tailwind-classes.py` committed by accident
- `3d7005c` 2026-09-23 — flow-spec docs swept in, removed one commit later
- `31dbc9d` 2026-10-01 — "revert the box removal — the ask was the text, not the box"

The website `AGENTS.md` branch law, locked 2026-09-13: `design/visual` is look only and must not touch the rail. `review/workflow-preview` was declared as the rail branch and never existed as a ref. Rail work ran as `feat/*` merged to `main`. The 2026-09-13 corpus quote is the user saying they are on `review/workflow-preview` "locking in design tokens" while the branch law says that name is not the rail. The agent and the law were using the branch names differently.

Corpus sessions on the same seam:

- 2026-09-22 `01a0c789` and 2026-09-23 `86e6aca2` — visual and token-system audits. The 09-23 summary: five admitted over-claims, a killed background re-audit.
- 2026-09-23 `h6-design-pass` — "the gate's closing line I shipped is false."
- 2026-09-11 — many turns on a 38px indent, verified by screenshot, with the test gate deferred.
- 2026-08-20 — the user demanded a light theme and the agent's last correction was still wrong. Stale cache in the same session.
- 2026-08-10 `continue-design-system-implementation` — tokenisation was delegated, then the session burned turns on a zombie process on port 3000.

`operations/knowledge/docs/chat-corpus/vault/00 - Dashboards/⚠️ Friction & Time Sinks MOC.md` records the earlier form of the same loop: iterating on CSS colours and pedigree layouts without pre-locked tokens. The gate was the response to that loop. The 2026-09 and 2026-10 history is the gate becoming a second document the visual commits did not read.

## Images and the host that does not resolve

`cdn.ts` `DEFAULT_CDN_HOST = 'https://cdn.evolutionstables.nz'`. `DoD.md` says the host does not resolve, and that v2 media goes to a new R2 bucket keyed from identity so the old keys are not carried over. `OUTCOME.md` keys files from the slug instead, stores only `image_count`, and requires three images before Available. `PLAN.md` Accounts: the new bucket waits on a Cloudflare token. The landing page's own background art can stay as static files. The old bucket and the old media keys are not reused.

`HORSE_STILLS` hardcodes extensions (png, webp, jpg) per frame. `hottathanafantasy` has two frames in the map. A placeholder under a stale slug passed CI (`DoD.md` lines 27–29). V2 does not keep a map to get wrong.

## What a later session should not reopen

- Whether tokens gate the first page. `PLAN.md` says they do not. The landing page is the visual north star.
- Whether `brand_dna` is ported. It is not.
- Whether `coming_soon_details` or `listed` come back as status values. They do not. Status and published are different columns.
- Whether `/horses/[slug]` is a second public route. `3277934` reverted a notify switch there described as "the second money door." `PLAN.md` Search: the public horse URL is `/marketplace/[slug]`.
