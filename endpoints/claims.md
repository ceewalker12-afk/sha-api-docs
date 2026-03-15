# Claims API

**Base Path:** `/api/claims`

Handles the full lifecycle of health insurance claims — from submission by hospitals to approval or rejection by SHA officers. Includes duplicate detection and risk scoring.

---

## How Claims Work

1. A hospital submits a claim after treating an SHA member
2. The system automatically calculates a **risk score** (0–100)
3. The system checks for **duplicate claims** (same member, hospital, treatment on the same day)
4. Claims officers review and approve or reject
5. Approved claims trigger payment processing

---

## Endpoints

### List Claims

```
GET /api/claims
```

Returns paginated claims with optional filters.

**Headers Required:** `Authorization: Bearer <token>`

**Query Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `page` | integer | Page number (default: 1) |
| `per_page` | integer | Results per page (default: 20) |
| `status` | string | `pending`, `approved`, `rejected` |
| `member_id` | integer | Filter by member |
| `hospital_id` | integer | Filter by hospital |

**Example Request:**
```
GET /api/claims?status=pending&hospital_id=3
```

**Success Response `200`:**
```json
{
  "claims": [
    {
      "id": 55,
      "member_id": 1,
      "member_name": "Jane Wanjiku",
      "hospital_id": 3,
      "hospital_name": "Kenyatta National Hospital",
      "treatment_type": "Outpatient",
      "claim_amount": 5000.00,
      "description": "Malaria treatment",
      "submission_date": "2024-06-01 09:15:00",
      "status": "pending",
      "risk_score": 12,
      "is_duplicate": 0
    }
  ],
  "total": 1,
  "page": 1,
  "per_page": 20
}
```

---

### Get Single Claim

```
GET /api/claims/{claim_id}
```

**Headers Required:** `Authorization: Bearer <token>`

**Success Response `200`:**
```json
{
  "id": 55,
  "member_name": "Jane Wanjiku",
  "hospital_name": "Kenyatta National Hospital",
  "claim_amount": 5000.00,
  "status": "pending",
  "risk_score": 12,
  "is_duplicate": 0
}
```

---

### Submit New Claim

```
POST /api/claims
```

Submits a new claim. Automatically runs duplicate detection and risk scoring.

**Headers Required:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "member_id": 1,
  "hospital_id": 3,
  "treatment_type": "Outpatient",
  "claim_amount": 5000.00,
  "description": "Malaria treatment - 3 day course"
}
```

**Required Fields:** `member_id`, `hospital_id`, `claim_amount`

**Success Response `201`:**
```json
{
  "message": "Claim submitted successfully",
  "claim_id": 56,
  "risk_score": 12,
  "is_duplicate": false
}
```

> If `is_duplicate` is `true`, the claim is flagged for manual review.

---

### Update Claim Status

```
PUT /api/claims/{claim_id}
```

Manually updates a claim's status.

**Headers Required:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "status": "approved"
}
```

**Success Response `200`:**
```json
{
  "message": "Claim updated successfully"
}
```

---

### Approve Claim

```
POST /api/claims/{claim_id}/approve
```

Shortcut endpoint to approve a specific claim.

**Headers Required:** `Authorization: Bearer <token>`

**Success Response `200`:**
```json
{
  "message": "Claim approved"
}
```

---

### Reject Claim

```
POST /api/claims/{claim_id}/reject
```

Rejects a claim with an optional reason.

**Headers Required:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "reason": "Duplicate claim submitted within same day"
}
```

**Success Response `200`:**
```json
{
  "message": "Claim rejected"
}
```

---

### Claims Statistics

```
GET /api/claims/stats
```

Returns a summary of all claims by status and total approved amount.

**Headers Required:** `Authorization: Bearer <token>`

**Success Response `200`:**
```json
{
  "pending": 45,
  "approved": 320,
  "rejected": 18,
  "total": 383,
  "total_approved_amount": 1850000.00
}
```

---

## Risk Score Guide

| Score Range | Risk Level | Recommended Action |
|-------------|------------|-------------------|
| 0 – 20 | Low | Auto-approve eligible |
| 21 – 50 | Medium | Manual review recommended |
| 51 – 100 | High | Flag for investigation |

---

## Claim Status Values

| Status | Meaning |
|--------|---------|
| `pending` | Submitted, awaiting review |
| `approved` | Verified and approved for payment |
| `rejected` | Denied with reason recorded |

---

## Advantages of This API

- **Automatic duplicate detection** — prevents double billing on the same day
- **Risk scoring on submission** — flags suspicious claims immediately
- **Separate approve/reject endpoints** — clean workflow for claims officers
- **Stats endpoint** — real-time dashboard data without complex queries
