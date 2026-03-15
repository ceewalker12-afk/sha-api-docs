# Technical Specifications

---

## Performance Metrics

| Metric | Target | Notes |
|--------|--------|-------|
| API Response Time | < 200ms | For standard CRUD endpoints |
| Authentication | < 100ms | JWT validation is stateless |
| Fraud Scoring | < 500ms | ML model inference |
| Database Queries | < 50ms | With proper indexing |
| USSD Response | < 2s | Africa's Talking requirement |
| Concurrent Users | 500+ | With Waitress/Gunicorn |
| Log Rotation | 10MB per file | 5 backup files kept |
| Token Expiry | 24 hours | Configurable via env |

### Scalability Targets
- Horizontal scaling via load balancer (Nginx)
- Stateless API — any instance handles any request
- Database connection pooling built-in
- Background jobs via APScheduler (non-blocking)

---

## Security Features

### Authentication
- **JWT (JSON Web Tokens)** — HS256 algorithm, 24-hour expiry
- **API Key Authentication** — for machine-to-machine integrations
- **Password Hashing** — PBKDF2 with unique salt per user
- **bcrypt** — additional password security layer

### Encryption
- **Fernet Symmetric Encryption** — for sensitive data fields at rest
- **HTTPS/TLS** — all traffic encrypted in transit (production)
- **Encrypted Backups** — database backups stored with AES encryption

### Access Control
- **RBAC (Role-Based Access Control)** — 6 distinct roles
- **Middleware enforcement** — every route checks permissions
- **API key scoping** — keys can be limited to specific endpoints

### Roles & Permissions

| Role | Access Level |
|------|-------------|
| `admin` | Full system access |
| `claims_officer` | Claims processing, fraud review |
| `doctor` | Patient records, treatment verification |
| `hospital_admin` | Hospital claims, facility management |
| `chp` | Member registration, household visits |
| `auditor` | Read-only audit logs and reports |

### Audit & Monitoring
- Every API action logged with user ID, timestamp, IP
- Loki integration for centralized log aggregation
- Rotating file logs (10MB, 5 backups)
- Security event logs separate from application logs

---

## Compliance Standards

| Standard | Status | Notes |
|----------|--------|-------|
| Kenya Data Protection Act 2019 | Aligned | Data minimization, consent, retention policies |
| HIPAA (Health Data) | Aligned | Encryption at rest and in transit, audit trails |
| GDPR Principles | Aligned | Right to erasure, data portability supported |
| ISO 27001 | Architecture aligned | Formal certification recommended for enterprise |
| Kenya Health Informatics Standards | Aligned | KMHFL facility codes used |

---

## Integration Capabilities

### REST API
- Full JSON REST API on port 5000
- CORS enabled for web clients
- Rate limiting via Flask-Limiter
- Versioned endpoints

### USSD
- Africa's Talking USSD gateway
- Callback endpoint: `POST /ussd/callback`
- Session-based menu navigation
- Works on all Kenyan mobile networks

### SMS
- Africa's Talking SMS (primary)
- Twilio (fallback)
- Notification triggers on claim status changes

### Webhooks
- Event-driven notifications to external systems
- Configurable webhook URLs per integration
- Retry logic on failure

### Third-Party APIs
- Payment gateway integration
- KMHFL (Kenya Master Health Facility List)
- Africa's Talking (SMS/USSD)
- Twilio (SMS)
- Loki (logging)

---

## Deployment Options

### Option 1 — On-Premise (Recommended for Government)
```
Server: Windows Server 2019+ or Ubuntu 20.04+
Python: 3.8+
Database: PostgreSQL 14+
Web Server: Nginx + Waitress (Windows) or Gunicorn (Linux)
```

### Option 2 — Cloud (Render / AWS / Azure)
```
Platform: Render, AWS EC2, Azure App Service
Database: Render PostgreSQL, AWS RDS, Azure Database
Storage: S3-compatible for backups
CDN: CloudFront / Azure CDN for web portal
```

### Option 3 — Docker / Containerized
```yaml
# docker-compose.yml included in repository
services:
  backend:   Flask API
  frontend:  Next.js web portal
  db:        PostgreSQL
```

### Option 4 — Hybrid
- Core API on-premise (data sovereignty)
- Web portal on cloud CDN
- Backups to cloud storage
- Monitoring via cloud Loki

---

## Environment Configuration

Key environment variables:

```env
FLASK_SECRET_KEY=<secret>
JWT_SECRET=<jwt_secret>
FLASK_HOST=0.0.0.0
FLASK_PORT=5000
FLASK_DEBUG=False
DATABASE_URL=postgresql://user:pass@host/db
AFRICASTALKING_API_KEY=<key>
AFRICASTALKING_USERNAME=<username>
TWILIO_ACCOUNT_SID=<sid>
TWILIO_AUTH_TOKEN=<token>
```
