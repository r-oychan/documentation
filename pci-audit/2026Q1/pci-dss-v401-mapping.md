# PCI DSS v4.0.1 Compliance Mapping - 2026Q1 Audit

**Assessment Date:** 2026-01-29
**Assessor:** Internal Pre-Assessment
**Scope:** Kraken (PCI CDE), DASH Main Application, Admin Portal

---

## Scope Definition

### Cardholder Data Environment (CDE)
- **Kraken** - Payment gateway module (stores/processes encrypted PAN)
- **Kraken VPC** - Isolated GCP VPC containing Kraken and Kraken Database

### Connected Systems (In-Scope)
- **DASH Main Application** - Connects to Kraken for tokenization (receives tokens only, no PAN)
- **Admin Portal** - Used by CS team to view transactions (masked PAN only)

### Out of Scope
- Third-party payment gateways (Soepay, GP) - their PCI compliance
- GCP infrastructure security - Google's responsibility
- Office WiFi - no direct CDE access

---

## Internal Documentation Inventory

| Document | Path | Last Reviewed |
|----------|------|---------------|
| Access Control & Identity Management | `security/digital/access-control.md` | 2026-01-29 |
| Admin Portal Access Control | `security/digital/admin-portal-access.md` | 2026-01-29 |
| Business Continuity & Disaster Recovery | `security/digital/business-continuity.md` | 2026-01-29 |
| Cryptographic Key Management | `security/digital/cryptographic-key-management.md` | 2026-01-29 |
| Deployment & Release Management | `security/digital/deployment-control.md` | 2026-01-29 |
| Incident Response | `security/digital/incident-response.md` | 2026-01-29 |
| Logging & Monitoring | `security/digital/logging-monitoring.md` | 2026-01-29 |
| Network Security | `security/digital/network-security.md` | 2026-01-29 |
| Secure Development & Data Protection | `security/digital/secure-development.md` | 2026-01-29 |
| Third-Party Risk Management | `security/digital/third-party-risk.md` | 2026-01-29 |
| Vulnerability Management | `security/digital/vulnerability-management.md` | 2026-01-29 |
| Physical Security | `security/physical/physical-security.md` | 2026-01-29 |

---

## PCI DSS v4.0.1 Requirement Mapping

### Requirement 1: Install and Maintain Network Security Controls

| Req ID | Requirement Summary | Status | Supporting Document | Section Reference | Evidence | Gaps/Notes |
|--------|---------------------|--------|---------------------|-------------------|----------|------------|
| 1.1.1 | Security policies documented, up to date, in use, known | **Partially Compliant** | Network Security | Section 4 | Document exists | Document not formally published or disseminated to all parties |
| 1.1.2 | Roles and responsibilities documented | **Compliant** | Network Security | Section 3 | Roles table | CTO owner, Engineering operator |
| 1.2.1 | NSC configuration standards defined, implemented, maintained | **Partially Compliant** | Network Security | Section 4.3 | Firewall rules table | Configuration standards exist but formal standards document not referenced |
| 1.2.2 | NSC changes approved via change control | **Compliant** | Network Security, Deployment Control | Section 4.3 | GitHub PR approvals, GCP Audit Logs | Changes via IaC require PR approval |
| 1.2.3 | Network diagram maintained | **Partially Compliant** | Network Security | Section 4.1 | ASCII diagram | Diagram exists but may need formal diagram with all connections |
| 1.2.4 | Data flow diagram maintained | **Partially Compliant** | Secure Development | Section 2 | ASCII diagram | Shows PAN flow but may need more detail |
| 1.2.5 | Services, protocols, ports identified and approved | **Partially Compliant** | Network Security | Section 4.3 | Firewall rules table | Ports documented (443), business need implied but not explicitly stated |
| 1.2.6 | Insecure services have security features | **Not Applicable** | Network Security | - | - | All services use HTTPS/443 |
| 1.2.7 | NSC configurations reviewed every 6 months | **Not Compliant** | Network Security | Section 8 | - | Quarterly review documented but not 6-month specific NSC review |
| 1.2.8 | NSC configuration files secured | **Compliant** | Network Security, Access Control | Section 4.3 | GCP IAM, GitHub access controls | IaC in GitHub with access controls |
| 1.3.1 | Inbound CDE traffic restricted | **Compliant** | Network Security | Section 4.3 | Firewall rules, Default deny | Only necessary traffic allowed |
| 1.3.2 | Outbound CDE traffic restricted | **Compliant** | Network Security | Section 4.3 | Firewall rules, Default deny | Only to gateways and KMS |
| 1.3.3 | NSCs between wireless and CDE | **Compliant** | Physical Security, Network Security | Section 4.3 | No direct WiFi-to-CDE path | Office WiFi isolated from GCP |
| 1.4.1 | NSCs between trusted/untrusted networks | **Compliant** | Network Security | Section 4.4 | Cloud Armor WAF | WAF at internet edge |
| 1.4.2 | Inbound untrusted traffic restricted | **Compliant** | Network Security | Section 4.4 | WAF, Firewall rules | DDoS protection, OWASP rules |
| 1.4.3 | Anti-spoofing measures | **Partially Compliant** | Network Security | - | GCP managed | Relies on GCP; not explicitly documented |
| 1.4.4 | CHD systems not directly accessible from untrusted | **Compliant** | Network Security | Section 4.2 | Kraken VPC isolation | Kraken in separate VPC, behind WAF |
| 1.4.5 | Internal IPs/routing limited disclosure | **Partially Compliant** | Network Security | - | - | Not explicitly addressed |
| 1.5.1 | Security controls on devices connecting to CDE | **Not Compliant** | - | - | - | No endpoint security documentation |

