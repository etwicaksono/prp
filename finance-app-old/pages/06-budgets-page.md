# Budgets Page

## Routes
- **URL**: `/budgets`
- **File**: `app/budgets/page.tsx` → `src/views/Budgets/Budgets.tsx`

## Purpose
Budget management page for creating and tracking spending limits by category.

## Page Structure

```typescript
app/budgets/page.tsx (wrapper)
  └─ ProtectedShell
      └─ Suspense
          └─ LazyBudgets (src/views/Budgets/Budgets.tsx)
```

## ⚠️ Current Status: **Prototype/Mockup**

**This page is NOT fully implemented:**
- ❌ No API integration
- ❌ No data persistence
- ❌ No edit/delete functionality
- ❌ No period filtering
- ❌ Uses static initial data
- ❌ Create only saves to local state (lost on refresh)

**Needs Implementation**: Full CRUD with API integration

---

## Key Features

### 1. Budget Cards Grid

**Layout**: Responsive grid of budget cards
- **Desktop (lg)**: 3 columns
- **Tablet (md)**: 2 columns
- **Mobile (xs)**: 1 column

**Card Display**:
```
┌─────────────────────────────┐
│ Food             [85%]      │
│ Monthly Budget              │
│                             │
│ Spent:        $250.00       │
│ Budgeted:     $400.00       │
│ [████████░░░░░░░] 85%       │
│ $150.00 left    85% used    │
└─────────────────────────────┘
```

**Visual Elements**:
- Category name (title)
- Period label (Weekly/Monthly/Yearly)
- Percentage badge (color-coded)
- Spent vs budgeted amounts
- Progress bar (color-coded)
- Remaining amount
- Usage percentage

---

### 2. Budget Data Structure

**Budget Interface**:
```typescript
interface Budget {
  id: number;
  category: string;
  budgeted: number;      // Total budget amount
  spent: number;         // Amount spent so far
  period: 'Weekly' | 'Monthly' | 'Yearly';
}
```

**Initial Data** (hardcoded):
```typescript
const INITIAL_BUDGETS: Budget[] = [
  { id: 1, category: 'Food', budgeted: 400, spent: 250, period: 'Monthly' },
  { id: 2, category: 'Transport', budgeted: 200, spent: 120, period: 'Monthly' },
  { id: 3, category: 'Entertainment', budgeted: 150, spent: 180, period: 'Monthly' },
  { id: 4, category: 'Shopping', budgeted: 300, spent: 100, period: 'Monthly' },
  { id: 5, category: 'Utilities', budgeted: 250, spent: 240, period: 'Monthly' },
];
```

**State Management**:
```typescript
const [budgets, setBudgets] = useState<Budget[]>(INITIAL_BUDGETS);
```

⚠️ **Problem**: Data resets on page refresh (not persisted)

---

### 3. Status Color Coding

**Three Status Levels**:

1. **Success (Green)**: < 80% spent
2. **Warning (Yellow)**: 80-99% spent
3. **Danger (Red)**: ≥ 100% spent (over budget)

**Status Function**:
```typescript
const getStatusVariant = (percentage: number): 'success' | 'warning' | 'danger' => {
  if (percentage >= 100) return 'danger';
  if (percentage >= 80) return 'warning';
  return 'success';
};
```

**Applied To**:
- Card border: `border-{variant}`
- Percentage badge: `bg-{variant}`
- Progress bar: `bg-{variant}`

---

### 4. Progress Calculation

**Percentage Formula**:
```typescript
const calculatePercentage = (spent: number, budgeted: number): number => {
  if (budgeted === 0) return 0;
  return Math.min(100, (spent / budgeted) * 100);
};
```

**Features**:
- Handles zero budget (returns 0%)
- Caps at 100% for display (even if over budget)
- Used for progress bar width and badge

**Remaining Amount**:
```typescript
budget.budgeted - budget.spent > 0
  ? `$${(budget.budgeted - budget.spent).toFixed(2)} left`
  : 'Over budget'
```

---

### 5. Create Budget Modal

**Trigger**: "Create Budget" button in page header

**Form Fields**:
1. **Category** (required)
   - Dropdown select
   - Static category list
   - Options: Food, Transport, Shopping, Entertainment, Utilities, Healthcare, Travel, Other

