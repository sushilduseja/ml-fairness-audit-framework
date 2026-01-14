# Product Requirements Document (PRD)

## Executive Summary

**Problem:** Financial institutions deploying ML for credit decisions, fraud detection, insurance underwriting, and KYC/AML face regulatory pressure to demonstrate absence of unlawful bias. EU AI Act (2025), US CFPB (2022), UK FCA, and Singapore MAS classify many models as "high-risk," requiring mandatory bias testing and documentation.

**Solution:** Automated fairness testing framework enabling ML teams to assess models for bias via standardized metrics, generate regulatory-compliant documentation, and receive actionable remediation guidance.

**Target Users:** ML Engineers (primary), Model Risk Managers (secondary), Compliance Officers (tertiary)

**Expected Outcomes:** Standardized testing methodology, reduced manual documentation effort (target: 70% time savings), audit-ready reports, increased model deployment approval confidence

---

## Market & User Research

### Regulatory Drivers
- **EU AI Act (2025):** Article 9 conformity assessment; Annex IV technical documentation requirements
- **US CFPB (2022):** Guidance on AI in credit decisions under ECOA
- **UK FCA (2023):** AI governance and fairness principles
- **Singapore MAS (2024):** FEAT framework compliance

### User Personas

| Persona | Background | Goals | Pain Points | Success Criteria |
|---------|-----------|-------|-----------|------------------|
| **ML Engineer** | 3-7 yrs experience | Test before deployment; satisfy model risk | Unclear which metrics; manual calculation complexity | Run test in <1 hour |
| **Model Risk Mgr** | Compliance validation | Consistent testing across inventory | Inconsistent documentation; interpretation difficulty | Approve/reject with confidence |
| **Compliance Officer** | Regulatory reporting | Demonstrate due diligence | Technical reports hard to translate for submissions | Generate audit-ready docs |

### Competitive Analysis
- **Commercial (Holistic AI, Credo AI):** Strong on enterprise/monitoring; gap on finance customization
- **Open Source (Fairlearn, AIF360):** Free & transparent; require coding expertise; no regulatory templates
- **DIY:** Tailored but inconsistent, unmaintained, poorly documented

**Differentiation:** Domain focus (finance) + regulatory alignment + accessibility (API+UI) + explainability layer + open-source core

---

## Product Requirements

### Must Have (MVP)
- 4 core fairness metrics (demographic parity, equalized odds, calibration, predictive parity)
- Statistical significance testing + 95% confidence intervals
- PDF report generation (EU AI Act compliant)
- Protected attribute detection & validation
- Basic visualization (confusion matrices, metric comparisons)
- REST API for test submission/result retrieval
- Dataset support up to 100K samples
- Test completion time <5 minutes
- Error handling with remediation guidance

### Should Have (Phase 2)
- 8+ additional metrics (individual fairness, causal fairness)
- Counterfactual fairness testing
- Interactive web dashboard
- Remediation recommendation engine
- Model version comparison
- Dataset support to 1M samples
- Webhook callbacks for async processing

### Won't Have (Out of Scope)
- Model training or retraining
- Data collection/labeling
- General ML ops features
- Real-time monitoring (batch focus)
- Automated remediation implementation

---

## Success Metrics

**Adoption:** Models tested/month, unique users, repeat usage rate (target: 50% 2+ tests)  
**Quality:** Report completeness (>95% regulatory requirements), metric accuracy (validated against papers), user satisfaction (>4/5)  
**Efficiency:** Bias test time <5 min (vs. 4-8 hrs manual), documentation time <30 min (vs. 8-16 hrs)  
**Business Impact:** 70%+ deployment approval for tested models, 0 fairness-related regulatory findings

---

## Prioritized Roadmap

**MVP (Q1, 12 weeks):** 4 core metrics, PDF reports, REST API, >90% test coverage  
**Phase 2 (Q2, 12 weeks):** 8+ metrics, dashboard, remediation engine, webhook support  
**Phase 3 (Q3+):** Custom metric builder, real-time monitoring, multi-language reports

---

## Risk Analysis

| Risk | Probability | Impact | Mitigation |
|------|---|---|---|
| LLM hallucinations in explanations | High | Medium | Ground in computed metrics; human review pre-launch |
| Metric computation errors | Medium | High | Validate against papers; cross-check Fairlearn/AIF360 |
| Liability for incorrect assessments | Medium | Critical | Clear disclaimers; user owns final decisions |
| Low adoption (too complex) | Medium | High | Onboarding wizard; training; documentation |
| Regulatory framework shifts | Low | Medium | Modular design; quarterly framework reviews |

---

## Non-Goals

- Not a legal compliance certification tool
- Not a model development platform
- Not automated remediation (recommendations only)
- Not real-time monitoring for MVP
