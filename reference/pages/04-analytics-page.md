# Analytics Page

## Routes
- **URL**: `/analytics`
- **File**: `app/analytics/page.tsx` → `src/views/Analytics/Analytics.tsx`

## Purpose
Detailed analytics page with tabbed reports, advanced filtering, and account drill-down.

## Page Structure

```typescript
app/analytics/page.tsx (wrapper)
  └─ ProtectedShell
      └─ Suspense
          └─ LazyAnalytics (src/views/Analytics/Analytics.tsx)
              └─ PeriodNavigationProvider
                  └─ AnalyticsContent
```

## Key Features

### 1. Tab-Based Navigation

**Four Main Tabs**:

1. **📊 Incomes & Expenses Report** (default)
   - Component: `IncomesExpensesReport`
   - Breakdown of income and expenses
   - Category analysis

2. **📈 Balance Trend**
   - Component: `BalanceTrend`
   - Balance over time for each account
   - Clickable accounts for drill-down

3. **💰 Cash Flow**
   - Component: `CashFlow`
   - Net cash flow analysis
   - Income vs expense trends

4. **📉 Advanced Charts and Reports**
   - Component: `AdvancedCharts`
   - Additional analytics and visualizations

**Tab State**: `activeTab` (useState)

**Tab Type**:
```typescript
type TabKey = 'income-expense' | 'balance-trend' | 'cash-flow' | 'advanced-charts';
```

**Rendering Logic**:
```typescript
const renderTabContent = () => {
  switch (activeTab) {
    case 'income-expense': return <IncomesExpensesReport />;
    case 'balance-trend': return <BalanceTrend />;
    case 'cash-flow': return <CashFlow />;
    case 'advanced-charts': return <AdvancedCharts />;
    default: return <IncomesExpensesReport />;
  }
};
```

---

### 2. Two-Column Layout

**Left Sidebar (3/12 columns, desktop only)**:
- Reuses `DesktopFilterSidebar` from Transactions page
- Title: "Analytics"
- Filter controls:
  - Search term
  - Sort options
  - Category filters (multi-select)
  - Account filters (multi-select)
  - Amount range (min/max)
  - Filter visibility toggles

**Right Content (9/12 columns)**:
- Period navigation controls (centered)
- Export button (top-right)
- Tab navigation pills
- Active tab content

**Mobile Layout**:
- Sidebar hidden (`.d-none .d-lg-block`)
- Export button shown above period navigation
- Tabs scroll horizontally if needed

---

### 3. Filter System (Shared with Transactions)

**Hook**: `useFilterData({ filterVisibilityStorageKey: FILTER_VISIBILITY_STORAGE_KEY })`

**LocalStorage Key**: `'finance-app-analytics-filter-visibility'`

**Provides**:
```typescript
{
  // Search & Sort
  searchTerm, setSearchTerm,
  sortOption, setSortOption,
  
  // Category Filters
  selectedCategories, setSelectedCategories,
  categoryTree, parentCategoryColors, categoryIcons, allCategories,
  
  // Account Filters
  selectedAccounts, setSelectedAccounts,
  accountTree, accountColors, accountIcons, selectableAccounts,
  
  // Amount Range
  minAmount, setMinAmount,
  maxAmount, setMaxAmount,
  
  // Filter Visibility
  filterVisibility, setFilterVisibility,
}
```

**Filter Visibility**: Controls which filter sections are expanded/collapsed

**Sidebar Component**: `DesktopFilterSidebar` (from Transactions)
- Fully reused component
- Same filter UI
- Same functionality
- Analytics-specific storage key

**Note**: Filters passed to tab components but **not all tabs use them** (implementation varies per tab)

---

### 4. Account Detail Drill-Down

**Feature**: Click account in Balance Trend tab → View account details

