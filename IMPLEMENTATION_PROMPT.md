# 🎯 Complete Implementation Prompt for Personal Finance Management Application

## Project Mission Statement

Build a production-ready personal finance management application similar to BudgetBakers using Next.js 15+ and PostgreSQL. The application must use a monolithic architecture with strict REST API separation between frontend and backend, enabling future backend replacement with any programming language.

---

## 📋 Implementation Requirements Checklist

### Phase 1: Project Setup & Infrastructure (Week 1)

```markdown
□ 1.1 Initialize Next.js 15+ project with TypeScript
   - Use App Router (not Pages Router)
   - Configure TypeScript with strict mode
   - Setup path aliases (@/components, @/lib, etc.)
   
□ 1.2 Setup PostgreSQL database
   - Install PostgreSQL 16+
   - Create database: finance_db
   - Configure connection pooling
   
□ 1.3 Configure Prisma ORM
   - Install Prisma 5.8+
   - Define schema with all 13 tables
   - Setup migrations folder structure
   
□ 1.4 Setup development environment
   - Configure ESLint with Next.js rules
   - Setup Prettier with consistent formatting
   - Configure Husky for pre-commit hooks
   - Setup environment variables (.env structure)
   
□ 1.5 Configure Docker (optional but recommended)
   - PostgreSQL container
   - Redis container for caching
   - MinIO for local file storage
```

### Phase 2: Database Implementation (Week 1-2)

```sql
-- CRITICAL: Implement these tables in exact order due to foreign key dependencies

□ 2.1 Create users table
   - UUID primary keys
   - Email + username unique constraints
   - Password hash with bcrypt
   - Soft delete with deleted_at
   - Timezone and locale settings
   
□ 2.2 Create account_groups table
   - User association
   - Position for ordering
   
□ 2.3 Create accounts table
   - Multiple account types (checking, savings, credit_card, cash, investment, loan)
   - Multi-currency support
   - Initial and current balance tracking
   - Credit limit for credit cards
   - Interest rate for savings/loans
   
□ 2.4 Create categories table (hierarchical)
   - Parent-child relationships
   - Type: income/expense/transfer
   - System vs user-defined categories
   - Icons and colors
   
□ 2.5 Create transactions table
   - Amount convention: negative for expenses, positive for income
   - Multiple payment methods
   - Currency and exchange rates
   - Transfer linkage support
   
□ 2.6 Create labels table
   - User-specific tags
   - Color coding
   
□ 2.7 Create transaction_labels junction table
   - Many-to-many relationship
   - CASCADE delete rules
   
□ 2.8 Create budgets table
   - Multiple budget types (category, total, envelope)
   - Period settings (monthly, quarterly, yearly)
   - Alert thresholds
   
□ 2.9 Create budget_categories table
   - Links budgets to categories
   - Allocated amounts
   
□ 2.10 Create recurring_transactions table
   - Frequency patterns
   - Auto-generation logic
   
□ 2.11 Create goals table
   - Savings goals tracking
   - Target vs current amounts
   
□ 2.12 Create attachments table
   - Receipt/document storage
   - File metadata
   
□ 2.13 Create audit_logs table
   - Track all data changes
   - JSONB for old/new values
```

### Phase 3: Authentication System (Week 2)

```typescript
□ 3.1 Implement JWT authentication
   - Access token (24h expiry)
   - Refresh token (7 days expiry)
   - Token rotation on refresh
   - Secure token storage strategy
   
□ 3.2 Create authentication endpoints
   POST /api/v1/auth/register
   - Email validation
   - Password strength requirements (min 8 chars, mixed case, numbers, special)
   - Auto-create default categories (70+ categories)
   - Auto-create default accounts (Cash, Checking, Savings)
   - Return tokens for immediate login
   
   POST /api/v1/auth/login
   - Support email or username
   - Account lockout after 5 failed attempts
   - Return user data with tokens
   
   POST /api/v1/auth/refresh
   - Validate refresh token
   - Generate new token pair
   - Implement token rotation
   
   POST /api/v1/auth/logout
   - Invalidate tokens
   - Clear client storage
   
   POST /api/v1/auth/forgot-password
   - Send reset email
   - Generate secure reset token
   
   POST /api/v1/auth/reset-password
   - Validate reset token
   - Update password
   
□ 3.3 Implement authentication middleware
   - Extract and verify JWT
   - Attach user to request context
   - Handle token expiry gracefully
   
□ 3.4 Optional: Implement 2FA
   - TOTP generation
   - QR code for authenticator apps
   - Backup codes
```

