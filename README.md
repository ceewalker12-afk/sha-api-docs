# SHA System API Documentation

> **Social Health Authority (SHA) Kenya — REST API Reference**

This documentation covers all available REST API endpoints for the SHA System — a health insurance management platform built for Kenya's Social Health Authority. The system manages member registration, claims processing, hospital verification, fraud detection, and community health promoter (CHP) workflows.

---

## Base URL

```
http://localhost:5000
```

> For production deployments, replace with your hosted domain.

---

## Authentication

All endpoints (except `/api/auth/login` and `/api/auth/register`) require a **Bearer JWT token** in the request header.

```http
Authorization: Bearer <your_token_here>
```

Tokens are obtained via the [Login endpoint](endpoints/auth.md#login) and expire after **24 hours**.

---

## API Modules

| Module | Base Path | Description |
|--------|-----------|-------------|
| [Authentication](endpoints/auth.md) | `/api/auth` | Login, register, token management |
| [Members](endpoints/members.md) | `/api/members` | SHA member registration & management |
| [Claims](endpoints/claims.md) | `/api/claims` | Health claims submission & processing |
| [Hospitals](endpoints/hospitals.md) | `/api/hospitals` | Hospital registration & verification |
| [Fraud Detection](endpoints/fraud.md) | `/api/fraud` | Risk assessment & fraud alerts |

---

## Workflows

| Workflow | Description |
|----------|-------------|
| [CHP Registration Workflow](workflows/chp-workflow.md) | End-to-end community health promoter process |
| [Claims Processing Workflow](workflows/claims-workflow.md) | Full claim lifecycle from submission to payment |
| [Fraud Detection Workflow](workflows/fraud-workflow.md) | How risk scoring and alerts work |

---

## Response Format

All responses return JSON. Successful responses include the requested data. Errors follow this format:

```json
{
  "error": "Description of what went wrong"
}
```

### HTTP Status Codes

| Code | Meaning |
|------|---------|
| `200` | Success |
| `201` | Created successfully |
| `400` | Bad request / missing required fields |
| `401` | Unauthorized / invalid or expired token |
| `404` | Resource not found |
| `500` | Internal server error |

---

## Pagination

Endpoints that return lists support pagination via query parameters:

| Parameter | Default | Description |
|-----------|---------|-------------|
| `page` | `1` | Page number |
| `per_page` | `20` | Results per page |

**Example:**
```
GET /api/members?page=2&per_page=10
```

**Paginated Response:**
```json
{
  "members": [...],
  "total": 150,
  "page": 2,
  "per_page": 10
}
```

---

## Tech Stack

- **Backend:** Python / Flask
- **Auth:** JWT (JSON Web Tokens)
- **Database:** SQLite (development) / PostgreSQL (production)
- **Frontend:** Tkinter (desktop) + Next.js (web portal)

---

## Quick Start

1. Start the backend server:
   ```bash
   python main_app.py
   ```

2. Login to get a token:
   ```bash
   curl -X POST http://localhost:5000/api/auth/login \
     -H "Content-Type: application/json" \
     -d '{"username": "admin", "password": "your_password"}'
   ```

3. Use the token in subsequent requests:
   ```bash
   curl http://localhost:5000/api/members \
     -H "Authorization: Bearer <token>"
   ```

---

## License

See [LICENSE](../sha-system/LICENSE) in the main repository.
