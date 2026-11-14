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

### Architectural Decisions Pending ADR Evaluation

The following decisions will be evaluated and documented in `01_ADR.md`:

1. **AI Hosting Strategy:** Managed services versus self-hosted workloads.
2. **Deployment Strategy:** Rolling, blue-green, or canary deployment.
3. **Model Registry Strategy:** MLflow registry versus alternative approaches.
4. **Monitoring Strategy:** Integrated observability stack and AI-specific quality monitoring.
5. **RAG Storage Strategy:** Vector storage, retrieval architecture, and tenant isolation.

Each ADR will document the alternatives, decision criteria, selected approach, trade-offs, consequences, and responsible owner.