### Phase 4: Core API Implementation (Week 2-3)

```typescript
□ 4.1 Account Management APIs
   GET    /api/v1/accounts
   - Filter by group, active status
   - Include calculated balances
   - Support pagination
   
   POST   /api/v1/accounts
   - Validate account types
   - Set initial balance
   - Auto-assign position
   
   GET    /api/v1/accounts/{id}
   PUT    /api/v1/accounts/{id}
   DELETE /api/v1/accounts/{id}
   - Soft delete implementation
   - Check for linked transactions
   
   PUT    /api/v1/accounts/reorder
   - Drag-drop position updates
   - Bulk update positions
   
□ 4.2 Transaction Management APIs
   GET    /api/v1/transactions
   - Advanced filtering (date range, amount, category, account)
   - Full-text search in descriptions
   - Pagination with cursor and offset
   - Sorting options
   
   POST   /api/v1/transactions
   - Validate amount sign convention
   - Auto-update account balance
   - Support multiple labels
   - Handle attachments
   
   GET    /api/v1/transactions/{id}
   - Include related data (category, account, labels)
   
   PUT    /api/v1/transactions/{id}
   - Recalculate account balances
   - Maintain audit trail
   
   DELETE /api/v1/transactions/{id}
   - Soft delete option
   - Update account balance
   
   POST   /api/v1/transactions/bulk
   - Batch operations
   - Transaction support
   
   POST   /api/v1/transactions/import
   - CSV parser implementation
   - OFX/QIF format support
   - Duplicate detection
   - Validation and error reporting
   
□ 4.3 Category Management APIs
   GET    /api/v1/categories
   GET    /api/v1/categories/tree
   - Hierarchical structure
   - Include transaction counts
   
   POST   /api/v1/categories
   - Validate parent relationships
   - Auto-assign colors from parent
   
   PUT    /api/v1/categories/{id}
   DELETE /api/v1/categories/{id}
   - Check for linked transactions
   - Option to reassign transactions
   
□ 4.4 Transfer APIs
   POST   /api/v1/transfers
   - Create linked transactions
   - Handle currency conversion
   - Atomic operation
   
   GET    /api/v1/transfers
   PUT    /api/v1/transfers/{id}
   DELETE /api/v1/transfers/{id}
   - Delete linked transactions
   
□ 4.5 Budget APIs
   GET    /api/v1/budgets
   POST   /api/v1/budgets
   - Category allocation
   - Period configuration
   
   GET    /api/v1/budgets/{id}/progress
   - Calculate spent amount
   - Projection calculations
   - Alert threshold checks
   
   PUT    /api/v1/budgets/{id}
   DELETE /api/v1/budgets/{id}
   
□ 4.6 Analytics APIs
   GET    /api/v1/analytics/summary
   - Income vs expense totals
   - Net worth calculation
   - Savings rate
   
   GET    /api/v1/analytics/trends
   - Time-series data
   - Moving averages
   - Year-over-year comparison
   
   GET    /api/v1/analytics/cashflow
   - Inflow/outflow analysis
   - Cash flow forecast
   
   GET    /api/v1/analytics/categories
   - Spending by category
   - Category trends
   - Budget vs actual
```

### Phase 5: Frontend Implementation (Week 3-4)

