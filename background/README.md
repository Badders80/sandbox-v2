# Background

Filed 2026-10-14 from the forensic pass over the 60-day corpus, the v2 brief, and the live engine tree.

This folder is a reading copy. It is not a second plan. `OUTCOME.md` is still the product law, `PLAN.md` is still the build law, and `OPERATIONS.md` is still the operating law. Where a note here and the outcome disagree, the outcome wins.

`FORENSIC-AUDIT.md` is the breakdown: five root causes, the V1 assumptions that were left soft, the four invariant tiers, and the kill list.

`sources/` holds the excerpts and counts that breakdown was built from, so a later session can check a claim without re-walking the trees. The full scored corpus stays where the audit wrote it:

- `audit/corpus-deep-dive/out/report.md`
- `audit/corpus-deep-dive/out/findings.json` (73 sessions)

## What is not in this folder

The sandbox repo is public. Email addresses, phone numbers, Stripe object ids, and KYC session ids from the old project are not copied here. The public plan already records the fact that matters for cleanup: one live Mulan purchase of $42, refunded, with `cancel_at` 2028-05-01, and an old Supabase project that holds personal data for 10 people and must be kept under the NZ Privacy Act 2020.

The 52 MB `engine/content` tree, the Word pro-formas, and the Rules of Racing PDF are not copied here. Paths are in `sources/04-legal-registry.md`.

## Files

| File | What it holds |
|---|---|
| `FORENSIC-AUDIT.md` | The breakdown |
| `sources/00-paths.md` | Every path the pass actually opened or measured |
| `sources/01-corpus.md` | Rates, the decay story, domain quote clusters |
| `sources/02-identity.md` | Alias map, naming-law deltas, MyStable drop, CDN map |
| `sources/03-pricing.md` | Four price columns, the 75% string, the 0.10% downgrade, the rebuild trap |
| `sources/04-legal-registry.md` | `soft-prefill.ts`, the CI exclusion, the 26 MB / 52 MB registry |
| `sources/05-estate-migrations.md` | Seven repos, duplicate migration numbers, commit heat |
| `sources/06-status-tokens.md` | Status enum, token gate, the revert list |
