# Third-Party Risk Management

**Owner:** CTO
**Version:** 1.1
**Last Reviewed:** 2026-01-29
**Review Cadence:** Quarterly

---

## 1. Purpose

We manage risks from third parties (contractors, vendors, service providers) who have access to our systems, code, or data. This control ensures third-party access is limited, monitored, and reviewed regularly.

**Risk Reduced:**
- Unauthorized access via contractor accounts
- Code quality issues from external contributors
- Data exposure through vendor systems
- Supply chain security risks

**Stakeholders:**
- Engineering Team (works with contractors)
- Customers (data handled by vendors)
- Business (vendor relationships)

---

## 2. Scope

### In Scope
- **Contractors:** External developers with code access
- **Payment Gateways:** Soepay, GP (payment processing partners)
- **SaaS Vendors:** Sentry, GitHub, GCP, Notion, Firebase, etc.
- **Access Types:** Code repository access, SaaS tool access

### Out of Scope
- Internal employees (covered by Access Control)
- One-time consultants with no system access
- Physical vendors (office supplies, etc.)

---

## 3. Roles & Responsibilities

| Role | Team/Individual | Responsibility |
|------|-----------------|----------------|
| Control Owner | CTO | Approves contractor access, sets vendor requirements |
| Contractor Manager | Engineering Lead | Manages day-to-day contractor work, reviews code |
| Access Reviewer | CTO | Conducts quarterly access review |
| Vendor Evaluator | CTO | Assesses new vendors before engagement |

---

## 4. How We Operate This Control

### 4.1 Third-Party Categories

| Category | Examples | Access Level | Our Responsibility |
|----------|----------|--------------|-------------------|
| **Contractors** | External developers | Code only (GitHub) | Full management |
| **Payment Gateways** | Soepay, GP | API integration | Monitor, secure integration |
| **Infrastructure** | GCP | Full platform | Configure securely |
| **SaaS Tools** | Sentry, Notion, GitHub | Tool-specific | Configure securely, manage access |

### 4.2 Contractor Access Management

**Access Granted:**
- GitHub repository access (code only)

**Access NOT Granted:**
- GCP infrastructure access
- Production systems
- Database access
- Admin Portal access

**Contractor Access Diagram:**

```
┌─────────────────────────────────────────────────────────────┐
│                     Contractor                               │
└──────────────────────────┬──────────────────────────────────┘
                           │
                           ▼
                    ┌─────────────┐
                    │   GitHub    │ ✓ Code access only
                    │   (Code)    │
                    └──────┬──────┘
                           │
                           ▼
                    ┌─────────────┐
                    │  Internal   │ All PRs reviewed by
                    │  Code       │ internal team before merge
                    │  Review     │
                    └─────────────┘
                           │
            ┌──────────────┴──────────────┐
            │                             │
            ▼                             ▼
     ┌─────────────┐               ┌─────────────┐
     │    GCP      │ ✗ No access   │  Production │ ✗ No access
     │   Infra     │               │   Systems   │
     └─────────────┘               └─────────────┘
```

**Contractor Onboarding:**
1. CTO approves contractor engagement
2. Engineering Lead creates GitHub account invite
3. Contractor added to specific repositories only (not all)
4. Contractor receives read/write access to code only
5. Access documented in [ASSUMPTION: Notion or spreadsheet]

**Contractor Code Review:**
- All contractor PRs require review by internal Engineering team member
- Same code review standards as internal developers
- Contractor code passes through all automated scans (SonarQube, npm audit)
- No contractor code merges without internal approval

**Contractor Offboarding:**
1. Engagement ends
2. Engineering Lead removes GitHub access within 24 hours
3. Access removal documented
4. Any shared credentials rotated [ASSUMPTION: If applicable]

### 4.3 Quarterly Access Review

**Frequency:** Quarterly

**Process:**
1. CTO obtains list of all contractor GitHub accounts
2. CTO verifies each contractor is still engaged
3. Departed contractors removed immediately
4. Access levels verified as appropriate
5. Review documented in [ASSUMPTION: Notion]

### 4.4 Payment Gateway Management (Soepay, GP)

**Relationship:** API integration partners for payment processing

**Our Responsibilities:**

| Area | What We Do |
|------|-----------|
| Secure integration | Use HTTPS, validate responses, handle errors securely |
| Credential management | API keys stored in GCP Secret Manager, rotated per policy |
| Monitoring | Log all gateway interactions, alert on failures |
| PCI scope | Kraken handles all gateway communication (isolated VPC) |

