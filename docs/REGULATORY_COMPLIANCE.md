# Regulatory Compliance Mapping

## EU AI Act (2025)

### Article 9 Conformity Assessment

**Requirement:** High-risk AI systems must undergo conformity assessment before deployment.

**Framework Response:**

| Requirement | How Framework Addresses | Evidence |
|-------------|------------------------|----------|
| Risk assessment | Fairness testing identifies bias risks | Fairness metrics report |
| Documentation | Auto-generated technical docs per Annex IV | PDF report w/ methodology |
| Testing protocols | 4+ metrics with statistical validation | Metric definitions, confidence intervals |
| Records retention | Test results stored with audit trail | Dashboard history, API logs |

### Annex IV Technical Documentation

Framework report includes:

1. **Model description**
   - Use case, risk classification, training data summary
   - Model architecture, performance metrics
   
2. **Protected attributes tested**
   - List of groups tested (gender, age, etc.)
   - Definitions of privileged/unprivileged groups
   
3. **Fairness metrics results**
   - Demographic parity, equalized odds, calibration, others
   - Confidence intervals, p-values, effect sizes
   - Pass/fail status for each metric
   
4. **Counterfactual analysis**
   - Individual fairness assessment
   - Example cases where treatment differs by group
   
5. **Limitations**
   - Metric assumptions documented
   - Data quality issues noted
   - Sample size warnings for small subgroups
   
6. **Remediation recommendations**
   - Prioritized by impact
   - Implementation guidance
   - Expected outcomes

### Article 9.3: Risk Management System

Framework supports documentation of:
- Regular bias testing (quarterly schedule)
- Change log (model updates, retraining)
- Incident tracking (regulatory findings)
- Remediation monitoring

### Article 9.5: Post-Market Monitoring

Framework enables:
- Baseline fairness metrics from deployment
- Monitoring test re-runs (quarterly)
- Drift detection (fairness degradation alerts)
- Documentation of findings and actions

---

## US ECOA (Equal Credit Opportunity Act)

### Regulation B: Disparate Impact Analysis

**Requirement:** Lenders must monitor lending practices for disparate impact (unequal treatment despite neutral policy).

**Framework Response:**

**Protected Classes:**
- Gender (male/female)
- Age (by decade or >65/<65)
- Race/Ethnicity (if available)
- Marital status
- National origin

**Metric:** Demographic Parity (approval rate disparity)

**Threshold:** 80% rule (disparate impact = 20%+ approval gap) or less stringent (10%) for regulation

**Example Report:**
```
Female: 68% approval rate
Male: 70% approval rate
Disparity: 2% ✅ PASS (below 20% threshold)
```

### Reg B Compliance Checklist

- [ ] Annual disparate impact analysis documented
- [ ] Fairness testing includes all protected classes
- [ ] Results preserved for 25 years (per regulation)
- [ ] Adverse action notices sent to applicants (separate from fairness)
- [ ] Response plan for any adverse findings

---

## UK FCA (Financial Conduct Authority)

### AI Governance Guidance (2023)

**Fairness Principles:** Models must not unfairly disadvantage consumers.

**Framework Mapping:**

| FCA Principle | Framework Implementation |
|---------------|--------------------------|
| Fairness | Multi-metric testing (4+ metrics) |
| Transparency | Plain-language explainability |
| Accountability | Audit trail and documentation |
| Robustness | Confidence intervals, significance testing |

### Consumer Duty (2023)

**Requirement:** Firms must act in consumers' best interests.

**Fairness Testing Supports:**
- Fair pricing (calibration: price proportional to actual risk)
- Equal access (demographic parity: not systematically excluding groups)
- Transparent decisions (explainability: explain why approved/denied)

### Example Compliance Evidence:
> "Fairness audit completed Jan 2025. Demographic parity: 4% approval disparity (PASS). Calibration verified: probability estimates accurate across genders. Risk-based pricing validated; women not systematically overcharged."

---

## Singapore MAS (Monetary Authority)

### FEAT Framework (2024)

**Pillars:** Fairness, Ethics, Accountability, Transparency

**Framework Alignment:**

| Pillar | Framework Capability |
|--------|---------------------|
| **Fairness** | Multi-metric testing (demographic parity, equalized odds) |
| **Ethics** | Responsible AI design; limitations transparency |
| **Accountability** | Audit trail; documented decisions; escalation processes |
| **Transparency** | Plain-language explanations; stakeholder communication |

### Compliance Reporting:

Framework supports annual FEAT attestation:
- Fairness: Testing frequency, metrics, results
- Ethics: Data governance, bias mitigation processes
- Accountability: Model governance, review procedures
- Transparency: Explainability methods, stakeholder engagement

---

## US CFPB (Consumer Financial Protection Bureau)

### Guidance on AI in Credit Decisions (2022)

**Prohibition:** Companies must not use algorithms that have disparate impact on protected classes.

**Framework Response:**

- **Testing:** Demographic parity by protected class
- **Documentation:** Fairness report with confidence intervals
- **Monitoring:** Quarterly re-testing (detect drift)
- **Remediation:** Action plan for any failures

**Required Analysis:**

1. Overall model accuracy by group (accuracy parity)
2. Approval/denial rates by group (demographic parity)
3. Loan pricing disparity (calibration)
4. Marketing/marketing outreach bias (if applicable)

---

## Implementation Roadmap

### Pre-Deployment (MVP)
- [x] Demographic parity testing (ECOA requirement)
- [x] Equalized odds testing (accuracy fairness)
- [x] Calibration testing (pricing fairness)
- [x] EU AI Act documentation template

### Post-Deployment (Phase 2)
- [ ] Monitoring dashboard (quarterly re-testing)
- [ ] Drift alerts (fairness degradation detection)
- [ ] Intersectionality analysis (gender × age, etc.)
- [ ] FEAT framework reporting (MAS Singapore)

### Long-Term (Phase 3)
- [ ] Custom fairness metrics (industry-specific)
- [ ] Causal inference (counterfactual fairness)
- [ ] Real-time fairness monitoring
- [ ] Multi-jurisdiction regulatory reports (auto-generated)

---

## Legal Disclaimers

**⚠️ Tool outputs are NOT legal compliance certifications**

- Framework provides fairness metrics; regulators determine legal sufficiency
- Legal interpretation required (consulting legal counsel recommended)
- Statistical fairness ≠ legal fairness (different standards)
- Results must be reviewed by qualified personnel (data scientist + model risk + legal)

**Appropriate Use:**
✅ Internal fairness assessment  
✅ Regulatory filing documentation  
✅ Risk management evidence  

**Inappropriate Use:**
❌ Standalone compliance certification  
❌ Legal defense without expert review  
❌ Automated approval/rejection decisions  

---

## Regulatory Monitoring

Framework helps track:
- Regulatory updates (quarterly review of AI Act guidance, CFPB circulars, FCA updates)
- Threshold changes (update disparity thresholds per regulator guidance)
- New protected classes (adapt testing as regulations evolve)
- Jurisdiction expansion (apply framework to multiple geographies)

**Responsibility:** Legal/compliance reviews regulatory environment; updates framework configuration as needed.
