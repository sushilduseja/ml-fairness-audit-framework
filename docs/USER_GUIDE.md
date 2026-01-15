# User Guide

## Getting Started

### Prerequisites
- API access credentials
- Model predictions (CSV/Parquet)
- Feature data including protected attributes
- Ground truth labels (optional, required for some metrics)

### First Test Workflow

**1. Prepare Data**
```
predictions file: model_id, predicted_probability
features file: model_id, [protected attributes], [other features]
```

**2. Submit Test**
Via API or dashboard interface, specify:
- Model identifier
- Data file locations
- Protected attributes to test
- Metrics to compute
- Report format preference

**3. Review Results**
- Overall assessment (PASS/CONDITIONAL_PASS/FAIL)
- Individual metric results
- Recommendations

**4. Document**
- Download report
- Attach to model documentation
- Store for audit trail

## Metric Selection Guidance

**Regulatory Requirements:**
- EU AI Act → comprehensive testing recommended
- ECOA → demographic parity typically required
- FCA/MAS → context-specific fairness principles

**Use Case Considerations:**
- Equal opportunity goal → demographic parity
- Accuracy critical → equalized odds
- Probability-driven decisions → calibration

**Metric Limitations:**
Some metrics mathematically incompatible when base rates differ. Select based on use case priorities; document rationale.

## Interpreting Results

### Status Classifications

**PASS:** All tested metrics meet thresholds
- **Action:** Proceed with deployment
- **Documentation:** Retain report for audit

**CONDITIONAL_PASS:** Some metrics fail but within acceptable ranges
- **Action:** Document findings and remediation plan
- **Requirement:** Obtain stakeholder approval before deployment

**FAIL:** Significant disparities detected
- **Action:** Implement remediation before deployment
- **Requirement:** Retest after remediation

### Understanding Confidence Intervals

Wide intervals indicate statistical uncertainty:
- May result from small sample sizes
- Subgroup size warnings provided
- Consider combining subgroups if meaningful

### Statistical Significance

P-values indicate whether disparity likely due to chance:
- p < 0.05: Statistically significant disparity
- p ≥ 0.05: May be due to random variation
- Not legal determination, statistical assessment only

## Common Workflows

### Pre-Deployment Testing
1. Complete model development
2. Run fairness test
3. Review results with model risk team
4. Implement remediation if needed
5. Obtain approval
6. Deploy

### Regulatory Examination Response
1. Retrieve historical test reports
2. Provide documentation showing:
   - Testing frequency
   - Metrics tested
   - Findings and remediation
   - Monitoring plan
3. Explain methodology if questioned

### Ongoing Monitoring
1. Establish baseline metrics at deployment
2. Periodic retesting (quarterly recommended)
3. Track metric trends over time
4. Investigate significant changes

## Troubleshooting

**"Protected attribute not found"**
→ Verify column name matches feature file exactly

**"Insufficient sample size"**
→ Combine subgroups if meaningful, or note limitation

**"Ground truth not available"**
→ Use metrics not requiring labels (demographic parity, calibration)

**"Results unexpected"**
→ Verify protected attribute group definitions (privileged vs. unprivileged)
