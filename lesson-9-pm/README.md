# MLOps PM Analysis — Iris Classification

## Task overview
This project reviews five MLflow experiment runs for an Iris flower classification task. The analysis is performed from an AI/ML Project Manager perspective, focusing on model evidence, experiment traceability, Prometheus/Grafana metrics, risks, and readiness for staging or production.

## Source materials
The analysis uses the mentor-provided Iris Classification input summary and screenshots showing:
- MLflow training runs and metric comparison
- The `rf-depth3` run details and parameters
- The saved `rf-depth3` model artifact
- The `rf-small` artifact page showing no recorded artifact
- Prometheus time-series and table views for `mlflow_accuracy` and `mlflow_loss`

The dataset contains 150 Iris samples and three classes. The supplied evaluation setup is a stratified 70/30 train/test split with `split_random_state=7`.

## Selected candidate
`rf-depth3` (Random Forest, `n_estimators=100`, `max_depth=3`, `random_state=42`).

- Accuracy: approximately 0.956
- Log loss: approximately 0.141
- Model artifact: available
- Run ID: `2ddc4f9d563c43e5a7c6c8523a580db3`

## PM decision
**Need more experiments before staging approval.**

The selected run has the best reported accuracy and log loss, a saved artifact, and traceable MLflow details. However, the test set has only 45 samples, and the supplied evidence does not include cross-validation, per-class metrics, explicit promotion thresholds, rollback steps, or complete operational ownership.

## Main risks
- Small test set may make the measured improvement unstable.
- Accuracy alone may hide class-specific errors.
- Artifacts are missing for four runs.
- Reproducibility evidence is incomplete.
- Staging thresholds, rollback, production monitoring, and ownership are not documented.
- Artifact retention, access permissions, infrastructure cost, and cleanup responsibilities are unclear.

## Required next steps
See `pm_report.md` for the detailed analysis, readiness checklist, and risk register. Before staging review, the team should run additional validation, provide class-level metrics, confirm reproducibility, and document promotion, rollback, monitoring, and operational ownership.
