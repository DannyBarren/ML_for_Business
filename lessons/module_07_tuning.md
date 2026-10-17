# Module 7: Hyperparameter Tuning and Validation (Especially for Small Data)

**Estimated time:** 3 hours total (about 1 hour of reading, 2 hours hands-on)
**Primary tools:** Cross-validation strategies, GridSearchCV, RandomizedSearchCV, nested cross-validation, optional Optuna, applied to the models from Modules 3–6

---

## Why This Module Matters

By now you've built regression, classification, tree, and clustering models, mostly with sensible defaults. This module answers two questions every stakeholder eventually asks:

1. **"How good is this model really?"** That's **validation**: getting a performance number you can trust and defend.
2. **"Could it be better?"** That's **tuning**: adjusting a model's settings to squeeze out real improvements.

Validation comes first and matters more. A modest model with an honest performance estimate is far more valuable than a "tuned" model whose score is secretly inflated. On small data especially, sloppy tuning is one of the fastest ways to fool yourself.

The good news: practical tuning is much simpler than it looks. For most business models, two or three settings matter, and good defaults get you most of the way.

---

## Lesson 7.1: What Hyperparameters Are

### Plain-language explanation

A **hyperparameter** is a setting you choose **before** training. The model can't learn it from the data. Coefficients, tree splits, and cluster centers are learned. Hyperparameters control **how** that learning happens.

### Business analogy

Think of hiring a contractor. The contractor figures out the details of the job (where each nail goes). You set the ground rules: the budget, the deadline, how cautious to be with old wiring. Hyperparameters are the ground rules.

### Examples you've already met

| Model | Hyperparameter | What it controls |
|---|---|---|
| Ridge / Lasso (Module 3) | `alpha` | How skeptical the model is of each driver |
| Logistic regression (Module 4) | `C` | Same idea; smaller C means more cautious |
| Decision tree (Module 5) | `max_depth` | How many questions deep the flowchart goes |
| Random forest (Module 5) | `min_samples_leaf` | Smallest group allowed at the end of a branch |
| Gradient boosting (Module 5) | `learning_rate` | Size of each correction step |
| K-means (Module 6) | `n_clusters` (K) | Number of segments |
| KNN (Module 4) | `n_neighbors` | How many similar cases to consult |

### Why it matters for decisions

Most hyperparameters are dials between **simple and cautious** and **flexible and risky**. Tuning is really choosing how much flexibility your data can support. That's a direct link to overfitting.

---

## Lesson 7.2: Why One Train/Test Split Isn't Enough

### Plain-language explanation

The simplest way to test a model is to hold back some data (say 20%), train on the rest, and score on the held-back part. The trouble is that the score depends heavily on **which** rows ended up in the test set, especially on small data.

### Business analogy

Judging a new salesperson on one week of results. If they happened to get easy leads that week, they look like a star. If they got tough ones, they look like a dud. You need several weeks to know.

### A quick demonstration (built into the template)

The template splits your data 20 different random ways and scores the same model each time. On a 100-row dataset, you might see something like this:

| Split | R² |
|---|---|
| Lowest of 20 | 0.31 |
| Highest of 20 | 0.74 |
| Average | 0.55 |

*(Example data for illustration only.)*

Same model, same data, wildly different scores. A single split could tell you anything from "mediocre" to "excellent."

### Why it matters for decisions

If you only report one split, you're reporting luck. Cross-validation (next lesson) replaces luck with an average and a range.

---

## Lesson 7.3: Cross-Validation, Testing Many Ways

### Plain-language explanation

**K-fold cross-validation** splits your data into K equal chunks (called **folds**). It trains on K−1 chunks and tests on the remaining one, then rotates until every chunk has been the test set once. You get K scores, and you report their **average** and **spread**.

### Business analogy

Rotating which office does the audit. Every branch gets audited by people from the other branches, and you average the findings. No single branch's quirks dominate.

### The main types

