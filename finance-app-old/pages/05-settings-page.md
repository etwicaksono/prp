# Settings Page

## Routes
- **URL**: `/settings?section={section}`
- **File**: `app/settings/page.tsx` → `src/views/settings/Settings.tsx`

## Purpose
Unified settings page with navigation sidebar for app configuration (currencies, categories, templates, labels, etc.).

## Page Structure

```typescript
app/settings/page.tsx (wrapper)
  └─ ProtectedShell
      └─ Suspense
          └─ LazySettings (src/views/settings/Settings.tsx)
```

## Key Features

### 1. URL-Based Navigation (with Query Params)

**URL Format**: `/settings?section={section}`

**Examples**:
- `/settings` → Defaults to "currencies"
- `/settings?section=categories` → Categories section
- `/settings?section=labels` → Labels section

**Navigation Type**: `SettingsSection` (union type)
```typescript
type SettingsSection =
  | 'currencies'
  | 'categories'
  | 'templates'
  | 'labels'
  | 'automatic-rules'
  | 'general'
  | 'billing'
  | 'privacy'
  | 'help';
```

**URL Integration**:
```typescript
// Read from URL
const sectionParam = searchParams.get('section');
const initialSection = isValidSection(sectionParam) ? sectionParam : 'currencies';

// Update URL
const handleSectionChange = (section: SettingsSection) => {
  setActiveSection(section);
  router.replace(`/settings?section=${section}`, { scroll: false });
};
```

**Browser Back/Forward Support**:
```typescript
useEffect(() => {
  const currentSection = searchParams.get('section');
  if (isValidSection(currentSection) && currentSection !== activeSection) {
    setActiveSection(currentSection);
  }
}, [searchParams, activeSection]);
```

**Benefits**:
- Shareable links to specific settings
- Browser history navigation works
- Bookmarkable sections

---

### 2. Two-Column Layout

**Left Sidebar (3/12 columns)**:
- Card with header: "Settings" title
- Grouped navigation sections
- Icon + label nav items
- Active state highlighting

**Right Content (9/12 columns)**:
- Card containing section content
- Rendered based on `activeSection` state

**Responsive**:
- Desktop (lg+): Two columns side-by-side
- Mobile: Sidebar shown (`.d-lg-block`, but no `.d-none` so shows on all sizes)

---

### 3. Navigation Structure (Grouped)

#### Navigation Groups

**WALLET Section**:
1. 💲 **Currencies** → `<Currencies />`
2. 📋 **Categories** → `<Categories />`
3. 📄 **Templates** → `<Templates />`
4. 🏷️ **Labels** → `<Labels />`
5. 🤖 **Automatic Rules** → Placeholder

**GENERAL Section**:
6. ⚙️ **General** → Placeholder
7. 💳 **Billing** → Placeholder
8. 🛡️ **Personal data & privacy** → Placeholder
9. ❓ **Help** → Placeholder

**Data Structure**:
```typescript
interface NavigationSection {
  title: string;           // 'WALLET' or 'GENERAL'
  items: NavigationItem[];
}

interface NavigationItem {
  id: SettingsSection;
  label: string;
  icon: IconComponent;
}

const navigationItems: NavigationSection[] = [
  {
    title: 'WALLET',
    items: [
      { id: 'currencies', label: 'Currencies', icon: FaDollarSign },
      // ...
    ]
  },
  // ...
];
```

---

### 4. Icon System

**Type Conversion**:
```typescript
type IconComponent = ComponentType<{ className?: string }>;

const toIconComponent = (icon: IconType): IconComponent =>
  icon as unknown as IconComponent;
```

**Why Needed?**
- React Icons exports `IconType`
- Need `ComponentType` for rendering
- Type cast ensures compatibility

**Usage**:
```typescript
{ id: 'currencies', label: 'Currencies', icon: toIconComponent(FaDollarSign) }
```

