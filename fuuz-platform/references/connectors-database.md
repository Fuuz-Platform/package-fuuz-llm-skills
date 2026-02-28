# Database Connectors

Two connectors for direct database access: Microsoft SQL Server and ODBC.

---

## MSSQL (`bb612514-2129-4fa9-849d-f9f8f6ec1c8e`)

Microsoft SQL Server connector with typed parameters, stored procedure support, connection pooling, and retry.

**Credentials:** `username`, `password`
**Connection Options:** `port`, `database`, `connectionTimeout` (seconds, default 30), `requestTimeout` (seconds, default 30), `encrypt` (boolean, default false)
**Endpoint URL:** SQL Server hostname

**Inbound:** Array of:

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `query` | string | Y | SQL query or stored procedure name |
| `parameters` | array | N | Typed SQL parameters (see below) |
| `procedure` | boolean | N | When `true`, executes as stored procedure via `request.execute()` |
| `options.retry.retries` | integer | N | Max retry attempts (default 0) |
| `options.retry.factor` | integer | N | Exponential backoff factor (default 2) |
| `options.retry.minTimeout` | integer | N | Min retry delay in ms (default 100) |

### Parameter Types

Parameters use `oneOf` with four categories based on which sizing fields apply:

**No sizing parameters:**

| dataType | Description |
|----------|-------------|
| `BigInt`, `Bit`, `Date`, `DateTime`, `Int`, `Float`, `Money`, `NText`, `Real`, `SmallDateTime`, `SmallInt`, `SmallMoney`, `Text`, `TinyInt`, `UniqueIdentifier`, `Xml` | Used as `sql[dataType]` directly |

**Length parameter:**

| dataType | Extra Field |
|----------|------------|
| `Char`, `NChar`, `NVarChar`, `VarChar` | `length` (integer) |

**Scale parameter:**

| dataType | Extra Field |
|----------|------------|
| `DateTime2`, `DateTimeOffset`, `Time` | `scale` (integer) |

**Precision + Scale parameters:**

| dataType | Extra Fields |
|----------|-------------|
| `Decimal`, `Numeric` | `precision` (integer), `scale` (integer) |

All parameter objects share these fields:

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `name` | string | Y | Parameter name |
| `dataType` | string | Y | SQL data type (see categories above) |
| `value` | any | N | Parameter value. Null if omitted. |
| `output` | boolean | N | When `true`, registers as output parameter |

### Outbound

Array of result objects:

```json
{
  "resultSets": [[]],
  "output": {},
  "rowsAffected": [0]
}
```

- `resultSets` — Array of record set arrays (one per SELECT statement or result set)
- `output` — Output parameter values
- `rowsAffected` — Array of affected row counts

When `returnErrors: true`, failed items return:

```json
{
  "resultSets": [],
  "output": {},
  "rowsAffected": [0],
  "errors": [{ "message": "...", "info": {}, "code": 0 }]
}
```

### Notes

- Connection pooling with max 10, min 2, idle timeout 30s
- Requests are executed **sequentially** (not in parallel) — `for...of` loop
- Non-retryable errors (containing "syntax", "invalid", "permission") abort immediately
- Retry uses exponential backoff via `p-retry`

---

## ODBC (`914c3b05-7d13-4fd2-a0f8-fe925e67f066`)

Generic ODBC connector using worker pool architecture. Supports Plex and NetSuite drivers with NetSuite OAuth JWT.

**Credentials:** Driver-specific (connection string components). Optional `netsuiteOAuth` object for NetSuite JWT auth.
**Connection Options:** `driver` (ODBC driver name)

**Inbound:** Array of:

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `query` | string | Y | SQL query |
| `variables` | array | N | Positional bind variables (substituted into `?` placeholders) |
| `options.retry.retries` | integer | N | Max retry attempts |
| `options.retry.factor` | integer | N | Exponential backoff factor |
| `options.retry.minTimeout` | integer | N | Min retry delay in ms |

### Outbound

Array of result row objects.

### Notes

- Uses worker pool (`odbcPool`) to execute queries in separate processes
- Supports Plex and NetSuite ODBC drivers
- NetSuite OAuth: When `credentialsJSON.netsuiteOAuth` is present, fetches a JWT token before executing queries
- Task timeout applied per query execution