| Method | How it works | When to use |
|---|---|---|
| **K-fold** (usually 5 or 10) | Split into K chunks; rotate | Standard choice for most regression and medium-to-large data |
| **Stratified K-fold** | Like K-fold, but each chunk keeps the same share of "yes" cases | **Always** for classification, especially rare outcomes |
| **Repeated K-fold** | Runs K-fold several times with different shuffles | Small data, to steady the estimate |
| **Leave-one-out (LOO)** | Each row is its own test set | Very small data (under ~50 rows); slow on larger data |
| **Time-series split** | Train on earlier periods, test on later ones only | **Any** time-ordered data: sales, demand, monthly churn |
| **Group K-fold** | Keeps all rows from one group (customer, store) in the same fold | When one customer or store has multiple rows |

### Choosing a strategy

| Your situation | Recommended validation |
|---|---|
| Under ~100 rows | Repeated K-fold (5-fold × 10 repeats) or leave-one-out |
| 100–1,000 rows | Repeated stratified 5- or 10-fold |
| 1,000+ rows | Stratified 5-fold, or a held-out test set plus 5-fold |
| Time-ordered data | Time-series split, regardless of size |
| Multiple rows per customer/store | Group K-fold, regardless of size |

### Why it matters for decisions

The validation strategy decides whether your reported score is honest. A great score from the wrong strategy (like random splits on time-ordered data) can collapse the moment you use the model for real.

---

## Lesson 7.4: Reading the Spread, Not Just the Average

### Plain-language explanation

Every cross-validation run gives you several scores. The **average** tells you typical performance. The **spread** (standard deviation, or lowest to highest) tells you how much to trust it.

### How to report it

Instead of:
> "The model's MAE is $2,600."

Say:
> "The model's MAE is about $2,600, typically ranging from $2,200 to $3,100 across validation rounds."

### Comparing two models

| Model | Average score | Spread (±) |
|---|---|---|
| Ridge | 0.61 | ±0.07 |
| Random forest | 0.64 | ±0.09 |

*(Example data for illustration only.)*

The random forest's average is higher, but the ranges overlap heavily. **These models are effectively tied.** The rule from earlier modules applies: when tied, choose the simpler one.

### Rule of thumb

If the difference between two models' averages is smaller than either one's spread, treat them as equal.

### Why it matters for decisions

This one habit prevents most "we upgraded to a fancier model" mistakes. It's also what makes your numbers credible to skeptical stakeholders.

---

## Lesson 7.5: The Settings That Actually Matter

You don't need to tune everything. Here's the short list for each model, with sensible starting ranges.

| Model | Tune these | Starting range | Leave at default |
|---|---|---|---|
| **Ridge** | `alpha` | 0.01 to 1,000 (log scale) | Everything else |
| **Lasso** | `alpha` | 0.001 to 10 (log scale) | Everything else |
| **ElasticNet** | `alpha`, `l1_ratio` | alpha as Lasso; l1_ratio 0.1–0.9 | Everything else |
| **Logistic regression** | `C` | 0.001 to 100 (log scale) | Use `class_weight="balanced"` for rare outcomes |
| **KNN** | `n_neighbors` | 3 to 30 | Weights: try "uniform" vs. "distance" |
| **Decision tree** | `max_depth`, `min_samples_leaf` | depth 2–6; leaf 5–50 | Everything else |
| **Random forest** | `max_depth`, `min_samples_leaf`, `max_features` | depth 4–None; leaf 1–20; features 0.3–1.0 | `n_estimators`: just use 300–500 |
| **Gradient boosting** | `learning_rate`, `max_depth`, `min_samples_leaf` | rate 0.03–0.2; depth 3–8; leaf 10–100 | Number of rounds: let **early stopping** decide |
| **K-means** | `n_clusters` | 2 to 10 | Covered in Module 6 (elbow, silhouette, judgment) |

### "Log scale" in plain English

For settings like alpha and C, try values that multiply by 10 each step (0.01, 0.1, 1, 10, 100), not values that add a fixed amount (1, 2, 3, 4). The interesting changes happen across orders of magnitude.

### Why it matters for decisions

Focusing on a few settings keeps tuning fast, keeps results understandable, and dramatically lowers the risk of tuning yourself into an overfit model.

---

## Lesson 7.6: Three Ways to Search

