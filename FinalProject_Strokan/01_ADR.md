# Architectural Decision Records — PayGuard AI
Status: Draft


## High-Level Architecture & Technology Stack

### Architecture Overview

PayGuard AI is a hybrid AI-powered payment risk and operations platform combining classical machine learning for transaction risk assessment with an LLM/RAG Copilot for analyst investigations.

The platform supports payment service providers, fintech companies, marketplaces, and merchants operating in regulated EU and US markets.

### Core Components

| Component | Purpose |
|---|---|
| Payment Event Ingestion | Receives and validates payment events from B2B client integrations. |
| Feature Processing | Prepares transaction attributes and risk-related features for ML inference. |
| ML Risk Scoring | Evaluates payment risk and identifies potentially suspicious transactions. |
| Risk Decision & Review Routing | Applies configurable risk policies and determines whether a case requires manual review. |
| Analyst Case Management | Supports investigation, evidence review, and human decision-making. |
| LLM/RAG Copilot | Retrieves relevant policies, PSP documentation, procedures, and historical case information to assist analysts. |
| Monitoring & Observability | Tracks system health, model quality, operational KPIs, and AI-specific risks. |
| MLOps & Deployment | Manages model versions, release validation, deployment, monitoring, and rollback. |

### Processing Flow

1. A B2B client submits payment events through an integration interface.
2. The platform validates events and prepares features for ML inference.
3. The ML model produces a transaction risk score.
4. The risk decision layer applies configurable policies and routes selected cases for manual review.
5. Analysts investigate cases using the LLM/RAG Copilot to retrieve relevant contextual information.
6. Analysts make final decisions; the Copilot does not autonomously approve or reject transactions.
7. Operational outcomes, model performance, and system metrics are monitored to support continuous improvement.

### Technology Stack

| Area | Technology / Approach |
|---|---|
| Cloud Platform | AWS |
| Infrastructure as Code | Terraform |
| Container Orchestration | Amazon EKS |
| Environments | Dev and staging namespaces in a non-production cluster; separate production cluster |
| CI Pipeline | GitLab CI |
| GitOps Deployment | Argo CD |
| ML Experiment Tracking | MLflow |
| Monitoring | Prometheus and Grafana |
| Log Management | Loki |
| Workflow Orchestration | AWS Step Functions |
| Security | IAM, RBAC, secrets management, encryption, network isolation |
| AI Architecture | Classical ML risk scoring + LLM/RAG Copilot |
| Decision Governance | Human-in-the-loop for final risk decisions |

### Security and Compliance Principles

- Enforce tenant isolation across customer data and RAG retrieval.
- Apply least-privilege access through IAM and RBAC.
- Protect sensitive payment and personal information.
- Prevent unauthorized disclosure of data through LLM responses.
- Maintain audit trails for risk decisions, model versions, and production changes.
- Apply GDPR-related data protection controls where applicable.
- Require human oversight for final risk decisions.

# Architectural Decision Records — PayGuard AI

## ADR-001: Hybrid Model Hosting Strategy

**Status:** Proposed  
**Decision Owner:** Daniel Carter, Engineering Lead  
**Consulted:** Marcus Lee (ML/AI Lead), Alex Morgan (MLOps Engineer), Emma Wilson (Security & Compliance Lead)  
**Date:** 2026-11-14

### Context and Problem

PayGuard AI combines classical ML transaction risk scoring with an LLM/RAG Copilot supporting payment risk analysts. The platform serves regulated B2B customers in the EU and US and processes approximately 35 million payment events per month.

The classical ML component requires predictable inference latency, controlled model versions, and reliable availability. The LLM Copilot requires language generation and contextual retrieval but is not authorized to make final payment risk decisions.

The hosting strategy must balance operational cost, scalability, security, deployment control, and time to market within the assumed $140,000 monthly MLOps budget.

### Alternatives Considered

| Option | Advantages | Disadvantages |
|---|---|---|
| A. Fully self-hosted on AWS EKS | Maximum infrastructure and model deployment control; reduced dependency on external inference APIs | High GPU costs for LLM hosting; greater maintenance and scaling complexity |
| B. Commercial AI APIs | Faster implementation; managed LLM infrastructure; reduced operational overhead | Vendor dependency; variable token costs; data processing and residency concerns |
| C. Hybrid hosting | Dedicated control over classical ML; managed LLM inference; independent scaling of both components | More complex integration, monitoring, and vendor governance |

### Decision

**Select Option C: Hybrid Hosting.**

- Deploy the classical ML risk-scoring service on AWS EKS using containerized inference workloads.
- Use a commercial LLM API for the analyst-facing Copilot, accessed through a controlled backend service.
- Keep RAG retrieval, tenant authorization, and sensitive data filtering within PayGuard AI's controlled AWS environment.
- Send only the minimum necessary, authorized context to the LLM provider.
- Require vendor security, data residency, retention, and contractual assessments before production approval.
- Maintain human-in-the-loop decision-making; LLM responses remain advisory.

