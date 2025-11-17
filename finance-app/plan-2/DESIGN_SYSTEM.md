# WalletFlow Design System

**Version**: 1.0
**Last Updated**: November 18, 2025

## Table of Contents
1. [Color Palette](#color-palette)
2. [Typography](#typography)
3. [Spacing System](#spacing-system)
4. [Border Radius](#border-radius)
5. [Shadows](#shadows)
6. [Transitions & Animations](#transitions--animations)
7. [Breakpoints](#breakpoints)
8. [Z-Index Layers](#z-index-layers)
9. [Tailwind Configuration](#tailwind-configuration)
10. [Usage Examples](#usage-examples)

---

## 1. Color Palette

### 1.1 Primary Colors

Brand colors used throughout the application for primary actions, links, and key UI elements.

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
```

**Usage:**
- Primary Buttons: `primary-600`
- Primary Hover: `primary-700`
- Primary Active: `primary-800`
- Primary Light Backgrounds: `primary-50`
- Links: `primary-600`

### 1.2 Semantic Colors

#### Success Colors (Income, Positive Actions)

```css
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
```

#### Warning Colors (Budget Alerts, Caution)

```css
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
```

#### Error Colors (Expenses, Errors, Destructive Actions)

```css
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
```

#### Info Colors (Information, Tips)

```css
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

### 1.3 Neutral Colors

Grayscale palette for UI elements, text, borders, and backgrounds.

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

### 1.4 Dark Mode Colors

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

### 1.5 Category Colors

Pre-defined colors for transaction categories.

```css
/* Category Colors */
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

### 1.6 Account Type Colors

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

---

## 2. Typography

### 2.1 Font Families

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

### 2.2 Font Scales

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

### 2.3 Typography Usage

#### Display Numbers (Balance, Amounts)
- **Font**: `font-mono`
- **Weight**: `font-semibold (600)`
- **Size**: `text-3xl` to `text-5xl`
- **Color**: `gray-900` (light) / `dark-text-primary` (dark)

#### Headings (H1)
- **Font**: `font-primary`
- **Weight**: `font-bold (700)`
- **Size**: `text-4xl` (mobile: `text-3xl`)
- **Line Height**: `leading-tight`
- **Color**: `gray-900` (light) / `dark-text-primary` (dark)

#### Headings (H2)
- **Font**: `font-primary`
- **Weight**: `font-semibold (600)`
- **Size**: `text-3xl` (mobile: `text-2xl`)
- **Color**: `gray-800` (light) / `dark-text-primary` (dark)

#### Headings (H3)
- **Font**: `font-primary`
- **Weight**: `font-semibold (600)`
- **Size**: `text-2xl` (mobile: `text-xl`)
- **Color**: `gray-800` (light) / `dark-text-primary` (dark)

#### Body Text
- **Font**: `font-primary`
- **Weight**: `font-normal (400)`
- **Size**: `text-base`
- **Line Height**: `leading-normal`
- **Color**: `gray-600` (light) / `dark-text-secondary` (dark)

#### Small Text
- **Font**: `font-primary`
- **Weight**: `font-normal (400)`
- **Size**: `text-sm`
- **Color**: `gray-500` (light) / `dark-text-muted` (dark)

#### Labels
- **Font**: `font-primary`
- **Weight**: `font-medium (500)`
- **Size**: `text-sm`
- **Color**: `gray-700` (light) / `dark-text-secondary` (dark)

---

## 3. Spacing System

Based on 4px increments for consistency.

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
```

### Component Spacing Guidelines

**Padding:**
- Small: `space-2` (8px)
- Medium: `space-4` (16px)
- Large: `space-6` (24px)

**Margin:**
- Small: `space-2` (8px)
- Medium: `space-4` (16px)
- Large: `space-8` (32px)

**Gap:**
- Small: `space-2` (8px)
- Medium: `space-4` (16px)
- Large: `space-6` (24px)

---

## 4. Border Radius

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
```

### Component Usage
- **Buttons**: `radius-lg` (8px)
- **Cards**: `radius-xl` (12px)
- **Inputs**: `radius-md` (6px)
- **Badges**: `radius-full` (circle)
- **Modals**: `radius-2xl` (16px)
- **Avatars**: `radius-full` (circle)

---

## 5. Shadows

### 5.1 Light Mode Shadows

```css
/* Shadow Levels */
--shadow-xs: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
--shadow-sm: 0 1px 3px 0 rgba(0, 0, 0, 0.1), 0 1px 2px -1px rgba(0, 0, 0, 0.1);
--shadow-md: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -2px rgba(0, 0, 0, 0.1);
--shadow-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -4px rgba(0, 0, 0, 0.1);
--shadow-xl: 0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 8px 10px -6px rgba(0, 0, 0, 0.1);
--shadow-2xl: 0 25px 50px -12px rgba(0, 0, 0, 0.25);
--shadow-inner: inset 0 2px 4px 0 rgba(0, 0, 0, 0.05);
```

### 5.2 Dark Mode Shadows

```css
--shadow-dark-sm: 0 1px 3px 0 rgba(0, 0, 0, 0.3), 0 1px 2px -1px rgba(0, 0, 0, 0.3);
--shadow-dark-md: 0 4px 6px -1px rgba(0, 0, 0, 0.4), 0 2px 4px -2px rgba(0, 0, 0, 0.4);
--shadow-dark-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.5), 0 4px 6px -4px rgba(0, 0, 0, 0.5);
```

### Component Usage
- **Cards**: `shadow-sm`
- **Dropdown Menus**: `shadow-lg`
- **Modals**: `shadow-2xl`
- **Buttons Hover**: `shadow-md`
- **Floating Action Buttons**: `shadow-xl`

---

## 6. Transitions & Animations

### 6.1 Timing Functions

```css
/* Timing Functions */
--ease-in: cubic-bezier(0.4, 0, 1, 1);
--ease-out: cubic-bezier(0, 0, 0.2, 1);
--ease-in-out: cubic-bezier(0.4, 0, 0.2, 1);
--ease-bounce: cubic-bezier(0.68, -0.55, 0.265, 1.55);
```

### 6.2 Duration

```css
/* Duration */
--duration-75: 75ms;
--duration-100: 100ms;
--duration-150: 150ms;
--duration-200: 200ms;
--duration-300: 300ms;
--duration-500: 500ms;
--duration-700: 700ms;
```

### 6.3 Standard Transitions

```css
/* Standard Transitions */
--transition-fast: all 150ms ease-in-out;
--transition-base: all 200ms ease-in-out;
--transition-slow: all 300ms ease-in-out;
```

### Component Animations
- **Button Hover**: `transition-fast` (150ms)
- **Dropdown Open**: `transition-base` (200ms)
- **Modal Enter/Exit**: `transition-slow` (300ms)
- **Page Transitions**: `transition-slow` (300ms)
- **Skeleton Loading**: 1.5s infinite linear

### 6.4 Keyframe Animations

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

---

## 7. Breakpoints

```css
/* Responsive Breakpoints */
--breakpoint-xs: 0px;       /* Mobile small */
--breakpoint-sm: 640px;     /* Mobile */
--breakpoint-md: 768px;     /* Tablet */
--breakpoint-lg: 1024px;    /* Desktop */
--breakpoint-xl: 1280px;    /* Large desktop */
--breakpoint-2xl: 1536px;   /* Extra large desktop */
```

### Media Queries

```css
@media (min-width: 640px)  { /* sm */ }
@media (min-width: 768px)  { /* md */ }
@media (min-width: 1024px) { /* lg */ }
@media (min-width: 1280px) { /* xl */ }
@media (min-width: 1536px) { /* 2xl */ }
```

---

## 8. Z-Index Layers

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
```

### Component Usage
- **Page Content**: `z-base` (0)
- **Dropdown Menus**: `z-dropdown` (1000)
- **Sticky Headers**: `z-sticky` (1020)
- **Floating Action Buttons**: `z-fixed` (1030)
- **Modal Backdrops**: `z-modal-backdrop` (1040)
- **Modals**: `z-modal` (1050)
- **Popovers**: `z-popover` (1060)
- **Tooltips**: `z-tooltip` (1070)
- **Toast Notifications**: `z-toast` (1080)

---

## 9. Tailwind Configuration

### tailwind.config.js

```javascript
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    './pages/**/*.{js,ts,jsx,tsx,mdx}',
    './components/**/*.{js,ts,jsx,tsx,mdx}',
    './app/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  darkMode: 'class',
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#EEF2FF',
          100: '#E0E7FF',
          200: '#C7D2FE',
          300: '#A5B4FC',
          400: '#818CF8',
          500: '#6366F1',
          600: '#4F46E5',
          700: '#4338CA',
          800: '#3730A3',
          900: '#312E81',
        },
        success: {
          50: '#F0FDF4',
          100: '#DCFCE7',
          200: '#BBF7D0',
          300: '#86EFAC',
          400: '#4ADE80',
          500: '#22C55E',
          600: '#16A34A',
          700: '#15803D',
          800: '#166534',
          900: '#14532D',
        },
        warning: {
          50: '#FFFBEB',
          100: '#FEF3C7',
          200: '#FDE68A',
          300: '#FCD34D',
          400: '#FBBF24',
          500: '#F59E0B',
          600: '#D97706',
          700: '#B45309',
          800: '#92400E',
          900: '#78350F',
        },
        error: {
          50: '#FEF2F2',
          100: '#FEE2E2',
          200: '#FECACA',
          300: '#FCA5A5',
          400: '#F87171',
          500: '#EF4444',
          600: '#DC2626',
          700: '#B91C1C',
          800: '#991B1B',
          900: '#7F1D1D',
        },
        dark: {
          'bg-primary': '#0F172A',
          'bg-secondary': '#1E293B',
          'bg-tertiary': '#334155',
          'border': '#475569',
          'text-primary': '#F1F5F9',
          'text-secondary': '#CBD5E1',
          'text-muted': '#94A3B8',
        },
      },
      fontFamily: {
        sans: ['Inter', 'sans-serif'],
        mono: ['JetBrains Mono', 'Fira Code', 'monospace'],
      },
      fontSize: {
        xs: ['0.75rem', { lineHeight: '1.25' }],
        sm: ['0.875rem', { lineHeight: '1.5' }],
        base: ['1rem', { lineHeight: '1.5' }],
        lg: ['1.125rem', { lineHeight: '1.5' }],
        xl: ['1.25rem', { lineHeight: '1.5' }],
        '2xl': ['1.5rem', { lineHeight: '1.25' }],
        '3xl': ['1.875rem', { lineHeight: '1.25' }],
        '4xl': ['2.25rem', { lineHeight: '1.25' }],
        '5xl': ['3rem', { lineHeight: '1.25' }],
      },
      spacing: {
        '0': '0',
        '1': '0.25rem',
        '2': '0.5rem',
        '3': '0.75rem',
        '4': '1rem',
        '5': '1.25rem',
        '6': '1.5rem',
        '8': '2rem',
        '10': '2.5rem',
        '12': '3rem',
        '16': '4rem',
        '20': '5rem',
        '24': '6rem',
      },
      borderRadius: {
        none: '0',
        sm: '0.125rem',
        DEFAULT: '0.25rem',
        md: '0.375rem',
        lg: '0.5rem',
        xl: '0.75rem',
        '2xl': '1rem',
        '3xl': '1.5rem',
        full: '9999px',
      },
      boxShadow: {
        xs: '0 1px 2px 0 rgba(0, 0, 0, 0.05)',
        sm: '0 1px 3px 0 rgba(0, 0, 0, 0.1), 0 1px 2px -1px rgba(0, 0, 0, 0.1)',
        md: '0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -2px rgba(0, 0, 0, 0.1)',
        lg: '0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -4px rgba(0, 0, 0, 0.1)',
        xl: '0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 8px 10px -6px rgba(0, 0, 0, 0.1)',
        '2xl': '0 25px 50px -12px rgba(0, 0, 0, 0.25)',
        inner: 'inset 0 2px 4px 0 rgba(0, 0, 0, 0.05)',
      },
      transitionDuration: {
        '75': '75ms',
        '100': '100ms',
        '150': '150ms',
        '200': '200ms',
        '300': '300ms',
        '500': '500ms',
        '700': '700ms',
      },
      transitionTimingFunction: {
        'bounce': 'cubic-bezier(0.68, -0.55, 0.265, 1.55)',
      },
      zIndex: {
        '0': '0',
        '1000': '1000',
        '1020': '1020',
        '1030': '1030',
        '1040': '1040',
        '1050': '1050',
        '1060': '1060',
        '1070': '1070',
        '1080': '1080',
      },
      keyframes: {
        fadeIn: {
          '0%': { opacity: '0' },
          '100%': { opacity: '1' },
        },
        slideInRight: {
          '0%': { transform: 'translateX(100%)', opacity: '0' },
          '100%': { transform: 'translateX(0)', opacity: '1' },
        },
        slideInBottom: {
          '0%': { transform: 'translateY(20px)', opacity: '0' },
          '100%': { transform: 'translateY(0)', opacity: '1' },
        },
        scaleIn: {
          '0%': { transform: 'scale(0.95)', opacity: '0' },
          '100%': { transform: 'scale(1)', opacity: '1' },
        },
        pulse: {
          '0%, 100%': { opacity: '1' },
          '50%': { opacity: '0.5' },
        },
        shimmer: {
          '0%': { backgroundPosition: '-1000px 0' },
          '100%': { backgroundPosition: '1000px 0' },
        },
        bounce: {
          '0%, 100%': { transform: 'translateY(0)' },
          '50%': { transform: 'translateY(-10px)' },
        },
      },
      animation: {
        'fade-in': 'fadeIn 300ms ease-in-out',
        'slide-in-right': 'slideInRight 300ms ease-out',
        'slide-in-bottom': 'slideInBottom 200ms ease-out',
        'scale-in': 'scaleIn 300ms ease-out',
        'pulse': 'pulse 2s infinite',
        'shimmer': 'shimmer 1.5s infinite linear',
        'bounce': 'bounce 1s infinite',
      },
    },
  },
  plugins: [],
}
```

---

## 10. Usage Examples

### 10.1 Button with Primary Color

```jsx
<button className="bg-primary-600 hover:bg-primary-700 active:bg-primary-800
                   text-white font-medium text-sm px-5 py-2.5
                   rounded-lg shadow-sm hover:shadow-md
                   transition-all duration-150
                   focus:outline-none focus:ring-2 focus:ring-primary-400 focus:ring-offset-2">
  Add Transaction
</button>
```

### 10.2 Card with Shadow

```jsx
<div className="bg-white dark:bg-dark-bg-secondary
                border border-gray-200 dark:border-dark-border
                rounded-xl p-6 shadow-sm hover:shadow-md
                transition-shadow duration-200">
  <h3 className="text-2xl font-semibold text-gray-900 dark:text-dark-text-primary mb-4">
    Account Balance
  </h3>
  <p className="text-5xl font-mono font-semibold text-gray-900 dark:text-dark-text-primary">
    $5,420.50
  </p>
</div>
```

### 10.3 Success Badge

```jsx
<span className="inline-flex items-center gap-1 px-2 py-1
                 bg-success-100 text-success-700
                 border border-success-200
                 rounded-full text-xs font-medium">
  <CheckIcon className="w-3 h-3" />
  Completed
</span>
```

### 10.4 Input Field

```jsx
<input
  type="text"
  className="w-full px-3 py-2.5
             bg-white dark:bg-dark-bg-secondary
             border border-gray-300 dark:border-dark-border
             rounded-md text-base text-gray-900 dark:text-dark-text-primary
             placeholder:text-gray-400 dark:placeholder:text-dark-text-muted
             focus:border-primary-500 focus:ring-2 focus:ring-primary-200
             transition-all duration-150"
  placeholder="Enter amount"
/>
```

### 10.5 Modal with Backdrop

```jsx
<div className="fixed inset-0 z-1040 bg-black/50 backdrop-blur-sm
                animate-fade-in">
  <div className="fixed inset-0 z-1050 flex items-center justify-center p-4">
    <div className="bg-white dark:bg-dark-bg-secondary
                    rounded-2xl shadow-2xl
                    max-w-md w-full p-6
                    animate-scale-in">
      <h2 className="text-2xl font-semibold text-gray-900 dark:text-dark-text-primary mb-4">
        Confirm Delete
      </h2>
      <p className="text-base text-gray-600 dark:text-dark-text-secondary mb-6">
        Are you sure you want to delete this transaction?
      </p>
      <div className="flex gap-3 justify-end">
        <button className="px-4 py-2 bg-gray-100 hover:bg-gray-200
                         text-gray-700 rounded-lg transition-colors">
          Cancel
        </button>
        <button className="px-4 py-2 bg-error-600 hover:bg-error-700
                         text-white rounded-lg transition-colors">
          Delete
        </button>
      </div>
    </div>
  </div>
</div>
```

### 10.6 Toast Notification

```jsx
<div className="fixed top-6 right-6 z-1080
                animate-slide-in-right">
  <div className="bg-white dark:bg-dark-bg-secondary
                  border-l-4 border-success-500
                  rounded-lg shadow-xl
                  p-4 flex items-start gap-3
                  min-w-[300px] max-w-[450px]">
    <CheckCircleIcon className="w-5 h-5 text-success-500 flex-shrink-0 mt-0.5" />
    <div className="flex-1">
      <h4 className="text-sm font-medium text-gray-900 dark:text-dark-text-primary">
        Transaction Added
      </h4>
      <p className="text-sm text-gray-600 dark:text-dark-text-secondary mt-1">
        Your transaction has been successfully recorded.
      </p>
    </div>
    <button className="text-gray-400 hover:text-gray-600 dark:hover:text-gray-300">
      <XIcon className="w-4 h-4" />
    </button>
  </div>
</div>
```

---

## Accessibility Notes

### Color Contrast

All color combinations meet WCAG AA standards (4.5:1 ratio minimum):
- Body text on white: Use `gray-700` or darker
- Small text: Use `gray-800` or darker
- Large text (18px+): Use `gray-600` or darker

### Color-Blind Friendly

Don't rely on color alone:
- Success states: Green + checkmark icon
- Error states: Red + error icon
- Warning states: Yellow + warning icon
- Use patterns in charts for better accessibility

### Motion Preferences

Respect user's motion preferences:

```css
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```

---

**Design System Version**: 1.0
**Last Updated**: November 18, 2025
**Maintained by**: WalletFlow Team
