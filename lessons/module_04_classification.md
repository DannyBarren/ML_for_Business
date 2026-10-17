# Module 4: Classification for Yes/No Decisions

**Estimated time:** 3 hours total (about 1 hour of reading, 2 hours hands-on)
**Primary algorithms:** Logistic regression, k-nearest neighbors (KNN), Naive Bayes, single decision tree

---

## Why This Module Matters

Many of the most valuable business questions have a yes/no answer:

- Will this customer cancel in the next 90 days?
- Will this lead convert?
- Will this invoice be paid late?
- Is this transaction suspicious?

Classification models don't just answer yes or no. They give each case a **probability**, and that lets you rank customers, leads, or orders from most to least likely. Ranking is where the business value lives: it tells you where to spend limited time, money, and attention.

This module also teaches the most underrated skill in applied ML: **turning a probability into a decision based on what mistakes actually cost.**

---

## Lesson 4.1: From Probabilities to Decisions

### Plain-language explanation

A classification model looks at each case and says, "I think there's a 72% chance this customer churns." That's a **probability**. To act, you pick a **threshold**: "We'll call every customer above 40%."

The model gives you the probability. **You** choose the threshold. That's a business decision, not a technical one.

### Business analogy

A weather app says there's a 30% chance of rain. Whether you bring an umbrella depends on you. Going to a wedding in a suit? Maybe bring it at 30%. Going for a jog? Maybe not until 70%. Same forecast, different decisions, because the cost of being wrong is different.

### Why it matters for decisions

Most software defaults to a 50% threshold. That's almost never the right choice for a business. Moving the threshold can change how many problems you catch, how much effort you spend, and how much money you make, often more than switching algorithms does.

---

## Lesson 4.2: The Confusion Matrix, Translated into Dollars

### Plain-language explanation

When a model makes yes/no calls, there are four possible outcomes:

| | **Actually yes** (churned) | **Actually no** (stayed) |
|---|---|---|
| **Model said yes** | ✅ **True positive:** caught it | ⚠️ **False alarm:** wasted effort |
| **Model said no** | ❌ **Missed case:** lost opportunity | ✅ **True negative:** correctly left alone |

This table is called a **confusion matrix**. It's the most important output in this module.

### Put a price on each box

For a churn example:

| Outcome | Business cost or value |
|---|---|
| Caught it (true positive) | Retention offer costs $50; saves a customer worth $1,200 per year (if the offer works) |
| False alarm | Spent $50 on someone who'd have stayed anyway |
| Missed case | Lost a $1,200-per-year customer |
| Correctly left alone | $0 |

Once you've priced the boxes, you can calculate the dollar value of any model and any threshold. That's how you compare options in a language leadership understands.

### Why it matters for decisions

Two models with the same "accuracy" can have very different business value depending on which mistakes they make. Pricing the confusion matrix makes those trade-offs visible.

---

## Lesson 4.3: Choosing the Right Metric

### Why accuracy misleads

If 5% of customers churn, a model that always says "nobody will churn" is 95% accurate and completely useless. This is the most common trap in classification.

### The metrics that matter

| Metric | Plain-English question it answers | Use when |
|---|---|---|
| **Precision** | "Of the cases we flagged, how many were real?" | False alarms are expensive (e.g., costly field visits, accusing a customer of fraud) |
| **Recall** | "Of all the real cases, how many did we catch?" | Missing a case is expensive (e.g., safety issues, high-value churn) |
| **F1 score** | A balance of precision and recall | You care about both and want one number |
| **ROC-AUC** | "How well does the model rank yes-cases above no-cases, at any threshold?" | Comparing models before you've chosen a threshold |
| **PR-AUC** | Like ROC-AUC, but more honest when the "yes" group is rare | Rare outcomes: fraud, churn under 10%, defaults |

### Business analogy for precision vs. recall

