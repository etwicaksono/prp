# Database Schema Documentation

## Overview
PostgreSQL database managed by Prisma ORM with 8 main tables for financial data management.

## Entity Relationship Diagram (Conceptual)

```
users (1) ─────┬─── (N) accounts
               ├─── (N) categories
               ├─── (N) transactions
               ├─── (N) transfers
               ├─── (N) labels
               ├─── (N) groups
               └─── (N) debts

groups (1) ─── (N) accounts

accounts (1) ───┬─── (N) transactions (indirect)
                ├─── (N) transfers (from_account)
                ├─── (N) transfers (to_account)
                └─── (N) debts

categories (1) ───┬─── (N) categories (self-referencing)
                  └─── (N) transactions (indirect via category_id)

debts (1) ─── (N) transactions

transfers (1) ─── (N) transactions

labels (1) ─── (N) labels_map
transactions (1) ─── (N) labels_map
```

## Tables

### 1. users
**Purpose**: Core user authentication and identification

| Column      | Type         | Constraints           | Description                    |
|-------------|--------------|----------------------|--------------------------------|
| id          | VARCHAR(36)  | PRIMARY KEY          | UUID for user                  |
| name        | VARCHAR(64)  | NOT NULL             | User's display name            |
| email       | VARCHAR(36)  | NOT NULL             | Email address                  |
| username    | VARCHAR(36)  | NOT NULL             | Unique username for login      |
| password    | VARCHAR(255) | NOT NULL             | Hashed password (bcrypt)       |
| created_at  | TIMESTAMP    | NOT NULL             | Account creation timestamp     |
| created_by  | VARCHAR(64)  | NULL                 | Creator identifier             |
| updated_at  | TIMESTAMP    | NULL                 | Last update timestamp          |
| updated_by  | VARCHAR(64)  | NULL                 | Last updater identifier        |

**Relations**:
- Has many: accounts, categories, transactions, transfers, labels, groups, debts, labels_map

---

### 2. accounts
**Purpose**: Financial accounts (bank accounts, cash, credit cards, etc.)

| Column          | Type         | Constraints           | Description                           |
|-----------------|--------------|----------------------|---------------------------------------|
| id              | VARCHAR(36)  | PRIMARY KEY          | UUID for account                      |
| user_id         | VARCHAR(36)  | FK → users.id        | Owner of the account                  |
| personal_id     | BIGINT       | NOT NULL             | User-specific sequential ID           |
| name            | VARCHAR(36)  | NOT NULL             | Account name                          |
| icon            | VARCHAR(36)  | NOT NULL             | Icon identifier (e.g., 'FaBank')      |
| active          | BOOLEAN      | DEFAULT true         | Whether account is active             |
| usability       | VARCHAR(32)  | NOT NULL             | Usage type (e.g., 'daily', 'savings') |
| account_type    | VARCHAR(32)  | NOT NULL             | Type (e.g., 'bank', 'cash', 'card')   |
| color           | VARCHAR(255) | NOT NULL             | Hex color for UI                      |
| initial_amount  | FLOAT        | NULL                 | Starting balance                      |
| group_id        | VARCHAR(36)  | FK → groups.id       | Optional grouping                     |
| position        | JSON         | NULL                 | Google sheet position, we'll implement it later             |
| created_at      | TIMESTAMP    | NOT NULL             |                                       |
| created_by      | VARCHAR(64)  | NULL                 |                                       |
| updated_at      | TIMESTAMP    | NULL                 |                                       |
| updated_by      | VARCHAR(64)  | NULL                 |                                       |

**Unique Constraints**:
- `(user_id, personal_id)` - Each user has their own personal_id sequence

**Relations**:
- Belongs to: users, groups
- Has many: debts, transfers (as from_account and to_account)

---

### 3. categories
**Purpose**: Income and expense categories (hierarchical structure)

| Column      | Type         | Constraints           | Description                        |
|-------------|--------------|----------------------|------------------------------------|
| id          | VARCHAR(36)  | PRIMARY KEY          | UUID for category                  |
| personal_id | BIGINT       | NOT NULL             | User-specific sequential ID        |
| user_id     | VARCHAR(36)  | FK → users.id        | Owner of the category              |
| parent_id   | VARCHAR(36)  | FK → categories.id   | Parent category (NULL if root)     |
| name        | VARCHAR(36)  | NOT NULL             | Category name                      |
| icon        | VARCHAR(36)  | NOT NULL             | Icon identifier                    |
| nature      | VARCHAR(8)   | NOT NULL             | 'WANT', 'NEED' or 'MUST'              |
| is_active   | BOOLEAN      | NOT NULL             | Whether category is active         |
| position    | JSON         | NULL                 | Google sheet position, we'll implement it later          |
| color       | VARCHAR(36)  | NULL                 | Hex color (inherited from parent)  |
| created_at  | TIMESTAMP    | NOT NULL             |                                    |
| created_by  | VARCHAR(64)  | NULL                 |                                    |
| updated_at  | TIMESTAMP    | NULL                 |                                    |
| updated_by  | VARCHAR(64)  | NULL                 |                                    |

