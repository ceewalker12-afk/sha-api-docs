# Quick Start Guide

Get the SHA system running in 5 minutes.

---

## Prerequisites

- Python 3.8+
- pip
- Git

---

## 1. Clone & Install

```bash
git clone https://github.com/ceewalker12-afk/sha-api-docs.git
cd sha-system

python -m venv venv

# Windows
venv\Scripts\activate

# Linux/Mac
source venv/bin/activate

pip install -r requirements.txt
```

---

## 2. Configure Environment

```bash
cp .env.example .env
```

Edit `.env` with your values:

```env
FLASK_SECRET_KEY=change-this-to-a-random-string
JWT_SECRET=another-random-string
FLASK_HOST=0.0.0.0
FLASK_PORT=5000
FLASK_DEBUG=False
```

---

## 3. Initialize Database

```bash
python setup_system.py
```

This creates all tables and a default admin user. The admin password is printed to the console — **save it immediately**.

---

## 4. Start the Server

```bash
# Windows
start.bat

# Or manually
python main_app.py
```

Server starts at `http://localhost:5000`

Verify it's running:
```bash
curl http://localhost:5000/health
```

Expected response:
```json
{
  "status": "healthy",
  "service": "SHA Verification System",
  "version": "1.0.0"
}
```

---

## 5. Get Your First Token

```bash
curl -X POST http://localhost:5000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"username": "admin", "password": "YOUR_ADMIN_PASSWORD"}'
```

Copy the `token` from the response.

---

## 6. Make Your First API Call

```bash
curl http://localhost:5000/api/members \
  -H "Authorization: Bearer YOUR_TOKEN_HERE"
```

---

## Code Examples

### Python

```python
import requests

BASE_URL = "http://localhost:5000"

# Login
response = requests.post(f"{BASE_URL}/api/auth/login", json={
    "username": "admin",
    "password": "your_password"
})
token = response.json()["token"]

headers = {"Authorization": f"Bearer {token}"}

# Register a member
member = requests.post(f"{BASE_URL}/api/members", headers=headers, json={
    "full_name": "Jane Wanjiku",
    "national_id": "12345678",
    "phone": "0712345678",
    "county": "Nairobi"
})
print(member.json())

# Check eligibility
eligibility = requests.get(
    f"{BASE_URL}/api/members/{member.json()['member_id']}/eligibility",
    headers=headers
)
print(eligibility.json())
```

### JavaScript / Node.js

```javascript
const axios = require('axios');

const BASE_URL = 'http://localhost:5000';

async function main() {
  // Login
  const { data: { token } } = await axios.post(`${BASE_URL}/api/auth/login`, {
    username: 'admin',
    password: 'your_password'
  });

  const headers = { Authorization: `Bearer ${token}` };

  // Submit a claim
  const claim = await axios.post(`${BASE_URL}/api/claims`, {
    member_id: 1,
    hospital_id: 1,
    treatment_type: 'Outpatient',
    claim_amount: 5000,
    description: 'Malaria treatment'
  }, { headers });

  console.log(claim.data);
}

main();
```

### PHP

```php
<?php
$base = 'http://localhost:5000';

// Login
$ch = curl_init("$base/api/auth/login");
curl_setopt($ch, CURLOPT_POST, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode([
    'username' => 'admin',
    'password' => 'your_password'
]));
curl_setopt($ch, CURLOPT_HTTPHEADER, ['Content-Type: application/json']);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
$response = json_decode(curl_exec($ch));
$token = $response->token;

// Get members
$ch = curl_init("$base/api/members");
curl_setopt($ch, CURLOPT_HTTPHEADER, ["Authorization: Bearer $token"]);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
$members = json_decode(curl_exec($ch));
print_r($members);
```

### cURL (Command Line)

```bash
# Login
TOKEN=$(curl -s -X POST http://localhost:5000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"username":"admin","password":"your_password"}' \
  | python -c "import sys,json; print(json.load(sys.stdin)['token'])")

# List members
curl http://localhost:5000/api/members \
  -H "Authorization: Bearer $TOKEN"

# Submit claim
curl -X POST http://localhost:5000/api/claims \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"member_id":1,"hospital_id":1,"claim_amount":5000,"treatment_type":"Outpatient"}'

# Check fraud stats
curl http://localhost:5000/api/fraud/stats \
  -H "Authorization: Bearer $TOKEN"
```

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| `401 Token is missing` | Add `Authorization: Bearer <token>` header |
| `401 Token has expired` | Call `POST /api/auth/refresh` to get a new token |
| `500 Internal server error` | Check `logs/sha_system.log` for details |
| `Connection refused` | Ensure server is running on correct port |
| Database errors | Run `python check_db.py` to diagnose |
| USSD not responding | Verify Africa's Talking webhook URL is set to `/ussd/callback` |

---

## Next Steps

- [Full API Reference](../endpoints/auth.md)
- [CHP Workflow](../workflows/chp-workflow.md)
- [Claims Processing Workflow](../workflows/claims-workflow.md)
- [Security Documentation](../security/security.md)
- [Deployment Options](../technical/specifications.md#deployment-options)
