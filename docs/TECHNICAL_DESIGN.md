# Technical Design Document

## Architecture Overview

```
Data Input → Validation → Fairness Engine → Results → Explainer → Report
              ↓             ↓                 ↓         ↓           ↓
         Schema Check   Metric Compute   Structured  Business   PDF/JSON
                        (Statistics)      Output     Language
```

**Components:**

1. **Data Validation:** Schema compliance, missing value handling, protected attribute detection
2. **Fairness Engine:** Statistical metric computation with confidence intervals
3. **Results Formatter:** Structured output with status classifications
4. **Explanation Layer:** LLM-based translation to stakeholder language
5. **Report Generator:** Regulatory-compliant documentation

## Fairness Metrics (Core Set)

### Demographic Parity
- **Definition:** P(Ŷ=1 | A=a) = P(Ŷ=1 | A=b)
- **Use Case:** Equal opportunity goals, ECOA compliance
- **Limitation:** Ignores ground truth differences
- **Reference:** Calmon et al. (2017)

### Equalized Odds
- **Definition:** P(Ŷ=1 | Y=y, A=a) = P(Ŷ=1 | Y=y, A=b)
- **Use Case:** Accuracy-focused, reliable labels
- **Limitation:** Requires labeled data
- **Reference:** Hardt et al. (2016)

### Calibration
- **Definition:** P(Y=1 | Ŷ=ŷ, A=a) = P(Y=1 | Ŷ=ŷ, A=b)
- **Use Case:** Probability-driven decisions
- **Limitation:** May conflict with other metrics
- **Reference:** Kleinberg et al. (2017)

## API Design

**Core Endpoints:**

```
POST /api/v1/fairness/test
- Submit test configuration
- Returns: test_id, status

GET /api/v1/fairness/test/{test_id}
- Retrieve results
- Returns: metrics, recommendations, report_url

POST /api/v1/fairness/explain
- Generate stakeholder explanation
- Returns: plain-language interpretation

GET /api/v1/fairness/metrics
- List available metrics
- Returns: catalog with definitions
```

**Design Principles:**
- RESTful conventions
- Async processing for large datasets
- Comprehensive error messages
- Idempotent operations

## Data Model

**Test Configuration Schema:**
```json
{
  "model_id": "string",
  "predictions_path": "string",
  "features_path": "string",
  "protected_attributes": [
    {
      "name": "string",
      "type": "binary|categorical",
      "privileged_group": "string"
    }
  ],
  "fairness_metrics": ["string"],
  "report_format": "pdf|json|html"
}
```

## Implementation Approach

**Metric Computation:**
- Leverage validated libraries (Fairlearn, AIF360) where applicable
- Custom extensions for financial services use cases
- Statistical testing via bootstrap resampling
- Confidence intervals (95% by convention)

**LLM Integration:**
- Purpose: Translate technical → business language
- Grounding: All statements tied to computed metrics
- Safeguard: No speculative claims
- Model: GPT-4o-mini for cost efficiency

**Scalability Patterns:**
- Parallel metric computation
- Sampling strategies for large datasets
- Result caching
- Background job processing

## Security Design

- No persistent storage of raw data
- Encryption at rest for results
- Role-based access control
- Audit logging
- On-premise deployment option
