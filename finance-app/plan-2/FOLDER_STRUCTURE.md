# WalletFlow - Folder Structure Guide

**Version:** 1.0.0
**Last Updated:** November 2025

---

## Table of Contents

1. [Overview](#overview)
2. [Frontend Structure (Next.js)](#frontend-structure-nextjs)
3. [Backend Structure (Node.js)](#backend-structure-nodejs)
4. [Backend Structure (Python)](#backend-structure-python)
5. [Backend Structure (Go)](#backend-structure-go)
6. [Shared Configuration](#shared-configuration)
7. [File Naming Conventions](#file-naming-conventions)

---

## 1. Overview

This document describes the complete folder structure for both frontend and backend implementations of WalletFlow.

### 1.1 Repository Structure

```
walletflow/
├── frontend/              # Next.js 15+ frontend application
├── backend-node/          # Node.js + Express backend (Option 1)
├── backend-python/        # Python + FastAPI backend (Option 2)
├── backend-go/            # Go + Gin backend (Option 3)
├── docker-compose.yml     # Multi-container setup
├── .env.example           # Environment variables template
└── README.md              # Project documentation
```

---

## 2. Frontend Structure (Next.js)

### 2.1 Complete Directory Tree

```
frontend/
├── .next/                           # Next.js build output (gitignored)
├── node_modules/                    # Dependencies (gitignored)
├── public/                          # Static assets
│   ├── images/
│   │   ├── logo.svg
│   │   ├── hero-bg.jpg
│   │   └── icons/
│   │       ├── apple-touch-icon.png
│   │       └── favicon.ico
│   ├── fonts/
│   │   ├── inter-var.woff2
│   │   └── jetbrains-mono.woff2
│   └── manifest.json               # PWA manifest
│
├── src/
│   ├── app/                         # Next.js 15 App Router
│   │   ├── (auth)/                  # Auth route group (different layout)
│   │   │   ├── login/
│   │   │   │   └── page.tsx
│   │   │   ├── register/
│   │   │   │   └── page.tsx
│   │   │   ├── reset-password/
│   │   │   │   └── page.tsx
│   │   │   ├── verify-email/
│   │   │   │   └── page.tsx
│   │   │   └── layout.tsx          # Auth layout (centered, no sidebar)
│   │   │
│   │   ├── (dashboard)/             # Dashboard route group (main layout)
│   │   │   ├── dashboard/
│   │   │   │   └── page.tsx
│   │   │   ├── accounts/
│   │   │   │   ├── page.tsx
│   │   │   │   ├── [id]/
│   │   │   │   │   └── page.tsx
│   │   │   │   └── new/
│   │   │   │       └── page.tsx
│   │   │   ├── transactions/
│   │   │   │   ├── page.tsx
│   │   │   │   ├── [id]/
│   │   │   │   │   └── page.tsx
│   │   │   │   └── new/
│   │   │   │       └── page.tsx
│   │   │   ├── budgets/
│   │   │   │   ├── page.tsx
│   │   │   │   ├── [id]/
│   │   │   │   │   └── page.tsx
│   │   │   │   └── new/
│   │   │   │       └── page.tsx
│   │   │   ├── goals/
│   │   │   │   ├── page.tsx
│   │   │   │   └── [id]/
│   │   │   │       └── page.tsx
│   │   │   ├── reports/
│   │   │   │   └── page.tsx
│   │   │   ├── analytics/
│   │   │   │   └── page.tsx
│   │   │   ├── settings/
│   │   │   │   ├── page.tsx
│   │   │   │   ├── profile/
│   │   │   │   │   └── page.tsx
│   │   │   │   ├── security/
│   │   │   │   │   └── page.tsx
│   │   │   │   ├── preferences/
│   │   │   │   │   └── page.tsx
│   │   │   │   └── categories/
│   │   │   │       └── page.tsx
│   │   │   └── layout.tsx           # Dashboard layout (sidebar, header)
│   │   │
│   │   ├── auth/
│   │   │   └── callback/
│   │   │       └── page.tsx         # OAuth callback handler
│   │   │
│   │   ├── api/                     # API routes (optional, for BFF pattern)
│   │   │   └── health/
│   │   │       └── route.ts
│   │   │
│   │   ├── layout.tsx               # Root layout
│   │   ├── page.tsx                 # Home page (landing)
│   │   ├── loading.tsx              # Root loading state
│   │   ├── error.tsx                # Root error boundary
│   │   └── not-found.tsx            # 404 page
│   │
│   ├── components/                  # React components
│   │   ├── ui/                      # Base UI components (shadcn/ui style)
│   │   │   ├── Button.tsx
│   │   │   ├── Input.tsx
│   │   │   ├── Select.tsx
│   │   │   ├── Modal.tsx
│   │   │   ├── Card.tsx
│   │   │   ├── Badge.tsx
│   │   │   ├── Avatar.tsx
│   │   │   ├── Dropdown.tsx
│   │   │   ├── Table.tsx
│   │   │   ├── Tabs.tsx
│   │   │   ├── Toast.tsx
│   │   │   └── Skeleton.tsx
│   │   │
│   │   ├── forms/                   # Form components
│   │   │   ├── LoginForm.tsx
│   │   │   ├── RegisterForm.tsx
│   │   │   ├── TransactionForm.tsx
│   │   │   ├── AccountForm.tsx
│   │   │   ├── BudgetForm.tsx
│   │   │   └── CategoryForm.tsx
│   │   │
│   │   ├── charts/                  # Chart components
│   │   │   ├── PieChart.tsx
│   │   │   ├── LineChart.tsx
│   │   │   ├── BarChart.tsx
│   │   │   └── AreaChart.tsx
│   │   │
│   │   ├── features/                # Feature-specific components
│   │   │   ├── dashboard/
│   │   │   │   ├── BalanceOverview.tsx
│   │   │   │   ├── RecentTransactions.tsx
│   │   │   │   ├── BudgetProgress.tsx
│   │   │   │   └── QuickActions.tsx
│   │   │   ├── accounts/
│   │   │   │   ├── AccountCard.tsx
│   │   │   │   ├── AccountList.tsx
│   │   │   │   └── AccountBalanceChart.tsx
│   │   │   ├── transactions/
│   │   │   │   ├── TransactionList.tsx
│   │   │   │   ├── TransactionItem.tsx
│   │   │   │   ├── TransactionFilters.tsx
│   │   │   │   └── TransactionStats.tsx
│   │   │   └── budgets/
│   │   │       ├── BudgetCard.tsx
│   │   │       ├── BudgetProgressBar.tsx
│   │   │       └── BudgetAlert.tsx
│   │   │
│   │   ├── layout/                  # Layout components
│   │   │   ├── Header.tsx
│   │   │   ├── Sidebar.tsx
│   │   │   ├── Footer.tsx
│   │   │   ├── MobileNav.tsx
│   │   │   └── Breadcrumbs.tsx
│   │   │
│   │   └── shared/                  # Shared components
│   │       ├── LoadingSpinner.tsx
│   │       ├── ErrorMessage.tsx
│   │       ├── EmptyState.tsx
│   │       ├── Pagination.tsx
│   │       ├── SearchBar.tsx
│   │       └── DatePicker.tsx
│   │
│   ├── lib/                         # Utility libraries
│   │   ├── api-client.ts            # Axios instance with interceptors
│   │   ├── api/                     # API functions
│   │   │   ├── auth.ts
│   │   │   ├── accounts.ts
│   │   │   ├── transactions.ts
│   │   │   ├── budgets.ts
│   │   │   ├── goals.ts
│   │   │   └── analytics.ts
│   │   ├── utils.ts                 # General utilities
│   │   ├── constants.ts             # App constants
│   │   ├── validators.ts            # Custom validation functions
│   │   └── formatters.ts            # Currency, date formatters
│   │
│   ├── hooks/                       # Custom React hooks
│   │   ├── useAuth.ts
│   │   ├── useAccounts.ts
│   │   ├── useTransactions.ts
│   │   ├── useBudgets.ts
│   │   ├── useDebounce.ts
│   │   ├── useLocalStorage.ts
│   │   └── useMediaQuery.ts
│   │
│   ├── contexts/                    # React contexts
│   │   ├── AuthContext.tsx
│   │   ├── ThemeContext.tsx
│   │   └── ToastContext.tsx
│   │
│   ├── stores/                      # Zustand stores
│   │   ├── authStore.ts
│   │   ├── accountsStore.ts
│   │   ├── filtersStore.ts
│   │   └── uiStore.ts
│   │
│   ├── types/                       # TypeScript types
│   │   ├── index.ts                 # Re-exports
│   │   ├── auth.types.ts
│   │   ├── account.types.ts
│   │   ├── transaction.types.ts
│   │   ├── budget.types.ts
│   │   ├── goal.types.ts
│   │   ├── category.types.ts
│   │   └── api.types.ts
│   │
│   ├── styles/                      # Global styles
│   │   ├── globals.css              # Tailwind imports + global styles
│   │   ├── variables.css            # CSS variables (design tokens)
│   │   └── animations.css           # Custom animations
│   │
│   └── config/                      # Configuration files
│       ├── routes.ts                # Route constants
│       ├── permissions.ts           # Permission definitions
│       └── api-endpoints.ts         # API endpoint constants
│
├── .env.local                       # Environment variables (gitignored)
├── .env.example                     # Environment template
├── .eslintrc.json                   # ESLint configuration
├── .prettierrc                      # Prettier configuration
├── .gitignore                       # Git ignore rules
├── next.config.js                   # Next.js configuration
├── tailwind.config.js               # Tailwind CSS configuration
├── postcss.config.js                # PostCSS configuration
├── tsconfig.json                    # TypeScript configuration
├── package.json                     # NPM dependencies
├── package-lock.json                # NPM lockfile
└── README.md                        # Frontend documentation
```

### 2.2 Key Directory Explanations

#### `/app` Directory
- **App Router**: Next.js 15's file-based routing
- **Route Groups**: `(auth)` and `(dashboard)` for different layouts
- **Dynamic Routes**: `[id]` for parameterized routes
- **Layouts**: Each group has its own layout

#### `/components` Directory
- **ui/**: Base, reusable UI components (design system)
- **forms/**: Form components with validation
- **features/**: Feature-specific composed components
- **layout/**: Navigation and layout components
- **shared/**: General-purpose components

#### `/lib` Directory
- **api-client.ts**: Configured Axios instance
- **api/**: API function wrappers
- **utils.ts**: Helper functions

#### `/hooks` Directory
- Custom React hooks for reusable logic
- React Query hooks for data fetching

#### `/stores` Directory
- Zustand stores for client-side state
- Separate from React Query (server state)

---

## 3. Backend Structure (Node.js)

### 3.1 Complete Directory Tree

```
backend-node/
├── node_modules/                    # Dependencies (gitignored)
├── dist/                            # Compiled JavaScript (gitignored)
│
├── src/
│   ├── controllers/                 # Request handlers
│   │   ├── auth.controller.ts
│   │   ├── users.controller.ts
│   │   ├── accounts.controller.ts
│   │   ├── transactions.controller.ts
│   │   ├── categories.controller.ts
│   │   ├── budgets.controller.ts
│   │   ├── goals.controller.ts
│   │   ├── transfers.controller.ts
│   │   ├── labels.controller.ts
│   │   ├── groups.controller.ts
│   │   ├── debts.controller.ts
│   │   ├── analytics.controller.ts
│   │   └── notifications.controller.ts
│   │
│   ├── routes/                      # API routes
│   │   ├── index.ts                 # Main router
│   │   ├── auth.routes.ts
│   │   ├── users.routes.ts
│   │   ├── accounts.routes.ts
│   │   ├── transactions.routes.ts
│   │   ├── categories.routes.ts
│   │   ├── budgets.routes.ts
│   │   ├── goals.routes.ts
│   │   ├── transfers.routes.ts
│   │   ├── labels.routes.ts
│   │   ├── groups.routes.ts
│   │   ├── debts.routes.ts
│   │   ├── analytics.routes.ts
│   │   └── notifications.routes.ts
│   │
│   ├── middleware/                  # Express middleware
│   │   ├── auth.middleware.ts       # JWT authentication
│   │   ├── validate.middleware.ts   # Request validation
│   │   ├── error.middleware.ts      # Error handling
│   │   ├── cors.middleware.ts       # CORS configuration
│   │   ├── rateLimit.middleware.ts  # Rate limiting
│   │   ├── logger.middleware.ts     # Request logging
│   │   └── upload.middleware.ts     # File upload handling
│   │
│   ├── services/                    # Business logic
│   │   ├── auth.service.ts
│   │   ├── user.service.ts
│   │   ├── account.service.ts
│   │   ├── transaction.service.ts
│   │   ├── category.service.ts
│   │   ├── budget.service.ts
│   │   ├── goal.service.ts
│   │   ├── transfer.service.ts
│   │   ├── analytics.service.ts
│   │   ├── email.service.ts
│   │   ├── notification.service.ts
│   │   └── session.service.ts
│   │
│   ├── models/                      # Database models (if not using Prisma)
│   │   └── index.ts
│   │
│   ├── schemas/                     # Validation schemas (Zod)
│   │   ├── auth.schema.ts
│   │   ├── user.schema.ts
│   │   ├── account.schema.ts
│   │   ├── transaction.schema.ts
│   │   ├── category.schema.ts
│   │   ├── budget.schema.ts
│   │   └── goal.schema.ts
│   │
│   ├── utils/                       # Utility functions
│   │   ├── jwt.ts                   # JWT utilities
│   │   ├── bcrypt.ts                # Password hashing
│   │   ├── validators.ts            # Custom validators
│   │   ├── formatters.ts            # Data formatters
│   │   ├── errors.ts                # Custom error classes
│   │   ├── constants.ts             # App constants
│   │   └── helpers.ts               # General helpers
│   │
│   ├── lib/                         # Third-party integrations
│   │   ├── prisma.ts                # Prisma client
│   │   ├── redis.ts                 # Redis client
│   │   ├── s3.ts                    # AWS S3 client
│   │   ├── email.ts                 # Email service client
│   │   └── oauth.ts                 # OAuth clients
│   │
│   ├── types/                       # TypeScript types
│   │   ├── index.d.ts               # Type declarations
│   │   ├── express.d.ts             # Express type extensions
│   │   ├── auth.types.ts
│   │   ├── api.types.ts
│   │   └── models.types.ts
│   │
│   ├── config/                      # Configuration
│   │   ├── index.ts                 # Main config export
│   │   ├── database.ts              # Database config
│   │   ├── redis.ts                 # Redis config
│   │   ├── email.ts                 # Email config
│   │   ├── oauth.ts                 # OAuth config
│   │   └── cors.ts                  # CORS config
│   │
│   ├── database/                    # Database-related files
│   │   ├── migrations/              # Prisma migrations
│   │   ├── seeds/                   # Database seeders
│   │   │   ├── users.seed.ts
│   │   │   ├── categories.seed.ts
│   │   │   └── index.ts
│   │   └── schema.prisma            # Prisma schema
│   │
│   ├── tests/                       # Tests
│   │   ├── unit/
│   │   │   ├── services/
│   │   │   │   ├── auth.service.test.ts
│   │   │   │   └── account.service.test.ts
│   │   │   └── utils/
│   │   │       └── jwt.test.ts
│   │   ├── integration/
│   │   │   ├── auth.test.ts
│   │   │   ├── accounts.test.ts
│   │   │   └── transactions.test.ts
│   │   ├── e2e/
│   │   │   └── user-flow.test.ts
│   │   └── setup.ts                 # Test setup
│   │
│   ├── docs/                        # API documentation
│   │   ├── openapi.yaml             # OpenAPI spec
│   │   └── swagger.ts               # Swagger setup
│   │
│   ├── scripts/                     # Utility scripts
│   │   ├── seed.ts                  # Database seeding
│   │   ├── migrate.ts               # Run migrations
│   │   └── generate-keys.ts         # Generate JWT keys
│   │
│   ├── server.ts                    # Server setup
│   └── app.ts                       # Express app configuration
│
├── .env                             # Environment variables (gitignored)
├── .env.example                     # Environment template
├── .eslintrc.json                   # ESLint configuration
├── .prettierrc                      # Prettier configuration
├── .gitignore                       # Git ignore rules
├── tsconfig.json                    # TypeScript configuration
├── jest.config.js                   # Jest configuration
├── package.json                     # NPM dependencies
├── package-lock.json                # NPM lockfile
├── Dockerfile                       # Docker configuration
├── .dockerignore                    # Docker ignore rules
└── README.md                        # Backend documentation
```

### 3.2 Key Files

#### `src/server.ts`
```typescript
import app from './app';
import config from './config';

const PORT = config.port || 3001;

app.listen(PORT, () => {
  console.log(`🚀 Server running on port ${PORT}`);
  console.log(`📚 API docs: http://localhost:${PORT}/docs`);
});
```

#### `src/app.ts`
```typescript
import express from 'express';
import helmet from 'helmet';
import cors from 'cors';
import routes from './routes';
import { errorMiddleware } from './middleware/error.middleware';
import { loggerMiddleware } from './middleware/logger.middleware';

const app = express();

// Security
app.use(helmet());
app.use(cors(corsOptions));

// Body parsing
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Logging
app.use(loggerMiddleware);

// API routes
app.use('/api/v1', routes);

// Error handling
app.use(errorMiddleware);

export default app;
```

#### `src/routes/index.ts`
```typescript
import { Router } from 'express';
import authRoutes from './auth.routes';
import usersRoutes from './users.routes';
import accountsRoutes from './accounts.routes';
// ... other routes

const router = Router();

router.use('/auth', authRoutes);
router.use('/users', usersRoutes);
router.use('/accounts', accountsRoutes);
// ... other routes

export default router;
```

---

## 4. Backend Structure (Python)

### 4.1 Complete Directory Tree

```
backend-python/
├── venv/                            # Virtual environment (gitignored)
│
├── app/
│   ├── api/                         # API routes
│   │   ├── v1/
│   │   │   ├── endpoints/
│   │   │   │   ├── auth.py
│   │   │   │   ├── users.py
│   │   │   │   ├── accounts.py
│   │   │   │   ├── transactions.py
│   │   │   │   ├── budgets.py
│   │   │   │   ├── goals.py
│   │   │   │   └── analytics.py
│   │   │   └── api.py               # API router
│   │   └── deps.py                  # Dependencies (auth, DB)
│   │
│   ├── core/                        # Core functionality
│   │   ├── config.py                # Configuration
│   │   ├── security.py              # Security utilities
│   │   ├── jwt.py                   # JWT handling
│   │   └── database.py              # Database connection
│   │
│   ├── models/                      # SQLAlchemy models
│   │   ├── user.py
│   │   ├── account.py
│   │   ├── transaction.py
│   │   ├── category.py
│   │   ├── budget.py
│   │   └── goal.py
│   │
│   ├── schemas/                     # Pydantic schemas
│   │   ├── auth.py
│   │   ├── user.py
│   │   ├── account.py
│   │   ├── transaction.py
│   │   ├── budget.py
│   │   └── goal.py
│   │
│   ├── services/                    # Business logic
│   │   ├── auth_service.py
│   │   ├── user_service.py
│   │   ├── account_service.py
│   │   ├── transaction_service.py
│   │   ├── budget_service.py
│   │   ├── email_service.py
│   │   └── analytics_service.py
│   │
│   ├── middleware/                  # FastAPI middleware
│   │   ├── auth.py
│   │   ├── cors.py
│   │   ├── error_handler.py
│   │   └── rate_limit.py
│   │
│   ├── utils/                       # Utilities
│   │   ├── validators.py
│   │   ├── formatters.py
│   │   ├── errors.py
│   │   └── constants.py
│   │
│   ├── db/                          # Database
│   │   ├── migrations/              # Alembic migrations
│   │   ├── seeds/                   # Database seeders
│   │   └── base.py                  # Base model
│   │
│   ├── tests/                       # Tests
│   │   ├── unit/
│   │   ├── integration/
│   │   └── conftest.py              # Pytest fixtures
│   │
│   ├── main.py                      # FastAPI app
│   └── __init__.py
│
├── alembic/                         # Alembic configuration
│   ├── versions/
│   └── env.py
│
├── .env                             # Environment variables (gitignored)
├── .env.example                     # Environment template
├── .gitignore                       # Git ignore rules
├── requirements.txt                 # Python dependencies
├── requirements-dev.txt             # Dev dependencies
├── pytest.ini                       # Pytest configuration
├── alembic.ini                      # Alembic configuration
├── Dockerfile                       # Docker configuration
└── README.md                        # Backend documentation
```

### 4.2 Key Files

#### `app/main.py`
```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from app.api.v1.api import api_router
from app.core.config import settings

app = FastAPI(
    title="WalletFlow API",
    version="1.0.0",
    docs_url="/docs",
    redoc_url="/redoc"
)

# CORS
app.add_middleware(
    CORSMiddleware,
    allow_origins=[settings.FRONTEND_URL],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# API routes
app.include_router(api_router, prefix="/api/v1")

@app.get("/")
def root():
    return {"message": "WalletFlow API v1.0.0"}
```

#### `app/core/config.py`
```python
from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    PROJECT_NAME: str = "WalletFlow"
    VERSION: str = "1.0.0"
    API_V1_STR: str = "/api/v1"

    DATABASE_URL: str
    REDIS_URL: str

    JWT_SECRET_KEY: str
    JWT_ALGORITHM: str = "HS256"
    ACCESS_TOKEN_EXPIRE_MINUTES: int = 15
    REFRESH_TOKEN_EXPIRE_DAYS: int = 7

    FRONTEND_URL: str

    class Config:
        env_file = ".env"

settings = Settings()
```

---

## 5. Backend Structure (Go)

### 5.1 Complete Directory Tree

```
backend-go/
├── cmd/
│   └── server/
│       └── main.go                  # Entry point
│
├── internal/
│   ├── api/                         # API handlers
│   │   ├── v1/
│   │   │   ├── auth.go
│   │   │   ├── users.go
│   │   │   ├── accounts.go
│   │   │   ├── transactions.go
│   │   │   ├── budgets.go
│   │   │   └── router.go
│   │   └── middleware/
│   │       ├── auth.go
│   │       ├── cors.go
│   │       ├── logger.go
│   │       └── error.go
│   │
│   ├── models/                      # GORM models
│   │   ├── user.go
│   │   ├── account.go
│   │   ├── transaction.go
│   │   ├── category.go
│   │   └── budget.go
│   │
│   ├── services/                    # Business logic
│   │   ├── auth_service.go
│   │   ├── user_service.go
│   │   ├── account_service.go
│   │   ├── transaction_service.go
│   │   └── email_service.go
│   │
│   ├── repository/                  # Database layer
│   │   ├── user_repository.go
│   │   ├── account_repository.go
│   │   └── transaction_repository.go
│   │
│   ├── dto/                         # Data Transfer Objects
│   │   ├── auth_dto.go
│   │   ├── user_dto.go
│   │   ├── account_dto.go
│   │   └── transaction_dto.go
│   │
│   ├── utils/                       # Utilities
│   │   ├── jwt.go
│   │   ├── bcrypt.go
│   │   ├── validators.go
│   │   └── errors.go
│   │
│   └── config/                      # Configuration
│       └── config.go
│
├── pkg/                             # Public packages
│   └── logger/
│       └── logger.go
│
├── migrations/                      # Database migrations
│   ├── 000001_create_users_table.up.sql
│   ├── 000001_create_users_table.down.sql
│   └── ...
│
├── tests/
│   ├── unit/
│   └── integration/
│
├── .env                             # Environment variables (gitignored)
├── .env.example                     # Environment template
├── .gitignore                       # Git ignore rules
├── go.mod                           # Go module definition
├── go.sum                           # Go dependencies checksums
├── Dockerfile                       # Docker configuration
├── Makefile                         # Build automation
└── README.md                        # Backend documentation
```

### 5.2 Key Files

#### `cmd/server/main.go`
```go
package main

import (
    "log"
    "backend-go/internal/api/v1"
    "backend-go/internal/config"
    "github.com/gin-gonic/gin"
)

func main() {
    cfg := config.Load()

    r := gin.Default()

    // API routes
    v1.RegisterRoutes(r, cfg)

    log.Printf("Server starting on port %s", cfg.Port)
    r.Run(":" + cfg.Port)
}
```

#### `internal/config/config.go`
```go
package config

import (
    "os"
    "github.com/joho/godotenv"
)

type Config struct {
    Port          string
    DatabaseURL   string
    RedisURL      string
    JWTSecret     string
    FrontendURL   string
}

func Load() *Config {
    godotenv.Load()

    return &Config{
        Port:        getEnv("PORT", "3001"),
        DatabaseURL: getEnv("DATABASE_URL", ""),
        RedisURL:    getEnv("REDIS_URL", ""),
        JWTSecret:   getEnv("JWT_SECRET", ""),
        FrontendURL: getEnv("FRONTEND_URL", ""),
    }
}

func getEnv(key, fallback string) string {
    if value := os.Getenv(key); value != "" {
        return value
    }
    return fallback
}
```

---

## 6. Shared Configuration

### 6.1 Docker Compose

```yaml
# docker-compose.yml
version: '3.8'

services:
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    ports:
      - "3000:3000"
    environment:
      - NEXT_PUBLIC_API_URL=http://localhost:3001/api/v1
    depends_on:
      - backend

  backend:
    build:
      context: ./backend-node
      dockerfile: Dockerfile
    ports:
      - "3001:3001"
    environment:
      - DATABASE_URL=postgresql://user:password@postgres:5432/walletflow
      - REDIS_URL=redis://redis:6379
      - FRONTEND_URL=http://localhost:3000
    depends_on:
      - postgres
      - redis

  postgres:
    image: postgres:15-alpine
    ports:
      - "5432:5432"
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=walletflow
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

volumes:
  postgres_data:
  redis_data:
```

### 6.2 Environment Variables

```bash
# .env.example

# Frontend
NEXT_PUBLIC_API_URL=http://localhost:3001/api/v1

# Backend
PORT=3001
NODE_ENV=development

# Database
DATABASE_URL=postgresql://user:password@localhost:5432/walletflow

# Redis
REDIS_URL=redis://localhost:6379

# JWT
JWT_ACCESS_SECRET=your-super-secret-access-key-min-256-bits
JWT_REFRESH_SECRET=your-super-secret-refresh-key-min-256-bits

# OAuth
GOOGLE_CLIENT_ID=your-google-client-id
GOOGLE_CLIENT_SECRET=your-google-client-secret
GOOGLE_REDIRECT_URI=http://localhost:3001/api/v1/auth/oauth/google/callback

FACEBOOK_APP_ID=your-facebook-app-id
FACEBOOK_APP_SECRET=your-facebook-app-secret
FACEBOOK_REDIRECT_URI=http://localhost:3001/api/v1/auth/oauth/facebook/callback

# Email
EMAIL_SERVICE=resend
EMAIL_API_KEY=your-resend-api-key
EMAIL_FROM=noreply@walletflow.com

# Frontend URL
FRONTEND_URL=http://localhost:3000
```

---

## 7. File Naming Conventions

### 7.1 Frontend (Next.js/React)

**Components:**
```
PascalCase for components:
  - Button.tsx
  - TransactionList.tsx
  - AccountCard.tsx

camelCase for utilities:
  - api-client.ts
  - formatters.ts
  - validators.ts
```

**Pages (App Router):**
```
kebab-case for routes:
  - /app/dashboard/page.tsx
  - /app/reset-password/page.tsx
  - /app/accounts/[id]/page.tsx
```

### 7.2 Backend

**Node.js:**
```
camelCase for files:
  - authController.ts
  - userService.ts
  - jwtUtils.ts

kebab-case for routes:
  - auth-routes.ts
  - account-routes.ts
```

**Python:**
```
snake_case for all files:
  - auth_controller.py
  - user_service.py
  - jwt_utils.py
```

**Go:**
```
snake_case for files:
  - auth_handler.go
  - user_service.go
  - jwt_utils.go

PascalCase for exported functions/types
lowercase for unexported functions/types
```

### 7.3 Test Files

```
Same name as source file with .test or .spec suffix:

JavaScript/TypeScript:
  - auth.service.test.ts
  - validators.spec.ts

Python:
  - test_auth_service.py
  - test_validators.py

Go:
  - auth_service_test.go
  - validators_test.go
```

---

## Appendix

### A. VSCode Recommended Extensions

```json
{
  "recommendations": [
    "dbaeumer.vscode-eslint",
    "esbenp.prettier-vscode",
    "bradlc.vscode-tailwindcss",
    "Prisma.prisma",
    "ms-python.python",
    "golang.go"
  ]
}
```

### B. Folder Structure Best Practices

1. **Separation of Concerns**: Each folder has a single responsibility
2. **Scalability**: Easy to add new features without restructuring
3. **Discoverability**: Clear naming makes files easy to find
4. **Consistency**: Same patterns across the project
5. **Modularity**: Code can be extracted or replaced easily

### C. Related Documents

- **PROJECT_OVERVIEW.md** - Architecture overview
- **API_DESIGN.md** - API endpoint specifications
- **DEPLOYMENT_GUIDE.md** - Deployment instructions

---

**Document Status:** ✅ Complete
**Last Updated:** November 2025
