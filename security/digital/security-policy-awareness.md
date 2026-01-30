# Security Policy & Awareness

**Owner:** CTO
**Version:** 1.1
**Last Reviewed:** 2026-01-30
**Review Cadence:** Annually

---

## 1. Purpose

We maintain security policies and ensure all personnel understand their security responsibilities through policy acknowledgment and regular training. This control ensures security knowledge is current, policies are followed in daily practice, and staff can recognize and respond to security threats.

**Risk Reduced:**
- Security incidents due to lack of awareness
- Policy violations from ignorance
- Phishing and social engineering attacks
- Inconsistent security practices across teams

**Stakeholders:**
- All employees (policy readers, training participants)
- Engineering Team (technical security practices)
- CS Team (customer data handling)
- CTO (policy owner, training oversight)

---

## 2. Scope

### In Scope
- **Personnel:** All employees (Engineering, QA, Product, CS)
- **Policies:** All security control documents in the internal portal
- **Training:** Annual security awareness training
- **Practices:** Daily application of security policies

### Out of Scope
- Contractor security training (covered in Third-Party Risk Management)
- Technical security certifications (individual career development)
- Customer security training

---

## 3. Roles & Responsibilities

| Role | Team/Individual | Responsibility |
|------|-----------------|----------------|
| Policy Owner | CTO | Maintains policies, approves updates, oversees training program |
| Policy Publisher | CTO | Publishes policies to internal portal, tracks acknowledgments |
| Training Coordinator | CTO | Schedules training, tracks completion, follows up on non-compliance |
| Employee | All Staff | Reads policies, completes training, applies practices daily |
| Team Lead | Engineering Lead, CS Lead | Ensures team members complete requirements, reinforces practices |

---

## 4. How We Operate This Control

### 4.1 Policy Documentation & Availability

**Where Policies Live:**
- All security policies are stored in the **Internal Portal**
- Policies are organized by control area (Access Control, Network Security, etc.)
- Current version and last review date are visible on each policy

**Policy Index:**

| Policy Document | Control Area | Review Frequency |
|-----------------|--------------|------------------|
| Access Control & Identity Management | Access management | Quarterly |
| Admin Portal Access Control | Application access | Quarterly |
| Business Continuity & Disaster Recovery | BC/DR | Quarterly |
| Cryptographic Key Management | Encryption | Quarterly |
| Deployment & Release Management | Change control | Quarterly |
| Incident Response | Incident handling | Quarterly |
| Logging & Monitoring | Audit logging | Quarterly |
| Network Security | Network controls | Quarterly |
| Secure Development & Data Protection | Secure SDLC | Quarterly |
| Third-Party Risk Management | Vendor management | Quarterly |
| Vulnerability Management | Vulnerability handling | Quarterly |
| Physical Security | Physical controls | Quarterly |
| Security Policy & Awareness | This document | Annually |

### 4.2 Policy Acknowledgment (Annual)

**Trigger:** New hire onboarding OR annual renewal (January each year)

**Process:**

1. **New Hire:**
   - Within first week of employment
   - CTO or Team Lead provides access to Internal Portal
   - New hire reads all security policies
   - New hire signs acknowledgment form
   - Acknowledgment recorded in [ASSUMPTION: Notion or HR system]

2. **Annual Renewal (All Staff):**
   - Every January, all employees re-acknowledge policies
   - CTO sends reminder with link to policies and acknowledgment form
   - Employees have 2 weeks to complete
   - CTO follows up on incomplete acknowledgments
   - Completion tracked in [ASSUMPTION: Notion or HR system]

**Acknowledgment Statement:**
> "I have read and understood all DASH security policies. I understand my responsibilities for protecting company and customer data. I agree to follow these policies in my daily work."

**Acknowledgment Record Contains:**
- Employee name
- Date of acknowledgment
- List of policies acknowledged
- Employee signature (digital or physical)

### 4.3 Security Awareness Training (Annual)

**Frequency:** Once per year (minimum), plus ad-hoc for new threats

