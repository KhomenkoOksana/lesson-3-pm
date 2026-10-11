# Acceptance Criteria: Docker Images for ML Inference

## 1. Docker Image Configuration

### AC-01: Fat Image Base

**Given** the Docker build configuration is available,

**When** the fat image is built,

**Then** `Dockerfile.fat` must use `python:3.13` as its base image.

**Verification:** Review `Dockerfile.fat` and inspect the built image configuration.

### AC-02: Slim Image Base

**Given** the Docker build configuration is available,

**When** the slim image is built,

**Then** `Dockerfile.slim` must use `python:3.13-slim` as its base image.

**Verification:** Review `Dockerfile.slim` and inspect the built image configuration.

### AC-03: Multi-Stage Build

**Given** the slim Dockerfile is available,

**When** the slim image is built,

**Then** the Dockerfile must use multiple build stages and copy the required runtime artifacts into the final image.

**Verification:** Review `Dockerfile.slim` and inspect the build stages.

### AC-04: Docker Build Success

**Given** all required source files, dependencies, and model artifacts are available,

**When** both Docker images are built using the documented commands,

**Then** both builds must complete successfully.

**Verification:** Execute the build commands and record the results.

### AC-05: Docker Image Naming

**Given** the build configuration is available,

**When** both images are built,

**Then** the resulting images must be tagged as `ml-infer-fat:1.0` and `ml-infer-slim:1.0`.

**Verification:** Run `docker images` and confirm both tags.

## 2. Dependencies and Project Files

### AC-06: Pinned Dependencies

**Given** the project contains `requirements.txt`,

**When** the dependency configuration is reviewed,

**Then** the required Python dependencies must have explicitly specified versions, and compatibility with the selected Python base images must be verified.

**Verification:** Review `requirements.txt` and the dependency installation and compatibility checks.

### AC-07: Docker Ignore Configuration

**Given** the repository contains project source files,

**When** the Docker build context is prepared,

**Then** `.dockerignore` must exclude unnecessary files, such as local environments, caches, and unrelated development files, without excluding required runtime artifacts.

**Verification:** Review `.dockerignore` and inspect the effective build context.

### AC-08: Required Runtime Artifacts

**Given** the inference application is ready,

**When** the Docker images are built,

**Then** the required application code, model artifact, and runtime dependencies must be available inside each image.

**Verification:** Inspect the image contents and run an inference test.

## 3. Inference Functionality

### AC-09: Fat Image Runtime

**Given** the fat image has been built,

**When** the documented Docker run command is executed,

**Then** the container must start the inference application without a fatal runtime error.

**Verification:** Execute the documented command and inspect the container output and exit status.

### AC-10: Slim Image Runtime

**Given** the slim image has been built,

**When** the documented Docker run command is executed,

**Then** the container must start the inference application without a fatal runtime error.

**Verification:** Execute the documented command and inspect the container output and exit status.

### AC-11: Top-3 Predictions

**Given** a valid test image is provided,

**When** inference is executed in either image,

**Then** the application must return the three highest-ranked class predictions in the documented output format.

**Verification:** Run inference and validate the output structure and the number of predictions.

### AC-12: Identical Test Conditions

**Given** both images are available,

**When** their inference results are compared,

**Then** both runs must use the same test input, the same model artifact, and equivalent preprocessing and inference settings.

**Verification:** Review the test procedure and model artifact version, and confirm the input and configuration used in both runs.

### AC-13: Prediction Equivalence

**Given** inference has been executed in both images using the same test input,

**When** the top-3 prediction results are compared,

**Then** the results must satisfy the prediction equivalence method agreed upon by the AI/ML team before testing.

**Verification:** Compare predicted class identifiers, their ranking, and numerical scores according to the predefined comparison method and tolerance, if applicable.

Any discrepancy must be investigated and documented before acceptance.

## 4. Optimization and Measurement

### AC-14: Image Size Reduction

**Given** both Docker images have been built successfully,

**When** their sizes are measured using the same Docker environment,

**Then** the slim image must be measurably smaller than the fat image.

**Verification:** Run `docker images` and record the image sizes in `report.md`.

### AC-15: Build and Startup Measurements

**Given** both images are available,

**When** the comparison tests are performed,

**Then** build time and startup time must be measured using a documented and consistent procedure.

**Verification:** Record the measurement method and results in `report.md`.

### AC-16: Layer Count Comparison

**Given** both Docker images have been built,

**When** their image histories are inspected,

**Then** the report must include a comparison of their layer counts using a consistent counting method.

**Verification:** Inspect `docker history` and document the counting method and results.

### AC-17: Security Findings

**Given** both images are available for review,

**When** the agreed image security scanning process is executed,

**Then** the report must document the scanner used, the findings for each image, and any unresolved high-priority issues.

**Verification:** Review the scan results and document findings and follow-up actions in `report.md`.

### AC-18: Unnecessary Files

**Given** the slim image has been built,

**When** its contents are reviewed,

**Then** unnecessary build-time files, caches, and unrelated development artifacts must not be included in the final runtime image unless explicitly justified.

**Verification:** Inspect the final image contents and document any justified exceptions.

## 5. Documentation and Reporting

### AC-19: README Instructions

**Given** the project is ready for review,

**When** another team member follows `README.md`,

**Then** the documented instructions must explain how to build both images, run inference, and validate the results.

**Verification:** Follow the documented procedure in a clean or reproducible environment.

### AC-20: Optimization Report

**Given** the image comparison has been completed,

**When** `report.md` is reviewed,

**Then** it must contain the measured image sizes, build times, startup times, layer counts, inference comparison results, security findings, and known limitations.

**Verification:** Review the report and confirm that actual measurements are distinguished from missing data.

### AC-21: Reproducible Validation

**Given** all required project files and artifacts are available,

**When** another team member follows the documented procedure,

**Then** they must be able to build both images, run inference on the same test input, and verify the prediction equivalence criteria.

**Verification:** Repeat the procedure and record the outcome.

## Definition of Done

The task is considered complete when:

- All required Dockerfiles, application files, model artifacts, dependency files, and documentation are available.
- Both images build successfully and use the specified base images.
- The slim image uses a multi-stage build.
- Both images run inference and return top-3 predictions.
- Prediction equivalence has been verified against the agreed criteria.
- The slim image is measurably smaller than the fat image.
- The comparison report contains actual measurements and security review results.
- The README provides reproducible build, run, and validation instructions.
- All failed checks, unresolved issues, and justified exceptions are documented.

The project must not be marked complete solely because the Docker images build successfully. Functional validation and documented evidence are also required.