# ISA-95 Part 5: Business-to-Manufacturing Transactions

**Reference:** ANSI/ISA-95.00.05 / IEC 62264-5
**Scope:** Part 5 defines the transaction models -- the patterns for exchanging information between enterprise systems (Level 4) and manufacturing operations systems (Level 3). While Parts 2-4 define what data to exchange, Part 5 defines how to exchange it: the messaging patterns, standard verbs, and conformance rules.

> **Copyright Note:** ISA-95 is a copyrighted standard published by the International Society of Automation. The descriptions below are written in our own words to explain the concepts and their application to the Fuuz platform. Clause references are provided so readers can consult the source standard for normative definitions.

---

## Three Transaction Models

*Reference: ISA-95 Part 5, Clause 5*

Part 5 defines three fundamental patterns for exchanging information between systems. Each pattern addresses a different communication need:

### PULL Model (Request/Response)

The PULL model is a synchronous request/response pattern where one system requests information or action from another and waits for a reply.

```
System A                          System B
   |                                 |
   |--- Request (Get/Process) ------>|
   |                                 |  (processes request)
   |<-- Response (Show/Respond) -----|
   |                                 |
```

| Aspect | Description |
|--------|-------------|
| **Initiator** | The system that needs information or wants to trigger an action |
| **Pattern** | Request message followed by response message |
| **Timing** | Synchronous -- the requestor waits for the response |
| **Use case** | "Give me the current production schedule" or "Process this work order" |
| **Verb pairs** | Get/Show (read data), Process/Acknowledge (write data), Change/Respond (modify data) |

**Fuuz Mapping:** The PULL model maps directly to Fuuz GraphQL API operations:
- **GraphQL Queries** = Get/Show pattern (request data, receive response)
- **GraphQL Mutations** = Process/Acknowledge pattern (submit data for processing, receive confirmation)
- ERP systems pull production data from Fuuz via GraphQL queries
- Fuuz pulls schedule data from ERP via REST/GraphQL API calls in Flows

### PUSH Model (Unsolicited)

The PUSH model is an asynchronous pattern where one system sends information to another without being asked. The receiver does not need to request it -- the sender decides when to send.

```
System A                          System B
   |                                 |
   |--- Unsolicited Message -------->|
   |    (Process/Notify)             |  (processes message)
   |<-- Acknowledgment --------------|
   |    (Acknowledge/Confirm)        |
   |                                 |
```

| Aspect | Description |
|--------|-------------|
| **Initiator** | The system that has new information to share |
| **Pattern** | Unsolicited message optionally followed by acknowledgment |
| **Timing** | Asynchronous -- sent when the sender determines it is appropriate |
| **Use case** | "Here is the updated production count" or "Equipment has gone down" |
| **Verb pairs** | Process/Acknowledge (push data for processing), Notify/Confirm (push notification) |

**Fuuz Mapping:** The PUSH model maps to several Fuuz mechanisms:
- **Webhooks** = Fuuz pushes event data to external systems when conditions are met
- **ERP integration Flows** = Fuuz pushes production performance data to ERP at job completion
- **MQTT publish** = Fuuz pushes commands or status updates to devices and other systems
- **API callbacks** = External systems push data to Fuuz GraphQL mutations (e.g., ERP pushes a new work order)

### PUBLISH Model (Subscribe)

The PUBLISH model is a broadcast pattern where one system publishes information to a topic, and any number of subscribers receive it. The publisher does not need to know who the subscribers are.

```
Publisher                    Broker/Topic               Subscriber(s)
   |                            |                           |
   |--- Publish (to topic) ---->|                           |
   |                            |--- Deliver to all ------->|
   |                            |    subscribers            |
   |                            |                           |
```

| Aspect | Description |
|--------|-------------|
| **Initiator** | The system that has information to broadcast |
| **Pattern** | Publish to topic; broker delivers to all subscribers |
| **Timing** | Asynchronous, event-driven |
| **Use case** | "Equipment status changed" broadcast to all interested systems |
| **Decoupling** | Publisher and subscribers are decoupled -- neither needs to know about the other |

**Fuuz Mapping:** The PUBLISH model maps directly to Fuuz MQTT infrastructure:
- **MQTT topics** follow the ISA-95 hierarchy: `fuuz/{site}/{area}/{line}/{cell}/{equipment}/{datatype}`
- **Device telemetry** is published by edge gateways to MQTT topics (sensor readings, equipment status)
- **Fuuz event broadcasts** publish operational events (work order status changes, quality alerts) to MQTT topics
- **Multiple consumers** can subscribe: Fuuz Flows, dashboards, historian, external analytics systems
- **UNS (Unified Namespace)** is the Fuuz implementation of the PUBLISH model, providing a single source of truth for all operational data

