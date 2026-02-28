| Property | Value |
| --- | --- |
| data | _object_ |

### data
| Property | Value |
| --- | --- |
| modelDefinitions | _complex array_ |

#### data.modelDefinitions
| # | description| fields| name|
| --- | --- | --- | --- |
| 1 | The AccessControlPolicy model represents policy defining access to Fuuz resources. | _complex_ | AccessControlPolicy |
| 2 | The AccessControlPolicyGroup model represents a group of access control policies, collectively controlling access to Fuuz resources. | _complex_ | AccessControlPolicyGroup |
| 3 | The AccessControlPolicyGroupAccessControlPolicy model represents a link between AccessControlPolicies and AccessControlPolicyGroups, defining which policies are members of which groups. | _complex_ | AccessControlPolicyGroupAccessControlPolicy |
| 4 | This model represnts an address that can be embedded into other models | _complex_ | Address |
| 5 | The ApplicationComponentDataFlowReference type includes metadata about a data flow that is referenced in an application. | _complex_ | ApplicationComponentDataFlowReference |
| 6 | The ApplicationComponentDataModelReference type includes metadata about a data model that is referenced in an application. | _complex_ | ApplicationComponentDataModelReference |
| 7 | The ApplicationComponentElementReference type includes metadata about an application component element that is referenced in an application. | _complex_ | ApplicationComponentElementReference |
| 8 | Normalized metadata about a relationship between a two app components or elements. | _complex_ | ApplicationComponentReference |
| 9 | The ApplicationComponentScreenReference type includes metadata about a screen that is referenced in an application. | _complex_ | ApplicationComponentScreenReference |
| 10 | The ApplicationConfiguration model represents the types of components which make up a Fuuz application. | _complex_ | ApplicationComponentType |
| 11 | The ApplicationConfiguration model represents configuration values for a Fuuz application. | _complex_ | ApplicationConfiguration |
| 12 | This model represents the metadata associated with a record. If the record was installed or updated by an Application or Package that will be referenced here. | _complex_ | ApplicationMetadata |
| 13 | The Application Span Event Log Event model will collect information about what ran a mutation. The specific Application Span Event Log Id can be found on any Application models through the _trace field. This will only capture trace information for span events that run a mutation. | _complex_ | ApplicationSpanEventLog |
| 14 | This model represents the trace fields captured through Data Flow, Screens, Gateways, Packages, etc. | _complex_ | ApplicationTraceMetadata |
| 15 | This model represents the trace fields captured through Data Flow, Screens, Gateways, Packages, etc. | _complex_ | ApplicationTraceMetadataDataChange |
| 16 | A Fuuz Calendar. | _complex_ | Calendar |
| 17 | A Fuuz Calendar Event. | _complex_ | CalendarEvent |
| 18 | A grouping of Fuuz Calendar Events. | _complex_ | CalendarEventGroup |
| 19 | An association between a Fuuz Calendar Event and a Grouping. | _complex_ | CalendarEventGroupCalendarEvent |
| 20 | An association between Fuuz Calendar Events and Fuuz Calendar Resources. | _complex_ | CalendarEventResource |
| 21 | An Fuuz Calendar Event Type. | _complex_ | CalendarEventType |
| 22 | A Fuuz Calendar Resource. | _complex_ | CalendarResource |
| 23 | A type of Fuuz calendar. | _complex_ | CalendarType |
| 24 | The Channel model represents a grouping of tags. | _complex_ | Channel |
| 25 | The ChannelStatus model represents a status that a Channel can be in. | _complex_ | ChannelStatus |
| 26 | The Connection model represents a Connector that has a set of options and credentials provided to it for connecting to another system. | _complex_ | Connection |
| 27 | The Connector model represents an application or protocol that Fuuz can connect to. | _complex_ | Connector |
| 28 | The DataFlow model represents an Fuuz data flow, used to design event flows, integrations, or workflows. | _complex_ | DataFlow |
| 29 | The DataFlowDeployment model represents a deployment of a version of an Fuuz data flow, which indicates that the flow should be run in the engine. | _complex_ | DataFlowDeployment |
| 30 | The DataFlowDeploymentLog model represents a log tied to a deployment of an Fuuz data flow.  Logs could be errors that occurred during flow execution, or data explicitly logged via the Log node. | _complex_ | DataFlowDeploymentLog |
| 31 | The DataFlowDeploymentLogType model represents the type of an Fuuz data flow log. | _complex_ | DataFlowDeploymentLogType |
| 32 | The content of a Node in a Data Flow. | _complex_ | DataFlowElement |
| 33 | This model represnts the debug configuration of a data flow node. | _complex_ | DataFlowElementDebug |
| 34 | This model represents the logging configuration of a data flow node. | _complex_ | DataFlowElementLogging |
| 35 | The DataFlowEnvironment model represents the environment where an Fuuz data flow runs. | _complex_ | DataFlowEnvironment |
| 36 | The DataFlowExecutionMetric collection stores node-level execution metrics such as node execution time for reporting purposes. | _complex_ | DataFlowExecutionMetric |
| 37 | The DataFlowMCPToolConfiguration type includes the configuration information necessary to expose a data flow as a tool in the MCP server. | _complex_ | DataFlowMCPToolConfiguration |
| 38 | Data Flow Schedule is a header record which links to a Data Flow and it's Scheduled Frequencies.  | _complex_ | DataFlowSchedule |
| 39 | Data Flow Schedule Frequency defines the frequency in which the configured payload will be sent to the Data Flow defined by the Data Flow Schedule it is linked too. | _complex_ | DataFlowScheduleFrequency |
| 40 | The DataFlowType model represents the type of an Fuuz data flow. | _complex_ | DataFlowType |
| 41 | The DataFlowVersion model represents a version of an Fuuz data flow, with a unique number and associated flow design. | _complex_ | DataFlowVersion |
| 42 | The DataMapping model represents an Fuuz data mapping, used to provide visually-editable transformations. | _complex_ | DataMapping |
| 43 | The DataMappingDeployment model represents a deployment of a version of an Fuuz Data Mapping. | _complex_ | DataMappingDeployment |
| 44 | The DataMappingTestCase model represents a saved data mapping test case. | _complex_ | DataMappingTestCase |
| 45 | The DataMappingVersion model represents a version of a saved data mapping. | _complex_ | DataMappingVersion |
| 46 | The DataModel model represents an Fuuz Data Model definition. | _complex_ | DataModel |
| 47 | The DataModelCustomField model represents a custom field on an Fuuz data model. | _complex_ | DataModelCustomField |
| 48 | The DataModelCustomFieldType model represents the type of an Fuuz data model custom field. | _complex_ | DataModelCustomFieldType |
| 49 | The DataModelDeployment model represents a deployment of a version of an Fuuz data model. | _complex_ | DataModelDeployment |
| 50 | The DataModelDeploymentLog model represents a log tied to a deployment of an Fuuz Data Model. | _complex_ | DataModelDeploymentLog |
| 51 | The DataModelDeploymentLogType model represents the type of an Fuuz data model deployment log. | _complex_ | DataModelDeploymentLogType |
| 52 | The DataModelDeploymentStatus model represents the status of an Fuuz data model deployment. | _complex_ | DataModelDeploymentStatus |
| 53 | The content of a Field in a Data Model. | _complex_ | DataModelElement |
| 54 | This model represents the relation metadata in a data model field. | _complex_ | DataModelElementRelationFields |
| 55 | This model represents the parsed type info for a data model field. | _complex_ | DataModelElementTypeInfo |
| 56 | The DataModelKind model represents the kinds of data models that Fuuz supports. | _complex_ | DataModelKind |
| 57 | The DataModelType model represents the type of an Fuuz data model based on its use case | _complex_ | DataModelType |
| 58 | The DataModelVersion model represents a version of an Fuuz Data Model. | _complex_ | DataModelVersion |
| 59 |  | _complex_ | Device |
| 60 |  | _complex_ | DeviceDriver |
| 61 |  | _complex_ | DeviceDriverType |
| 62 |  | _complex_ | DeviceDriverTypeDeviceSubscriptionType |
| 63 |  | _complex_ | DeviceDriverTypeFunction |
| 64 |  | _complex_ | DeviceGateway |
| 65 |  | _complex_ | DeviceGatewayDataFlow |
| 66 |  | _complex_ | DeviceGatewayDevice |
| 67 |  | _complex_ | DeviceGatewayFunction |
| 68 |  | _complex_ | DeviceSubscription |
| 69 |  | _complex_ | DeviceSubscriptionType |
| 70 | The DocumentDesign model represents the design of a printed document in Fuuz, such as a label or invoice. | _complex_ | DocumentDesign |
| 71 | The DocumentDesignDeployment model represents a deployment of a version of an Fuuz document design. | _complex_ | DocumentDesignDeployment |
| 72 | The DocumentDesignPayload model represents a saved sample payload input to a document design. | _complex_ | DocumentDesignPayload |
| 73 | The DocumentDesignVersion model represents a version of an Fuuz document design. | _complex_ | DocumentDesignVersion |
| 74 | The DocumentRender model represents a rendered instance of a document design. | _complex_ | DocumentRender |
| 75 | The DocumentRenderFormat model represents the output type of a rendered document. | _complex_ | DocumentRenderFormat |
| 76 | The export configuration model repesents the configurations for exports. | _complex_ | ExportConfiguration |
| 77 | The export format model represents the formats for exports. | _complex_ | ExportFormat |
| 78 | The export history model represents the export operation. | _complex_ | ExportHistory |
| 79 | This model represents the file info for an export history record. | _complex_ | ExportHistoryFile |
| 80 | The export status model represents the status of the exports. | _complex_ | ExportStatus |
| 81 | An external application to be used for configuration with Fuuz. | _complex_ | ExternalApplication |
| 82 | A configuration to be used for an external application with Fuuz. | _complex_ | ExternalApplicationConfiguration |
| 83 | The File type represents a file stored within Fuuz. | _complex_ | File |
| 84 | The Image type represents an image stored within Fuuz | _complex_ | Image |
| 85 | The InstalledPackage model represents an installed Fuuz package. | _complex_ | InstalledPackage |
| 86 | The InstalledPackageDependency model represents one InstalledPackage's dependency on another. | _complex_ | InstalledPackageDependency |
| 87 |  | _complex_ | IntegrationRequest |
| 88 | The IntegrationRequestLog model represents a log tied to a request made to the fuuz integration service. | _complex_ | IntegrationRequestLog |
| 89 | The Integration Request Status model holds all possible statuses of Integration Requests. | _complex_ | IntegrationRequestStatus |
| 90 | The Measure model represents a value with a unit of measurement, such as a quantity. | _complex_ | Measure |
| 91 | The Module type represents a grouping of related functionality in Fuuz. | _complex_ | Module |
| 92 | The ModuleGroup type represents a module group, grouping together related modules in Fuuz. | _complex_ | ModuleGroup |
| 93 |  | _complex_ | NotificationChannel |
| 94 |  | _complex_ | NotificationChannelRecipient |
| 95 |  | _complex_ | NotificationCommunication |
| 96 | The PackageBuilderState model represents a package being built with the PackageBuilder tool. | _complex_ | PackageBuilderState |
| 97 | The PackageDefinitionVersion model represents the definition of a Fuuz package. | _complex_ | PackageDefinition |
| 98 | The PackageDefinitionSelection model represents a data selection on the definition of a Fuuz package in the editor. | _complex_ | PackageDefinitionSelection |
| 99 | The PackageDefinitionVersion model represents the definition of a Fuuz package in the editor. | _complex_ | PackageDefinitionVersion |
| 100 | The PackageInstallation model represents the installation process of a Fuuz package. | _complex_ | PackageInstallation |
| 101 | The PackageInstallationLog model represents a log tied to an installation of a Fuuz package. | _complex_ | PackageInstallationLog |
| 102 | The PackageInstallationLogType model represents the type of a Fuuz package installation log. | _complex_ | PackageInstallationLogType |
| 103 | The PackageInstallationStage model represents the stage of a Fuuz package installation. | _complex_ | PackageInstallationStage |
| 104 | The PackageInstallationTask model represents a task performed while installing a package. | _complex_ | PackageInstallationTask |
| 105 | The PackageInstallationTaskStatus model represents the status of a Fuuz package installation task. | _complex_ | PackageInstallationTaskStatus |
| 106 | The PackageInstallationTaskType model represents the type of a Fuuz package installation task. | _complex_ | PackageInstallationTaskType |
| 107 | The RatioMeasure model represents a proportional relationship between two measures. | _complex_ | RatioMeasure |
| 108 |  | _complex_ | Recipient |
| 109 | The Role type represents an access control role that can be applied to types within Fuuz. | _complex_ | Role |
| 110 | This model represents a link between Roles and AccessControlPolicyGroups, defining which groups are assigned of which roles. | _complex_ | RoleAccessControlPolicyGroup |
| 111 | An external application role to be used for configuration with Fuuz. | _complex_ | RoleExternalApplicationConfiguration |
| 112 | The value of a setting for a role. | _complex_ | RoleSettingValue |
| 113 | The Route type represents the URL paths through which a screen is accessible in the Fuuz frontend. | _complex_ | Route |
| 114 | The SavedQuery model represent a saved graphQL statement. | _complex_ | SavedQuery |
| 115 | The SavedQueryOperationType represents the type of operation for a Saved Query within Fuuz. | _complex_ | SavedQueryOperationType |
| 116 | The SavedQueryParameter type represents a parameter that is an input to a Saved Query in Fuuz. | _complex_ | SavedQueryParameter |
| 117 | The SavedTransform model represents a saved Fuuz Scripting Language transform, as well as information about the transform's input and output. | _complex_ | SavedTransform |
| 118 | The SavedTransformDeployment model represents a deployment of a version of an Fuuz Scripting Language transform. | _complex_ | SavedTransformDeployment |
| 119 | The SavedTransformVersion model represents a version of a saved Fuuz Scripting Language transform. | _complex_ | SavedTransformVersion |
| 120 | Defines a collection of ScheduleEvents that provide available time. | _complex_ | Schedule |
| 121 | A ScheduleEvent defines available time for the Schedule it is associated with. | _complex_ | ScheduleEvent |
| 122 | Defines a group of schedules for a use case | _complex_ | ScheduleGroup |
| 123 | Schedule Group Exception Event defines unavailable time for all Schedules associated with the Schedule Group. | _complex_ | ScheduleGroupExceptionEvent |
| 124 | ScheduleType defines different configurations to schedule a Data Flow or process. | _complex_ | ScheduleType |
| 125 | Scheduling Configuration is defined for the SchedulingConfiguration Screen Element as well as the Schedule Engine. The definition defined on a record of this model will provide loading data into the scheduler as well as committing that data back to the database. This configuration will be copied upon creation of a SchedulingConfigurationRun. | _complex_ | SchedulingConfiguration |
| 126 | The Scheduling Configuration Run represents a scheduling attempt on the resources defined by the mappings provided in the Scheduling Configuration. When a Scheduling Configuration Run is created, it will automatically begin the process of scheduling the provided resources by automatically loading them into the Scheduler, processing them and updating the run record with the changes the scheduler made. From there you can commit those changes through the `commitSchedulingConfigurationRun` mutation. | _complex_ | SchedulingConfigurationRun |
| 127 | This model represents the filters that can be provided to the Scheduling Configuration Run | _complex_ | SchedulingConfigurationRunFilter |
| 128 | Defines the current status of a Scheduling Configuration Run. | _complex_ | SchedulingConfigurationRunStatus |
| 129 | Defines the available constraints when using the Scheduling Engine in the Fuuz Platform | _complex_ | SchedulingConstraintType |
| 130 | The Screen type represents the metadata defining a user interface screen in Fuuz. | _complex_ | Screen |
| 131 | The ScreenDeployment model represents a deployment of a version of an Fuuz screen, which indicates that the screen should be rendered when called. | _complex_ | ScreenDeployment |
| 132 | The content of an element in a Screen. | _complex_ | ScreenElement |
| 133 | This model represents the query prop for a screen element. | _complex_ | ScreenElementQuery |
| 134 | The ScreenVersion model represents a version of an Fuuz screen, with a unique number and associated screen design. | _complex_ | ScreenVersion |
| 135 | The ScriptLanguage model represents a language for the Fuuz Scripting system. | _complex_ | ScriptLanguage |
| 136 | The Sequence model represents a sequence of values that can be consumed within Fuuz. | _complex_ | Sequence |
| 137 | The SequenceNumber model represents the current number of a Sequence. | _complex_ | SequenceNumber |
| 138 | A Fuuz setting | _complex_ | Setting |
| 139 | A type that holds the values of all the input types a setting could be. | _complex_ | SettingInputType |
| 140 | A type that holds the values of all the input types a setting could be. | _complex_ | SettingSettingType |
| 141 | A type that holds the values of all the types a setting could be. | _complex_ | SettingType |
| 142 | The Tag model represents a single data point from a connected device. | _complex_ | Tag |
| 143 | The TagValue model represents a collected value for a Tag. | _complex_ | TagValue |
| 144 | The TagQuality model represents a way of categorizing Tag Values by the quality of the data. | _complex_ | TagValueQuality |
| 145 | The Topic model represents a topic for routing messages. | _complex_ | Topic |
| 146 | The Unit type represents a unit of measure within Fuuz. | _complex_ | Unit |
| 147 | The UnitConversion type represents a conversion between two Unit types in Fuuz. | _complex_ | UnitConversion |
| 148 | The UnitType type represents the types of Units that can be created within Fuuz. | _complex_ | UnitType |
| 149 | The User model represents a user account in Fuuz. | _complex_ | User |
| 150 | The UserAccessControlPolicy model represents a link between Users and AccessControlPolicies, representing direct assignment of a policy to a user's access. | _complex_ | UserAccessControlPolicy |
| 151 | The UserAccessControlPolicyGroup model represents a link between a User and AccessControlPolicyGroups, representing assignment of a policy group to a user's access. | _complex_ | UserAccessControlPolicyGroup |
| 152 | An external application user to be used for configuration with Fuuz. | _complex_ | UserExternalApplicationConfiguration |
| 153 | The UserFavorites type represents the pages marked as a favorite by the user within Fuuz. | _complex_ | UserFavorite |
| 154 | The UserRole type represents the roles that an individual user has been granted. | _complex_ | UserRole |
| 155 | The UserRoutePreference type represents a user's preferences (views, etc.) for a specific route in Fuuz. | _complex_ | UserRoutePreference |
| 156 | An association between a user and setting values. | _complex_ | UserSettingValue |
| 157 | The Visualization type represents the configuration for a visualization in Fuuz. | _complex_ | Visualization |
| 158 | The VisualizationType type represents the type of a visualization in Fuuz. | _complex_ | VisualizationType |

##### data.modelDefinitions[0]
| Property | Value |
| --- | --- |
| description | The AccessControlPolicy model represents policy defining access to Fuuz resources. |
| fields | _complex array_ |
| name | AccessControlPolicy |

###### data.modelDefinitions[0].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the AccessControlPolicy. | id | ID! |
| 2 | The name of the AccessControlPolicy. | name | String! |
| 3 | The policy defining access to Fuuz resources. | policy | JSONObject! |
| 4 | The description | description | String! |
| 5 | The Users who have this policy directly assigned. | users | [UserAccessControlPolicy!] |
| 6 | The AccessControlPolicyGroups to which this policy is assigned. | groups | [AccessControlPolicyGroupAccessControlPolicy!] |
| 7 | A relation to the data changes for this document. | dataChanges | [AccessControlPolicyDataChange!] |
| 8 | The date and time the record was created. | createdAt | DateTime! |
| 9 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 10 | The User who created this record. | createdByUser | User! |
| 11 | The date and time the record was last updated. | updatedAt | DateTime! |
| 12 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 13 | The User who last updated this record. | updatedByUser | User! |
| 14 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 15 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 16 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | AccessControlPolicyAggregate! |
| 17 | Custom fields configured for the data model. | _customFields | AccessControlPolicy_CustomFields! |

##### data.modelDefinitions[1]
| Property | Value |
| --- | --- |
| description | The AccessControlPolicyGroup model represents a group of access control policies, collectively controlling access to Fuuz resources. |
| fields | _complex array_ |
| name | AccessControlPolicyGroup |

###### data.modelDefinitions[1].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the AccessControlPolicyGroup. | id | ID! |
| 2 | The name of the AccessControlPolicyGroup. | name | String! |
| 3 | The description | description | String! |
| 4 | The Users who have this group assigned. | users | [UserAccessControlPolicyGroup!] |
| 5 | The AccessControlPolicies which are assigned to this group. | accessControlPolicies | [AccessControlPolicyGroupAccessControlPolicy!] |
| 6 | The Roles which are assigned to this group. | roles | [RoleAccessControlPolicyGroup!] |
| 7 | A relation to the data changes for this document. | dataChanges | [AccessControlPolicyGroupDataChange!] |
| 8 | The date and time the record was created. | createdAt | DateTime! |
| 9 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 10 | The User who created this record. | createdByUser | User! |
| 11 | The date and time the record was last updated. | updatedAt | DateTime! |
| 12 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 13 | The User who last updated this record. | updatedByUser | User! |
| 14 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 15 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 16 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | AccessControlPolicyGroupAggregate! |
| 17 | Custom fields configured for the data model. | _customFields | AccessControlPolicyGroup_CustomFields! |

##### data.modelDefinitions[2]
| Property | Value |
| --- | --- |
| description | The AccessControlPolicyGroupAccessControlPolicy model represents a link between AccessControlPolicies and AccessControlPolicyGroups, defining which policies are members of which groups. |
| fields | _complex array_ |
| name | AccessControlPolicyGroupAccessControlPolicy |

###### data.modelDefinitions[2].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the AccessControlPolicyGroupAccessControlPolicy. | id | ID! |
| 2 | The unique identifier of the AccessControlPolicyGroup the AccessControlPolicy is assigned to. | accessControlPolicyGroupId | ID! |
| 3 | The AccessControlPolicyGroup the AccessControlPolicy is assigned to. | accessControlPolicyGroup | AccessControlPolicyGroup! |
| 4 | The unique identifier of the AccessControlPolicy assigned to the AccessControlPolicyGroup. | accessControlPolicyId | ID! |
| 5 | The AccessControlPolicy assigned to the AccessControlPolicyGroup. | accessControlPolicy | AccessControlPolicy! |
| 6 | A relation to the data changes for this document. | dataChanges | [AccessControlPolicyGroupAccessControlPolicyDataChange!] |
| 7 | The date and time the record was created. | createdAt | DateTime! |
| 8 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 9 | The User who created this record. | createdByUser | User! |
| 10 | The date and time the record was last updated. | updatedAt | DateTime! |
| 11 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 12 | The User who last updated this record. | updatedByUser | User! |
| 13 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 14 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 15 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | AccessControlPolicyGroupAccessControlPolicyAggregate! |
| 16 | Custom fields configured for the data model. | _customFields | AccessControlPolicyGroupAccessControlPolicy_CustomFields! |

##### data.modelDefinitions[3]
| Property | Value |
| --- | --- |
| description | This model represnts an address that can be embedded into other models |
| fields | _complex array_ |
| name | Address |

###### data.modelDefinitions[3].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The line(s) that compose this address. | lines | [String] |
| 2 | The city associated with this address. | city | String |
| 3 | The state associated with this address. | state | String |
| 4 | The postal code associated with this address. | postalCode | String |
| 5 | The country associated with this address. | country | String |
| 6 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | AddressAggregate! |

##### data.modelDefinitions[4]
| Property | Value |
| --- | --- |
| description | The ApplicationComponentDataFlowReference type includes metadata about a data flow that is referenced in an application. |
| fields | _complex array_ |
| name | ApplicationComponentDataFlowReference |

###### data.modelDefinitions[4].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The ID of the referenced data flow. | dataFlowId | ID! |
| 2 | The referenced data flow. | dataFlow | DataFlow |
| 3 | The ID of the referenced data flow element. | dataFlowElementId | ID |
| 4 | The referenced data flow element. | dataFlowElement | DataFlowElement |
| 5 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ApplicationComponentDataFlowReferenceAggregate! |

##### data.modelDefinitions[5]
| Property | Value |
| --- | --- |
| description | The ApplicationComponentDataModelReference type includes metadata about a data model that is referenced in an application. |
| fields | _complex array_ |
| name | ApplicationComponentDataModelReference |

###### data.modelDefinitions[5].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The ID of the referenced data model. | dataModelId | ID! |
| 2 | The referenced data model. | dataModel | DataModel |
| 3 | The ID of the referenced data model element. | dataModelElementId | ID |
| 4 | The referenced data model element. | dataModelElement | DataModelElement |
| 5 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ApplicationComponentDataModelReferenceAggregate! |

##### data.modelDefinitions[6]
| Property | Value |
| --- | --- |
| description | The ApplicationComponentElementReference type includes metadata about an application component element that is referenced in an application. |
| fields | _complex array_ |
| name | ApplicationComponentElementReference |

###### data.modelDefinitions[6].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The data model and element for this application reference. | dataModel | ApplicationComponentDataModelReference |
| 2 | The data flow and element for this application reference. | dataFlow | ApplicationComponentDataFlowReference |
| 3 | The screen and element for this application reference. | screen | ApplicationComponentScreenReference |

##### data.modelDefinitions[7]
| Property | Value |
| --- | --- |
| description | Normalized metadata about a relationship between a two app components or elements. |
| fields | _complex array_ |
| name | ApplicationComponentReference |

###### data.modelDefinitions[7].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 | The ID of the application component type which is the source of the reference. The value of this field aligns with the source property which will be populated with reference metadata. | sourceApplicationComponentTypeId | ID! |
| 3 | The application component type which is the source of the reference. | sourceApplicationComponentType | ApplicationComponentType! |
| 4 | The source application component and element for this application reference. | source | ApplicationComponentElementReference! |
| 5 | The application component type which is the target of the reference. The value of this field aligns with the target property which will be populated with reference metadata. | targetApplicationComponentTypeId | ID! |
| 6 | The application component type which is the target of the reference. | targetApplicationComponentType | ApplicationComponentType! |
| 7 | The target application component and element for this application reference. | target | ApplicationComponentElementReference! |
| 8 | The date and time the record was created. | createdAt | DateTime! |
| 9 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 10 | The User who created this record. | createdByUser | User! |
| 11 | The date and time the record was last updated. | updatedAt | DateTime! |
| 12 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 13 | The User who last updated this record. | updatedByUser | User! |
| 14 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 15 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 16 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ApplicationComponentReferenceAggregate! |

##### data.modelDefinitions[8]
| Property | Value |
| --- | --- |
| description | The ApplicationComponentScreenReference type includes metadata about a screen that is referenced in an application. |
| fields | _complex array_ |
| name | ApplicationComponentScreenReference |

###### data.modelDefinitions[8].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The ID of the referenced screen. | screenId | ID! |
| 2 | The referenced screen. | screen | Screen |
| 3 | The ID of the referenced screen element. | screenElementId | ID |
| 4 | The referenced screen element. | screenElement | ScreenElement |
| 5 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ApplicationComponentScreenReferenceAggregate! |

##### data.modelDefinitions[9]
| Property | Value |
| --- | --- |
| description | The ApplicationConfiguration model represents the types of components which make up a Fuuz application. |
| fields | _complex array_ |
| name | ApplicationComponentType |

###### data.modelDefinitions[9].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 | The name of this application component type. | name | String! |
| 3 | The description of this application component type. | description | String! |
| 4 | The date and time the record was created. | createdAt | DateTime! |
| 5 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 6 | The User who created this record. | createdByUser | User! |
| 7 | The date and time the record was last updated. | updatedAt | DateTime! |
| 8 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 9 | The User who last updated this record. | updatedByUser | User! |
| 10 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 11 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 12 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ApplicationComponentTypeAggregate! |

