# Internal Documentation → PCI DSS v4.0.1 Mapping

**Assessment Date:** 2026-01-30
**Company:** DASH - Multi-vertical mobile commerce platform

This document shows how each internal control document maps to PCI DSS requirements.

---

## Company Overview Document

| Document | Purpose |
|----------|---------|
| **[company-overview.md](../../security/company-overview.md)** | Foundational document describing DASH business, payment architecture, tokenization strategy, and PCI scope definition |

This document provides context for all control documents and explains why PCI scope is limited to the Kraken payment module.

---

## Mapping Table

| Internal Document | Control Area | PCI Requirements Covered | Coverage Strength | Notes |
|-------------------|--------------|--------------------------|-------------------|-------|
| **[access-control.md](../../security/digital/access-control.md)** (v1.2) | Access Control & Identity Management | **7.1.1, 7.1.2, 7.2.1-7.2.5.1, 7.3.1-7.3.3**, 8.1.1, 8.1.2, 8.2.1-8.2.8, **8.2.7** (privileged user management), 12.1.3, 12.1.4 | **Strong** | **Req 7 fully compliant:** GCP IAM (infrastructure) + Firebase Auth (Admin Portal), role-based access by job function, quarterly reviews exceeding 6-month requirement, default deny, dedicated service accounts via Workload Identity; **NEW: IAM maker-checker process** - only CTO/CEO can modify IAM, dual authorization via email, emergency break-glass with 24-hour review |
| **[admin-portal-access.md](../../security/digital/admin-portal-access.md)** (v1.2) | Admin Portal Access Control | 3.4.1 (masking), 3.4.2 (partial), **7.2.6** (CHD query restriction), **8.2.1, 8.2.5, 8.2.6, 8.2.8, 8.3.1-8.3.11, 8.4.1-8.4.2, 8.5.1, 8.6.3**, 10.2.1.1, 10.2.1.4 | **Strong** | **Req 8 fully compliant:** MFA + passkey, password requirements, **password history (last 4 in Firestore)**, **15-min session timeout**, **same-day account revocation**, **credential rotation (SQL 90d, KMS 90d)**, **secrets in GitHub Vault/Secret Manager (never in logs/UI)** |
| **[business-continuity.md](../../security/digital/business-continuity.md)** | Business Continuity & Disaster Recovery | 12.10.1 (partial) | **Partial** | Backup procedures; not full BC/DR plan |
| **[cryptographic-key-management.md](../../security/digital/cryptographic-key-management.md)** | Cryptographic Key Management | 3.5.1, 3.5.1.2, 3.5.1.3, 3.6.1, 3.6.1.1-3.6.1.4, 3.7.1-3.7.7 | **Strong** | Full key lifecycle with GCP KMS; HSM protection; 90-day rotation; **Gap:** 3.7.8 key custodian acknowledgment missing |
| **[deployment-control.md](../../security/digital/deployment-control.md)** (v1.1) | Deployment & Release Management | 6.5.1-6.5.4, 6.5.6, 6.2.3.1, 2.2.1 (partial) | **Strong** | Change control with PR workflow, 2 engineer + QA + executive approval, rollback procedures, environment separation (Dev/QA/Prod) |
| **[incident-response.md](../../security/digital/incident-response.md)** | Incident Response | 12.10.1-12.10.6 | **Strong** | Comprehensive IR plan with post-incident review |
| **[logging-monitoring.md](../../security/digital/logging-monitoring.md)** (v1.2) | Logging & Monitoring | **10.1.1, 10.1.2, 10.2.1-10.2.1.7, 10.2.2, 10.3.1-10.3.4 (GCP inherited), 10.4.1, 10.4.2.1, 10.4.3, 10.5.1, 10.6.1-10.6.3 (GCP inherited), 10.7.1-10.7.3** | **Strong** | **Fully compliant:** 365-day log retention configured; Security Command Center Premium enabled; **Daily log review process** ([Section 4.7](../../security/digital/logging-monitoring.md#47-daily-log-review-process)) with checklist and sign-off; **Targeted risk analysis** for review frequency ([Section 4.8](../../security/digital/logging-monitoring.md#48-targeted-risk-analysis-for-log-review-frequency)); GCP inherited controls for immutability and time sync |
| **[network-security.md](../../security/digital/network-security.md)** (v1.4) | Network Security | 1.1.1, 1.1.2, 1.2.1-1.2.8, 1.3.1-1.3.3, 1.4.1-1.4.4, 2.2.3, 2.2.7, **4.1.1, 4.1.2, 4.2.1, 4.2.1.1**, 11.5.1 (partial) | **Strong** | VPC segmentation, WAF, firewall rules, NSC config standards ([Section 4.8](../../security/digital/network-security.md#48-nsc-configuration-standards)), 6-month NSC review, IaC change management, Cloud SQL Proxy ([Section 4.6.1](../../security/digital/network-security.md#461-cloud-sql-proxy)), HTTPS/TLS encryption ([Section 4.6.2](../../security/digital/network-security.md#462-httpstls-encryption)), **SSL/TLS Certificate Management with inventory ([Section 4.6.3](../../security/digital/network-security.md#463-ssltls-certificate-management))** |
| **[secure-development.md](../../security/digital/secure-development.md)** (v1.1) | Secure Development & Data Protection | 3.1.1, 3.1.2, 3.2.1, 3.3.1-3.3.2, 6.1.1, 6.1.2, 6.2.1, 6.2.3, 6.2.3.1, 6.2.4, 6.3.1, 6.3.3, 6.4.1, 6.5.5, 8.6.2, 8.6.3 | **Strong** | ADR process, code scanning (SonarQube, npm audit), tokenization architecture, cardholder data storage table, data minimization policy |
| **[security-training-guide.md](../../security/training/security-training-guide.md)** (v1.0) | Security Training | 6.2.2 (developer training), 6.5.5 (test data), 12.6.1-12.6.3 | **Strong** | Comprehensive training guide with Engineering-specific modules (9-10): OWASP Top 10, secure coding, logging standards, QA test data procedures |
| **[system-component-inventory.md](../../security/digital/system-component-inventory.md)** (v1.0) | System Component Inventory | 6.3.2, 12.5.1 | **Strong** | Application inventory (DASH Main, Kraken, Admin Portal), tech stack (Node.js 20, NestJS 10, TypeORM, PostgreSQL 15), Cloud SQL (1 RW + 1 RO), dependency tracking via npm audit in CI/CD, quarterly review |
| **[security-policy-awareness.md](../../security/digital/security-policy-awareness.md)** (v1.1) | Security Policy & Awareness | 1.1.1, 5.4.1, **8.3.1, 8.3.3, 8.3.8, 8.3.11, 8.4.1-8.4.3, 8.5.1** (MFA), 12.1.1, 12.6.1-12.6.3.2 | **Strong** | Annual training program, **comprehensive MFA documentation** (all systems: Microsoft 365, GCP, GitHub, Admin Portal), MFA methods table, lost MFA procedure, auth policy training, email phishing protection, device security |
| **[third-party-risk.md](../../security/digital/third-party-risk.md)** | Third-Party Risk Management | 12.8.1-12.8.4 (partial), 8.2.7, **5.1.1, 5.2.1 (GCP inherited)** | **Partial** | Vendor list exists; agreements and matrix incomplete; **GCP inherited controls for Req 5 documented ([Section 4.6](../../security/digital/third-party-risk.md#46-gcp-as-pci-dss-service-provider))** |
| **[vulnerability-management.md](../../security/digital/vulnerability-management.md)** (v1.2) | Vulnerability Management & Security Testing | 6.3.1, 6.3.3, **11.1.1-11.1.2, 11.3.1-11.3.2.1, 11.4.1-11.4.5, 11.5.2 (GCP)**, **5.1.1, 5.1.2, 5.2.1-5.2.3, 5.3.1-5.3.4 (GCP inherited)** | **Strong** | **Req 11 now comprehensive:** quarterly ASV scanning ([Section 5.2](../../security/digital/vulnerability-management.md#52-asv-external-vulnerability-scanning)), quarterly external pen tests ([Section 5.3](../../security/digital/vulnerability-management.md#53-external-penetration-testing)), annual internal pen tests ([Section 5.4](../../security/digital/vulnerability-management.md#54-internal-penetration-testing)), segmentation validation ([Section 5.5](../../security/digital/vulnerability-management.md#55-segmentation-validation)), remediation process ([Section 5.6](../../security/digital/vulnerability-management.md#56-findings-remediation-process)), vendor management ([Section 5.7](../../security/digital/vulnerability-management.md#57-vendor-management)); Code scanning via npm audit/SonarQube; GCP inherited controls documented |
| **[physical-security.md](../../security/physical/physical-security.md)** | Physical Security | 9.1.1, 9.1.2, 9.2.2-9.2.4, 9.3.1-9.3.2, 9.4.1, 9.4.6, 11.2.1, 11.2.2 | **Strong** | Access cards, visitor management, no physical media |
| **[company-overview.md](../../security/company-overview.md)** | Company & Application Overview | 12.5.1 (scope), 12.5.2 (scope validation), 3.x (tokenization) | **Strong** | Defines business context, payment architecture, tokenization strategy, PCI scope boundaries |
| **[security-standards-governance.md](../../security/digital/security-standards-governance.md)** (v1.0) | Security Standards & Exception Governance | **12.1.1, 12.1.2** (policies), **12.3.1** (partial - risk analysis framework), 1.1.1 (partial), 2.2.1 (partial - config standards) | **Strong** | Security baseline standards (PCI, OWASP, GCP, CIS); exception process requiring CTO approval (standard risk) or CTO+CEO approval (high risk); compensating control documentation; quarterly exception review; annual re-approval requirement |

---

## Coverage Analysis by Document

### Strong Coverage Documents (Satisfies majority of mapped requirements)

1. **access-control.md** - **Req 7 fully compliant (12/12)**: access control model, job function-based access, quarterly reviews, system accounts, default deny
2. **admin-portal-access.md** - **Req 8 authentication coverage**: password policy (8.3.5-8.3.6), lockout (8.3.4), passkey MFA (8.3.1, 8.4.1-8.4.2), unique IDs (8.2.1), first-use password change (8.3.5)
3. **cryptographic-key-management.md** - Full Req 3 encryption coverage
4. **deployment-control.md** - Complete change management process
5. **incident-response.md** - Comprehensive IR plan
6. **network-security.md** (v1.2) - **Excellent Req 1 coverage** including:
   - NSC configuration standards (Section 4.7)
   - Approved protocols, permitted ports, prohibited configurations
   - 6-month NSC configuration review process
   - IaC change management (Terraform + GitHub PR)
   - VPC segmentation, WAF, firewall rules
7. **secure-development.md** - Strong secure SDLC coverage
8. **physical-security.md** - Good physical controls for cloud environment

### Partial Coverage Documents (Gaps identified)

1. **logging-monitoring.md**
   - Gap: Log retention below 12 months
   - Gap: No daily log review process
   - Gap: No FIM documentation

2. **vulnerability-management.md**
   - Gap: Only code vulnerabilities; no infrastructure scanning
   - Gap: No ASV scanning reference

3. **third-party-risk.md**
   - Gap: Written agreements not documented
   - Gap: Responsibility matrix missing
   - Gap: PCI status monitoring incomplete

4. **business-continuity.md**
   - Gap: Not aligned with PCI IR requirements
   - Strength: Backup procedures documented

---

## Documents Not Mapped to PCI

The following control documents provide operational value but don't directly map to specific PCI requirements:

*None - all documents have some PCI relevance*

---

## PCI Requirements Without Internal Documentation

The following PCI requirement areas have **no or limited supporting internal documentation**:

| PCI Domain | Requirements | Documentation Needed | Status |
|------------|--------------|---------------------|--------|
| **Req 1 - Network Security** | 1.4.5, 1.5.1 | Internal IP disclosure controls | 1.4.5 minor gap; 1.5.1 addressed by device security (security-policy-awareness.md Section 4.6) |
| ~~**Req 5 - Anti-Malware**~~ | ~~5.1.1-5.4.1~~ | ~~Endpoint Security Policy~~ | **RESOLVED:** GCP inherited (serverless) + security-policy-awareness.md Section 4.6 (device security) |
| ~~**Req 11 - Testing**~~ | ~~11.3.1, 11.3.2, 11.4.1-11.4.5~~ | ~~Penetration Testing Methodology~~ | **RESOLVED:** vulnerability-management.md v1.2 Section 5 |
| **Req 12 - Policies** | 12.2.1 (partial), 12.7.1 | Approved software/hardware list, Personnel Screening | 12.2.1 partially addressed; 12.7.1 needs HR documentation |
| **Req 12 - TPSP** | 12.8.5 (partial) | TPSP Responsibility Matrix (Soepay, GP) | GCP matrix exists; need payment TPSP matrices |
| **Req 12 - Risk Analysis** | 12.3.1 | Targeted Risk Analysis | Implicit in control docs; formal TRA needed |

---

## Recommendations

### Priority 1: Create Missing Documents
1. ~~**Endpoint Security Policy** (Req 5)~~ - **RESOLVED:** GCP inherited + security-policy-awareness.md
2. ~~**Security Awareness Training Program** (Req 12.6)~~ - **RESOLVED:** security-training-guide.md (10 modules)
3. ~~**Penetration Testing Methodology** (Req 11.4)~~ - **RESOLVED:** vulnerability-management.md v1.2
4. **Personnel Screening Policy** (Req 12.7.1)
5. **Approved Software/Hardware List** (completes Req 12.2.1)

### Priority 2: Enhance Existing Documents
1. **logging-monitoring.md** - Add daily review process, extend retention
2. ~~**vulnerability-management.md** - Add infrastructure scanning section~~ - **RESOLVED:** v1.2 comprehensive security testing
3. **third-party-risk.md** - Add written agreement references, responsibility matrices (Soepay, GP)

### Priority 3: Create Supporting Artifacts
1. ~~**System Component Inventory** (Req 12.5.1)~~ - **RESOLVED:** system-component-inventory.md
2. **TPSP Responsibility Matrices for Soepay/GP** (Req 12.8.5)
3. **Cryptographic Cipher Suite Inventory** (Req 12.3.3)
4. **Targeted Risk Analysis** (Req 12.3.1)
5. **Unexpected PAN Discovery Procedure** (Req 12.10.7)
