# Secure Development & Data Protection

**Owner:** CTO
**Version:** 1.1
**Last Reviewed:** 2026-01-29
**Review Cadence:** Quarterly

---

## 1. Purpose

We design and build software with security and privacy at the core. This control ensures that security considerations are embedded from design through deployment, sensitive data is protected, and vulnerabilities are caught before reaching production.

**Risk Reduced:**
- Security flaws introduced during development
- Credential leakage in logs or code
- Unauthorized access to cardholder data
- Data exposure from poor architecture decisions

**Stakeholders:**
- Engineering team (implements secure code)
- QA team (validates security in testing)
- Customers (trust us with payment data)

---

## 2. Scope

### In Scope
- **Systems:** DASH main application, Kraken (payment gateway module)
- **Environments:** All (development, staging, production)
- **Data:** All application data, with special focus on cardholder data (PAN)
- **Processes:** Architecture review, code scanning, encryption, tokenization

### Out of Scope
- Infrastructure security (covered in separate control)
- Third-party gateway security (Soepay, GP) - their responsibility

### System Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                      DASH Main Application                   │
│                    (No PAN - tokens only)                    │
└─────────────────────────────┬───────────────────────────────┘
                              │ Token
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    Kraken (Gateway Module)                   │
│              - Tokenization                                  │
│              - PAN encryption (GCP KMS)                      │
│              - Multi-gateway connectivity                    │
└──────────────┬─────────────────────────────┬────────────────┘
               │                             │
               ▼                             ▼
        ┌───────────┐                 ┌───────────┐
        │  Soepay   │                 │    GP     │
        │ (Offline) │                 │  (MOTO)   │
        └───────────┘                 └───────────┘
