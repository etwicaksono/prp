# Personal Finance Management App - Product Requirements Document

## Table of Contents
1. [Project Overview](#project-overview)
2. [Design System](#design-system)
3. [Component Specifications](#component-specifications)
4. [Functional Requirements](#functional-requirements)
5. [Non-Functional Requirements](#non-functional-requirements)
6. [Database Design](#database-design)
7. [API Design](#api-design)
8. [Database Migration Strategy](#database-migration-strategy)
9. [Database Seeder](#database-seeder)
10. [Testing Strategy](#testing-strategy)
11. [Technology Stack & Libraries](#technology-stack--libraries)
12. [Development Phases](#development-phases)

---

## 1. Project Overview

### 1.1 Application Name
**WalletFlow** - Personal Finance Management System

### 1.2 Purpose
A comprehensive personal finance management application that helps users track expenses, manage budgets, monitor accounts, and achieve financial goals.

### 1.3 Architecture
- **Application**: Next.js 15+ Full-Stack (App Router) with TypeScript
  - Frontend: React components with Server Components
  - Backend: Next.js API Routes (App Router)
  - Database Access: Direct from API routes using Prisma ORM
- **Database**: PostgreSQL 15+
- **Deployment**: Single deployment (Vercel, Railway, or Docker container)

### 1.4 Architecture Benefits
- **Single Codebase**: Frontend and backend in one repository
- **Shared TypeScript Types**: Type safety across entire stack
- **Simplified Deployment**: One deployment, one domain
- **Server Components**: Better performance with React Server Components
- **Easy Local Development**: Single `npm run dev` command
- **Built-in API Routes**: No need for separate backend server
- **Vercel Optimization**: Can deploy to Vercel with zero configuration

### 1.4 Core Features
- Multi-account management
- Transaction tracking (income/expenses)
- Budget planning and monitoring
- Category-based expense tracking
- Financial reports and analytics
- Recurring transactions/planned payments
- Financial goals tracking
- Multi-currency support
- Account sharing (family/friends)
- CSV import/export
- Real-time dashboard

---

## 2. Design System

### 2.1 Color Palette

#### 2.1.1 Primary Colors
```css
/* Brand Colors */
--color-primary-50: #EEF2FF;   /* Lightest - backgrounds */
--color-primary-100: #E0E7FF;  /* Light backgrounds */
--color-primary-200: #C7D2FE;  /* Subtle borders */
--color-primary-300: #A5B4FC;  /* Muted elements */
--color-primary-400: #818CF8;  /* Secondary elements */
--color-primary-500: #6366F1;  /* Primary brand color */
--color-primary-600: #4F46E5;  /* Primary hover */
--color-primary-700: #4338CA;  /* Primary active */
--color-primary-800: #3730A3;  /* Dark primary */
--color-primary-900: #312E81;  /* Darkest primary */

/* Usage */
Primary Buttons: primary-600
Primary Hover: primary-700
Primary Active: primary-800
Primary Light Backgrounds: primary-50
Links: primary-600
```

#### 2.1.2 Semantic Colors

```css
/* Success - Income, Positive Actions */
--color-success-50: #F0FDF4;
--color-success-100: #DCFCE7;
--color-success-200: #BBF7D0;
--color-success-300: #86EFAC;
--color-success-400: #4ADE80;
--color-success-500: #22C55E;   /* Main success */
--color-success-600: #16A34A;   /* Success hover */
--color-success-700: #15803D;   /* Success active */
--color-success-800: #166534;
--color-success-900: #14532D;

/* Warning - Budget Alerts, Caution */
--color-warning-50: #FFFBEB;
--color-warning-100: #FEF3C7;
--color-warning-200: #FDE68A;
--color-warning-300: #FCD34D;
--color-warning-400: #FBBF24;
--color-warning-500: #F59E0B;   /* Main warning */
--color-warning-600: #D97706;   /* Warning hover */
--color-warning-700: #B45309;   /* Warning active */
--color-warning-800: #92400E;
--color-warning-900: #78350F;

/* Error - Expenses, Errors, Destructive Actions */
--color-error-50: #FEF2F2;
--color-error-100: #FEE2E2;
--color-error-200: #FECACA;
--color-error-300: #FCA5A5;
--color-error-400: #F87171;
--color-error-500: #EF4444;     /* Main error */
--color-error-600: #DC2626;     /* Error hover */
--color-error-700: #B91C1C;     /* Error active */
--color-error-800: #991B1B;
--color-error-900: #7F1D1D;

/* Info - Information, Tips */
--color-info-50: #EFF6FF;
--color-info-100: #DBEAFE;
--color-info-200: #BFDBFE;
--color-info-300: #93C5FD;
--color-info-400: #60A5FA;
--color-info-500: #3B82F6;      /* Main info */
--color-info-600: #2563EB;      /* Info hover */
--color-info-700: #1D4ED8;      /* Info active */
--color-info-800: #1E40AF;
--color-info-900: #1E3A8A;
```

#### 2.1.3 Neutral Colors

```css
/* Grayscale - UI Elements */
--color-gray-50: #F9FAFB;       /* Lightest background */
--color-gray-100: #F3F4F6;      /* Light background */
--color-gray-200: #E5E7EB;      /* Borders */
--color-gray-300: #D1D5DB;      /* Disabled borders */
--color-gray-400: #9CA3AF;      /* Placeholder text */
--color-gray-500: #6B7280;      /* Secondary text */
--color-gray-600: #4B5563;      /* Body text */
--color-gray-700: #374151;      /* Headings */
--color-gray-800: #1F2937;      /* Dark headings */
--color-gray-900: #111827;      /* Darkest text */

/* True Blacks & Whites */
--color-white: #FFFFFF;
--color-black: #000000;
```

#### 2.1.4 Dark Mode Colors

```css
/* Dark Mode Palette */
--dark-bg-primary: #0F172A;     /* Main background */
--dark-bg-secondary: #1E293B;   /* Card background */
--dark-bg-tertiary: #334155;    /* Elevated elements */
--dark-border: #475569;         /* Borders */
--dark-text-primary: #F1F5F9;   /* Main text */
--dark-text-secondary: #CBD5E1; /* Secondary text */
--dark-text-muted: #94A3B8;     /* Muted text */
```

#### 2.1.5 Category Colors

```css
/* Pre-defined Category Colors */
--category-food: #EF4444;       /* Red */
--category-transport: #F59E0B;  /* Orange */
--category-shopping: #EC4899;   /* Pink */
--category-entertainment: #8B5CF6; /* Purple */
--category-bills: #3B82F6;      /* Blue */
--category-healthcare: #10B981; /* Green */
--category-education: #6366F1;  /* Indigo */
--category-travel: #06B6D4;     /* Cyan */
--category-personal: #F97316;   /* Deep Orange */
--category-housing: #0EA5E9;    /* Sky */
--category-income: #22C55E;     /* Green */
--category-investment: #8B5CF6; /* Purple */
--category-other: #6B7280;      /* Gray */
```

#### 2.1.6 Account Type Colors

```css
/* Account Type Colors */
--account-checking: #4F46E5;    /* Indigo */
--account-savings: #10B981;     /* Green */
--account-credit: #EF4444;      /* Red */
--account-cash: #F59E0B;        /* Orange */
--account-loan: #DC2626;        /* Dark Red */
--account-investment: #8B5CF6;  /* Purple */
--account-crypto: #F97316;      /* Orange */
--account-other: #6B7280;       /* Gray */
```

### 2.2 Typography

#### 2.2.1 Font Families

```css
/* Primary Font (Sans-serif) */
--font-primary: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;

/* Monospace (Numbers, Currency) */
--font-mono: 'JetBrains Mono', 'Fira Code', 'Courier New', monospace;

/* Font Weights */
--font-weight-light: 300;
--font-weight-normal: 400;
--font-weight-medium: 500;
--font-weight-semibold: 600;
--font-weight-bold: 700;
--font-weight-extrabold: 800;
```

#### 2.2.2 Font Scales

```css
/* Desktop Font Sizes */
--text-xs: 0.75rem;      /* 12px - Small labels */
--text-sm: 0.875rem;     /* 14px - Body small */
--text-base: 1rem;       /* 16px - Body */
--text-lg: 1.125rem;     /* 18px - Large body */
--text-xl: 1.25rem;      /* 20px - Subheadings */
--text-2xl: 1.5rem;      /* 24px - H3 */
--text-3xl: 1.875rem;    /* 30px - H2 */
--text-4xl: 2.25rem;     /* 36px - H1 */
--text-5xl: 3rem;        /* 48px - Display */

/* Line Heights */
--leading-tight: 1.25;
--leading-normal: 1.5;
--leading-relaxed: 1.75;
```

#### 2.2.3 Typography Usage

```
Display Numbers (Balance, Amounts):
  Font: font-mono
  Weight: font-semibold (600)
  Size: text-3xl to text-5xl
  Color: gray-900 (light) / dark-text-primary (dark)

Headings (H1):
  Font: font-primary
  Weight: font-bold (700)
  Size: text-4xl (mobile: text-3xl)
  Line Height: leading-tight
  Color: gray-900 (light) / dark-text-primary (dark)

Headings (H2):
  Font: font-primary
  Weight: font-semibold (600)
  Size: text-3xl (mobile: text-2xl)
  Color: gray-800 (light) / dark-text-primary (dark)

Headings (H3):
  Font: font-primary
  Weight: font-semibold (600)
  Size: text-2xl (mobile: text-xl)
  Color: gray-800 (light) / dark-text-primary (dark)

Body Text:
  Font: font-primary
  Weight: font-normal (400)
  Size: text-base
  Line Height: leading-normal
  Color: gray-600 (light) / dark-text-secondary (dark)

Small Text:
  Font: font-primary
  Weight: font-normal (400)
  Size: text-sm
  Color: gray-500 (light) / dark-text-muted (dark)

Labels:
  Font: font-primary
  Weight: font-medium (500)
  Size: text-sm
  Color: gray-700 (light) / dark-text-secondary (dark)
```

### 2.3 Spacing System

```css
/* Spacing Scale (based on 4px) */
--space-0: 0;
--space-1: 0.25rem;   /* 4px */
--space-2: 0.5rem;    /* 8px */
--space-3: 0.75rem;   /* 12px */
--space-4: 1rem;      /* 16px */
--space-5: 1.25rem;   /* 20px */
--space-6: 1.5rem;    /* 24px */
--space-8: 2rem;      /* 32px */
--space-10: 2.5rem;   /* 40px */
--space-12: 3rem;     /* 48px */
--space-16: 4rem;     /* 64px */
--space-20: 5rem;     /* 80px */
--space-24: 6rem;     /* 96px */

/* Component Spacing */
Padding Small: space-2 (8px)
Padding Medium: space-4 (16px)
Padding Large: space-6 (24px)

Margin Small: space-2 (8px)
Margin Medium: space-4 (16px)
Margin Large: space-8 (32px)

Gap Small: space-2 (8px)
Gap Medium: space-4 (16px)
Gap Large: space-6 (24px)
```

### 2.4 Border Radius

```css
/* Border Radius Scale */
--radius-none: 0;
--radius-sm: 0.125rem;    /* 2px */
--radius-base: 0.25rem;   /* 4px */
--radius-md: 0.375rem;    /* 6px */
--radius-lg: 0.5rem;      /* 8px */
--radius-xl: 0.75rem;     /* 12px */
--radius-2xl: 1rem;       /* 16px */
--radius-3xl: 1.5rem;     /* 24px */
--radius-full: 9999px;    /* Circle */

/* Component Usage */
Buttons: radius-lg (8px)
Cards: radius-xl (12px)
Inputs: radius-md (6px)
Badges: radius-full (circle)
Modals: radius-2xl (16px)
Avatars: radius-full (circle)
```

### 2.5 Shadows

```css
/* Shadow Levels */
--shadow-xs: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
--shadow-sm: 0 1px 3px 0 rgba(0, 0, 0, 0.1), 0 1px 2px -1px rgba(0, 0, 0, 0.1);
--shadow-md: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -2px rgba(0, 0, 0, 0.1);
--shadow-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -4px rgba(0, 0, 0, 0.1);
--shadow-xl: 0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 8px 10px -6px rgba(0, 0, 0, 0.1);
--shadow-2xl: 0 25px 50px -12px rgba(0, 0, 0, 0.25);
--shadow-inner: inset 0 2px 4px 0 rgba(0, 0, 0, 0.05);

/* Dark Mode Shadows */
--shadow-dark-sm: 0 1px 3px 0 rgba(0, 0, 0, 0.3), 0 1px 2px -1px rgba(0, 0, 0, 0.3);
--shadow-dark-md: 0 4px 6px -1px rgba(0, 0, 0, 0.4), 0 2px 4px -2px rgba(0, 0, 0, 0.4);
--shadow-dark-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.5), 0 4px 6px -4px rgba(0, 0, 0, 0.5);

/* Component Usage */
Cards: shadow-sm
Dropdown Menus: shadow-lg
Modals: shadow-2xl
Buttons Hover: shadow-md
Floating Action Buttons: shadow-xl
```

### 2.6 Transitions & Animations

```css
/* Timing Functions */
--ease-in: cubic-bezier(0.4, 0, 1, 1);
--ease-out: cubic-bezier(0, 0, 0.2, 1);
--ease-in-out: cubic-bezier(0.4, 0, 0.2, 1);
--ease-bounce: cubic-bezier(0.68, -0.55, 0.265, 1.55);

/* Duration */
--duration-75: 75ms;
--duration-100: 100ms;
--duration-150: 150ms;
--duration-200: 200ms;
--duration-300: 300ms;
--duration-500: 500ms;
--duration-700: 700ms;

/* Standard Transitions */
--transition-fast: all 150ms ease-in-out;
--transition-base: all 200ms ease-in-out;
--transition-slow: all 300ms ease-in-out;

/* Component Animations */
Button Hover: transition-fast (150ms)
Dropdown Open: transition-base (200ms)
Modal Enter/Exit: transition-slow (300ms)
Page Transitions: transition-slow (300ms)
Skeleton Loading: 1.5s infinite linear
```

#### 2.6.1 Keyframe Animations

```css
/* Fade In */
@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

/* Slide In From Right */
@keyframes slideInRight {
  from {
    transform: translateX(100%);
    opacity: 0;
  }
  to {
    transform: translateX(0);
    opacity: 1;
  }
}

/* Slide In From Bottom */
@keyframes slideInBottom {
  from {
    transform: translateY(20px);
    opacity: 0;
  }
  to {
    transform: translateY(0);
    opacity: 1;
  }
}

/* Scale In */
@keyframes scaleIn {
  from {
    transform: scale(0.95);
    opacity: 0;
  }
  to {
    transform: scale(1);
    opacity: 1;
  }
}

/* Pulse (Budget Alert) */
@keyframes pulse {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.5; }
}

/* Shimmer (Loading Skeleton) */
@keyframes shimmer {
  0% { background-position: -1000px 0; }
  100% { background-position: 1000px 0; }
}

/* Bounce (Notification Badge) */
@keyframes bounce {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-10px); }
}
```

### 2.7 Breakpoints

```css
/* Responsive Breakpoints */
--breakpoint-xs: 0px;       /* Mobile small */
--breakpoint-sm: 640px;     /* Mobile */
--breakpoint-md: 768px;     /* Tablet */
--breakpoint-lg: 1024px;    /* Desktop */
--breakpoint-xl: 1280px;    /* Large desktop */
--breakpoint-2xl: 1536px;   /* Extra large desktop */

/* Media Queries */
@media (min-width: 640px)  { /* sm */ }
@media (min-width: 768px)  { /* md */ }
@media (min-width: 1024px) { /* lg */ }
@media (min-width: 1280px) { /* xl */ }
@media (min-width: 1536px) { /* 2xl */ }
```

### 2.8 Z-Index Layers

```css
/* Z-Index Scale */
--z-base: 0;
--z-dropdown: 1000;
--z-sticky: 1020;
--z-fixed: 1030;
--z-modal-backdrop: 1040;
--z-modal: 1050;
--z-popover: 1060;
--z-tooltip: 1070;
--z-toast: 1080;

/* Component Usage */
Page Content: z-base (0)
Dropdown Menus: z-dropdown (1000)
Sticky Headers: z-sticky (1020)
Floating Action Buttons: z-fixed (1030)
Modal Backdrops: z-modal-backdrop (1040)
Modals: z-modal (1050)
Popovers: z-popover (1060)
Tooltips: z-tooltip (1070)
Toast Notifications: z-toast (1080)
```

---

## 3. Component Specifications

### 3.1 Button Component

#### 3.1.1 Button Variants

**Primary Button**
```
Visual Properties:
  Background: primary-600
  Text: white
  Border: none
  Padding: 10px 20px (space-2.5 space-5)
  Border Radius: radius-lg (8px)
  Font Weight: font-medium (500)
  Font Size: text-sm (14px)
  Shadow: shadow-sm

States:
  Hover:
    Background: primary-700
    Shadow: shadow-md
    Transform: translateY(-1px)
    Transition: transition-fast (150ms)
  
  Active:
    Background: primary-800
    Shadow: shadow-xs
    Transform: translateY(0)
  
  Disabled:
    Background: gray-300
    Text: gray-500
    Cursor: not-allowed
    Opacity: 0.6
  
  Loading:
    Background: primary-600
    Cursor: wait
    Display spinner icon
    Text: Loading...

  Focus:
    Outline: 2px solid primary-400
    Outline Offset: 2px
```

**Secondary Button**
```
Visual Properties:
  Background: white
  Text: gray-700
  Border: 1px solid gray-300
  Padding: 10px 20px
  Border Radius: radius-lg
  Font Weight: font-medium
  Font Size: text-sm
  Shadow: shadow-sm

States:
  Hover:
    Background: gray-50
    Border: 1px solid gray-400
    Shadow: shadow-md
  
  Active:
    Background: gray-100
    Border: 1px solid gray-500
  
  Disabled:
    Background: gray-100
    Text: gray-400
    Border: 1px solid gray-200
    Cursor: not-allowed
```

**Destructive Button**
```
Visual Properties:
  Background: error-600
  Text: white
  Border: none
  Padding: 10px 20px
  Border Radius: radius-lg

States:
  Hover:
    Background: error-700
    Shadow: shadow-md
  
  Active:
    Background: error-800
```

**Ghost Button**
```
Visual Properties:
  Background: transparent
  Text: gray-700
  Border: none
  Padding: 10px 20px

States:
  Hover:
    Background: gray-100
  
  Active:
    Background: gray-200
```

**Icon Button**
```
Visual Properties:
  Background: transparent
  Padding: 8px (square)
  Border Radius: radius-md
  Size: 40px x 40px

States:
  Hover:
    Background: gray-100
  
  Active:
    Background: gray-200
```

#### 3.1.2 Button Sizes

```
Small:
  Padding: 6px 12px
  Font Size: text-xs (12px)
  Height: 32px
  Icon Size: 16px

Medium (Default):
  Padding: 10px 20px
  Font Size: text-sm (14px)
  Height: 40px
  Icon Size: 20px

Large:
  Padding: 12px 24px
  Font Size: text-base (16px)
  Height: 48px
  Icon Size: 24px
```

#### 3.1.3 Button Behavior

```
Click Interaction:
  1. User hovers: Apply hover state (150ms transition)
  2. User clicks: Apply active state
  3. Execute onClick handler
  4. If async operation:
     - Show loading state
     - Disable button
     - Show spinner
     - Replace text with "Loading..."
  5. On completion: Return to default state

Keyboard Navigation:
  - Tab: Focus button (show focus ring)
  - Enter/Space: Trigger click
  - Focus visible: Show outline

Accessibility:
  - aria-label for icon-only buttons
  - aria-disabled for disabled state
  - aria-busy for loading state
  - Minimum touch target: 44x44px
```

### 3.2 Input Component

#### 3.2.1 Text Input

```
Visual Properties:
  Background: white
  Border: 1px solid gray-300
  Padding: 10px 12px
  Border Radius: radius-md (6px)
  Font Size: text-base (16px)
  Font Weight: font-normal (400)
  Height: 44px
  Placeholder Color: gray-400

States:
  Default:
    Border: 1px solid gray-300
    Background: white
  
  Hover:
    Border: 1px solid gray-400
    Transition: transition-fast
  
  Focus:
    Border: 2px solid primary-500
    Outline: 2px solid primary-200 (offset 2px)
    Box Shadow: 0 0 0 3px rgba(99, 102, 241, 0.1)
  
  Disabled:
    Background: gray-100
    Border: 1px solid gray-200
    Text: gray-500
    Cursor: not-allowed
  
  Error:
    Border: 2px solid error-500
    Focus Outline: 2px solid error-200
  
  Success:
    Border: 2px solid success-500
    Focus Outline: 2px solid success-200

Dark Mode:
  Background: dark-bg-secondary
  Border: 1px solid dark-border
  Text: dark-text-primary
  Placeholder: dark-text-muted
```

#### 3.2.2 Input with Icon

```
Visual Properties:
  Same as text input
  Icon Position: Left or Right
  Icon Size: 20px
  Icon Color: gray-400
  Icon Padding: 12px

Layout:
  Left Icon:
    Icon: 12px from left
    Text: Starts after icon + 12px padding
  
  Right Icon:
    Icon: 12px from right
    Text: Ends before icon + 12px padding

States:
  Focus:
    Icon Color: primary-500
```

#### 3.2.3 Input Behavior

```
Focus Behavior:
  1. User clicks: Apply focus state
  2. Show cursor at click position
  3. Select all on Cmd/Ctrl + A
  4. Clear on Escape (if clearable)

Validation:
  On Blur:
    - Run validation
    - Show error if invalid
    - Display error message below input
  
  On Type (debounced 300ms):
    - Clear existing error
    - Run async validation (if applicable)
  
  On Submit:
    - Run all validations
    - Prevent submission if invalid
    - Focus first invalid field

Error Display:
  Position: Below input, 4px gap
  Color: error-600
  Font Size: text-sm (14px)
  Icon: Error icon (16px) before text
  Animation: slideInBottom (200ms)
```

#### 3.2.4 Number Input (Currency)

```
Visual Properties:
  Same as text input
  Font Family: font-mono
  Text Align: Right
  Prefix: Currency symbol (e.g., $)
  Decimal Places: 2

Behavior:
  On Focus:
    - Select all text
    - Show numeric keyboard on mobile
  
  On Input:
    - Allow only numbers and decimal point
    - Format as currency on blur
    - Maximum 2 decimal places
  
  Display Format:
    Empty: Show placeholder
    Typing: Show raw number
    Blurred: Show formatted currency ($1,234.56)
```

### 3.3 Select/Dropdown Component

#### 3.3.1 Select Field

```
Visual Properties:
  Background: white
  Border: 1px solid gray-300
  Padding: 10px 12px
  Border Radius: radius-md
  Height: 44px
  Arrow Icon: 20px chevron-down (gray-400)
  Arrow Position: Right side, 12px padding

States:
  Hover:
    Border: 1px solid gray-400
    Cursor: pointer
  
  Focus/Open:
    Border: 2px solid primary-500
    Arrow Icon: Rotate 180deg (200ms)
  
  Disabled:
    Background: gray-100
    Border: 1px solid gray-200
    Cursor: not-allowed
    Opacity: 0.6

Dropdown Menu:
  Background: white
  Border: 1px solid gray-200
  Border Radius: radius-lg
  Shadow: shadow-lg
  Max Height: 300px
  Overflow: auto
  Position: Absolute, below input
  Z-Index: z-dropdown (1000)
  Animation: slideInBottom (200ms)
  
  Dark Mode:
    Background: dark-bg-tertiary
    Border: 1px solid dark-border
```

#### 3.3.2 Dropdown Menu Item

```
Visual Properties:
  Padding: 10px 12px
  Font Size: text-base
  Color: gray-700
  Cursor: pointer
  
States:
  Hover:
    Background: primary-50
    Color: primary-700
    Transition: transition-fast
  
  Selected:
    Background: primary-100
    Color: primary-700
    Font Weight: font-medium
    Check Icon: 16px (right side)
  
  Disabled:
    Color: gray-400
    Cursor: not-allowed
    Hover: No effect

Group Header:
  Padding: 8px 12px
  Font Size: text-xs
  Font Weight: font-semibold
  Color: gray-500
  Text Transform: uppercase
  Letter Spacing: 0.05em
```

#### 3.3.3 Select Behavior

```
Opening:
  1. User clicks: Open dropdown
  2. Play slideInBottom animation (200ms)
  3. Rotate arrow icon 180deg
  4. Focus first item or selected item
  5. Add keyboard listener

Selection:
  1. User clicks option: Select item
  2. Close dropdown (fadeOut 150ms)
  3. Update display value
  4. Trigger onChange callback
  5. Remove focus

Keyboard Navigation:
  Arrow Down: Focus next item
  Arrow Up: Focus previous item
  Enter: Select focused item
  Escape: Close dropdown
  Home: Focus first item
  End: Focus last item
  Type: Jump to item starting with letter

Accessibility:
  - role="combobox"
  - aria-expanded="true/false"
  - aria-activedescendant on focused item
  - aria-label for screenreaders
```

### 3.4 Card Component

```
Visual Properties:
  Background: white
  Border: 1px solid gray-200
  Border Radius: radius-xl (12px)
  Padding: 24px (space-6)
  Shadow: shadow-sm
  
States:
  Default:
    Shadow: shadow-sm
    Transform: scale(1)
  
  Hover (if interactive):
    Shadow: shadow-md
    Transform: translateY(-2px)
    Border: 1px solid gray-300
    Transition: transition-base (200ms)
  
  Active (if clickable):
    Shadow: shadow-sm
    Transform: translateY(0)
  
  Selected:
    Border: 2px solid primary-500
    Background: primary-50

Dark Mode:
  Background: dark-bg-secondary
  Border: 1px solid dark-border
  Shadow: shadow-dark-sm

Variants:
  Elevated:
    Border: none
    Shadow: shadow-md
  
  Outlined:
    Border: 2px solid gray-200
    Shadow: none
  
  Ghost:
    Border: none
    Shadow: none
    Background: transparent
```

### 3.5 Modal/Dialog Component

```
Visual Properties:
  Background: white
  Border Radius: radius-2xl (16px)
  Shadow: shadow-2xl
  Max Width: 500px (sm), 700px (md), 900px (lg)
  Padding: 24px (space-6)
  
Backdrop:
  Background: rgba(0, 0, 0, 0.5)
  Backdrop Filter: blur(4px)
  Z-Index: z-modal-backdrop (1040)
  
Modal Container:
  Z-Index: z-modal (1050)
  Position: Fixed, centered
  Max Height: 90vh
  Overflow: auto

Animation:
  Enter:
    Modal: scaleIn (300ms)
    Backdrop: fadeIn (200ms)
  
  Exit:
    Modal: scaleOut (200ms)
    Backdrop: fadeOut (200ms)

Structure:
  Header:
    Padding Bottom: 16px
    Border Bottom: 1px solid gray-200
    
    Title:
      Font Size: text-2xl
      Font Weight: font-semibold
      Color: gray-900
    
    Close Button:
      Position: Absolute, top-right
      Padding: 8px
      Icon: X (20px)
      Color: gray-400
      Hover: gray-600, background gray-100
  
  Body:
    Padding: 24px 0
    Color: gray-600
    Font Size: text-base
    Line Height: leading-relaxed
  
  Footer:
    Padding Top: 16px
    Border Top: 1px solid gray-200
    Display: Flex
    Justify Content: flex-end
    Gap: 12px

Behavior:
  Opening:
    1. Render backdrop with fadeIn
    2. Render modal with scaleIn (50ms delay)
    3. Lock body scroll
    4. Focus first focusable element
    5. Trap focus within modal
  
  Closing:
    1. Play exit animations
    2. Unlock body scroll
    3. Return focus to trigger element
    4. Remove from DOM after animation
  
  Interaction:
    - Click backdrop: Close modal
    - Press Escape: Close modal
    - Click close button: Close modal
    - Tab: Cycle through focusable elements

Accessibility:
  - role="dialog"
  - aria-modal="true"
  - aria-labelledby pointing to title
  - aria-describedby pointing to body
  - Focus trap
  - Restore focus on close
```

### 3.6 Toast Notification Component

```
Visual Properties:
  Background: white
  Border Radius: radius-lg (8px)
  Shadow: shadow-xl
  Padding: 16px
  Min Width: 300px
  Max Width: 450px
  Border Left: 4px solid (variant color)

Position:
  Top Right: 24px from top, 24px from right
  Z-Index: z-toast (1080)

Variants:
  Success:
    Border Color: success-500
    Icon: CheckCircle (success-500)
    Background: success-50
  
  Error:
    Border Color: error-500
    Icon: XCircle (error-500)
    Background: error-50
  
  Warning:
    Border Color: warning-500
    Icon: AlertTriangle (warning-500)
    Background: warning-50
  
  Info:
    Border Color: info-500
    Icon: Info (info-500)
    Background: info-50

Structure:
  Icon:
    Size: 20px
    Position: Left
    Margin Right: 12px
  
  Content:
    Title: text-sm, font-medium, gray-900
    Message: text-sm, gray-600
  
  Close Button:
    Position: Absolute, top-right
    Padding: 4px
    Icon: X (16px)
    Hover: Background gray-100

Animation:
  Enter: slideInRight (300ms)
  Exit: slideInRight reverse (200ms)
  
  Auto Dismiss:
    Duration: 5000ms
    Progress Bar: 
      Height: 2px
      Background: variant-color
      Animation: Width 0 to 100% over duration

Behavior:
  Multiple Toasts:
    - Stack vertically
    - Gap: 12px
    - Max visible: 5
    - Older toasts fade out
  
  User Interaction:
    - Hover: Pause auto-dismiss timer
    - Leave: Resume timer
    - Click close: Dismiss immediately
    - Swipe right (mobile): Dismiss
```

### 3.7 Table Component

```
Visual Properties:
  Background: white
  Border: 1px solid gray-200
  Border Radius: radius-lg
  Overflow: hidden

Header Row:
  Background: gray-50
  Border Bottom: 1px solid gray-200
  Font Size: text-sm
  Font Weight: font-semibold
  Color: gray-700
  Padding: 12px 16px
  Height: 44px
  
  Sort Icon:
    Size: 16px
    Color: gray-400
    Position: Right of header text
    Hover: gray-600

Body Rows:
  Background: white
  Border Bottom: 1px solid gray-100
  Padding: 12px 16px
  Height: 60px
  Font Size: text-sm
  Color: gray-600
  
  States:
    Hover:
      Background: gray-50
      Transition: transition-fast
    
    Selected:
      Background: primary-50
      Border Left: 3px solid primary-500
    
    Clickable:
      Cursor: pointer

Empty State:
  Padding: 48px 24px
  Text Align: center
  Color: gray-500
  
  Icon: 
    Size: 48px
    Color: gray-300
    Margin Bottom: 16px
  
  Message:
    Font Size: text-base
    Color: gray-600

Loading State:
  Show skeleton rows (5 rows)
  Each cell: Animated shimmer
  Animation: shimmer (1.5s infinite)

Pagination:
  Position: Bottom of table
  Padding: 16px
  Border Top: 1px solid gray-200
  Display: Flex, justify-content: space-between
  
  Page Info:
    Font Size: text-sm
    Color: gray-600
  
  Page Controls:
    Buttons: Icon buttons
    Gap: 8px
    Disabled: gray-300

Responsive:
  Mobile (<768px):
    - Hide non-essential columns
    - Stack important data vertically
    - Add horizontal scroll
    - Show "View More" button per row
```

### 3.8 Badge Component

```
Visual Properties:
  Padding: 4px 8px
  Border Radius: radius-full
  Font Size: text-xs (12px)
  Font Weight: font-medium
  Line Height: 1
  Display: inline-flex
  Align Items: center
  Gap: 4px

Variants:
  Success:
    Background: success-100
    Color: success-700
    Border: 1px solid success-200
  
  Error:
    Background: error-100
    Color: error-700
    Border: 1px solid error-200
  
  Warning:
    Background: warning-100
    Color: warning-700
    Border: 1px solid warning-200
  
  Info:
    Background: info-100
    Color: info-700
    Border: 1px solid info-200
  
  Neutral:
    Background: gray-100
    Color: gray-700
    Border: 1px solid gray-200

Sizes:
  Small:
    Padding: 2px 6px
    Font Size: text-xs (10px)
  
  Medium (Default):
    Padding: 4px 8px
    Font Size: text-xs (12px)
  
  Large:
    Padding: 6px 12px
    Font Size: text-sm (14px)

With Icon:
  Icon Size: 12px (small), 14px (medium), 16px (large)
  Icon Position: Left of text
  Gap: 4px

Dot Badge (Notification):
  Size: 8px circle
  Background: error-500
  Position: Absolute, top-right
  Border: 2px solid white
  Animation: pulse (2s infinite)
```

### 3.9 Progress Bar Component

```
Visual Properties:
  Height: 8px
  Background: gray-200
  Border Radius: radius-full
  Overflow: hidden
  
Progress Fill:
  Background: Linear gradient based on percentage
  Border Radius: radius-full
  Transition: width 300ms ease-out
  
  Color Variants:
    0-50%: error-500 (Red)
    51-79%: warning-500 (Yellow)
    80-100%: success-500 (Green)
  
  Or Single Color:
    Primary: primary-600
    Success: success-500
    Error: error-500

With Label:
  Position: Above bar
  Display: Flex, justify-content: space-between
  Margin Bottom: 8px
  
  Label Text:
    Font Size: text-sm
    Font Weight: font-medium
    Color: gray-700
  
  Percentage:
    Font Size: text-sm
    Font Weight: font-semibold
    Color: gray-900

Animated:
  Loading State:
    Background: Shimmer gradient
    Animation: shimmer 1.5s infinite
  
  Incrementing:
    Transition: width 300ms ease-out
    On complete: Pulse animation

Sizes:
  Small: Height 4px
  Medium: Height 8px (default)
  Large: Height 12px
```

### 3.10 Avatar Component

```
Visual Properties:
  Border Radius: radius-full (circle)
  Overflow: hidden
  Display: flex, align-items: center, justify-content: center
  Background: gradient from primary-400 to primary-600
  
Sizes:
  Extra Small: 24px
  Small: 32px
  Medium: 40px (default)
  Large: 48px
  Extra Large: 64px

With Image:
  Object Fit: cover
  Object Position: center

Without Image (Initials):
  Font Weight: font-semibold
  Color: white
  Font Size: Based on size
    XS: text-xs (10px)
    SM: text-sm (12px)
    MD: text-base (14px)
    LG: text-lg (16px)
    XL: text-xl (20px)
  
  Background Colors (based on user ID hash):
    primary-500, success-500, error-500, 
    warning-500, info-500, purple-500,
    pink-500, indigo-500

Status Indicator:
  Size: 25% of avatar size
  Position: Bottom-right
  Border: 2px solid white
  Border Radius: radius-full
  
  Colors:
    Online: success-500
    Offline: gray-400
    Away: warning-500
    Busy: error-500

Group Avatars:
  Layout: Overlapping
  Overlap: -8px (medium), -12px (large)
  Z-Index: Decreasing from left to right
  Border: 2px solid white
  
  "+N More":
    Same size as avatars
    Background: gray-200
    Color: gray-700
    Font Weight: font-semibold
```

---

### 3.11 Transaction Card Component

```
Visual Properties:
  Background: white
  Border: 1px solid gray-200
  Border Radius: radius-lg (8px)
  Padding: 16px
  Display: flex
  Align Items: center
  Gap: 12px
  Cursor: pointer
  
Structure:
  Category Icon:
    Size: 40px circle
    Background: category-color with 10% opacity
    Icon: 20px, category-color
    Border Radius: radius-full
  
  Transaction Details:
    Flex: 1
    
    Description:
      Font Size: text-base
      Font Weight: font-medium
      Color: gray-900
      Margin Bottom: 4px
    
    Metadata:
      Font Size: text-sm
      Color: gray-500
      Display: flex
      Gap: 8px
      
      Items:
        - Category name
        - Bullet separator (·)
        - Account name
        - Bullet separator (·)
        - Date (relative: "Today", "Yesterday", or date)
  
  Amount:
    Font Family: font-mono
    Font Size: text-lg
    Font Weight: font-semibold
    Text Align: right
    
    Income: success-600
    Expense: error-600
    Transfer: info-600
    
    Prefix: 
      Income: +
      Expense: −
      Transfer: →

States:
  Hover:
    Background: gray-50
    Border: 1px solid gray-300
    Transform: translateX(4px)
    Transition: transition-fast
  
  Active/Selected:
    Background: primary-50
    Border: 2px solid primary-500
  
  Pending:
    Opacity: 0.6
    Badge: "Pending" (warning variant)

Mobile (<640px):
  Reduce padding: 12px
  Reduce icon size: 32px
  Font sizes: Slightly smaller
```

### 3.12 Budget Progress Card

```
Visual Properties:
  Background: white
  Border: 1px solid gray-200
  Border Radius: radius-xl (12px)
  Padding: 20px
  Shadow: shadow-sm

Structure:
  Header:
    Display: flex
    Justify Content: space-between
    Align Items: center
    Margin Bottom: 16px
    
    Budget Name:
      Font Size: text-lg
      Font Weight: font-semibold
      Color: gray-900
    
    Period Badge:
      Background: gray-100
      Color: gray-700
      Padding: 4px 8px
      Border Radius: radius-md
      Font Size: text-xs
  
  Amount Display:
    Display: flex
    Justify Content: space-between
    Margin Bottom: 12px
    
    Spent Amount:
      Font Family: font-mono
      Font Size: text-2xl
      Font Weight: font-bold
      Color: Based on percentage:
        0-79%: gray-900
        80-99%: warning-600
        100%+: error-600
    
    Budget Limit:
      Font Family: font-mono
      Font Size: text-sm
      Color: gray-500
      Align Self: flex-end
      
      Format: "of $X,XXX.XX"
  
  Progress Bar:
    Height: 12px
    Background: gray-200
    Border Radius: radius-full
    Overflow: hidden
    Margin Bottom: 12px
    
    Fill:
      Height: 100%
      Border Radius: radius-full
      Transition: width 500ms ease-out
      
      Colors by percentage:
        0-79%: success-500
        80-99%: warning-500
        100%+: error-500 (with pulse animation)
    
    Markers (if budget > 50%):
      80% threshold: Vertical line (warning-300)
      100% threshold: Vertical line (error-300)
  
  Footer:
    Display: flex
    Justify Content: space-between
    Font Size: text-sm
    
    Percentage:
      Color: Based on progress
      Font Weight: font-medium
      
      Icon: 
        <80%: TrendingUp (success-500)
        80-99%: AlertTriangle (warning-500)
        100%+: AlertCircle (error-500)
    
    Remaining:
      Color: gray-600
      
      Format: 
        Positive: "$XXX remaining"
        Negative: "$XXX over budget"

States:
  Hover:
    Shadow: shadow-md
    Transform: translateY(-2px)
    Transition: transition-base
  
  Alert State (>=80%):
    Border: 2px solid warning-300
    Background: warning-50
    Animation: pulse-slow (3s infinite)
  
  Exceeded State (>=100%):
    Border: 2px solid error-300
    Background: error-50
    Animation: pulse-slow (3s infinite)

Responsive:
  Mobile (<640px):
    Padding: 16px
    Font sizes: Reduce by 1 step
```

### 3.13 Account Balance Card

```
Visual Properties:
  Background: Gradient based on account type
  Border Radius: radius-xl (12px)
  Padding: 24px
  Shadow: shadow-lg
  Color: white
  Position: relative
  Overflow: hidden

Gradient Backgrounds:
  Checking: linear-gradient(135deg, primary-600, primary-800)
  Savings: linear-gradient(135deg, success-500, success-700)
  Credit Card: linear-gradient(135deg, error-500, error-700)
  Cash: linear-gradient(135deg, warning-500, warning-700)
  Investment: linear-gradient(135deg, purple-500, purple-700)

Structure:
  Background Pattern:
    Position: absolute
    Opacity: 0.1
    Pattern: Dots or geometric shapes
  
  Header:
    Display: flex
    Justify Content: space-between
    Margin Bottom: 32px
    
    Account Name:
      Font Size: text-base
      Font Weight: font-medium
      Color: white with 90% opacity
    
    Account Type Icon:
      Size: 24px
      Color: white with 80% opacity
  
  Balance:
    Margin Bottom: 8px
    
    Label:
      Font Size: text-sm
      Color: white with 80% opacity
      Margin Bottom: 4px
    
    Amount:
      Font Family: font-mono
      Font Size: text-4xl
      Font Weight: font-bold
      Color: white
      Letter Spacing: -0.02em
  
  Footer:
    Display: flex
    Justify Content: space-between
    Align Items: center
    
    Last Transaction:
      Font Size: text-sm
      Color: white with 80% opacity
    
    Quick Actions:
      Display: flex
      Gap: 8px
      
      Action Buttons:
        Background: white with 20% opacity
        Padding: 6px 12px
        Border Radius: radius-md
        Font Size: text-sm
        
        Hover:
          Background: white with 30% opacity

States:
  Hover:
    Transform: translateY(-4px)
    Shadow: shadow-2xl
    Transition: transition-base
  
  Loading:
    Shimmer effect over gradient
    Balance: Skeleton loader

Responsive:
  Mobile (<640px):
    Padding: 20px
    Balance Amount: text-3xl
```

### 3.14 Chart Components

#### 3.14.1 Line Chart (Income vs Expense)

```
Visual Properties:
  Container:
    Background: white
    Border: 1px solid gray-200
    Border Radius: radius-lg
    Padding: 20px
    Min Height: 300px
  
  Chart Area:
    Background: transparent
    Grid Lines: gray-200 (1px dashed)
    Grid Opacity: 0.3
  
  Axes:
    Color: gray-600
    Font Size: text-xs
    Font Weight: font-medium
    
    X-Axis: Months/Dates
    Y-Axis: Currency amounts
  
  Lines:
    Income Line:
      Color: success-500
      Width: 3px
      Curve: smooth (cardinal)
      
      Dot:
        Size: 8px
        Fill: success-500
        Border: 3px white
      
      Hover Dot:
        Size: 12px
        Shadow: shadow-lg
    
    Expense Line:
      Color: error-500
      Width: 3px
      Curve: smooth (cardinal)
  
  Tooltip:
    Background: white
    Border: 1px solid gray-200
    Border Radius: radius-md
    Padding: 12px
    Shadow: shadow-lg
    
    Date: text-sm, gray-600
    Values: text-base, font-semibold
    Icons: Currency symbols

Animation:
  Initial Load:
    Lines: Draw from left to right (1s)
    Dots: Scale in (delay based on position)
  
  Hover:
    Dot: Scale 1.5x, show tooltip
    Line: Increase opacity

Legend:
  Position: Top right
  Display: flex
  Gap: 16px
  
  Item:
    Display: flex
    Align Items: center
    Gap: 8px
    
    Indicator:
      Width: 16px
      Height: 3px
      Background: Line color
      Border Radius: radius-full
    
    Label:
      Font Size: text-sm
      Color: gray-700
```

#### 3.14.2 Pie Chart (Category Breakdown)

```
Visual Properties:
  Container:
    Background: white
    Padding: 20px
    
  Chart:
    Size: 300px diameter
    Center: Donut hole (100px diameter)
  
  Segments:
    Stroke: white (4px)
    
    Colors: Category-specific colors
    
    Hover:
      Transform: Scale 1.05
      Shadow: shadow-md
      Cursor: pointer
  
  Center Label:
    Position: Absolute center
    
    Total Label:
      Font Size: text-sm
      Color: gray-600
      Margin Bottom: 4px
    
    Total Amount:
      Font Family: font-mono
      Font Size: text-2xl
      Font Weight: font-bold
      Color: gray-900
  
  Legend:
    Position: Right side (desktop) or Below (mobile)
    Max Height: 300px
    Overflow: auto
    
    Item:
      Display: flex
      Align Items: center
      Gap: 12px
      Padding: 8px 12px
      Border Radius: radius-md
      Cursor: pointer
      
      Hover:
        Background: gray-50
      
      Color Indicator:
        Width: 12px
        Height: 12px
        Border Radius: radius-sm
        Background: Category color
      
      Label:
        Flex: 1
        Font Size: text-sm
        Color: gray-700
      
      Amount:
        Font Family: font-mono
        Font Size: text-sm
        Font Weight: font-semibold
        Color: gray-900
      
      Percentage:
        Font Size: text-xs
        Color: gray-500

Animation:
  Initial:
    Segments: Grow from center (800ms, stagger 100ms)
  
  Hover Segment:
    Tooltip appears
    Segment scales
    Corresponding legend item highlights
```

#### 3.14.3 Bar Chart (Monthly Comparison)

```
Visual Properties:
  Container:
    Background: white
    Padding: 20px
    Height: 350px
  
  Bars:
    Width: Responsive based on data points
    Gap: 8px
    Border Radius: radius-md (top only)
    
    Income Bars:
      Color: success-500
      Hover: success-600
    
    Expense Bars:
      Color: error-500
      Hover: error-600
  
  Grid:
    Horizontal lines: gray-200 (1px)
    Opacity: 0.5
  
  Axes:
    X-Axis: Month labels (text-sm, gray-600)
    Y-Axis: Amount scale (text-xs, gray-500)

Interactions:
  Hover Bar:
    Color: Darker shade
    Cursor: pointer
    Show tooltip above bar
    
    Tooltip:
      Background: gray-900
      Color: white
      Padding: 8px 12px
      Border Radius: radius-md
      Font Size: text-sm
      Arrow: Bottom center

Animation:
  Initial:
    Bars: Grow from bottom to top (600ms)
    Stagger: 50ms per bar
    Easing: ease-out
```

### 3.15 Empty State Component

```
Visual Properties:
  Container:
    Padding: 64px 24px
    Text Align: center
    Background: white or gray-50
  
  Icon:
    Size: 80px
    Color: gray-300
    Margin Bottom: 24px
    
    Common Icons:
      No Transactions: Receipt icon
      No Budgets: Target icon
      No Accounts: Wallet icon
      No Data: BarChart icon
  
  Heading:
    Font Size: text-xl
    Font Weight: font-semibold
    Color: gray-900
    Margin Bottom: 8px
  
  Description:
    Font Size: text-base
    Color: gray-600
    Max Width: 400px
    Margin: 0 auto 24px
    Line Height: leading-relaxed
  
  Action Button:
    Primary button style
    Icon: Plus or relevant action icon
    
    Examples:
      "Add Your First Transaction"
      "Create a Budget"
      "Connect an Account"

Animation:
  Enter:
    Icon: fadeIn + scale (500ms)
    Text: slideInBottom (300ms, delay 200ms)
    Button: slideInBottom (300ms, delay 400ms)
```

### 3.16 Loading Skeleton Component

```
Visual Properties:
  Background: linear-gradient(
    90deg,
    gray-200 0%,
    gray-300 50%,
    gray-200 100%
  )
  Background Size: 200% 100%
  Animation: shimmer 1.5s infinite
  Border Radius: Based on element type
  
Transaction Card Skeleton:
  Height: 72px
  Structure:
    - Circle (40px) - Category icon
    - Rectangle (60%, 16px) - Description
    - Rectangle (40%, 12px) - Metadata
    - Rectangle (80px, 20px) - Amount (right aligned)

Account Card Skeleton:
  Height: 160px
  Structure:
    - Rectangle (50%, 14px) - Account name
    - Rectangle (80%, 32px) - Balance
    - Rectangle (40%, 12px) - Last transaction

Budget Card Skeleton:
  Height: 200px
  Structure:
    - Rectangle (60%, 18px) - Budget name
    - Rectangle (40%, 24px) - Amount
    - Rectangle (100%, 12px) - Progress bar
    - Rectangle (30%, 12px) - Percentage

Chart Skeleton:
  Height: 300px
  Structure:
    Multiple rectangles of varying heights
    Arranged like bar chart
```

### 3.17 Date Picker Component

```
Visual Properties:
  Trigger:
    Same as input field
    Icon: Calendar (right side)
    Placeholder: "Select date"
  
  Popover:
    Background: white
    Border: 1px solid gray-200
    Border Radius: radius-lg
    Shadow: shadow-xl
    Padding: 16px
    Z-Index: z-popover
  
  Calendar Header:
    Display: flex
    Justify Content: space-between
    Align Items: center
    Margin Bottom: 12px
    
    Month/Year Display:
      Font Size: text-base
      Font Weight: font-semibold
      Color: gray-900
    
    Navigation Buttons:
      Icon buttons
      Icons: ChevronLeft, ChevronRight
  
  Calendar Grid:
    Display: grid
    Grid Template Columns: repeat(7, 1fr)
    Gap: 4px
    
    Day Headers:
      Font Size: text-xs
      Font Weight: font-medium
      Color: gray-500
      Text Align: center
      Padding: 8px
    
    Day Cells:
      Aspect Ratio: 1
      Border Radius: radius-md
      Display: flex
      Align Items: center
      Justify Content: center
      Font Size: text-sm
      Cursor: pointer
      
      States:
        Default: 
          Color: gray-900
          Background: white
        
        Hover:
          Background: gray-100
        
        Selected:
          Background: primary-600
          Color: white
          Font Weight: font-semibold
        
        Today:
          Border: 2px solid primary-500
          Font Weight: font-semibold
        
        Other Month:
          Color: gray-400
        
        Disabled:
          Color: gray-300
          Cursor: not-allowed

Behavior:
  Opening:
    Position: Below trigger (or above if not enough space)
    Animation: scaleIn (200ms)
    Focus: Current selected date or today
  
  Selection:
    Click date: Select and close
    Keyboard:
      Arrow keys: Navigate dates
      Enter: Select focused date
      Escape: Close without selecting
  
  Range Selection:
    First click: Start date (highlight)
    Hover: Show range preview (light background)
    Second click: End date (highlight)
    Range dates: Background primary-100
```

### 3.18 Currency Input Component

```
Visual Properties:
  Same as number input
  
  Prefix Symbol:
    Position: Left of input
    Color: gray-500
    Font Size: text-base
    Padding: 0 8px 0 12px
  
  Input Value:
    Font Family: font-mono
    Text Align: right
    Font Weight: font-medium
    Color: gray-900

Behavior:
  Formatting:
    On Focus:
      Show raw number: 1234.56
      Select all text
    
    On Blur:
      Format with commas: 1,234.56
      Add 2 decimal places
      Add currency symbol prefix
  
  Input Validation:
    Allow: 0-9, decimal point
    Block: Letters, special characters
    Max decimal places: 2
  
  Large Numbers:
    Support: Up to 15 digits
    Format: 1,234,567.89
    Scientific notation: Not used

States:
  Positive Amount (Income):
    Text Color: success-600
    Prefix: + (optional)
  
  Negative Amount (Expense):
    Text Color: error-600
    Prefix: - (optional)
  
  Zero:
    Text Color: gray-400
    Placeholder: "0.00"
```

### 3.19 Search Input Component

```
Visual Properties:
  Base: Text input
  
  Search Icon:
    Position: Left (12px from left)
    Size: 20px
    Color: gray-400
  
  Input:
    Padding Left: 44px (to account for icon)
    Placeholder: "Search transactions..."
  
  Clear Button:
    Position: Right (8px from right)
    Size: 20px
    Color: gray-400
    Hover: gray-600
    Cursor: pointer
    Display: Only when input has value
    
    Animation: fadeIn (150ms)

States:
  Focus:
    Icon Color: primary-500
    Border: 2px solid primary-500
  
  With Results:
    Border Radius: top only (if dropdown shown)
    Border Bottom: none

Results Dropdown:
  Position: Below input
  Background: white
  Border: 1px solid gray-200
  Border Top: none
  Border Radius: bottom only
  Shadow: shadow-lg
  Max Height: 400px
  Overflow: auto
  
  Result Item:
    Padding: 12px 16px
    Cursor: pointer
    
    Hover:
      Background: gray-50
    
    Selected:
      Background: primary-50
    
    Structure:
      Icon: Category icon (left)
      Text: Highlight matching text
      Amount: Right aligned

Behavior:
  Typing:
    Debounce: 300ms
    Min Characters: 2
    Show loading: After 300ms
  
  Keyboard Navigation:
    Arrow Down: Next result
    Arrow Up: Previous result
    Enter: Select highlighted result
    Escape: Close dropdown, clear input
  
  No Results:
    Show empty state
    Message: "No transactions found"
    Icon: Search with slash (20px)
```

### 3.20 Filter Component

```
Visual Properties:
  Trigger Button:
    Variant: Secondary button
    Icon: Filter icon (left)
    Text: "Filters"
    Badge: Number of active filters (if any)
  
  Popover:
    Background: white
    Border: 1px solid gray-200
    Border Radius: radius-lg
    Shadow: shadow-xl
    Padding: 20px
    Width: 320px
    Max Height: 600px
    Overflow: auto
  
  Header:
    Display: flex
    Justify Content: space-between
    Margin Bottom: 16px
    
    Title:
      Font Size: text-lg
      Font Weight: font-semibold
      Color: gray-900
    
    Clear All:
      Font Size: text-sm
      Color: primary-600
      Cursor: pointer
      Hover: primary-700
  
  Filter Groups:
    Margin Bottom: 20px
    
    Group Title:
      Font Size: text-sm
      Font Weight: font-semibold
      Color: gray-700
      Margin Bottom: 8px
    
    Filter Options:
      Display: flex
      Flex Direction: column
      Gap: 8px
      
      Checkbox Option:
        Display: flex
        Align Items: center
        Gap: 8px
        Padding: 8px
        Border Radius: radius-md
        Cursor: pointer
        
        Hover:
          Background: gray-50
        
        Checkbox:
          Size: 20px
          Color: primary-600
        
        Label:
          Font Size: text-sm
          Color: gray-700
  
  Date Range:
    Display: flex
    Gap: 12px
    
    From/To Inputs:
      Flex: 1
      Label above input
  
  Footer:
    Display: flex
    Gap: 12px
    Padding Top: 16px
    Border Top: 1px solid gray-200
    
    Reset Button:
      Flex: 1
      Variant: Secondary
    
    Apply Button:
      Flex: 1
      Variant: Primary

Active Filters Display:
  Position: Below filter button
  Display: flex
  Gap: 8px
  Flex Wrap: wrap
  Margin Top: 12px
  
  Filter Chip:
    Background: primary-100
    Color: primary-700
    Padding: 6px 12px
    Border Radius: radius-full
    Font Size: text-sm
    Display: flex
    Align Items: center
    Gap: 6px
    
    Remove Button:
      Size: 16px
      Color: primary-700
      Cursor: pointer
      Hover: primary-900
```

---

### 3.21 Page Layouts

#### 3.21.1 Dashboard Layout

```
Desktop Layout (>1024px):

Header:
  Height: 72px
  Background: white
  Border Bottom: 1px solid gray-200
  Padding: 0 32px
  Z-Index: z-sticky
  Position: sticky, top: 0
  
  Structure:
    Logo: Left (with app name)
    Search: Center (max-width 600px)
    User Menu: Right (notifications + avatar)

Sidebar:
  Width: 280px
  Background: gray-50
  Border Right: 1px solid gray-200
  Height: calc(100vh - 72px)
  Position: sticky
  Overflow: auto
  Padding: 24px 16px
  
  Navigation Items:
    Padding: 12px 16px
    Border Radius: radius-lg
    Gap: 12px
    Font Size: text-base
    
    Active:
      Background: primary-100
      Color: primary-700
      Font Weight: font-semibold
    
    Hover:
      Background: gray-100
    
    Icon: 20px (left)
    Badge: Right side (for notifications)

Main Content:
  Margin Left: 280px
  Padding: 32px
  Max Width: 1400px
  Background: gray-50
  Min Height: calc(100vh - 72px)
  
  Content Grid:
    Display: grid
    Grid Template Columns: 1fr 1fr
    Gap: 24px
    
    Full Width Sections:
      Grid Column: span 2

Tablet Layout (768px - 1023px):

Sidebar:
  Width: 72px (collapsed)
  Show icons only
  
  Hover:
    Width: 280px (expand)
    Show full navigation

Main Content:
  Margin Left: 72px
  Padding: 24px
  
  Grid: Single column

Mobile Layout (<768px):

Header:
  Padding: 0 16px
  
  Hamburger Menu: Left
  Logo: Center
  Avatar: Right

Sidebar:
  Display: none (default)
  
  Open State:
    Position: fixed
    Left: 0
    Top: 72px
    Width: 100%
    Height: calc(100vh - 72px)
    Background: white
    Z-Index: z-modal
    Transform: translateX(-100%)
    Transition: transform 300ms
    
    Open:
      Transform: translateX(0)

Main Content:
  Margin Left: 0
  Padding: 16px
  
  All sections: Full width
```

#### 3.21.2 Transaction List Page

```
Desktop Layout:

Page Header:
  Padding: 0 0 24px 0
  
  Title:
    Font Size: text-3xl
    Font Weight: font-bold
    Color: gray-900
  
  Actions Bar:
    Display: flex
    Gap: 12px
    Margin Top: 16px
    
    Items:
      - Add Transaction button (primary)
      - Import CSV button (secondary)
      - Export button (secondary)
      - Filter button (secondary)

Filters Section:
  Background: white
  Padding: 20px
  Border Radius: radius-lg
  Margin Bottom: 24px
  Border: 1px solid gray-200
  
  Layout:
    Display: grid
    Grid Template Columns: 1fr 1fr 1fr 1fr
    Gap: 16px
    
  Active Filters:
    Grid Column: span 4
    Margin Top: 16px

Transactions Table:
  Background: white
  Border Radius: radius-lg
  Border: 1px solid gray-200
  
  Table Layout:
    - Column: Date (120px)
    - Column: Description (flex)
    - Column: Category (180px)
    - Column: Account (150px)
    - Column: Amount (120px)
    - Column: Actions (80px)

Mobile Layout:

Replace table with card list
Each transaction: Transaction Card component
Stack filters vertically
Sticky "Add Transaction" FAB (bottom-right)
```

#### 3.21.3 Budget Management Page

```
Desktop Layout:

Page Header:
  Similar to Transaction List
  
Two Column Layout:
  
  Left Column (60%):
    Active Budgets List
    Each budget: Budget Progress Card
    Gap: 16px
    
  Right Column (40%):
    Quick Stats Card:
      - Total budgets amount
      - Total spent
      - Average adherence
    
    Create Budget Card:
      - Quick create form
      - Category selector
      - Amount input
      - Period selector

Mobile Layout:

Single column
Stack all cards vertically
Collapse quick stats by default
Full-screen budget creation modal
```

#### 3.21.4 Reports & Analytics Page

```
Desktop Layout:

Time Period Selector:
  Position: Top right
  Options: This Month, Last Month, This Year, Custom
  Width: 200px

Grid Layout:
  Display: grid
  Grid Template Columns: 1fr 1fr
  Gap: 24px
  
  Chart Cards:
    - Income vs Expense (Full width)
    - Category Breakdown (Half width)
    - Monthly Trends (Half width)
    - Net Worth Over Time (Full width)

Summary Cards Row:
  Display: grid
  Grid Template Columns: repeat(4, 1fr)
  Gap: 16px
  Margin Bottom: 24px
  
  Cards:
    - Total Income
    - Total Expenses
    - Net Savings
    - Savings Rate

Mobile Layout:

All charts: Full width
Stack vertically
Summary cards: 2 columns
Scrollable chart container
```

### 3.22 Responsive Behavior

#### 3.22.1 Breakpoint Strategy

```
Mobile First Approach:

Base Styles (0px+):
  - Mobile layout
  - Single column
  - Stack elements vertically
  - Touch-friendly sizing (44px minimum)
  - Simplified navigation

Small Tablet (640px+):
  - Two column grid for cards
  - Larger touch targets
  - Expanded spacing

Tablet (768px+):
  - Show collapsed sidebar
  - Two/three column layouts
  - Desktop-style navigation
  - Larger font sizes

Desktop (1024px+):
  - Full sidebar
  - Multi-column layouts
  - Hover states active
  - Full feature set

Large Desktop (1280px+):
  - Wider content areas
  - More columns in grids
  - Larger charts
  - More visible data
```

#### 3.22.2 Component Adaptations

```
Navigation:
  Mobile: Bottom tab bar or hamburger menu
  Tablet: Collapsed sidebar (icons only)
  Desktop: Full sidebar with labels

Tables:
  Mobile: Card list or horizontal scroll
  Tablet: Simplified table (fewer columns)
  Desktop: Full table with all columns

Forms:
  Mobile: Full width, stacked
  Tablet: Two column layout
  Desktop: Multi-column layout

Modals:
  Mobile: Full screen
  Tablet: 90% width, centered
  Desktop: Fixed width (500-700px), centered

Charts:
  Mobile: Simplified, essential data only
  Tablet: Medium detail
  Desktop: Full detail with legends

Data Grids:
  Mobile: 1 column
  Tablet: 2 columns
  Desktop: 3-4 columns
```

#### 3.22.3 Touch vs Mouse Interactions

```
Touch Devices:

Button Size:
  Minimum: 44px x 44px
  Padding: Increased by 4px
  Gap between items: Minimum 8px

Gestures:
  - Swipe left on transaction: Delete action
  - Swipe right on transaction: Edit action
  - Pull to refresh: Reload data
  - Long press: Show context menu
  - Pinch to zoom: Charts (if applicable)

Hover States:
  - Disabled on touch devices
  - Replace with active states
  - Use :active instead of :hover

Mouse/Desktop:

Hover Effects:
  - All interactive elements
  - Show tooltips on hover
  - Display additional info
  - Cursor changes

Context Menus:
  - Right-click support
  - Dropdown menus
  - Quick actions

Keyboard Shortcuts:
  - Tab navigation
  - Ctrl/Cmd + key combinations
  - Enter to submit
  - Escape to cancel
```

#### 3.22.4 Loading States

```
Initial Page Load:
  1. Show skeleton loaders
  2. Load critical data first
  3. Progressive enhancement
  4. Fade in content as it loads

Infinite Scroll:
  Show spinner at bottom
  Load more items automatically
  "Load More" button as fallback

Data Refresh:
  Pull-to-refresh (mobile)
  Refresh button (desktop)
  Subtle loading indicator
  Don't block UI

Optimistic Updates:
  Update UI immediately
  Show pending state
  Revert on error
  Show error message
```

### 3.23 Accessibility Specifications

#### 3.23.1 Keyboard Navigation

```
Tab Order:
  - Logical flow (left to right, top to bottom)
  - Skip links for main content
  - Trapped focus in modals
  - Visible focus indicators

Keyboard Shortcuts:
  Global:
    - /: Focus search
    - n: New transaction
    - b: View budgets
    - h: Go to home/dashboard
    - ?: Show keyboard shortcuts
  
  Navigation:
    - Tab: Next element
    - Shift + Tab: Previous element
    - Arrow keys: Navigate lists/menus
    - Enter/Space: Activate
    - Escape: Close/Cancel

Focus Indicators:
  Style:
    Outline: 2px solid primary-500
    Outline Offset: 2px
    Border Radius: radius-md
  
  Never remove: Always visible on keyboard nav
```

#### 3.23.2 Screen Reader Support

```
Semantic HTML:
  - Use proper heading hierarchy (h1 → h2 → h3)
  - nav, main, aside, footer elements
  - article for transactions
  - section for grouped content

ARIA Labels:
  - aria-label for icon buttons
  - aria-labelledby for sections
  - aria-describedby for help text
  - aria-live for dynamic updates
  - aria-expanded for dropdowns
  - aria-selected for active items

Alt Text:
  - Descriptive alt text for images
  - Empty alt for decorative images
  - Icon alternatives in text

Announcements:
  Success: "Transaction added successfully"
  Error: "Please fix the following errors"
  Loading: "Loading transactions"
  Navigation: "Navigated to Budgets page"
```

#### 3.23.3 Color Contrast

```
WCAG AA Compliance (4.5:1 ratio):

Text:
  - Body text on white: gray-700 or darker
  - Small text: gray-800 or darker
  - Large text (18px+): gray-600 or darker

Interactive Elements:
  - Links: primary-600 (sufficient contrast)
  - Buttons: Meet contrast requirements
  - Form borders: Minimum 3:1 ratio

Color-Blind Friendly:
  - Don't rely on color alone
  - Use icons + color
  - Use patterns in charts
  - Sufficient text labels

Success/Error States:
  - Success: Green + checkmark icon
  - Error: Red + error icon
  - Warning: Yellow + warning icon
  - Info: Blue + info icon
```

#### 3.23.4 Form Accessibility

```
Labels:
  - Every input has associated label
  - Use <label> element with for attribute
  - Or wrap input in label
  - Placeholder is not a substitute

Error Messages:
  - Announce with aria-live="polite"
  - Associate with aria-describedby
  - Clear, actionable messages
  - Display near the input

Required Fields:
  - Visual indicator (asterisk)
  - aria-required="true"
  - Mentioned in label

Help Text:
  - Associated with aria-describedby
  - Visible and clear
  - Before or after input
```

### 3.24 Animation & Motion

#### 3.24.1 Motion Preferences

```
Respect prefers-reduced-motion:

@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}

Provide Settings Toggle:
  - Enable/Disable animations
  - Stored in user preferences
  - Applied globally
```

#### 3.24.2 Performance

```
Animation Performance:

Use Transform and Opacity:
  - transform: translateX/Y/Z
  - transform: scale
  - transform: rotate
  - opacity
  
Avoid:
  - Animating width/height
  - Animating top/left/right/bottom
  - Animating background-color (use opacity)

Will-Change:
  - Apply to elements that will animate
  - Remove after animation complete
  - Don't overuse

GPU Acceleration:
  - Use translate3d(0,0,0)
  - Add backface-visibility: hidden
```

---

### 3.25 Micro-interactions & User Feedback

#### 3.25.1 Button Interactions

```
Click Feedback:
  1. User hovers (desktop):
     - Transition background color (150ms)
     - Lift button with transform: translateY(-2px)
     - Increase shadow
  
  2. User presses:
     - Transform: translateY(0) or scale(0.98)
     - Reduce shadow
     - Duration: 100ms
  
  3. User releases:
     - Return to hover state (if still hovering)
     - Or return to default state
  
  4. Loading state:
     - Spinner fades in (200ms)
     - Text fades out (150ms)
     - Button becomes disabled
     - Width: Maintain to prevent layout shift

Ripple Effect (Material Design):
  - On click: Create circular ripple from click point
  - Color: white with 20% opacity (dark buttons)
  - Color: black with 10% opacity (light buttons)
  - Duration: 600ms
  - Easing: ease-out
```

#### 3.25.2 Input Field Interactions

```
Focus Transition:
  1. User clicks input:
     - Border transitions to primary-500 (150ms)
     - Focus ring expands outward (200ms)
     - Label moves up (if floating label)
     - Placeholder fades out
  
  2. User types:
     - Character appears with subtle fade-in
     - No animation on rapid typing
  
  3. Validation (on blur):
     - If error: Border becomes red (200ms)
     - Error icon slides in from right (200ms)
     - Error message slides down (200ms)
     - Shake animation if critical error
  
  4. Success state:
     - Green checkmark fades in (200ms)
     - Border becomes green (200ms)

Auto-complete:
  - Dropdown slides down (200ms)
  - Items fade in with stagger (50ms each)
  - Highlight on keyboard navigation
  - Selected item scales slightly
```

#### 3.25.3 Form Submission

```
Submit Flow:
  1. User clicks submit:
     - Button shows loading state
     - Form fields become disabled
     - Subtle pulse on submit button
  
  2. Validation in progress:
     - Show progress indicator
     - Disable all inputs
  
  3. Success:
     - Success checkmark animation (scale in)
     - Green border flash on form
     - Toast notification slides in
     - Confetti animation (for important actions)
     - Redirect after 1000ms
  
  4. Error:
     - Shake form container (300ms)
     - Focus first error field
     - Error messages slide in
     - Red border flash
     - Return button to active state
```

#### 3.25.4 List & Card Interactions

```
Card Hover:
  - Lift card: translateY(-4px) in 200ms
  - Increase shadow: from sm to lg
  - Subtle scale: scale(1.01)
  - Border color: Slightly darker

List Item Hover:
  - Background color fade (150ms)
  - Slight padding increase (feels responsive)
  - Cursor changes to pointer

Drag and Drop:
  - On grab: 
    - Scale up: scale(1.05)
    - Increase shadow
    - Rotate slightly: rotate(2deg)
    - Cursor: grabbing
  
  - While dragging:
    - Opacity: 0.8
    - Follow cursor smoothly
    - Show drop zones with dashed border
  
  - On drop:
    - Snap to position with spring animation
    - Flash background color
    - Return to normal scale and rotation

Delete/Swipe Action:
  - Swipe left/right gesture
  - Card slides revealing action buttons
  - Delete button fades in
  - On confirm:
    - Card slides out (300ms)
    - Cards below slide up to fill space
    - Show undo toast for 5s
```

#### 3.25.5 Modal & Overlay Interactions

```
Modal Open:
  1. Backdrop:
     - Opacity: 0 → 0.5 (200ms)
     - Blur: 0 → 4px (200ms)
  
  2. Modal (50ms delay):
     - Opacity: 0 → 1 (300ms)
     - Scale: 0.95 → 1 (300ms)
     - Transform: translateY(20px) → 0
  
  3. Content:
     - Elements fade in with stagger
     - Focus first input after animation

Modal Close:
  1. Modal:
     - Opacity: 1 → 0 (200ms)
     - Scale: 1 → 0.95 (200ms)
  
  2. Backdrop:
     - Opacity: 0.5 → 0 (200ms)
     - Remove from DOM after animation

Drawer Slide:
  - From right: translateX(100%) → 0 (300ms)
  - From left: translateX(-100%) → 0 (300ms)
  - From bottom: translateY(100%) → 0 (300ms)
```

#### 3.25.6 Data Loading States

```
Skeleton Loading:
  - Shimmer animation (1.5s infinite)
  - Gradient: gray-200 → gray-300 → gray-200
  - Background-position animates
  - Maintains layout dimensions

Spinner:
  - Rotation: 360deg continuous
  - Duration: 1s linear infinite
  - Size based on context (16-48px)
  - Color: primary-600 or white

Progress Bar:
  - Width animates smoothly (300ms ease-out)
  - Color changes based on percentage
  - Pulse when near completion
  - Striped animation for indeterminate

Content Fade In:
  - After load: opacity 0 → 1 (300ms)
  - Stagger children (50ms delay each)
  - Slide up slightly: translateY(10px) → 0
```

#### 3.25.7 Notification Animations

```
Toast Enter:
  - Slide in from right (300ms)
  - Slight bounce at end
  - Easing: cubic-bezier(0.68, -0.55, 0.265, 1.55)

Toast Progress:
  - Progress bar: width 100% → 0
  - Duration: 5000ms linear
  - Pause on hover

Toast Exit:
  - Slide out to right (200ms)
  - Fade out simultaneously
  - Cards below slide up smoothly

Badge Pulse:
  - New notification: Scale pulse
  - Scale: 1 → 1.2 → 1 (300ms)
  - Repeat: 3 times
  - Then remain visible

Number Count Up:
  - On data change: Count from old to new
  - Duration: 500ms
  - Easing: ease-out
  - Format numbers during animation
```

#### 3.25.8 Chart Animations

```
Initial Load:
  Line Charts:
    - Draw path from 0% → 100% (1000ms)
    - Easing: ease-in-out
    - Points fade in after line
  
  Bar Charts:
    - Bars grow from bottom (800ms)
    - Stagger: 50ms per bar
    - Easing: ease-out
  
  Pie Charts:
    - Segments draw clockwise (1000ms)
    - Each segment: Slight delay
    - Center text fades in after segments

Data Update:
  - Smooth transition (500ms)
  - Morph existing shapes
  - Cross-fade if major change
  - Highlight changed data points

Hover Interactions:
  - Point/Bar scales up (150ms)
  - Tooltip fades in (100ms)
  - Connecting line appears
  - Dim other data points slightly
```

#### 3.25.9 Success Feedback

```
Transaction Added:
  - Checkmark icon animation (scale + rotate)
  - Success toast notification
  - New transaction slides into list
  - List items shift down smoothly
  - Green flash on account balance
  - Confetti burst (optional, for milestones)

Budget Created:
  - Success animation
  - Card slides in from top
  - Progress bar animates to current state
  - Celebration if first budget

Goal Completed:
  - Trophy/star animation
  - Confetti effect
  - Progress bar fills to 100%
  - Completion modal with celebration
  - Fireworks animation (optional)
```

#### 3.25.10 Error Feedback

```
Validation Error:
  - Input field shake (300ms)
  - 3-4 quick oscillations
  - Error message slides down
  - Focus moves to error field
  - Icon changes to error state

Network Error:
  - Show error toast
  - Offline indicator appears
  - Action button to retry
  - Retry button shows spinner

Permission Denied:
  - Modal with clear message
  - Icon illustrating issue
  - Suggested action
  - Contact support link
```

#### 3.25.11 Empty State Transitions

```
No Data → Data Appears:
  - Empty state fades out (300ms)
  - First items fade in with stagger
  - Continue loading if more items
  - Remove empty state from DOM

Data → No Data (after filter):
  - Current items fade out (200ms)
  - Empty state fades in (300ms)
  - Icon and text appear with delay
  - Suggest action button bounces in
```

#### 3.25.12 Context Menu

```
Right Click:
  - Menu appears at cursor (instant)
  - Items fade in with stagger (50ms)
  - Subtle scale from cursor point
  - Shadow expands outward

Hover Menu Items:
  - Background color transition (100ms)
  - Submenu slides from right (if any)
  - Icon color changes
  - Cursor changes to pointer

Select Item:
  - Background flash
  - Menu fades out (150ms)
  - Execute action
  - Show feedback (toast/modal)
```

---

## 4. Functional Requirements

### 2.1 User Management

#### 2.1.1 Authentication & Authorization
- **FR-AUTH-001**: Users must be able to register with email and password
- **FR-AUTH-002**: Users must be able to log in with email/password
- **FR-AUTH-003**: Users must be able to log in with OAuth (Google, Facebook)
- **FR-AUTH-004**: System must support password reset via email
- **FR-AUTH-005**: System must support email verification
- **FR-AUTH-006**: Users must be able to enable/disable two-factor authentication (2FA)
- **FR-AUTH-007**: System must implement JWT-based authentication with refresh tokens
- **FR-AUTH-008**: Session timeout after 7 days of inactivity

#### 2.1.2 User Profile
- **FR-USER-001**: Users can view and edit their profile (name, email, avatar)
- **FR-USER-002**: Users can set their default currency
- **FR-USER-003**: Users can set their timezone
- **FR-USER-004**: Users can set their preferred language
- **FR-USER-005**: Users can delete their account (with confirmation)
- **FR-USER-006**: System must anonymize or delete all user data upon account deletion

### 2.2 Account Management

#### 2.2.1 Account CRUD
- **FR-ACC-001**: Users can create multiple financial accounts (bank, cash, credit card, loan, investment)
- **FR-ACC-002**: Each account must have: name, type, currency, initial balance, description, icon/color
- **FR-ACC-003**: Users can update account details
- **FR-ACC-004**: Users can archive accounts (soft delete)
- **FR-ACC-005**: Users can set account visibility (active/hidden)
- **FR-ACC-006**: System must track account balance in real-time based on transactions

#### 2.2.2 Account Types
- **FR-ACC-007**: Support for the following account types:
  - Checking Account
  - Savings Account
  - Credit Card
  - Cash
  - Loan
  - Investment
  - Crypto Wallet
  - Other

#### 2.2.3 Multi-Currency
- **FR-ACC-008**: Each account can have a different currency
- **FR-ACC-009**: System must support 150+ major world currencies
- **FR-ACC-010**: System must allow manual exchange rate input
- **FR-ACC-011**: Dashboard must show total balance converted to user's default currency

### 2.3 Transaction Management

#### 2.3.1 Transaction Creation
- **FR-TXN-001**: Users can create income transactions
- **FR-TXN-002**: Users can create expense transactions
- **FR-TXN-003**: Users can create transfer transactions between accounts
- **FR-TXN-004**: Each transaction must have: amount, date, category, account, description, notes
- **FR-TXN-005**: Users can attach receipt images to transactions (max 5 per transaction)
- **FR-TXN-006**: Users can set transaction tags for better organization
- **FR-TXN-007**: Users can mark transactions as recurring

#### 2.3.2 Transaction Management
- **FR-TXN-008**: Users can edit transactions
- **FR-TXN-009**: Users can delete transactions
- **FR-TXN-010**: Users can duplicate transactions
- **FR-TXN-011**: Users can search transactions by date range, category, amount, description
- **FR-TXN-012**: Users can filter transactions by account, category, tag, date
- **FR-TXN-013**: Users can sort transactions by date, amount, category
- **FR-TXN-014**: System must paginate transaction lists (50 per page)

#### 2.3.3 Recurring Transactions
- **FR-TXN-015**: Users can create recurring transactions (daily, weekly, monthly, yearly, custom)
- **FR-TXN-016**: System must automatically create transactions based on recurring rules
- **FR-TXN-017**: Users can pause/resume recurring transactions
- **FR-TXN-018**: Users can edit recurring transaction templates
- **FR-TXN-019**: System must notify users before creating recurring transactions (1 day before)

#### 2.3.4 Transaction Import/Export
- **FR-TXN-020**: Users can import transactions via CSV file
- **FR-TXN-021**: Users can export transactions to CSV
- **FR-TXN-022**: CSV import must support column mapping
- **FR-TXN-023**: System must validate CSV data before import
- **FR-TXN-024**: System must detect and skip duplicate transactions during import

### 2.4 Category Management

#### 2.4.1 Category CRUD
- **FR-CAT-001**: System must provide default categories (Food, Transport, Entertainment, etc.)
- **FR-CAT-002**: Users can create custom categories
- **FR-CAT-003**: Users can create subcategories (2 levels deep)
- **FR-CAT-004**: Each category must have: name, icon, color, type (income/expense)
- **FR-CAT-005**: Users can edit category details
- **FR-CAT-006**: Users can archive categories (cannot be deleted if used in transactions)
- **FR-CAT-007**: Users can reorder categories

#### 2.4.2 Category Analytics
- **FR-CAT-008**: System must track spending per category
- **FR-CAT-009**: System must show category trends over time

### 2.5 Budget Management

#### 2.5.1 Budget Creation
- **FR-BUD-001**: Users can create budgets for specific categories
- **FR-BUD-002**: Users can create budgets for multiple categories combined
- **FR-BUD-003**: Each budget must have: name, amount, period (weekly/monthly/yearly/custom), categories
- **FR-BUD-004**: Users can set budget start and end dates
- **FR-BUD-005**: Users can create recurring budgets

#### 2.5.2 Budget Monitoring
- **FR-BUD-006**: System must show budget progress in real-time
- **FR-BUD-007**: System must show percentage spent vs. budget limit
- **FR-BUD-008**: System must alert users when reaching 80% of budget
- **FR-BUD-009**: System must alert users when exceeding budget
- **FR-BUD-010**: Dashboard must display all active budgets
- **FR-BUD-011**: System must show daily spending rate based on budget

#### 2.5.3 Budget Management
- **FR-BUD-012**: Users can edit budgets
- **FR-BUD-013**: Users can pause/archive budgets
- **FR-BUD-014**: Users can view budget history and performance

### 2.6 Financial Goals

#### 2.6.1 Goal Creation
- **FR-GOAL-001**: Users can create savings goals
- **FR-GOAL-002**: Each goal must have: name, target amount, deadline, description, icon
- **FR-GOAL-003**: Users can link goals to specific accounts
- **FR-GOAL-004**: Users can set goal priority (low/medium/high)

#### 2.6.2 Goal Tracking
- **FR-GOAL-005**: System must show goal progress percentage
- **FR-GOAL-006**: System must calculate required monthly savings to meet goal
- **FR-GOAL-007**: Users can manually update goal progress
- **FR-GOAL-008**: System must send reminders for goal milestones
- **FR-GOAL-009**: System must show projected completion date

#### 2.6.3 Goal Management
- **FR-GOAL-010**: Users can edit goals
- **FR-GOAL-011**: Users can mark goals as completed
- **FR-GOAL-012**: Users can archive/delete goals

### 2.7 Reports & Analytics

#### 2.7.1 Financial Reports
- **FR-REP-001**: System must generate monthly income vs. expense report
- **FR-REP-002**: System must show spending by category (pie chart)
- **FR-REP-003**: System must show spending trends over time (line chart)
- **FR-REP-004**: System must show account balance trends
- **FR-REP-005**: System must calculate and display net worth
- **FR-REP-006**: System must show cash flow analysis
- **FR-REP-007**: Users can generate custom date range reports
- **FR-REP-008**: Users can compare periods (month-over-month, year-over-year)

#### 2.7.2 Insights
- **FR-REP-009**: System must identify top spending categories
- **FR-REP-010**: System must detect unusual spending patterns
- **FR-REP-011**: System must provide spending predictions for next month
- **FR-REP-012**: System must calculate average daily/weekly/monthly spending

#### 2.7.3 Export
- **FR-REP-013**: Users can export reports to PDF
- **FR-REP-014**: Users can export reports to CSV
- **FR-REP-015**: Users can share reports via email

### 2.8 Dashboard

#### 2.8.1 Dashboard Components
- **FR-DASH-001**: Dashboard must show total balance across all accounts
- **FR-DASH-002**: Dashboard must show current month income vs. expense
- **FR-DASH-003**: Dashboard must display recent transactions (last 10)
- **FR-DASH-004**: Dashboard must show active budgets progress
- **FR-DASH-005**: Dashboard must show upcoming recurring transactions
- **FR-DASH-006**: Dashboard must display financial goals progress
- **FR-DASH-007**: Dashboard must show spending by category (current month)
- **FR-DASH-008**: Dashboard must be customizable (users can show/hide widgets)

#### 2.8.2 Dashboard Filters
- **FR-DASH-009**: Users can filter dashboard by date range
- **FR-DASH-010**: Users can filter dashboard by specific accounts

### 2.9 Sharing & Collaboration

#### 2.9.1 Account Sharing
- **FR-SHARE-001**: Users can share specific accounts with other users
- **FR-SHARE-002**: Share permissions: view-only, can-add-transactions, full-access
- **FR-SHARE-003**: Users can revoke shared access
- **FR-SHARE-004**: Shared users can see transactions and budgets for shared accounts
- **FR-SHARE-005**: System must send email invitation for account sharing
- **FR-SHARE-006**: Users can accept/reject sharing invitations

#### 2.9.2 Household/Group Management
- **FR-SHARE-007**: Users can create household groups
- **FR-SHARE-008**: Household members can share multiple accounts
- **FR-SHARE-009**: Household admins can add/remove members
- **FR-SHARE-010**: Each household can have shared budgets

### 2.10 Notifications

#### 2.10.1 System Notifications
- **FR-NOTIF-001**: System must send email notifications for budget alerts
- **FR-NOTIF-002**: System must send notifications for upcoming recurring transactions
- **FR-NOTIF-003**: System must send goal milestone notifications
- **FR-NOTIF-004**: System must send weekly/monthly financial summary
- **FR-NOTIF-005**: Users can configure notification preferences
- **FR-NOTIF-006**: In-app notification center for all notifications

#### 2.10.2 Notification Types
- Budget exceeded
- Budget 80% reached
- Recurring transaction created
- Goal milestone reached
- Sharing invitation
- Weekly summary
- Monthly summary

### 2.11 Settings & Preferences

#### 2.11.1 Application Settings
- **FR-SET-001**: Users can set default account for quick transactions
- **FR-SET-002**: Users can set default transaction view (list/grouped)
- **FR-SET-003**: Users can enable/disable animations
- **FR-SET-004**: Users can set theme (light/dark/system)
- **FR-SET-005**: Users can set date format preference
- **FR-SET-006**: Users can set number format preference

#### 2.11.2 Privacy Settings
- **FR-SET-007**: Users can enable/disable analytics
- **FR-SET-008**: Users can download all their data (GDPR compliance)
- **FR-SET-009**: Users can view privacy policy and terms of service

---

## 3. Non-Functional Requirements

### 3.1 Performance
- **NFR-PERF-001**: Dashboard must load in less than 2 seconds
- **NFR-PERF-002**: Transaction list must load 50 items in less than 500ms
- **NFR-PERF-003**: API responses must be under 200ms for simple queries
- **NFR-PERF-004**: System must support 10,000 concurrent users
- **NFR-PERF-005**: Database queries must use proper indexing
- **NFR-PERF-006**: System must implement caching for frequently accessed data

### 3.2 Security
- **NFR-SEC-001**: All passwords must be hashed using bcrypt (cost factor 12)
- **NFR-SEC-002**: All API endpoints must use HTTPS only
- **NFR-SEC-003**: System must implement rate limiting (100 requests/minute per user)
- **NFR-SEC-004**: System must sanitize all user inputs to prevent SQL injection
- **NFR-SEC-005**: System must implement CSRF protection
- **NFR-SEC-006**: System must implement XSS protection
- **NFR-SEC-007**: Sensitive data must be encrypted at rest
- **NFR-SEC-008**: System must log all authentication attempts
- **NFR-SEC-009**: System must implement account lockout after 5 failed login attempts

### 3.3 Scalability
- **NFR-SCALE-001**: System must be horizontally scalable
- **NFR-SCALE-002**: Database must support read replicas
- **NFR-SCALE-003**: System must use connection pooling
- **NFR-SCALE-004**: File uploads must use cloud storage (S3-compatible)
- **NFR-SCALE-005**: System must support database partitioning for large datasets

### 3.4 Reliability
- **NFR-REL-001**: System must have 99.9% uptime
- **NFR-REL-002**: System must perform automatic database backups daily
- **NFR-REL-003**: System must support disaster recovery
- **NFR-REL-004**: Transaction operations must be ACID-compliant
- **NFR-REL-005**: System must implement retry logic for failed operations

### 3.5 Usability
- **NFR-USE-001**: Application must be responsive (mobile, tablet, desktop)
- **NFR-USE-002**: Application must be accessible (WCAG 2.1 AA compliance)
- **NFR-USE-003**: Application must support keyboard navigation
- **NFR-USE-004**: Error messages must be user-friendly and actionable
- **NFR-USE-005**: Application must support internationalization (i18n)

### 3.6 Maintainability
- **NFR-MAINT-001**: Code must follow consistent style guides (ESLint, Prettier)
- **NFR-MAINT-002**: API must be versioned (v1, v2, etc.)
- **NFR-MAINT-003**: All functions must have TypeScript types
- **NFR-MAINT-004**: Critical functions must have JSDoc comments
- **NFR-MAINT-005**: Database migrations must be reversible

### 3.7 Compatibility
- **NFR-COMP-001**: Frontend must support modern browsers (Chrome, Firefox, Safari, Edge - last 2 versions)
- **NFR-COMP-002**: API must follow REST standards
- **NFR-COMP-003**: API must return consistent error formats
- **NFR-COMP-004**: API must support JSON content type

---

## 4. Database Design

### 4.1 Entity Relationship Diagram Overview

```
Users → Accounts → Transactions
Users → Categories
Users → Budgets → BudgetCategories
Users → Goals
Users → RecurringTransactions
Users → Notifications
Accounts → AccountShares
Users → Households → HouseholdMembers
Transactions → TransactionAttachments
Transactions → TransactionTags → Tags
```

### 4.2 Database Tables

#### 4.2.1 users
```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255), -- NULL for OAuth-only users
    first_name VARCHAR(100),
    last_name VARCHAR(100),
    avatar_url TEXT,
    default_currency VARCHAR(3) DEFAULT 'USD',
    timezone VARCHAR(50) DEFAULT 'UTC',
    locale VARCHAR(10) DEFAULT 'en',
    email_verified BOOLEAN DEFAULT FALSE,
    two_factor_enabled BOOLEAN DEFAULT FALSE,
    two_factor_secret VARCHAR(255),
    oauth_provider VARCHAR(50), -- 'google', 'facebook', NULL
    oauth_id VARCHAR(255),
    last_login_at TIMESTAMP WITH TIME ZONE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP WITH TIME ZONE,
    
    CONSTRAINT users_email_check CHECK (email ~* '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$')
);

CREATE INDEX idx_users_email ON users(email) WHERE deleted_at IS NULL;
CREATE INDEX idx_users_oauth ON users(oauth_provider, oauth_id) WHERE deleted_at IS NULL;
```

#### 4.2.2 accounts
```sql
CREATE TYPE account_type AS ENUM (
    'checking', 'savings', 'credit_card', 'cash', 
    'loan', 'investment', 'crypto', 'other'
);

CREATE TABLE accounts (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    name VARCHAR(100) NOT NULL,
    type account_type NOT NULL,
    currency VARCHAR(3) NOT NULL DEFAULT 'USD',
    initial_balance DECIMAL(15, 2) NOT NULL DEFAULT 0,
    current_balance DECIMAL(15, 2) NOT NULL DEFAULT 0,
    description TEXT,
    icon VARCHAR(50),
    color VARCHAR(7), -- Hex color code
    is_active BOOLEAN DEFAULT TRUE,
    display_order INTEGER DEFAULT 0,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    archived_at TIMESTAMP WITH TIME ZONE,
    
    CONSTRAINT accounts_current_balance_check CHECK (
        (type NOT IN ('credit_card', 'loan')) OR (current_balance <= 0)
    )
);

CREATE INDEX idx_accounts_user_id ON accounts(user_id) WHERE archived_at IS NULL;
CREATE INDEX idx_accounts_type ON accounts(type);
```

#### 4.2.3 categories
```sql
CREATE TYPE category_type AS ENUM ('income', 'expense');

CREATE TABLE categories (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    parent_id UUID REFERENCES categories(id) ON DELETE CASCADE,
    name VARCHAR(100) NOT NULL,
    type category_type NOT NULL,
    icon VARCHAR(50),
    color VARCHAR(7),
    is_system BOOLEAN DEFAULT FALSE, -- TRUE for default categories
    display_order INTEGER DEFAULT 0,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    archived_at TIMESTAMP WITH TIME ZONE,
    
    CONSTRAINT categories_unique_user_name UNIQUE (user_id, name, type) 
        WHERE archived_at IS NULL AND parent_id IS NULL,
    CONSTRAINT categories_parent_check CHECK (
        parent_id IS NULL OR 
        (SELECT parent_id FROM categories WHERE id = categories.parent_id) IS NULL
    )
);

CREATE INDEX idx_categories_user_id ON categories(user_id) WHERE archived_at IS NULL;
CREATE INDEX idx_categories_parent_id ON categories(parent_id);
CREATE INDEX idx_categories_type ON categories(type);
```

#### 4.2.4 transactions
```sql
CREATE TYPE transaction_type AS ENUM ('income', 'expense', 'transfer');

CREATE TABLE transactions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    account_id UUID NOT NULL REFERENCES accounts(id) ON DELETE RESTRICT,
    category_id UUID REFERENCES categories(id) ON DELETE SET NULL,
    to_account_id UUID REFERENCES accounts(id) ON DELETE RESTRICT, -- For transfers
    type transaction_type NOT NULL,
    amount DECIMAL(15, 2) NOT NULL,
    currency VARCHAR(3) NOT NULL,
    exchange_rate DECIMAL(15, 6) DEFAULT 1.0, -- For multi-currency
    description VARCHAR(255),
    notes TEXT,
    transaction_date DATE NOT NULL,
    recurring_transaction_id UUID REFERENCES recurring_transactions(id) ON DELETE SET NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP WITH TIME ZONE,
    
    CONSTRAINT transactions_amount_positive CHECK (amount > 0),
    CONSTRAINT transactions_transfer_check CHECK (
        (type != 'transfer') OR (to_account_id IS NOT NULL AND to_account_id != account_id)
    ),
    CONSTRAINT transactions_category_check CHECK (
        (type = 'transfer') OR (category_id IS NOT NULL)
    )
);

CREATE INDEX idx_transactions_user_id ON transactions(user_id) WHERE deleted_at IS NULL;
CREATE INDEX idx_transactions_account_id ON transactions(account_id) WHERE deleted_at IS NULL;
CREATE INDEX idx_transactions_category_id ON transactions(category_id);
CREATE INDEX idx_transactions_date ON transactions(transaction_date DESC);
CREATE INDEX idx_transactions_type ON transactions(type);
CREATE INDEX idx_transactions_recurring ON transactions(recurring_transaction_id);
```

#### 4.2.5 transaction_attachments
```sql
CREATE TABLE transaction_attachments (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    transaction_id UUID NOT NULL REFERENCES transactions(id) ON DELETE CASCADE,
    file_name VARCHAR(255) NOT NULL,
    file_url TEXT NOT NULL,
    file_size INTEGER NOT NULL, -- In bytes
    mime_type VARCHAR(100),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_transaction_attachments_transaction_id ON transaction_attachments(transaction_id);
```

#### 4.2.6 tags
```sql
CREATE TABLE tags (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    name VARCHAR(50) NOT NULL,
    color VARCHAR(7),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    
    CONSTRAINT tags_unique_user_name UNIQUE (user_id, name)
);

CREATE INDEX idx_tags_user_id ON tags(user_id);
```

#### 4.2.7 transaction_tags
```sql
CREATE TABLE transaction_tags (
    transaction_id UUID NOT NULL REFERENCES transactions(id) ON DELETE CASCADE,
    tag_id UUID NOT NULL REFERENCES tags(id) ON DELETE CASCADE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    
    PRIMARY KEY (transaction_id, tag_id)
);

CREATE INDEX idx_transaction_tags_tag_id ON transaction_tags(tag_id);
```

#### 4.2.8 budgets
```sql
CREATE TYPE budget_period AS ENUM ('weekly', 'monthly', 'yearly', 'custom');

CREATE TABLE budgets (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    name VARCHAR(100) NOT NULL,
    amount DECIMAL(15, 2) NOT NULL,
    period budget_period NOT NULL,
    start_date DATE NOT NULL,
    end_date DATE,
    currency VARCHAR(3) NOT NULL,
    is_recurring BOOLEAN DEFAULT FALSE,
    alert_threshold DECIMAL(5, 2) DEFAULT 80.00, -- Alert at 80%
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    archived_at TIMESTAMP WITH TIME ZONE,
    
    CONSTRAINT budgets_amount_positive CHECK (amount > 0),
    CONSTRAINT budgets_dates_check CHECK (end_date IS NULL OR end_date >= start_date),
    CONSTRAINT budgets_threshold_check CHECK (alert_threshold > 0 AND alert_threshold <= 100)
);

CREATE INDEX idx_budgets_user_id ON budgets(user_id) WHERE archived_at IS NULL;
CREATE INDEX idx_budgets_dates ON budgets(start_date, end_date);
```

#### 4.2.9 budget_categories
```sql
CREATE TABLE budget_categories (
    budget_id UUID NOT NULL REFERENCES budgets(id) ON DELETE CASCADE,
    category_id UUID NOT NULL REFERENCES categories(id) ON DELETE CASCADE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    
    PRIMARY KEY (budget_id, category_id)
);

CREATE INDEX idx_budget_categories_category_id ON budget_categories(category_id);
```

#### 4.2.10 goals
```sql
CREATE TYPE goal_priority AS ENUM ('low', 'medium', 'high');

CREATE TABLE goals (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    account_id UUID REFERENCES accounts(id) ON DELETE SET NULL,
    name VARCHAR(100) NOT NULL,
    target_amount DECIMAL(15, 2) NOT NULL,
    current_amount DECIMAL(15, 2) DEFAULT 0,
    currency VARCHAR(3) NOT NULL,
    deadline DATE,
    priority goal_priority DEFAULT 'medium',
    description TEXT,
    icon VARCHAR(50),
    is_completed BOOLEAN DEFAULT FALSE,
    completed_at TIMESTAMP WITH TIME ZONE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    archived_at TIMESTAMP WITH TIME ZONE,
    
    CONSTRAINT goals_target_positive CHECK (target_amount > 0),
    CONSTRAINT goals_current_nonnegative CHECK (current_amount >= 0),
    CONSTRAINT goals_current_lte_target CHECK (current_amount <= target_amount)
);

CREATE INDEX idx_goals_user_id ON goals(user_id) WHERE archived_at IS NULL;
CREATE INDEX idx_goals_account_id ON goals(account_id);
CREATE INDEX idx_goals_completed ON goals(is_completed);
```

#### 4.2.11 recurring_transactions
```sql
CREATE TYPE recurrence_frequency AS ENUM ('daily', 'weekly', 'biweekly', 'monthly', 'quarterly', 'yearly', 'custom');

CREATE TABLE recurring_transactions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    account_id UUID NOT NULL REFERENCES accounts(id) ON DELETE CASCADE,
    category_id UUID REFERENCES categories(id) ON DELETE SET NULL,
    to_account_id UUID REFERENCES accounts(id) ON DELETE CASCADE,
    type transaction_type NOT NULL,
    amount DECIMAL(15, 2) NOT NULL,
    currency VARCHAR(3) NOT NULL,
    description VARCHAR(255),
    notes TEXT,
    frequency recurrence_frequency NOT NULL,
    interval INTEGER DEFAULT 1, -- Every X frequency units
    start_date DATE NOT NULL,
    end_date DATE,
    next_occurrence DATE NOT NULL,
    is_active BOOLEAN DEFAULT TRUE,
    notify_before_days INTEGER DEFAULT 1,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    
    CONSTRAINT recurring_amount_positive CHECK (amount > 0),
    CONSTRAINT recurring_interval_positive CHECK (interval > 0),
    CONSTRAINT recurring_dates_check CHECK (end_date IS NULL OR end_date >= start_date)
);

CREATE INDEX idx_recurring_transactions_user_id ON recurring_transactions(user_id);
CREATE INDEX idx_recurring_transactions_next_occurrence ON recurring_transactions(next_occurrence) 
    WHERE is_active = TRUE;
```

#### 4.2.12 account_shares
```sql
CREATE TYPE share_permission AS ENUM ('view', 'add_transactions', 'full_access');

CREATE TABLE account_shares (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    account_id UUID NOT NULL REFERENCES accounts(id) ON DELETE CASCADE,
    owner_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    shared_with_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    permission share_permission NOT NULL DEFAULT 'view',
    is_accepted BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    accepted_at TIMESTAMP WITH TIME ZONE,
    revoked_at TIMESTAMP WITH TIME ZONE,
    
    CONSTRAINT account_shares_unique UNIQUE (account_id, shared_with_id) WHERE revoked_at IS NULL,
    CONSTRAINT account_shares_not_self CHECK (owner_id != shared_with_id)
);

CREATE INDEX idx_account_shares_account_id ON account_shares(account_id) WHERE revoked_at IS NULL;
CREATE INDEX idx_account_shares_shared_with ON account_shares(shared_with_id) WHERE revoked_at IS NULL;
```

#### 4.2.13 households
```sql
CREATE TABLE households (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(100) NOT NULL,
    description TEXT,
    created_by UUID NOT NULL REFERENCES users(id) ON DELETE RESTRICT,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_households_created_by ON households(created_by);
```

#### 4.2.14 household_members
```sql
CREATE TYPE household_role AS ENUM ('admin', 'member');

CREATE TABLE household_members (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    household_id UUID NOT NULL REFERENCES households(id) ON DELETE CASCADE,
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    role household_role NOT NULL DEFAULT 'member',
    joined_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    left_at TIMESTAMP WITH TIME ZONE,
    
    CONSTRAINT household_members_unique UNIQUE (household_id, user_id) WHERE left_at IS NULL
);

CREATE INDEX idx_household_members_household_id ON household_members(household_id) WHERE left_at IS NULL;
CREATE INDEX idx_household_members_user_id ON household_members(user_id) WHERE left_at IS NULL;
```

#### 4.2.15 notifications
```sql
CREATE TYPE notification_type AS ENUM (
    'budget_exceeded', 'budget_threshold', 'recurring_created', 
    'goal_milestone', 'sharing_invitation', 'weekly_summary', 'monthly_summary'
);

CREATE TABLE notifications (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    type notification_type NOT NULL,
    title VARCHAR(255) NOT NULL,
    message TEXT NOT NULL,
    data JSONB, -- Additional notification data
    is_read BOOLEAN DEFAULT FALSE,
    is_emailed BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    read_at TIMESTAMP WITH TIME ZONE
);

CREATE INDEX idx_notifications_user_id ON notifications(user_id, created_at DESC);
CREATE INDEX idx_notifications_unread ON notifications(user_id) WHERE is_read = FALSE;
```

#### 4.2.16 user_settings
```sql
CREATE TABLE user_settings (
    user_id UUID PRIMARY KEY REFERENCES users(id) ON DELETE CASCADE,
    theme VARCHAR(20) DEFAULT 'system', -- 'light', 'dark', 'system'
    date_format VARCHAR(20) DEFAULT 'MM/DD/YYYY',
    number_format VARCHAR(20) DEFAULT 'en-US',
    default_account_id UUID REFERENCES accounts(id) ON DELETE SET NULL,
    default_transaction_view VARCHAR(20) DEFAULT 'list',
    enable_animations BOOLEAN DEFAULT TRUE,
    enable_notifications BOOLEAN DEFAULT TRUE,
    enable_email_notifications BOOLEAN DEFAULT TRUE,
    enable_budget_alerts BOOLEAN DEFAULT TRUE,
    enable_weekly_summary BOOLEAN DEFAULT TRUE,
    enable_monthly_summary BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);
```

#### 4.2.17 refresh_tokens
```sql
CREATE TABLE refresh_tokens (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    token VARCHAR(500) UNIQUE NOT NULL,
    expires_at TIMESTAMP WITH TIME ZONE NOT NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    revoked_at TIMESTAMP WITH TIME ZONE
);

CREATE INDEX idx_refresh_tokens_user_id ON refresh_tokens(user_id);
CREATE INDEX idx_refresh_tokens_token ON refresh_tokens(token) WHERE revoked_at IS NULL;
CREATE INDEX idx_refresh_tokens_expires ON refresh_tokens(expires_at) WHERE revoked_at IS NULL;
```

#### 4.2.18 audit_logs
```sql
CREATE TABLE audit_logs (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE SET NULL,
    action VARCHAR(50) NOT NULL, -- 'login', 'logout', 'create', 'update', 'delete'
    entity_type VARCHAR(50), -- 'transaction', 'account', etc.
    entity_id UUID,
    ip_address INET,
    user_agent TEXT,
    metadata JSONB,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_audit_logs_user_id ON audit_logs(user_id, created_at DESC);
CREATE INDEX idx_audit_logs_entity ON audit_logs(entity_type, entity_id);
CREATE INDEX idx_audit_logs_action ON audit_logs(action, created_at DESC);
```

### 4.3 Database Views

#### 4.3.1 vw_account_balances
```sql
CREATE OR REPLACE VIEW vw_account_balances AS
SELECT 
    a.id,
    a.user_id,
    a.name,
    a.type,
    a.currency,
    a.initial_balance,
    a.initial_balance + COALESCE(
        (SELECT SUM(
            CASE 
                WHEN t.type = 'income' THEN t.amount
                WHEN t.type = 'expense' THEN -t.amount
                WHEN t.type = 'transfer' AND t.account_id = a.id THEN -t.amount
                WHEN t.type = 'transfer' AND t.to_account_id = a.id THEN t.amount
            END
        )
        FROM transactions t
        WHERE (t.account_id = a.id OR t.to_account_id = a.id)
          AND t.deleted_at IS NULL
        ), 0
    ) as calculated_balance,
    a.current_balance
FROM accounts a
WHERE a.archived_at IS NULL;
```

#### 4.3.2 vw_budget_progress
```sql
CREATE OR REPLACE VIEW vw_budget_progress AS
SELECT 
    b.id,
    b.user_id,
    b.name,
    b.amount as budget_amount,
    b.period,
    b.start_date,
    b.end_date,
    b.currency,
    COALESCE(
        (SELECT SUM(t.amount)
         FROM transactions t
         JOIN budget_categories bc ON bc.budget_id = b.id
         WHERE t.category_id = bc.category_id
           AND t.type = 'expense'
           AND t.transaction_date >= b.start_date
           AND (b.end_date IS NULL OR t.transaction_date <= b.end_date)
           AND t.deleted_at IS NULL
        ), 0
    ) as spent_amount,
    ROUND(
        (COALESCE(
            (SELECT SUM(t.amount)
             FROM transactions t
             JOIN budget_categories bc ON bc.budget_id = b.id
             WHERE t.category_id = bc.category_id
               AND t.type = 'expense'
               AND t.transaction_date >= b.start_date
               AND (b.end_date IS NULL OR t.transaction_date <= b.end_date)
               AND t.deleted_at IS NULL
            ), 0
        ) / b.amount) * 100, 2
    ) as progress_percentage
FROM budgets b
WHERE b.archived_at IS NULL;
```

#### 4.3.3 vw_category_spending
```sql
CREATE OR REPLACE VIEW vw_category_spending AS
SELECT 
    c.id as category_id,
    c.user_id,
    c.name as category_name,
    c.type,
    DATE_TRUNC('month', t.transaction_date) as month,
    SUM(t.amount) as total_amount,
    COUNT(t.id) as transaction_count
FROM categories c
LEFT JOIN transactions t ON t.category_id = c.id AND t.deleted_at IS NULL
WHERE c.archived_at IS NULL
GROUP BY c.id, c.user_id, c.name, c.type, DATE_TRUNC('month', t.transaction_date);
```

### 4.4 Database Functions

#### 4.4.1 Update Account Balance Trigger
```sql
CREATE OR REPLACE FUNCTION update_account_balance()
RETURNS TRIGGER AS $$
BEGIN
    IF TG_OP = 'INSERT' THEN
        -- Update account balance for new transaction
        UPDATE accounts
        SET current_balance = current_balance + 
            CASE 
                WHEN NEW.type = 'income' THEN NEW.amount
                WHEN NEW.type = 'expense' THEN -NEW.amount
                WHEN NEW.type = 'transfer' THEN -NEW.amount
            END,
            updated_at = CURRENT_TIMESTAMP
        WHERE id = NEW.account_id;
        
        -- Update to_account for transfers
        IF NEW.type = 'transfer' AND NEW.to_account_id IS NOT NULL THEN
            UPDATE accounts
            SET current_balance = current_balance + NEW.amount,
                updated_at = CURRENT_TIMESTAMP
            WHERE id = NEW.to_account_id;
        END IF;
        
    ELSIF TG_OP = 'UPDATE' THEN
        -- Reverse old transaction
        UPDATE accounts
        SET current_balance = current_balance - 
            CASE 
                WHEN OLD.type = 'income' THEN OLD.amount
                WHEN OLD.type = 'expense' THEN -OLD.amount
                WHEN OLD.type = 'transfer' THEN -OLD.amount
            END,
            updated_at = CURRENT_TIMESTAMP
        WHERE id = OLD.account_id;
        
        IF OLD.type = 'transfer' AND OLD.to_account_id IS NOT NULL THEN
            UPDATE accounts
            SET current_balance = current_balance - OLD.amount,
                updated_at = CURRENT_TIMESTAMP
            WHERE id = OLD.to_account_id;
        END IF;
        
        -- Apply new transaction
        UPDATE accounts
        SET current_balance = current_balance + 
            CASE 
                WHEN NEW.type = 'income' THEN NEW.amount
                WHEN NEW.type = 'expense' THEN -NEW.amount
                WHEN NEW.type = 'transfer' THEN -NEW.amount
            END,
            updated_at = CURRENT_TIMESTAMP
        WHERE id = NEW.account_id;
        
        IF NEW.type = 'transfer' AND NEW.to_account_id IS NOT NULL THEN
            UPDATE accounts
            SET current_balance = current_balance + NEW.amount,
                updated_at = CURRENT_TIMESTAMP
            WHERE id = NEW.to_account_id;
        END IF;
        
    ELSIF TG_OP = 'DELETE' THEN
        -- Reverse transaction
        UPDATE accounts
        SET current_balance = current_balance - 
            CASE 
                WHEN OLD.type = 'income' THEN OLD.amount
                WHEN OLD.type = 'expense' THEN -OLD.amount
                WHEN OLD.type = 'transfer' THEN -OLD.amount
            END,
            updated_at = CURRENT_TIMESTAMP
        WHERE id = OLD.account_id;
        
        IF OLD.type = 'transfer' AND OLD.to_account_id IS NOT NULL THEN
            UPDATE accounts
            SET current_balance = current_balance - OLD.amount,
                updated_at = CURRENT_TIMESTAMP
            WHERE id = OLD.to_account_id;
        END IF;
    END IF;
    
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trigger_update_account_balance
AFTER INSERT OR UPDATE OR DELETE ON transactions
FOR EACH ROW
EXECUTE FUNCTION update_account_balance();
```

#### 4.4.2 Updated Timestamp Trigger
```sql
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = CURRENT_TIMESTAMP;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- Apply to all tables with updated_at
CREATE TRIGGER update_users_updated_at BEFORE UPDATE ON users
FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_accounts_updated_at BEFORE UPDATE ON accounts
FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

-- Repeat for other tables...
```

---

## 5. API Design

### 5.1 API Architecture

#### 5.1.1 Next.js API Routes Structure
```
app/
├── api/
│   ├── auth/
│   │   ├── register/route.ts
│   │   ├── login/route.ts
│   │   ├── logout/route.ts
│   │   ├── refresh/route.ts
│   │   └── oauth/
│   │       └── google/route.ts
│   ├── users/
│   │   ├── me/route.ts
│   │   └── me/settings/route.ts
│   ├── accounts/
│   │   ├── route.ts
│   │   └── [id]/route.ts
│   ├── transactions/
│   │   ├── route.ts
│   │   ├── [id]/route.ts
│   │   ├── import/route.ts
│   │   └── export/route.ts
│   ├── categories/
│   │   ├── route.ts
│   │   └── [id]/route.ts
│   ├── budgets/
│   │   ├── route.ts
│   │   └── [id]/route.ts
│   ├── goals/
│   │   ├── route.ts
│   │   └── [id]/route.ts
│   ├── dashboard/route.ts
│   ├── analytics/
│   │   ├── income-expense/route.ts
│   │   ├── spending-trends/route.ts
│   │   └── net-worth/route.ts
│   └── notifications/
│       ├── route.ts
│       └── [id]/route.ts
```

#### 5.1.2 Base URL
```
Production: https://walletflow.com/api
Development: http://localhost:3000/api
```

#### 5.1.3 Authentication
- All authenticated endpoints check for session using NextAuth.js or custom JWT
- Session stored in HTTP-only cookies
- Access tokens expire after 15 minutes
- Refresh tokens expire after 7 days

#### 5.1.4 Database Access
- **Prisma ORM**: Type-safe database queries
- Connection pooling handled by Prisma
- Migrations managed by Prisma Migrate
- Database client instantiated once and reused

#### 5.1.5 Next.js API Route Example

```typescript
// app/api/transactions/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { getServerSession } from 'next-auth';
import { authOptions } from '@/lib/auth';
import { prisma } from '@/lib/prisma';
import { z } from 'zod';

// GET /api/transactions - List all transactions
export async function GET(request: NextRequest) {
  try {
    // Check authentication
    const session = await getServerSession(authOptions);
    if (!session?.user?.id) {
      return NextResponse.json(
        { success: false, error: { code: 'AUTH_001', message: 'Unauthorized' } },
        { status: 401 }
      );
    }

    // Get query parameters
    const { searchParams } = new URL(request.url);
    const page = parseInt(searchParams.get('page') || '1');
    const limit = parseInt(searchParams.get('limit') || '50');
    const accountId = searchParams.get('account_id');
    
    // Build query
    const where = {
      userId: session.user.id,
      deletedAt: null,
      ...(accountId && { accountId }),
    };

    // Fetch transactions with pagination
    const [transactions, total] = await Promise.all([
      prisma.transaction.findMany({
        where,
        include: {
          account: true,
          category: true,
          tags: { include: { tag: true } },
        },
        orderBy: { transactionDate: 'desc' },
        skip: (page - 1) * limit,
        take: limit,
      }),
      prisma.transaction.count({ where }),
    ]);

    return NextResponse.json({
      success: true,
      data: transactions,
      meta: {
        page,
        limit,
        total,
        totalPages: Math.ceil(total / limit),
      },
    });
  } catch (error) {
    console.error('Error fetching transactions:', error);
    return NextResponse.json(
      { success: false, error: { code: 'SYS_001', message: 'Internal server error' } },
      { status: 500 }
    );
  }
}

// POST /api/transactions - Create new transaction
export async function POST(request: NextRequest) {
  try {
    const session = await getServerSession(authOptions);
    if (!session?.user?.id) {
      return NextResponse.json(
        { success: false, error: { code: 'AUTH_001', message: 'Unauthorized' } },
        { status: 401 }
      );
    }

    // Validate request body
    const transactionSchema = z.object({
      accountId: z.string().uuid(),
      categoryId: z.string().uuid(),
      type: z.enum(['income', 'expense', 'transfer']),
      amount: z.number().positive(),
      currency: z.string().length(3),
      description: z.string().optional(),
      transactionDate: z.string().datetime(),
      toAccountId: z.string().uuid().optional(),
    });

    const body = await request.json();
    const validated = transactionSchema.parse(body);

    // Create transaction in database
    const transaction = await prisma.transaction.create({
      data: {
        ...validated,
        userId: session.user.id,
      },
      include: {
        account: true,
        category: true,
      },
    });

    // Account balance is updated via database trigger

    return NextResponse.json(
      { success: true, data: transaction },
      { status: 201 }
    );
  } catch (error) {
    if (error instanceof z.ZodError) {
      return NextResponse.json(
        {
          success: false,
          error: {
            code: 'VALID_001',
            message: 'Validation error',
            details: error.errors,
          },
        },
        { status: 400 }
      );
    }

    console.error('Error creating transaction:', error);
    return NextResponse.json(
      { success: false, error: { code: 'SYS_001', message: 'Internal server error' } },
      { status: 500 }
    );
  }
}
```

```typescript
// app/api/transactions/[id]/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { getServerSession } from 'next-auth';
import { authOptions } from '@/lib/auth';
import { prisma } from '@/lib/prisma';

// GET /api/transactions/[id] - Get transaction by ID
export async function GET(
  request: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    const session = await getServerSession(authOptions);
    if (!session?.user?.id) {
      return NextResponse.json(
        { success: false, error: { code: 'AUTH_001', message: 'Unauthorized' } },
        { status: 401 }
      );
    }

    const transaction = await prisma.transaction.findFirst({
      where: {
        id: params.id,
        userId: session.user.id,
        deletedAt: null,
      },
      include: {
        account: true,
        category: true,
        tags: { include: { tag: true } },
        attachments: true,
      },
    });

    if (!transaction) {
      return NextResponse.json(
        { success: false, error: { code: 'RES_001', message: 'Transaction not found' } },
        { status: 404 }
      );
    }

    return NextResponse.json({ success: true, data: transaction });
  } catch (error) {
    console.error('Error fetching transaction:', error);
    return NextResponse.json(
      { success: false, error: { code: 'SYS_001', message: 'Internal server error' } },
      { status: 500 }
    );
  }
}

// PATCH /api/transactions/[id] - Update transaction
export async function PATCH(
  request: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    const session = await getServerSession(authOptions);
    if (!session?.user?.id) {
      return NextResponse.json(
        { success: false, error: { code: 'AUTH_001', message: 'Unauthorized' } },
        { status: 401 }
      );
    }

    // Verify ownership
    const existing = await prisma.transaction.findFirst({
      where: { id: params.id, userId: session.user.id, deletedAt: null },
    });

    if (!existing) {
      return NextResponse.json(
        { success: false, error: { code: 'RES_001', message: 'Transaction not found' } },
        { status: 404 }
      );
    }

    const body = await request.json();
    
    const transaction = await prisma.transaction.update({
      where: { id: params.id },
      data: {
        ...body,
        updatedAt: new Date(),
      },
      include: {
        account: true,
        category: true,
      },
    });

    return NextResponse.json({ success: true, data: transaction });
  } catch (error) {
    console.error('Error updating transaction:', error);
    return NextResponse.json(
      { success: false, error: { code: 'SYS_001', message: 'Internal server error' } },
      { status: 500 }
    );
  }
}

// DELETE /api/transactions/[id] - Delete transaction (soft delete)
export async function DELETE(
  request: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    const session = await getServerSession(authOptions);
    if (!session?.user?.id) {
      return NextResponse.json(
        { success: false, error: { code: 'AUTH_001', message: 'Unauthorized' } },
        { status: 401 }
      );
    }

    // Verify ownership
    const existing = await prisma.transaction.findFirst({
      where: { id: params.id, userId: session.user.id, deletedAt: null },
    });

    if (!existing) {
      return NextResponse.json(
        { success: false, error: { code: 'RES_001', message: 'Transaction not found' } },
        { status: 404 }
      );
    }

    // Soft delete
    await prisma.transaction.update({
      where: { id: params.id },
      data: { deletedAt: new Date() },
    });

    return NextResponse.json({
      success: true,
      message: 'Transaction deleted successfully',
    });
  } catch (error) {
    console.error('Error deleting transaction:', error);
    return NextResponse.json(
      { success: false, error: { code: 'SYS_001', message: 'Internal server error' } },
      { status: 500 }
    );
  }
}
```

#### 5.1.6 Response Format
```json
{
  "success": true,
  "data": {},
  "message": "Operation successful",
  "meta": {
    "page": 1,
    "limit": 50,
    "total": 100
  }
}
```

#### 5.1.4 Error Format
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input data",
    "details": [
      {
        "field": "email",
        "message": "Invalid email format"
      }
    ]
  }
}
```

### 5.2 API Endpoints

#### 5.2.1 Authentication Endpoints

##### POST /auth/register
Register a new user
```json
Request:
{
  "email": "user@example.com",
  "password": "SecurePassword123!",
  "first_name": "John",
  "last_name": "Doe"
}

Response: 201 Created
{
  "success": true,
  "data": {
    "user": {
      "id": "uuid",
      "email": "user@example.com",
      "first_name": "John",
      "last_name": "Doe"
    },
    "access_token": "jwt_token",
    "refresh_token": "refresh_token"
  }
}
```

##### POST /auth/login
User login
```json
Request:
{
  "email": "user@example.com",
  "password": "SecurePassword123!"
}

Response: 200 OK
{
  "success": true,
  "data": {
    "user": {
      "id": "uuid",
      "email": "user@example.com",
      "first_name": "John",
      "last_name": "Doe"
    },
    "access_token": "jwt_token",
    "refresh_token": "refresh_token"
  }
}
```

##### POST /auth/oauth/google
OAuth login with Google
```json
Request:
{
  "token": "google_oauth_token"
}

Response: 200 OK
{
  "success": true,
  "data": {
    "user": { },
    "access_token": "jwt_token",
    "refresh_token": "refresh_token"
  }
}
```

##### POST /auth/refresh
Refresh access token
```json
Request:
{
  "refresh_token": "refresh_token"
}

Response: 200 OK
{
  "success": true,
  "data": {
    "access_token": "new_jwt_token",
    "refresh_token": "new_refresh_token"
  }
}
```

##### POST /auth/logout
Logout user (revoke refresh token)
```json
Request:
{
  "refresh_token": "refresh_token"
}

Response: 200 OK
{
  "success": true,
  "message": "Logged out successfully"
}
```

##### POST /auth/forgot-password
Request password reset
```json
Request:
{
  "email": "user@example.com"
}

Response: 200 OK
{
  "success": true,
  "message": "Password reset email sent"
}
```

##### POST /auth/reset-password
Reset password with token
```json
Request:
{
  "token": "reset_token",
  "password": "NewPassword123!"
}

Response: 200 OK
{
  "success": true,
  "message": "Password reset successful"
}
```

#### 5.2.2 User Endpoints

##### GET /users/me
Get current user profile
```json
Response: 200 OK
{
  "success": true,
  "data": {
    "id": "uuid",
    "email": "user@example.com",
    "first_name": "John",
    "last_name": "Doe",
    "avatar_url": "https://...",
    "default_currency": "USD",
    "timezone": "America/New_York",
    "email_verified": true,
    "two_factor_enabled": false
  }
}
```

##### PATCH /users/me
Update current user profile
```json
Request:
{
  "first_name": "John",
  "last_name": "Smith",
  "default_currency": "EUR",
  "timezone": "Europe/London"
}

Response: 200 OK
{
  "success": true,
  "data": { /* updated user */ }
}
```

##### DELETE /users/me
Delete current user account
```json
Request:
{
  "password": "CurrentPassword123!"
}

Response: 200 OK
{
  "success": true,
  "message": "Account deleted successfully"
}
```

##### GET /users/me/settings
Get user settings
```json
Response: 200 OK
{
  "success": true,
  "data": {
    "theme": "dark",
    "date_format": "MM/DD/YYYY",
    "enable_notifications": true,
    ...
  }
}
```

##### PATCH /users/me/settings
Update user settings
```json
Request:
{
  "theme": "dark",
  "enable_notifications": false
}

Response: 200 OK
{
  "success": true,
  "data": { /* updated settings */ }
}
```

#### 5.2.3 Account Endpoints

##### GET /accounts
Get all user accounts
```json
Query Parameters:
- include_archived: boolean (default: false)
- type: account_type (optional)

Response: 200 OK
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "name": "Main Checking",
      "type": "checking",
      "currency": "USD",
      "current_balance": 5000.00,
      "initial_balance": 1000.00,
      "is_active": true,
      "icon": "bank",
      "color": "#4F46E5"
    }
  ]
}
```

##### POST /accounts
Create new account
```json
Request:
{
  "name": "Savings Account",
  "type": "savings",
  "currency": "USD",
  "initial_balance": 5000.00,
  "description": "Emergency fund",
  "icon": "piggy-bank",
  "color": "#10B981"
}

Response: 201 Created
{
  "success": true,
  "data": { /* created account */ }
}
```

##### GET /accounts/:id
Get account by ID
```json
Response: 200 OK
{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "Main Checking",
    "type": "checking",
    "current_balance": 5000.00,
    "transactions_count": 150,
    "last_transaction_date": "2025-11-14"
  }
}
```

##### PATCH /accounts/:id
Update account
```json
Request:
{
  "name": "Primary Checking",
  "is_active": true,
  "color": "#EF4444"
}

Response: 200 OK
{
  "success": true,
  "data": { /* updated account */ }
}
```

##### DELETE /accounts/:id
Archive account (soft delete)
```json
Response: 200 OK
{
  "success": true,
  "message": "Account archived successfully"
}
```

##### GET /accounts/:id/balance-history
Get account balance history
```json
Query Parameters:
- start_date: date (required)
- end_date: date (required)
- interval: day|week|month (default: day)

Response: 200 OK
{
  "success": true,
  "data": [
    {
      "date": "2025-11-01",
      "balance": 5000.00
    },
    {
      "date": "2025-11-08",
      "balance": 5250.00
    }
  ]
}
```

#### 5.2.4 Transaction Endpoints

##### GET /transactions
Get all transactions with pagination
```json
Query Parameters:
- page: integer (default: 1)
- limit: integer (default: 50, max: 100)
- account_id: uuid (optional)
- category_id: uuid (optional)
- type: income|expense|transfer (optional)
- start_date: date (optional)
- end_date: date (optional)
- search: string (optional)
- sort: date_desc|date_asc|amount_desc|amount_asc (default: date_desc)

Response: 200 OK
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "type": "expense",
      "amount": 45.50,
      "currency": "USD",
      "description": "Grocery shopping",
      "transaction_date": "2025-11-14",
      "account": {
        "id": "uuid",
        "name": "Main Checking"
      },
      "category": {
        "id": "uuid",
        "name": "Food & Dining",
        "icon": "utensils"
      },
      "tags": ["groceries"],
      "attachments_count": 1
    }
  ],
  "meta": {
    "page": 1,
    "limit": 50,
    "total": 500,
    "total_pages": 10
  }
}
```

##### POST /transactions
Create new transaction
```json
Request:
{
  "account_id": "uuid",
  "type": "expense",
  "amount": 45.50,
  "currency": "USD",
  "category_id": "uuid",
  "description": "Grocery shopping",
  "notes": "Weekly grocery run",
  "transaction_date": "2025-11-14",
  "tags": ["groceries"]
}

Response: 201 Created
{
  "success": true,
  "data": { /* created transaction */ }
}
```

##### GET /transactions/:id
Get transaction by ID
```json
Response: 200 OK
{
  "success": true,
  "data": {
    "id": "uuid",
    "type": "expense",
    "amount": 45.50,
    "description": "Grocery shopping",
    "account": { },
    "category": { },
    "tags": [],
    "attachments": []
  }
}
```

##### PATCH /transactions/:id
Update transaction
```json
Request:
{
  "amount": 47.00,
  "description": "Grocery shopping - updated",
  "category_id": "uuid"
}

Response: 200 OK
{
  "success": true,
  "data": { /* updated transaction */ }
}
```

##### DELETE /transactions/:id
Delete transaction (soft delete)
```json
Response: 200 OK
{
  "success": true,
  "message": "Transaction deleted successfully"
}
```

##### POST /transactions/:id/attachments
Upload attachment to transaction
```json
Request: multipart/form-data
- file: File (max 5MB)

Response: 201 Created
{
  "success": true,
  "data": {
    "id": "uuid",
    "file_name": "receipt.jpg",
    "file_url": "https://...",
    "file_size": 1024000
  }
}
```

##### DELETE /transactions/:id/attachments/:attachmentId
Delete transaction attachment
```json
Response: 200 OK
{
  "success": true,
  "message": "Attachment deleted successfully"
}
```

##### POST /transactions/import
Import transactions from CSV
```json
Request: multipart/form-data
- file: CSV file
- account_id: uuid
- column_mapping: JSON string
{
  "date": "Date",
  "amount": "Amount",
  "description": "Description",
  "category": "Category"
}

Response: 200 OK
{
  "success": true,
  "data": {
    "imported": 45,
    "skipped": 2,
    "errors": []
  }
}
```

##### GET /transactions/export
Export transactions to CSV
```json
Query Parameters:
- start_date: date (required)
- end_date: date (required)
- account_id: uuid (optional)
- format: csv|pdf (default: csv)

Response: 200 OK
Content-Type: text/csv
Content-Disposition: attachment; filename="transactions.csv"
```

#### 5.2.5 Category Endpoints

##### GET /categories
Get all categories
```json
Query Parameters:
- type: income|expense (optional)
- include_archived: boolean (default: false)

Response: 200 OK
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "name": "Food & Dining",
      "type": "expense",
      "icon": "utensils",
      "color": "#EF4444",
      "is_system": true,
      "subcategories": [
        {
          "id": "uuid",
          "name": "Restaurants",
          "icon": "restaurant"
        }
      ]
    }
  ]
}
```

##### POST /categories
Create new category
```json
Request:
{
  "name": "Freelance Income",
  "type": "income",
  "icon": "briefcase",
  "color": "#10B981",
  "parent_id": null
}