```typescript
□ 5.1 Setup UI Framework
   - Install and configure Tailwind CSS
   - Setup Shadcn UI components
   - Configure theme variables
   - Setup responsive breakpoints
   
□ 5.2 Create Layout Components
   components/layout/
   ├── AppShell.tsx      // Main app layout
   ├── Sidebar.tsx       // Navigation sidebar
   ├── Header.tsx        // Top header with user menu
   ├── Footer.tsx        // Footer (if needed)
   └── MobileNav.tsx     // Mobile navigation
   
□ 5.3 Implement Authentication Pages
   app/(auth)/
   ├── login/page.tsx
   │   - Email/username input
   │   - Password with show/hide toggle
   │   - Remember me option
   │   - Forgot password link
   │   
   ├── register/page.tsx
   │   - Multi-step registration
   │   - Password strength indicator
   │   - Terms acceptance
   │   
   └── forgot-password/page.tsx
       - Email input
       - Success message
   
□ 5.4 Create Dashboard
   app/(dashboard)/page.tsx
   - Account balance cards
   - Recent transactions list
   - Budget progress widgets
   - Quick actions (add transaction, transfer)
   - Monthly spending chart
   - Net worth trend
   
□ 5.5 Implement Accounts Page
   app/(dashboard)/accounts/page.tsx
   - Account list with balances
   - Drag-and-drop reordering
   - Add/edit account modal
   - Account type icons
   - Active/archived toggle
   - Group management
   
□ 5.6 Build Transactions Page
   app/(dashboard)/transactions/page.tsx
   - Transaction list table
   - Advanced filters sidebar
   - Search functionality
   - Add transaction modal
   - Bulk operations toolbar
   - Import/export buttons
   - Pagination controls
   
□ 5.7 Create Categories Page
   app/(dashboard)/categories/page.tsx
   - Tree view for hierarchy
   - Drag-drop reorganization
   - Add/edit category modal
   - Icon and color pickers
   - Usage statistics
   
□ 5.8 Implement Budgets Page
   app/(dashboard)/budgets/page.tsx
   - Budget cards with progress bars
   - Period selector
   - Budget vs actual charts
   - Alert configuration
   - Budget history
   
□ 5.9 Build Analytics Page
   app/(dashboard)/analytics/page.tsx
   - Multiple chart types
   - Date range selector
   - Export functionality
   - Drill-down capabilities
   - Custom report builder
   
□ 5.10 Create Settings Page
   app/(dashboard)/settings/page.tsx
   - Profile management
   - Security settings
   - Notification preferences
   - Data export/import
   - Account deletion
```

### Phase 6: State Management & Data Fetching (Week 4)

```typescript
□ 6.1 Setup Zustand Store
   stores/
   ├── useAuthStore.ts
   │   - User state
   │   - Token management
   │   - Login/logout actions
   │   
   ├── useAccountStore.ts
   │   - Accounts list
   │   - Selected account
   │   - Balance calculations
   │   
   ├── useTransactionStore.ts
   │   - Transactions list
   │   - Filters state
   │   - Pagination state
   │   
   └── useUIStore.ts
       - Sidebar state
       - Modal states
       - Theme preferences
   
□ 6.2 Implement React Query
   - Setup QueryClient
   - Configure default options
   - Implement query keys factory
   - Setup optimistic updates
   - Configure cache invalidation
   
□ 6.3 Create API Client
   lib/api/
   ├── client.ts         // Axios instance
   ├── auth.ts          // Auth endpoints
   ├── accounts.ts      // Account endpoints
   ├── transactions.ts  // Transaction endpoints
   ├── categories.ts    // Category endpoints
   └── analytics.ts     // Analytics endpoints
   
□ 6.4 Build Custom Hooks
   hooks/
   ├── useAuth.ts
   ├── useAccounts.ts
   ├── useTransactions.ts
   ├── useBudgets.ts
   ├── useAnalytics.ts
   └── useInfiniteScroll.ts
```

### Phase 7: Advanced Features (Week 5)

```typescript
□ 7.1 Implement Recurring Transactions
   - Cron job or background task
   - Frequency patterns
   - Auto-generation logic
   - Edit series vs single instance
   
□ 7.2 Add File Attachments
   - Receipt upload
   - Image preview
   - S3/MinIO integration
   - File size limits
   - Supported formats
   
□ 7.3 Create Data Import/Export
   - CSV parser
   - Column mapping UI
   - Validation preview
   - Error handling
   - Progress indicator
   
□ 7.4 Build Search Functionality
   - Full-text search
   - Filter combinations
   - Search suggestions
   - Recent searches
   - Saved searches
   
□ 7.5 Implement Notifications
   - Budget alerts
   - Bill reminders
   - Goal progress
   - Email notifications
   - Push notifications (PWA)
```

### Phase 8: Testing Implementation (Week 5-6)

