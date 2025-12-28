# MAINTAIN Documentation
Generated from: docs.cognite.com (maintain)



<!-- SOURCE_START: docs.cognite.com/docs\cdf\maintain\guides\config.mdx -->
## File: docs.cognite.com/docs\cdf\maintain\guides\config.mdx

---
title: Configure Maintain
description: Configure Cognite Maintain by setting up data modeling spaces, access control, data ingestion, and application settings.
content-type: procedure
audience: administrator
experience-level: 200
lifecycle: use
article-type: article
---
<a id="before-you-start"></a>
## Before you start

Make sure you have the following:

- A project registered in the [CDF application](https://fusion.cognite.com).
- [The CDF API and the CDF application registered in Microsoft Entra ID](/cdf/access/entra/guides/configure_cdf_azure_oidc) and [Microsoft Entra ID and CDF groups set up to control access to CDF data](/cdf/access/entra/guides/create_groups_oidc).
- Assets in the CDF project populated in the [`CogniteAsset` concept](/cdf/dm/dm_reference/dm_core_data_model#asset).
- [URLs](/cdf/admin/allowlist) in your allowlist.

<a id="create-data-modeling-spaces"></a>
## Create data modeling spaces

Maintain stores asset data in [data models](/cdf/dm). You need to create at least 3 spaces to store your data and data models.

| Space name                     | Description                                                          |
| ------------------------------ | ---------------------------------------------------------------------|
| `cdf_apps_shared`              | This space holds user data, such as user profiles, and application configuration.               |
| `yourRootLocation_source_data` | This space holds space data (data coming from a customer source system, such as SAP) for a particular location. |
| `yourRootLocation_app_data`    | This space holds data coming from Maintain (from plans and layouts) for a particular location.   |

<Tip>
Create new `yourRootLocation_source_data` and `yourRootLocation_app_data` spaces for each root location you have if you want to implement access control per location.
</Tip>

You can create spaces in the following ways:

<Tabs>
<Tab title="Python SDK">
You can create the spaces using [Cognite Python SDK](https://cognite-sdk-python.readthedocs-hosted.com/en/latest/index.html). Use the following Python code and replace `yourRootLocation` with your root location/asset name in `yourRootLocation_source_data`, `yourRootLocation_app_data`.

```python
from cognite.client.data_classes.data_modeling import SpaceApply

# List of spaces to create
spaces_to_create = ["yourRootLocation_source_data", "yourRootLocation_app_data", "cdf_apps_shared"]

# Apply spaces
for space_name in spaces_to_create:
 client.data_modeling.spaces.apply(SpaceApply(space=space_name))
```
</Tab>

<Tab title="API">
Create the spaces using the [API endpoint](https://api-docs.cognite.com/20230101/tag/Spaces/operation/ApplySpaces).

Use the same names for the `space` and `name` attributes. The `space` attribute is also the space ID.
Replace `yourRootLocation` with your root location/asset name in `yourRootLocation_source_data`, `yourRootLocation_app_data`.
</Tab>
</Tabs>

<a id="set-up-access"></a>
## Set up access

You can use your existing identity provider (IdP) framework to manage access to Maintain and choose admin users. We currently support Microsoft Entra ID, Microsoft's cloud-based identity and access management service. By creating groups, you can assign different sets of capabilities to users and give each group different access rights.

<a id="create-admin-group"></a>
### Create admin group

<Tip>
If you have an admin group with the same capabilities, you can reuse that group and skip this step.
</Tip>

Create a group of users who can configure the Maintain application across all [locations](/cdf/locations/guides/index). See also how to [configure locations](#configure-locations).

<Steps>
<Step title="Navigate to Manage access">
Go to [<span class="ui-element">CDF</span>](https://fusion.cognite.com) > <span class="ui-element">Manage</span> > <span class="ui-element">Manage access</span>.
</Step>

<Step title="Configure capabilities">
Give `read` and `write` access to all capabilities with the scope `All`, or have the minimum set of capabilities.

| Capability type      | Action                         | Scope                            | Description                                                                                       |
| -------------------- | ------------------------------ | -------------------------------- | ------------------------------------------------------------------------------------------------- |
| Groups               | `groups: read`, `groups: list` | All                              | For Maintain administrators to grant access to users.                                              |
| Data models          | `dataModel: read`              | All                              | View data models.                  |
| Data model instances | `dataModelInstance:read`       | All                              | Access data organized in data models.                                 |
| Data model instances | `dataModelInstance:write`      | `cdf_apps_shared`                | Access and edit data organized in data models and spaces. |
</Step>
</Steps>

<a id="create-user-groups"></a>
### Create user groups

Create groups of users who can view activities, create plans and campaigns, and work with different layouts. You can create several groups with different capabilities. The suggested group is an example you can use.

| Name                                    | Description                                                                                                                            |
| --------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `maintain_plans` | Users in the group can view and edit plans and layouts. |

<Note>
Group names are suggestions and can differ from user to user.
</Note>

Assign the following group capabilities:

| Capability type      | Action                         |  Scope                                                                                                                                                  | Description                                               |
| -------------------- | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------- |
| Groups               | `groups: read`, `groups: list` | All             | View user groups.  |
| Data models          | `dataModel: read`              | `cdf_cdm`, `cdf_idm`, `cdf_maintain`, `cdf_apps_shared` , `idm_customer_datamodel_space`- the space where you extend the views | View and analyze the data created in Maintain.             |
| Data model instances | `dataModelInstance:read`       | `cdf_apps_shared`, `location_app_data_space`, `location_source_data_space` - a list of spaces to read the data from. Add all spaces you want to read the data from to the scope. | View and analyze the data created in Maintain.  |
| Data model instances | `dataModelInstance:write`      | `cdf_apps_shared` | Access and edit data organized in data models and spaces. |

<a id="ingest-data-into-data-models"></a>
## Ingest data into data models

All data for Maintain is stored within data models, with a few exceptions.

Generally, most tabular data is expected to exist inside RAW. Once the data is in RAW, use transformations to change this data into one of the following data models, depending on the view. Most of the data entries are required to use the application effectively. It's recommended to ingest the data entities in the order they appear in the table. Once you ingest `Assets` and `Maintenance Orders`, open the application to check that this data appears correctly.

| Data entity | View |
|-------------|------|
| Asset | cdf_cdm.Asset |
| Maintenance Orders (Work Orders) | cdf_idm.CogniteMaintenanceOrder |
| Operations | cdf_idm.Operations |
| Files (P&IDs) | cdf_cdm.File |
| Annotations (File Contextualisation) | cdf_cdm.DiagramAnnotation (edge) |
| 3D | Ingested through 3D APIs |

<Tip>
It's recommended to use the existing [3D APIs](https://api-docs.cognite.com/20230101/tag/3D-Models/operation/create3DModels) to ingest 3D data even though the data ends up in data modeling.
</Tip>

You can also use the [Cognite Toolkit](/cdf/deploy/cdf_toolkit) to ingest data into data models.

You don't need to ingest any data in the `Plan` and `Comment` data entries, as Maintain will use pre-existing data models to ingest data.

| Data entity | View |
|-------------|------|
| Plan | cdf_maintain.Plan |
| Comment | cdf_apps_shared.Comment |

## Configure Maintain

Once you have data ingested, the final step is to create a location configuration for Maintain to use.

<Steps>
<Step title="Sign in to Maintain">
Sign in to [Maintain](https://maintain.cogniteapp.com) with your organization ID.
</Step>

<Step title="Select spaces">
Select the spaces for the source data (`yourRootLocation_source_data`) and for the application data (`yourRootLocation_app_data`).

<Info>
A basic configuration is set up. {/* set up with a certain name? this is where you get your config.Name */} The configuration uses JSON and allows flexibility in how you view Maintain. You can see examples of [Field Configuration](#field_configuration) and [Feature Configuration](#feature_configuration).
</Info>
</Step>
</Steps>

<a id="extend-data-models"></a>
### Extend data models

[Views](/cdf/dm/dm_concepts#view) contain a group of properties that you can change for specific cases. By default, Maintain uses Cognite process industries data model that extends the core data model to meet the requirements for the process industries. You can customize entity definitions or views, such as `CogniteAsset` and `CogniteMaintenanceOrder`, by adding properties that are specific to your operations. For example, to extend the `CogniteMaintenanceOrder` property, you can create a new view and add the `Cost` field to this property.

In Maintain, you can filter on the entities, such as activities, assets, etc., with views defined in the `cdf_idm` (Process industries data model), `cdf_cdm` (Core data model) spaces, or the space with customized views.

To set up how you want to query CDF data modeling:

<Steps>
<Step title="Sign in to Maintain">
Sign in to [Maintain](https://maintain.cogniteapp.com) with your organization ID.
</Step>

<Step title="Access configuration">
Under Cognite Maintain, select the dropdown > **yourConfig** > edit (✏️).
</Step>

<Step title="Review view mappings">
In **Feature Configuration**, you'll see your view mappings.

```json
"viewMappings": {
    "asset": {
      "type": "view",
      "space": "cdf_cdm",
      "version": "v1",
      "externalId": "CogniteAsset"
    },
    "activity": {
      "type": "view",
      "space": "cdf_idm",
      "version": "v1",
      "externalId": "CogniteMaintenanceOrder"
    },
     "operation": {
      "type": "view",
      "space": "cdf_idm",
      "version": "v1",
      "externalId": "CogniteOperation"
    },
    "notification": {
      "type": "view",
      "space": "cdf_idm",
      "version": "v1",
      "externalId": "CogniteNotification"
    }
}
```
</Step>

<Step title="Configure spaces">
For each property, you can either keep the default space or the space where you created custom views.
</Step>

<Step title="Save changes">
Save to apply the changes.
</Step>
</Steps>

<a id="field-configuration"></a>
### Field configuration

<Tip>
The Maintain Field and Feature configurations are currently based on RAW JSON. It's recommended to copy the configuration into a text editor, edit it, and, once finished, paste it back into the configuration modal.
</Tip>

Field Configuration lets you choose which fields to show, how they look in the user interface, and how the user can use the fields. The Field Configuration is a JSON object that contains one key-value pair for each field in the activity view type that should appear in Maintain. The key should be the name of the field in the activity view type, while the value is a JSON object containing the configuration of that field in Maintain.

See the example:

```json
{
    "title": {
        "alias": "Activity Title",
        "displayInTable": 1
    },
    "status": {
        "alias": "Status",
        "displayInTable": 2
    }
}
```

In the example, Maintain displays two fields in the Activity table, with the “title“ field renamed to “Activity Title“ and “status“ renamed to “Status“. Also, the title should appear before the status.

Further configuration of the individual fields can be done by adding entries to Field Configuration. See the list of the basic configuration properties:

| Config key | Description | Type | Default if not set |
|------------|-------------|------|--------------------|
| `alias` | A string or an object containing a clear field name in different languages | string | Same value as the name of the field in the data model |
| `blacklisted` | Define whether this field should be completely excluded from being displayed in the application | boolean | false |
| `displayInGroupDropdown` | Define whether this field should be displayed in the group dropdown menu at the top level. You can group all fields under “Show all attributes“, but these are displayed first. | boolean | false |
| `displayInTable` | Define whether this field should be displayed in the table by default. The number provided defines the order in which the field will appear. If not provided, the field isn't displayed in the Activity table by default. | number | null |
| `additionalTableField` | Define whether this field should display an additional accessory field under it in the Activity table. The value is the name of the field to show in the Activity table. | string | null |
| `approximateDataSize` | Define how much space the field will occupy in the Activity table | string: 'small', 'medium' or 'large' | 'medium' |
| `displayInGantt` | Define whether the value of this field should be displayed under the activity bar in the Gantt lens | boolean | false |
| `displayInFilterMenu` | Define whether this field should be displayed by default in the filtering menu sidebar. Note that the user can add fields manually later, but this should be true for “common“ fields the customer may filter on. | boolean | false |
| `displayInSortDropdown` | Define whether this field should be displayed in the Sort dropdown | boolean | false |
| `prefix` | Enter the prefix to show next to the field value. For example, if the prefix is set to “$“ and the field value for an activity is “5“, the text to appear in the application will be “$5“. | string | null |
| `postfix` | Enter the postfix to show next to the field value. For example, if the postfix is set to “$“ and the field value for an activity is “5“, the text to appear in the application will be “5$“. | string | null |
| `decimalPoints` | Enter the number of decimal points to show if this is a number field. For example, if the value is “5.55555“ and `decimalPoints` is set to 2, the value to appear in the application will be “5.56“. | number | null |
| `fieldSubtype` | Define the field's subtype. This is used to define how filtering works in this field. Setting to “OPTION“ shows a dropdown list of all existing values. Setting to “FREETEXT“ will let the user filter it by a simple text string. Use freetext for any field where each value is unique, such as ID-type fields. | string: 'FREETEXT', 'OPTION' | 'OPTION' |
| `colorCoding` | Configures this field to have color coding based on the value. See [Color coding configuration](#color-coding-of-fields). | object | null |
| `colorCodingInGantt` | If “colorCoding“ is set, enable coloring Gantt for this field. | boolean | false |
| `colorCodingInTable` | If “colorCoding“ is set, use coloring on this field in the table. | boolean | false |
| `allowFilteringByMultipleValues` | On `fieldSubtype`: "FREETEXT" fields only; enables filtering by multiple values separated by comma | boolean | false |

<a id="color-coding-of-fields"></a>
#### Color coding of fields

You can configure color coding for the Gantt chart and the Activity table. If you've configured color coding for the specific fields, Maintain lets you color-code the Gantt bars. The color coding is defined by providing a specific color for each value of the field. By default, all values without explicit color coding will be gray. You can set the color coding in Field Configuration.

See the example to learn how to configure color coding on a `materialStatus` field.

```json
{
...
  "materialStatus": {
    ...
    "colorCoding": {
      "OK": {
        "background": "rgba(0, 100, 0, 1)",
        "color": "rgba(0, 255, 0, 1)"
      },
      "MISSING": {
        "background": "rgba(100, 0, 0, 1)",
        "color": "rgba(255, 0, 0, 1)"
      }
    },
    "colorCodingInGantt": true,
    "colorCodingInTable": true
  }
}
```

If the field value is "OK," it will be colored green; if it's "MISSING," it will be colored red. This field will be colored both in the table and the Gantt chart.

<a id="feature-configurations"></a>
### Feature configurations

Below are instruction to configure certain optional features of Maintain.

<a id="landing-page-configuration"></a>
#### Landing page configuration

You can configure the landing page to show the user's name. Since the name of a user depends on the customer's IdP, the way to format the name for each customer is different. By default, the landing page shows a welcome message without a name. If you set the `displayNameRegex` property in Feature Configuration to a regular expression with a capture group for “name“, it'll display only the matching substring in the header. For example, if the customer has a "last_name, first_name" format in their IdP, the following configuration can be used to display “Welcome, first_name“:

{/* is this instruction to show the name or name + last name? */}
```json
displayNameRegex: ".*, (?<name>.*)"
```

The format of the regular expression must follow the [JS standard](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_expressions/Cheatsheet).

<a id="language-configuration"></a>
#### Language configuration

Maintain supports multiple languages. When signing in for the first time, you can see a list of available languages. If you need certain languages to be available, set the `availableLanguages` property in Feature Configuration.

The example shows the setting for the English and German languages.

```json
"availableLanguages": ["en", "de"]
```

A full list of available languages is a work in progress. Reach out to the Cognite Maintain team for an up-to-date list.

<a id="customer-specific-language-configuration"></a>
#### Customer-specific language configuration

By default, Maintain supports localization of native application strings only. Custom strings aren't translated automatically and there are features that require manual translation:

- Naming of fields in the data model. See [Field Configuration](#field_configuration) > `alias`.

- Naming of fields in the activity creation modal. (Described in the activity creation section below on the "alias" property). {/* where below? modal or model*/}

<a id="time-and-date-configuration"></a>
#### Time and date configuration

To configure how dates are formatted globally in Maintain, set the following keys under `timezoneConfiguration` in Feature Configuration:

| Config key | Description | Type | Default if not set |
|------------|-------------|------|--------------------|
| `startOfWeek` | Set the starting day of the week. Possible values: `0` to start the week on Sunday; `1` to start the week on Monday. This affects date picker components in the application and also which day is the first day in the Gantt header on week granularity. | string | 1 |
| `dateFormat` | Set the global date format for dates. See [possible formats](https://day.js.org/docs/en/display/format). | string | DD/MM/YYYY |
| `dateTimeFormat` | Set the global date format for date and time, for example, where dates and time of the day are shown together. See [possible formats](https://day.js.org/docs/en/display/format). | string | DD/MM/YYYY HH:mm |

<a id="gantt"></a>
#### Gantt

The Gantt lens is added by default. To remove the Gantt lens, set "disabled" to `true` in Feature Configuration:

```json
"ganttConfiguration": { "disabled": true }
```

<a id="operations"></a>
#### Operations

To add operations, set  "enabled" to `true` in Feature Configuration:

```json
"subactivitiesConfiguration": { "enabled": true }
```

<a id="3d-and-digital-twin"></a>
#### 3D and digital twin

The 3D feature is added by default. To remove the functionality, set "disabled" to `true` in Feature Configuration:

```json
"threeDConfiguration": { "enabled": false }
```

<a id="basic-configuration"></a>
##### Basic configuration:

First, 3D configuration requires configuring the customer's 3D models (Locations). You can do this in Maintain > <span class="ui-element">your.Config</span> > <span class="ui-element">Root Location Configuration</span>.
You need to make an entry in this JSON object for each location (e.g., a 3D model) that the customer wants to visualize.

Each entry needs to have three entries:

| Entry | Description |
|-------|-------------|
| `label` | The human-readable name of the location that appears in the application. |
| `value` | The unique identifier of the site; can be anything as long as it's unique for the project. |
| `modelId` | The ID of the CDF model for this location.|

The example shows the configuration for a project that has two locations, Site A and Site B:

```json
{
  "locations": [
    {
      "label": "Site A",
      "value": "SITE_A",
      "modelId": 1234
    },
    {
      "label": "Site B",
      "value": "SITE_B",
      "modelId": 5678
    }
  ]
}
```

{/* If all criteria are met, the activity will show up on top of the asset annotation in the 2D lens if the document is open. // we started with 3D and now it's about 2D and asset annotation? double check */}

<a id="2d-and-industrial-canvas"></a>
#### 2D and Industrial Canvas

The 2D and Industrial Canvas functionality is added by default. To remove this functionality, set "disabled" to `true` in Feature Configuration:

```json
"canvasConfiguration": { "disabled": true }
```

By default, all supported documents ingested into the CDF project can be opened in Maintain. However, additional configuration is required to fully use the 2D features.

<a id="supported-document-types"></a>
##### Supported document types

Maintain supports the 'application/pdf' MIME type (Multimedia Internet Mail Extensions) documents. Other document types won't appear in the search in Maintain.

<Note>
All files must have an external ID, otherwise, you won't open a file.
</Note>

<a id="document-naming-and-grouping"></a>
##### Document naming and grouping

Documents can be grouped by their type. By default, documents have the `Unknown` type. Maintain supports the following types without additional configuration, but other types can be added on request:

| Value in 'Type' metadata | Description |
|--------------------------|-------------|
| LAY | Layout diagram |
| PID | P&ID |
| PLT | Plot plans |
| SLD | Single-line diagrams |
| UNK | Unknown |

<Note>
The document type is used for logical grouping only and doesn't affect how Maintain interacts with documents.
</Note>

In CDF, the metadata fields for documents in Maintain are `Name` for the document title and `Title` for the document description.

<a id="layout"></a>
#### Layout

To add layouts, set "enableCustomLayouts" to `true` in Feature Configuration:

```json
"enableCustomLayouts": true
```

Also it required presence of attribute activeColorCoding in MaintainLayout view (data model Maintain).

```javascript
type MaintainLayout {
  ...
  activeColorCoding: String
  ...
  }
```

<a id="plan-analysis-budget-and-hourly-optimization-and-resources-allocation"></a>
#### Plan Analysis, Budget and Hourly Optimization, and Resources Allocation

To add Plan Analysis and Resources Allocation (contains Budget and Hourly Optiomization), set "enablePlanAnalysis" and "enableResourceAllocation" to `true` in Feature Configuration:

```json
"cabinetConfiguration": {
  "enablePlanAnalysis": true,
  "enableResourceAllocation": true
}
```

You can see the bottom panel with <span class="ui-element">Plan Analysis</span>, <span class="ui-element">Budget and Hourly Optimization</span> , and <span class="ui-element">Resources Allocation</span> for the created plans only.

<Note>
To work with Resource Allocation, you need to add values for `personHours` and `numberOfMainResource` in the `CogniteOperation` view when ingesting data in your data model.
</Note>

<!-- SOURCE_END: docs.cognite.com/docs\cdf\maintain\guides\config.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\cdf\maintain\guides\import_activities.mdx -->
## File: docs.cognite.com/docs\cdf\maintain\guides\import_activities.mdx

---
title: Import activities from a CSV file
description: Import multiple activities into Maintain by uploading a CSV file with activity data.
content-type: procedure
audience: businessUser
to-L10N: true
experience-level: 200
lifecycle: use
article-type: article
---
The activities can be visualized and modified as activities created in Maintain.

<Steps>
<Step title="Download the CSV file template">
The imported CSV file needs to conform to your project-specific template. To download **maintain-import-template.csv**:

1. Navigate to **Activities** and select **Add activity**.
1. Select **Import activities**.
1. Select **Download template**.
</Step>

<Step title="Populate the CSV file template">

The CSV template will only contain a single row of values representing the possible values you can provide for an imported activity. This row is called the header row and shouldn't be removed when populating the template.

<Tip>
While the header **row** must not be removed, you can remove specific values from the header or change the order of the values. For example, if a certain field isn't relevant to import in a specific scenario, you can remove it from the header and the subsequent rows. This will result in the field being left blank on all imported activities.

</Tip>

To import a single activity, add a new row to the template and enter the appropriate values separated by commas. Each value in the new row should contain the value you want to populate on the respective header row that was provided in the template.

For example, if your template contains the following header row: `title,status,department`, create a CSV file with an additional row:

```
title,status,department
Erect scaffolding,Done,Rigging
```

You'll create an activity with the title "Erect scaffolding", status "Done," and department "Rigging" when imported.

<a id="format-date-fields"></a>
### Format date fields

Some activity fields in **Maintain** represent dates and must have a specific format to be properly imported. Date fields can be identified in the CSV template by a special header value in the form `startTime (DD/MM/YYYY)`. In this example, `startTime` is the name of the field, while `DD/MM/YYYY` is the date format that needs to be used in subsequent rows of the CSV file.

For example, importing the follwoing file will result in the activity being created with `startTime` set to December 10th 2023.

```
title,startTime (DD/MM/YYYY)
Erect scaffolding,10/12/2023
```

<a id="format-boolean-fields"></a>
### Format boolean fields

Some activity fields in **Maintain** represent boolean values, such as `true` or `false`. Boolean fields can be identified in the CSV template by a special header value in the form `isActive (true/false)`. These fields only have two valid values, `true` or `false`.

For example, importing the following file will result in the activity being created with `isActive` set to `false`.

```
title,isActive (true/false)
Erect scaffolding,false
```

<a id="format-values-containing-commas"></a>
### Format values containing commas

Due to the CSV format separating values using commas, be careful when an activity field also needs to contain a comma. **Maintain** processes CSV files according to the [RFC 4180 standard](https://www.rfc-editor.org/rfc/rfc4180), which requires values containing commas to be wrapped in double quotes.

For example, importing the following file will result in the activity being created with `city` set to `Boston, MA`.

```
title,city
Erect scaffolding,"Boston, MA"
```

<a id="unset-values"></a>
### Unset values

You can leave the field blank on non-required activity fields to represent missing or irrelevant values for the specific activity.

For example, importing the following file will result in the activity being created with `state` set to `New York`, but it will have no value in the `city` field.

```
title,city,state
Erect scaffolding,New York
```

<a id="connect-an-activity-to-an-asset"></a>
### Connect an activity to an asset

**Maintain** supports linking activities to CDF assets, which is used to contextualize the activity in 3D models, documents, PSN, and more. For **Maintain** to create the appropriate link to an asset in CDF, you must provide the CDF asset's external ID in the import template.

If you have asset contextualization in your project, there will be a special `assetExternalId` field in the template that you need to populate with the external ID of the activity's asset.

For example, when importing the following file and assuming a CDF asset exists with external ID `NY_BUILDING_23`, the activity will be contextualized to this asset when ingested into Maintain.

```
title,assetExternalId
Erect scaffolding,NY_BUILDING_23
```

If the provided external ID doesn't export, the user will see an error during import.

<Tip>
End-users may not know the external ID of the asset to which the activity is linked. If it's necessary to provide end-users with a list of the possible assets and their external IDs, we recommend retrieving this data using CDF's [asset API](https://api-docs.cognite.com/20230101/tag/Assets) or other data extractors.
</Tip>
</Step>

<Step title="Import a populated CSV file">

Once you have a populated CSV template, you can import it into **Maintain**:

1. Navigate to **Activities** > **Add activity**.
2. Select **Import activities**.
3. Select the **Click to select CSV file to import** field.
4. Select and upload the populated CSV file from your computer.
5. Verify that you imported the file by checking the **File Inspector** section. A green message should show the number of rows to be imported and if you have any warnings or errors.
6. If there are no errors, select **Import** to start the import process.
7. When the import succeeds, you'll see a green success message with the **Show activities** button.
8. Optional. Select **Show activities** to navigate to the imported activities and check that they look correct according to the CSV file.
</Step>
</Steps>

<!-- SOURCE_END: docs.cognite.com/docs\cdf\maintain\guides\import_activities.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\cdf\maintain\guides\set_work_scope.mdx -->
## File: docs.cognite.com/docs\cdf\maintain\guides\set_work_scope.mdx

---
title: Plan activities
description: Create and manage plans to schedule activities, identify conflicts, and optimize work scope.
content-type: procedure
audience: businessUser
to-L10N: true
experience-level: 100
lifecycle: use
article-type: article
---
Open **Plans** to plan and schedule activities and collaborate with coworkers. Open **Activities** to see all activities in your location. You can filter the list to find activities relevant to your plan.

<Steps>
<Step title="Create a plan">

Create a plan with a list of activities to be performed during an event, such as a campaign. You can find all plans under the **Plans** tab and recently created and viewed plans on the home page.

1. Select **Plan** > + **Create new plan**.
2. Name your plan and select attributes. You can create an empty plan and add activities later or use other attributes to filter on activities you want to include in the plan.
3. Select **Preview** to view all the activities to be added to your plan, and select **Apply** to create the plan.
</Step>

<Step title="Review your plan">

Open **Plan analysis** to assess your plan, then identify possible conflicts and necessary actions by viewing your plan through multiple lenses.

<a id="analyze-your-plan"></a>
### Analyze your plan

**Plan analysis** shows the plan breakdown for activity attributes such as job type, material status, long lead items, vendors' requirements, hot work, and lifting operations. You can also see the status of work preparations, such as permits and work package documents.

<a id="identify-conflicts-and-improvements"></a>
### Identify conflicts and improvements

Catch opportunities and avoid conflicts using the lenses:

- **Canvas**: View all documents related to your plan, such as **engineering diagrams** (P&IDs) and plot plans. Select a P&ID to see all the activities in a plan overlaid on the engineering diagram. You can also annotate the diagram with text, color, and lines to create isolation plans to support your plan optimization. Add comments to collaborate with co-workers or operations on, for example, isolation plans.

- **3D**: Add spatial context to see how activities and risks are placed in relation to each other. Zoom into the equipment to understand access, check the surroundings, investigate if you need more equipment, and locate other nearby activities.

<a id="discover-opportunities"></a>
### Discover opportunities

Use **Compare** to identify opportunities. You can view and add activities outside of your plan or include them as a reference while scoping your work.

1. Select **Compare >** **Activities not in my plan** to find activities you want to add to your plan. The activities inside and outside the plan have different color codes in the lenses.
2. Optional. Filter activities to narrow down the search.
3. Select the activities you want to add to your plan.

<Tip>
It can be helpful to compare the activities that occur in the same time frame, in the same location, with the same job type, etc.
</Tip>

Use **Group** <Icon icon="folder-tree" /> to analyze your campaign or shutdown in multiple ways, for example, by platform, deck level, grid level, and job type.

- You can configure, rearrange, and add new activity attributes to the Group list.
- The table view shows the result of your grouping. You can interact with and rearrange the grouped activities.

Use **Plan analysis** to view how your plan is impacted as you add or remove activities; if you are overloaded or underloaded. If you are overloaded, remove an activity to see how it impacts the plan. You can also sort by order type, OLAFD date, and total hours.

Use **Budget and hourly optimization** to create a baseline for comparison: POB, duration, non-productive hour %, and shift duration.

<Tip>
Create **Layouts** to save your preferred filters, grouping, and plan analysis.
</Tip>
</Step>

<Step title="Create visibility on the schedule">

Maintain connects scheduling data from several source systems to create visibility around one schedule. You can comment and collaborate on the schedule with your co-workers in Maintain. Schedule editing requires additional configuration if this isn't turned on in your environment.

The **Gantt chart** <Icon icon="chart-no-axes-gantt" /> shows the activities on a timeline to identify the best sequence of activities. It's supported by color-coding that shows the material status and the trades.

- Open the **Gantt chart** to sequence and schedule activities.
- Add comments to collaborate with your co-workers.
</Step>

<Step title="Export contextualized documents">

Maintain automatically maps activities to functional locations on documents such as P&IDs and plot plans. This mapping can be exported to PDF files to share your plan with colleagues and vendors.

- Next to **Add activity**, select **More options** (&#8942;) > **Export PDF** to download the P&IDs (with markup, colors, and isolation boundaries), with notes and activities in the plan, and share them with colleagues, vendors, and contractors.

- You can export the activities list and Gantt chart to PDF. The Excel export is only for the table view.

- Depending on the filter you use to view your activities, **Documents** lets you search for documents and tags available in that filter view that you can download as a PDF.

<Tip>
Switch between lenses and tools to optimize your plan.
</Tip>
</Step>
</Steps>


<!-- SOURCE_END: docs.cognite.com/docs\cdf\maintain\guides\set_work_scope.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\cdf\maintain\index.mdx -->
## File: docs.cognite.com/docs\cdf\maintain\index.mdx

---
title: About Cognite Maintain
description: Cognite Maintain helps you plan, review, and improve your field activities for medium to long-term projects.
content-type: concept
audience: businessUser
to-L10N: true
mode: 'wide'
experience-level: 100
lifecycle: use
article-type: map
---
<Warning>
The features described in this section are currently in [beta](/cdf/product_feature_status) testing and are subject to change.
</Warning>

With Maintain, you can visualize activities and all related data to find potential issues or opportunities, create detailed **campaign plans** for events like shutdowns and turnarounds, discover **opportunity scopes** and reduce isolation frequency and production loss, then monitor the progress of your activities, make adjustments, and **analyze maintenance trends** over time.

<a id="get-started"></a>
## Get started

<CardGroup cols={3}>

<Card title="Plan activities" icon="calendar-check" href="/cdf/maintain/guides/set_work_scope">
  Create and manage plans to schedule activities, identify conflicts, and optimize work scope.
</Card>

<Card title="Import activities from CSV" icon="upload" href="/cdf/maintain/guides/import_activities">
  Upload activity data in bulk from external sources using CSV files.
</Card>

<Card title="Sign in to Maintain" icon="arrow-right" href="https://maintain.cogniteapp.com/">
  Access the Maintain application to start planning and reviewing your field activities.
</Card>
</CardGroup>


<!-- SOURCE_END: docs.cognite.com/docs\cdf\maintain\index.mdx -->

================================================================================