**Training Schedule:**
- **Annual Training:** Conducted in Q1 each year
- **New Hire Training:** Within first 30 days of employment

**Training Content:**

| Topic | Description | Applicable To | Duration |
|-------|-------------|---------------|----------|
| **Security Policy Overview** | Summary of all security policies and expectations | All staff | 30 min |
| **Phishing & Social Engineering** | Recognizing attacks, reporting via Outlook, real examples | All staff | 45 min |
| **Account Security & MFA** | MFA requirements for email (Outlook) and GCP, password hygiene | All staff | 30 min |
| **Data Handling & Privacy** | PII/PAN protection, what NOT to log, data classification | All staff | 30 min |
| **Device Security** | Endpoint protection, encryption, screen lock, lost device procedures | All staff | 30 min |
| **Incident Reporting** | How to report security incidents, escalation paths | All staff | 15 min |
| **Physical Security** | Office access, visitor handling, clean desk policy | All staff | 15 min |
| **Defensive Coding Practices** | Input validation, output encoding, injection prevention | Engineering | 60 min |
| **Security-First Development** | Threat modeling, secure design principles, OWASP Top 10 | Engineering | 60 min |
| **Logging Standards** | What to log, what NOT to log (no PII/payment data), structured logging | Engineering | 30 min |
| **Access Management** | Least privilege, access requests, service account handling | Engineering | 30 min |

#### 4.3.1 Training Module Details

**Module 1: Phishing & Social Engineering (All Staff)**

Training covers:
- Common phishing indicators (suspicious sender, urgency, strange links)
- Real-world phishing examples relevant to our industry
- How to verify legitimate requests
- **Reporting phishing via Outlook:** Right-click suspicious email → Report → Report phishing
- What happens after you report (Security team reviews)
- Social engineering tactics (pretexting, baiting, tailgating)

**Key Takeaway:** When in doubt, don't click. Report via Outlook first, ask questions later.

**Module 2: Account Security & MFA (All Staff)**

| System | MFA Requirement | Method |
|--------|-----------------|--------|
| **Email (Outlook/Microsoft 365)** | **Required** | Microsoft Authenticator app or hardware key |
| **GCP Console** | **Required** | Google Authenticator or hardware key |
| **GitHub** | **Required** | Authenticator app or hardware key |
| **Admin Portal** | **Required** | Passkey (device-bound) |
| **Notion** | Recommended | SSO via Microsoft 365 |

**MFA Rules:**
- MFA must be enabled within first day of account creation
- Hardware security keys (YubiKey) preferred for high-privilege accounts
- SMS-based MFA is NOT acceptable (SIM swap risk)
- If MFA device is lost: report immediately to CTO, account locked until verified

**Module 3: Data Handling & Privacy (All Staff)**

**Data You Must Protect:**

| Data Type | Examples | Handling Rules |
|-----------|----------|----------------|
| **Payment Card Data (PAN)** | Card numbers, CVV, expiry | Never store outside Kraken; never in logs, emails, chat |
| **Personally Identifiable Information (PII)** | Names, addresses, phone numbers, email | Minimize collection; mask in logs; encrypt at rest |
| **Credentials** | API keys, passwords, tokens | Never in code; use Secret Manager; never share |
| **Internal Systems** | IP addresses, architecture details | Don't share externally without approval |

**What NOT to Do:**
- ❌ Screenshot customer data and share in Slack
- ❌ Copy production data to local machine for testing
- ❌ Email PAN or PII in plain text
- ❌ Store credentials in code or Git
- ❌ Discuss customer data in public places

**Module 4: Device Security (All Staff)**

| Requirement | Standard | Verification |
|-------------|----------|--------------|
| **Disk Encryption** | FileVault (Mac) or BitLocker (Windows) enabled | IT verification during onboarding |
| **Screen Lock** | Auto-lock after 5 minutes of inactivity | Device settings check |
| **Firewall** | OS firewall enabled | Device settings check |
| **OS Updates** | Apply security updates within 7 days | Automatic updates enabled |
| **Antivirus** | OS built-in (Windows Defender) or approved solution | Enabled and updated |
| **No Jailbreak/Root** | No modified devices for work | Policy acknowledgment |

