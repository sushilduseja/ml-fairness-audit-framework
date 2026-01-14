# ML Fairness Audit Report Template

**Report ID:** TEST_20250114_ABC123  
**Generated:** 2025-01-14 10:35 UTC  
**Model:** Credit Risk Assessment v2.1  
**Test Framework Version:** 1.0.0

---

## Executive Summary

This fairness audit evaluates the credit risk assessment model for bias across demographic groups. Testing was completed in compliance with EU AI Act Article 9 requirements and US ECOA disparate impact analysis standards.

**Overall Assessment:** CONDITIONAL_PASS ✓

**Key Finding:** Model demonstrates acceptable fairness on demographic parity and calibration metrics, but requires remediation on false positive rate disparity before production deployment.

**Recommended Action:** Implement threshold optimization by gender (estimated 2-week effort) to address equalized odds violation, then retest.

---

## 1. Model & Dataset Summary

### Model Information
- **Model Name:** Credit Risk Assessment v2.1
- **Use Case:** Credit approval decision support
- **Risk Classification:** High-risk (EU AI Act)
- **Training Date:** 2024-12-15
- **Framework:** XGBoost
- **Performance (AUC-ROC):** 0.82

### Dataset Summary
- **Total Samples:** 50,000
- **Time Period:** 2024 Q1-Q3
- **Features:** 45 (credit history, income, employment, etc.)
- **Ground Truth:** Binary (approved=1, denied=0)
- **Approval Rate:** 68.5% (34,250 approvals)

### Protected Attributes Tested

| Attribute | Type | Group Sizes | Definition |
|-----------|------|-------------|-----------|
| Gender | Binary | F: 22,500 (45%), M: 27,500 (55%) | Self-reported at application |
| Age | Categorical | <25: 7,500 (15%), 25-65: 36,000 (72%), >65: 6,500 (13%) | Years since birth |

---

## 2. Fairness Metrics Results

### 2.1 Demographic Parity (Group Fairness)

**Metric Definition:** Equal approval rates across demographic groups

| Protected Attribute | Group 1 Approval Rate | Group 2 Approval Rate | Disparity | Confidence Interval | P-Value | Status |
|---|---|---|---|---|---|---|
| **Gender** | F: 67.1% | M: 69.5% | 2.4% | [1.8%, 3.2%] | 0.042 | ✅ PASS |
| **Age** | <25: 62.0% | 25-65: 71.2% | 9.2% | [7.5%, 11.1%] | 0.0001 | ✅ PASS |
| **Age** | >65: 59.8% | 25-65: 71.2% | 11.4% | [9.2%, 13.8%] | 0.0001 | ⚠️ WARNING |

**Interpretation:**
- Gender disparity (2.4%) is small, statistically significant but unlikely to trigger disparate impact concerns
- Age group disparities exist; >65 vs. 25-65 shows 11.4% gap
- **Recommendation:** Monitor age disparity; consider targeted review if combined with other risk factors

---

### 2.2 Equalized Odds (Accuracy Fairness)

**Metric Definition:** Equal true positive rate and false positive rate across groups

| Protected Attribute | Group | TPR | FPR | TPR Disparity | FPR Disparity | Status |
|---|---|---|---|---|---|---|
| **Gender** | Female | 78.2% | 12.1% | 2.3% | 2.8% | ✅ PASS |
| | Male | 80.5% | 9.3% | | | |
| **Age** | <25 | 72.1% | 18.5% | 8.4% | 7.2% | ⚠️ WARNING |
| | 25-65 | 80.5% | 11.3% | | | |
| | >65 | 75.8% | 16.2% | 4.7% | 4.9% | ✅ PASS |

**Interpretation:**
- Gender: Both TPR and FPR disparities small and acceptable
- Age <25: Higher false positive rate (18.5% vs. 11.3%) → more young applicants incorrectly denied
- **Finding:** False positive disparity violates equalized odds threshold (7.2% > 5% threshold)
- **Root Cause:** Training data underrepresents young applicants; model less confident on this group
- **Severity:** HIGH - systematic disadvantage to young applicants

**Recommendation:**
1. **Threshold optimization:** Set approval threshold lower for age <25 to equalize FPR
2. **Alternative:** Retrain model with balanced data by age group
3. **Timeline:** 2-week implementation + validation

---

### 2.3 Calibration (Probability Reliability)

**Metric Definition:** Predicted approval probability equals actual approval rate within each probability bucket

| Probability Bucket | Female Actual Rate | Male Actual Rate | Disparity | Count | Status |
|---|---|---|---|---|---|
| 0.4-0.5 | 42% | 44% | 2% | 8,500 | ✅ PASS |
| 0.5-0.6 | 55% | 58% | 3% | 12,000 | ✅ PASS |
| 0.6-0.7 | 68% | 70% | 2% | 15,600 | ✅ PASS |
| 0.7-0.8 | 78% | 79% | 1% | 10,200 | ✅ PASS |
| 0.8-0.9 | 88% | 89% | 1% | 3,700 | ✅ PASS |

