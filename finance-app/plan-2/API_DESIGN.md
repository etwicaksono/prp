# WalletFlow - API Design Specification

**Version:** 1.0.0
**Last Updated:** November 2025
**API Version:** v1

---

## Table of Contents

1. [Introduction](#introduction)
2. [REST API Principles](#rest-api-principles)
3. [Base URLs](#base-urls)
4. [Authentication](#authentication)
5. [API Endpoints](#api-endpoints)
6. [Error Handling](#error-handling)
7. [Pagination & Filtering](#pagination--filtering)
8. [Rate Limiting](#rate-limiting)
9. [OpenAPI Specification](#openapi-specification)

---

## 1. Introduction

This document specifies all REST API endpoints for the WalletFlow backend server. The API follows RESTful principles and returns JSON responses.

### 1.1 API Overview

- **Protocol:** HTTPS (production), HTTP (development)
- **Data Format:** JSON
- **Authentication:** OAuth2 + JWT Bearer tokens
- **Versioning:** URL-based (`/api/v1/`)
- **Character Encoding:** UTF-8
- **Date Format:** ISO 8601 (`2025-11-18T10:30:00Z`)

### 1.2 Endpoint Count Summary

| Resource | Endpoints |
|----------|-----------|
| Authentication | 7 |
| Users | 6 |
| Accounts | 7 |
| Transactions | 10 |
| Categories | 7 |
| Budgets | 8 |
| Goals | 7 |
| Analytics | 6 |
| Transfers | 5 |
| Labels | 5 |
| Groups | 5 |
| Debts | 5 |
| Notifications | 4 |
| **TOTAL** | **72 endpoints** |

---

## 2. REST API Principles

### 2.1 HTTP Methods

| Method | Purpose | Idempotent | Safe |
|--------|---------|------------|------|
| GET | Retrieve resources | ✅ Yes | ✅ Yes |
| POST | Create resources | ❌ No | ❌ No |
| PUT | Update (full replace) | ✅ Yes | ❌ No |
| PATCH | Update (partial) | ❌ No | ❌ No |
| DELETE | Remove resources | ✅ Yes | ❌ No |

### 2.2 Resource Naming Conventions

```
✅ Good:
GET  /api/v1/transactions
GET  /api/v1/transactions/:id
POST /api/v1/transactions

❌ Bad:
GET  /api/v1/getAllTransactions
GET  /api/v1/transaction/:id  (singular)
POST /api/v1/createTransaction
```

### 2.3 HTTP Status Codes

| Code | Meaning | Usage |
|------|---------|-------|
| 200 | OK | Successful GET, PUT, PATCH, DELETE |
| 201 | Created | Successful POST |
| 204 | No Content | Successful DELETE (no response body) |
| 400 | Bad Request | Invalid request data |
| 401 | Unauthorized | Missing or invalid authentication |
| 403 | Forbidden | Authenticated but not authorized |
| 404 | Not Found | Resource doesn't exist |
| 409 | Conflict | Resource conflict (e.g., duplicate) |
| 422 | Unprocessable Entity | Validation error |
| 429 | Too Many Requests | Rate limit exceeded |
| 500 | Internal Server Error | Server error |
| 503 | Service Unavailable | Server overloaded/maintenance |

### 2.4 Standard Response Format

**Success Response:**
```json
{
  "success": true,
  "data": { /* resource or array */ },
  "message": "Operation successful",
  "meta": {
    "timestamp": "2025-11-18T10:30:00Z",
    "version": "v1"
  }
}
```

**Error Response:**
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid request data",
    "details": [
      {
        "field": "amount",
        "message": "Amount must be a number"
      }
    ]
  },
  "meta": {
    "timestamp": "2025-11-18T10:30:00Z",
    "version": "v1"
  }
}
```

---

## 3. Base URLs

### 3.1 Environment URLs

| Environment | Base URL | Description |
|-------------|----------|-------------|
| **Development** | `http://localhost:3001/api/v1` | Local development |
| **Staging** | `https://api-staging.walletflow.com/api/v1` | Testing environment |
| **Production** | `https://api.walletflow.com/api/v1` | Live production |

### 3.2 URL Structure

```
https://api.walletflow.com/api/v1/{resource}/{id}?{query}
│                          │   │  │        │    │
│                          │   │  │        │    └─ Query parameters
│                          │   │  │        └────── Resource identifier
│                          │   │  └─────────────── Resource name (plural)
│                          │   └────────────────── API version
│                          └────────────────────── API prefix
└───────────────────────────────────────────────── Domain
```

**Examples:**
```
GET  https://api.walletflow.com/api/v1/transactions?page=1&limit=50
POST https://api.walletflow.com/api/v1/accounts
GET  https://api.walletflow.com/api/v1/transactions/txn_123456
```

---

## 4. Authentication

All endpoints except authentication endpoints require a valid JWT access token.

**Request Header:**
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

See **AUTHENTICATION_GUIDE.md** for detailed authentication flows.

---

## 5. API Endpoints

### 5.1 Authentication Endpoints

#### 5.1.1 Register New User

```http
POST /api/v1/auth/register
```

**Request Body:**
```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "username": "johndoe",
  "password": "SecurePassword123!"
}
```

**Validation Rules:**
- `name`: Required, 2-64 characters
- `email`: Required, valid email format, unique
- `username`: Required, 3-36 characters, alphanumeric + underscore, unique
- `password`: Required, min 8 characters, must contain uppercase, lowercase, number

**Success Response (201):**
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "usr_a1b2c3d4",
      "name": "John Doe",
      "email": "john@example.com",
      "username": "johndoe",
      "created_at": "2025-11-18T10:30:00Z"
    },
    "message": "Registration successful. Please verify your email."
  }
}
```

**Error Response (400):**
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Email already exists"
  }
}
```

---

#### 5.1.2 Login (Email/Password)

```http
POST /api/v1/auth/login
```

**Request Body:**
```json
{
  "username": "johndoe",
  "password": "SecurePassword123!"
}
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "token_type": "Bearer",
    "expires_in": 900,
    "user": {
      "id": "usr_a1b2c3d4",
      "name": "John Doe",
      "email": "john@example.com",
      "username": "johndoe"
    }
  }
}
```

**Error Response (401):**
```json
{
  "success": false,
  "error": {
    "code": "INVALID_CREDENTIALS",
    "message": "Invalid username or password"
  }
}
```

---

#### 5.1.3 Refresh Access Token

```http
POST /api/v1/auth/refresh
```

**Request Body:**
```json
{
  "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "token_type": "Bearer",
    "expires_in": 900
  }
}
```

---

#### 5.1.4 Logout

```http
POST /api/v1/auth/logout
```

**Headers:**
```
Authorization: Bearer {access_token}
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Logged out successfully"
}
```

---

#### 5.1.5 OAuth Login (Google)

```http
GET /api/v1/auth/oauth/google
```

**Flow:**
1. Frontend redirects to this URL
2. User authenticates with Google
3. Backend receives Google auth code
4. Backend exchanges code for user info
5. Backend creates/updates user
6. Backend redirects to frontend with tokens

**Redirect URL:**
```
https://app.walletflow.com/auth/callback?access_token=...&refresh_token=...
```

---

#### 5.1.6 OAuth Login (Facebook)

```http
GET /api/v1/auth/oauth/facebook
```

Same flow as Google OAuth.

---

#### 5.1.7 Verify Email

```http
POST /api/v1/auth/verify-email
```

**Request Body:**
```json
{
  "token": "verification_token_from_email"
}
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Email verified successfully"
}
```

---

### 5.2 User Endpoints

#### 5.2.1 Get Current User Profile

```http
GET /api/v1/users/me
```

**Headers:**
```
Authorization: Bearer {access_token}
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "usr_a1b2c3d4",
    "name": "John Doe",
    "email": "john@example.com",
    "username": "johndoe",
    "email_verified": true,
    "two_factor_enabled": false,
    "created_at": "2025-11-18T10:30:00Z",
    "updated_at": "2025-11-18T10:30:00Z"
  }
}
```

---

#### 5.2.2 Update User Profile

```http
PATCH /api/v1/users/me
```

**Request Body:**
```json
{
  "name": "John Michael Doe",
  "email": "john.doe@example.com"
}
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "usr_a1b2c3d4",
    "name": "John Michael Doe",
    "email": "john.doe@example.com",
    "updated_at": "2025-11-18T11:00:00Z"
  },
  "message": "Profile updated successfully"
}
```

---

#### 5.2.3 Change Password

```http
POST /api/v1/users/me/change-password
```

**Request Body:**
```json
{
  "current_password": "OldPassword123!",
  "new_password": "NewSecurePassword456!",
  "confirm_password": "NewSecurePassword456!"
}
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Password changed successfully"
}
```

---

#### 5.2.4 Request Password Reset

```http
POST /api/v1/users/password-reset/request
```

**Request Body:**
```json
{
  "email": "john@example.com"
}
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Password reset link sent to your email"
}
```

---

#### 5.2.5 Reset Password

```http
POST /api/v1/users/password-reset/confirm
```

**Request Body:**
```json
{
  "token": "reset_token_from_email",
  "new_password": "NewSecurePassword456!",
  "confirm_password": "NewSecurePassword456!"
}
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Password reset successfully"
}
```

---

#### 5.2.6 Delete Account

```http
DELETE /api/v1/users/me
```

**Request Body:**
```json
{
  "password": "CurrentPassword123!",
  "confirmation": "DELETE MY ACCOUNT"
}
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Account deleted successfully. We've sent a data export to your email."
}
```

---

### 5.3 Account Endpoints

#### 5.3.1 Get All Accounts

```http
GET /api/v1/accounts
```

**Query Parameters:**
- `active`: boolean (filter by active status)
- `group_id`: string (filter by group)
- `account_type`: string (filter by type: bank, cash, credit, etc.)

**Success Response (200):**
```json
{
  "success": true,
  "data": [
    {
      "id": "acc_123456",
      "user_id": "usr_a1b2c3d4",
      "personal_id": 1,
      "name": "Chase Checking",
      "icon": "FaUniversity",
      "active": true,
      "usability": "daily",
      "account_type": "bank",
      "color": "#4F46E5",
      "initial_amount": 1000.00,
      "current_balance": 1250.50,
      "group_id": null,
      "created_at": "2025-11-01T10:00:00Z",
      "updated_at": "2025-11-18T10:30:00Z"
    },
    {
      "id": "acc_234567",
      "personal_id": 2,
      "name": "Cash Wallet",
      "icon": "FaWallet",
      "account_type": "cash",
      "color": "#F59E0B",
      "current_balance": 150.00,
      "active": true
    }
  ]
}
```

---

#### 5.3.2 Get Single Account

```http
GET /api/v1/accounts/:id
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "acc_123456",
    "name": "Chase Checking",
    "account_type": "bank",
    "current_balance": 1250.50,
    "initial_amount": 1000.00,
    "color": "#4F46E5",
    "icon": "FaUniversity",
    "active": true,
    "transaction_count": 87,
    "last_transaction_date": "2025-11-18T09:45:00Z"
  }
}
```

---

#### 5.3.3 Create Account

```http
POST /api/v1/accounts
```

**Request Body:**
```json
{
  "name": "Savings Account",
  "icon": "FaPiggyBank",
  "account_type": "savings",
  "usability": "savings",
  "color": "#10B981",
  "initial_amount": 5000.00,
  "group_id": null
}
```

**Validation Rules:**
- `name`: Required, 1-36 characters
- `icon`: Required, valid icon name
- `account_type`: Required, one of: bank, cash, credit, savings, investment, loan, crypto
- `usability`: Required, one of: daily, savings, bills, other
- `color`: Required, valid hex color
- `initial_amount`: Optional, number
- `group_id`: Optional, valid group ID

**Success Response (201):**
```json
{
  "success": true,
  "data": {
    "id": "acc_345678",
    "personal_id": 3,
    "name": "Savings Account",
    "icon": "FaPiggyBank",
    "account_type": "savings",
    "color": "#10B981",
    "initial_amount": 5000.00,
    "current_balance": 5000.00,
    "created_at": "2025-11-18T10:35:00Z"
  },
  "message": "Account created successfully"
}
```

---

#### 5.3.4 Update Account

```http
PATCH /api/v1/accounts/:id
```

**Request Body:**
```json
{
  "name": "High-Yield Savings",
  "color": "#059669"
}
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "acc_345678",
    "name": "High-Yield Savings",
    "color": "#059669",
    "updated_at": "2025-11-18T10:40:00Z"
  },
  "message": "Account updated successfully"
}
```

---

#### 5.3.5 Delete Account

```http
DELETE /api/v1/accounts/:id
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Account deleted successfully"
}
```

**Error Response (409):**
```json
{
  "success": false,
  "error": {
    "code": "ACCOUNT_HAS_TRANSACTIONS",
    "message": "Cannot delete account with existing transactions. Archive it instead."
  }
}
```

---

#### 5.3.6 Archive Account

```http
PATCH /api/v1/accounts/:id/archive
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "acc_345678",
    "active": false,
    "archived_at": "2025-11-18T10:45:00Z"
  },
  "message": "Account archived successfully"
}
```

---

#### 5.3.7 Get Account Balance History

```http
GET /api/v1/accounts/:id/balance-history
```

**Query Parameters:**
- `start_date`: ISO date (default: 30 days ago)
- `end_date`: ISO date (default: today)
- `interval`: string (daily, weekly, monthly)

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "account_id": "acc_123456",
    "history": [
      {
        "date": "2025-11-01",
        "balance": 1000.00
      },
      {
        "date": "2025-11-08",
        "balance": 1150.00
      },
      {
        "date": "2025-11-15",
        "balance": 1250.50
      }
    ]
  }
}
```

---

### 5.4 Transaction Endpoints

#### 5.4.1 Get All Transactions

```http
GET /api/v1/transactions
```

**Query Parameters:**
- `page`: number (default: 1)
- `limit`: number (default: 50, max: 100)
- `account_id`: string
- `category_id`: string
- `type`: INCOME | EXPENSE
- `start_date`: ISO date
- `end_date`: ISO date
- `min_amount`: number
- `max_amount`: number
- `keyword`: string (search in description)
- `label_ids`: comma-separated label IDs
- `payment_type`: string
- `payment_status`: string
- `sort_by`: date | amount (default: date)
- `sort_order`: asc | desc (default: desc)

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "transactions": [
      {
        "id": "txn_123456",
        "personal_id": 1543,
        "date": "2025-11-18T10:30:00Z",
        "account": {
          "id": "acc_123456",
          "name": "Chase Checking",
          "color": "#4F46E5"
        },
        "category": {
          "id": "cat_789012",
          "name": "Groceries",
          "icon": "FaShoppingCart",
          "color": "#EF4444"
        },
        "amount": -45.50,
        "type": "EXPENSE",
        "description": "Whole Foods",
        "payment_type": "Credit Card",
        "payment_status": "Cleared",
        "labels": [
          {
            "id": "lbl_111",
            "name": "Food",
            "color": "#EF4444"
          }
        ],
        "created_at": "2025-11-18T10:31:00Z"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 50,
      "total": 1543,
      "total_pages": 31
    }
  }
}
```

---

#### 5.4.2 Get Single Transaction

```http
GET /api/v1/transactions/:id
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "txn_123456",
    "personal_id": 1543,
    "date": "2025-11-18T10:30:00Z",
    "account_id": "acc_123456",
    "category_id": "cat_789012",
    "amount": -45.50,
    "type": "EXPENSE",
    "description": "Whole Foods grocery shopping",
    "currency": "USD",
    "payer": "John Doe",
    "payment_type": "Credit Card",
    "payment_status": "Cleared",
    "transfer_id": null,
    "debt_id": null,
    "attachments": [
      {
        "id": "att_111",
        "filename": "receipt.jpg",
        "url": "https://storage.walletflow.com/receipts/receipt.jpg",
        "size": 245678
      }
    ],
    "created_at": "2025-11-18T10:31:00Z",
    "updated_at": "2025-11-18T10:31:00Z"
  }
}
```

---

#### 5.4.3 Create Transaction

```http
POST /api/v1/transactions
```

**Request Body:**
```json
{
  "date": "2025-11-18T10:30:00Z",
  "account_id": "acc_123456",
  "category_id": "cat_789012",
  "amount": -45.50,
  "type": "EXPENSE",
  "description": "Whole Foods",
  "payment_type": "Credit Card",
  "payment_status": "Cleared",
  "label_ids": ["lbl_111", "lbl_222"]
}
```

**Validation Rules:**
- `date`: Required, valid ISO date
- `account_id`: Required, valid account ID
- `category_id`: Required, valid category ID
- `amount`: Required, number (negative for expense, positive for income)
- `type`: Required, INCOME or EXPENSE
- `description`: Optional, max 500 characters
- `payment_type`: Optional, predefined values
- `payment_status`: Optional, predefined values

**Success Response (201):**
```json
{
  "success": true,
  "data": {
    "id": "txn_234567",
    "personal_id": 1544,
    "date": "2025-11-18T10:30:00Z",
    "account_id": "acc_123456",
    "category_id": "cat_789012",
    "amount": -45.50,
    "type": "EXPENSE",
    "created_at": "2025-11-18T10:35:00Z"
  },
  "message": "Transaction created successfully"
}
```

---

#### 5.4.4 Update Transaction

```http
PATCH /api/v1/transactions/:id
```

**Request Body:**
```json
{
  "amount": -52.75,
  "description": "Whole Foods + Pharmacy"
}
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "txn_234567",
    "amount": -52.75,
    "description": "Whole Foods + Pharmacy",
    "updated_at": "2025-11-18T10:40:00Z"
  },
  "message": "Transaction updated successfully"
}
```

---

#### 5.4.5 Delete Transaction

```http
DELETE /api/v1/transactions/:id
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Transaction deleted successfully"
}
```

---

#### 5.4.6 Get Transaction Summary

```http
GET /api/v1/transactions/summary
```

**Query Parameters:**
- `start_date`: ISO date (default: first day of month)
- `end_date`: ISO date (default: today)
- `account_id`: string (optional)

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "total_income": 5000.00,
    "total_expense": 2345.75,
    "net_amount": 2654.25,
    "transaction_count": 87,
    "by_category": [
      {
        "category_id": "cat_789012",
        "category_name": "Groceries",
        "total": -345.50,
        "count": 8,
        "percentage": 14.7
      },
      {
        "category_id": "cat_890123",
        "category_name": "Transportation",
        "total": -120.00,
        "count": 5,
        "percentage": 5.1
      }
    ],
    "by_payment_type": [
      {
        "payment_type": "Credit Card",
        "total": -1200.00,
        "percentage": 51.2
      },
      {
        "payment_type": "Cash",
        "total": -145.75,
        "percentage": 6.2
      }
    ]
  }
}
```

---

#### 5.4.7 Import Transactions (CSV)

```http
POST /api/v1/transactions/import
```

**Content-Type:** `multipart/form-data`

**Request Body:**
```
file: transactions.csv
account_id: acc_123456
date_format: MM/DD/YYYY
```

**CSV Format:**
```csv
Date,Description,Amount,Category
11/18/2025,Whole Foods,-45.50,Groceries
11/17/2025,Salary,5000.00,Income
```

**Success Response (201):**
```json
{
  "success": true,
  "data": {
    "imported": 25,
    "skipped": 2,
    "errors": [
      {
        "row": 3,
        "error": "Invalid amount format"
      }
    ]
  },
  "message": "25 transactions imported successfully"
}
```

---

#### 5.4.8 Export Transactions (CSV)

```http
GET /api/v1/transactions/export
```

**Query Parameters:**
- `start_date`: ISO date
- `end_date`: ISO date
- `account_id`: string (optional)
- `format`: csv | xlsx | pdf

**Success Response (200):**
```
Content-Type: text/csv
Content-Disposition: attachment; filename="transactions_2025-11.csv"

