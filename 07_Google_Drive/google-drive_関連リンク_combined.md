# SoP：データモデリング（DM）におけるベストプラクティスとピアレビュー - 関連リンク
Generated from: Confluence (SoP：データモデリング（DM）におけるベストプラクティスとピアレビュー)

================================================================================

<!-- SOURCE_START: 関連リンク\Best Practices - Data Modeling  Cognite Hub.md -->
## File: 関連リンク\Best Practices - Data Modeling  Cognite Hub.md

---
title: "Best Practices - Data Modeling | Cognite Hub"
source: "https://hub.cognite.com/best-practices-internal-461/best-practices-data-modeling-5729"
created: 2025-12-28
description: "Authors	Overview	References	Best Practices	1. Layered Architecture	Source Data Models		Enterprise Data Models		Solution Data Models		Solution Model Writ..."
tags:
  - "CDF"
  - "CogniteDataFusion"
image: "https://uploads-eu-west-1.insided.com/cognite-en/attachment/1440x640/8cb6f445-74c0-41f5-b440-3c2625ad8a46_thumb.png"
---
![[198f6304bfaf24adabb981768d0913ad_MD5.webp]]

- [Magnus Schjølberg](https://hub.cognite.com/members/magnus-schjoelberg-5193)
- Practitioner

---

## Authors

Colin Nattrass  
Magnus Schjølberg  

---

## Overview

This guide outlines a strategic approach to designing scalable and efficient **data models** in Cognite Data Fusion (CDF), focusing on **core principles to ensure efficiency, maintainability, scalability, and security**.  

| **Service** | **Description** |
| --- | --- |
| [**Data Modelling Service**](https://docs.cognite.com/cdf/dm/) | Data Modelling Service (DMS) is the engine within CDF that builds and manages an Industrial Knowledge Graph. It uses a property graph structure (nodes, edges, and properties) with components like spaces, containers, and views to organise and contextualise industrial data. DMS provides APIs for creating, ingesting, and querying these models. |
| [**NEAT**](https://cognite-neat.readthedocs-hosted.com/en/latest/) | NEAT is a user-friendly solution for both domain experts and developers that enables the rapid development of data models. It also handles the extraction, transformation, and loading (ETL) of data instances, and ingests both the models and instances as knowledge graphs into Cognite Data Fusion. It can be best thought of as an interface to the Data Modelling Service, outside of CDF. |
| [**Toolkit**](https://docs.cognite.com/cdf/deploy/cdf_toolkit/) | Tooling to manage, configure, and deploy resources in CDF. |
| [**Atlas AI**](https://www.cognite.com/en/product/atlas) | Low-code workbench for building and deploying industrial AI agents that leverage contextualised data to address specific operational use cases. |

---

## References

The content for this condensed Best Practices guide is derived from the extensive peer-reviewed [Cognite Data Model Pattern document](https://docs.google.com/document/d/1654_FikBLXVauHtCvZxOQuK3xFEtkf9QY7LFs0LIxb0/edit?tab=t.0#heading=h.80ki2felbbcj) (author: Magnus Schjølberg). Note that this is currently a Work In Progress, but it is the authoritative long-form reference.  
  
Future updates to this guide will include references/examples within the [GSS Knowledge Base](https://github.com/cognitedata/gss-knowledge-base/) of the described Best Practices in practice.

---

## Best Practices

## 1\. Layered Architecture

Adopt a layered data model architecture that separates responsibilities and optimises for different use cases.  

#### Source Data Models

- Owned by **Data Producers**. This role loads data into Source Data Models, ensuring its quality and freshness. Responsibilities include building ELT pipelines into CDF, managing upstream data lineage, and continuously monitoring the source data for issues.
- Represent a "raw" copy of data from a single source system.
- They ensure data availability and quality and are roughly equivalent to the **silver layer** in a medallion architecture. [Refer to this source](https://dataengineering.wiki/Concepts/Data+Architecture/Medallion+Architecture) for a comprehensive overview of this data architecture paradigm.  
	![[94cd0c8bec791eb21c73c158964aaa68_MD5.webp]]
	*"Delta Lake Medallion Architecture" by Databricks"*

#### Enterprise Data Models

- Managed by **Data Modellers**. This role develops and maintains the Enterprise Data Model, using business knowledge to connect various Source Data Models. They map the EDM to solution models and, within CDF, utilize CDM, balancing scaling, performance, and storage constraints.
- The core layer for integrating and contextualising data from multiple sources.
- Serves as the single, validated source of truth.
- This layer optimises query performance and data storage by grouping granular asset types into higher-level classes. This layer is analogous to the **gold layer**.

#### Solution Data Models

- Solution Data Models are defined by **Data Consumers**. This role accesses solution models for specific use cases. Using domain knowledge, they specify requirements and work with the Data Modeller to find data in the Enterprise Data Model, helping define new solution schemas for integration as needed.
- They provide tailored, read-only views for specific applications.
- As a **general rule, data is mapped into Views directly** from the Enterprise Data Model containers rather than being transformed or duplicated.
- In specific cases, **the model can include its own containers** (populated by discrete Workflows) to store data that is unique to a particular use case or application.

#### Solution Model Write-Back Principle

- For write-back use cases, data must be written to a **separate Source Data Model** (owned by a Data Producer), never directly into the Enterprise layer.

Note: In certain scenarios (e.g. Quickstart projects), we suggest a simplified version of this layered architecture to expedite time to value (for example, with only two layers). The aforementioned describes a target architecture at enterprise scale.

### 2\. Core Design Principles

  
Fundamental design principles for creating efficient and scalable data models, including avoiding redundant data, distributing properties logically, and optimizing for querying performance.

#### Avoid Duplication via Shared IDs

- A single entity should be represented once in the knowledge graph.
- Data across views and layers is flexibly linked by using the **same Instance ID** (Space \+ External ID).
- Explicit direct relations are typically only needed to enable navigation in the UI/GraphQL or when linking instances that have different IDs.

#### Extend Core Data Models (CDM)

- Custom views **must extend** (implement) the relevant base CDM types (e.g., extend CogniteAsset → Asset).
- **Avoid using original CDM Views directly** to prevent schema conflicts and simplify relations. Relating CDM Views to custom Views that extend CDM is complicated, risks breaking the schema and losing relations.
- **Include all relevant CDM types for the best GraphQL and CDF service compatibility**; however, omit those not utilised (for example, excluding Cognite3DModel if there is no 3D model).
- **When extending CogniteAsset, it is recommended to do this once (with a single Asset type) and provide links to domain-specific dimensions** in a direct relation. This is opposed to the pattern of having a single-purpose wide and sparse View/Container, or multiple extensions of the CogniteAsset type.  
	- A top-level/central type makes the model easy to navigate/query
	- Allows implementation of specific hierarchies within the related sub-types
	- Provides flexibility to link documents and timeseries to all levels in the hierarchy
- A simplified example is as below, where a single Asset type implements CogniteAsset, with relations to LifeCycleInformation and MaintenanceAndIntegrity data-domains, and a TimeseriesReference back to the central Asset type).  
	  
	![[099d123810031b854eb40e322e22a8e9_MD5.webp]]
- **Note, the number of Views should be balanced** \- the more Views added, the more transformations/resources are needed in order to populate the data.

#### Distribute Properties Contextually

- Split properties for the same instance across **multiple, context-specific containers/views**.
- This ensures compliance with the **300-property view limit** and improves AI accuracy by restricting the view to only context-relevant properties. For example, a property like 'pipe coating' would be excluded from a view focused on 'Pump' equipment.

#### Model Relationships

- Use **Direct Relations** as the default type (One:One, One:Many, Many:Many). Include a reverse direct relation.
- Use **Edges** only for required relationship properties or to exceed the **2,000 linked instance limit**. Edges are great when you need to **traverse the graph** in your queries or **attach extra properties** to the relationship, but they do come with added complexity and cost. In contrast, **direct relations are simpler, faster, and less resource-intensive**.

### 3\. Query & Performance Optimisation

Implement indexing and constraints for efficiency and to prevent timeouts.  

#### Indexing Strategy

- Add **B-Tree indexes** to scalar properties/direct relations used for filtering or reverse traversal.
- Use **Inverted indexes** for list-type properties.
- Note the limit of **10 indexes per container**.

#### Container Constraints

- **It is mandatory to use the “requires” container constraints** (e.g., Pump requires Asset) on views that implement others. This enables critical under-the-hood query optimisations; it is not possible today to get acceptable performance without this.

#### AI Optimization

- **Use human-readable names and descriptions** for all properties to aid users and AI agents (Atlas AI).
- **De-normalise properties** that are frequently queried, and give additional context (applicable to AI agents, applications and human users)

### 4\. Security & Governance

Separate schema from data instances and use tooling for controlled deployment.  

#### Separate Schema and Instance Spaces

- Use distinct **schema spaces** (for model definitions) and **instance spaces** (for data). This enables granular access control.

#### Access Control Distinction

- datamodels:read gives schema access; **datamodelinstances:read** is required to read any data.
- Timeseries and Files are special cases to consider. Conceptually, they are meta/container types: the data model instance (the "node") points to the actual bulk storage for its data (the datapoints or the file bytes).  
	  
	To control access, you need two distinct types of permissions:  
	- **Schema Access:** You need **dataModels:read** on the **cdf\_cdm** space. This is required for your application to understand the *structure* and *definition* of what a CogniteTimeSeries or CogniteFile is.
	- **Data Access:** You need **dataModelInstances:read** on the **instance space** (e.g., my-asset-space). This is what actually grants access to read the specific instances and their associated timeseries datapoints or file bytes. Therefore, using distinct instance spaces is the essential strategy for segregating *data* access (within and outside of the Data Modelling Service).

#### Implement Location-Based Access

- Separate instances by creating **a space for each location** (e.g., sp\_location\_a\_instances) to control data access using CDF groups.  
	- Note: Limits on space count within the project should be considered when implementing a strategy that further subdivides data by source\_system, location or use case (source\_system + location + use case could result in an exponential growth in usage of spaces for enterprise-scale deployments).

#### Source/Enterprise Layer Instance Strategy

- **A strategy for instances between the source and enterprise layers should be defined** (in the Solution Layer, we are generally mapping data within the Enterprise Layer).
- **Separation** prevents data deletion risk and enables data isolation (e.g., for competitors) but increases the instance count.
- **Combining** reduces instances and simplifies mapping, but risks unintended data loss (extra care needs to be taken when making changes in the Source Layer, since deleting an instance in a source model would mean the same instance is deleted in the enterprise model).
- **Recommendation:** We recommend using **separate instances for the Source and Enterprise layers**, where each domain owns the instance space it writes to.
- **Example:** A PI extractor would create an extractor Source Model, with an instance space owned by the source domain. The derived domain (Enterprise Model) uses a separate instance space owned by the Enterprise layer.

### 5\. Deployment & Deployment of Data Models

#### Toolkit, Source Code Management and Deployment

- Use the **Cognite Toolkit** as the primary tool for managing and deploying Data Models.
- Store models in YAML format in in a **Source Code Management platform (Git)** to enable versioning, schema validation, and deployment across environments (Dev, Staging, Prod) using a CI/CD pipeline.

#### NEAT

- **Consider NEAT as a candidate for developing data models**, particularly in scenarios requiring the involvement of domain SMEs from the customer side. It can be leveraged to have them participate in both model design and implementation.  
	- Key advantages for SME involvement:  
		- **Model Design:** NEAT simplifies the extension of CDM types and the creation of an Enterprise Model that can be subdivided into Solution Models.
		- **Physical Modelling:** It enables the configuration of performance optimisations, such as indices and constraints.
	- Limitations:  
		- **NEAT may not be as useful where the majority of work is done by people with strong, pre-existing experience in Data Modelling Service**, as they may not require the specific
- **In all cases (including when using NEAT)**, you should still govern and version the model using the Toolkit and Git. This approach provides better change control, traceability, and standardized deployments (e.g., Gitflow). NEAT models, often stored in Excel, can be converted to YAML for deployment with the Toolkit.

### 6\. Naming & Versioning

Maintain consistency for long-term clarity and easy integration management.  

#### Follow Naming Conventions

[Follow the Naming Convention best practices](https://hub.cognite.com/best-practices-internal-461/best-practices-naming-conventions-5734). Key recommendations are listed below, and full details are available in the main guide.

- Use **PascalCase** for externalId of containers/views (e.g., CentrifugalPump).
- Use **camelCase** for property identifiers (e.g., ratedCurrent).
- **Avoid company names** in externalId.

#### Manage Versioning

- Use a **numerical versioning scheme** (v1, v1.0.0).
- **Bump the View version with every schema change** (Recommended).

#### Toolkit Variables

- **Use** [**Toolkit variables**](https://docs.cognite.com/cdf/deploy/cdf_toolkit/api/config_yaml#the-variables-section) to configure names (e.g. space names) and versions, avoiding the need to manually update all YAML configurations in the event of a change.

---

### Code Examples

  
[GSS Code Knowledge Base](https://github.com/cognitedata/gss-knowledge-base/tree/main/data_models): Contains several examples of Data Model definitions and queries, a good place to start.  

---

### Checklist

| **Category** | **Checklist Item** | **✅** |
| --- | --- | --- |
| **Layered Architecture** | Source Data Model(s) exist (A "raw" copy of data from a single source). |  |
|  | Enterprise Data Model(s) exist (data aggregates from multiple sources). |  |
|  | Enterprise Data Model(s) provide data that is generic and understandable without source system knowledge. |  |
|  | Solution Data Model(s) exist (provided for specific use cases). |  |
|  | Solution Models write-back data to a separate Source Data Model. |  |
| **Core Design & Scalability** | Properties are distributed across contextual containers and views. |  |
|  | Custom views extend (implement) the necessary base CDM types. |  |
|  | CogniteAsset is extended only once and linked to domain-specific dimensions. |  |
|  | Unnecessary data duplication is avoided. |  |
|  | Models use shared instance IDs to enable flexible linking. |  |
|  | Direct Relations are used as the default relationship. |  |
| **Query & Performance Optimisation** | B-Tree and Inverted indexes are used on frequently filtered properties. |  |
|  | requires container constraints are used on views that implement others. |  |
|  | Human-readable names and descriptions for all properties to aid users and AI agents. |  |
|  | De-normalized properties that are frequently queried and give additional context. |  |
| **Governance & Security** | Schema and instance spaces are kept separate. |  |
|  | Access control is set up per facility, plant, or site. |  |
|  | A clear instance strategy for the Source and Enterprise layers is defined. The recommendation is to separate. |  |
| **Development & Deployment** | The Cognite Toolkit is used to manage resources. |  |
|  | A source code management platform like Git is used. |  |
|  | NEAT is considered as one option to develop data models, where customer SMEs are heavily involved. |  |
| **Naming & Versioning** | An explicitly defined naming convention is in place, referencing the defined Best Practices. |  |
|  | A numerical versioning pattern is used. |  |
|  | View versions are incremented for every schema change. |  |
|  | Toolkit variables are used to configure names (e.g., space names) and versions. |  |

×

### Cookie settings

  

We use 3 different kinds of cookies. You can choose which cookies you want to accept. We need basic cookies to make this site work, therefore these are the minimum you can select. [Learn more about our cookies.](https://hub.cognite.com/site/terms)

<!-- SOURCE_END: 関連リンク\Best Practices - Data Modeling  Cognite Hub.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 関連リンク\Best Practices - Naming Conventions  Cognite Hub.md -->
## File: 関連リンク\Best Practices - Naming Conventions  Cognite Hub.md

---
title: "Best Practices - Naming Conventions | Cognite Hub"
source: "https://hub.cognite.com/best-practices-internal-461/best-practices-naming-conventions-5734"
created: 2025-12-28
description: "Author	Overview	Objective		Definitions		Definition of Artifact			Definition of Concept 			Typical concepts to include in names			Location			Data model t..."
tags:
  - "CDF"
  - "CogniteDataFusion"
image: "https://uploads-eu-west-1.insided.com/cognite-en/attachment/1440x640/f99c6982-b91f-4bb4-b255-a322ee15159e_thumb.jpg"
---
![[2882744b2e8be24771eca9c98096b43c_MD5.webp]]

- [Tor Erik Kvisle](https://hub.cognite.com/members/tor-erik-kvisle-1685)
- Practitioner

- [Author](https://hub.cognite.com/best-practices-internal-461/#Author)
- [Overview](https://hub.cognite.com/best-practices-internal-461/#Overview)
	- [Objective](https://hub.cognite.com/best-practices-internal-461/#Objective)
	- [Definitions](https://hub.cognite.com/best-practices-internal-461/#Definitions)
		- [Definition of Artifact](https://hub.cognite.com/best-practices-internal-461/#Definition+of+Artifact)
		- [Definition of Concept](https://hub.cognite.com/best-practices-internal-461/#Definition+of+Concept%C2%A0)
		- [Typical concepts to include in names](https://hub.cognite.com/best-practices-internal-461/#Typical+concepts+to+include+in+names)
		- [Location](https://hub.cognite.com/best-practices-internal-461/#Location)
		- [Data model type abbreviations](https://hub.cognite.com/best-practices-internal-461/#Data+model+type+abbreviations)
- [Scope](https://hub.cognite.com/best-practices-internal-461/#Scope)
	- [Artifacts in Scope](https://hub.cognite.com/best-practices-internal-461/#Artifacts+in+Scope)
		- [Data modeling resource types](https://hub.cognite.com/best-practices-internal-461/#Data+modeling+resource+types)
		- [Other resource types](https://hub.cognite.com/best-practices-internal-461/#Other+resource+types)
		- [CDF Building blocks](https://hub.cognite.com/best-practices-internal-461/#CDF+Building+blocks)
	- [Artifacts not in Scope](https://hub.cognite.com/best-practices-internal-461/#Artifacts+not+in+Scope)
- [Anatomy of a name or identifier](https://hub.cognite.com/best-practices-internal-461/#Anatomy+of+a+name+or+identifier)
- [General naming principles](https://hub.cognite.com/best-practices-internal-461/#General+naming+principles)
- [Capitalization practices](https://hub.cognite.com/best-practices-internal-461/#Capitalization+practices)
	- [Practices for building blocks](https://hub.cognite.com/best-practices-internal-461/#Practices+for+building+blocks)
	- [Practices for data modeling resource types](https://hub.cognite.com/best-practices-internal-461/#Practices+for+data+modeling+resource+types)
	- [Practices for other resource types](https://hub.cognite.com/best-practices-internal-461/#Practices+for+other+resource+types)
- [Use of concepts from external standards in identifiers](https://hub.cognite.com/best-practices-internal-461/#Use+of+concepts+from+external+standards+in+identifiers)
- [Naming conventions](https://hub.cognite.com/best-practices-internal-461/#Naming+conventions)
	- [Data modeling resource types](https://hub.cognite.com/best-practices-internal-461/#Data+modeling+resource+types)
		- [Data models](https://hub.cognite.com/best-practices-internal-461/#Data+models)
		- [Spaces](https://hub.cognite.com/best-practices-internal-461/#Spaces)
			- [Data model spaces](https://hub.cognite.com/best-practices-internal-461/#Data+model+spaces)
			- [Data model instance spaces](https://hub.cognite.com/best-practices-internal-461/#Data+model+instance+spaces)
		- [Data model view|container|edge](https://hub.cognite.com/best-practices-internal-461/#Data+model+view%7Ccontainer%7Cedge)
		- [Data model instance](https://hub.cognite.com/best-practices-internal-461/#Data+model+instance)
		- [Data model properties](https://hub.cognite.com/best-practices-internal-461/#Data+model+properties)
	- [Other resource types](https://hub.cognite.com/best-practices-internal-461/#Other+resource+types)
		- [Time series](https://hub.cognite.com/best-practices-internal-461/#Time+series)
		- [Streams](https://hub.cognite.com/best-practices-internal-461/#Streams)
		- [Records](https://hub.cognite.com/best-practices-internal-461/#Records)
		- [3D model](https://hub.cognite.com/best-practices-internal-461/#3D+model)
		- [3D model revision](https://hub.cognite.com/best-practices-internal-461/#3D+model+revision)
		- [3D scene](https://hub.cognite.com/best-practices-internal-461/#3D+scene)
		- [Location filter](https://hub.cognite.com/best-practices-internal-461/#Location+filter)
		- [Files](https://hub.cognite.com/best-practices-internal-461/#Files)
	- [CDF Building blocks](https://hub.cognite.com/best-practices-internal-461/#CDF+Building+blocks)
		- [Building block guidelines](https://hub.cognite.com/best-practices-internal-461/#Building+block+guidelines)
		- [Building block type prefixes](https://hub.cognite.com/best-practices-internal-461/#Building+block+type+prefixes)
		- [Naming conventions](https://hub.cognite.com/best-practices-internal-461/#Naming+conventions)
			- [CDF project](https://hub.cognite.com/best-practices-internal-461/#CDF+project)
			- [CDF organization](https://hub.cognite.com/best-practices-internal-461/#CDF+organization)
			- [Extraction pipelines](https://hub.cognite.com/best-practices-internal-461/#Extraction+pipelines)
			- [Hosted extractor](https://hub.cognite.com/best-practices-internal-461/#Hosted+extractor)
			- [Data workflow](https://hub.cognite.com/best-practices-internal-461/#Data+workflow)
			- [Raw databases](https://hub.cognite.com/best-practices-internal-461/#Raw+databases)
			- [Transformations](https://hub.cognite.com/best-practices-internal-461/#Transformations)
			- [Functions](https://hub.cognite.com/best-practices-internal-461/#Functions)
			- [Datasets](https://hub.cognite.com/best-practices-internal-461/#Datasets)
			- [IDP Access groups](https://hub.cognite.com/best-practices-internal-461/#IDP+Access+groups)
			- [CDF Access groups](https://hub.cognite.com/best-practices-internal-461/#CDF+Access+groups)
			- [IDP App registration/Service principal](https://hub.cognite.com/best-practices-internal-461/#IDP+App+registration/Service+principal)
			- [Chart](https://hub.cognite.com/best-practices-internal-461/#Chart)
			- [Canvas](https://hub.cognite.com/best-practices-internal-461/#Canvas)
			- [Streamlit](https://hub.cognite.com/best-practices-internal-461/#Streamlit%C2%A0)

---

## Author

Tor Erik Kvisle

---

## Overview

This document highlights important principles, considerations and practices for naming in the context of Cognite Data Fusion and adjacent IT Platforms.

## Objective

The objective of this document is to provide best practices and guidelines for naming in the context of Cognite Data Fusion (CDF) artifacts. The motivation for providing a common practice is:

- Maintainability - Known patterns across projects and accounts
- Scalability outside of single facilities - The guidelines include ways to make sure names are explicit and unique across facilities and other organizational units
- Efficiency - by reducing rework, duplication, and ambiguity
- Promoting use of common tooling - By using consistent naming across multiple engagements, it will be easier to create reusable tooling.

## Definitions

In this context, by name we include:

- Name - A human-understandable naming of an artifact
- Identifier - a unique identifier of an artifact, eg an external id
- File names

### Definition of Artifact

A generic term for either a CDF resource (type) or a CDF building block/function/capability.

### Definition of Concept

A generic term for something that can be used to categorize or group artifacts

### Typical concepts to include in names

- Source system
- Enterprise(Company)
- Location
- Industry segment
- Industry functions and processes, eg Maintenance management
- Resource categorization, eg Asset, Material
- Data model categorization, eg src,dom,sol
- Code or name for the entity within its context
- Access role/persona

### Location

  
A combination of one or more tokens that identifies the scope for a scope. It can be a physical location, or an organizational one.

Typical elements in a location:

- Region or country
- Site - the production facility (Manufacturing terminology)
- Asset - the production facility (Oil and gas terminology)
- Area - one particular area of a facility
- Unit/Zone/Line

### Data model type abbreviations

- src - source data model
- dm - Data model
- dom - Domain object model
- inst - instances

---

## Scope

## Artifacts in Scope

### Data modeling resource types

- Data models
- Data model spaces
- Data model instance spaces
- Data model views
- Data model containers
- Data model instances
- Data model edges
- Properties (view, container,edge)

### Other resource types

- Time series
- Streams
- Records
- 3D models and revisions
- 3D Scenes
- Locations
- Files

### CDF Building blocks

- CDF Project
- CDF Organization
- Extraction pipeline
- Hosted extractors
- Data workflows
- Raw databases
- Transformations
- Functions
- Data sets
- Entity matching pipeline
- Access groups in the IDP
- Access groups in Cognite
- App registrations/Service principals in the IDP
- Charts
- Canvases
- Streamlit applications
- Atlas AI Agents

## Artifacts not in Scope

- Data types from CDF Asset centric data model
- Directory structure of code repositories (like toolkit projects)
- Module names of Toolkit projects

---

## Anatomy of a name or identifier

A name can consist of:

- 0:1 Resource type, e.g. Data model space or
- 0:1 Artifact type, e.g. Transformation and
- *1:n Concepts*
- 1:n Separators

Structure:

<artifact type abbreviation>

<Separator>

<Concept 1>

<Separator>

<Concept n>

---

## General naming principles

| **Statement** |
| --- |
| Create a consistent register for possible values of the concepts that are used in naming of the concepts and artifacts. It can be an Excel sheet. Share it with the client and ask for approval if you see fit (e.g. to consider options for future scaling) |
| Include the necessary concepts in an identifier to make it unique |
| Balance between uniqueness and readability for human-readable names |
| Across artifacts, keep the same order of the concepts in the naming, unless there is a valid reason not to |
| Use abbreviations when needed  - When the resulting full name will be very long - When describing the type/kind of artifact - When the concept has commonly known and used abbreviations  Include the explanation of these abbreviations in the above mentioned register |
| When working with CDF building blocks, prefix the identifier with the type/kind of artifact |
| When working with instances of CDF resources, do not use a prefix for the type/kind of the entity |
| When not explicitly stated otherwise, standardize on using English in naming |
| A name should use singular forms instead of plural. |
| A name should use the present tense |
| A name should only use essential words. |
| Attributes with the same semantics across different objects should have the same identifier and name |
| There should not be any names or identifiers that only differ by their capitalization |
| Utilize the concepts in your spaces that balances cdf good performance practices, customer data segregation and access requirements and general good data organization practices performant and within the existing limits. |
| If the location/site/facility concept is in use for an artifact, apply “all” or something similar when this particular artifact is not scoped to an artifact. Eg with reference data like equipment types, locations, sites, etc |

  

---

## Capitalization practices

## Practices for building blocks

| **Building block** | **External id** | **Name** |
| --- | --- | --- |
| CDF Project | NA | kebab-case |
| CDF Organization | NA | kebab-case |
| All | snake\_case | Follow the general naming principles for name |

## Practices for data modeling resource types

| Resources type | External id | Name |
| --- | --- | --- |
| Data model  Data model view  Data model container  Data model edges | PascalCase | Follow general naming principles for name |
| Data model space  Data model instance space | snake\_case | Follow general naming principles for name |
| Data model instances | snake\_case | NA |
| Data model properties | NA | camelCase |

## Practices for other resource types

| Resources type | External id | Name |
| --- | --- | --- |
| TimeSeries  Records | snake\_case | Follow general naming principles for name |
| 3D model | NA | PascalCase |
| 3D model revision | NA | NA |
| Scene | snake\_case | Follow general naming principles for name |
| Location filter | snake\_case | Follow general naming principles for name |
| File | NA (Use source defined id) | NA (Use source defined name) |

---

## Use of concepts from external standards in identifiers

| **Statement** |
| --- |
| If one of the below concepts are used in identifiers, use codes mentioned in the standards.  CFIHOS:  - Disciplines - Document type  ISO 3166:  - Country codes  ISO 639-1 and ISO 639-2:  - Language codes |
| Within manufacturing (including downstream oil&gas), use levels from the ISA-95 equipment hierarchy model to organize equipment. |

---

## Naming conventions

## Data modeling resource types

### Data models

  
Data model names can have up to 255 characters and follow the general naming convention. Data model description can have up to 1024 characters. The description shall be in US English language.  

| **External id** | {Apply general naming and capitalization practices}\_src\|dom\|sol |
| --- | --- | --- | --- |
| **Name** | {Apply general naming and capitalization practices} SRC\|DOM\|SOL |

**Example**

| **External id** | Process\_Industry\_Data\_Model\_DOM |
| --- | --- |
| **Name** | Process industry data model DOM |

### Spaces

  
Space names can have up to 255 characters and follow the general naming convention. Space description can have up to 1024 characters. The description shall be in US English language.

#### Data model spaces

| **Identifier** | dm\_{src\|dom\|sol}\_{Apply general [naming](https://docs.google.com/document/d/1WIRnlcbcTx_ydxm0lbzHz60GjKRfa53FfcYDmezlR3s/edit?tab=t.0#heading=h.6wdncj6lvgnh) and [capitalization practices](https://docs.google.com/document/d/1WIRnlcbcTx_ydxm0lbzHz60GjKRfa53FfcYDmezlR3s/edit?tab=t.0#heading=h.1cat5bbzlts) } |
| --- | --- | --- | --- |
| **Name** | {Apply general [naming](https://docs.google.com/document/d/1WIRnlcbcTx_ydxm0lbzHz60GjKRfa53FfcYDmezlR3s/edit?tab=t.0#heading=h.6wdncj6lvgnh) and [capitalization practices](https://docs.google.com/document/d/1WIRnlcbcTx_ydxm0lbzHz60GjKRfa53FfcYDmezlR3s/edit?tab=t.0#heading=h.1cat5bbzlts) } DM - SRC\|DOM\|SOL |

**Example**

| **Identifier** | dm\_dom\_maintenance\_management |
| --- | --- |
| **Name** | Maintenance management - DM - DOM |

#### Data model instance spaces

Utilize the right number of concepts in your spaces, to be able to enforce the required access management, but still keep the solution performant and within the existing limits. Understand customer access requirements.

| **External id** | inst\_{Apply general naming and capitalization practices} |
| --- | --- |
| **Name** | {Apply general naming and capitalization practices} - INST |

**Example**

| **External id** | inst\_yggdrasil |
| --- | --- |
| **Name** | Yggradsil instance space - INST |

### Data model view|container|edge

| **External id** | {Apply general naming and capitalization practices} |
| --- | --- |
| **Name** | {Apply general naming and capitalization practices} |

  
**Example**  

| **External id** | FailureCause |
| --- | --- |
| **Name** | Failure cause container |

### Data model instance

| **External id** | {Apply general naming and capitalization practices} |
| --- | --- |
| **Name** | NA |

### Data model properties

| **Identifier** | {Apply general naming and capitalization practices} |
| --- | --- |
| **Description** | Follow practices for AI friendly descriptions |

  
**Example**  

| **External id** | errorCode |
| --- | --- |
| **Description** | Follow practices for AI friendly descriptions |

## Other resource types

### Time series

Typically a time series originates from a source system that has its own identifier. If possible, consider adding prefixes to ensure uniqueness. Like:

- Originating system (Eg pi, sap,sharepoint)
- Production site/asset

| **External id** | {Apply general naming and capitalization practices} |
| --- | --- |
| **Name** | NA |

### Streams

Use concepts to add uniqueness

- Originating system (Eg pi, sap,sharepoint)
- Production site/asset

| **External id** | {Apply general naming and capitalization practices} |
| --- | --- |
| **Name** | NA |

### Records

Typically a record originates from a source system that has its own identifier. If possible, consider adding prefixes to ensure uniqueness. Like:

- Originating system (Eg mes, scada)
- Production site/asset
- Machine number

| **External id** | {Apply general naming and capitalization practices} |
| --- | --- |
| **Name** | NA |

### 3D model

| **External id** | NA |
| --- | --- |
| **Name** | {Apply general naming and capitalization practices} |

### 3D model revision

You cannot influence neither the external id nor the name of a 3d model revision.  

| **External id** | NA |
| --- | --- |
| **Name** | NA |

### 3D scene

| **External id** | NA |
| --- | --- |
| **Name** | {Apply general naming and capitalization practices} |

### Location filter

| **External id** | loc\_{location} |
| --- | --- |
| **Name** | {Apply general naming and capitalization practices} |

### Files

Typically a file originates from a source system that has its own identifier. These systems ensure uniqueness within their universe. Adding prefixes is usually not needed

| **External id** | NA |
| --- | --- |
| **Name** | {Apply general naming and capitalization practices} |

## CDF Building blocks

### Building block guidelines

| **Guideline** | **Rationale** |
| --- | --- |
| For identifiers - use a prefix to indicate the type of building block |  |
| For names - do not use a prefix to indicate the name of building block |  |
| When constructing an external id, compose it by applying the necessary concepts to make it unique, instead of generating a random id |  |

### Building block type prefixes

Below are only suggestions. If these are clashing with existing acronyms at the customer, define ones that have no ambiguity.  

| **Building block** | **Prefix** |
| --- | --- |
| CDF Project | None |
| CDF Organization | None |
| Extraction configurations | ec |
| Extraction pipeline | ep |
| Raw databases | raw (optional) |
| Transformations | tr |
| Data workflows | wf |
| Functions | fn |
| Data sets | ds |
| Location filters | loc |
| Access groups in the IDP | gp\_cdf |
| Access groups in Cognite | gp |
| App registrations/Service principals in the IDP | app |
| Charts | None |
| Canvases | None |

  

### Naming conventions

Where name isn’t mentioned, it is either not applicable or general naming and capitalization practices should be applied

#### CDF project

If only 1 project initially in use, consider skipping the suffix. This project can then turn into production. Use of segment and or region and or country is optional, and relies on the

| **Name** | {enterprise}-{segment}-{region}-{country}-{project level} |
| --- | --- |

  
**Example**  

| **Name** | am-long-europe-dev |
| --- | --- |

#### CDF organization

| **Name** | {enterprise} |
| --- | --- |

#### Extraction pipelines

Apply the following concepts to your naming:

- Prefix (ep)
- Data type
- Source location/site/facility/asset - Can be multiple tokens
- Source system

| **External id** | ep\_{data type}\_{location}\_{source system} |
| --- | --- |
| **Name** | {Apply building block guidelines and capitalization practices} |

  
**Example**  

#### Hosted extractor

Within hosted extractors, the following sub components exists:

- Source - the connection to the source
- Job
- Destination
- Mapping

Apply the following concepts to your hosted extractor:

- Prefix (he)
- Data type (Optional, since one system can deliver multiple data types
- Source location/site/facility/asset - Can be multiple tokens
- Messaging provider (Optional, eg kafka)
- Source system
- Suffix source|job|destination|mapping

| **External id** | he\_{data type}\_{location}\_{messaging provider}\_{source system} |
| --- | --- |

  
**Example**  

| **External id** | he\_timeseries\_valhall\_kafka\_pi\_source |
| --- | --- |

#### Data workflow

A data workflow can orchestrate one or more cdf building blocks. It will normally have a specific task, such as:

- Creating an asset hierarchy from a set of sources
- Do calculations and enrichment of data

If the workflow has a source and target, include this in the name. If it doesn’t state its intent in the name

Apply the following concepts to your workflow:

- Prefix (wf)
- Source location/site/facility/asset - Can be multiple tokens. If not scoped to a location, add “all”
- Source data structure (Optional)
- Action/Intent(Optional)
- Target data structure (Optional)

| **External id** | wf\_{location}\_{intent}\_{source data structure}\_{target data structure} |
| --- | --- |

  
**Example**  

| **External id** | wf\_all\_sap\_assets\_to\_cdm  wf\_valhall\_calulate\_asset\_downtime |
| --- | --- |

#### Raw databases

| **DB Name** | {data type}\_{location}\_{source system} |
| --- | --- |
| **Table name** | From source, or follow general naming guidelines |

#### Transformations

| **External Id** | tr\_{source data structure}\_{location}\_to\_{target data structure} |
| --- | --- |

#### Functions

Apply the following concepts to your workflow:

- Prefix (fn)
- Source location/site/facility/asset - Can be multiple tokens. If not scoped to a location, add “all”
- Source data structure (Optional)
- Action/Intent(Optional)
- Target data structure (Optional)

| **External Id** | fn\_{source data structure}\_{location}\_{intent}\_{target data structure} |
| --- | --- |

#### Datasets

| **External Id** | ds\_{data type}\_{location} |
| --- | --- |

#### IDP Access groups

Often the client has requirements on the naming of these groups. If so, adhere to these. If not, below is a good practice.

Possible patterns:

- Persona based scheme. You create groups for the expected personas that will interact with the system. Will result in groups with a lot of overlapping capabilities, but is easy to understand and extend
- ACL based scheme. You create groups with few or more capabilities, reflecting the actions members of a group can do. Eg timeseries read. Will either result in many fine grained groups, very coarse grained groups, or groups where it’s hard to find a good name that describes the collection of actions it allows you to do. Consider this scheme when doing land/quickstart, but be prepared to change the pattern when expanding
- Data domain based scheme. You create groups with access to data within one data domain (eg Quality assurance, Maintenance management)
- Source system based scheme. You create groups with access to data from one source system, either within or across data tiers (source, domain, solution)

Often a combination of some of the above schemes will be the right approach  

| **External Id** | gp\_cdf\_{location}\_{persona\|resource type\|data domain}\_{action}\_{project level} |
| --- | --- | --- | --- |

  
**Example**  

| **External Id** | gp\_valhall\_timeseries\_read  gp\_valhall\_production\_read  gp\_valhall\_process\_engineer  gp\_valhall\_pi\_write |
| --- | --- |

#### CDF Access groups

Let the groups in CDF reflect the groups in the IDP

| **External Id** | gp\_{convention for CDF access group} |
| --- | --- |

#### IDP App registration/Service principal

| **External Id** | app\_{location}\_{persona}\_{source system}\_{data type}\_{action}\_{project level} |
| --- | --- |

  
**Example**  

| **External Id** | app\_valhall\_extractor\_pi\_timeseries\_write\_dev |
| --- | --- |

#### Chart

| **External Id** | NA |
| --- | --- |
| **Name** | Follow general naming and capitalization guidelines |

#### Canvas

| **External Id** | NA |
| --- | --- |
| **Name** | Follow general naming and capitalization guidelines |

#### Streamlit

| **External Id** | NA |
| --- | --- |
| **Name** | Follow general naming and capitalization guidelines |

×

### Cookie settings

  

We use 3 different kinds of cookies. You can choose which cookies you want to accept. We need basic cookies to make this site work, therefore these are the minimum you can select. [Learn more about our cookies.](https://hub.cognite.com/site/terms)

<!-- SOURCE_END: 関連リンク\Best Practices - Naming Conventions  Cognite Hub.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 関連リンク\Best Practices - Quickstart  Cognite Hub.md -->
## File: 関連リンク\Best Practices - Quickstart  Cognite Hub.md

---
title: "Best Practices - Quickstart | Cognite Hub"
source: "https://hub.cognite.com/best-practices-internal-461/best-practices-quickstart-5733"
created: 2025-12-28
description: "Author	Overview	Common Extractors in a Quick Start Project		References	Best Practices	1. Reusable & Maintainable Components		2. Follow Best Practice..."
tags:
  - "CDF"
  - "CogniteDataFusion"
image: "https://uploads-eu-west-1.insided.com/cognite-en/attachment/1440x640/975eef82-f4f3-41fa-8d9e-50125b518deb_thumb.jpg"
---
![[489142252f9585d269d9c5a1a05507f0_MD5.webp]]

- [Marie Lepoutre](https://hub.cognite.com/members/marie-lepoutre-5535)
- Practitioner

---

## Author

Marie Solvik Lepoutre

---

## Overview

This guide outlines best practices for delivering a **Quick Start** project in Cognite Data Fusion (CDF). The guidance focuses on establishing a solid, scalable foundation during the project's condensed timeline.  

The following core tools and services are commonly used in a Quick Start project:  

| **Service** | **Description** |
| --- | --- |
| [**Data Workflows**](https://docs.cognite.com/cdf/data_workflows/) | DAG-based orchestration of internal and external tasks. |
| [**Functions**](https://docs.cognite.com/cdf/functions/) | Compute solution for executing custom Python logic and interacting with APIs. |
| [**Transformations**](https://docs.cognite.com/cdf/integration/guides/transformation/transformations/) | SQL or mapping-based data transformations within CDF. |
| [**Staging/Raw**](https://docs.cognite.com/cdf/integration/guides/extraction/raw_explorer/) | Key-value storage useful for checkpointing and exchanging data between tasks. |
| [**Data Modeling Service**](https://docs.cognite.com/cdf/dm/) | A Property Graph database that can serve similar purposes as Staging/Raw. |
| [**Extraction Pipelines**](https://docs.cognite.com/cdf/integration/guides/interfaces/about_integrations/) | Observability and monitoring solution for extractors and data pipelines. |
| [**Toolkit**](https://docs.cognite.com/cdf/deploy/cdf_toolkit/) | Tooling to manage, configure, and deploy resources in CDF. |
| [**Entity Matching**](https://docs.cognite.com/cdf/integration/guides/contextualization/matching) | Contextualization tools that match entities from various source systems to the same entity in the CDF data model. |
| [**Streamlit**](https://docs.cognite.com/cdf/streamlit/) | Integrated tool to create data visualization tools, dashboards, or prototypes. |
| [**Canvas**](https://docs.cognite.com/cdf/explore/canvas/) | Workspace to analyze and investigate data and collaborate with co-workers. |
| [**Search**](https://docs.cognite.com/cdf/explore/search/) | Find industrial data. |
| [**Charts**](https://docs.cognite.com/cdf/charts/) | Analyze time series data. |
| [**Atlas AI**](https://docs.cognite.com/cdf/atlas_ai/) | Workspace to create and manage industrial AI agents. |

---

### Common Extractors in a Quick Start Project

The following extractors are frequently used to rapidly ingest data during a Quick Start:  

| **Extractor** | **Description** |
| --- | --- |
| [**Cognite DB Extractor**](https://docs.cognite.com/cdf/integration/guides/extraction/db) | Connects to databases using ODBC, runs queries, and batch extracts data into the CDF staging area or directly to the time series service. (Ensure you have ODBC drivers installed). Available as both a Windows service and a standalone executable. |
| [**Cognite OPC UA Extractor**](https://docs.cognite.com/cdf/integration/guides/extraction/opc_ua) | Connects to the open **OPC UA** protocol and streams time series and events into CDF. Batch extracts the node hierarchy into the CDF staging area or as CDF assets and relationships. |
| [**Cognite PI Extractor**](https://docs.cognite.com/cdf/integration/guides/extraction/pi) | Connects to the **PI Data Archive** and streams time series into the CDF time series service, including historical data (backfilling). |
| [**Cognite PI AF Extractor**](https://docs.cognite.com/cdf/integration/guides/extraction/pi_af) | Connects to the **PI Asset Framework** and batch extracts elements and their attributes, building an asset tree in the CDF staging area. |
| [**Cognite File Extractor**](https://docs.cognite.com/cdf/integration/guides/extraction/file) | Uploads files into the CDF files service from sources like: Local file systems, Online file storage (e.g., SharePoint Online, Azure Blob Storage), or Network sharing protocols (e.g., FTP, FTPS, and SFTP). |
| [**Cognite SAP Extractor**](https://docs.cognite.com/cdf/integration/guides/extraction/sap) | Connects to **SAP** through OData endpoints and batch extracts data into the CDF staging area. |

---

## References

The [Technical Project Delivery Framework](https://cognitedata.atlassian.net/wiki/spaces/ODM/pages/4340645891/Technical+Project+Delivery+Framework+for+Data+Fusion+Quick+Start) for Quick Start provides a detailed overview of technical activities and tasks for Quick Start projects.

---

## Best Practices

### 1\. Reusable & Maintainable Components

Design your solution with modularity and future scale to multiple sites in mind, even for a single-site Quick Start.  

**CI/CD Foundation**

- Use **Toolkit** to manage CDF resources for the project and create a private Cognite GitHub repository for it.
- Set up **CI/CD pipelines** following [this guide](https://docs.cognite.com/cdf/deploy/cdf_toolkit/guides/cicd/github_setup).
- Set up [pre-commit](https://github.com/cognitedata/gss-knowledge-base/blob/main/.pre-commit-config.yaml) and [rules for cursor](https://github.com/cognitedata/gss-knowledge-base/tree/main/.cursor/rules/project) to maintain code quality.

**Component Reuse**

- Use available [deployment packs](https://github.com/cognitedata/library/tree/main) when applicable to accelerate development.
- Avoid "re-inventing the wheel." Look for examples and components in the [Knowledge Base repository](https://github.com/cognitedata/gss-knowledge-base/tree/main/) and the [quickstart-module-foundation](https://github.com/cognitedata/quickstart-module-foundation).

### 2\. Follow Best Practice Guides

Quick Start projects benefit significantly from adhering to established standards.  

**Core Guides**

- Consult and follow the specific best practice guides for:
	- [**Data Pipelines**](https://hub.cognite.com/best-practices-internal-461/best-practices-data-pipelines-5298)
	- [**Data Modeling**](https://hub.cognite.com/best-practices-internal-461/best-practices-data-modelling-5729)
	- [**Naming Conventions**](https://hub.cognite.com/best-practices-internal-461/best-practices-naming-conventions-5734)
	- [**2D Contextualization**](https://hub.cognite.com/best-practices-internal-461/best-practices-2d-contextualization-5732)
	- [**Atlas AI for Quick Start**](https://hub.cognite.com/best-practices-internal-461/best-practices-atlas-ai-for-quickstart-5731)

### 3\. Basic Tools & Demos

Ensure the customer can immediately interact with their data using core CDF tools. Always configure and demonstrate the basic industrial tools with the customer’s data:

- **Search**
- **Canvas**
- **Charts**
- **Infield** (Note: Delivery of Infield is not for all Quick Start projects.)

### 4\. Project Acceleration

Leverage specialized Cognite team to ensure rapid and high-quality delivery.

  
**Involve the MAIA Accelerator Team**

- The **MAIA Accelerator team** is dedicated to supporting Quick Start projects.
- Request help through the [ticketing system](https://cognitedata.atlassian.net/servicedesk/customer/portal/111) for specialized tasks such as:
	- Data dump ingestion
	- Contextualization of time series and files
	- Extraction Pipeline setup
	- Project environment setup
- More details on their scope and how to engage can be found in the relevant [internal documentation](https://cognitedata.atlassian.net/wiki/spaces/GCCG/pages/5054562307/Maia+Accelerator+Tasks).

---

## Checklist: Quick Start Project Readiness

| **Category** | **Checklist Item** | **✅** |
| --- | --- | --- |
| **Setup & CI/CD** | Create a GitHub repository in the Cognite Organization for the project. |  |
|  | Use Toolkit to manage CDF resources and set up CI/CD pipelines following the provided guide. |  |
| **Extractors** | Set up extractors according to their documentation (referenced above). |  |
|  | Allocate one VM per extractor per project. |  |
|  | Look for existing configuration examples (e.g., in the Knowledge Base). |  |
| **Data Pipelines** | Design and implement data pipelines following the best practice [guide](https://hub.cognite.com/best-practices-internal-461/best-practices-data-pipelines-5298), ensuring they are built for future scaling to multiple sites. |  |
| **Data Modeling** | Follow the best practices [guide](https://hub.cognite.com/best-practices-internal-461/best-practices-data-modelling-5729) for Data Modeling. |  |
|  | Determine if a source Data Model layer is needed for the project size; otherwise, focus on the domain and solution model. |  |
| **Contextualization** | Follow [best practices](https://hub.cognite.com/best-practices-internal-461/best-practices-2d-contextualization-5732) for Contextualization and reuse code/components where possible. |  |
| **Naming Convention** | Establish naming conventions following the best practice [guide](https://hub.cognite.com/best-practices-internal-461/best-practices-naming-conventions-5734), ensuring the structure can easily accommodate future scaling across multiple sites. |  |
| **Reuse code and components** | Use available [deployment packs](https://github.com/cognitedata/library/tree/main) when applicable. |  |
|  | Look for examples in the [Knowledge Base repository](https://github.com/cognitedata/gss-knowledge-base/tree/main/) and the [quickstart-module-foundation](https://github.com/cognitedata/quickstart-module-foundation) |  |
| **Demos & Tools** | Be sure to demo and set up the following industrial tools with customer data:  - Charts - Search - Canvas - (Infield) |  |
| **Atlas AI** | Follow the best practice [guide](https://docs.google.com/document/d/1I7Ytp06Z4pAwijiyr899z6Ei8-Wm9Hosj9HSx-_wFR4/edit?tab=t.0) for Atlas AI for Quick Start. |  |
| **Project Support** | Involve the **MAIA Accelerator team** for help with technical tasks. |  |

  

×

### Cookie settings

  

We use 3 different kinds of cookies. You can choose which cookies you want to accept. We need basic cookies to make this site work, therefore these are the minimum you can select. [Learn more about our cookies.](https://hub.cognite.com/site/terms)

<!-- SOURCE_END: 関連リンク\Best Practices - Quickstart  Cognite Hub.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 関連リンク\Index - NEAT.md -->
## File: 関連リンク\Index - NEAT.md

---
title: "Index - NEAT"
source: "https://cognite-neat.readthedocs-hosted.com/en/latest/validation/index.html"
created: 2025-12-28
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
[Skip to content](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/#ai_readiness-neat-dms-ai-readiness)

## Index

**Neat supports 31 validation rules** for data modeling. These rules are learned from best practice, knowledge of the Cognite Data Fusion data modeling service, and practical experience from helping customers build and maintain their data models.

### Ai\_Readiness (NEAT-DMS-AI-READINESS)

Validators for checking if data model is AI-ready.

| code | name | message |
| --- | --- | --- |
| NEAT-DMS-AI-READINESS-001 | [DataModelMissingName](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-ai-readiness-001.html) | Validates that data model has a human-readable name. |
| NEAT-DMS-AI-READINESS-002 | [DataModelMissingDescription](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-ai-readiness-002.html) | Validates that data model has a human-readable description. |
| NEAT-DMS-AI-READINESS-003 | [ViewMissingName](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-ai-readiness-003.html) | Validates that a View has a human-readable name. |
| NEAT-DMS-AI-READINESS-004 | [ViewMissingDescription](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-ai-readiness-004.html) | Validates that a View has a human-readable description. |
| NEAT-DMS-AI-READINESS-005 | [ViewPropertyMissingName](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-ai-readiness-005.html) | Validates that a view property has a human-readable name. |
| NEAT-DMS-AI-READINESS-006 | [ViewPropertyMissingDescription](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-ai-readiness-006.html) | Validates that a View property has a human-readable description. |
| NEAT-DMS-AI-READINESS-007 | [EnumerationMissingName](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-ai-readiness-007.html) | Validates that an enumeration has a human-readable name. |
| NEAT-DMS-AI-READINESS-008 | [EnumerationMissingDescription](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-ai-readiness-008.html) | Validates that an enumeration value has a human-readable description. |

### Connections (NEAT-DMS-CONNECTIONS)

Validators for connections in data model specifications.

| code | name | message |
| --- | --- | --- |
| NEAT-DMS-CONNECTIONS-001 | [ConnectionValueTypeUnexisting](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-connections-001.html) | Validates that connection value types exist. |
| NEAT-DMS-CONNECTIONS-002 | [ConnectionValueTypeUndefined](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-connections-002.html) | Validates that connection value types are not None, i.e. undefined. |
| NEAT-DMS-CONNECTIONS-REVERSE-001 | [ReverseConnectionSourceViewMissing](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-connections-reverse-001.html) | Validates that source view referenced in reverse connection exist. |
| NEAT-DMS-CONNECTIONS-REVERSE-002 | [ReverseConnectionSourcePropertyMissing](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-connections-reverse-002.html) | Validates that source property referenced in reverse connections exist. |
| NEAT-DMS-CONNECTIONS-REVERSE-003 | [ReverseConnectionSourcePropertyWrongType](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-connections-reverse-003.html) | Validates that source property for the reverse connections is a direct relation. |
| NEAT-DMS-CONNECTIONS-REVERSE-004 | [ReverseConnectionContainerMissing](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-connections-reverse-004.html) | Validates that the container referenced by the reverse connection source properties exist. |
| NEAT-DMS-CONNECTIONS-REVERSE-005 | [ReverseConnectionContainerPropertyMissing](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-connections-reverse-005.html) | Validates that container property referenced by the reverse connections exists. |
| NEAT-DMS-CONNECTIONS-REVERSE-006 | [ReverseConnectionContainerPropertyWrongType](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-connections-reverse-006.html) | Validates that the container property used in reverse connection is the direct relations. |
| NEAT-DMS-CONNECTIONS-REVERSE-007 | [ReverseConnectionTargetMissing](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-connections-reverse-007.html) | Validates that the direct connection in reverse connection pair have target views specified. |
| NEAT-DMS-CONNECTIONS-REVERSE-008 | [ReverseConnectionPointsToAncestor](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-connections-reverse-008.html) | Validates that direct connections point to specific views rather than ancestors. |
| NEAT-DMS-CONNECTIONS-REVERSE-009 | [ReverseConnectionTargetMismatch](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-connections-reverse-009.html) | Validates that direct connections point to the correct target views. |

### Consistency (NEAT-DMS-CONSISTENCY)

Validators checking for consistency issues in data model.

| code | name | message |
| --- | --- | --- |
| NEAT-DMS-CONSISTENCY-001 | [ViewSpaceVersionInconsistentWithDataModel](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-consistency-001.html) | Validates that views have consistent space and version with the data model. |

### Containers (NEAT-DMS-CONTAINER)

Validators for checking containers in the data model.

| code | name | message |
| --- | --- | --- |
| NEAT-DMS-CONTAINER-001 | [ExternalContainerDoesNotExist](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-container-001.html) | Validates that any container referenced by a view property, when the |
| NEAT-DMS-CONTAINER-002 | [ExternalContainerPropertyDoesNotExist](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-container-002.html) | Validates that any container property referenced by a view property, when the |
| NEAT-DMS-CONTAINER-003 | [RequiredContainerDoesNotExist](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-container-003.html) | Validates that any container required by another container exists in the data model. |

### Limits (NEAT-DMS-LIMITS)

Validators for checking if defined data model is within CDF DMS schema limits.

| code | name | message |
| --- | --- | --- |
| NEAT-DMS-LIMITS-CONTAINER-001 | [ContainerPropertyCountIsOutOfLimits](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-limits-container-001.html) | Validates that a container does not exceed the maximum number of properties. |
| NEAT-DMS-LIMITS-CONTAINER-002 | [ContainerPropertyListSizeIsOutOfLimits](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-limits-container-002.html) | Validates that container property list sizes do not exceed CDF limits. |
| NEAT-DMS-LIMITS-DATA-MODEL-001 | [DataModelViewCountIsOutOfLimits](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-limits-data-model-001.html) | Validates that the data model does not exceed the maximum number of views. |
| NEAT-DMS-LIMITS-VIEW-001 | [ViewPropertyCountIsOutOfLimits](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-limits-view-001.html) | Validates that a view does not exceed the maximum number of properties. |
| NEAT-DMS-LIMITS-VIEW-002 | [ViewContainerCountIsOutOfLimits](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-limits-view-002.html) | Validates that a view does not reference too many containers. |
| NEAT-DMS-LIMITS-VIEW-003 | [ViewImplementsCountIsOutOfLimits](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-limits-view-003.html) | Validates that a view does not implement too many other views. |

### Views (NEAT-DMS-VIEW)

Validators for checking containers in the data model.

| code | name | message |
| --- | --- | --- |
| NEAT-DMS-VIEW-001 | [ViewToContainerMappingNotPossible](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-view-001.html) | Validates that container and container property referenced by view property exist. |
| NEAT-DMS-VIEW-002 | [ImplementedViewNotExisting](https://cognite-neat.readthedocs-hosted.com/en/latest/validation/neat-dms-view-002.html) | Validates that implemented (inherited) view exists. |

<!-- SOURCE_END: 関連リンク\Index - NEAT.md -->

================================================================================

