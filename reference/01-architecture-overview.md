# Architecture Overview

## Project Type
**Next.js 16 Full-Stack Application** with App Router, TypeScript, and Prisma ORM

## Tech Stack

### Frontend
- **Framework**: Next.js 16.0.0 (App Router)
- **UI Library**: React 18.3.1
- **Styling**: Bootstrap 5.3.8, React-Bootstrap 2.10.10, Custom CSS
- **State Management**: React Context API (custom contexts)
- **Charts**: Recharts 3.3.0
- **Icons**: React Icons 5.5.0
- **Forms**: React Number Format, React Color
- **Virtualization**: React Window with Infinite Loader
- **Drag & Drop**: @dnd-kit (sortable, core, utilities)
- **Notifications**: SweetAlert2 11.26.3
- **Validation**: Zod 3.23.8

### Backend
- **Runtime**: Next.js API Routes (App Router)
- **Database**: PostgreSQL via Prisma ORM 6.19.0
- **Authentication**: JWT (jose 6.1.0), bcryptjs 3.0.3
- **API Documentation**: Scalar OpenAPI (@scalar/nextjs-openapi)

### Development Tools
- **Language**: TypeScript 5.5.4
- **Testing**: Jest 29.7.0, Testing Library
- **Linting**: ESLint 9.38.0
- **Bundle Analysis**: @next/bundle-analyzer

## Project Structure

```
finance-web/
├── app/                    # Next.js App Router pages & API routes
│   ├── (pages)/           # Page routes
│   ├── api/               # API routes (v1 versioned)
│   ├── components/        # App-level components
│   ├── layout.tsx         # Root layout
│   └── providers.tsx      # Context providers wrapper
│
├── src/                   # Source code
│   ├── components/        # Reusable UI components
│   ├── context/          # React Context providers
│   ├── hooks/            # Custom React hooks
│   ├── lib/              # Backend utilities (auth, db, validation)
│   ├── services/         # API service layer (frontend)
│   ├── types/            # TypeScript type definitions
│   ├── utils/            # Utility functions
│   ├── views/            # Page-level view components
│   └── styles/           # Global CSS
│
├── prisma/               # Database schema & migrations
├── schemas/              # Zod validation schemas
├── __tests__/           # API tests
├── scripts/             # Build & validation scripts
└── public/              # Static assets
```

## Architecture Patterns

### 1. **Layered Architecture**
```
Presentation Layer (Views/Pages)
        ↓
Business Logic Layer (Contexts/Hooks)
        ↓
Service Layer (Services)
        ↓
API Layer (API Routes)
        ↓
Data Layer (Prisma ORM → PostgreSQL)
```

### 2. **App Router Structure**
- **File-based routing**: Each folder in `app/` represents a route
- **Colocation**: Route handlers (`route.ts`) and pages (`page.tsx`) in same directory
- **Layouts**: Nested layouts with `layout.tsx`
- **API Routes**: RESTful API in `app/api/v1/`

### 3. **State Management**
**Context API with Custom Providers:**
- `AuthContext` - Authentication state
- `AuthStateContext` - Auth loading state
- `ToastContext` - Notification system
- `TransactionModalContext` - Transaction modal & operations

### 4. **Code Splitting**
- **Lazy Loading**: Dynamic imports via `React.lazy()` in `LazyRoute.tsx`
- **Route-level splitting**: Each page component is code-split
- **Suspense boundaries**: Loading states with React Suspense

### 5. **Authentication Flow**
```
User Login → AuthService.login()
          ↓
JWT Token Generated (jose)
          ↓
Encrypted & Stored (crypto.ts)
          ↓
RequireAuth HOC checks token
          ↓
Protected routes rendered
```

### 6. **Data Flow**
```
User Action → Context/Hook → Service → API Route → Prisma → PostgreSQL
                                                         ↓
                                           Response ← Schema Validation (Zod)
```

## Key Design Decisions

### 1. **Client-Side Rendering (CSR) Preference**
- Most pages use `'use client'` directive
- Benefits: Rich interactivity, complex state management
- Trade-off: SEO less critical for finance app (authenticated users)

### 2. **Protected Shell Pattern**
```tsx
<RequireAuth>
  <Header />
  <Container>
    {children}
  </Container>
</RequireAuth>
```
- Wraps all authenticated pages
- Handles redirect to login if unauthenticated

### 3. **Service Layer Pattern**
- All API calls abstracted into services (`src/services/`)
- Consistent error handling
- Type-safe with TypeScript interfaces

### 4. **Schema-First API Validation**
- Zod schemas in `schemas/` directory
- Request/response validation
- Type inference from schemas

### 5. **Personal ID Pattern**
- Each user has separate personal_id sequence per resource type
- Allows user-specific numbering (Transaction #1, #2, etc.)
- Server provides max values via `GET /api/v1/personal-ids/max`

## Performance Optimizations

1. **Virtual Scrolling**: React Window for large transaction lists
2. **Memoization**: useMemo/useCallback throughout
3. **Bundle Analysis**: Scripts for analyzing bundle size
4. **Code Splitting**: Lazy-loaded routes
5. **Progressive Web App**: Service Worker registration

## Security Features

1. **JWT Authentication**: Secure token-based auth
2. **Token Encryption**: Client-side token encryption (crypto.ts)
3. **Password Hashing**: bcryptjs on server
4. **CORS Protection**: API route protection
5. **Environment Variables**: Sensitive data in .env files

## Database Design

- **Multi-tenant**: user_id on all tables
- **Soft Deletes**: active/is_active flags
- **Audit Fields**: created_at, updated_at, created_by, updated_by
- **Referential Integrity**: Foreign keys with Prisma relations

## API Design

- **RESTful**: Standard HTTP methods (GET, POST, PUT, DELETE)
- **Versioned**: `/api/v1/` prefix
- **Consistent Responses**: Standardized response format
- **OpenAPI Documentation**: Auto-generated API docs at `/api-docs`

## Testing Strategy

- **Unit Tests**: Jest for utilities and schemas
- **Integration Tests**: API route testing
- **Type Safety**: TypeScript for compile-time checks
- **Schema Validation**: Runtime validation with Zod

## Deployment

- **Platform**: Vercel (vercel.json config)
- **Database**: PostgreSQL (via DATABASE_URL env)
- **Build Process**: `next build` with optional bundle analysis
- **Environments**: Development, Staging, Production configs
