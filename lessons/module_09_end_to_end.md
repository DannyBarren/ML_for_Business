# Module 9: End-to-End Templates and Case Studies

**Estimated time:** 4 hours total (about 45 minutes of reading, 3 hours 15 minutes hands-on)
**Primary algorithms:** Everything from Modules 3–8, used in complete workflows

---

## Why This Module Matters

Up to now you've learned each skill on its own. Real projects don't come in neat pieces. A business question arrives, the data is messy, the deadline is close, and someone wants a recommendation, not a notebook.

This module puts it all together. You'll:

1. Learn a single **end-to-end workflow** that ties Modules 1–8 together
2. Use **configurable templates** so a new project starts with a few edits instead of a blank page
3. Work through **five case studies** covering small and large data, regression, classification, and segmentation
4. **Run a full project on your own data**

### Business analogy

The earlier modules taught you to use each tool in the workshop. This module is your first full build, start to finish, following a proven blueprint. After it, you can take on new projects without starting over each time.

---

## Lesson 9.1: The End-to-End Workflow

Every project in this course follows the same eight stages. Each one maps to a module you've finished.

| Stage | What you do | Module | Key output |
|---|---|---|---|
| **1. Frame** | Define the decision, target, baseline, and action owner | 1 | Project brief |
| **2. Prep** | Profile, clean, engineer features, check for leakage | 2 | Clean data + saved pipeline |
| **3. Benchmark** | Run baseline and a simple, interpretable model | 3, 4, or 6 | Benchmark scores |
| **4. Challenge** | Try tree models (or other clustering methods) | 5 or 6 | Comparison table |
| **5. Validate and tune** | Choose validation, tune lightly, get the honest score | 7 | Model card |
| **6. Explain** | Global and local explanations, stability checks | 8 | Drivers + confidence labels |
| **7. Decide** | Action rule, expected value, ROI vs. current approach | 8 | Action plan + ROI range |
| **8. Communicate and monitor** | Insight memo, monitoring plan, stakeholder review | 8 | One-page memo |

### The golden rules carried through every stage

- **Start from the decision**, not the data or the algorithm
- **Always beat a baseline**, and compare against what you do today
- **Prevent leakage** with decision-day checks and pipelines
- **Simpler wins ties**
- **Report honest scores with ranges**
- **Treat drivers as hypotheses** until a pilot confirms them
- **Every output has an owner and an action**

### Why it matters for decisions

A consistent workflow means your projects are comparable, reviewable, and repeatable. Leadership learns what to expect from your work, which builds trust faster than any single impressive result.

---

## Lesson 9.2: How the Templates Work

### Plain-language explanation

Each template is a complete notebook that runs the whole workflow for one type of question. At the top is a **configuration cell**. You fill in a handful of settings, run the notebook top to bottom, and then spend your time reading the outputs and making business calls.

### Business analogy

A tax preparation form. The structure, the calculations, and the order of steps are already set. You fill in your numbers, check each section makes sense, and sign. You don't redesign the form every year.

### The templates

| Template | Use for | Built from |
|---|---|---|
| [`01_linear_regression.ipynb`](../notebooks/01_linear_regression.ipynb) | "How much?" forecasts and driver analysis | Modules 1–3, 7, 8 |
| [`02_logistic_regression.ipynb`](../notebooks/02_logistic_regression.ipynb) | "Yes/no" predictions, scoring, and targeting | Modules 1, 2, 4, 7, 8 |
| [`03_decision_tree.ipynb`](../notebooks/03_decision_tree.ipynb) | Simple, readable rules for yes/no or how-much questions | Modules 5, 7, 8 |
| [`04_random_forest_churn.ipynb`](../notebooks/04_random_forest_churn.ipynb) | Churn scoring with a tree ensemble, thresholds, and a call list | Modules 4, 5, 7, 8 |
| [`05_gradient_boosting.ipynb`](../notebooks/05_gradient_boosting.ipynb) | Higher-accuracy models on larger data | Modules 5, 7, 8 |
| [`06_end_to_end_churn.ipynb`](../notebooks/06_end_to_end_churn.ipynb) | The complete workflow, from data to recommendation | All modules |
| Segmentation outline | Customer, product, or location segments | Module 6 |
| Insight memo template | The one-page memo for every project | Module 8, Lesson 8.10 |

