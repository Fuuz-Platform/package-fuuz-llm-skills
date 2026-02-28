# Integration

Nodes for connecting to external systems and services — HTTP, FTP, databases, ERP systems, cloud APIs, and third-party platforms. Most integration nodes share common properties: `connectionName` (or `connectionNameTransform`) to select a configured connection, `returnErrors` to handle failures gracefully, and `degreeOfParallelism` for batch requests. See overview for details on these shared patterns.

---

### ADP (`adp`)
**Type:** transition

A node used to make ADP requests.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | | Y | The ADP connection to use for the request. |
| `method` | string | | Y | The method to use for the request. Options: `get`, `put`, `post`, `delete`. |
| `pathTransform` | string | jsonata | Y | The path to make the request against. |
| `paramsTransform` | string | jsonata | N | The transform to produce the URL parameters for the request. |
| `dataTransform` | string | jsonata | N | The transform to manipulate the payload input into the body for the request. |
| `masked` | boolean | | N | Whether or not to mask sensitive data that might be returned from ADP. Default: `true`. |
| `roleCode` | string | | N | The role code to use for this request to ADP. Options: `employee`, `manager`, `practitioner`, `administrator`, `supervisor`. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### ADP Vista (`adpVista`)
**Type:** transition

A node used to make a request to the ADP Vista API.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionNameTransform` | string | | N | The ADP Vista connection to use with the request. |
| `path` | string | jsonata | Y | The URL path for the ADP Vista API endpoint. |
| `method` | string | jsonata | Y | The HTTP method to use for the request. Options: `POST`, `GET`. |
| `dataTransform` | string | jsonata | Y | A transform which produces the data to send to the API in the body of the request. |
| `responseTransform` | string | jsonata | N | A transform to reformat the response from the ADP Vista API. Default: `"$"`. |
| `returnErrors` | boolean | | N | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

Standard output port.

---

### Amazon (`amazon`)
**Type:** transition

A node used to make a request to the Amazon Selling Partner API.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | | Y | The Amazon connection to use for the request. |
| `amazonAction` | string | | Y | The Amazon action to execute. Options: `callAPI`, `download`, `upload`. |
| `amazonAPITransform` | string | jsonata | N | A transform which returns the data to call the API with. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### AWS Lambda (`awsLambda`)
**Type:** transition

A node used to interact with AWS Lambda functions.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionNameTransform` | string | | Y | The AWS Lambda connection to use with the request. |
| `lambdaAction` | string | jsonata | Y | The Lambda command to execute. Options: `InvokeCommand` (Invoke Command), `ListFunctionsCommand` (List Functions). |
| `regionTransform` | string | jsonata | N | The region of the AWS data center in which your function is deployed and executed. Default: `"\"us-east-1\""`. |
| `functionNameTransform` | string | jsonata | N | The name of the Lambda function to be executed. Visible when action is `InvokeCommand`. |
| `bodyTransform` | string | jsonata | N | A transform which produces data to send to the API in the request body. Default: `"$"`. |
| `responseTransform` | string | jsonata | N | A transform to reformat the payload returned from the AWS Lambda request. Default: `"$"`. |
| `returnErrors` | boolean | | N | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |
| `degreeOfParallelism` | integer | | N | The number of requests to execute in parallel when providing an array of payloads to the node. Default: `10`. |

Standard output port.

---

### Email (`email`)
**Type:** transition

A node used to send email via SMTP.

Email fields can be provided plain (sender@server.com) or formatted ("Sender Name" <sender@server.com>), or as an object with 'name' and 'address' properties. Lists of addresses can be provided as comma-separated strings, or as arrays of strings or objects.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | | Y | The SMTP connection to use for the request. |
| `emailFromTransform` | string | jsonata | Y | The email address of the sender. For most providers, this must match the account on the connection if provided. |
| `emailToTransform` | string | jsonata | Y | The email addresses of the recipients. |
| `emailSubjectTransform` | string | jsonata | Y | The subject of the email. |
| `emailHtmlTransform` | string | jsonata | Y | The HTML body of the email. |
| `emailAttachments` | array | | N | A list of attachments to include in the email. See sub-table below. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