**Lost or Stolen Device Procedure:**
1. Immediately notify CTO
2. Remote wipe initiated (if supported)
3. Change passwords for all work accounts
4. Report to police if theft suspected
5. Document incident

**Module 5: Defensive Coding Practices (Engineering)**

Training covers secure coding fundamentals:

| Vulnerability | Prevention Technique |
|--------------|---------------------|
| **SQL Injection** | Use parameterized queries; never concatenate user input |
| **XSS (Cross-Site Scripting)** | Output encoding; Content Security Policy; sanitize user input |
| **CSRF** | Use anti-CSRF tokens; SameSite cookies |
| **Broken Authentication** | Use established auth libraries; enforce strong passwords; implement MFA |
| **Sensitive Data Exposure** | Encrypt at rest and in transit; minimize data collection |
| **XML External Entities** | Disable XXE in XML parsers |
| **Broken Access Control** | Server-side authorization checks; deny by default |
| **Security Misconfiguration** | Use secure defaults; remove unnecessary features |
| **Insecure Deserialization** | Validate and sanitize serialized data; use safe formats (JSON) |
| **Using Components with Known Vulnerabilities** | Keep dependencies updated; use npm audit |

**Module 6: Security-First Development (Engineering)**

**Security-First Principles:**
1. **Assume breach:** Design as if attackers are already inside
2. **Defense in depth:** Multiple layers of security
3. **Least privilege:** Minimum access needed to function
4. **Fail secure:** Errors should deny access, not grant it
5. **Secure defaults:** Secure out of the box; require explicit opt-out

**When Writing Code, Always Ask:**
- What could go wrong if malicious input is provided?
- What data am I exposing? Should it be there?
- Who can access this endpoint? Is it authorized properly?
- Am I logging anything sensitive?
- Could this be used to escalate privileges?

**Module 7: Logging Standards (Engineering)**

**What to Log:**

| Log Type | Examples | Purpose |
|----------|----------|---------|
| Authentication events | Login success/failure, logout, MFA challenges | Security monitoring |
| Authorization failures | Access denied, permission errors | Intrusion detection |
| Data access | Who accessed what record (by ID, not content) | Audit trail |
| System events | Startup, shutdown, configuration changes | Operational monitoring |
| Errors | Application errors, exceptions (sanitized) | Debugging |

**What NEVER to Log:**

| Data Type | Why | Instead Log |
|-----------|-----|-------------|
| **PAN (Card Numbers)** | PCI violation | Token reference only |
| **CVV/PIN** | PCI violation | Nothing |
| **Passwords** | Security risk | "Authentication attempt" |
| **PII (Name, Email, Phone)** | Privacy risk | User ID or hashed identifier |
| **API Keys/Secrets** | Security risk | "API call to [service]" |
| **Session Tokens** | Security risk | Session ID (if needed) |
| **Full Request Bodies** | May contain sensitive data | Sanitized summary |

**Logging Format:**
```
{
  "timestamp": "ISO8601",
  "level": "INFO|WARN|ERROR",
  "service": "kraken|dash-core|admin-portal",
  "event": "user_login|payment_processed|access_denied",
  "user_id": "uuid (not email/name)",
  "request_id": "correlation id",
  "message": "Human readable, no PII"
}
```

**Training Materials:**
- **Security Training Guide:** [security-training-guide.md](../training/security-training-guide.md)

**Training Requirements by Role:**

| Role | Required Modules | Estimated Time |
|------|------------------|----------------|
| CS, Admin, Product | Modules 1-7 | ~3 hours |
| QA | Modules 1-8 | ~3.5 hours |
| Engineering | Modules 1-10 (all) | ~5 hours |