##### data.modelDefinitions[10]
| Property | Value |
| --- | --- |
| description | The ApplicationConfiguration model represents configuration values for a Fuuz application. |
| fields | _complex array_ |
| name | ApplicationConfiguration |

###### data.modelDefinitions[10].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the ApplicationConfiguration. | id | ID! |
| 2 | The name of the configuration. | name | String! |
| 3 | The description of the configuration. | description | String! |
| 4 | The JSON schema of the configuration values. | configSchema | JSONObject! |
| 5 | The JSON containing the configuration values. | configJson | JSONObject! |
| 6 | The unique identifier of the module this Application Configuration is part of. | moduleId | ID |
| 7 | The module this Application Configuration is part of. | module | Module |
| 8 | A relation to the data changes for this document. | dataChanges | [ApplicationConfigurationDataChange!] |
| 9 | The date and time the record was created. | createdAt | DateTime! |
| 10 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 11 | The User who created this record. | createdByUser | User! |
| 12 | The date and time the record was last updated. | updatedAt | DateTime! |
| 13 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 14 | The User who last updated this record. | updatedByUser | User! |
| 15 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 16 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 17 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ApplicationConfigurationAggregate! |
| 18 | Custom fields configured for the data model. | _customFields | ApplicationConfiguration_CustomFields! |

##### data.modelDefinitions[11]
| Property | Value |
| --- | --- |
| description | This model represents the metadata associated with a record. If the record was installed or updated by an Application or Package that will be referenced here. |
| fields | _complex array_ |
| name | ApplicationMetadata |

###### data.modelDefinitions[11].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The ID of the installed package that created or updated this record. | installedPackageId | ID |
| 2 | Relation to the Installed Package | installedPackage | InstalledPackage |
| 3 | The package installation that created or updated this record. | packageInstallationId | ID |
| 4 | Relation to the Package Installation | packageInstallation | PackageInstallation |
| 5 | The package definition that created or updated this record. | packageDefinitionId | ID |
| 6 | Relation to the Package Definition | packageDefinition | PackageDefinition |
| 7 | The package definition version that created or updated this record. | packageDefinitionVersionId | ID |
| 8 | Relation to the Package Definition Version | packageDefinitionVersion | PackageDefinitionVersion |
| 9 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ApplicationMetadataAggregate! |

##### data.modelDefinitions[12]
| Property | Value |
| --- | --- |
| description | The Application Span Event Log Event model will collect information about what ran a mutation. The specific Application Span Event Log Id can be found on any Application models through the _trace field. This will only capture trace information for span events that run a mutation. |
| fields | _complex array_ |
| name | ApplicationSpanEventLog |

###### data.modelDefinitions[12].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 | The ID of the parent span, this is not a relation. When you group by the spanId you can see all the child requests that were created under the parent. | spanId | ID |
| 3 | The service the request originates from. | originServiceName | String |
| 4 | The service the request was forwarded through. Through a trace, this could create a chain of services the request was forwarded through. | forwardedForServiceName | String |
| 5 | The type of event that was logged. Ex: 'Mutation' | eventType | String! |
| 6 | The ID of the overall trace, this is not a relation but can be used to group all spans under the trace to find all requests that mutated data from start to finish. | traceId | ID |
| 7 | The ID of the data flow that was executed as part of this span. | dataFlowId | ID |
| 8 | The Data Flow relation | dataFlow | DataFlow |
| 9 | The ID of the Saved transform that was executed as part of this span event. | savedTransformId | ID |
| 10 | The relation to the Saved Transform that was executed as part of this span event | savedTransform | SavedTransform |
| 11 | The ID of the saved transform version that was executed as part of this span event. | savedTransformVersionId | ID |
| 12 | The relation to the saved transform version that was created as part of this span event. | savedTransformVersion | SavedTransformVersion |
| 13 | The data flow element ID that was executed as part of this span event. | dataFlowElementId | ID |
| 14 | The relation to the data flow element that was executed as part of this span event. | dataFlowElement | DataFlowElement |
| 15 | The ID of the data flow version that was executed as part of this span event. | dataFlowVersionId | ID |
| 16 | The relation to the Data Flow Version that was executed as part of this span event. | dataFlowVersion | DataFlowVersion |
| 17 | The ID of the gateway that executed a request creating this span event. | deviceGatewayId | ID |
| 18 | Then relation to the gateway that executed a request for this span event. | deviceGateway | DeviceGateway |
| 19 | The gateway device that executed a request to create this span event. | deviceGatewayDeviceId | ID |
| 20 | The relation to the gateway device that executed a request to create this span event. | deviceGatewayDevice | DeviceGatewayDevice |
| 21 | The URL in which the span event log was captured. | url | String |
| 22 | The ID of the screen that executed a request to create the span event log event. | screenId | ID |
| 23 | The relation to the screen that executed a request to create this span event log event. | screen | Screen |
| 24 | The ID of the screen element that executed a request that created this span event log event. | screenElementId | ID |
| 25 | The relation to the screen element that executed a request that created this span event log event. | screenElement | ScreenElement |
| 26 | The ID of the screen version that executed a request that created this span event log event. | screenVersionId | ID |
| 27 | The relation to the screen version that executed a request that created this span event log event. | screenVersion | ScreenVersion |
| 28 | The time between the start of the request and when this action completed in milliseconds. | executionTime | Int |
| 29 | The topic of the request. | topic | String |
| 30 | The date and time the record was created. | createdAt | DateTime! |
| 31 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 32 | The User who created this record. | createdByUser | User! |
| 33 | The date and time the record was last updated. | updatedAt | DateTime! |
| 34 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 35 | The User who last updated this record. | updatedByUser | User! |
| 36 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ApplicationSpanEventLogAggregate! |

##### data.modelDefinitions[13]
| Property | Value |
| --- | --- |
| description | This model represents the trace fields captured through Data Flow, Screens, Gateways, Packages, etc. |
| fields | _complex array_ |
| name | ApplicationTraceMetadata |

###### data.modelDefinitions[13].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | References the trace in which the record was created/updated within. This is not a relation but purely a reference field. | traceId | ID |
| 2 | The Application Span ID created from the records change. | applicationSpanEventLogId | ID |
| 3 | Relation to the Application Span Event Log | applicationSpanEventLog | ApplicationSpanEventLog |
| 4 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ApplicationTraceMetadataAggregate! |

##### data.modelDefinitions[14]
| Property | Value |
| --- | --- |
| description | This model represents the trace fields captured through Data Flow, Screens, Gateways, Packages, etc. |
| fields | _complex array_ |
| name | ApplicationTraceMetadataDataChange |

###### data.modelDefinitions[14].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | References the trace in which the record was created/updated within. This is not a relation but purely a reference field. | traceId | ID |
| 2 | The Application Span ID created from the records change. | applicationSpanEventLogId | ID |
| 3 | Relation to the Application Span Event Log | applicationSpanEventLog | ApplicationSpanEventLog |
| 4 | The ID of the data flow that was executed as part of this span. | dataFlowId | ID |
| 5 | The Data Flow relation | dataFlow | DataFlow |
| 6 | The ID of the Saved transform that was executed as part of this span event. | savedTransformId | ID |
| 7 | The relation to the Saved Transform that was executed as part of this span event | savedTransform | SavedTransform |
| 8 | The ID of the saved transform version that was executed as part of this span event. | savedTransformVersionId | ID |
| 9 | The relation to the saved transform version that was created as part of this span event. | savedTransformVersion | SavedTransformVersion |
| 10 | The data flow element ID that was executed as part of this span event. | dataFlowElementId | ID |
| 11 | The relation to the data flow element that was executed as part of this span event. | dataFlowElement | DataFlowElement |
| 12 | The ID of the data flow version that was executed as part of this span event. | dataFlowVersionId | ID |
| 13 | The relation to the Data Flow Version that was executed as part of this span event. | dataFlowVersion | DataFlowVersion |
| 14 | The ID of the gateway that executed a request creating this span event. | deviceGatewayId | ID |
| 15 | Then relation to the gateway that executed a request for this span event. | deviceGateway | DeviceGateway |
| 16 | The gateway device that executed a request to create this span event. | deviceGatewayDeviceId | ID |
| 17 | The relation to the gateway device that executed a request to create this span event. | deviceGatewayDevice | DeviceGatewayDevice |
| 18 | The URL in which the span event log was captured. | url | String |
| 19 | The ID of the screen that executed a request to create the span event log event. | screenId | ID |
| 20 | The relation to the screen that executed a request to create this span event log event. | screen | Screen |
| 21 | The ID of the screen element that executed a request that created this span event log event. | screenElementId | ID |
| 22 | The relation to the screen element that executed a request that created this span event log event. | screenElement | ScreenElement |
| 23 | The ID of the screen version that executed a request that created this span event log event. | screenVersionId | ID |
| 24 | The relation to the screen version that executed a request that created this span event log event. | screenVersion | ScreenVersion |
| 25 | The topic of the request. | topic | String |
| 26 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ApplicationTraceMetadataDataChangeAggregate! |

##### data.modelDefinitions[15]
| Property | Value |
| --- | --- |
| description | A Fuuz Calendar. |
| fields | _complex array_ |
| name | Calendar |

###### data.modelDefinitions[15].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Calendar. | id | ID! |
| 2 | The name of this Calendar. | name | String! |
| 3 | The description of the Calendar. | description | String! |
| 4 | A JSON object to store additional configuration. | customData | JSONObject |
| 5 | The ID of the calendarType for this association. | calendarTypeId | ID |
| 6 | A relation to the Calendar Type. | calendarType | CalendarType |
| 7 | A relation to the events that are on this calendar. | calendarEvents | [CalendarEvent!] |
| 8 | A relation to the data changes for this document. | dataChanges | [CalendarDataChange!] |
| 9 | The date and time the record was created. | createdAt | DateTime! |
| 10 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 11 | The User who created this record. | createdByUser | User! |
| 12 | The date and time the record was last updated. | updatedAt | DateTime! |
| 13 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 14 | The User who last updated this record. | updatedByUser | User! |
| 15 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 16 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 17 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | CalendarAggregate! |
| 18 | Custom fields configured for the data model. | _customFields | Calendar_CustomFields! |

##### data.modelDefinitions[16]
| Property | Value |
| --- | --- |
| description | A Fuuz Calendar Event. |
| fields | _complex array_ |
| name | CalendarEvent |

###### data.modelDefinitions[16].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the CalendarEvent. | id | ID! |
| 2 | The title of the calendar event. | title | String! |
| 3 | The description of the calendar event. | description | String! |
| 4 | The time zone of the calendar event. | timeZone | String |
| 5 | The priority of this calendar event. | priority | Int! |
| 6 | The start datetime of the calendar event as an ISO Timestamp. For recurring events this will be the start of the recurrence. This field is automatically set when changes are made to the eventJson. | startAt | DateTime |
| 7 | The end datetime of the calendar event as an ISO Timestamp. For recurring events this will be the end of the first recurrence. This field is automatically set when changes are made to the eventJson. | endAt | DateTime |
| 8 | The JSON blob that defines the details of this event, consumed by FullCalendar | eventJson | JSONObject! |
| 9 | A JSON object to store additional configuration. | customData | JSONObject |
| 10 | The ID of the calendar that this event is a part of. | calendarId | ID! |
| 11 | A relation to the calendar that this event is a part of. | calendar | Calendar! |
| 12 | The ID of the calendar event type of this event. | calendarEventTypeId | ID! |
| 13 | A relation to the calendar event type of this event. | calendarEventType | CalendarEventType! |
| 14 | A list of CalendarEventGroupCalendarEvent associated to this event. | calendarEventGroupCalendarEvents | [CalendarEventGroupCalendarEvent!]! |
| 15 | The calendarEventResource for this association. | calendarEventResources | [CalendarEventResource!] |
| 16 | A relation to the data changes for this document. | dataChanges | [CalendarEventDataChange!] |
| 17 | The date and time the record was created. | createdAt | DateTime! |
| 18 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 19 | The User who created this record. | createdByUser | User! |
| 20 | The date and time the record was last updated. | updatedAt | DateTime! |
| 21 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 22 | The User who last updated this record. | updatedByUser | User! |
| 23 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 24 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 25 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | CalendarEventAggregate! |
| 26 | Custom fields configured for the data model. | _customFields | CalendarEvent_CustomFields! |

##### data.modelDefinitions[17]
| Property | Value |
| --- | --- |
| description | A grouping of Fuuz Calendar Events. |
| fields | _complex array_ |
| name | CalendarEventGroup |

###### data.modelDefinitions[17].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of this CalendarEventGroup. | id | ID! |
| 2 | The label for this group | label | String! |
| 3 | The color for this group | color | String |
| 4 | A list of CalendarEventGroupCalendarEvent associated to this group. | calendarEventGroupCalendarEvents | [CalendarEventGroupCalendarEvent!]! |
| 5 | A relation to the data changes for this document. | dataChanges | [CalendarEventGroupDataChange!] |
| 6 | The date and time the record was created. | createdAt | DateTime! |
| 7 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 8 | The User who created this record. | createdByUser | User! |
| 9 | The date and time the record was last updated. | updatedAt | DateTime! |
| 10 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 11 | The User who last updated this record. | updatedByUser | User! |
| 12 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 13 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 14 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | CalendarEventGroupAggregate! |
| 15 | Custom fields configured for the data model. | _customFields | CalendarEventGroup_CustomFields! |

##### data.modelDefinitions[18]
| Property | Value |
| --- | --- |
| description | An association between a Fuuz Calendar Event and a Grouping. |
| fields | _complex array_ |
| name | CalendarEventGroupCalendarEvent |

###### data.modelDefinitions[18].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of this CalendarEventGroupCalendarEvent. | id | ID! |
| 2 | The ID of the calendarEvent for this association. | calendarEventId | ID! |
| 3 | The calendarEvent for this association. | calendarEvent | CalendarEvent! |
| 4 | The ID of the calendarEventGroup for this association. | calendarEventGroupId | ID! |
| 5 | The calendarEventGroup for this association. | calendarEventGroup | CalendarEventGroup! |
| 6 | A relation to the data changes for this document. | dataChanges | [CalendarEventGroupCalendarEventDataChange!] |
| 7 | The date and time the record was created. | createdAt | DateTime! |
| 8 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 9 | The User who created this record. | createdByUser | User! |
| 10 | The date and time the record was last updated. | updatedAt | DateTime! |
| 11 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 12 | The User who last updated this record. | updatedByUser | User! |
| 13 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 14 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 15 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | CalendarEventGroupCalendarEventAggregate! |
| 16 | Custom fields configured for the data model. | _customFields | CalendarEventGroupCalendarEvent_CustomFields! |

##### data.modelDefinitions[19]
| Property | Value |
| --- | --- |
| description | An association between Fuuz Calendar Events and Fuuz Calendar Resources. |
| fields | _complex array_ |
| name | CalendarEventResource |

###### data.modelDefinitions[19].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of this CalendarEventResource. | id | ID! |
| 2 | The ID of the calendarEvent for this association. | calendarEventId | ID! |
| 3 | The calendarEvent for this association. | calendarEvent | CalendarEvent! |
| 4 | The ID of the calendarResource for this association. | calendarResourceId | ID! |
| 5 | The calendarResource for this association. | calendarResource | CalendarResource! |
| 6 | A relation to the data changes for this document. | dataChanges | [CalendarEventResourceDataChange!] |
| 7 | The date and time the record was created. | createdAt | DateTime! |
| 8 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 9 | The User who created this record. | createdByUser | User! |
| 10 | The date and time the record was last updated. | updatedAt | DateTime! |
| 11 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 12 | The User who last updated this record. | updatedByUser | User! |
| 13 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 14 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 15 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | CalendarEventResourceAggregate! |
| 16 | Custom fields configured for the data model. | _customFields | CalendarEventResource_CustomFields! |

##### data.modelDefinitions[20]
| Property | Value |
| --- | --- |
| description | An Fuuz Calendar Event Type. |
| fields | _complex array_ |
| name | CalendarEventType |

###### data.modelDefinitions[20].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the CalendarEventType. | id | ID! |
| 2 | The calendar event type. | label | String! |
| 3 | A detailed description of the calendar event type. | description | String! |
| 4 | The date and time the record was created. | createdAt | DateTime! |
| 5 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 6 | The User who created this record. | createdByUser | User! |
| 7 | The date and time the record was last updated. | updatedAt | DateTime! |
| 8 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 9 | The User who last updated this record. | updatedByUser | User! |
| 10 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 11 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 12 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | CalendarEventTypeAggregate! |

##### data.modelDefinitions[21]
| Property | Value |
| --- | --- |
| description | A Fuuz Calendar Resource. |
| fields | _complex array_ |
| name | CalendarResource |

###### data.modelDefinitions[21].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the CalendarResource. | id | ID! |
| 2 | The title of the calendar resource. | title | String! |
| 3 | The description of the calendar resource. | description | String! |
| 4 | A JSON object to store additional configuration. | customData | JSONObject |
| 5 | The color of the event backgrounds in this calendar resource. | eventBackgroundColor | String |
| 6 | The color of the border of events in this calendar resource. | eventBorderColor | String |
| 7 | The color of the events in this calendar resource. | eventColor | String |
| 8 | The color of the an event's text in this calendar resource. | eventTextColor | String |
| 9 | The ID of the parent calendar resource. | calendarResourceParentId | ID |
| 10 | The parent calendar resource. | calendarResourceParent | CalendarResource |
| 11 | The calendarEventResource for this association. | calendarEventResources | [CalendarEventResource!] |
| 12 | A relation to the data changes for this document. | dataChanges | [CalendarResourceDataChange!] |
| 13 | The date and time the record was created. | createdAt | DateTime! |
| 14 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 15 | The User who created this record. | createdByUser | User! |
| 16 | The date and time the record was last updated. | updatedAt | DateTime! |
| 17 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 18 | The User who last updated this record. | updatedByUser | User! |
| 19 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 20 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 21 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | CalendarResourceAggregate! |
| 22 | Custom fields configured for the data model. | _customFields | CalendarResource_CustomFields! |

##### data.modelDefinitions[22]
| Property | Value |
| --- | --- |
| description | A type of Fuuz calendar. |
| fields | _complex array_ |
| name | CalendarType |

###### data.modelDefinitions[22].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the calendar type. | id | ID! |
| 2 | The label of this calendar type. | label | String! |
| 3 | The description of the calendar type. | description | String! |
| 4 | A relation to the data changes for this document. | dataChanges | [CalendarTypeDataChange!] |
| 5 | The date and time the record was created. | createdAt | DateTime! |
| 6 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 7 | The User who created this record. | createdByUser | User! |
| 8 | The date and time the record was last updated. | updatedAt | DateTime! |
| 9 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 10 | The User who last updated this record. | updatedByUser | User! |
| 11 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 12 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 13 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | CalendarTypeAggregate! |
| 14 | Custom fields configured for the data model. | _customFields | CalendarType_CustomFields! |

##### data.modelDefinitions[23]
| Property | Value |
| --- | --- |
| description | The Channel model represents a grouping of tags. |
| fields | _complex array_ |
| name | Channel |

###### data.modelDefinitions[23].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Channel. | id | ID! |
| 2 | The code of the Channel. | code | String! |
| 3 | The description of the Channel. | description | String! |
| 4 | The unique identifier of the Channel Status this Channel is currently in. | channelStatusId | ID! |
| 5 | The Channel Status this Channel is currently in. | channelStatus | ChannelStatus! |
| 6 | The tags associated with this channel. | tags | [Tag!]! |
| 7 | A relation to the data changes for this document. | dataChanges | [ChannelDataChange!] |
| 8 | The date and time the record was created. | createdAt | DateTime! |
| 9 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 10 | The User who created this record. | createdByUser | User! |
| 11 | The date and time the record was last updated. | updatedAt | DateTime! |
| 12 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 13 | The User who last updated this record. | updatedByUser | User! |
| 14 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 15 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 16 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ChannelAggregate! |
| 17 | Custom fields configured for the data model. | _customFields | Channel_CustomFields! |

##### data.modelDefinitions[24]
| Property | Value |
| --- | --- |
| description | The ChannelStatus model represents a status that a Channel can be in. |
| fields | _complex array_ |
| name | ChannelStatus |

###### data.modelDefinitions[24].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Channel Status. | id | ID! |
| 2 | The label of the Channel Status. | label | String! |
| 3 | The description of the Channel Status. | description | String! |
| 4 | Whether or not the Channel Status should be a pickable option. Used to deprecate statuses. | usable | Boolean! |
| 5 | Whether or not the Channel Status indicates the Channel is active. | activeStatus | Boolean! |
| 6 | A relation to the data changes for this document. | dataChanges | [ChannelStatusDataChange!] |
| 7 | The date and time the record was created. | createdAt | DateTime! |
| 8 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 9 | The User who created this record. | createdByUser | User! |
| 10 | The date and time the record was last updated. | updatedAt | DateTime! |
| 11 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 12 | The User who last updated this record. | updatedByUser | User! |
| 13 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 14 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 15 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ChannelStatusAggregate! |
| 16 | Custom fields configured for the data model. | _customFields | ChannelStatus_CustomFields! |

##### data.modelDefinitions[25]
| Property | Value |
| --- | --- |
| description | The Connection model represents a Connector that has a set of options and credentials provided to it for connecting to another system. |
| fields | _complex array_ |
| name | Connection |

###### data.modelDefinitions[25].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Connection. | id | ID! |
| 2 | The name of the Connection. | name | String! |
| 3 | Whether the Connection is active. Connections that are not active can't be used in the integration system. | active | Boolean! |
| 4 | The unique identifier of the Connector this Connection uses. | connectorId | ID! |
| 5 | The Connector this Connection uses. | connector | Connector! |
| 6 | The URL to use for the Connection. | url | String! |
| 7 | The options to pass to the Connector when using this Connection. | options | JSON! |
| 8 | The credentials to pass to the Connector when using this Connection. | credentials | JSON! |
| 9 | Whether to enable request payload logging for this Connection. This is used to log the request payloads for this Connection. | requestPayloadLogging | Boolean |
| 10 | Whether to enable response payload logging for this Connection. This is used to log the response payloads for this Connection. | responsePayloadLogging | Boolean |
| 11 | A relation to the data changes for this document. | dataChanges | [ConnectionDataChange!] |
| 12 | The date and time the record was created. | createdAt | DateTime! |
| 13 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 14 | The User who created this record. | createdByUser | User! |
| 15 | The date and time the record was last updated. | updatedAt | DateTime! |
| 16 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 17 | The User who last updated this record. | updatedByUser | User! |
| 18 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 19 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 20 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ConnectionAggregate! |
| 21 | Custom fields configured for the data model. | _customFields | Connection_CustomFields! |

##### data.modelDefinitions[26]
| Property | Value |
| --- | --- |
| description | The Connector model represents an application or protocol that Fuuz can connect to. |
| fields | _complex array_ |
| name | Connector |

###### data.modelDefinitions[26].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Connector. | id | ID! |
| 2 | The name of the Connector. | name | String! |
| 3 | The description of the Connector. | description | String! |
| 4 | The JSON Schema definition for the options the Connector accepts. | optionsSchema | JSON! |
| 5 | The JSON Schema definition for the credentials the Connector accepts. | credentialsSchema | JSON! |
| 6 | The date and time the record was created. | createdAt | DateTime! |
| 7 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 8 | The User who created this record. | createdByUser | User! |
| 9 | The date and time the record was last updated. | updatedAt | DateTime! |
| 10 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 11 | The User who last updated this record. | updatedByUser | User! |
| 12 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 13 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 14 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ConnectorAggregate! |

##### data.modelDefinitions[27]
| Property | Value |
| --- | --- |
| description | The DataFlow model represents an Fuuz data flow, used to design event flows, integrations, or workflows. |
| fields | _complex array_ |
| name | DataFlow |

###### data.modelDefinitions[27].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Data Flow. | id | ID! |
| 2 | The name of the Data Flow. | name | String! |
| 3 | The description of the data flow. | description | String! |
| 4 | Whether the data flow is active and should run once deployed. | active | Boolean! |
| 5 | The unique identifier of the Data Flow this Data Flow substitutes. If the current Data Flow is active and deployed, it will replace the target Data Flows effectiveDeploymentId with it's currentDeploymentId | substitutesDataFlowId | ID |
| 6 | The relation to the Data Flow this Data Flow substitutes. If the current Data Flow is active and deployed, it will replace the target Data Flows effectiveDeployment with it's currentDeployment | substitutesDataFlow | DataFlow |
| 7 | The relation to the Data Flow that substitutes this Data Flow. If the Data Flow is active it will replace the current Data Flows effectiveDeploymentId with it's currentDeploymentId | substitutedByDataFlow | DataFlow |
| 8 | The unique identifier of the type of the data flow. | dataFlowTypeId | ID! |
| 9 | The type of the data flow. | dataFlowType | DataFlowType! |
| 10 | The identifier of the log level specified that defines which logs are captured. Example: "Info" - only Info, Warn, Error and Fatal will be captured ignoring Debug and Trace logs. | logLevelId | ID |
| 11 | The log level specified will define which logs are captured. Example: "Info" - only Info, Warn, Error and Fatal will be captured ignoring Debug and Trace logs. | logLevel | DataFlowDeploymentLogType |
| 12 | The configuration data necessary to expose this data flow to LLM agents via the MCP server as a tool. | mcpToolConfiguration | DataFlowMCPToolConfiguration |
| 13 | The versions of this data flow. | versions | [DataFlowVersion!] |
| 14 | The deployments of this data flow. | deployments | [DataFlowDeployment!] |
| 15 | The Data Flow Schedules for this Data Flow | schedules | [DataFlowSchedule!] |
| 16 | The unique identifier of the current deployment of this data flow. | currentDeploymentId | ID |
| 17 | The current deployment of this data flow. | currentDeployment | DataFlowDeployment |
| 18 | The unique identifier of the effective deployment of this data flow. | effectiveDeploymentId | ID |
| 19 | The effective deployment of this data flow. If the data flow is substituted by another active and deployed data flow it's current deployment will be the value. | effectiveDeployment | DataFlowDeployment |
| 20 | The unique identifier of the module this Data Flow is part of. | moduleId | ID |
| 21 | The module this Data Flow is part of. | module | Module |
| 22 | The normalized elements (nodes) of this data flow. | elements | [DataFlowElement!] |
| 23 | The application component references for which this data flow is the source. | sourceApplicationComponentReferences | [ApplicationComponentReference!] |
| 24 | The application component references for which this data flow is the target. | targetApplicationComponentReferences | [ApplicationComponentReference!] |
| 25 | A relation to the data changes for this document. | dataChanges | [DataFlowDataChange!] |
| 26 | The date and time the record was created. | createdAt | DateTime! |
| 27 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 28 | The User who created this record. | createdByUser | User! |
| 29 | The date and time the record was last updated. | updatedAt | DateTime! |
| 30 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 31 | The User who last updated this record. | updatedByUser | User! |
| 32 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 33 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 34 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataFlowAggregate! |
| 35 | Custom fields configured for the data model. | _customFields | DataFlow_CustomFields! |

##### data.modelDefinitions[28]
| Property | Value |
| --- | --- |
| description | The DataFlowDeployment model represents a deployment of a version of an Fuuz data flow, which indicates that the flow should be run in the engine. |
| fields | _complex array_ |
| name | DataFlowDeployment |