**Attachment items:**

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `attachmentFilenameTransform` | string | jsonata | Y | The name of the attached file. |
| `attachmentContentTransform` | string | jsonata | Y | The content of the attachment. |
| `attachmentEncoding` | string | | N | The encoding of the content. Options: `ascii` (ASCII), `utf8` (UTF-8), `base64` (Base64), `hex` (Hexadecimal), `binary` (Binary). |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### FTP (`ftp`)
**Type:** transition

A node used to make an FTP request.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | | Y | The FTP connection to use for the request. |
| `ftpAction` | string | | Y | The FTP action to execute. Options: `get`, `post`, `delete`, `move`, `list`. |
| `ftpFileTransform` | string | jsonata | N | The filename to Get, Move, or Delete, or the name of the file to Post. Visible for get/move/delete/post actions. |
| `ftpDestinationTransform` | string | jsonata | N | The destination folder to Move the file to, or the folder to List files in. Visible for move/list actions. |
| `ftpDataTransform` | string | jsonata | N | A transform which returns the data to Post to the file. Visible for post action. |
| `ftpMatch` | string | | N | A regular expression to execute against Listed file names. Only files with a name matching the regular expression will be returned. Visible for list action. |
| `ftpFirst` | number | | N | The number of files to return from a List action, counting from the start of the list. Visible for list action. |
| `ftpLast` | number | | N | The number of files to return from a List action, counting from the end of the list. Visible for list action. |
| `encoding` | string | | N | The encoding to use for a file when pulling it from FTP. Options: `utf8`, `base64`, `binary`, `hex`, `ascii`. Defaults to utf8; for PDF files base64 encoding is needed. Visible for get/post/put actions. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### Fuuz (`fuuz`)
**Type:** transition

A node used to make a request to an external Fuuz API.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | jsonata | Y | The Fuuz connection to use with the request. |
| `action` | string | jsonata | Y | The type of request. Options: `query` (GraphQL Query), `mutation` (GraphQL Mutation), `publish` (Publish Topic Message), `flow` (Execute Flow), `savedQuery` (Run Saved Query), `savedScript` (Run Saved Script). |
| `api` | string | | N | Which Fuuz API to issue the query or mutation against. Options: `application`, `system`. Default: `"application"`. Visible for query/mutation actions. |
| `query` | string | graphql | N | The GraphQL query to execute. Visible for query action. |
| `mutation` | string | graphql | N | The GraphQL mutation to execute. Visible for mutation action. |
| `savedQueryId` | string | jsonata | N | The id of the saved query to execute. Visible for savedQuery action. |
| `variables` | string | jsonata | N | A transform producing the data to be passed through the selected API. Default: `"{}"`. Visible for query/mutation/savedQuery actions. |
| `savedScriptId` | string | jsonata | N | The id of the saved script to execute. Visible for savedScript action. |
| `flowId` | string | jsonata | N | The id of the flow to execute. Visible for flow action. |
| `topicName` | string | jsonata | N | The topic name to publish to. Visible for publish action. |
| `payloadTransform` | string | jsonata | N | A transform producing the data to be passed through the selected API. Default: `"{}"`. Visible for flow/savedScript actions. |
| `topicMessages` | string | jsonata | N | A transform producing an array of messages to pass to the selected Topic. Default: `"[]"`. Visible for publish action. |
| `responseTransform` | string | jsonata | Y | A transform to reformat the response from the Fuuz API. Default: `"$"`. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "errors" property rather than aborting flow execution. Default: `false`. |
| `degreeOfParallelism` | integer | | Y | The number of requests to execute in parallel when providing an array of payloads to the node. Default: `10`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### Google BigQuery API (`googleBigQuery`)
**Type:** transition

