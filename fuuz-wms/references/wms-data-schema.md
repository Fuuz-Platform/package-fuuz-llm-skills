# Fuuz WMS Data Schema Documentation

> Auto-generated from Fuuz MCP API - `data_flow_data_model_to_graph`

## Summary

| Metric | Value |
|--------|-------|
| **Total Models** | 37 |
| **Total Relationships** | 124 |
| **Total Elements (Fields)** | 556 |
| **System Models** | 1 |
| **Custom Models** | 36 |

## Modules

The Fuuz WMS system is organized into 7 functional modules:

1. **Inventory Tracking** - Real-time monitoring of stock levels and item locations
2. **Order Processing** - Management of customer/supplier orders and fulfillment
3. **Cycle Counting** - Inventory verification and adjustment processes
4. **Logistics** - Transportation and delivery management
5. **Site Management** - Physical warehouse layout and storage configuration
6. **Order Receiving** - Inbound receipt and inspection workflows
7. **Testing** - Sensor measurement and equipment monitoring

---

## Data Models by Module

### Inventory Tracking

#### Adjustment
Reason codes for inventory quantity adjustments.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `name` | String! | unique |
| `externalId` | String | unique |
| `countLineInventoryUse` | Boolean! | |
| `tolerance` | Float! | |
| `system` | Boolean! | |
| `usable` | Boolean! | |
| `customData` | JSON | |
| `integrationData` | JSON | |
| `lastInventories` | [Inventory!]! | relationship |

#### Area
Physical, geographical, or logical grouping within a site.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `code` | String! | unique |
| `externalId` | String | unique |
| `name` | String | |
| `description` | String | |
| `active` | Boolean! | |
| `process` | Boolean! | |
| `storage` | Boolean! | |
| `productionCell` | Boolean! | |
| `productionLine` | Boolean! | |
| `customData` | JSON | |
| `integrationData` | JSON | |
| `countParameters` | [CountParameters!]! | relationship |
| `storageZones` | [StorageZone!]! | relationship |

#### HandlingUnit
Physical unit consisting of packaging materials (pallets, boxes, containers) used for storage/transport. Follows GS1 standards.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `serialNumber` | String! | unique |
| `barcode` | String | |
| `gln` | String | GLN identifier |
| `gtin` | String | GTIN identifier |
| `sscc` | String | SSCC code |
| `active` | Boolean! | |
| `mixed` | Boolean! | |
| `level` | Int! | |
| `height` | Measure | |
| `width` | Measure | |
| `length` | Measure | |
| `weight` | Measure | |
| `volume` | Measure | |
| `note` | String | |
| `externalId` | String | |
| `customData` | JSON | |
| `integrationData` | JSON | |
| `packagingConfigurationId` | ID | |
| `parentHandlingUnit` | HandlingUnit | relationship |
| `parentHandlingUnitId` | ID | |
| `childHandlingUnits` | [HandlingUnit!]! | relationship |
| `inventories` | [Inventory!]! | relationship |

#### Inventory
Core inventory model. **Data changes tracked for 25 years**.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `serialNumber` | String! | unique |
| `externalId` | String | unique |
| `barcode` | String | |
| `active` | Boolean! | |
| `shippable` | Boolean! | |
| `quantity` | Measure! | required |
| `grossWeight` | Measure | |
| `netWeight` | Measure | |
| `manufacturedAt` | DateTime | |
| `expiresAt` | DateTime | |
| `lastCountAt` | DateTime | |
| `trackingNumber` | String | |
| `workOrderId` | String | |
| `extProcessId` | String | |
| `extProductId` | String | |
| `note` | String | |
| `customData` | JSON | |
| `integrationData` | JSON | |
| `product` | Product | relationship |
| `productId` | ID | |
| `lot` | Lot | relationship |
| `lotId` | ID | |
| `handlingUnit` | HandlingUnit | relationship |
| `handlingUnitId` | ID | |
| `storageUnit` | StorageUnit | relationship |
| `storageUnitId` | ID | |
| `inventoryStatus` | InventoryStatus! | relationship |
| `inventoryStatusId` | ID! | |
| `process` | Process | relationship |
| `processId` | ID | |
| `receiptLine` | ReceiptLine | relationship |
| `receiptLineId` | ID | |
| `lastAdjustment` | Adjustment | relationship |
| `lastAdjustmentId` | ID | |
| `lastTransactionType` | TransactionType! | relationship |
| `lastTransactionTypeId` | ID | |
| `countLineInventories` | [CountLineInventory!]! | relationship |
| `sourceInventoryTraces` | [InventoryTrace!]! | relationship |
| `destinationInventoryTraces` | [InventoryTrace!]! | relationship |

