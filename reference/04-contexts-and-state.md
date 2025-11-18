# Contexts and State Management

## Overview
The application uses React Context API for global state management. Contexts are nested in a specific order in `app/providers.tsx`.

## Context Hierarchy

```
<ToastProvider>                      (Level 1)
  <AuthStateProvider>                (Level 2)
    <AuthProvider>                   (Level 3)
      <TransactionModalProvider>     (Level 4)
        {children}
      </TransactionModalProvider>
    </AuthProvider>
  </AuthStateProvider>
</ToastProvider>
```

---

## 1. ToastContext (`src/context/ToastContext.tsx`)

### Purpose
Global notification system for user feedback.

### Provides
```typescript
interface ToastContextValue {
  showToast: (message: string, type: 'success' | 'error' | 'info' | 'warning') => void;
}
```

### Usage
```typescript
const { showToast } = useToast();
showToast('Transaction created successfully!', 'success');
```

### Implementation
- No dependencies on other contexts
- Internal state for toast queue
- Auto-dismiss after timeout
- Stacks multiple toasts

### Component
`ToastAlert` renders the visual notifications

---

## 2. AuthStateContext (`src/context/AuthStateContext.tsx`)

### Purpose
Manages authentication loading state separately for better separation of concerns.

### Provides
```typescript
interface AuthStateContextValue {
  isAuthenticated: boolean;
  setIsAuthenticated: (value: boolean) => void;
  loading: boolean;
  setLoading: (value: boolean) => void;
}
```

### Usage
```typescript
const { isAuthenticated, loading, setIsAuthenticated, setLoading } = useAuthState();
```

### Implementation
- Simple state container
- No side effects
- Used by AuthProvider

---

## 3. AuthContext (`src/context/AuthContext.tsx`)

### Purpose
Handles authentication logic including login, logout, and token management.

### Provides
```typescript
interface AuthContextValue {
  isAuthenticated: boolean;
  loading: boolean;
  login: (response: LoginResult) => Promise<void>;
  logout: () => Promise<void>;
}
```

### Dependencies
- AuthStateContext (for state)
- ToastContext (for notifications)

### Token Management

**Encryption**:
- Tokens encrypted before localStorage storage
- Uses `tokenCrypto` utility (AES encryption)
- Keys: `APP_CONFIG.storageKeys.authToken` and `refreshToken`

**Storage Keys**:
```typescript
AUTH_TOKEN_KEY: 'finance-app-auth-token'
REFRESH_TOKEN_KEY: 'finance-app-refresh-token'
USER_DATA_KEY: 'finance-app-user-data'
```

### Login Flow
```
1. login(response) called with JWT tokens
2. Tokens encrypted
3. Stored in localStorage
4. setIsAuthenticated(true)
5. Success toast shown
```

### Logout Flow
```
1. logout() called
2. API call to /api/v1/auth/logout
3. Remove all localStorage keys
4. Clear crypto key
5. setIsAuthenticated(false)
6. Success/error toast shown
```

### Auto-Authentication Check
```typescript
useEffect(() => {
  const checkAuthentication = async () => {
    const encryptedToken = localStorage.getItem(AUTH_TOKEN_KEY);
    if (encryptedToken) {
      const decryptedToken = await tokenCrypto.decryptToken(encryptedToken);
      setIsAuthenticated(!!decryptedToken);
    }
  };
  checkAuthentication();
}, []);
```

---

## 4. TransactionModalContext (`src/context/TransactionModalContext.tsx`)

### Purpose
Centralized management of transaction modal operations and CRUD logic.

### Provides
```typescript
interface TransactionModalContextValue {
  openTransactionModal: (overrides?: Partial<TransactionFormValues>) => void;
  editTransaction: (transaction: TransactionFormValues) => void;
  closeTransactionModal: () => void;
  openQuickTransactionModal: () => void;
  setTransactionDefaults: (overrides?: Partial<TransactionFormValues>) => void;
  transactions: TransactionRecord[];
  accountIdToName: Record<string, string>;
  accountNameToId: Record<string, string>;
}
```

### Key Features

#### 1. Modal State Management
- `showTransactionModal` - Main transaction modal visibility
- `showQuickTransactionModal` - Quick preset modal visibility
- `isEditMode` - Differentiates create vs update
- `currentTransaction` - Form state

#### 2. Account Data
**Fetches accounts on mount**:
```typescript
useEffect(() => {
  if (isAuthenticated && !authLoading) {
    accountService.fetchAccounts().then(setApiAccounts);
  }
}, [isAuthenticated, authLoading]);
```

**Provides mappings**:
- `accountIdToName` - For displaying account names
- `accountNameToId` - For reverse lookup
- `selectableAccounts` - Array of account names (backward compatibility)

#### 3. Category Management
**Uses** `useCategoryData` hook:
```typescript
const {
  categories,
  categoryTree,
  parentCategoryColors,
  categoryIcons,
  allCategories,
  addCategory,
} = useCategoryData();
```

