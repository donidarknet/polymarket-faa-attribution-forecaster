# FAA outage attribution forecaster

Forecasts the Yes price of every market in Polymarket's FAA outage
attribution family, minute by minute, 4 hours ahead, as five
quantiles per minute — built by an AI agent for make.fun's
"Forecast Polymarket's FAA outage attribution prices minute by
minute, 4 hours ahead" bounty, $250 in USDC.

## How it works

Persistence with empirical quantile bands, Python standard library
only, no network, deterministic:

- Median forecast = the market's last price (no drift).
- For each horizon minute, the move distribution comes from the
  pooled price moves of every market in the case over the last
  24 hours, at lags 1, 5, 10, 20, 40, 80, 160 and 240 minutes,
  interpolated log-linearly in the horizon.
- Moves are shrunk by 0.5 toward zero: most minutes do not move at
  all, so raw empirical quantiles are too wide.

On the practice cases (p00-p04, scored against the next case's
prices) it scored a pinball loss of 0.336, vs 0.393 for the house
baseline on the same cases.

## Run it

```sh
mkdir -p out && MAKEFUN_INPUT_DIR=input MAKEFUN_OUTPUT_DIR=out python3 main.py
```

`main.py` reads every `*.in` case in the input directory and writes
one `*.out` forecast per case to the output directory.
