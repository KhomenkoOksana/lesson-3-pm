# Project Brief: Docker Optimization for ML Inference

## 1. Project Overview

The project aims to package a pretrained image classification model into two Docker images: a standard image (`ml-infer-fat:1.0`) and an optimized image (`ml-infer-slim:1.0`).

The solution will use a pretrained model from `torchvision.models` and a model artifact stored at `model/model.pt`. The inference application will be implemented in `app/inference.py`.

The goal is to make the model reproducible, portable, and easier to deploy across development, CI/CD, and staging environments.

## 2. Business Context

Machine learning models often work in a developer's local environment but may encounter dependency, configuration, or compatibility issues when moved to another environment.

Docker packaging helps reduce these inconsistencies by providing a standardized environment for running inference.

The project will also evaluate whether the Docker image can be optimized without changing the model's inference results.

## 3. Project Objectives

The project objectives are to:

- Package the image classification model into a Docker image.
- Provide a standard image based on `python:3.13`.
- Provide an optimized image based on `python:3.13-slim`.
- Use a multi-stage build for the slim image.
- Ensure that both images can run inference using the same model artifact and input.
- Verify that both images produce equivalent top-3 predictions.
- Measure and compare image size, build time, startup time, image layers, and available security findings.
- Document build, run, and validation instructions.

## 4. Technical Scope

The technical solution is expected to include:

- `Dockerfile.fat` — Docker build instructions for the standard image.
- `Dockerfile.slim` — Docker build instructions for the optimized image.
- `requirements.txt` — Python dependencies with pinned versions.
- `.dockerignore` — files and directories excluded from the Docker build context.
- `app/inference.py` — inference application code.
- `model/model.pt` — pretrained model artifact.
- `README.md` — build, run, and validation instructions.
- `report.md` — comparison of the fat and slim images.

The technical team must verify dependency compatibility with the selected Python base images before confirming that the images are ready for validation.

## 5. Expected Outcomes

The expected outcomes are:

1. Both Docker images build successfully.
2. Both images run inference using the same test input and model artifact.
3. Both images return top-3 predictions in the documented output format.
4. The predictions are equivalent between the fat and slim images, according to a predefined comparison method.
5. The image sizes and other agreed performance metrics are measured and documented.
6. The build and validation process can be reproduced by another team member using the README instructions.

The actual image sizes, performance measurements, and security findings will be recorded after the technical validation is completed.

## 6. Optimization Goal

The primary optimization goal is to reduce the Docker image size while preserving inference functionality and prediction equivalence.

The slim image will use `python:3.13-slim` and a multi-stage build to separate dependency installation from the final runtime image.

The optimization will be considered successful if the slim image is measurably smaller than the fat image and both images satisfy the agreed functional validation criteria.

No specific percentage reduction is assumed before measurements are available.

## 7. Delivery and Validation Environments

The solution must be suitable for:

- **Local development:** engineers can build and run both images locally.
- **CI/CD:** the build and validation process can be executed as part of an automated pipeline.
- **Staging:** the images can be considered for deployment after functional validation, compatibility checks, security review, and confirmation of environment-specific requirements.

A successful local run alone does not establish production readiness.

## 8. Key Stakeholders

- **AI Product Manager:** defines scope, coordinates delivery, tracks risks, and verifies acceptance criteria.
- **AI/ML Engineer:** provides the model artifact, inference implementation, dependency information, and prediction validation.
- **DevOps Engineer:** reviews Dockerfiles, build optimization, image configuration, and CI/CD readiness.
- **QA Engineer:** validates reproducibility, inference equivalence, and acceptance criteria.

## 9. Key Risks and Dependencies

The project depends on:

- Availability of the correct model artifact and its version information.
- Compatibility of Python, PyTorch, torchvision, and other dependencies.
- Availability of a consistent test input and expected inference output format.
- A defined method for comparing predictions between the two images.
- Successful execution of the build and runtime validation steps.

Potential risks include dependency incompatibility, missing runtime libraries, inconsistent predictions, excessive image size, and incomplete validation evidence.

## 10. Definition of Success

The project is successful when both Docker images are reproducibly buildable, both can execute inference on the same test input, and their top-3 predictions satisfy the agreed equivalence criteria.

The slim image must demonstrate a measurable size reduction compared with the fat image.

All validation results, known limitations, and relevant measurements must be documented.

Readiness for staging must be assessed separately against the applicable deployment, security, and operational requirements.