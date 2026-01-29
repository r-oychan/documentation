# Cryptographic Key Management

**Owner:** CTO
**Version:** 1.0
**Last Reviewed:** 2026-01-29
**Review Cadence:** Quarterly

---

## 1. Purpose

We manage cryptographic keys to protect cardholder data (PAN) and other sensitive information. This control ensures keys are generated securely, stored with appropriate protection, rotated regularly, and destroyed when no longer needed.

**Risk Reduced:**
- Unauthorized decryption of cardholder data
- Key compromise leading to data breach
- Weak or predictable encryption keys
- Orphaned keys without proper lifecycle management

**Stakeholders:**
- Engineering Team (uses KMS for encryption operations)
- Customers/Merchants (data protected by encryption)
- Payment Gateways (secure data exchange)

---

## 2. Scope

### In Scope
- **Systems:** Kraken (payment gateway module)
- **Key Types:** Data encryption keys (DEK) for PAN encryption
- **Infrastructure:** Google Cloud Platform Key Management Service (GCP KMS)
- **Environments:** Production

### Out of Scope
- TLS/SSL certificates (managed by GCP Load Balancer)
- SSH keys for developer access (covered in Access Control)
- Third-party gateway encryption (Soepay, GP responsibility)
- Application secrets and API keys (managed via GCP Secret Manager)

### Key Inventory

| Key Name | Purpose | Algorithm | Location | Rotation Period |
|----------|---------|-----------|----------|-----------------|
| Kraken PAN Encryption Key | Encrypts cardholder PAN at rest | [ASSUMPTION: AES-256-GCM - verify] | GCP KMS | 90 days |

---

## 3. Roles & Responsibilities

| Role | Team/Individual | Responsibility |
|------|-----------------|----------------|
| Control Owner | CTO | Sets key management policy, approves key access, reviews key usage |
| Key Administrator | Engineering Team | Configures KMS, monitors rotation, responds to alerts |
| Key User | Kraken Application | Performs encrypt/decrypt operations via service account |
| Auditor | CTO | Reviews key access logs quarterly |

### Access to KMS

| Role | KMS Permission | Justification |
|------|----------------|---------------|
| Kraken Service Account | `cloudkms.cryptoKeyEncrypterDecrypter` | Encrypt/decrypt PAN data |
| Engineering Team | `cloudkms.viewer` | View key metadata, troubleshoot issues |
| CTO | `cloudkms.admin` | Full management for break-glass scenarios |

---

## 4. How We Operate This Control

### 4.1 Key Generation

**When:** Initial setup or when new key is required

**How:**
1. Keys are generated within GCP KMS (never outside)
2. GCP generates keys using FIPS 140-2 Level 3 validated HSMs
3. Key material never leaves Google's HSM infrastructure
4. Key is created via GCP Console or Terraform (infrastructure as code)

**Steps to create a new key:**
1. Engineering submits PR with Terraform configuration for new key
2. PR requires approval (2 engineers + CTO)
3. GitHub Actions applies Terraform
4. Key creation logged in GCP Audit Logs

**Tools:** GCP Console, Terraform, GitHub Actions

### 4.2 Key Storage

**How we store keys:**
- All keys reside in GCP KMS (cloud-hosted HSM)
- Key material is never exported or accessible outside KMS
- Kraken accesses keys via GCP KMS API using service account credentials

**Protection:**
- GCP KMS provides hardware-level protection
- Service account credentials rotated [ASSUMPTION: annually - verify rotation policy]
- Access restricted via IAM policies

### 4.3 Key Rotation (Automated)

**Schedule:** Every 90 days (automated by GCP KMS)

**How it works:**
1. GCP KMS automatically generates new key version
2. New encryptions use the new key version
3. Previous key versions remain available for decryption
4. Kraken application code requires no changes (KMS handles versioning)

**Verification:**
1. CTO reviews key rotation in GCP Console quarterly
2. GCP Audit Logs show rotation events
3. Alert configured if rotation fails [ASSUMPTION: verify alert exists]

**Manual rotation (if needed):**
1. Navigate to GCP Console > Security > Key Management
2. Select the key
3. Click "Rotate Key"
4. Confirm rotation
5. Document reason in Notion

### 4.4 Key Usage

**Encryption (when PAN enters Kraken):**
1. Kraken receives PAN from merchant
2. Kraken calls GCP KMS encrypt API
3. KMS returns ciphertext
4. Kraken stores ciphertext in database
5. Kraken generates token for DASH main application

**Decryption (when sending to gateway):**
1. Kraken retrieves ciphertext from database
2. Kraken calls GCP KMS decrypt API
3. KMS returns plaintext PAN
4. Kraken sends PAN to gateway (Soepay/GP)
5. Plaintext PAN exists only in memory, never logged

**Access pattern:**
```
DASH Main App → Token → Kraken → KMS Decrypt → Gateway
                         ↑
                    Encrypted PAN
                    (stored in DB)
```