Response: 201 Created
{
  "success": true,
  "data": { /* created category */ }
}
```

##### PATCH /categories/:id
Update category
```json
Request:
{
  "name": "Freelance Projects",
  "color": "#3B82F6"
}

Response: 200 OK
{
  "success": true,
  "data": { /* updated category */ }
}
```

##### DELETE /categories/:id
Archive category
```json
Response: 200 OK
{
  "success": true,
  "message": "Category archived successfully"
}
```

##### GET /categories/:id/spending
Get category spending analytics
```json
Query Parameters:
- start_date: date (required)
- end_date: date (required)
- interval: day|week|month (default: month)

Response: 200 OK
{
  "success": true,
  "data": {
    "total_amount": 1500.00,
    "transaction_count": 45,
    "average_transaction": 33.33,
    "trend": [
      {
        "period": "2025-10",
        "amount": 750.00
      },
      {
        "period": "2025-11",
        "amount": 750.00
      }
    ]
  }
}
```

#### 5.2.6 Budget Endpoints

##### GET /budgets
Get all budgets
```json
Query Parameters:
- include_archived: boolean (default: false)
- period: weekly|monthly|yearly (optional)

Response: 200 OK
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "name": "Monthly Food Budget",
      "amount": 500.00,
      "period": "monthly",
      "start_date": "2025-11-01",
      "end_date": "2025-11-30",
      "currency": "USD",
      "spent_amount": 325.50,
      "progress_percentage": 65.10,
      "categories": [
        {
          "id": "uuid",
          "name": "Food & Dining"
        }
      ]
    }
  ]
}
```

##### POST /budgets
Create new budget
```json
Request:
{
  "name": "Monthly Transportation",
  "amount": 300.00,
  "period": "monthly",
  "start_date": "2025-11-01",
  "end_date": "2025-11-30",
  "currency": "USD",
  "category_ids": ["uuid1", "uuid2"],
  "alert_threshold": 80,
  "is_recurring": true
}

