# Changelog & Version History

All notable changes to the SHA Verification System API are documented here.

---

## [1.0.0] — 2024-06-01 (Current)

### Added
- JWT-based authentication (`/api/auth`)
- Member registration and eligibility API (`/api/members`)
- Claims submission and processing API (`/api/claims`)
- Hospital management and verification API (`/api/hospitals`)
- Fraud detection and risk scoring API (`/api/fraud`)
- AI/ML fraud detection engine with SHAP/LIME explainability
- USSD gateway integration via Africa's Talking (`/ussd/callback`)
- SMS and email notification API (`/api/notifications`)
- M-Pesa payment processing API (`/api/payments`)
- Audit trail API with export (`/api/audit`)
- User management with RBAC (`/api/users`)
- Document verification API (`/api/documents`)
- System status and health check endpoints (`/api/system`)
- Duplicate claim detection on submission
- Rotating file logs (10MB, 5 backups)
- Docker and docker-compose support
- Next.js web portal
- Tkinter desktop application

### Security
- PBKDF2-HMAC-SHA256 password hashing with unique salt
- Fernet encryption for sensitive data fields
- API key management with separate key store database
- Rate limiting via Flask-Limiter

---

## Versioning Policy

This API follows **Semantic Versioning (SemVer)**:

```
MAJOR.MINOR.PATCH

MAJOR — Breaking changes (endpoint removed, response structure changed)
MINOR — New endpoints or features (backward compatible)
PATCH — Bug fixes, security patches (backward compatible)
```

### Breaking Change Policy
- Breaking changes announced **30 days** in advance
- Old version supported for **90 days** after new version release
- Migration guide published with every major version

### Version Header
Specify API version in requests (optional, defaults to latest):
```http
X-API-Version: 1.0
```

---

## Upcoming — [1.1.0] (Q3 2025)

- `GET /api/members/bulk-import` — CSV bulk member import
- `GET /api/claims/export` — Claims export to PDF/CSV
- `POST /api/auth/mfa/enable` — MFA enrollment endpoint
- OpenAPI Swagger UI at `/api/docs`
- Python SDK (pip installable)

## Upcoming — [2.0.0] (Q1 2026)

- HL7 FHIR compliance endpoints
- Biometric verification integration
- GraphQL API option
- Webhook subscription management