Date,Description,Category,Amount,Account,Type
2025-11-18,Whole Foods,Groceries,-45.50,Chase Checking,EXPENSE
2025-11-17,Salary,Income,5000.00,Chase Checking,INCOME
```

---

#### 5.4.9 Bulk Delete Transactions

```http
POST /api/v1/transactions/bulk-delete
```

**Request Body:**
```json
{
  "transaction_ids": ["txn_123", "txn_456", "txn_789"]
}
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "deleted_count": 3
  },
  "message": "3 transactions deleted successfully"
}
```

---

#### 5.4.10 Get Recurring Transactions

```http
GET /api/v1/transactions/recurring
```

**Success Response (200):**
```json
{
  "success": true,
  "data": [
    {
      "id": "rec_123",
      "name": "Netflix Subscription",
      "account_id": "acc_123456",
      "category_id": "cat_entertainment",
      "amount": -15.99,
      "type": "EXPENSE",
      "frequency": "monthly",
      "start_date": "2025-01-01",
      "next_occurrence": "2025-12-01",
      "active": true,
      "auto_create": true
    }
  ]
}
```

---

### 5.5 Category Endpoints

#### 5.5.1 Get All Categories

```http
GET /api/v1/categories
```

**Query Parameters:**
- `nature`: NEED | WANT | MUST
- `is_active`: boolean
- `parent_id`: string (get children of specific parent)

**Success Response (200):**
```json
{
  "success": true,
  "data": [
    {
      "id": "cat_123456",
      "personal_id": 1,
      "name": "Food",
      "icon": "FaUtensils",
      "nature": "NEED",
      "is_active": true,
      "color": "#EF4444",
      "parent_id": null,
      "children": [
        {
          "id": "cat_234567",
          "personal_id": 2,
          "name": "Groceries",
          "icon": "FaShoppingCart",
          "nature": "NEED",
          "parent_id": "cat_123456"
        },
        {
          "id": "cat_345678",
          "personal_id": 3,
          "name": "Restaurants",
          "icon": "FaConciergeBell",
          "nature": "WANT",
          "parent_id": "cat_123456"
        }
      ]
    }
  ]
}
```

---

#### 5.5.2 Get Category Tree

```http
GET /api/v1/categories/tree
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "NEED": [
      {
        "id": "cat_123456",
        "name": "Food",
        "icon": "FaUtensils",
        "children": [...]
      }
    ],
    "WANT": [...],
    "MUST": [...]
  }
}
```

---

#### 5.5.3 Create Category

```http
POST /api/v1/categories
```

**Request Body:**
```json
{
  "name": "Entertainment",
  "icon": "FaFilm",
  "nature": "WANT",
  "color": "#8B5CF6",
  "parent_id": null
}
```

**Success Response (201):**
```json
{
  "success": true,
  "data": {
    "id": "cat_456789",
    "personal_id": 15,
    "name": "Entertainment",
    "icon": "FaFilm",
    "nature": "WANT",
    "color": "#8B5CF6",
    "is_active": true,
    "created_at": "2025-11-18T10:45:00Z"
  },
  "message": "Category created successfully"
}
```

---

#### 5.5.4 Update Category

```http
PATCH /api/v1/categories/:id
```

**Request Body:**
```json
{
  "name": "Entertainment & Leisure",
  "color": "#7C3AED"
}
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "cat_456789",
    "name": "Entertainment & Leisure",
    "color": "#7C3AED",
    "updated_at": "2025-11-18T10:50:00Z"
  },
  "message": "Category updated successfully"
}
```

---

#### 5.5.5 Delete Category

```http
DELETE /api/v1/categories/:id
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Category deleted successfully"
}
```

**Error Response (409):**
```json
{
  "success": false,
  "error": {
    "code": "CATEGORY_HAS_TRANSACTIONS",
    "message": "Cannot delete category with existing transactions"
  }
}
```

---

#### 5.5.6 Get Category Statistics

```http
GET /api/v1/categories/:id/statistics
```

**Query Parameters:**
- `start_date`: ISO date
- `end_date`: ISO date

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "category_id": "cat_123456",
    "category_name": "Food",
    "total_spent": 1250.75,
    "transaction_count": 45,
    "average_transaction": 27.79,
    "percentage_of_total": 23.5,
    "trend": "increasing",
    "month_over_month": 15.3
  }
}
```

