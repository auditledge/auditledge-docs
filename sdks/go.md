# Go SDK

The official Go SDK for AuditLedge.

## Installation

```bash
go get github.com/auditledge/auditledge-go
```

## Initialization

```go
import "github.com/auditledge/auditledge-go"

client, err := auditledge.New("your_api_key")
if err != nil {
    log.Fatal(err)
}
```

Store your API key in an environment variable:

```go
client, err := auditledge.New(os.Getenv("AUDITLEDGE_API_KEY"))
```

## Logging Events

```go
err := client.Log(auditledge.Event{
    Actor: auditledge.Actor{
        ID:    "user_123",
        Name:  "Alice",
        Email: "alice@example.com",
    },
    Action: "invoice.deleted",
    Resource: &auditledge.Resource{
        Type: "invoice",
        ID:   "inv_456",
        Name: "Invoice #1042",
    },
    OrganizationID: "org_789",
    Metadata: map[string]interface{}{
        "ip": "192.168.1.1",
    },
})
```

**Required fields:** `Actor.ID`, `Action`, `Resource.Type`, `Resource.ID`

## Querying Events

```go
events, err := client.Query(auditledge.QueryFilters{
    OrganizationID: "org_789",
    ActorID:        "user_123",
    Action:         "invoice.deleted",
    From:           "2026-01-01T00:00:00Z",
    Limit:          50,
    Offset:         0,
})
```

## HTTP Handler Example

```go
func deleteInvoiceHandler(w http.ResponseWriter, r *http.Request) {
    invoiceID := r.PathValue("id")

    // delete invoice logic...

    audit.Log(auditledge.Event{
        Actor:          auditledge.Actor{ID: userIDFromContext(r.Context())},
        Action:         "invoice.deleted",
        Resource:       &auditledge.Resource{Type: "invoice", ID: invoiceID},
        OrganizationID: orgIDFromContext(r.Context()),
        Metadata:       map[string]interface{}{"ip": r.RemoteAddr},
    })

    w.WriteHeader(http.StatusNoContent)
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

- [GitHub repo](https://github.com/auditledge/auditledge-go)
- [pkg.go.dev](https://pkg.go.dev/github.com/auditledge/auditledge-go)
