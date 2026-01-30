# Card Tokenization & Payment Data Flow

**Document Version:** 1.1
**Last Updated:** 2026-01-30
**Owner:** Engineering / Security
**Classification:** Internal

---

## 1. Purpose

This document describes how DASH handles cardholder data through the Kraken tokenization module.

**What Kraken Does:**
- Securely collects card data from mobile app
- Verifies card ownership via 3DS authentication
- Tokenizes sensitive information
- Processes future transactions using tokens only

**Risk Reduced:** Unauthorized access to cardholder data, data breaches, PCI scope expansion

**Who Relies On It:** Engineering, Security, Compliance, QSA auditors

---

## 2. System Components

| Component | Role | Handles PAN |
|-----------|------|-------------|
| **Mobile App** | Collects card details from user | Transient only (in-memory, passed directly to Kraken) |
| **Kraken** | Card verification, tokenization, gateway communication | **Yes - This is the CDE** |
| **Payment Gateway / Acquirer** | Authorization, 3DS, settlement | Yes (external provider) |
| **Core Module** | Business logic, uses tokens only | **No - Never receives PAN** |

---

## 3. Data Flow

### Overview Diagram

![DASH Kraken Card Tokenization & Payment Flow](images/dash-card-tokenization-flow-v2.jpg)

---

### Phase 1: Card Data Capture (PCI Scope Entry Point)

This is where cardholder data enters the system.

| Step | Action | Data | From → To |
|------|--------|------|-----------|
| **①** | User enters card details | PAN, Cardholder Name, Expiry Date | User → Mobile App |
| **②** | Card data sent directly to Kraken | PAN, Name, Expiry (TLS 1.2+) | Mobile App → Kraken |

**Key Controls:**
- Data encrypted in transit using TLS 1.2 or higher
- Core Module **never** receives or processes raw card data
- PAN bypasses all systems except Kraken

**Result:** Only Kraken handles cardholder data, defining the PCI boundary.

---

### Phase 2: Card Ownership Verification (3DS Test Transaction)

Before tokenization, we verify the user owns the card.

| Step | Action | From → To |
|------|--------|-----------|
| **③** | Kraken initiates $1 authorization + 3DS request | Kraken → Payment Gateway |
| **④** | 3D Secure authentication performed | Payment Gateway ↔ Card Issuer |
| **⑤** | Response received | Payment Gateway → Kraken |

**Decision Point:**
| Outcome | Action |
|---------|--------|
| Auth + 3DS **succeed** | Transaction voided immediately → Proceed to tokenization |
| Auth or 3DS **fails** | Card rejected → No tokenization occurs |

**Result:** Card ownership is verified before token creation.

---

### Phase 3: Tokenization

After successful verification, the card is tokenized.

| Step | Action | Detail |
|------|--------|--------|
| **⑥** | PAN encrypted inside Kraken | AES-256 via Cloud KMS |
| **⑦** | Card token generated | Non-reversible, non-guessable, unique |
| **⑧** | PAN cleared from memory | Immediate zeroization |

**Token Characteristics:**
- Non-reversible outside Kraken
- Non-guessable (cryptographically random)
- Unique per card (optionally per user or merchant)

**PAN Handling:**
- Never stored in plaintext
- Encrypted at rest with Cloud KMS
- Removed from memory immediately after token generation

**Result:** Sensitive card data replaced with secure token.

---

### Phase 4: Token Distribution

The token is returned for use by business systems.

| Step | Action | Data | From → To |
|------|--------|------|-----------|
| **⑨** | Token + metadata returned | Token, card brand, last 4 digits, expiry | Kraken → Mobile App |
| **⑩** | Token stored | Token only (NO PAN) | Mobile App → Core Module |

**What Core Module Receives:**
- Card token (for future transactions)
- Card brand (Visa, Mastercard, etc.)
- Last 4 digits (for display)
- Expiry date

**What Core Module Does NOT Receive:**
- Full PAN
- Cardholder name
- CVV (never stored anywhere)

**Result:** Business systems operate without exposure to cardholder data.

---

### Phase 5: Transaction Processing Using Token

Future payments use the token, never raw card data.

| Step | Action | Data | From → To |
|------|--------|------|-----------|
| **⑪** | Payment request initiated | Token + Amount + Context | Core Module → Kraken |
| **⑫** | Kraken decrypts token | PAN retrieved in secure memory | Internal to Kraken |
| **⑬** | Transaction sent to gateway | PAN (encrypted TLS) | Kraken → Payment Gateway |
| **⑭** | Gateway processes with issuer | Authorization request | Payment Gateway ↔ Card Issuer |
| **⑮** | Response returned | Auth result | Card Issuer → Core Module |

