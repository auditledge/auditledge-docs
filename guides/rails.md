# Rails Guide

Add AuditLedge to a Rails app in minutes.

## Install

Add to your `Gemfile`:

```ruby
gem 'auditledge'
```

```bash
bundle install
```

## Set up the client

Create an initializer so the client is shared across the app:

```ruby
# config/initializers/auditledge.rb
AUDIT = Auditledge::Client.new(ENV['AUDITLEDGE_API_KEY'])
```

Add your key to `.env` (using `dotenv-rails` or Rails credentials):

```
AUDITLEDGE_API_KEY=al_your_key_here
```

## Log events in controllers

```ruby
# app/controllers/invoices_controller.rb
class InvoicesController < ApplicationController
  def destroy
    @invoice = current_user.invoices.find(params[:id])
    @invoice.destroy

    AUDIT.log({
      actor: {
        id: current_user.id.to_s,
        name: current_user.name,
        email: current_user.email
      },
      action: 'invoice.deleted',
      resource: {
        type: 'invoice',
        id: @invoice.id.to_s,
        name: @invoice.number
      },
      organization_id: current_user.organization_id.to_s,
      metadata: { ip: request.remote_ip }
    })

    redirect_to invoices_path, notice: 'Invoice deleted.'
  end
end
```

## Concern pattern

Extract audit logging into a reusable concern:

```ruby
# app/controllers/concerns/auditable.rb
module Auditable
  def audit(action, resource)
    AUDIT.log({
      actor: { id: current_user.id.to_s, name: current_user.name },
      action: action,
      resource: resource,
      organization_id: current_user.organization_id.to_s,
      metadata: { ip: request.remote_ip }
    })
  end
end
```

```ruby
class InvoicesController < ApplicationController
  include Auditable

  def destroy
    @invoice.destroy
    audit('invoice.deleted', { type: 'invoice', id: @invoice.id.to_s })
    redirect_to invoices_path
  end
end
```

## ActiveRecord callback pattern

For model-level logging:

```ruby
# app/models/invoice.rb
after_destroy do |invoice|
  AUDIT.log({
    actor: { id: Current.user&.id.to_s },
    action: 'invoice.deleted',
    resource: { type: 'invoice', id: id.to_s },
    organization_id: organization_id.to_s
  })
end
```

## Related

- [Ruby SDK](../sdks/ruby)
- [auditledge-examples on GitHub](https://github.com/auditledge/auditledge-examples)
