# 42_Knowledge Base - All Content
Generated from: Obsidian Web Clipper (42_Knowledge Base)


## Best Practices (Internal)

### Best Practices Library\Best Practices (Internal)\Best Practices - 2D Contextualization  Cognite Hub.md

---
title: "Best Practices - 2D Contextualization | Cognite Hub"
source: "https://hub.cognite.com/best-practices-internal-461/best-practices-2d-contextualization-5732"
created: 2025-12-28
description: "Authors	Overview	References	General Pipeline Best Practices	Document Parser	Overview		References		Best Practices		Code Examples		Checklist		Diagram Pars..."
tags:
  - "CDF"
  - "CogniteDataFusion"
image: "https://uploads-eu-west-1.insided.com/cognite-en/attachment/1440x640/cc4e4e56-cfe3-43b3-a8b4-3201ebf528a3_thumb.png"
---
![[e2defece8ae6793724e0d77cf234278a_MD5.webp]]

- [Colin Nattrass](https://hub.cognite.com/members/colin-nattrass-5083)
- Practitioner

---

## Authors

  
Fredrik Larsen  
Colin Nattrass  
Christian Sletten  

---

## Overview

The primary intention of this guide is to provide an overview of tools within Cognite Data Fusion (CDF) for **contextualizing unstructured industrial data from 2D files** (such as engineering drawings and datasheets) to a structured target.

This guide outlines CDF's many **out-of-the-box contextualization tools,** including **where and when to use each** and details **best practices** to ensure optimal accuracy, scalability, and maintainability.

Note: This guide focuses on contextualization within an existing knowledge graph in the CDF [Data Modelling Service](https://docs.cognite.com/cdf/dm/). The term ‘entity’ is used throughout to refer to an existing node in the knowledge graph. An entity is commonly an ‘Asset’ or ‘Tag’.

| **Service** | **Description** |
| --- | --- |
| [Document Parser](https://docs.cognite.com/cdf/integration/guides/contextualization/document_parser/) | Extracts **structured data** (e.g., key-value pairs) from tabular, template-driven documents like datasheets and reports. |
| [Diagram Parser](https://docs.cognite.com/cdf/integration/guides/contextualization/interactive_diagrams) | Extracts relationships between **text-based identifiers on technical drawings** (e.g., [Piping and Instrumentation Diagrams](https://en.wikipedia.org/wiki/Piping_and_instrumentation_diagram) (P&ID), [Control System Diagrams](https://en.wikipedia.org/wiki/Control_system)) and entities in the knowledge graph.  This service also establishes relationships *between* drawings. |
| [Detailed Diagram Parser](https://docs.cognite.com/cdf/integration/guides/contextualization/diagram_parsing) | Extends the functionality of the classic Diagram Parser service (although is functionally a distinct service) by including **symbol detection** and mapping **internal symbol-to-symbol relationships**.  This creates a **digitalized representation** of the diagram, related to structured entities in CDF, which can be used to create a graph in CDF. |

Note that contextualisation of 3D unstructured data (such as CAD, point clouds and 360° images) is not within the scope of this guide.  

---

## References

[Hub: How-To’s: Overview of document parsing capabilities \[Cognite Official\]](https://hub.cognite.com/contextualization-402/overview-of-document-parsing-capabilities-cognite-official-4740)  
(This reference also includes a description of some other useful endpoints, such as the Document Summary and Document Question Answering services).  
  
[Data Pipelines Best Practices](https://hub.cognite.com/best-practices-internal-461/best-practices-data-pipelines-5298)  

---

## General Pipeline Best Practices

For large-scale, high-volume processing of documents, a robust pipeline is required. The [Data Pipelines Best Practices](https://docs.google.com/document/d/1Cnp3T-ddlojEKFxh31i34DXtnFTOvTY9yfsXH1-kfvs/edit?tab=t.0#heading=h.3so7mldcowaj) document is the primary source of truth for this pattern.  

At a high level, this architecture involves:

- **Asynchronous calls:** If using a Cognite Function to call a specific parsing API (e.g., Document Parser, Diagram Parser, Detailed Diagram Parser), calls to these services should be handled using an asynchronous pattern. For example, if any post-call completion steps need to occur (like writing instances to the Data Modelling Service), then these should be handled in a separate downstream processing stage. The Cognite Function should not wait for the contextualisation process to complete.
- **Batching and Scalability:** Initiating jobs in batches to improve throughput.
- **Rate Limiting:** Implementing client-side exponential backoff to manage CDF project concurrency limits and avoid API throttling (this is *generally* 50 concurrent processing jobs with the project, however refer to the specific service in questions for up to date limits).

---

## Document Parser

*Note: The Document Parser service is in the beta state and is constantly evolving. Certain Best Practices should be expected to develop alongside the product.*  

### Overview

  
The core purpose of the Document Parser is to enable the extraction of key-value pairs from technical documents such as datasheets, manuals, inspection reports and more. An example of a typical datasheet is as below:-  
  

![[14a6d860ad03f5fb5e393cb1f1571484_MD5.webp]]

The best fit for this service is when parsing unstructured data in key-value pairs.

It is not a good fit when parsing wide tabular data, especially when already in a structured digital-native form (e.g. an Excel sheet).  

---

### References

[Cogfluence: Document Parser](https://cognitedata.atlassian.net/wiki/spaces/PD/pages/4413423632/Document+Parser)

[Github: Document Parser](https://github.com/cognitedata/infrastructure/tree/master/lib/python/cogmono/docparser)

---

### Best Practices

**1\. Objective**

- **Data Instantiation and Linking:** Ingest extracted key-value pairs into a target data model instance, and link that instance to the primary entity (e.g., Asset) it describes

**2\. Key Considerations**  

| **Consideration** | **Detail** | **Notes** |
| --- | --- | --- |
| File Format | Must be a **PDF** file. |  |
| Page Count | Maximum of **100 pages** per document. | Results and performance generally improve with smaller files. |
| Content Type | Must contain data represented as **key-value pairs** (e.g., datasheets, equipment specifications). | The document should ideally describe a **single asset or piece of equipment**. |
| Language | Must contain **English text**. |  |
| Result Scope | Data from each document is ingested into a **separate data model instance**. | Batch processing is supported, but results remain isolated per document. |

**3\. Configuration**

The quality of the Data Model is your best leverage for high parsing accuracy:

- **Well-Defined Data Models:** The parser performs best when working with data models that have **clear names, descriptions, and minimal ambiguity**.
- **Leverage Property Descriptions:** Property descriptions are **injected into the LLM prompt** to provide fine-tuning on a per-property basis. Use this to specify:  
	- Details on **abbreviations**.
	- Required **formatting** for returned fields (e.g., date formats).
	- Necessary **unit conversions**.
- **Embedded text PDFs**: These are preferred over scanned images for better parsing accuracy (whether or not this is the case for the Vision-based Document Parser is as yet to be determined).
- Beneath the base, documented functionality, there are also the following configuration parameters:  
	- **useVision**: use the Cognite internally developed Vision Language Model to do the parsing (as opposed the open source [PDFPlumber](https://python.langchain.com/docs/integrations/document_loaders/pdfplumber/)).
	- **userPrompt**: users can provide their own prompt to the underlying parsing model to "bias" it (e.g., focus on certain aspects)
	- **Page range** (up to 10 pages max) so that the LLM will use multiple user-provided pages to do the parsing

**4\. Validation and Automation**  

The validation strategy should align with data criticality and desired levels of automation:

- **Human-in-the-loop (HIL) Validation:** For critical data, use the **out-of-the-box UI validation step** where a user must **approve the job** before data is stored in the Data Modelling Service (DMS).
- **Fully Automated Parsing:** If human approval isn't needed, you can utilise the \`/context/documentparser/jobs/start\` and \`/context/documentparser/jobs/write\` endpoints to trigger batches of files for processing, and write them to the Data Modelling Service without using the UI.
- **More generally, implement an appropriate QA strategy** to ascertain the quality and completeness of the data extraction.

---

### Code Examples

  
N/A  

---

### Checklist

| **Category** | **Checklist Item** | **✅** |
| --- | --- | --- |
| **Use Case & Document Prep** | Confirm the primary use case is extracting key-value pairs (not wide tabular data). |  |
|  | Confirm documents describe a single asset or piece of equipment. |  |
|  | Check file format: PDF is required. |  |
|  | Check page count: Documents are under the 100-page limit (smaller is better). |  |
|  | Check language: Documents contain English text. |  |
|  | Use embedded text PDFs (not scanned images) whenever possible for best accuracy. |  |
| **Data Model Configuration** | A target Data Model View is created to receive the parsed data. |  |
|  | The Data Model has clear, unambiguous names and descriptions to guide the LLM. |  |
|  | (Crucial) Property descriptions are highly detailed, specifying abbreviations, required formatting, and any unit conversions. |  |
| **Validation strategy** | Decide validation strategy: HIL (UI approval) for critical documents or Automated (auto-approve) for non-critical. |  |
|  | Define and implement a QA strategy to measure the quality and completeness of the data extraction |  |
| **Pipeline** | Adhere to Data Pipelines Best Practices (async calls, batching, rate limiting). |  |

## Diagram Parser

### Overview

The Diagram Parser enables the extraction of relationships between **text-based identifiers** on technical drawings, such as Piping and Instrumentation Diagrams (P&ID) and Control System Diagrams, and entities in the CDF knowledge graph.

It also supports linking **drawings to other drawings**, enabling a network of interconnected diagrams.

An example Piping and Instrumentation diagram is below, showing Tag/Entity relations in purple, and Document relations in orange.

The Diagram Parser uses a combination of Optical Character Recognition (OCR) to find candidates in the diagram to match, and a tokenisation and matching algorithm to match to a set of entities or files.

Tokenisation is the process of breaking down a string of text, like an ID or tag, into a list of its basic parts (tokens) e.g. *Tokens(23YE96133-01) = \[23, YE, 96133, 01\]*  

![[c8b25dcba61d9c14c7486743b3b36dff_MD5.webp]]

  

---

### References

[Cogfluence: Engineering Diagram Parser](https://cognitedata.atlassian.net/wiki/spaces/Contextual/pages/962101385/Engineering+diagram+parsing)

---

### Best Practices

**1\. Objective**

- **Entity and file linking:** Link detected text blocks in diagrams to their corresponding entities (Assets/Tags) and other files in the knowledge graph.

**2\. Key Considerations**

| **Consideration** | **Detail** | **Notes** |
| --- | --- | --- |
| File Format | Must be **PDF**, **JPEG**, **PNG**, or **TIFF** | PDF is most commonly used. |
| Page Structure | Supports **multi-page diagrams** |  |
| Tag Types | Detects **Assets**, **Diagrams**, and **Unlinked** tags |  |
| Contextualization Model | Offers **Standard** and **Advanced** models for matching | Offers Standard and Advanced models for matching.  Use the 'Advanced' model to configure partial matching and token rules.  This is a best practice for non-standard tag formats to improve match accuracy. |
| Approval Workflow | Tags must be **approved or rejected** manually or via automation | Human-in-the-loop validation is supported. |

The recommended approach for using the Diagram Parser is to start with and extend from the [official Deployment Pack](https://hub.cognite.com/deployment-packs-472/p-id-annotation-workflow-using-cdf-data-modeling-cognite-official-4680).  

**3\. Configuration**

- **WebUI vs API based workflow**:  
	  
	You can run the Diagram Parser in one of two ways:  
	- Directly through the CDF Web UI (which is useful for a small number of documents, but not very scalable) ([as described in the product documentation](https://docs.cognite.com/cdf/integration/guides/contextualization/interactive_diagrams/))
	- As part of an automated workflow that calls the directly, and . A reference for this pattern can be found within the two provided code examples to follow.  
		- For direct calls, a batch of up to 50 files (and page ranges) is supplied.
		- If a file is >50 pages you will need to make multiple calls for the same file.
- **Default parameters (no tuning)**: Start with the default parameters. Only adjust them if the default settings do not provide good results.
- **Tuning partialMatch** **parameter:**  
	- Keep false (Default): Use this for the highest precision. It guarantees that a match is only returned if all tokens (e.g., 12, AB, 1234) of an entity (e.g., 12-AB-1234) are found.
	- Set to true: Use this when you need to find entities even if parts of their name (like prefixes) are missing in the diagram. For example, to match the text AB-1234 to the entity 12-AB-1234.
	- Caution: This only works if the partial match is unambiguous. If the text AB-1234 could also relate to another entity like 13-AB-1234, no match will be returned.
- **Tuning minTokens parameter:**  
	- **Keep** **2** **(Default):** This is the recommended setting to prevent false positives. It avoids matching common, non-specific single words or numbers (e.g., "23") that might be in your entity list but appear frequently in the diagram.
	- **Set to** **1****:** Lower this parameter **only if** you are specifically trying to find entities that consist of a single token (e.g., a single standalone number or a single word) and you are failing to find them with the default.
- **Other tuning paremeters:**  
	- There are a **plethora of other tuning parameters/advanced settings** (for example, to read SHX or embedded text within the file. [Refer to the internal API reference](https://api-docs.cogheim.net/rapidoc/#post-/context/diagram/detect).
- **Consistent Identifier Formatting:** Text-based identifiers (e.g., tag names) should follow consistent formatting across diagrams to improve matching accuracy.
- **Use the RAW OCR Endpoint for false negatives:**[Retrieve RAW OCR result endpoin](https://pr-2117.specs.preview.cogniteapp.com/playground.json.html#tag/PandIDs-and-files/operation/pnidOcr) t can be a useful debugging tool to understand why certain entities for example, were not detected.

**4\. Validation and Automation**

- **Human-in-the-Loop (HIL):** Use UI-based validation for critical diagrams.
- **Automated Linking:** For non-critical diagrams, automate the process by auto-approving matches.
- **More generally, implement an appropriate QA strategy** to ascertain the quality and completeness of the contexutalization.

---

### Code Examples

Deployment Packs:

- [P&ID Annotation Workflow using CDF Data Modeling](https://hub.cognite.com/deployment-packs-472/p-id-annotation-workflow-using-cdf-data-modeling-cognite-official-4680)

Knowledge Base:

- [Diagram parsing for multi-page PDFs](https://github.com/cognitedata/gss-knowledge-base/tree/main/contextualization/toolkit_examples/diagram-parsing_for_multi_page_pdfs)

---

### Checklist

| **Category** | **Checklist Item** | **✅** |
| --- | --- | --- |
| **File & Entity Prep** | Ensure diagrams are in a supported format (PDF, JPEG, PNG, or TIFF). |  |
|  | Verify that target entities (Assets, Files, etc.) exist in the knowledge graph to be matched against. |  |
|  | Check that identifier formatting is as consistent as possible across the target entities (and ideally should be the case within the diagrams. |  |
| **Matching Configuration** | Plan to link detected text blocks in diagrams to their corresponding entities (Assets/Tags) and other files in the knowledge graph. |  |
|  | Start with default parameters (partialMatch=false, minTokens=2). |  |
|  | Select the 'Advanced' model if tuning is required for non-standard tags. |  |
|  | (If tuning) Set partialMatch=true only if you must match partial tags (e.g., missing prefixes) and understand the ambiguity risk. |  |
|  | (If tuning) Set minTokens=1 only if you must match single-token entities. |  |
|  | Plan to link detected text blocks in diagrams to their corresponding entities (Assets/Tags) and other files in the knowledge graph. |  |
| **Validation** | Decide validation strategy: HIL (UI approval) for critical diagrams or Automated (auto-approve) for non-critical. |  |
|  | Define and implement a QA strategy to ascertain the quality and completeness of the contextualisation. |  |
|  | (If debugging) Be prepared to use the RAW OCR result endpoint to investigate missed detections. |  |
| **Pipeline** | Consider using the "P&ID Annotation Workflow" deployment pack as a starting point. |  |
|  | Adhere to Data Pipelines Best Practices (async calls, batching, rate limiting). |  |

## Diagram Parser (new)

*Note: The Detailed Diagram Parsing service is in the beta stage and is constantly evolving. Certain Best Practices should be expected to develop alongside the product.*

### Overview

The Detailed Diagram Parser (DDP) is purpose-built for high-precision symbol detection within vectorized file formats like PDFs. The DDP's core function is to transform your static diagram library into digitalized, structured representations, unlocking the data required for knowledge graph creation and advanced analytics.  
  

![[d81b07fbb23a9b2b0e88fc99c460f8d7_MD5.webp]]

---

### References

[Cogfluence: Detailed Diagram Parser](https://cognitedata.atlassian.net/wiki/spaces/Contextual/pages/4104519778/Detailed+Diagram+Parsing)

---

### Best Practices

  
**1\. Objective**

- **Symbol detection:** Detect symbols in your PDF
- **Internal Relationships:** Map connections between symbols
	- e.g., flow paths, control loops
- **Entity Linking:** Link detected annotations (text entities) in the diagram to instances in your knowledge graph.

**2\. Key Considerations**

| **Consideration** | **Detail** | **Notes** |
| --- | --- | --- |
| File Format | **Vectorized** PDF’s | The DDP service only support vectorized PDF’s |
| Symbol Libraries | Requires a **symbol library** to be selected before parsing | **Templates:** A collection of symbols provided by Cognite.  **Custom Libraries:** Custom collections of symbols, each symbol is connected to an AssetClass → AssetType. |
| Geometry Support | Symbols must include **geometries** for accurate detection | Multiple geometries improve detection across visual variations.(i.e., multiple variants of the same symbol) |
| Mapping Workflow | Includes **symbol-to-tag** and **symbol-to-asset** mapping | Mapping is based on spatial overlap and metadata. |
| Connection Mapping | Supports **symbol-to-symbol connections** | Enables creation of a digital sub-graph.Will only trigger on vectors recognized as “piping”. |

**3\. Configuration**

- **Symbol Library Curation**: The accuracy of symbol detection is highly dependent on the quality and completeness of the selected symbol library. Invest time in customizing or extending a template library to match your specific diagram conventions \*before\* running large-scale parsing jobs.
- **Custom Symbols:** Use files that contain the original collection of symbols (legendary files) to create the base library. Enhance by adding variants as you start parsing and encounter different versions of the same symbol.

**4\. Validation and Automation**

- **Visual Validation:** Validate symbol detection and relationships using the UI by comparing the detected vectors and the diagram itself. You can toggle both the diagram on and off, as well with the detected vectors. Use the connection tab to visually confirm that all connections are created as well.
- **Automated Flow:** For high-volume use cases, automate parsing and linking using predefined rules.
- **More generally, implement an appropriate QA strategy** to ascertain the quality and completeness of the contextualisation. The more complex the relationships are, the more complex it will be to automate.

---

### Code Examples

**Symbols and text detection**

```python
1client.post(
2
3    url=f"/api/v1/projects/{client.config.project}/diagram-parsing/parsing/full",
4
6
7    json={
8
9        "documents": diagrams,
10
11        "filters": {
12
13            "Assets": config.service.filters.asset_filter,
14
15            "File": config.service.filters.file_filter,
16
17        },
18
19        "libraryId": config.service.library_id,
20
21        "minTokens": config.service.minTokens,
22
23        "nonce": client.iam.sessions.create().nonce,
24
25        "partialMatch": config.service.partialMatch,
26
27        "searchField": config.service.filters.searchField,
28
29    },
30
31)
```

  
**Symbols only**  

```python
1client.post(
2
3    url=f"/api/v1/projects/{client.config.project}/diagram-parsing/parsing/symbol-detection",
4
6
7    json={
8
9        "documents": diagrams,
10
11        "libraryId": config.service.library_id,
12
13        "nonce": client.iam.sessions.create().nonce,
14
15    },
16
17)
```

---

### Checklist

| **Category** | **Checklist Item** | **✅** |
| --- | --- | --- |
| File & Symbol Prep | Use vectorized files with embedded text to maximize the service performance. Tools like QCAD can be used to convert different formats such as SVG, DXF etc to achieve this. |  |
|  | (If using rasterized PDF) The service will notify you that the file is not vectorized. |  |
|  | (Crucial) Invest time to curate and customize a symbol library before running large-scale jobs. |  |
|  | Ensure symbols in the library include all symbol variations for accurate detection. Do this as an iterative process, avoiding unnecessary variants of the symbol. |  |
|  | Apply (to the library) any required symbols that are not present in the legendary file. Assure to have the corresponding AssetClass + AssetType. |  |
| Mapping Configuration | Plan to map internal symbol-to-symbol relationships (e.g., flow paths, control loops) if not automatically detected. |  |
|  | Plan to link detected annotations to their corresponding asset in the knowledge graph |  |
| Validation | Decide validation strategy: Visual UI validation or an automated flow. |  |
|  | (If HIL) Use the UI to visually confirm symbol detection and connections are correct. |  |
|  | (If Automating) Define clear rules for parsing and linking for high-volume scenarios. |  |
|  | Define and implement a QA strategy to measure the quality and completeness of the contextualisation. |  |
| Pipeline | Adhere to Data Pipelines Best Practices (async calls, batching, rate limiting). |  |

×

### Cookie settings

  

We use 3 different kinds of cookies. You can choose which cookies you want to accept. We need basic cookies to make this site work, therefore these are the minimum you can select. [Learn more about our cookies.](https://hub.cognite.com/site/terms)

---

### Best Practices Library\Best Practices (Internal)\Best Practices - Atlas AI for Quickstart  Cognite Hub.md

---
title: "Best Practices - Atlas AI for Quickstart | Cognite Hub"
source: "https://hub.cognite.com/best-practices-internal-461/best-practices-atlas-ai-for-quickstart-5731"
created: 2025-12-28
description: "Overview	References	Best Practices	1. Project Scoping and Definition		Clearly Defined Deliverables			Accurate Effort Estimation				2. Project Delivery..."
tags:
  - "CDF"
  - "CogniteDataFusion"
image: "https://uploads-eu-west-1.insided.com/cognite-en/attachment/1440x640/dafc2298-6253-4cf4-92b1-93fb6602cd8b_thumb.png"
---
![[9f6082dded03fa9f35f3a28f1636197f_MD5.webp]]

- [Valeriya Naumova](https://hub.cognite.com/members/valeriya-naumova-3745)
- Practitioner

## Overview

This guide outlines a strategic approach to designing, deploying, and managing ATLAS AI agents for QuickStart projects. It focuses on core principles to ensure efficiency, maintainability, and high-quality delivery, maximizing value and ROI for our clients.

| **Service** | **Description** |
| --- | --- |
| [**Data Modelling Service**](https://docs.cognite.com/cdf/dm/) | Data Modelling Service (DMS) is the engine within CDF that builds and manages an Industrial Knowledge Graph. It uses a property graph structure (nodes, edges, and properties) with components like spaces, containers, and views to organise and contextualise industrial data. DMS provides APIs for creating, ingesting, and querying these models. |
| [**Toolkit**](https://docs.cognite.com/cdf/deploy/cdf_toolkit/) | Tooling to manage, configure, and deploy resources in CDF. |
| [**Atlas AI**](https://www.cognite.com/en/product/atlas) | Low-code workbench for building and deploying industrial AI agents that leverage contextualised data to address specific operational use cases. |

---

## References

  
[ATLAS Playbook (WIP)](https://docs.google.com/document/d/10JwuYQF449M-07v2NSmm9OQPrBXTS-3Crr60giJEXpw/edit?tab=t.0#heading=h.zbmtwot4d7d2)

[Agents Templates: Confluence Page](https://cognitedata.atlassian.net/wiki/spaces/AA1/pages/5495161118/Atlas+AI+-+Industrial+Agentic+Solutions)

[Atlas Hub Github repository](https://github.com/cognitedata/value-delivery-atlas-hub)  

---

## Best Practices

### 1\. Project Scoping and Definition

#### Clearly Defined Deliverables

A QS Scope of Work (SoW) may include up to two QS agents from the table below. For each selected agent, the SoW must clearly define the expected deliverables and success criteria. For example, the Appendix of the QS SoW specifies the types of questions a Data Scout or Time Series Trender agent should be able to answer.

![[5cb7ce85396a05f5b8bfb65a66db2e0d_MD5.webp]]

\* X% Value means that by utilizing the Agent, industrial companies can reduce time their SMEs, operational team members, and planners spend looking for the information needed for their jobs by X%.

#### Accurate Effort Estimation

The [Delivery Effort Template (DET)](https://docs.google.com/spreadsheets/d/1WLcRHdL5IJoBScZpR_SJo3p-wS9nTF5LQS2KLkYV5d4/edit?gid=0#gid=0) should account for the required effort, with a rough guideline of half a week of Data Scientist (DS) work per agent and one week for Value Launchpad, a workshop held at the project end to identify and prototype new agents / use cases for the project's expansion phase. The SoW reviewer and approver are responsible for validating that the effort estimates, agent deliverables, and success criteria are properly captured in the scope.

### 2\. Project Delivery

#### Agent Scope and Capabilities

Agent detailed descriptions with configurations are available at this [Confluence page](https://cognitedata.atlassian.net/wiki/spaces/AA1/pages/5495161118/Atlas+AI+Industrial+Agentic+Solutions), the Streamlit agents are also published in the [Atlas Hub](https://github.com/cognitedata/value-delivery-atlas-hub) [Github Repo](https://github.com/cognitedata/value-delivery-atlas-hub) (end of September). In addition, Data Scout and Time Series Trender agents are available as templates in the Fusion UI.

At the project start, SA together with the customer details the scope of the agent based on the SOW description and customer data. Together they prepare an evaluation dataset for an agent to be used in the UAT and follow up performance monitoring. The evaluation dataset and procedure will be revisited by the DS after the data model is built to make any possible changes with the customer to account for the realities of the customer's data landscape, such as gaps or incomplete information.

#### Evaluation datasets

For the *Data Scout*, we recommend to include 10-20 questions with the expected answers in the evaluation dataset. The questions should be of a simple nature and reflect the data availability and quality in CDF. The agent must achieve an overall Task Success Rate of at least 90% on the UAT evaluation dataset. A template for the evaluation dataset is provided [here](https://docs.google.com/spreadsheets/d/1Be2z8maGGY7ZKT4zmw70bRVBoknLO6scLTLAzQwFflg/edit?gid=0#gid=0).

For the *Time Series Trender Agent*, we recommend having a set of 10-15 questions, defined with the customer. The procedure for automated testing of the **Time Series Trender Agent is not fully defined yet, it is work in progress**.

For the Streamlit-based agents, evaluation and evaluation datasets, respectively, will be focused on testing completion endpoints. Specifically, it means for the

- *Operations Summariser Agent:*the evaluation dataset will evaluate the quality of the text summaries. By evaluating that the summarizer agent includes/refers to the objects in the data model from after the time filter is applied on it. We recommend having 5-10 summaries in the dataset.
- *Work Package Generator Agent:*this agent will be evaluated qualitatively with the user. The evaluation dataset will involve few (~5) target canvases the agents should be automatically able to generate and populate. These target canvases will be pre-defined along with the customer and the agent should be able to generate these canvases with all the objects in the evaluation dataset.
- *Work Order Classifier:*the evaluation dataset will consist of work order names and corresponding classifications. The subset of activities on which the work order classifier will work will also need to be pre-defined since not all activities will contain detailed description which will make it automatically amenable to classification through a chat completion end-point. We recommend having at least 50 work orders, which shall be deemed representative of typical operational data. The agent is expected to achieve a minimum F1-Score of 0.85 on business-critical categories (e.g., 'Urgent Safety', 'High-Cost Repair'). The agent must achieve an overall weighted-average F1-Score of 0.90 across all categories.

#### Data Modeling Best Practices

Agent performance is fully dependent on the quality of the data contextualization and data model clarity. The QS agents have been built and tested on the CDM and simple extensions. For detailed guidance on optimizing data models for ATLAS AI, refer to the [Cognite Docs](https://docs.cognite.com/cdf/dm/dm_guides/dm_best_practices_ai_search/) and the [Confluence page: DM for Better Agent Interaction](https://cognitedata.atlassian.net/wiki/spaces/AA1/pages/5515739139/Data%2BModeling%2Bfor%2BBetter%2BAgent%2BInteraction).  

#### Evaluation Framework for UAT

The Project Manager (PM) is responsible for conducting the agent User Acceptance Testing (UAT). Until there is an official evaluation framework in the product, the UAT is performed using the Streamlit Evaluation Suite developed by the ATLAS team. The evaluation is conducted jointly with the customer on the pre-defined evaluation dataset.

![[1f187c478745c58ae95451f1b94debd5_MD5.webp]]

**Troubleshooting**

ATLAS co-innovation team is here to advise and support if there are any unknown or complex issues with the agent development. For the troubleshooting, consult the [support troubleshooting playbook](https://cognitedata.atlassian.net/wiki/spaces/AA1/pages/5596610652/00_Service+Overview), slack channels #topic-ai-agents to check if similar issues were encountered.

**Agent archiving**

Upon successful UAT and sign-off, the final agent configuration and evaluation dataset must be submitted to the central agent database [ATLAS Hub Github Repo](https://github.com/cognitedata/value-delivery-atlas-hub) for tracking, reuse, and analytics. This is needed to ensure that we have a snapshot of the successful agent performance for any further troubleshooting and also to improve our templates.  

#### Value Launchpad

At the end of the Quick Start project, the Value Delivery, Value Engineering, and Field Engineering teams will engage in a Value Launchpad with the customer. The goal is to identify and prototype new agents for the project's expansion phase.  

### 3\. Post-Delivery and Support

#### Agent Lifecycle Management

In the event of performance deterioration, the customer is expected to run the Evaluation Suite in their CDF environment with the same dataset as used for UAT. If the evaluation results drop by more than 10-15%, then the customer should file a support ticket via Zendesk platform.

Note: The process might be changed once the Evaluation feature is released as an official part of the Fusion (expected date: Q1 2026).

#### Handover to Support

The PM is responsible for a proper handover of the agents and solutions to the support team. The process is documented in [Confluence](https://cognitedata.atlassian.net/wiki/spaces/CMS/pages/3553951819/Template+cust_name+-+solution_name+Onboarding+Support+team+onto+the+Solution+-+Complete+Requirements+List) (general solution support handover) and [specifics for the ATLAS project onboarding are here](https://docs.google.com/document/d/1xprUdveiJl2hca3Hu4Warf1t2kZi_iKDOoSVZB5DTNU/edit?tab%3Dt.0%23heading%3Dh.26i9xwyk3l1r).

### 4\. Continuous Improvement and Enablement

#### Centralized Template Repository

All project templates, including risk assessments, UAT plans, and evaluation datasets, should be maintained in [ATLAS Hub Github Repo](https://github.com/cognitedata/value-delivery-atlas-hub). This ensures consistency across projects and provides a single source of truth for the delivery team. Team members are responsible for using the latest versions of these templates.

#### Upskilling and Partner Enablement

To successfully develop and deploy ATLAS AI, team members need a fundamental understanding of two key topics: data modeling and agent capabilities. The upskilling has to be done continuously as ATLAS is an evolving product and the agentic technology is evolving rapidly so both internal and external upskilling should be held on a quarterly basis.

## Checklist

| **Category** | **Checklist Item** | ✅ |
| --- | --- | --- |
| **Project Scoping** | Agentic scope reviewed and validated |  |
|  | Efforts (DET) reviewed and approved by a DS Manager |  |
|  | Evaluation dataset (for UAT/monitoring) scoped and detailed with the customer |  |
| **Project Delivery** | Success criteria and UAT plan for agents aligned with the customer |  |
|  | Data modeling and contextualization completed |  |
|  | Evaluation dataset and procedure revisited by the DS after the data model is built |  |
|  | Agent configuration and internal testing performed |  |
|  | Used the Streamlit Evaluation Suite for UAT |  |
|  | Consulted troubleshooting resources (playbook, Slack) for complex issues. |  |
|  | UAT and sign-off obtained from the customer |  |
|  | Agent and evaluation dataset submitted to the Agent Hub Repo |  |
|  | Customer informed of the performance deterioration support process (running Evaluation Suite, Zendesk ticket). |  |
|  | Value launchpad executed |  |
| **Post-Delivery** | Handover to support team completed |  |
| **Continuous Improvement and Enablement** | All project templates maintained in the ATLAS Hub Github Repo |  |
|  | Team members engaged in continuous upskilling/enablement (on data modeling and agent capabilities) |  |

×

### Cookie settings

  

We use 3 different kinds of cookies. You can choose which cookies you want to accept. We need basic cookies to make this site work, therefore these are the minimum you can select. [Learn more about our cookies.](https://hub.cognite.com/site/terms)

---

### Best Practices Library\Best Practices (Internal)\Best Practices - Data Modeling  Cognite Hub.md

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

---

### Best Practices Library\Best Practices (Internal)\Best Practices - Data Pipelines  Cognite Hub.md

---
title: "Best Practices - Data Pipelines | Cognite Hub"
source: "https://hub.cognite.com/best-practices-internal-461/best-practices-data-pipelines-5298"
created: 2025-12-28
description: "Author	Overview	References	Best Practices	1. Reusable & Maintainable Pipelines		Toolkit			Composable Tasks Grouped into Workflows			Templated Workfl..."
tags:
  - "CDF"
  - "CogniteDataFusion"
image: "https://uploads-eu-west-1.insided.com/cognite-en/attachment/1440x640/4711e948-3921-41cb-99ba-b81302831d90_thumb.png"
---
![[91ce290d69d1aa02287767c094968cf7_MD5.webp]]

- [Colin Nattrass](https://hub.cognite.com/members/colin-nattrass-5083)
- Practitioner

---

## Author

Colin Nattrass  

---

## Overview

  
A **data pipeline** is a series of automated data processing steps. It typically involves moving data from a source, transforming it, and loading it into a destination for storage or analysis.

This guide outlines best practices for orchestrating these data pipelines specifically within Cognite Data Fusion (CDF). The guidance focuses on building robust, efficient, and maintainable pipelines for transforming and working with data **already inside CDF**.

This document's scope intentionally **excludes** initial data ingestion (like extractors or direct API writes *into* CDF) and outbound processes where data is read from CDF to be consumed by external systems.

The following tools and services are commonly used for compute and pipeline orchestration:

| **Service** | **Description** |
| --- | --- |
| [Data Workflows](https://docs.cognite.com/cdf/data_workflows/) | [DAG-based](https://en.wikipedia.org/wiki/Directed_acyclic_graph) orchestration of internal and external tasks. |
| [Functions](https://docs.cognite.com/cdf/functions/) | Compute solution for executing custom Python logic and interacting with APIs. |
| [Transformations](https://docs.cognite.com/cdf/integration/guides/transformation/transformations/) | SQL or mapping-based data transformations within CDF. |
| [Staging/Raw](https://docs.cognite.com/cdf/integration/guides/extraction/raw_explorer/) | [key: value storage](https://en.wikipedia.org/wiki/Key-value_database) useful for checkpointing and exchanging data between tasks. |
| [Data Modelling Service](https://docs.cognite.com/cdf/dm/) | [A Property Graph](https://en.wikipedia.org/wiki/Graph_database) database that can serve similar purposes as Staging/Raw. |
| [Extraction Pipelines](https://docs.cognite.com/cdf/integration/guides/interfaces/about_integrations/) | [Observability and monitoring](https://en.wikipedia.org/wiki/Observability_\(software\)) solution for extractors and data pipelines. |
| [Toolkit](https://docs.cognite.com/cdf/deploy/cdf_toolkit/) | Tooling to manage, configure, and deploy resources in CDF. |

  

---

## References

[Data Engineering Wiki - Best Practices](https://dataengineering.wiki/Guides/Data+Pipeline+Best+Practices)  
  
[Naming Convention Best Practices](https://hub.cognite.com/best-practices-internal-461/best-practices-naming-conventions-5734)

---

## Best Practices

### 1\. Reusable & Maintainable Pipelines

Design pipelines with **modularity** in mind to promote reusable and maintainable logic.  

#### Toolkit

- Use **Toolkit** to manage most CDF resources for your project.
- Exceptions may include cases requiring dynamic [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) operations (e.g., frequently changing). In these cases, resources should still be managed programmatically, under version control, and deployed through [CI/CD](https://en.wikipedia.org/wiki/CI/CD).

#### Composable Tasks Grouped into Workflows

- Break complex operations into **small, single-purpose tasks**.
- Chain tasks such as **Cognite Functions** and **Transformations** using **Data Workflows**.

#### Templated Workflows

- Use **templated Workflows** to deploy solutions across multiple locations.
- Parameterise them using [Toolkit variables](https://docs.cognite.com/cdf/deploy/cdf_toolkit/api/config_yaml#variables-structure).
- A common pattern is deploying **one Workflow per location, per data product**, each with distinct inputs and cadence.
- Prefer **single, parameterised deployments of Cognite Functions**; for example, by deploying your Function with a mode parameter. This is both to group related code into a single deployable unit, and to reduce the number of functions deployed to CDF. [An example of this pattern can be found here.](https://github.com/cognitedata/gss-knowledge-base/tree/main/contextualization/toolkit_examples/diagram-parsing_for_multi_page_pdfs)
- **Transformations are a case where we need to balance customization with re-use**. If the tailoring/customization in a transformation to cater for differences across locations or other splits (eg, source system) gets high, it's probably wiser to create a separate transformation, even if there is some duplication.

**Example: Scaling a Pipeline for Multiple Locations**  

Consider a scenario where the same data pipeline must be deployed for multiple sites: location\_1 and location\_2.

The pipeline's goal is to process data from CogniteFiles and Tags, and contextualise the files by linking them to their relevant tags in a unified target Data Model.

The operational flow follows this order: **Load Tags → Contextualise Files**.  
  
**CDF Resources:**

- **Cognite Files**: Files are assumed to be written directly into the Data Model (File type) via an upstream ingestion process.
- **Transformations**: Used to load tag data from Raw/Staging into the Data Model (Tag type). Raw/Staging also assumes upload by an upstream process.
- **Functions**: Run contextualisation logic that links files to tags (for further information, see [Best Practices for Contextualisation](https://hub.cognite.com/best-practices-internal-461/best-practices-2d-contextualization-5732)[)](https://hub.cognite.com/best-practices-internal-461/best-practices-2d-contextualization-5732).
- **Target Data Model**: Stores Tag and File types, as well as their contextualised relationships.

**Parameterisation Strategy:**

1. **Templated Workflows**: A single Workflow template is authored and then deployed multiple times, each with a different site\_id (e.g., location\_1, location\_2). Each instance runs independently and can be monitored and triggered on its own.
2. **Shared Functions**: The contextualisation logic is implemented once as a shared Cognite Function that accepts input parameters like location\_id at runtime. This enables centralised logic with decentralised execution.
3. **Transformation Instances per Site**: If the transformation logic cannot be easily parameterised (e.g., it includes hardcoded site-specific identifiers or schema differences), a separate transformation can be instantiated per site (e.g., tr\_location\_1\_load\_tags, tr\_location\_2\_load\_tags). These are grouped and managed by their corresponding workflow instance.

This architecture achieves a balance: shared core logic for efficiency, while maintaining isolated instances where configuration demands it.

![[72bb4da95975fef79978ab4514c7928e_MD5.webp]]

The associated deployed workflows look as follows:

![[0a3d77eb154b37ec101f10ee39ad253c_MD5.webp]]

![[8b7a078725ce2ff6fa46565a1d2d41ca_MD5.webp]]

### 2\. Scheduling & Triggers

Use intelligent scheduling and triggering to optimize resource usage and ensure data freshness.  

#### Flexible Triggers

- Use **event-based triggers** (e.g., via the) to reduce latency.
- Where events aren’t feasible, use [CRON-based](https://en.wikipedia.org/wiki/Cron) schedules.
- Consider triggering Workflows directly from services like [custom extractors](https://docs.cognite.com/cdf/integration/concepts/extraction/#custom-extractors) or [Azure Data Factory](https://azure.microsoft.com/en-us/products/data-factory/).

#### Resource Awareness

- **Factor in total project load** and downstream impact when scheduling.
- **Avoid overlapping schedules** (e.g. every hour on the hour for every Workflow in the project).  
	- Tip: Schedule on odd minutes. Most people choose schedules at x:00, x:30, x:45 etc. Stagger schedules by opting for times like x:17, x:41, x:57 etc.
- **Monitor resource usage** to prevent contention in enterprise settings, where there are multiple groups utilising the same CDF project. Most product teams [expose valuable dashboards](https://grafana.cogheim.net/dashboards) detailing metrics related to their service. For larger accounts, this is something that can be discussion in conjunction with the Technical Account Manager (TAM).

### 3\. Observability

Implement monitoring and logging to gain insight into pipeline health.  

#### Monitoring & Alerts

- **Notifications for failures in stages of Data Workflows are on the 2026 roadmap**; until this is in place the following workaround strategies can be used:-  
	- **Configure email alerts for Transformations.**
	- **Associate a Cognite Function with an Extraction Pipeline**, where similar email alerting functionality can be configured. This is typically used with Extractors, however can be re-purposed for this case.
	- **Build a custom reporting solution,** that utilises the.
- Configure **email alerts** using group emails instead of individuals.
- Where possible, include **context** in alerts (workflow ID, error type, task name).
- **Document runbooks or troubleshooting steps**.

#### Logging

- Use **structured** print() **statements** in Functions to log key inputs, outputs, and metrics.
- Avoid the logging module; output can be suppressed.
- **Never log sensitive data**.

### 4\. Resilience & Integration

Design fault-tolerant pipelines with safe external integrations.  

#### Error Handling

- Implement [**timeouts, retries with backoff**](https://learn.microsoft.com/en-us/azure/architecture/patterns/retry), and structured error handling in all API calls.

#### Batching

- Apply a **batching strategy** (with multiple items in a call), where feasible.

#### Pacing

- Apply a **pacing strategy** (to control the timing or frequency of requests). Be aware of the rate limits and resulting throttling of the services being used.

#### Idempotency

- Ensure all tasks and external calls are [**idempotent**](https://dataengineering.wiki/Concepts/Software+Engineering/Idempotence) (i.e., safe to retry).

#### Recovery

- Use [**checkpointing**](https://en.wikipedia.org/wiki/Application_checkpointing) (Staging/Raw) for long-running steps.
- Employ **conditional branches** in Workflows for fallback scenarios.

#### Secure Authentication

- Integrate securely using **OpenID Connect** or other secure methods.
- Use [**CDF Function Secrets**](https://docs.cognite.com/cdf/functions/security) for credential management in Cognite Functions.

### 5\. Scalability

Design pipelines that scale with growing data and complexity.  

#### Parallelism

- Use [**fan-out/fan-in**](https://java-design-patterns.com/patterns/fanout-fanin/) patterns in Workflows for concurrency.
- Use [Dynamic Tasks](https://docs.cognite.com/cdf/data_workflows/task_types#dynamic-task) when Functions or Transformations can't handle large volumes in a single run.
- Intermediate data should be exchanged using **Staging/Raw** or the **Data Modelling Service**.

**Example: Contextualising thousands of files**

**Scenario**

You need to process thousands of documents stored in CDF. A single function run can't handle them all at once, so the pipeline is designed to process batches of files in parallel.

- The workflow runs on a schedule (e.g., hourly).
- Each run processes as many batches as are currently available.
- Remaining unprocessed documents are picked up in the next run.

**Execution Logic**

- **Step 1: Identify Batches**  
	- A Cognite Function identifies the next set of documents to process (e.g., files not yet contextualised) and writes metadata about each batch to Raw or the Data Model.
- **Step 2: Conditional Fan-Out**  
	- A conditional check determines whether any documents were found:
		- If none are found, the workflow ends.
		- If batches exist, a Dynamic Task fans out execution to process each batch in parallel. Each branch calls a Function to handle one batch.
- **Step 3: Finalise**  
	  
	After all batches complete, a final Function logs the processing status to Raw or the Data Model. The workflow then ends. Unprocessed documents will be picked up in the next scheduled run

**Architecture**  

![[ecf668b89ad9c2e3dff5c610c83b43f6_MD5.webp]]

**Good Practices**

- **Limit concurrency** (e.g., 4 parallel batches) to manage resource usage effectively.
- **Checkpoint** progress in Raw or the Data Model to track which documents have been processed.
- **Use retries with backoff** to handle transient failures.
- Ensure Functions are **idempotent**, so reprocessing does not cause duplication or corruption.
- Keep batch logic **lightweight and self-contained** for parallel execution.

#### Benchmarking

- Benchmark pipeline stages regularly to identify performance bottlenecks.  
	---

## Checklist

| Category | Checklist Item | ✅ |
| --- | --- | --- |
| **Modularity & Reuse** | Use Toolkit to manage CDF resources |  |
|  | Design tasks as single-purpose and composable |  |
|  | Chain Functions and Transformations via Workflows |  |
|  | Use Staging/Raw or the Data Modelling Service for intermediate data exchange between Workflow tasks. |  |
|  | Use templated and parameterised Workflows |  |
|  | Deploy Functions once with parameterised inputs |  |
|  | Group Transformations consistently with Workflows |  |
| **Scheduling & Triggers** | Use event-driven triggers or optimised CRON schedules |  |
|  | Schedule with awareness of project-wide resource load |  |
| **Observability** | Implement structured logging in Functions |  |
|  | Configure monitoring and alerts with full context |  |
|  | Document runbooks and troubleshooting guidance |  |
| **Resilience & Integration** | Implement robust error handling (timeouts, retries) |  |
|  | Ensure all tasks and external calls are idempotent |  |
|  | Use checkpointing and resume logic for long-running steps |  |
|  | Define conditional fallback paths in Workflows |  |
|  | Securely manage authentication and credentials |  |
|  | Apply batching and pacing for API calls, be aware of rate limits and throttling mechanisms for services being used. |  |
| **Scalability** | Use fan-out/fan-in patterns for concurrency |  |
|  | Use Dynamic Tasks/Workflows for handling large datasets |  |
|  | Regularly benchmark pipeline stages for performance |  |
| **Naming conventions** | An explicitly defined naming convention is in place for CDF resources which comprise the pipeline, referencing the defined [Naming Convention Best Practices](https://hub.cognite.com/best-practices-internal-461/best-practices-naming-conventions-5734). |  |

×

### Cookie settings

  

We use 3 different kinds of cookies. You can choose which cookies you want to accept. We need basic cookies to make this site work, therefore these are the minimum you can select. [Learn more about our cookies.](https://hub.cognite.com/site/terms)

---

### Best Practices Library\Best Practices (Internal)\Best Practices - Naming Conventions  Cognite Hub.md

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

---

### Best Practices Library\Best Practices (Internal)\Best Practices - Quickstart  Cognite Hub.md

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

---

### Best Practices Library\Best Practices (Internal)\設備階層機器仕様関連のデータモデリングの課題 - Cognite Japan - Confluence.md

---
title: "設備階層/機器仕様関連のデータモデリングの課題 - Cognite Japan - Confluence"
source: "https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5346295921"
created: 2025-12-28
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---

# Data Modeling Challenges Related to Equipment Hierarchy/Specifications

データモデリングへの入門の手始めとして、設備階層と機器仕様をどうモデリングするか、について検討しています。まだ解決できていませんが、検討の過程を共有しながら皆さんと解決していきたいと思っています。(2025/5/12現在。by 岡田)

## Table of Contents
1.  [Equipment Hierarchy](#equipment-hierarchy)
    1.1. [Common Implementation Example](#common-implementation-example)
    1.2. [Strict Implementation Example (Unsuccessful)](#strict-implementation-example-unsuccessful)
2.  [Class / Type](#class-type)
    2.1. [Class Overview](#class-overview)
    2.2. [Class Usage Distinction](#class-usage-distinction)
    2.3. [Asset Class Implementation](#asset-class-implementation)
    2.4. [Refinement by Asset Class Inheritance (Desired but Unsuccessful)](#refinement-by-asset-class-inheritance-desired-but-unsuccessful)
    2.5. [Equipment Class Implementation (Not like Asset Class)](#equipment-class-implementation-not-like-asset-class)

---

## 1. Equipment Hierarchy

### 1.1. Common Implementation Example

`cdf_cdm`では設備階層 (SAPでいう機能場所、Maximoでいうロケーション、CFIHOSでいうTag) は `CogniteAsset` で、実体のある設備や機器 (SAPでいう設備、Maximoでいう資産、CFIHOSでいうEquipment) は `CogniteEquipment` で実装するイメージとなっています。

下記はSAP PMを運用する顧客を想定した実装例です。`CogniteAsset` を継承した `Floc` というクラスで `parent` / `children` で階層を作り、最下層の `Floc` に `CogniteEquipment` を継承した `Equip` というクラスを割り当てています。

**Images:**
*   ![image-20250512-105930.png](blob:https://cognitedata.atlassian.net/6cc12488-e329-41d7-8286-c789faf9a7b7#media-blob-url=true&id=9f76f524-47d1-4b2a-833a-b56d602adbb7&collection=contentId-5346295921&contextId=5346295921&width=1146&height=485&alt=image-20250512-105930.png)
*   ![image-20250512-122855.png](blob:https://cognitedata.atlassian.net/231984c2-9beb-499f-8509-533b1eb6a692#media-blob-url=true&id=2c60cef8-5eea-40f7-9ff6-5095dc971417&collection=contentId-5346295921&contextId=5346295921&width=1735&height=595&alt=image-20250512-122855.png)

`Floc` には `Hierarchy Level` のようなPropertyを設定し、階層を指定するのが良さそうです。検索時に`Filters`で指定できるようになります。

### 1.2. Strict Implementation Example (Unsuccessful)

以下はうまくいかなかった例です。一般に会社、工場、エリア、装置、設備、というような階層が明確に作られるため、そのようにクラスを配置する方が`Atlas AI`による検索対象を指定しやすいと考えましたが、`CogniteAsset` を継承したひとつのクラスだけで階層を作らないと、`Search`の画面で`Tree`がうまく表示されないなどの不具合が出ました。(このあたり、良い例があれば教えてほしい。)

**Images:**
*   ![image-20250512-111816.png](blob:https://cognitedata.atlassian.net/6910b7d1-0357-4901-900a-50e434a8d570#media-blob-url=true&id=6910b7d1-0357-4901-900a-50e434a8d570&collection=contentId-5346295921&contextId=5346295921&width=1038&height=505&alt=image-20250512-111816.png)

(2025/10/20追記) 異なる`View`のインスタンス間におけるパスや階層の表示はサポートしていないとのことです。開発として、実装する予定ですが、(2025年5月の時点で)早くて26Q1らしいので、最新情報要確認。

**Reference Link:**
*   [https://cognitedata.atlassian.net/browse/UX-4228](https://cognitedata.atlassian.net/browse/UX-4228)

## 2. Class / Type

### 2.1. Class Overview

設備は一般に機種で分類 (SAPやCFIHOSでいうクラス、Maximoでいう分類) され、与えられた機種に応じて詳細な仕様 (SAPでいう特性値、Maximoでいう仕様、CFIHOSでいうProperty) を入力できたり、故障部位の候補を指定できたりします。クラスは設備階層 (機能場所、ロケーション、Tag) 側にも、設備 (設備、資産、Equipment) 側にも設定することができ、仕様の場合、階層側のクラスにはプロセス設計上の要求仕様が、設備側には設置された機器の購入仕様が入力されます。

クラスの分類の仕方はCFIHOSで`Tag Class`および`Equipment Class`が階層的に定義されており、`Tag Class`に対応する`Equipment Class`も決まっています。

**Reference (CFIHOS 2.0 RDL を見やすく加工したもの):**
*   [https://docs.google.com/spreadsheets/d/1mmTLivBHrEmfJHSN7yMoVgxI96yFU2TB/edit?usp=sharing&ouid=105941753345609856745&rtpof=true&sd=true](https://docs.google.com/spreadsheets/d/1mmTLivBHrEmfJHSN7yMoVgxI96yFU2TB/edit?usp=sharing&ouid=105941753345609856745&rtpof=true&sd=true)

**Images:**
*   ![image-20250512-115704.png](blob:https://cognitedata.atlassian.net/dd158fdd-ea4f-4d1c-bd10-fc564336fab1#media-blob-url=true&id=dd158fdd-ea4f-4d1c-bd10-fc564336fab1&collection=contentId-5346295921&contextId=5346295921&width=1295&height=710&alt=image-20250512-115704.png)

### 2.2. Class Usage Distinction

`Tag Class`と`Equipment Class`を使い分けるのは面倒なので、`Equipment Class`だけを使用する顧客も多いと思われます。分けて運用する場合は、`Pump`, `Compressor`, `Vessel`, `Heat Exchanger`といった中分類を`Tag Class`で、`Centrifugal Pump`, `Rotary Pump`, `Reciprocating Pump`といったより詳細な小分類を`Equipment Class`で設定するのが良さそうです。

CFIHOSで仕様(`Property`)は`Tag Class`および`Equipment Class`それぞれについて詳細に定義されています (ただし必ずしもこれに従う必要はなく、実際に従っている顧客もあまりいなさそう)。EAM/CMMSでは設備にクラスを割り当てると、クラスに応じた詳細仕様を入力できるようになります。

### 2.3. Asset Class Implementation

`cdf_cdm` には `CogniteAssetClass`, `CogniteAssetType`, `CogniteEquipmentType` の3つが用意されています。これらの使い分けの意図はよく分かりませんが、とりあえず`CogniteAssetClass` と `CogniteEquipmentType` を実装してみました。

`CogniteAsset` を継承した `test3` という階層を作成し、`CogniteAssetClass` を継承した `FlocClass` として`Compressor`の`Property`を入力してダイレクトリンクしています。

**Images:**
*   ![image-20250512-131226.png](blob:https://cognitedata.atlassian.net/fb29e9f6-2c34-43c7-b992-ecdc127b5d8f#media-blob-url=true&id=fb29e9f6-2c34-43c7-b992-ecdc127b5d8f&collection=contentId-5346295921&contextId=5346295921&width=819&height=321&alt=image-20250512-131226.png)

すると、`FlocClass` を割り当てられた `Floc` には、`FlocClass` に設定された`Property`が含まれて表示されます。

**Images:**
*   ![image-20250512-131037.png](blob:https://cognitedata.atlassian.net/f6bcc3e6-93bb-4bc4-84f8-009574cbde19#media-blob-url=true&id=f6bcc3e6-93bb-4bc4-84f8-009574cbde19&collection=contentId-5346295921&contextId=5346295921&width=948&height=460&alt=image-20250512-131037.png)

ちなみに `FlocClass` には `Floc` からのダイレクトリンクだけでなく、`reverse`のリンクを`Property`として設定する必要があります。すると `FlocClass` に `Floc` へのリンクのタブが表示されます。

なお `FlocClass` を割り当てたとしても、クラス名などは `Floc` に表示されません。また設備を検索する際にクラス名でフィルターをかけたいところですが、 `FlocClass` の`Property`で絞り込むことはできません。よって `Floc` に`Property`として `Functional Location Class Name` や、その上位にあたる `Functional Location Category` (静機器、回転機、電気、計装といった大分類) などの項目を設定するのが良さそうです。

**Images:**
*   ![image-20250512-133122.png](blob:https://cognitedata.atlassian.net/7dd28343-837d-4e9e-92f7-d6b05f73c884#media-blob-url=true&id=7dd28343-837d-4e9e-92f7-d6b05f73c884&collection=contentId-5346295921&contextId=5346295921&width=1316&height=563&alt=image-20250512-133122.png)
*   ![image-20250512-133447.png](blob:https://cognitedata.atlassian.net/185827d8-0bda-4864-8ea3-9f9c329b0891#media-blob-url=true&id=185827d8-0bda-4864-8ea3-9f9c329b0891&collection=contentId-5346295921&contextId=5346295921&width=438&height=270&alt=image-20250512-133447.png)

### 2.4. Refinement by Asset Class Inheritance (Desired but Unsuccessful)

前述のようにアセットクラスはクラス (機種) に応じた詳細仕様を設定できるようにしたい。`FlocClass`をさらに継承した `FlocClassCompressor` というクラスを作り、これを割り当てると圧縮機用の仕様が入力でき、`FlocClassPump` というクラスを割り当てればポンプ用の仕様が入力できる、としたいところ。しかし残念ながら `FlocClassCompressor` を `Floc` に割り当てても `FlocClassCompressor` の`Property`は `Floc` に表示されませんでした。つまり、機種ごとで定義されるさまざまな`Property`について、すべてを `CogniteAssetClass` を継承した `FlocClass` というひとつのクラスに設定する必要があるのでしょうか？これは大きな課題と思われます。

### 2.5. Equipment Class Implementation (Not like Asset Class)

同様に `CogniteEquipment` を継承した `Equip` に対して `CogniteEquipmentType` を継承した `EquipType` を割り当てても、 `EquipType` に入力した`Property`は `Equip` では表示されませんでした。なぜでしょう。

**Images:**
*   ![image-20250512-132025.png](blob:https://cognitedata.atlassian.net/e14bb66c-b1e7-467d-b2c8-54c5fbf45156#media-blob-url=true&id=e14bb66c-b1e7-467d-b2c8-54c5fbf45156&collection=contentId-5346295921&contextId=5346295921&width=1316&height=547&alt=image-20250512-132025.png)
*   ![image-20250512-132138.png](blob:https://cognitedata.atlassian.net/082d1679-e1ea-472c-8cfe-11144c6b065d#media-blob-url=true&id=082d1679-e1ea-472c-8cfe-11144c6b065d&collection=contentId-5346295921&contextId=5346295921&width=1320&height=560&alt=image-20250512-132138.png)

---


## Deployment Packs Library

### Best Practices Library\Deployment Packs Library\Cause Map in Canvas with Atlas AI Agents Cognite Official  Cognite Hub.md

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

---

### Best Practices Library\Deployment Packs Library\Configuring the Infield QS Deployment Pack with the Toolkit  Cognite Hub.md

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

---

### Best Practices Library\Deployment Packs Library\Contextualization Quality Dashboard  Cognite Hub.md

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

---

### Best Practices Library\Deployment Packs Library\How To CDF Entity Matching Module Cognite Official  Cognite Hub.md

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

---

### Best Practices Library\Deployment Packs Library\How to Deploy RMDM v1 (Reliability Maintenance Data Model) in Cognite Data Fusion  Cognite Hub.md

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

---

### Best Practices Library\Deployment Packs Library\How to get started with CDF performance testing Cognite Official  Cognite Hub.md

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

---

### Best Practices Library\Deployment Packs Library\ISA Manufacturing Data Model Extension  Cognite Hub.md

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

---

### Best Practices Library\Deployment Packs Library\P&ID Annotation Workflow using CDF Data Modeling Cognite Official  Cognite Hub.md

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

---

### Best Practices Library\Deployment Packs Library\Root Cause Analysis (RCA) Agents Module  Cognite Hub.md

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

---

