# football-stat-translation

**A Machine Learning Framework for Cross-League Per-90 Statistic Translation in Elite Football: A Comparative Model Zoo with Conformal Prediction**

*Under review — Journal of Quantitative Analysis in Sports (JQAS)*

---

## Overview

This repository contains the code and supplementary materials for the paper. We present the first machine learning framework specifically designed for cross-league per-90 statistic translation, covering all 25 directional pairs among the Big Five European leagues (Premier League, La Liga, Bundesliga, Serie A, Ligue 1) across seven seasons (2017/18–2023/24).

The framework trains a zoo of 12 models per position group to predict the log-ratio of a player's per-90 statistic in the destination league relative to the source league. A three-stage conformal prediction pipeline provides distribution-free, finite-sample 90% prediction intervals.

### Key Results

- **Mean MAE:** 0.3325 (log-ratio scale) on held-out test bridges
- **Conformal coverage:** 9/12 statistics ≥ 90% nominal (mean empirical coverage 0.932)
- **Beats baselines:** +6.1% MAE over naive mean, +7.6% over pair mean
- **Best models:** CatBoost dominates (5/12 stats), GBM-Huber (heavy-tailed MID PrgP), LightGBM (DEF Int with interaction feature)

---

## Repository Structure

```
football-stat-translation/
│
├── code/
│   ├── MLCLSTData.ipynb        # Data pipeline notebook
│   └── MLCLST.ipynb            # ML modelling notebook
│
├── results/
│   ├── outputdata.pdf          # Data pipeline output (rendered)
│   └── outputmlclst.pdf        # ML modelling output (rendered)
│
├── requirements.txt
├── LICENSE
└── README.md
```

---

## Methodology

### Data

Player per-90 statistics sourced from [FBref](https://fbref.com) (via Kaggle CSVs). Transfer bridges link a player's stats in their source season to their destination season, filtered to a minimum of 5 qualifying 90s at both ends.

| Position Group | Stats | Train (n) | Test (n) |
|---|---|---|---|
| FWD | KP, PrgP, PrgC, SCA | 202 | 82 |
| MID | KP, PrgP, PrgC, SCA | 255 | 109 |
| DEF | TklW, Int, Clr, Blocks | 257 | 135 |

### Features (6)

`Age`, `90s_src`, `League_ID_src`, `League_ID_trg`, `UEFA_coeff_delta`, `{stat}_src_per90`

### Model Zoo (12)

Ridge, Lasso, ElasticNet, BayesianRidge, RandomForest, GBM, XGBoost, LightGBM, CatBoost, MLP, GBM-Huber, XGBoost-Huber

### Conformal Prediction Pipeline

| Version | Calibration | Coverage |
|---|---|---|
| V1 (baseline) | 20% random holdout | 52.5% |
| V2 | 30% + interaction feature | 83.3% |
| Final (Mondrian) | League-stratified | 93.2% (mean) |

---

## Reproducing the Results

### 1. Install dependencies

```bash
pip install -r requirements.txt
```

### 2. Data pipeline

Run `code/MLCLSTData.ipynb` end-to-end. This outputs the six bridge CSVs (`ml_bridge_train/test_{fwd,mid,def}.csv`) to the `results/` folder.

### 3. Modelling

Run `code/MLCLST.ipynb` end-to-end. All results CSVs and publication figures are saved to `results/`.

> **Note:** The data pipeline must be run before the modelling notebook. All random seeds are fixed (`RANDOM_STATE = 42`).

---

## Data Availability

All player performance data used in this study are publicly available from [FBref](https://fbref.com). Raw CSVs are not included in this repository due to size; the bridge CSVs output by the data pipeline notebook are the direct inputs to the modelling notebook.

---

## Citation

If you use this code or methodology, please cite:

```bibtex
@article{shaikh2026football,
  title   = {A Machine Learning Framework for Cross-League Per-90 Statistic
             Translation in Elite Football: A Comparative Model Zoo with
             Conformal Prediction},
  author  = {Shaikh, M. A.},
  journal = {Journal of Quantitative Analysis in Sports},
  year    = {2026},
  note    = {Under review}
}
```

---

## License

MIT License — see [LICENSE](LICENSE) for details.
