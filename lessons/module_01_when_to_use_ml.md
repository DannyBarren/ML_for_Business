# Module 1: When to Use ML (and When Not To)

**Estimated time:** 2 hours total (about 45 minutes of reading, 75 minutes hands-on)

---

## Why This Module Comes First

Most failed analytics projects don't fail because the model was bad. They fail because someone asked the wrong question, had no baseline to compare against, or built something nobody could act on. This module helps you avoid all three before you write a single line of code.

Think of it like a contractor walking the site before quoting a job. Ten minutes of framing saves weeks of rework.

---

## Lesson 1.1: What Machine Learning Actually Is

### Plain-language explanation

Machine learning means finding patterns in past examples so you can make a good guess about new ones. That's it.

Picture an experienced sales manager who has seen hundreds of deals. When a new deal comes in, they glance at the company size, industry, and how fast the buyer replied, and say "this one's likely to close." They're using patterns from experience. ML does the same thing, except it can look at thousands of examples at once, it's consistent, and it can tell you which factors it weighed most.

What ML is **not**:
- It's not magic. It can't find a pattern that isn't in your data.
- It's not a crystal ball. It assumes the future looks roughly like the past.
- It's not proof of cause and effect. It finds what goes together, not necessarily what causes what.

### Why it matters for decisions

If you understand ML as "pattern-matching from past examples," you'll naturally ask the right questions: Do I have enough past examples? Will the future look like the past? Can I act on the pattern even if it's not causal?

---

## Lesson 1.2: The Three Kinds of Business Questions

Almost every ML question you'll ask falls into one of three buckets.

| Question type | Plain-English version | ML name | Business examples |
|---|---|---|---|
| How much? | "Predict a number" | Regression | Next month's sales, deal size, delivery time, project cost |
| Which one? | "Predict a category, usually yes/no" | Classification | Will this customer churn? Will this lead convert? Is this invoice likely late? |
| What groups? | "Find natural groupings" | Clustering | Customer segments, product families, store types |

There's also a fourth question that people often confuse with the first three:

| Question type | Plain-English version | Why it's different |
|---|---|---|
| Why? / What if? | "If I change X, what happens to Y?" | This is a **causal** question. ML can hint at answers, but the reliable tools are experiments (A/B tests) or careful before-and-after comparisons. |

### Business analogy

- **Regression** is like a weather forecast for temperature: a number with a margin of error.
- **Classification** is like a weather forecast for rain: a probability you turn into a decision (bring an umbrella or not).
- **Clustering** is like sorting a messy closet into piles of similar things, without anyone telling you the categories in advance.

### Why it matters for decisions

Naming the question type tells you which module to use, which metric to judge by, and what the final output looks like (a number, a ranked list, or a set of groups).

---

## Lesson 1.3: When Simpler Analysis Wins

ML is a power tool. Sometimes you need a screwdriver.

### Use simpler analysis when:

- **You need to describe what happened, not predict.** "Which region had the highest sales last quarter?" is a pivot table, not a model.
- **One or two factors explain almost everything.** If 90% of late shipments come from one carrier, you don't need a model. You need a call with that carrier.
- **A clear rule already works.** "Flag any order over $10,000 for review" might beat a model if it catches most problems and everyone understands it.
- **You want to know if a change caused a result.** Run an A/B test or a pilot.
- **You have very few examples of the thing you care about.** If only 6 customers churned last year, a model has almost nothing to learn from.
- **Nobody will act on the output.** A beautiful churn score is worthless if no one calls the at-risk customers.

### Use ML when:

- Many factors interact and no single rule captures the pattern
- You need to score or rank many items (customers, leads, orders) consistently
- The decision repeats often enough that small accuracy gains add up
- You have enough past examples with known outcomes
- Someone has committed to acting on the results

### The "pivot table first" rule

Before any model, spend 20 minutes with pivot tables and simple charts. If the answer jumps out, you may be done. If it doesn't, you've learned which factors to feed the model and have a sanity check for later.

---

## Lesson 1.4: Baselines, Your Most Important Number

### Plain-language explanation

A baseline is the score you'd get with no model at all, using the dumbest reasonable guess. Every model must beat it, or it isn't worth using.

