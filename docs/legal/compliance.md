# Compliance & Audit Documentation

---

## Regulatory Compliance

### Kenya Data Protection Act 2019

The SHA system is designed in alignment with the Kenya Data Protection Act (DPA) 2019:

| Requirement | Implementation |
|-------------|---------------|
| Lawful basis for processing | Member consent captured at registration |
| Data minimization | Only fields necessary for health insurance collected |
| Purpose limitation | Data used only for SHA benefit administration |
| Accuracy | Update endpoints available for all member data |
| Storage limitation | Configurable retention policies per data type |
| Security | Encryption at rest and in transit |
| Data subject rights | Access, correction, deletion endpoints available |
| Data breach notification | Audit trail enables rapid breach detection and reporting |

**Data Controller Responsibility:** The deploying organization (e.g. SHA Kenya, County Government) acts as data controller and must register with the Office of the Data Protection Commissioner (ODPC).

---

### HIPAA Alignment

| HIPAA Safeguard | SHA Implementation |
|----------------|-------------------|
| Access controls | JWT + RBAC — role-scoped data access |
| Audit controls | Full immutable audit trail on every action |
| Integrity controls | Fernet encryption prevents unauthorized modification |
| Transmission security | HTTPS/TLS 1.2+ in production |
| Workforce training | Training materials and certification program available |
| Minimum necessary | Each role sees only the data required for their function |

---

### GDPR Principles Alignment

| Principle | Implementation |
|-----------|---------------|
| Lawfulness, fairness, transparency | Consent at registration, privacy policy published |
| Purpose limitation | Data used only for stated health insurance purposes |
| Data minimization | Minimal fields collected per endpoint |
| Accuracy | Member update API available |
| Storage limitation | Data retention policy enforced (see Legal docs) |
| Integrity and confidentiality | Encryption + access control |
| Accountability | Audit trail + DPA alignment documentation |

---

## Audit Reports

### Internal Security Audit Checklist

The following checks should be performed before production deployment:

**Authentication & Authorization**
- [ ] Default admin password changed after first login
- [ ] JWT secret is a random 64+ character string (not default)
- [ ] All API endpoints require authentication (except `/health`, `/api/auth/login`)
- [ ] RBAC roles correctly restrict access per endpoint
- [ ] API keys stored hashed in separate database

**Data Security**
- [ ] HTTPS/TLS configured on production server
- [ ] Sensitive fields encrypted with Fernet
- [ ] Database backups encrypted before storage
- [ ] `.env` file not committed to version control
- [ ] Production `.env` has `FLASK_DEBUG=False`

**Network Security**
- [ ] Rate limiting enabled (`RATE_LIMIT` configured)
- [ ] CORS restricted to known origins in production
- [ ] Firewall rules limit database access to API server only
- [ ] SSH key-based access only (no password SSH)

**Monitoring**
- [ ] Log rotation configured (10MB, 5 backups)
- [ ] Health check endpoint monitored externally
- [ ] Alert configured for failed login spikes
- [ ] Backup schedule verified and tested

---

### Third-Party Audit

For enterprise and government deployments, a third-party security audit is recommended before go-live. The following firms have experience with Kenyan government health IT systems:

- Engage with a CISA-certified penetration testing firm
- Request a OWASP Top 10 assessment
- Conduct a data flow audit to verify DPA compliance

Contact ceewalker12@gmail.com to receive the system architecture documentation package required for third-party audits.

---

## Certification Documents

### ISO 27001 Alignment

The SHA system architecture aligns with ISO 27001 information security management principles:

| ISO 27001 Control | SHA Implementation |
|-------------------|-------------------|
| A.9 Access control | RBAC + JWT + API keys |
| A.10 Cryptography | Fernet encryption, PBKDF2 hashing |
| A.12 Operations security | Log rotation, backup procedures |
| A.13 Communications security | HTTPS/TLS, CORS controls |
| A.14 System acquisition | Secure development practices |
| A.16 Incident management | Fraud alert system, audit trail |
| A.17 Business continuity | Disaster recovery procedures |
| A.18 Compliance | DPA, HIPAA, GDPR alignment |

**Formal ISO 27001 certification** is on the roadmap for Q2 2026.

---

## Data Retention Policies

| Data Type | Retention Period | Basis |
|-----------|-----------------|-------|
| Member records | Duration of enrollment + 7 years | Kenya health records law |
| Claims records | 7 years from claim date | Insurance regulatory requirement |
| Audit logs | 7 years | Kenya DPA + health regulations |
| Security logs | 2 years | Security best practice |
| System logs | 90 days | Operational requirement |
| Deleted user accounts | 30 days then purged | DPA right to erasure |
| Payment records | 7 years | Financial regulations |
| Backup files | 90 days rolling | Operational requirement |

---

## Data Deletion Procedures

### Member Data Deletion Request

When a data subject requests erasure under the Kenya DPA:

```bash
# 1. Verify identity of requestor
# 2. Check if deletion is legally permissible (active claims block deletion)
# 3. Soft-delete the member record
curl -X DELETE http://localhost:5000/api/members/105 \
  -H "Authorization: Bearer $ADMIN_TOKEN"

# 4. Log the deletion request in audit trail
curl -X POST http://localhost:5000/api/audit/log \
  -H "Authorization: Bearer $ADMIN_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "user_identity": "admin",
    "action": "DATA_DELETION_REQUEST",
    "doc_id": 105
  }'

# 5. Confirm deletion to requestor within 30 days (DPA requirement)
```

### Bulk Log Purge

```bash
# Purge logs older than retention period
curl -X POST http://localhost:5000/api/audit/purge \
  -H "Authorization: Bearer $ADMIN_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"retention_days": 2555}'
```
