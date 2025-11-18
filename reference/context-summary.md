# Context Summary (`src/context/`)

## 🎯 **Purpose**
React Context providers for global state management across the finance application. 4 context providers handle authentication, notifications, and transaction operations.

## 📁 **Context Architecture**

```
Context Hierarchy (Provider Nesting Order):
├── ToastProvider              (Level 1 - Base notifications)
│   └── AuthStateProvider      (Level 2 - Auth state)
│       └── AuthProvider       (Level 3 - Auth logic)
│           └── TransactionModalProvider  (Level 4 - Transaction CRUD)
```

---

## 🔐 **1. AuthStateContext** (`AuthStateContext.tsx`)

### **Purpose**
Lightweight authentication state container separated from business logic for performance optimization.

### **Interface**
```typescript
interface AuthStateContextValue {
  isAuthenticated: boolean;
  setIsAuthenticated: (value: boolean) => void;
  loading: boolean;
  setLoading: (value: boolean) => void;
}
```

### **Key Features**
- **Pure state management** - No side effects or API calls
- **Performance optimized** with `useMemo` to prevent unnecessary re-renders
- **Simple boolean flags** for authentication status and loading state
- **Used by AuthProvider** as the state layer

### **Usage Pattern**
```typescript
const { isAuthenticated, setIsAuthenticated, loading, setLoading } = useAuthState();
```

---

## 🔑 **2. AuthContext** (`AuthContext.tsx`)

### **Purpose**
Complete authentication system with JWT token management, encryption, and API integration.

### **Interface**
```typescript
interface AuthContextValue {
  isAuthenticated: boolean;
  loading: boolean;
  login: (response: LoginResult) => Promise<void>;
  logout: () => Promise<void>;
}
```

### **Key Features**

#### **Token Management**
```typescript
// Encrypted localStorage storage
const AUTH_TOKEN_KEY = APP_CONFIG.storageKeys.authToken;
const REFRESH_TOKEN_KEY = APP_CONFIG.storageKeys.refreshToken;
const USER_DATA_KEY = APP_CONFIG.storageKeys.userData;

// Token encryption/decryption
await tokenCrypto.encryptToken(token);
await tokenCrypto.decryptToken(encryptedToken);
```

#### **Login Flow**
```typescript
login(response: LoginResult) → {
  1. Extract tokens from response (string or object format)
  2. Encrypt tokens using AES-GCM
  3. Store in localStorage
  4. Update authentication state
  5. Show success toast
  6. Handle errors with user feedback
}
```

#### **Logout Flow**
```typescript
logout() → {
  1. Call authService.logout() API
  2. Clear localStorage (tokens, user data, crypto keys)
  3. Update authentication state
  4. Show success/error toast
  5. Handle API errors gracefully
}
```

#### **Auto-Authentication Check**
```typescript
useEffect(() => {
  // On app startup
  1. Check localStorage for encrypted token
  2. Decrypt and validate token
  3. Set authentication state
  4. Handle errors silently
}, []);
```

### **Dependencies**
- **AuthStateContext** - For state management
- **ToastContext** - For user notifications
- **tokenCrypto** - For token encryption/decryption
- **authService** - For logout API calls
- **APP_CONFIG** - For storage key configuration

### **Error Handling**
```typescript
// Comprehensive error message extraction
extractLogoutErrorMessage(error) → {
  - String errors → Direct message
  - Error objects → error.message
  - API responses → response.data.message
  - Fallback → "Logout failed. Please try again."
}
```

---

## 🔔 **3. ToastContext** (`ToastContext.tsx`)

### **Purpose**
Global toast notification system for user feedback across the application.

### **Interface**
```typescript
interface ToastContextValue {
  showToast: (message: string, severity?: 'success' | 'error') => void;
}
```

### **Key Features**

#### **Simple API**
```typescript
const { showToast } = useToast();
showToast('Operation successful!', 'success');
showToast('Something went wrong', 'error');
```

