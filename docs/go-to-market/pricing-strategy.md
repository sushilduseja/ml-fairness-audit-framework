# Pricing Strategy

## Pricing Model

**Primary Model:** Annual Subscription (SaaS)  
**Secondary Model:** Usage-based (per-test) for cost-conscious segments  
**Tertiary Model:** Professional Services (implementation, custom metrics)

---

## Pricing Tiers

### Tier 1: Core (€10,000/year)

**Target:** Small-to-mid banks, regional institutions, FinTechs

**Included:**
- Up to 50 tests/year
- 4 core metrics (demographic parity, equalized odds, calibration, predictive equality)
- PDF reports (standard template)
- API access (basic rate limits)
- Email support
- 12 months result storage

**Use Case:** Single data science team testing models quarterly

**Positioning:** "Get started with compliance-ready fairness testing"

---

### Tier 2: Professional (€25,000/year)

**Target:** Large banks, enterprise institutions

**Included:**
- Unlimited tests/year
- 12+ fairness metrics (core + extended)
- Counterfactual fairness testing
- Interactive dashboard (metric exploration, test history)
- Advanced report customization (logo, custom thresholds)
- API access (premium rate limits: 100 req/min)
- Priority email + Slack support
- 3-year result storage
- Quarterly business reviews

**Use Case:** Multi-team deployment; extensive model portfolio; regulatory audit frequency

**Positioning:** "Enterprise-grade fairness auditing with dedicated support"

---

### Tier 3: Enterprise (Custom pricing)

**Target:** Largest banks, global institutions, regulated brokers

**Included:**
- Everything in Professional, plus:
- Custom fairness metrics (domain-specific)
- Dedicated customer success manager
- On-premise deployment option (no cloud data transmission)
- Custom integrations (MLflow, SageMaker, internal platforms)
- Training & change management
- Security audit (SOC 2 Type II validation)
- Legal review support (documentation)
- Custom SLA (99.9%+ uptime)

**Pricing:** €100K+ based on scope (dedicated engineering, support, customization)

**Use Case:** Mission-critical fairness testing; highest security/compliance requirements

---

## Pricing Rationale

### Value-Based Pricing Approach

**Basis:** Regulatory compliance value + operational efficiency

| Component | Quantification | Value |
|-----------|---|---|
| **Compliance Risk** | Regulatory violation fine | €1-10M+ |
| **Operational Efficiency** | Data science time saved | €30-80K/year |
| **Audit Preparation** | Examination prep cost | €50-200K |
| **Total Annual Value** | Conservative (efficiency only) | €30-80K |

**Pricing:** €10-25K captures 12-80% of value → strong ROI

### Comparable Pricing

| Product | Price | Size | Cost/Model |
|---------|-------|------|-----------|
| Holistic AI | €100K+/year | Enterprise | €5K+/model |
| Credo AI | €80K+/year | Enterprise | €4K+/model |
| **Our Core** | **€10K/year** | **SMB** | **€200/model** |
| **Our Professional** | **€25K/year** | **Enterprise** | **€250/model** |

**Positioning:** 5-20x cheaper than enterprise alternatives

---

## Packaging & Positioning

### Core ($10K/year)
**Tagline:** "Compliance-ready fairness testing"  
**Target:** Banks with 1-2 data science teams; regional institutions  
**Annual Commitment:** 12-month subscription

### Professional ($25K/year)
**Tagline:** "Enterprise fairness auditing"  
**Target:** Large banks; multiple business units; extensive model portfolios  
**Annual Commitment:** 12-month subscription
**Expansion:** +€5K/year per additional 100 tests or 50+ metrics

### Enterprise (Custom)
**Tagline:** "Dedicated fairness platform"  
**Target:** Mission-critical deployments; highest security needs  
**Negotiated:** 3-year contract; customization + support

---

## Go-To-Market Pricing

### Launch Pricing (Year 1)
- **Early Adopter Discount:** 20% off first year (Core/Professional)
- **Multi-Year Discount:** 15% off 3-year commitment
- **Pilot Program:** Free access for pilot customers (max 50 tests)

