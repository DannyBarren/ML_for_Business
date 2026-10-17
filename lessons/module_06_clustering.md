# Module 6: Clustering and Customer Segmentation

**Estimated time:** 3 hours total (about 1 hour of reading, 2 hours hands-on)
**Primary algorithms:** K-means, hierarchical (agglomerative) clustering, DBSCAN, Gaussian mixture models (GMM), PCA for visualization

---

## Why This Module Matters

Modules 3–5 predicted a known outcome. Clustering is different: there's **no target column**. You're asking the data, "What natural groups exist here?"

That's one of the most common business needs:

- Which kinds of customers do we have, and how should we treat each one?
- Which products behave alike in terms of demand, margin, or returns?
- Which stores or branches face similar conditions?
- Which accounts look unusual and deserve a closer look?

A good segmentation changes how a business operates: different offers, service levels, pricing, or messaging for each group. A bad one produces pretty charts that nobody uses. This module teaches you to build the useful kind.

---

## Lesson 6.1: What Clustering Does

### Plain-language explanation

Clustering looks at your rows (customers, products, locations) and groups together the ones that are most similar across the inputs you choose. You decide **what "similar" means** by choosing the inputs. The algorithm does the sorting.

### Business analogy

Imagine dumping a box of mixed hardware on a table: screws, nails, washers, bolts. Without labels, you'd naturally sort them by shape and size. Clustering does the same thing with customers, using spend, frequency, tenure, or whatever you choose.

### The most important point in this module

**Clusters reflect the inputs you choose.** Cluster on spending behavior and you get spending segments. Cluster on product mix and you get preference segments. Cluster on demographics and you get demographic segments. There's no single "true" segmentation, only more or less useful ones for a specific decision.

### Why it matters for decisions

Start from the decision. "We want to tailor retention offers" calls for behavior and value inputs. "We want to tailor product recommendations" calls for purchase-mix inputs. Choosing inputs is a business decision before it's a technical one.

---

## Lesson 6.2: RFM, the Starter Segmentation

### Plain-language explanation

**RFM** stands for:
- **Recency:** how recently did they buy?
- **Frequency:** how often do they buy?
- **Monetary:** how much do they spend?

These three numbers, built in Module 2, capture most of what matters about customer value and engagement.

### Two ways to use RFM

**1. Rule-based RFM (no ML needed)**
Score each customer 1–5 on each dimension by splitting into fifths, then combine:
- 5-5-5: champions
- 1-1-5: big spenders who've gone quiet
- 5-1-1: new, low-spend customers

This is often good enough, and it's a great baseline.

**2. Clustered RFM**
Feed the three RFM values into K-means (Lesson 6.4) and let the algorithm find the natural groupings. This often produces more natural boundaries than fixed fifths.

### Why it matters for decisions

RFM segments map directly to actions: reward champions, win back lapsed big spenders, nurture new customers. It's the fastest route from data to a marketing or account-management plan.

Like the baseline in Module 1, **always compare your clustered segments to simple RFM scoring.** If the clusters don't give you a more useful story, use the simpler version.

---

## Lesson 6.3: Why Scaling Matters So Much Here

### Plain-language explanation

Clustering measures "distance" between rows. If one input is in thousands of dollars and another is a count from 1 to 10, the dollar column completely dominates. The algorithm effectively ignores everything else.

### Business analogy

Comparing two cities by population (millions) and number of airports (1–3) without adjusting for units. You'd end up grouping cities only by population, as if airports didn't exist.

### What to do

- **Always scale** before clustering. The template does this by default (from Module 2).
- **Log-transform skewed inputs** like spend and order counts first. Otherwise, a few huge customers stretch the scale and squash everyone else into one blob.
- **Keep the number of inputs modest** (typically 3–8). Every extra input dilutes the others and makes clusters harder to interpret.

### Why it matters for decisions

Skipping scaling is the single most common reason segments come out as "big customers" and "everyone else." Proper scaling is what lets more subtle, useful patterns show up.

---

## Lesson 6.4: K-means, the Workhorse

### Plain-language explanation

K-means asks you for a number of groups, **K**. It then:
1. Places K starting points (called **centers**) in the data
2. Assigns every row to its nearest center
3. Moves each center to the middle of its assigned rows
4. Repeats until things settle

### Business analogy

Deciding where to put K regional warehouses. You start with rough locations, assign each customer to the closest warehouse, move each warehouse to the center of its customers, and repeat until the layout stops changing.

### Strengths

- Fast, even on very large data
- Simple to explain: "each customer is in the group whose average profile they're closest to"
- Each cluster's center is a ready-made profile

