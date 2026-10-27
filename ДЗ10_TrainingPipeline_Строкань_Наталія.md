# Automated Model Training Pipeline — PM Analysis

## 1. High-Level Workflow

**Objective:** Establish a controlled automated model training process using GitLab CI and AWS Step Functions, with experiment tracking, model registration, failure handling, and team notifications.

### Workflow Overview

```text
GitLab event / Manual trigger
              |
              v
       GitLab CI Pipeline
              |
              v
     AWS Step Functions
              |
              v
       Data Validation
              |
              v
         Training Job
              |
              v
       Model Evaluation
              |
              v
 Logging Metrics & Artifacts
              |
              v
     Model Registry Update
              |
              v
       Team Notification
```

### Component Responsibilities

| Component                     | Responsibility                                                                                                                              |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| GitLab event / Manual trigger | Initiates training based on predefined rules or an authorized manual request.                                                               |
| GitLab CI                     | Validates the trigger context, passes the commit SHA and branch, and starts the AWS Step Functions execution.                               |
| AWS Step Functions            | Orchestrates the training workflow, tracks execution status, manages retries for transient failures, and routes errors to failure handling. |
| Data Validation               | Checks dataset availability, required fields, and schema compatibility before training.                                                     |
| Training Job                  | Trains the model using the selected code, dataset, and configuration.                                                                       |
| Model Evaluation              | Calculates model metrics and compares results with the defined baseline and quality thresholds.                                             |
| MLflow                        | Records experiment parameters, metrics, run IDs, and model artifacts for traceability.                                                      |
| Model Registry                | Registers a model version as a candidate when the required quality gates are satisfied; registration does not imply production approval.    |
| Team Notification             | Reports success, rejection, or technical failure, including execution details, metrics, and the responsible next-step owner.                |

### Process Control

The pipeline must stop or route execution to failure handling when data validation fails or an unrecoverable technical error occurs. Transient technical failures may be retried according to predefined limits.

If training succeeds but the model fails quality gates, the result must be recorded and communicated without promoting the model.

A successful pipeline execution does not automatically authorize production deployment. Model promotion requires a separate approval decision based on quality, operational readiness, and defined ownership.

## 2. Pipeline Checkpoints

The following checkpoints define the required controls, responsible roles, success criteria, and failure-handling actions for the automated training pipeline.

| Pipeline stage                   | What is checked                                                                              | Owner                        | Success criteria                                                                  | Action on failure                                                                                    |
| -------------------------------- | -------------------------------------------------------------------------------------------- | ---------------------------- | --------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| **Data Validation**              | Dataset availability, schema compatibility, required fields, and missing critical values.    | Data Engineer                | Dataset passes all mandatory validation checks.                                   | Stop the pipeline, log validation errors, and notify the Data Engineer.                              |
| **Training Job**                 | Correct code version, dataset reference, model configuration, and training execution status. | ML Engineer                  | Training completes successfully with traceable input versions and configuration.  | Review execution logs; retry transient failures within defined limits or escalate persistent errors. |
| **Model Evaluation**             | Accuracy, log loss, comparison with baseline, and defined quality thresholds.                | ML Engineer + AI PM          | Model meets all required quality gates.                                           | Record the evaluation result, block model promotion, and notify the responsible team.                |
| **Metrics and Artifact Logging** | MLflow Run ID, parameters, evaluation metrics, and saved model artifact.                     | ML Engineer / MLOps Engineer | Metrics and model artifact are successfully logged and linked to the correct run. | Stop candidate promotion, investigate logging or storage errors, and retry when safe.                |
| **Model Registry**               | Model version, artifact reference, and candidate status.                                     | MLOps Engineer               | Qualified model is registered with traceable version and candidate status.        | Block promotion and investigate registry or artifact access errors.                                  |
| **Team Notification**            | Execution outcome, commit/branch, metrics, failure reason, and next-step owner.              | MLOps Engineer               | Team receives a clear success, rejection, or failure notification.                | Retry notification delivery and escalate if the team cannot be informed.                             |

