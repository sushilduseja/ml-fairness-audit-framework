# Fairness Metrics Reference

## Purpose

Financial ML models require bias testing under regulatory frameworks (EU AI Act, ECOA, FCA, MAS). This guide explains fairness metrics, their use cases, and limitations.

## Core Principle

**No single metric captures "fairness"** - selection depends on use case, regulatory requirements, and stakeholder values.

## Metric Catalog

### 1. Demographic Parity

**Definition:** Equal positive prediction rates across groups

**Mathematical:** |P(Ŷ=1|A=0) - P(Ŷ=1|A=1)|

**When Applicable:**
- Regulatory requirements (some ECOA interpretations)
- Equal opportunity as goal
- Outcomes should be proportional to group sizes

**Limitations:**
- Ignores ground truth differences
- May conflict with accuracy goals
- Can incentivize demographic balancing over merit

**Academic Reference:** Calmon et al. (2017)

---

### 2. Equalized Odds

**Definition:** Equal true positive and false positive rates

**Mathematical:** P(Ŷ=1|Y=y, A=a) = P(Ŷ=1|Y=y, A=b) for all y

**When Applicable:**
- Accuracy matters equally across groups
- Ground truth labels are reliable
- Error consequences similar for all groups

**Limitations:**
- Requires labeled data
- Can be mathematically impossible with demographic parity (different base rates)
- Stricter than other metrics

**Academic Reference:** Hardt et al. (2016)

---

### 3. Calibration

**Definition:** Predicted probabilities equally reliable across groups

**Mathematical:** P(Y=1|Ŷ=ŷ, A=a) = P(Y=1|Ŷ=ŷ, A=b)

**When Applicable:**
- Probability estimates drive decisions
- Risk-based pricing
- Stakeholders rely on confidence scores

**Limitations:**
- Incompatible with equalized odds when base rates differ (Chouldechova 2017)
- Requires granular probability buckets
- Can mask TPR/FPR disparities

**Academic Reference:** Chouldechova (2017), Kleinberg et al. (2017)

---

## Metric Selection Framework

**Step 1:** Understand regulatory requirements
- EU AI Act → comprehensive testing
- ECOA → disparate impact (demographic parity)
- FCA → fairness principles (context-specific)

**Step 2:** Identify use case priorities
- Legal mandate → demographic parity
- Accuracy critical → equalized odds
- Probability-driven → calibration
- Individual treatment → counterfactual fairness

**Step 3:** Acknowledge tradeoffs
- Some metrics are mathematically incompatible
- Optimization for one may worsen another
- Document prioritization rationale

## Common Challenges

**Impossibility Theorem (Chouldechova 2017):**
Cannot simultaneously satisfy demographic parity + equalized odds + calibration when base rates differ across groups.

**Solution:** Prioritize based on use case and regulatory requirements; document tradeoffs.

**Proxy Variables:**
Features correlated with protected attributes (ZIP code, job title) may perpetuate bias even if protected attributes excluded.

**Solution:** Feature importance analysis; consider removal if problematic.

**Small Subgroups:**
Statistical estimates unreliable for small groups (high variance).

**Solution:** Report sample sizes; flag unreliable estimates; consider combining subgroups where meaningful.

## References

- Hardt et al. (2016): "Equality of Opportunity in Supervised Learning"
- Chouldechova (2017): "Fair Prediction with Disparate Impact"
- Kleinberg et al. (2017): "Inherent Trade-Offs in the Fair Determination of Risk Scores"
- Calmon et al. (2017): "Optimized Pre-Processing for Discrimination Prevention"
