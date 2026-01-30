# Network Security

**Owner:** CTO
**Version:** 1.4
**Last Reviewed:** 2026-01-30
**Review Cadence:** Quarterly (Document), Every 6 months (NSC Configuration)

---

## 1. Purpose

We segment and protect network infrastructure to isolate sensitive systems, prevent unauthorized access, and reduce the attack surface. The PCI-scoped environment (Kraken) is isolated in its own VPC with dedicated security controls.

**Risk Reduced:**
- Unauthorized network access to PAN data
- Lateral movement between systems
- External attacks reaching sensitive infrastructure
- Data exfiltration

**Stakeholders:**
- Engineering Team (infrastructure management)
- Customers/Merchants (payment data protection)
- Payment Gateways (secure connectivity)

---

## 2. Scope

### In Scope
- **Systems:** Kraken (PCI scope), DASH Main Application, Admin Portal
- **Infrastructure:** GCP VPCs, firewalls, WAF, load balancers
- **Environments:** Production
- **Network Boundaries:** Internet edge, VPC boundaries, service-to-service communication

### Out of Scope
- Office network (covered in Physical Security)
- Third-party gateway internal networks (Soepay, GP)
- GCP's underlying infrastructure security (Google's responsibility)

---

## 3. Roles & Responsibilities

| Role | Team/Individual | Responsibility |
|------|-----------------|----------------|
| Control Owner | CTO | Defines network architecture, approves changes |
| Operator | Engineering Team | Configures and maintains network controls |
| Change Approver | CTO | Approves firewall rule and VPC changes |

---

## 4. How We Operate This Control

### 4.1 Network Architecture

#### Architecture Diagram

![DASH Kraken Network Architecture](images/dash-kraken-architecture-v4-direct-pan.jpg)

*Figure: Network architecture showing VPC segmentation. Kraken VPC (CDE) is isolated from DASH Main VPC. PAN data flows directly to Kraken, bypassing main backend. Only tokenization API connects the VPCs.*

#### ASCII Reference Diagram

```
                              ┌─────────────────────────────────────┐
                              │            Internet                  │
                              └──────────────────┬──────────────────┘
                                                 │
                              ┌──────────────────▼──────────────────┐
                              │         GCP Cloud Armor (WAF)        │
                              │     • DDoS protection                │
                              │     • OWASP rule sets                │
                              │     • Rate limiting                  │
                              └──────────────────┬──────────────────┘
                                                 │
                 ┌───────────────────────────────┼───────────────────────────────┐
                 │                               │                               │
    ┌────────────▼────────────┐    ┌────────────▼────────────┐                  │
    │    DASH Main VPC        │    │     Kraken VPC          │                  │
    │                         │    │     (PCI Scope)         │                  │
    │  ┌───────────────────┐  │    │  ┌───────────────────┐  │                  │
    │  │  DASH Application │  │    │  │     Kraken        │  │                  │
    │  │  (Tokens only -   │  │    │  │  • PAN storage    │  │                  │
    │  │   no PAN)         │  │    │  │  • Tokenization   │  │                  │
    │  └─────────┬─────────┘  │    │  │  • KMS encryption │  │                  │
    │            │            │    │  └─────────┬─────────┘  │                  │
    │  ┌─────────▼─────────┐  │    │            │            │                  │
    │  │   Admin Portal    │  │    │  ┌─────────▼─────────┐  │                  │
    │  │   (CS access)     │  │    │  │  Kraken Database  │  │                  │
    │  └───────────────────┘  │    │  │  (Encrypted PAN)  │  │                  │
    │                         │    │  └───────────────────┘  │                  │
    │      GCP Firewall       │    │      GCP Firewall       │                  │
    └─────────────────────────┘    └────────────┬────────────┘                  │
                                                │                               │
                                   ┌────────────▼────────────┐                  │
                                   │   Payment Gateways      │◀─────────────────┘
                                   │   • Soepay (Offline)    │
                                   │   • GP (MOTO)           │
                                   └─────────────────────────┘
```

