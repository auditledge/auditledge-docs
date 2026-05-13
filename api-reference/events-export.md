# GET /v1/events/export

Export your full audit trail as JSON or CSV.

---

## Request

```
GET https://api.auditledge.com/v1/events/export
Authorization: Bearer <api_key>
```

## Query parameters

Accepts all the same filters as [GET /v1/events](events-query.md), plus:

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `format` | `json` \| `csv` | `json` | Output format |

Unlike the query endpoint, export returns **all matching events** with no pagination.

## JSON export

```bash
curl "https://api.auditledge.com/v1/events/export?format=json" \
  -H "Authorization: Bearer al_your_key_here" \
  -o audit_logs.json
```

Response: a JSON array of event objects.

```json
[
  {
    "id": "a1b2c3d4-...",
    "timestamp": "2026-05-13T10:23:00.000Z",
    "actor_id": "user_123",
    "actor_name": "Alice",
    "action": "invoice.deleted",
    "resource_type": "invoice",
    "resource_id": "inv_456",
    "organization_id": "org_789",
    "metadata": {}
  }
]
```

## CSV export

```bash
curl "https://api.auditledge.com/v1/events/export?format=csv" \
  -H "Authorization: Bearer al_your_key_here" \
  -o audit_logs.csv
```

Response headers:

```
Content-Type: text/csv
Content-Disposition: attachment; filename=audit_logs.csv
```

CSV columns (in order):

```
id, timestamp, actor_id, actor_name, action, resource_type, resource_id, resource_name, organization_id
```

Note: `metadata` is not included in CSV exports. Use JSON format to preserve metadata fields.

## Filtered export example

Export only one actor's events for a date range:

```bash
curl "https://api.auditledge.com/v1/events/export?format=csv&actor_id=user_123&start_date=2026-05-01&end_date=2026-05-31T23:59:59Z" \
  -H "Authorization: Bearer al_your_key_here" \
  -o alice_may_2026.csv
```
