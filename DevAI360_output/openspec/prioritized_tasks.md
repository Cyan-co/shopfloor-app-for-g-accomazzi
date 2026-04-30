# Prioritized Implementation Tasks

## Phase 1: Foundation (P1)

1.  **Data Model (Backend):**
    - [ ] Create `User`, `Part`, `Order` entity classes.
    - [ ] Create `OrderStatus`, `Role` enums.
    - [ ] Create `UserRepository`, `PartRepository`, `OrderRepository`.
    - [ ] Create Flyway migration script for `users`, `parts`, `orders` tables.
2.  **Data Model (Frontend):**
    - [ ] Create `user.model.ts`, `part.model.ts`, `order.model.ts`.
    - [ ] Create `order-status.enum.ts`, `role.enum.ts`.

## Phase 2: Core Backend (P1)

3.  **Backend API (Service Layer):**
    - [ ] Create `OrderService` and `UserService`.
    - [ ] Implement all methods for `OrderService` and `UserService`.
4.  **Backend API (Controller Layer):**
    - [ ] Create `OrderController`.
    - [ ] Implement all endpoints for `OrderController`.
    - [ ] Create DTOs.
5.  **Security:**
    - [ ] Configure Spring Security with Keycloak.
    - [ ] Implement RBAC on service methods.
6.  **Error Handling:**
    - [ ] Create custom exception classes.
    - [ ] Implement a global exception handler.

## Phase 3: Frontend UI (P2)

7.  **Frontend UI (Services):**
    - [ ] Create `OrderService`, `AuthService`, `UserService`.
8.  **Frontend UI (Components):**
    - [ ] Create `OrderTableComponent`, `CreateOrderFormComponent`, `UserTableComponent`.
9.  **Frontend UI (Views/Pages):**
    - [ ] Create `ProductionDashboardComponent`, `WarehouseDashboardComponent`, `AdminDashboardComponent`.
    - [ ] Configure routing.

## Phase 4: Integration & Styling (P3)

10. **Styling:**
    - [ ] Implement main layout with Tailwind CSS.
    - [ ] Ensure components are responsive.
11. **Integration:**
    - [ ] Configure Angular proxy.
    - [ ] Implement UI error handling.