### Plain-language explanation

Tuning means trying different combinations of settings, scoring each with cross-validation, and keeping the best. There are three practical ways to do the trying.

### 1. Built-in automatic tuning (easiest)

Some models tune themselves with cross-validation: **RidgeCV**, **LassoCV**, **ElasticNetCV**, **LogisticRegressionCV**. You've already used these. For linear models, this is usually all you need.

### 2. Grid search

Try **every** combination in a small list.

**Example:** depth in [3, 5, 7] × leaf size in [5, 10, 20] = 9 combinations, each cross-validated.

**Analogy:** Taste-testing every combination of three coffees and three milks. Thorough, but the number of combinations explodes as you add options.

**Use when:** Two or three settings with a few values each.

### 3. Randomized search

Try a **fixed number** of random combinations from wider ranges.

**Analogy:** Instead of visiting every house on every street to find a good one, you visit 30 randomly chosen houses across the whole neighborhood. You'll almost always find one close to the best, in a fraction of the time.

**Use when:** Three or more settings, or wide ranges. Typically 20–60 tries is plenty.

### 4. Optuna (optional, for larger data)

A smarter search that learns from earlier tries and focuses on promising areas.

**Analogy:** A house hunter who notices the good homes cluster near the park and starts looking there.

**Use when:** Large data and slow models (like gradient boosting on hundreds of thousands of rows), where each try is expensive.

### Which to use

| Situation | Method |
|---|---|
| Linear and logistic regression | Built-in CV versions |
| 1–2 settings, small ranges | Grid search |
| 3+ settings or wide ranges | Randomized search (20–60 tries) |
| Large data, slow models | Randomized search or Optuna |

---

## Lesson 7.7: The Tuning Trap (Winning on Noise)

### Plain-language explanation

When you try many combinations and pick the best score, some of that "best" is just luck. The more combinations you try, the more likely you are to find one that happened to fit your particular validation folds well. Its score is **optimistically biased**.

### Business analogy

Flip 100 coins ten times each. One coin will probably land heads eight or nine times. It's not a magic coin. You just looked at enough coins that one got lucky. Picking the "best" of 200 tuning combinations works the same way.

### Why small data makes it worse

With small data, each fold is small, scores are noisy, and lucky winners are common. A combination can look 10% better purely by chance.

### The fixes

**1. Keep searches small.** Fewer combinations means less opportunity for luck. On small data, a grid of 6–12 combinations is often plenty.

**2. Prefer the simpler setting when scores are close.** If depth 3 scores 0.62 ± 0.08 and depth 7 scores 0.64 ± 0.10, choose depth 3. scikit-learn can't do this automatically, so the template includes a "simplest within one spread of the best" rule (often called the **one-standard-error rule**).

**3. Use nested cross-validation for your final reported score** (next lesson).

**4. On large data, hold back a final test set** that's never used during tuning, and score it only once at the very end.

---

## Lesson 7.8: Nested Cross-Validation, the Honest Score

### Plain-language explanation

**Nested cross-validation** puts one cross-validation loop inside another:

- **Inner loop:** does the tuning (picks the best settings)
- **Outer loop:** tests the whole "tune-then-train" process on data the inner loop never saw

The outer loop's average score is an honest estimate of how your tuned model will perform on new data.

### Business analogy

A hiring process where one panel picks the finalist, and a **separate** panel, who wasn't involved in the selection, evaluates how that finalist actually performs. The first panel's enthusiasm can't inflate the evaluation.

### When to use it

| Situation | Use nested CV? |
|---|---|
| Small data (under ~1,000 rows) | **Yes**, for your final reported score |
| Large data with a held-out test set | Not needed; the test set does the same job |
| Comparing several model types fairly on small data | **Yes** |
| Quick exploration | No; use regular CV and confirm later |

### What it costs

Nested CV is slower, because it runs the tuning search several times. On small data, that's seconds to minutes, so it's very affordable. On large data, use a held-out test set instead.

### Why it matters for decisions

The number you put in front of leadership should be the nested CV score (small data) or the held-out test score (large data), never the best score from the tuning search.

---

