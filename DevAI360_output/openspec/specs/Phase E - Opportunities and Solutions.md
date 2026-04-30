# Phase E: Opportunities and Solutions

## 1. Gap Analysis Summary

### Current State

The current process is a greenfield scenario with respect to a technical solution. It relies on a manual, paper-based system for material requests between the production line and the warehouse. There is no existing software to replace or integrate with for this core function.

### Target State

The target state is a fully digital, real-time web application as defined in the Phase B, C, and D architecture documents. This includes dedicated user interfaces, a robust backend, a relational database, and a cloud-native infrastructure.

### Gap Summary Table

| # | Gap Area | Current State | Target State | Priority |
|---|---|---|---|---|
| 1 | User Interface | Non-existent | Role-based Web UI (Angular) | High |
| 2 | Business Logic | Manual | Automated Backend API (Java/Spring) | High |
| 3 | Data Persistence | Paper forms | Centralized PostgreSQL Database | High |
| 4 | Authentication | Physical access | OIDC/JWT via Keycloak | High |
| 5 | Infrastructure | Non-existent | Kubernetes on Cloud | High |
| 6 | System Integration | Manual check | Real-time API call to Inventory System | Medium |
| 7 | Monitoring | Human observation | Prometheus/Grafana/ELK Stack | Medium |

---

## 2. Solution Building Blocks

### 2.1 Application Building Blocks (ABB)

| ID | Building Block | Description | Gap Addressed | Build/Buy |
|---|---|---|---|---|
| ABB-01 | Authentication UI | Login/logout screens and token handling. | Gap #1, #4 | Build |
| ABB-02 | Production Dashboard UI | UI for creating and tracking personal orders. | Gap #1 | Build |
| ABB-03 | Warehouse Dashboard UI | UI for viewing and processing open orders. | Gap #1 | Build |
| ABB-04 | Admin Dashboard UI | UI for managing users and all orders. | Gap #1 | Build |
| ABB-05 | User & Auth API | Backend service for user management and auth. | Gap #2, #4 | Build |
| ABB-06 | Order Management API | Core backend service for order lifecycle. | Gap #2 | Build |

### 2.2 Data Building Blocks (DBB)

| ID | Building Block | Description | Gap Addressed | Build/Buy |
|---|---|---|---|---|
| DBB-01 | Shop Floor DB Schema | All tables, indexes, and constraints. | Gap #3 | Build |
| DBB-02 | DB Migration Scripts | Flyway scripts to manage schema evolution. | Gap #3 | Build |

### 2.3 Technology Building Blocks (TBB)

| ID | Building Block | Description | Gap Addressed | Build/Buy |
|---|---|---|---|---|
| TBB-01 | Kubernetes Cluster | Managed K8s cluster (e.g., EKS, GKE). | Gap #5 | Buy |
| TBB-02 | CI/CD Pipeline | GitHub Actions workflow for build/test/deploy. | Gap #5 | Build |
| TBB-03 | Monitoring Stack | Prometheus/Grafana/ELK deployment. | Gap #7 | Buy |
| TBB-04 | Identity Provider | Keycloak deployment for OIDC. | Gap #4 | Buy |

### 2.4 Integration Building Blocks (IBB)

| ID | Building Block | Description | Gap Addressed | Build/Buy |
|---|---|---|---|---|
| IBB-01 | Inventory System Adapter | Anti-Corruption Layer to isolate the external inventory API. | Gap #6 | Build |

---

## 3. Build vs. Buy Analysis

### Build Decisions

| Component | Rationale |
|---|---|
| Core Application Logic | The business logic for order management and the specific UI workflows are unique to the shop floor's process and provide competitive value. |
| Inventory System Adapter | The adapter must be custom-built to match the specific, potentially non-standard, interface of the external inventory system. |

### Buy/Reuse Decisions

| Component | Product/Library | Rationale |
|---|---|---|
| Database | PostgreSQL | A mature, feature-rich, and reliable open-source relational database. |
| Container Orchestration | Kubernetes | The industry standard for container orchestration, providing scalability and resilience. |
| Identity Provider | Keycloak | A powerful and flexible open-source IAM solution that saves significant development effort. |
| Monitoring Stack | Prometheus, Grafana, ELK | A standard, powerful, and widely-supported open-source stack for observability. |

---

## 4. Work Packages

### Work Package Definition

| WP ID | Name | Building Blocks | Dependencies | Estimated Effort |
|---|---|---|---|---|
| WP-01 | Foundational Setup | TBB-01, TBB-02, TBB-03, TBB-04, DBB-02 | None | 3 weeks |
| WP-02 | Core Backend Services | ABB-05, ABB-06, DBB-01 | WP-01 | 4 weeks |
| WP-03 | Frontend Implementation | ABB-01, ABB-02, ABB-03 | WP-02 | 4 weeks |
| WP-04 | Integration & Admin | IBB-01, ABB-04 | WP-03 | 2 weeks |

### Work Package Sequencing

```
WP-01 (Foundation) 
    ↓
WP-02 (Core Backend Services)
    ↓
WP-03 (Frontend Implementation)
    ↓
WP-04 (Integration & Admin)
```
