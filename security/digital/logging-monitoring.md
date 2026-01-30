# Logging & Monitoring

**Owner:** CTO
**Version:** 1.2
**Last Reviewed:** 2026-01-30
**Review Cadence:** Quarterly

---

## 1. Purpose

We collect, store, and monitor logs to detect issues, investigate incidents, and maintain an audit trail of system activity. This control ensures we can answer "what happened and when" for any production event.

**Risk Reduced:**
- Undetected security incidents or breaches
- Inability to investigate production issues
- Missing audit trail for compliance
- Sensitive data leakage through logs

**Stakeholders:**
- Engineering Team (troubleshooting, incident response)
- CTO (security oversight, audit)
- CS Team (customer issue investigation via Admin Portal logs)

---

## 2. Scope

### In Scope
- **Systems:** All DASH applications, Kraken, Admin Portal
- **Environments:** Production, QA
- **Log Types:** Application logs, error tracking, GCP audit logs, access logs
- **Users:** Engineering team (log viewers)

### Out of Scope
- Local development environment logs
- Third-party SaaS tool logs (Notion, Slack, etc.)
- Payment gateway internal logs (Soepay, GP) - their responsibility

### GCP Inherited Logging (PCI DSS 4.0.1)

Because we deploy on **Cloud Run** and **Cloud Functions**, GCP provides significant inherited logging:

| Log Type | GCP Provides | PCI Requirement |
|----------|-------------|-----------------|
| Cloud Audit Logs (Admin Activity) | Always enabled, immutable | 10.2.1.2 (admin actions) |
| Cloud Audit Logs (Data Access) | Configurable, immutable | 10.2.1.1 (data access) |
| Cloud Run/Functions request logs | Automatic per-request logging | 10.2.1 (audit logs enabled) |
| VPC Flow Logs | Network traffic records | 10.2.1 |
| Access Transparency | Google admin access to your data | 10.2.1.2 |

**Key Benefit:** GCP Cloud Audit Logs are **immutable** and cannot be modified or deleted by customers, satisfying PCI 10.3.2 (logs protected from modification).

### Logging Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    DASH Applications                         │
│              (DASH Main, Kraken, Admin Portal)               │
└──────────────┬─────────────────────────────┬────────────────┘
               │                             │
               ▼                             ▼
┌─────────────────────────┐    ┌─────────────────────────────┐
│        Sentry           │    │      GCP Cloud Logging      │
│   (Error Tracking)      │    │     (Log Explorer)          │
│                         │    │                             │
│ • Exceptions            │    │ • Application logs          │
│ • Stack traces          │    │ • Audit logs                │
│ • Performance issues    │    │ • Access logs               │
└─────────────────────────┘    │ • Retention: 365 days       │
                               └──────────────┬──────────────┘
                                              │
                    ┌─────────────────────────┼─────────────────────────┐
                    │                         │                         │
                    ▼                         ▼                         ▼
