# Methodology

## Project Purpose

This project analyzes Alberta electricity demand, generation mix, high-demand surge periods, and hourly load forecasting performance using AESO-style electricity data.

The project has two connected parts:

1. **Tableau dashboard analysis**
   - Demand trend analysis
   - Regional load comparison
   - Load vs generation comparison
   - Fuel mix analysis
   - Surge-hour analysis
   - Model performance storytelling

2. **Python machine learning workflow**
   - Data preparation
   - Time-series feature engineering
   - Baseline model comparison
   - Statistical forecasting
   - Prophet forecasting
   - XGBoost forecasting
   - LSTM deep learning
   - Final model evaluation on a 2024 test set

The goal is to create an end-to-end analytics project that explains Alberta electricity behavior in a business-friendly dashboard while also testing forecasting models in a technical notebook.

---

## Data Preparation

The forecasting dataset is an hourly ML-ready file with the following key fields:

| Field | Description |
|---|---|
| `DT_MST` | Hourly timestamp in Mountain Standard Time |
| `TOTAL_LOAD` | Hourly electricity load in MW |
| `temperature` | Temperature variable used as an external forecasting feature |

The notebook first loads the dataset, converts `DT_MST` into a datetime field, sorts the data by timestamp, and sets the time index to hourly frequency.

Missing values in `TOTAL_LOAD` and `temperature` are handled using time-based interpolation followed by forward-fill and backward-fill. This keeps the series continuous and avoids gaps in the time-series models.

---

## Train/Test Split

The model evaluation uses a time-based split:

| Dataset | Date Range |
|---|---|
| Training Data | 2013-01-01 to 2023-12-31 |
| Test Data | 2024-01-01 to 2024-12-31 |

The test year is kept completely separate from training and validation so that the final model comparison reflects true out-of-sample forecasting performance.

The target variable is:

| Target | Description |
|---|---|
| `TOTAL_LOAD` | Hourly electricity load in MW |

---

## Stationarity Testing

Before building statistical time-series models, the notebook checks stationarity using:

- Augmented Dickey-Fuller test
- KPSS test

The raw load series shows conflicting stationarity signals, which is expected for electricity demand because the series contains strong seasonal and time-based patterns.

The notebook then checks differenced versions of the series:

- First difference
- 24-hour difference
- 168-hour difference

The results support the presence of strong daily and weekly seasonality. This helps justify the use of lag features and seasonal differencing in later models.

---

## Baseline Models

The project starts with simple baseline models to create a performance benchmark.

Baseline models include:

| Model | Purpose |
|---|---|
| SMA (168) | Uses a 168-hour moving average to capture weekly seasonality |
| CMA | Uses cumulative average as a simple long-term benchmark |
| EWMA (24) | Gives more weight to recent load observations |

These models are intentionally simple. They provide a baseline for evaluating whether more advanced models actually improve forecasting accuracy.

---

## Statistical Forecasting Models

The notebook includes several statistical time-series models:

| Model | Purpose |
|---|---|
| AR(168) | Uses weekly lag structure to model hourly load |
| ARIMA(2,1,2) | Applies differencing to handle non-stationarity |
| Manual SDIFF(24) + ARMA | Uses 24-hour seasonal differencing to capture daily seasonality |
| Manual SDIFF(24) + Temp + ARMA | Adds temperature as an external variable |

These models help compare traditional statistical forecasting approaches against machine learning and deep learning methods.

The statistical models are useful for understanding linear time-series behavior, but they are limited in capturing the nonlinear and complex patterns seen in electricity demand.

---

## Temperature and Load Relationship

The notebook analyzes the relationship between temperature and electricity load.

The relationship is not fully linear. Electricity demand tends to increase when temperatures are very low or very high, while demand is lower around moderate temperatures.

This supports the decision to include temperature-related features in forecasting models.

Temperature is used in:

- Prophet as an external regressor
- XGBoost through lagged and rolling temperature features
- LSTM through lagged and rolling temperature features

---

## Prophet Model

The Prophet model is trained with:

- Daily seasonality
- Weekly seasonality
- Yearly seasonality
- Temperature as an additional regressor

Prophet is useful because it captures broad trend and seasonal behavior in an interpretable way. However, it performs worse than the top machine learning models because it struggles more with sharp changes and high-variation periods in hourly electricity demand.

---

## XGBoost Feature Engineering

The XGBoost models use engineered features designed for hourly electricity load forecasting.

Feature groups include:

| Feature Group | Examples |
|---|---|
| Hourly seasonality | `hour_sin`, `hour_cos` |
| Weekly seasonality | `dow_sin`, `dow_cos` |
| Annual seasonality | `month_sin`, `month_cos`, `dayofyear` |
| Calendar effects | `is_weekend`, `is_holiday` |
| Load lags | `lag_2`, `lag_3`, `lag_24`, `lag_48`, `lag_72`, `lag_168`, `lag_336` |
| Rolling load features | `roll_mean_24`, `roll_mean_168`, `roll_mean_336`, `roll_mean_720` |
| Rolling variability | `roll_std_24`, `roll_std_168` |
| Temperature features | `temp_lag1`, `temp_lag24`, `temp_roll_mean_24`, `temp_roll_std_24` |

These features allow the model to learn recent demand behavior, daily patterns, weekly patterns, seasonal effects, holidays, and weather-related changes.

---

## XGBoost Direct Model

The XGBoost Direct model predicts hourly load using actual engineered features for the 2024 test period.

This model performs the best in the project because it captures nonlinear relationships between:

- recent load,
- time of day,
- day of week,
- seasonality,
- rolling demand behavior,
- temperature,
- holiday effects.

The direct approach also avoids recursive error accumulation.

---

## XGBoost Recursive Model

The recursive XGBoost model uses previous predictions as inputs for future predictions.

This approach is useful for simulating multi-step forecasting, but it performs worse than the direct model because errors accumulate over time. Once the model makes an error, that error can affect future lag and rolling features.

This explains why XGBoost Recursive has a higher MAE than XGBoost Direct.

---

## LSTM Model

The LSTM model is built using a 168-hour lookback window, which represents one full week of hourly history.

The LSTM uses:

- calendar features,
- lag features,
- rolling load statistics,
- temperature features,
- scaled input variables.

The model captures temporal patterns well and performs second-best overall. However, it tends to smooth peak demand periods and does not outperform XGBoost Direct.

---

## Evaluation Metrics

The models are evaluated using:

| Metric | Meaning |
|---|---|
| MAE | Mean Absolute Error in MW |
| RMSE | Root Mean Squared Error in MW |
| MAE % | MAE as a percentage of average 2024 load |
| RMSE % | RMSE as a percentage of average 2024 load |

MAE is used as the main ranking metric because it is easy to interpret in the same unit as the target variable: MW.

---

## Final Model Comparison

The final comparison is based on the 2024 test set.

| Rank | Model | MAE | MAE % | RMSE |
|---:|---|---:|---:|---:|
| 1 | XGBoost Direct | 70.16 MW | 1.02% | 91.79 MW |
| 2 | LSTM | 145.17 MW | 2.10% | 178.11 MW |
| 3 | XGBoost Recursive | 224.82 MW | 3.26% | 285.89 MW |
| 4 | Prophet + Temp | 289.58 MW | 4.20% | 380.97 MW |

XGBoost Direct is the best model because it achieves the lowest MAE and RMSE on the 2024 holdout test set.

---

## Tableau Dashboard Methodology

The Tableau dashboard uses cleaned and summarized output tables created from the Python audit process.

The dashboard is organized into four pages:

| Dashboard | Purpose |
|---|---|
| Executive Demand Overview | Shows annual, monthly, and hourly demand patterns |
| Regional Demand Analysis | Compares regional demand across Alberta |
| Supply Balance & Fuel Mix | Compares load/generation volume and analyzes fuel mix |
| Surge Events & Forecasting | Connects surge-hour behavior with forecasting results |

The dashboard is designed as a storytelling product rather than a collection of isolated charts.

---

## Surge Event Definition

Surge hours are defined as the top 5% of hourly load values within each year.

This year-specific method was used because the load distribution changes over time. A single global threshold across all years would overrepresent high-load years and underrepresent later years with lower load values.

Using a year-specific threshold makes the surge analysis more balanced across 2020–2024.

---

## Generation and Load Unit Handling

At the hourly level, load and generation are measured in MW.

When hourly MW values are summed across a quarter or year, the result is presented as GWh. This avoids misleading the viewer by treating summed hourly values as instantaneous MW.

In the dashboard:

| Metric Type | Unit |
|---|---|
| Hourly load | MW |
| Average load | MW |
| Hourly generation | MW |
| Average generation during surge | MW |
| Summed quarterly load | GWh |
| Summed quarterly generation | GWh |
| Fuel generation volume | GWh |

---

## Important Method Note

Load and generation are based on different AESO reporting datasets.

Load is based on hourly area-and-region data. Generation is based on CSD-reported generation.

Because these datasets do not use identical reporting definitions, the generation-load gap is presented as an analytical comparison rather than an official electricity surplus measure.

---

## Summary

This methodology combines business intelligence and machine learning into one project.

The Tableau dashboard explains demand and generation behavior in a visual format. The machine learning notebook tests forecasting models and identifies XGBoost Direct as the strongest model for 2024 hourly load prediction.

Together, the project demonstrates data preparation, dashboard design, feature engineering, time-series modeling, machine learning evaluation, and business communication.
