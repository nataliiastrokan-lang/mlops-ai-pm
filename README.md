# MLOps CI/CD — AI Product Management

This repository contains AI Product Management artifacts created as part of the MLOps CI/CD course.

The current homework focuses on preparing the **PayGuard AI image classification service** for reproducible containerized delivery. The PM scope includes defining the AI/ML team task, acceptance criteria, Docker delivery review, optimization evidence, local integration readiness, delivery risks, and stakeholder communication.

## Homework Artifacts

The `lesson-3-pm` branch contains the following artifacts:

1. [`project_brief.md`](project_brief.md) — business and technical context for the containerization task.
2. [`ai_ml_team_task.md`](ai_ml_team_task.md) — implementation task for the AI/ML team.
3. [`acceptance_criteria.md`](acceptance_criteria.md) — measurable acceptance criteria and Definition of Done.
4. [`docker_image_review.md`](docker_image_review.md) — AI PM checklist for reviewing the delivered Docker images.
5. [`optimization_report_review.md`](optimization_report_review.md) — framework for reviewing fat vs slim image optimization evidence.
6. [`compose_readiness_checklist.md`](compose_readiness_checklist.md) — checklist for local integrated environment readiness.
7. [`risk_register.md`](risk_register.md) — delivery risks, probability, impact, mitigation, and ownership.
8. [`stakeholder_summary.md`](stakeholder_summary.md) — concise delivery status for non-technical stakeholders.

## Recommended Review Order

The artifacts are designed to be reviewed in the following sequence:

`Project Brief`  
→ `AI/ML Team Task`  
→ `Acceptance Criteria`  
→ `Docker Image Review`  
→ `Optimization Report Review`  
→ `Compose Readiness Checklist`  
→ `Risk Register`  
→ `Stakeholder Summary`

This sequence follows the delivery lifecycle from defining the context and implementation task through validation, optimization, integration readiness, risk assessment, and stakeholder communication.

## Questions for the AI/ML Team Before Implementation

Before the team starts implementation, the following points should be confirmed:

### Model

- Which pretrained model from `torchvision.models` is being used?
- What is the exact source and version of `model/model.pt`?
- How is the model artifact version tracked?

### Inference

- What input format does `app/inference.py` expect?
- What preprocessing logic is applied before inference?
- What test input should be used as the reference for fat/slim comparison?
- What output format represents the top-3 prediction?

### Dependencies

- Which exact versions of `torch`, `torchvision`, and `pillow` are required?
- Are all required dependencies compatible with Python 3.13?
- Are any build-only dependencies required that should be excluded from the slim runtime image?

### Docker Delivery

- What are the expected build and run commands?
- Are there runtime environment variables or mounted files required for inference?
- What evidence will be provided for successful fat/slim builds and inference execution?

### Optimization

- What optimization target is expected for the slim image?
- Which metrics will be measured for the fat/slim comparison?
- Who will provide image size, build time, startup time, layer, and security evidence?
- Which container security scanning tool is approved for this project?

### Local Integration

- What are the required health and prediction API endpoints?
- How should `model-api` communicate with the model runtime?
- How are Redis and PostgreSQL used by the local environment?
- Which monitoring component is expected in Docker Compose?
- Which environment variables are required?
- Which local/test services must be used to ensure there is no connection to production infrastructure?

## Current Status

The PM requirements and review framework are prepared, and representative simulated measurements have been used to complete the Docker optimization review.

The optimization demonstrates an approximately 40% reduction in image size while preserving the same top-3 inference result. The current recommendation is Conditional Go: the solution may proceed toward staging after the remaining three High-severity security findings are reviewed and either remediated or formally accepted.

Docker Compose integration readiness remains subject to validation of the complete local environment, including service connectivity, configuration, health checks, environment isolation, and end-to-end prediction flow.
