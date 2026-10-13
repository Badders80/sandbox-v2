# V2 build plan

Status: waiting for the word **build**. No checkout of the repo until then. The operator guide is `OPERATIONS.md` in this folder.

Read `OUTCOME.md`, then `CONTINUE.md`. Use `DoD.md` only to check a choice that was already dropped. If `DoD.md` and `OUTCOME.md` disagree, follow `OUTCOME.md`.

## Rulings already made

These close contradictions in the old brief. They are not new product decisions.

1. The repo root is `/home/evo/evolution/engine_v2/website`. One private repo, two hosted apps. `DoD.md` still lists an option to put the repo at `engine_v2/` level (O7). `OUTCOME.md` closed that: do not `git init` the parent. The parent keeps `OUTCOME.md`, `CONTINUE.md`, and `DoD.md`.
2. Do not port the old packages, the monorepo, the alias map, or the 26 MB `engine/content` registry. Soft text is three columns on the horse. Audit item A1 does not block this build.
3. Image files are named from the slug, `01` upward, in a folder for that slug. A rename moves the folder. There is no redirect table and no slug-to-path lookup. `DoD.md` also says to key media by uuid. The outcome wins.
4. Design tokens and brand skinning are parked out of the build critical path. Steps 1 to 8 run with functional, unstyled Tailwind utility wireframes. The visual north star is preserved in `apps/website`, but visual polish, token extraction, and custom font integration (`Geist Mono`, `Instrument Serif`) occur as a post-build visual pass.
5. Shoulder brand, family number, DNA Typed, PV, and the blank BT field are not columns and are not printed. O9 stays parked.
6. The first slice stores the three soft columns and the race record as one jsonb blob. It does not build a second content stream for updates and highlights (DoD A6).
7. The three finished static horses stay static pages. I Stole A Manolo stays Coming Soon. Lady Ketchikan and Mulan The Brave are the two that can become Available.

## Working defaults

Change these before the first commit if you want a different shape. They are not in the outcome.

- Both apps are Next.js, in the one repo: `engine/` and `website/`. The engine hosts the DSL page, the rules, the compiler, the migrations, and the Stripe webhook. The website reads Supabase, holds the tokens, and never prices, compiles, or holds a Stripe secret.
- Signup and login use Supabase Auth. Google or email. The account row stores full name, email, and KYC status. Full name is collected at signup.
- Stripe stays in test mode until the buy path is flipped on. Live mode flips at the swap, not before.

## Legal authority

Settled 2026-10-13. The compiler writes the PDS and the SA as markdown from the horse, the trainer, and the DSL. It does not fill the Word files, and it does not port `legal_engine`.

The reference set lives in the new repo, read-only, copied in at build. It is three pieces:

1. The Rules of Racing PDF, effective 21 September 2026: `https://nztr.co.nz/sites/nztrindustry/files/2026-09/RULES-OF-RACING_effective-from-September-2026.pdf`. This is the rules authority. The Bloodstock Syndication Code of Practice is inside it, as the Second Appendix, made under Rule 432 (the Code text itself is marked amended 1 January 2016). The disclosure-statement requirements follow that appendix. The other pages of the 209-page rules are not part of the compiler check.
2. The three NZTR pro-forma `.docx` files now in `evolution/engine/website/packages/legal_engine/reference/nztr-pro-forma/`. They sit with the PDF as the official forms. They are a base reference. They are not the 26 MB content registry, and they are not templates to compile against. Fixed Monthly Contribution is the form that matches this product. The Monthly Invoiced form stays in the set so that difference stays visible.
3. A principles note beside those files. The compiler's self-audit checks the generated markdown against this note. Each line in the note points at the PDF clause, or at the Fixed Monthly form, that it came from.

The note carries these disclosure sections. Dropping one is a red flag:

- About the syndicate: horse, trainer, share structure, monthly costs, prizemoney split, New Zealand training, governed by the SA, post-career plan
- Upfront costs, including GST
- Number of shares and cost per share
- Estimated ongoing costs, stated as estimates, GST inclusive
- Insurance: insurer, term, insured value, cover type
- Valuation: still rendered; not required when the horse is leased or bought at public auction
- Veterinary report: a vet examined the horse and certified it sound for racing
- Material interests and commissions
- Standard industry risks
- Governing documents, with the SA attached
- Declaration: Authorised Syndicator under the NZTR Code of Practice; NZTR does not endorse or guarantee the offer
- Applicant acknowledgement: 18 or over, not prohibited from owning a racehorse, share percent, purchase price, personal details, signature

The note carries these syndicate-agreement points, which the SA must not contradict:

- Formed under the NZTR Bloodstock Syndication Code of Practice. The object is to acquire the horse, by purchase or lease, and to race it as a recreational pursuit.
- Binding on the Promoter, the Syndicate Manager, and each shareholder. A change needs a 75% special resolution.
- A signed acknowledgement or a ticked online box counts as signed.
- Ends on death, disposal, or lease expiry of the last horse.
- A share is an undivided tenant-in-common interest. It is not a partnership and not an agency.
- Transfer: notice, first offer to existing shareholders, then Syndicate Manager approval. The Promoter may subscribe and transfer freely.
- Manager appointed by the Promoter. Duties include the trust account, the register, and communication with shareholders.
- Meetings are not required. A resolution can pass without a meeting.
- Stakes money is held in trust and distributed pro-rata.
- Complaints go to the Syndicate Manager first. A serious complaint goes to NZTR. Disputes run consultant, then an expert nominated through the NZTR CEO, and that decision binds.
- Definitions in Code sections 1.1 and 1.2 are incorporated.
- The Fixed Monthly form adds financial statements, prepared to GAAP, within five months of a 31 March balance date.

Four differences are locked. The audit must keep them:

- Stakes are 1%, with the owner's minimum and step. The pro-forma's 5% example is not ours.
- Forfeiture is the stricter procedure already in the outcome: printed, not a system that runs it. The pro-forma's 14-day remedy, then sale at Fair Value less 20%, is not ours.
- The fee is the 5% and the 3% already in the listed-price formula. The Monthly Invoiced form's $365 flat manager fee is not ours.
- The investor split is the fixed percent on that DSL. An empty form shows 75%, and that is not a horse's split. The 16% statutory deduction is not the investor's return math.

Insurance, the vet report, and the valuation line are not columns yet. They get a home when the compiler is written.

## Search

Settled 2026-10-13. A small search layer is built with the pages. The current site's shape is the one to keep: one site URL, a title and description on each page, an Open Graph image, `robots.txt`, a sitemap of the landing page plus each public horse, and structured data for the organisation and the website.

The landing page carries the organisation and the website. The marketplace and each horse page carry their own title, description, and canonical URL. MyStable, login, and checkout stay out of the index. Press and the FAQ can be indexed later, as their own pages.

Three current mistakes stay behind:

- The public host is `https://www.evolutionstables.nz`. Canonicals, the sitemap, and structured data use that host. The apex `https://evolutionstables.nz` is not a second canonical. The current code uses the apex, and that mistake stays behind.
- Horse structured data uses the real page, `/marketplace/[slug]`. The current data points at `/horses/[slug]`.
- A horse's description is that horse's. An empty form's 75% is not copied into the site description as every horse's split. Each horse page has one `h1`.

The sitemap lists the landing page, the marketplace, and every published DSL, including Coming Soon. An unpublished DSL is absent. Buyer documents stay private and unindexed. A rename changes the public URL, and there is no redirect table, so the legal-name slug is chosen before a horse is first published.

The landing page and the horse page render as HTML. The first response contains the horse name, the status, and one sized hero image. Google Search Console is set up at the domain cutover, with the email sender and the second vendor login.

## Accounts