A store security guard:
- **High precision, low recall:** only stops people they're certain about. Few false accusations, but many shoplifters walk out.
- **High recall, low precision:** stops lots of people. Catches most shoplifters, but annoys many honest customers.

There's always a trade-off. The threshold is how you choose where to sit on it.

### Why it matters for decisions

Choosing the metric is choosing what "good" means for your business. Pick it **before** you look at results, so you're not tempted to report whichever number looks best.

---

## Lesson 4.4: Logistic Regression, Your Default Classifier

### Plain-language explanation

Logistic regression is the yes/no cousin of linear regression from Module 3. Instead of predicting a number, it predicts the probability of "yes." Each input gets a coefficient that pushes the probability up or down.

### Reading results: odds ratios

Coefficients in logistic regression are easiest to explain as **odds ratios**:

- **Odds ratio of 2.0:** each one-unit increase in this input doubles the odds of "yes"
- **Odds ratio of 0.5:** each one-unit increase halves the odds
- **Odds ratio of 1.0:** no effect

**Example:** "Each additional support ticket in the last 60 days is associated with 1.4x higher odds of churning." For non-technical audiences, the template also shows the change in **probability** for a typical customer, which many people find more intuitive: "For a typical customer, going from 1 to 3 support tickets raises churn probability from about 8% to about 15%."

### Why it's the default

- Highly interpretable
- Works well on small data, especially with regularization (the same Ridge/Lasso idea from Module 3, controlled by a setting called **C**, where smaller C means more cautious)
- Produces reasonably trustworthy probabilities
- Hard to beat on many business problems

The template uses a cross-validated version (LogisticRegressionCV) that picks C automatically.

---

## Lesson 4.5: Other Classifiers Worth Knowing

You'll compare logistic regression against three simpler alternatives. Each has a role.

### K-nearest neighbors (KNN)

**What it does:** Looks at the K most similar past cases and goes with the majority.
**Analogy:** "Customers most like this one mostly churned, so this one probably will too."
**Strengths:** Intuitive; can capture odd patterns.
**Weaknesses:** No coefficients to explain; sensitive to scaling (your pipeline handles that); slows down on very large data; struggles with many inputs.
**Use for:** Quick comparison, and "show me similar cases" explanations for sales or support teams.

### Naive Bayes

**What it does:** Combines the separate evidence from each input as if they were independent clues.
**Analogy:** A detective adding up clues one at a time without considering how they relate.
**Strengths:** Very fast; works surprisingly well with tiny data and text (like email subject lines or support ticket words).
**Weaknesses:** Probabilities are often over-confident; the independence assumption is rarely true.
**Use for:** Very small datasets, text-heavy data, and quick benchmarks.

### Single decision tree

**What it does:** Builds a flowchart of yes/no questions.
**Analogy:** A call center script: "Has the customer been with us under a year? If yes, did they contact support more than twice?..."
**Strengths:** Produces readable rules frontline teams can use without any software.
**Weaknesses:** Overfits easily unless kept shallow.
**Use for:** Turning patterns into simple operating rules. Keep it to a depth of 2–4. Module 5 builds on this with forests and boosting.

### Quick comparison

| Model | Interpretability | Small data | Large data | Trustworthy probabilities |
|---|---|---|---|---|
| Logistic regression | High (odds ratios) | Excellent | Good | Good |
| KNN | Medium (similar cases) | OK | Slow | Fair |
| Naive Bayes | Medium | Good | Fast | Poor |
| Shallow decision tree | High (rules) | OK if kept shallow | OK | Fair |

---

## Lesson 4.6: Imbalanced Data (When "Yes" Is Rare)

### Plain-language explanation

Most valuable yes/no questions are lopsided. Only 3% of transactions are fraud. Only 8% of customers churn. Only 15% of leads convert. This is called **imbalanced data**.

### Why it's a problem

The model can score well by mostly ignoring the rare group, which is exactly the group you care about.

### Practical fixes

