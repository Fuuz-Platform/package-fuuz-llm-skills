# Plex Connectors

Four connectors for Plex Manufacturing Cloud. The SOAP and UX connectors execute datasources and share the same outbound shape. The API and IAM API connectors use standard REST.

---

## Shared Outbound Shape (Plex SOAP + Plex UX)

Each payload item returns:

```json
{
  "Parameters": {},
  "Rows": [{}],
  "Errors": [{ "request": {}, "message": "string", "statusCode": 0 }]
}
```

- `Parameters` — Output parameters from the datasource
- `Rows` — Array of result row objects
- `Errors` — Present when `returnErrors: true` and a request fails

## Shared Pagination Model (Plex SOAP + Plex UX)

Two modes (determined by `oneOf` in schema):

**Key-based pagination:**

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `keyColumn` | string | Y | Column to use as cursor for next page |
| `keyParameter` | string | Y | Datasource parameter to pass cursor value |
| `rowLimit` | integer (min 1) | Y | Rows per page (injected as `Top_N` parameter) |
| `maxPages` | integer (min 1) | N | Max pages before stopping. Default 500. Exceeding throws error. |

**Offset pagination:**

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `offsetPagination` | boolean | Y | Must be `true` |
| `rowLimit` | integer (min 1) | Y | Rows per page (injected as `Limit` parameter) |
| `initialOffset` | integer (min 0) | N | Starting offset. Default 0. |
| `maxPages` | integer (min 1) | N | Max pages before stopping. Default 500. Silently stops. |

---

### Plex Classic SOAP (`464e9c3f-dd6c-47af-8078-cda1ff25e966`)

Executes Plex datasources via SOAP XML `ExecuteDataSource` endpoint.

**Credentials:** `Username`, `Password`

**Inbound:** Array of:

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `datasourceKey` | integer | Y | Plex datasource key |
| `datasourceName` | string | N | Optional datasource name for the XML element |
| `parameters` | object | Y | Input parameters (keys map to Plex `@`-prefixed params, minus the `@`) |
| `options.pagination` | object | N | See pagination model above |

**Outbound:** Shared `{ Parameters, Rows, Errors }` shape.

**Notes:**
- 5-minute HTTP timeout
- Checks `@Result_Error` output parameter for datasource-level errors
- `undefined` parameter values are omitted from the SOAP request

---

### Plex UX (`72974b11-897f-47bd-896d-133962ef7bbb`)

Executes Plex datasources via JSON REST API. Supports two modes.

**Credentials:** `Username`, `Password`

**Inbound:** Array of items matching one of two modes:

**Execute mode** (standard datasource execution):

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `datasourceKey` | integer | Y | Plex datasource key |
| `datasourceName` | string | N | Optional name |
| `parameters` | object | Y | Input parameters |
| `options.pagination` | object | N | See pagination model above |

POSTs to `/api/datasources/{datasourceKey}/execute` with `{ inputs: parameters }`.

**GET mode** (metadata/search):

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `method` | string | Y | Must be `"get"` or `"GET"` |
| `parameters.datasourceKey` | integer | N | Fetch datasource by key |
| `parameters.datasourceName` | string | N | Search datasource by name |

GETs `/api/datasources/{key}` or `/api/datasources/search?name={name}`. No pagination in this mode.

**Outbound:** Shared `{ Parameters, Rows, Errors }` shape (Errors may include optional `info` field).

**Notes:**
- 5-minute HTTP timeout
- Response columns/rows arrays are zipped into row objects
- Checks `response.outputs.Result_Error` for errors

---

### Plex API (`1cc8888c-bc12-4545-b354-d26f9493f0ac`)

Generic REST connector for Plex Connect APIs.

**Credentials:** `consumerKey`
**Connection Options:** `tenantId` (optional, overrideable per-request)

**Inbound:** Array of:

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `method` | enum | Y | `get`, `put`, `post`, `patch`, `delete` |
| `path` | string | Y | API path |
| `params` | object | N | Query string parameters |
| `tenantId` | string | N | Override connection-level tenant ID |
| `body` | any | N | Request body (object, array, string, number, null) |

**Outbound:** Array of raw API response bodies.

**Notes:**
- Sets `X-Plex-Connect-Api-Key` header from `consumerKey`
- Sets `X-Plex-Connect-Tenant-Id` from connection or per-request `tenantId`
- No built-in pagination

---

### Plex IAM API (`9db652bf-9f99-472e-ada2-71f8e92f973f`)

REST connector for Plex IAM (Identity & Access Management) API with OAuth2.

**Credentials:** `clientId`, `clientSecret`

**Inbound:** Array of:

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `method` | enum | Y | `get`, `put`, `post`, `patch`, `delete` |
| `path` | string | Y | API path |
| `params` | object | N | Query string parameters |
| `body` | any | N | Request body (object, array, string, number, null) |

**Outbound:** Array of `{ body, errors }`. When `returnErrors: true`, failed requests return `{ body: {}, errors: [{ statusCode, error }] }`.

**Notes:**
- OAuth2 Client Credentials flow — POSTs to `{url}/op/token`
- Tokens cached for ~9.5 minutes
- Sets `Authorization: Bearer {token}` on each request
