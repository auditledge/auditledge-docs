# Retention

Auditledge automatically deletes events older than your plan's retention window.

---

## Retention by plan

| Plan | Retention | Events/month | Price |
|------|-----------|-------------|-------|
| Free | 14 days | 10,000 | $0 |
| Starter | 1 year (365 days) | 500,000 | $19/month |
| Growth | 2 years (730 days) | 5,000,000 | $49/month |
| Pro | Custom | Unlimited | Contact us |

Upgrade at [auditledge.com/#pricing](https://auditledge.com/#pricing).

## How retention works

- A cleanup job runs daily and deletes events with a `timestamp` older than your plan's cutoff
- Retention is per-organization — each org is checked against the plan of the account that owns its API key
- Deletions are permanent and unrecoverable

## Checking your retention window

```bash
curl https://api.auditledge.com/v1/usage \
  -H "Authorization: Bearer al_your_key_here"
```

```json
{
  "plan": "starter",
  "events_this_month": 12450,
  "events_limit": 500000,
  "retention_days": 365
}
```

## Compliance requirements

| Framework | Minimum retention |
|-----------|-----------------|
| SOC 2 | 1 year |
| ISO 27001 | 1–3 years (varies) |
| PCI DSS | 1 year (3 months immediately accessible) |
| HIPAA | 6 years |
| GDPR | As long as necessary |

**Starter** (1 year) meets SOC 2 minimums.
**Growth** (2 years) exceeds SOC 2 and most ISO 27001 requirements.
**Pro** supports custom retention for HIPAA or other regulated industries.

## Export before downgrading

If you downgrade, events outside the new retention window are deleted on the next cleanup run. Export first:

```bash
curl "https://api.auditledge.com/v1/events/export?format=json" \
  -H "Authorization: Bearer al_your_key_here" \
  -o full_export.json
```
