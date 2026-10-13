# Identity — aliases, the naming law, the dropped holding

## What DoD.md concluded on 2026-10-10

The first diagnosis ("the same field copied into three files") was falsified by an independent audit that day. The replacement diagnosis, from the top of `DoD.md`:

- Two migrations share the number `00003` and both seed the same inventory rows. Last writer wins.
- The same horse is the DB row, `apps/web/src/lib/inventory-ids.ts`, and CDN paths derived from the slug.
- `horses-data.ts` is not a fact source. It is 596 lines that read and normalise the DB. An earlier draft of `DoD.md` claimed it hardcoded horse records. That claim was corrected in the same file.
- The fix named there: one identity per horse, paths derived from identity, no hand-kept alias maps, no duplicate migrations.

`DoD.md` data law also says sire and dam are horse records in the same table and pedigree is derived by traversal. `OUTCOME.md` superseded that: sire and dam are names on the horse. The audit's identity diagnosis stands. The self-referential pedigree does not.

`DoD.md` wanted media keyed by uuid or microchip. `OUTCOME.md` and `PLAN.md` ruling 3 superseded that: files are `{slug}/01` upward, and a rename moves the folder. There is no redirect table and no slug-to-path lookup.

## The map that is still in the website repo

`apps/web/src/lib/inventory-ids.ts`, as read 2026-10-14. The file's own header says a barn name is never a slug, then defines the alias map anyway so lookup accepts one.

Hardcoded uuids:

| Slug | Constant / uuid |
|---|---|
| `lady-ketchikan` | `11111111-0000-0000-0000-000000000001` (`LADY_KETCHIKAN_INVENTORY_ID`) |
| `mulan-the-brave` | `…0002` |
| `prudentia` | `…0003` |
| `hottathanafantasy` | `…0004` |
| `i-stole-a-manolo` | `…0005` |
| `first-gear` | `…0006` |

`NELLIE_INVENTORY_ID` is a deprecated alias of the Lady Ketchikan uuid.

`LEGACY_SLUG_ALIASES`:

| From | To |
|---|---|
| `nellie` | `lady-ketchikan` |
| `tml-x-yearn` | `mulan-the-brave` |
| `almanzor-x-night-danza` | `lady-ketchikan` |
| `turn-me-loose-x-yearn` | `mulan-the-brave` |
| `turn-me-loose-x-yearn-2023` | `mulan-the-brave` |

`canonicalSlug` returns the map value or the input. `getInventoryId` canonicalises first. The comment in that file says two call sites used to disagree, and the one that forgot failed silently: prod's Lady Ketchikan row still carried the barn-name slug, so `getInventoryId('nellie')` was `undefined` and the acceptance route skipped its holdings-hash sync.

`horses-data.ts` `slugCandidates` walks the map in both directions so a read succeeds whether prod has or has not been renamed. That is the deploy-order bridge. It is also a second copy of the alias list.

## The drop

`horses-data.ts` lines 28–32, on the campaign `id` field:

> A hand-kept slug→uuid map is a duplicate of DB state and goes stale on a rename; when it did, the horse now called `nellie` was still listed as `lady-ketchikan`, and the dashboard silently dropped the holding.

`mystable/page.tsx` lines 90–93 build `campaignsByHorseId` from `campaign.id`. The comment there says a real buyer holding the renamed horse saw an empty MyStable.

`DoD.md` lines 27–29 attribute a related failure to `horses-data.ts` comments: a hero image requested under a stale slug, and a placeholder that passed CI. The drop comment is the one still in `horses-data.ts`. The placeholder-passed-CI sentence is in `DoD.md`.

## Naming law, known deltas as at 2026-09-16

`evolution/engine/SSOT/doc/HORSE-IDENTITY-NAMING-LAW.md`. Locked 2026-09-16, absorbed into SSOT 2026-09-21. The law's own rule is "the slug in prod, content, media, the ship tree, `cdn.ts`, and `inventory-ids.ts` must be identical. No aliases." Section 5 then requires lookup to accept a barn name. That pair is what the alias map was written to satisfy.

| Horse | Issue recorded in the law |
|---|---|
| Lady Ketchikan | Prod slug `nellie`. Content and vault `almanzor-x-night-danza`. Ship tree `nellie`. Three names, two folders. |
| Mulan | Resolved 2026-09-17 to `mulan-the-brave`. Old shapes "survive only as redirects and alias-map entries." |
| `inventory-ids.ts` | A hedged alias in `cdn.ts` (`lady-ketchikan`) was never mirrored here. |
| Tests | `packages/storage/tests/cdn.test.ts` skipped `lady-ketchikan` in the cover-convention loop. |
| `engine/content` | The law's table says "in no git repo — 26MB of canonical facts, unversioned." Corrected in the same file on 2026-09-21: it is a git repo. On 2026-10-14 it is 52 MB and 7 commits. |
| `engine/_wt/` | The law first said the worktree list was empty. Corrected 2026-09-17: `git worktree list` from `engine/website` showed 6. The empty reading came from running the command outside a repo. |

Section 7: renaming a live horse is a migration, not an edit. It touches the prod row, stored document hashes, public asset URLs, and signed agreements. It is founder-gated. It is never a side effect of a tidy.

Slug shape the law locks, and `OUTCOME.md` keeps: official NZTR name, kebab-case, country suffix dropped. `Hottathanafantasy` stays one word. An unnamed horse is `sire-x-dam-yr` with the year required. Abbreviating the sire to `tml` is wrong because the slug must be derivable from the horse.

`supabase/seed.sql` lines 100–101 still assert slug `nellie` for one of the two unbuyable open campaigns, on a file dated in comments 2026-10-01. The seed and the naming law disagree, and the seed is what a reset applies.

## CDN map

`packages/storage/src/cdn.ts`:

```
export const DEFAULT_CDN_HOST = 'https://cdn.evolutionstables.nz';
```

`DoD.md` says that host does not resolve. `HORSE_STILLS` is a hand map of `/horses/{slug}/0N.ext` for the six horses. The comment says the `nellie` and `tml-x-yearn` blocks were deleted in G018 C4, and that an alias is how the two slug maps drifted apart. `hottathanafantasy` has two stills in the map. `OUTCOME.md` requires three images before a DSL can be Available. The map is not that count. `image_count` does not exist as a column. `inventory.hero_image_url` is `TEXT NOT NULL`.

`TRAINER_PORTRAITS` maps `lance-osullivan`, `wexford`, and `wexford-stables` onto the same file. That is a third small alias map.

## Stripe still has the dead slug

`docs/flow-spec/PURCHASE-FLOW-BIBLE.md` P016: sessions on 2026-09-14 carry `horse_slug: "tml-x-yearn"`. Sessions from 2026-09-21 carry `mulan-the-brave`. Decision D4 in that file: leave the alias map, and backfill Stripe only if a reconciliation joins on slug. `OUTCOME.md` says sales point at the uuid and there is no redirect table. A sold row that stores a slug is the thing that made D4 necessary.

## Corpus

2026-09-12 session, still identifying Mulan by a breeding URL whose path says `Yearn-NZ-2013-2023` plus a microchip. The naming law says life number `NZ00460867` and URL id `460867` are the same identifier (`DoD.md` field map, verified against `loveracing.nz/stud-book/460867/Mulan-The-Brave-NZ-2023.aspx`). The URL shape was not a stable name.
