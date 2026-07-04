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
| Hybrid | 18.70|XGBoost + Random Forest|

## 3. Guiding questions (graded)
Answer each in 2-5 sentences.

1. **Accuracy trap.** Why is plain accuracy misleading for your problem, and what did
   the confusion matrix (or the error distribution, for forecasting) reveal that accuracy hid?

2. **Cost of errors.** What does a false positive cost vs a false negative in your
   business context, and how did that drive your metric choice?

3. **Worth deploying?** By how much did your best model beat the dumb baseline?
   If the margin is small, is the model worth shipping — and would you ship it?

4. **What drives it.** Which features carry the predictions? Do they make business
   sense, or is the model leaning on a leak or a spurious correlation? How did you check?

5. **Worst errors.** Look at the 5 worst mistakes. What is the story — a data quality
   problem, mislabeled rows, or genuinely hard cases?

6. **Stability.** How much does the score move across CV folds? Would you hand this
   single number to a stakeholder as "the" performance? Why or why not?

7. **Leakage / time.** (Required for B too, in one line.) How did you guarantee the
   model never saw information from the future or from the test set? For a time-based
   split, what happens to the score compared with a random split, and why?

8. **Monday morning.** If this went live today, what would you monitor, and what signal
   would make you retrain it?

---

## 4. Model Card
Paste the completed Model Card from the notebook here.

```
(Model Card)
```

---

## 5. Reflection
What surprised you? What would you do differently with two more weeks or more data?
How would this approach change if it were part of your mid-term project?
