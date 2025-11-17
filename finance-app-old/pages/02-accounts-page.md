# Accounts Page

## Routes
- **URL**: `/accounts`
- **File**: `app/accounts/page.tsx` → `src/views/Accounts/Accounts.tsx`

## Purpose
Account management page with drag-and-drop reordering, filtering, and CRUD operations.

## Page Structure

```typescript
app/accounts/page.tsx (wrapper)
  └─ ProtectedShell
      └─ Suspense
          └─ LazyAccounts (src/views/Accounts/Accounts.tsx)
```

## Key Features

### 1. Two-Column Layout

**Left Sidebar (3/12 columns)**:
- "Accounts" heading with description
- "Add" button to create new account
- "Show Archived" toggle switch
- Summary card:
  - Total Balance (of visible accounts)
  - Active count
  - Archived count

**Right Content (9/12 columns)**:
- Drag-and-drop account list
- Active accounts section
- Archived accounts section (when toggled on)
- Empty state when no accounts

**Responsive Breakpoints**:
- `xl={3}/xl={9}` - Extra large screens
- `lg={4}/lg={8}` - Large screens
- Stacks vertically on smaller screens

---

### 2. Account Card Display

#### Card Components

**SortableAccountCard** (draggable, for active accounts):
```typescript
interface SortableAccountCardProps {
  account: Account;
  formatCurrency: (value: number) => string;
  onSelectAccount: (account: Account) => void;
  isArchived?: boolean;
}
```

**Card Layout**:
```
┌─────────────────────────────────────┐
│ [Icon] Account Name     IDR 50,000  │
│        Bank Account     [≡ Menu]    │
└─────────────────────────────────────┘
```

**Visual Elements**:
- Icon with circular background (lightened accent color)
- Account name (bold)
- Account type (muted, smaller)
- Balance (right-aligned, negative values highlighted)
- Drag handle (hamburger icon, active accounts only)

**Interactions**:
- Click card → Navigate to account detail page
- Click/drag hamburger icon → Reorder account
- Keyboard navigation (Enter/Space to select)

---

### 3. Drag-and-Drop Reordering

**Library**: @dnd-kit (DndContext, SortableContext, useSortable)

**Activation Constraint**: 8px distance (prevents accidental drags)

**Strategy**: `verticalListSortingStrategy` (vertical list)

**Sensors**:
- PointerSensor (mouse/touch)
- KeyboardSensor (accessibility)

#### Drag Flow

```
1. User grabs hamburger icon
   ↓
2. handleDragStart → setActiveId(account.id)
   ↓
3. DragOverlay shows draggable card
   ↓
4. User moves card
   ↓
5. handleDragEnd → Reorder in state
   ↓
6. Swap personal_id between dragged and target accounts
   ↓
7. Update order property (1, 2, 3...)
   ↓
8. Call API: accountService.swapAccountOrder()
   ↓
9. Optimistic UI update (doesn't wait for API)
```

#### Order Management

**Client-side State**:
```typescript
account.order = index + 1; // Display order (1, 2, 3...)
account.personal_id = number; // Database sequence ID
```

**API Call**:
```typescript
accountService.swapAccountOrder({
  order_map: [
    { id: 'uuid1', personal_id: 1 },
    { id: 'uuid2', personal_id: 2 },
    { id: 'uuid3', personal_id: 3 },
    // ... all accounts
  ]
});
```

**Personal ID Swap**:
- When accounts swap positions, their `personal_id` values swap
- Maintains database sequence integrity
- Server tracks max personal_ids via `GET /api/v1/personal-ids/max`
- Example: Account A (personal_id: 5) swaps with Account B (personal_id: 3)
  - After: Account A has personal_id: 3, Account B has personal_id: 5

**Optimistic Update**:
- UI updates immediately
- API call happens in background
- On error: Console log (no UI rollback)

**Archived Accounts**:
- Drag disabled (`useSortable({ disabled: isArchived })`)
- No hamburger icon shown
- Cannot be reordered

---

### 4. Account Filtering

**Toggle Switch**: "Show Archived"

**State**: `showArchived` (boolean)

**Filter Logic**:
```typescript
const filteredAccounts = useMemo(() => {
  const relevantAccounts = showArchived 
    ? accounts  // Show all
    : accounts.filter(a => !a.isArchived);  // Active only
  return [...relevantAccounts].sort((a, b) => a.order - b.order);
}, [accounts, showArchived]);
```