2. **Budgeted Amount** (required)
   - Number input
   - Step: 0.01 (allows cents)
   - Min: 0
   - Placeholder: "Enter amount"

3. **Budget Period** (required, default: Monthly)
   - Dropdown select
   - Options: Weekly, Monthly, Yearly

**Form Interface**:
```typescript
interface CurrentBudgetForm {
  category: string;
  budgeted: string;      // String for input, parsed to number on save
  period: 'Weekly' | 'Monthly' | 'Yearly';
}
```

**Modal State**:
```typescript
const [showModal, setShowModal] = useState<boolean>(false);
const [currentBudget, setCurrentBudget] = useState<CurrentBudgetForm>(createEmptyBudgetForm());
```

---

### 6. Create Budget Flow

**User Flow**:
```
1. User clicks "+ Create Budget"
   ↓
2. Modal opens with empty form
   ↓
3. User selects category (required)
   ↓
4. User enters amount (required)
   ↓
5. User selects period (default: Monthly)
   ↓
6. User clicks "Create Budget"
   ↓
7. Validation checks (category & amount)
   ↓
8. New budget added to state
   ↓
9. Modal closes
   ↓
10. New budget card appears in grid
```

**Save Handler**:
```typescript
const handleSaveBudget = () => {
  if (currentBudget.category && currentBudget.budgeted) {
    const newBudget: Budget = {
      ...currentBudget,
      id: budgets.length + 1,
      spent: 0,                              // New budgets start at $0 spent
      budgeted: parseFloat(currentBudget.budgeted)  // Parse string to number
    };
    
    setBudgets([...budgets, newBudget]);
    setShowModal(false);
  }
};
```

**Validation**:
- Simple existence check (category and budgeted must be filled)
- No detailed validation messages
- No duplicate category check

---

### 7. Static Categories

**Predefined List**:
```typescript
const CATEGORIES: readonly string[] = [
  'Food', 
  'Transport', 
  'Shopping', 
  'Entertainment', 
  'Utilities', 
  'Healthcare', 
  'Travel', 
  'Other'
] as const;
```

**Issues**:
- ❌ Not synced with actual categories in database
- ❌ User can't add custom categories
- ❌ Categories might not match transaction categories

**Should Be**: Fetched from category API

---

### 8. Budget Periods

**Three Period Options**:
```typescript
const PERIODS: readonly ('Weekly' | 'Monthly' | 'Yearly')[] = 
  ['Weekly', 'Monthly', 'Yearly'] as const;
```

**Display**: "{Period} Budget" (e.g., "Monthly Budget")

**Note**: Period doesn't affect calculation (no time-based tracking)

---

## Component Hierarchy

```
<Budgets>
  <Container>
    <div> {/* Header */}
      <h1>Budgets</h1>
      <Button> + Create Budget </Button>
    </div>
    
    <Row> {/* Budget Cards Grid */}
      <Col xs={12} md={6} lg={4}> {/* Per budget */}
        <Card>
          <Card.Body>
            - Category name & period
            - Percentage badge
            - Spent/budgeted amounts
            - Progress bar
            - Remaining/used info
          </Card.Body>
        </Card>
      </Col>
      {/* Repeat for each budget */}
    </Row>
    
    <Modal> {/* Create Budget */}
      <Modal.Header>Create Budget</Modal.Header>
      <Modal.Body>
        <Form>
          <Form.Group> {/* Category */}
            <Form.Select />
          </Form.Group>
          <Form.Group> {/* Amount */}
            <Form.Control type="number" />
          </Form.Group>
          <Form.Group> {/* Period */}
            <Form.Select />
          </Form.Group>
        </Form>
      </Modal.Body>
      <Modal.Footer>
        <Button>Cancel</Button>
        <Button>Create Budget</Button>
      </Modal.Footer>
    </Modal>
  </Container>
</Budgets>
```

---

## State Management

### Local State (useState)

```typescript
const [budgets, setBudgets] = useState<Budget[]>(INITIAL_BUDGETS);
const [showModal, setShowModal] = useState<boolean>(false);
const [currentBudget, setCurrentBudget] = useState<CurrentBudgetForm>(createEmptyBudgetForm());
```

