# AI/ML Team Task — Containerize Image Classification Inference Service

## Background

PayGuard AI uses an image classification service as part of its payment case investigation workflow. The AI/ML team has prepared a pretrained image classification model based on `torchvision.models`, with the model artifact stored in `model/model.pt` and inference logic implemented in `app/inference.py`.

The goal of this task is to package the inference solution into reproducible Docker images that can be consistently built, executed, compared, and prepared for further staging validation.

Two image variants are required: a baseline fat image for initial validation and an optimized slim image for more efficient delivery.

## Scope

The AI/ML team should:

- prepare `Dockerfile.fat` using `python:3.13` as the base image;
- prepare `Dockerfile.slim` using `python:3.13-slim` as the runtime base image;
- use a multi-stage build for the slim image;
- provide a `.dockerignore` file to exclude unnecessary files from the build context;
- provide a version-pinned `requirements.txt`;
- build both Docker images successfully;
- run inference from both images using the same test input;
- verify that both images return the same top-3 prediction;
- document build and run commands;
- provide evidence comparing the fat and slim images.

## Out of Scope

The following activities are not included in this task:

- model retraining;
- improvement of model accuracy;
- production Kubernetes deployment;
- full production monitoring.

## Expected Deliverables

The team should provide:

- `model/model.pt`;
- `app/inference.py`;
- `requirements.txt`;
- `Dockerfile.fat`;
- `Dockerfile.slim`;
- `.dockerignore`;
- `README.md` with build, run, and inference validation instructions;
- `report.md` with a comparison of the fat and slim images;
- Docker image `ml-infer-fat:1.0`;
- Docker image `ml-infer-slim:1.0`.

## Technical Constraints

- The fat image must use `python:3.13`.
- The slim image must use `python:3.13-slim`.
- The slim image must use a multi-stage build.
- Dependency versions must be explicitly pinned in `requirements.txt`.
- Required dependencies must be compatible with Python 3.13 and the selected base images.
- Unnecessary development dependencies and files should not be included in the runtime image.
- The same model artifact and preprocessing logic must be used by both image variants.
- The same test input must be used to validate both images.
- Image optimization must not change inference behavior.
- No production secrets or credentials may be included in the Docker images.

## Acceptance Criteria Summary

The task can be accepted when:

1. Both fat and slim images build without errors.
2. Both images can be started using documented `docker run` commands.
3. The fat image is based on `python:3.13`.
4. The slim image is based on `python:3.13-slim` and uses a multi-stage build.
5. Both images use the same model artifact, preprocessing logic, and test input.
6. Both images successfully return a top-3 prediction.
7. The inference result is identical for the fat and slim variants.
8. Dependency versions are pinned in `requirements.txt`.
9. `.dockerignore` is present and prevents unnecessary files from entering the build context.
10. `README.md` contains sufficient instructions to reproduce the build and validation process.
11. `optimization evidence required for the fat/slim comparison.
