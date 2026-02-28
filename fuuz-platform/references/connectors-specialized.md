# Specialized Connectors

15 connectors with unique schemas that don't follow the standard REST method+path pattern.

---

## File Transfer

### FTP/SFTP (`396f9092-0c1e-4b3b-ac6d-760094c9466c`)

Action-based file operations over FTP, FTPS, or SFTP.

**Credentials:** `Username`, `Password` (or `SSH Key` for SFTP)
**Connection Options:** `Protocol` (`FTP`, `FTPS`, `SFTP`), `Port`

**Inbound:** Array of:

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `action` | enum | Y | `get`, `getAllInDir`, `post`, `put`, `delete`, `move`, `list` |
| `file` | string | * | File path. Required for `get`, `post`, `put`, `delete`, `move`. |
| `data` | string | * | File content. Required for `post`, `put`. |
| `destination` | string | * | Destination path. Required for `move`, `getAllInDir`. |
| `options.limit` | number | N | Max files for `list` |
| `options.first` | number | N | First N files for `list` |
| `options.last` | number | N | Last N files for `list` |
| `options.match` | string | N | Regex filter for `list` file names |
| `options.encoding` | string | N | File encoding (`utf8`, `base64`, `binary`, `hex`, `ascii`) |
| `options.createDirs` | boolean | N | Create directories if missing |

Required fields depend on `action` (enforced via `oneOf`):
- `get`, `delete`: requires `file`
- `post`, `put`: requires `file` + `data`
- `move`: requires `file` + `destination`
- `getAllInDir`: requires `destination`
- `list`: no additional required fields

---

## Email

### SMTP (`749e15c7-b1d6-4438-9466-7dfe27271573`)

Send email via SMTP using nodemailer.

**Credentials:** `Username`, `Password`
**Connection Options:** `Secure` (boolean), `Port` (default 587), `Ignore TLS`, `Require TLS`
**Endpoint URL:** SMTP server hostname

**Inbound:** Array of (must include either `text` or `html`):

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `from` | string | Y | Sender email address |
| `to` | string | Y | Recipient email address(es) |
| `subject` | string | Y | Email subject |
| `text` | string | * | Plain text body (required if no `html`) |
| `html` | string | * | HTML body (required if no `text`) |
| `attachments` | array | N | File attachments |

**Attachment item:**

| Field | Type | Description |
|-------|------|-------------|
| `filename` | string | Attachment file name |
| `content` | string | Attachment content |
| `href` | string | URL to fetch attachment from |
| `httpHeaders` | object | Headers for fetching `href` |
| `contentType` | enum | `text/plain`, `application/pdf` |
| `contentDisposition` | string | Content disposition header |
| `cid` | string | Content ID for inline attachments |
| `encoding` | string | Content encoding |
| `headers` | object | Custom attachment headers |
| `raw` | string | Raw attachment data |

---

## AI

### OpenAI Chat (`e86501cd-80b4-46ea-97c8-33d31cfef632`)

OpenAI ChatCompletion API. Connection `optionsJSON` is deep-merged with each request as defaults (useful for default system messages).

**Credentials:** `apiKey`

**Inbound:** Array of:

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `model` | string | Y | Model name (e.g. `gpt-4`, `gpt-3.5-turbo`) |
| `messages` | array | Y | Chat messages array |

Additional OpenAI API parameters (temperature, max_tokens, etc.) can be passed as extra properties — they are forwarded to the `chat.completions.create` call.

**Message roles (via `anyOf`):**

| Role | Required Fields | Description |
|------|----------------|-------------|
| `system` / `user` | `content`, `role` | Content can be string or array of strings |
| `assistant` | `role` | Optional: `content`, `refusal`, `audio`, `tool_calls`, `name` |
| `tool` | `role`, `content`, `tool_call_id` | Tool response message |

**Outbound:** Array of OpenAI ChatCompletion response objects.

---

## E-Commerce / Marketplace

### Amazon SP-API (`085c0e1b-e0f2-47ae-a81f-1af51f90aeea`)

Amazon Selling Partner API with three action modes.