**Unique Constraints**:
- `(personal_id, user_id)` - Each user has their own category numbering

**Relations**:
- Belongs to: users, categories (self-referencing parent)
- Has many: categories (children)

**Note**: Hierarchical structure allows parent categories (e.g., "Food") with child categories (e.g., "Groceries", "Restaurants")

---

### 4. transactions
**Purpose**: Core financial transactions (income and expenses)

| Column         | Type         | Constraints              | Description                        |
|----------------|--------------|-------------------------|------------------------------------|
| id             | VARCHAR(36)  | PRIMARY KEY             | UUID for transaction               |
| user_id        | VARCHAR(36)  | FK → users.id           | Owner of transaction               |
| personal_id    | BIGINT       | NOT NULL                | User-specific sequential ID        |
| date           | TIMESTAMP    | NOT NULL                | Transaction date                   |
| account_id     | VARCHAR(36)  | NOT NULL                | Related account ID                 |
| category_id    | VARCHAR(36)  | NOT NULL                | Related category ID                |
| amount         | FLOAT        | NOT NULL                | Transaction amount (- for expense) |
| type           | VARCHAR(16)  | NOT NULL                | 'INCOME' or 'EXPENSE'              |
| description    | TEXT         | NULL                    | Transaction notes/description      |
| currency       | VARCHAR(3)   | NULL, DEFAULT 'IDR'     | Currency code (ISO 4217)           |
| payer          | VARCHAR(128) | NULL                    | Who paid (for expense tracking)    |
| payment_type   | VARCHAR(32)  | NULL                    | Cash, Credit Card, Bank Transfer   |
| payment_status | VARCHAR(32)  | NULL                    | Cleared, Pending, Scheduled        |
| position       | JSON         | NULL                    | Google sheet position, we'll implement it later          |
| transfer_id    | VARCHAR(36)  | FK → transfers.id       | If part of transfer                |
| debt_id        | VARCHAR(36)  | FK → debts.id           | If related to debt                 |
| created_at     | TIMESTAMP    | NOT NULL                |                                    |
| created_by     | VARCHAR(64)  | NULL                    |                                    |
| updated_at     | TIMESTAMP    | NULL                    |                                    |
| updated_by     | VARCHAR(64)  | NULL                    |                                    |

**Unique Constraints**:
- `(user_id, personal_id)` - Each user has their own transaction numbering

**Relations**:
- Belongs to: users, transfers, debts
- Has many: labels_map (many-to-many with labels)

**Amount Convention**:
- Negative values = Expenses
- Positive values = Income

---

### 5. transfers
**Purpose**: Money transfers between accounts

| Column       | Type         | Constraints                     | Description                    |
|--------------|--------------|--------------------------------|--------------------------------|
| id           | VARCHAR(36)  | PRIMARY KEY                    | UUID for transfer              |
| user_id      | VARCHAR(36)  | FK → users.id                  | Owner of transfer              |
| personal_id  | BIGINT       | NOT NULL                       | User-specific sequential ID    |
| date         | TIMESTAMP    | NOT NULL                       | Transfer date                  |
| from_account | VARCHAR(64)  | FK → accounts.id               | Source account                 |
| to_account   | VARCHAR(64)  | FK → accounts.id               | Destination account            |
| amount       | FLOAT        | NOT NULL                       | Transfer amount                |
| note         | STRING       | NOT NULL                       | Transfer notes                 |
| position     | JSON         | NULL                           | UI position/ordering data      |
| created_at   | TIMESTAMP    | NOT NULL                       |                                |
| created_by   | VARCHAR(64)  | NULL                           |                                |
| updated_at   | TIMESTAMP    | NULL                           |                                |
| updated_by   | VARCHAR(64)  | NULL                           |                                |

**Relations**:
- Belongs to: users, accounts (from_account), accounts (to_account)
- Has many: transactions (creates 2 transactions: one withdrawal, one deposit)

---

### 6. debts
**Purpose**: Track loans and debts

| Column      | Type         | Constraints           | Description                     |
|-------------|--------------|----------------------|---------------------------------|
| id          | VARCHAR(36)  | PRIMARY KEY          | UUID for debt                   |
| user_id     | VARCHAR(36)  | FK → users.id        | Owner                           |
| personal_id | BIGINT       | NOT NULL             | User-specific sequential ID     |
| account_id  | VARCHAR(36)  | FK → accounts.id     | Associated account              |
| name        | VARCHAR(64)  | NOT NULL             | Debt/loan name                  |
| type        | VARCHAR(16)  | NOT NULL             | 'DEBT' or 'LOAN'                |
| position    | JSON         | NULL                 | UI position/ordering data       |
| created_at  | TIMESTAMP    | NOT NULL             |                                 |
| created_by  | VARCHAR(64)  | NULL                 |                                 |
| updated_at  | TIMESTAMP    | NULL                 |                                 |
| updated_by  | VARCHAR(64)  | NULL                 |                                 |

