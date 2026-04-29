# Data Dictionary

This data dictionary explains the main fields used in the Tableau dashboard and machine learning notebook.

The project uses prepared hourly electricity load data, generation data, dashboard summary tables, and model output tables.

---

## Core Time Fields

| Field | Description | Example |
|---|---|---|
| `DT_MST` | Hourly timestamp in Mountain Standard Time | 2024-01-01 00:00:00 |
| `Year` | Calendar year extracted from timestamp | 2024 |
| `Month` | Month number from 1 to 12 | 1 |
| `Month_Name` | Month name | January |
| `Hour` | Hour of day from 0 to 23 | 17 |
| `Day_Of_Week` | Day name | Monday |
| `Quarter` | Calendar quarter | 2024Q1 |
| `Date` | Calendar date without time | 2024-01-01 |

---

## Load Fields

| Field | Description | Unit |
|---|---|---|
| `TOTAL_LOAD` | Hourly electricity load | MW |
| `avg_load_mw` | Average hourly load for a group such as year, month, hour, or region | MW |
| `median_load_mw` | Median hourly load for a group | MW |
| `min_load_mw` | Minimum hourly load for a group | MW |
| `max_load_mw` | Maximum hourly load for a group | MW |
| `p95_load_mw` | 95th percentile hourly load for a group | MW |
| `yoy_avg_load_change_pct` | Year-over-year percentage change in average load | Percent |
| `load_gwh` | Summed hourly load volume converted to GWh | GWh |

---

## Regional Load Fields

| Field | Description | Unit |
|---|---|---|
| `Region` | Alberta region name | Text |
| `rank_2024` | Ranking of regions by 2024 average load | Number |
| `avg_load_mw` | Average regional load | MW |
| `median_load_mw` | Median regional load | MW |
| `p95_load_mw` | 95th percentile regional load | MW |
| `max_load_mw` | Maximum regional load | MW |
| `hours` | Number of hourly records used in the summary | Count |

Regions included in the dashboard:

| Region |
|---|
| Calgary |
| Central |
| Edmonton |
| Northeast |
| Northwest |
| South |

---

## Generation Fields

| Field | Description | Unit |
|---|---|---|
| `Fuel_Type` | Generation source category | Text |
| `Volume` | Hourly generation value by fuel type | MW |
| `Total_Generation_MW` | Total hourly generation across fuel types | MW |
| `avg_generation_mw` | Average hourly generation by fuel type or period | MW |
| `median_generation_mw` | Median hourly generation by fuel type or period | MW |
| `p95_generation_mw` | 95th percentile generation by fuel type | MW |
| `max_generation_mw` | Maximum generation by fuel type | MW |
| `generation_gwh` | Summed generation volume converted to GWh | GWh |
| `total_generation_gwh` | Total generation volume by fuel type | GWh |

---

## Fuel Mix Fields

| Field | Description | Unit |
|---|---|---|
| `Fuel_Type` | Fuel or generation category | Text |
| `generation_gwh` | Annual generation volume for a fuel type | GWh |
| `year_total_generation_gwh` | Total generation volume across all fuel types in the year | GWh |
| `fuel_share_pct` | Fuel type share of annual generation | Percent |

Fuel types used in the generation mix analysis:

| Fuel Type | Description |
|---|---|
| Gas | Natural gas generation |
| Coal | Coal generation |
| Wind | Wind generation |
| Solar | Solar generation |
| Hydro | Hydroelectric generation |
| Dual Fuel | Dual-fuel generation category |
| Other | Other generation sources |
| Energy Storage | Storage-related generation or charging/discharging behavior |

---

## Load vs Generation Fields

| Field | Description | Unit |
|---|---|---|
| `Quarter` | Calendar quarter | Text |
| `load_gwh` | Summed load volume in the quarter | GWh |
| `generation_gwh` | Summed generation volume in the quarter | GWh |
| `avg_load_mw` | Average hourly load in the quarter | MW |
| `avg_generation_mw` | Average hourly generation in the quarter | MW |
| `generation_minus_load_gwh` | Difference between generation volume and load volume | GWh |
| `generation_to_load_ratio` | Ratio of generation volume to load volume | Ratio |
| `hours` | Number of hourly records in the quarter | Count |

Important note: the generation-load gap is an analytical comparison, not an official electricity surplus measure, because load and generation come from different AESO reporting datasets.