**Flow**:
```
1. User clicks account name in Balance Trend
   ↓
2. handleAccountClick(accountName)
   ↓
3. Find account in accountsData
   ↓
4. Convert AccountBalance → Account type
   ↓
5. setShowAccountDetail(true)
   ↓
6. Page replaces content with AccountDetail component
   ↓
7. User views account transactions/details
   ↓
8. User clicks "Back"
   ↓
9. handleBackFromAccountDetail()
   ↓
10. Returns to Analytics tab view
```

**State Management**:
```typescript
const [showAccountDetail, setShowAccountDetail] = useState(false);
const [selectedAccount, setSelectedAccount] = useState<Account | null>(null);
```

**Conditional Rendering**:
```typescript
if (showAccountDetail && selectedAccount) {
  return <AccountDetail account={selectedAccount} onBack={...} />;
}
// Otherwise render tabs
```

**Type Conversion**: `convertToAccount(accountBalance: AccountBalance): Account`

**Why Needed?** 
- Balance Trend uses `AccountBalance` from API
- AccountDetail expects `Account` type
- Conversion handles icon resolution and defaults

---

### 5. Account Data Fetching

**API Call**: On mount
```typescript
useEffect(() => {
  const balanceTrendData = await analyticsService.fetchBalanceTrend();
  setAccountsData(balanceTrendData.accounts);
}, []);
```

**Data Type**: `AccountBalance[]`
```typescript
interface AccountBalance {
  name: string;
  type: string;
  balance: number;
  color: string;
  icon?: string;
}
```

**Usage**: 
- Passed to Balance Trend tab
- Used for account detail drill-down conversion

---

### 6. Period Navigation

**Same as Dashboard/Reports**:
- Context: `PeriodNavigationProvider`
- Selector: `PeriodRangeSelector`
- State: `periodLabel`, `activePeriod`, `customRangeDraft`
- Options: Today, This Week, This Month, This Year, Custom

**Prop Passing**: `currentMonth={periodLabel}` passed to all tab components

**Future Enhancement**: Actual API integration with period filtering

---

### 7. Export Functionality (Placeholder)

**Button Display**:
- **Desktop**: Top-right, next to period selector
- **Mobile**: Above period selector

**Current Implementation**:
```typescript
<Button variant="outline-success" size="sm">
  <FaFileExport className="me-2" />
  Export
</Button>
```

**Status**: Not implemented (no onClick handler)

**Future**: 
- Export current tab data to PDF/CSV
- Export all analytics
- Email reports

---

### 8. Tab Components

#### 1. IncomesExpensesReport (`./components/IncomesExpensesReport`)

**Purpose**: Income vs expense breakdown

**Props**: `currentMonth` (string)

**Features**:
- Category-wise breakdown
- Income/expense comparison
- Pie charts or bar charts
- Total summaries

---

#### 2. BalanceTrend (`./components/BalanceTrend`)

**Purpose**: Account balance trends over time

**Props**: 
- `currentMonth` (string)
- `onAccountClick` (callback)

**Features**:
- Line charts per account
- Clickable account names
- Balance history
- Account comparison

**Interaction**: Clicking account name triggers drill-down to AccountDetail

---

#### 3. CashFlow (`./components/CashFlow`)

**Purpose**: Net cash flow analysis

**Props**: `currentMonth` (string)

**Features**:
- Income vs expense vs net
- Cash flow trends
- Period-over-period comparison

---

#### 4. AdvancedCharts (`./components/AdvancedCharts`)

**Purpose**: Additional analytics and visualizations

**Props**: `currentMonth` (string)

**Features**:
- Advanced chart types
- Custom reports
- Detailed breakdowns

---

## Component Hierarchy

