# 01. Project Structure and Setup

## ⚠️ IMPORTANT: After completing this document:
1. Run `npx tsc --noEmit` - Must have 0 errors
2. Run `npm run lint` - Must have 0 errors  
3. ✅ Check Phase 1 checklist in [Document 10](./10_IMPLEMENTATION_CHECKLIST.md#phase-1-project-setup)
4. 🧪 Write tests as per [Document 14](./14_TESTING_REQUIREMENTS.md)

## 📂 Complete Project Structure

Create this exact folder structure:

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
│   │       ├── budgets/
│   │       │   ├── route.ts
│   │       │   ├── [id]/
│   │       │   │   └── route.ts
│   │       │   └── status/
│   │       │       └── route.ts
│   │       └── analytics/
│   │           ├── summary/
│   │           │   └── route.ts
│   │           ├── trends/
│   │           │   └── route.ts
│   │           ├── cashflow/
│   │           │   └── route.ts
│   │           └── expenses-by-category/
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
│   │   │   ├── Table.tsx
│   │   │   ├── Skeleton.tsx
│   │   │   ├── Spinner.tsx
│   │   │   └── ErrorBoundary.tsx
│   │   ├── layout/
│   │   │   ├── AppShell.tsx
│   │   │   ├── Header.tsx
│   │   │   ├── Sidebar.tsx
│   │   │   ├── Footer.tsx
│   │   │   ├── MobileNav.tsx
│   │   │   └── RequireAuth.tsx
│   │   ├── features/
│   │   │   ├── TransactionModal.tsx
│   │   │   ├── TransactionForm.tsx
│   │   │   ├── TransactionList.tsx
│   │   │   ├── AccountCard.tsx
│   │   │   ├── AccountForm.tsx
│   │   │   ├── CategoryTree.tsx
│   │   │   ├── CategoryForm.tsx
│   │   │   ├── BudgetProgress.tsx
│   │   │   ├── QuickActions.tsx
│   │   │   └── QuickTransactionModal.tsx
│   │   └── charts/
│   │       ├── PieChart.tsx
│   │       ├── LineChart.tsx
│   │       ├── BarChart.tsx
│   │       └── DonutChart.tsx
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
│   │   ├── budgetService.ts
│   │   └── analyticsService.ts
│   │
│   ├── hooks/                         # Custom React hooks
│   │   ├── useAuth.ts
│   │   ├── useAccounts.ts
│   │   ├── useTransactions.ts
│   │   ├── useCategories.ts
│   │   ├── useCategoryData.ts
│   │   ├── useQuickTransactions.ts
│   │   ├── useDebounce.ts
│   │   ├── usePagination.ts
│   │   └── useLocalStorage.ts
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
│   │   │   ├── category.ts          # Category schemas
│   │   │   ├── transfer.ts          # Transfer schemas
│   │   │   └── common.ts            # Common schemas
│   │   └── api/
│   │       ├── response.ts          # Response builder
│   │       ├── errors.ts            # Error handlers
│   │       └── pagination.ts        # Pagination utilities
│   │
│   ├── types/                         # TypeScript type definitions
│   │   ├── api.ts                    # API types
│   │   ├── models.ts                 # Database models
│   │   ├── forms.ts                  # Form types
│   │   ├── services.ts              # Service types
│   │   └── global.d.ts              # Global types
│   │
│   ├── utils/                         # Utility functions
│   │   ├── crypto.ts                 # Token encryption
│   │   ├── format.ts                 # Formatters (currency, date)
│   │   ├── date.ts                   # Date utilities
│   │   ├── validation.ts            # Validation helpers
│   │   └── constants.ts              # App constants
│   │
│   ├── data/                          # Static data
│   │   ├── default_categories.json   # 70+ default categories
│   │   ├── default_accounts.json     # 3 default accounts
│   │   └── icons.ts                  # Icon mappings
│   │
│   └── styles/                        # Additional styles
│       ├── components.css
│       └── utilities.css
│
├── prisma/
│   ├── schema.prisma                 # Database schema
│   ├── migrations/                   # Migration files
│   └── seed.ts                       # Database seeder
│
├── public/                            # Static assets
│   ├── icons/
│   ├── images/
│   └── fonts/
│
├── tests/                             # Test files
│   ├── unit/
│   │   ├── services/
│   │   ├── utils/
│   │   └── hooks/
│   ├── integration/
│   │   ├── api/
│   │   └── db/
│   └── e2e/
│       ├── auth.spec.ts
│       ├── transactions.spec.ts
│       └── accounts.spec.ts
│
├── .env.example                       # Environment variables template
├── .env.local                         # Local environment variables (gitignored)
├── .gitignore
├── next.config.js                     # Next.js configuration
├── tailwind.config.ts                 # Tailwind CSS config
├── tsconfig.json                      # TypeScript config
├── package.json                       # Dependencies
├── README.md                          # Documentation
└── docker-compose.yml                 # Docker setup (optional)
```

## 🚀 Project Initialization

### Step 1: Create Next.js Project

```bash
# Create new Next.js project with TypeScript and Tailwind
npx create-next-app@latest finance-app --typescript --tailwind --app --src-dir --import-alias "@/*"

# Navigate to project
cd finance-app
```

### Step 2: Install Core Dependencies

```bash
# Core dependencies
npm install \
  @prisma/client@^5.8.0 \
  prisma@^5.8.0 \
  jose@^5.2.0 \
  bcryptjs@^2.4.3 \
  zod@^3.22.0 \
  zustand@^4.5.0 \
  axios@^1.6.0 \
  date-fns@^3.0.0 \
  clsx@^2.1.0 \
  tailwind-merge@^2.2.0

# UI libraries
npm install \
  @radix-ui/react-dialog \
  @radix-ui/react-dropdown-menu \
  @radix-ui/react-label \
  @radix-ui/react-select \
  @radix-ui/react-tabs \
  @radix-ui/react-toast \
  lucide-react@^0.300.0 \
  recharts@^2.10.0 \
  react-hook-form@^7.48.0 \
  @hookform/resolvers@^3.3.0

# Development dependencies
npm install -D \
  @types/bcryptjs@^2.4.6 \
  @types/node@^20.10.0 \
  @types/react@^18.2.0 \
  @types/react-dom@^18.2.0
```

### Step 3: Install Additional Development Dependencies for Strict Mode

For strict TypeScript and ESLint configuration (RECOMMENDED), install these packages:

```bash
# Install ESLint and related packages for strict code quality
npm install -D \
  @typescript-eslint/parser \
  @typescript-eslint/eslint-plugin \
  eslint-plugin-react \
  eslint-plugin-react-hooks \
  eslint-plugin-jsx-a11y \
  eslint-plugin-import \
  eslint-plugin-security \
  eslint-plugin-unused-imports \
  eslint-plugin-no-secrets \
  eslint-import-resolver-typescript \
  eslint-config-prettier \
  prettier

# After installation, copy the strict configurations from 01A_STRICT_CONFIGS.md
```

### Step 4: Environment Variables

Create `.env.local` file:

```env
# Database
DATABASE_URL="postgresql://postgres:password@localhost:5432/finance_db"

# JWT Secrets (generate strong secrets in production)
JWT_ACCESS_SECRET="your-super-secret-jwt-access-key-change-in-production"
JWT_REFRESH_SECRET="your-super-secret-jwt-refresh-key-change-in-production"

# App Configuration
NEXT_PUBLIC_API_URL="http://localhost:3000/api/v1"
NEXT_PUBLIC_APP_URL="http://localhost:3000"

# Optional: External Services
SENDGRID_API_KEY=""
AWS_S3_BUCKET=""
SENTRY_DSN=""
```

### Step 4: TypeScript Configuration

> **⚠️ IMPORTANT**: For a production-ready, bug-minimizing configuration with strict type checking and comprehensive ESLint rules, see **[01A_STRICT_CONFIGS.md](./01A_STRICT_CONFIGS.md)**. The configuration below is a basic starting point.

Update `tsconfig.json` (basic version):

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "lib": ["dom", "dom.iterable", "esnext"],
    "allowJs": true,
    "skipLibCheck": true,
    "strict": true,
    "noEmit": true,
    "esModuleInterop": true,
    "module": "esnext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "incremental": true,
    "plugins": [
      {
        "name": "next"
      }
    ],
    "paths": {
      "@/*": ["./src/*"],
      "@/components/*": ["./src/components/*"],
      "@/lib/*": ["./src/lib/*"],
      "@/services/*": ["./src/services/*"],
      "@/hooks/*": ["./src/hooks/*"],
      "@/context/*": ["./src/context/*"],
      "@/types/*": ["./src/types/*"],
      "@/utils/*": ["./src/utils/*"],
      "@/data/*": ["./src/data/*"],
      "@/styles/*": ["./src/styles/*"]
    }
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx", ".next/types/**/*.ts"],
  "exclude": ["node_modules"]
}
```

**🛡️ For production use, implement the strict configuration from [01A_STRICT_CONFIGS.md](./01A_STRICT_CONFIGS.md) which includes:**
- All TypeScript strict flags enabled
- No implicit any, strict null checks
- Comprehensive ESLint rules
- Security and accessibility checks
- Import ordering and unused code detection

### Step 5: Tailwind Configuration

Update `tailwind.config.ts`:

```typescript
import type { Config } from 'tailwindcss'

const config: Config = {
  darkMode: ["class"],
  content: [
    './pages/**/*.{ts,tsx}',
    './components/**/*.{ts,tsx}',
    './app/**/*.{ts,tsx}',
    './src/**/*.{ts,tsx}',
  ],
  theme: {
    container: {
      center: true,
      padding: "2rem",
      screens: {
        "2xl": "1400px",
      },
    },
    extend: {
      colors: {
        border: "hsl(var(--border))",
        input: "hsl(var(--input))",
        ring: "hsl(var(--ring))",
        background: "hsl(var(--background))",
        foreground: "hsl(var(--foreground))",
        primary: {
          DEFAULT: "hsl(var(--primary))",
          foreground: "hsl(var(--primary-foreground))",
        },
        secondary: {
          DEFAULT: "hsl(var(--secondary))",
          foreground: "hsl(var(--secondary-foreground))",
        },
        destructive: {
          DEFAULT: "hsl(var(--destructive))",
          foreground: "hsl(var(--destructive-foreground))",
        },
        muted: {
          DEFAULT: "hsl(var(--muted))",
          foreground: "hsl(var(--muted-foreground))",
        },
        accent: {
          DEFAULT: "hsl(var(--accent))",
          foreground: "hsl(var(--accent-foreground))",
        },
        popover: {
          DEFAULT: "hsl(var(--popover))",
          foreground: "hsl(var(--popover-foreground))",
        },
        card: {
          DEFAULT: "hsl(var(--card))",
          foreground: "hsl(var(--card-foreground))",
        },
        // Custom colors for finance app
        income: "#10b981",
        expense: "#ef4444",
      },
      borderRadius: {
        lg: "var(--radius)",
        md: "calc(var(--radius) - 2px)",
        sm: "calc(var(--radius) - 4px)",
      },
      keyframes: {
        "accordion-down": {
          from: { height: "0" },
          to: { height: "var(--radix-accordion-content-height)" },
        },
        "accordion-up": {
          from: { height: "var(--radix-accordion-content-height)" },
          to: { height: "0" },
        },
      },
      animation: {
        "accordion-down": "accordion-down 0.2s ease-out",
        "accordion-up": "accordion-up 0.2s ease-out",
      },
    },
  },
  plugins: [require("tailwindcss-animate")],
}

export default config
```

### Step 6: Global CSS

Update `app/globals.css`:

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  :root {
    --background: 0 0% 100%;
    --foreground: 222.2 84% 4.9%;
    --card: 0 0% 100%;
    --card-foreground: 222.2 84% 4.9%;
    --popover: 0 0% 100%;
    --popover-foreground: 222.2 84% 4.9%;
    --primary: 221.2 83.2% 53.3%;
    --primary-foreground: 210 40% 98%;
    --secondary: 210 40% 96.1%;
    --secondary-foreground: 222.2 47.4% 11.2%;
    --muted: 210 40% 96.1%;
    --muted-foreground: 215.4 16.3% 46.9%;
    --accent: 210 40% 96.1%;
    --accent-foreground: 222.2 47.4% 11.2%;
    --destructive: 0 84.2% 60.2%;
    --destructive-foreground: 210 40% 98%;
    --border: 214.3 31.8% 91.4%;
    --input: 214.3 31.8% 91.4%;
    --ring: 221.2 83.2% 53.3%;
    --radius: 0.5rem;
  }

  .dark {
    --background: 222.2 84% 4.9%;
    --foreground: 210 40% 98%;
    --card: 222.2 84% 4.9%;
    --card-foreground: 210 40% 98%;
    --popover: 222.2 84% 4.9%;
    --popover-foreground: 210 40% 98%;
    --primary: 217.2 91.2% 59.8%;
    --primary-foreground: 222.2 47.4% 11.2%;
    --secondary: 217.2 32.6% 17.5%;
    --secondary-foreground: 210 40% 98%;
    --muted: 217.2 32.6% 17.5%;
    --muted-foreground: 215 20.2% 65.1%;
    --accent: 217.2 32.6% 17.5%;
    --accent-foreground: 210 40% 98%;
    --destructive: 0 62.8% 30.6%;
    --destructive-foreground: 210 40% 98%;
    --border: 217.2 32.6% 17.5%;
    --input: 217.2 32.6% 17.5%;
    --ring: 224.3 76.3% 48%;
  }
}

