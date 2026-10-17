# Examples: Original Business ML Models by Danny Barren

This folder is **separate from the main course**. The notebooks here are **original models created by Danny Barren** for practical, real-world business use cases. They're here as **inspiration**: worked examples of how classical machine learning (logistic regression, ridge regression, random-forest-style tree ensembles, gradient boosting, and k-means clustering) can turn everyday business data into decisions that make or save money.

- **Original work:** every notebook, use case, and line of code was written by Danny Barren. None of it is taken from other people's work, tutorials, or competition notebooks.
- **Inspiration, not lessons:** the [course lessons](../lessons/) and [course notebooks](../notebooks/) teach the techniques step by step. These examples show the same techniques applied end to end on focused business problems.
- **Synthetic sample data:** when no file is provided, each notebook **generates its own synthetic demo dataset** (about 600–800 rows) with a realistic business story built in. The data is invented for demonstration only. It does not describe any real company, customer, or person.

## The examples

| # | Notebook | Business question | Approach |
|---|----------|-------------------|----------|
| 01 | [Loan Default Prediction](01_loan_default_prediction.ipynb) | Which loan applications are likely to default, and which should go to manual review? | Logistic regression, odds multipliers, expected-value review threshold |
| 02 | [Fuel Waste Prediction](02_fuel_waste_prediction.ipynb) | Which fleet trips burn excess fuel, and what's causing it? | HistGradientBoosting regression, MAE in litres and dollars, waste drivers |
| 03 | [Marketing KPI: Lead Conversion](03_marketing_kpi_prediction.ipynb) | Which leads will become customers, so sales works the best ones first? | HistGradientBoosting vs. logistic benchmark, lift and capture curve, capacity-aware cut-off |
| 04 | [Sales Driver Analysis](04_sales_driver_analysis.ipynb) | What drives weekly sales, and which levers are worth pulling? | Ridge regression with plain-unit effects, boosting cross-check, return on spend, what-if scenario |
| 05 | [Customer Segmentation](05_customer_segmentation.ipynb) | What natural customer groups exist, and how should we treat each one? | K-means with elbow and silhouette, stability check, segment profiles, action table, rulebook |

## How to use these notebooks

1. **Open a notebook** in Google Colab or Jupyter. (In Colab, use *File → Open notebook → GitHub* and paste this repository's URL.)
2. **Run it once as-is.** With `CSV_PATH = None` it builds the synthetic demo data and runs top to bottom, so you can see every step working.
3. **Edit the CONFIG cell** (the only cell you need to change):
   - `CSV_PATH`: the path to your own CSV file
   - The ID, target, numeric, and categorical **column names** that match your file
   - The **business numbers** (costs, values, margins, capacity) so thresholds and ROI reflect your economics
4. **Run all cells top to bottom** (*Runtime → Run all* in Colab). Every notebook checks your CONFIG first and gives a clear message if a column name doesn't match.
5. **Read the plain-language outputs** and the closing **"What to do next"** section, then pilot the recommended action before rolling it out widely.

**Requirements:** Python 3.9+, `pandas`, `numpy`, `scikit-learn` (1.3 or newer), `matplotlib`, `seaborn`, `scipy`, and `joblib`. All of them come preinstalled in Google Colab. Locally, use the repository's [`requirements.txt`](../requirements.txt).

## What every example has in common

- **CONFIG-first design:** change the file path and column names, and the rest runs on its own
- **Leakage-safe pipelines:** all cleaning (imputing, scaling, encoding) happens inside a scikit-learn `Pipeline` / `ColumnTransformer`
- **Honest evaluation:** a dummy baseline, cross-validation, light tuning that prefers the simplest setting within one standard error of the best, and a held-out test set
- **Business metrics:** dollars, expected value, lift, and capacity, not just accuracy
- **Plain-language interpretation:** coefficients, odds, or permutation importance explained in sentences
- **Small vs. large data notes:** what to change when you have hundreds of rows versus hundreds of thousands

> These examples are for education and inspiration. Validate any model on your own data, and run a small controlled test, before using it for real decisions. For regulated uses such as lending, also check with your compliance team.

---
© Danny Barren. Original examples accompanying *Machine Learning for Business Insights: Plug-and-Play Code for Small and Large Datasets*.