#### **Toast Configuration**
```typescript
<ToastAlert
  open={toastState.open}
  onClose={handleCloseToast}
  severity={toastState.severity}
  message={toastState.message}
  autoHideDuration={3000}
  anchorOrigin={{ vertical: 'bottom', horizontal: 'center' }}
/>
```

#### **State Management**
```typescript
interface ToastState {
  open: boolean;
  message: string;
  severity: 'success' | 'error';
}
```

### **Behavior**
- **Auto-hide**: Disappears after 3 seconds
- **Click-away prevention**: Doesn't close on outside clicks
- **Queue support**: Single toast at a time (can be extended)
- **Position**: Bottom center of screen

---

## 🏦 **4. TransactionModalContext** (`TransactionModalContext.tsx`)

### **Purpose**
Comprehensive transaction management system handling CRUD operations, modal state, and data integration. **Most complex context** (1379 lines).

### **Interface**
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

### **Key Features**

#### **1. Modal State Management**
```typescript
// Multiple modal states
const [showTransactionModal, setShowTransactionModal] = useState(false);
const [showQuickTransactionModal, setShowQuickTransactionModal] = useState(false);
const [isEditingTransaction, setIsEditingTransaction] = useState(false);
const [currentTransaction, setCurrentTransaction] = useState(createTransactionTemplate());
```

#### **2. Account Data Management**
```typescript
// Account integration
const [apiAccounts, setApiAccounts] = useState<ApiAccountResponse[]>([]);

// Bidirectional mappings
accountIdToName: Record<string, string>  // UUID → "Account Name"
accountNameToId: Record<string, string>  // "Account Name" → UUID

// Fetches accounts on authentication
useEffect(() => {
  if (isAuthenticated && !authLoading) {
    accountService.fetchAccounts().then(setApiAccounts);
  }
}, [isAuthenticated, authLoading]);
```

#### **3. Category Management**
```typescript
// Uses useCategoryData hook
const {
  categories,
  categoryTree,
  parentCategoryColors,
  categoryIcons,
  allCategories,
  addCategory,
} = useCategoryData();

// Dynamic category creation
ensureCategoryExists(categoryName) → ApiCategoryResponse | undefined
```

#### **4. Quick Transaction System**
```typescript
// Template management
const { quickTransactions, addQuickTransactionPreset } = useQuickTransactions(categories);

// Template creation
createQuickTransactionTemplate(overrides) → QuickTransactionFormValues
```

#### **5. Personal ID Management** 
```typescript
// Client-side incrementing (legacy - being migrated to server-side)
const [maxPersonalId, setMaxPersonalId] = useState(() => {
  const cached = localStorage.getItem('max_transaction_personal_id');
  return parseInt(cached) || 0;
});

// Conflict retry logic
// Catches personal_id conflicts (409 errors)
// Retries up to 3 times with incremented ID
// Updates localStorage after success
```

#### **6. Transaction CRUD Operations**

##### **Create Transaction**
```typescript
handleSaveTransaction(createAnother, context) → {
  1. Validate form fields (amount, account, category)
  2. Resolve category ID from name
  3. Convert amount (negative for expenses)
  4. Assign personal_id with conflict retry
  5. Call transactionService.createTransaction()
  6. Dispatch 'transaction-created' event
  7. Optionally create quick preset template
  8. Show success toast or handle errors
}
```

##### **Update Transaction**
```typescript
editTransaction(transaction) → {
  1. Pre-fill modal with transaction data
  2. Set isEditingTransaction = true
  3. Open modal for editing
  4. On save: call transactionService.updateTransaction()
  5. Dispatch 'transaction-updated' event
}
```

##### **Create Transfer**
```typescript
// Special handling for TRANSFER type
handleSaveTransaction(type === 'TRANSFER') → {
  1. Create two transactions (withdrawal + deposit)
  2. Handle currency conversion with to_amount
  3. POST /api/v1/transfers
  4. Link transactions via transfer_id
}
```