**Gateway Responsibilities:**
- Their infrastructure security
- Their PCI compliance
- Transaction processing
- Fraud detection on their side

**Vendor Assessment:**
- [ASSUMPTION: Initial due diligence performed before engagement]
- Gateway PCI compliance verified
- Security documentation reviewed [ASSUMPTION: Annually]

### 4.5 SaaS Vendor Management

**Critical SaaS Vendors:**

| Vendor | Purpose | Data Stored | Security Consideration |
|--------|---------|-------------|----------------------|
| GCP | Infrastructure | All production data | PCI compliant, SOC 2 |
| GitHub | Code repository | Source code | SOC 2, SSO enabled |
| Sentry | Error tracking | Error logs (sanitized) | No PAN, no credentials |
| Firebase | Auth (Admin Portal) | CS user accounts | MFA enforced |
| Notion | Documentation | Internal docs | No production data |

**Vendor Selection Criteria:**
- Security certifications (SOC 2, ISO 27001, PCI if applicable)
- Data handling practices
- Encryption (at rest and in transit)
- Access controls and audit logging

**Ongoing Management:**
- Access to SaaS tools follows Access Control policy
- [ASSUMPTION: SSO enabled where supported]
- Departed employees removed from all SaaS tools

---

## 5. Operational Guarantees

When this control operates correctly:

- [ ] Contractors have GitHub code access only - no infrastructure or production access
- [ ] All contractor code is reviewed by internal team before merge
- [ ] Contractor access is reviewed quarterly
- [ ] Departed contractors have access removed within 24 hours
- [ ] Payment gateway credentials are stored securely (GCP Secret Manager)
- [ ] Critical SaaS vendors have appropriate security certifications

---

## 6. Evidence Produced

| Evidence Type | Description | System/Tool | Retention | Collection | Owner |
|--------------|-------------|-------------|-----------|------------|-------|
| Contractor access list | Current contractor accounts | GitHub + [ASSUMPTION: Notion] | Point-in-time | Manual | Engineering Lead |
| Access review records | Quarterly review completion | [ASSUMPTION: Notion] | Indefinite | Manual | CTO |
| PR review history | Internal review of contractor code | GitHub | Indefinite | Automatic | Engineering |
| Vendor documentation | Security certs, contracts | [ASSUMPTION: Notion/Drive] | Duration of engagement | Manual | CTO |
| Offboarding records | Access removal confirmation | GitHub audit log | 90 days | Automatic | Engineering Lead |

### Evidence Retrieval

- **Contractor access:** GitHub > Organization > People > Filter by role
- **PR reviews:** GitHub > Repository > Pull Requests > Filter by author
- **GitHub audit log:** GitHub > Organization > Audit Log

---

## 7. Exceptions & Edge Cases

### Known Exceptions

| Exception | Justification | Compensating Control | Review Date |
|-----------|--------------|---------------------|-------------|
| None currently | - | - | - |

### Exception Process

1. Exception requests go to CTO
2. CTO evaluates risk (data exposure, access scope)
3. If approved:
   - Document justification
   - Set time limit
   - Define additional monitoring
4. Reviewed quarterly

### Edge Cases

- **Contractor needs temporary infra access:** Denied by default. If critical, CTO approval required, time-limited, supervised access only.
- **Vendor security incident:** Assess impact on our data. Rotate credentials. Consider alternative vendor if severe.
- **Urgent contractor offboarding:** Engineering Lead removes access immediately. CTO notified.
- **New vendor evaluation:** CTO reviews security documentation before any data shared or integration built.

---

## 8. Review & Maintenance

**Review Schedule:**
- **Frequency:** Quarterly
- **Next Review:** 2025-04-27
- **Reviewer:** CTO

**Update Triggers:**
- New contractor engaged
- New vendor onboarded
- Vendor security incident
- Changes to contractor access requirements

**Change History:**

| Date | Change | Author |
|------|--------|--------|
| 2025-01-27 | Initial document created | [Author] |

---

## 9. Related Controls

| Control Area | Relationship |
|-------------|--------------|
| Access Control & Identity Management | Contractor access is subset of access control |
| Secure Development & Data Protection | Contractor code passes through same security scans |
| Network Security | Kraken VPC isolates payment gateway integrations |
| Vulnerability Management | Contractor code scanned for vulnerabilities |
| Business Continuity & Disaster Recovery | GitHub and GCP are critical vendors for recovery |
| Physical Security | Visitor access relates to third-party management |
