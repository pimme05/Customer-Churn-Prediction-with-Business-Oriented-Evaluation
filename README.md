# Customer Churn Prediction with Business-Oriented Evaluation

## Overview
End-to-end churn prediction system designed to identify high-risk customers, estimate potential revenue loss, and prioritize retention actions under budget and operational constraints.

The project combines:
- a machine learning churn model (LightGBM),
- business-oriented decision thresholding,
- cost-based evaluation,
- explainability with SHAP,
- and a Power BI dashboard for monitoring and action.

---

## Business Problem
Customer churn is costly and relatively rare (~10% of customers).  
Missing a churner is significantly more expensive than contacting a loyal customer unnecessarily.

**Key challenges**
- Highly imbalanced target
- Default probability threshold (0.5) performs poorly
- Retention teams have limited capacity and budget

---

## Solution Approach
1. Trained a LightGBM churn prediction model with imbalance handling
2. Compared against a Logistic Regression baseline
3. Selected an **operational decision threshold** using recall-focused criteria on a validation set
4. Evaluated performance on a held-out test set
5. Quantified business impact using cost-based analysis
6. Generated customer-level outputs:
   - churn probability
   - risk tier
   - expected revenue loss
   - recommended retention action
7. Explained predictions using SHAP
8. Operationalized outputs in Power BI

---

## Data
- Source: Subscription business customer dataset (10,000 customers)
- Target: `churn` (binary)
- Features include:
  - engagement metrics
  - billing/payment behavior
  - satisfaction scores (CSAT, NPS)
  - tenure and contract attributes

Churn rate: **~10.2%**

---

## Model Performance (Test Set)

Evaluation performed on a **held-out test set (20%)**, with decision thresholds selected using a **validation set** to avoid data leakage.

### Baseline vs Final Model

| Model | ROC-AUC | PR-AUC | Recall (Churn) @ 0.5 | Notes |
|------|--------|--------|----------------------|------|
| Logistic Regression | 0.726 | 0.242 | 0.662 | Baseline |
| LightGBM | **0.773** | **0.250** | 0.074 | Better ranking power |

**Notes**
- ROC-AUC / PR-AUC measure ranking quality
- Recall at threshold 0.5 is low for churn due to imbalance
- Operational recall is optimized via threshold tuning (below)

---

## Operational Performance (Recall-Optimized Threshold)

Instead of using the default 0.5 cutoff, an **operational threshold** was selected to prioritize detecting churners.

**Threshold strategy**
- Objective: achieve ~80% recall for churn
- Threshold selected on validation set
- Applied to test set for final evaluation

### Test Set Results @ Operational Threshold

| Metric | Value |
|------|------|
| Churn Recall | **~0.80** |
| Precision (Churn) | ~0.24 |
| Accuracy | ~0.90 |
| Customers Flagged | ~6.3% |

**Interpretation**
- Accuracy remains high because most customers do not churn
- Precision is intentionally lower: false positives are cheaper than missed churners

---

## Business Impact (Cost-Based Evaluation)

A simple cost model was used to compare the churn model against a “do nothing” baseline.

**Cost assumptions**
- False Positive (unnecessary outreach): **$20**
- False Negative (missed churner): **$200**

### Cost Comparison (Test Set)

| Scenario | Estimated Cost |
|--------|----------------|
| Do Nothing Baseline | $40,800 |
| Model-Driven Intervention | **$18,660** |
| **Estimated Savings** | **$22,140 (~54%)** |

**Interpretation**
- Proactive retention significantly reduces expected churn loss
- Savings come from catching churners early, even with some over-contacting

---

## Scoring & Segmentation Results (All Customers)

| Metric | Value |
|------|------|
| Total Customers | 10,000 |
| Avg Churn Probability | 0.096 |
| High-Risk Customers | 629 (6.3%) |
| Expected Monthly Revenue at Risk | ~$33,300 |

---

## Retention Actions
Each customer is assigned a recommended action based on risk level and behavioral signals:

- Billing intervention
- Product re-onboarding
- Support recovery
- Retention offer
- Re-engagement campaign
- Monitor

This enables **targeted, cost-efficient retention strategies** instead of one-size-fits-all discounts.

---

## Explainability (SHAP)
SHAP is used to explain both global and individual predictions.

**Key global churn drivers**
- Engagement decline (logins, session time)
- Tenure patterns
- Payment failures
- Low satisfaction (CSAT, NPS)

Local SHAP explanations support **customer-specific retention decisions**.

---

## Tech Stack
- Python, Pandas, NumPy
- Scikit-learn
- LightGBM
- SHAP
  
---

## Key Takeaway
**Churn is predictable early through engagement, billing, and satisfaction signals.  
Focusing retention actions on a small high-risk segment can reduce expected churn loss by over 50%.**
