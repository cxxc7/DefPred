# 📘 **PROJECT SUMMARY — DEFECT PREDICTION & RISK ANALYSIS PIPELINE**

This project focuses on:

1. **Defect Prediction** – identifying fault-prone modules using code metrics, process metrics, and historical change data.
2. **Early Detection of High-Risk Components** – automatically flagging commits/files that are likely to introduce bugs.

The system contains **two analytical engines** and a **risk-scoring layer**, forming a complete defect-prediction pipeline.

---

# **1. MACHINE LEARNING DEFECT PREDICTION (PROMISE DATASETS)**

### ✔️ Datasets

Multiple PROMISE datasets are automatically downloaded: **jm1, kc1, kc2, cm1, pc1**.

### ✔️ Data Processing Pipeline

* Detects defect label
* Selects numeric feature columns
* Handles missing values
* Scales features
* Balances classes using **SMOTE**

### ✔️ ML Models Used

* Logistic Regression
* Decision Tree
* Random Forest
* SVM
* (Optional) XGBoost

### ✔️ For each dataset + model, the script produces:

* Accuracy, Precision, Recall, F1
* ROC-AUC
* Confusion Matrix
* ROC curve
* Precision-Recall curve
* Classification report
* Dataset-level comparison charts

A **final comparison table** identifies the best model per dataset.

**Purpose:** Demonstrates how ML models can predict defect-prone modules based on historical metrics.

---

# **2. CHANGE-HISTORY MINING WITH PYDRILLER (REPOSITORY ANALYSIS)**

### ✔️ User Input

The user provides **any GitHub repository URL**.

### ✔️ Metrics Extracted Per Commit

* Added/deleted lines
* Code churn
* Cyclomatic complexity of modified files
* Number of methods changed
* Files modified
* Developer experience
* Commit message length
* Timestamp

### ✔️ Output

* A full commit-level table
* Exported **commit_metrics.csv**

**Purpose:** Extracts real-world *process metrics* known to correlate with software defects.

---

# **3. RISK SCORING & COMMIT-LEVEL DEFECT LIKELIHOOD**

A custom evaluator computes a **risk score (0–1)** for every commit.

### ✔️ Components of the Risk Score

* Churn score
* Complexity score
* Files modified score
* Developer experience score
* Commit message score

### ✔️ Commit Classification

* **LOW RISK** – safe
* **MEDIUM RISK** – requires review
* **HIGH RISK** – likely fault-prone

### ✔️ Visualizations

* Distribution of risk scores
* Pie chart of risk classes
* “Top risky commits” bar chart

### ✔️ Repository Summary

* Percentage of LOW/MEDIUM/HIGH risk commits
* Highest-risk commits listed
* Overall safety interpretation

---

# **OVERALL CONTRIBUTION**

By combining:

* Static code metrics (PROMISE)
* Process metrics (PyDriller)
* ML classification models
* Commit-level risk scoring

…the pipeline delivers a **modern, end-to-end defect prediction system** capable of:

* Identifying fault-prone modules
* Detecting risky commits early
* Guiding developers toward targeted reviews and testing

---

# 📘 **CRISP–DM APPLICATION IN THIS PROJECT**

Below is how the entire pipeline aligns with the **CRISP-DM framework**.

---

## **1. BUSINESS UNDERSTANDING**

**Goals:**

* Predict defect-prone code modules
* Detect risky commits early
* Improve code review prioritization and reliability

**Questions Answered:**

* Which components are likely to contain defects?
* Which commits introduce the most risk?
* Which ML models work best?

---

## **2. DATA UNDERSTANDING**

### A) PROMISE Datasets – *Static Code Metrics*

* LOC
* Complexity
* Coupling
* Defect labels

### B) PyDriller Repo History – *Process Metrics*

* Churn
* Complexity of changed files
* Commit experience
* Message quality
* Time-based features

**Activities:**

* Explored dataset structures
* Inspected commit-level metrics
* Displayed samples and visualizations

---

## **3. DATA PREPARATION**

### ✔️ For PROMISE Data

* Automatic defect label detection
* Cleaning + imputation
* Scaling
* SMOTE balancing
* Removing non-numeric and constant features

### ✔️ For Repository Data

* Extracting commit metrics
* Normalizing numeric metrics
* Engineering combined “risk” features

---

## **4. MODELING**

### ML Models Trained

* Logistic Regression
* Decision Tree
* Random Forest
* SVM
* XGBoost (optional)

**Process:**

* Stratified train/test split
* Fitting on preprocessed data
* Predicting defect probabilities

### Commit-Level Modeling

* Heuristic risk scoring
* Produces **risk_score ∈ [0,1]**
* Classifies into **Low/Medium/High risk**

---

## **5. EVALUATION**

### For ML Models

* Accuracy
* Precision / Recall / F1
* ROC-AUC
* Confusion Matrix
* ROC + PR curves

### For Repo Risk Scoring

* Visual risk distribution
* Risk category percentages
* List of highest-risk commits
* Final repository safety assessment

---

## **6. DEPLOYMENT (Project-Level)**

Although not deployed as a service, the system supports:

* Running on **any GitHub repo** on demand
* Auto-generated risk reports
* Reusable architecture for continuous scoring
* CI/CD integration potential

**Future Enhancements:**

* Automatic pull-request scoring
* Integration with GitHub Actions or Jenkins
* Persistent model deployment

---

# **CRISP–DM SUMMARY TABLE**

| Phase                      | What Was Done                                     |
| -------------------------- | ------------------------------------------------- |
| **Business Understanding** | Defect prediction + early risk detection          |
| **Data Understanding**     | PROMISE static metrics + PyDriller change history |
| **Data Preparation**       | Cleaning, balancing, scaling, feature engineering |
| **Modeling**               | ML models + commit risk scoring                   |
| **Evaluation**             | ML metrics, visualizations, repo risk summary     |
| **Deployment**             | Reusable pipeline, CI/CD-ready                    |

---

# **CRISP–DM DIAGRAM (Reformatted)**

```
┌───────────────────────────────────────────┐
│ 1. BUSINESS UNDERSTANDING                 │
│ - Predict defect-prone modules            │
│ - Detect risky commits early              │
│ - Improve software reliability            │
└───────────────────────┬──────────────────┘
                        │
                        ▼
┌───────────────────────────────────────────┐
│ 2. DATA UNDERSTANDING                     │
│ - PROMISE datasets (static metrics)       │
│ - PyDriller commit history (process data) │
└───────────────────────┬──────────────────┘
                        │
                        ▼
┌───────────────────────────────────────────┐
│ 3. DATA PREPARATION                       │
│ - Cleaning, scaling, imputation           │
│ - SMOTE balancing                         │
│ - Commit metric extraction & normalization│
└───────────────────────┬──────────────────┘
                        │
                        ▼
┌───────────────────────────────────────────┐
│ 4. MODELING                               │
│ - ML models: LR, DT, RF, SVM, XGB         │
│ - Commit risk scoring                     │
└───────────────────────┬──────────────────┘
                        │
                        ▼
┌───────────────────────────────────────────┐
│ 5. EVALUATION                             │
│ - Accuracy, F1, AUC, Confusion Matrix     │
│ - Repo risk summary                       │
└───────────────────────┬──────────────────┘
                        │
                        ▼
┌───────────────────────────────────────────┐
│ 6. DEPLOYMENT                             │
│ - Reusable pipeline                       │
│ - Ready for CI/CD integration             │
└───────────────────────────────────────────┘
```
