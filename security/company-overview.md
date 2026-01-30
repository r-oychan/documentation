# Company and Application Overview

**Owner:** CTO
**Version:** 1.2
**Last Reviewed:** 2026-01-30
**Review Cadence:** Annually

---

## 1. Company Background

DASH is a multi-vertical mobile commerce platform operating in Hong Kong. We design, develop, and operate consumer-facing mobile applications and merchant applications, enabling an end-to-end digital commerce ecosystem.

**Business Verticals:**

| Vertical | Description | Payment Flow |
|----------|-------------|--------------|
| **Ride-Hailing** | Users select origin/destination, authorize payment, get matched with a driver | Pre-authorized payment, settled after ride completion |
| **Event Ticketing** | Users purchase tickets and entitlements through our app | Immediate payment, e-ticket delivered |

**Transaction Volume:** Expected to exceed HK$10M annually

**Team Locations:**

| Team | Location | Role |
|------|----------|------|
| Development | Hong Kong | Core engineering, architecture |
| Quality Assurance | Guangzhou | Testing, release validation |
| Engineering (Extended) | Philippines | Development support |
| Contractors | Various | Code contributions under NDA |
| Executive Leadership | Hong Kong | CEO, CTO, Head of Product |

---

## 2. Application Architecture

### 2.1 DASH Mobile Application (Consumer)

The DASH mobile application allows users to:
- Register and manage user accounts
- Browse available services (taxi rides, event tickets)
- Add payment cards (via Kraken module)
- Purchase services and tickets in-app
- Present digital tickets for admission

### 2.2 DASH Merchant Application

Merchants (event organizers, venues) use the DASH Merchant App to:
- Scan QR codes on e-tickets for admission validation
- View transaction summaries (tokenized data only)

### 2.3 Admin Portal

Internal CS and operations staff use the Admin Portal to:
- View transaction records (tokenized references only)
- Perform transaction reconciliation
- Process refunds and voids
- Distribute funds to merchants and drivers (day-end settlement)

**Key Security Principle:** No PAN or sensitive authentication data is accessible through any consumer-facing or administrative application except Kraken.

---

## 3. Payment Architecture

### System Architecture Diagram

![DASH Kraken System Architecture](digital/images/dash-kraken-architecture-v4-direct-pan.jpg)

*Figure: DASH system architecture showing Kraken as the isolated Cardholder Data Environment (CDE). PAN data flows directly from user apps to Kraken, bypassing DASH Main VPC entirely.*

### 3.1 Payment Module (Kraken)

Kraken is DASH's dedicated payment processing module and the **only component** that handles cardholder data.

#### 3.1.1 Cardholder Data Stored in Kraken

| Data Element | Stored | Encrypted | Notes |
|--------------|--------|-----------|-------|
| **Cardholder Name** | Yes | Yes (GCP KMS) | Required for payment gateway submission |
| **Primary Account Number (PAN)** | Yes | Yes (GCP KMS) | Card number, encrypted at field level |
| **Expiration Date** | Yes | Yes (GCP KMS) | Required for recurring/future payments |
| **CVV/CVC** | **No** | N/A | Never stored; used only during initial 3DS authentication, then discarded |
| **PIN / PIN Block** | **No** | N/A | Not applicable; DASH processes online/mobile payments only (no PIN entry) |
| **Full Track Data** | **No** | N/A | Not applicable; no magnetic stripe or chip data captured |

**Encryption Details:**
- All stored cardholder data is encrypted using **GCP KMS** with AES-256-GCM
- Encryption occurs at field level (not just disk encryption)
- Key material resides in FIPS 140-2 Level 3 validated HSMs
- Keys rotate automatically every 90 days
- **No human access to keys:** Key material never leaves the HSM and cannot be exported by anyone (including DASH staff, CTO, or Google employees)

