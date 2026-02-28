# Fuuz Connectors Reference

Quick reference for all 42 built-in connectors available in the Fuuz Industrial Operations Platform. For detailed inbound/outbound schemas, see `integration-service-reference.md`. Connectors enable integration with external systems, APIs, and services from within Fuuz data flows.

> **Usage:** Connectors are configured in **Configuration → Connectors** in the Fuuz platform UI, then used in flows via `integrateV2` nodes (backend flows) or `$integrate()` bindings (screen flows).

> **Full Details:** For complete credentials and options schemas, see `system-seeded-values.md` in the fuuz-flows or fuuz-platform references.

---

## Connector Summary by Category

### ERP / MES / Business Systems

| Connector | ID | Auth Type | Description |
|-----------|-----|-----------|-------------|
| **Plex API** | `1cc8888c-bc12-4545-b354-d26f9493f0ac` | Consumer Key | Plex HTTP API |
| **Plex Classic** | `464e9c3f-dd6c-47af-8078-cda1ff25e966` | Username/Password | Plex ERP (legacy) |
| **Plex UX** | `72974b11-897f-47bd-896d-133962ef7bbb` | Username/Password | Plex ERP (modern UI) |
| **Plex IAM API** | `9db652bf-9f99-472e-ada2-71f8e92f973f` | Client ID/Secret | Plex IAM authentication |
| **SAP Cloud** | `34e36d24-64f3-4d59-a764-bab5b851f72d` | Auth Token | SAP Business Application services |
| **SAP Success Factors** | `165219c4-830b-49af-828f-7964d4e4bd08` | Certificate + Private Key | SAP HR platform |
| **Infor** | `07ba704-0eef-11eb-adc1-0242ac120002` | OAuth (Client Credentials) | Infor cloud ERP |
| **NetsuiteSOAP** | `3c94fb0e-0767-11ec-9a03-0242ac130003` | OAuth Token-based | Netsuite SOAP API |
| **NetsuiteREST** | `ac61106b-72c6-4fca-8bc7-0ba3d54fb574` | OAuth Token-based | Netsuite REST API |
| **Dynamics 365** | `808fce4c-6e38-4db8-b4b4-5c45a5a6f1cb` | OAuth (Client Credentials) | Microsoft Dynamics 365 |
| **Arena** | `c6a47b3d-5740-4cec-a9d1-dfba96249d03` | Client ID + Username/Password | Discrete event simulation |

### CRM / Sales / Commerce

| Connector | ID | Auth Type | Description |
|-----------|-----|-----------|-------------|
| **Salesforce** | `44ff6253-ca43-4429-955a-2907803178b8` | Username/Password + Client ID/Secret | CRM platform |
| **WooCommerce** | `09fb753b-6b94-48b6-b70e-31ce7acdddac` | Consumer Key/Secret | WordPress eCommerce |
| **Magento** | `3d3b20ea-f261-40c1-9094-a556e60ce5e9` | Token or Username/Password | eCommerce platform |
| **Square** | `c125d58f-41f5-41f5-b42e-3a63e3441526` | Personal Access Token | Point-of-sale platform |
| **Clover** | `2d259032-b9ea-4904-afbf-d30d4fc142a9` | Auth Token | Point-of-sale system |
| **AmazonSPAPI** | `085c0e1b-e0f2-47ae-a81f-1af51f90aeea` | AWS IAM + OAuth | Amazon Selling Partner API |
| **Channel Advisor** | `1bbd8c15-601e-43f5-b584-e898ba58bfd2` | OAuth (Refresh Token) | Multi-channel commerce |
| **Spiro** | `fa98a8d9-74f3-4ce1-8ef9-4455c31d3ce4` | Auth Token | Sales relationship management |
| **Zoho** | `4ff8769a-bdea-11e8-a355-529269fb1459` | OAuth (Client Credentials) | Online office suite |

### HR / Workforce

| Connector | ID | Auth Type | Description |
|-----------|-----|-----------|-------------|
| **ADP** | `6a6da1f8-b69c-11e8-96f8-529269fb1459` | Client ID/Secret + SSL Cert | HR management |
| **ADP Vista** | `5b2ed01a-628e-413e-8baf-f6d4c01fb53b` | Client ID/Secret + Login | ADP Vista HR API |

### Cloud Services

| Connector | ID | Auth Type | Description |
|-----------|-----|-----------|-------------|
| **AWS Lambda** | `37766115-8263-4d3c-bae1-984d97372221` | AWS Access Key/Secret | Serverless functions |
| **Google API** | `2ab3d71a-2b81-4253-8428-8c359a26f082` | Service Account (Client Email + Private Key) | Google APIs (all services) |
| **OpenAI Chat** | `e86501cd-80b4-46ea-97c8-33d31cfef632` | API Key | OpenAI GPT models |

### Database

