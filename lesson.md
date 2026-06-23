# Lesson — L06 Time Series Forecasting

> **Chapter 6 of the NorthStar Retail story.** *Sarah Chen · Customer Experience Analyst · June 2023.*
> Marcus's brief from end-of-L05: *"Sales are seasonal. Can you forecast next quarter's revenue?"* Sarah opens `northstar_daily_revenue.csv` — 2 years of daily revenue, 731 rows. By Friday she has to show a 90-day forecast with honest error bars and a recommendation on which method to put into production.

This document is a **short reference** — the lesson itself is taught in the notebooks. Read it for orientation before class, then come back to it for the takeaways, the forecast-honesty checklist, the review questions, and the course map.

---

## How L06 is taught

| Stage | Where to go |
|---|---|
| **Pre-class** | `pre-class.md` + `notebooks/01_monday_morning.ipynb` |
| **In-class — Part 1: STL Decomposition** | `notebooks/02_decomposition.ipynb` |
| **In-class — Part 2: Classical Forecasting (Naive · ETS)** | `notebooks/03_classical_forecasting.ipynb` |
| **In-class — Part 3: ML Forecasting (Lag features + GB)** | `notebooks/04_ml_forecasting.ipynb` |
| **Self-study** | `notebooks/assignment.ipynb` + `notebooks/optional_extensions.ipynb` |
| **Reference & review** | This document |

The notebooks are the spine. Run them in order. Come back here for the consolidated takeaways and the review questions.

---

## Overview

Every supervised lesson before L06 assumed rows were independent — you could shuffle them, k-fold cross-validate, and the order didn't matter. Sarah's daily revenue file breaks that assumption: today's number depends on yesterday's, on the same day last week, and on the same day last year. By Friday she will hold three new tools: **STL decomposition** (to separate trend, seasonality, and noise), **classical forecasting with ETS/Holt-Winters** (the pragmatic baseline every team should run first), and **ML forecasting with lag features** (the modern industry default when you have exogenous signals or multiple seasonalities). Every future-facing number she ever ships — revenue forecasts, demand plans, capacity decisions — passes through these four ideas.

---

## Key takeaways

1. **Time series breaks the i.i.d. assumption.** (*i.i.d.* = **independent and identically distributed** — the assumption behind every earlier lesson that each row is drawn independently from the same distribution, so order carries no information and rows can be shuffled freely.) Today depends on yesterday, last week, and last year. That single fact rules out random shuffling, k-fold cross-validation, and any pipeline that lets future data leak into the past.
2. **Decompose before you forecast.** Every series is trend + seasonality + residual. STL separates them; if the residual still oscillates you missed a seasonality and the forecast will inherit that error.
3. **Always run a trivial baseline first.** Naive (repeat the last value) and Seasonal Naive (repeat the same day from one cycle ago) are the bar every fancier model must clear. If they win, the simple model *is* the answer.
4. **ETS / Holt-Winters is the pragmatic default for classical forecasting.** It models level + trend + seasonality with three intuitive knobs, fits in seconds, and is hard to beat on clean, single-seasonality series.
5. **For multiple seasonalities or exogenous signals, reframe as tabular regression.** Build lag features (`lag_1`, `lag_7`, `lag_365`) plus calendar features and let HistGradientBoosting do the rest. Same sklearn pipeline you already know.
6. **Pick the metric your audience will read.** MAE for "how wrong on average", RMSE when large errors matter disproportionately, MAPE for percentages — but MAPE explodes when actuals are near zero, so check first.

> **Optional extension — evaluating across windows.** Rolling time-series cross-validation (`TimeSeriesSplit`, never `KFold`) averages a model's error over several walk-forward windows so a single lucky or unlucky split can't flatter it. It's covered in the extension notebook and is not assessed this run.

---

## Forecasting honestly — a checklist

Before you ship a forecast — into a dashboard, a board pack, a capacity plan — run it through this three-step check:

1. **Did you decompose first?** Look at trend + seasonality + residual before fitting anything. If the residual still has a visible pattern, the seasonal periods are wrong and every model downstream will inherit that miss.
2. **Did a trivial baseline lose?** Naive and Seasonal Naive must both be on the comparison table with the same train/test split and the same metric. If your ML model can't beat them, ship the baseline — it's cheaper, simpler, and equally honest.
3. **Did you avoid a single lucky split?** Never shuffle a forecast — random k-fold leaks the future into the past, and one test window (a holiday slice, say) can flatter or punish any model. Quote a range, not a single point — forecast uncertainty grows with the horizon. *(Optional: average across rolling `TimeSeriesSplit` windows — see the extension notebook.)*

