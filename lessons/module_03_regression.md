# Module 3: Regression for Forecasting and Drivers

**Estimated time:** 3 hours total (about 1 hour of reading, 2 hours hands-on)
**Primary algorithms:** Linear regression, Ridge, Lasso, ElasticNet

---

## Why This Module Matters

Regression answers two of the most common business questions:

1. **"How much?"** How much will we sell next month? How big will this deal be? How long will this job take?
2. **"What drives it?"** Which factors push revenue, cost, or delivery time up or down, and by roughly how much?

The second question is often worth more than the first. A forecast tells you what's coming. A driver analysis tells you which levers to pull.

Linear regression is also the most interpretable model in this course. If you understand it well, every later module gets easier.

---

## Lesson 3.1: What Linear Regression Does

### Plain-language explanation

Linear regression finds the best straight-line relationship between your inputs and a number you want to predict. It gives you one number per input, called a **coefficient**, that says how much the outcome changes when that input goes up by one unit, holding the other inputs steady.

### Business analogy

Think of a pricing sheet for a contractor:

- Base fee: $500
- Plus $40 per square foot
- Plus $1,200 if it's a rush job
- Minus $300 for repeat customers

That's a linear regression. The base fee is the **intercept** (the starting point), and each "plus" or "minus" is a coefficient. Regression builds this pricing sheet for you by looking at past jobs.

### Why it matters for decisions

The output reads like a business rule. "Each extra sales rep in a region is associated with about $18,000 more monthly revenue" is a sentence a leadership team can discuss, challenge, and act on.

---

## Lesson 3.2: Reading Coefficients Correctly

### The key phrase: "holding everything else constant"

A coefficient tells you the effect of one input **while all the other inputs stay the same**. That's what makes regression more useful than looking at one chart at a time.

**Example:** You notice stores with more staff have higher sales. But bigger stores also have more staff. Regression separates the two: "Among stores of the same size, each extra staff member is associated with $X more in sales."

### Raw vs. standardized coefficients

| Type | What it tells you | When to use it |
|---|---|---|
| **Raw** | Effect per one real unit (per dollar, per day, per employee) | Explaining results to stakeholders: "each extra day of delay costs about $240" |
| **Standardized** | Effect per "typical variation" in that input | Comparing which inputs matter most, since raw units can't be compared directly |

**Analogy:** You can't say "square footage matters more than number of bathrooms" by comparing $40 per square foot to $8,000 per bathroom, because the units differ. Standardized coefficients put them on the same scale so you can rank them.

The template reports both.

### For category inputs

Coefficients on one-hot encoded categories are **compared to a reference group**. If "Region: South" has a coefficient of -$5,000, it means South averages $5,000 less than the reference region (say, North), all else equal. Always know which group is the reference.

### Why it matters for decisions

Misreading coefficients is the fastest way to make a bad call with a good model. Knowing what a coefficient does and doesn't say keeps your recommendations defensible.

---

## Lesson 3.3: Measuring How Good the Predictions Are

### The three numbers you'll see

| Metric | Plain-English meaning | Best for |
|---|---|---|
| **MAE** (Mean Absolute Error) | "On average, our predictions are off by $X." | Talking to stakeholders. It's in real units and easy to understand. |
| **RMSE** (Root Mean Squared Error) | Like MAE, but punishes big misses more heavily. | When a few big misses are costly (inventory, staffing). |
| **R²** (R-squared) | "The model explains X% of the variation in the outcome." | Comparing models on the same data. Don't present it alone. |

### Always compare to the baseline

From Module 1: the baseline for regression is "always predict the average." Report results like this:

> "Predicting the average gets us within $4,200 per job on average. Our model gets us within $2,600. That's a 38% improvement."

That sentence is worth more than any R² value.

### Always measure on unseen data

Scores on the data the model learned from are always too optimistic. Use cross-validation (explained below, covered in depth in Module 7) for every number you report.

---

## Lesson 3.4: Multicollinearity (When Inputs Overlap)

### Plain-language explanation

**Multicollinearity** is when two or more inputs move together so closely that the model can't tell which one is doing the work.

### Business analogy

If two salespeople always go to client meetings together, you can't tell who's closing the deals. You might credit one with everything and the other with nothing, and next month the credit flips.

### Signs of it

- Coefficients change a lot or flip sign when you add or remove a column
- A coefficient points the "wrong" direction (more marketing spend linked to fewer sales)
- Two inputs have a correlation above about 0.8

### Fixes

