# Acceptance Criteria — Image Classification Inference Containerization

The following criteria define how the AI PM will verify that the containerization task has been completed correctly.

## 1. Docker Image Build

- [ ] `Dockerfile.fat` exists and uses `python:3.13` as its base image.
- [ ] `Dockerfile.slim` exists and uses `python:3.13-slim` as its runtime base image.
- [ ] `Dockerfile.slim` uses a multi-stage build.
- [ ] `ml-infer-fat:1.0` builds successfully without errors.
- [ ] `ml-infer-slim:1.0` builds successfully without errors.
- [ ] `.dockerignore` exists and excludes files that are not required in the Docker build context.

## 2. Dependencies

- [ ] `requirements.txt` exists.
- [ ] Dependency versions are explicitly pinned.
- [ ] Required dependencies are compatible with Python 3.13.
- [ ] The slim runtime image does not contain unnecessary development dependencies.

## 3. Inference Validation

- [ ] Both images can be started successfully using `docker run`.
- [ ] The same model artifact is used for both images.
- [ ] The same preprocessing logic is used for both images.
- [ ] The same test input is used for validation of both images.
- [ ] The fat image successfully returns a top-3 prediction.
- [ ] The slim image successfully returns a top-3 prediction.
- [ ] The top-3 inference result from the fat and slim images is identical.

## 4. Optimization Validation

- [ ] The size of both Docker images is measured and documented.
- [ ] `report.md` contains a comparison of the fat and slim images.
- [ ] Optimization does not change the expected inference behavior.
- [ ] The slim runtime image does not contain unnecessary files copied from the build environment.

## 5. Documentation and Reproducibility

- [ ] `README.md` contains commands for building both Docker images.
- [ ] `README.md` contains commands for running both Docker images.
- [ ] `README.md` explains how to provide the test input and execute inference.
- [ ] The model source and/or model artifact version is documented.
- [ ] A team member who did not create the images can follow the README and reproduce the build and inference validation without undocumented manual steps.

## Definition of Done

The task is considered done when any team member can clone the repository, follow the instructions in `README.md`, successfully build `ml-infer-fat:1.0` and `ml-infer-slim:1.0`, run both images using the same test input, and receive the same top-3 prediction result.

In addition, the slim image must use `python:3.13-slim` with a multi-stage build, the required dependencies must be version-pinned, unnecessary files must be excluded from the runtime image, and `report.md` must provide measurable evidence comparing the fat and slim variants.

If any mandatory acceptance criterion above is not met or cannot be verified with available evidence, the task is not considered ready for staging assessment.
