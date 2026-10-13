# Pricing, stake units, and the 75% string

## The formula that is already locked

`packages/legal_engine/src/commercial-rules.ts`, founder-locked 2026-09-10. Header: if a number is the same for every horse, it lives here. Per-horse variables are form fields.

| Constant | Value | Line |
|---|---|---|
| `EVOLUTION_MARGIN_PCT` | 5.0 | 16 |
| `PLATFORM_FEE_PCT` | 3.0 | 19 |
| `FLOAT_DEPOSIT_MONTHS` | 3 | 22 |
| `FLOAT_PREPAID_MONTHS` | 2 | 23 |
| `FLOAT_TOTAL_MONTHS` | 5 | 24 |
| `QUALIFICATION_PAID_UP_MONTHS` | 2 | 28 |
| `REFUND_WINDOW_DAYS` | 14 | 31 |
| `SPECIAL_RESOLUTION_PCT` | 75 | 42 |
| `INVESTOR_RETURN_MIN_PCT` | 50 | 50 |
| `INVESTOR_RETURN_MAX_PCT` | 85 | 51 |
| `INVESTOR_RETURN_DEFAULT_PCT` | 75 | 52 |
| `CLOSE_STYLES` | `fourteen_day`, `three_x_remaining` | 110 |
| `LEASE_TERM_END_MONTH` | `2028-06` | 125 |

`retailFromWholesale` lines 127–130:

```
return Math.ceil(cost * (1 + EVOLUTION_MARGIN_PCT / 100) * (1 + PLATFORM_FEE_PCT / 100));
```

`REFUND_WINDOW_DAYS` is still exported. The block at lines 133–147 says `PAYOUT_WINDOW_DAYS` was removed on 2026-09-17 because distributions are quarterly and the old constant had promised investors money 14 days after NZTR paid, faster than NZTR's own release. `INDUSTRY_DEDUCTION_PCT_LOW` / `_HIGH` (15–18%) were removed in the same block. `OUTCOME.md` Refund, written 2026-10-14 into `v2/OUTCOME.md`, has no day count. The 14-day constant is the V1 leftover. It is not the v2 refund rule.

Lines 37–40, on purpose:

> Deliberately its own constant, NOT shared with `INVESTOR_RETURN_PCT`. The two happen to both be 75 today and are semantically unrelated — one is a voting threshold, the other a revenue split. Collapsing them would silently tie the governance rule to a commercially-negotiable number.

`investorReturnPctFrom` in this file returns `null` when the split string is missing (lines 103–106). The comment says substituting a platform default misstates the owner's terms.

`sandbox/rebuild/src/lib/pricing.ts` is the file `PLAN.md` step 3 says to copy. Its `retailFromWholesale` matches (margin 5, fee 3, `Math.ceil`). Its float is a single `FLOAT_TOTAL_MONTHS = 5`, not 3+2. Its `investorReturnPctFrom` does not match:

```
export function investorReturnPctFrom(distributionSplit?: string): string {
  if (!distributionSplit) return '75';
  const match = distributionSplit.match(/(\d+(?:\.\d+)?)\s*%/);
  return match ? match[1] : '75';
}
```

Copying that function reintroduces the defect `commercial-rules.ts` was edited to remove. Copy the ceil formula and the 3+2 constants. Leave this fallback behind.

## Worked numbers from the 2026-09-10 session

The session quote: local seed wholesale 6500 → $71 / $355. Prod wholesale 7000 → $76 / $380.

| Wholesale | ceil(×1.05×1.03) | Per 1%, ceiled | × 5 months |
|---|---|---|---|
| 6500 | 7030 | 71 | 355 |
| 7000 | 7571 | 76 | 380 |

`ceil(6500 * 1.05 * 1.03) = ceil(7029.75) = 7030`. `ceil(7030 / 100) = 71`. The second ceil is what `computeDslPricing` in the rebuild file does: `Math.ceil(listPrice * (stakePercentage / 100))` with `stakePercentage` defaulting to 1.0. The v2 column `retail monthly per percent` is that second figure. The generated-column form in the audit is `ceil(ceil(wholesale * 1.05 * 1.03) / 100)`.

## Four stored prices

`00001_initial_schema.sql` lines 127–130, on `inventory`:

```
cost_monthly_nzd NUMERIC(10,2) NOT NULL CHECK (cost_monthly_nzd > 0),
list_price_nzd NUMERIC(10,2) NOT NULL CHECK (list_price_nzd > 0),
monthly_lease_payment_unit_nzd NUMERIC(10,2) NOT NULL CHECK (monthly_lease_payment_unit_nzd > 0),
join_float_unit_nzd NUMERIC(10,2) NOT NULL CHECK (join_float_unit_nzd > 0),
```

No check ties `list_price_nzd` to `cost_monthly_nzd`. A row can be internally legal and arithmetically wrong. That is why the local seed and prod could both look valid.

Stake columns on the same table (lines 131–134):

