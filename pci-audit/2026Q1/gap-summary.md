# PCI DSS v4.0.1 Gap Summary - 2026Q1

**Assessment Date:** 2026-01-29
**Assessment Type:** Internal Pre-Assessment

---

## Executive Summary

Based on analysis of 12 internal control documents against PCI DSS v4.0.1 requirements:

| Status | Count | Percentage |
|--------|-------|------------|
| **Compliant** | ~93 | 46% |
| **Partially Compliant** | ~44 | 22% |
| **Not Compliant** | ~48 | 24% |
| **Not Applicable** | ~22 | 11% |

**Key Finding:** Strong documentation exists for access control, encryption, network security, and incident response. Critical gaps exist in endpoint security (Req 5), security testing (Req 11), and security awareness (Req 12).

---

## Critical Gaps (Must Address Before Audit)

### 1. No Anti-Malware/Endpoint Security Documentation
**Requirement:** 5.1.1 - 5.4.1 (11 requirements)
**Current State:** No documentation
**Impact:** High - Entire PCI requirement unfulfilled
**Remediation:**
- Create Endpoint Security Policy document
- Document anti-malware deployment on endpoints connecting to CDE
- Document how anti-malware is managed, updated, and monitored

### 2. No Penetration Testing Program
**Requirement:** 11.4.1 - 11.4.5
**Current State:** No documentation, no testing performed
**Impact:** High - Required annually
**Remediation:**
- Define penetration testing methodology
- Contract qualified penetration tester
- Schedule internal and external pen tests
- Document testing scope including segmentation validation

### 3. No ASV External Vulnerability Scanning
**Requirement:** 11.3.2
**Current State:** No ASV scans documented
**Impact:** High - Required quarterly by approved vendor
**Remediation:**
- Contract PCI SSC Approved Scanning Vendor (ASV)
- Schedule quarterly external scans
- Establish process for remediating findings

### 4. No Internal Infrastructure Vulnerability Scanning
**Requirement:** 11.3.1
**Current State:** Code scanning exists; infrastructure scanning absent
**Impact:** High - Required quarterly
**Remediation:**
- Implement internal vulnerability scanning tool
- Schedule quarterly scans of CDE infrastructure
- Document scanning procedures and remediation

### 5. Log Retention Below 12 Months
**Requirement:** 10.5.1
**Current State:** Application logs ~30 days, Audit logs ~400 days
**Impact:** High - Non-compliant
**Remediation:**
- Increase GCP Cloud Logging retention to 12 months minimum
- Ensure 3 months immediately accessible
- Document retention configuration

### 6. No Daily Log Review Process
**Requirement:** 10.4.1
**Current State:** Alerts exist but no formal daily review
**Impact:** High - Required daily
**Remediation:**
- Document daily log review process
- Assign responsibility for review
- Define what logs are reviewed and criteria for escalation
- Implement automated review mechanisms (10.4.1.1)

### 7. No File Integrity Monitoring (FIM)
**Requirement:** 10.3.4, 11.5.2
**Current State:** No FIM deployed or documented
**Impact:** Medium-High
**Remediation:**
- Deploy FIM solution on critical files and configurations
- Configure alerts for unauthorized changes
- Document FIM scope and procedures

### 8. No Security Awareness Training Program
**Requirement:** 12.6.1 - 12.6.3
**Current State:** No documentation
**Impact:** High - Required annually
**Remediation:**
- Create formal security awareness program
- Document training content including phishing, social engineering
- Implement annual training for all personnel
- Track acknowledgment of security policies

### 9. No Personnel Background Screening Documentation
**Requirement:** 12.7.1
**Current State:** No documentation
**Impact:** Medium
**Remediation:**
- Document personnel screening process
- Define screening requirements for CDE access positions
- Maintain screening records

### 10. No Acceptable Use Policy
**Requirement:** 12.2.1
**Current State:** No documentation
**Impact:** Medium
**Remediation:**
- Create Acceptable Use Policy document
- Cover remote access, wireless, mobile devices, email, internet usage
- Document approved technologies list

---

## Partial Compliance Items (Strengthen)

### Network Security Controls (Req 1)

| Gap | Current | Required | Action |
|-----|---------|----------|--------|
| NSC review frequency | Quarterly | Every 6 months minimum | Document 6-month NSC-specific review cycle |
| Anti-spoofing documentation | Relies on GCP | Explicit documentation | Document GCP anti-spoofing controls |
| Internal IP disclosure | Not documented | Controlled disclosure | Document controls on IP/routing disclosure |

