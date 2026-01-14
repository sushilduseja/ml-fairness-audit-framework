# Testing & Validation Strategy

## Overview

Comprehensive testing ensures metric accuracy, API reliability, and user trust in results.

---

## Unit Testing

### Metric Calculation Tests

**Coverage Target:** >90% for fairness metrics

**Test Types:**

1. **Ground Truth Validation**
   - Synthetic datasets with known bias (100% disparity)
   - Verify metrics detect it (high sensitivity)
   - Verify metrics pass unbiased data (high specificity)

2. **Edge Cases**
   - Small samples (n=10): confidence intervals widen as expected
   - Imbalanced groups (95:5 split): handles correctly
   - Zero disparity: metric returns 0 with narrow CI
   - Perfect disparity: metric returns max value

3. **Statistical Properties**
   - Confidence intervals: 95% CI covers true parameter 95% of time
   - P-values: calibrated correctly (5% false positive rate at α=0.05)
   - Bootstrap resampling: consistent across runs (seeded)

**Example Test Suite:**
```python
def test_demographic_parity():
    # Known bias data: 100% approval women, 0% approval men
    predictions = [1]*100 + [0]*100
    gender = ['F']*100 + ['M']*100
    
    parity = demographic_parity(predictions, gender)
    assert parity == 1.0  # 100% disparity
    
def test_demographic_parity_balanced():
    # Equal outcome: 70% approval both groups
    predictions = [1]*70 + [0]*30 + [1]*70 + [0]*30
    gender = ['F']*100 + ['M']*100
    
    parity = demographic_parity(predictions, gender)
    assert parity < 0.01  # ~0% disparity
```

---

## Integration Testing

### API Endpoint Tests

**Coverage:** All endpoints (POST /test, GET /test/{id}, POST /explain, GET /metrics)

**Test Scenarios:**

1. **Happy Path**
   - Submit valid test → PASS
   - Retrieve results → complete
   - Explain metric → coherent output
   - List metrics → >10 metrics returned

2. **Error Handling**
   - Missing auth header → 401
   - Invalid API key → 401
   - Missing required field → 400 with helpful error
   - Protected attribute not found → 422 with suggestions
   - Dataset too large (>10M) → 413

3. **Async Processing**
   - Submit test → returns immediately (202)
   - Poll result → transitions processing → completed
   - Webhook fires on completion → valid JSON

4. **Data Validation**
   - Empty dataset (n=0) → clear error
   - All same prediction (no variance) → warning in results
   - Missing ground truth for equalized_odds → skip metric, explain why

---

## Metric Validation

### Cross-Validation with Academic Libraries

**Comparison Against:**
- Fairlearn (Microsoft) - demographic parity, equalized odds
- AIF360 (IBM) - calibration, counterfactual fairness
- Academic papers (Hardt 2016, Chouldechova 2017)

**Validation Test:**
```python
from fairlearn.metrics import demographic_parity_difference
from framework.metrics import demographic_parity

# Use real dataset
y_true = df['actual_default']
y_pred = df['pred_probability'] > 0.5
groups = df['gender']

# Both should agree (within rounding)
fairlearn_result = demographic_parity_difference(y_true, y_pred, groups)
framework_result = demographic_parity(y_pred, groups)

assert abs(fairlearn_result - framework_result) < 0.001
```

### Confidence Interval Validation

**Method:** Bootstrap resampling with 10,000 iterations

**Validation:**
- Repeated sampling (same data, different seeds) → consistent CIs
- True parameter inside CI 95% of time (meta-test on 1000 datasets)
- CI width increases with smaller sample size (expected)

### Statistical Significance Testing

**Method:** Permutation test (null hypothesis: no disparity)

**Validation:**
- Type I error rate: ~5% (p < 0.05 on unbiased data)
- Power: >90% (detects 15% disparity with n=1000)
- Handles tied values (common in classification)

---

## LLM Output Validation

### Factual Consistency

**Validation Process:**

1. **Metric Grounding**
   - LLM explanation references actual metric value
   - Confidence interval mentioned if wide
   - P-value cited if relevant

2. **Hallucination Detection**
   - Red flags: Specific numbers not in input
   - Red flags: Regulatory claims without evidence
   - Red flags: Implementation claims not validated

3. **Example Validation**
   ```
   Metric Result: demographic_parity = 0.08, p=0.042
   
   ✅ Good explanation:
   "Model has 8% approval disparity between groups, 
   statistically significant (p=0.042)"
   
   ❌ Bad explanation:
   "Model has 12% approval disparity" (wrong number)
   "Will be sued under ECOA" (legal speculation)
   ```

