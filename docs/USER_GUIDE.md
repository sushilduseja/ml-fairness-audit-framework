# User Guide

## Quick Start (10 Minutes)

### Prerequisites
- API key (request from dashboard)
- Model predictions (CSV or Parquet)
- Feature data with protected attributes
- Ground truth labels (for equalized odds testing)

### Your First Test

**Step 1:** Prepare data files
```
predictions.parquet: model_id, predicted_probability
features.parquet: model_id, gender, age, income, ...
```

**Step 2:** Submit test via API or dashboard
```python
import requests
headers = {"Authorization": "Bearer YOUR_API_KEY"}
response = requests.post(
    "https://api.fairnessaudit.com/api/v1/fairness/test",
    headers=headers,
    json={
        "model_id": "credit_model_v1",
        "predictions_path": "s3://your-bucket/predictions.parquet",
        "features_path": "s3://your-bucket/features.parquet",
        "protected_attributes": [
            {"name": "gender", "type": "binary", "privileged_group": "M"}
        ],
        "fairness_metrics": ["demographic_parity", "equalized_odds"]
    }
)
test_id = response.json()["test_id"]
```

**Step 3:** Retrieve results (wait 2-5 minutes)
```python
results = requests.get(
    f"https://api.fairnessaudit.com/api/v1/fairness/test/{test_id}",
    headers=headers
).json()
print(results["overall_assessment"])  # PASS, CONDITIONAL_PASS, or FAIL
```

**Step 4:** Download PDF report
- Visit dashboard or access `report_url` from results

---

## User Guides by Persona

### ML Engineer: Running & Interpreting Tests

**When to Test**
- Before deploying new model to production
- After significant retraining
- When model risk requests validation

**Metric Selection Wizard**
1. What's your use case? (credit, fraud, hiring, insurance)
2. Do you have ground truth labels? (yes/no)
3. Do regulators require specific testing? (ECOA, EU AI Act, etc.)
→ System recommends appropriate metrics

**Understanding Results**

| Status | Meaning | Action |
|--------|---------|--------|
| **PASS** | All metrics meet thresholds | Deploy with confidence |
| **CONDITIONAL_PASS** | Some metrics fail; acceptable risk | Document decisions; get approval |
| **FAIL** | Significant bias detected | Remediate before deployment |

**Common Findings & Remediation**

**Finding: Demographic parity failure (8% approval disparity)**
- Root cause: Proxy variables in features (ZIP code = race proxy)
- Solution: Feature engineering (remove proxies; add protected status explicitly)
- Effort: Medium | Expected impact: High

**Finding: Equalized odds failure (12% FPR disparity)**
- Root cause: Base rate differences (groups have different default rates)
- Solution: Threshold optimization by group
- Effort: Low | Expected impact: High

**Finding: Calibration failure (probability overconfident for women)**
- Root cause: Smaller training data for minority group
- Solution: Collect more data or use calibration post-processing
- Effort: Medium | Expected impact: Medium

---

### Model Risk Manager: Reviewing & Approving Tests

**Approval Workflow**
1. Receive test request from ML engineer
2. Review fairness report (dashboard or PDF)
3. Check overall assessment and recommendation
4. Validate methodology (metrics appropriate for use case)
5. Approve (PASS) or request remediation (CONDITIONAL_PASS/FAIL)

**Key Sections to Review**

**Dataset Summary**
- Total samples (representative?)
- Protected attribute distributions (sufficient size per group?)
- Sample size warnings (small subgroups = unreliable estimates)

**Fairness Results**
- Metric value vs. threshold
- Confidence interval (how certain are we?)
- P-value (is disparity statistically significant or noise?)

**Red Flags**
- ❌ Very small subgroups (n < 100) → unreliable estimates
- ❌ High p-values (>0.05) → disparity may be due to chance
- ❌ Multiple metrics failing → deeper bias issue
- ❌ No remediation plan → not ready for deployment

**Approving Deployment**

**Case 1: All PASS**
- Decision: Approve deployment
- Documentation: Attach fairness report to model card

**Case 2: CONDITIONAL_PASS (some metrics fail)**
- Decision: Conditional approval
- Conditions: 
  - Document root cause analysis
  - Implement remediation (e.g., threshold adjustment)
  - Re-test after remediation
  - Implement monitoring plan
- Escalation: If disparity >15%, require legal review

**Case 3: FAIL**
- Decision: Do not deploy
- Action: Request remediation + re-test
- Escalation: Notify data science lead

---

### Compliance Officer: Generating Regulatory Reports

**Annual Regulatory Submissions**

**EU AI Act (Article 9) Documentation**

The fairness report contains:
1. ✅ Technical documentation (Article 9.4)
2. ✅ Risk management system evidence (Article 9.3)
3. ✅ Post-market monitoring plan (Article 9.5)

**How to Use**
- Export PDF report for each high-risk model
- Add to regulatory file for examinations
- Include in annual compliance certificate

**Example Scenario:** Regulator asks "How do you test for bias?"

Response using framework:
> "We use the ML Fairness Audit Framework to test all credit models quarterly. For model XYZ-v2, we tested:
> - Demographic parity (approval rate disparity: 3% - PASS)
> - Equalized odds (false positive disparity: 7% - PASS)
> - Calibration (probability reliability: consistent - PASS)
> 
> Results validated by Model Risk team. Documentation attached to regulatory file."

