
# PayGuard AI — Business Justification

**Document Status:** Draft  
**Document Owner:** Nataliia Strokan, Senior AI Product Manager  
**Business Sponsor:** Olivia Bennett, CPO  
**Planning Horizon:** 3 years  
**Version:** 1.0

## 1. Executive Business Context

PayGuard AI is a hypothetical B2B SaaS platform serving approximately 350 fintech companies, payment service providers, marketplaces, and merchants across EU and US markets.

The platform processes approximately 35 million payment events per month and combines:

- **Classical ML risk scoring** to identify suspicious payment activity and reduce unnecessary manual reviews.
- **LLM/RAG Operations Copilot** to support risk analysts with contextual information from approved policies, PSP procedures, and historical cases.
- **Human oversight** to ensure final payment risk decisions remain under authorized analyst control.

The current false positive rate (FPR) has increased from 4.8% to 8.1%, contributing to higher operational workload. Manual review volume has increased by approximately 35%.

The primary business objective is to reduce FPR from 8.1% to ≤5.0% within six months while maintaining fraud detection effectiveness and regulatory compliance.

The investment decision must consider both operational benefits and the total cost of implementing and operating the AI/ML platform.

## 2. Financial Baseline and Assumptions

### 2.1 Business and Operational Assumptions

| Parameter | Baseline | Target / Planning Assumption |
|---|---:|---:|
| B2B customers | 350 | 350 |
| Monthly payment events | 35,000,000 | 35,000,000 |
| Monthly payment transactions | 10,000,000 | 10,000,000 |
| False positive rate | 8.1% | ≤5.0% within 6 months |
| Monthly manual reviews | 100,000 | ≤70,000 |
| Average cost per manual review | $2.50 | ≤$2.00 |
| Monthly manual review cost | $250,000 | ≤$140,000 |
| Annual manual review cost | $3,000,000 | ≤$1,680,000 |
| Assumed monthly MLOps budget | — | $140,000 |

All figures represent hypothetical planning assumptions rather than verified financial or production data.

The false positive rate and manual review volume use different measurement denominators. Therefore, the reduction in FPR does not translate directly into the same percentage reduction in manual review volume.

### 2.2 Expected Operational Benefits

The projected monthly manual review savings are:

**$250,000 − $140,000 = $110,000 per month**

At a full year of target-level performance:

**Annualized gross savings = $110,000 × 12 = $1,320,000**

This represents a **44% reduction** in manual review operating costs.

The benefits are expected from two complementary improvements:

1. **Classical ML:** Better risk classification reduces unnecessary manual reviews.
2. **LLM/RAG Copilot:** Faster investigations reduce the average cost per review.

These savings are gross operational benefits, before deducting AI infrastructure, external LLM usage, personnel, implementation, and ongoing platform operating costs.

### 2.3 Financial Modeling Principles

The three-year financial model will:

- Separate one-time implementation costs from recurring operating expenses.
- Include compute, storage, networking, tooling, external LLM APIs, and personnel.
- Calculate ML inference and LLM Copilot unit economics.
- Account for a six-month benefit realization period.
- Compare gross operational benefits with incremental platform costs.
- Evaluate break-even conditions and sensitivity to transaction and LLM usage volumes.
- Identify financial risks and assumptions requiring validation.

The $140,000 monthly MLOps budget is treated as a planning ceiling, not automatically as actual monthly expenditure. The detailed TCO model will estimate costs by category and compare them with this ceiling.

The financial assessment will distinguish between operational efficiency gains and a positive investment return. A reduction in manual review costs alone does not guarantee a positive ROI.

