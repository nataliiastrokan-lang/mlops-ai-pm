
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


## 3. Three-Year Total Cost of Ownership (TCO)

### 3.1 Cost Model and Assumptions

The TCO model estimates the incremental costs of implementing and operating PayGuard AI over three years.

The selected architecture includes:

- AWS EKS for classical ML inference and application workloads.
- Managed commercial LLM API for the Operations Copilot.
- Amazon OpenSearch Service for RAG retrieval.
- Self-hosted MLflow for experiment tracking and model registry.
- GitLab CI, Argo CD, Terraform, and Prometheus/Grafana/Loki for deployment and observability.

The model assumes a stable operating baseline of 35 million payment events per month, with no automatic annual volume growth. Volume changes are evaluated separately in the sensitivity analysis.

Recurring costs include dedicated AI/MLOps personnel. Shared corporate overhead and existing non-AI payment platform costs are excluded.

### 3.2 Estimated Monthly Operating Costs

| Cost Category | Monthly Cost | Annual Cost |
|---|---:|---:|
| AWS compute and EKS workloads | $18,000 | $216,000 |
| Storage, databases, and OpenSearch | $7,000 | $84,000 |
| Networking and data transfer | $3,000 | $36,000 |
| Commercial LLM API usage | $12,000 | $144,000 |
| Monitoring, logging, and tooling | $5,000 | $60,000 |
| Dedicated engineering, ML, and MLOps personnel | $45,000 | $540,000 |
| Security, compliance, and operational support | $10,000 | $120,000 |
| **Total recurring operating cost** | **$100,000** | **$1,200,000** |

The estimated recurring cost is **$100,000 per month**, representing approximately 71.4% of the assumed $140,000 monthly MLOps budget ceiling.

The remaining $40,000 per month is budget headroom, not a guaranteed saving or available cash benefit.

### 3.3 One-Time Implementation Costs

| Implementation Activity | Estimated Cost |
|---|---:|
| Architecture, infrastructure setup, and CI/CD integration | $65,000 |
| ML pipeline, model registry, and deployment automation | $55,000 |
| LLM/RAG integration and security controls | $60,000 |
| Testing, compliance validation, and production readiness | $40,000 |
| **Total one-time implementation cost** | **$220,000** |

These costs represent incremental implementation effort and external or allocated project resources. They are excluded from recurring personnel costs to avoid double-counting.

### 3.4 Three-Year TCO Projection

The model assumes constant recurring operating costs across the three-year period and recognizes all implementation expenditure in Year 1.

| Cost Component | Year 1 | Year 2 | Year 3 | 3-Year Total |
|---|---:|---:|---:|---:|
| Recurring operating costs | $1,200,000 | $1,200,000 | $1,200,000 | $3,600,000 |
| One-time implementation | $220,000 | $0 | $0 | $220,000 |
| **Total Cost of Ownership** | **$1,420,000** | **$1,200,000** | **$1,200,000** | **$3,820,000** |

**Three-year TCO = $3,820,000**

The estimate excludes inflation, major infrastructure expansion, unexpected regulatory changes, and future product development beyond the defined scope.

### 3.5 Cost Governance

The MLOps Engineer monitors infrastructure utilization and cloud expenditure. The ML/AI Lead monitors model inference efficiency and LLM token consumption.

The Senior AI Product Manager reviews actual expenditure against the approved budget and expected business benefits.

Recommended financial controls include:

- AWS cost allocation tags and monthly cost reporting.
- Budget alerts at 80%, 90%, and 100% of approved spending limits.
- LLM token quotas, context-size restrictions, and cost-per-investigation monitoring.
- Resource utilization reviews and removal of idle capacity.
- Quarterly TCO forecasts and variance analysis.

Any material increase in recurring costs requires review of its expected business value before additional spending is approved.


## 4. Unit Economics

### 4.1 Purpose and Calculation Basis

Unit economics measure the cost efficiency of PayGuard AI at the expected operating volume.

The model uses the following assumptions:

| Parameter | Monthly Value |
|---|---:|
| Payment events processed | 35,000,000 |
| Payment transactions scored by ML | 10,000,000 |
| Manual investigations | 100,000 |
| Estimated LLM-assisted investigations | 70,000 |
| AWS compute and EKS cost | $18,000 |
| Commercial LLM API cost | $12,000 |
| Total recurring platform cost | $100,000 |

For planning purposes, each payment transaction is assumed to receive one primary ML risk score.

The 70,000 LLM-assisted investigations represent an illustrative target-state usage assumption, not measured adoption.

### 4.2 ML Inference Unit Economics

The direct ML infrastructure cost is approximated using the monthly AWS compute and EKS allocation.

**Cost per 1,000 payment events:**

$18,000 / 35,000,000 × 1,000 = **$0.514**

**Direct compute cost per ML-scored transaction:**

$18,000 / 10,000,000 = **$0.0018**

This is equivalent to **$1.80 per 1,000 scored transactions**.

These figures represent allocated compute costs only. They do not include storage, networking, personnel, monitoring, or implementation expenditure.

### 4.3 LLM Copilot Unit Economics

The LLM Copilot uses a commercial API, with costs driven by request frequency, input/output tokens, and provider pricing.

**Assumed monthly LLM API expenditure:** $12,000

**Assumed LLM-assisted investigations:** 70,000

**LLM API cost per assisted investigation:**

$12,000 / 70,000 = **$0.1714**

The estimated direct LLM API cost is approximately **$0.17 per assisted investigation**.

This assumes that the monthly API budget covers all model calls associated with those investigations, including retries and supporting requests.

Actual unit cost must be validated through token-level usage monitoring and provider billing data.

### 4.4 Fully Allocated Platform Unit Economics

The full recurring operating cost includes infrastructure, LLM usage, tooling, dedicated personnel, security, and operational support.

**Recurring platform cost per 1,000 payment events:**

$100,000 / 35,000,000 × 1,000 = **$2.86**

**Recurring platform cost per ML-scored transaction:**

$100,000 / 10,000,000 = **$0.01**

These are alternative allocations of the same total monthly platform cost and must not be added together.

### 4.5 Unit Economics Summary

| Metric | Estimated Unit Cost |
|---|---:|
| Direct AWS compute per 1,000 payment events | $0.51 |
| Direct AWS compute per ML-scored transaction | $0.0018 |
| Direct LLM API per assisted investigation | $0.17 |
| Fully allocated recurring cost per 1,000 payment events | $2.86 |
| Fully allocated recurring cost per scored transaction | $0.01 |

### 4.6 Interpretation and Optimization Opportunities

At the assumed operating volume, ML inference has a relatively low direct compute cost per transaction.

LLM usage introduces variable expenditure that depends on investigation volume, token consumption, and provider pricing.

Potential optimization opportunities include:

- Batching or caching appropriate non-sensitive retrieval operations.
- Limiting unnecessary LLM calls and oversized context windows.
- Monitoring token usage and cost per investigation.
- Right-sizing EKS workloads and reducing idle infrastructure.
- Tracking the relationship between Copilot adoption, investigation handling time, and operational savings.

Unit economics should be reassessed monthly after launch using actual infrastructure utilization, API billing, and investigation activity.


## 5. Business Benefits, ROI, and Break-Even Analysis

### 5.1 Expected Operational Benefits

PayGuard AI aims to reduce manual review workload through improved ML risk classification and increase analyst efficiency through the LLM/RAG Copilot.

The target-state financial benefit is based on:

| Metric | Baseline | Target State |
|---|---:|---:|
| Monthly manual reviews | 100,000 | 70,000 |
| Average review cost | $2.50 | $2.00 |
| Monthly manual review expenditure | $250,000 | $140,000 |
| Monthly gross savings | — | $110,000 |
| Annualized gross savings | — | $1,320,000 |

These savings assume that reduced review volume and handling effort translate into actual avoided expenditure or productive capacity that can be financially realized.

