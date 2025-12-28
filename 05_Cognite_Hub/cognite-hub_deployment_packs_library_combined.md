# 42_Knowledge Base - Deployment Packs Library
Generated from: Obsidian Web Clipper (42_Knowledge Base)

================================================================================

<!-- SOURCE_START: Best Practices Library\Deployment Packs Library\Cause Map in Canvas with Atlas AI Agents Cognite Official  Cognite Hub.md -->
## File: Best Practices Library\Deployment Packs Library\Cause Map in Canvas with Atlas AI Agents Cognite Official  Cognite Hub.md

---
title: "Cause Map in Canvas with Atlas AI Agents [Cognite Official] | Cognite Hub"
source: "https://hub.cognite.com/deployment-packs-472/cause-map-in-canvas-with-atlas-ai-agents-cognite-official-4804"
created: 2025-12-28
description: "Root Cause Analysis (RCA) is a method used to identify the underlying causes of equipment failures. The goal of RCA is to understand why failures occur..."
tags:
  - "CDF"
  - "CogniteDataFusion"
image: "https://uploads-eu-west-1.insided.com/cognite-en/attachment/1440x640/2f7da46f-cb4b-455e-82c8-b78f968aa666_thumb.png"
---
![Cause Map in Canvas with Atlas AI Agents [Cognite Official]](https://uploads-eu-west-1.insided.com/cognite-en/attachment/2f7da46f-cb4b-455e-82c8-b78f968aa666_thumb.png)

- [palronning](https://hub.cognite.com/members/palronning-546)
- Architect
- 17 replies

Root Cause Analysis (RCA) is a method used to identify the underlying causes of equipment failures. The goal of RCA is to understand why failures occur so that measures can be taken to prevent their recurrence.

The Industrial Canvas is particularly useful for conducting RCA by providing a structured environment to gather data and identify the underlying causes of equipment failures. The Cause Map agent further enhances the user experience by guiding the retrieval of relevant data and suggesting a cause map aligned with ISO 14224. It is especially useful for personnel such as site reliability engineers and has proven to significantly reduce the time spent on RCA.  

**This how-to article shows how to implement an Atlas AI agent that can be adapted and extended to suit the particular needs of customers.**

Requirements:

- Atlas AI must be enabled in your Cognite Data Fusion environment. Contact your Cognite Customer Business Executive or contact@cognite.com for more information.
- Equipment data must be available in Cognite Data Fusion Data Modelling
- Access to create and publish Agents and Agent Tools
- Access to deploy Cognite Function with session credentials

## Overview of the agent

  
When configured, the new agent will be available in the list of agents under the Atlas icon at the top left of the Industrial Canvas. A user can add it to both new and existing Canvases.

The Agent’s purpose is

- To help the user retrieve relevant data for the equipment that has failed
- To render a cause map of potential causes for the way in which it failed

In ISO 14224 terms, the cause map is suggested based on the Equipment’s ***Equipment Class*** and its possible ***Failure Mechanisms***, given the ***Failure Mode*** that instigated the root cause analysis process in the first place.

As a starting point, the agent’s goals, instructions and tools laid out in this article are designed to assist the user find the relevant data in the Core Data Model (CDM) and Process Industries Data Model (IDM). Where data is unavailable, the agent will ask the user for guidance.  
  
Note:

The agent uses a Cognite Function (source code provided) to retrieve a qualified cause map. The function currently supports equipment classes Heat Exchangers and Pumps. For other equipment classes, the agent can leverage the large language model to suggest a cause map. The result depends on the Language Model and may contain mistakes, hence it must be reviewed by domain experts before the RCA is concluded. The LLM fallback can be disabled by changing the instructions.

## Configuring the agent

1. Switch to the Atlas AI workspace in Cognite Data Fusion.
2. Create a new Agent and enter the following information, adjusting to your needs

| **Field** | **Example/Instruction** |
| --- | --- |
| Agent Name | Cause Map Agent |
| Description | An agent that helps the user find data related to Equipment and draws a cause map based on failure mode |
| Sample questions | 1. Retrieve equipment <sample equipment external id> 2. Find work orders for <sample equipment external id> 3. Give me a cause map for <sample equipment external id> |
| Language model | azure/gpt-4o or similar |
| Goal | The goal is to retrieve the right equipment and its data and then populate the canvas with a root cause analysis cause-map fetched from the RCA function on a particular equipment provided by the user. |
| Instructions | \-Converse with the user to check if they need an RCA cause map for any equipment.  \-Ask for the equipment name.  \-Use the find equipment tool.  \-Retrieve and provide the equipment details.  \- Ask the user if they also want to see the time series data or if they want an RCA map for the equipment.  \-If they want to see the time series data then Use the find time series tool to find the time series data linked to the equipment and ask if they want to see an rca cause map  \-If they say yes, then ask for the equipment class and failure mode.  \-Tell the user that the currently available equipment classes are Pumps(PU) and Heat exchangers(HE) and the currently available failure modes are AIR,BRD,ELP,ELU,ERO,FTS,HIO,INL,LOO,NOI,OHE,PDE,PLU,SER,STD,UST,VIB  The above failure codes stand for: Abnormal Instrument Reading, Breakdown, External leakage - process medium, External leakage - utility medium, Erratic output, Failure to start on demand, High output, Internal leakage, Low output, Noise, Overheating, Parameter deviation, Plugged / Choked, Minor in-service problems, Structural deficiency, Spurious stop, Vibration.  \-Once the user chooses the equipment class and failure mode. Pass the equipment class code(PU or HE) and failure mode as the input to the RCA function.  \-If the function returns a cause\_map then, Use the "cause\_map" content to build out a cause map and add it automatically to the canvas.  \-When you build out the cause map, make sure to use the entire data returned by the function.  \-First level of the map has to be the failure mode (for example - AIR) and then from level 2 start using the data received from the function and follow the hierarchy in the data depending on indentations.  \-No shortcuts to be used even when you see repeating patterns in data. The JSON hierarchy always needs to be followed.  \-Do not overlap the items on the map.  \-Always use the add\_cause\_map\_to\_canvas function to add the map to the canvas. |

1. Add the following Tools to the agent:

| **Find Equipment** |  |
| --- | --- |
| Tool Name | Find Equipment |
| Tool Instructions | \-Use this tool to retrieve information about equipment. Retrieve information from all the fields, especially a field named files which has to be passed in to find files tool  \-Find from CogniteEquipment using the name field.  \-When entering an equipment name like "20TT1042", do not split it (e.g., "20 TT 1042"). Pass the string exactly as it is to the query.  \-When retrieving equipment:  \-If no exact match is found, list the three closest matches and inform the user that the exact equipment could not be found, but similar ones are available.  \- Also fetch the "files" field.  \- Do not use sorting |

| **Find Time Series** |  |
| --- | --- |
| Tool Name | Find Time Series |
| Tool Instructions | \-when querying time series, filter on equipment space and external id. |

| **Call Function** |  |
| --- | --- |
| Tool Name | RCA Function |
| Tool Instructions | \-Call the function when the user explicitly asks for Root cause analysis or cause map on a particular equipment.  \-Provide the below JSON input to the function by replacing values for equipment\_class and failure\_mode with the details fetched from the user. Replace the canvas\_name and canvas\_external\_id values with the name and external id of the canvas on which the agent is being used.  {  "equipment\_class": "",  "canvas\_name": "",  "canvas\_external\_id": "",  "failure\_mode": ""  }  \-After the function executes, summarize what has been done based on the function response. |
| Function name | Cause Map Agent Function |
| Max polling time in minutes | 1 |
| Schema | {  "type": "object",  "properties": {  "equipment\_class": {  "type": "string",  "description": "The type of the equipment. This field is optional."  },  "canvas\_name": {  "type": "string",  "description": "The name of the canvas on which the agent is being used. Use an empty string if not available."  },  "canvas\_external\_id": {  "type": "string",  "description": "The external ID of the canvas on which the agent is being used. Use an empty string if not available."  },  "failure\_mode": {  "type": "string",  "description": "The failure mode for root cause analysis. This field is optional."  }  }  } |

1. Upload the Cognite Function

***Manual method:***

- Download Function folder from [https://github.com/cognitedata/library/tree/main/modules/root\_cause\_analysis/rca\_with\_atlas/functions/rca\_canvas\_builder](https://github.com/cognitedata/library/tree/main/modules/root_cause_analysis/rca_with_atlas/functions/rca_canvas_builder) and create a Zip file from it.
- Upload the Zip file to Cognite Data Fusion. Go to Data Management → Build Solutions → Functions.
- Add the Zip file in the interface
- Enter the following settings, then click Upload

| Dataset | For example <rca-agent-function-dataset> |
| --- | --- |
| Function name | Cause Map Agent Function |
| ExternalId | Match external id from Function tool setup |

**Toolkit method**

- Download module from [https://github.com/cognitedata/library/tree/main/modules/root\_cause\_analysis/rca\_with\_atlas](https://github.com/cognitedata/library/tree/main/modules/root_cause_analysis/rca_with_atlas) and add it to your modules folder
- Use Toolkit to deploy the function to your environment

## Using the Agent

1. Open or Create a new Canvas and give it an appropriate name
2. Click the Atlas Icon. The Sidebar opens. Find the agent in the list and click it.
![[c01f2a3847cb7373bcb17bf37f4278e5_MD5.webp]]

![[f017a68134696ffeed06f110e1567c56_MD5.webp]]

![[2cd502a6558839a4a3650587351b8cc7_MD5.webp]]

The agent should now be able to assist the user in retrieving data related to the failed equipment, and draw a cause map.

×

### Cookie settings

  

We use 3 different kinds of cookies. You can choose which cookies you want to accept. We need basic cookies to make this site work, therefore these are the minimum you can select. [Learn more about our cookies.](https://hub.cognite.com/site/terms)

<!-- SOURCE_END: Best Practices Library\Deployment Packs Library\Cause Map in Canvas with Atlas AI Agents Cognite Official  Cognite Hub.md -->

================================================================================

================================================================================

<!-- SOURCE_START: Best Practices Library\Deployment Packs Library\Configuring the Infield QS Deployment Pack with the Toolkit  Cognite Hub.md -->
## File: Best Practices Library\Deployment Packs Library\Configuring the Infield QS Deployment Pack with the Toolkit  Cognite Hub.md

---
title: "Configuring the Infield QS Deployment Pack with the Toolkit | Cognite Hub"
source: "https://hub.cognite.com/deployment-packs-472/configuring-the-infield-qs-deployment-pack-with-the-toolkit-5825"
created: 2025-12-28
description: "The InField QS Deployment Pack helps you configure the InField application for your project by providing the required data model, environment setup, and..."
tags:
  - "CDF"
  - "CogniteDataFusion"
image: "https://uploads-eu-west-1.insided.com/cognite-en/attachment/e328b668-eef0-457a-b6a6-422e513533de_thumb.png"
---
---

+1

- [Mittinpreet Singh Nayyar](https://hub.cognite.com/members/mittinpreet-singh-nayyar-7051)
- Practitioner

The InField QS Deployment Pack helps you configure the InField application for your project by providing the required data model, environment setup, and standard configuration aligned with InField 2.0 best practices This guide walks you through the purpose of InField, prerequisites, configuration steps, data-model setup, and how to deploy your InField QS module using the Cognite Toolkit!-->

![[12ad4ebb6a98b7ba4b69ac272200ddc1_MD5.webp]]

### What is InField?

**InField** is Cognite’s Industrial application for field execution and workforce coordination. It replaces paper-based data gathering and manual reporting with a structured, digital workflow.

**For field workers**

Use InField to:

- View and execute tasks
- Capture measurements, inspections, and observations
- Log field findings with traceability

For supervisors & planners

Use InField to:

- Plan, schedule, and assign work activities
- Manage locations, templates, and execution flows
- Monitor operational progress

InField relies heavily on the data model configuration in CDF. As InField QS transitions from an APM data model to a fully IDM-based model, additional care is required during deployment

## Why an InField QS Deployment Pack is Needed

Configuring the InField Web App is inherently complex due to:

- Continuous updates to InField’s data model
- Dependency on IDM-based data structures
- Multi-space configuration (source, app data, APM config, user profile spaces)
- Manual steps required inside the InField UI
- Need for correct identity and group mapping from Microsoft Entra ID

The QS Deployment Pack simplifies this by providing:

- Predefined InField spaces
- Standard groups & permissions
- Pre-configured data-model structure
- Environment variables ready for location-specific customization
- Repeatable deployment steps across environments

**Module Components**

The **InField QS Deployment Pack** contains the following module groups:

**1\. Common Module (cdf\_infield\_common)**

Includes:

- Admin group definitions
- Base data-model spaces
- Application configuration container

**2\. Location-Specific Module (cdf\_infield\_location)**

Includes:

- Source data spaces (SAP / CMMS imports)
- InField application data per location
- Location-specific user group bindings
- View mappings

**3\. Supporting Modules**

- IDM Data Model
- APM base model (used internally for compatibility)

### Prerequisites

Before configuring InField in your Toolkit project, ensure:

**CDF Application Setup:** Your project must already support:

- IDM Data Model
- Assets
- Time series
- Activities, operations & notifications (IDM source)

> *APM-based MaintenanceOrder (Project model) is* *not supported by InField.*

> Use cdf\_idm/CogniteMaintenanceOrder/v1

> Make sure your Project is DMOnly

This ensures the InField Web App can fully access the IDM-based models.  
  
**Access Management Setup (Toolkit-Handled)**

The Toolkit auto-creates required groups under:

`modules/infield/cdf_infield_common/auth`

**Admin Group**

> **applications-configuration.group.yaml**

  
Grants InField admin rights across locations.

**User Group Roles**

Predefined groups:

> - **infield\_location\_normal\_users\_source\_id:**
> 
> **Optional Best Practice:** Create 4 groups to segment the access for infield application
> 
> - **infield\_viewer\_role** – read-only access
> - **infield\_normal\_role** – standard technician access
> - **infield\_checklist\_admin\_role** – checklist management
> - **infield\_template\_admin\_role** – template management

All groups require correct **Entra ID Source IDs**, provided by your identity team.

### Data Modeling Space Configuration

The Deployment Pack defines all necessary spaces:

| **Space** | **File Location** | **Purpose** |
| --- | --- | --- |
| **cognite\_app\_data** | infieldAppData.space.yaml | User profiles & application metadata |
| **APM\_Config** | modules/common/cdf\_apm\_base | APM compatibility configs |
| **sp\_asset\_{{location}}\_source** | infieldLocationSourceData.space.yaml | Source data (SAP/CMMS) |
| **sp\_infield\_{{location}}\_app\_data** | infieldLocationAppData.space.yaml | InField app-level data |

## Follow the Steps to Configure Infield

### Step 1: Enable External Libraries

Edit your project's cdf.toml and add:

```
1[alpha_flags] 
2
3external-libraries = true 
4
5[library.cognite] 
6
7url = "https://github.com/cognitedata/library/releases/download/latest/packages.zip" 
8
9checksum = "sha256:795a1d303af6994cff10656057238e7634ebbe1cac1a5962a5c654038a88b078"
```

This allows the Toolkit to retrieve official library packages.

To help improve the Deployment Pack:

```
1cdf collect opt-in
```

### Step 3: Add the Module

Run:

```
1cdf modules init .
```

### Step 4: Select the Infield QS (NOTE: use Space bar to select module)

From the menu, select:

`Modules → Accelerator → InField Quickstart (QS)`

Toolkit will:

> - Download the InField QS package
> - Update configuration
> - Add module folders to your project

### Step 5: Update Configuration Variables

Open **config.dev.yaml**

Only a few variables must be manually updated.

> **1\. Application Configuration Source ID**
> 
> Provided by Entra ID team:
> 
> `infield: modules/accelerators/infield_quickstart/config.dev.yaml`

You need to manually create the app registration (service principal and client secret), then create a group and add the service principal to that group. Use the Group ID of the ApplicationConfiguration group as the source ID

> **2\. Location Identifier**
> 
> `infield: modules/accelerators/infield_quickstart/config.dev.yaml first_location: <location_name>`
> 
> **3\. Location User Group Mappings**
> 
> `infield_quickstart: cdf_infield_location: applicationsconfiguration_source_id: <source_id> infield_location_normal_users_source_id: <users_group_source_id>`
> 
> **4\. Environment Setup** Update project name:
> 
> `environment: modules/accelerators/infield_quickstart/config.dev.yaml project: <cdf_project_name> name: dev`

### Step 6: Deploy the Configuration

Run in order:

`cdf build --env=test `  

`cdf deploy --dry-run cdf deploy`

If the deployment succeeds, your CDF environment now contains:

- Spaces
- Containers
- Views
- Groups
- Permissions
- Location-level structures

### Step 7: Perform Manual UI Configuration in InField

Toolkit does **not** automate these steps.

**1\. Add Location**

Go to:

> **InField → Settings (⚙️) → + Add Location**

**2\. Configure Location Details**

> - Name
> - Description
> - Location code (if applicable)

**3\. View Mappings**

Set mappings for:

> - Asset views
> - Activities & operations
> - Notifications
> - Maintenance Order (use IDM view)

**4\. Select Spaces**

Under **InField Location Configuration**:

> - **Customer Instance Space** → select source data space
> - **InField Instance Space** → select app data space

**5\. Asset Explorer Setup**

Configure:

> - Asset hierarchy
> - Display fields
> - Associated timeseries

To complete the location setup:

> 1. Load **Asset hierarchy** → IDM
> 2. Load **Activities & Operations** → from SAP / CMMS
> 3. Load **Notifications** → IDM
> 4. Load **Maintenance Orders** → **IDM CogniteMaintenanceOrder/v1**

All data must be mapped to the correct location spaces.

## Verification

After successful deployment & UI configuration:

### Search Functionality

Use the search icon (top right) to look up:

- Assets
- Activities
- Work orders
- Notifications

### InField Dashboard

You should see:

- Asset details
- Associated time series
- Maintenance Order
- Activity schedule

## Support

If you encounter issues with this module or deployment, consult Cognite documentation or contact your Cognite representative.

!-->

×

### Cookie settings

  

We use 3 different kinds of cookies. You can choose which cookies you want to accept. We need basic cookies to make this site work, therefore these are the minimum you can select. [Learn more about our cookies.](https://hub.cognite.com/site/terms)

<!-- SOURCE_END: Best Practices Library\Deployment Packs Library\Configuring the Infield QS Deployment Pack with the Toolkit  Cognite Hub.md -->

================================================================================

================================================================================

<!-- SOURCE_START: Best Practices Library\Deployment Packs Library\Contextualization Quality Dashboard  Cognite Hub.md -->
## File: Best Practices Library\Deployment Packs Library\Contextualization Quality Dashboard  Cognite Hub.md

---
title: "Contextualization Quality Dashboard | Cognite Hub"
source: "https://hub.cognite.com/deployment-packs-472/contextualization-quality-dashboard-5810"
created: 2025-12-28
description: "Overview	Module Components	Deployment	Configuration	Post-Deployment: Running the Function	Launching the Dashboard	Scheduling the Function	Troubleshooti..."
tags:
  - "CDF"
  - "CogniteDataFusion"
image: "https://uploads-eu-west-1.insided.com/cognite-en/attachment/1440x640/24f97977-1641-433d-9685-70bfa4fde82b_thumb.png"
---
![[e832c8c1f89e23772f232a83f8a364d1_MD5.webp]]

- [Charan Raju](https://hub.cognite.com/members/charan-raju-7025)
- Practitioner

## Overview

The **Contextualization Quality Dashboard**  module provides a comprehensive solution for measuring, monitoring, and visualizing the  **contextualization quality**  of your data in  **Cognite Data Fusion (CDF)**. It consists of two main components:

1. **Contextualization Quality Metrics Function**  (external\_id: `context_quality_handler`) - Computes all quality metrics and saves them as a JSON file in CDF
2. **Streamlit Dashboard**  (`context_quality_dashboard`) - Visualizes the pre-computed metrics with interactive gauges, charts, and tables

This module helps data engineers and operations teams understand how well their data is contextualized across three key dimensions:

- **Asset Hierarchy Quality** - Structural integrity of the asset tree
- **Equipment-Asset Relationships** - Quality of equipment-to-asset mappings
- **Time Series Contextualization** - How well time series are linked to assets

---

## Module Components

```markdown
1context_quality/
2├── auth/                                    # Authentication configurations
3├── data_sets/
4│   └── context_quality_dashboard.DataSet.yaml  # Dataset for storing function code and files
5├── functions/
6│   ├── context_quality_handler/
7│   │   └── handler.py                       # Main Cognite Function code
8│   └── context_quality.Function.yaml        # Function configuration
9├── streamlit/
10│   ├── context_quality_dashboard/
11│   │   ├── main.py                          # Streamlit dashboard code
12│   │   └── requirements.txt                 # Python dependencies
13│   └── context_quality_dashboard.Streamlit.yaml  # Streamlit app configuration
14├── module.toml                              # Module metadata
15└── README.md                                # This file
16
```

---

## Deployment

### Prerequisites

Before you start, ensure you have:

- A Cognite Toolkit project set up locally
- Your project contains the standard `cdf.toml` file
- Valid authentication to your target CDF environment

### Step 1: Enable External Libraries

Edit your project's `cdf.toml` and add:

```
1[alpha_flags]
2external-libraries = true
3​​​​​​​
4[library.cognite]
5url = "https://github.com/cognitedata/library/releases/download/latest/packages.zip"
6checksum = "sha256:795a1d303af6994cff10656057238e7634ebbe1cac1a5962a5c654038a88b078"
```

This allows the Toolkit to retrieve official library packages.

> **📝 Note: Replacing the Default Library**
> 
> By default, a Cognite Toolkit project contains a `[library.toolkit-data]` section pointing to `https://github.com/cognitedata/toolkit-data/...`. This provides core modules like Quickstart, SourceSystem, Common, etc.
> 
> **These two library sections cannot coexist.** To use this Deployment Pack, you must **replace**  the  `toolkit-data`  section with  `library.cognite`:
> 
> | Replace This | With This |
> | --- | --- |
> | `[library.toolkit-data]` | `[library.cognite]` |
> | `github.com/cognitedata/toolkit-data/...` | `github.com/cognitedata/library/...` |
> 
> The `library.cognite` package includes all Deployment Packs developed by the Value Delivery Accelerator team (RMDM, RCA agents, Context Quality Dashboard, etc.).

> **⚠️ Checksum Warning**
> 
> When running `cdf modules add`, you may see a warning like:
> 
> ```
> 1WARNING [HIGH]: The provided checksum sha256:... does not match downloaded file hash sha256:...
> 2Please verify the checksum with the source and update cdf.toml if needed.
> 3This may indicate that the package content has changed.
> 4
> ```
> 
> **This is expected behavior.** The checksum in this documentation may be outdated because it gets updated with every release. The package will still download successfully despite the warning.
> 
> **To resolve the warning:** Copy the new checksum value shown in the warning message and update your `cdf.toml`  with it. For example, if the warning shows  `sha256:da2b33d60c66700f...`, update your config to:
> 
> ```
> 1[library.cognite]
> 2url = "https://github.com/cognitedata/library/releases/download/latest/packages.zip"
> 3​​​​​​​checksum = "sha256:da2b33d60c66700f..."
> ```

To help improve the Deployment Pack:

```
1cdf collect opt-in
```

### Step 3: Add the Module

Run:

```
1cdf modules init .
```

> **⚠️ Disclaimer**: This command will overwrite existing modules. Commit changes before running, or use a fresh directory.

### Step 4: Select the Dashboards Package

From the menu, select:

```
1Dashboards: Streamlit dashboards and visualization modules
2
```

Then select **Contextualization Quality Dashboard**.

### Step 5: Verify Folder Structure

After installation, your project should contain:

```
1modules/
2    └── dashboards/
3        └── context_quality/
4
```

### Step 6: Deploy to CDF

Build and deploy:

```
1cdf build
```
```
1cdf deploy --dry-run
```
```
1cdf deploy
```

---

## Configuration

### Default Data Model View

By default, the function queries the **Cognite Core Data Model (CDM)** views. The default configuration is set in lines **68-93**  of  `handler.py`:

```python
1DEFAULT_CONFIG = {
2    "chunk_size": 500,
3    # View configurations - DEFAULT: Cognite Core Data Model (cdf_cdm)
4    "asset_view_space": "cdf_cdm",
5    "asset_view_external_id": "CogniteAsset",
6    "asset_view_version": "v1",
7    "ts_view_space": "cdf_cdm",
8    "ts_view_external_id": "CogniteTimeSeries",
9    "ts_view_version": "v1",
10    "equipment_view_space": "cdf_cdm",
11    "equipment_view_external_id": "CogniteEquipment",
12    "equipment_view_version": "v1",
13    # Processing limits
14    "max_timeseries": 150000,
15    "max_assets": 150000,
16    "max_equipment": 150000,
17    # Freshness settings
18    "freshness_days": 30,
19    # Historical gap analysis
20    "enable_historical_gaps": True,
21    "gap_sample_rate": 20,
22    "gap_threshold_days": 7,
23    "gap_lookback": "1000d-ago",
24    # ... more config
25}
```

### Changing the Data Model View

If your project uses a **custom data model**  instead of the CDM, modify lines  **71-79**  in  `handler.py`:

```python
1# Example: Using a custom data model view
2"asset_view_space": "your_custom_space",
3"asset_view_external_id": "YourAssetView",
4"asset_view_version": "v1",
5"ts_view_space": "your_custom_space",
6"ts_view_external_id": "YourTimeSeriesView",
7"ts_view_version": "v1",
8"equipment_view_space": "your_custom_space",
9"equipment_view_external_id": "YourEquipmentView",
10"equipment_view_version": "v1",
```

Alternatively, you can pass configuration overrides when calling the function:

```python
1# Call the Contextualization Quality Metrics function
2client.functions.call(
3    external_id="context_quality_handler",
4    data={
5        "asset_view_space": "my_space",
6        "asset_view_external_id": "MyAssetView",
7        "asset_view_version": "v1"
8    }
9)
```

---

### Project-Specific Field Names

Different projects may use different property names for certain fields. Here are the key fields you may need to customize:

| Field | Default Property Name | Location to Modify |
| --- | --- | --- |
| Asset Criticality | `criticality` | Line 402 in `handler.py` |
| Equipment Criticality | `criticality` | Line 444 in `handler.py` |
| Asset Type | `type`  or  `assetClass` | Line 414 in `handler.py` |
| Equipment Type | `equipmentType` | Line 440 in `handler.py` |
| Serial Number | `serialNumber` | Line 442 in `handler.py` |
| Manufacturer | `manufacturer` | Line 443 in `handler.py` |

---

## Post-Deployment: Running the Function

> ⚠️ **IMPORTANT**: The Cognite Function  **MUST be executed at least once** before launching the Streamlit dashboard. The dashboard reads pre-computed metrics from a JSON file that the function generates.

### Run the Function

After deployment, trigger the function:

**Option 1: Using the CDF UI**

1. Navigate to **Functions** in the CDF UI
2. Find **Contextualization Quality Metrics**  (external\_id: `context_quality_handler`)
3. Click **Run**  or  **Call**

**Option 2: Using the SDK**

```python
1from cognite.client import CogniteClient
2
3client = CogniteClient()
4
5# Run the Contextualization Quality Metrics function with default configuration
6response = client.functions.call(
7    external_id="context_quality_handler",
8    data={}
9)
10print(response)
```
```
Verify Function Execution (Optional)
```

> **💡 Tip:** You can verify function execution directly in the CDF UI by navigating to **Functions**  →  **Contextualization Quality Metrics**  →  **Logs/Calls** to inspect the runtime logs and execution status.

The function will:

1. Query all Time Series, Assets, and Equipment from the configured views
2. Compute all quality metrics
3. Save results to a Cognite File: `contextualization_quality_metrics`

**Alternative: Verify via SDK**

```python
1file = client.files.retrieve(external_id="contextualization_quality_metrics")
2​​​​​​​print(f"File created: {file.name}, Size: {file.size} bytes")
```

---

## Launching the Dashboard

Once the function has run successfully, follow these steps to access the dashboard:

1. **Log in to CDF**  — Open  [Cognite Data Fusion](https://fusion.cognite.com/) and sign in to your project
2. **Navigate to Industrial Tools**  — In the left sidebar, click on  **Industrial Tools**
3. **Open Custom Apps**  — Select  **Custom Apps** from the menu
4. **Launch the Dashboard**  — Click on  **Contextualization Quality** to open the app

> **⚠️ Note:** If you see "Could not load metrics file", the Cognite Function has not been run yet. Return to the [Run the Function](https://github.com/cognitedata/library/blob/context_quality/modules/dashboards/context_quality/README.md#run-the-function) section and execute it first.

The dashboard will load the pre-computed metrics and display three tabs:

- **Asset Hierarchy Quality** — Structural integrity of your asset tree
- **Equipment-Asset Quality** — Equipment-to-asset mapping quality
- **Time Series Contextualization** — How well time series are linked to assets

---

## Scheduling the Function

For continuous monitoring, schedule the function to run periodically:

```python
1from cognite.client.data_classes import FunctionSchedule
2
3# Schedule the Contextualization Quality Metrics function
4client.functions.schedules.create(
5    FunctionSchedule(
6        name="Contextualization Quality Daily",
7        function_external_id="context_quality_handler",
8        cron_expression="0 6 * * *",  # Daily at 6 AM
9        data={}
10    )
11)
```

---

## Troubleshooting

### Dashboard shows "Could not load metrics file"

**Cause:** The Cognite Function has not been run yet, or it failed.

**Solution:**

1. Run the function manually (see above)
2. Check function logs for errors
3. Verify the file `contextualization_quality_metrics` exists in CDF Files

### Metrics show all zeros

**Cause:** The data model views are empty or misconfigured.

**Solution:**

1. Verify your data exists in the configured views
2. Check that the view space, external\_id, and version match your data model
3. Modify lines 71-79 in `handler.py` if using a custom data model

### Function times out

**Cause:** Too much data to process within the 10-minute function limit.

**Solution:**

1. Reduce processing limits in configuration:
	```python
	1{
	2    "max_timeseries": 50000,
	3    "max_assets": 50000,
	4    "max_equipment": 50000
	5}
	```
2. Disable historical gap analysis: `"enable_historical_gaps": False`

---

## Metrics Reference

The Contextualization Quality module computes **25+ metrics** across three categories. Below is a detailed explanation of each metric, including formulas and interpretation guidelines.

---

### 1\. Asset Hierarchy Metrics

These metrics assess the structural quality and completeness of your asset hierarchy.

#### 1.1 Hierarchy Completion Rate

**What it measures:** The percentage of non-root assets that have a valid parent link.

**Formula:**

```
1Hierarchy Completion Rate (%) = (Assets with Parent / Non-Root Assets) × 100
2
3Where:
4  Non-Root Assets = Total Assets - Root Assets
5
```

**Interpretation:**

- 🟢 **≥ 98%**: Excellent - Nearly complete hierarchy
- 🟡 **95-98%**: Warning - Some missing links
- 🔴 **< 95%**: Critical - Hierarchy has gaps

---

#### 1.2 Orphan Count & Rate

**What it measures:** Orphans are assets that have **no parent AND no children** - they are completely disconnected from the hierarchy.

**Formula:**

```
1Orphan Rate (%) = (Orphan Count / Total Assets) × 100
2
3Where:
4  Orphan = Asset where parent == NULL AND children_count == 0
5
```

**Interpretation:**

- 🟢 **0 orphans**: Perfect - All assets are connected
- 🟡 **1-5 orphans**: Warning - Minor disconnected assets
- 🔴 **\> 5 orphans**: Critical - Significant hierarchy issues

---

#### 1.3 Depth Statistics

**Average Depth:**

```
1Average Depth = Σ(Depth of each asset) / Total Assets
2
3Where:
4  Depth = Number of levels from root to the asset (root = 0)
5
```

**Max Depth:** The deepest level in the hierarchy.

**Interpretation:**

- 🟢 **Max Depth ≤ 6**: Good - Reasonable hierarchy depth
- 🟡 **Max Depth 7-8**: Warning - Deep hierarchy
- 🔴 **Max Depth > 8**: Critical - Excessively deep hierarchy

---

#### 1.4 Breadth Statistics

**Average Children per Parent:**

```
1Average Children = Σ(Children count per parent) / Number of parents
2
```

**Standard Deviation of Children:**

```
1Std Dev = √(Σ(children_count - avg_children)² / n)
2
```

A high standard deviation indicates an uneven distribution (some parents have many children, others have few).

---

### 2\. Equipment-Asset Relationship Metrics

These metrics measure the quality of equipment-to-asset mappings.

#### 2.1 Equipment Association Rate

**What it measures:** The percentage of equipment items that are linked to an asset.

**Formula:**

```
1Equipment Association Rate (%) = (Equipment with Asset Link / Total Equipment) × 100
2
```

**Interpretation:**

- 🟢 **≥ 90%**: Excellent - Nearly all equipment is linked
- 🟡 **70-90%**: Warning - Some unlinked equipment
- 🔴 **< 70%**: Critical - Many equipment items lack asset links

---

#### 2.2 Asset Equipment Coverage

**What it measures:** The percentage of assets that have at least one equipment linked to them.

**Formula:**

```
1Asset Equipment Coverage (%) = (Assets with Equipment / Total Assets) × 100
2
```

---

#### 2.3 Serial Number Completeness

**What it measures:** The percentage of equipment items that have the `serialNumber` property populated.

**Formula:**

```
1Serial Number Completeness (%) = (Equipment with Serial Number / Total Equipment) × 100
2
```

**Interpretation:**

- 🟢 **≥ 90%**: Excellent - Good equipment traceability
- 🟡 **70-90%**: Warning - Some missing serial numbers
- 🔴 **< 70%**: Critical - Poor equipment documentation

---

#### 2.4 Manufacturer Completeness

**What it measures:** The percentage of equipment items that have the `manufacturer` property populated.

**Formula:**

```
1Manufacturer Completeness (%) = (Equipment with Manufacturer / Total Equipment) × 100
2
```

---

#### 2.5 Type Consistency

**What it measures:** The percentage of equipment where the equipment type is consistent with the linked asset's type. Uses predefined mappings (e.g., a pump equipment should link to a pump asset).

**Formula:**

```
1Type Consistency Rate (%) = (Consistent Relationships / Total Equipment) × 100
2
```

> 📝 **Note:** Type mappings are defined in lines **96-101**  of  `handler.py`. Modify these mappings to match your project's type definitions:
> 
> ```python
> 1TYPE_MAPPINGS = {
> 2    "iso14224_va_di_diaphragm": ["VALVE", "CONTROL_VALVE"],
> 3    "iso14224_pu_centrifugal_pump": ["PUMP", "PUMPING_EQUIPMENT"],
> 4    # Add your custom mappings here
> 5}
> ```

---

#### 2.6 Critical Equipment Contextualization

**What it measures:** The percentage of critical equipment that is linked to an asset.

**Formula:**

```
1Critical Equipment Contextualization (%) = (Critical Equipment Linked / Total Critical Equipment) × 100
2
```

> ⚠️ **Note:** Similar to critical assets, if your project uses a different field name for equipment criticality, modify line **444**  in  `handler.py`:
> 
> ```python
> 1criticality=props.get("criticality"),  # Change "criticality" to your field name
> ```

---

### 3\. Time Series Contextualization Metrics

These metrics measure how well time series data is linked to assets and the quality of the time series metadata.

#### 3.1 TS to Asset Contextualization Rate

**What it measures:** The percentage of time series that are linked to at least one asset.

> ⚠️ **Why this is the primary metric:** An orphaned time series (not linked to any asset) cannot be associated with equipment, location, or business context. This makes the data difficult to discover and use. Every time series should be contextualized.

**Formula:**

```
1TS to Asset Rate (%) = (Time Series with Asset Link / Total Time Series) × 100
2
```

**Interpretation:**

- 🟢 **≥ 95%**: Excellent - Nearly all TS are contextualized
- 🟡 **90-95%**: Warning - Some orphaned time series exist
- 🔴 **< 90%**: Critical - Many time series lack asset context

---

#### 3.2 Asset Monitoring Coverage

**What it measures:** The percentage of assets that have at least one time series linked to them.

> **Note:** It is acceptable for some assets (e.g., structural assets, buildings, organizational units) to not have time series. This metric helps identify assets that *could* benefit from monitoring data.

**Formula:**

```
1Asset Monitoring Coverage (%) = (Assets with Time Series / Total Assets) × 100
2
```

**Interpretation:**

- 🟢 **\> 80%**: Good - Most assets have monitoring data
- 🟡 **70-80%**: Warning - Some assets lack time series
- 🔴 **< 70%**: Critical - Many assets are unmonitored

---

#### 3.3 Critical Asset Coverage

**What it measures:** The percentage of critical assets that have time series linked. Critical assets are those with `criticality = "critical"` in their properties.

**Formula:**

```
1Critical Asset Coverage (%) = (Critical Assets with TS / Total Critical Assets) × 100
2
```

**Interpretation:**

- 🟢 **100%**: Perfect - All critical assets are monitored
- 🟡 **≥ 95%**: Warning - Nearly all critical assets covered
- 🔴 **< 95%**: Critical - Critical assets are unmonitored

> ⚠️ **Note:** If your project uses a different property name for criticality (e.g., `priority`, `importance`, `criticalityLevel`), you must modify line  **402**  in  `handler.py`:
> 
> ```python
> 1if props.get("criticality") == "critical":  # Change "criticality" to your field name
> ```

---

#### 3.4 Source Unit Completeness

**What it measures:** The percentage of time series that have the `sourceUnit` property populated. The source unit represents the original unit of measurement from the data source (e.g., "°C", "bar", "rpm").

**Formula:**

```
1Source Unit Completeness (%) = (TS with sourceUnit / Total TS) × 100
2
```

**Interpretation:**

- 🟢 **\> 95%**: Excellent - Nearly all TS have unit information
- 🟡 **90-95%**: Good - Most TS have units
- 🔴 **< 90%**: Warning - Many TS lack unit metadata

---

#### 3.5 Target Unit Completeness (Standardized Unit)

**What it measures:** The percentage of time series with a standardized `unit` property. This represents the converted/normalized unit after any unit transformations.

**Formula:**

```
1Target Unit Completeness (%) = (TS with unit / Total TS) × 100
2
```

---

#### 3.6 Unit Mapping Rate

**What it measures:** When both `sourceUnit`  and  `unit` are present, this metric tracks how many have matching values (indicating no conversion was needed or conversion is complete).

**Formula:**

```
1Unit Mapping Rate (%) = (TS where unit == sourceUnit / TS with both units) × 100
2
```

---

#### 3.7 Data Freshness

**What it measures:** The percentage of time series that have been updated within the last N days (default: 30 days).

**Formula:**

```
1Data Freshness (%) = (TS updated within N days / Total TS) × 100
2
```

**Interpretation:**

- 🟢 **≥ 90%**: Excellent - Data is current
- 🟡 **70-90%**: Warning - Some stale data
- 🔴 **< 70%**: Critical - Significant stale data

---

#### 3.8 Average Time Since Last TS Update

**What it measures:** The average time difference between "now" and the `lastUpdatedTime` of time series.

> ⚠️ **Important:** This metric tracks **metadata updates** (when the time series definition was last modified), NOT when the latest datapoint was ingested. For actual data freshness, see the "Data Freshness" metric which checks if TS have been updated within the last N days.

**Formula:**

```
1Avg Time Since Last TS Update (hours) = Σ(Now - lastUpdatedTime) / Count of valid TS
2
```

---

#### 3.9 Historical Data Completeness

**What it measures:** The percentage of the time span that contains actual data (vs gaps). A gap is defined as a period longer than the threshold (default: 7 days) without any datapoints.

**Formula:**

```
1Historical Data Completeness (%) = ((Total Time Span - Total Gap Duration) / Total Time Span) × 100
2
```

**Example:** If a time series spans 365 days but has a 30-day gap:

```
1Completeness = (365 - 30) / 365 × 100 = 91.8%
2
```

**Interpretation:**

- 🟢 **≥ 95%**: Excellent - Minimal data gaps
- 🟡 **85-95%**: Warning - Some significant gaps
- 🔴 **< 85%**: Critical - Major data gaps exist

---

## Support

For troubleshooting or deployment issues:

- Refer to the [Cognite Documentation](https://docs.cognite.com/)
- Contact your **Cognite support team**
- Join the Slack channel **#topic-deployment-packs** for community support and discussions

×

### Cookie settings

  

We use 3 different kinds of cookies. You can choose which cookies you want to accept. We need basic cookies to make this site work, therefore these are the minimum you can select. [Learn more about our cookies.](https://hub.cognite.com/site/terms)

<!-- SOURCE_END: Best Practices Library\Deployment Packs Library\Contextualization Quality Dashboard  Cognite Hub.md -->

================================================================================

================================================================================

<!-- SOURCE_START: Best Practices Library\Deployment Packs Library\How To CDF Entity Matching Module Cognite Official  Cognite Hub.md -->
## File: Best Practices Library\Deployment Packs Library\How To CDF Entity Matching Module Cognite Official  Cognite Hub.md

---
title: "How To: CDF Entity Matching Module [Cognite Official] | Cognite Hub"
source: "https://hub.cognite.com/deployment-packs-472/how-to-cdf-entity-matching-module-cognite-official-5317"
created: 2025-12-28
description: "This how-to article describes an example deployment pack for entity matching in Cognite Data Fusion (CDF). The provided code and configuration demonstra..."
tags:
  - "CDF"
  - "CogniteDataFusion"
image: "https://uploads-eu-west-1.insided.com/cognite-en/attachment/e328b668-eef0-457a-b6a6-422e513533de_thumb.png"
---
---

- [Jan Inge Bergseth](https://hub.cognite.com/members/jan-inge-bergseth-202)
- MVP
- 11 replies

This how-to article describes an example deployment pack for **entity matching in Cognite Data Fusion (CDF)**. The provided code and configuration demonstrate how to match entities—such as time series, 3D nodes, and other entity types—to assets and tags using a scalable, production-ready approach.

The process operates entirely on **CDF Data Modeling objects** and uses **CDF Workflows** to orchestrate two core functions:

1. A metadata and alias enrichment step that updates or creates descriptions, tags, and aliases for entities and assets.
2. A matching step that uses these aliases and metadata to automatically establish relationships between entities and assets.

The result of running the workflow is enriched metadata (aliases, tags, and descriptions) for time series and other entities, along with direct relationships between entities (for example, time series) and assets or tags.

> **Note:** Screenshots and diagrams are illustrative. Automatically generated content may not reflect actual project data.

![[1e139acee74a5524aebdfabd5608015a_MD5.png]]

## Why Use This Module?

### Accelerate Time Series Contextualization with Production-Proven Code

Building an entity matching solution from scratch is complex and time-consuming. This module provides production-ready, battle-tested code that has been successfully deployed across multiple customer environments. It enables you to save weeks or months of development time while delivering enterprise-grade performance, reliability, and scalability.

### Key Benefits

- ⚡ **Production-proven**: Based on real-world implementations running in multiple customer production environments.
- 🚀 **Significant time savings**: Deploy in hours instead of spending weeks or months developing custom matching logic.
- 📊 **Proven performance**: 35–55% faster execution compared to legacy implementations, with 40–60% improved matching accuracy.
- 🔧 **Easy to extend**: Clean, modular architecture with well-documented functions makes customization straightforward.
- 📈 **Enterprise scale**: Processes 10,000+ time series per batch out of the box, with proven scalability for large industrial deployments.
- 🎯 **Multi-method matching**: Combines rule-based matching, AI-powered entity matching, and manual expert mappings in a unified solution.
- 🛡️ **Robust error handling**: Achieves a 95%+ success rate through retries, state management, and incremental processing.

### Time and Cost Savings

- **Development**: Save 4–8 weeks by leveraging proven, production-ready code.
- **Performance optimization**: Benefit from built-in performance improvements without months of tuning.
- **Maintenance**: Reduce ongoing maintenance through stable, well-tested components.
- **Accuracy**: Improve matching quality and reduce manual correction effort.
- **Iteration speed**: Rapidly adapt rules and algorithms to domain-specific requirements.

### Real-World Performance Metrics

- **Processing speed**: 35–55% faster than legacy implementations
- **Memory efficiency**: 30–50% reduction in memory usage
- **Matching accuracy**: 40–60% improvement over basic approaches
- **Batch capacity**: 10,000+ time series per batch
- **Cache efficiency**: 70%+ cache hit rate for metadata operations

Whether you are contextualizing hundreds or tens of thousands of time series, this module provides a scalable foundation proven in production environments. Start with the default configuration for immediate value, then customize matching rules and algorithms as needed.

## Key Features of the Process

The Entity Matching module delivers comprehensive contextualization capabilities in CDF by combining advanced matching algorithms with metadata optimization and workflow automation.

### Overview

The module is designed to:

- Support **manual expert mappings** for complex or domain-specific relationships
- Perform **rule-based matching** using regular expressions and business logic
- Leverage **AI-powered entity matching** using CDF’s built-in ML capabilities
- Optimize metadata to improve searchability and contextualization
- Scale efficiently using batch processing and performance monitoring
- Integrate seamlessly with **CDF Workflows** for scheduling and orchestration
- Maintain state for incremental processing and reliable error recovery

![[491bf664c5a7a796fe94d524007b5613_MD5.png]]

## Core Functions

### Time Series Entity Matching Function

**Purpose:** Matches time series to assets using advanced matching techniques.

**Key Features:**

- Manual mapping support for expert-defined relationships
- Rule-based matching using regex patterns and configurable logic
- AI-powered entity matching for pattern discovery
- Optimized performance with batch processing and caching
- Retry logic and robust error handling
- Detailed metrics and logging for monitoring and troubleshooting

**Typical Use Cases:**

- Expert-driven mapping of complex asset relationships
- Automatic contextualization of sensor data
- Discovery of asset–time series relationships
- Industrial IoT data organization
- Operational monitoring and process optimization

### Metadata Update Function

**Purpose:** Enriches and optimizes metadata for time series and assets to improve matching quality and searchability.

**Key Features:**

- Optimized batch processing with caching
- Discipline classification using NORSOK standards
- Memory-efficient processing with automatic cleanup
- Detailed performance benchmarking
- Comprehensive logging and error handling

**Typical Use Cases:**

- Metadata enrichment for improved search
- Discipline-based asset categorization
- Data quality improvements
- Search and discovery optimization

## Getting Started / Deployment (Cognite Toolkit)

### Prerequisites

Before you start, ensure you have:

- A Cognite Toolkit project set up locally
- Your project contains the standard cdf.toml file
- Valid authentication to your target CDF environment
- Access to a CDF project and credentials
- cognite-toolkit >= 0.7.33
- enable data plugin in cdf.toml file

**Step 1: Enable External Libraries**

Edit your project's cdf.toml and add:  

```
1[library.cognite] 
2
3url = "https://github.com/cognitedata/library/releases/download/latest/packages.zip" 
4checksum = "sha256:795a1d303af6994cff10656057238e7634ebbe1cac1a5962a5c654038a88b078"
```

This allows the Toolkit to retrieve official library packages.

### Step 2 (Optional but Recommended): Enable Usage Tracking

To help improve the Deployment Pack:

```
1cdf collect opt-in
```

### Step 3: Add the Module

Run:

```
1cdf modules init . --clean
```

**⚠️ Disclaimer**: This command will overwrite existing modules. Commit changes before running, or use a fresh directory.

This opens the interactive module selection interface.

### Step 4: Select the Entity Matching Package (NOTE: use Space bar to select module)

From the menu, select (Note, select using space bar):

```
1Contextualization: Module templates for data contextualization  
2  └── Contextualization Entity Matching
3
```

### Step 5: Verify Folder Structure

After installation, your project should now contain:

```
1modules
2     └── accelerators
3         └── contextualization
4             └── cdf_entity_matching
5
6
```

If you want to add more modules, continue with yes ('y') else no ('N')

And continue with creation, yes ('Y') => this then creates a folder structure in your destination with all the files from your selected modules.  

### Step 6: Deploy to CDF

NOTE: Update your config.dev.yaml file with project, source\_id, clisen ID / Secret and changes in spaces or versions.

Optional: Rename variables LOC and SOURCE. (or just test with default values)

Build deployment structure:

```
1cdf build
```

Optional dry run:

```
1cdf deploy --dry-run
```

Deploy module to your CDF project

```
1cdf deploy
```
- Note that the deployment uses a set of CDF capabilities, so you might need to add this to the CDF security group used by Toolkit to deploy

After deploy, data under upload\_data directory needs to be populated in the respective tables. This will be done using data upload command.

```
1cdf data upload dir .\modules\accelerators\contextualization\cdf_entity_matching\upload_data\ -v
```
- Note that data plugin should be enabled in cdf.toml file for running this command.

## Testing the Entity Matching module

If you wan to test the entity matching process, it is possible to download test data from the included source data in CDF Toolkit.

The process to do this would be:

- In your local cdf.toml file:
```
1[library.cognite]
2url = "https://github.com/cognitedata/toolkit-data/releases/download/latest/packages.zip"
```
- Before you run cdf modules init. note the the local folder structure will be updated lost with the now content from the modules you now download (so copy/store or run the init of the annotation module again after setting up the test data)
```
1cdf modules init . --clean
```
- Select package:
	- Models: Example of Minimum Extension of the Cognite Process Industry Model
- Select Yes to add more modules, and select:
	- SourceSystem: Module templates for setting up a data pipeline from a source system
	- And then (Note, select using space bar)
		- SAP Asset data
		- OSIsoft/Aveva PI

After installation, your project should now contain:

```
1     modules
2     ├── models
3     │   └── cdf_process_industry_extension
4     └── sourcesystem
5         ├── cdf_pi
6         └── cdf_sap_assets
7
8
9
```

If you want to add more modules, continue with yes ('y') else no ('N')

And continue with creation, yes ('Y') => this then creates a folder structure in your destination with all the files from your selected modules.

- Edit your **config.dev.yaml** file
	- Project è should be your CDF project name
	- For cdf\_pi and cdf\_sap\_assets (details on access control see CDF documentation):
		- groupSourceId: if using Azure, access group ID from Entra ID used by system user and/or users setting up CDF that should be able update data model and run Transformations
		- workflowClientId: environment variable with Entra ID object ID for APP
		- workflowClientSecret: environment variable with secret value for APP

Build deployment structure:

```
1cdf build
```

Optional dry run:

```
1cdf deploy --dry-run
```

Deploy module to your CDF project

```
1cdf deploy
```

After deploy, data under upload\_data directory needs to be populated in the respective tables. This will be done using data upload command.

```
1cdf data upload dir .\modules\accelerators\contextualization\cdf_entity_matching\upload_data\ -v
```

**⚠️** **NOTE**: Before running workflow that upload Assets/ Tags and creates hierarchy – make sure your transformation: Asset Transformations for SAP Springfield S/4HANA Assets don’t filter on ‘1157’ - making sure that all Assets are loaded to data model!

Run workflows to load data

- **sap\_s4ana\_population**:
- **aveva\_pi\_population**

You should now have test data to run a simple example of the annotation process:-)

## Run the Entity Matching workflow

If your module structure was overwritten by the models for test data, clean up, merge or just run the module init again as instructed above for the entity matching module. This to make it easy to make changes and redeploy when you are making the Entity Matching process relevant for your project.

After deployment, trigger the workflow click on Run):**EntityMatching** process via the CDF Workflows UI or API to execute the transformations in order.

In **Integrate / Data Workflows**: Entity Matching process

![[ebfa2fd0b9f0665e54065b7a5841cfc7_MD5.webp]]

In CDF UI: Build Solutions / Functions

![[a6ab079fdc7494f38fd902053743f52d_MD5.webp]]

Click on **View logs** and verify that the included test data fwhere processed without any errors.

You can also view the result in the good/bad tables in RAW – or by using search and look at the mapping from Asset to Time series, ex:

![[9e1493fd9ee221707683d4a4cd288f55_MD5.png]]

## Data Flow

![[c900d704e3743455cbd27274c64a45dc_MD5.png]]

  
  

×

### Cookie settings

  

We use 3 different kinds of cookies. You can choose which cookies you want to accept. We need basic cookies to make this site work, therefore these are the minimum you can select. [Learn more about our cookies.](https://hub.cognite.com/site/terms)

<!-- SOURCE_END: Best Practices Library\Deployment Packs Library\How To CDF Entity Matching Module Cognite Official  Cognite Hub.md -->

================================================================================

================================================================================

<!-- SOURCE_START: Best Practices Library\Deployment Packs Library\How to Deploy RMDM v1 (Reliability Maintenance Data Model) in Cognite Data Fusion  Cognite Hub.md -->
## File: Best Practices Library\Deployment Packs Library\How to Deploy RMDM v1 (Reliability Maintenance Data Model) in Cognite Data Fusion  Cognite Hub.md

---
title: "How to Deploy RMDM v1 (Reliability Maintenance Data Model) in Cognite Data Fusion | Cognite Hub"
source: "https://hub.cognite.com/deployment-packs-472/how-to-deploy-rmdm-v1-reliability-maintenance-data-model-in-cognite-data-fusion-5454"
created: 2025-12-28
description: "Overview The RMDM v1 module provides the foundational data model definitions required to implement a Reliability Maintenance Data Model in your Cognite..."
tags:
  - "CDF"
  - "CogniteDataFusion"
image: "https://uploads-eu-west-1.insided.com/cognite-en/attachment/e328b668-eef0-457a-b6a6-422e513533de_thumb.png"
---
---

- [Charan Raju](https://hub.cognite.com/members/charan-raju-7025)
- Practitioner

## Overview

The **RMDM v1 module** provides the foundational data model definitions required to implement a **Reliability Maintenance Data Model** in your **Cognite Data Fusion (CDF)** environment.  
It includes all necessary containers, views, and space configurations to establish a comprehensive framework for **maintenance and reliability management**.

This guide walks you through the concept of RMDM and the steps to download, configure, and deploy RMDM v1 using the **Cognite Toolkit**.

## What is RMDM?

**RMDM (Reliability Maintenance Data Model)** is a **domain-specific data model** introduced by Cognite to structure and unify reliability and maintenance-related data in **Cognite Data Fusion (CDF)**.

It extends Cognite’s **common data models (CDM, IDM)** to include **reliability-focused standards**, ensuring that maintenance and troubleshooting processes are supported with consistent, high-quality data.

RMDM is built to be **ISO 14224** and **NORSOK Z-008** compliant, which are globally recognized standards for equipment reliability, failure modes, and maintenance management.

## Why RMDM is Needed?

- Maintenance programs rely on **static or incomplete data**, leading to inefficiencies.
- Data is **siloed** across multiple systems (CMMS, DCS, Aveva, etc.), making it hard to transition from calendar-based to **condition-based maintenance (CBM)**.
- RCAs (Root Cause Analyses) typically require **weeks of manual data gathering** due to the lack of standardized structures.
- Poor standardization and naming conventions hinder data unification and collaboration.

**RMDM solves this by providing a common foundation for structuring and classifying data, enabling faster, more effective RCA and troubleshooting.**

## Module Components

The RMDM v1 module is structured as follows:

- **data\_models/** – Core RMDM data model definitions
- **containers/** – Container definitions for all RMDM entities
- **views/** – View definitions for all RMDM entities
- **rmdm\_v1.datamodel.yaml** – Main data model configuration
- **rmdm.space.yaml** – Space configuration for the RMDM data model

## Data Model Entities

The RMDM v1 defines a comprehensive set of entities, grouped into key categories:

### Core Asset Entities

- **Asset** – Physical or logical assets in the maintenance system
- **Equipment** – Specific equipment items requiring maintenance
- **EquipmentClass** – Classification system for equipment types
- **EquipmentFunction** – Functional categories for equipment
- **EquipmentType** – Specific types of equipment

### Maintenance Entities

- **MaintenanceOrder** – Work orders for maintenance activities
- **Notification** – System notifications and alerts

### Failure Analysis Entities

- **FailureNotification** – Notifications related to failures
- **FailureCause** – Root causes of equipment failures
- **FailureMechanism** – Mechanisms through which failures occur
- **FailureMode** – Specific modes of failure for equipment

### Extended Entities

- **File\_ext** – Extended file metadata for maintenance documentation
- **Timeseries\_ext** – Extended time series data for monitoring and analysis

## Prerequisites

Before you start, ensure you have the following:

- You already have a Cognite Toolkit project set up locally.
- Your project contains the standard `cdf.toml` file
- You have valid authentication to your target CDF environment

## Step 1: Enable External Libraries

Edit your project’s `cdf.toml` and add:

```
1[alpha_flags]
2external-libraries = true
3
4[library.cognite]
5url = "https://github.com/cognitedata/library/releases/download/latest/packages.zip"
6checksum = "sha256:795a1d303af6994cff10656057238e7634ebbe1cac1a5962a5c654038a88b078"
```

This allows the Toolkit to retrieve official library packages, including RMDM.

> **📝 Note: Replacing the Default Library**
> 
> By default, a Cognite Toolkit project contains a `[library.toolkit-data]` section pointing to `https://github.com/cognitedata/toolkit-data/...`. This provides core modules like Quickstart, SourceSystem, Common, etc.
> 
> **These two library sections cannot coexist.** To use this Deployment Pack, you must **replace**  the  `toolkit-data`  section with  `library.cognite`:
> 
> | Replace This | With This |
> | --- | --- |
> | `[library.toolkit-data]` | `[library.cognite]` |
> | `github.com/cognitedata/toolkit-data/...` | `github.com/cognitedata/library/...` |
> 
> The `library.cognite` package includes all Deployment Packs developed by the Value Delivery Accelerator team (RMDM, RCA agents, Context Quality Dashboard, etc.).

> **⚠️ Checksum Warning**
> 
> When running `cdf modules add`, you may see a warning like:
> 
> ```bash
> 1WARNING [HIGH]: The provided checksum sha256:... does not match downloaded file hash sha256:...
> 2Please verify the checksum with the source and update cdf.toml if needed.
> 3This may indicate that the package content has changed.
> ```
> 
> **This is expected behavior.** The checksum in this documentation may be outdated because it gets updated with every release. The package will still download successfully despite the warning.
> 
> **To resolve the warning:** Copy the new checksum value shown in the warning message and update your `cdf.toml`  with it. For example, if the warning shows  `sha256:da2b33d60c66700f...`, update your config to:

> ```bash
> 1[library.cognite]
> 2url = "https://github.com/cognitedata/library/releases/download/latest/packages.zip"
> 3checksum = "sha256:da2b33d60c66700f..."
> ```

## Step 2 (Optional but Recommended): Enable Usage Tracking

To help improve the Deployment Pack and provide insight to the Value Delivery Accelerator team, you can enable anonymous usage tracking:

`cdf collect opt-in `

This is optional, but highly recommended.

## Step 3: Add the Module

Run:

`cdf modules add `

This opens the interactive module selection interface.

## Step 4: Select the RMDM Data Models Package

From the menu, select:

`Data models: Data models that extend the core data model`

Follow the prompts.  
Toolkit will:

- Download the RMDM module
- Update the Toolkit configuration
- Place the files into your project

## Step 5: Verify Folder Structure

After installation, your project should now contain:

```markdown
1modules/
2    └── data_models/
3        └── rmdm_v1/
```

If you see this structure, RMDM has been successfully added to your project.

## Step 6: Deploy to CDF

Build and deploy as usual:

`cdf build `

`cdf deploy --dry-run`

`cdf deploy`

After deployment, the RMDM models, containers, and views will be available in your CDF environment.

## Support

For troubleshooting or deployment issues:

- Refer to the Cognite Documentation
- Contact your **Cognite support team**

×

### Cookie settings

  

We use 3 different kinds of cookies. You can choose which cookies you want to accept. We need basic cookies to make this site work, therefore these are the minimum you can select. [Learn more about our cookies.](https://hub.cognite.com/site/terms)

<!-- SOURCE_END: Best Practices Library\Deployment Packs Library\How to Deploy RMDM v1 (Reliability Maintenance Data Model) in Cognite Data Fusion  Cognite Hub.md -->

================================================================================

================================================================================

<!-- SOURCE_START: Best Practices Library\Deployment Packs Library\How to get started with CDF performance testing Cognite Official  Cognite Hub.md -->
## File: Best Practices Library\Deployment Packs Library\How to get started with CDF performance testing Cognite Official  Cognite Hub.md

---
title: "How to get started with CDF performance testing [Cognite Official] | Cognite Hub"
source: "https://hub.cognite.com/deployment-packs-472/how-to-get-started-with-cdf-performance-testing-cognite-official-5345"
created: 2025-12-28
description: "SummaryThis How-To outlines the critical role of performance testing in optimizing Cognite Data Fusion (CDF) projects, emphasizing its necessity for ens..."
tags:
  - "CDF"
  - "CogniteDataFusion"
image: "https://uploads-eu-west-1.insided.com/cognite-en/attachment/e328b668-eef0-457a-b6a6-422e513533de_thumb.png"
---
---

- [Jan Inge Bergseth](https://hub.cognite.com/members/jan-inge-bergseth-202)
- MVP
- 11 replies

### Summary

This How-To outlines the critical role of performance testing in optimizing Cognite Data Fusion (CDF) projects, emphasizing its necessity for ensuring efficiency and effectiveness. It details key measurable factors like data ingestion rates, query performance, and API response times, which are crucial for assessing CDF's operational health. Cognite provides a set of notebooks with example code to facilitate testing across various operations, including data ingestion, query performance, and data modeling, with a strong recommendation to use test/development projects and monitor CDF quota usage. Ultimately, performance testing is presented as essential for achieving project delivery success, ensuring production readiness, and boosting development efficiency, leading to tangible benefits such as faster dashboards, reliable ingestion, and significant cost optimization.

Monitoring Cognite Data Fusion (CDF) performance involves tracking several key factors that ensure the platform operates efficiently and effectively. Based on the documentation from Cognite, typical measurable factors include:

1. **Data Ingestion Rates:** Monitoring the speed and volume at which data is ingested into CDF from various sources.
2. **Data Processing Latency:** Measuring the time taken for CDF to process incoming data, including transformations and contextualization.
3. **Query Performance:** Assessing the speed and efficiency of data retrieval queries executed against the CDF platform.
4. **Pipeline Execution Times:** Monitoring the duration and success rates of data pipelines and workflows within CDF.
5. **Error and Warning Rates:** Monitoring the frequency and types of errors or warnings encountered during data processing and transformations.
6. **Data Quality Metrics:** Evaluating metrics related to data completeness, accuracy, consistency, and timeliness.
7. **API Response Times:** Measuring the responsiveness of CDF's API endpoints when interacting with external applications and services.
8. **System Availability:** Monitoring uptime and availability metrics to ensure CDF is accessible and operational according to service-level agreements (SLAs).
9. **Integration Health:** Assessing the status and performance of integrations with external systems and applications.

These factors help ensure that Cognite Data Fusion maintains high performance, reliability, and efficiency in managing and analyzing industrial data.

To get started with performance testing we have created a set of notebooks with example code for setting up the operation categories for testing. These categories include setting up and generating test data in your CDF project, running the tests and visualizing the results: These tests are:

| **Operation Category** | **What We Test** | **Project Impact** |
| --- | --- | --- |
| **Data Ingestion** | Time series batch/streaming ingestion | Data pipeline throughput & reliability |
| **Query Performance** | Range, aggregate, multi-series queries | Application response times |
| **Data Modeling** | Schema ops, instance CRUD, relationships | Data model scalability |
| **RAW Operations** | Bulk insert/query, table management | Data lake performance |
| **File Operations** | Upload/download, metadata handling | Asset management efficiency |
| **Transformations** | Pipeline execution, resource usage | Data processing workflows |

The Notebooks can be found and downloaded from the CDF Library GitHub repository:

- **All notebooks include automatic cleanup** functions
- **Test data is created in your CDF project** \- cleanup is essential
- **Use test/development projects** for performance testing when possible
- **Monitor your CDF quota usage** during large-scale tests

[https://github.com/cognitedata/library/tree/main/notebooks/CDF%20Performance%20Testing/notebooks](https://github.com/cognitedata/library/tree/main/notebooks/CDF%20Performance%20Testing/notebooks)

Download instructions:

1. Use the above link to navigate to the required repo.
2. You should be at library/notebooks/CDF Performance Testing/notebooks.
3. Click on library, this should take you to the home page of the repo.
4. Click on "<>Code" icon, it should give you the Clone option and the option to download repo as zip.
5. Use https/ssh/github cli to clone the repo into local or download the zip file as per choice.

## Why Performance Testing is Critical for CDF Projects

### 1\. Project Delivery Success

- **Meet SLA Requirements:** Ensure applications meet response time commitments
- **Scale Validation:** Verify system can handle projected data volumes
- **User Experience:** Prevent slow dashboards and frustrated end users
- **Cost Optimization:** Identify inefficient operations that waste CDF quota

### 2\. Production Readiness

- **Avoid Go-Live Issues:** Catch performance bottlenecks before production
- **Capacity Planning:** Size infrastructure correctly for data loads
- **System Stability:** Prevent timeouts and failures under load
- **Optimization Opportunities:** Find areas for significant performance gains

### 3\. Development Efficiency

- **Early Detection:** Identify performance regressions during development
- **Design Validation:** Verify data model and architecture decisions
- **Tuning Guidance:** Get specific recommendations for optimization
- **Baseline Establishment:** Track performance improvements over time

## Real-World Project Benefits

### Before Performance Testing:

❌ "Dashboard takes 30 seconds to load"  
❌ "Data ingestion pipeline fails with large batches"  
❌ "Users complain about slow search results"  
❌ "Hitting CDF quota limits unexpectedly"

### After Performance Testing:

✅ "Dashboard loads in <3 seconds"  
✅ "Ingestion handles 10,000 datapoints/second reliably"  
✅ "Search results return in <1 second"  
✅ "Optimized operations reduce CDF costs by 40%"

  
Included [README.md](https://github.com/cognitedata/library/blob/main/modules/notebooks/CDF%20Perfromace%20Testing/README.md) file in project contains detailed documentation and all steps you need to get started with using this module:

## Example output from testing

### Data Model Performance Testing

![[61495aac76fecfb84cee6e2f1115d3e9_MD5.png]]

A group of colorful graphsAI-generated content may be incorrect.

### Transformation Performance Testing

![[dd702acec2322afb9d020232bf23b1e8_MD5.png]]

A group of colorful bars and a bar chartAI-generated content may be incorrect.

### RAW Performance Testing

![[8a27a3b085820ffb39078294db8275ab_MD5.png]]

A group of colorful barsAI-generated content may be incorrect.

### File Performance Testing

![[6ba204646aee0ba9e26eda4da28c2817_MD5.png]]

A group of different colored barsAI-generated content may be incorrect.

### Time Series Ingestion Performance Testing

![[6d4d34515ae6813532322fe394d42c10_MD5.png]]

A group of graphs and chartsAI-generated content may be incorrect.

### Time Series Query Performance Testing

![[4ac2ed5644f9745f566da6a91cb811ca_MD5.png]]

A group of blue barsAI-generated content may be incorrect.

## Next steps

Start by running the tests as is, expand to your project need and start using real data instead of generated data, test transformation and test data model. Making the tests relevant for your project. If relevant, move code to run by a schedule to always keep tract of performance by tracking the results in separate performance time series for the CDF project.

By monitoring these measurable factors, organizations can gain insights into the operational health, data quality, and overall effectiveness of their Cognite Data Fusion implementation.

×

### Cookie settings

  

We use 3 different kinds of cookies. You can choose which cookies you want to accept. We need basic cookies to make this site work, therefore these are the minimum you can select. [Learn more about our cookies.](https://hub.cognite.com/site/terms)

<!-- SOURCE_END: Best Practices Library\Deployment Packs Library\How to get started with CDF performance testing Cognite Official  Cognite Hub.md -->

================================================================================

================================================================================

<!-- SOURCE_START: Best Practices Library\Deployment Packs Library\ISA Manufacturing Data Model Extension  Cognite Hub.md -->
## File: Best Practices Library\Deployment Packs Library\ISA Manufacturing Data Model Extension  Cognite Hub.md

---
title: "ISA Manufacturing Data Model Extension | Cognite Hub"
source: "https://hub.cognite.com/deployment-packs-472/isa-manufacturing-data-model-extension-5821"
created: 2025-12-28
description: "OverviewThis deployment pack provides an ISA-95/ISA-88 based manufacturing domain model for Cognite Data Fusion (CDF). It defines spaces, containers, vi..."
tags:
  - "CDF"
  - "CogniteDataFusion"
image: "https://uploads-eu-west-1.insided.com/cognite-en/attachment/e328b668-eef0-457a-b6a6-422e513533de_thumb.png"
---
---

- [Jan Inge Bergseth](https://hub.cognite.com/members/jan-inge-bergseth-202)
- MVP
- 11 replies

### Overview

This deployment pack provides an ISA-95/ISA-88 based manufacturing domain model for Cognite Data Fusion (CDF). It defines spaces, containers, views, and a composed data model, along with optional RAW seed data, SQL transformations, and a workflow to orchestrate data loading. The model is not 100% complete for any customers, but contains place holders and structures where project can add properties add & remove views as required by customer.  
This module follows Cognite's best practices for data modeling, focusing on practical, operational design rather than academic theory. It is optimized for industrial scale, high performance, and flexibility.

### Key Principles

- Leverages Cognite Data Modeling (Service + AI)
- Follows best practices for extending the Core Data Model (CDM)
- Supports both direct and reverse direct relationships
- Uses AI-friendly naming and descriptions to improve navigation and ensure accurate AI outputs
- Builds the data model around the Asset Hierarchy to simplify navigation and centralize relationship information
- Extends the Asset Hierarchy across multiple containers and views, reusing the same externalId to make navigation, search, and grouping easier

### Performance & Structure

- Uses purpose-specific (not generic) containers, allowing targeted indexing for faster queries
- Ensures smooth compatibility with CDF services such as Search and Canvas
- Clearly separates properties according to ISA-95 hierarchy levels

### User Experience

- Improved navigation in Search and Canvas through well-structured properties and hierarchy
- Optimized for common use cases, especially AI-driven ones, where "Asset" data is brought into Canvas. Organizing asset properties by parent asset levels improves clarity and user experience

### Data Model Architecture Diagram

The following diagram illustrates the relationships and hierarchies within the ISA-95/ISA-88 manufacturing data model:

![[d0b6deb996d3f49e93dde8df123ce7d5_MD5.webp]]

The draw.io diagram is available on request where changes relevant for the project can be added.

The diagram shows:

- **ISA-95 Organizational Hierarchy** (vertical structure): Enterprise → Site → Area → ProcessCell → Unit → EquipmentModule
- **ISA-88 Procedural Hierarchy** (recipe execution): Recipe → Procedure → UnitProcedure → Operation → Phase
- **ISA-95 Level 3 Production Management**: ProductDefinition → ProductRequest → ProductSegment
- **Cross-hierarchy relationships**: How organizational, procedural, and production management entities connect
- **Process Data Integration**: How ProcessParameters and ISATimeSeries link to both ISA-88 and ISA-95 entities
- **Work Order Integration**: How WorkOrders bridge production planning and execution

### Standards Compliance

This data model is based on two key international standards for manufacturing:

- **ISA-95 (ANSI/ISA-95)**: Enterprise-Control System Integration standard that defines the hierarchical structure of manufacturing operations from enterprise level (Level 0) down to production units (Level 4). This model implements the ISA-95 organizational hierarchy including Enterprise, Site, Area, ProcessCell, Unit, and EquipmentModule entities.
- **ISA-88 (ANSI/ISA-88)**: Batch Control standard that defines procedural models for batch manufacturing. This model implements the ISA-88 procedural hierarchy including Recipe, Procedure, UnitProcedure, Operation, and Phase entities, along with Batch execution tracking.

The data model integrates both standards to provide a comprehensive manufacturing data structure that supports:

- **Organizational hierarchy** (ISA-95 Levels 0-4): Enterprise → Site → Area → ProcessCell → Unit → EquipmentModule
- **Production management** (ISA-95 Level 3): ProductDefinition → ProductRequest → ProductSegment
- **Procedural hierarchy** (ISA-88): Recipe → Procedure → UnitProcedure → Operation → Phase
- **Execution tracking**: Batch (ISA-88), WorkOrder (ISA-95 Level 3), linking production planning to execution
- **Process data integration**: ProcessParameter definitions with ISATimeSeries actual values, linking to both ISA-88 phases and ISA-95 product segments
- **Quality and process data**: QualityResult, ProcessParameter with time series integration
- **Cross-hierarchy relationships**: Seamless navigation between ISA-95 organizational structure, ISA-95 production management, and ISA-88 procedural execution

### Module structure

![[6942c9f658d60e37bb478251859dec41_MD5.webp]]

### What each part does

- **data\_models/containers**: Column-level schemas for each entity (Area, Batch, Equipment, …). Names are prefixed with sp\_isa\_manufacturing\_ and bound to the sp\_isa\_manufacturing space.
- **data\_models/views**: Logical views over containers with relationships; many views implement standard cdf\_cdm interfaces (e.g., CogniteActivity, CogniteDescribable).
- **ISA\_Manufacturing.DataModel.yaml**: The compositional data model that includes all views from this module and referenced cdf\_cdm interfaces.
- **Spaces**: sp\_isa\_manufacturing.Space.yaml defines the schema space; sp\_isa\_instance.Space.yaml can hold instances.
- **data\_sets**: CDF dataset used by transformations and for lineage.
- **raw**: Optional RAW table(s) and seed data to bootstrap asset trees or mappings.
- **transformations**: SQL transformations to materialize/maintain instances and relations for the views.
- **workflows**: A CDF Workflow to orchestrate transformations and supporting steps.

## Key entities (views)

### ISA-95 Organizational Hierarchy (Levels 0-4)

- **Level 0 (Enterprise)**: Enterprise \- Top-level organizational entity
- **Level 4 (Site)**: Site \- Physical location where manufacturing operations are performed
- **Level 4 (Area)**: Area \- Logical or physical grouping of process cells within a site
- **Level 4 (ProcessCell)**: ProcessCell \- Collection of units that perform a specific manufacturing function
- **Level 4 (Unit)**: Unit \- Basic equipment entity that can carry out one or more processing activities
- **Level 4 (EquipmentModule)**: EquipmentModule \- Functional grouping of equipment within a unit
- **Equipment**: Equipment \- Physical equipment used in manufacturing processes
- **ISAAsset**: ISAAsset \- Generic asset entity for flexible asset hierarchy representation

### ISA-88 Procedural Model

- **Recipe**: Recipe \- Master recipe defining manufacturing process steps and parameters
- **Procedure**: Procedure \- Top-level procedural element that coordinates unit procedures
- **UnitProcedure**: UnitProcedure \- Procedural element that defines operations for a specific unit
- **Operation**: Operation \- Procedural element that groups phases for specific processing tasks
- **Phase**: Phase \- Lowest level procedural element that performs specific processing activities
- **ControlModule**: ControlModule \- Control module for equipment control logic

### ISA-95 Level 3 Production Management

- **ProductDefinition**: ProductDefinition \- Definition of the product process and resources (ISA-95 Level 3). Represents the master definition for how products are manufactured, including process requirements, resource allocation, and production specifications. Product definitions link to Units (ISA-95 Level 4) and contain ProductSegments that define specific production activities.
- **ProductRequest**: ProductRequest \- Request to produce specific quantities of products (ISA-95 Level 3). Represents actual production orders or requests that reference ProductDefinitions. Product requests specify quantities, priorities, due dates, and link to WorkOrders for execution. They bridge the gap between production planning (ISA-95 Level 3) and execution (ISA-88 Batch Control).
- **ProductSegment**: ProductSegment \- Segment of the product process and resources (ISA-95 Level 3). Represents discrete segments within a product definition that define specific production activities, resource requirements, and process parameters. Product segments contain requirements such as temperature, pressure, flow rate, pH, and time requirements. They link to ProcessParameters for control and monitoring, and connect to ISATimeSeries for process data acquisition.

### Execution and Production Management

- **Batch**: Batch \- Specific instance of a production run executed according to a recipe (ISA-88). Batches represent actual production executions and link to Recipes (ISA-88 procedural model), WorkOrders (ISA-95 Level 3 work management), and Sites (ISA-95 Level 4 organizational hierarchy). Batches track execution lifecycle from initiation through completion.
- **WorkOrder**: WorkOrder \- Work order for manufacturing execution (ISA-95 Level 3). Work orders represent specific work instructions that can be used for both production activities (linked to ProductRequests) and maintenance activities (linked to Equipment). They implement CogniteActivity and link to ISATimeSeries for process data, Equipment for equipment context, and Personnel for assignment tracking.

### Quality and Process Data

- **QualityResult**: QualityResult \- Quality test results and inspection data. Links to Batches for batch-level quality tracking and supports quality assurance and regulatory compliance.
- **ProcessParameter**: ProcessParameter \- Process parameter definitions (ISA-88). Defines parameters that need to be controlled or monitored during batch execution, including target values, min/max limits, and units of measure. ProcessParameters link to Phases (ISA-88 procedural elements) and ProductSegments (ISA-95 Level 3 production segments), enabling both procedural and production-level parameter tracking.

### Supporting Entities

- **Material**: Material \- Master material definition used in manufacturing. Links to Recipes (ISA-88) and Batches (ISA-88) for material traceability and production planning.
- **MaterialLot**: MaterialLot \- Specific lot or batch of material. Enables lot-level tracking and traceability for batch manufacturing and quality control.
- **Personnel**: Personnel \- Person involved in manufacturing operations. Links to WorkOrders for work assignment and personnel performance tracking.
- **ISATimeSeries**: ISATimeSeries \- Time series data linked to ISA entities. Implements CogniteTimeSeries and provides process data acquisition for ISA-95 assets, ISA-88 phases, ISA-95 product segments, and work orders. ISATimeSeries enables real-time and historical process data collection, supporting condition monitoring, performance analysis, and predictive maintenance.
- **ISAFile**: ISAFile \- File attachments linked to ISA entities. Provides document management for ProductDefinitions, ProductRequests, ProductSegments, and other ISA entities, supporting comprehensive documentation and compliance requirements.

## How to extend

1. Add a new entity
- Create a container in data\_models/containers/ (follow naming: sp\_isa\_manufacturing\_<Entity>.Container.yaml).
- Create a view in data\_models/views/<Entity>.view.yaml referencing the container and define relations.
- If applicable, add implements: entries with relevant cdf\_cdm views (e.g., CogniteDescribable, CogniteActivity).
- Include the new view in ISA\_Manufacturing.DataModel.yaml under views:.
1. Add relationships
- Use source \+ through in the view to model direct and reverse relations.
- Prefer multi‑reverse relations for lists; ensure identifiers match the related view’s property names.
1. Update transformations
- Add a new \*.Transformation.sql and corresponding \*.Transformation.yaml in transformations/ to populate/maintain instances for your entity and relations.
- Reference the correct dataset and spaces; reuse existing variables where possible.
1. Extend the workflow
- Modify workflows/wf\_isa\_manufacturing.Workflow.yaml to include new transformation tasks, dependencies, and failure handling.
1. Access control / locations
- If you need to scope data by location, update or add a locations/\*LocationFilter.yaml and ensure relevant groups/locations exist in your environment.
- There are 2 included locatoion examples

## Deployment (Cognite Toolkit)

### Prerequisites

Before you start, ensure you have:

- A Cognite Toolkit project set up locally
- Your project contains the standard cdf.toml file
- Valid authentication to your target CDF environment
- Access to a CDF project and credentials
- cognite-toolkit \>= 0.6.61

**Enable Feature Flags** (Required for CDF):

- Navigate to \[Cognite Unleash Feature Flags - FDX\_VIEW\_SWITCHER\](https://unleash-apps.cogniteapp.com/projects/default/features/FDX\_VIEW\_SWITCHER)
- Enable the FDX\_VIEW\_SWITCHER feature flag for your CDF project
- This feature flag enables the view switcher functionality in CDF Data Modeling, which is required for:
	- Proper view navigation in the CDF UI
	- Relationship visualization between views
	- Switching between different view versions
	- Enhanced data model exploration capabilities
- **Note**: Feature flags are project-specific, so ensure you enable it for the correct CDF project

**Access**: You need appropriate permissions in your CDF project to enable feature flags. Contact your CDF administrator if you don't have access.

### Step 1: Enable External Libraries

Edit your project's cdf.toml and add:  

```
1[alpha_flags] 
2
3external-libraries = true 
4
5[library.cognite] 
6
7url = "https://github.com/cognitedata/library/releases/download/latest/packages.zip" 
8
9checksum = "sha256:795a1d303af6994cff10656057238e7634ebbe1cac1a5962a5c654038a88b078"
```

This allows the Toolkit to retrieve official library packages.

### Step 2 (Optional but Recommended): Enable Usage Tracking

To help improve the Deployment Pack:

```
1cdf collect opt-in
```

### Step 3: Add the Module

Run:

```
1cdf modules init . --clean
```

**⚠️ Disclaimer**: This command will overwrite existing modules. Commit changes before running, or use a fresh directory.

This opens the interactive module selection interface.

### Step 4: Select the ISA Data Models Package (NOTE: use Space bar to select module)

From the menu, select:

```
1Data models: Data models that extend the core data model  
2
3  └── ISA 88/95 Manufacturing Data Model template
```

### Step 5: Verify Folder Structure

After installation, your project should now contain:

```
1modules/ 
2
3    └── data_models/ 
4
5        └── id = isa_manufacturing_extension/
```

If you want to add more modules, continue with yes ('y') else no ('N')

And continue with creation, yes ('Y') => this then creates a folder structure in your destination with all the files from your selected modules.  

### Step 6: Deploy to CDF

NOTE: Update your config.dev.yaml file with project and changes in spaces or versions

Build deployment structure:

```
1cdf build
```

Optional dry run:

```
1cdf deploy --dry-run
```

Deploy module to your CDF project

```
1cdf deploy
```
- Note that the deployment uses a set of CDF capabilities, so you might need to add this to the CDF security group used by Toolkit to deploy
- This will create/update spaces, containers, views, the composed data model, dataset, RAW resources, transformations, and workflows defined by this module.

### Run the workflow / transformations

- After deployment, trigger wf\_isa\_manufacturing via the CDF Workflows UI or API to execute the transformations in order.
- The workflow reads from RAW tables (including optional seed data in CSV format) and populates the ISA-95/ISA-88 data model instances.
- Alternatively, run individual transformations from the CDF UI if you prefer ad‑hoc execution during development.

### Workflow Execution Flow

![[34267ffcfa08f67a9f468412f60e3ac1_MD5.webp]]

Some of the workflow executes transformations:

1. **Build ISA Asset Tree** (isa\_asset\_tr)
- Creates the base ISAAsset hierarchy from RAW data
- This is a critical first step that all other transformations depend on
- Reads from ISA\_Manufacturing.isa\_asset RAW table
1. **ISA-95 Organizational Hierarchy Overlays** (executed in parallel after asset tree)
- **Enterprise** (enterprise\_tr): Creates Enterprise entities (ISA-95 Level 0)
- **Site** (site\_tr): Creates Site entities with Enterprise relationships (ISA-95 Level 4)
- **Area** (area\_tr): Creates Area entities with Site relationships (ISA-95 Level 4)
- **ProcessCell** (process\_cell\_tr): Creates ProcessCell entities with Area relationships (ISA-95 Level 4)
- **Unit** (unit\_tr): Creates Unit entities with ProcessCell relationships (ISA-95 Level 4)
- **EquipmentModule** (equipment\_module\_tr): Creates EquipmentModule entities with Unit relationships (ISA-95 Level 4)
1. **Equipment and Relationships**
- **Equipment** (equipment\_tr): Creates Equipment entities linked to assets and units
- **EquipmentModule-Equipment Linking** (equipment\_module\_link\_equipment\_tr): Establishes relationships between EquipmentModule and Equipment entities
1. **Work Orders and Time Series** (executed in parallel after equipment)
- **WorkOrder** (work\_order\_tr): Creates WorkOrder entities from RAW data
- **ISATimeSeries** (timeseries\_tr): Creates TimeSeries entities linked to assets and equipment
1. **Final Relationships**
- **WorkOrder-TimeSeries Linking** (work\_order\_timeseries\_tr): Links WorkOrders to their associated TimeSeries

After running the workflow you should have data in all the ISA specific views created for the project – testing and looking at the relationships in the Search UI would then look like this:

![[a91953babddc54e4c50d6aef59c0d51f_MD5.webp]]

### Test Data Loading

The workflow automatically loads test data from the included CSV files in the raw/ directory:

- isa\_asset.Table.csv: Sample ISA asset hierarchy data
- isa\_equipment.Table.csv: Sample equipment data
- isa\_timeseries.Table.csv: Sample time series data
- isa\_work\_order.Table.csv: Sample work order data

These CSV files are automatically uploaded to RAW tables during deployment and can be used to:

- Test the data model structure
- Validate transformations
- Demonstrate ISA-95/ISA-88 relationships
- Provide example data for development and training

### Workflow Features

- **Dependency Management**: Tasks execute only after their dependencies complete successfully
- **Error Handling**:
	- Asset tree build failure aborts the workflow (critical path)
	- Overlay failures skip individual tasks but continue the workflow
- **Retry Logic**: Each task includes retry logic (3 retries) for transient failures
- **Timeout Protection**: Tasks have timeout limits (3600 seconds) to prevent hanging

### Running the Workflow

After deployment, you can trigger the workflow in several ways:

1. **CDF Workflows UI**: Navigate to Workflows in CDF and trigger wf\_isa\_manufacturing
2. **CDF API**: Use the Workflows API to trigger the workflow programmatically
3. **Cognite Toolkit**: Use cdf workflows run command (if supported)

The workflow will process all transformations and load test data automatically, creating a complete ISA-95/ISA-88 manufacturing data model instance ready for use.

## ISA-95/ISA-88 Modeling Conventions and Relationships

### ISA-95 Hierarchy Levels

The model follows the ISA-95 standard hierarchy:

- **Level 0**: Enterprise (business organization) - Top-level organizational entity
- **Level 1**: Site (physical location) - Physical location where manufacturing operations are performed
- **Level 2**: Area (logical grouping within site) - Logical or physical grouping of process cells
- **Level 3**: ProcessCell (collection of units) - Collection of units performing specific manufacturing functions
- **Level 4**: Unit, EquipmentModule, Equipment (production equipment) - Basic equipment entities that carry out processing activities

**ISA-95 Level 3 Production Management** (separate from organizational hierarchy):

- **ProductDefinition**: Master definition of product processes and resources
- **ProductRequest**: Production orders/requests for specific quantities
- **ProductSegment**: Discrete segments within product definitions defining production activities

### ISA-88 Procedural Model

The model follows the ISA-88 procedural hierarchy:

- **Recipe**: Master recipe containing procedures - defines manufacturing process steps and parameters
- **Procedure**: Top-level procedural element coordinating unit procedures
- **UnitProcedure**: Procedural element defining operations for specific units
- **Operation**: Procedural element grouping phases for specific processing tasks
- **Phase**: Lowest level procedural element performing specific processing activities
- **ControlModule**: Control module for equipment control logic

### Integration Between ISA-95 and ISA-88 Hierarchies

The data model integrates ISA-95 organizational hierarchy with ISA-88 procedural model through several key relationships:

#### 1\. Organizational to Procedural Integration

- **Unit (ISA-95 Level 4) ↔ Recipe (ISA-88)**: Units are the physical equipment where recipes are executed. Recipes reference Units through ProductDefinitions, linking procedural control to physical equipment.
- **Equipment (ISA-88) ↔ Unit (ISA-95 Level 4)**: Equipment entities link to Units, connecting ISA-88 equipment model to ISA-95 organizational hierarchy.
- **Batch (ISA-88) ↔ Site (ISA-95 Level 4)**: Batches execute at specific Sites, providing organizational context for batch execution.

#### 2\. Production Management Integration

- **ProductDefinition (ISA-95 Level 3) ↔ Unit (ISA-95 Level 4)**: Product definitions specify which Units are used for production, linking production planning to organizational hierarchy.
- **ProductRequest (ISA-95 Level 3) ↔ WorkOrder (ISA-95 Level 3)**: Product requests generate WorkOrders for execution, connecting production planning to work management.
- **ProductRequest (ISA-95 Level 3) ↔ Batch (ISA-88)**: Product requests can be executed as Batches, linking ISA-95 production planning to ISA-88 batch execution.
- **ProductSegment (ISA-95 Level 3) ↔ ProcessParameter (ISA-88)**: Product segments define process requirements that map to ProcessParameters, connecting production planning to procedural control.

#### 3\. Process Data Integration

- **ProcessParameter (ISA-88) ↔ Phase (ISA-88)**: ProcessParameters are associated with Phases for procedural control during batch execution.
- **ProcessParameter (ISA-88) ↔ ProductSegment (ISA-95 Level 3)**: ProcessParameters also link to ProductSegments for production-level parameter tracking.
- **ISATimeSeries ↔ Multiple Entities**: Time series data links to:
	- **ISAAsset** (ISA-95): Asset-level process data
	- **Equipment** (ISA-88): Equipment-level sensor data
	- **Phase** (ISA-88): Phase-level execution data
	- **ProductSegment** (ISA-95 Level 3): Production segment-level data
	- **WorkOrder** (ISA-95 Level 3): Work execution data

### Time Series and Process Data Integration

#### CDF TimeSeries Integration

The ISATimeSeries entity implements CogniteTimeSeries from the CDF Common Data Model, providing seamless integration with CDF's time series infrastructure:

- **Process Data Acquisition**: ISATimeSeries enables real-time and historical process data collection from sensors, control systems, and equipment
- **Multi-Entity Linking**: Time series can be linked to multiple ISA entities simultaneously:
	- **ISA-95 Assets**: For asset-centric process monitoring and condition monitoring
	- **ISA-88 Phases**: For phase-level batch execution tracking
	- **ISA-95 ProductSegments**: For production segment-level process monitoring
	- **WorkOrders**: For work execution performance tracking
	- **Equipment**: For equipment-level sensor data and performance metrics
- **ProcessParameter Relationship**: ProcessParameters define what should be monitored (target values, limits, units), while ISATimeSeries provides the actual measured values. This separation enables:
	- Recipe-level parameter definitions (ProcessParameters)
	- Actual execution data collection (ISATimeSeries)
	- Comparison of actual vs. target values for quality control and process optimization

#### ProcessParameter and Time Series Workflow

1. **Definition Phase**: ProcessParameters are defined in Recipes or ProductDefinitions, specifying target values, min/max limits, and units
2. **Execution Phase**: During Batch execution or ProductSegment execution, ISATimeSeries collect actual process values
3. **Analysis Phase**: Actual values from ISATimeSeries can be compared against ProcessParameter targets for:
- Quality control and compliance
- Process optimization
- Deviation analysis
- Performance metrics

### Work Order Types and Relationships

WorkOrders serve multiple purposes in the manufacturing domain:

#### 1\. Production Work Orders

- **ProductRequest → WorkOrder**: Product requests generate WorkOrders for production execution
- **WorkOrder → Batch**: Work orders can execute Batches, linking ISA-95 work management to ISA-88 batch execution
- **WorkOrder → Equipment**: Work orders specify which Equipment is used for production
- **WorkOrder → ISATimeSeries**: Work orders link to time series for execution performance tracking

#### 2\. Maintenance Work Orders

- **WorkOrder → Equipment**: Maintenance work orders target specific Equipment for repair, inspection, or calibration
- **WorkOrder → ISAAsset**: Work orders link to ISA Assets for asset-centric maintenance tracking
- **WorkOrder → Personnel**: Work orders assign Personnel for work execution

#### 3\. Work Order as Activity

WorkOrders implement CogniteActivity, enabling:

- Activity-based queries and reporting
- Integration with CDF's activity tracking infrastructure
- Time-based work execution analysis
- Resource utilization tracking

### Relationship Patterns

- **Direct relationships**: Use source property to reference parent entities (e.g., Unit → ProcessCell, Phase → Operation)
- **Reverse relationships**: Use through with identifier to create reverse navigation (e.g., Batch → Procedures, ProductRequest → ProductDefinitions)
- **Multi-reverse relationships**: Use connectionType: multi\_reverse\_direct\_relation for collections (e.g., Recipe → Batches, ProductSegment → ProcessParameters)
- **Cross-hierarchy relationships**: Entities from different hierarchies can reference each other (e.g., ProductDefinition → Unit, Batch → Site, WorkOrder → Equipment)

## Data Flow and Relationship Examples

### Production Planning to Execution Flow

The following example illustrates how ISA-95 Level 3 production management integrates with ISA-88 batch execution:

1. **ProductDefinition** (ISA-95 Level 3) defines:
- Product process requirements
- Unit assignments (ISA-95 Level 4)
- ProductSegments with process requirements (temperature, pressure, flow rate, etc.)
1. **ProductRequest** (ISA-95 Level 3) is created:
- References ProductDefinition
- Specifies quantity, priority, due date
- Links to Unit for production location
1. **WorkOrder** (ISA-95 Level 3) is generated:
- Created from ProductRequest
- Links to Equipment for execution
- Assigns Personnel for work execution
1. **Batch** (Records) (ISA-88) executes:
- References Recipe (ISA-88 procedural model)
- Executes Phases (ISA-88) with ProcessParameters
- Links to WorkOrder for work management context
- Links to Site (ISA-95 Level 4) for organizational context
1. **ISATimeSeries** collects:
- Process data from Equipment during Phase execution
- Links to Phase, ProductSegment, WorkOrder, and Equipment
- Provides actual values for comparison with ProcessParameter targets

### Process Parameter and Time Series Example

**Scenario**: Monitoring temperature during a batch production phase

1. **ProcessParameter** defines:
- Parameter name: "Reactor Temperature"
- Target value: 75°C
- Min value: 70°C
- Max value: 80°C
- Unit of measure: °C
- Links to Phase (ISA-88) and ProductSegment (ISA-95 Level 3)
1. **ISATimeSeries** collects:
- Actual temperature measurements from sensor
- Links to Phase, ProductSegment, Equipment, and WorkOrder
- Provides time-stamped temperature values
1. **Analysis**:
- Compare ISATimeSeries actual values against ProcessParameter target
- Detect deviations outside min/max limits
- Generate quality reports and compliance documentation

### Work Order Types Example

**Production Work Order**:

- Created from ProductRequest
- Links to Equipment for production execution
- Generates Batch for ISA-88 execution
- Links to ISATimeSeries for performance tracking
- Assigns Personnel for work execution

**Maintenance Work Order**:

- Targets Equipment for repair or inspection
- Links to ISAAsset for asset context
- Links to ISATimeSeries for condition monitoring data
- Assigns Personnel for maintenance execution
- Tracks planned vs. actual start/end times

### Cross-Hierarchy Navigation Examples

**From ProductDefinition to Batch Execution**:

ProductDefinition → ProductRequest → WorkOrder → Batch → Phase → ProcessParameter

**From ISA-95 Organizational to ISA-88 Procedural**:

Site → Unit → Equipment → Phase → ProcessParameter → ISATimeSeries

**From Production Planning to Process Data**:

ProductSegment → ProcessParameter → Phase → ISATimeSeries

**From Work Management to Execution**:

WorkOrder → Equipment → Phase → Batch → Recipe

## Conventions & tips

- Keep implements: minimal and purposeful; include only the cdf\_cdm interfaces your view actually uses.
- Use consistent naming for properties and relation identifiers; prefer snake\_case for container property identifiers and descriptive names in views.
- Follow ISA-95/ISA-88 naming conventions in descriptions and property names.
- Include standard references (e.g., \[ISA-88\], \[ISA-95 Level 4\]) in property descriptions for clarity.
- Validate changes locally by running cdf build before deploying.
- Commit your changes alongside updates to transformations/workflows when introducing new entities.

## Standards References

- **ISA-95**: ANSI/ISA-95.00.01-2010 - Enterprise-Control System Integration Part 1: Models and Terminology
- **ISA-88**: ANSI/ISA-88.00.01-2010 - Batch Control Part 1: Models and Terminology

For more information about these standards, visit:

- \[ISA-95 Standard\](https://www.isa.org/standards-and-publications/isa-standards/isa-95)
- \[ISA-88 Standard\](https://www.isa.org/standards-and-publications/isa-standards/isa-88)

## Support

If you encounter issues with this module or deployment, consult Cognite documentation or contact your Cognite representative.

×

### Cookie settings

  

We use 3 different kinds of cookies. You can choose which cookies you want to accept. We need basic cookies to make this site work, therefore these are the minimum you can select. [Learn more about our cookies.](https://hub.cognite.com/site/terms)

<!-- SOURCE_END: Best Practices Library\Deployment Packs Library\ISA Manufacturing Data Model Extension  Cognite Hub.md -->

================================================================================

================================================================================

<!-- SOURCE_START: Best Practices Library\Deployment Packs Library\P&ID Annotation Workflow using CDF Data Modeling Cognite Official  Cognite Hub.md -->
## File: Best Practices Library\Deployment Packs Library\P&ID Annotation Workflow using CDF Data Modeling Cognite Official  Cognite Hub.md

---
title: "P&ID Annotation Workflow using CDF Data Modeling [Cognite Official] | Cognite Hub"
source: "https://hub.cognite.com/deployment-packs-472/p-id-annotation-workflow-using-cdf-data-modeling-cognite-official-4680"
created: 2025-12-28
description: "This how-to article describes and provides a structured example template for automating the P&ID annotation process in Cognite Data Fusion (CDF). Th..."
tags:
  - "CDF"
  - "CogniteDataFusion"
image: "https://uploads-eu-west-1.insided.com/cognite-en/attachment/e328b668-eef0-457a-b6a6-422e513533de_thumb.png"
---
---

- [Jan Inge Bergseth](https://hub.cognite.com/members/jan-inge-bergseth-202)
- MVP
- 11 replies

This how-to article describes and provides a structured example template for automating the P&ID annotation process in Cognite Data Fusion (CDF). The process leverages CDF Data Modeling and Workflows to automate annotation, linking P&ID documents to assets and other related files within the data model.

## Why Use This Module?

### Save Time and Accelerate P&ID Contextualization

This module is built on production-proven code that has been successfully deployed across multiple customer environments. Instead of building a P&ID annotation pipeline from scratch—which typically requires weeks or months of development, testing, and iteration—you can deploy this module in hours and begin contextualizing your P&ID documents immediately.

### Key Benefits

- ⚡ **Production-ready**: Battle-tested code based on real-world implementations running in production across several customers.
- 🚀 **Quick deployment**: Get up and running in hours, not weeks, through a simple configuration and deployment process.
- 🔧 **Easy to extend**: Clean, modular architecture makes customization straightforward.
- 📈 **Scalable foundation**: Runs as a single-threaded process by default, but is designed to be extended with parallel processing and asynchronous execution to handle large P&ID volumes.
- 🎯 **Proven results**: Incorporates best practices and lessons learned from multiple production deployments.

### Time Savings

- **Development**: Save weeks of development time by reusing proven, production-ready code.
- **Maintenance**: Reduce ongoing maintenance with stable, tested components.
- **Iteration speed**: Quickly adapt and extend the module to meet project-specific requirements.

Whether you are processing hundreds or thousands of P&ID documents, this module provides a solid foundation that can scale with your needs. Start with the single-threaded implementation for immediate value, and extend to parallel or asynchronous processing as volumes grow.

The final result is a set of populated annotations in the data model, linking P&ID files to assets and interrelated P&ID diagrams.

![[e748aa452bc8127a523fc80202a060a8_MD5.png]]

AD\_4nXeojo4x3cxaec0iic0JxojuuYPwwKn8ND-RRIuRxXL4MupIsIpTJMrYC-229nB0JixDVh21olTfnewbvi9V3koE\_txqBPMli7-S1lUyI0RlZFsUfZgm9eognfDsifoMPtm6j9EDMA?key=BP5dpwLNDUP5C90sHdGkhK-v

## Key Features of the Workflow

### Tagging Transformations for Input Filtering

- **Asset Tagging Transformation (**`**tr_asset_tagging**`**)**: Adds a `PID` tag to assets, enabling filtering during the annotation process. This serves as an example of how to control which assets are included in matching.
- **File Tagging Transformation (**`**tr_file_tagging**`**)**: Adds a `PID` tag to files, enabling filtering of which files are included in the annotation process.

These transformations can be customized to align with project-specific conventions for identifying relevant assets and files.

### Running the P&ID Annotation Process

The annotation workflow is configured via the extraction pipeline to match the structure and naming of your data model. Configuration includes:

- **Instance spaces** – where your data is stored
- **Schema spaces** – schema definitions of the data model
- **External IDs** – for extended views or types
- **View/type version**
- **Search properties** – used for matching P&IDs to assets and files
- **Filter properties and allowed values** (for example, tag values)
- **Debug mode** and extensive logging
- **Single-file processing** via configuration input
- **Delete functionality** for cleanup scenarios

If you update or change thresholds for automatic approval or suggestions, existing annotations created by this workflow should be cleaned up. Only annotations created by this process are removed; manually created annotations or annotations from other processes are preserved. When cleanup is disabled, external IDs prevent duplicate annotation creation.

State is managed using a RAW database/table to support incremental processing. The workflow uses synchronous Data Modeling APIs to process only new or updated files.

#### Run Modes

- **Incremental mode**: Processes only new or updated P&ID files using stored state.
- **ALL mode**: Clears status and logs from previous runs in RAW and reprocesses all P&ID files.
- **Full cleanup**: To also delete previously created annotations, set `cleanOldAnnotations = True` in the configuration.

### Annotation Logic

- Optional filtering of files and/or assets using configured filter properties
- Matching based on search properties between P&IDs, assets, and files
- Batch-level retry (up to three attempts) on failure
- Fallback to individual file processing if batch processing fails
- Failed individual files are logged and skipped
- All matches are written to RAW tables (document-to-asset and document-to-document matches)
- Threshold-based logic for automatic approval versus suggestion
- Annotation creation using the Data Modeling service
- Detailed status logging to the extraction pipeline

## Performance and Scalability

The current implementation processes files sequentially in a single-threaded mode, making it ideal for fast onboarding and moderate P&ID volumes. For large-scale production environments (thousands of files), the module can be extended with:

- **Parallel processing** to reduce overall execution time
- **Asynchronous operations** for improved resource utilization
- **Batch size optimization** based on infrastructure and file characteristics

The modular architecture makes these enhancements straightforward to implement as requirements grow.

> **Note:** Naming conventions should be adjusted to align with project-specific standards.

## Output & Visualization

- The workflow generates annotations that are displayed on P&ID diagrams as:
	- Purple boxes – Linking to assets.
	- Orange boxes – Linking to files.
- These annotations enhance data contextualization and improve traceability between P&IDs and related assets.

![[d90a5ae02a22bddfa19bbe0a0aeaa29e_MD5.png]]

AD\_4nXcKeEtWUcHZevWKpC72FJuoxwklzV6u6t\_HrsQlvgycfBlJQlDadXIcCMxCEye2lctKn\_TmjaPciNTY13UeC\_Q8Fa5hBNg7rF8f2yS87TUqoq5VpaDFWi54u2sZhlfRJVwH8yAW?key=BP5dpwLNDUP5C90sHdGkhK-v

## Deployment (Cognite Toolkit)

### Prerequisites

Before you start, ensure you have:

- A Cognite Toolkit project set up locally
- Your project contains the standard cdf.toml file
- Valid authentication to your target CDF environment
- Access to a CDF project and credentials
- cognite-toolkit >= 0.7.33

**Step 1: Enable External Libraries**

Edit your project's cdf.toml and add:  

```
1[library.cognite] 
2
3url = "https://github.com/cognitedata/library/releases/download/latest/packages.zip" 
4
5checksum = "sha256:795a1d303af6994cff10656057238e7634ebbe1cac1a5962a5c654038a88b078"
```

This allows the Toolkit to retrieve official library packages.

### Step 2 (Optional but Recommended): Enable Usage Tracking

To help improve the Deployment Pack:

```
1cdf collect opt-in
```

### Step 3: Add the Module

Run:

```
1cdf modules init . --clean
```

**⚠️ Disclaimer**: This command will overwrite existing modules. Commit changes before running, or use a fresh directory.

This opens the interactive module selection interface.

### Step 4: Select the P&ID Annotation Package (NOTE: use Space bar to select module)

From the menu, select (Note, select using space bar):

```
1Contextualization: Module templates for data contextualization  
2  └── Contextualization P&ID Annotation
3
```

### Step 5: Verify Folder Structure

After installation, your project should now contain:

```
1modules
2     └── accelerators
3         └── contextualization
4             └── cdf_p_and_id_annotation
5
```

If you want to add more modules, continue with yes ('y') else no ('N')

And continue with creation, yes ('Y') => this then creates a folder structure in your destination with all the files from your selected modules.  

### Step 6: Deploy to CDF

NOTE: Update your config.dev.yaml file with project, source\_id and changes in spaces or versions

**Optional:** Modify the module’s default configuration to fit your project

- In your CDF toolkit configuration yaml file, update:
	- location\_name: replace LOC with the location name / Asset name that you are working on
	- source\_name: replace SOURCE with the source where your P&ID files are extracted from
- Update the folder structure:

Rename files replacing LOC and SOURCE where applicable. (or just test with default values)

Build deployment structure:

```
1cdf build
```

Optional dry run:

```
1cdf deploy --dry-run
```

Deploy module to your CDF project

```
1cdf deploy
```
- Note that the deployment uses a set of CDF capabilities, so you might need to add this to the CDF security group used by Toolkit to deploy

## Testing the Annotation module

If you wan to test the annotation process, it is possible to download test data from the included source data in CDF Toolkit.

The process to do this would be:

- In your local cdf.toml file:
```
1[library.cognite]
2url = "https://github.com/cognitedata/toolkit-data/releases/download/latest/packages.zip"
```
- Before you run cdf modules init. note the the local folder structure will be updated lost with the now content from the modules you now download (so copy/store or run the init of the annotation module again after setting up the test data)
```
1cdf modules init . --clean
```
- Select package:
	- Models: Example of Minimum Extension of the Cognite Process Industry Model
- Select Yes to add more modules, and select:
	- SourceSystem: Module templates for setting up a data pipeline from a source system
	- And then (Note, select using space bar)
		- SAP Asset data
		- SharePoint files

After installation, your project should now contain:

```
1modules
2     ├── models
3     │   └── cdf_process_industry_extension
4     └── sourcesystem
5         ├── cdf_sap_assets
7
8
```

If you want to add more modules, continue with yes ('y') else no ('N')

And continue with creation, yes ('Y') => this then creates a folder structure in your destination with all the files from your selected modules.

- Edit your **config.dev.yaml** file
	- Project è should be your CDF project name
	- For cdf\_files and cdf\_sap\_assets (details on access control see CDF documentation):
		- groupSourceId: if using Azure, access group ID from Entra ID used by system user and/or users setting up CDF that should be able update data model and run Transformations
		- workflowClientId: environment variable with Entra ID object ID for APP
		- workflowClientSecret: environment variable with secret value for APP

Build deployment structure:

```
1cdf build
```

Optional dry run:

```
1cdf deploy --dry-run
```

Deploy module to your CDF project

```
1cdf deploy
```

**⚠️** **NOTE**: Before running workflow that upload Assets/ Tags and creates hierarchy – make sure your transformation: Asset Transformations for SAP Springfield S/4HANA Assets don’t filter on ‘1157’ - making sure that all Assets are loaded to data model!

Run workflows to load data

- **Sap\_s4ana\_population**:
- **files\_metadata\_springfield**:

You should now have test data to run a simple example of the annotation process:-)

## Run the P&ID File Annotation workflow

If your module structure was overwritten by the models for test data, clean up, merge or just run the module init again as instructed above for the P&ID annotation module. This to make it easy to make changes and redeploy when you are making the P&ID process relevant for your project.

After deployment, trigger the workflow click on Run): **P&ID File Annotation** process via the CDF Workflows UI or API to execute the transformations in order.

In **Integrate / Data Workflows**: P&ID File Annotation process

![[3d228203f4ddd7387fd1325023bf6103_MD5.webp]]

Click on Run to trigger the workflow

NOTE: failure to run could be related to access issues. The created access group **gp\_files\_LOC\_processing** should be connected to a Source ID that was part of the configuration you did before **cdf build**. If the provided source ID not is connected to the application ID / Secret:

functionClientId: ${IDP\_CLIENT\_ID}

functionClientSecret: ${IDP\_CLIENT\_SECRET}

The processes will fail. For details on access control see Cognite documentation: [https://docs.cognite.com/cdf/access](https://docs.cognite.com/cdf/access)

In CDF UI: Integrate/Staging, after running you should have (RAW Database & Tables):

![[7a686c36761de8ffc685bd8b3b76bbe3_MD5.png]]

AD\_4nXdbTOcx0e3LTHeiN1xv6ronhxBNlpo8imYNee-185BvxOK\_MJQIg66PwHXTorCVvB-1vWoXhNjE0tTlFt1171vKs\_ihxSt\_nZIj56X4IkY18T0guaz-v3k9glDaNM8T47q0ioL1?key=BP5dpwLNDUP5C90sHdGkhK-v

In CDF UI: Build Solutions / Functions

![[758c705b8cce8d3e83b568a6ea770b37_MD5.webp]]

Click on **View logs** and verify that the included test data files where processed without any errors.

Use CDF Search (in Industrial Tools) to verify that annotations are correctly applied.

Confirm that P&ID assets are correctly linked within the data model.

![[f927ae8bd3ec36cebafe7a3fcb3fcb12_MD5.png]]

AD\_4nXcVOlHj3bbUjD\_v1hIx7mqp9EHrdB2JSLNSzG8CG-cKWu1\_DeHRxcywuM2TwpAJvBYkL4edef1KL4LZG6oBNn1xQKCN\_0Y\_yVZDKn35\_Qz-NNhjqnUoxKgQXuTxF-vdxCamvEj48A?key=BP5dpwLNDUP5C90sHdGkhK-v

**Next Level**

With the P&ID Workflow you have an important building block in creating a workflow or process around the annotation process. In the illustration below you now have the processes for:

- Process for Alias creations
- Reading of your sources to be annotated
- Diagram detection or annotation of the P&ID
- The output related to mapped Documents & Assets in RAW tables

Then utilizing this in your custom application or process with P&ID Edit functionality would provide high quality annotations that can be used in multiple settings/applications /use cases.

![[20eecefbaf270e8cad78e4892da88863_MD5.png]]

AD\_4nXdhRL\_P31wRKUaGBM3R6G7MID6A1bCDOzzd4M4Y0GZFsO9uwLbZPfP22rrErYTzWsNuslPVKCQoIHi1L7AvQSaJlSXHoLJLt67jiecccpcM8t-gQp\_Cg0\_mBRj4qEVH9sI5lDBomA?key=BP5dpwLNDUP5C90sHdGkhK-v

×

### Cookie settings

  

We use 3 different kinds of cookies. You can choose which cookies you want to accept. We need basic cookies to make this site work, therefore these are the minimum you can select. [Learn more about our cookies.](https://hub.cognite.com/site/terms)

<!-- SOURCE_END: Best Practices Library\Deployment Packs Library\P&ID Annotation Workflow using CDF Data Modeling Cognite Official  Cognite Hub.md -->

================================================================================

================================================================================

<!-- SOURCE_START: Best Practices Library\Deployment Packs Library\Root Cause Analysis (RCA) Agents Module  Cognite Hub.md -->
## File: Best Practices Library\Deployment Packs Library\Root Cause Analysis (RCA) Agents Module  Cognite Hub.md

---
title: "Root Cause Analysis (RCA) Agents Module | Cognite Hub"
source: "https://hub.cognite.com/deployment-packs-472/root-cause-analysis-rca-agents-module-5808"
created: 2025-12-28
description: "Overview	Dependencies	Deployment	Agents	How It Works	Support Overview The Root Cause Analysis (RCA) module provides intelligent Atlas AI agents for adv..."
tags:
  - "CDF"
  - "CogniteDataFusion"
image: "https://uploads-eu-west-1.insided.com/cognite-en/attachment/1440x640/d94c3120-1679-462d-989c-cb77c2577184_thumb.png"
---
![[c1961bf674952a03d8d5dfff6c1f577c_MD5.webp]]

- [Charan Raju](https://hub.cognite.com/members/charan-raju-7025)
- Practitioner

## Overview

The Root Cause Analysis (RCA) module provides intelligent Atlas AI agents for advanced root cause analysis capabilities in your Cognite Data Fusion (CDF) environment. This module is designed to work with the RMDM (Reliability Maintenance Data Model) v1 data model deployed in CDF.

## Dependencies

> ⚠️ **Important:** This module requires the **data\_models/rmdm\_v1/** module to be deployed in your CDF project **before** deploying these agents.

All agents in this module connect to and query the RMDM data model within CDF to access equipment, failure notifications, time series, and other maintenance-related data.

---

## Deployment

### Prerequisites

Before you start, ensure you have the following:

- You already have a Cognite Toolkit project set up locally
- Your project contains the standard `cdf.toml` file
- You have valid authentication to your target CDF environment
- **The RMDM v1 data model is already deployed**  (see  `data_models/rmdm_v1/`)

### Step 1: Enable External Libraries and Agents

Edit your project's `cdf.toml` and add:

```
1[alpha_flags]
2external-libraries = true
3agents = true
4
5[library.cognite]
6url = "https://github.com/cognitedata/library/releases/download/latest/packages.zip"
7checksum = "sha256:795a1d303af6994cff10656057238e7634ebbe1cac1a5962a5c654038a88b078"
```

This allows the Toolkit to retrieve official library packages and enables Atlas AI agent deployment.

> **📝 Note: Replacing the Default Library**
> 
> By default, a Cognite Toolkit project contains a `[library.toolkit-data]` section pointing to `https://github.com/cognitedata/toolkit-data/...`. This provides core modules like Quickstart, SourceSystem, Common, etc.
> 
> **These two library sections cannot coexist.** To use this Deployment Pack, you must **replace**  the  `toolkit-data`  section with  `library.cognite`:
> 
> | Replace This | With This |
> | --- | --- |
> | `[library.toolkit-data]` | `[library.cognite]` |
> | `github.com/cognitedata/toolkit-data/...` | `github.com/cognitedata/library/...` |
> 
> The `library.cognite` package includes all Deployment Packs developed by the Value Delivery Accelerator team (RMDM, RCA agents, Context Quality Dashboard, etc.).

> **⚠️ Checksum Warning**
> 
> When running `cdf modules add`, you may see a warning like:
> 
> ```bash
> 1WARNING [HIGH]: The provided checksum sha256:... does not match downloaded file hash sha256:...
> 2Please verify the checksum with the source and update cdf.toml if needed.
> 3This may indicate that the package content has changed.
> ```
> 
> **This is expected behavior.** The checksum in this documentation may be outdated because it gets updated with every release. The package will still download successfully despite the warning.
> 
> **To resolve the warning:** Copy the new checksum value shown in the warning message and update your `cdf.toml`  with it. For example, if the warning shows  `sha256:da2b33d60c66700f...`, update your config to:

> ```bash
> 1[library.cognite]
> 2url = "https://github.com/cognitedata/library/releases/download/latest/packages.zip"
> 3checksum = "sha256:da2b33d60c66700f..."
> ```

To help improve the Deployment Pack and provide insight to the Value Delivery Accelerator team, you can enable anonymous usage tracking:

```
1cdf collect opt-in
```

This is optional, but highly recommended.

### Step 3: Add the Module

Run:

```
1cdf modules init .
```

> **⚠️ Disclaimer**: This command will overwrite your existing modules in the current directory. Make sure to commit any changes before running this command, or use it in a fresh project directory.

This opens the interactive module selection interface.

### Step 4: Select the Atlas AI Deployment Pack

From the menu, select:

```
1Atlas AI Deployment Pack: Deploy all Atlas AI modules in one package.
2
```

Then select **RCA with RMDM** module.

Follow the prompts. Toolkit will:

- Download the RCA agents module
- Update the Toolkit configuration
- Place the files into your project

### Step 5: Verify Folder Structure

After installation, your project should now contain:

```
1modules/
2    └── atlas_ai/
3        └── rca_with_rmdm/
4            ├── agents/
5            │   ├── cause_map_agent.Agent.yaml
6            │   ├── rca_agent.Agent.yaml
7            │   └── ts_agent.Agent.yaml
8            ├── data_sets/
9            │   └── rca_resources.DataSet.yaml
10            ├── files/
11            │   ├── combined_cause_map.CogniteFile.yaml
12            │   └── combined_cause_map.json
13            ├── module.toml
14            └── README.md
15
```

If you see this structure, the RCA agents module has been successfully added to your project.

### Step 6: Deploy to CDF

Build and deploy as usual:

```
1cdf build
```
```
1cdf deploy --dry-run
```
```
1cdf deploy
```

After deployment, the RCA agents will be available in your CDF environment's Atlas AI.

---

## Agents

This module contains three specialized Atlas AI agents that work together to provide comprehensive root cause analysis capabilities:

### 1\. Cause Map Agent (cause\_map\_agent.Agent.yaml)

The Cause Map Agent helps users generate visual cause maps for equipment failures.

**What it does:**

- Finds equipment and retrieves its latest failure notification, failure mode, and equipment class
- Generates a structured cause map showing the relationships between failure modes, failure mechanisms, root cause categories, and specific root causes
- Can work with either the latest high-priority failure notification or the most common failure mode for a piece of equipment
- Automatically builds and displays the cause map on the canvas, expanding the top 3 failure mechanisms with the highest failure rates
- Queries the RMDM data model for Equipment, FailureNotification, FailureMode, and EquipmentClass views

### 2\. RCA Agent (rca\_agent.Agent.yaml)

The RCA Agent is the main agent for conducting comprehensive root cause analysis investigations.

**What it does:**

- Guides users through a complete RCA workflow for failing equipment or assets
- Retrieves multiple types of data from the RMDM knowledge graph including:
	- Assets and their hierarchical relationships (parent, children, siblings)
	- Maintenance orders and work orders (corrective and preventive)
	- Failure notifications
	- Related documents and files (P&IDs, technical documentation)
	- Images
	- Time series metadata
- Acts as a maintenance professional and RCA expert, asking proactive follow-up questions
- Provides objective information and analysis without making assumptions or hallucinating
- Helps identify the most common failures and related patterns

### 3\. Time Series Agent (ts\_agent.Agent.yaml)

The Time Series Agent specializes in retrieving and analyzing time series data for equipment.

**What it does:**

- Retrieves time series data from the RMDM knowledge graph for assets
- Finds time series for related assets (children, siblings, or parent assets)
- Plots and visualizes time series data to identify trends, patterns, and anomalies
- Performs statistical analysis including computing averages with optional filtering
- Provides insights and recommendations based on time series analysis
- Acts as a maintenance professional and data analyst expert
- Guides users through time series analysis workflows within the industrial domain

## How It Works

All three agents connect to the RMDM data model (space: `rmdm`, version: `v1`) deployed in your CDF environment. They use various tools including:

- Knowledge graph queries to retrieve data from RMDM views
- Python code execution for complex data processing and analysis
- Document Q&A for analyzing technical documentation
- Image analysis for visual inspection
- Time series data retrieval and computation

These agents work together to provide a complete root cause analysis experience, from identifying equipment and failures, to analyzing historical data, to generating visual cause maps that help identify the root causes of equipment failures.

---

## Support

For troubleshooting or deployment issues:

- Refer to the [Cognite Documentation](https://docs.cognite.com/)
- Contact your **Cognite support team**
- Join the Slack channel **#topic-deployment-packs** for community support and discussions

×

### Cookie settings

  

We use 3 different kinds of cookies. You can choose which cookies you want to accept. We need basic cookies to make this site work, therefore these are the minimum you can select. [Learn more about our cookies.](https://hub.cognite.com/site/terms)

<!-- SOURCE_END: Best Practices Library\Deployment Packs Library\Root Cause Analysis (RCA) Agents Module  Cognite Hub.md -->

================================================================================

