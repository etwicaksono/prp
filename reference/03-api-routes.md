# API Routes Documentation

## Base URL Structure
All API routes are versioned and located under `/api/v1/`

## Authentication Routes

### POST `/api/v1/auth/register`
**Purpose**: Create new user account

**Request Body**:
```typescript
{
  name: string;
  email: string;
  username: string;
  password: string;
}
```

**Response**: 
```typescript
{
  success: boolean;
  message: string;
  data?: { user: UserData };
}
```

**Schema**: `schemas/auth/register.schema.ts`

---

### POST `/api/v1/auth/login`
**Purpose**: Authenticate user and return JWT tokens

**Request Body**:
```typescript
{
  username: string;
  password: string;
}
```

**Response**:
```typescript
{
  success: boolean;
  data: {
    access_token: string;
    refresh_token: string;
    user: UserData;
  };
}
```

**Schema**: `schemas/auth/login.schema.ts`

---

### POST `/api/v1/auth/refresh`
**Purpose**: Refresh access token using refresh token

**Headers**: 
- `Authorization: Bearer <refresh_token>`

**Response**:
```typescript
{
  access_token: string;
}
```

**Schema**: `schemas/auth/refresh.schema.ts`

---

### POST `/api/v1/auth/logout`
**Purpose**: Invalidate user session

**Headers**:
- `Authorization: Bearer <access_token>`

**Response**:
```typescript
{
  success: boolean;
  message: string;
}
```

**Schema**: `schemas/auth/logout.schema.ts`

---

## Account Routes

### GET `/api/v1/accounts`
**Purpose**: Fetch all accounts for authenticated user

**Query Parameters**:
- `active` (optional): boolean - Filter by active status
- `group_id` (optional): string - Filter by group

**Response**:
```typescript
{
  success: boolean;
  data: Account[];
}
```

**Schema**: `schemas/accounts/account.schema.ts`

---

### POST `/api/v1/accounts`
**Purpose**: Create new account

**Request Body**:
```typescript
{
  personal_id: number;
  name: string;
  icon: string;
  account_type: string;
  usability: string;
  color: string;
  initial_amount?: number;
  group_id?: string;
  active?: boolean;
}
```

**Schema**: `schemas/accounts/account.schema.ts`

---

### GET `/api/v1/accounts/[id]`
**Purpose**: Fetch single account by ID

**Response**:
```typescript
{
  success: boolean;
  data: Account;
}
```

---

### PUT `/api/v1/accounts/[id]`
**Purpose**: Update account

**Request Body**: Partial<Account>

---

### DELETE `/api/v1/accounts/[id]`
**Purpose**: Delete account

**Response**:
```typescript
{
  success: boolean;
  message: string;
}
```

---

### PUT `/api/v1/accounts/swap-order`
**Purpose**: Reorder accounts (drag & drop)

**Request Body**:
```typescript
{
  order_map: Array<{
    id: string;           // Account UUID
    personal_id: number;  // New position (1, 2, 3...)
  }>;
}
```

**Response**:
```typescript
{
  success: true;
  message: string;
  data: {
    updated_count: number;
  };
}
```

**Implementation**:
- Uses two-phase transaction to avoid unique constraint violations
- Only account owner can reorder their accounts

---

## Category Routes

### GET `/api/v1/categories`
**Purpose**: Fetch all categories for authenticated user

**Query Parameters**:
- `nature` (optional): 'INCOME' | 'EXPENSE'
- `is_active` (optional): boolean
- `parent_id` (optional): string
- `keyword` (optional): string - Search by name

**Response**:
```typescript
{
  success: boolean;
  data: Category[];
}
```

**Schema**: `schemas/categories/category.schema.ts`

---

### GET `/api/v1/categories/tree`
**Purpose**: Fetch hierarchical category tree

**Response**:
```typescript
{
  success: boolean;
  data: CategoryTree; // Nested structure
}
```

---

### POST `/api/v1/categories`
**Purpose**: Create new category

