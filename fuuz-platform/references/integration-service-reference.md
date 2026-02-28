# Integration Service Reference

> **Derived from the FUUZ integration service source code.** This document consolidates the complete connector registry, `$integrate()` API, authentication patterns, inbound/outbound schemas, and all 42 connectors organized into four groups: REST, Plex, Database, and Specialized. Every field, type, UUID, and schema detail is preserved for accurate integration code generation.

---

## Table of Contents

- [1. $integrate() API](#1-integrate-api)
- [2. Execution Context](#2-execution-context)
- [3. Authentication Patterns](#3-authentication-patterns)
- [4. Connector Quick Reference (UUID Registry)](#4-connector-quick-reference-uuid-registry)
- [5. REST-Pattern Connectors (21)](#5-rest-pattern-connectors-21)
  - [5.1 Base Inbound Shape](#51-base-inbound-shape)
  - [5.2 Minimal REST Connectors](#52-minimal-rest-connectors)
  - [5.3 UPPERCASE Method Connectors](#53-uppercase-method-connectors)
  - [5.4 Connectors with Extended Method Sets](#54-connectors-with-extended-method-sets)
  - [5.5 Connectors with Pagination/Filter Extensions](#55-connectors-with-paginationfilter-extensions)
  - [5.6 Connectors with Different Field Names](#56-connectors-with-different-field-names)
  - [5.7 Connectors with Mutual TLS](#57-connectors-with-mutual-tls)
- [6. Plex Connectors (4)](#6-plex-connectors-4)
  - [6.1 Shared Outbound Shape](#61-shared-outbound-shape)
  - [6.2 Shared Pagination Model](#62-shared-pagination-model)
  - [6.3 Plex Classic SOAP](#63-plex-classic-soap)
  - [6.4 Plex UX](#64-plex-ux)
  - [6.5 Plex API](#65-plex-api)
  - [6.6 Plex IAM API](#66-plex-iam-api)
- [7. Database Connectors (2)](#7-database-connectors-2)
  - [7.1 MSSQL](#71-mssql)
  - [7.2 ODBC](#72-odbc)
- [8. Specialized Connectors (15)](#8-specialized-connectors-15)
  - [8.1 File Transfer - FTP/SFTP](#81-file-transfer---ftpsftp)
  - [8.2 Email - SMTP](#82-email---smtp)
  - [8.3 AI - OpenAI Chat](#83-ai---openai-chat)
  - [8.4 E-Commerce - Amazon SP-API](#84-e-commerce---amazon-sp-api)
  - [8.5 Cloud Services - AWS Lambda](#85-cloud-services---aws-lambda)
  - [8.6 Cloud Services - Google API](#86-cloud-services---google-api)
  - [8.7 Shipping - FedEx](#87-shipping---fedex)
  - [8.8 Shipping - UPS Legacy](#88-shipping---ups-legacy)
  - [8.9 Shipping - Fuuz-UPS](#89-shipping---fuuz-ups)
  - [8.10 Shipping - USPS](#810-shipping---usps)
  - [8.11 ERP/SOAP - NetSuite SOAP](#811-erpsoap---netsuite-soap)
  - [8.12 EDI - EDI Nation](#812-edi---edi-nation)
  - [8.13 TecCom - File Upload](#813-teccom---file-upload)
  - [8.14 TecCom - Web Services](#814-teccom---web-services)
  - [8.15 Multi-Action - Fuuz](#815-multi-action---fuuz)

---

## 1. $integrate() API

The integration service is a Fastify microservice that routes payloads to connectors by UUID, validates inbound/outbound schemas, and executes external API calls. There are 42 connectors organized into four groups.

In data flow transforms, call connectors via:

```jsonata
$integrate({
  "connectionName": "My HTTP Connection",
  "options": {
    "degreeOfParallelism": 10,
    "returnErrors": true
  },
  "payload": [
    { "method": "get", "path": "/api/users" }
  ]
})
```

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `connectionName` | string | Y | Name of the configured connection |
| `options.degreeOfParallelism` | integer | N | Concurrent requests. Default 10. |
| `options.returnErrors` | boolean | N | When `true`, failed requests return error objects instead of aborting. Default `false`. |
| `payload` | array | Y | Array of request items (shape depends on connector) |

The `payload` array is validated against the connector's inbound JSON schema before execution. Each item in the array becomes one request. Results are returned as an array in the same order.

---

## 2. Execution Context

Each connector's `handle` function receives:

```js
{
  connection: {
    credentialsJSON,  // Parsed credentials (varies per connector)
    optionsJSON,      // Parsed connection options (varies per connector)
    endpoint: { url, name }
  },
  options: {
    degreeOfParallelism,  // From $integrate() options
    returnErrors          // From $integrate() options
  }
}
```

The connector registry (`CONNECTORS_BY_UUID`) maps UUIDs to connector modules. Each module exports `{ connectorUuid, schema: { inbound, outbound }, handle }`. Validators are created at startup via `schemaToValidator`.

---

## 3. Authentication Patterns

| Pattern | Connectors | Description |
|---------|------------|-------------|
| Basic Auth | HTTP, Confluence, Infor, Magento | Username + Password sent as Basic header or token |
| OAuth2 Client Credentials | Dynamics 365, CH Robinson, Plex IAM API, UPS OAuth, TecCom | clientId + clientSecret, token cached with TTL |
| OAuth2 Refresh Token | QuickBooks, Zoho, ChannelAdvisor | Refresh token grant, some rotate tokens |
| OAuth2 Password Grant | Salesforce | Username + Password + clientId + clientSecret + securityToken |
| OAuth1 TBA | NetSuite REST | Token-Based Auth with consumer/token key pairs |
| SAML 2.0 Bearer | SuccessFactors | SAML assertion signed with private key |
| API Key | SAP Cloud, Spiro, Clover, OpenAI | Static key in header |
| Mutual TLS | ADP, ADP Vista | Client certificate + key for mTLS |
| Session Token | Arena | Login with email/password, session cookie |
| Service Account | Google API | JWT signed with service account key |
| AWS Credentials | Amazon SP-API, AWS Lambda | Access key + secret + optional role ARN |
| Custom | FedEx, UPS Legacy, USPS, NetSuite SOAP | Connector-specific auth (XML credentials, SOAP headers, etc.) |

---

## 4. Connector Quick Reference (UUID Registry)

| Connector | UUID | Group | Auth |
|-----------|------|-------|------|
| HTTP | `05e0b880-7b0d-4ca1-9b8f-30126ffd305a` | REST | Basic |
| Confluence | `d7ca2d1a-cc71-47fb-8b26-d316bfaedaf9` | REST | Basic (email:apiKey) |
| Salesforce | `44ff6253-ca43-4429-955a-2907803178b8` | REST | OAuth2 Password |
| Dynamics 365 | `808fce4c-6e38-4db8-b4b4-5c45a5a6f1cb` | REST | OAuth2 Client Cred |
| QuickBooks | `88c548f7-4944-4eed-a948-9ce683e02a6f` | REST | OAuth2 Refresh |
| Zoho | `4ff8769a-bdea-11e8-a355-529269fb1459` | REST | OAuth2 Refresh |
| SAP Cloud | `34e36d24-64f3-4d59-a764-bab5b851f72d` | REST | API Key |
| Infor | `07ba704-0eef-11eb-adc1-0242ac120002` | REST | OAuth2 Password |
| SuccessFactors | `165219c4-830b-49af-828f-7964d4e4bd08` | REST | SAML 2.0 |
| Spiro | `fa98a8d9-74f3-4ce1-8ef9-4455c31d3ce4` | REST | API Key (Bearer) |
| ADP | `6a6da1f8-b69c-11e8-96f8-529269fb1459` | REST | Mutual TLS |
| ADP Vista | `5b2ed01a-628e-413e-8baf-f6d4c01fb53b` | REST | Mutual TLS + OAuth2 |
| Magento | `3d3b20ea-f261-40c1-9094-a556e60ce5e9` | REST | Token Auth |
| WooCommerce | `09fb753b-6b94-48b6-b70e-31ce7acdddac` | REST | Basic (consumerKey:secret) |
| NetSuite REST | `ac61106b-72c6-4fca-8bc7-0ba3d54fb574` | REST | OAuth1 TBA |
| Arena | `c6a47b3d-5740-4cec-a9d1-dfba96249d03` | REST | Session Token |
| ChannelAdvisor | `1bbd8c15-601e-43f5-b584-e898ba58bfd2` | REST | OAuth2 Refresh |
| CH Robinson | `3c751b02-b23c-4615-9eba-c7a5dc33c507` | REST | OAuth2 Client Cred |
| Clover | `2d259032-b9ea-4904-afbf-d30d4fc142a9` | REST | API Key (Bearer) |
| Square | `c125d58f-41f5-41f5-b42e-3a63e3441526` | REST | API Key (Bearer) |
| UPS OAuth | `c13ebe14-08f3-4c6a-bb1d-c717d8430ab9` | REST | OAuth2 Client Cred |
| Plex Classic SOAP | `464e9c3f-dd6c-47af-8078-cda1ff25e966` | Plex | Basic |
| Plex UX | `72974b11-897f-47bd-896d-133962ef7bbb` | Plex | Basic |
| Plex API | `1cc8888c-bc12-4545-b354-d26f9493f0ac` | Plex | API Key |
| Plex IAM API | `9db652bf-9f99-472e-ada2-71f8e92f973f` | Plex | OAuth2 Client Cred |
| MSSQL | `bb612514-2129-4fa9-849d-f9f8f6ec1c8e` | Database | Connection String |
| ODBC | `914c3b05-7d13-4fd2-a0f8-fe925e67f066` | Database | Driver-specific |
| FTP/SFTP | `396f9092-0c1e-4b3b-ac6d-760094c9466c` | Specialized | Username/Password |
| SMTP | `749e15c7-b1d6-4438-9466-7dfe27271573` | Specialized | Username/Password |
| OpenAI Chat | `e86501cd-80b4-46ea-97c8-33d31cfef632` | Specialized | API Key |
| Amazon SP-API | `085c0e1b-e0f2-47ae-a81f-1af51f90aeea` | Specialized | AWS + OAuth |
| AWS Lambda | `37766115-8263-4d3c-bae1-984d97372221` | Specialized | AWS Credentials |
| Google API | `2ab3d71a-2b81-4253-8428-8c359a26f082` | Specialized | Service Account |
| FedEx | `d16be6b2-ad5e-4b32-8e54-03f168b947f4` | Specialized | Multi-level Keys |
| UPS Legacy | `920e8e71-1de6-4160-8129-f16612c0d694` | Specialized | UserId/Password/License |
| USPS | `c1ce6d2d-0e76-4683-9b06-00812f5514f4` | Specialized | UserId |
| NetSuite SOAP | `3c94fb0e-0767-11ec-9a03-0242ac130003` | Specialized | OAuth1 TBA |
| EDI Nation | `01ebf215-8d6a-4516-a058-16cc40a25773` | Specialized | API Key |
| TecCom File Upload | `e2c23250-2da3-41af-8ea5-abe14a4aa77c` | Specialized | OAuth2 Client Cred |
| TecCom Web Services | `395ab4e2-2ef4-471c-ad42-5fdad7f22d6f` | Specialized | OAuth2 Client Cred |
| Fuuz | `43b90e5f-48a5-437c-b968-6f35a1663257` | Specialized | JWT API Key |
| Fuuz-UPS | `906f591e-0cac-41f7-9fc3-bdbae5d6117a` | Specialized | Server-managed |

---

## 5. REST-Pattern Connectors (21)

21 connectors that follow the `method + path + body` REST pattern. They share a common base shape with per-connector variations in field names, method casing, body types, and extra fields.

### 5.1 Base Inbound Shape

All REST connectors accept an array of items. The common fields are:

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `method` (or `action`) | enum | Y | HTTP method — lowercase or UPPERCASE depending on connector |
| `path` (or `endpoint`) | string | Y | API endpoint path |
| `data` (or `body`) | varies | N | Request body |

Extra fields (`headers`, `params`, `filters`, pagination options, etc.) vary per connector and are listed below.

**Outbound:** All REST connectors return an array. The exact item shape varies — some return raw response bodies, others return `{ body, errors }` or `{ data, errors }`.

---

### 5.2 Minimal REST Connectors

These connectors use the base shape with few extras.

#### HTTP (`05e0b880-7b0d-4ca1-9b8f-30126ffd305a`)

**Credentials:** `Username`, `Password` (optional — omitted if not set)
**Connection Options:** `Headers` (default headers merged into every request)

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `method` | enum | Y | `get`, `put`, `post`, `delete` |
| `path` | string | Y | API path |
| `data` | object, array, string, number, null | N | Request body |
| `headers` | object | N | Custom headers (merged with connection defaults) |
| `responseType` | enum | N | `json` (default), `binary`, `text`. Binary returns base64 string. |
| `options.returnHeaders` | boolean | N | When `true`, returns `{ response, headers }` instead of just the body |

---

#### Confluence (`d7ca2d1a-cc71-47fb-8b26-d316bfaedaf9`)

Identical inbound schema to HTTP. Uses Basic auth with `email:apiKey` encoded as base64.

**Credentials:** `email`, `apiKey`

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `method` | enum | Y | `get`, `put`, `post`, `delete` |
| `path` | string | Y | API path |
| `body` | object, array, string, number, null | N | Request body |
| `headers` | object | N | Custom headers |
| `responseType` | enum | N | `binary`, `json`, `text` |
| `options.returnHeaders` | boolean | N | Return response headers alongside body |

---

#### Arena (`c6a47b3d-5740-4cec-a9d1-dfba96249d03`)

**Credentials:** `Username` (email), `Password`, `Client ID` (workspaceId)
**Connection Options:** `Token URL`, `API URL`
**Auth:** Session token via email/password login

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `method` | enum | Y | `get`, `put`, `post`, `delete` |
| `path` | string | Y | API path |
| `data` | object | N | Request body |

---

#### Infor (`07ba704-0eef-11eb-adc1-0242ac120002`)

**Credentials:** `clientId`, `clientSecret`, `grantType`, `accessTokenUrl`, `userName`, `password`, `scope`
**Auth:** OAuth2 password grant. Token cached ~2 hours.

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `method` | enum | Y | `get`, `put`, `post`, `delete` |
| `path` | string | Y | API path |
| `data` | object, array, string, number, null | N | Request body |

**Notes:** Checks Infor-specific error fields (`cErrorMessage`, `errorMessage`) in successful responses.

---

#### ChannelAdvisor (`1bbd8c15-601e-43f5-b584-e898ba58bfd2`)

**Credentials:** `clientId`, `clientSecret`, `grantType`, `refreshToken`
**Auth:** OAuth2 refresh token grant. Token cached ~58 minutes.

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `method` | enum | Y | `get`, `put`, `post`, `delete` |
| `path` | string | Y | API path |
| `data` | object | N | Request body |

---

#### CH Robinson (`3c751b02-b23c-4615-9eba-c7a5dc33c507`)

**Credentials:** `clientId`, `clientSecret`, `audience`, `grantType`
**Auth:** OAuth2 client credentials (JSON body). Token cached ~23.5 hours.

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `method` | enum | Y | `get`, `put`, `post`, `delete` |
| `path` | string | Y | API path |
| `data` | object | N | Request body |

---

### 5.3 UPPERCASE Method Connectors

These connectors use UPPERCASE HTTP methods.

#### Dynamics 365 (`808fce4c-6e38-4db8-b4b4-5c45a5a6f1cb`)

**Credentials:** `clientID`, `clientSecret`, `scope`
**Connection Options:** `accessTokenUrl`
**Auth:** OAuth2 client credentials

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `method` | enum | Y | `GET`, `POST`, `DELETE`, `PUT`, `PATCH` |
| `path` | string | Y | API path |
| `body` | object, array, boolean, null, string, number | N | Request body (accepts any JSON type) |
| `headers` | object | N | Custom headers |

**Outbound:** `{ body, errors }` per item.

---

#### SuccessFactors (`165219c4-830b-49af-828f-7964d4e4bd08`)

Identical inbound schema to Dynamics 365 but uses SAML 2.0 Bearer Assertion auth.

**Credentials:** `companyId`, `apiKey`, `certificate`, `privateKey`, `username`, `userType`, `audiences`, `issuer`, `newToken`
**Auth:** SAML 2.0 Bearer Assertion -> OAuth2 token. Token cached ~8.3 minutes.

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `method` | enum | Y | `GET`, `POST`, `DELETE`, `PUT`, `PATCH` |
| `path` | string | Y | API path |
| `body` | object, array, boolean, null, string, number | N | Request body |
| `headers` | object | N | Custom headers |

**Outbound:** `{ body, errors }` per item.

---

#### WooCommerce (`09fb753b-6b94-48b6-b70e-31ce7acdddac`)

**Credentials:** `consumerKey`, `consumerSecret`
**Auth:** Basic auth (consumerKey:consumerSecret base64)

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `method` | enum | Y | `GET`, `PUT`, `POST`, `DELETE` |
| `path` | string | Y | API path (prepended with `/wp-json/wc/{version}/`) |
| `data` | object | N | Request body |
| `version` | enum | N | `v2`, `v3` (default `v3`) |
| `parameters` | object | N | Query string parameters |

**Outbound:** `{ data }` or `{ error }` per item.

---

### 5.4 Connectors with Extended Method Sets

#### Salesforce (`44ff6253-ca43-4429-955a-2907803178b8`)

**Credentials:** `Username`, `Password`, `Client ID`, `Client Secret`
**Auth:** OAuth2 password flow. Token cached ~59 minutes.

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `method` | enum | Y | `get`, `patch`, `put`, `post`, `delete`, `head` |
| `path` | string | Y | API path |
| `data` | object | N | Request body |
| `params` | object | N | Query string parameters |
| `options.pagination` | boolean | N | When `true`, auto-follows `nextRecordsUrl` to get all pages |

**Outbound:** `{ data, errors }` per item.

---

#### QuickBooks (`88c548f7-4944-4eed-a948-9ce683e02a6f`)

**Credentials:** `clientId`, `clientSecret`, `refreshToken`
**Auth:** OAuth2 refresh token with token rotation (new refresh token saved back to connection).

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `method` | enum | Y | `get`, `put`, `post`, `patch`, `delete` |
| `path` | string | Y | API path |
| `body` | object, array, string, number, null | N | Request body |
| `headers` | object | N | Custom headers |
| `params` | object | N | Query string parameters |

---

#### Square (`c125d58f-41f5-41f5-b42e-3a63e3441526`)

**Credentials:** `Personal Access Token`
**Auth:** Static Bearer token

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `method` | enum | Y | `get`, `patch`, `post`, `delete` |
| `path` | string | Y | API path |
| `data` | object | N | Request body |
| `params` | object | N | Query string parameters |
| `version` | enum | N | `v2` (default) |
| `options.pagination` | boolean | N | Auto-follows cursor-based pagination (default `true`) |

**Outbound:** `{ records, errors, cursor }` per item.

---

### 5.5 Connectors with Pagination/Filter Extensions

#### Magento (`3d3b20ea-f261-40c1-9094-a556e60ce5e9`)

**Credentials:** `Username`, `Password` (or `Token` for pre-authenticated)
**Connection Options:** `Token URL`
**Auth:** Token-based. Token cached ~59 minutes.

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `method` | enum | Y | `get`, `put`, `post`, `delete` |
| `path` | string | Y | API path |
| `data` | object or array | N | Request body |
| `filters` | array | N | Magento search criteria filters |
| `pagination` | object | N | Magento pagination options |
| `options.returnErrors` | boolean | N | Per-item error handling |

**Filter item:**

| Field | Type | Description |
|-------|------|-------------|
| `field` | string | Field name |
| `value` | string | Filter value |
| `condition_type` | enum | `eq`, `finset`, `from`, `gt`, `gteq`, `in`, `like`, `lt`, `lteq`, `moreq`, `neq`, `nin`, `notnull`, `null`, `to` |

**Pagination:**

| Field | Type | Description |
|-------|------|-------------|
| `currentPage` | integer | Current page number |
| `pageSize` | integer | Results per page |
| `sortOrders` | array | `[{ field, direction }]` |

---

#### Zoho (`4ff8769a-bdea-11e8-a355-529269fb1459`)

**Credentials:** `Authorization Token` (refresh token), `Client ID`, `Client Secret`
**Connection Options:** `API URL`, `Token URL`, `Redirect URL`, `Scope`
**Auth:** OAuth2 refresh token grant. Token cached ~59 minutes.

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `method` | enum | Y | `get`, `put`, `post`, `delete` |
| `path` | string | Y | API path |
| `data` | object | N | Request body |
| `modifiedSince` | string (date-time) | N | `If-Modified-Since` header value |
| `options.pages.start` | integer (min 1) | N | Starting page (required for pagination) |
| `options.pages.stop` | integer (min 1) | N | Ending page (default Infinity) |

---

#### Spiro (`fa98a8d9-74f3-4ce1-8ef9-4455c31d3ce4`)

**Credentials:** `Authorization Token`
**Connection Options:** `API URL`
**Auth:** Static Bearer token (JSON:API content type)

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `method` | enum | Y | `get`, `put`, `post`, `delete` |
| `path` | string | Y | API path |
| `data` | object | N | Request body |
| `filter` | string | N | Filter query string |
| `modifiedSince` | string (date-time) | N | `If-Modified-Since` header value |
| `options.returnErrors` | boolean | N | Per-item error handling |
| `options.pages.itemsPerPage` | integer (1-500) | N | Items per page (required for pagination) |
| `options.pages.start` | integer (min 1) | N | Starting page |
| `options.pages.stop` | integer (min 1) | N | Ending page |

---

#### SAP Cloud (`34e36d24-64f3-4d59-a764-bab5b851f72d`)

**Credentials:** `Authorization Token` (API Key)
**Connection Options:** `API URL`
**Auth:** API Key sent as `APIKey` header

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `method` | enum | Y | `get`, `put`, `post`, `delete` |
| `path` | string | Y | API path |
| `data` | object | N | Request body |
| `top` | string | N | OData `$top` -- max records |
| `skip` | string | N | OData `$skip` -- records to skip |
| `filter` | string | N | OData `$filter` expression |
| `inlinecount` | enum | N | `""`, `allpages`, `none` |
| `orderby` | string | N | OData `$orderby` |
| `select` | string | N | OData `$select` -- fields to return |
| `expand` | string | N | OData `$expand` -- related entities |

---

#### Clover (`2d259032-b9ea-4904-afbf-d30d4fc142a9`)

**Credentials:** `Authorization Token`
**Auth:** Static Bearer token

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `method` | enum | Y | `get`, `put`, `post`, `delete` |
| `path` | string | Y | API path |
| `data` | object | N | Request body |
| `filters` | array | N | Clover filter array |
| `expand` | array of strings | N | Related entities to expand |
| `limit` | string | N | Result limit (default `"100"`) |
| `orderBy` | string | N | Sort field |

**Filter item:** `{ field, value, condition_type }` where `condition_type` is one of `==`, `!=`, `>`, `<`, `>=`, `<=`.

---

### 5.6 Connectors with Different Field Names

#### NetSuite REST (`ac61106b-72c6-4fca-8bc7-0ba3d54fb574`)

Uses `endpoint`/`action` instead of `path`/`method`.

**Credentials:** `account`, `consumerKey`, `consumerSecret`, `tokenId`, `tokenSecret`
**Auth:** OAuth 1.0 Token-Based Auth (HMAC-SHA256)

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `endpoint` | string | Y | API path (used instead of `path`) |
| `action` | string | Y | HTTP method (used instead of `method`) |
| `body` | object, array, or string | N | Request body |
| `headers` | object | N | Custom headers |
| `parameters` | object | N | Query string parameters |

**Outbound:** `{ headers, data, error }` per item.

---

#### UPS OAuth (`c13ebe14-08f3-4c6a-bb1d-c717d8430ab9`)

Uses `action` instead of `method`.

**Credentials:** `clientId`, `clientSecret`, `accountNumber` (merchantId)
**Auth:** OAuth2 client credentials. Token cached ~4 hours.

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `action` | enum | Y | `GET`, `POST`, `DELETE`, `PUT`, `PATCH` |
| `path` | string | N | API path |
| `data` | object | N | Request body |

**Outbound:** `{ body, errors }` per item.

---

### 5.7 Connectors with Mutual TLS

#### ADP (`6a6da1f8-b69c-11e8-96f8-529269fb1459`)

**Credentials:** `Client ID`, `Client Secret`
**Connection Options:** `SSL Certificate`, `SSL Key`, `Token URL`, `API URL`
**Auth:** Mutual TLS + OAuth2 client credentials

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `method` | enum | Y | `get`, `put`, `post`, `delete` |
| `path` | string | Y | API path |
| `params` | object | N | Query string parameters |
| `data` | object | N | Request body |
| `options.masked` | boolean | N | Mask sensitive data (default `true`) |
| `options.roleCode` | enum | N | `employee`, `manager`, `practitioner`, `administrator`, `supervisor` |

---

#### ADP Vista (`5b2ed01a-628e-413e-8baf-f6d4c01fb53b`)

**Credentials:** `clientId`, `clientSecret`, `companyCode`, `scope`, `loginId`
**Connection Options:** `country`, `paymentUnit`, `legalEntity`
**Auth:** Mutual TLS + OAuth2 client credentials. Hardcoded URLs. Token cached ~30 minutes.

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `method` | enum | Y | `get`, `post` (only two methods) |
| `path` | string | Y | API path |
| `data` | array of objects | **Y** | Request body (**required**, must be array) |

**Notes:** Uses hardcoded `apigateway.vista.adp.com` URLs. Connection options (`country`, `paymentUnit`, `legalEntity`) are sent as request headers.

---

## 6. Plex Connectors (4)

Four connectors for Plex Manufacturing Cloud. The SOAP and UX connectors execute datasources and share the same outbound shape. The API and IAM API connectors use standard REST.

### 6.1 Shared Outbound Shape (Plex SOAP + Plex UX)

Each payload item returns:

```json
{
  "Parameters": {},
  "Rows": [{}],
  "Errors": [{ "request": {}, "message": "string", "statusCode": 0 }]
}
```

- `Parameters` -- Output parameters from the datasource
- `Rows` -- Array of result row objects
- `Errors` -- Present when `returnErrors: true` and a request fails

### 6.2 Shared Pagination Model (Plex SOAP + Plex UX)

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

### 6.3 Plex Classic SOAP (`464e9c3f-dd6c-47af-8078-cda1ff25e966`)

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

### 6.4 Plex UX (`72974b11-897f-47bd-896d-133962ef7bbb`)

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

### 6.5 Plex API (`1cc8888c-bc12-4545-b354-d26f9493f0ac`)

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

### 6.6 Plex IAM API (`9db652bf-9f99-472e-ada2-71f8e92f973f`)

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
- OAuth2 Client Credentials flow -- POSTs to `{url}/op/token`
- Tokens cached for ~9.5 minutes
- Sets `Authorization: Bearer {token}` on each request

---

## 7. Database Connectors (2)

Two connectors for direct database access: Microsoft SQL Server and ODBC.

### 7.1 MSSQL (`bb612514-2129-4fa9-849d-f9f8f6ec1c8e`)

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

#### Parameter Types

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

#### Outbound

Array of result objects:

```json
{
  "resultSets": [[]],
  "output": {},
  "rowsAffected": [0]
}
```

- `resultSets` -- Array of record set arrays (one per SELECT statement or result set)
- `output` -- Output parameter values
- `rowsAffected` -- Array of affected row counts

When `returnErrors: true`, failed items return:

```json
{
  "resultSets": [],
  "output": {},
  "rowsAffected": [0],
  "errors": [{ "message": "...", "info": {}, "code": 0 }]
}
```

#### Notes

- Connection pooling with max 10, min 2, idle timeout 30s
- Requests are executed **sequentially** (not in parallel) -- `for...of` loop
- Non-retryable errors (containing "syntax", "invalid", "permission") abort immediately
- Retry uses exponential backoff via `p-retry`

---

### 7.2 ODBC (`914c3b05-7d13-4fd2-a0f8-fe925e67f066`)

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

#### Outbound

Array of result row objects.

#### Notes

- Uses worker pool (`odbcPool`) to execute queries in separate processes
- Supports Plex and NetSuite ODBC drivers
- NetSuite OAuth: When `credentialsJSON.netsuiteOAuth` is present, fetches a JWT token before executing queries
- Task timeout applied per query execution

---

## 8. Specialized Connectors (15)

15 connectors with unique schemas that don't follow the standard REST method+path pattern.

### 8.1 File Transfer - FTP/SFTP (`396f9092-0c1e-4b3b-ac6d-760094c9466c`)

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

### 8.2 Email - SMTP (`749e15c7-b1d6-4438-9466-7dfe27271573`)

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

### 8.3 AI - OpenAI Chat (`e86501cd-80b4-46ea-97c8-33d31cfef632`)

OpenAI ChatCompletion API. Connection `optionsJSON` is deep-merged with each request as defaults (useful for default system messages).

**Credentials:** `apiKey`

**Inbound:** Array of:

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `model` | string | Y | Model name (e.g. `gpt-4`, `gpt-3.5-turbo`) |
| `messages` | array | Y | Chat messages array |

Additional OpenAI API parameters (temperature, max_tokens, etc.) can be passed as extra properties -- they are forwarded to the `chat.completions.create` call.

**Message roles (via `anyOf`):**

| Role | Required Fields | Description |
|------|----------------|-------------|
| `system` / `user` | `content`, `role` | Content can be string or array of strings |
| `assistant` | `role` | Optional: `content`, `refusal`, `audio`, `tool_calls`, `name` |
| `tool` | `role`, `content`, `tool_call_id` | Tool response message |

**Outbound:** Array of OpenAI ChatCompletion response objects.

---

### 8.4 E-Commerce - Amazon SP-API (`085c0e1b-e0f2-47ae-a81f-1af51f90aeea`)

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

### 8.5 Cloud Services - AWS Lambda (`37766115-8263-4d3c-bae1-984d97372221`)

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

### 8.6 Cloud Services - Google API (`2ab3d71a-2b81-4253-8428-8c359a26f082`)

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

### 8.7 Shipping - FedEx (`d16be6b2-ad5e-4b32-8e54-03f168b947f4`)

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

### 8.8 Shipping - UPS Legacy (`920e8e71-1de6-4160-8129-f16612c0d694`)

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

### 8.9 Shipping - Fuuz-UPS (`906f591e-0cac-41f7-9fc3-bdbae5d6117a`)

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

### 8.10 Shipping - USPS (`c1ce6d2d-0e76-4683-9b06-00812f5514f4`)

XML-based USPS postal API. The connector injects the user's USERID into XML data.

**Credentials:** `UserId`

**Inbound:** Array of:

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `action` | enum | Y | `POST` |
| `path` | string | N | URL path |
| `api` | string | N | USPS API name (sent as `API` query parameter) |
| `data` | string or object | N | XML data. Must contain `USERID=''` attribute -- the connector fills it in. |

**Notes:** Data is sent as a URL query parameter (`XML=...`), not in the request body. Responses are parsed from XML to JSON.

---

### 8.11 ERP/SOAP - NetSuite SOAP (`3c94fb0e-0767-11ec-9a03-0242ac130003`)

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

### 8.12 EDI - EDI Nation (`01ebf215-8d6a-4516-a058-16cc40a25773`)

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

### 8.13 TecCom - File Upload (`e2c23250-2da3-41af-8ea5-abe14a4aa77c`)

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

### 8.14 TecCom - Web Services (`395ab4e2-2ef4-471c-ad42-5fdad7f22d6f`)

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

### 8.15 Multi-Action - Fuuz (`43b90e5f-48a5-437c-b968-6f35a1663257`)

Multi-action connector for external Fuuz instances. Max 100 items per request.

**Credentials:** `ApiKey` (JWT -- decoded to extract URL and username)

**Inbound:** Array of (via `anyOf` -- action determines shape):

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