**Training Delivery:**
- In-person session for annual training (preferred) or recorded video
- Training includes knowledge check at end of each module
- Acknowledgment form signed after completion
- Separate technical track for Engineering team (Modules 9-10)

**Training Process:**

1. CTO schedules annual training session
2. All employees notified with training date and materials
3. Employees complete general training within specified timeframe (Modules 1-7)
4. QA completes additional QA security module (Module 8)
5. Engineering completes technical modules (Modules 9-10)
6. All employees sign acknowledgment form
7. Completion recorded with date
8. CTO follows up on incomplete training within 1 week
9. Repeated non-compliance escalated to management

### 4.4 Email Phishing Protection

**Technical Controls (Microsoft 365/Outlook):**

| Control | Status | Description |
|---------|--------|-------------|
| **Safe Links** | Enabled | URLs scanned and rewritten for protection |
| **Safe Attachments** | Enabled | Attachments scanned in sandbox before delivery |
| **Anti-phishing policies** | Enabled | ML-based detection of impersonation and phishing |
| **External sender warning** | Enabled | Banner displayed for emails from outside organization |
| **Spam filtering** | Enabled | Aggressive spam and bulk mail filtering |
| **DMARC/DKIM/SPF** | Configured | Email authentication to prevent spoofing |

**How to Report Phishing (Outlook):**

```
1. DO NOT click any links or download attachments
2. Right-click the suspicious email
3. Select "Report" → "Report phishing"
4. Email is sent to Microsoft for analysis and removed from inbox
5. Optionally: Forward to CTO for internal awareness
```

**What Happens After Reporting:**
- Microsoft analyzes the email and updates threat intelligence
- If legitimate threat: Similar emails blocked organization-wide
- CTO reviews reported phishing for patterns
- If widespread campaign: Company-wide alert sent

**Phishing Indicators to Watch For:**

| Indicator | Example |
|-----------|---------|
| Urgency/pressure | "Your account will be suspended in 24 hours" |
| Generic greeting | "Dear Customer" instead of your name |
| Suspicious sender | display name doesn't match email address |
| Strange links | Hover shows different URL than displayed text |
| Requests for credentials | "Please verify your password" |
| Unexpected attachments | Invoice or document you didn't request |
| Poor grammar/spelling | Legitimate companies proofread emails |

**Simulated Phishing Exercises:**
- CTO conducts periodic simulated phishing tests
- Employees who click are provided additional training
- Results tracked to measure awareness improvement
- No punitive action for first-time clicks; focus on education

### 4.5 Account MFA Requirements

**All work accounts must have MFA enabled.**

| Account Type | MFA Method | Enrollment Deadline | Enforcement |
|--------------|------------|---------------------|-------------|
| **Microsoft 365 (Email/Outlook)** | Microsoft Authenticator app | Day 1 of employment | Account locked if not enrolled |
| **GCP Console** | Google Authenticator or hardware key | Day 1 of employment | Access denied if not enrolled |
| **GitHub** | Authenticator app or hardware key | Day 1 of employment | Organization-enforced |
| **Admin Portal** | Passkey (device-bound) | Before first access | Application-enforced |
| **Notion** | SSO via Microsoft 365 | Automatic via SSO | SSO-enforced |

**MFA Setup Process (New Hire):**

1. IT creates accounts with MFA enrollment required
2. Employee receives setup instructions
3. Employee downloads authenticator app to personal mobile device
4. Employee completes MFA enrollment within 24 hours
5. Employee confirms MFA working by logging in
6. Account fully provisioned after MFA verification

**Lost MFA Device Procedure:**

1. Employee contacts CTO immediately
2. CTO verifies employee identity (in-person or video call)
3. CTO temporarily disables MFA (max 24 hours)
4. Employee sets up MFA on new device
5. Old device removed from trusted devices
6. Incident documented

**Unacceptable MFA Methods:**
- ❌ SMS/text message (SIM swap vulnerable)
- ❌ Email-based codes (circular dependency)
- ❌ Security questions alone

### 4.6 Device Security Requirements

