# Fairness Metrics Reference Guide

## Introduction

Financial ML models impact billions in lending, insurance, and investment decisions. Regulatory frameworks (EU AI Act, ECOA, FCA, MAS) now mandate bias testing for "high-risk" systems. This guide helps practitioners select and interpret fairness metrics.

### Why Fairness Matters
- **Legal:** ECOA prohibits discrimination; EU AI Act requires documented risk assessments
- **Ethical:** Models perpetuating historical bias undermine trust and inclusivity
- **Business:** Regulatory examination findings on fairness can trigger sanctions
- **Technical:** Understanding metric properties prevents false confidence in biased systems

### Key Principle
No single metric captures "fairness" - it's context-dependent. Select metrics based on use case, regulatory requirements, and stakeholder values.

---

## Core Metrics (MVP)

### 1. Demographic Parity (Statistical Parity)

**Definition:** Equal positive prediction rates across demographic groups
$$P(\hat{Y}=1|A=a) = P(\hat{Y}=1|A=b)$$

**Example:** % approved loans for women = % approved for men

**When to Use:**
- Legal requirement (some ECOA interpretations)
- When group outcomes should be equal
- Hiring, loan origination, parole decisions

**Limitations:**
- Ignores ground truth differences (e.g., different default rates)
- Incompatible with equalized odds when base rates differ
- Can incentivize demographic balancing over merit

**Calculation:**
```
parity_diff = |P(Ŷ=1|A=0) - P(Ŷ=1|A=1)|
threshold = 0.10 (Disparate Impact Rule)
pass if parity_diff < threshold
```

**Academic Reference:** Calmon et al. (2017)

---

### 2. Equalized Odds

**Definition:** Equal true positive and false positive rates across groups
$$P(\hat{Y}=1|Y=y, A=a) = P(\hat{Y}=1|Y=y, A=b) \text{ for all } y$$

**Example:** Fraction of defaulters caught by model is same across groups; fraction of non-defaulters flagged is same

**When to Use:**
- When accuracy matters equally to all groups
- Ground truth is reliable
- Credit scoring, fraud detection (lower FP rate better)

**Limitations:**
- Requires labeled data (often unavailable in production)
- Can be mathematically impossible with demographic parity if base rates differ
- Stricter than other metrics (harder to satisfy)

**Calculation:**
```
tpr_diff = |TPR_group_0 - TPR_group_1|
fpr_diff = |FPR_group_0 - FPR_group_1|
threshold = 0.10
pass if tpr_diff < threshold AND fpr_diff < threshold
```

**Academic Reference:** Hardt et al. (2016)

---

### 3. Calibration (Predictive Parity)

**Definition:** Predicted probabilities equally reliable across groups
$$P(Y=1|\hat{Y}=\hat{y}, A=a) = P(Y=1|\hat{Y}=\hat{y}, A=b)$$

**Example:** When model predicts 70% default probability for both groups, actual default rate is 70%

**When to Use:**
- Probability estimates drive decisions (e.g., interest rates)
- Stakeholders rely on model confidence scores
- Risk-based pricing, insurance underwriting

**Limitations:**
- Incompatible with equalized odds when base rates differ (Chouldechova 2017)
- Can hide TPR/FPR disparities
- Requires granular probability buckets (small sample issues)

**Calculation:**
```
For each probability bucket p:
  actual_rate_0 = P(Y=1 | Ŷ≈p, A=0)
  actual_rate_1 = P(Y=1 | Ŷ≈p, A=1)
calibration_diff = max_p |actual_rate_0 - actual_rate_1|
```

**Academic Reference:** Dressel & Farid (2018)

---

### 4. Predictive Equality

**Definition:** Equal false positive rates (Type I error) across groups
$$P(\hat{Y}=1|Y=0, A=a) = P(\hat{Y}=1|Y=0, A=b)$$

**Example:** Innocent people incorrectly accused at same rate across groups

**When to Use:**
- False positives have severe consequences (criminal justice, hiring)
- Focus on protecting historically disadvantaged groups

**Limitations:**
- Doesn't address true positive rates (may hurt minorities)
- Only meaningful for binary outcomes

