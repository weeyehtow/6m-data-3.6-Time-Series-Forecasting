# L06 — Time Series Forecasting

> *Sarah Chen's sixth week at NorthStar Retail. Marcus's question from L05: "Sales are seasonal. Can you forecast next quarter's revenue?" This week she does.*
> By the end of this lesson you will know how to decompose a time series into trend + seasonality + residual, build baseline forecasts with ETS, train a machine-learning forecaster with lag features, and evaluate any of them honestly with the right error metrics.

---

## Before you start — environment setup

> **If this is your first time with this course, follow the [Setup Guide →](./setup.md)**.
>
> **Already set up from L01–L05?** Your `dsai-m3` environment has everything (`statsmodels` for STL/ETS, `scikit-learn` for the ML forecaster, `pandas` for time-series indexing).

---

## Where L06 fits

| Lesson | What you build | What you carry forward |
|---|---|---|
| **L03 — Supervised Foundations** | Logistic regression on labelled data | The Pipeline pattern |
| **L04 — Trees & Ensembles** | Random Forest + Gradient Boosting | The "tune + compare" discipline |
| **L05 — Unsupervised Learning** | Segmentation + anomaly detection | The "find structure without labels" lens |
| **L06 — Time Series Forecasting** *(you are here)* | Decompose → forecast → evaluate revenue | The time-ordered-data toolkit |
| **L07 — Neural Networks** | First neural network | The path to deep learning |

**The narrative thread:** all five prior lessons treated rows as independent. Time-series data isn't independent — **today's revenue depends on yesterday's, last week's, last year's.** That's the conceptual leap.

---

## What you will be able to do by the end

- **Decompose** any time series into trend, seasonality, and residual using STL
- **Build** baseline forecasts (Naive, Seasonal Naive, ETS / Exponential Smoothing)
- **Build** an ML-based forecaster using lag features and a gradient boosting regressor
- **Evaluate** forecasts with the right metrics (MAE, RMSE, MAPE) — and never by shuffling time-ordered data
- **Recognise** when each forecasting method is appropriate — and when forecasting itself is the wrong tool

---

## How class time is structured (~3 hrs)

| Phase | Time | Format |
|---|---|---|
| Concept walkthrough | ~90 min | Instructor uses the [**interactive key-concepts walkthrough →**](https://su-ntu-ctp.github.io/6m-data-3.6-Time-Series-Forecasting/) |
| Hands-on code-alongs | ~90 min | Three notebooks (~25–30 min each) |
| (Self-study after class) | self-paced | Each notebook has a 🟡 Extension section + the assignment |

---

## Your learning path

### Phase 1 — Before class: self-study (~30 min)

**Start here →** [**pre-class.md**](./pre-class.md)

You'll run `01_monday_morning.ipynb` to see NorthStar's daily revenue data + watch a video on STL decomposition + try three mini-exercises.

---

### Phase 2 — In class: hands-on (~3 hrs)

**Short reference & review →** [**lesson.md**](./lesson.md) (overview, key takeaways, forecast-honesty checklist, review Q&A, L07→L10 course map)

**Interactive walkthrough →** [**Key Concepts — interactive page**](https://su-ntu-ctp.github.io/6m-data-3.6-Time-Series-Forecasting/) (the visual walkthrough used in class — revisit any time)

**Notebooks — run in order:**

| # | Notebook | Sarah's day | What you explore |
|---|---|---|---|
| 02 | [`02_decomposition.ipynb`](./notebooks/02_decomposition.ipynb) | Tuesday | STL decomposition · trend · seasonality · residual |
| 03 | [`03_classical_forecasting.ipynb`](./notebooks/03_classical_forecasting.ipynb) | Wednesday | Naive · Seasonal Naive · Exponential Smoothing (ETS) · MAE / RMSE / MAPE |
| 04 | [`04_ml_forecasting.ipynb`](./notebooks/04_ml_forecasting.ipynb) | Thursday | Lag features · HistGradientBoosting regressor · model comparison · 90-day forecast |

---

### Phase 3 — After class: assignment (self-paced)

**Assignment →** [`notebooks/assignment.ipynb`](./notebooks/assignment.ipynb)

Two domains: (A) UK electricity demand (highly seasonal); (B) coffee-shop daily customers (smaller, noisier, weekly seasonality dominant).

**Further reading →** [**reference.md**](./reference.md)

---

## Core vs Optional — what this lesson teaches

**🟢 Core (taught in class):**
- STL decomposition (trend + seasonality + residual)
- Exponential Smoothing / ETS — the pragmatic baseline
- ML-based forecasting with lag features + gradient boosting
- MAE / RMSE / MAPE error metrics

**🟡 Optional (self-study, not assessed):**
- Time-series cross-validation (TimeSeriesSplit / rolling-origin evaluation)
- ARIMA + AutoARIMA
- SARIMA (seasonal ARIMA)
- ACF / PACF and stationarity tests
- Prophet (Facebook's forecasting library)

Optional material lives in [`notebooks/optional_extensions.ipynb`](./notebooks/optional_extensions.ipynb).

---

## File map

```
README.md                              ← You are here
setup.md                               ← One-time environment setup
pre-class.md                           ← Phase 1: 30-min self-study guide
lesson.md                              ← Short reference & review (overview · takeaways · checklist · Q&A · course map)
reference.md                           ← Phase 3: Further reading + glossary
environment.yml                        ← Conda environment spec
docs/
  index.html                           ← Interactive key-concepts walkthrough (GitHub Pages)
notebooks/
  data/
    northstar_daily_revenue.csv        ← 731 days (2024-01-01 to 2025-12-31)
  01_monday_morning.ipynb              ← Pre-class hook: see the seasonality
  02_decomposition.ipynb               ← Part 1: STL decomposition
  03_classical_forecasting.ipynb       ← Part 2: Naive · Seasonal Naive · ETS
  04_ml_forecasting.ipynb              ← Part 3: Lag features + GB + comparison
  assignment.ipynb                     ← After class: electricity + coffee shop
  optional_extensions.ipynb            ← 🟡 ARIMA · SARIMA · Prophet · stationarity
```
