# PCI DSS v4.0.1 Compliance Mapping - 2026Q1 Audit

**Assessment Date:** 2026-01-29
**Assessor:** Internal Pre-Assessment
**Company:** DASH - Multi-vertical mobile commerce platform (Ride-Hailing, Event Ticketing)
**Transaction Volume:** Expected >HK$10M annually

---

## Company Overview

DASH is a multi-vertical mobile commerce provider operating in Hong Kong. Our platform enables:
- **Ride-Hailing:** Users book rides, pre-authorize payment, drivers receive day-end settlement
- **Event Ticketing:** Users purchase e-tickets, present QR codes at venues via DASH Merchant App

For complete company and architecture details, see [Company Overview](../../security/company-overview.md).

---

## Scope Definition

### Cardholder Data Environment (CDE)
- **Kraken** - Dedicated payment module (only component that stores/processes PAN)
- **Kraken VPC** - Isolated GCP VPC containing Kraken service and database

### Connected Systems (Out of PCI Scope due to Tokenization)
- **DASH Core** - Receives tokens only; no PAN storage, processing, or transmission
- **DASH Mobile App** - Card entry via Kraken iframe; app never sees PAN
- **DASH Merchant App** - QR code validation only; no payment data
- **Admin Portal** - Views masked PAN (first 6, last 4) and tokens only; no full PAN access

### Out of Scope
- **Third-party payment gateways** (Soepay, GP) - Their PCI compliance responsibility
- **Contactless/PayWave transactions** - Payment terminal handles PAN; DASH receives transaction reference only
- **Office WiFi** - No direct CDE access
- **External payment terminals** - PCI-compliant third-party handles all card processing

### Scope Reduction via Tokenization

DASH architecture strictly limits PCI scope through tokenization:
1. Card details enter only through Kraken (never DASH Core or mobile app)
2. Kraken encrypts PAN and returns tokens to other systems
3. All non-Kraken systems operate with tokens only
4. Full PAN never leaves Kraken except when sent to payment gateway

### GCP Inherited Controls (PCI DSS 4.0.1)

We deploy on **Google Cloud Platform** (Cloud Run, Cloud Functions) which maintains **PCI DSS 4.0.1 Level 1 Service Provider** compliance. Many PCI requirements are fully or partially satisfied through inherited controls.