**All devices used for work must meet these requirements:**

| Requirement | Standard | How to Verify |
|-------------|----------|---------------|
| **Disk Encryption** | FileVault (Mac), BitLocker (Windows) | System Preferences > Security |
| **Auto Screen Lock** | 5 minutes maximum | System Preferences > Lock Screen |
| **Firewall** | Enabled | System Preferences > Firewall |
| **OS Updates** | Within 7 days of release for security updates | Software Update |
| **Antivirus** | Windows Defender or approved solution | Security Center |
| **Password/Biometric Lock** | Required for device access | Device settings |

**BYOD (Bring Your Own Device) Policy:**

- Personal devices may be used for work email and chat only
- Personal devices must meet same security requirements
- Work data must be separable (MDM enrollment required)
- Company can remote wipe work data (not personal data)
- No personal devices for GCP or production system access

**Lost/Stolen Device Response:**

| Step | Action | Timeline |
|------|--------|----------|
| 1 | Report to CTO | Immediately |
| 2 | CTO initiates remote wipe | Within 1 hour |
| 3 | Change passwords for all work accounts | Within 1 hour |
| 4 | Revoke active sessions (GCP, GitHub, etc.) | Within 1 hour |
| 5 | File police report if theft | Within 24 hours |
| 6 | Document in incident log | Within 24 hours |
| 7 | Provision replacement device | As needed |

### 4.7 Daily Practice

**Expectation:** Security policies are practiced daily, not just read annually.

**How We Reinforce Daily Practice:**

| Practice | Reinforcement Method |
|----------|---------------------|
| Access control | Access requests go through documented process |
| Code review | Security checklist in PR template |
| Incident reporting | Clear escalation path known to all |
| Data handling | No PAN/PII in logs; no printing sensitive data |
| Physical security | Badge access, visitor escort |
| MFA usage | Required for all logins; enforced by systems |
| Phishing awareness | Report via Outlook; simulated phishing tests |
| Secure coding | Code scanning blocks vulnerabilities |
| Logging hygiene | Log reviews catch sensitive data leaks |

**Team Lead Responsibilities:**
- Observe team following security practices
- Correct policy violations promptly
- Reinforce good security behavior
- Report persistent issues to CTO
- Ensure team completes training on time

### 4.8 Policy Updates

**Annual Review:**
- CTO reviews all policies at least once per year
- Updates made to reflect:
  - Changes in technology or systems
  - New threats or vulnerabilities
  - Lessons learned from incidents
  - Regulatory or compliance changes

**Update Process:**
1. CTO identifies needed updates
2. CTO drafts policy changes
3. Changes reviewed by relevant stakeholders
4. Updated policy published to Internal Portal
5. Staff notified of significant changes
6. Re-acknowledgment required if material changes

**Version Control:**
- All policies include version number and last reviewed date
- Change history maintained in each document
- Previous versions archived

---

## 5. Operational Guarantees

When this control operates correctly:

- [ ] All security policies are documented and available in the Internal Portal
- [ ] All employees acknowledge security policies annually
- [ ] All employees complete security awareness training annually
- [ ] New hires complete policy acknowledgment and training within 30 days
- [ ] Policy acknowledgments are recorded with date and employee name
- [ ] Training completion is tracked with date and assessment score
- [ ] Policies are reviewed and updated at least annually
- [ ] Security practices are applied daily, not just during audits
- [ ] All work accounts have MFA enabled (Microsoft 365, GCP, GitHub)
- [ ] Email phishing protection is enabled for all users (Safe Links, Safe Attachments)
- [ ] All work devices meet security requirements (encryption, screen lock, updates)
- [ ] Engineering team completes secure coding training annually
- [ ] No PII or payment data is logged in application logs
- [ ] Phishing reports are reviewed and actioned within 24 hours

---

## 6. Evidence Produced

