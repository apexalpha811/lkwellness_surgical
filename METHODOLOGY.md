# Private investments: what these two briefs are, and how they differ

Written 2026-08-17. Read this before changing anything in this folder.

## What this folder is

Two investor briefs for L&K Wellness with **no lender anywhere in them**. Same business as the
briefs in `ample/` and `surgical/`: same rooms, same menu, same volumes, same revenue, same
operating income. What changes is the right-hand side of the balance sheet. The whole project cost
is raised as equity, so there is no loan, no debt service, no coverage ratio, no rate sensitivity
and no SBA program language.

| | Ample, private | Surgical, private |
|---|---|---|
| Project cost | $4,750,000 | **$5,000,000** |
| Equity raised | $4,750,000 | $5,000,000 |
| Senior debt | none | none |
| Working capital reserve | $1,105,800 | **$1,054,800** |
| Modelled cash trough | $42,467 at month 9 | $607,422 at month 11 |
| Operating income, stabilized | $224,801 /mo, $2,697,611 /yr | $189,541 /mo, $2,274,497 /yr |
| Unlevered yield on equity | 56.8% | 45.5% |
| Cumulative cash by month 36 | $4,589,439 | $3,154,780 |
| Equity payback | month 37, extrapolated | month 46, extrapolated |
| Footprint | 4,925 SF | 5,825 SF |
| Slides | 37 | 41 |

## The three decisions behind them

1. **All equity, no debt at all.** Decided in conversation on 2026-08-17. The alternative
   considered was keeping the same loan and simply renaming it "senior debt", which would have left
   every number untouched. That was rejected: the brief is for investors who are funding the whole
   thing.
2. **Surgical lands on $5,000,000 flat**, achieved by taking $300,000 out of its working capital
   reserve rather than out of any construction, equipment or pre-opening line. Nothing about the
   building changed.
3. **Ample stays at $4,750,000** with its reserve untouched.

## Two things a reader will notice, so they are disclosed rather than buried

**Ample's reserve is far larger than it needs to be.** Removing the debt service shrinks its
modelled cash trough from $269,054 to $42,467, so a $1,105,800 reserve now covers the trough 26
times over. That is roughly $1.1M of a $4.75M raise sitting idle. It is defensible as a cushion
that funds a much slower ramp than the one modelled, without going back for a second raise, and
that is exactly how the disclosures page and the register describe it. If the ask should be lower
instead, cut `EQUITY_RESERVE.ample` in `deck-build/model.js` and the project cost follows.

Surgical does not have this problem: its $1,054,800 reserve covers a $607,422 trough 1.7 times.

**The yield figures are large and narrowly defined.** 56.8% and 45.5% are stabilized annual
operating income divided by the amount raised. They are stated before tax, before any distribution
policy, before maintenance capital and before any assumption about a sale, so they are **not
investor returns**. Every artifact says so in those words. No IRR and no exit multiple appears
anywhere, because nothing in this model supports a terminal value that could be sourced, and
inventing one would be the single easiest thing in these documents to attack.

## How they are built

Exactly the same way as the other five briefs, from the same model. Nothing is retyped, so no
figure here can disagree with the lender version except where the capital stack genuinely differs.

```bash
node deck-build/model.js && node deck-build/build-sites.js && node deck-build/build-assumptions.js && node deck-build/build-deck.js
```

The two scenarios are `ampleEquity` and `surgicalEquity` in `deck-build/model.js`, derived from
`ample` and `surgical` with `Object.assign` and a `dataKey` back to the base, the same pattern the
private-equity variants use. They carry one flag, `allEquity: true`, and every difference in every
artifact hangs off that flag:

- `model.js` drives all debt fields to zero, sets `dscr` to null and an empty `rateBand`, and adds
  `unleveredYield`, `cumulativeCash36` and `paybackMonth`.
- `build-deck.js` swaps the SBA package slide for a capital-and-returns slide, and branches the
  cover, footer, ask, use of funds, ramp, sensitivity band, disclosures, timeline, risks and close.
- `site-template.html` hides the lender framing and renders the same capital section in its place.
  All seven sites still share this one template.
- `build-assumptions.js` swaps the capital-stack table and the rent sensitivity column.

**Do not fork the deck or the template to make a change here.** The whole point of the derivation
is that a fix to the business lands in both versions on the next build. The self-check asserts that
these two scenarios still describe the same building as their base: same revenue, same operating
income, same footprint, same space program, same menu, and no capital line other than the reserve
moved.

## What is unchanged and still open

The rent assumption of $5.50/SF/month NNN still has no broker letter behind it, the surgical
facility fee schedule is still constructed, and the surgeon principal relationship still needs an
operating agreement. All three are disclosed in these registers exactly as they are in the others.