**Sensitive Authentication Data (SAD) Policy:**
- CVV is collected during card registration for 3DS verification only
- CVV is transmitted directly to payment gateway during 3DS flow
- CVV is **never stored** in Kraken database, logs, or any persistent storage
- After 3DS authentication completes, CVV is discarded from memory

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              DASH Ecosystem                                  │
│                                                                              │
│  ┌─────────────────┐     ┌─────────────────┐     ┌─────────────────────┐   │
│  │  DASH Mobile    │     │  DASH Merchant  │     │    Admin Portal     │   │
│  │  App (Consumer) │     │  App            │     │    (CS Staff)       │   │
│  │                 │     │                 │     │                     │   │
│  │  • Book rides   │     │  • Scan QR      │     │  • View txns        │   │
│  │  • Buy tickets  │     │  • Validate     │     │  • Process refunds  │   │
│  │  • Add cards    │     │    admission    │     │  • Settlement       │   │
│  └────────┬────────┘     └─────────────────┘     └──────────┬──────────┘   │
│           │                                                  │              │
│           │  Card details                          Tokens    │              │
│           │  (one-time)                            only      │              │
│           ▼                                                  │              │
│  ┌────────────────────────────────────────────────────────────────────┐    │
│  │                         DASH Core                                   │    │
│  │                                                                     │    │
│  │   • User management        • Ride matching                         │    │
│  │   • Order processing       • Ticket fulfillment                    │    │
│  │   • Settlement logic       • Merchant management                   │    │
│  │                                                                     │    │
│  │   ⚠️  NO PAN STORAGE - TOKENS ONLY                                 │    │
│  └─────────────────────────────┬───────────────────────────────────────┘    │
│                                │                                            │
│                                │ Tokenization API                           │
│                                ▼                                            │
│  ╔════════════════════════════════════════════════════════════════════╗    │
│  ║                     KRAKEN (PCI SCOPE)                             ║    │
│  ║                                                                     ║    │
│  ║   • Card registration & tokenization                               ║    │
│  ║   • 3D Secure (3DS) authentication                                 ║    │
│  ║   • PAN encryption (GCP KMS)                                       ║    │
│  ║   • Payment gateway integration (Soepay, GP)                       ║    │
│  ║                                                                     ║    │
│  ║   ✓ Only component that handles PAN                                ║    │
│  ║   ✓ Isolated in separate GCP VPC                                   ║    │
│  ╚══════════════════════════════════╦═════════════════════════════════╝    │
│                                      ║                                      │
└──────────────────────────────────────║──────────────────────────────────────┘
                                       ║
                                       ▼
                          ┌─────────────────────────┐
                          │   Payment Gateways      │
                          │   • Soepay (Offline)    │
                          │   • GP (MOTO)           │
                          └─────────────────────────┘