- Keep the one that's easier to act on and drop the other
- Combine them into one feature (total marketing spend instead of three separate channels)
- Use Ridge regression, which handles this gracefully (next lesson)

### Why it matters for decisions

Multicollinearity doesn't hurt predictions much, but it wrecks driver analysis. If you're using coefficients to decide where to invest, you need to check for it.

---

## Lesson 3.5: Regularization, Your Built-In Guardrail

### Plain-language explanation

**Regularization** gently pulls coefficients toward zero unless the data strongly supports them. It stops the model from overreacting to noise.

### Business analogy

It's like a skeptical CFO. A new budget line has to justify itself. Small, unproven effects get trimmed. Big, consistent effects survive.

### The three regularized versions

| Model | What it does | When to use it |
|---|---|---|
| **Ridge** | Shrinks all coefficients a bit; keeps every input | Default choice, especially with overlapping inputs or small data |
| **Lasso** | Shrinks some coefficients all the way to zero | When you want the model to pick a short list of drivers |
| **ElasticNet** | A blend of Ridge and Lasso | Many overlapping inputs and you still want some selection |

### The strength setting

Each has a dial (called **alpha**) for how skeptical it is:
- **Low alpha:** behaves like plain linear regression
- **High alpha:** very cautious, coefficients shrink a lot

You don't need to pick alpha by hand. The template uses built-in cross-validated versions (RidgeCV, LassoCV) that try several values and pick the best automatically. Module 7 covers tuning in more depth.

### Why it matters for decisions

On small data, regularization is the difference between a stable driver list and one that changes every time you add ten rows. It's the single most useful protection against overfitting in this module.

**Important:** regularization requires scaled inputs. Your Module 2 pipeline already does this.

---

## Lesson 3.6: Checking If a Straight Line Is Good Enough

### Residuals, explained simply

A **residual** is the gap between what the model predicted and what actually happened. Plotting residuals reveals problems the scores hide.

### What to look for in a residual plot

| Pattern | What it means | Fix |
|---|---|---|
| Random scatter around zero | Good. The model fits reasonably. | None needed |
| Curved pattern | The relationship isn't a straight line | Add a squared term, log transform, or try tree models (Module 5) |
| Spread widens as predictions grow | Errors are bigger for bigger values | Log-transform the target |
| A few huge residuals | Outliers or unusual cases | Investigate them (Module 2, Lesson 2.3) |

### Log transforms for money values

Revenue, prices, and deal sizes are usually skewed: lots of small values and a few big ones. Taking the log of the target often fixes uneven errors. Coefficients then read as **percentage effects**: "each extra sales call is associated with about 3% higher deal value." Percentage effects are often more natural in business anyway.

---

## Lesson 3.7: How Sure Are We? Confidence Intervals

### Plain-language explanation

A coefficient is an estimate, not a fact. A **confidence interval** gives a range of plausible values.

> "Each extra day of delivery delay is associated with $180 to $310 in lost order value."

That's far more honest, and more useful, than "$245."

### Two ways the course gets them

- **statsmodels:** gives confidence intervals and p-values for plain linear regression, in a classic statistics-style table
- **Bootstrapping:** re-fits the model hundreds of times on resampled versions of your data and sees how much coefficients move. Works for any model, including Ridge and Lasso, and is especially useful on small data.

### Reading intervals for decisions

- **Interval doesn't include zero** (e.g., $180 to $310): you can be reasonably confident the effect is real and in that direction
- **Interval crosses zero** (e.g., -$50 to $400): the data can't tell whether the effect is positive, negative, or nothing. Don't build a strategy on it.
- **Very wide interval:** you need more data before acting

### Why it matters for decisions

Confidence intervals stop you from betting the budget on a driver that might be noise. On small data, they're not optional.

---

## Lesson 3.8: Correlation, Not Causation (Again)

Regression coefficients describe **association**, not cause and effect. "Customers who attend webinars spend 20% more" doesn't mean webinars cause spending. Engaged customers may simply do both.

Use regression to:
- **Prioritize** which levers to test
- **Forecast** outcomes under conditions similar to the past
- **Explain** patterns to stakeholders, with the right caveats

Don't use it alone to:
- Claim that changing an input **will** change the outcome by exactly that amount

When a driver looks promising, the next step is a pilot or A/B test.

---

## Step-by-Step Process: Running a Regression Analysis

