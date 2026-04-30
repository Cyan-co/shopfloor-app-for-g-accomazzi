# Proposal: Shop Floor Material Supply System Data Model

## 1. Entities Overview

- **User**: Represents an authenticated user of the system with a specific role.
- **Part**: Represents a catalog item that can be requested.
- **Order**: Represents a single material request made by a Production Line User.

## 2. User Schema

- **Table Name**: `users`
- **Description**: Represents an authenticated user of the system.
- **Fields**:
    - `UUID id`: Primary key
    - `String username`: User's unique login name
    - `String password`: User's hashed password
    - `String role`: Role (Production, Warehouse, Admin)
    - `TIMESTAMP created_at`: Creation timestamp
    - `TIMESTAMP updated_at`: Last update timestamp

## 3. Part Schema

- **Table Name**: `parts`
- **Description**: Represents a catalog item that can be requested.
- **Fields**:
    - `UUID id`: Primary key
    - `String part_number`: Unique identifier for the part
    - `String name`: Name of the part
    - `String description`: Description of the part
    - `Integer stock`: Available quantity
    - `TIMESTAMP created_at`: Creation timestamp
    - `TIMESTAMP updated_at`: Last update timestamp

## 4. Order Schema

- **Table Name**: `orders`
- **Description**: Represents a single material request.
- **Fields**:
    - `UUID id`: Primary key
    - `UUID user_id`: FK to the User who created the order
    - `UUID part_id`: FK to the Part being requested
    - `Integer quantity`: The amount of material requested
    - `String status`: Current status (New, In Progress, In Transit, Completed)
    - `Boolean is_deleted`: Flag for soft delete
    - `TIMESTAMP created_at`: Creation timestamp
    - `TIMESTAMP updated_at`: Last update timestamp

## 5. Relationships

- A User can have many Orders.
- A Part can be in many Orders.

## 6. Status/Lifecycle (Order)

- `NEW`: The order has been created by a Production Line User.
- `IN_PROGRESS`: A Warehouse User has started processing the order.
- `IN_TRANSIT`: The material is on its way to the production line.
- `COMPLETED`: The Production Line User has confirmed receipt of the material.