```typescript
□ 8.1 Unit Tests
   __tests__/unit/
   ├── services/        // Business logic tests
   ├── utils/          // Utility function tests
   ├── components/     // Component tests
   └── hooks/          // Custom hook tests
   
   Coverage targets:
   - Statements: 80%
   - Branches: 80%
   - Functions: 80%
   - Lines: 80%
   
□ 8.2 Integration Tests
   __tests__/integration/
   ├── auth.test.ts    // Auth flow tests
   ├── api/            // API endpoint tests
   └── db/             // Database operation tests
   
□ 8.3 E2E Tests
   e2e/
   ├── auth.spec.ts
   ├── transactions.spec.ts
   ├── budgets.spec.ts
   └── analytics.spec.ts
   
□ 8.4 Performance Tests
   - Lighthouse CI
   - Bundle size monitoring
   - API response time tests
   - Database query performance
```

### Phase 9: Security & Performance (Week 6)

```typescript
□ 9.1 Security Implementation
   - Rate limiting (100 req/min per user)
   - CORS configuration
   - Security headers (HSTS, CSP, etc.)
   - Input sanitization
   - SQL injection prevention
   - XSS protection
   
□ 9.2 Performance Optimization
   - Code splitting
   - Lazy loading
   - Image optimization
   - API response caching
   - Database query optimization
   - Index creation
   
□ 9.3 PWA Features
   - Service worker
   - Offline support
   - Install prompt
   - Push notifications
   - Background sync
   
□ 9.4 Monitoring Setup
   - Sentry error tracking
   - Performance monitoring
   - Custom metrics
   - Logging strategy
   - Health checks
```

### Phase 10: Deployment & DevOps (Week 6)

```yaml
□ 10.1 Setup CI/CD Pipeline
   .github/workflows/
   ├── test.yml         # Run tests on PR
   ├── build.yml        # Build verification
   └── deploy.yml       # Deploy to production
   
□ 10.2 Configure Environments
   - Development
   - Staging
   - Production
   - Environment variables
   - Secrets management
   
□ 10.3 Database Migrations
   - Migration scripts
   - Rollback procedures
   - Data backup strategy
   - Zero-downtime deploys
   
□ 10.4 Deployment Configuration
   - Vercel/AWS/DigitalOcean setup
   - Domain configuration
   - SSL certificates
   - CDN setup
   - Load balancing
```

---

## 🔥 Critical Implementation Details

### Database Seed Data Structure

```typescript
// MUST implement on user registration
const DEFAULT_CATEGORIES = {
  income: [
    { name: "Salary", icon: "FaBriefcase", color: "#10b981" },
    { name: "Freelance", icon: "FaLaptop", color: "#10b981" },
    { name: "Investments", icon: "FaChartLine", color: "#10b981" },
    { name: "Rental Income", icon: "FaHome", color: "#10b981" },
    { name: "Business", icon: "FaStore", color: "#10b981" },
    { name: "Gifts", icon: "FaGift", color: "#10b981" },
    { name: "Refunds", icon: "FaUndo", color: "#10b981" },
    { name: "Other Income", icon: "FaCoins", color: "#10b981" }
  ],
  expense: {
    "Food & Dining": {
      icon: "FaUtensils",
      color: "#f59e0b",
      children: ["Groceries", "Restaurants", "Coffee Shops", "Fast Food", "Delivery"]
    },
    "Transportation": {
      icon: "FaCar",
      color: "#6366f1",
      children: ["Fuel", "Public Transport", "Taxi/Uber", "Parking", "Car Maintenance", "Car Insurance"]
    },
    "Shopping": {
      icon: "FaShoppingBag",
      color: "#ec4899",
      children: ["Clothing", "Electronics", "Home & Garden", "Books", "Gifts", "Personal Care"]
    },
    "Housing": {
      icon: "FaHome",
      color: "#8b5cf6",
      children: ["Rent/Mortgage", "Property Insurance", "Property Tax", "Maintenance", "Home Services"]
    },
    "Utilities": {
      icon: "FaBolt",
      color: "#ef4444",
      children: ["Electricity", "Water", "Gas", "Internet", "Phone", "Trash"]
    },
    "Entertainment": {
      icon: "FaFilm",
      color: "#a855f7",
      children: ["Movies", "Games", "Music", "Sports", "Hobbies", "Subscriptions"]
    },
    "Healthcare": {
      icon: "FaMedkit",
      color: "#14b8a6",
      children: ["Doctor", "Pharmacy", "Dental", "Vision", "Health Insurance", "Gym/Fitness"]
    },
    "Financial": {
      icon: "FaChartPie",
      color: "#f97316",
      children: ["Loan Payments", "Credit Card Payments", "Investments", "Savings", "Taxes", "Fees"]
    },
    "Education": {
      icon: "FaGraduationCap",
      color: "#0ea5e9",
      children: ["Tuition", "Books", "Courses", "Training", "School Supplies"]
    },
    "Travel": {
      icon: "FaPlane",
      color: "#84cc16",
      children: ["Flights", "Hotels", "Vacation", "Travel Insurance", "Activities"]
    },
    "Pets": {
      icon: "FaPaw",
      color: "#fbbf24",
      children: ["Pet Food", "Vet", "Pet Supplies", "Pet Insurance", "Grooming"]
    },
    "Others": {
      icon: "FaEllipsisH",
      color: "#6b7280",
      children: ["Miscellaneous", "Cash Withdrawal", "Unknown"]
    }
  }
};

const DEFAULT_ACCOUNTS = [
  { name: "Cash", type: "cash", icon: "FaWallet", color: "#10b981", balance: 0 },
  { name: "Checking Account", type: "checking", icon: "FaUniversity", color: "#3b82f6", balance: 0 },
  { name: "Savings Account", type: "savings", icon: "FaPiggyBank", color: "#f59e0b", balance: 0 }
];
```

