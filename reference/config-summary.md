# Configuration Summary (`src/config/index.ts`)

## 📁 **Purpose**
Centralized application configuration management with environment variable parsing, validation, and fallback values.

## 🏗️ **Structure & Architecture**

### **Utility Functions**
```typescript
// String normalization and validation
normalizeString(value) → Trims whitespace, handles undefined
getStringOrFallback(value, fallback) → Returns fallback if empty
parsePositiveInteger(value, fallback) → Validates positive integers
```

### **Configuration Pattern**
- **Type-safe interfaces** for each config section
- **Environment variable parsing** with fallbacks
- **Runtime validation** of values
- **Immutable exports** for application-wide use

---

## ⚙️ **Configuration Sections**

### **1. ENV_CONFIG - Environment Detection**
```typescript
interface EnvConfig {
  mode: string;                 // 'development' | 'production' | 'test'
  isDevelopment: boolean;
  isProduction: boolean;
  isTest: boolean;
}
```

**Usage**: Feature flags, debugging, conditional behavior
```typescript
if (ENV_CONFIG.isDevelopment) {
  console.log('Debug info');
}
```

### **2. AUTH_CONFIG - JWT Authentication**
```typescript
interface AuthConfig {
  jwtSecret: string;                    // JWT signing secret
  jwtRefreshSecret: string;             // Refresh token secret
  accessTokenTTLSeconds: number;        // Access token expiry (24h default)
  refreshTokenTTLSeconds: number;       // Refresh token expiry (7d default)
}
```

**Environment Variables**:
- `JWT_SECRET` → Fallback: 'your-secret-key-change-this-in-production'
- `JWT_REFRESH_SECRET` → Fallback: 'your-refresh-secret-key-change-this-in-production'
- `ACCESS_TOKEN_TTL_SECONDS` → Fallback: 86400 (24 hours)
- `REFRESH_TOKEN_TTL_SECONDS` → Fallback: 604800 (7 days)

### **3. APP_CONFIG - Application Settings**
```typescript
interface AppConfig {
  name: string;                 // App display name
  version: string;              // App version
  storageKeys: AppStorageKeys;  // localStorage key names
  modalTimeout: number;         // Modal auto-hide timeout
}

interface AppStorageKeys {
  authToken: string;            // 'authToken'
  refreshToken: string;         // 'refreshToken' 
  userData: string;             // 'userData'
  cryptoKey: string;            // 'finance_app_crypto_key'
}
```

**Environment Variables**:
- `NEXT_PUBLIC_APP_NAME` → Fallback: 'Finance App'
- `NEXT_PUBLIC_APP_VERSION` → Fallback: '1.0.0'
- `NEXT_PUBLIC_STORAGE_*_KEY` → Storage key customization
- `NEXT_PUBLIC_MODAL_TIMEOUT` → Fallback: 3000ms

### **4. API_CONFIG - API Client Settings**
```typescript
interface ApiConfig {
  baseURL: string;              // API base URL
  timeout: number;              // Request timeout (30s)
  retryAttempts: number;        // Retry attempts (3)
}
```

**Environment Variables**:
- `NEXT_PUBLIC_API_BASE_URL` → Fallback: 'http://localhost:8080/api/v1'

### **5. CRYPTO_CONFIG - Encryption Settings**
```typescript
interface CryptoConfig {
  algorithm: string;            // 'AES-GCM'
  keyLength: number;            // 256 bits
  ivLength: number;             // 12 bytes
}
```

**Usage**: Token encryption in localStorage

### **6. OPENAPI_CONFIG - API Documentation**
```typescript
interface OpenApiConfig {
  apiDirectory: string;         // 'app/api'
  scalarCdnUrl: string;         // Scalar API docs CDN
  packageName: string;          // 'Finance API'
  packageDescription: string;   // API description
  packageVersion: string;       // '0.0.0'
}
```

**Environment Variables**:
- `OPENAPI_API_DIR` → API routes directory
- `SCALAR_CDN_URL` → API documentation CDN
- `npm_package_*` → Package.json values

### **7. API_RESPONSE_CONFIG - API Versioning**
```typescript
interface ApiResponseConfig {
  version: string;              // API version ('v1.0.0')
}
```

### **8. WEB_VITALS_CONFIG - Performance Monitoring**
```typescript
interface WebVitalsConfig {
  isEnabled: boolean;           // Performance tracking toggle
}
```

**Environment Variables**:
- `NEXT_PUBLIC_MEASURE_WEB_VITALS` → 'true'/'false'

---

## 🔧 **Key Features**

### **1. Environment Variable Validation**
```typescript
// Positive integer validation
accessTokenTTLSeconds: parsePositiveInteger(process.env.ACCESS_TOKEN_TTL_SECONDS, 24 * 60 * 60)
// String normalization
jwtSecret: getStringOrFallback(process.env.JWT_SECRET, 'default-secret')
```

### **2. Fallback Values**
- **Development-friendly defaults** for all settings
- **Production-ready placeholders** that force explicit configuration
- **Sensible timeouts and limits** (30s API timeout, 3 retry attempts)

### **3. Type Safety**
```typescript
// All configs are typed interfaces
const config: AuthConfig = AUTH_CONFIG; // TypeScript enforces structure
```

