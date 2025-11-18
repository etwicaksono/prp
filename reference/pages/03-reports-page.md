# Reports & Analytics Page

## Routes
- **URL**: `/reports`
- **File**: `app/reports/page.tsx` → `src/views/Reports/Reports.tsx`

## Purpose
Analytics and reporting page with customizable widget dashboard for financial insights.

## Page Structure

```typescript
app/reports/page.tsx (wrapper)
  └─ ProtectedShell
      └─ Suspense
          └─ LazyReports (src/views/Reports/Reports.tsx)
              └─ PeriodNavigationProvider
                  └─ ReportsContent
```

## Key Features

### 1. Page Header

**Layout**:
```
┌────────────────────────────────────────────────┐
│ Reports & Analytics                            │
│                                                 │
│ [Period Selector]        [Widget Settings ⚙️]  │
└────────────────────────────────────────────────┘
```

**Elements**:
- H1 heading: "Reports & Analytics"
- Period navigation controls (centered)
- Widget control panel button (right-aligned)

---

### 2. Widget System (Same as Dashboard)

#### Available Widgets (6 total)

1. **Balance Trend**
   - Line chart showing balance over time
   - Component: `BalanceTrendWidget`
   - Height: 350px
   - Line color: `#2563eb` (blue)

2. **Expenses by Category**
   - Pie chart of expense breakdown
   - Component: `CategoryPieChart`
   - Height: 350px
   - Outer radius: 100px
   - Filters out categories < 0.5%

