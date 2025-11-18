# Dashboard Page

## Routes
- **URL**: `/` (homepage)
- **File**: `app/page.tsx` → `src/views/Dashboard/Dashboard.tsx`

## Purpose
Main landing page for authenticated users showing financial overview with customizable widgets.

## Page Structure

```typescript
app/page.tsx (wrapper)
  └─ ProtectedShell
      └─ Suspense
          └─ LazyDashboard (src/views/Dashboard/Dashboard.tsx)
              └─ PeriodNavigationProvider
                  └─ DashboardContent
```

## Key Features

### 1. Account Cards Display
**Visual Layout**: Grid of account cards (4 columns on desktop, 2 on tablet, 1 on mobile)

**Each Card Shows**:
- Account name
- Current balance (formatted currency)
- Icon (customizable)
- Accent color (custom per account)
- Edit button (appears on hover)

**Interactions**:
- Click card → Navigate to Account Detail page
- Hover → Show edit button
- Click edit → Open AddAccountModal in edit mode
- "Add Account" card → Open AddAccountModal in create mode

**Data Flow**:
```
accountService.fetchAccounts()
  → ApiAccountResponse[]
  → mapApiAccountToAccount()
  → Account[] (with UI properties)
  → Rendered as Cards
```

---

### 2. Customizable Widget System

#### Widget Types
1. **Balance Trend** - Line chart showing balance over time
2. **Expenses by Category** - Pie chart of expense breakdown
3. **Income vs Expenses** - Bar chart comparing income/expense trends
4. **Recent Transactions** - List of latest transactions
5. **Budget Status** - Progress bars for budget tracking

#### Widget Features

**Drag & Drop Reordering**:
- Library: @dnd-kit (DndContext, SortableContext)
- Strategy: rectSortingStrategy
- Sensors: PointerSensor (8px activation), KeyboardSensor
- Persistence: localStorage key `'dashboard-widget-order'`

**Show/Hide Widgets**:
- Control panel button (gear icon) in top-right
- Dropdown menu with checkboxes
- State: `widgetVisibility` object
- Toggle individual widgets on/off

**Widget Order Management**:
```typescript
const DEFAULT_WIDGET_ORDER = [
  'balanceTrend',
  'expensesByCategory',
  'incomeVsExpenses',
  'recentTransactions',
  'budgetStatus',
];

// Saved to localStorage after each reorder
localStorage.setItem('dashboard-widget-order', JSON.stringify(widgetOrder));
```

**Widget Components Map**:
```typescript
const widgets: Record<string, { title: string; component: React.ReactNode }> = {
  balanceTrend: { title: 'Balance Trend', component: <BalanceTrendWidget /> },
  expensesByCategory: { title: 'Expenses by Category', component: <CategoryPieChart /> },
  incomeVsExpenses: { title: 'Income vs Expenses', component: <IncomeExpenseBarChart /> },
  recentTransactions: { title: 'Recent Transactions', component: <RecentTransactionsList /> },
  budgetStatus: { title: 'Budget Status', component: <BudgetStatusList /> },
};
```

---

### 3. Period Navigation

**Component**: `PeriodNavigation` + `PeriodRangeSelector`

**Context**: `PeriodNavigationProvider` wraps entire dashboard

**Available Periods**:
- Today
- This Week
- This Month
- This Year
- Custom Range (date picker)

**State**:
```typescript
{
  periodLabel: string;      // Display text
  activePeriod: string;     // Selected period ID
  customRangeDraft: {...};  // Custom date range state
}
```

**Usage**: Filters data for all widgets based on selected period

---

### 4. Data Loading

#### Parallel Data Fetching
```typescript
const [apiAccounts, expenses, trends, transactions, budgets] = await Promise.all([
  accountService.fetchAccounts(),
  analyticsService.fetchExpensesByCategory(),
  analyticsService.fetchIncomeExpenseTrend(),
  analyticsService.fetchRecentTransactions(20),
  budgetService.fetchBudgetStatus(),
]);
```

