# Components Overview

## Directory Structure

```
src/components/
├── Records/                           # Transaction/record management components
│   ├── RecordsHeader.tsx             # Bulk actions header with selection controls
│   ├── RecordsList.tsx               # Grouped transaction list with selection
│   └── index.ts                      # Exports
├── Widget/                           # Dashboard widget components
│   ├── BalanceTrendWidget.tsx        # Balance trend chart with data fetching
│   └── README.md                     # Widget documentation
├── AddAccountModal.tsx               # Account creation/editing modal
├── AmountRangeFilter.tsx             # Dual-range slider for amount filtering
├── BalanceTrendChart.tsx             # Recharts line chart for balance data
├── BudgetStatusList.tsx              # Budget progress display
├── CashFlowChart.tsx                 # Cash flow visualization
├── CategoryPieChart.tsx              # Category expense breakdown chart
├── ErrorBoundary.tsx                 # React error boundary with recovery
├── Header.tsx                        # App navigation header with sidebar
├── IncomeExpenseBarChart.tsx         # Income vs expense comparison chart
├── InputClearButton.tsx              # Input field clear button utility
├── PeriodNavigation.tsx              # Date period navigation controls
├── periodNavigationContext.tsx       # Period navigation state management
├── PeriodRangeSelector.tsx           # Date range picker component
├── periodRangeUtils.ts               # Date range calculation utilities
├── RecentTransactionsList.tsx        # Recent transactions widget
├── ServiceWorkerRegistration.tsx     # PWA service worker setup
├── ToastAlert.tsx                    # Toast notification component
├── WebVitalsReporter.tsx             # Performance monitoring
└── WidgetCards.tsx                   # Widget card containers for dashboard
```

---

## Component Categories

### 🏗️ **Layout & Navigation Components**

#### **Header.tsx**
**Purpose**: Main app navigation with responsive sidebar

**Key Features**:
- Responsive navigation (desktop navbar + mobile offcanvas)
- User profile dropdown with logout
- Quick add transaction button
- Dynamic active page highlighting
- Mobile-first responsive design

**Props**: None (uses contexts)

**Dependencies**:
- `useAuth()` - Authentication state
- `useTransactionModal()` - Quick transaction creation
- `usePathname()` - Active route detection

**Navigation Links**:
```typescript
const navigationLinks = [
  { to: '/', label: 'Dashboard', exact: true },
  { to: '/accounts', label: 'Accounts' },
  { to: '/transactions', label: 'Transactions' },
  { to: '/analytics', label: 'Analytics' },
  { to: '/reports', label: 'Reports' },
  { to: '/settings', label: 'Settings' },
  { to: '/budgets', label: 'Budgets' },
];
```

**Mobile Behavior**:
- Hamburger menu triggers offcanvas sidebar
- Sidebar auto-closes on navigation
- Touch-optimized interactions

---

### 📊 **Chart & Visualization Components**

#### **BalanceTrendChart.tsx**
**Purpose**: Recharts line chart for balance over time

**Props**:
```typescript
interface BalanceTrendChartProps {
  data: BalanceDataPoint[];
  height?: number | `${number}%`;
  lineColor?: string;
  formatValue?: (value: number) => string;
}
```

**Features**:
- Responsive container
- Custom tooltip with formatted values
- Simplified Y-axis labels (K/M notation)
- Smooth line animation
- Touch-friendly hover/tap interactions

**Data Format**:
```typescript
interface BalanceDataPoint {
  date: string;
  balance: number;
}
```

#### **CategoryPieChart.tsx**
**Purpose**: Expense breakdown by category

**Props**:
```typescript
interface CategoryPieChartProps {
  data: CategoryPieChartData[];
  colors?: string[];
  formatValue?: (value: number) => string;
  height?: number | `${number}%`;
  outerRadius?: number;
}
```

**Features**:
- Custom pie slice colors
- Percentage labels on slices
- Custom tooltip with total percentage
- Responsive sizing
- Default color palette

