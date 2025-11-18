# Services Layer Documentation

## Overview
Service layer abstracts all API calls with consistent error handling, type safety, and authentication token injection.

**Location**: `src/services/`

---

## Base API Service (`api.ts`)

### Purpose
Core HTTP client with auth token injection and error handling.

### Key Functions

```typescript
const api = {
  get<T>(url: string, config?: AxiosRequestConfig): Promise<T>
  post<T>(url: string, data?: unknown, config?: AxiosRequestConfig): Promise<T>
  put<T>(url: string, data?: unknown, config?: AxiosRequestConfig): Promise<T>
  
  // Personal ID Management
  getMaxPersonalIds(): Promise<Record<string, number>>
}
  delete<T>(url: string, config?: AxiosRequestConfig): Promise<T>
}
```

### Features

**1. Automatic Token Injection**:
```typescript
// Reads from localStorage
const token = await getAuthToken();
headers.Authorization = `Bearer ${token}`;
```

**2. Token Decryption**:
- Uses `tokenCrypto.decryptToken()`
- Handles encryption transparently

**3. Error Handling**:
- Catches network errors
- Extracts error messages from responses
- Throws with user-friendly messages

**4. Base URL**:
- All requests prefixed with `/api/v1/`

**5. Personal ID Management**:
```typescript
// Fetches max personal IDs for all tables
const maxIds = await api.getMaxPersonalIds();
// Returns: { transactions: 123, accounts: 45, ... }
```

---

## 1. authService (`authService.ts`)

### Endpoints

#### `login(username, password)`
```typescript
POST /api/v1/auth/login
Body: { username, password }
Response: { access_token, refresh_token, user }
```

#### `register(userData)`
```typescript
POST /api/v1/auth/register
Body: { name, email, username, password }
Response: { success, message, data }
```

#### `logout()`
```typescript
POST /api/v1/auth/logout
Headers: { Authorization: Bearer <token> }
Response: { success, message }
```

#### `refreshToken()`
```typescript
POST /api/v1/auth/refresh
Headers: { Authorization: Bearer <refresh_token> }
Response: { access_token }
```

---

## 2. accountService (`accountService.ts`)

### Types
```typescript
interface ApiAccountResponse {
  id: string;
  user_id: string;
  personal_id: number;
  name: string;
  icon: string;
  active: boolean;
  usability: string;
  account_type: string;
  color: string;
  initial_amount?: number;
  group_id?: string;
  position?: unknown;
}
```

### Endpoints

#### `fetchAccounts(filters?)`
```typescript
GET /api/v1/accounts?active=true&group_id=...
Response: ApiAccountResponse[]
```

#### `fetchAccountById(id)`
```typescript
GET /api/v1/accounts/{id}
Response: ApiAccountResponse
```

#### `createAccount(data)`
```typescript
POST /api/v1/accounts
Body: {
  personal_id,
  name,
  icon,
  account_type,
  usability,
  color,
  initial_amount?,
  group_id?,
  active?
}
Response: ApiAccountResponse
```

#### `updateAccount(id, data)`
```typescript
PUT /api/v1/accounts/{id}
Body: Partial<ApiAccountResponse>
Response: ApiAccountResponse
```

#### `deleteAccount(id)`
```typescript
DELETE /api/v1/accounts/{id}
Response: { success, message }
```

#### `swapAccountOrder(orderMap)`
```typescript
PUT /api/v1/accounts/swap-order
Body: { 
  order_map: Array<{ 
    id: string,           // Account UUID
    personal_id: number   // New position (1, 2, 3...)
  }> 
}
Response: { 
  success: true, 
  message: "Accounts reordered successfully", 
  data: { updated_count: number } 
}
```

**Implementation Details**:
- Uses two-phase transaction to avoid unique constraint violations
- Phase 1: Sets temporary negative personal_id values  
- Phase 2: Sets final positive personal_id values
- Only account owner can reorder their accounts
- All accounts in order_map must belong to authenticated user