Common baselines:
- **For "how much?" questions:** always predict the average (or last month's value)
- **For yes/no questions:** always predict the most common answer
- **For any question:** the rule your team already uses today

### Business analogy

If a consultant tells you their model is "85% accurate" at predicting which customers stay, ask: "What percent of customers stay anyway?" If it's 85%, the model does nothing that "assume everyone stays" doesn't.

### Why it matters for decisions

The baseline turns a meaningless accuracy number into a meaningful one: "Our model catches 3x more at-risk customers than our current rule." That's a sentence a leadership team can act on.

---

## Lesson 1.5: Do You Have Enough Data?

There's no magic number, but these working rules will keep you honest.

| Your situation | Rough guidance |
|---|---|
| Under 30 rows | Stick to descriptive analysis, charts, and expert judgment. A model will mostly memorize noise. |
| 30–300 rows (small data) | Simple, interpretable models with few inputs. Expect wide error ranges. Use the small-data methods in this course. |
| 300–5,000 rows | Most classical methods work well. Tree ensembles become reasonable. |
| 5,000+ rows | Full toolkit, including gradient boosting and richer features. |

Two more checks that matter as much as total rows:
- **Rows per input factor:** aim for at least 10–20 rows for every factor you include. With 100 rows, keep it to about 5–10 factors.
- **Examples of the rare outcome:** for yes/no questions, count the smaller group. 1,000 customers with only 15 churners is effectively a very small dataset.

---

## Step-by-Step Process: Framing an ML Project

Follow these seven steps for every project in this course and beyond.

1. **Write the business decision.** Not "build a churn model" but "decide each month which 200 customers get a retention call."
2. **Name the question type.** How much, which one, what groups, or what if?
3. **Define the target.** What exactly are you predicting, measured how, over what time window? ("Customer cancels within 90 days of today.")
4. **Check data availability at decision time.** For every input, ask: would I actually know this on the day I make the decision?
5. **Set the baseline.** Calculate the no-model score and write down your team's current rule.
6. **Size up the data.** Count rows, count factors, count examples of the rare outcome. Decide small-data or large-data path.
7. **Define "good enough" and the action.** What improvement over baseline would justify using this? Who acts on the output, and how?

If you can't complete steps 1, 3, and 7, stop. The project isn't ready for ML yet.

---

## Common Pitfalls and How to Avoid Them

### 1. Starting with the algorithm instead of the decision
**What it looks like:** "Let's try random forest on our CRM data and see what we find."
**Fix:** Always start with step 1 above. Algorithms are chosen last.

### 2. Overfitting, especially on small data
**What it is:** The model memorizes the quirks of your specific examples instead of learning general patterns. It looks brilliant on past data and fails on new data.
**Analogy:** A student who memorizes last year's exam answers instead of learning the subject. Perfect score on the practice test, poor score on the real one.
**Warning signs:**
- Near-perfect results on the data you trained on
- Results that swing wildly when you add or remove a few rows
- Lots of input factors compared to rows
- A "surprising" top driver that nobody in the business can explain
**Fixes (covered fully in Modules 3, 5, and 7):**
- Keep the number of factors small
- Always test on data the model hasn't seen
- Prefer simpler models on small data
- Treat any result as a hypothesis to confirm, not a final answer

### 3. Data leakage (using information from the future)
**What it looks like:** Predicting churn using "number of support tickets in the last 30 days of the contract," when you need the prediction 60 days before the contract ends.
**Analogy:** Betting on a horse race after seeing the finish.
**Fix:** Step 4 of the process. For every input, ask "would I know this on decision day?"

### 4. Confusing correlation with causation
**What it looks like:** "Customers who use feature X churn less, so let's force everyone onto feature X."
**Reality:** Maybe your most engaged customers use feature X *because* they're engaged.
**Fix:** Use models to find candidates, then test changes with a pilot or A/B test before rolling them out.

### 5. No baseline
**What it looks like:** Celebrating "80% accuracy" without knowing that guessing gets 78%.
**Fix:** Step 5, every time.

### 6. Building something nobody uses
**What it looks like:** A great model in a notebook that never makes it to the sales team.
**Fix:** Name the person who acts on the output before you start, and show them a mock-up of what they'll receive.

---

## Small vs. Large Data: What Changes in This Module

| Aspect | Small data (30–300 rows) | Large data (thousands+) |
|---|---|---|
| Framing steps | **Same.** The seven steps apply regardless of size. | **Same.** |
| Pivot-table-first check | Even more important. Often the answer is visible without a model. | Still worth doing to choose inputs and catch data problems. |
| Number of input factors | Keep it tight (roughly rows ÷ 15). Choose based on business knowledge. | Can include many more; the model can sort through them. |
| Baseline | Critical, because small improvements may just be luck. | Critical, because large data can make tiny, useless improvements look "significant." |
| Expectations | Wide error ranges. Results are directional, good for prioritizing and hypothesis-forming. | Narrower error ranges. Results can support automated decisions. |
| Overfitting risk | High. The main danger. | Lower, but leakage and drift become bigger risks. |
| Expert judgment | Plays a large role in validating results. | Still essential for sanity checks, but the data carries more weight. |

**Bottom line:** the thinking is the same at any size. What changes is how much you trust the output and how complex a model you can afford.

---

## Code Structure / Pipeline Outline

This module's framing workflow is a framing tool, not a modeling tool. It isn't one of the six notebooks published in this repository's [`notebooks/`](../notebooks/) folder, but every published notebook opens with the same steps (CONFIG cell, data peek, size check, baseline). Here's the outline:

```text
1. CONFIG CELL
   - file path
   - target column name
   - question type: "regression" | "classification" | "clustering"
   - date column (optional)
   - decision-day cutoff (optional)

2. LOAD AND PEEK
   - load CSV/Excel with pandas
   - show first rows, column types, row count

3. DATA SIZE CHECK
   - count rows and candidate input columns
   - compute rows-per-factor ratio
   - for classification: count rows in each outcome group
   - print recommendation: "small-data path" or "large-data path"

4. QUICK DESCRIPTIVE ANALYSIS ("pivot table first")
   - summary statistics for the target
   - target broken down by top categorical columns
   - simple charts of target vs. top numeric columns

5. BASELINE CALCULATION
   - regression: error if you always predict the average
   - classification: accuracy if you always predict the most common outcome
   - optional: score of the team's existing rule

6. LEAKAGE CHECKLIST (manual)
   - print every column name with a blank "known on decision day? Y/N" field

7. PROJECT BRIEF OUTPUT
   - fill-in-the-blank summary: decision, target, baseline,
     data size path, "good enough" threshold, action owner
```

You'll fill in the configuration cell, run everything, and mostly spend your time reading the output and filling in the project brief.

---

## Hands-On Exercise

**Time:** about 75 minutes

1. **Pick one real business decision** you or your team makes repeatedly. (15 minutes)
2. **Complete the seven framing steps** in the project brief template. (20 minutes)
3. **Run the framing notebook** on your own data, or on a provided sample dataset if yours isn't ready. (20 minutes)
4. **Answer one question honestly:** after the descriptive analysis, do you still need ML, or did the pivot tables answer it? (10 minutes)
5. **Fill in the leakage checklist** for every input column. (10 minutes)

---

## Discussion and Reflection Questions

1. **Think of a decision your business makes every week or month.** What would a "do-nothing" baseline look like for it, and how much better would a model need to be before you'd change how you work? Put a number or dollar figure on it if you can.

2. **List the five pieces of data you'd most want to use for that decision.** For each one, would you genuinely know it on the day you make the decision, or only afterward? Which ones surprised you?

3. **Recall a time your business acted on a pattern that turned out to be coincidence or the wrong way around** (or one you suspect might be). How would you test it properly today before committing money or people to it?

---

## What "Done" Looks Like

You've finished Module 1 when you have:

- [ ] A one-page project brief with a clear business decision, target definition, and action owner
- [ ] The question type identified (regression, classification, clustering, or "not ML")
- [ ] A calculated baseline number you'd need to beat
- [ ] A data size check with a small-data or large-data path chosen
- [ ] A completed leakage checklist
- [ ] An honest go/no-go call: ML, simpler analysis, or "need more data first"

A "no-go" is a perfectly good result. Deciding not to build a model you don't need is one of the most valuable skills in this course.

**Next up:** Module 2, Data Prep Essentials, where you'll turn the raw data you just profiled into something a model can learn from.