### Checkpoint Policy

Each checkpoint must have an explicit success condition and failure-handling action. A technical failure must not be treated as a model quality failure, and a model that fails evaluation must not be promoted automatically.

Transient infrastructure failures may be retried within predefined limits. Data validation and quality-gate failures require investigation or corrective action rather than repeated execution with unchanged inputs.

**PM control:** Pipeline completion and model release approval remain separate decisions. Only models that satisfy the required quality gates can proceed to candidate registration, and production promotion requires additional approval.

## 3. Quality Gates for a New Model

Quality gates determine whether a newly trained model is eligible for candidate registration and further release consideration. Passing technical training checks alone does not authorize production deployment.

For this educational scenario, Iris classification is used as an illustrative model. The thresholds below are proposed acceptance rules and must be validated before implementation.

| Quality gate                   | Proposed rule                                                                                                              | Owner               | Decision / Action                                                                                      |
| ------------------------------ | -------------------------------------------------------------------------------------------------------------------------- | ------------------- | ------------------------------------------------------------------------------------------------------ |
| **Data quality**               | No critical schema errors, missing mandatory features, or invalid target labels.                                           | Data Engineer       | Pass: continue training. Fail: stop pipeline and notify Data Engineer.                                 |
| **Primary ML metric**          | Accuracy ≥ 0.93 on the defined evaluation dataset.                                                                         | ML Engineer         | Pass: continue evaluation. Fail: reject candidate promotion.                                           |
| **Supporting ML metric**       | Log loss ≤ 0.20.                                                                                                           | ML Engineer         | Pass: continue. Fail: reject candidate promotion and investigate.                                      |
| **Baseline comparison**        | Accuracy must be at least equal to the approved baseline, and log loss must not exceed the baseline log loss.              | ML Engineer + AI PM | Pass: eligible for candidate registration. Fail: retain current baseline and request further analysis. |
| **Business / segment quality** | No material degradation in any Iris class compared with the baseline, based on per-class recall and confusion matrix.      | AI PM + ML Engineer | Pass: continue approval review. Fail: request additional validation.                                   |
| **Manual approval**            | AI PM and Tech Lead review quality results, reproducibility evidence, and identified risks before staging promotion.       | AI PM / Tech Lead   | Approved: allow staging promotion. Not approved: keep model as candidate or rejected.                  |
| **Model status**               | Only models passing mandatory quality gates may be registered as candidates; production status requires separate approval. | MLOps Engineer      | Update candidate status and preserve decision traceability; never auto-promote to production.          |

### Quality Gate Failure Policy

If training completes successfully but the model fails a quality gate, the pipeline must record the metrics and failure reason, block candidate promotion, and notify the responsible owner.

The existing approved model remains unchanged. The ML Engineer investigates the result and proposes corrective actions or additional experiments.

**PM decision rule:** A model may proceed to staging only after meeting the defined quality thresholds and receiving explicit approval. Production release requires a separate readiness assessment and authorization.

## 4. Training Pipeline Trigger Rules

Training pipeline triggers should be selected based on the environment, expected benefits, operational risks, and cost implications. Not every code or data change should automatically initiate a production-level training workflow.