### API Response Format Standards

```typescript
// Success Response
interface SuccessResponse<T> {
  success: true;
  data: T;
  meta?: {
    total?: number;
    page?: number;
    per_page?: number;
    total_pages?: number;
    timestamp: string;
    version: string;
  };
}

// Error Response
interface ErrorResponse {
  success: false;
  error: {
    code: string;         // e.g., "VALIDATION_ERROR"
    message: string;      // Human-readable message
    details?: any;        // Additional error details
    field?: string;       // For validation errors
  };
  meta: {
    timestamp: string;
    request_id: string;
  };
}

// Pagination Response
interface PaginatedResponse<T> {
  success: true;
  data: T[];
  pagination: {
    total: number;
    per_page: number;
    current_page: number;
    last_page: number;
    from: number;
    to: number;
  };
  links: {
    first: string;
    last: string;
    prev: string | null;
    next: string | null;
  };
}
```

### Amount Sign Convention

```typescript
// CRITICAL: Transaction amount convention
interface TransactionAmount {
  // ALWAYS use this convention:
  // - Negative values = Expenses
  // - Positive values = Income
  
  // Examples:
  // Salary: 5000.00 (positive)
  // Rent: -1500.00 (negative)
  // Groceries: -100.00 (negative)
  // Investment return: 250.00 (positive)
}

// Account balance calculation
function calculateBalance(account: Account, transactions: Transaction[]) {
  const balance = transactions.reduce((sum, tx) => {
    if (tx.type === 'expense' || tx.type === 'transfer_out') {
      return sum - Math.abs(tx.amount);
    } else if (tx.type === 'income' || tx.type === 'transfer_in') {
      return sum + Math.abs(tx.amount);
    }
    return sum;
  }, account.initial_balance);
  
  return balance;
}
```

### Transfer Implementation

```typescript
// CRITICAL: Transfers create TWO linked transactions
async function createTransfer(data: TransferInput) {
  return await prisma.$transaction(async (tx) => {
    // 1. Create transfer record
    const transfer = await tx.transfer.create({
      data: {
        user_id: data.user_id,
        from_account: data.from_account_id,
        to_account: data.to_account_id,
        amount: data.amount,
        date: data.date,
        description: data.description
      }
    });
    
    // 2. Create expense transaction (from account)
    await tx.transaction.create({
      data: {
        user_id: data.user_id,
        account_id: data.from_account_id,
        type: 'transfer_out',
        amount: -Math.abs(data.amount), // Negative
        transfer_id: transfer.id,
        date: data.date,
        description: `Transfer to ${toAccount.name}`
      }
    });
    
    // 3. Create income transaction (to account)
    await tx.transaction.create({
      data: {
        user_id: data.user_id,
        account_id: data.to_account_id,
        type: 'transfer_in',
        amount: Math.abs(data.to_amount || data.amount), // Positive
        transfer_id: transfer.id,
        date: data.date,
        description: `Transfer from ${fromAccount.name}`
      }
    });
    
    return transfer;
  });
}
```

### JWT Token Management

