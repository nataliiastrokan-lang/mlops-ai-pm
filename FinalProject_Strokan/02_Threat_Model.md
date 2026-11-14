
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


## 7. Threat Register

The register identifies initial risks before mitigation. Priorities are calculated using the Impact × Likelihood methodology defined in Section 6.

**Status definitions:**
- **Planned:** Mitigation identified but not yet implemented.
- **In Progress:** Controls are being implemented or tested.
- **Pending Validation:** Controls implemented but effectiveness not yet confirmed.

### 7.1 Security, Data, and Infrastructure Threats

| ID | Threat Scenario / Affected Asset | Impact | Likelihood | Priority | Potential Business Impact | Mitigation and Detection Controls | Owner | Status |
|---|---|---|---|---|---|---|---|---|
| T01 | Cross-tenant access through incorrectly configured APIs | 3 | 2 | **P1 (6)** | Exposure of customer payment data; incident response and contractual costs potentially exceeding $250K | Server-side tenant authorization, automated isolation tests, access anomaly alerts | Emma Wilson | Planned |
| T02 | Compromised API credentials enabling unauthorized requests | 3 | 2 | **P1 (6)** | Unauthorized access or service misuse; estimated $50K–$200K response and recovery exposure | Credential rotation, least privilege, rate limiting, suspicious activity detection | Daniel Carter | Planned |
| T03 | Manipulated training data degrading ML risk predictions | 3 | 2 | **P1 (6)** | Increased fraud losses or false positives; potential $100K+ operational impact | Dataset provenance, integrity checks, anomaly detection, controlled training access | Marcus Lee | Planned |
| T04 | Unauthorized model artifact replacement in MLflow | 3 | 1 | **P2 (3)** | Incorrect production scoring; emergency rollback and investigation costs of $25K–$100K | Registry RBAC, artifact integrity verification, release approval gates | Alex Morgan | Planned |
| T05 | CI/CD compromise causing unauthorized production deployment | 3 | 2 | **P1 (6)** | Production disruption or compromised scoring; potential $100K–$300K impact | Protected branches, short-lived credentials, signed artifacts, deployment audit logs | Alex Morgan | Planned |
| T06 | Excessive IAM permissions exposing production resources | 3 | 1 | **P2 (3)** | Sensitive data exposure or infrastructure compromise; potential $100K+ impact | Least-privilege IAM, periodic access reviews, privilege escalation alerts | Emma Wilson | Planned |
| T07 | ML inference service outage or capacity exhaustion | 3 | 2 | **P1 (6)** | Delayed risk decisions and customer disruption; estimated $20K–$80K per major incident | Autoscaling, load testing, availability alerts, tested recovery procedures | Daniel Carter | Planned |
| T08 | Sensitive payment information exposed through application logs | 3 | 1 | **P2 (3)** | Privacy incident, remediation costs, and regulatory exposure | Log redaction, retention controls, restricted access, automated sensitive-data scans | Emma Wilson | Planned |
| T09 | Model drift causing increased false positives | 2 | 3 | **P1 (6)** | Manual review costs remain near the $250K monthly baseline or increase further | Drift monitoring, FPR alerts, shadow evaluation, rollback and retraining triggers | Marcus Lee | Planned |
| T10 | Backup or recovery failure affecting MLflow and model artifacts | 2 | 2 | **P2 (4)** | Extended recovery time, release delays, and reconstruction costs of $20K–$60K | Automated backups, restoration tests, artifact replication, recovery objectives | Alex Morgan | Planned |
| T11 | Monitoring gaps delaying detection of production incidents | 2 | 2 | **P2 (4)** | Longer outages, delayed escalation, and increased operational losses | Alert coverage reviews, synthetic checks, dashboard validation, incident exercises | Alex Morgan | Planned |

### 7.2 LLM/RAG-Specific Threats

