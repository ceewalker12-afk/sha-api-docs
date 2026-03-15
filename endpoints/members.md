# Members API

**Base Path:** `/api/members`

Manages SHA member registration, profile updates, and eligibility checks. All endpoints require authentication.

---

## Why This API Exists

Every Kenyan enrolled in SHA needs a member record. This API is the single source of truth for:
- Who is registered under SHA
- Their location (county, sub-county, ward)
- Their current status (active, suspended, etc.)
- Whether they are eligible to receive healthcare benefits

---

## Endpoints

### List Members

```
GET /api/members
```

Returns a paginated list of all members with optional filters.

**Headers Required:** `Authorization: Bearer <token>`

**Query Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `page` | integer | Page number (default: 1) |
| `per_page` | integer | Results per page (default: 20) |
| `status` | string | Filter by status: `active`, `suspended`, `inactive` |
| `search` | string | Search by name or national ID |

**Example Request:**
```
GET /api/members?status=active&search=Wanjiku&page=1&per_page=10
```

**Success Response `200`:**
```json
{
  "members": [
    {
      "id": 1,
      "full_name": "Jane Wanjiku",
      "national_id": "12345678",
      "phone": "0712345678",
      "email": "jane@example.com",
      "dob": "1990-05-15",
      "gender": "female",
      "county": "Nairobi",
      "sub_county": "Westlands",
      "ward": "Parklands",
      "status": "active",
      "registration_date": "2024-01-15 10:30:00"
    }
  ],
  "total": 1,
  "page": 1,
  "per_page": 10
}
```

---

### Get Single Member

```
GET /api/members/{member_id}
```

Returns full details for one member.

**Headers Required:** `Authorization: Bearer <token>`

**Success Response `200`:**
```json
{
  "id": 1,
  "full_name": "Jane Wanjiku",
  "national_id": "12345678",
  "phone": "0712345678",
  "county": "Nairobi",
  "status": "active",
  "registration_date": "2024-01-15 10:30:00"
}
```

**Error Response `404`:**
```json
{
  "error": "Member not found"
}
```

---

### Register New Member

```
POST /api/members
```

Creates a new SHA member record.

**Headers Required:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "full_name": "John Kamau",
  "national_id": "87654321",
  "phone": "0722000111",
  "email": "john.kamau@example.com",
  "dob": "1985-03-20",
  "gender": "male",
  "county": "Kiambu",
  "sub_county": "Thika",
  "ward": "Township"
}
```

**Required Fields:** `full_name`, `national_id`

**Success Response `201`:**
```json
{
  "message": "Member created successfully",
  "member_id": 105
}
```

---

### Update Member

```
PUT /api/members/{member_id}
```

Updates one or more fields on an existing member record.

**Headers Required:** `Authorization: Bearer <token>`

**Request Body** (send only fields you want to update):
```json
{
  "phone": "0733999888",
  "county": "Mombasa",
  "status": "active"
}
```

**Updatable Fields:** `full_name`, `national_id`, `phone`, `email`, `dob`, `gender`, `county`, `sub_county`, `ward`, `status`

**Success Response `200`:**
```json
{
  "message": "Member updated successfully"
}
```

---

### Delete Member

```
DELETE /api/members/{member_id}
```

Permanently removes a member record.

**Headers Required:** `Authorization: Bearer <token>`

**Success Response `200`:**
```json
{
  "message": "Member deleted successfully"
}
```

---

### Check Eligibility

```
GET /api/members/{member_id}/eligibility
```

Checks whether a member is currently eligible for SHA benefits.

**Headers Required:** `Authorization: Bearer <token>`

**Success Response `200`:**
```json
{
  "member_id": 1,
  "name": "Jane Wanjiku",
  "eligible": true,
  "status": "active",
  "registration_date": "2024-01-15 10:30:00"
}
```

> A member is eligible when their `status` is `"active"`.

---

## Member Status Values

| Status | Meaning |
|--------|---------|
| `active` | Enrolled and eligible for benefits |
| `suspended` | Temporarily blocked (e.g. non-payment) |
| `inactive` | No longer enrolled |

---

## Advantages of This API

- **Centralized registry** — one place for all member data across counties
- **Search by name or ID** — fast lookup for hospitals and CHPs at point of care
- **Eligibility check** — hospitals can verify coverage before treatment
- **Pagination** — handles large datasets (millions of members) efficiently
