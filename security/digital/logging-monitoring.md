# Logging & Monitoring

**Owner:** CTO
**Version:** 1.1
**Last Reviewed:** 2026-01-29
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
└─────────────────────────┘    │ • Alerts (→ Teams + Email)  │
                               └──────────────┬──────────────┘
                                              │
                                              ▼
                               ┌─────────────────────────────┐
                               │  GCP Sensitive Data         │
                               │  Protection                 │
                               │                             │
                               │ • Scan for sensitive data   │
                               │ • Auto de-identification    │
                               │ • Monitoring & alerts       │
                               └─────────────────────────────┘
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

**GCP Audit Logs:**
- Admin Activity logs (always on)
- Data Access logs [ASSUMPTION: Enabled for sensitive resources]
- System Event logs

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

| Log Type | System | Retention Period |
|----------|--------|-----------------|
| Application logs | GCP Cloud Logging | [ASSUMPTION: 30 days default - verify] |
| Audit logs | GCP Cloud Logging | [ASSUMPTION: 400 days - verify] |
| Error tracking | Sentry | [ASSUMPTION: 90 days - verify plan] |
| Admin Portal actions | GCP Cloud Logging | [ASSUMPTION: 1 year - verify] |

### 4.6 Log Investigation

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
- [ ] Audit logs are retained for at least 400 days
- [ ] Log access is limited to authorized personnel

---

## 6. Evidence Produced

| Evidence Type | Description | System/Tool | Retention | Collection | Owner |
|--------------|-------------|-------------|-----------|------------|-------|
| Application logs | Runtime events, requests, errors | GCP Cloud Logging | [ASSUMPTION: 30 days] | Automatic | Engineering |
| GCP Audit Logs | IAM changes, resource access | GCP Cloud Logging | 400 days | Automatic | CTO |
| Error reports | Exceptions with stack traces | Sentry | [ASSUMPTION: 90 days] | Automatic | Engineering |
| Alert history | Fired alerts and responses | GCP Monitoring | [ASSUMPTION: 90 days] | Automatic | Engineering |
| Log access records | Who accessed Log Explorer | GCP Audit Logs | 400 days | Automatic | CTO |
| Sensitive data findings | Detected sensitive data in logs | GCP Sensitive Data Protection | 90 days | Automatic | CTO |

### Evidence Retrieval

- **Application logs:** GCP Console > Logging > Log Explorer > Filter by resource/time
- **Audit logs:** GCP Console > Logging > Log Explorer > Filter by `logName:"cloudaudit.googleapis.com"`
- **Sentry errors:** Sentry dashboard > Filter by project/date
- **Alert history:** GCP Console > Monitoring > Alerting > Incidents
- **Sensitive data findings:** GCP Console > Security > Sensitive Data Protection > Findings

### Evidence Gaps

- **Retention verification:** Confirm actual retention settings in GCP and Sentry match documented values
- **Alert response tracking:** No formal tracking of alert acknowledgment and resolution times

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
