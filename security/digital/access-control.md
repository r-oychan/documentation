# Access Control & Identity Management

**Owner:** CTO
**Version:** 1.2
**Last Reviewed:** 2026-01-30
**Review Cadence:** Quarterly

---

## 1. Purpose

We control access to infrastructure and systems to ensure only authorized personnel can view, modify, or deploy changes to production. This prevents unauthorized changes, reduces the blast radius of compromised credentials, and ensures we can trace all actions to specific individuals.

**Risk Reduced:**
- Unauthorized access to production infrastructure
- Unattributable changes to systems
- Privilege escalation from compromised accounts

**Stakeholders:**
- Engineering team (needs access to deploy and view logs)
- QA team (needs to approve releases)
- CTO, Head of Product, CEO (deployment approvers)
- CS team (no infrastructure access required)

---

## 2. Scope

### In Scope
- **Systems:** GCP infrastructure, GitHub repositories
- **Environments:** Production
- **Services:** All deployed applications and infrastructure
- **Users:** All employees (Product, QA, Engineering, CS teams)

### Out of Scope
- Third-party SaaS tools (Notion, Slack, etc.) - managed separately
- Development/local environments

### GCP IAM (Inherited Controls)

GCP Identity and Access Management provides foundational access control:

| PCI Requirement | GCP IAM Provides | Our Responsibility |
|-----------------|-----------------|-------------------|
| **7.2.1** (Access control model) | Role-based access control (RBAC) | Configure roles per least privilege |
| **7.3.2** (Default deny) | Default deny-all for resources | Explicitly grant required permissions |
| **8.2.1** (Unique user IDs) | Google accounts with unique identifiers | Use individual accounts, no shared |
| **8.3.1** (MFA for CDE) | Google account MFA | Enforce MFA for all GCP access |

**Cloud Run / Cloud Functions Service Identity:**
- Each service runs with dedicated service account (not user credentials)
- Workload Identity for GKE / Cloud Run (no static keys)
- Service accounts have minimal required permissions

---

## 3. Roles & Responsibilities

| Role | Team/Individual | Responsibility |
|------|-----------------|----------------|
| Control Owner | CTO | Approves access, conducts quarterly reviews, approves exceptions |
| Operator | Engineering Team | Manages day-to-day access provisioning in GCP Console |
| Deployment Approver | CTO, Head of Product, or CEO | Final approval for production deployments |
| QA Approver | QA Team | Signs off on release readiness |

### Team Access Levels

| Team | GCP Access | GitHub Access | Deployment Rights |
|------|-----------|---------------|-------------------|
| Engineering | Read-only (logs) | Read/Write | Via automated GitHub Actions only |
| QA | None | Read (to review PRs) | Approval authority |
| Product | None | Read | Approval authority (Head of Product) |
| CS | None | None | None |
| **CTO** | **IAM Admin** (with CEO approval) | Admin | Approval authority |
| **CEO** | **IAM Admin** (with CTO approval) | Admin | Approval authority |

### GCP IAM Administration

**Only CTO and CEO can modify GCP IAM roles and permissions.**

| Privilege | Who Has Access | Control |
|-----------|---------------|---------|
| View IAM roles | CTO, CEO | Direct access |
| Modify IAM roles | CTO, CEO | **Maker-checker required** |
| Create service accounts | CTO, CEO | **Maker-checker required** |
| Grant/revoke user access | CTO, CEO | **Maker-checker required** |
| Emergency break-glass | CTO or CEO alone | Post-action review within 24 hours |

---

## 4. How We Operate This Control

### 4.1 Granting Access to New Team Members

**Trigger:** New hire joins Engineering team

**Steps:**
1. CTO or Engineering lead creates Google account for new hire
2. Engineering lead adds user to appropriate GCP IAM role via GCP Console
   - Standard engineers: `roles/logging.viewer` (read-only log access)
3. Engineering lead adds user to GitHub organization with appropriate repository access
4. [ASSUMPTION: Access grant is communicated via Slack/email - no formal ticket]

**Tools:** GCP Console (IAM), GitHub org settings

**Automation:** None - this is a manual process

### 4.2 Production Deployment Authorization

Production deployments require approval from personnel with appropriate access levels:
- 2 Engineering team members (code review)
- 1 QA team member (release readiness)
- 1 Executive: CTO, Head of Product, or CEO (deployment authorization)

For full deployment workflow details, see [Deployment & Release Management](deployment-control.md).

### 4.3 GCP IAM Role Changes (Maker-Checker Process)

**Principle:** All GCP IAM changes require dual authorization from CTO and CEO. One proposes (maker), the other approves (checker).

