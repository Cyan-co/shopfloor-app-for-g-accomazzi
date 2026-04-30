# Phase C: Application Architecture (AA)

## 1. Component Overview

### Backend (Spring Boot 3.x)

- **Root Package**: `com.bosch.shopfloor`
- **Architecture Pattern**: Controller-Service-Repository

**Naming Conventions**:
- **Controllers**: `OrderController.java`, `UserController.java`
- **Services**: `OrderService.java`, `UserService.java`, `AuthorizationService.java`

**Rules**:
- All business logic, including state transition validation, MUST be encapsulated within the **Service** layer.
- Role-Based Access Control (RBAC) checks MUST be enforced in the **Service** layer before any business logic is executed.

---

### Frontend Standards (Angular 17+)

- **App Prefix**: `app-shopfloor`
- **Styling**: Tailwind CSS

**Naming Conventions**:
- **Components**: `production-dashboard.component.ts`, `warehouse-dashboard.component.ts`, `admin-dashboard.component.ts`
- **Services**: `order-api.service.ts`, `auth-api.service.ts`

**Rules**:
- The UI MUST dynamically show/hide features based on the user's role and permissions.
- The frontend MUST NOT contain any business logic or security rules; it is purely for presentation and user interaction.

---

## 2. Integration Contract

- **API Style**: RESTful
- **Base Path**: `/api`
- **Format**: JSON over HTTP
- **Status Codes**:
  - `200 OK`: Successful request.
  - `201 Created`: Successful creation of a resource (e.g., new order).
  - `400 Bad Request`: The request is malformed (e.g., missing fields).
  - `403 Forbidden`: The user is not authorized to perform the action.
  - `404 Not Found`: The requested resource does not exist.
  - `422 Unprocessable Entity`: The request is valid, but a business rule was violated (e.g., insufficient stock).

---

## 3. Endpoints (Derived from Business Architecture)

### `POST /api/orders`
- **Description**: Creates a new delivery order.
- **Permitted Role**: `Production Line User`
- **Request Body**: `{ "partNumber": "string", "quantity": integer }`
- **Success Response**: `201 Created` with the new order object.
- **Business Rule**: Fails with `422` if stock is insufficient.

### `GET /api/orders`
- **Description**: Retrieves a list of delivery orders.
- **Permitted Roles**: `Production Line User`, `Warehouse User`, `Administrator`
- **Behavior**: The `OrderService` will filter the returned orders based on the caller's role:
  - `Production Line User`: Returns only orders created by them.
  - `Warehouse User`: Returns orders with status `NEW`, `IN_PROGRESS`, `IN_TRANSIT`.
  - `Administrator`: Returns all orders.
- **Success Response**: `200 OK` with an array of order objects.

### `PATCH /api/orders/{id}/status`
- **Description**: Updates the status of a delivery order, driving the process flow.
- **Permitted Roles**: `Warehouse User`, `Production Line User`, `Administrator`
- **Request Body**: `{ "status": "string" }` (e.g., `"IN_PROGRESS"`)
- **Behavior**: The `OrderService` enforces the state machine defined in Phase B:
  - A `Warehouse User` can transition `NEW` -> `IN_PROGRESS` and `IN_PROGRESS` -> `IN_TRANSIT`.
  - A `Production Line User` can transition `IN_TRANSIT` -> `COMPLETED` (only on their own order).
  - An `Administrator` can set any status at any time.
- **Success Response**: `200 OK` with the updated order object.
- **Business Rule**: Fails with `403` if the role is not permitted to perform the transition.

### `DELETE /api/orders/{id}`
- **Description**: Deletes a delivery order.
- **Permitted Role**: `Administrator`
- **Behavior**: This provides the escape hatch for handling errors as defined in the business rules.
- **Success Response**: `204 No Content`.
- **Business Rule**: Fails with `403` if the user is not an Administrator.

---

## 4. Application Pattern

- **Architecture**: A classic decoupled client-server architecture. The frontend is a Single-Page Application (SPA) that interacts with a stateless backend REST API.
- **Backend**: A Spring Boot application providing the API.
  - **Java package**: `com.bosch.shopfloor`
- **Frontend**: An Angular application providing the user interface.
  - **Angular prefix**: `app`

---

## 5. Interface Contract (API)

- **Base Path**: `/api`
- **Format**: All data is exchanged in JSON format.
- **Error Handling**: The API MUST use standard HTTP status codes to communicate the outcome of a request. Business rule violations (e.g., attempting a forbidden state transition) will be reported with appropriate `4xx` status codes and a descriptive JSON error body.

---

## 6. Security Implementation

- **Enforcement Point**: RBAC is strictly enforced on the backend within the **Service layer**. User roles and permissions are checked before any business logic is executed.
- **Role Definitions**: The roles (`Production Line User`, `Warehouse User`, `Administrator`) are identical to those defined in the Phase B Business Architecture.
- **Frontend Trust**: The frontend is considered an untrusted client. All security decisions are made by the backend. The frontend will receive the user's role upon login and adapt the UI accordingly, but this is for usability, not security.