@layer base {
  * {
    @apply border-border;
  }
  body {
    @apply bg-background text-foreground;
  }
}

/* Custom utility classes */
@layer utilities {
  .text-income {
    @apply text-green-500;
  }
  
  .text-expense {
    @apply text-red-500;
  }
  
  .bg-income {
    @apply bg-green-500;
  }
  
  .bg-expense {
    @apply bg-red-500;
  }
}
```

### Step 7: Next.js Configuration

Update `next.config.js`:

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
  swcMinify: true,
  
  // API configuration
  async headers() {
    return [
      {
        source: '/api/:path*',
        headers: [
          { key: 'Access-Control-Allow-Credentials', value: 'true' },
          { key: 'Access-Control-Allow-Origin', value: '*' },
          { key: 'Access-Control-Allow-Methods', value: 'GET,POST,PUT,DELETE,OPTIONS' },
          { key: 'Access-Control-Allow-Headers', value: 'Accept, Content-Type, Authorization' },
        ],
      },
    ]
  },
  
  // Environment variables to expose to client
  env: {
    NEXT_PUBLIC_API_URL: process.env.NEXT_PUBLIC_API_URL,
    NEXT_PUBLIC_APP_URL: process.env.NEXT_PUBLIC_APP_URL,
  },
  
  // Image optimization
  images: {
    domains: ['localhost', 'your-cdn.com'],
    formats: ['image/avif', 'image/webp'],
  },
  
  // Experimental features
  experimental: {
    serverActions: {
      bodySizeLimit: '10mb',
    },
  },
}

module.exports = nextConfig
```

