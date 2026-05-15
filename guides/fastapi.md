# FastAPI Guide

Add AuditLedge to a FastAPI app in minutes.

## Install

```bash
pip install auditledge
```

## Set up the client

```python
# app/audit.py
import os
from auditledge import AuditLedge

audit = AuditLedge(os.environ['AUDITLEDGE_API_KEY'])
```

## Log events in route handlers

```python
from fastapi import APIRouter, Depends
from app.audit import audit
from app.auth import get_current_user

router = APIRouter()

@router.delete('/invoices/{invoice_id}')
async def delete_invoice(invoice_id: str, current_user=Depends(get_current_user)):
    # your delete logic here

    audit.log({
        'actor': {
            'id': str(current_user.id),
            'name': current_user.name,
            'email': current_user.email
        },
        'action': 'invoice.deleted',
        'resource': {'type': 'invoice', 'id': invoice_id},
        'organization_id': str(current_user.organization_id)
    })

    return {'success': True}
```

## Background task pattern (non-blocking)

Use FastAPI's `BackgroundTasks` to avoid blocking the response:

```python
from fastapi import BackgroundTasks

@router.delete('/invoices/{invoice_id}')
async def delete_invoice(
    invoice_id: str,
    background_tasks: BackgroundTasks,
    current_user=Depends(get_current_user)
):
    # your delete logic here

    background_tasks.add_task(audit.log, {
        'actor': {'id': str(current_user.id), 'name': current_user.name},
        'action': 'invoice.deleted',
        'resource': {'type': 'invoice', 'id': invoice_id},
        'organization_id': str(current_user.organization_id)
    })

    return {'success': True}
```

## Dependency injection pattern

Share the client across your app using FastAPI's dependency system:

```python
# app/dependencies.py
from functools import lru_cache
from auditledge import AuditLedge
import os

@lru_cache
def get_audit_client() -> AuditLedge:
    return AuditLedge(os.environ['AUDITLEDGE_API_KEY'])
```

```python
@router.delete('/invoices/{invoice_id}')
async def delete_invoice(
    invoice_id: str,
    current_user=Depends(get_current_user),
    audit=Depends(get_audit_client)
):
    # your delete logic here
    audit.log({ ... })
    return {'success': True}
```

## Related

- [Python SDK](../sdks/python)
- [auditledge-examples on GitHub](https://github.com/auditledge/auditledge-examples)
