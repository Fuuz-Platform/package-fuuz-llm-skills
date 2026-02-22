# Every system your business runs can connect to Fuuz

**Fuuz Industrial Operations Platform connects to virtually any enterprise, industrial, or cloud system through standard, non-proprietary integration protocols.** Because Fuuz supports 11 open integration methods — REST API, OPC UA, MQTT, SOAP, FTP/SFTP, ODBC/SQL, Webhooks, WebSockets, EDI, EtherNet/IP and Modbus TCP, and Sparkplug B — the platform acts as a universal bridge between your existing technology investments and a unified operations layer. This reference catalogs **500+ software applications, platforms, databases, and industrial systems** across every major industry category that support one or more of these same open protocols, confirming that whatever you already use, Fuuz can reach it.

The integration landscape divides cleanly: business systems (ERP, CRM, HR, eCommerce) communicate primarily through REST APIs, webhooks, SOAP, and EDI. Industrial systems (SCADA, MES, PLCs, robots) speak OPC UA, MQTT, EtherNet/IP, Modbus TCP, and Sparkplug B. Data platforms bridge both worlds through SQL/ODBC and REST. Fuuz's gateway drivers and cloud connectors cover all of these, eliminating the need for custom point-to-point integrations.

---

## ERP systems: the backbone of enterprise integration

Every major ERP system on the market exposes open integration interfaces. **REST API support is now universal** across ERP platforms, while SOAP/WSDL remains widely available (though declining), and EDI is critical for supply chain transactions.

### Tier 1 enterprise ERP

| System | Vendor | REST API | SOAP | FTP/SFTP | ODBC/SQL | Webhooks | EDI |
|--------|--------|----------|------|----------|----------|----------|-----|
| SAP S/4HANA | SAP | ✅ OData | ✅ | ✅ | ✅ HANA | ✅ Event Mesh | ✅ IDoc |
| SAP ECC | SAP | ✅ | ✅ RFC/BAPI | ✅ | ✅ | — | ✅ IDoc |
| SAP Business One | SAP | ✅ Service Layer | ✅ DI Server | ✅ | ✅ | — | Via partner |
| Oracle ERP Cloud | Oracle | ✅ | ✅ | ✅ SFTP/FBDI | Via BICC | ✅ Business Events | ✅ B2B |
| Oracle E-Business Suite | Oracle | ✅ ORDS | ✅ SOA Gateway | ✅ | ✅ Oracle DB | — | ✅ |
| Oracle JD Edwards | Oracle | ✅ AIS Server | ✅ | ✅ | ✅ | — | ✅ X12/EDIFACT |
| NetSuite | Oracle | ✅ SuiteTalk/RESTlets | ✅ | ✅ | ✅ SuiteAnalytics ODBC | Via SuiteScript | Via partner |
| Dynamics 365 F&O | Microsoft | ✅ OData V4 | ✅ | ✅ DMF | ✅ Azure SQL | ✅ Business Events | Via partner |
| Dynamics 365 Business Central | Microsoft | ✅ OData V4 | ✅ | Via extensions | ✅ SQL Server | ✅ Native | Via partner |

SAP uniquely bridges ERP to the shop floor through **SAP Plant Connectivity (PCo)**, which natively supports OPC UA, MQTT, and Modbus — making SAP one of the few ERP platforms with direct industrial protocol connectivity. All other ERPs reach industrial systems through middleware gateways like Kepware, Ignition, or OPC Router, which Fuuz's gateway drivers also support.

### Mid-market and manufacturing ERP

| System | Vendor | REST API | SOAP | ODBC/SQL | EDI | Other |
|--------|--------|----------|------|----------|-----|-------|
| Infor CloudSuite (SyteLine, LN, M3) | Infor | ✅ ION API | ✅ | ✅ | ✅ ION B2B | ION middleware |
| Epicor Kinetic | Epicor | ✅ OData/Swagger | ✅ legacy | ✅ SQL Server | ✅ X12/EDIFACT | Webhooks ✅ |
| IFS Cloud | IFS | ✅ 15,000+ OData APIs | ✅ BizAPIs | ✅ Oracle DB | ✅ | Webhooks ✅ |
| Acumatica | Acumatica | ✅ OpenAPI | ✅ | ✅ | Via partner | Webhooks ✅ |
| Sage X3 | Sage | ✅ | ✅ | ✅ SQL Server | ✅ | FTP ✅ |
| Sage Intacct | Sage | ✅ | ✅ XML/SOAP | Cloud-only | Via partner | — |
| SYSPRO | SYSPRO | ✅ e.net/OData | ✅ | ✅ SQL Server | ✅ | FTP ✅ |
| Plex | Rockwell | ✅ | ✅ | ✅ | ✅ Automotive EDI | Rockwell ecosystem |
| QAD Adaptive ERP | QAD | ✅ Swagger | ✅ legacy | ✅ | ✅ Cloud EDI | — |
| DELMIAworks (IQMS) | Dassault | ✅ | ✅ .NET SOA | ✅ SQL Server | ✅ | — |
| Odoo | Odoo S.A. | ✅ JSON-RPC | ✅ XML-RPC | ✅ PostgreSQL | Via module | Webhooks ✅ |