```
<Analytics>
  └─ PeriodNavigationProvider
      └─ AnalyticsContent
          <Container>
            <Row>
              <Col lg={3}> {/* Desktop only */}
                <DesktopFilterSidebar />
              </Col>
              
              <Col lg={9}>
                {showAccountDetail ? (
                  <AccountDetail />
                ) : (
                  <>
                    {/* Export button (mobile) */}
                    
                    <div> {/* Header */}
                      <PeriodNavigation>
                        <PeriodRangeSelector />
                      </PeriodNavigation>
                      
                      {/* Export button (desktop) */}
                      
                      <Nav> {/* Tabs */}
                        <Nav.Item> Income & Expense </Nav.Item>
                        <Nav.Item> Balance Trend </Nav.Item>
                        <Nav.Item> Cash Flow </Nav.Item>
                        <Nav.Item> Advanced Charts </Nav.Item>
                      </Nav>
                    </div>
                    
                    <div> {/* Tab content */}
                      {renderTabContent()}
                    </div>
                  </>
                )}
              </Col>
            </Row>
          </Container>
```

---

## State Management

### Local State (useState)

```typescript
const [activeTab, setActiveTab] = useState<TabKey>('income-expense');
const [showAccountDetail, setShowAccountDetail] = useState(false);
const [selectedAccount, setSelectedAccount] = useState<Account | null>(null);
const [accountsData, setAccountsData] = useState<AccountBalance[]>([]);
```

### Hook State (useFilterData)

All filter state managed by shared hook:
- Search, sort, categories, accounts, amount range
- Filter visibility (expanded/collapsed sections)

### Context State (usePeriodNavigation)

```typescript
const { state: { periodLabel, activePeriod, customRangeDraft } } = usePeriodNavigation();
```

---

## Type Conversion Logic

### convertToAccount Function

**Purpose**: Convert `AccountBalance` (from API) to `Account` (for UI)

**Transformation**:
```typescript
AccountBalance → Account

{
  name, type, balance, color, icon?
}
↓
{
  id: name.toLowerCase().replace(/\s+/g, '-'),  // Generate ID
  name,
  type,
  balance,
  currency: 'IDR',                               // Default
  backgroundColor: `${color}20`,                 // Alpha channel
  accentColor: color,
  icon: resolveIconComponent(icon),              // String → Component
  iconKey: icon || 'FaWallet',
  isActive: true,                                // Default
  excludeFromStatistics: false,                  // Default
  usability: 'USABLE',                           // Default
  order: 0,                                      // Default
}
```

**Icon Resolution**:
```typescript
const iconLibrary = FaIcons as Partial<Record<string, React.ComponentType>>;
const IconComponent = iconLibrary[iconKey] ?? FaIcons.FaWallet;
```

**Why Needed?**
- API returns minimal account data
- UI components expect full Account type
- Conversion fills in defaults and resolves icons

---

## AccountDetail Integration

**Component**: `AccountDetail` (from Accounts view)

**Props**:
```typescript
{
  account: Account;
  onBack: () => void;
  onEdit: (account: Account) => void;
  onDelete: (accountId: string) => void;
}
```

**Handlers**:

**onBack**: 
```typescript
const handleBackFromAccountDetail = () => {
  setShowAccountDetail(false);
  setSelectedAccount(null);
};
```

**onEdit**: (Not implemented)
```typescript
const handleEditAccount = (account: Account) => {
  console.warn('Edit account:', account);
  // TODO: Implement edit modal
};
```

**onDelete**: (Not implemented)
```typescript
const handleDeleteAccount = (accountId: string) => {
  console.warn('Delete account:', accountId);
  setShowAccountDetail(false);
  setSelectedAccount(null);
};
```

**Full-Page Replacement**:
- When `showAccountDetail === true`, entire page content replaced
- No sidebar, no tabs, only AccountDetail
- "Back" button returns to Analytics

---

## Responsive Design

### Desktop (lg+)
```
┌─────────────────────────────────────────┐
│ [Sidebar]     [Period Nav]  [Export]    │
│               [Tab Pills]                │
│ [Filters]     [Tab Content]             │
│               ...                        │
└─────────────────────────────────────────┘
```

### Mobile
```
┌─────────────────────────────────────────┐
│              [Export]                    │
│          [Period Nav]                    │
│          [Tab Pills]                     │
│          [Tab Content]                   │
│              ...                         │
└─────────────────────────────────────────┘
```

