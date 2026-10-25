# MLflow Experiment Analysis — PM Report

## 1. Business Context

**Use case:** This is an educational machine learning experiment focused on classifying Iris flowers into three species based on their measured characteristics. The objective is to evaluate alternative classification models and identify a candidate for further validation and potential deployment.

**Target users:** The assumed users are ML engineers and data scientists who need reliable classification predictions and reproducible experiment results. No external business users are defined in the provided materials.

**Cost of errors:** Incorrect classification is the primary quality concern. Since the materials do not establish different business consequences for individual species, all misclassifications are treated as equally important. There is no evidence to prioritize false positives over false negatives for a particular class.

**Primary metric:** Accuracy is selected as the primary evaluation metric because the task is multiclass classification and the objective is to maximize correct predictions. Log loss is used as a supporting metric to evaluate the quality of predicted probabilities.

**PM consideration:** The test set contains approximately 45 observations, so small accuracy differences may represent only one or two predictions. Additional validation is required before treating the observed improvement as reliable.

## 2. MLflow Runs Comparison

Five runs were evaluated within the `iris-classification` experiment. All runs used a 30% test split with `split_random_state=7`, enabling comparison on a consistent data split.

| Run                 | Key parameters                                                |   Accuracy |   Log loss | Model artifact | Reproducibility |
| ------------------- | ------------------------------------------------------------- | ---------: | ---------: | -------------- | --------------- |
| `logreg-baseline`   | Logistic Regression; C=1.0; max_iter=500                      |     0.9333 |     0.1913 | Not available  | Partial         |
| `logreg-strong-reg` | Logistic Regression; C=0.01; max_iter=500                     |     0.8444 |     0.6864 | Not available  | Partial         |
| `decision-stump`    | Decision Tree; max_depth=1; random_state=42                   |     0.6667 |     0.4621 | Not available  | Partial         |
| `rf-small`          | Random Forest; n_estimators=10; max_depth=2; random_state=42  |     0.9333 |     0.2278 | Not available  | Partial         |
| `rf-depth3`         | Random Forest; n_estimators=100; max_depth=3; random_state=42 | **0.9556** | **0.1415** | Available      | Partial         |

**Run identification:** Each experiment run has a unique MLflow Run ID. The selected candidate, `rf-depth3`, has the full Run ID `2ddc4f9d563c43e5a7c6c8523a580db3`. The remaining runs can be identified through their run names and Run IDs shown in the monitoring evidence.

**Key observations:**

- `rf-depth3` achieved the highest accuracy and lowest log loss among the five evaluated runs.
- `logreg-baseline` and `rf-small` achieved identical accuracy (93.33%), but the baseline had lower log loss.
- Stronger regularization reduced Logistic Regression performance in this experiment.
- The shallow decision tree produced the lowest accuracy.
- Only `rf-depth3` has a confirmed saved model artifact in the supplied evidence.

**Reproducibility assessment:** Run parameters, random seeds, and evaluation metrics are recorded. However, dataset versioning, source code revision, and complete environment reproducibility have not been independently verified. Reproducibility is therefore assessed as partial for all runs.

## 3. Candidate Selection

**Recommended candidate:** `rf-depth3` (Random Forest, 100 estimators, maximum depth 3).

**Baseline:** `logreg-baseline` (Logistic Regression, C=1.0).

The `rf-depth3` run is the strongest candidate among the five evaluated experiments based on the available evidence.

| Evaluation criterion | Baseline (`logreg-baseline`) | Candidate (`rf-depth3`) |
| -------------------- | ---------------------------: | ----------------------: |
| Accuracy             |                       93.33% |              **95.56%** |
| Log loss             |                       0.1913 |              **0.1415** |
| Saved model artifact |                Not confirmed |           **Available** |
| Full reproducibility |                 Not verified |            Not verified |

**Selection rationale:**

- The candidate improves accuracy by **2.22 percentage points** compared with the baseline.
- It achieves a lower log loss, indicating better performance on the probability-based evaluation metric.
- A saved model artifact is available, supporting further validation and deployment preparation.
- The selected configuration provides the strongest observed results among the evaluated runs.

