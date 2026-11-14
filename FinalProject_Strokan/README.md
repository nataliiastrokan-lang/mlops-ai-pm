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