**Rendering**:
```tsx
const Icon = item.icon;
<Icon className="settings-nav-icon" />
```

---

### 5. Section Validation

**Function**: `isValidSection(value: string | null): value is SettingsSection`

**Purpose**: Type-safe validation of URL query param

**Logic**:
```typescript
const validSections: SettingsSection[] = [
  'currencies', 'categories', 'templates', 'labels',
  'automatic-rules', 'general', 'billing', 'privacy', 'help'
];
return validSections.includes(value as SettingsSection);
```

**Usage**:
- Validates URL parameter before setting state
- Returns false for invalid/missing sections
- Falls back to default ('currencies')

**Type Guard**: TypeScript knows `value` is `SettingsSection` if function returns true

---

### 6. Section Components (4 Implemented)

#### 1. Currencies (`./Currencies.tsx`)

**Purpose**: Manage available currencies

**Features**:
- Currency list display
- Add/edit/delete currencies
- Default currency selection
- Exchange rate management (if applicable)

**File**: `src/views/settings/Currencies.tsx`
**CSS**: `src/views/settings/Currencies.css`

---

#### 2. Categories (`./Categories.tsx`)

**Purpose**: Manage transaction categories

**Features**:
- Category tree view (parent/child)
- Add/edit/delete categories
- Icon and color selection
- Active/inactive toggle
- Drag-and-drop reordering

**File**: `src/views/settings/Categories.tsx`
**CSS**: `src/views/settings/Categories.css`

---

#### 3. Templates (`./Templates.tsx`)

**Purpose**: Manage quick transaction templates

**Features**:
- Template list
- Create/edit/delete templates
- Pre-filled transaction data
- Quick transaction presets

**File**: `src/views/settings/Templates.tsx`
**CSS**: `src/views/settings/Templates.css`

---

#### 4. Labels (`./Labels.tsx`)

**Purpose**: Manage transaction labels/tags

**Features**:
- Label list with colors
- Create/edit/delete labels
- Color picker for each label
- Used in transaction tagging

**File**: `src/views/settings/Labels.tsx`
**CSS**: No separate CSS file

---

### 7. Placeholder Sections (5 Not Implemented)

**Display**: Simple `<div>` with message
```tsx
{activeSection === 'automatic-rules' && (
  <div>Automatic Rules content coming soon</div>
)}
```

**Sections**:
1. **Automatic Rules**: Transaction automation rules
2. **General**: App-wide settings (theme, language, etc.)
3. **Billing**: Subscription and payment settings
4. **Privacy**: Data privacy and security settings
5. **Help**: Help documentation and support

**Future Implementation**: Replace `<div>` with actual components

---

### 8. Active State Management

**State**: `activeSection` (useState)

**Initial Value**: From URL or default
```typescript
const sectionParam = searchParams.get('section');
const initialSection = isValidSection(sectionParam) ? sectionParam : 'currencies';
const [activeSection, setActiveSection] = useState<SettingsSection>(initialSection);
```

**Update Flow**:
```
1. User clicks nav item
   ↓
2. handleSectionChange(section)
   ↓
3. setActiveSection(section)
   ↓
4. router.replace with new query param
   ↓
5. URL updates without page reload
   ↓
6. Section content re-renders
```

**Sync with URL**:
```typescript
useEffect(() => {
  // Listen for URL changes (back/forward buttons)
  const currentSection = searchParams.get('section');
  if (isValidSection(currentSection) && currentSection !== activeSection) {
    setActiveSection(currentSection);
  }
}, [searchParams, activeSection]);
```

---

## Component Hierarchy