**Services Used**:
1. `accountService` - Account CRUD operations
2. `analyticsService` - Dashboard analytics data
3. `budgetService` - Budget tracking data

#### Data Transformation

**Account Mapping**:
```typescript
mapApiAccountToAccount(apiAccount, index) → Account
```

Transforms API response to UI-friendly Account object with:
- Icon component (React Icons)
- Accent color + background color (lightened version)
- Personal ID validation
- Default values for missing fields

**Filtering**:
- Only active accounts shown (`active: true`)
- Expense pie chart filters out categories < 0.5% (noise reduction)
- Failed mappings (null) filtered out

---

### 5. Account Management

#### Add Account Modal
**Component**: `AddAccountModal`

**Trigger**:
- Click "Add Account" card
- Opens modal with empty form

**Form Fields**:
- Name (required)
- Account Type (bank, cash, credit card, etc.)
- Currency (default: IDR)
- Initial Amount (optional)
- Icon (icon picker)
- Color (color picker)
- Usability (USABLE/PROTECTED)
- Active status

**Submit Flow**:
```
User fills form
  → AddAccountModal validates
  → Calls accountService.createAccount()
  → On success: Refresh all dashboard data
  → Close modal
```

#### Edit Account Modal
**Component**: Same `AddAccountModal` in edit mode

**Trigger**:
- Click edit button (pencil icon) on account card
- Stops event propagation to prevent card click

**Pre-population**:
```typescript
accountToNewAccountForm(account) → NewAccountForm
```

Converts Account to form-friendly format

**Submit Flow**:
```
User edits form
  → AddAccountModal validates
  → Calls accountService.updateAccount(accountId, data)
  → On success: Refresh all dashboard data
  → Close modal
```

---

### 6. Loading & Error States

**Initial Loading**:
- `loading` state set to `true` during fetch
- Widgets show "Loading..." text
- Prevents render until data ready

**Empty States**:
- "No expense data available" (pie chart)
- "No income/expense data available" (bar chart)
- "No recent transactions" (transaction list)
- "No budget data available" (budget list)

**Error Handling**:
- Console error logging
- Graceful degradation (empty UI if fetch fails)
- No error toast (silent failure)

**Double-Fetch Prevention**:
```typescript
const fetchedRef = React.useRef<boolean>(false);

useEffect(() => {
  if (fetchedRef.current) return;
  fetchedRef.current = true;
  // Fetch data...
}, []);
```

Prevents React StrictMode double-fetch in development

---

### 7. Responsive Design

**Grid Breakpoints**:
- `xs={12}` - Mobile: 1 column
- `sm={6}` - Tablet: 2 columns
- `md={3}` - Desktop: 4 columns (accounts)

**Widget Cards**:
- Bootstrap `Col` with responsive sizing
- Cards auto-stack on mobile

**Period Selector**:
- Responsive positioning with flex layout
- Control panel aligned to right

---

### 8. Currency Formatting

**Function**: `formatCurrency(value: number): string`

**Format**: 
- Locale: `id-ID` (Indonesian)
- Prefix: `IDR`
- Decimals: 2 places
- Negative sign before currency

**Examples**:
- `50000.00` → `"IDR 50,000.00"`
- `-25000.50` → `"-IDR 25,000.50"`

Used throughout dashboard for consistency

---

## State Management

### Local State (useState)
```typescript
const [accounts, setAccounts] = useState<Account[]>([]);
const [showAddAccountModal, setShowAddAccountModal] = useState(false);
const [editingAccount, setEditingAccount] = useState<Account | null>(null);
const [expenseData, setExpenseData] = useState<ExpenseByCategory[]>([]);
const [incomeExpenseData, setIncomeExpenseData] = useState<IncomeExpenseTrend[]>([]);
const [recentTransactions, setRecentTransactions] = useState<DashboardTransaction[]>([]);
const [budgetStatus, setBudgetStatus] = useState<BudgetStatus[]>([]);
const [loading, setLoading] = useState(true);
const [widgetOrder, setWidgetOrder] = useState<string[]>(() => loadWidgetOrder());
const [activeId, setActiveId] = useState<string | null>(null); // Drag state
const [showControlPanel, setShowControlPanel] = useState(false);
const [widgetVisibility, setWidgetVisibility] = useState({...});
```