Skip any of these and your forecast is a confident-sounding guess — the most expensive kind of mistake in planning.

---

## Check your understanding

Work through these after finishing the three Part notebooks. Attempt each question on your own first.

### Part 1 — Decomposition

**Q1.** Your STL residual has a clear monthly oscillation. What does that mean?

> *Sample answer:* STL only modelled the seasonality you specified in `period`. A leftover monthly pattern means there's monthly seasonality the model didn't capture. Run STL with a longer period (e.g., 30) or use a different decomposition (e.g., MSTL for multiple seasonalities).

**Q2.** Why does STL plot four panels, not three?

> *Sample answer:* The fourth panel is the original series. STL plots: original, trend, seasonal, residual — so you can visually verify the additive decomposition (trend + seasonal + residual ≈ original).

**Q3.** Should you use STL on log-transformed data?

> *Sample answer:* If the seasonality grows with the level (multiplicative seasonality), yes. Log-transform first, then decompose, then exponentiate the forecast. STL itself assumes additive components.

### Part 2 — Classical forecasting

**Q4.** Seasonal Naive beats your fancy ETS model on MAPE. What's happening?

> *Sample answer:* Your data is dominated by stable seasonality and the trend is weak. Naive captures the seasonality perfectly because it just looks back one cycle. ETS adds parameters (trend, smoothing) but the parameters didn't help on this data. Sometimes the simple baseline wins — that's a feature, not a bug.

**Q5.** ETS converged poorly and gave a warning. Should you trust the forecast?

> *Sample answer:* Sometimes. Convergence warnings often mean the data is too short, too noisy, or has structural breaks the model can't fit. Either: (a) get more data, (b) switch to the gradient-boosting lag-feature model (`HistGradientBoostingRegressor`, as used in the notebook), or (c) accept the warning if the held-out forecast is still good.

### Part 3 — ML-based forecasting

**Q6.** Your GB forecast has RMSE = £500 in training and RMSE = £2000 on a 30-day held-out test. What's happening?

> *Sample answer:* Overfitting OR the test period has a different regime (e.g., trained on regular months but tested through a holiday season). Look at the residuals on test — if they have a systematic pattern (consistently low or biased), the model isn't generalising. Try: more regularisation (smaller max_iter), more lag features, or check if the test period is genuinely different from training.

**Q7.** Should you scale lag features before feeding them to HistGradientBoosting?

> *Sample answer:* Trees don't care about scale. You don't need to scale for tree-based models. (For neural networks, yes.)

**Q8.** Your ML forecaster includes `lag_1`, `lag_7`, `lag_30`. Should you add `lag_2`, `lag_3`, ...?

> *Sample answer:* Probably not. lag_1 already captures recent state. lag_2 and lag_3 are very correlated with lag_1 (lag-1 autocorrelation is high). They add columns without much new information. Stick with biologically meaningful lags: 1 (yesterday), 7 (last week), 30 (last month), 365 (last year). Add more only if you have data-specific reason.

---

## Where L06 fits in the course

L06 is the toolkit for any number that points forward in time — forecasts, plans, capacity decisions. Its ideas resurface every time future data is involved.

| Lesson | How L06 shows up |
|---|---|
| **L07 — Neural Networks** | RNNs/LSTMs are the deep-learning answer to sequential data — same no-shuffling, no-future-leakage discipline applies, just a richer model class. |
| **L08 — Computer Vision** | Video and sensor streams are time series of images; STL-style decomposition reappears as temporal smoothing and motion vs background separation. |
| **L09 — NLP & Embeddings** | Token sequences are time series of symbols — causal masks and rolling-origin evaluation are the same idea as `TimeSeriesSplit`. |
| **L10 — Transformers & GenAI** | Forecast uncertainty becomes generation uncertainty; "predict the next value with a confidence band" generalises to "sample the next token from a distribution". |

---

> *"Great. Now — what if we want to predict whether a customer who STARTED shopping will COMPLETE checkout? Like, in real time as they shop?"* — Marcus, after Sarah's Friday forecast presentation.
>
> That question — *prediction from sequential customer behaviour using neural networks* — is the engine of **L07 (Neural Networks & Deep Learning).**
