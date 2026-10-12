# Docker Image Review Checklist

## 1. Purpose

This document provides a technical review checklist for the standard and optimized Docker images used for ML inference.

The goal is to verify that both images are correctly configured, reproducibly built, and capable of running the same image classification model with equivalent prediction results.

The review covers Docker configuration, dependencies, inference execution, reproducibility, and delivery readiness.

## 2. Dockerfile Review

### 2.1 Dockerfile Structure

- [ ] `Dockerfile.fat` exists.
- [ ] `Dockerfile.slim` exists.
- [ ] The fat image uses `python:3.13` as its base image.
- [ ] The slim image uses `python:3.13-slim` as its base image.
- [ ] The slim image uses a multi-stage build.
- [ ] The final slim stage contains all required runtime dependencies and artifacts.
- [ ] The `COPY` instructions include only the files required for the build or runtime.
- [ ] The working directory and application startup command are correctly configured.
- [ ] The `.dockerignore` file exists and excludes unnecessary files from the build context.
- [ ] No secrets or unnecessary local development files are included in the images.

### 2.2 Review Evidence

Request the following evidence from the technical team:

- Both Dockerfiles.
- Successful Docker build logs.
- The output of `docker images`.
- The output of `docker history` for both images.
- The contents of `.dockerignore`.

**Review outcome:** Pass / Fail / Needs clarification

**Comments and open issues:**

Record any missing configuration, build errors, or unexplained differences between the images.

## 3. Dependency Review

### 3.1 Dependency Configuration

- [ ] `requirements.txt` exists.
- [ ] Required Python dependencies have pinned versions.
- [ ] Python and dependency compatibility has been verified.
- [ ] PyTorch and torchvision versions are compatible with the selected Python environment.
- [ ] Pillow and other required image-processing dependencies are available.
- [ ] All dependencies required at runtime are installed in both images.
- [ ] Build-time dependencies that are unnecessary at runtime are excluded from the final slim image where practical.
- [ ] Required system libraries are available in the final runtime image.
- [ ] No unnecessary packages are included without justification.

### 3.2 Review Evidence

Request:

- The contents of `requirements.txt`.
- Dependency installation logs.
- The Python and library versions used by each image.
- Evidence that the model loads successfully in both environments.

**Review outcome:** Pass / Fail / Needs clarification

**Comments and open issues:**

Document dependency conflicts, missing system libraries, unsupported versions, or unresolved compatibility questions.

## 4. Inference Execution Review

### 4.1 Application and Runtime

- [ ] `app/inference.py` exists.
- [ ] The model artifact `model/model.pt` is available.
- [ ] The model can be loaded successfully inside the fat image.
- [ ] The model can be loaded successfully inside the slim image.
- [ ] Both images can be started using the documented `docker run` commands.
- [ ] The application accepts the documented test input.
- [ ] Both images return the top-3 class predictions.
- [ ] The output follows the documented format.
- [ ] Runtime errors are investigated and documented.

### 4.2 Review Evidence

Request:

- The documented Docker run commands.
- Logs from both inference runs.
- The test input used for validation.
- The top-3 prediction outputs from both images.
- Evidence of successful execution and exit status.

**Review outcome:** Pass / Fail / Needs clarification

**Comments and open issues:**

Record missing artifacts, startup errors, incorrect output formats, or failures to execute inference.

## 5. Reproducibility Review

### 5.1 Model and Input Consistency

- [ ] Both images use the same model artifact.
- [ ] The model source or version is documented.
- [ ] Both runs use the same test input.
- [ ] Both runs use equivalent preprocessing steps.
- [ ] Both runs use equivalent inference settings.
- [ ] The output comparison method is defined before validation.
- [ ] The top-3 class identifiers and ranking are compared.
- [ ] Numerical prediction scores are compared according to the agreed tolerance, if applicable.
- [ ] Any differences in inference results are investigated and documented.

### 5.2 Review Evidence

Request:

- Model artifact source and version information.
- The test input or its identifier.
- The preprocessing configuration.
- The comparison procedure and results.
- An explanation of any differences between fat and slim predictions.

**Review outcome:** Pass / Fail / Needs clarification

**Comments and open issues:**

Document any missing version information, inconsistent test conditions, or unexplained prediction differences.

## 6. Optimization Review

### 6.1 Image Size and Build Structure

- [ ] The sizes of both images have been measured.
- [ ] The measurement method is consistent for both images.
- [ ] The slim image is measurably smaller than the fat image.
- [ ] The layer counts have been recorded using a consistent counting method.
- [ ] Build time has been measured for both images.
- [ ] Startup time has been measured using a documented procedure.
- [ ] Unnecessary files in the final slim image have been reviewed.
- [ ] The results are recorded in `report.md`.

### 6.2 Review Evidence

Request:

- Output from `docker images`.
- Output from `docker history` for both images.
- Build and startup measurement results.
- The completed optimization report.
- An explanation of any unexpected results.

**Review outcome:** Pass / Fail / Needs clarification

**Comments and open issues:**

Document missing measurements, insufficient size reduction, or unnecessary runtime artifacts.

## 7. Security and Delivery Readiness

- [ ] Both images have been reviewed using the agreed security scanning process.
- [ ] Security findings are documented.
- [ ] Unresolved high-priority findings have been assessed.
- [ ] No secrets are embedded in the images.
- [ ] The model artifact and dependency sources are documented.
- [ ] Build and run instructions are reproducible.
- [ ] Known limitations are recorded.
- [ ] Outstanding issues are assigned to responsible team members.

**Review outcome:** Pass / Fail / Needs clarification

**Comments and open issues:**

Document security concerns, missing evidence, and any conditions that must be addressed before staging.

## 8. Review Summary

| Review Area | Status | Evidence / Comments |
|---|---|---|
| Dockerfile configuration | TBD | TBD |
| Dependencies | TBD | TBD |
| Inference execution | TBD | TBD |
| Reproducibility | TBD | TBD |
| Image optimization | TBD | TBD |
| Security review | TBD | TBD |
| Documentation | TBD | TBD |

Use the following status values:

- **Pass:** The requirement has been verified and supporting evidence is available.
- **Fail:** The requirement has not been met.
- **Needs clarification:** Evidence is missing or the result cannot yet be confirmed.
- **Not applicable:** The check does not apply, with justification documented.

## 9. Final Review Decision

**Overall status:** TBD

**Blocking issues:** TBD

**Required follow-up actions:** TBD

**Responsible owners:** TBD

**Staging recommendation:** TBD

The Docker solution should be recommended for staging only after the required functional checks have passed, prediction equivalence has been verified, relevant security findings have been assessed, and all applicable delivery requirements have been reviewed.

A successful image build alone is not sufficient evidence of staging readiness.