**No Global State**: All state is local

**No Persistence**: State lost on refresh

---

## User Flows

### View Budgets Flow
```
1. User navigates to /budgets
2. Page loads with 5 initial budgets (hardcoded)
3. Budget cards display in grid
4. User sees spending progress for each
```

### Create Budget Flow
```
1. User clicks "+ Create Budget"
2. Modal opens
3. User selects category from dropdown
4. User enters amount (e.g., 500)
5. User selects period (e.g., Monthly)
6. User clicks "Create Budget"
7. New budget added to state
8. Modal closes
9. New card appears in grid with $0 spent
```

### Cancel Creation Flow
```
1. User clicks "+ Create Budget"
2. Modal opens
3. User starts filling form
4. User clicks "Cancel" or X
5. Modal closes
6. Form data discarded
7. No budget created
```

---

## Visual Indicators

### Budget Status Colors

**Color Mapping**:
- **Green (success)**: On track (< 80% spent)
- **Yellow (warning)**: Approaching limit (80-99% spent)
- **Red (danger)**: Over budget (≥ 100% spent)

**Applied Elements**:
1. Card border color
2. Percentage badge background
3. Progress bar fill color

### Progress Bar

**Implementation**:
```tsx
<div className="progress" style={{ height: '8px' }}>
  <div
    className={`progress-bar bg-${statusVariant}`}
    style={{ width: `${percentage}%` }}
    role="progressbar"
    aria-valuenow={percentage}
    aria-valuemin={0}
    aria-valuemax={100}
  />
</div>
```

**Accessibility**: ARIA attributes for screen readers

---

## Missing Features (Not Implemented)

### 1. Edit Budget
- No edit button on cards
- Can't modify existing budgets
- Would need edit modal

### 2. Delete Budget
- No delete button
- Can't remove budgets
- Would need confirmation dialog

### 3. API Integration
- No fetch on mount
- No create API call
- No update API call
- No delete API call

### 4. Spent Amount Tracking
- Spent amounts are static
- Should calculate from transactions
- Should update in real-time

### 5. Period Filtering
- No date range selection
- No "This Month" vs "Last Month"
- Period is just a label

### 6. Budget History
- No historical tracking
- Can't see past periods
- No comparison charts

### 7. Category Validation
- No check for duplicate categories
- No category existence validation
- Static category list

### 8. Notifications
- No alerts when approaching limit
- No over-budget warnings
- No email notifications

### 9. Bulk Operations
- No bulk create
- No bulk delete
- No import/export

### 10. Advanced Features
- No rollover budgets
- No savings goals
- No budget templates
- No sharing/collaboration

---

## Dependencies

### Libraries
- **react-bootstrap**: Container, Row, Col, Card, Button, Form, Modal

### Internal Dependencies
- None (no shared components, hooks, or services)

---

## Responsive Design

### Grid Breakpoints
- `xs={12}` - Mobile: 1 column (full width)
- `md={6}` - Tablet: 2 columns (half width)
- `lg={4}` - Desktop: 3 columns (one-third width)

### Card Sizing
- Auto-height based on content
- Consistent padding via Bootstrap
- Responsive text sizing

---

## Utility Functions

### createEmptyBudgetForm
```typescript
const createEmptyBudgetForm = (): CurrentBudgetForm => ({
  category: '',
  budgeted: '',
  period: 'Monthly'
});
```

**Purpose**: Factory function for initial form state

### calculatePercentage
```typescript
const calculatePercentage = (spent: number, budgeted: number): number => {
  if (budgeted === 0) return 0;
  return Math.min(100, (spent / budgeted) * 100);
};
```

**Purpose**: Safe percentage calculation with zero handling

### getStatusVariant
```typescript
const getStatusVariant = (percentage: number): 'success' | 'warning' | 'danger' => {
  if (percentage >= 100) return 'danger';
  if (percentage >= 80) return 'warning';
  return 'success';
};
```

**Purpose**: Determine color variant based on spending level

---

## Type Safety

