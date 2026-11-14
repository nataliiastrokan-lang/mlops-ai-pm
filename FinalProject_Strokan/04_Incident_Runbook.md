
# PayGuard AI — Incident Response Runbook

**Document Status:** Draft  
**Document Owner:** Alex Morgan, MLOps Engineer  
**Business Owner:** Nataliia Strokan, Senior AI Product Manager  
**Version:** 1.0

## 1. Purpose and Scope

This runbook defines how PayGuard AI detects, escalates, contains, resolves, and reviews production incidents affecting its classical ML risk-scoring service and LLM/RAG Operations Copilot.

It addresses three incident scenarios:

1. ML model quality degradation.
2. LLM/RAG security or quality incidents.
3. Deployment and infrastructure failures.

The runbook supports operational continuity, protection of customer data, regulatory compliance, and controlled recovery.

## 2. Incident Severity and Response Targets

| Severity | Definition | Response Target | Illustrative MTTR Target |
|---|---|---|---|
| SEV-1 — Critical | Customer data exposure, material fraud detection failure, or widespread disruption of critical risk-scoring services | Acknowledge within 5 minutes | ≤60 minutes for containment or service restoration |
| SEV-2 — High | Significant model-quality degradation, Copilot safety issue, or partial service disruption | Acknowledge within 15 minutes | ≤4 hours |
| SEV-3 — Moderate | Limited degradation with an available workaround and no critical security impact | Acknowledge within 1 hour | ≤1 business day |

These targets are proposed operational objectives, not demonstrated historical performance. Security investigations and full corrective actions may take longer than initial containment or service restoration.

## 3. Incident Roles and Escalation

| Role | Responsibility |
|---|---|
| Incident Commander — Alex Morgan | Coordinates technical response, timeline, and escalation |
| Engineering Lead — Daniel Carter | Owns application and infrastructure recovery |
| ML/AI Lead — Marcus Lee | Investigates model quality, drift, and LLM/RAG behavior |
| Security & Compliance Lead — Emma Wilson | Leads security containment, privacy assessment, and regulatory escalation |
| QA / AI Evaluation Engineer — James Miller | Validates fixes and recovery criteria |
| Customer Operations Lead — Rachel Adams | Coordinates analyst workarounds and customer support |
| Senior AI Product Manager — Nataliia Strokan | Assesses business impact and coordinates stakeholder communications |
| CPO — Olivia Bennett | Makes executive decisions on material business impact and release resumption |

For suspected data exposure, Security & Compliance immediately leads the security investigation and determines applicable notification obligations.

## 4. Standard Incident Response Workflow

All three scenarios follow the same lifecycle:

**Detect → Triage → Contain → Diagnose → Recover → Validate → Communicate → Review**

### 4.1 First 15 Minutes — Common Actions

1. Acknowledge the alert and create an incident record.
2. Assign severity, Incident Commander, and technical responders.
3. Identify affected services, customers, and potential data exposure.
4. Freeze relevant production changes and preserve diagnostic evidence.
5. Apply immediate containment or activate a safe fallback when required.
6. Notify Security & Compliance for any suspected security or privacy incident.
7. Establish an incident communication channel and schedule the next status update.

### 4.2 Communication Rules

- **SEV-1:** Notify the CPO, Senior AI Product Manager, and relevant technical leads immediately; provide stakeholder updates every 15–30 minutes during active response.
- **SEV-2:** Notify responsible technical and business owners; provide updates at least hourly until stabilization.
- **SEV-3:** Track through standard operational reporting and escalate if impact increases.

External customer communications are coordinated by Customer Operations and approved by the appropriate business and compliance owners.

Regulatory notification decisions are made by Security & Compliance based on the incident facts and applicable requirements.

### 4.3 Recovery and Closure

An incident may be closed only after:

- The affected service or workflow is stable.
- Security and model-quality guardrails are satisfied.
- Recovery validation evidence is recorded.
- Relevant business and technical owners approve closure.
- Follow-up actions have owners and deadlines.

SEV-1 and SEV-2 incidents require a documented post-incident review covering root cause, impact, timeline, response effectiveness, and preventive actions.


## 5. Scenario 1 — ML Model Quality Degradation

