# Network Security

**Owner:** CTO
**Version:** 1.1
**Last Reviewed:** 2026-01-29
**Review Cadence:** Quarterly

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

We rely on GCP for underlying infrastructure security:

| Control | GCP Responsibility | Our Responsibility |
|---------|-------------------|-------------------|
| Physical data center security | GCP | N/A |
| Network infrastructure | GCP | VPC configuration |
| DDoS mitigation | GCP (Cloud Armor) | Enable and configure |
| Encryption in transit | GCP (TLS) | Enforce HTTPS |
| Encryption at rest | GCP (default) | Enable, manage KMS keys |

**GCP Compliance:**
- GCP maintains PCI DSS compliance for infrastructure
- We inherit GCP's physical and infrastructure controls
- Our responsibility: application-level controls and configuration

### 4.6 Monitoring & Alerting

**Network Monitoring:**

| What | Tool | Alert Condition |
|------|------|-----------------|
| Firewall denies | GCP VPC Flow Logs | Unusual deny patterns |
| WAF blocks | Cloud Armor logs | Attack patterns, high block rate |
| Traffic anomalies | GCP Cloud Monitoring | Unexpected traffic spikes |

**Logs:**
- VPC Flow Logs enabled for Kraken VPC
- Cloud Armor logs all requests (allowed and blocked)
- Logs feed into GCP Log Explorer (see Logging & Monitoring control)

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

---

## 6. Evidence Produced

| Evidence Type | Description | System/Tool | Retention | Collection | Owner |
|--------------|-------------|-------------|-----------|------------|-------|
| VPC Flow Logs | Network traffic records | GCP VPC Flow Logs | [ASSUMPTION: 30 days] | Automatic | Engineering |
| Firewall rules | Current rule configuration | GCP Firewall / IaC repo | Indefinite | Automatic | Engineering |
| Firewall change history | Rule modifications | GCP Audit Logs + GitHub | 400 days / Indefinite | Automatic | Engineering |
| WAF logs | Blocked/allowed requests | Cloud Armor | [ASSUMPTION: 30 days] | Automatic | Engineering |
| WAF rule configuration | Current WAF rules | GCP Cloud Armor | Point-in-time | Manual | Engineering |

### Evidence Retrieval

- **VPC Flow Logs:** GCP Console > VPC Network > Flow Logs
- **Firewall rules:** GCP Console > VPC Network > Firewall, or GitHub IaC repo
- **WAF logs:** GCP Console > Cloud Armor > Policies > Logs
- **Audit logs:** GCP Console > Logging > Filter by firewall/network changes

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
- **Frequency:** Quarterly
- **Next Review:** 2025-04-27
- **Reviewer:** CTO

**Update Triggers:**
- New services deployed
- New payment gateway integration
- Security incidents related to network
- Changes to PCI scope

**Change History:**

| Date | Change | Author |
|------|--------|--------|
| 2025-01-27 | Initial document created | [Author] |

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
