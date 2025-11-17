# Root Layout & App Structure

## File: `app/layout.tsx`

### Purpose
Root layout for the entire Next.js application. Wraps all pages with global providers and components.

### Code Structure

```typescript
import type { ReactNode } from 'react';
import type { Metadata, Viewport } from 'next';

// Global CSS imports
import 'bootstrap/dist/css/bootstrap.min.css';
import '../src/styles/main.css';
import '../src/styles/App.css';

// Core providers and components
import Providers from './providers';
import { ErrorBoundary } from '../src/components/ErrorBoundary';
import { WebVitalsReporter } from '../src/components/WebVitalsReporter';
import { ServiceWorkerRegistration } from '../src/components/ServiceWorkerRegistration';

export default function RootLayout({ children }: { children: ReactNode })
```

### Metadata Configuration

**App Title**: "My Finance Manager"

**Description**: "A comprehensive personal finance management application"

**PWA Configuration**:
- Manifest: `/manifest.json`
- Icons:
  - SVG logo: `/images/logo-image-only.svg`
  - Favicon PNG: `/images/favicon.png`
  - Apple touch icon: `/icon-192.png`

**Viewport Settings**:
- Theme color: `#0d6efd` (Bootstrap primary blue)
- Width: `device-width`
- Initial scale: 1
- Maximum scale: 5 (allows pinch zoom)

### Layout Hierarchy

```
<html lang="en">
  <body>
    <ErrorBoundary>              ← Catches React errors
      <Providers>                ← Context providers
        <div className="App">
          {children}             ← Page content
        </div>
      </Providers>
    </ErrorBoundary>
    <WebVitalsReporter />        ← Performance monitoring
    <ServiceWorkerRegistration /> ← PWA support
  </body>
</html>
```

---

## File: `app/providers.tsx`

### Purpose
Wraps application with React Context providers for global state management.

### Code Structure

```typescript
'use client';

import type { ReactNode } from 'react';
import { ToastProvider } from '../src/context/ToastContext';
import { AuthStateProvider } from '../src/context/AuthStateContext';
import { AuthProvider } from '../src/context/AuthContext';
import { TransactionModalProvider } from '../src/context/TransactionModalContext';

export default function Providers({ children }: { children: ReactNode })
```

### Provider Nesting Order

```
<ToastProvider>                      ← Level 1: Notifications (no dependencies)
  <AuthStateProvider>                ← Level 2: Auth loading state
    <AuthProvider>                   ← Level 3: Auth logic (uses AuthState + Toast)
      <TransactionModalProvider>     ← Level 4: Transaction operations (uses Auth)
        {children}                   ← App pages
      </TransactionModalProvider>
    </AuthProvider>
  </AuthStateProvider>
</ToastProvider>
```

### Provider Details

#### 1. ToastProvider
- **Purpose**: Global notification system
- **Dependencies**: None
- **Provides**: `showToast(message, type)` function
- **Used by**: Auth, API calls, form submissions

#### 2. AuthStateProvider
- **Purpose**: Manages authentication loading state
- **Dependencies**: None
- **Provides**: `isAuthenticated`, `loading` state
- **Used by**: AuthProvider, RequireAuth

#### 3. AuthProvider
- **Purpose**: Authentication logic (login, logout)
- **Dependencies**: ToastProvider, AuthStateProvider
- **Provides**: 
  - `isAuthenticated`: boolean
  - `loading`: boolean
  - `login(response)`: Promise<void>
  - `logout()`: Promise<void>
- **Token Management**: Encrypted storage with crypto.ts

#### 4. TransactionModalProvider
- **Purpose**: Centralized transaction modal and CRUD operations
- **Dependencies**: AuthProvider
- **Provides**:
  - `openTransactionModal(overrides?)`: void
  - `editTransaction(transaction)`: void
  - `closeTransactionModal()`: void
  - `openQuickTransactionModal()`: void
  - `setTransactionDefaults(overrides?)`: void
  - `transactions`: TransactionRecord[]
  - `accountIdToName`: Record<string, string>
  - `accountNameToId`: Record<string, string>

---

## File: `app/RequireAuth.tsx`

### Purpose
Higher-Order Component (HOC) that protects routes from unauthenticated access.

### Code Structure

```typescript
'use client';
import { useEffect, type ReactNode, type JSX } from 'react';
import { useAuth } from '../src/context/AuthContext';
import { usePathname, useRouter } from 'next/navigation';

export default function RequireAuth({ children }: { children: ReactNode })
```

### Authentication Flow

```
User accesses protected page
         ↓
RequireAuth checks isAuthenticated
         ↓
    ┌────┴────┐
   NO        YES
    ↓          ↓
Redirect   Render
to login   children
with ?from=
```

### Features

1. **Loading State**: Shows spinner while checking auth
2. **Redirect with Return URL**: Encodes current path in `?from=` query param
3. **Auto-redirect**: useEffect monitors auth state changes
4. **Loading UI**: Bootstrap spinner with "Loading..." message

### Usage Pattern

Wrapped by `ProtectedShell` component, which adds:
- Header navigation
- Container layout
- RequireAuth protection

---

## File: `app/components/ProtectedShell.tsx`

### Purpose
Reusable shell for all authenticated pages (Header + Container + Auth protection).

### Code Structure

```typescript
'use client';
import type { ReactNode, JSX } from 'react';
import { Container } from 'react-bootstrap';
import Header from '../../src/components/Header';
import RequireAuth from '../RequireAuth';

export default function ProtectedShell({ children }: { children: ReactNode })
```

### Layout Structure

```
<RequireAuth>
  <Header />                     ← Navigation bar
  <Container fluid className="main-container">
    {children}                   ← Page content
  </Container>
</RequireAuth>
```

### Used By
- Dashboard page
- Transactions page
- Accounts page
- Analytics page
- Budgets page
- Reports page
- Settings page
- Account Detail page

### Not Used By
- Login page (public)
- Register page (public)
- Offline page (public)

---

## File: `app/components/LazyRoute.tsx`

### Purpose
Pre-configured lazy-loaded route components for code splitting.

### Code Structure

```typescript
'use client';
import { lazy } from 'react';

export const LazyDashboard = lazy(() => import('../../src/views/Dashboard/Dashboard'));
export const LazyAccounts = lazy(() => import('../../src/views/Accounts/Accounts'));
export const LazyReports = lazy(() => import('../../src/views/Reports/Reports'));
export const LazyAnalytics = lazy(() => import('../../src/views/Analytics/Analytics'));
export const LazySettings = lazy(() => import('../../src/views/settings/Settings'));
export const LazyBudgets = lazy(() => import('../../src/views/Budgets/Budgets'));
export const LazyTransactions = lazy(() => import('../../src/views/Transactions/Transactions'));
```

### Bundle Splitting Strategy

Each lazy component creates a separate JavaScript bundle:
- `Dashboard-[hash].js`
- `Accounts-[hash].js`
- `Transactions-[hash].js`
- etc.

**Benefits**:
- Faster initial page load
- Components loaded only when needed
- Better caching (unchanged components keep same hash)

### Usage Pattern

```typescript
import { Suspense } from 'react';
import { LazyDashboard } from '../components/LazyRoute';

export default function Page() {
  return (
    <ProtectedShell>
      <Suspense fallback={<LoadingFallback />}>
        <LazyDashboard />
      </Suspense>
    </ProtectedShell>
  );
}
```

---

## File: `app/error.tsx`

### Purpose
Error boundary for handling runtime errors in the app router.

### Features
- Catches errors in page components
- Shows user-friendly error message
- Provides retry button
- Logs errors to console

---

## Global CSS Files

### 1. `bootstrap/dist/css/bootstrap.min.css`
- Bootstrap 5.3.8 framework styles
- Imported first for base styling

### 2. `src/styles/main.css`
- Custom global styles
- CSS variables
- Utility classes

### 3. `src/styles/App.css`
- App-specific styles
- Layout overrides
- Component-specific global styles

---

## Progressive Web App (PWA) Setup

### Service Worker Registration
**File**: `src/components/ServiceWorkerRegistration.tsx`

**Features**:
- Registers service worker for offline support
- Caching strategy for static assets
- Background sync capabilities

### Web Vitals Monitoring
**File**: `src/components/WebVitalsReporter.tsx`

**Metrics Tracked**:
- LCP (Largest Contentful Paint)
- FID (First Input Delay)
- CLS (Cumulative Layout Shift)
- FCP (First Contentful Paint)
- TTFB (Time to First Byte)

**Purpose**: Performance monitoring and optimization

---

## Key Considerations for Rewrite

1. **Provider Order Matters**: Dependencies must be respected in nesting
2. **Client Components**: All providers must be client-side ('use client')
3. **Auth Protection**: RequireAuth must wrap all protected content
4. **Code Splitting**: Lazy loading improves initial load time
5. **Error Boundaries**: Catch errors without crashing the app
6. **PWA Support**: Service worker enables offline functionality
7. **Performance**: Web vitals monitoring for optimization