**Display Sections**:
- Active accounts (always shown first)
- Separator: "ARCHIVED" heading
- Archived accounts (only when `showArchived === true`)

**Summary Calculation**:
- Total Balance: Sum of visible accounts only
- Active Count: Non-archived accounts
- Archived Count: From `inactiveAccountCount` state

---

### 5. Data Loading & Transformation

#### Initial Data Fetch

```typescript
useEffect(() => {
  if (fetchedRef.current) return; // Prevent double-fetch
  fetchedRef.current = true;

  const load = async () => {
    const apiAccounts = await accountService.fetchAccounts();
    const sorted = sortApiAccounts(apiAccounts);  // Sort by position
    const inactiveCount = sorted.filter(a => !a.active).length;
    const mapped = sorted.map(mapApiAccountToView).filter(Boolean);
    
    setInactiveAccountCount(inactiveCount);
    setAccounts(mapped);
  };
  
  load();
}, []);
```

**Fetch Prevention**: `fetchedRef` prevents React StrictMode double-fetch

#### API Response Transformation

**Input**: `ApiAccountResponse[]` from API
```typescript
{
  id: string;
  user_id: string;
  personal_id: number;
  name: string;
  icon: string;
  active: boolean;
  usability: string;
  account_type: string;
  color: string;
  initial_amount?: number;
  group_id?: string;
  position?: unknown;
}
```

**Output**: `Account[]` for UI
```typescript
{
  id: string;
  personal_id: number;
  order: number;
  name: string;
  type: string;
  balance: number;
  icon: React.ComponentType;
  accentColor: string;
  backgroundColor: string;
  isActive: boolean;
  isArchived: boolean;
  usability: 'USABLE' | 'PROTECTED';
  iconKey: string;
}
```

**Transformation Function**: `mapApiAccountToView(apiAccount, index)`

**Key Transformations**:
1. **Icon Resolution**: String key → React component
   - `'FaBank'` → `FaBank` component from react-icons
   - Fallback: `FaWallet`

2. **Color Processing**:
   - `accentColor`: From API response
   - `backgroundColor`: Lightened version via `lightenColor()`

3. **Balance**: `initial_amount` → `balance`

4. **Status Flags**:
   - `isActive`: From `active` field
   - `isArchived`: Inverse of `isActive`

5. **Order**: Index-based (1, 2, 3...)

6. **Type Safety**: 
   - Default values for missing fields
   - Null filtering (invalid accounts removed)

#### Sorting Algorithm

**Function**: `sortApiAccounts(accounts)`

**Sort Criteria**:
1. Primary: `position` field (if present)
2. Secondary: `personal_id` (fallback)

**Result**: Stable sort order maintained across sessions

---

### 6. Add Account Flow

**Trigger**: Click "Add" button in sidebar

**Modal Component**: `AddAccountModal` (shared with Dashboard)

**Modal State**: `showAddModal` (boolean)

**Form Fields** (in AddAccountModal):
- Account name (required)
- Account type (e.g., "Bank Account", "Cash")
- Currency (default: IDR)
- Initial amount (optional)
- Icon (icon picker)
- Color (color picker)
- Usability (USABLE/PROTECTED)
- Active status (checkbox)

**Submit Flow**:
```
1. User fills form in AddAccountModal
   ↓
2. Modal validates fields
   ↓
3. Modal calls accountService.createAccount()
   ↓
4. Modal triggers onSubmit callback
   ↓
5. Accounts page calls refreshAccounts()
   ↓
6. Fetches updated account list from API
   ↓
7. Updates state with new accounts
   ↓
8. Modal closes
   ↓
9. New account appears in list
```

**Refresh Function**: `refreshAccounts()`
- Re-fetches all accounts from API
- Re-sorts and re-maps data
- Updates state
- Same logic as initial load

---

### 7. View Account Details

**Trigger**: Click anywhere on account card (except drag handle)

**Navigation**: `router.push(\`/accounts/${account.id}?from=accounts\`)`

**Query Parameter**: `?from=accounts`
- Used by Account Detail page for back navigation
- Returns user to Accounts page on "Back" click

