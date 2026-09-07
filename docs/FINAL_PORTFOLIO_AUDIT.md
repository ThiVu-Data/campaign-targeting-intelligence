# Final Portfolio Audit — Campaign Targeting Intelligence
## Status
GREEN-LIGHT for portfolio publication.

## Checks
- UCI Bank Marketing, DOI 10.24432/C5K306, CC BY 4.0; attribution included.
- No employer or confidential company data.
- 41,188 rows; 20 source inputs + outcome; 4,640 positive records.
- `duration` excluded from the pre-contact model and used only for the leakage benchmark.
- Earlier 80% train / later 20% test.
- Baseline Logistic Regression selected by PR-AUC.
- Top-K results use cumulative capture and cumulative lift. Non-monotonic lift values are retained as observed test-set results, not smoothed or corrected.
- Explainability is aligned to Logistic Regression; XGBoost feature importance is removed from the final package.
- Correlated macroeconomic features are flagged for cautious coefficient interpretation.
- Holdout testing is a future measurement design, not a claimed observed result.
- No invented revenue, campaign cost, margin, ROI or causal uplift.