Settled 2026-10-13. The live site stays on its current connection. Vercel project `evolution-3-0` keeps deploying `Badders80/evo_02` (`apps/web`) to `www.evolutionstables.nz`. That repo, that project, its build command, and that domain are not changed during the build. The new build does not get linked to `evolution-3-0`.

At the domain cutover, `www.evolutionstables.nz` stops being served from `Badders80/evo_02` and is served from the new repo. The GitHub repo itself stays. It is the archive of the current site. The build does not rewrite it, rename it, or turn it into the v2 repo.

Talk to the vendors with the CLIs already logged in on this machine. No new connector is required for GitHub, Supabase, Vercel, or Stripe. The connected session tools are GitHub, Chrome DevTools, and Playwright.

| Vendor | What build uses | What it must not touch |
|---|---|---|
| GitHub | `Badders80/engine-v2` exists: `https://github.com/Badders80/engine-v2`. Empty, created 2026-10-13. Public for review, and private again before the first code commit. Local path `evolution/engine_v2/website` is not a checkout yet. The plan review copy is the public repo `Badders80/sandbox`. | `Badders80/evo_02` |
| Supabase | A new empty project in the existing org. Email auth first. | `evo-02-prod`, `Evolution-3.0`, `evolution-content-factory` |
| Vercel | Two new projects on the new repo, one per app (`engine/`, `website/`). Previews only. | `evolution-3-0` and `www.evolutionstables.nz` |
| Stripe | The existing Evolution Stables Limited account, test mode, through the Stripe CLI. | Live keys, and the live Mulan subscription |
| Google | `gcloud` is already `alex@evolutionstables.nz`. Google login is the existing OAuth client plus one new redirect for the new site. The live site's redirect stays. | Repointing the live client |
| Cloudflare R2 | A new bucket. This is the one missing login: no Cloudflare token is on this machine. Step 5 waits for that token. Steps 1 to 4, and the landing page, do not. | The old bucket and the old media keys |
| DNS, Search Console, email sender, second vendor login | At the domain cutover. | Any change while the old site is still serving |

## How the build session behaves (Execution Protocol)

Settled 2026-10-13. Updated 2026-10-14. This section is the sole operational law for any CLI model or agent session driving this build. The new repository receives NO `AGENTS.md`, NO `CLAUDE.md`, and NO separate `HOW-WE-WORK.md` or meta-rule files.

This plan and `OUTCOME.md` outrank all external estate instructions. Do not touch `Badders80/evo_02`, `evolution-3-0`, or legacy Supabase projects.

### 1. Atomic Step Protocol
- The build executes exactly ONE numbered step at a time from "When you say build" (Steps 1–10).
- Multi-step execution across prompt boundaries is forbidden. A step must be committed to git, pass CI/tests, and display a verified diff before the next step begins.
- If an instruction is ambiguous, halt and ask. Do not guess defaults or invent architectural glue.

### 2. Session Hygiene & Fresh Lifecycle (Anti-Thrash Tripwire)
- **One Step, One Session:** Every step executes in a fresh, clean CLI session. When a step is committed and verified on disk, exit the CLI. The next step boots in a brand-new turn with zero chat history.
- **15-Turn Cap:** If a step exceeds 15 turns without completing its verification diff, terminate the session immediately. Do not attempt to debug or converse in a degraded context. Check `git status`, revert dirty partial edits, and relaunch fresh.
- **Zero In-Memory State:** Progress is tracked strictly on disk (`git log`, passing tests, file diffs), never in LLM conversational memory.

### 3. Negative Constraints Precede Positive Rules
- All models have strong priors toward standard web/SaaS boilerplate. Prompts and step executions must enforce negative constraints first: what NOT to import, what NOT to name, and what NOT to add as default values.
- Shared pricing, splits, and schema structures are governed strictly by `OUTCOME.md` and this plan. Do not infer conventions from older packages or monorepo remnants.