## Lesson 7.9: Start with Defaults, Stop Early

### A practical tuning philosophy

1. **Run with defaults first.** Record the cross-validated score. This is your tuning baseline.
2. **Tune two or three settings** from the Lesson 7.5 table, with a small search.
3. **Check the improvement against the spread.** If the tuned model isn't clearly better than the default, keep the default.
4. **Stop when gains are small.** If a second round of tuning improves things by less than the spread, you're done.

### Business analogy

Adjusting a car's seat and mirrors before a drive: worth two minutes. Rebuilding the engine to save half a mile per gallon on a trip to the grocery store: not worth it.

### Realistic expectations

| Model | Typical gain from tuning over good defaults |
|---|---|
| Ridge, Lasso, logistic regression | Often meaningful, but built-in CV handles it automatically |
| Random forest | Usually small |
| Gradient boosting | Often meaningful, especially on large data |
| KNN | Moderate (choosing K matters) |
| Decision tree | Meaningful (depth and leaf size matter a lot) |

### Why it matters for decisions

Time spent tuning has a cost. Most business value comes from good framing (Module 1), clean features (Module 2), and correct validation, not from the fourth decimal place of a score.

---

## Lesson 7.10: Validating Clustering

Clustering has no "right answer" column to score against, so validation looks different. You've already met the tools in Module 6:

- **Silhouette score** across different K values
- **Stability checks** on random subsets and different time periods
- **Business usability:** can each segment be described and acted on?

The tuning trap applies here too. Trying many methods, K values, and input combinations, then picking the best silhouette, can produce segments that look great once and vanish next quarter. Stability checks are clustering's version of nested CV. Never skip them.

---

## Step-by-Step Process: Validating and Tuning Any Model

1. **Choose your validation strategy** from the Lesson 7.3 table, based on data size, time order, and whether rows repeat per customer or store.
2. **Confirm your main metric** (from Modules 3 and 4).
3. **Run the baseline and the default model** with that validation strategy. Record average and spread.
4. **Pick two or three settings to tune** from the Lesson 7.5 table.
5. **Choose a search method:** built-in CV, grid, or randomized (Lesson 7.6).
6. **Define small, sensible ranges.** Use log scale for alpha and C.
7. **Run the search** with your validation strategy inside it. Always tune the **whole pipeline** (prep plus model), never the model alone.
8. **Read the results table.** Look at the top several combinations, not just the winner. Note the spreads.
9. **Apply the simplicity rule:** choose the simplest setting within one spread of the best score.
10. **Compare tuned vs. default.** If the gain is smaller than the spread, keep the default.
11. **Get the honest score:** nested CV (small data) or a one-time held-out test (large data).
12. **Record everything in the model card:** validation method, settings tried, settings chosen, honest score with range.

---

## Common Pitfalls and How to Avoid Them

### 1. Overfitting the validation process on small data
**What it looks like:** 150 rows, a 300-combination grid, and a "best" model that's 12% better than default, which disappears on new data.
**Why it happens:** With noisy small folds and many tries, some combination will win by luck (Lesson 7.7).
**Fixes:**
- Use repeated K-fold or leave-one-out, never a single split
- Keep searches tiny: 6–12 combinations
- Tune only the one or two settings that matter most
- Apply the simplicity rule
- Report the nested CV score, not the search's best score
- Expect modest gains; be suspicious of large ones

### 2. Tuning on the test set
**What it looks like:** Trying settings, checking the test score, adjusting, checking again.
**Analogy:** Studying the actual exam questions, then claiming your score reflects what you learned.
**Fix:** The test set gets used **once**, at the end. All tuning happens inside cross-validation on training data.

### 3. Prepping outside the pipeline
**What it looks like:** Scaling or filling blanks on all the data, then running the tuning search.
**Fix:** Tune the full pipeline from Module 2, so every prep step is learned only from each training fold.

### 4. Random splits on time-ordered data
**What it looks like:** Predicting next month's sales with a model validated by randomly mixing months, so it trains on June to predict May.
**Fix:** Always use a time-series split for anything time-ordered.

