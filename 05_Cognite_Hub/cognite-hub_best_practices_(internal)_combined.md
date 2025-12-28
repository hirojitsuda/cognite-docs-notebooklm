# 42_Knowledge Base - Best Practices (Internal)
Generated from: Obsidian Web Clipper (42_Knowledge Base)

================================================================================

<!-- SOURCE_START: Best Practices Library\Best Practices (Internal)\Best Practices - 2D Contextualization  Cognite Hub.md -->
## File: Best Practices Library\Best Practices (Internal)\Best Practices - 2D Contextualization  Cognite Hub.md

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

<!-- SOURCE_END: Best Practices Library\Best Practices (Internal)\Best Practices - 2D Contextualization  Cognite Hub.md -->

================================================================================

================================================================================

<!-- SOURCE_START: Best Practices Library\Best Practices (Internal)\Best Practices - Atlas AI for Quickstart  Cognite Hub.md -->
## File: Best Practices Library\Best Practices (Internal)\Best Practices - Atlas AI for Quickstart  Cognite Hub.md

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

<!-- SOURCE_END: Best Practices Library\Best Practices (Internal)\Best Practices - Atlas AI for Quickstart  Cognite Hub.md -->

================================================================================

================================================================================

<!-- SOURCE_START: Best Practices Library\Best Practices (Internal)\Best Practices - Data Modeling  Cognite Hub.md -->
## File: Best Practices Library\Best Practices (Internal)\Best Practices - Data Modeling  Cognite Hub.md

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

<!-- SOURCE_END: Best Practices Library\Best Practices (Internal)\Best Practices - Data Modeling  Cognite Hub.md -->

================================================================================

================================================================================

<!-- SOURCE_START: Best Practices Library\Best Practices (Internal)\Best Practices - Data Pipelines  Cognite Hub.md -->
## File: Best Practices Library\Best Practices (Internal)\Best Practices - Data Pipelines  Cognite Hub.md

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

<!-- SOURCE_END: Best Practices Library\Best Practices (Internal)\Best Practices - Data Pipelines  Cognite Hub.md -->

================================================================================

================================================================================

<!-- SOURCE_START: Best Practices Library\Best Practices (Internal)\Best Practices - Naming Conventions  Cognite Hub.md -->
## File: Best Practices Library\Best Practices (Internal)\Best Practices - Naming Conventions  Cognite Hub.md

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

<!-- SOURCE_END: Best Practices Library\Best Practices (Internal)\Best Practices - Naming Conventions  Cognite Hub.md -->

================================================================================

================================================================================

<!-- SOURCE_START: Best Practices Library\Best Practices (Internal)\Best Practices - Quickstart  Cognite Hub.md -->
## File: Best Practices Library\Best Practices (Internal)\Best Practices - Quickstart  Cognite Hub.md

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

<!-- SOURCE_END: Best Practices Library\Best Practices (Internal)\Best Practices - Quickstart  Cognite Hub.md -->

================================================================================

================================================================================

<!-- SOURCE_START: Best Practices Library\Best Practices (Internal)\設備階層機器仕様関連のデータモデリングの課題 - Cognite Japan - Confluence.md -->
## File: Best Practices Library\Best Practices (Internal)\設備階層機器仕様関連のデータモデリングの課題 - Cognite Japan - Confluence.md

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

<!-- SOURCE_END: Best Practices Library\Best Practices (Internal)\設備階層機器仕様関連のデータモデリングの課題 - Cognite Japan - Confluence.md -->

================================================================================