Response: 201 Created
{
  "success": true,
  "data": { /* created budget */ }
}
```

##### GET /budgets/:id
Get budget by ID with details
```json
Response: 200 OK
{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "Monthly Food Budget",
    "amount": 500.00,
    "spent_amount": 325.50,
    "remaining_amount": 174.50,
    "progress_percentage": 65.10,
    "days_remaining": 15,
    "daily_budget_remaining": 11.63,
    "categories": [],
    "recent_transactions": []
  }
}
```

##### PATCH /budgets/:id
Update budget
```json
Request:
{
  "amount": 600.00,
  "alert_threshold": 85
}

Response: 200 OK
{
  "success": true,
  "data": { /* updated budget */ }
}
```

##### DELETE /budgets/:id
Archive budget
```json
Response: 200 OK
{
  "success": true,
  "message": "Budget archived successfully"
}
```

#### 5.2.7 Goal Endpoints

##### GET /goals
Get all goals
```json
Query Parameters:
- include_completed: boolean (default: true)
- include_archived: boolean (default: false)

Response: 200 OK
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "name": "Emergency Fund",
      "target_amount": 10000.00,
      "current_amount": 6500.00,
      "currency": "USD",
      "deadline": "2026-12-31",
      "priority": "high",
      "progress_percentage": 65.00,
      "monthly_required": 291.67,
      "is_completed": false
    }
  ]
}
```

##### POST /goals
Create new goal
```json
Request:
{
  "name": "Vacation Fund",
  "target_amount": 5000.00,
  "current_amount": 0,
  "currency": "USD",
  "deadline": "2026-06-01",
  "priority": "medium",
  "description": "Summer vacation to Europe",
  "account_id": "uuid",
  "icon": "plane"
}

