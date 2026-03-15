# Hospitals API

**Base Path:** `/api/hospitals`

Manages hospital registration, verification, and performance statistics within the SHA network.

---

## Why Hospital Management Matters

Only verified hospitals can submit claims against SHA. This API ensures:
- Every facility in the network has a unique facility code
- Hospitals can be verified or suspended by SHA administrators
- Performance data (approval rates, risk scores) is tracked per facility

---

## Endpoints

### List Hospitals

```
GET /api/hospitals
```

Returns a paginated list of hospitals.

**Headers Required:** `Authorization: Bearer <token>`

**Query Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `page` | integer | Page number (default: 1) |
| `per_page` | integer | Results per page (default: 20) |
| `status` | string | `active`, `pending`, `verified`, `suspended` |
| `search` | string | Search by name or facility code |

**Example Request:**
```
GET /api/hospitals?status=verified&search=Kenyatta
```

**Success Response `200`:**
```json
{
  "hospitals": [
    {
      "id": 1,
      "name": "Kenyatta National Hospital",
      "facility_code": "KNH001",
      "county": "Nairobi",
      "sub_county": "Starehe",
      "address": "Hospital Road, Nairobi",
      "phone": "0200000000",
      "email": "info@knh.or.ke",
      "status": "verified"
    }
  ],
  "total": 1,
  "page": 1,
  "per_page": 20
}
```

---

### Get Single Hospital

```
GET /api/hospitals/{hospital_id}
```

**Headers Required:** `Authorization: Bearer <token>`

**Success Response `200`:**
```json
{
  "id": 1,
  "name": "Kenyatta National Hospital",
  "facility_code": "KNH001",
  "county": "Nairobi",
  "status": "verified"
}
```

**Error Response `404`:**
```json
{
  "error": "Hospital not found"
}
```

---

### Register New Hospital

```
POST /api/hospitals
```

Adds a new hospital to the SHA network.

**Headers Required:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "name": "Mombasa County Referral Hospital",
  "facility_code": "MCR002",
  "county": "Mombasa",
  "sub_county": "Mvita",
  "address": "Moi Avenue, Mombasa",
  "phone": "0412000000",
  "email": "info@mombasareferral.go.ke"
}
```

**Required Fields:** `name`, `facility_code`

**Success Response `201`:**
```json
{
  "message": "Hospital registered successfully",
  "hospital_id": 25
}
```

---

### Hospital Statistics

```
GET /api/hospitals/{hospital_id}/stats
```

Returns performance metrics for a specific hospital.

**Headers Required:** `Authorization: Bearer <token>`

**Success Response `200`:**
```json
{
  "hospital_id": 1,
  "total_claims": 450,
  "approved": 390,
  "rejected": 60,
  "approval_rate": 86.67,
  "average_risk_score": 18.5
}
```

> A high `average_risk_score` or low `approval_rate` may indicate fraudulent billing patterns.

---

### Verify Hospital

```
POST /api/hospitals/{hospital_id}/verify
```

Updates a hospital's verification status.

**Headers Required:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "verified": true
}
```

Set `"verified": false` to revert to pending status.

**Success Response `200`:**
```json
{
  "message": "Hospital verification updated"
}
```

---

## Hospital Status Values

| Status | Meaning |
|--------|---------|
| `active` | Registered but not yet verified |
| `pending` | Under review |
| `verified` | Approved to submit claims |
| `suspended` | Blocked from submitting claims |

---

## Advantages of This API

- **Facility code system** — mirrors Kenya's KMHFL (Kenya Master Health Facility List) codes
- **Per-hospital stats** — identify high-risk facilities quickly
- **Verification workflow** — two-step process prevents unauthorized facilities from billing SHA
- **Search by name or code** — fast lookup during claim submission
