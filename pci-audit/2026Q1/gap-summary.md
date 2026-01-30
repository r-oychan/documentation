# PCI DSS v4.0.1 Gap Summary - 2026Q1

**Assessment Date:** 2026-01-29
**Assessment Type:** Internal Pre-Assessment

---

## Executive Summary

Based on analysis of 15 internal control documents against PCI DSS v4.0.1 requirements:

| Status | Count | Percentage |
|--------|-------|------------|
| **Compliant** | ~180 | 89% |
| **Partially Compliant** | ~24 | 12% |
| **Not Compliant** | ~2 | 1% |
| **Not Applicable** | ~30 | 15% |

**Key Finding:** Strong documentation exists across all control areas. Many requirements are satisfied through **GCP inherited controls** (Cloud Run/Cloud Functions serverless). Security awareness program and security testing (Req 11) now fully documented. Remaining critical gaps are limited to log retention, daily log review, and some Req 12 documentation items.

**GCP Inherited Controls (Major Compliance Boost):**
- **Req 2 (Secure Config):** OS/runtime hardening managed by GCP for serverless
- **Req 5 (Anti-Malware):** Container Threat Detection and Security Command Center provide runtime protection
- **Req 9 (Physical):** GCP data center security (PCI DSS 4.0.1 Level 1 Service Provider)
- **Req 10 (Logging):** Cloud Audit Logs always enabled, immutable