```
listed_stake_pct NUMERIC(5,2) NOT NULL CHECK (listed_stake_pct > 0 AND listed_stake_pct <= 100),
min_stake_pct NUMERIC(5,2) NOT NULL CHECK (min_stake_pct > 0 AND min_stake_pct <= listed_stake_pct),
stake_step_pct NUMERIC(5,2) NOT NULL CHECK (stake_step_pct > 0 AND stake_step_pct <= listed_stake_pct),
total_shares NUMERIC(5,2) NOT NULL CHECK (total_shares = round(listed_stake_pct / stake_step_pct, 2) AND total_shares > 0 AND total_shares <= 100),
```

`total_shares` is a stored derivative that does have an equality check. The prices do not. V2 should treat retail the way this file treated `total_shares`, and should not invent a second share-count column. `OUTCOME.md` stores listed stake, stake remaining, min stake, and step as percent.

## The split is a string

`supabase/migrations/20260909100728_00011_distribution_fields.sql`:

```
ADD COLUMN IF NOT EXISTS distribution_split TEXT,
ADD COLUMN IF NOT EXISTS distribution_schedule TEXT;
```

`20261001120000_00024_terms_disclosure.sql` lines 90–91:

> RETURN is NOT guaranteed: `distribution_split` is nullable text with no constraint, and '' is not a figure.

The trigger `enforce_listed_requires_advised_terms` only checks that the text is non-empty when status becomes `listed`. It does not check that the first percentage is between 50 and 85. The constraint `inventory_listed_requires_advised_terms` is the same test.

`supabase/seed.sql` lines 67–82, comment dated 2026-10-01:

> This is here because nothing else sets it. No migration seeds `distribution_split`; the only writer in the repo is the DSL CLI (`packages/legal_engine/scripts/dsl-workflow.ts:255`). So a reset used to take the split from every horse and the return simply vanished from every page — which is what happened on 2026-10-01, and could not be proved afterwards because the pre-reset snapshot did not capture this column.

The update sets `mulan-the-brave`, `i-stole-a-manolo`, and `nellie` to `75% Investor Return / 25% Owner Retention`. The closed campaigns are left unset on purpose. A page that cannot resolve a horse then has to choose between blank and 75. The rebuild helper chooses 75.

The 2026-09-09 corpus quote is the dropdown form of the same bug: the operator had to pick 80/20 before the form would allow 75/25. `investorSplitOptions` in `commercial-rules.ts` lines 87–92 builds that list with the default first, then every 5 points from 50 to 85 except 75. The order is derived. The stored value is still a sentence.

## Stake units, and the silent downgrade

`apps/web/src/lib/nellie-loop.ts` lines 27–44. Comment: investor-facing values are percentages. The reservation RPC (`00002`) counts step-units. Conversion happens only at that boundary. `stepPct` became required on 2026-09-24. Before that it defaulted to `SHARE_MATH.DEFAULT_STAKE_STEP_PCT` (0.5%), so a caller that omitted it converted Mulan (step 0.10%) as if the step were 0.5%.

`docs/flow-spec/PURCHASE-FLOW-SSOT.md` lines 126–133 names two silent substitutions:

1. `horses-data.ts` — `Number(row.min_stake_pct ?? 1.0)`. The column is `NOT NULL`, so a null is schema drift, not a value to invent.
2. `nellie-loop.ts` — the old default step.

Prod as read 2026-09-23, from that section: Mulan step 0.10%. Manolo and Nellie step 0.50%, minimum 1.00%. The local seed's Mulan row said 0.50%.

`supabase/migrations/20260921000000_00016_p022_reservation_leak.sql` lines 1–6:

> The 2026-09-14 incident: a buyer reserved 100 units at 1%, the Stripe session failed, the reservation sat past its TTL, and the retry hit the idempotent branch — which returned the EXISTING 10-unit reservation instead of checking whether the requested units were even available. The purchase silently downgraded 1% → 0.10%.

The sweep had no schedule. The ops endpoint was manual. Idempotency keyed on the existence of a reservation, not on the stake the buyer had just asked for.

`docs/flow-spec/INVESTOR-PURCHASE-AUDIT-2026-09-22.md` F4: `?open=1&units=99` rendered 99.00% and $6,425/mo against a stated max of 3.80%. `units=0.05` rendered 0.05%. The server would reject later. The screen showed an impossible stake first.

`docs/logs/2026-09-14.md`: first live purchase was 0.10% of Mulan, invoice $42. Vanilla unit recorded there as $65 / 1% / month, and 0.10% printing $7. Those log figures and the 7000 → $76 formula do not describe the same wholesale. The log is a point-in-time price. The formula is the invariant. V2 stores wholesale and generates retail so a log and a column cannot diverge without failing a check.

## What v2 stores instead

From `OUTCOME.md` rows: the DSL holds `investor split`, `wholesale monthly`, `listed stake`, `stake remaining`, `min stake`, `step`, and `retail monthly per percent` written by the engine. Payment style and close style are the two enums above. 75 is what an empty form shows. It is not a column default and it is not a horse's split.
