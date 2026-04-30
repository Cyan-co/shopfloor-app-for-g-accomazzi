# Tasks: Shop Floor Material Supply System Backend API Implementation

**Paths:** All backend code under `backend/`.

## Service Layer

- [ ] Create `OrderService` class in `com.bosch.shopfloor.service`
- [ ] Implement `createOrder` method with stock validation
- [ ] Implement `getOrdersForUser` method with role-based filtering
- [ ] Implement `updateOrderStatus` method with state transition logic
- [ ] Implement `deleteOrder` method
- [ ] Create `UserService` class in `com.bosch.shopfloor.service`
- [ ] Implement `findUserByUsername` method

## Controller Layer

- [ ] Create `OrderController` class in `com.bosch.shopfloor.controller`
- [ ] Implement `POST /api/orders` endpoint
- [ ] Implement `GET /api/orders` endpoint
- [ ] Implement `PATCH /api/orders/{id}/status` endpoint
- [ ] Implement `DELETE /api/orders/{id}` endpoint
- [ ] Create DTOs for request and response objects

## Security

- [ ] Configure Spring Security with Keycloak integration
- [ ] Implement RBAC checks on service methods using `@PreAuthorize`

## Error Handling

- [ ] Create custom exception classes for business rule violations
- [ ] Implement a global exception handler to return appropriate HTTP status codes