```typescript
// Token configuration
const TOKEN_CONFIG = {
  ACCESS_TOKEN_SECRET: process.env.JWT_ACCESS_SECRET!,
  REFRESH_TOKEN_SECRET: process.env.JWT_REFRESH_SECRET!,
  ACCESS_TOKEN_EXPIRY: '24h',
  REFRESH_TOKEN_EXPIRY: '7d',
};

// Token payload structure
interface TokenPayload {
  user_id: string;
  email: string;
  username: string;
  iat?: number;
  exp?: number;
}

// Token refresh implementation
async function refreshToken(refreshToken: string) {
  try {
    const payload = await verifyToken(refreshToken, TOKEN_CONFIG.REFRESH_TOKEN_SECRET);
    
    // Generate new token pair
    const newAccessToken = await generateToken(
      { user_id: payload.user_id, email: payload.email, username: payload.username },
      TOKEN_CONFIG.ACCESS_TOKEN_SECRET,
      TOKEN_CONFIG.ACCESS_TOKEN_EXPIRY
    );
    
    const newRefreshToken = await generateToken(
      { user_id: payload.user_id, email: payload.email, username: payload.username },
      TOKEN_CONFIG.REFRESH_TOKEN_SECRET,
      TOKEN_CONFIG.REFRESH_TOKEN_EXPIRY
    );
    
    return { accessToken: newAccessToken, refreshToken: newRefreshToken };
  } catch (error) {
    throw new Error('Invalid refresh token');
  }
}
```

---

## 🎨 UI/UX Requirements

### Design System

```scss
// Color Palette
$colors: (
  primary: #3b82f6,      // Blue
  secondary: #8b5cf6,    // Purple
  success: #10b981,      // Green
  warning: #f59e0b,      // Amber
  danger: #ef4444,       // Red
  info: #0ea5e9,         // Sky Blue
  
  // Income/Expense
  income: #10b981,       // Green
  expense: #ef4444,      // Red
  
  // Grays
  gray-50: #f9fafb,
  gray-100: #f3f4f6,
  gray-200: #e5e7eb,
  gray-300: #d1d5db,
  gray-400: #9ca3af,
  gray-500: #6b7280,
  gray-600: #4b5563,
  gray-700: #374151,
  gray-800: #1f2937,
  gray-900: #111827,
);

// Typography
$typography: (
  font-family: "'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif",
  
  // Headings
  h1: (size: 2.5rem, weight: 700, line-height: 1.2),
  h2: (size: 2rem, weight: 600, line-height: 1.3),
  h3: (size: 1.5rem, weight: 600, line-height: 1.4),
  h4: (size: 1.25rem, weight: 500, line-height: 1.4),
  h5: (size: 1rem, weight: 500, line-height: 1.5),
  
  // Body
  body-lg: (size: 1.125rem, weight: 400, line-height: 1.75),
  body: (size: 1rem, weight: 400, line-height: 1.5),
  body-sm: (size: 0.875rem, weight: 400, line-height: 1.5),
  
  // Special
  caption: (size: 0.75rem, weight: 400, line-height: 1.5),
  button: (size: 0.875rem, weight: 500, line-height: 1),
);

// Spacing
$spacing: (
  0: 0,
  1: 0.25rem,   // 4px
  2: 0.5rem,    // 8px
  3: 0.75rem,   // 12px
  4: 1rem,      // 16px
  5: 1.25rem,   // 20px
  6: 1.5rem,    // 24px
  8: 2rem,      // 32px
  10: 2.5rem,   // 40px
  12: 3rem,     // 48px
  16: 4rem,     // 64px
  20: 5rem,     // 80px
);

// Breakpoints
$breakpoints: (
  sm: 640px,
  md: 768px,
  lg: 1024px,
  xl: 1280px,
  2xl: 1536px,
);
```

### Component Library Requirements

```typescript
// Required Shadcn UI Components
const REQUIRED_COMPONENTS = [
  // Layout
  'Card', 'Separator', 'ScrollArea',
  
  // Forms
  'Form', 'Input', 'Label', 'Button', 'Select',
  'Checkbox', 'RadioGroup', 'Switch', 'Textarea',
  'DatePicker', 'Calendar',
  
  // Data Display
  'Table', 'DataTable', 'Badge', 'Avatar',
  
  // Feedback
  'Alert', 'Toast', 'Progress', 'Skeleton',
  'Dialog', 'AlertDialog', 'Sheet',
  
  // Navigation
  'Tabs', 'NavigationMenu', 'Breadcrumb',
  'DropdownMenu', 'ContextMenu', 'CommandMenu',
  
  // Others
  'Tooltip', 'Popover', 'Collapsible', 'Accordion'
];
```

