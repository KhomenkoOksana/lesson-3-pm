# Task: Build and Optimize Docker Images for ML Inference

## Background

The AI/ML team has a pretrained image classification model that needs to be packaged into reproducible Docker images.

The objective is to create two image variants: a standard image (`ml-infer-fat:1.0`) and an optimized image (`ml-infer-slim:1.0`).

Both images must run inference using the same model artifact and produce equivalent top-3 predictions for the same test input.

The optimized image should reduce image size without compromising inference functionality.

## Scope

The task includes:

- Creating `Dockerfile.fat` based on `python:3.13`.
- Creating `Dockerfile.slim` based on `python:3.13-slim`.
- Implementing a multi-stage build for the slim image.
- Providing a `.dockerignore` file.
- Providing a `requirements.txt` file with pinned dependency versions.
- Packaging the inference application in `app/inference.py`.
- Including the model artifact at `model/model.pt`.
- Building and running both Docker images.
- Validating top-3 predictions using the same test input.
- Comparing image size, build time, startup time, layer count, and available security findings.
- Documenting build, run, and validation instructions in `README.md`.
- Recording comparison results in `report.md`.

## Out of Scope

The following activities are excluded from this task:

- Retraining the model.
- Improving model accuracy or changing model architecture.
- Implementing production Kubernetes infrastructure.
- Implementing full production monitoring.
- Building a complete production API platform.
- Deploying to production.

## Expected Deliverables

The AI/ML team must provide:

1. `Dockerfile.fat` — standard Docker image build instructions.
2. `Dockerfile.slim` — optimized Docker image build instructions using a multi-stage build.
3. `.dockerignore` — exclusions for unnecessary files and directories.
4. `requirements.txt` — pinned Python dependencies.
5. `app/inference.py` — executable inference application.
6. `model/model.pt` — the agreed model artifact, with its version or source documented.
7. `README.md` — instructions for building, running, and validating both images.
8. `report.md` — comparison of image sizes, build and startup times, layer counts, inference results, and available security findings.

The report must distinguish measured results from metrics that have not yet been collected.

## Technical Constraints

### Base Images

- Fat image: `python:3.13`.
- Slim image: `python:3.13-slim`.

### Build Strategy

- The slim image must use a multi-stage build.
- Both images must use the same model artifact and equivalent inference logic.
- Unnecessary build-time dependencies must not be included in the final runtime image unless required.
- The `.dockerignore` file must exclude irrelevant files from the Docker build context.
- Dependency versions must be pinned in `requirements.txt`.
- Dependencies must be verified for compatibility with the selected Python base images.

### Inference Requirements

- The application must load the agreed pretrained image classification model.
- The input format and preprocessing steps must be documented.
- The application must return the top-3 predictions in a documented output format.
- Both images must be tested using the same test input.
- Prediction equivalence must be verified using a predefined comparison method.

### Reproducibility and Security

- The model artifact version or source must be documented.
- Build and run commands must be reproducible.
- Secrets must not be embedded in the Docker images.
- The final images must be reviewed for unnecessary files and available security findings.

## Acceptance Criteria Summary

The task will be considered complete when:

- Both Docker images build successfully.
- The fat image uses `python:3.13`.
- The slim image uses `python:3.13-slim` and a multi-stage build.
- Both images run inference successfully.
- Both images return top-3 predictions for the same test input.
- Predictions satisfy the agreed equivalence criteria.
- The slim image is measurably smaller than the fat image.
- Image comparison metrics and validation results are documented in `report.md`.
- `README.md` contains reproducible build, run, and validation instructions.
- The repository includes all required deliverables.

## Dependencies and Open Questions

Before implementation is considered ready, the team must confirm:

- The source and version of the pretrained model.
- The availability and compatibility of `model/model.pt`.
- The supported input image format and preprocessing requirements.
- The expected output format for top-3 predictions.
- The compatibility of PyTorch, torchvision, Pillow, and other dependencies with Python 3.13.
- The method and tolerance, if applicable, for comparing predictions.
- The test input to be used for validation.

Any unresolved compatibility issues or missing artifacts must be reported before the task is marked complete.

## Definition of Done

The task is done when another team member can follow the README instructions, build both Docker images, run inference on the same test input, verify equivalent top-3 predictions, and review the documented comparison results.

All required files must be present in the repository, and any remaining limitations must be explicitly documented.