**Who Can Make Changes:**
- Only CTO and CEO have GCP IAM Admin privileges
- No other personnel can modify IAM roles or permissions
- Engineering team has read-only access (logs only)

#### 4.3.1 Standard IAM Change Process

**Trigger:** Need to grant, modify, or revoke GCP access

**Steps:**

1. **Request (Maker):**
   - CTO or CEO identifies the IAM change needed
   - Maker documents the change request via email to the other party:
     - User/service account affected
     - Current permissions (if any)
     - Requested permissions
     - Business justification
     - Duration (permanent or temporary with end date)

2. **Approval (Checker):**
   - The other executive (CEO if CTO is maker, CTO if CEO is maker) reviews the request
   - Checker verifies:
     - Business justification is valid
     - Permissions follow least privilege principle
     - Change is appropriate for the user's role
   - Checker responds via email with explicit approval or rejection

3. **Implementation:**
   - After receiving email approval, maker implements the change in GCP Console
   - Maker sends confirmation email with:
     - Screenshot of the change (IAM page showing new permissions)
     - Timestamp of change
     - Reference to approval email

4. **Logging:**
   - Email thread serves as authorization record
   - GCP Cloud Audit Logs automatically capture the technical change
   - Both records retained for audit purposes

**Example Email Flow:**

```
From: CTO
To: CEO
Subject: [IAM Request] Grant logging access to new engineer John Smith

Request:
- User: john.smith@dash.hk
- Current: No GCP access
- Requested: roles/logging.viewer
- Justification: New hire on Engineering team, needs log access for troubleshooting
- Duration: Permanent (until employment ends)

Please approve or reject.
---

From: CEO
To: CTO
Subject: RE: [IAM Request] Grant logging access to new engineer John Smith

Approved. Please proceed.
---

From: CTO
To: CEO
Subject: RE: [IAM Request] Grant logging access to new engineer John Smith

Implemented at 2026-01-30 14:30 HKT.
[Screenshot attached showing IAM role assignment]
```

#### 4.3.2 IAM Change Categories

| Change Type | Maker | Checker | Notes |
|-------------|-------|---------|-------|
| New user access (read-only) | CTO or CEO | The other | Standard process |
| New user access (write/admin) | CTO or CEO | The other | Extra scrutiny on justification |
| Permission upgrade | CTO or CEO | The other | Document why upgrade needed |
| Permission revocation | CTO or CEO | The other | Immediate for terminations |
| Service account creation | CTO or CEO | The other | Document intended use |
| Service account permission change | CTO or CEO | The other | Review service scope |
| Role/policy modification | CTO or CEO | The other | Impact assessment required |

#### 4.3.3 Emergency IAM Changes (Break-Glass)

**When Applicable:**
- Security incident requiring immediate access revocation
- Production outage requiring emergency access grant
- Other executive unavailable and delay would cause harm

**Process:**
1. CTO or CEO makes the emergency change unilaterally
2. Maker documents the change immediately via email (even if other party unavailable)
3. Within 24 hours: Post-action review with the other executive
4. Email confirmation that emergency change was reviewed and ratified (or rolled back)

**Emergency Email Template:**

```
Subject: [EMERGENCY IAM] Immediate access revocation - suspected compromise

Action Taken:
- User: jane.doe@dash.hk
- Change: All GCP access revoked
- Time: 2026-01-30 09:15 HKT
- Reason: Suspected credential compromise, immediate action required

This was an emergency action. Please review within 24 hours.
```

### 4.4 Infrastructure Changes (IaC)

**Trigger:** Infrastructure modification needed (non-IAM)

**Steps:**
1. Engineer submits PR with infrastructure changes
2. Same approval process as production deployment (2 engineers + QA + executive)
3. GitHub Actions applies infrastructure changes

**Tools:** GitHub, GitHub Actions, GCP

**Automation:** Infrastructure changes applied via GitHub Actions

**Note:** IAM changes are NOT made via IaC. All IAM changes follow the maker-checker process in Section 4.3.

### 4.5 Quarterly Access Review

**Trigger:** Quarterly cycle (calendar reminder)

**Steps:**
1. CTO exports current GCP IAM user list from GCP Console
2. CTO compares against current employee roster
3. CTO removes access for any departed employees
4. CTO verifies access levels are appropriate for current roles
5. Review completion recorded in Notion

**Tools:** GCP Console, HR roster, Notion

**Automation:** None - manual review

### 4.6 Sprint Sign-Off

**Trigger:** End of sprint

**Steps:**
1. QA reviews completed work in sprint
2. QA signs off on release readiness in sprint meeting
3. Sign-off recorded in Notion

**Tools:** Notion