**Trade-offs and limitations:**

The accuracy improvement represents approximately one additional correct prediction on the 45-sample test set. This is insufficient evidence of a statistically reliable improvement.

The Random Forest candidate uses 100 trees, while the Logistic Regression baseline is a simpler model. Potential differences in inference latency, memory consumption, and infrastructure cost have not been measured.

The provided evidence does not include cross-validation results, per-class precision and recall, a confusion matrix, or performance measurements under realistic serving conditions.

**PM recommendation:** Select `rf-depth3` as the leading candidate for additional validation, but do not approve production deployment based solely on the current experiment results.

## 4. Prometheus Monitoring and Metrics Consistency

Prometheus exposes the experiment evaluation metrics through two metric series: `mlflow_accuracy` and `mlflow_loss`. The exported metrics include labels such as `experiment`, `model_type`, `run_id`, and `run_name`, allowing individual time series to be associated with the corresponding MLflow runs.

### MLflow vs. Prometheus Validation

| Run                 | MLflow accuracy | Prometheus accuracy | MLflow loss | Prometheus loss | Result |
| ------------------- | --------------: | ------------------: | ----------: | --------------: | ------ |
| `logreg-strong-reg` |          0.8444 |              0.8444 |      0.6864 |          0.6864 | Match  |
| `decision-stump`    |          0.6667 |              0.6667 |      0.4621 |          0.4621 | Match  |
| `logreg-baseline`   |          0.9333 |              0.9333 |      0.1913 |          0.1913 | Match  |
| `rf-small`          |          0.9333 |              0.9333 |      0.2278 |          0.2278 | Match  |
| `rf-depth3`         |          0.9556 |              0.9556 |      0.1415 |          0.1415 | Match  |

_Values are rounded to four decimal places for readability._

### Monitoring Assessment

The supplied Prometheus evidence confirms that accuracy and loss values are consistent with the corresponding MLflow experiment results at the displayed precision.

The `run_id` and `run_name` labels support traceability between the monitoring metrics and the original experiments.

However, these are exported experiment metrics rather than live production inference metrics. Their availability does not confirm that the selected model has been deployed or that production monitoring is operational.

### Additional Monitoring Requirements

Before production deployment, the team should establish:

- **Inference latency:** p95 and p99 response times under expected workload.
- **Reliability:** request error rate, availability, and throughput.
- **Model quality:** performance on newly labeled data, including per-class metrics.
- **Data monitoring:** input distribution changes and potential data drift.
- **Operational controls:** monitoring ownership, alert thresholds, escalation procedures, and retention policies.

**PM assessment:** Experiment metrics are consistent across MLflow and Prometheus. Production monitoring readiness remains unverified and requires additional implementation and validation.

## 5. Production Readiness Checklist

| Question                                        | Yes / No / Partial | Comment                                                                                                                                |
| ----------------------------------------------- | ------------------ | -------------------------------------------------------------------------------------------------------------------------------------- |
| Is there a baseline for comparison?             | Yes                | `logreg-baseline` is available with 93.33% accuracy and 0.1913 log loss.                                                               |
| Is the primary metric clearly defined?          | Yes                | Accuracy is the primary metric; log loss is used as a supporting metric.                                                               |
| Is a model artifact available?                  | Yes                | `rf-depth3` has a saved model artifact and supporting environment files in MLflow.                                                     |
| Is the candidate Run ID available?              | Yes                | Full Run ID: `2ddc4f9d563c43e5a7c6c8523a580db3`.                                                                                       |
| Are experiment parameters available?            | Yes                | Model parameters, test split, and random seeds are recorded in MLflow.                                                                 |
| Are metrics available in MLflow?                | Yes                | Accuracy and log loss are recorded for all five runs.                                                                                  |
| Are metrics available in Grafana / Prometheus?  | Yes                | Accuracy and loss are visible in Prometheus and match MLflow values. Grafana evidence was not provided.                                |
| Is there a defined staging promotion criterion? | Partial            | `rf-depth3` is selected based on accuracy, loss, and artifact availability, but formal staging acceptance criteria are not documented. |
| Is there a rollback plan?                       | No                 | No documented rollback procedure or verified previous deployable model version is provided.                                            |
| Is there an owner for the decision?             | No                 | A specific accountable decision owner is not identified in the supplied evidence.                                                      |
| Is there a post-deployment monitoring plan?     | Partial            | Experiment metrics are exported to Prometheus, but production monitoring thresholds, alerting, and response ownership are not defined. |