#### **IncomeExpenseBarChart.tsx**
**Purpose**: Income vs expense comparison

**Features**:
- Dual bars (income/expense)
- Custom colors (green/red)
- Tooltip with formatted currency
- Responsive container

#### **CashFlowChart.tsx**
**Purpose**: Cash flow visualization over time

**Features**:
- Bar chart showing net cash flow
- Color coding (positive/negative)
- Time period X-axis
- Formatted currency tooltips

---

### 🏷️ **Business Widget Components**

#### **Widget/BalanceTrendWidget.tsx**
**Purpose**: Self-contained widget with data fetching

**Features**:
- Automatic data fetching from `analyticsService`
- Loading spinner state
- Error state handling
- Balance change indicator (up/down arrows)
- Current vs previous period comparison

**Props**:
```typescript
interface BalanceTrendWidgetProps {
  formatCurrency?: (value: number) => string;
  height?: number;
  lineColor?: string;
}
```

#### **RecentTransactionsList.tsx**
**Purpose**: Dashboard widget showing latest transactions

**Features**:
- Configurable transaction limit
- Category icons and colors
- Formatted amounts
- Click to view details
- Empty state handling

#### **BudgetStatusList.tsx**
**Purpose**: Budget progress tracking widget

**Features**:
- Progress bars for each budget category
- Color-coded status (green/yellow/red)
- Percentage completion
- Spent vs budgeted amounts

#### **WidgetCards.tsx**
**Purpose**: Reusable card containers for dashboard widgets

**Features**:
- Consistent styling across widgets
- Loading states
- Error boundaries
- Responsive grid layout

---

### 📋 **Data Management Components**

#### **Records/RecordsList.tsx**
**Purpose**: Advanced transaction list with grouping and selection

**Key Features**:
- **Date Grouping**: Transactions grouped by date
- **Batch Selection**: Individual + day-level checkboxes
- **Sticky Headers**: Date headers stay visible during scroll
- **Rich Display**: Category icons, colors, account names
- **Click Handling**: Tap/click to edit transactions
- **Responsive Design**: Mobile-optimized layout

**Props**:
```typescript
interface RecordsListProps {
  groupedTransactions: GroupedTransactions;
  selectedRecords: Set<string>;
  accountName: string;
  onSelectRecord: (recordId: string) => void;
  onEditRecord: (record: TransactionRecord) => void;
  onDeleteRecord?: (recordId: string) => void;
  formatCurrency: (amount: number) => string;
  showCheckboxes?: boolean;
  showDropdownMenu?: boolean;
  showPayer?: boolean;
  showType?: boolean;
}
```

**Advanced Features**:
- Icon resolution from FontAwesome by name
- Conditional field display (payer, type, etc.)
- Selection propagation (day → individual transactions)
- Optimized rendering for large lists

#### **Records/RecordsHeader.tsx**
**Purpose**: Bulk actions header with selection controls

**Features**:
- **Selection State Display**: "Selected X items"
- **Bulk Actions**: Edit, Export, Delete buttons
- **Select All Toggle**: Master checkbox
- **Clear Selection**: Reset all selections
- **Responsive Layout**: Collapses on mobile

**Props**:
```typescript
interface RecordsHeaderProps {
  selectedCount: number;
  totalCount: number;
  allSelected: boolean;
  onSelectAll: () => void;
  onClearSelection: () => void;
  onBulkEdit?: () => void;
  onBulkExport?: () => void;
  onBulkDelete?: () => void;
  summaryText?: string;
  showBulkActions?: boolean;
}
```

**States**:
- **No Selection**: Shows "Found X records"
- **Has Selection**: Shows bulk action buttons + close button

---

### 🔧 **Form & Input Components**

#### **AddAccountModal.tsx**
**Purpose**: Complex account creation/editing modal

