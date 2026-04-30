# Proposal: Shop Floor Material Supply System Backend API

**Ref:** Depends on `@openspec/changes/01-data-model`

## 1. Service Layer

### OrderService

Manages the lifecycle of delivery orders, enforcing business rules and state transitions.

**Methods:**
- `createOrder(partNumber, quantity)`: Creates a new order, validating against stock.
- `getOrdersForUser(user)`: Retrieves orders based on the user's role.
- `updateOrderStatus(orderId, newStatus, user)`: Updates the status of an order, enforcing state transition rules.
- `deleteOrder(orderId, user)`: Deletes an order (for administrators).

### UserService

Manages user authentication and role-based access.

**Methods:**
- `findUserByUsername(username)`: Retrieves a user by their username.

## 2. Business Rules

1. **State Transition**:
   - When: A user attempts to change an order's status.
   - Then: The `OrderService` validates the transition against the allowed state machine (NEW -> IN_PROGRESS -> IN_TRANSIT -> COMPLETED).
2. **RBAC**:
   - When: A user accesses an API endpoint.
   - Then: The system checks if the user's role has the required permissions.
3. **Stock Check**:
   - When: A Production Line User creates a new order.
   - Then: The system checks the external inventory system to ensure sufficient stock is available.

## 3. API Endpoints

| Method | Path | Description | Auth Required |
|---|---|---|---|
| POST | /api/orders | Creates a new delivery order. | Production Line User |
| GET | /api/orders | Retrieves a list of delivery orders. | Any authenticated user |
| PATCH | /api/orders/{id}/status | Updates the status of a delivery order. | Warehouse User, Production Line User, Administrator |
| DELETE | /api/orders/{id} | Deletes a delivery order. | Administrator |

## 4. Error Handling

- `400 Bad Request`: The request is malformed.
- `403 Forbidden`: The user is not authorized to perform the action.
- `404 Not Found`: The requested resource does not exist.
- `422 Unprocessable Entity`: A business rule was violated (e.g., insufficient stock).
