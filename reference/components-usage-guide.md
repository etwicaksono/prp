# Components Usage Guide & Behaviors

## 🎯 **How to Use Finance App Components**

This guide explains the practical usage, behaviors, and interaction patterns of all components in the finance application.

---

## 🏗️ **Layout & Navigation Components**

### **Header Component**

#### **Basic Usage**
```typescript
import Header from '@/components/Header';

// Used in root layout - no props needed
<Header />
```

#### **Behavior**
- **Auto-detects current route** using `usePathname()` and highlights active nav link
- **Mobile detection**: Shows hamburger menu on screens < 992px
- **Authentication awareness**: Displays user profile when logged in
- **Quick actions**: "Add Transaction" button triggers modal via context

#### **Navigation Flow**
```
Desktop (≥992px):
┌─────────────────────────────────────┐
│ [Logo] [Nav Links] [User] [+ Add]   │
└─────────────────────────────────────┘

Mobile (<992px):
┌─────────────────────────┐
│ [☰] [Logo] [User] [+]   │
└─────────────────────────┘
```

#### **Event Behaviors**
- **Navigation**: `router.push(href)` with automatic highlighting
- **Mobile menu**: Opens/closes offcanvas sidebar
- **Add transaction**: Calls `openTransactionModal()` from context
- **Logout**: Calls `logout()` and redirects to login

#### **Context Dependencies**
```typescript
const { openTransactionModal } = useTransactionModal();
const { logout } = useAuth();
```

---

## 📊 **Chart Components Usage**

### **BalanceTrendChart**

#### **Basic Usage**
```typescript
import BalanceTrendChart from '@/components/BalanceTrendChart';

const data = [
  { date: '2024-11-01', balance: 1000000 },
  { date: '2024-11-02', balance: 1050000 },
  // ... more data points
];

<BalanceTrendChart 
  data={data}
  height={300}
  lineColor="#2563eb"
  formatValue={(value) => `IDR ${value.toLocaleString('id-ID')}`}
/>
```

#### **Behavior**
- **Responsive**: Auto-adjusts to container width
- **Interactive tooltips**: Hover/tap shows formatted values
- **Smooth animations**: Line draws with animation on load
- **Y-axis formatting**: Auto-converts large numbers (1M, 500K)

#### **Data Requirements**
```typescript
interface BalanceDataPoint {
  date: string;     // Format: 'YYYY-MM-DD'
  balance: number;  // Positive/negative amounts
}
```

### **CategoryPieChart**

#### **Basic Usage**
```typescript
import CategoryPieChart from '@/components/CategoryPieChart';

const data = [
  { name: 'Food & Dining', value: 500000 },
  { name: 'Transportation', value: 300000 },
  { name: 'Shopping', value: 200000 },
];

<CategoryPieChart
  data={data}
  colors={['#0088FE', '#00C49F', '#FFBB28']}
  formatValue={(value) => `IDR ${value.toLocaleString('id-ID')}`}
  height={400}
/>
```

#### **Behavior**
- **Color assignment**: Uses provided colors or defaults
- **Percentage labels**: Shows percentage on each slice
- **Interactive tooltips**: Displays value + percentage
- **Responsive sizing**: Adjusts radius based on container

### **IncomeExpenseBarChart**

#### **Usage Pattern**
```typescript
const data = [
  { period: 'Jan 2024', income: 5000000, expense: 3500000 },
  { period: 'Feb 2024', income: 4800000, expense: 4000000 },
];

<IncomeExpenseBarChart
  data={data}
  height={350}
  incomeColor="#22c55e"
  expenseColor="#ef4444"
/>
```

#### **Behavior**
- **Dual bars**: Side-by-side income (green) vs expense (red)
- **Hover effects**: Highlights individual bars
- **Zero handling**: Shows bars even for zero values
- **Responsive**: Adjusts bar width based on container

---

## 🏷️ **Widget Components Usage**

### **BalanceTrendWidget**

#### **Smart Widget Usage**
```typescript
import BalanceTrendWidget from '@/components/Widget/BalanceTrendWidget';

// Self-contained widget - handles its own data fetching
<BalanceTrendWidget
  formatCurrency={(value) => formatCurrency(value, 'IDR')}
  height={300}
  lineColor="#2563eb"
/>
```