### Expansion Pricing (Year 2+)
- **Standard Pricing:** Above tiers apply
- **Retention Discount:** 10% for year 2+ (loyalty)
- **Upgrade Path:** 50% proration when upgrading tiers mid-year

---

## Freemium Consideration

### Freemium Model (Alternative)

**Free Tier Limitations:**
- 5 tests/month
- 1 metric (demographic parity only)
- JSON output (no PDF)
- Community support (forum only)
- 30-day result retention

**Goal:** Drive adoption, convert to paid

**Pros:**
- Low friction (no credit card required)
- Enables SMBs/startups
- Network effects (more users)
- High-quality leads (self-filtered)

**Cons:**
- Support burden (community + internal)
- Lower conversion rate (estimated 5-10%)
- Cannibalization (users stay on free)
- Less revenue per user

**Decision:** **Not recommended for MVP**
- Financial services market is B2B2B (sales-driven)
- Compliance requirement drives purchasing (not price-sensitive on small features)
- Support burden high with specialized domain
- Better to focus on free trials (time-limited, limited features)

**Alternative:** Free Trial Program
- 30-day unlimited access (all features)
- No credit card required
- Self-service onboarding
- Convert at end of trial (high-touch sales for enterprise)

---

## Customer Acquisition Strategy

### Lead Gen + Sales Motion

1. **Content Marketing** (fairness guides, regulatory analysis)
   → Drive to free trial signup
   
2. **Free Trial** (30 days unlimited access)
   → Use AI Fairness Audit Framework to test their own models
   
3. **Sales Outreach** (high-touch for $25K+)
   → Post-trial, risk manager / compliance officer needs strong
   → Sales team follows up with customization / SLA discussion
   
4. **Expansion** (add-on modules, professional services)
   → Upsell remediation guidance, custom metrics after core adoption

### Pricing by Channel

| Channel | Price | Terms | Target |
|---------|-------|-------|--------|
| **Self-Service** (Online) | €10K/year | Annual | SMB; Agile |
| **Sales Direct** | €25K+/year | Negotiated | Enterprise |
| **Partners** | OEM/Revenue Share | Custom | Platforms (MLflow, SageMaker) |
| **Professional Services** | €2-5K/day | SOW | Implementation, training |

---

## Unit Economics (Year 1)

### Core Tier (€10K/year)
- **Unit Gross Margin:** 75% (€7.5K)
- **CAC:** €30K (enterprise sales required)
- **Payback Period:** 4 years (not ideal, require expansion)

### Professional Tier (€25K/year)
- **Unit Gross Margin:** 75% (€18.75K)
- **CAC:** €30K (includes trials + demo)
- **Payback Period:** 1.6 years ✅
- **LTV (5 years, 90% retention):** €66K → LTV/CAC = 2.2 ✅

### Blended (50/50 mix)
- **Average Revenue:** €17.5K/customer
- **Blended Gross Margin:** 75% (€13.125K)
- **Payback Period:** 2.3 years

---

## Pricing Evolution

### Year 1
- Core: €10K, Professional: €25K
- Launch discount: 20%
- Pilot program: Free (5 customers max)

### Year 2
- Core: €12K (+20%), Professional: €30K (+20%)
- Retention discount: 10%
- Expansion modules: +€5K (monitoring, remediation)

### Year 3+
- Index pricing to inflation + value
- Add vertical pricing (insurance-specific, etc.)
- Internationalize (€→ local currencies)

---

## Competitive Pricing Response

### If Competitors Price Lower
- **Maintain premium position** (differentiation on domain expertise, compliance)
- Bundle with professional services (add value beyond testing)
- Negotiate long-term contracts (discount for multi-year)

### If Competitors Price Higher
- **Aggressive positioning** ("70% savings vs. alternatives")
- Highlight simplicity (no additional infrastructure needed)
- Offer migration incentives (switch from competitors)

---

## Success Metrics (Pricing)

- **ARR (Annual Recurring Revenue):** €500K by end of Year 1
- **CAC Payback Period:** <2 years
- **Net Dollar Retention:** >100% (expansion revenue)
- **Win Rate:** >20% of qualified opportunities
- **Price Realization:** >95% (discounting <5%)
