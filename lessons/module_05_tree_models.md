# Module 5: Tree-Based Models and Ensembles

**Estimated time:** 3.5 hours total (about 1 hour 15 minutes of reading, 2 hours 15 minutes hands-on)
**Primary algorithms:** Decision trees, random forest, extra trees, gradient boosting (scikit-learn HistGradientBoosting; optional XGBoost and LightGBM)

---

## Why This Module Matters

Linear and logistic regression (Modules 3 and 4) assume each factor has a steady, straight-line effect. Real business data often doesn't work that way:

- A discount might do nothing below 10%, then sharply lift sales above it
- Long-tenure customers might only churn when support tickets **and** price increases happen together
- Delivery delays might hurt small orders badly but barely matter for large ones

Tree-based models capture these **thresholds**, **curves**, and **combinations** automatically. They're the workhorses of applied ML on business data and often the most accurate classical models available.

The trade-off is that they're harder to explain than a coefficient table. This module teaches you to use them well, to explain them honestly, and to know when the extra accuracy is actually worth it.

---

## Lesson 5.1: Decision Trees as Flowcharts

### Plain-language explanation

A decision tree asks a series of yes/no questions about your data, splitting it into smaller and smaller groups until each group is fairly uniform. Each final group (called a **leaf**) gets a prediction: a number for regression, or a probability for classification.

### Business analogy

It's a triage flowchart at an urgent-care desk:

```text
Is the order over $5,000?
├── Yes → Is the customer new (under 6 months)?
│         ├── Yes → HIGH risk of late payment (38%)
│         └── No  → LOW risk (6%)
└── No  → Has the customer paid late before?
          ├── Yes → MEDIUM risk (21%)
          └── No  → LOW risk (4%)
```
*(Example figures for illustration only.)*

Anyone can follow that, with no software needed.

### How a tree chooses its questions

At each step, the tree tries every possible split on every input and picks the one that best separates the outcomes. It's like a detective always asking the single question that rules out the most suspects.

### Why it matters for decisions

Shallow trees (two to four levels deep) produce **operating rules** you can hand to a credit team, a dispatcher, or a sales manager. Sometimes a rule that's 90% as accurate as a complex model, but fully understood, creates more value than the complex model ever would.

---

## Lesson 5.2: Why Single Trees Overfit

### Plain-language explanation

If you let a tree keep splitting, it will eventually create a leaf for almost every row. It "explains" your past data perfectly and generalizes terribly.

### Business analogy

A manager who builds a rule for every exception: "Customers in Dayton who signed up in March, bought blue widgets, and called support on a Thursday always renew." True for the two customers it describes. Meaningless for everyone else.

### The settings that keep trees in check

| Setting | Plain-English meaning | Typical starting value |
|---|---|---|
| `max_depth` | How many questions deep the flowchart can go | 3–5 |
| `min_samples_leaf` | Smallest group allowed at the end of a branch | 5–20 (more on small data, as a share of rows) |
| `min_samples_split` | Smallest group allowed to be split further | 10–40 |

**Rule of thumb:** each leaf should hold enough cases that you'd trust a percentage based on it. You wouldn't trust "38% late-payment rate" based on 3 customers.

### Why it matters for decisions

A deep tree's rules look specific and confident, which makes them dangerously persuasive. Limiting depth and leaf size keeps rules honest.

---

## Lesson 5.3: Random Forests, the Wisdom of Crowds

### Plain-language explanation

A **random forest** builds hundreds of decision trees, each trained on a slightly different random sample of your rows and allowed to consider only a random subset of inputs at each split. It then averages their predictions.

### Business analogy

Instead of asking one expert, you ask 300 experts who each saw slightly different case files and had different specialties. Individually, each is a bit quirky. Together, their quirks cancel out and the average is remarkably reliable.

### Why it works

- Each tree overfits in its own way
- Averaging many different overfit trees produces a stable, accurate result
- The randomness in inputs stops one strong factor from dominating every tree

### Key settings

| Setting | Plain-English meaning | Typical starting value |
|---|---|---|
| `n_estimators` | Number of trees | 300–500 (more is rarely worse, just slower) |
| `max_depth` | Depth limit per tree | None (unlimited) on large data; 4–8 on small data |
| `min_samples_leaf` | Smallest final group per tree | 1–5 on large data; 5–10+ on small data |
| `max_features` | Share of inputs each split can consider | `"sqrt"` for classification; 0.3–0.5 for regression |