###### data.modelDefinitions[28].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Data Flow Deployment. | id | ID! |
| 2 | The label containing the version and the created date to be used in screens <VERSION>: <mm-dd-yy hh:mm:ss>  | label | String! |
| 3 | The JSON containing the data flow, for consumption by the data flow engine. | flow | JSONObject! |
| 4 | The unique identifier of the data flow this record is a deployment of. | dataFlowId | ID! |
| 5 | The data flow this record is a deployment of. | dataFlow | DataFlow! |
| 6 | The unique identifier of the data flow version this record is a deployment of. | dataFlowVersionId | ID! |
| 7 | The data flow version this record is a deployment of. | dataFlowVersion | DataFlowVersion! |
| 8 | The logs from this data flow deployment. | logs | [DataFlowDeploymentLog!] |
| 9 | Defines if all previous versions of the data flow were forced to stopped after this deployment. | forceStopPreviousVersions | Boolean |
| 10 | The date and time the record was created. | createdAt | DateTime! |
| 11 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 12 | The User who created this record. | createdByUser | User! |
| 13 | The date and time the record was last updated. | updatedAt | DateTime! |
| 14 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 15 | The User who last updated this record. | updatedByUser | User! |
| 16 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 17 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 18 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataFlowDeploymentAggregate! |

##### data.modelDefinitions[29]
| Property | Value |
| --- | --- |
| description | The DataFlowDeploymentLog model represents a log tied to a deployment of an Fuuz data flow.  Logs could be errors that occurred during flow execution, or data explicitly logged via the Log node. |
| fields | _complex array_ |
| name | DataFlowDeploymentLog |

###### data.modelDefinitions[29].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Data Flow Deployment. | id | ID! |
| 2 | The unique identifier of the data flow deployment this record was logged from. | dataFlowDeploymentId | ID |
| 3 | The data flow deployment this record was logged from. | dataFlowDeployment | DataFlowDeployment |
| 4 | The unique identifier of the type of this log. | dataFlowDeploymentLogTypeId | ID! |
| 5 | The type of this log. | dataFlowDeploymentLogType | DataFlowDeploymentLogType |
| 6 | The unique identifier for the data flow that logged this record. | dataFlowId | ID |
| 7 | The data flow this record was logged from. | dataFlow | DataFlow |
| 8 | The unique identifier for the data flow version that logged this record. | dataFlowVersionId | ID |
| 9 | The data flow this record was logged from. | dataFlowVersion | DataFlowVersion |
| 10 | The unique identifier of the Data Flow substitute that logged this record. | substitutedByDataFlowId | ID |
| 11 | The relation to the Data Flow substitute that logged this record. | substitutedByDataFlow | DataFlow |
| 12 | The unique identifier of the user that executed the data flow that logged this record. | executedByUserId | ID |
| 13 | The user that executed the data flow that logged this record. | executedByUser | User |
| 14 | The id of the execution that triggered this log. | executionMessageId | String |
| 15 | The id of the node that logged this message. | nodeId | String |
| 16 | The name of the node that logged this message. | nodeName | String |
| 17 | The type of the node that logged this message. | nodeType | String |
| 18 | The human-readable message from the log. | message | String! |
| 19 | A shortened version of the message property | shortMessage | String |
| 20 | The JSON containing metadata for this log. | metadata | JSONObject! |
| 21 | The date and time the record was created. | createdAt | DateTime! |
| 22 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 23 | The User who created this record. | createdByUser | User! |
| 24 | The date and time the record was last updated. | updatedAt | DateTime! |
| 25 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 26 | The User who last updated this record. | updatedByUser | User! |
| 27 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 28 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 29 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataFlowDeploymentLogAggregate! |

##### data.modelDefinitions[30]
| Property | Value |
| --- | --- |
| description | The DataFlowDeploymentLogType model represents the type of an Fuuz data flow log. |
| fields | _complex array_ |
| name | DataFlowDeploymentLogType |

###### data.modelDefinitions[30].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the data flow deployment log type. | id | ID! |
| 2 | The label of the data flow deployment log type. | label | String! |
| 3 | The description of the data flow deployment log type. | description | String! |
| 4 | The sort order of the data flow deployment log type. | sortOrder | Int! |
| 5 | The date and time the record was created. | createdAt | DateTime! |
| 6 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 7 | The User who created this record. | createdByUser | User! |
| 8 | The date and time the record was last updated. | updatedAt | DateTime! |
| 9 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 10 | The User who last updated this record. | updatedByUser | User! |
| 11 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 12 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 13 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataFlowDeploymentLogTypeAggregate! |

##### data.modelDefinitions[31]
| Property | Value |
| --- | --- |
| description | The content of a Node in a Data Flow. |
| fields | _complex array_ |
| name | DataFlowElement |

###### data.modelDefinitions[31].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 | The Id of the field within the model. | elementId | String! |
| 3 | The name of the field. | name | String! |
| 4 | The full type string of the field. | type | String! |
| 5 | The description of the field. | description | String |
| 6 | The full definition of the field. | definition | JSONObject! |
| 7 | The type-specific configuration of the field. | configuration | JSONObject! |
| 8 | The path at which this node can be found within the flow. | path | String! |
| 9 | The ID of the Data Flow that contains this node. | dataFlowId | ID! |
| 10 | The Data Flow that contains this node. | dataFlow | DataFlow! |
| 11 | The editor debug information for the Data Flow Node. | debug | DataFlowElementDebug |
| 12 | The editor logging information for the Data Flow Node. | logging | DataFlowElementLogging |
| 13 | The application component references for which this data flow element is the source. | sourceApplicationComponentReferences | [ApplicationComponentReference!] |
| 14 | The application component references for which this data flow element is the target. | targetApplicationComponentReferences | [ApplicationComponentReference!] |
| 15 | A relation to the data changes for this document. | dataChanges | [DataFlowElementDataChange!] |
| 16 | The date and time the record was created. | createdAt | DateTime! |
| 17 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 18 | The User who created this record. | createdByUser | User! |
| 19 | The date and time the record was last updated. | updatedAt | DateTime! |
| 20 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 21 | The User who last updated this record. | updatedByUser | User! |
| 22 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 23 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 24 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataFlowElementAggregate! |

##### data.modelDefinitions[32]
| Property | Value |
| --- | --- |
| description | This model represnts the debug configuration of a data flow node. |
| fields | _complex array_ |
| name | DataFlowElementDebug |

###### data.modelDefinitions[32].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | Enabling breakpoint halts the execution of the flow in the designer when it reaches the node. | breakpointEnabled | Boolean |
| 2 | Enabling allows the state of the publish or subscribe node to be picked up on the front when debugging. | publishSubscribeClient | Boolean |
| 3 | Enabling allows the payload of the publish node to be sent to Fuuz server. | publishToFuuz | Boolean |
| 4 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataFlowElementDebugAggregate! |

##### data.modelDefinitions[33]
| Property | Value |
| --- | --- |
| description | This model represents the logging configuration of a data flow node. |
| fields | _complex array_ |
| name | DataFlowElementLogging |

###### data.modelDefinitions[33].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | Enabling allows the collection of metrics for this node. | executionMetrics | Boolean |
| 2 | Enabling allows the collection of logs generated at the start of the execution of this node. | executionStarted | Boolean |
| 3 | Enabling allows the collection of logs generated when the execution of this node succeeds. | executionSucceeded | Boolean |
| 4 | Enabling allows the collection of logs generated when the execution of this node fails. | executionFailed | Boolean |
| 5 | Enabling allows the collection of trace logs generated by this node. | enableTraceLogging | Boolean |
| 6 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataFlowElementLoggingAggregate! |

##### data.modelDefinitions[34]
| Property | Value |
| --- | --- |
| description | The DataFlowEnvironment model represents the environment where an Fuuz data flow runs. |
| fields | _complex array_ |
| name | DataFlowEnvironment |

###### data.modelDefinitions[34].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Data Flow Environment. | id | ID! |
| 2 | The label of the Data Flow Environment. | label | String! |
| 3 | The description of the Data Flow Environment. | description | String! |
| 4 | The date and time the record was created. | createdAt | DateTime! |
| 5 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 6 | The User who created this record. | createdByUser | User! |
| 7 | The date and time the record was last updated. | updatedAt | DateTime! |
| 8 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 9 | The User who last updated this record. | updatedByUser | User! |
| 10 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 11 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 12 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataFlowEnvironmentAggregate! |

##### data.modelDefinitions[35]
| Property | Value |
| --- | --- |
| description | The DataFlowExecutionMetric collection stores node-level execution metrics such as node execution time for reporting purposes. |
| fields | _complex array_ |
| name | DataFlowExecutionMetric |

###### data.modelDefinitions[35].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Data Flow Execution Metric. | id | ID! |
| 2 | The unique identifier of the data flow this metric is for. | dataFlowId | ID! |
| 3 | The data flow this metric is for. | dataFlow | DataFlow |
| 4 | The unique identifier of the data flow version this metric is for. | dataFlowVersionId | ID! |
| 5 | The data flow version this metric is for. | dataFlowVersion | DataFlowVersion |
| 6 | The unique identifier of the data flow deployment this metric is for. | dataFlowDeploymentId | ID! |
| 7 | The data flow deployment this metric is for. | dataFlowDeployment | DataFlowDeployment |
| 8 | The unique identifier of the message this metric is for. | messageId | String! |
| 9 | The unique identifier of the node this metric is for. | nodeId | String! |
| 10 | The system category of the node this metric is for. | nodeType | String! |
| 11 | The user-provided name of the node this metric is for. | nodeName | String! |
| 12 | Whether or not the execution this metric is for errored. | errored | Boolean! |
| 13 | The execution time of the node this metric is for, in milliseconds. | executionTimeMs | Float! |
| 14 | The date and time the record was created. | createdAt | DateTime! |
| 15 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 16 | The User who created this record. | createdByUser | User! |
| 17 | The date and time the record was last updated. | updatedAt | DateTime! |
| 18 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 19 | The User who last updated this record. | updatedByUser | User! |
| 20 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 21 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 22 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataFlowExecutionMetricAggregate! |

##### data.modelDefinitions[36]
| Property | Value |
| --- | --- |
| description | The DataFlowMCPToolConfiguration type includes the configuration information necessary to expose a data flow as a tool in the MCP server. |
| fields | _complex array_ |
| name | DataFlowMCPToolConfiguration |

###### data.modelDefinitions[36].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | Whether this data flow should be enabled for LLM agents to execute via the MCP server. | enabled | Boolean! |
| 2 | A JSON schema defining the shape of the input the LLM agent needs to supply to the tool. This input becomes the payload to the data flow. This schema is used by the LLM agent to format the input, and to determine if this tool is the correct one to call. | inputSchema | JSONObject |
| 3 | A JSON schema defining the shape of the output the tool will return to the LLM. The data flow output is validated against this schema prior to returning the output to the agent. This schema is used by the LLM agent to determine if this is the correct tool to call. | outputSchema | JSONObject |
| 4 | If true, the tool does not modify its environment. | readOnly | Boolean! |
| 5 | If true, the tool may perform destructive updates to its environment. If false, the tool performs only additive updates. This property is meaningful only when readOnly is set to false. | destructive | Boolean! |
| 6 | If true, calling the tool repeatedly with the same arguments will have no additional effect on the its environment. This property is meaningful only when readOnly is set to false. | idempotent | Boolean! |
| 7 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataFlowMCPToolConfigurationAggregate! |

##### data.modelDefinitions[37]
| Property | Value |
| --- | --- |
| description | Data Flow Schedule is a header record which links to a Data Flow and it's Scheduled Frequencies.  |
| fields | _complex array_ |
| name | DataFlowSchedule |

###### data.modelDefinitions[37].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 | The name for the schedule, the name should represent the schedules purpose. | name | String! |
| 3 |  | description | String |
| 4 | The active field indicates whether the schedule should run. If the value is true, the schedule will be added, if false the schedule will be ignored. | active | Boolean! |
| 5 | A JSON Schema which defines the inputs that can be used for a schedule and render inputs on the frequencies form dialogs. Additionally the inputSchema is used to validate the payload provided on the frequency before the schedule executes. If an invalid payload is provided on the frequency. The frequency will be updated with a status that indicates the issue. | inputSchema | JSONObject! |
| 6 | The data flow the schedule will execute. | dataFlowId | ID! |
| 7 | The data flow the schedule will execute. | dataFlow | DataFlow! |
| 8 | The frequencies in which the schedule should emit. | dataFlowScheduleFrequencies | [DataFlowScheduleFrequency!]! |
| 9 | A relation to the data changes for this document. | dataChanges | [DataFlowScheduleDataChange!] |
| 10 | The date and time the record was created. | createdAt | DateTime! |
| 11 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 12 | The User who created this record. | createdByUser | User! |
| 13 | The date and time the record was last updated. | updatedAt | DateTime! |
| 14 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 15 | The User who last updated this record. | updatedByUser | User! |
| 16 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 17 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 18 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataFlowScheduleAggregate! |
| 19 | Custom fields configured for the data model. | _customFields | DataFlowSchedule_CustomFields! |

##### data.modelDefinitions[38]
| Property | Value |
| --- | --- |
| description | Data Flow Schedule Frequency defines the frequency in which the configured payload will be sent to the Data Flow defined by the Data Flow Schedule it is linked too. |
| fields | _complex array_ |
| name | DataFlowScheduleFrequency |

###### data.modelDefinitions[38].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 | A name that represents the purpose of the frequency. | name | String! |
| 3 | The active field indicates whether this frequency should be used or ignored. | active | Boolean! |
| 4 | The valid field indicates whether this frequency payload was validated with the schedule input schema. | valid | Boolean |
| 5 | The last logged execution of the frequency. | lastExecution | DateTime |
| 6 | The input to send to the corresponding Data Flow. | payload | JSON |
| 7 | The status is a JSON object with a message and info property. If an error occurs during initialization of the frequency a basic error message will be provided along with more detail in the info object. | status | JSON |
| 8 | The frequency configuration for the selected schedule type. | config | JSONObject |
| 9 | The Data Flow Schedule the frequency is linked too. | dataFlowScheduleId | ID! |
| 10 | The Data Flow Schedule the frequency is linked too. | dataFlowSchedule | DataFlowSchedule! |
| 11 | The schedule type to use for this frequency. | scheduleTypeId | ID! |
| 12 | The schedule type to use for this frequency. | scheduleType | ScheduleType! |
| 13 | A relation to the data changes for this document. | dataChanges | [DataFlowScheduleFrequencyDataChange!] |
| 14 | The date and time the record was created. | createdAt | DateTime! |
| 15 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 16 | The User who created this record. | createdByUser | User! |
| 17 | The date and time the record was last updated. | updatedAt | DateTime! |
| 18 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 19 | The User who last updated this record. | updatedByUser | User! |
| 20 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 21 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 22 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataFlowScheduleFrequencyAggregate! |
| 23 | Custom fields configured for the data model. | _customFields | DataFlowScheduleFrequency_CustomFields! |

##### data.modelDefinitions[39]
| Property | Value |
| --- | --- |
| description | The DataFlowType model represents the type of an Fuuz data flow. |
| fields | _complex array_ |
| name | DataFlowType |

###### data.modelDefinitions[39].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Data Flow Type. | id | ID! |
| 2 | The label of the Data Flow Type. | label | String! |
| 3 | The description of the Data Flow Type. | description | String! |
| 4 | The ID of the environment where data flows of this type execute. | dataFlowEnvironmentId | ID! |
| 5 | The environment where data flows of this type execute | dataFlowEnvironment | DataFlowEnvironment! |
| 6 | The date and time the record was created. | createdAt | DateTime! |
| 7 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 8 | The User who created this record. | createdByUser | User! |
| 9 | The date and time the record was last updated. | updatedAt | DateTime! |
| 10 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 11 | The User who last updated this record. | updatedByUser | User! |
| 12 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 13 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 14 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataFlowTypeAggregate! |

##### data.modelDefinitions[40]
| Property | Value |
| --- | --- |
| description | The DataFlowVersion model represents a version of an Fuuz data flow, with a unique number and associated flow design. |
| fields | _complex array_ |
| name | DataFlowVersion |

###### data.modelDefinitions[40].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Data Flow Version. | id | ID! |
| 2 | The number of the Data Flow Version. | number | String! |
| 3 | The description of the data flow version. | description | String! |
| 4 | The unique identifier of the data flow this record is a version of. | dataFlowId | ID! |
| 5 | The data flow this record is a version of. | dataFlow | DataFlow! |
| 6 | The JSON containing the data flow, for consumption by the data flow engine. | flow | JSONObject |
| 7 | The JSON containing the data flow diagram, for consumption by the data flow designer. | diagram | JSONObject |
| 8 | The deployments of this data flow version. | deployments | [DataFlowDeployment!] |
| 9 | Whether this data flow version has ever been deployed. | deployed | Boolean! |
| 10 | A relation to the data changes for this document. | dataChanges | [DataFlowVersionDataChange!] |
| 11 | The date and time the record was created. | createdAt | DateTime! |
| 12 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 13 | The User who created this record. | createdByUser | User! |
| 14 | The date and time the record was last updated. | updatedAt | DateTime! |
| 15 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 16 | The User who last updated this record. | updatedByUser | User! |
| 17 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 18 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 19 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataFlowVersionAggregate! |
| 20 | Custom fields configured for the data model. | _customFields | DataFlowVersion_CustomFields! |

##### data.modelDefinitions[41]
| Property | Value |
| --- | --- |
| description | The DataMapping model represents an Fuuz data mapping, used to provide visually-editable transformations. |
| fields | _complex array_ |
| name | DataMapping |

###### data.modelDefinitions[41].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the data mapping. | id | ID! |
| 2 | The name of the data mapping. | name | String! |
| 3 | The description of the data mapping. | description | String! |
| 4 | Whether the data mapping is active and should run once deployed. | active | Boolean! |
| 5 | The versions of this data mapping. | versions | [DataMappingVersion!] |
| 6 | The deployments of this data mapping. | deployments | [DataMappingDeployment!] |
| 7 | The unique identifier of the current deployment of this data mapping. | currentDeploymentId | ID |
| 8 | The current deployment of this data mapping. | currentDeployment | DataMappingDeployment |
| 9 | The unique identifier of the Data Mapping this Data Mapping substitutes. If the current Data Mapping is active and deployed, it will replace the target Data Mappings effectiveDeploymentId with it's currentDeploymentId | substitutesDataMappingId | ID |
| 10 | The relation to the Data Mapping this Data Mapping substitutes. If the current Data Mapping is active and deployed, it will replace the target Data Mappings effectiveDeployment with it's currentDeployment | substitutesDataMapping | DataMapping |
| 11 | The relation to the Data Mapping that substitutes this Data Mapping. If the Data Mapping is active it will replace the current Data Mappings effectiveDeploymentId with it's currentDeploymentId | substitutedByDataMapping | DataMapping |
| 12 | The unique identifier of the Effective Deployment of this Data Mapping. | effectiveDeploymentId | ID |
| 13 | The Effective Deployment of this Data Mapping. | effectiveDeployment | DataMappingDeployment |
| 14 | The input schema of the mapping, synchronized with the current deployment. | inputSchema | JSONObject |
| 15 | The output schema of the mapping, synchronized with the current deployment. | outputSchema | JSONObject |
| 16 | The test cases of this data mapping. | testCases | [DataMappingTestCase!] |
| 17 | The unique identifier of the module this data mapping is part of. | moduleId | ID |
| 18 | The module this data mapping is part of. | module | Module |
| 19 | A relation to the data changes for this document. | dataChanges | [DataMappingDataChange!] |
| 20 | The date and time the record was created. | createdAt | DateTime! |
| 21 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 22 | The User who created this record. | createdByUser | User! |
| 23 | The date and time the record was last updated. | updatedAt | DateTime! |
| 24 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 25 | The User who last updated this record. | updatedByUser | User! |
| 26 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 27 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 28 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataMappingAggregate! |
| 29 | Custom fields configured for the data model. | _customFields | DataMapping_CustomFields! |

##### data.modelDefinitions[42]
| Property | Value |
| --- | --- |
| description | The DataMappingDeployment model represents a deployment of a version of an Fuuz Data Mapping. |
| fields | _complex array_ |
| name | DataMappingDeployment |

###### data.modelDefinitions[42].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the data mapping deployment. | id | ID! |
| 2 | The label of this data mapping deployment <MAPPING NAME>: <VERSION>: <mm-dd-yy hh:mm:ss> | label | String! |
| 3 | The unique identifier of the data mapping this is a deployment of. | dataMappingId | ID! |
| 4 | The data mapping this is a deployment of. | dataMapping | DataMapping! |
| 5 | The unique identifier of the data mapping version this is a deployment of. | dataMappingVersionId | ID! |
| 6 | The data mapping version this is a deployment of. | dataMappingVersion | DataMappingVersion! |
| 7 | The compiled mapping. | mapping | JSONObject! |
| 8 | The input schema defining the shape of the input data to the script. | inputSchema | JSONObject! |
| 9 | The output schema defining the shape of the result of the script. | outputSchema | JSONObject! |
| 10 | The date and time the record was created. | createdAt | DateTime! |
| 11 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 12 | The User who created this record. | createdByUser | User! |
| 13 | The date and time the record was last updated. | updatedAt | DateTime! |
| 14 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 15 | The User who last updated this record. | updatedByUser | User! |
| 16 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 17 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 18 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataMappingDeploymentAggregate! |

##### data.modelDefinitions[43]
| Property | Value |
| --- | --- |
| description | The DataMappingTestCase model represents a saved data mapping test case. |
| fields | _complex array_ |
| name | DataMappingTestCase |

###### data.modelDefinitions[43].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the test case. | id | ID! |
| 2 | The name of the test case. Unique per mapping. | name | String! |
| 3 | The description of the test case. | description | String! |
| 4 | Whether this test case is active and should be used for verifying correctness of the mapping. | active | Boolean! |
| 5 | Optional additional configuration for this test case for consumption n the data mapping designer. | metadata | JSONObject |
| 6 | The payload to be used as input for this test case. | payload | JSON |
| 7 | The expected output for this test case. | result | JSON |
| 8 | The unique identifier of the data mapping this is a test case of. | dataMappingId | ID! |
| 9 | The data mapping this is a test case of. | dataMapping | DataMapping! |
| 10 | The date and time the record was created. | createdAt | DateTime! |
| 11 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 12 | The User who created this record. | createdByUser | User! |
| 13 | The date and time the record was last updated. | updatedAt | DateTime! |
| 14 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 15 | The User who last updated this record. | updatedByUser | User! |
| 16 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 17 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 18 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataMappingTestCaseAggregate! |
| 19 | Custom fields configured for the data model. | _customFields | DataMappingTestCase_CustomFields! |

##### data.modelDefinitions[44]
| Property | Value |
| --- | --- |
| description | The DataMappingVersion model represents a version of a saved data mapping. |
| fields | _complex array_ |
| name | DataMappingVersion |

###### data.modelDefinitions[44].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the data mapping version. | id | ID! |
| 2 | The number of the data mapping version. | number | String! |
| 3 | The description of the data mapping's version. | description | String |
| 4 | The unique identifier of the data mapping this is a version of. | dataMappingId | ID! |
| 5 | The data mapping this is a version of. | dataMapping | DataMapping! |
| 6 | The JSON containing the data mapping, for consumption by the data mapping engine. | mapping | JSONObject |
| 7 | The JSON containing the data mapping diagram, for consumption by the data mapping designer. | diagram | JSONObject |
| 8 | The input schema defining the shape of the input data to the mapping. | inputSchema | JSON! |
| 9 | The output schema defining the shape of the result of the mapping. | outputSchema | JSON! |
| 10 | The deployments of this data mapping version. | deployments | [DataMappingDeployment!] |
| 11 | Whether this data mapping version has ever been deployed. | deployed | Boolean! |
| 12 | A relation to the data changes for this document. | dataChanges | [DataMappingVersionDataChange!] |
| 13 | The date and time the record was created. | createdAt | DateTime! |
| 14 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 15 | The User who created this record. | createdByUser | User! |
| 16 | The date and time the record was last updated. | updatedAt | DateTime! |
| 17 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 18 | The User who last updated this record. | updatedByUser | User! |
| 19 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 20 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 21 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataMappingVersionAggregate! |
| 22 | Custom fields configured for the data model. | _customFields | DataMappingVersion_CustomFields! |

##### data.modelDefinitions[45]
| Property | Value |
| --- | --- |
| description | The DataModel model represents an Fuuz Data Model definition. |
| fields | _complex array_ |
| name | DataModel |

###### data.modelDefinitions[45].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Data Model. | id | ID! |
| 2 | The name of the Data Model. | name | String! |
| 3 | The description of the Data Model. | description | String! |
| 4 | The unique identifier of the kind of the Data Model. | dataModelKindId | ID! |
| 5 | The kind of the Data Model. | dataModelKind | DataModelKind! |
| 6 | The unique identifier of the type of the Data Model. | dataModelTypeId | ID |
| 7 | The type of the Data Model. | dataModelType | DataModelType |
| 8 | The versions of this Data Model. | versions | [DataModelVersion!] |
| 9 | The deployments of this Data Model. | deployments | [DataModelDeployment!] |
| 10 | The unique identifier of the current Deployment of this Data Model. | currentDataModelDeploymentId | ID |
| 11 | The current Deployment of this Data Model. | currentDataModelDeployment | DataModelDeployment |
| 12 | The current Deployment of this Data Model. | currentDeployment | DataModelDeployment |
| 13 | The estimate data size of this Data Model. | estimatedDataBytes | String |
| 14 | The estimated count of records of this Data Model. | estimatedRecordCount | String |
| 15 | The estimate data size of this Data Change Data Model. | estimatedDataChangeDataBytes | String |
| 16 | The estimated count of records of this Data Change Data Model. | estimatedDataChangeRecordCount | String |
| 17 | The ID of the module group the module is in. | moduleId | ID |
| 18 | A relation to the module group the module is in. | module | Module |
| 19 | The normalized elements (fields) of this data model. | elements | [DataModelElement!] |
| 20 | The application component references for which this data model is the source. | sourceApplicationComponentReferences | [ApplicationComponentReference!] |
| 21 | The application component references for which this data model is the target. | targetApplicationComponentReferences | [ApplicationComponentReference!] |
| 22 | The custom fields of this type. | customFields | [DataModelCustomField!] |
| 23 | A relation to the data changes for this document. | dataChanges | [DataModelDataChange!] |
| 24 | The date and time the record was created. | createdAt | DateTime! |
| 25 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 26 | The User who created this record. | createdByUser | User! |
| 27 | The date and time the record was last updated. | updatedAt | DateTime! |
| 28 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 29 | The User who last updated this record. | updatedByUser | User! |
| 30 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 31 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 32 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataModelAggregate! |
| 33 | Custom fields configured for the data model. | _customFields | DataModel_CustomFields! |

##### data.modelDefinitions[46]
| Property | Value |
| --- | --- |
| description | The DataModelCustomField model represents a custom field on an Fuuz data model. |
| fields | _complex array_ |
| name | DataModelCustomField |