### What's in a typical configuration cell

```text
PROJECT SETTINGS
  project_name, decision_owner, decision_statement

DATA SETTINGS
  file_path, id_column, target_column, date_column (optional),
  group_column (optional), scoring_date (optional)

COLUMN SETTINGS
  numeric_columns, categorical_columns,
  ordinal_columns (+ order), columns_to_drop, log_transform_columns

MODELING SETTINGS
  task_type, main_metric, data_size_mode ("auto" | "small" | "large"),
  models_to_try, tuning_level ("none" | "light" | "full")

DECISION SETTINGS
  value_per_success, cost_per_action,
  action_success_rate (low / medium / high), team_capacity,
  current_approach_rule (optional)
```

### Built-in guardrails

Every template includes automatic warnings for the most common mistakes:

- **Data size check:** recommends the small or large path and adjusts defaults
- **Rows-per-feature warning:** flags when you have too many inputs for your data
- **Leakage alarm:** flags suspiciously perfect scores or one dominant input
- **Rare-category warning:** flags categories with too few rows
- **Tie detector:** flags when a complex model doesn't beat a simpler one by more than the score spread
- **Stability warning:** flags drivers or segments that change across folds or subsets
- **Extrapolation warning:** flags new cases outside the range of training data

You can switch any of them off, but you'll need a reason to.

---

## Lesson 9.3: Adapting a Template to Your Data

### The five-edit start

For most projects, you only need to change five things to get a first full run:

1. **File path** to your data
2. **Target column** (or clustering inputs for segmentation)
3. **ID column** to exclude from modeling
4. **Column lists:** which are numbers, categories, and ordered categories
5. **Decision economics:** value per success and cost per action (rough estimates are fine for a first pass)

Everything else has sensible defaults.

### Then iterate

- **First run:** check the profile, warnings, and baseline. Fix data issues.
- **Second run:** refine features, drop leaky or useless columns, adjust the column lists.
- **Third run:** review model comparisons, explanations, and ROI; draft the memo.

### Business analogy

Moving into a furnished apartment. On day one you unpack the essentials and start living there. Over the next few weeks you rearrange things to suit how you actually live. You don't rebuild the walls.

### When to go beyond the template

- Your data has an unusual structure (for example, many rows per customer over time that need summarizing first)
- You need a feature the template can't build automatically (a company-specific ratio or business rule)
- Your stakeholders require a specific output format (a CRM upload file, a particular report layout)

Each template has a clearly marked **custom features** cell and a **custom outputs** cell for these cases, so your changes stay organized.

---

## Lesson 9.4: The Five Case Studies

Each case study comes with a sample dataset, a completed notebook, and a finished insight memo. Work through them in order: each one stretches a different skill.

### Case Study 1: Small Business Pricing Drivers

| | |
|---|---|
| **Business** | A home-services company quoting jobs (roofing, repairs, installations) |
| **Question** | Which factors drive the final job price, and are we quoting consistently? |
| **Data** | About 80 completed jobs |
| **Task type** | Regression (small data) |
| **Main tools** | Ridge regression, confidence intervals, bootstrap stability, shallow tree for comparison |

**What it teaches:**
- Working with very small data honestly
- Choosing a small number of business-driven inputs (job size, material type, rush flag, crew size)
- Why Ridge beats more complex models here
- Presenting drivers with wide but honest ranges

**Typical finding style:** "Rush jobs are priced about $X–$Y higher, all else equal. Material type explains more of the price gap than crew size. Quotes for similar jobs vary more than expected, which suggests a pricing guide could improve consistency." *(Actual figures come from the case study data.)*

