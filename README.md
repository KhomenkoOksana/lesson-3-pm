# lesson-3-pm
# Image Classification Docker Delivery — PM Documentation

## 1. Project Overview

This project documents the preparation and validation of two Docker images for an image classification model:

- `ml-infer-fat:1.0` — a baseline image for initial validation.
- `ml-infer-slim:1.0` — an optimized image based on `python:3.13-slim` and a multi-stage Docker build.

The model uses a pretrained architecture from `torchvision.models`. The model artifact is expected to be available at `model/model.pt`, and the inference code is located at `app/inference.py`.

The primary goal is to reduce the Docker image size without changing inference behavior. Both images must be built, run, and validated against the agreed acceptance criteria before the optimized image can be considered ready for the next environment.

**Important:** Actual image sizes, build times, startup times, and inference test results must be measured and documented. They must not be assumed or invented.

## 2. Project Goals

The project aims to:

- Build and validate the baseline and optimized Docker images.
- Verify compatibility with Python 3.13, PyTorch, torchvision, and other required dependencies.
- Confirm that both images produce consistent predictions for the same test inputs.
- Compare image size, build time, startup time, and relevant security findings.
- Document reproducible build and verification steps.
- Identify outstanding risks and requirements before staging validation.

## 3. Project Structure

The `lesson-3-pm/` directory contains the following PM artifacts:

| File | Purpose |
|---|---|
| `project_brief.md` | Describes the project context, goals, scope, and expected outcomes. |
| `ai_ml_team_task.md` | Defines the task, technical constraints, deliverables, and responsibilities for the AI/ML team. |
| `acceptance_criteria.md` | Specifies the conditions that must be met before the task can be accepted. |
| `docker_image_review.md` | Provides a checklist for reviewing the Dockerfiles, dependencies, inference setup, and reproducibility. |
| `optimization_report_review.md` | Defines how to compare the fat and slim images and document the measured results. |
| `compose_readiness_checklist.md` | Lists the checks required to assess Docker Compose readiness and service configuration. |
| `risk_register.md` | Records project risks, their potential impact, mitigation actions, and owners. |
| `stakeholder_summary.md` | Summarizes the project, expected benefits, key risks, and readiness requirements for stakeholders. |
| `README.md` | Serves as the main entry point and navigation guide for the project documentation. |

## 4. How to Use the Documentation

Recommended review sequence:

1. Read `project_brief.md` to understand the business and technical context.
2. Review `ai_ml_team_task.md` to understand the expected deliverables and constraints.
3. Use `acceptance_criteria.md` to define how completion will be verified.
4. Use `docker_image_review.md` to review the image implementation.
5. Complete `optimization_report_review.md` after collecting actual measurements.
6. Use `compose_readiness_checklist.md` if Docker Compose is part of the required setup.
7. Review `risk_register.md` and confirm the priority and ownership of outstanding risks.
8. Share `stakeholder_summary.md` with stakeholders who need a concise project update.

## 5. Initial Verification Commands

Run these commands from the directory containing `Dockerfile.fat`, `Dockerfile.slim`, and the required build context.

### Build the baseline image

```bash
docker build -f Dockerfile.ече -t ml-infer-fat:1.0 .
```

### Build the optimized image

```bash
docker build -f Dockerfile.slim -t ml-infer-slim:1.0 .
```

### Compare image sizes

```bash
docker images ml-infer-fat:1.0 ml-infer-slim:1.0
```

### Inspect image history

```bash
docker history ml-infer-fat:1.0
docker history ml-infer-slim:1.0
```

These commands provide initial evidence about image size and build history. They do not, by themselves, prove that the optimized image is functionally equivalent, secure, or ready for staging.

### Additional checks

Build time should be measured for both images using the same environment and comparable build conditions. Startup time should be measured using the confirmed runtime command and a consistent test procedure.

Inference validation must use the same model artifact, preprocessing, and test inputs for both images. The expected output format and acceptable tolerance for confidence scores must be agreed with the AI/ML team.

The exact container startup and inference commands are **TBD** until the AI/ML team confirms the image entrypoint, required arguments, input format, and output format.

## 6. Docker Compose Readiness

If Docker Compose is required and a valid `compose.yaml` or `docker-compose.yml` is available, the following commands can be used for local verification:

```bash
docker compose config
docker compose up --build -d
docker compose ps
docker compose logs --tail=100
docker compose down
```

The Compose configuration, service list, ports, health checks, and dependencies must be confirmed before running the full setup.

Any example health-check or inference endpoint must be replaced with the actual endpoint provided by the engineering team.

Local Compose validation does not automatically establish staging or production readiness.

## 7. Open Questions for the AI/ML Team

Before final validation, confirm the following:

1. What is the exact model version, and how is `model/model.pt` produced and versioned?
2. What input formats, preprocessing steps, and output schema does `app/inference.py` support?
3. Are Python 3.13, PyTorch, torchvision, and all other dependencies compatible with both base images?
4. What are the confirmed commands for starting the container and running an inference request?
5. What test images and expected outputs should be used to compare the two images?
6. What tolerance is acceptable for differences in confidence scores?
7. Which build artifacts, caches, and unnecessary files must be excluded through `.dockerignore`?
8. What are the measured image sizes, build times, startup times, and relevant security findings?
9. Is Docker Compose required for this task? If yes, which services and dependencies must be included?
10. How will configuration and secrets be supplied without storing credentials in the image or repository?
11. What documentation and evidence are required before staging validation?

## 8. Current Status and Outstanding Evidence

The documentation defines the planned validation process. Actual technical readiness must be established using implementation evidence.

The following items remain **TBD until verified**:

- Successful builds of both images.
- Confirmed runtime and inference commands.
- Inference consistency results.
- Measured image sizes and build and startup times.
- Security scan results.
- Confirmation of dependency compatibility.
- Docker Compose readiness, if applicable.
- Approval to proceed to staging.

The AI/ML team should provide implementation details and inference evidence. DevOps should confirm container runtime, image inspection, and infrastructure requirements. The AI Product Manager coordinates the acceptance criteria, tracks open questions and risks, and communicates the final status to stakeholders.

## 9. Definition of Done

The task can be considered complete when:

- Both Docker images build successfully.
- Both images run using documented and reproducible commands.
- Inference results meet the agreed consistency criteria.
- Optimization measurements are recorded and compared.
- Required dependency and security checks are completed.
- Required documentation is reviewed and updated.
- Outstanding risks have owners and agreed next steps.
- Readiness for the next environment is explicitly confirmed.

**A successful local build is not sufficient evidence of staging or production readiness.**