### Niche and specialized ERP

The following systems all support at minimum **REST API and SQL/ODBC connectivity**, with many also supporting SOAP, FTP, and EDI: Global Shop Solutions, Exact (Globe/Online), Priority ERP, Rootstock (Salesforce-based, inherits REST/SOAP/Webhooks/WebSockets), ABAS ERP, ECi M1, ECi JobBOSS², Cetec ERP, MIE Trak Pro, Genius ERP, OptiProERP (SAP B1-based), BatchMaster ERP, ProcessPro, Deacom ERP, Aptean (Ross, Apprise, Made2Manage), Visibility.ONE, VAI S2K, Datacor ERP, Blue Link ERP, Deltek Costpoint, and Epicor Prophet 21.

---

## MES, SCADA, historians, and IIoT platforms speak Fuuz's native language

This category represents Fuuz's core integration territory. These platforms share the same industrial protocols — **OPC UA, MQTT, Sparkplug B, EtherNet/IP, Modbus TCP** — that Fuuz's gateway drivers use natively.

### Manufacturing execution systems (MES)

| System | Vendor | REST | OPC UA | MQTT | SOAP | SQL | EtherNet/IP / Modbus |
|--------|--------|------|--------|------|------|-----|---------------------|
| Siemens Opcenter | Siemens | ✅ | ✅ | Via adapter | ✅ | ✅ | Via adapter |
| AVEVA MES | AVEVA | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ 240+ drivers |
| GE Proficy MES | GE Digital | ✅ | ✅ | ✅ | ✅ | ✅ | Via adapter |
| Dassault DELMIA Apriso | Dassault | ✅ | ✅ | Via adapter | ✅ | ✅ | — |
| SAP ME/MII | SAP | ✅ | ✅ via PCo | Via PCo | ✅ | ✅ | Via PCo |
| Sepasoft MES (Ignition) | Sepasoft | ✅ | ✅ | ✅ | — | ✅ | ✅ **+ Sparkplug B** |
| Parsec TrakSYS | Parsec | ✅ | ✅ | ✅ | — | ✅ | Via adapter |
| Critical Manufacturing MES | Critical Mfg | ✅ | ✅ | ✅ | — | ✅ | Via adapter |
| iTAC | Dürr Group | ✅ | ✅ | ✅ | ✅ | ✅ | Via adapter |
| Tulip | Tulip | ✅ | ✅ | ✅ | — | ✅ | — |
| MachineMetrics | MachineMetrics | ✅ | Via adapter | ✅ | — | — | MTConnect |
| FORCAM FORCE | FORCAM | ✅ | ✅ | ✅ | — | ✅ | Via adapter |

Additional MES platforms with open integration: Rockwell Plex MES, 42Q, Aegis FactoryLogix, MPDV Hydra, Epicor Advanced MES, Lighthouse Systems, Shoplogix, Augury, Sight Machine, and Rockwell FactoryTalk ProductionCentre.

### SCADA systems

**Every major SCADA platform supports OPC UA and Modbus TCP natively**, making them directly reachable by Fuuz gateway drivers.

| System | Vendor | OPC UA | MQTT | Modbus TCP | Sparkplug B | REST | SQL | WebSockets |
|--------|--------|--------|------|------------|-------------|------|-----|------------|
| **Ignition** | Inductive Automation | ✅ | ✅ | ✅ | ✅ Reference impl. | ✅ | ✅ | ✅ |
| AVEVA System Platform | AVEVA | ✅ | ✅ | ✅ | Via DataHub | — | ✅ | — |
| Siemens WinCC / WinCC OA | Siemens | ✅ | ✅ | ✅ | — | ✅ | ✅ | ✅ |
| GE Digital iFIX | GE Digital | ✅ | Via adapter | ✅ | — | — | ✅ | — |
| Rockwell FactoryTalk View | Rockwell | ✅ | — | — | — | — | ✅ | — |
| Schneider Geo SCADA Expert | Schneider | ✅ | ✅ | ✅ | — | ✅ | ✅ | — |
| COPA-DATA zenon | COPA-DATA | ✅ | ✅ | ✅ | — | ✅ | ✅ | — |
| VTScada | Trihedral | ✅ | ✅ | ✅ | — | ✅ | ✅ | ✅ |
| GENESIS64 | ICONICS/Mitsubishi | ✅ | ✅ | ✅ | — | ✅ | ✅ | ✅ |

Also supporting OPC UA and Modbus: ABB Ability Symphony Plus, Emerson DeltaV, Honeywell Experion PKS, Yokogawa CENTUM VP, Rapid SCADA, Indusoft Web Studio (AVEVA), and Citect SCADA (Schneider).

### Process historians and time-series databases