#### **7. Form State Management**
```typescript
// Complex form state
interface TransactionFormValues {
  personal_id?: number;
  templateId: string;
  transactionId?: string;
  type: 'INCOME' | 'EXPENSE' | 'TRANSFER';
  description: string;
  amount: string;
  currency: string;
  date: string;
  dateTime: string;
  category: string;
  categoryId: string;
  accountId: string;
  toAccountId: string;      // For transfers
  toAmount: string;         // For currency conversion
  toCurrency: string;
  labels: LabelOption[];
  labelIds: string[];
  selectedLabelNames: string[];
  createTemplate: boolean;
  note: string;
  payer: string;
  paymentType: PaymentType;
  paymentStatus: PaymentStatus;
}
```

#### **8. Event System**
```typescript
// Cross-component communication
window.dispatchEvent(new CustomEvent('transaction-created', {
  detail: { transaction, isTransfer }
}));

window.dispatchEvent(new CustomEvent('transaction-updated', {
  detail: { transactionId, transaction }
}));
```

#### **9. Validation System**
```typescript
// Schema-based validation
const [validationErrors, setValidationErrors] = useState<Record<string, string>>({});

// Required fields validation
validateTransactionInput() → {
  - amount: Must be provided and numeric
  - accountId: Required
  - category: Required for non-transfers
  - toAccountId: Required for transfers
}
```

#### **10. Template System**
```typescript
// Transaction templates
createTransactionTemplate(overrides) → {
  1. Generate default form values
  2. Set current date/time
  3. Apply any overrides
  4. Return complete form object
}

// Template selection
handleTemplateSelect(templateId) → {
  1. Load quick transaction preset
  2. Pre-fill form with saved values
  3. Convert account name to ID
  4. Open modal with populated data
}
```

### **Complex Dependencies**
- **useCategoryData** - Category management hook
- **useQuickTransactions** - Template system hook
- **accountService** - Account API operations
- **transactionService** - Transaction API operations
- **categoryService** - Category API operations
- **useAuth** - Authentication state
- **Multiple utility functions** - Date formatting, validation, error handling

### **Performance Considerations**
```typescript
// Heavy state management (10+ useState calls)
// Complex useEffect dependencies
// API calls with error handling
// Event system for cross-component updates
// localStorage integration for persistence
```

---

## 🔄 **Context Interaction Patterns**

### **Authentication Flow**
```
1. App loads → AuthStateProvider initializes
2. AuthProvider checks localStorage for tokens
3. Decrypt tokens → Set authentication state
4. TransactionModalProvider activates on auth success
5. Components subscribe to auth state changes
```

### **Transaction Creation Flow**
```
1. User triggers openTransactionModal()
2. TransactionModalContext creates form template
3. User fills form → handleTransactionChange updates state
4. User saves → handleSaveTransaction validates & calls API
5. Success → Dispatch event → Other components refresh
6. Show toast notification → Close modal
```

### **Error Handling Flow**
```
1. API error occurs in any context
2. Extract user-friendly error message
3. Call showToast(message, 'error')
4. ToastContext displays notification
5. User sees consistent error feedback
```

### **Logout Flow**
```
1. User clicks logout in Header
2. AuthContext.logout() called
3. API logout call + localStorage cleanup
4. AuthStateContext updated
5. All dependent contexts reset
6. User redirected to login
```

---

## 📊 **Context Complexity Analysis**

| Context | Lines | State Pieces | API Calls | Complexity |
|---------|--------|-------------|-----------|------------|
| **ToastContext** | 67 | 1 | 0 | ⭐ Low |
| **AuthStateContext** | 49 | 2 | 0 | ⭐ Low |
| **AuthContext** | 246 | 0* | 1 | ⭐⭐⭐ Medium |
| **TransactionModalContext** | 1379 | 10+ | 5+ | ⭐⭐⭐⭐⭐ High |

*AuthContext uses AuthStateContext for state