**Breakpoints**:
- Sidebar: `.d-none .d-lg-block` (hidden on mobile)
- Export button: Positioned differently on mobile vs desktop
- Tab pills: Scroll horizontally on mobile

---

## Tab Navigation Pills

**Component**: Bootstrap `Nav` with `variant="pills"`

**Styling**:
- Pills (rounded buttons)
- Active state (highlighted)
- Icons: Unicode emoji (📊, 📈, 💰, 📉)
- Flex layout: Icon + text

**Tab Structure**:
```tsx
<Nav.Item>
  <Nav.Link
    active={activeTab === 'income-expense'}
    onClick={() => setActiveTab('income-expense')}
  >
    <span className="tab-icon">📊</span>
    Incomes & Expenses Report
  </Nav.Link>
</Nav.Item>
```

**CSS Class**: `.analytics-tabs` (custom styling)

---

## Dependencies

### Libraries
- **react-bootstrap**: Container, Row, Col, Nav, Button
- **react-icons**: FaFileExport, FaIcons (for icon resolution)

### Internal Components
- **PeriodNavigation, PeriodRangeSelector**: Period filtering
- **DesktopFilterSidebar**: Filter controls (from Transactions)
- **IncomesExpensesReport**: Income/expense tab
- **BalanceTrend**: Balance trend tab
- **CashFlow**: Cash flow tab
- **AdvancedCharts**: Advanced charts tab
- **AccountDetail**: Account drill-down view (from Accounts)

### Hooks
- **useFilterData**: Shared filter state (from Transactions)
- **usePeriodNavigation**: Period state

### Services
- **analyticsService**: `fetchBalanceTrend()`

### Types
- **Account**: From Accounts view
- **AccountBalance**: From analyticsService
- **TabKey**: Custom type for tab identifiers

---

## User Flows

### View Analytics Flow
```
1. User navigates to /analytics
2. Page loads with "Income & Expenses" tab active
3. Data fetches in background
4. Tab content renders
5. User can click tabs to switch views
```

### Switch Tab Flow
```
1. User clicks tab (e.g., "Balance Trend")
2. setActiveTab('balance-trend')
3. renderTabContent() switches component
4. BalanceTrend component renders
5. New data/charts displayed
```

### Account Drill-Down Flow
```
1. User viewing "Balance Trend" tab
2. User clicks account name
3. handleAccountClick(accountName)
4. Find account in accountsData
5. Convert to Account type
6. setShowAccountDetail(true)
7. Page replaces content with AccountDetail
8. User views transactions for that account
9. User clicks "Back"
10. Returns to Balance Trend tab
```

### Filter Analytics Flow
```
1. User adjusts filters in sidebar
2. Selected categories/accounts/amounts update
3. Filters stored in state (via useFilterData)
4. Tab components receive updated filters
5. Charts/data update (if implemented)
```

### Period Selection Flow
```
1. User clicks period selector
2. Chooses "This Month" (or custom range)
3. periodLabel updates
4. Passed as currentMonth to tabs
5. Tabs update data (if implemented)
```

---

## Performance Considerations

### Account Data Fetching
- Fetched once on mount
- Stored in state
- Reused for drill-down
- No refetch on tab change

### Filter State
- Managed by shared hook
- Persistent across tab switches
- LocalStorage for filter visibility

### Tab Rendering
- Only active tab rendered
- Switch re-renders new tab component
- Previous tab unmounted

### AccountDetail View
- Full page replacement (not modal)
- Unmounts tab content
- Remounts tabs on back

---

## LocalStorage Keys

1. **Filter Visibility**: `'finance-app-analytics-filter-visibility'`
   - Stores which filter sections are expanded
   - Separate from Transactions page

---

## Error Handling