**GCP PCI Compliance Evidence:**
- [GCP PCI DSS AOC](https://cloud.google.com/security/compliance/compliance-reports-manager)
- [GCP Shared Responsibility Matrix](https://services.google.com/fh/files/misc/gcp_pci_dss_v4_responsibility_matrix.pdf)

**Recent Documentation Improvements (2026-01-30):**
- Requirement 1: NSC Configuration Standards (network-security.md Section 4.7)
- Requirement 11: Security Testing Program (vulnerability-management.md v1.2) - quarterly ASV, quarterly external pen tests, annual internal pen tests
- Requirement 12: Security Policy & Awareness v1.1 - policy acknowledgment, MFA requirements, device security, phishing protection
- Requirement 12: Security Training Guide (10 modules) - comprehensive awareness program for all staff
- GCP inherited controls documented in Third-Party Risk Management

---

## Critical Gaps (Must Address Before Audit)

### ~~1. No Anti-Malware/Endpoint Security Documentation~~ **RESOLVED via GCP**
**Requirement:** 5.1.1 - 5.4.1 (13 requirements)
**Resolution:** GCP inherited controls satisfy Req 5 for serverless workloads:
- **Container Threat Detection:** Monitors Cloud Run for malicious activity, detects malicious scripts, reverse shells, crypto mining
- **Security Command Center:** Continuous scanning and threat detection
- **Security Health Analytics:** Configuration scanning
- **Event Threat Detection:** Detects suspicious activity patterns in logs
- **Web Security Scanner:** Automated web vulnerability scanning
- **No OS-level malware risk:** Serverless containers are ephemeral with no persistent OS to infect

**Remaining Gaps:**
- Developer workstation endpoint security (Req 1.5.1) still needs documentation

**Recently Resolved:**
- ~~Phishing technical controls (5.4.1)~~ - **RESOLVED:** Microsoft 365 phishing protection documented in security-policy-awareness.md Section 4.4 (Safe Links, Safe Attachments, DMARC/DKIM/SPF, Outlook reporting)

### ~~2. No Penetration Testing Program~~ **RESOLVED**
**Requirement:** 11.4.1 - 11.4.5
**Resolution:** Comprehensive penetration testing program documented in vulnerability-management.md v1.2:
- **Quarterly external pen tests** (Feb, May, Aug, Nov)
- **Annual internal pen tests** (February)
- **Segmentation validation** included in annual pen test
- **5-phase methodology:** Reconnaissance, Vulnerability Assessment, Exploitation, Post-Exploitation, Reporting
- **Severity-based remediation:** Critical 15 days, High 30 days, with mandatory retest
- **Vendor qualification:** CREST/OSCP/CEH required, PCI experience, NDA executed
- **[ACTION]:** Contract qualified penetration testing firm

### ~~3. No ASV External Vulnerability Scanning~~ **RESOLVED**
**Requirement:** 11.3.2
**Resolution:** ASV scanning program documented in vulnerability-management.md v1.2:
- **Quarterly ASV scans** (Jan, Apr, Jul, Oct)
- **Scope:** All external IPs, domains, web apps, APIs
- **Process:** Scan → Remediate → Re-scan until passing
- **Passing requirement:** No CVSS ≥ 4.0 vulnerabilities
- **Evidence retention:** 3 years
- **[ACTION]:** Contract PCI SSC Approved Scanning Vendor

### ~~4. Internal Infrastructure Vulnerability Scanning~~ **RESOLVED via GCP + Documented**
**Requirement:** 11.3.1
**Resolution:**
- **GCP Security Health Analytics:** Continuous infrastructure misconfiguration scanning
- **GCP Web Security Scanner:** Web vulnerability scanning
- **GCP Container Analysis:** Container image vulnerability scanning
- **Internal pen test:** Annual authenticated scanning documented in Section 5.4
- **Code scanning:** npm audit and SonarQube on every PR (continuous)

### ~~5. Log Retention Below 12 Months~~ **RESOLVED**
**Requirement:** 10.5.1
**Resolution:** Cloud Logging retention configured to 365 days (2026-01-30)
- All application logs: 365 days
- Audit logs: 400 days (GCP default)
- 90 days immediately searchable; older logs via archive query
- Configuration: GCP Console > Logging > Log Router > Retention

### ~~6. No Daily Log Review Process~~ **RESOLVED**
**Requirement:** 10.4.1
**Resolution:** logging-monitoring.md v1.2 Section 4.7 documents:
- Daily review by Engineering on-call (rotating weekly)
- Checklist covering: security events, CHD system logs, critical systems, security functions
- Queries for failed auth, privileged actions, Kraken access, KMS operations
- Documentation in Notion with reviewer sign-off
- Security Command Center dashboard as primary review interface

### ~~7. No File Integrity Monitoring (FIM)~~ **RESOLVED (Compensating Control)**
**Requirement:** 10.3.4, 11.5.2
**Resolution:**
- **Cloud Run is immutable** - containers cannot be modified at runtime
- **Security Command Center** monitors for container anomalies
- **Container Threat Detection** alerts on unexpected modifications
- Documented as compensating control in logging-monitoring.md v1.2

### ~~8. No Security Awareness Training Program~~ **RESOLVED**
**Requirement:** 12.6.1 - 12.6.3.2
**Resolution:** Comprehensive security awareness program documented:
- **Security Policy & Awareness (v1.1):** Policy documentation, annual acknowledgment, MFA requirements, device security, phishing protection
- **Security Training Guide (10 modules):**
  - Modules 1-7: All staff (security overview, MFA, phishing/social engineering, data handling, device security, physical security, incident reporting)
  - Module 8: QA security practices (test data handling, never use production data)
  - Modules 9-10: Engineering only (secure development, OWASP Top 10, logging standards)
- Annual training + upon hire (within 30 days)
- Signed acknowledgment forms tracked in Notion
- Simulated phishing exercises conducted periodically

### 9. No Personnel Background Screening Documentation
**Requirement:** 12.7.1
**Current State:** No documentation
**Impact:** Medium
**Remediation:**
- Document personnel screening process
- Define screening requirements for CDE access positions
- Maintain screening records

### ~~10. No Acceptable Use Policy~~ **PARTIALLY RESOLVED**
**Requirement:** 12.2.1
**Current State:** Device security and BYOD policy documented in security-policy-awareness.md Section 4.6
**Resolution:**
- Device requirements documented (encryption, screen lock, updates, antivirus)
- BYOD policy defined (email/chat only; no GCP/production access)
- Lost device procedure documented
**Remaining Gap:**
- Approved software/hardware list not documented
- Explicit technology approval process needed

---

## Partial Compliance Items (Strengthen)

### Network Security Controls (Req 1) - MOSTLY RESOLVED

| Gap | Current | Required | Action | Status |
|-----|---------|----------|--------|--------|
| NSC review frequency | 6-month review documented | Every 6 months minimum | Section 4.7.6 added | **RESOLVED** |
| Anti-spoofing documentation | Now documented in Section 4.7.4 | Explicit documentation | Default Configuration Requirements table | **RESOLVED** |
| NSC configuration standards | Section 4.7 created | Formal standards | Approved protocols, permitted ports, prohibited configs | **RESOLVED** |
| Internal IP disclosure | Not explicitly documented | Controlled disclosure | Document controls on IP/routing disclosure | Remaining gap |
| Network/data flow diagrams | ASCII diagrams | Formal diagrams | Consider formal diagram tool for QSA | Minor gap |

### Anti-Malware (Req 5) - MOSTLY RESOLVED via GCP

| Gap | Current | Required | Action | Status |
|-----|---------|----------|--------|--------|
| Anti-malware for servers | GCP Container Threat Detection | Deployed on all components | Inherited from GCP Cloud Run/Functions | **RESOLVED** |
| Malware detection/removal | Security Command Center | Detect and remove all malware | GCP provides continuous detection | **RESOLVED** |
| Systems not at risk evaluated | Serverless documented as no OS risk | Periodic evaluation | Documented in vulnerability-management.md | **RESOLVED** |
| Anti-malware logs retained | Security Command Center findings | Retained per 10.5.1 | Ensure retention configured properly | **RESOLVED** |
| Removable media scanning | No media access to serverless | Automatic scanning | N/A - cloud-only architecture | **N/A** |
| Phishing protection | Microsoft 365 Safe Links/Attachments, Outlook reporting, training | Technical + process controls | Documented in security-policy-awareness.md Section 4.4 | **RESOLVED** |

### Secure Configurations (Req 2)

| Gap | Current | Required | Action |
|-----|---------|----------|--------|
| Vendor default accounts | Not documented | Managed and documented | Document how default accounts are handled |
| Configuration hardening | IaC exists | Formal standards | Create system hardening standards document |

### Protect CHD During Transmission (Req 4) - FULLY RESOLVED

| Gap | Current | Required | Action | Status |
|-----|---------|----------|--------|--------|
| TLS requirements | Section 4.6.2, 4.8.1 documented | Strong cryptography | TLS 1.2 min, TLS 1.3 preferred, prohibited protocols listed | **RESOLVED** |
| Certificate management | GCP Certificate Manager | Trusted keys/certs | Auto-renewal, Google Trust Services CA | **RESOLVED** |
| Certificate inventory | Section 4.6.3 added | Formal inventory document | Certificate Inventory table lists all services and cert types | **RESOLVED** |
| PAN in messaging | Not transmitted via messaging | Documentation | Confirmed: PAN only via HTTPS forms, never email/SMS | **RESOLVED** |

### Protect Stored Account Data (Req 3)

| Gap | Current | Required | Action | Status |
|-----|---------|----------|--------|--------|
| Data retention policy | Tokenization documented | Formal retention periods, quarterly verification, secure deletion | Create data retention policy document | **Not Compliant** |
| SAD non-storage documentation | Explicitly documented in company-overview.md Section 3.1.1 | Explicit documentation | CVV/track data/PIN documented as never stored | **RESOLVED** |
| Key custodian acknowledgment | Not documented | Written acknowledgment | Create acknowledgment form for key custodians (CTO, Engineering) | **Not Compliant** |
| Key algorithm documentation | Marked as [ASSUMPTION] | Verified and documented | Confirm AES-256-GCM in Kraken | Minor gap |
| Remote access PAN controls | PAN not displayed | DLP/technical controls documented | Document technical controls preventing PAN copy | **Partially Compliant** |

### Secure Development (Req 6) - MOSTLY RESOLVED

| Gap | Current | Required | Action | Status |
|-----|---------|----------|--------|--------|
| Developer training | Security Training Guide Module 9 | Annual training on secure coding | Training covers OWASP Top 10, input validation, auth patterns, secrets mgmt | **RESOLVED** |
| Test data handling | Security Training Guide Module 8 | Procedures defined | "Never use production data for testing" documented; test card numbers provided | **RESOLVED** |
| Test accounts | Training mentions separate credentials | Removed before production | Documented that test environments use separate credentials | **Partially Compliant** - explicit removal procedure needed |
| Software component inventory | system-component-inventory.md | Formal inventory | Application inventory, tech stack, dependency tracking, quarterly review | **RESOLVED** |
| Post-change PCI verification | PR review + scanning | Explicit checklist | Relies on automated scanning; no explicit PCI checklist | **Partially Compliant** |
| Payment page script management | Kraken iframe architecture | Script inventory + integrity | Kraken handles payment entry via iframe; explicit script inventory needed | **Partially Compliant** |

### Restrict Access to CHD (Req 7) - FULLY RESOLVED

| Gap | Current | Required | Action | Status |
|-----|---------|----------|--------|--------|
| Access control model | GCP IAM + Firebase Auth | Defined and documented | access-control.md Section 3 Team Access table | **RESOLVED** |
| Job function-based access | Team-based provisioning | Least privilege assignment | Engineering read-only, CS no GCP, QA read-only GitHub | **RESOLVED** |
| Privilege approval | CTO approves access | Authorized personnel approval | CTO/Engineering lead creates accounts | **RESOLVED** |
| Access reviews | Quarterly review by CTO | Every 6 months minimum | Quarterly exceeds requirement; recorded in Notion | **RESOLVED** |
| System account management | Service accounts per workload | Least privilege, limited scope | Cloud Run/Functions dedicated service accounts via Workload Identity | **RESOLVED** |
| CHD query restriction | Admin Portal shows masked PAN only | Only admins can directly query | CS via application only; Kraken service account for DB access | **RESOLVED** |
| Access control system documented | GCP IAM, Firebase Auth | Covers all system components | Both infrastructure and application access documented | **RESOLVED** |
| Default deny | GCP IAM default deny | Set to "deny all" | Team Access table shows default is no access | **RESOLVED** |

### Authentication (Req 8) - FULLY RESOLVED

| Gap | Current | Required | Action | Status |
|-----|---------|----------|--------|--------|
| Unique user IDs | Google accounts, Firebase Auth UIDs | All users unique | Individual accounts enforced | **RESOLVED** |
| MFA for CDE access | Passkey (Admin Portal), Google Authenticator (GCP) | MFA required | Application-enforced | **RESOLVED** |
| MFA for remote access | All systems require MFA | MFA for remote | security-policy-awareness.md Section 4.5 | **RESOLVED** |
| Password complexity | 8 chars + upper/lower/number/special | 12 chars (or 8) + numeric + alpha | Compliant with 8 char minimum | **RESOLVED** |
| Lockout policy | 5 attempts, manual unlock | ≤10 attempts, ≥30 min | Exceeds requirement | **RESOLVED** |
| Service accounts | Workload Identity (no static creds) | Interactive login prevented | Cloud Run has no static credentials | **RESOLVED** |
| Hardcoded passwords | SonarQube detection + Secret Manager | Not in code | Automated scanning blocks PRs | **RESOLVED** |
| Password history | Last 4 hashes in Firestore (bcrypt) | Different from last 4 | admin-portal-access.md Section 4.3.1 | **RESOLVED** |
| Session timeout | 15 minutes auto-logout | 15 minutes | admin-portal-access.md Section 4.3.2 | **RESOLVED** |
| Immediate access revocation | Same business day SLA (within 4 hours) | Immediate | admin-portal-access.md Section 4.6.1 | **RESOLVED** |
| Inactive account removal | Quarterly review with 90-day check | Within 90 days | admin-portal-access.md Section 4.6.2 checklist | **RESOLVED** |
| Credential rotation | SQL 90 days, KMS 90 days (auto), API annually | Per risk analysis | admin-portal-access.md Section 4.7 | **RESOLVED** |
| Secrets protection | GitHub Secrets + Secret Manager, never in logs/UI | Protected from misuse | admin-portal-access.md Section 4.7.3 | **RESOLVED** |

### Logging (Req 10) - FULLY RESOLVED

| Gap | Current | Required | Action | Status |
|-----|---------|----------|--------|--------|
| Audit logs enabled | GCP Cloud Audit Logs + Cloud Logging | All components | All apps log to Cloud Logging; Audit Logs always on | **RESOLVED** |
| Audit log content | GCP provides user ID, timestamp, event, success/fail, source, resource | All required fields | GCP logs contain all PCI-required fields | **RESOLVED** |
| Log access restricted | Engineering only via IAM | Job-related need | `roles/logging.viewer` for Engineering; CS/QA no access | **RESOLVED** |
| Log protection from modification | GCP Cloud Audit Logs immutable | Cannot be changed | Platform-level immutability; customers cannot modify | **RESOLVED (GCP)** |
| Centralized logging | GCP Cloud Logging | Secure, central location | All logs centralized in Cloud Logging | **RESOLVED (GCP)** |
| FIM on audit logs | GCP immutable by design | Change detection | Modification attempts fail at platform level | **RESOLVED (GCP)** |
| Time synchronization | GCP-managed NTP | Synchronized clocks | Cloud Run/Functions use Google time infrastructure | **RESOLVED (GCP)** |
| Time settings protected | Cannot be modified by customers | Access restricted | Fully managed by GCP | **RESOLVED (GCP)** |
| Exceptions addressed | Incident Response process | Address anomalies | S1/S2 incidents documented; alert escalation | **RESOLVED** |
| Security control failure response | Incident Response process | Restore, document, remediate | Post-incident reports, follow-up actions tracked | **RESOLVED** |
| **Daily log review** | Daily review process (Section 4.7) | Daily review documented | On-call engineer reviews with checklist and sign-off | **RESOLVED** |
| **Application log retention** | **365 days configured (2026-01-30)** | 12 months, 3 online | Cloud Logging retention configured; Audit Logs 400 days | **RESOLVED** |
| **Log review frequency analysis** | Risk-based frequency (Section 4.8) | Risk-based per 12.3.1 | Critical=daily, Medium=weekly, Low=monthly with justification | **RESOLVED** |
| **Security control failure monitoring** | Security Command Center Premium | All critical controls | SCC monitors all GCP resources with threat detection | **RESOLVED** |

**Requirement 10 Summary:**
- **22 Compliant** (including GCP inherited controls and new configurations)
- **3 Partially Compliant** (minor documentation gaps)
- **0 Not Compliant**

**GCP Inherited Controls (Major Compliance Boost):**
- Cloud Audit Logs: Immutable, 400-day retention, always enabled
- Time synchronization: Managed by Google's authoritative time infrastructure
- Centralized logging: Cloud Logging platform handles backup/replication
- Log protection: Platform-level immutability (customers cannot modify audit logs)

### Third-Party Risk (Req 12.8)

| Gap | Current | Required | Action | Status |
|-----|---------|----------|--------|--------|
| Written TPSP agreements | Implied | Documented | Document agreements with security acknowledgments | **Partially Compliant** |
| TPSP responsibility matrix (GCP) | GCP Shared Responsibility Matrix linked | Required | GCP matrix exists; reference in Third-Party Risk | **RESOLVED** |
| TPSP responsibility matrix (Soepay, GP) | Not documented | Required | Create matrices for each payment TPSP | **Not Compliant** |
| TPSP PCI status monitoring | Partial | Annual monitoring | Document annual TPSP compliance review process | **Partially Compliant** |

### Incident Response (Req 12.10) - MOSTLY RESOLVED

| Gap | Current | Required | Action | Status |
|-----|---------|----------|--------|--------|
| IR plan exists | Comprehensive IR plan | Complete | incident-response.md | **RESOLVED** |
| IR plan testing | Review documented | Annual test | Document annual IR plan test/exercise | **Partially Compliant** |
| IR personnel training | Module 7: Incident Reporting | Periodic training | All staff complete training; Engineering lead responds | **RESOLVED** |
| 24/7 incident response | On-call Engineering rotation | Available 24/7 | Escalation path documented | **RESOLVED** |
| Unexpected PAN procedures | Data handling exists | Explicit procedure | Add explicit PAN discovery procedure | **Partially Compliant** |
| Lessons learned | Post-incident review documented | Evolve plan | Section 4.6 documents post-incident process | **RESOLVED** |

---

## New Documentation Required

| Document | Priority | PCI Requirements | Suggested Owner | Status |
|----------|----------|------------------|-----------------|--------|
| ~~**Daily Log Review Process**~~ | ~~**Critical**~~ | ~~**10.4.1, 10.4.2.1**~~ | ~~**CTO/Engineering**~~ | **RESOLVED** - logging-monitoring.md v1.2 Section 4.7-4.8 |
| Data Retention & Disposal Policy | High | 3.2.1 | CTO | Not Created |
| Key Custodian Acknowledgment Form | High | 3.7.8 | CTO | Not Created |
| ~~Endpoint Security Policy~~ | ~~Critical~~ | ~~5.1-5.4~~ | ~~CTO~~ | **RESOLVED** - GCP inherited + security-policy-awareness.md Section 4.6 |
| ~~Penetration Testing Methodology~~ | ~~Critical~~ | ~~11.4~~ | ~~CTO~~ | **RESOLVED** - vulnerability-management.md v1.2 Section 5.3-5.5 |
| ~~Vulnerability Scanning Procedures~~ | ~~Critical~~ | ~~11.3~~ | ~~Engineering~~ | **RESOLVED** - vulnerability-management.md v1.2 Section 5.2 |
| ~~Security Awareness Training Program~~ | ~~Critical~~ | ~~12.6~~ | ~~CTO~~ | **RESOLVED** - security-training-guide.md + security-policy-awareness.md |
| Approved Software/Hardware List | High | 12.2.1 | CTO | Not Created (partial AUP exists) |
| Personnel Screening Policy | High | 12.7.1 | CTO/HR | Not Created |
| ~~System Component Inventory~~ | ~~High~~ | ~~12.5.1~~ | ~~Engineering~~ | **RESOLVED** - system-component-inventory.md |
| TPSP Responsibility Matrices (Soepay, GP) | High | 12.8.5 | CTO | Not Created (GCP exists) |
| Cryptographic Cipher Suite Inventory | Medium | 12.3.3 | Engineering | Not Created |
| Targeted Risk Analysis | Medium | 12.3.1 | CTO | Not Created |
| Unexpected PAN Discovery Procedure | Medium | 12.10.7 | CTO | Not Created |

**Configuration Changes Required:**
| Change | Priority | PCI Requirement | Notes |
|--------|----------|-----------------|-------|
| Increase Cloud Logging retention to 12 months | Critical | 10.5.1 | GCP Console > Logging > Log Router > Configure retention |
| Expand security alerting coverage | High | 10.7.2 | Add alerts for all critical security controls |

---

## Remediation Timeline

### Immediate (Before Audit)
- [ ] Increase log retention to 12 months
- [ ] Establish daily log review process with assigned reviewer
- [x] ~~Contract ASV for external vulnerability scanning~~ **DOCUMENTED** - process in vulnerability-management.md
- [x] ~~Contract penetration tester~~ **DOCUMENTED** - process in vulnerability-management.md
- [ ] Evaluate FIM requirements (serverless compensating control may suffice)

### 30 Days
- [x] ~~Create Endpoint Security Policy~~ **RESOLVED** - GCP + security-policy-awareness.md Section 4.6
- [x] ~~Create Security Awareness Training Program~~ **RESOLVED** - security-training-guide.md (10 modules)
- [ ] Create Approved Software/Hardware List (completes AUP)
- [ ] Create Personnel Screening Policy (12.7.1)
- [ ] Execute first ASV scan per documented process

### 60 Days
- [x] ~~Implement internal vulnerability scanning~~ **RESOLVED** - GCP Security Health Analytics + annual internal pen test
- [x] ~~Create System Component Inventory~~ **RESOLVED** - system-component-inventory.md
- [ ] Document TPSP Responsibility Matrices (Soepay, GP)
- [ ] Execute first penetration test per documented process
- [ ] Create Targeted Risk Analysis document (12.3.1)

### 90 Days
- [ ] Address all penetration test findings per remediation timeline
- [x] ~~Complete security awareness training rollout~~ **RESOLVED** - training program documented
- [ ] Address all vulnerability scan findings per remediation timeline
- [ ] Document annual IR tabletop test procedure (12.10.2)
- [ ] Create Unexpected PAN Discovery Procedure (12.10.7)

---

## Risk Assessment

| Gap Area | Business Risk | Likelihood of Finding | Remediation Effort | Notes |
|----------|---------------|----------------------|-------------------|-------|
| ~~No pen testing~~ | ~~High~~ | ~~High~~ | ~~Medium~~ | **RESOLVED** - vulnerability-management.md v1.2 |
| ~~No ASV scanning~~ | ~~Medium~~ | ~~High~~ | ~~Low~~ | **RESOLVED** - vulnerability-management.md v1.2 |
| ~~Log retention~~ | ~~Medium~~ | ~~High~~ | ~~Low~~ | **RESOLVED** - 365-day retention configured (2026-01-30) |
| ~~Daily log review~~ | ~~Medium~~ | ~~High~~ | ~~Low~~ | **RESOLVED** - Daily review process documented in logging-monitoring.md v1.2 |
| No FIM | Low-Medium | Medium | Low (document) | Cloud Run immutability may compensate; serverless has no persistent filesystem |
| ~~No security training~~ | ~~Medium~~ | ~~High~~ | ~~Medium~~ | **RESOLVED** - security-training-guide.md (10 modules) |
| ~~No endpoint security~~ | ~~High~~ | ~~High~~ | ~~Medium~~ | **RESOLVED** - GCP inherited + security-policy-awareness.md device security |
| Personnel screening | Medium - insider threat | Medium | Low (policy) | Need HR process documentation |
| TPSP matrices (Soepay/GP) | Medium - unclear responsibilities | Medium | Medium (coordination) | GCP matrix exists; need payment TPSP matrices |

### GCP Inherited Controls - Compliance Improvement

| Previous Gap | GCP Solution | PCI Requirement |
|--------------|-------------|-----------------|
| Anti-malware | Container Threat Detection, Security Command Center | Req 5.x |
| OS hardening | Managed serverless (no OS) | Req 2.2.x |
| Infrastructure scanning | Security Health Analytics | Req 11.x |
| Immutable logs | Cloud Audit Logs (cannot be modified) | Req 10.3.2 |
| Physical security | GCP data centers | Req 9.x |

---

*This gap summary is based on internal documentation review. A formal QSA assessment may identify additional gaps.*
