# Fairness Audit Report Template

*This template demonstrates report structure and content. Example values are illustrative.*

**Report ID:** [Generated]  
**Model:** [Name]  
**Test Date:** [ISO 8601]  
**Framework Version:** [Semantic version]

---

## Executive Summary

This fairness audit evaluates [model name] for bias across demographic groups, aligned with [applicable regulations].

**Overall Assessment:** [PASS / CONDITIONAL_PASS / FAIL]

**Key Findings:** [Summary of significant findings]

**Recommended Actions:** [Prioritized recommendations]

---

## 1. Model & Dataset Summary

### Model Information
- **Model Name:** [Name]
- **Use Case:** [Business purpose]
- **Risk Classification:** [Regulatory classification]
- **Model Type:** [Classification/regression/ranking]
- **Performance Metric:** [Accuracy/AUC/etc.]

### Dataset Summary
- **Total Samples:** [Count]
- **Time Period:** [Date range]
- **Features:** [Count and types]
- **Ground Truth:** [Available/not available]

### Protected Attributes Tested

| Attribute | Type | Groups | Definition |
|-----------|------|--------|------------|
| [Name] | [Binary/categorical] | [Group labels] | [How defined] |

---

## 2. Fairness Metrics Results

### Demographic Parity

**Definition:** Equal positive prediction rates across groups

**Results:**

| Protected Attribute | Group 1 Rate | Group 2 Rate | Disparity | CI | P-Value | Status |
|---|---|---|---|---|---|---|
| [Attribute] | [%] | [%] | [%] | [range] | [value] | [PASS/FAIL] |

**Interpretation:** [Plain language explanation]

### Equalized Odds

**Definition:** Equal true positive and false positive rates

**Results:**

| Attribute | Group | TPR | FPR | TPR Disparity | FPR Disparity | Status |
|---|---|---|---|---|---|---|
| [Attribute] | [Group 1] | [%] | [%] | [%] | [%] | [PASS/FAIL] |

**Interpretation:** [Plain language explanation]

### Calibration

**Definition:** Predicted probabilities equally reliable

**Results:** [Calibration analysis by probability bucket]

**Interpretation:** [Plain language explanation]

---

## 3. Limitations & Assumptions

### Data Limitations
- Sample size considerations
- Missing protected attributes
- Ground truth quality
- Time period representativeness

### Methodological Limitations
- Statistical vs. causal interpretation
- Metric incompatibility considerations
- Proxy variable issues
- Intersectionality coverage

---

## 4. Recommendations

### Priority Recommendations

**Priority 1:** [Issue description]
- **Root Cause:** [Analysis]
- **Mitigation Options:** [Specific approaches]
- **Implementation Effort:** [Estimate]
- **Expected Impact:** [Qualitative]

**Priority 2:** [Issue description]
- [Similar structure]

### Monitoring Plan
- **Frequency:** [Recommended cadence]
- **Metrics to Track:** [List]
- **Alert Thresholds:** [Criteria]

---

## 5. Regulatory Compliance

### EU AI Act Alignment
- Article 9 conformity assessment
- Annex IV technical documentation
- Risk management evidence

### US ECOA Alignment
- Disparate impact analysis
- Protected class coverage
- Documentation retention

---

## Appendix: Methodology

### Statistical Methods
- Bootstrap confidence intervals (method description)
- Permutation testing (method description)
- Significance level: [Alpha value]

### Software & Validation
- Framework version
- Academic library cross-validation
- Metric implementation references

---

**DISCLAIMER:** This report provides statistical fairness assessment. Statistical fairness does not guarantee legal compliance. Legal interpretation requires qualified legal counsel. Final deployment decision remains human responsibility.
