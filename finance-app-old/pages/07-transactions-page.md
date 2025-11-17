# Transactions Page

## Routes
- **URL**: `/transactions`
- **File**: `app/transactions/page.tsx` → `src/views/Transactions/Transactions.tsx`

## Purpose
Core transaction management page with advanced filtering, CRUD operations, and bulk actions.

## Page Structure

```typescript
app/transactions/page.tsx (wrapper)
  └─ ProtectedShell
      └─ Suspense
          └─ LazyTransactions (src/views/Transactions/Transactions.tsx)
              └─ PeriodNavigationProvider
                  └─ TransactionsContent
```

## ⭐ Key Features (Comprehensive)

### 1. **Two-Column Responsive Layout**

**Desktop (lg+)**:
```
┌─────────────────────────────────────────────┐
│ [Filter Sidebar]  [Period Nav]              │
│ - Search          [Transaction List]        │
│ - Sort            - Grouped by date         │
│ - Categories      - Bulk actions            │
│ - Accounts        - Individual actions      │
│ - Amount Range    - Selection checkboxes    │
│ - Add Button                                │
└─────────────────────────────────────────────┘
```

**Mobile**:
```
┌─────────────────────────────────────────────┐
│ [Filter] [Add] (buttons)                    │
│ [Period Navigation]                         │
│ [Transaction List]                          │
│ - Touch-friendly                            │
│ - Filter in offcanvas                       │
│ - Swipe gestures                           │
└─────────────────────────────────────────────┘
```

---

### 2. **Advanced Filtering System**

#### Filter Components

**Desktop**: `DesktopFilterSidebar`
**Mobile**: `MobileFilterOffcanvas`

**Filter Types**:

1. **Search Term**
   - Real-time search in transaction descriptions
   - Debounced API calls
   - Highlights search results

2. **Sort Options**
   - Date (newest/oldest first)
   - Amount (highest/lowest first)
   - Category (A-Z)
   - Account (A-Z)

3. **Category Filter**
   - Multi-select dropdown
   - Hierarchical display (parent/child)
   - Color-coded categories
   - "Select All" option

4. **Account Filter**
   - Multi-select dropdown
   - Shows account icons and colors
   - Balance display
   - Active/inactive status

5. **Amount Range**
   - Min/max sliders or inputs
   - Currency formatting
   - Default range: 0 to 20M IDR

6. **Period Filter** (via PeriodNavigation)
   - Today, This Week, This Month, This Year
   - Custom date range picker
   - Quick period buttons

#### Filter State Management

**Hook**: `useFilterData()` (shared with Analytics)

**Persistence**:
- Filter visibility: localStorage
- Filter values: URL params (future enhancement)

**Real-time Updates**:
- Debounced search (300ms)
- Immediate filter application
- Loading states during fetch

---

### 3. **Transaction Data Pipeline**

#### API to UI Transformation

**Source**: `ApiTransactionResponse[]`
**Target**: `TransactionRecord[]` → `GroupedTransactions`

**Transformation Steps**:
```typescript
1. fetchTransactionsFromServer()
   ↓
2. transactionService.fetchTransactions()
   ↓
3. mapApiTransactions()
   ↓
4. Group by date
   ↓
5. Render in RecordsList
```

**Data Mapping**: `mapApiTransactions()`
- ✅ Date/time handling (multiple formats)
- ✅ Category resolution (ID → name, icon, color)
- ✅ Account resolution (ID → name)
- ✅ Amount normalization (signed numbers)
- ✅ Label parsing (array of label objects)
- ✅ Type normalization (INCOME/EXPENSE/TRANSFER)
- ✅ Currency formatting
- ✅ Personal ID extraction

#### Grouping Logic

**Function**: Creates `GroupedTransactions` object
```typescript
{
  "November 15, 2024": [
    { id: "1", description: "Groceries", amount: 150, ... },
    { id: "2", description: "Coffee", amount: 45, ... }
  ],
  "November 14, 2024": [
    { id: "3", description: "Salary", amount: 5000, ... }
  ]
}
```