1. **Use the right metrics:** recall, precision, PR-AUC. Not accuracy.
2. **Stratified splits:** make sure every training and testing chunk has the same share of "yes" cases. The template does this automatically.
3. **Class weights:** tell the model that mistakes on the rare group count more. In scikit-learn, this is `class_weight="balanced"`, a single setting.
4. **Adjust the threshold:** often the most effective fix. Lowering the threshold catches more of the rare group.

### What to avoid (for now)

You may hear about oversampling methods like SMOTE, which create synthetic examples of the rare group. They can help in some cases but often add complexity without improving real-world results, and they're easy to misuse in ways that leak information. This course sticks with class weights plus threshold tuning, which are simpler and usually just as effective.

### Count your rare cases

From Module 1: if you have 2,000 customers but only 25 churned, you effectively have a **very small** dataset. The number of rare-group cases matters more than the total row count.

---

## Lesson 4.7: Calibration (Can You Trust "70% Likely"?)

### Plain-language explanation

A model is **well calibrated** if, among all the cases it says are 70% likely, about 70% actually turn out "yes."

### Business analogy

A weather forecaster who says "70% chance of rain" should see rain on about 7 of every 10 such days. If it rains on only 3 of those days, their numbers can't be trusted, even if they're good at telling rainy days from dry ones.

### Why it matters for decisions

- **For ranking** (who to call first), calibration matters less. You only need the order to be right.
- **For dollar calculations** (expected value, budgets, forecasts), calibration matters a lot. If the model says a lead is 40% likely to close and you multiply by deal size to forecast pipeline, that 40% needs to be real.

### How to check and fix

- The template draws a **calibration chart**: predicted probability vs. actual rate. Points near the diagonal line mean good calibration.
- Logistic regression is usually reasonably calibrated already.
- If calibration is poor, scikit-learn's `CalibratedClassifierCV` can adjust it. This needs a decent amount of data; on small data, prefer logistic regression and skip the extra step.

---

## Lesson 4.8: Lift and Gain Charts for Targeting

### Plain-language explanation

These charts answer the question every manager asks: **"If we only act on the top X% of cases, how many of the real ones do we catch?"**

- **Gain chart:** "Calling the top 20% of customers by churn score reaches 60% of all customers who would churn."
- **Lift:** "That's 3x better than calling a random 20%."

### Business analogy

You have a marketing budget to mail 1,000 of your 10,000 customers. Mailing randomly reaches about 10% of likely buyers. Mailing the model's top 1,000 might reach 35%. That 3.5x lift is the business case for the model.

### Why it matters for decisions

Lift and gain charts are often the single best slide to show stakeholders. They connect directly to capacity: "Our team can handle 200 calls a week. Here's how many at-risk customers that reaches."

---

## Lesson 4.9: Setting the Threshold

Here are three practical ways to choose, from simplest to most rigorous.

### Method 1: Capacity-based
"We can act on 200 cases per month." Pick the threshold that flags about 200. Simple, and often the most realistic.

### Method 2: Metric-based
"We need to catch at least 70% of churners." Pick the threshold that hits 70% recall, then check whether the precision (and the number of false alarms) is acceptable.

### Method 3: Profit-based
Use your priced confusion matrix from Lesson 4.2. The template calculates expected profit at every threshold and shows the one that maximizes it.

**Example output:**

| Threshold | Flagged | Caught | False alarms | Expected profit |
|---|---|---|---|---|
| 0.20 | 640 | 182 | 458 | $41,300 |
| 0.30 | 410 | 151 | 259 | $52,900 |
| 0.40 | 260 | 118 | 142 | $49,700 |
| 0.50 | 150 | 81 | 69 | $38,800 |

*(Example data for illustration only.)*

In this example, 0.30 maximizes profit, far from the default 0.50.

### Important caveat

Profit calculations depend on assumptions, like how often a retention offer works. State those assumptions clearly and test how much the best threshold changes if they're off. If small changes swing the answer, recommend a pilot.

