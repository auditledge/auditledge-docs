# GET /v1/events

Query and paginate your audit events.

---

## Request

```
GET https://api.auditledge.com/v1/events
Authorization: Bearer <api_key>
```

## Query parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `limit` | integer | `20` | Number of events to return |
| `offset` | integer | `0` | Number of events to skip (for pagination) |
| `search` | string | — | Searches across `action`, `actor_name`, and `resource_type` (case-insensitive partial match) |
| `actor_id` | string | — | Exact actor ID match |
| `actor_name` | string | — | Case-insensitive partial name match |
| `action` | string | — | Case-insensitive partial match — `invoice` matches `invoice.created` and `invoice.deleted` |
| `resource_type` | string | — | Exact resource type match |
| `organization_id` | string | key's org | Override the organization to query |
| `start_date` | ISO 8601 | — | Events at or after this timestamp |
| `end_date` | ISO 8601 | — | Events at or before this timestamp |

Events are always returned **newest first**.

## Example — filter by action and date range

```bash
curl "https://api.auditledge.com/v1/events?action=invoice&start_date=2026-05-01&end_date=2026-05-31T23:59:59Z&limit=50" \
  -H "Authorization: Bearer al_your_key_here"
```

## Example — paginate through all results

```bash
# Page 1
curl "https://api.auditledge.com/v1/events?limit=20&offset=0" \
  -H "Authorization: Bearer al_your_key_here"

# Page 2
curl "https://api.auditledge.com/v1/events?limit=20&offset=20" \
  -H "Authorization: Bearer al_your_key_here"
```

## Response

```json
{
  "events": [
    {
      "id": "a1b2c3d4-...",
      "timestamp": "2026-05-13T10:23:00.000Z",
      "actor_id": "user_123",
      "actor_name": "Alice",
      "actor_email": "alice@acme.com",
      "action": "invoice.deleted",
      "resource_type": "invoice",
      "resource_id": "inv_456",
      "resource_name": "Invoice #1042",
      "metadata": {},
      "organization_id": "org_789"
    }
  ],
  "total": 284,
  "limit": 20,
  "offset": 0
}
```

| Field | Description |
|-------|-------------|
| `events` | Array of event objects for this page |
| `total` | Total matching events — use this to build pagination UI |
| `limit` | The limit applied |
| `offset` | The offset applied |

## Pagination formula

```
total_pages = ceil(total / limit)
next_offset = offset + limit   // if offset + limit < total
```
