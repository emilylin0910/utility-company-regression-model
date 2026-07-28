# utility-company-regression-model
# H&S Revenue Forecasting — Seasonal Dummy & Interaction Models

OLS regression analysis comparing two revenue forecasting specifications for H&S, each using a seasonal dummy variable and an interaction term. Models are fit on 2011–2013 data and evaluated on a true 2014 hold-out to recommend the stronger specification for ongoing forecasting use.

## Objective

Build two OLS regression models — each with at least one seasonal dummy variable and one interaction term — and compare them on in-sample fit and out-of-sample forecast accuracy to recommend the stronger model for H&S's revenue forecasting process.

## Data

`AICPA_regressionAnalysisData.csv` — 48 months (Jan 2011–Dec 2014) with columns:

| Column | Description |
|---|---|
| `type` | `dt4training` (2011–2013, 36 months) or `dt4testing` (2014, 12 months, true hold-out) |
| `date` | Month-end date |
| `revenue` | Monthly revenue |
| `production` | Units produced |
| `coolDD` | Cooling degree days (hot-weather intensity) |
| `heatDD` | Heating degree days (cold-weather intensity) |

## Methodology

1. **Seasonal dummies.** `summer_DV` (Jun–Aug) and `fall_DV` (Sep–Nov) are built with `np.where`. Winter/Spring is left as the baseline to avoid the dummy-variable trap — all seasonal coefficients are read relative to Winter/Spring.
2. **Interaction terms.** `summer_interaction = production × summer_DV` and `fall_interaction = production × fall_DV`, letting the production-to-revenue slope shift by season rather than just the intercept.
3. **Train/test split.** 36 training months (2011–2013) fit the models; 12 hold-out months (2014) test forecast accuracy on data never seen during fitting.
4. **Evaluation.** In-sample R² / Adjusted R², plus out-of-sample RMSE and MAPE on the 2014 hold-out.

## Models

**Model 1 — Seasonal Production Interaction Model**

`revenue = b0 + b1(production) + b2(summer_DV) + b3(fall_DV) + b4(summer_interaction)`

Tests whether summer shifts both the average revenue level and the revenue earned per unit of production.

**Model 2 — Seasonal Weather Interaction Model**

`revenue = b0 + b1(production) + b2(summer_DV) + b3(fall_DV) + b4(fall_interaction) + b5(coolDD)`

Keeps both seasonal dummies, interacts production with fall instead of summer, and adds `coolDD` as a direct weather driver — testing whether the fall production slope differs and whether cooling demand explains revenue beyond the season label alone.

## Results

| Model | R² | Adj. R² | RMSE (holdout, $) | MAPE (holdout, %) |
|---|---|---|---|---|
| Model 1: Production × Summer | 0.82 | 0.80 | 3,365,952 | 20.63% |
| Model 2: Production × Fall + coolDD | 0.90 | 0.88 | 3,290,692 | 19.14% |

**Recommendation: Model 2.** It outperforms Model 1 on every metric — higher R² and Adjusted R² in-sample, and lower RMSE and MAPE on the 2014 out-of-sample hold-out. Adding `coolDD` explains roughly 7–8 additional points of R² beyond season alone.

## Requirements

```
numpy
pandas
matplotlib
statsmodels
```

## Usage

1. Place `AICPA_regressionAnalysisData.csv` in the working directory.
2. Run the notebook top to bottom in Jupyter or Google Colab.
3. The final cell renders the recommendation memo summarizing findings for H&S leadership.

## Repo contents

- `HS_Revenue_Forecasting.ipynb` — full analysis notebook (data load, feature engineering, both models, visualizations, forecast comparison, and memo)
- `AICPA_regressionAnalysisData.csv` — source data (not included here; supply your own copy)