---

## Step-by-Step Process: Running a Classification Analysis

1. **Start from your Module 1 brief.** Confirm the target is yes/no and define it precisely ("cancels within 90 days of the scoring date").
2. **Load your Module 2 pipeline and cleaned data.**
3. **Count your cases.** Total rows, "yes" cases, "no" cases. Decide small-data or large-data path based on the **smaller group**.
4. **Choose your main metric** (recall, precision, PR-AUC, or ROC-AUC) based on the cost of mistakes. Write it down before modeling.
5. **Price the confusion matrix.** Estimate the cost or value of each of the four outcomes.
6. **Calculate the baseline.** "Always predict the most common outcome," plus your team's current rule if one exists.
7. **Fit logistic regression** with automatic C selection and class weights. Record cross-validated scores.
8. **Fit the comparison models:** KNN, Naive Bayes, and a shallow decision tree.
9. **Compare all models** on your chosen metric. When scores are close, prefer logistic regression for interpretability.
10. **Check calibration** if you'll use probabilities in dollar calculations.
11. **Interpret the model:** odds ratios and probability changes for logistic regression; rules for the tree.
12. **Build lift and gain charts.**
13. **Choose a threshold** using the capacity, metric, or profit method, and test how sensitive it is to your assumptions.
14. **Write the decision summary:** who to act on, how many, expected results, key drivers, and caveats.

---

## Common Pitfalls and How to Avoid Them

### 1. Overfitting on small data
**What it looks like:** 150 rows with 20 "yes" cases, 12 inputs, and a model with near-perfect scores that falls apart on new data.
**Analogy:** A new manager who's seen 20 bad hires and builds an elaborate theory around them. With so few examples, the "pattern" is mostly coincidence.
**Fixes:**
- Base your rows-per-feature rule on the **rare group**: roughly 10–20 "yes" cases per input. With 20 churners, that's one or two inputs, maybe three with strong regularization.
- Default to regularized logistic regression with automatic C selection
- Keep decision trees to depth 2–3
- Use repeated stratified cross-validation and look at how much scores vary between rounds
- Treat results as a prioritization aid, not an automated decision-maker

### 2. Celebrating accuracy on imbalanced data
**What it looks like:** "Our fraud model is 97% accurate!" when 97% of transactions are legitimate.
**Fix:** Always report against the baseline, and use precision, recall, or PR-AUC.

### 3. Using the default 0.5 threshold
**What it looks like:** Flagging only cases above 50%, which for rare outcomes may catch almost nothing.
**Fix:** Choose the threshold deliberately (Lesson 4.9).

### 4. Leakage from the target definition
**What it looks like:** Predicting "churned this quarter" using activity data from the same quarter, including the weeks after cancellation.
**Fix:** Set a clear scoring date. Inputs come from **before** it; the outcome comes from **after** it. The template has a scoring-date setting to enforce this.

### 5. Trusting uncalibrated probabilities in dollar math
**What it looks like:** Multiplying Naive Bayes probabilities by deal value to forecast revenue.
**Fix:** Check the calibration chart first. Use logistic regression for dollar calculations when possible.

### 6. Fooling yourself with unstable results
**What it looks like:** Choosing a model because its F1 score was 0.62 vs. 0.59, when scores vary by ±0.08 across cross-validation rounds.
**Fix:** Look at the spread of scores, not just the average. If ranges overlap, the models are effectively tied. Pick the simpler one.

### 7. Reading odds ratios as causes
**What it looks like:** "Customers on the annual plan have 0.3x the odds of churning, so let's force everyone onto annual plans."
**Fix:** Loyal customers may choose annual plans. Recommend a test, such as offering an annual-plan discount to a random group.

### 8. Building a score nobody acts on
**What it looks like:** A churn score in a spreadsheet the account team never opens.
**Fix:** Design the output with the people who'll use it: a short, ranked list with the top two or three reasons for each case.