### 5. Ignoring repeated rows per customer or store
**What it looks like:** The same customer appears in both training and test folds, so the model "recognizes" them.
**Fix:** Use group K-fold with the customer or store ID as the group.

### 6. Huge grids "to be thorough"
**What it looks like:** Eight settings, five values each, 390,625 combinations, an overnight run.
**Fix:** Two or three settings; randomized search for anything bigger than a small grid.

### 7. Reporting only the best score
**What it looks like:** "Our tuned model achieves 0.81 AUC," taken straight from the tuning search.
**Fix:** Report the nested CV or held-out test score, with its range.

### 8. Using stratified splits for regression or forgetting them for classification
**What it looks like:** Rare "yes" cases ending up mostly in one fold, so scores swing wildly.
**Fix:** Stratified folds for every classification problem. The template sets this automatically based on task type.

---

## Small vs. Large Data: What Changes in This Module

| Aspect | Small data (30–300 rows) | Large data (thousands+) |
|---|---|---|
| Validation strategy | Repeated K-fold (5×10) or leave-one-out | 5-fold CV, or train/validation/test split |
| Final honest score | **Nested cross-validation** | One-time score on a held-out test set |
| Score spread | Wide; always report it | Narrow; still worth reporting |
| Settings to tune | 1–2 | 2–4 |
| Search size | 6–12 combinations | 20–60 randomized tries, or Optuna |
| Search method | Built-in CV or small grid | Randomized search or Optuna |
| Simplicity rule | **Essential**; default to simpler settings | Still useful; gains are more trustworthy |
| Expected gains from tuning | Small; be suspicious of big jumps | Can be meaningful, especially for boosting |
| Early stopping (boosting) | Usually not using boosting (Module 5) | Always on, with a validation set |
| Time-series split | **Same**: always for time-ordered data | **Same** |
| Group K-fold | **Same**: always for repeated rows per customer, store, or account | **Same** |
| Reporting | Average **and** range, plus a confidence label | Average and range; the range is usually tight |

**Bottom line:** on small data, validation is where most of your effort goes. Use repeated cross-validation, tune very little, report the spread, and get your final honest score from nested CV. On large data, a simple held-out test set is enough for the final score, and tuning (especially for boosting) can pay off, but the same rules about leakage, time order, and repeated rows apply at every size.

---

## Code Structure / Pipeline Outline

Tuning and validation aren't a separate project; they live inside every modeling notebook. Each companion notebook in this repository includes a validation and tuning section that follows this outline:

```text
1. CONFIG CELL
   - task type: "regression" | "classification"
   - main metric (and optional extra metrics to report)
   - data size mode: "small" | "medium" | "large" (auto-suggested)
   - time column (optional -> time-series split)
   - group column (optional -> group K-fold)
   - search type: "none" | "grid" | "random"
   - number of random tries (e.g., 20 small / 60 large)
   - random seed

2. VALIDATION STRATEGY (chosen automatically, printed in plain English)
   - time column set     -> TimeSeriesSplit
   - group column set    -> GroupKFold
   - classification      -> StratifiedKFold / RepeatedStratifiedKFold
   - regression          -> KFold / RepeatedKFold
   - small mode: repeated (e.g., 5 folds x 10 repeats)

3. ONE-SPLIT DEMONSTRATION (optional)
   - score the same model on 10 different random splits
   - show how much the score moves around

4. PIPELINE
   - Module 2 prep + model in ONE Pipeline
   - so every prep step is learned only from each training fold

5. BASELINE AND DEFAULT MODEL
   - dummy baseline score with spread
   - model with default settings, score with spread
   - train vs. validation gap (overfitting check)

6. SEARCH SPACE
   - 1-2 settings (small) or 2-4 settings (large)
   - log-scale ranges for strength / learning-rate settings
   - grid or randomized search over the WHOLE pipeline

7. READING THE RESULTS
   - top 10 settings: mean score, spread, train-validation gap
   - simplicity rule: pick the simplest (or least-overfit)
     setting within one spread of the best
   - "tuning worth it?" check: gain vs. default, compared to spread

8. VALIDATION CURVE (optional)
   - score vs. one setting (e.g., tree depth)
   - shows where the model starts to overfit

9. HONEST FINAL SCORE
   - small mode: nested cross-validation
   - large mode: score the held-out test set ONCE

10. LEARNING CURVE (optional)
    - score vs. amount of training data
    - answers "would more data help?"

11. OUTPUT
    - chosen settings, honest score with range,
      validation method used, and number of combinations tried
```