---

#### 5.5.7 Reorder Categories

```http
POST /api/v1/categories/reorder
```

**Request Body:**
```json
{
  "order_map": [
    { "id": "cat_123", "personal_id": 1 },
    { "id": "cat_456", "personal_id": 2 },
    { "id": "cat_789", "personal_id": 3 }
  ]
}
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "updated_count": 3
  },
  "message": "Categories reordered successfully"
}
```

---

### 5.6 Budget Endpoints

#### 5.6.1 Get All Budgets

```http
GET /api/v1/budgets
```

**Query Parameters:**
- `period`: monthly | weekly | yearly
- `active`: boolean

**Success Response (200):**
```json
{
  "success": true,
  "data": [
    {
      "id": "bgt_123456",
      "name": "November 2025 Budget",
      "category_id": "cat_789012",
      "category_name": "Groceries",
      "amount": 500.00,
      "spent": 345.50,
      "remaining": 154.50,
      "percentage": 69.1,
      "period": "monthly",
      "start_date": "2025-11-01",
      "end_date": "2025-11-30",
      "status": "on_track",
      "alert_threshold": 80,
      "alert_triggered": false
    }
  ]
}
```

---

#### 5.6.2 Get Single Budget

```http
GET /api/v1/budgets/:id
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "bgt_123456",
    "name": "November Groceries",
    "category_id": "cat_789012",
    "amount": 500.00,
    "spent": 345.50,
    "remaining": 154.50,
    "percentage": 69.1,
    "period": "monthly",
    "start_date": "2025-11-01",
    "end_date": "2025-11-30",
    "rollover_enabled": false,
    "alert_threshold": 80,
    "transactions": [
      {
        "id": "txn_123",
        "date": "2025-11-18",
        "amount": -45.50,
        "description": "Whole Foods"
      }
    ]
  }
}
```

