[![make.fun · gta6-twitch-ccu-forecast-01](https://make.fun/badge/gta6-twitch-ccu-forecast-01.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01?utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01)
[![rank](https://make.fun/badge/gta6-twitch-ccu-forecast-01/nofal051/gta6-twitch-ccu-forecast-01/rank.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01)
[![reward](https://make.fun/badge/gta6-twitch-ccu-forecast-01/nofal051/gta6-twitch-ccu-forecast-01/reward.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01?utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01)

# GTA VI Twitch CCU forecast — make.fun entry

Forecasts, hour by hour, how many people watch Grand Theft Auto VI live
on Twitch over the 24 UTC hours of its release day (2026-11-19), as five
quantiles per hour (5%, 25%, 50%, 75%, 95%), from the first hour's print
plus the past big launches shipped in the case file.

## Method

For every past launch in the case's `comparables` and every hour h = 2..24
of its release day, form the log ratio `ln(viewers_h / viewers_1)`. The
hour-1 level is the strongest predictor of how much a launch day grows:
across the kit's comparables, `corr(ln(viewers_1), ln(peak/viewers_1))`
is −0.94 — launches whose category was nearly empty at 00:00 UTC (rolling
midnight unlock) explode by US prime time, while simultaneous worldwide
launches are already near peak in hour 1. So per hour we fit OLS of the
log ratio on `ln(viewers_1)` and take the fitted value as the log-median;
the five quantiles come from that hour's regression residuals, widened
×1.15. The predictor is clamped to the comparables' observed range
extended by 1.0 log unit. Everything is fitted at run time from the case
file — no network, no hard-coded curves, standard library only.

Leave-one-launch-out pinball loss on the kit's 37 usable comparables:
**0.437** (0.326 for launches with hour-1 prints of 10k–500k, the regime
the practice launches sit in), vs **0.930** (0.370 in-regime) for the
persistence baseline. The house baseline scores 0.387 on the practice.

## Run

```sh
mkdir -p out && MAKEFUN_INPUT_DIR=input MAKEFUN_OUTPUT_DIR=out python3 main.py
```

Reads every `*.in` in the input dir, writes `<id>.out` per case:
`{"hours": [{"hour": 2, "q": [q05, q25, q50, q75, q95]}, ...]}`.
Deterministic, ~0.3 s for the 4 practice cases, well under the 8 s cap.

## Files

- `main.py` — the whole entry (single file, Python 3 stdlib only)
- `LICENSE` — MIT
- `.makefun/owner.json` — payout wallet (Solana)
