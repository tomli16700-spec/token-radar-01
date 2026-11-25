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

## Top of the board

1. **Steakhouse Financial** — $494.3M implied (Risk Curators, $2.75B TVL)
2. **Gauntlet** — $294.4M implied (Risk Curators, $1.58B TVL)
3. **Veda** — $279.8M implied (Onchain Capital Allocator, $1.56B TVL)

Implied value is a model, not a promise: it says what a token would be worth
if the protocol's record stayed as it is, not whether or when a token is issued.