A node used to make a request to Google BigQuery.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionNameTransform` | string | | Y | The Google connection to use with the request. |
| `clientVersion` | string | jsonata | Y | The Google Client API Version. Options: `v2` (V2). |
| `clientAPIResource` | string | jsonata | Y | The Google API Resource to access. Options: `datasets`, `jobs`, `models`, `projects`, `routines`, `rowAccessPolicies`, `tabledata`, `tables`. |
| `clientAPIAction` | string | jsonata | Y | The Google API Action to execute. Available actions vary by selected resource (e.g., get, insert, delete, list, query, cancel, patch, update, undelete, getServiceAccount, getIamPolicy, setIamPolicy, testIamPermissions, insertAll, getQueryResults). |
| `projectIdTransform` | string | jsonata | N | The ID of the Google Cloud project. |
| `datasetIdTransform` | string | jsonata | N | The ID of the dataset. |
| `jobIdTransform` | string | jsonata | N | The ID of the BigQuery job. |
| `modelIdTransform` | string | jsonata | N | The ID of the BigQuery ML model. |
| `routineIdTransform` | string | jsonata | N | The ID of the routine. |
| `tableIdTransform` | string | jsonata | N | The ID of the table. |
| `resourceTransform` | string | jsonata | N | The full resource identifier for IAM policy actions. |
| `bodyTransform` | string | jsonata | N | Request body. Expects `{ "requestBody": { ... } }`. Default: `"$"`. |
| `optionsTransform` | string | jsonata | N | Optional parameters to pass with the request body. Default: `"{}"`. |
| `responseTransform` | string | jsonata | N | A transform to reformat the response. Default: `"$"`. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |
| `degreeOfParallelism` | integer | | Y | The number of requests to execute in parallel when providing an array of payloads to the node. Default: `10`. |

Standard output port.

---

### Google Drive API (`googleDrive`)
**Type:** transition

A node used to make a request to Google Drive API.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionNameTransform` | string | | Y | The Google connection to use with the request. |
| `clientVersionTransform` | string | | Y | The Google Client API Version. Options: `v2`, `v3`. |
| `clientAPIResourceTransform` | string | | Y | The Google API Resource. Options: `about`, `changes`, `channels`, `comments`, `drives`, `files`, `permissions`, `replies`, `revisions`. |
| `clientAPIActionTransform` | string | | Y | The Google API Action. Options: `create`, `get`, `update`, `delete`, `copy`, `emptyTrash`, `export`, `generateIds`, `getStartPageToken`, `list`, `listLabels`, `modifyLabels`, `stop`, `watch`, `hide`, `unhide`. |
| `bodyTransform` | string | jsonata | N | Request body. Default: `"$"`. |
| `optionsTransform` | string | jsonata | N | Optional parameters. Default: `"{}"`. |
| `responseTransform` | string | jsonata | N | A transform to reformat the response. Default: `"$"`. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |
| `degreeOfParallelism` | integer | | Y | The number of requests to execute in parallel when providing an array of payloads to the node. Default: `10`. |

Standard output port.

---

### HTTP (`http`)
**Type:** transition

A node used to make an HTTP request.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | | Y | The HTTP connection to use for the request. |
| `httpMethod` | string | | Y | The HTTP method to use for the request. Options: `get`, `put`, `post`, `delete`. |
| `httpPathTransform` | string | jsonata | Y | The HTTP path to make the request against. |
| `httpHeadersTransform` | string | jsonata | N | A transform producing the HTTP headers to include with the request. |
| `httpDataTransform` | string | jsonata | N | The transform to manipulate the payload input into the data for the request. |
| `returnErrors` | boolean | | Y | When true, failed requests return an "error" property at the same level as a standard response. Default: `false`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### Infor (`infor`)
**Type:** transition

A node used to make a request to the Infor (REST) API.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | | Y | The Infor connection to use with the request. |
| `path` | string | jsonata | Y | The URL path for the Infor API endpoint. |
| `method` | string | | Y | The HTTP method. Options: `post`, `get`, `put`, `delete`. |
| `dataTransform` | string | jsonata | N | A transform which produces the data to send in the request body. |
| `returnErrors` | boolean | | N | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### Integrate (`integrateV2`)
**Type:** transition