Response: 201 Created
{
  "success": true,
  "data": { /* created goal */ }
}
```

##### GET /goals/:id
Get goal by ID
```json
Response: 200 OK
{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "Vacation Fund",
    "target_amount": 5000.00,
    "current_amount": 1250.00,
    "progress_percentage": 25.00,
    "months_remaining": 6,
    "monthly_required": 625.00,
    "projected_completion": "2026-05-15"
  }
}
```

##### PATCH /goals/:id
Update goal
```json
Request:
{
  "current_amount": 1500.00,
  "target_amount": 5500.00
}

Response: 200 OK
{
  "success": true,
  "data": { /* updated goal */ }
}
```

##### POST /goals/:id/complete
Mark goal as completed
```json
Response: 200 OK
{
  "success": true,
  "data": {
    "id": "uuid",
    "is_completed": true,
    "completed_at": "2025-11-15T10:30:00Z"
  }
}
```

##### DELETE /goals/:id
Archive goal
```json
Response: 200 OK
{
  "success": true,
  "message": "Goal archived successfully"
}
```

#### 5.2.8 Recurring Transaction Endpoints

##### GET /recurring-transactions
Get all recurring transactions
```json
Query Parameters:
- is_active: boolean (optional)
- type: income|expense|transfer (optional)

Response: 200 OK
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "type": "expense",
      "amount": 50.00,
      "currency": "USD",
      "description": "Netflix Subscription",
      "frequency": "monthly",
      "interval": 1,
      "next_occurrence": "2025-12-01",
      "is_active": true,
      "account": {},
      "category": {}
    }
  ]
}
```

##### POST /recurring-transactions
Create recurring transaction
```json
Request:
{
  "account_id": "uuid",
  "category_id": "uuid",
  "type": "expense",
  "amount": 50.00,
  "currency": "USD",
  "description": "Netflix Subscription",
  "frequency": "monthly",
  "interval": 1,
  "start_date": "2025-11-01",
  "end_date": null,
  "notify_before_days": 1
}