| Evidence Type | Description | System/Tool | Retention | Collection | Owner |
|--------------|-------------|-------------|-----------|------------|-------|
| Policy acknowledgment records | Signed acknowledgments | Notion/HR system | Duration of employment + 1 year | Manual | CTO |
| Training completion records | Training dates, scores | Training platform/Notion | Duration of employment + 1 year | Manual | CTO |
| Policy documents | Current versions | Internal Portal | Indefinite | Manual | CTO |
| Policy change history | Version history | Internal Portal / Git | Indefinite | Automatic | CTO |
| Training materials | Slides, videos, quizzes | Training platform | Indefinite | Manual | CTO |
| MFA enrollment status | Users with MFA enabled | Microsoft 365 Admin, GCP IAM | Real-time | Automatic | CTO |
| Phishing reports | Reported suspicious emails | Microsoft 365 Security Center | 90 days | Automatic | CTO |
| Simulated phishing results | Click rates, training completion | Phishing simulation tool | 1 year | Automatic | CTO |
| Device compliance status | Encryption, update status | Device management / manual audit | Point-in-time | Manual/Automatic | CTO |
| Log audit results | Reviews for PII/PAN in logs | Manual review / automated scanning | Per review | Manual | Engineering |

### Evidence Retrieval

- **Policy acknowledgments:** Notion > Security > Acknowledgments database
- **Training records:** Training platform dashboard or Notion
- **Current policies:** Internal Portal > Security Policies
- **Policy history:** Git repository or Internal Portal version history
- **MFA status:** Microsoft 365 Admin Center > Users > MFA status; GCP Console > IAM > 2-step verification
- **Phishing reports:** Microsoft 365 Security Center > Threat management > Submitted messages
- **Device compliance:** Manual verification during onboarding; periodic spot checks

---

## 7. Exceptions & Edge Cases

### Known Exceptions

| Exception | Justification | Compensating Control | Review Date |
|-----------|--------------|---------------------|-------------|
| None currently | - | - | - |

### Exception Process

1. Employee requests exception from CTO (e.g., extended training deadline)
2. CTO evaluates reason and risk
3. If approved:
   - Exception documented with expiration date
   - Compensating measures identified if needed
4. Exceptions reviewed at next policy review

### Edge Cases

- **Employee on extended leave:** Training deadline extended; complete within 30 days of return
- **Training platform unavailable:** In-person training session conducted; documented manually
- **New policy released mid-year:** Targeted communication and acknowledgment for material changes only
- **Employee refuses to sign:** Escalate to management; continued refusal is employment issue

---

## 8. Review & Maintenance

**Review Schedule:**
- **Frequency:** Annually
- **Next Review:** 2027-01-29
- **Reviewer:** CTO

**Training Schedule:**
- **Annual Training:** Q1 each year
- **Next Training:** [ASSUMPTION: Q1 2027]

**Update Triggers:**
- New security threats (phishing campaigns, etc.)
- Security incidents revealing awareness gaps
- New systems or processes requiring training
- Regulatory or compliance requirement changes
- Employee feedback on training effectiveness

**Change History:**

| Date | Change | Author |
|------|--------|--------|
| 2026-01-29 | Initial document created | [Author] |
| 2026-01-30 | v1.1: Expanded training content (defensive coding, security-first development, logging standards); Added email phishing protection (Outlook reporting); Added MFA requirements (Microsoft 365, GCP); Added device security requirements; Added BYOD policy | [Author] |

---

## 9. Related Controls

| Control Area | Relationship |
|-------------|--------------|
| Access Control & Identity Management | Training covers access management; MFA requirements documented here |
| Incident Response | Training covers incident reporting; phishing reports feed incident process |
| Secure Development & Data Protection | Defensive coding and logging standards training; security-first development |
| Physical Security | Training covers physical security practices |
| Third-Party Risk Management | Contractor training requirements defined there |
| Logging & Monitoring | Logging standards (no PII/PAN) documented here; enforcement in monitoring |
| Vulnerability Management | Secure coding training reduces code vulnerabilities |
| All other control documents | All policies require acknowledgment per this control |