| System | Vendor | REST API | OPC UA | MQTT | SQL/ODBC |
|--------|--------|----------|--------|------|----------|
| OSIsoft PI (AVEVA PI) | AVEVA | ✅ PI Web API | Via PI Adapter | Via Cirrus Link connector | ✅ PI OLEDB/SQL |
| GE Proficy Historian | GE Digital | ✅ | ✅ Server built-in | ✅ Collector | ✅ |
| AVEVA Historian | AVEVA | ✅ AVEVA Insight | ✅ | Via adapter | ✅ InSQL/ODBC |
| Honeywell Uniformance PHD | Honeywell | ✅ | ✅ | — | ✅ |
| AspenTech InfoPlus.21 | AspenTech | ✅ | ✅ | — | ✅ |
| Canary Labs Historian | Canary Labs | ✅ | ✅ | ✅ | ✅ |
| InfluxDB | InfluxData | ✅ | — | ✅ via Telegraf | ✅ |
| TimescaleDB | Timescale | Via PostgREST | — | — | ✅ PostgreSQL |
| TDengine | TDengine | ✅ | — | ✅ taosAdapter | ✅ JDBC/ODBC |

### IIoT platforms and MQTT brokers

These platforms form the connective tissue between edge devices and enterprise systems. **Fuuz's Sparkplug B and MQTT support ensures native interoperability** with this entire category.

| Platform | Vendor | MQTT | OPC UA | Sparkplug B | REST | EtherNet/IP / Modbus |
|----------|--------|------|--------|-------------|------|---------------------|
| **KEPServerEX** | PTC | ✅ IoT Gateway | ✅ 160+ drivers | — | ✅ | ✅ Both native |
| **Litmus Edge** | Litmus | ✅ | ✅ | ✅ | ✅ | ✅ 250+ drivers |
| **HiveMQ** | HiveMQ | ✅ Broker | Via HiveMQ Edge | ✅ Aware extension | ✅ | Via Edge bridge |
| **Cirrus Link** | Cirrus Link | ✅ | ✅ | ✅ Spec authors | ✅ | Via Ignition |
| **HighByte Intelligence Hub** | HighByte | ✅ | ✅ | ✅ | ✅ | ✅ Modbus native |
| AWS IoT Core | AWS | ✅ | — | — | ✅ | — |
| Azure IoT Hub | Microsoft | ✅ | Via Edge module | — | ✅ | Via Edge module |
| ThingWorx | PTC | ✅ | ✅ via Kepware | — | ✅ | Via Kepware |
| Siemens Insights Hub | Siemens | ✅ | ✅ | — | ✅ | ✅ S7/Modbus |
| Eurotech Everyware | Eurotech | ✅ | Via adapter | ✅ | ✅ | ✅ Modbus |
| Eclipse Mosquitto | Eclipse | ✅ Broker | — | ✅ Compatible | — | — |
| EMQX | EMQ | ✅ Broker | — | ✅ | ✅ | — |
| Losant | Losant | ✅ | — | — | ✅ | — |
| Cumulocity IoT | Software AG | ✅ | Via adapter | — | ✅ | Via adapter |
| AVEVA Connect | AVEVA | ✅ | ✅ | — | ✅ | — |

---

## PLCs, robots, and industrial devices connect through standard fieldbus protocols

Fuuz gateway drivers support EtherNet/IP, Modbus TCP, and OPC UA — the three protocols that cover virtually every PLC and robot on the planet. **OPC UA has become near-universal** across major PLC manufacturers, while EtherNet/IP dominates North American discrete manufacturing and PROFINET dominates European installations.

### PLC manufacturers and protocol support

| Manufacturer | Key Products | EtherNet/IP | Modbus TCP | OPC UA | MQTT | PROFINET |
|-------------|-------------|-------------|------------|--------|------|----------|
| **Allen-Bradley/Rockwell** | ControlLogix, CompactLogix | ✅ Native | Via module | ✅ Embedded (V36+) | Via gateway | Via module |
| **Siemens** | S7-1200, S7-1500 | Via module | ✅ | ✅ Built-in | ✅ OPC UA PubSub | ✅ Native |
| **ABB** | AC500 | ✅ | ✅ Native | ✅ | ✅ Ability platform | ✅ |
| **Schneider Electric** | Modicon M340, M580 | ✅ | ✅ Native (invented Modbus) | ✅ Module | Via gateway | Via module |
| **Mitsubishi** | MELSEC iQ-R, iQ-F | ✅ iQ-R | Via FB | ✅ Module | Via gateway | — |
| **Omron** | NX/NJ Series | ✅ Native | ✅ FB | ✅ Built-in | ✅ FB library | — |
| **Beckhoff** | TwinCAT | ✅ | ✅ | ✅ TF6100 | ✅ TF6105 | ✅ |
| **B&R Automation** | X20, X90 | ✅ | ✅ | ✅ Built-in | ✅ | ✅ |
| **Phoenix Contact** | PLCnext | ✅ | ✅ | ✅ Built-in | ✅ | ✅ Native |
| **WAGO** | PFC100/200 | ✅ | ✅ Native | ✅ Built-in | ✅ Native | — |
| **Emerson** | PACSystems RX3i | ✅ | ✅ Native | ✅ | Via edge | ✅ |
| **Bosch Rexroth** | ctrlX CORE | ✅ | ✅ | ✅ | ✅ | ✅ |

