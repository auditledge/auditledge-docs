# PHP SDK

The official PHP SDK for AuditLedge.

## Installation

```bash
composer require auditledge/auditledge-php
```

## Initialization

```php
use Auditledge\AuditLedge;

$client = new AuditLedge('your_api_key');
```

Store your API key in an environment variable:

```php
$client = new AuditLedge($_ENV['AUDITLEDGE_API_KEY']);
```

## Logging Events

```php
$client->log([
    'actor' => [
        'id' => 'user_123',
        'name' => 'Alice',
        'email' => 'alice@example.com'
    ],
    'action' => 'invoice.deleted',
    'resource' => [
        'type' => 'invoice',
        'id' => 'inv_456',
        'name' => 'Invoice #1042'
    ],
    'organization_id' => 'org_789',
    'metadata' => ['ip' => $_SERVER['REMOTE_ADDR']]
]);
```

**Required fields:** `actor.id`, `action`, `resource.type`, `resource.id`

## Querying Events

```php
$events = $client->query([
    'organization_id' => 'org_789',
    'actor_id' => 'user_123',
    'action' => 'invoice.deleted',
    'from' => '2026-01-01T00:00:00Z',
    'limit' => 50,
    'offset' => 0
]);
```

## Exporting Events

```php
$csv = $client->export([
    'format' => 'csv',
    'organization_id' => 'org_789'
]);

$json = $client->export([
    'format' => 'json',
    'organization_id' => 'org_789'
]);
```

**Supported formats:** `csv`, `json`

## Laravel Example

```php
public function destroy(Invoice $invoice)
{
    $invoice->delete();

    app('auditledge')->log([
        'actor' => [
            'id' => (string) auth()->id(),
            'name' => auth()->user()->name
        ],
        'action' => 'invoice.deleted',
        'resource' => ['type' => 'invoice', 'id' => (string) $invoice->id],
        'organization_id' => (string) auth()->user()->organization_id,
        'metadata' => ['ip' => request()->ip()]
    ]);

    return redirect()->route('invoices.index');
}
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

- [Packagist](https://packagist.org/packages/auditledge/auditledge-php)
- [GitHub repo](https://github.com/auditledge/auditledge-php)
- [Laravel guide](../guides/laravel)
