# Next.js Guide

Add AuditLedge to a Next.js app in minutes.

## Install

```bash
npm install auditledge
```

## Set up the client

Create a shared client module so you don't reinstantiate on every request:

```javascript
// lib/audit.js
const AuditLedge = require('auditledge');

const audit = new AuditLedge(process.env.AUDITLEDGE_API_KEY);

module.exports = audit;
```

Add your key to `.env.local` (never commit this file):

```
AUDITLEDGE_API_KEY=al_your_key_here
```

## Log events from API routes

```javascript
// app/api/invoices/[id]/route.js
import { NextResponse } from 'next/server';
import audit from '@/lib/audit';

export async function DELETE(request, { params }) {
  const { id } = params;

  // your delete logic here

  audit.log({
    actor: {
      id: session.user.id,
      name: session.user.name,
      email: session.user.email
    },
    action: 'invoice.deleted',
    resource: { type: 'invoice', id },
    organization_id: session.user.organizationId,
    metadata: {
      ip: request.headers.get('x-forwarded-for') || request.ip
    }
  }).catch(console.error);

  return NextResponse.json({ success: true });
}
```

## Log events from Server Actions

```javascript
// app/actions/invoices.js
'use server';

import audit from '@/lib/audit';
import { auth } from '@/lib/auth';

export async function deleteInvoice(invoiceId) {
  const session = await auth();

  // your delete logic here

  audit.log({
    actor: { id: session.user.id, name: session.user.name },
    action: 'invoice.deleted',
    resource: { type: 'invoice', id: invoiceId },
    organization_id: session.user.organizationId
  }).catch(console.error);
}
```

## Fire and forget

Use `.catch(console.error)` instead of `await` so audit logging never blocks your response.

## Related

- [JavaScript SDK](../sdks/javascript)
- [auditledge-examples on GitHub](https://github.com/auditledge/auditledge-examples)
