# Risk Register

## 1. Purpose

The purpose of this risk register is to identify, assess, and mitigate potential risks related to building, optimizing, testing, and delivering Docker images for an image classification ML model.

The register covers technical compatibility, inference consistency, image optimization, security, reproducibility, and local environment readiness.

Risk probability and impact are initial qualitative assessments. They must be reviewed and updated as the engineering team provides evidence and completes validation.

## 2. Risk Assessment

| ID | Risk | Probability | Impact | Mitigation | Owner |
|---|---|---|---|---|---|
| R1 | The Docker image works locally but fails in the staging environment due to differences in configuration, dependencies, or runtime settings. | Medium | High | Test the image in an environment representative of staging. Document runtime requirements and validate the image before deployment. | DevOps |
| R2 | The slim image produces different predictions from the fat image because of dependency differences, preprocessing changes, or numerical variations. | Medium | High | Run both images against the same test inputs. Compare top-3 predictions and confidence scores against an agreed tolerance. Add regression tests. | AI/ML Engineer |
| R3 | Python 3.13, PyTorch, torchvision, or Pillow versions are incompatible, causing build failures or runtime errors. | Medium | High | Verify compatibility before implementation. Pin dependency versions, test installation on both base images, and document supported versions. | AI/ML Engineer |
| R4 | Unnecessary files, caches, development dependencies, or intermediate build artifacts are included in the final image, increasing its size. | Medium | Medium | Configure `.dockerignore`, use multi-stage builds for the slim image, and inspect the final image contents. | DevOps |
| R5 | The README does not contain complete or correct build and run instructions, preventing team members from reproducing the results. | Medium | Medium | Document prerequisites, build commands, run commands, test inputs, expected outputs, and troubleshooting steps. Validate the instructions on a clean environment. | AI PM |
| R6 | The fat and slim images are not compared using actual measurements, so the optimization benefit cannot be demonstrated. | Medium | Medium | Record image size, build time, startup time, and security findings for both images under comparable conditions. Store the results in `report.md`. | DevOps |
| R7 | Production secrets, credentials, or API keys are accidentally included in the repository, Docker image, or Compose configuration. | Low | High | Never commit production secrets. Use safe example configuration files, exclude local secret files through `.dockerignore` and `.gitignore` where appropriate, and scan the repository and images for exposed secrets. | DevOps |
| R8 | The local Docker Compose environment connects to production databases, Redis instances, queues, or other production infrastructure. | Low | High | Use isolated local services, separate local credentials, and explicitly configured connection strings. Verify network access and confirm that production endpoints are not used. | DevOps |
| R9 | The model artifact has no clearly documented version or source, making it difficult to reproduce inference results or investigate regressions. | Medium | High | Record the model artifact version, source, checksum or other stable identifier, and associated test results. Ensure both images use the intended artifact. | AI/ML Engineer |
| R10 | The Docker image remains too large, increasing storage requirements and the time required to transfer and deploy it. | Medium | Medium | Measure the size of both images, identify the largest components, remove unnecessary runtime dependencies, and evaluate whether the optimization meets the agreed delivery targets. | DevOps |

## 3. Risk Prioritization

The following risks should receive particular attention before the team recommends moving to staging:

- **R1 — Local-to-staging failure:** Successful local execution does not guarantee that the image will work in another environment.
- **R2 — Inference inconsistency:** Optimization must not unintentionally change model behavior.
- **R3 — Dependency incompatibility:** Unsupported or incompatible library versions can prevent the model from building or running.
- **R7 — Production secret exposure:** A security incident can have serious consequences even if the probability is low.
- **R8 — Production infrastructure connection:** Local testing must not unintentionally affect production data or services.
- **R9 — Missing model version:** Without traceability, the team may be unable to reproduce or investigate prediction differences.

These risks should be reviewed with the responsible engineering owners. Any unresolved high-impact risk must be assessed before the next delivery milestone.

## 4. Risk Monitoring and Ownership

The AI PM is responsible for maintaining the risk register, coordinating risk reviews, tracking mitigation actions, and escalating unresolved issues.

Technical owners are responsible for validating the risks within their areas of expertise and providing evidence that mitigation actions have been completed.

The risk register should be updated when:

- A new technical issue is identified.
- An assumption is confirmed or disproved.
- A mitigation action is completed.
- Test results change the assessed probability or impact.
- The project moves from local validation to staging preparation.

Risk probability and impact must be reassessed based on evidence rather than treated as permanent values.

## 5. Definition of Done

The risk register is considered complete when:

- At least eight relevant project risks are documented.
- Each risk has a probability and impact assessment.
- Every risk has a defined mitigation action.
- Every risk has an assigned owner.
- High-impact risks have been reviewed with the relevant technical stakeholders.
- Open risks and mitigation actions are tracked before the staging decision.
- The register is updated as new evidence becomes available.

## 6. Final Assessment

The main delivery risks relate to inference consistency, dependency compatibility, environment differences, security, and reproducibility.

The most important priority is to demonstrate that both Docker images build and run successfully, produce consistent predictions on the same test inputs, and can be reproduced using documented instructions.

Security and environment isolation must also be verified before any staging transition.

The risk register does not eliminate risks by itself. It provides visibility, ownership, and mitigation plans so that the team can make informed delivery decisions.

The project should proceed to staging only after the required validation is completed and unresolved high-impact risks have been reviewed and explicitly addressed.
