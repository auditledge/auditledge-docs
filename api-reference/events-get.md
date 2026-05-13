# GET /v1/events/:id

Retrieve a single event by its ID.

---

## Request

```
GET https://api.auditledge.com/v1/events/:id
Authorization: Bearer <api_key>
```

| Parameter | Description |
|-----------|-------------|
| `:id` | The UUID of the event (returned by `POST /v1/events` or `GET /v1/events`) |

## Example

```bash
curl "https://api.auditledge.com/v1/events/a1b2c3d4-e5f6-7890-abcd-ef1234567890" \
  -H "Authorization: Bearer al_your_key_here"
```

## Response — 200

```json
{
  "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "timestamp": "2026-05-13T10:23:00.000Z",
  "actor_id": "user_123",
  "actor_name": "Alice",
  "actor_email": "alice@acme.com",
  "action": "invoice.deleted",
  "resource_type": "invoice",
  "resource_id": "inv_456",
  "resource_name": "Invoice #1042",
  "metadata": {
    "ip": "203.0.113.42"
  },
  "organization_id": "org_789",
  "api_key_id": "key_uuid"
}
```

## Error responses

| Status | Description |
|--------|-------------|
| `401` | Missing or invalid API key |
| `404` | Event not found, or belongs to a different organization |
| `500` | Internal server error |

## Note on scoping

Events are scoped to your API key's organization. An event ID that exists in a different organization returns `404` — this prevents leaking information about other organizations' data.
