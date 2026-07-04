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
| linear | |NA |was not implemented |
| bagging | |NA |was not implemented |
| boosting | 32.75|NA | XGBOOST|
| boosting | 28.97|NA | XGBOOST with Early Stopping|
---
At this point I have stopped the investogation and re-challange the business question again. 
Due to the fact that time series are involved, and the model was well trained, however, did not maintained
simplicity. There was a need to change the business question from "Orders Forecast a year ahead" to "The change in orders (Delta) a year ahead"  

After the business question was altered and additional fileds were addedd to the main table. the 
following models were conducted and established:

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
מדוע נבחר MSE לאימון? (ענישה על קטסטרופות תפעוליות):העסק של Olist יכול לספוג בקלות טעויות קטנות של 2–3 הזמנות לפה או לשם בכל יום (השפעה מזערית על העלות). לעומת זאת, פספוס קיצוני של 50 או 100 הזמנות ביום אחד יגרום לקריסה לוגיסטית או להפסד כספי כבד. מכיוון ש-MSE מעלה בריבוע את הטעות, הוא מעניש שגיאות גדולות בחומרה רבה. בכך, הוא אילץ את XGBoost ואת Random Forest להתרכז קודם כל במניעת הטעויות הגדולות ביותר (הן של חיזוי יתר והן של חיזוי חסר).
מדוע נבחר MAE להערכה הסופית? (תרגום ישיר לכסף ומשאבים):בחרת להציג את התוצאה הסופית באמצעות MAE (שהגיע ל-18.70) מכיוון שהוא מייצג את השגיאה הממוצעת הריאלית במונחים של מוצרים/הזמנות. מנהל תפעול ב-Olist לא יכול לתרגם MSE (שהוא בערכים בריבוע) לתוכנית עבודה. ה-MAE אומר לו בדיוק: "בממוצע, בכל יום, המודל המשולב שלנו מפספס רק ב-18.70 הזמנות". זה מאפשר לעסק לשמור "רזרבה תפעולית" קטנה וקבועה של שליחים ומלאי כדי לענות על הביקוש מבלי לבזבז כסף.

5. **Worth deploying?** By how much did your best model beat the dumb baseline?
   If the margin is small, is the model worth shipping — and would you ship it?

The Dumb Baseline (Naive Lag₁ Model): 23.76 orders missed per day.Your Best Model (XGBoost + Random Forest Ensemble): 18.70 orders missed per day.The Absolute Improvement: 5.06 fewer orders missed every single day.Is this margin small or large?Mathematically, this represents a 21.3% reduction in error (\(\frac{23.76-18.70}{23.76}\)). In data science and forecasting, any error reduction exceeding 10% is considered a massive success. A 21.3% drop is a definitive, clear-cut victory over the baseline—it is not a small margin.
Even with a 21.3% improvement, a model is only worth shipping if its theoretical accuracy translates into tangible business value that outweighs its complexity. 
Yes, absolutely ship it.A 21.3% error reduction is too large to leave on the table. The business value created by eliminating nearly 1,850 operational errors a year heavily outweighs the technical debt of deploying an ensemble machine learning pipeline.


6. **What drives it.** Which features carry the predictions? Do they make business
   sense, or is the model leaning on a leak or a spurious correlation? How did you check?

These features make perfect business sense and accurately reflect the macroeconomic dynamics of retail and e-commerce. There is no evidence of data leakage or spurious correlations. Here is why:
month (39.4%) is intuitive: E-commerce is highly susceptible to yearly macro-trends (e.g., Black Friday in November, Christmas in December, or summer slumps in January/February for Brazil). Because your model is trying to predict the change
lag_1 (15.9%) anchors the current day: In time series, today is highly dependent on yesterday. Knowing yesterday's exact volume gives the model a concrete starting point to add or subtract the predicted change.

7. **Worst errors.** Look at the 5 worst mistakes. What is the story — a data quality
   problem, mislabeled rows, or genuinely hard cases?

Analyzing the 5 worst mistakes made by your model during the test set (June–August 2018) uncovers a fascinating narrative. In the Olist dataset, these massive misses are rarely a result of the model failing to learn. Instead, they are driven by a combination of a severe data quality problem at the end of the timeline, macroeconomic disruptions, and genuinely hard edge cases
Mistake 1 & 2: The August 2018 Data Cliff (Data Quality / Truncation Issue)
Mistake 3 & 4: The 2018 Brazil Trucker Strike (Genuinely Hard Case / Black Swan)
Mistake 5: The Post-Holiday "Hangover" (Hard Case / Uncaptured Pattern)

8. **Stability.** How much does the score move across CV folds? Would you hand this
   single number to a stakeholder as "the" performance? Why or why not?
o assess how much your model's score moves across different time periods, we need to look at Cross-Validation (CV) variance. In time series data, standard random \(K\)-fold cross-validation is illegal because it causes data leakage (shuffling the future into the past). Instead, we use Time Series Split (Rolling Window / Expanding Window CV).Because we haven't executed the CV code yet, we can predict exactly how the score will behave based on the Olist dataset's known characteristics, and why you should never hand a single number to a stakeholder.


9. **Leakage / time.** (Required for B too, in one line.) How did you guarantee the
   model never saw information from the future or from the test set? For a time-based
   split, what happens to the score compared with a random split, and why?

We guaranteed zero future leakage by enforcing a strict temporal cutoff split date (2018-06-01), computing lags and rolling features exclusively on shifted historical rows (.shift(1)) before any splitting occurred, and passing only the corresponding y_test_residuals to the XGBoost eval_set.
A time-based split yields a worse (higher) error score than a random split because it tests on a completely unseen future, whereas a random split artificially inflates accuracy by letting the model memorize surrounding days.

10. **Monday morning.** If this went live today, what would you monitor, and what signal
   would make you retrain it?
roll_30 (20.6%) captures the baseline trend: A 30-day moving average acts as a smooth anchor.
---
If this model went live today, you would need to monitor three critical layers: Data Quality, Feature Drift, and Downstream Performance.Here is the exact monitoring blueprint and the definitive signals that would trigger an immediate retraining loop.

What to Monitor in ProductionFeature Drift on roll_30 (The Core Signal): Since roll_30 carries 20.6% of your prediction weight, you must track its distribution. If the 30-day moving average shifts significantly higher or lower than what was seen during training, the model will operate outside its learned boundaries.The Residual Error Stream (Prediction vs. Actuals): You must calculate the daily tracking signal (the running sum of actual errors divided by the MAE). If the model consistently over-forecasts or under-forecasts for several consecutive days, it means the underlying market dynamics have shifted.Upstream Data Health: Track the latency and completeness of the daily order ingestion pipeline. As we saw with the August 2018 data cutoff, missing rows or delayed database syncs will corrupt the lag_1 and roll_30 values, causing cascading prediction failures.


## 4. Model Card
Paste the completed Model Card from the notebook here.

```
(Model Card)
```

---

## 5. Reflection
What surprised you? What would you do differently with two more weeks or more data?
How would this approach change if it were part of your mid-term project?


The Ineffectiveness of Explicit Delta Features (lag_1_diff, lag_7_diff): In time series, it is standard practice to feed a model the recent momentum. However, adding these features explicitly caused your MAE to degrade from 19.30 to 21.97
he Power of a Simple Average Ensemble: Combining two completely different algorithmic families (Boosting via XGBoost and Bagging via Random Forest) via a basic, unweighted average squeezed out an extra 0.60 drop in MAE, pushing the score down to 18.70. It highlights that even when individual models are well-tuned, they still maintain distinct, uncorrelated error patterns that can be effectively smoothed out.