**Request Body**:
```typescript
{
  personal_id: number;
  name: string;
  icon: string;
  nature: 'INCOME' | 'EXPENSE';
  parent_id?: string;
  color?: string;
  is_active: boolean;
}
```

---

### PUT `/api/v1/categories/[id]`
**Purpose**: Update category

**Request Body**: Partial<Category>

---

### DELETE `/api/v1/categories/[id]`
**Purpose**: Delete category

**Response**:
```typescript
{
  success: boolean;
  message: string;
}
```

---

### POST `/api/v1/categories/swap-order`
**Purpose**: Reorder categories

**Request Body**:
```typescript
{
  category_ids: string[];
}
```

---

## Transaction Routes

### GET `/api/v1/transactions`
**Purpose**: Fetch transactions with filtering and pagination

**Query Parameters**:
- `page` (optional): number - Page number (default: 1)
- `limit` (optional): number - Items per page (default: 50)
- `account_id` (optional): string
- `category_id` (optional): string
- `type` (optional): 'INCOME' | 'EXPENSE'
- `start_date` (optional): string (ISO date)
- `end_date` (optional): string (ISO date)
- `min_amount` (optional): number
- `max_amount` (optional): number
- `keyword` (optional): string - Search in description
- `label_ids` (optional): string - Comma-separated label IDs
- `payment_type` (optional): string
- `payment_status` (optional): string
- `sort_by` (optional): 'date' | 'amount' (default: 'date')
- `sort_order` (optional): 'asc' | 'desc' (default: 'desc')

**Response**:
```typescript
{
  success: boolean;
  data: {
    transactions: Transaction[];
    pagination: {
      page: number;
      limit: number;
      total: number;
      totalPages: number;
    };
  };
}
```

**Schema**: `schemas/transactions/transaction.schema.ts`

---

### GET `/api/v1/transactions/summary`
**Purpose**: Get transaction summary statistics

**Query Parameters**: Same date filters as GET /transactions

**Response**:
```typescript
{
  success: boolean;
  data: {
    total_income: number;
    total_expense: number;
    net_amount: number;
    transaction_count: number;
    by_category: Array<{
      category_id: string;
      category_name: string;
      total: number;
    }>;
  };
}
```

---

### POST `/api/v1/transactions`
**Purpose**: Create new transaction

**Request Body**:
```typescript
{
  personal_id: number;
  date: Date;
  account_id: string;
  category_id: string;
  amount: number; // Negative for expense, positive for income
  type: 'INCOME' | 'EXPENSE';
  description?: string;
  currency?: string;
  payer?: string;
  payment_type?: 'Cash' | 'Credit Card' | 'Bank Transfer' | 'Digital Wallet';
  payment_status?: 'Cleared' | 'Pending' | 'Scheduled';
  label_ids?: string[];
}
```

**Note**: Amount should be negative for expenses, positive for income

---

### GET `/api/v1/transactions/[id]`
**Purpose**: Fetch single transaction

**Response**:
```typescript
{
  success: boolean;
  data: Transaction;
}
```

---

### PUT `/api/v1/transactions/[id]`
**Purpose**: Update transaction

**Request Body**: Partial<Transaction>

---

### DELETE `/api/v1/transactions/[id]`
**Purpose**: Delete transaction

**Response**:
```typescript
{
  success: boolean;
  message: string;
}
```

---

## Transfer Routes

### GET `/api/v1/transfers`
**Purpose**: Fetch all transfers

**Query Parameters**:
- `start_date` (optional): string
- `end_date` (optional): string
- `from_account` (optional): string
- `to_account` (optional): string

**Response**:
```typescript
{
  success: boolean;
  data: Transfer[];
}
```

**Schema**: `schemas/transfers/transfer.schema.ts`

---

### POST `/api/v1/transfers`
**Purpose**: Create money transfer between accounts

**Request Body**:
```typescript
{
  personal_id: number;
  date: Date | string;
  from_account_id: string;
  to_account_id: string;
  amount: number;
  note: string;
  to_amount?: number; // For currency conversion
}
```

