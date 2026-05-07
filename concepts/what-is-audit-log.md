# What is an Audit Log?

An audit log is an immutable, chronological record of events in your application — capturing **who did what, and when**.

## Why your SaaS needs an audit log

Every SaaS product that sells to businesses will eventually be asked:

- "Who deleted that record?"
- "Who changed the billing plan?"
- "Who invited that user last Tuesday?"
- "Can I see all activity for my account?"

Enterprise customers expect this. Compliance frameworks (SOC 2, HIPAA, GDPR) often require it. And once your first enterprise customer asks for it, you need it immediately.

## What a good audit log captures

Each event should record:

| Field | Description | Example |
|-------|-------------|---------|
| `actor` | Who performed the action | `{ id: "user_123", name: "Alice" }` |
| `action` | What they did | `invoice.deleted` |
| `resource` | What was affected | `{ type: "invoice", id: "inv_456" }` |
| `timestamp` | When it happened | `2026-04-14T10:23:00Z` |
| `organization_id` | Which tenant | `org_789` |
| `metadata` | Additional context | `{ ip: "192.168.1.1" }` |

## The hard part isn't storing logs

Storing audit events in a database table is straightforward. The hard parts are:

- **Async logging** — never blocking your main application thread
- **Zero data loss** — a lost audit event can be a compliance failure
- **Queryability at scale** — filtering millions of events by actor, action, date range
- **Retention management** — automatically expiring old events per your policy
- **Immutability** — ensuring events can't be tampered with

## Why use Auditledge instead of building it yourself

Developers who have built audit logs in-house report spending **days to months** on initial implementation — and the work is never truly done. Every new compliance requirement or scale milestone means revisiting the implementation.

Auditledge handles all of this for you:

```javascript
await client.log({
  actor: { id: 'user_123', name: 'Alice' },
  action: 'invoice.deleted',
  resource: { type: 'invoice', id: 'inv_456' },
  organization_id: 'org_789'
});
```

One API call. That's it.

## Related

- [Quickstart](../getting-started/quickstart.md)
- [Event Schema](./event-schema.md)
- [Retention Policy](./retention.md)