#### **Behavior**
- **Auto-fetches data** from `analyticsService.fetchBalanceTrend()`
- **Loading states**: Shows spinner while fetching
- **Error handling**: Displays error message on failure
- **Balance change indicator**: Shows up/down arrow with change percentage
- **Refresh capability**: Re-fetches on component mount

#### **Lifecycle**
```
Mount → Loading → Fetch Data → Success/Error → Display
```

### **RecentTransactionsList**

#### **Usage in Dashboard**
```typescript
import RecentTransactionsList from '@/components/RecentTransactionsList';

<RecentTransactionsList
  limit={10}
  onTransactionClick={(transaction) => {
    openTransactionModal(transaction);
  }}
  formatCurrency={formatCurrency}
  showAccountName={true}
/>
```

#### **Behavior**
- **Clickable items**: Each transaction opens edit modal
- **Category icons**: Dynamically resolves FontAwesome icons
- **Color coding**: Uses category colors for visual distinction
- **Empty state**: Shows "No recent transactions" when empty
- **Date formatting**: Shows relative dates (2 hours ago, Yesterday)

---

## 📋 **Data Management Components**

### **RecordsList - Advanced Transaction List**

#### **Complex Usage**
```typescript
import { RecordsList } from '@/components/Records';

const groupedTransactions = {
  'November 15, 2024': [
    { id: '1', description: 'Coffee', amount: 45000, categoryName: 'Food', /* ... */ },
    { id: '2', description: 'Gas', amount: 100000, categoryName: 'Transport', /* ... */ }
  ],
  'November 14, 2024': [
    { id: '3', description: 'Salary', amount: 5000000, categoryName: 'Income', /* ... */ }
  ]
};

<RecordsList
  groupedTransactions={groupedTransactions}
  selectedRecords={new Set(['1', '3'])}
  accountName="Main Account"
  onSelectRecord={(id) => setSelectedIds(prev => 
    prev.has(id) ? prev.delete(id) : prev.add(id)
  )}
  onEditRecord={(record) => openEditModal(record)}
  onDeleteRecord={(id) => handleDelete(id)}
  formatCurrency={(amount) => formatCurrency(amount)}
  showCheckboxes={true}
  showPayer={true}
  showType={false}
/>
```

#### **Complex Behaviors**

##### **Selection Logic**
```typescript
// Individual selection
onSelectRecord('transaction-123') → toggles single checkbox

// Day-level selection  
// If user clicks day header checkbox:
// - If all day transactions selected → deselect all
// - If some/none selected → select all for that day
```

##### **Grouping Behavior**
- **Sticky headers**: Date headers stick to top during scroll
- **Visual grouping**: Each date section has distinct styling
- **Empty states**: Shows "No records found" when empty

##### **Icon Resolution**
```typescript
// Component automatically resolves category icons
const CategoryIcon = FaIcons[transaction.categoryIcon]; // e.g., 'FaCoffee'
// Falls back to 📦 emoji if icon not found
```

### **RecordsHeader - Bulk Actions**

#### **Usage with Selection State**
```typescript
import { RecordsHeader } from '@/components/Records';

<RecordsHeader
  selectedCount={selectedIds.size}
  totalCount={allTransactions.length}
  allSelected={selectedIds.size === allTransactions.length}
  onSelectAll={() => {
    if (allSelected) {
      setSelectedIds(new Set());
    } else {
      setSelectedIds(new Set(allTransactions.map(t => t.id)));
    }
  }}
  onClearSelection={() => setSelectedIds(new Set())}
  onBulkEdit={() => openBulkEditModal(Array.from(selectedIds))}
  onBulkExport={() => exportTransactions(Array.from(selectedIds))}
  onBulkDelete={() => deleteBulkTransactions(Array.from(selectedIds))}
  summaryText="Total: IDR 2,500,000"
  showBulkActions={true}
/>
```

#### **State-Driven Behavior**
```typescript
// No selection state
selectedCount === 0 → Shows: "Found X records" with summary

// Has selection state  
selectedCount > 0 → Shows: "Selected X items" with bulk action buttons
```

#### **Bulk Action Flow**
```
Select Items → Header Shows Actions → Click Action → Confirmation → API Calls → Refresh
```

---

## 🔧 **Form Components Usage**

### **AddAccountModal - Complex Form**

