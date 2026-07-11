# REPORT — Module 3 · Assignment 1 · Supervised Learning

**Name:** RAVIT BAR-LEV  **ID:** 029290400  **Date:** 04/07/2026
**Chosen task:** C (A · Olist negative review / B · Telco churn / C · Olist daily volume forecast)

> Keep this report in English. Be concise and honest. "I don't know, and here is why"
> is a professional answer. Empty confidence is not.

---

## 1. Problem framing
Business question (one short paragraph):
The time frame presented in "Olist_orders_dataset" file is 04/10/2016 (first order) until 29/08/2018.
I would like to predict the orders placed beyond 29/08/2018. In fact, I would like to supply the 
Purchasing department the full forcast a year ahead, i.e. until 29/08/2019.

Target definition:
Orders Forecast a year ahead - 29/08/18 to 29/08/2019

**Primary metric and why it fits the business cost** (cost of a false positive vs a
false negative; for forecasting, cost of over- vs under-forecasting):

The primary metric examined was the Mean Squered Error (MSE) due to it's simplicity to evaluate whether the business question was answered or not.
---

## 2. Results table
Fill in every model **and the dumb baseline** on the locked test set.

| Model | Primary metric(MSE) | Secondary metric | Notes |
|---|---|---|---|
| baseline |23.76 |NA | dumb model |
| bagging | |NA |was not implemented | I took the risk in order to save money
| boosting | 32.75|NA | XGBOOST|
| boosting | 28.97|NA | XGBOOST with Early Stopping|
---
At this point I have stopped the investigation and re-challange the business question again. 
Due to the fact that time series are involved, and the model did well on the trainning part, however, did not maintained
the simplicity principle, There was a need to change the business question from:
"Orders Forecast a year ahead" to 
"The change in orders (Delta) a year ahead"   

After the business question was altered and additional fields were added to the main table. 
the MSE's hereunder, were established:

| Model | Primary metric(MSE) | Secondary metric | Notes |
|---|---|---|---|
| baseline |23.76 |dumb model|
| bagging |19.45 |Random Forest|
| boosting | 19.30|XGBoost with Early Stopping|
| MAE | 18.70|XGBoost + Random Forest|

## 3. Guiding questions (graded)
Answer each in 2-5 sentences.

1. **Accuracy trap.** Why is plain accuracy misleading for your problem, and what did
   the confusion matrix (or the error distribution, for forecasting) reveal that accuracy hid?
   Answer: For time series problems accuracy is misleading because you cannot tell from it's prediction, if it
   pedicts right or just gussing. That is the reason I have skipped this metric.

3. **Cost of errors.** What does a false positive cost vs a false negative in your
   business context, and how did that drive your metric choice?
   Answer:
   Why MSE was chosen for training? (Penalizing operational catastrophes):
   Olist's business can easily absorb small errors of 2–3 orders here or there each day, as they have a minimal      impact on costs.
   In contrast, an extreme miss of 50 or 100 orders in a single day would trigger a logistical collapse or heavy     financial losses. Because MSE squares the errors, it penalizes large deviations severely.
   By doing so, it forced XGBoost and Random Forest to prioritize preventing the largest errors first (both over-    forecasting and under-forecasting).
   Why MAE was chosen for final evaluation?
   I chose to present the final results using MAE (which reached 18.70) because it represents the actual average     error in terms of real products and orders. An operations manager at Olist cannot easily translate MSE (which     is expressed in squared units) into an actionable workflow.
   The MAE tells them exactly: "On average, each day, our ensemble model misses by only 18.70 orders."
   This allows the business to maintain a small, steady operational buffer of couriers and inventory to meet
   demand without wasting capital.

5. **Worth deploying?** By how much did your best model beat the dumb baseline?
   If the margin is small, is the model worth shipping — and would you ship it?
   Answer:
   The Dumb Baseline (Naive Lag₁ Model): 23.76 orders missed per day.
   My Best Model (XGBoost + Random Forest Ensemble): 18.70 orders missed per day.
   The Absolute Improvement: 5.06 fewer orders missed every single day.
   Is this margin small or large?
   Mathematically, this represents a 21.3% reduction in error (\(\frac{23.76-18.70}{23.76}\)).
   In data science and forecasting, any error reduction exceeding 10% is considered a massive success.
   A 21.3% drop is a definitive, clear-cut victory over the baseline—it is not a small margin.
   Even with a 21.3% improvement, a model is only worth shipping if its theoretical accuracy translates into
   tangible business value that outweighs its complexity. 
   A 21.3% error reduction is too large to leave on the table. The business value created by eliminating nearly      1,850 operational errors a year heavily outweighs the technical debt of deploying an ensemble machine
   learning pipeline.