┌─────────────────────────┐  ┌─────────────────────────┐  ┌─────────────────────────┐
│  Security Command       │  │  GCP Sensitive Data     │  │  GCP Cloud Monitoring   │
│  Center (SCC)           │  │  Protection             │  │  (Alerting)             │
│                         │  │                         │  │                         │
│ • Threat detection      │  │ • Scan for PAN/PII      │  │ • Real-time alerts      │
│ • Vulnerability scan    │  │ • Auto de-identify      │  │ • Teams + Email notify  │
│ • Security findings     │  │ • Policy monitoring     │  │ • Daily review support  │
│ • Compliance dashboard  │  │                         │  │                         │
└─────────────────────────┘  └─────────────────────────┘  └─────────────────────────┘
```

---

## 3. Roles & Responsibilities

| Role | Team/Individual | Responsibility |
|------|-----------------|----------------|
| Control Owner | CTO | Sets logging standards, reviews retention, approves exceptions |
| Operator | Engineering Team | Configures logging, responds to alerts, investigates issues |
| Alert Responder | Engineering Team (on-call) | Acknowledges and triages alerts |
| Log Viewer | All Engineers | Access to view logs for troubleshooting |

---

## 4. How We Operate This Control

### 4.1 Log Collection

**Application Logs:**
- All applications send logs to GCP Cloud Logging
- Logs include: timestamp, service name, log level, message, request ID
- [ASSUMPTION: Structured JSON logging format]

**Error Tracking:**
- Sentry captures exceptions, stack traces, and performance issues
- Sentry SDK integrated into all applications
- Errors grouped and deduplicated automatically

**GCP Audit Logs (Inherited - PCI DSS Compliant):**
- **Admin Activity logs:** Always on, cannot be disabled (GCP managed)
- **Data Access logs:** Enabled for sensitive resources (Kraken, KMS)
- **System Event logs:** GCP infrastructure events
- **Access Transparency logs:** Near real-time Google admin access (if enabled)
- **Retention:** Admin Activity logs retained 400 days by default (exceeds PCI 12-month requirement)

**Cloud Run / Cloud Functions Logging:**
- Request logs automatically captured (no agent installation required)
- Execution logs streamed to Cloud Logging
- Error logs with stack traces
- Cold start and scaling events

### 4.2 Log Sanitization

**What We Prevent in Logs:**
- Full PAN (card numbers)
- Passwords or credentials
- API keys or secrets
- Full customer PII [ASSUMPTION: Define which fields]

**How We Enforce:**
1. Logging libraries configured with sanitization filters
2. SonarQube rules detect logging of sensitive patterns
3. Code review checks for proper log handling
4. GCP Sensitive Data Protection scans logs for sensitive data
5. GCP Sensitive Data Protection automatically de-identifies detected sensitive data

**Sanitization Rules:**

| Data Type | Treatment | Example |
|-----------|-----------|---------|
| PAN | Never logged; tokenized reference only | - |
| Passwords | Never logged | - |
| API keys | Never logged | - |
| Email addresses | Masked: first 2 + last 2 chars before @ + domain | `jo**ny@example.com` |
| Transaction IDs | Logged (non-sensitive) | - |
| User IDs | Logged (for traceability) | - |

### 4.3 Sensitive Data Protection (Automated)

**Tool:** GCP Sensitive Data Protection (formerly DLP)

**What It Does:**
- Continuously scans logs for sensitive data patterns
- Automatically de-identifies sensitive data if detected
- Monitors and reports on sensitive data findings

**Detection Patterns:**
| InfoType | Description |
|----------|-------------|
| CREDIT_CARD_NUMBER | Card numbers (PAN) |
| EMAIL_ADDRESS | Email addresses |
| PHONE_NUMBER | Phone numbers |

**De-identification Methods:**
- Masking (partial redaction)
- Tokenization

**Monitoring:**
- Sensitive Data Protection findings reviewed in GCP Console
- Alerts triggered when sensitive data detected in logs
- Findings feed into security monitoring workflow

### 4.3 Log Access

**Who Can View Logs:**

| Team | Sentry Access | GCP Log Explorer Access |
|------|---------------|------------------------|
| Engineering | Yes (all projects) | Yes (read-only) |
| QA | [ASSUMPTION: Read-only Sentry] | [ASSUMPTION: No access] |
| CS | No | No (use Admin Portal for customer data) |
| CTO/CEO | Yes | Yes (admin) |

**Access Method:**
- Sentry: Direct login via Sentry dashboard
- GCP Log Explorer: Via GCP Console with IAM permissions

### 4.4 Alerting

**Alert Configuration:**
- Alerts configured in GCP Log Explorer
- Alert conditions based on log patterns (errors, security events)

**Alert Types:**

| Alert Category | Trigger | Notification | Response |
|---------------|---------|--------------|----------|
| Application errors | Error rate spike | Teams + Email | Engineer investigates |
| Security events | Failed login threshold | Teams + Email | Engineer + CTO notified |
| System health | Service down | Teams + Email | On-call engineer responds |

**Alert Response Process:**
1. Alert fires and notifies via Teams channel + email
2. On-call engineer acknowledges alert
3. Engineer investigates using Log Explorer and Sentry
4. Issue resolved or escalated
5. [ASSUMPTION: No formal incident ticket created for all alerts - only major incidents]

### 4.5 Log Retention

**Cloud Logging Retention Configuration:** 365 days (configured 2026-01-30)

| Log Type | System | Retention Period | Notes |
|----------|--------|-----------------|-------|
| Application logs | GCP Cloud Logging | **365 days** | Custom retention configured |
| Audit logs (Admin Activity) | GCP Cloud Logging | **400 days** | GCP default, immutable |
| Audit logs (Data Access) | GCP Cloud Logging | **365 days** | Custom retention configured |
| Security Command Center findings | GCP SCC | **365 days** | Configured with SCC Premium |
| Error tracking | Sentry | 90 days | Sentry retention policy |
| Admin Portal actions | GCP Cloud Logging | **365 days** | Included in application logs |

**Retention Configuration:**
- GCP Console > Logging > Log Router > Configure retention
- All log buckets set to 365-day retention
- Audit logs automatically retained 400 days (cannot be reduced)
- 3 months (90 days) immediately searchable; older logs available via archive query

### 4.6 Security Command Center (SCC)

**Status:** Enabled (Premium tier)

**What Security Command Center Provides:**

| Feature | Description | Benefit |
|---------|-------------|---------|
| **Event Threat Detection** | Real-time analysis of Cloud Logging for threats | Detects malware, crypto mining, data exfiltration |
| **Container Threat Detection** | Runtime security for Cloud Run | Detects malicious scripts, reverse shells |
| **Security Health Analytics** | Configuration scanning | Identifies misconfigurations, compliance gaps |
| **Web Security Scanner** | Automated web app vulnerability scanning | Finds XSS, outdated libraries, misconfigs |
| **Vulnerability Dashboard** | Centralized security posture view | Single pane for all security findings |
| **Compliance Monitoring** | Continuous compliance checks | Maps findings to PCI DSS, CIS benchmarks |

**SCC Findings Integration:**
- Findings automatically logged to Cloud Logging
- Critical/High findings trigger alerts (Teams + Email)
- Findings feed into daily log review process
- Compliance dashboard used for quarterly reviews

**SCC Alert Configuration:**

| Finding Severity | Notification | Response SLA |
|-----------------|--------------|--------------|
| Critical | Teams + Email + Phone (on-call) | 1 hour |
| High | Teams + Email | 4 hours |
| Medium | Teams + Email | 24 hours |
| Low | Daily review summary | Next review cycle |

### 4.7 Daily Log Review Process

**Frequency:** Daily (weekdays), Weekend coverage via automated alerts

**Reviewer:** Engineering On-Call (rotating weekly)

**Review Time:** Morning (within first 2 hours of business day)

#### 4.7.1 What We Review Daily

| Log Category | Source | What to Look For |
|--------------|--------|------------------|
| **Security Events** | SCC Dashboard, Cloud Logging | Failed logins, privilege escalation, suspicious API calls |
| **Kraken (CHD System)** | Cloud Logging (Kraken service) | Payment errors, unusual transaction patterns, access anomalies |
| **Authentication** | Firebase Auth logs, GCP IAM | Failed auth attempts, new account creations, permission changes |
| **Critical System Components** | Cloud Run, Cloud SQL, KMS | Service errors, database connection issues, key access |
| **Security Functions** | WAF (Cloud Armor), VPC Flow Logs | Blocked requests, unusual traffic patterns, new IPs |

#### 4.7.2 Daily Review Checklist

**Security Command Center Review:**
- [ ] Check SCC Dashboard for new Critical/High findings
- [ ] Review Event Threat Detection alerts from past 24 hours
- [ ] Check Container Threat Detection for Cloud Run anomalies
- [ ] Note any new Medium findings for tracking

**Cloud Logging Review:**
- [ ] Query: Failed authentication attempts (threshold: >10 from single IP)
- [ ] Query: Admin/privileged actions in past 24 hours
- [ ] Query: Kraken service errors and access logs
- [ ] Query: KMS key access events

**Alert Review:**
- [ ] Acknowledge all fired alerts from past 24 hours
- [ ] Verify alerts were appropriately triaged
- [ ] Close resolved alerts with notes

#### 4.7.3 Daily Review Queries

**Failed Authentication (Cloud Logging):**
```
resource.type="cloud_run_revision"
severity>=WARNING
textPayload=~"authentication failed|login failed|unauthorized"
timestamp>="-24h"
```

**Privileged Actions (Audit Logs):**
```
logName:"cloudaudit.googleapis.com/activity"
protoPayload.authenticationInfo.principalEmail:*
protoPayload.methodName=~"SetIamPolicy|CreateServiceAccount|delete"
timestamp>="-24h"
```

**Kraken CHD Access:**
```
resource.labels.service_name="kraken"
timestamp>="-24h"
```

**KMS Key Operations:**
```
resource.type="cloudkms_cryptokey"
timestamp>="-24h"
```

#### 4.7.4 Review Documentation

**Daily Review Log Location:** Notion > Security > Daily Log Reviews

**Each Review Entry Contains:**

| Field | Description |
|-------|-------------|
| Date | Review date |
| Reviewer | Name of reviewer (on-call engineer) |
| Review Start Time | When review began |
| Review End Time | When review completed |
| SCC Findings | Count of new Critical/High/Medium findings |
| Alerts Reviewed | Number of alerts reviewed |
| Anomalies Found | Description of any suspicious activity |
| Actions Taken | Any escalations, incidents created, or follow-ups |
| Sign-off | Reviewer confirmation that review is complete |

**Escalation Criteria:**
- Any Critical SCC finding → Escalate to CTO immediately
- >50 failed auth attempts from single source → Potential brute force, escalate
- Unexpected admin actions → Verify with user, escalate if unauthorized
- Kraken access anomaly → Escalate to CTO, potential incident

### 4.8 Periodic Log Review (Non-Critical Systems)

**Targeted Risk Analysis for Review Frequency:**

Based on risk assessment, non-critical systems are reviewed at the following frequencies:

| System Category | Risk Level | Review Frequency | Justification |
|-----------------|------------|------------------|---------------|
| **CDE Systems (Kraken, KMS)** | Critical | Daily | Stores/processes cardholder data |
| **Auth Systems (Firebase, IAM)** | Critical | Daily | Controls all access |
| **Security Functions (WAF, NSC)** | Critical | Daily | Protects perimeter |
| **Production Applications (DASH Main)** | High | Daily | Customer-facing, processes transactions |
| **Admin Portal** | High | Daily | Access to transaction data |
| **Cloud SQL (Production)** | High | Daily | Contains customer data |
| **QA Environment** | Medium | Weekly | No production data, isolated |
| **CI/CD Pipeline (GitHub Actions)** | Medium | Weekly | Could affect production |
| **Monitoring Infrastructure** | Low | Monthly | Supporting system |
| **Development Tools** | Low | Monthly | No production access |

**Risk Factors Considered:**
1. **Data Sensitivity:** Does the system process/store CHD or PII?
2. **Access Level:** Can the system access the CDE?
3. **Exposure:** Is the system internet-facing?
4. **Impact:** What is the impact if compromised?
5. **Attack Surface:** How likely is the system to be targeted?

**Review Process for Non-Critical Systems:**
- Weekly reviews conducted every Monday (QA, CI/CD)
- Monthly reviews conducted first Monday of month (Monitoring, Dev tools)
- Findings documented in same Notion database as daily reviews
- Anomalies escalate to daily review process

### 4.9 Log Investigation

**For Production Issues:**
1. Check Sentry for recent exceptions
2. Use GCP Log Explorer to search relevant timeframe
3. Filter by service, request ID, or user ID
4. Correlate logs across services using request ID

**For Security Investigations:**
1. Identify timeframe and affected systems
2. Query GCP Audit Logs for access patterns
3. Check application logs for suspicious activity
4. Document findings
5. Escalate to CTO if security incident confirmed

---

## 5. Operational Guarantees

When this control operates correctly:

- [ ] All production applications send logs to GCP Cloud Logging
- [ ] All application errors are captured in Sentry
- [ ] Logs never contain full PAN, passwords, or API keys
- [ ] All engineers can view logs for troubleshooting
- [ ] Alerts are configured for critical error conditions
- [ ] **All logs are retained for at least 365 days (12 months)**
- [ ] **Audit logs are retained for at least 400 days**
- [ ] Log access is limited to authorized personnel
- [ ] **Security Command Center is enabled and monitoring all GCP resources**
- [ ] **Daily log review is performed every business day by on-call engineer**
- [ ] **Daily review includes: security events, CHD system logs, critical systems, security functions**
- [ ] **Daily review is documented with reviewer sign-off**
- [ ] **Non-critical systems are reviewed per risk-based frequency (weekly/monthly)**

---

## 6. Evidence Produced

| Evidence Type | Description | System/Tool | Retention | Collection | Owner |
|--------------|-------------|-------------|-----------|------------|-------|
| Application logs | Runtime events, requests, errors | GCP Cloud Logging | **365 days** | Automatic | Engineering |
| GCP Audit Logs | IAM changes, resource access | GCP Cloud Logging | **400 days** | Automatic | CTO |
| Security Command Center findings | Threats, vulnerabilities, misconfigs | GCP SCC | **365 days** | Automatic | CTO |
| Error reports | Exceptions with stack traces | Sentry | 90 days | Automatic | Engineering |
| Alert history | Fired alerts and responses | GCP Monitoring | 365 days | Automatic | Engineering |
| Log access records | Who accessed Log Explorer | GCP Audit Logs | 400 days | Automatic | CTO |
| Sensitive data findings | Detected sensitive data in logs | GCP Sensitive Data Protection | 90 days | Automatic | CTO |
| **Daily review logs** | Daily log review completion records | Notion | Indefinite | Manual | Engineering |
| **Weekly/Monthly review logs** | Periodic review completion records | Notion | Indefinite | Manual | Engineering |

### Evidence Retrieval

- **Application logs:** GCP Console > Logging > Log Explorer > Filter by resource/time
- **Audit logs:** GCP Console > Logging > Log Explorer > Filter by `logName:"cloudaudit.googleapis.com"`
- **Security Command Center:** GCP Console > Security > Security Command Center > Findings
- **Sentry errors:** Sentry dashboard > Filter by project/date
- **Alert history:** GCP Console > Monitoring > Alerting > Incidents
- **Sensitive data findings:** GCP Console > Security > Sensitive Data Protection > Findings
- **Daily review records:** Notion > Security > Daily Log Reviews
- **Retention configuration:** GCP Console > Logging > Log Router > [bucket] > Retention

### Evidence Verification

| Configuration | How to Verify | Expected Value |
|---------------|---------------|----------------|
| Log retention | Log Router > _Default bucket > Retention | 365 days |
| SCC enabled | Security Command Center > Dashboard | Premium tier active |
| Daily reviews | Notion > Daily Log Reviews | Entry for each business day |

---

## 7. Exceptions & Edge Cases

### Known Exceptions

| Exception | Justification | Compensating Control | Review Date |
|-----------|--------------|---------------------|-------------|
| Development logs not centralized | Cost/noise reduction | Dev issues caught in QA/Prod | Quarterly |

### Exception Process

1. Exception requests go to CTO
2. CTO evaluates security/audit impact
3. Approved exceptions documented
4. Reviewed quarterly

### Edge Cases

- **Log Explorer unavailable:** Use Sentry for error investigation; GCP status page for outage updates
- **Sentry quota exceeded:** Logs still available in GCP; prioritize fixing Sentry integration
- **Sensitive data found in logs:** Immediately notify CTO, delete affected logs, investigate source, fix logging code

---

## 8. Review & Maintenance

**Review Schedule:**
- **Frequency:** Quarterly
- **Next Review:** 2025-04-27
- **Reviewer:** CTO

**Update Triggers:**
- New applications or services added
- Changes to logging infrastructure
- Security incidents involving log data
- Changes to retention requirements

**Change History:**

| Date | Change | Author |
|------|--------|--------|
| 2025-01-27 | Initial document created | [Author] |
| 2026-01-29 | Added GCP inherited logging controls for serverless (Cloud Run/Functions) | [Author] |
| 2026-01-30 | v1.2: **365-day log retention** configured for Cloud Logging; **Security Command Center (Premium)** enabled with threat detection, vulnerability scanning, compliance monitoring; **Daily Log Review Process** documented (Section 4.7) with checklist, queries, and sign-off; **Targeted Risk Analysis** for periodic review frequency (Section 4.8); Updated retention table and evidence | [Author] |

---

## 9. Related Controls

| Control Area | Relationship |
|-------------|--------------|
| Access Control & Identity Management | Log access governed by IAM; IAM changes logged here |
| Secure Development & Data Protection | Log sanitization enforced during development |
| Deployment & Release Management | Deployment events logged |
| Admin Portal Access Control | Admin Portal actions logged for audit |
| Incident Response | Logs are primary investigation tool for incidents |
| Network Security | VPC Flow Logs and WAF logs feed into central logging |
| Vulnerability Management | Scan results and alerts captured in logs |
| Third-Party Risk Management | GCP inherited logging controls documented there |
| Security Standards & Exception Governance | Log retention exceptions follow governance process |
| Security Policy & Awareness | Logging standards training for Engineering team |
