# Legal Documentation

---

## Terms of Service

**SHA Verification System — Terms of Use**
Developed by Christopher Mugambi | University of Nairobi

### 1. Ownership
This software was developed by Christopher Mugambi. All intellectual property rights are retained by the developer unless explicitly transferred via a written agreement.

### 2. Permitted Use
- Authorized government agencies may deploy this system under a formal licensing agreement
- Private organizations may use this system under a commercial license
- Developers may evaluate the system in a non-production sandbox environment

### 3. Prohibited Use
- Unauthorized reproduction or redistribution of source code
- Reverse engineering for competitive purposes
- Use for any purpose that violates Kenyan law or international health data regulations
- Processing data of individuals without their consent

### 4. Liability
The developer provides this system "as-is" for evaluation purposes. Production deployments require a formal support agreement. The developer is not liable for data loss, system downtime, or financial losses arising from system use without a support agreement.

---

## Privacy Policy

### Data We Collect
The SHA system collects and processes:
- Member demographic data (name, national ID, date of birth, location)
- Health claims data (treatment type, amounts, dates)
- User account data (username, hashed password, role)
- System usage logs (actions, timestamps, IP addresses)

### How Data Is Used
- Member data: To verify eligibility and process claims
- Claims data: To adjudicate health insurance claims and detect fraud
- Audit logs: To maintain accountability and support investigations

### Data Sharing
- Data is not sold to third parties
- Data may be shared with authorized government agencies under legal obligation
- Anonymized aggregate data may be used for public health reporting

### Data Retention
| Data Type | Retention Period |
|-----------|-----------------|
| Member records | Duration of enrollment + 7 years |
| Claims records | 7 years (Kenya health records law) |
| Audit logs | 7 years |
| System logs | 90 days |
| Deleted accounts | 30 days then purged |

### Your Rights (Kenya Data Protection Act 2019)
- Right to access your personal data
- Right to correct inaccurate data
- Right to erasure (where legally permissible)
- Right to data portability
- Right to object to processing

Contact: ceewalker12@gmail.com for data requests.

---

## License Agreement

```
SHA Verification System
Copyright (c) 2024-2025 Christopher Mugambi

COMMERCIAL LICENSE

This software and its documentation are proprietary and confidential.
Unauthorized copying, modification, distribution, or use of this software,
via any medium, is strictly prohibited without a written license agreement.

For licensing inquiries:
  Email: ceewalker12@gmail.com
  Institution: University of Nairobi

EVALUATION LICENSE
A limited evaluation license is granted to authorized parties for
non-production assessment purposes only, for a period not exceeding 30 days.
```

---

## Data Processing Agreement (DPA)

For organizations deploying this system to process personal health data:

### Controller vs Processor
- **Data Controller:** The deploying organization (e.g., SHA Kenya, County Government)
- **Data Processor:** Christopher Mugambi / development team (during implementation)

### Processing Obligations
The processor agrees to:
- Process data only on documented instructions from the controller
- Ensure confidentiality of authorized personnel
- Implement appropriate technical and organizational security measures
- Assist the controller in fulfilling data subject rights
- Delete or return all personal data upon contract termination
- Provide all information necessary to demonstrate compliance

### Sub-processors
Any sub-processors (e.g., cloud hosting providers) must be approved by the controller and bound by equivalent data protection obligations.

---

## Service Level Agreement (SLA)

### Standard SLA
| Metric | Commitment |
|--------|-----------|
| API Uptime | 99.5% monthly |
| Response Time | < 500ms (95th percentile) |
| Support Response | 48 business hours |
| Security Patches | Within 72 hours |
| Backup Frequency | Daily |

### Enterprise SLA
| Metric | Commitment |
|--------|-----------|
| API Uptime | 99.95% monthly |
| Response Time | < 200ms (95th percentile) |
| Support Response | 2 hours (24/7) |
| Security Patches | Within 24 hours |
| Backup Frequency | Every 6 hours |
| Dedicated Support | Named account manager |

### SLA Credits
If uptime falls below the committed level:
| Uptime Achieved | Credit |
|----------------|--------|
| 99.0% – 99.5% | 10% of monthly fee |
| 95.0% – 99.0% | 25% of monthly fee |
| < 95.0% | 50% of monthly fee |
