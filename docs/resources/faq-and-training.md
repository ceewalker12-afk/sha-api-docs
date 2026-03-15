# Knowledge Base & FAQ

---

## General Questions

**Q: What is the SHA Verification System?**
A: It is a national health insurance management platform built for Kenya's Social Health Authority. It handles member registration, claims processing, fraud detection, hospital management, and community health promoter workflows.

**Q: What programming languages can I use to integrate?**
A: Any language that can make HTTP requests. We have code examples for Python, JavaScript, PHP, and cURL. Official SDKs for Python and JavaScript are coming in Q3 2025.

**Q: Is there a sandbox environment for testing?**
A: Yes. Contact ceewalker12@gmail.com for sandbox credentials. The sandbox mirrors production but uses test data only.

**Q: How do I get API access?**
A: Contact ceewalker12@gmail.com with your organization name, use case, and required APIs. You will receive API keys and documentation within 2 business days.

---

## Authentication

**Q: How long do JWT tokens last?**
A: 24 hours. Use `POST /api/auth/refresh` before expiry to get a new token without re-logging in.

**Q: What's the difference between JWT tokens and API keys?**
A: JWT tokens are for user-facing applications (web, mobile, desktop). API keys are for server-to-server integrations where a human login is not practical.

**Q: What happens if my token expires mid-session?**
A: You will receive a `401 Token has expired` response. Call `POST /api/auth/refresh` with your current token to get a new one, then retry the original request.

---

## Members & Claims

**Q: Can the same person be registered twice?**
A: The system checks national ID for duplicates. Attempting to register the same national ID twice will return a conflict error.

**Q: What does a risk score of 0 mean on a claim?**
A: It means the claim passed all fraud checks with no anomalies detected. Scores of 0–20 are considered low risk.

**Q: What triggers a duplicate claim flag?**
A: Same member ID + same hospital ID + same treatment type submitted on the same calendar day.

**Q: Can a rejected claim be resubmitted?**
A: Yes. Correct the issue identified in the rejection reason and submit a new claim. The old rejected claim remains in the system for audit purposes.

---

## Fraud Detection

**Q: How is the risk score calculated?**
A: The system uses a 10-point fraud detection engine combining ML models, rule-based checks, network analysis, and historical patterns. SHAP/LIME explainability shows which factors contributed most.

**Q: Does a high risk score automatically reject a claim?**
A: No. A high score creates a fraud alert and flags the claim for manual review. A human officer makes the final decision.

**Q: How quickly are fraud alerts generated?**
A: Instantly — alerts are created at the moment of claim submission if the risk score exceeds 50.

---

## Technical

**Q: What database does the system use?**
A: SQLite for development (zero config), PostgreSQL for production.

**Q: Can the system work offline?**
A: The desktop (Tkinter) application supports offline data collection with sync when connectivity is restored. The web portal and API require connectivity.

**Q: What is the maximum file upload size?**
A: 16MB by default. Configurable via `MAX_CONTENT_LENGTH` in `.env`.

**Q: Does the system support multi-tenancy?**
A: Yes, via role-based access control and hospital/area assignment per user. Full multi-tenant isolation is available in the enterprise tier.

---

## Training Materials

### Video Tutorials (Coming Q3 2025)
- Getting Started with the SHA API (15 min)
- CHP Registration Workflow (10 min)
- Claims Processing End-to-End (20 min)
- Fraud Investigation Walkthrough (15 min)
- Setting Up Webhooks (10 min)

### Written Guides Available Now
| Guide | Location |
|-------|----------|
| Quick Start (5 minutes) | `docs/guides/quickstart.md` |
| Step-by-Step Tutorials | `docs/guides/tutorials.md` |
| Best Practices | `docs/guides/best-practices-and-migration.md` |
| CHP Workflow | `workflows/chp-workflow.md` |
| Claims Workflow | `workflows/claims-workflow.md` |
| Fraud Workflow | `workflows/fraud-workflow.md` |
| Security Guide | `docs/security/security.md` |
| API Reference | `endpoints/` |

### Training Workshops
Available for enterprise clients and government deployments. Contact ceewalker12@gmail.com to schedule:
- Half-day: API integration for developers
- Full-day: System administration and operations
- 2-day: Complete staff training (CHPs, claims officers, admins)