### Strengths

- Robust "out of the box," and hard to badly misconfigure
- Handles curves, thresholds, and interactions automatically
- No scaling needed
- Works reasonably on mid-sized data

### Extra trees (a close cousin)

**Extra trees** (extremely randomized trees) add even more randomness by picking split points at random rather than searching for the best one. They're faster and sometimes more stable on noisy or small data. The template tries both.

---

## Lesson 5.4: Gradient Boosting, Learning from Mistakes

### Plain-language explanation

**Gradient boosting** builds trees **one after another**. Each new tree focuses on the errors the previous trees made. The final prediction adds up all the trees' contributions.

### Business analogy

An editor reviewing a report in rounds. The first pass fixes the biggest problems. The second fixes what the first missed. Each round makes smaller, more targeted corrections. After 200 rounds, the report is excellent. After 2,000 rounds, the editor starts "fixing" things that weren't broken, and that's overfitting.

### Random forest vs. gradient boosting

| | Random forest | Gradient boosting |
|---|---|---|
| How trees are built | Independently, in parallel | One after another, each fixing earlier errors |
| Typical tree depth | Deep | Shallow (3–8 levels) |
| Out-of-the-box performance | Very good | Good; excellent once tuned |
| Peak performance | Very good | Often the best of any classical model |
| Risk of overfitting | Low | Higher; needs care |
| Sensitivity to settings | Low | Medium to high |
| Best for | Reliable first choice, small-to-mid data | Larger data where extra accuracy pays |

### Key settings

| Setting | Plain-English meaning | Typical starting value |
|---|---|---|
| `learning_rate` | How big each correction step is | 0.05–0.1 |
| `n_estimators` / `max_iter` | Number of correction rounds | Let early stopping decide |
| `max_depth` | Depth of each small tree | 3–6 |
| `min_samples_leaf` | Smallest final group | 20+ on large data |
| Early stopping | Stop adding trees when validation scores stop improving | **Always on** |

**The learning-rate trade-off:** a smaller learning rate means more, smaller steps. It's usually more accurate but slower. 0.05 with early stopping is a sensible default.

### Which boosting library?

| Library | When to use |
|---|---|
| **scikit-learn HistGradientBoosting** | Default. Built in, fast, handles missing values and categories natively. |
| **LightGBM** | Very large datasets (hundreds of thousands of rows+); fastest option |
| **XGBoost** | Widely used and well documented; similar performance to LightGBM |

For most business datasets, HistGradientBoosting is all you need. The template supports all three with one config setting.

---

## Lesson 5.5: Feature Importance, and Its Traps

### Plain-language explanation

Tree models don't have coefficients, so you need another way to see which inputs matter. **Feature importance** ranks inputs by how much they contribute to the model's predictions.

### Two kinds of importance

**1. Built-in (impurity-based) importance**
- Comes free with every tree model
- Measures how much each input improved the splits
- **Trap:** it's biased toward inputs with many distinct values (like customer ID, exact dollar amounts, or zip codes), even when they're useless. An ID column can rank as "most important" while being pure noise.

**2. Permutation importance** (recommended)
- Shuffle one input's values randomly and measure how much the model's accuracy drops
- Big drop means the model relies on that input; no drop means it doesn't
- Measured on **validation data**, so it reflects real predictive value
- Works for any model, so you can compare trees with regression fairly

### Business analogy for permutation importance

To see how much a team relies on one employee, have them take a week off and see how much output drops. If nothing changes, they weren't critical to that work, whatever their job title says.

### Two more traps

- **Overlapping inputs share credit.** If "total spend" and "number of orders" move together, shuffling one barely hurts because the model leans on the other. Both look less important than they are. Check correlations (Module 3, Lesson 3.4) and interpret related inputs as a group.
- **Importance isn't direction.** It tells you an input **matters**, not whether more of it is good or bad. Module 8 adds partial dependence plots and SHAP to show direction and shape.

### Why it matters for decisions

Feature importance is often what leadership asks for: "What drives this?" Using the wrong kind can send your team chasing a meaningless variable. Use permutation importance, and always sanity-check the top drivers with business logic.

---

## Lesson 5.6: Handling Categories in Tree Models

### The good news

Tree models are less fussy than linear models:
- **No scaling needed**
- **Ordinal encoding usually works fine**, even for unordered categories, because trees can split on any value
- **HistGradientBoosting and LightGBM handle categories natively**: you just tell them which columns are categorical

