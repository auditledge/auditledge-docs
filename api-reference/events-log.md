# POST /v1/events

Ingest a single audit event.

---

## Request

```
POST https://api.auditledge.com/v1/events
Authorization: Bearer <api_key>
Content-Type: application/json
```

### Body

```json
{
  "actor": {
    "id":    "string (required)",
    "name":  "string (optional)",
    "email": "string (optional)"
  },
  "action": "string (required)",
  "resource": {
    "type": "string (required)",
    "id":   "string (required)",
    "name": "string (optional)"
  },
  "metadata":        "object (optional)",
  "organization_id": "string (optional — defaults to key's org)",
  "timestamp":       "ISO 8601 string (optional — defaults to now)"
}
```

## Response — 201 Created

```json
{
  "success": true,
  "event": {
    "id":            "uuid",
    "timestamp":     "2026-05-13T10:23:00.000Z",
    "actor_id":      "user_123",
    "actor_name":    "Alice",
    "actor_email":   "alice@acme.com",
    "action":        "invoice.deleted",
    "resource_type": "invoice",
    "resource_id":   "inv_456",
    "resource_name": "Invoice #1042",
    "metadata":      {},
    "organization_id": "org_789",
    "api_key_id":    "uuid"
  }
}
```

## Error responses

| Status | Message | Description |
|--------|---------|-------------|
| `400` | `Missing required fields` | `actor.id`, `action`, `resource.type`, or `resource.id` is missing |
| `401` | `Missing or invalid authorization header` | No `Bearer` token in the request |
| `401` | `Invalid API key` | Key does not exist or is revoked |
| `429` | `Monthly event limit reached` | Plan cap hit — upgrade at auditledge.com |
| `500` | `Internal server error` | Server-side failure |

## Notes

- Events are stored immediately and are queryable within milliseconds
- The `timestamp` field can be backdated — useful for importing historical events
- `metadata` accepts any flat or nested JSON object
- A `201` guarantees the event is persisted — safe to fire and forget