```

---

## 3. Roles & Responsibilities

| Role | Team/Individual | Responsibility |
|------|-----------------|----------------|
| Control Owner | CTO | Approves ADRs, sets security standards, owns exceptions |
| ADR Reviewer | Engineering Team | Reviews and approves architecture decisions |
| Developer | Engineering Team | Writes secure code, addresses scan findings |
| QA | QA Team | Validates security in test cycles |

---

## 4. How We Operate This Control

### 4.1 Architecture Design Review (ADR)

**Trigger:** New feature or significant change proposed

**Steps:**
1. Engineer creates ADR document in GitHub (migrating from Notion)
2. ADR must address:
   - Data flow (what data, where stored, who accesses)
   - Security implications
   - Privacy considerations (data minimization)
3. Engineering team reviews ADR
4. At least one senior engineer approves before implementation begins
5. ADR linked in related PRs

**Tools:** GitHub (primary), Notion (legacy)

**Automation:** None - manual review process

### 4.2 Code Scanning (Automated)

**Trigger:** Every pull request

**Tools:**
- **SonarQube** - Static code analysis, security vulnerabilities
- **npm audit** - Dependency vulnerability scanning
- **Linting** - Code quality and potential security issues

**Steps:**
1. Developer submits PR
2. GitHub Actions triggers scans automatically
3. Results reported in PR
4. **Critical/High severity:** PR blocked until fixed
5. **Medium/Low severity:** Warning displayed, team decides
6. Developer addresses findings
7. Re-scan on new commits

**Decision Matrix:**

| Severity | Action | Blocker? |
|----------|--------|----------|
| Critical | Must fix before merge | Yes |
| High | Must fix before merge | Yes |
| Medium | Fix or document justification | No |
| Low | Best effort | No |

### 4.3 Credential Protection

**What we prevent:**
- Credentials in code
- Secrets in logs
- PAN in logs or non-Kraken systems

**How:**
1. SonarQube rules detect hardcoded secrets
2. Logging libraries configured to redact sensitive patterns
3. Code review checks for credential handling
4. [ASSUMPTION: Pre-commit hooks for secret detection - verify if implemented]

### 4.4 Data Minimization

**Principle:** Store only what we need to know

**Implementation:**
- DASH main application stores tokens only (no cardholder data)
- Kraken stores only the minimum cardholder data required for gateway submission
- Cardholder data never logged, even in Kraken
- CVV never stored (used only during 3DS authentication, then discarded)

### 4.5 Cardholder Data Storage & Tokenization (Kraken)

**Cardholder Data Stored in Kraken:**

| Data Element | Stored | Encrypted | Justification |
|--------------|--------|-----------|---------------|
| Cardholder Name | Yes | Yes (GCP KMS) | Required by payment gateways |
| PAN (Card Number) | Yes | Yes (GCP KMS) | Required for payment processing |
| Expiration Date | Yes | Yes (GCP KMS) | Required for recurring payments |
| CVV/CVC | **No** | N/A | Used only during 3DS, then discarded |
| PIN / PIN Block | **No** | N/A | Not applicable (online payments only) |
| Track Data | **No** | N/A | Not applicable (no magnetic stripe) |

**Architecture:**
- Kraken is the only component that handles cardholder data
- DASH main app receives tokens, never sees cardholder data
- CVV collected for 3DS verification only, never persisted

**Encryption:**
- All stored cardholder data encrypted using Google Cloud KMS (AES-256-GCM)
- Encryption at field level (not just disk encryption)
- Key rotation: Every 90 days (automated by GCP KMS)
- Key material resides in FIPS 140-2 Level 3 HSMs

**Tokenization Flow:**
1. Cardholder data enters Kraken (name, PAN, expiry, CVV)
2. Kraken performs 3DS authentication using CVV
3. CVV discarded immediately after 3DS completes
4. Kraken encrypts remaining data (name, PAN, expiry) using KMS
5. Kraken generates non-reversible token
6. Token returned to DASH main application
7. DASH stores token only (no cardholder data)
8. When gateway interaction needed, Kraken decrypts and sends to gateway

**Supported Gateways:**
| Gateway | Type | Use Case |
|---------|------|----------|
| Soepay | Offline | Card-present transactions |
| GP | Online MOTO | Card-not-present transactions |

---

## 5. Operational Guarantees

When this control operates correctly:

- [ ] All new features undergo Architecture Design Review before implementation
- [ ] Every PR is scanned by SonarQube and npm audit
- [ ] Critical and High severity vulnerabilities block PR merge
- [ ] PAN exists only in Kraken, never in DASH main application
- [ ] All PAN is encrypted with GCP KMS
- [ ] KMS keys rotate every 90 days
- [ ] No credentials are hardcoded in source code

---

## 6. Evidence Produced

| Evidence Type | Description | System/Tool | Retention | Collection | Owner |
|--------------|-------------|-------------|-----------|------------|-------|
| ADR documents | Architecture decisions | GitHub/Notion | Indefinite | Manual | Engineering |
| SonarQube reports | Code scan results per PR | SonarQube | [ASSUMPTION: 90 days - verify] | Automatic | Engineering |
| npm audit logs | Dependency scan results | GitHub Actions | [ASSUMPTION: 90 days - verify] | Automatic | Engineering |
| PR approval history | Code review records | GitHub | GitHub default | Automatic | Engineering |
| KMS key rotation logs | Key rotation events | GCP Cloud Audit Logs | [ASSUMPTION: 400 days - verify] | Automatic | CTO |
| KMS usage logs | Encryption/decryption operations | GCP Cloud Audit Logs | [ASSUMPTION: 400 days - verify] | Automatic | CTO |

### Evidence Retrieval

- **SonarQube findings:** Access SonarQube dashboard, filter by project/date
- **KMS rotation:** GCP Console > Security > Key Management > View rotation history
- **ADRs:** GitHub repository search or Notion workspace

---

## 7. Exceptions & Edge Cases

### Known Exceptions

| Exception | Justification | Compensating Control | Review Date |
|-----------|--------------|---------------------|-------------|
| Kraken stores PAN | Required for gateway interaction | Encryption via KMS, tokenization, isolated module | Quarterly |
| Medium/Low vulns may merge | Prioritization of delivery | Tracked for future fix, documented in PR | Per occurrence |

### Exception Process

1. Developer documents justification in PR
2. Senior engineer or CTO approves exception
3. Exception tracked in GitHub issue for follow-up
4. Reviewed in quarterly security review

### Edge Cases

- **Zero-day in dependency:** Escalate to CTO, emergency patch process
- **SonarQube unavailable:** Manual security review required before merge
- **New gateway integration:** Requires dedicated ADR with security focus

---

## 8. Review & Maintenance

**Review Schedule:**
- **Frequency:** Quarterly
- **Next Review:** 2025-04-27
- **Reviewer:** CTO

**Update Triggers:**
- New payment gateway added
- Changes to Kraken architecture
- New scanning tools adopted
- Security incident related to code or data

**Change History:**

| Date | Change | Author |
|------|--------|--------|
| 2025-01-27 | Initial document created | [Author] |

---

## 9. Related Controls

| Control Area | Relationship |
|-------------|--------------|
| Access Control & Identity Management | Controls who can approve PRs and access Kraken |
| Deployment & Release Management | ADR and PR process is part of change control |
| Vulnerability Management | Scan findings feed into vulnerability tracking |
| Logging & Monitoring | Monitors for anomalous access to encrypted data |
| Network Security | Kraken VPC isolation and firewall rules defined there |
| Third-Party Risk Management | Contractor code passes through same scanning |
| System Component Inventory | Tracks application stack and dependencies |
| Security Policy & Awareness | Engineering security training (OWASP, secure coding) defined there |
| Security Standards & Exception Governance | Development practice exceptions follow governance process |
