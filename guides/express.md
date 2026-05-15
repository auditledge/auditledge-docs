# Express Guide

Add AuditLedge to an Express app in minutes.

## Install

```bash
npm install auditledge
```

## Set up the client

```javascript
// lib/audit.js
const AuditLedge = require('auditledge');

module.exports = new AuditLedge(process.env.AUDITLEDGE_API_KEY);
```

## Log events in route handlers

```javascript
const express = require('express');
const audit = require('./lib/audit');

const router = express.Router();

router.delete('/invoices/:id', async (req, res) => {
  const { id } = req.params;

  // your delete logic here

  audit.log({
    actor: {
      id: req.user.id,
      name: req.user.name,
      email: req.user.email
    },
    action: 'invoice.deleted',
    resource: { type: 'invoice', id },
    organization_id: req.user.organizationId,
    metadata: { ip: req.ip }
  }).catch(console.error);

  res.json({ success: true });
});
```

## Middleware pattern

For high-volume apps, centralize logging with a middleware:

```javascript
// middleware/audit.js
const audit = require('../lib/audit');

module.exports = function auditLog(action, getResource) {
  return (req, res, next) => {
    res.on('finish', () => {
      if (res.statusCode < 400) {
        audit.log({
          actor: { id: req.user.id, name: req.user.name },
          action,
          resource: getResource(req),
          organization_id: req.user.organizationId,
          metadata: { ip: req.ip, status: res.statusCode }
        }).catch(console.error);
      }
    });
    next();
  };
};
```

```javascript
// Usage
router.delete(
  '/invoices/:id',
  auditLog('invoice.deleted', (req) => ({ type: 'invoice', id: req.params.id })),
  deleteInvoiceHandler
);
```

## Related

- [JavaScript SDK](../sdks/javascript)
- [auditledge-examples on GitHub](https://github.com/auditledge/auditledge-examples)
