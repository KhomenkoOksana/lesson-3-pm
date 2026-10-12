# Docker Compose Readiness Checklist

## 1. Purpose

The purpose of this checklist is to assess whether the local development environment is ready for integration testing using Docker Compose.

The target environment should include:

- Model API
- Model service or model runtime
- Redis
- PostgreSQL
- Monitoring

The main objective is to determine whether the required services can be started together with a single command and whether a test prediction can be completed without manual intervention.

**Important:** This checklist defines the target readiness requirements. A requirement must not be marked as completed until it has been verified. Missing implementation details are marked as `TBD`.

## 2. Readiness Checklist

| Check | Expected Result | Status | Owner / Next Action |
|---|---|---|---|
| `compose.yaml` exists | The repository contains a valid Docker Compose configuration. | TBD | DevOps: create or verify `compose.yaml`. |
| Model API is defined | The API service has a build configuration or image, an entry point, and the required environment variables. | TBD | AI/ML Engineer: confirm the API implementation and startup command. |
| Model runtime is defined | The model runtime can load `model/model.pt` and perform inference. | TBD | AI/ML Engineer: confirm whether the runtime is a separate service or part of the API. |
| Redis is defined | Redis is configured as a local service if required by the application. | TBD | DevOps: configure the service and connection settings. |
| PostgreSQL is defined | PostgreSQL is configured as a local service if required by the application. | TBD | DevOps: configure the database and local connection settings. |
| Monitoring is defined | The required monitoring service or monitoring configuration is available. | TBD | DevOps: confirm the monitoring solution and configuration. |
| Services start automatically | All required services start through Docker Compose without manual container startup. | TBD | DevOps: verify service dependencies and startup configuration. |
| Ports are configured correctly | Required ports are mapped correctly, without unintended conflicts with other local services. | TBD | DevOps: document the API port and other required port mappings. |
| Health endpoint exists | The API exposes a health endpoint, such as `/health`, that reports service readiness. | TBD | AI/ML Engineer: implement or confirm the endpoint. |
| Predict endpoint exists | The API exposes a prediction endpoint, such as `/predict`, with a documented input and output format. | TBD | AI/ML Engineer: implement or confirm the endpoint and provide a test request. |
| Environment variables are externalized | Environment-specific configuration is provided through environment variables or an appropriate local `.env` file. | TBD | DevOps: document required variables and provide a safe example configuration. |
| No production secrets are included | No production credentials, API keys, passwords, or tokens are committed to the repository or image. | TBD | DevOps: review configuration and secret handling. |
| No production infrastructure is connected | The local environment does not connect to production databases, Redis instances, queues, or other production services. | TBD | DevOps: verify connection strings, network settings, and credentials. |
| Model artifact is available | The required model artifact is accessible to the runtime and its version or source is documented. | TBD | AI/ML Engineer: confirm artifact availability and version. |
| Test prediction can be completed | A documented test request returns a valid prediction without manual intervention. | TBD | QA: execute the integration test and record the result. |
| README contains startup instructions | The README documents prerequisites, environment configuration, startup, verification, and shutdown commands. | TBD | AI PM: verify documentation completeness with the engineering team. |

## 3. Verification Commands

The following commands can be used once the corresponding files and services are available.

### 3.1. Validate the Compose Configuration

```bash
docker compose config
```

Expected result: Docker Compose validates the configuration and displays the resolved service configuration without errors.

Owner: DevOps.

### 3.2. Build and Start the Local Environment

```bash
docker compose up --build -d
```

Expected result: All required services are built when necessary and started in the background.

Owner: DevOps.

Note: Successful command execution alone does not prove that every service is healthy or that inference works.

### 3.3. Check Service Status

```bash
docker compose ps
```

Expected result: The required services are running, and health status is available where health checks are configured.

Owner: DevOps.

### 3.4. Review Service Logs

```bash
docker compose logs --tail=100
```

Expected result: The logs do not contain unresolved startup errors, missing dependencies, model-loading failures, or connection errors.

Owner: DevOps and AI/ML Engineer.

### 3.5. Verify the Health Endpoint

Once the API port and endpoint have been confirmed, use the following example:

```bash
curl -f http://localhost:8000/health
```

Expected result: The API returns a successful response indicating that it is ready to serve requests.

Owner: QA.

Note: Port `8000` and endpoint `/health` are examples. The engineering team must confirm the actual port, endpoint, and response format.

### 3.6. Verify the Prediction Endpoint

Once the request format and test image are confirmed, use the documented prediction command.

Example for an API that accepts an uploaded image:

```bash
curl -f -X POST \
  -F "file=@test-image.jpg" \
  http://localhost:8000/predict
```

Expected result: The API returns a valid prediction, including the top-3 classes if required by the acceptance criteria.

Owner: QA and AI/ML Engineer.

Note: The endpoint, input field name, and test image path are illustrative and must be aligned with the actual API implementation.

### 3.7. Stop the Local Environment

```bash
docker compose down
```

Expected result: The Compose-managed containers and network are stopped and removed. Persistent volumes are not removed by this command unless separately configured or requested.

Owner: DevOps.

## 4. Questions for the Engineering Team

1. Does `compose.yaml` already exist, or does it need to be created?
2. Is the model runtime a separate service, or is inference performed directly inside the Model API?
3. Which services are required for the first local integration test?
4. Does the application actually require Redis and PostgreSQL at this stage?
5. Which monitoring tool or service will be used?
6. What port and endpoint are used for health checks?
7. What is the exact request and response format for `/predict`?
8. Where is the model artifact stored, and how is its version identified?
9. How are local environment variables configured?
10. How will the team ensure that local testing cannot access production infrastructure?
11. Can the complete environment start with one documented command?
12. Can QA run a test prediction without manually starting individual services?

## 5. Definition of Readiness

The local environment can be considered ready for integration testing when:

- The Compose configuration is valid.
- All required services are defined and start successfully.
- Service dependencies and health checks are configured appropriately.
- The API health endpoint responds successfully.
- The prediction endpoint accepts a documented test input and returns a valid result.
- The model artifact is available to the runtime.
- Local configuration does not expose production secrets or connect to production infrastructure.
- The startup and verification procedures are documented in the README.

## 6. Final Assessment

**Current readiness status: TBD — verification required.**

The project brief defines the required Docker images and inference artifacts, but the presence of a complete Docker Compose environment has not yet been confirmed.

The engineering team must verify the existence of `compose.yaml`, confirm the required services, configure safe local connections, and demonstrate a successful test prediction.

If the required services are not yet implemented, the checklist should remain open and the missing work should be assigned to the appropriate owners.

The local environment should be recommended for integration testing only after the required checks have been completed and the results have been documented.

A successful local test is an important milestone, but it does not by itself establish production readiness.