```
<Settings>
  <Container>
    <Row>
      <Col lg={3}> {/* Sidebar */}
        <Card>
          <Card.Header>
            <h1>Settings</h1>
          </Card.Header>
          <Card.Body>
            <Nav> {/* Navigation */}
              {/* WALLET Section */}
              <div className="settings-nav-section">
                <div>WALLET</div>
                <Nav.Link> {/* Currencies */}
                  <Icon /> Currencies
                </Nav.Link>
                <Nav.Link> {/* Categories */}
                  <Icon /> Categories
                </Nav.Link>
                {/* ... */}
              </div>
              
              {/* GENERAL Section */}
              <div className="settings-nav-section">
                <div>GENERAL</div>
                <Nav.Link> {/* General */}
                  <Icon /> General
                </Nav.Link>
                {/* ... */}
              </div>
            </Nav>
          </Card.Body>
        </Card>
      </Col>
      
      <Col lg={9}> {/* Content */}
        <Card>
          <Card.Body>
            {activeSection === 'currencies' && <Currencies />}
            {activeSection === 'categories' && <Categories />}
            {activeSection === 'templates' && <Templates />}
            {activeSection === 'labels' && <Labels />}
            {/* Placeholders for others */}
          </Card.Body>
        </Card>
      </Col>
    </Row>
  </Container>
</Settings>
```

---

## State Management

### Local State (useState)

```typescript
const [activeSection, setActiveSection] = useState<SettingsSection>(initialSection);
```

### Router State (useSearchParams)

```typescript
const searchParams = useSearchParams();
const sectionParam = searchParams.get('section');
```

**No Global State**: All state is local to Settings page

---

## Routing & Navigation

### URL Pattern

**Base**: `/settings`

**With Section**: `/settings?section=categories`

**Query Parameter**: `section` (validated against `SettingsSection` type)

### Navigation Method

**Router Replace** (not push):
```typescript
router.replace(`/settings?section=${section}`, { scroll: false });
```

**Why `replace`?**
- Doesn't add new history entry for every nav click
- Cleaner browser history
- Less back-button presses needed

**Why `scroll: false`?**
- Prevents scroll to top on section change
- Better UX for sidebar navigation

### Browser Integration

**Back/Forward Buttons**: Fully supported via `useEffect` sync

**Bookmark/Share**: Each section has unique URL

**Direct Access**: Can navigate directly to `/settings?section=labels`

---

## CSS Structure

### Main Styles

**File**: `src/views/settings/Settings.css`

**Classes**:
- `.settings-title` - Page title in sidebar header
- `.settings-nav-section` - Navigation group wrapper
- `.settings-nav-title` - Section heading (WALLET, GENERAL)
- `.settings-nav-link` - Individual nav items
- `.settings-nav-link.active` - Active nav item
- `.settings-nav-icon` - Icon styling

### Section-Specific Styles

**Currencies**: `Currencies.css`
**Categories**: `Categories.css`
**Templates**: `Templates.css`
**Labels**: Inline styles or inherited

---

## Dependencies

### Libraries
- **react-bootstrap**: Container, Row, Col, Nav, Card
- **react-icons**: FaCog, FaCreditCard, FaTag, FaList, FaFileAlt, FaRobot, FaQuestionCircle, FaShieldAlt, FaDollarSign
- **next/navigation**: useRouter, useSearchParams

### Internal Components
- **Currencies**: Currency management
- **Categories**: Category management
- **Templates**: Template management
- **Labels**: Label management

### Types
- **SettingsSection**: Union type for valid sections
- **NavigationItem**: Nav item structure
- **NavigationSection**: Nav group structure
- **IconComponent**: Component type for icons

---

## User Flows

### Navigate to Settings Flow
```
1. User clicks "Settings" in app navigation
2. Navigates to /settings (no query param)
3. Defaults to /settings?section=currencies
4. Currencies component renders
```

### Change Section Flow
```
1. User clicks "Categories" nav item
2. handleSectionChange('categories')
3. State updates: activeSection = 'categories'
4. URL updates: /settings?section=categories
5. Categories component renders
6. Previous component (Currencies) unmounts
```

### Browser Back Flow
```
1. User on /settings?section=labels
2. User clicks browser back button
3. URL changes to /settings?section=categories
4. useEffect detects URL change
5. setActiveSection('categories')
6. Categories component renders
```