Where to find it in the repository:

| Notebook | Tuning approach shown |
|---|---|
| [`notebooks/01_linear_regression.ipynb`](../notebooks/01_linear_regression.ipynb) | Built-in CV for Ridge / Lasso strength |
| [`notebooks/02_logistic_regression.ipynb`](../notebooks/02_logistic_regression.ipynb) | Small grid over regularization strength |
| [`notebooks/03_decision_tree.ipynb`](../notebooks/03_decision_tree.ipynb) | Validation curve on depth, grid / randomized search, simplicity rule |
| [`notebooks/04_random_forest_churn.ipynb`](../notebooks/04_random_forest_churn.ipynb) | Randomized search, picking the least-overfit setting within one spread |
| [`notebooks/05_gradient_boosting.ipynb`](../notebooks/05_gradient_boosting.ipynb) | Randomized search with early stopping (large) or fixed rounds (small) |
| [`notebooks/06_end_to_end_churn.ipynb`](../notebooks/06_end_to_end_churn.ipynb) | Grid for logistic, randomized for random forest, champion selection |

---

## Hands-On Exercise

**Time:** about 2 hours

1. **Run the one-split demonstration** on your data. Write down the lowest and highest score you saw across 10 random splits. (10 minutes)
2. **Confirm the validation strategy** the template chose. Does your data have time order or repeated rows per customer? Override the choice if needed and explain why. (10 minutes)
3. **Score the baseline and a default model** with cross-validation. Record the average, the spread, and the train-vs-validation gap. (15 minutes)
4. **Define a small search space**: one or two settings for small data, two to four for large. Use log-scale ranges where appropriate. (15 minutes)
5. **Run the search** (grid for small spaces, randomized for larger ones). Note how many combinations you tried. (15 minutes)
6. **Read the top 10 results.** Apply the simplicity rule. Did the "best" setting win by more than the spread, or is it effectively tied with simpler options? (20 minutes)
7. **Get the honest score**: nested CV on small data, or a single test-set score on large data. Compare it to the best score from the search. How much did the search flatter itself? (20 minutes)
8. **Run the learning curve** (optional). Would more data be likely to help? (5 minutes)
9. **Write a three-sentence validation summary** for a manager: what you tested, the honest score with its range, and whether tuning was worth it. (10 minutes)

---

## Discussion and Reflection Questions

1. **Your tuned model scores 0.78, and the default model scores 0.76, with a spread of ±0.04 across folds.** What would you report to your manager, and which model would you deploy? What would have to be true for the tuned model to be worth the extra complexity?

2. **Think about how your data was collected.** Does it have a time order, repeated customers or stores, or seasonal patterns? Which validation mistake from this module would be easiest to make with your data, and what would it make you believe that isn't true?

3. **A vendor tells you their model achieves 95% accuracy.** Using what you learned in this module, list three questions you would ask before believing that number.

---

## What "Done" Looks Like

You've finished Module 7 when you have:

- [ ] A validation strategy that fits your data's size, time order, and structure, written down in one sentence
- [ ] Baseline and default-model scores reported as an average **and** a range
- [ ] A small, deliberate search space (not a giant grid), with the number of combinations recorded
- [ ] A chosen setting picked with the simplicity rule, not just the top score
- [ ] An honest final score from nested CV (small data) or a one-time test set (large data)
- [ ] A short validation summary a non-technical manager could follow

If tuning barely helped, that's normal and useful to know. Most of a model's value comes from good data, a good baseline, and honest validation. Tuning is the last few percent, and on small data it's often zero.

**Next up:** Module 8, Interpreting Results and Turning Them into Decisions, where you'll explain what your model learned and translate it into actions, ROI estimates, and a one-page memo leadership can act on.