### 4.5 Key Destruction

**When:** Key is no longer needed (system decommissioned, key compromised)

**Process:**
1. CTO approves key destruction request
2. Verify no data still encrypted with the key (or data is no longer needed)
3. Schedule key version destruction in GCP KMS (24-hour minimum delay)
4. GCP KMS destroys key material after scheduled period
5. Document destruction in Notion

**GCP KMS destruction safeguards:**
- Minimum 24-hour delay before destruction (configurable up to 120 days)
- Destruction can be cancelled during delay period
- Destroyed keys cannot be recovered

### 4.6 Key Compromise Response

**If key compromise is suspected:**
1. Immediately rotate the key (manual rotation in GCP Console)
2. Notify CTO
3. Assess scope of potential exposure
4. Consider re-encrypting affected data with new key
5. Follow Incident Response process
6. Document in incident report

---

## 5. Operational Guarantees

When this control operates correctly:

- [ ] All PAN encryption uses GCP KMS (no local encryption)
- [ ] Encryption keys rotate automatically every 90 days
- [ ] Key material never leaves GCP KMS HSM
- [ ] Only Kraken service account can encrypt/decrypt PAN
- [ ] All key operations are logged in GCP Audit Logs
- [ ] Key destruction requires CTO approval and 24-hour delay

---

## 6. Evidence Produced

| Evidence Type | Description | System/Tool | Retention | Collection | Owner |
|--------------|-------------|-------------|-----------|------------|-------|
| Key creation logs | When keys were created | GCP Cloud Audit Logs | [ASSUMPTION: 400 days - verify] | Automatic | CTO |
| Key rotation logs | Automatic and manual rotations | GCP Cloud Audit Logs | [ASSUMPTION: 400 days - verify] | Automatic | CTO |
| Key usage logs | Encrypt/decrypt operations | GCP Cloud Audit Logs | [ASSUMPTION: 400 days - verify] | Automatic | CTO |
| Key access logs | Who accessed key metadata | GCP Cloud Audit Logs | [ASSUMPTION: 400 days - verify] | Automatic | CTO |
| Key destruction records | Scheduled and completed destructions | GCP Cloud Audit Logs | [ASSUMPTION: 400 days - verify] | Automatic | CTO |
| Quarterly review records | Review of key management | Notion | Indefinite | Manual | CTO |

### Evidence Retrieval

**To view key rotation history:**
1. GCP Console > Security > Key Management
2. Select key ring and key
3. View "Key versions" tab
4. Each version shows creation date

**To view key usage logs:**
1. GCP Console > Logging > Logs Explorer
2. Filter: `resource.type="cloudkms_cryptokey"`
3. Review encrypt/decrypt operations

**To export audit logs:**
1. GCP Console > Logging > Logs Explorer
2. Apply filters for KMS operations
3. Download logs for audit review

---

## 7. Exceptions & Edge Cases

### Known Exceptions

| Exception | Justification | Compensating Control | Review Date |
|-----------|--------------|---------------------|-------------|
| Previous key versions retained | Required to decrypt historical data | Old versions disabled after data migration | Quarterly |

### Exception Process

1. Exception request submitted to CTO
2. CTO evaluates risk and business need
3. If approved, exception documented in Notion
4. Exception reviewed quarterly

### Edge Cases

- **KMS unavailable:** Kraken cannot process new transactions; existing tokens remain valid. Alert Engineering and wait for GCP resolution.
- **Key version limit reached:** GCP KMS supports unlimited versions. If performance degrades, consider destroying very old versions after verifying no data needs them.
- **Emergency key rotation:** Can be performed immediately via GCP Console by CTO or authorized engineer.

---

## 8. Review & Maintenance

**Review Schedule:**
- **Frequency:** Quarterly
- **Next Review:** 2026-04-29
- **Reviewer:** CTO

**Review Checklist:**
- [ ] Verify automatic key rotation is occurring (check last rotation date)
- [ ] Review key access permissions (IAM)
- [ ] Check for any unusual key usage patterns in logs
- [ ] Confirm no unauthorized key versions created
- [ ] Verify key destruction policy is still appropriate

**Update Triggers:**
- New encryption requirements
- Changes to Kraken architecture
- Key compromise or suspected compromise
- Changes to GCP KMS service
- Regulatory requirement changes

**Change History:**

| Date | Change | Author |
|------|--------|--------|
| 2026-01-29 | Initial document created | [Author] |

---

## 9. Related Controls

| Control Area | Relationship |
|-------------|--------------|
| Secure Development & Data Protection | Defines how PAN is encrypted/tokenized using these keys |
| Access Control & Identity Management | Controls who can access KMS and key administration |
| Logging & Monitoring | KMS audit logs flow to central logging |
| Network Security | Kraken VPC isolation protects KMS API access |
| Incident Response | Key compromise handled via incident process |
| Business Continuity & Disaster Recovery | Key backup handled by GCP (cross-region replication) |