#### InventoryStatus
Controls the lifecycle state of all inventory.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `code` | String! | unique |
| `externalId` | String | |
| `usable` | Boolean! | |
| `system` | Boolean! | |
| `inventoryUse` | Boolean! | |
| `locationUse` | Boolean! | |
| `lotUse` | Boolean! | |
| `customData` | JSON | |
| `integrationData` | JSON | |
| `inventories` | [Inventory!]! | relationship |
| `lots` | [Lot!]! | relationship |

#### InventoryTrace
Tracks inventory transactions (depletions, splits, merges). **Data tracked for traceability**.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `externalId` | String | unique |
| `timestamp` | DateTime! | required |
| `sourceQuantitySubtracted` | Measure! | |
| `destinationQuantityAdded` | Measure! | |
| `customData` | JSON | |
| `integrationData` | JSON | |
| `sourceInventory` | Inventory! | relationship |
| `sourceInventoryId` | ID! | |
| `destinationInventory` | Inventory! | relationship |
| `destinationInventoryId` | ID! | |

#### LabelDesign
Stores legacy label designs (ZPL, IPL, etc.) for printing.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `code` | String! | unique |
| `name` | String! | unique |
| `active` | Boolean! | |
| `description` | String | |
| `format` | String | |
| `dpmm` | Int! | dots per mm |
| `width` | Measure | |
| `length` | Measure | |
| `revision` | String! | |
| `variableData` | String | |
| `lastUpdatedAt` | DateTime | |
| `customData` | JSON | |
| `integrationData` | JSON | |
| `image` | Image | relationship |
| `imageId` | ID | |

#### Lot
Logical groupings of inventory from the same batch/heat/production process.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `number` | String! | unique |
| `supplierLotId` | String | unique |
| `externalId` | String | unique |
| `manufacturedAt` | DateTime | |
| `receivedAt` | DateTime | |
| `releasedAt` | DateTime | |
| `expiresAt` | DateTime | |
| `bestBeforeAt` | DateTime | |
| `countryOfOrigin` | String | |
| `manufacturerId` | String | |
| `productionLineId` | String | |
| `hazardClassification` | String | |
| `storageRequirements` | String | |
| `qcHoldReason` | String | |
| `customData` | JSON | |
| `integrationData` | JSON | |
| `product` | Product! | relationship |
| `productId` | ID! | |
| `inventoryStatus` | InventoryStatus! | relationship |
| `inventoryStatusId` | ID! | |
| `masterLot` | Lot | relationship |
| `masterLotId` | ID | |
| `subLots` | [Lot!]! | relationship |
| `inventories` | [Inventory!]! | relationship |
| `receiptLines` | [ReceiptLine!]! | relationship |

#### Process
Process codes defining inventory stage.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `code` | String! | unique |
| `description` | String | |
| `system` | Boolean! | |
| `usable` | Boolean! | |
| `customData` | JSON | |
| `inventories` | [Inventory!]! | relationship |

#### Product
Common product/item/SKU data model used across Fuuz apps.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `code` | String! | unique |
| `gtin` | String | unique |
| `sku` | String | unique |
| `externalId` | String | unique |
| `name` | String | |
| `number` | String | |
| `description` | String | |
| `revision` | String | |
| `upc` | String | |
| `active` | Boolean! | |
| `forceHold` | Boolean! | |
| `shelfLifeDays` | Int! | |
| `height` | Measure! | |
| `width` | Measure | |
| `length` | Measure! | |
| `weight` | Measure! | |
| `customData` | JSON | |
| `integrationData` | JSON | |
| `defaultQuantityUnit` | Unit! | relationship |
| `defaultQuantityUnitId` | ID! | |
| `productCategory` | ProductCategory | relationship |
| `productCategoryId` | ID | |
| `barcodeDesign` | DocumentDesign | relationship |
| `barcodeDesignId` | ID | |
| `inventories` | [Inventory!]! | relationship |
| `lots` | [Lot!]! | relationship |
| `orderLines` | [OrderLine!]! | relationship |
| `countLines` | [CountLine!]! | relationship |
| `countParameters` | [CountParameters!]! | relationship |
| `receiptLines` | [ReceiptLine!]! | relationship |
| `productPreferredStorageUnits` | [ProductPreferredStorageUnit!]! | relationship |

