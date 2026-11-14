# PayGuard AI — Project Legend

Status: Draft

This document defines the shared product context,
business assumptions, architecture, and team roles
for the AI PM MLOps Governance Package.

## Team Structure & Governance

PayGuard AI operates with a lean cross-functional governance team of eight core roles, supported by shared engineering, data, SRE, and finance functions.

| Role | Owner | Primary Responsibility |
|---|---|---|
| CPO | Olivia Bennett | Product strategy, investment approval, board alignment |
| Senior AI Product Manager | Nataliia Strokan | Roadmap, KPIs, prioritization, business readiness, Go/No-Go coordination |
| Engineering Lead | Daniel Carter | System architecture, integrations, technical delivery |
| ML/AI Lead | Marcus Lee | Classical ML risk model, LLM/RAG evaluation, model quality and drift |
| MLOps Engineer | Alex Morgan | AWS EKS, CI/CD, MLflow, Argo CD, monitoring and rollback |
| Security & Compliance Lead | Emma Wilson | Threat modeling, GDPR, security controls and compliance readiness |
| QA / AI Evaluation Engineer | James Miller | Functional testing, AI evaluation, regression testing and release evidence |
| Customer Operations Lead | Rachel Adams | Customer incident communication, support and operational feedback |

### Governance Principles

- Each production risk, release criterion, and incident response action has a designated accountable owner.
- The Senior AI PM coordinates release readiness and Go/No-Go decisions.
- The ML/AI Lead owns classical ML and LLM/RAG quality, supported by engineering specialists.
- The MLOps Engineer owns deployment execution, infrastructure observability, and technical rollback.
- The Security & Compliance Lead approves security and regulatory readiness.
- The QA / AI Evaluation Engineer provides independent validation and release evidence.
- Critical unresolved security, quality, or infrastructure blockers prevent production release.
- The CPO approves major investments and material business-risk exceptions, subject to mandatory compliance requirements.
- Shared Finance supports TCO, unit economics, and ROI calculations.

## Business Objectives & KPIs

### Primary Product Objective

Improve the efficiency and cost-effectiveness of payment risk operations by reducing the false positive rate from **8.1% to ≤5% within six months**, while maintaining fraud detection effectiveness and regulatory compliance.

### Expected Business Impact

- Reduce unnecessary manual reviews and associated operational costs.
- Improve risk analysts' productivity and case resolution efficiency.
- Enable scalable payment risk operations without proportional increases in staffing.
- Maintain reliable fraud detection and compliance controls.

### Measurement Approach

The false positive rate is the primary measurable performance target. Supporting business and operational KPIs will quantify cost savings, review workload reduction, and analyst productivity improvements.

All targets represent planning assumptions for the hypothetical PayGuard AI product and will be validated through the Business Justification.


### Operational KPIs and Financial Baseline

The following assumptions establish the operational baseline and six-month targets for PayGuard AI. They will be used to estimate the business value of the proposed ML risk-scoring improvements and LLM/RAG-assisted investigations.

| Metric | Baseline | 6-Month Target |
|---|---:|---:|
| Monthly payment events | 35,000,000 | 35,000,000 |
| Monthly transactions (assumed) | 10,000,000 | 10,000,000 |
| False positive rate | 8.1% | ≤5.0% |
| Monthly manual reviews | 100,000 | ≤70,000 |
| Average cost per manual review | $2.50 | ≤$2.00 |
| Monthly manual review cost | $250,000 | ≤$140,000 |
| Annualized manual review cost | $3,000,000 | ≤$1,680,000 |

#### Expected Financial Impact

At the six-month target operating level:

- **Baseline monthly review cost:** 100,000 × $2.50 = $250,000
- **Target monthly review cost:** 70,000 × $2.00 = $140,000
- **Potential monthly gross savings:** $110,000
- **Potential annualized gross savings:** $1,320,000
- **Reduction in manual review expenditure:** 44%

#### Value Drivers

**Classical ML Risk Scoring**
- Reduces false positive alerts.
- Decreases the number of unnecessary manual reviews.
- Maintains fraud detection effectiveness through defined model-quality guardrails.

**LLM/RAG Operations Copilot**
- Accelerates case investigation by retrieving relevant policies, procedures, and historical case information.
- Supports analysts with contextual explanations and evidence summaries.
- Improves analyst productivity and reduces average handling cost per review.
- Preserves human oversight for final risk decisions.

#### Assumptions and Measurement Constraints

- All financial figures are hypothetical planning assumptions, not actual company results.
- Monthly payment events and monthly transactions are distinct measures.
- The false positive rate must be calculated against a clearly defined population of legitimate transactions; it must not be interpreted as the percentage of all transactions sent for manual review.
- The reduction in manual reviews is an operational target, not a direct mathematical conversion from the false positive rate.
- The $1.32 million figure represents annualized gross savings at the target operating level, not first-year realized savings or net ROI.
- Actual financial benefits depend on adoption, transaction volumes, review-routing rules, staffing costs, and implementation expenses.
- Savings from reduced review volume and lower cost per review will be calculated together to prevent double-counting.
- The detailed three-year TCO, implementation costs, adoption ramp-up, ROI, break-even analysis, and sensitivity scenarios will be documented in `05_Business_Justification.md`.