### Secure Configurations (Req 2)

| Gap | Current | Required | Action |
|-----|---------|----------|--------|
| Vendor default accounts | Not documented | Managed and documented | Document how default accounts are handled |
| Configuration hardening | IaC exists | Formal standards | Create system hardening standards document |

### Secure Development (Req 6)

| Gap | Current | Required | Action |
|-----|---------|----------|--------|
| Developer training | Not documented | Annual training | Document secure coding training program |
| Test data handling | Not documented | Procedures defined | Document test data procedures (no production data) |
| Test accounts | Not documented | Removed before production | Document test account management |

### Authentication (Req 8)

| Gap | Current | Required | Action |
|-----|---------|----------|--------|
| Password history | Not documented | Different from last 4 | Configure and document password history |
| Service account management | Partial | Full lifecycle | Document service account rotation policies |
| Immediate access revocation | Quarterly review | Upon termination | Document immediate revocation process |

### Logging (Req 10)

| Gap | Current | Required | Action |
|-----|---------|----------|--------|
| Log review frequency | Ad-hoc | Risk-based, documented | Create targeted risk analysis for review frequency |
| Security control failure monitoring | Some alerts | All critical controls | Expand alerting to all security controls |

### Third-Party Risk (Req 12.8)

| Gap | Current | Required | Action |
|-----|---------|----------|--------|
| Written TPSP agreements | Implied | Documented | Document agreements with security acknowledgments |
| TPSP responsibility matrix | Not documented | Required | Create matrix for each TPSP (Soepay, GP, GCP, etc.) |
| TPSP PCI status monitoring | Partial | Annual monitoring | Document annual TPSP compliance review process |

### Incident Response (Req 12.10)

| Gap | Current | Required | Action |
|-----|---------|----------|--------|
| IR plan testing | Review documented | Annual test | Document annual IR plan test/exercise |
| IR personnel training | Not documented | Periodic training | Create and document IR training program |
| Unexpected PAN procedures | Not documented | Required | Add PAN discovery procedures to IR plan |

---

## New Documentation Required

| Document | Priority | PCI Requirements | Suggested Owner |
|----------|----------|------------------|-----------------|
| Endpoint Security Policy | Critical | 5.1-5.4 | CTO |
| Penetration Testing Methodology | Critical | 11.4 | CTO |
| Vulnerability Scanning Procedures | Critical | 11.3 | Engineering |
| Security Awareness Training Program | Critical | 12.6 | CTO |
| Acceptable Use Policy | High | 12.2.1 | CTO |
| Personnel Screening Policy | High | 12.7.1 | CTO/HR |
| System Component Inventory | High | 12.5.1 | Engineering |
| TPSP Responsibility Matrices | High | 12.8.5 | CTO |
| Cryptographic Cipher Suite Inventory | Medium | 12.3.3 | Engineering |
| System Hardening Standards | Medium | 2.2.1 | Engineering |

---

## Remediation Timeline

### Immediate (Before Audit)
- [ ] Increase log retention to 12 months
- [ ] Establish daily log review process
- [ ] Contract ASV for external vulnerability scanning
- [ ] Contract penetration tester
- [ ] Document FIM requirements

### 30 Days
- [ ] Create Endpoint Security Policy
- [ ] Create Security Awareness Training Program
- [ ] Create Acceptable Use Policy
- [ ] Deploy FIM solution
- [ ] Complete first ASV scan

### 60 Days
- [ ] Implement internal vulnerability scanning
- [ ] Create System Component Inventory
- [ ] Document TPSP Responsibility Matrices
- [ ] Complete penetration testing
- [ ] Document Personnel Screening Policy

### 90 Days
- [ ] Address all penetration test findings
- [ ] Complete security awareness training rollout
- [ ] Address all vulnerability scan findings
- [ ] Complete TPSP compliance reviews

---

## Risk Assessment

| Gap Area | Business Risk | Likelihood of Finding | Remediation Effort |
|----------|---------------|----------------------|-------------------|
| No pen testing | High - undetected vulnerabilities | High | Medium (contract) |
| No ASV scanning | Medium - external exposure | High | Low (contract) |
| Log retention | Medium - inability to investigate | High | Low (config change) |
| No FIM | High - undetected changes | High | Medium (deploy tool) |
| No security training | Medium - human error | High | Medium (create program) |
| No endpoint security | High - malware risk | High | Medium (document/deploy) |

---

*This gap summary is based on internal documentation review. A formal QSA assessment may identify additional gaps.*