| Connector | ID | Auth Type | Description |
|-----------|-----|-----------|-------------|
| **Microsoft SQL Server** | `bb612514-2129-4fa9-849d-f9f8f6ec1c8e` | Username/Password | SQL Server database |
| **ODBC** | `914c3b05-7d13-4fd2-a0f8-fe925e67f066` | Username/Password or Netsuite OAuth | Generic ODBC connections |

> **Note:** Additional SQL databases (MySQL, PostgreSQL, Oracle DB, IBM DB2) are available as **Device Drivers** via the Fuuz Gateway, not as cloud connectors.

### Protocol / Transport

| Connector | ID | Auth Type | Description |
|-----------|-----|-----------|-------------|
| **HTTP** | `05e0b880-7b0d-4ca1-9b8f-30126ffd305a` | Username/Password (optional) | Generic HTTP/REST |
| **FTP** | `396f9092-0c1e-4b3b-ac6d-760094c9466c` | Username + Password/SSH Key | File Transfer Protocol (FTP/SFTP) |
| **SMTP** | `749e15c7-b1d6-4438-9466-7dfe27271573` | Username/Password | Email sending |

### Shipping / Logistics

| Connector | ID | Auth Type | Description |
|-----------|-----|-----------|-------------|
| **UPS** | `920e8e71-1de6-4160-8129-f16612c0d694` | Service Access Token + Username | UPS (legacy) |
| **UPS OAuth** | `c13ebe14-08f3-4c6a-bb1d-c717d8430ab9` | Client ID/Secret + Account Number | UPS (modern OAuth) |
| **Fuuz UPS** | `906f591e-0cac-41f7-9fc3-bdbae5d6117a` | Access License Number | UPS with Fuuz credentials |
| **FedEx** | `d16be6b2-ad5e-4b32-8e54-03f168b947f4` | Client + Parent + User Credentials | Federal Express |
| **USPS** | `c1ce6d2d-0e76-4683-9b06-00812f5514f4` | User ID | US Postal Service |
| **C. H. Robinson** | `3c751b02-b23c-4615-9eba-c7a5dc33c507` | OAuth (Client Credentials) | Freight logistics |

### EDI / Document Exchange

| Connector | ID | Auth Type | Description |
|-----------|-----|-----------|-------------|
| **EDI Nation** | `01ebf215-8d6a-4516-a058-16cc40a25773` | API Key | EDI document processing |
| **TecCom Web Services** | `395ab4e2-2ef4-471c-ad42-5fdad7f22d6f` | Username/Password | TecCom SOAP services |
| **TecCom File Upload** | `e2c23250-2da3-41af-8ea5-abe14a4aa77c` | Client ID/Secret + TecCom ID | TecCom data upload |

### Collaboration / Productivity

| Connector | ID | Auth Type | Description |
|-----------|-----|-----------|-------------|
| **Confluence** | `d7ca2d1a-cc71-47fb-8b26-d316bfaedaf9` | API Key + Email | Atlassian Confluence |
| **QuickBooks** | `88c548f7-4944-4eed-a948-9ce683e02a6f` | OAuth (Client Credentials + Refresh Token) | Accounting platform |

### Platform / Internal

| Connector | ID | Auth Type | Description |
|-----------|-----|-----------|-------------|
| **Fuuz** | `43b90e5f-48a5-437c-b968-6f35a1663257` | API Key | Connect to another Fuuz environment |
| **MFGx** | `970ae55b-7ad0-497e-a77e-f9eb63aff73d` | API Key + Username | MFGx platform (legacy) |
| **Echo** | `050f2838-96da-413d-926a-db9aaf2af67f` | None | Test/debug connector |

> **Note:** SAP RFC integration is available as a **Device Driver** (`sapRfc`) via the Fuuz Gateway, not as a cloud connector. See the Device Driver Types section below.

---

## Authentication Patterns

Connectors use several authentication patterns:

### API Key
Simple key-based authentication. Used by: EDI Nation, OpenAI Chat, Fuuz, Confluence.
```
Credentials: { "apiKey": "your-api-key" }
```

### Username / Password
Basic credentials. Used by: HTTP, SMTP, Plex Classic, Plex UX, Salesforce.
```
Credentials: { "Username": "user", "Password": "pass" }
```

### OAuth Client Credentials
Client ID/Secret with token URL. Used by: Infor, Dynamics 365, ADP, Channel Advisor, C. H. Robinson, Zoho.
```
Credentials: { "clientId": "...", "clientSecret": "...", "grantType": "client_credentials" }
```

### OAuth with Refresh Token
Token-based with automatic refresh. Used by: QuickBooks, Netsuite.
```
Credentials: { "clientId": "...", "clientSecret": "...", "refreshToken": "..." }
```

### Certificate-Based
SSL certificate authentication. Used by: ADP (SSL cert + key), SAP Success Factors (certificate + private key).
```
Credentials: { "certificate": "...", "privateKey": "..." }
```

