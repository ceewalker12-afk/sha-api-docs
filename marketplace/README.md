# SHA API Marketplace

> 15 production-ready APIs built on the SHA Verification System — available for integration by governments, healthcare providers, financial institutions, and enterprises.

**Developer:** Christopher Mugambi | University of Nairobi
**Contact:** ceewalker12@gmail.com

---

## Available APIs

| # | API | Base Path | Target |
|---|-----|-----------|--------|
| 1 | [Document Verification API](apis/01-document-verification.md) | `/api/documents` | Government, healthcare, education |
| 2 | [Fraud Detection API](apis/02-fraud-detection.md) | `/api/fraud` | Insurance, banks, healthcare |
| 3 | [User Management API](apis/03-user-management.md) | `/api/users` | SaaS, enterprise applications |
| 4 | [Authentication API](apis/04-authentication.md) | `/api/auth` | Web apps, mobile, enterprise |
| 5 | [Notification API](apis/05-notification.md) | `/api/notifications` | Marketing, customer service, alerts |
| 6 | [Payment Processing API](apis/06-payment-processing.md) | `/api/payments` | E-commerce, subscriptions, fintech |
| 7 | [System Status API](apis/07-system-status.md) | `/api/system` | DevOps, monitoring, IT |
| 8 | [Audit Trail API](apis/08-audit-trail.md) | `/api/audit` | Compliance, security, legal |
| 9 | [Means Testing API](apis/09-means-testing.md) | `/api/means-testing` | Social services, welfare, NGOs |
| 10 | [Contribution Management API](apis/10-contribution-management.md) | `/api/contributions` | Insurance, pensions, memberships |
| 11 | [Fund Management API](apis/11-fund-management.md) | `/api/funds` | Finance, investment, treasury |
| 12 | [Referral Management API](apis/12-referral-management.md) | `/api/referrals` | Healthcare, specialist networks |
| 13 | [Claims Management API](apis/13-claims-management.md) | `/api/claims` | Insurance, healthcare, processors |
| 14 | [Household Registration API](apis/14-household-registration.md) | `/api/households` | Social services, census, community |
| 15 | [Provider Management API](apis/15-provider-management.md) | `/api/providers` | Healthcare networks, vendors |

---

## Implementation Phases

### Phase 1 — Core APIs (Months 1–3)
- Document Verification API
- Fraud Detection API
- Authentication API
- User Management API

### Phase 2 — Communication & Payments (Months 4–6)
- Notification API
- Payment Processing API
- System Status API

### Phase 3 — Specialized APIs (Months 7–12)
- Audit Trail API
- Means Testing API
- Contribution Management API
- Fund Management API
- Referral Management API
- Claims Management API
- Household Registration API
- Provider Management API

---

## Target Markets

| Market | Relevant APIs |
|--------|--------------|
| Government Agencies | Document Verification, Fraud Detection, Audit Trail |
| Healthcare Providers | Claims Management, Referrals, Provider Management |
| Financial Institutions | Fraud Detection, Payments, Contributions, Funds |
| Insurance Companies | Claims Processing, Fraud Detection, Audit Trail |
| SaaS Companies | Authentication, User Management, Notifications |
| Non-Profit Organizations | Means Testing, Household Registration, Contributions |

---

## Authentication

All APIs use JWT Bearer tokens. Get a token first:

```bash
curl -X POST https://your-domain.com/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"username": "your_username", "password": "your_password"}'
```

Then use it on every request:
```http
Authorization: Bearer <your_token>
```

---

## Technical Notes

- Each API is independently deployable via Docker
- All APIs share the same JWT authentication layer
- Rate limiting applied per API key
- Full audit trail on every endpoint
- Contact ceewalker12@gmail.com for API keys and integration support
