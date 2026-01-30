# Admin Portal Access Control

**Owner:** CTO
**Version:** 1.2
**Last Reviewed:** 2026-01-30
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
| **Password history** | **Last 4 passwords stored (one-way encoded in Firestore)** |
| MFA | Passkey (required) |
| Failed login lockout | 5 attempts |
| **Session timeout** | **15 minutes of inactivity (auto logout)** |

### 4.3.1 Password History Enforcement

**How It Works:**
1. When user sets a new password, system hashes it with bcrypt
2. New password hash compared against last 4 stored password hashes in Firestore
3. If new password matches any of the last 4, password change is rejected
4. On successful change, new hash added to history, oldest removed (maintain max 4)

**Storage:**
- Password history stored in Firestore collection: `users/{uid}/password_history`
- Each entry contains: bcrypt hash (one-way, cannot be reversed), timestamp
- Hashes are never decrypted - comparison done via bcrypt.compare()

**User Experience:**
- If user tries to reuse a recent password: "Password cannot be the same as your last 4 passwords"
- User must choose a genuinely new password

### 4.3.2 Session Timeout (Auto Logout)

**Configuration:**
- Idle session timeout: 15 minutes
- Implemented at application level (not Firebase Auth default)

**How It Works:**
1. Application tracks last user activity (clicks, keystrokes, navigation)
2. After 15 minutes of no activity, session is invalidated
3. User sees "Session expired" message
4. User must re-authenticate (password + passkey) to continue

**What Counts as Activity:**
- Any click or tap
- Any keyboard input
- Page navigation within Admin Portal

**What Does NOT Reset Timer:**
- Background API calls
- Browser tab sitting idle
- Mouse movement without clicking


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

### 4.6 Account Removal & Access Review

#### 4.6.1 Immediate Revocation on Termination

**Trigger:** Employee leaves company (resignation, termination, or end of contract)

**Process:**
1. HR notifies CTO/CEO of employee departure (same day)
2. Super Admin (CTO/CEO) **immediately** removes accounts:
   - Firebase Auth (Admin Portal) - account disabled/deleted
   - GCP IAM - user removed from all roles
   - GitHub - removed from organization
   - Microsoft 365 - account disabled
3. Removal completed **within the same business day** of notification
4. Removal logged with timestamp in Notion

**SLA:**
- **Target:** Same business day as HR notification
- **Maximum:** Within 4 hours of notification during business hours
- If notification received after hours: first thing next business day

**Tools:** Firebase Auth console, GCP Console, GitHub org settings, Microsoft 365 Admin

#### 4.6.2 Quarterly Access Review

**Trigger:** Quarterly cycle (every 3 months)

**Steps:**
1. CTO exports current user lists from:
   - Firebase Auth (Admin Portal users)
   - GCP IAM (infrastructure users)
   - GitHub (code access)
2. CTO compares against current HR roster
3. Any discrepancies identified (accounts for departed employees, unknown accounts)
4. Discrepancies remediated immediately
5. Access levels verified appropriate for current roles
6. Review completion recorded in Notion with date and reviewer signature

**Review Checklist:**
- [ ] All Admin Portal accounts match active CS employees
- [ ] All GCP accounts match active Engineering/CTO employees
- [ ] All GitHub accounts match active Engineering/QA employees
- [ ] No inactive accounts older than 90 days
- [ ] No orphaned service accounts

**Tools:** Firebase Auth console, GCP Console, GitHub org settings, HR roster, Notion

### 4.7 Credential & Secret Rotation

#### 4.7.1 Cloud SQL Database Credentials

**Rotation Schedule:** Every 90 days (3 months)

**Process:**
1. Engineering generates new Cloud SQL user password
2. New password stored in GCP Secret Manager (new version)
3. Application configuration updated to use new secret version
4. Deployment triggered to pick up new credentials
5. Old password disabled after confirming new password works
6. Rotation logged with date in Notion

**Automation:**
- Calendar reminder set for rotation dates
- Secret Manager versioning provides audit trail

#### 4.7.2 GCP KMS Key Rotation

**Rotation Schedule:** Every 90 days (3 months) - automated

**How It Works:**
- GCP KMS automatically generates new key version every 90 days
- New encryptions use the new key version automatically
- Previous versions remain available for decryption of existing data
- No manual intervention required

**Verification:**
- CTO verifies key rotation quarterly via GCP Console > Security > Key Management
- Rotation events visible in Cloud Audit Logs

**See also:** Cryptographic Key Management control document for full key lifecycle details.

#### 4.7.3 API Keys & Secrets Management

**Storage:** GitHub Secrets (Vault) + GCP Secret Manager

**Security Controls:**