Additional PLC platforms with Modbus TCP and/or EtherNet/IP support: Honeywell ControlEdge, AutomationDirect (BRX, Productivity), Red Lion Controls, Delta Electronics, Unitronics, Keyence, IDEC, Turck, and Yokogawa (STARDOM, FA-M3).

### Robot manufacturers

| Manufacturer | EtherNet/IP | OPC UA | MQTT | Modbus TCP | REST API |
|-------------|-------------|--------|------|------------|----------|
| **FANUC** | ✅ R648 option | ✅ R553 option | Via gateway | — | — |
| **ABB Robotics** | ✅ | ✅ RobotWare 7 | ✅ IoT Gateway | — | ✅ Robot Web Services |
| **KUKA** | ✅ | ✅ KUKA.OPC UA | Via gateway | — | — |
| **Yaskawa Motoman** | ✅ | Via SDK | Via gateway | — | — |
| **Universal Robots** | Via URCaps | Via URCaps | Via URCaps | ✅ Built-in | RTDE/Dashboard |
| **Stäubli** | ✅ | ✅ CS9 | — | — | — |
| **Comau** | ✅ | ✅ | — | — | — |
| **Franka Emika** | — | — | — | — | ✅ libfranka |

Additional robot manufacturers supporting EtherNet/IP: Mitsubishi Robots, Kawasaki Robotics, Doosan Robotics, Techman Robot, DENSO Robotics, and Epson Robots.

### Industrial gateways and edge devices

These protocol converters bridge legacy and modern industrial systems — HMS Networks (Anybus bridges ~20 networks; Ewon supports MQTT/REST), Opto 22 groov EPIC/RIO (**native MQTT, Sparkplug B, OPC UA, REST**), Advantech (OPC UA, MQTT, Modbus, REST), Moxa (Modbus, OPC UA, MQTT, EtherNet/IP), Softing dataFEED (OPC UA specialist with MQTT publisher), Hilscher netX (EtherNet/IP, PROFINET, Modbus, OPC UA, MQTT), ProSoft Technology (EtherNet/IP, Modbus, PROFINET), Digi International, and Weidmüller.

### Industrial sensors and I/O

Modern smart sensors from **Balluff, SICK, Banner Engineering, Endress+Hauser, and Emerson Rosemount** increasingly support IO-Link, OPC UA, and even MQTT directly. When connected through IO-Link masters or industrial gateways, their data flows through EtherNet/IP, PROFINET, or Modbus TCP — all protocols Fuuz handles natively.

---

## Business software: CRM, HR, eCommerce, and POS

These categories communicate almost exclusively through **REST APIs and webhooks** — both fully supported by Fuuz cloud connectors.

### CRM systems

Every major CRM supports REST API and webhooks: **Salesforce** (REST, SOAP, Webhooks, Streaming API), **Microsoft Dynamics 365** (OData REST, SOAP, Webhooks), **HubSpot** (REST, Webhooks), **Zoho CRM** (REST, SOAP, Webhooks), **SugarCRM** (REST, SOAP, SQL, Webhooks), **Pipedrive**, **Freshsales**, **Monday.com**, **Insightly**, **Copper**, **Keap/Infusionsoft**, **Vtiger**, **Close CRM**, and **Zendesk Sell**.

### HR and HCM platforms

| System | Vendor | REST API | SOAP | FTP/SFTP | Webhooks |
|--------|--------|----------|------|----------|----------|
| ADP Workforce Now | ADP | ✅ | — | ✅ Batch payroll | ✅ Event notifications |
| Workday HCM | Workday | ✅ | ✅ Primary | ✅ EIB | — (requires polling) |
| SAP SuccessFactors | SAP | ✅ OData | ✅ Legacy | ✅ | ✅ Intelligent Services |
| UKG Pro/Ready | UKG | ✅ | ✅ Legacy | ✅ | ✅ Event Service |
| Ceridian Dayforce | Dayforce | ✅ | — | ✅ | ✅ |
| BambooHR | BambooHR | ✅ | — | — | ✅ |

Also REST API-enabled: Paychex, Paycom, Paylocity, Gusto, Rippling, Namely, Oracle HCM Cloud, and Cornerstone OnDemand.

### eCommerce platforms

All support REST API and webhooks: **Shopify/Plus** (REST + GraphQL + Webhooks), **Adobe Commerce/Magento** (REST, SOAP, GraphQL, SQL, FTP, Webhooks), **WooCommerce** (REST, SQL, Webhooks), **BigCommerce** (REST, GraphQL, Webhooks), **Salesforce Commerce Cloud** (REST, SOAP, FTP, Webhooks), **SAP Commerce Cloud/Hybris** (REST, SOAP, FTP, SQL, EDI), **Commercetools** (REST, GraphQL, Webhooks), **VTEX**, **Saleor** (GraphQL, WebSockets, Webhooks), **Elastic Path**, **PrestaShop**, **OpenCart**, and **Wix/Squarespace**.

### Point-of-sale and payments

REST API + Webhooks: **Square**, **Clover**, **Toast**, **Lightspeed**, **Shopify POS**, **Revel Systems**, **Stripe** (comprehensive REST + Webhooks), **PayPal/Braintree**, **Adyen** (REST + SFTP + Webhooks), **Oracle MICROS** (REST + SOAP + SQL + WebSockets), and **NCR Aloha**.