### 4.2 VPC Segmentation

| VPC | Purpose | Contains | PAN Access |
|-----|---------|----------|------------|
| **DASH Main VPC** | General application workloads | DASH App, Admin Portal | No (tokens only) |
| **Kraken VPC** | PCI-scoped payment processing | Kraken service, Kraken DB | Yes (encrypted) |

**Segmentation Principle:**
- Kraken VPC is isolated from DASH Main VPC
- Communication between VPCs is restricted to tokenization API only
- DASH Main never receives or stores PAN - only tokens

### 4.3 Firewall Rules

**Kraken VPC Firewall (PCI Scope):**

| Direction | Source | Destination | Port | Purpose |
|-----------|--------|-------------|------|---------|
| Ingress | DASH Main VPC | Kraken API | 443 | Tokenization requests |
| Ingress | [ASSUMPTION: Specific IPs] | Kraken API | 443 | Gateway callbacks |
| Egress | Kraken | Soepay | 443 | Payment processing |
| Egress | Kraken | GP | 443 | Payment processing |
| Egress | Kraken | GCP KMS | 443 | Encryption/decryption |
| Default | Any | Any | Any | **Deny** |

**DASH Main VPC Firewall:**

| Direction | Source | Destination | Port | Purpose |
|-----------|--------|-------------|------|---------|
| Ingress | Internet (via WAF) | Load Balancer | 443 | User traffic |
| Egress | DASH App | Kraken VPC | 443 | Tokenization |
| Egress | DASH App | Internet | 443 | Third-party APIs |
| Default | Any | Any | Any | **Deny** |

**Firewall Change Process:**
1. Engineer submits PR with firewall rule change (Infrastructure as Code)
2. Requires approval per Deployment & Release Management control
3. Changes applied via GitHub Actions
4. Changes logged in GCP Audit Logs

### 4.4 Web Application Firewall (WAF)

**Tool:** GCP Cloud Armor

**Protection Enabled:**
- DDoS protection (automatic)
- OWASP Top 10 rule sets
- Rate limiting
- Geographic restrictions [ASSUMPTION: If applicable]
- Custom rules for application-specific threats

**WAF Placement:**
- Front of all internet-facing services
- Filters traffic before reaching application

**WAF Management:**
- Rules managed via GCP Console / Infrastructure as Code
- Changes follow standard change process
- Alerts configured for blocked attacks

### 4.5 GCP Managed Security (Inherited Controls)

We deploy on Google Cloud Platform, which maintains **PCI DSS 4.0.1 Level 1 Service Provider** compliance. Our applications run on **Cloud Run** and **Cloud Functions** (serverless), which provide significant inherited security controls.