Response: 201 Created
{
  "success": true,
  "data": { /* created recurring transaction */ }
}
```

##### PATCH /recurring-transactions/:id
Update recurring transaction
```json
Request:
{
  "amount": 55.00,
  "is_active": false
}

Response: 200 OK
{
  "success": true,
  "data": { /* updated recurring transaction */ }
}
```

##### DELETE /recurring-transactions/:id
Delete recurring transaction
```json
Response: 200 OK
{
  "success": true,
  "message": "Recurring transaction deleted successfully"
}
```

#### 5.2.9 Dashboard & Analytics Endpoints

##### GET /dashboard
Get dashboard summary
```json
Query Parameters:
- start_date: date (optional, default: current month start)
- end_date: date (optional, default: current month end)

Response: 200 OK
{
  "success": true,
  "data": {
    "total_balance": 15000.00,
    "total_income": 5000.00,
    "total_expenses": 3500.00,
    "net_income": 1500.00,
    "accounts_summary": [
      {
        "id": "uuid",
        "name": "Main Checking",
        "balance": 5000.00,
        "currency": "USD"
      }
    ],
    "recent_transactions": [],
    "active_budgets": [],
    "upcoming_recurring": [],
    "goals_progress": [],
    "spending_by_category": [
      {
        "category_id": "uuid",
        "category_name": "Food & Dining",
        "amount": 500.00,
        "percentage": 28.5
      }
    ]
  }
}
```

##### GET /analytics/income-expense
Get income vs expense analytics
```json
Query Parameters:
- start_date: date (required)
- end_date: date (required)
- interval: day|week|month (default: month)