Potential improvements in fraud losses, customer retention, and compliance outcomes are excluded from quantified benefits because supporting estimates are not available.

### 5.2 Benefit Realization Assumptions

The model assumes benefits increase linearly during the first six months, reaching the target monthly savings of $110,000 at the end of Month 6.

For simplicity, the average benefit during the first six months is assumed to be 50% of the target-state monthly savings.

**Year 1 gross benefits:**

- Months 1–6: 6 × $55,000 = $330,000
- Months 7–12: 6 × $110,000 = $660,000
- **Year 1 total: $990,000**

Years 2 and 3 assume sustained target-state savings of $1,320,000 annually.

This is a simplified planning model; actual benefit realization may be nonlinear and depends on adoption, model performance, and operational implementation.

### 5.3 Three-Year Financial Projection

| Financial Metric | Year 1 | Year 2 | Year 3 | Total |
|---|---:|---:|---:|---:|
| Gross operational benefits | $990,000 | $1,320,000 | $1,320,000 | $3,630,000 |
| Recurring operating costs | $1,200,000 | $1,200,000 | $1,200,000 | $3,600,000 |
| One-time implementation costs | $220,000 | $0 | $0 | $220,000 |
| **Net financial benefit** | **-$430,000** | **$120,000** | **$120,000** | **-$190,000** |

**Three-year net financial benefit = $3,630,000 − $3,820,000 = -$190,000**

### 5.4 Return on Investment

ROI is calculated as:

**ROI = (Total Benefits − Total Costs) / Total Costs × 100%**

For the three-year planning horizon:

**ROI = ($3,630,000 − $3,820,000) / $3,820,000 × 100%**

**Three-year ROI ≈ -5.0%**

The base-case financial model does not achieve a positive three-year ROI from manual review savings alone.

However, the platform approaches operating break-even after reaching target-state performance.

### 5.5 Operating Break-Even

At target-state performance:

- Monthly gross operational savings: $110,000
- Monthly recurring operating costs: $100,000
- **Monthly operating contribution: +$10,000**

The platform reaches monthly operating break-even when realized savings equal or exceed $100,000.

Under the linear six-month ramp-up assumption, monthly savings reach $100,000 near Month 5.5.

This represents operating break-even, not recovery of the initial investment.

### 5.6 Cumulative Investment Break-Even

The initial implementation cost and early operating losses create a cumulative deficit.

At the end of Year 3, the model projects a cumulative deficit of $190,000.

With a sustained positive operating contribution of $10,000 per month, an additional 19 months would be required to recover that deficit.

**Illustrative cumulative break-even: approximately Month 55.**

This estimate assumes stable costs and benefits beyond the three-year planning horizon. It excludes discounting, inflation, and future reinvestment.

### 5.7 Financial Interpretation

The proposed investment generates measurable operational improvements but does not fully recover its costs within three years under the base-case assumptions.

A positive investment decision would therefore require one or more of the following:

- Lower recurring platform costs through infrastructure and LLM optimization.
- Additional validated benefits from fraud-loss prevention or customer retention.
- Higher operational savings supported by actual pilot results.
- A longer investment horizon justified by strategic or compliance value.

The recommended approach is to proceed through controlled validation rather than commit to unrestricted production-scale expenditure before the expected benefits are demonstrated.


## 6. Sensitivity Analysis

### 6.1 Scenario Assumptions

The sensitivity analysis evaluates how changes in transaction volume affect PayGuard AI's operating costs and expected benefits.

Three scenarios are considered:

- **Low Volume (0.5×):** 17.5 million payment events per month.
- **Base Case (1×):** 35 million payment events per month.
- **High Volume (2×):** 70 million payment events per month.

The model assumes that transaction volume, manual review volume, and LLM-assisted investigations change proportionally.

For simplicity, the percentage reduction in manual review costs remains constant across scenarios.

This assumption must be validated because transaction risk distributions, customer behavior, and analyst workload may not scale linearly.

### 6.2 Cost Scaling Assumptions

The $100,000 monthly operating cost is divided into fixed and variable components.

