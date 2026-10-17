# Module 8: Interpreting Results and Turning Them into Decisions

**Estimated time:** 3 hours total (about 1 hour of reading, 2 hours hands-on)
**Primary tools:** Coefficient analysis, permutation importance, partial dependence plots (PDP), individual conditional expectation (ICE) plots, SHAP values, expected-value calculations

---

## Why This Module Matters

A model that nobody understands rarely gets used. A model that gets used without being understood can do real damage. This module closes the gap between "the model works" and "the business acts on it wisely."

You'll learn to answer the four questions every stakeholder asks:

1. **"What drives this outcome?"** (global explanations)
2. **"Why did this customer, deal, or order get this score?"** (local explanations)
3. **"What should we actually do?"** (from predictions to decisions)
4. **"How sure are you, and what could go wrong?"** (uncertainty and limits)

If you can answer those four clearly, you'll be more useful than many technically stronger analysts.

---

## Lesson 8.1: Global vs. Local Explanations

### Plain-language explanation

- A **global explanation** describes how the model behaves **overall**: which factors matter most across all cases, and in which direction.
- A **local explanation** describes **one specific prediction**: why this particular customer got a 78% churn score.

### Business analogy

A store manager reviewing performance:
- **Global:** "Across all our stores, staffing levels and foot traffic drive most of the sales differences."
- **Local:** "The Elm Street store underperformed last month mainly because of road construction, despite good staffing."

Both are true. They answer different questions for different people.

### Who needs which

| Audience | Usually needs | Why |
|---|---|---|
| Leadership | Global | To set strategy and allocate budget |
| Operations / analytics | Global + local | To check the model makes sense and monitor it |
| Frontline (sales, support, credit) | Local | To act on individual cases and explain them to customers |
| Customers or regulators | Local | To understand or challenge a specific decision |

### Why it matters for decisions

Giving leadership a pile of individual explanations, or giving a salesperson a global importance chart, wastes everyone's time. Match the explanation to the decision being made.

---

## Lesson 8.2: Choosing the Right Explanation Tool

You've already met some of these. Here's how they fit together.

| Tool | Question it answers | Global or local | Works with | Main caveat |
|---|---|---|---|---|
| **Coefficients / odds ratios** (Modules 3–4) | "How much does each factor move the outcome, holding others constant?" | Global | Linear and logistic models | Only valid if the straight-line assumption roughly holds |
| **Permutation importance** (Module 5) | "Which factors does the model rely on most?" | Global | Any model | Shows **how much**, not **which direction**; overlapping inputs share credit |
| **Partial dependence plots (PDP)** | "As this factor increases, what happens to the prediction on average?" | Global | Any model | Averages can hide differences between groups |
| **ICE plots** | "Does that effect look the same for every case?" | Between global and local | Any model | Can get cluttered on large data |
| **SHAP values** | "How much did each factor push this prediction up or down?" | Local (and global when summarized) | Any model; fast for tree models | Explains the **model**, not the real world |

### A practical default

- **Linear or logistic model:** coefficients or odds ratios, with confidence intervals. Add SHAP only if you need per-case explanations for frontline teams.
- **Tree model:** permutation importance + PDP for the top drivers + SHAP for individual cases.

---

## Lesson 8.3: Partial Dependence Plots, the "What Happens If" Chart

### Plain-language explanation

A **partial dependence plot** shows how the model's prediction changes as one factor goes from low to high, averaging over everything else. It reveals the **shape** of a relationship: straight, curved, flat, or a sudden jump.

### How it works (in plain terms)

For a factor like "discount %," the tool takes every row in your data, pretends its discount is 0%, and averages the predictions. Then it repeats for 5%, 10%, 15%, and so on. Plotting those averages gives the curve.

### Business analogy

A thermostat test. You set the whole building to 66°F, then 68°F, then 70°F, and record average comfort scores at each. The resulting curve shows where comfort peaks, without needing to understand the HVAC system's internals.

### Common shapes and what they mean