```

### 3.2 Card Registration Flow

1. User initiates "Add Card" in DASH Mobile App
2. Card details entered directly into Kraken-hosted secure form (iframe/SDK):
   - Cardholder name
   - Card number (PAN)
   - Expiration date
   - CVV (for 3DS verification only)
3. Kraken performs 3DS authentication:
   - CVV sent to payment gateway for verification
   - CVV **discarded immediately** after 3DS completes (never stored)
4. Kraken encrypts cardholder data using GCP KMS:
   - Cardholder name → encrypted → stored
   - PAN → encrypted → stored
   - Expiration date → encrypted → stored
5. Kraken generates a non-reversible token
6. Kraken returns token to DASH Core
7. DASH Core stores only the token (no cardholder data)
8. Subsequent payments use token only

**Key Principles:**
- Card details never touch DASH Core or any system outside Kraken
- CVV is never stored anywhere, not even temporarily in database
- Only Kraken can decrypt stored cardholder data (via service account)

### 3.3 Payment Processing Flow

**Ride-Hailing (Pre-Authorization):**
1. User requests ride → DASH Core creates order
2. DASH Core sends token to Kraken for pre-authorization
3. Kraken decrypts PAN and sends to gateway
4. Ride completes → DASH Core requests capture
5. Day-end: Funds distributed to driver

**Event Ticketing (Immediate Capture):**
1. User selects tickets → DASH Core creates order
2. DASH Core sends token to Kraken for payment
3. Kraken processes payment via gateway
4. DASH Core generates e-ticket and sends to user
5. User presents QR code at venue → Merchant App validates

---

## 4. PCI DSS Scope Definition

### 4.1 In-Scope Systems

| System | Reason |
|--------|--------|
| **Kraken** | Stores, processes, and transmits PAN |
| **Kraken Database** | Encrypted PAN storage |
| **Kraken VPC** | Network containing Kraken components |
| **GCP KMS** | Encryption keys for PAN |

### 4.2 Out-of-Scope Systems

| System | Reason |
|--------|--------|
| **DASH Mobile App** | Card entry via Kraken iframe; no PAN stored |
| **DASH Core** | Receives tokens only; no PAN |
| **Admin Portal** | Displays masked PAN and tokens only; no full PAN |
| **DASH Merchant App** | No payment data; QR validation only |
| **Office Network** | No direct CDE access |

### 4.3 Out-of-Scope Payment Channels

| Channel | Reason |
|---------|--------|
| **PayWave / Contactless** | Payment terminal handles PAN; DASH never sees card data |
| **External Payment Terminals** | PCI-compliant third-party handles all card processing |

**Justification:** For contactless payments (e.g., payWave), the payment terminal processes the card transaction and sends DASH only a transaction reference. DASH does not receive, process, or store any cardholder data for these transactions.

---

## 5. Tokenization Strategy

### 5.1 How Tokenization Reduces Scope

```
┌─────────────────────────────────────────────────────────────────┐
│                      WITHOUT TOKENIZATION                        │
│                                                                  │
│   User → App → Core → Database → Gateway                        │
│          PAN    PAN    PAN        PAN                           │
│                                                                  │
│   Result: Entire chain is in PCI scope                          │
└─────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│                      WITH TOKENIZATION (DASH)                    │
│                                                                  │
│   User → App → Kraken → Core → Admin Portal                     │
│          CHD    CHD    Token   Token                            │
│                 ↓                                                │
│        (encrypted via KMS)                                       │
│                                                                  │
│   Result: Only Kraken is in PCI scope                           │
└─────────────────────────────────────────────────────────────────┘