| Control | Implementation |
|---------|---------------|
| **Storage** | All API keys and secrets stored in GitHub Secrets or GCP Secret Manager |
| **Clear text prevention** | Secrets never appear in source code, config files, or environment variables in repository |
| **Build log protection** | GitHub Actions configured to mask secrets - never visible in build logs |
| **UI protection** | Secrets not displayed in any admin UI or configuration screens |
| **Access control** | Only Engineering team has access to Secret Manager; secrets accessed programmatically at runtime |

**What Is Stored as Secrets:**
- Payment gateway API keys (Soepay, GP)
- Cloud SQL connection credentials
- Third-party service credentials
- Internal service-to-service tokens

**How Secrets Are Used:**
1. Secrets defined in GitHub Secrets (organization level) or GCP Secret Manager
2. GitHub Actions injects secrets at build/deploy time (masked in logs)
3. Applications retrieve secrets from Secret Manager at runtime
4. Secrets never written to disk or logged

**Rotation Policy:**
- API keys: Rotate upon suspected compromise or annually (whichever is sooner)
- Service credentials: Every 90 days or upon personnel change with access

### 4.8 Refund/Void Audit

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
- [ ] **Users cannot reuse their last 4 passwords (enforced via Firestore history)**
- [ ] **Sessions auto-logout after 15 minutes of inactivity**
- [ ] Full PAN is never visible in Admin Portal
- [ ] All refund/void operations are logged with agent ID and timestamp
- [ ] No transaction can be charged beyond original amount
- [ ] **Departed employee accounts are removed same business day (GCP, Admin Portal, GitHub)**
- [ ] User access is reviewed quarterly (every 3 months)
- [ ] **Cloud SQL credentials rotate every 90 days**
- [ ] **GCP KMS keys rotate every 90 days (automated)**
- [ ] **API keys and secrets are never visible in build logs or UI**

---

## 6. Evidence Produced

| Evidence Type | Description | System/Tool | Retention | Collection | Owner |
|--------------|-------------|-------------|-----------|------------|-------|
| Login logs | Authentication attempts (success/fail) | Firebase Auth + GCP Logging | 1 year (Cloud Logging) | Automatic | CTO |
| Session timeout events | Auto-logout after 15 min inactivity | Application logs (GCP Logging) | 1 year | Automatic | Engineering |
| Password change events | Password resets and history enforcement | Firebase Auth + Firestore | Indefinite (hashes only) | Automatic | CTO |
| Password history | Last 4 password hashes (bcrypt) | Firestore | Current + last 4 only | Automatic | Engineering |
| Refund/void logs | All refund/void transactions with agent ID | Application DB + GCP Logging | 1 year | Automatic | CS Lead |
| Account changes | User creation/deletion events | Firebase Auth + GCP Audit Logs | 1 year | Automatic | CTO |
| Account removal records | Same-day termination removals | Notion + GCP Audit Logs | Indefinite | Manual + Automatic | CTO |
| Access review records | Quarterly review completion | Notion | Indefinite | Manual | CTO |
| Audit review records | Refund/void audit findings | Notion | Indefinite | Manual | CS Lead |
| Credential rotation logs | SQL password, KMS key rotations | GCP Audit Logs + Notion | 1 year (Audit Logs), Indefinite (Notion) | Automatic + Manual | Engineering |
| Secret Manager access logs | Who accessed which secrets | GCP Audit Logs | 1 year | Automatic | Engineering |

### Evidence Retrieval

- **Session timeouts:** GCP Logging > Filter by `session_expired` or `auto_logout` events
- **Password history enforcement:** Firestore > `users/{uid}/password_history` collection
- **Account removals:** Notion > Access Review database > filter by "Termination" type
- **Credential rotation:** GCP Console > Secret Manager > Versions tab; GCP Console > KMS > Key versions
- **Secrets access:** GCP Console > Logging > Filter: `resource.type="secretmanager.googleapis.com"`

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
| 2026-01-30 | v1.2: Added password history (last 4, Firestore), 15-min session timeout, immediate account revocation SLA, Cloud SQL/KMS credential rotation, secrets management (GitHub Vault, no clear text in logs/UI) | [Author] |

---

## 9. Related Controls

| Control Area | Relationship |
|-------------|--------------|
| Access Control & Identity Management | Admin Portal is a system covered by overall access policy; immediate revocation applies to GCP/GitHub as well |
| Cryptographic Key Management | KMS key rotation policy defined there; referenced here for 90-day rotation |
| Secure Development & Data Protection | PAN masking implemented per data protection standards; secrets management aligned |
| Logging & Monitoring | Refund/void logs, session timeouts, and auth events feed into central logging |
| Incident Response | CS reports incidents; uses Admin Portal to verify impact |
| Physical Security | CS Room separation supports CS-only access |
| Security Standards & Exception Governance | Authentication exceptions follow exception governance process |
| Security Policy & Awareness | MFA requirements and training documented there |