### Requirement 2: Apply Secure Configurations

| Req ID | Requirement Summary | Status | Supporting Document | Section Reference | Evidence | Gaps/Notes |
|--------|---------------------|--------|---------------------|-------------------|----------|------------|
| 2.1.1 | Security policies documented | **Partially Compliant** | Access Control | Section 4 | Document exists | Not formally disseminated |
| 2.1.2 | Roles documented | **Compliant** | Access Control | Section 3 | Roles table | - |
| 2.2.1 | Configuration standards developed and maintained | **Partially Compliant** | Deployment Control | Section 4 | Pipeline automation | Standards exist via IaC but not formal hardening document |
| 2.2.2 | Vendor default accounts managed | **Not Compliant** | - | - | - | No documentation on default account handling |
| 2.2.3 | Primary functions isolated/secured | **Compliant** | Network Security | Section 4.2 | VPC segmentation | Kraken isolated from DASH Main |
| 2.2.4 | Only necessary services enabled | **Partially Compliant** | Network Security | Section 4.3 | - | Implied via firewall rules; no explicit audit |
| 2.2.5 | Insecure services documented with mitigations | **Not Applicable** | - | - | - | No insecure services documented |
| 2.2.6 | Security parameters prevent misuse | **Partially Compliant** | Secure Development | Section 4 | Code scanning | SonarQube scans; no explicit system hardening |
| 2.2.7 | Non-console admin access encrypted | **Compliant** | Network Security | Section 4 | HTTPS only | All access via HTTPS (port 443) |
| 2.3.1 | Wireless defaults changed | **Not Applicable** | Physical Security | - | - | No wireless in CDE |
| 2.3.2 | Wireless encryption keys changed | **Not Applicable** | - | - | - | No wireless in CDE |

### Requirement 3: Protect Stored Account Data

| Req ID | Requirement Summary | Status | Supporting Document | Section Reference | Evidence | Gaps/Notes |
|--------|---------------------|--------|---------------------|-------------------|----------|------------|
| 3.1.1 | Security policies documented | **Partially Compliant** | Secure Development, Crypto Key Mgmt | - | Documents exist | Not formally published |
| 3.1.2 | Roles documented | **Compliant** | Secure Development | Section 3 | Roles table | - |
| 3.2.1 | SAD not retained after authorization | **Assumed Compliant** | Secure Development | Section 4.5 | - | [ASSUMPTION: Kraken does not store SAD] |
| 3.3.1 | PAN masked when displayed | **Compliant** | Admin Portal Access | Section 4.5 | Data visibility table | Full PAN never displayed |
| 3.3.2 | PAN masked unless business need | **Compliant** | Admin Portal Access | Section 4.5 | - | Only masked PAN shown |
| 3.4.1 | PAN rendered unreadable | **Compliant** | Crypto Key Mgmt, Secure Development | Section 4.4 | GCP KMS encryption | Encrypted with GCP KMS |
| 3.5.1 | Cryptographic keys protected | **Compliant** | Crypto Key Mgmt | Section 4.2 | GCP KMS HSM | Keys in FIPS 140-2 L3 HSM |
| 3.5.1.1 | HSM or key custodians | **Compliant** | Crypto Key Mgmt | Section 4.1 | GCP KMS HSM | Cloud HSM used |
| 3.5.1.2 | Keying material secure | **Compliant** | Crypto Key Mgmt | Section 4.2 | GCP KMS | Key material never exported |
| 3.6.1 | Key management procedures | **Compliant** | Crypto Key Mgmt | Sections 4.1-4.5 | Full lifecycle documented | Generation, storage, rotation, destruction |
| 3.6.1.1 | Strong cryptographic key generation | **Compliant** | Crypto Key Mgmt | Section 4.1 | GCP KMS HSM | FIPS 140-2 validated |
| 3.6.1.2 | Secure key distribution | **Compliant** | Crypto Key Mgmt | Section 4.2 | GCP KMS API | Keys never leave KMS |
| 3.6.1.3 | Secure key storage | **Compliant** | Crypto Key Mgmt | Section 4.2 | GCP KMS HSM | Hardware protection |
| 3.6.1.4 | Cryptoperiod defined | **Compliant** | Crypto Key Mgmt | Section 4.3 | 90-day rotation | Automated rotation |
| 3.7.1 | Key retirement/replacement | **Compliant** | Crypto Key Mgmt | Section 4.3 | Rotation logs | Old versions retained for decryption |
| 3.7.2 | Key destroyed when no longer needed | **Compliant** | Crypto Key Mgmt | Section 4.5 | Destruction process | 24-hour delay, CTO approval |
| 3.7.3 | Compromised keys replaced | **Compliant** | Crypto Key Mgmt | Section 4.6 | Compromise response | Immediate rotation process |