| ID | Threat Scenario / Affected Asset | Impact | Likelihood | Priority | Potential Business Impact | Mitigation and Detection Controls | Owner | Status |
|---|---|---|---|---|---|---|---|---|
| T12 | Prompt injection embedded in retrieved policy documents | 3 | 2 | **P1 (6)** | Manipulated analyst recommendations or unauthorized disclosure; potential $50K–$200K impact | Treat retrieved content as untrusted, injection testing, tool restrictions, output validation | Marcus Lee | Planned |
| T13 | Cross-tenant document retrieval through incorrect RAG filtering | 3 | 2 | **P1 (6)** | Confidential customer information leakage; potential $250K+ response and contractual exposure | Backend tenant authorization, isolation tests, retrieval audit logs, access alerts | Emma Wilson | Planned |
| T14 | LLM hallucination producing incorrect compliance guidance | 3 | 2 | **P1 (6)** | Incorrect investigations, regulatory near-misses, and remediation costs | Grounded responses, source citations, evaluation datasets, human approval | Marcus Lee | Planned |
| T15 | Sensitive data transmitted unnecessarily to external LLM provider | 3 | 1 | **P2 (3)** | Privacy or contractual violations; potentially significant regulatory exposure | PII filtering, context minimization, vendor assessment, request logging controls | Emma Wilson | Planned |
| T16 | Outdated or poisoned RAG documents influencing responses | 2 | 3 | **P1 (6)** | Repeated incorrect policy guidance, rework, and compliance investigation costs | Document provenance, validity metadata, approval workflow, index freshness alerts | Rachel Adams | Planned |
| T17 | LLM API outage or severe latency degradation | 2 | 2 | **P2 (4)** | Reduced analyst productivity and increased manual handling cost | Provider health monitoring, timeouts, circuit breakers, manual workflow fallback | Daniel Carter | Planned |
| T18 | Excessive LLM token consumption through misuse or uncontrolled context | 2 | 2 | **P2 (4)** | Unexpected API expenditure; illustrative additional cost of $10K–$30K per month | Token budgets, request quotas, usage alerts, context-size limits | Alex Morgan | Planned |

### 7.3 Initial Risk Summary

| Category | Number of Threats |
|---|---:|
| Security, data, and infrastructure | 11 |
| LLM/RAG-specific | 7 |
| **Total** | **18** |

| Priority | Number of Threats |
|---|---:|
| P1 — Critical | 9 |
| P2 — High | 9 |
| P3 — Moderate/Low | 0 |
| **Total** | **18** |

The initial assessment identifies nine P1 risks requiring mitigation and validation before production approval.

These are planning-level risk estimates, not observed incidents. Financial exposure ranges describe illustrative consequences of individual threats and must not be summed as expected annual losses.

The absence of P3 risks reflects the register's focus on material threats to production readiness rather than an absence of lower-priority operational risks.


## 8. Risk Treatment, Residual Risk, and Governance

### 8.1 Risk Treatment Plan

All nine identified P1 risks require documented mitigation and validation before production approval.

Risk treatment priorities are:

1. **Data protection and tenant isolation:** Prevent unauthorized access to payment information and customer-specific RAG documents (T01, T02, T13).
2. **ML integrity and reliability:** Protect training data, deployment pipelines, inference availability, and model quality (T03, T05, T07, T09).
3. **LLM/RAG safety:** Prevent prompt injection, unsupported compliance guidance, and use of outdated documents (T12, T14, T16).
4. **Operational resilience:** Address P2 risks through access controls, monitoring, recovery procedures, and cost guardrails.

Each mitigation must have an accountable owner, implementation evidence, and validation results.

### 8.2 Residual Risk Assessment

Residual risk is evaluated after mitigation controls have been implemented and tested.

The responsible technical owner proposes updated Impact and Likelihood scores. Security & Compliance reviews the evidence and confirms the residual priority.

Residual risks must not be marked as accepted solely because controls have been documented.

**Risk acceptance rules:**

- **P1:** Unresolved P1 risks block production release.
- **P2:** Require validated mitigation or documented acceptance by Security & Compliance and the CPO.
- **P3:** May be accepted by the responsible owner with periodic review.

Any exception involving customer data exposure, regulatory obligations, or unauthorized AI decision-making requires explicit Security & Compliance approval and cannot override applicable legal requirements.

### 8.3 Production Go/No-Go Criteria

Production approval requires:

- All P1 mitigation controls implemented and validated.
- Successful cross-tenant isolation and access-control testing.
- Approved ML model evaluation and rollback readiness.
- Successful LLM/RAG security and quality evaluations.
- Confirmed monitoring, alerting, and incident response ownership.
- Documented residual risks and required approvals.

The **Senior AI Product Manager** coordinates the Go/No-Go review.

The **Engineering Lead** confirms technical readiness, the **ML/AI Lead** confirms model quality, and the **Security & Compliance Lead** confirms security and compliance readiness.

The **CPO** provides final business release authorization, subject to mandatory security and compliance gates.

### 8.4 Ongoing Governance

The threat model is reviewed:

- Before major production releases.
- After significant security, model-quality, or LLM incidents.
- When introducing new external providers or sensitive data flows.
- At least quarterly during normal operations.

Changes to risk scores, mitigation status, ownership, or acceptance decisions must be recorded and traceable.

Threat scenarios will inform production release checklists, monitoring requirements, incident response procedures, and stakeholder reporting.