**Dynamic Category Creation**:
```typescript
ensureCategoryExists(categoryName) → ApiCategoryResponse | undefined
```
- Searches existing categories
- Fetches from API if not found

#### 4. Quick Transaction Presets
**Uses** `useQuickTransactions` hook:
```typescript
const { quickTransactions, addQuickTransactionPreset } = useQuickTransactions(categories);
```

- Stores frequently used transaction templates
- Pre-fills modal with saved values
- Persisted in localStorage

#### 5. Personal ID Management
**Server-side fetching**:
```typescript
const [maxPersonalIds, setMaxPersonalIds] = useState<Record<string, number>>({});

useEffect(() => {
  if (isAuthenticated) {
    // Fetch current max personal IDs from server
    api.get('/api/v1/personal-ids/max').then(setMaxPersonalIds);
  }
}, [isAuthenticated]);
```

**API Endpoint**: `GET /api/v1/personal-ids/max`
```typescript
Response: {
  transactions: 123,
  accounts: 45,
  categories: 67,
  labels: 12,
  // ... other tables
}
```

**Conflict Retry Logic**:
- Catches personal_id conflicts (409 errors)
- Fetches fresh max personal IDs from server
- Retries up to 3 times with incremented ID
- No localStorage management needed

#### 6. Transaction CRUD Operations

**Create Transaction**:
```typescript
handleSaveTransaction(createAnother, context?)
```
- Validates form fields
- Resolves category ID
- Converts amount (negative for expenses)
- Calls `transactionService.createTransaction()`
- Retry logic for personal_id conflicts
- Dispatches `'transaction-created'` event
- Optionally creates quick preset template

**Update Transaction**:
```typescript
editTransaction(transaction)
```
- Pre-fills modal with transaction data
- Sets `isEditMode = true`
- Calls `transactionService.updateTransaction()`
- Dispatches `'transaction-updated'` event

**Create Transfer**:
```typescript
// Inside handleSaveTransaction when type === 'TRANSFER'
POST /api/v1/transfers
```
- Creates two transactions (withdrawal + deposit)
- Handles currency conversion with `to_amount`

#### 7. Form State Management

**Template Creation**:
```typescript
createTransactionTemplate(overrides?) → TransactionFormValues
```
- Default values for all fields
- Current date/time
- Personal ID assignment
- Optional overrides

**Change Handler**:
```typescript
handleTransactionChange(event: TransactionChangeEvent)
```
- Updates `currentTransaction` state
- Clears validation errors
- Special handling for `dateTime` and `note` fields

**Template Selection**:
```typescript
handleTemplateSelect(templateId)
```
- Loads quick transaction preset
- Pre-fills form with saved values
- Converts account name to ID

#### 8. Validation
```typescript
const [validationErrors, setValidationErrors] = useState<Record<string, string>>({});
```

**Required Fields**:
- `amount` - Must be provided
- `accountId` - Required
- `category` - Required for non-transfers
- `toAccountId` - Required for transfers

**Validation Display**:
- Passed to `TransactionModal` as prop
- Shown under form fields
- Cleared on field change

#### 9. Event System

**Dispatched Events**:
```typescript
window.dispatchEvent(new CustomEvent('transaction-created', {
  detail: { transaction, isTransfer }
}));

window.dispatchEvent(new CustomEvent('transaction-updated', {
  detail: { transactionId, transaction }
}));
```

**Purpose**: Notify other components (e.g., Transactions page) to refresh

---

## Context Usage Patterns

### 1. Nested Hooks
```typescript
// Inside a component
const { showToast } = useToast();
const { isAuthenticated } = useAuth();
const { openTransactionModal } = useTransactionModal();
```

### 2. Conditional Rendering
```typescript
const { loading, isAuthenticated } = useAuth();

if (loading) return <Spinner />;
if (!isAuthenticated) return <Redirect to="/login" />;
return <ProtectedContent />;
```

### 3. Cross-Component Communication
```typescript
// Component A
const { openTransactionModal } = useTransactionModal();
openTransactionModal({ type: 'EXPENSE', categoryId: '123' });

// Component B (modal rendered)
// Modal opens with pre-filled values
```

---

## Key Considerations for Rewrite

1. **Provider Order**: Must maintain dependency hierarchy
2. **Token Security**: Encryption required for localStorage
3. **Personal ID Conflicts**: Implement retry logic with max attempts
4. **Category Resolution**: Dynamic category creation must handle API failures
5. **Event System**: Custom events for cross-component updates
6. **Account Mappings**: Maintain ID ↔ Name bidirectional mappings
7. **Form Validation**: Clear errors on field change, show under inputs
8. **Loading States**: Prevent fetching if auth not ready
9. **LocalStorage Keys**: Use APP_CONFIG constants
10. **Type Safety**: Strict TypeScript types for all context values