1. **Start from your Module 1 brief.** Confirm the target is a number and the decision is clear.
2. **Load your Module 2 pipeline and cleaned data.**
3. **Check the target's distribution.** If it's heavily skewed (common with money), plan to log-transform it.
4. **Check for overlapping inputs.** Look at the correlation table and handle any pairs above about 0.8.
5. **Calculate the baseline.** MAE of always predicting the average, using cross-validation.
6. **Fit plain linear regression** as a first, transparent model. Record its cross-validated MAE.
7. **Fit Ridge and Lasso** with built-in alpha selection. Record their cross-validated MAE.
8. **Compare all three to the baseline.** If none beat it meaningfully, stop and revisit your features.
9. **Pick the model.** When scores are close, choose the simpler or more stable one.
10. **Check residuals.** Look for curves, widening spread, or outliers. Adjust and re-run if needed.
11. **Get confidence intervals** for the coefficients (statsmodels or bootstrap).
12. **Write the driver summary.** Top three to five drivers in plain English, each with direction, size, range, and a "can we act on it?" note.
13. **Write the forecast summary** (if forecasting). Predicted value plus a realistic error range.

---

## Common Pitfalls and How to Avoid Them

### 1. Overfitting on small data
**What it looks like:** 60 rows, 15 inputs, an impressive R² on the training data, and coefficients that swing wildly when you re-run with slightly different data.
**Analogy:** Drawing a line through every dot on a scatter plot. It fits perfectly and predicts nothing.
**Fixes:**
- Keep the rows-per-feature ratio around 10–20 (from Module 2)
- Default to Ridge instead of plain linear regression
- Report cross-validated scores only, never training scores
- Use bootstrapped confidence intervals; if a driver's interval crosses zero, don't promote it
- Re-run the analysis leaving out a random 10% of rows. If the top drivers change, your results aren't stable enough to act on yet.

### 2. Reporting training-data scores
**What it looks like:** "Our model has an R² of 0.94!" measured on the same data it learned from.
**Fix:** Always use cross-validation. The template does this by default.

### 3. Comparing raw coefficients to rank importance
**What it looks like:** "Price per unit has a coefficient of 2,000 and email opens has 15, so price matters 130 times more."
**Fix:** Use standardized coefficients to rank. Use raw coefficients to explain.

### 4. Ignoring the reference category
**What it looks like:** "The West region coefficient is -$3,000, so West is bad."
**Fix:** It's -$3,000 compared to the reference region. State the comparison explicitly.

### 5. Trusting coefficients with overlapping inputs
**What it looks like:** "Our model says email marketing hurts sales," because email and social spend always rise together.
**Fix:** Check correlations first; combine or drop overlapping inputs, or use Ridge.

### 6. Treating drivers as causes
**What it looks like:** "Regression says reps who make more calls close more, so let's require 50 calls a day."
**Fix:** Present drivers as candidates for action, and recommend a pilot before scaling up.

### 7. Forecasting outside the data
**What it looks like:** Using a model built on stores of 2,000–8,000 square feet to predict a 30,000-square-foot store.
**Analogy:** A teen growth chart can't predict height at 40.
**Fix:** Only predict within the range of your past data. The template flags inputs outside the training range.

---

## Small vs. Large Data: What Changes in This Module

| Aspect | Small data (30–300 rows) | Large data (thousands+) |
|---|---|---|
| Default model | **Ridge**, almost always | Ridge or Lasso; plain linear regression is fine if inputs don't overlap |
| Number of inputs | Tight: roughly rows ÷ 15 | Can be larger; Lasso helps trim |
| Validation | Repeated k-fold or leave-one-out cross-validation | Standard 5-fold cross-validation or a held-out test set |
| Confidence intervals | **Essential.** Bootstrap them. Expect wide ranges. | Usually narrow; still report them |
| Stability check | Re-run on subsets. Drivers that disappear aren't real. | Less critical, but still useful |
| Residual checks | Few points, so look at each large residual individually | Look at overall patterns; sample if needed |
| What results mean | Directional: "probably positive, roughly this size" | More precise; can support automated forecasts |
| Statistical significance | Hard to reach; that's normal. Focus on direction and plausibility. | Easy to reach, even for tiny, useless effects. Focus on whether the size matters to the business. |
| When regression isn't enough | Usually stick with it; complex models overfit | Consider tree models (Module 5) if residuals show curves or interactions |

**Bottom line:** the process is the same. On small data, regression is often your best tool, but you must lean on regularization, cross-validation, and confidence intervals. On large data, regression is a strong, transparent benchmark that more complex models must beat.

---

## Code Structure / Pipeline Outline

