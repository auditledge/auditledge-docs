# Compliance

Audit logs are a requirement — not just a feature — for most compliance frameworks that enterprise SaaS customers operate under.

## Frameworks that require audit logs

### SOC 2 (Type I and II)
The CC7 (Common Criteria) and CC8 control families require logging of system access, data changes, and privileged actions. Auditors will ask for evidence that logs are collected, retained, and tamper-resistant.

### HIPAA
The Security Rule (45 CFR § 164.312(b)) requires covered entities and business associates to implement hardware, software, and procedural mechanisms to record and examine activity in systems containing protected health information (PHI).

### GDPR
Article 5(2) (accountability principle) and Article 30 (records of processing activities) require that you can demonstrate who accessed or modified personal data, and when. Audit logs are a primary evidence source in breach investigations.

### ISO 27001
Annex A control 8.15 (Logging) requires logging of user activities, exceptions, and security events. Logs must be protected against tampering and unauthorized access.

### PCI DSS
Requirement 10 mandates logging all access to system components and cardholder data, with retention of at least 12 months (3 months immediately available).

---

## How AuditLedge satisfies these requirements

| Requirement | How AuditLedge addresses it |
|-------------|----------------------------|
| Who did what | Every event captures `actor_id`, `actor_name`, and `actor_email` |
| What was affected | `resource_type`, `resource_id`, and `resource_name` on every event |
| When it happened | Immutable `timestamp` stored at ingestion; supports backdating for imports |
| Tamper resistance | Events are append-only — stored events cannot be modified or deleted via the API |
| Retention | Configurable per plan; events are retained for the duration of your subscription |
| Export | CSV and JSON export for auditor evidence packages |

---

## What to log for compliance

Compliance auditors typically look for coverage of these action categories:

**Authentication events**
```
user.login        user.logout       user.login_failed
user.mfa_enabled  user.mfa_disabled user.password_changed
```

**Access control events**
```
user.invited      user.role_changed user.removed
api_key.created   api_key.revoked
```

**Data events**
```
record.created    record.updated    record.deleted
record.exported   record.accessed
```

**Billing and admin events**
```
billing.plan_upgraded  billing.plan_cancelled
org.settings_changed   org.sso_enabled
```

---

## Retention by plan

| Plan | Retention |
|------|-----------|
| Free | 14 days |
| Starter | 1 year (365 days) |
| Growth | 2 years (730 days) |
| Pro | Custom |

See [Retention Policy](retention) for details.

---

## Related

- [Retention Policy](retention)
- [Event Schema](event-schema)
- [Export Events](../api-reference/events-export)
