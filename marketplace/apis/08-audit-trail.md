# Audit Trail API

**Base Path:** `/api/audit`
**Target:** Compliance teams, security departments, legal firms

---

## Overview

Immutable action logging with security event tracking, user activity analysis, anomaly detection, and exportable compliance reports.

**Key Features:**
- Every system action logged automatically
- Security event log (logins, failures, IP tracking)
- High-risk activity detection with configurable threshold
- Per-user activity summaries with anomaly flags
- Export to CSV/JSON for compliance reporting
- Log purge with configurable retention policy

---

## Endpoints

### Get Audit Logs
```
GET /api/audit/logs
```
**Required Role:** `admin`, `auditor`

**Query Parameters:**
| Parameter | Description |
|-----------|-------------|
| `user_identity` | Filter by username |
| `action` | Filter by action type |
| `start_date` | From date (ISO format) |
| `end_date` | To date (ISO format) |
| `min_score` | Minimum risk score |
| `limit` | Max results (default: 100) |

**Response `200`:**
```json
{
  "success": true,
  "logs": [
    {
      "id": 1,
      "user_identity": "claims_officer_01",
      "action": "CLAIM_APPROVED",
      "doc_id": 56,
      "score": 12,
      "timestamp": "2024-06-01T09:15:00Z",
      "ip_address": "192.168.1.100"
    }
  ],
  "count": 1
}
```

---

### Get Security Logs
```
GET /api/audit/logs/security
```
**Required Role:** `admin`, `auditor`

**Query Parameters:** `event_type`, `username`, `ip_address`, `start_date`, `end_date`, `limit`

Returns login attempts, failures, permission violations, and suspicious access patterns.

---

### Get High-Risk Activities
```
GET /api/audit/logs/high-risk
```
**Required Role:** `admin`, `auditor`

**Query Parameters:**
| Parameter | Default | Description |
|-----------|---------|-------------|
| `threshold` | `70` | Minimum risk score to include |

---

### Audit Statistics
```
GET /api/audit/stats
```
**Required Role:** `admin`, `auditor`

Returns system-wide action counts, top users by activity, and risk distribution.

---

### User Activity Summary
```
GET /api/audit/user-activity/<username>
```
**Required Role:** `admin`, `auditor`

**Query Parameters:** `days` (default: 30)

Returns action history and detected anomalies for a specific user.

**Response `200`:**
```json
{
  "success": true,
  "username": "claims_officer_01",
  "summary": { "total_actions": 145, "approvals": 98, "rejections": 12 },
  "anomalies": [],
  "anomaly_count": 0
}
```

---

### Export Audit Report
```
POST /api/audit/export
```
**Required Role:** `admin`

**Request Body:**
```json
{
  "start_date": "2024-01-01",
  "end_date": "2024-06-30",
  "format": "csv"
}
```

**Supported formats:** `csv`, `json`

---

### Create Manual Log Entry
```
POST /api/audit/log
```
**Required Role:** `admin`, `auditor`, `user`, `doctor`

For logging custom events not automatically captured.

---

### Purge Old Logs
```
POST /api/audit/purge
```
**Required Role:** `admin`

```json
{ "retention_days": 365 }
```

---

## Use Cases
- Regulatory compliance reporting (Kenya DPA, HIPAA)
- Security incident investigation
- Staff accountability monitoring
- Legal evidence for fraud prosecution
- SOC 2 audit preparation