###### data.modelDefinitions[46].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Data Model Custom Field. | id | ID! |
| 2 | The label of the custom field. This label will be displayed to users in the UI as input and table column labels. | label | String! |
| 3 | The name of the custom field. This name will be used in the GraphQL API. It should be unique across all custom fields in the same Data Model and cannot be changed after the field is created. | apiFieldName | String! |
| 4 | The description of the Data Model Custom Field. This description will be displayed to users in the UI in field-level help. | description | String! |
| 5 | A explanation of why this field is deprecated. Fields with a deprecation reason set are considered deprecated and should not be used in queries or mutations, but are not removed from the API. | deprecationReason | String |
| 6 | Whether to show this custom field on forms which use the Custom Fields Column screen element. | showOnTables | Boolean! |
| 7 | Whether to show this custom field on forms which use the Custom Fields Input screen element. | showOnForms | Boolean! |
| 8 | The label of the group (if any) to place this custom field in on tables and forms. Fields with the same group label are placed under a shared heading. Groups provide a simple mechanism for organizing custom fields. | groupLabel | String |
| 9 | The name of the Data Model this custom field should appear on. | dataModelName | String! |
| 10 | The Data Model this custom field should appear on. This relation will only resolve if the data model is a custom model. | dataModel | DataModel |
| 11 | The ID of the type of this data model custom field. | dataModelCustomFieldTypeId | ID! |
| 12 | The type of this data model custom field. | dataModelCustomFieldType | DataModelCustomFieldType! |
| 13 | The GraphQL type string used for this field in the GraphQL API. This is set automatically on field creation based on the selected custom field type. | graphQLType | String! |
| 14 | A relation to the data changes for this document. | dataChanges | [DataModelCustomFieldDataChange!] |
| 15 | The date and time the record was created. | createdAt | DateTime! |
| 16 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 17 | The User who created this record. | createdByUser | User! |
| 18 | The date and time the record was last updated. | updatedAt | DateTime! |
| 19 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 20 | The User who last updated this record. | updatedByUser | User! |
| 21 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 22 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 23 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataModelCustomFieldAggregate! |

##### data.modelDefinitions[47]
| Property | Value |
| --- | --- |
| description | The DataModelCustomFieldType model represents the type of an Fuuz data model custom field. |
| fields | _complex array_ |
| name | DataModelCustomFieldType |

###### data.modelDefinitions[47].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Data Model Custom Field Type. | id | ID! |
| 2 | The label of the Data Model Custom Field Type. | label | String! |
| 3 | The description of the Data Model Custom Field Type. | description | String! |
| 4 | The custom fields of this type. | customFields | [DataModelCustomField!] |
| 5 | The date and time the record was created. | createdAt | DateTime! |
| 6 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 7 | The User who created this record. | createdByUser | User! |
| 8 | The date and time the record was last updated. | updatedAt | DateTime! |
| 9 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 10 | The User who last updated this record. | updatedByUser | User! |
| 11 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 12 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 13 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataModelCustomFieldTypeAggregate! |

##### data.modelDefinitions[48]
| Property | Value |
| --- | --- |
| description | The DataModelDeployment model represents a deployment of a version of an Fuuz data model. |
| fields | _complex array_ |
| name | DataModelDeployment |

###### data.modelDefinitions[48].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Data Model Deployment. | id | ID! |
| 2 | The JSON containing the Model definition. | modelDefinition | JSONObject! |
| 3 | The unique identifier of the Data Model this is a deployment of. | dataModelId | ID! |
| 4 | The Data Model this is a deployment of. | dataModel | DataModel! |
| 5 | The unique identifier of the Data Model Version this is a deployment of. | dataModelVersionId | ID! |
| 6 | The Data Model Version this is a deployment of. | dataModelVersion | DataModelVersion! |
| 7 | The batch of Data Model Deployments this deployment was part of. This field is generated as a unique indentifier when deploying a one or more data models. Once all items in the batch complete the deploying process, the GraphQL model map will regenerate to serve the new schema. | dataModelDeploymentBatchId | ID |
| 8 | The unique identifier of the current status for this Data Model Deployment. | dataModelDeploymentStatusId | ID! |
| 9 | A relation to the current status for this Data Model Deployment. | dataModelDeploymentStatus | DataModelDeploymentStatus! |
| 10 | The Fuuz Scripting Language expression to use to transform each record of the Data Model during deployment. | mappingExpression | String! |
| 11 | The unique identifier of the type of the Data Model. | dataModelTypeId | ID |
| 12 | The type of the Data Model. | dataModelType | DataModelType |
| 13 | A relation to the logs for this Data Model Deployment. | logs | [DataModelDeploymentLog]! |
| 14 | A relation to the data changes for this document. | dataChanges | [DataModelDeploymentDataChange!] |
| 15 | The date and time the record was created. | createdAt | DateTime! |
| 16 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 17 | The User who created this record. | createdByUser | User! |
| 18 | The date and time the record was last updated. | updatedAt | DateTime! |
| 19 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 20 | The User who last updated this record. | updatedByUser | User! |
| 21 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 22 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 23 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataModelDeploymentAggregate! |
| 24 | Custom fields configured for the data model. | _customFields | DataModelDeployment_CustomFields! |

##### data.modelDefinitions[49]
| Property | Value |
| --- | --- |
| description | The DataModelDeploymentLog model represents a log tied to a deployment of an Fuuz Data Model. |
| fields | _complex array_ |
| name | DataModelDeploymentLog |

###### data.modelDefinitions[49].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Data Model Tenant Deployment Log. | id | ID! |
| 2 | The unique identifier of the data model tenant deployment this record was logged from. | dataModelDeploymentId | ID! |
| 3 | The data model tenant deployment this record was logged from. | dataModelDeployment | DataModelDeployment! |
| 4 | The unique identifier of the type of this log. | dataModelDeploymentLogTypeId | ID! |
| 5 | The type of this log. | dataModelDeploymentLogType | DataModelDeploymentLogType! |
| 6 | The human-readable message from the log. | message | String! |
| 7 | The JSON containing metadata for this log. | metadata | JSONObject! |
| 8 | The date and time the record was created. | createdAt | DateTime! |
| 9 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 10 | The User who created this record. | createdByUser | User! |
| 11 | The date and time the record was last updated. | updatedAt | DateTime! |
| 12 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 13 | The User who last updated this record. | updatedByUser | User! |
| 14 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 15 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 16 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataModelDeploymentLogAggregate! |

##### data.modelDefinitions[50]
| Property | Value |
| --- | --- |
| description | The DataModelDeploymentLogType model represents the type of an Fuuz data model deployment log. |
| fields | _complex array_ |
| name | DataModelDeploymentLogType |

###### data.modelDefinitions[50].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Data Model Tenant Deployment Log Type. | id | ID! |
| 2 | The label of the Data Model Tenant Deployment Log Type. | label | String! |
| 3 | The description of the Data Model Tenant Deployment Log Type. | description | String! |
| 4 | The date and time the record was created. | createdAt | DateTime! |
| 5 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 6 | The User who created this record. | createdByUser | User! |
| 7 | The date and time the record was last updated. | updatedAt | DateTime! |
| 8 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 9 | The User who last updated this record. | updatedByUser | User! |
| 10 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 11 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 12 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataModelDeploymentLogTypeAggregate! |

##### data.modelDefinitions[51]
| Property | Value |
| --- | --- |
| description | The DataModelDeploymentStatus model represents the status of an Fuuz data model deployment. |
| fields | _complex array_ |
| name | DataModelDeploymentStatus |

###### data.modelDefinitions[51].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Data Model Tenant Deployment Status. | id | ID! |
| 2 | The label of the Data Model Tenant Deployment Status. | label | String! |
| 3 | The description of the Data Model Tenant Deployment Status. | description | String! |
| 4 | The date and time the record was created. | createdAt | DateTime! |
| 5 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 6 | The User who created this record. | createdByUser | User! |
| 7 | The date and time the record was last updated. | updatedAt | DateTime! |
| 8 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 9 | The User who last updated this record. | updatedByUser | User! |
| 10 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 11 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 12 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataModelDeploymentStatusAggregate! |

##### data.modelDefinitions[52]
| Property | Value |
| --- | --- |
| description | The content of a Field in a Data Model. |
| fields | _complex array_ |
| name | DataModelElement |

###### data.modelDefinitions[52].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 | The Id of the field within the model. | elementId | String! |
| 3 | The name of the field. | name | String! |
| 4 | The full type string of the field. | type | String! |
| 5 | The description of the field. | description | String |
| 6 | The full definition of the field. | definition | JSONObject! |
| 7 | The type-specific configuration of the field. | configuration | JSONObject! |
| 8 | The path at which this field can be found within the model definition. | path | String! |
| 9 | The ID of the Data Model that contains this field. | dataModelId | ID! |
| 10 | The Data Model that contains this field. | dataModel | DataModel! |
| 11 | Whether this field is unique within the data model. | unique | Boolean! |
| 12 | Whether this field is the label field for the data model. | labelField | Boolean! |
| 13 | Whether this field is a relation. | relation | Boolean! |
| 14 | The relation metadata for this field, if this field is a relation. | relationFields | DataModelElementRelationFields |
| 15 | The parsed type info for this field. | typeInfo | DataModelElementTypeInfo! |
| 16 | The application component references for which this data model version element is the source. | sourceApplicationComponentReferences | [ApplicationComponentReference!] |
| 17 | The application component references for which this data model version element is the target. | targetApplicationComponentReferences | [ApplicationComponentReference!] |
| 18 | A relation to the data changes for this document. | dataChanges | [DataModelElementDataChange!] |
| 19 | The date and time the record was created. | createdAt | DateTime! |
| 20 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 21 | The User who created this record. | createdByUser | User! |
| 22 | The date and time the record was last updated. | updatedAt | DateTime! |
| 23 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 24 | The User who last updated this record. | updatedByUser | User! |
| 25 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 26 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 27 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataModelElementAggregate! |

##### data.modelDefinitions[53]
| Property | Value |
| --- | --- |
| description | This model represents the relation metadata in a data model field. |
| fields | _complex array_ |
| name | DataModelElementRelationFields |

###### data.modelDefinitions[53].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | When the field type is another model, the 'from' field indicates the field of this model to join to the field of the referenced model. | from | String! |
| 2 | When the type is another model, the 'to' field indicates the field of the referenced model from which this model joins on. | to | String! |
| 3 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataModelElementRelationFieldsAggregate! |

##### data.modelDefinitions[54]
| Property | Value |
| --- | --- |
| description | This model represents the parsed type info for a data model field. |
| fields | _complex array_ |
| name | DataModelElementTypeInfo |

###### data.modelDefinitions[54].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The type base for the field, without modifiers applied. | base | String! |
| 2 | Whether the field type indicates this field is a list. | list | Boolean! |
| 3 | Whether the field type indicates this field is required (non-null). | required | Boolean! |
| 4 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataModelElementTypeInfoAggregate! |

##### data.modelDefinitions[55]
| Property | Value |
| --- | --- |
| description | The DataModelKind model represents the kinds of data models that Fuuz supports. |
| fields | _complex array_ |
| name | DataModelKind |

###### data.modelDefinitions[55].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Data Model Kind. | id | ID! |
| 2 | The label of the Data Model Kind. | label | String! |
| 3 | The description of the Data Model Kind. | description | String! |
| 4 | The date and time the record was created. | createdAt | DateTime! |
| 5 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 6 | The User who created this record. | createdByUser | User! |
| 7 | The date and time the record was last updated. | updatedAt | DateTime! |
| 8 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 9 | The User who last updated this record. | updatedByUser | User! |
| 10 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 11 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 12 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataModelKindAggregate! |

##### data.modelDefinitions[56]
| Property | Value |
| --- | --- |
| description | The DataModelType model represents the type of an Fuuz data model based on its use case |
| fields | _complex array_ |
| name | DataModelType |

###### data.modelDefinitions[56].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Data Model Type. | id | ID! |
| 2 | The name of the Data Model Type. | name | String! |
| 3 | The description of the Data Model Type. | description | String! |
| 4 | The date and time the record was created. | createdAt | DateTime! |
| 5 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 6 | The User who created this record. | createdByUser | User! |
| 7 | The date and time the record was last updated. | updatedAt | DateTime! |
| 8 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 9 | The User who last updated this record. | updatedByUser | User! |
| 10 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 11 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 12 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataModelTypeAggregate! |

##### data.modelDefinitions[57]
| Property | Value |
| --- | --- |
| description | The DataModelVersion model represents a version of an Fuuz Data Model. |
| fields | _complex array_ |
| name | DataModelVersion |

###### data.modelDefinitions[57].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Data Model Version. | id | ID! |
| 2 | The number of the Data Model Version. | number | String! |
| 3 | The description of the data model's version. | description | String |
| 4 | The unique identifier of the Data Model this is a version of. | dataModelId | ID! |
| 5 | The Model this is a version of. | dataModel | DataModel! |
| 6 | The JSON containing the Data Model definition. | modelDefinition | JSONObject |
| 7 | The unique identifier of the type of the Data Model. | dataModelTypeId | ID |
| 8 | The type of the Data Model. | dataModelType | DataModelType |
| 9 | The deployments of this Data Model Version. | deployments | [DataModelDeployment!] |
| 10 | Whether this Data Model Version has ever been deployed. | deployed | Boolean! |
| 11 | A relation to the data changes for this document. | dataChanges | [DataModelVersionDataChange!] |
| 12 | The date and time the record was created. | createdAt | DateTime! |
| 13 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 14 | The User who created this record. | createdByUser | User! |
| 15 | The date and time the record was last updated. | updatedAt | DateTime! |
| 16 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 17 | The User who last updated this record. | updatedByUser | User! |
| 18 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 19 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 20 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DataModelVersionAggregate! |
| 21 | Custom fields configured for the data model. | _customFields | DataModelVersion_CustomFields! |

##### data.modelDefinitions[58]
| Property | Value |
| --- | --- |
| description |  |
| fields | _complex array_ |
| name | Device |

###### data.modelDefinitions[58].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 |  | deviceDriverId | ID! |
| 3 |  | deviceDriver | DeviceDriver! |
| 4 |  | name | String! |
| 5 |  | description | String |
| 6 |  | configuration | JSONObject! |
| 7 |  | active | Boolean! |
| 8 |  | deviceGateways | [DeviceGatewayDevice!]! |
| 9 | The number of seconds to wait between scheduled checks of device status. | statusSyncIntervalSeconds | Int |
| 10 |  | statusOk | Boolean |
| 11 |  | statusMessage | String |
| 12 |  | statusInfo | JSONObject |
| 13 |  | statusLastUpdatedAt | DateTime |
| 14 | A relation to the data changes for this document. | dataChanges | [DeviceDataChange!] |
| 15 | The date and time the record was created. | createdAt | DateTime! |
| 16 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 17 | The User who created this record. | createdByUser | User! |
| 18 | The date and time the record was last updated. | updatedAt | DateTime! |
| 19 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 20 | The User who last updated this record. | updatedByUser | User! |
| 21 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 22 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 23 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DeviceAggregate! |
| 24 | Custom fields configured for the data model. | _customFields | Device_CustomFields! |

##### data.modelDefinitions[59]
| Property | Value |
| --- | --- |
| description |  |
| fields | _complex array_ |
| name | DeviceDriver |

###### data.modelDefinitions[59].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 |  | deviceDriverTypeId | ID! |
| 3 |  | deviceDriverType | DeviceDriverType! |
| 4 |  | name | String! |
| 5 |  | description | String! |
| 6 |  | configurationSchema | JSONObject! |
| 7 |  | devices | [Device!]! |
| 8 | The date and time the record was created. | createdAt | DateTime! |
| 9 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 10 | The User who created this record. | createdByUser | User! |
| 11 | The date and time the record was last updated. | updatedAt | DateTime! |
| 12 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 13 | The User who last updated this record. | updatedByUser | User! |
| 14 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 15 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 16 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DeviceDriverAggregate! |

##### data.modelDefinitions[60]
| Property | Value |
| --- | --- |
| description |  |
| fields | _complex array_ |
| name | DeviceDriverType |

###### data.modelDefinitions[60].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 |  | name | String! |
| 3 |  | description | String! |
| 4 |  | deviceDriverTypeFunctions | [DeviceDriverTypeFunction!]! |
| 5 |  | deviceDrivers | [DeviceDriver!]! |
| 6 | The date and time the record was created. | createdAt | DateTime! |
| 7 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 8 | The User who created this record. | createdByUser | User! |
| 9 | The date and time the record was last updated. | updatedAt | DateTime! |
| 10 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 11 | The User who last updated this record. | updatedByUser | User! |
| 12 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 13 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 14 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DeviceDriverTypeAggregate! |

##### data.modelDefinitions[61]
| Property | Value |
| --- | --- |
| description |  |
| fields | _complex array_ |
| name | DeviceDriverTypeDeviceSubscriptionType |

###### data.modelDefinitions[61].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 | null | deviceDriverTypeId | ID! |
| 3 |  | deviceDriverType | DeviceDriverType! |
| 4 | null | deviceSubscriptionTypeId | ID! |
| 5 |  | deviceSubscriptionType | DeviceSubscriptionType! |
| 6 | A relation to the data changes for this document. | dataChanges | [DeviceDriverTypeDeviceSubscriptionTypeDataChange!] |
| 7 | The date and time the record was created. | createdAt | DateTime! |
| 8 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 9 | The User who created this record. | createdByUser | User! |
| 10 | The date and time the record was last updated. | updatedAt | DateTime! |
| 11 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 12 | The User who last updated this record. | updatedByUser | User! |
| 13 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 14 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 15 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DeviceDriverTypeDeviceSubscriptionTypeAggregate! |

##### data.modelDefinitions[62]
| Property | Value |
| --- | --- |
| description |  |
| fields | _complex array_ |
| name | DeviceDriverTypeFunction |

###### data.modelDefinitions[62].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 |  | deviceDriverTypeId | ID! |
| 3 |  | deviceDriverType | DeviceDriverType! |
| 4 |  | label | String! |
| 5 |  | name | String! |
| 6 |  | description | String! |
| 7 |  | requestSchema | JSONObject! |
| 8 |  | responseSchema | JSONObject! |
| 9 |  | defaultFunctionTimeoutSeconds | Int! |
| 10 | The date and time the record was created. | createdAt | DateTime! |
| 11 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 12 | The User who created this record. | createdByUser | User! |
| 13 | The date and time the record was last updated. | updatedAt | DateTime! |
| 14 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 15 | The User who last updated this record. | updatedByUser | User! |
| 16 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 17 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 18 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DeviceDriverTypeFunctionAggregate! |

##### data.modelDefinitions[63]
| Property | Value |
| --- | --- |
| description |  |
| fields | _complex array_ |
| name | DeviceGateway |

###### data.modelDefinitions[63].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 |  | name | String! |
| 3 |  | hostname | String! |
| 4 |  | applicationVersion | String! |
| 5 |  | description | String |
| 6 |  | macAddress | String |
| 7 |  | ipAddress | String |
| 8 |  | configuration | JSONObject! |
| 9 |  | devices | [DeviceGatewayDevice!]! |
| 10 | The number of seconds to wait between scheduled checks of device status. | statusSyncIntervalSeconds | Int |
| 11 |  | statusOk | Boolean |
| 12 |  | statusMessage | String |
| 13 |  | statusInfo | JSONObject |
| 14 |  | statusLastUpdatedAt | DateTime |
| 15 |  | hasUpdates | Boolean |
| 16 | Whether this device gateway has an active subscription socket. | socketActive | Boolean |
| 17 | The last time socketActive was updated | socketActiveLastUpdatedAt | DateTime |
| 18 | A relation to the data changes for this document. | dataChanges | [DeviceGatewayDataChange!] |
| 19 | The date and time the record was created. | createdAt | DateTime! |
| 20 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 21 | The User who created this record. | createdByUser | User! |
| 22 | The date and time the record was last updated. | updatedAt | DateTime! |
| 23 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 24 | The User who last updated this record. | updatedByUser | User! |
| 25 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 26 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 27 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DeviceGatewayAggregate! |
| 28 | Custom fields configured for the data model. | _customFields | DeviceGateway_CustomFields! |

##### data.modelDefinitions[64]
| Property | Value |
| --- | --- |
| description |  |
| fields | _complex array_ |
| name | DeviceGatewayDataFlow |

###### data.modelDefinitions[64].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record. Every model should have an id field. | id | ID! |
| 2 |  | deviceGatewayId | ID! |
| 3 |  | dataFlowId | ID! |
| 4 |  | deviceGateway | DeviceGateway! |
| 5 |  | dataFlow | DataFlow! |
| 6 |  | label | String |
| 7 | A relation to the data changes for this document. | dataChanges | [DeviceGatewayDataFlowDataChange!] |
| 8 | The date and time the record was created. | createdAt | DateTime! |
| 9 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 10 | The User who created this record. | createdByUser | User! |
| 11 | The date and time the record was last updated. | updatedAt | DateTime! |
| 12 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 13 | The User who last updated this record. | updatedByUser | User! |
| 14 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 15 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 16 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DeviceGatewayDataFlowAggregate! |
| 17 | Custom fields configured for the data model. | _customFields | DeviceGatewayDataFlow_CustomFields! |

##### data.modelDefinitions[65]
| Property | Value |
| --- | --- |
| description |  |
| fields | _complex array_ |
| name | DeviceGatewayDevice |

###### data.modelDefinitions[65].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 |  | deviceGatewayId | ID! |
| 3 |  | deviceId | ID! |
| 4 |  | deviceGateway | DeviceGateway! |
| 5 |  | device | Device! |
| 6 |  | statusOk | Boolean |
| 7 |  | statusMessage | String |
| 8 |  | statusInfo | JSONObject |
| 9 |  | statusLastUpdatedAt | DateTime |
| 10 |  | label | String |
| 11 |  | deviceSubscriptions | [DeviceSubscription] |
| 12 | A relation to the data changes for this document. | dataChanges | [DeviceGatewayDeviceDataChange!] |
| 13 | The date and time the record was created. | createdAt | DateTime! |
| 14 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 15 | The User who created this record. | createdByUser | User! |
| 16 | The date and time the record was last updated. | updatedAt | DateTime! |
| 17 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 18 | The User who last updated this record. | updatedByUser | User! |
| 19 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 20 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 21 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DeviceGatewayDeviceAggregate! |
| 22 | Custom fields configured for the data model. | _customFields | DeviceGatewayDevice_CustomFields! |

##### data.modelDefinitions[66]
| Property | Value |
| --- | --- |
| description |  |
| fields | _complex array_ |
| name | DeviceGatewayFunction |

###### data.modelDefinitions[66].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 |  | label | String! |
| 3 |  | name | String! |
| 4 |  | description | String! |
| 5 |  | requestSchema | JSONObject! |
| 6 |  | responseSchema | JSONObject! |
| 7 |  | defaultFunctionTimeoutSeconds | Int! |
| 8 | The date and time the record was created. | createdAt | DateTime! |
| 9 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 10 | The User who created this record. | createdByUser | User! |
| 11 | The date and time the record was last updated. | updatedAt | DateTime! |
| 12 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 13 | The User who last updated this record. | updatedByUser | User! |
| 14 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 15 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 16 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DeviceGatewayFunctionAggregate! |

##### data.modelDefinitions[67]
| Property | Value |
| --- | --- |
| description |  |
| fields | _complex array_ |
| name | DeviceSubscription |

###### data.modelDefinitions[67].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 |  | name | String! |
| 3 |  | description | String |
| 4 |  | fullyQualifiedTopicName | String |
| 5 |  | configuration | JSONObject! |
| 6 | null | deviceGatewayDeviceId | ID! |
| 7 |  | deviceGatewayDevice | DeviceGatewayDevice! |
| 8 | null | deviceSubscriptionTypeId | ID! |
| 9 |  | deviceSubscriptionType | DeviceSubscriptionType! |
| 10 |  | updateFrequencySeconds | Int! |
| 11 | A relation to the data changes for this document. | dataChanges | [DeviceSubscriptionDataChange!] |
| 12 | The date and time the record was created. | createdAt | DateTime! |
| 13 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 14 | The User who created this record. | createdByUser | User! |
| 15 | The date and time the record was last updated. | updatedAt | DateTime! |
| 16 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 17 | The User who last updated this record. | updatedByUser | User! |
| 18 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 19 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 20 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DeviceSubscriptionAggregate! |
| 21 | Custom fields configured for the data model. | _customFields | DeviceSubscription_CustomFields! |

##### data.modelDefinitions[68]
| Property | Value |
| --- | --- |
| description |  |
| fields | _complex array_ |
| name | DeviceSubscriptionType |

###### data.modelDefinitions[68].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 |  | label | String! |
| 3 |  | configurationSchema | JSONObject! |
| 4 |  | dataSchema | JSONObject! |
| 5 | A relation to the data changes for this document. | dataChanges | [DeviceSubscriptionTypeDataChange!] |
| 6 | The date and time the record was created. | createdAt | DateTime! |
| 7 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 8 | The User who created this record. | createdByUser | User! |
| 9 | The date and time the record was last updated. | updatedAt | DateTime! |
| 10 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 11 | The User who last updated this record. | updatedByUser | User! |
| 12 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 13 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 14 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DeviceSubscriptionTypeAggregate! |

##### data.modelDefinitions[69]
| Property | Value |
| --- | --- |
| description | The DocumentDesign model represents the design of a printed document in Fuuz, such as a label or invoice. |
| fields | _complex array_ |
| name | DocumentDesign |

###### data.modelDefinitions[69].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the document design. | id | ID! |
| 2 | The name of the document design. | name | String! |
| 3 | A description of the document design. | description | String! |
| 4 | Whether the document design is active and should be available to render. | active | Boolean! |
| 5 | The unique identifier of the current deployment of this document design. | currentDocumentDesignDeploymentId | ID |
| 6 | The current deployment of this document design. | currentDocumentDesignDeployment | DocumentDesignDeployment |
| 7 | The unique identifier of the current deployment of this document design. | currentDeploymentId | ID |
| 8 | The current deployment of this document design. | currentDeployment | DocumentDesignDeployment |
| 9 | The unique identifier of the Effective Deployment of this document design. | effectiveDeploymentId | ID |
| 10 | The Effective Deployment of this document design. | effectiveDeployment | DocumentDesignDeployment |
| 11 | The unique identifier of the Document Design this Document Design substitutes. If the current Document Design is active and deployed, it will replace the target Saved Transforms effectiveDeploymentId with it's currentDeploymentId | substitutesDocumentDesignId | ID |
| 12 | The relation to the Document Design this Document Design substitutes. If the current Document Design is active and deployed, it will replace the target Saved Transforms effectiveDeployment with it's currentDeployment | substitutesDocumentDesign | DocumentDesign |
| 13 | The relation to the Document Design that substitutes this Document Design. If the Document Design is active it will replace the current Saved Transforms effectiveDeploymentId with it's currentDeploymentId | substitutedByDocumentDesign | DocumentDesign |
| 14 | The unique identifier of the primary data flow used to generate this document. | primaryGenerationDataFlowId | ID |
| 15 | The primary data flow used to generate this document. | primaryGenerationDataFlow | DataFlow |
| 16 | The list of versions for this document design. | documentDesignVersions | [DocumentDesignVersion!] |
| 17 | The list of deployments for this document design. | documentDesignDeployments | [DocumentDesignDeployment!] |
| 18 | The list of saved sample payloads for this document design version. | documentDesignPayloads | [DocumentDesignPayload!] |
| 19 | The list of rendered documents created from this document design. | documentRenders | [DocumentRender!] |
| 20 | A relation to the data changes for this document. | dataChanges | [DocumentDesignDataChange!] |
| 21 | The date and time the record was created. | createdAt | DateTime! |
| 22 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 23 | The User who created this record. | createdByUser | User! |
| 24 | The date and time the record was last updated. | updatedAt | DateTime! |
| 25 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 26 | The User who last updated this record. | updatedByUser | User! |
| 27 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 28 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 29 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DocumentDesignAggregate! |
| 30 | Custom fields configured for the data model. | _customFields | DocumentDesign_CustomFields! |

##### data.modelDefinitions[70]
| Property | Value |
| --- | --- |
| description | The DocumentDesignDeployment model represents a deployment of a version of an Fuuz document design. |
| fields | _complex array_ |
| name | DocumentDesignDeployment |

