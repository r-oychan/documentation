# Business Continuity & Disaster Recovery

**Owner:** CTO
**Version:** 1.1
**Last Reviewed:** 2026-01-29
**Review Cadence:** Quarterly

---

## 1. Purpose

We maintain backups and recovery capabilities to ensure business operations can continue after data loss, system failure, or disaster. This control ensures we can restore services and data within acceptable timeframes.

**Risk Reduced:**
- Permanent data loss
- Extended service outages
- Inability to recover from system failures
- Loss of source code and business logic

**Stakeholders:**
- Customers/Merchants (service availability, data integrity)
- Engineering Team (system recovery)
- Business (operational continuity)

---

## 2. Scope

### In Scope
- **Data:** Production databases (DASH, Kraken), configuration data
- **Code:** All source code repositories
- **Systems:** GCP-hosted infrastructure
- **Environments:** Production

### Out of Scope
- Development/QA environment data (can be recreated)
- Third-party SaaS data (Notion, Slack - vendor responsibility)
- Local developer workstations

---

## 3. Roles & Responsibilities

| Role | Team/Individual | Responsibility |
|------|-----------------|----------------|
| Control Owner | CTO | Sets backup policy, approves recovery procedures |
| Operator | Engineering Team | Monitors backups, executes recovery when needed |
| Recovery Lead | CTO or Senior Engineer | Coordinates recovery during incidents |

---

## 4. How We Operate This Control

### 4.1 Backup Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Production Data                          │
└──────────────────────────┬──────────────────────────────────┘
                           │
         ┌─────────────────┼─────────────────┐
         │                 │                 │
         ▼                 ▼                 ▼
┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
│   Databases     │ │   Source Code   │ │  Infrastructure │
│                 │ │                 │ │   Config        │
│ GCP Automated   │ │ GitHub Cloud    │ │ GitHub (IaC)    │
│ Daily Backups   │ │ Storage         │ │                 │
└─────────────────┘ └─────────────────┘ └─────────────────┘
```

### 4.2 Database Backups

**Tool:** GCP Automated Backups

**Configuration:**

| Setting | Value |
|---------|-------|
| Frequency | Daily (automated) |
| Retention | [ASSUMPTION: 7 days - verify GCP config] |
| Type | Full backup |
| Location | [ASSUMPTION: Same region + cross-region copy] |
| Encryption | GCP-managed encryption at rest |

**Backup Process:**
1. GCP automatically triggers daily backup
2. Backup stored in GCP Cloud Storage
3. Backup encrypted at rest
4. Old backups automatically deleted per retention policy

**Monitoring:**
- [ASSUMPTION: Backup success/failure alerts configured]
- Engineering reviews backup status [ASSUMPTION: Weekly]

### 4.3 Source Code Protection

**Tool:** GitHub (Cloud-hosted)

**Protection Measures:**

| Measure | Implementation |
|---------|---------------|
| Storage | GitHub cloud infrastructure (redundant) |
| Access | Authenticated access only |
| History | Full Git history preserved |
| Branches | Protected branches prevent accidental deletion |

**Recovery Capability:**
- Any developer can clone full repository
- Git history allows recovery to any point in time
- GitHub maintains infrastructure redundancy

### 4.4 Infrastructure as Code

**Tool:** GitHub repository

**What's Stored:**
- GCP infrastructure definitions
- Firewall rules
- VPC configurations
- Deployment configurations

**Recovery Capability:**
- Infrastructure can be recreated from code
- Configuration changes tracked in Git history

### 4.5 Recovery Procedures

**Database Recovery:**

| Scenario | Procedure | RTO | RPO |
|----------|-----------|-----|-----|
| Data corruption | Restore from latest backup | [ASSUMPTION: 4 hours] | 24 hours (daily backup) |
| Accidental deletion | Restore specific tables | [ASSUMPTION: 2 hours] | 24 hours |
| Full database loss | Restore full backup | [ASSUMPTION: 4 hours] | 24 hours |

**Steps for Database Recovery:**
1. Incident identified (see Incident Response)
2. CTO or Senior Engineer authorizes recovery
3. Engineer identifies appropriate backup point
4. Restore initiated via GCP Console
5. Data integrity verified
6. Application reconnected
7. Recovery documented

**Application Recovery:**

| Scenario | Procedure | RTO |
|----------|-----------|-----|
| Code deployment issue | Rollback via GitHub Actions | [ASSUMPTION: 15 minutes] |
| Full application failure | Redeploy from GitHub | [ASSUMPTION: 1 hour] |

### 4.6 Recovery Testing

**Frequency:** [ASSUMPTION: Annually or after major changes]

**Test Scope:**
- Restore database backup to test environment
- Verify data integrity
- Document recovery time

**Documentation:**
- Test results recorded in [ASSUMPTION: Notion]
- Issues identified added to remediation backlog

---

## 5. Operational Guarantees

When this control operates correctly:

- [ ] Production databases are backed up daily (automated by GCP)
- [ ] Database backups are retained for at least [ASSUMPTION: 7 days]
- [ ] All source code is stored in GitHub (cloud-hosted, redundant)
- [ ] Infrastructure configuration is stored as code in GitHub
- [ ] Database can be restored within [ASSUMPTION: 4 hours] (RTO)
- [ ] Maximum data loss is 24 hours (RPO - daily backup)

---

## 6. Evidence Produced

| Evidence Type | Description | System/Tool | Retention | Collection | Owner |
|--------------|-------------|-------------|-----------|------------|-------|
| Backup logs | Daily backup completion | GCP Cloud SQL / Backup | [ASSUMPTION: 90 days] | Automatic | Engineering |
| Backup inventory | List of available backups | GCP Console | Point-in-time | Manual | Engineering |
| Recovery test results | Annual test documentation | [ASSUMPTION: Notion] | Indefinite | Manual | CTO |
| Git history | Code change history | GitHub | Indefinite | Automatic | Engineering |

### Evidence Retrieval

- **Backup status:** GCP Console > SQL > Backups
- **Backup logs:** GCP Console > Logging > Filter by backup operations
- **Code history:** GitHub > Repository > Commits

---

## 7. Exceptions & Edge Cases

### Known Exceptions

| Exception | Justification | Compensating Control | Review Date |
|-----------|--------------|---------------------|-------------|
| Dev/QA not backed up | Can be recreated from production schema | Production backups available | Quarterly |

### Exception Process

1. Exception requests go to CTO
2. CTO evaluates business impact
3. Approved exceptions documented
4. Reviewed quarterly

### Edge Cases

- **GCP region outage:** [ASSUMPTION: Cross-region backups enable recovery in alternate region]
- **GitHub outage:** Local clones on developer machines provide temporary access. Wait for GitHub recovery.
- **Corrupted backup:** Restore from previous day's backup. Investigate corruption cause.
- **Recovery during incident:** Follow Incident Response process. Recovery Lead coordinates.

---

## 8. Review & Maintenance

**Review Schedule:**
- **Frequency:** Quarterly
- **Next Review:** 2025-04-27
- **Reviewer:** CTO

**Update Triggers:**
- Changes to backup configuration
- Recovery test findings
- New systems added requiring backup
- RTO/RPO requirements change

**Change History:**

| Date | Change | Author |
|------|--------|--------|
| 2025-01-27 | Initial document created | [Author] |

---

## 9. Related Controls

| Control Area | Relationship |
|-------------|--------------|
| Incident Response | Recovery procedures invoked during incidents |
| Access Control & Identity Management | Recovery access requires appropriate permissions |
| Secure Development & Data Protection | Backup encryption aligned with data protection |
| Network Security | Backup storage protected by GCP security |
| Third-Party Risk Management | GitHub and GCP are critical vendors for recovery |
| Logging & Monitoring | Backup completion logs captured in Cloud Logging |
| Security Standards & Exception Governance | RTO/RPO exceptions follow governance process |