Response: 200 OK
{
  "success": true,
  "data": [
    {
      "period": "2025-10",
      "income": 5000.00,
      "expense": 3200.00,
      "net": 1800.00
    },
    {
      "period": "2025-11",
      "income": 5000.00,
      "expense": 3500.00,
      "net": 1500.00
    }
  ]
}
```

##### GET /analytics/spending-trends
Get spending trends
```json
Query Parameters:
- start_date: date (required)
- end_date: date (required)
- category_id: uuid (optional)

Response: 200 OK
{
  "success": true,
  "data": {
    "average_daily": 116.67,
    "average_weekly": 816.67,
    "average_monthly": 3500.00,
    "highest_spending_day": {
      "date": "2025-11-10",
      "amount": 350.00
    },
    "trend": "increasing"
  }
}
```

##### GET /analytics/net-worth
Get net worth over time
```json
Query Parameters:
- start_date: date (required)
- end_date: date (required)
- interval: day|week|month (default: month)

Response: 200 OK
{
  "success": true,
  "data": [
    {
      "date": "2025-10-31",
      "net_worth": 13500.00,
      "assets": 15000.00,
      "liabilities": 1500.00
    },
    {
      "date": "2025-11-30",
      "net_worth": 15000.00,
      "assets": 17000.00,
      "liabilities": 2000.00
    }
  ]
}
```

##### GET /analytics/category-breakdown
Get detailed category breakdown
```json
Query Parameters:
- start_date: date (required)
- end_date: date (required)
- type: income|expense (default: expense)

Response: 200 OK
{
  "success": true,
  "data": [
    {
      "category_id": "uuid",
      "category_name": "Food & Dining",
      "amount": 500.00,
      "percentage": 28.5,
      "transaction_count": 15,
      "average_transaction": 33.33,
      "subcategories": []
    }
  ]
}
```

#### 5.2.10 Sharing Endpoints

##### GET /accounts/:id/shares
Get account shares
```json
Response: 200 OK
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "shared_with": {
        "id": "uuid",
        "email": "partner@example.com",
        "first_name": "Jane"
      },
      "permission": "full_access",
      "is_accepted": true,
      "created_at": "2025-10-01T00:00:00Z"
    }
  ]
}
```

##### POST /accounts/:id/share
Share account with user
```json
Request:
{
  "email": "partner@example.com",
  "permission": "view"
}

Response: 201 Created
{
  "success": true,
  "data": { /* share details */ },
  "message": "Invitation sent successfully"
}
```

##### PATCH /account-shares/:id
Update share permission
```json
Request:
{
  "permission": "add_transactions"
}

Response: 200 OK
{
  "success": true,
  "data": { /* updated share */ }
}
```

##### DELETE /account-shares/:id
Revoke account share
```json
Response: 200 OK
{
  "success": true,
  "message": "Access revoked successfully"
}
```

##### POST /account-shares/:id/accept
Accept share invitation
```json
Response: 200 OK
{
  "success": true,
  "message": "Invitation accepted"
}
```

##### POST /account-shares/:id/reject
Reject share invitation
```json
Response: 200 OK
{
  "success": true,
  "message": "Invitation rejected"
}
```

#### 5.2.11 Notification Endpoints

##### GET /notifications
Get all notifications
```json
Query Parameters:
- is_read: boolean (optional)
- type: notification_type (optional)
- page: integer (default: 1)
- limit: integer (default: 20)

Response: 200 OK
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "type": "budget_exceeded",
      "title": "Budget Exceeded",
      "message": "Your Monthly Food Budget has been exceeded",
      "data": {
        "budget_id": "uuid",
        "budget_name": "Monthly Food Budget"
      },
      "is_read": false,
      "created_at": "2025-11-15T10:00:00Z"
    }
  ],
  "meta": {
    "unread_count": 5
  }
}
```

##### PATCH /notifications/:id/read
Mark notification as read
```json
Response: 200 OK
{
  "success": true,
  "message": "Notification marked as read"
}
```

##### POST /notifications/read-all
Mark all notifications as read
```json
Response: 200 OK
{
  "success": true,
  "message": "All notifications marked as read"
}
```

##### DELETE /notifications/:id
Delete notification
```json
Response: 200 OK
{
  "success": true,
  "message": "Notification deleted"
}
```

### 5.3 API Error Codes

```
AUTH_001: Invalid credentials
AUTH_002: Email already exists
AUTH_003: Invalid or expired token
AUTH_004: Account not verified
AUTH_005: Two-factor authentication required
AUTH_006: Invalid two-factor code

VALID_001: Validation error
VALID_002: Missing required field
VALID_003: Invalid field format
VALID_004: Field value out of range

RES_001: Resource not found
RES_002: Resource already exists
RES_003: Cannot delete resource (in use)
RES_004: Access denied

SYS_001: Internal server error
SYS_002: Database error
SYS_003: Service unavailable
SYS_004: Rate limit exceeded

BIZ_001: Insufficient balance
BIZ_002: Budget limit exceeded
BIZ_003: Cannot transfer to same account
BIZ_004: Invalid date range
```

---

## 6. Database Migration Strategy

### 6.1 Migration Tools
- Use a migration library compatible with multiple languages
- Recommended: Flyway, Liquibase, or language-specific tools
- For Node.js backend: `node-pg-migrate` or `db-migrate`
- For Python backend: `Alembic`
- For Go backend: `golang-migrate`

### 6.2 Migration Naming Convention
```
V{version}__{description}.sql

Examples:
V001__create_users_table.sql
V002__create_accounts_table.sql
V003__add_two_factor_to_users.sql
```

### 6.3 Migration Structure

#### Migration File Example
```sql
-- V001__create_users_table.sql
-- ================================================
-- Description: Create users table with authentication
-- Author: Development Team
-- Date: 2025-11-15
-- ================================================

-- Up Migration
CREATE TABLE IF NOT EXISTS users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255),
    first_name VARCHAR(100),
    last_name VARCHAR(100),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_users_email ON users(email);

-- Down Migration (in separate file or section)
-- DROP TABLE IF EXISTS users CASCADE;
```

### 6.4 Migration Execution Order

1. **Phase 1: Core Tables (V001-V010)**
   - V001: users
   - V002: user_settings
   - V003: refresh_tokens
   - V004: accounts
   - V005: categories
   - V006: transactions
   - V007: transaction_attachments
   - V008: tags
   - V009: transaction_tags
   - V010: audit_logs

2. **Phase 2: Budget & Goals (V011-V015)**
   - V011: budgets
   - V012: budget_categories
   - V013: goals
   - V014: recurring_transactions
   - V015: notifications

3. **Phase 3: Sharing Features (V016-V020)**
   - V016: account_shares
   - V017: households
   - V018: household_members

4. **Phase 4: Views & Functions (V021-V025)**
   - V021: vw_account_balances view
   - V022: vw_budget_progress view
   - V023: vw_category_spending view
   - V024: trigger functions
   - V025: helper functions

5. **Phase 5: Indexes & Optimizations (V026+)**
   - V026: additional indexes
   - V027: performance optimizations

### 6.5 Migration Best Practices

1. **Always make migrations reversible**
2. **Test migrations on development/staging before production**
3. **Backup database before running migrations**
4. **Use transactions for migration execution**
5. **Version control all migration files**
6. **Document complex migrations**
7. **Never modify existing migrations - create new ones**
8. **Use consistent naming conventions**

### 6.6 Rollback Strategy

```sql
-- Each migration should have a corresponding rollback
-- Example rollback script

-- Rollback V015__create_notifications_table.sql
BEGIN;

DROP TABLE IF EXISTS notifications CASCADE;

-- Log the rollback
INSERT INTO migration_log (version, action, executed_at)
VALUES ('V015', 'rollback', CURRENT_TIMESTAMP);

COMMIT;
```

---

## 7. Database Seeder

### 7.1 Seeder Purpose
- Populate database with initial/default data
- Create demo accounts for testing
- Set up system categories
- Generate sample data for development

### 7.2 Seed Data Structure

#### 7.2.1 System Categories Seed
```json
{
  "income_categories": [
    {
      "name": "Salary",
      "icon": "briefcase",
      "color": "#10B981",
      "is_system": true
    },
    {
      "name": "Freelance",
      "icon": "laptop",
      "color": "#3B82F6",
      "is_system": true
    },
    {
      "name": "Investment",
      "icon": "chart-line",
      "color": "#8B5CF6",
      "is_system": true
    },
    {
      "name": "Gift",
      "icon": "gift",
      "color": "#EC4899",
      "is_system": true
    },
    {
      "name": "Other Income",
      "icon": "plus-circle",
      "color": "#6B7280",
      "is_system": true
    }
  ],
  "expense_categories": [
    {
      "name": "Food & Dining",
      "icon": "utensils",
      "color": "#EF4444",
      "is_system": true,
      "subcategories": [
        {"name": "Restaurants", "icon": "restaurant"},
        {"name": "Groceries", "icon": "shopping-cart"},
        {"name": "Coffee & Tea", "icon": "coffee"}
      ]
    },
    {
      "name": "Transportation",
      "icon": "car",
      "color": "#F59E0B",
      "is_system": true,
      "subcategories": [
        {"name": "Fuel", "icon": "gas-pump"},
        {"name": "Public Transit", "icon": "bus"},
        {"name": "Parking", "icon": "parking"},
        {"name": "Maintenance", "icon": "wrench"}
      ]
    },
    {
      "name": "Shopping",
      "icon": "shopping-bag",
      "color": "#EC4899",
      "is_system": true,
      "subcategories": [
        {"name": "Clothing", "icon": "tshirt"},
        {"name": "Electronics", "icon": "laptop"},
        {"name": "Home & Garden", "icon": "home"}
      ]
    },
    {
      "name": "Entertainment",
      "icon": "film",
      "color": "#8B5CF6",
      "is_system": true,
      "subcategories": [
        {"name": "Movies & TV", "icon": "tv"},
        {"name": "Games", "icon": "gamepad"},
        {"name": "Sports", "icon": "football"}
      ]
    },
    {
      "name": "Bills & Utilities",
      "icon": "file-invoice",
      "color": "#3B82F6",
      "is_system": true,
      "subcategories": [
        {"name": "Electricity", "icon": "bolt"},
        {"name": "Water", "icon": "tint"},
        {"name": "Internet", "icon": "wifi"},
        {"name": "Phone", "icon": "phone"}
      ]
    },
    {
      "name": "Healthcare",
      "icon": "heartbeat",
      "color": "#10B981",
      "is_system": true,
      "subcategories": [
        {"name": "Doctor", "icon": "stethoscope"},
        {"name": "Pharmacy", "icon": "pills"},
        {"name": "Insurance", "icon": "shield"}
      ]
    },
    {
      "name": "Education",
      "icon": "graduation-cap",
      "color": "#6366F1",
      "is_system": true,
      "subcategories": [
        {"name": "Tuition", "icon": "school"},
        {"name": "Books", "icon": "book"},
        {"name": "Courses", "icon": "certificate"}
      ]
    },
    {
      "name": "Travel",
      "icon": "plane",
      "color": "#06B6D4",
      "is_system": true,
      "subcategories": [
        {"name": "Flights", "icon": "plane-departure"},
        {"name": "Hotels", "icon": "hotel"},
        {"name": "Activities", "icon": "hiking"}
      ]
    },
    {
      "name": "Personal Care",
      "icon": "spa",
      "color": "#F97316",
      "is_system": true,
      "subcategories": [
        {"name": "Haircut", "icon": "cut"},
        {"name": "Gym", "icon": "dumbbell"}
      ]
    },
    {
      "name": "Housing",
      "icon": "home",
      "color": "#0EA5E9",
      "is_system": true,
      "subcategories": [
        {"name": "Rent", "icon": "key"},
        {"name": "Mortgage", "icon": "home-lg-alt"},
        {"name": "Repairs", "icon": "tools"}
      ]
    },
    {
      "name": "Other Expenses",
      "icon": "ellipsis-h",
      "color": "#6B7280",
      "is_system": true
    }
  ]
}
```

#### 7.2.2 Demo User Seed
```javascript
const demoUser = {
  email: "demo@walletflow.com",
  password: "Demo123!",
  first_name: "Demo",
  last_name: "User",
  default_currency: "USD",
  email_verified: true
};