| Shape | Example | Business implication |
|---|---|---|
| Steady upward line | More sales calls, more revenue | Consistent lever; more is better across the range |
| Rises then flattens | Discount helps up to 15%, then nothing | **Diminishing returns.** Don't overspend beyond the bend. |
| Flat then jumps | Late-payment risk jumps after 60 days of tenure | A **threshold.** Focus effort right around it. |
| U-shape | Satisfaction lowest at mid-tier price | Something different is happening in the middle; investigate |
| Flat | Factor barely matters | Don't spend time or money on it |

### ICE plots: checking whether everyone behaves the same

A PDP shows the **average** effect. An **ICE plot** draws a separate faint line for every individual case. If the lines roughly follow the same shape, the average tells the truth. If they fan out or cross, different groups respond differently, and you may want separate strategies.

**Example:** A discount PDP looks gently positive. The ICE plot reveals that new customers respond strongly, while long-time customers don't respond at all. The average hid two very different stories.

### Why it matters for decisions

PDPs answer "how much of this lever is worth pulling?" That's often the most actionable insight a model can produce.

---

## Lesson 8.4: SHAP Values, Explaining Individual Predictions

### Plain-language explanation

**SHAP** splits each prediction into contributions from each factor. It starts from the average prediction across all cases, then shows how much each factor pushed **this** prediction up or down.

### Business analogy

