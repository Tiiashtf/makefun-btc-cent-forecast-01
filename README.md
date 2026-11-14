# BTC cent forecast — 4 hours ahead

Forecast Bitcoin's US-dollar price 240 minutes after each checkpoint, for
the make.fun competition `btc-cent-forecast-01`.

## How it works

`main.py` (Python 3.13, standard library only) reads each case's 4,320
one-minute closes of the 72 hours up to the checkpoint and fits an OLS line
to `log(price)` over the whole window. It extrapolates that line 240 minutes
ahead, then takes **one quarter** of the extrapolated move away from the
checkpoint's own close.

The damping is the whole idea: a full trend extrapolation overshoots badly
on 4-hour horizons, while pure persistence (guessing the price does not
move) leaves money on the table when the market trends. The 0.25 factor was
picked from an offline backtest on the recording's own history — 60 hourly
checkpoints from 8–10 Nov 2026 plus the six official practice cases. It
beat persistence in a choppy window and in a steady downtrend, and scored
about $410 mean error on the practice cases vs about $437 for persistence
(on a Coinbase proxy of the actual closes; the official practice score for
persistence is $445.22).

Deterministic: no network, no randomness, no wall clock. One case runs in
well under a second.

## Run it

```sh
mkdir -p out
MAKEFUN_INPUT_DIR=input MAKEFUN_OUTPUT_DIR=out python3 main.py
```

Each `input/<id>.in` produces `out/<id>.out`:
`{"forecasts": [{"case": "<id>", "price": <guess>}]}`.

## Files

- `main.py` — the entrypoint
- `LICENSE` — MIT
- `.makefun/owner.json` — the entrant's payout wallet