### 4. Verification by Evidence (Disk & Diff Only)
- Self-attestation is void. Statements such as "Done", "Completed", or summary checkmarks without terminal evidence are rejected.
- To complete any step, the session must provide:
  1. The exact file paths and line ranges modified.
  2. The raw output of `git diff` demonstrating adherence to constraints.
  3. The passing terminal output (exit code 0) of relevant tests, linters, or typechecks.

### 5. Role Separation (Executor ≠ Auditor)
- The model or session executing code changes NEVER audits its own output. Asking the builder model to "review your work" is prohibited.
- When diff audits are required, dispatch the raw `git diff` to an independent reviewer turn/model (or founder inspection) using a strict pass/fail grading lens.

### 6. Scope Fencing and Immediate Parking
- A step cannot open side-tracks, create unapproved utility packages, or refactor previously settled files.
- Any finding, edge case, or secondary question not strictly required to satisfy the active step is deferred immediately to the ## Parked table. No auto-expansion.

### 7. Design and Asset Constraints
- The landing page is the visual north star. There is NO separate design pass, design token gate, or lookbook.
- Tokens (canvas, gold, type scale, system sans, Geist Mono, Instrument Serif) are transcribed once into a single theme file during the landing page build.
- Logos copy from `evolution/engine/brand`. `DESIGN.md` in that folder stays behind.
- Marketplace, horse listing, and MyStable reuse the established theme file. They do not introduce new tokens or palettes.


## Reference: sandbox/rebuild

Settled 2026-10-13. Updated 2026-10-14. `/home/evo/sandbox/rebuild` is a reference only. It does not become part of this plan's repo, and the new repo does not import it. Its README still aims the prototype at the live monorepo. That aim is ignored.

Two things are taken from it:
1. The pricing ceiling math from `src/lib/pricing.ts` is copied into the engine at step 3. **Critical exception:** `investorReturnPctFrom` falling back to `'75'` is strictly forbidden. The function must return `null` on missing/unassigned split, matching `packages/legal_engine/src/commercial-rules.ts`.
2. The screen copy below is the buy-workflow content. The React components, the Vite app, the drawer, and the old token package are not taken.

The prototype's pay button and its confirm screen are not the flow. Pay leaves for hosted Stripe Checkout. The sale exists when the webhook writes it. A screen that says the buyer already owns the stake, before that write, is not built.

## Buy workflow

Settled 2026-10-13. Step 8 implements this sequence. This section is where the investor checkout is executed. Screen detail is added here, not in a second plan. The screen words come from the rebuild steps. The numbers come from the DSL and the rules module. A logged-out visitor signs in and returns to the same horse. Coming Soon has no Buy Now.

One surface. The same five screens are used on a phone and on a desktop. The layout reflows. There is no second mobile flow and no separate drawer. Controls stay usable on a phone. Stripe Checkout is Stripe's own page.

1. Terms. The horse's legal name. The monthly price for the chosen stake. A stepper bound to that DSL's minimum, step, and maximum, with the dynamic rule: effective_min_stake = min(min_stake, stake_remaining). When stake_remaining < min_stake, allow purchasing the full remaining balance so inventory does not orphan. Four rows: initial payment: 5 months (3 months deposit reserve, 2 months prepaid) times the retail rate; monthly thereafter: 1× monthly share price, billed on the 1st starting Month 3; the lease period, from that DSL's term start and end date; the distribution share, which is that DSL's investor split, of stakes won, from official NZTR results. An empty form's 75% is not shown as this horse's split. The note under the rows: fixed monthly payment, no surprise capital calls, then the refund sentence in the Refund section of `OUTCOME.md`. "Billed monthly on the 1st" may be printed. It does not create an automated charge job. Later monthly charges stay manual. Footer: Evolution Stables is an NZTR-authorised syndicator. The button is "Review Legal Documents".

