# Security Standards & Exception Governance

**Owner:** CTO
**Version:** 1.0
**Last Reviewed:** 2026-01-30
**Review Cadence:** Annually

---

## 1. Purpose

We adopt security best practices and industry standards (including PCI DSS requirements) as our baseline security configuration. When operational needs require deviation from these standards, we follow a formal exception process requiring executive approval. This ensures security decisions are deliberate, documented, and revisited periodically.

**Risk Reduced:**
- Security controls implemented inconsistently
- Undocumented deviations creating compliance gaps
- Shadow configurations bypassing security requirements
- Inability to demonstrate due diligence during audits

**Stakeholders:**
- Engineering Team (implements controls, may request exceptions)
- CTO (security standards owner, exception approver)
- CEO (exception approver for high-risk deviations)
- QA Team (verifies implementation)
- All employees (operates within standards)

---

## 2. Scope

### In Scope
- **Systems:** All DASH systems, applications, and infrastructure
- **Environments:** Production, QA, Development
- **Standards Covered:**
  - PCI DSS v4.0.1 requirements (for cardholder data environment)
  - Security best practices (OWASP, CIS Benchmarks, GCP security guidelines)
  - Internal security control documents
- **Personnel:** All employees and contractors

### Out of Scope
- Third-party vendor compliance (covered in Third-Party Risk Management)
- Customer security configurations

---

## 3. Roles & Responsibilities

| Role | Team/Individual | Responsibility |
|------|-----------------|----------------|
| Standards Owner | CTO | Defines security standards, reviews exception requests, approves standard-risk exceptions |
| Executive Approver | CEO | Approves high-risk exceptions jointly with CTO |
| Exception Requester | Any Employee | Submits exception requests with business justification |
| Implementation Owner | Engineering | Implements controls and compensating controls |
| Compliance Reviewer | CTO | Validates exception doesn't create unacceptable compliance risk |
| Auditor | QA / External | Verifies controls are implemented as documented |

---

## 4. How We Operate This Control

### 4.1 Security Standards Baseline

**Default Position:** All security controls follow industry best practices and applicable compliance requirements.

**Standards We Follow:**

| Standard | Scope | Source |
|----------|-------|--------|
| **PCI DSS v4.0.1** | Cardholder Data Environment (Kraken) | PCI Security Standards Council |
| **OWASP Top 10** | All web applications | OWASP Foundation |
| **GCP Security Best Practices** | All GCP infrastructure | Google Cloud documentation |
| **CIS Benchmarks** | System hardening (where applicable) | Center for Internet Security |
| **Internal Control Documents** | All operations | DASH security documentation |

**Implementation Principle:**
- Security controls are implemented according to standards by default
- No deviation without documented exception
- Standards apply to new and existing systems

### 4.2 Standard Configuration Requirements

The following configurations are **mandatory** unless a formal exception is approved:

**Authentication & Access:**
- MFA required for all systems with access to production or customer data
- Unique user accounts (no shared credentials)
- Password complexity: 8+ characters with complexity OR 12+ characters passphrase
- Session timeout: 15 minutes for systems accessing cardholder data
- Account lockout: 5 failed attempts

**Encryption:**
- Data at rest: AES-256 encryption for all sensitive data
- Data in transit: TLS 1.2 minimum, TLS 1.3 preferred
- Key management via GCP KMS with automatic rotation

**Logging & Monitoring:**
- All security events logged
- Logs retained for minimum 12 months
- Centralized logging (GCP Cloud Logging)
- No PII/PAN in logs

**Network Security:**
- Default deny firewall rules
- VPC segmentation for CDE
- WAF protection for public endpoints

**Development:**
- Code review required for all changes
- Automated security scanning (SonarQube, npm audit)
- Separate credentials per environment
- No production data in non-production environments

### 4.3 Exception Request Process

**When an Exception is Needed:**
- Operational requirement prevents standard implementation
- Technical limitation requires alternative approach
- Business urgency requires temporary deviation
- Legacy system cannot meet current standard

**Exception Request Contents:**

| Field | Description | Required |
|-------|-------------|----------|
| Requester | Name and role of person requesting | Yes |
| Date | Request submission date | Yes |
| Standard/Control | Which standard or control requires deviation | Yes |
| Current Requirement | What the standard requires | Yes |
| Proposed Deviation | What you want to do instead | Yes |
| Business Justification | Why the deviation is necessary | Yes |
| Risk Assessment | What risks the deviation introduces | Yes |
| Compensating Controls | How risks will be mitigated | Yes |
| Duration | Temporary (with end date) or permanent | Yes |
| Systems Affected | Which systems are impacted | Yes |
| Compliance Impact | Does this affect PCI or other compliance | Yes |

