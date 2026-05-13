# Sending your first event

This page walks through the `POST /v1/events` endpoint in detail.

---

## Endpoint

```
POST https://api.auditledge.com/v1/events
```

## Required fields

| Field | Type | Description |
|-------|------|-------------|
| `actor.id` | string | Unique identifier for the user who performed the action |
| `action` | string | What happened — use dot notation e.g. `invoice.deleted` |
| `resource.type` | string | The type of thing affected e.g. `invoice`, `user`, `project` |
| `resource.id` | string | The unique ID of the affected resource |

## Optional fields

| Field | Type | Description |
|-------|------|-------------|
| `actor.name` | string | Human-readable name of the actor |
| `actor.email` | string | Email address of the actor |
| `resource.name` | string | Human-readable name of the resource |
| `metadata` | object | Any additional key/value pairs you want to attach |
| `organization_id` | string | Override the org scoped to the API key |
| `timestamp` | string | ISO 8601 datetime — defaults to the time the request is received |

## Minimal example

```bash
curl -X POST https://api.auditledge.com/v1/events \
  -H "Authorization: Bearer al_your_key_here" \
  -H "Content-Type: application/json" \
  -d '{
    "actor":    { "id": "user_123" },
    "action":   "invoice.deleted",
    "resource": { "type": "invoice", "id": "inv_456" }
  }'
```

## Full example

```bash
curl -X POST https://api.auditledge.com/v1/events \
  -H "Authorization: Bearer al_your_key_here" \
  -H "Content-Type: application/json" \
  -d '{
    "actor": {
      "id":    "user_123",
      "name":  "Alice Nguyen",
      "email": "alice@acme.com"
    },
    "action": "user.role_changed",
    "resource": {
      "type": "user",
      "id":   "user_456",
      "name": "Bob Smith"
    },
    "metadata": {
      "field":     "role",
      "old_value": "viewer",
      "new_value": "admin",
      "ip":        "203.0.113.42"
    },
    "organization_id": "org_789",
    "timestamp": "2026-05-13T10:23:00.000Z"
  }'
```

## Success response — 201

```json
{
  "success": true,
  "event": {
    "id": "a1b2c3d4-e5f6-...",
    "timestamp": "2026-05-13T10:23:00.000Z",
    "actor_id": "user_123",
    "actor_name": "Alice Nguyen",
    "actor_email": "alice@acme.com",
    "action": "user.role_changed",
    "resource_type": "user",
    "resource_id": "user_456",
    "resource_name": "Bob Smith",
    "metadata": {
      "field": "role",
      "old_value": "viewer",
      "new_value": "admin",
      "ip": "203.0.113.42"
    },
    "organization_id": "org_789",
    "api_key_id": "key_uuid"
  }
}
```

## Error responses

| Status | Body | Cause |
|--------|------|-------|
| `400` | `Missing required fields` | One or more of `actor.id`, `action`, `resource.type`, `resource.id` is absent |
| `401` | `Invalid API key` | Key is wrong or revoked |
| `429` | `Monthly event limit reached` | You have hit your plan's monthly cap |
| `500` | `Internal server error` | Something went wrong on our end |

## Naming actions

We recommend dot notation with a noun and past-tense verb:

```
invoice.created     invoice.updated     invoice.deleted
user.invited        user.role_changed   user.removed
project.archived    billing.plan_upgraded
```

## Using metadata for changes

If an event represents a field change, structure `metadata` with `field`, `old_value`, and `new_value`. The Auditledge dashboard renders these automatically as a highlighted diff:

```json
"metadata": {
  "field":     "status",
  "old_value": "active",
  "new_value": "suspended"
}
```