### Weaknesses

- You must choose K
- Assumes roughly round, similar-sized groups
- Sensitive to outliers, which can pull centers away
- Results can vary slightly between runs (the template runs it many times and keeps the best)

### Why it's the default

For most business segmentation, K-means on well-chosen, scaled inputs produces clear, actionable groups. Start here.

---

## Lesson 6.5: Choosing the Number of Segments

There's no single right answer. Use three lenses together.

### Lens 1: The elbow method

Plot how tightly grouped the clusters are (called **inertia**) for K = 2, 3, 4, ... 10. It always improves as K grows, but at some point the improvement slows sharply. That bend is the "elbow."

**Analogy:** Adding staff to a project. The first few hires help a lot; after a point, each extra person adds little.

### Lens 2: Silhouette score

Measures how well each row fits its own cluster compared to the nearest other cluster. Ranges from -1 to 1:
- **Above 0.5:** clearly separated groups
- **0.25–0.5:** reasonable, some overlap (very common in business data)
- **Below 0.25:** weak structure; groups blur together

Pick the K with a relatively high silhouette that also makes business sense.

### Lens 3: Business usability (the deciding lens)

- **Can your team actually act differently for each segment?** If marketing can only run three campaigns, seven segments isn't useful.
- **Is each segment big enough to matter?** A segment with 1% of customers may not justify its own strategy.
- **Can you describe each segment in one sentence?** If not, it's probably not a real segment.

**Typical sweet spot: 3–6 segments.**

### Why it matters for decisions

The numbers suggest a range. Business judgment makes the call. A slightly lower silhouette with four clearly actionable segments beats a higher one with nine confusing ones.

---

## Lesson 6.6: Other Clustering Methods Worth Knowing

### Hierarchical (agglomerative) clustering

**What it does:** Starts with every row as its own group, then repeatedly merges the two most similar groups until everything is one big group. The result is a tree diagram called a **dendrogram**.
**Analogy:** An organizational chart built from the bottom up: individuals form teams, teams form departments, departments form divisions.
**Strengths:** You can see the whole structure and "cut" the tree at whatever level makes sense; no need to pick K in advance; great for small data.
**Weaknesses:** Slow on large data (it compares every pair of rows).
**Use for:** Small data (under a few thousand rows), and when you want to see how segments nest inside each other.

### DBSCAN

**What it does:** Finds dense pockets of similar rows and labels isolated rows as **noise** (outliers).
**Analogy:** Spotting neighborhoods on a map at night by where the lights cluster. Scattered farmhouses aren't forced into any neighborhood.
**Strengths:** Finds oddly shaped groups; flags unusual rows automatically; no need to pick K.
**Weaknesses:** Sensitive to its two settings (how close counts as "near," and how many neighbors make a dense area); struggles when groups have different densities.
**Use for:** Finding outliers and anomalies (unusual accounts, suspicious transactions, odd locations), and data where groups aren't round blobs.

### Gaussian mixture models (GMM)

**What it does:** Like K-means, but gives each row a **probability** of belonging to each cluster, and allows stretched, oval-shaped groups.
**Analogy:** Instead of saying "this customer is a bargain hunter," it says "this customer is 70% bargain hunter, 30% loyal regular."
**Strengths:** Handles overlapping segments honestly; flexible shapes.
**Weaknesses:** More complex to explain; can be unstable on small data.
**Use for:** Medium-to-large data where segments genuinely overlap and soft membership is useful (for example, mixed marketing).

### Quick comparison

| Method | Need to choose K? | Handles outliers | Small data | Large data | Easy to explain |
|---|---|---|---|---|---|
| K-means | Yes | Poorly | Good | Excellent | High |
| Hierarchical | No (cut the tree) | Moderately | Excellent | Slow | High (dendrogram) |
| DBSCAN | No | Excellent (labels them) | Fair | Good | Medium |
| GMM | Yes | Poorly | Fair | Good | Medium |

---

## Lesson 6.7: Seeing Your Segments with PCA

### Plain-language explanation

You might cluster on six inputs, but you can't draw a six-dimensional chart. **PCA** (principal component analysis) compresses many inputs into two summary dimensions that capture as much variation as possible, so you can plot every row on a flat chart colored by segment.

### Business analogy

A shadow of a 3D object on a wall. You lose some detail, but you can still see the overall shape and whether the pieces are separated.

### How to read a PCA chart

- **Well-separated colored clouds:** distinct segments
- **Heavy overlap:** segments may blur together in reality (check the silhouette)
- **Far-off scattered points:** potential outliers

### Important caveat