A node used to integrate with external systems. Input to the node can be a single payload, or an array of payloads to make many requests.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionNameTransform` | string | | Y | Which integration connection to use for the request. |
| `requestTransform` | string | jsonata | Y | A transform to populate the integrate request. Default: `"$"`. |
| `responseTransform` | string | jsonata | Y | A transform to reformat the payload returned from the integrate request. Default: `"$"`. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |
| `degreeOfParallelism` | integer | | Y | The number of requests to execute in parallel when providing an array of payloads to the node. Default: `10`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### Magento (`magento`)
**Type:** transition

A node used to make a request to the Magento (REST) API.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | | Y | The Magento connection to use with the request. |
| `path` | string | jsonata | Y | The URL path for the Magento API endpoint. |
| `method` | string | | Y | The HTTP method. Options: `post`, `get`, `put`, `delete`. |
| `filtersTransform` | string | jsonata | N | A transform producing the filters for the request. |
| `dataTransform` | string | jsonata | N | A transform producing the data for the request body. |
| `returnErrors` | boolean | | N | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### SQL Server (`mssql`)
**Type:** transition

A node used to interact with Microsoft SQL Server.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | | Y | The ODBC connection to use for the request. |
| `query` | string | | Y | The SQL query to execute. Default: `""`. |
| `parameters` | array | | N | A list of parameters to pass to the query. See sub-table below. |
| `procedure` | boolean | | Y | Whether or not you are executing a stored procedure. Default: `false`. |
| `maxRetries` | integer | | Y | The maximum number of times to retry a valid, but unsuccessful request. Default: `0`. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

**Parameter items:**

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `name` | string | | Y | The name of this parameter. |
| `dataType` | string | | Y | Options: `BigInt`, `Bit`, `Char`, `Date`, `DateTime`, `DateTime2`, `DateTimeOffset`, `Decimal`, `Float`, `Int`, `Money`, `NChar`, `NText`, `NVarChar`, `Numeric`, `Real`, `SmallDateTime`, `SmallInt`, `SmallMoney`, `Text`, `Time`, `TinyInt`, `UniqueIdentifier`, `VarChar`, `Xml`. |
| `valueTransform` | string | jsonata | N | A JSONata transform to determine the value for this parameter. |
| `length` | integer | | N | The length of the data type. |
| `scale` | integer | | N | The scale of the data type. |
| `precision` | integer | | N | The precision of the data type. |
| `output` | boolean | | N | Whether this is an output parameter. Default: `false`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### Netsuite REST (`netsuiteRest`)
**Type:** transition

A node used to make a request to the Netsuite REST API.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionNameTransform` | string | | Y | The Netsuite REST connection to use for the request. |
| `netsuiteEndpoint` | string | | N | The Netsuite path to query. |
| `netsuiteRestAction` | string | | Y | The HTTP action. Options: `GET`, `POST`, `PATCH`, `PUT`, `DELETE`. |
| `headers` | string | jsonata | N | Optional request headers. |
| `netsuiteRestParameters` | string | jsonata | N | Additional request parameters. |
| `netsuiteRestBody` | string | jsonata | N | Body content sent with the request. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### Netsuite SOAP (`netsuiteSoap`)
**Type:** transition

