# Smart Mobility & Ride Analytics — README

## Overview
This dataset contains ride-hailing / smart-mobility trip records used to answer eight
business questions about pricing, ratings, and demand (correlation, hypothesis testing,
outlier detection, probability, and confidence-interval analysis). The accompanying notebook
is `Smart_Mobility___Ride_Analytics.ipynb`.

## Source
| | |
|---|---|
| File name | `Trip_Analysis.xlsx` |
| Format | Excel (`.xlsx`) |
| Loaded from | `https://raw.githubusercontent.com/karthi-95Entri/Smart-Mobility-Ride-Analytics/main/Trip_Analysis.xlsx` |
| Loaded with | `pandas.read_excel()` |

> The notebook does not run any explicit cleaning steps (no duplicate removal, missing-value
> handling, or range validation) — it reads the file and analyzes it as-is. It also references
> a `Trip_ID` column that it deliberately excludes from the numeric analysis, so the raw file
> likely has more columns than the ones described below.

## Columns referenced in the notebook

| Column | Type | Description | Notes |
|---|---|---|---|
| `Trip_ID` | identifier | Unique trip identifier | Present but excluded from correlation/covariance analysis |
| `Trip_Distance` | numeric | Distance traveled during the trip | Used against `Fare_Amount` for correlation/covariance |
| `Fare_Amount` | numeric | Total fare charged for the trip | Primary outcome variable across most questions |
| `Surge_Multiplier` | numeric | Surge-pricing multiplier applied to the base fare | Grouped to test relationship with ratings |
| `Customer_Rating` | numeric | Customer's rating of the ride | Compared against surge pricing |
| `Ride_Category` | categorical | Service tier | Observed values: `Economy`, `Premium`, `Shared` |
| `Ride_Time` | categorical | Time-of-day bucket | Observed values: `Peak`, `Non-Peak` |

A derived column, `Fare_ZScore` (plus boolean flags `IQR_Outlier` and `ZScore_Outlier`), is
created during the outlier-detection step.

## Analyses performed
1. **Distance vs. fare** — Pearson correlation, covariance, and significance (p-value) between
   `Trip_Distance` and `Fare_Amount`.
2. **Premium vs. economy pricing** — independent t-test (Premium vs Economy) and one-way ANOVA
   across `Economy` / `Premium` / `Shared` fares.
3. **Surge pricing vs. ratings** — Pearson correlation and one-way ANOVA of `Customer_Rating`
   grouped by `Surge_Multiplier`.
4. **Peak vs. non-peak revenue** — summary statistics, Welch's t-test, and a boxplot comparing
   `Fare_Amount` for `Peak` vs `Non-Peak` rides.
5. **Fare anomaly detection** — IQR/quartile method and z-score method (|z| > 3) on
   `Fare_Amount`, plus percentile summaries and a combined outlier flag.
6. **Ride-category demand probabilities** — category-level probabilities, a conditional
   probability table (fare given category), and its reverse (category given fare).
7. **Operational drivers of fare** — Pearson and Spearman correlation matrices, covariance
   matrix, and PCA-style eigenvalue/explained-variance analysis (via `StandardScaler` +
   `np.linalg.eig`) on the numeric columns excluding `Trip_ID`.
8. **Statistical support for pricing decisions** — 95% confidence interval for mean fare
   (analytic and bootstrap, 5,000 resamples), one-sample t-test against a reference fare of
   250, and a t-test comparing Economy vs Premium fares.

## Requirements
```
pandas
numpy
scipy
scikit-learn
matplotlib
seaborn
```

## How to reproduce
1. Install the requirements above (`pip install pandas numpy scipy scikit-learn matplotlib seaborn`).
2. Open `Smart_Mobility___Ride_Analytics.ipynb` in Jupyter.
3. Run all cells top to bottom — the notebook fetches `Trip_Analysis.xlsx` directly from the
   GitHub URL, so no local data file is needed.

## Known limitations
- No data-cleaning step is applied in this notebook, so duplicates, missing values, or
  out-of-range values (e.g., negative distances/fares) are not addressed before analysis —
  unlike some other versions of this dataset that do clean it first.
- Dataset provenance (collection method, time period, geography) is not documented in the
  notebook or source repository.
- No license is stated for the source file; confirm reuse permissions with the repository
  owner before using outside coursework.
- The `Ride_Category` values include a third tier, `Shared`, that isn't used consistently
  across every question (e.g., question 2's t-test only compares Premium vs Economy, while
  the ANOVA includes Shared).
