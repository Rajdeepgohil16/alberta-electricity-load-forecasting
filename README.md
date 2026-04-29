# Forecasting Alberta Electricity Load and Generation Mix

## Project Overview

This project analyzes Alberta electricity demand, generation mix, high-demand surge periods, and hourly load forecasting performance using AESO-style electricity data.

The project combines a Tableau dashboard with a Python-based machine learning workflow. The dashboard explains demand trends, regional load patterns, fuel mix changes, surge-hour behavior, and forecasting results. The machine learning notebook compares multiple forecasting models to identify the best approach for predicting hourly electricity load.

This project was built as a portfolio case study for data analytics, business intelligence, and applied machine learning roles.

---

## Project Objectives

The project focuses on four main questions:

1. How does Alberta electricity demand change over time?
2. How does electricity demand differ across Alberta regions?
3. How does Alberta's generation mix change by fuel type?
4. Which forecasting model performs best for hourly electricity load prediction?

---

## Dashboard Preview

### 1. Executive Demand Overview

![Executive Demand Overview](dashboard/01_executive_demand_overview.png)

This dashboard summarizes Alberta electricity demand patterns from 2011 to 2024. It highlights the annual load trend, monthly seasonality, and the average daily load profile.

Main insight: Alberta electricity demand shows clear seasonal and daily patterns. Winter months carry higher average load, and the average daily load profile peaks around the late afternoon.

---

### 2. Regional Demand Analysis

![Regional Demand Analysis](dashboard/02_regional_demand_analysis.png)

This dashboard compares electricity demand across Alberta regions and shows how regional load patterns change over time.

Main insight: Regional load differs across Alberta, with Edmonton ranking highest in 2024 average regional demand. The regional trend also shows a visible post-2019 shift, which is interpreted cautiously as a possible source, coverage, or reporting-structure change.

---

### 3. Supply Balance & Fuel Mix

![Supply Balance & Fuel Mix](dashboard/03_supply_balance_fuel_mix.png)

This dashboard compares load and CSD-reported generation volume, then analyzes Alberta's generation mix by fuel type.

Main insight: Gas dominates Alberta's generation mix, coal declines sharply over time, and wind and solar increase their share. The generation-load gap is treated as an analytical comparison, not an official market surplus, because load and generation come from different AESO reporting datasets.

---

### 4. Surge Events & Forecasting

![Surge Events & Forecasting](dashboard/04_surge_events_forecasting.png)

This dashboard connects high-demand surge behavior with forecasting performance.

Main insight: Surge hours are defined as the top 5% of hourly load values within each year. This avoids one high-load year dominating the threshold. During high-demand hours, gas provides the largest average generation contribution. XGBoost Direct achieved the best forecasting performance on the 2024 test set.

---

## Dashboard Story

The Tableau dashboard is designed to tell a clear analytical story:

Demand trend → Regional demand → Supply and fuel mix → Surge behavior → Forecasting performance

This structure helps the viewer understand the electricity system from a business and operational perspective before reviewing the machine learning results.

---

## Data Sources

The project uses AESO-style electricity datasets prepared for dashboarding and forecasting.

| Data Category | Description |
|---|---|
| Hourly Load Data | Alberta hourly electricity load by area and region |
| Generation Data | Hourly generation by fuel type |
| Temperature Data | External weather variable used for forecasting |
| Dashboard Summary Tables | Aggregated tables created from the cleaned data for Tableau |

The full raw datasets are not included in this repository because of file size and source-data management. The notebook expects a prepared ML-ready hourly dataset with the following key fields:

| Field | Description |
|---|---|
| DT_MST | Hourly timestamp in Mountain Standard Time |
| TOTAL_LOAD | Hourly electricity load in MW |
| temperature | Temperature variable used as an external forecasting feature |

---

## Important Method Note

Load and generation are based on different AESO reporting datasets.

Load is based on hourly area-and-region data. Generation is based on CSD-reported generation.

Because these datasets do not use identical reporting definitions, the generation-load gap shown in this project should be interpreted as an analytical comparison, not as an official electricity surplus measure.

This distinction is important because the dashboard compares two different reporting definitions.

---

## Tableau Dashboard Design

The Tableau dashboard is organized into four pages:

| Dashboard Page | Purpose |
|---|---|
| Executive Demand Overview | Shows long-term, monthly, and hourly electricity demand patterns |
| Regional Demand Analysis | Compares electricity demand across Alberta regions |
| Supply Balance & Fuel Mix | Compares load and generation volume and analyzes fuel mix |
| Surge Events & Forecasting | Explains high-demand periods and model performance |

---

## Key Dashboard Insights

### Demand Patterns

Alberta electricity load has strong seasonal and daily patterns. Winter months show the highest average load, with January having the highest average monthly load. May has the lowest average monthly load. The average daily load profile reaches its highest point around 17:00.

