# DASH Security Training Guide

**Owner:** CTO
**Version:** 1.0
**Last Updated:** 2026-01-30
**Audience:** All DASH Staff

---

## Welcome

This guide contains everything you need to know about security at DASH. Security is everyone's responsibility - from how you handle your laptop to how you respond to suspicious emails.

**Training Requirements:**

| Role | Required Modules | Estimated Time |
|------|------------------|----------------|
| All Staff (CS, Admin, Product) | Modules 1-7 | ~3 hours |
| QA Team | Modules 1-8 | ~3.5 hours |
| Engineering Team | Modules 1-10 (all modules) | ~5 hours |

**How to Use This Guide:**
1. Read each module applicable to your role
2. Complete the knowledge check at the end of each module
3. Sign the acknowledgment form after completing all required modules
4. Refer back to this guide whenever you have security questions

---

## Table of Contents

1. [Security at DASH: Why It Matters](#module-1-security-at-dash-why-it-matters)
2. [Account Security & MFA](#module-2-account-security--mfa)
3. [Phishing & Social Engineering](#module-3-phishing--social-engineering)
4. [Data Handling & Privacy](#module-4-data-handling--privacy)
5. [Device Security](#module-5-device-security)
6. [Physical Security](#module-6-physical-security)
7. [Incident Reporting](#module-7-incident-reporting)
8. [QA Security Practices](#module-8-qa-security-practices) *(QA & Engineering)*
9. [Secure Development Practices](#module-9-secure-development-practices) *(Engineering Only)*
10. [Logging & Monitoring Standards](#module-10-logging--monitoring-standards) *(Engineering Only)*

---

## Module 1: Security at DASH: Why It Matters

**Audience:** All Staff | **Time:** 15 minutes

### Why Security Matters

DASH processes payments for ride-hailing and event ticketing. We handle:
- Customer payment card data
- Personal information (names, phone numbers, addresses)
- Driver and merchant financial information
- Transaction records

**A security breach could:**
- Expose customer payment cards to fraud
- Damage customer trust and our reputation
- Result in regulatory fines and legal action
- Cause financial loss to customers, drivers, and merchants

### Your Role in Security

Every person at DASH plays a critical role:

| Team | Security Responsibility |
|------|------------------------|
| **Engineering** | Build secure systems, protect code and infrastructure |
| **QA** | Test for security vulnerabilities, validate security controls |
| **Product** | Design features with security in mind |
| **CS** | Protect customer data, recognize social engineering |
| **Admin** | Secure physical access, protect company information |

### The Three Principles

**1. Protect What Matters**
- Customer data is sacred - treat it like your own
- Payment card data (PAN) never leaves Kraken (our payment system)
- When in doubt, ask before sharing any data

**2. Verify Before Trusting**
- Verify the identity of people requesting access or information
- Don't click suspicious links - verify first
- Question unusual requests, even from "executives"

**3. Report Immediately**
- See something suspicious? Report it
- Clicked a bad link? Report it
- Lost your laptop? Report it
- No blame for honest mistakes - we want to know fast

### Key Takeaway

> Security is not just IT's job. Every email you scrutinize, every password you protect, and every suspicious activity you report makes DASH safer.

### Knowledge Check - Module 1

- [ ] I understand that DASH handles sensitive payment and personal data
- [ ] I know my team's role in maintaining security
- [ ] I will verify before trusting and report suspicious activity immediately

---

## Module 2: Account Security & MFA

**Audience:** All Staff | **Time:** 30 minutes

### Your Work Accounts

You have access to several work systems. Each requires strong authentication.

| System | Purpose | Who Has Access |
|--------|---------|----------------|
| **Microsoft 365 (Outlook)** | Email, calendar, documents | All staff |
| **Notion** | Documentation, project management | All staff |
| **Admin Portal** | Customer support, transactions | CS, Admin |
| **GitHub** | Source code | Engineering, QA |
| **GCP Console** | Cloud infrastructure | Engineering |

### Multi-Factor Authentication (MFA) is Mandatory

MFA adds a second layer of protection. Even if someone steals your password, they can't access your account without your second factor.

**MFA Requirements:**

| Account | MFA Method | Setup Deadline |
|---------|------------|----------------|
| Microsoft 365 (Outlook) | Microsoft Authenticator app | Day 1 |
| GCP Console | Google Authenticator or hardware key | Day 1 |
| GitHub | Authenticator app or hardware key | Day 1 |
| Admin Portal | Passkey (device-bound) | Before first access |

### How to Set Up MFA

**Microsoft 365 (Outlook):**
1. Download "Microsoft Authenticator" app on your phone
2. Go to [mysignins.microsoft.com](https://mysignins.microsoft.com)
3. Click "Security info" → "Add sign-in method"
4. Select "Authenticator app" and follow the prompts
5. Scan the QR code with your Authenticator app

**GCP Console:**
1. Go to [myaccount.google.com/security](https://myaccount.google.com/security)
2. Click "2-Step Verification" → "Get Started"
3. Choose "Authenticator app"
4. Scan QR code with Google Authenticator app

**GitHub:**
1. Go to GitHub → Settings → Password and authentication
2. Click "Enable" under Two-factor authentication
3. Choose "Set up using an app"
4. Scan QR code with your authenticator app

### Unacceptable MFA Methods

These methods are NOT allowed because they can be compromised:

| Method | Why It's Not Allowed |
|--------|---------------------|
| SMS text messages | SIM swap attacks can steal your number |
| Email codes | If your email is compromised, MFA is useless |
| Security questions | Answers can be guessed or found on social media |

### Password Requirements

Even with MFA, strong passwords matter:

| Requirement | Standard |
|-------------|----------|
| Minimum length | 12 characters |
| Complexity | Mix of upper, lower, numbers, symbols |
| Uniqueness | Different password for each work account |
| Password manager | Strongly recommended (1Password, Bitwarden) |
| Never share | Don't share passwords, even with IT |

**Password Tips:**
- Use a passphrase: `Coffee-Mountain-Laptop-42!`
- Never reuse passwords across accounts
- Use a password manager to generate and store passwords
- Never write passwords on sticky notes or in documents

### If You Lose Your MFA Device

1. **Contact CTO immediately** (phone call or in-person)
2. CTO will verify your identity (video call or in-person)
3. MFA temporarily disabled (maximum 24 hours)
4. Set up MFA on new device immediately
5. Old device removed from trusted devices

**Never:**
- Ask a colleague to let you use their account
- Share MFA codes with anyone
- Delay reporting a lost device

### Knowledge Check - Module 2

- [ ] I have set up MFA on all my work accounts
- [ ] I understand why SMS-based MFA is not acceptable
- [ ] I know the procedure if I lose my MFA device
- [ ] My passwords are unique and stored securely

---

## Module 3: Phishing & Social Engineering

**Audience:** All Staff | **Time:** 45 minutes

### What is Phishing?

Phishing is when attackers trick you into revealing sensitive information or clicking malicious links. They pretend to be someone you trust.

**Common Phishing Types:**

| Type | Description | Example |
|------|-------------|---------|
| **Email phishing** | Fake emails that look legitimate | "Your account will be suspended" |
| **Spear phishing** | Targeted at you specifically | Uses your name, role, or projects |
| **Whaling** | Targets executives | Fake legal or financial requests |
| **Vishing** | Phone-based phishing | Caller claims to be IT support |
| **Smishing** | SMS-based phishing | Text with suspicious link |

### Real-World Phishing Examples

**Example 1: Fake Password Reset**
```
From: security@micr0soft-support.com
Subject: Urgent: Password Expiring

Your Microsoft 365 password expires in 24 hours.
Click here to reset: [malicious-link.com/reset]
```

**Red Flags:**
- Sender domain is misspelled (micr0soft with a zero)
- Creates urgency
- Generic greeting (no name)
- Link doesn't go to microsoft.com

**Example 2: CEO Fraud**
```
From: david.ceo@dashapp-hk.com
Subject: Urgent wire transfer needed

I'm in a meeting and need you to process an urgent
payment. Can you wire $50,000 to this account?
I'll explain later. Keep this confidential.
```

**Red Flags:**
- Wrong domain (dashapp-hk.com instead of real domain)
- Unusual request from executive
- Urgency and secrecy
- Request to bypass normal process

**Example 3: Fake Invoice**
```
From: billing@vendor-payments.net
Subject: Invoice #INV-2026-4521 Overdue

Please find attached the overdue invoice.
Payment is required immediately to avoid service interruption.

[Download Invoice.pdf.exe]
```

**Red Flags:**
- Unknown sender/vendor
- Attachment with double extension (.pdf.exe)
- Urgency and threats
- You didn't request this

### How to Identify Phishing

**Check the Sender:**
- Hover over the sender's name to see the actual email address
- Look for misspellings: `@micr0soft.com` vs `@microsoft.com`
- Be suspicious of external senders claiming to be internal

**Check the Links:**
- Hover over links (don't click!) to see the actual URL
- Look for misspellings: `paypa1.com` vs `paypal.com`
- Legitimate companies use their real domains

**Check the Message:**

| Red Flag | Example |
|----------|---------|
| Urgency | "Act within 24 hours or lose access" |
| Threats | "Your account will be suspended" |
| Generic greeting | "Dear Customer" instead of your name |
| Poor grammar | Spelling errors, awkward phrasing |
| Requests for credentials | "Verify your password" |
| Unexpected attachments | Invoice you didn't request |

### How to Report Phishing in Outlook

**Step-by-Step:**

```
1. DO NOT click any links or download attachments
2. DO NOT reply to the email
3. Right-click on the suspicious email
4. Select "Report" from the menu
5. Click "Report phishing"
6. The email is sent to Microsoft for analysis and removed from your inbox
```

**Optional:** Forward the email to CTO for internal awareness if it's a targeted attack.

### What Happens After You Report

1. Microsoft analyzes the email
2. If confirmed malicious: Similar emails blocked for everyone
3. CTO reviews reported phishing for patterns
4. If widespread campaign: Company-wide alert sent

### Social Engineering Beyond Email

**Phone Calls (Vishing):**
- Never give passwords or MFA codes over the phone
- IT will NEVER ask for your password
- If unsure, hang up and call back using a known number

**In-Person:**
- Verify ID of anyone asking for access
- Don't hold doors for tailgaters
- Escort all visitors

**USB Drives:**
- Never plug in unknown USB drives
- Report found USB drives to IT
- Attackers drop infected drives intentionally

### Simulated Phishing Exercises

We conduct periodic simulated phishing tests:
- Not designed to "catch" you - designed to train you
- If you click: Additional training provided
- No punitive action for first-time clicks
- Results help us improve training

### Knowledge Check - Module 3

- [ ] I can identify at least 5 red flags in a phishing email
- [ ] I know how to report phishing in Outlook (right-click → Report → Report phishing)
- [ ] I will never give my password or MFA code over the phone
- [ ] I understand that IT will NEVER ask for my password

---

## Module 4: Data Handling & Privacy

**Audience:** All Staff | **Time:** 30 minutes

### Data Classification at DASH

We classify data by sensitivity:

| Classification | Description | Examples | Handling |
|----------------|-------------|----------|----------|
| **Restricted** | Most sensitive, regulated | PAN, CVV, encryption keys | Kraken only; never copy/share |
| **Confidential** | Business sensitive | Customer PII, financials, code | Encrypt; share only if needed |
| **Internal** | For DASH employees only | Processes, org info | Don't share externally |
| **Public** | Can be shared openly | Marketing materials, public docs | No restrictions |

### Payment Card Data (PAN)

**The Golden Rule:** PAN never leaves Kraken.

**What is PAN?**
- Primary Account Number = the 16-digit card number
- CVV = the 3-4 digit security code
- Expiry date
- Cardholder name (when combined with card number)

**Where PAN Lives:**
- Only in Kraken (our payment module)
- Encrypted with GCP KMS
- Never in logs, emails, chat, screenshots

**What You See Instead:**
- Tokens (meaningless reference numbers)
- Masked PAN: `****-****-****-1234`

**If You Accidentally See Full PAN:**
1. Do not copy, screenshot, or write it down
2. Report to CTO immediately
3. Note where you saw it (system, screen, time)

### Personally Identifiable Information (PII)

**What is PII?**

| PII Type | Examples |
|----------|----------|
| Direct identifiers | Name, email, phone number, address |
| Indirect identifiers | Date of birth, IP address, device ID |
| Financial | Bank account, transaction history |
| Location | GPS coordinates, trip history |

**PII Handling Rules:**

| Action | Allowed? |
|--------|----------|
| View PII for your job function | Yes |
| Share PII with colleagues who need it | Yes, minimally |
| Copy PII to personal device | **No** |
| Send PII in unencrypted email | **No** |
| Share PII externally without approval | **No** |
| Screenshot customer data | **No** |

### What NOT to Do

**Never:**

| Bad Practice | Why It's Dangerous |
|--------------|-------------------|
| Screenshot customer data and share in Slack | Data leaves controlled environment |
| Email PAN or CVV to anyone | Email is not secure; violates regulations |
| Copy production data to local machine | Uncontrolled storage, no encryption |
| Store credentials in code or Git | Exposed to anyone with code access |
| Discuss customer data in public | Could be overheard |
| Print documents with PII | Paper is hard to control |

**Instead:**

| Good Practice | How |
|--------------|-----|
| Reference customers by ID | User ID: `usr_abc123` not "John Smith" |
| Use tokens for payments | Token: `tok_xyz789` not card number |
| Share data in approved systems | Notion, Admin Portal (not personal email) |
| Use Secret Manager for credentials | GCP Secret Manager, never in code |

### Data Minimization

**Principle:** Only access data you need for your specific task.

| Role | Data Access |
|------|-------------|
| **CS** | Customer name, masked PAN, transaction status |
| **Engineering** | System logs, error traces (no PII) |
| **Product** | Aggregated analytics (no individual data) |
| **Admin** | Employee records, vendor contacts |

**Questions to Ask:**
- Do I need this specific data to do my job?
- Am I accessing the minimum necessary?
- Is there a less sensitive way to accomplish this?

### Handling Customer Requests

**Customer asks for their data:**
1. Verify customer identity through proper channels
2. Only provide data through approved systems
3. Never send data via personal email

**Customer asks to delete their data:**
1. Follow the data deletion process (documented separately)
2. Confirm deletion with customer
3. Document the request and completion

### Knowledge Check - Module 4

- [ ] I understand that PAN only exists in Kraken and is never shared
- [ ] I can identify what constitutes PII
- [ ] I will never screenshot customer data or send PII via personal email
- [ ] I only access data I need for my specific job function

---

## Module 5: Device Security

**Audience:** All Staff | **Time:** 30 minutes

### Your Work Device is a Security Boundary

Your laptop, phone, and other devices are entry points to DASH systems. If your device is compromised, attackers may access:
- Your email and documents
- Customer data
- Source code and systems
- Our entire network

### Device Security Requirements

**All work devices must meet these requirements:**

| Requirement | Standard | How to Verify |
|-------------|----------|---------------|
| **Disk Encryption** | FileVault (Mac) or BitLocker (Windows) | System Preferences → Security |
| **Screen Lock** | Auto-lock after 5 minutes | System Preferences → Lock Screen |
| **Strong Password/Biometric** | Required to unlock | Device settings |
| **Firewall** | Enabled | System Preferences → Firewall |
| **OS Updates** | Install within 7 days of release | Software Update |
| **Antivirus** | Windows Defender or approved solution | Security Center |

### Setting Up Device Security

**Mac - Enable FileVault:**
1. System Preferences → Security & Privacy → FileVault
2. Click "Turn On FileVault"
3. Store recovery key securely (not on the device)

**Mac - Set Screen Lock:**
1. System Preferences → Lock Screen
2. Set "Require password" to "immediately"
3. Set "Turn display off" to 5 minutes or less

**Windows - Enable BitLocker:**
1. Settings → Update & Security → Device encryption
2. Turn on device encryption
3. Back up recovery key to Microsoft account or save securely

**Windows - Set Screen Lock:**
1. Settings → Personalization → Lock screen
2. Screen timeout settings → 5 minutes or less
3. Require sign-in → When PC wakes from sleep

### Software & Downloads

**Only install approved software:**
- Work applications approved by IT
- Development tools from official sources (Engineering)
- No pirated software - ever

**Browser Extensions:**
- Only install from official browser stores
- Limit extensions to what you need
- Remove unused extensions

**Downloading Files:**
- Be cautious of downloads from unknown sources
- Scan downloaded files with antivirus
- Never download executable files from emails

### Public WiFi

**Risks:**
- Attackers can intercept traffic on public WiFi
- Fake WiFi networks can capture your data
- Other users may attempt to access your device

**Rules:**
- Avoid accessing sensitive systems on public WiFi
- Use company VPN if you must use public WiFi
- Turn off auto-connect to open networks
- Prefer mobile hotspot over unknown WiFi

### BYOD (Bring Your Own Device) Policy

**Personal devices may be used for:**
- Work email (Outlook app)
- Work chat (Teams/Slack)
- Notion access

**Personal devices may NOT be used for:**
- GCP Console access
- GitHub access to production repos
- Admin Portal access
- Any system with customer data

**Requirements for personal devices:**
- Same security settings as work devices
- MDM enrollment may be required
- Company can remote wipe work data (not personal)

### Lost or Stolen Device

**Immediately:**
1. Contact CTO (phone call) - don't wait
2. CTO initiates remote wipe
3. Change passwords for all work accounts
4. Revoke active sessions (GCP, GitHub, Microsoft 365)

**Within 24 hours:**
5. File police report if theft suspected
6. Document incident details
7. Request replacement device

**Remote Wipe Capabilities:**
- Mac: Find My Mac can locate and wipe
- Windows: Microsoft 365 can wipe enrolled devices
- Mobile: MDM can wipe work data

### Leaving Your Device

**When stepping away:**
- Lock your screen: `Cmd+Ctrl+Q` (Mac) or `Win+L` (Windows)
- Never leave device unlocked in public
- Never leave device visible in car

**End of day:**
- Log out of sensitive applications
- Lock device or shut down
- Store device securely (not visible through windows)

### Knowledge Check - Module 5

- [ ] My device has full disk encryption enabled
- [ ] My screen locks automatically after 5 minutes or less
- [ ] I know the procedure for a lost or stolen device (contact CTO immediately)
- [ ] I understand the BYOD policy and restrictions

---

## Module 6: Physical Security

**Audience:** All Staff | **Time:** 15 minutes

### Office Access

**Access Cards:**
- Your access card is personal - never share it
- Don't hold doors open for others (even colleagues)
- Report lost/stolen cards immediately

**Visitors:**
- All visitors must sign in at reception
- Visitors must be escorted at all times
- Visitor badges must be visible
- Sign visitors out when they leave

### Tailgating Prevention

**Tailgating** is when an unauthorized person follows you through a secured door.

**What to do:**
- Don't hold doors for people you don't recognize
- Politely ask unknown persons to badge in themselves
- If someone claims to have forgotten their badge, direct them to reception
- Report persistent tailgaters to Admin

**Script:** "Sorry, I can't let you in without your badge. Reception can help you."

### Clean Desk Policy

**Before leaving your desk:**

| Item | Action |
|------|--------|
| Documents with sensitive data | Lock in drawer or shred |
| Laptop | Lock screen or take with you |
| Sticky notes with passwords | Never have these; remove if found |
| USB drives | Lock away or take with you |
| Mobile devices | Take with you or lock away |

### Sensitive Document Handling

**Printing:**
- Avoid printing sensitive data
- If you must print, collect immediately
- Shred sensitive documents - don't recycle
- Never print PAN or CVV (this is prohibited)

**Whiteboards:**
- Erase sensitive information after meetings
- Don't leave customer data, architecture diagrams, or credentials visible

### Reporting Physical Security Issues

**Report immediately:**
- Unfamiliar people in the office
- Doors propped open
- Lost or stolen access cards
- Suspicious packages
- Broken locks or access systems

**Who to contact:** Admin team or CTO

### Knowledge Check - Module 6

- [ ] I will not hold doors open for people I don't recognize
- [ ] I know to escort all visitors
- [ ] I follow the clean desk policy
- [ ] I will shred sensitive documents rather than recycling them

---

## Module 7: Incident Reporting

**Audience:** All Staff | **Time:** 15 minutes

### What is a Security Incident?

A security incident is any event that compromises or could compromise the security of our systems or data.

**Examples of security incidents:**

| Category | Examples |
|----------|----------|
| **Account compromise** | Unusual login alerts, unauthorized access |
| **Malware** | Virus detected, ransomware, suspicious processes |
| **Data exposure** | PII sent to wrong recipient, public exposure |
| **Phishing** | Clicked malicious link, entered credentials |
| **Physical** | Lost laptop, stolen access card, tailgater |
| **System** | Unauthorized changes, suspicious activity in logs |

### When to Report

**Always report:**
- You clicked a suspicious link (even if nothing seemed to happen)
- You entered credentials on a suspicious site
- You received a targeted phishing email
- You lost a device or access card
- You see unusual activity on your accounts
- You accidentally sent data to the wrong person
- You see someone suspicious in the office
- You notice something "weird" (trust your instincts)

**There is no penalty for reporting:**
- Honest mistakes happen - we want to know fast
- Early reporting prevents small issues from becoming breaches
- Delayed reporting increases damage

### How to Report

**Immediate Reporting:**

| Channel | When to Use |
|---------|-------------|
| **Phone CTO directly** | Lost device, suspected breach, urgent issues |
| **Slack #security** | Suspicious emails, questions, non-urgent issues |
| **Email CTO** | Detailed incident reports, documentation |

**What to Include:**
1. What happened (be specific)
2. When it happened (date and time)
3. What systems or data were involved
4. What actions you took
5. Any evidence (screenshots, email headers)

### Incident Response Timeline

After you report:

| Time | Action |
|------|--------|
| **Immediate** | CTO acknowledges receipt, assesses urgency |
| **Within 1 hour** | Initial containment (password reset, device wipe, etc.) |
| **Within 24 hours** | Investigation begins, you may be asked for more details |
| **Ongoing** | Updates provided, root cause identified |
| **After resolution** | Lessons learned, process improvements |

### Incident Examples and Actions

**Scenario 1: You clicked a phishing link**
1. Don't enter any credentials
2. Close the browser immediately
3. Report to CTO via Slack or phone
4. Change your password (from a different device if possible)
5. Watch for suspicious activity on your accounts

**Scenario 2: You lost your laptop**
1. Call CTO immediately (don't wait)
2. CTO initiates remote wipe
3. Change all work passwords
4. File police report if stolen
5. Document what was on the device

**Scenario 3: You sent customer data to the wrong email**
1. Report to CTO immediately
2. Contact the recipient and request deletion
3. Document what data was sent
4. CTO assesses if customer notification is needed

### Knowledge Check - Module 7

- [ ] I know that clicking a suspicious link is reportable (even if nothing happened)
- [ ] I know how to contact CTO for security incidents
- [ ] I understand there is no penalty for reporting honest mistakes
- [ ] I will report immediately rather than waiting

---

## Module 8: QA Security Practices

**Audience:** QA Team & Engineering | **Time:** 30 minutes

### Security in the QA Process

QA plays a critical role in security by:
- Testing that security controls work as designed
- Identifying vulnerabilities before they reach production
- Validating that sensitive data is handled correctly
- Ensuring security requirements are met

### Test Data Principles

**Never use production data for testing.**

| Data Type | Test Environment Rule |
|-----------|----------------------|
| PAN (card numbers) | Use test card numbers only |
| Customer PII | Use synthetic/fake data |
| Credentials | Use test credentials, never production |
| API keys | Use test environment keys only |

**Test Card Numbers (for QA use):**

| Card Type | Test Number | CVV | Expiry |
|-----------|-------------|-----|--------|
| Visa | 4111 1111 1111 1111 | 123 | Any future date |
| Mastercard | 5555 5555 5555 4444 | 123 | Any future date |
| Declined | 4000 0000 0000 0002 | 123 | Any future date |

### Security Testing Checklist

**Authentication Testing:**
- [ ] Verify MFA is required for sensitive operations
- [ ] Test password requirements (length, complexity)
- [ ] Verify account lockout after failed attempts
- [ ] Test session timeout
- [ ] Verify logout clears session completely

**Authorization Testing:**
- [ ] Verify users can only access their own data
- [ ] Test role-based access controls
- [ ] Attempt to access unauthorized endpoints
- [ ] Verify API endpoints require authentication

**Data Protection Testing:**
- [ ] Verify PAN is never displayed in full
- [ ] Check logs don't contain PII or PAN
- [ ] Verify data is encrypted in transit (HTTPS)
- [ ] Test that CVV is never stored

**Input Validation Testing:**
- [ ] Test with special characters (<, >, ', ", &)
- [ ] Test with SQL injection patterns
- [ ] Test with excessively long inputs
- [ ] Test with unexpected data types

### Reporting Security Issues

**When you find a security issue:**

1. **Document thoroughly:**
   - Steps to reproduce
   - Expected vs actual behavior
   - Screenshots/recordings
   - Impact assessment

2. **Report appropriately:**
   - Critical issues: Direct message to CTO and Engineering Lead
   - Other issues: Create ticket marked "Security"

3. **Don't exploit:**
   - Verify the issue exists, then stop
   - Don't access data you shouldn't
   - Don't share vulnerability details widely

### Test Environment Security

**Test environments must:**
- Not have production data
- Use separate credentials from production
- Be isolated from production networks
- Have test payment gateway configurations

**You are responsible for:**
- Not copying production data to test
- Using only test credentials
- Reporting if you see production data in test

### Knowledge Check - Module 8

- [ ] I will never use production data for testing
- [ ] I know the test card numbers to use for QA
- [ ] I know how to report security issues I discover
- [ ] I understand the security testing checklist

---

## Module 9: Secure Development Practices

**Audience:** Engineering Team Only | **Time:** 60 minutes

> **ENGINEERING ONLY**: This module contains technical security practices specific to the Engineering team.

### Security-First Mindset

**Five Principles:**

1. **Assume breach:** Design as if attackers are already inside
2. **Defense in depth:** Multiple layers of security
3. **Least privilege:** Minimum access needed to function
4. **Fail secure:** Errors deny access, not grant it
5. **Secure defaults:** Secure out of the box; explicit opt-out for less security

### The OWASP Top 10

Know and prevent these common vulnerabilities:

| Vulnerability | Prevention |
|--------------|------------|
| **A01: Broken Access Control** | Server-side authorization; deny by default; validate permissions |
| **A02: Cryptographic Failures** | Use strong encryption (AES-256); protect keys; encrypt PII |
| **A03: Injection** | Parameterized queries; input validation; output encoding |
| **A04: Insecure Design** | Threat modeling; secure design patterns; security requirements |
| **A05: Security Misconfiguration** | Secure defaults; remove unnecessary features; harden configs |
| **A06: Vulnerable Components** | Keep dependencies updated; npm audit; remove unused packages |
| **A07: Auth Failures** | Strong password policies; MFA; rate limiting; secure sessions |
| **A08: Data Integrity Failures** | Verify software integrity; sign releases; validate inputs |
| **A09: Logging Failures** | Log security events; protect logs; ensure log integrity |
| **A10: SSRF** | Validate URLs; whitelist allowed destinations; block metadata endpoints |

### Input Validation

**Never trust user input.**

```javascript
// BAD - SQL Injection vulnerable
const query = `SELECT * FROM users WHERE id = '${userId}'`;

// GOOD - Parameterized query
const query = 'SELECT * FROM users WHERE id = $1';
const result = await db.query(query, [userId]);
```

```javascript
// BAD - XSS vulnerable
element.innerHTML = userInput;

// GOOD - Escaped output
element.textContent = userInput;
// or use a sanitization library
element.innerHTML = DOMPurify.sanitize(userInput);
```

**Validation Rules:**
- Validate on the server (client validation is not security)
- Whitelist acceptable input, don't blacklist bad input
- Validate type, length, format, and range
- Reject invalid input - don't try to "fix" it

### Output Encoding

**Encode output based on context:**

| Context | Encoding |
|---------|----------|
| HTML body | HTML entity encoding |
| HTML attributes | Attribute encoding |
| JavaScript | JavaScript encoding |
| URL parameters | URL encoding |
| CSS | CSS encoding |

### Authentication & Authorization

**Authentication (Who are you?):**
- Use established libraries (Passport, Firebase Auth)
- Never store passwords in plain text
- Use bcrypt or Argon2 for password hashing
- Implement MFA for sensitive operations
- Use secure session management

**Authorization (What can you do?):**
- Check permissions on every request (server-side)
- Don't rely on hidden UI elements for security
- Implement role-based access control (RBAC)
- Verify resource ownership before access

```javascript
// BAD - Only checks if logged in
app.get('/user/:id', requireAuth, async (req, res) => {
  const user = await User.findById(req.params.id);
  return res.json(user);
});

// GOOD - Checks ownership
app.get('/user/:id', requireAuth, async (req, res) => {
  if (req.params.id !== req.user.id && !req.user.isAdmin) {
    return res.status(403).json({ error: 'Forbidden' });
  }
  const user = await User.findById(req.params.id);
  return res.json(user);
});
```

### Secrets Management

**Never hardcode secrets:**

```javascript
// BAD - Hardcoded secret
const apiKey = 'sk_live_abc123xyz';

// GOOD - Environment variable
const apiKey = process.env.API_KEY;

// BETTER - Secret Manager
const apiKey = await getSecret('api-key');
```

**Where secrets live:**
- GCP Secret Manager (production)
- Environment variables (local development)
- Never in code, never in Git

**Detection:**
- SonarQube scans for hardcoded secrets
- Git hooks prevent committing secrets
- If you accidentally commit a secret: rotate it immediately

### Dependency Security

**Keep dependencies updated:**

```bash
# Check for vulnerabilities
npm audit

# Fix vulnerabilities
npm audit fix

# Update dependencies
npm update
```

**Rules:**
- Run `npm audit` before every commit
- Address critical/high vulnerabilities immediately
- Review new dependencies before adding
- Minimize number of dependencies

### Code Review Security Checklist

When reviewing code, check:

- [ ] No hardcoded secrets or credentials
- [ ] Input validation on all user inputs
- [ ] Output encoding appropriate to context
- [ ] SQL queries are parameterized
- [ ] Authorization checks on all endpoints
- [ ] No sensitive data in logs
- [ ] Dependencies are up to date
- [ ] Error messages don't leak internal details
- [ ] Files are uploaded safely (if applicable)
- [ ] APIs require authentication

### Knowledge Check - Module 9

- [ ] I can explain the OWASP Top 10 vulnerabilities
- [ ] I know how to prevent SQL injection and XSS
- [ ] I will never hardcode secrets in code
- [ ] I understand the code review security checklist

---

## Module 10: Logging & Monitoring Standards

**Audience:** Engineering Team Only | **Time:** 30 minutes

> **ENGINEERING ONLY**: This module covers logging standards and practices specific to the Engineering team.

### Why Logging Matters

Logs serve multiple purposes:
- **Security monitoring:** Detect attacks and unauthorized access
- **Incident investigation:** Understand what happened during a breach
- **Debugging:** Troubleshoot application issues
- **Compliance:** Demonstrate security controls work

### What to Log

**Security Events (Always Log):**

| Event Type | What to Include |
|------------|-----------------|
| Authentication | User ID, success/failure, timestamp, IP |
| Authorization failures | User ID, resource, action, timestamp |
| Data access | User ID, resource type, action (not the data itself) |
| Administrative actions | Admin ID, action, target, timestamp |
| System events | Service name, event type, timestamp |
| Errors | Error type, context (sanitized), timestamp |

### What NEVER to Log

**These must never appear in logs:**

| Data Type | Why | Instead Log |
|-----------|-----|-------------|
| **PAN (Card Numbers)** | PCI DSS violation | Token reference only |
| **CVV/CVC** | Never stored anywhere | Nothing |
| **Passwords** | Security risk | "Authentication attempt" |
| **PII (Names, Emails, Phones)** | Privacy risk | User ID only |
| **API Keys / Secrets** | Security risk | "API call to [service]" |
| **Session Tokens** | Session hijacking risk | Session ID (if needed) |
| **Full Request Bodies** | May contain sensitive data | Sanitized summary |
| **Full Response Bodies** | May contain sensitive data | Status code only |

### Log Format Standard

Use structured JSON logging:

```json
{
  "timestamp": "2026-01-30T14:30:00.000Z",
  "level": "INFO",
  "service": "kraken",
  "event": "payment_authorized",
  "user_id": "usr_abc123",
  "request_id": "req_xyz789",
  "transaction_id": "txn_def456",
  "amount_cents": 10000,
  "currency": "HKD",
  "message": "Payment authorized successfully"
}
```

**Required Fields:**

| Field | Description | Example |
|-------|-------------|---------|
| `timestamp` | ISO8601 format | `2026-01-30T14:30:00.000Z` |
| `level` | Log level | `DEBUG`, `INFO`, `WARN`, `ERROR` |
| `service` | Service name | `kraken`, `dash-core`, `admin-portal` |
| `event` | Event type | `user_login`, `payment_processed` |
| `request_id` | Correlation ID | `req_xyz789` |
| `message` | Human-readable message | No PII |

### Log Levels

Use appropriate log levels:

| Level | When to Use | Example |
|-------|-------------|---------|
| `ERROR` | Unexpected failures, requires attention | Database connection failed |
| `WARN` | Potential issues, degraded state | Rate limit approaching |
| `INFO` | Significant business events | Payment processed |
| `DEBUG` | Detailed diagnostic info | Request routing details |

**In Production:**
- Log `INFO` and above by default
- `DEBUG` only when troubleshooting specific issues
- Never log raw request/response bodies

### Common Mistakes

**BAD - Logging PII:**
```javascript
logger.info(`User ${user.email} logged in`);
// Exposes email in logs
```

**GOOD - Logging User ID:**
```javascript
logger.info('User logged in', { user_id: user.id });
```

**BAD - Logging Sensitive Errors:**
```javascript
logger.error(`Login failed: invalid password for ${email}`);
// Reveals that email exists
```

**GOOD - Generic Error:**
```javascript
logger.warn('Login failed', {
  user_id: userId,
  reason: 'invalid_credentials'
});
```

**BAD - Logging Full Requests:**
```javascript
logger.debug('Received request', { body: req.body });
// May contain passwords, card numbers
```

**GOOD - Logging Metadata:**
```javascript
logger.debug('Received request', {
  endpoint: req.path,
  method: req.method,
  content_length: req.body?.length
});
```

### Log Sanitization

If you must log request data, sanitize it:

```javascript
function sanitizeForLogging(data) {
  const sanitized = { ...data };

  // Remove known sensitive fields
  const sensitiveFields = [
    'password', 'card_number', 'cvv', 'pan',
    'authorization', 'api_key', 'token', 'secret'
  ];

  sensitiveFields.forEach(field => {
    if (sanitized[field]) {
      sanitized[field] = '[REDACTED]';
    }
  });

  return sanitized;
}
```

### Log Retention

| Log Type | Retention | Accessibility |
|----------|-----------|---------------|
| Application logs | 12 months minimum | 3 months immediately available |
| Security events | 12 months minimum | 3 months immediately available |
| Audit logs | 12 months minimum | Full period searchable |

### Log Review

Logs are automatically:
- Aggregated in GCP Cloud Logging
- Monitored for security alerts
- Reviewed during incident investigations

**Periodic Reviews:**
- Security team reviews authentication failures weekly
- Automated alerts for anomalous patterns
- Quarterly log audit for sensitive data leaks

### Knowledge Check - Module 10

- [ ] I know what must never appear in logs (PAN, CVV, passwords, PII)
- [ ] I use structured JSON logging with required fields
- [ ] I use user_id instead of email/name in logs
- [ ] I understand log levels and when to use each

---

## Training Completion

### Acknowledgment Form

After completing all required modules, sign this acknowledgment:

---

**Security Training Acknowledgment**

I, _________________________, confirm that I have:

- [ ] Completed all security training modules required for my role
- [ ] Understood the security policies and practices described
- [ ] Understood my responsibility to protect company and customer data
- [ ] Understood my obligation to report security incidents immediately
- [ ] Agreed to follow these security practices in my daily work

**Signature:** _________________________

**Date:** _________________________

**Team:** [ ] CS  [ ] Admin  [ ] Product  [ ] QA  [ ] Engineering

---

### Knowledge Check Answers

Your team lead or CTO will verify your understanding through:
1. Review of completed module checkboxes
2. Brief verbal confirmation of key concepts
3. Practical demonstration (where applicable)

### Questions?

If you have questions about any security topic:
- Slack: #security channel
- Email: CTO
- In-person: Ask your team lead

### Annual Refresher

This training must be completed:
- **New hires:** Within 30 days of start date
- **All staff:** Annually (Q1 each year)
- **After incidents:** Additional targeted training as needed

---

## Quick Reference Card

**Cut out and keep at your desk:**

```
┌─────────────────────────────────────────────────────────┐
│           DASH SECURITY QUICK REFERENCE                 │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  REPORT PHISHING:                                       │
│  Right-click email → Report → Report phishing           │
│                                                         │
│  REPORT INCIDENTS:                                      │
│  Phone CTO immediately, then Slack #security            │
│                                                         │
│  LOST DEVICE:                                           │
│  Call CTO immediately (don't wait!)                     │
│                                                         │
│  LOCK YOUR SCREEN:                                      │
│  Mac: Cmd+Ctrl+Q  |  Windows: Win+L                     │
│                                                         │
│  NEVER:                                                 │
│  • Share passwords or MFA codes                         │
│  • Click links in suspicious emails                     │
│  • Send PAN/CVV via email or chat                       │
│  • Use production data for testing                      │
│  • Install unapproved software                          │
│                                                         │
│  ALWAYS:                                                │
│  • Use MFA on all accounts                              │
│  • Lock screen when away                                │
│  • Verify before trusting                               │
│  • Report suspicious activity                           │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

**Document Information:**
- Version: 1.0
- Last Updated: 2026-01-30
- Owner: CTO
- Review Cycle: Annual
- Next Review: 2027-01-30
