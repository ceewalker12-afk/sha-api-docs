# Step-by-Step Tutorials

---

## Tutorial 1 — Register a Household and Check Eligibility

**Goal:** A CHP registers a new household in the field and checks their SHA eligibility.

### Step 1 — Login as CHP
```bash
curl -X POST http://localhost:5000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"username": "chp_nairobi_01", "password": "your_password"}'
```
Save the `token` from the response.

### Step 2 — Search if member already exists
```bash
curl "http://localhost:5000/api/members?search=12345678" \
  -H "Authorization: Bearer $TOKEN"
```

### Step 3 — Register if not found
```bash
curl -X POST http://localhost:5000/api/members \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "full_name": "Peter Otieno",
    "national_id": "12345678",
    "phone": "0700111222",
    "dob": "1978-11-03",
    "county": "Kisumu",
    "sub_county": "Kisumu Central",
    "ward": "Market Milimani"
  }'
```
Note the `member_id` from the response.

### Step 4 — Check eligibility
```bash
curl http://localhost:5000/api/members/105/eligibility \
  -H "Authorization: Bearer $TOKEN"
```

**Expected response:**
```json
{ "eligible": true, "status": "active", "name": "Peter Otieno" }
```

---

## Tutorial 2 — Submit and Process a Claim

**Goal:** A hospital submits a claim and a claims officer approves it.

### Step 1 — Hospital verifies member eligibility
```bash
curl http://localhost:5000/api/members/1/eligibility \
  -H "Authorization: Bearer $HOSPITAL_TOKEN"
```

### Step 2 — Submit claim after treatment
```bash
curl -X POST http://localhost:5000/api/claims \
  -H "Authorization: Bearer $HOSPITAL_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "member_id": 1,
    "hospital_id": 3,
    "treatment_type": "Outpatient",
    "claim_amount": 5000,
    "description": "Malaria treatment - 3 day course"
  }'
```

Check `risk_score` and `is_duplicate` in the response.

### Step 3 — Claims officer reviews pending claims
```bash
curl "http://localhost:5000/api/claims?status=pending" \
  -H "Authorization: Bearer $OFFICER_TOKEN"
```

### Step 4 — Approve the claim
```bash
curl -X POST http://localhost:5000/api/claims/56/approve \
  -H "Authorization: Bearer $OFFICER_TOKEN"
```

---

## Tutorial 3 — Investigate a Fraud Alert

**Goal:** A fraud officer investigates and resolves a high-risk alert.

### Step 1 — View active high-severity alerts
```bash
curl "http://localhost:5000/api/fraud/alerts?status=active&severity=high" \
  -H "Authorization: Bearer $TOKEN"
```

### Step 2 — Review the flagged claim
```bash
curl http://localhost:5000/api/claims/55 \
  -H "Authorization: Bearer $TOKEN"
```

### Step 3 — Check hospital track record
```bash
curl http://localhost:5000/api/hospitals/3/stats \
  -H "Authorization: Bearer $TOKEN"
```

### Step 4 — Run manual risk assessment
```bash
curl -X POST http://localhost:5000/api/fraud/risk-assessment \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"type": "claim", "id": 55}'
```

### Step 5a — If legitimate, resolve and approve
```bash
curl -X POST http://localhost:5000/api/fraud/alerts/7/resolve \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"resolution": "Verified with hospital. Legitimate claim."}'

curl -X POST http://localhost:5000/api/claims/55/approve \
  -H "Authorization: Bearer $TOKEN"
```

### Step 5b — If fraudulent, reject and resolve
```bash
curl -X POST http://localhost:5000/api/claims/55/reject \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"reason": "Fraudulent billing confirmed."}'

curl -X POST http://localhost:5000/api/fraud/alerts/7/resolve \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"resolution": "Fraud confirmed. Claim rejected. Referred to compliance."}'
```

---

## Tutorial 4 — Send a Payment and Confirm via Callback

**Goal:** Collect an SHA contribution via M-Pesa.

### Step 1 — Initiate STK Push
```bash
curl -X POST http://localhost:5000/api/payments/initiate \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "phone": "0712345678",
    "amount": 500,
    "reference": "SHA-CONTRIB-2024-001",
    "description": "SHA Monthly Contribution"
  }'
```

### Step 2 — Poll for status
```bash
curl http://localhost:5000/api/payments/status/TXN-20240601-001 \
  -H "Authorization: Bearer $TOKEN"
```

M-Pesa will also POST to `/api/payments/callback` automatically when payment completes.
