# Quickstart

Add a production-grade audit log to your SaaS in under 5 minutes.

## 1. Create an account

Go to [auditledge.com](https://auditledge.com) and sign up for free. No credit card required.

## 2. Get your API key

From your dashboard, copy your API key.

## 3. Install the SDK

```bash
# JavaScript / Node.js
npm install auditledge

# Python
pip install auditledge

# PHP
composer require auditledge/auditledge-php

# Ruby
gem install auditledge

# Go
go get github.com/auditledge/auditledge-go
```

## 4. Log your first event

```javascript
const AuditLedge = require('auditledge');
const client = new AuditLedge('your_api_key');

await client.log({
  actor: { id: 'user_123', name: 'Alice' },
  action: 'invoice.deleted',
  resource: { type: 'invoice', id: 'inv_456' },
  organization_id: 'org_789'
});
```

That's it. Your audit event is stored, queryable, and retained for the duration of your plan.

## 5. View your events

Log in to your [Auditledge dashboard](https://auditledge.com/dashboard) to browse, filter, and export your audit events.

## Next steps

- [API Reference](../api-reference/events-log.md)
- [Event Schema](../concepts/event-schema.md)
- [Framework guides](../guides/)
- [Pricing](https://auditledge.com/pricing)
