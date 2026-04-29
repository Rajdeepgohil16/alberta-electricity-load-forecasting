# Limitations

This document outlines the main limitations of the project. These limitations are important because they explain how the results should be interpreted and where future improvements could strengthen the analysis.

---

## 1. Raw Data Not Included

The full raw AESO datasets are not included in this repository because of file size and source-data management.

The repository includes the dashboard screenshots, final notebook, documentation, and final model comparison output. To fully reproduce the workflow, the user must download or prepare the required AESO-style datasets and update the notebook data path.

---

## 2. Local Data Path Must Be Updated

The notebook was developed using a local prepared ML-ready dataset. Anyone running the notebook on another machine must update the data path to point to their local copy of the prepared dataset.

The expected file should include at least:

| Required Column | Purpose |
|---|---|
| `DT_MST` | Hourly timestamp |
| `TOTAL_LOAD` | Target variable for forecasting |
| `temperature` | External forecasting feature |

---

## 3. Load and Generation Definitions Differ

Load and generation are based on different AESO reporting datasets.

Load is based on hourly area-and-region data, while generation is based on CSD-reported generation. These datasets do not use identical definitions.

Because of this, the generation-load gap shown in the dashboard should be interpreted as an analytical comparison, not as an official electricity surplus measure.

This is one of the most important limitations in the dashboard interpretation.

---

## 4. Structural Shift in Load Data

The annual and regional load trends show a visible post-2019 shift.

This shift may reflect source changes, coverage changes, reporting-structure differences, or real demand behavior. The dashboard does not claim a single confirmed cause.

For this reason, the long-term trend is used for context, while the main supply, surge, and forecasting analysis focuses more heavily on the 2020–2024 period.

---

## 5. Single Holdout Test Year

The final model comparison uses 2024 as the holdout test year.

This is useful because it provides a clean out-of-sample evaluation, but it does not fully test model stability across multiple years.

A stronger evaluation would use rolling cross-validation or multiple backtesting windows.

---

## 6. Limited External Variables

Temperature is the main external variable used in the forecasting workflow.

Other potentially important demand drivers were not included, such as:

- electricity market price,
- population growth,
- economic activity,
- industrial operations,
- major outages,
- demand response events,
- policy changes,
- large customer behavior,
- unexpected extreme events.

Including more external variables could improve forecasting accuracy and explainability.

---

## 7. Limited Hyperparameter Tuning

The project includes several forecasting models, but hyperparameter tuning was limited.

XGBoost and LSTM could potentially be improved using more systematic tuning methods such as:

- grid search,
- random search,
- Bayesian optimization,
- time-series cross-validation,
- model-specific tuning pipelines.

The current model results are strong, but further tuning could improve performance.

---

## 8. Recursive Forecasting Error Accumulation

The XGBoost Recursive model performs worse than XGBoost Direct because recursive predictions depend on previous predicted values.

When one prediction is slightly wrong, that error can affect future lag and rolling features. Over time, these errors accumulate and reduce forecast accuracy.

This is a known limitation of recursive multi-step forecasting.

---

## 9. LSTM Peak Smoothing

The LSTM model performs well overall, but it tends to smooth peak demand periods.

This means it can follow the general pattern of electricity load but may underpredict sharp spikes or extreme demand changes.

This is important for electricity forecasting because peak demand periods are operationally important.

---

## 10. Model Interpretability

Machine learning and deep learning models can provide strong predictive accuracy, but they are less interpretable than simpler statistical models.

XGBoost is more interpretable than LSTM, but additional explainability methods such as SHAP analysis would make the model easier to explain to business or energy stakeholders.

---

## 11. Dashboard Uses Summary Tables

The Tableau dashboard uses cleaned and summarized output tables rather than connecting directly to every raw file.

This was intentional because the dashboard is designed as a portfolio-ready storytelling product. However, it means the dashboard is not a fully automated production dashboard.

A future version could connect Tableau to a refreshed data pipeline or database.

---

## 12. Surge Definition Is Project-Specific

Surge hours are defined as the top 5% of hourly load values within each year.

This is a project-defined analytical threshold, not an official AESO threshold.

The definition is useful for identifying high-demand periods, but it should not be interpreted as an official market or reliability threshold.

---

## 13. Tableau Screenshots Are Static

The repository includes exported Tableau dashboard screenshots.

Static screenshots make the project easy to review on GitHub, but they do not provide the full interactivity of Tableau. A Tableau Public link could improve accessibility if the workbook is published online.

---

## 14. Forecasting Scope

The project focuses on hourly load forecasting using historical load, calendar features, temperature, and engineered time-series features.

It does not forecast generation mix, market price, emissions, or grid reliability outcomes.

---

## 15. Future Improvements

Future improvements could include:

- rolling cross-validation across multiple years,
- more external variables,
- SHAP explainability for XGBoost,
- probabilistic forecasting or prediction intervals,
- improved LSTM architecture,
- automated data pipeline,
- Tableau Public deployment,
- model deployment through a simple web app,
- comparison with additional models such as LightGBM, CatBoost, or Temporal Fusion Transformer.

---

## Summary

The project provides a strong end-to-end analytics workflow, but the results should be interpreted with the limitations above.

The strongest result is that XGBoost Direct performed best on the 2024 test set, while the Tableau dashboard provides a clear business-facing explanation of demand patterns, regional differences, fuel mix changes, surge behavior, and forecasting performance.