### Step 8: Package.json Scripts

Update `package.json`:

```json
{
  "name": "finance-app",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint",
    "lint:fix": "next lint --fix",
    "type-check": "tsc --noEmit",
    "type-check:watch": "tsc --noEmit --watch",
    "validate": "npm run type-check && npm run lint",
    "validate:fix": "npm run type-check && npm run lint:fix",
    "format": "prettier --write \"**/*.{ts,tsx,json,md}\"",
    "format:check": "prettier --check \"**/*.{ts,tsx,json,md}\"",
    "pre-commit": "npm run validate && npm run format:check",
    "db:generate": "prisma generate",
    "db:migrate": "prisma migrate dev",
    "db:push": "prisma db push",
    "db:seed": "prisma db seed",
    "db:reset": "prisma migrate reset",
    "db:studio": "prisma studio",
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage",
    "test:e2e": "playwright test",
    "analyze": "ANALYZE=true next build"
  },
  "prisma": {
    "seed": "ts-node --compiler-options {\"module\":\"CommonJS\"} prisma/seed.ts"
  }
}
```

### IMPORTANT: Validation Commands

These commands MUST pass before considering implementation complete:

```bash
# Run TypeScript type checking (MUST have 0 errors)
npm run type-check
# or
npx tsc --noEmit

# Run ESLint checking (MUST have 0 errors)
npm run lint
# or to auto-fix fixable issues
npm run lint:fix

# Run both validations at once
npm run validate

# Check code formatting
npm run format:check

# Run all pre-commit checks
npm run pre-commit
```