A node used to make a request to the Netsuite SOAP API.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | | Y | The Netsuite SOAP connection to use for the request. |
| `netsuiteSoapAction` | string | | Y | The SOAP action to execute. Options: `login`, `ssoLogin`, `mapSso`, `changePassword`, `changeEmail`, `logout`, `add`, `delete`, `search`, `searchMore`, `searchMoreWithId`, `searchNext`, `update`, `upsert`, `addList`, `deleteList`, `updateList`, `upsertList`, `get`, `getList`, `getAll`, `getSavedSearch`, `getCustomizationId`, `initialize`, `initializeList`, `getSelectValue`, `getItemAvailability`, `getBudgetExchangeRate`, `getCurrencyRate`, `getDataCenterUrls`, `getPostingTransactionSummary`, `getServerTime`, `attach`, `detach`, `updateInviteeStatus`, `updateInviteeStatusList`, `asyncAddList`, `asyncUpdateList`, `asyncUpsertList`, `asyncDeleteList`, `asyncGetList`, `asyncInitializeList`, `asyncSearch`, `getAsyncResult`, `checkAsyncStatus`, `getDeleted`. |
| `netsuiteSoapTransform` | string | jsonata | Y | SOAP based XML body to send with the request. |
| `returnXML` | boolean | | Y | When true, successful responses will be returned as XML. Default: `false`. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### ODBC (`odbcV2`)
**Type:** transition

A node used to make an ODBC query.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionNameTransform` | string | | Y | The ODBC connection to use for the request. |
| `query` | string | | Y | The SQL query to execute. Default: `""`. |
| `variableTransform` | string | jsonata | N | A transform producing an array of values to bind with the SQL query. Default: `"[]"`. |
| `maxRetries` | integer | | Y | The maximum number of times to retry. Default: `0`. |
| `responseTransform` | string | jsonata | Y | A transform to reformat the response. Default: `"$"`. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "Errors" property rather than aborting flow execution. Default: `false`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### OpenAI Chat (`openaiChat`)
**Type:** transition

A node used to interact with OpenAI Chat GPT models.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | | N | The OpenAI connection to use for the request. |
| `model` | string | | Y | The model to use. Refer to the OpenAI documentation for a list of models. |
| `messages` | string | jsonata | Y | A transform producing the messages array for chat completions. |
| `additionalOptions` | string | jsonata | N | Additional options to be applied to this operation. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### Plex API (`plexApi`)
**Type:** transition

A node used to make Plex API requests.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | | Y | The Plex API connection. Supports Plex API and Plex IAM API connections. |
| `method` | string | | Y | The method. Options: `get`, `put`, `post`, `patch`, `delete`. |
| `pathTransform` | string | jsonata | Y | The path to make the request against. |
| `paramsTransform` | string | jsonata | N | URL parameters for the request. |
| `bodyTransform` | string | jsonata | N | The body for the request. |
| `tenantIdTransform` | string | jsonata | N | The Tenant ID. Does nothing when using Plex IAM API. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### Plex Datasource (`plexDatasourceV2`)
**Type:** transition

A node used to call a Plex datasource.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionNameTransform` | string | | Y | The Plex integration connection. Supports Plex and Plex UX connections. |
| `datasourceKey` | string | | Y | The key of the datasource to execute. |
| `parameterTransform` | string | jsonata | Y | Parameters to send to the Plex datasource. Keys should align with datasource parameter names. Default: `"{}"`. |
| `responseTransform` | string | jsonata | Y | A transform to reformat the response. Default: `"$"`. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "Errors" property rather than aborting flow execution. Default: `false`. |
| `degreeOfParallelism` | integer | | Y | The number of requests to execute in parallel when providing an array of payloads. Default: `10`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### Quickbooks (`quickbooks`)
**Type:** transition

A node used to make Quickbooks API requests.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | | Y | The Quickbooks connection to use for the request. |
| `method` | string | | Y | The method. Options: `get`, `put`, `post`, `patch`, `delete`. |
| `headersTransform` | string | jsonata | N | HTTP Headers for the request. |
| `pathTransform` | string | jsonata | Y | The path to make the request against. |
| `paramsTransform` | string | jsonata | N | URL parameters for the request. |
| `bodyTransform` | string | jsonata | N | The body for the request. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### Salesforce (`salesforce`)
**Type:** transition