**Key Features**:
- **Dual Mode**: Create new or edit existing accounts
- **Rich Form**: Name, type, color, icon, initial amount
- **Icon Picker**: FontAwesome icon selection
- **Color Picker**: Hex color input with validation
- **Currency Formatting**: Real-time amount formatting
- **Validation**: Schema-based validation with error display
- **Account Types**: Predefined account types with icons

**Props**:
```typescript
interface AddAccountModalProps {
  show: boolean;
  onHide: () => void;
  onSubmit: (form: NewAccountForm) => void;
  title?: string;
  initialValue?: NewAccountForm;
  accountId?: string;
  isEditMode?: boolean;
}
```

**Form Structure**:
```typescript
interface NewAccountForm {
  name: string;
  color: string;                    // Hex color
  accountType: string;              // From ACCOUNT_TYPES
  initialAmount: string;            // Formatted currency
  currency: string;                 // Default 'IDR'
  excludeFromStatistics: boolean;
  iconKey: string;                  // FontAwesome icon name
  isActive: boolean;
  usability: 'USABLE' | 'PROTECTED';
}
```

**Account Types Available**:
- General, Cash, Checking account, Credit account
- Savings account, Bonus, Life insurance account
- Investment account, Loan, Mortgage, Overdraft account

#### **AmountRangeFilter.tsx**
**Purpose**: Dual-range slider for amount filtering

**Features**:
- **Dual Sliders**: Min/max amount selection
- **Input Fields**: Direct numeric input
- **Currency Formatting**: Real-time IDR formatting
- **Validation**: Min <= Max constraint
- **Responsive**: Works on mobile touch

**Props**:
```typescript
interface AmountRangeFilterProps {
  minAmount: number;
  maxAmount: number;
  onMinAmountChange: (value: number) => void;
  onMaxAmountChange: (value: number) => void;
  currency?: string;
  minLimit?: number;      // Default: 0
  maxLimit?: number;      // Default: 20M IDR
  step?: number;          // Default: 100K IDR
}
```

**Implementation**:
- Uses `rc-slider` for range slider
- Uses `react-number-format` for currency inputs
- Syncs slider position with input values

#### **InputClearButton.tsx**
**Purpose**: Clear button for input fields

**Features**:
- Appears when input has value
- X button to clear field
- Customizable positioning
- Accessible (proper aria labels)

---

### 📅 **Date & Period Components**

#### **PeriodNavigation.tsx**
**Purpose**: Navigation controls for date periods

**Features**:
- Previous/Next period buttons
- Integrated with `PeriodNavigationContext`
- Customizable button styling
- Keyboard navigation support
- Accessible (proper aria labels)

**Props**:
```typescript
interface PeriodNavigationProps {
  onPrevious?: () => void;
  onNext?: () => void;
  disablePrevious?: boolean;
  disableNext?: boolean;
  previousAriaLabel?: string;
  nextAriaLabel?: string;
  className?: string;
  children: ReactNode;          // Period display content
}
```

#### **periodNavigationContext.tsx**
**Purpose**: Centralized period/date range state management

**Key Features**:
- **Period Types**: month, year, week, custom
- **Preset Ranges**: Today, This Week, This Month, etc.
- **Custom Ranges**: Start/end date picker
- **Navigation**: Shift periods forward/backward
- **Formatting**: Automatic period label generation

**State Structure**:
```typescript
interface PeriodNavigationState {
  activePeriod: ActivePeriodMeta;
  periodLabel: string;           // "November 2024"
  dateRange: PeriodRange;        // { start: '2024-11-01', end: '2024-11-30' }
  customRangeDraft: PeriodRange; // Temporary custom range
}
```

**Actions Available**:
```typescript
interface PeriodNavigationActions {
  handleSelectPreset: (presetKey: string) => void;
  handleSelectMonth: (year: number, month: number) => void;
  handleSelectYear: (year: number) => void;
  handleCustomDraftChange: (range: Partial<PeriodRange>) => void;
  handleCustomApply: (range?: Partial<PeriodRange>) => void;
  handleQuickSelect: (range: PeriodRange, label: string) => void;
  shiftPeriod: (direction: number) => void;
}
```