## ✅ Setup Verification Checklist

- [ ] Next.js 15+ project created with TypeScript
- [ ] All dependencies installed
- [ ] ESLint dev dependencies installed (for strict mode)
- [ ] Environment variables configured
- [ ] Database connection string set
- [ ] TypeScript paths configured
- [ ] **Strict tsconfig.json applied** (from 01A_STRICT_CONFIGS.md)
- [ ] **Strict .eslintrc.json configured** (from 01A_STRICT_CONFIGS.md)
- [ ] **Prettier configured** (.prettierrc and .prettierignore)
- [ ] Tailwind CSS configured
- [ ] Global styles added
- [ ] Next.js config updated
- [ ] All folders created as specified
- [ ] Package.json scripts added
- [ ] **`npm run type-check` passes with 0 errors**
- [ ] **`npm run lint` passes with 0 errors**
- [ ] **`npm run build` completes successfully**

## 🎯 Next Steps

Once the project structure is set up:
1. Continue to [02_DATABASE_SCHEMA.md](./02_DATABASE_SCHEMA.md) to set up the database
2. Implement authentication system as described in [03_AUTHENTICATION_SYSTEM.md](./03_AUTHENTICATION_SYSTEM.md)
3. Build API endpoints following [04_API_IMPLEMENTATION.md](./04_API_IMPLEMENTATION.md)
