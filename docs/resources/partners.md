# Integration Partners & Partner Program

---

## Partner Directory

### Technology Partners

| Partner | Integration Type | Status |
|---------|-----------------|--------|
| Africa's Talking | SMS & USSD gateway | Active |
| Twilio | SMS fallback | Active |
| M-Pesa (Safaricom Daraja) | Payment processing | Active |
| ChromaDB | Vector store for RAG/AI | Active |
| Loki (Grafana) | Centralized logging | Active |

### Healthcare Data Partners
| Partner | Integration Type | Status |
|---------|-----------------|--------|
| KMHFL (Kenya Master Health Facility List) | Facility codes | Aligned |
| DHIS2 | Health data reporting | Planned Q4 2025 |
| OpenMRS | Electronic medical records | Planned Q1 2026 |

### Government Partners
| Partner | Integration Type | Status |
|---------|-----------------|--------|
| IPRS (Integrated Population Registration System) | National ID verification | Planned |
| KRA | Tax compliance for contributions | Planned |
| NHIF (legacy) | Data migration support | Available |

---

## Partner Program

### Why Become a Partner?

- Listed in the official SHA partner directory
- Early access to new API features and beta programs
- Co-marketing opportunities (joint press releases, case studies)
- Technical support priority
- Revenue sharing on referred enterprise clients
- Access to sandbox environment with extended limits

### Partner Tiers

| Tier | Requirements | Benefits |
|------|-------------|----------|
| **Community** | Active integration built | Directory listing, documentation access |
| **Silver** | 1 live customer using integration | Co-marketing, priority support |
| **Gold** | 5+ live customers | Revenue sharing, dedicated account manager |
| **Strategic** | Enterprise-level integration | Joint roadmap input, co-development opportunities |

---

## Integration Guides

### Integrating with Africa's Talking (SMS/USSD)

1. Create account at [africastalking.com](https://africastalking.com)
2. Get API key and username
3. Set in `.env`:
```env
AFRICASTALKING_API_KEY=your_key
AFRICASTALKING_USERNAME=your_username
```
4. Set USSD callback URL in Africa's Talking dashboard:
```
https://your-domain.com/ussd/callback
```
5. Test with `SMS_MOCK=false` in `.env`

---

### Integrating with M-Pesa (Daraja API)

1. Register at [developer.safaricom.co.ke](https://developer.safaricom.co.ke)
2. Create a Lipa Na M-Pesa Online app
3. Set in `.env`:
```env
MPESA_CONSUMER_KEY=your_key
MPESA_CONSUMER_SECRET=your_secret
MPESA_SHORTCODE=your_shortcode
MPESA_PASSKEY=your_passkey
MPESA_CALLBACK_URL=https://your-domain.com/api/payments/callback
```
4. Whitelist your callback URL in Daraja portal
5. Test with `MPESA_MOCK=false`

---

### Integrating with DHIS2 (Planned)

The SHA system will support DHIS2 data push for national health reporting:
```
POST /api/integrations/dhis2/sync
```
Contact ceewalker12@gmail.com to join the DHIS2 integration beta.

---

## Certification Programs

### SHA Certified Developer

Demonstrates proficiency in integrating with the SHA API.

**Requirements:**
- Complete the Quick Start tutorial
- Build a working integration (any language)
- Pass the online assessment (30 questions, 70% pass mark)
- Submit a code sample for review

**Benefits:**
- SHA Certified Developer badge
- Listed in partner directory
- Priority support access

**Apply:** ceewalker12@gmail.com with subject "Developer Certification"

---

### SHA Certified Implementation Partner

For organizations that implement the SHA system for clients.

**Requirements:**
- Minimum 2 certified developers on staff
- 1 completed live implementation
- Sign the partner agreement
- Attend a 1-day partner training

**Benefits:**
- All Silver partner benefits
- Listed as implementation partner
- Access to implementation toolkit and templates
- Lead referrals from SHA team

---

## Co-Marketing Opportunities

Partners at Silver tier and above can participate in:

- **Joint case studies** — document your implementation success
- **Webinars** — co-present technical or business content
- **Conference presence** — joint booth at health IT events in Kenya/East Africa
- **Press releases** — announce integrations and go-lives together
- **Social media** — cross-promotion on LinkedIn and Twitter

To discuss co-marketing, contact: ceewalker12@gmail.com

---

## API Marketplace

All 15 SHA APIs are documented and available for integration:

| API | Documentation |
|-----|--------------|
| Document Verification | `marketplace/apis/01-document-verification.md` |
| Fraud Detection | `marketplace/apis/02-fraud-detection.md` |
| User Management | `marketplace/apis/03-user-management.md` |
| Authentication | `marketplace/apis/04-authentication.md` |
| Notifications | `marketplace/apis/05-notification.md` |
| Payment Processing | `marketplace/apis/06-payment-processing.md` |
| System Status | `marketplace/apis/07-system-status.md` |
| Audit Trail | `marketplace/apis/08-audit-trail.md` |
| Means Testing | `marketplace/apis/09-means-testing.md` |
| Contribution Management | `marketplace/apis/10-contribution-management.md` |
| Fund Management | `marketplace/apis/11-fund-management.md` |
| Referral Management | `marketplace/apis/12-referral-management.md` |
| Claims Management | `marketplace/apis/13-claims-management.md` |
| Household Registration | `marketplace/apis/14-household-registration.md` |
| Provider Management | `marketplace/apis/15-provider-management.md` |