### Account Fetch Error
```typescript
try {
  const balanceTrendData = await analyticsService.fetchBalanceTrend();
  setAccountsData(balanceTrendData.accounts);
} catch (err) {
  console.error('Failed to load accounts:', err);
  // Graceful degradation: empty accountsData
}
```

**No User Feedback**: Silent failure, console log only

### Missing Account for Drill-Down
```typescript
const accountBalance = accountsData.find(acc => acc.name === accountName);
if (accountBalance) {
  // Proceed with drill-down
}
// Otherwise: No action (silent fail)
```

### Tab Component Errors
- Handled by each tab component
- No error boundaries at Analytics level

---

## CSS Classes

**Page**: `.analytics-header`, `.analytics-tabs`, `.analytics-content`

**Tabs**: Bootstrap Nav classes + custom `.analytics-tabs`

**Tab Icons**: `.tab-icon` (inline span)

---

## Key Considerations for Rewrite

1. **Shared Filter Hook**: Reuses `useFilterData` from Transactions
2. **Tab System**: Simple state-based tab switching (not React Router)
3. **Account Conversion**: `AccountBalance` → `Account` type transformation
4. **Full-Page Drill-Down**: Not modal, replaces entire content
5. **Icon Resolution**: String keys → React components via FaIcons
6. **Period Context**: Wrapped in PeriodNavigationProvider
7. **Sidebar Reuse**: Exact same component as Transactions page
8. **Export Placeholder**: Button exists but not implemented
9. **Filter Storage**: Separate localStorage key from Transactions
10. **Mobile Responsive**: Sidebar hidden, button repositioned
11. **Tab Components**: Separate files in `./components/` directory
12. **Unicode Emojis**: Used for tab icons (📊, 📈, 💰, 📉)
13. **No Routing**: Tab state managed locally (not URL-based)
14. **AccountDetail Integration**: Shared component from Accounts view
15. **Minimal Error Handling**: Console logs only, no user feedback

---

## Tab Component Files

**Location**: `src/views/Analytics/components/`

1. **IncomesExpensesReport.tsx** - Income/expense analysis
2. **BalanceTrend.tsx** - Balance trends with clickable accounts
3. **CashFlow.tsx** - Cash flow analysis
4. **AdvancedCharts.tsx** - Advanced visualizations

**Index File**: `src/views/Analytics/components/index.ts`
- Exports all tab components
- Centralized import point

---

## Differences from Dashboard/Reports

| Feature | Dashboard | Reports | Analytics |
|---------|-----------|---------|-----------|
| **Layout** | Widget grid | Widget grid | Tabs + Sidebar |
| **Customization** | Drag-drop widgets | Drag-drop widgets | Tab navigation |
| **Filters** | None | None | Full sidebar |
| **Account View** | Cards | None | Detail drill-down |
| **Period Nav** | ✅ Yes | ✅ Yes | ✅ Yes |
| **Focus** | Overview | Analytics | Deep analysis |

**Unique Features**:
- Tab-based navigation (not widgets)
- Filter sidebar (like Transactions)
- Account detail drill-down
- Export button (placeholder)

---

## Future Enhancements (Not Implemented)

1. **Export Functionality**: PDF/CSV/Excel export
2. **Period Filtering**: API integration with date ranges
3. **Filter Integration**: Apply filters to tab data
4. **Account Edit**: Implement edit modal from drill-down
5. **Account Delete**: Implement delete with confirmation
6. **URL-based Tabs**: Shareable links to specific tabs
7. **Comparison Mode**: Compare multiple periods
8. **Custom Reports**: User-defined report templates
9. **Scheduled Reports**: Email reports on schedule
10. **Print Styling**: Print-friendly CSS
11. **Mobile Filter Offcanvas**: Filter panel for mobile
12. **Breadcrumbs**: Navigation trail (Analytics > Balance Trend > Account)
13. **Deep Linking**: Direct links to account details
14. **Filter Presets**: Save common filter combinations
15. **Data Export Per Tab**: Export tab-specific data
