# MLflow Experiment Analysis and PM Decision Report

## 1. Business Context

**ML task:** Iris flower classification using the Iris dataset.

**Objective:** Predict one of three flower species — Setosa, Versicolor, or Virginica — from four input features: sepal length, sepal width, petal length, and petal width.

**Dataset and evaluation setup:**
- Total dataset size: 150 flowers.
- Train/test split: 70% / 30%.
- Test set: 45 flowers.
- The split is stratified.
- `split_random_state = 7`.

**Decision supported by the model:** The model predicts the flower species from measured flower characteristics. The result supports a classification/demo use case; no higher-impact business decision or real end-user workflow was specified in the supplied brief.

**Primary evaluation metrics:**
- **Accuracy** measures the share of correctly classified flowers. Higher is better.
- **Log loss** evaluates the quality and confidence of predicted probabilities and penalizes confident incorrect predictions. Lower is better.

Accuracy is useful as an overall measure, but it is not sufficient on its own. Log loss adds information about prediction confidence. Before a real production decision, the team should also provide a confusion matrix and per-class precision, recall, and F1-score to check whether errors are concentrated in one species.

**Most harmful error:** The supplied brief does not define different business costs for misclassifying the three species. For this task, errors for any class should therefore be reviewed rather than assuming one class is more costly than another.

## 2. MLflow Runs Analysis

Experiment: `iris-classification`.

| Run name | What changed / model | Main parameters | Accuracy | Log loss | Model artifact? | PM comment |
|---|---|---|---:|---:|---|---|
| `logreg-strong-reg` | Logistic Regression with stronger regularization | `C=0.01`, `max_iter=500` | 0.844 | 0.686 | No | Lowest-performing logistic regression run in this comparison; no artifact is available. |
| `decision-stump` | Shallow Decision Tree | `max_depth=1`, `random_state=42` | 0.667 | 0.462 | No | Lowest accuracy; the shallow tree appears too simple for this dataset. |
| `logreg-baseline` | Logistic Regression baseline | `C=1.0`, `max_iter=500` | 0.933 | 0.191 | No | Strong baseline; accuracy is close to the best run, but log loss is higher. |
| `rf-small` | Small Random Forest | `n_estimators=10`, `max_depth=2`, `random_state=42` | 0.933 | 0.228 | No | Matches baseline accuracy but has higher log loss; no saved model artifact. |
| `rf-depth3` | Random Forest with more trees and depth 3 | `n_estimators=100`, `max_depth=3`, `random_state=42` | **0.956** | **0.141** | **Yes** | Best observed result on both reported metrics; artifact and run details are available. |

### Candidate run identification

- **Candidate:** `rf-depth3`
- **Run ID:** `2ddc4f9d563c43e5a7c6c8523a580db3`
- **Status in MLflow:** Finished
- **Logged parameters visible in the supplied run screenshot:** `model_type=random_forest`, `n_estimators=100`, `max_depth=3`, `random_state=42`, `test_size=0.3`, `split_random_state=7`.
- **Logged metrics:** accuracy approximately `0.9556`; log loss approximately `0.1415`.
- **Logged model artifact:** Yes.

The exact run IDs for the other four runs are not legible in the supplied summary data and are not reproduced here. Their run names and metrics are used instead; the full IDs can be added from the corresponding MLflow run pages if required.

### Reproducibility and audit trail

The candidate has a traceable run ID, parameters, metrics, and a saved model artifact. The artifact folder contains:
- `MLmodel`
- `model.skops`
- `conda.yaml`
- `requirements.txt`
- `input_example.json`

This is a useful reproducibility record because it identifies the model format, dependencies, and an example input. However, the supplied evidence does not confirm a versioned dataset reference, source-code commit, full environment lock, or a documented procedure for rebuilding the run. These should be checked before production.

The other four runs have no model artifacts according to the supplied materials. This does not prevent comparing their logged metrics, but it limits the ability to retrieve and deploy those exact trained models.

## 3. Staging / Production Candidate

**Selected candidate: `rf-depth3`.**

Reasons:
1. It has the highest observed accuracy: `0.956`.
2. It has the lowest observed log loss: `0.141`.
3. It outperforms the logistic regression baseline (`0.933` accuracy, `0.191` log loss) on both reported metrics.
4. It has a saved model artifact.
5. Its run ID and key parameters are visible in MLflow, which supports traceability.

The accuracy improvement over the baseline is approximately 2.2 percentage points (from 0.933 to 0.956). Since the test set contains only 45 flowers, this corresponds to approximately one additional correctly classified test example. The improvement is promising, but it should not be treated as conclusive evidence of generalization.

There is also a useful comparison with `rf-small`: both Random Forest runs have similar accuracy (0.933 versus 0.956), while `rf-depth3` has lower log loss (0.141 versus 0.228). This suggests better probability quality in the reported test evaluation.

**Baseline:** `logreg-baseline` is available as a comparison point.

**Trade-off:** The selected Random Forest is more complex than the logistic regression baseline. The supplied results show better test metrics, but do not provide inference latency, resource consumption, model size comparison, or explainability analysis.

**Decision:** `rf-depth3` is the best current candidate, but the evidence is not yet sufficient for an unconditional production promotion. Additional validation is recommended before staging approval, especially because the test set is small and only one split is documented.

## 4. Grafana / Prometheus Review

### Grafana / Prometheus shows:
- `mlflow_accuracy` and `mlflow_loss` are exported.
- The metrics include labels for `experiment`, `run_name`, `run_id`, and `model_type`.
- The time-series graphs and table views show values for the experiment runs.
- A graph gap around 08:34 UTC is documented as an exporter restart; the supplied brief states that it does not affect metric values.