PCA is for **looking**, not for explaining. The two axes are blends of your inputs and rarely have a clean business meaning. Use segment profiles (next lesson) to explain what each segment is.

---

## Lesson 6.8: Profiling and Naming Segments

### This is where segmentation becomes useful

A cluster number ("Segment 3") means nothing to a marketing manager. A profile and a name ("Lapsed big spenders: high lifetime value, no purchase in 6+ months") is actionable.

### Build a profile table

For each segment, show:
- **Size:** count and % of total
- **Average of each clustering input** (in original business units, not scaled values)
- **Comparison to overall average** (e.g., "2.3x average spend")
- **Outcomes you didn't cluster on:** churn rate, margin, support cost, NPS. This is where business value shows up.

**Example profile table:**

| Segment | Size | Avg. spend | Orders/yr | Days since last order | Churn rate | Suggested name |
|---|---|---|---|---|---|---|
| 1 | 18% | $2,400 | 14 | 12 | 4% | Loyal champions |
| 2 | 31% | $380 | 3 | 45 | 12% | Occasional regulars |
| 3 | 9% | $1,900 | 9 | 210 | 41% | Lapsed big spenders |
| 4 | 42% | $95 | 1 | 160 | 28% | One-and-done |

*(Example data for illustration only.)*

### Write a persona and an action for each

- **Loyal champions:** protect and reward. Early access, loyalty perks, referral asks.
- **Lapsed big spenders:** win back. Personal outreach, a strong comeback offer.
- **Occasional regulars:** grow frequency. Reminders, bundles, subscription offers.
- **One-and-done:** low-cost nurture. Automated emails; don't overspend.

### Explain segments with a shallow decision tree

Train a depth 2–3 decision tree (Module 5) to predict segment labels from the inputs. The tree's rules become a plain-English definition:

> "Lapsed big spenders: spend over $1,200 **and** no order in the last 120 days."

These rules can also assign **new** customers to segments in a spreadsheet or CRM without running any model.

---

## Lesson 6.9: Are Your Segments Real? Stability Checks

### Plain-language explanation

Clustering always produces clusters, even on random noise. You need to check that your segments reflect real structure and not chance.

### Business analogy

Seeing shapes in clouds. Everyone can find a rabbit if they look long enough. The question is whether someone else, looking at a different patch of sky, would see the same rabbit.

### Three practical checks

1. **Re-run on random subsets.** Cluster several random 80% samples. Do similar segments appear with similar profiles?
2. **Re-run with different starting points.** For K-means, do repeated runs give the same groupings? (The template measures agreement automatically.)
3. **Check over time.** If you have data from different periods, cluster each one. Do the same segments show up?

### Reading the results

- **Segments reappear consistently:** real structure; safe to build strategy on
- **Segments shuffle or merge:** structure is weak; use fewer segments or simpler RFM rules
- **One segment appears only sometimes:** may be too small or borderline; consider merging it

### Why it matters for decisions

Building a marketing program, a sales territory plan, or a pricing tier around a segment that disappears next quarter is expensive. Stability checks protect that investment.

---

## Step-by-Step Process: Building a Segmentation

1. **Start from your Module 1 brief.** Name the decision the segments will drive and how many distinct actions your team can realistically run.
2. **Choose clustering inputs** that match that decision. Keep it to 3–8, with a business reason for each.
3. **Prep the inputs:** fill blanks, log-transform skewed values, review outliers, and scale everything (Module 2 pipeline, clustering mode).
4. **Build the simple baseline:** rule-based RFM scoring or an existing segmentation your team already uses.
5. **Run K-means for K = 2 through 10.** Plot the elbow chart and silhouette scores.
6. **Shortlist two or three values of K** that look reasonable and are practical for your team.
7. **On small data, also run hierarchical clustering** and review the dendrogram.
8. **Optionally run DBSCAN** to flag outliers and unusual rows.
9. **Visualize each shortlisted option with PCA.**
10. **Profile each option** in original business units, including outcomes you didn't cluster on.
11. **Choose the final K** based on separation, stability, and business usability.
12. **Run stability checks.** If segments don't hold, reduce K or fall back to the simpler baseline.
13. **Name each segment, write a one-line persona, and assign an action.**
14. **Train a shallow decision tree** to produce plain-English segment rules for assigning new customers.
15. **Write the segmentation summary:** segments, sizes, profiles, actions, and a refresh schedule.

---

## Common Pitfalls and How to Avoid Them

