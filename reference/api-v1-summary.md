# API v1 Summary (`app/api/v1`)

## 🎯 **Purpose**
RESTful API backend built with Next.js App Router providing complete finance application functionality with JWT authentication, comprehensive validation, multi-tenant data isolation, and **automatic default data seeding** for new users.

## 🏗️ **API Architecture**

### **Structure Pattern**
```
app/api/v1/
├── auth/                    # Authentication endpoints
│   ├── login/route.ts      # POST - User login with JWT tokens
│   ├── logout/route.ts     # POST - Secure logout with cleanup
│   ├── refresh/route.ts    # POST - Token refresh
│   └── register/route.ts   # POST - User registration
├── accounts/               # Account management
│   ├── route.ts           # GET, POST - List/create accounts
│   ├── [id]/route.ts      # GET, PUT, DELETE - Account CRUD
│   └── swap-order/route.ts # PUT - Drag-and-drop reordering
├── transactions/          # Transaction management
│   ├── route.ts           # GET, POST - List/create transactions
│   ├── [id]/route.ts      # GET, PUT, DELETE - Transaction CRUD
│   └── summary/route.ts   # GET - Transaction statistics
├── categories/            # Category management
│   ├── route.ts           # GET, POST - List/create categories
│   ├── [id]/route.ts      # GET, PUT, DELETE - Category CRUD
│   ├── tree/route.ts      # GET - Hierarchical category tree
│   └── swap-order/route.ts # PUT - Category reordering
├── transfers/             # Account-to-account transfers
│   ├── route.ts           # GET, POST - List/create transfers
│   └── [id]/route.ts      # GET, PUT, DELETE - Transfer CRUD
├── labels/                # Transaction labels/tags
│   ├── route.ts           # GET, POST - List/create labels
│   └── [id]/route.ts      # GET, PUT, DELETE - Label CRUD
├── groups/                # Account grouping
│   ├── route.ts           # GET, POST - List/create groups
│   └── [id]/route.ts      # GET, PUT, DELETE - Group CRUD
└── debts/                 # Debt tracking
    ├── route.ts           # GET, POST - List/create debts
    └── [id]/route.ts      # GET, PUT, DELETE - Debt CRUD
```

---

## 🔐 **Authentication System**

### **JWT Token Strategy**
```typescript
// Dual token system
interface TokenPayload {
  user_id: string;
  email: string;
  username: string;
}

// Token lifetimes
ACCESS_TOKEN_TTL: 24 hours
REFRESH_TOKEN_TTL: 7 days
```

### **Authentication Endpoints**

#### **POST `/api/v1/auth/login`**
**Purpose**: User authentication with email or username

**Request**:
```json
{
  "email_or_username": "user@example.com",
  "password": "securepassword"
}
```

**Response**:
```json
{
  "success": true,
  "message": "Login successful",
  "data": {
    "access_token": "eyJhbGciOiJIUzI1NiIs...",
    "refresh_token": "eyJhbGciOiJIUzI1NiIs...",
    "user": {
      "id": "uuid-string",
      "email": "user@example.com",
      "username": "username",
      "created_at": "2024-11-17T00:00:00.000Z",
      "updated_at": "2024-11-17T00:00:00.000Z"
    }
  }
}
```

#### **POST `/api/v1/auth/refresh`**
**Purpose**: Refresh expired access tokens

**Request**:
```json
{
  "refresh_token": "eyJhbGciOiJIUzI1NiIs..."
}
```

**Response**:
```json
{
  "success": true,
  "data": {
    "access_token": "new-jwt-token",
    "refresh_token": "new-refresh-token",
    "expired_at": "2024-11-18T00:00:00.000Z",
    "refreshable_until": "2024-11-24T00:00:00.000Z"
  }
}
```

#### **POST `/api/v1/auth/logout`**
**Purpose**: Secure session termination

**Headers**: `Authorization: Bearer <token>`