| Trigger                           | When appropriate                                                                              | Benefit                                                           | Risk                                                                                          | Environment / Policy                                                                                                        |
| --------------------------------- | --------------------------------------------------------------------------------------------- | ----------------------------------------------------------------- | --------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Push / merge to main**          | When approved changes to training code or configuration are merged.                           | Early detection of integration and training issues.               | Frequent or unnecessary training runs and increased compute costs.                            | Dev: automatic for relevant changes. Staging: conditional. Prod: no automatic deployment.                                   |
| **Manual trigger**                | For controlled experiments, troubleshooting, or release candidate preparation.                | Allows an authorized user to select the timing and configuration. | Human error, incorrect parameters, or unauthorized execution.                                 | Dev / Staging: permitted. Prod-related execution: restricted to authorized roles using GitLab OIDC federation with AWS IAM. |
| **Scheduled training**            | When data is updated regularly and periodic retraining is justified.                          | Predictable retraining cadence and resource planning.             | Unnecessary training when data has not changed; recurring infrastructure costs.               | Dev / Staging: optional. Prod-related training: only with an approved schedule, budget, and restricted AWS permissions.     |
| **Training configuration change** | When model parameters, feature settings, or training configuration are updated.               | Validates the impact of configuration changes.                    | Excessive runs caused by minor or irrelevant changes.                                         | Dev: automatic for relevant changes. Staging: controlled validation. Prod: no automatic deployment.                         |
| **Data / model drift detected**   | When monitoring indicates that data distribution or model performance has materially changed. | Enables timely investigation and retraining.                      | False alerts, unstable retraining frequency, and potentially poor-quality replacement models. | Staging: retraining may be initiated after validation. Prod: investigation and approval required before model promotion.    |

### Trigger Governance and AWS Access Control

GitLab CI should validate the trigger type, branch, commit SHA, and required parameters before starting AWS Step Functions.

Automatic triggers should be limited to relevant changes, with controls against duplicate executions and unnecessary compute usage.

**AWS authentication and authorization:**

- GitLab CI authenticates to AWS through **OpenID Connect (OIDC) federation**, obtaining temporary credentials via AWS STS instead of storing long-lived AWS access keys.
- Separate IAM roles are configured for non-production and production environments, following the **least-privilege principle**.
- Production IAM role trust policies restrict access to authorized GitLab projects and protected branches or environments.
- IAM permissions restrict GitLab CI to starting and monitoring the explicitly authorized AWS Step Functions state machine.
- AWS Step Functions uses its own execution role for downstream AWS services, with permissions limited to the required workflow operations.

**Production control:**

A successful OIDC authentication authorizes access to the permitted AWS resources but does not authorize model deployment. Production promotion requires successful quality gates, documented evaluation results, and explicit manual approval.

The AI PM defines the trigger and approval policies together with the ML Engineer and MLOps Engineer, balancing model quality, security, operational reliability, and training costs.

## 5. Risk Register

The following risks cover security, data quality, model performance, operational reliability, and cost management within the automated training pipeline.

Likelihood and impact are assessed qualitatively as **Low / Medium / High**.

| ID  | Risk                                                                                                                                     | Likelihood | Impact | Mitigation                                                                                                                                                                            | Owner                        |
| --- | ---------------------------------------------------------------------------------------------------------------------------------------- | ---------- | ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------- |
| R1  | **Excessive AWS permissions:** GitLab CI or Step Functions roles have broader access than required.                                      | Medium     | High   | Use GitLab OIDC federation, temporary AWS credentials, separate environment-specific IAM roles, restrictive trust policies, and least-privilege permissions. Review access regularly. | DevOps / Security Engineer   |
| R2  | **Excessive training costs:** Frequent triggers or duplicate executions increase AWS compute expenses.                                   | High       | High   | Restrict triggers to relevant changes, prevent duplicate runs, define execution limits, and configure AWS budget alerts.                                                              | MLOps Engineer / AI PM       |
| R3  | **Invalid or incomplete training data:** Missing required fields, schema changes, or corrupted data produce unreliable training results. | Medium     | High   | Enforce mandatory data validation before training; stop execution on critical errors and notify the Data Engineer.                                                                    | Data Engineer                |
| R4  | **Model fails quality gates:** Training completes, but accuracy, log loss, or baseline comparison does not meet acceptance criteria.     | Medium     | High   | Apply automated evaluation thresholds, preserve metrics and failure reasons, block candidate promotion, and require further investigation.                                            | ML Engineer / AI PM          |
| R5  | **AWS workflow execution failure:** Temporary service outages, timeouts, or failed training jobs interrupt the pipeline.                 | Medium     | Medium | Configure Step Functions retry with bounded attempts and backoff for transient errors; use failure handling and notifications for persistent errors.                                  | MLOps Engineer               |
| R6  | **Missing experiment traceability:** MLflow metrics, parameters, run IDs, or model artifacts are not stored correctly.                   | Medium     | High   | Verify successful logging and artifact availability before candidate registration; retain commit SHA, dataset reference, and run ID.                                                  | ML Engineer / MLOps Engineer |
| R7  | **Unauthorized or premature model promotion:** A model is released without completing quality gates or obtaining approval.               | Low        | High   | Separate candidate registration from production promotion; enforce manual approval, protected environments, and role-based access.                                                    | AI PM / MLOps Engineer       |
| R8  | **Model Registry inconsistency:** An incorrect model version or artifact is registered or linked to the wrong training run.              | Medium     | High   | Validate model version, artifact reference, and MLflow Run ID before registration; maintain an auditable version history.                                                             | MLOps Engineer               |
| R9  | **Notification failure:** Pipeline failures or rejected models are not communicated to responsible team members.                         | Medium     | Medium | Monitor notification delivery, retry failed notifications, and provide an alternative escalation mechanism.                                                                           | MLOps Engineer               |