**Keyboard Support**:
- Enter key: Navigate to detail
- Space key: Navigate to detail

---

### 8. Empty State

**Condition**: `filteredAccounts.length === 0`

**Display**:
- Folder icon (FaFolderOpen)
- Heading: "No accounts to show"
- Message: "Toggle archived accounts or add a new one to get started."

**Scenarios**:
- No accounts created yet
- All accounts archived + "Show Archived" is off

---

### 9. Currency Formatting

**Function**: `formatCurrency(value: number): string`

**Format**:
- Locale: `en-US` (uses periods for decimals, commas for thousands)
- Prefix: `IDR`
- Decimals: 2 places
- Negative sign: Before currency

**Examples**:
- `50000.00` → `"IDR 50,000.00"`
- `-25000.50` → `"-IDR 25,000.50"`

**Negative Styling**:
- CSS class: `accounts-list__balance--negative`
- Visual indicator for debt/overdraft

---

### 10. Type Safety & Utilities

#### Utility Functions

**getNumericValue(value, fallback = 0)**:
- Safely extracts number from unknown type
- Returns fallback if invalid
- Handles both number and string inputs

**getStringValue(value, fallback)**:
- Safely extracts non-empty string
- Returns fallback if empty or not string

**getBooleanValue(value, fallback)**:
- Type-safe boolean extraction
- Returns fallback if not boolean

**Purpose**: Defensive programming for API responses (handles missing/malformed data)

#### IconWrapper Component

```typescript
const IconWrapper: React.FC<{ icon: any; size?: number; className?: string }> 
  = ({ icon: Icon, size, className }) => <Icon size={size} className={className} />;
```

**Purpose**: 
- Handles dynamic icon components
- Bypasses TypeScript strict typing issues
- Consistent icon rendering

---

## State Management

### Local State (useState)

```typescript
const [showArchived, setShowArchived] = useState<boolean>(false);
const [accounts, setAccounts] = useState<Account[]>([]);
const [inactiveAccountCount, setInactiveAccountCount] = useState<number>(0);
const [activeId, setActiveId] = useState<string | null>(null);  // Current drag ID
const [showAddModal, setShowAddModal] = useState<boolean>(false);
```

### Computed State (useMemo)

```typescript
const filteredAccounts = useMemo(() => { ... }, [accounts, showArchived]);
const summary = useMemo(() => { ... }, [accounts, inactiveAccountCount, showArchived]);
```

**Benefits**:
- Prevents unnecessary recalculations
- Only recomputes when dependencies change

### Refs

```typescript
const fetchedRef = useRef<boolean>(false);
```

**Purpose**: Prevent double-fetch in React StrictMode

---

## Component Hierarchy

```
<Accounts>
  <Container>
    <Row>
      <Col> {/* Sidebar */}
        <Card>
          - Title & Description
          - Add Button
          - Show Archived Toggle
          - Summary Stats
        </Card>
      </Col>
      
      <Col> {/* Account List */}
        <DndContext>
          <SortableContext>
            {/* Active Accounts */}
            <SortableAccountCard /> (multiple)
            
            {/* Archived Section */}
            <div> "ARCHIVED" </div>
            <SortableAccountCard isArchived /> (multiple)
            
            {/* Empty State */}
            <Card> "No accounts" </Card>
          </SortableContext>
          
          <DragOverlay>
            <DraggableCard /> {/* Visual feedback during drag */}
          </DragOverlay>
        </DndContext>
      </Col>
    </Row>
    
    <AddAccountModal /> {/* Shared modal */}
  </Container>
</Accounts>
```

---

## Dependencies

### Libraries
- **react-bootstrap**: Container, Row, Col, Card, Button, Form
- **react-icons**: FaBars, FaPlus, FaWallet, FaFolderOpen
- **@dnd-kit**: DndContext, SortableContext, useSortable, DragOverlay
- **next/navigation**: useRouter

### Internal Dependencies
- **Components**: AddAccountModal
- **Services**: accountService
- **Utils**: resolveIconFromApiName, lightenColor, accountUtils
- **Types**: Account, ApiAccountResponse, OrderMapItem

---

## Performance Optimizations