---

## WMS, supply chain, shipping, and logistics

**EDI is the dominant integration protocol** in this space, alongside REST APIs and FTP/SFTP. Over 80% of WMS and TMS platforms support EDI natively.

### Warehouse management systems

| System | Vendor | REST | SOAP | FTP/SFTP | SQL | EDI | Webhooks |
|--------|--------|------|------|----------|-----|-----|----------|
| Manhattan Associates | Manhattan | ✅ | ✅ | ✅ | ✅ | ✅ X12/EDIFACT | ✅ |
| Blue Yonder | Blue Yonder | ✅ | ✅ | ✅ | ✅ MOCA | ✅ X12/EDIFACT | — |
| SAP EWM | SAP | ✅ OData | ✅ | ✅ | ✅ HANA | ✅ IDoc | — |
| Oracle WMS Cloud | Oracle | ✅ | ✅ | ✅ | ✅ | ✅ | — |
| Körber WMS | Körber/Infios | ✅ | ✅ | ✅ | ✅ SQL SP | ✅ | — |
| Infor WMS | Infor | ✅ ION | ✅ | ✅ | ✅ | ✅ BOD/X12 | Via ION |
| Fishbowl | Fishbowl | ✅ | — | ✅ | ✅ | Via partner | — |
| 3PL Central (Extensiv) | Extensiv | ✅ | — | ✅ | — | ✅ 80+ partners | ✅ |

Also EDI-capable: SnapFulfil, Deposco, Softeon, Cin7, NetSuite WMS, Odoo Inventory, and many more.

### Supply chain planning and procurement

EDI and REST dominate: **Kinaxis RapidResponse**, **o9 Solutions**, **Blue Yonder Luminate Planning**, **SAP IBP**, **Oracle Supply Chain Planning**, **Coupa** (REST + cXML + EDI), **E2open** (EDI core competency, X12/EDIFACT), **SAP Ariba** (REST + cXML + EDI), **Jaggaer**, **Ivalua**, and **GEP SMART**.

### Shipping and logistics

| Carrier/Platform | REST API | SOAP | EDI | Webhooks | FTP/SFTP |
|-----------------|----------|------|-----|----------|----------|
| UPS | ✅ OAuth 2.0 | ✅ Legacy (sunset 2025) | ✅ X12 214/210/856 | — | ✅ |
| FedEx | ✅ OAuth 2.0 | ✅ Legacy (retiring June 2026) | ✅ X12 | ✅ Push tracking | — |
| USPS | ✅ OAuth 2.0 (new V3) | Retired | — | — | — |
| DHL | ✅ | — | ✅ X12/EDIFACT | ✅ | ✅ |
| ShipStation | ✅ | — | — | ✅ Extensive | — |
| EasyPost | ✅ | — | — | ✅ | — |