### 4.4 Exception Approval Workflow

```
┌─────────────────────────────────────────────────────────────┐
│                    Exception Request                         │
│              (Submitted by any employee)                     │
└─────────────────────────────┬───────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│               CTO Reviews Request                            │
│  • Validates business justification                          │
│  • Assesses risk level                                       │
│  • Evaluates compensating controls                           │
│  • Checks compliance impact                                  │
└─────────────────────────────┬───────────────────────────────┘
                              │
              ┌───────────────┴───────────────┐
              │         Risk Level?            │
              └───────────────┬───────────────┘
                              │
         ┌────────────────────┼────────────────────┐
         │                    │                    │
         ▼                    ▼                    ▼
┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│   Low Risk      │  │  Standard Risk  │  │   High Risk     │
│                 │  │                 │  │                 │
│ CTO approves    │  │ CTO approves    │  │ CTO + CEO       │
│ (document only) │  │ with conditions │  │ joint approval  │
└────────┬────────┘  └────────┬────────┘  └────────┬────────┘
         │                    │                    │
         └────────────────────┴────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                  Exception Documented                        │
│  • Recorded in Exception Register (Notion)                   │
│  • Review date set (max 12 months)                           │
│  • Compensating controls implemented                         │
└─────────────────────────────────────────────────────────────┘
```

### 4.5 Risk Classification

**Low Risk:**
- Does not affect cardholder data or PCI scope
- Compensating control fully mitigates risk
- Affects non-production environment only
- **Approval:** CTO only

**Standard Risk:**
- Affects production systems but not CDE
- Partial compensating control available
- Temporary deviation (< 6 months)
- **Approval:** CTO only, with documented conditions

**High Risk:**
- Affects cardholder data environment (CDE)
- Impacts PCI DSS compliance status
- No adequate compensating control available
- Permanent or long-term deviation (> 6 months)
- Could result in data breach if exploited
- **Approval:** CTO AND CEO joint approval required

### 4.6 Compensating Controls

When deviating from a standard, compensating controls must:

1. **Meet the intent** of the original requirement
2. **Provide similar protection** against the same threat
3. **Be documented** with clear implementation steps
4. **Be verifiable** through evidence or testing
5. **Not rely solely** on future remediation

**Compensating Control Documentation:**

| Element | Description |
|---------|-------------|
| Original Requirement | What standard control is being compensated |
| Constraint | Why original cannot be implemented |
| Objective | Security objective being achieved |
| Compensating Control | Alternative control implemented |
| Validation | How effectiveness is verified |
| Risk Remaining | Any residual risk after compensation |

### 4.7 Exception Register

All approved exceptions are tracked in the Exception Register.

**Exception Register Location:** Notion > Security > Exception Register

**Register Contents:**

| Field | Description |
|-------|-------------|
| Exception ID | Unique identifier (EXC-YYYY-NNN) |
| Request Date | When exception was requested |
| Requester | Who submitted the request |
| Standard/Control | What is being deviated from |
| Deviation Description | Brief description of the exception |
| Business Justification | Why exception is needed |
| Risk Level | Low / Standard / High |
| Approver(s) | CTO, or CTO + CEO for high risk |
| Approval Date | When exception was approved |
| Compensating Controls | Mitigations in place |
| Duration | Temporary (with end date) or Permanent |
| Review Date | Next mandatory review (max 12 months) |
| Status | Active / Expired / Remediated / Rejected |

### 4.8 Exception Review Process

**Quarterly Review (All Active Exceptions):**
1. CTO pulls list of active exceptions from Exception Register
2. For each exception:
   - Verify compensating controls still in place
   - Assess if original constraint still exists
   - Check if remediation path is available
   - Update status if conditions changed
3. Document review completion with date

**Annual Review (Mandatory):**
- All exceptions must be re-approved annually
- No exception can remain active > 12 months without re-approval
- Expired exceptions without re-approval must be remediated immediately

**Review Triggers:**
- Security incident related to excepted area
- Compliance audit finding
- Technology change enabling standard implementation
- Personnel change (new CTO/CEO must review and re-approve)

