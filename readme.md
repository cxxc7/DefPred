"""
---------------------------------------------------------------
PROJECT SUMMARY – DEFECT PREDICTION & RISK ANALYSIS PIPELINE
---------------------------------------------------------------

This project focuses on:
1. Defect Prediction – identifying fault-prone modules using
   code metrics, process metrics, and change history.
2. Early Detection of High-Risk Components – automatically
   flagging commits/files likely to introduce bugs.

The project has two major components:

================================================================
1) MACHINE LEARNING DEFECT PREDICTION (PROMISE DATASETS)
================================================================
- Multiple PROMISE defect datasets (jm1, kc1, kc2, cm1, pc1)
  are downloaded automatically.
- A processing pipeline prepares the data:
        • detects defect label
        • keeps only relevant numeric metrics
        • handles missing values
        • applies scaling
        • applies SMOTE for imbalance (when available)

- Models used:
        • Logistic Regression
        • Decision Tree
        • Random Forest
        • SVM
        • (optionally) XGBoost

- For each dataset and each model, the script generates:
        • accuracy, precision, recall, F1
        • ROC-AUC score
        • confusion matrix (displayed inline)
        • ROC curve (displayed inline)
        • Precision-Recall curve
        • Classification report
        • Aggregated comparison bar charts

- A final comparison table is created showing
  which model performs best per dataset.

This part demonstrates how ML models trained on historical
software metrics can predict which modules are likely to be defective.

================================================================
2) CHANGE-HISTORY MINING USING PYDRILLER (REPO ANALYSIS)
================================================================
- The user inputs ANY GitHub repo URL.
- PyDriller mines the entire commit history and extracts
  real engineering metrics per commit:
        • total added lines
        • total deleted lines
        • churn per commit
        • cyclomatic complexity of changed files
        • number of methods changed
        • files modified count
        • developer experience (number of prior commits)
        • commit message length
        • commit timestamp

- All commits (not just top 5) are shown in a clean table.
- A CSV file (commit_metrics.csv) is generated for reuse.

This part captures important *process metrics* proven in
research to correlate with bugs.

================================================================
3) RISK SCORING & COMMIT-LEVEL DEFECT LIKELIHOOD
================================================================
- A custom evaluator computes a “risk score” for every commit.
- It normalizes and combines metrics like:
      • churn score
      • complexity score
      • files modified score
      • developer experience score
      • commit message score

- Weighted mathematically into a final RISK SCORE (0–1).
- Each commit is automatically classified as:
      LOW RISK (safe)
      MEDIUM RISK (needs review)
      HIGH RISK (likely fault-prone)

- Inline visualizations include:
      • distribution of risk scores
      • pie chart of risk categories
      • bar chart of top risky commits

- A final repository-level summary is generated:
      • % HIGH, MEDIUM, LOW risk commits
      • top risky commits
      • interpretation of overall safety

This part provides EARLY DETECTION of risky changes,
meeting the second objective of the project.

================================================================
OVERALL CONTRIBUTION
================================================================
Through the combination of:
    • static code metrics (PROMISE datasets),
    • process metrics (PyDriller computed),
    • ML defect prediction,
    • commit-level risk scoring,
the pipeline forms a complete, modern defect prediction system.

It identifies:
    - which modules/datasets are fault-prone (ML models)
    - which commits in a live repository introduce risk
    - where developers should focus testing and review efforts.

This satisfies the project topic:
“Defect Prediction – Predicting fault-prone modules using code
metrics, process metrics, and change history. Early detection
of high-risk components.”

---------------------------------------------------------------
"""