#### ProductCategory
Grouping for products.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `code` | String! | unique |
| `externalId` | String | unique |
| `description` | String | |
| `usable` | Boolean! | |
| `customData` | JSON | |
| `integrationData` | JSON | |
| `products` | [Product!]! | relationship |

#### TransactionType
Identifies types of inventory transactions.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `name` | String! | unique |
| `externalId` | String | unique |
| `description` | String | |
| `usable` | Boolean! | |
| `customData` | JSON | |
| `integrationData` | JSON | |
| `lastInventories` | [Inventory!]! | relationship |

---

### Order Processing

#### BusinessPartner
Customer or supplier entity.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `code` | String! | unique |
| `externalId` | String | unique |
| `name` | String | |
| `active` | Boolean! | |
| `customer` | Boolean! | |
| `supplier` | Boolean! | |
| `allowTransfer` | Boolean! | |
| `creditLimit` | Float | |
| `dunsNumber` | String | |
| `taxId` | String | |
| `paymentTerms` | String | |
| `firmHorizon` | Duration | |
| `forecastHorizon` | Duration | |
| `plannedHorizon` | Duration | |
| `internalNote` | String | |
| `packagingNote` | String | |
| `shippingNote` | String | |
| `customData` | JSON | |
| `integrationData` | JSON | |
| `responsibleUser` | User | relationship |
| `responsibleUserId` | ID | |
| `businessPartnerAddresses` | [BusinessPartnerAddress!]! | relationship |
| `orders` | [Order!]! | relationship |
| `destinationOrders` | [Order!]! | relationship |

#### BusinessPartnerAddress
Address locations for business partners with optional packaging/shipping notes.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `code` | String! | unique |
| `externalId` | String | unique |
| `textAddress` | String! | |
| `address` | Address | |
| `partnerLocationId` | String | |
| `active` | Boolean! | |
| `internalNote` | String | |
| `packagingNote` | String | |
| `shippingNote` | String | |
| `customData` | JSON | |
| `integrationData` | JSON | |
| `businessPartner` | BusinessPartner! | relationship |
| `businessPartnerId` | ID! | |
| `orders` | [Order!]! | relationship |
| `orderLines` | [OrderLine!]! | relationship |
| `destinationOrders` | [Order!]! | relationship |
| `destinationOrderLines` | [OrderLine!]! | relationship |

#### Order
Order header. **Data changes tracked for 90 days**.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `number` | String! | unique |
| `externalId` | String | unique |
| `extStatus` | String | |
| `active` | Boolean! | |
| `customData` | JSON | |
| `integrationData` | JSON | |
| `businessPartner` | BusinessPartner | relationship |
| `businessPartnerId` | ID | |
| `businessPartnerAddress` | BusinessPartnerAddress | relationship |
| `businessPartnerAddressId` | ID | |
| `destinationBusinessPartner` | BusinessPartner | relationship |
| `destinationBusinessPartnerId` | ID | |
| `destinationBusinessPartnerAddress` | BusinessPartnerAddress | relationship |
| `destinationBusinessPartnerAddressId` | ID | |
| `orderType` | OrderType | relationship |
| `orderTypeId` | ID! | |
| `orderStatus` | OrderStatus | relationship |
| `orderStatusId` | ID! | |
| `orderLines` | [OrderLine!]! | relationship |
| `receipts` | [Receipt!]! | relationship |

#### OrderLine
Individual line items on an order.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `number` | String! | |
| `externalId` | String | |
| `unitPrice` | Float! | |
| `active` | Boolean! | |
| `customData` | JSON | |
| `integrationData` | JSON | |
| `order` | Order! | relationship |
| `orderId` | ID! | |
| `product` | Product! | relationship |
| `productId` | ID! | |
| `businessPartnerAddress` | BusinessPartnerAddress | relationship |
| `businessPartnerAddressId` | ID | |
| `destinationBusinessPartnerAddress` | BusinessPartnerAddress | relationship |
| `destinationBusinessPartnerAddressId` | ID | |
| `orderLineReleases` | [OrderLineRelease!]! | relationship |
| `receiptLines` | [ReceiptLine!]! | relationship |

