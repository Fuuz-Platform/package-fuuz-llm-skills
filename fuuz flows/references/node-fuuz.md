# Fuuz

Nodes for interacting with the Fuuz platform — querying, mutating, aggregating data, executing flows, sending email, generating documents, and more.

---

### Aggregate (`aggregate`)
**Type:** transition

A node used to execute an aggregation pipeline against Fuuz.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `collectionTransform` | string | jsonata | Y | A JSONata expression to generate the collection to aggregate from. |
| `pipelineTransform` | string | jsonata | Y | A JSONata expression to generate the aggregation pipeline. |
| `explain` | boolean | | N | Enable this setting to get the execution plan for the aggregation pipeline. This is often helpful when performance tuning a pipeline. Default: `false`. |

Standard output port.

**Validation:** requireInputNode, requireOutputNode

---

### Execute Flow (`executeFlow`)
**Type:** transition

A node used to execute a deployed flow in Fuuz. Flows will be executed starting from a Request node, and must have a Response node in the flow.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `flowId` | integer | combobox | Y | The flow to execute. If the flow is not deployed, this node will throw an error. |
| `payloadTransform` | string | jsonata | N | The transform to use to format the input before it is given as the payload to the flow's Request node. |

Standard output port.

**Validation:** requireInputNode

---

### Query (`query`)
**Type:** transition

A node used to run a query on data from the Fuuz GraphQL APIs.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `api` | string | | Y | Which Fuuz API to run the query. Options: `application`, `system`. Default: `"application"`. |
| `query` | string | graphql | Y | The query to execute. |
| `variablesTransform` | string | jsonata | Y | A transform producing the variables to pass into the query. Default: `"{}"`. |

Standard output port.

**Validation:** requireInputNode, requireOutputNode

---

### Mutate (`mutate`)
**Type:** transition

A node used to mutate data in the Fuuz GraphQL APIs.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `api` | string | | Y | Which Fuuz API to issue the mutation against. Options: `Application`, `System`. Default: `"Application"`. |
| `mutation` | string | graphql | Y | The mutation to execute. |
| `variablesTransform` | string | jsonata | N | A transform producing the variables to pass into the mutation. Default: `"{}"`. |

Standard output port.

**Validation:** requireInputNode, requireOutputNode

---

### System Email (`systemEmail`)
**Type:** transition

A node used to send email via Fuuz.

Email fields can be provided plain (to@server.com) or formatted ("To Name" <to@server.com>), or as an object with 'name' and 'address' properties. Lists of addresses can be provided as comma-separated strings, or as arrays of strings or objects.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `emailToTransform` | string | jsonata | Y | The email addresses of the recipients. |
| `emailCCTransform` | string | jsonata | N | The email addresses of recipients to CC on the email. |
| `emailBCCTransform` | string | jsonata | N | The email addresses of recipients to BCC on the email. |
| `emailReplyToTransform` | string | jsonata | N | The email address to use for replies to the email. |
| `emailSubjectTransform` | string | jsonata | Y | The subject of the email. |
| `emailHtmlTransform` | string | jsonata | Y | The HTML body of the email. |
| `emailAttachments` | array | | N | A list of attachments to include in the email. See sub-table below. |

**Attachment items:**

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `attachmentFilenameTransform` | string | jsonata | Y | The name of the attached file. |
| `attachmentContentTransform` | string | jsonata | Y | A transform outputting the encoded content of the attachment. |
| `attachmentEncoding` | string | combobox | N | The encoding of the content. Options: `ascii` (ASCII), `utf8` (UTF-8), `base64` (Base64), `hex` (Hexadecimal), `binary` (Binary). |

Standard output port.

**Validation:** requireInputNode

---

### Render Document (`generateDocument`)
**Type:** transition

A node used to generate a document in Fuuz.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `documentDesignTransform` | string | | Y | The document design to generate. |
| `payloadTransform` | string | jsonata | Y | A transform which returns the payload to pass to the document. Default: `"$"`. |
| `filenameTransform` | string | jsonata | N | The file name to use for the generated document. If no file name is supplied, one is auto-generated from the design name and current date and time. |
| `documentRenderFormatTransform` | string | | Y | The format to render the document in. Default: `"\"Pdf\""`. |
| `cultureTransform` | string | jsonata | N | The culture to apply to the rendered document. |
| `returnContent` | boolean | | N | When true, this will include the content of the document in the output. Default: `true`. |
| `saveRenderedFile` | boolean | | N | When true, the document will be saved. Default: `true`. |

Standard output port.

**Validation:** requireInputNode, requireOutputNode

---

### Get Calendar (`getCalendar`)
**Type:** transition

A node used to get a Fuuz Calendar by id or name. The id will override the name field if a calendar is found for that id. If a calendar is not found for the id provided, the name will be used to lookup the calendar. If no calendar is found for the id or name provided an error will be thrown.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `calendarInput` | object | | N | The calendar to return. Accepts an id and/or name, or an array of such objects. |

Standard output port.

**Validation:** requireInputNode, requireOutputNode

---

### Saved Query (`savedQuery`)
**Type:** transition

A node used to execute a saved query in Fuuz.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `savedQueryId` | string | combobox | Y | The saved query to execute. |
| `variablesTransform` | string | jsonata | Y | A transform on the input payload to become the variables to pass into the query. Default: `"{}"`. |

Standard output port.

**Validation:** requireInputNode, requireOutputNode

---

### EDI Translation (`ediIntegration`)
**Type:** transition

Node for integrating with EDI Nation API to read, write, and acknowledge EDI messages. The EDI Nation API will convert X12 and EDIFACT documents to a JSON format providing an easier way to map documents with JSONata and Javascript. The EDI Nation system connection may not be enabled in your environment by default.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `action` | string | jsonata | Y | The action to perform. Options: `read`, `write`, `ack`, `validate`. Read converts raw EDI to JSON; Write converts JSON back to raw EDI; Ack generates an acknowledgement document; Validate checks document validity. |
| `format` | string | jsonata | Y | The document format. Options: `x12` (X12), `edifact` (EDIFACT). |
| `filename` | string | jsonata | N | The name of the file being translated. Visible when action is `read`. |
| `fileContents` | string | jsonata | N | The EDI data to be translated. |

Additional context-dependent Options properties appear based on the selected action and format combination. Each action/format pair (e.g., read+x12, write+edifact) exposes a dedicated Options field (jsonata) pre-populated with sensible defaults for API options such as `ignoreNullValues`, `continueOnError`, `charSet`, `preserveWhitespace`, `detectDuplicates`, `eancomS3`, and others.

Standard output port.

**Validation:** minimumNoteLength: 20, requireChangedName, requireInputNode, requireWalkthrough
