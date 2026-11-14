
# PayGuard AI — Threat Model

**Document Status:** Proposed  
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


## 4. Attack Surface Overview

PayGuard AI combines transaction processing, ML inference, RAG retrieval, and an external LLM service. Each integration introduces potential entry points for malicious inputs, unauthorized access, data leakage, or operational disruption.

### 4.1 External APIs and Data Ingestion

B2B customers submit payment events through authenticated APIs. Potential threats include stolen API credentials, malformed requests, excessive traffic, and manipulated transaction attributes.

Training data may also contain incorrect or deliberately altered records that affect model quality.

**Security controls:** API authentication, rate limiting, schema validation, data integrity checks, and tenant-specific authorization.

### 4.2 ML Training, Registry, and Inference

The ML lifecycle includes training datasets, MLflow experiments, model artifacts, and production inference endpoints.

Attackers or compromised internal accounts could manipulate training data, replace approved model artifacts, or deploy unauthorized model versions. Model degradation may also occur without malicious activity.

**Security controls:** Restricted registry permissions, artifact integrity verification, version traceability, deployment approvals, and continuous model-quality monitoring.

### 4.3 LLM and RAG Components

The Operations Copilot retrieves information from policy documents, PSP procedures, and historical cases before sending authorized context to a commercial LLM API.

The primary AI-specific threats include:

- Prompt injection through retrieved documents or user inputs.
- Cross-tenant information disclosure during retrieval.
- Hallucinated recommendations or unsupported compliance interpretations.
- Exposure of sensitive information to an external LLM provider.
- Retrieval of outdated or manipulated policies.

Retrieved documents must be treated as untrusted content, even when stored in an approved index.

**Security controls:** Tenant authorization before retrieval, document provenance checks, prompt injection testing, context minimization, output validation, source citations, and human oversight.

### 4.4 CI/CD and Infrastructure

GitLab CI, Argo CD, Terraform, and AWS EKS control infrastructure and application releases.

Compromised CI/CD credentials, unauthorized configuration changes, insecure container images, or excessive IAM permissions could affect production integrity and availability.

**Security controls:** Least-privilege IAM/RBAC, protected branches, secret management, container scanning, deployment approval gates, and audit logging.

### 4.5 Monitoring and Operational Access

Prometheus, Grafana, and Loki collect operational telemetry, including application errors, model performance, and deployment events.

Monitoring systems may expose sensitive data if logs contain payment information, customer identifiers, prompts, or retrieved document content.

**Security controls:** Log redaction, access restrictions, retention policies, encryption, and monitoring of privileged activities.

## 5. Trust Boundaries

A trust boundary exists whenever data or control moves between components with different security responsibilities.

| Boundary | Main Risk | Required Control |
|---|---|---|
| B2B customer → PayGuard API | Unauthorized requests or manipulated data | Authentication, validation, tenant authorization |
| Data ingestion → ML training | Data poisoning or integrity loss | Dataset validation, provenance, access controls |
| MLflow registry → Production EKS | Unauthorized or incorrect model deployment | Artifact verification, release approvals |
| Analyst → RAG retrieval | Access to another tenant's information | Server-side authorization and tenant isolation |
| RAG documents → LLM context | Prompt injection or outdated instructions | Document validation, context filtering |
| PayGuard backend → External LLM API | Sensitive data disclosure or provider dependency | Data minimization, encryption, vendor assessment |
| GitLab CI / Argo CD → Production | Unauthorized changes or compromised releases | Least privilege, protected workflows, audit logs |
| Production services → Monitoring | Leakage through logs and telemetry | Redaction, restricted access, retention controls |

### 5.1 Security Responsibility

PayGuard AI is responsible for protecting its infrastructure, enforcing tenant isolation, validating model releases, and controlling information transmitted to external services.

The external LLM provider is responsible for the security of its managed infrastructure under the applicable contractual terms.

Security & Compliance must verify that provider controls, data processing conditions, and applicable residency requirements are acceptable before production use.

Human analysts retain responsibility for final payment risk decisions. The LLM Copilot provides supporting information and must not independently authorize or reject transactions.


## 6. Risk Scoring Methodology

PayGuard AI uses a 3×3 risk assessment matrix to evaluate security, operational, compliance, and AI-specific threats.

Each threat receives an **Impact** score and a **Likelihood** score on a scale of 1–3.

**Risk Score = Impact × Likelihood**

### 6.1 Impact Assessment

| Score | Level | Definition |
|---|---|---|
| 1 | Low | Limited operational disruption, minor quality degradation, or negligible financial impact |
| 2 | Medium | Measurable service degradation, increased manual review costs, or a contained security incident |
| 3 | High | Significant financial loss, sensitive data exposure, regulatory consequences, or disruption of critical payment risk operations |

### 6.2 Likelihood Assessment

| Score | Level | Definition |
|---|---|---|
| 1 | Unlikely | Requires uncommon conditions or multiple control failures |
| 2 | Possible | Credible scenario that could occur under normal operating conditions |
| 3 | Likely | High exposure or a recurring failure pattern without additional controls |

### 6.3 Risk Priority Matrix

| Impact \ Likelihood | 1 — Unlikely | 2 — Possible | 3 — Likely |
|---|---|---|---|
| **3 — High** | P2 (3) | P1 (6) | P1 (9) |
| **2 — Medium** | P3 (2) | P2 (4) | P1 (6) |
| **1 — Low** | P3 (1) | P3 (2) | P3 (3) |

### 6.4 Priority and Escalation Rules

| Priority | Risk Scores | Required Response |
|---|---|---|
| P1 — Critical | 6–9 | Immediate mitigation planning, accountable owner, and Security & Compliance escalation; unresolved P1 risks block production approval |
| P2 — High | 3–4, where Impact = 3 or score = 4 | Documented mitigation plan, assigned owner, and validation before production or formal risk acceptance |
| P3 — Moderate/Low | 1–3, excluding Impact = 3 | Track through standard risk management and periodic review |

Risk priorities represent the **initial risk level before mitigation**. Residual risks must be reassessed after controls are implemented and validated.

### 6.5 Risk Register Requirements

Each identified threat must include:

- Threat ID and category.
- Threat scenario and affected asset.
- Impact and likelihood scores.
- Risk priority.
- Potential business or compliance impact.
- Proposed mitigation and detection controls.
- Accountable owner.
- Mitigation status.

All financial impacts in the threat register are planning estimates for the hypothetical PayGuard AI business, not historical incident losses.

The Security & Compliance Lead owns the consolidated risk register. Technical owners are responsible for implementing and validating their assigned mitigations.
