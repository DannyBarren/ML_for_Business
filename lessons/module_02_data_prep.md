# Module 2: Data Prep Essentials

**Estimated time:** 3 hours total (about 1 hour of reading, 2 hours hands-on)

---

## Why This Module Matters

Data prep is the unglamorous part of ML. It's also where most of the value gets won or lost. A simple model on clean, well-built data almost always beats a fancy model on messy data.

Think of it like cooking. The recipe (the algorithm) matters, but if the ingredients are spoiled or mislabeled, no recipe saves the dish. This module teaches you to check, clean, and prep your ingredients the same way every time.

The good news: you don't need to be a programmer to do this well. You need a checklist, business judgment, and the templates in this module.

---

## Lesson 2.1: Profiling Your Data in Ten Minutes

### Plain-language explanation

Profiling means taking a quick inventory before you do anything else: how many rows, what kinds of columns, what's missing, what looks odd.

### Business analogy

It's a warehouse walk-through before an audit. You're not counting every box yet. You're checking that the aisles are labeled, nothing's obviously broken, and the numbers on the clipboard roughly match what's on the shelves.

### What to check

| Check | What you're looking for | Why it matters |
|---|---|---|
| Row and column count | Does it match what you expected from the source system? | Catches broken exports and accidental filters |
| Column types | Are numbers stored as numbers and dates as dates? | "$1,200" stored as text can't be averaged |
| Missing values | Which columns have blanks, and how many? | Determines what you can use and how to fill gaps |
| Duplicates | Repeated rows or repeated IDs | Double-counted customers distort every result |
| Value ranges | Minimums, maximums, and averages | Spots negative ages, $0 orders, or dates in 2099 |
| Category counts | How many distinct values each text column has | Finds typos ("Ohio", "OH", "ohio") and rare categories |
| Target column | Distribution of the thing you're predicting | Confirms the target looks the way you expect |

### Why it matters for decisions

Ten minutes of profiling prevents the worst outcome in analytics: presenting a confident recommendation to leadership that turns out to be built on a broken export.

---

## Lesson 2.2: Handling Missing Data

### Plain-language explanation

Missing values are blanks in your data. Models can't work with blanks, so you have to decide what to do with them. You have three options: drop, fill, or flag.

### The three options

**Drop** the row or column
- Drop a **column** if most of it is missing (as a rough rule, more than 40–50%) and it isn't critical
- Drop a **row** only if very few rows are affected and they aren't different from the rest
- On small data, be very cautious about dropping rows. Every row is precious.

**Fill** the blank with a reasonable value
- Numbers: usually the median (the middle value), which is less thrown off by extremes than the average
- Categories: the most common value, or a new category called "Unknown"
- Business-logic fills: sometimes a blank genuinely means zero (no discount applied, no support tickets filed). Check with whoever owns the data.

**Flag** that it was missing
- Add a yes/no column like `income_was_missing`
- This lets the model learn if "missing" itself means something

### Business analogy

Imagine a job application where someone left "previous salary" blank. You could ignore the application (drop), assume a typical salary (fill), or note "declined to share" (flag). The last one is often the most informative, because people who leave it blank might be different in a meaningful way.

### Why it matters for decisions

Missing data is rarely random. Customers who don't fill in their phone number might be less engaged. Deals with no "close date" might be stalled. Flagging lets you capture that signal instead of hiding it.

---

## Lesson 2.3: Outliers and Odd Values

### Plain-language explanation

Outliers are values far from the rest: a $500,000 order in a business where most are $500, or a customer with 4,000 logins in a month.

### Ask three questions about each outlier

1. **Is it an error?** A typo, a test record, or a system glitch. Fix it or remove it.
2. **Is it real but rare?** Your one enterprise client. Keep it, but consider whether it belongs in the same analysis as everyone else.
3. **Is it the thing you care about?** In fraud detection, the outliers *are* the point. Don't remove them.

### Common fixes for real-but-extreme values

- **Capping:** set anything above the 99th percentile to the 99th-percentile value
- **Log transform:** compresses big numbers so a $1M deal and a $100K deal aren't treated as wildly different (useful for revenue, prices, and counts)
- **Separate analysis:** model enterprise and small-business customers separately

### Business analogy

If you're figuring out the typical lunch spend at your company, one person's $3,000 client dinner shouldn't decide the answer. But if you're auditing expenses, that's exactly the receipt you want to look at.

---

## Lesson 2.4: Turning Categories into Numbers

### Plain-language explanation

Models only understand numbers. Columns like "Region", "Product Type", or "Plan Tier" need converting. This is called **encoding**.