The specific LLM provider will be selected following security evaluation and cost benchmarking.

### Rationale

The hybrid approach provides deployment control and predictable capacity planning for high-volume classical ML inference without requiring PayGuard AI to operate GPU-intensive LLM infrastructure.

Managed LLM inference reduces initial engineering effort and supports faster delivery of the Copilot. Separating the two components also allows independent scaling and cost monitoring.

This approach supports the business objective of reducing false positives and manual review costs while preserving fraud detection effectiveness and compliance controls.

### Consequences and Risks

**Positive:**
- Lower initial infrastructure complexity for the LLM component.
- Independent scaling and deployment of ML and LLM workloads.
- Clear separation of transaction risk scoring and analyst assistance.

**Negative / Risks:**
- Dependency on an external LLM provider for Copilot availability.
- Variable API costs driven by token consumption.
- Potential exposure of sensitive information if retrieval and filtering controls fail.

**Required Mitigations:**
- Apply tenant-level authorization, data minimization, and PII filtering before LLM requests.
- Monitor LLM token usage, latency, errors, and cost per investigation.
- Provide an analyst workflow without the Copilot during LLM provider outages.
- Establish provider incident escalation and contractual data protection requirements.

### Validation and Review

**Owners:** Engineering Lead and ML/AI Lead, with Security & Compliance approval.

Validate the decision through staging load tests, LLM quality evaluations, security assessments, and unit-cost estimates before production release.

Revisit the hosting strategy if LLM usage costs exceed approved thresholds, provider restrictions affect compliance, or production reliability becomes unacceptable.

**Related artifacts:** `02_Threat_Model.md`, `03_Release_Checklist.md`, `04_Incident_Runbook.md`, `05_Business_Justification.md`.


---

## ADR-002: Deployment Strategy — Canary vs Blue-Green vs A/B

**Status:** Proposed  
**Decision Owner:** Alex Morgan, MLOps Engineer  
**Consulted:** Daniel Carter (Engineering Lead), Marcus Lee (ML/AI Lead), James Miller (QA / AI Evaluation Engineer)

### Context and Problem

PayGuard AI processes approximately 35 million payment events per month. A defective ML model release could increase false positives, create additional manual reviews, or reduce fraud detection effectiveness.

The deployment strategy must minimize production risk while enabling controlled validation, measurable rollout decisions, and rapid rollback.

The LLM/RAG Copilot also requires controlled releases, particularly when changing prompts, retrieval logic, or model providers.

### Alternatives Considered

| Strategy | Advantages | Disadvantages |
|---|---|---|
| Canary | Gradual rollout; real production validation; limited initial exposure | Requires traffic routing, monitoring, and clear promotion criteria |
| Blue-Green | Fast environment switching and rollback; isolated deployment environments | Higher infrastructure cost; limited gradual validation |
| A/B Testing | Supports comparison of model performance and business outcomes | Requires careful experiment design; unsuitable as the primary safety mechanism for high-risk releases |

### Decision

**Select Canary deployment as the primary production release strategy.**

- Deploy new classical ML model versions to a limited share of production traffic, starting at 5%.
- Increase exposure progressively to 25%, 50%, and 100% after successful validation.
- Compare new and baseline models using false positive rate, fraud detection effectiveness, inference latency, and error rate.
- Apply automated technical rollback triggers and manual approval gates for model-quality promotion.
- Release LLM/RAG Copilot changes through staged deployment with evaluation of answer quality, retrieval accuracy, sensitive data exposure, and prompt injection resistance.
- Use GitLab CI for validation and Argo CD for GitOps-based deployment to AWS EKS.

### Rationale

Canary deployment reduces the impact of defective releases by limiting initial exposure and allowing performance comparison before full rollout.

This is particularly important because PayGuard AI's business objective depends on reducing the false positive rate from 8.1% to ≤5% without compromising fraud detection effectiveness.

A/B testing may complement Canary deployment for business experiments but will not replace production safety controls.

### Consequences and Risks

**Positive:**
- Lower production exposure during initial rollout.
- Measurable model-quality and operational validation.
- Controlled promotion and rollback decisions.

**Negative / Risks:**
- Additional traffic-routing and monitoring complexity.
- Statistical uncertainty when evaluating model quality on small traffic samples.
- Delayed fraud labels may prevent immediate assessment of detection effectiveness.

**Required Mitigations:**
- Use pre-production validation and shadow evaluation before Canary rollout.
- Monitor technical health continuously and model quality over an agreed evaluation window.
- Prevent automatic promotion when model-quality evidence is insufficient.
- Roll back to the last validated model version if release guardrails are breached.

### Validation and Review

**Owners:** MLOps Engineer and ML/AI Lead.

Promotion requires successful technical checks, model-quality evidence, and QA approval. Security or compliance blockers prevent production rollout.

The AI Product Manager coordinates the final Go/No-Go decision based on release readiness and business impact.

Review the strategy after major incidents or when production traffic and infrastructure requirements change.