#### OrderLineRelease
Individual ship/firm/plan schedules for order lines.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `number` | String! | |
| `externalId` | String | |
| `dueAt` | Date! | required |
| `shipAt` | Date! | required |
| `quantity` | Measure! | |
| `quantityLeft` | Measure! | |
| `allowOverage` | Boolean! | |
| `allowPartial` | Boolean! | |
| `associatedBarcode` | String | |
| `active` | Boolean! | |
| `actualFulfilmentAt` | Date | |
| `customData` | JSON | |
| `integrationData` | JSON | |
| `orderLine` | OrderLine! | relationship |
| `orderLineId` | ID! | |
| `orderLineReleaseStatus` | OrderLineReleaseStatus! | relationship |
| `orderLineReleaseStatusId` | ID! | |
| `orderLineReleaseType` | OrderLineReleaseType | relationship |
| `orderLineReleaseTypeId` | ID | |
| `receiptLineOrderLineReleases` | [ReceiptLineOrderLineRelease!]! | relationship |

#### OrderLineReleaseStatus
Status codes for order line releases. System-marked statuses should not be modified.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `code` | String! | unique |
| `description` | String | |
| `system` | Boolean! | |
| `usable` | Boolean! | |
| `customData` | JSON | |
| `integrationData` | JSON | |
| `orderLineReleases` | [OrderLineRelease!]! | relationship |

#### OrderStatus
Status codes for orders. System-marked statuses should not be modified.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `code` | String! | unique |
| `description` | String | |
| `system` | Boolean! | |
| `usable` | Boolean! | |
| `orderUse` | Boolean! | |
| `orderLineReleaseUse` | Boolean! | |
| `customData` | JSON | |
| `integrationData` | JSON | |
| `orders` | [Order!]! | relationship |
| `orderLineReleases` | [OrderLineRelease!]! | relationship |

#### OrderType
Classification of order types.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `name` | String! | |
| `usable` | Boolean! | |
| `orders` | [Order!]! | relationship |

---

### Cycle Counting

#### Count
Cycle count header for optimizing inventory verification.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `number` | Int! | unique |
| `externalId` | String | |
| `blind` | Boolean! | |
| `scheduledStartAt` | DateTime | |
| `actualStartAt` | DateTime | |
| `completedAt` | DateTime | |
| `customData` | JSON | |
| `integrationData` | JSON | |
| `countStatus` | CountStatus | relationship |
| `countStatusId` | ID | |
| `countLines` | [CountLine!]! | relationship |
| `countParameters` | [CountParameters!]! | relationship |

#### CountLine
Aggregated expectations for count based on parameters.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `number` | Int! | |
| `expectedQuantity` | Float! | |
| `expectedWeight` | Float! | |
| `approvedAt` | DateTime | |
| `count` | Count! | relationship |
| `countId` | ID! | |
| `product` | Product! | relationship |
| `productId` | ID! | |
| `expectedStorageUnit` | StorageUnit | relationship |
| `expectedStorageUnitId` | ID | |
| `countLineInventories` | [CountLineInventory!]! | relationship |

#### CountLineInventory
Individual inventory items within a count line.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `quantity` | Measure! | |
| `weight` | Measure! | |
| `note` | String | |
| `adjustedAt` | DateTime | |
| `countLine` | CountLine! | relationship |
| `countLineId` | ID! | |
| `inventory` | Inventory! | relationship |
| `inventoryId` | ID! | |
| `storageUnit` | StorageUnit | relationship |
| `storageUnitId` | ID | |
| `counterUser` | User! | relationship |
| `counterUserId` | ID | |
| `adjustingUser` | User | relationship |
| `adjustingUserId` | ID | |
| `adjustment` | Adjustment | relationship |
| `adjustmentId` | ID | |

#### CountParameters
Filter criteria used when starting a count.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `number` | Int! | unique |
| `createdFromAt` | Date | |
| `createdToAt` | Date | |
| `updatedFromAt` | Date | |
| `updatedToAt` | Date | |
| `count` | Count! | relationship |
| `countId` | ID! | |
| `area` | Area | relationship |
| `areaId` | ID | |
| `storageZone` | StorageZone | relationship |
| `storageZoneId` | ID | |
| `storageUnit` | StorageUnit | relationship |
| `storageUnitId` | ID | |
| `product` | Product | relationship |
| `productId` | ID | |
| `productCategory` | ProductCategory | relationship |
| `productCategoryId` | ID | |

