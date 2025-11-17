# 🤖 AI Agent Implementation Guide - Personal Finance Management Application

## Overview

This guide is specifically designed for AI agents to implement a production-ready personal finance management application similar to BudgetBakers. The application uses Next.js 15+ with PostgreSQL and follows a monolithic architecture with strict REST API separation.

## 📚 Guide Structure

This implementation guide is split into the following documents:

1. **[01_PROJECT_STRUCTURE.md](./01_PROJECT_STRUCTURE.md)** - Complete project structure and setup instructions
   - **[01A_STRICT_CONFIGS.md](./01A_STRICT_CONFIGS.md)** - Strict TypeScript & ESLint configurations for bug prevention
2. **[02_DATABASE_SCHEMA.md](./02_DATABASE_SCHEMA.md)** - Full PostgreSQL database schema with Prisma
3. **[03_AUTHENTICATION_SYSTEM.md](./03_AUTHENTICATION_SYSTEM.md)** - JWT authentication and authorization
4. **[04_API_IMPLEMENTATION.md](./04_API_IMPLEMENTATION.md)** - Complete REST API implementation
5. **[05_FRONTEND_FOUNDATION.md](./05_FRONTEND_FOUNDATION.md)** - Frontend architecture and components
6. **[06_CONTEXT_STATE_MANAGEMENT.md](./06_CONTEXT_STATE_MANAGEMENT.md)** - React Context and state management
7. **[07_DEFAULT_DATA_SEEDING.md](./07_DEFAULT_DATA_SEEDING.md)** - Default categories and accounts
8. **[08_SERVICES_LAYER.md](./08_SERVICES_LAYER.md)** - API service layer implementation
9. **[09_CRITICAL_RULES.md](./09_CRITICAL_RULES.md)** - Critical implementation rules and conventions
10. **[10_IMPLEMENTATION_CHECKLIST.md](./10_IMPLEMENTATION_CHECKLIST.md)** - Validation checklist and common issues

### 🚀 Additional Features (Optional)
11. **[11_BACKUP_RESTORE_FEATURE.md](./11_BACKUP_RESTORE_FEATURE.md)** - Data export/import for backup and restore
12. **[12_THEME_SYSTEM.md](./12_THEME_SYSTEM.md)** - Complete theming system with dark/light/custom modes
    - **[12A_THEME_API_ENDPOINTS.md](./12A_THEME_API_ENDPOINTS.md)** - API endpoints for theme preferences
13. **[13_GOOGLE_SHEETS_INTEGRATION.md](./13_GOOGLE_SHEETS_INTEGRATION.md)** - Bidirectional Google Sheets sync using personal_id as key
    - **[13A_GOOGLE_SHEETS_FRONTEND_SERVICE.md](./13A_GOOGLE_SHEETS_FRONTEND_SERVICE.md)** - Frontend service implementation

## 🎯 Key Requirements

### Technical Stack
- **Frontend**: Next.js 15+, React 19, TypeScript 5.3+
- **UI**: Tailwind CSS 3.4+, Shadcn UI
- **State**: Zustand 4.5+, React Context API
- **Backend**: Next.js API Routes
- **Database**: PostgreSQL 16+, Prisma ORM 5.8+
- **Auth**: JWT (jose library), bcryptjs
- **Validation**: Zod 3.22+

### Architecture Principles
1. **Monolithic with API Separation**: Frontend and backend in same codebase but communicate only via REST APIs
2. **Type Safety**: End-to-end TypeScript with strict mode
3. **Security First**: JWT authentication, encrypted token storage, input validation
4. **User Isolation**: Multi-tenant architecture with user_id filtering
5. **Extensible Design**: New features can be added without breaking existing functionality (see docs 11 & 12 for examples)

### Core Features
- Multi-account management (bank, cash, credit cards)
- Transaction tracking with categories and labels
- Hierarchical category system (70+ default categories)
- Budget management and tracking
- Analytics and reporting
- Transfer between accounts
- Data import/export
- Recurring transactions
- **Data Backup & Restore** (JSON export/import) - Document 11
- **Theme System** (Dark/Light/Custom themes) - Document 12
- **Google Sheets Sync** (Bidirectional sync with personal_id as key) - Document 13

## 🚀 Implementation Phases

### Phase 1: Foundation (Day 1-2)
- Project initialization
- Database setup
- Environment configuration
- Folder structure creation

### Phase 2: Backend Core (Day 3-4)
- JWT authentication system
- Auth endpoints with auto-seeding
- Middleware implementation
- Response builders

### Phase 3: API Endpoints (Day 5-7)
- Account management
- Transaction CRUD with personal_id logic
- Category hierarchy
- Transfer system
- Analytics endpoints

### Phase 4: Frontend Foundation (Day 8-9)
- UI framework setup
- Context providers
- Service layer
- Authentication pages

### Phase 5: Dashboard Features (Day 10-12)
- Dashboard layout
- Account management UI
- Transaction modal
- Category tree
- Analytics charts

### Phase 6: Testing & Polish (Day 13-14)
- Unit testing
- Integration testing
- Performance optimization
- Security audit
- Bug fixes

## ⚠️ Critical Conventions

### 1. Amount Sign Convention
```typescript
// ALWAYS:
// - Expenses: NEGATIVE amounts
// - Income: POSITIVE amounts
const expense = -100.00;  // $100 expense
const income = 500.00;    // $500 income
```

### 2. Personal ID System
Each user has their own sequential numbering:
- User A: Transaction #1, #2, #3...
- User B: Transaction #1, #2, #3...

### 3. Context Provider Order
```tsx
<ToastProvider>           // Level 1
  <AuthStateProvider>     // Level 2
    <AuthProvider>        // Level 3
      <TransactionModalProvider> // Level 4
```

### 4. API Response Format
```typescript
// Success
{ success: true, data: {...}, meta: {...} }

// Error
{ success: false, error: { code: "...", message: "..." } }
```

## 📂 Required File Structure

```
finance-app/
├── app/                    # Next.js App Router
├── src/
│   ├── components/        # UI components
│   ├── context/          # React contexts
│   ├── services/         # API services
│   ├── hooks/           # Custom hooks
│   ├── lib/             # Core utilities
│   ├── types/           # TypeScript types
│   ├── utils/           # Helper functions
│   └── data/            # Static data
├── prisma/              # Database
├── public/              # Static assets
└── tests/               # Test files
```

## 🔍 How to Use This Guide

1. **Start with Document 01**: Read the project structure guide first
2. **Follow Sequentially**: Each document builds on the previous
3. **Copy Exactly**: Use the exact code provided, don't improvise
4. **Test Each Phase**: Verify each implementation phase before proceeding
5. **Check Document 09**: Review critical rules before implementing each feature
6. **Use Document 10**: Validate your implementation against the checklist

## 🎯 Success Criteria

The implementation is successful when:
- All 13 database tables are created correctly
- User registration creates 70+ categories and 3 default accounts
- JWT authentication works with encrypted token storage
- All CRUD operations follow REST conventions
- Transactions use proper amount sign convention
- Transfers create two linked transactions
- Personal IDs are user-specific and sequential
- All contexts work in the correct hierarchy
- Analytics calculate accurately
- The app is production-ready

## 🆘 Support References

- [Next.js 15 Documentation](https://nextjs.org/docs)
- [Prisma Documentation](https://www.prisma.io/docs)
- [Shadcn UI Components](https://ui.shadcn.com)
- [Tailwind CSS](https://tailwindcss.com/docs)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook)

## 📝 Important Notes

1. **Follow Exactly**: Do not optimize or improvise unless fixing errors
2. **TypeScript Strict**: Use strict mode, avoid 'any' types
3. **Error Handling**: Implement at every level
4. **Testing**: Test with real data, not just mocks
5. **Documentation**: Document any necessary deviations

---

**Start with [01_PROJECT_STRUCTURE.md](./01_PROJECT_STRUCTURE.md) to begin implementation.**
