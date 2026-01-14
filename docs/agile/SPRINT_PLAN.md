# Agile Execution Plan

## MVP Roadmap (12 Weeks)

Phased approach focusing on core value: automated fairness testing + regulatory documentation.

---

## Sprint 1: Core Metrics Engine (4 weeks)

### Goals
- Implement 4 core fairness metrics
- Statistical testing framework
- Ground truth validation
- Deployable metric library

### User Stories

**US-101: Implement Demographic Parity Metric**
- AC: Calculate approval rate disparity between groups
- AC: Return point estimate + 95% CI
- AC: Include p-value from permutation test
- AC: Handle binary and multiclass protected attributes
- Points: 5

**US-102: Implement Equalized Odds Metric**
- AC: Calculate FPR and TPR disparity
- AC: Handle missing ground truth (skip gracefully)
- AC: Bootstrap 10K iterations for CI
- AC: Validate against Fairlearn library
- Points: 5

**US-103: Implement Calibration Metric**
- AC: Group predictions into probability buckets
- AC: Compare actual outcome rate across groups
- AC: Return max bucket disparity
- AC: Validate against AIF360
- Points: 5

**US-104: Implement Predictive Equality Metric**
- AC: Calculate FPR disparity only (no TPR)
- AC: Same CI/significance testing as others
- AC: Document use case guidance
- Points: 3