**Response**:
```json
{
  "success": true,
  "message": "Logout successful"
}
```

#### **POST `/api/v1/auth/register`**
**Purpose**: New user registration with automatic default data seeding

**Request**:
```json
{
  "email": "john@example.com", 
  "username": "johndoe",
  "password": "securepassword"
}
```

**Response**:
```json
{
  "success": true,
  "message": "User registered successfully",
  "data": {
    "access_token": "eyJhbGciOiJIUzI1NiIs...",
    "refresh_token": "eyJhbGciOiJIUzI1NiIs...",
    "user": {
      "id": "uuid-string",
      "email": "john@example.com",
      "username": "johndoe",
      "created_at": "2024-11-17T00:00:00.000Z",
      "updated_at": "2024-11-17T00:00:00.000Z"
    }
  }
}
```

**Automatic Data Seeding**:
When a new user registers, the system automatically creates:

**Default Categories** (`src/data/default_categories.json`):
11 major category groups with 70+ subcategories:

1. **Food & Drinks** (4 subcategories)
   - Groceries, main meal • Bar, cafe, snack • Restaurant, fast-food

2. **Shopping** (11 subcategories) 
   - Clothes & shoes • Electronics • Toiletries • Gifts • Home & garden • Kids • Pets • etc.

3. **Housing** (6 subcategories)
   - Rent • Mortgage • Energy, utilities • Maintenance • Property insurance • Services

4. **Transportation** (4 subcategories)
   - Public transport • Taxi • Business trips • Long distance

5. **Vehicle** (6 subcategories) 
   - Fuel • Parking • Vehicle maintenance • Vehicle insurance • Leasing • Rentals

6. **Life & Entertainment** (13 subcategories)
   - Health care • Education • Hobbies • Holiday, trips • Active sport • Books, subscriptions • etc.

7. **Communication, Gadgets** (4 subcategories)
   - Internet, phone • Laptop, Smartphone • Software, apps • Postal services

8. **Financial Expenses** (7 subcategories)
   - Taxes • Insurances • Loan, interests • Charges, fees • Fines • Child support

9. **Investments** (5 subcategories)
   - Financial investments • Savings • Realty • Collection • Vehicles, chattels

10. **Income** (11 subcategories)
    - Wage, invoices • Interests • Gifts • Rental income • Dividends • Sale • Refunds • etc.

11. **Others** (2 subcategories)
    - Others • Missing

**Features**:
- **Need/Want/Must Classification**: Categories classified by priority (NEED, WANT, MUST)
- **FontAwesome Icons**: Each category has specific icon (FaUtensils, FaCar, FaHome, etc.)
- **Color-coded**: Unified color scheme with customizable colors
- **Hierarchical Structure**: Parent → Children relationship for organization

**Default Accounts** (`src/data/default_accounts.json`):
3 essential accounts for immediate use:

1. **Cash Wallet** (personal_id: 1)
   - Icon: wallet • Status: Active, Usable • Balance: 0.00

2. **Bank Account** (personal_id: 2) 
   - Icon: bank • Status: Active, Usable • Balance: 0.00

3. **Savings** (personal_id: 3)
   - Icon: piggy-bank • Status: Active, Usable • Balance: 0.00

**Features**:
- **Ready to Use**: All accounts active and immediately usable
- **Sequential Personal IDs**: 1, 2, 3 for easy reference  
- **Zero Balance Start**: Users can add initial amounts later
- **Standard Icons**: Recognizable icons for quick identification

**Seeding Process**:
```typescript
// Registration flow with automatic seeding:
1. Create user account with encrypted password
2. Generate JWT tokens (access + refresh)
3. Auto-create 70+ default categories:
   - Create parent categories first
   - Create child categories with parent_id links
   - Sequential personal_ids starting from 1
4. Auto-create 3 default accounts:
   - Cash Wallet, Bank Account, Savings
   - personal_ids: 1, 2, 3
5. All data isolated by user_id (multi-tenant)
6. Error handling: continues even if seeding partially fails
7. Return tokens and user data for immediate login
```