const demoAccounts = [
  {
    name: "Main Checking",
    type: "checking",
    currency: "USD",
    initial_balance: 5000.00,
    icon: "bank",
    color: "#4F46E5"
  },
  {
    name: "Savings Account",
    type: "savings",
    currency: "USD",
    initial_balance: 10000.00,
    icon: "piggy-bank",
    color: "#10B981"
  },
  {
    name: "Credit Card",
    type: "credit_card",
    currency: "USD",
    initial_balance: -1500.00,
    icon: "credit-card",
    color: "#EF4444"
  }
];

// Generate 3 months of sample transactions
const generateSampleTransactions = (userId, accounts, categories) => {
  const transactions = [];
  const startDate = new Date();
  startDate.setMonth(startDate.getMonth() - 3);
  
  // Generate 100-150 transactions over 3 months
  for (let i = 0; i < 125; i++) {
    transactions.push({
      user_id: userId,
      account_id: randomFromArray(accounts).id,
      category_id: randomFromArray(categories.filter(c => c.type === 'expense')).id,
      type: Math.random() > 0.15 ? 'expense' : 'income',
      amount: randomAmount(10, 300),
      currency: 'USD',
      description: randomDescription(),
      transaction_date: randomDate(startDate, new Date())
    });
  }
  
  return transactions;
};
```

### 7.3 Seeder Execution Order

1. **System Data**
   - Default categories (income/expense)
   - System settings
   - Currency list

2. **Demo User (Optional for Development)**
   - Demo user account
   - Demo accounts (checking, savings, credit)
   - Sample transactions
   - Sample budgets
   - Sample goals

### 7.4 Seeder Scripts

#### Node.js Seeder Example
```javascript
// seeders/001-system-categories.js
module.exports = {
  up: async (db) => {
    const categories = require('./data/categories.json');
    
    for (const category of categories.expense_categories) {
      const result = await db.query(
        `INSERT INTO categories (name, type, icon, color, is_system)
         VALUES ($1, $2, $3, $4, $5)
         RETURNING id`,
        [category.name, 'expense', category.icon, category.color, true]
      );
      
      if (category.subcategories) {
        for (const sub of category.subcategories) {
          await db.query(
            `INSERT INTO categories (name, type, icon, color, is_system, parent_id)
             VALUES ($1, $2, $3, $4, $5, $6)`,
            [sub.name, 'expense', sub.icon, category.color, true, result.rows[0].id]
          );
        }
      }
    }
  },
  
  down: async (db) => {
    await db.query('DELETE FROM categories WHERE is_system = true');
  }
};
```

---

## 8. Testing Strategy

### 8.1 Testing Pyramid

```
          /\
         /  \    E2E Tests (10%)
        /____\
       /      \  Integration Tests (30%)
      /________\
     /          \ Unit Tests (60%)
    /__________/
```

### 8.2 Unit Testing

#### 8.2.1 Backend Unit Tests

**Test Categories:**
- Service layer functions
- Utility functions
- Data validation
- Business logic
- Database queries (with mocks)

**Example Tests:**

```javascript
// __tests__/services/transaction.service.test.js
describe('TransactionService', () => {
  describe('createTransaction', () => {
    it('should create an expense transaction successfully', async () => {
      const mockTransaction = {
        account_id: 'uuid',
        type: 'expense',
        amount: 100.00,
        category_id: 'uuid',
        description: 'Test expense',
        transaction_date: '2025-11-15'
      };
      
      const result = await TransactionService.create(mockTransaction);
      
      expect(result).toHaveProperty('id');
      expect(result.amount).toBe(100.00);
      expect(result.type).toBe('expense');
    });
    
    it('should throw error for negative amount', async () => {
      const invalidTransaction = {
        account_id: 'uuid',
        type: 'expense',
        amount: -100.00,
        category_id: 'uuid'
      };
      
      await expect(
        TransactionService.create(invalidTransaction)
      ).rejects.toThrow('Amount must be positive');
    });
    
    it('should update account balance after creating transaction', async () => {
      const accountBefore = await AccountService.findById('uuid');
      
      await TransactionService.create({
        account_id: 'uuid',
        type: 'expense',
        amount: 50.00,
        category_id: 'uuid'
      });
      
      const accountAfter = await AccountService.findById('uuid');
      
      expect(accountAfter.current_balance).toBe(
        accountBefore.current_balance - 50.00
      );
    });
  });
  
  describe('validateTransfer', () => {
    it('should reject transfer to same account', () => {
      const transfer = {
        account_id: 'uuid-1',
        to_account_id: 'uuid-1',
        type: 'transfer',
        amount: 100
      };
      
      expect(() => {
        TransactionService.validateTransfer(transfer);
      }).toThrow('Cannot transfer to same account');
    });
  });
});

// __tests__/utils/currency.util.test.js
describe('Currency Utils', () => {
  describe('convertCurrency', () => {
    it('should convert USD to EUR correctly', () => {
      const result = convertCurrency(100, 'USD', 'EUR', 0.85);
      expect(result).toBe(85.00);
    });
    
    it('should return same amount for same currency', () => {
      const result = convertCurrency(100, 'USD', 'USD');
      expect(result).toBe(100.00);
    });
  });
  
  describe('formatCurrency', () => {
    it('should format USD correctly', () => {
      const result = formatCurrency(1234.56, 'USD', 'en-US');
      expect(result).toBe('$1,234.56');
    });
    
    it('should format EUR correctly', () => {
      const result = formatCurrency(1234.56, 'EUR', 'de-DE');
      expect(result).toBe('1.234,56 €');
    });
  });
});
```

**Testing Tools:**
- Test Framework: Jest / Vitest
- Assertion Library: Built-in (Jest) or Chai
- Mocking: jest.mock() or Sinon
- Code Coverage: Jest coverage reports

**Coverage Targets:**
- Overall: 80% minimum
- Services: 90% minimum
- Utils: 95% minimum
- Controllers: 70% minimum

#### 8.2.2 Frontend Unit Tests

**Test Categories:**
- React components (isolated)
- Custom hooks
- Utility functions
- Form validation
- State management

**Example Tests:**

```typescript
// __tests__/components/TransactionForm.test.tsx
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import { TransactionForm } from '@/components/transactions/TransactionForm';

describe('TransactionForm', () => {
  it('should render all form fields', () => {
    render(<TransactionForm />);
    
    expect(screen.getByLabelText(/amount/i)).toBeInTheDocument();
    expect(screen.getByLabelText(/description/i)).toBeInTheDocument();
    expect(screen.getByLabelText(/category/i)).toBeInTheDocument();
    expect(screen.getByLabelText(/date/i)).toBeInTheDocument();
  });
  
  it('should validate required fields', async () => {
    render(<TransactionForm />);
    
    const submitButton = screen.getByRole('button', { name: /submit/i });
    fireEvent.click(submitButton);
    
    await waitFor(() => {
      expect(screen.getByText(/amount is required/i)).toBeInTheDocument();
      expect(screen.getByText(/category is required/i)).toBeInTheDocument();
    });
  });
  
  it('should submit form with valid data', async () => {
    const onSubmit = jest.fn();
    render(<TransactionForm onSubmit={onSubmit} />);
    
    fireEvent.change(screen.getByLabelText(/amount/i), {
      target: { value: '100.50' }
    });
    fireEvent.change(screen.getByLabelText(/description/i), {
      target: { value: 'Test transaction' }
    });
    
    const submitButton = screen.getByRole('button', { name: /submit/i });
    fireEvent.click(submitButton);
    
    await waitFor(() => {
      expect(onSubmit).toHaveBeenCalledWith({
        amount: 100.50,
        description: 'Test transaction',
        // ... other fields
      });
    });
  });
});

// __tests__/hooks/useTransactions.test.ts
import { renderHook, waitFor } from '@testing-library/react';
import { useTransactions } from '@/hooks/useTransactions';

describe('useTransactions', () => {
  it('should fetch transactions on mount', async () => {
    const { result } = renderHook(() => useTransactions());
    
    expect(result.current.loading).toBe(true);
    
    await waitFor(() => {
      expect(result.current.loading).toBe(false);
      expect(result.current.transactions).toHaveLength(50);
    });
  });
  
  it('should handle pagination', async () => {
    const { result } = renderHook(() => useTransactions());
    
    await waitFor(() => {
      expect(result.current.transactions).toHaveLength(50);
    });
    
    act(() => {
      result.current.loadMore();
    });
    
    await waitFor(() => {
      expect(result.current.transactions).toHaveLength(100);
    });
  });
});
```

**Testing Tools:**
- Test Framework: Vitest or Jest
- Component Testing: React Testing Library
- Hooks Testing: @testing-library/react-hooks
- Mock API: MSW (Mock Service Worker)

### 8.3 Integration Testing

#### 8.3.1 API Integration Tests

**Test Scenarios:**
- Complete API workflows
- Database interactions
- Authentication flows
- Multi-step operations

**Example Tests:**

```javascript
// __tests__/integration/transaction-workflow.test.js
describe('Transaction Workflow Integration', () => {
  let authToken;
  let userId;
  let accountId;
  
  beforeAll(async () => {
    // Setup: Create user and get auth token
    const registerRes = await request(app)
      .post('/api/v1/auth/register')
      .send({
        email: 'test@example.com',
        password: 'Test123!',
        first_name: 'Test',
        last_name: 'User'
      });
    
    authToken = registerRes.body.data.access_token;
    userId = registerRes.body.data.user.id;
    
    // Create an account
    const accountRes = await request(app)
      .post('/api/v1/accounts')
      .set('Authorization', `Bearer ${authToken}`)
      .send({
        name: 'Test Account',
        type: 'checking',
        currency: 'USD',
        initial_balance: 1000.00
      });
    
    accountId = accountRes.body.data.id;
  });
  
  it('should complete full transaction lifecycle', async () => {
    // 1. Create transaction
    const createRes = await request(app)
      .post('/api/v1/transactions')
      .set('Authorization', `Bearer ${authToken}`)
      .send({
        account_id: accountId,
        type: 'expense',
        amount: 50.00,
        category_id: 'category-uuid',
        description: 'Test expense',
        transaction_date: '2025-11-15'
      });
    
    expect(createRes.status).toBe(201);
    expect(createRes.body.data).toHaveProperty('id');
    const transactionId = createRes.body.data.id;
    
    // 2. Verify account balance updated
    const accountRes = await request(app)
      .get(`/api/v1/accounts/${accountId}`)
      .set('Authorization', `Bearer ${authToken}`);
    
    expect(accountRes.body.data.current_balance).toBe(950.00);
    
    // 3. Update transaction
    const updateRes = await request(app)
      .patch(`/api/v1/transactions/${transactionId}`)
      .set('Authorization', `Bearer ${authToken}`)
      .send({
        amount: 75.00
      });
    
    expect(updateRes.status).toBe(200);
    
    // 4. Verify balance updated again
    const accountRes2 = await request(app)
      .get(`/api/v1/accounts/${accountId}`)
      .set('Authorization', `Bearer ${authToken}`);
    
    expect(accountRes2.body.data.current_balance).toBe(925.00);
    
    // 5. Delete transaction
    const deleteRes = await request(app)
      .delete(`/api/v1/transactions/${transactionId}`)
      .set('Authorization', `Bearer ${authToken}`);
    
    expect(deleteRes.status).toBe(200);
    
    // 6. Verify balance restored
    const accountRes3 = await request(app)
      .get(`/api/v1/accounts/${accountId}`)
      .set('Authorization', `Bearer ${authToken}`);
    
    expect(accountRes3.body.data.current_balance).toBe(1000.00);
  });
});

// __tests__/integration/budget-tracking.test.js
describe('Budget Tracking Integration', () => {
  it('should track budget progress correctly', async () => {
    // Create budget
    const budget = await request(app)
      .post('/api/v1/budgets')
      .set('Authorization', `Bearer ${authToken}`)
      .send({
        name: 'Monthly Food',
        amount: 500.00,
        period: 'monthly',
        start_date: '2025-11-01',
        end_date: '2025-11-30',
        category_ids: [foodCategoryId]
      });
    
    const budgetId = budget.body.data.id;
    
    // Add transactions in category
    await request(app)
      .post('/api/v1/transactions')
      .set('Authorization', `Bearer ${authToken}`)
      .send({
        account_id: accountId,
        type: 'expense',
        amount: 200.00,
        category_id: foodCategoryId,
        transaction_date: '2025-11-10'
      });
    
    // Check budget progress
    const budgetProgress = await request(app)
      .get(`/api/v1/budgets/${budgetId}`)
      .set('Authorization', `Bearer ${authToken}`);
    
    expect(budgetProgress.body.data.spent_amount).toBe(200.00);
    expect(budgetProgress.body.data.progress_percentage).toBe(40.00);
    expect(budgetProgress.body.data.remaining_amount).toBe(300.00);
    
    // Add more transactions to exceed budget
    await request(app)
      .post('/api/v1/transactions')
      .set('Authorization', `Bearer ${authToken}`)
      .send({
        account_id: accountId,
        type: 'expense',
        amount: 350.00,
        category_id: foodCategoryId,
        transaction_date: '2025-11-15'
      });
    
    // Verify budget exceeded
    const budgetFinal = await request(app)
      .get(`/api/v1/budgets/${budgetId}`)
      .set('Authorization', `Bearer ${authToken}`);
    
    expect(budgetFinal.body.data.spent_amount).toBe(550.00);
    expect(budgetFinal.body.data.progress_percentage).toBe(110.00);
    
    // Verify notification created
    const notifications = await request(app)
      .get('/api/v1/notifications')
      .set('Authorization', `Bearer ${authToken}`);
    
    const budgetNotif = notifications.body.data.find(
      n => n.type === 'budget_exceeded'
    );
    expect(budgetNotif).toBeDefined();
  });
});
```

**Testing Tools:**
- API Testing: Supertest or Axios
- Test Database: Separate test database
- Transaction Rollback: After each test
- Test Containers: For isolated DB instances

#### 8.3.2 Frontend Integration Tests

**Example Tests:**

```typescript
// __tests__/integration/dashboard.test.tsx
describe('Dashboard Integration', () => {
  it('should display complete dashboard with real data', async () => {
    // Mock API responses
    server.use(
      rest.get('/api/v1/dashboard', (req, res, ctx) => {
        return res(ctx.json({
          success: true,
          data: mockDashboardData
        }));
      })
    );
    
    render(<Dashboard />);
    
    await waitFor(() => {
      expect(screen.getByText(/Total Balance/i)).toBeInTheDocument();
      expect(screen.getByText(/\$15,000.00/i)).toBeInTheDocument();
    });
    
    expect(screen.getByText(/Recent Transactions/i)).toBeInTheDocument();
    expect(screen.getAllByRole('row')).toHaveLength(11); // Header + 10 transactions
    
    expect(screen.getByText(/Active Budgets/i)).toBeInTheDocument();
    expect(screen.getByText(/Monthly Food/i)).toBeInTheDocument();
  });
  
  it('should handle adding transaction from dashboard', async () => {
    render(<Dashboard />);
    
    const addButton = screen.getByRole('button', { name: /add transaction/i });
    fireEvent.click(addButton);
    
    // Fill form
    fireEvent.change(screen.getByLabelText(/amount/i), {
      target: { value: '100' }
    });
    fireEvent.change(screen.getByLabelText(/description/i), {
      target: { value: 'Test expense' }
    });
    
    fireEvent.click(screen.getByRole('button', { name: /save/i }));
    
    await waitFor(() => {
      expect(screen.getByText(/Transaction added/i)).toBeInTheDocument();
    });
  });
});
```

### 8.4 End-to-End (E2E) Testing

**Test Scenarios:**
- Critical user journeys
- Cross-browser compatibility
- Real user workflows

**Example Tests:**

```typescript
// e2e/user-onboarding.spec.ts
import { test, expect } from '@playwright/test';

test.describe('User Onboarding Flow', () => {
  test('should complete full onboarding process', async ({ page }) => {
    // 1. Visit homepage
    await page.goto('/');
    
    // 2. Click sign up
    await page.click('text=Sign Up');
    
    // 3. Fill registration form
    await page.fill('input[name="email"]', 'newuser@example.com');
    await page.fill('input[name="password"]', 'SecurePass123!');
    await page.fill('input[name="first_name"]', 'New');
    await page.fill('input[name="last_name"]', 'User');
    await page.click('button[type="submit"]');
    
    // 4. Verify redirected to onboarding
    await expect(page).toHaveURL(/\/onboarding/);
    
    // 5. Set currency
    await page.selectOption('select[name="currency"]', 'USD');
    await page.click('text=Next');
    
    // 6. Create first account
    await page.fill('input[name="account_name"]', 'Main Checking');
    await page.selectOption('select[name="account_type"]', 'checking');
    await page.fill('input[name="initial_balance"]', '5000');
    await page.click('text=Create Account');
    
    // 7. Skip category customization
    await page.click('text=Skip for now');
    
    // 8. Verify redirected to dashboard
    await expect(page).toHaveURL(/\/dashboard/);
    await expect(page.locator('h1')).toContainText('Dashboard');
    
    // 9. Verify account visible
    await expect(page.locator('text=Main Checking')).toBeVisible();
    await expect(page.locator('text=$5,000.00')).toBeVisible();
  });
});

// e2e/transaction-management.spec.ts
test.describe('Transaction Management', () => {
  test('should create, edit, and delete transaction', async ({ page }) => {
    await page.goto('/dashboard');
    
    // Login first
    await page.fill('input[name="email"]', 'test@example.com');
    await page.fill('input[name="password"]', 'Test123!');
    await page.click('button[type="submit"]');
    
    // Create transaction
    await page.click('text=Add Transaction');
    await page.fill('input[name="amount"]', '100.50');
    await page.fill('input[name="description"]', 'Grocery shopping');
    await page.selectOption('select[name="category"]', 'Food & Dining');
    await page.click('button:has-text("Save")');
    
    // Verify transaction appears
    await expect(page.locator('text=Grocery shopping')).toBeVisible();
    await expect(page.locator('text=$100.50')).toBeVisible();
    
    // Edit transaction
    await page.click('text=Grocery shopping');
    await page.click('button:has-text("Edit")');
    await page.fill('input[name="amount"]', '105.75');
    await page.click('button:has-text("Save")');
    
    // Verify update
    await expect(page.locator('text=$105.75')).toBeVisible();
    
    // Delete transaction
    await page.click('text=Grocery shopping');
    await page.click('button:has-text("Delete")');
    await page.click('button:has-text("Confirm")');
    
    // Verify deletion
    await expect(page.locator('text=Grocery shopping')).not.toBeVisible();
  });
});
```

**E2E Testing Tools:**
- Playwright or Cypress
- Cross-browser testing
- Visual regression testing (Percy, Chromatic)
- Performance testing

### 8.5 Test Data Management

```javascript
// __tests__/fixtures/users.fixture.js
export const testUsers = {
  validUser: {
    email: 'test@example.com',
    password: 'Test123!',
    first_name: 'Test',
    last_name: 'User'
  },
  adminUser: {
    email: 'admin@example.com',
    password: 'Admin123!',
    first_name: 'Admin',
    last_name: 'User',
    role: 'admin'
  }
};

// __tests__/fixtures/transactions.fixture.js
export const testTransactions = {
  validExpense: {
    type: 'expense',
    amount: 100.00,
    currency: 'USD',
    description: 'Test expense',
    transaction_date: '2025-11-15'
  },
  validIncome: {
    type: 'income',
    amount: 5000.00,
    currency: 'USD',
    description: 'Salary',
    transaction_date: '2025-11-01'
  }
};
```

### 8.6 CI/CD Integration

```yaml
# .github/workflows/test.yml
name: Tests

on: [push, pull_request]

jobs:
  unit-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
      - run: npm install
      - run: npm run test:unit
      - run: npm run test:coverage
      
  integration-tests:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_DB: walletflow_test
          POSTGRES_PASSWORD: postgres
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
      - run: npm install
      - run: npm run migrate:test
      - run: npm run test:integration
      
  e2e-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
      - run: npm install
      - run: npx playwright install
      - run: npm run test:e2e
```

---

## 9. Technology Stack & Libraries

### 9.1 Core Stack (Single Application)

#### 9.1.1 Framework
```json
{
  "dependencies": {
    "next": "^15.0.0",
    "react": "^18.3.0",
    "react-dom": "^18.3.0",
    "typescript": "^5.3.0"
  }
}
```

#### 9.1.2 Database & ORM
```json
{
  "dependencies": {
    "@prisma/client": "^5.7.0"
  },
  "devDependencies": {
    "prisma": "^5.7.0"
  }
}
```

**Prisma Schema Location**: `prisma/schema.prisma`

**Prisma Setup:**
```prisma
// prisma/schema.prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

// Models follow the database schema from Section 6
model User {
  id            String   @id @default(uuid())
  email         String   @unique
  passwordHash  String?  @map("password_hash")
  firstName     String?  @map("first_name")
  lastName      String?  @map("last_name")
  avatarUrl     String?  @map("avatar_url")
  // ... rest of fields
  
  accounts      Account[]
  transactions  Transaction[]
  budgets       Budget[]
  goals         Goal[]
  
  @@map("users")
}

// ... other models
```

#### 9.1.3 Authentication
```json
{
  "dependencies": {
    "next-auth": "^5.0.0",
    "bcrypt": "^5.1.0",
    "@types/bcrypt": "^5.0.0"
  }
}
```

**NextAuth.js Configuration:**
```typescript
// app/api/auth/[...nextauth]/route.ts
import NextAuth from 'next-auth';
import CredentialsProvider from 'next-auth/providers/credentials';
import GoogleProvider from 'next-auth/providers/google';
import { PrismaAdapter } from '@auth/prisma-adapter';
import { prisma } from '@/lib/prisma';
import bcrypt from 'bcrypt';

export const authOptions = {
  adapter: PrismaAdapter(prisma),
  providers: [
    CredentialsProvider({
      name: 'Credentials',
      credentials: {
        email: { label: "Email", type: "email" },
        password: { label: "Password", type: "password" }
      },
      async authorize(credentials) {
        if (!credentials?.email || !credentials?.password) {
          return null;
        }

        const user = await prisma.user.findUnique({
          where: { email: credentials.email },
        });

        if (!user || !user.passwordHash) {
          return null;
        }

        const isValid = await bcrypt.compare(
          credentials.password,
          user.passwordHash
        );

        if (!isValid) {
          return null;
        }

        return {
          id: user.id,
          email: user.email,
          name: `${user.firstName} ${user.lastName}`,
        };
      },
    }),
    GoogleProvider({
      clientId: process.env.GOOGLE_CLIENT_ID!,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
    }),
  ],
  session: {
    strategy: 'jwt',
    maxAge: 7 * 24 * 60 * 60, // 7 days
  },
  pages: {
    signIn: '/login',
    signOut: '/logout',
    error: '/error',
  },
};