#### **PeriodRangeSelector.tsx**
**Purpose**: Comprehensive date range picker UI

**Features**:
- **Preset Buttons**: Quick select common periods
- **Month/Year Dropdowns**: Specific month selection
- **Custom Date Inputs**: Start/end date pickers
- **Validation**: Ensures end >= start date
- **Responsive**: Mobile-friendly layout

**Presets Available**:
- Today, Yesterday
- This Week, Last Week
- This Month, Last Month
- This Quarter, Last Quarter
- This Year, Last Year
- All Time

#### **periodRangeUtils.ts**
**Purpose**: Date calculation and formatting utilities

**Key Functions**:
- `getPresetRange(presetKey)` - Calculate preset date ranges
- `getMonthRange(year, month)` - Month boundaries
- `getYearRange(year)` - Year boundaries
- `formatMonthLabel(year, month)` - "November 2024"
- `formatCustomRangeLabel(range)` - "Nov 1 - Nov 30, 2024"
- `parseISODate(iso)` / `toISODate(date)` - Date conversions

---

### 🚨 **Error Handling & System Components**

#### **ErrorBoundary.tsx**
**Purpose**: React error boundary with user-friendly recovery

**Features**:
- **Error Catching**: Catches JavaScript errors in component trees
- **Error Logging**: Console logging in development
- **User-Friendly UI**: Error message with recovery options
- **Error ID Generation**: Unique error tracking IDs
- **Recovery Actions**: Retry, go home buttons
- **Development Mode**: Additional error details in dev

**Props**:
```typescript
interface Props {
  children: ReactNode;
  fallback?: (error: Error, reset: () => void) => ReactNode;
  onError?: (error: Error, errorInfo: ErrorInfo) => void;
  isolate?: boolean; // Isolate error to component tree
}
```

**Error UI Features**:
- Branded error page with app styling
- Error ID for support/debugging
- Multiple recovery options
- Responsive design

#### **ToastAlert.tsx**
**Purpose**: Bootstrap-based toast notifications

**Features**:
- **Multiple Severities**: success, info, warning, error
- **Auto-hide**: Configurable timeout
- **Positioning**: 9 position options
- **Responsive**: Works on all screen sizes
- **Accessible**: Proper ARIA attributes

**Props**:
```typescript
interface ToastAlertProps {
  open: boolean;
  onClose?: (event: SyntheticEvent | Event, reason?: string) => void;
  severity?: 'success' | 'info' | 'warning' | 'error';
  message: ReactNode;
  autoHideDuration?: number;
  anchorOrigin?: AnchorOrigin;
}
```

**Position Options**:
- Top: start, center, end
- Bottom: start, center, end

---

### ⚙️ **System & Performance Components**

#### **ServiceWorkerRegistration.tsx**
**Purpose**: Progressive Web App service worker setup

**Features**:
- Service worker registration
- Update detection and prompts
- Offline capability setup
- Browser compatibility checking
- User notification for app updates

#### **WebVitalsReporter.tsx**
**Purpose**: Performance monitoring and reporting

**Features**:
- Core Web Vitals tracking
- Performance metric collection
- Configurable reporting endpoint
- Non-blocking performance monitoring

---

## 🎯 **Key Implementation Patterns**

### **1. Responsive Design**
**Pattern**: Mobile-first with Bootstrap classes
```typescript
// Desktop hidden, mobile visible
<div className="d-lg-none">Mobile Content</div>

// Mobile hidden, desktop visible  
<div className="d-none d-lg-block">Desktop Content</div>
```

