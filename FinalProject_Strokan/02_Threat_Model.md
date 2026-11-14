
# PayGuard AI — Threat Model

**Document Status:** Draft  
**Document Owner:** Emma Wilson, Security & Compliance Lead  
**Contributors:** Nataliia Strokan (Senior AI Product Manager), Daniel Carter (Engineering Lead), Marcus Lee (ML/AI Lead), Alex Morgan (MLOps Engineer)  
**Version:** 1.0

## 1. Purpose and Scope

This threat model identifies and evaluates security, privacy, compliance, and AI-specific risks associated with the PayGuard AI platform.

PayGuard AI is a B2B SaaS solution serving approximately 350 fintech, payment service provider, marketplace, and merchant clients across EU and US markets. The platform processes approximately 35 million payment events per month.

The system combines:

- **Classical ML risk scoring:** Transaction risk assessment and anomaly detection.
- **LLM/RAG Operations Copilot:** Analyst assistance using approved policies, PSP procedures, and historical case information.
- **Human oversight:** Authorized analysts remain responsible for final risk decisions.

The threat model covers the production AI/ML lifecycle, including data ingestion, model training, deployment, inference, document retrieval, external LLM integration, monitoring, and incident response.

## 2. System Boundaries and Critical Assets

### 2.1 System Boundaries

The assessed environment includes:

- AWS infrastructure, including EKS, storage, networking, and IAM.
- Classical ML inference services deployed on EKS.
- MLflow experiment tracking and model registry.
- GitLab CI and Argo CD deployment workflows.
- Amazon OpenSearch Service for RAG retrieval.
- External commercial LLM API accessed through a controlled backend.
- Prometheus, Grafana, and Loki monitoring infrastructure.
- Analyst-facing application and authorized B2B integrations.

External LLM provider infrastructure and customer-managed systems are outside PayGuard AI's direct operational control. Their interfaces, data exchanges, and associated risks remain in scope.

### 2.2 Critical Assets

| Asset | Security Importance |
|---|---|
| Payment transaction and risk data | Confidentiality, integrity, regulatory compliance |
| Customer and tenant information | Confidentiality, strict tenant isolation |
| ML training datasets and model artifacts | Integrity, reproducibility, traceability |
| ML inference endpoints | Availability, integrity of risk predictions |
| RAG documents and vector indexes | Confidentiality, document integrity, retrieval accuracy |
| LLM prompts, responses, and configurations | Confidentiality, output integrity, auditability |
| API credentials and infrastructure secrets | Confidentiality, access control |
| CI/CD configurations and deployment manifests | Integrity, controlled production changes |
| Audit logs and monitoring records | Integrity, availability, incident investigation |

## 3. Threat Actors

The assessment considers external attackers, malicious or compromised tenant users, unauthorized insiders, compromised service accounts, and third-party service failures.

It also considers AI-specific attack sources, including malicious instructions embedded in retrieved documents, poisoned training data, and manipulated model inputs.

Threats may result from deliberate attacks, configuration errors, operational mistakes, or unexpected model behavior.

