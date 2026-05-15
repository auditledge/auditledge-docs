# JavaScript / Node.js SDK

The official JavaScript SDK for AuditLedge. Works in Node.js and any server-side JavaScript runtime.

## Installation

```bash
npm install auditledge
```

## Initialization

```javascript
const AuditLedge = require('auditledge');

const client = new AuditLedge('your_api_key');
```

Store your API key in an environment variable — never commit it to source code:

```javascript
const client = new AuditLedge(process.env.AUDITLEDGE_API_KEY);
```

**Constructor options:**

| Option | Type | Description |
|--------|------|-------------|
| `apiKey` | string | Your AuditLedge API key (required) |
| `options.baseUrl` | string | Override the default API endpoint (optional) |

## Logging Events

```javascript
await client.log({
  actor: {
    id: 'user_123',
    name: 'Alice',
    email: 'alice@example.com'
  },
  action: 'invoice.deleted',
  resource: {
    type: 'invoice',
    id: 'inv_456',
    name: 'Invoice #1042'
  },
  organization_id: 'org_789',
  metadata: { ip: '192.168.1.1' }
});
```

**Required fields:** `actor.id`, `action`, `resource.type`, `resource.id`

**Fire and forget (recommended for most cases):**

```javascript
client.log({ ... }).catch(console.error);
```

**Await confirmation (when you need the event ID back):**

```javascript
const event = await client.log({ ... });
console.log(event.id);
```

## Querying Events

```javascript
const events = await client.query({
  organization_id: 'org_789',
  actor_id: 'user_123',
  action: 'invoice.deleted',
  from: '2026-01-01T00:00:00Z',
  to: '2026-12-31T23:59:59Z',
  limit: 50,
  offset: 0
});
```

**Filter options:**

| Option | Type | Description |
|--------|------|-------------|
| `organization_id` | string | Filter by organization |
| `actor_id` | string | Filter by actor |
| `action` | string | Filter by action name |
| `from` | ISO 8601 string | Start of time range |
| `to` | ISO 8601 string | End of time range |
| `limit` | number | Results per page (default 50) |
| `offset` | number | Pagination offset |

## Retrieving a Single Event

```javascript
const event = await client.get('evt_abc123');
```

## Exporting Events

```javascript
const csv = await client.export({
  format: 'csv',
  organization_id: 'org_789'
});

const json = await client.export({
  format: 'json',
  organization_id: 'org_789'
});
```

**Supported formats:** `csv`, `json`

## Action Naming Convention

Use dot notation: `resource.action`

```
user.created         user.updated         user.deleted
invoice.sent         invoice.paid         invoice.voided
subscription.started subscription.upgraded subscription.cancelled
api_key.created      api_key.revoked
```

## Related

- [npm package](https://npmjs.com/package/auditledge)
- [GitHub repo](https://github.com/auditledge/auditledge-js)
- [Express guide](../guides/express)
- [Next.js guide](../guides/nextjs)