### Budget Types
```typescript
interface Budget {
  id: number;
  category: string;
  budgeted: number;
  spent: number;
  period: 'Weekly' | 'Monthly' | 'Yearly';
}

interface CurrentBudgetForm {
  category: string;
  budgeted: string;  // String for form input
  period: 'Weekly' | 'Monthly' | 'Yearly';
}
```

### Type Guards
- Period: Union type `'Weekly' | 'Monthly' | 'Yearly'`
- Status: Union type `'success' | 'warning' | 'danger'`

### Constants
```typescript
const CATEGORIES: readonly string[] = [...] as const;
const PERIODS: readonly ('Weekly' | 'Monthly' | 'Yearly')[] = [...] as const;
```

**readonly**: Ensures immutability

---

## CSS Classes

**Bootstrap Classes**:
- `.budget-card` - Custom class for card styling
- `.border-{variant}` - Color-coded borders
- `.bg-{variant}` - Color-coded backgrounds
- `.progress` - Progress bar container
- `.progress-bar` - Progress bar fill

**Custom Classes** (expected but not in file):
- `.budget-amounts` - Spacing for amounts section

---

## Key Considerations for Rewrite

### 1. API Integration Required ⚠️

**Must Implement**:
```typescript
// Fetch budgets on mount
useEffect(() => {
  const fetchBudgets = async () => {
    const data = await budgetService.fetchBudgets();
    setBudgets(data);
  };
  fetchBudgets();
}, []);

// Create budget via API
const handleSaveBudget = async () => {
  const newBudget = await budgetService.createBudget({
    category_id: selectedCategory.id,
    amount: parseFloat(currentBudget.budgeted),
    period: currentBudget.period,
  });
  setBudgets([...budgets, newBudget]);
};
```

### 2. Dynamic Category Loading

**Current**: Static array
```typescript
const CATEGORIES = ['Food', 'Transport', ...];
```

**Should Be**: Fetched from API
```typescript
const [categories, setCategories] = useState<Category[]>([]);
useEffect(() => {
  categoryService.fetchCategories().then(setCategories);
}, []);
```

### 3. Spent Amount Calculation

**Current**: Static values
```typescript
spent: 250  // Hardcoded
```

**Should Be**: Calculated from transactions
```typescript
// API should aggregate transactions by category
GET /api/v1/budgets/status?start_date=...&end_date=...
```

### 4. Period-Based Filtering

**Must Add**:
- Date range based on period (weekly/monthly/yearly)
- Current period vs past periods
- Period selector component

### 5. Edit/Delete Functionality

**Add Handlers**:
```typescript
const handleEditBudget = (budget: Budget) => {
  setCurrentBudget({
    category: budget.category,
    budgeted: budget.budgeted.toString(),
    period: budget.period,
  });
  setEditingId(budget.id);
  setShowModal(true);
};

const handleDeleteBudget = async (id: number) => {
  if (confirm('Delete this budget?')) {
    await budgetService.deleteBudget(id);
    setBudgets(budgets.filter(b => b.id !== id));
  }
};
```

### 6. Validation Improvements

**Add**:
- Required field validation with error messages
- Duplicate category check
- Minimum amount validation
- Form validation library (e.g., React Hook Form + Zod)

### 7. Real-Time Updates

**Add Event Listeners**:
```typescript
useEffect(() => {
  // Listen for transaction events
  window.addEventListener('transaction-created', refreshBudgets);
  return () => window.removeEventListener('transaction-created', refreshBudgets);
}, []);
```

### 8. Empty State

**Add**:
```tsx
{budgets.length === 0 && (
  <div className="text-center py-5">
    <h3>No budgets yet</h3>
    <p>Create your first budget to start tracking spending</p>
    <Button onClick={handleAddBudget}>Create Budget</Button>
  </div>
)}
```

### 9. Loading State

**Add**:
```typescript
const [loading, setLoading] = useState(true);

{loading ? (
  <Spinner />
) : (
  <Row>{/* Budget cards */}</Row>
)}
```

### 10. Error Handling

**Add Try-Catch**:
```typescript
try {
  await budgetService.createBudget(data);
  showToast('Budget created', 'success');
} catch (error) {
  showToast('Failed to create budget', 'error');
}
```

### 11. Currency Formatting

**Current**: Hardcoded `$` symbol
```typescript
${budget.spent.toFixed(2)}
```