### Responsive Design Requirements

```css
/* Mobile First Approach */
/* Default: Mobile (< 640px) */
.container {
  padding: 1rem;
  max-width: 100%;
}

/* Tablet (640px - 1024px) */
@media (min-width: 640px) {
  .container {
    padding: 1.5rem;
    max-width: 640px;
  }
}

/* Desktop (1024px+) */
@media (min-width: 1024px) {
  .container {
    padding: 2rem;
    max-width: 1280px;
  }
  
  /* Sidebar visible on desktop */
  .sidebar {
    display: block;
    width: 280px;
  }
}
```

---

## ⚡ Performance Requirements

### Core Web Vitals Targets

```javascript
// Performance metrics targets
const PERFORMANCE_TARGETS = {
  // Core Web Vitals
  LCP: 2.5,     // Largest Contentful Paint < 2.5s
  FID: 100,     // First Input Delay < 100ms
  CLS: 0.1,     // Cumulative Layout Shift < 0.1
  
  // Additional metrics
  FCP: 1.8,     // First Contentful Paint < 1.8s
  TTFB: 600,    // Time to First Byte < 600ms
  TTI: 3.8,     // Time to Interactive < 3.8s
  
  // Custom metrics
  API_RESPONSE_P95: 200,    // 95th percentile < 200ms
  DB_QUERY_P95: 100,        // 95th percentile < 100ms
  BUNDLE_SIZE: 200,         // Initial JS < 200KB
};
```

### Optimization Checklist

```markdown
□ Code Splitting
  - Route-based splitting
  - Component lazy loading
  - Dynamic imports for heavy libraries
  
□ Image Optimization
  - Next.js Image component
  - WebP format
  - Responsive images
  - Lazy loading
  
□ Caching Strategy
  - API response caching
  - Static asset caching
  - Database query caching
  - CDN implementation
  
□ Database Optimization
  - Proper indexing
  - Query optimization
  - Connection pooling
  - Read replicas
  
□ Bundle Optimization
  - Tree shaking
  - Minification
  - Compression (gzip/brotli)
  - Source map optimization
```

---

## 🚨 Common Pitfalls to Avoid

### 1. Database Design Mistakes
```typescript
// ❌ WRONG: Storing amounts as strings
amount: "1000.50"

// ✅ CORRECT: Use DECIMAL type
amount: DECIMAL(15,2)

// ❌ WRONG: Not handling multi-currency
amount: 1000

// ✅ CORRECT: Store currency and exchange rate
amount: 1000,
currency: 'USD',
exchange_rate: 1.0
```

### 2. API Design Mistakes
```typescript
// ❌ WRONG: Inconsistent response format
return { user: userData }
return { data: { accounts: [...] } }

// ✅ CORRECT: Consistent format
return { success: true, data: userData }
return { success: true, data: accounts }

// ❌ WRONG: No pagination
GET /api/v1/transactions // Returns all transactions

// ✅ CORRECT: Always paginate
GET /api/v1/transactions?page=1&limit=50
```

### 3. Authentication Mistakes
```typescript
// ❌ WRONG: Storing plain passwords
password: userInput.password

// ✅ CORRECT: Hash passwords
password: await bcrypt.hash(userInput.password, 10)

// ❌ WRONG: No token refresh
// Access token expires and user logged out

// ✅ CORRECT: Implement refresh token flow
// Automatically refresh before expiry
```

### 4. State Management Mistakes
```typescript
// ❌ WRONG: Prop drilling
<Parent user={user}>
  <Child user={user}>
    <GrandChild user={user}>

// ✅ CORRECT: Use context or state management
const user = useAuthStore(state => state.user)

// ❌ WRONG: Not handling loading states
const { data } = useQuery(...)
return <div>{data.map(...)}</div>

// ✅ CORRECT: Handle all states
const { data, isLoading, error } = useQuery(...)
if (isLoading) return <Skeleton />
if (error) return <Error />
return <div>{data.map(...)}</div>
```

---

## 📊 Success Metrics