---

#### 5.6.3 Create Budget

```http
POST /api/v1/budgets
```

**Request Body:**
```json
{
  "name": "December Groceries",
  "category_id": "cat_789012",
  "amount": 600.00,
  "period": "monthly",
  "start_date": "2025-12-01",
  "end_date": "2025-12-31",
  "rollover_enabled": true,
  "alert_threshold": 80
}
```

**Success Response (201):**
```json
{
  "success": true,
  "data": {
    "id": "bgt_234567",
    "name": "December Groceries",
    "category_id": "cat_789012",
    "amount": 600.00,
    "period": "monthly",
    "created_at": "2025-11-18T10:55:00Z"
  },
  "message": "Budget created successfully"
}
```

---

#### 5.6.4 Update Budget

```http
PATCH /api/v1/budgets/:id
```

**Request Body:**
```json
{
  "amount": 650.00,
  "alert_threshold": 75
}
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "bgt_234567",
    "amount": 650.00,
    "alert_threshold": 75,
    "updated_at": "2025-11-18T11:00:00Z"
  },
  "message": "Budget updated successfully"
}
```

---

#### 5.6.5 Delete Budget

```http
DELETE /api/v1/budgets/:id
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Budget deleted successfully"
}
```

---

#### 5.6.6 Get Budget Overview

