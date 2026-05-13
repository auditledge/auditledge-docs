# What is an audit log?

An audit log is an immutable, chronological record of "who did what, and when" inside your application.

---

## Why SaaS products need one

Business customers ask for audit logs constantly:

- "Who deleted that record?"
- "Who changed the billing plan?"
- "Who invited that user last week?"

It is both a trust feature and a compliance requirement. SOC 2, HIPAA, and GDPR all mandate some form of audit trail, and enterprise buyers will block a deal until it exists.

## What a good audit event captures

| Field | Example | Purpose |
|-------|---------|---------|
| **Actor** | `Alice (user_123)` | Who did it |
| **Action** | `invoice.deleted` | What happened |
| **Resource** | `Invoice #1042 (inv_456)` | What was affected |
| **Timestamp** | `2026-05-13T10:23:00Z` | When it happened |
| **Organization** | `org_789` | Which tenant (for multi-tenant SaaS) |
| **Metadata** | `{ "ip": "203.0.113.42" }` | Additional context |

## Why it is hard to build yourself

Storing rows in a Postgres table is easy. Making it production-grade is not:

- **Async ingestion** — logging must never block your main app thread
- **Zero data loss** — a missing audit event is a compliance failure, not a bug
- **Queryability at scale** — searching millions of events by actor, action, or date must stay fast
- **Retention management** — old events need automatic expiry per-tenant, per-plan
- **Immutability** — events must not be editable after creation
- **Ongoing maintenance** — it is never truly done; requirements keep expanding

Developers who have built audit logs report spending days to a month on the initial build, then continuing to patch it afterwards.

## How Auditledge handles it

You send one `POST` request per event. Auditledge handles storage, querying, retention, and the dashboard your team uses to investigate activity. No infrastructure to manage, no schema to maintain.

```bash
curl -X POST https://api.auditledge.com/v1/events \
  -H "Authorization: Bearer al_your_key_here" \
  -H "Content-Type: application/json" \
  -d '{
    "actor":    { "id": "user_123", "name": "Alice" },
    "action":   "invoice.deleted",
    "resource": { "type": "invoice", "id": "inv_456" },
    "organization_id": "org_789"
  }'
```

→ [Quickstart](../getting-started/quickstart.md)
→ [Event schema](event-schema.md)