3. **Income vs Expenses (Yearly)**
   - Bar chart comparing income/expense by period
   - Component: `IncomeExpenseBarChart`
   - Height: 100% (flexible)
   - Colors: Green (#2ecc71) and Red (#e74c3c)

4. **Cash Flow Analysis** ⭐ (Unique to Reports)
   - Line chart showing income, expense, and net flow
   - Component: `CashFlowChart`
   - Height: 100% (flexible)
   - Three lines: Income (green), Expense (red), Net (black)

5. **Recent Transactions**
   - List of last 30 transactions
   - Component: `RecentTransactionsList`
   - Scrollable list

6. **Budget Status**
   - Progress bars for budget tracking
   - Component: `BudgetStatusList`
   - Shows top 5 budgets
   - Status indicators (on-track, warning, exceeded)

#### Widget Management

**Drag & Drop Reordering**:
- Library: @dnd-kit
- Strategy: `rectSortingStrategy` (grid layout)
- Sensors: PointerSensor (8px activation), KeyboardSensor
- Persistence: localStorage key `'reports-widget-order'`

**Show/Hide Widgets**:
- Control panel: Gear icon button (top-right)
- Dropdown with checkboxes for each widget
- State: `widgetVisibility` object
- Instant show/hide on toggle

**Widget Order State**:
```typescript
const DEFAULT_WIDGET_ORDER = [
  'balanceTrend',
  'expensesByCategory',
  'incomeVsExpenses',
  'cashFlow',
  'recentTransactions',
  'budgetStatus',
];
```

**LocalStorage Persistence**:
- Key: `'reports-widget-order'`
- Saved on every reorder
- Loaded on mount
- Fallback to default if invalid

---

### 3. Period Navigation

**Same as Dashboard**:
- PeriodNavigation component with PeriodRangeSelector
- Filters: Today, This Week, This Month, This Year, Custom Range
- Context: `PeriodNavigationProvider` wraps entire page
- State: `periodLabel`, `activePeriod`, `customRangeDraft`

**Usage**: Filters data for all widgets based on selected period

---

### 4. Data Loading

#### Parallel Data Fetching

```typescript
const [expenses, incomeExpense, cashFlow, transactions, budgets] = await Promise.all([
  analyticsService.fetchExpensesByCategory(),
  analyticsService.fetchIncomeExpenseTrend(),
  analyticsService.fetchCashFlow(),           // 🆕 Unique to Reports
  analyticsService.fetchRecentTransactions(30), // 30 vs 20 on Dashboard
  budgetService.fetchBudgetStatus(),
]);
```

**Differences from Dashboard**:
- Fetches cash flow data (not on Dashboard)
- Fetches 30 recent transactions (vs 20)
- No account data fetching

#### Services Used

1. **analyticsService**:
   - `fetchExpensesByCategory()` → `ExpenseByCategory[]`
   - `fetchIncomeExpenseTrend()` → `IncomeExpenseTrend[]`
   - `fetchCashFlow()` → `CashFlowData[]` (Reports only)
   - `fetchRecentTransactions(30)` → `DashboardTransaction[]`

2. **budgetService**:
   - `fetchBudgetStatus()` → `BudgetStatus[]`

#### Data Types

**ExpenseByCategory**:
```typescript
{
  name: string;      // Category name
  value: number;     // Total expense amount
  color?: string;    // Chart color
}
```

**IncomeExpenseTrend**:
```typescript
{
  period: string;    // '2024-01', '2024-02', etc.
  income: number;    // Total income for period
  expense: number;   // Total expense for period
}
```

**CashFlowData**: (Reports-specific)
```typescript
{
  period: string;    // Date/period label
  income: number;    // Income for period
  expense: number;   // Expense for period
  net: number;       // Net cash flow (income - expense)
}
```

**DashboardTransaction**:
```typescript
{
  id: string;
  date: string;
  description: string;
  amount: number;
  type: 'INCOME' | 'EXPENSE';
  category_name: string;
  account_name: string;
}
```

**BudgetStatus**:
```typescript
{
  id: string;
  category_name: string;
  budget_amount: number;
  spent_amount: number;
  percentage: number;
  status: 'on-track' | 'warning' | 'exceeded';
}
```

---

### 5. Currency Formatting

#### Two Formatting Functions

**formatCurrency(value: number): string**
- Locale: `id-ID` (Indonesian)
- Decimals: 2 places
- Usage: Display values, labels
- Example: `50000.00` → `"IDR 50,000.00"`

**formatCurrencyTooltip(value: number): [string, string]**
- Locale: `id-ID`
- Decimals: 0 places (whole numbers)
- Returns: `[formatted, 'Amount']` tuple
- Usage: Chart tooltips
- Example: `50000` → `["IDR 50,000", "Amount"]`

**Why Two Functions?**
- Display: Full precision with decimals
- Tooltips: Cleaner without decimals

---

### 6. Widget Control Panel

**Component**: Dropdown with gear icon

**Visual Design**:
- Button: 40×40px, transparent background
- Hover effect: Light gray background (#f3f4f6)
- Icon: RiListSettingsLine (24px, gray)
- Dropdown: 280px wide, rounded corners, shadow

**Dropdown Content**:
```
Customize Widgets
Show or hide widgets on this page
─────────────────────────
☑ Balance Trend
☑ Expenses by Category
☑ Income vs Expenses (Yearly)
☑ Cash Flow Analysis
☑ Recent Transactions
☑ Budget Status
```

**Behavior**:
- Check/uncheck to show/hide widgets
- Changes apply immediately
- No "Save" button needed

---

### 7. Expense Data Filtering

**Purpose**: Remove noise from pie chart

**Logic**:
```typescript
const filteredExpenseData = useMemo(() => {
  const nonZero = expensesFromService.filter(item => item.value > 0);
  const total = nonZero.reduce((sum, item) => sum + item.value, 0);
  return total > 0 
    ? nonZero.filter(item => (item.value / total) >= 0.005)  // >= 0.5%
    : [];
}, [expensesFromService]);
```

**Filter Threshold**: 0.5% of total (rounds to 1% or more in pie chart)

**Example**:
- Total expenses: 10,000,000 IDR
- Category with 40,000 IDR (0.4%): **Filtered out**
- Category with 60,000 IDR (0.6%): **Shown**

**Why?** Improves chart readability by removing tiny slices

---

### 8. Loading & Empty States

#### Loading State
- Shown while fetching data
- `loading` state set to `true` during fetch
- Each widget shows: "Loading..." (centered)

#### Empty States (per widget)
- **Expenses by Category**: "No expense data available"
- **Income vs Expenses**: "No income/expense data available"
- **Cash Flow**: "No cash flow data available"
- **Recent Transactions**: "No recent transactions"
- **Budget Status**: "No budget data available"

**Styling**: Centered, muted gray text, padding

---

### 9. Drag & Drop Implementation

**Same as Dashboard**:

```typescript
const sensors = useSensors(
  useSensor(PointerSensor, { activationConstraint: { distance: 8 } }),
  useSensor(KeyboardSensor, { coordinateGetter: sortableKeyboardCoordinates })
);

const handleDragEnd = (event) => {
  const { active, over } = event;
  if (over && active.id !== over.id) {
    setWidgetOrder(items => {
      const oldIndex = items.indexOf(active.id);
      const newIndex = items.indexOf(over.id);
      return arrayMove(items, oldIndex, newIndex);
    });
  }
};
```

**Visual Feedback**:
- `DragOverlay`: Shows widget card during drag
- Opacity 0.5 for original card position
- Full opacity for drag overlay

**Grid Layout**: `rectSortingStrategy` (vs vertical list)

---

## State Management

### Local State (useState)

```typescript
const [expensesFromService, setExpensesFromService] = useState<ExpenseByCategory[]>([]);
const [incomeExpenseData, setIncomeExpenseData] = useState<IncomeExpenseTrend[]>([]);
const [cashFlowData, setCashFlowData] = useState<CashFlowData[]>([]);
const [recentTransactions, setRecentTransactions] = useState<DashboardTransaction[]>([]);
const [budgetStatus, setBudgetStatus] = useState<BudgetStatus[]>([]);
const [loading, setLoading] = useState(true);
const [widgetOrder, setWidgetOrder] = useState<string[]>(() => loadWidgetOrder());
const [activeId, setActiveId] = useState<string | null>(null);  // Drag state
const [showControlPanel, setShowControlPanel] = useState(false);
const [widgetVisibility, setWidgetVisibility] = useState({
  balanceTrend: true,
  expensesByCategory: true,
  incomeVsExpenses: true,
  cashFlow: true,
  recentTransactions: true,
  budgetStatus: true,
});
```

### Computed State (useMemo)

```typescript
const filteredExpenseData = useMemo(() => {
  // Filter out categories < 0.5%
}, [expensesFromService]);
```

### Context State (usePeriodNavigation)

```typescript
const { state: { periodLabel, activePeriod, customRangeDraft } } = usePeriodNavigation();
```

### Persistent State (localStorage)

**Key**: `'reports-widget-order'`

**Load Function**:
```typescript
const loadWidgetOrder = (): string[] => {
  const stored = localStorage.getItem(WIDGET_ORDER_STORAGE_KEY);
  if (stored) {
    const parsed = JSON.parse(stored);
    // Validate and merge with defaults if needed
    return parsed;
  }
  return DEFAULT_WIDGET_ORDER;
};
```

**Save on Change**:
```typescript
useEffect(() => {
  localStorage.setItem(WIDGET_ORDER_STORAGE_KEY, JSON.stringify(widgetOrder));
}, [widgetOrder]);
```

---

## Component Hierarchy

```
<Reports>
  └─ PeriodNavigationProvider
      └─ ReportsContent
          <Container>
            <Row>
              <Col>
                <h1>Reports & Analytics</h1>
                <div> {/* Header controls */}
                  <PeriodNavigation>
                    <PeriodRangeSelector />
                  </PeriodNavigation>
                  <Dropdown> {/* Widget control panel */}
                    <Form.Check /> (multiple)
                  </Dropdown>
                </div>
              </Col>
            </Row>
            
            <DndContext>
              <SortableContext>
                <Row>
                  <SortableWidgetCard /> (multiple, filtered)
                </Row>
              </SortableContext>
              
              <DragOverlay>
                <WidgetCard /> {/* Visual feedback */}
              </DragOverlay>
            </DndContext>
          </Container>
```

---

## Dependencies

### Libraries
- **react-bootstrap**: Container, Row, Col, Form, Dropdown
- **react-icons**: RiListSettingsLine
- **@dnd-kit**: DndContext, SortableContext, DragOverlay, sensors
- **recharts**: (via chart components)

### Internal Components
- **PeriodNavigation, PeriodRangeSelector**: Period filtering
- **CategoryPieChart**: Expense breakdown
- **IncomeExpenseBarChart**: Income vs expense comparison
- **CashFlowChart**: Cash flow analysis (Reports-specific)
- **RecentTransactionsList**: Transaction list
- **BudgetStatusList**: Budget progress
- **BalanceTrendWidget**: Balance over time
- **SortableWidgetCard, WidgetCard**: Widget wrappers

### Services
- **analyticsService**: Analytics data
- **budgetService**: Budget data

---

## Performance Optimizations

1. **Parallel Fetching**: `Promise.all()` for simultaneous requests
2. **Memoization**: `useMemo()` for filtered expense data
3. **Callbacks**: `useCallback()` for drag handlers
4. **Lazy Loading**: Report page lazily loaded
5. **Code Splitting**: Suspense boundary
6. **LocalStorage Caching**: Widget order persists

---

## Comparison with Dashboard

### Similarities
- Customizable widget system
- Drag-and-drop reordering
- Period navigation
- Widget visibility toggles
- LocalStorage persistence
- Same chart components

### Differences

| Feature | Dashboard | Reports |
|---------|-----------|---------|
| **Account Cards** | ✅ Yes | ❌ No |
| **Cash Flow Widget** | ❌ No | ✅ Yes |
| **Transaction Count** | 20 | 30 |
| **Chart Heights** | 300px | 350px |
| **Storage Key** | `dashboard-widget-order` | `reports-widget-order` |
| **Page Title** | None | "Reports & Analytics" |
| **Layout** | Full width | Full width |

### Why Separate Pages?

1. **Different Focus**: 
   - Dashboard: Quick overview + account management
   - Reports: Deep analytics and insights

2. **Widget Sets**:
   - Dashboard: Account-centric
   - Reports: Analytics-centric (cash flow)

3. **Data Volume**:
   - Dashboard: Recent snapshot (20 transactions)
   - Reports: More historical data (30 transactions, larger charts)

4. **User Intent**:
   - Dashboard: Daily monitoring
   - Reports: Analysis and planning

---

## User Flows

### View Reports Flow
```
1. User navigates to /reports
2. Page loads with loading states
3. Data fetches in parallel
4. Widgets render with data
5. User interacts with period selector
6. Data updates (future enhancement)
```

### Customize Widget Layout Flow
```
1. User clicks gear icon (⚙️)
2. Dropdown opens with checkboxes
3. User checks/unchecks widgets
4. Widgets show/hide immediately
5. User drags widget to reorder
6. Order saves to localStorage
7. Layout persists across sessions
```

### Analyze Cash Flow Flow (Reports-specific)
```
1. User views Cash Flow widget
2. Chart shows income, expense, net lines
3. User hovers over data points
4. Tooltip shows values
5. User identifies trends
6. User changes period to compare
```

---

## Chart Color Scheme

**Predefined Colors**:
```typescript
const COLORS = [
  '#0088FE',  // Blue
  '#00C49F',  // Teal
  '#FFBB28',  // Yellow
  '#FF8042',  // Orange
  '#8884D8',  // Purple
  '#FF6B6B',  // Red
  '#4ECDC4',  // Cyan
  '#95E1D3',  // Mint
];
```

**Chart-Specific Colors**:
- Income: `#2ecc71` (Green)
- Expense: `#e74c3c` (Red)
- Net/Balance: `#000000` (Black)
- Balance Trend Line: `#2563eb` (Blue)

---

## Empty State Handling

**All Widgets Check for Empty Data**:

```typescript
loading ? (
  <div>Loading...</div>
) : data.length === 0 ? (
  <div>No data available</div>
) : (
  <ChartComponent data={data} />
)
```

**Progressive Enhancement**:
- Loading state → Empty state → Data display
- Graceful degradation if API fails
- No error messages to user (logged to console)

---

## LocalStorage Management

### Widget Order

**Key**: `'reports-widget-order'`

**Structure**: JSON array of widget IDs
```json
[
  "balanceTrend",
  "expensesByCategory",
  "incomeVsExpenses",
  "cashFlow",
  "recentTransactions",
  "budgetStatus"
]
```

**Validation**:
- Checks if array length matches expected
- Merges with defaults if outdated
- Falls back to defaults on error

**Persistence**:
- Saved on every reorder
- Loaded on mount
- Synced across tabs (same origin)

---

## Accessibility

1. **Keyboard Navigation**: Keyboard sensor for drag-and-drop
2. **ARIA Labels**: Widget control panel properly labeled
3. **Focus Management**: Focusable dropdown toggle
4. **Semantic HTML**: Proper heading hierarchy (H1 for page title)

---

## Error Handling

### Fetch Errors
- Caught and logged to console
- No user-facing error messages
- Empty states shown if data unavailable
- Graceful degradation

### LocalStorage Errors
- Try-catch around read/write operations
- Falls back to default widget order
- Console logs error details

### Render Errors
- Widget not found: Returns `null` (no crash)
- Invalid widget ID: Filtered out
- Missing data: Empty state component

---

## Key Considerations for Rewrite

1. **Cash Flow Widget**: Unique to Reports, ensure API endpoint exists
2. **Transaction Count**: Fetch 30 (vs 20 on Dashboard)
3. **Chart Heights**: 350px (vs 300px on Dashboard)
4. **LocalStorage Key**: Different from Dashboard (`reports-` prefix)
5. **Expense Filtering**: 0.5% threshold for pie chart
6. **No Accounts**: No account fetching or display
7. **Period Context**: Wrapped in PeriodNavigationProvider
8. **Widget Visibility**: All visible by default
9. **Drag Strategy**: rectSortingStrategy (grid layout)
10. **Currency Formatting**: Two functions (display vs tooltip)
11. **Color Constants**: COLORS array for charts
12. **Empty States**: Specific message per widget
13. **Loading States**: Shown during initial fetch
14. **No Data Refresh**: Static data (no polling/websockets)
15. **Widget Count**: 6 widgets (vs 5 on Dashboard)

---

## Future Enhancements (Not Implemented)

1. **Export Reports**: PDF/CSV export functionality
2. **Date Range Filtering**: Actual API integration with period selector
3. **Chart Interactions**: Drill-down into categories
4. **Comparison Mode**: Compare multiple periods
5. **Custom Date Ranges**: More granular date selection
6. **Print Styling**: Print-friendly CSS
7. **Widget Customization**: Choose which data to show
8. **Report Scheduling**: Email reports
9. **Data Export**: Download raw data
10. **Advanced Filters**: Filter by account, category

---

## CSS Classes (Expected)

**Page Container**: `.reports-page` (if styled separately)

**Widget Control**: Custom inline styles (no specific classes)

**Charts**: Component-specific classes from Recharts and custom components

**Dropdown**: Bootstrap classes + custom inline styles
