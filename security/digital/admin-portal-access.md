# Admin Portal Access Control

**Owner:** CTO
**Last Reviewed:** 2025-01-27
**Review Cadence:** Quarterly

---

## 1. Purpose

We control access to the Admin Portal to ensure only authorized CS team members can view transaction data and perform limited operations (refund/void). This prevents unauthorized data access, ensures all sensitive actions are auditable, and protects customers from fraudulent transactions.

**Risk Reduced:**
- Unauthorized access to customer transaction data
- Fraudulent refunds or voids
- Untracked changes to transaction state
- Stale accounts from departed employees

**Stakeholders:**
- CS Team (uses portal for customer support)
- Customers (trust us with transaction data)
- Finance (reconciliation depends on accurate refund/void records)

---

## 2. Scope

### In Scope
- **Systems:** DASH Admin Portal
- **Environments:** Production
- **Users:** CS Team members
- **Operations:** View transactions, issue refunds, void transactions

### Out of Scope
- Engineering infrastructure access (covered in Access Control & Identity Management)
- Payment gateway admin access (managed by gateway providers)
- Customer-facing applications

### Data Visibility

| Data Type | Visible in Admin Portal |
|-----------|------------------------|
| Transaction list | Yes |
| Transaction details | Yes |
| Full PAN | **No** (masked/tokenized) |
| Customer contact info | Yes |
| Refund/void history | Yes |

---

## 3. Roles & Responsibilities

| Role | Team/Individual | Responsibility |
|------|-----------------|----------------|
| Control Owner | CTO | Sets access policy, approves exceptions |
| Super Admin | CTO or CEO | Creates/removes CS user accounts |
| Operator | CS Team | Uses portal for customer support operations |
| Audit Reviewer | CS Lead | Reviews refund/void activity |

---

## 4. How We Operate This Control