**Interpretation:**
- Probability estimates reliable and consistent across both genders
- Model confidence scores can be trusted for risk-based pricing
- **Status:** PASS - no gender-based calibration concerns

---

### 2.4 Predictive Equality (False Positive Focus)

**Metric Definition:** Equal false positive rates (incorrect denials) across groups

| Protected Attribute | Group | FPR | Disparity | Status |
|---|---|---|---|---|
| **Gender** | Female | 12.1% | 2.8% | ✅ PASS |
| | Male | 9.3% | | |
| **Age** | <25 | 18.5% | 7.2% | ❌ FAIL |
| | 25-65 | 11.3% | | |

**Interpretation:**
- Gender FPR acceptable (women denied only slightly more often without cause)
- Age <25 FPR significantly higher (18.5% vs. 11.3%) → many young applicants incorrectly denied
- **Severity:** HIGH - violates both equalized odds and predictive equality thresholds
- **Impact:** Estimated 700-1,000 young applicants per year affected (incorrectly denied)

---

## 3. Counterfactual Analysis (Individual Fairness)

**Testing:** Simulated "what-if" scenarios by flipping protected attributes

### Example Cases

**Case 1: Young Female vs. Young Male**
- Identical credit profile (score: 650, income: $50K, debt: $15K)
- Female: 58% approval probability → **APPROVED**
- Male: 61% approval probability → **APPROVED**
- **Result:** ✅ CONSISTENT (both approved)

**Case 2: 30-year-old Female vs. 25-year-old Female**
- Identical credit profile (score: 720, income: $75K, debt: $10K)
- 30-year-old: 78% approval → **APPROVED**
- 25-year-old: 72% approval → **APPROVED** (but lower confidence)
- **Result:** ⚠️ TREATMENT DIFFERS (approval confidence differs)

**Interpretation:**
- Individual fairness appears reasonable for gender
- Age creates systematic differences in treatment (younger = less approved, all else equal)
- Consistent with group fairness findings

---

## 4. Limitations & Assumptions

### Data Limitations
1. **Ground Truth Quality:** Binary outcome available for all 50K samples, but potential label lag (final outcome taken 6 months post-decision)
2. **Missing Protected Attributes:** Some applicants didn't report gender/age; excluded from analysis (0.5% of dataset)
3. **Sample Size:** Age >65 subgroup small (n=6,500); confidence intervals wider for this group
4. **Time Period:** Data from 2024 Q1-Q3; may not reflect seasonal patterns

### Methodological Limitations
1. **Statistical vs. Causal:** Metrics show correlation, not causation; age disparity could reflect unmeasured factors (employment type, credit history quality)
2. **Proxy Variables:** Zip code, employment type may be correlated with protected attributes; not explicitly tested
3. **Metric Incompatibility:** Chouldechova (2017) theorem: cannot simultaneously optimize demographic parity + equalized odds + calibration when base rates differ; trade-offs necessary
4. **Intersectionality:** Analysis tests attributes independently; interactions (gender × age) not tested

### Model Limitations
1. **Training Data Imbalance:** Young applicants underrepresented in training data (15% vs. 22% in application population)
2. **Feature Importance:** Model relies heavily on credit score; may perpetuate historical lending patterns
3. **Recency:** Model trained Dec 2024; may not reflect recent policy changes or market conditions

---

## 5. Recommendations & Remediation

### Priority 1: Address Equalized Odds Violation (Age <25)

**Issue:** 7.2% FPR disparity; young applicants incorrectly denied at higher rate

**Root Cause:** Model undertrained on young applicants; lower confidence on this group

**Option A: Threshold Optimization (Recommended)**
- Set approval threshold lower for age <25 (threshold: 0.58 vs. 0.62 for others)
- Expected impact: FPR equalization to <2% disparity
- Implementation effort: **LOW** (1-2 weeks)
- Trade-off: May increase approval for young applicants; validate impact

**Option B: Retrain with Balanced Data**
- Oversample young applicants in training data (target: 22% representation)
- Retrain model architecture
- Expected impact: Natural equalization of FPR/TPR
- Implementation effort: **MEDIUM** (3-4 weeks)
- Trade-off: Overall model performance may change slightly

**Option C: Hybrid Approach**
- Implement immediate threshold adjustment (Option A)
- Plan retrain for Q2 2025 (Option B)
- Provides quick compliance + longer-term solution

**Recommendation:** **Option C** (hybrid)
- Deploy threshold adjustment within 2 weeks
- Retest to validate FPR disparity <2%
- Plan retrain for next model cycle