### MLflow UI shows:
- The experiment runs and their names.
- Parameters and evaluation metrics for each run.
- The selected `rf-depth3` run details and its model artifact.

### Together, these tools help:
- MLflow provides experiment-level traceability and stores run parameters, metrics, and artifacts.
- Prometheus/Grafana provides a time-series view that helps monitor exported metrics and compare runs.
- The labels make it possible to associate a metric series with a particular run, provided the labels are retained in the query and dashboard.

### Still missing for a production decision:
- A documented automated reconciliation check between MLflow and Prometheus values.
- Alert thresholds and an owner for responding to alerts.
- Operational serving metrics such as prediction latency, request/error rate, and resource usage after deployment.
- Model-quality monitoring on newly labelled production data.
- A defined process for detecting data drift and model performance degradation.

The supplied values appear consistent with the MLflow results at the displayed precision. The exporter restart should be recorded as an observability interruption, not interpreted as a change in model quality.

## 5. Production-Readiness Checklist

| Question | Status | Comment |
|---|---|---|
| Is there a baseline for comparison? | Yes | `logreg-baseline` is available. |
| Is the primary metric clear? | Partially | Accuracy and log loss are defined; class-level metrics are still needed. |
| Is there a model artifact? | Yes | Available for `rf-depth3` only. |
| Is there a run ID for the candidate? | Yes | Candidate run ID is recorded above. |
| Are experiment parameters logged? | Yes | Candidate parameters are visible in MLflow. |
| Are metrics logged in MLflow? | Yes | Accuracy and loss are available. |
| Are metrics available in Prometheus/Grafana? | Yes | Both metrics are exported with identifying labels. |
| Is there a documented staging promotion threshold? | No evidence | Acceptance thresholds have not been supplied. |
| Is there a rollback plan? | No evidence | Rollback owner and procedure are not documented. |
| Is there a named decision owner? | No evidence | Ownership is not specified in the supplied materials. |
| Is there a post-deployment monitoring plan? | Partially | Exported experiment metrics are shown, but production model-quality monitoring is not described. |
| Are artifact storage and retention policies defined? | No evidence | Storage location, retention period, and cleanup policy are not specified. |
| Are access permissions defined? | No evidence | MLflow/Grafana access roles are not specified. |
| Is infrastructure cost ownership defined? | No evidence | Cost owner and budget controls are not specified. |

## 6. Risk Register

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Candidate is selected based on a small test set | Medium | Medium | Repeat evaluation with cross-validation and/or additional held-out data; report confidence intervals where appropriate. |
| Accuracy hides errors for a particular species | Medium | Medium | Add confusion matrix, per-class precision, recall, and F1-score; review errors by class. |
| Model artifact is missing for four runs | High | Medium | Log and retain artifacts for all runs that may be considered for promotion; define artifact retention rules. |
| Experiment cannot be fully reproduced | Medium | High | Record dataset version, code commit, dependency versions, random seeds, and execution environment. |
| No explicit promotion thresholds | High | High | Agree acceptance criteria for accuracy, log loss, and class-level metrics before staging/production. |
| No rollback procedure is documented | Medium | High | Define rollback trigger, previous approved model version, responsible owner, and tested rollback steps. |
| Prometheus metrics may become inconsistent with MLflow | Low/Medium | Medium | Add a reconciliation check using `run_id`; document exporter restart behavior and alert on missing/stale series. |
| No production drift/performance monitoring plan | Medium | High | Define data-quality and drift checks, delayed-label evaluation, retraining triggers, and alert ownership. |
| Ownership, access, cost, and cleanup are unclear | Medium | Medium | Assign owners for MLflow/Grafana access, artifact storage, infrastructure budget, retention, and resource cleanup. |

Likelihood and impact are qualitative PM assessments based on the gaps in the supplied evidence, not measured incident probabilities.

## 7. PM Decision Report

**Recommendation: Need more experiments before staging approval.**

The `rf-depth3` Random Forest is the strongest candidate among the five evaluated runs. It achieved the highest accuracy (0.956) and the lowest log loss (0.141) in the supplied test results. It also has a saved model artifact, a traceable MLflow run ID, and logged parameters. The `logreg-baseline` provides a useful baseline with 0.933 accuracy and 0.191 log loss. The observed accuracy gain is approximately 2.2 percentage points, which represents about one additional correct prediction on a 45-example test set. Therefore, the improvement is promising but should not be considered conclusive. Prometheus exports both accuracy and loss with labels that include the run ID, and the documented exporter restart does not affect the metric values. Before staging approval, the team should repeat validation using cross-validation or additional test data and provide a confusion matrix plus per-class precision, recall, and F1-score. The team should also confirm dataset and code versioning so the candidate can be reproduced. Staging acceptance thresholds, rollback steps, monitoring responsibilities, and a named decision owner must be documented. Artifact storage and retention, MLflow/Grafana access, infrastructure cost ownership, and cleanup responsibilities also need clarification. After these checks pass, the PM and ML lead can review the evidence and make a controlled staging decision. Production promotion should require successful staging validation and an agreed post-deployment monitoring and rollback plan.

### Actions required before staging review

1. Run cross-validation or evaluate on an additional independent test set.
2. Provide confusion matrix and per-class precision, recall, and F1-score.
3. Confirm dataset version, code commit, dependency versions, and reproducibility steps.
4. Define acceptance thresholds for all agreed model-quality metrics.
5. Document rollback procedure and assign a decision owner.
6. Define monitoring, alert response, artifact retention, access, cost, and cleanup ownership.
