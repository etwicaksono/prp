# Finance App - Complete Project Documentation

**Project**: My Finance Manager  
**Type**: Next.js 16 Full-Stack Application  
**Purpose**: Personal finance management with accounts, transactions, budgets, and analytics  
**Generated**: 2025-11-15

---

## 📋 Table of Contents

1. [Quick Overview](#quick-overview)
2. [Documentation Files](#documentation-files)
3. [Project Structure](#project-structure)
4. [Technology Stack](#technology-stack)
5. [Core Features](#core-features)
6. [Key Concepts](#key-concepts)

---

## Quick Overview

### What is This App?

A comprehensive personal finance management application that allows users to:
- Track multiple accounts (bank accounts, cash, credit cards)
- Record income and expense transactions
- Categorize transactions hierarchically
- Create budgets and track spending
- View analytics with charts and reports
- Tag transactions with labels
- Transfer money between accounts
- Manage financial data offline (PWA)

### Architecture at a Glance

```
Frontend (React 18 + Next.js 16 App Router)
    ↓
Context API (Global State: Auth, Toast, Transaction Modal)
    ↓
Service Layer (Abstracted API calls)
    ↓
API Routes (/api/v1/*) - RESTful endpoints
    ↓
Prisma ORM
    ↓
PostgreSQL Database
```

---

## Documentation Files

### Core Architecture
- **[01-architecture-overview.md](./01-architecture-overview.md)** - Project structure, tech stack, design patterns
- **[02-database-schema.md](./02-database-schema.md)** - Complete database schema with all 9 tables
- **[03-api-routes.md](./03-api-routes.md)** - All REST API endpoints with request/response formats

### Application Code
- **[04-contexts-and-state.md](./04-contexts-and-state.md)** - React Context providers and state management
- **[05-services-layer.md](./05-services-layer.md)** - Service layer for API communication

### Pages
- **[pages/00-root-layout.md](./pages/00-root-layout.md)** - Root layout, providers, authentication wrapper
- **[pages/01-dashboard-page.md](./pages/01-dashboard-page.md)** - Dashboard with customizable widgets
- **[pages/02-accounts-page.md](./pages/02-accounts-page.md)** - Accounts list with drag-and-drop reordering
- **[pages/03-reports-page.md](./pages/03-reports-page.md)** - Reports & Analytics with customizable widgets
- **[pages/04-analytics-page.md](./pages/04-analytics-page.md)** - Analytics with tabs, filters, and drill-down
- **[pages/05-settings-page.md](./pages/05-settings-page.md)** - Settings with sidebar navigation and URL routing
- **[pages/06-budgets-page.md](./pages/06-budgets-page.md)** - Budgets with progress tracking (prototype/needs API)
- **[pages/07-transactions-page.md](./pages/07-transactions-page.md)** - Core transaction management (most complex)

### Components
- **[components-overview.md](./components-overview.md)** - Complete component library analysis (22 components)
- **[components-usage-guide.md](./components-usage-guide.md)** - How to use components, behaviors, and interactions

### Configuration
- **[config-summary.md](./config-summary.md)** - Application configuration, environment variables, and settings

### Contexts
- **[context-summary.md](./context-summary.md)** - React Context providers for global state management (4 contexts)

### API Routes
- **[api-v1-summary.md](./api-v1-summary.md)** - Complete REST API documentation (23 endpoints, auto data seeding)

---

## Key Documentation Index

Follow these documents in order for complete understanding:

1. **Start Here**: `01-architecture-overview.md` - Understand the overall architecture
2. **Database**: `02-database-schema.md` - Learn the data model
3. **API**: `03-api-routes.md` - Understand all endpoints
4. **State**: `04-contexts-and-state.md` - Global state management
5. **Services**: `05-services-layer.md` - API communication layer
6. **Pages**: `pages/` directory - Individual page documentation

---

## Project Structure (Simplified)

```
finance-web/
├── app/                          # Next.js App Router
│   ├── layout.tsx                # Root layout
│   ├── providers.tsx             # Context providers
│   ├── (pages)/                  # All page routes
│   └── api/v1/                   # API endpoints
│
├── src/
│   ├── components/               # Reusable UI components
│   ├── context/                  # React Context providers
│   ├── services/                 # API service layer
│   ├── views/                    # Page-level components
│   ├── hooks/                    # Custom React hooks
│   ├── utils/                    # Utility functions
│   ├── types/                    # TypeScript types
│   ├── lib/                      # Backend utilities
│   └── styles/                   # CSS files
│
├── prisma/
│   ├── schema.prisma             # Database schema
│   └── migrations/               # DB migrations
│
└── schemas/                      # Zod validation schemas
```

---

## Core Entities

### 1. Users
- Authentication (JWT)
- Multi-tenant isolation
- Each user has separate data

### 2. Accounts
- Bank accounts, cash, credit cards
- Custom icons, colors
- Initial balance
- Active/archived status

### 3. Categories
- Hierarchical (parent/child)
- Income vs Expense
- Custom icons, colors

### 4. Transactions
- Income or Expense
- Linked to account + category
- Labels (many-to-many)
- Payment type/status
- Payer tracking

### 5. Transfers
- Money between accounts
- Currency conversion
- Creates 2 transactions

### 6. Labels
- Tags for transactions
- Color-coded
- Many-to-many relationship

### 7. Budgets
- Per category
- Progress tracking
- Alerts

---

## Key Concepts

### Personal ID Pattern
- User-specific sequential IDs
- Format: Transaction #1, #2, #3 (per user)
- Unique constraint: (user_id, personal_id)
- Server provides max values: `GET /api/v1/personal-ids/max`

### Amount Convention
- Negative = Expense
- Positive = Income
- Single amount field in DB

### Transaction Modal Context
- Centralized transaction CRUD
- Handles create, edit, delete
- Retry logic for conflicts
- Dynamic category creation

### Authentication Flow
1. Login → JWT tokens
2. Encrypt → localStorage
3. Decrypt → API header
4. Refresh when expired

---

## Technology Stack Summary

**Frontend**: React 18 + Next.js 16 + TypeScript  
**UI**: Bootstrap 5 + React-Bootstrap  
**State**: React Context API  
**Backend**: Next.js API Routes  
**Database**: PostgreSQL + Prisma ORM  
**Auth**: JWT (jose) + bcryptjs  
**Validation**: Zod schemas  
**Charts**: Recharts  
**Testing**: Jest + Testing Library  

---

## Getting Started

```bash
# Install dependencies
npm install

# Setup database
npx prisma generate
npx prisma migrate deploy

# Start development
npm run dev
```

---

## For the Next AI Agent

**Mission**: Rewrite this entire application

**Follow this order**:
1. Read all documentation files in `docs/rewrite/`
2. Understand database schema (`02-database-schema.md`)
3. Study API routes (`03-api-routes.md`)
4. Learn state management (`04-contexts-and-state.md`)
5. Review service layer (`05-services-layer.md`)
6. Examine page implementations (`pages/` directory)

**Critical Concepts**:
- Personal ID pattern with retry logic
- Transaction amount convention (signed numbers)
- Context provider hierarchy (order matters!)
- Token encryption for security
- Custom events for cross-component updates

**Start Implementation**:
1. Set up Next.js 16 + TypeScript
2. Configure Prisma + PostgreSQL
3. Implement authentication first
4. Build API routes (follow schemas)
5. Create service layer
6. Implement contexts
7. Build pages one by one

---

**Document Status**: ✅ Complete  
**Ready for Rewrite**: Yes  
**Last Updated**: 2025-11-15