"""
================================================================
HOW CRISP-DM IS APPLIED IN THIS DEFECT PREDICTION PROJECT
================================================================

This project follows the CRISP-DM (Cross-Industry Standard Process
for Data Mining) methodology. Below is how each phase is addressed:

----------------------------------------------------------------
1) BUSINESS UNDERSTANDING
----------------------------------------------------------------
Goal:
    • Predict defect-prone modules and high-risk commits.
    • Help developers detect risky code changes early.
    • Reduce bugs by focusing reviews/testing on fault-prone areas.

Questions answered:
    • Which components are most likely to generate defects?
    • Which commits introduce risk?
    • Which ML models perform best for defect prediction?

----------------------------------------------------------------
2) DATA UNDERSTANDING
----------------------------------------------------------------
Two types of data are used:

A) Static Code Metrics (PROMISE datasets)
    - Metrics such as LOC, complexity, coupling, etc.
    - Past version-level defect labels.

B) Process & Change Metrics (PyDriller repository mining)
    - Commit history, churn, file changes, complexity per change,
      developer experience, message length.
    - Gives “behavioral” signals of risky changes.

Actions taken:
    • Explored dataset shapes.
    • Identified defect label.
    • Inspected commit-level metrics from GitHub repo.
    • Visualized and printed dataset samples.

----------------------------------------------------------------
3) DATA PREPARATION
----------------------------------------------------------------
Steps applied to PROMISE datasets:
    • Automatic label detection.
    • Removal of non-numeric attributes.
    • Handling missing values using median imputation.
    • Feature scaling using StandardScaler.
    • Applying SMOTE to balance imbalanced defect classes.
    • Dropped constant/irrelevant features.

Steps applied to repository data:
    • Extracted metrics across all commits.
    • Normalized churn, complexity, experience, etc.
    • Created combined risk features from change history.

----------------------------------------------------------------
4) MODELING
----------------------------------------------------------------
ML models trained on PROMISE datasets:
    • Logistic Regression
    • Decision Tree
    • Random Forest
    • SVM
    • XGBoost (optional)

For each model:
    • Train/test split with stratification.
    • Fit on scaled + balanced features.
    • Predictions generated for defect probability.

Heuristic risk scoring for commit-level history:
    • Weighted combination of churn, complexity, file changes,
      experience and message length.
    • Produces risk_score ∈ [0,1] for each commit.
    • Classifies commits into LOW, MEDIUM, HIGH risk.

----------------------------------------------------------------
5) EVALUATION
----------------------------------------------------------------
Metrics used for ML models:
    • Accuracy
    • Precision
    • Recall
    • F1-score
    • ROC-AUC
    • Confusion matrices

For repository risk scoring:
    • Visualization of risk score distribution.
    • Percentage of HIGH/MEDIUM/LOW risk commits.
    • Top risky commits listed for developer priority.
    • Overall repository safety conclusion.

All results shown inside Google Colab (graphs inline).

----------------------------------------------------------------
6) DEPLOYMENT (Project-Level Deployment Interpretation)
----------------------------------------------------------------
While not deployed as a live system, the pipeline supports:
    • Re-running on any GitHub repo via input().
    • Auto-generation of risk reports.
    • Model comparison and best-model identification.
    • Continuous defect prediction capability for any codebase.

In future:
    • Integrate into CI/CD pipelines.
    • Use trained models to score incoming pull requests.

================================================================
CRISP-DM SUMMARY
================================================================
CRISP-DM is fully implemented:
    • Business Understanding → Defect prediction + risk analysis
    • Data Understanding → PROMISE metrics + PyDriller history
    • Data Preparation → Cleaning, scaling, feature engineering
    • Modeling → Multiple ML models + heuristic risk scoring
    • Evaluation → Visual metrics + repo-level risk summary
    • Deployment → Reusable pipeline for any repository

This demonstrates a complete predictive analytics workflow
for real-world defect prediction in software engineering.
================================================================




                            CRISP–DM for Defect Prediction Project
================================================================================

                ┌──────────────────────────────────────────────────────┐
                │ 1. BUSINESS UNDERSTANDING                            │
                │ - Predict defect-prone modules                       │
                │ - Early detection of risky commits                   │
                │ - Improve software reliability                       │
                └──────────────┬───────────────────────────────────────┘
                               │
                               ▼
                ┌──────────────────────────────────────────────────────┐
                │ 2. DATA UNDERSTANDING                                │
                │ - PROMISE datasets (static code metrics)             │
                │ - PyDriller commit history (process metrics)         │
                │ - Explore dataset shapes & commit patterns           │
                └──────────────┬───────────────────────────────────────┘
                               │
                               ▼
                ┌──────────────────────────────────────────────────────┐
                │ 3. DATA PREPARATION                                  │
                │ - Cleaning, imputation, scaling                      │
                │ - SMOTE for imbalance                                │
                │ - Extract churn, complexity, experience              │
                │ - Normalize metrics for risk scoring                 │
                └──────────────┬───────────────────────────────────────┘
                               │
                               ▼
                ┌──────────────────────────────────────────────────────┐
                │ 4. MODELING                                          │
                │ - ML Models: LR, DT, RF, SVM, XGB                    │
                │ - Commit risk scoring model (heuristic)              │
                │ - Classification: LOW / MEDIUM / HIGH risk           │
                └──────────────┬───────────────────────────────────────┘
                               │
                               ▼
                ┌──────────────────────────────────────────────────────┐
                │ 5. EVALUATION                                        │
                │ - Accuracy, Precision, Recall, F1, AUC               │
                │ - Confusion Matrix, ROC/PR curves                    │
                │ - Repo-level risk summary                            │
                └──────────────┬───────────────────────────────────────┘
                               │
                               ▼
                ┌──────────────────────────────────────────────────────┐
                │ 6. DEPLOYMENT                                        │
                │ - Reusable pipeline                                  │
                │ - Risk scoring for ANY GitHub repository             │
                │ - Ready for CI/CD integration                        │
                └──────────────────────────────────────────────────────┘

================================================================================

"""