#### **Create Mode Usage**
```typescript
import AddAccountModal from '@/components/AddAccountModal';

const [showModal, setShowModal] = useState(false);

<AddAccountModal
  show={showModal}
  onHide={() => setShowModal(false)}
  title="Create New Account"
  onSubmit={async (formData) => {
    try {
      await accountService.createAccount({
        personal_id: getNextPersonalId(),
        name: formData.name,
        color: formData.color,
        account_type: formData.accountType,
        // ... other fields
      });
      showToast('Account created successfully!', 'success');
      setShowModal(false);
      refreshAccounts();
    } catch (error) {
      showToast(error.message, 'error');
    }
  }}
  isEditMode={false}
/>
```

#### **Edit Mode Usage**
```typescript
<AddAccountModal
  show={showEditModal}
  onHide={() => setShowEditModal(false)}
  title="Edit Account"
  initialValue={{
    name: existingAccount.name,
    color: existingAccount.color,
    accountType: existingAccount.account_type,
    initialAmount: existingAccount.initial_amount?.toString() || '',
    currency: existingAccount.currency || 'IDR',
    excludeFromStatistics: !existingAccount.include_in_stats,
    iconKey: existingAccount.icon,
    isActive: existingAccount.active,
    usability: existingAccount.usability as 'USABLE' | 'PROTECTED',
  }}
  accountId={existingAccount.id}
  isEditMode={true}
  onSubmit={(formData) => updateAccount(existingAccount.id, formData)}
/>
```

#### **Form Behaviors**

##### **Icon Picker Behavior**
- **Grid layout**: Shows FontAwesome icons in responsive grid
- **Search filter**: Type to filter icons by name
- **Visual selection**: Selected icon highlighted
- **Fallback**: Defaults to `FaWallet` if none selected

##### **Color Picker Behavior**
```typescript
// Real-time color preview
colorHexInput = "#ce9600" → Updates preview circle immediately
// Validation on blur → Shows error if invalid hex
```

##### **Currency Input Behavior**
```typescript
// Real-time formatting
Input: "1000000" → Display: "IDR 1,000,000"
// Parsing on submit
Display: "IDR 1,000,000" → API: 1000000
```

##### **Validation Flow**
```
User Types → Real-time Validation → Show/Hide Errors → Submit Attempt → Full Validation
```

### **AmountRangeFilter - Dual Range Input**

#### **Usage in Filter Sidebar**
```typescript
import AmountRangeFilter from '@/components/AmountRangeFilter';

<AmountRangeFilter
  minAmount={0}
  maxAmount={5000000}
  onMinAmountChange={(value) => setFilters(prev => ({ ...prev, minAmount: value }))}
  onMaxAmountChange={(value) => setFilters(prev => ({ ...prev, maxAmount: value }))}
  currency="IDR"
  minLimit={0}
  maxLimit={20000000}
  step={100000}
  controlId="transactionAmountFilter"
/>
```

#### **Dual Input Behavior**

##### **Slider Synchronization**
```typescript
// User drags slider → Updates input field
// User types in input → Updates slider position
// Maintains sync between both interaction methods
```

##### **Validation Logic**
```typescript
// Ensures min <= max
if (newMin > maxAmount) {
  setMaxAmount(newMin); // Auto-adjust max
}
if (newMax < minAmount) {
  setMinAmount(newMax); // Auto-adjust min
}
```

##### **Currency Formatting**
```typescript
// Input shows formatted values
Display: "IDR 1,000,000"
// But callbacks receive raw numbers
onMinAmountChange(1000000) // Raw number
```

---

## 📅 **Date & Period Components**

### **PeriodNavigation + Context Usage**

#### **Provider Setup**
```typescript
import { PeriodNavigationProvider, PeriodNavigation } from '@/components/PeriodNavigation';

// Wrap your component tree
<PeriodNavigationProvider>
  <YourPageComponent />
</PeriodNavigationProvider>
```

#### **Navigation Usage**
```typescript
import PeriodNavigation, { usePeriodNavigation } from '@/components/PeriodNavigation';

function TransactionPage() {
  const { state, actions } = usePeriodNavigation();
  
  return (
    <PeriodNavigation>
      <h3>{state.periodLabel}</h3> {/* "November 2024" */}
    </PeriodNavigation>
  );
}
```

#### **Period Navigation Behaviors**

