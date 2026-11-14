
# PayGuard AI — Executive Summary

**Document Status:** Proposed  
**Prepared by:** Nataliia Strokan, Senior AI Product Manager  
**Decision Sponsor:** Olivia Bennett, CPO  
**Decision Requested:** Conditional approval for a controlled pilot

## Business Challenge and Objective

PayGuard AI is a proposed hybrid AI solution for a B2B fintech SaaS platform serving approximately 350 EU and US customers and processing 35 million payment events monthly.

The false positive rate (FPR) has increased from 4.8% to 8.1%, contributing to a 35% increase in manual review workload and higher operational costs.

**Business objective:** Reduce FPR from 8.1% to ≤5.0% within six months while maintaining fraud detection effectiveness and regulatory compliance.

## Proposed Solution and Architecture

PayGuard AI combines:

- **Classical ML risk scoring** to improve transaction classification and reduce unnecessary manual reviews.
- **LLM/RAG Operations Copilot** to support analysts with contextual information from approved policies, PSP procedures, and historical cases.
- **Human oversight** to retain authorized analyst control over final payment risk decisions.

The recommended architecture uses AWS EKS for ML inference, a commercial LLM API for the Copilot, MLflow for model governance, and Amazon OpenSearch Service for controlled RAG retrieval.

Canary deployments, GitOps, monitoring, tenant isolation, and validated rollback procedures support operational reliability and security.

## Financial Assessment

| Metric | Base-Case Estimate |
|---|---:|
| Current monthly manual review cost | $250,000 |
| Target monthly manual review cost | $140,000 |
| Target monthly gross savings | $110,000 |
| Estimated monthly AI/MLOps operating cost | $100,000 |
| One-time implementation cost | $220,000 |
| Three-year TCO | $3.82M |
| Three-year gross benefits | $3.63M |
| **Three-year ROI** | **−5.0%** |
| Illustrative cumulative break-even | Month 55 |

The platform is projected to generate a positive monthly operating contribution of $10,000 after achieving target-state performance. However, the base case does not recover the full investment within three years.

Financial figures are hypothetical planning assumptions and require pilot validation.

## Critical Risks and Release Controls

The threat assessment identifies 18 material threats, including 10 P1 risks related to tenant isolation, ML integrity, availability, and LLM/RAG safety.

Key controls include:

- Tenant-level authorization and sensitive-data protection.
- Model-quality validation and fraud detection guardrails.
- Prompt injection testing, grounded responses, and document freshness checks.
- Canary deployment, monitoring, and tested rollback.
- Human approval for final risk decisions.

The production readiness checklist contains 40 verification controls. All remain Not Verified in the planning documentation; therefore, production release is currently **No-Go pending validation**.

## Recommendation and Decision Gates

**Recommendation: Conditional Go for a controlled pilot; No-Go for unrestricted production-scale investment.**

Approve a limited validation phase with a defined budget, measurable success criteria, and mandatory security controls.

Production expansion should require:

1. Validated mitigation of all P1 risks and successful release-readiness checks.
2. Demonstrated FPR improvement without unacceptable fraud detection degradation.
3. Measured reductions in manual review volume and handling costs.
4. Updated TCO, unit economics, and ROI based on pilot evidence.
5. Formal Security & Compliance approval and CPO authorization.

**Executive decision requested:** Authorize a capped pilot and require an evidence-based investment reassessment before full-scale production expansion.

