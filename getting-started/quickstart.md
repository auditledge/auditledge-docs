# Quickstart

Get your first audit event into Auditledge in under 5 minutes.

---

## 1. Create an account

Sign up at [auditledge.com/signup](https://auditledge.com/signup). No credit card required — the free tier includes 10,000 events/month.

## 2. Generate an API key

1. Go to [Dashboard → API Keys](https://auditledge.com/dashboard/keys)
2. Click **New key**, give it a name
3. Copy the key — it starts with `al_` and is shown only once

## 3. Send your first event

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

A `201` response means the event is stored:

```json
{
  "success": true,
  "event": {
    "id": "a1b2c3d4-...",
    "timestamp": "2026-05-13T10:23:00.000Z",
    "actor_id": "user_123",
    "actor_name": "Alice",
    "action": "invoice.deleted",
    "resource_type": "invoice",
    "resource_id": "inv_456",
    "organization_id": "org_789"
  }
}
```

## 4. View it in the dashboard

Open [Dashboard → Events](https://auditledge.com/dashboard), connect your API key, and the event appears immediately. You can filter by actor, action, or date range, and export to CSV or JSON.

---

## Next steps

- [Authentication](authentication.md) — how API keys work and how to rotate them
- [Sending your first event](first-event.md) — full field reference with examples
- [Event schema](../concepts/event-schema.md) — all fields, types, and constraints
- [Query events](../api-reference/events-query.md) — filtering and pagination
