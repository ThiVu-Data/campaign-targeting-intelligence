# Campaign Targeting Intelligence

**Data Analyst Portfolio Project | Python • Machine Learning • Customer Analytics • Business Decision Support**

## Business question
**If the campaign team can only contact a limited share of customers, who should be contacted first?**

The project ranks customers by predicted subscription probability, then turns those scores into contact-capacity scenarios such as Top 5%, 10%, 20% and 30%.

## Data source
**UCI Machine Learning Repository — Bank Marketing**

- Creators: S. Moro, P. Rita, P. Cortez
- DOI: `10.24432/C5K306`
- File used: `bank-additional-full.csv`
- Records: **41,188**
- Source fields: **20 inputs + outcome**
- Period: **May 2008 – November 2010**
- License: **CC BY 4.0**

Citation: Moro, S., Rita, P., & Cortez, P. (2014). *Bank Marketing* [Dataset]. UCI Machine Learning Repository. https://doi.org/10.24432/C5K306

## Analysis approach

### Use information available before contact
`duration` is excluded because call duration is only known after a call starts. A separate leakage check shows why including it would make the score look better without making the model usable for pre-call targeting.

### Test on a later period
The source file is ordered by date. The earlier 80% is used for training and the later 20% for testing.

- Train: **32,950 records**, subscription rate **6.4%**
- Test: **8,238 records**, subscription rate **30.8%**

The difference shows a clear shift between the earlier and later periods.

### Compare models for ranking
Baseline Logistic Regression, class-weighted Logistic Regression, Random Forest and XGBoost are compared on the later test period.

The strongest PR-AUC in this run is **baseline Logistic Regression**:
- PR-AUC: **0.527**
- ROC-AUC: **0.748**

More complex models did not improve ranking performance on this test period.

### Turn scores into a contact decision

| Contact capacity | Customers | Subscribers captured | Capture rate | Cumulative lift |
|---:|---:|---:|---:|---:|
| 5% | 411 | 227 | 8.9% | 1.79x |
| 10% | 823 | 469 | 18.5% | 1.85x |
| 20% | 1,647 | 979 | 38.5% | 1.93x |
| 30% | 2,471 | 1,362 | 53.6% | 1.79x |

Each row is cumulative from the top of the ranked list. Cumulative lift does not have to decrease smoothly on a finite test set because predicted scores do not perfectly order actual outcomes.

At **20% contact capacity**, the ranked list contains **1,647 customers** and captures **38.5% of subscribers** in the test period.

## Explainability
The final model is Logistic Regression, so the explainability file uses **Logistic Regression coefficients**.

Coefficients describe associations inside the fitted model, not causal effects. Some macroeconomic features are highly correlated, so individual coefficients should be interpreted cautiously.

## Campaign measurement
The model estimates subscription propensity. It does not prove that a campaign caused a subscription.

For a future randomized campaign:

**Incremental subscription rate = Target group rate − Holdout group rate**

This project does not claim causal uplift or ROI because the source data does not contain a randomized treatment/control design, campaign cost or customer value.

## Limitations
- Historical Portuguese bank telemarketing data may not transfer directly to another market.
- The earlier and later periods have a large distribution shift.
- Propensity prediction is not causal uplift modeling.
- Campaign cost and customer value are not available in the source data.
- Model performance should be monitored as customer behavior changes.

## Repository structure
```text
├── README.md
├── data/
│   ├── bank-additional-full.csv
│   └── bank-additional-names.txt
├── notebooks/
│   └── campaign_targeting_intelligence.ipynb
├── presentation/
│   └── Campaign_Targeting_Intelligence_Portfolio_Final.pptx
├── docs/
│   ├── DATA_SOURCE_AND_LICENSE.txt
│   ├── FINAL_PORTFOLIO_AUDIT.md
│   ├── model_results_temporal_test.csv
│   ├── contact_capacity_scenarios.csv
│   ├── logistic_regression_coefficients.csv
│   ├── macroeconomic_feature_correlations.csv
│   └── leakage_benchmark.csv
└── requirements.txt
```

## Portfolio summary
Built a campaign-targeting workflow using a licensed UCI dataset, temporal validation and pre-contact features; compared multiple models and translated predicted probabilities into ranked contact-capacity scenarios for business decision support.