2. Documents. Title "Review & Sign Documents", for this stake in this horse. The PDS, then the SA, each compiled with the account name. The buyer accepts the PDS, then the SA. The SA acceptance includes these four declarations: not disqualified or suspended under the NZTR Rules of Racing; acquiring the stake as the true beneficial owner; bound by the NZTR Rules of Racing and the Welfare Guidelines; Evolution Stables Limited is the syndicate manager. Both accepts are required. The button then reads "Continue to Identity Verification". The footer says the signed documents are hashed and stored for MyStable. The webhook is what writes the hashes.

3. Identity. If the account is already verified, the buyer confirms they are the account's full name and continues. If not, the engine starts Stripe Identity. The screen asks for a current NZ driver licence or passport. It does not say the buyer is an NZTR-verified member. Identity is Stripe Identity on the account. A failure stops the sale, sends the buyer the KYC-failed email, and does not open Checkout. Footer: encrypted, handled under the NZ Privacy Act 2020.

4. Pay. The screen repeats the initial payment, the monthly amount, the lease period, and the investor split, then says the card is processed by Stripe Checkout. The button sends the browser to the Checkout URL the engine returns. Cancelling Checkout returns to the horse page and writes no sale. A card decline writes no sale and sends no founder email.

5. Return. The browser comes back to MyStable. If the webhook has not written the sale yet, MyStable shows that purchase as pending. It does not say the stake is owned. When the sale row exists, MyStable shows the stake, the amount, and the two documents. An our-fault failure of Checkout or the webhook sends the founder email and leaves no sale.

## Operations

Settled 2026-10-13. `OPERATIONS.md` in this folder is the one operator guide, and the operating law. `OUTCOME.md` remains the product law. `PLAN.md` remains the build law. If the guide and the outcome disagree, the outcome wins and the guide is corrected.

On step 1 the file moves to `engine/OPERATIONS.md` inside `Badders80/engine-v2`. It does not go in the parent `evolution/engine_v2/` folder. The website app does not get a second copy. Each later step adds the click-path for the thing it just built. A procedure is written once, in that file.

## When you say build

Do these in order. Stop at the end of a step if something in the outcome does not fit.

1. Use `Badders80/engine-v2` at `evolution/engine_v2/website`, as in "Accounts" above. Make it private before the first code commit. Move `sandbox/v2/OPERATIONS.md` to `engine/OPERATIONS.md`. Do not `git init` `evolution/engine_v2`. Do not add `CLAUDE.md` or `AGENTS.md`. Do not change `Badders80/evo_02` or its Vercel project. First commit is the two empty apps, that guide, CI that typechecks, lints, and tests, and the pre-commit / CI script `scripts/assert-no-anti-patterns.sh` that scans for and fails on any banned legacy token (`INVENTORY_UUID_BY_SLUG`, `LEGACY_SLUG_ALIASES`, `slugCandidates`, `HORSE_STILLS`, `DEFAULT_CDN_HOST`, `CONTENT_DIR`, `resolveHorseSlug`, `REFUND_WINDOW_DAYS`, `brand_dna`, `orca`).
2. New empty Supabase project. One migration series (`001_initial_schema.sql` upward) for the rows in `OUTCOME.md`: `trainers`, `owners`, `horses`, `dsls`, `accounts`, `sales`. No copied business data. No two migrations share a numeric prefix. No slug-to-id table. Encode Tier 1 Postgres physical constraints:
   - `horses`: `id uuid primary key`, `microchip text unique`, `slug text unique not null`, `sire_name text`, `dam_name text`, `image_count integer not null default 0 check (image_count >= 0)`, `registered boolean not null`. Soft prose strictly limited to `about_horse`, `racing_outlook_and_pedigree`, `race_expectation` (text) and `race_record` (jsonb).
   - `dsls`: `unique (horse_id, end_date)`. `payment_model` enum (`subscription_float`, `upfront`), `close_style` enum (`fourteen_day`, `three_x_remaining`). `status` enum (`coming_soon`, `available`, `fully_allocated`, `completed`), `published boolean not null default false`.
   - `wholesale_monthly_per_percent numeric(10,2) not null check (wholesale_monthly_per_percent > 0)`.
   - `retail_monthly_per_percent numeric(10,2) generated always as (ceil(wholesale_monthly_per_percent * 1.05 * 1.03)) stored`. Writers cannot store a second price.
   - `wholesale_monthly_total numeric(10,2) generated always as (wholesale_monthly_per_percent * 100.0) stored`.
   - `investor_split numeric(5,2)` with no default. `check (investor_split is null or (investor_split >= 50 and investor_split <= 85))`.
   - `listed_stake`, `min_stake`, `step` numeric(5,2) check (`min_stake > 0 and step > 0 and min_stake <= listed_stake and mod(listed_stake, step) = 0`). No magic defaults (`?? 1.0`, `0.5`).
   - Constraint trigger: `status = 'available'` requires `published = true`, `investor_split is not null`, `wholesale_monthly_per_percent > 0`, horse `registered = true`, and `horses.image_count >= 3`.