**Benefits**:
- **Zero Setup**: Users can start tracking immediately after registration
- **Best Practices**: Comprehensive categorization system included
- **Customizable**: Users can modify, add, or remove categories/accounts later
- **Production Ready**: New users get a fully functional finance tracking system

---

## 🏦 **Core Resource Endpoints**

### **Accounts Management**

#### **GET `/api/v1/accounts`**
**Purpose**: List user accounts with filtering

**Query Params**:
- `keyword` - Search account names
- `limit` - Pagination limit (default: 100)
- `offset` - Pagination offset (default: 0)

**Response**:
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid-string",
      "user_id": "user-uuid",
      "personal_id": 1,
      "name": "Main Checking",
      "icon": "FaUniversity",
      "active": true,
      "usability": "USABLE",
      "account_type": "Checking account",
      "color": "#2563eb",
      "initial_amount": 1000.00,
      "balance": 1250.75,
      "group_id": null,
      "created_at": "2024-11-17T00:00:00.000Z",
      "updated_at": "2024-11-17T00:00:00.000Z"
    }
  ],
  "meta": {
    "max_personal_id": 5,
    "total": 3,
    "limit": 100,
    "offset": 0
  }
}
```

#### **POST `/api/v1/accounts`**
**Purpose**: Create new account

**Request**:
```json
{
  "personal_id": 6,
  "name": "Savings Account",
  "icon": "FaPiggyBank",
  "account_type": "Savings account",
  "color": "#22c55e",
  "initial_amount": 5000.00,
  "usability": "USABLE",
  "active": true
}
```

#### **PUT `/api/v1/accounts/swap-order`**
**Purpose**: Reorder accounts via drag-and-drop

**Request**:
```json
{
  "order_map": [
    { "id": "uuid-1", "personal_id": 1 },
    { "id": "uuid-2", "personal_id": 2 },
    { "id": "uuid-3", "personal_id": 3 }
  ]
}
```

**Implementation**:
- Uses two-phase transaction to avoid unique constraint violations
- Phase 1: Sets temporary negative personal_id values
- Phase 2: Sets final positive personal_id values

### **Transactions Management**

#### **GET `/api/v1/transactions`**
**Purpose**: List transactions with advanced filtering

**Query Params**:
- `account_ids` - Comma-separated account UUIDs
- `category_ids` - Comma-separated category UUIDs  
- `type` - 'INCOME' or 'EXPENSE'
- `start_date` - Filter from date (YYYY-MM-DD)
- `end_date` - Filter to date (YYYY-MM-DD)
- `min_amount` - Minimum amount filter
- `max_amount` - Maximum amount filter
- `keyword` - Search in descriptions
- `sort` - Sort order ('date_desc', 'date_asc', 'amount_desc', 'amount_asc')
- `limit` - Pagination limit
- `offset` - Pagination offset

**Response**:
```json
{
  "success": true,
  "data": [
    {
      "id": "transaction-uuid",
      "user_id": "user-uuid",
      "personal_id": 123,
      "date": "2024-11-17",
      "account_id": "account-uuid",
      "category_id": "category-uuid",
      "amount": -45.00,
      "type": "EXPENSE",
      "description": "Coffee at Starbucks",
      "currency": "IDR",
      "payer": "John Doe",
      "payment_type": "Credit Card",
      "payment_status": "Cleared",
      "labels": [
        {
          "id": "label-uuid",
          "name": "Coffee",
          "color": "#8b5cf6"
        }
      ]
    }
  ],
  "meta": {
    "total": 1500,
    "limit": 50,
    "offset": 0,
    "max_personal_id": 1500
  }
}
```

#### **POST `/api/v1/transactions`**
**Purpose**: Create new transaction

**Request**:
```json
{
  "personal_id": 124,
  "date": "2024-11-17T14:30:00.000Z",
  "account_id": "account-uuid",
  "category_id": "category-uuid", 
  "amount": -75.50,
  "type": "EXPENSE",
  "description": "Lunch at restaurant",
  "currency": "IDR",
  "payer": "John Doe",
  "payment_type": "Cash",
  "payment_status": "Cleared",
  "label_ids": ["label-uuid-1", "label-uuid-2"]
}
```

**409 Conflict Response**:
```json
{
  "success": false,
  "message": "A transaction with this personal_id already exists"
}
```

#### **GET `/api/v1/transactions/summary`**
**Purpose**: Transaction statistics and analytics

**Query Params**: Same date/account filtering as main transactions endpoint

**Response**:
```json
{
  "success": true,
  "data": {
    "total_income": 5000.00,
    "total_expense": 3500.00,
    "net_amount": 1500.00,
    "transaction_count": 45,
    "by_category": [
      {
        "category_id": "uuid",
        "category_name": "Food & Dining",
        "total": 1200.00,
        "count": 15
      }
    ]
  }
}
```

### **Categories Management**

#### **GET `/api/v1/categories`**
**Purpose**: List categories with filtering

**Query Params**:
- `keyword` - Search category names
- `nature` - 'INCOME' or 'EXPENSE' 
- `is_active` - Filter by active status
- `limit`, `offset` - Pagination

#### **GET `/api/v1/categories/tree`**
**Purpose**: Hierarchical category structure

**Response**:
```json
{
  "success": true,
  "data": [
    {
      "id": "parent-uuid",
      "name": "Food & Dining",
      "nature": "EXPENSE",
      "icon": "FaUtensils",
      "color": "#ef4444",
      "children": [
        {
          "id": "child-uuid",
          "name": "Restaurants",
          "parent_id": "parent-uuid"
        }
      ]
    }
  ]
}
```

### **Transfers Management**

#### **POST `/api/v1/transfers`**
**Purpose**: Create account-to-account transfer

**Request**:
```json
{
  "personal_id": 25,
  "date": "2024-11-17T10:00:00.000Z",
  "from_account": "checking-uuid",
  "to_account": "savings-uuid", 
  "amount": 1000.00,
  "to_amount": 1000.00,
  "description": "Monthly savings transfer",
  "currency": "IDR",
  "to_currency": "IDR"
}
```

**Behavior**:
- Creates transfer record
- Creates two linked transactions:
  - EXPENSE transaction in source account (-amount)
  - INCOME transaction in destination account (+amount)
- All operations wrapped in database transaction for atomicity

---

## 🔧 **Common API Patterns**

### **1. Authentication Middleware**
```typescript
// All protected endpoints use requireAuth
const authResult = await requireAuth(request);
if ('error' in authResult) {
  return authResult.error; // 401 Unauthorized
}
const { user } = authResult;
```

### **2. Validation System**
```typescript
// Request body validation
const { field1, field2 } = await validateBody(request, RequestSchema);