###### data.modelDefinitions[70].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the document design deployment. | id | ID! |
| 2 | The JSON data defining the document design. | design | JSONObject! |
| 3 | The ID of the document design this record is a deployment of. | documentDesignId | ID! |
| 4 | The document design this record is a deployment of. | documentDesign | DocumentDesign! |
| 5 | The ID of the document design version this record is a deployment of. | documentDesignVersionId | ID! |
| 6 | The document design version this record is a deployment of. | documentDesignVersion | DocumentDesignVersion! |
| 7 | The list of rendered documents created from this document design deployment. | documentRenders | [DocumentRender!] |
| 8 | The date and time the record was created. | createdAt | DateTime! |
| 9 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 10 | The User who created this record. | createdByUser | User! |
| 11 | The date and time the record was last updated. | updatedAt | DateTime! |
| 12 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 13 | The User who last updated this record. | updatedByUser | User! |
| 14 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 15 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 16 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DocumentDesignDeploymentAggregate! |

##### data.modelDefinitions[71]
| Property | Value |
| --- | --- |
| description | The DocumentDesignPayload model represents a saved sample payload input to a document design. |
| fields | _complex array_ |
| name | DocumentDesignPayload |

###### data.modelDefinitions[71].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the document payload. | id | ID! |
| 2 | The name of the document payload. | name | String! |
| 3 | The content of the document payload. | payload | JSONObject! |
| 4 | The ID of the Document Design to which this payload belongs. | documentDesignId | ID! |
| 5 | The Document Design to which this payload belongs. | documentDesign | DocumentDesign! |
| 6 | A relation to the data changes for this document. | dataChanges | [DocumentDesignPayloadDataChange!] |
| 7 | The date and time the record was created. | createdAt | DateTime! |
| 8 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 9 | The User who created this record. | createdByUser | User! |
| 10 | The date and time the record was last updated. | updatedAt | DateTime! |
| 11 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 12 | The User who last updated this record. | updatedByUser | User! |
| 13 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 14 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 15 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DocumentDesignPayloadAggregate! |
| 16 | Custom fields configured for the data model. | _customFields | DocumentDesignPayload_CustomFields! |

##### data.modelDefinitions[72]
| Property | Value |
| --- | --- |
| description | The DocumentDesignVersion model represents a version of an Fuuz document design. |
| fields | _complex array_ |
| name | DocumentDesignVersion |

###### data.modelDefinitions[72].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the document design version. | id | ID! |
| 2 | The number of this document design version. | number | String! |
| 3 | A description of this version of the document design. | description | String! |
| 4 | The JSON data defining the document design. | design | JSONObject |
| 5 | The ID of the document design this record is a version of. | documentDesignId | ID! |
| 6 | The document design this record is a version of. | documentDesign | DocumentDesign! |
| 7 | The list of rendered documents created from this document design version. | documentRenders | [DocumentRender!] |
| 8 | The list of deployments for this document design version. | documentDesignDeployments | [DocumentDesignDeployment!] |
| 9 | Whether this document design version has ever been deployed. | deployed | Boolean! |
| 10 | A relation to the data changes for this document. | dataChanges | [DocumentDesignVersionDataChange!] |
| 11 | The date and time the record was created. | createdAt | DateTime! |
| 12 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 13 | The User who created this record. | createdByUser | User! |
| 14 | The date and time the record was last updated. | updatedAt | DateTime! |
| 15 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 16 | The User who last updated this record. | updatedByUser | User! |
| 17 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 18 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 19 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DocumentDesignVersionAggregate! |
| 20 | Custom fields configured for the data model. | _customFields | DocumentDesignVersion_CustomFields! |

##### data.modelDefinitions[73]
| Property | Value |
| --- | --- |
| description | The DocumentRender model represents a rendered instance of a document design. |
| fields | _complex array_ |
| name | DocumentRender |

###### data.modelDefinitions[73].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the rendered document. | id | ID! |
| 2 | The filename of the rendered document. | name | String! |
| 3 | The ID of the document design used to render this document. | documentDesignId | ID! |
| 4 | The document design used to render this document. | documentDesign | DocumentDesign! |
| 5 | The ID of the document design version used to render this document. | documentDesignVersionId | ID! |
| 6 | The document design version used to render this document. | documentDesignVersion | DocumentDesignVersion! |
| 7 | The ID of the document design deployment used to render this document. | documentDesignDeploymentId | ID! |
| 8 | The document design deployment used to render this document. | documentDesignDeployment | DocumentDesignDeployment! |
| 9 | The ID of the File which contains the rendered document. | fileId | ID |
| 10 | The File which contains the rendered document. | file | File |
| 11 | The payload used to render this instance of the document. | payload | JSONObject |
| 12 | The culture applied to the document upon generation. | culture | String! |
| 13 | Additional metadata about this instance of the rendered document. | metadata | JSONObject! |
| 14 | The ID of the format of the generated document. | documentRenderFormatId | ID! |
| 15 | The format of the generated document. | documentRenderFormat | DocumentRenderFormat! |
| 16 | A relation to the data changes for this document. | dataChanges | [DocumentRenderDataChange!] |
| 17 | The date and time the record was created. | createdAt | DateTime! |
| 18 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 19 | The User who created this record. | createdByUser | User! |
| 20 | The date and time the record was last updated. | updatedAt | DateTime! |
| 21 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 22 | The User who last updated this record. | updatedByUser | User! |
| 23 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 24 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 25 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DocumentRenderAggregate! |
| 26 | Custom fields configured for the data model. | _customFields | DocumentRender_CustomFields! |

##### data.modelDefinitions[74]
| Property | Value |
| --- | --- |
| description | The DocumentRenderFormat model represents the output type of a rendered document. |
| fields | _complex array_ |
| name | DocumentRenderFormat |

###### data.modelDefinitions[74].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the format. | id | ID! |
| 2 | The unique label of the format. | label | String! |
| 3 | The description of the format. | description | String! |
| 4 | The MIME type of documents rendered in this format. | mimeType | String! |
| 5 | The file extension of documents rendered in this format. | extension | String! |
| 6 | A relation to the data changes for this document. | dataChanges | [DocumentRenderFormatDataChange!] |
| 7 | The date and time the record was created. | createdAt | DateTime! |
| 8 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 9 | The User who created this record. | createdByUser | User! |
| 10 | The date and time the record was last updated. | updatedAt | DateTime! |
| 11 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 12 | The User who last updated this record. | updatedByUser | User! |
| 13 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 14 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 15 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | DocumentRenderFormatAggregate! |
| 16 | Custom fields configured for the data model. | _customFields | DocumentRenderFormat_CustomFields! |

##### data.modelDefinitions[75]
| Property | Value |
| --- | --- |
| description | The export configuration model repesents the configurations for exports. |
| fields | _complex array_ |
| name | ExportConfiguration |

###### data.modelDefinitions[75].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the export configuration. | id | ID! |
| 2 | The description of the export configuration. | description | RichText |
| 3 | The label for the export configuration. | label | String! |
| 4 | The query string used in the configuration to query data. | query | String! |
| 5 | JSON object containing the parameters used to filter the queried data to export. | parameters | JSONObject! |
| 6 | JSON object containing the configuration for export format. | exportFormatConfiguration | JSONObject! |
| 7 | Data Model from which the data is exported. | dataModel | String! |
| 8 | The frequency to the run the export. | frequency | String |
| 9 | The timezone to run the export configuration schedule. | timeZone | TimeZone |
| 10 | ID of the related export format. | exportFormatId | ID! |
| 11 | Relation to the export format for the configuration. | exportFormat | ExportFormat! |
| 12 | List of export histories related to an export configuration. | exportHistories | [ExportHistory!]! |
| 13 | A relation to the data changes for this document. | dataChanges | [ExportConfigurationDataChange!] |
| 14 | The date and time the record was created. | createdAt | DateTime! |
| 15 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 16 | The User who created this record. | createdByUser | User! |
| 17 | The date and time the record was last updated. | updatedAt | DateTime! |
| 18 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 19 | The User who last updated this record. | updatedByUser | User! |
| 20 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 21 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 22 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ExportConfigurationAggregate! |
| 23 | Custom fields configured for the data model. | _customFields | ExportConfiguration_CustomFields! |

##### data.modelDefinitions[76]
| Property | Value |
| --- | --- |
| description | The export format model represents the formats for exports. |
| fields | _complex array_ |
| name | ExportFormat |

###### data.modelDefinitions[76].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the export format. | id | ID! |
| 2 | Description for the export format. | description | String! |
| 3 | Label of the export format. | label | String! |
| 4 | JSON object containing the configuration schema of the export format. | configurationSchema | JSONObject! |
| 5 | List of export configurations related to a format. | exportConfigurations | [ExportConfiguration!]! |
| 6 | The date and time the record was created. | createdAt | DateTime! |
| 7 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 8 | The User who created this record. | createdByUser | User! |
| 9 | The date and time the record was last updated. | updatedAt | DateTime! |
| 10 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 11 | The User who last updated this record. | updatedByUser | User! |
| 12 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 13 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 14 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ExportFormatAggregate! |

##### data.modelDefinitions[77]
| Property | Value |
| --- | --- |
| description | The export history model represents the export operation. |
| fields | _complex array_ |
| name | ExportHistory |

###### data.modelDefinitions[77].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | A unique identifier for an export history. | id | ID! |
| 2 | The label for the export history. | label | String |
| 3 | The description for the export history. | description | RichText |
| 4 | The query string used for the export run. | query | String |
| 5 | Count of the actual number of rows being exported. | processedRowCount | Int |
| 6 | Count of the total number of rows exported for the run. | estimatedTotalRowCount | Int |
| 7 | The estimated file size for the run in number of bytes. | estimatedFileSize | Int |
| 8 | JSON object containing the parameters used to filter the queried data to export. | parameters | JSONObject |
| 9 | The error message encountered during the export run, if any. This provides information about issues that occurred. | errorMessage | String |
| 10 | JSON object containing the error data from the execution. | error | JSONObject |
| 11 | The identifier for the export configuration associated with this run. | exportConfigurationId | ID |
| 12 | The export configuration used for this run. | exportConfiguration | ExportConfiguration |
| 13 | The identifier for the status of the export run. | exportStatusId | ID! |
| 14 | The current status of the export run, providing details about the progress or completion state of the export process. | exportStatus | ExportStatus! |
| 15 | Data Model from which the data is exported. | dataModel | String |
| 16 | ID of the related export format. | exportFormatId | ID |
| 17 | Relation to the export format for the configuration. | exportFormat | ExportFormat |
| 18 | The file that contains the url to the export history data. | exportHistoryFile | ExportHistoryFile |
| 19 | The date and time the record was created. | createdAt | DateTime! |
| 20 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 21 | The User who created this record. | createdByUser | User! |
| 22 | The date and time the record was last updated. | updatedAt | DateTime! |
| 23 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 24 | The User who last updated this record. | updatedByUser | User! |
| 25 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 26 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 27 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ExportHistoryAggregate! |
| 28 | Custom fields configured for the data model. | _customFields | ExportHistory_CustomFields! |

##### data.modelDefinitions[78]
| Property | Value |
| --- | --- |
| description | This model represents the file info for an export history record. |
| fields | _complex array_ |
| name | ExportHistoryFile |

###### data.modelDefinitions[78].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Export History File. | id | ID! |
| 2 | The name of the Export History File. | name | String! |
| 3 | This URL will change every time the export history file is queried and is only valid for a short time. The expiration time is configurable using the expiresInSeconds parameter and defaults to 15 minutes. | url | String! |
| 4 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ExportHistoryFileAggregate! |

##### data.modelDefinitions[79]
| Property | Value |
| --- | --- |
| description | The export status model represents the status of the exports. |
| fields | _complex array_ |
| name | ExportStatus |

###### data.modelDefinitions[79].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the export status. | id | ID! |
| 2 | The description for the export status. | description | String! |
| 3 | The label of the export status. | label | String! |
| 4 |  | exportHistories | [ExportHistory!]! |
| 5 | The date and time the record was created. | createdAt | DateTime! |
| 6 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 7 | The User who created this record. | createdByUser | User! |
| 8 | The date and time the record was last updated. | updatedAt | DateTime! |
| 9 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 10 | The User who last updated this record. | updatedByUser | User! |
| 11 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 12 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 13 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ExportStatusAggregate! |

##### data.modelDefinitions[80]
| Property | Value |
| --- | --- |
| description | An external application to be used for configuration with Fuuz. |
| fields | _complex array_ |
| name | ExternalApplication |

###### data.modelDefinitions[80].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The id of the external application. | id | ID! |
| 2 | The name of the external application. | name | String! |
| 3 | The unique identifier for the default external application configuration for this external application. | defaultConfigurationId | ID |
| 4 | The relation to the external application configuration that is set as the default for this external application. | defaultConfiguration | ExternalApplicationConfiguration |
| 5 | The list of external application configurations for this external application. | configurations | [ExternalApplicationConfiguration] |
| 6 | A relation to the data changes for this document. | dataChanges | [ExternalApplicationDataChange!] |
| 7 | The date and time the record was created. | createdAt | DateTime! |
| 8 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 9 | The User who created this record. | createdByUser | User! |
| 10 | The date and time the record was last updated. | updatedAt | DateTime! |
| 11 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 12 | The User who last updated this record. | updatedByUser | User! |
| 13 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 14 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 15 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ExternalApplicationAggregate! |
| 16 | Custom fields configured for the data model. | _customFields | ExternalApplication_CustomFields! |

##### data.modelDefinitions[81]
| Property | Value |
| --- | --- |
| description | A configuration to be used for an external application with Fuuz. |
| fields | _complex array_ |
| name | ExternalApplicationConfiguration |

###### data.modelDefinitions[81].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The id for this external application configuration. | id | ID! |
| 2 | The name for this external application configuration. | name | String! |
| 3 | The ID of the external application that goes to this configuration. | externalApplicationId | ID! |
| 4 | The relation to the external application. | externalApplication | ExternalApplication! |
| 5 | A json object containing the configuration for the external application. | configuration | JSONObject! |
| 6 | The list of users using this external application configuration. | users | [UserExternalApplicationConfiguration] |
| 7 | The list of roles using this external application configuration. | roles | [RoleExternalApplicationConfiguration] |
| 8 | A relation to the data changes for this document. | dataChanges | [ExternalApplicationConfigurationDataChange!] |
| 9 | The date and time the record was created. | createdAt | DateTime! |
| 10 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 11 | The User who created this record. | createdByUser | User! |
| 12 | The date and time the record was last updated. | updatedAt | DateTime! |
| 13 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 14 | The User who last updated this record. | updatedByUser | User! |
| 15 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 16 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 17 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ExternalApplicationConfigurationAggregate! |
| 18 | Custom fields configured for the data model. | _customFields | ExternalApplicationConfiguration_CustomFields! |

##### data.modelDefinitions[82]
| Property | Value |
| --- | --- |
| description | The File type represents a file stored within Fuuz. |
| fields | _complex array_ |
| name | File |

###### data.modelDefinitions[82].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the File. | id | ID! |
| 2 | The name of the File. | name | String! |
| 3 | The encoding of the File. | encoding | String! |
| 4 | The MIME type of the File. | mimeType | String! |
| 5 | This URL will change every time the image is queried and is only valid for a short time. The expiration time is configurable using the expiresInSeconds parameter and defaults to 15 minutes. | url | String! |
| 6 | The content of the File as a string. The string must be encoded with the encoding of the file. | content | String! |
| 7 | A relation to the data changes for this document. | dataChanges | [FileDataChange!] |
| 8 | The date and time the record was created. | createdAt | DateTime! |
| 9 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 10 | The User who created this record. | createdByUser | User! |
| 11 | The date and time the record was last updated. | updatedAt | DateTime! |
| 12 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 13 | The User who last updated this record. | updatedByUser | User! |
| 14 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 15 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 16 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | FileAggregate! |
| 17 | Custom fields configured for the data model. | _customFields | File_CustomFields! |

##### data.modelDefinitions[83]
| Property | Value |
| --- | --- |
| description | The Image type represents an image stored within Fuuz |
| fields | _complex array_ |
| name | Image |

###### data.modelDefinitions[83].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Image. | id | ID! |
| 2 | The file name of the Image. | name | String! |
| 3 | This URL will change every time the image is queried and is only valid for a short time. The expiration time is configurable using the expiresInSeconds parameter and defaults to 15 minutes. | url | String! |
| 4 | The Base-64 encoded content of the Image. | content | String! |
| 5 | The MIME type of the Image. | mimeType | String! |
| 6 | This URL will change every time the image is queried and is only valid for a short time. The expiration time is configurable using the expiresInSeconds parameter and defaults to 15 minutes. | thumbnailUrl | String! |
| 7 | A relation to the data changes for this document. | dataChanges | [ImageDataChange!] |
| 8 | The date and time the record was created. | createdAt | DateTime! |
| 9 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 10 | The User who created this record. | createdByUser | User! |
| 11 | The date and time the record was last updated. | updatedAt | DateTime! |
| 12 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 13 | The User who last updated this record. | updatedByUser | User! |
| 14 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 15 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 16 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ImageAggregate! |
| 17 | Custom fields configured for the data model. | _customFields | Image_CustomFields! |

##### data.modelDefinitions[84]
| Property | Value |
| --- | --- |
| description | The InstalledPackage model represents an installed Fuuz package. |
| fields | _complex array_ |
| name | InstalledPackage |

###### data.modelDefinitions[84].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the InstalledPackage. | id | ID! |
| 2 | The name of the installed package. | name | String! |
| 3 | The semantic version of the installed package. | version | String! |
| 4 | The description of the Package. | description | RichText |
| 5 | A relation to the InstalledPackages of this package's dependencies. | dependencyPackages | [InstalledPackageDependency!] |
| 6 | Whether or not this package should remain installed if its last dependent is uninstalled. | standalone | Boolean! |
| 7 | The unique identifier of the current installation of this package. | currentInstallationId | ID |
| 8 | The current installation of this package. | currentInstallation | PackageInstallation |
| 9 | Installations of this package. | packageInstallations | [PackageInstallation!] |
| 10 | The unique identifier of the Application Definition Installation this is part from. | applicationDefinitionInstallationId | ID |
| 11 | The unique identifier of the Application Definition Installation this is part from. | applicationDefinitionVersionInstallationId | ID |
| 12 | The date and time the record was created. | createdAt | DateTime! |
| 13 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 14 | The User who created this record. | createdByUser | User! |
| 15 | The date and time the record was last updated. | updatedAt | DateTime! |
| 16 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 17 | The User who last updated this record. | updatedByUser | User! |
| 18 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 19 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 20 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | InstalledPackageAggregate! |
| 21 | Custom fields configured for the data model. | _customFields | InstalledPackage_CustomFields! |

##### data.modelDefinitions[85]
| Property | Value |
| --- | --- |
| description | The InstalledPackageDependency model represents one InstalledPackage's dependency on another. |
| fields | _complex array_ |
| name | InstalledPackageDependency |

###### data.modelDefinitions[85].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the InstalledPackageDependency. | id | ID! |
| 2 | The unique identifier of the InstalledPackage which depends on the other. | dependentPackageId | ID! |
| 3 | The InstalledPackage which depends on the other. | dependentPackage | InstalledPackage! |
| 4 | The unique identifier of the InstalledPackage which the other depends on. | dependencyPackageId | ID! |
| 5 | The InstalledPackage which the other depends on. | dependencyPackage | InstalledPackage! |
| 6 | The date and time the record was created. | createdAt | DateTime! |
| 7 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 8 | The User who created this record. | createdByUser | User! |
| 9 | The date and time the record was last updated. | updatedAt | DateTime! |
| 10 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 11 | The User who last updated this record. | updatedByUser | User! |
| 12 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 13 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 14 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | InstalledPackageDependencyAggregate! |
| 15 | Custom fields configured for the data model. | _customFields | InstalledPackageDependency_CustomFields! |

##### data.modelDefinitions[86]
| Property | Value |
| --- | --- |
| description |  |
| fields | _complex array_ |
| name | IntegrationRequest |

###### data.modelDefinitions[86].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 |  | integrationRequestStatusId | ID |
| 3 |  | sourceName | String! |
| 4 |  | sourceDataType | String! |
| 5 |  | destinationName | String! |
| 6 |  | destinationDataType | String! |
| 7 |  | completedAt | DateTime |
| 8 |  | integrationRequestStatus | IntegrationRequestStatus |
| 9 |  | sourceMetadata | JSONObject! |
| 10 |  | destinationMetadata | JSONObject! |
| 11 |  | metadata | JSONObject |
| 12 | A relation to the data changes for this document. | dataChanges | [IntegrationRequestDataChange!] |
| 13 | The date and time the record was created. | createdAt | DateTime! |
| 14 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 15 | The User who created this record. | createdByUser | User! |
| 16 | The date and time the record was last updated. | updatedAt | DateTime! |
| 17 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 18 | The User who last updated this record. | updatedByUser | User! |
| 19 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 20 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 21 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | IntegrationRequestAggregate! |
| 22 | Custom fields configured for the data model. | _customFields | IntegrationRequest_CustomFields! |

##### data.modelDefinitions[87]
| Property | Value |
| --- | --- |
| description | The IntegrationRequestLog model represents a log tied to a request made to the fuuz integration service. |
| fields | _complex array_ |
| name | IntegrationRequestLog |

###### data.modelDefinitions[87].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Integration Request Log. | id | ID! |
| 2 | When Return Errors is true, even if an error is encountered it will return as a success. | returnErrors | Boolean |
| 3 | Defines how many requests can execute concurrently. | degreeOfParallelism | Int |
| 4 | The key on the object the request was nested under. For example, in the context of Data Flow executions to the integration service these will be the NodeID. | requestName | String |
| 5 | The number of requests in the payload object. This indicates how many requests the connector made out. | numberOfRequests | Int |
| 6 | The internal Fuuz request ID for the integration request | requestId | String |
| 7 | The Id of the User that made the integration request. | executedByUserId | ID! |
| 8 | The User that made the integration request. | executedByUser | User |
| 9 | The unique identifier of the connection that was used to make this integration request. | connectionId | ID! |
| 10 | The connection that was used to make this integration request. | connection | Connection |
| 11 | The name of the connection used for integration request. | connectionName | String! |
| 12 | The connection Url used for the integration request. | connectionUrl | String! |
| 13 | The timestamp of when the request was made. | requestTimestamp | DateTime! |
| 14 | The request payload of the integration request. This payload may be incomplete if the request/response is too large. | requestPayload | JSON |
| 15 | The response payload of the integration request. This payload may be incomplete if the request/response is too large. | responsePayload | JSON |
| 16 | The timestamp of when the response was made. | responseTimestamp | DateTime |
| 17 | The total time the request took in milseconds. | responseTime | Float |
| 18 | The error message generated from this request. | error | String |
| 19 | The date and time the record was created. | createdAt | DateTime! |
| 20 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 21 | The User who created this record. | createdByUser | User! |
| 22 | The date and time the record was last updated. | updatedAt | DateTime! |
| 23 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 24 | The User who last updated this record. | updatedByUser | User! |
| 25 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 26 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 27 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | IntegrationRequestLogAggregate! |

##### data.modelDefinitions[88]
| Property | Value |
| --- | --- |
| description | The Integration Request Status model holds all possible statuses of Integration Requests. |
| fields | _complex array_ |
| name | IntegrationRequestStatus |

###### data.modelDefinitions[88].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 |  | label | String! |
| 3 |  | description | String |
| 4 | A relation to the data changes for this document. | dataChanges | [IntegrationRequestStatusDataChange!] |
| 5 | The date and time the record was created. | createdAt | DateTime! |
| 6 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 7 | The User who created this record. | createdByUser | User! |
| 8 | The date and time the record was last updated. | updatedAt | DateTime! |
| 9 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 10 | The User who last updated this record. | updatedByUser | User! |
| 11 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 12 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 13 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | IntegrationRequestStatusAggregate! |

##### data.modelDefinitions[89]
| Property | Value |
| --- | --- |
| description | The Measure model represents a value with a unit of measurement, such as a quantity. |
| fields | _complex array_ |
| name | Measure |

###### data.modelDefinitions[89].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | null | value | Float! |
| 2 | null | unitId | ID! |
| 3 | A relation to the Unit of this Measurement. | unit | Unit! |
| 4 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | MeasureAggregate! |

##### data.modelDefinitions[90]
| Property | Value |
| --- | --- |
| description | The Module type represents a grouping of related functionality in Fuuz. |
| fields | _complex array_ |
| name | Module |

###### data.modelDefinitions[90].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Module. | id | ID! |
| 2 | The friendly name of the module. | name | String! |
| 3 | The description of the module. | description | String! |
| 4 | The flag to indicate if the modules is system generated or custom . | system | Boolean! |
| 5 | The ID of the module group the module is in. | moduleGroupId | ID! |
| 6 | A relation to the module group the module is in. | moduleGroup | ModuleGroup! |
| 7 | A relation to the data changes for this document. | dataChanges | [ModuleDataChange!] |
| 8 | The date and time the record was created. | createdAt | DateTime! |
| 9 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 10 | The User who created this record. | createdByUser | User! |
| 11 | The date and time the record was last updated. | updatedAt | DateTime! |
| 12 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 13 | The User who last updated this record. | updatedByUser | User! |
| 14 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 15 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 16 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ModuleAggregate! |
| 17 | Custom fields configured for the data model. | _customFields | Module_CustomFields! |

##### data.modelDefinitions[91]
| Property | Value |
| --- | --- |
| description | The ModuleGroup type represents a module group, grouping together related modules in Fuuz. |
| fields | _complex array_ |
| name | ModuleGroup |

###### data.modelDefinitions[91].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the ModuleGroup. | id | ID! |
| 2 | The friendly name of the module group. | name | String! |
| 3 | The icon for the module group. | icon | String! |
| 4 | The description of the module group. | description | String! |
| 5 | The flag to indicate if the module group is system generated or custom . | system | Boolean! |
| 6 | A relation to the modules in this module group. | modules | [Module!] |
| 7 | A relation to the data changes for this document. | dataChanges | [ModuleGroupDataChange!] |
| 8 | The date and time the record was created. | createdAt | DateTime! |
| 9 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 10 | The User who created this record. | createdByUser | User! |
| 11 | The date and time the record was last updated. | updatedAt | DateTime! |
| 12 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 13 | The User who last updated this record. | updatedByUser | User! |
| 14 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 15 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 16 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ModuleGroupAggregate! |
| 17 | Custom fields configured for the data model. | _customFields | ModuleGroup_CustomFields! |

##### data.modelDefinitions[92]
| Property | Value |
| --- | --- |
| description |  |
| fields | _complex array_ |
| name | NotificationChannel |

###### data.modelDefinitions[92].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 |  | name | String! |
| 3 |  | description | String |
| 4 |  | bindingKey | String |
| 5 | The recipient relation model. | recipients | [NotificationChannelRecipient] |
| 6 | If the default email should be sent out for this channel. Turn this off if you are going to use a flow to handle communication. | sendEmail | Boolean |
| 7 | The inputSchema provides dynamic inputs on forms and validation for a configuration. | inputSchema | JSONObject! |
| 8 | A template to be sent out to all recipents. | emailTemplate | String |
| 9 | A relation to the data changes for this document. | dataChanges | [NotificationChannelDataChange!] |
| 10 | The date and time the record was created. | createdAt | DateTime! |
| 11 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 12 | The User who created this record. | createdByUser | User! |
| 13 | The date and time the record was last updated. | updatedAt | DateTime! |
| 14 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 15 | The User who last updated this record. | updatedByUser | User! |
| 16 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 17 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 18 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | NotificationChannelAggregate! |
| 19 | Custom fields configured for the data model. | _customFields | NotificationChannel_CustomFields! |

