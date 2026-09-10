# ROADMAP — Forecasting and decision system for the ERCOT electricity market

> Living document. Phase 1 is specified; phases 2 and 3 are a sketch that **will
> change** with what you learn. Any detail 12 months out is false precision.

---

## 1. What the finished project looks like

At the end there is **one public URL** where anyone can see:

- The probabilistic day-ahead price forecast for the next 24 hours, published
  **before** the actual outcome exists
- The accumulated record of hits and errors against the baselines, from day one,
  with no possibility of retouching
- The error breakdown by regime: normal hours, price spikes, negative-price hours
- The output of the battery optimiser running on those forecasts, measured in
  dollars captured

And **a repository** with tests, continuous integration, documentation and a
technical report explaining every design decision.

The most valuable asset in the whole project is not the model. It is the
**out-of-sample track record published in advance**. Anyone can show a pretty
backtest; almost nobody can show six months of forecasts that were already
published before the outcome was known. That cannot be faked, and that is why it
is worth something.

---

## 2. Architecture of the final system

### 2.1 Ingestion and storage
- Reproducible pipeline of ERCOT data: day-ahead prices, real-time prices,
  load, wind and solar forecasts published by the operator, actual values,
  generation outages
- **Point-in-time** weather data: not the weather that happened, but the forecast
  that was available at the moment of prediction
- Every record carries an `available_at` stamp. Without that stamp it does not
  enter the system
- Storage in partitioned Parquet + DuckDB for analytical querying
- Automated daily orchestration

### 2.2 Feature layer
- Versioned and documented features
- **Automated temporal leakage test**: no feature may read a record whose
  `available_at` is later than the moment of prediction. This test runs in CI and
  if it fails, the pipeline breaks. It is the piece that separates a serious
  project from one that collapses in the first interview

### 2.3 Models, in strict order
1. Seasonal naive (same hour of the previous day / previous week)
2. LEAR — autoregressive with Lasso, the standard benchmark in the literature
3. Gradient boosting with quantile loss
4. Distributional neural network

No model is added unless it beats the previous one under the same protocol.

### 2.4 Evaluation
- Rolling backtesting with retraining, never a random split
- Probabilistic metrics: pinball loss, CRPS, interval coverage
- Calibration diagrams
- Diebold-Mariano test to tell whether a difference is real or noise
- Results disaggregated by regime

### 2.5 Live forecasting
- Runs on its own every day
- Publishes and freezes the forecast before market close
- Accumulates the historical record immutably

### 2.6 Decision layer — battery arbitrage
- Takes the predicted **distribution**, not a point value
- Optimises charge and discharge with stochastic dynamic programming or MPC
- Measured in dollars against two references: the perfect-foresight policy
  (theoretical ceiling) and the policy that uses only the point forecast
- The point of this layer is to demonstrate something almost nobody demonstrates:
  **that well-modelled uncertainty is worth money**

### 2.7 Communication
- A README that can be read in three minutes and makes clear what you built
- Technical report with the full methodology
- Three or four articles on the hard decisions: why negative prices break
  logarithmic transformations, how point-in-time weather features are built, why
  a high Sharpe ratio is suspicious

---

## 3. Phase walkthrough

### PHASE 1 — Foundation (weeks 1-8, ~80 h)
Ingestion, storage, point-in-time discipline, measured baselines, first quantile
model, honest evaluation.

**Deliverable:** a repository with a reproducible pipeline and a results table
where the model beats the baselines (or does not), with a statistical test.

**Decision gate:** are the baselines beaten significantly? Is the work still
interesting to you? If both are yes, you continue.

### PHASE 2 — Production (months 3-5)
Daily automation, live publishing, dashboard, start of the real track record.
Stronger models.

**Deliverable:** a public URL running on its own, accumulating history.

**This is the phase that makes you hireable.** A system running by itself in
production is a completely different signal from a notebook.

### PHASE 3 — Decision (months 6-9)
Battery optimiser on the predicted distribution. Quantification of the economic
value of uncertainty.

**Deliverable:** a report with the value captured vs. the two references.

**Decision gate:** here you weigh career vs. product, with six months of real
work behind you instead of a blank page.

### PHASE 4 — Optional, depending on what you decide at the previous gate (months 9-12)

**Branch A — Career.** Published technical report, articles, applications. The
live track record is your calling card.

**Branch B — Product.** Honest warning: forecasts are a commodity, nobody pays
for them. What gets paid for is the decision tool of a specific player with a
specific problem. To find out who that player is you have to talk to people in
the sector, and to get their attention you need this already built. That is why
this branch is here and not at the start.

Both branches share everything above. There is nothing to choose today.

---

## 4. What will change

- The chosen market, if a serious data limitation shows up in ERCOT
- The model family, depending on what actually works
- The timeline: 10 h/week is an assumption, not a promise
- The whole of Phase 4, which depends on information you do not have yet

What does **not** change: the order. Data before features, features before
models, baseline before ML, forecast before decision. That order is not
bureaucracy — each stage makes the next one verifiable.

---

## 5. Operating rule

No new phase opens until the previous one has its deliverable finished and
written up. If a better idea shows up halfway through, it goes into an ideas file
and is assessed at the next decision gate, not before.