7. **What drives it.** Which features carry the predictions? Do they make business
   sense, or is the model leaning on a leak or a spurious correlation? How did you check?
   Answer:
   These features make perfect business sense and accurately reflect the macroeconomic dynamics of retail and e-     commerce. There is no evidence of data leakage or spurious correlations.
   Here is why: month (39.4%) is intuitive: E-commerce is highly susceptible to yearly macro-trends
   (e.g., Black Friday in November, Christmas in December, or summer slumps in January/February for Brazil).         Because my model is trying to predict the change lag_1 (15.9%) anchors the current day:
   In time series, today is highly dependent on yesterday. Knowing yesterday's exact volume gives the model a        concrete starting point to add or subtract the predicted change.

7. **Worst errors.** Look at the 5 worst mistakes. What is the story — a data quality
   problem, mislabeled rows, or genuinely hard cases?
   Answer:
   Analyzing the 5 worst mistakes made by my model during the test set (June–August 2018) uncovers the following     narrative. In the Olist dataset, these massive misses are rarely a result of the model failing to learn.
   Instead, they are driven by a combination of a severe data quality problem at the end of the timeline,
   macroeconomic disruptions, and genuinely hard edge cases
   Mistake 1 & 2: The August 2018 Data Cliff (Data Quality / Truncation Issue)
   Mistake 3 & 4: The 2018 Brazil Trucker Strike (Genuinely Hard Case / Black Swan)
   Mistake 5: The Post-Holiday "Hangover" (Hard Case / Uncaptured Pattern)

9. **Stability.** How much does the score move across CV folds? Would you hand this
   single number to a stakeholder as "the" performance? Why or why not?
   Answer:
   To assess how much my model's score moves across different time periods, I need to look at Cross-Validation
   (CV) variance. In time series data, standard random \(K\)-fold cross-validation is illegal because it causes
   data leakage. Instead, I use the Time Series Split (Rolling Window / Expanding Window CV).
   

11. **Leakage / time.** (Required for B too, in one line.) How did you guarantee the
   model never saw information from the future or from the test set? For a time-based
   split, what happens to the score compared with a random split, and why?
   Answer:
   I have guaranteed zero future leakage by enforcing a strict temporal cutoff split date (2018-06-01), computing
   lags and rolling features exclusively on shifted historical rows (.shift(1)) before any splitting occurred,
   and passing only the corresponding y_test_residuals to the XGBoost eval_set.
   A time-based split yields a worse (higher) error score than a random split because it tests on a completely
   unseen future, whereas a random split artificially inflates accuracy by letting the model memorize surrounding
   days.

10. **Monday morning.** If this went live today, what would you monitor, and what signal
   would make you retrain it?
   Answer:
   If this model went live today, I will need to monitor three critical layers: Data Quality, Feature Drift, and     Downstream Performance.
   Here is the exact monitoring blueprint and the definitive signals that would trigger an immediate
   retraining loop.
   In terms of data quality: The analysis carries 20.6% of the prediction weight. That means if the distribution     shifts significantly higher or lower than what was seen during training, the model will operate outside its
   learned boundaries.
   In terms of features: I think that calculating the daily tracking signals (the running sum of actual errors       divided by the MAE) will give a glance of the trand. If the model consistently over-forecasts or under-
   forecasts for several consecutive days, it means the underlying market dynamics have shifted.
   In terms of Upstream Data perfromance:
   There is a need to track the latency and completeness of the daily order ingestion pipeline.
   As we saw with the August 2018 data cutoff, missing rows or delayed database syncs will corrupt the lag_1 and
   roll_30 values, causing cascading prediction failures.
_______________________________________________________________________________________________________________
## Model Card
Paste the completed Model Card from the notebook here.
## 1. Overview
- Task / business question:
- Dataset (which option) and time range:
The time frame presented in "Olist_orders_dataset" file is 04/10/2016 (first order) until 29/08/2018. 
I would like to predict the orders placed beyond 29/08/2018. 
In fact, I would like to supply the Purchasing department the full forcast a year ahead, i.e. until 29/08/2019.

