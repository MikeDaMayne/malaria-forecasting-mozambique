# Predicting Malaria Incidence in Mozambique with Machine Learning

Code for the paper **"A Machine Learning Approach to Predicting Malaria Incidence in Mozambique"** (Milky Girma; submitted to the *National High School Journal of Science*).

The study asks whether machine learning models can forecast **monthly, village-level** malaria incidence more accurately than a simple persistence baseline (last month's incidence), and whether adding village-level information — a single historical-incidence feature or a composite Malaria Proneness Index (MPI) — improves those forecasts.

## Data

| Source | What it provides | Link |
|---|---|---|
| PATH, NgenIRS Project — Mopeia passive case detection | Monthly malaria cases, tests, and spray status for 185 villages in Mopeia, Mozambique (Dec 2016 – Oct 2018) | https://data.humdata.org/dataset/2fcaba53-eb4c-43e0-8cba-aa6ee0108844 |
| NASA POWER (Langley Research Center) | Monthly rainfall, temperature (mean/min/max) and relative humidity for the grid cell containing Mopeia town (queried at 17.98°S, 35.71°E; cell centred at 18.0°S, 35.625°E) | https://power.larc.nasa.gov/ |

Download the epidemiological dataset from HDX and place it in `data/` before running the notebooks. <!-- edit the folder name if yours differs -->

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

<!-- Fill in with your actual notebook names -->

| Notebook | What it does | Paper output |
|---|---|---|
| `01_...ipynb` | Load and clean the HDX data; merge NASA POWER climate data | Table 1, Figure 1 |
| `02_...ipynb` | Lag features and village-level feature / MPI versions | Table 2 |
| `03_...ipynb` | Model training, tuning and baselines | Tables 3, 4, 6 |
| `04_...ipynb` | Village-level bootstrap and one-hot comparison | Tables 5, 7, 8 |
| `05_...ipynb` | Correlations, Lasso coefficients, Random Forest importances | Figures 2–4 |

## Reproducing

Tested with Python 3.14.7 and scikit-learn 1.9.1.

```bash
pip install scikit-learn==1.9.1 pandas numpy matplotlib seaborn jupyter
```

Run the notebooks in order. Random seeds are fixed: 42 for models, 0 for the bootstrap.

## Limitations

The data cover one district and 22 months, and all villages share one climate series (village locations are anonymized), so climate effects cannot be separated from the seasonal cycle. The target is *recorded* incidence under passive case detection, which likely under-counts villages far from health facilities. See the paper's Limitations section for details.
