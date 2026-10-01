# Docker Image Review — AI PM Checklist

## Purpose

This checklist is used by the AI PM to review the Docker delivery prepared by the AI/ML team before the solution proceeds to further staging-readiness assessment.

The review focuses on the correctness of the Docker configuration, dependency management, inference execution, and reproducibility between the fat and slim image variants.

## 1. Dockerfile Review

| Check                                                             | Status | Evidence / Comment                                     |
| ----------------------------------------------------------------- | ------ | ------------------------------------------------------ |
| `Dockerfile.fat` exists                                           | ☐      |                                                        |
| `Dockerfile.slim` exists                                          | ☐      |                                                        |
| Fat image uses `python:3.13`                                      | ☐      | Verify `FROM` instruction in `Dockerfile.fat`          |
| Slim runtime image uses `python:3.13-slim`                        | ☐      | Verify runtime `FROM` instruction in `Dockerfile.slim` |
| Slim image uses a multi-stage build                               | ☐      | Verify separate build and runtime stages               |
| `COPY` instructions follow a logical order                        | ☐      | Review Dockerfile structure and copied artifacts       |
| `.dockerignore` exists                                            | ☐      | Verify file in repository                              |
| `.dockerignore` excludes unnecessary files from the build context | ☐      | Review exclusion rules                                 |

## 2. Dependency Review

| Check                                                                    | Status | Evidence / Comment                                              |
| ------------------------------------------------------------------------ | ------ | --------------------------------------------------------------- |
| `requirements.txt` exists                                                | ☐      |                                                                 |
| Dependency versions are explicitly pinned                                | ☐      | Review `requirements.txt`                                       |
| Dependencies are compatible with Python 3.13                             | ☐      | Confirm through successful build/test or compatibility evidence |
| Required versions of `torch`, `torchvision`, and `pillow` are documented | ☐      | Review `requirements.txt`                                       |
| Runtime image contains only dependencies required for inference          | ☐      | Review slim image contents/build stages                         |
| Development-only dependencies are excluded from the slim runtime image   | ☐      | Review installed packages and Dockerfile stages                 |

## 3. Inference Execution Review

| Check                                                     | Status | Evidence / Comment           |
| --------------------------------------------------------- | ------ | ---------------------------- |
| `ml-infer-fat:1.0` builds successfully                    | ☐      | Build log / successful build |
| `ml-infer-slim:1.0` builds successfully                   | ☐      | Build log / successful build |
| Fat image starts successfully using `docker run`          | ☐      | Execution evidence           |
| Slim image starts successfully using `docker run`         | ☐      | Execution evidence           |
| Test image/input can be provided to the inference process | ☐      | Verify documented invocation |
| Fat image returns a top-3 prediction                      | ☐      | Capture inference output     |
| Slim image returns a top-3 prediction                     | ☐      | Capture inference output     |
| Build and run commands are documented in `README.md`      | ☐      | Review README                |

## 4. Reproducibility Review

| Check                                                                    | Status | Evidence / Comment                                |
| ------------------------------------------------------------------------ | ------ | ------------------------------------------------- |
| Both images use the same `model/model.pt` artifact                       | ☐      | Compare model source/version                      |
| Model artifact source or version is documented                           | ☐      | README/report/model metadata                      |
| Both images use the same preprocessing logic                             | ☐      | Review `app/inference.py` and image configuration |
| The same test input is used for both variants                            | ☐      | Test evidence                                     |
| Fat and slim images return the same top-3 prediction                     | ☐      | Compare inference outputs                         |
| A second team member can reproduce the documented build and test process | ☐      | Independent validation using README               |

## Review Outcome

**Overall status:** `TBD — Pending delivery evidence`

**Open issues:** `TBD`

**AI PM conclusion:**  
The Docker solution can proceed to optimization and staging-readiness assessment only after the mandatory checks above are supported by evidence. A successful build alone is not sufficient: both image variants must execute correctly, use the same model and preprocessing logic, and demonstrate equivalent inference results on the same test input.

Any failed or unverifiable mandatory check should be documented as an open issue and resolved or explicitly accepted as a risk before further promotion.