**Key decision:** Create a simple pricing guide based on the top drivers, and review quotes that fall far from the model's estimate.

---

### Case Study 2: Customer Churn Prediction

| | |
|---|---|
| **Business** | A subscription service with monthly and annual plans |
| **Question** | Which customers are likely to cancel in the next 90 days, and which retention offers are worth the cost? |
| **Data** | About 7,000 customers |
| **Task type** | Classification (large data) |
| **Main tools** | Logistic regression benchmark, gradient boosting, threshold tuning, lift charts, SHAP, expected value |

**What it teaches:**
- Defining a clean target with a scoring date to prevent leakage
- Handling imbalanced outcomes
- When gradient boosting earns its place over logistic regression
- Turning scores into tiers with expected value and ROI

**Typical finding style:** "Targeting the top 20% by model score reaches roughly X% of likely churners. The profit-maximizing threshold is well below 50%. Recent support issues and plan type are the strongest signals."

**Key decision:** A tiered retention program with a three-month pilot to measure the actual success rate of retention offers.

---

### Case Study 3: B2B Lead Scoring

| | |
|---|---|
| **Business** | A B2B software company with a small sales team |
| **Question** | Which inbound leads should sales prioritize each week? |
| **Data** | About 250 past leads, around 40 of which converted |
| **Task type** | Classification (small data, rare outcome) |
| **Main tools** | Regularized logistic regression, repeated stratified cross-validation, nested CV, capacity-based threshold, odds ratios |

**What it teaches:**
- Why the number of **conversions** (about 40), not the number of leads, sets your limits
- Keeping inputs to a handful when the rare group is small
- Capacity-based thresholds ("our team can work 30 leads a week")
- Communicating directional findings with medium confidence

**Typical finding style:** "Leads from companies in our core size band, who requested a demo, are several times more likely to convert. The model ranks leads better than our current first-come-first-served approach, but with only about 40 conversions, results are directional."

**Key decision:** Prioritize the top-scored leads each week and track conversion by score tier for a quarter before automating anything.

---

### Case Study 4: Retail Customer Segmentation

| | |
|---|---|
| **Business** | A multi-location retailer with a loyalty program |
| **Question** | What distinct customer groups do we have, and how should marketing treat each one? |
| **Data** | About 50,000 transactions, summarized to customer level |
| **Task type** | Segmentation (large data) |
| **Main tools** | RFM features, rule-based RFM baseline, K-means, silhouette and elbow, stability checks, profile tables, segment rules via shallow tree |

**What it teaches:**
- Summarizing transaction data into customer-level features
- Comparing clustering to simple RFM rules
- Choosing a practical number of segments
- Profiling with outcomes not used in clustering (return rate, margin)
- Producing plain-English segment rules for the CRM

**Typical finding style:** "Four segments hold up across stability checks. One small group of lapsed high spenders has outsized win-back value. Segment rules are simple enough to apply in the loyalty system directly."

**Key decision:** A segment-specific marketing calendar, with a win-back campaign as the first test.

---

### Case Study 5: Store or Branch Performance

| | |
|---|---|
| **Business** | A regional chain with about 40 locations |
| **Question** | What separates our top-performing locations from the rest, and where should we invest? |
| **Data** | About 40 locations, one row each |
| **Task type** | Regression and shallow trees (very small data) |
| **Main tools** | Pivot-table-first analysis, Ridge with very few inputs, leave-one-out validation, bootstrap intervals, shallow tree rules, heavy reliance on business judgment |

**What it teaches:**
- The limits of ML on very small data
- Why the Module 1 "pivot table first" rule often wins here
- Using models to **structure a conversation** rather than make the decision
- Being comfortable recommending "not ML" or "more data first"

**Typical finding style:** "With 40 locations, the model can support about two or three drivers. Local foot traffic and manager tenure show the most consistent association with performance, but intervals are wide. These are hypotheses to test with targeted investments at a few locations."