**Should Be**: Use app-wide currency formatter
```typescript
formatCurrency(budget.spent)  // Returns "IDR 250.00"
```

### 12. Budget Service

**Create**: `src/services/budgetService.ts`
```typescript
export const budgetService = {
  fetchBudgets: () => api.get<Budget[]>('/api/v1/budgets'),
  createBudget: (data) => api.post<Budget>('/api/v1/budgets', data),
  updateBudget: (id, data) => api.put<Budget>(`/api/v1/budgets/${id}`, data),
  deleteBudget: (id) => api.delete(`/api/v1/budgets/${id}`),
  fetchBudgetStatus: () => api.get<BudgetStatus[]>('/api/v1/budgets/status'),
};
```

### 13. Database Schema

**Add Budget Table**:
```sql
CREATE TABLE budgets (
  id VARCHAR(36) PRIMARY KEY,
  user_id VARCHAR(36) NOT NULL,
  category_id VARCHAR(36) NOT NULL,
  amount FLOAT NOT NULL,
  period VARCHAR(16) NOT NULL,  -- 'Weekly', 'Monthly', 'Yearly'
  start_date TIMESTAMP,
  end_date TIMESTAMP,
  created_at TIMESTAMP NOT NULL,
  FOREIGN KEY (user_id) REFERENCES users(id),
  FOREIGN KEY (category_id) REFERENCES categories(id)
);
```

### 14. API Endpoints

**Add Routes**:
- `GET /api/v1/budgets` - List budgets
- `POST /api/v1/budgets` - Create budget
- `PUT /api/v1/budgets/:id` - Update budget
- `DELETE /api/v1/budgets/:id` - Delete budget
- `GET /api/v1/budgets/status` - Get spending status (with aggregation)

### 15. Period Logic

**Implement Date Ranges**:
```typescript
const getPeriodDates = (period: 'Weekly' | 'Monthly' | 'Yearly') => {
  const now = new Date();
  switch (period) {
    case 'Weekly':
      return { start: startOfWeek(now), end: endOfWeek(now) };
    case 'Monthly':
      return { start: startOfMonth(now), end: endOfMonth(now) };
    case 'Yearly':
      return { start: startOfYear(now), end: endOfYear(now) };
  }
};
```

---

## Comparison with Other Pages

| Feature | Dashboard | Budgets |
|---------|-----------|---------|
| **API Integration** | ✅ Yes | ❌ No |
| **Data Persistence** | ✅ Yes | ❌ No |
| **CRUD Operations** | ✅ Full | ❌ Create only (local) |
| **Period Filtering** | ✅ Yes | ❌ No |
| **Loading States** | ✅ Yes | ❌ No |
| **Error Handling** | ✅ Yes | ❌ No |
| **Empty States** | ✅ Yes | ❌ No |
| **Edit/Delete** | ✅ Yes | ❌ No |

**Status**: Budgets page is a **prototype** compared to other pages

---

## Implementation Priority

**Phase 1: Basic Functionality**
1. Add budgetService with API calls
2. Fetch budgets on mount
3. Implement create API call
4. Add loading/error states
5. Add empty state

**Phase 2: CRUD Operations**
6. Implement edit functionality
7. Implement delete functionality
8. Add validation with error messages
9. Add confirmation dialogs

**Phase 3: Data Integration**
10. Fetch categories from API
11. Calculate spent amounts from transactions
12. Implement period-based date ranges
13. Add real-time updates (event listeners)

**Phase 4: Enhanced UX**
14. Add budget status notifications
15. Add budget history/trends
16. Add comparison charts
17. Add import/export
18. Add budget templates

---

## Testing Considerations

**Current State**: Not testable (no API, static data)

**After Implementation**:
1. Unit tests for utility functions
2. Integration tests for API calls
3. E2E tests for create/edit/delete flows
4. Visual regression tests for status colors
5. Accessibility tests for modal and cards

---

## Documentation Status

✅ Page structure documented  
✅ Current features analyzed  
✅ Missing features identified  
✅ Implementation roadmap provided  
⚠️ Needs API implementation  
⚠️ Needs full CRUD operations  
⚠️ Needs data integration  

**Next Steps**: Follow implementation priority to complete page