### **4. Runtime Environment Detection**
```typescript
// Environment-specific behavior
ENV_CONFIG.isDevelopment // true in dev
ENV_CONFIG.isProduction  // true in production
ENV_CONFIG.isTest        // true in test environment
```

---

## 📍 **Usage Throughout Application**

### **In Services**
```typescript
// API client configuration
import { API_CONFIG } from '@/config';
axios.defaults.baseURL = API_CONFIG.baseURL;
axios.defaults.timeout = API_CONFIG.timeout;
```

### **In Authentication**
```typescript
// JWT token handling
import { AUTH_CONFIG, APP_CONFIG } from '@/config';
jwt.sign(payload, AUTH_CONFIG.jwtSecret, { 
  expiresIn: AUTH_CONFIG.accessTokenTTLSeconds 
});
localStorage.setItem(APP_CONFIG.storageKeys.authToken, token);
```

### **In Components**
```typescript
// Environment-specific features
import { ENV_CONFIG } from '@/config';
if (ENV_CONFIG.isDevelopment) {
  return <DebugPanel />;
}
```

### **In Crypto Utils**
```typescript
// Token encryption
import { CRYPTO_CONFIG, APP_CONFIG } from '@/config';
encrypt(token, {
  algorithm: CRYPTO_CONFIG.algorithm,
  keyLength: CRYPTO_CONFIG.keyLength
});
```

---

## 🔐 **Security Considerations**

### **Secrets Management**
- **JWT secrets** have development fallbacks that **must be changed in production**
- **Storage keys** are configurable for security through obscurity
- **Crypto settings** use industry-standard AES-GCM encryption

### **Environment Separation**
```typescript
// Different configs per environment
// .env.development
JWT_SECRET=dev-secret-key

// .env.production  
JWT_SECRET=super-secure-production-key-256-chars-long
```

### **Client vs Server Environment Variables**
- **`NEXT_PUBLIC_*`** → Available in browser (client-side)
- **No prefix** → Server-side only (JWT secrets, etc.)

---

## ⚠️ **Important Configuration Notes**

### **1. Production Requirements**
```bash
# MUST be set in production
JWT_SECRET=your-production-secret-256-chars
JWT_REFRESH_SECRET=your-production-refresh-secret
NEXT_PUBLIC_API_BASE_URL=https://api.yourapp.com/api/v1
```

### **2. Development Defaults**
```typescript
// Safe for development, MUST change for production
jwtSecret: 'your-secret-key-change-this-in-production'
apiBaseURL: 'http://localhost:8080/api/v1'
```

### **3. Token Lifetimes**
```typescript
// Default token expiration
accessTokenTTLSeconds: 24 * 60 * 60,    // 24 hours
refreshTokenTTLSeconds: 7 * 24 * 60 * 60 // 7 days
```

### **4. Storage Keys**
```typescript
// localStorage keys (can be customized for security)
storageKeys: {
  authToken: 'authToken',                  // Encrypted JWT
  refreshToken: 'refreshToken',            // Encrypted refresh token
  userData: 'userData',                    // User profile data
  cryptoKey: 'finance_app_crypto_key'      // Encryption key
}
```

---

## 🔄 **Configuration Dependencies**

### **External Dependencies**
- **Next.js** environment variable system
- **Node.js** process.env
- **npm package.json** values (for OpenAPI)

### **Internal Dependencies**
- **Crypto utilities** → CRYPTO_CONFIG
- **API services** → API_CONFIG, AUTH_CONFIG
- **Storage utilities** → APP_CONFIG.storageKeys
- **Components** → ENV_CONFIG for environment detection

---

## 📋 **Configuration Checklist for Deployment**

### **✅ Required for Production**
- [ ] Set `JWT_SECRET` (256+ character random string)
- [ ] Set `JWT_REFRESH_SECRET` (different from JWT_SECRET)
- [ ] Set `NEXT_PUBLIC_API_BASE_URL` (production API endpoint)
- [ ] Verify `NODE_ENV=production`

### **⚙️ Optional Configuration**
- [ ] Customize `ACCESS_TOKEN_TTL_SECONDS` (default: 24h)
- [ ] Customize `REFRESH_TOKEN_TTL_SECONDS` (default: 7d)
- [ ] Enable `NEXT_PUBLIC_MEASURE_WEB_VITALS=true` for monitoring
- [ ] Customize storage keys for additional security

### **🔒 Security Best Practices**
- [ ] JWT secrets are random and unique
- [ ] API URLs use HTTPS in production
- [ ] Environment variables are not committed to git
- [ ] Different secrets for different environments

---

## 🎯 **Key Implementation Points**

### **1. Import Pattern**
```typescript
// Named imports for specific configs
import { AUTH_CONFIG, APP_CONFIG } from '@/config';

// Use throughout application
const token = localStorage.getItem(APP_CONFIG.storageKeys.authToken);
```

### **2. Environment Detection**
```typescript
// Feature flags based on environment
if (ENV_CONFIG.isDevelopment) {
  enableDevTools();
}
```

### **3. Configuration Validation**
- All environment variables are validated and sanitized
- Fallback values ensure app doesn't crash with missing config
- Type safety prevents configuration errors

### **4. Centralized Management**
- Single source of truth for all application settings
- Easy to modify behavior across entire application
- Clear separation of concerns by configuration domain

**This configuration system provides a robust, type-safe, and secure foundation for the entire finance application!** 🚀
