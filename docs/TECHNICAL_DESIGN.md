# Technical Design Document

## System Architecture

```
Model Artifacts → Preprocessing → Fairness Engine → Results → LLM Explainer → Report Generator
                      ↓                  ↓            ↓          ↓              ↓
                Data Validation    Metric Compute  Structured  Business      PDF/JSON/HTML
                                   (12+ algos)      Results    Language
```

**Core Components:**

1. **Data Ingestion & Validation** - Load predictions/features; validate schema; detect protected attributes
2. **Fairness Metrics Engine** - Compute 12+ group/individual fairness metrics with confidence intervals and significance tests
3. **Counterfactual Analysis** - Test individual fairness via attribute flipping; generate case examples
4. **LLM Explainability** - Translate technical metrics to stakeholder-appropriate language (context-aware prompting)
5. **Report Generator** - Create EU AI Act-compliant PDF; JSON for APIs; HTML for dashboards
6. **Recommendation Engine** - Suggest bias mitigation strategies (reweighting, threshold adjustment, feature engineering)

---

## Data Model

**Test Configuration:**
```json
{
  "test_id": "unique_identifier",
  "model_metadata": {
    "model_name": "credit_scoring_v2",
    "model_type": "classification",
    "use_case": "credit_approval",
    "risk_classification": "high_risk_eu_ai_act"
  },
  "dataset": {
    "predictions_file": "s3://path/predictions.parquet",
    "features_file": "s3://path/features.parquet",
    "ground_truth_column": "actual_default",
    "prediction_column": "predicted_probability"
  },
  "protected_attributes": [
    {
      "name": "gender",
      "type": "binary",
      "privileged_group": "male",
      "unprivileged_group": "female"
    }
  ],
  "fairness_metrics": ["demographic_parity", "equalized_odds"],
  "report_config": {
    "format": "pdf",
    "audience": "regulatory_examiner"
  }
}
```

**Test Results (Sample):**
```json
{
  "test_id": "TEST_20250114_ABC",
  "status": "completed",
  "fairness_results": [
    {
      "metric": "demographic_parity_difference",
      "value": 0.08,
      "threshold": 0.10,
      "status": "PASS",
      "confidence_interval": [0.06, 0.10],
      "p_value": 0.042,
      "interpretation": "Small but statistically significant disparity"
    }
  ],
  "overall_assessment": "CONDITIONAL_PASS",
  "recommendations": [
    {
      "priority": "high",
      "issue": "Equalized odds violation",
      "mitigation": "Threshold optimization by group",
      "references": ["Hardt et al. 2016", "EU AI Act Article 10"]
    }
  ]
}
```

---

## Fairness Metrics Catalog

### Group Fairness (4 Core Metrics for MVP)

| Metric | Definition | Use Case | Limitation |
|--------|-----------|----------|-----------|
| **Demographic Parity** | P(Ŷ=1\|A=a) = P(Ŷ=1\|A=b) | Equal opportunity goal | Ignores ground truth differences |
| **Equalized Odds** | Equal TPR and FPR across groups | When accuracy matters | Requires labeled data |
| **Calibration** | P(Y=1\|Ŷ=ŷ,A=a) = P(Y=1\|Ŷ=ŷ,A=b) | Probability estimates important | Can conflict with other metrics |
| **Predictive Equality** | Equal FPR across groups | False positives costly | Doesn't address TPR |

### Individual Fairness (Phase 2)

**Counterfactual Fairness:** Prediction unchanged when protected attribute flipped. Use when individual treatment matters (hiring, lending).

**Implementation Notes:**
- All metrics computed with 95% bootstrap confidence intervals
- Significance tested via permutation tests
- Effect sizes reported (Cohen's d, odds ratios)
- Handles intersectionality (2-way attribute combinations)

---

## API Design

**Endpoint Structure:**

```
POST /api/v1/fairness/test
Request: test_id, model_id, predictions_path, features_path, protected_attributes, metrics, report_format
Response: {test_id, status: "processing", estimated_completion}

GET /api/v1/fairness/test/{test_id}
Response: {test_id, status: "completed", results, report_url, recommendations}

POST /api/v1/fairness/explain
Request: metric, result, audience (data_scientist|compliance_officer)
Response: {explanation, key_takeaways, recommendations}

GET /api/v1/fairness/metrics
Response: {metrics: [{name, category, description, use_cases, limitations}]}
```

**Design Principles:** RESTful; async processing (webhooks); idempotent; comprehensive error messages

---

## Technical Decisions

**Metric Implementation:** Hybrid approach - leverage Fairlearn/AIF360 for standard metrics (trustworthy, maintained); custom extensions for finance-specific needs; validate against academic papers

**LLM Integration:** GPT-4o-mini for explanations (cost: ~$0.10-0.50/test); ground outputs in computed metrics (no speculation); human review during development

**Scalability:**
- Parallel metric computation (separate worker per metric)
- Stratified sampling for large datasets (10M sample support)
- Result caching (confusion matrices, group statistics)
- Celery/RabbitMQ for async jobs
- 10+ concurrent tests; <5 min completion for 100K samples

**Security:**
- No persistent raw data storage (predictions, features deleted post-test)
- Encryption at rest for results
- RBAC (Data Scientist, Model Risk, Compliance, Admin roles)
- Audit logging of all API calls
- On-premise deployment option (no cloud data transmission)

---

## Report Generation

**EU AI Act Compliance Template Structure:**

1. **Executive Summary** (non-technical)
2. **Model Description** (use case, architecture, training data)
3. **Protected Attributes Tested**
4. **Fairness Metrics Results** (with statistical significance)
5. **Counterfactual Analysis** (individual fairness findings)
6. **Limitations & Assumptions**
7. **Recommendations** (prioritized mitigation strategies)
8. **Appendix** (methodology, metric definitions, ground truth validation)

**Formats:** PDF (auditors), JSON (APIs), HTML (interactive dashboard)