### What still matters

- **Group rare categories** (Module 2). Trees can still overfit to a category with three rows.
- **Watch high-cardinality columns** (hundreds of distinct values, like zip codes or product SKUs). Either group them, replace them with a meaningful summary (like average order value per zip), or drop them.

The template adjusts your Module 2 pipeline automatically for tree models: it switches to ordinal or native category handling and skips scaling.

---

## Lesson 5.7: Should You Use a Tree Model at All?

Always compare tree models against your Module 3 or Module 4 linear model. Then decide.

### Use the linear model when:
- Scores are close (within the cross-validation spread)
- You need coefficients, odds ratios, or confidence intervals for stakeholders
- You have small data
- The decision is high-stakes and must be fully explainable (lending, pricing, HR)

### Use a tree ensemble when:
- It clearly beats the linear model, beyond the noise
- The extra accuracy has real dollar value (check with your Module 4 profit table or Module 3 error analysis)
- Residuals or business knowledge suggest thresholds and interactions
- You'll explain it with permutation importance and the tools in Module 8

### Use a shallow decision tree when:
- Frontline teams need a rule they can follow without software
- It performs close to the ensemble, which happens more often than people expect

### Business analogy

Choosing a model is like choosing a vehicle. A bicycle (linear model) is simple, cheap, and easy to repair. A sports car (gradient boosting) is faster but needs more maintenance and expertise. For a two-mile commute, the bicycle often wins. Make the sports car earn its place.

---

## Step-by-Step Process: Running a Tree-Based Analysis

1. **Start from your Module 1 brief and Module 3 or 4 results.** Your linear model's cross-validated score is the benchmark to beat.
2. **Load your cleaned data and switch the pipeline to tree mode** (no scaling; ordinal or native categories).
3. **Check data size** and choose the small-data or large-data path.
4. **Fit a shallow decision tree** (depth 2–4). Record its score and print its rules.
5. **Fit a random forest** with sensible defaults. Record its cross-validated score and spread.
6. **Fit extra trees.** Record its score.
7. **Fit gradient boosting** (HistGradientBoosting, or XGBoost or LightGBM on large data) **with early stopping**. Record its score.
8. **Build the comparison table:** baseline, linear model, shallow tree, random forest, extra trees, boosting. Include score spreads.
9. **Decide whether any tree model is worth it** using Lesson 5.7. If not, stop here and stay with the linear model. That's a valid outcome.
10. **Calculate permutation importance** for your chosen model on validation data.
11. **Sanity-check the top drivers.** Do they make business sense? Are any suspiciously high-cardinality or possible leakage?
12. **Compare drivers across models.** Do the linear model and the tree model agree on the top factors? Agreement builds confidence; disagreement is worth investigating.
13. **Translate into business terms.** For classification, rerun the Module 4 lift, gain, and threshold analysis with the new model. For regression, compare error in business units.
14. **Write the model decision summary:** which model, why, how much better than the benchmark, top drivers, and caveats.

---

## Common Pitfalls and How to Avoid Them

### 1. Overfitting on small data (the biggest risk in this module)
**What it looks like:** 120 rows, a gradient boosting model with a near-perfect training score, and cross-validated scores that bounce around wildly between rounds.
**Analogy:** A powerful tool in a small room. Gradient boosting has enough flexibility to memorize every quirk in 120 rows.
**Fixes:**
- On small data, **default to a random forest or extra trees with limited depth and larger leaves**, not boosting
- Set `min_samples_leaf` so each leaf holds a meaningful share of your data (for example, 5–10% of rows)
- Use repeated cross-validation and look at the spread, not just the average
- Require the tree model to beat the linear model by more than the score spread before choosing it
- If in doubt, use the linear model

### 2. Trusting built-in feature importance
**What it looks like:** "Customer ID is our top driver!"
**Fix:** Use permutation importance on validation data. Drop ID columns entirely (Module 2).

### 3. Boosting without early stopping
**What it looks like:** Setting 5,000 rounds and walking away.
**Fix:** Always enable early stopping. The template sets it on by default.

### 4. Reading importance as direction or cause
**What it looks like:** "Discount rate is the top driver, so let's increase discounts."
**Fix:** Importance says it matters, not how. Use partial dependence or SHAP (Module 8) to see direction, and treat any causal claim as something to test.