**Features**:
- Groups by formatted date (e.g., "November 15, 2024")
- Sorts chronologically (newest first)
- Time stamps for individual transactions
- Handles invalid dates gracefully

---

### 4. **Transaction CRUD Operations**

#### Create Transaction

**Modal**: `TransactionModal`

**Flow**:
```
1. Click "Add Transaction" or URL param ?openTransactionModal
   ↓
2. Modal opens with empty form
   ↓
3. User fills required fields (amount, account, category)
   ↓
4. handleSaveTransaction()
   ↓
5. Validation → Metadata resolution → API call
   ↓
6. Success: Refresh list, close modal
   ↓
7. Error: Show SweetAlert2 error
```

**Validation**:
- Amount required (non-zero)
- Account required (valid ID)
- Category required (non-transfers)
- To-account required (transfers only)

**Advanced Features**:
- **Template Selection**: Quick transaction presets
- **Create Another**: Stay in modal after save
- **Dynamic Category Creation**: Creates categories if not found
- **Personal ID Management**: Auto-increment with conflict retry

#### Edit Transaction

**Trigger**: Click edit on transaction row

**Pre-population**: Maps API data back to form format

**Update Flow**:
```
1. handleRecordEdit()
   ↓
2. Find source transaction
   ↓
3. Pre-populate form with existing values
   ↓
4. User modifies fields
   ↓
5. handleSaveTransaction() in edit mode
   ↓
6. API PUT request
   ↓
7. Optimistic update + server refresh
```

#### Delete Transaction

**Trigger**: Click delete on transaction row

**Confirmation**: SweetAlert2 modal with transaction details

**Flow**:
```
1. handleRecordDelete()
   ↓
2. Find source transaction
   ↓
3. Show confirmation with details
   ↓
4. If confirmed: Optimistic delete
   ↓
5. API DELETE request
   ↓
6. Success: Show success toast
   ↓
7. Error: Revert + show error
```

---

### 5. **Bulk Operations**

#### Selection System

**State**: `selectedTransactionIds` (array of numbers)

**UI Elements**:
- Individual checkboxes per transaction
- "Select All" checkbox in header
- Selection count display
- Clear selection button

**Functions**:
- `handleToggleTransactionSelect()` - Toggle single
- `handleRecordsSelectAll()` - Toggle all
- `handleClearSelection()` - Clear all

#### Bulk Actions

**Available Actions**:
1. **Bulk Edit** - Edit multiple transactions
2. **Bulk Export** - Export selected to CSV/PDF
3. **Bulk Delete** - Delete multiple with confirmation

**Handler**: `useBulkActionHandler()` hook

**UI**: Actions appear when ≥1 transaction selected

---

### 6. **Quick Transaction System**

#### Templates/Presets

**Purpose**: Save frequently used transaction patterns

**Components**:
- `QuickTransactionModal` - Create templates
- Template dropdown in main transaction modal

**Data Structure**:
```typescript
interface QuickTransactionOption {
  id: string | number;
  description: string;
  category: string;
  amount: string;
  account: string;
  type: TransactionType;
  currency: string;
}
```

**Usage**:
1. Create template from QuickTransactionModal
2. Select template in TransactionModal
3. Form auto-fills with template values
4. User can modify and save

**Persistence**: localStorage via `useQuickTransactions()` hook

---

### 7. **Real-Time Updates**

#### Event System

**Event Listeners**:
```typescript
useEffect(() => {
  const handleTransactionCreated = () => {
    fetchTransactionsFromServer({ force: true });
  };

  window.addEventListener('transaction-created', handleTransactionCreated);
  return () => window.removeEventListener('transaction-created', handleTransactionCreated);
}, []);
```

**Events**:
- `transaction-created` - From TransactionModalContext
- `transaction-updated` - From edit operations