### The two main methods

**One-hot encoding** (for categories with no natural order)
- Creates one yes/no column per category
- "Region" with values North, South, West becomes three columns: `region_North`, `region_South`, `region_West`
- Use for: regions, industries, product lines, sales channels

**Ordinal encoding** (for categories with a natural order)
- Replaces categories with ranked numbers
- "Plan Tier" with Basic, Pro, Enterprise becomes 1, 2, 3
- Use for: tiers, sizes (S/M/L), satisfaction ratings, education levels

### Handling rare categories

If "Industry" has 40 values and 25 of them appear fewer than five times, group the rare ones into "Other." Otherwise the model tries to learn from a handful of examples per category, which is a fast route to overfitting.

### Why it matters for decisions

Encoding wrong can quietly mislead the model. Coding regions as North=1, South=2, West=3 tells the model that West is "three times" North, which is nonsense. Getting this right is what keeps the drivers the model reports meaningful.

---

## Lesson 2.5: Feature Engineering That Actually Matters

### Plain-language explanation

A **feature** is just an input column the model learns from. **Feature engineering** means creating better inputs from the raw data you have. This is where your business knowledge beats any algorithm.

### Business analogy

A raw transaction log is like a pile of receipts. Feature engineering is the bookkeeper turning those receipts into "average spend per visit," "days since last purchase," and "share of purchases on sale." Same information, much more useful.

### The most valuable business features

| Feature type | Examples | Typical use |
|---|---|---|
| **Recency** | Days since last purchase, login, or contact | Churn, re-engagement |
| **Frequency** | Purchases per month, logins per week | Loyalty, engagement scoring |
| **Monetary** | Total spend, average order value | Customer value, segmentation |
| **Tenure** | Months as a customer, employee, or vendor | Churn, lifetime value |
| **Ratios** | Discount as % of price, support tickets per order, margin % | Profitability, risk |
| **Trends** | Spend this quarter vs. last quarter | Early warning signals |
| **Date parts** | Month, day of week, quarter, holiday flag | Seasonality in sales or demand |

### Why it matters for decisions

Good features make results explainable. "Customers whose spend dropped 30% quarter-over-quarter are 4x more likely to churn" is something a manager can act on. A model trained only on raw columns rarely produces insights that clear.

---

## Lesson 2.6: Data Leakage, the Silent Killer

### Plain-language explanation

**Leakage** happens when your model learns from information it wouldn't actually have at the moment you need a prediction. It makes models look brilliant in testing and useless in real life.

### Business analogy

It's like grading a stock-picking strategy using next month's newspaper. Of course it looks great.

### Common leakage examples

| Prediction | Leaky feature | Why it leaks |
|---|---|---|
| Will this customer churn? | "Cancellation reason" | Only exists after they've churned |
| Will this deal close? | "Contract signed date" | Only filled in for deals that closed |
| Will this invoice be paid late? | "Days overdue" | That's basically the answer |
| Next month's sales | Next month's ad spend (if not yet decided) | Not known on decision day |

### The second, sneakier kind: prep leakage

If you calculate fill values or scaling using **all** your data before splitting into training and testing sets, the test set has quietly "seen" the training data. The fix is to bundle all prep steps into a **pipeline**, covered next, so they're learned only from training data.

### Why it matters for decisions

Leakage is the number one reason ML projects get approved on great test results and then quietly fail in production. Catching it protects your credibility.

---

## Lesson 2.7: Pipelines, Your Repeatable Recipe

### Plain-language explanation

A **pipeline** packages every prep step (filling blanks, encoding categories, scaling numbers) together with the model into one object. When you train it, everything learns from training data only. When you use it on new data, every step is applied the same way automatically.

### Business analogy

It's a standard operating procedure. Instead of a new hire remembering twelve steps in the right order, the procedure is written down and followed identically every time.

### Why pipelines matter

- **Prevents prep leakage** automatically
- **Makes results repeatable:** next month's data is prepped exactly like this month's
- **Makes handoff easy:** one saved file contains everything needed to score new data
- **Works with validation and tuning** in Module 7 without extra effort

In scikit-learn, you'll use two building blocks:
- **Pipeline:** chains steps in order
- **ColumnTransformer:** applies different prep to different column types (numbers get one treatment, categories another)

You won't have to write these from scratch. The template does it, and you'll just list which columns are numeric, which are categories, and which are ordered categories.

---

## Lesson 2.8: Scaling Numbers (When It Matters)

### Plain-language explanation

