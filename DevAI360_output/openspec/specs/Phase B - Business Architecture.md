# Phase B: Business Architecture (BA)

## 1. Business Domain

- **Domain**: Shop Floor Material Logistics
- **Process description**: This domain covers the process of supplying materials from a warehouse to a production line. It begins when a production line operator requests a specific part, and ends when the material is delivered and the order is closed. The process is designed to be transparent, with real-time status tracking available to all involved parties.

---

## 2. Business Actors & Roles

- **Production Line User**:
  - **Responsibilities**: Creates material requests (delivery orders) for specific parts and quantities needed on the production line. Confirms the receipt of materials to complete the delivery order.
- **Warehouse User**:
  - **Responsibilities**: Monitors for new material requests. Fulfills requests by picking the required materials and updating the order status as it moves through the fulfillment process (e.g., picking, in-transit).
- **Administrator**:
  - **Responsibilities**: Manages the overall system, including user accounts and roles. Has override capabilities to manage orders at any stage (edit status, delete order) to handle exceptions or correct errors.

---

## 3. Business Process Flow (The Golden Path)

This defines the primary, successful lifecycle of a single "delivery order".

`NEW` -> `IN_PROGRESS` -> `IN_TRANSIT` -> `COMPLETED`

---

## 4. Business Rules

These rules are enforceable constraints on the business process.

- **State Transition Constraints**:
  - A delivery order is always created with the status `NEW`.
  - The transition `NEW` -> `IN_PROGRESS` can only be initiated by a **Warehouse User**.
  - The transition `IN_PROGRESS` -> `IN_TRANSIT` can only be initiated by a **Warehouse User**.
  - The transition `IN_TRANSIT` -> `COMPLETED` can only be initiated by the original requesting **Production Line User**.

- **Role-Based Access Control (RBAC) Rules**:
  - **Production Line User**: Can create `NEW` orders. Can only view orders they have created. Can only perform the final `COMPLETED` transition on their own orders.
  - **Warehouse User**: Can view all orders with status `NEW`, `IN_PROGRESS`, or `IN_TRANSIT`. Cannot create new orders.
  - **Administrator**: Can view, edit the status of, or delete any order at any point in its lifecycle.

- **Immutability & Pre-conditions**:
  - A `NEW` order cannot be created if the requested quantity for a part exceeds the available stock in the inventory system. The system must reject the creation request.
  - Once an order reaches the `COMPLETED` state, it is considered final and cannot be modified by any user except an **Administrator**.

- **Exception Handling**:
  - **Administrator** intervention is the designated mechanism for handling any state that falls outside the Golden Path. This includes, but is not limited to, cancelling an `IN_PROGRESS` order or correcting a mistakenly delivered item.
