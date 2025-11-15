# WalletFlow - Quick Start Guide

Complete guide to set up and run the WalletFlow application locally.

## Prerequisites

Before you begin, ensure you have the following installed:

- **Node.js** 20.x or higher ([Download](https://nodejs.org/))
- **PostgreSQL** 15.x or higher ([Download](https://www.postgresql.org/download/))
- **Git** ([Download](https://git-scm.com/downloads))
- **npm** or **pnpm** (comes with Node.js)

## 🚀 Quick Setup (5 minutes)

### Step 1: Create Next.js Project

```bash
# Create new Next.js project with TypeScript
npx create-next-app@latest walletflow --typescript --tailwind --app --no-src-dir

# Navigate to project
cd walletflow
```

When prompted, select:
- ✅ TypeScript
- ✅ ESLint
- ✅ Tailwind CSS
- ✅ App Router
- ❌ src/ directory
- ✅ Import alias (@/*)

### Step 2: Install Dependencies

```bash
# Install core dependencies
npm install @prisma/client next-auth@beta @auth/prisma-adapter bcrypt zod

# Install UI dependencies
npm install @tanstack/react-query zustand clsx tailwind-merge lucide-react
npm install @radix-ui/react-dialog @radix-ui/react-dropdown-menu @radix-ui/react-select
npm install react-hook-form @hookform/resolvers react-hot-toast
npm install recharts date-fns papaparse

# Install file upload
npm install uploadthing @uploadthing/react sharp

# Install email
npm install resend react-email @react-email/components

# Install monitoring
npm install @vercel/analytics @sentry/nextjs

# Install dev dependencies
npm install -D prisma @types/bcrypt @types/papaparse tsx
npm install -D @testing-library/react @testing-library/jest-dom vitest @vitejs/plugin-react
npm install -D playwright @playwright/test
```

### Step 3: Set Up PostgreSQL Database

#### Option A: Local PostgreSQL

```bash
# Create database
createdb walletflow

# Or using psql
psql postgres
CREATE DATABASE walletflow;
\q
```

#### Option B: Cloud PostgreSQL (Recommended for Development)

**Neon.tech (Free tier):**
1. Go to [neon.tech](https://neon.tech)
2. Sign up and create a project
3. Copy the connection string

**Supabase (Free tier):**
1. Go to [supabase.com](https://supabase.com)
2. Create a project
3. Go to Settings → Database → Connection String
4. Copy the connection string

**Vercel Postgres:**
1. Go to [vercel.com/storage/postgres](https://vercel.com/storage/postgres)
2. Create a database
3. Copy the connection string

### Step 4: Initialize Prisma

```bash
# Initialize Prisma
npx prisma init
```

This creates:
- `prisma/schema.prisma` - Database schema
- `.env` - Environment variables

### Step 5: Configure Environment Variables

Create `.env.local` in the root directory:

```bash
# Database
DATABASE_URL="postgresql://username:password@localhost:5432/walletflow"

# NextAuth (generate with: openssl rand -base64 32)
NEXTAUTH_URL="http://localhost:3000"
NEXTAUTH_SECRET="your-super-secret-key-change-this-in-production"

# OAuth Providers (Optional - for Google/Facebook login)
GOOGLE_CLIENT_ID="your-google-client-id"
GOOGLE_CLIENT_SECRET="your-google-client-secret"

# File Upload - UploadThing (Optional)
UPLOADTHING_SECRET="your-uploadthing-secret"
UPLOADTHING_APP_ID="your-uploadthing-app-id"

# Email - Resend (Optional)
RESEND_API_KEY="your-resend-api-key"

# Monitoring (Optional)
NEXT_PUBLIC_SENTRY_DSN="your-sentry-dsn"
```

**Generate NEXTAUTH_SECRET:**
```bash
openssl rand -base64 32
```

### Step 6: Create Prisma Schema

Replace the content of `prisma/schema.prisma`:

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id                String    @id @default(uuid())
  email             String    @unique
  passwordHash      String?   @map("password_hash")
  firstName         String?   @map("first_name")
  lastName          String?   @map("last_name")
  avatarUrl         String?   @map("avatar_url")
  defaultCurrency   String    @default("USD") @map("default_currency")
  timezone          String    @default("UTC")
  locale            String    @default("en")
  emailVerified     Boolean   @default(false) @map("email_verified")
  twoFactorEnabled  Boolean   @default(false) @map("two_factor_enabled")
  twoFactorSecret   String?   @map("two_factor_secret")
  oauthProvider     String?   @map("oauth_provider")
  oauthId           String?   @map("oauth_id")
  lastLoginAt       DateTime? @map("last_login_at")
  createdAt         DateTime  @default(now()) @map("created_at")
  updatedAt         DateTime  @updatedAt @map("updated_at")
  deletedAt         DateTime? @map("deleted_at")

  accounts      Account[]
  transactions  Transaction[]
  categories    Category[]
  budgets       Budget[]
  goals         Goal[]
  
  @@map("users")
}

enum AccountType {
  checking
  savings
  credit_card
  cash
  loan
  investment
  crypto
  other
}

model Account {
  id              String      @id @default(uuid())
  userId          String      @map("user_id")
  name            String
  type            AccountType
  currency        String      @default("USD")
  initialBalance  Decimal     @default(0) @map("initial_balance") @db.Decimal(15, 2)
  currentBalance  Decimal     @default(0) @map("current_balance") @db.Decimal(15, 2)
  description     String?
  icon            String?
  color           String?
  isActive        Boolean     @default(true) @map("is_active")
  displayOrder    Int         @default(0) @map("display_order")
  createdAt       DateTime    @default(now()) @map("created_at")
  updatedAt       DateTime    @updatedAt @map("updated_at")
  archivedAt      DateTime?   @map("archived_at")

  user              User          @relation(fields: [userId], references: [id], onDelete: Cascade)
  transactions      Transaction[] @relation("AccountTransactions")
  transfersTo       Transaction[] @relation("ToAccount")
  
  @@map("accounts")
}

enum TransactionType {
  income
  expense
  transfer
}

model Transaction {
  id                      String          @id @default(uuid())
  userId                  String          @map("user_id")
  accountId               String          @map("account_id")
  categoryId              String?         @map("category_id")
  toAccountId             String?         @map("to_account_id")
  type                    TransactionType
  amount                  Decimal         @db.Decimal(15, 2)
  currency                String
  exchangeRate            Decimal         @default(1.0) @map("exchange_rate") @db.Decimal(15, 6)
  description             String?
  notes                   String?
  transactionDate         DateTime        @map("transaction_date") @db.Date
  recurringTransactionId  String?         @map("recurring_transaction_id")
  createdAt               DateTime        @default(now()) @map("created_at")
  updatedAt               DateTime        @updatedAt @map("updated_at")
  deletedAt               DateTime?       @map("deleted_at")

  user      User      @relation(fields: [userId], references: [id], onDelete: Cascade)
  account   Account   @relation("AccountTransactions", fields: [accountId], references: [id], onDelete: Restrict)
  category  Category? @relation(fields: [categoryId], references: [id], onDelete: SetNull)
  toAccount Account?  @relation("ToAccount", fields: [toAccountId], references: [id], onDelete: Restrict)
  
  @@map("transactions")
}

enum CategoryType {
  income
  expense
}

model Category {
  id           String        @id @default(uuid())
  userId       String?       @map("user_id")
  parentId     String?       @map("parent_id")
  name         String
  type         CategoryType
  icon         String?
  color        String?
  isSystem     Boolean       @default(false) @map("is_system")
  displayOrder Int           @default(0) @map("display_order")
  createdAt    DateTime      @default(now()) @map("created_at")
  updatedAt    DateTime      @updatedAt @map("updated_at")
  archivedAt   DateTime?     @map("archived_at")

  user         User?         @relation(fields: [userId], references: [id], onDelete: Cascade)
  transactions Transaction[]
  
  @@map("categories")
}

model Budget {
  id             String   @id @default(uuid())
  userId         String   @map("user_id")
  name           String
  amount         Decimal  @db.Decimal(15, 2)
  period         String   // 'weekly', 'monthly', 'yearly', 'custom'
  startDate      DateTime @map("start_date") @db.Date
  endDate        DateTime? @map("end_date") @db.Date
  currency       String
  isRecurring    Boolean  @default(false) @map("is_recurring")
  alertThreshold Decimal  @default(80.00) @map("alert_threshold") @db.Decimal(5, 2)
  createdAt      DateTime @default(now()) @map("created_at")
  updatedAt      DateTime @updatedAt @map("updated_at")
  archivedAt     DateTime? @map("archived_at")

  user User @relation(fields: [userId], references: [id], onDelete: Cascade)
  
  @@map("budgets")
}

model Goal {
  id            String   @id @default(uuid())
  userId        String   @map("user_id")
  name          String
  targetAmount  Decimal  @map("target_amount") @db.Decimal(15, 2)
  currentAmount Decimal  @default(0) @map("current_amount") @db.Decimal(15, 2)
  currency      String
  deadline      DateTime? @db.Date
  priority      String   @default("medium") // 'low', 'medium', 'high'
  description   String?
  icon          String?
  isCompleted   Boolean  @default(false) @map("is_completed")
  completedAt   DateTime? @map("completed_at")
  createdAt     DateTime @default(now()) @map("created_at")
  updatedAt     DateTime @updatedAt @map("updated_at")
  archivedAt    DateTime? @map("archived_at")

  user User @relation(fields: [userId], references: [id], onDelete: Cascade)
  
  @@map("goals")
}
```

### Step 7: Create Prisma Client Singleton

Create `lib/prisma.ts`:

```typescript
import { PrismaClient } from '@prisma/client';

const globalForPrisma = globalThis as unknown as {
  prisma: PrismaClient | undefined;
};

export const prisma = globalForPrisma.prisma ?? new PrismaClient();

if (process.env.NODE_ENV !== 'production') globalForPrisma.prisma = prisma;
```

### Step 8: Run Database Migration

```bash
# Create and run migration
npx prisma migrate dev --name init

# Generate Prisma Client
npx prisma generate
```

### Step 9: Create Database Seeder

Create `prisma/seed.ts`:

```typescript
import { PrismaClient } from '@prisma/client';
import bcrypt from 'bcrypt';

const prisma = new PrismaClient();

async function main() {
  console.log('🌱 Seeding database...');

  // Create demo user
  const hashedPassword = await bcrypt.hash('Demo123!', 12);
  
  const user = await prisma.user.upsert({
    where: { email: 'demo@walletflow.com' },
    update: {},
    create: {
      email: 'demo@walletflow.com',
      passwordHash: hashedPassword,
      firstName: 'Demo',
      lastName: 'User',
      emailVerified: true,
      defaultCurrency: 'USD',
    },
  });

  console.log('✅ Created demo user:', user.email);

  // Create system categories
  const categories = [
    { name: 'Salary', type: 'income', icon: '💼', color: '#10B981' },
    { name: 'Food & Dining', type: 'expense', icon: '🍽️', color: '#EF4444' },
    { name: 'Transportation', type: 'expense', icon: '🚗', color: '#F59E0B' },
    { name: 'Shopping', type: 'expense', icon: '🛍️', color: '#EC4899' },
    { name: 'Bills & Utilities', type: 'expense', icon: '📄', color: '#3B82F6' },
  ];

  for (const cat of categories) {
    await prisma.category.upsert({
      where: { 
        userId_name_type: { 
          userId: user.id, 
          name: cat.name, 
          type: cat.type as any 
        } 
      },
      update: {},
      create: {
        ...cat,
        userId: user.id,
        isSystem: true,
      },
    });
  }

  console.log('✅ Created system categories');

  // Create demo account
  await prisma.account.create({
    data: {
      userId: user.id,
      name: 'Main Checking',
      type: 'checking',
      currency: 'USD',
      initialBalance: 5000,
      currentBalance: 5000,
      icon: '🏦',
      color: '#4F46E5',
    },
  });

  console.log('✅ Created demo account');
  console.log('🎉 Seeding completed!');
}

main()
  .catch((e) => {
    console.error('❌ Seeding failed:', e);
    process.exit(1);
  })
  .finally(async () => {
    await prisma.$disconnect();
  });
```

Add seed script to `package.json`:

```json
{
  "prisma": {
    "seed": "tsx prisma/seed.ts"
  }
}
```

Run the seeder:

```bash
npx prisma db seed
```

### Step 10: Set Up NextAuth

Create `lib/auth.ts`:

```typescript
import { NextAuthOptions } from 'next-auth';
import CredentialsProvider from 'next-auth/providers/credentials';
import GoogleProvider from 'next-auth/providers/google';
import { PrismaAdapter } from '@auth/prisma-adapter';
import { prisma } from '@/lib/prisma';
import bcrypt from 'bcrypt';

export const authOptions: NextAuthOptions = {
  adapter: PrismaAdapter(prisma) as any,
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
  },
  callbacks: {
    async jwt({ token, user }) {
      if (user) {
        token.id = user.id;
      }
      return token;
    },
    async session({ session, token }) {
      if (session.user) {
        (session.user as any).id = token.id;
      }
      return session;
    },
  },
};
```

Create `app/api/auth/[...nextauth]/route.ts`:

```typescript
import NextAuth from 'next-auth';
import { authOptions } from '@/lib/auth';

const handler = NextAuth(authOptions);
export { handler as GET, handler as POST };
```

### Step 11: Copy Design System Files

Copy the following files to your project:

1. **Design Tokens**: Copy `DESIGN_TOKENS.css` to `app/globals.css`
2. **Tailwind Config**: Replace `tailwind.config.js` with provided config
3. **Components**: Create components from `COMPONENT_IMPLEMENTATION_GUIDE.md`

### Step 12: Create First API Route

Create `app/api/transactions/route.ts`:

```typescript
import { NextRequest, NextResponse } from 'next/server';
import { getServerSession } from 'next-auth';
import { authOptions } from '@/lib/auth';
import { prisma } from '@/lib/prisma';

export async function GET(request: NextRequest) {
  try {
    const session = await getServerSession(authOptions);
    
    if (!session?.user?.id) {
      return NextResponse.json(
        { success: false, error: 'Unauthorized' },
        { status: 401 }
      );
    }

    const transactions = await prisma.transaction.findMany({
      where: {
        userId: (session.user as any).id,
        deletedAt: null,
      },
      include: {
        account: true,
        category: true,
      },
      orderBy: {
        transactionDate: 'desc',
      },
      take: 50,
    });

    return NextResponse.json({
      success: true,
      data: transactions,
    });
  } catch (error) {
    console.error('Error fetching transactions:', error);
    return NextResponse.json(
      { success: false, error: 'Internal server error' },
      { status: 500 }
    );
  }
}
```

### Step 13: Start Development Server

```bash
# Start Next.js development server
npm run dev

# Open browser
# http://localhost:3000
```

You should see the Next.js welcome page!

### Step 14: Open Prisma Studio (Database GUI)

```bash
# In a new terminal
npx prisma studio

# Opens at http://localhost:5555
```

---

## 📝 Available Scripts

```bash
# Development
npm run dev                  # Start dev server

# Database
npm run prisma:generate      # Generate Prisma Client
npm run prisma:migrate       # Create migration
npm run prisma:studio        # Open database GUI
npm run prisma:seed          # Run database seeder

# Build & Production
npm run build                # Build for production
npm run start                # Start production server

# Testing
npm run test                 # Run unit tests
npm run test:e2e             # Run E2E tests

# Code Quality
npm run lint                 # Lint code
npm run type-check           # Type checking
```

---

## 🎯 Next Steps

1. **Create Login Page**: `app/login/page.tsx`
2. **Create Dashboard**: `app/dashboard/page.tsx`
3. **Build Components**: Follow `COMPONENT_IMPLEMENTATION_GUIDE.md`
4. **Add More API Routes**: See `PRODUCT_REQUIREMENTS.md` Section 5
5. **Implement Features**: Follow development phases

---

## 🐛 Troubleshooting

### Database Connection Issues

```bash
# Test database connection
npx prisma db push

# Reset database
npx prisma migrate reset
```

### Prisma Client Not Found

```bash
# Regenerate Prisma Client
npx prisma generate
```

### NextAuth Session Issues

1. Make sure `NEXTAUTH_URL` matches your dev server
2. Check `NEXTAUTH_SECRET` is set
3. Clear browser cookies and try again

### Port Already in Use

```bash
# Kill process on port 3000
npx kill-port 3000

# Or use different port
PORT=3001 npm run dev
```

---

## 📚 Useful Resources

- [Next.js Documentation](https://nextjs.org/docs)
- [Prisma Documentation](https://www.prisma.io/docs)
- [NextAuth.js Documentation](https://next-auth.js.org)
- [Tailwind CSS Documentation](https://tailwindcss.com/docs)
- [Product Requirements](./PRODUCT_REQUIREMENTS.md)
- [Component Guide](./COMPONENT_IMPLEMENTATION_GUIDE.md)

---

## 🚀 Deploy to Production

### Deploy to Vercel (Recommended)

```bash
# Install Vercel CLI
npm i -g vercel

# Login
vercel login

# Deploy
vercel

# Deploy to production
vercel --prod
```

Set environment variables in Vercel Dashboard.

### Deploy to Railway

```bash
# Install Railway CLI
npm i -g @railway/cli

# Login
railway login

# Deploy
railway up
```

---

**You're all set! Start building your personal finance app! 🎉**
