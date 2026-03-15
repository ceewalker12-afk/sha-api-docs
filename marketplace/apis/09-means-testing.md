# Means Testing API

**Base Path:** `/api/means-testing`
**Target:** Social services, welfare programs, non-profits

---

## Overview

Financial assessment and eligibility scoring for households. Determines subsidy levels, benefit eligibility, and categorizes households by economic status.

**Key Features:**
- Household financial assessment
- Eligibility scoring algorithm
- Subsidy tier determination
- Household economic categorization
- Integration with member registration

---

## Endpoints

### Conduct Means Test
```
POST /api/means-testing/conduct
```
**Required Role:** `admin`, `chp`, `user`

**Request Body:**
```json
{
  "household_id": 10,
  "conducted_by": 5,
  "monthly_income": 8000,
  "household_size": 5,
  "dwelling_type": "rental",
  "assets": {
    "land": false,
    "vehicle": false,
    "livestock": false
  },
  "employment_status": "informal",
  "location": {
    "county": "Nairobi",
    "ward": "Mathare"
  }
}
```

**Response `200`:**
```json
{
  "success": true,
  "household_id": 10,
  "eligibility_score": 78,
  "category": "vulnerable",
  "subsidy_tier": "full",
  "eligible_for": ["full_subsidy", "sha_coverage"],
  "assessment_date": "2024-06-01T10:00:00Z"
}
```

---

### Get Household Assessment
```
GET /api/means-testing/household/<id>
```
**Required Role:** `admin`, `auditor`, `chp`

Returns the latest means test result and history for a household.

**Response `200`:**
```json
{
  "success": true,
  "household_id": 10,
  "latest_assessment": {
    "eligibility_score": 78,
    "category": "vulnerable",
    "subsidy_tier": "full",
    "conducted_by": "CHP Jane Wanjiku",
    "assessment_date": "2024-06-01T10:00:00Z"
  },
  "history": []
}
```

---

## Eligibility Categories

| Category | Score Range | Subsidy Tier |
|----------|-------------|-------------|
| Indigent | 85–100 | Full subsidy |
| Vulnerable | 65–84 | Full subsidy |
| Low income | 45–64 | Partial subsidy |
| Middle income | 25–44 | Minimal subsidy |
| Self-pay | 0–24 | No subsidy |

---

## Use Cases
- SHA subsidy eligibility determination
- Social welfare program targeting
- NGO beneficiary selection
- County bursary fund allocation
- Food assistance program eligibility
