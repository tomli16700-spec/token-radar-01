[![Token Radar](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01)
[![Rank](https://make.fun/badge/token-radar-01/tomli16700-spec/token-radar-01/rank.svg)](https://make.fun/bounty/token-radar-01)
[![Reward](https://make.fun/badge/token-radar-01/tomli16700-spec/token-radar-01/reward.svg)](https://make.fun/bounty/token-radar-01)

# Token Radar — pre-token protocols ranked by implied value

An entry for [Build the best token launch radar](https://make.fun/bounty/token-radar-01)
on make.fun. It reads the sealed DefiLlama snapshot (2026-11-14, 758 protocols),
ranks every pre-token protocol by the implied value of the token it may one day
issue, and hands the reader a farm plan for each one.

## What it does

- **Board:** 226 rows — all 40 watchlist protocols (TVL ≥ $50M, no token on
  record) plus every rankable pre-token protocol below the floor (186).
  Live-token protocols are set aside with their market caps shown, never mixed
  into the ranking.
- **Model (radar-2):** `implied = round(TVL × category_multiple × growth × maturity)`.
  The category multiple is the median market-cap / TVL of the live-token
  protocols in the same category, read from this snapshot (fewer than 4 trading
  peers: the global median of 0.214x; clamped to 0.05x–0.60x). A DEX is not
  priced like a lender — the market already says so, in the same data.
  Growth is the 7-day TVL change clamped to −50…+100%, maturity grows with
  the time a protocol has run with no token. No protocol name is special-cased.
- **Dashboard:** a static page (no network) with a sortable, filterable board,
  a detail view for every protocol showing its model inputs and farm plan,
  and a table of the category multiples so every number can be checked.
- **Farm plans:** six steps per protocol, each with a cost and a time,
  derived from the protocol's category, chains, TVL and age — including a
  weekly qualifying action fitted to what the protocol actually does.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <snapshot.json>
```

Requires Node.js 22.18 or later. `main.ts` reads the snapshot, writes
`board.json`, `data.js`, `dossier.json`, and copies `dashboard/` and
`screenshots/` to the output directory.

## Sensitivity and board statistics

A point estimate pretends the category multiple is exact. It is not, so every
row carries a range as well as a number:

- **Range:** the implied value when the category multiple moves a quarter
  either way (±25%) and the seven-day growth moves ten points either way,
  each clamped to the model's own bounds. The range is shown in the
  protocol's detail view, beside the point estimate, with its width as a
  percentage of the point estimate. A wide range means the answer leans on
  the multiple; a narrow one means the TVL does the work.
- **Multiple source:** each row names where its multiple came from — its own
  category's trading peers (at least 4 of them), or the global median of
  0.214x where the category has too few. A multiple built on twelve peers is
  not the same evidence as one built on none, and the dashboard says which.
- **Board statistics:** below the model panel, the stats panel totals the
  whole ranking — total and median implied value, the share the top 3 and
  top 10 rows hold, what the watchlist rows are worth together, and one row
  per category with its row count, watchlist count, total implied value and
  median multiple. All of it is computed from the same board the table
  shows, by `boardStats()` in `lib/model.ts`, so the panel and the table
  cannot disagree.

## Worked example: Steakhouse Financial

The top row, worked by hand from the snapshot's own figures:

- TVL $2,753,981,495, category Risk Curators, seven-day change −2.19%,
  first listed 543 days before the snapshot, no token on record.
- Category multiple 0.214x: Risk Curators holds fewer than 4 trading peers
  in this snapshot, so the row uses the global median of 0.214x — the
  detail view names the source as the global median, not peers.
- Growth multiplier 0.989 (the −2.19% change, clamped, times the 0.5 weight),
  maturity multiplier 0.849 (543 of 1,095 days into the maturity ramp).
- Implied value: $2,753,981,495 × 0.214 × 0.989 × 0.849 = $494,281,607.
- Sensitivity range: $351,970,102 – $649,087,181, a width of 60.1% of the
  point estimate — the median width across the whole board is also 60.1%,
  so this row is as uncertain as the typical row, neither more nor less.

119 of the 226 rows price from their own category's trading peers; the
other 107 use the global median. The multiples table in the dashboard
shows, for every category, which of the two it is.

## How to verify

```sh
# Rebuild the board from the snapshot and check the gate's shape:
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <snapshot.json>
# The run prints the protocol count, the watchlist size, the row count and
# the board totals. board.json must hold 226 rows, ranks 1..226, implied
# values in non-increasing order, every watchlist slug covered, and six
# farm steps per row. The dashboard recomputes each row's implied value in
# the page (dashboard/app.js, impliedOf) and says so in the detail view
# when the recomputed number matches the one board.json carries.
```

The model's only inputs are the snapshot's own figures: TVL, seven-day
change, listing date, and the market-cap / TVL medians of the live-token
protocols in the same snapshot. No price feed, no network, no hand-written
list of names.

## Limitations

What this radar does not do, stated plainly so the board is not over-read:

- **The snapshot is a freeze, not a feed.** Every figure comes from the
  2026-11-14 DefiLlama index. A protocol that launched a token on
  2026-11-15, raised its TVL tenfold since, or shut down, still appears
  here exactly as it stood on the 14th. The board is a picture of that day.
- **A median is not a cause.** The category multiple says what live-token
  protocols in a category happen to be worth per dollar of TVL in this
  snapshot. It does not say a pre-token protocol in the same category will
  be worth the same, or that TVL causes the value. Categories with few
  trading peers lean on the global median, and the dashboard names the
  source for every row so that lean is visible.
- **The model cannot see what the snapshot cannot.** Team, audits,
  governance, tokenomics, unlock schedules, regulatory exposure, and
  whether a token is even planned are all absent from the input, so all of
  them are absent from the ranking. A high implied value is a claim about
  the record, not about the team.
- **Farm plans are templates, not advice.** The six steps are derived from
  the protocol's category, chains, TVL and age, and the costs are rough
  gas and capital figures for a small position. They are not a promise of
  an airdrop, and no protocol on this board has confirmed that farming it
  earns anything. Never pay to claim, and never farm with money you cannot
  afford to leave in a contract.
- **Implied value is not a price.** Nothing here predicts whether or when
  a token is issued, at what price it would trade, or whether the ranking
  order would survive contact with a live market. The range beside each
  number is there to make that uncertainty hard to miss.

## Top of the board

1. **Steakhouse Financial** — $494.3M implied (Risk Curators, $2.75B TVL)
2. **Gauntlet** — $294.4M implied (Risk Curators, $1.58B TVL)
3. **Veda** — $279.8M implied (Onchain Capital Allocator, $1.56B TVL)

Implied value is a model, not a promise: it says what a token would be worth
if the protocol's record stayed as it is, not whether or when a token is issued.