---

## Small vs. Large Data: What Changes in This Module

| Aspect | Small data (under ~50–100 rare-group cases) | Large data (hundreds+ rare-group cases) |
|---|---|---|
| What "small" means | Count the **smaller group**, not total rows | Plenty of examples in both groups |
| Default model | Regularized logistic regression | Logistic regression as benchmark; tree ensembles (Module 5) often win |
| Number of inputs | Very tight: about 10–20 rare cases per input | Can include many more |
| Validation | Repeated stratified k-fold (e.g., 5-fold, repeated 10 times); report score spread | Stratified 5-fold, or train/validation/test split |
| Class weights | Use them | Use them, or rely on threshold tuning |
| Calibration | Check visually, but don't add calibration steps; there isn't enough data | Check and fix with CalibratedClassifierCV if needed |
| Threshold choice | Capacity-based is often most practical; profit estimates will be rough | Profit-based optimization becomes reliable |
| Decision trees | Depth 2–3 maximum | Can go deeper, but forests and boosting usually do better |
| KNN | Fine to try | Can get slow; usually not the best choice |
| How to use results | Prioritization and hypothesis generation; humans make the final call | Can support semi-automated or automated decisions with monitoring |
| Lift charts | Will look jumpy with few cases; read the overall shape | Smooth and reliable |

**Bottom line:** the process is identical. On small data, the number of rare-group cases is your real limit, so lean hard on regularized logistic regression and treat outputs as a smart prioritization tool. On large data, you gain precision and room for more complex models, and threshold tuning becomes a reliable profit lever.

---

## Code Structure / Pipeline Outline

The classification workflow follows this outline:

```text
1. CONFIG CELL
   - path to cleaned data and saved Module 2 pipeline
   - target column and which value means "yes"
   - scoring date (optional, for leakage protection)
   - main metric: "recall" | "precision" | "f1" | "roc_auc" | "pr_auc"
   - confusion matrix costs/values (4 numbers)
   - team capacity (optional, e.g., 200 cases per month)
   - data size mode: "small" | "large" (auto-suggested from rare-group count)

2. LOAD DATA AND PIPELINE
   - load cleaned data
   - split inputs (X) and target (y)

3. CLASS BALANCE CHECK
   - count and % of yes / no
   - rare-group count and rows-per-feature check
   - small/large path recommendation

4. VALIDATION SETUP
   - small mode: repeated stratified k-fold
   - large mode: stratified 5-fold (or train/validation/test)

5. BASELINES
   - "always predict most common" model
   - optional: existing business rule

6. CANDIDATE MODELS
   - logistic regression with class weights (the default)
   - small mode: regularization strength chosen by built-in CV
   - optional: KNN, Naive Bayes, shallow decision tree (depth 2-3)
   - cross-validated scores on the main metric, with spread

7. MODEL COMPARISON TABLE
   - every model vs. the baseline
   - average score and spread (min / max across folds)
   - "effectively tied?" flag when ranges overlap
   - recommendation: simplest model within the spread of the best

8. FINAL MODEL FIT
   - chosen model fit on training data only
   - test set scored ONCE, at the end

9. CALIBRATION CHECK
   - calibration chart (predicted % vs. actual %)
   - large mode: optional CalibratedClassifierCV fix
   - small mode: visual check only, with a warning

10. THRESHOLD SELECTION
    - out-of-fold probabilities (never the test set)
    - capacity-based: threshold that yields N cases per period
    - metric-based: threshold that maximizes the main metric
    - profit-based: expected value at each threshold,
      using the four confusion-matrix values from the config
    - side-by-side table of all three choices

11. CONFUSION MATRIX IN DOLLARS
    - counts in each box at the chosen threshold
    - each box multiplied by its value or cost
    - net value vs. "do nothing" and vs. "act on everyone"

12. LIFT AND GAIN
    - decile table: % of all "yes" cases captured in the top 10%, 20%, ...
    - lift chart and cumulative gain chart

13. INTERPRETATION
    - odds ratios with confidence intervals
    - plain-English sentences: "each extra support ticket multiplies
      the odds of churn by 1.15, holding everything else constant"
    - reference categories listed

14. OUTPUT
    - scored list: ID, probability, risk tier, top 2-3 reasons
    - saved model file (joblib)
    - model card: data size, rare-group count, model, CV score and
      spread, test score, threshold and why it was chosen
```

