# Fund Management API

**Base Path:** `/api/funds`
**Target:** Financial institutions, investment firms, treasury departments

---

## Overview

Multi-fund balance tracking, allocation management, and financial reporting. Supports SHA's multiple benefit funds (primary care, inpatient, chronic disease, emergency, etc.) with real-time balance visibility.

**Key Features:**
- Multi-fund balance tracking
- Fund allocation and disbursement recording
- Real-time balance queries per fund code
- Aggregate balance reporting across all funds
- Transaction history per fund
- Low-balance alerts

---

## Endpoints

### Get Fund Balance
```
GET /api/funds/balance/<code>
```
**Required Role:** `admin`, `auditor`

Returns the current balance and recent transactions for a specific fund.

**Example:**
```
GET /api/funds/balance/SHA-PRIMARY-CARE
```

**Response `200`:**
```json
{
  "success": true,
  "fund_code": "SHA-PRIMARY-CARE",
  "fund_name": "Primary Care Fund",
  "current_balance": 45000000,
  "currency": "KES",
  "last_updated": "2024-06-01T08:00:00Z",
  "monthly_inflow": 8500000,
  "monthly_outflow": 7200000,
  "status": "healthy"
}
```

---

### Get All Fund Balances
```
GET /api/funds/balances
```
**Required Role:** `admin`, `auditor`

Returns balances across all registered funds in a single call.

**Response `200`:**
```json
{
  "success": true,
  "funds": [
    {
      "fund_code": "SHA-PRIMARY-CARE",
      "fund_name": "Primary Care Fund",
      "current_balance": 45000000,
      "status": "healthy"
    },
    {
      "fund_code": "SHA-INPATIENT",
      "fund_name": "Inpatient Fund",
      "current_balance": 120000000,
      "status": "healthy"
    },
    {
      "fund_code": "SHA-CHRONIC",
      "fund_name": "Chronic Disease Fund",
      "current_balance": 3200000,
      "status": "low"
    }
  ],
  "total_balance": 168200000,
  "currency": "KES"
}
```

---

### Record Fund Transaction
```
POST /api/funds/transaction
```
**Required Role:** `admin`

Records an inflow (contribution) or outflow (claim payment) against a fund.

**Request Body:**
```json
{
  "fund_code": "SHA-PRIMARY-CARE",
  "transaction_type": "outflow",
  "amount": 5000,
  "reference": "CLAIM-56-PAYMENT",
  "description": "Claim #56 payment to KNH",
  "recorded_by": 1
}
```

**Response `201`:**
```json
{
  "success": true,
  "transaction_id": "TXN-FUND-20240601-001",
  "fund_code": "SHA-PRIMARY-CARE",
  "new_balance": 44995000
}
```

---

### Fund Transaction History
```
GET /api/funds/history/<code>
```
**Required Role:** `admin`, `auditor`

**Query Parameters:** `start_date`, `end_date`, `transaction_type`, `limit`

---

## Fund Status Indicators

| Status | Meaning |
|--------|---------|
| `healthy` | Balance above minimum threshold |
| `low` | Balance below 10% of monthly outflow |
| `critical` | Balance below 5% — immediate action needed |
| `frozen` | Fund temporarily suspended |

---

## Use Cases
- SHA benefit fund treasury management
- County health fund monitoring
- Insurance reserve fund tracking
- NGO program fund management
- Multi-fund investment portfolio tracking
