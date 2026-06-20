# Setup — L06 — Time Series Forecasting

> One-time environment setup. **If you already set up the `dsai-m3` environment for L01–L05, you can skip this — L06 introduces no new dependencies beyond the L03 baseline (STL/ETS come from `statsmodels`, the ML forecaster from scikit-learn, both already in the environment). Just clone this repo and pick the `dsai-m3` kernel.**

This guide gets you from "fresh machine" to "running the L06 notebooks". It takes about 15 minutes the first time.

---

## 1. Prerequisites

You need:

- **Git** — to clone this repository
- **Miniconda** (or Anaconda) — to create the Python environment. [Install Miniconda →](https://docs.conda.io/projects/miniconda/en/latest/)
- **VS Code** — with the Python and Jupyter extensions. [Download VS Code →](https://code.visualstudio.com/)

Check git and conda are working:

```bash
git --version
conda --version
```

---

## 2. Clone this repository

```bash
git clone https://github.com/su-ntu-ctp/6m-data-3.6-Time-Series-Forecasting.git
cd 6m-data-3.6-Time-Series-Forecasting
```

---

## 3. Create the conda environment

From inside the `6m-data-3.6-Time-Series-Forecasting` folder:

```bash
conda env create -f environment.yml
```

This creates an environment called **`dsai-m3`** with Python, pandas, numpy, matplotlib, scikit-learn, statsmodels, and Jupyter. It takes 3–5 minutes.

> **Already have a `dsai-m3` environment from L01–L05?** You don't need to recreate it. L06 introduces no new dependencies — STL and ETS ship with `statsmodels`, and `HistGradientBoostingRegressor` ships with scikit-learn, both already in your environment. Prophet is optional and gated behind a try-import in `optional_extensions.ipynb`.

Activate the environment:

```bash
conda activate dsai-m3
```

---

## 4. Open the project in VS Code

```bash
code .
```

When VS Code opens, it may prompt you to install recommended extensions (Python, Jupyter). Accept.

---

## 5. Select the `dsai-m3` kernel

1. Open `notebooks/01_monday_morning.ipynb`.
2. In the top-right of the notebook, click **Select Kernel**.
3. Choose **Python Environments → dsai-m3**.

If `dsai-m3` doesn't appear, restart VS Code and try again. If it still doesn't appear, run `conda env list` in a terminal to confirm the environment exists.

---

## 6. Smoke test

In `01_monday_morning.ipynb`, run the first cell (the imports). If it completes without errors, you're set.

If you see `ModuleNotFoundError`, the wrong kernel is selected — go back to Step 5.

---

## Troubleshooting

**`conda: command not found`** → Miniconda isn't installed or isn't on your PATH. Reinstall Miniconda and restart your terminal.

**`environment.yml` solver hangs** → Try `conda env create -f environment.yml --solver=libmamba`. If that still hangs, delete the env (`conda env remove -n dsai-m3`) and retry.

**Kernel doesn't appear in VS Code** → Run `python -m ipykernel install --user --name dsai-m3` from inside the activated environment.

**Notebooks run but plots don't show** → Make sure you have `%matplotlib inline` at the top, or use the VS Code "Run Cell" button (not just `Shift+Enter` in a stale terminal).

---

Once setup is done, head back to **[README.md](./README.md)** → Phase 1.
