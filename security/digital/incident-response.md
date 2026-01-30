# Incident Response

**Owner:** CTO
**Version:** 1.1
**Last Reviewed:** 2026-01-29
**Review Cadence:** Quarterly

---

## 1. Purpose

We detect, respond to, and resolve production incidents to minimize business impact and restore normal operations. This control ensures incidents are handled consistently, communicated clearly, and documented for learning.

**Risk Reduced:**
- Prolonged service outages affecting customers
- Financial loss from unresolved payment issues
- Poor communication during incidents eroding trust
- Repeat incidents due to lack of documentation

**Stakeholders:**
- Customers/Merchants (service availability)
- CS Team (customer communication, incident reporters)
- Engineering Team (incident resolution)
- Business Leadership (impact awareness)

---

## 2. Scope

### In Scope
- **Systems:** All DASH production applications (DASH Main, Kraken, Admin Portal)
- **Environments:** Production
- **Incident Sources:** CS reports, system alerts, internal discovery
- **Incident Types:** Service outages, payment failures, security events, data issues

### Out of Scope
- Development/QA environment issues (handled via standard bug process)
- Third-party outages outside our control (Soepay, GP) - we monitor and communicate but cannot resolve
- Feature requests or non-urgent bugs

---

## 3. Roles & Responsibilities

| Role | Team/Individual | Responsibility |
|------|-----------------|----------------|
| Control Owner | CTO | Sets incident process, reviews post-incident, approves hotfixes |
| Incident Manager | Selected Engineer | Coordinates response, maintains communication with business/impacted parties |
| Responder | Engineering Team | Investigates and implements fix |
| QA Approver | QA Team | Tests hotfix, signs off before and after production deployment |
| Executive Approver | CTO / Head of Product / CEO | Approves hotfix for production deployment |
| Reporter | CS Team / Alerts / Anyone | Identifies and reports incident |

---

## 4. How We Operate This Control

### 4.1 Incident Detection

**Sources:**

| Source | How Detected | Initial Handler |
|--------|--------------|-----------------|
| CS Report | Customer complaint via CS team | CS escalates to Engineering |
| System Alert | GCP Log Explorer alert → Teams + Email | On-call Engineer |
| Internal Discovery | Engineer notices issue | Discovering Engineer |

### 4.2 Incident Classification

**Classify by Business Impact:**

| Severity | Criteria | Response Time | Examples |
|----------|----------|---------------|----------|
| **S1 - Critical** | Most users/merchants impacted AND/OR money-related | Immediate hotfix | Payment processing down, data breach, funds miscalculated |
| **S2 - High** | Significant user impact, workaround may exist | Same day | Major feature broken, partial outage |
| **S3 - Medium** | Limited impact, workaround available | Next business day | Minor feature broken, performance degradation |
| **S4 - Low** | Minimal impact | Scheduled fix | Cosmetic issues, edge cases |

**Classification Decision:**

```
Is the incident impacting most users/merchants?
    └── YES → Is it money-related (payments, refunds, funds)?
                  └── YES → S1 (Critical) → Initiate Hotfix
                  └── NO  → S1 (Critical) → Initiate Hotfix
    └── NO  → Is it money-related?
                  └── YES → S1 (Critical) → Initiate Hotfix
                  └── NO  → S2/S3/S4 based on scope → Standard process
```

**Key Rule:** If it impacts most users/merchants OR involves money → S1 → Hotfix process

### 4.3 Incident Response Workflow (S1 - Critical)

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│  Detection  │───▶│  Classify   │───▶│  Assign IM  │───▶│  Hotfix     │
│  (CS/Alert/ │    │  (S1-S4)    │    │  (Incident  │    │  Development│
│   Internal) │    │             │    │   Manager)  │    │             │
└─────────────┘    └─────────────┘    └─────────────┘    └──────┬──────┘
                                                                │
┌─────────────┐    ┌─────────────┐    ┌─────────────┐           │
│  Document   │◀───│  Merge to   │◀───│  Verify on  │◀───┬──────┘
│  in Notion  │    │  Lower Env  │    │  Production │    │
│             │    │             │    │  (QA)       │    │
└─────────────┘    └─────────────┘    └─────────────┘    │
                                                         │
                                      ┌─────────────┐    │
                                      │  Deploy to  │◀───┤
                                      │  Production │    │
                                      └─────────────┘    │
                                                         │
                                      ┌─────────────┐    │
                                      │  Approval   │◀───┘
                                      │  (QA + Exec)│
                                      └─────────────┘