### Service Account
Service account with private key. Used by: Google API.
```
Credentials: { "clientEmail": "...", "privateKey": "..." }
```

### Authorization Token
Simple bearer token. Used by: Clover, SAP Cloud, Spiro.
```
Credentials: { "Authorization Token": "your-token" }
```

---

## Connectors vs Device Drivers

Fuuz has two integration mechanisms:

| Feature | Connectors | Device Drivers |
|---------|-----------|----------------|
| **Runs in** | Cloud (Fuuz orchestration service) | On-premise (Fuuz Gateway) |
| **Used in** | Backend flows, Screen flows | Gateway flows |
| **Flow node** | `integrateV2` / `$integrate()` | `$executeDeviceFunction()` |
| **Examples** | HTTP, Salesforce, AWS, SMTP | PLC, Modbus, OPC-UA, SQL, MQTT |
| **Authentication** | Configured centrally in Fuuz | Configured per gateway device |
| **Network access** | Internet (cloud-to-cloud) | Local network (on-premise) |

### When to Use Which

- **Connector**: Integrating with a cloud API, SaaS platform, or internet-accessible service
- **Device Driver**: Communicating with on-premise equipment, local databases, or factory-floor devices
- **Both**: Some systems (like Plex, SAP) have both cloud connectors AND gateway device drivers for different use cases

---

## Using Connectors in Flows

### Backend Flows (integrateV2 node)
```json
{
  "nodeType": "integrateV2",
  "connectorId": "05e0b880-7b0d-4ca1-9b8f-30126ffd305a",
  "method": "GET",
  "url": "https://api.example.com/data"
}
```

### Screen Flows ($integrate binding)
```
$integrate({
  "connectorId": "05e0b880-7b0d-4ca1-9b8f-30126ffd305a",
  "method": "POST",
  "url": "https://api.example.com/submit",
  "body": $state.formData
})
```

---

## Device Driver Types (Gateway)

For on-premise integration via the Fuuz Gateway:

| Driver Type | ID | Drivers | Key Functions |
|-------------|-----|---------|---------------|
| **File** | `file` | Local File | Read, Write, List, Delete, Move, Status |
| **HTTP** | `http` | HTTP Client | Request, Status |
| **Modbus** | `modbus` | Modbus TCP | Read/Write Coils, Read/Write Registers, Status |
| **MQTT** | `mqtt` | MQTT Client | Publish, Status |
| **MQTT Sparkplug** | `mqttSparkplug` | Sparkplug B | Device Birth/Death, Publish Data, Status |
| **OPC-UA** | `opcua` | OPCUA Client | Browse, Read, Write, Status |
| **PCCC PLC** | `pccc` | PCCC PLC | Read/Write Tag(s), Status |
| **PLC** | `plc` | Ethernet/IP PLC | Browse, Read/Write Tag(s), Status |
| **Printer** | `printer` | Native Printer, TCP Printer | Print, Print Raw, Print File, Get Jobs, Status |
| **Remote System Call** | `remoteSystemCall` | SAP RFC | Call, Status |
| **Server** | `server` | HTTP Server, MQTT Broker, TCP Server | Status |
| **SQL** | `sql` | IBM DB2, Microsoft SQL, MySQL, Oracle DB | Query, Status |
| **TCP** | `tcp` | TCP Socket | Write, Status |

---

## Device Subscription Types

Subscriptions define how devices push data to Fuuz:

| Subscription Type | ID | Use Case |
|-------------------|-----|----------|
| **File System Change** | `fileSystemChange` | Watch for file changes |
| **HTTP POST Endpoint** | `httpEndpoint` | Receive HTTP POST data |
| **Modbus Values** | `modbusValues` | Poll Modbus registers |
| **MQTT Receive Data** | `mqttBrokerReceiveData` | Receive MQTT broker data |
| **MQTT Topics** | `mqttTopics` | Subscribe to MQTT topics |
| **Node Values** | `nodeValues` | Subscribe to OPC-UA nodes |
| **PCCC Tag Values** | `pcccTagValues` | Subscribe to PCCC PLC tags |
| **Tag Values** | `tagValues` | Subscribe to PLC tags |
| **TCP Receive Data** | `tcpSocketReceiveData` | Receive TCP socket data |
| **TCP Socket** | `tcpSocketSubscription` | Subscribe to TCP socket |

All subscription types support **Store and Forward** — buffering data locally when disconnected from Fuuz and forwarding when reconnected.

---

## Related References

| Reference | Skill | Content |
|-----------|-------|---------|
| `system-seeded-values.md` | fuuz-platform, fuuz-flows | Complete connector credentials and options schemas |
| `platform-architecture.md` | fuuz-platform | Device gateway architecture diagram |
| `fuuz flows/SKILL.md` | fuuz-flows | How to use connectors in flow nodes |
