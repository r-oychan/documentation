# Physical Security

**Owner:** CTO
**Version:** 1.1
**Last Reviewed:** 2026-01-29
**Review Cadence:** Quarterly

---

## 1. Purpose

We control physical access to office facilities and enforce data handling practices to prevent unauthorized access to systems and sensitive information. This control ensures separation of duties between teams and prevents physical data leakage.

**Risk Reduced:**
- Unauthorized physical access to work areas
- Conflict of interest between CS and Engineering teams
- Data leakage through printed materials or physical media
- Network-based attacks from office WiFi

**Stakeholders:**
- All employees (office access)
- CS Team (restricted area access)
- Engineering Team (separation from CS)
- Customers (data protection)

---

## 2. Scope

### In Scope
- **Facilities:** DASH office premises
- **Areas:** General office, CS Room (restricted)
- **Controls:** Access cards, network isolation, data handling
- **Users:** All employees, visitors

### Out of Scope
- GCP data center physical security (Google's responsibility)
- Remote work locations (covered by acceptable use policy)
- Third-party vendor offices

---

## 3. Roles & Responsibilities

| Role | Team/Individual | Responsibility |
|------|-----------------|----------------|
| Control Owner | CTO | Sets physical security policy, approves access exceptions |
| Facility Manager | [ASSUMPTION: Office Manager or CTO] | Manages access card provisioning, maintains access logs |
| CS Room Access Approver | CTO | Approves CS Room access requests |

---

## 4. How We Operate This Control

### 4.1 Office Access Control

**Access Method:** Access cards

**Access Levels:**

| Area | Who Has Access | Purpose |
|------|---------------|---------|
| General Office | All employees | Day-to-day work |
| CS Room | CS Team only | Customer support operations, data separation |

**Card Provisioning:**

1. New employee joins
2. HR notifies Facility Manager
3. Facility Manager issues access card with appropriate access level
4. Card configured for:
   - General office access (all employees)
   - CS Room access (CS team only)
5. [ASSUMPTION: Card issuance logged in access control system]

**Card Deprovisioning:**

1. Employee leaves company
2. HR notifies Facility Manager
3. Access card collected and deactivated
4. [ASSUMPTION: Within 24 hours of last working day]

### 4.2 CS Room Separation

**Purpose:** Prevent conflict of interest between CS and Engineering teams

**Controls:**
- CS Room requires separate access card authorization
- Only CS team members have CS Room access
- Engineering team does not have access to CS Room
- Sensitive customer interactions handled in CS Room

**Rationale:**
- CS handles refunds/voids (financial operations)
- Engineering handles code/infrastructure
- Separation prevents unauthorized influence on financial operations

### 4.3 Network Isolation

**Office WiFi:**
- Office WiFi does **not** have direct connectivity to GCP infrastructure
- GCP access requires authenticated VPN or direct console login
- WiFi is for general internet access only

**GCP Access Path:**

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│  Office WiFi    │────▶│    Internet     │────▶│   GCP Console   │
│  (No direct     │     │                 │     │  (Authenticated │
│   GCP access)   │     │                 │     │   access only)  │
└─────────────────┘     └─────────────────┘     └─────────────────┘
                                                        │
                                                        ▼
                                                ┌─────────────────┐
                                                │  IAM + MFA      │
                                                │  (Per Access    │
                                                │   Control doc)  │
                                                └─────────────────┘
```

### 4.4 Data Handling - No Physical Data

**Prohibited:**
- Printing customer/user data
- Printing database contents or exports
- Physical media containing sensitive data (USB drives, etc.)
- Screenshots or photos of sensitive data on screens

**What We Do:**
- All data access is digital and logged
- No paper records of customer information
- No physical backups or tapes
- Database access via authenticated tools only

**Enforcement:**
- No printers configured with access to production data
- [ASSUMPTION: Clean desk policy - verify if formal]
- Code review checks for data export functionality

### 4.5 Visitor Access

**Process:**
1. Employee hosts visitor
2. Visitor signs in at reception [ASSUMPTION: Visitor log exists]
3. Visitor escorted at all times
4. Visitor does not receive access card
5. Visitor signed out upon departure

**Restrictions:**
- Visitors cannot access CS Room
- Visitors cannot be left unattended in work areas

---

## 5. Operational Guarantees

When this control operates correctly:

- [ ] All employees access office via access card
- [ ] Only CS team members can access CS Room
- [ ] Engineering team cannot access CS Room (separation of duties)
- [ ] Office WiFi has no direct path to GCP infrastructure
- [ ] No customer data is printed or stored on physical media
- [ ] Departed employees have access cards deactivated
- [ ] Visitors are escorted and logged

---

## 6. Evidence Produced

| Evidence Type | Description | System/Tool | Retention | Collection | Owner |
|--------------|-------------|-------------|-----------|------------|-------|
| Access card logs | Entry/exit records | Access control system | [ASSUMPTION: 90 days] | Automatic | Facility Manager |
| Card provisioning records | Who has what access | Access control system | [ASSUMPTION: Duration of employment + 1 year] | Manual | Facility Manager |
| Visitor log | Visitor sign-in/out | [ASSUMPTION: Paper or digital log] | [ASSUMPTION: 90 days] | Manual | Reception |

### Evidence Gaps

- **Access log retention:** Verify actual retention period in access control system
- **Visitor log format:** Confirm if digital or paper-based

---

## 7. Exceptions & Edge Cases

### Known Exceptions

| Exception | Justification | Compensating Control | Review Date |
|-----------|--------------|---------------------|-------------|
| CTO may access CS Room | Oversight responsibility | Access logged, infrequent | Quarterly |

### Exception Process

1. Exception requests go to CTO
2. CTO evaluates business need
3. Approved exceptions documented
4. Reviewed quarterly

### Edge Cases

- **Lost access card:** Report immediately to Facility Manager. Card deactivated. Temporary card issued. Permanent replacement within [ASSUMPTION: 2 business days].
- **After-hours access needed:** Access cards work 24/7 for authorized personnel. [ASSUMPTION: No additional approval needed]
- **Emergency (fire, etc.):** All doors unlock. Normal access control resumes after emergency resolved.

---

## 8. Review & Maintenance

**Review Schedule:**
- **Frequency:** Quarterly
- **Next Review:** 2025-04-27
- **Reviewer:** CTO

**Update Triggers:**
- Office relocation or expansion
- New restricted areas added
- Security incidents related to physical access
- Changes to team structure affecting separation

**Change History:**

| Date | Change | Author |
|------|--------|--------|
| 2025-01-27 | Initial document created | [Author] |

---

## 9. Related Controls

| Control Area | Relationship |
|-------------|--------------|
| Access Control & Identity Management | Digital access complements physical access |
| Admin Portal Access Control | CS Room separation supports CS-only portal access |
| Logging & Monitoring | Physical access logs complement digital audit logs |
| Network Security | Office WiFi isolation complements network security |
| Third-Party Risk Management | Visitor access relates to third-party management |
| Security Policy & Awareness | Physical security training (Module 6) for all staff |
| Incident Response | Physical security incidents follow incident process |