**GCP Evidence:**
- [GCP PCI DSS AOC](https://cloud.google.com/security/compliance/compliance-reports-manager) - Available via Compliance Reports Manager
- [GCP PCI DSS Shared Responsibility Matrix](https://services.google.com/fh/files/misc/gcp_pci_dss_v4_responsibility_matrix.pdf)

**Serverless Benefits (Cloud Run / Cloud Functions):**

| PCI Requirement Area | Inherited Control | Notes |
|---------------------|-------------------|-------|
| **Req 2** (Secure Config) | OS/runtime hardening | Fully managed by GCP; no OS access |
| **Req 5** (Anti-Malware) | Infrastructure protection | Security Command Center, Container Threat Detection |
| **Req 6** (Secure Dev) | Patch management (runtime) | GCP patches container runtime |
| **Req 9** (Physical Access) | Data center security | GCP's responsibility |
| **Req 10** (Logging) | Cloud Audit Logs | Always enabled, immutable |
| **Req 11** (Testing) | Infrastructure scanning | Security Health Analytics |

**Applicability Notes:**
- Requirements marked "GCP Inherited" rely on GCP's PCI compliance
- Customer responsibilities documented in internal control documents
- Combined evidence (GCP AOC + internal documentation) required for full compliance

---

## Internal Documentation Inventory

| Document | Path | Last Reviewed |
|----------|------|---------------|
| **[Company & Application Overview](../../security/company-overview.md)** | `security/company-overview.md` | 2026-01-30 |
| [Access Control & Identity Management](../../security/digital/access-control.md) | `security/digital/access-control.md` | 2026-01-29 |
| [Admin Portal Access Control](../../security/digital/admin-portal-access.md) | `security/digital/admin-portal-access.md` | 2026-01-29 |
| [Business Continuity & Disaster Recovery](../../security/digital/business-continuity.md) | `security/digital/business-continuity.md` | 2026-01-29 |
| [Cryptographic Key Management](../../security/digital/cryptographic-key-management.md) | `security/digital/cryptographic-key-management.md` | 2026-01-29 |
| [Deployment & Release Management](../../security/digital/deployment-control.md) | `security/digital/deployment-control.md` | 2026-01-29 |
| [Incident Response](../../security/digital/incident-response.md) | `security/digital/incident-response.md` | 2026-01-29 |
| [Logging & Monitoring](../../security/digital/logging-monitoring.md) | `security/digital/logging-monitoring.md` | 2026-01-29 |
| [Network Security](../../security/digital/network-security.md) | `security/digital/network-security.md` | 2026-01-29 |
| [Secure Development & Data Protection](../../security/digital/secure-development.md) | `security/digital/secure-development.md` | 2026-01-29 |
| **[Security Standards & Exception Governance](../../security/digital/security-standards-governance.md)** | `security/digital/security-standards-governance.md` | 2026-01-30 |
| [Security Policy & Awareness](../../security/digital/security-policy-awareness.md) | `security/digital/security-policy-awareness.md` | 2026-01-30 |
| [Security Training Guide](../../security/training/security-training-guide.md) | `security/training/security-training-guide.md` | 2026-01-30 |
| [System Component Inventory](../../security/digital/system-component-inventory.md) | `security/digital/system-component-inventory.md` | 2026-01-30 |
| [Third-Party Risk Management](../../security/digital/third-party-risk.md) | `security/digital/third-party-risk.md` | 2026-01-29 |
| [Vulnerability Management & Security Testing](../../security/digital/vulnerability-management.md) | `security/digital/vulnerability-management.md` | 2026-01-30 |
| [Physical Security](../../security/physical/physical-security.md) | `security/physical/physical-security.md` | 2026-01-29 |

---

## PCI DSS v4.0.1 Requirement Mapping

### Requirement 1: Install and Maintain Network Security Controls

| Req ID | Requirement Summary | Status | Supporting Document | Section Reference | Evidence | Gaps/Notes |
|--------|---------------------|--------|---------------------|-------------------|----------|------------|
| 1.1.1 | Security policies documented, up to date, in use, known | **Compliant** | [Network Security](../../security/digital/network-security.md), [Security Policy & Awareness](../../security/digital/security-policy-awareness.md) | [NS §4](../../security/digital/network-security.md#4-how-we-operate-this-control), [NS §8](../../security/digital/network-security.md#8-review--maintenance); [SPA §4.1-4.2](../../security/digital/security-policy-awareness.md#4-how-we-operate-this-control) | Document v1.2, quarterly review, Internal Portal availability, annual acknowledgment | Policies documented, reviewed quarterly, available in Internal Portal, personnel acknowledge annually |
| 1.1.2 | Roles and responsibilities documented | **Compliant** | [Network Security](../../security/digital/network-security.md) | [§3](../../security/digital/network-security.md#3-roles--responsibilities) | Roles table | CTO owner, Engineering operator, Change Approver defined |
| 1.2.1 | NSC configuration standards defined, implemented, maintained | **Compliant** | [Network Security](../../security/digital/network-security.md) | [§4.7](../../security/digital/network-security.md#47-nsc-configuration-standards) | NSC Configuration Standards document | Section 4.7 defines: approved protocols (TLS 1.2+), permitted ports (443, SSH restricted), prohibited configs, default deny, IaC change management |
| 1.2.2 | NSC changes approved via change control | **Compliant** | [Network Security](../../security/digital/network-security.md), [Deployment Control](../../security/digital/deployment-control.md) | [NS §4.7.5](../../security/digital/network-security.md#475-change-management-for-nsc) | GitHub PR approvals, Terraform state, GCP Audit Logs | IaC (Terraform) with GitHub PR approval required; auditable, traceable, revertable |
| 1.2.3 | Network diagram maintained | **Partially Compliant** | [Network Security](../../security/digital/network-security.md) | [§4.1](../../security/digital/network-security.md#41-network-architecture) | ASCII diagram | Diagram shows VPC segmentation and data paths; formal diagram tool recommended for QSA |
| 1.2.4 | Data flow diagram maintained | **Partially Compliant** | [Secure Development](../../security/digital/secure-development.md) | [§2](../../security/digital/secure-development.md#2-scope) | ASCII diagram | Shows PAN flow through Kraken; formal diagram tool recommended |
| 1.2.5 | Services, protocols, ports identified and approved | **Compliant** | [Network Security](../../security/digital/network-security.md) | [§4.3](../../security/digital/network-security.md#43-firewall-rules), [§4.7.1](../../security/digital/network-security.md#471-approved-protocols), [§4.7.2](../../security/digital/network-security.md#472-permitted-ports) | Firewall rules table, Permitted Ports table | Ports documented (443 HTTPS, 22 SSH restricted), business need documented per rule, HTTPS required |
| 1.2.6 | Insecure services have security features | **Not Applicable** | [Network Security](../../security/digital/network-security.md) | [§4.7.3](../../security/digital/network-security.md#473-prohibited-configurations) | Prohibited Configurations table | Insecure protocols explicitly prohibited (HTTP, FTP, Telnet, TLS 1.0/1.1) |
| 1.2.7 | NSC configurations reviewed every 6 months | **Compliant** | [Network Security](../../security/digital/network-security.md) | [§4.7.6](../../security/digital/network-security.md#476-nsc-configuration-review), [§8](../../security/digital/network-security.md#8-review--maintenance) | NSC review checklist, Next NSC Review date | 6-month NSC configuration review process documented with checklist; next review 2026-07-29 |
| 1.2.8 | NSC configuration files secured | **Compliant** | [Network Security](../../security/digital/network-security.md), [Access Control](../../security/digital/access-control.md) | [NS §4.7.5](../../security/digital/network-security.md#475-change-management-for-nsc) | GitHub access controls, Terraform state in GCS, PR approvals | IaC files in GitHub with access controls; Terraform state versioned in GCS bucket; changes require PR approval |
| 1.3.1 | Inbound CDE traffic restricted | **Compliant** | [Network Security](../../security/digital/network-security.md) | [§4.3](../../security/digital/network-security.md#43-firewall-rules) | Kraken VPC Firewall table, Default deny | Only DASH Main VPC and specific gateway IPs allowed; default deny all |
| 1.3.2 | Outbound CDE traffic restricted | **Compliant** | [Network Security](../../security/digital/network-security.md) | [§4.3](../../security/digital/network-security.md#43-firewall-rules) | Kraken VPC Firewall table, Default deny | Only Soepay, GP, GCP KMS allowed; default deny all |
| 1.3.3 | NSCs between wireless and CDE | **Compliant** | [Physical Security](../../security/physical/physical-security.md), [Network Security](../../security/digital/network-security.md) | [NS §4.3](../../security/digital/network-security.md#43-firewall-rules) | No direct WiFi-to-CDE path | CDE in GCP; office WiFi has no direct path to GCP VPCs |
| 1.4.1 | NSCs between trusted/untrusted networks | **Compliant** | [Network Security](../../security/digital/network-security.md) | [§4.4](../../security/digital/network-security.md#44-waf-configuration), [§4.7.4](../../security/digital/network-security.md#474-default-configuration-requirements) | Cloud Armor WAF, GCP Firewall | WAF at internet edge; firewalls at VPC boundaries; stateful inspection enabled |
| 1.4.2 | Inbound untrusted traffic restricted | **Compliant** | [Network Security](../../security/digital/network-security.md) | [§4.4](../../security/digital/network-security.md#44-waf-configuration) | WAF rules, Firewall rules | DDoS protection, OWASP Top 10 rules, rate limiting; only authorized traffic allowed |
| 1.4.3 | Anti-spoofing measures | **Compliant** | [Network Security](../../security/digital/network-security.md) | [§4.7.4](../../security/digital/network-security.md#474-default-configuration-requirements) | Default Configuration Requirements table | Anti-spoofing documented as enabled (GCP default for VPC) |
| 1.4.4 | CHD systems not directly accessible from untrusted | **Compliant** | [Network Security](../../security/digital/network-security.md) | [§4.2](../../security/digital/network-security.md#42-vpc-segmentation) | VPC Segmentation table, architecture diagram | Kraken VPC isolated; internet traffic passes through WAF and DASH Main VPC first |
| 1.4.5 | Internal IPs/routing limited disclosure | **Partially Compliant** | [Network Security](../../security/digital/network-security.md) | - | - | Not explicitly addressed in documentation; GCP does not expose internal IPs by default but no formal control documented |
| 1.5.1 | Security controls on devices connecting to CDE | **Not Compliant** | - | - | - | No endpoint security documentation; requires Endpoint Security Policy |

### Requirement 2: Apply Secure Configurations

| Req ID | Requirement Summary | Status | Supporting Document | Section Reference | Evidence | Gaps/Notes |
|--------|---------------------|--------|---------------------|-------------------|----------|------------|
| 2.1.1 | Security policies documented | **Compliant** | [Access Control](../../security/digital/access-control.md), [Security Policy & Awareness](../../security/digital/security-policy-awareness.md) | [AC §4](../../security/digital/access-control.md#4-how-we-operate-this-control), [SPA §4.1](../../security/digital/security-policy-awareness.md#41-policy-documentation) | Documents in Internal Portal | Policies documented and disseminated via Internal Portal |
| 2.1.2 | Roles documented | **Compliant** | [Access Control](../../security/digital/access-control.md) | [§3](../../security/digital/access-control.md#3-roles--responsibilities) | Roles table | - |
| 2.2.1 | Configuration standards developed and maintained | **Compliant** | [Network Security](../../security/digital/network-security.md), [Deployment Control](../../security/digital/deployment-control.md) | [NS §4.7](../../security/digital/network-security.md#47-nsc-configuration-standards) | NSC Configuration Standards, IaC | **GCP Inherited:** OS/runtime hardening for Cloud Run/Functions. App config via IaC |
| 2.2.2 | Vendor default accounts managed | **Compliant (GCP Inherited)** | [Third-Party Risk](../../security/digital/third-party-risk.md) | [§4.6](../../security/digital/third-party-risk.md#46-gcp-as-pci-dss-service-provider) | GCP manages serverless | **GCP Inherited:** Cloud Run/Functions have no OS-level default accounts. Service accounts managed via IAM |
| 2.2.3 | Primary functions isolated/secured | **Compliant** | [Network Security](../../security/digital/network-security.md) | [§4.2](../../security/digital/network-security.md#42-vpc-segmentation) | VPC segmentation | Kraken isolated from DASH Main |
| 2.2.4 | Only necessary services enabled | **Compliant (GCP Inherited)** | [Network Security](../../security/digital/network-security.md) | [§4.5](../../security/digital/network-security.md#45-gcp-security-services) | Cloud Run/Functions | **GCP Inherited:** Serverless = no unnecessary services. Only application code runs |
| 2.2.5 | Insecure services documented with mitigations | **Not Applicable** | [Network Security](../../security/digital/network-security.md) | [§4.7.3](../../security/digital/network-security.md#473-prohibited-configurations) | - | Insecure protocols prohibited |
| 2.2.6 | Security parameters prevent misuse | **Compliant** | [Secure Development](../../security/digital/secure-development.md), [Network Security](../../security/digital/network-security.md) | [SD §4](../../security/digital/secure-development.md#4-how-we-operate-this-control), [NS §4.7](../../security/digital/network-security.md#47-nsc-configuration-standards) | Code scanning, config standards | SonarQube scans; NSC config standards documented |
| 2.2.7 | Non-console admin access encrypted | **Compliant** | [Network Security](../../security/digital/network-security.md) | [§4.7.1](../../security/digital/network-security.md#471-approved-protocols) | TLS 1.2+ required | All access via HTTPS (port 443), TLS 1.2 minimum |
| 2.3.1 | Wireless defaults changed | **Not Applicable** | [Physical Security](../../security/physical/physical-security.md) | - | - | No wireless in CDE |
| 2.3.2 | Wireless encryption keys changed | **Not Applicable** | - | - | - | No wireless in CDE |

### Requirement 3: Protect Stored Account Data

**Scope Context:** DASH uses tokenization to minimize PAN storage. Only **Kraken** (payment module) stores PAN, encrypted via GCP KMS. All other systems (DASH Core, Admin Portal, Mobile App) handle tokens only.

| Req ID | Requirement Summary | Status | Supporting Document | Section Reference | Evidence | Gaps/Notes |
|--------|---------------------|--------|---------------------|-------------------|----------|------------|
| 3.1.1 | Security policies documented, up to date, in use, known | **Compliant** | [Crypto Key Mgmt](../../security/digital/cryptographic-key-management.md), [Secure Development](../../security/digital/secure-development.md), [Admin Portal Access](../../security/digital/admin-portal-access.md) | [CKM §1](../../security/digital/cryptographic-key-management.md#1-purpose), [CKM §8](../../security/digital/cryptographic-key-management.md#8-review--maintenance) | Documents v1.0-1.1, quarterly review, Internal Portal availability | Policies for stored data protection documented across multiple control docs |
| 3.1.2 | Roles documented | **Compliant** | [Crypto Key Mgmt](../../security/digital/cryptographic-key-management.md), [Secure Development](../../security/digital/secure-development.md) | [CKM §3](../../security/digital/cryptographic-key-management.md#3-roles--responsibilities), [SD §3](../../security/digital/secure-development.md#3-roles--responsibilities) | Roles tables | CTO owner, Engineering operator, Key Administrator defined |
| 3.2.1 | Data retention minimized with disposal policies | **Partially Compliant** | [Secure Development](../../security/digital/secure-development.md), [Company Overview](../../security/company-overview.md) | [SD §4.4](../../security/digital/secure-development.md#44-data-minimization), [CO §3.1.1](../../security/company-overview.md#311-cardholder-data-storage) | Tokenization architecture, CHD storage table | Cardholder data storage documented (name, PAN, expiry). **Gap:** No formal data retention policy with specific periods, quarterly verification, or secure deletion procedures |
| 3.3.1 | SAD not stored after authorization | **Compliant** | [Company Overview](../../security/company-overview.md), [Secure Development](../../security/digital/secure-development.md) | [CO §3.1.1](../../security/company-overview.md#311-cardholder-data-storage), [SD §4.5](../../security/digital/secure-development.md#45-tokenization-flow) | CHD storage table, Tokenization flow | Explicitly documented: CVV used only during 3DS authentication then discarded; never stored in database, logs, or persistent storage |
| 3.3.1.1 | Full track data not stored | **Compliant** | [Company Overview](../../security/company-overview.md) | [§3.1.1](../../security/company-overview.md#311-cardholder-data-storage) | CHD storage table | Explicitly documented: "Not applicable; no magnetic stripe or chip data captured" - DASH processes online/mobile payments only |
| 3.3.1.2 | CVV not stored | **Compliant** | [Company Overview](../../security/company-overview.md), [Secure Development](../../security/digital/secure-development.md) | [CO §3.1.1](../../security/company-overview.md#311-cardholder-data-storage), [CO §3.2](../../security/company-overview.md#32-card-registration-flow), [SD §4.5](../../security/digital/secure-development.md#45-tokenization-flow) | CHD storage table, Card Registration Flow | Explicitly documented: CVV "Never stored; used only during initial 3DS authentication, then discarded" |
| 3.3.1.3 | PIN/PIN block not stored | **Not Applicable** | [Company Overview](../../security/company-overview.md) | [§3.1.1](../../security/company-overview.md#311-cardholder-data-storage) | CHD storage table | "Not applicable; DASH processes online/mobile payments only (no PIN entry)" |
| 3.3.2 | SAD encrypted if stored prior to authorization | **Not Applicable** | [Secure Development](../../security/digital/secure-development.md) | [§4.5](../../security/digital/secure-development.md#45-tokenization-flow) | - | SAD not stored at any time per tokenization design |
| 3.3.3 | Issuer SAD storage requirements | **Not Applicable** | - | - | - | DASH is not an issuer or issuing services provider |
| 3.4.1 | PAN masked when displayed (BIN + last 4 max) | **Compliant** | [Admin Portal Access](../../security/digital/admin-portal-access.md) | [§4.5](../../security/digital/admin-portal-access.md#45-transaction-operations) | Data Visibility table | "Full PAN: **No** (masked/tokenized)" - Admin Portal never displays full PAN |
| 3.4.2 | Remote access prevents PAN copy/relocation | **Partially Compliant** | [Admin Portal Access](../../security/digital/admin-portal-access.md) | [§4.5](../../security/digital/admin-portal-access.md#45-transaction-operations) | No full PAN displayed | PAN not visible = cannot copy. **Gap:** No explicit technical controls (DLP) documented for remote access scenarios |
| 3.5.1 | PAN rendered unreadable anywhere stored | **Compliant** | [Crypto Key Mgmt](../../security/digital/cryptographic-key-management.md), [Secure Development](../../security/digital/secure-development.md) | [CKM §4.2](../../security/digital/cryptographic-key-management.md#42-key-storage), [SD §4.4](../../security/digital/secure-development.md#44-data-minimization) | GCP KMS encryption, Key Usage diagram | PAN encrypted via GCP KMS with AES-256-GCM; stored as ciphertext in Kraken DB |
| 3.5.1.1 | Keyed cryptographic hashes with key management | **Not Applicable** | [Crypto Key Mgmt](../../security/digital/cryptographic-key-management.md) | - | - | DASH uses encryption (not hashing) to render PAN unreadable |
| 3.5.1.2 | Disk encryption with additional PAN protection | **Compliant** | [Crypto Key Mgmt](../../security/digital/cryptographic-key-management.md) | [§4.2](../../security/digital/cryptographic-key-management.md#42-key-storage) | GCP KMS field-level encryption | PAN encrypted at field level (not just disk); GCP provides disk encryption as additional layer |
| 3.5.1.3 | Disk encryption managed properly | **Compliant** | [Crypto Key Mgmt](../../security/digital/cryptographic-key-management.md) | [§4.2](../../security/digital/cryptographic-key-management.md#42-key-storage) | GCP KMS architecture | Logical access via IAM (separate from OS); decryption keys in KMS (not user accounts); authentication via Workload Identity |
| 3.6.1 | Key protection procedures defined | **Compliant** | [Crypto Key Mgmt](../../security/digital/cryptographic-key-management.md) | [§4.1-4.5](../../security/digital/cryptographic-key-management.md#4-how-we-operate-this-control) | Full lifecycle documented, IAM access table | Access restricted to Kraken service account; keys stored in HSM; procedures for generation, storage, rotation, destruction |
| 3.6.1.1 | Cryptographic architecture documented (Service Providers) | **Compliant** | [Crypto Key Mgmt](../../security/digital/cryptographic-key-management.md) | [§2 (Key Inventory)](../../security/digital/cryptographic-key-management.md#2-scope) | Key inventory table | Algorithm (AES-256-GCM assumed), location (GCP KMS), rotation period (90 days) documented. **Minor gap:** Algorithm marked as [ASSUMPTION] |
| 3.6.1.2 | Secret/private keys stored securely | **Compliant** | [Crypto Key Mgmt](../../security/digital/cryptographic-key-management.md) | [§4.2](../../security/digital/cryptographic-key-management.md#42-key-storage) | GCP KMS HSM diagram, FIPS 140-2 L3 | Keys stored exclusively in HSM; **key material cannot be exported by anyone** (hardware-enforced); no human access to key bytes |
| 3.6.1.3 | Access to cleartext key components restricted | **Compliant** | [Crypto Key Mgmt](../../security/digital/cryptographic-key-management.md) | [§3](../../security/digital/cryptographic-key-management.md#3-roles--responsibilities) | IAM access table, "No Human Access" section | **No human has access to key material** - GCP KMS architecture ensures keys never leave HSM; all personnel (including CTO and Google) have zero access to actual key bytes |
| 3.6.1.4 | Keys stored in fewest locations | **Compliant** | [Crypto Key Mgmt](../../security/digital/cryptographic-key-management.md) | [§4.2](../../security/digital/cryptographic-key-management.md#42-key-storage) | GCP KMS only | Single location: GCP KMS. Key material never leaves Google's HSM infrastructure |
| 3.7.1 | Strong key generation | **Compliant** | [Crypto Key Mgmt](../../security/digital/cryptographic-key-management.md) | [§4.1](../../security/digital/cryptographic-key-management.md#41-key-generation) | GCP KMS HSM | "Keys generated within GCP KMS using FIPS 140-2 Level 3 validated HSMs" |
| 3.7.2 | Secure key distribution | **Compliant** | [Crypto Key Mgmt](../../security/digital/cryptographic-key-management.md) | [§4.2](../../security/digital/cryptographic-key-management.md#42-key-storage) | GCP KMS API | "Key material never leaves Google's HSM infrastructure"; access via KMS API only (no manual distribution) |
| 3.7.3 | Secure key storage | **Compliant** | [Crypto Key Mgmt](../../security/digital/cryptographic-key-management.md) | [§4.2](../../security/digital/cryptographic-key-management.md#42-key-storage) | GCP KMS HSM | Hardware-level protection; FIPS 140-2 Level 3 validated |
| 3.7.4 | Key changes at end of cryptoperiod | **Compliant** | [Crypto Key Mgmt](../../security/digital/cryptographic-key-management.md) | [§4.3](../../security/digital/cryptographic-key-management.md#43-key-rotation-automated) | 90-day automated rotation | Cryptoperiod defined (90 days); automatic rotation via GCP KMS; rotation events logged |
| 3.7.5 | Key retirement/replacement/destruction | **Compliant** | [Crypto Key Mgmt](../../security/digital/cryptographic-key-management.md) | [§4.3](../../security/digital/cryptographic-key-management.md#43-key-rotation-automated), [§4.5](../../security/digital/cryptographic-key-management.md#45-key-destruction), [§4.6](../../security/digital/cryptographic-key-management.md#46-key-compromise-response) | Destruction process, compromise response | Retired keys retained for decryption; destruction requires CTO approval + 24hr delay; compromise triggers immediate rotation |
| 3.7.6 | Split knowledge and dual control for manual ops | **Compliant (GCP Inherited)** | [Crypto Key Mgmt](../../security/digital/cryptographic-key-management.md) | [§4.1](../../security/digital/cryptographic-key-management.md#41-key-generation), [§4.5](../../security/digital/cryptographic-key-management.md#45-key-destruction) | GCP KMS | No manual key operations - all key management via GCP KMS. Key creation requires PR approval (dual control); destruction requires CTO approval |
| 3.7.7 | Prevention of unauthorized key substitution | **Compliant** | [Crypto Key Mgmt](../../security/digital/cryptographic-key-management.md) | [§4.1](../../security/digital/cryptographic-key-management.md#41-key-generation), [§3](../../security/digital/cryptographic-key-management.md#3-roles--responsibilities) | IAM access controls, PR approval | Key creation requires Terraform PR with 2 engineer + CTO approval; IAM restricts who can modify keys |
| 3.7.8 | Key custodian acknowledgment | **Not Compliant** | - | - | - | **Gap:** No formal written acknowledgment from key custodians documented |
| 3.7.9 | Key sharing guidance for service providers | **Not Applicable** | - | - | - | DASH does not share cryptographic keys with customers |

### Requirement 4: Protect Cardholder Data with Strong Cryptography During Transmission

**Scope Context:** DASH transmits cardholder data only within the Kraken module and to payment gateways (Soepay, GP). All transmission uses TLS 1.2+ encryption. No PAN is transmitted via email, messaging, or wireless networks.

| Req ID | Requirement Summary | Status | Supporting Document | Section Reference | Evidence | Gaps/Notes |
|--------|---------------------|--------|---------------------|-------------------|----------|------------|
| 4.1.1 | Security policies for Req 4 documented, up to date, in use, known | **Compliant** | [Network Security](../../security/digital/network-security.md), [Company Overview](../../security/company-overview.md) | [NS §4.6](../../security/digital/network-security.md#46-encryption-in-transit), [NS §4.8.1](../../security/digital/network-security.md#481-approved-protocols), [NS §8.2](../../security/digital/network-security.md#8-review--maintenance) | Document v1.3, quarterly review, Internal Portal | Encryption in transit policies documented with TLS requirements, prohibited protocols, and certificate management |
| 4.1.2 | Roles and responsibilities documented | **Compliant** | [Network Security](../../security/digital/network-security.md) | [§3](../../security/digital/network-security.md#3-roles--responsibilities) | Roles table | CTO owner, Engineering operator for network security including encryption |
| 4.2.1 | Strong cryptography for PAN transmission over open/public networks | **Compliant** | [Network Security](../../security/digital/network-security.md), [Company Overview](../../security/company-overview.md) | [NS §4.6.1](../../security/digital/network-security.md#461-cloud-sql-proxy), [NS §4.6.2](../../security/digital/network-security.md#462-httpstls-encryption), [NS §4.8.1](../../security/digital/network-security.md#481-approved-protocols), [NS §4.8.3](../../security/digital/network-security.md#483-prohibited-configurations) | TLS Configuration table, External Traffic table, Prohibited Configurations | **Fully documented:** (1) Only trusted keys via GCP Certificate Manager, (2) TLS 1.2 minimum/TLS 1.3 preferred, (3) Insecure protocols prohibited (TLS 1.0/1.1/SSL explicitly banned), (4) GCP-managed modern cipher suites only |
| 4.2.1.1 | Inventory of trusted keys and certificates maintained | **Compliant** | [Network Security](../../security/digital/network-security.md) | [§4.6.3](../../security/digital/network-security.md#463-ssltls-certificate-management) | Certificate Inventory table, Certificate Lifecycle table | **Certificate inventory documented:** All services listed (Cloud Run, Cloud Functions, Cloud SQL Proxy, Load Balancer, GCP APIs); all GCP-managed with automatic renewal; Google Trust Services CA |
| 4.2.1.2 | Wireless networks transmitting PAN use strong cryptography | **Not Applicable** | [Network Security](../../security/digital/network-security.md), [Physical Security](../../security/physical/physical-security.md) | [NS §4.8.3](../../security/digital/network-security.md#483-prohibited-configurations) | No wireless CHD transmission | CDE is entirely cloud-hosted (GCP); office WiFi has no connection to CDE; no wireless transmission of PAN |
| 4.2.2 | PAN secured with strong cryptography via end-user messaging | **Compliant** | [Company Overview](../../security/company-overview.md), [Secure Development](../../security/digital/secure-development.md) | [CO §3.1.1](../../security/company-overview.md#311-cardholder-data-storage), [SD §4.4](../../security/digital/secure-development.md#44-data-minimization) | CHD storage table, Data Minimization | PAN is **never transmitted via email, SMS, or messaging**. Kraken receives PAN only via secure HTTPS form; transmits only to payment gateways via HTTPS. Tokens used for all other communication |

#### Requirement 4 Supporting Evidence Summary

**Encryption Standards Documented:**

| Standard | Documentation | Evidence Location |
|----------|--------------|-------------------|
| TLS 1.2 minimum | network-security.md Section 4.8.1 | Approved Protocols table |
| TLS 1.3 preferred | network-security.md Section 4.6.2 | TLS Configuration table |
| Prohibited protocols | network-security.md Section 4.8.3 | Prohibited Configurations table |
| Certificate management | network-security.md Section 4.8.1 | GCP Certificate Manager |
| HTTPS enforcement | network-security.md Section 4.6.2 | Enforcement section |

**Transmission Paths for Cardholder Data:**

| Path | Encryption | Documentation |
|------|-----------|---------------|
| User → Kraken (card entry) | HTTPS/TLS 1.2+ | network-security.md 4.6.2 |
| Kraken → Cloud SQL (storage) | Cloud SQL Proxy (auto TLS) | network-security.md 4.6.1 |
| Kraken → Payment Gateway | HTTPS/TLS 1.2+ | network-security.md 4.6.2 |
| Kraken → GCP KMS (encryption) | HTTPS/TLS 1.2+ | network-security.md 4.6.2 |

### Requirement 5: Protect All Systems and Networks from Malicious Software

**Scope Context:** DASH's CDE runs on **GCP Cloud Run** and **Cloud Functions** (fully managed serverless). This fundamentally changes Requirement 5 applicability - there is no persistent OS to protect from traditional malware. GCP provides infrastructure-level threat detection and protection as part of its PCI DSS 4.0.1 Level 1 Service Provider compliance.

**GCP Serverless Security Benefits:**
- **No persistent OS:** Container instances are ephemeral; malware cannot persist across invocations
- **Immutable deployments:** Container images are read-only at runtime
- **Container Threat Detection:** Monitors Cloud Run for suspicious activity at runtime
- **Security Command Center:** Continuous vulnerability and threat scanning
- **Web Security Scanner:** Automated web application vulnerability detection

| Req ID | Requirement Summary | Status | Supporting Document | Section Reference | Evidence | Gaps/Notes |
|--------|---------------------|--------|---------------------|-------------------|----------|------------|
| 5.1.1 | Security policies and procedures for Req 5 documented, up to date, in use, known | **Compliant** | [Vulnerability Management](../../security/digital/vulnerability-management.md), [Third-Party Risk](../../security/digital/third-party-risk.md) | [VM §2](../../security/digital/vulnerability-management.md#2-scope), [VM §4.1](../../security/digital/vulnerability-management.md#41-gcp-security-command-center); [TPR §4.6](../../security/digital/third-party-risk.md#46-gcp-as-pci-dss-service-provider) | GCP inherited controls documentation, Internal Portal availability | Policies documented: serverless has no OS-level malware risk; GCP provides Container Threat Detection and Security Command Center. GCP PCI DSS inherited controls table in third-party-risk.md |
| 5.1.2 | Roles and responsibilities for Req 5 documented | **Compliant** | [Vulnerability Management](../../security/digital/vulnerability-management.md) | [§3](../../security/digital/vulnerability-management.md#3-roles--responsibilities) | Roles table | CTO owns control; Engineering operates and monitors Security Command Center; GCP manages infrastructure security |
| 5.2.1 | Anti-malware deployed on all system components (except those evaluated as not at risk per 5.2.3) | **Compliant (GCP Inherited)** | [Vulnerability Management](../../security/digital/vulnerability-management.md), [Third-Party Risk](../../security/digital/third-party-risk.md) | [VM §4.1](../../security/digital/vulnerability-management.md#41-gcp-security-command-center), [TPR §4.6](../../security/digital/third-party-risk.md#46-gcp-as-pci-dss-service-provider) | GCP Security Command Center, Container Threat Detection | **GCP Inherited:** (1) Container Threat Detection monitors Cloud Run/Functions for malicious activity, (2) Security Health Analytics scans for misconfigurations, (3) Event Threat Detection detects suspicious patterns. Documented in inherited controls table |
| 5.2.2 | Anti-malware detects all known types and removes/blocks/contains them | **Compliant (GCP Inherited)** | [Third-Party Risk](../../security/digital/third-party-risk.md) | [§4.6](../../security/digital/third-party-risk.md#46-gcp-as-pci-dss-service-provider) | GCP Security Command Center documentation | **GCP Inherited:** Container Threat Detection detects: malicious scripts, reverse shells, crypto mining, privilege escalation. GCP continuously updates threat signatures |
| 5.2.3 | Systems not at risk for malware evaluated periodically | **Compliant (GCP Inherited)** | [Vulnerability Management](../../security/digital/vulnerability-management.md) | [§2 (Out of Scope)](../../security/digital/vulnerability-management.md#2-scope) | Serverless architecture documented | Serverless (Cloud Run/Functions) has no persistent OS - explicitly documented as "no OS-level malware risk" in scope section. GCP continuously evaluates threats |
| 5.2.3.1 | Frequency of evaluations defined via targeted risk analysis (per 12.3.1) | **Compliant (GCP Inherited)** | [Vulnerability Management](../../security/digital/vulnerability-management.md), [Third-Party Risk](../../security/digital/third-party-risk.md) | [VM §2](../../security/digital/vulnerability-management.md#2-scope), [TPR §4.6](../../security/digital/third-party-risk.md#46-gcp-as-pci-dss-service-provider) | Continuous GCP monitoring | **GCP Inherited:** Real-time continuous monitoring eliminates need for periodic risk-based evaluation - GCP monitors 24/7. Annual GCP compliance review documented in third-party-risk.md |
| 5.3.1 | Anti-malware solution kept current via automatic updates | **Compliant (GCP Inherited)** | [Third-Party Risk](../../security/digital/third-party-risk.md) | [§4.6](../../security/digital/third-party-risk.md#46-gcp-as-pci-dss-service-provider) | GCP managed services | **GCP Inherited:** GCP manages all threat detection updates for Security Command Center and Container Threat Detection. DASH has no action required |
| 5.3.2 | Anti-malware performs periodic/real-time scans OR continuous behavioral analysis | **Compliant (GCP Inherited)** | [Vulnerability Management](../../security/digital/vulnerability-management.md) | [§4.1](../../security/digital/vulnerability-management.md#41-gcp-security-command-center) | Security Command Center continuous scanning | **GCP Inherited:** (1) Container Threat Detection performs continuous behavioral analysis at runtime, (2) Security Health Analytics runs continuous configuration scanning, (3) Web Security Scanner performs periodic web vulnerability scans |
| 5.3.2.1 | Frequency of periodic scans defined via targeted risk analysis (if applicable) | **Compliant (GCP Inherited)** | - | - | Continuous monitoring | **GCP Inherited:** Not applicable - GCP uses continuous/real-time monitoring (not periodic scans), which exceeds periodic scanning requirements |
| 5.3.3 | Removable media automatically scanned when inserted/connected | **Not Applicable** | [Physical Security](../../security/physical/physical-security.md), [Vulnerability Management](../../security/digital/vulnerability-management.md) | [PS §4.4](../../security/physical/physical-security.md#44-data-handling---no-physical-data), [VM §2](../../security/digital/vulnerability-management.md#2-scope) | No removable media policy | (1) CDE is entirely cloud-hosted - no physical media access to servers, (2) Serverless containers cannot access USB/removable media, (3) Office policy prohibits removable media with CHD |
| 5.3.4 | Anti-malware audit logs enabled and retained per 10.5.1 | **Compliant (GCP Inherited)** | [Logging & Monitoring](../../security/digital/logging-monitoring.md), [Third-Party Risk](../../security/digital/third-party-risk.md) | [LM §4.1](../../security/digital/logging-monitoring.md#41-log-collection), [TPR §4.6](../../security/digital/third-party-risk.md#46-gcp-as-pci-dss-service-provider) | Security Command Center findings, Cloud Audit Logs | **GCP Inherited:** (1) Security Command Center findings retained with timestamps, (2) Container Threat Detection events logged to Cloud Audit Logs, (3) Logs exportable to Cloud Logging for extended retention. **Note:** Ensure retention meets 10.5.1 requirements |
| 5.3.5 | Anti-malware cannot be disabled/altered by users unless authorized | **Compliant (GCP Inherited)** | [Third-Party Risk](../../security/digital/third-party-risk.md) | [§4.6](../../security/digital/third-party-risk.md#46-gcp-as-pci-dss-service-provider) | GCP managed services | **GCP Inherited:** Container Threat Detection and Security Command Center are platform services - DASH users cannot disable them for managed Cloud Run/Functions. Requires GCP organization-level admin to disable |
| 5.4.1 | Processes and automated mechanisms detect/protect personnel against phishing | **Compliant** | [Security Policy & Awareness](../../security/digital/security-policy-awareness.md) | [§4.3](../../security/digital/security-policy-awareness.md#43-security-awareness-training), [§4.4](../../security/digital/security-policy-awareness.md#44-email-and-phishing-protection) | Microsoft 365 Security, Outlook phishing reporting, Training content | **Fully documented:** (1) Microsoft 365 Safe Links and Safe Attachments enabled, (2) Anti-phishing policies with ML detection, (3) DMARC/DKIM/SPF configured, (4) Phishing reporting via Outlook (right-click → Report phishing), (5) Security awareness training covers phishing recognition, (6) Simulated phishing exercises conducted |

#### Requirement 5 Supporting Evidence Summary

**GCP Inherited Anti-Malware Controls:**

| GCP Service | PCI Req 5 Coverage | What It Does |
|-------------|-------------------|--------------|
| **Container Threat Detection** | 5.2.1, 5.2.2, 5.3.2 | Detects malicious scripts, reverse shells, crypto mining at runtime in Cloud Run |
| **Security Health Analytics** | 5.2.1, 5.3.2 | Continuous misconfiguration scanning |
| **Event Threat Detection** | 5.2.1, 5.2.2 | Detects suspicious activity patterns in logs |
| **Web Security Scanner** | 5.3.2 | Automated web vulnerability scanning |
| **Container Analysis** | 5.2.1 | Scans container images for vulnerabilities |

**Why Traditional Anti-Malware is Not Applicable:**

| Traditional Requirement | DASH Serverless Reality | Compensating Control |
|------------------------|------------------------|---------------------|
| Install AV on servers | No persistent OS on Cloud Run/Functions | Container Threat Detection |
| Update AV signatures | No AV software installed | GCP manages threat detection updates |
| Periodic malware scans | Containers are ephemeral | Continuous behavioral analysis |
| Removable media scanning | No physical media access | Cloud-only architecture |

**Documentation References:**
- vulnerability-management.md Section 2: "OS patching: Fully managed by GCP - no customer responsibility"
- vulnerability-management.md Section 4.1: Lists GCP Security Command Center capabilities
- third-party-risk.md Section 4.6: GCP PCI inherited controls table (Req 5 explicitly listed)

### Requirement 6: Develop and Maintain Secure Systems and Software

**Scope Context:** DASH develops custom software (DASH Main, Kraken, Admin Portal) using Node.js. All code is reviewed via PR process, scanned automatically (SonarQube, npm audit), and deployed via GitHub Actions. Public-facing applications are protected by GCP Cloud Armor WAF.

| Req ID | Requirement Summary | Status | Supporting Document | Section Reference | Evidence | Gaps/Notes |
|--------|---------------------|--------|---------------------|-------------------|----------|------------|
| 6.1.1 | Security policies and procedures for Req 6 documented, up to date, in use, known | **Compliant** | [Secure Development](../../security/digital/secure-development.md), [Deployment Control](../../security/digital/deployment-control.md), [Vulnerability Management](../../security/digital/vulnerability-management.md) | [SD §1](../../security/digital/secure-development.md#1-purpose), [SD §8](../../security/digital/secure-development.md#8-review--maintenance); [DC §1](../../security/digital/deployment-control.md#1-purpose), [DC §8](../../security/digital/deployment-control.md#8-review--maintenance) | Documents v1.1, quarterly review, Internal Portal | Secure development policies documented across three control documents; quarterly review cadence; available in Internal Portal |
| 6.1.2 | Roles and responsibilities for Req 6 documented, assigned, understood | **Compliant** | [Secure Development](../../security/digital/secure-development.md), [Deployment Control](../../security/digital/deployment-control.md), [Vulnerability Management](../../security/digital/vulnerability-management.md) | [SD §3](../../security/digital/secure-development.md#3-roles--responsibilities), [DC §3](../../security/digital/deployment-control.md#3-roles--responsibilities), [VM §3](../../security/digital/vulnerability-management.md#3-roles--responsibilities) | Roles tables | CTO owner; Engineering operator; Reviewer and Exception Approver roles defined |
| 6.2.1 | Bespoke/custom software developed securely (industry standards, PCI DSS, security at each SDLC stage) | **Compliant** | [Secure Development](../../security/digital/secure-development.md), [Security Training Guide](../../security/training/security-training-guide.md) | [SD §4.1-4.4](../../security/digital/secure-development.md#4-how-we-operate-this-control); [Training Module 9](../../security/training/security-training-guide.md#module-9-secure-development-practices) | ADR process, SonarQube, npm audit, Training Guide | (1) ADR process for security review before implementation, (2) SonarQube static analysis on every PR, (3) npm audit for dependency vulnerabilities, (4) Security considerations documented in ADRs, (5) Training covers OWASP Top 10 and secure coding |
| 6.2.2 | Software development personnel trained annually on software security, secure design, secure coding, security testing tools | **Compliant** | [Security Training Guide](../../security/training/security-training-guide.md), [Security Policy & Awareness](../../security/digital/security-policy-awareness.md) | [Training Module 9](../../security/training/security-training-guide.md#module-9-secure-development-practices); [SPA §4.3](../../security/digital/security-policy-awareness.md#43-security-awareness-training) | Training Guide Modules 9-10, Annual training schedule | **Newly documented:** Security Training Guide includes Engineering-specific modules: (1) Module 9: Secure Development Practices (OWASP Top 10, input validation, output encoding, auth patterns, secrets management), (2) Module 10: Logging Standards. Annual training in Q1 |
| 6.2.3 | Bespoke/custom software reviewed prior to release (secure coding guidelines, existing/emerging vulnerabilities, corrections implemented) | **Compliant** | [Secure Development](../../security/digital/secure-development.md), [Deployment Control](../../security/digital/deployment-control.md) | [SD §4.2](../../security/digital/secure-development.md#42-code-scanning); [DC §4.1](../../security/digital/deployment-control.md#41-change-control-workflow) | SonarQube reports, GitHub PR approvals | (1) Every PR scanned by SonarQube for security vulnerabilities, (2) Critical/High vulnerabilities block merge, (3) PR requires 2 engineer reviews + QA + executive approval, (4) Corrections required before merge |
| 6.2.3.1 | Manual code reviews by knowledgeable personnel other than author, approved by management | **Compliant** | [Deployment Control](../../security/digital/deployment-control.md) | [§4.1](../../security/digital/deployment-control.md#41-change-control-workflow) | GitHub PR history, Approval workflow | (1) PR requires 2 Engineering team members (not author), (2) 1 QA team member, (3) 1 Executive (CTO/Head of Product/CEO) approval - fulfills "management" requirement per PCI guidance |
| 6.2.4 | Software engineering techniques prevent common attacks (injection, data attacks, crypto attacks, business logic, access control, high-risk vulns) | **Compliant** | [Secure Development](../../security/digital/secure-development.md), [Security Training Guide](../../security/training/security-training-guide.md), [Vulnerability Management](../../security/digital/vulnerability-management.md) | [SD §4.2](../../security/digital/secure-development.md#42-code-scanning); [Training Module 9](../../security/training/security-training-guide.md#module-9-secure-development-practices); [VM §4.1](../../security/digital/vulnerability-management.md#41-gcp-security-command-center) | SonarQube rules, Training content, npm audit | **Documented prevention techniques:** (1) SonarQube detects SQL injection, XSS, CSRF, hardcoded secrets, (2) Training covers parameterized queries, output encoding, auth patterns, (3) npm audit blocks vulnerable dependencies, (4) ADR process reviews security implications |
| 6.3.1 | Security vulnerabilities identified using industry sources, assigned risk ranking (high-risk/critical identified) | **Compliant** | [Vulnerability Management](../../security/digital/vulnerability-management.md) | [§4.1](../../security/digital/vulnerability-management.md#41-gcp-security-command-center), [§4.3](../../security/digital/vulnerability-management.md#43-severity-classification) | npm audit (NVD/CVE), SonarQube, Severity matrix | (1) npm audit uses National Vulnerability Database, (2) SonarQube uses industry vulnerability databases, (3) Severity classification: Critical/High/Medium/Low, (4) Critical/High block merge, (5) GCP Security Command Center for infrastructure |
| 6.3.2 | Inventory of bespoke/custom software and third-party components maintained for vulnerability/patch management | **Compliant** | [System Component Inventory](../../security/digital/system-component-inventory.md), [Vulnerability Management](../../security/digital/vulnerability-management.md) | [SCI §4.1-4.4](../../security/digital/system-component-inventory.md#4-how-we-operate-this-control) | package.json, package-lock.json, Inventory document | **Documented:** (1) Application inventory (DASH Main, Kraken, Admin Portal), (2) Technology stack (Node.js 20, NestJS 10, TypeORM, PostgreSQL 15), (3) Dependencies tracked in package.json/lock files, (4) npm audit on every build, (5) Quarterly inventory review |
| 6.3.3 | System components protected from known vulnerabilities (critical patches within 1 month, others per risk assessment) | **Compliant** | [Vulnerability Management](../../security/digital/vulnerability-management.md), [Secure Development](../../security/digital/secure-development.md) | [VM §4.3](../../security/digital/vulnerability-management.md#43-severity-classification); [SD §4.2](../../security/digital/secure-development.md#42-code-scanning) | npm audit blocking, Severity response times | (1) Critical/High vulnerabilities block PR merge (immediate), (2) Medium within sprint, (3) Low in backlog. **GCP Inherited:** OS/runtime patching fully managed by GCP for Cloud Run/Functions |
| 6.4.1 | Public-facing web apps: Annual security assessment OR automated technical solution (WAF) | **Compliant** | [Network Security](../../security/digital/network-security.md), [Vulnerability Management](../../security/digital/vulnerability-management.md) | [NS §4.4](../../security/digital/network-security.md#44-waf-configuration); [VM §4.1](../../security/digital/vulnerability-management.md#41-gcp-security-command-center) | GCP Cloud Armor WAF configuration | **WAF approach:** (1) GCP Cloud Armor deployed in front of all public-facing apps, (2) OWASP Top 10 rule sets enabled, (3) DDoS protection, (4) Rate limiting, (5) Audit logs generated, (6) Configured to block attacks |
| 6.4.2 | Automated technical solution (WAF) continually detects and prevents web-based attacks | **Compliant** | [Network Security](../../security/digital/network-security.md) | [§4.4](../../security/digital/network-security.md#44-waf-configuration) | GCP Cloud Armor configuration, WAF logs | (1) Cloud Armor actively running 24/7, (2) Auto-updated by GCP, (3) Generates audit logs (GCP Cloud Logging), (4) Configured to block web-based attacks. This requirement effective March 2025 |
| 6.4.3 | Payment page scripts managed (authorized, integrity assured, inventory maintained) | **Partially Compliant** | [Secure Development](../../security/digital/secure-development.md) | [§4.5](../../security/digital/secure-development.md#45-tokenization-flow) | Kraken architecture (iframe) | Kraken uses secure iframe for payment entry (card details enter Kraken directly, not DASH main app). **Gap:** No explicit script inventory or integrity monitoring documentation. May be mitigated by Kraken iframe architecture |
| 6.5.1 | Changes to production follow procedures (reason, security impact, approval, testing, compliance testing, rollback) | **Compliant** | [Deployment Control](../../security/digital/deployment-control.md) | [§4.1](../../security/digital/deployment-control.md#41-change-control-workflow), [§4.2](../../security/digital/deployment-control.md#42-rollback-procedures) | GitHub PR workflow, Pipeline logs | (1) PR includes change description, (2) Security impact via code scanning, (3) Approval: 2 engineers + QA + executive, (4) Automated tests in pipeline, (5) SonarQube validates Req 6.2.4 compliance, (6) Rollback within 15 minutes documented |
| 6.5.2 | After significant change, PCI DSS requirements confirmed in place, documentation updated | **Partially Compliant** | [Deployment Control](../../security/digital/deployment-control.md) | [§4.1](../../security/digital/deployment-control.md#41-change-control-workflow) | PR process | Changes tested through pipeline. **Gap:** No explicit post-change PCI compliance verification checklist; relies on PR review and automated scanning |
| 6.5.3 | Pre-production environments separated from production with access controls | **Compliant** | [Deployment Control](../../security/digital/deployment-control.md), [Network Security](../../security/digital/network-security.md) | [DC §4.1](../../security/digital/deployment-control.md#41-change-control-workflow); [NS §4.2](../../security/digital/network-security.md#42-vpc-segmentation) | Environment separation, VPC isolation | (1) Dev, QA, Production environments documented, (2) Pipeline promotes code through environments, (3) Kraken VPC isolated from DASH Main VPC, (4) IAM controls access per environment |
| 6.5.4 | Roles separated between production and pre-production for accountability | **Compliant** | [Deployment Control](../../security/digital/deployment-control.md), [Access Control](../../security/digital/access-control.md) | [DC §4.1](../../security/digital/deployment-control.md#41-change-control-workflow); [AC §3](../../security/digital/access-control.md#3-roles--responsibilities) | PR approval workflow, IAM roles | (1) Developers create PRs, (2) Different reviewers approve, (3) Executive approval required for production, (4) Team access table shows differentiated access levels |
| 6.5.5 | Live PANs not used in pre-production environments (unless included in CDE) | **Compliant** | [Secure Development](../../security/digital/secure-development.md), [Security Training Guide](../../security/training/security-training-guide.md) | [SD §4.4](../../security/digital/secure-development.md#44-data-minimization); [Training Module 8](../../security/training/security-training-guide.md#module-8-qa-test-data-procedures) | Data minimization policy, Test data policy | (1) "Store only what we need" principle documented, (2) Security Training Module 8 explicitly states "Never use production data for testing", (3) Test card numbers provided for QA (4111..., 5555...), (4) Kraken stores PAN; test environments use test cards only |
| 6.5.6 | Test data and test accounts removed before production | **Partially Compliant** | [Security Training Guide](../../security/training/security-training-guide.md) | [Module 8](../../security/training/security-training-guide.md#module-8-qa-test-data-procedures) | Test environment policy | Training states test environments must not have production data and use separate credentials. **Gap:** No explicit procedure documented for removing test accounts before production release |

#### Requirement 6 Supporting Evidence Summary

**Secure Development Lifecycle:**

| SDLC Phase | Security Control | Documentation |
|------------|------------------|---------------|
| **Design** | Architecture Design Review (ADR) | secure-development.md Section 4.1 |
| **Development** | Secure coding training (OWASP Top 10) | security-training-guide.md Module 9 |
| **Code Review** | Manual review + SonarQube + npm audit | secure-development.md Section 4.2 |
| **Testing** | Automated tests, QA security checklist | security-training-guide.md Module 8 |
| **Deployment** | PR approval workflow, executive sign-off | deployment-control.md Section 4.1 |
| **Production** | WAF protection (Cloud Armor) | network-security.md Section 4.4 |

**Code Scanning Coverage:**

| Tool | What It Detects | PCI Req 6.2.4 Coverage |
|------|-----------------|------------------------|
| **SonarQube** | SQL injection, XSS, CSRF, hardcoded secrets, security hotspots | Injection, business logic, access control |
| **npm audit** | Vulnerable dependencies (CVE database) | High-risk vulnerabilities |
| **GCP Web Security Scanner** | Web vulnerabilities (inherited) | Web application attacks |

**Developer Training Content (Module 9):**

| Topic | PCI Req 6.2.2 Coverage |
|-------|------------------------|
| OWASP Top 10 | Software security relevant to job function |
| Input validation, output encoding | Secure coding techniques |
| Authentication/authorization patterns | Secure software design |
| Secrets management | Secure coding techniques |
| SonarQube usage | Security testing tools |

**Change Control Evidence:**

| Evidence | Location | Retention |
|----------|----------|-----------|
| PR approval history | GitHub | Indefinite |
| Code scan results | SonarQube, GitHub Actions | 90 days |
| Pipeline execution logs | GitHub Actions | 90 days |
| Executive approvals | GitHub PR, Slack screenshots | Indefinite |

### Requirement 7: Restrict Access to System Components and Cardholder Data by Business Need to Know

**Scope Context:** DASH controls access to system components and cardholder data through a combination of GCP IAM (for infrastructure) and Firebase Auth (for Admin Portal). Access is granted based on job function with quarterly reviews. Only Kraken stores PAN; other systems use tokens only.

**Access Control Architecture:**

| System | Access Control | Users | CHD Exposure |
|--------|---------------|-------|--------------|
| **GCP Infrastructure** | GCP IAM (RBAC) | Engineering (read-only logs), CTO/CEO (admin) | None (logs, infrastructure) |
| **Admin Portal** | Firebase Auth + Passkey MFA | CS Team | Masked PAN only (first 6, last 4) |
| **Kraken Database** | Service accounts only | No human access | PAN stored encrypted |
| **GitHub Repositories** | GitHub org access | Engineering, QA (read), Product (read) | Code only (no CHD) |

| Req ID | Requirement Summary | Status | Supporting Document | Section Reference | Evidence | Gaps/Notes |
|--------|---------------------|--------|---------------------|-------------------|----------|------------|
| 7.1.1 | Security policies and operational procedures for Req 7 documented, up to date, in use, known | **Compliant** | [Access Control](../../security/digital/access-control.md), [Admin Portal Access](../../security/digital/admin-portal-access.md) | [AC §1](../../security/digital/access-control.md#1-purpose), [AC §8](../../security/digital/access-control.md#8-review--maintenance); [APA full doc](../../security/digital/admin-portal-access.md) | Document v1.1, quarterly review, Internal Portal | Access control policies documented: (1) access-control.md covers infrastructure access, (2) admin-portal-access.md covers CHD access, (3) Quarterly review cadence, (4) Available in Internal Portal |
| 7.1.2 | Roles and responsibilities for Req 7 documented, assigned, understood | **Compliant** | [Access Control](../../security/digital/access-control.md), [Admin Portal Access](../../security/digital/admin-portal-access.md) | [AC §3](../../security/digital/access-control.md#3-roles--responsibilities), [APA §3](../../security/digital/admin-portal-access.md#3-roles--responsibilities) | Roles tables | **Documented roles:** Control Owner (CTO), Operator (Engineering), Deployment Approver (CTO/Product/CEO), QA Approver, Super Admin (CTO/CEO for Admin Portal) |
| 7.2.1 | Access control model defined: appropriate access per business needs, based on job classification, least privileges | **Compliant** | [Access Control](../../security/digital/access-control.md) | [§2 (Scope)](../../security/digital/access-control.md#2-scope), [§3](../../security/digital/access-control.md#3-roles--responsibilities) | GCP IAM inherited controls table, Team Access Levels table | **Documented:** (1) Role-based access control via GCP IAM, (2) Access by job function (Engineering, QA, Product, CS, CTO/CEO), (3) Least privilege: Engineering = read-only logs, CS = no GCP access, CTO/CEO = admin for break-glass |
| 7.2.2 | Access assigned based on job classification and least privileges | **Compliant** | [Access Control](../../security/digital/access-control.md) | [§3](../../security/digital/access-control.md#3-roles--responsibilities), [§4.1](../../security/digital/access-control.md#41-access-provisioning) | Team Access Levels table, Provisioning process | **Documented:** (1) New hires get access based on team membership, (2) Standard engineers: `roles/logging.viewer` (read-only), (3) QA/Product: No GCP access, read-only GitHub, (4) CS: No GCP/GitHub access |
| 7.2.3 | Required privileges approved by authorized personnel | **Compliant** | [Access Control](../../security/digital/access-control.md) | [§4.1](../../security/digital/access-control.md#41-access-provisioning) | Provisioning process, CTO approval | **Documented:** (1) CTO or Engineering lead creates accounts, (2) CTO approves access grants, (3) CTO conducts quarterly access reviews to verify appropriateness |
| 7.2.4 | All user accounts and access reviewed at least every 6 months | **Compliant** | [Access Control](../../security/digital/access-control.md) | [§4.4](../../security/digital/access-control.md#44-quarterly-access-review) | Quarterly access review process, Notion records | **Documented quarterly review:** (1) CTO exports GCP IAM user list, (2) Compares against HR roster, (3) Removes departed employees, (4) Verifies access levels appropriate for current roles, (5) Review recorded in Notion. **Exceeds requirement** (quarterly > 6 months) |
| 7.2.5 | Application and system accounts assigned based on least privileges, limited to required systems | **Compliant** | [Access Control](../../security/digital/access-control.md), [Crypto Key Mgmt](../../security/digital/cryptographic-key-management.md) | [AC §2](../../security/digital/access-control.md#2-scope), [CKM §3](../../security/digital/cryptographic-key-management.md#3-roles--responsibilities) | Cloud Run service accounts, IAM access table | **Documented:** (1) Each Cloud Run/Cloud Function has dedicated service account, (2) Workload Identity (no static keys), (3) Service accounts have minimal required permissions, (4) Kraken service account can only access PAN encryption keys |
| 7.2.5.1 | Application/system accounts reviewed periodically per targeted risk analysis | **Compliant** | [Access Control](../../security/digital/access-control.md) | [§4.4](../../security/digital/access-control.md#44-quarterly-access-review) | Quarterly review includes service accounts | **Quarterly review covers:** (1) All GCP IAM users including service accounts, (2) Access appropriateness verification, (3) Management acknowledgment (CTO). **Note:** Review frequency (quarterly) documented; formal risk analysis for frequency not yet created per 12.3.1 |
| 7.2.6 | User access to CHD repositories restricted via applications, only admins can directly query | **Compliant** | [Admin Portal Access](../../security/digital/admin-portal-access.md), [Secure Development](../../security/digital/secure-development.md) | [APA §4.5](../../security/digital/admin-portal-access.md#45-transaction-operations), [SD §4.4](../../security/digital/secure-development.md#44-data-minimization) | Data Visibility table, Tokenization architecture | **Fully documented:** (1) CS accesses CHD only via Admin Portal application, (2) Full PAN never displayed in Admin Portal (masked/tokenized), (3) No direct database access for users, (4) Only Kraken service account can query PAN in database, (5) CTO/Engineering can query logs but PAN is encrypted at rest |
| 7.3.1 | Access control system restricts access based on need to know, covers all system components | **Compliant** | [Access Control](../../security/digital/access-control.md), [Admin Portal Access](../../security/digital/admin-portal-access.md) | [AC §2](../../security/digital/access-control.md#2-scope), [APA §4](../../security/digital/admin-portal-access.md#4-how-we-operate-this-control) | GCP IAM, Firebase Auth, Team Access table | **Documented systems:** (1) GCP IAM controls infrastructure access (default deny, explicit grants), (2) Firebase Auth controls Admin Portal (MFA required), (3) GitHub controls code access, (4) All in-scope systems covered |
| 7.3.2 | Access control system enforces privileges based on job classification and function | **Compliant** | [Access Control](../../security/digital/access-control.md) | [§2](../../security/digital/access-control.md#2-scope), [§3](../../security/digital/access-control.md#3-roles--responsibilities) | GCP IAM inherited controls table, Team Access Levels | **Documented enforcement:** (1) GCP IAM RBAC enforces role-based access, (2) Roles assigned per team (Engineering → logging.viewer, CS → none), (3) Default deny for all resources, (4) Explicit grants required |
| 7.3.3 | Access control system set to "deny all" by default | **Compliant** | [Access Control](../../security/digital/access-control.md), [Network Security](../../security/digital/network-security.md) | [AC §2](../../security/digital/access-control.md#2-scope), [NS §4.8.4](../../security/digital/network-security.md#484-default-configuration-requirements) | GCP IAM inherited controls, Default Configuration Requirements table | **Documented:** (1) GCP IAM default: deny-all for resources, (2) Access must be explicitly granted, (3) Team Access table shows default is no access (CS has no GCP, no GitHub), (4) NSC default action: Deny (documented in network-security.md Section 4.8.4) |

#### Requirement 7 Supporting Evidence Summary

**Access Control Model (7.2.1):**

| Team | GCP Access | GitHub Access | Admin Portal | Database Access |
|------|-----------|---------------|--------------|-----------------|
| **Engineering** | Read-only (logs) | Read/Write | No | No direct access |
| **QA** | None | Read | No | No |
| **Product** | None | Read | No | No |
| **CS** | None | None | Yes (MFA required) | No |
| **CTO/CEO** | Full admin | Admin | Super Admin | No direct access |

**Access Review Process (7.2.4):**

| Review Type | Frequency | Reviewer | Evidence Location |
|-------------|-----------|----------|-------------------|
| GCP IAM users | Quarterly | CTO | Notion, GCP Audit Logs |
| Admin Portal accounts | Quarterly | CS Lead + CTO | Firebase Auth, Notion |
| GitHub org members | Quarterly | CTO | GitHub org settings |
| Service accounts | Quarterly | CTO | GCP IAM |

**CHD Access Restriction (7.2.6):**

| CHD Location | Who Can Access | How | Control |
|--------------|---------------|-----|---------|
| Kraken Database (PAN) | Kraken service account only | Programmatic via application | GCP IAM, Workload Identity |
| Admin Portal (masked) | CS Team | Web UI with MFA | Firebase Auth + Passkey |
| Logs (encrypted PAN) | Engineering | GCP Cloud Logging | GCP IAM, read-only |

**Documentation References:**
- access-control.md Section 3: Team Access Levels table
- access-control.md Section 4.1: Provisioning process
- access-control.md Section 4.4: Quarterly access review
- admin-portal-access.md Section 4.5: Transaction operations (masked PAN only)
- crypto-key-management.md Section 3: IAM access table for encryption keys

### Requirement 8: Identify Users and Authenticate Access to System Components

**Scope Context:** DASH has two main authentication domains: (1) GCP infrastructure access via Google accounts with Google Authenticator MFA, (2) Admin Portal access via Firebase Auth with passkey MFA. All users have unique IDs; service accounts are managed via GCP IAM with Workload Identity (no static credentials for Cloud Run).

**Authentication Architecture Summary:**

| System | Auth Method | MFA | User ID Source |
|--------|------------|-----|----------------|
| **GCP Infrastructure** | Google Account | Google Authenticator / Hardware Key | Google account email |
| **Admin Portal** | Firebase Auth + Passkey | Passkey (device-bound) | Firebase UID |
| **GitHub** | GitHub account | Authenticator app / Hardware Key | GitHub username |
| **Microsoft 365 (Email)** | Microsoft Account | Microsoft Authenticator | Microsoft 365 email |
| **Cloud Run Services** | Workload Identity | N/A (machine identity) | Service account email |

| Req ID | Requirement Summary | Status | Supporting Document | Section Reference | Evidence | Gaps/Notes |
|--------|---------------------|--------|---------------------|-------------------|----------|------------|
| 8.1.1 | Security policies and procedures for Req 8 documented, up to date, in use, known | **Compliant** | [Access Control](../../security/digital/access-control.md), [Admin Portal Access](../../security/digital/admin-portal-access.md), [Security Policy & Awareness](../../security/digital/security-policy-awareness.md) | [AC §1](../../security/digital/access-control.md#1-purpose), [AC §8](../../security/digital/access-control.md#8-review--maintenance); [SPA §4.5](../../security/digital/security-policy-awareness.md#45-mfa-requirements) | Documents v1.1, quarterly review, Internal Portal, MFA requirements table | Authentication policies documented: (1) access-control.md covers GCP/GitHub access, (2) admin-portal-access.md covers Admin Portal authentication, (3) security-policy-awareness.md documents MFA requirements for all systems |
| 8.1.2 | Roles and responsibilities for Req 8 documented, assigned, understood | **Compliant** | [Access Control](../../security/digital/access-control.md), [Admin Portal Access](../../security/digital/admin-portal-access.md) | [AC §3](../../security/digital/access-control.md#3-roles--responsibilities), [APA §3](../../security/digital/admin-portal-access.md#3-roles--responsibilities) | Roles tables | **Documented:** Control Owner (CTO), Operator (Engineering), Super Admin (CTO/CEO for Admin Portal accounts) |
| 8.2.1 | All users assigned unique ID before access to system components or CHD | **Compliant** | [Access Control](../../security/digital/access-control.md), [Admin Portal Access](../../security/digital/admin-portal-access.md) | [AC §4.1](../../security/digital/access-control.md#41-access-provisioning), [APA §4.2](../../security/digital/admin-portal-access.md#42-account-provisioning) | Google accounts, Firebase Auth UIDs | **Documented:** (1) Each employee has individual Google account (unique email), (2) Each CS user has individual Firebase Auth account (unique UID), (3) No shared user accounts policy in GCP IAM inherited controls table |
| 8.2.2 | Group/shared/generic accounts managed with documented justification and approval | **Compliant** | [Access Control](../../security/digital/access-control.md), [Crypto Key Mgmt](../../security/digital/cryptographic-key-management.md) | [AC §2](../../security/digital/access-control.md#2-scope), [CKM §3](../../security/digital/cryptographic-key-management.md#3-roles--responsibilities) | GCP IAM inherited controls, Service accounts table | **Documented:** (1) No shared user accounts - all users have individual Google/Firebase accounts, (2) Service accounts are for application use only (not shared by humans), (3) Service accounts documented with purpose (Kraken = encrypt/decrypt only), (4) Workload Identity = no static credentials |
| 8.2.3 | Service providers with remote access use unique auth per customer premises | **Not Applicable** | - | - | - | DASH is not a service provider with remote access to customer premises |
| 8.2.4 | Addition/deletion/modification of user IDs managed with approval and documented privileges | **Compliant** | [Access Control](../../security/digital/access-control.md), [Admin Portal Access](../../security/digital/admin-portal-access.md) | [AC §4.1](../../security/digital/access-control.md#41-access-provisioning), [AC §4.4](../../security/digital/access-control.md#44-quarterly-access-review); [APA §4.2](../../security/digital/admin-portal-access.md#42-account-provisioning) | Provisioning process, Quarterly review | **Documented:** (1) CTO/Engineering lead creates accounts with appropriate IAM role, (2) Access provisioning documented in Notion, (3) Super Admin (CTO/CEO) creates Admin Portal accounts, (4) Quarterly review verifies access appropriate |
| 8.2.5 | Access for terminated users immediately revoked | **Compliant** | [Access Control](../../security/digital/access-control.md), [Admin Portal Access](../../security/digital/admin-portal-access.md), [Third-Party Risk](../../security/digital/third-party-risk.md) | [APA §4.6.1](../../security/digital/admin-portal-access.md#461-account-revocation); [TPR §4.2](../../security/digital/third-party-risk.md#42-contractor-access-management) | Same-day removal SLA, Notion records | **Documented:** (1) HR notifies CTO/CEO same day, (2) All accounts (GCP, Admin Portal, GitHub, Microsoft 365) removed **same business day** (SLA: within 4 hours during business hours), (3) Removal logged in Notion with timestamp, (4) Quarterly review catches any discrepancies |
| 8.2.6 | Inactive user accounts removed or disabled within 90 days | **Partially Compliant** | [Access Control](../../security/digital/access-control.md) | [§4.4](../../security/digital/access-control.md#44-quarterly-access-review) | Quarterly review | **Gap:** No specific 90-day inactive account policy. Current state: Quarterly review checks for departed employees and appropriate access. **Need:** Add inactive account identification to quarterly review checklist |
| 8.2.7 | Third-party remote access accounts enabled only when needed, monitored | **Compliant** | [Third-Party Risk](../../security/digital/third-party-risk.md) | [§4.2](../../security/digital/third-party-risk.md#42-contractor-access-management) | Contractor access diagram, Offboarding process | **Documented:** (1) Contractors have GitHub code access only - no infrastructure/production, (2) Departed contractors removed within 24 hours, (3) All contractor PRs reviewed by internal team, (4) Quarterly access review verifies contractor engagement status |
| 8.2.8 | Idle session timeout after 15 minutes | **Compliant** | [Admin Portal Access](../../security/digital/admin-portal-access.md) | [§4.3.2](../../security/digital/admin-portal-access.md#432-session-timeout) | Application-level timeout | **Documented:** (1) 15-minute idle session timeout implemented at application level, (2) Tracks last user activity (clicks, keystrokes, navigation), (3) Session invalidated after 15 min inactivity, (4) User must re-authenticate (password + passkey) to continue |
| 8.3.1 | All user access authenticated via at least one factor (password, token, biometric) | **Compliant** | [Access Control](../../security/digital/access-control.md), [Admin Portal Access](../../security/digital/admin-portal-access.md), [Security Policy & Awareness](../../security/digital/security-policy-awareness.md) | [APA §4.3](../../security/digital/admin-portal-access.md#43-authentication-requirements); [SPA §4.5](../../security/digital/security-policy-awareness.md#45-mfa-requirements) | Auth requirements table, MFA requirements table | **Documented:** (1) Admin Portal: password + passkey (two factors), (2) GCP: Google account with password + MFA (two factors), (3) GitHub: password/SSO + authenticator (two factors), (4) Microsoft 365: password + Authenticator |
| 8.3.2 | Strong cryptography for auth factors during transmission and storage | **Compliant** | [Admin Portal Access](../../security/digital/admin-portal-access.md), [Network Security](../../security/digital/network-security.md) | [APA §4.3](../../security/digital/admin-portal-access.md#43-authentication-requirements); [NS §4.6.2](../../security/digital/network-security.md#462-httpstls-encryption) | bcrypt hashing, HTTPS/TLS 1.2+ | **Documented:** (1) Passwords hashed with bcrypt (admin-portal-access.md), (2) All transmission via HTTPS/TLS 1.2+ (network-security.md), (3) Firebase Auth uses industry-standard encryption, (4) Google account passwords managed by Google (secure by default) |
| 8.3.3 | User identity verified before modifying auth factors | **Compliant** | [Admin Portal Access](../../security/digital/admin-portal-access.md), [Security Policy & Awareness](../../security/digital/security-policy-awareness.md) | [APA §4.4](../../security/digital/admin-portal-access.md#44-account-lockout); [SPA §4.5](../../security/digital/security-policy-awareness.md#45-mfa-requirements) | Account unlock process, Lost MFA procedure | **Documented:** (1) Account lockout requires Super Admin to unlock via Firebase console (identity verification implied), (2) Lost MFA device: CTO verifies identity via in-person or video call before disabling MFA (security-policy-awareness.md Section 4.5) |
| 8.3.4 | Invalid auth attempts limited: lockout after ≤10 attempts, 30+ min duration | **Compliant** | [Admin Portal Access](../../security/digital/admin-portal-access.md) | [§4.3](../../security/digital/admin-portal-access.md#43-authentication-requirements), [§4.4](../../security/digital/admin-portal-access.md#44-account-lockout) | Authentication requirements table, Lockout section | **Documented:** (1) Failed login lockout: 5 attempts (exceeds requirement of ≤10), (2) Manual unlock by Super Admin (unlimited duration until admin action), (3) GCP and Microsoft 365 have built-in lockout policies |
| 8.3.5 | Passwords set to unique value for first use and forced to change | **Compliant** | [Admin Portal Access](../../security/digital/admin-portal-access.md) | [§4.2](../../security/digital/admin-portal-access.md#42-account-provisioning) | Account provisioning process | **Documented:** (1) Super Admin sets initial temporary password, (2) New user must complete password reset on first login (must meet strength requirements), (3) Then completes passkey enrollment |
| 8.3.6 | Password complexity: min 12 chars (or 8 if system doesn't support), numeric + alpha | **Compliant** | [Admin Portal Access](../../security/digital/admin-portal-access.md) | [§4.3](../../security/digital/admin-portal-access.md#43-authentication-requirements) | Authentication requirements table | **Documented:** (1) Minimum 8 characters (system enforced), (2) 1 uppercase, 1 lowercase, 1 number, 1 special character. **Note:** PCI v4.0.1 prefers 12 chars; 8 chars acceptable if system limitation. Firebase Auth can support 12+; consider increasing |
| 8.3.7 | Passwords different from last 4 used | **Compliant** | [Admin Portal Access](../../security/digital/admin-portal-access.md) | [§4.3.1](../../security/digital/admin-portal-access.md#431-password-history) | Firestore password history | **Documented:** (1) Last 4 password hashes stored in Firestore (`users/{uid}/password_history`), (2) Hashes stored as one-way bcrypt (cannot be reversed), (3) On password change, new password compared against last 4, (4) User must choose genuinely new password |
| 8.3.8 | Auth policies documented and communicated: strong factors, protect factors, no reuse, change if compromised | **Compliant** | [Security Policy & Awareness](../../security/digital/security-policy-awareness.md) | [§4.3](../../security/digital/security-policy-awareness.md#43-security-awareness-training), [§4.5](../../security/digital/security-policy-awareness.md#45-mfa-requirements) | Training content, MFA rules | **Documented:** (1) Module 2 "Account Security & MFA" covers strong authentication, (2) MFA rules prohibit SMS (SIM swap risk), (3) Lost MFA device procedure documented, (4) "If MFA device lost: report immediately to CTO" implies compromise response |
| 8.3.9 | Single-factor passwords changed every 90 days OR dynamic security analysis | **Not Applicable** | [Admin Portal Access](../../security/digital/admin-portal-access.md), [Security Policy & Awareness](../../security/digital/security-policy-awareness.md) | [APA §4.3](../../security/digital/admin-portal-access.md#43-authentication-requirements); [SPA §4.5](../../security/digital/security-policy-awareness.md#45-mfa-requirements) | MFA required for all | MFA required for all CDE access (Admin Portal: passkey, GCP: Authenticator, GitHub: Authenticator). No single-factor authentication scenarios exist. **Note:** Admin Portal password expires every 180 days but this is moot due to MFA |
| 8.3.10 | Service provider single-factor customer access guidance | **Not Applicable** | - | - | - | DASH does not provide customer access to CHD via single-factor authentication |
| 8.3.10.1 | Service provider 90-day password change or dynamic analysis for customers | **Not Applicable** | - | - | - | DASH is not a service provider with customer access to CHD |
| 8.3.11 | Auth factors (tokens, smart cards, certs) assigned to individual, protected | **Compliant** | [Admin Portal Access](../../security/digital/admin-portal-access.md), [Security Policy & Awareness](../../security/digital/security-policy-awareness.md) | [APA §4.3](../../security/digital/admin-portal-access.md#43-authentication-requirements); [SPA §4.5](../../security/digital/security-policy-awareness.md#45-mfa-requirements) | Passkey, MFA enrollment | **Documented:** (1) Passkeys are device-bound (assigned to individual user's device), (2) MFA enrollment on personal mobile device, (3) Cannot share passkeys between users, (4) Hardware keys (YubiKey) preferred for high-privilege accounts |
| 8.4.1 | MFA for all non-console access to CDE for personnel with admin access | **Compliant** | [Admin Portal Access](../../security/digital/admin-portal-access.md), [Security Policy & Awareness](../../security/digital/security-policy-awareness.md) | [APA §4.3](../../security/digital/admin-portal-access.md#43-authentication-requirements); [SPA §4.5](../../security/digital/security-policy-awareness.md#45-mfa-requirements) | Passkey MFA, GCP Authenticator | **Documented:** (1) Admin Portal requires password + passkey (MFA) for all access including Super Admins, (2) GCP Console requires Google account with MFA for all access, (3) MFA requirements table lists all systems with MFA required |
| 8.4.2 | MFA for all non-console access to CDE | **Compliant** | [Admin Portal Access](../../security/digital/admin-portal-access.md), [Security Policy & Awareness](../../security/digital/security-policy-awareness.md) | [APA §4.3](../../security/digital/admin-portal-access.md#43-authentication-requirements); [SPA §4.5](../../security/digital/security-policy-awareness.md#45-mfa-requirements) | Passkey MFA | **Documented:** (1) All Admin Portal access requires MFA (passkey), (2) This is application-enforced - cannot access without completing MFA, (3) No user accounts exempt from MFA requirement |
| 8.4.3 | MFA for all remote network access that could access/impact CDE | **Compliant** | [Security Policy & Awareness](../../security/digital/security-policy-awareness.md) | [§4.5](../../security/digital/security-policy-awareness.md#45-mfa-requirements) | MFA requirements table | **Documented:** (1) GCP Console access requires MFA (Google Authenticator), (2) GitHub access requires MFA (Authenticator/hardware key), (3) Microsoft 365 requires MFA, (4) All these systems have remote access to or could impact CDE (code deployment, infrastructure, Admin Portal access) |
| 8.5.1 | MFA systems: not susceptible to replay, cannot be bypassed, two different factor types, success of all required | **Compliant** | [Admin Portal Access](../../security/digital/admin-portal-access.md), [Security Policy & Awareness](../../security/digital/security-policy-awareness.md) | [APA §4.3](../../security/digital/admin-portal-access.md#43-authentication-requirements); [SPA §4.5](../../security/digital/security-policy-awareness.md#45-mfa-requirements) | Passkey, Authenticator | **Documented:** (1) Passkeys are cryptographic (not replayable), (2) MFA cannot be bypassed - enforced by application, (3) Two factor types: password (something you know) + passkey (something you have), (4) Both required before access granted. Google/Microsoft Authenticator also meet criteria |
| 8.6.1 | System/application accounts: interactive login prevented unless exceptional, justified, approved, attributable | **Compliant** | [Access Control](../../security/digital/access-control.md), [Crypto Key Mgmt](../../security/digital/cryptographic-key-management.md) | [AC §2](../../security/digital/access-control.md#2-scope); [CKM §3](../../security/digital/cryptographic-key-management.md#3-roles--responsibilities) | Workload Identity, Service accounts table | **Documented:** (1) Cloud Run/Cloud Functions use Workload Identity (no interactive login possible), (2) Service accounts cannot be used for interactive login (machine identity only), (3) Service accounts documented with specific purpose (Kraken = encrypt/decrypt), (4) All service account actions logged in GCP Audit Logs (attributable) |
| 8.6.2 | Passwords not hardcoded in scripts, config files, or source code | **Compliant** | [Secure Development](../../security/digital/secure-development.md) | [§4.3](../../security/digital/secure-development.md#43-credential-protection) | SonarQube rules, GCP Secret Manager | **Documented:** (1) SonarQube detects hardcoded secrets in code (blocks PR merge), (2) Secrets stored in GCP Secret Manager, (3) Code review checks for credential handling, (4) Logging libraries configured to redact sensitive patterns |
| 8.6.3 | Application/system account passwords changed periodically per risk analysis, sufficient complexity | **Compliant** | [Admin Portal Access](../../security/digital/admin-portal-access.md), [Crypto Key Mgmt](../../security/digital/cryptographic-key-management.md) | [APA §4.7](../../security/digital/admin-portal-access.md#47-credential-rotation); [CKM §4.3](../../security/digital/cryptographic-key-management.md#43-key-rotation-automated) | Secret Manager, KMS rotation | **Documented:** (1) Cloud SQL credentials rotate every 90 days, (2) GCP KMS keys auto-rotate every 90 days, (3) API keys rotate annually or upon suspected compromise, (4) Secrets stored in GitHub Secrets/GCP Secret Manager with versioning, (5) Secrets never visible in build logs or UI |

#### Requirement 8 Supporting Evidence Summary

**Authentication Methods by System (8.3.1):**

| System | Factor 1 (Know) | Factor 2 (Have) | Factor 3 (Are) | MFA Enforced |
|--------|----------------|-----------------|----------------|--------------|
| **Admin Portal** | Password (bcrypt) | Passkey (device-bound) | - | Yes (application) |
| **GCP Console** | Google password | Google Authenticator / Hardware key | - | Yes (organization) |
| **GitHub** | Password / SSO | Authenticator / Hardware key | - | Yes (organization) |
| **Microsoft 365** | Password | Microsoft Authenticator | - | Yes (tenant policy) |

**Password Policy Compliance (8.3.5, 8.3.6):**

| Setting | Admin Portal | PCI Requirement | Status |
|---------|-------------|-----------------|--------|
| Minimum length | 8 characters | 12 (or 8 if unsupported) | Compliant (8 OK, 12 preferred) |
| Complexity | Upper + Lower + Number + Special | Numeric + Alpha | **Exceeds** |
| First-use change | Required | Required | Compliant |
| Expiry | 180 days | N/A (MFA used) | N/A |
| History | Not documented | Last 4 different | **Gap** |
| Lockout threshold | 5 attempts | ≤10 attempts | **Exceeds** |
| Lockout duration | Manual unlock | ≥30 minutes | **Exceeds** (unlimited) |

**MFA Coverage (8.4.1, 8.4.2, 8.4.3):**

| Access Type | System | MFA Method | Documented |
|-------------|--------|------------|------------|
| CDE Admin Access | Admin Portal | Passkey | admin-portal-access.md 4.3 |
| CDE User Access | Admin Portal | Passkey | admin-portal-access.md 4.3 |
| Remote Infrastructure | GCP Console | Google Authenticator | security-policy-awareness.md 4.5 |
| Remote Code Access | GitHub | Authenticator / Hardware | security-policy-awareness.md 4.5 |
| Remote Email | Microsoft 365 | Microsoft Authenticator | security-policy-awareness.md 4.5 |

**Service Account Management (8.6.1):**

| Account | Purpose | Interactive Login | Credential Type | Rotation |
|---------|---------|-------------------|-----------------|----------|
| Kraken Service Account | PAN encrypt/decrypt | Disabled (Workload Identity) | None (auto-managed) | Automatic |
| Cloud Run Service Account | Application runtime | Disabled (Workload Identity) | None (auto-managed) | Automatic |
| Cloud Functions Service Account | Background jobs | Disabled (Workload Identity) | None (auto-managed) | Automatic |

**Documentation References:**
- admin-portal-access.md Section 4.3: Authentication Requirements table (password policy, MFA)
- admin-portal-access.md Section 4.2: Account Provisioning (first-use password change)
- admin-portal-access.md Section 4.4: Lockout policy
- security-policy-awareness.md Section 4.5: MFA Requirements table (all systems)
- security-policy-awareness.md Section 4.3: Training Module 2 (Account Security & MFA)
- access-control.md Section 2: GCP IAM inherited controls (unique IDs, MFA)
- crypto-key-management.md Section 3: Service account access table
- secure-development.md Section 4.3: Credential Protection (SonarQube, Secret Manager)

### Requirement 9: Restrict Physical Access to Cardholder Data

**Scope Context:** DASH's Cardholder Data Environment (CDE) is **entirely cloud-hosted** on GCP Cloud Run and Cloud SQL. There are **no on-premises systems** that store, process, or transmit cardholder data. Physical security requirements for the CDE are satisfied through **GCP inherited controls** as a PCI DSS 4.0.1 Level 1 Service Provider.

**DASH Office Physical Security:**
- Office has access card controls for general office and CS Room (restricted)
- CS Room separated from Engineering (conflict of interest prevention)
- No cardholder data is printed, stored on physical media, or backed up offline
- Office WiFi has no direct connectivity to GCP/CDE

**GCP Inherited Physical Security:**
| PCI Requirement | GCP Provides | Evidence |
|-----------------|-------------|----------|
| Data center physical access | 24/7 security, biometric controls, CCTV | GCP AOC |
| Environmental controls | Fire suppression, HVAC, power redundancy | GCP SOC 2 |
| Media destruction | Secure media sanitization and destruction | GCP AOC |

| Req ID | Requirement Summary | Status | Supporting Document | Section Reference | Evidence | Gaps/Notes |
|--------|---------------------|--------|---------------------|-------------------|----------|------------|
| 9.1.1 | Security policies and procedures for Req 9 documented, up to date, in use, known | **Compliant** | [Physical Security](../../security/physical/physical-security.md) | [§1](../../security/physical/physical-security.md#1-purpose), [§8](../../security/physical/physical-security.md#8-review--maintenance) | Document v1.1, quarterly review, Internal Portal | Physical security policies documented for office; CDE physical security inherited from GCP |
| 9.1.2 | Roles and responsibilities for Req 9 documented, assigned, understood | **Compliant** | [Physical Security](../../security/physical/physical-security.md) | [§3](../../security/physical/physical-security.md#3-roles--responsibilities) | Roles table | CTO (Control Owner), Facility Manager (access cards), CS Room Access Approver |
| 9.2.1 | Appropriate facility entry controls to restrict physical access to systems in CDE | **Compliant (GCP Inherited)** | [Physical Security](../../security/physical/physical-security.md), [Third-Party Risk](../../security/digital/third-party-risk.md) | [PS §4.1](../../security/physical/physical-security.md#41-office-access-control); [TPR §4.6](../../security/digital/third-party-risk.md#46-gcp-as-pci-dss-service-provider) | GCP AOC, Access cards for office | **CDE in GCP:** GCP data centers have 24/7 security, biometric access, mantraps. **Office:** No CDE systems on-premises; access cards for office entry |
| 9.2.1.1 | Individual physical access to sensitive areas within CDE monitored (cameras/access controls) | **Compliant (GCP Inherited)** | [Third-Party Risk](../../security/digital/third-party-risk.md) | [§4.6](../../security/digital/third-party-risk.md#46-gcp-as-pci-dss-service-provider) | GCP AOC | **GCP Inherited:** GCP data centers have CCTV monitoring, access logs, 90+ day retention. DASH has no on-premises CDE sensitive areas |
| 9.2.2 | Physical/logical controls restrict use of publicly accessible network jacks | **Not Applicable** | [Physical Security](../../security/physical/physical-security.md) | [§4.3](../../security/physical/physical-security.md#43-network-isolation) | Network isolation diagram | CDE is cloud-hosted; office WiFi has no direct GCP access; no network jacks connect to CDE |
| 9.2.3 | Physical access to wireless APs, gateways, networking hardware restricted | **Compliant (GCP Inherited)** | [Physical Security](../../security/physical/physical-security.md), [Third-Party Risk](../../security/digital/third-party-risk.md) | [PS §4.3](../../security/physical/physical-security.md#43-network-isolation); [TPR §4.6](../../security/digital/third-party-risk.md#46-gcp-as-pci-dss-service-provider) | GCP AOC, Office network isolation | **GCP Inherited:** GCP manages all CDE networking hardware. **Office:** WiFi is internet-only; no CDE networking equipment on-premises |
| 9.2.4 | Access to consoles in sensitive areas restricted via locking | **Not Applicable** | - | - | - | No on-premises consoles or servers in CDE; all access via GCP Console (logical access, not physical) |
| 9.3.1 | Procedures for authorizing/managing physical access of personnel to CDE | **Compliant (GCP Inherited)** | [Physical Security](../../security/physical/physical-security.md), [Third-Party Risk](../../security/digital/third-party-risk.md) | [PS §4.1](../../security/physical/physical-security.md#41-office-access-control); [TPR §4.6](../../security/digital/third-party-risk.md#46-gcp-as-pci-dss-service-provider) | GCP AOC, Access card system | **GCP Inherited:** GCP manages personnel access to data centers. **DASH Office:** Access cards issued on hire, deactivated on termination |
| 9.3.1.1 | Physical access to sensitive areas within CDE controlled: authorized, revoked on termination, mechanisms returned | **Compliant** | [Physical Security](../../security/physical/physical-security.md), [Admin Portal Access](../../security/digital/admin-portal-access.md) | [PS §4.1](../../security/physical/physical-security.md#41-office-access-control); [APA §4.6.1](../../security/digital/admin-portal-access.md#461-account-revocation) | Card deprovisioning process | **Office:** (1) Access cards collected on termination, (2) Cards deactivated same business day per admin-portal-access.md, (3) CDE physical access managed by GCP |
| 9.3.2 | Procedures for visitor access to CDE: authorized, escorted, badged, distinguishable | **Compliant** | [Physical Security](../../security/physical/physical-security.md) | [§4.5](../../security/physical/physical-security.md#45-visitor-access) | Visitor log, escort process | **Documented:** (1) Visitors sign in at reception, (2) Escorted at all times, (3) Cannot access CS Room, (4) Signed out on departure. **Note:** Office has no CDE; visitors cannot physically access GCP |
| 9.3.3 | Visitor badges surrendered/deactivated before leaving or at expiration | **Compliant** | [Physical Security](../../security/physical/physical-security.md) | [§4.5](../../security/physical/physical-security.md#45-visitor-access) | Visitor process | Visitors do not receive permanent badges; signed out upon departure; no CDE on-premises |
| 9.3.4 | Visitor logs maintained: name, org, date/time, authorizer; retained 3 months | **Compliant** | [Physical Security](../../security/physical/physical-security.md) | [§4.5](../../security/physical/physical-security.md#45-visitor-access), [§6](../../security/physical/physical-security.md#6-evidence-produced) | Visitor log | **Documented:** Visitor log captures sign-in/out. Retention: 90 days (meets 3-month requirement). **Note:** Office has no CDE |
| 9.4.1 | All media with CHD physically secured | **Compliant** | [Physical Security](../../security/physical/physical-security.md) | [§4.4](../../security/physical/physical-security.md#44-data-handling---no-physical-data) | No physical media policy | **Policy:** No cardholder data on physical media (USB, paper, tapes). All CHD stored in GCP Cloud SQL (encrypted) |
| 9.4.1.1 | Offline media backups with CHD stored in secure location | **Not Applicable** | [Business Continuity](../../security/digital/business-continuity.md), [Physical Security](../../security/physical/physical-security.md) | [PS §4.4](../../security/physical/physical-security.md#44-data-handling---no-physical-data) | GCP automated backups | **No offline backups:** All backups are GCP Cloud SQL automated backups (online, encrypted). No tapes or offline media |
| 9.4.1.2 | Security of offline media backup location reviewed annually | **Not Applicable** | - | - | - | No offline media backups exist |
| 9.4.2 | Media with CHD classified by sensitivity | **Not Applicable** | [Physical Security](../../security/physical/physical-security.md) | [§4.4](../../security/physical/physical-security.md#44-data-handling---no-physical-data) | No physical media | No physical media with CHD exists to classify; data classification applies to digital data only |
| 9.4.3 | Media with CHD sent outside facility: logged, secured courier, tracked | **Compliant** | [Physical Security](../../security/physical/physical-security.md) | [§4.4](../../security/physical/physical-security.md#44-data-handling---no-physical-data) | Prohibited | **Policy:** Media with CHD is **never** sent outside facility. Prohibited by policy |
| 9.4.4 | Management approves media with CHD moved outside facility | **Compliant** | [Physical Security](../../security/physical/physical-security.md) | [§4.4](../../security/physical/physical-security.md#44-data-handling---no-physical-data) | Prohibited | **Policy:** Media with CHD is **never** moved outside. No approval process needed because activity is prohibited |
| 9.4.5 | Inventory logs of electronic media with CHD maintained | **Not Applicable** | [Physical Security](../../security/physical/physical-security.md) | [§4.4](../../security/physical/physical-security.md#44-data-handling---no-physical-data) | No physical media | No electronic media (USB, external drives, etc.) with CHD. All data in GCP Cloud SQL |
| 9.4.5.1 | Electronic media inventory conducted annually | **Not Applicable** | - | - | - | No electronic media with CHD exists |
| 9.4.6 | Hard-copy materials with CHD destroyed when no longer needed (cross-cut shred, incinerate, pulp) | **Compliant** | [Physical Security](../../security/physical/physical-security.md) | [§4.4](../../security/physical/physical-security.md#44-data-handling---no-physical-data) | No printing policy | **Policy:** CHD is **never printed**. No hard-copy materials with CHD exist. Prohibition documented |
| 9.4.7 | Electronic media with CHD destroyed when no longer needed (destroyed or data unrecoverable) | **Compliant (GCP Inherited)** | [Third-Party Risk](../../security/digital/third-party-risk.md) | [§4.6](../../security/digital/third-party-risk.md#46-gcp-as-pci-dss-service-provider) | GCP AOC | **GCP Inherited:** GCP handles secure media destruction for Cloud SQL storage. DASH has no electronic media with CHD on-premises |
| 9.5.1 | POI devices protected from tampering/unauthorized substitution | **Not Applicable** | - | - | - | **DASH does not use POI devices.** All payments are online/mobile (card-not-present). No physical card readers |
| 9.5.1.1 | POI device inventory maintained (make, model, location, serial) | **Not Applicable** | - | - | - | No POI devices |
| 9.5.1.2 | POI device surfaces periodically inspected for tampering | **Not Applicable** | - | - | - | No POI devices |
| 9.5.1.2.1 | POI inspection frequency defined via targeted risk analysis | **Not Applicable** | - | - | - | No POI devices |
| 9.5.1.3 | POI personnel trained on tampering awareness | **Not Applicable** | - | - | - | No POI devices |

#### Requirement 9 Supporting Evidence Summary

**Physical Security Applicability:**

| Physical Security Area | DASH Office | GCP Data Center (CDE) |
|----------------------|-------------|----------------------|
| Entry controls | Access cards | 24/7 security, biometrics (GCP) |
| Visitor management | Sign-in log, escort | Strict visitor controls (GCP) |
| Media with CHD | **Prohibited** | Encrypted storage (GCP) |
| Offline backups | **None** | N/A (online backups only) |
| POI devices | **None** | N/A |
| Networking equipment for CDE | **None on-premises** | GCP responsibility |

**Why Most Requirements Are N/A or GCP Inherited:**

| Reason | Requirements Affected |
|--------|----------------------|
| **CDE is 100% cloud-hosted** | 9.2.1, 9.2.1.1, 9.2.3, 9.2.4, 9.3.1 |
| **No on-premises CHD storage** | 9.4.1.1, 9.4.1.2, 9.4.5, 9.4.5.1, 9.4.7 |
| **No POI devices (card-not-present only)** | 9.5.1, 9.5.1.1, 9.5.1.2, 9.5.1.2.1, 9.5.1.3 |
| **No physical media with CHD** | 9.4.2, 9.4.3, 9.4.4 |

**Office Physical Controls (Non-CDE):**

| Control | Implementation | Evidence |
|---------|---------------|----------|
| Access cards | All employees issued cards; levels by role | Access control system logs |
| CS Room restriction | CS team only; Engineering excluded | Card configuration |
| Visitor log | Sign-in/out with escort | Paper/digital log, 90-day retention |
| No CHD printing | Policy prohibits printing customer data | Policy documented |
| No physical media | USB/external drives with CHD prohibited | Policy documented |

**Documentation References:**
- physical-security.md Section 4.1: Office access control (cards, provisioning, deprovisioning)
- physical-security.md Section 4.4: Data handling (no physical media, no printing)
- physical-security.md Section 4.5: Visitor access (sign-in, escort, restrictions)
- third-party-risk.md Section 4.6: GCP PCI DSS inherited controls
- business-continuity.md: Confirms no offline backups (GCP automated only)

### Requirement 10: Log and Monitor All Access

**Scope Context:** DASH uses GCP Cloud Logging as the centralized logging platform. All applications (DASH Main, Kraken, Admin Portal) run on Cloud Run/Cloud Functions, which automatically send logs to Cloud Logging. GCP Cloud Audit Logs are **always enabled and immutable** for Admin Activity logs, satisfying several Req 10 requirements through inherited controls.

**GCP Inherited Logging Controls:**

| Log Type | GCP Provides | PCI Requirement | Notes |
|----------|-------------|-----------------|-------|
| Admin Activity Logs | Always enabled, immutable, 400-day retention | 10.2.1.2, 10.3.2 | IAM changes, resource modifications |
| Data Access Logs | Configurable, immutable when enabled | 10.2.1.1 | CHD access via Kraken |
| Cloud Run Request Logs | Automatic per-request | 10.2.1 | All API requests logged |
| Access Transparency | Google admin access to customer data | 10.2.1.2 | Enterprise feature |

**Application Logging Architecture:**

| Component | Logging Target | Log Types | Retention |
|-----------|---------------|-----------|-----------|
| DASH Main | GCP Cloud Logging | Application logs, requests | [ASSUMPTION: 30 days] |
| Kraken | GCP Cloud Logging | Payment operations, CHD access | [ASSUMPTION: 30 days] |
| Admin Portal | GCP Cloud Logging + Firebase Auth | User actions, auth events | 400 days (Audit Logs) |
| All Services | Sentry | Errors, exceptions, stack traces | [ASSUMPTION: 90 days] |

| Req ID | Requirement Summary | Status | Supporting Document | Section Reference | Evidence | Gaps/Notes |
|--------|---------------------|--------|---------------------|-------------------|----------|------------|
| 10.1.1 | Security policies for Req 10 documented, up to date, in use, known | **Compliant** | [Logging & Monitoring](../../security/digital/logging-monitoring.md) | [§1](../../security/digital/logging-monitoring.md#1-purpose), [§8](../../security/digital/logging-monitoring.md#8-review--maintenance) | Document v1.1, quarterly review, Internal Portal | Logging policies documented with quarterly review cycle |
| 10.1.2 | Roles and responsibilities for Req 10 documented, assigned, understood | **Compliant** | [Logging & Monitoring](../../security/digital/logging-monitoring.md) | [§3](../../security/digital/logging-monitoring.md#3-roles--responsibilities) | Roles table | CTO (Control Owner), Engineering (Operator), Alert Responder defined |
| 10.2.1 | Audit logs enabled and active for all system components and CHD | **Compliant** | [Logging & Monitoring](../../security/digital/logging-monitoring.md) | [§4.1](../../security/digital/logging-monitoring.md#41-log-collection) | GCP Cloud Logging configuration | All apps log to Cloud Logging; GCP Audit Logs always enabled for Cloud Run |
| 10.2.1.1 | Audit logs capture all individual user access to CHD | **Compliant** | [Admin Portal Access](../../security/digital/admin-portal-access.md), [Logging & Monitoring](../../security/digital/logging-monitoring.md) | [APA §4.5](../../security/digital/admin-portal-access.md#45-transaction-operations), [LM §4.1](../../security/digital/logging-monitoring.md#41-log-collection) | Refund/void logs with agent ID | Admin Portal logs all CHD access (masked PAN only); Kraken logs payment operations |
| 10.2.1.2 | Audit logs capture all actions by individuals with admin access | **Compliant** | [Logging & Monitoring](../../security/digital/logging-monitoring.md), [Access Control](../../security/digital/access-control.md) | [LM §4.1](../../security/digital/logging-monitoring.md#41-log-collection), [AC §4](../../security/digital/access-control.md#4-how-we-operate-this-control) | GCP Cloud Audit Logs | **GCP Inherited:** Admin Activity logs always on, immutable; captures IAM changes, deployments, config changes |
| 10.2.1.3 | Audit logs capture all access to audit logs | **Compliant** | [Logging & Monitoring](../../security/digital/logging-monitoring.md) | [§6](../../security/digital/logging-monitoring.md#6-evidence-produced) | GCP Audit Logs | GCP logs all Log Explorer access; access restricted to Engineering via IAM |
| 10.2.1.4 | Audit logs capture all invalid logical access attempts | **Compliant** | [Admin Portal Access](../../security/digital/admin-portal-access.md), [Logging & Monitoring](../../security/digital/logging-monitoring.md) | [APA §4.4](../../security/digital/admin-portal-access.md#44-account-lockout), [LM §4.1](../../security/digital/logging-monitoring.md#41-log-collection) | Firebase Auth logs | Failed login attempts logged; 5-attempt lockout; alerts configured for threshold |
| 10.2.1.5 | Audit logs capture all changes to identification/authentication credentials | **Compliant** | [Logging & Monitoring](../../security/digital/logging-monitoring.md), [Admin Portal Access](../../security/digital/admin-portal-access.md) | [LM §4.1](../../security/digital/logging-monitoring.md#41-log-collection), [APA §4.2-4.3](../../security/digital/admin-portal-access.md#42-account-provisioning) | GCP Audit Logs, Firebase Auth logs | Account creation, password changes, privilege changes logged |
| 10.2.1.6 | Audit logs capture initialization/start/stop of audit logs | **Compliant (GCP Inherited)** | [Third-Party Risk](../../security/digital/third-party-risk.md) | [§4.6](../../security/digital/third-party-risk.md#46-gcp-as-pci-dss-service-provider) | GCP AOC | **GCP Inherited:** Cloud Audit Logs cannot be disabled or stopped by customers; GCP manages audit log infrastructure |
| 10.2.1.7 | Audit logs capture all creation/deletion of system-level objects | **Compliant** | [Logging & Monitoring](../../security/digital/logging-monitoring.md) | [§4.1](../../security/digital/logging-monitoring.md#41-log-collection) | GCP Audit Logs | Cloud Run deployments, Cloud SQL changes, IAM modifications all logged |
| 10.2.2 | Audit logs record required details (user ID, event type, date/time, success/fail, origin, affected resource) | **Compliant** | [Logging & Monitoring](../../security/digital/logging-monitoring.md) | [§4.1](../../security/digital/logging-monitoring.md#41-log-collection) | Log format documentation | GCP logs contain: principal, action, timestamp, success/failure, resource, source IP |
| 10.3.1 | Read access to audit logs limited to job-related need | **Compliant** | [Logging & Monitoring](../../security/digital/logging-monitoring.md), [Access Control](../../security/digital/access-control.md) | [LM §4.3](../../security/digital/logging-monitoring.md#43-log-access-control), [AC §3](../../security/digital/access-control.md#3-roles--responsibilities) | Access table, GCP IAM | Engineering: `roles/logging.viewer`; CS: No access; QA: No access |
| 10.3.2 | Audit log files protected from modification by individuals | **Compliant (GCP Inherited)** | [Logging & Monitoring](../../security/digital/logging-monitoring.md), [Third-Party Risk](../../security/digital/third-party-risk.md) | [LM §2](../../security/digital/logging-monitoring.md#2-scope), [TPR §4.6](../../security/digital/third-party-risk.md#46-gcp-as-pci-dss-service-provider) | GCP AOC | **GCP Inherited:** Cloud Audit Logs are immutable; cannot be modified or deleted by customers |
| 10.3.3 | Audit logs promptly backed up to secure, central, internal log server | **Compliant (GCP Inherited)** | [Logging & Monitoring](../../security/digital/logging-monitoring.md) | [§4.1](../../security/digital/logging-monitoring.md#41-log-collection), Architecture diagram | GCP Cloud Logging | **GCP Inherited:** All logs centralized in Cloud Logging (managed by GCP); replication handled by GCP infrastructure |
| 10.3.4 | FIM/change-detection on audit logs to ensure logs cannot be changed without alerts | **Compliant (GCP Inherited)** | [Third-Party Risk](../../security/digital/third-party-risk.md) | [§4.6](../../security/digital/third-party-risk.md#46-gcp-as-pci-dss-service-provider) | GCP AOC | **GCP Inherited:** Cloud Audit Logs are immutable by design; any modification attempt would fail at platform level |
| 10.4.1 | Security event logs reviewed at least once daily (security events, CHD logs, critical systems, security functions) | **Compliant** | [Logging & Monitoring](../../security/digital/logging-monitoring.md) | [§4.7](../../security/digital/logging-monitoring.md#47-daily-log-review-process) | Daily review process, checklist, Notion sign-off | Daily review by on-call engineer covering: security events (SCC), Kraken CHD logs, critical systems, auth/security functions; documented checklist with reviewer sign-off in Notion |
| 10.4.1.1 | Automated mechanisms used to perform audit log reviews | **Partially Compliant** | [Logging & Monitoring](../../security/digital/logging-monitoring.md) | [§4.4](../../security/digital/logging-monitoring.md#44-alerting) | Alert configuration | Alerts exist for error rate spikes, failed logins, security events → Teams + Email; **Gap:** Not comprehensive across all security events |
| 10.4.2 | Logs of all other system components reviewed periodically | **Partially Compliant** | [Logging & Monitoring](../../security/digital/logging-monitoring.md) | [§4.4](../../security/digital/logging-monitoring.md#44-alerting) | Ad-hoc review | Logs available for investigation; no scheduled periodic review documented |
| 10.4.2.1 | Log review frequency defined in targeted risk analysis per 12.3.1 | **Compliant** | [Logging & Monitoring](../../security/digital/logging-monitoring.md) | [§4.8](../../security/digital/logging-monitoring.md#48-targeted-risk-analysis-for-log-review-frequency) | Risk-based frequency table, justification | Targeted risk analysis documented: Critical systems (daily), Medium (weekly), Low (monthly); risk factors considered: data sensitivity, CDE access, exposure, impact, attack surface |
| 10.4.3 | Exceptions and anomalies identified during review process are addressed | **Compliant** | [Incident Response](../../security/digital/incident-response.md), [Logging & Monitoring](../../security/digital/logging-monitoring.md) | [IR §4](../../security/digital/incident-response.md#4-how-we-operate-this-control), [LM §4.4](../../security/digital/logging-monitoring.md#44-alerting) | Incident process, alert response | Alerts escalate to on-call engineer; S1/S2 incidents documented in Notion |
| 10.5.1 | Audit log history retained 12 months, 3 months immediately available | **Compliant** | [Logging & Monitoring](../../security/digital/logging-monitoring.md) | [§4.5](../../security/digital/logging-monitoring.md#45-log-retention) | GCP Cloud Logging 365-day retention | **Configured 2026-01-30:** All logs retained 365 days (12 months); Audit Logs 400 days; 90 days immediately searchable, older logs via archive query |
| 10.6.1 | System clocks synchronized using time-synchronization technology | **Compliant (GCP Inherited)** | [Third-Party Risk](../../security/digital/third-party-risk.md) | [§4.6](../../security/digital/third-party-risk.md#46-gcp-as-pci-dss-service-provider) | GCP AOC | **GCP Inherited:** Cloud Run/Functions use GCP-managed NTP; Google's authoritative time infrastructure |
| 10.6.2 | Systems configured to correct and consistent time (designated time servers, UTC, external sources) | **Compliant (GCP Inherited)** | [Third-Party Risk](../../security/digital/third-party-risk.md) | [§4.6](../../security/digital/third-party-risk.md#46-gcp-as-pci-dss-service-provider) | GCP AOC | **GCP Inherited:** GCP manages time synchronization across all services; based on UTC/Google time infrastructure |
| 10.6.3 | Time synchronization settings and data protected (access restricted, changes logged/monitored) | **Compliant (GCP Inherited)** | [Third-Party Risk](../../security/digital/third-party-risk.md) | [§4.6](../../security/digital/third-party-risk.md#46-gcp-as-pci-dss-service-provider) | GCP AOC | **GCP Inherited:** Customers cannot modify time settings on Cloud Run/Functions; fully managed by GCP |
| 10.7.2 | Failures of critical security control systems detected, alerted, and addressed promptly | **Partially Compliant** | [Logging & Monitoring](../../security/digital/logging-monitoring.md), [Incident Response](../../security/digital/incident-response.md) | [LM §4.4](../../security/digital/logging-monitoring.md#44-alerting), [IR §4](../../security/digital/incident-response.md#4-how-we-operate-this-control) | Some alerts configured | Alerts for: app errors, service down, failed logins. **Gap:** Not all controls monitored (FIM, anti-malware alerts, audit log mechanism failures) |
| 10.7.3 | Security control failures responded to promptly (restore, document duration, identify cause, remediate, prevent recurrence) | **Compliant** | [Incident Response](../../security/digital/incident-response.md) | [§4.3](../../security/digital/incident-response.md#43-incident-response-workflow-s1---critical), [§4.6](../../security/digital/incident-response.md#46-post-incident-report--review) | Incident process, post-incident review | S1 incidents: hotfix process, post-incident report with root cause, follow-up actions tracked |

#### Requirement 10 Supporting Evidence Summary

**Log Types and Sources:**

| Log Category | Source | Destination | Retention | Evidence |
|--------------|--------|-------------|-----------|----------|
| Application logs | DASH Main, Kraken, Admin Portal | GCP Cloud Logging | [ASSUMPTION: 30 days] | Log Explorer queries |
| Admin Activity logs | GCP IAM, Cloud Run, Cloud SQL | GCP Cloud Logging | 400 days (immutable) | Audit log filter |
| Data Access logs | Kraken database, KMS | GCP Cloud Logging | [ASSUMPTION: 400 days] | Audit log filter |
| Authentication logs | Firebase Auth | Firebase Console + GCP | 400 days | Firebase Auth console |
| Error tracking | All applications | Sentry | [ASSUMPTION: 90 days] | Sentry dashboard |
| Security alerts | GCP Log-based alerts | Teams + Email | Alert history | GCP Monitoring console |

**Log Content (10.2.2 Compliance):**

| Required Field | GCP Implementation | Example |
|----------------|-------------------|---------|
| User identification | `protoPayload.authenticationInfo.principalEmail` | `user@dash.com` |
| Type of event | `protoPayload.methodName` | `google.iam.admin.v1.CreateRole` |
| Date and time | `timestamp` | `2026-01-30T10:15:30.123Z` |
| Success/failure indication | `protoPayload.status` | `code: 0` (success) or error code |
| Origination of event | `protoPayload.requestMetadata.callerIp` | `203.0.113.45` |
| Affected resource | `resource.labels` | `projects/dash-prod/locations/us-central1` |

**Critical Gaps to Address:**

| Gap | Requirement | Current State | Remediation |
|-----|-------------|---------------|-------------|
| **No daily log review process** | 10.4.1 | Alerts exist, but no documented daily review | Document daily review process with assigned reviewer, define what logs are reviewed, define escalation criteria |
| **Application log retention <12 months** | 10.5.1 | ~30 days default | Configure Cloud Logging retention to 12 months for application logs (Audit Logs already 400 days) |
| **No targeted risk analysis for review frequency** | 10.4.2.1 | No formal risk analysis | Create risk analysis per 12.3.1 defining review frequency for non-critical systems |
| **Incomplete security control monitoring** | 10.7.2 | Some alerts configured | Expand alerting to cover: FIM (if implemented), anti-malware (GCP SCC), audit logging mechanism failures |

**Strengths:**

| Area | Strength | Evidence |
|------|----------|----------|
| Log immutability | GCP Cloud Audit Logs cannot be modified by customers | GCP AOC, platform design |
| Time synchronization | Fully managed by GCP, no customer configuration | GCP AOC |
| Centralized logging | All logs in single platform (Cloud Logging) | Architecture diagram |
| Admin action logging | Always-on, 400-day retention | GCP Audit Log configuration |
| Log access control | IAM-based, Engineering only | Access Control document |

**Documentation References:**
- logging-monitoring.md: Sections 4.1-4.5 (log collection, access, alerting, retention)
- admin-portal-access.md: Section 4.5 (refund/void logging), Section 6 (evidence)
- access-control.md: Section 3 (log access by team)
- incident-response.md: Section 4 (alert response, incident handling)
- third-party-risk.md: Section 4.6 (GCP inherited controls)

### Requirement 11: Test Security Systems and Networks

**Scope Context:** DASH has established a comprehensive third-party security testing program documented in vulnerability-management.md v1.2. This includes quarterly ASV scanning, quarterly external penetration testing, annual internal penetration testing, and segmentation validation.

**Testing Program Summary:**

| Test Type | Frequency | Provider | Status |
|-----------|-----------|----------|--------|
| ASV External Vulnerability Scan | Quarterly | PCI SSC Approved Vendor [To be contracted] | **Documented** |
| External Penetration Test | Quarterly | Qualified pen test firm [To be contracted] | **Documented** |
| Internal Penetration Test | Annually | Qualified pen test firm [To be contracted] | **Documented** |
| Segmentation Validation | Annually | Pen test firm | **Documented** |

| Req ID | Requirement Summary | Status | Supporting Document | Section Reference | Evidence | Gaps/Notes |
|--------|---------------------|--------|---------------------|-------------------|----------|------------|
| 11.1.1 | Security policies for Req 11 documented, up to date, in use, known | **Compliant** | [Vulnerability Management](../../security/digital/vulnerability-management.md) | [§1](../../security/digital/vulnerability-management.md#1-purpose), [§5](../../security/digital/vulnerability-management.md#5-third-party-security-testing-program), [§9](../../security/digital/vulnerability-management.md#9-related-controls) | Document v1.2, quarterly review | Security testing policies now documented |
| 11.1.2 | Roles and responsibilities for Req 11 documented, assigned, understood | **Compliant** | [Vulnerability Management](../../security/digital/vulnerability-management.md) | [§3](../../security/digital/vulnerability-management.md#3-roles--responsibilities) | Roles table | CTO (Testing Coordinator), Engineering Lead (Remediation Owner) |
| 11.2.1 | Wireless access points managed | **Compliant** | [Physical Security](../../security/physical/physical-security.md) | [§4.3](../../security/physical/physical-security.md#43-network-isolation) | No CDE wireless | Office WiFi isolated from CDE; CDE is cloud-hosted |
| 11.2.2 | Authorized wireless inventory | **Compliant** | [Physical Security](../../security/physical/physical-security.md) | [§4.3](../../security/physical/physical-security.md#43-network-isolation) | Network isolation | No CDE wireless; WiFi for internet only |
| 11.3.1 | Internal vulnerability scans quarterly | **Compliant** | [Vulnerability Management](../../security/digital/vulnerability-management.md) | [§4.1-4.2](../../security/digital/vulnerability-management.md#41-gcp-security-command-center), [§5.4](../../security/digital/vulnerability-management.md#54-internal-penetration-testing) | npm audit, SonarQube, GCP Security Health Analytics | Code scans every PR (continuous); GCP Security Health Analytics (continuous); internal pen test scope covers internal scanning |
| 11.3.1.1 | Other vulnerabilities managed by targeted risk analysis | **Partially Compliant** | [Vulnerability Management](../../security/digital/vulnerability-management.md) | [§4.3](../../security/digital/vulnerability-management.md#43-severity-classification) | Severity classification | Severity-based prioritization documented; formal risk analysis per 12.3.1 still needed |
| 11.3.1.2 | Authenticated internal scanning | **Compliant** | [Vulnerability Management](../../security/digital/vulnerability-management.md) | [§5.4](../../security/digital/vulnerability-management.md#54-internal-penetration-testing) | Internal pen test scope | Internal pen test includes authenticated application testing, service account review |
| 11.3.1.3 | Internal scans after significant change | **Compliant** | [Vulnerability Management](../../security/digital/vulnerability-management.md) | [§4.2](../../security/digital/vulnerability-management.md#42-code-vulnerability-scanning) | PR scans | Every PR triggers npm audit and SonarQube scans automatically |
| 11.3.2 | External vulnerability scans by ASV quarterly | **Compliant** | [Vulnerability Management](../../security/digital/vulnerability-management.md) | [§5.2](../../security/digital/vulnerability-management.md#52-asv-external-vulnerability-scanning) | ASV scanning schedule | Quarterly ASV scans documented: Q1 Jan, Q2 Apr, Q3 Jul, Q4 Oct; [Vendor to be contracted] |
| 11.3.2.1 | External scans after significant change | **Compliant** | [Vulnerability Management](../../security/digital/vulnerability-management.md) | [§5.2](../../security/digital/vulnerability-management.md#52-asv-external-vulnerability-scanning) | ASV process | Re-scan process documented for failed scans and post-remediation |
| 11.4.1 | Penetration testing methodology defined | **Compliant** | [Vulnerability Management](../../security/digital/vulnerability-management.md) | [§5.3](../../security/digital/vulnerability-management.md#53-external-penetration-testing) | Pen test methodology | 5-phase methodology: Recon, Vuln Assessment, Exploitation, Post-Exploitation, Reporting |
| 11.4.2 | Internal penetration testing annually | **Compliant** | [Vulnerability Management](../../security/digital/vulnerability-management.md) | [§5.4](../../security/digital/vulnerability-management.md#54-internal-penetration-testing) | Internal pen test schedule | Annual internal pen test scope: internal APIs, DB access, IAM, secrets, application logic |
| 11.4.3 | External penetration testing annually | **Compliant** | [Vulnerability Management](../../security/digital/vulnerability-management.md) | [§5.3](../../security/digital/vulnerability-management.md#53-external-penetration-testing) | External pen test schedule | Quarterly external pen tests (exceeds annual requirement); web apps, APIs, network perimeter, payment flows |
| 11.4.4 | Pen test findings corrected and retested | **Compliant** | [Vulnerability Management](../../security/digital/vulnerability-management.md) | [§5.6](../../security/digital/vulnerability-management.md#56-findings-remediation-process) | Remediation process | Severity-based deadlines (Critical 15d, High 30d); retest required for Critical/High; remediation tracking |
| 11.4.5 | Segmentation testing validates isolation | **Compliant** | [Vulnerability Management](../../security/digital/vulnerability-management.md) | [§5.5](../../security/digital/vulnerability-management.md#55-segmentation-validation) | Segmentation validation | Annual segmentation test: Kraken VPC isolation, DB network rules, firewall rules, service account permissions |
| 11.5.1 | IDS/IPS or network intrusion detection | **Compliant** | [Network Security](../../security/digital/network-security.md), [Third-Party Risk](../../security/digital/third-party-risk.md) | [NS §4.4](../../security/digital/network-security.md#44-waf-configuration), [TPR §4.6](../../security/digital/third-party-risk.md#46-gcp-as-pci-dss-service-provider) | Cloud Armor WAF, GCP Security Command Center | **GCP Inherited:** Cloud Armor (WAF), Event Threat Detection, Container Threat Detection provide IDS/IPS capabilities |
| 11.5.2 | Change detection mechanism (FIM) | **Compliant (GCP Inherited)** | [Third-Party Risk](../../security/digital/third-party-risk.md), [Vulnerability Management](../../security/digital/vulnerability-management.md) | [TPR §4.6](../../security/digital/third-party-risk.md#46-gcp-as-pci-dss-service-provider) | GCP Container Analysis, Cloud Run immutability | **GCP Inherited:** Cloud Run containers are immutable; changes require new deployment; Container Analysis tracks image changes |
| 11.6.1 | Payment page tamper detection | **Partially Compliant** | [Secure Development](../../security/digital/secure-development.md) | [§4](../../security/digital/secure-development.md#4-how-we-operate-this-control) | Kraken iframe architecture | Payment entry via Kraken iframe (isolated from DASH Main); **Gap:** Explicit script integrity monitoring not documented |

#### Requirement 11 Supporting Evidence Summary

**Testing Schedule (2026):**

| Quarter | ASV Scan | External Pen Test | Internal Pen Test | Segmentation Test |
|---------|----------|-------------------|-------------------|-------------------|
| Q1 | Jan 1-15 | Feb 1-14 | Feb (annual) | Feb (annual) |
| Q2 | Apr 1-15 | May 1-14 | - | - |
| Q3 | Jul 1-15 | Aug 1-14 | - | - |
| Q4 | Oct 1-15 | Nov 1-14 | - | - |

**Vendor Qualification Requirements:**

| Vendor Type | Qualification |
|-------------|---------------|
| ASV | PCI SSC Approved Scanning Vendor list |
| Pen Test | CREST, OSCP, CEH or equivalent; PCI experience |

**Remediation Timelines:**

| Severity | CVSS | Deadline | Retest |
|----------|------|----------|--------|
| Critical | 9.0-10.0 | 15 days | Required |
| High | 7.0-8.9 | 30 days | Required |
| Medium | 4.0-6.9 | 90 days | Recommended |
| Low | 0.1-3.9 | Next release | No |

**Documentation References:**
- vulnerability-management.md Section 5.1: Security testing overview
- vulnerability-management.md Section 5.2: ASV external scanning
- vulnerability-management.md Section 5.3: External penetration testing
- vulnerability-management.md Section 5.4: Internal penetration testing
- vulnerability-management.md Section 5.5: Segmentation validation
- vulnerability-management.md Section 5.6: Findings remediation process
- vulnerability-management.md Section 5.7: Vendor management

### Requirement 12: Support Information Security with Policies and Programs

| Req ID | Requirement Summary | Status | Supporting Document | Section Reference | Evidence | Gaps/Notes |
|--------|---------------------|--------|---------------------|-------------------|----------|------------|
| 12.1.1 | Overall security policy established, published, maintained, disseminated | **Compliant** | [Security Policy & Awareness](../../security/digital/security-policy-awareness.md), All control documents | [SPA §4.1-4.2](../../security/digital/security-policy-awareness.md#41-policy-documentation) | Internal Portal, policy index, annual acknowledgment | All security policies documented, stored in Internal Portal, organized by control area; disseminated to all personnel |
| 12.1.2 | Policy reviewed annually, updated as needed | **Compliant** | [Security Policy & Awareness](../../security/digital/security-policy-awareness.md), All documents | [SPA §4.8](../../security/digital/security-policy-awareness.md#8-review--maintenance), [SPA §8](../../security/digital/security-policy-awareness.md#8-review--maintenance) | Quarterly review (most docs), annual review (SPA), change history | All control documents have defined review cadence and change history |
| 12.1.3 | Roles defined, personnel acknowledge responsibilities | **Compliant** | [Security Policy & Awareness](../../security/digital/security-policy-awareness.md), [Security Training Guide](../../security/training/security-training-guide.md) | [SPA §4.2](../../security/digital/security-policy-awareness.md#42-annual-policy-acknowledgment) | Acknowledgment form, role definitions | Section 4.2: Annual policy acknowledgment with signed statement; Section 3: Roles tables in all docs |
| 12.1.4 | CISO or security-knowledgeable executive assigned | **Compliant** | All documents | §3 (all docs) | CTO ownership | CTO is designated security owner across all control documents |
| 12.2.1 | Acceptable use policies for end-user technologies | **Partially Compliant** | [Security Policy & Awareness](../../security/digital/security-policy-awareness.md) | [§4.6](../../security/digital/security-policy-awareness.md#46-device-security) | Device security requirements, BYOD policy | Device security requirements documented; BYOD policy defined; Missing: explicit AUP for approved software/hardware list |
| 12.3.1 | Targeted risk analysis documented | **Partially Compliant** | [Security Standards & Exception Governance](../../security/digital/security-standards-governance.md) | [§4.5 (Risk Classification)](../../security/digital/security-standards-governance.md#45-risk-classification) | Risk levels defined, exception approval based on risk | Risk classification framework documented (Low/Standard/High); exception approval tied to risk level; formal standalone TRA document still needed for specific requirements |
| 12.3.2 | Customized approach analysis | **Not Applicable** | - | - | - | Not using customized approach |
| 12.3.3 | Cryptographic cipher suites documented, reviewed annually | **Partially Compliant** | [Crypto Key Mgmt](../../security/digital/cryptographic-key-management.md), [Network Security](../../security/digital/network-security.md) | [CKM §4](../../security/digital/cryptographic-key-management.md#4-how-we-operate-this-control), [NS §4.6.3](../../security/digital/network-security.md#463-ssltls-certificate-management) | AES-256-GCM, TLS 1.2+, certificate inventory | Algorithms documented; certificate inventory exists; Missing: comprehensive cipher suite inventory with review process |
| 12.3.4 | Hardware/software technologies reviewed annually | **Compliant** | [System Component Inventory](../../security/digital/system-component-inventory.md) | [§4.1-4.4](../../security/digital/system-component-inventory.md#4-how-we-operate-this-control), [§5](../../security/digital/system-component-inventory.md#5-operational-guarantees) | Tech stack documented, quarterly review, npm audit | Application inventory, tech stack (Node.js 20, NestJS 10), dependencies tracked via npm audit in CI/CD; quarterly review process |
| 12.5.1 | System component inventory maintained | **Compliant** | [System Component Inventory](../../security/digital/system-component-inventory.md) | [§4.1-4.4](../../security/digital/system-component-inventory.md#4-how-we-operate-this-control) | Inventory document, package.json | Application inventory (DASH Main, Kraken, Admin Portal), tech stack, infrastructure, dependencies tracked; quarterly review |
| 12.5.2 | PCI scope documented annually | **Compliant** | This document, [Company Overview](../../security/company-overview.md) | [Scope section](#scope-definition) | Scope definition, data flow diagrams | Scope fully documented including CDE (Kraken), connected systems, out-of-scope systems, segmentation controls |
| 12.6.1 | Security awareness program implemented | **Compliant** | [Security Policy & Awareness](../../security/digital/security-policy-awareness.md), [Security Training Guide](../../security/training/security-training-guide.md) | [SPA §4.3](../../security/digital/security-policy-awareness.md#43-security-awareness-training), [Training full doc](../../security/training/security-training-guide.md) | Training guide (10 modules), acknowledgment form | Comprehensive program: 10 modules covering all staff (Modules 1-7), QA (Module 8), Engineering (Modules 9-10); role-based training tracks |
| 12.6.2 | Awareness program reviewed annually, updated for new threats | **Compliant** | [Security Policy & Awareness](../../security/digital/security-policy-awareness.md) | [§4.8](../../security/digital/security-policy-awareness.md#8-review--maintenance), [§8](../../security/digital/security-policy-awareness.md#8-review--maintenance) | Version control, change history | Annual review process documented; update triggers include new threats, incidents, system changes; v1.1 added phishing/MFA content |
| 12.6.3 | Personnel training upon hire, annually, with acknowledgment | **Compliant** | [Security Policy & Awareness](../../security/digital/security-policy-awareness.md), [Security Training Guide](../../security/training/security-training-guide.md) | [SPA §4.3](../../security/digital/security-policy-awareness.md#43-security-awareness-training), [Training Completion](../../security/training/security-training-guide.md) | Acknowledgment form, completion tracking | New hire within 30 days; annual Q1; multiple methods (in-person, video); signed acknowledgment form |
| 12.6.3.1 | Training includes phishing and social engineering | **Compliant** | [Security Training Guide](../../security/training/security-training-guide.md) | [Module 3](../../security/training/security-training-guide.md#module-3-phishing--social-engineering) | Training content, examples | Module 3: Phishing & Social Engineering (45 min) - common types, real examples, red flags, Outlook reporting, simulated exercises |
| 12.6.3.2 | Training includes acceptable use of technologies | **Compliant** | [Security Training Guide](../../security/training/security-training-guide.md), [Security Policy & Awareness](../../security/digital/security-policy-awareness.md) | [Training Module 5](../../security/training/security-training-guide.md#module-5-device-security), [SPA §4.6](../../security/digital/security-policy-awareness.md#46-device-security) | Device security module, BYOD policy | Module 5: Device Security (30 min) covers device requirements, public WiFi, BYOD restrictions, software installation rules |
| 12.7.1 | Personnel screening prior to CDE access | **Partially Compliant** | - | - | - | Not explicitly documented; Gap: Need personnel screening policy |
| 12.8.1 | TPSP list maintained | **Compliant** | [Third-Party Risk](../../security/digital/third-party-risk.md) | [§4.5](../../security/digital/third-party-risk.md#45-saas-vendor-management) | Vendor table | Critical vendors listed: GCP, Soepay, GP, Microsoft 365 |
| 12.8.2 | Written TPSP agreements | **Partially Compliant** | [Third-Party Risk](../../security/digital/third-party-risk.md) | - | - | Agreements implied but not explicitly documented; Gap: Document written agreements |
| 12.8.3 | TPSP due diligence process | **Partially Compliant** | [Third-Party Risk](../../security/digital/third-party-risk.md) | [§4.5](../../security/digital/third-party-risk.md#45-saas-vendor-management) | Vendor selection criteria | Due diligence process exists [ASSUMPTION]; Gap: Formalize process documentation |
| 12.8.4 | TPSP PCI status monitored annually | **Partially Compliant** | [Third-Party Risk](../../security/digital/third-party-risk.md) | [§4.4](../../security/digital/third-party-risk.md#44-payment-gateway-management-soepay-gp) | GCP AOC reference | GCP PCI DSS 4.0.1 Level 1 documented; Gap: Annual monitoring process for all TPSPs |
| 12.8.5 | TPSP responsibility matrix documented | **Partially Compliant** | [Third-Party Risk](../../security/digital/third-party-risk.md), This document | [GCP Inherited Controls](../../security/digital/third-party-risk.md#46-gcp-as-pci-dss-service-provider) | GCP Shared Responsibility Matrix link | GCP responsibility matrix referenced; Gap: Create matrices for Soepay, GP |
| 12.10.1 | Incident response plan exists | **Compliant** | [Incident Response](../../security/digital/incident-response.md) | [Full document](../../security/digital/incident-response.md) | IR plan | Comprehensive plan: roles, communication, containment, recovery, data backup, legal, payment brand notification |
| 12.10.2 | IR plan reviewed/tested annually | **Partially Compliant** | [Incident Response](../../security/digital/incident-response.md) | [§8](../../security/digital/incident-response.md#8-review--maintenance) | Quarterly review | Quarterly review documented; Gap: Annual tabletop test not explicitly documented |
| 12.10.3 | 24/7 incident response personnel | **Compliant** | [Incident Response](../../security/digital/incident-response.md) | [§4.1](../../security/digital/incident-response.md#41-incident-detection) | On-call engineer, escalation path | Engineering on-call rotation; CTO escalation for security incidents; contact methods documented |
| 12.10.4 | IR personnel trained | **Compliant** | [Security Training Guide](../../security/training/security-training-guide.md) | [Module 7](../../security/training/security-training-guide.md#module-7-incident-reporting) | Training module, acknowledgment | Module 7: Incident Reporting (15 min) - what to report, how to report, timeline, examples; All staff complete this module |
| 12.10.4.1 | IR training frequency per risk analysis | **Partially Compliant** | [Security Training Guide](../../security/training/security-training-guide.md) | [Module 7](../../security/training/security-training-guide.md#module-7-incident-reporting), Training Completion | Annual training | Annual training documented; Gap: No formal TRA determining frequency |
| 12.10.5 | IR includes monitoring alerts (IDS, NSC, FIM, payment pages, wireless) | **Compliant** | [Incident Response](../../security/digital/incident-response.md), [Logging & Monitoring](../../security/digital/logging-monitoring.md) | [IR §4.1](../../security/digital/incident-response.md#41-incident-detection) | Alert sources documented | GCP Cloud Monitoring alerts, Security Command Center, CS reports, Cloud Audit Logs anomaly detection |
| 12.10.6 | IR evolved with lessons learned | **Compliant** | [Incident Response](../../security/digital/incident-response.md) | [§4.6](../../security/digital/incident-response.md#46-post-incident-report--review) | Post-incident review | Post-incident review process documented; follow-up actions tracked; lessons incorporated |
| 12.10.7 | Unexpected PAN discovery procedures | **Partially Compliant** | [Secure Development](../../security/digital/secure-development.md) | [§4.2.4](../../security/digital/secure-development.md#424-data-classification), [§4.5.3](../../security/digital/secure-development.md#45-tokenization-flow) | Data scanning references | Cardholder data storage table defined; data minimization policy; Gap: Explicit unexpected PAN procedure not documented |

**Requirement 12 Summary:**
- **Compliant:** 21
- **Partially Compliant:** 10
- **Not Compliant:** 0
- **Not Applicable:** 1

**Key Improvements (2026-01-30):**
- Security Training Guide (10 modules) provides comprehensive awareness program
- Annual policy acknowledgment process with signed statements
- Phishing and social engineering training (Module 3)
- Device security and acceptable use training (Module 5)
- Incident reporting training for all staff (Module 7)
- Engineering-specific secure development modules (9-10)
- MFA requirements documented for all systems

**Remaining Gaps:**
- 12.7.1: Personnel screening policy needs documentation
- 12.8.2-12.8.5: TPSP written agreements and responsibility matrices (except GCP) need documentation
- 12.2.1: Explicit approved software/hardware list for AUP
- 12.3.1: Formal targeted risk analysis document
- 12.10.2: Annual IR tabletop test documentation
- 12.10.7: Explicit unexpected PAN discovery procedure

---

## Gap Summary

### Critical Gaps (Must Address)

| Domain | Gap | Impact | Notes |
|--------|-----|--------|-------|
| ~~**Req 11**~~ | ~~No penetration testing program~~ | ~~High~~ | **RESOLVED:** vulnerability-management.md v1.2 - quarterly external pen tests, annual internal pen tests |
| ~~**Req 11**~~ | ~~No ASV external vulnerability scanning~~ | ~~High~~ | **RESOLVED:** vulnerability-management.md v1.2 - quarterly ASV scanning documented |
| ~~**Req 10**~~ | ~~Log retention below 12 months~~ | ~~High~~ | **RESOLVED:** Cloud Logging retention configured to 365 days (2026-01-30) |
| ~~**Req 10**~~ | ~~No daily log review process~~ | ~~High~~ | **RESOLVED:** Daily log review process documented in logging-monitoring.md v1.2 Section 4.7 |
| **Req 10** | No file integrity monitoring (FIM) | Medium | Cloud Run immutability as compensating control; serverless has no persistent filesystem; SCC monitors for container modifications |
| **Req 12** | No personnel screening documentation | Medium | Document HR background check process (12.7.1) |
| **Req 1** | Endpoint security for devices connecting to CDE | Medium | Developer workstations; partially addressed in security-policy-awareness.md Section 4.6 |
| **Req 3** | No key custodian acknowledgment | Medium | Key custodians must formally acknowledge responsibilities (3.7.8) |
| **Req 3** | No formal data retention policy | Medium | Document retention periods, verification process, secure deletion (3.2.1) |

### Resolved/Mitigated Gaps (via GCP Inherited Controls and New Documentation)

| Domain | Previous Gap | Resolution |
|--------|-------------|------------|
| **Req 5** | No anti-malware documentation | **Resolved:** GCP Container Threat Detection and Security Command Center provide anti-malware for serverless (no OS-level malware risk) |
| **Req 2** | No system hardening documentation | **Resolved:** Cloud Run/Functions are fully managed by GCP; no OS hardening required |
| **Req 2** | Vendor default accounts | **Resolved:** Serverless has no OS-level accounts; service accounts managed via IAM |
| **Req 6** | No developer security training | **Resolved:** Security Training Guide Modules 9-10 (OWASP Top 10, secure coding, logging standards) |
| **Req 6** | No test data handling procedures | **Resolved:** Security Training Guide Module 8 documents "never use production data for testing" with test card numbers |
| **Req 6** | No software component inventory | **Resolved:** system-component-inventory.md with application inventory, tech stack, npm dependency tracking, quarterly review |
| **Req 11** | No penetration testing | **Resolved:** vulnerability-management.md v1.2 - quarterly external, annual internal pen tests, segmentation validation |
| **Req 11** | No ASV scanning | **Resolved:** vulnerability-management.md v1.2 - quarterly ASV scanning with severity-based remediation timelines |
| **Req 11** | No internal infrastructure scanning | **Mitigated:** Security Health Analytics provides continuous scanning; supplemental scans may be needed |
| **Req 12** | No security awareness program | **Resolved:** Security Training Guide (10 modules) + Security Policy & Awareness document; annual training with acknowledgment |
| **Req 12** | No personnel training | **Resolved:** Comprehensive training: Modules 1-7 (all staff), Module 8 (QA), Modules 9-10 (Engineering); upon hire + annual |
| **Req 12** | No phishing training | **Resolved:** Security Training Guide Module 3: Phishing & Social Engineering (45 min) with Outlook reporting procedure |
| **Req 12** | No acceptable use training | **Resolved:** Security Training Guide Module 5: Device Security; Security Policy & Awareness Section 4.6: BYOD policy |
| **Req 12** | No IR personnel training | **Resolved:** Security Training Guide Module 7: Incident Reporting for all staff |
| **Req 10** | Log retention below 12 months | **Resolved:** Cloud Logging retention configured to 365 days (logging-monitoring.md v1.2 Section 4.5) |
| **Req 10** | No daily log review process | **Resolved:** Daily log review process with checklist, queries, and sign-off (logging-monitoring.md v1.2 Section 4.7) |
| **Req 10** | No targeted risk analysis for log review | **Resolved:** Risk-based review frequency documented (logging-monitoring.md v1.2 Section 4.8) |
| **Req 10** | Limited security monitoring | **Resolved:** Security Command Center Premium enabled - threat detection, vulnerability scanning, compliance monitoring |

### Partial Compliance Items (Strengthen)

| Domain | Item | Current State | Needed |
|--------|------|---------------|--------|
| **Req 1** | Network/data flow diagrams | ASCII diagrams exist | Formal diagram tool (Visio, draw.io) for QSA presentation |
| **Req 1** | Internal IP disclosure | GCP default protection | Document explicit controls for IP/routing disclosure |
| **Req 2** | Vendor default accounts | Not documented | Document default account handling |
| **Req 3** | Data retention policy | Tokenization documented | Formal retention periods, quarterly verification, deletion procedures |
| **Req 3** | ~~SAD non-storage~~ | ~~Assumed via tokenization~~ | **RESOLVED:** Explicit documentation in company-overview.md and secure-development.md |
| **Req 3** | Key inventory algorithm | Marked as [ASSUMPTION] | Verify and document AES-256-GCM usage |
| **Req 3** | Remote access PAN controls | PAN not displayed | Document DLP/technical controls for remote access scenarios |
| **Req 4** | ~~Certificate inventory~~ | ~~GCP-managed certs~~ | **RESOLVED:** Certificate inventory added in network-security.md Section 4.6.3 |
| **Req 6** | ~~Developer training~~ | ~~Not documented~~ | **RESOLVED:** Security Training Guide Modules 9-10 |
| **Req 6** | ~~Test data handling~~ | ~~Not documented~~ | **RESOLVED:** Security Training Guide Module 8 |
| **Req 6** | Test account removal | Training mentions separate credentials | Explicit removal procedure before production |
| **Req 6** | ~~Software component inventory~~ | ~~npm package.json/lock~~ | **RESOLVED:** system-component-inventory.md created |
| **Req 6** | Payment page script management | Kraken iframe architecture | Script inventory and integrity monitoring |
| **Req 8** | Service account management | Partially documented | Document rotation policies |
| **Req 12** | TPSP agreements | Implied | Document written agreements |
| **Req 12** | TPSP responsibility matrix | Not documented | Create responsibility matrix |

### Documentation Gaps

| Document Needed | Purpose | Status |
|-----------------|---------|--------|
| ~~Endpoint Security Policy~~ | ~~Addresses Req 5 (anti-malware)~~ | **RESOLVED:** GCP inherited + Security Policy & Awareness Section 4.6 (device security) |
| ~~Security Awareness Training Program~~ | ~~Addresses Req 12.6~~ | **RESOLVED:** Security Training Guide (10 modules) + Security Policy & Awareness |
| ~~Penetration Testing Methodology~~ | ~~Addresses Req 11.4~~ | **RESOLVED:** vulnerability-management.md v1.2 Section 5 |
| Acceptable Use Policy (approved software/hardware list) | Addresses Req 12.2.1 | Partially addressed; need explicit approved list |
| ~~System Component Inventory~~ | ~~Addresses Req 12.5.1~~ | **RESOLVED:** system-component-inventory.md |
| TPSP Responsibility Matrix (Soepay, GP) | Addresses Req 12.8.5 | GCP matrix exists; need Soepay/GP matrices |
| Personnel Screening Policy | Addresses Req 12.7.1 | Not documented; need HR process documentation |
| Targeted Risk Analysis | Addresses Req 12.3.1 | Implicit in control docs; need standalone formal TRA |
| Unexpected PAN Discovery Procedure | Addresses Req 12.10.7 | Data handling exists; need explicit PAN discovery procedure |

---

## Compliance Summary by Requirement

| Requirement | Compliant | Partially Compliant | Not Compliant | N/A |
|-------------|-----------|---------------------|---------------|-----|
| 1 - Network Security Controls | 15 | 3 | 1 | 1 |
| 2 - Secure Configurations | 8 | 0 | 0 | 3 |
| 3 - Protect Stored Data | 21 | 2 | 1 | 5 |
| 4 - Protect Transmission | 5 | 0 | 0 | 1 |
| 5 - Anti-Malware | 13 | 0 | 0 | 1 |
| 6 - Secure Development | 15 | 3 | 0 | 0 |
| 7 - Restrict Access | 12 | 0 | 0 | 0 |
| 8 - Authentication | 26 | 2 | 0 | 3 |
| 9 - Physical Access | 12 | 0 | 0 | 15 |
| 10 - Logging & Monitoring | 22 | 3 | 0 | 0 |
| 11 - Security Testing | 15 | 2 | 0 | 0 |
| 12 - Policies & Programs | 21 | 10 | 0 | 1 |

### Overall Assessment

**Total Requirements Analyzed:** ~200 (excluding service provider-only)

| Status | Count | Percentage |
|--------|-------|------------|
| **Compliant** | ~180 | 89% |
| **Partially Compliant** | ~24 | 12% |
| **Not Compliant** | ~2 | 1% |
| **Not Applicable** | ~30 | 15% |

**Key Improvements (2026-01-30):**
- **Requirement 1:** NSC Configuration Standards added (Section 4.8)
- **Requirement 2:** GCP serverless inherited controls documented (no OS hardening needed)
- **Requirement 3:** Cardholder data storage documented (company-overview.md Section 3.1.1)
- **Requirement 4:** SSL/TLS certificate management and inventory documented (network-security.md Section 4.6.3)
- **Requirement 5:** GCP Container Threat Detection, Security Command Center, phishing protection (Microsoft 365)
- **Requirement 6:** Developer security training documented (security-training-guide.md Modules 9-10), test data handling (Module 8), WAF protection (Cloud Armor)
- **Requirement 7:** Access control model fully documented with GCP IAM (infrastructure) and Firebase Auth (Admin Portal), quarterly reviews, CHD access restrictions, default deny
- **Requirement 8:** Authentication comprehensively documented with MFA for all CDE access, password history (last 4), 15-min session timeout, same-day account revocation, credential rotation (SQL/KMS 90 days)
- **Requirement 9:** Physical security fully analyzed - CDE is 100% cloud-hosted (GCP inherited), office has access cards and visitor management, no POI devices (card-not-present only), no physical media with CHD
- **Requirement 10:** Logging comprehensively analyzed - GCP Cloud Audit Logs (immutable, 400-day retention) provide inherited controls for log protection and time sync; main gaps are daily review process and application log retention
- **Requirement 11:** Security testing program fully documented - quarterly ASV scanning, quarterly external pen tests, annual internal pen tests, segmentation validation, remediation process with severity-based deadlines, vendor qualification requirements
- **Requirement 12:** Policies & Programs comprehensively documented:
  - Security Policy & Awareness (v1.1): Policy documentation, annual acknowledgment process, MFA requirements, device security, email phishing protection
  - Security Training Guide (10 modules): Comprehensive awareness program covering all staff (Modules 1-7), QA (Module 8), Engineering (Modules 9-10)
  - Training includes phishing/social engineering (Module 3), acceptable use/device security (Module 5), incident reporting (Module 7)
  - Annual training with upon-hire requirement; signed acknowledgment forms
  - Remaining gaps: Personnel screening policy, TPSP written agreements/matrices (except GCP), formal targeted risk analysis
- **GCP PCI DSS 4.0.1 Level 1 Service Provider** compliance documented in Third-Party Risk Management

*Many requirements are satisfied through GCP inherited controls for Cloud Run/Cloud Functions (serverless). See GCP Shared Responsibility Matrix for details.*

---

## Recommendations for Remediation

### Immediate Actions (Before Audit)

1. ~~**Increase log retention to 12 months**~~ - **RESOLVED:** Cloud Logging retention configured to 365 days (2026-01-30)
2. ~~**Establish daily log review process**~~ - **RESOLVED:** logging-monitoring.md v1.2 Section 4.7 documents daily review with checklist
3. ~~**Contract ASV for external scanning**~~ - **RESOLVED:** vulnerability-management.md v1.2 documents quarterly ASV process
4. ~~**Contract penetration tester**~~ - **RESOLVED:** vulnerability-management.md v1.2 documents quarterly external, annual internal pen tests
5. ~~**Evaluate FIM requirement**~~ - **RESOLVED:** Security Command Center monitors containers; Cloud Run immutability documented as compensating control

### Short-Term Actions (1-3 Months)

1. ~~**Create Security Awareness Training program**~~ - **RESOLVED:** Security Training Guide (10 modules) created
2. ~~**Document Endpoint Security policy**~~ - **RESOLVED:** Security Policy & Awareness Section 4.6 covers device security
3. **Create approved software/hardware list** - Completes Acceptable Use Policy (12.2.1)
4. ~~**Create System Component Inventory**~~ - **RESOLVED:** system-component-inventory.md created
5. **Document TPSP responsibility matrices** - Create matrices for Soepay and GP (GCP matrix exists)
6. **Document personnel screening process** - Background check policy for CDE access (12.7.1)

### Medium-Term Actions (3-6 Months)

1. **Create formal Targeted Risk Analysis** - Standalone TRA document (12.3.1)
2. **Document unexpected PAN discovery procedure** - Explicit procedure for 12.10.7
3. **Verify TPSP written agreements exist** - Document/obtain signed agreements (12.8.2)
4. **Document annual IR tabletop test** - Plan and document test procedure (12.10.2)
5. **Create comprehensive cipher suite inventory** - With annual review process (12.3.3)

---

*This mapping was generated based on internal documentation as of 2026-01-29. A formal QSA assessment is required for official PCI DSS compliance validation.*
