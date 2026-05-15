# Ruby SDK

The official Ruby SDK for AuditLedge.

## Installation

```bash
gem install auditledge
```

Or add to your `Gemfile`:

```ruby
gem 'auditledge'
```

Then run:

```bash
bundle install
```

## Initialization

```ruby
require 'auditledge'

client = Auditledge::Client.new('your_api_key')
```

Store your API key in an environment variable:

```ruby
client = Auditledge::Client.new(ENV['AUDITLEDGE_API_KEY'])
```

## Logging Events

```ruby
client.log({
  actor: { id: 'user_123', name: 'Alice', email: 'alice@example.com' },
  action: 'invoice.deleted',
  resource: { type: 'invoice', id: 'inv_456', name: 'Invoice #1042' },
  organization_id: 'org_789',
  metadata: { ip: '192.168.1.1' }
})
```

**Required fields:** `actor[:id]`, `action`, `resource[:type]`, `resource[:id]`

## Querying Events

```ruby
events = client.query(
  organization_id: 'org_789',
  actor_id: 'user_123',
  action: 'invoice.deleted',
  from: '2026-01-01T00:00:00Z',
  limit: 50,
  offset: 0
)
```

## Rails Example

Set up a shared client in an initializer:

```ruby
# config/initializers/auditledge.rb
AUDIT = Auditledge::Client.new(ENV['AUDITLEDGE_API_KEY'])
```

Use it in any controller:

```ruby
def destroy
  @invoice.destroy

  AUDIT.log({
    actor: { id: current_user.id.to_s, name: current_user.name },
    action: 'invoice.deleted',
    resource: { type: 'invoice', id: @invoice.id.to_s },
    organization_id: current_user.organization_id.to_s,
    metadata: { ip: request.remote_ip }
  })

  redirect_to invoices_path
end
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

- [RubyGems](https://rubygems.org/gems/auditledge)
- [GitHub repo](https://github.com/auditledge/auditledge-ruby)
- [Rails guide](../guides/rails)