---

## 3. transactionService (`transactionService.ts`)

### Types
```typescript
interface ApiTransactionResponse {
  id: string;
  user_id: string;
  personal_id: number;
  date: string;
  account_id: string;
  category_id: string;
  amount: number;
  type: 'INCOME' | 'EXPENSE';
  description?: string;
  currency?: string;
  payer?: string;
  payment_type?: string;
  payment_status?: string;
  transfer_id?: string;
  debt_id?: string;
  labels?: Array<{ id: string; name: string; color: string }>;
}

interface CreateTransactionRequest {
  personal_id: number;
  date: Date;
  account_id: string;
  category_id: string;
  amount: number; // Negative for expense, positive for income
  type: 'INCOME' | 'EXPENSE';
  description?: string;
  currency?: string;
  label_ids?: string[];
  payer?: string;
  payment_type?: string;
  payment_status?: string;
}
```

### Endpoints

#### `fetchTransactions(params)`
```typescript
GET /api/v1/transactions?page=1&limit=50&account_id=...
Query Parameters:
- page, limit (pagination)
- account_id, category_id, type
- start_date, end_date
- min_amount, max_amount
- keyword (search)
- label_ids (comma-separated)
- payment_type, payment_status
- sort_by, sort_order

Response: {
  transactions: ApiTransactionResponse[];
  pagination: {
    page, limit, total, totalPages
  }
}
```

#### `fetchTransactionById(id)`
```typescript
GET /api/v1/transactions/{id}
Response: ApiTransactionResponse
```

#### `createTransaction(data)`
```typescript
POST /api/v1/transactions
Body: CreateTransactionRequest
Response: ApiTransactionResponse
```

**Important**: Amount should be negative for expenses, positive for income

#### `updateTransaction(id, data)`
```typescript
PUT /api/v1/transactions/{id}
Body: Partial<CreateTransactionRequest>
Response: ApiTransactionResponse
```

#### `deleteTransaction(id)`
```typescript
DELETE /api/v1/transactions/{id}
Response: { success, message }
```

#### `fetchTransactionSummary(params)`
```typescript
GET /api/v1/transactions/summary?start_date=...&end_date=...
Response: {
  total_income: number;
  total_expense: number;
  net_amount: number;
  transaction_count: number;
  by_category: Array<{
    category_id, category_name, total
  }>;
}
```

---

## 4. categoryService (`categoryService.ts`)

### Types
```typescript
interface ApiCategoryResponse {
  id: string;
  personal_id: number;
  user_id: string;
  parent_id?: string;
  name: string;
  icon: string;
  nature: 'WANT' | 'NEED' | 'MUST';
  is_active: boolean;
  color?: string;
  is_parent?: boolean;
}
```

### Endpoints

#### `fetchCategories(filters?)`
```typescript
GET /api/v1/categories?nature=EXPENSE&is_active=true&keyword=...
Response: ApiCategoryResponse[]
```

#### `fetchCategoryTree()`
```typescript
GET /api/v1/categories/tree
Response: CategoryTree (hierarchical structure)
```

#### `createCategory(data)`
```typescript
POST /api/v1/categories
Body: {
  personal_id,
  name,
  icon,
  nature: 'WANT' | 'NEED' | 'MUST',
  parent_id?,
  color?,
  is_active
}
Response: ApiCategoryResponse
```

#### `updateCategory(id, data)`
```typescript
PUT /api/v1/categories/{id}
Body: Partial<ApiCategoryResponse>
Response: ApiCategoryResponse
```

#### `deleteCategory(id)`
```typescript
DELETE /api/v1/categories/{id}
Response: { success, message }
```

#### `swapCategoryOrder(categoryIds)`
```typescript
POST /api/v1/categories/swap-order
Body: { category_ids: string[] }
Response: { success }
```

---

## 5. analyticsService (`analyticsService.ts`)