### Technical Metrics
- [ ] 80% test coverage
- [ ] < 2s page load time
- [ ] < 200ms API response time
- [ ] Zero critical security vulnerabilities
- [ ] 99.9% uptime

### User Experience Metrics
- [ ] < 3 clicks to add transaction
- [ ] < 5 seconds to see analytics
- [ ] Mobile-responsive on all screens
- [ ] Accessible (WCAG 2.1 AA)
- [ ] Works offline (basic features)

### Business Metrics
- [ ] Support 10,000+ concurrent users
- [ ] Handle 1M+ transactions per user
- [ ] Data export in < 30 seconds
- [ ] 90% user satisfaction score

---

## 🔗 Required Integrations

### Essential Integrations
```yaml
Payment Processing:
  - Stripe/PayPal for premium features
  
Email Service:
  - SendGrid/AWS SES for notifications
  
File Storage:
  - AWS S3/Cloudinary for attachments
  
Authentication:
  - OAuth providers (Google, Apple, Microsoft)
  
Analytics:
  - Google Analytics/Mixpanel
  
Error Tracking:
  - Sentry for error monitoring
  
Monitoring:
  - DataDog/New Relic for APM
```

### Optional Integrations
```yaml
Bank Connections:
  - Plaid for bank account linking
  - Yodlee for transaction import
  
Currency Exchange:
  - Fixer.io/ExchangeRate-API
  
Tax Services:
  - TurboTax/H&R Block export
  
Backup:
  - Google Drive/Dropbox sync
  
AI Features:
  - OpenAI for transaction categorization
  - Claude for financial insights
```

---

## 🎯 Final Delivery Checklist

### Code Quality
- [ ] TypeScript strict mode enabled
- [ ] ESLint no errors
- [ ] Prettier formatted
- [ ] No console.logs in production
- [ ] No hardcoded secrets

### Documentation
- [ ] README with setup instructions
- [ ] API documentation (OpenAPI/Swagger)
- [ ] Component Storybook
- [ ] Architecture diagrams
- [ ] Database schema documentation

### Testing
- [ ] Unit tests passing
- [ ] Integration tests passing
- [ ] E2E tests for critical flows
- [ ] Performance tests passing
- [ ] Security scan completed

### Deployment
- [ ] Environment variables configured
- [ ] Database migrations ready
- [ ] CI/CD pipeline configured
- [ ] Monitoring setup
- [ ] Backup strategy implemented

### Features
- [ ] All CRUD operations working
- [ ] Authentication fully functional
- [ ] Data import/export working
- [ ] Analytics displaying correctly
- [ ] Mobile responsive

---

## 💡 Pro Tips for Implementation

1. **Start with Authentication**: Get auth working first as everything depends on it
2. **Use Database Transactions**: Ensure data consistency for financial operations
3. **Implement Audit Logging Early**: Track all changes from the beginning
4. **Test with Real Data**: Use realistic data volumes in testing
5. **Consider Time Zones**: Store in UTC, display in user's timezone
6. **Plan for Scale**: Design for 10x current requirements
7. **Security First**: Never trust client input, validate everything
8. **Make it Fast**: Users expect instant response for financial data
9. **Keep it Simple**: Start with MVP, iterate based on feedback
10. **Document Everything**: Your future self will thank you

---

## 🏁 Implementation Timeline

### Week 1-2: Foundation
- Project setup
- Database schema
- Authentication system
- Basic API structure

### Week 3-4: Core Features
- Account management
- Transaction CRUD
- Category management
- Basic UI components

### Week 5-6: Advanced Features
- Analytics & reports
- Budgets
- Data import/export
- Testing & optimization

### Week 7-8: Polish & Deploy
- UI polish
- Performance optimization
- Security hardening
- Production deployment

---

## 🆘 Support & Resources

### Documentation
- [Next.js 15 Docs](https://nextjs.org/docs)
- [Prisma Documentation](https://www.prisma.io/docs)
- [Shadcn UI Components](https://ui.shadcn.com)
- [TanStack Query](https://tanstack.com/query)
- [Zustand State Management](https://zustand-demo.pmnd.rs)

### Communities
- Next.js Discord
- Prisma Slack
- Stack Overflow
- Reddit r/nextjs

This implementation prompt provides everything needed to build a production-ready personal finance management application. Follow the phases sequentially, implement all critical details, and refer to the common pitfalls section to avoid mistakes. Good luck! 🚀
