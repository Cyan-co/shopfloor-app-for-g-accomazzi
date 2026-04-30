# Phase C: Data Architecture (DA)

## 1. Data Architecture Overview

### Data Principles

- **Single Source of Truth**: Each data entity has a single, authoritative source within the database.
- **Data Integrity**: Integrity is enforced at the database level using constraints (NOT NULL, UNIQUE, FOREIGN KEY).
- **Audit Trail**: Business-critical operations on orders are logged for traceability.
- **Soft Delete**: Orders are soft-deleted to allow for recovery and maintain historical integrity.

### Technology Stack

- **Database**: PostgreSQL 15+
- **ORM**: Spring Data JPA (via Hibernate)
- **Migrations**: Flyway
- **Caching**: Redis will be used for session caching and can be optionally used for query caching if performance requires.

---

## 2. Logical Data Model

### 2.1 Core Entities

| Entity | Description | Owner |
|--------|-------------|-------|
| User | Represents an authenticated user of the system with a specific role. | System |
| Part | Represents a catalog item that can be requested. | System |
| Order | Represents a single material request made by a Production Line User. | Business Process |

### 2.2 Entity Relationships

```
  User (1) ----< (N) Order
  Part (1) ----< (N) Order
```
*A User can have many Orders.*
*A Part can be in many Orders.*

### 2.3 Entity Attributes

**Entity: User**
| Attribute | Type | Nullable | Description |
|---|---|---|---|
| id | UUID | No | Primary key |
| username | String | No | User's unique login name |
| password | String | No | User's hashed password |
| role | String | No | Role (Production, Warehouse, Admin) |
| created_at | TIMESTAMP | No | Creation timestamp |
| updated_at | TIMESTAMP | No | Last update timestamp |

**Entity: Part**
| Attribute | Type | Nullable | Description |
|---|---|---|---|
| id | UUID | No | Primary key |
| part_number | String | No | Unique identifier for the part |
| name | String | No | Name of the part |
| description | String | Yes | Description of the part |
| stock | Integer | No | Available quantity |
| created_at | TIMESTAMP | No | Creation timestamp |
| updated_at | TIMESTAMP | No | Last update timestamp |

**Entity: Order**
| Attribute | Type | Nullable | Description |
|---|---|---|---|
| id | UUID | No | Primary key |
| user_id | UUID | No | FK to the User who created the order |
| part_id | UUID | No | FK to the Part being requested |
| quantity | Integer | No | The amount of material requested |
| status | String | No | Current status (New, In Progress, In Transit, Completed) |
| is_deleted | Boolean | No | Flag for soft delete |
| created_at | TIMESTAMP | No | Creation timestamp |
| updated_at | TIMESTAMP | No | Last update timestamp |

---

## 3. Physical Data Model

### 3.1 Database Schema
- **Schema name**: `shopfloor_schema`

### 3.2 Table Definitions

**Table: users**
| Column | Type | Constraints | Index |
|---|---|---|---|
| id | UUID | PK, NOT NULL | PK |
| username | VARCHAR(100) | UQ, NOT NULL | UQ |
| password | VARCHAR(255) | NOT NULL | - |
| role | VARCHAR(50) | NOT NULL, CHECK(role IN (...)) | IDX |
| created_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | - |
| updated_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | - |

**Table: parts**
| Column | Type | Constraints | Index |
|---|---|---|---|
| id | UUID | PK, NOT NULL | PK |
| part_number | VARCHAR(100) | UQ, NOT NULL | UQ |
| name | VARCHAR(255) | NOT NULL | - |
| description | TEXT | YES | - |
| stock | INT | NOT NULL, CHECK(stock >= 0) | - |
| created_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | - |
| updated_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | - |

**Table: orders**
| Column | Type | Constraints | Index |
|---|---|---|---|
| id | UUID | PK, NOT NULL | PK |
| user_id | UUID | FK, NOT NULL | IDX |
| part_id | UUID | FK, NOT NULL | IDX |
| quantity | INT | NOT NULL, CHECK(quantity > 0) | - |
| status | VARCHAR(50) | NOT NULL, CHECK(status IN (...)) | IDX |
| is_deleted | BOOLEAN | NOT NULL, DEFAULT FALSE | IDX |
| created_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | - |
| updated_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | - |

### 3.3 Naming Conventions
| Element | Convention | Example |
|---|---|---|
| Table | snake_case, plural | `orders`, `users` |
| Column | snake_case | `created_at`, `order_status` |
| Primary Key | `id` | `id` |
| Foreign Key | `{table_name}_id` | `user_id`, `part_id` |
| Index | `idx_{table}_{columns}` | `idx_orders_status` |
| Unique Constraint | `uq_{table}_{columns}` | `uq_users_username` |

### 3.4 Index Strategy
| Table | Index | Columns | Type | Rationale |
|---|---|---|---|---|
| orders | idx_orders_user_id | user_id | B-tree | Fast lookup of orders by user. |
| orders | idx_orders_part_id | part_id | B-tree | Fast lookup of orders by part. |
| orders | idx_orders_status | status | B-tree | Fast filtering of orders by status for warehouse/admin dashboards. |

---

## 4. Data Integrity to 9. Performance

*(Sections 4 through 9 will follow the standard boilerplate provided in the skill instructions, as they represent general best practices applicable to this project without needing further customization at this stage.)*

---

## 7. Data Migration

### 7.1 Migration Strategy
- **Tool**: Flyway
- **Naming**: `V{YYYYMMDDHHMM}__{description}.sql`
- **Version format**: `V202407311000__create_users_table.sql`