**Related Threats:** T03 (Training Data Manipulation), T09 (Model Drift)  
**Default Severity:** SEV-2; escalate to SEV-1 if fraud detection effectiveness deteriorates materially or critical risk decisions are affected.  
**Incident Owner:** Marcus Lee, ML/AI Lead  
**Technical Support:** Alex Morgan, MLOps Engineer  
**Expected Frequency:** Occasional; assessed during ongoing model monitoring.  
**Target MTTR:** ≤4 hours for containment or restoration of the last validated model.

### 5.1 Detection and Triggers

Potential detection signals include:

- False positive rate exceeds the approved operational threshold.
- Fraud recall or precision falls below established guardrails when confirmed labels become available.
- Significant changes in input feature distributions or prediction scores.
- Increased manual review volume compared with the expected baseline.
- Model-quality alerts from Prometheus/Grafana or scheduled evaluation jobs.

Because confirmed fraud outcomes may arrive with a delay, drift and prediction-distribution alerts serve as early warning signals rather than definitive evidence of model failure.

### 5.2 First 15 Minutes

1. Acknowledge the alert and assign an incident severity.
2. Identify the affected model version, deployment time, and customer segments.
3. Compare current performance with the previous validated model and baseline metrics.
4. Check for recent changes in input data, features, model artifacts, or deployment configuration.
5. Pause further Canary promotion or model releases.
6. Notify the Engineering Lead, MLOps Engineer, and Senior AI Product Manager.
7. If critical fraud detection guardrails are breached, initiate rollback to the last validated model.

### 5.3 Diagnosis

The ML/AI Lead investigates:

- Data drift, feature pipeline changes, and missing or malformed inputs.
- Model version differences and MLflow experiment records.
- Recent changes in transaction patterns or customer segments.
- Discrepancies between offline evaluation and production performance.
- Delayed fraud labels and potential measurement errors.

The MLOps Engineer checks infrastructure health, deployment history, and monitoring accuracy.

### 5.4 Containment and Mitigation

- Stop Canary traffic expansion immediately.
- Route affected traffic to the last validated model version when rollback is justified.
- Restore the previous approved deployment configuration through GitLab CI and Argo CD.
- Increase manual review oversight for affected transaction segments when necessary.
- Preserve prediction samples, model identifiers, monitoring snapshots, and deployment records for investigation.

A retrained model must not be deployed directly to production without the required evaluation and release approvals.

### 5.5 Recovery Validation

Recovery is confirmed when:

- The approved model version is serving production traffic.
- Inference latency, error rate, and availability meet operational SLOs.
- Prediction distributions and available quality indicators return to acceptable ranges.
- Fraud detection guardrails are validated using available labels and representative evaluation datasets.
- Customer Operations confirms that review workflows are functioning.

The ML/AI Lead approves model-quality recovery. The Incident Commander confirms operational stability.

### 5.6 Communication and Escalation

The Senior AI Product Manager reports:

- Affected customer segments and estimated transaction volume.
- Observed changes in false positives, fraud detection indicators, and manual review workload.
- Containment actions and expected recovery timeline.
- Any potential impact on the six-month FPR improvement target.

Escalate to SEV-1 and involve the CPO immediately if the incident threatens critical fraud controls or causes widespread customer disruption.

### 5.7 Post-Incident Review and Prevention

Within two business days of stabilization, conduct a post-incident review covering:

- Root cause and contributing data or model changes.
- Detection effectiveness and time to containment.
- Business impact and affected customers.
- Rollback effectiveness and monitoring gaps.
- Corrective actions, owners, and deadlines.

Preventive improvements may include stronger data validation, more representative evaluation datasets, improved drift thresholds, and revised Canary promotion criteria.


## 6. Scenario 2 — LLM/RAG Security or Quality Incident

**Related Threats:** T12 (Prompt Injection), T13 (Cross-Tenant Retrieval), T14 (LLM Hallucination), T15 (Sensitive Data Exposure), T16 (Outdated RAG Documents)  
**Default Severity:** SEV-2; escalate immediately to SEV-1 for suspected sensitive-data exposure, cross-tenant leakage, or material compliance impact.  
**Incident Owner:** Emma Wilson, Security & Compliance Lead  
**Technical Support:** Marcus Lee, ML/AI Lead; Daniel Carter, Engineering Lead  
**Expected Frequency:** Occasional quality incidents; security events treated as exceptional but high-impact.  
**Target MTTR:** ≤4 hours for containment; ≤60 minutes for containment of SEV-1 incidents.