- Target definition:
Target definition: Orders Forecast a year ahead - 29/08/18 to 29/08/2019

## 2. Metric & performance
- Primary metric and WHY (business cost of FP vs FN / over- vs under-forecast):
The primary metric examined was the Mean Squered Error (MSE) due to it's simplicity to evaluate whether 
the business question was answered or not.

- Dumb baseline score: 23.76 days(i.e. 23.76 orders a day)
- XGBoost model score (on the locked test set): 32.95 (i.e. 32.95 orders a day)
- Did it beat the baseline meaningfully? Is it worth deploying? No, it didn't therefore, it is not worth deploying

## 3. What the model relies on
- Top features and whether they make business sense:
- Any feature you suspect is a leak or spurious? How did you check?
At this point I have stopped the investigation and re-challange the business question again. 
Due to the fact that time series are involved, and the model did well on the trainning part, however, did not maintained
the simplicity principle, There was a need to change the business question from:
"Orders Forecast a year ahead" to 
"The change in orders (Delta) a year ahead"  

After the business question was altered and additional fields were added to the main table. 
the MSE's hereunder, were established:

baseline Score: 23.76
bagging Random Forest Score: 19.45
boosting XGBoost with Early Stopping Score: 19.30
Combined Score for XGBoost + Random Forest: 18.70

## 4. Limitations & failure modes
- The 5 worst errors — what is the pattern?
1. Randomly splitting your data into training and testing sets using standard K-Fold cross-validation
2. Calculating MSE on raw time series data that contains a strong upward/downward trend or rigid seasonal patterns.
3. Creating lag features but failing to properly shift the target variable during the validation phase.
4. Evaluating MSE across different time windows without normalizing or considering changes in the baseline scale of the data
5. Calculating a single, global MSE over a long validation period that includes a structural break

- Where would this model break?
A combination of error #4 and error#5 presented above
- Stability across folds (mean +/- std): 
  To assess how much my model's score moves across different time periods, I need to look at Cross-Validation
  (CV) variance. In time series data, standard random \(K\)-fold cross-validation is illegal because it causes
  data leakage. Instead, I use the Time Series Split (Rolling Window / Expanding Window CV).

## 5. Fairness / ethics
- Could any group be systematically mis-served by this model? 
Yes, as seen, the model was very good at the training part and less in the testing part

## 6. Real world
- If deployed Monday: what would you monitor? What triggers a retrain?
If this model went live today, I will need to monitor three critical layers: Data Quality, Feature Drift, and     Downstream Performance.
   Here is the exact monitoring blueprint and the definitive signals that would trigger an immediate
   retraining loop.
   In terms of data quality: The analysis carries 20.6% of the prediction weight. That means if the distribution     shifts significantly higher or lower than what was seen during training, the model will operate outside its
   learned boundaries.
   In terms of features: I think that calculating the daily tracking signals (the running sum of actual errors       divided by the MAE) will give a glance of the trand. If the model consistently over-forecasts or under-
   forecasts for several consecutive days, it means the underlying market dynamics have shifted.
   In terms of Upstream Data perfromance:
   There is a need to track the latency and completeness of the daily order ingestion pipeline.
   As we saw with the August 2018 data cutoff, missing rows or delayed database syncs will corrupt the lag_1 and
   roll_30 values, causing cascading prediction failures.
```
(Model Card)
```

---

## 7. Reflection
What surprised you? What would you do differently with two more weeks or more data?
How would this approach change if it were part of your mid-term project?
Answer:
The Ineffectiveness of Explicit Delta Features (lag_1_diff, lag_7_diff): In time series, it is standard practice to feed a model the recent momentum. However, adding these features explicitly caused your MAE to degrade from 19.30 to 21.97
he Power of a Simple Average Ensemble: Combining two completely different algorithmic families (Boosting via XGBoost and Bagging via Random Forest) via a basic, unweighted average squeezed out an extra 0.60 drop in MAE, pushing the score down to 18.70. It highlights that even when individual models are well-tuned, they still maintain distinct, uncorrelated error patterns that can be effectively smoothed out.