3. Rules module in `engine/src/lib/pricing.ts`. Copy the ceiling price formula from `sandbox/rebuild/src/lib/pricing.ts`. Shared numbers live here, not on each DSL. Pure functions only. Do not copy the `'75'` fallback: empty input returns `null`. Implement unit test contracts failing CI on drift:
   - `retailPerPercentFromWholesale(65) === 71` and per-1% rate is `71`. `totalForStake(71, 5) === 355`.
   - `retailPerPercentFromWholesale(70) === 76` and per-1% rate is `76`. `totalForStake(76, 5) === 380`.
   - Initial float strictly equals 5 months (3 reserve + 2 prepaid) times per-1% rate ($355 for $71, $380 for $76).
   - `isInvestorReturnPctInRange` strictly accepts [50, 85] and rejects 49 and 86.
   - Missing split returns `null`, never `'75'` or `"75%"`.
   - Stake calculation throws if `stake % step !== 0`. Required step argument, no default.
   - Refund text contract: asserts exact sentence from `OUTCOME.md § Refund` and regex fails on any day count (`/\d+\s+days?/i`).
4. DSL page on the engine. Create, edit, and view. Horse, trainer, and owner are written from that page. They do not get their own screens. A registered horse loads from a loveracing.nz link, then the operator confirms and fills the soft slots. An unregistered horse is typed by hand and cannot be published past Coming Soon. Branding fields from the page are not stored.
5. New R2 bucket configuration:
   - **Production Bucket**: `evolution-v2-horse-images`
   - **File Structure**: `{horse-slug}/{image-number}` (e.g., `lady-ketchikan/01`)
   - **Access**: Public read for horse images, private write
   - **Requirements**: Minimum three images before a DSL can be Available
   - **Database**: Only `image_count` is stored - no image paths
   - **Fallback**: If R2 access is delayed, use Supabase Storage temporarily
   
   Public images only, for the horses that go through this flow. `image_count` is the only image fact in the database. Private KYC and proof-of-address files stay in Supabase Storage, not R2.
   
   **Configuration**: See `/r2-config/README.md` for full setup details and access patterns.