---

### Priority 2: Monitor Age Disparity (Non-25-65 Groups)

**Issue:** 11.4% disparity >65 vs. 25-65; warrants monitoring

**Recommended Action:**
- Quarterly monitoring (retest every 3 months)
- Investigate if disparity grows
- If grows >15%: escalate to model risk for investigation

**Timeline:** Ongoing

---

### Priority 3: Collect Additional Data (Young Applicants)

**Issue:** Underrepresentation of young applicants in training data

**Recommended Action:**
- Target recruitment of young applicants in loan portfolio (marketing focus)
- Collect 12+ months additional data
- Retrain with expanded dataset (target: 22% young applicants)

**Timeline:** 12-month data collection + 3 weeks retrain (Q2 2025)

---

## 6. Regulatory & Compliance Mapping

### EU AI Act Compliance

✅ **Article 9 (Conformity Assessment):** Framework generates required documentation  
✅ **Annex IV (Technical Documentation):** Report fulfills requirements  
✅ **Risk Management:** Bias testing demonstrates risk mitigation  
⚠️ **Monitoring:** Quarterly retest planned (fulfills Article 9.5)

### US ECOA Compliance

✅ **Disparate Impact Analysis:** Demographic parity tested and documented  
✅ **Reg B Adverse Action:** Framework supports notice generation (separate workflow)  
⚠️ **Finding:** Age disparity documented; monitor for ECOA exposure

### UK FCA Compliance

✅ **Fairness Principles:** Multi-metric testing demonstrates fairness consideration  
✅ **Consumer Duty:** Pricing validated (calibration confirmed)  
✅ **Transparency:** Report ready for consumer inquiries

---

## 7. Implementation Checklist

**Before Production Deployment:**
- [ ] Implement threshold adjustment for age <25 (Priority 1)
- [ ] Retrain and validate (Priority 2)
- [ ] Legal review of remediation (2 days)
- [ ] Model risk approval (1 week)
- [ ] Retesting (1 day)

**Estimated Timeline:** 3-4 weeks

**Go/No-Go Decision:** Pending threshold optimization + retest

---

## 8. Monitoring Plan

**Frequency:** Quarterly (Jan, Apr, Jul, Oct)

**Metrics to Track:**
- Demographic parity by gender & age (alert if >5% change)
- Equalized odds FPR/TPR (alert if <threshold disparities grow)
- Calibration (alert if calibration degrades)

**Alert Thresholds:**
- FPR disparity grows >2%: investigate
- TPR disparity grows >3%: escalate
- Demographic parity >10%: escalate to legal review

**Escalation:**
- Model Risk Manager (first alert)
- Chief Risk Officer (second alert within 90 days)
- Executive Committee (if potential ECOA violation)

---

## Appendix A: Methodology

### Bootstrap Confidence Intervals
- Method: Stratified bootstrap resampling (10,000 iterations)
- Sample: With replacement, stratified by protected attribute
- CI Calculation: 2.5th and 97.5th percentiles of bootstrap distribution
- Coverage: 95% nominal coverage

### Permutation Testing
- Null Hypothesis: No difference in outcomes across groups
- Method: Permute protected attribute labels; recompute metric
- Iterations: 10,000 permutations
- P-value: Proportion of permutations with metric ≥ observed

### Software & Libraries
- Languages: Python 3.11
- Libraries: Fairlearn 0.9.0, AIF360 0.4.0, scikit-learn 1.3.0
- Validation: Results cross-checked against Fairlearn implementations

---

## Appendix B: Fairness Metrics Definitions

### Demographic Parity
$$DP = |P(\hat{Y}=1|A=0) - P(\hat{Y}=1|A=1)|$$

Where $\hat{Y}$ is predicted class and $A$ is protected attribute

### Equalized Odds
$$EOdds = \max(|FPR_0 - FPR_1|, |TPR_0 - TPR_1|)$$

### Calibration
$$Calib = \max_p |P(Y=1|\hat{Y}=p,A=0) - P(Y=1|\hat{Y}=p,A=1)|$$

### Predictive Equality
$$PE = |FPR_0 - FPR_1|$$

---

## Sign-Off

**Report Generated By:** ML Fairness Audit Framework v1.0  
**Test Completed:** 2025-01-14 10:35 UTC  
**Framework Validation:** Validated against Fairlearn (v0.9.0) and academic papers

**Reviewer (Data Science):** [Signature]  
**Date:** __________

**Reviewer (Model Risk):** [Signature]  
**Date:** __________

**Reviewer (Legal/Compliance):** [Signature]  
**Date:** __________

---

**DISCLAIMER:** This report documents statistical fairness assessments. Statistical fairness does not guarantee legal compliance. Legal interpretation and approval required from qualified legal counsel before production deployment. Final deployment decision remains human responsibility.
