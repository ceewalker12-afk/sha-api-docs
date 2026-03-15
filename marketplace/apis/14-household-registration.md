# Household Registration API

**Base Path:** `/api/households`
**Target:** Social services, census bureaus, community organizations

---

## Overview

Registers and manages household units with dependant tracking, eligibility checks, and means testing integration. Designed for CHP field use with offline-capable data collection.

**Key Features:**
- Household unit registration with head-of-household
- Dependant addition and management
- Household eligibility status
- Means testing integration
- Location-based household tracking (county, ward, village)
- Offline-first design for field use

---

## Endpoints

### Register Household
```
POST /api/households/register
```
**Required Role:** `admin`, `chp`, `user`

**Request Body:**
```json
{
  "head_of_household": {
    "full_name": "John Kamau",
    "national_id": "12345678",
    "phone": "0712345678",
    "dob": "1978-03-15",
    "gender": "male"
  },
  "location": {
    "county": "Kiambu",
    "sub_county": "Thika",
    "ward": "Township",
    "village": "Makongeni",
    "house_number": "B12"
  },
  "household_size": 5,
  "registered_by": 8
}
```

**Response `201`:**
```json
{
  "success": true,
  "message": "Household registered successfully",
  "household_id": "HH-KIAMBU-2024-00234",
  "head_member_id": 105,
  "registered_at": "2024-06-01T10:00:00Z"
}
```

---

### Add Dependant
```
POST /api/households/<id>/add-dependant
```
**Required Role:** `admin`, `chp`, `user`

**Request Body:**
```json
{
  "full_name": "Mary Kamau",
  "dob": "2010-07-20",
  "gender": "female",
  "relationship": "daughter",
  "national_id": null
}
```

**Response `201`:**
```json
{
  "success": true,
  "message": "Dependant added successfully",
  "member_id": 106,
  "household_id": "HH-KIAMBU-2024-00234"
}
```

---

### Get Household
```
GET /api/households/<id>
```
**Required Role:** `admin`, `chp`, `auditor`

Returns full household record including all members and latest means test result.

**Response `200`:**
```json
{
  "success": true,
  "household_id": "HH-KIAMBU-2024-00234",
  "head_of_household": "John Kamau",
  "location": {
    "county": "Kiambu",
    "ward": "Township",
    "village": "Makongeni"
  },
  "household_size": 5,
  "members": [
    { "member_id": 105, "name": "John Kamau", "relationship": "head" },
    { "member_id": 106, "name": "Mary Kamau", "relationship": "daughter" }
  ],
  "means_test": {
    "category": "low_income",
    "subsidy_tier": "partial",
    "last_assessed": "2024-06-01"
  },
  "eligibility_status": "active"
}
```

---

### Search Households
```
GET /api/households
```
**Required Role:** `admin`, `chp`, `auditor`

**Query Parameters:** `county`, `ward`, `village`, `head_name`, `national_id`, `page`, `per_page`

---

### Household Statistics
```
GET /api/households/stats
```
**Required Role:** `admin`, `auditor`

**Response `200`:**
```json
{
  "total_households": 45200,
  "total_members": 198400,
  "by_county": {
    "Nairobi": 12400,
    "Kiambu": 8900
  },
  "by_subsidy_tier": {
    "full": 18200,
    "partial": 15600,
    "none": 11400
  }
}
```

---

## Use Cases
- SHA household enrollment drives
- National census data collection
- Social welfare beneficiary registration
- Community health mapping
- School feeding program targeting
