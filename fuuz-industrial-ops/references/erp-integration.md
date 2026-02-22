# ERP Integration Patterns

## General Architecture

```
Fuuz Flow (integrateV2 node) → Connector → External API
                              ↓
                    Configured in: Configuration → Connectors
                    Auth managed by: Connector credentials
```

## Connector Configuration

Connectors are configured per-tenant in **Configuration → Connectors**. Each connector stores:
- Endpoint URL / base URL
- Authentication credentials (API keys, OAuth tokens, username/password)
- Custom headers
- Timeout settings

## Integration Flow Pattern

```
Schedule/DataChanges →
Query Fuuz Data →
Transform to External Format →
IntegrateV2 (API call) →
Transform Response →
Upsert Fuuz Records (with external IDs) →
Log Integration Result
```

## Key Principles

1. **Never hardcode credentials** — Always use configured connectors
2. **Map external IDs** — Store ERP record IDs in Fuuz for reconciliation:
   ```json
   { "externalId": "PLX-12345", "erpSource": "plex", "lastSyncAt": "2026-01-01T00:00:00Z" }
   ```
3. **Use upsert for sync operations** — Prevents duplicates on re-runs:
   ```jsonata
   {
     "payload": $.records.{
       "where": { "externalId": externalId },
       "create": $,
       "update": $
     }
   }
   ```
4. **Batch API calls** — ERP systems have rate limits. Process in batches of 50-100.
5. **Create audit logs** — Log every external API call for debugging and compliance
6. **Handle errors gracefully** — Use try-catch patterns (unless/when guards) for API failures

## Plex Integration

### Plex Connectors Available
- **Plex API** — Modern REST API
- **Plex Classic** — Legacy SOAP API
- **Plex UX** — UX-specific endpoints
- **Plex IAM** — Identity/authentication

### Common Plex Flows
| Flow | Direction | Trigger | Description |
|---|---|---|---|
| Sync Work Orders | Plex → Fuuz | Schedule (15 min) | Pull open work orders |
| Report Production | Fuuz → Plex | DataChanges (ProductionLog) | Push production counts |
| Sync Inventory | Bidirectional | Schedule (hourly) | Reconcile inventory levels |
| Master Data Sync | Plex → Fuuz | Schedule (daily) | Products, BOMs, routings |

## NetSuite Integration

### NetSuite Connectors Available
- **NetsuiteSOAP** — SOAP web services
- **NetsuiteREST** — RESTlet endpoints

### Common NetSuite Flows
| Flow | Direction | Trigger | Description |
|---|---|---|---|
| Sync Sales Orders | NetSuite → Fuuz | Schedule (15 min) | Pull new sales orders |
| Update Fulfillment | Fuuz → NetSuite | DataChanges (Shipment) | Push shipment status |
| Inventory Adjustment | Fuuz → NetSuite | DataChanges (InventoryTransaction) | Push inventory changes |

## SAP Integration

### SAP Connectors Available
- **SAP Cloud** — Cloud Platform Integration
- **SAP Success Factors** — HR module
- **SAP RFC** (via Gateway) — On-premise SAP via Remote Function Calls

### Gateway RFC Pattern
For on-premise SAP systems, use the Fuuz Gateway with SAP RFC driver:

```
Gateway Flow:
DataChanges → Transform to RFC payload →
Device Function (SAP RFC call) →
Transform Response → Mutate Fuuz
```

## Integration Logging Pattern

Create an `IntegrationLog` model to track all external API calls:

```json
{
  "modelName": "IntegrationLog",
  "fields": [
    { "name": "id", "type": "ID!" },
    { "name": "connectorId", "type": "String!" },
    { "name": "direction", "type": "String! (inbound|outbound)" },
    { "name": "endpoint", "type": "String!" },
    { "name": "requestPayload", "type": "JSON" },
    { "name": "responsePayload", "type": "JSON" },
    { "name": "statusCode", "type": "Int" },
    { "name": "success", "type": "Boolean!" },
    { "name": "errorMessage", "type": "String" },
    { "name": "executionTimeMs", "type": "Int" },
    { "name": "recordCount", "type": "Int" }
  ]
}
```

## Error Handling

### Retry Pattern
```
IntegrateV2 → Guard (check success) →
  [Success path] → Process results
  [Failure path] → Log error → Wait (30s) → Retry (max 3)
```

### Common ERP API Errors

| Error | Cause | Solution |
|---|---|---|
| 401 Unauthorized | Expired token/credentials | Refresh connector credentials |
| 429 Too Many Requests | Rate limit exceeded | Add delays between batches, reduce batch size |
| 500 Internal Server Error | ERP system issue | Retry with exponential backoff |
| Timeout | Large payload or slow response | Reduce batch size, increase timeout |
| Connection refused | Network/firewall issue | Check Gateway connectivity for on-prem systems |

## Testing Best Practices

1. **Use sandbox environments** — Never develop against production ERP
2. **Start with read operations** — Test queries before mutations
3. **Validate data mapping** — Log transformed payloads before sending
4. **Test edge cases** — Empty results, special characters, large payloads
5. **Monitor rate limits** — Track API call counts per interval