| Cost Category | Base Monthly Cost | Scaling Assumption |
|---|---:|---|
| AWS compute and EKS | $18,000 | 70% variable, 30% fixed |
| Storage and OpenSearch | $7,000 | 60% variable, 40% fixed |
| Networking and data transfer | $3,000 | 100% variable |
| Commercial LLM API | $12,000 | 100% variable |
| Monitoring and tooling | $5,000 | 40% variable, 60% fixed |
| Engineering, ML, and MLOps personnel | $45,000 | Fixed within modeled capacity |
| Security, compliance, and operational support | $10,000 | Fixed within modeled capacity |

**Total fixed monthly cost: $62,600**

**Total variable monthly cost at base volume: $37,400**

The model assumes existing staffing and infrastructure can support the modeled range without major redesign. Capacity limits would require additional investment.

### 6.3 Monthly Scenario Comparison

| Metric | Low (0.5×) | Base (1×) | High (2×) |
|---|---:|---:|---:|
| Payment events | 17.5M | 35M | 70M |
| Scored transactions | 5M | 10M | 20M |
| LLM-assisted investigations | 35,000 | 70,000 | 140,000 |
| Gross operational savings | $55,000 | $110,000 | $220,000 |
| Fixed operating costs | $62,600 | $62,600 | $62,600 |
| Variable operating costs | $18,700 | $37,400 | $74,800 |
| **Total monthly operating costs** | **$81,300** | **$100,000** | **$137,400** |
| **Monthly operating contribution** | **-$26,300** | **+$10,000** | **+$82,600** |

The high-volume scenario remains below the assumed $140,000 monthly MLOps budget ceiling, but with limited headroom.

### 6.4 Three-Year Scenario Comparison

The model applies the same six-month benefit ramp-up as the base case and assumes constant operating costs within each scenario.

One-time implementation expenditure remains $220,000.

| Metric | Low (0.5×) | Base (1×) | High (2×) |
|---|---:|---:|---:|
| Three-year gross benefits | $1,815,000 | $3,630,000 | $7,260,000 |
| Three-year recurring costs | $2,926,800 | $3,600,000 | $4,946,400 |
| One-time implementation | $220,000 | $220,000 | $220,000 |
| **Three-year TCO** | **$3,146,800** | **$3,820,000** | **$5,166,400** |
| **Three-year net benefit** | **-$1,331,800** | **-$190,000** | **+$2,093,600** |
| **Three-year ROI** | **-42.3%** | **-5.0%** | **+40.5%** |

### 6.5 Interpretation

**Low-volume scenario:** The platform does not achieve operating break-even. Fixed costs remain significant relative to the reduced benefits.

**Base scenario:** The platform achieves positive monthly operating contribution after reaching target-state performance but does not recover its initial investment within three years.

**High-volume scenario:** The platform generates positive three-year ROI because benefits scale faster than the modeled operating costs.

However, this scenario depends on maintaining service quality, infrastructure capacity, and the same operational savings per transaction at twice the baseline volume.

### 6.6 Sensitivity Risks

The financial outcome is particularly sensitive to:

- Actual transaction and investigation volumes.
- Realized reduction in manual reviews and handling costs.
- LLM token consumption per investigation.
- Additional infrastructure requirements at higher volumes.
- Staffing increases required to maintain service quality.
- Model accuracy and fraud detection performance.

Before expanding production capacity, PayGuard AI should validate these assumptions through measured pilot results, cloud cost reports, and analyst productivity data.


## 7. Financial Risks and Investment Recommendation

### 7.1 Key Financial Risks

