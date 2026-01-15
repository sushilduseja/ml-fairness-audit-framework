# API Reference

*This is a design specification demonstrating API structure.*

## Authentication

```
Header: Authorization: Bearer YOUR_API_KEY
```

## Core Endpoints

### POST /api/v1/fairness/test

Submit fairness testing job.

**Request Schema:**
```json
{
  "model_id": "string",
  "model_name": "string",
  "use_case": "credit_approval|fraud_detection|underwriting",
  "predictions_path": "string (S3/GCS/Azure URI)",
  "features_path": "string",
  "ground_truth_column": "string",
  "prediction_column": "string",
  "protected_attributes": [
    {
      "name": "string",
      "type": "binary|categorical",
      "privileged_group": "string",
      "unprivileged_group": "string"
    }
  ],
  "fairness_metrics": ["demographic_parity", "equalized_odds", "calibration"],
  "report_config": {
    "format": "pdf|json|html",
    "audience": "data_scientist|compliance_officer|examiner"
  }
}
```

**Response (202 Accepted):**
```json
{
  "test_id": "string",
  "status": "processing",
  "created_at": "ISO 8601 timestamp",
  "estimated_completion": "ISO 8601 timestamp"
}
```

### GET /api/v1/fairness/test/{test_id}

Retrieve test results.

**Response (200 OK):**
```json
{
  "test_id": "string",
  "status": "completed|processing|failed",
  "overall_assessment": "PASS|CONDITIONAL_PASS|FAIL",
  "dataset_summary": {
    "total_samples": "integer",
    "protected_attribute_distributions": {}
  },
  "fairness_results": [
    {
      "metric": "string",
      "value": "float",
      "threshold": "float",
      "status": "PASS|FAIL",
      "confidence_interval": ["float", "float"],
      "p_value": "float",
      "interpretation": "string"
    }
  ],
  "recommendations": [
    {
      "priority": "high|medium|low",
      "issue": "string",
      "mitigation_strategy": "string",
      "implementation_effort": "low|medium|high"
    }
  ],
  "report_url": "string"
}
```

### POST /api/v1/fairness/explain

Generate stakeholder-appropriate explanation.

**Request:**
```json
{
  "metric": "string",
  "result": {"value": "float", "status": "string"},
  "audience": "compliance_officer|business_stakeholder|data_scientist",
  "use_case": "string"
}
```

**Response:**
```json
{
  "explanation": "string",
  "key_takeaways": ["string"],
  "business_implications": ["string"],
  "next_steps": ["string"]
}
```

### GET /api/v1/fairness/metrics

List available metrics with metadata.

**Response:**
```json
{
  "metrics": [
    {
      "name": "string",
      "category": "group_fairness|individual_fairness",
      "description": "string",
      "use_cases": ["string"],
      "limitations": ["string"],
      "references": ["string"]
    }
  ]
}
```

## Error Handling

**Standard Error Format:**
```json
{
  "error": {
    "code": "string",
    "message": "string",
    "remediation": "string"
  }
}
```