```http
GET /api/v1/budgets/overview
```

**Query Parameters:**
- `month`: YYYY-MM (default: current month)

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "total_budgeted": 3500.00,
    "total_spent": 2345.75,
    "total_remaining": 1154.25,
    "percentage": 67.0,
    "status": "on_track",
    "budgets_exceeded": 2,
    "budgets_on_track": 8,
    "budgets_under": 3
  }
}
```

---

#### 5.6.7 Create Budget from Template

```http
POST /api/v1/budgets/from-template
```

**Request Body:**
```json
{
  "template_id": "tpl_123",
  "start_date": "2025-12-01",
  "end_date": "2025-12-31"
}
```

**Success Response (201):**
```json
{
  "success": true,
  "data": {
    "budgets_created": 10,
    "budget_ids": ["bgt_111", "bgt_222", "..."]
  },
  "message": "10 budgets created from template"
}
```

---

#### 5.6.8 Get Budget Templates

```http
GET /api/v1/budgets/templates
```

**Success Response (200):**
```json
{
  "success": true,
  "data": [
    {
      "id": "tpl_123",
      "name": "Conservative Budget",
      "description": "50/30/20 rule budget",
      "categories": [
        {
          "category_id": "cat_groceries",
          "percentage": 15,
          "amount": 600
        }
      ]
    }
  ]
}
```

---

### 5.7 Goal Endpoints

#### 5.7.1 Get All Goals

```http
GET /api/v1/goals
```

**Query Parameters:**
- `status`: active | completed | archived
- `type`: savings | debt_payoff | investment

**Success Response (200):**
```json
{
  "success": true,
  "data": [
    {
      "id": "goal_123456",
      "name": "Emergency Fund",
      "type": "savings",
      "target_amount": 10000.00,
      "current_amount": 3500.00,
      "percentage": 35.0,
      "start_date": "2025-01-01",
      "target_date": "2025-12-31",
      "monthly_contribution": 600.00,
      "projected_completion": "2026-02-15",
      "on_track": false,
      "status": "active"
    }
  ]
}
```

---

#### 5.7.2 Get Single Goal

```http
GET /api/v1/goals/:id
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "goal_123456",
    "name": "Emergency Fund",
    "description": "Save 6 months of expenses",
    "type": "savings",
    "target_amount": 10000.00,
    "current_amount": 3500.00,
    "percentage": 35.0,
    "start_date": "2025-01-01",
    "target_date": "2025-12-31",
    "monthly_contribution": 600.00,
    "projected_completion": "2026-02-15",
    "on_track": false,
    "linked_account_id": "acc_savings",
    "contributions": [
      {
        "date": "2025-11-01",
        "amount": 600.00,
        "transaction_id": "txn_111"
      }
    ],
    "milestones": [
      {
        "amount": 2500.00,
        "name": "25% Complete",
        "reached": true,
        "date_reached": "2025-08-15"
      },
      {
        "amount": 5000.00,
        "name": "50% Complete",
        "reached": false
      }
    ]
  }
}
```

---

#### 5.7.3 Create Goal

```http
POST /api/v1/goals
```

**Request Body:**
```json
{
  "name": "Vacation Fund",
  "description": "Save for trip to Japan",
  "type": "savings",
  "target_amount": 5000.00,
  "start_date": "2025-11-01",
  "target_date": "2026-06-30",
  "monthly_contribution": 625.00,
  "linked_account_id": "acc_savings"
}
```

**Success Response (201):**
```json
{
  "success": true,
  "data": {
    "id": "goal_234567",
    "name": "Vacation Fund",
    "target_amount": 5000.00,
    "current_amount": 0,
    "percentage": 0,
    "created_at": "2025-11-18T11:05:00Z"
  },
  "message": "Goal created successfully"
}
```

---

#### 5.7.4 Update Goal

```http
PATCH /api/v1/goals/:id
```

**Request Body:**
```json
{
  "monthly_contribution": 700.00,
  "target_date": "2026-05-31"
}
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "goal_234567",
    "monthly_contribution": 700.00,
    "target_date": "2026-05-31",
    "projected_completion": "2026-04-15",
    "updated_at": "2025-11-18T11:10:00Z"
  },
  "message": "Goal updated successfully"
}
```

---

#### 5.7.5 Delete Goal

```http
DELETE /api/v1/goals/:id
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Goal deleted successfully"
}
```

---

#### 5.7.6 Add Goal Contribution

```http
POST /api/v1/goals/:id/contributions
```

**Request Body:**
```json
{
  "amount": 625.00,
  "date": "2025-11-18",
  "note": "Monthly contribution"
}
```

**Success Response (201):**
```json
{
  "success": true,
  "data": {
    "contribution_id": "contrib_123",
    "goal_id": "goal_234567",
    "amount": 625.00,
    "new_total": 625.00,
    "percentage": 12.5,
    "created_at": "2025-11-18T11:15:00Z"
  },
  "message": "Contribution added successfully"
}
```

---

#### 5.7.7 Get Goal Progress

```http
GET /api/v1/goals/:id/progress
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "goal_id": "goal_234567",
    "progress_history": [
      {
        "date": "2025-11-01",
        "amount": 0,
        "percentage": 0
      },
      {
        "date": "2025-11-18",
        "amount": 625.00,
        "percentage": 12.5
      }
    ],
    "projection": [
      {
        "date": "2025-12-01",
        "projected_amount": 1250.00
      },
      {
        "date": "2026-01-01",
        "projected_amount": 1875.00
      }
    ]
  }
}
```

---

### 5.8 Analytics Endpoints

#### 5.8.1 Get Spending Analytics

```http
GET /api/v1/analytics/spending
```

**Query Parameters:**
- `start_date`: ISO date
- `end_date`: ISO date
- `group_by`: category | account | payment_type | day | week | month

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "total_spending": 2345.75,
    "by_category": [
      {
        "category": "Groceries",
        "amount": 345.50,
        "percentage": 14.7,
        "transaction_count": 8
      }
    ],
    "by_day": [
      {
        "date": "2025-11-18",
        "amount": 145.50
      }
    ],
    "trend": {
      "direction": "increasing",
      "percentage": 12.5
    }
  }
}
```