// Query parameter validation  
const { keyword, limit, offset } = validateQuery(request, QuerySchema);

// Path parameter validation
const { id } = validatePathParams(params, PathParamsSchema);

// Response validation
const validatedResponse = validateResponse(response, ResponseSchema);
```

### **3. Response Format**
```typescript
// Success responses
ApiResponseBuilder.success(message, data, meta?)

// Error responses
ApiResponseBuilder.error(message)

// Standard format
{
  "success": boolean,
  "message": string,
  "data"?: any,
  "meta"?: { pagination, totals, etc. }
}
```

### **4. Error Handling**
```typescript
try {
  // API logic
} catch (error) {
  if (error instanceof ValidationError) {
    return handleValidationError(error);
  }
  
  console.error('API Error:', error);
  return NextResponse.json(
    ApiResponseBuilder.error('Internal server error'), 
    { status: 500 }
  );
}
```

### **5. Multi-tenancy**
```typescript
// All queries automatically filtered by user_id
const accounts = await db.accounts.findMany({
  where: {
    user_id: user.user_id, // From JWT token
    // ... other filters
  }
});
```

---

## 🆔 **Personal ID System**

### **Purpose**
User-specific sequential numbering for each resource type (Transaction #1, #2, #3...)

### **Implementation**
```typescript
// Find max personal_id for user
const maxResult = await db.transactions.findFirst({
  where: { user_id: user.user_id },
  orderBy: { personal_id: 'desc' },
  select: { personal_id: true }
});

