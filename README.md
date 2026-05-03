# Covid Mask Compliance Analysis

This repository contains a reproducible data preparation and modelling workflow for analysing predictors of COVID-19 protective behaviour compliance before and after sustained mask mandates. The current implementation uses Australia as the initial case study and is designed to support later cross-national extension.

# Statement

This project builds on the methodological framework of Ryan et al. (2025). In particular, several parts of the data preprocessing, policy-period definition, outcome construction, and model-selection strategy follow their approach. This repository adapts that framework for the current research design by organising the workflow into a reproducible pipeline, applying it to the Australian case study, comparing multiple classification models, and preparing the structure for later cross-national extension.



## Project aim

This project compared whether the predictive factors influencing public compliance with mask mandates differed across countries and time periods before and after their implementation. The analysis results consisted of two binary variables:

1. Face mask wearing compliance.
2. Broader protective behaviour compliance.

Four classification models are compared:

- Logistic regression
- Classification tree
- Random forest
- XGBoost

Model selection is based mainly on cross-validated ROC AUC, followed by held-out test evaluation.

## Repository structure

```text
.
├── Data/
│   ├── australia.csv
│   └── OxCGRT_AUS_latest.csv
├── Cleaned Data/
│   ├── cleaned_data.csv
│   ├── cleaned_data_preprocessing.csv
│   ├── cleaned_data_preprocessing_renamed.csv
│   ├── mandate_start_dates.csv
│   ├── missing_value_counts.csv
│   └── Splits/
│       ├── X_train_before_mask.csv
│       ├── X_test_before_mask.csv
│       ├── y_train_before_mask.csv
│       ├── y_test_before_mask.csv
│       ├── X_train_after_mask.csv
│       ├── X_test_after_mask.csv
│       ├── y_train_after_mask.csv
│       ├── y_test_after_mask.csv
│       ├── X_train_before_protective.csv
│       ├── X_test_before_protective.csv
│       ├── y_train_before_protective.csv
│       ├── y_test_before_protective.csv
│       ├── X_train_after_protective.csv
│       ├── X_test_after_protective.csv
│       ├── y_train_after_protective.csv
│       ├── y_test_after_protective.csv
│       └── split_summary.csv
├── Result/
│   ├── table_cv_results.csv
│   ├── table_cv_results_full.csv
│   ├── table_test_results.csv
│   ├── selected_model_summary.csv
│   ├── auc_pivot.csv
│   ├── f1_pivot.csv
│   ├── roc_selected_models.png
│   ├── roc_all_models_comparison.png
│   ├── feature_importance_include_state.png
│   ├── feature_importance_no_state.png
│   ├── table_feature_importance_include_state.csv
│   └── table_feature_importance_no_state.csv
├── 01_Data Clean.ipynb.ipynb
├── 02_Column Names update.ipynb
├── 03_split.ipynb
├── 04_Mode_Logistic Regression.ipynb
├── 05_Model_Classification Tree.ipynb
├── 06_Model_Random Forest.ipynb
├── 07_Model_XGBoost.ipynb
└── ALL.ipynb
```

## Notebook workflow

Run the notebooks in the following order.

### 1. `01_Data Clean.ipynb.ipynb`

Processes the raw Australian survey data and OxCGRT policy data. Main tasks include:

- Loading `australia.csv` and `OxCGRT_AUS_latest.csv`.
- Identifying state-specific sustained mask mandate start dates from the OxCGRT H6 face-covering indicator.
- Counting missing values.
- Cleaning invalid and missing responses.
- Standardising dates, state identifiers, and survey variables.
- Creating the preprocessed analytical dataset.

Main outputs:

- `Cleaned Data/mandate_start_dates.csv`
- `Cleaned Data/missing_value_counts.csv`
- `Cleaned Data/cleaned_data.csv`
- `Cleaned Data/cleaned_data_preprocessing.csv`

### 2. `02_Column Names update.ipynb`

Renames variables into more readable labels for modelling, reporting, and interpretation.

Main outputs:

- Variable-name mapping file.
- `Cleaned Data/cleaned_data_preprocessing_renamed.csv`

### 3. `03_split.ipynb`

