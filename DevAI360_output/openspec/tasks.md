# Implementation Tasks

## Data Model

### Backend
- [ ] Create `User` entity class in `com.bosch.shopfloor.model`
- [ ] Create `Part` entity class in `com.bosch.shopfloor.model`
- [ ] Create `Order` entity class in `com.bosch.shopfloor.model`
- [ ] Create `OrderStatus` enum in `com.bosch.shopfloor.model`
- [ ] Create `Role` enum in `com.bosch.shopfloor.model`
- [ ] Create `UserRepository` interface in `com.bosch.shopfloor.repository`
- [ ] Create `PartRepository` interface in `com.bosch.shopfloor.repository`
- [ ] Create `OrderRepository` interface in `com.bosch.shopfloor.repository`
- [ ] Create Flyway migration script to create `users`, `parts`, and `orders` tables

### Frontend
- [ ] Create `user.model.ts` in `src/app/models`
- [ ] Create `part.model.ts` in `src/app/models`
- [ ] Create `order.model.ts` in `src/app/models`
- [ ] Create `order-status.enum.ts` in `src/app/models`
- [ ] Create `role.enum.ts` in `src/app/models`

## Backend API

### Service Layer
- [ ] Create `OrderService` class in `com.bosch.shopfloor.service`
- [ ] Implement `createOrder` method with stock validation
- [ ] Implement `getOrdersForUser` method with role-based filtering
- [ ] Implement `updateOrderStatus` method with state transition logic
- [ ] Implement `deleteOrder` method
- [ ] Create `UserService` class in `com.bosch.shopfloor.service`
- [ ] Implement `findUserByUsername` method

### Controller Layer
- [ ] Create `OrderController` class in `com.bosch.shopfloor.controller`
- [ ] Implement `POST /api/orders` endpoint
- [ ] Implement `GET /api/orders` endpoint
- [ ] Implement `PATCH /api/orders/{id}/status` endpoint
- [ ] Implement `DELETE /api/orders/{id}` endpoint
- [ ] Create DTOs for request and response objects

### Security
- [ ] Configure Spring Security with Keycloak integration
- [ ] Implement RBAC checks on service methods using `@PreAuthorize`

### Error Handling
- [ ] Create custom exception classes for business rule violations
- [ ] Implement a global exception handler to return appropriate HTTP status codes

## Frontend UI

### Views/Pages
- [ ] Create `ProductionDashboardComponent`
- [ ] Create `WarehouseDashboardComponent`
- [ ] Create `AdminDashboardComponent`
- [ ] Configure routing for the dashboards based on user role

### Components
- [ ] Create `OrderTableComponent` to display orders
- [ ] Create `CreateOrderFormComponent` for creating new orders
- [ ] Create `UserTableComponent` for the admin dashboard

### Services
- [ ] Create `OrderService` to interact with the `/api/orders` endpoint
- [ ] Create `AuthService` for user authentication and role management
- [ ] Create `UserService` to interact with the `/api/users` endpoint

### Styling
- [ ] Implement the main application layout using Tailwind CSS
- [ ] Ensure all components are responsive

### Integration
- [ ] Configure a proxy for the Angular development server to communicate with the backend
- [ ] Implement error handling to display user-friendly messages