### 6.1 Detection and Triggers

Potential signals include:

- Copilot responses containing information from another tenant.
- Prompt injection instructions detected in retrieved documents or user inputs.
- Unsupported compliance recommendations or fabricated source citations.
- Responses based on expired or unapproved policy documents.
- Sensitive payment information transmitted to the external LLM provider without authorization.
- Alerts from retrieval audit logs, security monitoring, automated evaluations, or analyst reports.

### 6.2 First 15 Minutes

1. Acknowledge the incident and assign severity.
2. Notify Security & Compliance immediately if data exposure or unauthorized access is suspected.
3. Identify affected tenants, users, documents, prompts, and Copilot versions.
4. Disable the affected retrieval source, prompt configuration, or Copilot functionality as necessary.
5. Preserve relevant audit logs, document versions, model identifiers, and request metadata using approved secure procedures.
6. Prevent further exposure by restricting affected access paths and suspending unsafe integrations.
7. Activate the analyst workflow without Copilot assistance if safe operation cannot be confirmed.
8. Notify the Senior AI Product Manager and CPO for SEV-1 incidents.

### 6.3 Diagnosis

The response team investigates:

- Whether the incident originated from user input, retrieved documents, access-control failure, or model behavior.
- Whether tenant authorization was enforced before retrieval.
- Whether prompt injection bypassed application-level restrictions.
- Whether retrieved documents were current, approved, and correctly indexed.
- Whether sensitive data was included in requests to the external LLM provider.
- Whether incorrect guidance influenced actual analyst decisions.

Security & Compliance determines whether the incident constitutes a reportable privacy or compliance event.

### 6.4 Containment and Mitigation

- Disable affected RAG indexes, document sources, or Copilot features.
- Revoke compromised credentials and restrict unauthorized access.
- Remove or quarantine malicious, outdated, or unapproved documents.
- Restore the last validated prompt and retrieval configuration.
- Apply additional authorization checks and context filtering.
- Require manual review of potentially affected analyst recommendations.
- Coordinate with the external LLM provider if provider-side investigation or containment is necessary.

Do not restore affected functionality until the underlying security or quality failure has been addressed and validated.

### 6.5 Recovery Validation

Recovery requires:

- Successful tenant-isolation and access-control tests.
- Passing prompt injection and sensitive-data protection evaluations.
- Verified document provenance, freshness, and retrieval accuracy.
- Acceptable grounded-response quality and citation correctness.
- Confirmation that analysts can safely use the restored Copilot.
- Security & Compliance approval for incidents involving privacy or compliance concerns.

The ML/AI Lead validates AI quality. Security & Compliance approves security recovery, and the Incident Commander confirms operational stability.

### 6.6 Communication and Escalation

The Senior AI Product Manager coordinates internal business communications, including:

- Affected customer segments and investigation workflows.
- Potential operational and compliance impact.
- Containment status and availability of manual fallback.
- Expected restoration timeline and follow-up actions.

Customer Operations coordinates customer communications.

Security & Compliance determines regulatory and contractual notification requirements, including applicable deadlines, based on verified incident facts.

No unverified claims about data exposure or regulatory impact should be communicated as confirmed findings.

### 6.7 Post-Incident Review and Prevention

Within two business days of stabilization, initiate a post-incident review covering:

- Root cause and attack or failure path.
- Affected tenants, documents, and analyst decisions.
- Effectiveness of authorization, filtering, and evaluation controls.
- Detection and containment timelines.
- Required changes to prompts, retrieval configuration, security tests, or document governance.

Assign corrective actions with owners and deadlines. Repeat relevant adversarial and tenant-isolation tests before the next production release.


## 7. Scenario 3 — Deployment or Infrastructure Failure

