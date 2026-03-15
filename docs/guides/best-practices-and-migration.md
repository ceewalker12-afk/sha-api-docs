# Best Practices & Migration Guide

---

## Best Practices

### Authentication
- Store JWT tokens securely — never in `localStorage` for web apps (use `httpOnly` cookies)
- Refresh tokens before expiry using `POST /api/auth/refresh` — don't wait for a 401
- Use API keys for server-to-server integrations, JWT for user-facing apps
- Rotate API keys every 90 days

### Performance
- Use pagination on all list endpoints — never fetch all records at once
- Cache eligibility checks for 5 minutes — they don't change frequently
- Batch member lookups where possible instead of individual requests
- Use `per_page=50` max to keep response times under 200ms

### Error Handling
```python
response = requests.get(url, headers=headers)

if response.status_code == 401:
    # Token expired — refresh and retry
    token = refresh_token()
elif response.status_code == 429:
    # Rate limited — back off and retry after delay
    time.sleep(60)
elif response.status_code >= 500:
    # Server error — log and alert, do not retry immediately
    log_error(response)
```

### Claims Submission
- Always check member eligibility (`GET /api/members/{id}/eligibility`) before submitting a claim
- Include `treatment_type` and `description` — improves risk scoring accuracy
- Check `is_duplicate` in the response — if `true`, review before proceeding
- Monitor `risk_score` in the response — scores above 50 need manual review

### Fraud Detection
- Run `POST /api/fraud/risk-assessment` on high-value transactions before approval
- Monitor `GET /api/fraud/stats` daily — sudden spikes in `active_alerts` need attention
- Set up webhook notifications for new high-severity alerts

### Security
- Always use HTTPS in production — never send tokens over HTTP
- Validate all input on your side before sending to the API
- Use the minimum required role for each integration — don't use `admin` tokens for read-only operations
- Log all API calls on your side for your own audit trail

---

## Migration Guide

### Migrating from Paper-Based Systems

**Step 1 — Export existing data**
Export member records to CSV with columns:
`full_name, national_id, phone, dob, gender, county, sub_county, ward`

**Step 2 — Bulk import via API**
```python
import csv, requests

client = SHAClient(base_url, username, password)

with open('members.csv') as f:
    for row in csv.DictReader(f):
        client.register_member({
            "full_name": row["full_name"],
            "national_id": row["national_id"],
            "phone": row["phone"],
            "dob": row["dob"],
            "county": row["county"]
        })
```

**Step 3 — Verify import**
```bash
curl http://localhost:5000/api/members?per_page=1 \
  -H "Authorization: Bearer $TOKEN"
# Check "total" matches expected count
```

---

### Migrating from Another API System

**Step 1 — Map your existing endpoints to SHA endpoints**

| Your System | SHA System |
|-------------|-----------|
| `GET /patients` | `GET /api/members` |
| `POST /patients` | `POST /api/members` |
| `GET /patients/{id}/coverage` | `GET /api/members/{id}/eligibility` |
| `POST /claims` | `POST /api/claims` |
| `GET /claims/{id}` | `GET /api/claims/{id}` |

**Step 2 — Update authentication**

Replace your existing auth headers with JWT:
```http
# Old
X-Auth-Token: your_old_token

# New
Authorization: Bearer your_jwt_token
```

**Step 3 — Update response parsing**

SHA responses wrap data in consistent fields:
```json
{ "members": [...], "total": 150, "page": 1, "per_page": 20 }
```

**Step 4 — Test in sandbox before going live**

Contact ceewalker12@gmail.com for sandbox credentials.

---

## Troubleshooting Guide

| Problem | Cause | Solution |
|---------|-------|----------|
| `401 Token is missing` | No Authorization header | Add `Authorization: Bearer <token>` |
| `401 Token has expired` | Token older than 24hrs | Call `POST /api/auth/refresh` |
| `401 Invalid token` | Token tampered or wrong secret | Re-login to get fresh token |
| `403 Forbidden` | Role doesn't have permission | Use account with correct role |
| `400 field is required` | Missing required field in body | Check endpoint docs for required fields |
| `404 Not found` | Wrong ID or resource deleted | Verify the ID exists |
| `409 Conflict` | Duplicate username on register | Use a different username |
| `429 Too Many Requests` | Rate limit exceeded | Wait 60 seconds and retry |
| `500 Internal server error` | Server-side issue | Check `logs/sha_system.log` |
| USSD not responding | Wrong callback URL | Set webhook to `POST /ussd/callback` |
| M-Pesa callback not firing | Wrong callback URL in Daraja | Set to `POST /api/payments/callback` |
| Database errors on startup | DB not initialized | Run `python setup_system.py` |