**Scaling** puts numeric columns on a similar range, so "annual revenue" (in millions) doesn't drown out "number of employees" (in tens).

### When to scale

| Model type | Needs scaling? |
|---|---|
| Linear and logistic regression with regularization (Modules 3–4) | **Yes** |
| K-nearest neighbors (Module 4) | **Yes** |
| Clustering (Module 6) | **Yes, critically** |
| Decision trees, random forest, gradient boosting (Module 5) | **No** |

The pipeline template includes scaling by default. It does no harm for tree models, so you won't need to remember the table.

---

## Step-by-Step Process: Prepping Any Dataset

Follow these steps in order, every time.

1. **Load and profile.** Run the profiling cell and read every line of output. Note anything surprising.
2. **Fix obvious errors.** Remove test records and duplicates, fix data types, and standardize category spellings.
3. **Run the leakage check.** Using your Module 1 checklist, remove any column not known on decision day.
4. **Decide on missing values.** For each column with blanks, choose drop, fill, or flag, and write a one-line reason.
5. **Review outliers.** For each extreme value, decide: error, real-but-rare, or the point of the analysis.
6. **Engineer features.** Create recency, frequency, monetary, tenure, ratio, and date features that fit your question.
7. **Group rare categories.** Combine categories with very few rows into "Other."
8. **Trim your feature list.** On small data especially, keep only features with a clear business reason to matter.
9. **Classify each column** as numeric, unordered category, or ordered category, and enter them in the template's config cell.
10. **Build and save the pipeline.** Run the template so all prep steps are bundled together.
11. **Write a data prep log.** A short record of every decision you made and why. Future you will thank you.

---

## Common Pitfalls and How to Avoid Them

### 1. Skipping the profile
**What it looks like:** Jumping straight into modeling.
**Fix:** Profiling takes ten minutes. Never skip it.

### 2. Too many features on small data (the big overfitting trap)
**What it looks like:** 80 rows and 40 input columns, because "more information is better."
**Why it's dangerous:** With that many features, the model can find "patterns" that are pure coincidence. It's like finding that customers whose names start with M and who signed up on Tuesdays buy more. It fits your 80 rows perfectly and means nothing.
**Fix:**
- Aim for at least 10–20 rows per feature
- Choose features with a business reason first, not because they're available
- Combine related features into one (a single "engagement score" instead of six separate activity columns)
- Group rare categories aggressively, since each category becomes its own column after one-hot encoding

### 3. Creating dozens of features "just in case"
**What it looks like:** Generating every possible ratio and combination.
**Fix:** Engineer features that answer a question a manager would ask. If you can't explain why it might matter, leave it out.

### 4. Prepping before splitting
**What it looks like:** Filling blanks or scaling with averages from the whole dataset, then splitting into train and test.
**Fix:** Always use a pipeline. This is the whole reason it exists.

### 5. Deleting outliers automatically
**What it looks like:** Removing everything more than three standard deviations from the average.
**Fix:** Look at the outliers. Some are your most important customers or the exact events you're trying to predict.

### 6. Silent fills that hide meaning
**What it looks like:** Filling blank "last login date" with the median, when blank actually means "never logged in."
**Fix:** Ask the data owner what a blank means. Add a missing-value flag whenever in doubt.

### 7. One-off manual edits
**What it looks like:** Fixing values by hand in Excel before loading.
**Fix:** Do fixes in the notebook so they're repeatable on next month's data. If you must edit by hand, record it in the prep log.

---

## Small vs. Large Data: What Changes in This Module

| Aspect | Small data (30–300 rows) | Large data (thousands+) |
|---|---|---|
| Profiling | **Same.** Plus, eyeball every row if you can. With 80 rows, you can. | **Same.** Use summary statistics and charts; sample rows to inspect. |
| Missing values | Avoid dropping rows. Prefer fill-and-flag. | Dropping a small share of rows is usually fine. |
| Outliers | One outlier can swing results dramatically. Review each one individually. | Individual outliers matter less; use capping or log transforms systematically. |
| Number of features | Keep it small, roughly rows ÷ 15. Business judgment picks them. | Can include many more; later modules help sort useful from useless. |
| Rare categories | Group aggressively (anything under ~5–10 rows into "Other"). | Can keep more categories, but still group the very rare ones. |
| Feature engineering | Fewer, stronger, business-driven features. | Room to try more, then let feature importance (Modules 5 and 8) prune them. |
| Leakage risk | **Same.** Equally damaging at any size. | **Same**, plus more columns means more places for leakage to hide. |
| Pipelines | **Same.** Always use one. | **Same.** Always use one. |
| Speed | Not a concern. | Profile on a sample first if the data is very large, then confirm on the full set. |

