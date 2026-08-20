
# Rates Curve & Swap Pricing Toolkit
**Stage:** Problem Framing & Scoping (Stage 01)

## Problem Statement
Rates desks need quick, defensible pricing and risk figures for vanilla instruments — a swap PV and DV01 to sanity-check a trade or size a hedge — but production pricing systems are built for booking trades, not for one-off research checks. This project builds a lightweight, transparent Python toolkit that turns public rates data (the U.S. Treasury daily par yield curve and SOFR from FRED) into the outputs a sell-side Market Quantitative Analyst produces day to day: a bootstrapped discount curve, a priced vanilla interest rate swap with DV01, and a decomposition of how the curve itself moves. A secondary equity-option module prices a single option expiry with Black-Scholes and backs out implied volatility, so the toolkit covers both of the desk's core pricing patterns.

Because free market swap quotes are not available (see Assumptions), correctness is established by cross-checking against QuantLib as a reference implementation and by self-consistency tests — an at-market swap must price to approximately zero, and the bootstrapped curve must reprice the input par yields.

## Stakeholder & User
- Decides: Rates desk trader, who acts on (or escalates) the number.
- Uses the output: The supporting quant/risk analyst, who runs the check and hands over the result.
- Timing: On-demand, once per session or daily. A research/verification tool, not a real-time execution system.

## Useful Answer & Decision
- Type: Predictive/computational — priced values and risk sensitivities, not a descriptive summary - or causal claim.
- Metric: Swap PV and DV01; discount factors and zero rates by tenor; option price, implied volatility, and Greeks for the secondary module.
- Artifact: A reproducible notebook/toolkit any teammate can re-run against a new day's data.

## Assumptions & Constraints
- Treasury par yields as the risk-free proxy, with single-curve discounting — real desks use dual-curve OIS discounting with a separate forward curve. A deliberate, documented simplification.

- Market swap quotes are not freely available. ICE Swap Rate is licensed, and FRED's swap series (DSWP1–DSWP30) were discontinued in October 2016. Validation therefore uses QuantLib cross-checks and self-consistency tests, not market benchmarks.

- SOFR is used as the floating-leg reference rate; term SOFR is approximated from the overnight series rather than licensed CME Term SOFR fixings.

- Black-Scholes assumptions for the secondary module: European exercise, lognormal returns, flat continuous dividend yield.

- Option chains are available only as a current snapshot, not as history. Any time-series volatility work uses VIX as the implied-vol series and underlying price history for realized vol.
No transaction costs, counterparty/credit risk, or funding valuation adjustments.

## Known Unknowns / Risks
- Curve interpolation may be unstable at very short or very long tenors — will sanity-check bootstrapped forwards for negative or implausible values.

- Day-count and business-day conventions (ACT/360 vs 30/360, modified following) materially change swap PV; getting these wrong is the most likely source of error, so conventions are stated explicitly and tested against QuantLib.

- yfinance depends on an undocumented Yahoo endpoint and breaks periodically — will pin the version, snapshot every pull to data/raw/, and fail loudly rather than silently returning empty chains.

- Treasury publishes no rates on holidays; the ingestion layer must distinguish a missing business day from a genuine data failure.

## Lifecycle Mapping
- <Goal> → Problem Framing & Scoping (Stage 01) → <Deliverable>

- Frame the problem and stakeholder → Stage 01 → Scoping paragraph, README, stakeholder memo

- Reproducible environment → Stage 02 → .env with a live FRED API key, requirements.txt, config helper

- Core data-handling skills → Stage 03 → Summary-stats notebook on curve data + first pricing utility

- Acquire curve and rate data → Stage 04 → Treasury HTML table scrape + FRED API pull, saved to data/raw/

- Storage layer → Stage 05 → CSV (raw) + Parquet (processed), write_df/read_df

- Clean and validate → Stage 06 → Cleaned curve series, holiday and missing-tenor handling

- Later stages (Stage 07–15) → Outlier analysis on rate jumps, curve-shape EDA, tenor/moneyness features, PCA of curve moves into level/slope/curvature (with vol-smile regression as a secondary model), DV01 and risk communication, stakeholder reporting, packaging into src/, and a rerunnable pipeline.


## Repo Plan
data/, src/, notebooks/, docs/

- Raw snapshots land in data/raw/ as CSV and cleaned/derived data in data/processed/ as Parquet
- Reusable curve and pricing functions accumulate in src/
- one notebook per stage in notebooks/
- stakeholder-facing documents in docs/
- generated output reports in reports/
- calibrated curve objects and model parameters in model/
- Updated weekly, in step with each stage's homework

# Stakeholder Brief — <Rates Curve & Swap Pricing Toolkit>
**Audience:** <Rates desk trader / supporting quant-risk analyst> | **Cadence:** <On-demand (once per session or daily)> **Decision Supported:** <Sanity-checking a swap PV or DV01 before acting on it or escalating it>

## Context
A rates trader does quick checks most days — does this swap's DV01 look right, has the curve moved enough to matter for the hedge. Today that means either waiting on the production pricing system, which is built for booking trades and slow to spin up for a one-off, or eyeballing it, which is not defensible when someone later asks how the number was derived. There is no fast, transparent, rerunnable way to answer "what is this worth on today's curve" without setup overhead.

## What You'll Receive
- A bootstrapped discount curve from the latest published Treasury par yields, with zero rates and discount factors by tenor.
- A priced vanilla interest rate swap: PV and DV01, plus how both shift when the curve moves.
- A plain-language note on the assumptions behind every number — single-curve discounting, stated day-count conventions, data as of the last published business day.

## Assumptions & Constraints
- Single-curve discounting off Treasury par yields; production desks use dual-curve OIS discounting.
- Free market swap quotes do not exist, so numbers are validated against QuantLib and self-consistency checks rather than against dealer quotes.
- End-of-day published data — suitable for research and verification, not for live execution.
Day-count and business-day conventions are stated explicitly, since they materially change PV.