**Relations**:
- Belongs to: users, accounts
- Has many: transactions

---

### 7. groups
**Purpose**: Organize accounts into groups

| Column      | Type         | Constraints           | Description                    |
|-------------|--------------|----------------------|--------------------------------|
| id          | VARCHAR(36)  | PRIMARY KEY          | UUID for group                 |
| user_id     | VARCHAR(36)  | FK → users.id        | Owner                          |
| personal_id | BIGINT       | NOT NULL             | User-specific sequential ID    |
| name        | VARCHAR(64)  | NOT NULL             | Group name                     |
| created_at  | TIMESTAMP    | NOT NULL             |                                |
| created_by  | VARCHAR(64)  | NULL                 |                                |
| updated_at  | TIMESTAMP    | NULL                 |                                |
| updated_by  | VARCHAR(64)  | NULL                 |                                |

**Relations**:
- Belongs to: users
- Has many: accounts

---

### 8. labels
**Purpose**: Tags for categorizing transactions (many-to-many)

| Column      | Type         | Constraints           | Description                    |
|-------------|--------------|----------------------|--------------------------------|
| id          | VARCHAR(36)  | PRIMARY KEY          | UUID for label                 |
| user_id     | VARCHAR(36)  | FK → users.id        | Owner                          |
| personal_id | BIGINT       | NOT NULL             | User-specific sequential ID    |
| name        | VARCHAR(64)  | NOT NULL             | Label name                     |
| color       | VARCHAR(16)  | NOT NULL             | Hex color for UI               |
| created_at  | TIMESTAMP    | NOT NULL             |                                |
| created_by  | VARCHAR(64)  | NULL                 |                                |
| updated_at  | TIMESTAMP    | NULL                 |                                |
| updated_by  | VARCHAR(64)  | NULL                 |                                |

**Unique Constraints**:
- `(user_id, personal_id)` - Each user has their own label numbering

**Relations**:
- Belongs to: users
- Has many: labels_map

**Indexes**:
- `user_id` - For efficient filtering by user

---

### 9. labels_map
**Purpose**: Junction table for many-to-many relationship between transactions and labels

| Column         | Type         | Constraints                | Description                    |
|----------------|--------------|---------------------------|--------------------------------|
| id             | VARCHAR(36)  | PRIMARY KEY               | UUID for mapping               |
| user_id        | VARCHAR(36)  | FK → users.id, CASCADE    | Owner (denormalized)           |
| transaction_id | VARCHAR(36)  | FK → transactions.id      | Transaction being labeled      |
| label_id       | VARCHAR(36)  | FK → labels.id, CASCADE   | Label applied                  |

**Unique Constraints**:
- `(transaction_id, label_id)` - Each transaction-label pair is unique

**Relations**:
- Belongs to: users, transactions, labels
- Cascade delete: When transaction or label is deleted, mapping is removed

**Indexes**:
- `user_id` - For efficient filtering by user
- `transaction_id` - For finding all labels of a transaction
- `label_id` - For finding all transactions with a label

---

## Common Patterns

### Personal ID Pattern
Every user-owned entity has:
- `personal_id`: User-specific sequential numbering (1, 2, 3...)
- Allows "Transaction #5" to mean different things for different users
- Unique constraint on `(user_id, personal_id)`

### Audit Fields
All tables include:
- `created_at`: Timestamp when record was created
- `created_by`: Identifier of creator (nullable)
- `updated_at`: Timestamp of last update (nullable)
- `updated_by`: Identifier of last updater (nullable)

### Soft Deletes
- `active` (accounts): Boolean flag instead of deletion
- `is_active` (categories): Boolean flag instead of deletion

### UI Metadata
- `position`: JSON field for storing UI-specific data (ordering, custom properties)
- `color`: Hex color codes for visual distinction
- `icon`: Icon identifiers for React Icons

## Migrations

Located in `prisma/migrations/`:
1. `202511120000_init/` - Initial schema creation
2. `20251113214258_add_labels_tables/` - Added labels and labels_map tables
3. `20251114212824_delete_transactions_labels_column/` - Removed deprecated labels column

## Prisma Configuration

**Generator**: `prisma-client-js`
**Binary Targets**: `["native", "rhel-openssl-3.0.x"]` (for deployment compatibility)
**Datasource**: PostgreSQL via `DATABASE_URL` environment variable

## Key Considerations for Rewrite

1. **Personal ID Management**: Client-side caching in localStorage, retry logic for conflicts
2. **Cascading Deletes**: Only labels_map has CASCADE; others use RESTRICT to prevent data loss
3. **Foreign Key Constraints**: Strict referential integrity enforced
4. **JSON Fields**: Used for flexible UI data storage (position)
5. **Multi-tenancy**: All queries must filter by `user_id` for data isolation
6. **Transaction Types**: Type field + amount sign convention (negative = expense)
