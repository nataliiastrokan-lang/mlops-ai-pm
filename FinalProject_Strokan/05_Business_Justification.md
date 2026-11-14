
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
