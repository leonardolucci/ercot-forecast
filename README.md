# ercot-forecast

Probabilistic day-ahead price forecasting for the ERCOT electricity market
(Texas). The goal is a public track record of forecasts published before the
outcome is known, plus a battery-arbitrage layer that turns those forecasts
into charge and discharge decisions. Early stage: see [Status](#status).

## Why

ERCOT combines heavy wind and solar penetration, negative prices and extreme
scarcity spikes. A point forecast hides exactly the hours that matter, so the
target is the whole predictive distribution, and the final question is
whether that uncertainty is worth money to a battery operator.

Design rules:

- **Point-in-time data.** Every record carries an `available_at` timestamp,
  and no feature may use information published after the moment of
  prediction. An automated leakage test in CI will enforce it.
- **Baselines first.** Seasonal naive and LEAR (LASSO-estimated
  autoregressive) are measured before any machine-learning model, and a
  model stays only if it beats them under the same rolling backtest.
- **Probabilistic evaluation.** Pinball loss, CRPS, interval coverage and
  Diebold-Mariano tests, broken down by regime: normal hours, price spikes
  and negative prices.
- **A record that cannot be retouched.** Live forecasts will be frozen before
  market close and stored append-only.

## Status

The repository structure and a reproducible environment are in place; data
ingestion comes next. There are no data, models or forecasts yet, and this
section will say so until there are.

## Reproduce

Requires [Git](https://git-scm.com/) and [uv](https://docs.astral.sh/uv/).
uv finds or downloads the pinned Python version (3.13) by itself.

```bash
git clone https://github.com/leonardolucci/ercot-forecast.git
cd ercot-forecast
uv sync --locked
```

`--locked` makes `uv sync` fail rather than silently update `uv.lock`, so the
environment you get is exactly the one committed.

## Plan

1. **Foundation:** ingestion, point-in-time storage, baselines, a first
   quantile model, and an evaluation with significance tests.
2. **Production:** daily automated forecasts, published and frozen before the
   outcome, with a public dashboard.
3. **Decision:** a battery optimiser driven by the predicted distribution,
   measured against perfect foresight and against the point forecast.

## Contact

Leonardo Lucci - [LinkedIn](https://www.linkedin.com/in/leonardo-lucci)