**Key decision:** Pilot a manager-retention or training program at a handful of locations and compare results, instead of rolling out chain-wide based on the model.

---

### Case study comparison

| Case | Rows | Task | Data size path | Likely winning model | Confidence level |
|---|---|---|---|---|---|
| 1. Pricing drivers | ~80 | Regression | Small | Ridge | Medium |
| 2. Churn | ~7,000 | Classification | Large | Gradient boosting (vs. logistic) | High (after pilot) |
| 3. Lead scoring | ~250 (~40 conversions) | Classification | Small (rare outcome) | Regularized logistic | Medium |
| 4. Segmentation | ~50,000 transactions | Clustering | Large | K-means (vs. RFM rules) | High |
| 5. Branch performance | ~40 | Regression | Very small | Ridge / pivot tables | Low to medium |

Notice the pattern: **the smaller the data, the simpler the winning model and the more the recommendation leans on pilots and business judgment.** That's not a weakness. It's the honest, professional answer.

---

## Lesson 9.5: Choosing Which Template to Start With

When a new question lands on your desk, use this quick guide.

```text
What does the decision need?
├── A number (sales, cost, time, price)?          → 01_linear_regression
├── A yes/no or a ranked list (churn, convert)?   → 02_logistic_regression or
│                                                   06_end_to_end_churn
├── Groups (segments, types, clusters)?           → segmentation outline (Module 6)
└── "If we change X, what happens to Y?"          → Design a pilot or A/B test
                                                    (use a model only to choose
                                                     what to test)

Then check data size:
├── Under ~30 rows (or under ~20 rare-group cases) → Descriptive analysis first;
│                                                    ML likely not worth it yet
├── ~30–300 rows                                  → Small-data path
└── Thousands+                                    → Large-data path
```

---

## Lesson 9.6: Presenting a Finished Project

### The 10-minute stakeholder review

When you present a finished project, follow this order:

1. **The decision** (30 seconds): what question this answers and who acts on it
2. **The recommendation** (1 minute): the answer, in one or two sentences
3. **The evidence** (3 minutes): top drivers or segments, one chart, confidence labels
4. **The impact** (2 minutes): ROI range compared to the current approach, with assumptions stated
5. **The limits** (1 minute): what the model can't tell you
6. **The next step** (1 minute): usually a pilot, with an owner and timeline
7. **Questions** (remaining time)

### Business analogy

A good doctor's appointment. Diagnosis first, then the evidence, then options and trade-offs, then a clear next step. Nobody wants to hear about lab procedures before learning what's wrong.

### What to have ready but not present

- The full model card
- The comparison table
- Validation and stability results
- The data prep log

When someone asks "how do you know?", you'll have the answer one click away. That's what makes you credible.

---

## Lesson 9.7: Making It Repeatable

### Plain-language explanation

A one-time analysis helps once. A repeatable process keeps helping. The final step is setting up the project so it can be re-run next month or next quarter with minimal effort.

### What to save for every project

| Item | Why |
|---|---|
| Configuration cell (as a separate file) | Re-run with new data by changing one path |
| Saved pipeline + model | Score new data consistently |
| Data prep log | Anyone can understand what was done and why |
| Model card | Records validation method, honest score, settings |
| Monitoring snapshot | Baseline for spotting drift (Module 8) |
| Insight memo | The decision record |

### A simple refresh routine

1. Export fresh data in the same format
2. Update the file path in the config
3. Run the **scoring** section to produce new predictions or segment labels
4. Run the **monitoring** section to check for drift
5. If drift or performance triggers are hit, re-run the full workflow
6. Send the updated ranked list or segment file to the action owner

### Why it matters for decisions

When refreshing a model takes 30 minutes instead of three weeks, the business can act on current information. That's the difference between an analytics project and an analytics capability.

---

## Step-by-Step Process: Running Your Own End-to-End Project

