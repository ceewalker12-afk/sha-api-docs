# Webhooks & API Keys

---

## Webhooks

Webhooks allow the SHA system to push real-time event notifications to your application instead of you polling for updates.

### Supported Webhook Events

| Event | Trigger |
|-------|---------|
| `claim.submitted` | New claim submitted |
| `claim.approved` | Claim approved by officer |
| `claim.rejected` | Claim rejected |
| `fraud.alert.created` | New fraud alert generated |
| `fraud.alert.resolved` | Fraud alert resolved |
| `member.registered` | New member registered |
| `member.status_changed` | Member status updated |
| `payment.completed` | M-Pesa payment confirmed |
| `payment.failed` | Payment failed |
| `hospital.verified` | Hospital verification updated |

---

### Registering a Webhook

```bash
curl -X POST http://localhost:5000/api/webhooks/register \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://your-app.com/sha-webhook",
    "events": ["claim.approved", "fraud.alert.created"],
    "secret": "your_webhook_secret"
  }'
```

---

### Webhook Payload Format

Every webhook POST to your URL follows this structure:

```json
{
  "event": "claim.approved",
  "timestamp": "2024-06-01T10:00:00Z",
  "data": {
    "claim_id": 56,
    "member_id": 1,
    "hospital_id": 3,
    "claim_amount": 5000,
    "approved_at": "2024-06-01T10:00:00Z"
  },
  "signature": "sha256=abc123..."
}
```

---

### Verifying Webhook Signatures

Always verify the `signature` header to confirm the request came from SHA:

```python
import hmac, hashlib

def verify_webhook(payload_body, signature, secret):
    expected = hmac.new(
        secret.encode(),
        payload_body,
        hashlib.sha256
    ).hexdigest()
    return hmac.compare_digest(f"sha256={expected}", signature)

# In your Flask/Django view:
signature = request.headers.get("X-SHA-Signature")
if not verify_webhook(request.data, signature, "your_webhook_secret"):
    return "Invalid signature", 401
```

---

### Webhook Retry Policy

| Attempt | Delay |
|---------|-------|
| 1st retry | 1 minute |
| 2nd retry | 5 minutes |
| 3rd retry | 30 minutes |
| 4th retry | 2 hours |
| Final | Marked as failed after 4 retries |

Your endpoint must return `HTTP 200` to acknowledge receipt. Any other status triggers a retry.

---

### M-Pesa Callback (Built-in Webhook)

M-Pesa posts payment confirmations to:
```
POST /api/payments/callback
```

No authentication required on this endpoint — M-Pesa calls it directly. The system validates the payload internally.

---

## API Keys

API keys are for **server-to-server** integrations where JWT login is not practical (e.g. hospital billing systems, third-party apps).

### Generating an API Key

```bash
cd sha-system
python api_keys/key_manager.py create \
  --user-id 1 \
  --key-name "KNH Hospital Integration" \
  --created-by 1
```

Output:
```
API Key created successfully:
  Key ID: 3
  Key Name: KNH Hospital Integration
  API Key: sha_live_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
  
IMPORTANT: Save this key now. It cannot be retrieved again.
```

---

### Using an API Key

Include the key in the `X-API-Key` header:

```bash
curl http://localhost:5000/api/members \
  -H "X-API-Key: sha_live_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
```

---

### Listing API Keys

```bash
python api_keys/key_manager.py list
```

---

### Revoking a Compromised Key

```bash
python api_keys/key_manager.py revoke --key-id 3 --created-by 1
```

Revocation is immediate — the key stops working instantly.

---

### API Key Security Rules

- Keys are stored **hashed** — not even admins can retrieve the original after creation
- Save the key immediately after generation — it is shown only once
- Use separate keys per integration — makes revocation targeted
- Keys can be scoped to specific IP ranges (enterprise feature)
- Rotate keys every 90 days as a security best practice

---

### Integration Examples

#### Python with API Key
```python
import requests

headers = {"X-API-Key": "sha_live_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"}
response = requests.get("http://localhost:5000/api/members", headers=headers)
print(response.json())
```

#### Node.js with API Key
```javascript
const axios = require('axios');
const response = await axios.get('http://localhost:5000/api/members', {
  headers: { 'X-API-Key': 'sha_live_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx' }
});
```

#### cURL with API Key
```bash
curl http://localhost:5000/api/claims \
  -H "X-API-Key: sha_live_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
```