**PM assessment:** The experiment provides sufficient evidence to select `rf-depth3` as the leading candidate. However, staging promotion criteria, rollback planning, decision ownership, and post-deployment monitoring require further clarification before deployment approval.

## 6. Risk Register

| Risk                                                           | Probability | Potential impact                                                                       | Mitigation                                                                                                    | Proposed owner              |
| -------------------------------------------------------------- | ----------- | -------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------- | --------------------------- |
| **R1. Limited test dataset**                                   | High        | The observed accuracy improvement may not generalize to new data.                      | Perform cross-validation, review per-class metrics and confusion matrix, and validate across repeated splits. | Data Scientist              |
| **R2. Incomplete experiment reproducibility**                  | Medium      | The team may be unable to reproduce results or investigate discrepancies.              | Record dataset version, Git commit, and dependencies; verify reproducibility.                                 | ML Engineer                 |
| **R3. Incomplete model artifact management**                   | Medium      | Model promotion, recovery, or rollback may fail.                                       | Register the approved model, establish artifact retention rules, and verify model loading.                    | MLOps Engineer              |
| **R4. Insufficient production monitoring**                     | High        | Model degradation, inference errors, or latency problems may remain undetected.        | Define production metrics, alert thresholds, dashboards, and incident response procedures.                    | MLOps / DevOps              |
| **R5. Unverified serving performance and infrastructure cost** | Medium      | The model may exceed latency requirements or infrastructure budgets.                   | Conduct staging load tests and measure latency, resource consumption, and cost.                               | MLOps Engineer              |
| **R6. Missing deployment governance**                          | High        | Deployment may proceed without clear approval, rollback procedures, or accountability. | Assign a decision owner, define staging acceptance criteria, and test rollback.                               | Product Manager / Tech Lead |

### Risk Prioritization

Risks R1, R4, and R6 are assigned **High probability** as a conservative planning assessment because the supplied evidence does not demonstrate sufficient validation, production monitoring readiness, or deployment governance.

These ratings are preliminary and should be reviewed with the responsible technical teams.

**PM assessment:** The identified risks do not invalidate `rf-depth3` as the leading candidate, but they prevent unconditional production approval.

## 7. Final PM Decision

**Decision: Need More Experiments / Validation before staging promotion.**

The `rf-depth3` model is selected as the leading candidate based on the five evaluated MLflow runs. It achieved the highest accuracy (95.56%) and lowest log loss (0.1415), outperforming the `logreg-baseline` model. The selected run also includes a saved model artifact and a traceable MLflow Run ID. Accuracy and loss values are consistent between MLflow and Prometheus, supporting confidence in the exported experiment metrics. However, the accuracy improvement over the baseline represents only one additional correct prediction on the 45-sample test set. Additional validation, including cross-validation and per-class performance analysis, is required to establish whether this improvement is reliable. Dataset versioning, source code revision, and end-to-end reproducibility must also be verified. Before staging promotion, the team should confirm that the saved model can be loaded and served successfully and define measurable staging acceptance criteria. Inference latency, resource consumption, and expected infrastructure costs must be evaluated before production approval. The MLOps team should define artifact storage and retention policies, model versioning, and cleanup responsibilities. MLflow and Prometheus access permissions, metrics retention, and monitoring ownership must be documented. Production monitoring should include latency, error rate, throughput, model quality indicators, and appropriate alerts. A rollback procedure and an accountable deployment decision owner must be established before production release. **The PM recommendation is to complete these validation and operational readiness activities before approving staging promotion, while retaining `rf-depth3` as the preferred candidate.**