### **2. Icon Resolution**
**Pattern**: Dynamic FontAwesome icon loading
```typescript
const resolveIconComponent = (iconName?: string) => {
  if (!iconName || !Object.hasOwn(FaIcons, iconName)) {
    return undefined;
  }
  return FaIcons[iconName as keyof typeof FaIcons];
};
```

### **3. Context Integration**
**Pattern**: Hook-based context consumption
```typescript
const { openTransactionModal } = useTransactionModal();
const { logout } = useAuth();
const { state, actions } = usePeriodNavigation();
```

### **4. Error Handling**
**Pattern**: Try-catch with user feedback
```typescript
try {
  await apiCall();
  showToast('Success!', 'success');
} catch (error) {
  console.error('Error:', error);
  setError(error.message);
}
```

### **5. Loading States**
**Pattern**: Boolean loading state with spinners
```typescript
if (loading) {
  return <Spinner animation="border" />;
}
if (error) {
  return <Alert variant="danger">{error}</Alert>;
}
```

### **6. Form Validation**
**Pattern**: Schema-based validation with error display
```typescript
const [errors, setErrors] = useState<Record<string, string>>({});

const validate = () => {
  const result = schema.safeParse(formData);
  if (!result.success) {
    setErrors(formatZodErrors(result.error));
    return false;
  }
  setErrors({});
  return true;
};
```

---

## 🔗 **Component Dependencies**

### **External Libraries**
- **react-bootstrap**: UI components and layout
- **react-icons**: FontAwesome icons
- **recharts**: Chart components
- **rc-slider**: Range slider component
- **react-number-format**: Currency formatting

### **Internal Dependencies**
- **Contexts**: Auth, TransactionModal, PeriodNavigation
- **Services**: API services for data fetching
- **Utils**: Date formatting, numeric input utilities
- **Types**: TypeScript interfaces for props and data

---

## ✅ **Component Testing Considerations**

### **Unit Test Priorities**
1. **Form Components**: Validation logic, input handling
2. **Chart Components**: Data transformation, formatting
3. **Error Boundary**: Error catching and recovery
4. **Date Components**: Period calculations, formatting

### **Integration Test Priorities**
1. **Records Components**: Selection state management
2. **Navigation**: Route handling, context integration
3. **Modal Components**: Form submission, validation

### **E2E Test Priorities**
1. **Header Navigation**: Mobile/desktop navigation flows
2. **Transaction Management**: Create, edit, bulk operations
3. **Date Filtering**: Period selection, navigation

---

## 🚀 **Performance Optimization**

### **Implemented Optimizations**
- **React.memo**: Chart components for expensive renders
- **useCallback**: Event handlers in frequently re-rendering components
- **useMemo**: Data transformations and computations
- **Lazy Loading**: Large modals and complex components
- **Virtual Scrolling**: Large transaction lists

### **Recommended Enhancements**
- **Code Splitting**: Dynamic imports for chart libraries
- **Debouncing**: Search inputs and filter changes
- **Virtualization**: Very long lists (1000+ items)
- **Image Optimization**: Account/category icons

---

## 📋 **Component Status Summary**

| Component | Complexity | Dependencies | Test Priority | Notes |
|-----------|------------|--------------|---------------|--------|
| **RecordsList** | High | Multiple contexts | High | Core transaction UI |
| **AddAccountModal** | High | Form validation | High | Complex form logic |
| **Header** | Medium | Auth, Navigation | Medium | Responsive navigation |
| **BalanceTrendChart** | Medium | Recharts | Medium | Chart customization |
| **PeriodNavigation** | Medium | Period context | High | Date logic critical |
| **ErrorBoundary** | Medium | None | High | Error handling |
| **ToastAlert** | Low | Bootstrap | Low | Simple notification |
| **AmountRangeFilter** | Medium | External libs | Medium | Complex input |

**Total Components**: 22  
**High Complexity**: 3  
**Medium Complexity**: 7  
**Low Complexity**: 12  

All components are production-ready with proper TypeScript typing, error handling, and responsive design patterns.