---

## 🎯 **Key Implementation Patterns**

### **1. Context Composition**
```typescript
// Nested provider pattern
<ToastProvider>
  <AuthStateProvider>
    <AuthProvider>
      <TransactionModalProvider>
        {children}
      </TransactionModalProvider>
    </AuthProvider>
  </AuthStateProvider>
</ToastProvider>
```

### **2. Hook-based Access**
```typescript
// Each context provides a custom hook
const { showToast } = useToast();
const { isAuthenticated } = useAuth();
const { openTransactionModal } = useTransactionModal();
```

### **3. Error Boundaries**
```typescript
// All contexts throw errors for missing providers
if (!context) {
  throw new Error('useAuth must be used within an AuthProvider');
}
```

### **4. Type Safety**
```typescript
// Strong TypeScript interfaces for all context values
interface AuthContextValue {
  isAuthenticated: boolean;
  login: (response: LoginResult) => Promise<void>;
  // ... fully typed
}
```

### **5. Performance Optimization**
```typescript
// Strategic use of useMemo and useCallback
const value = useMemo(() => ({ ... }), [dependencies]);
const handleSave = useCallback(() => { ... }, [deps]);
```

---

## ⚠️ **Critical Implementation Notes**

### **1. Provider Order Matters**
- ToastContext must be outermost (for error notifications)
- AuthStateContext before AuthContext (state dependency)
- TransactionModalContext requires authentication

### **2. TransactionModalContext Complexity**
- **Most complex component** in entire application
- Handles 10+ state pieces simultaneously
- Integrates with 3+ API services
- Manages real-time event system
- Consider breaking into smaller contexts for rewrite

### **3. Personal ID Management Migration**
- Currently client-side localStorage caching
- Being migrated to server-side API approach
- Conflict retry logic critical for data integrity

### **4. Token Security**
- All tokens encrypted before localStorage storage
- AES-GCM encryption with tokenCrypto utility
- Automatic cleanup on logout
- Configuration via APP_CONFIG

### **5. Event System**
- Custom DOM events for cross-component communication
- 'transaction-created' and 'transaction-updated' events
- Other components listen for real-time updates

---

## 🚀 **Rewrite Recommendations**

### **1. Context Splitting**
```typescript
// Consider splitting TransactionModalContext into:
- TransactionModalContext (UI state only)
- TransactionDataContext (API operations)
- TransactionFormContext (form management)
- QuickTransactionContext (template system)
```

### **2. State Management Library**
```typescript
// For TransactionModal complexity, consider:
- Redux Toolkit (for complex state)
- Zustand (for lighter state management)  
- React Query (for API state)
```

### **3. Performance Optimization**
```typescript
// Optimize context performance:
- Selective context subscriptions
- Memoized context values
- Reduced re-render frequency
- Virtual scrolling for large lists
```

### **4. Error Handling Enhancement**
```typescript
// Centralized error handling:
- Error boundary integration
- Structured error logging
- User-friendly error messages
- Retry mechanisms
```

### **5. Testing Strategy**
```typescript
// Context testing priorities:
1. AuthContext - Login/logout flows
2. TransactionModalContext - CRUD operations  
3. ToastContext - Notification display
4. Integration - Cross-context communication
```

---

## 📋 **Context Status Summary**

✅ **ToastContext** - Simple, well-structured notification system  
✅ **AuthStateContext** - Clean state container with performance optimization  
✅ **AuthContext** - Secure JWT token management with encryption  
⚠️ **TransactionModalContext** - **High complexity**, needs careful rewrite consideration  

**Total Contexts**: 4  
**High Complexity**: 1 (TransactionModalContext)  
**Medium Complexity**: 1 (AuthContext)  
**Low Complexity**: 2 (ToastContext, AuthStateContext)  

**All contexts are production-ready** with proper error handling, TypeScript typing, and security considerations. The TransactionModalContext requires extra attention due to its complexity and multiple responsibilities.