1. **Pick a real decision** your business makes repeatedly. Write the Module 1 project brief.
2. **Choose your template** using the Lesson 9.5 guide.
3. **Make the five-edit start** (Lesson 9.3) and do a first full run.
4. **Read every warning.** Fix data issues, leakage, and rare categories. Re-run.
5. **Review the benchmark and comparison table.** Apply the "simpler wins ties" rule.
6. **Check the validation strategy** the template chose. Confirm it fits your data's size, time order, and structure.
7. **Tune lightly** if a model looks promising. Record the honest score.
8. **Run explanations and stability checks.** Sort drivers into actionable, uncertain, and descriptive.
9. **Design the action rule and calculate ROI** against your current approach.
10. **Write the insight memo**, using the case study memos as examples.
11. **Review with one real stakeholder.** Revise until they can explain it back to you.
12. **Save everything** for a repeatable refresh (Lesson 9.7).
13. **Schedule your first refresh or pilot check-in** on the calendar.

---

## Common Pitfalls and How to Avoid Them

### 1. Running the template on autopilot
**What it looks like:** Filling in the config, running every cell, and copying the outputs into a memo without reading the warnings.
**Fix:** The template does the mechanics. **You** do the judgment. Read every warning and every profile before moving on.

### 2. Overfitting across the whole project on small data
**What it looks like:** On a 90-row dataset, you try six models, tune each one, test several feature sets, and pick the best combination. Every individual step seemed reasonable, but together you've effectively tried hundreds of options and picked the luckiest.
**Analogy:** Asking 20 friends for stock tips and only telling people about the one who happened to be right.
**Fixes:**
- On small data, **decide your plan before you start**: which two or three models, which features, which validation method
- Keep a **project log** of everything you tried, not just what worked
- Use nested CV for the final honest score
- Default to the template's small-data path, which limits the number of models and tuning options automatically
- Label findings medium or low confidence unless confirmed by a pilot or strong business knowledge

### 3. Copying a case study's conclusions instead of its process
**What it looks like:** "The churn case study used gradient boosting, so I will too," on a 150-row dataset.
**Fix:** Copy the **workflow**, not the winning model. Let your data size and comparison table decide.

### 4. Skipping the framing stage because "we already know the question"
**What it looks like:** Jumping into the template with a vague goal like "understand our customers."
**Fix:** Write the project brief first. If you can't name the decision and the action owner, you're not ready to run the template.

### 5. Customizing too much, too early
**What it looks like:** Rewriting half the template before the first full run, before you've seen what the defaults produce.
**Fix:** Make the five-edit start, run it end to end, and read the results. Only then change what genuinely doesn't fit, and put your changes in the custom features and custom outputs cells so they stay organized and easy to review.

### 6. Leaving out the economics
**What it looks like:** A model with a strong ROC-AUC, a nice importance chart, and no estimate of what acting on it is worth.
**Fix:** Fill in the decision settings (value per success, cost per action, success rate, capacity) even if they're rough. Leadership decides based on money and effort, not model scores.

### 7. Treating the project as one-and-done
**What it looks like:** A great analysis delivered once, then a stale call list still being used nine months later.
**Fix:** Save the configuration, pipeline, model card, and monitoring snapshot (Lesson 9.7), and put the first refresh date on the calendar before you present.

### 8. Presenting the model instead of the decision
**What it looks like:** A stakeholder review that spends 20 minutes on algorithms and two minutes on what to do.
**Fix:** Lead with the recommendation, the expected value, and the confidence level. Keep the technical detail in the appendix for anyone who asks (Lesson 9.6).

---

## Small vs. Large Data: What Changes in This Module