const handler = NextAuth(authOptions);
export { handler as GET, handler as POST };
```

### 9.2 Frontend Libraries

#### 9.2.1 UI & Styling
```json
{
  "dependencies": {
    "@radix-ui/react-dialog": "^1.0.5",
    "@radix-ui/react-dropdown-menu": "^2.0.6",
    "@radix-ui/react-select": "^2.0.0",
    "@radix-ui/react-tabs": "^1.0.4",
    "@radix-ui/react-popover": "^1.0.7",
    "tailwindcss": "^3.4.0",
    "clsx": "^2.1.0",
    "tailwind-merge": "^2.2.0",
    "lucide-react": "^0.300.0",
    "recharts": "^2.10.0",
    "react-hot-toast": "^2.4.1"
  }
}
```

#### 9.1.3 Form Handling & Validation
```json
{
  "dependencies": {
    "react-hook-form": "^7.49.0",
    "zod": "^3.22.0",
    "@hookform/resolvers": "^3.3.0"
  }
}
```

#### 9.1.4 State Management
```json
{
  "dependencies": {
    "@tanstack/react-query": "^5.17.0",
    "zustand": "^4.4.0"
  }
}
```

#### 9.1.5 Data Fetching & API
```json
{
  "dependencies": {
    "axios": "^1.6.0",
    "@tanstack/react-query-devtools": "^5.17.0"
  }
}
```

#### 9.1.6 Date & Time
```json
{
  "dependencies": {
    "date-fns": "^3.0.0",
    "react-day-picker": "^8.10.0"
  }
}
```

#### 9.1.7 File Upload
```json
{
  "dependencies": {
    "react-dropzone": "^14.2.0"
  }
}
```

#### 9.1.8 Authentication
```json
{
  "dependencies": {
    "next-auth": "^5.0.0",
    "jose": "^5.2.0"
  }
}
```

#### 9.1.9 Internationalization
```json
{
  "dependencies": {
    "next-intl": "^3.4.0"
  }
}
```

#### 9.1.10 Charts & Visualization
```json
{
  "dependencies": {
    "recharts": "^2.10.0",
    "chart.js": "^4.4.0",
    "react-chartjs-2": "^5.2.0"
  }
}
```

#### 9.1.11 Utilities
```json
{
  "dependencies": {
    "lodash": "^4.17.21",
    "currency.js": "^2.0.4",
    "react-number-format": "^5.3.0"
  }
}
```

#### 9.1.12 Development Tools
```json
{
  "devDependencies": {
    "@types/node": "^20.10.0",
    "@types/react": "^18.2.0",
    "@types/react-dom": "^18.2.0",
    "eslint": "^8.56.0",
    "eslint-config-next": "^15.0.0",
    "prettier": "^3.1.0",
    "prettier-plugin-tailwindcss": "^0.5.9",
    "autoprefixer": "^10.4.0",
    "postcss": "^8.4.0"
  }
}
```

### 9.3 Backend Libraries (API Routes)

#### 9.3.1 Validation
```json
{
  "dependencies": {
    "zod": "^3.22.0"
  }
}
```

#### 9.3.2 File Upload & Storage
```json
{
  "dependencies": {
    "uploadthing": "^6.0.0",
    "@uploadthing/react": "^6.0.0",
    "sharp": "^0.33.0"
  }
}
```

**UploadThing Configuration (for file uploads):**
```typescript
// app/api/uploadthing/core.ts
import { createUploadthing, type FileRouter } from "uploadthing/next";
import { getServerSession } from "next-auth";
import { authOptions } from "@/lib/auth";
 
const f = createUploadthing();
 
export const ourFileRouter = {
  transactionAttachment: f({ image: { maxFileSize: "4MB", maxFileCount: 5 } })
    .middleware(async () => {
      const session = await getServerSession(authOptions);
      if (!session?.user) throw new Error("Unauthorized");
      return { userId: session.user.id };
    })
    .onUploadComplete(async ({ metadata, file }) => {
      console.log("Upload complete for userId:", metadata.userId);
      console.log("file url", file.url);
      return { uploadedBy: metadata.userId };
    }),
} satisfies FileRouter;
 
export type OurFileRouter = typeof ourFileRouter;
```

#### 9.3.3 Email
```json
{
  "dependencies": {
    "resend": "^2.0.0",
    "react-email": "^2.0.0",
    "@react-email/components": "^0.0.12"
  }
}
```

**Email Configuration:**
```typescript
// lib/email.ts
import { Resend } from 'resend';
import { WelcomeEmail } from '@/emails/WelcomeEmail';

const resend = new Resend(process.env.RESEND_API_KEY);

export async function sendWelcomeEmail(email: string, name: string) {
  await resend.emails.send({
    from: 'WalletFlow <hello@walletflow.com>',
    to: email,
    subject: 'Welcome to WalletFlow',
    react: WelcomeEmail({ name }),
  });
}
```

#### 9.3.4 CSV Processing
```json
{
  "dependencies": {
    "papaparse": "^5.4.0",
    "@types/papaparse": "^5.3.0"
  }
}
```

#### 9.3.5 Logging & Monitoring
```json
{
  "dependencies": {
    "@vercel/analytics": "^1.1.0",
    "@sentry/nextjs": "^7.88.0"
  }
}
```

**Sentry Configuration:**
```typescript
// sentry.client.config.ts & sentry.server.config.ts
import * as Sentry from "@sentry/nextjs";

Sentry.init({
  dsn: process.env.NEXT_PUBLIC_SENTRY_DSN,
  tracesSampleRate: 1.0,
  environment: process.env.NODE_ENV,
});
```

#### 9.3.6 Background Jobs (Optional)
```json
{
  "dependencies": {
    "@upstash/qstash": "^1.0.0"
  }
}
```

**For recurring transaction creation:**
```typescript
// app/api/cron/recurring-transactions/route.ts
import { Client } from "@upstash/qstash";
import { NextResponse } from "next/server";
import { prisma } from "@/lib/prisma";

const qstash = new Client({ token: process.env.QSTASH_TOKEN! });

export async function GET(request: Request) {
  // Verify cron secret
  if (request.headers.get("Authorization") !== `Bearer ${process.env.CRON_SECRET}`) {
    return NextResponse.json({ error: "Unauthorized" }, { status: 401 });
  }

  // Process recurring transactions
  const dueTransactions = await prisma.recurringTransaction.findMany({
    where: {
      isActive: true,
      nextOccurrence: {
        lte: new Date(),
      },
    },
  });

  for (const recurring of dueTransactions) {
    // Create actual transaction
    await prisma.transaction.create({
      data: {
        userId: recurring.userId,
        accountId: recurring.accountId,
        categoryId: recurring.categoryId,
        type: recurring.type,
        amount: recurring.amount,
        currency: recurring.currency,
        description: recurring.description,
        transactionDate: new Date(),
        recurringTransactionId: recurring.id,
      },
    });

    // Update next occurrence
    // ... calculate next date based on frequency
  }

  return NextResponse.json({ processed: dueTransactions.length });
}
```

### 9.4 Development Tools

#### 9.2.1 Core Framework
```json
{
  "dependencies": {
    "express": "^4.18.0",
    "typescript": "^5.3.0",
    "ts-node": "^10.9.0",
    "dotenv": "^16.3.0"
  }
}
```

#### 9.2.2 Database
```json
{
  "dependencies": {
    "pg": "^8.11.0",
    "pg-promise": "^11.5.0"
  }
}
```

#### 9.2.3 Migration Tools
```json
{
  "dependencies": {
    "node-pg-migrate": "^6.2.0"
  }
}
```

#### 9.2.4 Authentication & Security
```json
{
  "dependencies": {
    "jsonwebtoken": "^9.0.0",
    "bcrypt": "^5.1.0",
    "express-rate-limit": "^7.1.0",
    "helmet": "^7.1.0",
    "cors": "^2.8.5",
    "express-validator": "^7.0.0",
    "passport": "^0.7.0",
    "passport-google-oauth20": "^2.0.0",
    "passport-facebook": "^3.0.0"
  }
}
```

#### 9.2.5 File Upload & Storage
```json
{
  "dependencies": {
    "multer": "^1.4.5-lts.1",
    "aws-sdk": "^2.1500.0",
    "sharp": "^0.33.0"
  }
}
```

#### 9.2.6 Email
```json
{
  "dependencies": {
    "nodemailer": "^6.9.0",
    "@sendgrid/mail": "^8.1.0"
  }
}
```

#### 9.2.7 CSV Processing
```json
{
  "dependencies": {
    "csv-parse": "^5.5.0",
    "csv-stringify": "^6.4.0",
    "papaparse": "^5.4.0"
  }
}
```

#### 9.2.8 Logging
```json
{
  "dependencies": {
    "winston": "^3.11.0",
    "morgan": "^1.10.0"
  }
}
```

#### 9.2.9 Validation
```json
{
  "dependencies": {
    "joi": "^17.11.0",
    "validator": "^13.11.0"
  }
}
```

#### 9.2.10 Testing
```json
{
  "devDependencies": {
    "jest": "^29.7.0",
    "supertest": "^6.3.0",
    "@types/jest": "^29.5.0",
    "@types/supertest": "^6.0.0",
    "ts-jest": "^29.1.0"
  }
}
```

#### 9.2.11 Development Tools
```json
{
  "devDependencies": {
    "nodemon": "^3.0.0",
    "ts-node-dev": "^2.0.0",
    "eslint": "^8.56.0",
    "@typescript-eslint/eslint-plugin": "^6.15.0",
    "@typescript-eslint/parser": "^6.15.0",
    "prettier": "^3.1.0"
  }
}
```

### 9.3 Alternative Backend Stacks

#### 9.3.1 Python (FastAPI)
```txt
fastapi==0.108.0
uvicorn[standard]==0.25.0
sqlalchemy==2.0.23
alembic==1.13.0
psycopg2-binary==2.9.9
pydantic==2.5.0
python-jose[cryptography]==3.3.0
passlib[bcrypt]==1.7.4
python-multipart==0.0.6
pytest==7.4.0
pytest-asyncio==0.21.0
```

#### 9.3.2 Go (Gin)
```go
github.com/gin-gonic/gin
github.com/lib/pq
github.com/golang-migrate/migrate
github.com/golang-jwt/jwt
golang.org/x/crypto/bcrypt
github.com/joho/godotenv
github.com/stretchr/testify
```

### 9.4 Development Tools

```json
{
  "devDependencies": {
    "@types/node": "^20.10.0",
    "@types/react": "^18.2.0",
    "@types/react-dom": "^18.2.0",
    "eslint": "^8.56.0",
    "eslint-config-next": "^15.0.0",
    "prettier": "^3.1.0",
    "prettier-plugin-tailwindcss": "^0.5.9",
    "autoprefixer": "^10.4.0",
    "postcss": "^8.4.0"
  }
}
```

### 9.5 Testing Libraries

```json
{
  "devDependencies": {
    "@testing-library/react": "^14.1.0",
    "@testing-library/jest-dom": "^6.1.0",
    "@testing-library/user-event": "^14.5.0",
    "vitest": "^1.0.0",
    "@vitejs/plugin-react": "^4.2.0",
    "playwright": "^1.40.0",
    "@playwright/test": "^1.40.0"
  }
}
```

### 9.6 Complete package.json Example

```json
{
  "name": "walletflow",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "prisma generate && next build",
    "start": "next start",
    "lint": "next lint",
    "test": "vitest",
    "test:e2e": "playwright test",
    "prisma:generate": "prisma generate",
    "prisma:migrate": "prisma migrate dev",
    "prisma:studio": "prisma studio",
    "prisma:seed": "tsx prisma/seed.ts"
  },
  "dependencies": {
    "next": "^15.0.0",
    "react": "^18.3.0",
    "react-dom": "^18.3.0",
    "typescript": "^5.3.0",
    
    "@prisma/client": "^5.7.0",
    "next-auth": "^5.0.0",
    "bcrypt": "^5.1.0",
    "@auth/prisma-adapter": "^1.0.0",
    
    "@tanstack/react-query": "^5.17.0",
    "zustand": "^4.4.0",
    
    "@radix-ui/react-dialog": "^1.0.5",
    "@radix-ui/react-dropdown-menu": "^2.0.6",
    "@radix-ui/react-select": "^2.0.0",
    "tailwindcss": "^3.4.0",
    "clsx": "^2.1.0",
    "tailwind-merge": "^2.2.0",
    "lucide-react": "^0.300.0",
    
    "react-hook-form": "^7.49.0",
    "zod": "^3.22.0",
    "@hookform/resolvers": "^3.3.0",
    
    "recharts": "^2.10.0",
    "date-fns": "^3.0.0",
    "react-day-picker": "^8.10.0",
    
    "uploadthing": "^6.0.0",
    "@uploadthing/react": "^6.0.0",
    "sharp": "^0.33.0",
    
    "resend": "^2.0.0",
    "react-email": "^2.0.0",
    "@react-email/components": "^0.0.12",
    
    "papaparse": "^5.4.0",
    "react-hot-toast": "^2.4.1",
    
    "@vercel/analytics": "^1.1.0",
    "@sentry/nextjs": "^7.88.0"
  },
  "devDependencies": {
    "@types/node": "^20.10.0",
    "@types/react": "^18.2.0",
    "@types/react-dom": "^18.2.0",
    "@types/bcrypt": "^5.0.0",
    "@types/papaparse": "^5.3.0",
    
    "prisma": "^5.7.0",
    "tsx": "^4.7.0",
    
    "eslint": "^8.56.0",
    "eslint-config-next": "^15.0.0",
    "prettier": "^3.1.0",
    "prettier-plugin-tailwindcss": "^0.5.9",
    
    "@testing-library/react": "^14.1.0",
    "@testing-library/jest-dom": "^6.1.0",
    "vitest": "^1.0.0",
    "@vitejs/plugin-react": "^4.2.0",
    "playwright": "^1.40.0",
    "@playwright/test": "^1.40.0",
    
    "autoprefixer": "^10.4.0",
    "postcss": "^8.4.0"
  }
}
```

### 9.7 Environment Variables

```bash
# .env.local

# Database
DATABASE_URL="postgresql://user:password@localhost:5432/walletflow"

# NextAuth
NEXTAUTH_URL="http://localhost:3000"
NEXTAUTH_SECRET="your-secret-key-here"

# OAuth Providers
GOOGLE_CLIENT_ID="your-google-client-id"
GOOGLE_CLIENT_SECRET="your-google-client-secret"

# File Upload (UploadThing)
UPLOADTHING_SECRET="your-uploadthing-secret"
UPLOADTHING_APP_ID="your-uploadthing-app-id"

# Email (Resend)
RESEND_API_KEY="your-resend-api-key"

# Monitoring
NEXT_PUBLIC_SENTRY_DSN="your-sentry-dsn"
SENTRY_AUTH_TOKEN="your-sentry-auth-token"

# Analytics
NEXT_PUBLIC_VERCEL_ANALYTICS_ID="your-analytics-id"

# Cron Jobs
CRON_SECRET="your-cron-secret"

# Optional: Background Jobs
QSTASH_URL="https://qstash.upstash.io"
QSTASH_TOKEN="your-qstash-token"
```

### 9.8 Database Setup with Prisma

#### Initialize Prisma
```bash
npx prisma init
```

#### Create Migration
```bash
npx prisma migrate dev --name init
```

#### Generate Prisma Client
```bash
npx prisma generate
```

#### Open Prisma Studio (Database GUI)
```bash
npx prisma studio
```

#### Seed Database
```bash
npx prisma db seed
```

**Seed Configuration (package.json):**
```json
{
  "prisma": {
    "seed": "tsx prisma/seed.ts"
  }
}
```

### 9.9 Deployment Options

#### Option 1: Vercel (Recommended)
**Easiest deployment for Next.js applications**

```bash
# Install Vercel CLI
npm i -g vercel

# Deploy
vercel

# Deploy to production
vercel --prod
```

**Environment Variables**: Set in Vercel Dashboard
**Database**: Use Vercel Postgres or external PostgreSQL (Neon, Supabase)
**File Storage**: UploadThing handles storage automatically

#### Option 2: Railway
**Simple deployment with database included**

```bash
# Install Railway CLI
npm i -g @railway/cli

# Login
railway login

# Initialize project
railway init

# Deploy
railway up
```

**Features:**
- Built-in PostgreSQL database
- Automatic HTTPS
- Easy environment variables
- One-click deployments

#### Option 3: Docker + VPS
**Self-hosted option**

```dockerfile
# Dockerfile
FROM node:20-alpine AS base

# Install dependencies only when needed
FROM base AS deps
RUN apk add --no-cache libc6-compat
WORKDIR /app

COPY package.json package-lock.json ./
RUN npm ci

# Rebuild the source code only when needed
FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .

# Generate Prisma Client
RUN npx prisma generate

# Build Next.js
RUN npm run build

# Production image
FROM base AS runner
WORKDIR /app

ENV NODE_ENV production

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

COPY --from=builder /app/public ./public
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

USER nextjs

EXPOSE 3000

ENV PORT 3000

CMD ["node", "server.js"]
```

```yaml
# docker-compose.yml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - DATABASE_URL=postgresql://postgres:password@db:5432/walletflow
      - NEXTAUTH_URL=http://localhost:3000
      - NEXTAUTH_SECRET=${NEXTAUTH_SECRET}
    depends_on:
      - db
    restart: unless-stopped

  db:
    image: postgres:15-alpine
    environment:
      - POSTGRES_DB=walletflow
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    restart: unless-stopped

volumes:
  postgres_data:
```

**Deploy Commands:**
```bash
# Build and start
docker-compose up -d

# View logs
docker-compose logs -f

# Stop
docker-compose down
```

### 9.10 Project Structure

```
walletflow/
├── app/
│   ├── (auth)/
│   │   ├── login/
│   │   │   └── page.tsx
│   │   └── register/
│   │       └── page.tsx
│   ├── (dashboard)/
│   │   ├── layout.tsx
│   │   ├── dashboard/
│   │   │   └── page.tsx
│   │   ├── transactions/
│   │   │   └── page.tsx
│   │   ├── budgets/
│   │   │   └── page.tsx
│   │   └── analytics/
│   │       └── page.tsx
│   ├── api/
│   │   ├── auth/
│   │   │   └── [...nextauth]/
│   │   │       └── route.ts
│   │   ├── users/
│   │   │   └── me/
│   │   │       └── route.ts
│   │   ├── accounts/
│   │   │   ├── route.ts
│   │   │   └── [id]/
│   │   │       └── route.ts
│   │   ├── transactions/
│   │   │   ├── route.ts
│   │   │   ├── [id]/
│   │   │   │   └── route.ts
│   │   │   ├── import/
│   │   │   │   └── route.ts
│   │   │   └── export/
│   │   │       └── route.ts
│   │   ├── budgets/
│   │   │   ├── route.ts
│   │   │   └── [id]/
│   │   │       └── route.ts
│   │   ├── uploadthing/
│   │   │   ├── core.ts
│   │   │   └── route.ts
│   │   └── cron/
│   │       └── recurring-transactions/
│   │           └── route.ts
│   ├── layout.tsx
│   └── page.tsx
├── components/
│   ├── ui/
│   │   ├── Button.tsx
│   │   ├── Input.tsx
│   │   ├── Card.tsx
│   │   └── Modal.tsx
│   ├── transactions/
│   │   ├── TransactionCard.tsx
│   │   ├── TransactionForm.tsx
│   │   └── TransactionList.tsx
│   ├── budgets/
│   │   ├── BudgetCard.tsx
│   │   └── BudgetForm.tsx
│   └── charts/
│       ├── LineChart.tsx
│       └── PieChart.tsx
├── lib/
│   ├── prisma.ts
│   ├── auth.ts
│   ├── utils.ts
│   └── email.ts
├── hooks/
│   ├── useTransactions.ts
│   ├── useBudgets.ts
│   └── useAccounts.ts
├── types/
│   └── index.ts
├── prisma/
│   ├── schema.prisma
│   ├── migrations/
│   └── seed.ts
├── public/
│   ├── icons/
│   └── images/
├── emails/
│   ├── WelcomeEmail.tsx
│   └── BudgetAlertEmail.tsx
├── .env.local
├── .gitignore
├── next.config.js
├── tailwind.config.js
├── tsconfig.json
├── package.json
└── README.md
```

---

## 10. Development Phases

## 10. Development Phases

### Phase 1: Foundation & Setup (Week 1-2)
- [ ] Initialize Next.js 15 project with TypeScript
- [ ] Set up Tailwind CSS with design tokens
- [ ] Configure Prisma with PostgreSQL
- [ ] Set up NextAuth.js authentication
- [ ] Create database schema in Prisma
- [ ] Run initial migrations
- [ ] Set up project structure (folders, files)
- [ ] Configure ESLint, Prettier
- [ ] Set up Git repository and CI/CD (GitHub Actions)
- [ ] Create base layout components (Header, Sidebar, Footer)

### Phase 2: Authentication & User Management (Week 3)
- [ ] Implement registration API route
- [ ] Implement login/logout API routes
- [ ] Add OAuth providers (Google, Facebook)
- [ ] Create login/register pages
- [ ] Implement user profile API routes
- [ ] Create user profile page
- [ ] Add email verification flow
- [ ] Implement password reset
- [ ] Create user settings page
- [ ] Add session management

### Phase 3: Core Features - Accounts & Transactions (Week 4-6)
- [ ] Create accounts API routes (CRUD)
- [ ] Build account management UI
- [ ] Implement account cards and list
- [ ] Create transactions API routes (CRUD)
- [ ] Build transaction form component
- [ ] Implement transaction list with pagination
- [ ] Add transaction filtering and search
- [ ] Create category system (seeders)
- [ ] Build category management UI
- [ ] Implement CSV import/export
- [ ] Add transaction attachments (UploadThing)
- [ ] Create basic dashboard

### Phase 4: Budgets & Goals (Week 7-9)
- [ ] Create budgets API routes
- [ ] Build budget creation form
- [ ] Implement budget progress cards
- [ ] Add budget alerts and notifications
- [ ] Create goals API routes
- [ ] Build goal tracking UI
- [ ] Implement goal progress visualization
- [ ] Add recurring transactions API
- [ ] Build recurring transaction UI
- [ ] Set up cron jobs for recurring transactions
- [ ] Create notification system
- [ ] Implement email notifications (Resend)

### Phase 5: Analytics & Reports (Week 10-12)
- [ ] Create analytics API routes
- [ ] Implement income vs expense chart
- [ ] Build category breakdown pie chart
- [ ] Add spending trends visualization
- [ ] Create net worth tracking
- [ ] Build reports page
- [ ] Add date range filters
- [ ] Implement export to PDF
- [ ] Create financial insights
- [ ] Add spending predictions

### Phase 6: Sharing & Collaboration (Week 13-14)
- [ ] Create account sharing API routes
- [ ] Build sharing invitation flow
- [ ] Implement permission management
- [ ] Add household/group features
- [ ] Create shared budgets
- [ ] Build collaboration UI
- [ ] Add activity notifications

### Phase 7: Polish, Testing & Optimization (Week 15-16)
- [ ] Write unit tests (80% coverage target)
- [ ] Create integration tests
- [ ] Add E2E tests with Playwright
- [ ] Performance optimization
- [ ] Accessibility audit (WCAG AA)
- [ ] SEO optimization
- [ ] Add loading states everywhere
- [ ] Implement error boundaries
- [ ] Add analytics (Vercel Analytics)
- [ ] Set up error tracking (Sentry)
- [ ] Mobile responsive testing
- [ ] Cross-browser testing
- [ ] Security audit
- [ ] Documentation

### Phase 8: Launch Preparation (Week 17+)
- [ ] Beta testing with real users
- [ ] Bug fixes from beta
- [ ] Create onboarding flow
- [ ] Add help documentation
- [ ] Set up production database
- [ ] Configure production environment
- [ ] Deploy to production (Vercel/Railway)
- [ ] Set up monitoring and alerts
- [ ] Create backup strategy
- [ ] Final security review
- [ ] Launch! 🚀

### Development Timeline Summary

```
Week 1-2:   Foundation & Setup
Week 3:     Authentication & User Management  
Week 4-6:   Accounts & Transactions (Core)
Week 7-9:   Budgets & Goals
Week 10-12: Analytics & Reports
Week 13-14: Sharing & Collaboration
Week 15-16: Testing & Optimization
Week 17+:   Beta Testing & Launch
```

### Recommended Development Workflow

1. **Daily Development Loop:**
   ```bash
   # Start development server
   npm run dev
   
   # Open Prisma Studio (database GUI)
   npm run prisma:studio
   
   # Run tests in watch mode
   npm run test
   ```

2. **Database Changes:**
   ```bash
   # Make schema changes in prisma/schema.prisma
   # Create migration
   npm run prisma:migrate
   
   # Generate Prisma Client
   npm run prisma:generate
   ```

3. **Before Commit:**
   ```bash
   # Lint
   npm run lint
   
   # Type check
   npm run type-check
   
   # Run all tests
   npm run test
   ```

4. **Deployment:**
   ```bash
   # Deploy to Vercel
   vercel --prod
   
   # Or push to main branch (automatic deployment)
   git push origin main
   ```

---

## Appendix

### A. API Response Examples

See API Design section for detailed examples.

### B. Database Schema Diagram

Would be represented visually in a tool like dbdiagram.io or draw.io

### C. User Stories

**As a user, I want to:**
- Track my daily expenses so I can see where my money goes
- Set monthly budgets so I can control my spending
- View reports to understand my financial trends
- Share accounts with my partner so we can manage household finances together
- Import transactions from CSV to save time on data entry
- Set financial goals to save for important purchases
- Get alerts when I exceed my budget

### D. Glossary

- **Account**: A financial account (bank account, credit card, cash, etc.)
- **Transaction**: A financial event (income, expense, or transfer)
- **Category**: A classification for transactions
- **Budget**: A spending limit for a specific period
- **Goal**: A savings target with a deadline
- **Recurring Transaction**: An automatic transaction that repeats

---

**Document Version**: 1.0  
**Last Updated**: November 15, 2025  
**Author**: Development Team
