# Python SDK

The official Python SDK for AuditLedge.

## Installation

```bash
pip install auditledge
```

## Initialization

```python
from auditledge import AuditLedge

client = AuditLedge('your_api_key')
```

Store your API key in an environment variable:

```python
import os
from auditledge import AuditLedge

client = AuditLedge(os.environ['AUDITLEDGE_API_KEY'])
```

## Logging Events

```python
client.log({
    'actor': {
        'id': 'user_123',
        'name': 'Alice',
        'email': 'alice@example.com'
    },
    'action': 'invoice.deleted',
    'resource': {
        'type': 'invoice',
        'id': 'inv_456',
        'name': 'Invoice #1042'
    },
    'organization_id': 'org_789',
    'metadata': {'ip': '192.168.1.1'}
})
```

**Required fields:** `actor.id`, `action`, `resource.type`, `resource.id`

## Querying Events

```python
events = client.query({
    'organization_id': 'org_789',
    'actor_id': 'user_123',
    'action': 'invoice.deleted',
    'from': '2026-01-01T00:00:00Z',
    'to': '2026-12-31T23:59:59Z',
    'limit': 50,
    'offset': 0
})
```

## Exporting Events

```python
csv_data = client.export({
    'format': 'csv',
    'organization_id': 'org_789'
})

json_data = client.export({
    'format': 'json',
    'organization_id': 'org_789'
})
```

**Supported formats:** `csv`, `json`

## Django Example

```python
def delete_invoice(request, invoice_id):
    invoice = Invoice.objects.get(id=invoice_id)
    invoice.delete()

    audit.log({
        'actor': {
            'id': str(request.user.id),
            'name': request.user.get_full_name()
        },
        'action': 'invoice.deleted',
        'resource': {'type': 'invoice', 'id': str(invoice_id)},
        'organization_id': str(request.user.organization_id),
        'metadata': {'ip': request.META.get('REMOTE_ADDR')}
    })
```

## Action Naming Convention

Use dot notation: `resource.action`

```
user.created         user.updated         user.deleted
invoice.sent         invoice.paid         invoice.voided
subscription.started subscription.upgraded subscription.cancelled
api_key.created      api_key.revoked
```

## Related

- [PyPI package](https://pypi.org/project/auditledge)
- [GitHub repo](https://github.com/auditledge/auditledge-python)
- [FastAPI guide](../guides/fastapi)