### Direct URL Access Flow
```
1. User navigates to /settings?section=templates
2. sectionParam = 'templates'
3. isValidSection('templates') → true
4. initialSection = 'templates'
5. Templates component renders on mount
```

### Invalid Section Flow
```
1. User navigates to /settings?section=invalid
2. sectionParam = 'invalid'
3. isValidSection('invalid') → false
4. initialSection = 'currencies' (fallback)
5. Currencies component renders
6. URL remains /settings?section=invalid (not corrected)
```

---

## Navigation Icons

| Section | Icon | Library |
|---------|------|---------|
| Currencies | 💲 FaDollarSign | react-icons/fa |
| Categories | 📋 FaList | react-icons/fa |
| Templates | 📄 FaFileAlt | react-icons/fa |
| Labels | 🏷️ FaTag | react-icons/fa |
| Automatic Rules | 🤖 FaRobot | react-icons/fa |
| General | ⚙️ FaCog | react-icons/fa |
| Billing | 💳 FaCreditCard | react-icons/fa |
| Privacy | 🛡️ FaShieldAlt | react-icons/fa |
| Help | ❓ FaQuestionCircle | react-icons/fa |

---

## Responsive Design

### Desktop (lg+)
```
┌──────────────────────────────────────┐
│ [Settings Sidebar] [Section Content] │
│ - Currencies       [Content Area]    │
│ - Categories                          │
│ - Templates                           │
│ - Labels                              │
│ ...                                   │
└──────────────────────────────────────┘
```

### Mobile/Tablet
```
┌──────────────────────────────────────┐
│ [Settings Sidebar]                   │
│ - Currencies                          │
│ - Categories                          │
│ ...                                   │
└──────────────────────────────────────┘
┌──────────────────────────────────────┐
│ [Section Content]                    │
│ (Below sidebar)                      │
└──────────────────────────────────────┘
```

**Note**: Sidebar doesn't hide on mobile (unlike other pages)
- Uses `.d-lg-block` (show on large+)
- No `.d-none` on mobile
- Stacks vertically on smaller screens

---

## Section Rendering Pattern

**Conditional Rendering** (not switch statement):
```typescript
{activeSection === 'currencies' && <Currencies />}
{activeSection === 'categories' && <Categories />}
{activeSection === 'templates' && <Templates />}
{activeSection === 'labels' && <Labels />}
{activeSection === 'automatic-rules' && <div>Coming soon</div>}
// ...
```

**Why This Pattern?**
- Simple and explicit
- Only active component is mounted
- Previous component fully unmounts
- No unnecessary renders

**Alternative Pattern** (not used):
```typescript
// Could use switch/case with renderContent()
```

---

## Type Safety

### SettingsSection Union Type
```typescript
type SettingsSection = 
  | 'currencies'
  | 'categories'
  | 'templates'
  | 'labels'
  | 'automatic-rules'
  | 'general'
  | 'billing'
  | 'privacy'
  | 'help';
```

**Benefits**:
- Type-safe section validation
- Autocomplete in IDE
- Compile-time checks
- Type guard with `isValidSection()`

### Icon Type Conversion
```typescript
type IconComponent = ComponentType<{ className?: string }>;
```

**Handles**: Converting `IconType` to `ComponentType`

---

## Performance Considerations

### Section Component Mounting
- Only active section component mounted
- Previous section unmounted on change
- No hidden components consuming memory

### URL Updates
- `router.replace` (not `push`)
- `scroll: false` prevents unnecessary scrolling
- No page reloads

### Navigation Rendering
- Static navigation items (no dynamic fetching)
- Icons pre-imported
- No re-renders on section change (only content area)

---

## Error Handling

### Invalid Section Handling
```typescript
const initialSection = isValidSection(sectionParam) ? sectionParam : 'currencies';
```

**Behavior**: Falls back to 'currencies' if invalid

