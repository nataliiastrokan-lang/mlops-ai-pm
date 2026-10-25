# mlops-ai-pm

MLOps CI/CD course — AI Product Management homework and final project

# MLflow Experiment Analysis — PM Homework

## Overview

This homework provides a Product Manager's assessment of five machine learning experiment runs in the `iris-classification` MLflow experiment.

The analysis focuses on model comparison, candidate selection, reproducibility, monitoring consistency, production readiness, risks, and the final PM decision.

## Contents

- [`pm_report.md`](./pm_report.md) — complete PM analysis and deployment recommendation.
- [`screenshots/`](./screenshots/) — nine screenshots documenting MLflow runs, model artifacts, and Prometheus metrics.

## Evidence

| Screenshots                                                              | Description                                       |
| ------------------------------------------------------------------------ | ------------------------------------------------- |
| `01_mlflow_runs_overview.png` – `02_mlflow_metrics_comparison.png`       | Experiment overview and model comparison          |
| `03_mlflow_best_run_details.png` – `05_mlflow_rf_small_no_artifacts.png` | Candidate configuration and artifact verification |
| `06_prometheus_accuracy_graph.png` – `09_prometheus_loss_values.png`     | Prometheus accuracy/loss graphs and values        |

## Key Findings

The `rf-depth3` run achieved the highest accuracy (95.56%) and lowest log loss (0.1415) among five evaluated runs. Its model artifact is available, and the exported Prometheus metrics match the MLflow results.

However, the limited test dataset, incomplete reproducibility evidence, and outstanding deployment controls require further validation.

**PM Decision: Need More Experiments / Validation before staging promotion.**

## Scope

This submission is based on the provided MLflow and Prometheus evidence. It does not include model training, infrastructure deployment, or independently executed experiments.