##### data.modelDefinitions[93]
| Property | Value |
| --- | --- |
| description |  |
| fields | _complex array_ |
| name | NotificationChannelRecipient |

###### data.modelDefinitions[93].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 | The ID of the Notification channel. | notificationChannelId | ID! |
| 3 | The notification channel relation model. | notificationChannel | NotificationChannel |
| 4 | The ID of the recipient. | recipientId | ID! |
| 5 | The recipient relation model. | recipient | Recipient |
| 6 | A relation to the data changes for this document. | dataChanges | [NotificationChannelRecipientDataChange!] |
| 7 | The date and time the record was created. | createdAt | DateTime! |
| 8 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 9 | The User who created this record. | createdByUser | User! |
| 10 | The date and time the record was last updated. | updatedAt | DateTime! |
| 11 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 12 | The User who last updated this record. | updatedByUser | User! |
| 13 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 14 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 15 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | NotificationChannelRecipientAggregate! |
| 16 | Custom fields configured for the data model. | _customFields | NotificationChannelRecipient_CustomFields! |

##### data.modelDefinitions[94]
| Property | Value |
| --- | --- |
| description |  |
| fields | _complex array_ |
| name | NotificationCommunication |

###### data.modelDefinitions[94].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 | The number of the notification. | number | String |
| 3 | The ID of the Notification channel. | notificationChannelId | ID! |
| 4 | The notification channel relation model. | notificationChannel | NotificationChannel |
| 5 | When the notification was sent. | occurredAt | DateTime! |
| 6 | The content of the notification. | message | String |
| 7 | All the recipients this notification was sent to. | recipients | [String] |
| 8 | A relation to the data changes for this document. | dataChanges | [NotificationCommunicationDataChange!] |
| 9 | The date and time the record was created. | createdAt | DateTime! |
| 10 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 11 | The User who created this record. | createdByUser | User! |
| 12 | The date and time the record was last updated. | updatedAt | DateTime! |
| 13 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 14 | The User who last updated this record. | updatedByUser | User! |
| 15 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 16 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 17 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | NotificationCommunicationAggregate! |

##### data.modelDefinitions[95]
| Property | Value |
| --- | --- |
| description | The PackageBuilderState model represents a package being built with the PackageBuilder tool. |
| fields | _complex array_ |
| name | PackageBuilderState |

###### data.modelDefinitions[95].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the package builder state. | id | ID! |
| 2 | The unique name of the package being built. | packageName | String! |
| 3 | The Package Builder's state. | editorState | JSONObject! |
| 4 | A relation to the data changes for this document. | dataChanges | [PackageBuilderStateDataChange!] |
| 5 | The date and time the record was created. | createdAt | DateTime! |
| 6 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 7 | The User who created this record. | createdByUser | User! |
| 8 | The date and time the record was last updated. | updatedAt | DateTime! |
| 9 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 10 | The User who last updated this record. | updatedByUser | User! |
| 11 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 12 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 13 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | PackageBuilderStateAggregate! |
| 14 | Custom fields configured for the data model. | _customFields | PackageBuilderState_CustomFields! |

##### data.modelDefinitions[96]
| Property | Value |
| --- | --- |
| description | The PackageDefinitionVersion model represents the definition of a Fuuz package. |
| fields | _complex array_ |
| name | PackageDefinition |

###### data.modelDefinitions[96].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the package definition. | id | ID! |
| 2 | The name of the package definition. | name | String! |
| 3 | The description of the package definition. | description | RichText |
| 4 | The versions of this package definition. | versions | [PackageDefinitionVersion!] |
| 5 | The unique identifier of the Application Definition Installation ID the package was installed from | applicationDefinitionInstallationId | ID |
| 6 | The unique identifier of the Application Definition the package was installed from or published as | applicationDefinitionId | ID |
| 7 | The unique identifier of the Application Definition Type | applicationDefinitionTypeId | ID |
| 8 | The unique identifier of the Application Publisher | applicationPublisherId | ID |
| 9 | The unique identifier for the Application Definition Category. | applicationDefinitionCategoryId | ID |
| 10 | A list of tags that are used for keyword searching in the Application Store. | tags | [String!] |
| 11 | A relation to the data changes for this document. | dataChanges | [PackageDefinitionDataChange!] |
| 12 | The date and time the record was created. | createdAt | DateTime! |
| 13 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 14 | The User who created this record. | createdByUser | User! |
| 15 | The date and time the record was last updated. | updatedAt | DateTime! |
| 16 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 17 | The User who last updated this record. | updatedByUser | User! |
| 18 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 19 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 20 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | PackageDefinitionAggregate! |
| 21 | Custom fields configured for the data model. | _customFields | PackageDefinition_CustomFields! |

##### data.modelDefinitions[97]
| Property | Value |
| --- | --- |
| description | The PackageDefinitionSelection model represents a data selection on the definition of a Fuuz package in the editor. |
| fields | _complex array_ |
| name | PackageDefinitionSelection |

###### data.modelDefinitions[97].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the PackageDefinitionSelection. | id | ID! |
| 2 | The unique identifier of the package definition version this record belongs to. | packageDefinitionVersionId | ID! |
| 3 | The package definition version this record belongs to. | packageDefinitionVersion | PackageDefinitionVersion! |
| 4 | The API against which this selection is being made. | api | String! |
| 5 | The name of the DataModel from which this selection is being made. | modelName | String! |
| 6 | The list of selected records. | selected | JSON! |
| 7 | The list of selected fields. | fields | JSON! |
| 8 | Any additional data on a selection. | metadata | JSON! |
| 9 | The date and time the record was created. | createdAt | DateTime! |
| 10 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 11 | The User who created this record. | createdByUser | User! |
| 12 | The date and time the record was last updated. | updatedAt | DateTime! |
| 13 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 14 | The User who last updated this record. | updatedByUser | User! |
| 15 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 16 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 17 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | PackageDefinitionSelectionAggregate! |
| 18 | Custom fields configured for the data model. | _customFields | PackageDefinitionSelection_CustomFields! |

##### data.modelDefinitions[98]
| Property | Value |
| --- | --- |
| description | The PackageDefinitionVersion model represents the definition of a Fuuz package in the editor. |
| fields | _complex array_ |
| name | PackageDefinitionVersion |

###### data.modelDefinitions[98].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the PackageDefinitionVersion. | id | ID! |
| 2 | The semantic version of the package version being defined. | version | String! |
| 3 | The description of the package version being defined. | description | RichText |
| 4 | A relation to the selections in this package definition version. | selections | [PackageDefinitionSelection]! |
| 5 | The JSON containing metadata for the package definition version. | metadata | JSONObject |
| 6 | The unique identifier of the package definition this record is a version of. | packageDefinitionId | ID! |
| 7 | The package definition this record is a version of. | packageDefinition | PackageDefinition! |
| 8 | Whether the package definition is in read-only mode. | readOnly | Boolean |
| 9 | The unique identifier of the File record representing this package definition's .fuuz archive. Once created, the definition is locked. | exportedFileId | ID |
| 10 | The File record representing this package definition's .fuuz archive. Once created, the definition is locked. | exportedFile | File |
| 11 | The Application Definition Version the package was published as or installed from | applicationDefinitionVersionId | ID |
| 12 | The Application Definition Deployment ID the package was installed from | applicationDefinitionVersionDeploymentId | ID |
| 13 | The unique identifer of the Application Definition Version Installation ID | applicationDefinitionVersionInstallationId | ID |
| 14 | The date and time the record was created. | createdAt | DateTime! |
| 15 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 16 | The User who created this record. | createdByUser | User! |
| 17 | The date and time the record was last updated. | updatedAt | DateTime! |
| 18 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 19 | The User who last updated this record. | updatedByUser | User! |
| 20 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 21 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 22 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | PackageDefinitionVersionAggregate! |
| 23 | Custom fields configured for the data model. | _customFields | PackageDefinitionVersion_CustomFields! |

##### data.modelDefinitions[99]
| Property | Value |
| --- | --- |
| description | The PackageInstallation model represents the installation process of a Fuuz package. |
| fields | _complex array_ |
| name | PackageInstallation |

###### data.modelDefinitions[99].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the PackageInstallation. | id | ID! |
| 2 | The name of the package being installed. | name | String! |
| 3 | The semantic version of the package being installed. | version | String! |
| 4 | The description of the package being installed. | description | RichText |
| 5 | A hash of package name to version range, describing the packages that this package depends on. | dependencies | JSONObject! |
| 6 | The URL from which the package was originally installed. | sourceURL | String! |
| 7 | The unique identifier of the File record representing this installation's .fuuz archive. | sourceFileId | ID |
| 8 | The File record representing this installation's .fuuz archive. | sourceFile | File |
| 9 | The unique identifier of the package header this installation goes on. | installedPackageId | ID! |
| 10 | The package header this installation goes on. | installedPackage | InstalledPackage! |
| 11 | The unique identifier of this package's installation stage. | packageInstallationStageId | ID! |
| 12 | This package's installation stage. | packageInstallationStage | PackageInstallationStage! |
| 13 | A relation to the logs for this Package Installation. | logs | [PackageInstallationLog]! |
| 14 | A relation to the tasks performed by this Package Installation. | tasks | [PackageInstallationTask]! |
| 15 | The unique identifier of the Application Definition Installation this originates from. | applicationDefinitionInstallationId | ID |
| 16 | The unique identifier of the Application Definition Version Installation this originates from. | applicationDefinitionVersionInstallationId | ID |
| 17 | The date and time the record was created. | createdAt | DateTime! |
| 18 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 19 | The User who created this record. | createdByUser | User! |
| 20 | The date and time the record was last updated. | updatedAt | DateTime! |
| 21 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 22 | The User who last updated this record. | updatedByUser | User! |
| 23 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 24 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 25 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | PackageInstallationAggregate! |
| 26 | Custom fields configured for the data model. | _customFields | PackageInstallation_CustomFields! |

##### data.modelDefinitions[100]
| Property | Value |
| --- | --- |
| description | The PackageInstallationLog model represents a log tied to an installation of a Fuuz package. |
| fields | _complex array_ |
| name | PackageInstallationLog |

###### data.modelDefinitions[100].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Package Installation Log. | id | ID! |
| 2 | The unique identifier of the package installation this record was logged from. | packageInstallationId | ID! |
| 3 | The package installation this record was logged from. | packageInstallation | PackageInstallation! |
| 4 | The unique identifier of the type of this log. | packageInstallationLogTypeId | ID! |
| 5 | The type of this log. | packageInstallationLogType | PackageInstallationLogType! |
| 6 | The human-readable message from the log. | message | String! |
| 7 | The JSON containing metadata for this log. | metadata | JSONObject! |
| 8 | The date and time the record was created. | createdAt | DateTime! |
| 9 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 10 | The User who created this record. | createdByUser | User! |
| 11 | The date and time the record was last updated. | updatedAt | DateTime! |
| 12 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 13 | The User who last updated this record. | updatedByUser | User! |
| 14 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 15 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 16 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | PackageInstallationLogAggregate! |
| 17 | Custom fields configured for the data model. | _customFields | PackageInstallationLog_CustomFields! |

##### data.modelDefinitions[101]
| Property | Value |
| --- | --- |
| description | The PackageInstallationLogType model represents the type of a Fuuz package installation log. |
| fields | _complex array_ |
| name | PackageInstallationLogType |

###### data.modelDefinitions[101].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Package Installation Log Type. | id | ID! |
| 2 | The label of the Package Installation Log Type. | label | String! |
| 3 | The description of the Package Installation Log Type. | description | String! |
| 4 | The date and time the record was created. | createdAt | DateTime! |
| 5 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 6 | The User who created this record. | createdByUser | User! |
| 7 | The date and time the record was last updated. | updatedAt | DateTime! |
| 8 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 9 | The User who last updated this record. | updatedByUser | User! |
| 10 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 11 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 12 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | PackageInstallationLogTypeAggregate! |

##### data.modelDefinitions[102]
| Property | Value |
| --- | --- |
| description | The PackageInstallationStage model represents the stage of a Fuuz package installation. |
| fields | _complex array_ |
| name | PackageInstallationStage |

###### data.modelDefinitions[102].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Package Installation Stage. | id | ID! |
| 2 | The label of the Package Installation Stage. | label | String! |
| 3 | The description of the Package Installation Stage. | description | String! |
| 4 | The date and time the record was created. | createdAt | DateTime! |
| 5 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 6 | The User who created this record. | createdByUser | User! |
| 7 | The date and time the record was last updated. | updatedAt | DateTime! |
| 8 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 9 | The User who last updated this record. | updatedByUser | User! |
| 10 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 11 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 12 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | PackageInstallationStageAggregate! |

##### data.modelDefinitions[103]
| Property | Value |
| --- | --- |
| description | The PackageInstallationTask model represents a task performed while installing a package. |
| fields | _complex array_ |
| name | PackageInstallationTask |

###### data.modelDefinitions[103].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Package Installation Task. | id | ID! |
| 2 | The unique identifier of the package installation this task was performed for. | packageInstallationId | ID! |
| 3 | The package installation this task was performed for. | packageInstallation | PackageInstallation! |
| 4 | The unique identifier of the type of this package installation task. | packageInstallationTaskTypeId | ID! |
| 5 | The type of this package installation task. | packageInstallationTaskType | PackageInstallationTaskType! |
| 6 | The unique identifier of the status of this package installation task. | packageInstallationTaskStatusId | ID! |
| 7 | The status of this package installation task. | packageInstallationTaskStatus | PackageInstallationTaskStatus! |
| 8 | The JSON representation of the task performed. | task | JSONObject |
| 9 | A relation to the data changes for this document. | dataChanges | [PackageInstallationTaskDataChange!] |
| 10 | The date and time the record was created. | createdAt | DateTime! |
| 11 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 12 | The User who created this record. | createdByUser | User! |
| 13 | The date and time the record was last updated. | updatedAt | DateTime! |
| 14 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 15 | The User who last updated this record. | updatedByUser | User! |
| 16 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 17 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 18 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | PackageInstallationTaskAggregate! |
| 19 | Custom fields configured for the data model. | _customFields | PackageInstallationTask_CustomFields! |

##### data.modelDefinitions[104]
| Property | Value |
| --- | --- |
| description | The PackageInstallationTaskStatus model represents the status of a Fuuz package installation task. |
| fields | _complex array_ |
| name | PackageInstallationTaskStatus |

###### data.modelDefinitions[104].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Package Installation Task Status. | id | ID! |
| 2 | The label of the Package Installation Task Status. | label | String! |
| 3 | The description of the Package Installation Task Status. | description | String! |
| 4 | The date and time the record was created. | createdAt | DateTime! |
| 5 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 6 | The User who created this record. | createdByUser | User! |
| 7 | The date and time the record was last updated. | updatedAt | DateTime! |
| 8 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 9 | The User who last updated this record. | updatedByUser | User! |
| 10 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 11 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 12 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | PackageInstallationTaskStatusAggregate! |

##### data.modelDefinitions[105]
| Property | Value |
| --- | --- |
| description | The PackageInstallationTaskType model represents the type of a Fuuz package installation task. |
| fields | _complex array_ |
| name | PackageInstallationTaskType |

###### data.modelDefinitions[105].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Package Installation Task Type. | id | ID! |
| 2 | The label of the Package Installation Task Type. | label | String! |
| 3 | The description of the Package Installation Task Type. | description | String! |
| 4 | The date and time the record was created. | createdAt | DateTime! |
| 5 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 6 | The User who created this record. | createdByUser | User! |
| 7 | The date and time the record was last updated. | updatedAt | DateTime! |
| 8 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 9 | The User who last updated this record. | updatedByUser | User! |
| 10 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 11 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 12 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | PackageInstallationTaskTypeAggregate! |

##### data.modelDefinitions[106]
| Property | Value |
| --- | --- |
| description | The RatioMeasure model represents a proportional relationship between two measures. |
| fields | _complex array_ |
| name | RatioMeasure |

###### data.modelDefinitions[106].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The identifier of the numerator unit. | numeratorUnitId | ID! |
| 2 | The numerator unit. | numeratorUnit | Unit! |
| 3 | The identifier of the denominator unit. | denominatorUnitId | ID! |
| 4 | The denominator unit. | denominatorUnit | Unit! |
| 5 | The value of the numerator of the ratio. | numeratorValue | Float! |
| 6 | The value of the denominator of the ratio. | denominatorValue | Float! |
| 7 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | RatioMeasureAggregate! |

##### data.modelDefinitions[107]
| Property | Value |
| --- | --- |
| description |  |
| fields | _complex array_ |
| name | Recipient |

###### data.modelDefinitions[107].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 |  | name | String! |
| 3 | The ID of the user to send notifications to. | userId | ID |
| 4 | The user relation model. | user | User |
| 5 | The ID of the role to send notifications to. | roleId | ID |
| 6 | The role relation model. | role | Role |
| 7 | A list of email addresses for non-Fuuz recipients. | emailAddresses | [String] |
| 8 | A relation to the data changes for this document. | dataChanges | [RecipientDataChange!] |
| 9 | The date and time the record was created. | createdAt | DateTime! |
| 10 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 11 | The User who created this record. | createdByUser | User! |
| 12 | The date and time the record was last updated. | updatedAt | DateTime! |
| 13 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 14 | The User who last updated this record. | updatedByUser | User! |
| 15 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 16 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 17 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | RecipientAggregate! |
| 18 | Custom fields configured for the data model. | _customFields | Recipient_CustomFields! |

##### data.modelDefinitions[108]
| Property | Value |
| --- | --- |
| description | The Role type represents an access control role that can be applied to types within Fuuz. |
| fields | _complex array_ |
| name | Role |

###### data.modelDefinitions[108].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Role. | id | ID! |
| 2 | The name of the Role. | name | String! |
| 3 | A description of the role. | description | String! |
| 4 | The role-based menu associated with this role. | menu | JSONObject |
| 5 | A relation to the users assigned to this Role | userRoles | [UserRole!] |
| 6 | A relation to the value of settings assigned to this Role | roleSettingValues | [RoleSettingValue!] |
| 7 | The unique identifier of the Home Screen. | homeScreenId | ID |
| 8 | A relation to the value of the Home Screen assigned to this Role. | homeScreen | Screen |
| 9 | The AccessControlPolicyGroups to which this role is assigned. | groups | [RoleAccessControlPolicyGroup!] |
| 10 | A relation to the data changes for this document. | dataChanges | [RoleDataChange!] |
| 11 | The date and time the record was created. | createdAt | DateTime! |
| 12 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 13 | The User who created this record. | createdByUser | User! |
| 14 | The date and time the record was last updated. | updatedAt | DateTime! |
| 15 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 16 | The User who last updated this record. | updatedByUser | User! |
| 17 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 18 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 19 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | RoleAggregate! |
| 20 | Custom fields configured for the data model. | _customFields | Role_CustomFields! |

##### data.modelDefinitions[109]
| Property | Value |
| --- | --- |
| description | This model represents a link between Roles and AccessControlPolicyGroups, defining which groups are assigned of which roles. |
| fields | _complex array_ |
| name | RoleAccessControlPolicyGroup |

###### data.modelDefinitions[109].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the RoleAccessControlPolicyGroup. | id | ID! |
| 2 | The unique identifier of the AccessControlPolicyGroup assigned to the Role. | accessControlPolicyGroupId | ID! |
| 3 | The AccessControlPolicyGroup assigned to the Role. | accessControlPolicyGroup | AccessControlPolicyGroup! |
| 4 | The unique identifier of the Role the AccessControlPolicyGroup is assigned to. | roleId | ID! |
| 5 | The Role to which the AccessControlPolicyGroup is assigned to. | role | Role! |
| 6 | A relation to the data changes for this document. | dataChanges | [RoleAccessControlPolicyGroupDataChange!] |
| 7 | The date and time the record was created. | createdAt | DateTime! |
| 8 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 9 | The User who created this record. | createdByUser | User! |
| 10 | The date and time the record was last updated. | updatedAt | DateTime! |
| 11 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 12 | The User who last updated this record. | updatedByUser | User! |
| 13 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 14 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 15 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | RoleAccessControlPolicyGroupAggregate! |
| 16 | Custom fields configured for the data model. | _customFields | RoleAccessControlPolicyGroup_CustomFields! |

##### data.modelDefinitions[110]
| Property | Value |
| --- | --- |
| description | An external application role to be used for configuration with Fuuz. |
| fields | _complex array_ |
| name | RoleExternalApplicationConfiguration |

###### data.modelDefinitions[110].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The id for this role external application configuration. | id | ID! |
| 2 | The ID of the role linking to an external application that goes to this configuration. | roleId | ID! |
| 3 | The relation to the role for this external application configuration. | role | Role! |
| 4 | The ID of the external application configuration for this user configuration. | externalApplicationConfigurationId | ID! |
| 5 | The relation to the external application configuration for this user configuration. | externalApplicationConfiguration | ExternalApplicationConfiguration! |
| 6 | A relation to the data changes for this document. | dataChanges | [RoleExternalApplicationConfigurationDataChange!] |
| 7 | The date and time the record was created. | createdAt | DateTime! |
| 8 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 9 | The User who created this record. | createdByUser | User! |
| 10 | The date and time the record was last updated. | updatedAt | DateTime! |
| 11 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 12 | The User who last updated this record. | updatedByUser | User! |
| 13 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 14 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 15 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | RoleExternalApplicationConfigurationAggregate! |
| 16 | Custom fields configured for the data model. | _customFields | RoleExternalApplicationConfiguration_CustomFields! |

##### data.modelDefinitions[111]
| Property | Value |
| --- | --- |
| description | The value of a setting for a role. |
| fields | _complex array_ |
| name | RoleSettingValue |

###### data.modelDefinitions[111].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of this RoleSettingValue. | id | ID! |
| 2 | The ID of the role this setting value is linked to. | roleId | ID! |
| 3 | The associated role. | role | Role! |
| 4 | The ID of the setting this role is linked to. | settingId | ID! |
| 5 | The Setting. | setting | Setting! |
| 6 | The value associated with this setting. | value | JSON! |
| 7 | A relation to the data changes for this document. | dataChanges | [RoleSettingValueDataChange!] |
| 8 | The date and time the record was created. | createdAt | DateTime! |
| 9 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 10 | The User who created this record. | createdByUser | User! |
| 11 | The date and time the record was last updated. | updatedAt | DateTime! |
| 12 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 13 | The User who last updated this record. | updatedByUser | User! |
| 14 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 15 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 16 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | RoleSettingValueAggregate! |
| 17 | Custom fields configured for the data model. | _customFields | RoleSettingValue_CustomFields! |

##### data.modelDefinitions[112]
| Property | Value |
| --- | --- |
| description | The Route type represents the URL paths through which a screen is accessible in the Fuuz frontend. |
| fields | _complex array_ |
| name | Route |

###### data.modelDefinitions[112].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the route. | id | ID! |
| 2 | The title of the route. | title | String! |
| 3 | The URL path of the route, including any URL parameters. | path | String! |
| 4 | The unique identifier of the module this route is part of. | moduleId | ID! |
| 5 | The module this route is part of. | module | Module! |
| 6 | The unique identifier of the screen exposed through this route. | screenId | ID! |
| 7 | The screen exposed through this route. | screen | Screen! |
| 8 | Whether this route should appear in the system menu and menu search.  Typically, table- and dashboard-style screens are menu accessible, while form-style screens are not. | isMenuAccessible | Boolean! |
| 9 | The submenu to place this route in if the route is menu accessible. | submenu | String! |
| 10 | Whether the screen appears in full screen mode or not. | isFullscreen | Boolean! |
| 11 | A relation to the data changes for this document. | dataChanges | [RouteDataChange!] |
| 12 | The date and time the record was created. | createdAt | DateTime! |
| 13 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 14 | The User who created this record. | createdByUser | User! |
| 15 | The date and time the record was last updated. | updatedAt | DateTime! |
| 16 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 17 | The User who last updated this record. | updatedByUser | User! |
| 18 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 19 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 20 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | RouteAggregate! |
| 21 | Custom fields configured for the data model. | _customFields | Route_CustomFields! |

##### data.modelDefinitions[113]
| Property | Value |
| --- | --- |
| description | The SavedQuery model represent a saved graphQL statement. |
| fields | _complex array_ |
| name | SavedQuery |

###### data.modelDefinitions[113].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Saved Query. | id | ID! |
| 2 | The name of the Saved Query. | name | String! |
| 3 | The description of the Saved Query. | description | String! |
| 4 | The text of the Saved Query. | queryText | String! |
| 5 | The unique identifier of this record's Saved Query Operation Type. | savedQueryOperationTypeId | ID! |
| 6 | A relation to the Saved Query Operation Type of this record. | savedQueryOperationType | SavedQueryOperationType! |
| 7 | A list of the parameters that serve as input for this Saved Query. | savedQueryParameters | [SavedQueryParameter!] |
| 8 | The sample variables used when editing the Saved Query in the API Explorer. | sampleVariables | JSONObject |
| 9 | A relation to the data changes for this document. | dataChanges | [SavedQueryDataChange!] |
| 10 | The date and time the record was created. | createdAt | DateTime! |
| 11 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 12 | The User who created this record. | createdByUser | User! |
| 13 | The date and time the record was last updated. | updatedAt | DateTime! |
| 14 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 15 | The User who last updated this record. | updatedByUser | User! |
| 16 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 17 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 18 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | SavedQueryAggregate! |
| 19 | Custom fields configured for the data model. | _customFields | SavedQuery_CustomFields! |

##### data.modelDefinitions[114]
| Property | Value |
| --- | --- |
| description | The SavedQueryOperationType represents the type of operation for a Saved Query within Fuuz. |
| fields | _complex array_ |
| name | SavedQueryOperationType |

###### data.modelDefinitions[114].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Saved Query Operation Type. | id | ID! |
| 2 | The label of the Saved Query Operation Type. | label | String! |
| 3 | The description of the Saved Query Operation Type. | description | String! |
| 4 | A relation to the data changes for this document. | dataChanges | [SavedQueryOperationTypeDataChange!] |
| 5 | The date and time the record was created. | createdAt | DateTime! |
| 6 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 7 | The User who created this record. | createdByUser | User! |
| 8 | The date and time the record was last updated. | updatedAt | DateTime! |
| 9 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 10 | The User who last updated this record. | updatedByUser | User! |
| 11 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 12 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 13 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | SavedQueryOperationTypeAggregate! |
| 14 | Custom fields configured for the data model. | _customFields | SavedQueryOperationType_CustomFields! |

##### data.modelDefinitions[115]
| Property | Value |
| --- | --- |
| description | The SavedQueryParameter type represents a parameter that is an input to a Saved Query in Fuuz. |
| fields | _complex array_ |
| name | SavedQueryParameter |

###### data.modelDefinitions[115].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The name of the Saved Query. | name | String! |
| 2 | The type of the parameter. | typeName | String! |
| 3 | Whether or not this parameter is required. | required | Boolean! |
| 4 | Whether or not this parameter represents a list of input values. | list | Boolean! |
| 5 | The default value of the parameter. | defaultValue | JSON |
| 6 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | SavedQueryParameterAggregate! |

##### data.modelDefinitions[116]
| Property | Value |
| --- | --- |
| description | The SavedTransform model represents a saved Fuuz Scripting Language transform, as well as information about the transform's input and output. |
| fields | _complex array_ |
| name | SavedTransform |

