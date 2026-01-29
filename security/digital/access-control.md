# Access Control & Identity Management

**Owner:** CTO
**Version:** 1.1
**Last Reviewed:** 2026-01-29
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
| CTO/CEO | [ASSUMPTION: Full admin access for break-glass scenarios] | Admin | Approval authority |

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

### 4.3 Infrastructure Changes

**Trigger:** Infrastructure modification needed

**Steps:**
1. Engineer submits PR with infrastructure changes
2. Same approval process as production deployment (2 engineers + QA + executive)
3. GitHub Actions applies infrastructure changes

**Tools:** GitHub, GitHub Actions, GCP

**Automation:** Infrastructure changes applied via GitHub Actions

### 4.4 Quarterly Access Review

**Trigger:** Quarterly cycle (calendar reminder)

**Steps:**
1. CTO exports current GCP IAM user list from GCP Console
2. CTO compares against current employee roster
3. CTO removes access for any departed employees
4. CTO verifies access levels are appropriate for current roles
5. Review completion recorded in Notion

**Tools:** GCP Console, HR roster, Notion

**Automation:** None - manual review

### 4.5 Sprint Sign-Off

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

---

## 6. Evidence Produced

| Evidence Type | Description | System/Tool | Retention | Collection | Owner |
|--------------|-------------|-------------|-----------|------------|-------|
| PR approval history | Who approved each deployment | GitHub | [ASSUMPTION: GitHub retention default - verify] | Automatic | Engineering |
| GitHub Actions logs | Deployment execution details | GitHub Actions | [ASSUMPTION: 90 days default - verify] | Automatic | Engineering |
| GCP Audit Logs | IAM changes, resource access | GCP Cloud Audit Logs | [ASSUMPTION: 400 days - verify your config] | Automatic | CTO |
| Sprint sign-off | QA approval per sprint | Notion | [ASSUMPTION: Indefinite] | Manual | QA |
| Access review records | Quarterly review completion | Notion | [ASSUMPTION: Indefinite] | Manual | CTO |

### Evidence Gaps

- **Access provisioning:** No ticket/audit trail when access is granted. Consider logging access grants in Notion or a ticket system.
- **Retention periods:** Need to verify actual retention settings in GitHub and GCP.

---

## 7. Exceptions & Edge Cases

### Known Exceptions

| Exception | Justification | Compensating Control | Review Date |
|-----------|--------------|---------------------|-------------|
| [ASSUMPTION: CTO/CEO have elevated access] | Break-glass for emergencies | Actions logged in GCP Audit Logs | Quarterly |

### Exception Process

1. Exception requests go to CTO
2. CTO evaluates risk and documents justification
3. Approved exceptions logged in Notion
4. Exceptions reviewed quarterly

### Edge Cases

- **Emergency deployment (all approvers unavailable):** [ASSUMPTION: Not yet defined - recommend documenting a break-glass process]
- **Contractor access:** See Third-Party Risk Management control for contractor access process

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

---

## 9. Related Controls

| Control Area | Relationship |
|-------------|--------------|
| Deployment & Release Management | Implements deployment workflow using access levels defined here |
| Logging & Monitoring | GCP logs provide audit trail for this control |
| Incident Response | Access revocation is part of incident handling |
| Third-Party Risk Management | Contractor access defined there (code only, no infra) |
| Physical Security | Physical office access complements digital access |