**Note**: Creates two transactions (one withdrawal, one deposit)

---

### GET `/api/v1/transfers/[id]`
**Purpose**: Fetch single transfer

---

### PUT `/api/v1/transfers/[id]`
**Purpose**: Update transfer

---

### DELETE `/api/v1/transfers/[id]`
**Purpose**: Delete transfer (also deletes associated transactions)

---

## Label Routes

### GET `/api/v1/labels`
**Purpose**: Fetch all labels

**Response**:
```typescript
{
  success: boolean;
  data: Label[];
}
```

**Schema**: `schemas/labels/label.schema.ts`

---

### POST `/api/v1/labels`
**Purpose**: Create new label

**Request Body**:
```typescript
{
  personal_id: number;
  name: string;
  color: string; // Hex color
}
```

---

### PUT `/api/v1/labels/[id]`
**Purpose**: Update label

---

### DELETE `/api/v1/labels/[id]`
**Purpose**: Delete label (cascades to labels_map)

---

## Group Routes

### GET `/api/v1/groups`
**Purpose**: Fetch all account groups

**Response**:
```typescript
{
  success: boolean;
  data: Group[];
}
```

**Schema**: `schemas/groups/group.schema.ts`

---

### POST `/api/v1/groups`
**Purpose**: Create new group

**Request Body**:
```typescript
{
  personal_id: number;
  name: string;
}
```

---

### PUT `/api/v1/groups/[id]`
**Purpose**: Update group

---

### DELETE `/api/v1/groups/[id]`
**Purpose**: Delete group

---

## Debt Routes

### GET `/api/v1/debts`
**Purpose**: Fetch all debts/loans

**Response**:
```typescript
{
  success: boolean;
  data: Debt[];
}
```

**Schema**: `schemas/debts/debt.schema.ts`

---

### POST `/api/v1/debts`
**Purpose**: Create new debt/loan record

**Request Body**:
```typescript
{
  personal_id: number;
  account_id: string;
  name: string;
  type: 'DEBT' | 'LOAN';
}
```

---

### PUT `/api/v1/debts/[id]`
**Purpose**: Update debt

---

### DELETE `/api/v1/debts/[id]`
**Purpose**: Delete debt

---

## OpenAPI Documentation

### GET `/api/openapi/[...openapi]`
**Purpose**: Auto-generated API documentation

**URL**: `/api-docs`

**Tool**: Scalar OpenAPI viewer

---

## Common Patterns

### Authentication
All routes except auth routes require:
```
Authorization: Bearer <access_token>
```

### Error Responses
```typescript
{
  success: false;
  error: string;
  details?: ValidationError[];
}
```

### Validation
- All requests validated with Zod schemas
- Type-safe with TypeScript
- Located in `schemas/` directory

### Pagination
Standard pagination response:
```typescript
{
  page: number;
  limit: number;
  total: number;
  totalPages: number;
}
```

### Multi-tenancy
All queries automatically filtered by authenticated `user_id` from JWT token

### Personal ID Management

**Endpoint**: `GET /api/v1/personal-ids/max`
**Response**:
```typescript
{
  transactions: 123,
  accounts: 45,
  categories: 67,
  labels: 12,
  groups: 8,
  // ... other tables
}
```

### Personal ID Conflicts
- API returns 409 Conflict if personal_id already exists
- Frontend fetches fresh max values from `/api/v1/personal-ids/max`
- Implements retry logic with incremented personal_id
- Max 3 retry attempts

## Implementation Details

### Request Flow
```
Request → API Route → Validation (Zod) → Auth Check (JWT) → Prisma Query → Response
```

### File Location
All route handlers: `app/api/v1/<resource>/route.ts`

### Supported HTTP Methods
- GET: Fetch resources
- POST: Create resources
- PUT: Update resources (full)
- DELETE: Remove resources

### Response Format
Consistent across all endpoints:
```typescript
{
  success: boolean;
  data?: T;
  error?: string;
  message?: string;
}
```
