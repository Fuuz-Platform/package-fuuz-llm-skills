# Fuuz Platform — System Seeded Values Reference

This document provides a comprehensive reference for all system-seeded (built-in) values across the Fuuz Industrial Operations Platform. These values are pre-configured in every Fuuz environment and define the foundational types, drivers, connectors, settings, and configurations that the platform operates on.

Use this reference when building Fuuz applications to understand the available system enumerations, connector options, device driver configurations, and platform settings.

> **Source:** Fuuz platform GraphQL API schema exports from [fuuz.com](https://fuuz.com)

---

## Table of Contents

1. [Application Architecture](#application-architecture)
   - [Application Component Types](#applicationcomponenttype)
   - [Tenant Types](#tenanttype)
2. [Data Models](#data-models)
   - [Data Model Types](#datamodeltype)
   - [Data Model Kinds](#datamodelkind)
   - [Data Model Custom Field Types](#datamodelcustomfieldtype)
   - [Data Model Deployment Log Types](#datamodeldeploymentlogtype)
3. [Data Flows](#data-flows)
   - [Data Flow Types](#dataflowtype)
   - [Data Flow Deployment Log Types](#dataflowdeploymentlogtype)
4. [Connectors](#connectors)
   - [Connectors](#connector)
5. [Device Gateway](#device-gateway)
   - [Device Driver Types](#devicedrivertype)
   - [Device Drivers](#devicedriver)
   - [Device Subscription Types](#devicesubscriptiontype)
   - [Device Gateway Functions](#devicegatewayfunction)
6. [Scheduling](#scheduling)
   - [Schedule Types](#scheduletype)
   - [Scheduling Constraint Types](#schedulingconstrainttype)
7. [Saved Queries](#saved-queries)
   - [Saved Query Operation Types](#savedqueryoperationtype)
8. [Package Installation](#package-installation)
   - [Package Installation Task Types](#packageinstallationtasktype)
   - [Package Installation Log Types](#packageinstallationlogtype)
9. [Settings](#settings)
   - [Setting Types](#settingtype)
   - [Setting Input Types](#settinginputtype)
   - [System Settings](#setting)
10. [Units of Measure](#units-of-measure)
   - [Unit Types](#unittype)

---

## Application Architecture

Core building blocks that define how Fuuz applications are structured.

### Application Component Types

**System Model:** `applicationComponentType`  
**Count:** 3 seeded values

| ID | Name | Description |
|-----|------|-------------|
| `dataFlow` | Data Flow | Data Flows define app business logic in a flow-based programming paradigm. Flows can be run directly or run automatically on a schedule or in response to events in Fuuz. |
| `dataModel` | Data Model | Data Models define data storage for an app, with a fully-featured GraphQL API automatically provided for every model. |
| `screen` | Screen | Screens define the user interface through which end users interact with an app. |

---

### Tenant Types

**System Model:** `tenantType`  
**Count:** 5 seeded values

| ID | Label | Description | Allow Creation |
|-----|------|-------------|----------------|
| `administration` | Administration | Administration tenants are used to manage the enterprise. These tenants are where user access and permissions, app installations, licensing, and other enterprise-wide configuration is managed. | False |
| `application` | Application | Application tenants contain a Fuuz app and related plugins. These tenants have limited access to the Fuuz designer tools in production. | True |
| `applicationDevelopment` | Application Development | Application Development tenants contain Fuuz tools to build an app and related plugins. | True |
| `custom` | Custom | Custom tenants have the full set of Fuuz custom application tools available. | True |
| `integration` | Integration | Integration tenants provide tools for seamless integration between Fuuz Apps and external applications, ensuring efficient data exchange and workflow optimization. | True |

---

## Data Models

Types and configurations that govern how data is stored and structured in Fuuz.

### Data Model Types

**System Model:** `dataModelType`  
**Count:** 3 seeded values

| ID | Name | Description |
|-----|------|-------------|
| `master` | Master | Master data represents the core or standardized information required to create transactional data in an Application. It typically includes entities such as customers, products, suppliers, or employ... |
| `setup` | Setup | Setup data refers to the foundational information required to configure an Application. It typically includes status, settings, and types that establish the state or behavior of an App. This data w... |
| `transactional` | Transactional | Transaction data captures the specific activities, events, or interactions that occur within the Application. It contains operational details of individual transactions, such as sales orders, purch... |

---

### Data Model Kinds

**System Model:** `dataModelKind`  
**Count:** 2 seeded values

| ID | Label | Description |
|-----|------|-------------|
| `embeddable` | Embeddable | Embeddable data models can be nested within Reference data models. They cannot be stored directly. |
| `reference` | Reference | Reference data models are the default data model for MFGx. They are stored independently and can contain embeddable data models or reference other reference data models. |

---

### Data Model Custom Field Types

**System Model:** `dataModelCustomFieldType`  
**Count:** 8 seeded values

| ID | Label | Description |
|-----|------|-------------|
| `date` | Date | Date fields are used to store a date without a time. |
| `dateTime` | Date & Time | Date & Time fields are used to store a date with a time. |
| `decimal` | Decimal | Decimal fields are used to store decimal numbers. |
| `integer` | Integer | Integer fields are used to store positive or negative whole numbers. |
| `markdown` | Markdown | Markdown fields are used to store markdown-formatted text. |
| `text` | Text | Text fields are used to store short strings of text. |
| `time` | Time | Time fields are used to store a time without a date. |
| `trueFalse` | True / False | True/False ("boolean") fields are used to store true or false values. |

---

### Data Model Deployment Log Types

**System Model:** `dataModelDeploymentLogType`  
**Count:** 6 seeded values

| ID | Label | Description |
|-----|------|-------------|
| `debug` | Debug | This log contains diagnostic information about a specific behavior. |
| `error` | Error | This log contains information about an important procedural failure. |
| `fatal` | Fatal | This log contains information about a shutdown due to severe problems. |
| `info` | Info | This log contains information about normal, significant behavior. |
| `trace` | Trace | This log contains information about the exact details of a program state. |
| `warn` | Warn | This log contains information about unusual behavior outside of errors. |

---

## Data Flows

Types and configurations for Fuuz's flow-based programming engine that defines business logic.

### Data Flow Types

**System Model:** `dataFlowType`  
**Count:** 5 seeded values

Data flow types define the environment and purpose of a data flow within the Fuuz platform.

#### Document

**ID:** `Document`  
**Environment:** Backend  
**Description:** Data flows which generate and return base-64 encoded documents from MFGx.

*Environment Detail:* Data flows which run on the MFGx orchestration service.  Backend flows typically interact with APIs and run on a schedule or in response to events or requests

---

#### Edge

**ID:** `Edge`  
**Environment:** Gateway  
**Description:** Data flows which collect data from on-premise devices and perform pre-processing or aggregation before sending it to the cloud.

*Environment Detail:* Data flows which run in an instance of the Device Gateway. Gateway flows typically interact with on-premise devices through device drivers and functions.

---

#### Integration

**ID:** `Integration`  
**Environment:** Backend  
**Description:** Data flows which move data between business systems.

*Environment Detail:* Data flows which run on the MFGx orchestration service.  Backend flows typically interact with APIs and run on a schedule or in response to events or requests

---

#### Screen

**ID:** `Screen`  
**Environment:** Web  
**Description:** Data flows which run on a Fuuz screen and can interact with users and screen elements.

*Environment Detail:* Data flows which run in a user's web browser.  Web flows typically interact directly with a user, presenting forms and navigating between screens, and with MFGx APIs.

---

#### System

**ID:** `System`  
**Environment:** Backend  
**Description:** System-level data flows which primarily work with MFGx data and events.

*Environment Detail:* Data flows which run on the MFGx orchestration service.  Backend flows typically interact with APIs and run on a schedule or in response to events or requests

---

### Data Flow Deployment Log Types

**System Model:** `dataFlowDeploymentLogType`  
**Count:** 6 seeded values

| ID | Label | Description | Sort Order |
|-----|------|-------------|------------|
| `Debug` | Debug | This log contains diagnostic information about a specific behavior. | 1 |
| `Error` | Error | This log contains information about an important procedural failure. | 4 |
| `Fatal` | Fatal | This log contains information about a shutdown due to severe problems. | 5 |
| `Info` | Info | This log contains information about normal, significant behavior. | 2 |
| `Trace` | Trace | This log contains information about the exact details of a program state. | 0 |
| `Warn` | Warn | This log contains information about unusual behavior outside of errors. | 3 |

---

## Connectors

Pre-built integration connectors for connecting Fuuz to external systems, APIs, and services.

### Connectors

**System Model:** `connector`  
**Count:** 44 seeded values

The following connectors are pre-built into the Fuuz platform for integrating with external systems. Each connector defines an options schema (default connection options) and a credentials schema (authentication fields).

#### EDI Nation

**ID:** `01ebf215-8d6a-4516-a058-16cc40a25773`  
**Description:** A connector to interact with the EDI Nation API. For more information, visit https://support.edifabric.com/hc/en-us/sections/360005605638-EDI-API-Reference

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `accessToken` | string | Yes | The API Key provided by EDI Nation |

---

#### Echo

**ID:** `050f2838-96da-413d-926a-db9aaf2af67f`  
**Description:** A cave or large room.

---

#### HTTP

**ID:** `05e0b880-7b0d-4ca1-9b8f-30126ffd305a`  
**Description:** HyperText Transfer Protocol.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `Password` | string | No | Password |
| `Username` | string | No | Username |

**Options:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `Headers` | object | No |  |

---

#### Infor

**ID:** `07ba704-0eef-11eb-adc1-0242ac120002`  
**Description:** Infor focuses on business applications for organizations delivered via cloud computing as a service.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `accessTokenUrl` | string | Yes | Access Token Url (pu+ot) |
| `clientId` | string | Yes | Client ID (ci) |
| `clientSecret` | string | Yes | Client Secret (cs) |
| `grantType` | string | Yes | Grant Type |
| `password` | string | Yes | Password (sask) |
| `scope` | string | No | Scope |
| `userName` | string | Yes | Username (saak) |

---

#### AmazonSPAPI

**ID:** `085c0e1b-e0f2-47ae-a81f-1af51f90aeea`  
**Description:** The Selling Partner API is a REST-based API that helps Amazon sellers programmatically access various data.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `AWS_ACCESS_KEY_ID` | string | Yes | Access Key ID |
| `AWS_SECRET_ACCESS_KEY` | string | Yes | Secret Access Key |
| `AWS_SELLING_PARTNER_ROLE` | string | Yes | Selling Partner Role |
| `SELLING_PARTNER_APP_CLIENT_ID` | string | Yes | Client ID |
| `SELLING_PARTNER_APP_CLIENT_SECRET` | string | Yes | Client Secret |

**Options:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `access_token` | string | No | Access Token |
| `auto_request_throttled` | boolean | No | Auto Request Throttled |
| `auto_request_tokens` | boolean | No | Auto Request Tokens |
| `endpoints_versions` | object | No | Endpoints Versions |
| `only_grantless_operations` | boolean | No | Allow only grantless operations |
| `refresh_token` | string | No | Refresh Token |
| `region` | string | No | Region |
| `role_credentials` | object | No | Role Credentials |
| `use_sandbox` | boolean | No | Use Sandbox |
| `version_fallback` | boolean | No | Version Fallback |

---

#### WooCommerce

**ID:** `09fb753b-6b94-48b6-b70e-31ce7acdddac`  
**Description:** WooCommerce is a flexible, open-source eCommerce solution built on WordPress.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `consumerKey` | string | Yes | Consumer Key |
| `consumerSecret` | string | Yes | Consumer Secret |

---

#### SAP Success Factors

**ID:** `165219c4-830b-49af-828f-7964d4e4bd08`  
**Description:** SAP Success Factors API Connection

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `apiKey` | string | Yes | API Key |
| `audiences` | string | No | audiences |
| `certificate` | string | Yes | Certificate |
| `companyId` | string | Yes | Comapny Id |
| `issuer` | string | No | issuer |
| `privateKey` | string | Yes | Private Key |
| `userType` | string | No | User Type |
| `username` | string | Yes | Usermane |

---

#### Channel Advisor

**ID:** `1bbd8c15-601e-43f5-b584-e898ba58bfd2`  
**Description:** Channel Advisor API Connection

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `clientId` | string | Yes | Application ID |
| `clientSecret` | string | Yes | Shared Secret |
| `grantType` | string | Yes | Grant Type |
| `redirectUri` | string | No | Redirect URL |
| `refreshToken` | string | Yes | Refresh Token |

---

#### Plex API

**ID:** `1cc8888c-bc12-4545-b354-d26f9493f0ac`  
**Description:** The Plex API Connector allows you to connect to Plex's HTTP API.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `consumerKey` | string | Yes | The Consumer Key to use when authenticating. |

**Options:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `tenantId` | string | No | The Plex Tenant ID. The connection has a default Tenant ID of the main enterprise PCN. The ID for additional PCN's under the enterprise can be found through the Tenant Directory API endpoint. |

---

#### Google API

**ID:** `2ab3d71a-2b81-4253-8428-8c359a26f082`  
**Description:** A Google API Connector that utilizes Google's API Client. This list contains all of the APIs supported by the Google API Client: https://googleapis.dev/nodejs/googleapis/latest/

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `clientEmail` | string | Yes | The Client Email to use when authenticating |
| `privateKey` | string | Yes | The Private Key to use when authenticating |

**Options:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `scopes` | array | Yes | Scopes |

---

#### Clover

**ID:** `2d259032-b9ea-4904-afbf-d30d4fc142a9`  
**Description:** An integrated point-of-sale system.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `Authorization Token` | string | Yes | Authorization Token |

---

#### SAP Cloud

**ID:** `34e36d24-64f3-4d59-a764-bab5b851f72d`  
**Description:** Business Application services.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `Authorization Token` | string | Yes | Authorization Token |

**Options:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `API URL` | string | Yes | API URL |

---

#### AWS Lambda

**ID:** `37766115-8263-4d3c-bae1-984d97372221`  
**Description:** A connector that interacts with AWS Lambda.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `accessKeyId` | string | Yes | The ID for your AWS credentials. |
| `secretAccessKey` | string | Yes | The secret key used to authenticate requests. |
| `sessionToken` | string | No | A temporary token only used with temporary credentials. |

---

#### TecCom Web Services

**ID:** `395ab4e2-2ef4-471c-ad42-5fdad7f22d6f`  
**Description:** A connector to use TecCom SOAP Web Services.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `password` | string | Yes | Password |
| `username` | string | Yes | User Name |

**Options:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `language` | string | No | Language |

---

#### FTP

**ID:** `396f9092-0c1e-4b3b-ac6d-760094c9466c`  
**Description:** File Transfer Protocol.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `Host Key` | string | No | Host Key |
| `Host Key Hash` | string | No | Host Key Hash |
| `Passphrase` | string | No | Passphrase |
| `Password` | string | No | Password |
| `SSH Key` | string | No | SSH Key |
| `Username` | string | Yes | Username |

**Options:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `Character Encoding` | string | No | Character Encoding |
| `Chunk Size` | string | No | Chunk Size |
| `Port` | integer | No | Port |
| `Protocol` | string | Yes | Protocol |
| `cipher` | array | No | Cipher |
| `hmac` | array | No | HMAC |
| `kex` | array | No | Kex |
| `serverHostKey` | array | No | Server Host Key |

---

#### C. H. Robinson

**ID:** `3c751b02-b23c-4615-9eba-c7a5dc33c507`  
**Description:** C. H. Robinson API Connection

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `audience` | string | Yes | Audience |
| `clientId` | string | Yes | Client ID |
| `clientSecret` | string | Yes | Client Secret |
| `grantType` | string | Yes | Grant Type |

---

#### NetsuiteSOAP

**ID:** `3c94fb0e-0767-11ec-9a03-0242ac130003`  
**Description:** Netsuite SOAP based API connector

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `account` | string | Yes | Account ID |
| `consumerKey` | string | Yes | Consumer Key |
| `consumerSecret` | string | Yes | Consumer Secret |
| `tokenId` | string | Yes | Token ID |
| `tokenSecret` | string | Yes | Token Secret |

**Options:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `endpointVersion` | string | Yes | Endpoint Version |

---

#### Magento

**ID:** `3d3b20ea-f261-40c1-9094-a556e60ce5e9`  
**Description:** An open-source e-commerce platform.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `Password` | string | No | Password |
| `Token` | string | No | Token |
| `Username` | string | No | Username |

**Options:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `Token URL` | string | No | Token URL |

---

#### Fuuz

**ID:** `43b90e5f-48a5-437c-b968-6f35a1663257`  
**Description:** Fuuz® by MFGx is an industrial operations platform that helps companies of all sizes build, run, and evolve the way they operate while maintaining speed, scale, and flexibility.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `ApiKey` | string | Yes | Fuuz API Key |

---

#### Salesforce

**ID:** `44ff6253-ca43-4429-955a-2907803178b8`  
**Description:** A customer relationship management solution that brings companies and customers together.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `Client ID` | string | Yes | Client ID |
| `Client Secret` | string | Yes | Client Secret |
| `Password` | string | Yes | Password |
| `Username` | string | Yes | Username |

---

#### Plex Classic

**ID:** `464e9c3f-dd6c-47af-8078-cda1ff25e966`  
**Description:** An ERP platform for manufaturing.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `Password` | string | Yes | Password |
| `Username` | string | Yes | Username |

---

#### Zoho

**ID:** `4ff8769a-bdea-11e8-a355-529269fb1459`  
**Description:** A web-based online office suite.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `Authorization Token` | string | Yes | Authorization Token |
| `Client ID` | string | Yes | Client ID |
| `Client Secret` | string | Yes | Client Secret |

**Options:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `API URL` | string | Yes | API URL |
| `Redirect URL` | string | Yes | Redirect URL |
| `Scope` | string | Yes | Scope |
| `Token URL` | string | Yes | Token URL |

---

#### ADP Vista

**ID:** `5b2ed01a-628e-413e-8baf-f6d4c01fb53b`  
**Description:** A connector to use the ADP Vista API.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `clientId` | string | Yes | Client Id |
| `clientSecret` | string | Yes | Client Secret |
| `companyCode` | string | Yes | Company Code |
| `loginId` | string | Yes | Login ID |
| `scope` | string | Yes | Scope |

**Options:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `country` | string | No |  |
| `legalEntity` | string | No |  |
| `paymentUnit` | string | No |  |

---

#### ADP

**ID:** `6a6da1f8-b69c-11e8-96f8-529269fb1459`  
**Description:** Human Resources management software and services.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `Client ID` | string | Yes | Client ID |
| `Client Secret` | string | Yes | Client Secret |

**Options:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `API URL` | string | Yes | API URL |
| `SSL Certificate` | string | Yes | SSL Certificate |
| `SSL Key` | string | Yes | SSL Key |
| `Token URL` | string | Yes | Token URL |

---

#### Plex UX

**ID:** `72974b11-897f-47bd-896d-133962ef7bbb`  
**Description:** An ERP platform for manufaturing.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `Password` | string | Yes | Password |
| `Username` | string | Yes | Username |

---

#### SMTP

**ID:** `749e15c7-b1d6-4438-9466-7dfe27271573`  
**Description:** Simple Mail Transfer Protocol.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `Password` | string | Yes | Password |
| `Username` | string | Yes | Username |

**Options:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `Ignore TLS` | boolean | No | Ignore TLS |
| `Port` | integer | No | Port |
| `Require TLS` | boolean | No | Require TLS |
| `Secure` | boolean | No | Secure |

---

#### Dynamics 365

**ID:** `808fce4c-6e38-4db8-b4b4-5c45a5a6f1cb`  
**Description:** Microsoft Dynamics 365

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `clientID` | string | Yes | Client ID |
| `clientSecret` | string | Yes | Client Secret |
| `scope` | string | Yes | Scope |

**Options:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `accessTokenUrl` | string | Yes | Access Token URL |

---

#### QuickBooks

**ID:** `88c548f7-4944-4eed-a948-9ce683e02a6f`  
**Description:** The QuickBooks Connector allows you to connect to the QuickBooks REST API.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `clientId` | string | Yes | The Client ID to use when authenticating. |
| `clientSecret` | string | Yes | The Client Secret to use when authenticating. |
| `refreshToken` | string | Yes | The Refresh Token to use when authenticating. This will periodically be replaced with a new refresh token. |

---

#### Fuuz UPS

**ID:** `906f591e-0cac-41f7-9fc3-bdbae5d6117a`  
**Description:** United Parcel Service with Fuuz credentials

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `AccessLicenseNumber` | string | No | Access License Number |

---

#### ODBC

**ID:** `914c3b05-7d13-4fd2-a0f8-fe925e67f066`  
**Description:** Open Database Connectivity (ODBC) is a standard API for accessing database management systems.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `netsuiteOAuth` | object | No | Netsuite OAuth Configuration |
| `password` | string | No | The password to use for authenticating. |
| `username` | string | No | The username of the user to authenticate as. |

**Options:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `connectionTimeout` | integer | No | How long before an idle connection will close, in seconds |
| `customProperties` | string | No | Allows you to set the custom properties of an ODBC connection string. |
| `driver` | string | Yes | The driver to utilize for this ODBC connection. |
| `loginTimeout` | integer | No | How long before the connection process will attempt to connect before timing out, in seconds. |
| `port` | integer | Yes | The port to connect to on the destination host. |

---

#### UPS

**ID:** `920e8e71-1de6-4160-8129-f16612c0d694`  
**Description:** United Parcel Service

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `ServiceAccessToken` | object | Yes |  |
| `TransactionSrc` | string | Yes | Transaction Source |
| `UsernameToken` | object | Yes |  |

**Options:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `Shipping_Default` | object | No |  |

---

#### MFGx

**ID:** `970ae55b-7ad0-497e-a77e-f9eb63aff73d`  
**Description:** A customizable manufacturing platform.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `ApiKey` | string | Yes | API Key |
| `Username` | string | Yes | Username |

---

#### Plex IAM API

**ID:** `9db652bf-9f99-472e-ada2-71f8e92f973f`  
**Description:** The Plex IAM API Connector allows you to connect to Plex's IAM HTTP API.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `clientId` | string | Yes | The Client ID to use when authenticating. |
| `clientSecret` | string | Yes | The Client Secret to use when authenticating. |

---

#### NetsuiteREST

**ID:** `ac61106b-72c6-4fca-8bc7-0ba3d54fb574`  
**Description:** Netsuite REST based API connector

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `account` | string | Yes | Account ID |
| `consumerKey` | string | Yes | Consumer Key |
| `consumerSecret` | string | Yes | Consumer Secret |
| `tokenId` | string | Yes | Token ID |
| `tokenSecret` | string | Yes | Token Secret |

---

#### Microsoft SQL Server

**ID:** `bb612514-2129-4fa9-849d-f9f8f6ec1c8e`  
**Description:** SQL Server is a relational database developed by Microsoft.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `password` | string | Yes | The password to use for authenticating. |
| `username` | string | Yes | The username of the user to authenticate as. |

**Options:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `connectionTimeout` | integer | No | How long before an idle connection will close, in seconds |
| `database` | string | No | The default database to connect to. |
| `encrypt` | boolean | No | Whether the connection should be encrypted. (required for Azure) |
| `port` | integer | Yes | The port to connect to on the destination host. |
| `requestTimeout` | integer | No | How long a request can run before it is timed out, in seconds. |

---

#### Square

**ID:** `c125d58f-41f5-41f5-b42e-3a63e3441526`  
**Description:** Provides credit card processing at the point-of-sale and other services for merchants.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `Personal Access Token` | string | Yes | Personal Access Token |

---

#### UPS OAuth

**ID:** `c13ebe14-08f3-4c6a-bb1d-c717d8430ab9`  
**Description:** A connector to use UPS OAuth api.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `accountNumber` | string | Yes | Account Number |
| `clientId` | string | Yes | Client Id |
| `clientSecret` | string | Yes | Client Secret |

---

#### USPS

**ID:** `c1ce6d2d-0e76-4683-9b06-00812f5514f4`  
**Description:** United States Postal Service

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `UserId` | string | Yes | User ID |

---

#### Arena

**ID:** `c6a47b3d-5740-4cec-a9d1-dfba96249d03`  
**Description:** Discrete event simulation and automation.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `Client ID` | string | Yes | Client ID |
| `Password` | string | Yes | Password |
| `Username` | string | Yes | Username |

**Options:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `API URL` | string | Yes | API URL |
| `Token URL` | string | Yes | Token URL |

---

#### FedEx

**ID:** `d16be6b2-ad5e-4b32-8e54-03f168b947f4`  
**Description:** Federal Express

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `clientDetail` | object | Yes | Client Detail |
| `parentCredentials` | object | Yes | Parent Credentials |
| `userCredentials` | object | Yes | User Credentials |

**Options:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `labelDefaults` | object | No | Label Defaults |
| `shipper` | object | No | Default Ship From |
| `transactionDetail` | object | No |  |

---

#### Confluence

**ID:** `d7ca2d1a-cc71-47fb-8b26-d316bfaedaf9`  
**Description:** Atlassian Confluence

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `apiKey` | string | Yes | API Key |
| `email` | string | Yes | Email |

---

#### TecCom File Upload

**ID:** `e2c23250-2da3-41af-8ea5-abe14a4aa77c`  
**Description:** Used to load data into the TecCom Business Cloud software.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `clientId` | string | Yes | Client Id |
| `clientSecret` | string | Yes | Client Secret |
| `tecComId` | string | Yes | TecCom Id |

---

#### OpenAI Chat

**ID:** `e86501cd-80b4-46ea-97c8-33d31cfef632`  
**Description:** A connector to interact with OpenAI Chat GPT models via the OpenAI API.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `apiKey` | string | Yes | Your OpenAI API key. |

---

#### Spiro

**ID:** `fa98a8d9-74f3-4ce1-8ef9-4455c31d3ce4`  
**Description:** A sales Relationship Management platform.

**Credentials:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `Authorization Token` | string | Yes | Authorization Token |

**Options:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `API URL` | string | Yes | API URL |

---

## Device Gateway

Device drivers, driver types, subscription types, and gateway functions for connecting to on-premise industrial equipment and data sources.

### Device Driver Types

**System Model:** `deviceDriverType`  
**Count:** 13 seeded values

Driver types categorize device drivers and define the functions available to all drivers of that type.

#### File

**ID:** `file`  
**Description:** Drivers for file access.

**Functions:**

- **`File: Delete`** (id: `fileDelete`, timeout: 60s): Deletes a file.
- **`File: List`** (id: `fileList`, timeout: 60s): List files in a directory.
- **`File: Move`** (id: `fileMove`, timeout: 60s): Move a file.
- **`File: Read`** (id: `fileRead`, timeout: 60s): Reads a file.
- **`File: Status`** (id: `fileStatus`, timeout: 15s): The status of the root folder.
- **`File: Write`** (id: `fileWrite`, timeout: 60s): Write to a file.

**Drivers:**

- `Local File` (id: `localFile`): A driver for files on the local machine.

---

#### HTTP

**ID:** `http`  
**Description:** Drivers for HTTP Driver functionality.

**Functions:**

- **`HTTP: Request`** (id: `HTTPRequest`, timeout: 15s): Make an http request.
- **`HTTP: Status`** (id: `HTTPStatus`, timeout: 15s): The status of the base url.

**Drivers:**

- `HTTP Client` (id: `HTTPClient`): HTTP Client for call client side api.

---

#### Modbus

**ID:** `modbus`  
**Description:** Drivers which support Modbus devices.

**Functions:**

- **`Modbus: Read Coil`** (id: `modbusReadCoil`, timeout: 15s): Reads a sequence of coils from a Modbus device.
- **`Modbus: Read Discrete Input`** (id: `modbusReadDiscreteInput`, timeout: 15s): Reads a sequence of discrete inputs from a Modbus device.
- **`Modbus: Read Holding Register`** (id: `modbusReadHoldingRegister`, timeout: 15s): Reads a sequence of holding registers from a Modbus device.
- **`Modbus: Read Input Register`** (id: `modbusReadInputRegister`, timeout: 15s): Reads a sequence of input registers from a Modbus device.
- **`Modbus: Status`** (id: `modbusStatus`, timeout: 15s): Queries the status of the Modbus device.
- **`Modbus: Write Coils`** (id: `modbusWriteCoils`, timeout: 15s): Writes values to a block of coils on a Modbus device.
- **`Modbus: Write Holding Registers`** (id: `modbusWriteHoldingRegisters`, timeout: 15s): Writes a values to a block of holding registers on a Modbus device.

**Drivers:**

- `Modbus TCP` (id: `modbusTCP`): A driver for Modbus devices over TCP

---

#### MQTT

**ID:** `mqtt`  
**Description:** MQTT Drivers.

**Functions:**

- **`MQTT: Publish`** (id: `mqttPublish`, timeout: 10s): Publish messages to a topic.
- **`MQTT: Status`** (id: `mqttStatus`, timeout: 10s): Checks the connection to the MQTT broker.

**Drivers:**

- `MQTT Client` (id: `mqttClient`): A driver for MQTT

---

#### MQTT Sparkplug

**ID:** `mqttSparkplug`  
**Description:** Driver type for setting up a sparkplug B edge node.

**Functions:**

- **`Sparkplug B: Device Birth`** (id: `mqttSparkplugDeviceBirth`, timeout: 10s): Publish birth message for a device or a list of devices.
- **`Sparkplug B: Device Death`** (id: `mqttSparkplugDeviceDeath`, timeout: 10s): Publish a death certificate for a device.
- **`Sparkplug B: Publish Device Data`** (id: `mqttSparkplugPublishDeviceData`, timeout: 10s): Publish messages from a device.
- **`Sparkplug B: Publish Node Data`** (id: `mqttSparkplugPublishNodeData`, timeout: 10s): Publish messages from the edge of network node.
- **`Sparkplug B: Status`** (id: `mqttSparkplugStatus`, timeout: 10s): Check the status of the connection to mqtt broker.

**Drivers:**

- `MQTT Sparkplug B` (id: `mqttSparkplugB`): A driver for publishing data over sparkplug B.

---

#### OPCUA

**ID:** `opcua`  
**Description:** Drivers for opcua functionality.

**Functions:**

- **`OPCUA: Browse`** (id: `opcuaBrowse`, timeout: 15s): Browse to see what nodes are children of a given node. If a node is not provided children of the root node will be given.
- **`OPCUA: Read`** (id: `opcuaRead`, timeout: 15s): Read nodes from an opcua server.
- **`OPCUA: Status`** (id: `opcuaStatus`, timeout: 15s): Queries the status of the connection to the server.
- **`OPCUA: Write`** (id: `opcuaWrite`, timeout: 15s): Write to nodes on an opcua server.

**Drivers:**

- `OPCUA Client` (id: `opcuaClient`): A OPCUA client driver.

---

#### PCCC PLC

**ID:** `pccc`  
**Description:** Drivers which support PCCC PLC operations.

**Functions:**

- **`PCCC PLC: Read Tag`** (id: `pcccReadTag`, timeout: 15s): Read tag value from a PCCC PLC.
- **`PCCC PLC: Read Tags`** (id: `pcccReadTags`, timeout: 15s): Read tag values from a PLC.
- **`PCCC: Status`** (id: `pcccStatus`, timeout: 15s): Get the status of the PLC.
- **`PCCC PLC: Write Tag`** (id: `pcccWriteTag`, timeout: 15s): Write a tag value to a PCCC PLC.
- **`PCCC PLC: Write Tags`** (id: `pcccWriteTags`, timeout: 15s): Write tag values to a PCCC PLC.

**Drivers:**

- `PCCC PLC` (id: `plcPCCC`): PCCC PLC Driver

---

#### PLC

**ID:** `plc`  
**Description:** Drivers which support common PLC operations.

**Functions:**

- **`PLC: Browse Tags`** (id: `plcBrowseTags`, timeout: 15s): Browse Tags on PLC device.
- **`PLC: Read Tag`** (id: `plcReadTag`, timeout: 15s): Reads a tag value from a PLC.
- **`PLC: Read Tags`** (id: `plcReadTags`, timeout: 15s): Read tag values from a PLC.
- **`PLC: Status`** (id: `plcStatus`, timeout: 15s): Get the status of the PLC.
- **`PLC: Write Tag`** (id: `plcWriteTag`, timeout: 15s): Writes a value to a PLC tag.
- **`PLC: Write Tags`** (id: `plcWriteTags`, timeout: 15s): Writes values to PLC tags.

**Drivers:**

- `Ethernet/IP PLC` (id: `ethernetIpPlc`): A driver for PLCs using Allen-Bradly's Ethernet/IP protocol.

---

#### Printer

**ID:** `printer`  
**Description:** Drivers which support common printing operations.

**Functions:**

- **`Printer: Get Jobs`** (id: `printerGetJobs`, timeout: 15s): Queries the job queue of the printer.
- **`Printer: Print`** (id: `printerPrint`, timeout: 60s): Sends a document (i.e. a PDF) to the printer.
- **`Print File`** (id: `printerPrintFile`, timeout: 60s): Sends an MFGx file to the printer.
- **`Printer: Print Raw`** (id: `printerPrintRaw`, timeout: 60s): Sends a raw printer command (i.e. a ZPL string) to the printer.
- **`Printer: Status`** (id: `printerStatus`, timeout: 15s): Queries the status of the printer.

**Drivers:**

- `Native Printer` (id: `nativePrinter`): A printer driver which uses the system print spooler to send documents and commands to the printer.
- `TCP Printer` (id: `tcpPrinter`): A printer driver which uses a direct TCP network connection to send documents and commands to the printer.

---

#### Remote System Call

**ID:** `remoteSystemCall`  
**Description:** Drivers for making remote system calls.

**Functions:**

- **`Remote System Call: Call`** (id: `remoteSystemCallCall`, timeout: 10s): Make a call to the remote system.
- **`Remote System Call: Status`** (id: `remoteSystemCallStatus`, timeout: 10s): Check the status of the remote server.

**Drivers:**

- `SAP RFC` (id: `sapRfc`): A driver for making remote function calls to SAP.

---

#### Server

**ID:** `server`  
**Description:** Server Drivers.

**Functions:**

- **`Server: Status`** (id: `serverStatus`, timeout: 10s): Checks the status of the Server.

**Drivers:**

- `HTTP Server` (id: `HTTPServer`): HTTP Server driver can relay requests to a data flow or subscribe to HTTP POST endpoints.
- `MQTT Broker` (id: `mqttBroker`): A driver writing data to a MQTT Broker.
- `TCP Server` (id: `TCPServer`): TCP Server driver can subscribe to a TCP port.

---

#### SQL

**ID:** `sql`  
**Description:** Drivers for SQL databases.

**Functions:**

- **`SQL: Query`** (id: `sqlQuery`, timeout: 10s): Executes SQL against a database.
- **`SQL: Status`** (id: `sqlStatus`, timeout: 10s): Checks the connection to the SQL server.

**Drivers:**

- `IBM DB2` (id: `ibmdb2`): A driver for IBM DB2 server.
- `Microsoft SQL` (id: `microsoftSql`): A driver for Microsoft SQL server.
- `MySQL` (id: `mySql`): A driver for MySQL server.
- `Oracle DB` (id: `oracledb`): A driver for Oracle server. Requires Oracle Instant Client Basic or Basic Light: https://www.oracle.com/database/technologies/instant-client.html

---

#### TCP

**ID:** `tcp`  
**Description:** Drivers for writing to tcp sockets.

**Functions:**

- **`TCP: Status`** (id: `tcpStatus`, timeout: 10s): Check the status of a socket.
- **`TCP: Write`** (id: `tcpWrite`, timeout: 10s): Write data to a tcp socket.

**Drivers:**

- `TCP Socket` (id: `tcpSocket`): A driver writing data to a tcp socket.

---

### Device Drivers

**System Model:** `deviceDriver`  
**Count:** 19 seeded values

Device drivers connect the Fuuz Device Gateway to on-premise equipment, databases, and communication protocols. Each driver belongs to a driver type and defines a configuration schema.

#### Ethernet/IP PLC

**ID:** `ethernetIpPlc`  
**Driver Type:** PLC  
**Description:** A driver for PLCs using Allen-Bradly's Ethernet/IP protocol.

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `ip` | string | Yes | The IP address of the PLC. |
| `scanRateSeconds` | number | Yes | The Scan Rate in seconds for subscriptions on this device. |
| `slot` | integer | Yes | The slot the PLC processor is on. |

---

#### HTTP Client

**ID:** `HTTPClient`  
**Driver Type:** HTTP  
**Description:** HTTP Client for call client side api.

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `baseUrl` | string | Yes | Base Url |
| `headers` | object | Yes | Headers |

---

#### HTTP Server

**ID:** `HTTPServer`  
**Driver Type:** Server  
**Description:** HTTP Server driver can relay requests to a data flow or subscribe to HTTP POST endpoints.

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `endpoints` | array | No | The data flow relay endpoints for this server |
| `port` | number | Yes | The listen port for this HTTP server. |
| `requestSizeLimit` | string | No | The size limit for incoming requests (1mb, 300k). Defaults to 100k. |

---

#### IBM DB2

**ID:** `ibmdb2`  
**Driver Type:** SQL  
**Description:** A driver for IBM DB2 server.

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `database` | string | Yes | The database to connect to. |
| `password` | string | Yes | The password for the user that connects to the user. |
| `poolSize` | number | No | Maximum number of connections |
| `port` | number | No | The port the DB2 server is running on. |
| `server` | string | Yes | The host name of the DB2 server. |
| `user` | string | Yes | The user to connect to the server as. |

---

#### Local File

**ID:** `localFile`  
**Driver Type:** File  
**Description:** A driver for files on the local machine.

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `root` | string | No | The path to the directory that will be considered the root for this device. |

---

#### Microsoft SQL

**ID:** `microsoftSql`  
**Driver Type:** SQL  
**Description:** A driver for Microsoft SQL server.

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `database` | string | Yes | The database to connect to. |
| `encrypt` | boolean | No | Whether the connection to the server is encrypted. Enabled by default. |
| `password` | string | Yes | The password for the user that connects to the user. |
| `port` | number | No | The port the SQL server is running on. |
| `server` | string | Yes | The host name of the SQL server. |
| `trustServerCertificate` | boolean | No | If invalid or self signed certs should be trusted. |
| `user` | string | Yes | The user to connect to the server as. |

---

#### Modbus TCP

**ID:** `modbusTCP`  
**Driver Type:** Modbus  
**Description:** A driver for Modbus devices over TCP

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `host` | string | Yes | The hostname or IP address of the Modbus device is located at. |
| `port` | integer | Yes | The port the Modbus device is listening on. |
| `scanRateSeconds` | number | Yes | The Scan Rate in seconds for subscriptions on this device. |

---

#### MQTT Broker

**ID:** `mqttBroker`  
**Driver Type:** Server  
**Description:** A driver writing data to a MQTT Broker.

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `port` | number | Yes | The port to connect to. |

---

#### MQTT Client

**ID:** `mqttClient`  
**Driver Type:** MQTT  
**Description:** A driver for MQTT

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `brokerUrl` | string | Yes | the url for the mqtt broker. |
| `lastWillAndTestament` | object | No | The last will and testament is a message that will be sent out to subscribers if this client disconnects ungracefully. |
| `options` | string | No |  |
| `password` | string | No | The password to use to authenticate with the broker. |
| `username` | string | No | The username to use to authenticate with the broker. |

---

#### MQTT Sparkplug B

**ID:** `mqttSparkplugB`  
**Driver Type:** MQTT Sparkplug  
**Description:** A driver for publishing data over sparkplug B.

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `birthCertificate` | array | No | The birth certificate for this edge of network node. This should contain all of the metrics/data points that this gateway will publish |
| `birthFlow` | string | No | Use this flow to register any devices that do not have their birth certificates configured in the "devices" field when a birth message is requested. The node birth certificate and any device birth ... |
| `brokerUrl` | string | Yes | the url for the mqtt broker. |
| `dcmdFlow` | string | No | The flow to used to proccess Device Command events |
| `devices` | array | No | All of the devices and their birth certificates. This is for default devices that you know will be connected to the edge of network node. Other devices and still be birthed using the deviceBirth fu... |
| `edgeNodeName` | string | Yes | The name of this edge of network gateway. |
| `groupId` | string | Yes | The group Id is to create logical groupings of devices. |
| `messageFlow` | string | No | The flow to handle messages recived that are not supported by this verion of sparkplug. |
| `ncmdFlow` | string | No | The flow to used to proccess Node Command events |
| `options` | string | No |  |
| `password` | string | Yes | The password to use to authenticate with the broker. |
| `username` | string | Yes | The username to use to authenticate with the broker. |

---

#### MySQL

**ID:** `mySql`  
**Driver Type:** SQL  
**Description:** A driver for MySQL server.

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `database` | string | Yes | The database to connect to. |
| `options` | object | No | The options for set extra connection options. |
| `password` | string | Yes | The password for the user that connects to the user. |
| `port` | number | No | The port the MySQl server is running on. |
| `server` | string | Yes | The host name of the MySQL server. |
| `user` | string | Yes | The user to connect to the server as. |

---

#### Native Printer

**ID:** `nativePrinter`  
**Driver Type:** Printer  
**Description:** A printer driver which uses the system print spooler to send documents and commands to the printer.

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `printerName` | string | Yes | The name of the installed printer on the machine, including the printer server prefix if one is in use. |

---

#### OPCUA Client

**ID:** `opcuaClient`  
**Driver Type:** OPCUA  
**Description:** A OPCUA client driver.

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `applicationName` | string | Yes | The name Fuuz will use to identify its self to the server. |
| `certificateRootFolder` | string | No | The folder to manage certificates from. |
| `endpoint` | string | Yes | The server endpoint to connect to. |
| `securityMode` | string | Yes | The mode to use to secure the connection to the server.. |
| `securityPolicy` | string | Yes | The policy to use to secure the connection to the server. |
| `user` | object | No |  |
| `userTokenType` | string | Yes | The authentication method to use for the user session. |

---

#### Oracle DB

**ID:** `oracledb`  
**Driver Type:** SQL  
**Description:** A driver for Oracle server. Requires Oracle Instant Client Basic or Basic Light: https://www.oracle.com/database/technologies/instant-client.html

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `database` | string | Yes | The database to connect to. |
| `password` | string | Yes | The password for the user that connects to the database. |
| `port` | number | No | The port the Oracle server is running on. |
| `server` | string | Yes | The host name of the Oracle server. |
| `user` | string | Yes | The user to connect to the server as. |

---

#### PCCC PLC

**ID:** `plcPCCC`  
**Driver Type:** PCCC PLC  
**Description:** PCCC PLC Driver

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `host` | string | Yes | The IP address of the PLC. |
| `port` | integer | Yes | The PLC port |
| `scanRateSeconds` | number | Yes | The Scan Rate in seconds for subscriptions on this device. |

---

#### SAP RFC

**ID:** `sapRfc`  
**Driver Type:** Remote System Call  
**Description:** A driver for making remote function calls to SAP.

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `ahost` | string | Yes | The host name or ip of the sap server. (ahost) |
| `client` | string | Yes | The client version. Usually 620 |
| `lang` | string | Yes | The language. Usually  EN  |
| `options` | string | No |  |
| `passwd` | string | Yes | The password to use to authenticate with SAP. |
| `sysnr` | string | Yes | The System Number. (sysnr) |
| `user` | string | Yes | The username to use to connect to SAP. |

---

#### TCP Printer

**ID:** `tcpPrinter`  
**Driver Type:** Printer  
**Description:** A printer driver which uses a direct TCP network connection to send documents and commands to the printer.

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `host` | string | Yes | The hostname or IP address of the printer. |
| `port` | integer | Yes | The TCP port to connect to on the host. |
| `statusCommand` | string | No | The printer command string to use to query the printer status.  When not provided, a simple connection test is performed instead. |
| `statusResponseParser` | string | No | A JSONata transformation which receives the status response from the printer status command as the payload, and is expected to return an object with ok, message, and info properties.  When not prov... |

---

#### TCP Server

**ID:** `TCPServer`  
**Driver Type:** Server  
**Description:** TCP Server driver can subscribe to a TCP port.

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `dataFlowId` | string | No |  |
| `debounce` | object | No |  |
| `executeFlowOnConnect` | boolean | No | If enabled, new TCP connections will execute the configured data flow upon connection. Disabled by default. |
| `port` | number | Yes | The listen port for this TCP server. |

---

#### TCP Socket

**ID:** `tcpSocket`  
**Driver Type:** TCP  
**Description:** A driver writing data to a tcp socket.

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `dataFlowId` | string | No |  |
| `debounce` | object | No |  |
| `host` | string | Yes | The hostname or ip address to connect to. |
| `port` | number | Yes | The port to connect to. |
| `writeOnConnect` | boolean | Yes | Write on Connect |

---

### Device Subscription Types

**System Model:** `deviceSubscriptionType`  
**Count:** 10 seeded values

Subscription types define how a device reports data back to Fuuz. Each subscription type has a configuration schema defining its parameters.

#### File System Change

**ID:** `fileSystemChange`

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `depth` | number | No | The depth of sub folders to search if path is a folder. If left blank all sub folders will be watched. |
| `event` | string | Yes | The type of change to listen for. |
| `path` | string | Yes | The path to the file or folder to watch for changes |
| `polling` | boolean | No | Whether to use polling to watch a file or folder. Can be more cpu intensive, but might be needed to watch files over a network. |

---

#### HTTP POST Endpoint

**ID:** `httpEndpoint`

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `continuousValues` | boolean | No | Applied to Store and Forward. If enabled, all value updates are pushed to Fuuz, otherwise the most recent value for each endpoint is pushed to Fuuz. |
| `path` | string | Yes | The path mapping for this subscription. |
| `storeAndForward` | boolean | Yes | If enabled, updates from subscribed endpoints will be locally stored and forwarded to Fuuz. When disconnected from Fuuz, values are stored until re-connected. |

---

#### Modbus Values

**ID:** `modbusValues`

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `address` | number | Yes | The data address |
| `continuousValues` | boolean | No | Applied to Store and Forward. If enabled, all node value updates are pushed to Fuuz, otherwise the most recent node value for each node is pushed to Fuuz. |
| `length` | number | Yes | The data length |
| `registerType` | string | Yes | The type of register to read from. |
| `storeAndForward` | boolean | No | If enabled tag values will be stored on disk until they can be pushed to mfgx.io suscessfully. Otherwise tag values will attempt to be sent off to mfgx.io once read. |
| `swapEndianness` | boolean | No | Swaps the byte order (Endianness) of the response. |

---

#### MQTT Receive Data

**ID:** `mqttBrokerReceiveData`

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `continuousValues` | boolean | No | Applied to Store and Forward. If enabled, all value updates are pushed to Fuuz, otherwise the most recent value for each endpoint is pushed to Fuuz. |
| `storeAndForward` | boolean | Yes | If enabled, updates from subscribed endpoints will be locally stored and forwarded to Fuuz. When disconnected from Fuuz, values are stored until re-connected. |
| `topics` | array | No | Array of topics to subscribe to. |

---

#### MQTT Topics

**ID:** `mqttTopics`

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `continuousValues` | boolean | Yes | Applied to Store and Forward. If enabled, all node value updates are pushed to Fuuz, otherwise the most recent node value for each node is pushed to Fuuz. |
| `storeAndForward` | boolean | Yes | If enabled messages will be stored on disk until they can be pushed to fuuz suscessfully. Otherwise node values will attempt to be sent off to fuuz once read. |
| `topics` | array | Yes | Array of topics to subscribe to. |

---

#### Node Values

**ID:** `nodeValues`

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `continuousValues` | boolean | No | Applied to Store and Forward. If enabled, all tag value updates are pushed to Fuuz, otherwise the latest tag value is pushed to Fuuz. |
| `nodes` | array | Yes | Array of node ids to subscribe to. |
| `storeAndForward` | boolean | Yes | If enabled node values will be stored on disk until they can be pushed to fuuz suscessfully. Otherwise node values will attempt to be sent off to fuuz once read. |

---

#### PCCC Tag Values

**ID:** `pcccTagValues`

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `continuousValues` | boolean | No | Applied to Store and Forward. If enabled, all node value updates are pushed to Fuuz, otherwise the most recent node value for each node is pushed to Fuuz. |
| `storeAndForward` | boolean | Yes | If enabled tag values will be stored on disk until they can be pushed to mfgx.io suscessfully. Otherwise tag values will attempt to be sent off to mfgx.io once read. |
| `tag` | string | Yes | The name of the tag to read in the PLC. |

---

#### Tag Values

**ID:** `tagValues`

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `continuousValues` | boolean | No | Applied to Store and Forward. If enabled, all node value updates are pushed to Fuuz, otherwise the most recent node value for each node is pushed to Fuuz. |
| `scope` | string | No | The optional program scope of the tag. If the program scope is not provided it will read from the Global program scope. |
| `storeAndForward` | boolean | Yes | If enabled tag values will be stored on disk until they can be pushed to mfgx.io suscessfully. Otherwise tag values will attempt to be sent off to mfgx.io once read. |
| `tag` | string | Yes | The name of the tag to read in the PLC. |
| `type` | string | Yes | The data type of the tag. |

---

#### TCP Receive Data

**ID:** `tcpSocketReceiveData`

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `continuousValues` | boolean | No | Applied to Store and Forward. If enabled, all value updates are pushed to Fuuz, otherwise the most recent value for each endpoint is pushed to Fuuz. |
| `storeAndForward` | boolean | Yes | If enabled, updates from subscribed endpoints will be locally stored and forwarded to Fuuz. When disconnected from Fuuz, values are stored until re-connected. |

---

#### TCP Socket

**ID:** `tcpSocketSubscription`

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `continuousValues` | boolean | No | Applied to Store and Forward. If enabled, all value updates are pushed to Fuuz, otherwise the most recent value for is pushed to Fuuz. |
| `storeAndForward` | boolean | Yes | If enabled, updates from socket will be locally stored and forwarded to Fuuz. When disconnected from Fuuz, values are stored until re-connected. |

---

### Device Gateway Functions

**System Model:** `deviceGatewayFunction`  
**Count:** 6 seeded values

Gateway functions are operations that can be invoked on a Fuuz Device Gateway.

#### Apply Device Gateway Updates

**ID:** `applyGatewayUpdates`  
**Timeout:** 30s  
**Description:** Apply available device gateway updates.

**Request Parameters:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `updateDrivers` | boolean | Yes | Install device gateway driver updates |
| `updateGateway` | boolean | Yes | Install device gateway update |

**Response Fields:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `properties` | string | No |  |

---

#### Get Driver Devices

**ID:** `getDriverDevices`  
**Timeout:** 60s  
**Description:** Gets the list of devices connected to the gateway, if the driver supports it.

**Request Parameters:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `deviceDriverId` | string | Yes | The driver to list connected devices for. |

**Response Fields:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `devices` | array | Yes | The list of driver devices connected to the gateway. |

---

#### Get Gateway Status

**ID:** `getGatewayStatus`  
**Timeout:** 10s  
**Description:** Get the status of the Device Gateway

**Response Fields:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `applicationVersion` | string | No | Application Version |
| `hostname` | string | No | Hostname |
| `ipAddress` | string | No | IP Address |
| `macAddress` | string | No | MAC Address |
| `statusInfo` | object | No | Status Info |
| `statusMessage` | string | No | Status Message |
| `statusOk` | boolean | No | Status Ok |

---

#### List Log Files

**ID:** `listLogFiles`  
**Timeout:** 30s  
**Description:** Lists the log files available on the gateway.

**Request Parameters:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `deviceId` | string | No | The ID of the device to list logs for. When not specified, the function will list the main application log files. |

**Response Fields:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `logFiles` | array | Yes | The list of log files. |

---

#### Ping Hosts

**ID:** `pingHosts`  
**Timeout:** 30s  
**Description:** Ping hosts reachable from the device gateway.

**Request Parameters:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `hosts` | array | Yes |  |

---

#### Read Log File

**ID:** `readLogFile`  
**Timeout:** 60s  
**Description:** Reads the latest logs from a log file on the gateway.

**Request Parameters:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `deviceId` | string | No | The ID of the device to list logs for. When not specified, the function will read the main application log files. |
| `last` | integer | No | The number of lines to read from the end of the file. When not specified, the last 50 lines will be read. |
| `logFile` | string | No | The name of the log file. When not specified, the function will read the current log file. |

**Response Fields:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `logs` | string | Yes | The logs read from the file. |

---

## Scheduling

Schedule and constraint types used for scheduling data flows and operations.

### Schedule Types

**System Model:** `scheduleType`  
**Count:** 1 seeded values

#### Cron

**ID:** `cron`

**Configuration:**

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `cronString` | string | Yes | The cron schedule on which to execute this node. |
| `timezone` | string | No | The timezone to run the schedule in. |

---

### Scheduling Constraint Types

**System Model:** `schedulingConstraintType`  
**Count:** 6 seeded values

| ID | Name | Description |
|-----|------|-------------|
| `finishnoearlierthan` | Finish no earlier than | "Finish no earlier than" constraint. Restricting an event to finish on or after a specified date. |
| `finishnolaterthan` | Finish no later than | "Finish no later than" constraint. Restricting an event to finish on or before a specified date. |
| `mustfinishon` | Must finish on | "Must finish on" constraint. Restricts an event to finish on a specified date. |
| `muststarton` | Must start on | "Must start on" constraint. Restricts an event to start on a specified date. |
| `startnoearlierthan` | Start no earlier than | "Start no earlier than" constraint. Restricting an event to start on or after a specified date. |
| `startnolaterthan` | Start no later than | "Start no later than" constraint. Restricting an event to start on or before a specified date. |

---

## Saved Queries

Types for saved GraphQL operations.

### Saved Query Operation Types

**System Model:** `savedQueryOperationType`  
**Count:** 3 seeded values

| ID | Label | Description |
|-----|------|-------------|
| `Mutation` | Mutation | An operation which changes data in the system. |
| `Query` | Query | An operation which retrieves data from the system. |
| `Subscription` | Subscription | An operation which listens for changes in the system. |

---

## Package Installation

Types related to Fuuz app package installation and deployment.

### Package Installation Task Types

**System Model:** `packageInstallationTaskType`  
**Count:** 4 seeded values

| ID | Label | Description |
|-----|------|-------------|
| `create` | Create | This task represents a set of insertions into the database. |
| `delete` | Delete | This task represents a set of deletions from the database. |
| `transform` | Transform | This task represents the execution of a custom jsonata transform. |
| `update` | Update | This task represents a set of updates applied to existing data. |

---

### Package Installation Log Types

**System Model:** `packageInstallationLogType`  
**Count:** 6 seeded values

| ID | Label | Description |
|-----|------|-------------|
| `debug` | Debug | This log contains diagnostic information about a specific behavior. |
| `error` | Error | This log contains information about an important procedural failure. |
| `fatal` | Fatal | This log contains information about a shutdown due to severe problems. |
| `info` | Info | This log contains information about normal, significant behavior. |
| `trace` | Trace | This log contains information about the exact details of a program state. |
| `warn` | Warn | This log contains information about unusual behavior outside of errors. |

---

## Settings

Platform settings, setting types, and input types that control Fuuz behavior and appearance.

### Setting Types

**System Model:** `settingType`  
**Count:** 2 seeded values

| ID | Label | Description |
|-----|------|-------------|
| `Client` | Client | Settings that are applicable to specific clients. |
| `User` | User | Settings that control user-level preferences. |

---

### Setting Input Types

**System Model:** `settingInputType`  
**Count:** 9 seeded values

| ID | Label | Description |
|-----|------|-------------|
| `Boolean` | Boolean | A boolean type. |
| `Color` | Color | A color type. |
| `Date` | Date | A date type. |
| `DateTime` | DateTime | A date/time type. |
| `Float` | Float | A float type. |
| `Integer` | Integer | An integer type. |
| `List` | List | A list type. |
| `String` | String | A string type. |
| `Time` | Time | A time type. |

---

### System Settings

**System Model:** `setting`  
**Count:** 12 seeded values

These are the system-level settings available in Fuuz, controlling display formats, theming, locale, and more.

#### Always Display Time Zones

**ID:** `AlwaysDisplayTimeZones`  
**Input Type:** Boolean  
**Default Value:** `False`  
**Description:** Whether to always display time zone identifiers on dates. If false, time zone identifiers are only displayed when the date's time zone differs from the system time zone. The system time zone is determined by the Time Zone setting, or by the user's device time zone when the Time Zone setting is not set.

---

#### Date Format

**ID:** `DateFormat`  
**Input Type:** String  
**Default Value:** `L`  
**Description:** A default format string for date display fields, columns, and inputs. Format strings use [Moment.js format syntax](https://momentjs.com/docs/#/displaying/format/). The default format provides a short date string that automatically changes depending on the Locale setting or user's device locale.

---

#### Date & Time Format

**ID:** `DateTimeFormat`  
**Input Type:** String  
**Default Value:** `L LT`  
**Description:** A default format string for dateTime display fields, columns, and inputs. Format strings use [Moment.js format syntax](https://momentjs.com/docs/#/displaying/format/). The default format provides a short date & time string that automatically changes depending on the Locale setting or user's device locale.

---

#### Float Format

**ID:** `FloatFormat`  
**Input Type:** String  
**Default Value:** `0,0.[00000000]`  
**Description:** A default format string for number display fields, columns, and inputs. Format strings use [Numeral.js format syntax](http://numeraljs.com/#format). The default format provides a number string with thousands and decimal separators that automatically change depending on the Locale setting or user's device locale, and a maxmimum of 8 digits of decimal precision.

---

#### Integer Format

**ID:** `IntegerFormat`  
**Input Type:** String  
**Default Value:** `0,0`  
**Description:** A default format string for integer display fields, columns, and inputs. Format strings use [Numeral.js format syntax](http://numeraljs.com/#format). The default format provides an integer string with thousands separators that automatically change depending on the Locale setting or user's device locale.

---

#### Locale

**ID:** `Locale`  
**Input Type:** String  
**Scopes:** Client, User  
**Description:** A locale to apply to MFGx, overriding the user's browser locale.  Affects date formatting, number formatting, and application language.

---

#### Logo Image Source URL

**ID:** `LogoImageSourceURL`  
**Input Type:** String  
**Default Value:** ``  
**Description:** The URL to a logo that is branded upon a MFGx webpage.

---

#### Primary Color

**ID:** `PrimaryColor`  
**Input Type:** Color  
**Description:** The Primary Color of the theme.

---

#### Secondary Color

**ID:** `SecondaryColor`  
**Input Type:** Color  
**Description:** The Secondary Color of the theme.

---

#### Theme Mode

**ID:** `ThemeMode`  
**Input Type:** List  
**Default Value:** `System`  
**Scopes:** Client, User  
**Description:** What theme mode to use for the user interface.

**Allowed Values:** `Dark`, `Light`, `System`

---

#### Time Format

**ID:** `TimeFormat`  
**Input Type:** String  
**Default Value:** `LT`  
**Description:** A default format string for time display fields, columns, and inputs. Format strings use [Moment.js format syntax](https://momentjs.com/docs/#/displaying/format/). The default format provides a short time string that automatically changes depending on the Locale setting or user's device locale.

---

#### Time Zone

**ID:** `TimeZone`  
**Input Type:** List  
**Description:** A default [IANA time zone identifier](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) to apply to dates and times in Fuuz. When not set, dates and times are displayed in a user's device time zone.

**Allowed Values:** 596 timezone values (IANA timezone database)

---

## Units of Measure

Unit type classifications for measurements used across the platform.

### Unit Types

**System Model:** `unitType`  
**Count:** 7 seeded values

| ID | Label | Description |
|-----|------|-------------|
| `Amount` | Amount | Unit Type based on the amount of objects. |
| `Area` | Area | Unit type based on the cross-product of two lengths. |
| `Currency` | Currency | Unit Type based on money. |
| `Length` | Length | Unit Type based on the the measurement of an object end to end. |
| `Time` | Time | Unit type based on a length of time. |
| `Volume` | Volume | Unit Type based on the space used object, normally in a container. |
| `Weight` | Weight | Unit Type based on the mass of an object. |

---

*Generated from Fuuz Platform GraphQL schema exports. For the latest system values, refer to the Fuuz platform at [fuuz.com](https://fuuz.com).*