### Types
```typescript
interface ExpenseByCategory {
  name: string;
  value: number;
  color?: string;
}

interface IncomeExpenseTrend {
  period: string; // '2024-01', '2024-02', etc.
  income: number;
  expense: number;
}

interface DashboardTransaction {
  id: string;
  date: string;
  description: string;
  amount: number;
  type: 'INCOME' | 'EXPENSE';
  category_name: string;
  account_name: string;
}
```

### Endpoints

#### `fetchExpensesByCategory(params?)`
```typescript
GET /api/v1/analytics/expenses-by-category?start_date=...
Response: ExpenseByCategory[]
```

#### `fetchIncomeExpenseTrend(params?)`
```typescript
GET /api/v1/analytics/income-expense-trend?period=monthly
Response: IncomeExpenseTrend[]
```

#### `fetchRecentTransactions(limit = 20)`
```typescript
GET /api/v1/transactions?limit=20&sort_order=desc
Response: DashboardTransaction[]
```

#### `fetchBalanceTrend(params?)`
```typescript
GET /api/v1/analytics/balance-trend?start_date=...&end_date=...
Response: Array<{ date: string; balance: number }>
```

---

## 6. budgetService (`budgetService.ts`)

### Types
```typescript
interface BudgetStatus {
  id: string;
  category_name: string;
  budget_amount: number;
  spent_amount: number;
  percentage: number;
  status: 'on-track' | 'warning' | 'exceeded';
}
```

### Endpoints

#### `fetchBudgetStatus()`
```typescript
GET /api/v1/budgets/status
Response: BudgetStatus[]
```

---

## 7. labelService (`labelService.ts`)

### Types
```typescript
interface ApiLabelResponse {
  id: string;
  user_id: string;
  personal_id: number;
  name: string;
  color: string;
}
```

### Endpoints

#### `fetchLabels()`
```typescript
GET /api/v1/labels
Response: ApiLabelResponse[]
```

#### `createLabel(data)`
```typescript
POST /api/v1/labels
Body: { personal_id, name, color }
Response: ApiLabelResponse
```

#### `updateLabel(id, data)`
```typescript
PUT /api/v1/labels/{id}
Body: Partial<ApiLabelResponse>
Response: ApiLabelResponse
```

#### `deleteLabel(id)`
```typescript
DELETE /api/v1/labels/{id}
Response: { success, message }
```

---

## 8. groupService (`groupService.ts`)

### Endpoints

#### `fetchGroups()`
```typescript
GET /api/v1/groups
Response: ApiGroupResponse[]
```

#### `createGroup(data)`
```typescript
POST /api/v1/groups
Body: { personal_id, name }
Response: ApiGroupResponse
```

---

## Common Patterns

### 1. Error Handling
All services throw errors with formatted messages:
```typescript
try {
  await transactionService.createTransaction(data);
} catch (error) {
  showToast(error.message, 'error');
}
```

### 2. Type Safety
All responses typed with TypeScript interfaces

### 3. Consistent API
- All use base `api` client
- Automatic token injection
- Standardized response format

### 4. Filtering
Services accept filter objects:
```typescript
fetchTransactions({
  account_id: '123',
  start_date: '2024-01-01',
  type: 'EXPENSE'
})
```

### 5. Pagination
Standard pagination params:
```typescript
{ page: 1, limit: 50 }
```

---

## Key Considerations for Rewrite

1. **Token Management**: All services depend on `api.ts` for auth
2. **Error Messages**: Extract user-friendly messages from API errors
3. **Type Definitions**: Maintain strict interfaces for API responses
4. **Amount Convention**: Transactions use signed amounts (- = expense)
5. **Date Formatting**: Use `formatDateForBackend()` utility
6. **Personal ID**: Client manages incrementing, server validates
7. **Label IDs**: Pass as `label_ids` array in transaction requests
8. **Filter Objects**: Services accept optional filter parameters
9. **Response Unwrapping**: Some endpoints return `{ data: T }`, others return `T` directly
10. **Retry Logic**: Not in service layer, handled by context/components
