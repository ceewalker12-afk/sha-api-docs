# Contribution Management API

**Base Path:** `/api/contributions`
**Target:** Insurance companies, pension funds, membership organizations

---

## Overview

Handles contribution calculations, payment recording, and reporting for SHA members. Supports multiple contribution tiers based on income, employment type, and household category.

**Key Features:**
- Dynamic contribution calculation by member category
- Payment recording and tracking
- Contribution history per member
- Arrears detection and reporting
- Integration with payment and means testing APIs

---

## Endpoints

### Calculate Contribution
```
POST /api/contributions/calculate
```
**Required Role:** `admin`, `user`, `chp`

Calculates the required contribution amount for a member based on their profile and means test result.

**Request Body:**
```json
{
  "member_id": 1,
  "employment_type": "formal",
  "monthly_income": 45000,
  "household_size": 4,
  "subsidy_tier": "none"
}
```

**Response `200`:**
```json
{
  "success": true,
  "member_id": 1,
  "monthly_contribution": 500,
  "annual_contribution": 6000,
  "contribution_tier": "standard",
  "subsidy_applied": 0,
  "effective_date": "2024-07-01"
}
```

---

### Record Contribution Payment
```
POST /api/contributions/record
```
**Required Role:** `admin`, `user`

Records a confirmed contribution payment against a member's account.

**Request Body:**
```json
{
  "member_id": 1,
  "amount": 500,
  "payment_method": "mpesa",
  "transaction_reference": "TXN-20240601-001",
  "period": "2024-06",
  "recorded_by": 5
}
```

**Response `201`:**
```json
{
  "success": true,
  "message": "Contribution recorded successfully",
  "contribution_id": 88,
  "member_id": 1,
  "period": "2024-06",
  "status": "paid"
}
```

---

### Get Member Contribution History
```
GET /api/contributions/member/<member_id>
```
**Required Role:** `admin`, `auditor`, `user`

**Response `200`:**
```json
{
  "success": true,
  "member_id": 1,
  "total_paid": 6000,
  "months_paid": 12,
  "arrears": 0,
  "last_payment": "2024-06-01",
  "history": [
    {
      "period": "2024-06",
      "amount": 500,
      "status": "paid",
      "payment_method": "mpesa"
    }
  ]
}
```

---

### Contribution Statistics
```
GET /api/contributions/stats
```
**Required Role:** `admin`, `auditor`

**Response `200`:**
```json
{
  "success": true,
  "total_collected": 15000000,
  "active_contributors": 28500,
  "defaulters": 1200,
  "collection_rate": 95.9
}
```

---

## Contribution Tiers

| Tier | Employment Type | Monthly Amount |
|------|----------------|----------------|
| Subsidized | Indigent/Vulnerable | Fully covered |
| Low income | Informal/casual | Reduced rate |
| Standard | Formal employed | Standard rate |
| Enhanced | High income | Enhanced rate |

---

## Use Cases
- SHA monthly contribution collection
- SACCO health benefit contributions
- Pension fund health rider payments
- NGO member subscription management
- School health program fee collection