###### data.modelDefinitions[116].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the saved script. | id | ID! |
| 2 | The name of the saved script. | name | String! |
| 3 | The description of the saved script. | description | String! |
| 4 | The saved script of the current saved script deployment | transform | String |
| 5 | The input schema of the current saved script deployment | inputSchema | JSON |
| 6 | The output schema of the current saved script deployment | outputSchema | JSON |
| 7 | The unique identifier of the script language for the current saved script deployment | scriptLanguageId | ID |
| 8 | The script language of the current saved script deployment | scriptLanguage | ScriptLanguage! |
| 9 | The versions of this saved script. | versions | [SavedTransformVersion!] |
| 10 | The deployments of this saved script. | deployments | [SavedTransformDeployment!] |
| 11 | The unique identifier of the current Deployment of this saved script. | currentDeploymentId | ID |
| 12 | The current Deployment of this saved script. | currentDeployment | SavedTransformDeployment |
| 13 | The unique identifier of the Saved Transform this Saved Transform substitutes. If the current Saved Transform is active and deployed, it will replace the target Saved Transforms effectiveDeploymentId with it's currentDeploymentId | substitutesSavedTransformId | ID |
| 14 | The relation to the Saved Transform this Saved Transform substitutes. If the current Saved Transform is active and deployed, it will replace the target Saved Transforms effectiveDeployment with it's currentDeployment | substitutesSavedTransform | SavedTransform |
| 15 | The relation to the Saved Transform that substitutes this Saved Transform. If the Saved Transform is active it will replace the current Saved Transforms effectiveDeploymentId with it's currentDeploymentId | substitutedBySavedTransform | SavedTransform |
| 16 | The unique identifier of the Effective Deployment of this saved script. | effectiveDeploymentId | ID |
| 17 | The Effective Deployment of this saved script. | effectiveDeployment | SavedTransformDeployment |
| 18 | Whether this saved script is active and can be executed. | active | Boolean! |
| 19 | Whether or not the saved script is deprecated and should not be used. | deprecated | Boolean |
| 20 | A relation to the data changes for this document. | dataChanges | [SavedTransformDataChange!] |
| 21 | The date and time the record was created. | createdAt | DateTime! |
| 22 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 23 | The User who created this record. | createdByUser | User! |
| 24 | The date and time the record was last updated. | updatedAt | DateTime! |
| 25 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 26 | The User who last updated this record. | updatedByUser | User! |
| 27 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 28 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 29 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | SavedTransformAggregate! |
| 30 | Custom fields configured for the data model. | _customFields | SavedTransform_CustomFields! |

##### data.modelDefinitions[117]
| Property | Value |
| --- | --- |
| description | The SavedTransformDeployment model represents a deployment of a version of an Fuuz Scripting Language transform. |
| fields | _complex array_ |
| name | SavedTransformDeployment |

###### data.modelDefinitions[117].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Saved Transform Deployment. | id | ID! |
| 2 | The label of this Saved Transform Deployment <SCRIPT NAME>: <VERSION>: <mm-dd-yy hh:mm:ss> | label | String! |
| 3 | The unique identifier of the Saved Transform this is a deployment of. | savedTransformId | ID! |
| 4 | The Saved Transform this is a deployment of. | savedTransform | SavedTransform! |
| 5 | The unique identifier of the Saved Transform Version this is a deployment of. | savedTransformVersionId | ID! |
| 6 | The Saved Transform Version this is a deployment of. | savedTransformVersion | SavedTransformVersion! |
| 7 | The unique identifier of the language the Saved Transform script uses. | scriptLanguageId | ID! |
| 8 | The language the Saved Transform script uses. | scriptLanguage | ScriptLanguage! |
| 9 | The saved script. | transform | String! |
| 10 | The input schema defining the shape of the input data to the script. | inputSchema | JSON! |
| 11 | The output schema defining the shape of the result of the script. | outputSchema | JSON! |
| 12 | The date and time the record was created. | createdAt | DateTime! |
| 13 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 14 | The User who created this record. | createdByUser | User! |
| 15 | The date and time the record was last updated. | updatedAt | DateTime! |
| 16 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 17 | The User who last updated this record. | updatedByUser | User! |
| 18 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 19 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 20 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | SavedTransformDeploymentAggregate! |

##### data.modelDefinitions[118]
| Property | Value |
| --- | --- |
| description | The SavedTransformVersion model represents a version of a saved Fuuz Scripting Language transform. |
| fields | _complex array_ |
| name | SavedTransformVersion |

###### data.modelDefinitions[118].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Saved Transform Version. | id | ID! |
| 2 | The number of the Saved Transform Version. | number | String! |
| 3 | The description of the saved transform's version. | description | String |
| 4 | The unique identifier of the Saved Transform this is a version of. | savedTransformId | ID! |
| 5 | The Saved Transform this is a version of. | savedTransform | SavedTransform! |
| 6 | The unique identifier of the language the Saved Transform script uses. | scriptLanguageId | ID! |
| 7 | The language the Saved Transform script uses. | scriptLanguage | ScriptLanguage! |
| 8 | The saved script. | transform | String! |
| 9 | The input schema defining the shape of the input data to the script. | inputSchema | JSON! |
| 10 | The output schema defining the shape of the result of the script. | outputSchema | JSON! |
| 11 | The deployments of this Saved Transform Version. | deployments | [SavedTransformDeployment!] |
| 12 | Whether this Saved Transform version has ever been deployed. | deployed | Boolean! |
| 13 | A relation to the data changes for this document. | dataChanges | [SavedTransformVersionDataChange!] |
| 14 | The date and time the record was created. | createdAt | DateTime! |
| 15 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 16 | The User who created this record. | createdByUser | User! |
| 17 | The date and time the record was last updated. | updatedAt | DateTime! |
| 18 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 19 | The User who last updated this record. | updatedByUser | User! |
| 20 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 21 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 22 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | SavedTransformVersionAggregate! |
| 23 | Custom fields configured for the data model. | _customFields | SavedTransformVersion_CustomFields! |

##### data.modelDefinitions[119]
| Property | Value |
| --- | --- |
| description | Defines a collection of ScheduleEvents that provide available time. |
| fields | _complex array_ |
| name | Schedule |

###### data.modelDefinitions[119].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record. | id | ID! |
| 2 | A unique human readable identifier for the Schedule. | code | String! |
| 3 | When rendered in the Schedule Editor, the lowest sortOrder value will be rendered first. | sortOrder | Int |
| 4 | A non-unique common name for the Schedule | name | String! |
| 5 | User defined description for what this Schedule is used for and or notes about it's use case. | description | RichText |
| 6 | The unique identifier for the Schedule Group associated with the schedule. | scheduleGroupId | ID! |
| 7 | A relation to the ScheduleGroup associated with this schedule. | scheduleGroup | ScheduleGroup! |
| 8 | A relation to the ScheduleEvents associated with the schedule. | scheduleEvents | [ScheduleEvent!]! |
| 9 | Provides a contigous timeline of event ocurrences, events that are adjacent or overlapping will be merged together | timeline | [EventRange]! |
| 10 | A relation to the data changes for this document. | dataChanges | [ScheduleDataChange!] |
| 11 | The date and time the record was created. | createdAt | DateTime! |
| 12 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 13 | The User who created this record. | createdByUser | User! |
| 14 | The date and time the record was last updated. | updatedAt | DateTime! |
| 15 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 16 | The User who last updated this record. | updatedByUser | User! |
| 17 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 18 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 19 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ScheduleAggregate! |
| 20 | Custom fields configured for the data model. | _customFields | Schedule_CustomFields! |

##### data.modelDefinitions[120]
| Property | Value |
| --- | --- |
| description | A ScheduleEvent defines available time for the Schedule it is associated with. |
| fields | _complex array_ |
| name | ScheduleEvent |

###### data.modelDefinitions[120].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record | id | ID! |
| 2 | A user defined label for the Schedule Event to better understand the events use case. | label | String! |
| 3 | A recurring rule string that defines when the recurrence should start, how frequently it should recur and when it should stop it's recurrences | rrule | RRule! |
| 4 | An ISO8601 Duration string that defines the duration of the event. | duration | Duration! |
| 5 | The unique identifier of the Schedule associated with the event | scheduleId | ID! |
| 6 | A relation to the Schedule associated with the event | schedule | Schedule! |
| 7 | A relation to the data changes for this document. | dataChanges | [ScheduleEventDataChange!] |
| 8 | The date and time the record was created. | createdAt | DateTime! |
| 9 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 10 | The User who created this record. | createdByUser | User! |
| 11 | The date and time the record was last updated. | updatedAt | DateTime! |
| 12 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 13 | The User who last updated this record. | updatedByUser | User! |
| 14 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 15 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 16 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ScheduleEventAggregate! |
| 17 | Custom fields configured for the data model. | _customFields | ScheduleEvent_CustomFields! |

##### data.modelDefinitions[121]
| Property | Value |
| --- | --- |
| description | Defines a group of schedules for a use case |
| fields | _complex array_ |
| name | ScheduleGroup |

###### data.modelDefinitions[121].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 | A unique human readable identifier for the Schedule Group. | code | String! |
| 3 | A non-unique common name for the Schedule Group | name | String! |
| 4 | User defined description for what this Schedule Group is used for and or notes about it's use case. | description | RichText |
| 5 | A relation to the Schedules associated with this Schedule Group. | schedules | [Schedule!]! |
| 6 | A relation to the ScheduleGroupExceptionEvents that are associated to the Schedule Group. | scheduleGroupExceptionEvents | [ScheduleGroupExceptionEvent!]! |
| 7 | A relation to the data changes for this document. | dataChanges | [ScheduleGroupDataChange!] |
| 8 | The date and time the record was created. | createdAt | DateTime! |
| 9 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 10 | The User who created this record. | createdByUser | User! |
| 11 | The date and time the record was last updated. | updatedAt | DateTime! |
| 12 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 13 | The User who last updated this record. | updatedByUser | User! |
| 14 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 15 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 16 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ScheduleGroupAggregate! |
| 17 | Custom fields configured for the data model. | _customFields | ScheduleGroup_CustomFields! |

##### data.modelDefinitions[122]
| Property | Value |
| --- | --- |
| description | Schedule Group Exception Event defines unavailable time for all Schedules associated with the Schedule Group. |
| fields | _complex array_ |
| name | ScheduleGroupExceptionEvent |

###### data.modelDefinitions[122].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record | id | ID! |
| 2 | A user defined label for the Schedule Group Exception to better understand the events use case. | label | String! |
| 3 | A recurring rule string that defines when the recurrence should start, how frequently it should recur and when it should stop it's recurrences | rrule | RRule! |
| 4 | An ISO8601 Duration string that defines the duration of the event. | duration | Duration! |
| 5 | The unique identifier for the Schedule Group associated with the Schedule Group Exception. | scheduleGroupId | ID! |
| 6 | Relation to the ScheduleGroup. | scheduleGroup | ScheduleGroup! |
| 7 | A relation to the data changes for this document. | dataChanges | [ScheduleGroupExceptionEventDataChange!] |
| 8 | The date and time the record was created. | createdAt | DateTime! |
| 9 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 10 | The User who created this record. | createdByUser | User! |
| 11 | The date and time the record was last updated. | updatedAt | DateTime! |
| 12 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 13 | The User who last updated this record. | updatedByUser | User! |
| 14 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 15 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 16 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ScheduleGroupExceptionEventAggregate! |
| 17 | Custom fields configured for the data model. | _customFields | ScheduleGroupExceptionEvent_CustomFields! |

##### data.modelDefinitions[123]
| Property | Value |
| --- | --- |
| description | ScheduleType defines different configurations to schedule a Data Flow or process. |
| fields | _complex array_ |
| name | ScheduleType |

###### data.modelDefinitions[123].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 | The name of the schedule type. | name | String! |
| 3 | The configSchema provides dynamic inputs on forms and validation for a configuration. | configSchema | JSONObject |
| 4 | The date and time the record was created. | createdAt | DateTime! |
| 5 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 6 | The User who created this record. | createdByUser | User! |
| 7 | The date and time the record was last updated. | updatedAt | DateTime! |
| 8 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 9 | The User who last updated this record. | updatedByUser | User! |
| 10 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 11 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 12 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ScheduleTypeAggregate! |

##### data.modelDefinitions[124]
| Property | Value |
| --- | --- |
| description | Scheduling Configuration is defined for the SchedulingConfiguration Screen Element as well as the Schedule Engine. The definition defined on a record of this model will provide loading data into the scheduler as well as committing that data back to the database. This configuration will be copied upon creation of a SchedulingConfigurationRun. |
| fields | _complex array_ |
| name | SchedulingConfiguration |

###### data.modelDefinitions[124].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 | The name of your Scheduling Configuration. This will generally align with the use case of the Scheduling Configuration. | name | String! |
| 3 | The Scheduling Window will define what part of the schedule can be changed from within the context of a scheduling run. | schedulingWindow | Duration! |
| 4 | Dependencies define relations between events (Ex: A -> B when A is completed, B can be started). Dependency Mapping provides the data model and field mappings to load dependencies into the Scheduler. More than one mapping can be provided referencing different models. | dependencyMapping | JSON! |
| 5 | Events are the range of time defined on the schedule (Ex: Work Order Process). Event Mapping provides the data model and field mappings to load events into the Scheduler. More than one mapping can be provided referencing different models. | eventMapping | JSON! |
| 6 | Resources are the units that you allocate the events to (Ex: Workcenter). Resource Mapping provides the data model and field mappings to load resources into the Scheduler. More than one mapping can be provided referencing different models. | resourceMapping | JSON! |
| 7 | Assignments are the relation between Events and Resources, defining what is currently assigned to a resource. Assignment Mapping provides the data model and field mappings to load assignments into the Scheduler. More than one mapping can be provided referencing different models. | assignmentMapping | JSON! |
| 8 | Defines the User Interface options for the scheduler | uiOptions | JSONObject |
| 9 | Defines options use for the Scheduler, these will be shared across the front and backend. | options | JSONObject |
| 10 | The relation to the SchedulingConfigurationRuns created based on the configuration of this record. | schedulingConfigurationRuns | [SchedulingConfigurationRun!]! |
| 11 | A relation to the data changes for this document. | dataChanges | [SchedulingConfigurationDataChange!] |
| 12 | The date and time the record was created. | createdAt | DateTime! |
| 13 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 14 | The User who created this record. | createdByUser | User! |
| 15 | The date and time the record was last updated. | updatedAt | DateTime! |
| 16 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 17 | The User who last updated this record. | updatedByUser | User! |
| 18 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 19 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 20 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | SchedulingConfigurationAggregate! |
| 21 | Custom fields configured for the data model. | _customFields | SchedulingConfiguration_CustomFields! |

##### data.modelDefinitions[125]
| Property | Value |
| --- | --- |
| description | The Scheduling Configuration Run represents a scheduling attempt on the resources defined by the mappings provided in the Scheduling Configuration. When a Scheduling Configuration Run is created, it will automatically begin the process of scheduling the provided resources by automatically loading them into the Scheduler, processing them and updating the run record with the changes the scheduler made. From there you can commit those changes through the `commitSchedulingConfigurationRun` mutation. |
| fields | _complex array_ |
| name | SchedulingConfigurationRun |

###### data.modelDefinitions[125].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 | The label can be used to assist in searching for a specific run. If no value is provided it will be defaulted. | label | String |
| 3 | The calculated start date of the Scheduling Run based on the Configurations Scheduling window. Will always be populated by the start of the day when the run is created. | startDate | DateTime |
| 4 | The calculated end date of the Scheduling Run based on the Configurations Scheduling window. Will always be populated with the startDate + schedulingWindow duration | endDate | DateTime |
| 5 | The Scheduling Window will define what part of the schedule can be changed from within the context of a scheduling run. This is copied from the SchedulingConfiguration upon creation of a run. | schedulingWindow | Duration |
| 6 | The changes reported by Bryntum | changes | JSONObject |
| 7 | An array of objects containing a dataModel and predicate property used for targeting events to be scheduled. | filter | [SchedulingConfigurationRunFilter] |
| 8 | The ID of the Scheduling Configuration of which this run will be created against. | schedulingConfigurationId | ID! |
| 9 | The relation to the Scheduling Configuration | schedulingConfiguration | SchedulingConfiguration! |
| 10 | The ID of the Scheduling Configuration Run Status the Run is currently in. | schedulingConfigurationRunStatusId | ID |
| 11 | The relation to the Scheduling Configuration Run Status. | schedulingConfigurationRunStatus | SchedulingConfigurationRunStatus |
| 12 | If an error occurs during scheduling or committing that error will be reported here. | error | JSON |
| 13 | When autoCommit is true, the run record will automatically update the Events model with changes provided by the scheduler. This is useful when there is no desire to review the schedule before it's committed. | autoCommit | Boolean! |
| 14 | When autoSchedule is true, the Scheduling Engine will automatically solve for the provided constraints and provide a new schedule. | autoSchedule | Boolean |
| 15 | Dependencies define relations between events (Ex: A -> B when A is completed, B can be started). Dependency Mapping provides the data model and field mappings to load dependencies into the Scheduler. More than one mapping can be provided referencing different models. | dependencyMapping | JSON! |
| 16 | Events are the range of time defined on the schedule (Ex: Work Order Process). Event Mapping provides the data model and field mappings to load events into the Scheduler. More than one mapping can be provided referencing different models. | eventMapping | JSON! |
| 17 | Resources are the units that you allocate the events to (Ex: Workcenter). Resource Mapping provides the data model and field mappings to load resources into the Scheduler. More than one mapping can be provided referencing different models. | resourceMapping | JSON! |
| 18 | Assignments are the relation between Events and Resources, defining what is currently assigned to a resource. Assignment Mapping provides the data model and field mappings to load assignments into the Scheduler. More than one mapping can be provided referencing different models. | assignmentMapping | JSON! |
| 19 | Defines the User Interface options for the scheduler | uiOptions | JSONObject |
| 20 | Defines options use for the Scheduler, these will be shared across the front and backend. | options | JSONObject |
| 21 | A relation to the data changes for this document. | dataChanges | [SchedulingConfigurationRunDataChange!] |
| 22 | The date and time the record was created. | createdAt | DateTime! |
| 23 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 24 | The User who created this record. | createdByUser | User! |
| 25 | The date and time the record was last updated. | updatedAt | DateTime! |
| 26 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 27 | The User who last updated this record. | updatedByUser | User! |
| 28 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 29 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 30 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | SchedulingConfigurationRunAggregate! |
| 31 | Custom fields configured for the data model. | _customFields | SchedulingConfigurationRun_CustomFields! |

##### data.modelDefinitions[126]
| Property | Value |
| --- | --- |
| description | This model represents the filters that can be provided to the Scheduling Configuration Run |
| fields | _complex array_ |
| name | SchedulingConfigurationRunFilter |

###### data.modelDefinitions[126].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The Data Model the filters correlate with. | dataModel | String! |
| 2 | A GraphQL predicate used to filter the data in the table. | predicate | JSONObject! |
| 3 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | SchedulingConfigurationRunFilterAggregate! |

##### data.modelDefinitions[127]
| Property | Value |
| --- | --- |
| description | Defines the current status of a Scheduling Configuration Run. |
| fields | _complex array_ |
| name | SchedulingConfigurationRunStatus |

###### data.modelDefinitions[127].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 | The name of the status | name | String! |
| 3 | Provides information to understand what the status means. | description | String! |
| 4 | A relation to the data changes for this document. | dataChanges | [SchedulingConfigurationRunStatusDataChange!] |
| 5 | The date and time the record was created. | createdAt | DateTime! |
| 6 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 7 | The User who created this record. | createdByUser | User! |
| 8 | The date and time the record was last updated. | updatedAt | DateTime! |
| 9 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 10 | The User who last updated this record. | updatedByUser | User! |
| 11 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 12 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 13 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | SchedulingConfigurationRunStatusAggregate! |

##### data.modelDefinitions[128]
| Property | Value |
| --- | --- |
| description | Defines the available constraints when using the Scheduling Engine in the Fuuz Platform |
| fields | _complex array_ |
| name | SchedulingConstraintType |

###### data.modelDefinitions[128].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id field. | id | ID! |
| 2 | The name of the constraint | name | String! |
| 3 | A description describing what the constraint does. | description | RichText! |
| 4 | The date and time the record was created. | createdAt | DateTime! |
| 5 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 6 | The User who created this record. | createdByUser | User! |
| 7 | The date and time the record was last updated. | updatedAt | DateTime! |
| 8 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 9 | The User who last updated this record. | updatedByUser | User! |
| 10 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 11 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 12 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | SchedulingConstraintTypeAggregate! |

##### data.modelDefinitions[129]
| Property | Value |
| --- | --- |
| description | The Screen type represents the metadata defining a user interface screen in Fuuz. |
| fields | _complex array_ |
| name | Screen |

###### data.modelDefinitions[129].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the screen. | id | ID! |
| 2 | The name of the screen. | name | String! |
| 3 | The description of the screen. | description | String! |
| 4 | Whether the screen is active and should run once deployed. | active | Boolean! |
| 5 | Whether the screen is available on the edge. | edgeScreen | Boolean! |
| 6 | Force fullscreen mode always. | forceFullscreen | Boolean |
| 7 | The module used for this screen. | moduleId | ID |
| 8 | The unique identifier of the module this Screen is part of. | module | Module |
| 9 | A relation to the routes through which this screen is exposed. | routes | [Route!] |
| 10 | The versions of this screen. | versions | [ScreenVersion!] |
| 11 | The deployments of this screen. | deployments | [ScreenDeployment!] |
| 12 | The unique identifier of the current deployment of this screen. | currentDeploymentId | ID |
| 13 | The current deployment of this screen. | currentDeployment | ScreenDeployment |
| 14 | The unique identifier of the Screen this Screen substitutes. If the current Screen is active and deployed, it will replace the target Screens effectiveDeploymentId with it's currentDeploymentId | substitutesScreenId | ID |
| 15 | The relation to the Screen this Screen substitutes. If the current Screen is active and deployed, it will replace the target Screens effectiveDeployment with it's currentDeployment | substitutesScreen | Screen |
| 16 | The relation to the Screen that substitutes this Screen. If the Screen is active it will replace the current Screens effectiveDeploymentId with it's currentDeploymentId | substitutedByScreen | Screen |
| 17 | The unique identifier of the Effective Deployment of this Screen Design. | effectiveDeploymentId | ID |
| 18 | The Effective Deployment of this Screen Design. | effectiveDeployment | ScreenDeployment |
| 19 | This screen will be used as widget. | isWidget | Boolean |
| 20 | The normalized elements of this screen. | elements | [ScreenElement!] |
| 21 | The application component references for which this screen is the source. | sourceApplicationComponentReferences | [ApplicationComponentReference!] |
| 22 | The application component references for which this screen is the target. | targetApplicationComponentReferences | [ApplicationComponentReference!] |
| 23 | A relation to the data changes for this document. | dataChanges | [ScreenDataChange!] |
| 24 | The date and time the record was created. | createdAt | DateTime! |
| 25 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 26 | The User who created this record. | createdByUser | User! |
| 27 | The date and time the record was last updated. | updatedAt | DateTime! |
| 28 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 29 | The User who last updated this record. | updatedByUser | User! |
| 30 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 31 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 32 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ScreenAggregate! |
| 33 | Custom fields configured for the data model. | _customFields | Screen_CustomFields! |

##### data.modelDefinitions[130]
| Property | Value |
| --- | --- |
| description | The ScreenDeployment model represents a deployment of a version of an Fuuz screen, which indicates that the screen should be rendered when called. |
| fields | _complex array_ |
| name | ScreenDeployment |

###### data.modelDefinitions[130].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Screen Deployment. | id | ID! |
| 2 | The JSON design of the screen. | design | JSONObject! |
| 3 | The unique identifier of the screen this record is a deployment of. | screenId | ID! |
| 4 | The screen this record is a deployment of. | screen | Screen! |
| 5 | The unique identifier of the screen version this record is a deployment of. | screenVersionId | ID! |
| 6 | The screen version this record is a deployment of. | screenVersion | ScreenVersion! |
| 7 | The date and time the record was created. | createdAt | DateTime! |
| 8 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 9 | The User who created this record. | createdByUser | User! |
| 10 | The date and time the record was last updated. | updatedAt | DateTime! |
| 11 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 12 | The User who last updated this record. | updatedByUser | User! |
| 13 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 14 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 15 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ScreenDeploymentAggregate! |

##### data.modelDefinitions[131]
| Property | Value |
| --- | --- |
| description | The content of an element in a Screen. |
| fields | _complex array_ |
| name | ScreenElement |

###### data.modelDefinitions[131].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The primary unique ID of the record.  Every model should have an id element. | id | ID! |
| 2 | The Id of the element within the model. | elementId | String! |
| 3 | The name of the element. | name | String! |
| 4 | The full type string of the element. | type | String! |
| 5 | The description of the element. | description | String |
| 6 | The full definition of the element. | definition | JSONObject! |
| 7 | The type-specific configuration of the element. | configuration | JSONObject! |
| 8 | The path at which this element can be found within the screen design. | path | String! |
| 9 | The ID of the Screen that contains this element. | screenId | ID! |
| 10 | The Screen that contains this element. | screen | Screen! |
| 11 | The name of the screen component (tab) the element appears in. | componentName | String! |
| 12 | The label for this element, if it's an input element or column. | label | String |
| 13 | The data path for this element, if it's an input element. | dataPath | String |
| 14 | The query configuration for this element, if the element is data-bearing. | query | ScreenElementQuery |
| 15 | The application component references for which this screen element is the source. | sourceApplicationComponentReferences | [ApplicationComponentReference!] |
| 16 | The application component references for which this screen element is the target. | targetApplicationComponentReferences | [ApplicationComponentReference!] |
| 17 | A relation to the data changes for this document. | dataChanges | [ScreenElementDataChange!] |
| 18 | The date and time the record was created. | createdAt | DateTime! |
| 19 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 20 | The User who created this record. | createdByUser | User! |
| 21 | The date and time the record was last updated. | updatedAt | DateTime! |
| 22 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 23 | The User who last updated this record. | updatedByUser | User! |
| 24 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 25 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 26 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ScreenElementAggregate! |

##### data.modelDefinitions[132]
| Property | Value |
| --- | --- |
| description | This model represents the query prop for a screen element. |
| fields | _complex array_ |
| name | ScreenElementQuery |

###### data.modelDefinitions[132].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The API this screen element queries. | api | String |
| 2 | The model this screen element queries. | model | String |
| 3 | The fields this screen element queries. | fields | [String!] |
| 4 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ScreenElementQueryAggregate! |

##### data.modelDefinitions[133]
| Property | Value |
| --- | --- |
| description | The ScreenVersion model represents a version of an Fuuz screen, with a unique number and associated screen design. |
| fields | _complex array_ |
| name | ScreenVersion |

###### data.modelDefinitions[133].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the screen version. | id | ID! |
| 2 | The number of the screen version. | number | String! |
| 3 | The description of the screen version. | description | String! |
| 4 | The unique identifier of the screen this record is a version of. | screenId | ID! |
| 5 | The screen this record is a version of. | screen | Screen! |
| 6 | The JSON design of the screen. | design | JSONObject! |
| 7 | The deployments of this screen version. | deployments | [ScreenDeployment!] |
| 8 | Whether this screen version has ever been deployed. | deployed | Boolean! |
| 9 | A relation to the data changes for this document. | dataChanges | [ScreenVersionDataChange!] |
| 10 | The date and time the record was created. | createdAt | DateTime! |
| 11 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 12 | The User who created this record. | createdByUser | User! |
| 13 | The date and time the record was last updated. | updatedAt | DateTime! |
| 14 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 15 | The User who last updated this record. | updatedByUser | User! |
| 16 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 17 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 18 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ScreenVersionAggregate! |
| 19 | Custom fields configured for the data model. | _customFields | ScreenVersion_CustomFields! |

