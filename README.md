# Campaign Targeting Intelligence

Ranking bank customers before contact and translating model scores into a practical contact list under limited campaign capacity.

## Business Question

If the campaign team can contact only a limited share of customers, who should be contacted first?

This project uses historical bank telemarketing data to rank customers before contact and evaluate how effectively the ranking concentrates likely subscribers within different contact capacity levels.

## Key Result

The later test period had a **30.8% positive rate**, providing a no-skill PR-AUC reference of approximately **0.308**.

Logistic Regression achieved a **PR-AUC of 0.527**, the highest among the four models evaluated.

At **20% contact capacity**, the top **1,647 customers** captured **38.5% of subscribers** observed in the test period, with a cumulative lift of **1.93x** over random contact.

## Data

| Field | Value |
| --- | --- |
| Dataset | Bank Marketing |
| Source | UCI Machine Learning Repository |
| File used | `bank-additional-full.csv` |
| Observations | 41,188 |
| Period | May 2008 to November 2010 |
| Inputs | 20 |
| Target | Term deposit subscription |
| Positive rate | 11.3% |
| License | CC BY 4.0 |

The dataset contains customer profile, contact context, campaign history and macroeconomic information.

`duration` was excluded from the final targeting model because call duration is only known after a call starts. It would therefore not be available when customers are scored for a pre contact campaign list.

## Methodology

1. Audited the source data, feature distributions and target class balance.
2. Retained `unknown` as a distinct category rather than treating it as missing data.
3. Preserved temporal ordering by using the earlier 80% of observations for training and the later 20% for testing.
4. Excluded `duration` because call duration is unavailable when customers are scored for pre contact targeting.
5. One hot encoded categorical variables and standardized numeric variables for Logistic Regression.
6. Compared Logistic Regression (baseline model), Logistic Regression (class weighted), Random Forest (class weighted) and XGBoost (class weighted).
7. Used PR-AUC as the primary model selection metric because the target class is imbalanced.
8. Ranked customers from highest to lowest predicted probability of subscription.
9. Evaluated cumulative targeting performance at the top 5%, 10%, 20% and 30% of the ranked customer list.

## Model Comparison

| Model | PR-AUC |
| --- | ---: |
| No-skill reference | 0.308 |
| **Logistic Regression (baseline model)** | **0.527** |
| Logistic Regression (class weighted) | 0.521 |
| Random Forest (class weighted) | 0.490 |
| XGBoost (class weighted) | 0.412 |

Logistic Regression produced the highest PR-AUC on the later test period and was selected as the final ranking model.

## Contact Capacity

| Capacity | Customers | Subscribers Captured | Cumulative Lift |
| --- | ---: | ---: | ---: |
| Top 5% | 411 | 8.9% | 1.79x |
| Top 10% | 823 | 18.5% | 1.85x |
| Top 20% | 1,647 | **38.5%** | **1.93x** |
| Top 30% | 2,471 | 53.6% | 1.79x |

Each row is cumulative from the top of the ranked customer list.

The Top 20% scenario illustrates the capacity tradeoff. The contact list is reduced to 1,647 customers while retaining 38.5% of the subscribers observed in the test period.

**Ranking stability.** Cumulative lift is not monotonic across the evaluated capacity points, with Top 5% lift below Top 20%. Performance at the very top of the ranked list should therefore be interpreted cautiously.

## Operational Workflow

**Eligibility → Score → Rank → Select → Holdout → Measure**

Business and compliance rules are applied first. Eligible customers are then scored and ranked from highest to lowest predicted probability. The campaign team can select the top group according to available contact capacity.

A randomized holdout group would be required to measure incremental campaign impact. The current model estimates subscription propensity; it does not establish that contacting a customer causes the subscription.

A future campaign could measure:

`Incremental subscription rate = Target group rate − Holdout group rate`

## Limitations

**Temporal shift.** The data comes from historical bank telemarketing campaigns, and customer behavior may change over time. The subscription rate also differs substantially between the earlier training period and the later test period.

**Propensity, not uplift.** The model predicts subscription probability rather than the incremental effect of contact. A randomized experiment would be required to estimate campaign uplift.

**No cost or customer value data.** Campaign costs and customer value are not available in the dataset, so this project does not estimate ROI or profit.

**Correlated macroeconomic features.** Some macroeconomic variables are highly correlated. Logistic Regression coefficients are useful for understanding the model score, but they should not be interpreted as causal effects.

**Single temporal split.** Performance is evaluated on one out of time test period. Small differences between model results should therefore be interpreted cautiously.

**Probability calibration.** The positive rate differs substantially between the training and test periods. The model is used for ranking in this analysis; predicted probabilities should not be treated as calibrated conversion probabilities without additional validation.

## Project Files

`notebooks/campaign_targeting_intelligence.ipynb`  
Main analysis with saved outputs.

`presentation/Campaign_Targeting_Intelligence.pptx`  
Business presentation of the analysis and recommendations.

`docs/model_results_temporal_test.csv`  
Model comparison results on the temporal test period.

`docs/contact_capacity_scenarios.csv`  
Top 5%, 10%, 20% and 30% contact capacity results.

`docs/logistic_regression_coefficients.csv`  
Logistic Regression coefficients from the final model.

`docs/macroeconomic_feature_correlations.csv`  
Correlation check for macroeconomic variables.

`docs/leakage_benchmark.csv`  
Benchmark showing the effect of including `duration`.

`docs/DATA_SOURCE_AND_LICENSE.txt`  
Dataset source, citation and license information.

## Tools

Python, pandas, scikit-learn, XGBoost, matplotlib and Jupyter Notebook.

## References

Moro, S., Rita, P., & Cortez, P. (2014). *Bank Marketing* [Data set]. UCI Machine Learning Repository. https://doi.org/10.24432/C5K306

Moro, S., Cortez, P., & Rita, P. (2014). *A Data-Driven Approach to Predict the Success of Bank Telemarketing.* Decision Support Systems, 62, 22–31. https://doi.org/10.1016/j.dss.2014.03.001

## Profile

**Thi Vu**  
Data Analyst

LinkedIn: https://www.linkedin.com/in/thivu-data/  
GitHub: https://github.com/ThiVu-Data