### Regional Demand

Regional demand differs across Calgary, Central, Edmonton, Northeast, Northwest, and South. Edmonton ranked highest in 2024 average regional load. A visible post-2019 shift appears in the regional data, so the trend is interpreted cautiously rather than treated as a confirmed demand event.

### Supply and Fuel Mix

Gas is the dominant generation source. Coal declines sharply over time, while wind and solar increase their share. When hourly MW values are summed over quarters or years, they are presented as GWh to avoid misleading unit interpretation.

### Surge Events

Surge hours are defined as the top 5% of hourly load values within each year. This year-specific method avoids one high-load year dominating the threshold. The final surge definition produced 2,194 high-demand hours across 2020–2024. The yearly surge threshold range was approximately 7,568 MW to 10,468 MW. Gas provides the largest average generation contribution during surge hours.

### Forecasting

XGBoost Direct achieved the best forecasting performance on the 2024 test set. LSTM ranked second and captured temporal patterns reasonably well. XGBoost Recursive performed worse than XGBoost Direct because recursive predictions accumulate error over time. Prophet captured general seasonality but had higher error during more variable periods.

---

## Machine Learning Workflow

The machine learning notebook follows an end-to-end forecasting process:

1. Load and clean hourly electricity load data
2. Interpolate missing load and temperature values
3. Split the dataset into training and testing periods
4. Run stationarity checks using ADF and KPSS tests
5. Build baseline forecasting models
6. Build statistical time-series models
7. Analyze the relationship between temperature and load
8. Train Prophet with temperature as an external regressor
9. Engineer time-series features for XGBoost
10. Train XGBoost Direct and XGBoost Recursive models
11. Train an LSTM deep learning model
12. Compare all models using 2024 test-set performance
13. Save the final model comparison output

---

## Train/Test Split

| Period | Date Range |
|---|---|
| Training Data | 2013-01-01 to 2023-12-31 |
| Test Data | 2024-01-01 to 2024-12-31 |

The target variable is TOTAL_LOAD.

The forecasting frequency is hourly.

---

## Models Tested

The notebook compares several model families:

| Category | Models |
|---|---|
| Baseline Models | Simple Moving Average, Cumulative Moving Average, EWMA |
| Statistical Models | AR(168), ARIMA(2,1,2), seasonal differenced SARIMA/SARIMAX |
| Additive Model | Prophet with temperature |
| Machine Learning | XGBoost Direct, XGBoost Recursive |
| Deep Learning | LSTM |

---

## Feature Engineering

The XGBoost and LSTM models use time-series features designed to capture daily, weekly, seasonal, and weather-related demand patterns.

Feature groups include:

- Hour-of-day cyclical features
- Day-of-week cyclical features
- Month cyclical features
- Weekend indicator
- Alberta holiday indicator
- Load lag features
- Rolling load means
- Rolling load standard deviations
- Lagged temperature features
- Rolling temperature statistics

Example features include hour_sin, hour_cos, dow_sin, dow_cos, month_sin, month_cos, lag_24, lag_168, roll_mean_24, roll_mean_168, roll_std_24, temp_lag1, temp_lag24, and temp_roll_mean_24.

These features help the models learn daily demand cycles, weekly seasonality, recent load behavior, temperature effects, and holiday/weekend patterns.

---

## Final Model Results

The final model comparison was evaluated on the 2024 test set.

| Rank | Model | MAE | MAE % | RMSE |
|---:|---|---:|---:|---:|
| 1 | XGBoost Direct | 70.16 MW | 1.02% | 91.79 MW |
| 2 | LSTM | 145.17 MW | 2.10% | 178.11 MW |
| 3 | XGBoost Recursive | 224.82 MW | 3.26% | 285.89 MW |
| 4 | Prophet + Temp | 289.58 MW | 4.20% | 380.97 MW |

The full ranked model comparison is available in outputs/final_model_comparison_2024.csv.

---

## Best Model

The best-performing model was XGBoost Direct.

Performance on the 2024 test set:

| Metric | Value |
|---|---:|
| MAE | 70.16 MW |
| RMSE | 91.79 MW |
| MAE % | 1.02% |

XGBoost Direct performed best because it captured nonlinear relationships between electricity load, time-based patterns, lagged demand, rolling statistics, holidays, and temperature. It also avoided the error accumulation issue seen in recursive forecasting.

---

## Model Interpretation

### XGBoost Direct

XGBoost Direct achieved the lowest forecasting error. It was able to capture nonlinear load behavior and complex interactions between time, lag, rolling, and temperature features.

### LSTM

The LSTM model performed second-best. It captured temporal patterns using a 168-hour lookback window, representing one full week of hourly history. However, it slightly smoothed peak load periods and did not outperform XGBoost.

### XGBoost Recursive

The recursive XGBoost model performed worse than the direct version because each prediction depended on previous predicted values. This caused errors to accumulate over time.