**Calculation:**
```
fpr_diff = |FPR_group_0 - FPR_group_1|
threshold = 0.10
```

---

## Individual Fairness (Phase 2+)

### Counterfactual Fairness

**Definition:** Prediction unchanged if protected attribute flipped in causal model

**Example:** Loan decision same if applicant's gender were different (holding other factors constant)

**When to Use:**
- Individual treatment matters (hiring decisions, credit approvals)
- Protected attribute should have no causal effect

**Limitations:**
- Requires causal model (difficult to specify correctly)
- Assumes feature independence
- Computationally expensive

---

## Metric Selection Framework

**Step 1: Understand Use Case**
- What is the decision? (approve/deny, rank, classify)
- Who is impacted? (customers, employees, market participants)
- What are error consequences? (false positive vs. false negative)

**Step 2: Check Regulatory Requirements**
- EU AI Act → comprehensive testing (multiple metrics)
- ECOA → disparate impact analysis (demographic parity)
- FCA → fairness principles (context-specific)

**Step 3: Select Metrics**
- If legal mandate: use demographic parity
- If accuracy critical: use equalized odds
- If probability-driven: use calibration
- If individual treatment: add counterfactual fairness

**Step 4: Acknowledge Tradeoffs**
- Some metrics mathematically incompatible (Chouldechova 2017)
- Optimizing one may worsen another
- Balance based on use case priorities

---

## Common Pitfalls

### 1. Impossibility Theorem (Chouldechova 2017)
Cannot simultaneously satisfy demographic parity + equalized odds + calibration when base rates differ. Choose based on use case priorities.

### 2. Statistical vs. Causal Fairness
Statistical fairness (these metrics) tests association; doesn't prove causation. Bias may be driven by unmeasured confounders.

### 3. Proxy Variables
Model may use proxies for protected attributes (e.g., ZIP code as proxy for race). Test feature importance; consider removal if problematic.

### 4. Measurement Error
Protected attribute labels may be inaccurate. Conduct validation study; document error rates.

### 5. Subgroup Size
Small subgroups have unreliable estimates. Report sample sizes; flag high-variance subgroups.

---

## Case Studies

### Credit Scoring
**Scenario:** Bank deploys credit risk model; regulators require fairness testing

**Metric Selection:**
- Primary: Equalized odds (accuracy matters; default rates reliable)
- Secondary: Demographic parity (legal requirement)
- Tertiary: Calibration (interest rates probability-driven)

**Finding:** 12% FPR disparity (women denied more despite same default risk) → recomm: threshold adjustment by group

**Outcome:** Retraining with threshold optimization → 3% FPR disparity → regulatory approval

### Fraud Detection
**Scenario:** Fintech anti-fraud model flags transactions; disproportionately blocks customers from specific regions

**Metric Selection:**
- Primary: Equalized odds (minimize false positives equally)
- Secondary: Demographic parity (customer experience)

**Finding:** 18% FPR for region A vs. 4% for region B → recomm: retraining with region-specific thresholds

**Outcome:** Balanced FPR → reduced customer complaints + maintained fraud detection

---

## Decision Tree

```
┌─ Legal mandate for equal rates? ──→ Use Demographic Parity
│
├─ Need reliable probability estimates? ──→ Use Calibration
│
├─ Accuracy critical & ground truth available? ──→ Use Equalized Odds
│
├─ False positives have severe consequences? ──→ Use Predictive Equality
│
└─ Individual treatment matters? ──→ Use Counterfactual Fairness
```

---

## References

- **Hardt et al. (2016):** "Equality of Opportunity in Supervised Learning" (equalized odds framework)
- **Chouldechova (2017):** "Fair Prediction with Disparate Impact" (impossibility theorem)
- **Dressel & Farid (2018):** "The accuracy, fairness, and limits of predictive algorithms" (calibration analysis)
- **Buolamwini & Gebru (2018):** "Gender Shades" (intersectionality + measurement)
- **Calmon et al. (2017):** "Data Pre-processing Techniques for Classification without Discrimination"
- Fraud detection false positive disparities
- Trading algorithm market impact fairness