---

## 5. Operational Guarantees

When this control operates correctly:

- [ ] No one outside Engineering team has write access to GCP infrastructure
- [ ] All production deployments require approval from 2 engineers + QA + 1 executive
- [ ] All deployments are executed via GitHub Actions (no manual deployments)
- [ ] All deployment approvals are logged in GitHub PR history
- [ ] Access is reviewed quarterly by CTO
- [ ] **Only CTO and CEO can modify GCP IAM roles and permissions**
- [ ] **All IAM changes (except emergencies) require maker-checker approval via email**
- [ ] **IAM change requests include user, permissions, and business justification**
- [ ] **IAM change approvals are documented in email with explicit approval**
- [ ] **Emergency IAM changes are reviewed within 24 hours**

---

## 6. Evidence Produced

| Evidence Type | Description | System/Tool | Retention | Collection | Owner |
|--------------|-------------|-------------|-----------|------------|-------|
| PR approval history | Who approved each deployment | GitHub | Indefinite (GitHub default) | Automatic | Engineering |
| GitHub Actions logs | Deployment execution details | GitHub Actions | 90 days | Automatic | Engineering |
| GCP Audit Logs | IAM changes, resource access | GCP Cloud Audit Logs | 400 days | Automatic | CTO |
| **IAM change request emails** | Maker request with justification | Email (Microsoft 365) | Indefinite | Manual | CTO/CEO |
| **IAM change approval emails** | Checker approval/rejection | Email (Microsoft 365) | Indefinite | Manual | CTO/CEO |
| **IAM change confirmation emails** | Implementation confirmation with screenshot | Email (Microsoft 365) | Indefinite | Manual | CTO/CEO |
| Sprint sign-off | QA approval per sprint | Notion | Indefinite | Manual | QA |
| Access review records | Quarterly review completion | Notion | Indefinite | Manual | CTO |

### Evidence Retrieval

- **IAM change authorization:** Search email for subject line `[IAM Request]` or `[EMERGENCY IAM]`
- **Technical IAM changes:** GCP Console > Logging > Filter: `protoPayload.methodName=~"SetIamPolicy|CreateServiceAccount"`
- **Correlation:** Match email timestamps with GCP Audit Log timestamps to verify authorized changes

---

## 7. Exceptions & Edge Cases

### Known Exceptions

| Exception | Justification | Compensating Control | Review Date |
|-----------|--------------|---------------------|-------------|
| CTO/CEO have IAM Admin access | Required for access management | Maker-checker process, dual authorization | Quarterly |
| Emergency IAM changes without prior approval | Security incidents, production outages | 24-hour post-action review, email documentation | Per incident |

### Exception Process

1. Exception requests go to CTO
2. CTO evaluates risk and documents justification
3. Approved exceptions logged in Notion
4. Exceptions reviewed quarterly

### Edge Cases

- **Emergency IAM change (other executive unavailable):** CTO or CEO may act alone; must document via email and review within 24 hours
- **Both CTO and CEO unavailable:** No IAM changes until one returns (except automated service account operations)
- **Disagreement between CTO and CEO:** Delay change until consensus; if urgent, CEO decision is final with documented rationale
- **Contractor access:** See Third-Party Risk Management control for contractor access process (contractors never get GCP IAM access)

---

## 8. Review & Maintenance

**Review Schedule:**
- **Frequency:** Quarterly
- **Next Review:** 2025-04-27
- **Reviewer:** CTO

**Update Triggers:**
- New team members join or leave
- New systems added to scope
- Changes to approval workflow
- Security incidents related to access

**Change History:**

| Date | Change | Author |
|------|--------|--------|
| 2025-01-27 | Initial document created | [Author] |
| 2026-01-29 | Added GCP IAM inherited controls for serverless workloads | [Author] |
| 2026-01-30 | v1.2: Added **GCP IAM maker-checker process** (Section 4.3) - only CTO/CEO can modify IAM; all changes require dual authorization via email; emergency break-glass with 24-hour review; added IAM change evidence types | [Author] |

---

## 9. Related Controls

| Control Area | Relationship |
|-------------|--------------|
| Deployment & Release Management | Implements deployment workflow using access levels defined here |
| Logging & Monitoring | GCP Audit Logs provide immutable record of IAM changes |
| Incident Response | Access revocation is part of incident handling; emergency IAM process applies |
| Third-Party Risk Management | Contractor access defined there (code only, no GCP IAM access) |
| Physical Security | Physical office access complements digital access |
| Security Standards & Exception Governance | IAM exceptions follow the exception governance process |
| Admin Portal Access Control | User access to Admin Portal follows similar dual-control principles |