**ECOA Disparate Impact Analysis**

Framework reports demographic parity by protected class:

```
Gender (Female vs. Male):
- Approval rate female: 68%
- Approval rate male: 70%
- Disparity: 2% (PASS - below 10% threshold)

Age (Over 65 vs. Under 65):
- Approval rate >65: 62%
- Approval rate <65: 71%
- Disparity: 9% (PASS - below 10% threshold)
```

Include in periodic disparate impact analysis (Reg B requirement).

---

## FAQ

### "Which fairness metrics should I use?"

**Simple answer:** Use the metric selection wizard.

**Manual selection:**
- **Demographic Parity:** Legal requirement (ECOA interpretations), equal opportunity goal
- **Equalized Odds:** Accuracy matters equally; reliable ground truth
- **Calibration:** Probability estimates drive decisions (pricing, risk-based)
- **Predictive Equality:** False positives have severe consequences

**Complexity:** Some metrics are mathematically incompatible. See Fairness Metrics Guide for details.

### "What does PASS/FAIL mean?"

**PASS:** Metric value is below threshold (✅ acceptable)  
**FAIL:** Metric value exceeds threshold (❌ investigate)  
**CONDITIONAL_PASS:** Some metrics fail, but acceptable with remediation

Thresholds are defaults (10% disparity) but adjustable by use case.

### "How do I explain results to non-technical stakeholders?"

Use the "Explain" API endpoint:

```python
explanation = requests.post(
    "https://api.fairnessaudit.com/api/v1/fairness/explain",
    headers=headers,
    json={
        "metric": "equalized_odds_difference",
        "result": {"value": 0.15, "status": "FAIL"},
        "audience": "business_stakeholder"
    }
).json()
print(explanation["explanation"])
```

Returns business-friendly summary automatically.

### "What if metrics conflict?"

Some metrics are mathematically incompatible (Chouldechova 2017). Example: Can't simultaneously have demographic parity + equalized odds + calibration if base rates differ.

**Solution:** Prioritize based on use case
- Legal priority: demographic parity
- Accuracy priority: equalized odds
- Probability priority: calibration

Document prioritization in model risk file.

### "How often should I test?"

**Minimum:** Before production deployment  
**Best practice:** Quarterly (model drift, data shift detection)  
**Regulatory requirement:** Annually (for high-risk models)

Monitor for performance degradation between tests.

### "What if my group size is very small (n=50)?"

Small subgroups have unreliable estimates. Framework warns: "Sample size <100, confidence intervals may be wide."

**Options:**
1. Combine subgroups if meaningful (e.g., "non-majority" instead of individual ethnicities)
2. Use conservative thresholds (e.g., 5% disparity instead of 10%)
3. Report but don't rely solely on subgroup results

### "What's the difference between statistical and legal fairness?"

**Statistical Fairness** (what the tool measures):
- "Are outcomes statistically different across groups?"
- Based on data, not judgment

**Legal Fairness** (what regulators decide):
- "Is this a violation of law?"
- Requires judgment, context, and legal interpretation

**Bottom Line:** Tool shows statistical disparities. Legal counsel determines legal implications.

---

## Common Workflows

### Workflow 1: Deploying a New Model

1. **Prepare data**
   - Predictions (probability scores)
   - Features (including protected attributes)
   - Ground truth labels (optional, for equalized odds)

2. **Submit test**
   - Use dashboard or API
   - Select metrics (or use wizard recommendation)
   - Set report audience (engineer/compliance)

3. **Review results**
   - Check overall assessment
   - If FAIL → implement remediation → re-test
   - If CONDITIONAL_PASS → document decisions → get approval
   - If PASS → proceed to deployment

4. **Document**
   - Download PDF report
   - Attach to model card
   - Archive for audit trail

### Workflow 2: Responding to Regulatory Examination

1. **Examiner question:** "How do you test for bias?"

2. **Provide evidence:**
   - Most recent fairness reports (PDF)
   - Summary of testing frequency
   - Documentation of any failures and remediation

3. **Key documents:**
   - Test results (metrics, confidence intervals, p-values)
   - Dataset summary (sample sizes, distributions)
   - Recommendations implemented
   - Monitoring plan

---

## Sample Datasets

[Download sample datasets to practice](https://github.com/fairness-audit-framework/samples)

- `credit_scoring_demo.parquet` (10K samples, credit dataset)
- `fraud_detection_demo.parquet` (50K samples)
- `hiring_demo.parquet` (5K samples)

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Protected attribute not found" | Verify column name matches feature file |
| "Dataset too large (>10M)" | Sample or split into batches |
| "Ground truth not available" | Skip equalized odds; use demographic parity instead |
| "Results seem wrong" | Check protected attribute definition (privileged group correct?) |
| "Test is slow" | Large dataset; will complete in <5 min typically |

---

## Support

- **Documentation:** [docs.fairnessaudit.com](https://docs.fairnessaudit.com)
- **API Reference:** [api.fairnessaudit.com/docs](https://api.fairnessaudit.com/docs)
- **Email:** support@fairnessaudit.com
- **Slack:** [Community channel](https://slack.fairnessaudit.com)