**GCP PCI DSS Compliance:**
- GCP has been independently assessed against PCI DSS 4.0.1 by a Qualified Security Assessor (QSA)
- GCP's Attestation of Compliance (AOC) is available via [Compliance Reports Manager](https://cloud.google.com/security/compliance/compliance-reports-manager)
- [GCP PCI DSS Shared Responsibility Matrix](https://services.google.com/fh/files/misc/gcp_pci_dss_v4_responsibility_matrix.pdf) documents control ownership

**Inherited Controls (GCP Responsibility):**

| PCI Req | Control | GCP Responsibility | Evidence |
|---------|---------|-------------------|----------|
| 1.x | Physical network infrastructure | GCP owns all physical network equipment | GCP AOC |
| 2.x | System hardening (infrastructure) | GCP hardens underlying infrastructure | GCP AOC |
| 3.x | Encryption at rest (default) | GCP encrypts all data at rest by default | GCP documentation |
| 4.x | Encryption in transit (internal) | GCP encrypts all internal network traffic | GCP AOC |
| 9.x | Physical data center security | GCP controls physical access to data centers | GCP AOC, SOC 2 |
| 10.x | Infrastructure audit logging | GCP Cloud Audit Logs automatically enabled | Cloud Audit Logs |
| 12.x | GCP security policies | GCP maintains security policies for infrastructure | GCP AOC |

**Shared Controls (Serverless - Cloud Run / Cloud Functions):**

| Control | GCP Provides | Our Responsibility |
|---------|-------------|-------------------|
| Container/function runtime | Managed, patched by GCP | Application code security |
| OS patching | Fully managed by GCP | N/A (no OS access) |
| Network isolation | VPC, VPC Service Controls | Configure ingress/egress settings |
| Scaling & availability | Auto-scaling, multi-zone | Configure min/max instances |
| TLS termination | Managed TLS certificates | Enforce HTTPS-only |
| DDoS protection | Cloud Armor integration | Enable and configure rules |
| IAM authentication | Cloud IAM | Configure IAM policies |

**Serverless Security Benefits:**
- **No OS/patch management:** Cloud Run and Cloud Functions are fully managed; GCP handles all OS patching
- **Immutable deployments:** Each deployment creates new container/function revision
- **Automatic scaling:** No manual capacity management or server hardening
- **Short-lived execution:** Functions execute and terminate (reduced attack surface)
- **VPC Service Controls:** Data exfiltration prevention for serverless workloads

**Our Responsibility (Application Level):**

| Area | What We Configure |
|------|------------------|
| Ingress settings | Cloud Run/Functions network ingress restrictions |
| VPC connectors | Egress through VPC for private resource access |
| Service accounts | Least-privilege IAM for each service |
| Secrets | Application secrets via Secret Manager |
| Application code | Secure coding, vulnerability scanning |
| Data handling | PAN tokenization, log sanitization |

### 4.6 Secure Connectivity Patterns

#### 4.6.1 Database Access via Cloud SQL Proxy

All Cloud Run and Cloud Functions services connect to Cloud SQL databases through **Cloud SQL Auth Proxy**, not direct database connections.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    Database Connectivity Architecture                        │
│                                                                              │
│   ┌──────────────────┐                      ┌──────────────────────────────┐│
│   │  Cloud Run       │                      │       Cloud SQL              ││
│   │  (Kraken)        │                      │       (PostgreSQL)           ││
│   │                  │   Cloud SQL Proxy    │                              ││
│   │  Application ────┼──────────────────────┼──▶ Database                  ││
│   │                  │                      │                              ││
│   └──────────────────┘                      └──────────────────────────────┘│
│                                                                              │
│   Connection Properties:                                                     │
│   ✓ Automatic TLS encryption (managed by GCP)                               │
│   ✓ IAM-based authentication (no database passwords in code)                │
│   ✓ No public IP required on Cloud SQL instance                             │
│   ✓ Connections via Unix socket or TCP with automatic encryption            │
└─────────────────────────────────────────────────────────────────────────────┘
```

**How Cloud SQL Proxy Works:**

| Aspect | Implementation |
|--------|---------------|
| **Connection Method** | Cloud Run built-in Cloud SQL connector (sidecar proxy) |
| **Authentication** | Service account IAM authentication; no database passwords stored |
| **Encryption** | Automatic TLS encryption between proxy and Cloud SQL |
| **Network Path** | Private Google network; traffic never traverses public internet |
| **Authorization** | IAM role `roles/cloudsql.client` required on service account |

**Security Benefits:**

| Benefit | Description |
|---------|-------------|
| **No exposed database ports** | Cloud SQL instance has no public IP; unreachable from internet |
| **No credentials in code** | IAM authentication eliminates database password management |
| **Automatic encryption** | All traffic encrypted; no manual TLS configuration needed |
| **Audit logging** | Cloud SQL connections logged in Cloud Audit Logs |
| **Least privilege** | Each service account has minimal required Cloud SQL permissions |

**Configuration:**
- Cloud Run services configured with Cloud SQL connection in deployment manifest
- Service account granted `roles/cloudsql.client` via IAM
- Cloud SQL instance configured for private IP only (no public IP)

#### 4.6.2 Service-to-Service Communication (HTTPS/SSL)

All HTTP communication between services uses SSL/TLS encryption.

**External Traffic (Internet → Cloud Run):**

| Layer | Encryption |
|-------|------------|
| Client → GCP Load Balancer | TLS 1.2+ (managed certificate) |
| Load Balancer → Cloud Armor | Internal GCP network (encrypted) |
| Cloud Armor → Cloud Run | Internal GCP network (encrypted) |

**Internal Traffic (Service → Service):**

| Communication Path | Encryption Method |
|-------------------|-------------------|
| Cloud Run → Cloud Run | HTTPS (TLS 1.2+) via internal URLs |
| Cloud Run → Cloud Functions | HTTPS (TLS 1.2+) |
| Cloud Run → External APIs | HTTPS (TLS 1.2+) enforced in code |
| Cloud Run → Payment Gateways | HTTPS (TLS 1.2+) required by gateways |
| Cloud Run → GCP KMS | HTTPS (TLS 1.2+) via GCP APIs |

**Enforcement:**
- All Cloud Run services configured for HTTPS-only (HTTP redirects to HTTPS)
- Application code rejects non-HTTPS connections for external calls
- GCP-managed TLS certificates with automatic renewal
- No self-signed certificates in production

**TLS Configuration:**

| Setting | Value |
|---------|-------|
| Minimum TLS Version | TLS 1.2 |
| Preferred TLS Version | TLS 1.3 |
| Certificate Authority | Google Trust Services (managed) |
| Certificate Renewal | Automatic (no manual intervention) |
| Cipher Suites | GCP-managed modern suites only |

#### 4.6.3 SSL/TLS Certificate Management (GCP Managed)

All SSL/TLS certificates are fully managed by Google Cloud Platform. **DASH does not manually create, install, or renew any certificates.**

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    SSL/TLS Certificate Management                            │
│                                                                              │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                    GCP Certificate Manager                           │   │
│   │                                                                      │   │
│   │   • Automatic certificate provisioning                              │   │
│   │   • Automatic renewal (before expiration)                           │   │
│   │   • No manual intervention required                                 │   │
│   │   • Certificate Authority: Google Trust Services                    │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                              │                                               │
│              ┌───────────────┼───────────────┐                              │
│              ▼               ▼               ▼                              │
│   ┌──────────────┐  ┌──────────────┐  ┌──────────────┐                     │
│   │ Cloud Run    │  │ Cloud        │  │ Cloud SQL    │                     │
│   │ Services     │  │ Functions    │  │ Proxy        │                     │
│   │              │  │              │  │              │                     │
│   │ HTTPS auto   │  │ HTTPS auto   │  │ TLS auto     │                     │
│   └──────────────┘  └──────────────┘  └──────────────┘                     │
│                                                                              │
│   DASH Responsibility: NONE (fully managed by GCP)                          │
└─────────────────────────────────────────────────────────────────────────────┘
```

**Certificate Inventory:**

| Service | Certificate Type | Managed By | Renewal | DASH Action Required |
|---------|-----------------|------------|---------|---------------------|
| Cloud Run (all services) | Google-managed SSL | GCP | Automatic | None |
| Cloud Functions | Google-managed SSL | GCP | Automatic | None |
| Cloud SQL Proxy | Google-managed SSL | GCP | Automatic | None |
| GCP Load Balancer | Google-managed SSL | GCP | Automatic | None |
| GCP APIs (KMS, Logging, etc.) | Google Trust Services | GCP | Automatic | None |

**Why GCP-Managed Certificates:**

| Benefit | Description |
|---------|-------------|
| **No expiration risk** | GCP renews certificates automatically before expiration |
| **No private key management** | Private keys are generated and stored by GCP; never exposed |
| **Trusted CA** | Google Trust Services is a widely-trusted public CA |
| **No procurement** | No need to purchase or request certificates from third parties |
| **No installation** | Certificates are automatically deployed to services |
| **Strong cryptography** | GCP enforces modern TLS versions and cipher suites |

**Certificate Lifecycle (Fully Automated):**

| Phase | GCP Responsibility | DASH Responsibility |
|-------|-------------------|---------------------|
| **Provisioning** | Generates certificate when service is deployed | Deploy service via IaC |
| **Installation** | Installs certificate on load balancer/service | None |
| **Renewal** | Renews certificate ~30 days before expiration | None |
| **Revocation** | Handles revocation if certificate is compromised | Report suspected compromise |
| **Monitoring** | Monitors certificate health | None (GCP alerts if issues) |

**What DASH Does NOT Do:**

- ❌ Generate CSRs (Certificate Signing Requests)
- ❌ Purchase certificates from CAs
- ❌ Install certificates manually
- ❌ Track certificate expiration dates
- ❌ Renew certificates
- ❌ Manage private keys
- ❌ Configure cipher suites (GCP manages)

**Verification:**

To verify certificate status for any Cloud Run service:
```bash
# View certificate details for a Cloud Run service
gcloud run services describe [SERVICE_NAME] --region [REGION] --format="value(status.url)"
# Then check certificate via browser or:
openssl s_client -connect [SERVICE_URL]:443 -servername [SERVICE_URL]
```

**Evidence:**
- Certificate provisioning and renewal events logged in Cloud Audit Logs
- Certificate status visible in GCP Console > Certificate Manager
- No manual certificate management tickets or processes exist (by design)

### 4.7 Monitoring & Alerting

**Network Monitoring:**

| What | Tool | Alert Condition |
|------|------|-----------------|
| Firewall denies | GCP VPC Flow Logs | Unusual deny patterns |
| WAF blocks | Cloud Armor logs | Attack patterns, high block rate |
| Traffic anomalies | GCP Cloud Monitoring | Unexpected traffic spikes |
| Cloud SQL connections | Cloud Audit Logs | Unusual connection patterns |

**Logs:**
- VPC Flow Logs enabled for Kraken VPC
- Cloud Armor logs all requests (allowed and blocked)
- Cloud SQL connection logs captured in Cloud Audit Logs
- Logs feed into GCP Log Explorer (see Logging & Monitoring control)

### 4.8 NSC Configuration Standards

This section defines the minimum configuration requirements for all Network Security Controls (NSCs) including GCP Firewalls, Cloud Armor WAF, VPCs, and load balancers.

#### 4.8.1 Approved Protocols & Encryption

| Category | Requirement |
|----------|-------------|
| **Minimum TLS Version** | TLS 1.2 (TLS 1.3 preferred) |
| **HTTPS Required** | All external and internal traffic must use HTTPS (port 443) |
| **Certificate Management** | Managed via GCP Certificate Manager; auto-renewal enabled |
| **Cipher Suites** | GCP-managed modern cipher suites only; weak ciphers disabled |

#### 4.8.2 Permitted Ports

| Port | Protocol | Usage | Allowed For |
|------|----------|-------|-------------|
| 443 | HTTPS | All application traffic | All VPCs |
| 22 | SSH | Emergency access only | Restricted to specific IPs, requires approval |

**All other ports are denied by default.**

#### 4.8.3 Prohibited Configurations

The following are explicitly **NOT PERMITTED**:

| Prohibited | Reason |
|------------|--------|
| Unencrypted protocols (HTTP, FTP, Telnet) | Data exposure risk |
| TLS 1.0, TLS 1.1, SSL | Deprecated, known vulnerabilities |
| Open ingress from 0.0.0.0/0 (except via WAF) | Unrestricted access to CDE |
| Direct database access from internet | Database exposure |
| Firewall rules without documented business justification | Audit compliance |
| Any-to-any allow rules | Violates least privilege |

#### 4.8.4 Default Configuration Requirements

All NSCs must be configured with:

| Requirement | Standard |
|-------------|----------|
| **Default Policy** | Deny all (explicit allow required) |
| **Rule Documentation** | Every rule must have description and business justification |
| **Logging** | All NSCs must have logging enabled |
| **Anti-Spoofing** | Enabled (GCP default for VPC) |
| **Stateful Inspection** | Enabled (GCP Firewall default) |

#### 4.8.5 Infrastructure as Code (IaC) Change Management

All NSC configurations are managed through Infrastructure as Code with strict change controls:

**Tools:**
- **Terraform** for GCP infrastructure definitions
- **GitHub** for version control and PR workflow
- **GitHub Actions** for automated deployment

**Change Process:**

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│  Engineer       │    │  GitHub PR      │    │  Approval       │    │  GitHub Actions │
│  creates branch │───▶│  with IaC       │───▶│  by CTO/        │───▶│  applies        │
│  & modifies     │    │  changes        │    │  Engineering    │    │  changes        │
│  Terraform      │    │                 │    │  Lead           │    │  to GCP         │
└─────────────────┘    └─────────────────┘    └─────────────────┘    └─────────────────┘
```

**Change Control Properties:**

| Property | How Achieved |
|----------|--------------|
| **Auditable** | Full Git history of all changes with author, timestamp, and commit message |
| **Traceable** | PR linked to ticket/issue; approval recorded in GitHub |
| **Revertable** | Git revert to previous state; Terraform state allows rollback |
| **Reviewable** | PR diff shows exact changes; requires approval before merge |

**PR Requirements for NSC Changes:**
1. Clear description of what is changing and why
2. Business justification for new allow rules
3. Security impact assessment for rule modifications
4. Approval from CTO or designated approver
5. Automated validation (Terraform plan) before apply

**Evidence Produced:**
- Git commit history (indefinite retention)
- PR approval records (indefinite retention)
- GitHub Actions deployment logs (90 days in GitHub, exported to GCP Logging)
- Terraform state files (versioned in GCS bucket)

#### 4.8.6 NSC Configuration Review

**Review Frequency:** Every 6 months (minimum)

**Review Process:**
1. Engineering exports current firewall rules and VPC configurations
2. CTO reviews against configuration standards
3. Unnecessary or overly permissive rules identified
4. Remediation PRs created for non-compliant configurations
5. Review completion documented

**Review Checklist:**
- [ ] All rules have valid business justification
- [ ] No rules violate prohibited configurations
- [ ] Default deny policy in place
- [ ] Logging enabled on all NSCs
- [ ] No stale rules (unused for 90+ days)
- [ ] Segmentation between CDE and non-CDE maintained

---

## 5. Operational Guarantees

When this control operates correctly:

- [ ] Kraken (PCI scope) runs in isolated VPC separate from DASH Main
- [ ] All internet traffic passes through WAF (Cloud Armor)
- [ ] Firewall default policy is deny-all
- [ ] Only explicitly allowed traffic can reach Kraken VPC
- [ ] DASH Main VPC can only communicate with Kraken via tokenization API
- [ ] All firewall changes are logged and require approval
- [ ] VPC Flow Logs are enabled for PCI-scoped VPC
- [ ] All NSC configurations are managed via IaC (Terraform) with GitHub PR approval
- [ ] NSC configuration standards are documented and enforced
- [ ] NSC configurations are reviewed at least every 6 months
- [ ] All changes are auditable, traceable, and revertable via Git history
- [ ] **All database connections use Cloud SQL Proxy** (no direct database access)
- [ ] **All HTTP traffic uses TLS 1.2+** (HTTPS enforced everywhere)
- [ ] Cloud SQL instances have no public IP (private access only)
- [ ] **All SSL/TLS certificates are GCP-managed** (no manual certificate management)
- [ ] Certificates renew automatically (no expiration risk)

---

## 6. Evidence Produced

| Evidence Type | Description | System/Tool | Retention | Collection | Owner |
|--------------|-------------|-------------|-----------|------------|-------|
| VPC Flow Logs | Network traffic records | GCP VPC Flow Logs | [ASSUMPTION: 30 days] | Automatic | Engineering |
| Firewall rules | Current rule configuration | GCP Firewall / IaC repo | Indefinite | Automatic | Engineering |
| Firewall change history | Rule modifications | GCP Audit Logs + GitHub | 400 days / Indefinite | Automatic | Engineering |
| WAF logs | Blocked/allowed requests | Cloud Armor | [ASSUMPTION: 30 days] | Automatic | Engineering |
| WAF rule configuration | Current WAF rules | GCP Cloud Armor | Point-in-time | Manual | Engineering |
| IaC Git history | All NSC configuration changes | GitHub | Indefinite | Automatic | Engineering |
| PR approval records | Change approvals with reviewer | GitHub | Indefinite | Automatic | Engineering |
| Terraform state | Current infrastructure state | GCS bucket (versioned) | Indefinite | Automatic | Engineering |
| NSC review records | 6-month configuration reviews | GitHub Issues / Notion | Indefinite | Manual | CTO |

### Evidence Retrieval

- **VPC Flow Logs:** GCP Console > VPC Network > Flow Logs
- **Firewall rules:** GCP Console > VPC Network > Firewall, or GitHub IaC repo
- **WAF logs:** GCP Console > Cloud Armor > Policies > Logs
- **Audit logs:** GCP Console > Logging > Filter by firewall/network changes
- **IaC change history:** GitHub > Infrastructure repo > Commits / Pull Requests
- **Terraform state:** GCS bucket > terraform-state > version history
- **NSC review records:** GitHub Issues labeled "nsc-review" or Notion > Security > NSC Reviews

---

## 7. Exceptions & Edge Cases

### Known Exceptions

| Exception | Justification | Compensating Control | Review Date |
|-----------|--------------|---------------------|-------------|
| None currently | - | - | - |

### Exception Process

1. Exception requests go to CTO
2. CTO evaluates security impact
3. Approved exceptions documented with expiration date
4. Reviewed quarterly

### Edge Cases

- **Emergency firewall change:** Follow Incident Response process. CTO can approve expedited change. Document in incident ticket.
- **New payment gateway integration:** Requires dedicated security review. New egress rules added via standard change process.
- **DDoS attack:** Cloud Armor handles automatically. Engineering monitors and adjusts rules if needed.

---

## 8. Review & Maintenance

**Review Schedule:**
- **Document Review Frequency:** Quarterly
- **NSC Configuration Review Frequency:** Every 6 months (per PCI DSS 1.2.7)
- **Next Document Review:** 2026-04-29
- **Next NSC Configuration Review:** 2026-07-29
- **Reviewer:** CTO

**Update Triggers:**
- New services deployed
- New payment gateway integration
- Security incidents related to network
- Changes to PCI scope
- NSC configuration drift detected

**Change History:**

| Date | Change | Author |
|------|--------|--------|
| 2025-01-27 | Initial document created | [Author] |
| 2026-01-29 | Added NSC Configuration Standards (Section 4.8) with IaC change management | [Author] |
| 2026-01-29 | Expanded GCP inherited controls section with PCI DSS 4.0.1 shared responsibility details for Cloud Run/Functions | [Author] |
| 2026-01-30 | v1.3: Added Section 4.6 Secure Connectivity Patterns - Cloud SQL Proxy for database access, HTTPS/SSL for all HTTP traffic | [Author] |
| 2026-01-30 | v1.4: Added Section 4.6.3 SSL/TLS Certificate Management - documenting full GCP management of all certificates with certificate inventory | [Author] |

---

## 9. Related Controls

| Control Area | Relationship |
|-------------|--------------|
| Secure Development & Data Protection | Kraken architecture and PAN handling defined there |
| Access Control & Identity Management | GCP IAM controls who can modify network config |
| Logging & Monitoring | Network logs feed into central logging |
| Deployment & Release Management | Firewall changes follow deployment process |
| Incident Response | Network incidents handled per incident process |
| Physical Security | Office network isolation covered there |
| Third-Party Risk Management | Payment gateway connectivity managed here |
| Security Policy & Awareness | Staff acknowledgment and training requirements |
