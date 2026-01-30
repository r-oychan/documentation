# System Component Inventory

**Owner:** CTO
**Version:** 1.0
**Last Reviewed:** 2026-01-30
**Review Cadence:** Quarterly (Inventory), Per Build (Dependencies)

---

## 1. Purpose

We maintain an inventory of all system components, software, and third-party dependencies to enable effective vulnerability management, patch management, and change control. This inventory ensures we know exactly what is running in production and can respond quickly to security advisories.

**Risk Reduced:**
- Unknown vulnerable components in production
- Delayed response to security advisories (CVEs)
- Untracked software creating compliance gaps
- Shadow IT and unapproved dependencies

**Stakeholders:**
- Engineering Team (maintains inventory, responds to vulnerabilities)
- CTO (owns inventory accuracy, approves new components)
- QSA/Auditors (requires inventory for compliance validation)

---

## 2. Scope

### In Scope
- **Applications:** DASH Main, Kraken (payment module), Admin Portal
- **Infrastructure:** GCP Cloud Run, Cloud Functions, Cloud SQL
- **Dependencies:** All npm packages (third-party libraries)
- **Databases:** PostgreSQL (Cloud SQL)
- **Environments:** Production, QA, Development

### Out of Scope
- GCP managed infrastructure (Google's responsibility)
- Third-party SaaS tools (Notion, GitHub, Sentry) - covered in Third-Party Risk Management
- Payment gateway internal systems (Soepay, GP)

---

## 3. Roles & Responsibilities

| Role | Team/Individual | Responsibility |
|------|-----------------|----------------|
| Inventory Owner | CTO | Ensures inventory accuracy, approves new components |
| Inventory Maintainer | Engineering Team | Updates inventory, responds to vulnerabilities |
| Dependency Auditor | CI/CD Pipeline (automated) | Scans dependencies on every build |
| Reviewer | Engineering Lead | Reviews inventory changes quarterly |

---

## 4. How We Operate This Control

### 4.1 Application Inventory

**DASH Production Applications:**

| Application | Purpose | Runtime | PCI Scope | Repository |
|-------------|---------|---------|-----------|------------|
| **DASH Main** | Core platform (ride-hailing, ticketing) | GCP Cloud Run | No (tokens only) | `dash-main` |
| **Kraken** | Payment processing, tokenization | GCP Cloud Run | **Yes** | `kraken` |
| **Admin Portal** | CS operations, transaction management | GCP Cloud Run | No (masked PAN only) | `admin-portal` |
| **Background Jobs** | Scheduled tasks, settlements | GCP Cloud Functions | No | `dash-jobs` |

### 4.2 Technology Stack

**Backend Stack:**

| Layer | Technology | Version | Purpose |
|-------|------------|---------|---------|
| **Runtime** | Node.js | 20.x LTS | JavaScript runtime |
| **Framework** | NestJS | 10.x | Backend framework |
| **ORM** | TypeORM | 0.3.x | Database abstraction |
| **Language** | TypeScript | 5.x | Type-safe JavaScript |

**Database Stack:**

| Component | Technology | Configuration | Purpose |
|-----------|------------|---------------|---------|
| **Primary Database** | PostgreSQL (Cloud SQL) | 1x Read-Write instance | Primary data store |
| **Read Replica** | PostgreSQL (Cloud SQL) | 1x Read-Only instance | Read scaling, reporting |
| **Connection** | Cloud SQL Proxy | Automatic TLS, IAM auth | Secure database connectivity |

**Database Architecture:**

```
┌─────────────────────────────────────────────────────────────────┐
│                        Applications                              │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────────┐  │
│  │  DASH Main  │  │   Kraken    │  │     Admin Portal        │  │
│  └──────┬──────┘  └──────┬──────┘  └───────────┬─────────────┘  │
│         │                │                      │                │
│         └────────────────┼──────────────────────┘                │
│                          │                                       │
│                          ▼                                       │
│              ┌───────────────────────┐                          │
│              │   Cloud SQL Proxy     │                          │
│              │   (Automatic TLS)     │                          │
│              └───────────┬───────────┘                          │
└──────────────────────────┼──────────────────────────────────────┘
                           │
           ┌───────────────┴───────────────┐
           │                               │
           ▼                               ▼
┌─────────────────────┐      ┌─────────────────────┐
│   Cloud SQL (RW)    │      │   Cloud SQL (RO)    │
│   PostgreSQL 15     │─────▶│   PostgreSQL 15     │
│   Primary Instance  │ Sync │   Read Replica      │
│                     │      │                     │
│   • Write queries   │      │   • Read queries    │
│   • Transactions    │      │   • Reporting       │
│   • PAN storage     │      │   • Analytics       │
│     (Kraken only)   │      │                     │
└─────────────────────┘      └─────────────────────┘
```

**Infrastructure Stack:**

| Component | Service | Configuration | Notes |
|-----------|---------|---------------|-------|
| **Compute** | GCP Cloud Run | Fully managed containers | Serverless, auto-scaling |
| **Serverless** | GCP Cloud Functions | Event-driven | Background jobs |
| **Database** | GCP Cloud SQL | PostgreSQL 15 | 1 RW + 1 RO replica |
| **Key Management** | GCP KMS | FIPS 140-2 Level 3 HSM | PAN encryption |
| **Secrets** | GCP Secret Manager | Versioned secrets | API keys, credentials |
| **WAF** | GCP Cloud Armor | OWASP rules | DDoS, attack prevention |
| **Load Balancer** | GCP Cloud Load Balancing | Global, HTTPS | Traffic distribution |
| **Logging** | GCP Cloud Logging | Centralized logs | Audit trail |
| **Monitoring** | GCP Cloud Monitoring | Metrics, alerts | Observability |

### 4.3 Third-Party Dependency Management

**Dependency Source of Truth:**

| Application | Dependency File | Lock File | Location |
|-------------|-----------------|-----------|----------|
| DASH Main | `package.json` | `package-lock.json` | Repository root |
| Kraken | `package.json` | `package-lock.json` | Repository root |
| Admin Portal | `package.json` | `package-lock.json` | Repository root |

**How Dependencies are Tracked:**

1. **Source of Truth:** `package.json` and `package-lock.json` in each repository
2. **Version Pinning:** Lock files ensure reproducible builds
3. **Automated Scanning:** npm audit runs on every PR and build
4. **Vulnerability Blocking:** Critical/High vulnerabilities block merge

**Dependency Audit Process:**

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│  Developer  │────▶│  Create PR  │────▶│   CI/CD     │────▶│  npm audit  │
│  adds/      │     │  with new   │     │  Pipeline   │     │  executes   │
│  updates    │     │  dependency │     │  triggered  │     │             │
│  package    │     │             │     │             │     │             │
└─────────────┘     └─────────────┘     └─────────────┘     └──────┬──────┘
                                                                   │
                    ┌──────────────────────────────────────────────┘
                    │
                    ▼
        ┌───────────────────────┐
        │  Vulnerabilities      │
        │  Found?               │
        └───────────┬───────────┘
                    │
          ┌─────────┴─────────┐
          │                   │
          ▼                   ▼
    ┌───────────┐       ┌───────────┐
    │ Critical/ │       │  Medium/  │
    │ High      │       │  Low      │
    │           │       │           │
    │ PR        │       │ Warning   │
    │ BLOCKED   │       │ displayed │
    └───────────┘       └───────────┘
```

### 4.4 Key Dependencies by Category

**Authentication & Security:**

| Package | Purpose | Audited |
|---------|---------|---------|
| `@nestjs/passport` | Authentication framework | Yes (CI/CD) |
| `passport-jwt` | JWT strategy | Yes (CI/CD) |
| `bcrypt` | Password hashing | Yes (CI/CD) |
| `helmet` | Security headers | Yes (CI/CD) |

**Database & ORM:**

| Package | Purpose | Audited |
|---------|---------|---------|
| `typeorm` | Database ORM | Yes (CI/CD) |
| `pg` | PostgreSQL driver | Yes (CI/CD) |
| `@google-cloud/sql` | Cloud SQL connectivity | Yes (CI/CD) |

**Cloud Integration:**

| Package | Purpose | Audited |
|---------|---------|---------|
| `@google-cloud/kms` | KMS encryption | Yes (CI/CD) |
| `@google-cloud/secret-manager` | Secrets access | Yes (CI/CD) |
| `@google-cloud/logging` | Structured logging | Yes (CI/CD) |

**API & Validation:**

| Package | Purpose | Audited |
|---------|---------|---------|
| `class-validator` | Input validation | Yes (CI/CD) |
| `class-transformer` | Data transformation | Yes (CI/CD) |
| `@nestjs/swagger` | API documentation | Yes (CI/CD) |

**Note:** Full dependency list is maintained in each repository's `package.json`. The above are key security-relevant packages.

### 4.5 Inventory Update Process

**When Inventory Updates:**

| Trigger | Action | Responsibility |
|---------|--------|----------------|
| New application deployed | Add to Application Inventory table | Engineering Lead |
| Major version upgrade | Update Technology Stack table | Developer |
| New dependency added | Automatically tracked in package.json | Developer |
| Dependency removed | Automatically tracked in package.json | Developer |
| Quarterly review | Full inventory validation | CTO |

**Quarterly Review Checklist:**

- [ ] Application inventory matches deployed services
- [ ] Technology versions are current and supported
- [ ] No deprecated packages in use
- [ ] All dependencies scanned in last 90 days
- [ ] No untracked systems discovered

### 4.6 Version Management

**Supported Version Policy:**

| Component | Version Policy | End-of-Life Action |
|-----------|---------------|-------------------|
| Node.js | LTS versions only | Upgrade before EOL |
| PostgreSQL | Current or Current-1 | Upgrade within 6 months of EOL |
| NestJS | Latest stable | Upgrade within 3 months |
| TypeORM | Latest stable | Upgrade within 3 months |
| npm packages | Latest compatible | Update for security fixes |

**Current Version Status:**

| Component | Current Version | EOL Date | Status |
|-----------|-----------------|----------|--------|
| Node.js | 20.x LTS | April 2026 | Active LTS |
| PostgreSQL | 15 | November 2027 | Supported |
| NestJS | 10.x | N/A (latest) | Current |
| TypeORM | 0.3.x | N/A (latest) | Current |

---

## 5. Operational Guarantees

When this control operates correctly:

- [ ] All production applications are listed in the inventory
- [ ] Technology stack versions are documented and current
- [ ] All third-party dependencies are tracked in package.json/lock files
- [ ] Every build triggers automated dependency vulnerability scanning (npm audit)
- [ ] Critical/High vulnerabilities block deployment
- [ ] Inventory is reviewed and validated quarterly
- [ ] No untracked systems exist in production

---

## 6. Evidence Produced

| Evidence Type | Description | System/Tool | Retention | Collection | Owner |
|--------------|-------------|-------------|-----------|------------|-------|
| Dependency manifest | package.json, package-lock.json | Git repository | Indefinite | Automatic | Engineering |
| Vulnerability scan results | npm audit output per build | GitHub Actions | 90 days | Automatic | Engineering |
| PR check history | Pass/fail for dependency scans | GitHub | Indefinite | Automatic | Engineering |
| Inventory document | This document | Git repository | Indefinite | Manual | CTO |
| Quarterly review records | Checklist completion | Notion | Indefinite | Manual | CTO |
| Cloud SQL configuration | Database instances | GCP Console | Real-time | Automatic | Engineering |

### Evidence Retrieval

- **Dependency list:** Repository > `package.json` and `package-lock.json`
- **Vulnerability scans:** GitHub > PR > Checks tab > npm audit results
- **Deployed versions:** GCP Console > Cloud Run > Service > Revisions
- **Database configuration:** GCP Console > SQL > Instances

---

## 7. Exceptions & Edge Cases

### Known Exceptions

| Exception | Justification | Compensating Control | Review Date |
|-----------|--------------|---------------------|-------------|
| Development dependencies not in inventory | Not deployed to production | Scanned by npm audit anyway | Quarterly |

### Exception Process

1. Engineer requests to add non-standard component
2. CTO evaluates security posture
3. If approved:
   - Component added to inventory
   - Scanning configured in CI/CD
   - Security documentation reviewed
4. Reviewed quarterly

### Edge Cases

- **Emergency dependency update:** Follow hotfix process; update inventory within 24 hours
- **Deprecated package:** Track in inventory with EOL date; plan migration
- **Transitive vulnerability:** npm audit catches these; fix via direct dependency update
- **GCP service addition:** Add to Infrastructure Stack table; review security configuration

---

## 8. Review & Maintenance

**Review Schedule:**
- **Frequency:** Quarterly (full inventory); Per build (dependencies)
- **Next Review:** 2026-04-30
- **Reviewer:** CTO

**Update Triggers:**
- New application deployed
- Major version upgrade
- New infrastructure service adopted
- Security incident revealing inventory gap
- Quarterly review cycle

**Change History:**

| Date | Change | Author |
|------|--------|--------|
| 2026-01-30 | Initial document created | [Author] |

---

## 9. Related Controls

| Control Area | Relationship |
|-------------|--------------|
| Vulnerability Management | Inventory enables vulnerability tracking and patching |
| Secure Development & Data Protection | Dependencies scanned as part of secure SDLC |
| Deployment & Release Management | Inventory reflects what gets deployed |
| Third-Party Risk Management | Third-party packages require risk assessment |
| Network Security | Infrastructure inventory defines network boundaries |
| Cryptographic Key Management | KMS is part of infrastructure inventory |

---

## Appendix A: Quick Reference - Production Components

```
DASH Production Environment
├── Applications (Cloud Run)
│   ├── DASH Main (Node.js 20 / NestJS 10 / TypeORM)
│   ├── Kraken (Node.js 20 / NestJS 10 / TypeORM) [PCI SCOPE]
│   └── Admin Portal (Node.js 20 / NestJS 10 / TypeORM)
│
├── Background Jobs (Cloud Functions)
│   └── dash-jobs (Node.js 20)
│
├── Databases (Cloud SQL)
│   ├── Primary (PostgreSQL 15, Read-Write)
│   └── Replica (PostgreSQL 15, Read-Only)
│
├── Security Services
│   ├── Cloud KMS (Encryption keys)
│   ├── Secret Manager (API keys, credentials)
│   ├── Cloud Armor (WAF)
│   └── Cloud SQL Proxy (Secure DB access)
│
└── Dependencies
    └── npm packages (tracked in package.json, scanned per build)
```

---

## Appendix B: Dependency Scanning Commands

**Check vulnerabilities locally:**
```bash
npm audit
```

**Fix vulnerabilities (where possible):**
```bash
npm audit fix
```

**Generate dependency report:**
```bash
npm list --all > dependencies.txt
```

**Check for outdated packages:**
```bash
npm outdated
```

**CI/CD Pipeline Command (GitHub Actions):**
```yaml
- name: Security audit
  run: npm audit --audit-level=high
```