const maxPersonalId = Number(maxResult?.personal_id ?? 0n);
```

### **Conflict Resolution**
```typescript
// 409 Conflict when personal_id already exists
try {
  await db.transactions.create({
    data: { personal_id: requestedId, /* ... */ }
  });
} catch (error) {
  if (error.code === 'P2002') { // Unique constraint violation
    return NextResponse.json(
      ApiResponseBuilder.error('A transaction with this personal_id already exists'),
      { status: 409 }
    );
  }
}
```

**Client Retry Logic**:
- Frontend catches 409 responses
- Increments personal_id and retries
- Max 3 retry attempts

---

## 📊 **Advanced Features**

### **1. Drag-and-Drop Reordering**
```typescript
// Accounts and Categories support swap-order endpoints
PUT /api/v1/accounts/swap-order
PUT /api/v1/categories/swap-order

// Two-phase update to prevent unique constraint violations
// Phase 1: Temporary negative values
// Phase 2: Final positive values
```

### **2. Comprehensive Filtering**
```typescript
// Advanced query building
const whereClause = {
  user_id: user.user_id,
  ...(account_ids?.length && { account_id: { in: account_ids } }),
  ...(category_ids?.length && { category_id: { in: category_ids } }),
  ...(start_date && { date: { gte: new Date(start_date) } }),
  ...(end_date && { date: { lte: new Date(end_date) } }),
  ...(keyword && { description: { contains: keyword, mode: 'insensitive' } }),
};
```

### **3. Balance Calculations**
```typescript
// Real-time balance calculation from transactions
const balance = initial_amount + 
  transactions.reduce((sum, t) => 
    sum + (t.type === 'INCOME' ? t.amount : -t.amount), 0
  );