The companion notebook in this repository, [`notebooks/02_logistic_regression.ipynb`](../notebooks/02_logistic_regression.ipynb), implements this outline. Like every template in the course, you change the file path and a few column names in the CONFIG cell, run top to bottom, and spend your time reading the output. The Random Forest churn notebook ([`notebooks/04_random_forest_churn.ipynb`](../notebooks/04_random_forest_churn.ipynb)) reuses the same threshold, lift, and calibration steps with a tree ensemble, which you'll meet in Module 5.

---

## Hands-On Exercise

**Time:** about 2 hours

1. **Load your Module 2 outputs** (or the provided late-payment or churn demo dataset). Confirm which value of the target means "yes." (10 minutes)
2. **Run the class balance check.** Write down the rare-group count and which path (small or large) the template recommends. Do you agree? (10 minutes)
3. **Fill in the four confusion-matrix values** with your team: what is a correct catch worth, what does a wasted action cost, what does a missed case cost? Rough numbers are fine. (15 minutes)
4. **Run the baseline and model comparison.** Record each model's main-metric score and spread. Which models are effectively tied? (20 minutes)
5. **Check calibration.** Would you trust the probabilities in a revenue or cost calculation? (10 minutes)
6. **Choose a threshold** three ways: capacity-based, metric-based, and profit-based. Pick one and write two sentences explaining why. (20 minutes)
7. **Read the lift table.** How many "yes" cases would your team catch by working only the top 20% of the list? (10 minutes)
8. **Interpret the top five odds ratios** in plain English. Mark each one as "something we can change," "something we can only observe," or "suspicious, check for leakage." (15 minutes)
9. **Draft the scored list** your frontline team would actually receive, including the top reasons for each case. Show it to one person who would use it and note their reaction. (10 minutes)

---

## Discussion and Reflection Questions

1. **Think about the four boxes of your confusion matrix.** Which mistake is more expensive for your business: acting when you didn't need to, or missing a case you should have caught? How did that answer change the threshold you chose?

2. **Your team can only handle a limited number of cases per week.** If the model flags twice that many, how would you decide who gets contacted first? What would you tell the people who aren't contacted?

3. **Pick one odds ratio that looks like a lever** (for example, "customers who use feature X churn less"). What else could explain that pattern? What small, randomized test would tell you whether pushing that lever actually changes the outcome?

---

## What "Done" Looks Like

You've finished Module 4 when you have:

- [ ] A class balance check with the rare-group count and a small- or large-data path chosen
- [ ] A cross-validated comparison of the baseline and at least two classifiers, with score spreads
- [ ] A chosen final model with a one-sentence justification ("simpler wins ties")
- [ ] A calibration chart reviewed, with a note on whether probabilities can be used in dollar math
- [ ] A deliberately chosen threshold, with the method and reasoning written down
- [ ] A confusion matrix translated into dollars at that threshold
- [ ] A lift or gain table showing how much better than random the ranked list is
- [ ] Odds ratios for the top drivers, explained in plain English
- [ ] A draft scored list your team could act on, with reasons attached

If your best model only slightly beats the baseline, that's still useful: a modest lift on a well-targeted list can be worth real money, and you now know exactly how much to expect.

**Next up:** Module 5, Tree-Based Models and Ensembles, where you'll test whether decision trees, random forests, and gradient boosting can beat your logistic regression benchmark, and learn when they're worth the extra complexity.