### 4.1 Authentication Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                       CS Team User                          │
└─────────────────────────────┬───────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                     Firebase Auth                            │
│  ┌─────────────────┐    ┌─────────────────┐                 │
│  │ Password Login  │ +  │  Passkey (MFA)  │                 │
│  └─────────────────┘    └─────────────────┘                 │
└─────────────────────────────┬───────────────────────────────┘
                              │ Authenticated
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                     DASH Admin Portal                        │
│              (No full PAN - tokens only)                     │
└─────────────────────────────────────────────────────────────┘
```

### 4.2 Account Provisioning

**Trigger:** New CS team member onboarded

**Steps:**
1. HR notifies CTO/CEO of new CS hire
2. Super Admin (CTO or CEO) creates account in Firebase Auth
3. Super Admin sets initial temporary password
4. New user logs in and completes:
   - Password reset (must meet strength requirements)
   - Passkey enrollment (MFA)
5. [ASSUMPTION: Account creation logged in Firebase Auth console]

**Tools:** Firebase Auth console

**Automation:** None - manual account creation by Super Admin only

### 4.3 Authentication Requirements

| Requirement | Setting |
|-------------|---------|
| Password minimum length | 8 characters |
| Password complexity | 1 uppercase, 1 lowercase, 1 number, 1 special character |
| Password expiry | 180 days |
| Password hashing | bcrypt |
| MFA | Passkey (required) |
| Failed login lockout | 5 attempts |


### 4.4 Login Process

**Steps:**
1. User navigates to Admin Portal
2. User enters email and password
3. Firebase Auth validates credentials
4. If password correct, prompt for passkey (MFA)
5. User authenticates with passkey
6. Access granted to Admin Portal
7. Login event logged

**Lockout:**
- After 5 failed password attempts, account is locked
- Super Admin manually resets account via Firebase console

### 4.5 Transaction Operations

**View Transactions:**
- CS team can view transaction list and details
- Full PAN is never displayed (masked/tokenized)

**Refund Transaction:**
1. CS agent locates transaction
2. CS agent initiates refund (amount limited to original transaction)
3. System prevents refund exceeding original amount
4. Refund logged with: timestamp, agent ID, transaction ID, amount, reason
5. Refund processed through payment gateway

**Void Transaction:**
1. CS agent locates transaction
2. CS agent initiates void
3. Void logged with: timestamp, agent ID, transaction ID, reason
4. Void processed through payment gateway

**Restrictions:**
- Cannot charge additional amounts to customer
- Cannot refund more than original transaction amount
- All refund/void actions are logged and auditable

### 4.6 Quarterly Access Review

**Trigger:** Quarterly cycle + HR termination notifications

**Steps:**
1. CS Lead obtains current Firebase Auth user list
2. CS Lead compares against current HR roster
3. Departed employees identified
4. Super Admin (CTO/CEO) removes accounts for departed employees
5. Review completion recorded

**HR Integration:**
- When an employee leaves, HR notifies CTO/CEO
- Account removed within [ASSUMPTION: 24 hours of termination]

**Tools:** Firebase Auth console, HR roster

### 4.7 Refund/Void Audit

**Frequency:** [ASSUMPTION: Monthly or as needed]

**Steps:**
1. CS Lead pulls refund/void report from Admin Portal or GCP Logging
2. CS Lead reviews for anomalies:
   - Unusual refund volume by agent
   - Refunds without customer complaint tickets
   - Pattern of refunds to same customers
3. Anomalies escalated to CTO
4. Review documented

**Tools:** Application database, GCP Cloud Logging

---

## 5. Operational Guarantees

When this control operates correctly:

- [ ] Only Super Admins (CTO/CEO) can create or remove CS accounts
- [ ] All Admin Portal logins require password + passkey (MFA)
- [ ] Accounts lock after 5 failed login attempts
- [ ] Passwords expire every 180 days
- [ ] Full PAN is never visible in Admin Portal
- [ ] All refund/void operations are logged with agent ID and timestamp
- [ ] No transaction can be charged beyond original amount
- [ ] Departed employee accounts are removed (tied to HR process)
- [ ] User access is reviewed quarterly

---

## 6. Evidence Produced

| Evidence Type | Description | System/Tool | Retention | Collection | Owner |
|--------------|-------------|-------------|-----------|------------|-------|
| Login logs | Authentication attempts (success/fail) | Firebase Auth | [ASSUMPTION: 30 days - verify] | Automatic | CTO |
| Refund/void logs | All refund/void transactions with agent ID | Application DB + GCP Logging | [ASSUMPTION: 1 year - verify] | Automatic | CS Lead |
| Account changes | User creation/deletion events | Firebase Auth | [ASSUMPTION: 30 days - verify] | Automatic | CTO |
| Access review records | Quarterly review completion | Notion | Indefinite | Manual | CS Lead |
| Audit review records | Refund/void audit findings | Notion | Indefinite | Manual | CS Lead |

### Evidence Gaps

- **Firebase Auth retention:** Verify actual log retention period in Firebase
- **Account provisioning:** No formal ticket trail for account creation requests

---

## 7. Exceptions & Edge Cases

### Known Exceptions

| Exception | Justification | Compensating Control | Review Date |
|-----------|--------------|---------------------|-------------|
| None currently | - | - | - |

### Exception Process

1. Exception requests go to CTO
2. CTO evaluates risk
3. Approved exceptions documented
4. Reviewed quarterly

### Edge Cases

- **Account locked, urgent customer issue:** Super Admin (CTO/CEO) unlocks account via Firebase console
- **Super Admin unavailable:** Both CTO and CEO have Super Admin access; escalate to whichever is available
- **Disputed refund:** CS Lead reviews audit log, escalates to CTO if fraud suspected

---

## 8. Review & Maintenance

**Review Schedule:**
- **Frequency:** Quarterly
- **Next Review:** 2025-04-27
- **Reviewer:** CTO

**Update Triggers:**
- CS team membership changes
- Firebase Auth configuration changes
- Changes to refund/void workflow
- Security incidents related to Admin Portal

**Change History:**

| Date | Change | Author |
|------|--------|--------|
| 2025-01-27 | Initial document created | [Author] |

---

## 9. Related Controls

| Control Area | Relationship |
|-------------|--------------|
| Access Control & Identity Management | Admin Portal is a system covered by overall access policy |
| Secure Development & Data Protection | PAN masking implemented per data protection standards |
| Logging & Monitoring | Refund/void logs feed into central logging |