**Bottom line:** the steps are identical. Small data demands more care per row and tighter feature lists. Large data demands more systematic checks and watching for leakage hidden among many columns.

---

## Code Structure / Pipeline Outline

The data prep workflow (built into the CONFIG, EDA, and pipeline cells of every notebook in [`notebooks/`](../notebooks/)) follows this outline:

```text
1. CONFIG CELL
   - file path
   - target column
   - ID column (excluded from modeling)
   - date column (optional)
   - lists to fill in:
       numeric_columns
       categorical_columns       (no natural order)
       ordinal_columns + order   (e.g., Basic < Pro < Enterprise)
       columns_to_drop           (leakage, IDs, irrelevant)
   - rare category threshold (e.g., 10 rows)

2. LOAD AND PROFILE
   - load data with pandas
   - shape, data types, missing-value counts and %
   - duplicate check
   - numeric summaries (min, max, median)
   - category value counts
   - target distribution
   - DATA SIZE CHECK (from Module 1): small or large path

3. CLEANING (business-rule fixes, done in code)
   - remove duplicates and test records
   - fix data types (text-to-number, text-to-date)
   - standardize category spellings
   - drop leakage and ID columns

4. OUTLIER REVIEW
   - list extreme values per numeric column
   - optional capping or log transform for chosen columns

5. FEATURE ENGINEERING
   - recency / frequency / monetary helpers
   - tenure from start date
   - ratio features
   - date parts (month, day of week, quarter)
   - missing-value flags for selected columns

6. RARE CATEGORY GROUPING
   - collapse categories below threshold into "Other"

7. FEATURE COUNT CHECK
   - rows-per-feature ratio after encoding
   - warning if ratio is too low for small data

8. BUILD PREPROCESSING PIPELINE
   - numeric branch:      fill blanks (median) -> scale
   - categorical branch:  fill blanks ("Unknown") -> one-hot encode
   - ordinal branch:      fill blanks (most common) -> ordinal encode
   - combine branches with ColumnTransformer
   - (model is attached in Modules 3-6)

9. SANITY CHECK
   - fit pipeline on a training split only
   - show the transformed column names and a few rows

10. SAVE OUTPUTS
    - cleaned dataset
    - saved pipeline
    - auto-generated data prep log (decisions + counts)
```

Your job is mostly to fill in the config cell, read the profile, and make the business calls. The template handles the mechanics.

---

## Hands-On Exercise

**Time:** about 2 hours

1. **Load your dataset** (or the provided sample customer dataset) and run the profiling section. Write down three things that surprised you. (20 minutes)
2. **Clean and check for leakage**, using your Module 1 checklist. (20 minutes)
3. **Make missing-value and outlier decisions** for every affected column, with a one-line reason each. (20 minutes)
4. **Engineer three to five features** a manager in your business would find meaningful. (25 minutes)
5. **Group rare categories and run the feature count check.** If you're on small data and the ratio is too low, cut features until it isn't. (15 minutes)
6. **Build and save the pipeline**, then review the transformed output to confirm it looks right. (10 minutes)
7. **Write your data prep log**, in plain English, as if handing it to a colleague. (10 minutes)

---

## Discussion and Reflection Questions

1. **Pick the column in your data with the most missing values.** What do blanks in that column actually mean in your business? Would a customer, deal, or order with that blank behave differently from one without it? How would you check?

2. **If you could only give a model five features to answer your Module 1 question, which five would you choose and why?** What does your choice reveal about what you already believe drives the outcome, and how would you react if the model disagreed?

3. **Look at your most extreme record (the largest order, the oldest account, the most active user).** Is it an error, a rare but real case, or exactly the kind of case your business decision is about? What would change in your recommendation if you removed it, and what would change if you kept it?

---

## What "Done" Looks Like

You've finished Module 2 when you have:

- [ ] A data profile reviewed, with surprises written down
- [ ] Leaky columns removed using your Module 1 checklist
- [ ] A missing-value and outlier decision for every affected column, each with a one-line reason
- [ ] Three to five meaningful engineered features
- [ ] Rare categories grouped and the rows-per-feature check passed for your data size
- [ ] A saved pipeline that applies every prep step consistently
- [ ] A plain-English data prep log a colleague could follow

Clean, well-documented data is the biggest single driver of model quality. The time you spent here will pay off in every module that follows.

**Next up:** Module 3, Regression for Forecasting and Drivers, where you'll build your first model to predict a number and find out which factors move it.