### Prophet + Temperature

Prophet captured broad trend and seasonal behavior, and temperature helped explain part of the variation. However, Prophet had higher errors during peak or high-variation periods.

### Statistical Models

AR, ARIMA, SARIMA, and SARIMAX helped establish a statistical baseline, but they were limited by their linear structure and did not capture the full nonlinear behavior of electricity demand.

---

## Project Outputs

| Output | Location |
|---|---|
| Tableau Dashboard Screenshots | dashboard/ |
| Machine Learning Notebook | notebooks/alberta_load_forecasting.ipynb |
| Notebook HTML Export | notebooks/alberta_load_forecasting.html |
| Final Model Comparison | outputs/final_model_comparison_2024.csv |
| Methodology Notes | docs/methodology.md |
| Data Dictionary | docs/data_dictionary.md |
| Limitations | docs/limitations.md |
| Source Notes | docs/source_links.md |

---

## Repository Structure

alberta-electricity-load-forecasting/
├── README.md
├── requirements.txt
├── .gitignore
├── dashboard/
│   ├── 01_executive_demand_overview.png
│   ├── 02_regional_demand_analysis.png
│   ├── 03_supply_balance_fuel_mix.png
│   └── 04_surge_events_forecasting.png
├── notebooks/
│   ├── alberta_load_forecasting.ipynb
│   └── alberta_load_forecasting.html
├── outputs/
│   └── final_model_comparison_2024.csv
└── docs/
    ├── methodology.md
    ├── data_dictionary.md
    ├── limitations.md
    └── source_links.md

---

## How to Run the Notebook

1. Clone the repository.

git clone https://github.com/Rajdeepgohil16/alberta-electricity-load-forecasting.git

2. Install the required Python packages.

pip install -r requirements.txt

3. Open the notebook.

notebooks/alberta_load_forecasting.ipynb

4. Update the data path in the notebook to point to your local prepared AESO ML-ready dataset.

The notebook expects a dataset with the following columns:

- DT_MST
- TOTAL_LOAD
- temperature

---

## Data Access Note

The full raw AESO datasets are not included in this repository because of file size and source-data management.

To reproduce the full analysis:

1. Download the required AESO-style load and generation datasets.
2. Prepare the ML-ready hourly dataset.
3. Update the notebook data path.
4. Run the notebook from top to bottom.

The dashboard screenshots and final model comparison are included so that the project can still be reviewed without downloading the full raw dataset.

---

## Tools and Technologies

| Category | Tools |
|---|---|
| Programming | Python |
| Data Processing | pandas, NumPy |
| Visualization | Tableau, Matplotlib |
| Machine Learning | scikit-learn, XGBoost |
| Statistical Modeling | statsmodels |
| Forecasting | Prophet |
| Deep Learning | TensorFlow/Keras |
| Time-Series Features | Lag features, rolling statistics, cyclical encoding |
| Evaluation | MAE, RMSE, MAE %, RMSE % |

---

## Skills Demonstrated

This project demonstrates:

- Data cleaning and preparation
- Time-series analysis
- Feature engineering
- Forecasting model development
- Machine learning model comparison
- Deep learning using LSTM
- Forecast evaluation using MAE and RMSE
- Tableau dashboard design
- Energy analytics storytelling
- Business-facing communication
- GitHub project documentation

---

## Limitations

This project has several limitations:

- The full raw AESO dataset is not included in the repository.
- The model evaluation uses a single 2024 holdout test set rather than full rolling cross-validation.
- Temperature is the main external variable; other demand drivers such as economic activity, population, market price, industrial operations, and unexpected events were not included.
- Load and generation comparisons should be interpreted carefully because they are based on different AESO reporting datasets.
- Machine learning and deep learning models can be less interpretable than simpler statistical models.
- Hyperparameter tuning was limited and could be improved in future iterations.

---

## Future Improvements

Future improvements could include:

- Add rolling cross-validation across multiple test years
- Include additional external variables such as electricity price, population, economic activity, and industrial demand
- Add probabilistic forecasting or prediction intervals
- Tune XGBoost and LSTM hyperparameters more systematically
- Add SHAP analysis for model explainability
- Deploy the best model through a simple web application
- Automate the data pipeline from raw AESO files to Tableau-ready and ML-ready outputs

---

## Final Conclusion

This project shows how electricity demand and generation data can be transformed into a business-facing analytics product and a machine learning forecasting workflow.

The Tableau dashboard explains demand trends, regional patterns, fuel mix changes, and high-demand surge behavior. The Python notebook evaluates multiple forecasting approaches and identifies XGBoost Direct as the strongest model for 2024 hourly load forecasting.

Overall, the project demonstrates an end-to-end analytics workflow across data preparation, visualization, time-series modeling, machine learning, deep learning, evaluation, and communication.