### Requirement 4: Protect CHD During Transmission

| Req ID | Requirement Summary | Status | Supporting Document | Section Reference | Evidence | Gaps/Notes |
|--------|---------------------|--------|---------------------|-------------------|----------|------------|
| 4.1.1 | Security policies documented | **Partially Compliant** | Network Security | - | Document exists | - |
| 4.1.2 | Roles documented | **Compliant** | Network Security | Section 3 | Roles table | - |
| 4.2.1 | Strong cryptography for transmission | **Compliant** | Network Security | Section 4.3 | HTTPS only (443) | TLS via GCP Load Balancer |
| 4.2.1.1 | Trusted keys/certificates | **Partially Compliant** | Crypto Key Mgmt | - | - | TLS managed by GCP; no documentation |
| 4.2.1.2 | Wireless transmissions encrypted | **Not Applicable** | - | - | - | No wireless transmission of CHD |
| 4.2.2 | PAN secured in messaging | **Not Compliant** | - | - | - | No documentation on email/messaging controls |

### Requirement 5: Protect Against Malware

| Req ID | Requirement Summary | Status | Supporting Document | Section Reference | Evidence | Gaps/Notes |
|--------|---------------------|--------|---------------------|-------------------|----------|------------|
| 5.1.1 | Security policies documented | **Not Compliant** | - | - | - | No anti-malware documentation |
| 5.1.2 | Roles documented | **Not Compliant** | - | - | - | - |
| 5.2.1 | Anti-malware deployed | **Not Compliant** | - | - | - | No documentation on endpoint protection |
| 5.2.2 | Anti-malware auto-updates | **Not Compliant** | - | - | - | - |
| 5.2.3 | Anti-malware scans | **Not Compliant** | - | - | - | - |
| 5.2.3.1 | Targeted risk analysis for scan frequency | **Not Compliant** | - | - | - | - |
| 5.3.1 | Anti-malware cannot be disabled | **Not Compliant** | - | - | - | - |
| 5.3.2 | Anti-malware logs retained | **Not Compliant** | - | - | - | - |
| 5.3.2.1 | Anti-malware logs reviewed | **Not Compliant** | - | - | - | - |
| 5.3.3 | Removable media scanned | **Partially Compliant** | Physical Security | Section 4.4 | No physical media policy | Policy states no physical media with sensitive data |
| 5.4.1 | Phishing attack protection | **Not Compliant** | - | - | - | No technical controls documented |

### Requirement 6: Develop and Maintain Secure Systems

| Req ID | Requirement Summary | Status | Supporting Document | Section Reference | Evidence | Gaps/Notes |
|--------|---------------------|--------|---------------------|-------------------|----------|------------|
| 6.1.1 | Security policies documented | **Compliant** | Secure Development | Full document | Document exists | - |
| 6.1.2 | Roles documented | **Compliant** | Secure Development | Section 3 | Roles table | - |
| 6.2.1 | Custom code secure development | **Compliant** | Secure Development | Section 4 | ADR, Code scanning | SonarQube, npm audit |
| 6.2.2 | Developer training | **Not Compliant** | - | - | - | No formal training program documented |
| 6.2.3 | Code reviewed before production | **Compliant** | Deployment Control | Section 4.1 | GitHub PR approvals | 2 engineers + QA + executive |
| 6.2.3.1 | Automated code review tools | **Compliant** | Secure Development | Section 4.2 | SonarQube reports | Automated on every PR |
| 6.2.4 | Software engineering techniques prevent vulns | **Compliant** | Secure Development | Section 4 | Code scanning, ADR | OWASP-aligned scanning |
| 6.3.1 | Security vulnerabilities identified and ranked | **Compliant** | Vulnerability Management | Section 4.3 | Severity matrix | Critical/High/Medium/Low |
| 6.3.2 | System software inventoried | **Partially Compliant** | - | - | - | No explicit inventory document |
| 6.3.3 | Security patches installed timely | **Compliant** | Vulnerability Management | Section 4 | npm audit, PR blocking | Critical/High block merge |
| 6.4.1 | Public web apps protected | **Compliant** | Network Security | Section 4.4 | Cloud Armor WAF | OWASP rules enabled |
| 6.4.2 | Public apps reviewed annually | **Partially Compliant** | Secure Development | Section 8 | Quarterly review | ADR process exists; annual specific review unclear |
| 6.4.3 | Payment page scripts managed | **Partially Compliant** | - | - | - | No documentation on payment page script control |
| 6.5.1 | Change control process | **Compliant** | Deployment Control | Section 4 | PR workflow | Documented approval workflow |
| 6.5.2 | Significant change tested | **Compliant** | Deployment Control | Section 4.1 | QA sign-off | QA approval required |
| 6.5.3 | Pre-production test data | **Not Compliant** | - | - | - | No documentation on test data handling |
| 6.5.4 | Production data not used for testing | **Not Compliant** | - | - | - | Not documented |
| 6.5.5 | Test accounts removed before production | **Not Compliant** | - | - | - | Not documented |
| 6.5.6 | Custom code changes documented | **Compliant** | Deployment Control | Section 4 | GitHub PR history | Full change history |