---

#### 5.8.2 Get Income Analytics

```http
GET /api/v1/analytics/income
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "total_income": 5000.00,
    "by_category": [
      {
        "category": "Salary",
        "amount": 5000.00,
        "percentage": 100
      }
    ],
    "average_monthly": 5000.00,
    "trend": "stable"
  }
}
```

---

#### 5.8.3 Get Cash Flow

```http
GET /api/v1/analytics/cash-flow
```

**Query Parameters:**
- `start_date`: ISO date
- `end_date`: ISO date
- `interval`: daily | weekly | monthly

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "cash_flow": [
      {
        "date": "2025-11-01",
        "income": 5000.00,
        "expense": 1234.50,
        "net": 3765.50
      },
      {
        "date": "2025-11-08",
        "income": 0,
        "expense": 567.25,
        "net": -567.25
      }
    ],
    "summary": {
      "total_income": 5000.00,
      "total_expense": 2345.75,
      "net_cash_flow": 2654.25,
      "savings_rate": 53.1
    }
  }
}
```

---

#### 5.8.4 Get Net Worth

```http
GET /api/v1/analytics/net-worth
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "net_worth": 15750.50,
    "assets": 20000.00,
    "liabilities": 4249.50,
    "accounts": [
      {
        "account_id": "acc_checking",
        "account_name": "Chase Checking",
        "balance": 1250.50,
        "type": "asset"
      },
      {
        "account_id": "acc_credit",
        "account_name": "Credit Card",
        "balance": -1249.50,
        "type": "liability"
      }
    ],
    "history": [
      {
        "date": "2025-11-01",
        "net_worth": 14500.00
      },
      {
        "date": "2025-11-18",
        "net_worth": 15750.50
      }
    ]
  }
}
```

---

#### 5.8.5 Get Top Merchants

```http
GET /api/v1/analytics/top-merchants
```

**Query Parameters:**
- `limit`: number (default: 10)
- `start_date`: ISO date
- `end_date`: ISO date

**Success Response (200):**
```json
{
  "success": true,
  "data": [
    {
      "merchant": "Whole Foods",
      "total_spent": 245.50,
      "transaction_count": 5,
      "average_transaction": 49.10
    },
    {
      "merchant": "Starbucks",
      "total_spent": 89.50,
      "transaction_count": 12,
      "average_transaction": 7.46
    }
  ]
}
```

---

#### 5.8.6 Get Financial Health Score

```http
GET /api/v1/analytics/health-score
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "overall_score": 75,
    "grade": "B",
    "factors": {
      "savings_rate": {
        "score": 80,
        "value": 25,
        "target": 20,
        "status": "good"
      },
      "debt_to_income": {
        "score": 70,
        "value": 35,
        "target": 30,
        "status": "fair"
      },
      "emergency_fund": {
        "score": 60,
        "value": 3,
        "target": 6,
        "status": "needs_improvement"
      }
    },
    "recommendations": [
      "Increase emergency fund to 6 months of expenses",
      "Pay down credit card debt to improve score"
    ]
  }
}
```

---

### 5.9 Transfer Endpoints

#### 5.9.1 Get All Transfers

```http
GET /api/v1/transfers
```

**Success Response (200):**
```json
{
  "success": true,
  "data": [
    {
      "id": "trf_123456",
      "personal_id": 15,
      "date": "2025-11-18T10:00:00Z",
      "from_account": {
        "id": "acc_checking",
        "name": "Chase Checking"
      },
      "to_account": {
        "id": "acc_savings",
        "name": "Savings Account"
      },
      "amount": 500.00,
      "note": "Monthly savings",
      "created_at": "2025-11-18T10:01:00Z"
    }
  ]
}
```

---

#### 5.9.2 Create Transfer

```http
POST /api/v1/transfers
```

**Request Body:**
```json
{
  "date": "2025-11-18T10:00:00Z",
  "from_account_id": "acc_checking",
  "to_account_id": "acc_savings",
  "amount": 500.00,
  "note": "Monthly savings"
}
```

**Success Response (201):**
```json
{
  "success": true,
  "data": {
    "id": "trf_234567",
    "personal_id": 16,
    "from_account_id": "acc_checking",
    "to_account_id": "acc_savings",
    "amount": 500.00,
    "transactions": [
      {
        "id": "txn_withdrawal",
        "account_id": "acc_checking",
        "amount": -500.00,
        "type": "EXPENSE"
      },
      {
        "id": "txn_deposit",
        "account_id": "acc_savings",
        "amount": 500.00,
        "type": "INCOME"
      }
    ],
    "created_at": "2025-11-18T10:20:00Z"
  },
  "message": "Transfer created successfully"
}
```

---

#### 5.9.3 Update Transfer

```http
PATCH /api/v1/transfers/:id
```

**Request Body:**
```json
{
  "amount": 600.00,
  "note": "Increased monthly savings"
}
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "trf_234567",
    "amount": 600.00,
    "note": "Increased monthly savings",
    "updated_at": "2025-11-18T10:25:00Z"
  },
  "message": "Transfer updated successfully"
}
```

---

#### 5.9.4 Delete Transfer

```http
DELETE /api/v1/transfers/:id
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Transfer and associated transactions deleted successfully"
}
```

---

#### 5.9.5 Get Transfer Details

```http
GET /api/v1/transfers/:id
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "trf_234567",
    "date": "2025-11-18T10:00:00Z",
    "from_account": {
      "id": "acc_checking",
      "name": "Chase Checking",
      "balance_before": 1750.50,
      "balance_after": 1150.50
    },
    "to_account": {
      "id": "acc_savings",
      "name": "Savings Account",
      "balance_before": 4500.00,
      "balance_after": 5100.00
    },
    "amount": 600.00,
    "note": "Increased monthly savings",
    "created_at": "2025-11-18T10:20:00Z"
  }
}
```

---

### 5.10 Label Endpoints

#### 5.10.1 Get All Labels

```http
GET /api/v1/labels
```

**Success Response (200):**
```json
{
  "success": true,
  "data": [
    {
      "id": "lbl_123456",
      "personal_id": 1,
      "name": "Tax Deductible",
      "color": "#10B981",
      "usage_count": 45,
      "created_at": "2025-01-01T10:00:00Z"
    }
  ]
}
```

---

#### 5.10.2 Create Label

```http
POST /api/v1/labels
```

**Request Body:**
```json
{
  "name": "Business Expense",
  "color": "#3B82F6"
}
```

**Success Response (201):**
```json
{
  "success": true,
  "data": {
    "id": "lbl_234567",
    "personal_id": 5,
    "name": "Business Expense",
    "color": "#3B82F6",
    "created_at": "2025-11-18T10:30:00Z"
  },
  "message": "Label created successfully"
}
```

---

#### 5.10.3 Update Label

```http
PATCH /api/v1/labels/:id
```

**Request Body:**
```json
{
  "name": "Work Related",
  "color": "#2563EB"
}
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "lbl_234567",
    "name": "Work Related",
    "color": "#2563EB",
    "updated_at": "2025-11-18T10:35:00Z"
  },
  "message": "Label updated successfully"
}
```

---

#### 5.10.4 Delete Label

```http
DELETE /api/v1/labels/:id
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Label deleted successfully"
}
```

---

#### 5.10.5 Get Label Statistics

```http
GET /api/v1/labels/:id/statistics
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "label_id": "lbl_123456",
    "label_name": "Tax Deductible",
    "transaction_count": 45,
    "total_amount": 3450.00,
    "recent_transactions": [...]
  }
}
```

---

### 5.11 Group Endpoints

#### 5.11.1 Get All Groups

```http
GET /api/v1/groups
```

**Success Response (200):**
```json
{
  "success": true,
  "data": [
    {
      "id": "grp_123456",
      "personal_id": 1,
      "name": "Personal Accounts",
      "account_count": 5,
      "total_balance": 6750.50,
      "created_at": "2025-01-01T10:00:00Z"
    }
  ]
}
```

---

#### 5.11.2 Create Group

```http
POST /api/v1/groups
```

**Request Body:**
```json
{
  "name": "Business Accounts"
}
```

**Success Response (201):**
```json
{
  "success": true,
  "data": {
    "id": "grp_234567",
    "personal_id": 3,
    "name": "Business Accounts",
    "created_at": "2025-11-18T10:40:00Z"
  },
  "message": "Group created successfully"
}
```

---

#### 5.11.3 Update Group

```http
PATCH /api/v1/groups/:id
```

**Request Body:**
```json
{
  "name": "Freelance Business"
}
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "grp_234567",
    "name": "Freelance Business",
    "updated_at": "2025-11-18T10:45:00Z"
  },
  "message": "Group updated successfully"
}
```

---

#### 5.11.4 Delete Group

```http
DELETE /api/v1/groups/:id
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Group deleted successfully. Accounts moved to default group."
}
```

---

#### 5.11.5 Get Group Details

```http
GET /api/v1/groups/:id
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "grp_123456",
    "name": "Personal Accounts",
    "accounts": [
      {
        "id": "acc_checking",
        "name": "Chase Checking",
        "balance": 1250.50
      }
    ],
    "total_balance": 6750.50,
    "account_count": 5
  }
}
```

---

### 5.12 Debt Endpoints

#### 5.12.1 Get All Debts

```http
GET /api/v1/debts
```

**Success Response (200):**
```json
{
  "success": true,
  "data": [
    {
      "id": "debt_123456",
      "personal_id": 1,
      "name": "Car Loan",
      "type": "LOAN",
      "account_id": "acc_loan",
      "principal_amount": 20000.00,
      "current_balance": 15750.00,
      "interest_rate": 5.5,
      "monthly_payment": 450.00,
      "paid_amount": 4250.00,
      "percentage_paid": 21.3,
      "remaining_months": 35,
      "payoff_date": "2028-10-18",
      "created_at": "2023-11-18T10:00:00Z"
    }
  ]
}
```

---

#### 5.12.2 Create Debt

```http
POST /api/v1/debts
```

**Request Body:**
```json
{
  "name": "Student Loan",
  "type": "DEBT",
  "account_id": "acc_student_loan",
  "principal_amount": 30000.00,
  "interest_rate": 4.5,
  "monthly_payment": 350.00,
  "start_date": "2025-11-01"
}
```

**Success Response (201):**
```json
{
  "success": true,
  "data": {
    "id": "debt_234567",
    "personal_id": 2,
    "name": "Student Loan",
    "type": "DEBT",
    "principal_amount": 30000.00,
    "current_balance": 30000.00,
    "created_at": "2025-11-18T10:50:00Z"
  },
  "message": "Debt created successfully"
}
```

---

#### 5.12.3 Update Debt

```http
PATCH /api/v1/debts/:id
```

**Request Body:**
```json
{
  "monthly_payment": 400.00
}
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "debt_234567",
    "monthly_payment": 400.00,
    "payoff_date": "2031-08-15",
    "updated_at": "2025-11-18T10:55:00Z"
  },
  "message": "Debt updated successfully"
}
```

---

#### 5.12.4 Delete Debt

```http
DELETE /api/v1/debts/:id
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Debt deleted successfully"
}
```

---

#### 5.12.5 Get Debt Payment Schedule

```http
GET /api/v1/debts/:id/payment-schedule
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "debt_id": "debt_123456",
    "schedule": [
      {
        "month": 1,
        "date": "2025-12-18",
        "payment": 450.00,
        "principal": 377.50,
        "interest": 72.50,
        "balance": 19622.50
      },
      {
        "month": 2,
        "date": "2026-01-18",
        "payment": 450.00,
        "principal": 379.23,
        "interest": 70.77,
        "balance": 19243.27
      }
    ],
    "total_interest": 2850.00,
    "total_payments": 22850.00
  }
}
```

---

### 5.13 Notification Endpoints

#### 5.13.1 Get All Notifications

```http
GET /api/v1/notifications
```

**Query Parameters:**
- `read`: boolean
- `type`: string (budget_alert, bill_reminder, goal_milestone, etc.)

**Success Response (200):**
```json
{
  "success": true,
  "data": [
    {
      "id": "notif_123456",
      "type": "budget_alert",
      "title": "Budget Alert: Groceries",
      "message": "You've spent 85% of your grocery budget",
      "read": false,
      "action_url": "/budgets/bgt_123",
      "created_at": "2025-11-18T10:00:00Z"
    },
    {
      "id": "notif_234567",
      "type": "goal_milestone",
      "title": "Goal Milestone Reached!",
      "message": "You've reached 50% of your Emergency Fund goal",
      "read": false,
      "action_url": "/goals/goal_123",
      "created_at": "2025-11-17T15:30:00Z"
    }
  ]
}
```

---

#### 5.13.2 Mark Notification as Read

```http
PATCH /api/v1/notifications/:id/read
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Notification marked as read"
}
```

---

#### 5.13.3 Mark All Notifications as Read

```http
POST /api/v1/notifications/mark-all-read
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "marked_count": 12
  },
  "message": "12 notifications marked as read"
}
```

---

#### 5.13.4 Delete Notification

```http
DELETE /api/v1/notifications/:id
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Notification deleted successfully"
}
```

---

## 6. Error Handling

### 6.1 Error Response Format

```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable error message",
    "details": [...],
    "stack": "..." // Only in development
  },
  "meta": {
    "timestamp": "2025-11-18T10:30:00Z",
    "version": "v1",
    "request_id": "req_abc123def456"
  }
}
```

### 6.2 Error Codes

| Code | HTTP Status | Description |
|------|-------------|-------------|
| `VALIDATION_ERROR` | 400 | Request data validation failed |
| `UNAUTHORIZED` | 401 | Missing or invalid authentication |
| `FORBIDDEN` | 403 | Authenticated but not authorized |
| `NOT_FOUND` | 404 | Resource not found |
| `CONFLICT` | 409 | Resource conflict (duplicate) |
| `RATE_LIMIT_EXCEEDED` | 429 | Too many requests |
| `INTERNAL_ERROR` | 500 | Server error |
| `SERVICE_UNAVAILABLE` | 503 | Service temporarily unavailable |

### 6.3 Validation Error Details

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid request data",
    "details": [
      {
        "field": "amount",
        "value": "abc",
        "message": "Amount must be a number",
        "rule": "type"
      },
      {
        "field": "email",
        "value": "invalid-email",
        "message": "Email must be valid",
        "rule": "email"
      }
    ]
  }
}
```