### Explanation Quality Rubric

| Dimension | Excellent | Good | Poor |
|-----------|-----------|------|------|
| **Accuracy** | Values match metric results | Minor rounding differences | Wrong numbers |
| **Clarity** | Non-technical language | Mix of jargon/plain | Highly technical |
| **Actionability** | Concrete next steps | Generic recommendations | No guidance |
| **Grounding** | References metric facts | Mostly factual | Speculative |

### QA Process

1. **Human review** (pre-launch): 50 generated explanations reviewed manually
2. **Automated checks** (post-launch): Flag likely hallucinations for manual review
3. **User feedback** (ongoing): Collect ratings; improve prompts based on feedback

---

## User Acceptance Testing (UAT)

### Beta Testing Program

**Participants:** 3-5 financial institutions (credit, fraud, insurance)

**Scenarios:**

1. **Persona: ML Engineer**
   - Task: Run fairness test on existing production model
   - Success: Complete in <30 min, interpret results correctly

2. **Persona: Model Risk Manager**
   - Task: Review test results; approve/reject for deployment
   - Success: Make decision confidently with clear rationale

3. **Persona: Compliance Officer**
   - Task: Prepare regulatory report from test results
   - Success: Generate audit-ready documentation with minimal manual work

### Feedback Metrics

- Task completion rate (% users finish without help)
- Time to task completion
- Error rate (misinterpretations)
- Satisfaction rating (1-5 Likert scale)
- NPS (Net Promoter Score)

### Bug Resolution

- Critical (crashes, wrong metrics): fix same day
- Major (confusing UI, wrong numbers): fix within 3 days
- Minor (typos, UX improvements): backlog for next release

---

## Performance Testing

### Load Testing

**Scenario:** 10 concurrent tests, 100K samples each

**Targets:**
- Test completion time: <5 minutes
- API response time: <2 seconds
- 99th percentile latency: <5 seconds
- Memory per test: <2GB
- No errors or timeouts

**Tools:** Locust (Python load testing)

### Stress Testing

**Scenario:** 50 concurrent tests (10x normal load)

**Acceptance:** System degrades gracefully
- Queue requests (don't error)
- Prioritize in-flight tests
- Provide realistic ETAs
- No data loss

---

## Security Testing

### Vulnerability Assessment

- OWASP Top 10 (injection, authentication, data exposure)
- SQL injection tests (data path validation)
- XSS testing (report generation)
- CSRF protection verification
- Authentication bypass attempts

### Data Privacy

- ✅ Predictions/features not stored permanently
- ✅ Results encrypted at rest
- ✅ Audit logging of data access
- ✅ GDPR compliance (right to deletion)

---

## Continuous Testing

### Pre-Commit Checks
- Unit tests pass (>90% coverage)
- Linting (code style)
- Type checking (if applicable)

### Pre-Release Checklist
- [ ] All unit tests pass
- [ ] Integration tests pass
- [ ] Metric validation against papers
- [ ] Load testing acceptable
- [ ] Security scan clean
- [ ] UAT feedback addressed
- [ ] Documentation updated
- [ ] Release notes prepared

### Post-Launch Monitoring
- Metric accuracy (sample audits)
- API error rates (target: <0.1%)
- User issue volume (escalation if >10/day)
- Regulatory findings (should be zero)

---

## Test Data

### Synthetic Datasets

**Available for download:**
- `balanced_bias_100.csv` (perfect balance, no bias)
- `disparity_25pct.csv` (25% approval gap)
- `small_subgroup.csv` (tests edge case handling)
- `imbalanced.csv` (95:5 group split)

### Real Dataset (Production Simulation)

- 50K samples (typical financial dataset size)
- 60% approval rate (realistic baseline)
- Gender, age distributions match US census
- Known bias pattern (simulated but realistic)

---

## Validation Artifacts

### Test Report Template

Each release includes:
- Unit test coverage report
- Integration test results (pass/fail by endpoint)
- Metric validation results (vs. academic libraries)
- Load test report (latency, throughput)
- Security scan summary
- UAT feedback summary

### Quality Gates

Release proceeds only if:
- Unit test coverage ≥ 90%
- All integration tests passing
- Zero critical security findings
- Load test meets targets
- UAT feedback resolved (critical/major issues)
