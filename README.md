# Predicting Malaria Incidence in Mozambique with Machine Learning

Code for the paper **"A Machine Learning Approach to Predicting Malaria Incidence in Mozambique"** (Milky Girma; submitted to the *National High School Journal of Science*).

The study asks whether machine learning models can forecast **monthly, village-level** malaria incidence more accurately than a simple persistence baseline (last month's incidence), and whether adding village-level information — a single historical-incidence feature or a composite Malaria Proneness Index (MPI) — improves those forecasts.

## Data

| Source | What it provides | Link |
|---|---|---|
| PATH, NgenIRS Project — Mopeia passive case detection | Monthly malaria cases, tests, and spray status for 185 villages in Mopeia, Mozambique (Dec 2016 – Oct 2018) | https://data.humdata.org/dataset/2fcaba53-eb4c-43e0-8cba-aa6ee0108844 |
| NASA POWER (Langley Research Center) | Monthly rainfall, temperature (mean/min/max) and relative humidity for the grid cell containing Mopeia town (queried at 17.98°S, 35.71°E; cell centred at 18.0°S, 35.625°E) | https://power.larc.nasa.gov/ |

After cleaning and dropping each village's first month, the analysis uses **3,676 village-month observations from 185 villages** (January 2017 – October 2018).

## Method

- **Target:** monthly malaria incidence per 1,000 residents.
- **Features (23):** spray status, missing-distance flag, current humidity and temperatures, 2–4-month lags of rainfall, humidity and temperatures, previous-month incidence, and a village-level feature.
- **Chronological splits:** 70/30 by month is the primary design (train Jan 2017 – Mar 2018, test Apr – Oct 2018); 60/40 and 80/20 are reported as checks on either side.
- **Leakage control:** village-level features use each village's earlier months only; scaling (StandardScaler) and min-max scaling are fitted on the training period only; no random shuffling.
- **Choosing the village-level feature:** five candidates (none, heuristic composite MPI, equal weights, log-equal, past incidence only) were fixed in advance and compared by rolling-origin validation on the last four training months. Past incidence only — *village historical incidence* — was selected before any test-set evaluation.
- **Models:** OLS, Ridge, Lasso, ElasticNet, Decision Tree, Random Forest, KNN; hyperparameters tuned with GridSearchCV over the same rolling-origin folds.
- **Baselines:** global training mean, village training mean, and persistence (previous month).
- **Uncertainty:** 95% confidence intervals for each gain from a village-level bootstrap (2,000 resamples).

## Main results (70/30 split)

- Best model: **Ridge with village historical incidence, test R² = 68.9%** — 9.2 points above persistence (59.7%) and 21.6 points above the village-mean baseline (47.3%).
- The best model beat persistence at every split (by 1.9, 9.2 and 17.6 points at 60/40, 70/30 and 80/20).
- Village historical incidence improved Lasso by 2.8 points at 70/30 and 3.0 at 80/20 (95% CIs excluding zero); tree-based models did not consistently benefit.
- The hand-weighted composite MPI added under one point to Lasso and reduced Random Forest accuracy.

Full tables and discussion are in the paper.

## Repository structure

| Notebook | What it does | Paper output |
|---|---|---|
| `1_Data_Cleaning.ipynb` | Loads and cleans the HDX data; imputes and flags missing distance | Data preparation |
| `2_Climate Merging and Features.ipynb` | Merges NASA POWER climate data; builds lags and the past-only village feature | Table 1 |
| `3_Exploratory Model Training.ipynb` | Early exploration; not part of the paper | None |
| `4_Models And Analysis.ipynb` | Models, tuning, baselines, feature validation, bootstrap, one-hot comparison, composite versions, all figures | Tables 2–8, Figures 1–4 |

Run notebooks 1, 2 and 4 in order (3 is optional and not part of the paper). Random seeds are fixed: 42 for models, 0 for the bootstrap.

To reproduce the 60/40 and 80/20 results, change the split fraction `0.7` to `0.6` or `0.8` in notebook 2 (`split_point`) and notebook 4 (`split_point_date`), then rerun both.

## Limitations

The data cover one district and 22 months, and all villages share one climate series (village locations are anonymized), so climate effects cannot be separated from the seasonal cycle. The target is *recorded* incidence under passive case detection, which likely under-counts villages far from health facilities. See the paper's Limitations section for details.