**US-105: Build Statistical Testing Framework**
- AC: Confidence interval computation (bootstrap)
- AC: Permutation test for significance
- AC: Effect size calculations (Cohen's d, odds ratios)
- AC: >90% unit test coverage
- Points: 8

### Deliverables
- `fairness_metrics.py` module (4 metrics + testing)
- Unit test suite (50+ tests)
- Validation report vs. academic libraries
- Documentation with math definitions

### Definition of Done
- Code review approved
- All tests passing
- Coverage >90%
- Validation against papers complete

---

## Sprint 2: API & Report Generation (4 weeks)

### Goals
- RESTful API for test submission
- PDF report generation
- LLM-powered explanations
- End-to-end workflow

### User Stories

**US-201: Build Test Submission API**
- AC: POST /api/v1/fairness/test accepts test config
- AC: Validate required fields (model, data paths, attributes)
- AC: Return test_id + estimated completion time
- AC: Async processing (return immediately)
- Points: 5

**US-202: Build Test Results API**
- AC: GET /api/v1/fairness/test/{test_id} returns results
- AC: Include metric values, CIs, p-values
- AC: Include remediation recommendations
- AC: Return report URL
- Points: 5

**US-203: Build Explainability API**
- AC: POST /api/v1/fairness/explain translates metrics
- AC: Context-aware prompting (audience: data scientist vs. compliance)
- AC: Ground output in computed metrics (no hallucination)
- AC: Return business-language summary + key takeaways
- Points: 5

**US-204: Generate PDF Reports**
- AC: EU AI Act compliant template
- AC: Include all required sections (model description, metrics, limitations)
- AC: Professional formatting
- AC: <30 second generation for 100K sample dataset
- Points: 8

**US-205: Build Job Queue & Async Processing**
- AC: Celery/RabbitMQ task queue
- AC: Support 10+ concurrent tests
- AC: Webhook callback on completion
- AC: Retry logic for failures
- Points: 8

**US-206: Data Validation & Protected Attribute Detection**
- AC: Validate schema compliance
- AC: Detect protected attributes in features (auto-suggest)
- AC: Warn on missing values, imbalance
- AC: Clear error messages with remediation
- Points: 5

### Deliverables
- FastAPI/Flask application with 6 endpoints
- Celery worker for background processing
- PDF report generator
- LLM integration (GPT-4o-mini prompts)
- Integration test suite

### Definition of Done
- API endpoints tested (happy path + errors)
- PDF reports manually reviewed
- LLM outputs QA'd (no hallucinations)
- Load testing passed (10 concurrent)

---

## Sprint 3: UI & Documentation (4 weeks)

### Goals
- Web interface for test submission
- Complete user documentation
- Example datasets & quickstart
- Beta-ready system

### User Stories

**US-301: Build Web Dashboard**
- AC: Test submission form (drag-drop file upload)
- AC: Status dashboard (monitor running tests)
- AC: Results viewer (metric visualization)
- AC: PDF report download
- Points: 8

**US-302: Create User Guide Documentation**
- AC: Quick start (10-min first test)
- AC: Persona-specific guides (ML engineer, risk manager, compliance)
- AC: API reference with code examples
- AC: FAQ addressing common questions
- AC: Troubleshooting guide
- Points: 5

**US-303: Prepare Sample Datasets**
- AC: Create 3-4 realistic demo datasets
- AC: Include ground truth labels
- AC: Document bias patterns in each
- AC: Host for download
- Points: 3

**US-304: Build Metric Selection Wizard**
- AC: Interactive questionnaire (use case, requirements)
- AC: Recommend appropriate metrics
- AC: Explain tradeoffs (incompatible metrics)
- Points: 5

**US-305: Create Regulatory Compliance Documentation**
- AC: EU AI Act mapping (Article 9 requirements)
- AC: ECOA disparate impact guidance
- AC: FCA fairness principles
- AC: Examples for each
- Points: 5

**US-306: Prepare Fairness Metrics Guide**
- AC: Plain language explanations for 4 core metrics
- AC: When/when not to use each
- AC: Academic references
- AC: Case study examples
- Points: 5

### Deliverables
- React/Vue web application
- Comprehensive user guide (PDF & web)
- API reference documentation
- Sample datasets & examples
- Fairness metrics educational content
- Getting started tutorial (video or walkthrough)

### Definition of Done
- Dashboard tested by 2-3 internal users
- Documentation complete & reviewed
- Sample datasets validated
- No critical usability issues

---

## Sprint 4+: Backlog (Future Phases)

### Should Have (Phase 2, Weeks 13-24)

**US-401: Expand Metric Library**
- AC: Add 8+ additional metrics (counterfactual, individual fairness)
- AC: Same validation & testing as core metrics

**US-402: Build Analytics Dashboard**
- AC: Interactive metric exploration
- AC: Trend analysis (test history)
- AC: Intersectionality analysis (gender × age)

**US-403: Remediation Recommendation Engine**
- AC: Suggest 3-5 remediation strategies per failure
- AC: Implementation difficulty estimates
- AC: Expected impact on metrics

**US-404: Model Version Comparison**
- AC: Compare fairness across model versions
- AC: Highlight improvements/regressions

### Could Have (Phase 3, Weeks 25+)

**US-501: Real-Time Monitoring**
- AC: Continuous fairness tracking (batch updates)
- AC: Degradation alerts
- AC: Drift detection

**US-502: Custom Metric Builder**
- AC: Low-code metric definition
- AC: User-defined fairness metrics

---

## Velocity & Burndown

### Sprint Velocity Targets
- Sprint 1: 26 points (metric implementation)
- Sprint 2: 36 points (API + complexity)
- Sprint 3: 26 points (UI + docs)

### Risk Mitigation

| Risk | Probability | Mitigation |
|------|---|---|
| LLM hallucinations in explanations | Medium | Manual QA; ground in metrics |
| Metric computation errors | Medium | Validation vs. papers before deploy |
| Performance (slow tests) | Low | Load testing; optimization if needed |
| Adoption (too complex) | Medium | Wizard; comprehensive documentation |

---

## Definition of MVP Done

**Product-Market Fit Validated:**
- [ ] 4 core metrics working correctly
- [ ] API endpoints reliable (99%+ uptime)
- [ ] PDF reports audit-ready
- [ ] Explanations LLM-powered but grounded
- [ ] Documentation complete & accurate
- [ ] Beta users can complete tests independently
- [ ] User satisfaction >4/5
- [ ] No critical bugs in production

**Launch Criteria:**
- [ ] Security audit passed
- [ ] Load testing successful (10 concurrent)
- [ ] Metric validation complete
- [ ] Legal review completed
- [ ] User documentation approved
- [ ] Support process defined
- [ ] Pricing finalized

---

## Post-MVP Success Metrics

**Week 1-4:** Onboarding & feedback
- Time to first test
- User satisfaction
- Support ticket themes

**Month 2-3:** Adoption & usage
- Models tested per month
- Repeat usage rate
- Feature requests

**Month 4-6:** Business impact
- Deployment approval rate (% of tested models approved)
- Regulatory findings (should be zero)
- Customer expansion (willing to expand usage)

**Launch go/no-go decision:** End of Sprint 3 (week 12)
- If metrics not met → extend phase or pivot
- If metrics met → proceed to Phase 2