Builds four modelling datasets by combining two outcomes with two policy periods:

- Before mandate - face mask wearing
- After mandate - face mask wearing
- Before mandate - protective behaviour
- After mandate - protective behaviour

Each task is split into 80% training data and 20% test data using stratification.

Main outputs:

- Train/test predictor files in `Cleaned Data/Splits/`
- Train/test target files in `Cleaned Data/Splits/`
- `Cleaned Data/Splits/split_summary.csv`

### 4. `04_Mode_Logistic Regression.ipynb`

Fits logistic regression models for the four modelling tasks. Logistic regression is used as the baseline interpretable linear classifier.

### 5. `05_Model_Classification Tree.ipynb`

Fits classification tree models. Hyperparameters are selected using randomized search with stratified cross-validation.

### 6. `06_Model_Random Forest.ipynb`

Fits random forest models. Hyperparameters are selected using randomized search with stratified cross-validation.

### 7. `07_Model_XGBoost.ipynb`

Fits XGBoost models. Hyperparameters are selected using randomized search with stratified cross-validation, and feature importance values are extracted for interpretation.

### 8. `ALL.ipynb`

Runs the integrated modelling and result-generation workflow. This is the main notebook for reproducing the reported tables and figures.

Main outputs include:

- Cross-validation comparison table
- Selected held-out test performance table
- ROC curves for selected XGBoost models
- ROC curves comparing all four algorithms
- Feature-importance figures with and without state indicators
- Feature-importance summary tables

## Data sources

The project combines:

- Imperial College London/YouGov COVID-19 Behaviour Tracker survey data.
- Oxford COVID-19 Government Response Tracker policy data.

The survey data provide individual-level behavioural, demographic, health, wellbeing, trust, and risk-perception variables. The OxCGRT H6 face-covering indicator is used to define sustained mask-mandate periods.

## Modelling details

The workflow uses four binary classification tasks:

| Task name | Outcome | Policy period |
|---|---|---|
| `before_mask` | Face mask wearing | Before sustained mandate |
| `after_mask` | Face mask wearing | After sustained mandate |
| `before_protective` | Broader protective behaviour | Before sustained mandate |
| `after_protective` | Broader protective behaviour | After sustained mandate |

Class imbalance is handled using random oversampling on the training data only. The test data are not resampled.

Evaluation metrics:

- ROC AUC
- Precision
- Recall
- Accuracy
- F1 score

ROC AUC is used as the primary model-selection metric.

## Reproducibility

The random seed is set to my student ID:

```python
RANDOM_STATE = 1948883
```

The workflow uses:

- Stratified train-test splitting
- Stratified 5-fold cross-validation
- Training-only oversampling
- Held-out test evaluation

## Required Python packages

The notebooks require the following main packages:

```text
numpy
pandas
matplotlib
scikit-learn
imbalanced-learn
xgboost
ipython
```



## How to reproduce the analysis

1. Place the raw files in the `Data/` folder:
   - `australia.csv`
   - `OxCGRT_AUS_latest.csv`

2. Run the notebooks:
   - `01_Data Clean.ipynb.ipynb`
   - `02_Column Names update.ipynb`
   - `03_split.ipynb`
   - `ALL.ipynb`

3. 4 models:
   - `04_Mode_Logistic Regression.ipynb`
   - `05_Model_Classification Tree.ipynb`
   - `06_Model_Random Forest.ipynb`
   - `07_Model_XGBoost.ipynb`

4. Check the generated outputs in the `Result/` folder.

## Key findings


- XGBoost gives the strongest cross-validated ROC AUC across the four modelling tasks.
- Predictive performance is higher after sustained mask mandates than before mandates.
- State indicators carry substantial predictive information, showing that regional policy timing and local public health context played an important role.
- After excluding state indicators, the main predictors still changed before and after mandates.
- For face mask wearing, the strongest non-state predictor was non-mask protective behaviour in both periods, but its importance decreased after mandates. Survey timing and willingness to self-isolate became more important after mandates.
- For broader protective behaviour, the strongest non-state predictor shifted from physical contact count and perceived COVID severity before mandates to willingness to self-isolate after mandates.

