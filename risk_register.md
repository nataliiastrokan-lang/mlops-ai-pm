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
| High-severity vulnerabilities remain in the slim container image                                            | Medium      | High — known vulnerabilities may create a security exposure if the image is promoted without review               | Review the 3 remaining High-severity findings; remediate them or document formal risk acceptance before staging promotion                | DevOps + Security       |

## Priority Risks

The following risks require particular attention before staging assessment because of their potential impact:

1. **Remaining High-severity security findings** — the slim image still contains three High-severity findings that must be reviewed and either remediated or formally accepted before staging promotion.
2. **Inference inconsistency between fat and slim images** — current comparison shows the same top-3 result, but inference consistency must remain part of regression validation for future image changes.
3. **Python/dependency incompatibility** — dependency changes may affect future builds or runtime behavior and should remain controlled through version pinning and validation.
4. **Exposure of production secrets or connections to production services** — local testing must remain isolated from production.
5. **Missing model artifact versioning** — without a traceable model artifact, inference cannot be reliably reproduced.

## AI PM Review Approach

The optimization review provides evidence that the slim image delivers measurable improvement while preserving the expected top-3 inference result. The image size decreased by approximately 40%, unnecessary runtime content was removed, and the security profile improved compared with the fat image.

However, three High-severity security findings remain open. Therefore, the current recommendation is Conditional Go: the solution may proceed toward staging only after these findings are reviewed and either remediated or formally accepted.

Risk closure requires supporting evidence rather than documentation of mitigation actions alone. Any unresolved High-impact risk must be explicitly reviewed before staging promotion.