A node used to make a request to the Salesforce REST API.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | | Y | The Salesforce connection to use with the request. |
| `path` | string | jsonata | Y | The URL path for the Salesforce API endpoint. |
| `method` | string | | Y | The HTTP method. Options: `post`, `get`, `put`, `delete`, `patch`, `head`. |
| `dataTransform` | string | jsonata | N | A transform producing the data for the request body. |
| `paramsTransform` | string | jsonata | N | A transform producing the params for the request. |
| `returnErrors` | boolean | | N | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |
| `pagination` | boolean | | N | When true, automatically retrieves and merges paginated results. Default: `false`. |
| `degreeOfParallelism` | integer | | N | The number of parallel requests for array payloads. Default: `10`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### Success Factors (`successFactors`)
**Type:** transition

A node used to make a request to the Success Factors (REST) API.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionNameTransform` | string | | N | The Success Factors connection to use with the request. |
| `path` | string | jsonata | Y | The URL path for the API endpoint. |
| `method` | string | jsonata | Y | The HTTP method. Options: `POST`, `GET`, `PUT`, `DELETE`, `PATCH`. |
| `dataTransform` | string | jsonata | N | A transform producing the data for the request body. |
| `responseTransform` | string | jsonata | N | A transform to reformat the response. Default: `"$"`. |
| `returnErrors` | boolean | | N | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

Standard output port.

---

### TecCom File Upload (`tecComFileUpload`)
**Type:** transition

A node used to convert JSON to a CSV and send to TecCom.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionNameTransform` | string | | N | The TecCom connection to use with the request. |
| `requestMode` | string | | Y | The request mode. Options: `REPLACE`, `MODIFY`. |
| `files` | array | | Y | The files to upload. See sub-table below. |
| `responseTransform` | string | jsonata | N | A transform to reformat the response. Default: `"$"`. |
| `returnErrors` | boolean | | N | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

**File items:**

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `fileName` | string | | N | Options: `article`, `assortment`, `article_data`, `sales_uom`, `buyer_grouping`, `availability`, `article_description`, `article_buyer`, `alternative`, `condition_group`, `price`, `alc`, `packaging`, `external_document`. |
| `data` | string | jsonata | N | The data to upload. Default: `"$"`. |

Standard output port.

---

### TecCom Web Service (`tecComWebService`)
**Type:** transition

A node used to make web service requests to TecCom.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionNameTransform` | string | | N | The TecCom connection to use with the request. |
| `functionId` | string | | Y | The Id of the web service function. |
| `parameterTransform` | string | jsonata | Y | Parameters for the TecCom Web Service Function. Each element becomes another request. Default: `"{}"`. |
| `responseTransform` | string | jsonata | N | A transform to reformat the response. Default: `"$"`. |
| `returnErrors` | boolean | | N | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

Standard output port.

---

### United Parcel Service (`ups`)
**Type:** transition

A node used to make a request to the UPS API.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionNameTransform` | string | | N | The UPS connection to use with the request. |
| `path` | string | jsonata | N | The URL path for the UPS API endpoint. |
| `action` | string | jsonata | N | The HTTP method. Options: `POST`, `GET`, `PUT`, `DELETE`, `PATCH`. |
| `dataTransform` | string | jsonata | N | A transform producing the data for the request body. |
| `responseTransform` | string | jsonata | N | A transform to reformat the response. Default: `"$"`. |
| `returnErrors` | boolean | | N | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

Standard output port.

---

### Woocommerce (`woocommerce`)
**Type:** transition

A node used to make a request to the Woocommerce (REST) API.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | | Y | The Woocommerce connection to use with the request. |
| `path` | string | jsonata | Y | The URL path for the Woocommerce API endpoint. |
| `method` | string | | Y | The HTTP method. Options: `POST`, `GET`, `PUT`, `DELETE`. |
| `version` | string | | N | The Version of Woocommerce. Options: `v2`, `v3`. |
| `parametersTransform` | string | jsonata | N | A transform producing the parameters for the request. |
| `dataTransform` | string | jsonata | N | A transform producing the data for the request body. |
| `returnErrors` | boolean | | N | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough
