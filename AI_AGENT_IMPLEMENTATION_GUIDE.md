# 🤖 AI Agent Implementation Guide - Personal Finance Management Application

## CRITICAL: Read This First

This guide is designed for AI agents to implement a production-ready personal finance management application. Follow every instruction exactly as specified. Do not deviate from the patterns, naming conventions, or architectural decisions outlined here.

---

## 📂 EXACT Project Structure to Create

```bash
finance-app/
├── app/                                # Next.js 15+ App Router
│   ├── (auth)/                        # Public authentication routes
│   │   ├── layout.tsx                 # Auth layout (no header/sidebar)
│   │   ├── login/
│   │   │   └── page.tsx              # Login page
│   │   ├── register/
│   │   │   └── page.tsx              # Register page
│   │   └── forgot-password/
│   │       └── page.tsx              # Password reset
│   │
│   ├── (dashboard)/                   # Protected dashboard routes
│   │   ├── layout.tsx                 # Dashboard layout with sidebar
│   │   ├── page.tsx                   # Dashboard home
│   │   ├── accounts/
│   │   │   ├── page.tsx              # Accounts list
│   │   │   └── [id]/
│   │   │       └── page.tsx         # Account details
│   │   ├── transactions/
│   │   │   ├── page.tsx              # Transactions list
│   │   │   └── [id]/
│   │   │       └── page.tsx         # Transaction details
│   │   ├── categories/
│   │   │   └── page.tsx              # Categories management
│   │   ├── budgets/
│   │   │   └── page.tsx              # Budgets management
│   │   ├── analytics/
│   │   │   └── page.tsx              # Analytics dashboard
│   │   └── settings/
│   │       └── page.tsx              # User settings
│   │
│   ├── api/
│   │   └── v1/                       # REST API v1
│   │       ├── auth/
│   │       │   ├── login/
│   │       │   │   └── route.ts
│   │       │   ├── register/
│   │       │   │   └── route.ts
│   │       │   ├── logout/
│   │       │   │   └── route.ts
│   │       │   └── refresh/
│   │       │       └── route.ts
│   │       ├── accounts/
│   │       │   ├── route.ts
│   │       │   ├── [id]/
│   │       │   │   └── route.ts
│   │       │   └── swap-order/
│   │       │       └── route.ts
│   │       ├── transactions/
│   │       │   ├── route.ts
│   │       │   ├── [id]/
│   │       │   │   └── route.ts
│   │       │   └── summary/
│   │       │       └── route.ts
│   │       ├── categories/
│   │       │   ├── route.ts
│   │       │   ├── [id]/
│   │       │   │   └── route.ts
│   │       │   ├── tree/
│   │       │   │   └── route.ts
│   │       │   └── swap-order/
│   │       │       └── route.ts
│   │       ├── personal-ids/
│   │       │   └── max/
│   │       │       └── route.ts
│   │       ├── transfers/
│   │       │   ├── route.ts
│   │       │   └── [id]/
│   │       │       └── route.ts
│   │       ├── labels/
│   │       │   ├── route.ts
│   │       │   └── [id]/
│   │       │       └── route.ts
│   │       ├── groups/
│   │       │   ├── route.ts
│   │       │   └── [id]/
│   │       │       └── route.ts
│   │       └── analytics/
│   │           ├── summary/
│   │           │   └── route.ts
│   │           ├── trends/
│   │           │   └── route.ts
│   │           └── cashflow/
│   │               └── route.ts
│   │
│   ├── layout.tsx                     # Root layout
│   ├── providers.tsx                  # Context providers wrapper
│   └── globals.css                    # Global styles
│
├── src/
│   ├── components/                    # Reusable UI components
│   │   ├── common/
│   │   │   ├── Button.tsx
│   │   │   ├── Input.tsx
│   │   │   ├── Modal.tsx
│   │   │   ├── Card.tsx
│   │   │   └── Table.tsx
│   │   ├── layout/
│   │   │   ├── Header.tsx
│   │   │   ├── Sidebar.tsx
│   │   │   ├── Footer.tsx
│   │   │   └── MobileNav.tsx
│   │   ├── features/
│   │   │   ├── TransactionModal.tsx
│   │   │   ├── AccountCard.tsx
│   │   │   ├── CategoryTree.tsx
│   │   │   ├── BudgetProgress.tsx
│   │   │   └── QuickActions.tsx
│   │   └── charts/
│   │       ├── PieChart.tsx
│   │       ├── LineChart.tsx
│   │       └── BarChart.tsx
│   │
│   ├── context/                       # React Context providers
│   │   ├── ToastContext.tsx          # Level 1: Notifications
│   │   ├── AuthStateContext.tsx      # Level 2: Auth state
│   │   ├── AuthContext.tsx           # Level 3: Auth logic
│   │   └── TransactionModalContext.tsx # Level 4: Transaction modal
│   │
│   ├── services/                      # API service layer
│   │   ├── api.ts                    # Base API client
│   │   ├── authService.ts
│   │   ├── accountService.ts
│   │   ├── transactionService.ts
│   │   ├── categoryService.ts
│   │   ├── transferService.ts
│   │   ├── labelService.ts
│   │   ├── groupService.ts
│   │   └── analyticsService.ts
│   │
│   ├── hooks/                         # Custom React hooks
│   │   ├── useAuth.ts
│   │   ├── useAccounts.ts
│   │   ├── useTransactions.ts
│   │   ├── useCategories.ts
│   │   ├── useCategoryData.ts
│   │   ├── useQuickTransactions.ts
│   │   └── useDebounce.ts
│   │
│   ├── lib/                           # Backend utilities
│   │   ├── auth/
│   │   │   ├── jwt.ts                # JWT generation/validation
│   │   │   ├── password.ts           # Password hashing
│   │   │   └── middleware.ts         # Auth middleware
│   │   ├── db/
│   │   │   ├── prisma.ts            # Prisma client singleton
│   │   │   └── seed.ts              # Database seeder
│   │   ├── validation/
│   │   │   ├── auth.ts              # Auth schemas
│   │   │   ├── account.ts           # Account schemas
│   │   │   ├── transaction.ts       # Transaction schemas
│   │   │   └── common.ts            # Common schemas
│   │   └── api/
│   │       ├── response.ts          # Response builder
│   │       └── errors.ts            # Error handlers
│   │
│   ├── types/                         # TypeScript type definitions
│   │   ├── api.ts                    # API types
│   │   ├── models.ts                 # Database models
│   │   ├── forms.ts                  # Form types
│   │   └── global.d.ts              # Global types
│   │
│   ├── utils/                         # Utility functions
│   │   ├── crypto.ts                 # Token encryption
│   │   ├── format.ts                 # Formatters
│   │   ├── date.ts                   # Date utilities
│   │   └── constants.ts              # App constants
│   │
│   ├── data/                          # Static data
│   │   ├── default_categories.json   # 70+ default categories
│   │   └── default_accounts.json     # 3 default accounts
│   │
│   └── styles/                        # Additional styles
│       └── components.css
│
├── prisma/
│   ├── schema.prisma                 # Database schema
│   ├── migrations/                   # Migration files
│   └── seed.ts                       # Database seeder
│
├── public/                            # Static assets
│   ├── icons/
│   └── images/
│
├── tests/                             # Test files
│   ├── unit/
│   ├── integration/
│   └── e2e/
│
├── .env.example                       # Environment variables template
├── .env.local                         # Local environment variables
├── .gitignore
├── next.config.js                     # Next.js configuration
├── tailwind.config.ts                 # Tailwind CSS config
├── tsconfig.json                      # TypeScript config
├── package.json                       # Dependencies
└── README.md                          # Documentation
```

---

## 🗄️ EXACT Database Schema (PostgreSQL + Prisma)

### Step 1: Create prisma/schema.prisma

```prisma
// This is your Prisma schema file
generator client {
  provider = "prisma-client-js"
  binaryTargets = ["native", "rhel-openssl-3.0.x"]
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id              String    @id @default(uuid())
  email           String    @unique @db.VarChar(255)
  username        String    @unique @db.VarChar(100)
  password_hash   String    @db.VarChar(255)
  full_name       String?   @db.VarChar(255)
  avatar_url      String?   @db.VarChar(500)
  email_verified  Boolean   @default(false)
  two_factor_enabled Boolean @default(false)
  two_factor_secret String? @db.VarChar(255)
  timezone        String    @default("UTC") @db.VarChar(50)
  currency        String    @default("USD") @db.VarChar(3)
  date_format     String    @default("YYYY-MM-DD") @db.VarChar(20)
  number_format   String    @default("1,234.56") @db.VarChar(20)
  created_at      DateTime  @default(now())
  updated_at      DateTime  @updatedAt
  deleted_at      DateTime?
  
  // Relations
  accounts        Account[]
  categories      Category[]
  transactions    Transaction[]
  transfers       Transfer[]
  labels          Label[]
  groups          AccountGroup[]
  budgets         Budget[]
  goals           Goal[]
  recurring       RecurringTransaction[]
  audit_logs      AuditLog[]
  
  @@index([email])
  @@index([username])
}

model AccountGroup {
  id          String    @id @default(uuid())
  user_id     String
  name        String    @db.VarChar(100)
  icon        String?   @db.VarChar(50)
  color       String?   @db.VarChar(7)
  position    Int
  created_at  DateTime  @default(now())
  updated_at  DateTime  @updatedAt
  
  // Relations
  user        User      @relation(fields: [user_id], references: [id], onDelete: Cascade)
  accounts    Account[]
  
  @@unique([user_id, position])
  @@index([user_id])
}

model Account {
  id                  String    @id @default(uuid())
  user_id             String
  personal_id         BigInt
  group_id            String?
  name                String    @db.VarChar(100)
  account_type        String    @db.VarChar(50) // checking, savings, credit_card, cash, investment, loan
  currency            String    @default("USD") @db.VarChar(3)
  initial_balance     Decimal   @default(0) @db.Decimal(15, 2)
  current_balance     Decimal   @default(0) @db.Decimal(15, 2)
  credit_limit        Decimal?  @db.Decimal(15, 2)
  interest_rate       Decimal?  @db.Decimal(5, 2)
  icon                String    @db.VarChar(50)
  color               String    @db.VarChar(7)
  is_active           Boolean   @default(true)
  is_included_in_total Boolean @default(true)
  position            Json?
  created_at          DateTime  @default(now())
  updated_at          DateTime  @updatedAt
  deleted_at          DateTime?
  created_by          String?   @db.VarChar(64)
  updated_by          String?   @db.VarChar(64)
  
  // Relations
  user                User      @relation(fields: [user_id], references: [id], onDelete: Cascade)
  group               AccountGroup? @relation(fields: [group_id], references: [id], onDelete: SetNull)
  transactions        Transaction[]
  transfers_from      Transfer[] @relation("FromAccount")
  transfers_to        Transfer[] @relation("ToAccount")
  goals               Goal[]
  recurring           RecurringTransaction[]
  
  @@unique([user_id, personal_id])
  @@index([user_id])
  @@index([group_id])
}

model Category {
  id              String    @id @default(uuid())
  user_id         String
  personal_id     BigInt
  parent_id       String?
  name            String    @db.VarChar(100)
  type            String    @db.VarChar(20) // income, expense
  nature          String    @default("WANT") @db.VarChar(8) // WANT, NEED, MUST
  icon            String    @db.VarChar(50)
  color           String?   @db.VarChar(7)
  is_system       Boolean   @default(false)
  is_active       Boolean   @default(true)
  position        Json?
  created_at      DateTime  @default(now())
  updated_at      DateTime  @updatedAt
  created_by      String?   @db.VarChar(64)
  updated_by      String?   @db.VarChar(64)
  
  // Relations
  user            User      @relation(fields: [user_id], references: [id], onDelete: Cascade)
  parent          Category? @relation("CategoryHierarchy", fields: [parent_id], references: [id], onDelete: Cascade)
  children        Category[] @relation("CategoryHierarchy")
  transactions    Transaction[]
  budget_categories BudgetCategory[]
  recurring       RecurringTransaction[]
  
  @@unique([user_id, personal_id])
  @@index([user_id])
  @@index([parent_id])
}

model Transaction {
  id              String    @id @default(uuid())
  user_id         String
  personal_id     BigInt
  account_id      String
  category_id     String?
  type            String    @db.VarChar(20) // income, expense, transfer_in, transfer_out
  amount          Decimal   @db.Decimal(15, 2)
  currency        String    @default("USD") @db.VarChar(3)
  exchange_rate   Decimal   @default(1) @db.Decimal(10, 6)
  date            DateTime  @db.Date
  description     String?   @db.Text
  payee           String?   @db.VarChar(255)
  payment_method  String?   @db.VarChar(50)
  payment_status  String?   @db.VarChar(32)
  reference_number String?  @db.VarChar(100)
  is_recurring    Boolean   @default(false)
  recurring_id    String?
  transfer_id     String?
  position        Json?
  created_at      DateTime  @default(now())
  updated_at      DateTime  @updatedAt
  deleted_at      DateTime?
  created_by      String?   @db.VarChar(64)
  updated_by      String?   @db.VarChar(64)
  
  // Relations
  user            User      @relation(fields: [user_id], references: [id], onDelete: Cascade)
  account         Account   @relation(fields: [account_id], references: [id], onDelete: Cascade)
  category        Category? @relation(fields: [category_id], references: [id], onDelete: SetNull)
  recurring       RecurringTransaction? @relation(fields: [recurring_id], references: [id], onDelete: SetNull)
  transfer        Transfer? @relation(fields: [transfer_id], references: [id], onDelete: SetNull)
  labels          TransactionLabel[]
  attachments     Attachment[]
  
  @@unique([user_id, personal_id])
  @@index([user_id, date])
  @@index([account_id])
  @@index([category_id])
  @@index([transfer_id])
}

model Transfer {
  id              String    @id @default(uuid())
  user_id         String
  personal_id     BigInt
  date            DateTime  @db.Date
  from_account    String
  to_account      String
  amount          Decimal   @db.Decimal(15, 2)
  to_amount       Decimal?  @db.Decimal(15, 2)
  description     String?   @db.Text
  currency        String    @default("USD") @db.VarChar(3)
  to_currency     String?   @db.VarChar(3)
  position        Json?
  created_at      DateTime  @default(now())
  updated_at      DateTime  @updatedAt
  created_by      String?   @db.VarChar(64)
  updated_by      String?   @db.VarChar(64)
  
  // Relations
  user            User      @relation(fields: [user_id], references: [id], onDelete: Cascade)
  from_account_rel Account  @relation("FromAccount", fields: [from_account], references: [id], onDelete: Cascade)
  to_account_rel  Account   @relation("ToAccount", fields: [to_account], references: [id], onDelete: Cascade)
  transactions    Transaction[]
  
  @@unique([user_id, personal_id])
  @@index([user_id])
  @@index([from_account])
  @@index([to_account])
}

model Label {
  id              String    @id @default(uuid())
  user_id         String
  personal_id     BigInt
  name            String    @db.VarChar(50)
  color           String    @db.VarChar(7)
  created_at      DateTime  @default(now())
  created_by      String?   @db.VarChar(64)
  updated_at      DateTime  @updatedAt
  updated_by      String?   @db.VarChar(64)
  
  // Relations
  user            User      @relation(fields: [user_id], references: [id], onDelete: Cascade)
  transactions    TransactionLabel[]
  
  @@unique([user_id, personal_id])
  @@index([user_id])
}

model TransactionLabel {
  id              String    @id @default(uuid())
  transaction_id  String
  label_id        String
  
  // Relations
  transaction     Transaction @relation(fields: [transaction_id], references: [id], onDelete: Cascade)
  label           Label     @relation(fields: [label_id], references: [id], onDelete: Cascade)
  
  @@unique([transaction_id, label_id])
  @@index([transaction_id])
  @@index([label_id])
}

model Attachment {
  id              String    @id @default(uuid())
  transaction_id  String
  file_name       String    @db.VarChar(255)
  file_url        String    @db.VarChar(500)
  file_type       String?   @db.VarChar(50)
  file_size       Int?
  uploaded_at     DateTime  @default(now())
  
  // Relations
  transaction     Transaction @relation(fields: [transaction_id], references: [id], onDelete: Cascade)
  
  @@index([transaction_id])
}

model Budget {
  id              String    @id @default(uuid())
  user_id         String
  name            String    @db.VarChar(100)
  type            String    @db.VarChar(20) // category, total, envelope
  period          String    @db.VarChar(20) // monthly, quarterly, yearly
  amount          Decimal   @db.Decimal(15, 2)
  currency        String    @default("USD") @db.VarChar(3)
  start_date      DateTime  @db.Date
  end_date        DateTime? @db.Date
  is_active       Boolean   @default(true)
  alert_threshold_1 Int     @default(50)
  alert_threshold_2 Int     @default(80)
  allow_rollover  Boolean   @default(false)
  created_at      DateTime  @default(now())
  updated_at      DateTime  @updatedAt
  
  // Relations
  user            User      @relation(fields: [user_id], references: [id], onDelete: Cascade)
  categories      BudgetCategory[]
  
  @@index([user_id])
}

model BudgetCategory {
  budget_id       String
  category_id     String
  allocated_amount Decimal? @db.Decimal(15, 2)
  
  // Relations
  budget          Budget    @relation(fields: [budget_id], references: [id], onDelete: Cascade)
  category        Category  @relation(fields: [category_id], references: [id], onDelete: Cascade)
  
  @@id([budget_id, category_id])
  @@index([budget_id])
  @@index([category_id])
}

model RecurringTransaction {
  id              String    @id @default(uuid())
  user_id         String
  account_id      String
  category_id     String?
  type            String    @db.VarChar(20)
  amount          Decimal   @db.Decimal(15, 2)
  currency        String    @db.VarChar(3)
  description     String?   @db.Text
  payee           String?   @db.VarChar(255)
  frequency       String    @db.VarChar(20) // daily, weekly, monthly, yearly
  interval_value  Int       @default(1)
  start_date      DateTime  @db.Date
  end_date        DateTime? @db.Date
  last_processed  DateTime? @db.Date
  next_due_date   DateTime? @db.Date
  is_active       Boolean   @default(true)
  created_at      DateTime  @default(now())
  updated_at      DateTime  @updatedAt
  
  // Relations
  user            User      @relation(fields: [user_id], references: [id], onDelete: Cascade)
  account         Account   @relation(fields: [account_id], references: [id], onDelete: Cascade)
  category        Category? @relation(fields: [category_id], references: [id], onDelete: SetNull)
  transactions    Transaction[]
  
  @@index([user_id])
  @@index([account_id])
}

model Goal {
  id              String    @id @default(uuid())
  user_id         String
  name            String    @db.VarChar(100)
  target_amount   Decimal   @db.Decimal(15, 2)
  current_amount  Decimal   @default(0) @db.Decimal(15, 2)
  currency        String    @default("USD") @db.VarChar(3)
  target_date     DateTime? @db.Date
  account_id      String?
  icon            String?   @db.VarChar(50)
  color           String?   @db.VarChar(7)
  is_completed    Boolean   @default(false)
  created_at      DateTime  @default(now())
  updated_at      DateTime  @updatedAt
  
  // Relations
  user            User      @relation(fields: [user_id], references: [id], onDelete: Cascade)
  account         Account?  @relation(fields: [account_id], references: [id], onDelete: SetNull)
  
  @@index([user_id])
}

model AuditLog {
  id              String    @id @default(uuid())
  user_id         String
  action          String    @db.VarChar(50)
  entity_type     String    @db.VarChar(50)
  entity_id       String?
  old_values      Json?
  new_values      Json?
  ip_address      String?   @db.Inet
  user_agent      String?   @db.Text
  created_at      DateTime  @default(now())
  
  // Relations
  user            User      @relation(fields: [user_id], references: [id], onDelete: Cascade)
  
  @@index([user_id])
  @@index([entity_type, entity_id])
}
```

---

## 🔐 EXACT Authentication Implementation

### Step 1: Create JWT Token Management (src/lib/auth/jwt.ts)

```typescript
import { SignJWT, jwtVerify } from 'jose';
import { NextRequest } from 'next/server';

const JWT_ACCESS_SECRET = new TextEncoder().encode(
  process.env.JWT_ACCESS_SECRET || 'your-secret-key-change-in-production'
);
const JWT_REFRESH_SECRET = new TextEncoder().encode(
  process.env.JWT_REFRESH_SECRET || 'your-refresh-secret-change-in-production'
);

const ACCESS_TOKEN_EXPIRY = '24h';
const REFRESH_TOKEN_EXPIRY = '7d';

export interface TokenPayload {
  user_id: string;
  email: string;
  username: string;
}

export async function generateAccessToken(payload: TokenPayload): Promise<string> {
  return new SignJWT(payload)
    .setProtectedHeader({ alg: 'HS256' })
    .setIssuedAt()
    .setExpirationTime(ACCESS_TOKEN_EXPIRY)
    .sign(JWT_ACCESS_SECRET);
}

export async function generateRefreshToken(payload: TokenPayload): Promise<string> {
  return new SignJWT(payload)
    .setProtectedHeader({ alg: 'HS256' })
    .setIssuedAt()
    .setExpirationTime(REFRESH_TOKEN_EXPIRY)
    .sign(JWT_REFRESH_SECRET);
}

export async function verifyAccessToken(token: string): Promise<TokenPayload> {
  const { payload } = await jwtVerify(token, JWT_ACCESS_SECRET);
  return payload as TokenPayload;
}

export async function verifyRefreshToken(token: string): Promise<TokenPayload> {
  const { payload } = await jwtVerify(token, JWT_REFRESH_SECRET);
  return payload as TokenPayload;
}

export async function extractTokenFromHeader(request: NextRequest): Promise<string | null> {
  const authorization = request.headers.get('Authorization');
  if (!authorization) return null;
  
  const [type, token] = authorization.split(' ');
  if (type !== 'Bearer' || !token) return null;
  
  return token;
}
```

### Step 2: Create Password Hashing (src/lib/auth/password.ts)

```typescript
import bcrypt from 'bcryptjs';

const SALT_ROUNDS = 10;

export async function hashPassword(password: string): Promise<string> {
  return bcrypt.hash(password, SALT_ROUNDS);
}

export async function verifyPassword(password: string, hash: string): Promise<boolean> {
  return bcrypt.compare(password, hash);
}

export function validatePasswordStrength(password: string): {
  isValid: boolean;
  errors: string[];
} {
  const errors: string[] = [];
  
  if (password.length < 8) {
    errors.push('Password must be at least 8 characters long');
  }
  if (!/[A-Z]/.test(password)) {
    errors.push('Password must contain at least one uppercase letter');
  }
  if (!/[a-z]/.test(password)) {
    errors.push('Password must contain at least one lowercase letter');
  }
  if (!/[0-9]/.test(password)) {
    errors.push('Password must contain at least one number');
  }
  if (!/[!@#$%^&*]/.test(password)) {
    errors.push('Password must contain at least one special character');
  }
  
  return {
    isValid: errors.length === 0,
    errors
  };
}
```

### Step 3: Create Auth Middleware (src/lib/auth/middleware.ts)

```typescript
import { NextRequest, NextResponse } from 'next/server';
import { verifyAccessToken, extractTokenFromHeader } from './jwt';
import { prisma } from '@/lib/db/prisma';

export interface AuthResult {
  user: {
    user_id: string;
    email: string;
    username: string;
  };
}

export interface AuthError {
  error: NextResponse;
}

export async function requireAuth(request: NextRequest): Promise<AuthResult | AuthError> {
  try {
    const token = await extractTokenFromHeader(request);
    
    if (!token) {
      return {
        error: NextResponse.json(
          { success: false, error: { code: 'AUTH_REQUIRED', message: 'Authentication required' } },
          { status: 401 }
        )
      };
    }
    
    const payload = await verifyAccessToken(token);
    
    // Verify user still exists
    const user = await prisma.user.findUnique({
      where: { id: payload.user_id },
      select: { id: true, email: true, username: true }
    });
    
    if (!user) {
      return {
        error: NextResponse.json(
          { success: false, error: { code: 'USER_NOT_FOUND', message: 'User not found' } },
          { status: 401 }
        )
      };
    }
    
    return {
      user: {
        user_id: user.id,
        email: user.email,
        username: user.username
      }
    };
  } catch (error) {
    return {
      error: NextResponse.json(
        { success: false, error: { code: 'AUTH_INVALID_TOKEN', message: 'Invalid or expired token' } },
        { status: 401 }
      )
    };
  }
}
```

---

## 📊 EXACT Default Data Structure

### Step 1: Create Default Categories (src/data/default_categories.json)

```json
{
  "income": [
    { "name": "Salary", "icon": "FaBriefcase", "color": "#10b981", "nature": "MUST" },
    { "name": "Freelance", "icon": "FaLaptop", "color": "#10b981", "nature": "WANT" },
    { "name": "Investments", "icon": "FaChartLine", "color": "#10b981", "nature": "WANT" },
    { "name": "Rental Income", "icon": "FaHome", "color": "#10b981", "nature": "WANT" },
    { "name": "Business", "icon": "FaStore", "color": "#10b981", "nature": "WANT" },
    { "name": "Gifts", "icon": "FaGift", "color": "#10b981", "nature": "WANT" },
    { "name": "Refunds", "icon": "FaUndo", "color": "#10b981", "nature": "WANT" },
    { "name": "Other Income", "icon": "FaCoins", "color": "#10b981", "nature": "WANT" }
  ],
  "expense": {
    "Food & Drinks": {
      "icon": "FaUtensils",
      "color": "#f59e0b",
      "nature": "NEED",
      "children": [
        { "name": "Groceries", "icon": "FaShoppingCart", "nature": "NEED" },
        { "name": "Restaurant, fast-food", "icon": "FaConciergeBell", "nature": "WANT" },
        { "name": "Bar, cafe", "icon": "FaCoffee", "nature": "WANT" },
        { "name": "Delivery", "icon": "FaTruck", "nature": "WANT" }
      ]
    },
    "Shopping": {
      "icon": "FaShoppingBag",
      "color": "#ec4899",
      "nature": "WANT",
      "children": [
        { "name": "Clothes & shoes", "icon": "FaTshirt", "nature": "NEED" },
        { "name": "Electronics", "icon": "FaLaptop", "nature": "WANT" },
        { "name": "Home, garden", "icon": "FaHome", "nature": "NEED" },
        { "name": "Toiletry", "icon": "FaToiletPaper", "nature": "NEED" },
        { "name": "Kids", "icon": "FaBaby", "nature": "NEED" },
        { "name": "Pets", "icon": "FaPaw", "nature": "NEED" },
        { "name": "Gifts, joy", "icon": "FaGift", "nature": "WANT" },
        { "name": "Health and beauty", "icon": "FaSpa", "nature": "WANT" },
        { "name": "Stationery", "icon": "FaPencilAlt", "nature": "NEED" },
        { "name": "Books, audio, subscriptions", "icon": "FaBook", "nature": "WANT" },
        { "name": "Tools", "icon": "FaTools", "nature": "NEED" }
      ]
    },
    "Housing": {
      "icon": "FaHome",
      "color": "#8b5cf6",
      "nature": "MUST",
      "children": [
        { "name": "Rent", "icon": "FaKey", "nature": "MUST" },
        { "name": "Mortgage", "icon": "FaFileContract", "nature": "MUST" },
        { "name": "Energy, utilities", "icon": "FaBolt", "nature": "MUST" },
        { "name": "Maintenance, repairs", "icon": "FaWrench", "nature": "NEED" },
        { "name": "Property insurance", "icon": "FaShieldAlt", "nature": "MUST" },
        { "name": "Services", "icon": "FaHandshake", "nature": "NEED" }
      ]
    },
    "Transportation": {
      "icon": "FaBus",
      "color": "#6366f1",
      "nature": "NEED",
      "children": [
        { "name": "Public transport", "icon": "FaTrain", "nature": "NEED" },
        { "name": "Taxi", "icon": "FaTaxi", "nature": "WANT" },
        { "name": "Business trips", "icon": "FaSuitcase", "nature": "NEED" },
        { "name": "Long distance", "icon": "FaPlane", "nature": "WANT" }
      ]
    },
    "Vehicle": {
      "icon": "FaCar",
      "color": "#06b6d4",
      "nature": "NEED",
      "children": [
        { "name": "Fuel", "icon": "FaGasPump", "nature": "NEED" },
        { "name": "Parking", "icon": "FaParking", "nature": "NEED" },
        { "name": "Vehicle maintenance", "icon": "FaOilCan", "nature": "NEED" },
        { "name": "Vehicle insurance", "icon": "FaShieldAlt", "nature": "MUST" },
        { "name": "Leasing", "icon": "FaFileContract", "nature": "MUST" },
        { "name": "Rentals", "icon": "FaKey", "nature": "WANT" }
      ]
    },
    "Life & Entertainment": {
      "icon": "FaFilm",
      "color": "#a855f7",
      "nature": "WANT",
      "children": [
        { "name": "Health care, doctor", "icon": "FaUserMd", "nature": "MUST" },
        { "name": "Wellness, beauty", "icon": "FaSpa", "nature": "WANT" },
        { "name": "Active sport, fitness", "icon": "FaDumbbell", "nature": "WANT" },
        { "name": "Culture, sport events", "icon": "FaTicketAlt", "nature": "WANT" },
        { "name": "Education", "icon": "FaGraduationCap", "nature": "NEED" },
        { "name": "Hobbies", "icon": "FaPalette", "nature": "WANT" },
        { "name": "Holiday, trips", "icon": "FaUmbrellaBeach", "nature": "WANT" },
        { "name": "TV, streaming", "icon": "FaTv", "nature": "WANT" },
        { "name": "Charity, gifts", "icon": "FaHandHoldingHeart", "nature": "WANT" },
        { "name": "Lottery, gambling", "icon": "FaDice", "nature": "WANT" },
        { "name": "Alcohol, tobacco", "icon": "FaWineGlass", "nature": "WANT" },
        { "name": "Books, audio, subscriptions", "icon": "FaBook", "nature": "WANT" },
        { "name": "Life events", "icon": "FaBirthdayCake", "nature": "WANT" }
      ]
    },
    "Communication, PC": {
      "icon": "FaPhone",
      "color": "#f97316",
      "nature": "NEED",
      "children": [
        { "name": "Phone, cell phone", "icon": "FaMobileAlt", "nature": "NEED" },
        { "name": "Internet", "icon": "FaWifi", "nature": "NEED" },
        { "name": "Software, apps, games", "icon": "FaGamepad", "nature": "WANT" },
        { "name": "Postal services", "icon": "FaEnvelope", "nature": "NEED" }
      ]
    },
    "Financial expenses": {
      "icon": "FaChartPie",
      "color": "#ef4444",
      "nature": "MUST",
      "children": [
        { "name": "Taxes", "icon": "FaFileInvoiceDollar", "nature": "MUST" },
        { "name": "Insurances", "icon": "FaShieldAlt", "nature": "MUST" },
        { "name": "Loan, interests", "icon": "FaHandHoldingUsd", "nature": "MUST" },
        { "name": "Charges, fees", "icon": "FaMoneyCheckAlt", "nature": "MUST" },
        { "name": "Fines", "icon": "FaGavel", "nature": "MUST" },
        { "name": "Child support", "icon": "FaBaby", "nature": "MUST" },
        { "name": "Advisory", "icon": "FaUserTie", "nature": "WANT" }
      ]
    },
    "Investments": {
      "icon": "FaChartLine",
      "color": "#14b8a6",
      "nature": "WANT",
      "children": [
        { "name": "Financial investments", "icon": "FaChartArea", "nature": "WANT" },
        { "name": "Savings", "icon": "FaPiggyBank", "nature": "NEED" },
        { "name": "Realty", "icon": "FaBuilding", "nature": "WANT" },
        { "name": "Collections", "icon": "FaGem", "nature": "WANT" },
        { "name": "Vehicles, chattels", "icon": "FaCar", "nature": "WANT" }
      ]
    },
    "Others": {
      "icon": "FaEllipsisH",
      "color": "#6b7280",
      "nature": "WANT",
      "children": [
        { "name": "Others", "icon": "FaQuestion", "nature": "WANT" },
        { "name": "Missing", "icon": "FaExclamationTriangle", "nature": "WANT" }
      ]
    }
  }
}
```

### Step 2: Create Default Accounts (src/data/default_accounts.json)

```json
[
  {
    "personal_id": 1,
    "name": "Cash",
    "account_type": "cash",
    "icon": "FaWallet",
    "color": "#10b981",
    "currency": "USD",
    "initial_balance": 0,
    "is_active": true,
    "is_included_in_total": true
  },
  {
    "personal_id": 2,
    "name": "Checking Account",
    "account_type": "checking",
    "icon": "FaUniversity",
    "color": "#3b82f6",
    "currency": "USD",
    "initial_balance": 0,
    "is_active": true,
    "is_included_in_total": true
  },
  {
    "personal_id": 3,
    "name": "Savings Account",
    "account_type": "savings",
    "icon": "FaPiggyBank",
    "color": "#f59e0b",
    "currency": "USD",
    "initial_balance": 0,
    "is_active": true,
    "is_included_in_total": true
  }
]
```

---

## 🎯 EXACT API Implementation

### Step 1: Response Builder (src/lib/api/response.ts)

```typescript
export interface SuccessResponse<T = any> {
  success: true;
  data?: T;
  message?: string;
  meta?: {
    total?: number;
    page?: number;
    per_page?: number;
    total_pages?: number;
    timestamp?: string;
    version?: string;
  };
}

export interface ErrorResponse {
  success: false;
  error: {
    code: string;
    message: string;
    details?: any;
    field?: string;
  };
  meta?: {
    timestamp: string;
    request_id?: string;
  };
}

export class ApiResponseBuilder {
  static success<T>(data?: T, message?: string, meta?: any): SuccessResponse<T> {
    return {
      success: true,
      ...(data !== undefined && { data }),
      ...(message && { message }),
      ...(meta && { meta: { ...meta, timestamp: new Date().toISOString() } })
    };
  }
  
  static error(code: string, message: string, details?: any): ErrorResponse {
    return {
      success: false,
      error: {
        code,
        message,
        ...(details && { details })
      },
      meta: {
        timestamp: new Date().toISOString()
      }
    };
  }
  
  static paginated<T>(
    data: T[],
    page: number,
    limit: number,
    total: number
  ): SuccessResponse<T[]> {
    return {
      success: true,
      data,
      meta: {
        page,
        per_page: limit,
        total,
        total_pages: Math.ceil(total / limit),
        timestamp: new Date().toISOString()
      }
    };
  }
}
```

### Step 2: Register Endpoint with Auto-Seeding (app/api/v1/auth/register/route.ts)

```typescript
import { NextRequest, NextResponse } from 'next/server';
import { z } from 'zod';
import { prisma } from '@/lib/db/prisma';
import { hashPassword, validatePasswordStrength } from '@/lib/auth/password';
import { generateAccessToken, generateRefreshToken } from '@/lib/auth/jwt';
import { ApiResponseBuilder } from '@/lib/api/response';
import defaultCategories from '@/data/default_categories.json';
import defaultAccounts from '@/data/default_accounts.json';

const RegisterSchema = z.object({
  email: z.string().email(),
  username: z.string().min(3).max(20).regex(/^[a-zA-Z0-9_]+$/),
  password: z.string().min(8),
  full_name: z.string().optional()
});

export async function POST(request: NextRequest) {
  try {
    const body = await request.json();
    const validation = RegisterSchema.safeParse(body);
    
    if (!validation.success) {
      return NextResponse.json(
        ApiResponseBuilder.error(
          'VALIDATION_ERROR',
          'Invalid input data',
          validation.error.errors
        ),
        { status: 400 }
      );
    }
    
    const { email, username, password, full_name } = validation.data;
    
    // Validate password strength
    const passwordValidation = validatePasswordStrength(password);
    if (!passwordValidation.isValid) {
      return NextResponse.json(
        ApiResponseBuilder.error(
          'WEAK_PASSWORD',
          'Password does not meet requirements',
          passwordValidation.errors
        ),
        { status: 400 }
      );
    }
    
    // Check if user exists
    const existingUser = await prisma.user.findFirst({
      where: {
        OR: [{ email }, { username }]
      }
    });
    
    if (existingUser) {
      return NextResponse.json(
        ApiResponseBuilder.error(
          'USER_EXISTS',
          'User with this email or username already exists'
        ),
        { status: 409 }
      );
    }
    
    // Hash password
    const password_hash = await hashPassword(password);
    
    // Create user with default data in transaction
    const result = await prisma.$transaction(async (tx) => {
      // 1. Create user
      const user = await tx.user.create({
        data: {
          email,
          username,
          password_hash,
          full_name,
          email_verified: false
        }
      });
      
      // 2. Create default income categories
      for (const category of defaultCategories.income) {
        await tx.category.create({
          data: {
            user_id: user.id,
            personal_id: BigInt(defaultCategories.income.indexOf(category) + 1),
            name: category.name,
            type: 'income',
            nature: category.nature,
            icon: category.icon,
            color: category.color,
            is_system: true,
            is_active: true
          }
        });
      }
      
      // 3. Create default expense categories with hierarchy
      let categoryPersonalId = defaultCategories.income.length + 1;
      
      for (const [parentName, parentData] of Object.entries(defaultCategories.expense)) {
        // Create parent category
        const parent = await tx.category.create({
          data: {
            user_id: user.id,
            personal_id: BigInt(categoryPersonalId++),
            name: parentName,
            type: 'expense',
            nature: parentData.nature,
            icon: parentData.icon,
            color: parentData.color,
            is_system: true,
            is_active: true
          }
        });
        
        // Create child categories
        for (const child of parentData.children) {
          await tx.category.create({
            data: {
              user_id: user.id,
              personal_id: BigInt(categoryPersonalId++),
              parent_id: parent.id,
              name: child.name,
              type: 'expense',
              nature: child.nature || parentData.nature,
              icon: child.icon,
              color: parentData.color, // Inherit parent color
              is_system: true,
              is_active: true
            }
          });
        }
      }
      
      // 4. Create default accounts
      for (const account of defaultAccounts) {
        await tx.account.create({
          data: {
            user_id: user.id,
            personal_id: BigInt(account.personal_id),
            name: account.name,
            account_type: account.account_type,
            icon: account.icon,
            color: account.color,
            currency: account.currency,
            initial_balance: account.initial_balance,
            current_balance: account.initial_balance,
            is_active: account.is_active,
            is_included_in_total: account.is_included_in_total
          }
        });
      }
      
      return user;
    });
    
    // Generate tokens
    const tokenPayload = {
      user_id: result.id,
      email: result.email,
      username: result.username
    };
    
    const accessToken = await generateAccessToken(tokenPayload);
    const refreshToken = await generateRefreshToken(tokenPayload);
    
    return NextResponse.json(
      ApiResponseBuilder.success({
        user: {
          id: result.id,
          email: result.email,
          username: result.username,
          full_name: result.full_name
        },
        access_token: accessToken,
        refresh_token: refreshToken
      }, 'Registration successful')
    );
    
  } catch (error) {
    console.error('Registration error:', error);
    return NextResponse.json(
      ApiResponseBuilder.error(
        'INTERNAL_ERROR',
        'An error occurred during registration'
      ),
      { status: 500 }
    );
  }
}
```

### Step 3: Transaction Creation with Personal ID Management (app/api/v1/transactions/route.ts)

```typescript
import { NextRequest, NextResponse } from 'next/server';
import { z } from 'zod';
import { prisma } from '@/lib/db/prisma';
import { requireAuth } from '@/lib/auth/middleware';
import { ApiResponseBuilder } from '@/lib/api/response';

const CreateTransactionSchema = z.object({
  personal_id: z.number(),
  date: z.string().datetime(),
  account_id: z.string().uuid(),
  category_id: z.string().uuid(),
  amount: z.number(),
  type: z.enum(['income', 'expense']),
  description: z.string().optional(),
  currency: z.string().default('USD'),
  payee: z.string().optional(),
  payment_method: z.string().optional(),
  payment_status: z.string().optional(),
  label_ids: z.array(z.string().uuid()).optional()
});

export async function POST(request: NextRequest) {
  // Authentication check
  const authResult = await requireAuth(request);
  if ('error' in authResult) return authResult.error;
  
  const { user } = authResult;
  
  try {
    const body = await request.json();
    const validation = CreateTransactionSchema.safeParse(body);
    
    if (!validation.success) {
      return NextResponse.json(
        ApiResponseBuilder.error(
          'VALIDATION_ERROR',
          'Invalid transaction data',
          validation.error.errors
        ),
        { status: 400 }
      );
    }
    
    const data = validation.data;
    
    // CRITICAL: Amount sign convention
    // Expenses should be negative, income should be positive
    const finalAmount = data.type === 'expense' 
      ? -Math.abs(data.amount) 
      : Math.abs(data.amount);
    
    // Create transaction with retry logic for personal_id conflicts
    let retries = 3;
    let lastError: any;
    
    while (retries > 0) {
      try {
        const transaction = await prisma.$transaction(async (tx) => {
          // Create transaction
          const created = await tx.transaction.create({
            data: {
              user_id: user.user_id,
              personal_id: BigInt(data.personal_id),
              date: new Date(data.date),
              account_id: data.account_id,
              category_id: data.category_id,
              amount: finalAmount,
              type: data.type,
              description: data.description,
              currency: data.currency,
              payee: data.payee,
              payment_method: data.payment_method,
              payment_status: data.payment_status
            },
            include: {
              category: {
                select: { name: true, icon: true, color: true }
              },
              account: {
                select: { name: true }
              }
            }
          });
          
          // Create label associations if provided
          if (data.label_ids && data.label_ids.length > 0) {
            await tx.transactionLabel.createMany({
              data: data.label_ids.map(label_id => ({
                transaction_id: created.id,
                label_id
              }))
            });
          }
          
          // Update account balance
          await tx.account.update({
            where: { id: data.account_id },
            data: {
              current_balance: {
                increment: finalAmount
              }
            }
          });
          
          return created;
        });
        
        // Return successful response
        return NextResponse.json(
          ApiResponseBuilder.success(transaction, 'Transaction created successfully'),
          { status: 201 }
        );
        
      } catch (error: any) {
        lastError = error;
        
        // Check if it's a unique constraint violation on personal_id
        if (error.code === 'P2002' && error.meta?.target?.includes('personal_id')) {
          // Fetch new max personal_id
          const maxTransaction = await prisma.transaction.findFirst({
            where: { user_id: user.user_id },
            orderBy: { personal_id: 'desc' },
            select: { personal_id: true }
          });
          
          const newPersonalId = Number(maxTransaction?.personal_id || 0n) + 1;
          data.personal_id = newPersonalId;
          
          retries--;
          continue;
        }
        
        // If not a personal_id conflict, throw immediately
        throw error;
      }
    }
    
    // All retries exhausted
    return NextResponse.json(
      ApiResponseBuilder.error(
        'PERSONAL_ID_CONFLICT',
        'Unable to create transaction after multiple attempts'
      ),
      { status: 409 }
    );
    
  } catch (error) {
    console.error('Transaction creation error:', error);
    return NextResponse.json(
      ApiResponseBuilder.error(
        'INTERNAL_ERROR',
        'Failed to create transaction'
      ),
      { status: 500 }
    );
  }
}

// GET endpoint for fetching transactions
export async function GET(request: NextRequest) {
  const authResult = await requireAuth(request);
  if ('error' in authResult) return authResult.error;
  
  const { user } = authResult;
  const { searchParams } = new URL(request.url);
  
  // Parse query parameters
  const page = parseInt(searchParams.get('page') || '1');
  const limit = parseInt(searchParams.get('limit') || '50');
  const account_id = searchParams.get('account_id');
  const category_id = searchParams.get('category_id');
  const type = searchParams.get('type');
  const start_date = searchParams.get('start_date');
  const end_date = searchParams.get('end_date');
  const keyword = searchParams.get('keyword');
  const sort_by = searchParams.get('sort_by') || 'date';
  const sort_order = searchParams.get('sort_order') || 'desc';
  
  try {
    // Build where clause
    const where: any = {
      user_id: user.user_id,
      deleted_at: null
    };
    
    if (account_id) where.account_id = account_id;
    if (category_id) where.category_id = category_id;
    if (type) where.type = type;
    if (start_date) where.date = { ...where.date, gte: new Date(start_date) };
    if (end_date) where.date = { ...where.date, lte: new Date(end_date) };
    if (keyword) {
      where.OR = [
        { description: { contains: keyword, mode: 'insensitive' } },
        { payee: { contains: keyword, mode: 'insensitive' } }
      ];
    }
    
    // Execute query
    const [transactions, total] = await Promise.all([
      prisma.transaction.findMany({
        where,
        include: {
          category: {
            select: { name: true, icon: true, color: true }
          },
          account: {
            select: { name: true }
          },
          labels: {
            include: {
              label: {
                select: { id: true, name: true, color: true }
              }
            }
          }
        },
        orderBy: { [sort_by]: sort_order },
        skip: (page - 1) * limit,
        take: limit
      }),
      prisma.transaction.count({ where })
    ]);
    
    // Transform response
    const transformedTransactions = transactions.map(tx => ({
      ...tx,
      personal_id: Number(tx.personal_id),
      amount: Number(tx.amount),
      labels: tx.labels.map(l => l.label)
    }));
    
    return NextResponse.json(
      ApiResponseBuilder.paginated(
        transformedTransactions,
        page,
        limit,
        total
      )
    );
    
  } catch (error) {
    console.error('Transaction fetch error:', error);
    return NextResponse.json(
      ApiResponseBuilder.error(
        'INTERNAL_ERROR',
        'Failed to fetch transactions'
      ),
      { status: 500 }
    );
  }
}
```

---

## 🎨 EXACT Frontend Context Implementation

### Step 1: Token Encryption Utility (src/utils/crypto.ts)

```typescript
const CRYPTO_KEY = 'finance-app-secret-key-2024'; // Change in production

export const tokenCrypto = {
  async encryptToken(token: string): Promise<string> {
    try {
      // Simple base64 encoding for development
      // Use proper encryption library in production
      const encrypted = btoa(token + '::' + CRYPTO_KEY);
      return encrypted;
    } catch (error) {
      console.error('Token encryption error:', error);
      return '';
    }
  },
  
  async decryptToken(encryptedToken: string): Promise<string | null> {
    try {
      const decrypted = atob(encryptedToken);
      const [token, key] = decrypted.split('::');
      
      if (key !== CRYPTO_KEY) {
        return null;
      }
      
      return token;
    } catch (error) {
      console.error('Token decryption error:', error);
      return null;
    }
  },
  
  clearKey(): void {
    // Clear any stored keys if needed
  }
};
```

### Step 2: App Constants (src/utils/constants.ts)

```typescript
export const APP_CONFIG = {
  storageKeys: {
    authToken: 'finance-app-auth-token',
    refreshToken: 'finance-app-refresh-token',
    userData: 'finance-app-user-data'
  },
  api: {
    baseUrl: process.env.NEXT_PUBLIC_API_URL || '/api/v1',
    timeout: 30000
  },
  ui: {
    toastDuration: 3000,
    modalAnimationDuration: 200
  },
  pagination: {
    defaultLimit: 50,
    maxLimit: 100
  }
};
```

### Step 3: Context Providers Wrapper (app/providers.tsx)

```tsx
'use client';

import { ReactNode } from 'react';
import { ToastProvider } from '@/context/ToastContext';
import { AuthStateProvider } from '@/context/AuthStateContext';
import { AuthProvider } from '@/context/AuthContext';
import { TransactionModalProvider } from '@/context/TransactionModalContext';

interface ProvidersProps {
  children: ReactNode;
}

// CRITICAL: Provider order matters due to dependencies
export function Providers({ children }: ProvidersProps) {
  return (
    <ToastProvider>
      <AuthStateProvider>
        <AuthProvider>
          <TransactionModalProvider>
            {children}
          </TransactionModalProvider>
        </AuthProvider>
      </AuthStateProvider>
    </ToastProvider>
  );
}
```

### Step 4: Auth Context Implementation (src/context/AuthContext.tsx)

```tsx
'use client';

import React, { createContext, useContext, useEffect, ReactNode } from 'react';
import { useRouter } from 'next/navigation';
import { tokenCrypto } from '@/utils/crypto';
import { APP_CONFIG } from '@/utils/constants';
import { authService } from '@/services/authService';
import { useToast } from './ToastContext';
import { useAuthState } from './AuthStateContext';

interface AuthContextValue {
  isAuthenticated: boolean;
  loading: boolean;
  login: (response: any) => Promise<void>;
  logout: () => Promise<void>;
}

const AuthContext = createContext<AuthContextValue | undefined>(undefined);

export function AuthProvider({ children }: { children: ReactNode }) {
  const router = useRouter();
  const { showToast } = useToast();
  const { isAuthenticated, setIsAuthenticated, loading, setLoading } = useAuthState();
  
  useEffect(() => {
    const checkAuth = async () => {
      setLoading(true);
      try {
        const encryptedToken = localStorage.getItem(APP_CONFIG.storageKeys.authToken);
        
        if (encryptedToken) {
          const token = await tokenCrypto.decryptToken(encryptedToken);
          setIsAuthenticated(!!token);
        } else {
          setIsAuthenticated(false);
        }
      } catch (error) {
        console.error('Auth check error:', error);
        setIsAuthenticated(false);
      } finally {
        setLoading(false);
      }
    };
    
    checkAuth();
  }, [setIsAuthenticated, setLoading]);
  
  const login = async (response: any) => {
    try {
      // Encrypt and store tokens
      const encryptedAccessToken = await tokenCrypto.encryptToken(response.access_token);
      const encryptedRefreshToken = await tokenCrypto.encryptToken(response.refresh_token);
      
      localStorage.setItem(APP_CONFIG.storageKeys.authToken, encryptedAccessToken);
      localStorage.setItem(APP_CONFIG.storageKeys.refreshToken, encryptedRefreshToken);
      localStorage.setItem(APP_CONFIG.storageKeys.userData, JSON.stringify(response.user));
      
      setIsAuthenticated(true);
      showToast('Login successful!', 'success');
      router.push('/');
    } catch (error) {
      console.error('Login error:', error);
      showToast('Login failed', 'error');
    }
  };
  
  const logout = async () => {
    try {
      await authService.logout();
    } catch (error) {
      console.error('Logout API error:', error);
    } finally {
      // Clear local storage
      localStorage.removeItem(APP_CONFIG.storageKeys.authToken);
      localStorage.removeItem(APP_CONFIG.storageKeys.refreshToken);
      localStorage.removeItem(APP_CONFIG.storageKeys.userData);
      
      tokenCrypto.clearKey();
      setIsAuthenticated(false);
      showToast('Logged out successfully', 'success');
      router.push('/login');
    }
  };
  
  return (
    <AuthContext.Provider value={{ isAuthenticated, loading, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}

export function useAuth() {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth must be used within AuthProvider');
  }
  return context;
}
```

---

## ⚠️ CRITICAL Implementation Rules

### 1. Amount Convention
```typescript
// ALWAYS follow this convention:
// - Expenses: NEGATIVE amounts
// - Income: POSITIVE amounts

// Example:
const expense = -100.00;  // $100 expense
const income = 500.00;    // $500 income

// When creating transaction:
const finalAmount = type === 'expense' 
  ? -Math.abs(amount) 
  : Math.abs(amount);
```

### 2. Personal ID Management
```typescript
// Personal IDs are user-specific sequential numbers
// Each user has their own sequence: 1, 2, 3...

// On conflict (409 error):
// 1. Fetch max personal_id from server
// 2. Increment and retry
// 3. Maximum 3 retries
```

### 3. Transfer Creation
```typescript
// Transfers MUST create TWO transactions:
// 1. EXPENSE in source account (negative amount)
// 2. INCOME in destination account (positive amount)
// Both linked by transfer_id
```

### 4. Context Provider Order
```tsx
// MUST maintain this exact order:
<ToastProvider>           // Level 1: No dependencies
  <AuthStateProvider>     // Level 2: Depends on Toast
    <AuthProvider>        // Level 3: Depends on AuthState and Toast
      <TransactionModalProvider> // Level 4: Depends on Auth
        {children}
      </TransactionModalProvider>
    </AuthProvider>
  </AuthStateProvider>
</ToastProvider>
```

### 5. Token Storage
```typescript
// ALWAYS encrypt tokens before localStorage:
const encrypted = await tokenCrypto.encryptToken(token);
localStorage.setItem(KEY, encrypted);

// ALWAYS decrypt when reading:
const encrypted = localStorage.getItem(KEY);
const token = await tokenCrypto.decryptToken(encrypted);
```

### 6. Default Data Seeding
```typescript
// On user registration, MUST create:
// - 70+ categories (hierarchical structure)
// - 3 default accounts (Cash, Checking, Savings)
// - All with proper personal_ids starting from 1
```

### 7. API Response Format
```typescript
// ALL endpoints must return this format:
// Success:
{
  success: true,
  data: {...},
  meta: {...}
}

// Error:
{
  success: false,
  error: {
    code: 'ERROR_CODE',
    message: 'Human readable message'
  }
}
```

### 8. Database Transactions
```typescript
// Use Prisma transactions for atomic operations:
await prisma.$transaction(async (tx) => {
  // All operations here succeed or fail together
});
```

---

## 🚀 Implementation Order

### Phase 1: Foundation (Day 1-2)
1. Initialize Next.js project with TypeScript
2. Setup Prisma with complete schema
3. Configure environment variables
4. Create folder structure exactly as specified

### Phase 2: Backend Core (Day 3-4)
1. Implement JWT authentication system
2. Create auth endpoints with auto-seeding
3. Implement requireAuth middleware
4. Create response builders and error handlers

### Phase 3: API Endpoints (Day 5-7)
1. Implement all account endpoints
2. Create transaction endpoints with personal_id logic
3. Add category management with hierarchy
4. Implement transfer creation with linked transactions
5. Add remaining endpoints (labels, groups, analytics)

### Phase 4: Frontend Foundation (Day 8-9)
1. Setup Tailwind and Shadcn UI
2. Create context providers in exact order
3. Implement service layer with token injection
4. Create authentication pages

### Phase 5: Dashboard Features (Day 10-12)
1. Build dashboard layout with sidebar
2. Implement account management UI
3. Create transaction list and modal
4. Add category tree view
5. Build analytics charts

### Phase 6: Testing & Polish (Day 13-14)
1. Write unit tests for critical functions
2. Add integration tests for API
3. Perform security audit
4. Optimize performance
5. Final testing and bug fixes

---

## 📝 Validation Checklist

Before considering the implementation complete, verify:

- [ ] Database has exactly 13 tables as specified
- [ ] User registration creates 70+ categories and 3 accounts
- [ ] JWT tokens are encrypted before storage
- [ ] Amounts follow negative/positive convention
- [ ] Transfers create two linked transactions
- [ ] Personal IDs are user-specific and sequential
- [ ] Context providers are in correct order
- [ ] All API responses follow standard format
- [ ] Authentication middleware protects all routes
- [ ] Account balances update correctly
- [ ] Transaction modal handles all CRUD operations
- [ ] Categories maintain parent-child relationships
- [ ] Drag-and-drop reordering works
- [ ] Search and filters work correctly
- [ ] Analytics calculate accurately

---

## 🆘 Common Issues and Solutions

### Issue 1: Personal ID Conflicts
```typescript
// Solution: Implement retry logic
let retries = 3;
while (retries > 0) {
  try {
    // Create with personal_id
  } catch (error) {
    if (error.code === 'P2002') {
      // Fetch new max and retry
      retries--;
      continue;
    }
    throw error;
  }
}
```

### Issue 2: Token Expiry
```typescript
// Solution: Implement token refresh
if (error.response?.status === 401) {
  const newToken = await refreshToken();
  // Retry request with new token
}
```

### Issue 3: Context Not Available
```typescript
// Solution: Check provider hierarchy
// Ensure components are wrapped in providers
// Check that hooks are used inside provider tree
```

### Issue 4: Database Migration Errors
```bash
# Solution: Reset and re-migrate
npx prisma migrate reset
npx prisma generate
npx prisma migrate dev
```

---

## 🎯 Final Notes for AI Agent

1. **Follow this guide exactly** - Do not improvise or optimize unless fixing errors
2. **Test each phase** before moving to next
3. **Keep console.logs** during development for debugging
4. **Use TypeScript strictly** - No 'any' types except where specified
5. **Implement error handling** at every level
6. **Follow naming conventions** exactly as shown
7. **Create all files** in exact paths specified
8. **Use exact dependencies** versions when possible
9. **Test with real data** not just mock data
10. **Document any deviations** if absolutely necessary

This implementation guide contains everything needed to build the complete application. Follow it step by step, and the application will work exactly as the original specification requires.
