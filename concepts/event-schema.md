# Event schema

Every audit event stored in Auditledge follows the same structure.

---

## Full schema

```json
{
  "id":            "uuid",
  "timestamp":     "ISO 8601 string",
  "actor_id":      "string",
  "actor_name":    "string | null",
  "actor_email":   "string | null",
  "action":        "string",
  "resource_type": "string",
  "resource_id":   "string",
  "resource_name": "string | null",
  "metadata":      "object | null",
  "organization_id": "string",
  "api_key_id":    "uuid"
}
```

## Field reference

### `id`
UUID assigned by Auditledge at ingestion time. Globally unique. Use this to retrieve a specific event via `GET /v1/events/:id`.

### `timestamp`
ISO 8601 datetime in UTC. Defaults to the time the request reaches the API if not supplied. Can be backdated for historical imports.

### `actor_id` (required)
Your internal ID for the user who performed the action. We do not generate or manage user identities.

### `actor_name`
Human-readable display name. Shown in the dashboard and CSV exports. Optional but strongly recommended.

### `actor_email`
Email address of the actor. Shown below `actor_name` in the dashboard. Optional.

### `action` (required)
What happened. We recommend dot notation — `noun.verb`:

```
invoice.created     invoice.updated     invoice.deleted
user.invited        user.role_changed   user.removed
project.archived    billing.plan_upgraded
```

Indexed for partial-match filtering — `?action=invoice` returns all invoice events.

### `resource_type` (required)
The category of thing that was affected. Examples: `invoice`, `user`, `project`, `document`, `api_key`.

### `resource_id` (required)
The unique ID of the specific resource that was affected.

### `resource_name`
Human-readable name of the resource. Shown in the dashboard. Optional but recommended.

### `metadata`
Any JSON object. No enforced schema — store whatever context is useful.

Common patterns:

```json
// IP and browser info
{ "ip": "203.0.113.42", "user_agent": "Mozilla/5.0..." }

// Field change — rendered as a diff in the dashboard
{ "field": "status", "old_value": "active", "new_value": "suspended" }

// Bulk action context
{ "count": 47, "filter": "unpaid" }
```

When `metadata` contains `old_value` and `new_value`, the dashboard renders a highlighted Changes section (red for old, green for new) with an optional `field` label.

### `organization_id`
The tenant this event belongs to. Defaults to the organization scoped to the API key.

### `api_key_id`
The key that ingested this event. Read-only — set by the server.

---

## Ingestion payload vs stored event

When ingesting, you send a nested structure:

```json
{
  "actor":    { "id": "...", "name": "...", "email": "..." },
  "action":   "...",
  "resource": { "type": "...", "id": "...", "name": "..." },
  "metadata": {}
}
```

The stored event (returned by all `GET` endpoints) is flattened:

```json
{
  "actor_id":      "...",
  "actor_name":    "...",
  "actor_email":   "...",
  "action":        "...",
  "resource_type": "...",
  "resource_id":   "...",
  "resource_name": "...",
  "metadata":      {}
}
```