1. **useMemo**: Computed values cached (filteredAccounts, summary)
2. **useCallback**: Event handlers memoized (drag handlers)
3. **Double-fetch Prevention**: useRef prevents duplicate API calls
4. **Optimistic Updates**: UI updates before API confirmation
5. **Drag Activation Constraint**: 8px prevents accidental drags
6. **Vertical Strategy**: Optimized for list sorting

---

## Accessibility

1. **Keyboard Navigation**:
   - Account cards: Enter/Space to select
   - Drag handle: Keyboard sensor for reordering

2. **ARIA Labels**:
   - Drag button: `aria-label="Reorder {account.name}"`
   - Cards: `role="button"` with `tabIndex={0}`

3. **Focus Management**:
   - Cards receive focus
   - Drag handles accessible via keyboard

---

## User Flows

### Create Account Flow
```
1. Click "Add" button
2. AddAccountModal opens
3. Fill form (name, type, amount, icon, color)
4. Click "Save"
5. Modal validates + calls API
6. Page refreshes account list
7. Modal closes
8. New account appears at bottom of list
```

### Reorder Accounts Flow
```
1. Hover over account card
2. Click and hold hamburger icon (≡)
3. Drag card up or down
4. Release at desired position
5. Accounts reorder in UI immediately
6. personal_id values swap between moved accounts (server manages max counts)
7. API call updates all account positions
8. Order persists across page reloads
```

### View Account Details Flow
```
1. Click on account card (not drag handle)
2. Navigate to /accounts/{id}?from=accounts
3. Account detail page loads
4. User views transactions, balance, details
5. Click "Back" button
6. Returns to /accounts page
```

### Toggle Archived Flow
```
1. Toggle "Show Archived" switch
2. showArchived state updates
3. filteredAccounts recomputes
4. Archived section appears/disappears
5. Summary stats update (total balance changes)
```

---

## Error Handling

### API Errors

**Load Accounts**:
- Catch error
- Log to console
- No user-facing error (graceful degradation)
- Empty state shows if no accounts loaded

**Reorder Accounts**:
- Catch error
- Log to console
- UI already updated (optimistic)
- No rollback on error

**Create Account** (in AddAccountModal):
- Error handling in modal component
- Toast notification on failure

### Data Validation

**mapApiAccountToView**:
- Returns `null` for invalid accounts (missing ID)
- Filtered out: `.filter((account): account is Account => account !== null)`

**Utility Functions**:
- Always return fallback values
- Never throw errors
- Safe for malformed API responses

---

## CSS Classes

**Page Container**: `.accounts-page`

**Sidebar**:
- `.accounts-sidebar`
- `.accounts-sidebar__title`
- `.accounts-sidebar__caption`
- `.accounts-sidebar__add-btn`
- `.accounts-sidebar__switch`
- `.accounts-sidebar__form-switch`
- `.accounts-sidebar__summary`
- `.accounts-sidebar__summary-item`
- `.accounts-sidebar__summary-grid`

**Account List**:
- `.accounts-list`
- `.accounts-list__item`
- `.accounts-list__body`
- `.accounts-list__icon`
- `.accounts-list__details`
- `.accounts-list__name`
- `.accounts-list__type`
- `.accounts-list__balance`
- `.accounts-list__balance--negative`
- `.accounts-list__menu-btn`
- `.accounts-list__empty`
- `.accounts-list__empty-icon`

---

## Key Considerations for Rewrite

1. **Drag-and-Drop**: Requires @dnd-kit library with specific setup
2. **Personal ID Swapping**: Critical for database integrity during reorder
3. **Order Map API**: Send entire list of {id, personal_id} pairs
4. **Optimistic Updates**: UI updates before API confirmation
5. **Fetch Prevention**: useRef to avoid double-fetch in StrictMode
6. **Icon Resolution**: String keys must resolve to React components
7. **Color Utilities**: lightenColor() creates background from accent
8. **Null Filtering**: Invalid accounts filtered out after mapping
9. **Archive Handling**: Archived accounts non-draggable
10. **Responsive Layout**: Sidebar stacks on mobile
11. **Query Params**: ?from=accounts for back navigation
12. **Summary Calculation**: Based on visible accounts only
13. **Error Handling**: Silent failures, console logs only
14. **Type Safety**: Utility functions handle malformed API data
15. **Accessibility**: Keyboard navigation and ARIA labels required