### 1. Over-segmenting small data (this module's version of overfitting)
**What it looks like:** 120 customers split into seven segments, some with only six or eight members.
**Why it's dangerous:** With so few rows, small segments are often random clumps. The profiles look specific ("these 7 customers buy mostly on Tuesdays"), but they won't hold up next quarter.
**Fixes:**
- Keep K small, usually 2–4 on small data
- Require every segment to hold a meaningful share (as a rough rule, at least 10% of rows or 15–20 rows, whichever is larger)
- Use hierarchical clustering and a dendrogram so you can see whether groups are clearly separate
- Run stability checks on subsets and trust only segments that reappear
- Compare against simple RFM rules; on small data, the rules often win

### 2. Forgetting to scale
**What it looks like:** Segments split purely by spend, ignoring everything else.
**Fix:** Always scale. Log-transform skewed inputs first.

### 3. Too many inputs
**What it looks like:** Clustering on 25 columns "to be thorough."
**Why it hurts:** With many inputs, every row looks roughly equally far from every other, and clusters become meaningless. Profiles become impossible to summarize.
**Fix:** 3–8 inputs, chosen for the decision at hand.

### 4. Letting outliers create their own segment
**What it looks like:** A "segment" of four mega-customers, or K-means centers pulled toward extreme accounts.
**Fix:** Log-transform or cap extreme values first, or handle the biggest accounts separately. Use DBSCAN to spot outliers before clustering.

### 5. Choosing K only by the numbers
**What it looks like:** "The silhouette score peaks at K=9, so we have nine segments."
**Fix:** Use the numbers to narrow the range, then let business usability decide.

### 6. Profiling in scaled units
**What it looks like:** "Segment 2 has a spend score of 1.4 and a frequency of -0.6."
**Fix:** Always report profiles in original business units: dollars, days, counts.

### 7. Treating segments as fixed truths
**What it looks like:** Building a two-year strategy on a segmentation run once.
**Fix:** Run stability checks, and plan a refresh (monthly or quarterly). Customers move between segments, and tracking those moves is often valuable in itself.

### 8. Segments with no action attached
**What it looks like:** A beautiful deck of personas, and nothing changes operationally.
**Fix:** Every segment gets a named owner and a specific action before you call the work done.

### 9. Mixing IDs or outcome columns into the inputs
**What it looks like:** Including "churned: yes/no" as a clustering input, then "discovering" a high-churn segment.
**Fix:** Cluster on behaviors and characteristics. Keep outcomes (churn, margin, NPS) **separate**, and use them only to profile segments afterward. That's how you find out whether segments genuinely differ in ways that matter.

---

## Small vs. Large Data: What Changes in This Module

| Aspect | Small data (30–300 rows) | Large data (thousands+) |
|---|---|---|
| Default method | Hierarchical clustering plus K-means as a cross-check | K-means (fast and scalable) |
| Number of segments | 2–4 | 3–6 typically; more only if each is actionable |
| Minimum segment size | At least ~10% of rows or 15–20 rows | At least a few percent; must be big enough to justify its own action |
| Number of inputs | 3–5 | 3–8 |
| Choosing K | Dendrogram + silhouette + judgment | Elbow + silhouette + judgment |
| DBSCAN | Use cautiously; few rows make density hard to judge | Useful for outlier detection |
| GMM | Usually avoid; unstable | Useful when segments overlap |
| Stability checks | **Essential.** Expect some instability; trust only what reappears consistently. | Still important; check over time periods too |
| Simple RFM baseline | Often the better final choice | Useful benchmark; clustering usually adds nuance |
| PCA chart | Every point is visible; inspect individual rows | Sample points for readability |
| Speed | Not an issue | Hierarchical clustering gets slow; use K-means or sample |
| Profiling | Can review every member of each segment by hand | Rely on summary tables and charts |

**Bottom line:** the process is identical, but small data calls for fewer, larger segments and a strong bias toward simple RFM rules unless clustering clearly adds value. Large data supports more segments and more methods, but business usability still decides how many you keep.

---

## Code Structure / Pipeline Outline

The segmentation workflow follows this outline:

```text
1. CONFIG CELL
   - path to cleaned data
   - ID column (kept for labeling, never clustered)
   - clustering input columns (3-8)
   - columns to log-transform
   - profile-only outcome columns (churn, margin, NPS, etc.)
   - K range to test (e.g., 2-10)
   - minimum segment size (e.g., 10% of rows or 20 rows)
   - data size mode: "small" | "large" (auto-suggested from row count)
   - random seed (for repeatable results)

2. LOAD DATA
   - load cleaned data
   - keep ID and profile-only columns aside

3. INPUT CHECKS
   - blanks, skew, and extreme values for each input
   - correlation table (flag pairs above ~0.8; keep one)
   - warning if more than 8 inputs are selected

4. PREP
   - log-transform skewed money/count inputs
   - cap extreme values (optional)
   - scale every input (standard or robust scaling)

5. RFM BASELINE (optional)
   - simple rule-based segments from recency, frequency, monetary
   - kept for comparison with the clustering result

6. CHOOSING K
   - elbow chart (with an automatic "bend" suggestion)
   - silhouette score for each K
   - small mode: dendrogram from hierarchical clustering
   - table: K, silhouette, smallest segment size, "too small?" flag

7. FINAL CLUSTERING
   - K-means with the chosen K (several random starts)
   - large mode: MiniBatchKMeans, or fit on a sample
     and assign the remaining rows
   - segment sizes (count and %)

8. PCA MAP
   - 2-D scatter colored by segment
   - share of variation the two axes capture
   - large mode: plot a sample for readability

9. PROFILES
   - average of each input per segment, in ORIGINAL units
   - index vs. overall average (100 = average)
   - profile-only outcomes per segment (churn, margin, NPS)
   - heat-map of the index table

10. SEGMENT RULEBOOK
    - shallow decision tree predicting segment from the inputs
    - printed rules ("spend over $2,000 and last order within 30 days")
    - how often the rules match the clustering (agreement %)

11. STABILITY CHECKS
    - re-run with different random seeds
    - re-run on random 80% subsets
    - agreement score (adjusted Rand index) for each re-run
    - flag segments that split, merge, or vanish

12. OUTPUT
    - segment assignment file: ID, segment number, segment name
    - profile table and persona sheet (name, description, action, owner)
    - saved scaler + model for scoring new customers
```

This outline matches the Module 6 notebook, [`07_clustering_segmentation.ipynb`](../notebooks/07_clustering_segmentation.ipynb) ([open in Colab](https://colab.research.google.com/github/DannyBarren/ML_for_Business/blob/main/notebooks/07_clustering_segmentation.ipynb)). Open it, edit the CONFIG cell, and run top to bottom.

---

## Hands-On Exercise

**Time:** about 2 hours

1. **Pick the decision first.** Write one sentence: "We will use these segments to decide ______." Choose 3–8 inputs that matter for that decision. (15 minutes)
2. **Build an RFM baseline** using simple rules. Note how many customers fall into each group. (15 minutes)
3. **Run the input checks and prep.** Log-transform skewed inputs, drop one of any highly overlapping pair, and scale. (15 minutes)
4. **Choose K** using the elbow chart, the silhouette scores, and (on small data) the dendrogram. Narrow it to two candidate values. (15 minutes)
5. **Fit both candidates** and build the profile tables in original units. Pick the one your team could actually use. (20 minutes)
6. **Profile the outcomes.** Do segments differ meaningfully in churn, margin, or satisfaction? (10 minutes)
7. **Generate the segment rulebook** with a shallow decision tree. Would a salesperson understand the rules? (10 minutes)
8. **Run the stability checks.** Mark each segment as stable, somewhat stable, or unstable. (10 minutes)
9. **Write a persona card** for each segment: a short name, two-sentence description, one specific action, and an owner. Compare against the RFM baseline. Did clustering add enough to justify itself? (10 minutes)

---

## Discussion and Reflection Questions

1. **Look at your segment profiles.** If you showed them to your sales or marketing team without names, would they recognize the groups? Which segment would surprise them, and is that surprise a genuine insight or a sign something is off?

2. **Your clustering produced a segment that is interesting but small**, say 4% of customers. Is it worth a dedicated action? What would you need to know about its value, cost to serve, or growth potential to decide?

3. **Customers move between segments over time.** Which movement would worry you most (for example, from "loyal high spenders" to "lapsing")? How could you track those moves each month or quarter, and who would act on them?

---

## What "Done" Looks Like

You've finished Module 6 when you have:

- [ ] A one-sentence statement of the decision the segments support
- [ ] 3–8 clustering inputs chosen, checked for overlap, transformed, and scaled
- [ ] An RFM or rule-based baseline for comparison
- [ ] A chosen number of segments, justified by the numbers **and** business usability
- [ ] Profile tables in original business units, including outcome columns used for profiling only
- [ ] A PCA map and a plain-English segment rulebook
- [ ] Stability checks run, with unstable segments flagged
- [ ] A persona card for every segment with a name, an action, and an owner

If simple RFM rules turn out to be just as useful as clustering, use them. Segments your team understands and acts on beat clever segments nobody uses.

**Next up:** Module 7, Hyperparameter Tuning and Validation, where you'll learn how to get honest scores and tune models without fooling yourself, especially when data is scarce.