CHD = Cardholder Data (name, PAN, expiration date)
Note: CVV collected but never stored
```

### 5.2 Token Properties

| Property | Value |
|----------|-------|
| Format | Non-reversible reference (cannot derive PAN or any CHD) |
| Storage | DASH Core and Admin Portal |
| Usage | Transaction reference, refunds, settlements |
| Contains | No cardholder data; reference only |
| CHD Access | Only Kraken can convert token → cardholder data for gateway submission |

### 5.3 What Tokens Replace

| Original Data | Token Replaces | Retrievable via Token |
|---------------|----------------|----------------------|
| Cardholder Name | Yes | Only by Kraken (for gateway) |
| PAN | Yes | Only by Kraken (for gateway) |
| Expiration Date | Yes | Only by Kraken (for gateway) |
| CVV | No (never stored) | N/A |

When DASH Core needs to process a payment, it sends the token to Kraken. Kraken retrieves and decrypts the associated cardholder data, then submits to the payment gateway.

---

## 6. Security Governance

### 6.1 Independent Assessment

| Assessment | Frequency | Provider |
|------------|-----------|----------|
| PCI DSS Assessment | Annual | Independent QSA |
| Penetration Testing | Quarterly | Qualified third-party |
| Vulnerability Scanning | Quarterly | ASV (external), Internal tools |

### 6.2 Access Control Summary

| Role | System Access | PAN Access |
|------|---------------|------------|
| Engineering (HK) | GCP (read-only logs), GitHub | No |
| QA (Guangzhou) | GitHub (read), QA environments | No |
| Engineering (Philippines) | GitHub, limited GCP | No |
| Contractors | GitHub (specific repos only) | No |
| CS Staff | Admin Portal | No (masked display only) |
| CTO/CEO | Full GCP (break-glass) | No (Kraken service account only) |

### 6.3 Personnel Security

- All employees complete security awareness training annually
- Contractors sign NDAs and complete security training
- Background checks performed for personnel with CDE access
- Access reviewed quarterly

---

## 7. Settlement and Fund Distribution

### 7.1 Driver Settlement (Ride-Hailing)

1. Rides completed throughout the day
2. Day-end batch process calculates driver earnings
3. Funds transferred to driver accounts (bank transfer)
4. No PAN involved in settlement (all tokenized)

### 7.2 Merchant Settlement (Event Ticketing)

1. Ticket sales processed via Kraken
2. Settlement batch runs per merchant agreement
3. Funds transferred to merchant accounts
4. Reconciliation available in Admin Portal (tokenized data)

---

## 8. Infrastructure Overview

### 8.1 Cloud Platform

| Component | Provider | Notes |
|-----------|----------|-------|
| **Compute** | GCP Cloud Run, Cloud Functions | Serverless, fully managed |
| **Database** | GCP Cloud SQL | Managed PostgreSQL, private IP only |
| **Database Connectivity** | Cloud SQL Proxy | Automatic TLS, IAM authentication, no exposed ports |
| **Key Management** | GCP KMS | FIPS 140-2 Level 3 HSM |
| **Logging** | GCP Cloud Logging | Immutable audit logs |
| **WAF** | GCP Cloud Armor | DDoS protection, OWASP rules |
| **Secrets** | GCP Secret Manager | API keys, credentials |

### 8.2 Secure Connectivity

| Connection Type | Method | Encryption |
|----------------|--------|------------|
| **App → Database** | Cloud SQL Proxy | Automatic TLS (no public IP) |
| **Service → Service** | HTTPS | TLS 1.2+ |
| **App → Payment Gateway** | HTTPS | TLS 1.2+ |
| **App → GCP KMS** | HTTPS API | TLS 1.2+ |
| **Internet → App** | HTTPS via Load Balancer | TLS 1.2+ (managed certificates) |

**Key Security Properties:**
- Cloud SQL instances have **no public IP** (unreachable from internet)
- Database connections use **IAM authentication** (no database passwords in code)
- All HTTP traffic encrypted with **TLS 1.2 minimum**
- GCP manages TLS certificates with automatic renewal

### 8.3 GCP PCI Compliance

GCP maintains **PCI DSS 4.0.1 Level 1 Service Provider** compliance. We inherit significant security controls from GCP for our serverless workloads (Cloud Run, Cloud Functions).

See [Third-Party Risk Management](digital/third-party-risk.md) for inherited controls documentation.

---

## 9. Document References

| Document | Purpose |
|----------|---------|
| [Network Security](digital/network-security.md) | VPC segmentation, firewall rules, NSC standards |
| [Access Control](digital/access-control.md) | IAM policies, access provisioning |
| [Secure Development](digital/secure-development.md) | SDLC, code scanning, tokenization architecture |
| [Cryptographic Key Management](digital/cryptographic-key-management.md) | GCP KMS, key rotation |
| [Admin Portal Access](digital/admin-portal-access.md) | CS access, transaction visibility |
| [Third-Party Risk](digital/third-party-risk.md) | GCP, payment gateways, vendors |
| [Logging & Monitoring](digital/logging-monitoring.md) | Audit logs, alerting |
| [Incident Response](digital/incident-response.md) | Security incident handling |
| [Security Policy & Awareness](digital/security-policy-awareness.md) | Training, policy acknowledgment |

---

## 10. Change History

| Date | Change | Author |
|------|--------|--------|
| 2026-01-30 | Initial document created | [Author] |
| 2026-01-30 | v1.1: Added detailed cardholder data storage table (Section 3.1.1), clarified CVV never stored, expanded tokenization details | [Author] |
| 2026-01-30 | v1.2: Added Section 8.2 Secure Connectivity - Cloud SQL Proxy, HTTPS/TLS requirements | [Author] |
