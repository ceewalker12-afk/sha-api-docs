# SDK Documentation

Official and community SDKs for integrating with the SHA Verification System API.

---

## Official SDKs

### Python SDK (Coming Q3 2025)

```bash
pip install sha-api-sdk
```

```python
from sha_sdk import SHAClient

client = SHAClient(
    base_url="https://your-domain.com",
    username="your_username",
    password="your_password"
)

# Auto-handles login and token refresh
members = client.members.list(status="active")
claim = client.claims.submit(member_id=1, hospital_id=3, amount=5000)
eligibility = client.members.check_eligibility(member_id=1)
```

### JavaScript / Node.js SDK (Coming Q3 2025)

```bash
npm install sha-api-sdk
```

```javascript
const { SHAClient } = require('sha-api-sdk');

const client = new SHAClient({
  baseUrl: 'https://your-domain.com',
  username: 'your_username',
  password: 'your_password'
});

const members = await client.members.list({ status: 'active' });
const claim = await client.claims.submit({ memberId: 1, hospitalId: 3, amount: 5000 });
```

---

## Manual Integration (Available Now)

Until official SDKs are released, use the REST API directly. Below are ready-to-use helper classes.

### Python Helper Class

```python
import requests

class SHAClient:
    def __init__(self, base_url, username, password):
        self.base_url = base_url
        self.token = self._login(username, password)
        self.headers = {"Authorization": f"Bearer {self.token}"}

    def _login(self, username, password):
        r = requests.post(f"{self.base_url}/api/auth/login",
                          json={"username": username, "password": password})
        return r.json()["token"]

    def get_members(self, **params):
        return requests.get(f"{self.base_url}/api/members",
                            headers=self.headers, params=params).json()

    def register_member(self, data):
        return requests.post(f"{self.base_url}/api/members",
                             headers=self.headers, json=data).json()

    def check_eligibility(self, member_id):
        return requests.get(f"{self.base_url}/api/members/{member_id}/eligibility",
                            headers=self.headers).json()

    def submit_claim(self, data):
        return requests.post(f"{self.base_url}/api/claims",
                             headers=self.headers, json=data).json()

    def get_fraud_stats(self):
        return requests.get(f"{self.base_url}/api/fraud/stats",
                            headers=self.headers).json()

# Usage
client = SHAClient("http://localhost:5000", "admin", "your_password")
print(client.get_members(status="active"))
print(client.check_eligibility(1))
```

### JavaScript Helper Class

```javascript
const axios = require('axios');

class SHAClient {
  constructor(baseUrl) {
    this.baseUrl = baseUrl;
    this.token = null;
  }

  async login(username, password) {
    const { data } = await axios.post(`${this.baseUrl}/api/auth/login`,
      { username, password });
    this.token = data.token;
    this.headers = { Authorization: `Bearer ${this.token}` };
    return this;
  }

  async getMembers(params = {}) {
    const { data } = await axios.get(`${this.baseUrl}/api/members`,
      { headers: this.headers, params });
    return data;
  }

  async submitClaim(payload) {
    const { data } = await axios.post(`${this.baseUrl}/api/claims`,
      payload, { headers: this.headers });
    return data;
  }

  async checkEligibility(memberId) {
    const { data } = await axios.get(
      `${this.baseUrl}/api/members/${memberId}/eligibility`,
      { headers: this.headers });
    return data;
  }
}

// Usage
const client = new SHAClient('http://localhost:5000');
await client.login('admin', 'your_password');
console.log(await client.getMembers({ status: 'active' }));
```

### PHP Helper Class

```php
<?php
class SHAClient {
    private $baseUrl;
    private $token;

    public function __construct($baseUrl, $username, $password) {
        $this->baseUrl = $baseUrl;
        $this->token = $this->login($username, $password);
    }

    private function login($username, $password) {
        $response = $this->request('POST', '/api/auth/login', [
            'username' => $username,
            'password' => $password
        ], false);
        return $response['token'];
    }

    private function request($method, $path, $data = null, $auth = true) {
        $ch = curl_init($this->baseUrl . $path);
        $headers = ['Content-Type: application/json'];
        if ($auth) $headers[] = "Authorization: Bearer {$this->token}";
        curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
        if ($method === 'POST') {
            curl_setopt($ch, CURLOPT_POST, true);
            curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($data));
        }
        return json_decode(curl_exec($ch), true);
    }

    public function getMembers($params = []) {
        $query = http_build_query($params);
        return $this->request('GET', "/api/members?$query");
    }

    public function checkEligibility($memberId) {
        return $this->request('GET', "/api/members/$memberId/eligibility");
    }

    public function submitClaim($data) {
        return $this->request('POST', '/api/claims', $data);
    }
}

// Usage
$client = new SHAClient('http://localhost:5000', 'admin', 'your_password');
print_r($client->getMembers(['status' => 'active']));
```

---

## SDK Roadmap

| SDK | Status | Release |
|-----|--------|---------|
| Python | In development | Q3 2025 |
| JavaScript/Node.js | In development | Q3 2025 |
| PHP | Planned | Q4 2025 |
| Java | Planned | Q1 2026 |
| C# / .NET | Planned | Q1 2026 |

To request an SDK for a specific language, contact: ceewalker12@gmail.com