Enterprise TMS platforms with full EDI support: Oracle Transportation Management (**10,000+ carrier connections**), SAP TM, MercuryGate, Descartes Systems (one of the world's largest logistics EDI networks), BluJay/E2open, Trimble TMS, TMW Systems, and McLeod Software. Supply chain visibility platforms project44 and FourKites support both REST APIs and EDI for carrier connectivity.

---

## Quality management and maintenance systems

### Quality management systems (QMS) and SPC

| System | Vendor | REST | SOAP | SQL/ODBC | OPC UA | Webhooks |
|--------|--------|------|------|----------|--------|----------|
| ETQ Reliance | Hexagon | ✅ | ✅ | ✅ | — | — |
| MasterControl | MasterControl | ✅ | ✅ | ✅ | — | — |
| Veeva Vault Quality | Veeva | ✅ Extensive | ✅ | — | — | — |
| TrackWise Digital | Honeywell | ✅ | ✅ | ✅ | — | — |
| **InfinityQS ProFicient/Enact** | Advantive | ✅ | — | ✅ | **✅ OPC UA + OPC DA** | — |
| ComplianceQuest | ComplianceQuest | ✅ | ✅ | — | — | ✅ Salesforce |
| Oracle QualityOne | Oracle | ✅ | ✅ | ✅ | — | — |

**InfinityQS stands out as the only QMS with native OPC UA/DA support** for direct shop floor data collection. Additional QMS platforms with REST APIs: Qualio, Greenlight Guru, Arena Quality (PTC), Intelex, IQS/CEBOS, QT9, Propel Quality, and Net-Inspect (aerospace). Metrology software (Hexagon PC-DMIS, Zeiss CALYPSO, Mitutoyo MeasurLink) primarily uses ODBC/SQL and file-based integration. SPC tools (WinSPC, Zontec, Hertzler ProFicient) support ODBC and OPC for automated data collection.

### CMMS and EAM systems

| System | Vendor | REST | SOAP | SQL/ODBC | MQTT | Webhooks |
|--------|--------|------|------|----------|------|----------|
| **IBM Maximo** | IBM | ✅ MIF/OSLC | ✅ | ✅ DB2/Oracle/SQL Server | ✅ MAS Monitor | ✅ |
| **SAP PM / S/4HANA** | SAP | ✅ OData | ✅ RFC/BAPI | ✅ HANA | ✅ via PCo | ✅ |
| Infor/HxGN EAM | Hexagon | ✅ | ✅ WS Hub | ✅ Oracle | — | — |
| Oracle eAM | Oracle | ✅ | ✅ | ✅ | — | — |
| Fiix | Rockwell | ✅ | — | ✅ | — | ✅ |
| UpKeep | UpKeep | ✅ | — | — | — | ✅ |
| MaintainX | MaintainX | ✅ | — | — | — | ✅ |
| eMaint | Fluke/Fortive | ✅ | — | ✅ | — | ✅ |
| Limble | Limble | ✅ | — | — | — | ✅ |
| Fracttal | Fracttal | ✅ | — | — | ✅ IoT | ✅ |

Additional CMMS platforms with REST APIs: Brightly (Siemens), FMX, Hippo CMMS, Coast, ManagerPlus, MAPCON, TMA Systems, MEX, MicroMain, Infraspeak, Maxpanda, EZOfficeInventory, Asset Panda, and ServiceChannel (also supports EDI).

---

## Data platforms, BI tools, databases, and cloud infrastructure

### Cloud data warehouses

All support **REST API and ODBC/JDBC**: **Snowflake**, **Databricks**, **Google BigQuery**, **Amazon Redshift**, and **Azure Synapse Analytics**. These are the most common destinations for industrial data aggregation.

### BI and analytics platforms

All support REST API; most support ODBC/SQL for data source connectivity: **Microsoft Power BI** (REST, ODBC, SQL), **Tableau** (REST, ODBC, Webhooks), **Looker** (REST, SQL, Webhooks), **Qlik Sense** (REST, ODBC), **Grafana** (REST, SQL, Webhooks, **MQTT plugin**), **Sisense**, **Domo** (REST, Webhooks, ODBC), **ThoughtSpot**, **Metabase**, **Apache Superset**, **MicroStrategy**, **TIBCO Spotfire**, **SAP Analytics Cloud**, **Oracle Analytics Cloud**, and **IBM Cognos Analytics**. Grafana's official MQTT data source plugin makes it particularly relevant for real-time industrial dashboarding.

### Databases

| Database | ODBC/JDBC | REST API | MQTT | SQL |
|----------|-----------|----------|------|-----|
| PostgreSQL | ✅ | Via PostgREST | — | ✅ |
| MySQL / MariaDB | ✅ | — | — | ✅ |
| Microsoft SQL Server | ✅ | ✅ (2022+) | — | ✅ |
| Oracle Database | ✅ | ✅ ORDS | — | ✅ |
| MongoDB | ✅ BI Connector | ✅ Atlas Data API | — | — |
| InfluxDB | Limited | ✅ | ✅ via Telegraf | ✅ (v3) |
| Redis | — | ✅ Modules | — | — |
| Elasticsearch | ODBC (paid) | ✅ Native | — | — |
| CockroachDB | ✅ PG-compatible | ✅ | — | ✅ |
| TimescaleDB | ✅ PG-compatible | Via PostgREST | — | ✅ |
| Azure Cosmos DB | ✅ | ✅ | — | ✅ SQL API |
| Neo4j | ✅ Connector | ✅ | — | Cypher |
| SingleStore | ✅ | ✅ Data API | — | ✅ |
| IBM Db2 | ✅ | ✅ | — | ✅ |
| Teradata | ✅ | ✅ | — | ✅ |
| Amazon DynamoDB | Via CData driver | ✅ | — | PartiQL |
| Firebase/Firestore | — | ✅ | — | — |
| Supabase | ✅ PG | ✅ PostgREST | — | ✅ |
| Cassandra | Via DataStax driver | — | — | CQL |
| Couchbase | Via Simba driver | ✅ | — | SQL++ |

### Cloud serverless and iPaaS

**AWS Lambda**, **Azure Functions**, and **Google Cloud Functions** all expose HTTP/REST triggers, making them callable from Fuuz. iPaaS platforms provide the middleware layer:

- **Node-RED**: The most protocol-rich low-code platform — **native MQTT, WebSocket, REST, OPC UA (community), Modbus, and FTP**
- **Apache Camel**: Official OPC UA support (camel-milo), plus MQTT, REST, SOAP, FTP, SQL
- **Apache NiFi**: Built-in MQTT processors, OPC UA via community bundles, FTP, REST
- **MuleSoft**: REST, SOAP, MQTT (official connector), FTP, ODBC, WebSockets
- **Azure Logic Apps**: REST, SOAP, FTP, Webhooks (400+ connectors)
- **Workato**: REST, SOAP, Webhooks, ODBC
- **Zapier / Make / n8n**: REST + Webhooks (n8n adds MQTT)
- **Dell Boomi**: REST, SOAP, FTP, ODBC, MQTT
- **Celigo / SnapLogic**: REST, SOAP, FTP, ODBC
- **Microsoft Power Automate**: REST, Webhooks, ODBC
- **Talend**: REST, SOAP, ODBC, FTP, MQTT

---

## AI, LLM platforms, and collaboration tools

### AI and LLM platforms

Every major AI platform exposes a **REST API**, making LLM capabilities accessible through Fuuz's cloud connectors: **OpenAI** (REST + Webhooks + WebSockets for Realtime API), **Anthropic Claude** (REST), **Google Gemini/Vertex AI** (REST + WebSockets for Live API), **Azure OpenAI** (REST + Webhooks + WebSockets), **AWS Bedrock** (REST + EventBridge), **Meta Llama** (REST via hosting providers), **Mistral AI**, **Cohere**, **Hugging Face** (REST + Webhooks), **Replicate** (REST + Webhooks), **NVIDIA NIM**, **Databricks AI**, **IBM watsonx** (REST + legacy SOAP), **DeepSeek**, **xAI/Grok**, **Groq**, **Together AI**, and **Perplexity AI**.

### Document management and collaboration

| Platform | REST API | SOAP | Webhooks | SQL/ODBC | FTP |
|----------|----------|------|----------|----------|-----|
| Microsoft SharePoint | ✅ Graph API | ✅ Legacy | ✅ | ✅ SQL Server | — |
| Google Workspace | ✅ | — | ✅ Pub/Sub | — | — |
| Box | ✅ 150+ endpoints | — | ✅ | — | — |
| DocuSign | ✅ | ✅ Legacy | ✅ Connect | — | — |
| OpenText | ✅ | ✅ | — | ✅ | ✅ |
| Alfresco (Hyland) | ✅ CMIS | ✅ | ✅ | — | ✅ |
| Laserfiche | ✅ | ✅ Legacy | ✅ | ✅ | — |

Additional platforms with REST API + Webhooks: **Atlassian** (Jira, Confluence, Bitbucket), **Asana**, **Monday.com**, **Trello**, **Notion**, **ClickUp**, **Wrike**, **Smartsheet**, **Slack** (REST + WebSockets), **Microsoft Teams** (REST + WebSockets), **Zoom**, **Webex**, **ServiceNow** (REST + SOAP + Webhooks), **Zendesk**, **Freshdesk**, **Airtable**, **Dropbox**, **PandaDoc**, **DocuWare**, and **Adobe Acrobat Sign**.

### DevOps platforms

All support REST API + Webhooks: **GitHub**, **GitLab**, **Azure DevOps**, **Jenkins**, **Docker Hub**, **Kubernetes** (REST + Webhooks + WebSockets), **Terraform**, **Ansible**, **CircleCI**, **Datadog**, **Splunk** (REST + Webhooks + ODBC), **New Relic**, **PagerDuty**, and **Grafana**.

---

## PLM and engineering software

### Enterprise PLM platforms

| System | Vendor | REST | SOAP | OPC UA | SQL/ODBC | Webhooks |
|--------|--------|------|------|--------|----------|----------|
| Siemens Teamcenter | Siemens | ✅ | ✅ T4EA | ✅ Via Insights Hub | ✅ JDBC/ODBC | Via SOA |
| PTC Windchill | PTC | ✅ OData/Swagger | ✅ Info*Engine | — | ✅ | — |
| Dassault 3DEXPERIENCE | Dassault | ✅ | ✅ Legacy | — | ✅ | Via JMS |
| Arena Solutions | PTC | ✅ Comprehensive | — | — | — | ✅ |
| Aras Innovator | Aras | ✅ OData (v11+) | ✅ Primary | — | ✅ SQL | — |
| Autodesk Vault | Autodesk | ✅ | ✅ Legacy | — | ✅ SQL Server | ✅ APS |
| Oracle Agile PLM | Oracle | — | ✅ Primary (BPEL) | — | ✅ Oracle DB | — |
| SAP PLM | SAP | ✅ OData | ✅ RFC/BAPI | ✅ Via PCo | ✅ HANA | ✅ Event Mesh |
| OpenBOM | OpenBOM | ✅ | — | — | — | ✅ |
| Propel PLM | Propel | ✅ Salesforce | ✅ Salesforce | — | — | ✅ |

CAD software (Siemens NX, PTC Creo, SOLIDWORKS, CATIA, Autodesk Inventor/AutoCAD) primarily uses desktop APIs (.NET, COM, C++) rather than network protocols, but cloud versions increasingly expose REST APIs through their respective platforms.

---

## Automotive and aerospace industry software

**Automotive**: QAD Adaptive ERP (REST + **EDI critical for OEM supply chains**, X12/EDIFACT/VDA), Plex (REST + EDI), SupplyOn (REST + EDIFACT/WebEDI for Bosch/German OEMs), and VDA-specific EDI tools (SEEBURGER, OpenText, Data Interchange — supporting VDA 4905, 4913, 4915 message formats over OFTP2). AIAG defines standard EDI transaction sets (X12 830, 856, 862) used across the North American automotive supply chain.

**Aerospace**: Deltek Costpoint (REST + SOAP + SQL + FTP), IFS Aerospace & Defense (REST + SQL), Epicor for Aerospace (REST + SOAP + SQL + EDI), Quantum Control (GraphQL + Oracle DB), and the Siemens/Dassault/SAP aerospace solutions inherit their parent platforms' full integration capabilities. Net-Inspect provides aerospace-focused quality data exchange.

---

## EDI standards: the complete reference

Fuuz's EDI connectivity supports all major electronic data interchange standards. The EDINation/EdiFabric Spec Library — one of the most comprehensive EDI reference platforms — covers **10+ standard families across 46+ EDIFACT versions and 300+ X12 transaction sets**.

### ANSI ASC X12 (dominant in North America)

Versions range from 2040 through 8060, with **4010 most common in retail** and **5010 mandated for healthcare**. Key transaction sets:

- **Retail/supply chain**: 850 (Purchase Order), 855 (PO Acknowledgment), 856 (ASN/Ship Notice), 810 (Invoice), 846 (Inventory), 832 (Price Catalog), 830 (Planning Schedule), 860 (PO Change), 997/999 (Acknowledgments)
- **Transportation**: 204 (Load Tender), 210 (Freight Details), 214 (Shipment Status), 990 (Load Response)
- **Warehouse**: 940 (Shipping Order), 943/944/945 (Stock Transfer), 947 (Inventory Adjustment)
- **Finance**: 820 (Payment/Remittance), 824 (Application Advice)

### X12 HIPAA (US healthcare — mandatory)

Version 005010 is currently mandated; 007030 is next. Complete transaction set: **837P/I/D** (Claims), **835** (Payment/Remittance), **270/271** (Eligibility), **276/277** (Claim Status), **278** (Prior Authorization), **834** (Enrollment), **820** (Premium Payment), **999** (Acknowledgment).

### UN/EDIFACT (dominant internationally)

Over **200 message types** across 46+ directory versions (D.93A through D.22A). Key messages: ORDERS, INVOIC, DESADV (ASN), DELFOR (Delivery Forecast), DELJIT (Just-in-Time), PRICAT (Price Catalog), SLSRPT (Sales Report), INVRPT (Inventory), CUSCAR/CUSDEC (Customs), BAPLIE (Maritime Bayplan), and CONTRL/APERAK (Acknowledgments).

### Industry-specific EDI standards

- **EANCOM/GS1** (global retail): EDIFACT subset with GS1 identifiers (GTINs, GLNs, SSCCs). Key messages mirror EDIFACT: ORDERS, INVOIC, DESADV, PRICAT, SLSRPT
- **VDA** (German automotive): Fixed-length format — VDA 4905 (Delivery Schedule), 4913 (ASN), 4915 (JIT Call-Off), 4906 (Invoice). Newer 4984/4987 are EDIFACT-based
- **HL7** (healthcare): v2.x (pipe-delimited, 95% of US healthcare), v3 (XML), and **FHIR** (modern REST-based, R4 current). Key messages: ADT (Admit/Discharge), ORM (Orders), ORU (Results), SIU (Scheduling), VXU (Immunizations)
- **NCPDP SCRIPT** (e-prescribing): NEWRX, REFREQ/REFRES, RXCHG, CANRX, medication history. Version 2017071 currently mandated
- **NCPDP Telecom** (pharmacy claims): Real-time POS claim processing. Version D.0 current, F6 mandated by 2028
- **IATA PADIS** (aviation): PNRGOV (Passenger Name Record), PAXLST (Passenger List) for border control
- **EDIGAS** (European gas): NOMINT (Nominations), ALOCAT (Allocation), DELORD (Delivery) — versions up to 6.1
- **SMDG** (maritime/shipping): BAPLIE (Bayplan), COPARN (Container Announcement), CODECO (Gate-In/Out), VERMAS (Verified Gross Mass)
- **EDIFACT ACE** (US Customs): CUSCAR (Cargo Report), CUSDEC (Declaration) for CBP electronic filing

---

## Conclusion: protocol coverage ensures universal connectivity

This catalog demonstrates that **Fuuz's 11 supported integration protocols collectively cover every software category in enterprise and industrial computing**. Three key patterns emerge from this research:

**REST API is the universal language of modern software.** Every system cataloged here — from SAP S/4HANA to a $20/month CMMS — exposes REST endpoints. Fuuz's cloud connectors leverage this universality to reach any cloud or business application.

**OPC UA and MQTT are converging as the dual standard for industrial data.** Over 85% of SCADA platforms, 75% of MES systems, and virtually all major PLC manufacturers now support OPC UA natively. MQTT adoption is accelerating, with Sparkplug B providing the standardized payload format that Ignition, HiveMQ, Cirrus Link, Litmus Edge, and Opto 22 have championed. Fuuz's gateway drivers speak all three protocols.

**EDI remains irreplaceable for supply chain transactions.** With over 80% of WMS, TMS, and supply chain platforms supporting X12 and/or EDIFACT, and industry-specific standards (VDA for automotive, HL7 for healthcare, EANCOM for retail) deeply embedded in their respective sectors, EDI connectivity gives Fuuz access to the transactional backbone of global commerce.

The bottom line: **if a system exists in your operations, it almost certainly speaks at least one protocol that Fuuz understands** — and usually several.