# API Reference

## Overview

RESTful API for bias testing integration. Async processing with webhook callbacks. All endpoints require authentication via API key.

---

## Authentication

```
Header: Authorization: Bearer YOUR_API_KEY
```

All requests must include authentication header. API keys created in dashboard.

---

## POST /api/v1/fairness/test

Submit a fairness testing job.

**Request:**
```json
{
  "model_id": "credit_scoring_v2",
  "model_name": "Credit Risk Assessment",
  "use_case": "credit_approval",
  "predictions_path": "s3://bucket/predictions.parquet",
  "features_path": "s3://bucket/features.parquet",
  "ground_truth_column": "actual_default",
  "prediction_column": "predicted_probability",
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

**Response (202 Accepted):**
```json
{
  "test_id": "TEST_20250114_ABC123",
  "status": "processing",
  "created_at": "2025-01-14T10:30:00Z",
  "estimated_completion": "2025-01-14T10:35:00Z"
}
```

**Errors:**
- `400 Bad Request`: Missing required fields
- `401 Unauthorized`: Invalid API key
- `422 Unprocessable Entity`: Protected attribute not found

---

## GET /api/v1/fairness/test/{test_id}

Retrieve test results and recommendations.

**Response (200 OK):**
```json
{
  "test_id": "TEST_20250114_ABC123",
  "status": "completed",
  "overall_assessment": "CONDITIONAL_PASS",
  "dataset_summary": {
    "total_samples": 50000,
    "protected_attribute_distributions": {
      "gender": {"male": 0.55, "female": 0.45}
    }
  },
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
  "recommendations": [
    {
      "priority": "high",
      "metric": "equalized_odds_difference",
      "issue": "False positive disparity",
      "mitigation_strategy": "threshold_optimization",
      "implementation_effort": "low"
    }
  ],
  "report_url": "https://reports.example.com/TEST_20250114_ABC123.pdf"
}
```

**Status Values:** `processing`, `completed`, `failed`, `cancelled`

---

## POST /api/v1/fairness/explain

Generate business-language explanation of metric results.

**Request:**
```json
{
  "metric": "equalized_odds_difference",
  "result": {"value": 0.15, "threshold": 0.10, "status": "FAIL"},
  "audience": "compliance_officer",
  "use_case": "credit_approval"
}
```

**Response (200 OK):**
```json
{
  "explanation": "The model incorrectly denies credit to women 15% more often than men with similar default risk. This is statistically significant and violates fair lending principles.",
  "key_takeaways": [
    "Women with same creditworthiness are denied more often",
    "Disparity is statistically significant (p < 0.001)",
    "Impacts thousands of customers annually"
  ],
  "business_implications": [
    "Legal risk: potential ECOA violation",
    "Regulatory examination likely",
    "Reputational risk if public"
  ],
  "next_steps": [
    "Root cause analysis",
    "Threshold optimization by gender",
    "Validation on holdout set",
    "Document remediation"
  ]
}
```

---

## GET /api/v1/fairness/metrics

List available fairness metrics.

**Response (200 OK):**
```json
{
  "metrics": [
    {
      "name": "demographic_parity_difference",
      "category": "group_fairness",
      "description": "Difference in positive prediction rates between groups",
      "formula": "|P(Ŷ=1|A=0) - P(Ŷ=1|A=1)|",
      "use_cases": [
        "Legal requirement under ECOA",
        "Equal opportunity goal",
        "Loan origination, hiring"
      ],
      "limitations": [
        "Ignores ground truth differences",
        "Incompatible with equalized odds if base rates differ"
      ],
      "references": ["Calmon et al. (2017)"]
    }
  ]
}
```

---

## Webhook Callback

When job completes, POST to your specified webhook URL:

```json
{
  "test_id": "TEST_20250114_ABC123",
  "status": "completed",
  "timestamp": "2025-01-14T10:35:00Z",
  "overall_assessment": "CONDITIONAL_PASS",
  "report_url": "https://reports.example.com/TEST_20250114_ABC123.pdf"
}
```

---

## Error Handling

All errors return standard format:

```json
{
  "error": {
    "code": "INVALID_ATTRIBUTE",
    "message": "Protected attribute 'ethnicity' not found in features",
    "available_attributes": ["gender", "age", "income"],
    "remediation": "Verify attribute names match feature columns"
  }
}
```

---

## Code Examples

**Python:**
```python
import requests

headers = {"Authorization": "Bearer sk_test_abc123"}
response = requests.post(
    "https://api.fairnessaudit.com/api/v1/fairness/test",
    headers=headers,
    json={
        "model_id": "credit_v2",
        "predictions_path": "s3://bucket/pred.parquet",
        "features_path": "s3://bucket/feat.parquet",
        "protected_attributes": [{"name": "gender", "type": "binary"}],
        "fairness_metrics": ["demographic_parity", "equalized_odds"]
    }
)
test_id = response.json()["test_id"]
```

**cURL:**
```bash
curl -X POST https://api.fairnessaudit.com/api/v1/fairness/test \
  -H "Authorization: Bearer sk_test_abc123" \
  -H "Content-Type: application/json" \
  -d '{
    "model_id": "credit_v2",
    "predictions_path": "s3://bucket/pred.parquet",
    "protected_attributes": [{"name": "gender", "type": "binary"}]
  }'
```

---

## Rate Limiting

- 100 requests/minute per API key
- Max 10 concurrent tests per account
- Dataset size limit: 10M samples
- Response: `429 Too Many Requests` with retry-after header

---

## Versioning

Current version: v1  
Deprecation policy: 12-month notice before removal