6. Website reads the rows. The landing page is already built, per "How the build session behaves". This step is the marketplace and the horse listing, with the search layer in "Search" above. Press, updates, and the FAQ do not live on the horse, trainer, or owner. Coming Soon is visible and cannot be bought. Available means Buy Now. Unpublished stays off the marketplace and out of the sitemap.
7. Compiler, against the legal authority above. Copy the PDF, the three pro-formas, and the principles note into the repo as the read-only reference set. PDS reads the horse soft text columns (`about_horse`, `racing_outlook_and_pedigree`, `race_expectation`), the trainer bio, and the DSL terms. Strictly forbid filesystem content scans or frontmatter lookups. SA reads hard facts and commercial terms, no soft prose. Promoter line on every document: Alex Baddeley, Evolution Stables Ltd. Payment default, prize freeze, and forfeiture are printed procedures. A system that runs them is not part of this outcome. The self-audit checks both documents against the principles note.
8. Purchase, per "Buy workflow" above. Logged-in buyer. The website modal asks the engine for the PDS and SA compiled with the account name. Buyer accepts. Engine starts Stripe Identity if the account is not verified, and records the result on the account. Engine creates Checkout and returns the URL. Webhook writes the sale: stake, amount, Stripe session id, Identity id, both documents, both hashes. A later edit to the horse does not change a document already sold. Browser returns to MyStable. MyStable reads that account and that user's sales. Checkout does not create another profile. Do not import `sandbox/rebuild`.
9. Four emails, sent by the engine, and no others. Buyer: sale written (stake, amount, link to MyStable). Buyer: KYC failed. Founder: sale succeeded. Founder: checkout or webhook failed for a reason that is ours. A card decline does not send that alert. Later monthly charges and defaults are manual.
10. Preview with the buy path off, on the new Vercel projects. Listings may show Coming Soon. Prove the purchase wiring on a preview, not on the live domain. The flip to Buy Now is a readiness decision. The proof is one real sale after that flip. `evolution-3-0` keeps serving `www.evolutionstables.nz` until then, and the new build does not call back into the old one.

## Before any deletion of the old production data

Not code, and not step 1. These block cleanup only.

- Export and keep the old Supabase project. It holds 10 named people's emails, phones, KYC ids, and signed document hashes, plus the private document buckets. Handle it under the NZ Privacy Act 2020. Do not delete it.
- The founder's live Mulan purchase ($42, refunded, subscription `cancel_at` 2028-05-01) is real. Cancel what is still open, confirm the refund, and keep the invoices and the subscription. Do not delete the Stripe account. Delete only the probe products and the dead test webhook after that check.
- New R2 bucket. Do not reuse the old media keys.

## Acceptance

- One horse sells on v2, end to end, real money, real investor. That is the roll gate.
- Edit the stake in its one home. The listing, the PDS, the SA, and the generated PDF all change. Rename the horse. URLs, image paths, and new documents follow. Sold documents do not.
- No duplicate migration numbers. No slug-to-id lookup anywhere.

## Parked

| Item | Why it waits |
|---|---|
| O6 / A1, the 26 MB registry | Not a requirement. Soft text is on the horse row. |
| O8, performance-profile field list | Race record is one jsonb blob. Read the profile when the entry loader is built. |
| O9 / A7, branding fields in the PDS or SA | Not in the outcome. Deciding later must not be done by porting a frozen package. |
| A6, content stream shape | First slice has the three soft columns only. |
| A11, how many old GitHub repos are public | Re-verify before archiving. The Sep-14 count is stale. |
| Observability vendor, and the email sender that replaces Gmail SMTP | Needed before the public buy path, not before the first commit. |
| Second owner on Supabase, Stripe, R2, Vercel, GitHub, DNS, Google, and Search Console | Account task, at the domain cutover, before the public buy path. |
| Fuller investor profile, reservations, race-by-race table, payout engine, automated monthly charges | Later. They attach to these rows. |
| Brand & Design Token Skinning | Visual presentation layer. Steps 1-8 run with standard unstyled Tailwind wireframes. Custom typography (Geist Mono, Instrument Serif) and palette tokens apply after core engine, data model, checkout, and compiler pass CI. |

## Removed on 2026-10-13

- `evolution/docs/superpowers/plans/2026-10-13-v2-build-plan.md` contradicted this layout and put O6 first. Deleted.
- Empty `evolution/engine_v2/docs/superpowers/plans/` deleted.
- `sandbox/v2/O6-inventory.md` treated the NZTR pro formas as the 26 MB registry. Deleted. The pro-formas are the legal reference set in "Legal authority" above.
