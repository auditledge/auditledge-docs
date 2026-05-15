# Laravel Guide

Add AuditLedge to a Laravel app in minutes.

## Install

```bash
composer require auditledge/auditledge-php
```

## Set up the client

Register AuditLedge as a singleton in a service provider:

```php
// app/Providers/AppServiceProvider.php
use Auditledge\AuditLedge;

public function register(): void
{
    $this->app->singleton('auditledge', function () {
        return new AuditLedge(env('AUDITLEDGE_API_KEY'));
    });
}
```

Add your key to `.env`:

```
AUDITLEDGE_API_KEY=al_your_key_here
```

## Log events in controllers

```php
// app/Http/Controllers/InvoiceController.php
use Illuminate\Http\Request;

class InvoiceController extends Controller
{
    public function destroy(Request $request, Invoice $invoice)
    {
        $invoice->delete();

        app('auditledge')->log([
            'actor' => [
                'id' => (string) auth()->id(),
                'name' => auth()->user()->name,
                'email' => auth()->user()->email
            ],
            'action' => 'invoice.deleted',
            'resource' => [
                'type' => 'invoice',
                'id' => (string) $invoice->id,
                'name' => $invoice->number
            ],
            'organization_id' => (string) auth()->user()->organization_id,
            'metadata' => ['ip' => $request->ip()]
        ]);

        return redirect()->route('invoices.index');
    }
}
```

## Observer pattern

For model-level logging, use a Laravel Observer:

```php
// app/Observers/InvoiceObserver.php
class InvoiceObserver
{
    public function deleted(Invoice $invoice): void
    {
        app('auditledge')->log([
            'actor' => ['id' => (string) auth()->id(), 'name' => auth()->user()->name],
            'action' => 'invoice.deleted',
            'resource' => ['type' => 'invoice', 'id' => (string) $invoice->id],
            'organization_id' => (string) $invoice->organization_id
        ]);
    }
}
```

Register it in a service provider:

```php
Invoice::observe(InvoiceObserver::class);
```

## Related

- [PHP SDK](../sdks/php)
- [auditledge-examples on GitHub](https://github.com/auditledge/auditledge-examples)
