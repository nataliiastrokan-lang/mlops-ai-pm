# Project Brief — PayGuard AI Image Classification Service

## Business Context

PayGuard AI is a B2B SaaS platform for Payment Risk & Operations teams in fintech and e-commerce. The platform helps teams identify potentially risky payment activity, prioritize cases for manual review, and investigate them using AI-assisted workflows.

For the purpose of this homework, the provided image classification model is treated as a supporting ML service within the PayGuard AI product context. An image classification service is used to classify incoming visual evidence and route it to the appropriate downstream processing flow. Reliable inference is therefore required to ensure that the same input is processed consistently across development, testing, and staging environments.

## Technical Context

The AI/ML team has prepared an image classification model based on a pretrained model from `torchvision.models`. The model artifact is stored as `model/model.pt`, while inference logic is implemented in `app/inference.py`.

The current delivery goal is to containerize the inference service and provide two Docker image variants:

- `ml-infer-fat:1.0` — a baseline image based on `python:3.13`, intended for initial validation and comparison.
- `ml-infer-slim:1.0` — an optimized image based on `python:3.13-slim` and built using a multi-stage build to reduce unnecessary runtime content and image size.

Docker images are treated as delivery artifacts because they package the inference application, runtime environment, dependencies, and model-related components into a reproducible unit that can be built and executed consistently across environments.

## Optimization Goal

The slim image should reduce unnecessary runtime components while preserving the functional behavior of the baseline image. Optimization must not change the model artifact, preprocessing logic, input format, or inference result.

Both images must be tested using the same input, and both must return the same top-3 prediction result.

The comparison between the fat and slim images should provide measurable evidence of the optimization, including image size and other available build/runtime characteristics.

## Validation Environment

The containerized inference solution must be suitable for verification:

- locally using Docker;
- as part of a CI/CD validation process;
- before promotion to a staging environment.

Successful local execution alone does not mean that the solution is production-ready. Before staging, the team must confirm reproducibility, dependency compatibility, consistent inference behavior, documentation completeness, and readiness of the required integration environment.

## Expected Outcome

The expected outcome is a reproducible and verifiable containerized inference solution with a baseline fat image and an optimized slim image. Both variants must provide equivalent inference behavior, while the slim image should demonstrate measurable optimization and be suitable for further staging-readiness assessment.
