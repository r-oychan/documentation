# DASH Security Documentation

This folder contains security control documentation for DASH, a multi-vertical mobile commerce platform.

## Quick Start

1. **Start here:** [Company Overview](company-overview.md) - Understand DASH business, payment architecture, and PCI scope
2. **PCI Audit:** See [pci-audit/2026Q1/](../pci-audit/2026Q1/) for compliance mapping

## Document Structure

```
security/
├── company-overview.md              # Business context, architecture, PCI scope
├── digital/                         # Digital security controls
│   ├── access-control.md            # IAM, access provisioning, maker-checker
│   ├── admin-portal-access.md       # CS access, authentication, transaction visibility
│   ├── business-continuity.md       # BC/DR, backups
│   ├── cryptographic-key-management.md  # GCP KMS, PAN encryption
│   ├── deployment-control.md        # Change management, CI/CD
│   ├── incident-response.md         # Security incident handling
│   ├── logging-monitoring.md        # Audit logs, alerting, daily review
│   ├── network-security.md          # VPCs, firewalls, TLS, NSC standards
│   ├── secure-development.md        # SDLC, code scanning, tokenization
│   ├── security-policy-awareness.md # Policy acknowledgment, MFA, device security
│   ├── security-standards-governance.md  # Exception process, standards baseline
│   ├── system-component-inventory.md    # Application & dependency inventory
│   ├── third-party-risk.md          # Vendors, contractors, GCP inherited controls
│   └── vulnerability-management.md  # Scanning, pen testing, ASV
├── training/
│   └── security-training-guide.md   # 10-module security training program
└── physical/
    └── physical-security.md         # Office access, visitor management
```

## Document Version Summary

| Document | Version | Last Reviewed | Owner | Review Cadence |
|----------|---------|---------------|-------|----------------|
| **company-overview.md** | 1.0 | 2026-01-29 | CTO | Quarterly |
| **access-control.md** | 1.2 | 2026-01-30 | CTO | Quarterly |
| **admin-portal-access.md** | 1.2 | 2026-01-30 | CTO | Quarterly |
| **business-continuity.md** | 1.1 | 2026-01-29 | CTO | Quarterly |
| **cryptographic-key-management.md** | 1.1 | 2026-01-30 | CTO | Quarterly |
| **deployment-control.md** | 1.1 | 2026-01-29 | Engineering | Quarterly |
| **incident-response.md** | 1.1 | 2026-01-29 | CTO | Quarterly |
| **logging-monitoring.md** | 1.2 | 2026-01-30 | CTO | Quarterly |
| **network-security.md** | 1.4 | 2026-01-30 | CTO | Quarterly |
| **secure-development.md** | 1.1 | 2026-01-29 | CTO | Quarterly |
| **security-policy-awareness.md** | 1.1 | 2026-01-30 | CTO | Annually |
| **security-standards-governance.md** | 1.0 | 2026-01-30 | CTO | Annually |
| **security-training-guide.md** | 1.0 | 2026-01-30 | CTO | Annually |
| **system-component-inventory.md** | 1.0 | 2026-01-30 | CTO | Quarterly |
| **third-party-risk.md** | 1.1 | 2026-01-29 | CTO | Quarterly |
| **vulnerability-management.md** | 1.2 | 2026-01-30 | CTO | Quarterly |
| **physical-security.md** | 1.1 | 2026-01-29 | CTO | Quarterly |

**Recent Updates (2026-01-30):**
- **access-control.md** v1.2: Added GCP IAM maker-checker process (CTO/CEO dual authorization)
- **admin-portal-access.md** v1.2: Added password history, session timeout, credential rotation
- **logging-monitoring.md** v1.2: Added 365-day retention, Security Command Center, daily log review
- **network-security.md** v1.4: Added SSL/TLS certificate management, Cloud SQL Proxy
- **vulnerability-management.md** v1.2: Added comprehensive security testing (ASV, pen testing)
- **security-standards-governance.md** v1.0: New document for exception governance
- **system-component-inventory.md** v1.0: New document for application/dependency inventory
- **security-training-guide.md** v1.0: New 10-module training program

## Key Concepts

### PCI Scope

- **In Scope:** Kraken payment module only
- **Out of Scope:** DASH Core, Mobile App, Merchant App, Admin Portal (all use tokens)

### Payment Architecture

```
User → DASH App → Kraken (PAN) → Token → DASH Core
                     ↓
              Payment Gateway
```

Only Kraken handles PAN. All other systems work with tokens.

### GCP Inherited Controls

We deploy on GCP Cloud Run and Cloud Functions (serverless). GCP provides:
- PCI DSS 4.0.1 Level 1 Service Provider compliance
- OS/runtime hardening (no customer responsibility)
- Container Threat Detection (anti-malware)
- Immutable Cloud Audit Logs

See [Third-Party Risk Management](digital/third-party-risk.md) for details.

## For QSA/Auditors

1. [Company Overview](company-overview.md) - Start here for business context
2. [PCI DSS Mapping](../pci-audit/2026Q1/pci-dss-v401-mapping.md) - Requirement-by-requirement compliance status
3. [Gap Summary](../pci-audit/2026Q1/gap-summary.md) - Known gaps and remediation status
4. [GCP AOC](https://cloud.google.com/security/compliance/compliance-reports-manager) - GCP's PCI DSS attestation

## Document Conventions

- `[ASSUMPTION: ...]` - Unverified information that needs confirmation
- All documents reviewed quarterly (except Security Policy & Awareness - annually)
- Version and Last Reviewed date at top of each document
