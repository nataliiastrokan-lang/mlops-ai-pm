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


---

## ADR-003: Model Registry Strategy

**Status:** Proposed  
**Decision Owner:** Marcus Lee, ML/AI Lead  
**Consulted:** Alex Morgan (MLOps Engineer), Daniel Carter (Engineering Lead), Emma Wilson (Security & Compliance Lead)

### Context and Problem

PayGuard AI requires reliable tracking, versioning, and governance of classical ML models used for payment risk scoring.

Each production model must be traceable to its training run, parameters, evaluation metrics, and approved artifacts. This is essential for controlled releases, rollback, auditability, and investigation of model-quality incidents.

The registry must integrate with the existing AWS EKS, GitLab CI, and Argo CD architecture while supporting regulated B2B operations.

### Alternatives Considered

| Option | Advantages | Disadvantages |
|---|---|---|
| A. Self-hosted MLflow | Open-source flexibility; experiment tracking and model registry in one platform; integration with existing MLOps workflows | Requires infrastructure maintenance, access controls, backups, and availability management |
| B. Amazon SageMaker Model Registry | Managed AWS integration; model versioning and approval workflows; reduced registry maintenance | Additional service dependency and potentially higher platform costs |
| C. Combined MLflow + SageMaker | MLflow experimentation with AWS-managed registry governance | Duplicate metadata, additional integrations, and increased operational complexity |

### Decision

**Select Option A: Self-hosted MLflow as the primary model registry.**

- Use MLflow Tracking to record training runs, parameters, metrics, and artifacts.
- Register validated classical ML model versions in MLflow Model Registry.
- Maintain explicit model lifecycle states: candidate, staging-approved, production-approved, and archived.
- Require documented evaluation evidence and approval before production promotion.
- Store model artifacts in controlled AWS storage with encryption and restricted access.
- Integrate approved model versions with GitLab CI and Argo CD deployment workflows.
- Retain previous validated model versions for rollback and audit purposes.

LLM provider model identifiers, prompt versions, retrieval configuration, and evaluation results will be tracked separately as versioned application configuration and release evidence.

### Rationale

MLflow provides the experiment tracking and model governance capabilities required for PayGuard AI without introducing a second model management platform.

It also builds on the MLOps processes already established during earlier project work, reducing implementation complexity and supporting consistent model evaluation.

The selected approach prioritizes traceability, controlled production promotion, and operational cost management.

### Consequences and Risks

**Positive:**
- Centralized experiment tracking and model versioning.
- Traceability between training results, approved models, and production deployments.
- Reduced platform complexity compared with maintaining two registries.

**Negative / Risks:**
- PayGuard AI is responsible for MLflow availability, upgrades, and backups.
- Misconfigured registry permissions could allow unauthorized model changes.
- Registry metadata and deployed model versions could become inconsistent.

**Required Mitigations:**
- Enforce least-privilege access using IAM and role-based permissions.
- Protect artifact storage through encryption and access restrictions.
- Maintain backups and recovery procedures for registry metadata and artifacts.
- Require CI validation and authorized approval for production model promotion.
- Monitor registry availability and verify deployed model versions against approved release records.

### Validation and Review

**Owners:** ML/AI Lead and MLOps Engineer.

Validate model registration, approval controls, artifact integrity, version traceability, and rollback procedures before production release.

Security & Compliance must approve registry access controls and auditability.

Reassess the registry strategy if operational overhead, reliability, or regulatory requirements exceed the capabilities of the selected approach.


---

## ADR-004: RAG Storage Strategy and Tenant Isolation

**Status:** Proposed  
**Decision Owner:** Daniel Carter, Engineering Lead  
**Consulted:** Marcus Lee (ML/AI Lead), Alex Morgan (MLOps Engineer), Emma Wilson (Security & Compliance Lead)

### Context and Problem

PayGuard AI's LLM/RAG Copilot helps payment risk analysts investigate suspicious transactions by retrieving relevant PSP procedures, compliance policies, and historical case information.

The platform serves 350 B2B clients across EU and US markets. The retrieval system must prevent unauthorized access to customer-specific information while providing accurate, up-to-date context for LLM-generated recommendations.

A cross-tenant data leak could expose sensitive financial information, create regulatory consequences, and damage customer trust.

### Alternatives Considered

| Option | Advantages | Disadvantages |
|---|---|---|
| A. Pinecone | Managed vector database; fast implementation; scalable retrieval | External vendor dependency; additional data residency and cost considerations |
| B. PostgreSQL with pgvector | Familiar relational database; flexible metadata filtering; potentially lower cost at moderate scale | Requires database tuning and capacity management as vector workloads grow |
| C. Amazon OpenSearch Service | AWS integration; vector and keyword search; metadata filtering and access controls | Operational configuration complexity; infrastructure costs must be monitored |

### Decision

**Select Option C: Amazon OpenSearch Service with vector search capabilities.**

- Store approved policy documents, PSP procedures, and authorized case knowledge in a controlled retrieval index.
- Associate documents and retrieval requests with tenant identifiers and access permissions.
- Enforce tenant authorization in the backend before retrieval; do not rely on LLM instructions or metadata filtering alone.
- Apply document versioning and validity checks to prevent outdated policies from being presented as current.
- Return source references with retrieved content to support evidence-based analyst review.
- Apply data minimization and sensitive information filtering before passing retrieved context to the commercial LLM API.
- Maintain audit logs of retrieval requests, access decisions, and document versions.

### Rationale

Amazon OpenSearch Service supports the combination of semantic and keyword retrieval needed for compliance documentation and investigation workflows.

Its integration with the existing AWS environment simplifies infrastructure governance and supports consistent security monitoring.

The selected approach prioritizes retrieval accuracy, document traceability, and tenant isolation over minimizing infrastructure complexity.

### Consequences and Risks

**Positive:**
- Centralized retrieval of relevant operational and compliance knowledge.
- Support for hybrid semantic and keyword search.
- Integration with AWS security and monitoring controls.

**Negative / Risks:**
- Additional infrastructure and operational costs.
- Risk of cross-tenant information exposure through incorrect access configuration.
- Retrieval quality may deteriorate when documents are outdated, incomplete, or incorrectly indexed.

**Required Mitigations:**
- Enforce tenant-level authorization and verify isolation through automated security tests.
- Restrict index access using IAM, network controls, and least-privilege permissions.
- Implement document lifecycle management, including versioning and expiration rules.
- Evaluate retrieval relevance, citation accuracy, and prompt injection resistance before production release.
- Monitor retrieval failures, unauthorized access attempts, and indexing errors.

### Validation and Review

**Owners:** Engineering Lead and Security & Compliance Lead.

Production approval requires successful tenant-isolation testing, retrieval-quality evaluation, access-control verification, and review of data residency requirements.

The ML/AI Lead validates retrieval quality, while the MLOps Engineer validates monitoring and operational readiness.

Reassess the storage strategy if retrieval quality, operational cost, scalability, or compliance requirements change materially.
