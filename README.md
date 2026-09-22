# Household Power Consumption — Analysis & Forecasting

Analysis of the UCI *Individual Household Electric Power Consumption* dataset: minute-level electrical measurements from a single household in Sceaux, near Paris, France (Dec 2006 – Nov 2010). Covers data cleaning, seasonal consumption patterns, sub-metering circuit breakdown, and time-series forecasting with both classical statistical methods and machine learning.

> This report was generated using AI under general human direction. At the time of writing, the contents have not been comprehensively reviewed by a human analyst.

## Contents

- `household_power_analysis.qmd` — Quarto source (R) for the full analysis
- `images/` — key forecast comparison plots
- `data/` — dataset not included in this repo (see below); place the raw file here to reproduce

## Data

Dataset: [UCI Individual Household Electric Power Consumption](https://archive.ics.uci.edu/dataset/235/individual+household+electric+power+consumption)

The raw file (`household_power_consumption.txt`, ~127 MB) is not committed to this repository — it exceeds GitHub's file size limits. Download it from the UCI link above and place it in a `data/` folder (or the repo root, matching the path used in the `.qmd`) before rendering.

## Method summary

- **Cleaning**: all missing values fall on fully-missing rows (no partial-row gaps). Gaps ≤ 1 day were linearly interpolated (`zoo::na.approx`); multi-day gaps were dropped rather than interpolated, to avoid fabricating flat trends across real daily cycles.
- **Seasonality**: consumption follows a strong annual cycle — winter median ~1.3–1.5 kW vs. summer ~0.6–0.7 kW, consistent with heating-driven demand.
- **Sub-metering**: three metered circuits (kitchen, laundry, water heater/AC) plus a derived "unmetered" residual, which turns out to be the single largest component of consumption.
- **Forecasting**:
  - Daily resolution: ARIMA outperformed STL+ETS and ARIMA with day-of-week/month regressors (RMSE 0.248 kW on a 30-day holdout).
  - Hourly resolution: a random forest with lag/rolling-window features roughly halved the error of an ARIMA + Fourier-terms model (RMSE 0.36 kW vs. 0.69 kW on a 48-hour holdout). Adding outdoor temperature (via the Open-Meteo archive API) did not improve the ML model — lag features already capture the temperature-driven seasonal signal.

## Forecast comparisons

**ARIMA + Fourier — 48-hour forecast vs. actuals**

![ARIMA + Fourier forecast](images/hourly_forecast.png)

**Random Forest vs. ARIMA + Fourier — 48-hour forecast comparison**

![Random Forest vs ARIMA forecast comparison](images/rf_forecast.png)

## Reproducing

Requires [Quarto](https://quarto.org) and R with the following packages: `data.table`, `ggplot2`, `lubridate`, `zoo`, `forecast`, `ranger`, `jsonlite`.

```bash
quarto render household_power_analysis.qmd
```

## Author

Uthman B. Adedeji
