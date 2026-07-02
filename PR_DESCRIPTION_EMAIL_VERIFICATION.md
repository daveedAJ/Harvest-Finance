# Email Verification Implementation (Issues #502, #975)

## Summary

Implements email verification for user accounts using signed JWT tokens. Unverified users can log in but are blocked from creating vaults and making deposits until they verify their email address.

## Changes

### 1. User Entity & Migration
- **`backend/src/database/entities/user.entity.ts`**: Removed `emailVerificationToken` field, kept `emailVerifiedAt: Date | null`
- **`backend/src/database/migrations/1700000000023-AddEmailVerificationToUsers.ts`**: New migration adding `email_verified_at` column and index, dropping old `email_verification_token` column

### 2. Auth Service Updates
- **`backend/src/auth/auth.service.ts`**:
  - Added `verificationTokenExpiry = '24h'` constant
  - `register()` now generates a signed JWT verification token and sends verification email
  - `verifyEmail()` validates JWT type and sets `emailVerifiedAt`
  - `resendVerification()` generates new 24-hour JWT with rate limiting (3 requests/hour)
  - Added `sendVerificationEmail()` private method
  - Added `isEmailVerified()` public method

### 3. Auth Controller Updates
- **`backend/src/auth/auth.controller.ts`**:
  - Improved `GET /auth/verify-email?token=...` with Swagger documentation
  - `POST /auth/resend-verification` protected with `@UseGuards(RateLimitGuard)` and `@RateLimit({ limit: 3, ttl: 3600 })`

### 4. Vault Operation Protection
- **`backend/src/farm-vaults/farm-vaults.service.ts`**: Added `AuthService` dependency and email verification checks in `createVault()` and `deposit()`
- **`backend/src/vaults/vaults.service.ts`**: Added `AuthService` dependency and email verification checks in `depositToVault()` and `batchDepositToVaults()`
- **`backend/src/admin/admin.service.ts`**: Added `AuthService` dependency and email verification check in `createVault()`

### 5. Module Updates
- **`backend/src/farm-vaults/farm-vaults.module.ts`**: Added `AuthModule` import
- **`backend/src/admin/admin.module.ts`**: Added `AuthModule` import
- **`backend/src/app.module.ts`**: Added `AddEmailVerificationToUsers1700000000023` migration import
- **`backend/src/database/data-source.ts`**: Added migration import

### 6. Tests
- **`backend/src/auth/auth.service.spec.ts`**: Added comprehensive email verification unit tests
- **`backend/test/auth.e2e-spec.ts`**: Added integration tests for registration, verification, resend, rate limiting, and protected operations
- **`backend/src/farm-vaults/farm-vaults.service.spec.ts`**: Added email verification protection tests
- **`backend/src/vaults/vaults.service.spec.ts`**: Added `AuthService` mock and email verification tests

## API Endpoints

### `GET /auth/verify-email?token=...`
Validates the JWT verification token and marks the user's email as verified.

**Response:**
- `200 OK`: Email verified successfully
- `400 Bad Request`: Invalid or expired token

### `POST /auth/resend-verification`
Generates and sends a new 24-hour verification email.

**Response:**
- `200 OK`: Verification email sent
- `429 Too Many Requests`: Rate limit exceeded (3 requests/hour)
- `400 Bad Request`: Email already verified

## Protected Operations

Unverified users receive `403 Forbidden` with message `"Email verification required"` when attempting:
- Create vaults (`POST /farm-vaults`, `POST /admin/vaults`)
- Make deposits (`POST /vaults/{id}/deposit`, `POST /farm-vaults/{id}/deposit`, `POST /vaults/batch-deposit`)

## Migration

Run the migration to update the database schema:
```bash
cd backend && npm run migration:run
```

## Testing

```bash
cd backend
npm run test                    # Unit tests
npm run test:e2e               # E2E tests
npm run lint                   # Lint check
```

## Breaking Changes

None. Existing APIs remain unchanged. Unverified users can still log in but are restricted from vault creation and deposits.