### Context State (usePeriodNavigation)
```typescript
const { state: { periodLabel, activePeriod, customRangeDraft } } = usePeriodNavigation();
```

### Persistent State (localStorage)
- Widget order: `'dashboard-widget-order'`
- Loaded on mount, saved on change

---

## Dependencies

### Libraries
- **react-bootstrap**: Layout, Cards, Dropdown, Form
- **react-icons**: FaWallet, FaPencilAlt, RiListSettingsLine
- **@dnd-kit**: Drag & drop widget reordering
- **next/navigation**: useRouter for navigation

### Internal Dependencies
- **Services**: accountService, analyticsService, budgetService
- **Components**: 
  - PeriodNavigation, PeriodRangeSelector
  - AddAccountModal
  - CategoryPieChart, IncomeExpenseBarChart
  - RecentTransactionsList, BudgetStatusList
  - BalanceTrendWidget
  - SortableWidgetCard, WidgetCard
- **Utils**: resolveIconFromApiName, lightenColor, accountUtils

---

## Performance Optimizations

1. **Parallel Data Fetching**: `Promise.all()` for simultaneous requests
2. **Memoization**: `React.useMemo()` for filtered expense data
3. **Lazy Loading**: Dashboard lazily loaded via `LazyDashboard`
4. **Code Splitting**: Suspense boundary for async loading
5. **Drag Performance**: 8px activation constraint (prevents accidental drags)
6. **LocalStorage Caching**: Widget order persisted across sessions

---

## User Flows

### Add Account Flow
```
1. User clicks "Add Account" card
2. AddAccountModal opens
3. User fills form (name, type, amount, etc.)
4. User clicks "Save"
5. Modal validates + calls API
6. Dashboard refreshes all data
7. Modal closes, new account card appears
```

### Edit Account Flow
```
1. User hovers over account card
2. Edit button (pencil) appears
3. User clicks edit button (event doesn't bubble to card)
4. AddAccountModal opens in edit mode (pre-filled)
5. User modifies fields
6. User clicks "Save"
7. Modal validates + calls API
8. Dashboard refreshes all data
9. Modal closes, card updates
```

### View Account Details Flow
```
1. User clicks account card (anywhere except edit button)
2. Router navigates to `/accounts/{id}?from=dashboard`
3. AccountDetail page loads
4. User can return via "Back" button
5. Back button checks `?from=` param and routes to Dashboard
```

### Customize Widgets Flow
```
1. User clicks control panel button (gear icon)
2. Dropdown opens with widget checkboxes
3. User checks/unchecks widgets
4. Widgets immediately show/hide
5. User drags widget card to reorder
6. Widget order updates + saves to localStorage
7. Order persists across page reloads
```

---

## Key Considerations for Rewrite

1. **Widget System**: Highly customizable, order + visibility persisted
2. **Drag & Drop**: Uses @dnd-kit, ensure library is installed
3. **Data Fetching**: Parallel fetching critical for performance
4. **Account Mapping**: Complex transformation logic, handle nulls
5. **Modal State**: Single modal for both add/edit, controlled by `editingAccount`
6. **Period Context**: Entire dashboard wrapped in PeriodNavigationProvider
7. **Responsive Grid**: Bootstrap grid with specific breakpoints
8. **Error Handling**: Silent failures, no user-facing errors
9. **LocalStorage**: Widget order + account metadata cached
10. **Icon Resolution**: React Icons components resolved from string keys
11. **Color Utilities**: `lightenColor()` creates background from accent color
12. **Navigation Params**: `?from=dashboard` for back navigation
13. **Fetch Prevention**: `fetchedRef` prevents double-fetch in StrictMode
14. **Event Propagation**: Edit button `stopPropagation()` to prevent card click