This module's companion notebook is [`notebooks/01_linear_regression.ipynb`](../notebooks/01_linear_regression.ipynb). Its outline:

```text
1. CONFIG CELL
   - path to cleaned data and saved Module 2 pipeline
   - target column
   - log_transform_target: True / False
   - data size mode: "small" | "large" (auto-suggested)
   - number of bootstrap rounds (e.g., 500 for small data)

2. LOAD DATA AND PIPELINE
   - load cleaned data
   - split inputs (X) and target (y)
   - optional: apply log to target

3. PRE-MODEL CHECKS
   - target distribution chart
   - correlation table for numeric inputs
   - flag pairs with correlation > 0.8
   - rows-per-feature check

4. VALIDATION SETUP
   - small mode: repeated k-fold or leave-one-out
   - large mode: standard 5-fold

5. BASELINE
   - "always predict the average" model
   - cross-validated MAE and RMSE

6. MODEL COMPARISON
   - pipeline + plain linear regression
   - pipeline + RidgeCV (auto alpha)
   - pipeline + LassoCV (auto alpha)
   - optional: pipeline + ElasticNetCV
   - cross-validated MAE, RMSE, R² for each
   - comparison table vs. baseline (% improvement)

7. FINAL MODEL FIT
   - choose model (best score, simpler when close)
   - fit on all data

8. RESIDUAL DIAGNOSTICS
   - predicted vs. actual chart
   - residuals vs. predicted chart
   - list of largest residuals for review

9. COEFFICIENTS
   - raw coefficients (business units)
   - standardized coefficients (ranking)
   - reference categories listed
   - bar chart of standardized coefficients

10. UNCERTAINTY
    - statsmodels table (plain regression), or
    - bootstrap confidence intervals (any model)
    - flag drivers whose interval crosses zero

11. STABILITY CHECK (small mode)
    - re-fit on several random 90% subsets
    - show how often each driver stays in the top 5

12. FORECAST (optional)
    - score new rows
    - prediction with error range
    - warning for inputs outside training range

13. OUTPUT SUMMARY
    - auto-filled driver summary table
    - model card: data size, model chosen, CV error, baseline error
```

---

## Hands-On Exercise

**Time:** about 2 hours

1. **Load your Module 2 outputs** (or the provided small-business pricing dataset, about 80 rows). (10 minutes)
2. **Run the pre-model checks.** Decide whether to log-transform the target and handle any overlapping inputs. (15 minutes)
3. **Run the baseline and model comparison.** Record each model's cross-validated MAE and its improvement over baseline. (20 minutes)
4. **Choose a final model** and write one sentence on why. (5 minutes)
5. **Review residuals.** Investigate the three largest misses. Are they errors, unusual cases, or a sign the model is missing something? (20 minutes)
6. **Interpret coefficients and confidence intervals.** Identify drivers you'd trust, drivers you wouldn't, and why. (20 minutes)
7. **Run the stability check** (small data) and note any drivers that come and go. (10 minutes)
8. **Write a one-paragraph driver summary** for a non-technical manager, with one recommended next step. (20 minutes)

---

## Discussion and Reflection Questions

1. **Look at your top three drivers.** For each one, can your business actually change it? If yes, what would a small, low-risk pilot to test it look like? If not, how is knowing about it still useful (for example, in forecasting or setting expectations)?

2. **Find a driver whose confidence interval crosses zero, or one that came and went in the stability check.** If a colleague insisted that factor matters, what would you tell them? What additional data or test would settle it?

3. **Your model's average error is some dollar or unit amount.** Is that error small enough to change a real decision, such as staffing, pricing, ordering, or budgeting? What error would you need before you'd trust the forecast over your team's current gut estimate?

---

## What "Done" Looks Like

You've finished Module 3 when you have:

- [ ] A cross-validated comparison of the baseline, linear regression, Ridge, and Lasso
- [ ] A chosen final model with a one-sentence justification
- [ ] Residual plots reviewed and the largest misses explained
- [ ] A ranked list of drivers using standardized coefficients
- [ ] Confidence intervals for each driver, with unreliable ones flagged
- [ ] A stability check (if on small data)
- [ ] A plain-English driver summary with direction, size, range, and one recommended action
- [ ] (Optional) A forecast with an honest error range

If your model only modestly beats the baseline, that's still a real result. You've learned which factors matter, which don't, and how predictable your outcome actually is.

**Next up:** Module 4, Classification for Yes/No Decisions, where you'll move from predicting "how much" to predicting "will it happen," and turn probabilities into decisions your team can act on.
