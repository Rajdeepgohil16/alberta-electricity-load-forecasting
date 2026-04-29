# Source Links

This document lists the main public sources used or referenced for the project.

The full raw datasets are not stored in this repository because of file size and source-data management. To reproduce the full project, download the relevant AESO datasets and prepare the ML-ready hourly load file used by the notebook.

---

## Main Data Source: AESO

AESO stands for Alberta Electric System Operator. AESO provides public market and system reporting resources related to Alberta electricity load, generation, supply, demand, and market statistics.

Official AESO website:

https://www.aeso.ca/

---

## Hourly Load by Area and Region

This source provides hourly load data by Alberta area and region.

Used for:

- annual load trend,
- monthly demand pattern,
- average daily load profile,
- regional demand comparison,
- regional ranking.

Official AESO page:

https://www.aeso.ca/market/market-and-system-reporting/data-requests/hourly-load-by-area-and-region/

Project note:

The dashboard uses this source for load and regional demand analysis. The long-term trend is interpreted carefully because the data shows a visible post-2019 structural shift.

---

## AESO Market and System Reporting

This section contains AESO market and system reporting resources, including current and historical market reports, supply and demand information, and other electricity market data.

Used for:

- project context,
- understanding AESO reporting categories,
- supporting discussion of load and generation definitions.

Official AESO page:

https://www.aeso.ca/market/market-and-system-reporting/

---

## Annual Market Statistics Reports

AESO Annual Market Statistics reports summarize key market and system metrics, including Alberta Internal Load and System Load.

Used for:

- checking high-level load context,
- understanding the difference between load reporting definitions,
- supporting the caution that project load/generation comparisons should not be treated as official electricity surplus.

Official AESO page:

https://www.aeso.ca/market/market-and-system-reporting/annual-market-statistic-reports/

---

## Historical Generation / Supply and Demand Context

AESO provides market and system reporting resources related to current and historical supply and demand.

Used for:

- generation mix analysis,
- fuel type analysis,
- comparison of CSD-reported generation and load measures.

Official AESO market and system reporting page:

https://www.aeso.ca/market/market-and-system-reporting/

---

## Alberta Market Surveillance Administrator

The Alberta Market Surveillance Administrator provides electricity market monitoring and reporting. This can be useful for additional context on Alberta electricity market conditions, system load, net demand, supply, and market behavior.

Used for:

- supporting market context,
- understanding that system load and Alberta Internal Load can differ,
- supporting careful interpretation of load and generation comparisons.

MSA website:

https://www.albertamsa.ca/

Example report used for context:

https://www.albertamsa.ca/assets/Documents/Quarterly-Report-for-Q4-2024.pdf

---

## Project Data Preparation Note

The notebook expects a prepared ML-ready hourly dataset with at least the following fields:

| Field | Description |
|---|---|
| `DT_MST` | Hourly timestamp |
| `TOTAL_LOAD` | Hourly electricity load in MW |
| `temperature` | Temperature variable used for forecasting |

The final ML-ready file was prepared before modeling and is not included in this repository.

---

## Important Reporting Note

Load and generation are not treated as perfectly identical reporting measures in this project.

Load is based on hourly area-and-region data. Generation is based on CSD-reported generation or generation-style source data. Because these sources use different reporting definitions, the generation-load comparison is treated as an analytical comparison rather than an official electricity surplus calculation.

---