#### CountStatus
Status codes for counts.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `name` | String | |
| `description` | String | |
| `active` | Boolean | |
| `usable` | Boolean | |

---

### Logistics

#### OrderLineReleaseType
Types for order line releases.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `name` | String | |
| `description` | String | |
| `active` | Boolean | |
| `usable` | Boolean | |

---

### Site Management

#### ProductPreferredStorageUnit
Defines product-storage unit compatibility with priority for putaway.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `priority` | Int! | |
| `product` | Product! | relationship |
| `productId` | ID! | |
| `storageUnit` | StorageUnit! | relationship |
| `storageUnitId` | ID! | |

#### StorageUnit
Individual storage locations (bins, racks, shelves).

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `code` | String! | unique |
| `externalId` | String | unique |
| `name` | String | |
| `description` | String | |
| `active` | Boolean! | |
| `latitude` | String | |
| `longitude` | String | |
| `altitude` | String | |
| `height` | Measure | |
| `width` | Measure | |
| `length` | Measure | |
| `storage` | Boolean! | |
| `receiving` | Boolean! | |
| `shipping` | Boolean! | |
| `picking` | Boolean! | |
| `packing` | Boolean! | |
| `qualityControl` | Boolean! | |
| `crossDock` | Boolean! | |
| `returns` | Boolean! | |
| `customData` | JSON | |
| `integrationData` | JSON | |
| `storageZone` | StorageZone | relationship |
| `storageZoneId` | ID | |
| `storageUnitStatus` | StorageUnitStatus | relationship |
| `storageUnitStatusId` | ID | |
| `defaultInventoryStatus` | InventoryStatus | relationship |
| `defaultInventoryStatusId` | ID | |
| `inventories` | [Inventory!]! | relationship |
| `receiptLines` | [ReceiptLine!]! | relationship |
| `dockReceipts` | [Receipt!]! | relationship |
| `putAwayReceiptLines` | [ReceiptLine!]! | relationship |
| `countParameters` | [CountParameters!]! | relationship |
| `countLineInventories` | [CountLineInventory!]! | relationship |
| `expectedCountLines` | [CountLine!]! | relationship |
| `products` | [Product!]! | relationship |

#### StorageUnitStatus
Status codes for storage units.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `code` | String | |
| `description` | String | |
| `active` | Boolean | |
| `usable` | Boolean | |

#### StorageZone
Logical groupings of storage units with specific handling characteristics.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `code` | String! | unique |
| `externalId` | String | |
| `description` | String | |
| `usable` | Boolean! | |
| `refrigerated` | Boolean! | |
| `hazardous` | Boolean! | |
| `highValue` | Boolean! | |
| `oversized` | Boolean! | |
| `fastMoving` | Boolean! | |
| `slowMoving` | Boolean! | |
| `overflow` | Boolean! | |
| `crossDocking` | Boolean! | |
| `kitting` | Boolean! | |
| `returnProcessing` | Boolean! | |
| `area` | Area | relationship |
| `areaId` | ID | |
| `storageUnits` | [StorageUnit!]! | relationship |
| `countParameters` | [CountParameters!]! | relationship |

---

### Order Receiving

#### Receipt
Receipt header (one per BOL/PackingSlip/ASN). May have multiple line items.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `number` | String! | unique |
| `asnNumber` | String | unique |
| `externalId` | String | unique |
| `bolNumber` | String | |
| `carrier` | String | |
| `trackingNumber` | String | |
| `truckNumber` | String | |
| `note` | String | |
| `receivedAt` | DateTime | |
| `order` | Order | relationship |
| `orderId` | ID | |
| `receiptStatus` | ReceiptStatus! | relationship |
| `receiptStatusId` | ID! | |
| `dockStorageUnit` | StorageUnit | relationship |
| `dockStorageUnitId` | ID | |
| `receiptLines` | [ReceiptLine!]! | relationship |

#### ReceiptException
Report on received/expected inventory that differs from ordered.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `number` | String! | unique |
| `externalId` | String | unique |
| `description` | String | |
| `resolutionNotes` | String | |
| `resolvedAt` | DateTime | |
| `active` | Boolean! | |
| `customData` | JSON | |
| `integrationData` | JSON | |
| `receiptLine` | ReceiptLine! | relationship |
| `receiptLineId` | ID! | |
| `receiptExceptionReason` | ReceiptExceptionReason | relationship |
| `receiptExceptionReasonId` | ID | |
| `resolvedByUser` | User | relationship |
| `resolvedByUserId` | ID | |