---

## Message Structure

*Reference: ISA-95 Part 5, Clause 6*

Part 5 defines a standard structure for transaction messages, regardless of which model (PULL, PUSH, PUBLISH) is used.

### Message Components

| Component | Description | Fuuz Mapping |
|-----------|-------------|--------------|
| **Application area** | Identifies the sending system, intended receiver, and message context | GraphQL operation context, MQTT topic path |
| **Data area** | Contains the verb (action to perform) and the noun (object being acted upon) | GraphQL query/mutation name + variables, MQTT message payload |
| **Verb** | The action requested (Get, Process, Change, etc.) | GraphQL operation type + name |
| **Noun** | The business object being acted upon (personnel, equipment, work order, etc.) | GraphQL type being queried/mutated |
| **Response code** | Status of the transaction (success, partial success, failure) | GraphQL response with data or errors |

### B2MML (Business to Manufacturing Markup Language)

Part 5 references B2MML as the standard XML implementation of ISA-95 transaction messages. B2MML provides XML schemas for all ISA-95 objects and transactions.

**Fuuz Mapping:**
- Fuuz uses GraphQL (JSON-based) rather than B2MML (XML-based) as its primary API format
- For B2MML interoperability, Fuuz can provide transformation Flows that convert between B2MML XML and Fuuz GraphQL JSON
- The underlying data models are the same -- only the serialization format differs
- Integration with B2MML-speaking systems (some ERP and MES systems) can be handled by middleware or Fuuz integration Flows

---

## The 13 Standard Verbs

*Reference: ISA-95 Part 5, Clause 7*

Part 5 defines thirteen standard verbs that describe all the actions that can be performed in business-to-manufacturing transactions. These verbs are grouped by the transaction model they support.

### PULL Model Verbs

| Verb | Purpose | Fuuz Equivalent |
|------|---------|-----------------|
| **Get** | Request to retrieve data about one or more objects | GraphQL Query |
| **Show** | Response containing the requested data | GraphQL Query Response (data field) |
| **Process** | Request to create or update an object | GraphQL Mutation (create/update) |
| **Acknowledge** | Response confirming the processing request was received and processed | GraphQL Mutation Response (success/error) |
| **Change** | Request to modify a previously submitted object | GraphQL Mutation (update) |
| **Respond** | Response to a change request | GraphQL Mutation Response |
| **Cancel** | Request to cancel a previously submitted object | GraphQL Mutation (cancel/delete) |

### PUSH Model Verbs

| Verb | Purpose | Fuuz Equivalent |
|------|---------|-----------------|
| **Process** | Unsolicited submission of data for processing (same verb as PULL, but unsolicited) | Webhook callback or API push |
| **Acknowledge** | Confirmation that the pushed data was received and processed | Webhook response or acknowledgment message |
| **Notify** | Unsolicited notification of a condition or event (informational, may not require action) | MQTT publish, push notification, email/SMS alert |
| **Confirm** | Acknowledgment that a notification was received | MQTT QoS acknowledgment, notification read receipt |

### PUBLISH Model Verbs

| Verb | Purpose | Fuuz Equivalent |
|------|---------|-----------------|
| **Publish** | Broadcast data to a topic for all subscribers | MQTT Publish to UNS topic |
| **Sync** | Request to synchronize data between systems (bidirectional) | Fuuz sync Flow (compare and reconcile) |
| **Create** | Announce the creation of a new object | MQTT message on creation event topic |
| **Close** | Announce the closure or completion of an object | MQTT message on completion event topic |

### Verb Interaction Patterns

```
PULL Pattern:
    Get ---------> Show
    Process -----> Acknowledge
    Change ------> Respond
    Cancel ------> Acknowledge

PUSH Pattern:
    Process -----> Acknowledge  (unsolicited)
    Notify ------> Confirm      (unsolicited)

PUBLISH Pattern:
    Publish -----> (delivered to subscribers)
    Sync --------> (bidirectional reconciliation)
    Create ------> (announced to subscribers)
    Close -------> (announced to subscribers)
```

---

## Standard Nouns

*Reference: ISA-95 Part 5, Clause 8*

The nouns represent the business objects that can be exchanged using the standard verbs. Each noun corresponds to an object model defined in Parts 2-4.

### Resource Nouns

