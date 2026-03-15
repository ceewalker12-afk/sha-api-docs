# Fraud Detection API

**Base Path:** `/api/fraud`

Provides risk assessment, fraud alert management, and high-risk claim identification to protect SHA funds from fraudulent billing.

---

## How Fraud Detection Works

Every claim submitted gets a **risk score from 0 to 100**:
- Score is calculated at submission time
- Scores above 50 are flagged as high-risk
- High-risk assessments automatically create fraud alerts
- Claims officers can investigate and resolve alerts

---

## Endpoints

### List Fraud Alerts

```
GET /api/fraud/alerts
```

Returns all fraud alerts with optional filters.

**Headers Required:** `Authorization: Bearer <token>`

**Query Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `page` | integer | Page number (default: 1) |
| `per_page` | integer | Results per page (default: 20) |
| `status` | string | `active`, `resolved` |
| `severity` | string | `low`, `medium`, `high` |

**Example Request:**
```
GET /api/fraud/alerts?status=active&severity=high
```

**Success Response `200`:**
```json
{
  "alerts": [
    {
      "id": 7,
      "alert_type": "High Risk Claim",
      "description": "Risk score 87 detected for claim ID 55",
      "severity": "high",
      "status": "active",
      "created_at": "2024-06-01 09:20:00"
    }
  ],
  "total": 1,
  "page": 1,
  "per_page": 20
}
```

---

### Get Single Alert

```
GET /api/fraud/alerts/{alert_id}
```

**Headers Required:** `Authorization: Bearer <token>`

**Success Response `200`:**
```json
{
  "id": 7,
  "alert_type": "High Risk Claim",
  "description": "Risk score 87 detected for claim ID 55",
  "severity": "high",
  "status": "active",
  "created_at": "2024-06-01 09:20:00"
}
```

**Error Response `404`:**
```json
{
  "error": "Alert not found"
}
```

---

### Resolve Alert

```
POST /api/fraud/alerts/{alert_id}/resolve
```

Marks a fraud alert as resolved after investigation.

**Headers Required:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "resolution": "Investigated and confirmed legitimate. Hospital provided supporting documents."
}
```

**Success Response `200`:**
```json
{
  "message": "Alert resolved"
}
```

---

### Risk Assessment

```
POST /api/fraud/risk-assessment
```

Runs a risk assessment on a specific claim or member and returns a score with a recommendation.

**Headers Required:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "type": "claim",
  "id": 55
}
```

**Supported Types:** `claim`, `member`

**Success Response `200`:**
```json
{
  "type": "claim",
  "id": 55,
  "risk_score": 87,
  "risk_level": "high",
  "recommendation": "Reject"
}
```

**Recommendation Values:**

| Risk Level | Score Range | Recommendation |
|------------|-------------|----------------|
| `low` | 0 – 20 | Approve |
| `medium` | 21 – 50 | Review |
| `high` | 51 – 100 | Reject |

> High-risk assessments automatically create a fraud alert in the system.

---

### High-Risk Claims

```
GET /api/fraud/high-risk-claims
```

Returns claims above a specified risk score threshold.

**Headers Required:** `Authorization: Bearer <token>`

**Query Parameters:**

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `threshold` | integer | `50` | Minimum risk score to include |

**Example Request:**
```
GET /api/fraud/high-risk-claims?threshold=70
```

**Success Response `200`:**
```json
{
  "claims": [
    {
      "id": 55,
      "member_name": "Jane Wanjiku",
      "hospital_name": "Kenyatta National Hospital",
      "claim_amount": 150000.00,
      "risk_score": 87,
      "status": "pending"
    }
  ],
  "count": 1
}
```

---

### Fraud Statistics

```
GET /api/fraud/stats
```

Returns a summary of fraud detection activity.

**Headers Required:** `Authorization: Bearer <token>`

**Success Response `200`:**
```json
{
  "active_alerts": 12,
  "resolved_alerts": 45,
  "high_severity": 8,
  "high_risk_claims": 23
}
```

---

## Alert Severity Levels

| Severity | Meaning |
|----------|---------|
| `low` | Minor anomaly, monitor only |
| `medium` | Requires review before processing |
| `high` | Immediate investigation required |

---

## Advantages of This API

- **Automatic alerting** — no manual monitoring needed; high-risk events create alerts instantly
- **Adjustable threshold** — tune sensitivity for high-risk claim queries
- **Resolution tracking** — full audit trail of how each alert was handled
- **Separate stats endpoint** — powers real-time fraud dashboards
- **Protects public funds** — prevents fraudulent claims from being paid out