### 4.9 Exception Rejection

**Reasons for Rejection:**
- Insufficient business justification
- Compensating control does not adequately address risk
- Compliance impact too severe (e.g., would fail PCI audit)
- Simpler path to compliance available

**After Rejection:**
- Requester notified with reasons
- Option to revise and resubmit
- Must implement standard control if no alternative approved

### 4.10 Emergency Exceptions

**For urgent operational needs:**

1. CTO may grant verbal approval for temporary exception (max 7 days)
2. Formal documentation must be completed within 48 hours
3. CEO notified within 24 hours for high-risk areas
4. Full approval process completed within 7 days or exception expires

**Emergency Exception Tracking:**
- Same register, marked with "EMERGENCY" flag
- Post-incident review required

---

## 5. Operational Guarantees

When this control operates correctly:

- [ ] All security controls follow documented standards unless exception exists
- [ ] No deviation from security standards without written CTO approval
- [ ] High-risk deviations (CDE, PCI, data breach potential) require CTO AND CEO joint approval
- [ ] All exceptions are documented in the Exception Register with business justification
- [ ] Compensating controls are implemented and documented for all approved exceptions
- [ ] All exceptions are reviewed at least annually
- [ ] No exception remains active beyond its approved duration without re-approval
- [ ] Emergency exceptions are formalized within 7 days

---

## 6. Evidence Produced

| Evidence Type | Description | System/Tool | Retention | Collection | Owner |
|--------------|-------------|-------------|-----------|------------|-------|
| Exception requests | Request documents with justification | Notion | Duration of exception + 3 years | Manual | CTO |
| Approval records | CTO/CEO approval with date | Notion + Email | Duration of exception + 3 years | Manual | CTO |
| Exception Register | All exceptions with status | Notion | Indefinite | Manual | CTO |
| Quarterly review records | Review completion documentation | Notion | 3 years | Manual | CTO |
| Compensating control evidence | Proof controls are implemented | Varies | Duration of exception | Manual/Automatic | Engineering |
| Emergency exception notifications | CEO notifications for urgent approvals | Email | 3 years | Manual | CTO |

### Evidence Retrieval

- **Exception Register:** Notion > Security > Exception Register
- **Approval Records:** Notion > Security > Exception Register > [Exception ID] > Approvals
- **Review Records:** Notion > Security > Exception Register > Quarterly Reviews
- **Email Approvals:** CTO/CEO email archives (for high-risk joint approvals)

---

## 7. Exceptions & Edge Cases

### Known Exceptions

| Exception | Justification | Compensating Control | Review Date |
|-----------|--------------|---------------------|-------------|
| None currently | - | - | - |

### Edge Cases

- **CTO unavailable for approval:** CEO may approve standard-risk exceptions temporarily; CTO must ratify within 7 days of return
- **CEO unavailable for high-risk approval:** Must wait for CEO unless emergency (then emergency process)
- **Disagreement between CTO and CEO:** CEO decision is final, but CTO's concerns must be documented
- **Inherited exceptions (acquisition, new system):** Must be documented within 30 days; treated as new exceptions requiring approval
- **Compliance requirement conflict:** If two standards conflict, document which takes precedence and why

---

## 8. Review & Maintenance

**Review Schedule:**
- **Frequency:** Annually (standards review), Quarterly (exception review)
- **Next Review:** 2027-01-30
- **Reviewer:** CTO

**Update Triggers:**
- New compliance requirement (e.g., PCI version update)
- Security incident revealing gap
- New technology adoption
- Change in risk appetite
- CTO or CEO personnel change

**Change History:**

| Date | Change | Author |
|------|--------|--------|
| 2026-01-30 | Initial document created | [Author] |

---

## 9. Related Controls

| Control Area | Relationship |
|-------------|--------------|
| Access Control & Identity Management | Access exceptions follow this governance process |
| Network Security | Network configuration exceptions require approval here |
| Secure Development & Data Protection | Development practice exceptions governed here |
| Cryptographic Key Management | Encryption exceptions (e.g., algorithm choice) governed here |
| Logging & Monitoring | Logging exceptions (e.g., retention) governed here |
| Vulnerability Management | Vulnerability acceptance/deferral uses this exception process |
| Third-Party Risk Management | Vendor security exceptions governed here |
| Incident Response | Security incidents may trigger exception reviews |
| Security Policy & Awareness | This document is part of the security policy set |