##### **Button Behavior**
```typescript
// Previous button: shifts period backward
onClick Previous → Nov 2024 → Oct 2024

// Next button: shifts period forward  
onClick Next → Nov 2024 → Dec 2024

// Auto-disables at boundaries (e.g., future dates)
```

##### **Context State Flow**
```typescript
// State updates automatically trigger re-renders
state.dateRange → { start: '2024-11-01', end: '2024-11-30' }
state.periodLabel → "November 2024"
state.activePeriod → { type: 'month', year: 2024, month: 11 }
```

### **PeriodRangeSelector - Date Range Picker**

#### **Full Featured Usage**
```typescript
import PeriodRangeSelector from '@/components/PeriodRangeSelector';
import { usePeriodNavigation } from '@/components/PeriodNavigation';

function FilterSidebar() {
  const { state, actions } = usePeriodNavigation();
  
  return (
    <PeriodRangeSelector
      activePeriod={state.activePeriod}
      customRangeDraft={state.customRangeDraft}
      onSelectPreset={actions.handleSelectPreset}
      onSelectMonth={actions.handleSelectMonth}
      onSelectYear={actions.handleSelectYear}
      onCustomDraftChange={actions.handleCustomDraftChange}
      onCustomApply={actions.handleCustomApply}
    />
  );
}
```

#### **Interaction Behaviors**

##### **Preset Button Behavior**
```typescript
// Click "This Month" → Immediately updates period
onClick("this-month") → 
  state.activePeriod = { type: 'month', year: 2024, month: 11 }
  state.dateRange = { start: '2024-11-01', end: '2024-11-30' }
  state.periodLabel = "November 2024"
```

##### **Custom Range Behavior**
```typescript
// Two-step process: Draft → Apply
1. User selects start date → Updates customRangeDraft only
2. User selects end date → Updates customRangeDraft only  
3. User clicks "Apply" → Updates main state with custom range

// Validation during draft
if (endDate < startDate) → Shows error, prevents apply
```

##### **Month/Year Dropdown Behavior**
```typescript
// Year dropdown shows ±5 years from current
// Month dropdown shows all months for selected year
// Selection immediately updates period (no "Apply" needed)
```

---

## 🚨 **Error Handling Components**

### **ErrorBoundary Usage**

#### **Component Wrapping**
```typescript
import { ErrorBoundary } from '@/components/ErrorBoundary';

// Wrap potentially error-prone components
<ErrorBoundary
  fallback={(error, reset) => (
    <div>
      <h3>Something went wrong in the transaction list</h3>
      <button onClick={reset}>Try Again</button>
    </div>
  )}
  onError={(error, errorInfo) => {
    console.error('Transaction list error:', error);
    // Send to error tracking service
  }}
  isolate={true}
>
  <TransactionList />
</ErrorBoundary>
```

#### **Error Recovery Behavior**
```typescript
// When error occurs:
1. Component catches error → Shows fallback UI
2. User clicks "Retry" → reset() clears error state → Re-renders component
3. User clicks "Go Home" → Navigates to dashboard
4. Error details logged to console (dev mode)
```

### **ToastAlert Usage**

#### **With Toast Context**
```typescript
import { useToast } from '@/context/ToastContext';

function SomeComponent() {
  const { showToast } = useToast();
  
  const handleSubmit = async () => {
    try {
      await apiCall();
      showToast('Success!', 'success');
    } catch (error) {
      showToast(error.message, 'error');
    }
  };
}
```

#### **Direct Usage**
```typescript
import ToastAlert from '@/components/ToastAlert';

<ToastAlert
  open={showToast}
  onClose={() => setShowToast(false)}
  severity="success"
  message="Account created successfully!"
  autoHideDuration={4000}
  anchorOrigin={{ vertical: 'top', horizontal: 'right' }}
/>
```

#### **Toast Behavior**
- **Auto-hide**: Disappears after `autoHideDuration` (default: 4000ms)
- **Manual close**: Click X or call `onClose`
- **Stacking**: Multiple toasts stack vertically
- **Positioning**: 9 different anchor positions available

---

## 🔄 **Component Interaction Patterns**

### **Pattern 1: Modal → List → Context Flow**

```typescript
// User opens AddAccountModal
<AddAccountModal onSubmit={(data) => {
  // 1. Create account via API
  const newAccount = await accountService.createAccount(data);
  
  // 2. Update context state
  setAccounts(prev => [...prev, newAccount]);
  
  // 3. Close modal
  setShowModal(false);
  
  // 4. Show success toast
  showToast('Account created!', 'success');
  
  // 5. Refresh related components automatically via context
}} />
```