A restaurant bill broken down by line item. The total (the prediction) equals the base charge (the average) plus each item (each factor's contribution). You can see exactly what drove the total.

### Example: one customer's churn score

| | Contribution |
|---|---|
| Average churn probability (starting point) | 12% |
| 4 support tickets in last 60 days | +18% |
| Price increase last month | +11% |
| On monthly plan (not annual) | +6% |
| 3 years as a customer | −9% |
| **This customer's predicted churn probability** | **38%** |

*(Example data for illustration only. Real SHAP values for classification are often calculated on a log-odds scale; the template converts them to approximate probability terms for business readers.)*

This is the kind of breakdown a customer success manager can act on: "Reach out about the support issues and the price change."

### SHAP in summary form

Averaging SHAP values across all cases also gives a **global** view:
- **SHAP bar chart:** average size of each factor's contribution (similar to importance)
- **SHAP beeswarm chart:** each dot is one case, showing both size **and direction**. For example, high tenure (red dots) pushes churn down; low tenure (blue dots) pushes it up.

### SHAP waterfall charts for frontline teams

A **waterfall chart** draws the table above as a bar chart, and it's the best visual for explaining a single case. The template generates one for any row you choose, and can attach the **top three reasons** for every scored case in a ranked list (Module 4).

### The big caveat

SHAP explains **what the model does**, not **how the world works**. If the model learned a quirk or a correlation, SHAP faithfully reports that quirk. It's an X-ray of the model, not of reality.

---

## Lesson 8.5: Correlation, Causation, and What Models Can't Tell You

### The core problem

Every tool in this module explains **associations the model learned**. None of them prove that changing a factor will change the outcome.

### Business analogy

Ice cream sales and sunburns rise together. A model would happily predict sunburns from ice cream sales, and SHAP would confidently show ice cream as a top driver. Banning ice cream wouldn't prevent a single sunburn. Summer causes both.

### Three questions to ask about any top driver

1. **Could something else cause both?** (Engaged customers may both attend webinars and renew.)
2. **Could it run the other way?** (Do happy customers use the feature, or does the feature make them happy?)
3. **Can we actually change it?** (You can change price; you can't change a customer's industry.)

### Sort drivers into three buckets

| Bucket | Examples | How to use it |
|---|---|---|
| **Actionable and plausibly causal** | Response time, discount level, onboarding steps | Candidates for a pilot or A/B test |
| **Actionable but uncertain** | Feature usage, webinar attendance | Test before investing; don't mandate based on the model alone |
| **Not actionable (descriptive)** | Industry, region, company size | Use for targeting and forecasting, not as levers |

### The right next step: a pilot

When a driver looks promising, recommend a small, controlled test:
- Pick a random group to receive the change (e.g., faster support response)
- Keep a comparable random group as-is
- Compare outcomes after a set period

This turns "the model suggests" into "we've shown." Module 1's framing applies: the model points you to the experiment worth running.

### Why it matters for decisions

Most costly analytics mistakes come from treating drivers as levers. Being clear about this builds trust, because stakeholders learn that your recommendations are carefully qualified.

---

## Lesson 8.6: From Scores to Actions

### Plain-language explanation

A prediction becomes valuable only when it changes what someone does. You need a clear rule that connects model output to action.

### Three common patterns

**1. Tiered actions** (most common)
Split cases into tiers by score and assign a different action to each.

| Tier | Churn score | Action | Owner |
|---|---|---|---|
| High | Above 40% | Personal call from account manager within 5 days | Customer success |
| Medium | 20–40% | Targeted email with tailored offer | Marketing |
| Low | Below 20% | No special action | — |

*(Example thresholds for illustration only; set yours using Module 4, Lesson 4.9.)*

**2. Ranked lists with capacity limits**
"Our team can handle 150 calls this month. Here are the top 150 by score, each with its top three reasons."

**3. Forecast-driven planning** (regression)
"The model forecasts 1,240 orders next month, likely between 1,100 and 1,380. Staff for 1,300 and have an on-call plan above that."

### Expected value: putting a price on each action

For each case, estimate:

$$
\text{Expected value of acting} = (\text{probability of outcome}) \times (\text{value if action works}) \times (\text{chance action works}) - (\text{cost of action})
$$

**Example:** A customer with 40% churn probability is worth $1,200 per year. A retention call costs $40 and saves about 30% of at-risk customers who receive it.

$$
0.40 \times \$1{,}200 \times 0.30 - \$40 = \$104
$$

Positive expected value means the call is worth making. The template calculates this for every case and ranks them.

### Be honest about the assumptions

"Chance the action works" is usually a guess at first. State it clearly, run the calculation at a low, medium, and high value, and recommend a pilot to measure it properly.

---

## Lesson 8.7: Estimating ROI for Leadership

### A simple, defensible ROI estimate

1. **Number of cases acted on** (from your tier or capacity rule)
2. **Expected outcomes caught** (from your confusion matrix or lift chart, Module 4)
3. **Assumed success rate of the action** (stated clearly)
4. **Value per success** (from finance or historical data)
5. **Cost per action** plus any one-time setup cost

$$
\text{Estimated ROI} = \frac{\text{Total expected value} - \text{Total cost}}{\text{Total cost}}
$$

### Always compare three scenarios

| Scenario | Who gets action | Estimated net value |
|---|---|---|
| Do nothing | No one | $0 (baseline) |
| Current approach | e.g., everyone over $1,000 spend | $X |
| Model-driven | Top tier by model score | $Y |

The model's value is the gap between the current approach and the model-driven approach, not between the model and doing nothing.

### Present a range, not a single number

> "We estimate the model-driven approach adds $40,000 to $95,000 per year compared with our current process, depending on how effective the retention offer turns out to be. A 3-month pilot would narrow that range."

That's credible, honest, and actionable.

---

## Lesson 8.8: Communicating Uncertainty

### Why it matters

Stakeholders don't need certainty. They need to know **how much to rely** on what you're telling them. Being upfront about uncertainty makes you more credible, not less.

### Plain-language ways to express it

| Instead of | Say |
|---|---|
| "The model is 82% accurate." | "The model correctly ranks a churner above a non-churner about 8 times out of 10, based on testing on data it hadn't seen." |
| "Revenue will be $1.2M." | "Revenue will most likely be between $1.1M and $1.3M." |
| "Discount drives conversion." | "Higher discounts are associated with more conversions up to about 15%, but we haven't tested whether discounts cause it." |
| "This customer will churn." | "This customer is in our highest-risk group; about 4 in 10 customers like them leave within 90 days." |

### A simple confidence label

Rate each major finding:

| Confidence | Meaning | Typical evidence |
|---|---|---|
| **High** | Act on it | Stable across validation, confirmed by a pilot or several methods, makes business sense |
| **Medium** | Act cautiously or pilot first | Consistent in validation, plausible, not yet tested |
| **Low** | Treat as a hypothesis | Unstable, small data, wide interval, or surprising with no clear explanation |

---

## Lesson 8.9: Monitoring and When to Retrain

### Plain-language explanation

Models are built on past data. When the business changes (new pricing, a new competitor, a recession, a new product), the patterns can shift, and the model quietly gets worse. This is called **drift**.

### Business analogy

A road map is accurate when printed. Over time, new roads open and old ones close. The map isn't wrong; the world changed. You need to check it periodically.

### What to monitor (monthly or quarterly)

1. **Input drift:** are the inputs changing? (Average order size jumped 30%; a new region appeared.)
2. **Prediction drift:** is the share of high-risk cases changing unexpectedly?
3. **Performance:** once outcomes are known, is the model still beating the baseline?

### Simple retraining triggers

- Performance falls below your Module 1 "good enough" threshold
- A major business change (pricing, product, market)
- Input distributions shift noticeably
- A fixed schedule (e.g., every 6 or 12 months) as a minimum

### Why it matters for decisions

A model is a living tool, not a one-time project. Planning monitoring up front stops a good model from silently becoming a bad one.

---

## Lesson 8.10: The Insight Memo

### Why a memo, not a deck

Charts and dashboards get skimmed. A one-page memo forces clear thinking and gives decision-makers what they need in two minutes.

### The template

1. **Question:** the business decision this analysis supports (from Module 1)
2. **Answer:** the recommendation in one or two sentences
3. **Evidence:** the top three drivers or findings, each with direction, size, and a confidence label
4. **Performance:** honest score versus baseline and current approach, with range (from Module 7)
5. **Expected impact:** ROI range under stated assumptions
6. **What we don't know:** key assumptions, causal uncertainty, data limits
7. **Recommended action:** specific next step, owner, and timeline (often a pilot)
8. **Monitoring plan:** what you'll track and when you'll revisit

### Writing tips

- Lead with the answer, not the method
- Use business units (dollars, customers, days), not model metrics
- One chart maximum: usually a lift chart, PDP, or SHAP summary
- Name what the model **can't** tell you; it protects you and the reader
- If it doesn't fit on one page, cut until it does

---

## Step-by-Step Process: From Model to Decision

1. **Revisit your Module 1 brief.** Restate the decision, the action owner, and the "good enough" threshold.
2. **Load your final model** and its honest score from Module 7.
3. **Produce the global explanation:**
   - Linear models: coefficients or odds ratios with confidence intervals
   - Tree models: permutation importance
4. **Plot PDPs for the top three to five drivers.** Note the shape of each.
5. **Add ICE plots** for the top one or two drivers to check whether effects differ across cases.
6. **Run SHAP** on the final model. Review the summary chart and compare it with your global explanation.
7. **Generate local explanations** for three to five specific cases: a high score, a low score, and a surprising one.
8. **Sanity-check every top driver** with the three causation questions. Sort each into actionable, uncertain, or descriptive.
9. **Design the action rule:** tiers, ranked list, or forecast-driven plan, with owners.
10. **Calculate expected value and ROI** under low, medium, and high assumptions. Compare to the current approach.
11. **Assign confidence labels** to each major finding.
12. **Define the monitoring plan** and retraining triggers.
13. **Write the one-page insight memo.**
14. **Review it with one real stakeholder** before finalizing. If they can't explain the recommendation back to you, revise it.

---

## Common Pitfalls and How to Avoid Them

### 1. Over-interpreting explanations on small data (overfitting's sneaky cousin)
**What it looks like:** 90 rows, and a SHAP chart showing a precise, curvy relationship for a minor factor, which then becomes a slide titled "Key insight."
**Why it's dangerous:** On small data, models partly fit noise, and explanation tools faithfully explain that noise. A dramatic PDP curve might rest on just a few rows.
**Fixes:**
- Check how many rows sit in each part of a PDP; the template draws a data-density strip under every plot. Ignore regions with very few cases.
- Re-run explanations on several random subsets (or from each cross-validation fold). Trust only drivers and shapes that stay consistent.
- Prefer linear models with confidence intervals on small data; they explain themselves with honest uncertainty built in.
- Label small-data findings "medium" or "low" confidence unless they're confirmed by business knowledge or a pilot.

### 2. Treating explanations as causes
**What it looks like:** "SHAP shows webinar attendance reduces churn, so let's require webinars."
**Fix:** Apply the three causation questions. Recommend a pilot for anything you want to treat as a lever.

### 3. Trusting PDPs where inputs overlap heavily
**What it looks like:** A PDP for "number of orders" that imagines customers with 50 orders and $20 total spend, combinations that never happen.
**Why:** PDPs vary one factor while holding others fixed, even when that creates unrealistic cases.
**Fix:** Check correlations first (Module 3). For tightly linked inputs, interpret them together, or rely more on SHAP and ICE plots.

### 4. Explaining a leaky or broken model beautifully
**What it looks like:** Polished SHAP charts for a model whose top driver is a leaked variable.
**Fix:** Explanations are only as good as the model. If a top driver seems too good to be true, revisit the Module 1 leakage checklist before presenting anything. A model that looks brilliant usually has a problem, not a secret.

### 5. Presenting importance charts as the recommendation
**What it looks like:** A slide with a ranked bar chart of drivers and the title "Findings," with no action attached.
**Fix:** Every driver you present needs a "so what": the action it suggests, who owns it, and how confident you are. Charts support the recommendation; they aren't the recommendation.

### 6. A single, best-case ROI number
**What it looks like:** "This program will save $480,000 a year," based on optimistic success rates and full team capacity.
**Fix:** Show low, medium, and high scenarios, and state the assumptions behind each. Leadership trusts a range they can challenge more than a precise number they can't.

### 7. Explaining too much to the wrong audience
**What it looks like:** Showing SHAP waterfall charts to a frontline team that needs a call list, or a single customer's explanation to a board that needs the overall picture.
**Fix:** Match the explanation to the audience (Lesson 8.1). Leaders get global drivers and ROI. Frontline teams get a short list with the top two or three reasons per case. Analysts get the full detail.

### 8. Launching without a monitoring plan
**What it looks like:** The model goes live, results look good for a quarter, then quietly degrade as customers, prices, or products change, and nobody notices.
**Fix:** Before launch, agree on what you'll monitor, how often, who looks at it, and what triggers a retrain (Lesson 8.9).

---

## Small vs. Large Data: What Changes in This Module

| Aspect | Small data (30–300 rows) | Large data (thousands+) |
|---|---|---|
| Preferred explanation | Coefficients or odds ratios with confidence intervals | Permutation importance, PDPs, and SHAP |
| PDP / ICE plots | Use cautiously; check the data-density strip and ignore thin regions | Reliable across most of the range |
| SHAP | Fine for individual cases; treat global patterns as tentative | Reliable summary and local explanations |
| Stability check | **Essential**: re-run explanations across folds or subsets | Still useful; drivers are usually stable |
| Confidence labels | Default to medium or low unless confirmed by a pilot or strong business knowledge | High is achievable for consistent, stable drivers |
| ROI estimates | Wide ranges; present as rough and directional | Narrower ranges; can support budget decisions |
| Pilot design | Small pilots may lack the volume to show a clear result; plan longer or combine periods | Randomized pilots can give clear answers quickly |
| Use of results | Prioritization and hypothesis generation, with humans in the loop | Can support semi-automated decisions with monitoring |
| Monitoring | Check quarterly; too few cases for monthly signals | Monthly or more often; automated alerts are practical |

**Bottom line:** the steps are the same at any size. On small data, explanations are hypotheses to test, not conclusions to announce, so lean on simple models, honest intervals, and pilots. On large data, explanations become more precise, but the line between correlation and causation doesn't move at all.

---

## Code Structure / Pipeline Outline

Interpretation is built into every companion notebook rather than living in a separate one. The interpretation and decision sections follow this outline:

```text
1. CONFIG CELL
   - path to saved final model (joblib) and data
   - top N drivers to explain (e.g., 5)
   - cases to explain individually (IDs, or "highest / lowest / surprising")
   - decision economics: value per success, cost per action,
     action success rate (low / medium / high), team capacity

2. GLOBAL EXPLANATION
   - linear models: coefficients or odds ratios with confidence intervals
   - tree models: permutation importance on held-out data, repeated,
     with error bars
   - plain-English sentence for each top driver

3. "WHAT HAPPENS IF" CHARTS
   - partial dependence plots for the top drivers
   - ICE lines for the top 1-2 drivers
   - data-density strip under each plot

4. LOCAL EXPLANATIONS (optional, requires the shap package)
   - SHAP summary chart
   - waterfall charts for selected individual cases

5. STABILITY CHECK
   - importance ranks re-computed on bootstrap samples or CV folds
   - how often each driver stays in the top 5

6. DRIVER SORTING (manual)
   - each top driver labeled: actionable | uncertain | descriptive

7. ACTION RULE
   - risk tiers or ranked list, sized to team capacity
   - expected value per tier

8. ROI SCENARIOS
   - low / medium / high success-rate assumptions
   - compared to "do nothing" and the current approach
   - break-even success rate

9. OUTPUT
   - action list (ID, score, tier, top reasons)
   - driver summary table with confidence labels
   - pre-filled insight memo skeleton
```

Where to find each piece in this repository:

| Notebook | Interpretation shown |
|---|---|
| [`notebooks/01_linear_regression.ipynb`](../notebooks/01_linear_regression.ipynb) | Coefficients in business units, standardized ranking, intervals |
| [`notebooks/02_logistic_regression.ipynb`](../notebooks/02_logistic_regression.ipynb) | Odds multipliers, threshold and expected-value tables |
| [`notebooks/03_decision_tree.ipynb`](../notebooks/03_decision_tree.ipynb) | Plain-English leaf rules and split stability |
| [`notebooks/04_random_forest_churn.ipynb`](../notebooks/04_random_forest_churn.ipynb) | Permutation importance, PDP / ICE, importance-rank stability, call list |
| [`notebooks/05_gradient_boosting.ipynb`](../notebooks/05_gradient_boosting.ipynb) | Permutation importance and PDP for boosted models |
| [`notebooks/06_end_to_end_churn.ipynb`](../notebooks/06_end_to_end_churn.ipynb) | The full path from drivers to actions, tiers, ROI, and an executive summary |

---

## Hands-On Exercise

**Time:** about 2 hours

1. **Restate your Module 1 brief** in two sentences: the decision, the owner, and what "good enough" means. (5 minutes)
2. **Produce the global explanation** for your final model and write one plain-English sentence for each of the top five drivers. (20 minutes)
3. **Plot PDPs for the top three drivers** and ICE lines for the top one. Describe each shape (flat, steady, threshold, plateau) and check the density strip. (20 minutes)
4. **Explain three individual cases**: a high score, a low score, and a surprising one. Would the explanation make sense to the person who owns that customer or account? (15 minutes)
5. **Sort every top driver** into actionable, uncertain, or descriptive, using the three causation questions. (15 minutes)
6. **Design the action rule**: tiers or a ranked list, sized to your team's capacity, with an owner for each tier. (10 minutes)
7. **Calculate ROI** under low, medium, and high assumptions, and find the break-even success rate. (15 minutes)
8. **Write the one-page insight memo** using the template in Lesson 8.10, with confidence labels on each finding. (20 minutes)

---

## Discussion and Reflection Questions

1. **Pick your most "actionable" driver.** If you had to bet your own budget on it, what evidence would you want first? Design the smallest pilot that would give you that evidence, including who gets the action, who doesn't, and how long you'd wait.

2. **Your ROI estimate is positive in the medium and high scenarios but negative in the low scenario.** How would you present that to leadership? What would you recommend: launch, pilot, or wait?

3. **Imagine your model has been live for six months.** What is the first sign that it's starting to fail, and who in your organization would be the first to notice? How could you make sure you notice before they do?

---

## What "Done" Looks Like

You've finished Module 8 when you have:

- [ ] A global explanation of your final model, in plain English, with uncertainty shown
- [ ] PDP (and ICE) charts for the top drivers, with thin-data regions ignored
- [ ] Local explanations for at least three individual cases
- [ ] Every top driver sorted into actionable, uncertain, or descriptive
- [ ] A concrete action rule with owners, sized to real team capacity
- [ ] ROI under low, medium, and high scenarios, compared to the current approach
- [ ] A monitoring plan with clear retraining triggers
- [ ] A one-page insight memo reviewed by at least one real stakeholder

If your memo recommends a pilot instead of a full rollout, that's often the most responsible and credible outcome. A well-designed test turns a model's hint into proof.

**Next up:** Module 9, End-to-End Templates and Case Studies, where you'll put every step together and run a complete project from question to recommendation.