| Noun | ISA-95 Source | Fuuz Model(s) |
|------|---------------|----------------|
| **Personnel** | Part 2 personnel model | `User`, `Operator`, `PersonnelClass` |
| **Personnel Class** | Part 2 personnel class model | `PersonnelClass` |
| **Equipment** | Part 2 equipment model | `Asset`, `Cell`, `Line`, `Workcenter` |
| **Equipment Class** | Part 2 equipment class model | `EquipmentClass` |
| **Physical Asset** | Part 2 physical asset model | `PhysicalAsset` |
| **Physical Asset Class** | Part 2 physical asset class model | `PhysicalAssetClass` |
| **Material Definition** | Part 2 material definition model | `Product`, `MaterialDefinition` |
| **Material Class** | Part 2 material class model | `MaterialClass` |
| **Material Lot** | Part 2 material lot model | `Lot` |
| **Material Sublot** | Part 2 material sublot model | `Sublot` |

### Process Nouns

| Noun | ISA-95 Source | Fuuz Model(s) |
|------|---------------|----------------|
| **Process Segment** | Part 2 process segment model | `Operation`, `ProcessStep` |

### Operations Nouns (Business View)

| Noun | ISA-95 Source | Fuuz Model(s) |
|------|---------------|----------------|
| **Operations Definition** | Part 2 operations definition model | `Routing` |
| **Operations Schedule** | Part 2 operations schedule model | Collection of `WorkOrder` |
| **Operations Performance** | Part 2 operations performance model | Collection of `ProductionLog` |
| **Operations Capability** | Part 2 operations capability model | `WorkcenterCapability` |

### Work Nouns (Work View -- Part 4)

| Noun | ISA-95 Source | Fuuz Model(s) |
|------|---------------|----------------|
| **Work Master** | Part 4 work master model | `WorkMaster`, `Operation` |
| **Work Directive** | Part 4 work directive model | `WorkDirective`, `RoutingStep` |
| **Work Schedule** | Part 4 work schedule model | Collection of `JobOrder` |
| **Work Performance** | Part 4 work performance model | Collection of `JobResponse` |
| **Work Capability** | Part 4 work capability model | Dynamic capability calculation |
| **Work Alert** | Part 4 work alert model | `Alert` |
| **Work Calendar** | Part 4 work calendar model | `WorkCalendar` |

---

## Verb-Noun Action Matrix

*Reference: ISA-95 Part 5, Clause 9*

Not every verb applies to every noun. Part 5 defines which verb-noun combinations are valid. The following table summarizes the primary patterns:

### Resource Nouns -- Applicable Verbs

| Noun | Get/Show | Process/Ack | Change/Respond | Cancel/Ack | Publish |
|------|----------|-------------|----------------|------------|---------|
| Personnel | Yes | Yes | Yes | Yes | Yes |
| Equipment | Yes | Yes | Yes | Yes | Yes |
| Physical Asset | Yes | Yes | Yes | Yes | Yes |
| Material Definition | Yes | Yes | Yes | Yes | Yes |
| Material Lot | Yes | Yes | Yes | Yes | Yes |
| Material Sublot | Yes | Yes | Yes | Yes | Yes |
| Material Class | Yes | Yes | Yes | Yes | Yes |

### Operations Nouns -- Applicable Verbs

| Noun | Get/Show | Process/Ack | Change/Respond | Cancel/Ack | Publish |
|------|----------|-------------|----------------|------------|---------|
| Operations Definition | Yes | Yes | Yes | -- | Yes |
| Operations Schedule | Yes | Yes | Yes | Yes | Yes |
| Operations Performance | Yes | Yes | Yes | -- | Yes |
| Operations Capability | Yes | Yes | -- | -- | Yes |
| Process Segment | Yes | Yes | Yes | -- | Yes |

### Work Nouns -- Applicable Verbs

| Noun | Get/Show | Process/Ack | Change/Respond | Cancel/Ack | Publish |
|------|----------|-------------|----------------|------------|---------|
| Work Master | Yes | Yes | Yes | -- | Yes |
| Work Directive | Yes | Yes | Yes | -- | Yes |
| Work Schedule | Yes | Yes | Yes | Yes | Yes |
| Work Performance | Yes | Yes | Yes | -- | Yes |
| Work Capability | Yes | Yes | -- | -- | Yes |
| Work Alert | Yes | Yes | -- | -- | Yes |
| Work Calendar | Yes | Yes | Yes | -- | Yes |

**Key observations:**
- All nouns support Get/Show (read access) and Process/Acknowledge (create/update)
- Schedules and resource nouns support Cancel (work can be cancelled, definitions generally cannot)
- All nouns support Publish (any object change can be broadcast)
- Capability nouns are read-only from the business view (capability is reported, not commanded)

---

## Transaction Profiles

*Reference: ISA-95 Part 5, Clause 10*

A transaction profile is a declaration of which verb-noun combinations a system supports. Transaction profiles enable conformance testing and interoperability assessment -- two systems can compare their profiles to determine what data they can exchange and how.