### 5. Choosing the most complex model by default
**What it looks like:** Using XGBoost because "it wins competitions," for a 200-row dataset where Ridge regression performs the same.
**Fix:** Make every model beat the simpler one by a meaningful margin. Simpler wins ties.

### 6. Tuning everything at once
**What it looks like:** A grid of eight settings with five values each, running overnight on 300 rows.
**Fix:** Start with defaults. Module 7 covers practical tuning that focuses on the two or three settings that matter.

### 7. Leakage that trees exploit ruthlessly
**What it looks like:** A suspiciously perfect score, with one input dominating importance.
**Why trees make it worse:** They'll find and exploit any leak instantly, including subtle ones a linear model would only partly pick up.
**Fix:** If one input dominates and the score seems too good, revisit the Module 1 leakage checklist for that input.

### 8. Forgetting trees can't extrapolate
**What it looks like:** Using a tree model to forecast sales for a record-breaking holiday season.
**Why:** Trees predict using averages of past groups, so they can never predict above the highest value (or below the lowest) they've seen.
**Fix:** For forecasting beyond past ranges, prefer linear models, or combine both.

---

## Small vs. Large Data: What Changes in This Module

| Aspect | Small data (30–300 rows) | Large data (thousands+) |
|---|---|---|
| Default tree model | Random forest or extra trees, with limited depth | Gradient boosting (HistGradientBoosting, LightGBM, or XGBoost) |
| Shallow single tree | Very useful for rules; depth 2–3 | Useful for rules; ensembles usually win on accuracy |
| Gradient boosting | Usually avoid; high overfitting risk | Often the best performer |
| Tree depth | Shallow (3–6) | Deeper allowed for forests; 4–8 for boosting |
| Leaf size | Large: roughly 5–10% of rows | Small: 1–20 rows for forests, 20+ for boosting |
| Validation | Repeated k-fold; examine score spread | 5-fold, or train/validation/test with early stopping |
| Benchmark to beat | Linear model, which often wins | Linear model, which usually loses but stays as a sanity check |
| Feature importance | Permutation importance will be noisy; repeat it and look for consistent top drivers | Permutation importance is reliable |
| Number of inputs | Still tight; trees don't fix having too few rows | Can include many; importance helps prune |
| Speed | Not an issue | Use HistGradientBoosting or LightGBM; sample during exploration |
| Libraries | scikit-learn only | scikit-learn, plus optional LightGBM or XGBoost |

**Bottom line:** the process is the same, but the default model flips. On small data, tree ensembles are a cross-check and a source of simple rules, and the linear model often stays your final choice. On large data, gradient boosting typically earns its place, and the linear model becomes the benchmark that keeps it honest.

---

## Code Structure / Pipeline Outline

The tree-model workflow follows this outline:

```text
1. CONFIG CELL
   - path to cleaned data and Module 2 pipeline settings
   - target column; task type: "regression" | "classification"
   - main metric (from Module 3 or 4)
   - linear benchmark score (or auto-run linear model)
   - boosting library: "sklearn" | "lightgbm" | "xgboost"
   - data size mode: "small" | "large" (auto-suggested)
   - permutation importance repeats (e.g., 20 for small, 5 for large)

2. LOAD DATA
   - load cleaned data
   - split inputs (X) and target (y)

3. TREE-MODE PREPROCESSING
   - reuse Module 2 cleaning
   - numeric: fill blanks only (no scaling)
   - categorical: ordinal encoding or native category handling
   - drop/group high-cardinality columns

4. VALIDATION SETUP
   - small mode: repeated (stratified) k-fold
   - large mode: 5-fold, or train/validation/test split
     with a validation set for early stopping

5. BENCHMARKS
   - baseline (from Module 1)
   - linear model (from Module 3 or 4)

6. SHALLOW DECISION TREE
   - depth 2-4, sensible leaf size
   - cross-validated score
   - printed rules + tree diagram

7. ENSEMBLE MODELS
   - random forest (size-mode defaults)
   - extra trees (size-mode defaults)
   - gradient boosting with early stopping
     (skipped or flagged as "high risk" in small mode)
   - cross-validated scores with spread for each

8. MODEL COMPARISON TABLE
   - all models vs. baseline and linear benchmark
   - improvement vs. linear, compared to score spread
   - automatic "worth it?" flag

9. FINAL MODEL FIT
   - chosen model fit on training data
   - (boosting: number of rounds from early stopping)

10. FEATURE IMPORTANCE
    - built-in importance (shown for comparison, with warning)
    - permutation importance on validation data, repeated
    - bar chart with error bars

11. DRIVER COMPARISON
    - top drivers: linear model vs. tree model side by side
    - flag disagreements for review

12. LEAKAGE AND SANITY FLAGS
    - warn if one input dominates importance (possible leakage)
    - warn if any single input alone scores suspiciously well
      (e.g., ROC-AUC above 0.90 by itself)
    - warn if the training score is far above the CV score
      (overfitting gap)
    - warn if new rows fall outside the training range
      (trees can't extrapolate)

13. PARTIAL DEPENDENCE PREVIEW
    - "what happens if" charts for the top 3 drivers
    - ICE lines to see whether everyone behaves the same
    - (covered in depth in Module 8)

14. OUTPUT SUMMARY
    - model card: data size, models tried, chosen model,
      CV score and spread, improvement vs. linear benchmark
    - plain-English rules from the shallow tree
    - saved model file (joblib) and rules text file
```