### **Pattern 2: Filter → API → Chart Flow**

```typescript
// User changes date range in PeriodRangeSelector
onCustomApply(range) → 
  // 1. Context updates period state
  state.dateRange = range → 
  
  // 2. Components listening to context re-render
  useEffect(() => {
    fetchBalanceData(state.dateRange);
  }, [state.dateRange]) →
  
  // 3. Charts update with new data
  <BalanceTrendChart data={newData} />
```

### **Pattern 3: Bulk Selection → Actions Flow**

```typescript
// User selects transactions in RecordsList
<RecordsList onSelectRecord={(id) => {
  // 1. Update selection state
  setSelectedIds(prev => prev.has(id) ? 
    new Set([...prev].filter(x => x !== id)) : 
    new Set([...prev, id])
  );
}} />

// 2. RecordsHeader reacts to selection change
<RecordsHeader 
  selectedCount={selectedIds.size}
  // Shows bulk actions when selectedCount > 0
/>

// 3. User clicks bulk action
onBulkDelete={() => {
  // Confirmation → API calls → State updates → UI refresh
}}
```

---

## ⚡ **Performance Behaviors**

### **Chart Component Performance**
```typescript
// Charts use React.memo for expensive re-renders
const MemoizedChart = React.memo(BalanceTrendChart);

// Only re-renders when data actually changes
<MemoizedChart data={chartData} />
```

### **List Component Virtualization**
```typescript
// For large transaction lists (1000+ items)
// Component automatically uses virtual scrolling
if (transactions.length > 500) {
  return <VirtualizedRecordsList />
} else {
  return <RegularRecordsList />
}
```

### **Context Performance**
```typescript
// Contexts use selective subscriptions
const { dateRange } = usePeriodNavigation(); // Only re-renders on dateRange change
// Not: const { state } = usePeriodNavigation(); // Would re-render on any state change
```

---

## 🎯 **Best Practices for Using Components**

### **1. Context Dependencies**
```typescript
// Always wrap components that need contexts
<AuthProvider>
  <TransactionModalProvider>
    <PeriodNavigationProvider>
      <YourComponents />
    </PeriodNavigationProvider>
  </TransactionModalProvider>
</AuthProvider>
```

### **2. Error Boundaries**
```typescript
// Wrap complex components
<ErrorBoundary>
  <ComplexTransactionList />
</ErrorBoundary>
```

### **3. Loading States**
```typescript
// Components with async data should handle loading
if (loading) return <Spinner />;
if (error) return <ErrorMessage />;
return <ActualContent />;
```

### **4. Form Validation**
```typescript
// Use schema validation for forms
const result = schema.safeParse(formData);
if (!result.success) {
  setErrors(formatValidationErrors(result.error));
  return;
}
```

### **5. Currency Formatting**
```typescript
// Use consistent currency formatting
const formatCurrency = (amount: number) => 
  new Intl.NumberFormat('id-ID', {
    style: 'currency',
    currency: 'IDR'
  }).format(amount);
```

---

## 🚀 **Component Lifecycle Behaviors**

### **Mount → Data → Display Pattern**
```typescript
useEffect(() => {
  // 1. Component mounts
  setLoading(true);
  
  // 2. Fetch data
  fetchData()
    .then(setData)
    .catch(setError)
    .finally(() => setLoading(false));
}, [dependencies]);

// 3. Render based on state
if (loading) return <Spinner />;
if (error) return <Error />;
return <Content data={data} />;
```

### **Form Submission Lifecycle**
```typescript
const handleSubmit = async (formData) => {
  // 1. Validate
  if (!validate(formData)) return;
  
  // 2. Show loading
  setSubmitting(true);
  
  try {
    // 3. API call
    await apiCall(formData);
    
    // 4. Success feedback
    showToast('Success!', 'success');
    
    // 5. Close/reset form
    onClose();
  } catch (error) {
    // 6. Error handling
    setError(error.message);
  } finally {
    // 7. Hide loading
    setSubmitting(false);
  }
};
```

**All components are designed to work together seamlessly** with consistent patterns, proper error handling, and responsive behaviors! 🎉
