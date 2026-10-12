# Optimization Report Review

## 1. Purpose

The purpose of this review is to evaluate the optimization report prepared by the AI/ML team.

The review checks whether the optimized Docker image is smaller than the baseline image while preserving model inference functionality and prediction results.

It also verifies that the measurements are reliable, reproducible, and clearly documented.

## 2. Images Under Review

The team should compare the following Docker images:

- **Baseline image:** `ml-infer-fat:1.0`, built using `python:3.13`.
- **Optimized image:** `ml-infer-slim:1.0`, built using `python:3.13-slim` and a multi-stage build.

Both images must use the same model artifact, test input, preprocessing, and equivalent inference settings.

## 3. Required Report Content

The optimization report (`report.md`) prepared by the AI/ML team should include:

- Docker image sizes before and after optimization.
- Absolute and percentage image size reduction.
- Build time for both images.
- Container startup time for both images.
- Inference execution results for both images.
- Comparison of top-3 predictions using the same test input.
- Docker image layer information.
- Security scan results, if a security scanner is available.
- Identified issues, risks, and optimization trade-offs.
- Commands and test conditions required to reproduce the results.

The AI PM should verify that each result is supported by evidence. Missing information must be marked as `TBD`, with the responsible team member and the required command or action documented in the review comments.

## 4. Metrics Review

| Metric | Baseline Image | Optimized Image | Review Status | AI PM Comments |
|---|----------------|-----------------|---------------|---|
| Docker image size | 1.61 GB        | 176.18 Mb       | done          | **Owner:** AI/ML Engineer or DevOps Engineer. **Command:** `docker images \| grep ml-infer`. Record both image sizes using the same units. |
| Image layer history | 18 layers      | 14 layers       | done          | **Owner:** AI/ML Engineer or DevOps Engineer. **Commands:** `docker history ml-infer-fat:1.0` and `docker history ml-infer-slim:1.0`. Compare layer history and identify unnecessary layers. |
| Build time | 92.2s          | 20.9s           | done          | **Owner:** AI/ML Engineer or DevOps Engineer. **Action:** Measure the build time for both images under comparable conditions. Example command: `time docker build -f Dockerfile.fat -t ml-infer-fat:1.0 .`. Use the corresponding Dockerfile and tag for the optimized image. |
| Container startup time | TBD            | TBD             | TBD           | **Owner:** AI/ML Engineer or DevOps Engineer. **Action:** Measure and document the startup time for both containers using the same procedure and comparable conditions. |
| Inference execution | TBD            | TBD             | TBD           | **Owner:** AI/ML Engineer. **Action:** Run the same inference test against both images. Record whether inference completes successfully and compare execution times. |
| Top-3 predictions | TBD            | TBD             | TBD           | **Owner:** AI/ML Engineer. **Action:** Run both images with the same test input and compare the top-3 predicted classes and scores using agreed acceptance criteria. |
| Security scan findings | Docker Scout reports 304 vulnerabilities in the base image python:3.13: 2 Critical, 34 High, 42 Medium, 209 Low, and 17 Unknown. The final image ml-infer-fat:1.0 displays zero vulnerabilities in the image-level counters. Verify scan scope before recording the final image results.            | Docker Scout reports 35 vulnerabilities in the base image python:3.13-slim: 6 High, 3 Medium, 26 Low, and 0 Critical/Unknown. The final image ml-infer-slim:1.0 displays zero vulnerabilities in the image-level counters. Verify scan scope and confirm comparable scan results before concluding that the optimized image is more secure.             | TBD           | **Owner:** DevOps Engineer or Security Engineer. **Action:** Scan both images using the available security scanning tool and document vulnerabilities and other findings. |
| Reproducibility | TBD            | TBD             | TBD           | **Owner:** AI/ML Engineer. **Action:** Document build commands, test commands, model artifact, input data, dependencies, and environment details so another team member can reproduce the results. |

All measurements must be provided by the responsible team members. The AI PM must not invent missing measurements or assume that optimization has succeeded before reviewing the evidence.

## 5. Image Size Reduction Review

The report must show the baseline and optimized image sizes and calculate the absolute and percentage reduction.

**Formula:**

`Size reduction (%) = ((Baseline size - Optimized size) / Baseline size) × 100`

Example for illustration only:

- Baseline image size: 1,000 MB.
- Optimized image size: 600 MB.
- Absolute reduction: 400 MB.
- Percentage reduction: 40%.

These values are illustrative only and must not be presented as actual project results.

The AI PM should verify that:

- Both image sizes were obtained using the same method.
- The units are consistent.
- The percentage reduction is calculated correctly.
- The result meets the project's agreed optimization target, if one has been defined.

**AI PM action:** If the image sizes are missing, request them from the AI/ML Engineer or DevOps Engineer and ask them to run:

`docker images | grep ml-infer`

## 6. Functional Equivalence Review

Reducing image size must not break model inference.

The report must demonstrate that:

- Both images load the intended model artifact.
- Both images process the same test input.
- Both images use equivalent preprocessing and inference settings.
- Both images return exactly three predictions.
- Prediction results are equivalent according to the agreed comparison method and tolerance.

The AI PM should request evidence from the AI/ML Engineer showing the output from both images for the same input.

If the predictions differ, the team must investigate the cause and document the findings before the optimized image is accepted.

**AI PM action:** If test results are missing, request the inference test command or script and the corresponding outputs for both images.

## 7. Performance and Reproducibility Review

The report should explain how build time, container startup time, and inference execution time were measured.

The AI PM should verify that:

- The test environment and measurement methods are documented.
- Both images were tested under comparable conditions.
- The report includes the commands used to build and run the images.
- The model artifact and test input are identical for both tests.
- Results can be reproduced by another team member.
- Missing measurements and failed tests are clearly identified.

The AI PM should distinguish between image-size improvements and runtime performance improvements. A smaller Docker image does not automatically mean faster inference.

If performance measurements are missing, the AI PM should request them from the AI/ML Engineer or DevOps Engineer.

## 8. Security and Risk Review

The report should identify any issues introduced by optimization, including:

- Missing system libraries or runtime dependencies.
- Model loading or inference failures.
- Differences in prediction results.
- Build or container startup regressions.
- Security vulnerabilities or outdated dependencies.
- Limitations of the testing environment.

The report should describe the impact of each significant issue and propose a follow-up action.

If security scan results are unavailable, the AI PM should request them from the DevOps Engineer or Security Engineer, or document why the scan could not be performed.

## 9. Review Summary

| Review Item | Status | AI PM Comments |
|---|---|---|
| Baseline and optimized images are identified | TBD | **Owner:** AI/ML Engineer. **Command:** `docker images \| grep ml-infer`. Confirm that both image names and tags are correct. |
| Image size reduction is measured | TBD | **Owner:** AI/ML Engineer or DevOps Engineer. **Command:** `docker images \| grep ml-infer`. Record the sizes and calculate the percentage reduction. |
| Image layer history is reviewed | TBD | **Owner:** AI/ML Engineer or DevOps Engineer. **Commands:** `docker history ml-infer-fat:1.0` and `docker history ml-infer-slim:1.0`. Review the layer history of both images. |
| Build time is documented | TBD | **Owner:** AI/ML Engineer or DevOps Engineer. **Action:** Request build-time measurements and the commands used to obtain them. |
| Container startup time is documented | TBD | **Owner:** AI/ML Engineer or DevOps Engineer. **Action:** Request startup-time measurements for both containers under comparable conditions. |
| Inference functionality is verified | TBD | **Owner:** AI/ML Engineer. **Action:** Request test results confirming successful inference using both images. |
| Top-3 prediction equivalence is checked | TBD | **Owner:** AI/ML Engineer. **Action:** Request a comparison using the same test input and agreed acceptance criteria. |
| Security findings are documented | TBD | **Owner:** DevOps Engineer or Security Engineer. **Action:** Request security scan results or document why scanning was not performed. |
| Results are reproducible | TBD | **Owner:** AI/ML Engineer. **Action:** Verify that build commands, test commands, inputs, and environment details are documented. |
| Risks and trade-offs are documented | TBD | **Owner:** AI/ML Engineer, with input from DevOps or Security as needed. **Action:** Request explanations for unresolved issues and proposed follow-up actions. |

### Status Definitions

- **Pass** — the requirement is met and supported by evidence.
- **Fail** — the requirement is not met or a critical issue remains unresolved.
- **TBD** — the required data or evidence is not yet available.
- **N/A** — the check is not applicable, with a justification documented.

## 10. Final Review Decision

**Review decision:** TBD

**Key findings:** TBD

**Required follow-up actions:** TBD

**Responsible team members:** TBD

**Reviewer comments:** TBD

The optimized image should be considered ready for the next delivery stage only when the required measurements are available, inference functionality has been verified, prediction results meet the agreed acceptance criteria, and critical issues have been resolved or explicitly accepted by the responsible stakeholders.