**Result:** PAN is used only within Kraken's secure environment.

---

## 4. Operational Guarantees

These statements are always true when this control is followed:

| Guarantee | Verification Method |
|-----------|---------------------|
| PAN is never transmitted to Core Module | Code review, network logs |
| PAN is never stored in plaintext | Encryption audit, KMS logs |
| All card registrations complete 3DS verification | Transaction logs |
| Tokens cannot be reversed outside Kraken | Architecture design review |
| PAN is cleared from memory after tokenization | Code review, security testing |
| All PAN transmissions use TLS 1.2+ | Certificate audit, network config |
| CVV is never stored | Code review, database audit |

---

## 5. Security Controls Summary

### Data Protection

| Control | Implementation |
|---------|----------------|
| Encryption in transit | TLS 1.2+ for all connections |
| Encryption at rest | AES-256 via Cloud KMS |
| Key management | HSM-backed Cloud KMS (FIPS 140-2 Level 3) |
| Memory handling | Immediate zeroization after PAN usage |

### Access Control

| Control | Implementation |
|---------|----------------|
| RBAC | Strict role-based access to Kraken systems |
| No manual PAN access | No operational or admin access to raw PAN |
| Network segmentation | Kraken isolated in dedicated VPC |

### Tokenization Controls

| Control | Implementation |
|---------|----------------|
| Token irreversibility | Tokens cannot be reversed outside Kraken |
| Token vault isolation | Separated from business logic systems |
| Token validity | Tokens only usable within Kraken |

### Authentication & Fraud Prevention

| Control | Implementation |
|---------|----------------|
| 3DS mandatory | Required during every card verification |
| $1 auth test | Validates card is active and authorized |
| Issuer authentication | Cardholder verified by issuing bank |

### Logging & Monitoring

| Control | Implementation |
|---------|----------------|
| No PAN in logs | Logs contain tokens and transaction references only |
| Audit trail | All transactions logged with timestamps |
| Monitoring | Continuous security monitoring and alerting |

---

## 6. Evidence Produced

| Evidence Type | System/Tool | Retention | Collection | Owner |
|---------------|-------------|-----------|------------|-------|
| 3DS authentication logs | Kraken | 1 year | Automatic | Engineering |
| Token generation logs | Kraken | 1 year | Automatic | Engineering |
| $1 auth/void records | Payment Gateway | 1 year | Automatic | Engineering |
| KMS key usage logs | Cloud KMS | 1 year | Automatic | Security |
| TLS certificate records | GCP | 1 year | Automatic | Infrastructure |
| Network flow logs | VPC Flow Logs | 1 year | Automatic | Infrastructure |

---

## 7. Scope Reduction Summary

| Component | PCI Scope Status | Justification |
|-----------|------------------|---------------|
| **Kraken** | Full PCI DSS scope | Handles, stores, transmits PAN |
| **Core Module** | Out of scope | Tokens only, never handles PAN |
| **DASH Main Backend** | Out of scope | No cardholder data |
| **Mobile App** | Limited scope | Transient PAN in memory, passed directly to Kraken |
| **Admin Portal** | Out of scope | No access to cardholder data |

---

## 8. Exceptions & Edge Cases

| Scenario | Handling |
|----------|----------|
| 3DS not supported by card | Card rejected - cannot be tokenized |
| $1 auth fails | Card rejected - ownership not verified |
| Token lookup fails | Transaction declined, logged for investigation |
| KMS unavailable | Transactions queued, incident triggered |

---

## 9. Related Controls

- Network Security (VPC segmentation isolating Kraken)
- Cryptographic Key Management (Cloud KMS operations)
- Access Control & Identity Management (Kraken access)
- Logging & Monitoring (transaction and security logs)
- Secure Development (code handling PAN)
- Incident Response (breach procedures)

---

## 10. Review & Maintenance

| Item | Value |
|------|-------|
| Review frequency | Quarterly |
| Last reviewed | 2026-01-30 |
| Next review | 2026-04-30 |
| Update triggers | Architecture changes, PCI requirement updates, gateway changes, security incidents |

---

## Appendix: Flow Summary

```
CARD REGISTRATION:
User → Mobile App → [DIRECT to Kraken] → 3DS+$1 Auth → Tokenize → Token returned
                         ↓
                    Core Module stores TOKEN ONLY

PAYMENT PROCESSING:
Core Module → Token+Amount → Kraken → Decrypt → Gateway → Issuer → Response
```

**The Core Module NEVER sees PAN. Kraken is the single point of cardholder data handling.**