**Credentials:** `SELLING_PARTNER_APP_CLIENT_ID`, `SELLING_PARTNER_APP_CLIENT_SECRET`, `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `AWS_SELLING_PARTNER_ROLE`
**Connection Options:** `region`, `refresh_token`, `access_token`, `use_sandbox`, etc.

**Inbound:** Array of:

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `action` | enum | Y | `callAPI`, `download`, `upload` |
| `data` | any | Y | Action-specific data |

**Action-specific `data`:**
- `callAPI`: Object passed to `AWSClient.callAPI(data)`
- `download`: `{ report_document: {}, options?: {} }`
- `upload`: `{ feed_upload_details: {}, feed: {} }`

**Outbound:** `{ Results: [], Errors: [] }` per item.

---

## Cloud Services

### AWS Lambda (`37766115-8263-4d3c-bae1-984d97372221`)

Execute AWS Lambda SDK commands.

**Credentials:** `accessKeyId`, `secretAccessKey`, `sessionToken` (optional)

**Inbound:** Array of:

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `action` | string | Y | Lambda SDK command name (e.g. `InvokeCommand`, `ListFunctionsCommand`) |
| `region` | string | N | AWS region (default `us-east-1`) |
| `body` | object | N | Command input |

**`body` for `InvokeCommand`:**

| Field | Type | Description |
|-------|------|-------------|
| `FunctionName` | string | Lambda function name |
| `Payload` | any | Automatically JSON-stringified and encoded to binary |
| `InvocationType` | string | `RequestResponse` (default), `Event`, `DryRun` |
| `LogType` | string | `None`, `Tail` |
| `ClientContext` | string | Client context string |
| `Qualifier` | string | Function version/alias |

**Outbound:** `{ results, errors }` per item. `InvokeCommand` response `Payload` is decoded from Uint8Array back to JSON.

---

### Google API (`2ab3d71a-2b81-4253-8428-8c359a26f082`)

Dynamic Google API client via `googleapis`. Supports Drive, BigQuery, and other Google APIs.

**Credentials:** `clientEmail`, `privateKey` (service account)
**Connection Options:** `scopes` (OAuth2 scopes array)

**Inbound:** Array of:

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `api` | string | Y | Google API name (e.g. `drive`, `bigquery`) |
| `resource` | string | Y | API resource (e.g. `files`, `datasets`) |
| `action` | string | Y | Resource action (e.g. `create`, `get`, `list`, `delete`) |
| `body` | object | N | Request body |
| `body.requestBody` | object | N | Request payload |
| `body.media` | object | N | File upload: `{ body: "base64string", mimeType: "..." }` |
| `body.resource` | object | N | Resource metadata |
| `options` | object | N | Additional request options |
| `version` | string | N | API version (e.g. `v3`, `v2`) |

**Notes:**
- Drive file uploads: when `api=drive`, `resource=files`, `action=create` with `body.media.body`, the base64 content is converted to a memory stream for upload
- Calls `google[api]({version, auth})[resource][action](body, options)`

**Outbound:** `{ results, errors }` per item.

---

## Shipping

### FedEx (`d16be6b2-ad5e-4b32-8e54-03f168b947f4`)

XML SOAP connector for FedEx shipping APIs.

**Credentials:** `parentCredentials.key`, `parentCredentials.password`, `userCredentials.key`, `userCredentials.password`, `clientDetail.accountNumber`, `clientDetail.meterNumber`, `clientDetail.integratorId`
**Connection Options:** `transactionDetail`, `labelDefaults`, `shipper` (address/company info)

**Inbound:** Array of:

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `action` | enum | Y | `Address Validation`, `Rates`, `Shipping Validation`, `Shipment Create`, `Shipment Delete`, `POST` |
| `data` | string or object | N | XML body or data object. For `POST`, raw data is sent as-is. |

**Notes:** All requests are HTTP POST with `Content-Type: text/xml`. Responses are parsed from XML to JSON.

---

### UPS Legacy (`920e8e71-1de6-4160-8129-f16612c0d694`)

Legacy UPS API with action-based routing.

**Credentials:** `UsernameToken.Username`, `UsernameToken.Password`, `TransactionSrc`, `ServiceAccessToken.AccessLicenseNumber`
**Connection Options:** `Shipping_Defaults`

**Inbound:** Array of:

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `action` | enum | Y | `GET`, `POST`, `DELETE`, `Address Validation`, `Create Shipment` |
| `path` | string | N | API path |
| `data` | object | N | Request body |
| `version` | enum | N | `v1` |

---

### Fuuz-UPS (`906f591e-0cac-41f7-9fc3-bdbae5d6117a`)

Server-managed UPS connector. Credentials are read from server config, not the connection.

**Credentials:** Optional `AccessLicenseNumber` override (server provides defaults)

**Inbound:** Array of:

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `action` | enum | Y | `GET`, `POST`, `DELETE`, `License Agreement`, `License Request` |
| `path` | string | N | API path |
| `data` | object | N | Request body |
| `version` | enum | N | `v1` |

---

### USPS (`c1ce6d2d-0e76-4683-9b06-00812f5514f4`)

XML-based USPS postal API. The connector injects the user's USERID into XML data.

**Credentials:** `UserId`

**Inbound:** Array of:

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `action` | enum | Y | `POST` |
| `path` | string | N | URL path |
| `api` | string | N | USPS API name (sent as `API` query parameter) |
| `data` | string or object | N | XML data. Must contain `USERID=''` attribute — the connector fills it in. |

**Notes:** Data is sent as a URL query parameter (`XML=...`), not in the request body. Responses are parsed from XML to JSON.

---

## ERP / SOAP

### NetSuite SOAP (`3c94fb0e-0767-11ec-9a03-0242ac130003`)

SOAP connector for NetSuite with 40+ actions.

**Credentials:** `account`, `consumerKey`, `consumerSecret`, `tokenId`, `tokenSecret`
**Connection Options:** `endpointVersion`

**Inbound:** Array of:

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `action` | enum | Y | SOAP action (see list below) |
| `requestBody` | string | Y | XML body content |
| `options.returnXML` | boolean | N | When `true`, return raw XML instead of parsed JSON |

**Actions:** `loginasd`, `ssoLogin`, `mapSso`, `changePassword`, `changeEmail`, `logout`, `add`, `delete`, `search`, `searchMore`, `searchMoreWithId`, `searchNext`, `update`, `upsert`, `addList`, `deleteList`, `updateList`, `upsertList`, `get`, `getList`, `getAll`, `getSavedSearch`, `getCustomizationId`, `initialize`, `initializeList`, `getSelectValue`, `getItemAvailability`, `getBudgetExchangeRate`, `getCurrencyRate`, `getDataCenterUrls`, `getPostingTransactionSummary`, `getServerTime`, `attach`, `detach`, `updateInviteeStatus`, `updateInviteeStatusList`, `asyncAddList`, `asyncUpdateList`, `asyncUpsertList`, `asyncDeleteList`, `asyncGetList`, `asyncInitializeList`, `asyncSearch`, `getAsyncResult`, `checkAsyncStatus`, `getDeleted`

**Outbound:** `{ data: { headers, body }, error }` per item. When `returnXML: true`, `data` is raw XML string.

---

## EDI

### EDI Nation (`01ebf215-8d6a-4516-a058-16cc40a25773`)

EDI read/write/validate/acknowledge operations via EDI Nation API.

**Credentials:** `accessToken` (API key)
**Endpoint URL:** EDI Nation API URL

**Inbound:** Array of (via `anyOf`):

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `action` | enum | Y | `read`, `write`, `ack`, `validate` |
| `format` | enum | Y | `x12`, `edifact` |
| `data` | varies | Y | For `read`: `{ fileContents, fileName? }`. For others: EDI data object. |
| `options.apiOptions` | object | N | Additional API options |

---

## TecCom

### TecCom File Upload (`e2c23250-2da3-41af-8ea5-abe14a4aa77c`)

Converts JSON data to CSV, zips it, and uploads to TecCom.

**Credentials:** `clientId`, `clientSecret`
**Connection Options:** `buyerGLN`, `supplierGLN`
**Auth:** OAuth2 client credentials (hardcoded TecAlliance OAuth URL)

**Inbound:** Array of:

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `requestMode` | enum | Y | `REPLACE`, `MODIFY` |
| `files` | array | Y | Files to upload |

**File item:**

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `fileName` | enum | Y | `article`, `assortment`, `article_data`, `sales_uom`, `buyer_grouping`, `availability`, `article_description`, `article_buyer`, `alternative`, `condition_group`, `price`, `alc`, `packaging`, `external_document` |
| `data` | array | Y | Array of row objects to convert to CSV |

---

### TecCom Web Services (`395ab4e2-2ef4-471c-ad42-5fdad7f22d6f`)

SOAP web service calls to TecCom.

**Credentials:** `clientId`, `clientSecret`
**Connection Options:** `buyerGLN`, `supplierGLN`
**Auth:** OAuth2 client credentials

**Inbound:** Array of:

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `functionId` | string | Y | Web service function ID |
| `parameters` | object | Y | Function parameters (each key-value becomes a SOAP parameter) |

**Outbound:** `{ Timestamp, Reference, OriginatingFunction, Status }` per item.

---

## Multi-Action Platforms

### Fuuz (`43b90e5f-48a5-437c-b968-6f35a1663257`)

Multi-action connector for external Fuuz instances. Max 100 items per request.

**Credentials:** `ApiKey` (JWT — decoded to extract URL and username)

**Inbound:** Array of (via `anyOf` — action determines shape):

**`query` action:**

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `action` | | Y | `query` |
| `query` | string | Y | GraphQL query |
| `api` | enum | Y | `application`, `system` |
| `variables` | object | N | Query variables |

**`mutation` action:**

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `action` | | Y | `mutation` |
| `mutation` | string | Y | GraphQL mutation |
| `api` | enum | Y | `application`, `system` |
| `variables` | object | N | Mutation variables |

**`flow` action:**

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `action` | | Y | `flow` |
| `flowId` | string | Y | Flow ID to execute |
| `payload` | object | N | Flow payload |

**`savedQuery` action:**

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `action` | | Y | `savedQuery` |
| `savedQueryId` | string | Y | Saved query ID |
| `variables` | object | N | Query variables |

**`savedScript` action:**

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `action` | | Y | `savedScript` |
| `savedScriptId` | string | Y | Saved script ID |
| `payload` | object | N | Script payload |

**`publish` action:**

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `action` | | Y | `publish` |
| `topicName` | string | Y | Topic name |
| `topicMessages` | array | Y | Messages: `[{ value: any }]` |

---

