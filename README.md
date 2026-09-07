# Campaign Targeting Intelligence

## Business Question

If the campaign team can only contact a limited share of customers, who should be contacted first?

This project uses historical bank marketing data to rank customers before contact and turn model scores into a practical contact list.

## Key Result

The baseline Logistic Regression achieved the highest PR-AUC at **0.527** on the later 20% temporal test set.

At **20% contact capacity**, the top **1,647 customers** captured **38.5% of actual subscribers**, with a cumulative lift of **1.93x**.

## Data

**Dataset:** Bank Marketing  
**Source:** UCI Machine Learning Repository  
**File used:** `bank-additional-full.csv`  
**Records:** 41,188  
**Period:** May 2008 to November 2010  
**License:** CC BY 4.0  
**DOI:** 10.24432/C5K306

Citation:

Moro, S., Rita, P., & Cortez, P. (2014). Bank Marketing [Dataset]. UCI Machine Learning Repository.

The dataset contains customer profile, contact context, campaign history and macroeconomic information.

`duration` was excluded from the final targeting model because call duration is only known after a call starts. It would not be available when the contact list is created.

## Approach

1. Audited the source data and target distribution.
2. Kept `unknown` as an explicit source category.
3. Used the earlier 80% of records for training and the later 20% for testing.
4. Excluded `duration` from the pre contact feature set.
5. One hot encoded categorical fields and scaled numeric fields for Logistic Regression.
6. Compared baseline Logistic Regression, class weighted Logistic Regression, Random Forest and XGBoost.
7. Used PR-AUC as the main model selection metric because the target is imbalanced.
8. Ranked customers by predicted subscription probability.
9. Evaluated contact capacity at the Top 5%, 10%, 20% and 30% of the ranked list.

## Model Comparison

| Model | PR-AUC |
| --- | ---: |
| Logistic Regression (baseline) | **0.527** |
| Logistic Regression (class weighted) | 0.521 |
| Random Forest (class weighted) | 0.490 |
| XGBoost (class weighted) | 0.412 |

Baseline Logistic Regression was selected as the final ranking model.

## Contact Capacity

| Capacity | Customers | Subscribers Captured | Cumulative Lift |
| --- | ---: | ---: | ---: |
| Top 5% | 411 | 8.9% | 1.79x |
| Top 10% | 823 | 18.5% | 1.85x |
| Top 20% | 1,647 | **38.5%** | **1.93x** |
| Top 30% | 2,471 | 53.6% | 1.79x |

Each row is cumulative from the top of the ranked list.

The Top 20% scenario is a useful example of how the model can support a capacity decision. It reduces the contact list to 1,647 customers while retaining 38.5% of the subscribers observed in the test period.

## How I Would Use the Model

A campaign team could use the model in the following order:

**Eligibility → Score → Rank → Select → Holdout → Measure**

Business and compliance rules would be applied first. Eligible customers would then receive a model score and be ranked from highest to lowest. The team could select the top group based on available contact capacity.

A random holdout group should be kept for measurement. The model predicts likelihood to subscribe, but it does not prove that the campaign caused the subscription.

A future campaign could measure:

`Incremental subscription rate = Target group rate - Holdout group rate`

## Limitations

The data comes from historical bank telemarketing campaigns, and customer behavior changes over time. The subscription rate is also substantially different between the earlier training period and the later test period.

The model predicts subscription probability rather than campaign uplift. Campaign cost and customer value are not available in the dataset, so this project does not estimate ROI or profit.

Some macroeconomic variables are highly correlated. Logistic Regression coefficients are therefore useful for understanding the model score, but they should not be interpreted as causal effects.

## Project Files

`notebooks/campaign_targeting_intelligence.ipynb`  
Main analysis with saved outputs.

`presentation/Campaign_Targeting_Intelligence_Portfolio_Final.pptx`  
Business presentation of the analysis and recommendations.

`docs/model_results_temporal_test.csv`  
Final model comparison results.

`docs/contact_capacity_scenarios.csv`  
Top 5%, 10%, 20% and 30% capacity results.

`docs/logistic_regression_coefficients.csv`  
Final Logistic Regression coefficients.

`docs/macroeconomic_feature_correlations.csv`  
Correlation check for macroeconomic variables.

`docs/leakage_benchmark.csv`  
Benchmark showing why `duration` should not be used in the deployable model.

`docs/DATA_SOURCE_AND_LICENSE.txt`  
Dataset source, citation and license information.

## Tools

Python, pandas, scikit-learn, XGBoost, matplotlib and Jupyter Notebook.

## Profile

**LinkedIn:** https://www.linkedin.com/in/thivu-data/  
**GitHub:** https://github.com/ThiVu-Data