### Risk Management Approach

High-impact risks must be addressed before the pipeline is considered production-ready. In particular, excessive AWS permissions, uncontrolled training costs, and failed quality gates require explicit preventive controls.

The AI PM reviews risk status with the technical owners and ensures that unresolved critical risks are included in the production-readiness decision.

A successful training execution does not override security restrictions, quality requirements, or manual production approval.

## 6. Jira Acceptance Criteria

**Story:** Integrate GitLab CI with AWS Step Functions for an Automated Training Pipeline

**User Story:**

As an MLOps Engineer, I want GitLab CI to securely trigger and monitor an AWS Step Functions training workflow so that model training is automated, traceable, and controlled by defined quality gates.

### Acceptance Criteria

**AC1 — Workflow Trigger**

- **Given** an authorized GitLab CI pipeline is running,
- **When** the training stage is triggered,
- **Then** GitLab CI starts the configured AWS Step Functions state machine and receives an execution identifier.

**AC2 — Execution Context**

- **Given** GitLab CI initiates a training workflow,
- **When** the execution starts,
- **Then** the workflow receives the commit SHA, branch, trigger source, and relevant training configuration references.

**AC3 — Execution Visibility**

- **Given** an AWS Step Functions execution has started,
- **When** a team member reviews the GitLab pipeline,
- **Then** the execution status and a reference to the AWS execution details are available.

**AC4 — Data Validation**

- **Given** the training workflow has started,
- **When** data validation is executed,
- **Then** training proceeds only if all mandatory data checks pass; otherwise, execution is stopped and the failure reason is recorded.

**AC5 — Metrics and Artifact Logging**

- **Given** model training and evaluation have completed,
- **When** results are processed,
- **Then** the MLflow Run ID, parameters, evaluation metrics, and model artifact are stored and traceable to the execution.

**AC6 — Quality Gates**

- **Given** evaluation metrics are available,
- **When** the model is compared with the approved thresholds and baseline,
- **Then** a model that fails any mandatory quality gate is blocked from candidate promotion, and the failure reason is recorded.

**AC7 — Model Registry Update**

- **Given** the model passes all mandatory quality gates and required artifacts are available,
- **When** the registry stage executes,
- **Then** the model is registered as a candidate with the correct version, artifact reference, and MLflow Run ID.

**AC8 — Secure AWS Access**

- **Given** an authorized GitLab CI job needs to access AWS,
- **When** it requests AWS credentials,
- **Then** authentication uses OIDC federation and temporary credentials, with IAM permissions restricted to the required environment and Step Functions operations.

**AC9 — Success and Failure Notifications**