---

## 7. Pagination & Filtering

### 7.1 Pagination

**Query Parameters:**
- `page`: Page number (default: 1)
- `limit`: Items per page (default: 50, max: 100)

**Response:**
```json
{
  "success": true,
  "data": [...],
  "pagination": {
    "page": 1,
    "limit": 50,
    "total": 1543,
    "total_pages": 31,
    "has_next": true,
    "has_prev": false
  }
}
```

### 7.2 Filtering

**Common Filters:**
- `start_date` & `end_date`: Date range
- `account_id`: Filter by account
- `category_id`: Filter by category
- `type`: Filter by type
- `keyword`: Search text

**Example:**
```
GET /api/v1/transactions?start_date=2025-11-01&end_date=2025-11-30&category_id=cat_123&page=1&limit=50
```

### 7.3 Sorting

**Query Parameters:**
- `sort_by`: Field name (date, amount, name, etc.)
- `sort_order`: asc | desc

**Example:**
```
GET /api/v1/transactions?sort_by=amount&sort_order=desc
```

---

## 8. Rate Limiting

### 8.1 Rate Limit Rules

| Endpoint Type | Limit | Window |
|---------------|-------|--------|
| Authentication | 5 requests | 15 minutes |
| Read Operations (GET) | 100 requests | 1 minute |
| Write Operations (POST/PUT/PATCH/DELETE) | 30 requests | 1 minute |
| Analytics | 10 requests | 1 minute |

