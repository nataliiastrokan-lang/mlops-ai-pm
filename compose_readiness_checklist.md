# Docker Compose Readiness Checklist

## Purpose

This checklist evaluates whether the PayGuard AI local inference environment can be started and tested as an integrated system using Docker Compose.

A working Docker image alone is not sufficient for integration readiness. The local environment must include the required services, configuration, dependencies, and health checks and must support an end-to-end test prediction without manual service startup.

## 1. Compose Configuration

| Check                                                             | Status | Evidence / Comment              |
| ----------------------------------------------------------------- | ------ | ------------------------------- |
| `compose.yaml` exists                                             | ☐      | Verify in repository            |
| `compose.yaml` is valid and can be parsed by Docker Compose       | ☐      | Validate through Docker Compose |
| Local environment can be started with `docker compose up --build` | ☐      | Execution evidence required     |
| Startup command is documented in `README.md`                      | ☐      | Review README                   |
| All required services are defined in Compose                      | ☐      | Review service definitions      |

## 2. Required Services

The local environment is expected to include:

- `model-api`;
- model service or model runtime;
- Redis;
- PostgreSQL;
- monitoring.

| Service                 | Defined | Starts Successfully | Comment |
| ----------------------- | ------- | ------------------- | ------- |
| `model-api`             | ☐       | ☐                   |         |
| Model service / runtime | ☐       | ☐                   |         |
| Redis                   | ☐       | ☐                   |         |
| PostgreSQL              | ☐       | ☐                   |         |
| Monitoring              | ☐       | ☐                   |         |

## 3. Service Integration

| Check                                                      | Status | Evidence / Comment |
| ---------------------------------------------------------- | ------ | ------------------ |
| Services start without requiring separate manual startup   | ☐      |                    |
| `model-api` can communicate with the model service/runtime | ☐      |                    |
| Required Redis connection is configured                    | ☐      |                    |
| Required PostgreSQL connection is configured               | ☐      |                    |
| Required service dependencies are correctly defined        | ☐      |                    |
| Required ports are configured correctly                    | ☐      |                    |
| Services can communicate through the local Compose network | ☐      |                    |

## 4. API Readiness

| Check                                                                 | Status | Evidence / Comment                          |
| --------------------------------------------------------------------- | ------ | ------------------------------------------- |
| Health endpoint is available                                          | ☐      | Endpoint/path to be confirmed by AI/ML team |
| Health endpoint returns a successful status when the service is ready | ☐      | Test evidence required                      |
| Predict endpoint is available                                         | ☐      | Endpoint/path to be confirmed by AI/ML team |
| Predict endpoint accepts the expected test input                      | ☐      |                                             |
| Predict endpoint returns a top-3 prediction                           | ☐      |                                             |
| Test prediction can be executed without undocumented manual actions   | ☐      |                                             |

## 5. Configuration and Environment

| Check                                                                                        | Status | Evidence / Comment   |
| -------------------------------------------------------------------------------------------- | ------ | -------------------- |
| Environment-specific configuration is externalized through `.env` where appropriate          | ☐      | Review configuration |
| Required environment variables are documented                                                | ☐      |                      |
| Local/default values do not contain production credentials                                   | ☐      |                      |
| Production secrets are not stored in `compose.yaml`                                          | ☐      |                      |
| Production secrets are not committed in `.env`                                               | ☐      |                      |
| Local environment does not connect to production PostgreSQL                                  | ☐      |                      |
| Local environment does not connect to production Redis                                       | ☐      |                      |
| Local environment does not connect to a production queue or other production-only dependency | ☐      |                      |

## 6. End-to-End Local Validation

The local environment should be validated using the following flow:

```text
Test input
    ↓
model-api
    ↓
model service / runtime
    ↓
prediction
    ↓
top-3 result
```

Supporting services such as Redis, PostgreSQL, and monitoring must start as part of the same Compose environment where required by the solution.

Validation should confirm that:

- [ ] all services start using one Compose command;
- [ ] required services reach a healthy/ready state;
- [ ] the test input can be submitted through the API;
- [ ] inference completes successfully;
- [ ] a top-3 prediction is returned;
- [ ] no manual service startup or undocumented configuration is required.

## Current Readiness Status

**Compose readiness:** `TBD — implementation evidence not yet provided`

The local environment can be considered ready when all required services start with:

`docker compose up --build`

and a test prediction can be completed successfully without manual startup or undocumented configuration steps.

Local Compose readiness demonstrates that the components can operate together in an integration-style environment. It does not by itself demonstrate production readiness.

Any failed or unverifiable mandatory check must be resolved or documented as a risk before the solution proceeds toward staging.