**Purpose**: Sync with other components (Dashboard, etc.)

#### Optimistic Updates

**Pattern**: Update UI immediately, then sync with server

**Example**: Delete transaction
```typescript
// 1. Remove from local state
setApiTransactions(prev => prev.filter(txn => txn.id !== deletedId));

// 2. Call API
await transactionService.deleteTransaction(id);

// 3. Refresh from server (in case of conflicts)
await fetchTransactionsFromServer({ force: true });
```

---

### 8. **Personal ID Management**

#### Server-Side Fetching

**API Endpoint**: `GET /api/v1/personal-ids/max`

**Purpose**: Generate user-specific sequential IDs (Transaction #1, #2, #3...)

**Fetch Logic**:
```typescript
const [maxPersonalIds, setMaxPersonalIds] = useState<Record<string, number>>({});

useEffect(() => {
  if (isAuthenticated) {
    api.get('/api/v1/personal-ids/max')
      .then(setMaxPersonalIds)
      .catch(console.error);
  }
}, [isAuthenticated]);

// Get next personal ID for transactions
const getNextTransactionPersonalId = () => {
  return (maxPersonalIds.transactions || 0) + 1;
};
```

#### Conflict Resolution

**Problem**: Multiple users might generate same personal_id

**Solution**: Retry logic in transaction handlers
- Try with current max_personal_id + 1
- If 409 Conflict: Fetch fresh max from server and retry
- Max 3 attempts
- No localStorage management needed

---

### 9. **URL Integration**

#### Modal Triggers

**Query Parameter**: `?openTransactionModal`

**Usage**: Other pages can link to open transaction modal
```typescript
// From Dashboard or other pages
router.push('/transactions?openTransactionModal');
```

**Cleanup**: Removes param after opening modal

#### Future Enhancements

- Filter state in URL params
- Deep linking to specific transactions
- Shareable filtered views

---

### 10. **Mobile Responsiveness**

#### Breakpoint Strategy

**Breakpoint**: `lg` (992px+)

**Desktop (≥992px)**:
- Two-column layout
- Filter sidebar visible
- Desktop dropdown interactions
- Hover states

**Mobile (<992px)**:
- Single-column layout
- Filter sidebar hidden
- Filter/Add buttons in header
- Offcanvas filter panel
- Touch-optimized interactions

#### Mobile-Specific Features

**Filter Button**: Opens `MobileFilterOffcanvas`
**Add Button**: Mobile-friendly transaction creation
**Touch Gestures**: Swipe to select/delete (future)
**Responsive Tables**: Horizontal scroll for wide content

---

## State Management

### Local State (useState - 14 pieces!)

```typescript
// Core Data
const [apiTransactions, setApiTransactions] = useState<ApiTransactionResponse[]>([]);
const [currentTransaction, setCurrentTransaction] = useState<TransactionFormValues>(/**/);
const [maxPersonalIds, setMaxPersonalIds] = useState<Record<string, number>>({});

// UI State
const [showTransactionModal, setShowTransactionModal] = useState<boolean>(false);
const [isEditingTransaction, setIsEditingTransaction] = useState<boolean>(false);
const [showQuickTransactionModal, setShowQuickTransactionModal] = useState<boolean>(false);
const [showFilterSidebar, setShowFilterSidebar] = useState<boolean>(false);

// Selection State
const [selectedTransactionIds, setSelectedTransactionIds] = useState<number[]>([]);

// Form State
const [newQuickTransaction, setNewQuickTransaction] = useState<QuickTransactionFormValues>(/**/);

// Loading State
const [loadingTransactions, setLoadingTransactions] = useState<boolean>(false);

// Cache State
const lastFetchSignatureRef = useRef<string | null>(null);
const inflightFetchSignatureRef = useRef<string | null>(null);
```

### Hook State (useFilterData)

**Comprehensive Filter State**:
```typescript
{
  // Search & Sort
  searchTerm, setSearchTerm,
  sortOption, setSortOption,
  
  // Multi-Select Filters
  selectedCategories, setSelectedCategories,
  selectedAccounts, setSelectedAccounts,
  
  // Range Filters  
  minAmount, maxAmount,
  setMinAmount, setMaxAmount,
  
  // UI State
  filterVisibility, setFilterVisibility,
  
  // Data
  categories, apiAccounts, 
  categoryTree, parentCategoryColors, categoryIcons,
  accountTree, accountColors, accountIcons,
  allCategories, selectableAccounts,
}
```

### Context State (usePeriodNavigation)

```typescript
const { state: { dateRange, periodLabel, activePeriod, customRangeDraft } } = usePeriodNavigation();
```

### Persistent State (localStorage)

1. **Filter visibility** - Expanded/collapsed sections
2. **Quick transactions** - Template presets

### Server State (API)

1. **max_personal_ids** - Fetched from `GET /api/v1/personal-ids/max`

---

## Component Hierarchy

```
<Transactions>
  └─ PeriodNavigationProvider
      └─ TransactionsContent
          <Container>
            {/* Mobile Header */}
            <div className="d-lg-none">
              <Button onClick={toggleFilterSidebar}>Filter</Button>
              <Button onClick={openCreateTransactionModal}>Add</Button>
            </div>
            
            <Row>
              {/* Desktop Sidebar */}
              <Col lg={3} className="d-none d-lg-block">
                <DesktopFilterSidebar />
              </Col>
              
              {/* Main Content */}
              <Col lg={9}>
                <PeriodNavigation>
                  <PeriodRangeSelector />
                </PeriodNavigation>
                
                <Card>
                  <RecordsHeader /> {/* Bulk actions */}
                  <RecordsList />  {/* Transaction list */}
                </Card>
              </Col>
            </Row>
            
            {/* Mobile Filter Panel */}
            <MobileFilterOffcanvas />
            
            {/* Modals */}
            <TransactionModal />
            <QuickTransactionModal />
          </Container>
```

---

## Key Utility Functions

### Data Transformation

```typescript
// API Response → UI Format
mapApiTransactions(apiTransactions: ApiTransactionResponse[]): TransactionRecord[]

// Form Template Creation  
createTransactionTemplate(overrides?: Partial<TransactionFormValues>): TransactionFormValues

// Quick Template Creation
createQuickTransactionTemplate(overrides?: Partial<QuickTransactionFormValues>): QuickTransactionFormValues
```

### Validation & Normalization

```typescript
// Type-Safe Extractors
toNonEmptyString(value: unknown): string | undefined
toFiniteNumber(value: unknown, fallback = 0): number

// Transaction Type Validation
normalizeTransactionType(value: unknown, fallback: TransactionType): TransactionType

// Payment Type/Status Validation
normalizePaymentTypeValue(value: unknown, fallback: PaymentType): PaymentType
normalizePaymentStatusValue(value: unknown, fallback: PaymentStatus): PaymentStatus
```

### Category & Account Resolution

```typescript
// Ensure Category Exists (with API lookup/creation)
ensureCategoryExists(categoryName: string): Promise<ApiCategoryResponse | undefined>

// Account Name → ID Mapping
accountNameToIdMap: Record<string, string>

// Category Name → Object Mapping
categoryByName: Record<string, ApiCategoryResponse>
```

---

## Performance Optimizations

### 1. **Fetch Deduplication**

**Problem**: Multiple simultaneous filter changes trigger duplicate API calls

**Solution**: Signature-based deduplication
```typescript
const signaturePayload = {
  startDate, endDate, accounts, categories, 
  minAmount, maxAmount, search, sort
};
const signature = JSON.stringify(signaturePayload);

// Skip if same signature already in flight or completed
if (signature === inflightFetchSignatureRef.current) return false;
if (signature === lastFetchSignatureRef.current) return true;
```

### 2. **Memoization**

**useMemo Dependencies**:
- `transactions` - Depends on apiTransactions + mapApiTransactions
- `groupedTransactionRecords` - Depends on filteredTransactions + category data
- `selectedIdsSet` - Depends on selectedTransactionIds
- `accountNameToIdMap` - Depends on apiAccounts

### 3. **useCallback Dependencies**

**Heavy Functions**:
- `fetchTransactionsFromServer` - API call with complex params
- `handleSaveTransaction` - CRUD logic
- `handleTransactionChange` - Form updates
- `mapApiTransactions` - Data transformation

### 4. **Lazy Loading & Code Splitting**

- Page component lazy loaded via `LazyTransactions`
- Heavy modals loaded on demand
- Filter components conditionally rendered

### 5. **Virtual Scrolling** (Future Enhancement)

Long transaction lists could benefit from virtualization like in Dashboard.

---

## Error Handling

### API Errors

**Pattern**: Try-catch with SweetAlert2 user feedback

```typescript
try {
  await transactionService.deleteTransaction(id);
  await Swal.fire({ icon: 'success', title: 'Deleted', ... });
} catch (error) {
  await Swal.fire({ icon: 'error', title: 'Delete Failed', text: error.message });
}
```

### Validation Errors

**Source**: `validateTransactionInput()` from handlers

**Display**: SweetAlert2 modal with specific field errors

**Fields Validated**:
- Amount (required, numeric)
- Account (required, valid ID)
- Category (required for non-transfers)
- To-account (required for transfers)

### Data Mapping Errors

**Graceful Degradation**:
- Invalid dates → Current date
- Missing categories → "Unknown" category
- Missing accounts → "Unknown" account
- Invalid amounts → 0

**Logging**: Console errors for debugging

---

## Currency Formatting

### formatCurrency Function

**Implementation**: `Intl.NumberFormat` with fallbacks
```typescript
const formatCurrency = (amount: number, currency = 'IDR'): string => {
  try {
    return new Intl.NumberFormat('en-US', {
      style: 'currency',
      currency,
      minimumFractionDigits: 2,
    }).format(Math.abs(amount));
  } catch {
    return `${currency} ${Math.abs(amount).toFixed(2)}`;
  }
};
```

**Features**:
- Supports multiple currencies
- Handles negative numbers (sign prefix)
- Fallback for unsupported currencies
- Used throughout transaction display

---

## Dependencies

### Libraries
- **react-bootstrap**: Layout, Cards, Buttons, Forms
- **react-icons**: FaFilter, FaPlus, category/account icons
- **sweetalert2**: User confirmations and error messages
- **next/navigation**: Router, pathname, search params

### Internal Components
- **DesktopFilterSidebar** / **MobileFilterOffcanvas**: Filtering
- **TransactionModal** / **QuickTransactionModal**: CRUD operations
- **RecordsHeader** / **RecordsList**: Transaction display
- **PeriodNavigation** / **PeriodRangeSelector**: Date filtering

### Hooks & Services
- **useFilterData**: Shared filter state
- **usePeriodNavigation**: Date context  
- **useQuickTransactions**: Template management
- **useBulkActionHandler**: Bulk operations
- **transactionService**: API calls
- **categoryService**: Category API

### Handlers & Utils
- **transactionHandlers**: CRUD logic (validate, create, update, delete)
- **formatDateForInput**: Date formatting
- **Various utility functions**: Type-safe data extraction

---

## User Flows

### View Transactions Flow
```
1. User navigates to /transactions
2. Page loads with PeriodNavigationProvider
3. useFilterData loads filter state
4. fetchTransactionsFromServer() called
5. API returns transactions for current period
6. mapApiTransactions() transforms data
7. Grouped by date and displayed
8. User sees transaction list with filters
```

### Filter Transactions Flow
```
1. User types in search box
2. setSearchTerm() updates state
3. fetchTransactionsFromServer() called (debounced)
4. API called with search param
5. Results filtered server-side
6. UI updates with filtered transactions
7. URL updated (future enhancement)
```

### Create Transaction Flow
```
1. User clicks "Add Transaction" or mobile + button
2. setShowTransactionModal(true)
3. TransactionModal opens with empty form
4. User fills required fields (amount, account, category)
5. User clicks "Save"
6. handleSaveTransaction() called
7. validateTransactionInput() checks fields
8. resolveTransactionMetadata() processes data
9. handleCreateTransaction() calls API
10. handlePostCreationTasks() updates UI
11. Success: Refresh list, close modal
12. Error: Show SweetAlert2 with details
```

### Edit Transaction Flow
```
1. User clicks edit icon on transaction row
2. handleRecordEdit() finds source transaction
3. Pre-populate TransactionModal with existing data
4. setIsEditingTransaction(true)
5. User modifies fields
6. User clicks "Save" 
7. handleSaveTransaction() in edit mode
8. handleUpdateTransaction() calls API PUT
9. Success: Refresh list, close modal
10. Error: Show error, keep modal open
```

### Delete Transaction Flow
```
1. User clicks delete icon on transaction row
2. handleRecordDelete() finds source transaction
3. SweetAlert2 confirmation with transaction details
4. If confirmed: Optimistic delete (remove from UI)
5. API DELETE call
6. Success: Show success toast, refresh list
7. Error: Revert UI change, show error
```

### Bulk Operations Flow
```
1. User selects transactions via checkboxes
2. selectedTransactionIds updated
3. RecordsHeader shows bulk actions
4. User clicks bulk action (edit/export/delete)
5. useBulkActionHandler() processes action
6. Confirmation dialog (for destructive actions)
7. API calls for each selected transaction
8. UI updates based on results
9. Clear selection after completion
```

### Quick Transaction Flow
```
1. User clicks "Add Template" in TransactionModal
2. QuickTransactionModal opens
3. User fills template form (description, category, amount, account)
4. User clicks "Save Template"
5. addQuickTransactionPreset() saves to localStorage
6. Template available in dropdown
7. User selects template → Form auto-fills
8. User can modify and save as normal transaction
```

### Mobile Filter Flow
```
1. User on mobile device
2. Filter sidebar hidden (.d-none .d-lg-block)
3. User clicks filter button
4. MobileFilterOffcanvas slides in from right
5. Same filter options as desktop
6. User applies filters
7. Offcanvas closes
8. Transaction list updates
```

---

## CSS Classes

### Page Structure
- `.transactions-page` - Main container
- `.transactions-ledger-card` - Main content card
- `.transactions-ledger-card__body` - Card content

### Responsive Classes
- `.d-none .d-lg-block` - Desktop only (sidebar)
- `.d-lg-none` - Mobile only (buttons)

### Custom Classes (Expected)
- Various transaction-specific styling
- Filter panel styling
- Mobile-specific overrides

---

## Key Considerations for Rewrite

### 1. **Complex State Management** ⚠️

**Challenge**: 14+ pieces of local state + hook state + context state

**Recommendation**: Consider state management library (Redux Toolkit, Zustand) for cleaner organization

### 2. **Performance Critical Functions**

**Heavy Operations**:
- `mapApiTransactions()` - Transforms large datasets
- `fetchTransactionsFromServer()` - API calls with complex params
- Grouping transactions by date
- Filter state synchronization

**Optimization**: Ensure proper memoization and debouncing

### 3. **Personal ID Management**

**Critical**: Must implement retry logic for personal_id conflicts
- Server-side API endpoint: `GET /api/v1/personal-ids/max`
- Fresh fetch on conflicts
- Retry with incremented ID (max 3 attempts)

### 4. **Event System**

**Must Implement**: Custom events for cross-component updates
```typescript
window.dispatchEvent(new CustomEvent('transaction-created', { detail }));
```

### 5. **Category Resolution**

**Dynamic Creation**: `ensureCategoryExists()` function
- Search existing categories
- Fetch from API if not found  
- Create new category if still missing
- Complex fallback logic

### 6. **Mobile Responsiveness**

**Two-Component Strategy**:
- `DesktopFilterSidebar` (desktop)
- `MobileFilterOffcanvas` (mobile)
- Same logic, different UI patterns

### 7. **Transaction Handlers** ⚠️

**External Module**: Logic split into `transactionHandlers.ts`
- `validateTransactionInput()`
- `resolveTransactionMetadata()`
- `handleCreateTransaction()`
- `handleUpdateTransaction()`
- `handleCreateTransfer()`
- `handlePostCreationTasks()`

**Must Implement**: All handler functions with proper error handling

### 8. **Bulk Operations**

**Hook Dependency**: Uses `useBulkActionHandler()` hook
- Must implement this hook
- Handle bulk edit/export/delete
- Progress indicators for long operations

### 9. **Template System**

**localStorage Integration**: Via `useQuickTransactions()` hook
- Persist transaction templates
- Auto-fill forms
- Template management CRUD

### 10. **URL Integration**

**Query Param**: `?openTransactionModal` support
- Deep linking to transaction creation
- Clean URL after modal opens
- Future: Filter state in URL

### 11. **Real-Time Updates**

**Event Listeners**: Must listen for external transaction changes
- Dashboard transaction creation
- Context modal operations
- Cross-tab synchronization (future)

### 12. **Data Pipeline Integrity**

**Critical Chain**: API → Transform → Group → Display
- Each step must handle edge cases
- Proper error boundaries
- Graceful degradation

### 13. **Filter Deduplication**

**Advanced Pattern**: Signature-based API call deduplication
- Prevents duplicate requests
- Handles concurrent filter changes
- Cache management with refs

### 14. **Form State Complexity**

**TransactionFormValues**: Large interface with 20+ fields
- Template system integration  
- Edit/create mode switching
- Validation across multiple field combinations

### 15. **TypeScript Complexity**

**Heavy Type Usage**: Multiple complex interfaces
- Type guards for API data
- Union types for enums
- Generic type transformations

---

## Testing Considerations

### Unit Tests Needed
1. Utility functions (toNonEmptyString, normalizeTypes, etc.)
2. Data transformation (mapApiTransactions)
3. Currency formatting
4. Date/time handling

### Integration Tests Needed  
1. Filter state synchronization
2. API call deduplication
3. Personal ID management
4. Transaction CRUD flows

### E2E Tests Needed
1. Complete transaction creation flow
2. Edit/delete operations
3. Bulk selection and actions
4. Mobile filter interaction
5. Template system usage

### Performance Tests
1. Large transaction list rendering
2. Filter performance with many options
3. Memory usage with long sessions

---

## Comparison with Other Pages

| Feature | Dashboard | Accounts | **Transactions** |
|---------|-----------|----------|------------------|
| **Complexity** | High | Medium | **Highest** |
| **State Pieces** | 8 | 5 | **14+** |
| **API Calls** | 5 parallel | 1 | **1 complex** |
| **Modals** | 1 | 1 | **2** |
| **Filtering** | None | Toggle only | **Advanced (8 types)** |
| **Bulk Ops** | None | None | **Full suite** |
| **Mobile Design** | Responsive | Responsive | **Offcanvas** |
| **Real-time** | Refresh button | None | **Event listeners** |

**Transactions is the most complex page** with the most features and integration points.

---

## Documentation Status

✅ Complete feature analysis  
✅ State management breakdown  
✅ Component hierarchy mapping  
✅ Data transformation pipeline  
✅ User flows (6 major flows)  
✅ Performance optimization details  
✅ Mobile responsiveness strategy  
✅ Error handling patterns  
✅ Key implementation considerations  
⚠️ Requires careful implementation due to complexity  
⚠️ Consider state management library  
⚠️ Ensure proper testing coverage

**This is the core page of the application** - implement with extra care and testing!
