
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
