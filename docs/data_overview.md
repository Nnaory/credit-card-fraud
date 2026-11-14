# Dataset Overview

## Source

Kaggle Playground Series S6E11 - Credit Card Fraud Detection Dataset.

## Files

- `train.csv`: Training dataset containing 26 features including the target label `is_fraud` (approx. 93.97 MiB).
- `test.csv`: Evaluation dataset containing 25 features without the target label (approx. 39.17 MiB).
- `sample_submission.csv`: Benchmark submission template containing `id` and placeholder prediction probability `is_fraud` (approx. 8.01 MiB).

## Dataset Dimensions

- **Training Set (`train.csv`)**: 699,960 rows × 26 columns
- **Test Set (`test.csv`)**: 299,984 rows × 25 columns
- **Sample Submission (`sample_submission.csv`)**: 299,984 rows × 2 columns

## Target

- **Name**: `is_fraud`
- **Type**: Binary indicator (`float64` / boolean classification target)
- **Classes**:
  - `0.0` (Legitimate): 688,271 records (98.33%)
  - `1.0` (Fraudulent): 11,689 records (1.67%)
- **Imbalance Ratio**: Approximately 58.88 : 1 (severe class imbalance requiring stratified splits and PR-AUC / ROC-AUC metric alignment).

## Feature Overview

A total of 25 input features and 1 target label are categorized as follows:

- **Identifier (1 feature)**:
  - `id`: Unique record identifier for each transaction (`int64`, 100% unique cardinality).
- **Numerical Features (11 features)**:
  - Continuous: `amount_usd`, `hours_since_last_txn`, `distance_from_home_km`, `account_balance_usd`, `velocity_score`, `merchant_risk_score`.
  - Discrete counts: `txn_count_last_24h`, `card_age_months`, `customer_age`, `cvv_retry_count`, `prior_disputes`.
- **Cyclical Temporal Features (2 features)**:
  - `time_of_day_hour`: Integer hour bounded within `[0, 23]` (requires sine/cosine cyclical encoding).
  - `day_of_week`: Integer day bounded within `[0, 6]` (requires sine/cosine cyclical encoding).
- **Categorical Features (5 features)**:
  - `merchant_category`: Merchant industry sector (12 unique values).
  - `card_type`: Payment card brand/tier (5 unique values).
  - `auth_method`: Transaction authentication mechanism (5 unique values).
  - `channel`: Transaction origination channel (5 unique values).
  - `device_type`: User device/operating system platform (8 unique values).
- **Boolean Features (6 features)**:
  - `is_foreign_transaction`: Flag for cross-border transactions (`bool`).
  - `is_new_merchant`: Flag for unfamiliar or newly encountered merchant (`bool`).
  - `used_vpn`: Flag for VPN or proxy IP masking (`bool`).
  - `ip_country_mismatch`: Flag for discrepancy between billing country and IP location (`bool`).
  - `billing_shipping_mismatch`: Flag for address discrepancy between billing and shipping (`bool`).
  - `is_ai_generated_scam_attempt`: Flag for automated or AI-generated scam detection signals (`bool`).

## Data Integrity

- **Missing values**: 0 missing values across all columns in both training and test datasets (100% complete data).
- **Duplicate rows**: 0 duplicate records found in either partition.
- **Duplicate IDs**: 0 duplicate transaction identifiers (`id` is strictly unique).

## Initial Observations

1. **Clean Baseline Integrity**: The dataset exhibits pristine completeness with zero null entries, duplicate records, or corrupted data types, eliminating the requirement for complex missing-value imputation.
2. **Periodic Temporal Nature**: `time_of_day_hour` and `day_of_week` follow closed periodic cycles. Standard linear tree splits or linear weight assignments will fail to capture midnight and weekend continuity without trigonometric encoding.
3. **Severe Class Imbalance**: Fraud accounts for only ~1.67% of the training distribution. Modeling strategies must employ Stratified K-Fold validation and evaluate against Area Under the Precision-Recall Curve (PR-AUC) and ROC-AUC rather than accuracy.
4. **Leakage & ID Handling**: Column `id` has perfect 1:1 cardinality with rows and must be dropped from feature vectors during model training to avoid overfitting to arbitrary sequence IDs.