##### data.modelDefinitions[134]
| Property | Value |
| --- | --- |
| description | The ScriptLanguage model represents a language for the Fuuz Scripting system. |
| fields | _complex array_ |
| name | ScriptLanguage |

###### data.modelDefinitions[134].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Script Language. | id | ID! |
| 2 | The name of the Script Language. | name | String! |
| 3 | The description of the Script Language. | description | String! |
| 4 | The date and time the record was created. | createdAt | DateTime! |
| 5 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 6 | The User who created this record. | createdByUser | User! |
| 7 | The date and time the record was last updated. | updatedAt | DateTime! |
| 8 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 9 | The User who last updated this record. | updatedByUser | User! |
| 10 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 11 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 12 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | ScriptLanguageAggregate! |

##### data.modelDefinitions[135]
| Property | Value |
| --- | --- |
| description | The Sequence model represents a sequence of values that can be consumed within Fuuz. |
| fields | _complex array_ |
| name | Sequence |

###### data.modelDefinitions[135].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Sequence. | id | ID! |
| 2 | The name of the Sequence. | name | String! |
| 3 | The type of the Sequence. | type | String! |
| 4 | Increment value for this Sequence. | increment | Int! |
| 5 | The prefix to add to Sequence Values for this Sequence. | prefix | String! |
| 6 | The suffix to add to Sequence Values for this Sequence. | suffix | String! |
| 7 | The number of zeroes to pad Sequence Values of this Sequence with. | zeroPadding | Int! |
| 8 | A relation to the Sequence Numbers for this Sequence. | sequenceNumbers | [SequenceNumber!] |
| 9 | A relation to the data changes for this document. | dataChanges | [SequenceDataChange!] |
| 10 | The date and time the record was created. | createdAt | DateTime! |
| 11 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 12 | The User who created this record. | createdByUser | User! |
| 13 | The date and time the record was last updated. | updatedAt | DateTime! |
| 14 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 15 | The User who last updated this record. | updatedByUser | User! |
| 16 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 17 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 18 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | SequenceAggregate! |
| 19 | Custom fields configured for the data model. | _customFields | Sequence_CustomFields! |

##### data.modelDefinitions[136]
| Property | Value |
| --- | --- |
| description | The SequenceNumber model represents the current number of a Sequence. |
| fields | _complex array_ |
| name | SequenceNumber |

###### data.modelDefinitions[136].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Sequence Number. | id | ID! |
| 2 | The label of this Sequence Number. | label | String! |
| 3 | The ID of the Sequence this Sequence Number belongs to. | sequenceId | ID! |
| 4 | A relation to the Sequence this Sequence Number belongs to. | sequence | Sequence! |
| 5 | The key associated with this Sequence Number. | key | String |
| 6 | The value associated with this Sequence Number. | value | JSON! |
| 7 | The date and time the record was created. | createdAt | DateTime! |
| 8 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 9 | The User who created this record. | createdByUser | User! |
| 10 | The date and time the record was last updated. | updatedAt | DateTime! |
| 11 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 12 | The User who last updated this record. | updatedByUser | User! |
| 13 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 14 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 15 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | SequenceNumberAggregate! |
| 16 | Custom fields configured for the data model. | _customFields | SequenceNumber_CustomFields! |

##### data.modelDefinitions[137]
| Property | Value |
| --- | --- |
| description | A Fuuz setting |
| fields | _complex array_ |
| name | Setting |

###### data.modelDefinitions[137].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Setting. | id | ID! |
| 2 | The name of the setting. | name | String! |
| 3 | The ID for the type of input this setting is. | settingInputTypeId | ID! |
| 4 | The setting input type. | settingInputType | SettingInputType! |
| 5 | The description of this setting. | description | String! |
| 6 | The default value of this setting. | defaultValue | JSON |
| 7 | The list of setting values for this setting. | listValues | JSON |
| 8 | The list of types linked to this setting | types | [SettingSettingType!] |
| 9 | The list of user-level values of this setting. | userSettingValues | [UserSettingValue!] |
| 10 | The list of role-level values of this setting. | roleSettingValues | [RoleSettingValue!] |
| 11 | A relation to the data changes for this document. | dataChanges | [SettingDataChange!] |
| 12 | The date and time the record was created. | createdAt | DateTime! |
| 13 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 14 | The User who created this record. | createdByUser | User! |
| 15 | The date and time the record was last updated. | updatedAt | DateTime! |
| 16 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 17 | The User who last updated this record. | updatedByUser | User! |
| 18 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 19 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 20 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | SettingAggregate! |
| 21 | Custom fields configured for the data model. | _customFields | Setting_CustomFields! |

##### data.modelDefinitions[138]
| Property | Value |
| --- | --- |
| description | A type that holds the values of all the input types a setting could be. |
| fields | _complex array_ |
| name | SettingInputType |

###### data.modelDefinitions[138].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the SettingInputType. | id | ID! |
| 2 | The label for the input setting type. | label | String! |
| 3 | A description of the input setting type. | description | String! |
| 4 | The date and time the record was created. | createdAt | DateTime! |
| 5 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 6 | The User who created this record. | createdByUser | User! |
| 7 | The date and time the record was last updated. | updatedAt | DateTime! |
| 8 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 9 | The User who last updated this record. | updatedByUser | User! |
| 10 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 11 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 12 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | SettingInputTypeAggregate! |

##### data.modelDefinitions[139]
| Property | Value |
| --- | --- |
| description | A type that holds the values of all the input types a setting could be. |
| fields | _complex array_ |
| name | SettingSettingType |

###### data.modelDefinitions[139].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the SettingSettingType. | id | ID! |
| 2 | The ID of the setting this user is linked to. | settingId | ID! |
| 3 | The setting. | setting | Setting! |
| 4 | The ID of the setting type. | settingTypeId | ID! |
| 5 | The setting type. | settingType | SettingType! |
| 6 | The date and time the record was created. | createdAt | DateTime! |
| 7 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 8 | The User who created this record. | createdByUser | User! |
| 9 | The date and time the record was last updated. | updatedAt | DateTime! |
| 10 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 11 | The User who last updated this record. | updatedByUser | User! |
| 12 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 13 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 14 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | SettingSettingTypeAggregate! |

##### data.modelDefinitions[140]
| Property | Value |
| --- | --- |
| description | A type that holds the values of all the types a setting could be. |
| fields | _complex array_ |
| name | SettingType |

###### data.modelDefinitions[140].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the SettingType. | id | ID! |
| 2 | The label for the setting type. | label | String! |
| 3 | A description for the setting type. | description | String! |
| 4 | The date and time the record was created. | createdAt | DateTime! |
| 5 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 6 | The User who created this record. | createdByUser | User! |
| 7 | The date and time the record was last updated. | updatedAt | DateTime! |
| 8 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 9 | The User who last updated this record. | updatedByUser | User! |
| 10 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 11 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 12 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | SettingTypeAggregate! |

##### data.modelDefinitions[141]
| Property | Value |
| --- | --- |
| description | The Tag model represents a single data point from a connected device. |
| fields | _complex array_ |
| name | Tag |

###### data.modelDefinitions[141].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Tag. | id | ID! |
| 2 | The code of the Tag. | code | String! |
| 3 | The name of the Tag. | name | String! |
| 4 | The unique identifier of the Channel the Tag belongs to. | channelId | ID! |
| 5 | The Channel the Tag belongs to. | channel | Channel! |
| 6 | The values associated with this tag. | tagValues | [TagValue!]! |
| 7 | A relation to the data changes for this document. | dataChanges | [TagDataChange!] |
| 8 | The date and time the record was created. | createdAt | DateTime! |
| 9 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 10 | The User who created this record. | createdByUser | User! |
| 11 | The date and time the record was last updated. | updatedAt | DateTime! |
| 12 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 13 | The User who last updated this record. | updatedByUser | User! |
| 14 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 15 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 16 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | TagAggregate! |
| 17 | Custom fields configured for the data model. | _customFields | Tag_CustomFields! |

##### data.modelDefinitions[142]
| Property | Value |
| --- | --- |
| description | The TagValue model represents a collected value for a Tag. |
| fields | _complex array_ |
| name | TagValue |

###### data.modelDefinitions[142].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the TagValue. | id | ID! |
| 2 | The unique identifier of the Tag the TagValue belongs to. | tagId | ID! |
| 3 | The Tag the TagValue belongs to. | tag | Tag! |
| 4 | The unique identifier of the Quality of the TagValue. | tagValueQualityId | ID! |
| 5 | The Quality of the TagValue. | tagValueQuality | TagValueQuality! |
| 6 | The value collected. | value | JSON! |
| 7 | When the Tag Value occurred. | occurredAt | DateTime! |
| 8 | The date and time the record was created. | createdAt | DateTime! |
| 9 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 10 | The User who created this record. | createdByUser | User! |
| 11 | The date and time the record was last updated. | updatedAt | DateTime! |
| 12 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 13 | The User who last updated this record. | updatedByUser | User! |
| 14 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 15 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 16 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | TagValueAggregate! |
| 17 | Custom fields configured for the data model. | _customFields | TagValue_CustomFields! |

##### data.modelDefinitions[143]
| Property | Value |
| --- | --- |
| description | The TagQuality model represents a way of categorizing Tag Values by the quality of the data. |
| fields | _complex array_ |
| name | TagValueQuality |

###### data.modelDefinitions[143].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Channel Status. | id | ID! |
| 2 | The label of the Channel Status. | label | String! |
| 3 | The description of the Channel Status. | description | String! |
| 4 | The date and time the record was created. | createdAt | DateTime! |
| 5 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 6 | The User who created this record. | createdByUser | User! |
| 7 | The date and time the record was last updated. | updatedAt | DateTime! |
| 8 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 9 | The User who last updated this record. | updatedByUser | User! |
| 10 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 11 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 12 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | TagValueQualityAggregate! |

##### data.modelDefinitions[144]
| Property | Value |
| --- | --- |
| description | The Topic model represents a topic for routing messages. |
| fields | _complex array_ |
| name | Topic |

###### data.modelDefinitions[144].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Topic. | id | ID! |
| 2 | The name of the Topic. | name | String! |
| 3 | The fully qualified name of the Topic. This is used when subscribing to topics. | fullyQualifiedName | String! |
| 4 | The description of the Topic. | description | String! |
| 5 | The JSON Schema to use for validating the value of messages published to the Topic. | valueSchema | JSON! |
| 6 | Defines whether this topic is accessible by webhooks or not. | webhookAccessible | Boolean |
| 7 | A relation to the data changes for this document. | dataChanges | [TopicDataChange!] |
| 8 | The date and time the record was created. | createdAt | DateTime! |
| 9 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 10 | The User who created this record. | createdByUser | User! |
| 11 | The date and time the record was last updated. | updatedAt | DateTime! |
| 12 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 13 | The User who last updated this record. | updatedByUser | User! |
| 14 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 15 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 16 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | TopicAggregate! |
| 17 | Custom fields configured for the data model. | _customFields | Topic_CustomFields! |

##### data.modelDefinitions[145]
| Property | Value |
| --- | --- |
| description | The Unit type represents a unit of measure within Fuuz. |
| fields | _complex array_ |
| name | Unit |

###### data.modelDefinitions[145].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the unit. | id | ID! |
| 2 | The identifier of the Unit Type the Unit belongs to. | unitTypeId | ID! |
| 3 | A relation to the Unit Type this Unit belongs to. | unitType | UnitType! |
| 4 | The name of the Unit. | name | String! |
| 5 | The short code of the Unit. | code | String! |
| 6 | The conversions that have the Unit as a source. | conversions | [UnitConversion!] |
| 7 | A relation to the data changes for this document. | dataChanges | [UnitDataChange!] |
| 8 | The date and time the record was created. | createdAt | DateTime! |
| 9 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 10 | The User who created this record. | createdByUser | User! |
| 11 | The date and time the record was last updated. | updatedAt | DateTime! |
| 12 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 13 | The User who last updated this record. | updatedByUser | User! |
| 14 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 15 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 16 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | UnitAggregate! |
| 17 | Custom fields configured for the data model. | _customFields | Unit_CustomFields! |

##### data.modelDefinitions[146]
| Property | Value |
| --- | --- |
| description | The UnitConversion type represents a conversion between two Unit types in Fuuz. |
| fields | _complex array_ |
| name | UnitConversion |

###### data.modelDefinitions[146].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the UnitConversion. | id | ID! |
| 2 | The identifier of the Unit to convert from. | sourceUnitId | ID! |
| 3 | The Unit to convert from. | sourceUnit | Unit! |
| 4 | The identifier of the Unit to convert to. | targetUnitId | ID! |
| 5 | The Unit to convert to. | targetUnit | Unit! |
| 6 | The conversion factor to use for converting from the source to the target. | factor | Float! |
| 7 | A relation to the data changes for this document. | dataChanges | [UnitConversionDataChange!] |
| 8 | The date and time the record was created. | createdAt | DateTime! |
| 9 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 10 | The User who created this record. | createdByUser | User! |
| 11 | The date and time the record was last updated. | updatedAt | DateTime! |
| 12 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 13 | The User who last updated this record. | updatedByUser | User! |
| 14 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 15 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 16 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | UnitConversionAggregate! |
| 17 | Custom fields configured for the data model. | _customFields | UnitConversion_CustomFields! |

##### data.modelDefinitions[147]
| Property | Value |
| --- | --- |
| description | The UnitType type represents the types of Units that can be created within Fuuz. |
| fields | _complex array_ |
| name | UnitType |

###### data.modelDefinitions[147].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the UnitType. | id | ID! |
| 2 | The human readable label for the unit type. | label | String! |
| 3 | The description for the unit type. | description | String! |
| 4 | The units that have been assigned this unit type. | units | [Unit!] |
| 5 | The date and time the record was created. | createdAt | DateTime! |
| 6 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 7 | The User who created this record. | createdByUser | User! |
| 8 | The date and time the record was last updated. | updatedAt | DateTime! |
| 9 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 10 | The User who last updated this record. | updatedByUser | User! |
| 11 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 12 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 13 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | UnitTypeAggregate! |
| 14 | Custom fields configured for the data model. | _customFields | UnitType_CustomFields! |

##### data.modelDefinitions[148]
| Property | Value |
| --- | --- |
| description | The User model represents a user account in Fuuz. |
| fields | _complex array_ |
| name | User |

###### data.modelDefinitions[148].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the User. | id | ID! |
| 2 | The email address of the User.  The email is also used as the username. | email | String! |
| 3 | The first name of the User. | firstName | String! |
| 4 | The middle name of the User. | middleName | String! |
| 5 | The last name of the User. | lastName | String! |
| 6 | Whether the User account is active.  Inactive users aren't allowed to log into Fuuz or access Fuuz APIs. | active | Boolean! |
| 7 | The unique identifier of the access type assigned to the user in the current application . | accessTypeId | ID! |
| 8 | The UserAccessControlPolicys this User has been attached to. | userAccessControlPolicies | [UserAccessControlPolicy!] |
| 9 | The UserAccessControlPolicyGroups this User has been attached to. | userAccessControlPolicyGroups | [UserAccessControlPolicyGroup!] |
| 10 | The UserRole this user has been attached to. | userRoles | [UserRole!] |
| 11 | The date and time the record was created. | createdAt | DateTime! |
| 12 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 13 | The User who created this record. | createdByUser | User! |
| 14 | The date and time the record was last updated. | updatedAt | DateTime! |
| 15 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 16 | The User who last updated this record. | updatedByUser | User! |
| 17 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 18 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 19 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | UserAggregate! |

##### data.modelDefinitions[149]
| Property | Value |
| --- | --- |
| description | The UserAccessControlPolicy model represents a link between Users and AccessControlPolicies, representing direct assignment of a policy to a user's access. |
| fields | _complex array_ |
| name | UserAccessControlPolicy |

###### data.modelDefinitions[149].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the UserAccessControlPolicy. | id | ID! |
| 2 | The unique identifier of the User the AccessControlPolicy is assigned to. | userId | ID! |
| 3 | The User the AccessControlPolicy is assigned to. | user | User! |
| 4 | The unique identifier of the AccessControlPolicy assigned to the User. | accessControlPolicyId | ID! |
| 5 | The AccessControlPolicy assigned to the User. | accessControlPolicy | AccessControlPolicy! |
| 6 | A relation to the data changes for this document. | dataChanges | [UserAccessControlPolicyDataChange!] |
| 7 | The date and time the record was created. | createdAt | DateTime! |
| 8 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 9 | The User who created this record. | createdByUser | User! |
| 10 | The date and time the record was last updated. | updatedAt | DateTime! |
| 11 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 12 | The User who last updated this record. | updatedByUser | User! |
| 13 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 14 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 15 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | UserAccessControlPolicyAggregate! |
| 16 | Custom fields configured for the data model. | _customFields | UserAccessControlPolicy_CustomFields! |

##### data.modelDefinitions[150]
| Property | Value |
| --- | --- |
| description | The UserAccessControlPolicyGroup model represents a link between a User and AccessControlPolicyGroups, representing assignment of a policy group to a user's access. |
| fields | _complex array_ |
| name | UserAccessControlPolicyGroup |

###### data.modelDefinitions[150].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the UserTenantAccessControlPolicyGroup. | id | ID! |
| 2 | The unique identifier of a User the AccessControlPolicyGroup is assigned to. | userId | ID! |
| 3 | The User the AccessControlPolicyGroup is assigned to. | user | User! |
| 4 | The unique identifier of the AccessControlPolicyGroup assigned to the User. | accessControlPolicyGroupId | ID! |
| 5 | The AccessControlPolicyGroup assigned to the User. | accessControlPolicyGroup | AccessControlPolicyGroup! |
| 6 | A relation to the data changes for this document. | dataChanges | [UserAccessControlPolicyGroupDataChange!] |
| 7 | The date and time the record was created. | createdAt | DateTime! |
| 8 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 9 | The User who created this record. | createdByUser | User! |
| 10 | The date and time the record was last updated. | updatedAt | DateTime! |
| 11 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 12 | The User who last updated this record. | updatedByUser | User! |
| 13 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 14 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 15 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | UserAccessControlPolicyGroupAggregate! |
| 16 | Custom fields configured for the data model. | _customFields | UserAccessControlPolicyGroup_CustomFields! |

##### data.modelDefinitions[151]
| Property | Value |
| --- | --- |
| description | An external application user to be used for configuration with Fuuz. |
| fields | _complex array_ |
| name | UserExternalApplicationConfiguration |

###### data.modelDefinitions[151].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The id for this user external application configuration. | id | ID! |
| 2 | The ID of the user linking to an external application that goes to this configuration. | userId | ID! |
| 3 | The relation to the user for this external application configuration. | user | User! |
| 4 | The ID of the external application configuration for this user configuration. | externalApplicationConfigurationId | ID! |
| 5 | The relation to the external application configuration for this user configuration. | externalApplicationConfiguration | ExternalApplicationConfiguration! |
| 6 | A relation to the data changes for this document. | dataChanges | [UserExternalApplicationConfigurationDataChange!] |
| 7 | The date and time the record was created. | createdAt | DateTime! |
| 8 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 9 | The User who created this record. | createdByUser | User! |
| 10 | The date and time the record was last updated. | updatedAt | DateTime! |
| 11 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 12 | The User who last updated this record. | updatedByUser | User! |
| 13 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 14 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 15 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | UserExternalApplicationConfigurationAggregate! |
| 16 | Custom fields configured for the data model. | _customFields | UserExternalApplicationConfiguration_CustomFields! |

##### data.modelDefinitions[152]
| Property | Value |
| --- | --- |
| description | The UserFavorites type represents the pages marked as a favorite by the user within Fuuz. |
| fields | _complex array_ |
| name | UserFavorite |

###### data.modelDefinitions[152].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the favorite. | id | ID! |
| 2 | The name of the favorite. | name | String! |
| 3 | The path of the favorite. | path | String! |
| 4 | The query string of the favorite. | queryString | String! |
| 5 | The hotkey of the favorite. | hotkey | String! |
| 6 | The number related to the order of the favorite. | sortOrder | Int! |
| 7 | The identifier of the user who created the favorite. | userId | ID! |
| 8 | The relation the favorite has to the user. | user | User! |
| 9 | A relation to the data changes for this document. | dataChanges | [UserFavoriteDataChange!] |
| 10 | The date and time the record was created. | createdAt | DateTime! |
| 11 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 12 | The User who created this record. | createdByUser | User! |
| 13 | The date and time the record was last updated. | updatedAt | DateTime! |
| 14 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 15 | The User who last updated this record. | updatedByUser | User! |
| 16 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 17 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 18 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | UserFavoriteAggregate! |
| 19 | Custom fields configured for the data model. | _customFields | UserFavorite_CustomFields! |

##### data.modelDefinitions[153]
| Property | Value |
| --- | --- |
| description | The UserRole type represents the roles that an individual user has been granted. |
| fields | _complex array_ |
| name | UserRole |

###### data.modelDefinitions[153].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the UserRole. | id | ID! |
| 2 | The relation to the User. | user | User! |
| 3 | The relation to the Role. | role | Role! |
| 4 | The unique identifier of the User | userId | ID! |
| 5 | The unique identifier of the Role | roleId | ID! |
| 6 | Indicates whether this userRole is set as the default option. | default | Boolean! |
| 7 | A relation to the data changes for this document. | dataChanges | [UserRoleDataChange!] |
| 8 | The date and time the record was created. | createdAt | DateTime! |
| 9 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 10 | The User who created this record. | createdByUser | User! |
| 11 | The date and time the record was last updated. | updatedAt | DateTime! |
| 12 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 13 | The User who last updated this record. | updatedByUser | User! |
| 14 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 15 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 16 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | UserRoleAggregate! |
| 17 | Custom fields configured for the data model. | _customFields | UserRole_CustomFields! |

##### data.modelDefinitions[154]
| Property | Value |
| --- | --- |
| description | The UserRoutePreference type represents a user's preferences (views, etc.) for a specific route in Fuuz. |
| fields | _complex array_ |
| name | UserRoutePreference |

###### data.modelDefinitions[154].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the user route preference record. | id | ID! |
| 2 | The URL path of the route these preferences are for, including any URL parameters. | path | String! |
| 3 | The unique identifier of the user these preferences are for. | userId | ID! |
| 4 | The user these preferences are for. | user | User! |
| 5 | The JSON data defining the user's preferences for the route. | preferences | JSONObject! |
| 6 | A relation to the data changes for this document. | dataChanges | [UserRoutePreferenceDataChange!] |
| 7 | The date and time the record was created. | createdAt | DateTime! |
| 8 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 9 | The User who created this record. | createdByUser | User! |
| 10 | The date and time the record was last updated. | updatedAt | DateTime! |
| 11 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 12 | The User who last updated this record. | updatedByUser | User! |
| 13 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 14 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 15 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | UserRoutePreferenceAggregate! |
| 16 | Custom fields configured for the data model. | _customFields | UserRoutePreference_CustomFields! |

##### data.modelDefinitions[155]
| Property | Value |
| --- | --- |
| description | An association between a user and setting values. |
| fields | _complex array_ |
| name | UserSettingValue |

###### data.modelDefinitions[155].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the UserSettingValue. | id | ID! |
| 2 | The ID of the user these setting values are linked to. | userId | ID! |
| 3 | The user linked to this UserSettingValue. | user | User! |
| 4 | The ID of the setting this user is linked to. | settingId | ID! |
| 5 | The setting. | setting | Setting! |
| 6 | The setting values associated with the user. | value | JSON! |
| 7 | A relation to the data changes for this document. | dataChanges | [UserSettingValueDataChange!] |
| 8 | The date and time the record was created. | createdAt | DateTime! |
| 9 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 10 | The User who created this record. | createdByUser | User! |
| 11 | The date and time the record was last updated. | updatedAt | DateTime! |
| 12 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 13 | The User who last updated this record. | updatedByUser | User! |
| 14 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 15 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 16 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | UserSettingValueAggregate! |
| 17 | Custom fields configured for the data model. | _customFields | UserSettingValue_CustomFields! |

##### data.modelDefinitions[156]
| Property | Value |
| --- | --- |
| description | The Visualization type represents the configuration for a visualization in Fuuz. |
| fields | _complex array_ |
| name | Visualization |

###### data.modelDefinitions[156].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Visualization. | id | ID! |
| 2 | The identifier of the Visualization Type the Visualization belongs to. | visualizationTypeId | ID! |
| 3 | A relation to the Visualization Type this Visualization belongs to. | visualizationType | VisualizationType! |
| 4 | The name of the Visualization. | name | String! |
| 5 | The configuration data used to control the appearance of the visualization. | configuration | JSONObject! |
| 6 | Parameters used to preview a visualization. | previewParameters | JSON |
| 7 | The identifier of the Saved Transform used as a datasource for this Visualization. | datasourceSavedTransformId | ID! |
| 8 | The Saved Transform used as a datasource for this Visualization. | datasourceSavedTransform | SavedTransform! |
| 9 | A relation to the data changes for this document. | dataChanges | [VisualizationDataChange!] |
| 10 | The date and time the record was created. | createdAt | DateTime! |
| 11 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 12 | The User who created this record. | createdByUser | User! |
| 13 | The date and time the record was last updated. | updatedAt | DateTime! |
| 14 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 15 | The User who last updated this record. | updatedByUser | User! |
| 16 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 17 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 18 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | VisualizationAggregate! |
| 19 | Custom fields configured for the data model. | _customFields | Visualization_CustomFields! |

##### data.modelDefinitions[157]
| Property | Value |
| --- | --- |
| description | The VisualizationType type represents the type of a visualization in Fuuz. |
| fields | _complex array_ |
| name | VisualizationType |

###### data.modelDefinitions[157].fields
| # | description| name| type|
| --- | --- | --- | --- |
| 1 | The unique identifier of the Visualization Type. | id | ID! |
| 2 | The label of the Visualization Type. | label | String! |
| 3 | The schema for the chart's datasource.  Used to generate form inputs that map onto the various properties of the datasource of this type of FusionChart, and to validate the visualization configuration. | configurationSchema | JSONObject! |
| 4 | The schema for the chart's datasource.  Used to generate form inputs that map onto the various properties of the datasource of this type of FusionChart, and to validate the visualization configuration. | extraConfigurationSchema | JSONObject! |
| 5 | The FusionCharts chart type to use for this visualization type. | chartType | String! |
| 6 | The description of the Visualization Type. | description | String |
| 7 | The image url for this visualization type. | imageUrl | String |
| 8 | A relation to the data changes for this document. | dataChanges | [VisualizationTypeDataChange!] |
| 9 | The date and time the record was created. | createdAt | DateTime! |
| 10 | The unique identifier of the User who created this record. | createdByUserId | ID! |
| 11 | The User who created this record. | createdByUser | User! |
| 12 | The date and time the record was last updated. | updatedAt | DateTime! |
| 13 | The unique identifier of the User who last updated this record. | updatedByUserId | ID! |
| 14 | The User who last updated this record. | updatedByUser | User! |
| 15 | The trace information for the record. Trace information provides details on how the most recent change to the record was made. | _trace | ApplicationTraceMetadata |
| 16 | The application metadata of the record. Application metadata provides details on what app or package the record belongs to. | _metadata | ApplicationMetadata |
| 17 | Aggregated data for the node, grouped by the selected node fields. | _aggregate | VisualizationTypeAggregate! |
| 18 | Custom fields configured for the data model. | _customFields | VisualizationType_CustomFields! |