**Related Threats:** T05 (CI/CD Compromise), T07 (ML Inference Outage), T10 (Backup or Recovery Failure), T11 (Monitoring Gaps), T17 (LLM Provider Outage)  
**Default Severity:** SEV-2; escalate to SEV-1 for widespread risk-scoring disruption, confirmed deployment compromise, or critical service unavailability.  
**Incident Owner:** Alex Morgan, MLOps Engineer  
**Technical Support:** Daniel Carter, Engineering Lead  
**Expected Frequency:** Occasional deployment failures; major infrastructure outages considered exceptional.  
**Target MTTR:** ≤4 hours for SEV-2; ≤60 minutes for SEV-1 service restoration or containment.

### 7.1 Detection and Triggers

Potential detection signals include:

- Canary deployment exceeds approved error-rate or latency thresholds.
- Argo CD reports `OutOfSync`, `Degraded`, or failed synchronization.
- EKS pods repeatedly restart, become unavailable, or fail readiness checks.
- Production inference endpoints experience elevated errors or reduced availability.
- Infrastructure capacity, networking, or dependent services fail.
- External LLM API becomes unavailable or exceeds acceptable response times.

An Argo CD `OutOfSync` status indicates a difference between desired and actual configuration; it does not automatically indicate a production incident.

### 7.2 First 15 Minutes

1. Acknowledge the alert and classify incident severity.
2. Identify affected services, environments, customer segments, and recent deployments.
3. Pause Canary promotion and suspend further production changes.
4. Review Argo CD application health, synchronization status, and recent GitLab CI executions.
5. Check EKS pod health, infrastructure metrics, API availability, and error logs.
6. Trigger automated rollback for confirmed technical guardrail violations or initiate controlled manual rollback.
7. Notify the Engineering Lead, Senior AI Product Manager, and relevant operational responders.
8. Activate fallback procedures if the external LLM provider or Copilot is unavailable.

### 7.3 Diagnosis

The MLOps Engineer and Engineering Lead investigate:

- Deployment configuration differences between approved Git state and production.
- Container image versions, application logs, and recent pipeline changes.
- EKS resource exhaustion, autoscaling behavior, and network connectivity.
- Failed health checks, dependency errors, or unavailable infrastructure components.
- External LLM provider status and API failure patterns.
- Possible unauthorized deployment changes or compromised credentials.

For suspected unauthorized changes, preserve evidence and involve Security & Compliance before modifying affected systems beyond necessary containment.

### 7.4 Containment and Mitigation

- Stop Canary rollout and prevent further traffic expansion.
- Restore the last approved application and model deployment configuration.
- Use GitLab CI and Argo CD to reconcile production with the approved rollback revision.
- Scale or restart affected workloads when appropriate and safe.
- Restore service dependencies or activate approved recovery procedures.
- Disable Copilot functionality and use the manual analyst workflow during an external LLM provider outage.
- Escalate infrastructure failures to the relevant cloud or service provider when necessary.

Do not enable uncontrolled Argo CD self-healing or pruning in production as an emergency workaround. Production changes must follow approved GitOps and incident procedures.

### 7.5 Recovery Validation

Recovery is confirmed when:

- Argo CD reports the approved deployment configuration as synchronized and healthy.
- EKS workloads pass readiness and availability checks.
- API error rates, p95/p99 latency, and throughput meet operational SLOs.
- The approved ML model version is serving production traffic.
- Critical security and tenant-isolation controls remain effective.
- Customer Operations confirms that analyst workflows are functioning.

The Engineering Lead confirms application recovery, and the Incident Commander approves operational stabilization.

### 7.6 Communication and Escalation

The Senior AI Product Manager communicates:

- Affected services and estimated customer impact.
- Whether risk scoring or only Copilot functionality is affected.
- Current mitigation and rollback status.
- Expected restoration timeline and business continuity measures.

For SEV-1 incidents, notify the CPO immediately and provide updates every 15–30 minutes.

Customer Operations coordinates external customer updates when service availability or customer workflows are affected.

### 7.7 Post-Incident Review and Prevention

Within two business days of stabilization, conduct a post-incident review covering:

- Root cause and contributing deployment or infrastructure changes.
- Detection, escalation, rollback, and recovery timelines.
- Customer impact and service availability.
- Effectiveness of Canary guardrails, GitOps controls, and monitoring.
- Corrective actions, responsible owners, and deadlines.

Preventive improvements may include stronger pipeline validation, improved capacity planning, more reliable rollback testing, and enhanced monitoring coverage.
