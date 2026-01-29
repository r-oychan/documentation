# Internal Documentation → PCI DSS v4.0.1 Mapping

**Assessment Date:** 2026-01-29

This document shows how each internal control document maps to PCI DSS requirements.

---

## Mapping Table

| Internal Document | Control Area | PCI Requirements Covered | Coverage Strength | Notes |
|-------------------|--------------|--------------------------|-------------------|-------|
| **access-control.md** | Access Control & Identity Management | 7.1.1, 7.1.2, 7.2.1-7.2.6, 7.3.1-7.3.3, 8.1.1, 8.1.2, 8.2.1-8.2.8, 12.1.3, 12.1.4 | **Strong** | Comprehensive access controls with quarterly review |
| **admin-portal-access.md** | Admin Portal Access Control | 3.3.1, 3.3.2, 7.2.6, 8.2.1, 8.3.1-8.3.11, 8.4.1-8.4.2, 8.5.1, 10.2.1.1, 10.2.1.4 | **Strong** | MFA, password requirements, transaction logging |
| **business-continuity.md** | Business Continuity & Disaster Recovery | 12.10.1 (partial) | **Partial** | Backup procedures; not full BC/DR plan |
| **cryptographic-key-management.md** | Cryptographic Key Management | 3.4.1, 3.5.1, 3.5.1.1, 3.5.1.2, 3.6.1, 3.6.1.1-3.6.1.4, 3.7.1-3.7.3 | **Strong** | Full key lifecycle with GCP KMS |
| **deployment-control.md** | Deployment & Release Management | 6.5.1-6.5.6, 2.2.1 (partial) | **Strong** | Change control, approval workflow |
| **incident-response.md** | Incident Response | 12.10.1-12.10.6 | **Strong** | Comprehensive IR plan with post-incident review |
| **logging-monitoring.md** | Logging & Monitoring | 10.1.1, 10.1.2, 10.2.1-10.2.2, 10.3.1-10.3.3, 10.4.3 (partial), 10.6.1-10.6.3 | **Partial** | Logging exists; retention and daily review gaps |
| **network-security.md** | Network Security | 1.1.1, 1.1.2, 1.2.1-1.2.8, 1.3.1-1.3.3, 1.4.1-1.4.5, 2.2.3, 2.2.7, 4.2.1, 11.5.1 (partial) | **Strong** | VPC segmentation, WAF, firewall rules |
| **secure-development.md** | Secure Development & Data Protection | 3.1.1, 3.1.2, 3.2.1, 6.1.1, 6.1.2, 6.2.1, 6.2.3, 6.2.3.1, 6.2.4, 6.3.1, 6.3.3, 6.4.1, 8.6.2, 8.6.3 | **Strong** | ADR process, code scanning, tokenization |
| **third-party-risk.md** | Third-Party Risk Management | 12.8.1-12.8.4 (partial), 8.2.7 | **Partial** | Vendor list exists; agreements and matrix incomplete |
| **vulnerability-management.md** | Vulnerability Management | 6.3.1, 6.3.3, 11.3.1.3 (partial) | **Partial** | Code vulnerabilities covered; infrastructure scanning missing |
| **physical-security.md** | Physical Security | 9.1.1, 9.1.2, 9.2.2-9.2.4, 9.3.1-9.3.2, 9.4.1, 9.4.6, 11.2.1, 11.2.2 | **Strong** | Access cards, visitor management, no physical media |

---

## Coverage Analysis by Document

### Strong Coverage Documents (Satisfies majority of mapped requirements)

1. **access-control.md** - Covers Req 7 and 8 comprehensively
2. **admin-portal-access.md** - Strong authentication and logging controls
3. **cryptographic-key-management.md** - Full Req 3 encryption coverage
4. **deployment-control.md** - Complete change management process
5. **incident-response.md** - Comprehensive IR plan
6. **network-security.md** - Good segmentation and firewall documentation
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

The following PCI requirement areas have **no supporting internal documentation**:

| PCI Domain | Requirements | Documentation Needed |
|------------|--------------|---------------------|
| **Req 5 - Anti-Malware** | 5.1.1-5.4.1 | Endpoint Security Policy |
| **Req 11 - Testing** | 11.3.1, 11.3.2, 11.4.1-11.4.5 | Penetration Testing Methodology, Vulnerability Scanning Procedures |
| **Req 12 - Policies** | 12.2.1, 12.6.1-12.6.3, 12.7.1 | Acceptable Use Policy, Security Awareness Training, Personnel Screening |
| **Req 12 - TPSP** | 12.8.5 | TPSP Responsibility Matrix |

---

## Recommendations

### Priority 1: Create Missing Documents
1. **Endpoint Security Policy** (Req 5)
2. **Security Awareness Training Program** (Req 12.6)
3. **Penetration Testing Methodology** (Req 11.4)
4. **Acceptable Use Policy** (Req 12.2.1)

### Priority 2: Enhance Existing Documents
1. **logging-monitoring.md** - Add daily review process, extend retention
2. **vulnerability-management.md** - Add infrastructure scanning section
3. **third-party-risk.md** - Add written agreement references, responsibility matrix

### Priority 3: Create Supporting Artifacts
1. **System Component Inventory** (Req 12.5.1)
2. **TPSP Responsibility Matrices** (Req 12.8.5)
3. **Cryptographic Cipher Suite Inventory** (Req 12.3.3)
