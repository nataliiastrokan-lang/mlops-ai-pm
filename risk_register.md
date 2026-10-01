# Risk Register — Image Classification Containerization

## Purpose

This register captures the main delivery risks identified during the containerization and local integration readiness assessment of the PayGuard AI image classification service.

Risk probability is rated as **Low / Medium / High**. Impact describes the potential effect on delivery, inference reliability, security, or staging readiness.

| Risk                                                                                                        | Probability | Impact                                                                                                            | Mitigation                                                                                                                               | Owner                   |
| ----------------------------------------------------------------------------------------------------------- | ----------- | ----------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- | ----------------------- |
| Docker images work locally but fail in the staging environment                                              | Medium      | High — delays staging validation and may reveal environment-specific dependencies late in delivery                | Validate configuration and dependencies before promotion; use reproducible build/run instructions; perform staging smoke tests           | DevOps                  |
| Slim image produces a different inference result from the fat image                                         | Low         | High — optimization may have changed dependencies or runtime behavior and affected model output                   | Use the same model artifact, preprocessing logic, and test input for both images; compare top-3 predictions before acceptance            | AI/ML Engineer + QA     |
| Python 3.13 is incompatible with required `torch`, `torchvision`, `pillow`, or other dependencies           | Medium      | High — one or both images may fail to build or execute inference                                                  | Pin dependency versions; confirm Python 3.13 compatibility; validate both builds before staging assessment                               | AI/ML Engineer          |
| Unnecessary or sensitive files are included in the Docker build context or runtime image                    | Medium      | Medium — increases image size and may expose internal files or information                                        | Maintain and review `.dockerignore`; inspect slim runtime image contents before acceptance                                               | AI/ML Engineer + DevOps |
| `README.md` does not provide complete or reproducible build/run instructions                                | Medium      | Medium — other team members or CI/CD may be unable to reproduce the delivery                                      | Validate README by having another team member execute the documented process without undocumented steps                                  | AI PM + QA              |
| Fat/slim optimization is accepted without measurable comparison evidence                                    | Medium      | Medium — team cannot demonstrate that optimization provides an actual delivery benefit                            | Record image size, layers, build/startup characteristics where available, inference comparison, and security findings in `report.md`     | AI PM + DevOps          |
| Production secrets are included in `compose.yaml`, `.env`, or container configuration                       | Low         | High — credentials could be exposed through source control or local environments                                  | Use non-production local configuration; keep secrets out of committed files; review Compose configuration before sharing or staging      | DevOps                  |
| Local Compose environment connects to production PostgreSQL, Redis, queue, or another production dependency | Low         | High — test activity could affect production data or services                                                     | Use isolated local/test services and environment-specific configuration; verify connection settings before running Compose               | DevOps                  |
| Model artifact version or source is not documented                                                          | Medium      | High — inference results may not be reproducible and the team may be unable to identify which model was delivered | Record model artifact version/source and ensure both images use the same `model/model.pt`                                                | AI/ML Engineer          |
| Slim image remains too large or contains unnecessary runtime dependencies                                   | Medium      | Medium — slower transfer/deployment and reduced benefit from optimization                                         | Use `python:3.13-slim`, multi-stage build, review installed dependencies and runtime contents, and compare image size with the fat image | DevOps + AI/ML Engineer |

## Priority Risks

The following risks require particular attention before staging assessment because of their potential impact:

1. **Inference inconsistency between fat and slim images** — optimization must not change model behavior.
2. **Python/dependency incompatibility** — failure may prevent successful build or inference execution.
3. **Exposure of production secrets or connections to production services** — local testing must remain isolated from production.
4. **Missing model artifact versioning** — without a traceable model artifact, inference cannot be reliably reproduced.

## AI PM Review Approach

The AI PM should review this register together with the evidence collected through the Docker image review, optimization report review, and Compose readiness checklist.

Risks should not be considered mitigated solely because a mitigation action is documented. Closure requires relevant evidence, such as successful build and inference results, configuration review, documented model version, or independent reproduction of the documented process.

Any unresolved High-impact risk should be explicitly reviewed before recommending the solution for staging.