### 8.2 Rate Limit Headers

```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1700310600
```

### 8.3 Rate Limit Exceeded Response

```json
{
  "success": false,
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Too many requests. Please try again in 45 seconds.",
    "retry_after": 45
  }
}
```

---

## 9. OpenAPI Specification

### 9.1 Swagger Documentation

**URL:** `https://api.walletflow.com/docs`

Interactive API documentation with:
- All endpoints documented
- Request/response examples
- Try it out functionality
- Authentication testing
- Schema definitions

### 9.2 OpenAPI JSON

**URL:** `https://api.walletflow.com/api/v1/openapi.json`

Download the full OpenAPI 3.0 specification for:
- Code generation
- API client creation
- Testing tools
- Documentation generation

### 9.3 Postman Collection

**URL:** `https://api.walletflow.com/postman/collection.json`

Import into Postman for:
- All endpoints pre-configured
- Environment variables
- Authentication setup
- Example requests

---

## Appendix

### A. HTTP Header Reference

**Request Headers:**
```
Content-Type: application/json
Authorization: Bearer {access_token}
Accept: application/json
X-Request-ID: {unique_id}
```

**Response Headers:**
```
Content-Type: application/json
X-Request-ID: {unique_id}
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1700310600
```

### B. Currency Codes (ISO 4217)

Supported currencies:
- USD - US Dollar
- EUR - Euro
- GBP - British Pound
- JPY - Japanese Yen
- CAD - Canadian Dollar
- AUD - Australian Dollar
- CHF - Swiss Franc
- CNY - Chinese Yuan
- INR - Indian Rupee

### C. Related Documents

- **AUTHENTICATION_GUIDE.md** - OAuth2 and JWT details
- **PROJECT_OVERVIEW.md** - Architecture overview
- **FOLDER_STRUCTURE.md** - Backend folder organization
- **DEPLOYMENT_GUIDE.md** - API deployment instructions

---

**Document Status:** ✅ Complete
**API Version:** v1
**Total Endpoints:** 72
