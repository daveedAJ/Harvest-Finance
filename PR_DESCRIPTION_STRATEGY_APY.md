# 🚀 Create Pull Request - Strategy APY & Vault Scoring

## Direct PR Creation Link

**Click this link to create your Pull Request:**

### https://github.com/daveedAJ/Harvest-Finance/compare/main...feat/strategy-apy-clean-v3

---

## 📋 PR Title

```
feat: implement strategy APY tracking, vault scoring, and email verification
```

## 📝 PR Description

```markdown
## Summary

This PR implements a comprehensive vault strategy scoring system and strategy APY tracking, addressing GitHub issues #504 and #977, along with email verification (#502, #975).

## Features

### Strategy APY Tracking
- ✅ Strategy APY history tracking with database persistence
- ✅ Vault APY history entity and migration
- ✅ Strategy entity for tracking APY data

### Vault Strategy Scoring
- ✅ Strategy score (0-100) for each vault based on weighted components
- ✅ Risk-adjusted APY scoring (40% weight)
- ✅ TVL stability scoring (25% weight)
- ✅ Historical drawdown scoring (20% weight)
- ✅ Operator reputation scoring (15% weight)
- ✅ Hourly score recalculation via cron job
- ✅ Score history persistence in database
- ✅ GET /vaults/:id/score-breakdown API endpoint
- ✅ Comprehensive unit tests

### Email Verification
- ✅ Email verification with signed JWT tokens
- ✅ 24-hour token expiry
- ✅ Resend verification endpoint with rate limiting (3 requests/hour)
- ✅ Protected vault operations for unverified users

## Changes

### New Files
- `src/analytics/scoring.service.ts` - Scoring service with all calculation logic
- `src/analytics/scoring.service.spec.ts` - Unit tests for scoring service
- `src/vaults/dto/score-breakdown.dto.ts` - DTO for score breakdown response
- `src/database/entities/vault-score-history.entity.ts` - Entity for score history
- `src/database/entities/strategy.entity.ts` - Strategy entity
- `src/database/entities/vault-apy-history.entity.ts` - Vault APY history entity
- `src/database/migrations/1700000000017-CreateStrategyAndApyHistory.ts` - Migration for strategy and APY history
- `src/database/migrations/1700000000018-CreateVaultScoreHistory.ts` - Migration for score history table
- `src/database/migrations/1700000000023-AddEmailVerificationToUsers.ts` - Migration for email verification
- `docs/scoring-model.md` - Documentation for the scoring model

### Modified Files
- `src/database/entities/vault.entity.ts` - Added strategyScore column
- `src/database/entities/index.ts` - Export new entities
- `src/database/data-source.ts` - Added migrations
- `src/analytics/analytics.module.ts` - Added ScoringService
- `src/vaults/vaults.controller.ts` - Added score-breakdown endpoint
- `src/vaults/vaults.module.ts` - Added AnalyticsModule and entities
- `src/vaults/vaults.service.ts` - Added email verification checks
- `src/farm-vaults/farm-vaults.service.ts` - Added email verification checks
- `src/farm-vaults/farm-vaults.module.ts` - Added AuthModule import
- `src/admin/admin.service.ts` - Added email verification check
- `src/admin/admin.module.ts` - Added AuthModule import
- `src/auth/auth.service.ts` - Added email verification methods
- `src/auth/auth.controller.ts` - Added verification endpoints
- `src/auth/auth.service.spec.ts` - Added email verification tests
- `src/app.module.ts` - Added entities and migrations
- `backend/test/auth.e2e-spec.ts` - Added E2E tests

## Score Calculation

The overall strategy score is calculated as:

```
strategyScore = round(
  apyScore * 0.4 +
  tvlStabilityScore * 0.25 +
  drawdownScore * 0.2 +
  operatorScore * 0.15
)
```

## How to Test

```bash
# Run tests
cd harvest-finance/backend
npm test -- src/analytics/scoring.service.spec.ts
npm test -- src/auth/auth.service.spec.ts

# Build to verify no compilation errors
npm run build

# Run E2E tests
npm run test:e2e
```

## API Endpoints

### GET /vaults/:vaultId/score-breakdown
Returns the detailed score breakdown for a specific vault.

### GET /auth/verify-email?token=...
Validates the JWT verification token and marks the user's email as verified.

### POST /auth/resend-verification
Generates and sends a new 24-hour verification email.

## Checklist

- [x] Code follows project style guidelines
- [x] No new dependencies added
- [x] New tests included and passing
- [x] Documentation updated
- [x] All acceptance criteria met

Fixes #504 #977 #502 #975
```

---

## 🎯 Quick Steps

1. **Click the link above** - Takes you to GitHub comparison page
2. **Review the changes** - All Strategy APY, Vault Scoring, and Email Verification implementation
3. **Click "Create pull request"** - Green button on the right
4. **Add PR description** - Use the template above

## 📊 Branch Information

- **Branch**: `feat/strategy-apy-clean-v3`
- **Target**: `main`
- **Repository**: https://github.com/daveedAJ/Harvest-Finance
- **Commits**: 1 commit with 21 files changed (1452 insertions, 383 deletions)