### Profile Components

| Component | Description |
|-----------|-------------|
| **Supported nouns** | Which business objects the system can handle |
| **Supported verbs per noun** | Which actions the system can perform on each object |
| **Transaction model** | Whether each combination uses PULL, PUSH, or PUBLISH |
| **Role** | Whether the system acts as requester, responder, publisher, or subscriber for each combination |

### Profile Example

A Fuuz deployment might declare a transaction profile such as:

| Noun | Verb | Model | Role |
|------|------|-------|------|
| Operations Schedule | Process | PUSH | Responder (receives schedules from ERP) |
| Operations Schedule | Get/Show | PULL | Responder (ERP can query current schedules) |
| Operations Performance | Process | PUSH | Initiator (pushes performance to ERP) |
| Operations Performance | Get/Show | PULL | Responder (ERP can query performance) |
| Equipment | Publish | PUBLISH | Publisher (broadcasts equipment status) |
| Material Lot | Get/Show | PULL | Responder (ERP can query lot data) |
| Work Alert | Publish | PUBLISH | Publisher (broadcasts alerts) |

**Fuuz Implementation:**
- Each Fuuz deployment has an effective transaction profile determined by its configured integrations
- The GraphQL API schema defines which nouns are available and what operations (verbs) can be performed
- MQTT topic subscriptions and publications define the PUBLISH profile
- Webhook configurations define the PUSH profile
- Transaction profiles are useful for documenting and validating ERP integration scope

---

## Fuuz Transaction Pattern Implementation

### How Fuuz Implements Each Transaction Model

| ISA-95 Model | Fuuz Technology | Implementation Details |
|--------------|----------------|----------------------|
| **PULL** | GraphQL API | Queries (Get/Show), Mutations (Process/Acknowledge, Change/Respond, Cancel). Typed schema provides self-documenting API. |
| **PUSH** | Webhooks + Integration Flows | Fuuz Flows trigger on data events and push to external systems via HTTP. External systems push to Fuuz via GraphQL mutations. |
| **PUBLISH** | MQTT (UNS) | Equipment telemetry, status changes, and operational events are published to hierarchical MQTT topics. Any system can subscribe. |

### Common Integration Patterns

**ERP Schedule Import (PUSH, inbound):**
```
ERP System                    Fuuz Platform
    |                              |
    |-- POST work order ---------->|  (GraphQL mutation)
    |   (Process verb)             |
    |<-- Success/Error response ---|  (Acknowledge verb)
    |                              |
    |                              |-- Publish to MQTT topic -->  (Notify subscribers)
```

**Production Performance Export (PUSH, outbound):**
```
Fuuz Platform                 ERP System
    |                              |
    |  (Job complete event)        |
    |-- POST production log ------>|  (Process verb, via webhook/API)
    |                              |
    |<-- Acknowledgment -----------|  (Acknowledge verb)
```

**Equipment Status Broadcast (PUBLISH):**
```
Equipment Gateway             MQTT Broker              Multiple Subscribers
    |                              |                         |
    |-- Publish status ----------->|                         |
    |   (to UNS topic)            |-- Deliver to ---------->| Fuuz Dashboards
    |                              |   all subscribers       | Fuuz Flows
    |                              |                         | Historian
    |                              |                         | External Analytics
```

**On-Demand Data Query (PULL):**
```
External System               Fuuz Platform
    |                              |
    |-- GraphQL Query ------------>|  (Get verb)
    |   (e.g., current inventory)  |
    |<-- GraphQL Response ---------|  (Show verb)
    |   (JSON data)                |
```

---

## Summary: Part 5 Transaction Models and Fuuz Mapping

| ISA-95 Part 5 Concept | Description | Fuuz Implementation |
|-----------------------|-------------|---------------------|
| PULL Model | Synchronous request/response | GraphQL Queries and Mutations |
| PUSH Model | Asynchronous unsolicited messaging | Webhooks, Integration Flows, API callbacks |
| PUBLISH Model | Broadcast pub/sub messaging | MQTT with UNS topic hierarchy |
| 13 Standard Verbs | Get, Show, Process, Acknowledge, Change, Respond, Cancel, Sync, Confirm, Create, Close, Notify, Publish | Mapped to GraphQL operations, MQTT messages, and webhook events |
| Nouns | All Part 2-4 business objects | Fuuz data models exposed via GraphQL schema |
| Verb-Noun Matrix | Valid combinations of actions and objects | Defined by GraphQL schema (which types support which operations) |
| Transaction Profiles | Declaration of supported transactions for conformance | Documented per deployment based on configured integrations |
| B2MML | XML serialization of ISA-95 transactions | Transformation Flows for B2MML interoperability when needed |
