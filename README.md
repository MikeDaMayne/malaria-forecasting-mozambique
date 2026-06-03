# Malaria Incidence Prediction using Climate and Epidemiological Data
This project investigates how lagged climate and epidemiological variables influence malaria incidence using machine learning models, which were trained by epidemiological data on Mozambique.

## Problem Statement
Malaria remains a major public health challenge in many regions, particularly Africa.
Accurate prediction of malaria incidence can help for planning and resource allocation for intervention programs in burdened regions.

This project aims to model malaria incidence using climate data and epidemiological data, with a focus of assessing the importance of temporal (lagged) features and engineered features.

## Dataset
Two datasets were used:
- **Epidemiological dataset**: contains malaria incidence, testing rates, and distance from health facilities of some villages.
- **Climate data**: contians rainfall, temperature, humidity, and other climatic data related to malaria incidences and outbreaks. All the climate variables were also lagged.

Climate data was sourced from NASA POWER.

The final dataset fed to models was made by integrated the two datasets.

Important features include:
- spray_satus
- total_residents
- clinic_tests
- total_rdts
- avg_distance_to_health_facility_km
- positivity_rate
- prev_month_incidence_per_1000
- rainfall
- prev_month_rainfall
- mpi (Malaria Proneness Index, Engineered feature)

## Methodology

- Data was split 80/20 into training and testing tests chronologically for two reasons:
  - To preserve temporal structure
  - To avoid overfitting to a single village with generally high incidence.
- Feature engineering included:
  - Lagged variables (by up to 4 months) for all climate variables and incidence.
  - A unique malaria proneness index (MPI) assigned to each village based on its general vulnerablility to malaria, accounting for the high spatial variability of average incidences in the data. Calculated using distance from health facilities, spray_status, lagged incidence, and positivity rate.
- Models used:
  - Random Forest
  - Linear Regression (OLS)
  - Decision Trees
  - Regularized linear models (Lasso, Ridge, and ElasticNet)
  - kNN
- Two baseline models were used:
  - V1: model simply predicting the average of the village as incidence
  - V2: model predicting current incidence solely based on **lagged** incidence

## Key Insights

- Lagged incidence dominated predictive performance, accounting for the majority of predictive power (>60%)
- Regularlized linear models (particularly Lasso) produced the highest R^2 score, likely due to its feature removal, suggesting that there was considerable noise in the data, particularly the climate features.
- Random Forest was a very close second.

## How to Run

Simply run the notebooks in order of the numbering in their filename (1_, 2_, ...)

Ensure required Python libraries are installed.

## Limitations

The only limitation seen so far is the small size of the dataset the models have been trained on.

Having a larget dataset size would positively affect the reliability of the models.

## Future Work

- Explore time-series models (e.g., LSTM)
- Improve interpretability of linear models
- Investigate for any possible data leakage.