### Requirement 7: Restrict Access to CHD

| Req ID | Requirement Summary | Status | Supporting Document | Section Reference | Evidence | Gaps/Notes |
|--------|---------------------|--------|---------------------|-------------------|----------|------------|
| 7.1.1 | Security policies documented | **Compliant** | Access Control | Full document | - | - |
| 7.1.2 | Roles documented | **Compliant** | Access Control | Section 3 | Roles table | - |
| 7.2.1 | Access control model | **Compliant** | Access Control | Section 3 | Team access levels table | Role-based access |
| 7.2.2 | Access assigned by job function | **Compliant** | Access Control | Section 4.1 | Provisioning process | Based on team membership |
| 7.2.3 | Privileged access requires approval | **Compliant** | Access Control | Section 4.1 | CTO approval | CTO approves access |
| 7.2.4 | Access reviews | **Compliant** | Access Control | Section 4.4 | Quarterly review | CTO conducts |
| 7.2.5 | Access to system components | **Compliant** | Access Control | Section 3 | Team access table | Minimum necessary |
| 7.2.5.1 | Access to CHD reviewed | **Compliant** | Access Control | Section 4.4 | Quarterly review records | - |
| 7.2.6 | Application access restricted | **Compliant** | Admin Portal Access | Section 4 | Firebase Auth | MFA required |
| 7.3.1 | Access control system config documented | **Partially Compliant** | Access Control | Section 4 | GCP IAM | Process documented; formal config doc unclear |
| 7.3.2 | Deny all by default | **Compliant** | Access Control, Network Security | Section 3 | Team table shows no access by default | CS has no GCP access |
| 7.3.3 | Access revocation | **Partially Compliant** | Access Control | Section 4.4 | Quarterly review | Immediate revocation process unclear |

### Requirement 8: Identify Users and Authenticate Access

| Req ID | Requirement Summary | Status | Supporting Document | Section Reference | Evidence | Gaps/Notes |
|--------|---------------------|--------|---------------------|-------------------|----------|------------|
| 8.1.1 | Security policies documented | **Compliant** | Access Control, Admin Portal Access | Full documents | - | - |
| 8.1.2 | Roles documented | **Compliant** | Access Control | Section 3 | - | - |
| 8.2.1 | Unique user IDs | **Compliant** | Access Control | Section 4.1 | Google accounts, Firebase Auth | Individual accounts |
| 8.2.2 | Shared/generic accounts managed | **Partially Compliant** | Access Control | - | - | Service accounts exist; management unclear |
| 8.2.3 | Service account authentication | **Partially Compliant** | Crypto Key Mgmt | Section 3 | Service account credentials | Rotation policy marked as assumption |
| 8.2.4 | User lifecycle processes | **Compliant** | Access Control | Section 4.1, 4.4 | Provisioning/review | - |
| 8.2.5 | Terminated user access revoked | **Partially Compliant** | Access Control | Section 4.4 | Quarterly review | Immediate revocation not documented |
| 8.2.6 | Inactive accounts removed | **Partially Compliant** | Access Control | Section 4.4 | Quarterly review | No specific inactive policy |
| 8.2.7 | Third-party access managed | **Compliant** | Third-Party Risk | Section 4.2 | Contractor access | GitHub only, no infra |
| 8.2.8 | Shared/generic accounts not used | **Partially Compliant** | Access Control | - | Individual Google accounts | Some service accounts exist |
| 8.3.1 | MFA for CDE access | **Compliant** | Admin Portal Access | Section 4.3 | Passkey MFA | Firebase Auth with passkey |
| 8.3.2 | MFA for non-console admin access | **Compliant** | Access Control | - | GCP IAM | [ASSUMPTION: GCP MFA enabled] |
| 8.3.3 | MFA implemented correctly | **Compliant** | Admin Portal Access | Section 4.3 | Passkey | Not replayable |
| 8.3.4 | MFA for remote access | **Compliant** | Admin Portal Access | Section 4.3 | Passkey required | - |
| 8.3.5 | Authentication factors for passwords | **Compliant** | Admin Portal Access | Section 4.3 | Requirements table | 8 chars, complexity |
| 8.3.6 | Password complexity | **Compliant** | Admin Portal Access | Section 4.3 | - | Upper, lower, number, special |
| 8.3.7 | Passwords different from previous 4 | **Not Compliant** | Admin Portal Access | - | - | Not documented |
| 8.3.8 | Lockout after failed attempts | **Compliant** | Admin Portal Access | Section 4.4 | 5 attempts | Manual unlock |
| 8.3.9 | Password expiry | **Compliant** | Admin Portal Access | Section 4.3 | 180 days | - |
| 8.3.10 | Additional auth factors | **Compliant** | Admin Portal Access | Section 4.3 | Passkey + password | Two factors |
| 8.3.10.1 | Additional factors for customers | **Not Applicable** | - | - | - | Admin portal for internal CS only |
| 8.3.11 | Hardware tokens protected | **Compliant** | Admin Portal Access | Section 4.3 | Passkey | Device-bound |
| 8.4.1 | MFA for admin access | **Compliant** | Admin Portal Access | Section 4.3 | Passkey | - |
| 8.4.2 | MFA for CDE access | **Compliant** | Admin Portal Access | Section 4.3 | Passkey | - |
| 8.4.3 | MFA for remote network access | **Partially Compliant** | - | - | - | GCP access assumed to have MFA |
| 8.5.1 | MFA systems secure | **Compliant** | Admin Portal Access | Section 4.3 | Firebase Auth | Industry standard |
| 8.6.1 | Interactive logins disabled for system accounts | **Partially Compliant** | Crypto Key Mgmt | Section 3 | Service accounts | Service account usage documented |
| 8.6.2 | Hardcoded passwords not used | **Compliant** | Secure Development | Section 4.3 | SonarQube rules | Secret detection |
| 8.6.3 | Application passwords protected | **Compliant** | Secure Development | Section 4.3 | GCP Secret Manager | Secrets in Secret Manager |