```

### **4. Linked Data Management**
```typescript
// Transfers create linked transactions atomically
await db.$transaction(async (tx) => {
  // Create transfer record
  const transfer = await tx.transfers.create({ data: transferData });
  
  // Create expense transaction (from account)
  await tx.transactions.create({ 
    data: { amount: -amount, account_id: from_account, transfer_id: transfer.id }
  });
  
  // Create income transaction (to account)
  await tx.transactions.create({ 
    data: { amount: to_amount, account_id: to_account, transfer_id: transfer.id }
  });
});
```

---

## 🛡️ **Security Features**

### **1. Authentication Required**
- All endpoints except auth require valid JWT token
- Bearer token in Authorization header
- Token payload includes user identification

### **2. Data Isolation** 
- All queries filtered by `user_id` from JWT
- Users can only access their own data
- No cross-user data leakage possible

### **3. Input Validation**
- Zod schema validation for all inputs
- Type-safe request/response handling
- SQL injection prevention through Prisma ORM

### **4. Password Security**
- bcrypt hashing with salt rounds
- Passwords never returned in responses
- Secure password comparison

### **5. Token Security**
- JWT signed with secure secrets
- Refresh token rotation
- Configurable token lifetimes
- Secure logout with token cleanup

---

## 📈 **Performance Optimizations**

### **1. Database Queries**
```typescript
// Efficient joins and selects
const transactions = await db.transactions.findMany({
  where: filters,
  include: {
    category: { select: { name: true, icon: true, color: true } },
    account: { select: { name: true } },
    labels: { select: { id: true, name: true, color: true } }
  },
  orderBy: { date: 'desc' },
  take: limit,
  skip: offset
});
```

### **2. Pagination**
- Consistent limit/offset pagination
- Metadata includes totals for UI
- Configurable page sizes

### **3. Selective Data Loading**
- Only load required fields
- Conditional includes based on request needs
- Efficient count queries for totals

---

## 🔍 **API Documentation**

### **OpenAPI Integration**
- Comprehensive JSDoc annotations for every endpoint
- Scalar API documentation generation
- Request/response examples
- Error code documentation

### **Documentation Tags**
- **@summary**: Endpoint description
- **@description**: Detailed behavior explanation  
- **@tag**: Resource grouping (Auth, Accounts, Transactions, etc.)
- **@security**: Authentication requirements (bearerAuth)
- **@response**: Status codes and response formats

---

## ⚠️ **Critical Implementation Notes**

### **1. Database Schema Alignment**
```typescript
// CRITICAL: Schema must match database structure
// transactions table has 'description' field, NOT 'note'
// Always verify field mappings before deployment
```

### **2. Personal ID Conflicts**
- 409 status codes require client retry logic
- Frontend must implement max 3 retry attempts
- Personal ID management being migrated to server-side

### **3. Transfer Atomicity**
- All transfer operations must use database transactions
- Linked transaction consistency is critical
- Rollback on any failure in the chain

### **4. Token Management**
- Access tokens expire after 24 hours
- Refresh tokens expire after 7 days
- Client must handle token refresh automatically

### **5. Data Validation**
- All inputs validated with Zod schemas
- Type safety enforced throughout
- Graceful error handling for validation failures

---

## 📋 **API Endpoint Summary**

| Resource | Endpoints | CRUD | Special Features |
|----------|-----------|------|------------------|
| **Auth** | 4 endpoints | Login/Register/Refresh/Logout | JWT tokens, secure logout |
| **Accounts** | 3 endpoints | Full CRUD | Balance calculation, reordering |
| **Transactions** | 4 endpoints | Full CRUD + Summary | Advanced filtering, personal_id conflicts |
| **Categories** | 4 endpoints | Full CRUD + Tree | Hierarchical structure, reordering |
| **Transfers** | 2 endpoints | Full CRUD | Linked transactions, atomicity |
| **Labels** | 2 endpoints | Full CRUD | Transaction tagging |
| **Groups** | 2 endpoints | Full CRUD | Account grouping |
| **Debts** | 2 endpoints | Full CRUD | Debt tracking |

**Total Endpoints**: 23 endpoints across 8 resource types

## 🌟 **Key Feature: Automatic Data Seeding**
New user registration automatically creates:
- **70+ default categories** in 11 major groups (Food, Shopping, Housing, Transportation, etc.)
- **3 default accounts** (Cash Wallet, Bank Account, Savings)  
- **Hierarchical category structure** with parent/child relationships
- **Ready-to-use finance tracking system** with zero manual setup required

---

## 🚀 **API Status & Readiness**

✅ **Authentication System** - Complete JWT implementation with refresh tokens  
✅ **CRUD Operations** - Full create, read, update, delete for all resources  
✅ **Advanced Filtering** - Comprehensive query parameters and search  
✅ **Data Validation** - Zod schema validation throughout  
✅ **Security Implementation** - Multi-tenant isolation, input validation  
✅ **Performance Features** - Pagination, selective loading, efficient queries  
✅ **Auto Data Seeding** - 70+ categories + 3 accounts created automatically on registration  
✅ **Documentation** - OpenAPI annotations and response examples  
✅ **Error Handling** - Consistent error responses and status codes  

**The API is production-ready** with robust authentication, automatic user onboarding, comprehensive validation, and advanced features for a complete finance application backend! 🎯
