# Proposal: Shop Floor Material Supply System Frontend UI

**Ref:** Depends on `@openspec/changes/01-data-model`, `@openspec/changes/02-backend-api`

## 1. View Architecture

### Production Dashboard

- **Purpose**: Allows Production Line Users to create new material requests and view the status of their existing requests.
- **User Roles**: Production Line User
- **Layout**: A table displaying the user's orders and a form to create a new order.

### Warehouse Dashboard

- **Purpose**: Allows Warehouse Users to view and manage open material requests.
- **User Roles**: Warehouse User
- **Layout**: A table displaying all `NEW` and `IN_PROGRESS` orders, with actions to update the status.

### Admin Dashboard

- **Purpose**: Allows Administrators to view and manage all users and orders.
- **User Roles**: Administrator
- **Layout**: Tables for managing users and orders, with full CRUD capabilities.

## 2. Component Requirements

| Component | Purpose | Data Source |
|---|---|---|
| OrderTable | Displays a list of orders. | GET /api/orders |
| CreateOrderForm | A form to create a new order. | POST /api/orders |
| UserTable | Displays a list of users. | GET /api/users (to be created) |

## 3. User Interactions

### Create Order

- **Trigger**: Production Line User fills out and submits the "Create Order" form.
- **Action**: A `POST` request is sent to `/api/orders`.
- **Feedback**: The order table is updated with the new order.

### Update Order Status

- **Trigger**: Warehouse User clicks a button to change the status of an order.
- **Action**: A `PATCH` request is sent to `/api/orders/{id}/status`.
- **Feedback**: The order's status is updated in the table.

## 4. Role-based Visibility

| Element | Production Line User | Warehouse User | Administrator |
|---|---|---|---|
| Production Dashboard | ✓ | ✗ | ✓ |
| Warehouse Dashboard | ✗ | ✓ | ✓ |
| Admin Dashboard | ✗ | ✗ | ✓ |
| Create Order Form | ✓ | ✗ | ✓ |
| Update Status Buttons | ✗ | ✓ | ✓ |
| Delete Order Button | ✗ | ✗ | ✓ |

## 5. API Integration

| Component | Endpoint | Method | Purpose |
|---|---|---|---|
| OrderTable | /api/orders | GET | Fetch orders to display. |
| CreateOrderForm | /api/orders | POST | Create a new order. |
| OrderTable | /api/orders/{id}/status | PATCH | Update an order's status. |
| AdminDashboard | /api/orders/{id} | DELETE | Delete an order. |