In this repository the outline is split across three companion notebooks, each built CONFIG-first so you only change the file path and a few column names:

| Notebook | What it covers |
|---|---|
| [`notebooks/03_decision_tree.ipynb`](../notebooks/03_decision_tree.ipynb) | A single, shallow decision tree: depth checks, readable leaf rules, small-leaf warnings, and a stability check on the first split |
| [`notebooks/04_random_forest_churn.ipynb`](../notebooks/04_random_forest_churn.ipynb) | A random forest churn model with a leakage sniff test, a logistic benchmark, light tuning, threshold and profit tables, and permutation importance |
| [`notebooks/05_gradient_boosting.ipynb`](../notebooks/05_gradient_boosting.ipynb) | Histogram gradient boosting with early stopping (on for large data, off for small), randomized tuning, and optional XGBoost / LightGBM upgrades |

---

## Hands-On Exercise

**Time:** about 2 hours 15 minutes

1. **Load your Module 2 outputs** (or a provided demo dataset) and confirm your linear benchmark score from Module 3 or 4. (10 minutes)
2. **Fit a shallow decision tree** (depth 2–4). Print the rules and read them aloud. Would a frontline manager recognize these rules as sensible? (20 minutes)
3. **Run the random forest** with the size-mode defaults. Record its cross-validated score and spread. (15 minutes)
4. **Run gradient boosting.** On large data, use early stopping and note how many rounds it chose. On small data, note whether the template flagged it as high risk. (20 minutes)
5. **Fill in the comparison table.** Does any tree model beat the linear benchmark by more than the score spread? If not, say so plainly. (15 minutes)
6. **Compare built-in importance with permutation importance.** Note any input that ranks very differently between the two. (15 minutes)
7. **Compare top drivers** between the linear model and your best tree model. Where they agree, confidence goes up. Where they disagree, write down a possible reason. (20 minutes)
8. **Review the leakage and sanity flags.** Investigate anything flagged. (10 minutes)
9. **Write a one-paragraph recommendation:** which model you'd use, and why the extra complexity is (or isn't) worth it for this decision. (10 minutes)

---

## Discussion and Reflection Questions

1. **Your gradient boosting model beats the linear model by a small margin, but the linear model is far easier to explain.** Who in your organization would need to trust and act on this model? Which would you choose, and what improvement would change your mind?

2. **Look at your shallow decision tree's rules.** Could your team use them directly, as a checklist, without any model at all? What would you lose by doing that, and what would you gain?

3. **Find an input that ranks high in importance.** Importance tells you the model relies on it, not which direction it pushes or whether it causes the outcome. How would you find out the direction, and how would you test whether changing it changes the result?

---

## What "Done" Looks Like

You've finished Module 5 when you have:

- [ ] A shallow decision tree with printed, plain-English rules
- [ ] Cross-validated scores, with spread, for a random forest and gradient boosting
- [ ] A comparison table against the baseline and the linear benchmark, with a clear "worth it?" call
- [ ] Permutation importance for the chosen model, with built-in importance shown only for comparison
- [ ] A side-by-side driver comparison between the linear and tree models
- [ ] All leakage and sanity flags reviewed and resolved
- [ ] A one-paragraph model recommendation a manager could follow

If the linear model wins or ties, that's a strong result, not a failure. You've confirmed that the simple, explainable model is good enough, and you've earned the right to say so with evidence.

**Next up:** Module 6, Clustering and Customer Segmentation, where there's no target to predict and the goal shifts to finding natural groups you can act on.
