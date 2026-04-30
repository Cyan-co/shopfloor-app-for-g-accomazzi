# Phase D: Technology Architecture (TA)

## 1. Infrastructure Overview

This document specifies the infrastructure and operational environment for the Shop Floor Material Supply System.

### Container Platform

- **Runtime**: Docker
- **Orchestration**: Kubernetes will be used for STAGING and PROD environments. Docker Compose is suitable for local DEV environments.
- **Registry**: GitHub Container Registry (ghcr.io)

### Deployment Environments

| Environment | Purpose | Scaling | Resources |
|---|---|---|---|
| DEV | Local development | 1 replica | Minimal (Docker Compose) |
| TEST | Automated integration testing | 1 replica | Minimal |
| STAGING | Pre-production validation, UAT | 2 replicas | Production-like |
| PROD | Live production environment | 3+ replicas (auto-scaling) | Full |

---

## 2. Network Architecture

### Ingress Configuration

- **Load Balancer**: A cloud-provider managed L7 Load Balancer.
- **Ingress Controller**: NGINX Ingress Controller for Kubernetes.
- **TLS Termination**: TLS will be terminated at the Ingress Controller, using certificates managed by cert-manager with Let's Encrypt.
- **DNS**: `api.prod.shopfloor.g-accomazzi.com`, `app.prod.shopfloor.g-accomazzi.com`

### Service Communication

- **Internal**: Service-to-service communication will use Kubernetes DNS resolution within the cluster.
- **External**: All external traffic to the backend MUST go through an API Gateway, which handles request routing to the appropriate service.

### Network Policies

- Kubernetes Network Policies will be enforced to restrict traffic. Default policy is `deny-all`.
- Egress traffic from the cluster will be restricted to known external endpoints (e.g., external inventory system API).

---

## 3. Database Infrastructure

### PostgreSQL Configuration

- **Version**: 15+
- **Connection Pooling**: PgBouncer will be used as a sidecar to the application pods to manage connections efficiently.
- **Backup Strategy**: Daily full snapshots with Point-In-Time Recovery (PITR) enabled, retained for 30 days.
- **Replication**: A primary-replica setup will be used for the PROD database to ensure high availability.

---

## 4. Caching Infrastructure

### Redis Configuration

- **Purpose**: Primarily for user session caching to support stateless application design. Can be extended for query result caching if performance bottlenecks are identified.
- **Eviction Policy**: `allkeys-lru` (Least Recently Used).
- **Persistence**: RDB snapshots enabled for basic disaster recovery of session data.

---

## 5. Security Infrastructure

### Authentication & Authorization

- **Protocol**: OpenID Connect (OIDC) built on OAuth 2.0.
- **Identity Provider**: Keycloak will be deployed as the identity provider.
- **Token format**: JSON Web Tokens (JWT).
- **Token storage**: Tokens will be stored in HttpOnly cookies to mitigate XSS risks.

### Secrets Management

- **Storage**: Kubernetes Secrets will be used for managing all application secrets (API keys, DB credentials).
- **Access**: Access to secrets will be restricted via RBAC to specific service accounts.

### TLS Configuration

- **Minimum Version**: TLS 1.2 is required for all external communication.
- **Certificate Authority**: Let's Encrypt.

---

## 6. Monitoring & Observability

### Metrics
- **Tool**: Prometheus for metric collection.
- **Dashboards**: Grafana for visualization.
- **Required Metrics**: RED metrics (Rate, Errors, Duration) for all services, JVM metrics for the backend, and business metrics (e.g., orders created/fulfilled).

### Logging
- **Tool**: The ELK Stack (Elasticsearch, Logstash, Kibana).
- **Log Format**: All services must output structured JSON logs to stdout.

### Tracing
- **Tool**: Jaeger will be integrated for distributed tracing.
- **Propagation**: W3C Trace Context standard will be used.

### Alerting
- Prometheus Alertmanager will be configured with critical alerts for error rates, latency spikes, and service unavailability, with notifications sent to a dedicated engineering channel.

---

## 7. CI/CD Pipeline

- **Platform**: GitHub Actions.
- **Pipeline Trigger**: On push to `main` (for DEV/TEST) and on tag creation (for STAGING/PROD).

### Pipeline Stages

```
Build -> Test -> Security Scan -> Deploy (DEV) -> Deploy (TEST) -> Deploy (STAGING) -> Deploy (PROD)
```

### Stage Requirements

| Stage | Actions | Gate |
|---|---|---|
| Build | Compile, Dockerize, Unit Tests | All tests pass |
| Test | Integration Tests | All tests pass |
| Security Scan | SonarQube, Trivy (for vulnerabilities) | No critical vulnerabilities |
| Deploy PROD | Manual approval via GitHub Actions environment | STAGING success + approval |

---

## 8. Disaster Recovery

### Backup Strategy

| Component | Frequency | Retention | RTO/RPO |
|---|---|---|---|
| Database (PG) | Daily full, continuous WAL archiving | 30 days | < 1 hour / < 5 mins |
| Configurations (Git) | On change (Git history) | Indefinite | < 15 minutes |
| Secrets (K8s) | Backed up with etcd | Daily | < 30 minutes |

### Recovery Procedures
- A full disaster recovery runbook will be maintained in the project's wiki, detailing the step-by-step process to restore the service in a new region. Target RTO is < 4 hours.