#### ReceiptExceptionReason
Reason codes for receipt exceptions.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `name` | String | unique |
| `usable` | Boolean | |
| `receiptExceptions` | [ReceiptException!]! | relationship |

#### ReceiptLine
Specific products being received for a given receipt.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `number` | String! | |
| `externalId` | String | |
| `quantity` | Measure! | |
| `numberOfInventory` | Int! | |
| `addToHandlingUnit` | Boolean! | |
| `note` | String | |
| `receipt` | Receipt! | relationship |
| `receiptId` | ID! | |
| `product` | Product! | relationship |
| `productId` | ID! | |
| `receiptStatus` | ReceiptStatus! | relationship |
| `receiptStatusId` | ID! | |
| `orderLine` | OrderLine | relationship |
| `orderLineId` | ID | |
| `lot` | Lot | relationship |
| `lotId` | ID | |
| `storageUnit` | StorageUnit | relationship |
| `storageUnitId` | ID | |
| `putAwayStorageUnit` | StorageUnit | relationship |
| `putAwayStorageUnitId` | ID | |
| `receiptException` | ReceiptException | relationship |
| `inventories` | [Inventory!]! | relationship |
| `receiptLineOrderLineReleases` | [ReceiptLineOrderLineRelease!]! | relationship |

#### ReceiptLineOrderLineRelease
Junction table linking receipt lines to order line releases.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `receiptLine` | ReceiptLine! | relationship |
| `receiptLineId` | ID! | |
| `orderLineRelease` | OrderLineRelease! | relationship |
| `orderLineReleaseId` | ID! | |

#### ReceiptStatus
Status codes for receipts and receipt lines.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `code` | String! | unique |
| `externalId` | String | unique |
| `description` | String | |
| `system` | Boolean! | |
| `usable` | Boolean! | |
| `receiptUse` | Boolean! | |
| `receiptLineUse` | Boolean! | |
| `receiptExceptionUse` | Boolean! | |
| `customData` | JSON | |
| `integrationData` | JSON | |
| `receipts` | [Receipt!]! | relationship |
| `receiptLines` | [ReceiptLine!]! | relationship |
| `receiptExceptions` | [ReceiptException!]! | relationship |

---

### Testing

#### SensorMeasurement
Equipment sensor readings with fault detection.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `equipmentId` | String! | required |
| `sensorId` | String! | required |
| `value` | Float! | required |
| `occurredAt` | DateTime! | required |
| `fault` | Boolean! | |
| `faultReason` | String | |
| `lowerBound` | Float | |
| `upperBound` | Float | |

---

## Entity Relationship Summary

### Key Relationships

```
Area
  └── StorageZone (1:N)
        └── StorageUnit (1:N)
              └── Inventory (1:N)

Product
  ├── ProductCategory (N:1)
  ├── Lot (1:N)
  ├── Inventory (1:N)
  └── OrderLine (1:N)

Order
  ├── BusinessPartner (N:1)
  ├── OrderLine (1:N)
  │     └── OrderLineRelease (1:N)
  └── Receipt (1:N)
        └── ReceiptLine (1:N)
              └── Inventory (1:N)

Count
  ├── CountParameters (1:N)
  └── CountLine (1:N)
        └── CountLineInventory (1:N)

HandlingUnit
  ├── HandlingUnit (self-referencing parent/child)
  └── Inventory (1:N)
```

### Data Retention Notes

| Model | Retention Period |
|-------|------------------|
| Inventory | 25 years (full audit trail) |
| InventoryTrace | Long-term (traceability) |
| Order | 90 days (change tracking) |

---

## Common Field Patterns

### Standard Identifiers
- `id`: Internal unique identifier (ID!)
- `code`: Business-readable unique code (String!)
- `externalId`: External system identifier (String, unique)
- `number`: Sequence number (Int! or String!)

### Extension Fields
- `customData`: JSON for custom fields
- `integrationData`: JSON for integration metadata

### Status Flags
- `active`: Whether record is active
- `usable`: Whether record can be used in operations
- `system`: Whether record is system-managed (do not modify)

### Measurement Types
- `Measure`: Quantity with unit (e.g., `{ value: 10, unit: "kg" }`)
- `Address`: Structured address object
- `Duration`: Time period