### Requirement 9: Restrict Physical Access

| Req ID | Requirement Summary | Status | Supporting Document | Section Reference | Evidence | Gaps/Notes |
|--------|---------------------|--------|---------------------|-------------------|----------|------------|
| 9.1.1 | Security policies documented | **Compliant** | Physical Security | Full document | - | - |
| 9.1.2 | Roles documented | **Compliant** | Physical Security | Section 3 | Roles table | - |
| 9.2.1 | Physical access controls to CDE | **Partially Compliant** | Physical Security | - | - | CDE in GCP (Google's responsibility); no on-prem CDE |
| 9.2.1.1 | Individual physical access | **Not Applicable** | - | - | - | Cloud-hosted CDE |
| 9.2.2 | Visitor management | **Compliant** | Physical Security | Section 4.5 | Visitor log | Escorted, signed in/out |
| 9.2.3 | Visitor identification | **Partially Compliant** | Physical Security | Section 4.5 | Sign-in process | Badge not explicitly documented |
| 9.2.4 | Visitor logs | **Compliant** | Physical Security | Section 4.5 | Visitor log | [ASSUMPTION: 90 days retention] |
| 9.3.1 | Authorized access | **Compliant** | Physical Security | Section 4.1 | Access cards | Card-based access |
| 9.3.1.1 | Physical access revoked | **Compliant** | Physical Security | Section 4.1 | Card deprovisioning | Within 24 hours |
| 9.3.2 | Access devices protected | **Partially Compliant** | Physical Security | Section 7 | Lost card process | - |
| 9.3.3 | Physical access controlled to network jacks | **Not Applicable** | - | - | - | Cloud infrastructure |
| 9.3.4 | Console access limited | **Not Applicable** | - | - | - | Cloud infrastructure |
| 9.4.1 | Media physically secured | **Compliant** | Physical Security | Section 4.4 | No physical media policy | No printing/USB with CHD |
| 9.4.1.1 | Offline media backups secured | **Not Applicable** | Business Continuity | Section 4 | GCP automated | No offline backups |
| 9.4.1.2 | Media classified | **Partially Compliant** | - | - | - | No explicit classification |
| 9.4.2 | Media sent securely | **Compliant** | Physical Security | Section 4.4 | Prohibited | No media sent |
| 9.4.3 | Media approved before moving | **Compliant** | Physical Security | Section 4.4 | Prohibited | No media moved |
| 9.4.4 | Inventory of media | **Not Applicable** | - | - | - | No physical media |
| 9.4.5 | Media destroyed securely | **Not Applicable** | - | - | - | No physical media |
| 9.4.5.1 | Verify media destroyed | **Not Applicable** | - | - | - | No physical media |
| 9.4.6 | Hard-copy materials destroyed | **Compliant** | Physical Security | Section 4.4 | No printing policy | CHD not printed |
| 9.4.7 | Electronic media destroyed | **Not Applicable** | - | - | - | Cloud-hosted |
| 9.5.1 | POI devices protected | **Not Applicable** | - | - | - | No POI devices |
| 9.5.1.1 | POI device inventory | **Not Applicable** | - | - | - | No POI devices |
| 9.5.1.2 | POI devices inspected | **Not Applicable** | - | - | - | No POI devices |
| 9.5.1.2.1 | POI inspection frequency | **Not Applicable** | - | - | - | No POI devices |
| 9.5.1.3 | POI personnel trained | **Not Applicable** | - | - | - | No POI devices |

### Requirement 10: Log and Monitor All Access

| Req ID | Requirement Summary | Status | Supporting Document | Section Reference | Evidence | Gaps/Notes |
|--------|---------------------|--------|---------------------|-------------------|----------|------------|
| 10.1.1 | Security policies documented | **Compliant** | Logging & Monitoring | Full document | - | - |
| 10.1.2 | Roles documented | **Compliant** | Logging & Monitoring | Section 3 | Roles table | - |
| 10.2.1 | Audit logs enabled | **Compliant** | Logging & Monitoring | Section 4.1 | GCP Cloud Logging | All apps |
| 10.2.1.1 | User access to CHD logged | **Compliant** | Admin Portal Access | Section 4.5 | Refund/void logs | Agent ID, timestamp |
| 10.2.1.2 | Admin actions logged | **Compliant** | Logging & Monitoring | Section 4.1 | GCP Audit Logs | - |
| 10.2.1.3 | Audit log access logged | **Compliant** | Logging & Monitoring | Section 6 | GCP Audit Logs | Log access records |
| 10.2.1.4 | Invalid access attempts logged | **Compliant** | Admin Portal Access | Section 4.4 | Firebase Auth | Failed logins |
| 10.2.1.5 | Identity changes logged | **Compliant** | Logging & Monitoring | Section 4.1 | GCP Audit Logs | Account changes |
| 10.2.1.6 | Audit log start/stop logged | **Partially Compliant** | - | - | GCP managed | Relies on GCP |
| 10.2.1.7 | System object changes logged | **Compliant** | Logging & Monitoring | Section 4.1 | GCP Audit Logs | - |
| 10.2.2 | Audit logs contain required details | **Compliant** | Logging & Monitoring | Section 4.1 | Log format | User ID, event type, timestamp |
| 10.3.1 | Log access restricted | **Compliant** | Logging & Monitoring | Section 4.3 | Access table | Engineering only |
| 10.3.2 | Logs protected from modification | **Compliant** | Logging & Monitoring | - | GCP managed | Cloud-hosted logs |
| 10.3.3 | Logs backed up centrally | **Compliant** | Logging & Monitoring | Section 4.1 | GCP Cloud Logging | Centralized |
| 10.3.4 | File integrity monitoring on logs | **Not Compliant** | - | - | - | No FIM documented |
| 10.4.1 | Security event logs reviewed daily | **Not Compliant** | Logging & Monitoring | Section 4.4 | Alerting exists | No daily review process documented |
| 10.4.1.1 | Automated log review | **Partially Compliant** | Logging & Monitoring | Section 4.4 | Alert configuration | Alerts exist; not comprehensive |
| 10.4.2 | Other system logs reviewed periodically | **Partially Compliant** | Logging & Monitoring | Section 4.4 | - | Ad-hoc review |
| 10.4.2.1 | Log review frequency defined | **Not Compliant** | - | - | - | No risk analysis |
| 10.4.3 | Exceptions addressed | **Compliant** | Incident Response | Section 4 | Incident process | - |
| 10.5.1 | Logs retained 12 months, 3 months online | **Not Compliant** | Logging & Monitoring | Section 4.5 | [ASSUMPTION: 30 days app logs] | Retention below 12 months |
| 10.6.1 | Time synchronization | **Compliant** | - | - | GCP managed | Cloud NTP |
| 10.6.2 | Consistent time settings | **Compliant** | - | - | GCP managed | - |
| 10.6.3 | Time settings protected | **Compliant** | - | - | GCP managed | - |
| 10.7.2 | Security control failures detected and alerted | **Partially Compliant** | Logging & Monitoring | Section 4.4 | Some alerts | Not all controls monitored |
| 10.7.3 | Security control failures responded to | **Compliant** | Incident Response | Section 4 | Incident process | - |

### Requirement 11: Test Security Systems and Networks

| Req ID | Requirement Summary | Status | Supporting Document | Section Reference | Evidence | Gaps/Notes |
|--------|---------------------|--------|---------------------|-------------------|----------|------------|
| 11.1.1 | Security policies documented | **Partially Compliant** | Vulnerability Management | - | Document exists | Testing procedures incomplete |
| 11.1.2 | Roles documented | **Compliant** | Vulnerability Management | Section 3 | - | - |
| 11.2.1 | Wireless access points managed | **Compliant** | Physical Security | Section 4.3 | No CDE wireless | Office WiFi isolated |
| 11.2.2 | Authorized wireless inventory | **Compliant** | Physical Security | Section 4.3 | - | No CDE wireless |
| 11.3.1 | Internal vulnerability scans quarterly | **Not Compliant** | Vulnerability Management | - | - | No internal scanning documented |
| 11.3.1.1 | Other vulnerabilities managed by risk | **Not Compliant** | - | - | - | No risk analysis |
| 11.3.1.2 | Authenticated scanning | **Not Compliant** | - | - | - | Not documented |
| 11.3.1.3 | Scans after significant change | **Partially Compliant** | Vulnerability Management | Section 4.2 | PR scans | Code scans on every PR; infra scans unclear |
| 11.3.2 | External vulnerability scans by ASV | **Not Compliant** | - | - | - | No ASV scans documented |
| 11.3.2.1 | External scans after significant change | **Not Compliant** | - | - | - | - |
| 11.4.1 | Penetration testing methodology | **Not Compliant** | - | - | - | No pen test documentation |
| 11.4.2 | Internal penetration testing | **Not Compliant** | - | - | - | - |
| 11.4.3 | External penetration testing | **Not Compliant** | - | - | - | - |
| 11.4.4 | Pen test findings corrected | **Not Compliant** | - | - | - | - |
| 11.4.5 | Segmentation testing | **Not Compliant** | - | - | - | VPC segmentation exists but not tested |
| 11.5.1 | IDS/IPS deployed | **Partially Compliant** | Network Security | Section 4.4 | Cloud Armor WAF | WAF exists; not full IDS/IPS |
| 11.5.2 | Change detection mechanism | **Not Compliant** | - | - | - | No FIM documented |
| 11.6.1 | Payment page tamper detection | **Not Compliant** | - | - | - | No documentation |

### Requirement 12: Support Information Security with Policies and Programs

| Req ID | Requirement Summary | Status | Supporting Document | Section Reference | Evidence | Gaps/Notes |
|--------|---------------------|--------|---------------------|-------------------|----------|------------|
| 12.1.1 | Overall security policy | **Partially Compliant** | All documents | - | Control documents | No single overarching policy |
| 12.1.2 | Policy reviewed annually | **Compliant** | All documents | Section 8 | Quarterly review | - |
| 12.1.3 | Roles defined, acknowledged | **Partially Compliant** | All documents | Section 3 | Roles tables | Acknowledgment not documented |
| 12.1.4 | CISO assigned | **Compliant** | All documents | - | CTO | CTO owns security |
| 12.2.1 | Acceptable use policies | **Not Compliant** | - | - | - | No AUP documented |
| 12.3.1 | Targeted risk analysis | **Not Compliant** | - | - | - | No formal risk analysis |
| 12.3.2 | Customized approach analysis | **Not Applicable** | - | - | - | Not using customized approach |
| 12.3.3 | Cryptographic cipher suites documented | **Not Compliant** | Crypto Key Mgmt | - | - | Algorithm assumed but not inventoried |
| 12.3.4 | Hardware/software review | **Not Compliant** | - | - | - | No annual technology review |
| 12.5.1 | System component inventory | **Not Compliant** | - | - | - | No formal inventory |
| 12.5.2 | PCI scope documented annually | **Partially Compliant** | This document | Scope section | - | First documentation |
| 12.6.1 | Security awareness program | **Not Compliant** | - | - | - | No training program |
| 12.6.2 | Awareness program reviewed | **Not Compliant** | - | - | - | - |
| 12.6.3 | Personnel training | **Not Compliant** | - | - | - | - |
| 12.6.3.1 | Training on threats | **Not Compliant** | - | - | - | - |
| 12.6.3.2 | Training on acceptable use | **Not Compliant** | - | - | - | - |
| 12.7.1 | Personnel screening | **Not Compliant** | - | - | - | No background check documentation |
| 12.8.1 | TPSP list maintained | **Compliant** | Third-Party Risk | Section 4.5 | Vendor table | Critical vendors listed |
| 12.8.2 | Written TPSP agreements | **Partially Compliant** | Third-Party Risk | - | - | Agreements implied but not documented |
| 12.8.3 | TPSP due diligence process | **Partially Compliant** | Third-Party Risk | Section 4.5 | Vendor selection criteria | [ASSUMPTION: Due diligence done] |
| 12.8.4 | TPSP PCI status monitored | **Partially Compliant** | Third-Party Risk | Section 4.4 | - | GCP PCI compliant noted; monitoring unclear |
| 12.8.5 | TPSP responsibility matrix | **Not Compliant** | - | - | - | Not documented |
| 12.10.1 | Incident response plan | **Compliant** | Incident Response | Full document | - | Comprehensive plan |
| 12.10.2 | IR plan reviewed/tested annually | **Partially Compliant** | Incident Response | Section 8 | Quarterly review | Annual test not documented |
| 12.10.3 | 24/7 incident response | **Partially Compliant** | Incident Response | Section 4.1 | On-call engineer | 24/7 availability implied |
| 12.10.4 | IR personnel trained | **Not Compliant** | - | - | - | No training documented |
| 12.10.4.1 | IR training frequency | **Not Compliant** | - | - | - | - |
| 12.10.5 | IR includes monitoring alerts | **Compliant** | Incident Response | Section 4.1 | Alert sources | GCP alerts, CS reports |
| 12.10.6 | IR evolved with lessons learned | **Compliant** | Incident Response | Section 4.6 | Post-incident review | Follow-up actions tracked |
| 12.10.7 | Unexpected PAN procedures | **Not Compliant** | - | - | - | Not documented |

---

## Gap Summary

### Critical Gaps (Must Address)

| Domain | Gap | Impact |
|--------|-----|--------|
| **Req 5** | No anti-malware/endpoint security documentation | High - entire requirement unfulfilled |
| **Req 11** | No penetration testing program | High - required annually |
| **Req 11** | No ASV external vulnerability scanning | High - required quarterly |
| **Req 11** | No internal vulnerability scanning (infrastructure) | High - required quarterly |
| **Req 10** | Log retention below 12 months | High - non-compliant |
| **Req 10** | No daily log review process | High - required |
| **Req 10** | No file integrity monitoring (FIM) | Medium-High |
| **Req 12** | No security awareness training program | High - required annually |
| **Req 12** | No personnel background screening documentation | Medium |
| **Req 12** | No acceptable use policy | Medium |

### Partial Compliance Items (Strengthen)

| Domain | Item | Current State | Needed |
|--------|------|---------------|--------|
| **Req 1** | NSC review frequency | Quarterly documented | Document 6-month NSC-specific review |
| **Req 2** | Vendor default accounts | Not documented | Document default account handling |
| **Req 6** | Developer training | Not documented | Formal secure coding training |
| **Req 6** | Test data handling | Not documented | Document test data procedures |
| **Req 8** | Service account management | Partially documented | Document rotation policies |
| **Req 12** | TPSP agreements | Implied | Document written agreements |
| **Req 12** | TPSP responsibility matrix | Not documented | Create responsibility matrix |

### Documentation Gaps

| Document Needed | Purpose |
|-----------------|---------|
| Endpoint Security Policy | Addresses Req 5 (anti-malware) |
| Security Awareness Training Program | Addresses Req 12.6 |
| Penetration Testing Methodology | Addresses Req 11.4 |
| Acceptable Use Policy | Addresses Req 12.2.1 |
| System Component Inventory | Addresses Req 12.5.1 |
| TPSP Responsibility Matrix | Addresses Req 12.8.5 |
| Personnel Screening Policy | Addresses Req 12.7.1 |

---

## Compliance Summary by Requirement

| Requirement | Compliant | Partially Compliant | Not Compliant | N/A |
|-------------|-----------|---------------------|---------------|-----|
| 1 - Network Security Controls | 10 | 6 | 2 | 1 |
| 2 - Secure Configurations | 4 | 3 | 1 | 3 |
| 3 - Protect Stored Data | 14 | 1 | 0 | 0 |
| 4 - Protect Transmission | 2 | 2 | 1 | 1 |
| 5 - Anti-Malware | 0 | 1 | 10 | 0 |
| 6 - Secure Development | 8 | 3 | 4 | 0 |
| 7 - Restrict Access | 10 | 3 | 0 | 0 |
| 8 - Authentication | 17 | 7 | 1 | 1 |
| 9 - Physical Access | 7 | 3 | 0 | 15 |
| 10 - Logging & Monitoring | 13 | 5 | 4 | 0 |
| 11 - Security Testing | 2 | 2 | 11 | 0 |
| 12 - Policies & Programs | 6 | 8 | 14 | 1 |

### Overall Assessment

**Total Requirements Analyzed:** ~200 (excluding service provider-only)

| Status | Count | Percentage |
|--------|-------|------------|
| **Compliant** | ~93 | 46% |
| **Partially Compliant** | ~44 | 22% |
| **Not Compliant** | ~48 | 24% |
| **Not Applicable** | ~22 | 11% |

---

## Recommendations for Remediation

### Immediate Actions (Before Audit)

1. **Increase log retention to 12 months** - Configure GCP Cloud Logging retention
2. **Establish daily log review process** - Document who reviews and how
3. **Contract ASV for external scanning** - Quarterly requirement
4. **Contract penetration tester** - Annual requirement
5. **Implement file integrity monitoring** - Deploy FIM solution

### Short-Term Actions (1-3 Months)

1. **Create Security Awareness Training program** - Document and implement
2. **Document Endpoint Security policy** - Anti-malware requirements
3. **Create Acceptable Use Policy** - End-user technology guidelines
4. **Create System Component Inventory** - Formal asset list
5. **Document TPSP responsibility matrices** - For Soepay, GP, GCP, etc.

### Medium-Term Actions (3-6 Months)

1. **Implement internal vulnerability scanning** - Tool and process
2. **Establish personnel screening process** - Background checks
3. **Create formal configuration hardening standards** - Beyond IaC
4. **Implement comprehensive FIM** - Payment pages and critical files
5. **Conduct penetration testing** - Internal and external

---

*This mapping was generated based on internal documentation as of 2026-01-29. A formal QSA assessment is required for official PCI DSS compliance validation.*