- **Given** the workflow finishes successfully, fails technically, or rejects a model due to quality gates,
- **When** the final outcome is determined,
- **Then** the responsible team receives a notification containing the execution status, commit/branch, available metrics, execution reference, and next-step owner.

**AC10 — Production Promotion Control**

- **Given** a model has successfully completed training and passed quality gates,
- **When** the model becomes eligible for release,
- **Then** no automatic production deployment occurs without explicit approval from the authorized decision-makers.

## 7. Success and Failure Notifications

The automated training pipeline must notify the responsible team about the final execution outcome. Notifications should provide sufficient context to identify the training run, review results, and determine the next action.

The examples below use illustrative values and links.

### Example 1 — Successful Training Pipeline

**Subject:** [SUCCESS] Automated Model Training — Candidate Ready for Review

- **Pipeline:** Automated Model Training Pipeline
- **Status:** SUCCESS — Quality gates passed
- **Environment:** Staging
- **Branch:** main
- **Commit SHA:** a1b2c3d
- **MLflow Run ID:** run-1042
- **Key Metrics:** Accuracy: 0.9556; Log Loss: 0.1415
- **Model Registry:** New candidate version registered
- **Execution Details:** AWS Step Functions execution link / GitLab pipeline link
- **Next Action:** Review candidate results and determine whether to approve the model for staging deployment.
- **Owner:** AI PM / Tech Lead

**Note:** Successful training and candidate registration do not authorize automatic production deployment.

### Example 2 — Technical Pipeline Failure

**Subject:** [FAILED] Automated Model Training — Data Validation Error

- **Pipeline:** Automated Model Training Pipeline
- **Status:** FAILED — Data validation
- **Environment:** Staging
- **Branch:** main
- **Commit SHA:** a1b2c3d
- **Failure Reason:** Required dataset column is missing.
- **Key Metrics:** Not available — training was not started.
- **Model Registry:** No candidate registered
- **Execution Details:** AWS Step Functions execution link / validation logs
- **Next Action:** Investigate the dataset schema, correct the source data, and rerun validation.
- **Owner:** Data Engineer

### Example 3 — Model Rejected by Quality Gates

**Subject:** [REJECTED] Automated Model Training — Quality Gates Not Passed

- **Pipeline:** Automated Model Training Pipeline
- **Status:** REJECTED — Model quality
- **Environment:** Staging
- **Branch:** main
- **Commit SHA:** a1b2c3d
- **MLflow Run ID:** run-1043
- **Key Metrics:** Accuracy: 0.89; Log Loss: 0.24
- **Quality Gate Result:** Accuracy below 0.93; log loss above 0.20
- **Model Registry:** Candidate registration blocked
- **Execution Details:** AWS Step Functions execution link / MLflow run link
- **Next Action:** Investigate model performance, review training data and parameters, and plan corrective experiments.
- **Owner:** ML Engineer / AI PM

### Notification Policy

Notifications must distinguish between successful execution, technical failure, and model quality rejection.

Each message must include the pipeline name, final status, commit or branch reference, available metrics, execution details, responsible owner, and required next action.

If notification delivery fails, the system should retry delivery within predefined limits and provide an alternative escalation path.

## 8. Conclusion

The proposed automated training pipeline integrates GitLab CI and AWS Step Functions to provide a controlled and traceable model training process.

Data validation, model evaluation, MLflow tracking, and Model Registry controls help ensure that only qualified models proceed to candidate registration.

OIDC-based AWS authentication, least-privilege IAM roles, and environment-specific access restrictions reduce security risks.

Defined trigger policies, bounded retries, and cost monitoring improve operational reliability and prevent unnecessary resource consumption.

Quality gates and baseline comparisons ensure that technical training success is not confused with acceptable model performance.

Before production release, the team must verify security, data and model quality, monitoring, rollback readiness, and explicit approval from responsible stakeholders.

**A successfully completed training pipeline does not automatically mean that the model is production-ready.**