| Risk | Potential Impact | Mitigation | Owner |
|---|---|---|---|
| Lower-than-expected operational savings | Negative ROI and extended payback period | Validate FPR improvement, review volume, and handling cost through a controlled pilot | Nataliia Strokan |
| Higher LLM API consumption | Increased cost per investigation and budget overruns | Token budgets, context optimization, usage monitoring, and provider cost reviews | Marcus Lee |
| Infrastructure cost escalation | Reduced operating margin and potential budget breach | Capacity testing, AWS cost alerts, resource right-sizing, and quarterly forecasts | Alex Morgan |
| Insufficient analyst adoption | Lower productivity gains despite platform expenditure | Analyst UAT, training, adoption monitoring, and workflow improvements | Rachel Adams |
| Model-quality degradation | Increased manual reviews or fraud-related losses | Continuous evaluation, drift monitoring, Canary deployment, and rollback controls | Marcus Lee |
| Security or compliance incident | Remediation costs, contractual exposure, and potential regulatory consequences | Validate P1 mitigations, tenant isolation, data protection, and incident readiness | Emma Wilson |

### 7.2 Investment Recommendation

**Recommendation: Conditional Go for a controlled pilot; No-Go for unrestricted production-scale investment at this stage.**

The base-case financial model projects:

- Three-year TCO of **$3.82 million**.
- Three-year gross operational benefits of **$3.63 million**.
- Three-year net financial benefit of **−$190,000**.
- Three-year ROI of approximately **−5.0%**.
- Positive monthly operating contribution of **$10,000** after target-state performance is achieved.

The investment demonstrates potential operational value but does not achieve positive cumulative ROI within the three-year planning horizon under current assumptions.

A controlled pilot is recommended to validate model performance, analyst productivity, LLM usage costs, and operational savings before committing to full-scale deployment.

This recommendation is a business investment decision, not production release authorization. Any pilot involving production data or customer workflows must first satisfy applicable security, compliance, and release-readiness requirements.

### 7.3 Investment Decision Gates

| Gate | Required Evidence | Decision Owner |
|---|---|---|
| Gate 1 — Pilot Authorization | Approved scope, capped budget, success metrics, security controls, and accountable owners | Olivia Bennett |
| Gate 2 — Technical and Security Readiness | Validated critical controls, model evaluations, tenant isolation, monitoring, and rollback readiness | Emma Wilson / Daniel Carter |
| Gate 3 — Business Value Validation | Measured changes in FPR, manual review volume, handling cost, analyst adoption, and fraud detection effectiveness | Nataliia Strokan |
| Gate 4 — Financial Reassessment | Updated TCO, actual unit costs, benefit realization, ROI, and sensitivity scenarios | Nataliia Strokan / Olivia Bennett |
| Gate 5 — Production Expansion | Positive investment justification, approved residual risks, and completed production Go/No-Go checklist | Olivia Bennett |

The pilot must have predefined success criteria and a spending limit. Expansion should be deferred if critical safety controls fail, fraud detection effectiveness deteriorates, or projected benefits remain insufficient to justify recurring costs.

### 7.4 Proposed Financial Success Criteria

The following criteria are proposed for evaluation and require approval before the pilot begins:

- Demonstrate measurable FPR improvement toward the six-month target of ≤5.0%.
- Maintain approved fraud detection recall and precision guardrails.
- Validate the feasibility of reducing manual reviews from 100,000 to ≤70,000 per month.
- Demonstrate a credible path toward reducing average review cost from $2.50 to ≤$2.00.
- Measure actual LLM API cost per assisted investigation against the modeled $0.17.
- Confirm that projected monthly recurring operating costs remain within the $140,000 budget ceiling.
- Update the investment case using measured benefits and costs rather than planning assumptions alone.

### 7.5 Final Business Conclusion

PayGuard AI addresses a material operational challenge in payment risk management: increasing false positives, higher manual review costs, and the need for reliable compliance guidance.

The proposed hybrid AI architecture offers opportunities to improve risk-scoring efficiency and analyst productivity while maintaining human oversight.

However, the financial model shows that operational savings alone are insufficient to deliver a positive three-year ROI under the base-case assumptions.

**The recommended decision is to authorize a limited, controlled validation phase, subject to approved funding and mandatory security controls, and reassess full-scale investment based on demonstrated business value.**

The Senior AI Product Manager is accountable for consolidating the pilot results and presenting the updated investment recommendation to the CPO.
