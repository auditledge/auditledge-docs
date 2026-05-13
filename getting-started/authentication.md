# Authentication

Every request to the Auditledge API must include an API key.

---

## API keys

API keys are generated per account in the [Dashboard → API Keys](https://auditledge.com/dashboard/keys) page. Each key:

- Starts with the prefix `al_`
- Is shown **once** at creation — copy it immediately
- Is stored as a SHA-256 hash on our servers (the raw key is never retained)
- Is scoped to your organization — it can only read and write events belonging to your `organization_id`

## Sending the key

Pass the key as a `Bearer` token in the `Authorization` header on every request:

```bash
Authorization: Bearer al_your_key_here
```

Full example:

```bash
curl https://api.auditledge.com/v1/events \
  -H "Authorization: Bearer al_your_key_here"
```

## Error responses

| Status | Meaning |
|--------|---------|
| `401 Missing or invalid authorization header` | No `Authorization` header was sent, or it did not start with `Bearer ` |
| `401 Invalid API key` | The key does not exist or has been revoked |

## Rotating a key

1. Go to [Dashboard → API Keys](https://auditledge.com/dashboard/keys)
2. Create a new key and deploy it to your application
3. Revoke the old key — it stops working immediately

There is no grace period after revocation. Rotate keys before revoking.

## Security recommendations

- Store the key in an environment variable, never in source code
- Use a separate key per environment (production, staging)
- Rotate keys whenever a team member with access leaves