**No Error UI**: Silent fallback (no error message to user)

### Missing Components
**Placeholder sections**: Show "Coming soon" message

**No Error Boundaries**: Assumes components exist or show placeholder

---

## Key Considerations for Rewrite

1. **URL-Based Navigation**: Must use query params, not route segments
2. **Section Validation**: Type-safe validation required
3. **Browser History**: `router.replace` with `scroll: false`
4. **URL Sync**: useEffect to listen for back/forward navigation
5. **Icon Type Conversion**: Type casting needed for React Icons
6. **Grouped Navigation**: Two sections (WALLET, GENERAL)
7. **Placeholder Sections**: 5 sections not implemented (show message)
8. **Component Unmounting**: Only active section rendered
9. **No Mobile Hiding**: Sidebar shows on all screen sizes
10. **CSS Files**: Separate CSS for some section components
11. **Default Section**: Falls back to 'currencies'
12. **Navigation Data**: Static array, not fetched
13. **Active State**: CSS class `.active` on nav link
14. **Card Layout**: Both sidebar and content wrapped in cards
15. **Title Placement**: "Settings" in sidebar header, not main content

---

## Section Component Files

**Location**: `src/views/settings/`

1. **Settings.tsx** - Main settings page
2. **Currencies.tsx** - Currency management
3. **Categories.tsx** - Category management
4. **Templates.tsx** - Template management
5. **Labels.tsx** - Label management
6. **Settings.css** - Main settings styles
7. **Currencies.css** - Currency-specific styles
8. **Categories.css** - Category-specific styles
9. **Templates.css** - Template-specific styles

---

## Future Enhancements (Not Implemented)

1. **Automatic Rules**: Transaction automation and triggers
2. **General Settings**: 
   - Theme selection (dark/light)
   - Language preferences
   - Date/number formatting
   - Notifications
3. **Billing**: 
   - Subscription management
   - Payment methods
   - Invoice history
4. **Privacy**: 
   - Data export
   - Account deletion
   - Privacy settings
   - Security options
5. **Help**: 
   - Documentation
   - FAQs
   - Contact support
   - Feature tutorials
6. **Search**: Search within settings
7. **Mobile Offcanvas**: Collapsible sidebar on mobile
8. **Keyboard Shortcuts**: Quick navigation between sections
9. **Unsaved Changes**: Warn before leaving section with changes
10. **Section Permissions**: Role-based access to sections

---

## Comparison with Other Pages

| Feature | Transactions | Analytics | Settings |
|---------|-------------|-----------|----------|
| **Navigation** | None | Tabs | Sidebar |
| **URL Pattern** | `/transactions` | `/analytics` | `/settings?section=*` |
| **Sidebar** | Filters | Filters | Navigation |
| **Layout** | Two-column | Two-column | Two-column |
| **Browser History** | Single page | Single page | Multi-section |
| **Mobile Sidebar** | Offcanvas | Hidden | Visible (stacked) |

---

## Navigation Pattern Comparison

| Page | Pattern | Implementation |
|------|---------|----------------|
| **Dashboard** | None | Single page |
| **Reports** | Tabs | Local state only |
| **Analytics** | Tabs | Local state only |
| **Settings** | Sidebar + URL | Query params + state |

**Settings is unique**: Only page with URL-based navigation for sub-sections

---

## Additional Notes

### Why Query Params (Not Route Segments)?

**Current**: `/settings?section=categories`
**Alternative**: `/settings/categories`

**Reasons for Query Params**:
1. Single page component (Settings.tsx)
2. Simpler routing configuration
3. Easy fallback handling
4. No nested route files needed

**Trade-off**: URL less semantic but implementation simpler

### Why Not useContext?

Settings state is local, no need for global context:
- State only used within Settings page
- No sharing with other pages
- URL serves as "global" state

### Why Card Wrapper?

Consistent with app design:
- Sidebar and content both in cards
- Visual separation
- Clean, organized appearance