```

**Step-by-Step:**

1. **Detection & Classification**
   - Incident reported via CS, alert, or internal discovery
   - Engineer classifies severity based on business impact
   - If S1 → proceed with hotfix process

2. **Assign Incident Manager**
   - Senior engineer or CTO assigns an Incident Manager
   - Incident Manager responsibilities:
     - Coordinate technical response
     - Communicate status to business stakeholders
     - Update impacted parties (CS relays to customers if needed)
     - Track timeline and actions

3. **Hotfix Development**
   - Engineer creates hotfix branch from production
   - Minimal fix focused on resolving the incident
   - Engineer tests locally

4. **Hotfix Approval**
   - QA tests hotfix in available environment
   - QA signs off on fix
   - Executive approval required: **CTO or Head of Product or CEO**
   - Approvals documented in PR or Notion ticket

5. **Deploy to Production**
   - Hotfix deployed via GitHub Actions (follows Deployment & Release Management)
   - Incident Manager monitors deployment

6. **Production Verification**
   - QA performs verification testing on production
   - Confirms incident is resolved
   - Incident Manager confirms with business stakeholders

7. **Merge Back to Lower Environments**
   - Hotfix branch merged back to main/development branches
   - Ensures Dev and QA environments have the fix

8. **Documentation**
   - Incident documented in Notion ticket
   - Include: timeline, root cause, fix applied, impact, follow-up actions

### 4.4 Communication During Incident

**Incident Manager Responsibilities:**

| Audience | Channel | Frequency | Content |
|----------|---------|-----------|---------|
| Engineering Team | Teams | Real-time | Technical updates, task assignments |
| Business Stakeholders | Teams/Email | Every 30 min for S1 | Status, ETA, impact summary |
| CS Team (for customers) | Teams | As needed | Customer-facing messaging guidance |

**Communication Template (S1):**

```
INCIDENT UPDATE - [Severity] - [Brief Description]
Status: [Investigating / Fix in Progress / Deployed / Resolved]
Impact: [What is affected]
ETA: [Estimated resolution time]
Next Update: [Time]
```

### 4.5 Non-Critical Incidents (S2-S4)

- Logged in Notion as bug/issue
- Prioritized in regular sprint planning
- No hotfix process required
- Standard deployment process applies

### 4.6 Post-Incident Report & Review

**Trigger:** After every S1 incident (and optionally S2 incidents with significant impact)

**Timeline:**
- Post-Incident Report created within 3 business days of resolution
- Review meeting held within 5 business days of resolution

**Step-by-Step:**

1. **Create Post-Incident Report**
   - Incident Manager creates report in Notion (Incident Reports database)
   - Report must be completed before review meeting

2. **Post-Incident Report Contents**

   | Section | Description |
   |---------|-------------|
   | **Incident Summary** | Brief description of what happened |
   | **Severity & Impact** | Classification (S1-S4), affected users/merchants, financial impact if any |
   | **Timeline** | Chronological list of events from detection to resolution |
   | **Root Cause** | Technical root cause of the incident |
   | **Resolution** | What fix was applied, how it was deployed |
   | **What Went Well** | Aspects of response that worked effectively |
   | **What Could Improve** | Areas for process or technical improvement |
   | **Follow-Up Actions** | Specific action items to prevent recurrence (with owners and due dates) |
   | **Participants** | List of people involved in incident response |

3. **Conduct Review Meeting**
   - Attendees: Incident Manager, responders, CTO, affected stakeholders
   - Walk through the Post-Incident Report
   - Discuss improvements and assign follow-up action owners
   - CTO approves final report

4. **Store and Track**
   - Final report stored in Notion (Incident Reports database)
   - Follow-up actions tracked in Notion until completion
   - Link report to original incident ticket

**Post-Incident Report Template (Notion):**

```
# Post-Incident Report: [Incident Title]

**Date:** [Incident Date]
**Severity:** [S1/S2/S3/S4]
**Incident Manager:** [Name]
**Report Author:** [Name]
**Report Date:** [Date report created]

## Incident Summary
[2-3 sentence summary]

## Impact
- Users/Merchants affected: [Number/Description]
- Duration: [Start time] to [End time] ([X] hours/minutes)
- Financial impact: [If applicable]

## Timeline
| Time | Event |
|------|-------|
| [Time] | [What happened] |
| ... | ... |

## Root Cause
[Technical explanation of what caused the incident]

## Resolution
[What fix was applied and how]

## What Went Well
- [Item 1]
- [Item 2]

## What Could Improve
- [Item 1]
- [Item 2]

## Follow-Up Actions
| Action | Owner | Due Date | Status |
|--------|-------|----------|--------|
| [Action item] | [Name] | [Date] | [Pending/Complete] |

## Participants
- [Name] - [Role in incident]
```

---

## 5. Operational Guarantees

When this control operates correctly:

- [ ] All S1 incidents trigger the hotfix process
- [ ] Every S1 incident has an assigned Incident Manager
- [ ] All hotfixes are approved by QA and an executive (CTO/Head of Product/CEO)
- [ ] All hotfixes are verified on production by QA after deployment
- [ ] All hotfix branches are merged back to lower environments
- [ ] All incidents are documented in Notion with timeline and resolution
- [ ] Business stakeholders receive regular updates during S1 incidents
- [ ] All S1 incidents have a Post-Incident Report created within 3 business days
- [ ] All Post-Incident Reports are stored in Notion with root cause and follow-up actions
- [ ] All follow-up actions are tracked to completion

---

## 6. Evidence Produced

| Evidence Type | Description | System/Tool | Retention | Collection | Owner |
|--------------|-------------|-------------|-----------|------------|-------|
| Incident tickets | Initial incident record during response | Notion | Indefinite | Manual | Incident Manager |
| **Post-Incident Reports** | Full analysis with root cause, timeline, and follow-ups | Notion | Indefinite | Manual | Incident Manager |
| Hotfix PR history | Code changes, approvals | GitHub | Indefinite | Automatic | Engineering |
| Deployment logs | Hotfix deployment execution | GitHub Actions | 90 days | Automatic | Engineering |
| Communication logs | Status updates during incident | Teams | [ASSUMPTION: Teams retention policy] | Automatic | Incident Manager |
| Follow-up action tracking | Actions to prevent recurrence | Notion | Indefinite | Manual | Action Owners |

### Evidence Retrieval

- **Incident history:** Notion > Incident Tickets database
- **Post-Incident Reports:** Notion > Incident Reports database (linked to incident tickets)
- **Hotfix approvals:** GitHub > PR with `[HOTFIX]` label
- **Deployment logs:** GitHub Actions > Filter by hotfix workflow runs
- **Follow-up actions:** Notion > Incident Reports > Follow-Up Actions table

---

## 7. Exceptions & Edge Cases

### Known Exceptions

| Exception | Justification | Compensating Control | Review Date |
|-----------|--------------|---------------------|-------------|
| None currently | - | - | - |

### Exception Process

1. Exception requests go to CTO
2. CTO evaluates risk
3. Approved exceptions documented in incident ticket
4. Reviewed in post-incident review

### Edge Cases

- **All executives unavailable for S1 approval:** Engineering Lead may approve with immediate notification to all three executives. Document in incident ticket.
- **Incident during off-hours:** On-call engineer initiates response, contacts Incident Manager candidates until one is available.
- **Third-party outage (Soepay/GP):** Document as incident, communicate to stakeholders, but resolution depends on third party. Track their updates.
- **Multiple S1 incidents simultaneously:** CTO assigns separate Incident Managers. Prioritize by financial impact.

---

## 8. Review & Maintenance

**Review Schedule:**
- **Frequency:** Quarterly
- **Next Review:** 2025-04-27
- **Reviewer:** CTO

**Update Triggers:**
- Post-incident review identifies process gaps
- Changes to approval authority
- New systems added to scope
- Significant incident volume change

**Change History:**

| Date | Change | Author |
|------|--------|--------|
| 2025-01-27 | Initial document created | [Author] |
| 2026-01-29 | Added Post-Incident Report process and template | [Author] |

---

## 9. Related Controls

| Control Area | Relationship |
|-------------|--------------|
| Deployment & Release Management | Hotfix deployment follows deployment control (expedited) |
| Logging & Monitoring | Alerts trigger incident detection; logs used for investigation |
| Access Control & Identity Management | Incident may require emergency access changes |
| Admin Portal Access Control | CS uses Admin Portal to verify customer impact |
| Business Continuity & Disaster Recovery | Data recovery procedures may be invoked |
| Vulnerability Management | Zero-day vulnerabilities may trigger incidents |
| Network Security | Network incidents handled per this process |
| Security Policy & Awareness | Incident reporting training for all staff (Module 7) |
| Security Standards & Exception Governance | Post-incident may trigger exception reviews |
| Cryptographic Key Management | Key compromise handled via incident process |