---

## Surge Fields

The final surge analysis uses a year-specific threshold.

Surge hours are defined as the top 5% of hourly load values within each year.

| Field | Description | Unit |
|---|---|---|
| `YEARLY_SURGE_THRESHOLD_MW` | 95th percentile load threshold calculated separately for each year | MW |
| `YEARLY_SURGE_FLAG` | Indicator for whether an hour is a surge hour | 0 or 1 |
| `YEARLY_SURGE_LABEL` | Text label identifying surge vs normal hours | Text |
| `surge_hours` | Number of high-demand surge hours | Count |
| `surge_share_pct` | Share of total hours classified as surge hours | Percent |
| `avg_load_during_surge_mw` | Average load during surge hours | MW |
| `min_yearly_threshold_mw` | Lowest yearly surge threshold across years | MW |
| `max_yearly_threshold_mw` | Highest yearly surge threshold across years | MW |

---

## Surge Generation Fields

| Field | Description | Unit |
|---|---|---|
| `Fuel_Type` | Generation source category | Text |
| `avg_generation_during_surge_mw` | Average generation during surge hours by fuel type | MW |
| `share_of_avg_surge_generation_pct` | Fuel type share of average surge-hour generation | Percent |

This table helps explain which generation sources contribute most during high-demand periods.

---

## Temperature Fields

| Field | Description | Unit |
|---|---|---|
| `temperature` | Hourly temperature variable used as an external forecasting feature | Degrees Celsius |
| `temp_lag1` | Temperature lagged by 1 hour | Degrees Celsius |
| `temp_lag24` | Temperature lagged by 24 hours | Degrees Celsius |
| `temp_roll_mean_24` | 24-hour rolling average temperature | Degrees Celsius |
| `temp_roll_std_24` | 24-hour rolling temperature variability | Degrees Celsius |

Temperature is used because electricity load is affected by heating and cooling demand.

---

## Machine Learning Feature Fields

| Field | Description |
|---|---|
| `hour_sin` | Cyclical encoding of hour of day |
| `hour_cos` | Cyclical encoding of hour of day |
| `dow_sin` | Cyclical encoding of day of week |
| `dow_cos` | Cyclical encoding of day of week |
| `month_sin` | Cyclical encoding of month |
| `month_cos` | Cyclical encoding of month |
| `dayofyear` | Day number within the year |
| `is_weekend` | Weekend indicator |
| `is_holiday` | Alberta holiday indicator |
| `lag_2` | Load lagged by 2 hours |
| `lag_3` | Load lagged by 3 hours |
| `lag_24` | Load lagged by 24 hours |
| `lag_48` | Load lagged by 48 hours |
| `lag_72` | Load lagged by 72 hours |
| `lag_168` | Load lagged by 168 hours |
| `lag_336` | Load lagged by 336 hours |
| `roll_mean_24` | 24-hour rolling average load |
| `roll_mean_168` | 168-hour rolling average load |
| `roll_mean_336` | 336-hour rolling average load |
| `roll_mean_720` | 720-hour rolling average load |
| `roll_std_24` | 24-hour rolling load variability |
| `roll_std_168` | 168-hour rolling load variability |

---

## Model Evaluation Fields

| Field | Description | Unit |
|---|---|---|
| `Rank` | Model ranking based on MAE | Number |
| `Model` | Forecasting model name | Text |
| `MAE` | Mean Absolute Error | MW |
| `MAE_%` | MAE as a percentage of average test load | Percent |
| `RMSE` | Root Mean Squared Error | MW |
| `RMSE_%` | RMSE as a percentage of average test load | Percent |

MAE is the primary ranking metric because it is easy to interpret in MW.

---

## Unit Guide

| Metric Type | Correct Unit |
|---|---|
| Hourly load | MW |
| Average load | MW |
| Hourly generation | MW |
| Average generation | MW |
| Summed load over quarter/year | GWh |
| Summed generation over quarter/year | GWh |
| Fuel share | Percent |
| Forecasting error | MW |

---

## Notes on Interpretation

The dashboard uses MW for hourly and average values. It uses GWh when hourly MW values are summed over longer time periods.

Load and generation comparisons should be interpreted carefully because they are based on different reporting datasets. The generation-load gap is included for analytical comparison, not as an official market surplus calculation.