| Aspect | Small data (30–300 rows) | Large data (thousands+) |
|---|---|---|
| Template path | Small-data path: repeated CV, small grids, stronger regularization | Large-data path: 5-fold CV or held-out test set, wider searches |
| Models to try | Two or three, decided in advance (usually linear + one tree model) | Linear benchmark plus random forest and gradient boosting |
| Tuning level | "none" or "light" | "light" or "full" |
| Final honest score | Nested CV | One-time held-out test set |
| Explanations | Coefficients with intervals; tree rules as a cross-check | Permutation importance, PDPs, SHAP |
| Case studies that fit | Pricing drivers (Case Study 1), small-store comparisons | Churn (Case Study 2), lead scoring, large segmentations |
| Confidence labels | Mostly medium or low; confirm with pilots | High is achievable for stable findings |
| Use of output | Prioritization and hypotheses, with a human making the call | Ranked lists and tiers that feed day-to-day operations |
| Refresh rhythm | Quarterly, or when enough new rows arrive | Monthly, with automated monitoring |
| Biggest risk | Overfitting across the whole project (Pitfall 2) | Leakage, drift, and acting on tiny "significant" gains |

**Bottom line:** the end-to-end workflow is identical at every size. The template's data size check sets sensible defaults for you, but your judgment decides how much to trust the result and how boldly to act on it.

---

## Code Structure / Pipeline Outline

The capstone notebook, [`notebooks/06_end_to_end_churn.ipynb`](../notebooks/06_end_to_end_churn.ipynb), runs the full workflow on a customer churn case study, comparing logistic regression with a random forest. It works with built-in demo data when no file path is set, so you can run it before touching your own data.

```text
1. CONFIG CELL
   - CSV path (None = use built-in demo data)
   - ID column, target column, value meaning "churned"
   - numeric and categorical column lists, columns to drop
   - main metric, data size mode ("auto" | "small" | "large")
   - business values: value if a customer is saved, cost per
     retention action, action success rate, team capacity

2. DEMO DATA (only if no CSV path is given)

3. CONFIG CHECK
   - confirms every listed column exists
   - confirms the target has exactly two values

4. LIGHT EDA AND QUALITY CHECKS
   - rows, columns, blanks, duplicates, churn rate
   - churn rate by top categories
   - leakage sniff test: flags any single input that predicts
     churn suspiciously well on its own

5. DATA SIZE CHECK
   - rare-group count and rows-per-input ratio
   - small / large path recommendation

6. SPLIT AND VALIDATION SETUP
   - stratified train / test split (test set locked away)
   - stratified (repeated, in small mode) cross-validation

7. PIPELINES
   - logistic: impute + scale numbers, one-hot encode categories
   - random forest: impute numbers, ordinal-encode categories

8. BASELINE AND DEFAULT MODELS
   - dummy baseline, default logistic, default random forest
   - CV scores with spread and train-vs-validation gap

9. TUNING
   - grid search for logistic regularization strength
   - randomized search for random forest settings
   - simplicity rule: simplest setting within one spread of the best
   - champion model chosen ("simpler wins ties")

10. TEST-SET EVALUATION (once)
    - precision, recall, F1, ROC-AUC, PR-AUC
    - confusion matrix

11. BUSINESS METRICS
    - threshold chosen on out-of-fold predictions, never the test set
    - expected-value table by threshold, with team capacity
    - sensitivity check on the action success rate
    - lift by decile

12. INTERPRETATION
    - odds multipliers (logistic) or permutation importance and
      partial dependence (random forest)
    - plain-language insight sentences

13. WHAT TO DO NEXT
    - driver-to-action mapping
    - risk tiers and a call list for the retention team
    - A/B test plan for the retention offer
    - monitoring and retraining triggers
    - executive summary template

14. SAVE AND CHECKLIST
    - saved model and call list
    - small vs. large data notes and a final review checklist
```

The other notebooks in the repository are the building blocks for the other case studies:

| Notebook | Best starting point for |
|---|---|
| [`notebooks/01_linear_regression.ipynb`](../notebooks/01_linear_regression.ipynb) | Case Study 1 (pricing drivers) and Case Study 5 (store or branch performance) |
| [`notebooks/02_logistic_regression.ipynb`](../notebooks/02_logistic_regression.ipynb) | Case Study 3 (B2B lead scoring) on small or medium data |
| [`notebooks/03_decision_tree.ipynb`](../notebooks/03_decision_tree.ipynb) | Simple rules any case study can hand to a frontline team |
| [`notebooks/04_random_forest_churn.ipynb`](../notebooks/04_random_forest_churn.ipynb) | Case Study 2 (churn) when you want to focus on the tree model |
| [`notebooks/05_gradient_boosting.ipynb`](../notebooks/05_gradient_boosting.ipynb) | Case Studies 2, 3, and 5 on large data |
| [`notebooks/06_end_to_end_churn.ipynb`](../notebooks/06_end_to_end_churn.ipynb) | The complete workflow, from loading data to a recommendation |

Case Study 4 (retail segmentation) follows the Module 6 outline.

---

## Hands-On Exercise (Capstone)

**Time:** about 3 hours 15 minutes

1. **Run the end-to-end notebook on the demo data** without changing anything. Read every output and warning, and note anything you don't understand. (25 minutes)
2. **Write your project brief** for one real, repeated decision in your business (Module 1). (20 minutes)
3. **Choose your template** with the Lesson 9.5 guide and make the five-edit start. Run it end to end on your own data. (30 minutes)
4. **Fix what the warnings flag** (blanks, rare categories, possible leakage) and re-run. (20 minutes)
5. **Review the comparison table.** Apply "simpler wins ties" and record the honest score with its range. (15 minutes)
6. **Interpret the drivers** and sort each into actionable, uncertain, or descriptive. (20 minutes)
7. **Design the action rule** and calculate ROI under low, medium, and high assumptions. (20 minutes)
8. **Write the one-page insight memo.** (25 minutes)
9. **Do a 10-minute review** with a colleague, using the Lesson 9.6 format. Revise based on their questions. (15 minutes)
10. **Save everything for refresh** and put the first refresh or pilot check-in on the calendar. (5 minutes)

No suitable data of your own yet? Run steps 3–10 on one of the five case studies and write the memo as if you were presenting it to that business.

---

## Discussion and Reflection Questions

1. **Look back at your capstone project.** Which stage took the most time: framing, data prep, modeling, or turning results into a decision? What does that tell you about where to invest effort on your next project?

2. **Your stakeholder asks, "So should we do it?"** Using your memo, answer in two sentences. What's the recommendation, and how confident are you? If you can't answer in two sentences, what's missing?

3. **Name one decision in your business that is made repeatedly on gut feel today.** Which template would you start with, what data would you need, and what would a small, low-risk first pilot look like?

---

## What "Done" Looks Like

You've finished Module 9, and the course, when you have:

- [ ] Run the end-to-end notebook on demo data and understood every section
- [ ] A project brief for a real (or case-study) decision
- [ ] A full template run on that data, with every warning reviewed
- [ ] A comparison table with an honest score, range, and "simpler wins ties" call
- [ ] Drivers interpreted, sorted, and labeled with confidence levels
- [ ] An action rule with owners, plus ROI under three scenarios
- [ ] A one-page insight memo reviewed by a real stakeholder
- [ ] Everything saved for a repeatable refresh, with the next date on the calendar

---

## Where to Go from Here

You now have a complete, repeatable workflow for the three most common business questions: how much, which ones, and what groups. A few suggestions for what comes next:

- **Run one real project end to end within the next month**, while the workflow is fresh. A small project that changes one decision is worth more than an ambitious one that never ships.
- **Start a project log** and keep it for every analysis. Over time it becomes your organization's memory of what was tried, what worked, and what didn't.
- **Build the habit of piloting.** Models tell you where to look; well-designed tests tell you what works.
- **Revisit the modules as reference.** Module 2 when the data is messy, Module 7 when a score seems too good, Module 8 when it's time to present.

The goal of this course was never to turn you into a data scientist. It was to help you make better decisions with the data you already have, know when a model is worth trusting, and say so with confidence. You're ready.
