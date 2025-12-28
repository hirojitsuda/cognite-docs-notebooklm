---
source: Cognite API Documentation
api_version: 20230101
api_docs_url: https://api-docs.cognite.com/20230101/
openapi_spec: openapi_20230101.json
exported_at: 2025-12-28T20:37:23.459419
---

# Cognite API

# Introduction
This is the reference documentation for the Cognite API with
an overview of all the available methods.

# Postman
Select the **Download** button to download our OpenAPI specification to get started.

To import your data into Postman, select **Import**, and the Import modal opens.
You can import items by dragging or dropping files or folders. You can choose how to import your API and manage the import settings in **View Import Settings**.

In the Import Settings, set the **Folder organization** to **Tags**, select
**Enable optional parameters** to turn off the settings, and select **Always inherit authentication** to turn on the settings. Select **Import**.

Set the Authorization to **Oauth2.0**. By default, the settings are for Open Industrial Data. Navigate to [Cognite Hub](https://hub.cognite.com/open-industrial-data-211) to understand how to get the credentials for use in Postman.

For more information, see [Getting Started with Postman](https://developer.cognite.com/dev/guides/postman/).

# Pagination
Most resource types can be paginated, indicated by the field `nextCursor` in the response.
By passing the value of `nextCursor` as the cursor you will get the next page of `limit` results.
Note that all parameters except `cursor` has to stay the same.

# Parallel retrieval
As general guidance, Parallel Retrieval is a technique that should be used when due to query complexity, retrieval of data in a single request is significantly slower than it would otherwise be for a simple request.  Parallel retrieval does not act as a speed multiplier on optimally running queries.  By parallelizing such requests, data retrieval performance can be tuned to meet the client application needs. 

CDF supports parallel retrieval through the `partition` parameter, which has the format `m/n` where `n` is the amount of partitions you would like to split the entire data set into.
If you want to download the entire data set by splitting it into 10 partitions, do the following in parallel with `m` running from 1 to 10:
  - Make a request to `/events` with `partition=m/10`.
  - Paginate through the response by following the cursor as explained above. Note that the `partition` parameter needs to be passed to all subqueries.

Processing of parallel retrieval requests is subject to concurrency quota availability. The request returns the `429` response upon exceeding concurrency limits. See the Request throttling chapter below.

To prevent unexpected problems and to maximize read throughput, you should at most use 10 partitions. 
Some CDF resources will automatically enforce a maximum of 10 partitions.
For more specific and detailed information, please read the ```partition``` attribute documentation for the CDF resource you're using.  

# Requests throttling
Cognite Data Fusion (CDF) returns the HTTP `429` (too many requests) response status code when project capacity exceeds the limit.

The throttling can happen:
  - If a user or a project sends too many (more than allocated) concurrent requests.
  - If a user or a project sends a too high (more than allocated) rate of requests in a given amount of time.

Cognite recommends using a retry strategy based on truncated exponential backoff to handle sessions with HTTP response codes 429.

Cognite recommends using a reasonable number (up to 10) of  `Parallel retrieval` partitions.

Following these strategies lets you slow down the request frequency to maximize productivity without having to re-submit/retry failing requests.

See more [here](https://developer.cognite.com/dev/concepts/request_throttling/).

# API versions
## Version headers
This API uses calendar versioning, and version names follow the `YYYYMMDD` format.
You can find the versions currently available by using the version selector at the top of this page.

To use a specific API version, you can pass the `cdf-version: $version` header along with your requests to the API.

## Beta versions
The beta versions provide a preview of what the stable version will look like in the future.
Beta versions contain functionality that is reasonably mature, and highly likely to become a part of the stable API.

Beta versions are indicated by a `-beta` suffix after the version name. For example, the beta version header for the
2023-01-01 version is then `cdf-version: 20230101-beta`.

## Alpha versions
Alpha versions contain functionality that is new and experimental, and not guaranteed to ever become a part of the stable API.
This functionality presents no guarantee of service, so its use is subject to caution.

Alpha versions are indicated by an `-alpha` suffix after the version name. For example, the alpha version header for
the 2023-01-01 version is then `cdf-version: 20230101-alpha`.

**API Version**: v1

## サーバー

- https://{cluster}.cognitedata.com/api/v1/projects/{project}
  - The URL for the CDF cluster to connect to

## API エンドポイント

### /3d/files/{threedFileId}

#### GET

**概要**: Retrieve a single 3D file



> **Required capabilities:** `threedAcl:READ`

Retrieve the contents of a 3D file. This applies to the output types 'ciff-processed', 'ciff-partially-processed' and 'node-property-metadata-json'.

This endpoint supports tag-based caching.

This endpoint is only compatible with 3D file IDs from the 3D API, and not compatible with
file IDs from the Files API.

**パラメータ**:

- `threedFileId` (integer, 必須)
  - The ID of the 3D file to retrieve.

**レスポンス**:

- `200`: Success
- `400`: 

---

### /3d/files/{threedFileId}/{subPath}

#### GET

**概要**: Retrieve a 3D directory file



> **Required capabilities:** `threedAcl:READ`

Retrieve the contents of a 3D file, for a blobId which is of a directory type.
This applies to the output types 'gltf-directory', 'reveal-directory', 'ept-pointcloud', 'tiles-directory', 'model-from-points'.

The 'subPath' elements, i.e. directory and/or file names, within each directory output type is subject to change and depends on each output type.
- The 'gltf-directory' output is used by Reveal v3+ for visualizing CAD files and contains a 'scene.json' file, which describes what other files are available within the blobId.
- The 'reveal-directory' output is used by Reveal v1-2 for visualizing CAD files and contains a 'scene.json' file, which describes what other files are available within the blobId.
- The 'ept-pointcloud' output is used by Reveal for visualizing point clouds, and contains a 'ept.json' file. The 'ept.json' file contains general information for the point cloud data. The file named 'ept-hierarchy/0-0-0-0.json' for the same blobId lists all the output point files which can be retrieved from a 'ept-data' folder for the same blobId, e.g. 'ept-data/0-0-0-0.bin'. The '.bin' files are in a custom point cloud format following the schema in the 'ept.json' file. Additionally, a 'filterOptions.json' file contains a description of which options were used when processing the point cloud.

Experimental outputs, normally not enabled:
The 'tiles-directory' output contains files for classification of the point cloud data. Retrieve the 'tiles.json' file from this output for a overview of the files within this output.
The 'model-from-points' output is used for storing a mesh based output file of some parts of the point cloud. This is stored as a 'model.ciff' file, in the Cognite internal file format.

This endpoint supports tag-based caching.

This endpoint is only compatible with 3D file IDs from the 3D API, and not compatible with
file IDs from the Files API.

**パラメータ**:

- `threedFileId` (integer, 必須)
  - The blob ID of the 3D output directory.
- `subPath` (string, 必須)
  - The filename for the 3D file to retrieve.

**レスポンス**:

- `200`: Success
- `400`: 

---

### /3d/job/result/rejections

#### POST

**概要**: Update Job Result Rejections

Job Instance Results have internal unique keys you can reject
to allow yourself to filter away job results items you are no longer
interested in

**リクエストボディ**:

The Job space and externalId and the ResultInstanceId with the operation you
want to perform on the result.

Only one operation can be done at the time:
- add
- remove

**レスポンス**:

- `200`: 
- `400`: 

---

### /3d/job/result/rejections/list

#### POST

**概要**: List Job Result Rejections

A list of internal keys that are to be considered rejected

**リクエストボディ**:

The Job space and externalId and the ResultInstanceId which you want the
rejections from

**レスポンス**:

- `200`: A List of rejected keys objects
- `400`: 

---

### /3d/job/results/list

#### POST

**概要**: List Job Results

List the results of a job

**リクエストボディ**:

The job space and externalId for the job you want results for

**レスポンス**:

- `200`: A list of results for the job
- `400`: 

---

### /3d/jobs

#### POST

**概要**: Create a job using 3D source data



> **Required capabilities:** `datamodels:READ(cdf_cdm, cdf_cdm_3d)` `datamodelinstances:WRITE`

Creates a job to be run using a 3D source. Currently supported in data-modeling projects only.
The user defines a dms instance they want the job to run against, and defines it as the source of the job.
Inside the job you define the space a job will exist in, and its external id, this is not populated into dms.
The job external id should be created by the client, and be unique.
If the job result is stored in dms, it will be stored using the space of the job.
A session nonce is required by the request.

Job types with supported job sources:
- Job Type: ImageTextDetection, Job Source type: Cognite360ImageCollection

**リクエストボディ**:

The job with a given source. Currently supports only one job to be created at the time.

**レスポンス**:

- `201`: A list of the created jobs
- `400`: 

---

### /3d/jobs/delete

#### POST

**概要**: Delete jobs



> **Required capabilities:** `datamodels:READ(cdf_cdm, cdf_cdm_3d)` `datamodelinstances:WRITE`

Delete jobs in your project. The job instance results won't be deleted by this operation.

**リクエストボディ**:

List of space and externalIds for jobs you want to delete

**レスポンス**:

- `200`: 
- `400`: 

---

### /3d/jobs/list

#### POST

**概要**: List jobs



> **Required capabilities:** `datamodels:READ(cdf_cdm, cdf_cdm_3d)` `datamodelinstances:READ`

List jobs for your project.

**リクエストボディ**:

A cursor for paginating the list of jobs.

**レスポンス**:

- `200`: A flattened list of jobs with their source
- `400`: 

---

### /3d/mappings/{assetId}/modelnodes

#### GET

**概要**: List mappings for one assetID across all 3D models



> **Required capabilities:** `threedAcl:READ`

Retrieves a list of `node IDs` from the hierarchy of all available 3D models which are mapped to the supplied `asset ID`.
If a `nodeId` is mapped to an `assetId` but is invalid or does not exist anymore, it will be omitted from the results.
An asset referenced in an asset mapping is not guaranteed to exist.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: A list of mappings between assets and 3D valid nodes and revisions in which exist
- `400`: 

---

### /3d/models

#### GET

**概要**: List 3D models



> **Required capabilities:** `threedAcl:READ`

Retrieves a list of all models in a project. This operation supports pagination. You can filter out all models without a published revision.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)
- `includeRevisionInfo` (boolean, 任意)
  - If true, return latest revision info as part of response
- `published` (boolean, 任意)
  - Filter based on whether or not it has published revisions.

**レスポンス**:

- `200`: A list of models.
- `400`: 

---

#### POST

**概要**: Create 3D models



> **Required capabilities:** `threedAcl:CREATE`


**リクエストボディ**:

The models to create.

**レスポンス**:

- `201`: A list of the created models.
- `400`: 

---

### /3d/models/delete

#### POST

**概要**: Delete 3D models



> **Required capabilities:** `threedAcl:DELETE`


**リクエストボディ**:

List of models to delete.

**レスポンス**:

- `200`: 
- `400`: 

---

### /3d/models/update

#### POST

**概要**: Update 3D models



> **Required capabilities:** `threedAcl:UPDATE`


**リクエストボディ**:

List of changes.

**レスポンス**:

- `200`: Corresponding models after applying the updates.
- `400`: 

---

### /3d/models/{modelId}

#### GET

**概要**: Retrieve a 3D model



> **Required capabilities:** `threedAcl:READ`


**パラメータ**:

- `` (unknown, 任意)

**レスポンス**:

- `200`: A model object
- `400`: 

---

### /3d/models/{modelId}/revisions

#### GET

**概要**: List 3D revisions



> **Required capabilities:** `threedAcl:READ`

Retrieves a list of all revisions of a model. This operation supports pagination. You can also filter revisions if they are marked as published or not by using the query param published.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)
- `published` (boolean, 任意)
  - Filter based on published status.

**レスポンス**:

- `200`: A list of revisions of the model.
- `400`: 

---

#### POST

**概要**: Create 3D revisions



> **Required capabilities:** `threedAcl:CREATE`

Creates revision(s) and starts processing job(s).

Check beta API documentation for information about additional options.

Hybrid projects: Use the CreateRevision3DClassicAndHybridBody to create revisions.

DataModelOnly: Please use the 3d/jobs endpoint (Beta for now) to create the revision(s) and start jobs. CreateRevision3DDmsOnlyBody will eventually be deprecated.


**パラメータ**:

- `` (unknown, 任意)

**リクエストボディ**:

The revisions to create.


**レスポンス**:

- `201`: A list of created revisions.
- `400`: 

---

### /3d/models/{modelId}/revisions/delete

#### POST

**概要**: Delete 3D revisions



> **Required capabilities:** `threedAcl:DELETE`


**パラメータ**:

- `` (unknown, 任意)

**リクエストボディ**:

List of revisions ids to delete.

**レスポンス**:

- `200`: 
- `400`: 

---

### /3d/models/{modelId}/revisions/update

#### POST

**概要**: Update 3D revisions



> **Required capabilities:** `threedAcl:UPDATE`


**パラメータ**:

- `` (unknown, 任意)

**リクエストボディ**:

List of changes.

**レスポンス**:

- `200`: Corresponding revisions after applying the updates.
- `400`: 

---

### /3d/models/{modelId}/revisions/{revisionId}

#### GET

**概要**: Retrieve a 3D revision



> **Required capabilities:** `threedAcl:READ`


**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: A revision object

---

### /3d/models/{modelId}/revisions/{revisionId}/logs

#### GET

**概要**: List 3D revision logs



> **Required capabilities:** `threedAcl:READ`

List log entries for the revision

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)
- `severity` (integer, 任意)

**レスポンス**:

- `200`: A list of log entries
- `400`: 

---

### /3d/models/{modelId}/revisions/{revisionId}/mappings

#### GET

**概要**: List 3D asset mappings



> **Required capabilities:** `threedAcl:READ`

Lists 3D assets mappings that match the ID for the specified filter query parameter type. Only one type of filter can be specified for each request, either `assetId`, `nodeId` or `intersectsBoundingBox`.

In Hybrid CDF projects: The nodeId and assetId mappings are returned by default.
To instead see any assetInstanceId mappings (for CoreDM based contextualization),
set the `getDmsInstances` query parameter to `true`.

In DataModelingOnly CDF projects: This endpoint is not available. Use DMS queries to list CogniteCADNodes.

Note: When filtering for a specific nodeId, only nodeIds that actually exists will be returned.
When filtering for a specific assetId, all mappings will be returned, even if the assetId does not exist.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)
- `nodeId` (integer, 任意)
- `assetId` (integer, 任意)
- `intersectsBoundingBox` (string, 任意)
  - Example: `{"min":[0.0, 0.0, 0.0], "max":[1.0, 1.0, 1.0]}`

If given, only return asset mappings for assets whose bounding box
intersects the given bounding box.

Must be a JSON object with `min`, `max` arrays of coordinates.

- `getDmsInstances` (boolean, 任意)
  - If true, the response will include any `assetInstanceId` instances for the mappings.


**レスポンス**:

- `200`: A list of mappings between assets and 3D nodes
- `400`: 

---

#### POST

**概要**: Create 3D asset mappings



> **Required capabilities:** `threedAcl:UPDATE`

Create asset mappings

Asset mappings allows connections to be made between assets and 3D nodes. This can be used to contextualize 3D models.

In Hybrid CDF projects: Asset IDs obtained from a mapping may be invalid, and not point to real asset IDs.
The assetId existence is not checked by any means by the 3D API.
The mappings will be stored until an `assetId` and `nodeId` pair is removed through the mappings delete endpoint.

In DataModelingOnly CDF projects: When creating asset mappings, the data model based assetInstanceId (with space and externalId),
and the 3d model revision nodeId, must be accessible, existing and valid.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)

**リクエストボディ**:

The asset mappings to create.

**レスポンス**:

- `201`: A list of created asset mappings.
- `400`: 

---

### /3d/models/{modelId}/revisions/{revisionId}/mappings/delete

#### POST

**概要**: Delete 3D asset mappings



> **Required capabilities:** `threedAcl:DELETE`

Delete a list of asset mappings.

In Hybrid CDF projects: The delete request items requires a `nodeId`, and either an `assetId` or an `assetInstanceId`.

In DataModelingOnly CDF projects: The delete request items requires a `nodeId` and an `assetInstanceId` (with space and externalId).

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)

**リクエストボディ**:

The IDs of the asset mappings to delete.

**レスポンス**:

- `200`: 
- `400`: 

---

### /3d/models/{modelId}/revisions/{revisionId}/mappings/list

#### POST

**概要**: Filter 3D asset mappings



> **Required capabilities:** `threedAcl:READ`

Lists 3D assets mappings that match the specified filter parameter.
Only one type of filter can be specified for each request, either `assetIds`, `nodeIds` or `treeIndexes`.

In Hybrid CDF projects: The nodeId and assetId mappings are returned by default.
To instead see only any `assetInstanceId` mappings (for CoreDM based contextualization),
set the `getDmsInstances` property to `true`, in the request body.

In DataModelingOnly CDF projects: This endpoint is not available. Use DMS queries to list CogniteCADNodes.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)

**リクエストボディ**:

The filter for asset mappings to get.

**レスポンス**:

- `200`: A list of matching asset mappings.
- `400`: 

---

### /3d/models/{modelId}/revisions/{revisionId}/nodes

#### GET

**概要**: List 3D nodes



> **Required capabilities:** `threedAcl:READ`

Retrieves a list of nodes from the hierarchy in the 3D model. You can also request a specific subtree with the 'nodeId' query parameter and limit the depth of the resulting subtree with the 'depth' query parameter. By default, nodes are returned in order of ascending treeIndex. We suggest trying to set the query parameter `sortByNodeId` to `true` to check whether it makes your use case faster. The `partition` parameter can only be used if `sortByNodeId` is set to `true`. This operation supports pagination. If the model revision is still being processed, you will get a HTTP status 400 when accessing nodes too early. Wait until the retrieve revision response returns "status":"Done" before calling nodes endpoints.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)
- `depth` (integer, 任意)
  - Get sub nodes up to this many levels below the specified node. Depth 0 is the root node.
- `nodeId` (integer, 任意)
  - ID of a node that are the root of the subtree you request (default is the root node).
- `sortByNodeId` (boolean, 任意)
  - Enable sorting by nodeId. When this parameter is `true`, nodes will be listed in order of ascending nodeId. Enabling this option will likely result in faster response for many requests.
- `properties` (string, 任意)
  - Example: `{"category1":{"property1":"value1"}}`

Filter for node properties. Only nodes that match all the given properties exactly will be listed.
The filter must be a JSON object with the same format as the `properties` field.


**レスポンス**:

- `200`: A list of nodes of a revision.
- `400`: 

---

### /3d/models/{modelId}/revisions/{revisionId}/nodes/byids

#### POST

**概要**: Get 3D nodes by ID



> **Required capabilities:** `threedAcl:READ`

Retrieves specific nodes given by a list of IDs.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)

**リクエストボディ**:

The request body containing the IDs of the nodes to retrieve. Will return error 400 if the revision is still being processed.

**レスポンス**:

- `200`: A list of nodes.
- `400`: 

---

### /3d/models/{modelId}/revisions/{revisionId}/nodes/list

#### POST

**概要**: Filter 3D nodes



> **Required capabilities:** `threedAcl:READ`

List nodes in a project, filtered by node names or node property values specified by supplied filters. This operation supports pagination and partitions. If the model revision is still being processed, you will get a HTTP status 400 when accessing nodes too early. Wait until the retrieve revision response returns "status":"Done" before calling nodes endpoints.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)

**リクエストボディ**:

**レスポンス**:

- `200`: A list of nodes satisfying the supplied node property filters.
- `400`: 

---

### /3d/models/{modelId}/revisions/{revisionId}/nodes/{nodeId}/ancestors

#### GET

**概要**: List 3D ancestor nodes



> **Required capabilities:** `threedAcl:READ`

Retrieves a list of ancestor nodes of a given node, including itself, in the hierarchy of the 3D model. This operation supports pagination. Will return error 400 if the revision is still being processed.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)
- `nodeId` (integer, 必須)
  - ID of the node to get the ancestors of.

**レスポンス**:

- `200`: A list of ancestor nodes.
- `400`: 

---

### /3d/models/{modelId}/revisions/{revisionId}/outputs

#### GET

**概要**: List available outputs



> **Required capabilities:** `threedAcl:READ`

Retrieve a list of available outputs for a processed 3D model. An output can be a format that can be consumed by a viewer (e.g. Reveal) or import in external tools. Each of the outputs will have an associated version which is used to identify the version of output format (not the revision of the processed output). Note that the structure of the outputs will vary and is not covered here.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)
- `format` (string, 任意)
  - Format identifier, e.g. 'ept-pointcloud' (point cloud). Well known formats are:
'ept-pointcloud' (point cloud data) or 'reveal-directory' (output supported by Reveal).
'all-outputs' can be used to retrieve all outputs for a 3D revision. Note that some of
the outputs are internal, where the format and availability might change without warning.


**レスポンス**:

- `200`: Returns a list of outputs and available versions per output for the given revision.

---

### /3d/models/{modelId}/revisions/{revisionId}/thumbnail

#### POST

**概要**: Update 3D revision thumbnail



> **Required capabilities:** `threedAcl:UPDATE`


**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)

**リクエストボディ**:

The request body containing the file ID of the thumbnail image (from Files API).

**レスポンス**:

- `200`: 
- `400`: 

---

### /3d/nodes/list

#### POST

**概要**: Filter which nodes are within a boundingbox for a revision.

This endpoint is only supported in dm-only projects.
The nodeIds can be found as part of a CogniteCadNode in the field `cadNodeReference`.
Similar functionality is supported in classic through:
- list 3D asset mappings endpoint + intersectsBoundingBox

**リクエストボディ**:

The Model, Revision, a list of nodes and an area definition

**レスポンス**:

- `200`: A list containing the nodes that are within the area definition for the given Model Revision
- `400`: 

---

### /ai/agents

### /ai/agents/byids

### /ai/agents/chat

### /ai/agents/delete

### /ai/chat/completions

### /ai/embeddings

### /ai/evals

### /ai/evals/byids

### /ai/evals/delete

### /ai/evals/metrics/correctness

### /ai/evals/runs

### /ai/evals/runs/byids

### /ai/evals/runs/delete

### /ai/services/availability

### /ai/tools/documents/ask

#### POST

**概要**: Ask questions about one or more documents.

This API endpoint uses a language model to answer questions about documents. Provided with a natural language question and a list of files, the API returns a multi-part answer with references to the locations in the documents that were used to build the answer.

### Limitations
- Currently, we only support PDF files.
- The PDF files can be a maximum of 400 pages long.
- You can only pass 100 files in a single request.

**パラメータ**:

- `content-type` (string, 必須)

**リクエストボディ**:

**レスポンス**:

- `200`: Successful Response
- `400`: 

---

### /ai/tools/documents/summarize

#### POST

**概要**: Summarize documents.

Generates concise summaries for a list of input documents using a language model.

**パラメータ**:

- `content-type` (string, 必須)

**リクエストボディ**:

**レスポンス**:

- `200`: Successful Response
- `400`: 

---

### /ai/tools/pythonsdk/completions

### /ai/tools/pythonsdk/edit

### /ai/tools/query/execute

### /ai/tools/query/generate

### /ai/usage/metrics/aggregate

### /annotations

#### POST

**概要**: Create annotations



> **Required capabilities:** `annotationsAcl:WRITE`

Creates the given annotations.

### Identifiers
An annotation _must_ reference an **annotated resource**.

The reference can be made by providing the internal ID of the annotated resource.

### Status
The annotation _must_ have the `status` field set to either "suggested", "rejected", or "approved"

### Access control
The caller _must_ have read-access on all the **annotated resources**, otherwise the call will fail.

### Annotation types and Data
The annotation **type** property _must_ be set to one of the globally available annotation types.
See the documentation of the `annotationType` and `data` attributes for details.

The annotation **data** _must_ conform to the schema provided by the annotation type.

### Creating Application and User
The creating application and its version _must_ be provided. The creating user _must_ be provided, but if the
annotation is being created by a service, this _can_ be set to `null`.

**リクエストボディ**:

**レスポンス**:

- `201`: 
- `400`: 

---

### /annotations/byids

#### POST

**概要**: Retrieve annotations

Retrieves the referenced annotations.

The caller _must_ have read-access on all the **annotated resources**, otherwise the call will fail.

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 

---

### /annotations/delete

#### POST

**概要**: Delete annotations



> **Required capabilities:** `annotationsAcl:WRITE`

Deletes the referenced annotations completely.

The caller _must_ have read-access on all the **annotated resources**, otherwise the call will fail.

**リクエストボディ**:

**レスポンス**:

- `200`: Successful deletion
- `400`: 

---

### /annotations/list

#### POST

**概要**: Filter annotations

Lists the annotations the caller has access to, based on a filter.

The caller _must_ have read-access on the **annotated resources** listed in the filter, otherwise the call will
fail.

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 

---

### /annotations/suggest

#### POST

**概要**: Suggest annotations



> **Required capabilities:** `annotationsAcl:SUGGEST`

Suggests the given annotations, i.e. creates them with `status` set to "suggested"

### Identifiers
An annotation _must_ reference an **annotated resource**.

The reference can be made by providing the internal ID of the annotated resource.

### Access control
The caller _must_ have read-access on all the **annotated resources**, otherwise the call will fail.

### Annotation types and Data
The annotation **type** property _must_ be set to one of the globally available annotation types.
See the documentation of the `annotationType` and `data` attributes for details.

The annotation **data** _must_ conform to the schema provided by the annotation type.

### Creating Application and User
The creating application and its version _must_ be provided. The creating user _must_ be provided, but if the
annotation is being created by a service, this _can_ be set to `null`.

**リクエストボディ**:

**レスポンス**:

- `201`: 
- `400`: 

---

### /annotations/update

#### POST

**概要**: Update annotations



> **Required capabilities:** `annotationsAcl:WRITE`

Updates the referenced annotations.

The caller _must_ have read-access on all the **annotated resources**, otherwise the call will fail.

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 

---

### /annotations/{annotationId}

#### GET

**概要**: Get an annotation

Retrieves the referenced annotation.

The caller _must_ have read-access on the **annotated resource**, otherwise the call will fail.

**パラメータ**:

- `` (unknown, 任意)

**レスポンス**:

- `200`: 
- `400`: 

---

### /api/v0/orgs/{org}/projects

### /api/v1/orgs/{org}

#### GET

**概要**: Retrieve an organization

Retrieve an organization by its ID.

#### Access control
Requires the caller to be an admin in the organization, or any of its ancestors.

_Example:_ Assume an organization hierarchy like: `org-a` -> `org-b` -> `org-c`.
To retrieve `org-c`, which means calling 'GET /api/v1/orgs/org-c', the caller must be an admin in `org-a`, `org-b` or
`org-c`.

**パラメータ**:

- `` (unknown, 任意)

**レスポンス**:

- `200`: 

---

### /api/v1/orgs/{org}/delete

#### POST

**概要**: Delete an organization

Delete an organization. Users will be locked out of the organization immediately. This also applies to the caller.

The organization cannot contain sub-organizations or projects at the time of deletion.

This is a soft-delete, so Cognite Support can restore the organization in case of accidents.

#### Access control
Requires the caller to be an admin in the organization, or any of its ancestors.

_Example:_ Assume an organization hierarchy like: `org-a` -> `org-b` -> `org-c`.
To delete `org-c`, which means calling 'POST /api/v1/orgs/org-c/delete', the caller must be an admin in `org-a`, `org-b` or
`org-c`.

**パラメータ**:

- `` (unknown, 任意)

**レスポンス**:

- `200`: Successful deletion

---

### /api/v1/orgs/{org}/orgs

#### GET

**概要**: List child organizations

List all child organizations under the specified parent organization.

#### Access control
Requires the caller to be an admin in the parent organization (i.e. the one on the path), or any of its ancestors.

_Example:_ Assume an organization hierarchy like: `org-a` -> `org-b` -> `org-c`.
To list child organizations under `org-b`, which means calling 'GET /api/v1/orgs/org-b/orgs', the caller must be an
admin in `org-a` or `org-b`.

**パラメータ**:

- `` (unknown, 任意)

**レスポンス**:

- `200`: 

---

#### POST

**概要**: Create an organization

Create a child organization under the specified organization.

#### Access control
Requires the caller to be an admin in the parent organization (i.e. the one on the path), or any of its ancestors.
In addition, the flag `adminsCanCreateOrgsInSubtree` must be set to `true` in that organization.

_Example:_ Assume an organization hierarchy like: `org-a` -> `org-b` -> `org-c`.
To create a new organization under `org-c`, which means calling 'POST /api/v1/orgs/org-c/orgs', the caller must be an
admin in at least one of `org-a`, `org-b`, or `org-c`, and `adminsCanCreateOrgsInSubtree` must be `true` in that same organization.

**パラメータ**:

- `` (unknown, 任意)

**リクエストボディ**:

**レスポンス**:

- `201`: 
- `403`: Creation forbidden: The caller is not allowed to create sub-organizations.

---

### /api/v1/orgs/{org}/principals

#### GET

**概要**: List principals

List principals in an organization.

All principal types are supported.

#### Access control
Requires the caller to be logged into the target organization, or
to be an admin in any of its the ancestor organizations.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: 

---

### /api/v1/orgs/{org}/principals/byids

#### POST

**概要**: Retrieve principals by reference

Retrieve principals in an organization by ID or external ID.

Service accounts can be retrieved by ID or external ID.
Users can be retrieved by ID.

#### Access control
Requires the caller to be logged into the target organization, or
to be an admin in any of its the ancestor organizations.

**パラメータ**:

- `` (unknown, 任意)

**リクエストボディ**:

**レスポンス**:

- `200`: 

---

### /api/v1/orgs/{org}/principals/{principal}/sessions

#### GET

**概要**: List login sessions

List login sessions for a user principal in an organization.

Note that a login session is what a user gets after logging into Fusion, InField, or another Cognite
application. It is distinct from the concept of the "sessions" that can be used for background work like
Transformations or Functions; however, each such background session is backed by a login session.

This endpoint does not work for service account principals, which do not have login sessions.

#### Access control
Requires the caller to be an admin of the target organization (it is not sufficient to be an admin of an
ancestor organization).

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: 
- `400`: 
- `403`: 
- `404`: 

---

### /api/v1/orgs/{org}/principals/{principal}/sessions/revoke

#### POST

**概要**: Revoke login sessions

Revoke one or more login sessions for a user principal in an organization.

Note that a login session is what a user gets after logging into Fusion, InField, or another Cognite
application. It is distinct from the concept of the "sessions" that can be used for background work like
Transformations or Functions; however, each such background session is backed by a login session.

Upon successful completion of a login flow, the user receives an access token and a refresh token, which are
both associated with the newly established login session. (Note that the user may have multiple concurrent
login sessions.) The user may use the access token to send requests to the Cognite API, and the refresh token
to obtain a new access token before the current one expires. Refreshing will also produce a new refresh token
and invalidate the previous one. Every access token and refresh token in this "chain" is associated with the
same login session.

Revoking a login session will set its `status` to `REVOKED` (which will be reflected in
`/api/v1/orgs/{org}/principals/{principal}/sessions`), which within a short amount of time will prevent the user
from accessing the Cognite systems via that login session. Specifically:
- Requests to the Cognite API that use any access token that is associated with this session will be rejected.
- Requests to refresh the login session will be rejected.
- Background jobs that were started from this login session will stop working. Note that this might take up to
  one hour to take effect.

Note that the user may still have other active login sessions, which will remain active. If you want to
completely prevent a user from accessing Cognite systems, you should remove them from your organization's
external identity provider (e.g. your Entra ID tenant), which will prevent them from logging in again, and then
call `/api/v1/orgs/{org}/principals/{principal}/sessions` to list all their current login sessions and then pass
all their ids to this endpoint (in batches, if there are more than 10) to revoke them.

Also note that because the `REVOKED` status is "stronger" than the `LOGGED_OUT` status (background sessions
remain active in the latter status but not in the former), it is possible to revoke a login session that is
`LOGGED_OUT` (which will stop any associated background sessions). Currently, `REVOKED` has the same
consequences as `EXPIRED`, but revocation might be expanded in the future to become even stronger, so it is also
possible to revoke a login session that is `EXPIRED`. Revoking a login session that is already `REVOKED` will
succeed but will have no effect (in particular, the `deactivatedTime` of the login session will not change).

This endpoint is atomic: if revocation of any of the given login sessions fails, none of the login sessions will
be revoked. In other words, any other status code than 204 means that no login sessions were revoked.

This endpoint does not work for service account principals, which do not have login sessions.

#### Access control
Requires the caller to be an admin of the target organization (it is not sufficient to be an admin of an
ancestor organization).

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)

**リクエストボディ**:

**レスポンス**:

- `204`: Login sessions revoked successfully.
- `400`: 
- `403`: 
- `404`: 

---

### /api/v1/orgs/{org}/projects

#### GET

**概要**: List projects in an organization

List all projects in the specified organization.

#### Access control
If `includeAdminProperties` is true, it requires the caller to be an admin in the organization, or any of its ancestors.
Otherwise, it requires the caller to be a member of the organization.

_Example:_ Assume an organization hierarchy like: `org-a` -> `org-b` -> `org-c`.
To list projects in `org-c`, which means calling 'GET /api/v1/orgs/org-c/projects?includeAdminProperties',
the caller must be an admin in `org-a`, `org-b` or `org-c`.

**パラメータ**:

- `` (unknown, 任意)
- `includeAdminProperties` (boolean, 任意)
  - Whether to include admin properties of the projects in the response.

**レスポンス**:

- `200`: 

---

#### POST

**概要**: Create a project

Create a project in the specified organization, in the given cluster. The project is immediately available
for login in Cognite applications, for example through Cognite Data Fusion.

#### Cluster placement
The chosen cluster must be one of the allowed clusters for the organization.

The project cannot be moved to a different cluster after creation, so make sure to choose the correct one
with respect to data locality requirements.

#### Project OIDC configuration
The OIDC configuration will be copied from the immediate parent organization.

The caller can, and should, set an initial admin group ID for the project. That group is managed by the external
identity provider, as for the organization.
If that group is not set, the project will inherit the admin group ID of the organization it's created in.
See the `projectAdminGroupId` field in the request body for more details on this behavior.

#### Access control
Requires the caller to be an admin in the organization, or any of its ancestors.
In addition, the flag `adminsCanCreateProjectsInSubtree` must be set to `true` in that organization.

_Example:_ Assume an organization hierarchy like: `org-a` -> `org-b` -> `org-c`.
To create a project in `org-c`, which means calling 'POST /api/v1/orgs/org-c/projects', the caller must be an admin in
`org-a`, `org-b` or `org-c`.
Also, `adminsCanCreateProjectsInSubtree` must be `true` in that same organization.

**パラメータ**:

- `` (unknown, 任意)

**リクエストボディ**:

**レスポンス**:

- `201`: 
- `403`: Creation forbidden: The caller is not allowed to create projects.

---

### /api/v1/principals/me

#### GET

**概要**: Get the current caller's information

Retrieves the information of the principal issuing the request.

This endpoint is useful for clients to verify their own identity.
All authenticated principals can call this endpoint.

**レスポンス**:

- `200`: 

---

### /api/v1/projects/{project}/diagram-parsing/parsing/full

#### POST

**概要**: Run parsing with tag detection for files



> **Required capabilities:** `DiagramParsingAcl:WRITE` `DataModelInstancesAcl:WRITE`

A parsing job will be started on the files to detect their entities and connections and map assets.

**パラメータ**:

- `` (unknown, 任意)

**リクエストボディ**:

Defines parsing options needed for symbol and tag detection jobs.

**レスポンス**:

- `200`: 
- `400`: 

---

### /assets

#### GET

**概要**: List assets



> **Required capabilities:** `assetsAcl:READ`

List all assets, or only the assets matching the specified query.

### Request throttling
This endpoint is meant for data analytics/exploration usage and is not suitable for high load data retrieval usage.
It is a subject of the new throttling schema (limited request rate and concurrency).
Please check [Assets resource description](../../) for more information.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)
- `name` (unknown, 任意)
- `parentIds` (unknown, 任意)
  - List only assets that have one of the parentIds as a parent. The parentId for root assets is null.
- `parentExternalIds` (unknown, 任意)
  - List only assets that have one of the parentExternalIds as a parent. The parentId for root assets is null.
- `rootIds` (unknown, 任意)
  - This parameter is deprecated. Use assetSubtreeIds instead. List only assets that have one of the rootIds as a root asset. A root asset is its own root asset.
- `assetSubtreeIds` (unknown, 任意)
  - List only assets that are in a subtree rooted at any of these assetIds (including the roots given).  If the total size of the given subtrees exceeds 100,000 assets, an error will be returned.
- `assetSubtreeExternalIds` (unknown, 任意)
  - List only assets that are in a subtree rooted at any of these assetExternalIds. If the total size of the given subtrees exceeds 100,000 assets, an error will be returned.
- `source` (string, 任意)
- `root` (boolean, 任意)
- `minCreatedTime` (unknown, 任意)
- `maxCreatedTime` (unknown, 任意)
- `minLastUpdatedTime` (unknown, 任意)
- `maxLastUpdatedTime` (unknown, 任意)
- `externalIdPrefix` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: 
- `400`: 
- `429`: 

---

#### POST

**概要**: Create assets



> **Required capabilities:** `assetsAcl:WRITE`

Create multiple asset objects in the same project.
It is possible to create a maximum of 1000 assets per request.

### Request throttling
This endpoint is a subject of the new throttling schema (limited request rate and concurrency).
Please check [Assets resource description](../../) for more information.

**リクエストボディ**:

List of the assets to create. You can create a maximum of 1000 assets per request.

**レスポンス**:

- `201`: 
- `400`: 
- `429`: 

---

### /assets/aggregate

#### POST

**概要**: Aggregate assets



> **Required capabilities:** `assetsAcl:READ`

The aggregation API lets you compute aggregated results on assets,
such as getting the count of all assets in a project, checking
different names and descriptions of assets in your project, etc.

#### Aggregate filtering
##### Filter (filter & advancedFilter) data for aggregates
Filters behave the same way as for the `Filter assets` endpoint.
In text properties, the values are aggregated in a case-insensitive manner.

##### aggregateFilter to filter aggregate results
`aggregateFilter` works similarly to `advancedFilter` but always applies to aggregate properties.
For instance, in case of an aggregation for the `source` property, only the values (aka buckets) of the `source` property can be filtered out.

### Request throttling
This endpoint is meant for data analytics/exploration usage and is not suitable for high load data retrieval usage.\
It is a subject of the new throttling schema (limited request rate and concurrency).
Please check [Assets resource description](../../) for more information.

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 
- `429`: 

---

### /assets/byids

#### POST

**概要**: Retrieve assets



> **Required capabilities:** `assetsAcl:READ`

Retrieve assets by IDs or external IDs.
If you specify to get aggregates, then be aware that the aggregates are eventually consistent.

### Request throttling
This endpoint is a subject of the new throttling schema (limited request rate and concurrency).
Please check [Assets resource description](../../) for more information.

**リクエストボディ**:

All provided IDs and external IDs must be unique.

**レスポンス**:

- `200`: 
- `400`: 
- `429`: 

---

### /assets/delete

#### POST

**概要**: Delete assets



> **Required capabilities:** `assetsAcl:WRITE`

Delete assets. By default, `recursive=false` and the request would fail if attempting to delete assets that are referenced as parent by other assets.
To delete such assets and all its descendants, set recursive to true.
The limit of the request does not include the number of descendants that are deleted.

### Request throttling
This endpoint is a subject of the new throttling schema (limited request rate and concurrency).
Please check [Assets resource description](../../) for more information.

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 
- `429`: 

---

### /assets/list

#### POST

**概要**: Filter assets



> **Required capabilities:** `assetsAcl:READ`

Retrieve a list of assets in the same project. This operation supports pagination by cursor.
Apply Filtering and Advanced filtering criteria to select a subset of assets.

### Advanced filtering
Advanced filter lets you create complex filtering expressions that combine simple operations,
such as `equals`, `prefix`, `exists`, etc., using boolean operators `and`, `or`, and `not`.
It applies to basic fields as well as metadata.

See the `advancedFilter` attribute in the example.

See more information about filtering DSL [here](https://docs.cognite.com/dev/concepts/resource_filtering_dsl/ "filtering DSL").

#### Supported leaf filters

| Leaf filter    | Supported fields                   | Description  |
|----------------|------------------------------------|--------------|
| `containsAll`  | Array type fields                  | Only includes results which contain all of the specified values. <br /> `{"containsAll": {"property": ["property"], "values": [1, 2, 3]}}` |
| `containsAny`  | Array type fields                  | Only includes results which contain at least one of the specified values. <br /> `{"containsAny": {"property": ["property"], "values": [1, 2, 3]}}` |
| `equals`       | Non-array type fields              | Only includes results that are equal to the specified value. <br /> `{"equals": {"property": ["property"], "value": "example"}}` |
| `exists`       | All fields                         | Only includes results where the specified property exists (has value). <br /> `{"exists": {"property": ["property"]}}` |
| `in`           | Non-array type fields              | Only includes results that are equal to one of the specified values. <br /> `{"in": {"property": ["property"], "values": [1, 2, 3]}}` |
| `prefix`       | String type fields                 | Only includes results which start with the specified value. <br /> `{"prefix": {"property": ["property"], "value": "example"}}` |
| `range`        | Non-array type fields              | Only includes results that fall within the specified range. <br /> `{"range": {"property": ["property"], "gt": 1, "lte": 5}}` <br /> Supported operators: `gt`, `lt`, `gte`, `lte` |
| `search`       | `["name"]`, `["description"]`      | Introduced to provide functional parity with /assets/search endpoint. <br /> `{"search": {"property": ["property"], "value": "example"}}` |

##### Search
The `search` leaf filter provides functional parity with the `/assets/search` endpoint.
It's available only for the `["description"]` and `["name"]` properties. When specifying only this filter with no explicit ordering,
behavior is the same as of the `/assets/search/` endpoint without specifying filters.
Explicit sorting overrides the default ordering by relevance.
It's possible to use the `search` leaf filter as any other leaf filter for creating complex queries.

See the `search` filter in the `advancedFilter` attribute in the example.

#### advancedFilter attribute limits
- filter query max depth: 10
- filter query max number of clauses: 100
- `and` and `or` clauses must have at least one element
- `property` array of each leaf filter has the following limitations:
  - number of elements in the array is in the range [1, 2]
  - elements must not be blank
  - each element max length is 128 symbols
  - property array must match one of the existing properties (static or dynamic metadata).
- `containsAll`, `containsAny`, and `in` filter `values` array size must be in the range [1, 100]
- `containsAll`, `containsAny`, and `in` filter `values` array must contain elements of a primitive type (number, string)
- `range` filter must have at least one of `gt`, `gte`, `lt`, `lte` attributes.
  But `gt` is mutually exclusive to `gte`, while `lt` is mutually exclusive to `lte`.
  At least one of the bounds must be specified.
- `gt`, `gte`, `lt`, `lte` in the `range` filter must be a primitive value
- `search` filter `value` must not be blank and the length must be in the range [1, 128]
- filter query may have maximum 2 search leaf filters
- maximum leaf filter string value length is different depending on the property the filter is using:
  - `externalId` - 255
  - `name` - 128 for the `search` filter and 255 for other filters
  - `description` - 128 for the `search` filter and 255 for other filters
  - `labels` item - 255
  - `source` - 128
  - any `metadata` key - 128

### Sorting
By default, assets are sorted by `id` in the ascending order.
Use the `search` leaf filter to sort the results by relevance.
Sorting by other fields can be explicitly requested. The `order` field is optional
and defaults to `desc` for `_score_` and `asc` for all other fields.
The `nulls` field is optional and defaults to `auto`. `auto` is translated to
`last` for the `asc` order and to `first` for the `desc` order by the service.
Partitions are done independently of sorting; there's no guarantee of the sort order between elements from different partitions.

See the `sort` attribute in the example.

#### Null values
In case the `nulls` attribute has the `auto` value or the attribute isn't specified,
null (missing) values are considered to be bigger than any other values.
They are placed last when sorting in the `asc` order and first when sorting in `desc`.
Otherwise, missing values are placed according to the `nulls` attribute (last or first), and their placement doesn't depend on the `order` value.
Values, such as empty strings, aren't considered as nulls.

#### Sorting by score
Use a special sort property `_score_` when sorting by relevance.
The more filters a particular asset matches, the higher its score is. This can be useful,
for example, when building UIs. Let's assume we want exact matches to be be displayed above matches by
prefix as in the request below. An asset named `pump` will match both `equals` and `prefix`
filters and, therefore, have higher score than assets with names like `pump valve` that match only `prefix` filter.

```
"advancedFilter" : {
  "or" : [
    {
      "equals": {
        "property": ["name"],
        "value": "pump"
      }
    },
    {
      "prefix": {
        "property": ["name"],
        "value": "pump"
      }
    }
  ]
},
"sort": [
  {
    "property" : ["_score_"]
  }
]
```

### Request throttling
This endpoint is meant for data analytics/exploration usage and is not suitable for high load data retrieval usage.
It is a subject of the new throttling schema (limited request rate and concurrency).
Please check [Assets resource description](../../) for more information.

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 
- `429`: 

---

### /assets/search

#### POST

**概要**: Search assets



> **Required capabilities:** `assetsAcl:READ`

Fulltext search for assets based on result relevance. Primarily meant
for human-centric use-cases, not for programs, since matching and
ordering may change over time. Additional filters can also be
specified. This operation doesn't support pagination.

### Request throttling
This endpoint is meant for data analytics/exploration usage and is not suitable for high load data retrieval usage.
It is a subject of the new throttling schema (limited request rate and concurrency).
Please check [Assets resource description](../../) for more information.

**リクエストボディ**:

Search query

**レスポンス**:

- `200`: 
- `400`: 
- `429`: 

---

### /assets/update

#### POST

**概要**: Update assets



> **Required capabilities:** `assetsAcl:WRITE`

Update the attributes of assets.

### Request throttling
This endpoint is a subject of the new throttling schema (limited request rate and concurrency).
Please check [Assets resource description](../../) for more information.

**リクエストボディ**:

All provided IDs and external IDs must be unique. Fields that aren't included in the request aren't changed.

**レスポンス**:

- `200`: 
- `400`: 
- `429`: 

---

### /assets/{id}

#### GET

**概要**: Retrieve an asset by its ID



> **Required capabilities:** `assetsAcl:READ`

Retrieve an asset by its internal ID. If you want to retrieve assets by externalIds, use Retrieve assets instead.

### Request throttling
This endpoint is a subject of the new throttling schema (limited request rate and concurrency).
Please check [Assets resource description](../../) for more information.

**パラメータ**:

- `` (unknown, 任意)

**レスポンス**:

- `200`: 
- `400`: 
- `429`: 

---

### /context/diagram/convert

#### POST

**概要**: Convert a diagram to image format



> **Required capabilities:** `assetsAcl:READ` `filesAcl:READ`

Convert interactive engineering diagrams to image format, with highlighted annotations.
Supported input file mime_types are application/pdf, image/jpeg, image/png, image/tiff.
Supported output image formats are PNG and SVG, only the svg embeds the input annotations.

**リクエストボディ**:

**レスポンス**:

- `200`: Success
- `400`: 

---

### /context/diagram/convert/{jobId}

#### GET

**概要**: Get the results for converting an engineering diagram to an image



> **Required capabilities:** `assetsAcl:READ` `filesAcl:READ`

Get the results for converting an engineering diagram to SVG and PNG formats.

**パラメータ**:

- `` (unknown, 任意)

**レスポンス**:

- `200`: Success
- `400`: 

---

### /context/diagram/detect

#### POST

**概要**: Detect annotations in engineering diagrams



> **Required capabilities:** `assetsAcl:READ` `filesAcl:READ`

Detect annotations in engineering diagrams. Note: All users in a CDF project with assets read-all and files read access to the requested files can access data sent to this endpoint.
Supported input file mime_types are application/pdf, image/jpeg, image/png, image/tiff. Also note that the header of a successful response contains an `X-Job-Token` which allows to fetch the result of the
job at `/context/diagram/detect/{jobId}` without requiring 'assetsAcl:READ'.

**リクエストボディ**:

**レスポンス**:

- `200`: Success
- `400`: 

---

### /context/diagram/detect/{jobId}

#### GET

**概要**: Retrieve engineering diagram detect results



> **Required capabilities:** `assetsAcl:READ` `filesAcl:READ`

Get the results from an engineering diagram detect job. Providing the `X-Job-Token` header returned by the
`/context/diagram/detect` endpoint is required unless the caller has read access to all assets. Results are available for 30 days following the completion of the detect job.

**パラメータ**:

- `` (unknown, 任意)

**レスポンス**:

- `200`: Success
- `400`: 

---

### /context/documentparser/jobs

### /context/documentparser/jobs/byids

### /context/documentparser/jobs/start

### /context/documentparser/jobs/write

#### POST

**概要**: Write Results to Data Models (INTERNAL)

Write results to data models.


**リクエストボディ**:

**レスポンス**:

- `200`: Results written to data models successfully.

---

### /context/documentparser/{jobId}

### /context/entitymatching

#### GET

**概要**: List entity matching models



> **Required capabilities:** `entitymatchingAcl:READ`

List all available entity matching models.

**パラメータ**:

- `limit` (integer, 任意)
  - Limits the number of results to be returned. The maximum results returned by the server is 1000 even if you specify a higher limit.

**レスポンス**:

- `200`: Success
- `400`: 

---

#### POST

**概要**: Create entity matcher model



> **Required capabilities:** `entitymatchingAcl:WRITE`

Train a model that predicts matches between entities (for example, time series names to asset names). This is also known as fuzzy joining. If there are no trueMatches (labeled data), you train a static (unsupervised) model, otherwise a machine learned (supervised) model is trained.

**リクエストボディ**:

**レスポンス**:

- `200`: Success
- `400`: 

---

### /context/entitymatching/byids

#### POST

**概要**: Retrieve entity matching models



> **Required capabilities:** `entitymatchingAcl:READ`

Retrieve entity matching models by IDs or external IDs.

**リクエストボディ**:

**レスポンス**:

- `200`: Success
- `400`: 

---

### /context/entitymatching/delete

#### POST

**概要**: Delete entity matcher model



> **Required capabilities:** `entitymatchingAcl:WRITE`

Deletes an entity matching model. Currently, this is a soft delete, and only removes the entry from listing.

**リクエストボディ**:

**レスポンス**:

- `200`: Models deleted.
- `400`: 

---

### /context/entitymatching/jobs/{jobId}

#### GET

**概要**: Retrieve entity matcher predict results



> **Required capabilities:** `assetsAcl:READ` `entitymatchingAcl:WRITE`

Get the results from a predict job. Note: 'assetsAcl:READ' capability is required, unless you specify a valid `X-Job-Token` in the request header. The `X-Job-Token` is provided in the response header of the initial call to `/context/entitymatching/predict`

**パラメータ**:

- `` (unknown, 任意)
- `X-Job-Token` (unknown, 任意)

**レスポンス**:

- `200`: Success
- `400`: 

---

### /context/entitymatching/list

#### POST

**概要**: Filter models



> **Required capabilities:** `entitymatchingAcl:READ`

Use filtering options to find entity matcher models.

**リクエストボディ**:

**レスポンス**:

- `200`: Success
- `400`: 

---

### /context/entitymatching/predict

#### POST

**概要**: Predict matches



> **Required capabilities:** `entitymatchingAcl:WRITE`

Predicts entity matches using a trained model. Note: 'assetsAcl:READ' capability is required unless both `sources` and `targets` are specified in the request. Also note that the header of a successful response contains a `X-Job-Token` which allows to fetch the result of the job at `/context/entitymatching/jobs/{jobId}` without requiring 'assetsAcl:READ'.

**リクエストボディ**:

**レスポンス**:

- `200`: Success
- `400`: 

---

### /context/entitymatching/refit

#### POST

**概要**: Re-fit entity matcher model



> **Required capabilities:** `entitymatchingAcl:READ` `entitymatchingAcl:WRITE`

Creates a new model by re-training an existing model on existing data but with additional true matches. The old model is not changed. The new model gets a new id and new external id if `newExternalId` is set, or no external id if `newExternalId` is not set. Use for efficient re-training of the model after a user creates additional confirmed matches.

**リクエストボディ**:

**レスポンス**:

- `200`: Success
- `400`: 

---

### /context/entitymatching/update

#### POST

**概要**: Update entity matching models



> **Required capabilities:** `entitymatchingAcl:WRITE`

Update entity matching models by IDs or external IDs.

**リクエストボディ**:

**レスポンス**:

- `200`: Success
- `400`: 

---

### /context/entitymatching/{id}

#### GET

**概要**: Retrieve an entity matching model by the ID of the model



> **Required capabilities:** `entitymatchingAcl:READ`

Shows the status of the model. If the status is completed, shows the parameters used to train the model.

**パラメータ**:

- `` (unknown, 任意)

**レスポンス**:

- `200`: Success
- `400`: 

---

### /context/vision/extract

#### POST

**概要**: Extract features from images



> **Required capabilities:** `filesAcl:READ`

Start an asynchronous prediction job for extracting features such as text, asset tags or industrial objects from images.
The response of the POST request contains a job ID, which can be used to make subsequent (GET) calls
to check the status and retrieve the results of the job
(see [Retrieve results from a feature extraction job](getVisionExtract)).

It is possible to have up to 20 concurrent jobs per CDF project.

The files referenced by `items` in the request body must fulfill the following requirements:

* Must have file extension: `.jpeg`, `.jpg` or `.png`
* Must have `image/png` or `image/jpeg` as `mimeType`

New feature extractors may be added in the future.


**パラメータ**:

- `` (unknown, 任意)

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 

---

### /context/vision/extract/{jobId}

#### GET

**概要**: Retrieve results from a feature extraction job on images



> **Required capabilities:** `filesAcl:READ`

Retrieve results from a feature extraction job on images.

Note that since files are split up into batches and processed independently of each other, the items in successfully completed batches will be returned even if files in other batches are still being processed. The job status will be `Running` until all batches have been processed. If one of the items in a batch fails, the results from items in other completed batches will still be returned. The corresponding items and error message(s) of failed batches will be populated in `failedItems`.
Additionally, the status of the job is set to `Completed` if at least one batch is successfully completed, otherwise the status is set to `Failed`.


**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: 
- `400`: 

---

### /context/vision/segment

### /dataproducts

### /dataproducts/delete

### /dataproducts/update

### /dataproducts/{externalId}

### /dataproducts/{externalId}/versions

### /dataproducts/{externalId}/versions/delete

### /dataproducts/{externalId}/versions/update

### /dataproducts/{externalId}/versions/{versionId}

### /datasets

#### POST

**概要**: Create data sets



> **Required capabilities:** `datasetsAcl:WRITE`

You can create a maximum of 10 data sets per request.

**リクエストボディ**:

List of the data sets to create.

**レスポンス**:

- `200`: 
- `400`: 

---

### /datasets/aggregate

#### POST

**概要**: Aggregate data sets



> **Required capabilities:** `datasetsAcl:READ`

Aggregate data sets in the same project. Criteria can be applied to select a subset of data sets.

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 

---

### /datasets/byids

#### POST

**概要**: Retrieve data sets



> **Required capabilities:** `datasetsAcl:READ`

Retrieve data sets by IDs or external IDs.

**リクエストボディ**:

List of the IDs of the data sets to retrieve. You can retrieve a maximum of 1000 data sets per request. All IDs must be unique.

**レスポンス**:

- `200`: 
- `400`: 

---

### /datasets/list

#### POST

**概要**: Filter data sets



> **Required capabilities:** `datasetsAcl:READ`

Use advanced filtering options to find data sets.

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 

---

### /datasets/update

#### POST

**概要**: Update data sets.



> **Required capabilities:** `datasetsAcl:WRITE`


**リクエストボディ**:

All provided IDs and external IDs must be unique. Fields that are not included in the request, are not changed.

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/connections

#### GET

**概要**: List connections



> **Required capabilities:** `DiagramParsingAcl:READ`

List all connections defined in the current project.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: 
- `400`: 

---

#### POST

**概要**: Create connections



> **Required capabilities:** `DiagramParsingAcl:WRITE`

Create connections between symbols in parsed diagrams.

**リクエストボディ**:

Connections to create.

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/connections/byids

#### POST

**概要**: Retrieve connections



> **Required capabilities:** `DiagramParsingAcl:READ`

Retrieve up to 100 connections by specifying their IDs.

**リクエストボディ**:

List of IDs for the connections to return.

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/connections/delete

#### POST

**概要**: Delete connections



> **Required capabilities:** `DiagramParsingAcl:WRITE`

Delete up to 100 connections by specifying their IDs.

**リクエストボディ**:

List of connections IDs to delete.

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/diagrams

#### GET

**概要**: List diagrams



> **Required capabilities:** `DiagramParsingAcl:READ`

List all diagrams defined in the current project.

**パラメータ**:

- `` (unknown, 任意)

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/diagrams/byids

#### POST

**概要**: Retrieve diagrams



> **Required capabilities:** `DiagramParsingAcl:READ`

Retrieve up to 100 diagrams by specifying their external IDs.

**リクエストボディ**:

List of IDs for the diagrams to return.

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/diagrams/list

#### POST

**概要**: Filter diagrams



> **Required capabilities:** `DiagramParsingAcl:READ`

Filter and retrieve up to 100 diagrams by specifying their properties.

**リクエストボディ**:

Body contains the properties to filter on.

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/diagrams/{diagramId}

#### GET

**概要**: Get an extended diagram by external id



> **Required capabilities:** `DiagramParsingAcl:READ`

Get diagram information and its parsed outputs using the diagram ID. The parsed output includes all diagram entities and their connections extracted during the file parsing process.

**パラメータ**:

- `` (unknown, 任意)

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/diagrams/{diagramId}/with-paths

#### GET

**概要**: Get an extended diagram with paths by external id



> **Required capabilities:** `DiagramParsingAcl:READ`

Get a single diagram containing entities, connections, and SVG data by using its external id

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/diagrams/{fileSpace}/{fileExternalId}/{pageNumber}

#### GET

**概要**: Get an extended diagram by file reference



> **Required capabilities:** `DiagramParsingAcl:READ`

Get diagram information and its parsed outputs using its related fileId and pageNumber. The parsed output includes all diagram entities and their connections extracted during the file parsing process.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/entities

#### GET

**概要**: List diagram entities



> **Required capabilities:** `DiagramParsingAcl:READ`

List all diagram entities defined for the diagram specified by the diagramId parameter.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: 
- `400`: 

---

#### POST

**概要**: Create diagram entities



> **Required capabilities:** `DiagramParsingAcl:WRITE`

Create diagram entities.

**リクエストボディ**:

Diagram entities to create.

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/entities/byids

#### POST

**概要**: Retrieve diagram entities



> **Required capabilities:** `DiagramParsingAcl:READ`

Retrieve up to 100 diagram entities by specifying their IDs.

**リクエストボディ**:

List of IDs for the diagram entities to return.

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/entities/delete

#### POST

**概要**: Delete diagram entities



> **Required capabilities:** `DiagramParsingAcl:WRITE`

Delete up to 100 diagram entities by specifying their IDs.

**リクエストボディ**:

List of diagram entity IDs to delete.

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/entities/update

#### POST

**概要**: Update diagram entities



> **Required capabilities:** `DiagramParsingAcl:WRITE`

Update the properties of a set of diagram entities

**リクエストボディ**:

List of diagram entities to be updated.

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/geometries

#### GET

**概要**: List geometries



> **Required capabilities:** `DiagramParsingAcl:READ`

List all geometries (also called variants in documentation) defined in the current project.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: 
- `400`: 

---

#### POST

**概要**: Create geometries



> **Required capabilities:** `DiagramParsingAcl:WRITE`

Create geometries.

**リクエストボディ**:

Geometries to create.

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/geometries/byids

#### POST

**概要**: Retrieve geometries



> **Required capabilities:** `DiagramParsingAcl:READ`

Retrieve up to 100 geometries by specifying their IDs.

**リクエストボディ**:

List of IDs for the geometries to return.

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/geometries/delete

#### POST

**概要**: Delete geometries



> **Required capabilities:** `DiagramParsingAcl:WRITE`

Delete up to 100 geometries by specifying their IDs.

**リクエストボディ**:

List of geometry IDs to delete.

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/geometries/update

#### POST

**概要**: Update geometries



> **Required capabilities:** `DiagramParsingAcl:WRITE`

Update the properties of a set of geometries

**リクエストボディ**:

List of geometries to be updated.

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/libraries

#### GET

**概要**: List libraries



> **Required capabilities:** `DiagramParsingAcl:READ`

List all libraries defined in the current project.

**パラメータ**:

- `` (unknown, 任意)

**レスポンス**:

- `200`: 
- `400`: 

---

#### POST

**概要**: Create libraries



> **Required capabilities:** `DiagramParsingAcl:WRITE`

Create libraries.

**リクエストボディ**:

Libraries to create.

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/libraries/byids

#### POST

**概要**: Retrieve libraries



> **Required capabilities:** `DiagramParsingAcl:READ`

Retrieve up to 100 libraries by specifying their IDs.

**リクエストボディ**:

List of IDs for the libraries to return.

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/libraries/delete

#### POST

**概要**: Delete libraries



> **Required capabilities:** `DiagramParsingAcl:WRITE`

Delete up to 100 libraries by specifying their IDs.

**リクエストボディ**:

List of libraries IDs to delete.

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/libraries/update

#### POST

**概要**: Update libraries



> **Required capabilities:** `DiagramParsingAcl:WRITE`

Update the properties of a set of libraries.

**リクエストボディ**:

List of libraries to be updated.

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/libraries/{libraryId}

#### GET

**概要**: Get a library with symbols



> **Required capabilities:** `DiagramParsingAcl:READ`

Returns a single library with all its symbols and geometries.

**パラメータ**:

- `` (unknown, 任意)

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/libraries/{libraryId}/copy

#### POST

**概要**: Copy library



> **Required capabilities:** `DiagramParsingAcl:WRITE`

Creates a new library by copying an existing library, including all its symbols and geometries.

**パラメータ**:

- `` (unknown, 任意)

**リクエストボディ**:

Name of the new library

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/parsing/instant

#### POST

**概要**: Run an instant parsing job for a file



> **Required capabilities:** `DiagramParsingAcl:WRITE`

Start an instant parsing job on a file to detect diagram symbols based on the specified library. Parsed outputs do not persist.

**リクエストボディ**:

Specifies the library to use when instant parsing the given file.

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/parsing/symbol-detection

#### POST

**概要**: Run parsing for files



> **Required capabilities:** `DiagramParsingAcl:WRITE`

Start a parsing job on files to detect their symbols and connections.

**リクエストボディ**:

Specifies the library to use when parsing the given files.

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/svg-data/diagram/{diagramId}

#### GET

**概要**: Get SVG data by diagramId



> **Required capabilities:** `DiagramParsingAcl:READ` `DataModelInstancesAcl:READ`

Get the SVG data of a file by externalId of a diagram.

**パラメータ**:

- `` (unknown, 任意)

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/svg-data/file/{fileSpace}/{fileExternalId}/{pageNumber}

#### GET

**概要**: Get SVG data by file reference



> **Required capabilities:** `DiagramParsingAcl:READ` `DataModelInstancesAcl:READ`

Get the SVG data of a file by file instanceId and pageNumber.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/symbols

#### GET

**概要**: List symbols



> **Required capabilities:** `DiagramParsingAcl:READ`

List all symbols defined in the current project.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: 
- `400`: 

---

#### POST

**概要**: Create symbols



> **Required capabilities:** `DiagramParsingAcl:WRITE`

Create symbols

**リクエストボディ**:

List of symbols to create.

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/symbols/byids

#### POST

**概要**: Retrieve symbols



> **Required capabilities:** `DiagramParsingAcl:READ`

Retrieve up to 100 symbols by specifying their IDs.

**リクエストボディ**:

List of IDs for the symbols to return.

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/symbols/delete

#### POST

**概要**: Delete symbols



> **Required capabilities:** `DiagramParsingAcl:WRITE`

Delete up to 100 symbols by specifying their IDs.

**リクエストボディ**:

List of symbols IDs to delete.

**レスポンス**:

- `200`: 
- `400`: 

---

### /diagram-parsing/symbols/update

#### POST

**概要**: Update symbols



> **Required capabilities:** `DiagramParsingAcl:WRITE`

Update the properties of a set of symbols

**リクエストボディ**:

List of symbols to be updated.

**レスポンス**:

- `200`: 
- `400`: 

---

### /documents/aggregate

#### POST

**概要**: Aggregate documents



> **Required capabilities:** `filesAcl:READ`

The aggregation API lets you compute aggregated results on documents, such as
getting the count of all documents in a project, checking
different authors of documents in a project and the count of documents in
each of those aggregations. By specifying an additional filter or search, you can
aggregate only among documents matching the specified filter or search.

When you don't specify the `aggregate` field in the request
body,  the default behavior is to return the count of all matched documents.

### Supported aggregates

<table>

<tr>
<th> <div style="width:200px"> Aggregate </div> </th> 
<th> <div style="width:350px"> Description </div> </th> 
<th> Example  </th>
</tr>

<tr>
<td> <code>count</code> </td> <td> Count of documents matching the specified filters and search. </td> <td>  

```json
{
    "search":{
        "query":"example",
        
    },
    "aggregate":"count",
    
}
```

<tr>
<td> <code>cardinalityValues</code> </td> <td> Returns an approximate count of 
distinct values for the specified properties. </td> <td>  

```json
{
    "aggregate":"cardinalityValues",
    "properties":[
        {
            "property":[
                "mimeType"
            ]
        }
    ]
}
```

</td>
</tr>

<tr>
<td> <code>cardinalityProperties</code> </td> <td> Returns an approximate count of
distinct properties for a given property path. Currently only implemented for the
<code>["sourceFile", "metadata"]</code> path.</td> <td>

```json
{
    "aggregate":"cardinalityProperties",
    "path":["sourceFile", "metadata"]
}
```

</td>
</tr>

</td>
</tr>

<tr>
<td> <code>uniqueValues</code> </td> <td> Returns top unique values for specified properties (up to the
requested limit) and the count of each in the property specified in <code>properties</code>. 
The list will have the highest count first. </td> <td>  

```json
{
    "aggregate":"uniqueValues",
    "properties":[
        {
            "property":[
                "author"
            ]
        }
    ]
}
```

</td>
</tr>

<tr>
<td> <code>uniqueProperties</code> </td> <td> Returns top unique properties values for specified properties (up to the
requested limit) and the count of each in the property specified in <code>properties</code>. 
The list will have the highest count first.

Currently, the <code>uniqueProperties</code> aggregate is only supported for property 
<code>["sourceFile", "metadata"]</code> .</td> <td>  

```json
{
    "aggregate":"uniqueProperties",
    "properties":[
        {
            "property":[
                "sourceFile",
                "metadata"
            ]
        }
    ]
}
```

</td>
</tr>

</table>

### Aggregate filtering

> Only some aggregate types currently support <code>aggregateFilter</code>

Aggregate filtering works directly on the aggregated result. While a normal filter filters relevant documents, aggregate filtering filters the aggregated bucket values. This is useful for e.g. listing metadata keys; while a normal filter will return all metadata keys for related documents, the aggregate filter can be used to reduce the aggregate result even further.

Tip: use both <code>filter</code> and <code>aggregateFilter</code> to potentially speed up queries, as the <code>aggregateFilter</code> is essentially a post filter.


#### Filter metadata keys by prefix

Here we only show metadata keys which starts with "car".
```json
{
    "aggregate": "uniqueProperties",
    "properties": [{"property": ["sourceFile", "metadata"]}],
    "aggregateFilter": {"prefix": {"value": "car"}}
}
```

#### Filter metadata values by prefix

Here we only show metadata values which starts with "ctx", for the given metadata key "car-codes".
```json
{
    "aggregate": "uniqueValues",
    "properties": [{"property": ["sourceFile", "metadata", "car-codes"]}],
    "aggregateFilter": {"prefix": {"value": "ctx"}}
}
```


**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 

---

### /documents/content

#### POST

**概要**: Retrieve document content



> **Required capabilities:** `filesAcl:READ`

Returns extracted textual information for the given document.

The documents pipeline extracts up to 1MiB of textual information from each processed document.
The search and list endpoints truncate the textual content of each document, in order to reduce the size
of the returned payload. If you want the whole text for a document, you can use this endpoint.

The `accept` request header MUST be set to `text/plain`. Other values will
give an HTTP 406 error.

**リクエストボディ**:

Fields to be set for the content request.

**レスポンス**:

- `200`: OK
- `400`: 

---

### /documents/list

#### POST

**概要**: List documents



> **Required capabilities:** `filesAcl:READ`

Retrieves a list of the documents in a project. You can use filters to narrow down the list.
Unlike the search endpoint, the pagination isn't restricted to 1000 documents in total, meaning
this endpoint can be used to iterate through all the documents in your project.

For more information on how the filtering works, see the documentation for the search endpoint.

**リクエストボディ**:

Fields to be set for the list request.

**レスポンス**:

- `200`: 
- `400`: 

---

### /documents/passages/search

#### POST

**概要**: Semantic search for passages



> **Required capabilities:** `filesAcl:READ`

This endpoint lets you search for relevant passages of up to 100 PDF documents by using advanced filters and semantic search queries. 
Where documents search results in a list of relevant documents, this endpoint gives you a result of the most relevant parts of a set of documents.
For each query you have to specify a list of document ids using the `in` filter (see exmple).

Semantic search works by comparing the semantic meaning of the search query to the semantic meaning of the document passages. 
The document passages are automatically derived and indexed upon file uploads (PDF only).

A natural flow would be:
 1. Upload a document
 2. Query `/documents/status` to check if the document is ready (see beta documentation)
 3. Query `/documents/passages/search` to find relevant passages in the document(s) 

### Passages search offers semantic search filters

> This endpoint requires a list of document ids that you want to do semantic search on. 
> You can not currently search through all passages for the entire project in a single query.

#### Ordering

Search results are ranked by relevance, with the most relevant appearing first. How relevance is determined depends on the filter you choose:

 - **Lexical Search Filter**: This filter looks for exact word matches, making it ideal for finding specific dates, names, or keywords. Use this when you know exactly what you’re looking for. This is the default search behavior for the Documents API.

 - **Semantic Search Filter**: This filter understands the meaning behind words and phrases, even if they don’t match exactly. It’s useful for broader searches or when you’re looking for related ideas or concepts.

Choose the filter based on your needs: use Lexical for precise results and Semantic for more general, context-based searches.

When both filters are used together, the results are combined to give you a balanced, relevant list. For best results, we recommend starting with both filters active.


#### Examples

The following request will return document passages matching the specified search query for the document ids 1, 2, and 3.

```json
{
    "filter":{
        "and": [
            {
                "semanticSearch":{
                    "property":["content"],
                    "value":"I have an overheating pump that is 85 degrees celsius, what dangers are there with higher temperatures"
                }
            },
            {
                "in":{
                    "property":["document", "id"],
                    "values":[1, 2, 3]
                }
            }
        ]
    }
}
 ```

If you need keyword matching you can specify a `lexicalSearch` filter. This helps with edge cases where it's hard to extract meaning such as numbers (eg. dates), names, etc.

```json
{
    "filter":{
        "and": [
            {
                "semanticSearch":{
                    "property":["content"],
                    "value":"I have an overheating pump that is 85 degrees celsius, what dangers are there with higher temperatures"
                }
            },
            {
                "lexicalSearch":{
                    "property":["content"],
                    "value":"pump AJ523-253-133"
                }
            },
            {
                "in":{
                    "property":["document", "id"],
                    "values":[1, 2, 3]
                }
            }
        ]
    }
}
 ```

Doing just lexical search is also possible:

```json
{
    "filter":{
        "and": [
            {
                "lexicalSearch":{
                    "property":["content"],
                    "value":"pump AJ523-253-133"
                }
            },
            {
                "in":{
                    "property":["document", "id"],
                    "values":[1, 2, 3]
                }
            }
        ]
    }
}
 ```


### Filtering

Filtering uses a special JSON filtering language.
It's flexible and consists of a number of different "leaf" filters, which can be combined arbitrarily using the boolean clauses `and`.
This is the same filters used in the search, list and aggregate endpoints, with some restrictions and the addition of "semanticSearch" filter.

#### Supported leaf filters

<table>
<tr>
<th> <div style="width:150px"> Leaf filter </div> </th> 
<th> <div style="width:200px"> Supported fields </div> </th> 
<th> Description  </th>
</tr>

<tr>
<td> <code>equals</code> </td> <td> Non-array type fields </td> <td> Only includes results that are equal to the specified value. 

```json
{
    "equals":{
        "property":["property"],
        "value":"example"
    }
}
```
</td>
</tr>

<tr>
<td> <code>in</code> </td> <td> Non-array type fields </td> <td> Only includes results that are equal to one of the specified values.  

```json
{
    "in":{
        "property":["property"],
        "values":[
            1,
            2,
            3
        ]
    }
}
```
</td>
</tr>


<tr>
<td> <code>semanticSearch</code> </td> <td> <code>content</code> </td> <td>

```json
{
    "semanticSearch":{
        "property":["property"],
        "value":"example"
    }
}
```
</td>
</tr>


<tr>
<td> <code>lexicalSearch</code> </td> <td> <code>content</code> </td> <td>

```json
{
    "lexicalSearch":{
        "property":["property"],
        "value":"example"
    }
}
```
</td>
</tr>

</table>

#### Properties

The following overview shows the properties you can filter on and which filter applies to which property.

| Property                      | Type                | Applicable filters            |
|-------------------------------|---------------------|-------------------------------|
| `["document", "id"]`          | integer             | equals, in                    |
| `["document", "externalId"]`  | string              | equals, in                    |
| `["document", "instanceId"]`  | object              | equals, in                    |
| `["type"]`                    | string              | equals                        |
| `["content"]`                 | string              | semanticSearch, lexicalSearch |

#### Full example

```json
{
    "filter":{
      "and": [
        {
          "in": {
            "property": ["document", "instanceId"],
            "values": [
              {"space": "space1", "externalId": "my-ext-id-1"},
              {"space": "space1", "externalId": "my-ext-id-45"},
              {"space": "space1", "externalId": "some-other-ext-id"},
            ]
          }
        },
        {
          "semanticSearch":{
              "property":["content"],
              "value":"A person walking a dog at night time"
          }
        },
        {
          "lexicalSearch":{
              "property":["content"],
              "value":"11:23pm"
          }
        }
      ]
    }
}
 ```


**リクエストボディ**:

Fields to be set for the search request.

**レスポンス**:

- `200`: 
- `400`: 

---

### /documents/search

#### POST

**概要**: Search for documents



> **Required capabilities:** `filesAcl:READ`

This endpoint lets you search for documents by using advanced filters and free text queries.
Free text queries are matched against the documents' filenames and contents.

### Free text search

#### Boolean operators

The `+` symbol represents an AND operation, and the `|` symbol represents an OR.
Searching for `lorem + ipsum` will match documents containing both "lorem" AND "ipsum" in the filename or content.
Similarly, searching for `lorem | ipsum` will match documents containing either "lorem" OR "ipsum" in the filename or
content.

The default operator between the search keywords is AND.
That means that searching for two terms without any operator, for example, `lorem ipsum`, will
match documents containing both the words "lorem" and "ipsum" in the filename or content.

You can use the operator `-` to exclude documents containing a specific word.
For instance, the search `lorem -ipsum` will match documents that contain the word "lorem", but does NOT contain the
word "ipsum".

#### Phrases

Enclose multiple words inside double quotes `"` to group these words together.
Normally, the search query `lorem ipsum` will match not only "lorem ipsum" but also "lorem cognite ipsum",
and in general, there can be any number of words between the two words in the query.
The search query `"lorem ipsum"`, however, will match only exactly "lorem ipsum" and not "lorem cognite ipsum".

#### Escape

To search for the special characters (`+`, `|`, `-`, `"`. `\`), escape with a preceding backslash `\`.

#### Ordering

When you search for a term, the endpoint tries to return the most relevant documents first, with less relevant documents further down
the list.
There are a few factors that determine the relevance of a document:

- If the search terms are found multiple times within a document, the relevance of that document is higher.
- For searches with multiple terms, documents containing all the terms are considered more relevant than documents
  containing only some.
- Matches of the terms in the filename field of the document are more relevant than matches in the document's content.

#### Examples

The following request will return documents matching the specified search query.

```json
{
  "search": {
    "query": "cognite \"lorem ipsum\""
   }
}
 ```

The following example combines a query with a filter.
The search request will return documents matching the search query, where `externalId` starts with "1".
The results will be ordered by how well they match the query string.

```json
{
    "search":{
        "query":"cognite \"lorem ipsum\""
    },
    "filter":{
        "prefix":{
            "property":[
                "externalId"
            ],
            "value":"1"
        }
    }
}
```

### Highlights

When you enable highlights for your search query, the response contains an additional highlight field for each
search hit, including the highlighted fragments for your query matches. However, the result limit is 20 documents due to the operation costs.

### Filtering

Filtering uses a special JSON filtering language.
It's quite flexible and consists of a number of different "leaf" filters, which can be combined arbitrarily using the boolean clauses `and`, `or`, and `not`.

#### Supported leaf filters

<table>
<tr>
<th> <div style="width:150px"> Leaf filter </div> </th> 
<th> <div style="width:200px"> Supported fields </div> </th> 
<th> Description  </th>
</tr>

<tr>
<td> <code>equals</code> </td> <td> Non-array type fields </td> <td> Only includes results that are equal to the specified value. 

```json
{
    "equals":{
        "property":[
            "property"
        ],
        "value":"example"
    }
}
```
</td>
</tr>

<tr>
<td> <code>in</code> </td> <td> Non-array type fields </td> <td> Only includes results that are equal to one of the specified values.  

```json
{
    "in":{
        "property":[
            "property"
        ],
        "values":[
            1,
            2,
            3
        ]
    }
}
```
</td>
</tr>

<td> <code>containsAll</code> </td> <td> Array type fields </td> <td> Only includes results which contain all of the specified values.  

```json
{
    "containsAll":{
        "property":[
            "property"
        ],
        "values":[
            1,
            2,
            3
        ]
    }
}
```
</td>
<tr>

<td> <code>containsAny</code> </td> <td> Array type fields </td> <td> Only includes results which contain all of the specified values.  

```json
{
    "containsAny":{
        "property":[
            "property"
        ],
        "values":[
            1,
            2,
            3
        ]
    }
}
```
</td>

<tr>
<td> <code>exists</code> </td> <td> All fields </td> <td> Only includes results where the specified property exists (has value).  

```json
{
    "exists":{
        "property":[
            "property"
        ]
    }
}
```
</td>
</tr>

<tr>
<td> <code>prefix</code> </td> <td> String type fields </td> <td> Only includes results which start with the specified value. 

```json
{
    "prefix":{
        "property":[
            "property"
        ],
        "value":"example"
    }
}
```
</td>
</tr>

<tr>
<td> <code>range</code> </td> <td> Non-array type fields </td> <td> Only includes results that fall within the specified range. 

Supported operators: <code>gt</code>, <code>lt</code>, <code>gte</code>, <code>lte</code>

```json
{
    "range":{
        "property":[
            "property"
        ],
        "gt":1,
        "lte":5
    }
}
```
</td>
</tr>

<tr>
<td> <code>geojsonIntersects</code> </td> <td> <code>geoLocation</code> </td> <td> Only includes results where the geoshape intersects with the specified geometry.

```json
{
    "geojsonIntersects":{
        "property":[
            "sourceFile",
            "geoLocation"
        ],
        "geometry":{
            "type":"Polygon",
            "coordinates": [[[30, 10], [40, 40], [20, 40], [10, 20], [30, 10]]],
        }
    }
}
```
</td>
</tr>

<tr>
<td> <code>geojsonDisjoint</code> </td> <td> <code>geoLocation</code> </td> <td> Only includes results where the geoshape has nothing in common with the specified geometry.

```json
{
    "geojsonDisjoint":{
        "property":[
            "sourceFile",
            "geoLocation"
        ],
        "geometry":{
            "type":"Polygon",
            "coordinates": [[[30, 10], [40, 40], [20, 40], [10, 20], [30, 10]]],
        }
    }
}
```
</td>
</tr>

<tr>
<td> <code>geojsonWithin</code> </td> <td> <code>geoLocation</code> </td> <td> Only includes results where the geoshape falls within the specified geometry.

```json
{
    "geojsonWithin":{
        "property":[
            "sourceFile",
            "geoLocation"
        ],
        "geometry":{
            "type":"Polygon",
            "coordinates": [[[30, 10], [40, 40], [20, 40], [10, 20], [30, 10]]],
        }
    }
}
```
</td>
</tr>

<tr>
<td> <code>inAssetSubtree</code> </td> <td> <code>assetIds</code>, <code>assetExternalIds</code> </td> <td> Only includes results with a related asset in a subtree rooted at any specified IDs.

```json
{
    "filter":{
        "inAssetSubtree":{
            "property":[
                "sourceFile",
                "assetIds"
            ],
            "values":[
                1,
                2,
                3
            ]
        }
    }
}
```
</td>
</tr>

<tr>
<td> <code>search</code> </td> <td> <code>name</code>, <code>content</code> </td> <td>

```json
{
    "search":{
        "property":[
            "property"
        ],
        "value":"example"
    }
}
```
</td>
</tr>

</table>

#### Properties

The following overview shows the properties you can filter and which filter applies to which property.

| Property                                 | Type                | Applicable filters                                                                                                                                                             |
|------------------------------------------|---------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `["id"]`                                 | integer             | equals, in, range, exists                                                                                                                                                      |
| `["externalId"]`                         | string              | equals, in, prefix, exists                                                                                                                                                     |
| `["instanceId"]`                         | string              | equals, in                                                                                                                                                                     |
| `["instanceId", "space"]`                | string              | equals, in, prefix, exists                                                                                                                                                     |
| `["instanceId", "externalId"]`           | string              | equals, in, prefix, exists                                                                                                                                                     |
| `["title"]`                              | string              | equals, in, prefix, exists                                                                                                                                                     |
| `["author"]`                             | string              | equals, in, prefix, exists                                                                                                                                                     |
| `["createdTime"]`                        | integer             | equals, in, range, exists                                                                                                                                                      |
| `["modifiedTime"]`                       | integer             | equals, in, range, exists                                                                                                                                                      |
| `["lastIndexedTime"]`                    | integer             | equals, in, range, exists                                                                                                                                                      |
| `["mimeType"]`                           | string              | equals, in, prefix, exists                                                                                                                                                     |
| `["extension"]`                          | string              | equals, in, prefix, exists                                                                                                                                                     |
| `["pageCount"]`                          | integer             | equals, in, range, exists                                                                                                                                                      |
| `["type"]`                               | string              | equals, in, prefix, exists                                                                                                                                                     |
| `["geoLocation"]`                        | geometry object     | geojsonIntersects, geojsonDisjoint, geojsonWithin, exists                                                                                                                      |
| `["language"]`                           | string              | equals, in, prefix, exists                                                                                                                                                     |
| `["assetIds"]`                           | array of integers   | containsAny, containsAll, exists, inAssetSubtree                                                                                                                               |
| `["assetExternalIds"]`                   | array of strings    | containsAny, containsAll, exists, inAssetSubtree                                                                                                                               |
| `["labels"]`                             | array of Labels     | containsAny, containsAll, exists                                                                                                                                               |
| `["content"]`                            | string              | search                                                                                                                                                                         |
| `["sourceFile", "name"]`                 | string              | equals, in, prefix, exists, search                                                                                                                                             |
| `["sourceFile", "mimeType"]`             | string              | equals, in, prefix, exists                                                                                                                                                     |
| `["sourceFile", "size"]`                 | integer             | equals, in, range, exists                                                                                                                                                      |
| `["sourceFile", "source"]`               | string              | equals, in, prefix, exists                                                                                                                                                     |
| `["sourceFile", "directory"]`            | string              | equals, in, prefix, exists                                                                                                                                                     |
| `["sourceFile", "assetIds"]`             | array of integers   | containsAny, containsAll, exists, inAssetSubtree                                                                                                                               |
| `["sourceFile", "assetExternalIds"]`     | array of strings    | containsAny, containsAll, exists, inAssetSubtree                                                                                                                               |
| `["sourceFile", "datasetId"]`            | integer             | equals, in, range, exists                                                                                                                                                      |
| `["sourceFile", "securityCategories"]`   | array of integers   | containsAny, containsAll, exists                                                                                                                                               |
| `["sourceFile", "geoLocation"]`          | geometry object     | geojsonIntersects, geojsonDisjoint, geojsonWithin, exists                                                                                                                      |
| `["sourceFile", "labels"]`               | array of Labels     | containsAny, containsAll, exists                                                                                                                                               |
| `["sourceFile", "metadata", <key>]`      | string              | equals, in, prefix, exists                                                                                                                                                     |
| `["sourceFile", "metadata"]`             | string              | equals, in, prefix, exists<br><br>This is a special filter field that targets all metadata values. <br>An alternative to creating a filter for each key in the metadata field. |

#### Full example

```json
{
   "filter": {
      "and": [
         {
            "or": [
               {
                  "equals": {
                     "property": [
                        "type"
                     ],
                     "value": "PDF"
                  }
               },
               {
                  "prefix": {
                     "property": [
                        "externalId"
                     ],
                     "value": "hello"
                  }
               }
            ]
         },
         {
            "range": {
               "property": [
                  "createdTime"
               ],
               "lte": 1519862400000
            }
         },
         {
            "not": {
               "in": {
                  "property": [
                     "sourceFile",
                     "name"
                  ],
                  "values": [
                     "My Document.doc",
                     "My Other Document.docx"
                  ]
               }
            }
         }
      ]
   }
}
 ```

### Sorting

By default, search results are ordered by relevance, meaning how well they match the given query string.
However, it's possible to specify a different property to sort by.
Sorting can be ascending or descending. The sort order is ascending if none is specified.

#### Properties

The following overview shows all properties that can be sorted on.

| Property                        |
|---------------------------------|
| `["id"]`                        |
| `["externalId"]`                |
| `["mimeType"]`                  |
| `["extension"]`                 |
| `["pageCount"]`                 |
| `["author"]`                    |
| `["title"]`                     |
| `["language"]`                  |
| `["type"]`                      |
| `["createdTime"]`               |
| `["modifiedTime"]`              |
| `["lastIndexedTime"]`           |
| `["sourceFile", "name"]`        |
| `["sourceFile", "mimeType"]`    |
| `["sourceFile", "size"]`        |
| `["sourceFile", "source"]`      |
| `["sourceFile", "datasetId"]`   |
| `["sourceFile", "metadata", *]` |

#### Example

```json
{
    "sort":[
        {
            "property":[
                "createdTime"
            ],
            "order":"asc",
            
        }
    ]
}
 ```


**リクエストボディ**:

Fields to be set for the search request.

**レスポンス**:

- `200`: 
- `400`: 

---

### /documents/status

### /documents/{documentId}/preview/image/pages/{pageNumber}

#### GET

**概要**: Retrieve a image preview of a page from a document



> **Required capabilities:** `filesAcl:READ`

This endpoint returns a rendered image preview for a specific page of the specified document.

The `accept` request header MUST be set to `image/png`. Other values will
give an HTTP 406 error.

The rendered image will be downsampled to a maximum of 2400x2400 pixels.
Only PNG format is supported and only the first 10 pages can be rendered.

Previews will be rendered if neccessary during the request. Be prepared
for the request to take a few seconds to complete.

**パラメータ**:

- `documentId` (integer, 必須)
  - Internal ID for document to preview
- `pageNumber` (integer, 必須)
  - Page number to preview. Starting at 1 for first page

**レスポンス**:

- `200`: OK
- `400`: 
- `401`: 
- `406`: 
- `422`: 

---

### /documents/{documentId}/preview/pdf

#### GET

**概要**: Retrieve a PDF preview of a document



> **Required capabilities:** `filesAcl:READ`

This endpoint returns a rendered PDF preview for a specified document.

The `accept` request header MUST be set to `application/pdf`. Other values will
give an HTTP 406 error.

This endpoint is optimized for in-browser previews. We reserve the right
to adjust the quality and other attributes of the output with this in mind.
Please reach out to us if you have a different use case and requirements.

Previews will be rendered if neccessary during the request. Be prepared
for the request to take a few seconds to complete.

**パラメータ**:

- `` (unknown, 任意)
- `documentId` (integer, 必須)
  - Internal ID for document to preview

**レスポンス**:

- `200`: OK
- `400`: 
- `401`: 
- `406`: 
- `422`: 

---

### /documents/{documentId}/preview/pdf/temporarylink

#### GET

**概要**: Retrieve a temporary link to a PDF preview of a document



> **Required capabilities:** `filesAcl:READ`

This endpoint works similar as the normal preview endpoint except
it returns a short-lived temporary link to download the rendered preview instead
of returning the binary data.

**パラメータ**:

- `` (unknown, 任意)
- `documentId` (integer, 必須)
  - Internal ID for document to preview

**レスポンス**:

- `200`: 
- `400`: 
- `401`: 
- `422`: 

---

### /documents/{id}/content

#### GET

**概要**: Retrieve document content



> **Required capabilities:** `filesAcl:READ`

Returns extracted textual information for the given document.
The documents pipeline extracts up to 1MiB of textual information from each processed document.
The search and list endpoints truncate the textual content of each document, in order to reduce the size
of the returned payload. If you want the whole text for a document, you can use this endpoint.
The `accept` request header MUST be set to `text/plain`. Other values will
give an HTTP 406 error.

**パラメータ**:

- `` (unknown, 任意)

**レスポンス**:

- `200`: OK
- `400`: 

---

### /events

#### GET

**概要**: List events



> **Required capabilities:** `eventsAcl:READ`

List events optionally filtered on query parameters

### Request throttling
This endpoint is meant for data analytics/exploration usage and is not suitable for high load data retrieval usage.
It is a subject of the new throttling schema (limited request rate and concurrency).
Please check [Events resource description](../../) for more information.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)
- `minStartTime` (unknown, 任意)
- `maxStartTime` (unknown, 任意)
- `minEndTime` (unknown, 任意)
- `maxEndTime` (unknown, 任意)
- `minActiveAtTime` (unknown, 任意)
- `maxActiveAtTime` (unknown, 任意)
- `assetIds` (unknown, 任意)
  - Asset IDs of equipment that this event relates to. Format is list of IDs serialized as JSON array(int64). Takes [ 1 .. 100 ] of unique items.
- `assetExternalIds` (unknown, 任意)
  - Asset external IDs of equipment that this event relates to. Takes 1..100 unique items.
- `assetSubtreeIds` (unknown, 任意)
  - Only include events that have a related asset in a subtree rooted at any of these assetIds (including the roots given). If the total size of the given subtrees exceeds 100,000 assets, an error will be returned.
- `assetSubtreeExternalIds` (unknown, 任意)
  - Only include events that have a related asset in a subtree rooted at any of these assetExternalIds (including the roots given). If the total size of the given subtrees exceeds 100,000 assets, an error will be returned.
- `source` (string, 任意)
- `type` (unknown, 任意)
- `subtype` (unknown, 任意)
- `minCreatedTime` (unknown, 任意)
- `maxCreatedTime` (unknown, 任意)
- `minLastUpdatedTime` (unknown, 任意)
- `maxLastUpdatedTime` (unknown, 任意)
- `externalIdPrefix` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)
- `sort` (array, 任意)
  - Sort by an array of the selected fields. Syntax: `["<fieldname>:asc|desc"]`. Default sort order is `asc` with short syntax: `["<fieldname>"]`.
Filter accepts the following field names:
  `dataSetId`,
  `externalId`,
  `type`,
  `subtype`,
  `startTime`,
  `endTime`,
  `createdTime`,
  `lastUpdatedTime`,
  `source`,
  `description`,
  `metadata`.
Partitions are done independently of sorting, there's no guarantee on sort order between elements from different partitions.


**レスポンス**:

- `200`: 
- `400`: 
- `429`: 

---

#### POST

**概要**: Create events



> **Required capabilities:** `eventsAcl:WRITE`

Creates multiple event objects in the same project.
It is possible to post a maximum of 1000 events per request.

### Request throttling
This endpoint is a subject of the new throttling schema (limited request rate and concurrency).
Please check [Events resource description](../../) for more information.

**リクエストボディ**:

List of events to be posted. It is possible to post a maximum of 1000 events per request.

**レスポンス**:

- `201`: 
- `400`: 
- `429`: 

---

### /events/aggregate

#### POST

**概要**: Aggregate events



> **Required capabilities:** `eventsAcl:READ`

The aggregation API lets you compute aggregated results on events,
such as getting the count of all Events in a project, checking
different descriptions of events in your project, etc.

#### Aggregate filtering
##### Filter (filter & advancedFilter) data for aggregates
Filters behave the same way as for the `Filter events` endpoint.
In text properties, the values are aggregated in a case-insensitive manner.

##### aggregateFilter to filter aggregate results
`aggregateFilter` works similarly to `advancedFilter` but always applies to aggregate properties.
For instance, in an aggregation for the `source` property, only the values (aka buckets) of the `source` property can be filtered out.

### Request throttling
This endpoint is meant for data analytics/exploration usage and is not suitable for high load data retrieval usage.\
The Aggregates endpoint, as with all endpoints in the Events API,  is subject to a request budget that applies
limits to both request rate and concurrency.
Please check [Events resource description](../../) for more information.

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 
- `429`: 

---

### /events/byids

#### POST

**概要**: Retrieve events



> **Required capabilities:** `eventsAcl:READ`

Retrieves information about events in the same project. Events
are returned in the same order as the ids listed in the query.

A maximum of 1000 event IDs may be listed per request and all of them
must be unique.

### Request throttling
This endpoint is a subject of the new throttling schema (limited request rate and concurrency).
Please check [Events resource description](../../) for more information.

**リクエストボディ**:

List of IDs of events to retrieve. Must be up to a maximum of 1000 IDs, and all of them must be unique.

**レスポンス**:

- `200`: 
- `400`: 
- `429`: 

---

### /events/delete

#### POST

**概要**: Delete events



> **Required capabilities:** `eventsAcl:WRITE`

Deletes events with the given ids.
A maximum of 1000 events can be deleted per request.

### Request throttling
This endpoint is a subject of the new throttling schema (limited request rate and concurrency).
Please check [Events resource description](../../) for more information.

**リクエストボディ**:

List of IDs to delete.

**レスポンス**:

- `200`: 
- `400`: 
- `429`: 

---

### /events/list

#### POST

**概要**: Filter events



> **Required capabilities:** `eventsAcl:READ`

Retrieve a list of events in the same project. This operation supports pagination by cursor.
Apply Filtering and Advanced filtering criteria to select a subset of events.

### Advanced filtering
Advanced filter lets you create complex filtering expressions that combine simple operations,
such as `equals`, `prefix`, `exists`, etc., using boolean operators `and`, `or`, and `not`.
It applies to basic fields as well as metadata.

See the `advancedFilter` attribute in the example.

See more information about filtering DSL [here](https://docs.cognite.com/dev/concepts/resource_filtering_dsl/ "filtering DSL").

#### Supported leaf filters
| Leaf filter    | Supported fields       | Description  |
|----------------|------------------------|--------------|
| `containsAll`  | Array type fields      | Only includes results which contain all of the specified values. <br /> `{"containsAll": {"property": ["property"], "values": [1, 2, 3]}}` |
| `containsAny`  | Array type fields      | Only includes results which contain at least one of the specified values. <br /> `{"containsAny": {"property": ["property"], "values": [1, 2, 3]}}` |
| `equals`       | Non-array type fields  | Only includes results that are equal to the specified value. <br /> `{"equals": {"property": ["property"], "value": "example"}}` |
| `exists`       | All fields             | Only includes results where the specified property exists (has value). <br /> `{"exists": {"property": ["property"]}}` |
| `in`           | Non-array type fields  | Only includes results that are equal to one of the specified values. <br /> `{"in": {"property": ["property"], "values": [1, 2, 3]}}` |
| `prefix`       | String type fields     | Only includes results which start with the specified value. <br /> `{"prefix": {"property": ["property"], "value": "example"}}` |
| `range`        | Non-array type fields  | Only includes results that fall within the specified range. <br /> `{"range": {"property": ["property"], "gt": 1, "lte": 5}}` <br /> Supported operators: `gt`, `lt`, `gte`, `lte` |
| `search`       | `["description"]`      | Introduced to provide functional parity with /events/search endpoint. <br /> `{"search": {"property": ["property"], "value": "example"}}` |

##### Search
The `search` leaf filter provides functional parity with the `/events/search` endpoint.
It's available only for the `["description"]` field. When specifying only this filter with no explicit ordering,
behavior is the same as of the `/events/search/` endpoint without specifying filters.
Explicit sorting overrides the default ordering by relevance.
It's possible to use the `search` leaf filter as any other leaf filter for creating complex queries.

See the `search` filter in the `advancedFilter` attribute in the example.

#### advancedFilter attribute limits
- filter query max depth: 10
- filter query max number of clauses: 100
- filter by metadata is case-insensitive, and it supports filtering on keys and values up to 256 characters
- `and` and `or` clauses must have at least one element
- `property` array of each leaf filter has the following limitations:
  - number of elements in the array is in the range [1, 2]
  - elements must not be blank
  - each element max length is 128 symbols
  - property array must match one of the existing properties (static or dynamic metadata)
- `containsAll`, `containsAny`, and `in` filter `values` array size must be in the range [1, 100]
- `containsAll`, `containsAny`, and `in` filter `values` array must contain elements of a primitive type (number, string)
- `range` filter must have at least one of `gt`, `gte`, `lt`, `lte` attributes.
  But `gt` is mutually exclusive to `gte`, while `lt` is mutually exclusive to `lte`.
  For metadata, both upper and lower bounds must be specified.
- `gt`, `gte`, `lt`, `lte` in the `range` filter must be a primitive value
- `search` filter `value` must not be blank and the length must be in the range [1, 128]
- filter query may have maximum 2 search leaf filters
- maximum leaf filter string value length is different depending on the property the filter is using:
  - `externalId` - 255
  - `description` - 128 for the `search` filter and 255 for other filters
  - `type` - 64
  - `subtype` - 64
  - `source` - 128
  - any `metadata` key - 128

### Sorting
By default, events are sorted by their creation time in the ascending order.
Use the `search` leaf filter to sort the results by relevance.
Sorting by other fields can be explicitly requested. The `order` field is optional and defaults
to `desc` for `_score_` and `asc` for all other fields.
The `nulls` field is optional and defaults to `auto`. `auto` is translated to `last`
for the `asc` order and to `first` for the `desc` order by the service.
Partitions are done independently of sorting: there's no guarantee of the sort order between elements from different partitions.

See the `sort` attribute in the example.

#### Null values
In case the `nulls` attribute has the `auto` value or the attribute isn't specified,
null (missing) values are considered to be bigger than any other values.
They are placed last when sorting in the `asc` order and first when sorting in `desc`.
Otherwise, missing values are placed according to the `nulls` attribute (last or first), and their placement doesn't depend on the `order` value.
Values, such as empty strings, aren't considered as nulls.

#### Sorting by score
Use a special sort property `_score_` when sorting by relevance.
The more filters a particular event matches, the higher its score is. This can be useful,
for example, when building UIs. Let's assume we want exact matches to be be displayed above matches by
prefix as in the request below. An event with the type `fire` will match both `equals` and `prefix`
filters and, therefore, have higher score than events with names like `fire training` that match only the `prefix` filter.

```
"advancedFilter" : {
  "or" : [
    {
      "equals": {
        "property": ["type"],
        "value": "fire"
      }
    },
    {
      "prefix": {
        "property": ["type"],
        "value": "fire"
      }
    }
  ]
},
"sort": [
  {
    "property" : ["_score_"]
  }
]
```

### Request throttling
This endpoint is meant for data analytics/exploration usage and is not suitable for high load data retrieval usage.
It is a subject of the new throttling schema (limited request rate and concurrency).
Please check [Events resource description](../../) for more information.

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 
- `429`: 

---

### /events/search

#### POST

**概要**: Search events



> **Required capabilities:** `eventsAcl:READ`

Fulltext search for events based on result relevance. Primarily meant
for human-centric use-cases, not for programs, since matching and
ordering may change over time. Additional filters can also be
specified. This operation doesn't support pagination.

### Request throttling
This endpoint is meant for data analytics/exploration usage and is not suitable for high load data retrieval usage.
It is a subject of the new throttling schema (limited request rate and concurrency).
Please check [Events resource description](../../) for more information.

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 
- `429`: 

---

### /events/update

#### POST

**概要**: Update events



> **Required capabilities:** `eventsAcl:WRITE`

Updates events in the same project. This operation supports
partial updates; Fields omitted from queries will remain unchanged on
objects.

For primitive fields (String, Long, Int), use 'set': 'value' to update
value; use 'setNull': true to set that field to null.

For the Json Array field (e.g. assetIds), use 'set': [value1, value2] to
update value; use 'add': [v1, v2] to add values to current list of
values; use 'remove': [v1, v2] to remove these values from current list
of values if exists.

A maximum of 1000 events can be updated per request, and all of the
event IDs must be unique.

### Request throttling
This endpoint is a subject of the new throttling schema (limited request rate and concurrency).
Please check [Events resource description](../../) for more information.

**リクエストボディ**:

List of changes. A maximum of 1000 events can be updated per request, and all of the event IDs must be unique.

**レスポンス**:

- `200`: 
- `400`: 
- `429`: 

---

### /events/{id}

#### GET

**概要**: Receive an event by its ID



> **Required capabilities:** `eventsAcl:READ`

Retrieves an event by its internal (service-generated) ID.

### Request throttling
This endpoint is a subject of the new throttling schema (limited request rate and concurrency).
Please check [Events resource description](../../) for more information.

**パラメータ**:

- `id` (unknown, 必須)

**レスポンス**:

- `200`: 
- `400`: 
- `429`: 

---

### /extpipes

#### GET

**概要**: List extraction pipelines



> **Required capabilities:** `extractionpipelinesAcl:READ`

Returns a list of all extraction pipelines for a given project

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: Response with the list of extraction pipelines

---

#### POST

**概要**: Create extraction pipelines



> **Required capabilities:** `extractionpipelinesAcl:WRITE` `datasetsAcl:READ`

Creates multiple new extraction pipelines. A maximum of 1000 extraction pipelines can be created per request.

**リクエストボディ**:

**レスポンス**:

- `200`: Response with the list of extraction pipelines
- `400`: Response for a failed request

---

### /extpipes/byids

#### POST

**概要**: Retrieve extraction pipelines



> **Required capabilities:** `extractionpipelinesAcl:READ`

Retrieves information about multiple extraction pipelines in the same project. All ids and externalIds must be unique.

**リクエストボディ**:

**レスポンス**:

- `200`: Response with the list of extraction pipelines
- `400`: Response for a failed request

---

### /extpipes/config

#### GET

**概要**: Get a single configuration revision



> **Required capabilities:** `extractionconfigsAcl:READ`

Retrieves a single configuration revision. By default, the latest revision is retrieved.

**パラメータ**:

- `externalId` (string, 必須)
- `revision` (integer, 任意)
- `activeAtTime` (integer, 任意)

**レスポンス**:

- `200`: Response with the retrieved configuration revision
- `400`: Response for a failed request

---

#### POST

**概要**: Create extraction configuration revision



> **Required capabilities:** `extractionconfigsAcl:WRITE`

Creates a configuration revision for the given extraction pipeline.

**リクエストボディ**:

**レスポンス**:

- `200`: Response with the created configuration revision
- `400`: Response for a failed request

---

### /extpipes/config/revert

#### POST

**概要**: Revert configuration revision



> **Required capabilities:** `extractionconfigsAcl:WRITE`

Reverts the latest configuration revision to an older revision. Equivalent to creating a new revision identical to the old revision.

**リクエストボディ**:

**レスポンス**:

- `200`: Response with the new latest configuration revision
- `400`: Response for a failed request

---

### /extpipes/config/revisions

#### GET

**概要**: List configuration revisions



> **Required capabilities:** `extractionconfigsAcl:READ`

Lists configuration revisions for the given extraction pipeline.

**パラメータ**:

- `externalId` (string, 必須)
- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: Response with the retrieved configuration revisions and an optional cursor
- `400`: Response for a failed request

---

### /extpipes/delete

#### POST

**概要**: Delete extraction pipelines



> **Required capabilities:** `extractionpipelinesAcl:WRITE` `datasetsAcl:READ`

Delete extraction pipelines for given list of ids and externalIds. When the extraction pipeline is deleted, all extraction pipeline runs related to the extraction pipeline are automatically deleted.

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: Response for a failed request

---

### /extpipes/list

#### POST

**概要**: Filter extraction pipelines



> **Required capabilities:** `extractionpipelinesAcl:READ`

Use advanced filtering options to find extraction pipelines.

**リクエストボディ**:

**レスポンス**:

- `200`: Response with the list of extraction pipelines
- `400`: Response for a failed request

---

### /extpipes/runs

#### GET

**概要**: List extraction pipeline runs



> **Required capabilities:** `extractionrunsAcl:READ`

List of all extraction pipeline runs for a given extraction pipeline. Sorted by createdTime value with descendant order.

**パラメータ**:

- `externalId` (string, 必須)
- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: Response with list of extraction pipeline runs

---

#### POST

**概要**: Create extraction pipeline runs



> **Required capabilities:** `extractionrunsAcl:WRITE` `datasetsAcl:READ`

Create multiple extraction pipeline runs. Current version supports one extraction pipeline run per request. Extraction pipeline runs support three statuses: success, failure, seen. The content of the Error Message parameter is configurable and will contain any messages that have been configured within the extraction pipeline.

**リクエストボディ**:

**レスポンス**:

- `200`: Response with list of extraction pipeline runs
- `400`: Response for a failed request

---

### /extpipes/runs/list

#### POST

**概要**: Filter extraction pipeline runs



> **Required capabilities:** `extractionrunsAcl:READ`

Use advanced filtering options to find extraction pipeline runs. Sorted by createdTime value with descendant order.

**リクエストボディ**:

**レスポンス**:

- `200`: Response with list of extraction pipeline runs
- `400`: Response for a failed request

---

### /extpipes/update

#### POST

**概要**: Update extraction pipelines



> **Required capabilities:** `extractionpipelinesAcl:WRITE` `datasetsAcl:READ`

Update information for a list of extraction pipelines. Fields that are not included in the request, are not changed.

**リクエストボディ**:

**レスポンス**:

- `200`: Response with the list of updated extraction pipelines
- `400`: Response for a failed request

---

### /extpipes/{id}

#### GET

**概要**: Retrieve an extraction pipeline by its ID.



> **Required capabilities:** `extractionpipelinesAcl:READ`

Retrieve an extraction pipeline by its ID. If you want to retrieve extraction pipelines by externalIds, use Retrieve extraction pipelines instead.

**パラメータ**:

- `` (unknown, 任意)

**レスポンス**:

- `200`: Single extraction pipeline response
- `400`: Response for a failed request

---

### /extractors

#### GET

**概要**: List Extractors

List all available extractors.

**レスポンス**:

- `200`: List of extractors.
- `400`: 

---

### /extractors/byids

#### POST

**概要**: Retrieve Extractors

Retrieve extractors by external ID, optionally ignoring unknown IDs.

**リクエストボディ**:

**レスポンス**:

- `200`: List of extractors.
- `400`: Response for a failed request
- `422`: 

---

### /extractors/releases

#### GET

**概要**: List releases

List releases for all extractors, or optionally for a single extractor. Results are sorted in descending version order.

**パラメータ**:

- `externalId` (string, 任意)

**レスポンス**:

- `200`: List of extractor releases.
- `400`: 

---

### /extractors/releases/byids

#### POST

**概要**: Retrieve Releases

Retrieve releases by extractor external ID and version, optionally ignoring unknown IDs.

**リクエストボディ**:

List of releases to retrieve.

**レスポンス**:

- `200`: List of extractor releases.
- `400`: 
- `422`: 

---

### /extractors/schemas/{extractor}/{version}

#### GET

**概要**: Get Config Schema

Retrieve the configuration schema for the specified extractor and release version. The configuration schema is a [JSON Schema](https://json-schema.org/) file.

**パラメータ**:

- `extractor` (string, 必須)
- `version` (string, 必須)

**レスポンス**:

- `200`: Extractor configuration schema as JSON.
- `400`: 

---

### /extractors/solutions

#### GET

**概要**: List Solutions

List solutions. Solutions connect sources to extractors, documenting how a specific extractor might be used to connect to a source system.

**レスポンス**:

- `200`: List of solutions.
- `400`: 

---

### /extractors/solutions/byids

#### POST

**概要**: Retrieve Solutions

Retrieve solutions by external ID. Solutions connect sources to extractors, documenting how a specific extractor might be used to connect to a source system.

**リクエストボディ**:

**レスポンス**:

- `200`: List of solutions.
- `400`: 
- `422`: 

---

### /extractors/sources

#### GET

**概要**: List Source Systems

List source systems. Source systems represent sources that extractors may read from. They are tied to extractors through Solutions.

**レスポンス**:

- `200`: List of source systems.
- `400`: 

---

### /extractors/sources/byids

#### POST

**概要**: Retrieve Source Systems

Retrieve source systems. Source systems represent sources that extractors may read from. They are tied to extractors through Solutions.

**リクエストボディ**:

List of source systems to retrieve.

**レスポンス**:

- `200`: List of source systems.
- `400`: 
- `422`: 

---

### /files

#### GET

**概要**: List files



> **Required capabilities:** `filesAcl:READ`

The GET /files operation can be used to return information for all files in a project.

Optionally you can add one or more of the following query parameters.
The filter query parameters will filter the results to only include files that match all filter parameters.

### Request throttling
This endpoint is intended for data analytics and exploration purposes. It is not designed to support high-throughput data retrieval.
Please note that this endpoint is subject to the new throttling policy, which imposes limits on both request rate and concurrency.
For more details, please refer to the [Files resource documentation](../../).

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)
- `mimeType` (unknown, 任意)
- `source` (unknown, 任意)
- `assetIds` (unknown, 任意)
- `assetExternalIds` (unknown, 任意)
  - Asset external IDs of related equipment that this file relates to. Takes 1..100 unique items.
- `dataSetIds` (unknown, 任意)
- `rootAssetIds` (unknown, 任意)
  - Only include files that have a related asset in a tree rooted at any of these root assetIds.
- `assetSubtreeIds` (unknown, 任意)
  - Only include files that have a related asset in a subtree rooted at any of these assetIds (including the roots given). If the total size of the given subtrees exceeds 100,000 assets, an error will be returned.
- `assetSubtreeExternalIds` (unknown, 任意)
  - Only include files that have a related asset in a subtree rooted at any of these assetExternalIds (including the roots given). If the total size of the given subtrees exceeds 100,000 assets, an error will be returned.
- `minCreatedTime` (unknown, 任意)
- `maxCreatedTime` (unknown, 任意)
- `minLastUpdatedTime` (unknown, 任意)
- `maxLastUpdatedTime` (unknown, 任意)
- `minUploadedTime` (unknown, 任意)
- `maxUploadedTime` (unknown, 任意)
- `minSourceCreatedTime` (unknown, 任意)
  - Include files that have sourceCreatedTime set and with minimum this value.
- `maxSourceCreatedTime` (unknown, 任意)
  - Include files that have sourceCreatedTime set and with maximum this value.
- `minSourceModifiedTime` (unknown, 任意)
  - Include files that have sourceModifiedTime set and with minimum this value.
- `maxSourceModifiedTime` (unknown, 任意)
  - Include files that have sourceModifiedTime set and with maximum this value.
- `externalIdPrefix` (unknown, 任意)
- `uploaded` (boolean, 任意)
  - Whether or not the actual file is uploaded. This field is returned only by the API, it has no effect in a post body.
- `` (unknown, 任意)

**レスポンス**:

- `200`: 
- `400`: 

---

#### POST

**概要**: Upload file



> **Required capabilities:** `filesAcl:WRITE`

Create metadata information and get an uploadUrl for a file.

To upload the file, send an HTTP PUT request to the uploadUrl from the response, with the relevant 'Content-Type' and 'Content-Length' headers.

If the uploadUrl contains the string '/v1/files/gcs_proxy/', you can make a Google Cloud Storage (GCS) resumable upload request
as documented in https://cloud.google.com/storage/docs/json_api/v1/how-tos/resumable-upload.

The uploadUrl expires after one week.
Any file info entry that does not have the actual file uploaded within one week will be automatically deleted.

Note: The uploadUrl from initFileUpload supports files smaller than 5 GiB.

The initMultiPartUpload and completeMultiPartUpload endpoints provides an alternative way to upload files, both small and large, up to 1 TiB in size.
These endpoints exposes a uniform multipart upload API for all cloud vendor environments for CDF. Optionally parallel part uploads can be used, for faster uploads.

### Request throttling
This endpoint is subject to the new throttling policy, which enforces limits on both request rate and concurrency.
For more details, please refer to the [Files resource documentation](../../).

**パラメータ**:

- `Origin` (string, 任意)
  - The 'Origin' header parameter is required if there is a Cross Origin issue.
- `overwrite` (boolean, 任意)
  - If 'overwrite' is set to true, and the POST body content specifies a 'externalId' field, fields for the file found for externalId can be overwritten. The default setting is false.

If metadata is included in the request body, all of the original metadata will be overwritten.
The actual file will be overwritten after a successful upload with the uploadUrl from the response.
If there is no successful upload, the current file contents will be kept.

File-Asset mappings only change if explicitly stated in the assetIds field of the POST json body.
Do not set assetIds in request body if you want to keep the current file-asset mappings.

**リクエストボディ**:

Fields to be set for the file.

**レスポンス**:

- `201`: 
- `400`: 

---

### /files/aggregate

#### POST

**概要**: Aggregate files



> **Required capabilities:** `filesAcl:READ`

Calculate aggregates for files, based on optional filter specification.
Returns the following aggregates: `count`

### Request throttling
This endpoint is intended for data analytics and exploration purposes. It is not designed to support high-throughput data retrieval.
Please note that this endpoint is subject to the new throttling policy, which imposes limits on both request rate and concurrency.
For more details, please refer to the [Files resource documentation](../../).

**リクエストボディ**:

Files aggregate request body

**レスポンス**:

- `200`: 
- `400`: 

---

### /files/byids

#### POST

**概要**: Retrieve files



> **Required capabilities:** `filesAcl:READ`

Retrieves metadata information about multiple specific files in the same project.
Results are returned in the same order as in the request. This operation does not return the file contents.

### Request throttling
Please note that this endpoint is subject to the new throttling policy, which imposes limits on both request rate and concurrency.
For more details, please refer to the [Files resource documentation](../../).

**リクエストボディ**:

List of IDs of files to retrieve. Must be up to a maximum of 1000 IDs, and all of them must be unique.

**レスポンス**:

- `200`: 
- `400`: 

---

### /files/completemultipartupload

#### POST

**概要**: Complete multipart upload



> **Required capabilities:** `filesAcl:WRITE`

Completes a multipart file upload.
This endpoint must be called by the client when an 'initMultiPartUpload' operation (POST /files/initmultipartupload) was called,
and all file content parts have been successfully uploaded using PUT for all upload URLs.
Either `id` or `externalId` must be specified in the request body, but not both. The `uploadId` is also required.
The values for these properties can be retrieved from the response of the initMultiPartUpload operation.

### Request throttling
Please note that this endpoint is subject to the new throttling policy, which imposes limits on both request rate and concurrency.
For more details, please refer to the [Files resource documentation](../../).

**リクエストボディ**:

The JSON request body which specifies which file id/externalId and uploadId to complete the multipart upload for.

**レスポンス**:

- `200`: 
- `400`: 

---

### /files/delete

#### POST

**概要**: Delete files



> **Required capabilities:** `filesAcl:WRITE`

Deletes the files with the given ids.

A maximum of 1000 files can be deleted per request.

### Request throttling
Please note that this endpoint is subject to the new throttling policy, which imposes limits on both request rate and concurrency.
For more details, please refer to the [Files resource documentation](../../).

**リクエストボディ**:

List of IDs of files to delete.

**レスポンス**:

- `200`: 
- `400`: 

---

### /files/downloadlink

#### POST

**概要**: Download files



> **Required capabilities:** `filesAcl:READ`

Retrieves a list of download URLs for the specified list of file IDs.
After getting the download links, the client has to issue a GET request to the returned URLs,
which will respond with the contents of the file. The links will by default expire after 30 seconds.
If providing the query parameter extendedExpiration the links will expire after 1 hour.

### Request throttling
Please note that this endpoint is subject to the new throttling policy, which imposes limits on both request rate and concurrency.
For more details, please refer to the [Files resource documentation](../../).

**パラメータ**:

- `extendedExpiration` (boolean, 任意)

**リクエストボディ**:

List of file IDs to retrieve the download URL for.

**レスポンス**:

- `200`: 
- `400`: 

---

### /files/icon

#### GET

**概要**: Get icon



> **Required capabilities:** `filesAcl:READ`

The GET /files/icon operation can be used to get an image representation of a file.

Either id or externalId must be provided as a query parameter (but not both).
Supported file formats:
- Normal jpeg and png files are currently fully supported.
- Other image file formats might work, but continued support for these are not guaranteed.
- Currently only supporting thumbnails for image files.
Attempts to get icon for unsupported files will result in status 400.

### Request throttling
Please note that this endpoint is subject to the new throttling policy, which imposes limits on both request rate and concurrency.
For more details, please refer to the [Files resource documentation](../../).

**パラメータ**:

- `id` (unknown, 任意)
- `externalId` (unknown, 任意)
- `space` (unknown, 任意)
- `instanceExternalId` (unknown, 任意)

**レスポンス**:

- `200`: Thumbnail image (JPEG)
- `400`: 

---

### /files/initmultipartupload

#### POST

**概要**: Upload multipart file



> **Required capabilities:** `filesAcl:WRITE`

Multipart file upload enables upload of files larger than 5 GiB, using a uniform API on all cloud environments for CDF.

Each file part must be larger than 5 MiB, and smaller than 4000 MiB. The file part for the last uploadURL can be smaller than 5 MiB. Maximum 250 upload URLs can be requested.
The supported maximum size for each file uploaded with the multi-part upload API is therefore 1000 GiB (1.073 TB / 0.976 TiB).
The client should calculate the ideal number of parts depending on predetermined or estimated file size, between 1 and the maximum.
Specify the number of parts in the `parts` URL query parameter. The `parts` parameter is required.

The request creates metadata information for a new file, and returns in addition to the file `id`, also a `uploadId`, and a list of `uploadUrls` for uploading the file contents.
To upload a file, send an HTTP PUT request to each of the uploadUrls, with the corresponding part of the file in the request body.
You may use a 'Content-Length' header in the PUT request for each part, but this is not required.
A failed part PUT upload can be retried.

The client must ensure that the parts of the source file are stored in the correct order, using the order of the `uploadUrls` as specified in the response.
This to avoid ending up with a corrupt final file.

The parts can optionally be uploaded in parallel, preferably on a subset of parts at a time, for example maximum 3 concurrent PUT operations.

Once all file parts have been uploaded, the client should call the 'files/completemultipartupload' endpoint,
with the required file ID (as `id` or `externalId`) and `uploadId` fields in the request body. This will assemble the parts into one file.
The file's `uploaded` flag will then eventually be set to `true`.

A standard sequence of calls to upload a large file with multipart upload would be for example as follows:
1. POST files/initmultipartupload?parts=8, to start a multipart upload session with 8 parts. Expect a 201 CREATED response code, and a response body with information to be used in the part uploads and completemultipartupload requests.
2. PUT `uploadUrl`, for each of the `uploadUrls` in the response from files/initmultipartupload. Expect a 200 OK or 201 CREATED response for each PUT request.
3. POST files/completemultipartupload, with request body '{ "id":123456789, "uploadId":"ABCD4321EFGH" }'. This will assemble the file. Expect a 200 OK response.

Consider verifying that the file is eventually marked as uploaded with a call to the getFileByInternalId endpoint.

NOTE: The uploadUrls expires after one week.
A file that does not have the file content parts uploaded and completed within one week will be automatically deleted.

### Request throttling
Please note that this endpoint is subject to the new throttling policy, which imposes limits on both request rate and concurrency.
For more details, please refer to the [Files resource documentation](../../).

**パラメータ**:

- `overwrite` (boolean, 任意)
  - If 'overwrite' is set to true, and the POST body content specifies a 'externalId' field, fields for the file found for externalId can be overwritten. The default setting is false.

If metadata is included in the request body, all of the original metadata will be overwritten.
The actual file will be overwritten after a successful upload with the uploadUrls from the response.
If there is no successful upload, the current file contents will be kept.

File-Asset mappings only change if explicitly stated in the assetIds field of the POST json body.
Do not set assetIds in request body if you want to keep the current file-asset mappings.
- `parts` (integer, 必須)
  - The 'parts' parameter specifies how many uploadURLs should be returned, for uploading the file contents in parts. See main endpoint description for more details.

**リクエストボディ**:

Fields to be set for the file.

**レスポンス**:

- `201`: 
- `400`: 

---

### /files/list

#### POST

**概要**: Filter files



> **Required capabilities:** `filesAcl:READ`

Retrieves a list of all files in a project. Criteria can be supplied to
select a subset of files. This operation supports pagination with cursors.

### Request throttling
This endpoint is intended for data analytics and exploration purposes. It is not designed to support high-throughput data retrieval.
Please note that this endpoint is subject to the new throttling policy, which imposes limits on both request rate and concurrency.
For more details, please refer to the [Files resource documentation](../../).

**リクエストボディ**:

The project name

**レスポンス**:

- `200`: 
- `400`: 

---

### /files/multiuploadlink

#### POST

**概要**: Get multipart file upload link

Multipart file upload enables upload of files larger than 5 GiB, using a uniform API on all cloud environments for CDF.

Each file part must be larger than 5 MiB, and smaller than 4000 MiB. The file part for the last uploadURL can be smaller than 5 MiB. Maximum 250 upload URLs can be requested.
The supported maximum size for each file uploaded with the multi-part upload API is therefore 1000 GiB (1.073 TB / 0.976 TiB).
The client should calculate the ideal number of parts depending on predetermined or estimated file size, between 1 and the maximum.
Specify the number of parts in the `parts` URL query parameter. The `parts` parameter is required.

The request returns in addition to the file `id`, also a `uploadId`, and a list of `uploadUrls` for uploading the file contents.
To upload a file, send an HTTP PUT request to each of the uploadUrls, with the corresponding part of the file in the request body.
You may use a 'Content-Length' header in the PUT request for each part, but this is not required.
A failed part PUT upload can be retried.

The client must ensure that the parts of the source file are stored in the correct order, using the order of the `uploadUrls` as specified in the response.
This to avoid ending up with a corrupt final file.

The parts can optionally be uploaded in parallel, preferably on a subset of parts at a time, for example maximum 3 concurrent PUT operations.

Once all file parts have been uploaded, the client should call the 'files/completemultipartupload' endpoint,
with the required file ID (as `id` or `externalId`) and `uploadId` fields in the request body. This will assemble the parts into one file.
The file's `uploaded` flag will then eventually be set to `true`.

A standard sequence of calls to upload a large file with multipart upload would be for example as follows:
1. POST files/initmultipartupload?parts=8, to start a multipart upload session with 8 parts. Expect a 201 CREATED response code, and a response body with information to be used in the part uploads and completemultipartupload requests.
2. PUT `uploadUrl`, for each of the `uploadUrls` in the response from files/initmultipartupload. Expect a 200 OK or 201 CREATED response for each PUT request.
3. POST files/completemultipartupload, with request body '{ "id":123456789, "uploadId":"ABCD4321EFGH" }'. This will assemble the file. Expect a 200 OK response.

Consider verifying that the file is eventually marked as uploaded with a call to the getFileByInternalId endpoint.

NOTE: The uploadUrls expires after one week.
A file that does not have the file content parts uploaded and completed within one week will be automatically deleted.

### Request throttling
Please note that this endpoint is subject to the new throttling policy, which imposes limits on both request rate and concurrency.
For more details, please refer to the [Files resource documentation](../../).

**パラメータ**:

- `parts` (integer, 必須)
  - The 'parts' parameter specifies how many uploadURLs should be returned, for uploading the file contents in parts. See main endpoint description for more details.

**リクエストボディ**:

Fields to be set for the file.

**レスポンス**:

- `201`: 
- `400`: 

---

### /files/search

#### POST

**概要**: Search files



> **Required capabilities:** `filesAcl:READ`

Search for files based on relevance.
You can also supply a strict match filter as in Filter files, and search in the results from the filter.
Returns first 1000 results based on relevance. This operation does not support pagination.

### Request throttling
This endpoint is intended for data analytics and exploration purposes. It is not designed to support high-throughput data retrieval.
Please note that this endpoint is subject to the new throttling policy, which imposes limits on both request rate and concurrency.
For more details, please refer to the [Files resource documentation](../../).

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 

---

### /files/update

#### POST

**概要**: Update files



> **Required capabilities:** `filesAcl:WRITE`

Updates the information for the files specified in the request body.

If you want to update the file content, uploaded using the uploadUrl, please
use the initFileUpload request with the query parameter 'overwrite=true'.
Alternatively, delete and recreate the file.

For primitive fields (String, Long, Int), use 'set': 'value' to update
value; use 'setNull': true to set that field to null.

For the Json Array field (e.g. assetIds and securityCategories): Use either only 'set', or a combination of 'add' and/or 'remove'.

__AssetIds update examples__:

Example request body to overwrite assetIds with a new set, asset ID 1 and 2.

```
{
  "items": [
    {
      "id": 1,
      "update": {
        "assetIds" : {
          "set" : [ 1, 2 ]
        }
      }
    }
  ]
}
```

Example request body to add one asset Id, and remove another asset ID.

```
{
  "items": [
    {
      "id": 1,
      "update": {
        "assetIds" : {
          "add" : [ 3 ],
          "remove": [ 2 ]
        }
      }
    }
  ]
}
```

__Metadata update examples__:

Example request body to overwrite metadata with a new set.
```
{
  "items": [
    {
      "id": 1,
      "update": {
        "metadata": {
          "set": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      }
    }
  ]
}
```

Example request body to add two key-value pairs and remove two other key-value pairs by key for
the metadata field.
```
{
  "items": [
    {
      "id": 1,
      "update": {
        "metadata": {
          "add": {
            "key3": "value3",
            "key4": "value4"
          },
          "remove": [
            "key1",
            "key2"
          ]
        }
      }
    }
  ]
}
```

### Request throttling
Please note that this endpoint is subject to the new throttling policy, which imposes limits on both request rate and concurrency.
For more details, please refer to the [Files resource documentation](../../).

**リクエストボディ**:

The JSON request body which specifies which files and fields to update.

**レスポンス**:

- `200`: 
- `400`: 

---

### /files/uploadlink

#### POST

**概要**: Get upload file link

Get an uploadUrl for a file.

To upload the file, send an HTTP PUT request to the uploadUrl from the response, with the relevant 'Content-Type' and 'Content-Length' headers.

If the uploadUrl contains the string '/v1/files/gcs_proxy/', you can make a Google Cloud Storage (GCS) resumable upload request
as documented in https://cloud.google.com/storage/docs/json_api/v1/how-tos/resumable-upload.

The uploadUrl expires after one week.
Any file info entry that does not have the actual file uploaded within one week will be automatically deleted.

Note: The uploadUrl from getUploadLink supports files smaller than 5 GiB.

The getMultiPartUploadLink and completeMultiPartUpload endpoints provides an alternative way to upload files, both small and large, up to 1 TiB in size.
These endpoints exposes a uniform multipart upload API for all cloud vendor environments for CDF. Optionally parallel part uploads can be used, for faster uploads.

### Request throttling
Please note that this endpoint is subject to the new throttling policy, which imposes limits on both request rate and concurrency.
For more details, please refer to the [Files resource documentation](../../).

**パラメータ**:

- `Origin` (string, 任意)
  - The 'Origin' header parameter is required if there is a Cross Origin issue.

**リクエストボディ**:

Fields to be set for the file.

**レスポンス**:

- `201`: 
- `400`: 

---

### /files/{id}

#### GET

**概要**: Retrieve a file by its ID



> **Required capabilities:** `filesAcl:READ`

Returns file info for the file ID

### Request throttling
Please note that this endpoint is subject to the new throttling policy, which imposes limits on both request rate and concurrency.
For more details, please refer to the [Files resource documentation](../../).

**パラメータ**:

- `id` (unknown, 必須)

**レスポンス**:

- `200`: 
- `400`: 

---

### /functions

#### GET

**概要**: List functions



> **Required capabilities:** `functionsAcl:READ`

List functions.

**パラメータ**:

- `` (unknown, 任意)

**レスポンス**:

- `200`: 
- `400`: 

---

#### POST

**概要**: Create functions



> **Required capabilities:** `functionsAcl:WRITE`

You can only create one function per request.

**リクエストボディ**:



**レスポンス**:

- `201`: 
- `400`: 

---

### /functions/byids

#### POST

**概要**: Retrieve functions



> **Required capabilities:** `functionsAcl:READ`

Retrieve functions by ids.

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 

---

### /functions/delete

#### POST

**概要**: Delete functions



> **Required capabilities:** `functionsAcl:WRITE`

Delete functions. You can delete a maximum of 10 functions per request. Function source files stored in the Files API must be deleted separately.

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 

---

### /functions/limits

#### GET

**概要**: Functions limits



> **Required capabilities:** `functionsAcl:READ`

Service limits for the associated project.

**レスポンス**:

- `200`: 
- `400`: 

---

### /functions/list

#### POST

**概要**: Filter functions

Use advanced filtering options to find functions.

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 

---

### /functions/schedules

#### GET

**概要**: List schedules



> **Required capabilities:** `functionsAcl:READ`

List function schedules in project.

**パラメータ**:

- `` (unknown, 任意)

**レスポンス**:

- `200`: 
- `400`: 

---

#### POST

**概要**: Create schedules



> **Required capabilities:** `functionsAcl:WRITE`

Create function schedules. Function schedules trigger asynchronous calls with specific input data, based on a cron expression that determines when these triggers should be fired. Use e.g. http://www.cronmaker.com to be guided on how to generate a cron expression. One of `FunctionId` or `FunctionExternalId` (deprecated) must be set (but not both). When creating a schedule with a session, i.e. with a `nonce`, `FunctionId` must be used. The `nonce` will be used to bind the session before function execution, and the session will be kept alive for the lifetime of the schedule. **WARNING:** Secrets or other confidential information should not be passed via the `data` object. There is a dedicated `secrets` object in the request body to "Create functions" for this purpose.

**リクエストボディ**:



**レスポンス**:

- `201`: 
- `400`: 

---

### /functions/schedules/byids

#### POST

**概要**: Retrieve schedules



> **Required capabilities:** `functionsAcl:READ`

Retrieve function schedules by schedule ids.

**リクエストボディ**:

List of IDs of schedules to retrieve. Must be up to a maximum of 10000 items and all of them must be unique.

**レスポンス**:

- `200`: 
- `400`: 

---

### /functions/schedules/delete

#### POST

**概要**: Delete schedules



> **Required capabilities:** `functionsAcl:WRITE`

Delete function schedules.

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 

---

### /functions/schedules/list

#### POST

**概要**: Filter function schedules

Use advanced filtering options to find function schedules.  At most one of `FunctionId` or `FunctionExternalId` can be specified.

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 

---

### /functions/schedules/{scheduleId}

#### GET

**概要**: Retrieve a function schedule by its id



> **Required capabilities:** `functionsAcl:READ`

Retrieve a function schedule by its id.

**レスポンス**:

- `200`: OK
- `400`: 

---

### /functions/schedules/{scheduleId}/input_data

#### GET

**概要**: Retrieve function input data



> **Required capabilities:** `functionsAcl:READ`

Retrieve the input data to the associated function.

**レスポンス**:

- `200`: 
- `400`: 

---

### /functions/status

#### GET

**概要**: Get activation status



> **Required capabilities:** `functionsAcl:READ`

Get activation status

**レスポンス**:

- `200`: 
- `400`: 

---

#### POST

**概要**: Activate Functions



> **Required capabilities:** `functionsAcl:WRITE`

Activate Cognite Functions. This will create the necessary backend infrastructure for Cognite Functions.

**レスポンス**:

- `202`: 
- `400`: 

---

### /functions/{functionId}

#### GET

**概要**: Retrieve a function by its id



> **Required capabilities:** `functionsAcl:READ`

Retrieve a function by its id. If you want to retrieve functions by names, use Retrieve functions instead.

**レスポンス**:

- `200`: OK
- `400`: 

---

### /functions/{functionId}/call

#### POST

**概要**: Call a function asynchronously



> **Required capabilities:** `functionsAcl:WRITE`

Perform a function call. To provide input data to the function, add the data in an object called `data` in the request body. It will be available as the `data` argument into the function. Info about the function call at runtime can be obtained through the `function_call_info` argument if added in the function handle. **WARNING:** Secrets or other confidential information should not be passed via the `data` object. There is a dedicated `secrets` object in the request body to "Create functions" for this purpose.

**リクエストボディ**:

**レスポンス**:

- `201`: 
- `400`: 

---

### /functions/{functionId}/calls

#### GET

**概要**: List function calls



> **Required capabilities:** `functionsAcl:READ`

List function calls.

**レスポンス**:

- `200`: 
- `400`: 

---

### /functions/{functionId}/calls/byids

#### POST

**概要**: Retrieve calls



> **Required capabilities:** `functionsAcl:READ`

Retrieve function calls by call ids.

**リクエストボディ**:

List of IDs of calls to retrieve. Must be up to a maximum of 10000 items and all of them must be unique.

**レスポンス**:

- `200`: 
- `400`: 

---

### /functions/{functionId}/calls/list

#### POST

**概要**: Filter function calls

Use advanced filtering options to find function calls.

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 

---

### /functions/{functionId}/calls/{callId}

#### GET

**概要**: Retrieve a function call by its id



> **Required capabilities:** `functionsAcl:READ`

Retrieve function calls.

**レスポンス**:

- `200`: OK
- `400`: 

---

### /functions/{functionId}/calls/{callId}/logs

#### GET

**概要**: Retrieve logs for function call



> **Required capabilities:** `functionsAcl:READ`

Get logs from a function call.

**レスポンス**:

- `200`: 
- `400`: 

---

### /functions/{functionId}/calls/{callId}/response

#### GET

**概要**: Retrieve response for function call



> **Required capabilities:** `functionsAcl:READ`

Retrieve response from a function call.

**パラメータ**:


**レスポンス**:

- `200`: 
- `400`: 

---

### /geospatial/compute

#### POST

**概要**: Compute



> **Required capabilities:** `geospatialAcl:READ` `geospatialCrsAcl:READ`

Compute custom json output structures or well known binary format responses based on calculation or selection of feature properties or direct values given in the request.

**リクエストボディ**:

**レスポンス**:

- `200`: 

---

### /geospatial/crs

#### GET

**概要**: List Coordinate Reference Systems



> **Required capabilities:** `geospatialCrsAcl:READ`

List the defined Coordinate Reference Systems. The list can be limited to the custom Coordinate Reference Systems defined for the tenant.

**パラメータ**:

- `` (unknown, 任意)

**レスポンス**:

- `200`: 
- `400`: 

---

#### POST

**概要**: Create Coordinate Reference Systems



> **Required capabilities:** `geospatialCrsAcl:WRITE`

Creates custom Coordinate Reference Systems.

**リクエストボディ**:

List of custom Coordinate Reference Systems to be created.

**レスポンス**:

- `201`: 
- `400`: 

---

### /geospatial/crs/byids

#### POST

**概要**: Get Coordinate Reference Systems



> **Required capabilities:** `geospatialCrsAcl:READ`

Get Coordinate Reference Systems by their Spatial Reference IDs

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 

---

### /geospatial/crs/delete

#### POST

**概要**: Delete Coordinate Reference Systems



> **Required capabilities:** `geospatialCrsAcl:WRITE`

Delete custom Coordinate Reference Systems.

**リクエストボディ**:

List of custom Coordinate Reference Systems to be deleted.

**レスポンス**:

- `200`: 
- `400`: 

---

### /geospatial/featuretypes

#### POST

**概要**: Create feature types



> **Required capabilities:** `geospatialAcl:WRITE`

Creates feature types. Each tenant can have up to 100 feature types.

**リクエストボディ**:

List of feature types to be created. It is possible to create up to 100 feature types in one request provided the total number of feature types on the tenant will not exceed 100.

**レスポンス**:

- `201`: 
- `400`: 

---

### /geospatial/featuretypes/byids

#### POST

**概要**: Retrieve feature types



> **Required capabilities:** `geospatialAcl:READ`

Retrieves feature types by external ids. It is possible to retrieve up to 100 items per request, i.e. the maximum number of feature types for a tenant.

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 

---

### /geospatial/featuretypes/delete

#### POST

**概要**: Delete feature types



> **Required capabilities:** `geospatialAcl:WRITE`

Delete feature types.

**リクエストボディ**:

List of feature types to be deleted. It is possible to delete a maximum of 10 feature types per request. Feature types must not have related features. Feature types with related features can be deleted using force flag.

**レスポンス**:

- `200`: 
- `400`: 

---

### /geospatial/featuretypes/list

#### POST

**概要**: List feature types



> **Required capabilities:** `geospatialAcl:READ`

List all feature types

**レスポンス**:

- `200`: 
- `400`: 

---

### /geospatial/featuretypes/update

#### POST

**概要**: Update feature types



> **Required capabilities:** `geospatialAcl:READ` `geospatialAcl:WRITE`

Update one or more feature types

**リクエストボディ**:

List of feature types to be updated. It is possible to add and remove properties and indexes. WARNING: removing properties will result in data loss in corresponding features.

**レスポンス**:

- `200`: 
- `400`: 

---

### /geospatial/featuretypes/{featureTypeExternalId}/features

#### GET

**概要**: Get features



> **Required capabilities:** `geospatialAcl:READ`

Get features with paging support

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)
- `limit` (unknown, 任意)
- `allowCrsTransformation` (unknown, 任意)
- `allowDimensionalityMismatch` (unknown, 任意)

**レスポンス**:

- `200`: 
- `400`: 

---

#### POST

**概要**: Create features



> **Required capabilities:** `geospatialAcl:READ` `geospatialAcl:WRITE`

Create features

**パラメータ**:

- `` (unknown, 任意)

**リクエストボディ**:

**レスポンス**:

- `201`: 
- `400`: 

---

### /geospatial/featuretypes/{featureTypeExternalId}/features/aggregate

#### POST

**概要**: Aggregate features



> **Required capabilities:** `geospatialAcl:READ`

Search for features based on the feature property filter and perform requested aggregations on a given property. Aggregations are supported for all filters that do not contain `stWithin`, `stWithinProperly`, `stContains` and `stContainsProperly` search in 3D geometries.

**パラメータ**:

- `` (unknown, 任意)

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 

---

### /geospatial/featuretypes/{featureTypeExternalId}/features/byids

#### POST

**概要**: Retrieve features



> **Required capabilities:** `geospatialAcl:READ`

Retrieves features by external ids. It is possible to retrieve up to 1000 items per request.

**パラメータ**:

- `` (unknown, 任意)

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 

---

### /geospatial/featuretypes/{featureTypeExternalId}/features/delete

#### POST

**概要**: Delete features



> **Required capabilities:** `geospatialAcl:READ` `geospatialAcl:WRITE`

Delete features.

**パラメータ**:

- `` (unknown, 任意)

**リクエストボディ**:

List of features to be deleted. It is possible to post a maximum of 1000 items per request.

**レスポンス**:

- `200`: 
- `400`: 

---

### /geospatial/featuretypes/{featureTypeExternalId}/features/list

#### POST

**概要**: Filter all features



> **Required capabilities:** `geospatialAcl:READ`

List features based on the feature property filter passed in the body of the request. This operation supports pagination by cursor.

**パラメータ**:

- `` (unknown, 任意)

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 

---

### /geospatial/featuretypes/{featureTypeExternalId}/features/search

#### POST

**概要**: Search features



> **Required capabilities:** `geospatialAcl:READ`

Search for features based on the feature property filter passed in the body of the request. The result of the search is limited to a maximum of 1000 items. Results in excess of the limit are truncated. This means that the complete result set of the search cannot be retrieved with this method. However, for a given unmodified feature collection, the result of the search is deterministic and does not change over time.

**パラメータ**:

- `` (unknown, 任意)

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 

---

### /geospatial/featuretypes/{featureTypeExternalId}/features/search-streaming

#### POST

**概要**: Search and stream features



> **Required capabilities:** `geospatialAcl:READ`

Search for features based on the feature property filter passed in the body of the request. The streaming response format can be length prefixed, new line delimited, record separator delimited or concatenated depending on requested output (see https://en.wikipedia.org/wiki/JSON_streaming).

**パラメータ**:

- `` (unknown, 任意)

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 

---

### /geospatial/featuretypes/{featureTypeExternalId}/features/update

#### POST

**概要**: Update features



> **Required capabilities:** `geospatialAcl:READ` `geospatialAcl:WRITE`

Update features. This is a replace operation, i.e., all feature properties have to be sent in the request body even if their values do not change.

**パラメータ**:

- `` (unknown, 任意)

**リクエストボディ**:

List of features to update.

**レスポンス**:

- `200`: 
- `400`: 

---

### /geospatial/featuretypes/{featureTypeExternalId}/features/{featureExternalId}/rasters/{rasterPropertyName}

#### POST

**概要**: Get a raster from a feature property



> **Required capabilities:** `geospatialAcl:READ`

Get a raster from a feature property. The feature property must be of type RASTER.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)

**リクエストボディ**:

**レスポンス**:

- `200`: The binary file of the raster in the specified format
- `400`: 

---

#### PUT

**概要**: Put a raster into a feature property



> **Required capabilities:** `geospatialAcl:READ` `geospatialAcl:WRITE`

Put a raster into a feature property. The feature property must be of type RASTER.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 

---

### /geospatial/featuretypes/{featureTypeExternalId}/features/{featureExternalId}/rasters/{rasterPropertyName}/delete

#### POST

**概要**: Delete a raster from a feature property



> **Required capabilities:** `geospatialAcl:READ` `geospatialAcl:WRITE`

Delete a raster from a feature property. If there is no raster already, the operation is a no-op.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: 
- `400`: 

---

### /groups

#### GET

**概要**: List groups



> **Required capabilities:** `groupsAcl:LIST`

Retrieves a list of groups the asking principal a member of. Principals with groups:list capability can optionally ask for all groups in a project.

**パラメータ**:

- `all` (boolean, 任意)
  - Whether to get all groups, only available with the groups:list acl.

**レスポンス**:

- `200`: A list of groups.

---

#### POST

**概要**: Create groups



> **Required capabilities:** `groupsAcl:CREATE`

Creates one or more named groups, each with a set of capabilities. All users with any group membership in a CDF project automatically get the `userProfilesAcl:READ` capability and can search for other users. If not assigned automatically, user profiles must be enabled for the project. To enable user profiles, contact [support](https://support.cognite.com/).

**リクエストボディ**:

List of groups to create.

**レスポンス**:

- `201`: A list of the created groups.
- `400`: 

---

### /groups/delete

#### POST

**概要**: Delete groups



> **Required capabilities:** `groupsAcl:DELETE`

Deletes the groups with the given IDs.

**リクエストボディ**:

List of group IDs to delete

**レスポンス**:

- `200`: 

---

### /hostedextractors/destinations

#### GET

**概要**: List Destinations



> **Required capabilities:** `hostedExtractors:READ`

List all destinations in a given project. If more than `limit` destinations exist, a cursor for pagination will be returned with the response.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: List of destinations and an optional cursor.
- `400`: 
- `422`: Validation Error

---

#### POST

**概要**: Create Destinations



> **Required capabilities:** `hostedExtractors:WRITE`

Create up to 100 destinations.

**リクエストボディ**:

Destinations to create.

**レスポンス**:

- `200`: List of created destinations
- `400`: Response for a failed request
- `422`: 

---

### /hostedextractors/destinations/byIds

#### POST

**概要**: Retrieve Destinations



> **Required capabilities:** `hostedExtractors:READ`

Retrieve a list of up to 100 destinations by their external ID, optionally ignoring unknown IDs.

**リクエストボディ**:

List of external IDs of destinations to retrieve.

**レスポンス**:

- `200`: List of retrieved destinations.
- `400`: 
- `422`: 

---

### /hostedextractors/destinations/delete

#### POST

**概要**: Delete Destinations



> **Required capabilities:** `hostedExtractors:WRITE`

Delete a list of destinations by their external ID. This operation will fail if any destination in the request is associated with a job, unless the `force` query parameter is set to `true`.

**リクエストボディ**:

List of external IDs of destinations to delete.

**レスポンス**:

- `200`: Empty response
- `400`: 
- `422`: 

---

### /hostedextractors/destinations/update

#### POST

**概要**: Update Destinations



> **Required capabilities:** `hostedExtractors:WRITE`

Update up to 100 destinations.

**リクエストボディ**:

Destinations to update.

**レスポンス**:

- `200`: List of updated destinations.
- `400`: 
- `422`: 

---

### /hostedextractors/jobs

#### GET

**概要**: List Jobs



> **Required capabilities:** `hostedExtractors:READ`

List all jobs in a given project. If more than `limit` jobs exist, a cursor for pagination will be returned with the response.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)
- `source` (string, 任意)
  - External ID of source the returned jobs must be tied to.
- `destination` (string, 任意)
  - External ID of destination the returned jobs must be tied to.
- `mapping` (string, 任意)
  - External ID of mapping the returned jobs must be tied to.

**レスポンス**:

- `200`: List of jobs and an optional cursor.
- `400`: 
- `422`: 

---

#### POST

**概要**: Create Jobs



> **Required capabilities:** `hostedExtractors:WRITE`

Create up to 100 jobs.

**リクエストボディ**:

Jobs to create.

**レスポンス**:

- `200`: Successful Response
- `400`: 
- `422`: 

---

### /hostedextractors/jobs/byIds

#### POST

**概要**: Retrieve Jobs



> **Required capabilities:** `hostedExtractors:READ`

Retrieve a list of up to 100 jobs by their external ID, optionally ignoring unknown IDs.

**リクエストボディ**:

List of external IDs of jobs to retrieve.

**レスポンス**:

- `200`: List of retrieved jobs.
- `400`: 
- `422`: 

---

### /hostedextractors/jobs/delete

#### POST

**概要**: Delete Jobs



> **Required capabilities:** `hostedExtractors:WRITE`

Delete a list of jobs by their external ID.

**リクエストボディ**:

List of external IDs of jobs to delete.

**レスポンス**:

- `200`: Empty response
- `400`: 
- `422`: 

---

### /hostedextractors/jobs/logs

#### GET

**概要**: Get Job Logs



> **Required capabilities:** `hostedExtractors:READ`

List logs, optionally filtering on job, source, or destination. Logs are retrieved in reverse chronological order.

**パラメータ**:

- `job` (string, 任意)
  - Require returned logs to belong to the job given by this external ID.
- `` (unknown, 任意)
- `` (unknown, 任意)
- `source` (string, 任意)
  - Require returned logs to belong to the any job with source given by this external ID.
- `destination` (string, 任意)
  - Require returned logs to belong to the any job with destination given by this external ID.

**レスポンス**:

- `200`: List of retrieved job logs
- `400`: 
- `422`: 

---

### /hostedextractors/jobs/metrics

#### GET

**概要**: Get Job Metrics



> **Required capabilities:** `hostedExtractors:READ`

List metrics, optionally filtering on job, source, or destination. Logs are retrieved in reverse chronological order.

**パラメータ**:

- `job` (string, 任意)
  - Require returned metrics to belong to the job given by this external ID.
- `` (unknown, 任意)
- `` (unknown, 任意)
- `source` (string, 任意)
  - Require returned metrics to belong to the any job with source given by this external ID.
- `destination` (string, 任意)
  - Require returned metrics to belong to the any job with destination given by this external ID.

**レスポンス**:

- `200`: List of retrieved job metrics.
- `400`: 
- `422`: 

---

### /hostedextractors/jobs/update

#### POST

**概要**: Update Jobs



> **Required capabilities:** `hostedExtractors:WRITE`

Update a list of up to 100 jobs.

**リクエストボディ**:

Jobs to update.

**レスポンス**:

- `200`: List of updated jobs.
- `400`: 
- `422`: 

---

### /hostedextractors/mappings

#### GET

**概要**: List Mappings



> **Required capabilities:** `hostedExtractors:READ`

List all mappings in a given project. If more than `limit` mappings exist, a cursor for pagination will be returned with the response.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: List of mappings and an optional cursor.
- `400`: 
- `422`: 

---

#### POST

**概要**: Create Mappings



> **Required capabilities:** `hostedExtractors:WRITE`

Create up to 100 mappings.

**リクエストボディ**:

Mappings to create.

**レスポンス**:

- `200`: List of created mappings
- `400`: 
- `422`: 

---

### /hostedextractors/mappings/byIds

#### POST

**概要**: Retrieve Mappings



> **Required capabilities:** `hostedExtractors:READ`

Retrieve a list of up to 100 mappings by their external ID, optionally ignoring unknown IDs.

**パラメータ**:

- `cdf-version` (string, 任意)

**リクエストボディ**:

List of external IDs of mappings to retrieve.

**レスポンス**:

- `200`: List of retrieved mappings.
- `400`: 
- `422`: 

---

### /hostedextractors/mappings/delete

#### POST

**概要**: Delete Mappings



> **Required capabilities:** `hostedExtractors:WRITE`

Delete a list of mappings by their external ID. This operation will fail if any mapping in the request is associated with a job, unless the `force` query parameter is set to `true`.

**リクエストボディ**:

List of external IDs of mappings to delete.

**レスポンス**:

- `200`: Empty response
- `400`: 
- `422`: 

---

### /hostedextractors/mappings/update

#### POST

**概要**: Update Mappings



> **Required capabilities:** `hostedExtractors:WRITE`

Update up to 100 mappings.

**リクエストボディ**:

Mappings to update.

**レスポンス**:

- `200`: List of updated mappings.
- `400`: 
- `422`: 

---

### /hostedextractors/preview

#### POST

**概要**: Create Preview



> **Required capabilities:** `hostedExtractors:WRITE`

Create a preview job. Previews will run for up to 10 minutes, and return once they receive any data. Use the /result endpoint to check the status of the preview.

**リクエストボディ**:

Preview to create.

**レスポンス**:

- `200`: Created preview.
- `400`: 
- `422`: 

---

### /hostedextractors/preview/download

#### POST

**概要**: Download preview result



> **Required capabilities:** `hostedExtractors:READ`

Download the raw, binary result of a preview job, before it is passed throguh any transformations. Note that this endpoint will return 404 if there is no raw data.

**リクエストボディ**:

External ID of preview job to get result for.

**レスポンス**:

- `200`: Raw result from the preview.
- `400`: 
- `422`: 

---

### /hostedextractors/preview/result

#### POST

**概要**: Get preview result



> **Required capabilities:** `hostedExtractors:READ`

Get the result of a preview job. Note that previews may be automatically deleted as soon as 1 hour after they are created.

**リクエストボディ**:

External ID of preview job to get result for.

**レスポンス**:

- `200`: Current status of the preview.
- `400`: 
- `422`: 

---

### /hostedextractors/sources

#### GET

**概要**: List Sources



> **Required capabilities:** `hostedExtractors:READ`

List all sources in a given project. If more than `limit` sources exist, a cursor for pagination will be returned with the response.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: List of sources and an optional cursor.
- `400`: Response for a failed request
- `422`: Validation Error

---

#### POST

**概要**: Create Sources



> **Required capabilities:** `hostedExtractors:READ`

Create up to 100 sources.

**リクエストボディ**:

Sources to create.

**レスポンス**:

- `200`: List of created sources.
- `400`: 
- `422`: 

---

### /hostedextractors/sources/byIds

#### POST

**概要**: Retrieve Sources



> **Required capabilities:** `hostedExtractors:READ`

Retrieve a list of up to 100 sources by their external ID, optionally ignoring unknown IDs.

**リクエストボディ**:

List of external IDs of sources to retrieve.

**レスポンス**:

- `200`: List of retrieved sources.
- `400`: 
- `422`: 

---

### /hostedextractors/sources/delete

#### POST

**概要**: Delete Sources



> **Required capabilities:** `hostedExtractors:WRITE`

Delete a list of sources by their external ID. This operation will fail if any destination in the request is associated with a job, unless the `force` query parameter is set to `true`.

**リクエストボディ**:

List of external IDs of sources to delete.

**レスポンス**:

- `200`: Empty response
- `400`: 
- `422`: 

---

### /hostedextractors/sources/update

#### POST

**概要**: Update Sources



> **Required capabilities:** `hostedExtractors:WRITE`

Update up to 100 sources.

**リクエストボディ**:

Sources to update.

**レスポンス**:

- `200`: List of updated sources.
- `400`: 
- `422`: 

---

### /labels

#### POST

**概要**: Create label definitions.



> **Required capabilities:** `labelsAcl:WRITE`

Creates label definitions that can be used across different resource types. The label definitions are uniquely identified by their external id.

**リクエストボディ**:

List of label definitions to create

**レスポンス**:

- `201`: 
- `400`: 

---

### /labels/byids

#### POST

**概要**: Retrieve labels



> **Required capabilities:** `labelsAcl:READ`

Retrieve labels using their external ids

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 

---

### /labels/delete

#### POST

**概要**: Delete label definitions.



> **Required capabilities:** `labelsAcl:WRITE`

Delete all the label definitions specified by their external ids. The resource items that have the corresponding label attached remain unmodified. It is up to the client to clean up the resource items from their attached labels if necessary.

**リクエストボディ**:

List of external ids of label definitions to delete.

**レスポンス**:

- `200`: 
- `400`: 

---

### /labels/list

#### POST

**概要**: Filter labels



> **Required capabilities:** `labelsAcl:READ`

Use advanced filtering options to find label definitions.

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 

---

### /models/containers

#### GET

**概要**: List containers defined in the project



> **Required capabilities:** `DataModelsAcl:READ`

List of containers defined in the current project. You can filter the list by specifying a space.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: 
- `400`: 

---

#### POST

**概要**: Create or update containers



> **Required capabilities:** `DataModelsAcl:WRITE`

Add or update (upsert) containers. For unchanged container specifications, the operation completes without making any changes.  We will not update the ```lastUpdatedTime``` value for containers that remain unchanged.

**リクエストボディ**:

Containers to add or update.

**レスポンス**:

- `200`: 
- `400`: 
- `409`: View conflict

---

### /models/containers/byids

#### POST

**概要**: Retrieve containers by their external ids



> **Required capabilities:** `DataModelsAcl:READ`

Retrieve up to 100 containers by their specified external ids.

**リクエストボディ**:

List of external-ids of containers to retrieve.

**レスポンス**:

- `200`: 
- `400`: 

---

### /models/containers/constraints/delete

#### POST

**概要**: Delete constraints from containers



> **Required capabilities:** `DataModelsAcl:WRITE`

Delete one or more container constraints. Currently limited to 10 constraints at a time.

**リクエストボディ**:

List of the references to constraints you want to delete.

**レスポンス**:

- `200`: 
- `400`: 

---

### /models/containers/delete

#### POST

**概要**: Delete containers



> **Required capabilities:** `DataModelsAcl:WRITE`

Delete one or more containers. Currently limited to 100 containers at a time.

**リクエストボディ**:

List of the spaces and external-ids for the containers you want to delete.

**レスポンス**:

- `200`: 
- `400`: 

---

### /models/containers/indexes/delete

#### POST

**概要**: Delete indexes from containers



> **Required capabilities:** `DataModelsAcl:WRITE`

Delete one or more container indexes. Currently limited to 10 indexes at a time.

**リクエストボディ**:

List of the references to indexes you want to delete.

**レスポンス**:

- `200`: 
- `400`: 

---

### /models/containers/inspect

#### POST

**概要**: Inspect containers



> **Required capabilities:** `DataModelsAcl:READ`


**リクエストボディ**:

Which containers to inspect and the inspection operations to run.

**レスポンス**:

- `200`: 
- `400`: 

---

### /models/datamodels

#### GET

**概要**: List data models defined in the project



> **Required capabilities:** `DataModelsAcl:READ`

List data models defined in the project. You can filter the returned models by the specified space.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: 
- `400`: 

---

#### POST

**概要**: Create or update data models



> **Required capabilities:** `DataModelsAcl:WRITE`

Add or update (upsert) data models. For unchanged data model specifications, the operation completes without making any changes.  We will not update the ```lastUpdatedTime``` value for models that remain unchanged.

**リクエストボディ**:

List of data models to add

**レスポンス**:

- `200`: 
- `400`: 
- `409`: Data model conflict

---

### /models/datamodels/byids

#### POST

**概要**: Retrieve data models by their external ids



> **Required capabilities:** `DataModelsAcl:READ`

Retrieve up to 100 data models by their external ids. Views can be auto-expanded when the ```InlineViews``` query parameter is set.

**パラメータ**:

- `` (unknown, 任意)

**リクエストボディ**:

List of external-ids of data models to retrieve.

**レスポンス**:

- `200`: 
- `400`: 

---

### /models/datamodels/delete

#### POST

**概要**: Delete data models



> **Required capabilities:** `DataModelsAcl:WRITE`

Delete one or more data models.  Currently limited to 100 models at a time.  This does not delete the views, nor the containers they reference.

**リクエストボディ**:

List of references to data models you wish to delete

**レスポンス**:

- `200`: 
- `400`: 

---

### /models/instances

#### POST

**概要**: Create or update nodes/edges



> **Required capabilities:** `DataModelsAcl:WRITE`

Create or update nodes and edges in a transaction. The ```items``` field of the payload is an array of objects
where each object describes a node or an edge to create, patch or replace. The ```instanceType``` field of
each object must be ```node``` or ```edge``` and determines how the rest of the object is interpreted.

This operation is currently limited to 1000 nodes and/or edges at a time.

Individual nodes and edges are uniquely identified by their externalId and space.

Instances will be returned in the same order as they are supplied in the request.

For more details on ingesting instances into a graph, see [Ingesting instances]
(https://docs.cognite.com/cdf/dm/dm_concepts/dm_ingestion).

### Creating new instances

When there is no node or edge with the given externalId in the given space, a node will be created and the
properties provided for each of the containers or views in the ```sources``` array will be populated for the
node/edge. Nodes can also be created implicitly when an edge between them is created (if
```autoCreateStartNodes``` and/or ``` autoCreateEndNodes``` is set), or when a direct relation
property is set, the target node does not exist and ```autoCreateDirectRelations``` is set.

To add a node or edge, the user must have capabilities to access (write to) both the view(s) referenced in
```sources``` and the container(s) underlying these views, as well as any directly referenced containers.

### Updating (patching) or replacing instances

When a node or edge (instance) with the given externalId already exists in a space, the
properties named in the ```sources``` field will be written to the instance. Other properties will remain
unchanged. To replace the whole set of properties for an instance (a node or an edge) rather than patch the
instance, set the ```replace``` parameter to ```true```.

Note: When using ```replace``` as ```true``` it will replace any omitted property to either it's default
value as defined on the container(s) or null if no default value is set. All properties on the related
container(s) referenced in the provided ```sources``` view(s) will be replaced, so even when the provided
view(s) only contains a subset of properties from a container all the properties in that container for that
instance will be replaced. If the underlying container(s) have any required properties that are not provided,
the operation will fail.

If you use a writable view to update properties (that is, the source you are referring to in ```sources```
is a view), you must have write access to the view as well as all of its backing containers.

### No-change patch operations
When a node/edge item has no changes compared to the existing instance - that is, when the supplied property
values are equal to the corresponding values in the existing node/edge, the node/edge will stay unchanged.
In this case, the ```lastUpdatedTime``` values for the nodes/edges in question will not change.

### Deleting instances
It's also possible to delete instances by passing instance references to the `delete` field. Upserts and
deletes can be combined in the same request and will be executed atomically. The total number of ingested and
deleted items cannot exceed 1000. If an instance doesn’t exist, the delete operation skips it and it won't be
included in the response.


**リクエストボディ**:

Nodes/edges to add or update.

**レスポンス**:

- `200`: 
- `400`: 
- `409`: Ingestion conflict

---

### /models/instances/aggregate

#### POST

**概要**: Aggregate data across nodes/edges



> **Required capabilities:** `DataModelsAcl:READ`

Aggregate data for nodes or edges in a project. You can use an optional query or filter specification to limit the result.

**リクエストボディ**:

Aggregation specification.

**レスポンス**:

- `200`: 
- `400`: 

---

### /models/instances/byids

#### POST

**概要**: Retrieve nodes/edges by their external ids



> **Required capabilities:** `DataModelsAcl:READ`

Retrieve up to 1000 nodes or edges by their external ids.

**リクエストボディ**:

List of external-ids for nodes or edges to retrieve. Properties for **up to 10 unique views** (in total across the external ids requested) can be retrieved in one query.

**レスポンス**:

- `200`: 
- `400`: 

---

### /models/instances/delete

#### POST

**概要**: Delete nodes/edges



> **Required capabilities:** `DataModelsAcl:WRITE`

Delete nodes and edges in a transaction. Limited to 1000 nodes/edges at a time.


When a node is selected for deletion, all connected incoming and outgoing edges that point to or from it are also deleted. However, please note that the operation might fail if the node has a high number of edge connections. If this is the case, consider deleting the edges connected to the node before deleting the node itself.
Nodes and/or edges that do not exist in the graph when the delete request is processed will be ignored. The rationale for this is that the end-state will reflect the desired state regardless of the instance's previous state.
Note that attempting to delete something from a space that does not exist will be considered an invalid request, as the API does not affect spaces themselves, only graph nodes and edges. 

**リクエストボディ**:

List of types, spaces, and external-ids for nodes and edges to delete.

**レスポンス**:

- `200`: 
- `400`: 

---

### /models/instances/inspect

#### POST

**概要**: Inspect instances



> **Required capabilities:** `DataModelsAcl:READ`


**リクエストボディ**:

Change filter specification

**レスポンス**:

- `200`: 
- `400`: 

---

### /models/instances/list

#### POST

**概要**: Filter nodes/edges



> **Required capabilities:** `DataModelsAcl:READ`

Filter the instances - nodes and edges - in a project.

**リクエストボディ**:

Filter based on the instance type, the name, the external-ids, and on properties. The filter supports sorting and pagination. The instances must have data in all the views referenced by the sources field. Properties for up to 10 views can be retrieved in one query.

**レスポンス**:

- `200`: 
- `400`: 

---

### /models/instances/query

#### POST

**概要**: Query nodes/edges



> **Required capabilities:** `DataModelsAcl:READ`

Specification of query endpoint. For more information, see [Query language](https://docs.cognite.com/cdf/dm/dm_concepts/dm_querying).

**リクエストボディ**:

Query specification.

**レスポンス**:

- `200`: 
- `400`: 

---

### /models/instances/search

#### POST

**概要**: Search for nodes/edges



> **Required capabilities:** `DataModels:READ`

Search text fields in views for nodes or edge(s). The service will return up to 1000 results. This operation orders the results by relevance, across the specified spaces.

**リクエストボディ**:

The search specification.

**レスポンス**:

- `200`: 
- `400`: 

---

### /models/instances/sync

#### POST

**概要**: Sync nodes/edges



> **Required capabilities:** `DataModelsAcl:READ`

Subscribe to changes for nodes and edges in a project, matching a supplied filter.  This endpoint will always return a ```NextCursor```.  The sync specification mirrors the query interface, but sorting is not currently supported. For more information, see [Query language](https://docs.cognite.com/cdf/dm/dm_concepts/dm_querying).

**リクエストボディ**:

Change filter specification

**レスポンス**:

- `200`: 
- `400`: 

---

### /models/spaces

#### GET

**概要**: List spaces defined in the project



> **Required capabilities:** `DataModelsAcl:READ`

List spaces defined in the current project.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: 
- `400`: 

---

#### POST

**概要**: Create or update spaces



> **Required capabilities:** `DataModelsAcl:WRITE`

Add or update (upsert) spaces. For unchanged space specifications, the operation completes without making any changes.  We will not update the ```lastUpdatedTime``` value for spaces that remain unchanged.

**リクエストボディ**:

Spaces to add or update.

**レスポンス**:

- `200`: 
- `400`: 
- `409`: Space conflict

---

### /models/spaces/byids

#### POST

**概要**: Retrieve spaces by their space-ids



> **Required capabilities:** `DataModelsAcl:READ`

Retrieve up to 100 spaces by specifying their space-ids.

**リクエストボディ**:

List of space-ids for the spaces to return.

**レスポンス**:

- `200`: 
- `400`: 

---

### /models/spaces/delete

#### POST

**概要**: Delete spaces



> **Required capabilities:** `DataModelsAcl:WRITE`

Delete one or more spaces. Currently limited to 100 spaces at a time.


If an existing data model references a space, you cannot delete that space. Nodes, edges and other data types that are part of a space will no longer be available. 

**リクエストボディ**:

List of space-ids for spaces to delete.

**レスポンス**:

- `200`: 
- `400`: 

---

### /models/statistics

#### GET

**概要**: Retrieve project-wide statistics and limits



> **Required capabilities:** `DataModelsAcl:READ`

Returns statistics and limits for data modeling resources across the project. Use this endpoint to monitor your overall data modeling usage and available capacity.

**レスポンス**:

- `200`: Statistics and limits for data modeling related resources in the project
- `400`: 

---

### /models/statistics/spaces

#### GET

**概要**: Retrieve statistics by space



> **Required capabilities:** `DataModelsAcl:READ`

Returns statistics for data modeling resources grouped by each space in the project.

**レスポンス**:

- `200`: List of spaces with associated data modeling statistics
- `400`: 

---

### /models/statistics/spaces/byids

#### POST

**概要**: Retrieve statistics by space for specific space-ids



> **Required capabilities:** `DataModelsAcl:READ`

Returns statistics for data modeling resources grouped by space for a specified list of spaces.

**リクエストボディ**:

List of space-ids to return statistics for.

**レスポンス**:

- `200`: List of spaces with associated data modeling statistics
- `400`: 

---

### /models/views

#### GET

**概要**: List views defined in the project



> **Required capabilities:** `DataModelsAcl:READ`

List of views defined in the current project. You can filter the list by specifying a space.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: 
- `400`: 

---

#### POST

**概要**: Create or update views



> **Required capabilities:** `DataModelsAcl:WRITE`

Add or update (upsert) views. For unchanged view specifications, the operation completes without making any changes.  We will not update the ```lastUpdatedTime``` value for views that remain unchanged.

**リクエストボディ**:

Views to add or update.

**レスポンス**:

- `200`: 
- `400`: 
- `409`: View conflict

---

### /models/views/byids

#### POST

**概要**: Retrieve views by their external ids



> **Required capabilities:** `DataModelsAcl:READ`

Retrieve up to 100 views by their external ids.

**パラメータ**:

- `` (unknown, 任意)

**リクエストボディ**:

List of external-ids of views to retrieve.

**レスポンス**:

- `200`: 
- `400`: 

---

### /models/views/delete

#### POST

**概要**: Delete views



> **Required capabilities:** `DataModelsAcl:WRITE`

Delete one or more views.  Currently limited to 100 views at a time.

**リクエストボディ**:

List of references to views you want to delete.

**レスポンス**:

- `200`: 
- `400`: 

---

### /postgresgateway

#### GET

**概要**: List users



> **Required capabilities:** `postgresGateway:READ`

List all users in a given project. If more than `limit` users exist, a cursor for pagination will be returned with the response.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: List of users paginated
- `400`: 
- `422`: 

---

#### POST

**概要**: Create users



> **Required capabilities:** `postgresGateway:WRITE`

Create postgres users.

**リクエストボディ**:

List of postgres users to create

**レスポンス**:

- `200`: List of created users
- `400`: Response of a failed request
- `422`: 

---

### /postgresgateway/byids

#### POST

**概要**: Retrieve users



> **Required capabilities:** `postgresGateway:READ`

Retreive a list of postgres users by their usernames, optionally ignoring unknown usernames

**リクエストボディ**:

**レスポンス**:

- `200`: List of users retrieved by their usernames
- `400`: Response of a failed request
- `422`: 

---

### /postgresgateway/delete

#### POST

**概要**: Delete users



> **Required capabilities:** `[object Object]`

Delete postgres users

**リクエストボディ**:

Postgres users to delete

**レスポンス**:

- `200`: 
- `400`: 
- `422`: 

---

### /postgresgateway/list

#### POST

**概要**: Filter users

List all postgres users for a given project. If more than `limit` users exist, a cursor for pagination will be returned with the response.

**リクエストボディ**:

**レスポンス**:

- `200`: List of users paginated
- `400`: 
- `422`: Validation Error

---

### /postgresgateway/tables/{username}

#### GET

**概要**: List tables



> **Required capabilities:** `postgresGateway:READ`

List all tables in a given project. If more than `limit` tables exist, a cursor for pagination will be returned with the response.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)
- `includeBuiltIns` (boolean, 任意)
  - Determines if API should return built-in tables or not

**レスポンス**:

- `200`: List of tables paginated
- `400`: 
- `422`: 

---

#### POST

**概要**: Create tables



> **Required capabilities:** `postgresGateway:WRITE`

Create up to 10 tables.

**パラメータ**:

- `` (unknown, 任意)

**リクエストボディ**:

List of tables to create

**レスポンス**:

- `200`: List of created tables
- `400`: Response of a failed request
- `422`: 

---

### /postgresgateway/tables/{username}/byids

#### POST

**概要**: Retrieve tables



> **Required capabilities:** `postgresGateway:READ`

Retreive a list of postgres tables for a user by their table names, optionally ignoring unknown table names

**パラメータ**:

- `` (unknown, 任意)

**リクエストボディ**:

**レスポンス**:

- `200`: List postgres tables by table names
- `400`: Response of a failed request
- `422`: 

---

### /postgresgateway/tables/{username}/delete

#### POST

**概要**: Delete tables



> **Required capabilities:** `[object Object]`

Delete up to 10 tables

**パラメータ**:

- `` (unknown, 任意)

**リクエストボディ**:

Tables to delete

**レスポンス**:

- `200`: Empty response
- `400`: 
- `422`: 

---

### /postgresgateway/update

#### POST

**概要**: Update users



> **Required capabilities:** `[object Object]`

Update postgres users

**リクエストボディ**:

Users to update

**レスポンス**:

- `200`: Returns users with updates
- `400`: 
- `422`: 

---

### /principals/me

#### GET

**概要**: Get the user profile of the principal issuing the request

Deprecated in favor of the new [endpoint](#tag/Principals/operation/getMe).

Retrieves the user profile of the principal issuing the request.

**レスポンス**:

- `200`: 

---

### /profiles

#### GET

**概要**: List all user profiles in the current project



> **Required capabilities:** `userProfilesAcl:READ`

Deprecated in favor of the [List principals](#tag/Principals/operation/listPrincipals) endpoint.

List all user profiles in the current project. This operation supports pagination by cursor.
The results are ordered alphabetically by name.

**パラメータ**:

- `identityType` (unknown, 任意)
- `limit` (integer, 任意)
  - Limits the number of results to be returned. The server returns no more than 1000 results even if the specified limit is larger. The default limit is 25.
- `` (unknown, 任意)

**レスポンス**:

- `200`: 

---

### /profiles/byids

#### POST

**概要**: Retrieve profiles by ID in the current project



> **Required capabilities:** `userProfilesAcl:READ`

Deprecated in favor of the [Retrieve principals](#tag/Principals/operation/getPrincipalsById) endpoint.

Retrieve one or more user profiles indexed by the user identifier in the same CDF project.

**リクエストボディ**:

Specify a maximum of 1000 unique IDs.

**レスポンス**:

- `200`: 
- `400`: The response for a failed request.

---

### /profiles/me

#### GET

**概要**: (deprecated) Get the user profile of the principal issuing the request



> **Required capabilities:** `userProfilesAcl:READ`

Deprecated in favor of the new [endpoint](#tag/Principals/operation/getMe).

Retrieves the user profile of the principal issuing the request.
If a principal doesn't have a user profile, you get a not found (404) response code.
Note: User profiles are created asyncronously, so the endpoint might return 404 for
a new principal for a while (usually seconds) until the profile has been created.

**レスポンス**:

- `200`: 
- `404`: The user profile for the requesting principal was not found.

---

### /profiles/search

#### POST

**概要**: Search user profiles in the current project



> **Required capabilities:** `userProfilesAcl:READ`

Deprecated in favor of the [List principals](#tag/Principals/operation/listPrincipals) endpoint.

Search user profiles in the current project. The result set ordering and match criteria
threshold may change over time. This operation does not support pagination.

**リクエストボディ**:

Query for user profile search.

**レスポンス**:

- `200`: List of matched user profiles. If no user profile matches, you'll receive an empty set.

---

### /projects

### /projects/{project}

### /raw/dbs

#### GET

**概要**: List databases



> **Required capabilities:** `rawAcl:LIST`


**パラメータ**:

- `limit` (integer, 任意)
  - Limit on the number of databases to be returned.
- `` (unknown, 任意)

**レスポンス**:

- `200`: A list of databases.

---

#### POST

**概要**: Create databases



> **Required capabilities:** `rawAcl:WRITE`

Create databases in a project. It is possible to post a maximum of 1000 databases per request.

**リクエストボディ**:

List of names of databases to be created.

**レスポンス**:

- `201`: The created databases.

---

### /raw/dbs/delete

#### POST

**概要**: Delete databases



> **Required capabilities:** `rawAcl:WRITE`

It deletes a database, but fails if the database is not empty and recursive is set to false (default).

**リクエストボディ**:

List of names of the databases to be deleted.

**レスポンス**:

- `200`: 

---

### /raw/dbs/{dbName}/tables

#### GET

**概要**: List tables in a database



> **Required capabilities:** `rawAcl:LIST`


**パラメータ**:

- `dbName` (string, 必須)
  - The name of a database to retrieve tables from.
- `limit` (integer, 任意)
  - Limit on the number of tables to be returned.
- `` (unknown, 任意)

**レスポンス**:

- `200`: A list of tables in the database

---

#### POST

**概要**: Create tables in a database



> **Required capabilities:** `rawAcl:WRITE`

Create tables in a database. It is possible to post a maximum of 1000 tables per request.

**パラメータ**:

- `dbName` (string, 必須)
  - Name of the database to create tables in.
- `ensureParent` (boolean, 任意)
  - Create database if it doesn't exist already

**リクエストボディ**:

List of tables to create.

**レスポンス**:

- `201`: The created tables.
- `400`: 

---

### /raw/dbs/{dbName}/tables/delete

#### POST

**概要**: Delete tables in a database



> **Required capabilities:** `rawAcl:WRITE`


**パラメータ**:

- `dbName` (string, 必須)
  - Name of the database to delete tables in.

**リクエストボディ**:

List of tables to delete.

**レスポンス**:

- `200`: 
- `400`: 

---

### /raw/dbs/{dbName}/tables/{tableName}/cursors

#### GET

**概要**: Retrieve cursors for parallel reads



> **Required capabilities:** `rawAcl:READ`

Retrieve cursors based on the last updated time range. Normally this endpoint is used for reading in parallel.

Each cursor should be supplied as the 'cursor' query parameter on GET requests to [Read Rows](#operation/getRows).
**Note** that the 'minLastUpdatedTime' and the 'maxLastUpdatedTime' query parameter on [Read Rows](#operation/getRows) are ignored when a cursor is specified.


**パラメータ**:

- `dbName` (string, 必須)
  - Name of the database.
- `tableName` (string, 必須)
  - Name of the table.
- `minLastUpdatedTime` (unknown, 任意)
  - An exclusive filter, specified as the number of milliseconds that have elapsed since 00:00:00 Thursday, 1 January 1970, Coordinated Universal Time (UTC), minus leap seconds.
- `maxLastUpdatedTime` (unknown, 任意)
  - An inclusive filter, specified as the number of milliseconds that have elapsed since 00:00:00 Thursday, 1 January 1970, Coordinated Universal Time (UTC), minus leap seconds.
- `numberOfCursors` (integer, 任意)
  - The number of cursors to return, by default it's 10.

**レスポンス**:

- `200`: Response with cursors

---

### /raw/dbs/{dbName}/tables/{tableName}/rows

#### GET

**概要**: Retrieve rows from a table



> **Required capabilities:** `rawAcl:READ`


**パラメータ**:

- `dbName` (string, 必須)
  - Name of the database.
- `tableName` (string, 必須)
  - Name of the table.
- `limit` (integer, 任意)
  - Limit the number of results. The API may return fewer than the specified limit.
- `columns` (string, 任意)
  - Ordered list of column keys, separated by commas. Leave empty for all, use single comma to retrieve only row keys.
- `` (unknown, 任意)
- `minLastUpdatedTime` (unknown, 任意)
  - An exclusive filter, specified as the number of milliseconds that have elapsed since 00:00:00 Thursday, 1 January 1970, Coordinated Universal Time (UTC), minus leap seconds.
- `maxLastUpdatedTime` (unknown, 任意)
  - An inclusive filter, specified as the number of milliseconds that have elapsed since 00:00:00 Thursday, 1 January 1970, Coordinated Universal Time (UTC), minus leap seconds.

**レスポンス**:

- `200`: Rows returned

---

#### POST

**概要**: Insert rows into a table



> **Required capabilities:** `rawAcl:WRITE`

Insert rows into a table. It is possible to post a maximum of 10000 rows per request.
It will replace the columns of an existing row if the rowKey already exists.

The rowKey is limited to 1024 characters which also includes Unicode characters.
The maximum size of columns are 5 MiB, however the maximum size of one column name and value is 2621440 characters each.
If you want to store huge amount of data per row or column we recommend using the Files API to upload blobs, then reference it from the Raw row.

The columns object is a key value object, where the key corresponds to the column name while the value is the column value.
It supports all the valid types of values in JSON, so number, string, array, and even nested JSON structure (see payload example to the right).

If an error occurs during the write, partial data may be written as there is no rollback. However, it's safe to retry the request, since this endpoint supports both update and insert (upsert).

A row's last updated timestamp will only be updated if the new column contents are considered different to the old one. An identical JSON string should always be counted as the same contents.


**パラメータ**:

- `dbName` (string, 必須)
  - Name of the database.
- `tableName` (string, 必須)
  - Name of the table.
- `ensureParent` (boolean, 任意)
  - Create database/table if it doesn't exist already

**リクエストボディ**:

List of rows to create.

**レスポンス**:

- `200`: 
- `400`: 

---

### /raw/dbs/{dbName}/tables/{tableName}/rows/delete

#### POST

**概要**: Delete rows in a table



> **Required capabilities:** `rawAcl:WRITE`


**パラメータ**:

- `dbName` (string, 必須)
  - Name of the database containing the rows.
- `tableName` (string, 必須)
  - Name of the table containing the rows.

**リクエストボディ**:

Keys to the rows to delete.

**レスポンス**:

- `200`: 

---

### /raw/dbs/{dbName}/tables/{tableName}/rows/{rowKey}

#### GET

**概要**: Retrieve row by key



> **Required capabilities:** `rawAcl:READ`


**パラメータ**:

- `dbName` (string, 必須)
  - Name of the database to retrieve the row from.
- `tableName` (string, 必須)
  - Name of the table to retrieve the row from.
- `rowKey` (string, 必須)
  - Row key of the row to retrieve.

**レスポンス**:

- `200`: Single row from the raw database table with the specified rowKey.

---

### /relationships

#### GET

**概要**: List relationships



> **Required capabilities:** `relationshipsAcl:READ`

Lists all relationships. The order of retrieved objects may change for two calls with the same parameters.
The endpoint supports pagination. The initial call to this endpoint should not contain a cursor, but the cursor parameter should be used to retrieve further pages of results.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: 
- `500`: 

---

#### POST

**概要**: Create relationships



> **Required capabilities:** `relationshipsAcl:WRITE`

List of the relationships to create. You can create a maximum of 1000 relationships per request. Relationships should be unique, but CDF does not prevent you from creating duplicates where only the externalId differs.

Relationships are uniquely identified by their externalId. Non-unique relationships will not be created.

The order of relationships in the response equals the order in the request.

**リクエストボディ**:

**レスポンス**:

- `201`: 
- `400`: 
- `409`: 
- `500`: 

---

### /relationships/byids

#### POST

**概要**: Retrieve relationships



> **Required capabilities:** `relationshipsAcl:READ`

Retrieve relationships by external IDs. You can retrieve a maximum of 1000 relationships per request.
The order of the relationships in the response equals the order in the request.

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 
- `409`: 

---

### /relationships/delete

#### POST

**概要**: Delete relationships



> **Required capabilities:** `relationshipsAcl:WRITE`

Delete the relationships between resources identified by the external IDs in the request. You can delete a maximum of 1000 relationships per request.

**リクエストボディ**:

**レスポンス**:

- `202`: 
- `409`: 
- `500`: 

---

### /relationships/list

#### POST

**概要**: Filter relationships



> **Required capabilities:** `relationshipsAcl:READ`

Lists relationships matching the query filter in the request. You can retrieve a maximum of 1000 relationships per request.

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 
- `409`: 

---

### /relationships/update

#### POST

**概要**: Update relationships



> **Required capabilities:** `relationshipsAcl:WRITE` `relationshipsAcl:READ`

Update relationships between resources according to the partial definitions of the relationships given in the payload of the request. This means that fields not mentioned in the payload will remain unchanged. Up to 1000 relationships can be updated in one operation.
To delete a value from an optional value the `setNull` field should be set to `true`.
The order of the updated relationships in the response equals the order in the request.

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `400`: 
- `409`: 

---

### /reportdeletion

### /reportdeletion/pending

### /reportdeletion/reset

### /securitycategories

#### GET

**概要**: List security categories



> **Required capabilities:** `securityCategoriesAcl:LIST`

Retrieves a list of all security categories for a project.

**パラメータ**:

- `sort` (string, 任意)
  - Sort descending or ascending.
- `cursor` (string, 任意)
  - Cursor to use for paging through results.
- `limit` (integer, 任意)
  - Return up to this many results. Maximum is 1000. Default is 25.

**レスポンス**:

- `200`: A list of security categories.

---

#### POST

**概要**: Create security categories



> **Required capabilities:** `securityCategoriesAcl:CREATE`

Creates security categories with the given names. Duplicate names in the request are ignored.
If a security category with one of the provided names exists already, then the request will fail and no security categories are created.


**リクエストボディ**:

List of categories to create

**レスポンス**:

- `201`: A list of security categories.

---

### /securitycategories/delete

#### POST

**概要**: Delete security categories



> **Required capabilities:** `securityCategoriesAcl:DELETE`

Deletes the security categories that match the provided IDs.
If any of the provided IDs does not belong to an existing security category, then the request will fail and no security categories are deleted.


**リクエストボディ**:

List of security category IDs to delete.

**レスポンス**:

- `200`: 
- `400`: 

---

### /seismic/batchdownload

#### POST

**概要**: Download multiple seismic objects as a ZIP archive



> **Required capabilities:** `seismicAcl:READ` `experimentAcl:USE`

Download multiple seismic objects specified by the filter, as a streamed ZIP archive file.

**リクエストボディ**:

The filter that determines the seismic objects to return.

**レスポンス**:

- `200`: The generated ZIP archive file
- `400`: 

---

### /seismic/seismics/segy/{seismicId}

#### GET

**概要**: Download a seismic object as a SEG-Y file



> **Required capabilities:** `seismicAcl:READ`

Retrieves a SEG-Y file with all traces contained within the given seismic object.

**パラメータ**:

- `` (unknown, 任意)

**レスポンス**:

- `200`: 
- `400`: 

---

### /sequences

#### GET

**概要**: List sequences



> **Required capabilities:** `sequencesAcl:READ`

List sequences. Use `nextCursor` to paginate through the results.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)
- `limit` (integer, 任意)
  - Limits the number of results to be returned. The server returns a maximum of 1000 results even if you specify a higher limit.

**レスポンス**:

- `200`: Paged response with a list of sequences.

---

#### POST

**概要**: Create sequences



> **Required capabilities:** `sequencesAcl:WRITE`

Create one or more sequences.

**リクエストボディ**:

Sequence to be stored.

**レスポンス**:

- `201`: Response with the created sequence.
- `400`: 

---

### /sequences/aggregate

#### POST

**概要**: Aggregate sequences



> **Required capabilities:** `sequencesAcl:READ`

The aggregation API allows you to compute aggregated results from a set of sequences, such as
getting the number of sequences in a project or checking what assets the different sequences
in your project are associated with (along with the number of sequences for each asset).
By specifying `filter` and/or `advancedFilter`, the aggregation will take place only over those
sequences that match the filters. `filter` and `advancedFilter` behave the same way as in the
`list` endpoint.

<details>
<summary>
The default behavior, when the <code>aggregate</code> field is not specified the request body, is to return the
number of sequences that match the filters (if any), which is the same behavior as when the
<code>aggregate</code> field is set to <code>count</code>.
</summary>

The following requests will both return the total number of
sequences whose `name` begins with `pump`:

```
{
  "advancedFilter": {"prefix": {"property": ["name"], "value": "pump"}}
}
```

and

```
{
  "aggregate": "count",
  "advancedFilter": {"prefix": {"property": ["name"], "value": "pump"}}
}
```

The response might be:

```
{"items": [{"count": 42}]}
```
</details>

<details>
<summary>
Setting <code>aggregate</code> to <code>uniqueValues</code> and specifying a property in <code>properties</code> (this field is an array, but currently only supports one property) will
return all unique values (up to a maximum of 1000) that are taken on by that property
across all the sequences that match the filters, as well as the number of sequences that
have each of those property values.
</summary>

This example request finds all the unique asset ids that are
referenced by the sequences in your project whose `name` begins with `pump`:

```
{
  "aggregate": "uniqueValues",
  "properties": [{"property": ["assetId"]}],
  "advancedFilter": {"prefix": {"property": ["name"], "value": "pump"}}
}
```

The response might be the following, saying that 23 sequences are associated with asset 18
and 107 sequences are associated with asset 76:

```
{
  "items": [
    {"values": ["18"], "count": 23},
    {"values": ["76"], "count": 107}
  ]
}
```
</details>

<details>
<summary>
Setting <code>aggregate</code> to <code>cardinalityValues</code> will instead return the approximate number of
distinct values that are taken on by the given property among the matching sequences.
</summary>

Example request:

```
{
  "aggregate": "cardinalityValues",
  "properties": [{"property": ["assetId"]}],
  "advancedFilter": {"prefix": {"property": ["name"], "value": "pump"}}
}
```

The result is likely exact when the set of unique values is small. In this example, there are likely two distinct asset ids among the matching sequences:

```
{"items": [{"count": 2}]}
```
</details>

<details>
<summary>
Setting <code>aggregate</code> to <code>uniqueProperties</code> will return the set of unique properties whose property
path begins with <code>path</code> (which can currently only be <code>["metadata"]</code>) that are contained in the sequences that match the filters.
</summary>

Example request:

```
{
  "aggregate": "uniqueProperties",
  "path": ["metadata"],
  "advancedFilter": {"prefix": {"property": ["name"], "value": "pump"}}
}
```

The result contains all the unique metadata keys in the sequences whose `name` begins with
`pump`, and the number of sequences that contains each metadata key:

```
{
  "items": [
    {"values": [{"property": ["metadata", "tag"]}], "count": 43},
    {"values": [{"property": ["metadata", "installationDate"]}], "count": 97}
  ]
}
```
</details>

<details>
<summary>
Setting <code>aggregate</code> to <code>cardinalityProperties</code> will instead return the approximate number of
different property keys whose path begins with <code>path</code> (which can currently only be <code>["metadata"]</code>, meaning that this can only be used to count the approximate number of distinct metadata keys among the matching sequences).
</summary>

Example request:

```
{
  "aggregate": "cardinalityProperties",
  "path": ["metadata"],
  "advancedFilter": {"prefix": {"property": ["name"], "value": "pump"}}
}
```

The result is likely exact when the set of unique values is small. In this example, there are likely two distinct metadata keys among the matching sequences:

```
{"items": [{"count": 2}]}
```
</details>

The `aggregateFilter` field may be specified if `aggregate` is set to `cardinalityProperties` or `uniqueProperties`. The structure of this field is similar to that of `advancedFilter`, except that the set of leaf filters is smaller (`in`, `prefix`, and `range`), and that none of the leaf filters specify a property. Unlike `advancedFilter`, which is applied _before_ the aggregation (in order to restrict the set of sequences that the aggregation operation should be applied to), `aggregateFilter` is applied _after_ the initial aggregation has been performed, in order to restrict the set of results.

<details>
<summary>
Click here for more details about <code>aggregateFilter</code>. 
</summary>

When `aggregate` is set to `uniqueProperties`, the result set contains a number of _property paths_, each with an associated count that shows how many sequences contained that property (among those sequences that matched the `filter` and `advancedFilter`, if they were specified) . If `aggregateFilter` is specified, it will restrict the property paths included in the output. Let us add an `aggregateFilter` to the `uniqueProperties` example from above:

```
{
  "aggregate": "uniqueProperties",
  "path": ["metadata"],
  "advancedFilter": {"prefix": {"property": ["name"], "value": "pump"}},
  "aggregateFilter": {"prefix": {"value": "t"}}
}
```

Now, the result only contains those metadata properties whose key begins with `t` (but it will be the same set of metadata properties that begin with `t` as in the original query without `aggregateFilter`, and the counts will be the same):

```
{
  "items": [
    {"values": [{"property": ["metadata", "tag"]}], "count": 43}
  ]
}
```

Similarly, adding `aggregateFilter` to `cardinalityProperties` will return the approximate number of properties whose property key matches `aggregateFilter` from those sequences matching the `filter` and `advancedFilter` (or from all sequences if neither `filter` nor `aggregateFilter` are specified):

```
{
  "aggregate": "cardinalityProperties",
  "path": ["metadata"],
  "advancedFilter": {"prefix": {"property": ["name"], "value": "pump"}},
  "aggregateFilter": {"prefix": {"value": "t"}}
}
```

As we saw above, only one property matches:

```
{"items": [{"count": 1}]}
```

Note that `aggregateFilter` is also accepted when `aggregate` is set to `cardinalityValues` or `cardinalityProperties`. For those aggregations, the effect of any `aggregateFilter` could also be achieved via a similar `advancedFilter`. However, `aggregateFilter` is not accepted when `aggregate` is omitted or set to `count`.
</details>

### Rate and concurrency limits

Rate and concurrency limits apply this endpoint. If a request exceeds one of the limits,
it will be throttled with a `429: Too Many Requests` response. More on limit types
and how to avoid being throttled is described
[here](https://developer.cognite.com/dev/concepts/request_throttling/).

| Limit          | Per project           | Per user (identity)   |
|----------------|-----------------------|-----------------------|
| Rate           | 15 requests per second| 10 requests per second|
| Concurrency    | 6 concurrent requests | 4 concurrent requests |


**リクエストボディ**:

Aggregates the sequences that match the given criteria.

**レスポンス**:

- `200`: Response with the aggregated sequences. The type of the response depends on the value of the `aggregate` parameter in the request.
- `400`: 

---

### /sequences/byids

#### POST

**概要**: Retrieve sequences



> **Required capabilities:** `sequencesAcl:READ`

Retrieves one or more sequences by ID or external ID. The response returns the sequences in the same order as in the request.

**リクエストボディ**:

Ids of the sequences

**レスポンス**:

- `200`: Response with the requested sequences.

---

### /sequences/data

#### POST

**概要**: Insert rows



> **Required capabilities:** `sequencesAcl:WRITE`

Inserts rows into a sequence. This overwrites data in rows and columns that exist.

**リクエストボディ**:

Data posted.

**レスポンス**:

- `200`: 

---

### /sequences/data/delete

#### POST

**概要**: Delete rows



> **Required capabilities:** `sequencesAcl:WRITE`

Deletes the given rows of the sequence. All columns are affected.

**リクエストボディ**:

Indicate the sequences and the rows where data should be deleted.

**レスポンス**:

- `200`: 

---

### /sequences/data/latest

#### POST

**概要**: Retrieve last row



> **Required capabilities:** `sequencesAcl:READ`

Retrieves the last row in one or more sequences. Note that the last row in a sequence is the
one with the highest row number, which is not necessarily the one that was ingested most
recently.


**リクエストボディ**:

Description of data requested.

**レスポンス**:

- `200`: Response with the sequence data found.

---

### /sequences/data/list

#### POST

**概要**: Retrieve rows



> **Required capabilities:** `sequencesAcl:READ`

Processes data requests and returns the result. Note that this operation uses a dynamic limit on the number of rows returned based on the number and type of columns; use the provided cursor to paginate and retrieve all data.

**リクエストボディ**:

Description of data requested.

**レスポンス**:

- `200`: Response with the sequence data found.

---

### /sequences/delete

#### POST

**概要**: Delete sequences



> **Required capabilities:** `sequencesAcl:WRITE`

Deletes the sequences with the specified IDs. If one or more of the sequences do not exist, the `ignoreUnknownIds` parameter controls what will happen: if it is `true`, the sequences that do exist will be deleted, and the request succeeds; if it is `false` or absent, nothing will be deleted, and the request fails.

**リクエストボディ**:

Ids of the sequences to delete.

**レスポンス**:

- `200`: 

---

### /sequences/list

#### POST

**概要**: Filter sequences



> **Required capabilities:** `sequencesAcl:READ`

<details>
<summary>
Retrieves a list of sequences that match the given criteria.
</summary>

### Advanced filtering

The `advancedFilter`
field lets you create complex filtering expressions that combine simple operations,
such as `equals`, `prefix`, and `exists`, by using the Boolean operators `and`, `or`, and `not`.
Filtering applies to basic fields as well as metadata. See the `advancedFilter` syntax in the request example.



#### Supported leaf filters

| Leaf filter    | Supported fields       | Description and example                                                                                                                                                            |
|----------------|------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `containsAll`  | Array type fields      | Only includes results which contain all of the specified values. <br /> `{"containsAll": {"property": ["property"], "values": [1, 2, 3]}}`                                         |
| `containsAny`  | Array type fields      | Only includes results which contain at least one of the specified values. <br /> `{"containsAny": {"property": ["property"], "values": [1, 2, 3]}}`                                |
| `equals`       | Non-array type fields  | Only includes results that are equal to the specified value. <br /> `{"equals": {"property": ["property"], "value": "example"}}`                                                   |
| `exists`       | All fields             | Only includes results where the specified property exists (has a value). <br /> `{"exists": {"property": ["property"]}}`                                                           |
| `in`           | Non-array type fields  | Only includes results that are equal to one of the specified values. <br /> `{"in": {"property": ["property"], "values": [1, 2, 3]}}`                                              |
| `prefix`       | String type fields     | Only includes results which start with the specified text. <br /> `{"prefix": {"property": ["property"], "value": "example"}}`                                                     |
| `range`        | Non-array type fields  | Only includes results that fall within the specified range. <br /> `{"range": {"property": ["property"], "gt": 1, "lte": 5}}` <br /> Supported operators: `gt`, `lt`, `gte`, `lte` |
 | `search`       | `["name"]` and `["description"]` | Introduced to provide functional parity with the /sequences/search endpoint. <br /> `{"search": {"property": ["property"], "value": "example"}}`                        |

#### Supported properties

| Property                          | Type               |
|-----------------------------------|--------------------|
| `["description"]`                 | string             |
| `["externalId"]`                  | string             |
| `["metadata", "<someCustomKey>"]` | string             |
| `["name"]`                        | string             |
| `["assetId"]`                      | number              |
| `["assetRootId"]`                  | number              |
| `["createdTime"]`                  | number              |
| `["dataSetId"]`                    | number              |
| `["id"]`                           | number              |
| `["lastUpdatedTime"]`              | number              |

#### Limits

- Filter query max depth: 10.
- Filter query max number of clauses: 100.
- `and` and `or` clauses must have at least one element (and at most 99, since each element counts
  towards the total clause limit, and so does the `and`/`or` clause itself).
- The `property` array of each leaf filter has the following limitations:
  - Number of elements in the array is 1 or 2.
  - Elements must not be null or blank.
  - Each element max length is 256 characters.
  - The `property` array must match one of the existing properties (static top-level property or dynamic metadata property).
- `containsAll`, `containsAny`, and `in` filter `values` array size must be in the range [1, 100].
- `containsAll`, `containsAny`, and `in` filter `values` array must contain elements of number or string type (matching the type of the given property).
- `range` filter must have at lest one of `gt`, `gte`, `lt`, `lte` attributes.
  But `gt` is mutually exclusive to `gte`, while `lt` is mutually exclusive to `lte`.
- `gt`, `gte`, `lt`, `lte` in the `range` filter must be of number or string type (matching the type of the given property).
- `search` filter `value` must not be blank, and the length must be in the range [1, 128], and there
  may be at most two `search` filters in the entire filter query.
- The maximum length of the `value` of a leaf filter that is applied to a string property is 256.

### Sorting

By default, sequences are sorted by their creation time in ascending order.
Sorting by another property or by several other properties can be explicitly requested via the
`sort` field, which must contain a list
of one or more sort specifications. Each sort specification indicates the `property` to sort on
and, optionally, the `order` in which to sort (defaults to `asc`). If multiple sort specifications are
supplied, the results are sorted on the first property, and those with the same value for the first
property are sorted on the second property, and so on.  
Partitioning is done independently of sorting; there is no guarantee of sort order between elements from different partitions.

#### Null values

In case the `nulls` field has the `auto` value, or the field isn't specified, null (missing) values
are considered bigger than any other values. They are placed last when sorting in the `asc`
order and first in the `desc` order. Otherwise, missing values are placed according to
the `nulls` field (`last` or `first`), and their placement won't depend on the `order`
field. Note that the number zero, empty strings, and empty lists are all considered
_not_ null.

#### Example

```json
{
  "sort": [
    {
      "property" : ["createdTime"],
      "order": "desc",
      "nulls": "last"
    },
    {
      "property" : ["metadata", "<someCustomKey>"]
    }
  ]
}
```


#### Properties

You can sort on the following properties:

| Property                          |
|-----------------------------------|
| `["assetId"]`                     |
| `["createdTime"]`                 |
| `["dataSetId"]`                   |
| `["description"]`                 |
| `["externalId"]`                  |
| `["lastUpdatedTime"]`             |
| `["metadata", "<someCustomKey>"]` |
| `["name"]`                        |

#### Limits

The `sort` array must contain 1 to 2 elements.
</details>


**リクエストボディ**:

Retrieves a list of sequences matching the given criteria.

**レスポンス**:

- `200`: Response with a list of sequences matching the given criteria.

---

### /sequences/search

#### POST

**概要**: Search sequences



> **Required capabilities:** `sequencesAcl:READ`

Retrieves a list of sequences matching the given criteria. This operation doesn't support pagination.

**リクエストボディ**:

Retrieves a list of sequences matching the given criteria. This operation doesn't support pagination.

**レスポンス**:

- `200`: Response with a list of sequences matching the given criteria.

---

### /sequences/update

#### POST

**概要**: Update sequences



> **Required capabilities:** `sequencesAcl:WRITE`

Updates one or more sequences. Fields outside of the request remain unchanged.

**リクエストボディ**:

Patch definition

**レスポンス**:

- `200`: Response with the updated sequences.

---

### /sessions

#### GET

**概要**: List sessions



> **Required capabilities:** `sessionsAcl:LIST`

List all sessions in the current project.

**パラメータ**:

- `status` (string, 任意)
  - If given, only sessions with the given status are returned.

- `cursor` (string, 任意)
  - Cursor to use for paging through results.
- `limit` (integer, 任意)
  - Return up to this many results. Maximum is 1000. Default is 25.

**レスポンス**:

- `200`: A list of sessions in the current project.
- `400`: 

---

#### POST

**概要**: Create sessions



> **Required capabilities:** `sessionsAcl:CREATE`

Create sessions

**リクエストボディ**:

A request containing the information needed to create a session.

**レスポンス**:

- `200`: List of session creation related information
- `400`: 

---

### /sessions/batchrefresh

### /sessions/byids

#### POST

**概要**: Retrieve sessions with given IDs



> **Required capabilities:** `sessionsAcl:LIST`

Retrieves sessions with given IDs. The request will fail if any of the IDs does not belong to an existing session.

**リクエストボディ**:

List of session IDs to retrieve

**レスポンス**:

- `200`: A list of sessions with the given ids
- `400`: 

---

### /sessions/detach

### /sessions/revoke

#### POST

**概要**: Revoke sessions



> **Required capabilities:** `sessionsAcl:DELETE`

Revoke access to a session. Revocation is idempotent.
Revocation of a session may in some cases take up to 1 hour to take effect.


**リクエストボディ**:

A request containing the information needed to revoke sessions.

**レスポンス**:

- `200`: List of revoked sessions.
If the user does not have the `sessionsAcl:LIST` capability, then only the session IDs will be present in the response.

- `400`: 

---

### /sessions/token

### /simulators

### /simulators/delete

### /simulators/integrations

### /simulators/integrations/delete

#### POST

**概要**: Delete Simulator Integrations

Delete a simulator integration. This will also delete all associated simulator resources (e.g. routines, runs).

**リクエストボディ**:

**レスポンス**:

- `200`: Successful Response
- `403`: Forbidden
- `500`: Internal Server Error

---

### /simulators/integrations/list

#### POST

**概要**: Filter Simulator Integrations

Retrieves a list of simulator integrations that match the given criteria.

**リクエストボディ**:

**レスポンス**:

- `200`: Successful Response
- `403`: Forbidden
- `500`: Internal Server Error

---

### /simulators/integrations/update

### /simulators/list

#### POST

**概要**: Filter Simulators

List simulators.

**リクエストボディ**:

**レスポンス**:

- `200`: Successful Response
- `403`: Forbidden
- `500`: Internal Server Error

---

### /simulators/logs/byids

#### POST

**概要**: Retrieve Simulator Logs

Retrieve one or more simulator logs by IDs.

**リクエストボディ**:

**レスポンス**:

- `200`: Successful Response
- `400`: Bad Request
- `403`: Forbidden
- `500`: Internal Server Error

---

### /simulators/logs/update

### /simulators/models

#### POST

**概要**: Create Simulator Model

Create a simulator model.

**リクエストボディ**:

**レスポンス**:

- `201`: Successful Response
- `400`: Bad Request
- `403`: Forbidden
- `500`: Internal Server Error

---

### /simulators/models/aggregate

#### POST

**概要**: Aggregate Simulator Models

Calculate aggregates for simulator models, considering the optional filter specification.

**リクエストボディ**:

**レスポンス**:

- `200`: Successful Response
- `400`: Bad Request
- `403`: Forbidden
- `500`: Internal Server Error

---

### /simulators/models/byids

#### POST

**概要**: Retrieve Simulator Model

Retrieve a simulator model by ID or external ID.

**リクエストボディ**:

**レスポンス**:

- `200`: Successful Response
- `400`: Bad Request
- `403`: Forbidden
- `500`: Internal Server Error

---

### /simulators/models/delete

#### POST

**概要**: Delete Simulator Model

Delete a simulator model. This will also delete all associated simulator resources (e.g. revisions, routines, runs).

**リクエストボディ**:

**レスポンス**:

- `200`: Successful Response
- `403`: Forbidden
- `500`: Internal Server Error

---

### /simulators/models/list

#### POST

**概要**: Filter Simulator Models

Retrieves a list of simulator models that match the given criteria.

**リクエストボディ**:

**レスポンス**:

- `200`: Successful Response
- `400`: Bad Request
- `403`: Forbidden
- `500`: Internal Server Error

---

### /simulators/models/revisions

#### POST

**概要**: Create Simulator Model Revision

Create a simulator model revision.

**リクエストボディ**:

**レスポンス**:

- `201`: Successful Response
- `400`: Bad Request
- `403`: Forbidden
- `500`: Internal Server Error

---

### /simulators/models/revisions/byids

#### POST

**概要**: Retrieve Simulator Model Revisions

Retrieve one or more simulator model revisions by IDs or external IDs.

**リクエストボディ**:

**レスポンス**:

- `200`: Successful Response
- `400`: Bad Request
- `403`: Forbidden
- `500`: Internal Server Error

---

### /simulators/models/revisions/data/list

### /simulators/models/revisions/data/update

### /simulators/models/revisions/list

#### POST

**概要**: Filter Simulator Model Revisions

Retrieves a list of simulator model revisions that match the given criteria.

**リクエストボディ**:

**レスポンス**:

- `200`: Successful Response
- `400`: Bad Request
- `403`: Forbidden
- `500`: Internal Server Error

---

### /simulators/models/revisions/update

### /simulators/models/update

#### POST

**概要**: Update Simulator Model

Update a simulator model.

**リクエストボディ**:

**レスポンス**:

- `200`: Successful Response
- `400`: Bad Request
- `403`: Forbidden
- `500`: Internal Server Error

---

### /simulators/routines

#### POST

**概要**: Create Simulator Routine

Create a simulator routine.

**リクエストボディ**:

**レスポンス**:

- `201`: Successful Response
- `400`: Bad Request
- `403`: Forbidden
- `500`: Internal Server Error

---

### /simulators/routines/aggregate

#### POST

**概要**: Aggregate Simulator Routines

Calculate aggregates for simulator routines, considering the optional filter specification.

**リクエストボディ**:

**レスポンス**:

- `200`: Successful Response
- `400`: Bad Request
- `403`: Forbidden
- `500`: Internal Server Error

---

### /simulators/routines/delete

#### POST

**概要**: Delete Simulator Routine

Delete a simulator routine. This will also delete all associated simulator resources (e.g. revisions, runs, data).

**リクエストボディ**:

**レスポンス**:

- `200`: Successful Response
- `403`: Forbidden
- `500`: Internal Server Error

---

### /simulators/routines/list

#### POST

**概要**: Filter Simulator Routines

Retrieves a list of simulator routines that match the given criteria.

**リクエストボディ**:

**レスポンス**:

- `200`: Successful Response
- `400`: Bad Request
- `403`: Forbidden
- `500`: Internal Server Error

---

### /simulators/routines/revisions

#### POST

**概要**: Create Simulator Routine Revision

The total size of the request body can be a maximum of 50.00 KB.

When creating a simulator routine revision, be mindful of the following:

- The amount and size of the inputs you provide to the simulator routine.
- The size of the outputs you expect from the simulator routine.

The total size of the data sent by a simulator connector cannot exceed 100.00 KB per simulation run.

**リクエストボディ**:

**レスポンス**:

- `201`: Successful Response
- `400`: Bad Request
- `403`: Forbidden
- `413`: Request Entity Too Large
- `500`: Internal Server Error

---

### /simulators/routines/revisions/byids

#### POST

**概要**: Retrieve Simulator Routine Revisions

Retrieve one or more simulator routine revisions by IDs or external IDs.

**リクエストボディ**:

**レスポンス**:

- `200`: Successful Response
- `400`: Bad Request
- `403`: Forbidden
- `500`: Internal Server Error

---

### /simulators/routines/revisions/list

#### POST

**概要**: Filter Simulator Routine Revisions

Retrieves a list of simulator routine revisions that match the given criteria.

**リクエストボディ**:

**レスポンス**:

- `200`: Successful Response
- `400`: Bad Request
- `403`: Forbidden
- `500`: Internal Server Error

---

### /simulators/run

#### POST

**概要**: Run Simulation

Request to run a simulator routine asynchronously.

The total size of the request body can be a maximum of 50.00 KB.

**リクエストボディ**:

**レスポンス**:

- `201`: Successful Response
- `400`: Bad Request
- `403`: Forbidden
- `413`: Request Entity Too Large
- `500`: Internal Server Error

---

### /simulators/run/callback

### /simulators/runs/byids

#### POST

**概要**: Retrieve Simulation

Retrieve a simulation run by ID.

**リクエストボディ**:

**レスポンス**:

- `200`: Successful Response
- `400`: Bad Request
- `403`: Forbidden
- `500`: Internal Server Error

---

### /simulators/runs/data/list

#### POST

**概要**: Retrieve Simulation Data

Retrieve data associated with a simulation run by ID.

**リクエストボディ**:

**レスポンス**:

- `200`: Successful Response
- `400`: Bad Request
- `403`: Forbidden
- `500`: Internal Server Error

---

### /simulators/runs/list

#### POST

**概要**: Filter Simulation Runs

Retrieves a list of simulation runs that match the given criteria.

**リクエストボディ**:

**レスポンス**:

- `200`: Successful Response
- `400`: Bad Request
- `403`: Forbidden
- `500`: Internal Server Error

---

### /simulators/update

### /streams

#### GET

**概要**: List streams



> **Required capabilities:** `StreamsAcl:READ`

List all streams available in the project.


**レスポンス**:

- `200`: List of streams.

- `400`: 

---

#### POST

**概要**: Create stream



> **Required capabilities:** `StreamsAcl:CREATE`

Create a new stream. A stream is a target for high volume data ingestion,
with data shaped by the Data Modeling concepts.


**リクエストボディ**:

Stream to create.


**レスポンス**:

- `201`: Stream created.

- `400`: 
- `409`: Stream with the requested ID is already exist.


---

### /streams/delete

#### POST

**概要**: Delete stream



> **Required capabilities:** `StreamsAcl:DELETE`

Delete a stream by its identifier, along with all records stored in the stream.

If the stream does not exist, the operation succeeds without error (idempotent delete).


**リクエストボディ**:

Stream to delete.


**レスポンス**:

- `200`: 
- `400`: 

---

### /streams/{streamId}

#### GET

**概要**: Retrieve stream



> **Required capabilities:** `StreamsAcl:READ`

Retrieve a stream by its identifier.


**パラメータ**:

- `` (unknown, 任意)
- `includeStatistics` (boolean, 任意)
  - If set to ```true```, usage statistics will be  returned together with stream settings.


**レスポンス**:

- `200`: Stream retrieved.

- `400`: 

---

#### DELETE

**概要**: Delete stream (DEPRECATED)



> **Required capabilities:** `StreamsAcl:DELETE`

**DEPRECATED**: This endpoint is no longer supported and returns HTTP 410 Gone.
Please use `POST /streams/delete` instead.


**パラメータ**:

- `` (unknown, 任意)

**レスポンス**:

- `410`: This endpoint is deprecated and no longer supported.
Please use POST /streams/delete instead.


---

### /streams/{streamId}/records

#### POST

**概要**: Ingest records into a stream



> **Required capabilities:** `StreamRecordsAcl:WRITE` `DataModelsAcl:READ`

Before a user can add records, all referenced schema elements (Spaces, Containers, Properties) must be defined/created in [Data Modeling](https://docs.cognite.com/cdf/dm)

To add records, the user must have capabilities to access (read to) the referenced containers as well as capabilities to access (write to) the referenced record ```space```.

Batches of records are ingested into a ```stream```.

Under normal ingestion operation, and where the data ```stream``` does not contain any duplicated records,
the record will be created, and the properties defined in each of the containers in the ```sources``` array will be populated.

For immutable streams a duplicate record is defined as one which is being written to the same ```space```,
which uses the same container definitions in the ```sources``` array,
and all the properties (names and values) are the same.

For immutable streams under most circumstances, when duplicate records are encountered during ingestion, the duplicate is discarded.
However there are rare occasions when certain internal database operations occur, a duplicate may be stored.

For mutable streams duplicated record identifiers (```space``` + ```externalId```) are not allowed
and a request which tries to create a record with existing identifier will be rejected.

**Note:** The maximum total request size is 10MB.


**パラメータ**:

- `` (unknown, 任意)

**リクエストボディ**:

Records to ingest into a stream.


**レスポンス**:

- `202`: 
- `400`: 
- `409`: Some mutable records with the specified `space` and `externalId` already exist in the stream.
But some records might have been created successfully.
Which records were successfully created and which were rejected with a conflict can be understood from the response content.

**Note:** the error may appear due to some HTTP request retries (in intermediate components like SDK, Load balancers, Application Gateway, etc),
so better to check which records are in the stream when you receive this error.
`.../records/filter` or `.../records/sync` endpoints with a filter by `space` and `externalId` can be used to check the stored state of the conflicting records.

- `422`: Some mutable record identifiers (`space` and `externalId`) are duplicated in the request.

- `500`: If at least one record was not ingested due to an internal error, and all other errors in the `failed` list were non-retryable,
this error code will be returned.

**Note:** Record operations are not transactional. This means that during batch ingestion,
some records may be created successfully even if the creation of others fails.
The response body indicates which records were successfully created and which were not.

An internal server error usually indicates an issue that requires investigation. A simple retry might not help,
but a few retries could be a reasonable approach.

**Note:** If you choose to retry on a 500 error, it is recommended to retry only the failed records rather than the entire request.
For mutable streams this helps avoid receiving 409 status codes records that were already successfully created in a previous attempt.
For immutable streams, this helps prevent accidental duplicate records from appearing in the stream (in rare cases when the deduplication mechanism fails).

- `503`: If at least one record was not ingested due to the service being temporarily unavailable,
this error code will be returned.

**Note:** Record operations are not transactional. This means that during batch ingestion, some records may be created successfully
even if the creation of others fails. The response body indicates which records were successfully created and which were not.

A "service temporarily unavailable" error usually indicates a temporary issue, and several retries may help complete the record creation.

**Note:** It is recommended to retry ingestion of only the failed records rather than the entire request.
For mutable streams, this helps avoid receiving 409 status codes for records that were already successfully created in a previous attempt.
For immutable streams, this helps prevent accidental duplicate records from appearing in the stream (in rare cases when the deduplication mechanism fails).


---

### /streams/{streamId}/records/aggregate

#### POST

**概要**: Aggregate records data from a stream



> **Required capabilities:** `StreamRecordsAcl:READ` `DataModelsAcl:READ`

Aggregate data for records from the ```stream```.

You can use an optional filter specification to limit the results.

The Records API supports three main groups of aggregates:

*Metric aggregates*: ```sum```, ```min```, ```max```, ```count```, etc.

*Bucket aggregates* (result records are aggregated into groups (buckets) — similar to the Data Model Instances ```groupBy``` concept):
  ```uniqueValues``` (group by property values), ```timeHistogram``` (group by date/time ranges), ```filters``` (group by filter criteria), etc.

*Pipeline aggregates*: ```movingFunction```.
**Note:** The pipeline aggregate applies to the results of another histogram aggregate.
  It applies to metrics (such as count, min, max, sum, etc.), not to the records in the parent aggregate buckets.
  This differs from sub-aggregates, which apply directly to records within each bucket.

Every aggregate request can contain multiple aggregate entries.
Every bucket aggregate can contain multiple sub-aggregates (of any type), which are applied to records in each bucket of the parent aggregate.

<u>Example:</u>

Imagine there is paddle-tennis players `game_statistics` container with model:
```
{
  game_id: Long,
  game_time: Instant,
  player_name: String,
  points_scored: Int,
  ...
}
```

To get the following data:
```
for each day
    calculate average number of points scored by players in all games on that day
    and for each player
        calculate the total number of points scored by the player in all games they participated in on that day
        and find the max number of points scored by the player in all games they participated in on that day
and find the max number of points scored by players in all games they participated in during the entire search period.
```

Use aggregates like these (meta language):
```
timeHistogram(daily, game_time) // group by (form buckets) 1-day range based on `game_time` property values
    avg(points_scored)          // inside every day group calculate average value in `points_scored` property
    uniqueValues(player_name)   // inside every day group group by (form buckets) player names
        sum(points_scored)      // inside every player group which is inside every day group calculate the sum of points scored
        max(points_scored)      // inside every player group which is inside every day group find the maximum of points scored
max(points_scored)              // find the maximum number of points scored across all statistic records (without any grouping)
```

Or the same in Cognite Records Aggregates DSL (Json):
```
{
  "my_groups_by_1d_range": {
    "timeHistogram": {
      "calendarInterval": "1d",
      "property": [ "paddle", "game_statistics", "game_time" ],
      "aggregates": {
        "my_groups_by_player_name": {
          "uniqueValues": {
            "property": [ "paddle", "game_statistics", "player_name" ],
            "aggregates": {
              "my_player_daily_scores_sum": {
                "sum": { "property": [ "paddle", "game_statistics", "points_scored" ] }
              },
              "my_player_daily_scores_maximum": {
                "max": { "property": [ "paddle", "game_statistics", "points_scored" ] }
              }
            }
          }
        },
        "my_daily_scores_average": {
          "avg": { "property": [ "paddle", "game_statistics", "points_scored" ] }
        }
      }
    }
  },
  "my_scores_maximum_across_all_games": {
    "max": { "property": [ "paddle", "game_statistics", "points_scored" ] }
  }
}
```

and the response would be
```
{
  "my_groups_by_1d_range": {
    "timeHistogramBuckets": [
      {
        "count": 13
        "intervalStart": "2025-05-25T00:00:00Z",
        "aggregates": {
          "my_groups_by_player_name": {
            "uniqueValueBuckets": [
              {
                "count": 3
                "value": "Vasya Rogov",
                "aggregates": {
                  "my_player_daily_scores_sum": {
                    "sum": 57
                  },
                  "my_player_daily_scores_maximum": {
                    "max": 25
                  }
                }
              },
              {
                "count": 5
                "value": "Vinni Pooh",
                "aggregates": {
                  "my_player_daily_scores_sum": {
                    "sum": 53
                  },
                  "my_player_daily_scores_maximum": {
                    "max": 21
                  }
                }
              },
              ...
            ]
          },
          "my_daily_scores_average": {
            "avg": 33
          }
        }
      },
      {
        "count": 7
        "intervalStart": "2025-05-26T00:00:00Z",
        "aggregates": {
          "my_groups_by_player_name": {
            "uniqueValueBuckets": [
              {
                "count": 1
                "value": "Vasya Rogov",
                "aggregates": {
                  "my_player_daily_scores_sum": {
                    "sum": 24
                  },
                  "my_player_daily_scores_maximum": {
                    "max": 24
                  }
                }
              },
              {
                "count": 2
                "value": "Pyatochok",
                "aggregates": {
                  "my_player_daily_scores_sum": {
                    "sum": 42
                  },
                  "my_player_daily_scores_maximum": {
                    "max": 23
                  }
                }
              },
              ...
            ]
          },
          "my_daily_scores_average": {
            "avg": 22
          }
        }
      },
      ...
    ]
  },
  "my_scores_maximum_across_all_games": {
    "max": 42
  }
}
```


**パラメータ**:

- `` (unknown, 任意)

**リクエストボディ**:

Aggregation specification.


**レスポンス**:

- `200`: 
- `400`: 

---

### /streams/{streamId}/records/delete

#### POST

**概要**: Delete records from a stream



> **Required capabilities:** `StreamRecordsAcl:WRITE`

**Note:** This endpoint is only available for mutable streams.

To delete records, the user must have capabilities to access (write to) the referenced record ```space```.

Batches of records are deleted from a ```stream``` with implicit `ignoreUnknownIds=true` semantic.

The record operations are eventually consistent,
but under normal conditions deleted records stop being returned by other endpoints within a few seconds.
Sync endpoint is returning a "tombstone" record (which only has a few top level fields and a deleted status)
for at least 3 days since deleting the actual record.


**パラメータ**:

- `` (unknown, 任意)

**リクエストボディ**:

Records to delete from a stream.


**レスポンス**:

- `200`: 
- `400`: 
- `422`: Some record identifiers (`space` and `externalId`) are duplicated in the request or attempt to delete records from an immutable stream.

- `500`: If at least one record was not deleted due to an internal error, and all other errors in the `failed` list were non-retryable,
this error code will be returned.

**Note:** Record operations are not transactional. This means that during batch delete,
some records may be deleted successfully even if the deletion of others fails.
The response body indicates which records were successfully deleted and which were not.

An internal server error usually indicates an issue that requires investigation. A simple retry might not help,
but a few retries could be a reasonable approach.

**Note:** If you choose to retry on a 500 error, it is recommended to retry only the failed records rather than the entire request.

- `503`: If at least one record was not deleted due to the service being temporarily unavailable,
this error code will be returned.

**Note:** Record operations are not transactional. This means that during batch delete, some records may be deleted successfully
even if the deletion of others fails. The response body indicates which records were successfully deleted and which were not.

A "service temporarily unavailable" error usually indicates a temporary issue, and several retries may help complete the records deletion.

**Note:** It is recommended to retry deletion of only the failed records rather than the entire request.


---

### /streams/{streamId}/records/filter

#### POST

**概要**: Retrieve records from a stream



> **Required capabilities:** `StreamRecordsAcl:READ` `DataModelsAcl:READ`

Filter the records from the ```stream```.


**パラメータ**:

- `` (unknown, 任意)

**リクエストボディ**:

Filter records based on the last updated time, on the space and on container(s) properties.
The response supports custom sorting. Otherwise, the records in the response are sorted by last updated time.


**レスポンス**:

- `200`: 
- `400`: 

---

### /streams/{streamId}/records/sync

#### POST

**概要**: Sync records from a stream



> **Required capabilities:** `StreamRecordsAcl:READ` `DataModelsAcl:READ`

Subscribe to changes for records from the ```stream```, matching a supplied filter.


**パラメータ**:

- `` (unknown, 任意)

**リクエストボディ**:

Change filter specification.


**レスポンス**:

- `200`: 
- `400`: 

---

### /streams/{streamId}/records/upsert

#### POST

**概要**: Upsert (create or update) records into a stream



> **Required capabilities:** `StreamRecordsAcl:WRITE` `DataModelsAcl:READ`

**Note:** This endpoint is only available for mutable streams.

Before a user can upsert records, all referenced schema elements (Spaces, Containers, Properties) must be defined/created in [Data Modeling](https://docs.cognite.com/cdf/dm).

To upsert records, the user must have capabilities to access (read to) the referenced containers as well as capabilities to access (write to) the referenced record ```space```.

Batches of records are ingested or updated into a ```stream```.

Under normal upsert operation, the record will be created or updated, and the properties defined in each of the containers in the ```sources``` array will be populated.

If record identifiers (```space``` + ```externalId```) are already in the stream, records are fully updated (no partial updates for this endpoint).
Otherwise, records are created.

**Note:** The maximum total request size is 10MB.


**パラメータ**:

- `` (unknown, 任意)

**リクエストボディ**:

Records to upsert into a stream.


**レスポンス**:

- `202`: 
- `400`: 
- `409`: Some records with the specified `space` and `externalId` are being concurrently modified by this and other requests.
But some records might have been created or updated successfully.
Which records were successfully upserted and which were rejected with a conflict can be understood from the response content.

**Note:** better to check which records are in the stream when you receive this error.
`.../records/filter` or `.../records/sync` endpoints with a filter by `space` and `externalId` can be used
to check the stored state of the conflicting records.

- `422`: Some record identifiers (`space` and `externalId`) are duplicated in the request or attempt to modify records in an immutable stream.

- `500`: If at least one record was not upserted due to an internal error, and all other errors in the `failed` list were non-retryable,
this error code will be returned.

**Note:** Record operations are not transactional. This means that during batch upsert,
some records may be created/updated successfully even if the creation/updating of others fails.
The response body indicates which records were successfully upserted and which were not.

An internal server error usually indicates an issue that requires investigation. A simple retry might not help,
but a few retries could be a reasonable approach.

**Note:** If you choose to retry on a 500 error, it is recommended to retry only the failed records rather than the entire request.
This helps avoid receiving 409 status codes or overriding records to outdated state (if multiple threads update records in your app).

- `503`: If at least one record was not upserted due to the service being temporarily unavailable,
this error code will be returned.

**Note:** Record operations are not transactional. This means that during batch upsert, some records may be created/updated successfully
even if the creation/updating of others fails. The response body indicates which records were successfully upserted and which were not.

A "service temporarily unavailable" error usually indicates a temporary issue, and several retries may help complete the record upserting.

**Note:** It is recommended to retry only the failed records rather than the entire request.
This helps avoid receiving 409 status codes or overriding records to outdated state (if multiple threads update records in your app).


---

### /timeseries

#### GET

**概要**: List time series



> **Required capabilities:** `timeSeriesAcl:READ`

List time series. Use `nextCursor` to paginate through the results.

**パラメータ**:

- `limit` (integer, 任意)
  - Limits the number of results to return. CDF returns a maximum of 1000 results even if you specify a higher limit.
- `` (unknown, 任意)
- `` (unknown, 任意)
- `` (unknown, 任意)
- `assetIds` (unknown, 任意)
  - Gets the time series related to the assets. The format is a list of IDs serialized as a JSON array(int64). Takes [ 1 .. 100 ] unique items.
- `rootAssetIds` (unknown, 任意)
  - Only includes time series that have a related asset in a tree rooted at any of these root `assetIds`.
- `externalIdPrefix` (unknown, 任意)

**レスポンス**:

- `200`: A list of time series in lexicographic order.

---

#### POST

**概要**: Create time series



> **Required capabilities:** `timeSeriesAcl:WRITE`

Creates one or more time series.

**リクエストボディ**:

**レスポンス**:

- `201`: Response with the created time series.
- `400`: 
- `409`: Time series with the specified externalIds already exists. Retry request, with the existing externalIds removed.
- `422`: Duplicated externalIds found. Retry request, keeping only one instance of each duplicated externalId.

---

### /timeseries/aggregate

#### POST

**概要**: Aggregate time series



> **Required capabilities:** `timeSeriesAcl:READ`

The aggregation API allows you to compute aggregated results from a set of time series, such as
getting the number of time series in a project or checking what assets the different time series
in your project are associated with (along with the number of time series for each asset).
By specifying `filter` and/or `advancedFilter`, the aggregation will take place only over those
time series that match the filters. `filter` and `advancedFilter` behave the same way as in the
`list` endpoint.

<details>
<summary>
The default behavior, when the <code>aggregate</code> field is not specified the request body, is to return the
number of time series that match the filters (if any), which is the same behavior as when the
<code>aggregate</code> field is set to <code>count</code>.
</summary>

The following requests will both return the total number of
time series whose `name` begins with `pump`:

```
{
  "advancedFilter": {"prefix": {"property": ["name"], "value": "pump"}}
}
```

and

```
{
  "aggregate": "count",
  "advancedFilter": {"prefix": {"property": ["name"], "value": "pump"}}
}
```

The response might be:

```
{"items": [{"count": 42}]}
```
</details>

<details>
<summary>
Setting <code>aggregate</code> to <code>uniqueValues</code> and specifying a property in <code>properties</code> (this field is an array, but currently only supports one property) will
return all unique values (up to a maximum of 1000) that are taken on by that property
across all the time series that match the filters, as well as the number of time series that
have each of those property values.
</summary>

This example request finds all the unique asset ids that are
referenced by the time series in your project whose `name` begins with `pump`:

```
{
  "aggregate": "uniqueValues",
  "properties": [{"property": ["assetId"]}],
  "advancedFilter": {"prefix": {"property": ["name"], "value": "pump"}}
}
```

The response might be the following, saying that 23 time series are associated with asset 18
and 107 time series are associated with asset 76:

```
{
  "items": [
    {"values": ["18"], "count": 23},
    {"values": ["76"], "count": 107}
  ]
}
```
</details>

<details>
<summary>
Setting <code>aggregate</code> to <code>cardinalityValues</code> will instead return the approximate number of
distinct values that are taken on by the given property among the matching time series.
</summary>

Example request:

```
{
  "aggregate": "cardinalityValues",
  "properties": [{"property": ["assetId"]}],
  "advancedFilter": {"prefix": {"property": ["name"], "value": "pump"}}
}
```

The result is likely exact when the set of unique values is small. In this example, there are likely two distinct asset ids among the matching time series:

```
{"items": [{"count": 2}]}
```
</details>

<details>
<summary>
Setting <code>aggregate</code> to <code>uniqueProperties</code> will return the set of unique properties whose property
path begins with <code>path</code> (which can currently only be <code>["metadata"]</code>) that are contained in the time series that match the filters.
</summary>

Example request:

```
{
  "aggregate": "uniqueProperties",
  "path": ["metadata"],
  "advancedFilter": {"prefix": {"property": ["name"], "value": "pump"}}
}
```

The result contains all the unique metadata keys in the time series whose `name` begins with
`pump`, and the number of time series that contains each metadata key:

```
{
  "items": [
    {"values": [{"property": ["metadata", "tag"]}], "count": 43},
    {"values": [{"property": ["metadata", "installationDate"]}], "count": 97}
  ]
}
```
</details>

<details>
<summary>
Setting <code>aggregate</code> to <code>cardinalityProperties</code> will instead return the approximate number of
different property keys whose path begins with <code>path</code> (which can currently only be <code>["metadata"]</code>, meaning that this can only be used to count the approximate number of distinct metadata keys among the matching time series).
</summary>

Example request:

```
{
  "aggregate": "cardinalityProperties",
  "path": ["metadata"],
  "advancedFilter": {"prefix": {"property": ["name"], "value": "pump"}}
}
```

The result is likely exact when the set of unique values is small. In this example, there are likely two distinct metadata keys among the matching time series:

```
{"items": [{"count": 2}]}
```
</details>

The `aggregateFilter` field may be specified if `aggregate` is set to `cardinalityProperties` or `uniqueProperties`. The structure of this field is similar to that of `advancedFilter`, except that the set of leaf filters is smaller (`in`, `prefix`, and `range`), and that none of the leaf filters specify a property. Unlike `advancedFilter`, which is applied _before_ the aggregation (in order to restrict the set of time series that the aggregation operation should be applied to), `aggregateFilter` is applied _after_ the initial aggregation has been performed, in order to restrict the set of results.

<details>
<summary>
Click here for more details about <code>aggregateFilter</code>. 
</summary>

When `aggregate` is set to `uniqueProperties`, the result set contains a number of _property paths_, each with an associated count that shows how many time series contained that property (among those time series that matched the `filter` and `advancedFilter`, if they were specified) . If `aggregateFilter` is specified, it will restrict the property paths included in the output. Let us add an `aggregateFilter` to the `uniqueProperties` example from above:

```
{
  "aggregate": "uniqueProperties",
  "path": ["metadata"],
  "advancedFilter": {"prefix": {"property": ["name"], "value": "pump"}},
  "aggregateFilter": {"prefix": {"value": "t"}}
}
```

Now, the result only contains those metadata properties whose key begins with `t` (but it will be the same set of metadata properties that begin with `t` as in the original query without `aggregateFilter`, and the counts will be the same):

```
{
  "items": [
    {"values": [{"property": ["metadata", "tag"]}], "count": 43}
  ]
}
```

Similarly, adding `aggregateFilter` to `cardinalityProperties` will return the approximate number of properties whose property key matches `aggregateFilter` from those time series matching the `filter` and `advancedFilter` (or from all time series if neither `filter` nor `aggregateFilter` are specified):

```
{
  "aggregate": "cardinalityProperties",
  "path": ["metadata"],
  "advancedFilter": {"prefix": {"property": ["name"], "value": "pump"}},
  "aggregateFilter": {"prefix": {"value": "t"}}
}
```

As we saw above, only one property matches:

```
{"items": [{"count": 1}]}
```

Note that `aggregateFilter` is also accepted when `aggregate` is set to `cardinalityValues` or `cardinalityProperties`. For those aggregations, the effect of any `aggregateFilter` could also be achieved via a similar `advancedFilter`. However, `aggregateFilter` is not accepted when `aggregate` is omitted or set to `count`.
</details>

### Rate and concurrency limits

Rate and concurrency limits apply this endpoint. If a request exceeds one of the limits,
it will be throttled with a `429: Too Many Requests` response. More on limit types
and how to avoid being throttled is described
[here](https://developer.cognite.com/dev/concepts/request_throttling/).

| Limit          | Per project           | Per user (identity)   |
|----------------|-----------------------|-----------------------|
| Rate           | 15 requests per second| 10 requests per second|
| Concurrency    | 6 concurrent requests | 4 concurrent requests |


**リクエストボディ**:

Aggregates the time series that match the given criteria.

**レスポンス**:

- `200`: Response with the aggregated time series. The type of the response depends on the value of the `aggregate` parameter in the request.
- `400`: 

---

### /timeseries/byids

#### POST

**概要**: Retrieve time series



> **Required capabilities:** `timeSeriesAcl:READ`

Retrieves one or more time series by ID or external ID. The response returns the time series in the same order as in the request.

**リクエストボディ**:

List of the IDs of the time series to retrieve.

**レスポンス**:

- `200`: Response with a list of time series matching the IDs.
- `400`: IDs not found.
- `422`: Duplicate IDs found. Retry request, keeping only one instance of each duplicated ID.

---

### /timeseries/data

#### POST

**概要**: Insert data points



> **Required capabilities:** `timeSeriesAcl:WRITE`

Insert data points into a time series. You can do this for multiple time series.
If you insert a data point with a timestamp that already exists, it will be overwritten with the new value.

**リクエストボディ**:

The datapoints to insert.

**レスポンス**:

- `200`: 
- `400`: IDs not found.
- `422`: Duplicate IDs found. Retry request, keeping only one instance of each duplicated ID.

---

### /timeseries/data/delete

#### POST

**概要**: Delete data points



> **Required capabilities:** `timeSeriesAcl:WRITE`

Delete data points from time series.

**リクエストボディ**:

The list of delete requests to perform.

**レスポンス**:

- `200`: 
- `400`: IDs not found.

---

### /timeseries/data/latest

#### POST

**概要**: Retrieve latest data point



> **Required capabilities:** `timeSeriesAcl:READ`

Retrieves the latest data point in one or more time series. Note that the latest data point
in a time series is the one with the highest timestamp, which is not necessarily the one
that was ingested most recently.


**リクエストボディ**:

The list of the queries to perform.

**レスポンス**:

- `200`: A list of responses. Each response contains a list with the most recent data point or an empty list if no data points are found.
- `400`: IDs not found.

---

### /timeseries/data/list

#### POST

**概要**: Retrieve data points



> **Required capabilities:** `timeSeriesAcl:READ`

Retrieves a list of data points from multiple time series in a project.
This operation supports aggregation and pagination.
Learn more about [aggregation](<https://docs.cognite.com/dev/concepts/aggregation/>).

Note: when `start` isn't specified in the top level and for an individual item, it will default to epoch 0, which is 1 January, 1970, thus
excluding potential existent data points before 1970. `start` needs to be specified as a negative number to get data points before 1970.

**リクエストボディ**:

Specify parameters to query for multiple data points. If you omit fields in individual data point query items, the top-level field values are used. For example, you can specify a default limit for all items by setting the top-level limit field. If you request aggregates, only the aggregates are returned. If you don't request any aggregates, all data points are returned.

**レスポンス**:

- `200`: Lists of data points for the specified queries.
- `400`: IDs not found.

---

### /timeseries/delete

#### POST

**概要**: Delete time series



> **Required capabilities:** `timeSeriesAcl:WRITE`

Deletes the time series with the specified IDs and their data points.

**リクエストボディ**:

Specify a list of the time series to delete.

**レスポンス**:

- `200`: 
- `400`: IDs not found.
- `422`: Duplicate IDs found. Retry request, keeping only one instance of each duplicated ID.

---

### /timeseries/list

#### POST

**概要**: Filter time series



> **Required capabilities:** `timeSeriesAcl:READ`

<details>
<summary>
Retrieves a list of time series that match the given criteria.
</summary>

### Advanced filtering

The `advancedFilter`
field lets you create complex filtering expressions that combine simple operations,
such as `equals`, `prefix`, and `exists`, by using the Boolean operators `and`, `or`, and `not`.
Filtering applies to basic fields as well as metadata. See the `advancedFilter` syntax in the request example.



#### Supported leaf filters

| Leaf filter    | Supported fields       | Description and example                                                                                                                                                            |
|----------------|------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `containsAll`  | Array type fields      | Only includes results which contain all of the specified values. <br /> `{"containsAll": {"property": ["property"], "values": [1, 2, 3]}}`                                         |
| `containsAny`  | Array type fields      | Only includes results which contain at least one of the specified values. <br /> `{"containsAny": {"property": ["property"], "values": [1, 2, 3]}}`                                |
| `equals`       | Non-array type fields  | Only includes results that are equal to the specified value. <br /> `{"equals": {"property": ["property"], "value": "example"}}`                                                   |
| `exists`       | All fields             | Only includes results where the specified property exists (has a value). <br /> `{"exists": {"property": ["property"]}}`                                                           |
| `in`           | Non-array type fields  | Only includes results that are equal to one of the specified values. <br /> `{"in": {"property": ["property"], "values": [1, 2, 3]}}`                                              |
| `prefix`       | String type fields     | Only includes results which start with the specified text. <br /> `{"prefix": {"property": ["property"], "value": "example"}}`                                                     |
| `range`        | Non-array type fields  | Only includes results that fall within the specified range. <br /> `{"range": {"property": ["property"], "gt": 1, "lte": 5}}` <br /> Supported operators: `gt`, `lt`, `gte`, `lte` |
 | `search`       | `["name"]` and `["description"]` | Introduced to provide functional parity with the /timeseries/search endpoint. <br /> `{"search": {"property": ["property"], "value": "example"}}`                        |

#### Supported properties

| Property                          | Type               |
|-----------------------------------|--------------------|
| `["description"]`                 | string             |
| `["externalId"]`                  | string             |
| `["metadata", "<someCustomKey>"]` | string             |
| `["name"]`                        | string             |
| `["unit"]`                         | string              |
| `["unitExternalId"]`               | string              |
| `["unitQuantity"]`                 | string              |
| `["instanceId", "space"]`          | string              |
| `["instanceId", "externalId"]`     | string              |
| `["assetId"]`                      | number              |
| `["assetRootId"]`                  | number              |
| `["createdTime"]`                  | number              |
| `["dataSetId"]`                    | number              |
| `["id"]`                           | number              |
| `["lastUpdatedTime"]`              | number              |
| `["isStep"]`                       | Boolean             |
| `["isString"]`                     | Boolean             |
| `["type"]`                         | string              |
| `["securityCategories"]`           | array of numbers    |

#### Limits

- Filter query max depth: 10.
- Filter query max number of clauses: 100.
- `and` and `or` clauses must have at least one element (and at most 99, since each element counts
  towards the total clause limit, and so does the `and`/`or` clause itself).
- The `property` array of each leaf filter has the following limitations:
  - Number of elements in the array is 1 or 2.
  - Elements must not be null or blank.
  - Each element max length is 256 characters.
  - The `property` array must match one of the existing properties (static top-level property or dynamic metadata property).
- `containsAll`, `containsAny`, and `in` filter `values` array size must be in the range [1, 100].
- `containsAll`, `containsAny`, and `in` filter `values` array must contain elements of number or string type (matching the type of the given property).
- `range` filter must have at lest one of `gt`, `gte`, `lt`, `lte` attributes.
  But `gt` is mutually exclusive to `gte`, while `lt` is mutually exclusive to `lte`.
- `gt`, `gte`, `lt`, `lte` in the `range` filter must be of number or string type (matching the type of the given property).
- `search` filter `value` must not be blank, and the length must be in the range [1, 128], and there
  may be at most two `search` filters in the entire filter query.
- The maximum length of the `value` of a leaf filter that is applied to a string property is 256.

### Sorting

By default, time series are sorted by their creation time in ascending order.
Sorting by another property or by several other properties can be explicitly requested via the
`sort` field, which must contain a list
of one or more sort specifications. Each sort specification indicates the `property` to sort on
and, optionally, the `order` in which to sort (defaults to `asc`). If multiple sort specifications are
supplied, the results are sorted on the first property, and those with the same value for the first
property are sorted on the second property, and so on.  
Partitioning is done independently of sorting; there is no guarantee of sort order between elements from different partitions.

#### Null values

In case the `nulls` field has the `auto` value, or the field isn't specified, null (missing) values
are considered bigger than any other values. They are placed last when sorting in the `asc`
order and first in the `desc` order. Otherwise, missing values are placed according to
the `nulls` field (`last` or `first`), and their placement won't depend on the `order`
field. Note that the number zero, empty strings, and empty lists are all considered
_not_ null.

#### Example

```json
{
  "sort": [
    {
      "property" : ["createdTime"],
      "order": "desc",
      "nulls": "last"
    },
    {
      "property" : ["metadata", "<someCustomKey>"]
    }
  ]
}
```


#### Properties

You can sort on the following properties:

| Property                          |
|-----------------------------------|
| `["assetId"]`                     |
| `["createdTime"]`                 |
| `["dataSetId"]`                   |
| `["description"]`                 |
| `["externalId"]`                  |
| `["lastUpdatedTime"]`             |
| `["metadata", "<someCustomKey>"]` |
| `["name"]`                        |
| `["instanceId", "space"]`         |
| `["instanceId", "externalId"]`    |

#### Limits

The `sort` array must contain 1 to 2 elements.
</details>


**リクエストボディ**:

**レスポンス**:

- `200`: Response with a list of time series matching the specified criteria.

---

### /timeseries/search

#### POST

**概要**: Search time series



> **Required capabilities:** `timeSeriesAcl:READ`

Fulltext search for time series based on result relevance. Primarily meant
for human-centric use cases, not for programs, since matching and
order may change over time. Additional filters can also be
specified. This operation does not support pagination.

**リクエストボディ**:

**レスポンス**:

- `200`: List of search results.

---

### /timeseries/subscriptions

#### GET

**概要**: List subscriptions



> **Required capabilities:** `timeSeriesSubscriptionsAcl:READ`

List all subscriptions for the tenant. Use nextCursor to paginate through the results.

**パラメータ**:

- `limit` (integer, 任意)
  - Limit on the number of results to return.
- `` (unknown, 任意)

**レスポンス**:

- `200`: A complete list of subscriptions in the tenant.

---

#### POST

**概要**: Create subscriptions



> **Required capabilities:** `timeSeriesSubscriptionsAcl:WRITE`

Create one or more subscriptions that can be used to listen for changes in data points for a set of time series.

**リクエストボディ**:

The subscriptions to create.

**レスポンス**:

- `201`: A list of subscriptions that were created.

---

### /timeseries/subscriptions/byids

#### POST

**概要**: Retrieve subscriptions



> **Required capabilities:** `timeSeriesSubscriptionsAcl:READ`

Retrieve one or more subscriptions by external ID.

**リクエストボディ**:

Specify a list of the subscriptions to retrieve.

**レスポンス**:

- `200`: The retrieved subscriptions.

---

### /timeseries/subscriptions/data/list

#### POST

**概要**: List subscription data



> **Required capabilities:** `timeSeriesSubscriptionsAcl:READ`

Fetch the next batch of data from a given subscription and partition(s).
Data can be ingested datapoints and time ranges where data is deleted.
This endpoint will also return changes to the subscription itself, that is, if time series
are added or removed from the subscription.

The data can be returned multiple times, there is no mechanism to acknowledge or delete data.
Use cursors to paginate through the results.

**リクエストボディ**:

**レスポンス**:

- `200`: A batch of data from the subscription.

---

### /timeseries/subscriptions/delete

#### POST

**概要**: Delete subscriptions



> **Required capabilities:** `timeSeriesSubscriptionsAcl:WRITE`

Delete subscription(s). This operation cannot be undone

**リクエストボディ**:

Specify a list of the subscriptions to delete.

**レスポンス**:

- `200`: 
- `400`: IDs not found.

---

### /timeseries/subscriptions/members

#### GET

**概要**: List subscription members



> **Required capabilities:** `timeSeriesSubscriptionsAcl:READ`

List all member time series for a subscription.

For non-filter subscriptions, either `externalId` or `instanceId` will be set. This is to be
able to see if each time series was added by external id or instance id. If a time series
has been added to a subscription both by external id and by instance id, it will be listed
twice in the output. If a time series that belonged to the subscription has been deleted,
`id` will not be set.

Use `nextCursor` to paginate through the results.

**パラメータ**:

- `limit` (integer, 任意)
  - Limit on the number of results to return.
- `` (unknown, 任意)
- `externalId` (unknown, 任意)
  - External id of the subscription.

**レスポンス**:

- `200`: A complete list of member time series in the subscription.

---

### /timeseries/subscriptions/update

#### POST

**概要**: Update subscriptions



> **Required capabilities:** `timeSeriesSubscriptionsAcl:WRITE`

Updates one or more subscriptions. Fields that are not included in the request, are not changed.

If both `instanceIds` and `timeSeriesIds` are set, they must either both be add/remove or
both be set operations.

**リクエストボディ**:

The subscriptions to update

**レスポンス**:

- `200`: A list of subscriptions updated

---

### /timeseries/synthetic/query

#### POST

**概要**: Synthetic query

Execute an on-the-fly synthetic query

**リクエストボディ**:

The list of queries to perform

**レスポンス**:

- `200`: List of datapoints for the specified queries.
- `400`: Query error

---

### /timeseries/update

#### POST

**概要**: Update time series



> **Required capabilities:** `timeSeriesAcl:WRITE`

Updates one or more time series. Fields outside of the request remain unchanged.

For primitive fields (those whose type is string, number, or boolean), use `"set": value`
to update the value; use `"setNull": true` to set the field to null.

For JSON array fields (for example `securityCategories`), use `"set": [value1, value2]` to
update the value; use `"add": [value1, value2]` to add values; use
`"remove": [value1, value2]` to remove values.

**リクエストボディ**:

List of changes.

**レスポンス**:

- `200`: Response with the corresponding time series after applying the updates.
- `400`: IDs not found.
- `409`: Time series with the specified externalIds already exists. Retry request, with the existing externalIds removed.
- `422`: Duplicate IDs found. Retry request, keeping only one instance of each duplicated ID.

---

### /token/inspect

#### GET

**概要**: Inspect



> **Required capabilities:** `projectsAcl:LIST` `groupsAcl:LIST`

Inspect CDF access granted to an IdP issued token

**レスポンス**:

- `200`: Ok response
- `401`: 
- `403`: 

---

### /transformations

#### GET

**概要**: List transformations



> **Required capabilities:** `transformationsAcl:READ`

List transformations. Use nextCursor to paginate through the results.

**パラメータ**:

- `limit` (integer, 任意)
  - Limits the number of results to be returned. The maximum is 1000, default limit is 100.
- `cursor` (string, 任意)
  - Cursor for paging through results.
- `includePublic` (boolean, 任意)
  - Whether public transformations should be included in the results. The default is true.
- `withJobDetails` (boolean, 任意)
  - Whether transformations should contain information about jobs. The default is true.

**レスポンス**:

- `200`: Paged response with list of transformations.
- `400`: The response for a failed request.
- `403`: The response for a forbidden request.

---

#### POST

**概要**: Create transformations



> **Required capabilities:** `transformationsAcl:WRITE`

Create a maximum of 1000 transformations per request.

**リクエストボディ**:

**レスポンス**:

- `200`: Response with list of transformations.
- `400`: The response for a failed request.
- `403`: The response for a forbidden request.
- `409`: The response for a conflict.

---

### /transformations/byids

#### POST

**概要**: Retrieve transformations



> **Required capabilities:** `transformationsAcl:READ`

Retrieve a maximum of 1000 transformations by ids and externalIds per request.

**リクエストボディ**:

**レスポンス**:

- `200`: Response with list of transformations.
- `400`: The response for a failed request.
- `403`: The response for a forbidden request.
- `409`: The response for a conflict.

---

### /transformations/cancel

#### POST

**概要**: Cancel a transformation



> **Required capabilities:** `transformationsAcl:WRITE`


**リクエストボディ**:

**レスポンス**:

- `200`: Empty response.
- `400`: The response for a failed request.
- `403`: The response for a forbidden request.

---

### /transformations/delete

#### POST

**概要**: Delete transformations



> **Required capabilities:** `transformationsAcl:WRITE`

Delete a maximum of 1000 transformations by ids and externalIds per request.

**リクエストボディ**:

**レスポンス**:

- `200`: Empty response.
- `400`: The response for a failed request.
- `403`: The response for a forbidden request.
- `409`: The response for a conflict.

---

### /transformations/filter

#### POST

**概要**: Filter transformations



> **Required capabilities:** `transformationsAcl:READ`

Filter transformations. Use nextCursor to paginate through the results.

**リクエストボディ**:

**レスポンス**:

- `200`: Paged response with list of transformations.
- `400`: The response for a failed request.
- `403`: The response for a forbidden request.

---

### /transformations/jobs

#### GET

**概要**: List jobs



> **Required capabilities:** `transformationsAcl:READ`


**パラメータ**:

- `transformationId` (integer, 任意)
  - List only jobs for the specified transformation. The transformation is identified by ID.
- `transformationExternalId` (string, 任意)
  - List only jobs for the specified transformation. The transformation is identified by external ID.
- `limit` (integer, 任意)
  - Limits the number of results to be returned. The maximum is 1000, default limit is 100.
- `cursor` (string, 任意)
  - Cursor for paging through results.

**レスポンス**:

- `200`: Response with list of jobs.
- `400`: The response for a failed request.
- `403`: The response for a forbidden request.

---

### /transformations/jobs/byids

#### POST

**概要**: Retrieve jobs by ids



> **Required capabilities:** `transformationsAcl:READ`

Retrieve a maximum of 1000 jobs by ids per request.

**リクエストボディ**:

**レスポンス**:

- `200`: Response with list of jobs.
- `400`: The response for a failed request.
- `403`: The response for a forbidden request.
- `409`: The response for a conflict.

---

### /transformations/jobs/{id}/metrics

#### GET

**概要**: List job metrics by job id



> **Required capabilities:** `transformationsAcl:READ`


**パラメータ**:

- `id` (integer, 必須)
  - The job id.

**レスポンス**:

- `200`: Metrics for how many resources has been read/written by the transformation.
- `400`: The response for a failed request.
- `403`: The response for a forbidden request.

---

### /transformations/notifications

#### GET

**概要**: List notification subscriptions



> **Required capabilities:** `transformationsAcl:READ`


**パラメータ**:

- `transformationId` (integer, 任意)
  - List only notifications for the specified transformation. The transformation is identified by ID.
- `transformationExternalId` (string, 任意)
  - List only notifications for the specified transformation. The transformation is identified by external ID.
- `destination` (string, 任意)
  - Filter by notification destination.
- `limit` (integer, 任意)
  - Limits the number of results to be returned. The maximum is 1000, default limit is 100.
- `cursor` (string, 任意)
  - Cursor for paging through results.

**レスポンス**:

- `200`: Response with paged list of notification subscriptions.
- `400`: The response for a failed request.
- `403`: The response for a forbidden request.

---

#### POST

**概要**: Subscribe for notifications



> **Required capabilities:** `transformationsAcl:WRITE`

Subscribe for notifications on the transformation errors.

**リクエストボディ**:

List of the notifications for new subscriptions. Must be up to a maximum of 1000 items.

**レスポンス**:

- `200`: Response with list of created notification subscriptions.
- `400`: The response for a failed request.
- `403`: The response for a forbidden request.
- `409`: The response for a conflict.

---

### /transformations/notifications/delete

#### POST

**概要**: Delete notification subscriptions by notification ID



> **Required capabilities:** `transformationsAcl:WRITE`

Deletes the specified notification subscriptions on the transformation. Requests to delete non-existing subscriptions do nothing, but do not throw an error.

**リクエストボディ**:

List of IDs to be deleted.

**レスポンス**:

- `200`: Empty response.
- `400`: The response for a failed request.
- `403`: The response for a forbidden request.
- `409`: The response for a conflict.

---

### /transformations/query/run

#### POST

**概要**: Run query



> **Required capabilities:** `transformationsAcl:READ`

Preview a SQL query.

**リクエストボディ**:

**レスポンス**:

- `200`: Response with resulting rows from the query.
- `400`: The response for a failed request.
- `403`: The response for a forbidden request.

---

### /transformations/run

#### POST

**概要**: Run a transformation



> **Required capabilities:** `transformationsAcl:WRITE`


**リクエストボディ**:

**レスポンス**:

- `200`: Response with a single job.
- `400`: The response for a failed request.
- `403`: The response for a forbidden request.

---

### /transformations/schedules

#### GET

**概要**: List all schedules



> **Required capabilities:** `transformationsAcl:READ`

List all transformation schedules. Use nextCursor to paginate through the results.

**パラメータ**:

- `limit` (integer, 任意)
  - Limits the number of results to be returned. The maximum is 1000, default limit is 100.
- `cursor` (string, 任意)
  - Cursor for paging through results.
- `includePublic` (boolean, 任意)
  - Whether public transformations should be included in the results. The default is true.

**レスポンス**:

- `200`: Response with list of schedules.
- `400`: The response for a failed request.
- `403`: The response for a forbidden request.
- `409`: The response for a conflict.

---

#### POST

**概要**: Schedule transformations



> **Required capabilities:** `transformationsAcl:WRITE`

Schedule transformations with the specified configurations.

**リクエストボディ**:

List of the schedules to create. Must be up to a maximum of 1000 items.

**レスポンス**:

- `201`: Response with list of schedules.
- `400`: The response for a failed request.
- `403`: The response for a forbidden request.
- `409`: The response for a conflict.

---

### /transformations/schedules/byids

#### POST

**概要**: Retrieve schedules



> **Required capabilities:** `transformationsAcl:READ`

Retrieve transformation schedules by transformation IDs or external IDs.

**リクエストボディ**:

List of transformation IDs of schedules to retrieve. Must be up to a maximum of 1000 items and all of them must be unique.

**レスポンス**:

- `200`: Response with list of schedules.
- `400`: The response for a failed request.
- `403`: The response for a forbidden request.
- `409`: The response for a conflict.

---

### /transformations/schedules/delete

#### POST

**概要**: Unschedule transformations



> **Required capabilities:** `transformationsAcl:WRITE`

Unschedule transformations by IDs or externalIds.

**リクエストボディ**:

List of transformation IDs of schedules to delete. Must be up to a maximum of 1000 items and all of them must be unique.

**レスポンス**:

- `200`: Empty response.
- `400`: The response for a failed request.
- `403`: The response for a forbidden request.
- `409`: The response for a conflict.

---

### /transformations/schedules/update

#### POST

**概要**: Update schedules



> **Required capabilities:** `transformationsAcl:WRITE`


**リクエストボディ**:

List of schedule updates. Must be up to a maximum of 1000 items.

**レスポンス**:

- `200`: Response with list of schedules.
- `400`: The response for a failed request.
- `403`: The response for a forbidden request.
- `409`: The response for a conflict.

---

### /transformations/schema/instances

#### GET

**概要**: Get Instance schema.



> **Required capabilities:** `transformationsAcl:READ`

For View centric schema, `viewSpace`, `viewExternalId`, `viewVersion` need to be specified while `withInstanceSpace`, `isConnectionDefinition`, `instanceType` are optional . For Data Model centric schema, `dataModelSpace`, `dataModelExternalId`, `dataModelVersion`, `type` need to be specified and `relationshipFromType` is optional. For Both scenarios `conflictMode` is required.

**パラメータ**:

- `conflictMode` (string, 任意)
  - conflict mode of the transformation.
 One of the following conflictMode types can be provided:
 `upsert`, `delete`
 
- `viewSpace` (string, 任意)
  - Space of the View. Not required if `isConnectionDefinition` is true. Relevant for View centric schema.
- `viewExternalId` (string, 任意)
  - External id of the View. Not required if `isConnectionDefinition` is true. Relevant for View centric schema.
- `viewVersion` (string, 任意)
  - Version of the View. Not required if `isConnectionDefinition` is true. Relevant for View centric schema.
- `instanceType` (string, 任意)
  - Instance type to deal with
- `withInstanceSpace` (boolean, 任意)
  - Is instance space set at the transformation config or not
- `isConnectionDefinition` (boolean, 任意)
  - If the edge is a connection definition or not
- `dataModelSpace` (string, 任意)
  - Space of the Data Model. Relevant for Data Model centric schema.
- `dataModelExternalId` (string, 任意)
  - External id of the Data Model. Relevant for Data Model centric schema.
- `dataModelVersion` (string, 任意)
  - Version of the Data Model. Relevant for Data Model centric schema.
- `type` (string, 任意)
  - External id of the View in the Data model. Relevant for Data Model centric schema.
- `relationshipFromType` (string, 任意)
  - Property Identifier of Connection Definition in `type`. Relevant for Data Model centric schema.

**レスポンス**:

- `200`: Response with the schema columns of the target schema type
- `400`: The response for a failed request.
- `403`: The response for a forbidden request.

---

### /transformations/schema/sequence_rows

#### GET

**概要**: Get the schema of a sequence



> **Required capabilities:** `transformationsAcl:READ`


**パラメータ**:

- `externalId` (string, 必須)
  - External ID of the Sequence

**レスポンス**:

- `200`: Response with the schema columns of the target schema type
- `400`: The response for a failed request.
- `403`: The response for a forbidden request.

---

### /transformations/schema/{schemaType}

#### GET

**概要**: Get the schema of resource type



> **Required capabilities:** `transformationsAcl:READ`


**パラメータ**:

- `schemaType` (string, 必須)
  - Name of the target schema type, please provide one of the following:
 `assets`, `timeseries`, `asset_hierarchy`, `events`, `datapoints`
 `string_datapoints`, `sequences`, `files`, `labels`,
 `relationships`, `raw`, `data_sets`
- `conflictMode` (string, 任意)
  - One of the following conflictMode types can be provided:
 `abort`, `upsert`, `update`, `delete`

**レスポンス**:

- `200`: Response with the schema columns of the target schema type
- `400`: The response for a failed request.
- `403`: The response for a forbidden request.

---

### /transformations/update

#### POST

**概要**: Update transformations



> **Required capabilities:** `transformationsAcl:WRITE`

Update the attributes of transformations, maximum 1000 per request.

**リクエストボディ**:

**レスポンス**:

- `200`: Response with list of transformations.
- `400`: The response for a failed request.
- `403`: The response for a forbidden request.
- `409`: The response for a conflict.

---

### /units

#### GET

**概要**: List all units

List all units.

**レスポンス**:

- `200`: Response with a list of units

---

### /units/byids

#### POST

**概要**: Retrieve units by external IDs

Retrieves one or more units by external ID. The response returns the units in the same order as in the request.

**リクエストボディ**:

**レスポンス**:

- `200`: Response with a list of units
- `400`: Bad Request

---

### /units/systems

#### GET

**概要**: List all unit systems

List all unit systems.

**レスポンス**:

- `200`: Response with a list of unit systems

---

### /units/{externalId}

#### GET

**概要**: Retrieve unit by external ID

Retrieve a single unit by its external ID.

**パラメータ**:

- `externalId` (string, 必須)
  - External ID of the unit

**レスポンス**:

- `200`: Response with a single unit
- `400`: Bad Request

---

### /update

### /workflows

#### GET

**概要**: List workflows



> **Required capabilities:** `workflowOrchestrationACL:READ`

List workflows in the project.

**パラメータ**:

- `limit` (unknown, 任意)
- `cursor` (unknown, 任意)

**レスポンス**:

- `200`: List of workflows

---

#### POST

**概要**: Create or update a workflow



> **Required capabilities:** `workflowOrchestrationACL:WRITE`

Create or update a workflow. Limited to a single workflow per request.

**リクエストボディ**:

**レスポンス**:

- `200`: List of created workflows
- `400`: 

---

### /workflows/delete

#### POST

**概要**: Delete workflows



> **Required capabilities:** `workflowOrchestrationACL:WRITE`

Delete workflows, including all associated workflow versions.

**パラメータ**:

- `ignoreUnknownIds` (boolean, 任意)
  - If `true`, ignore unknown workflow ids. If `false`, a `404 Not Found` error is returned and none of the workflows are deleted if any of the workflow ids are unknown.

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `404`: 

---

### /workflows/executions/list

#### POST

**概要**: Filter workflow executions



> **Required capabilities:** `workflowOrchestrationACL:READ`

List workflow executions matching a given filter.

**リクエストボディ**:

**レスポンス**:

- `200`: Filtered list of workflow executions

---

### /workflows/executions/{executionId}

#### GET

**概要**: Retrieve workflow execution details



> **Required capabilities:** `workflowOrchestrationACL:READ`

Retrieve detailed information about a specific workflow execution.

**レスポンス**:

- `200`: Details of a workflow execution

---

### /workflows/executions/{executionId}/cancel

#### POST

**概要**: Cancel a workflow execution



> **Required capabilities:** `workflowOrchestrationACL:WRITE`

Stops the specified execution from starting new workflow tasks and sets the workflow execution status to `TERMINATED`. Already running tasks will be marked as CANCELED. Note that the actions taken by the canceled tasks won't be stopped, and these need to be canceled separately if desired. For example, to cancel a running transformation, use the [/transformations/cancel](#/Transformations/postApiV1ProjectsProjectTransformationsCancel) endpoint.

**パラメータ**:

- `executionId` (unknown, 必須)

**リクエストボディ**:

**レスポンス**:

- `200`: Updated workflow execution
- `404`: 

---

### /workflows/executions/{executionId}/retry

#### POST

**概要**: Retry workflow execution



> **Required capabilities:** `workflowOrchestrationACL:WRITE`

This endpoint restarts a previously failed, timed out, or terminated workflow execution by retrying tasks that did not complete successfully. It aims to resume execution activity from the point(s) of failure.

Behavior of the retry operation:
- Targeted Task Retry: Only retries tasks that have stopped in a terminal state such as `CANCELED`, `FAILED`, `FAILED_WITH_TERMINAL_ERROR`, and `TIMED_OUT`. Optional tasks are not retried.
- Subworkflows and Dynamic Tasks: When a failure occurs within a subworkflow or as part of a dynamic task, only the individual nested tasks that failed are retried. The subworkflow or dynamic task container itself is not retried.
- Retry Limits: Tasks that have reached or exceeded their designated retry limits will not have their retry counts reset to zero. Instead, each retry request permits these tasks a single additional retry.


**パラメータ**:

- `executionId` (unknown, 必須)

**リクエストボディ**:

**レスポンス**:

- `200`: Updated workflow execution
- `404`: 

---

### /workflows/tasks/{taskId}/update

#### POST

**概要**: Update task status



> **Required capabilities:** `workflowOrchestrationACL:WRITE`

Update the status of a task that is defined to be completed asynchronously (i.e. task parameter `isAsyncComplete` is set to `true`). The status can be set to `COMPLETED`, `FAILED`, or `FAILED_WITH_TERMINAL_ERROR`.

 - `COMPLETED`: the workflow execution will continue according to the workflow definition.\
 - `FAILED`: the task will be retried according to the `retries` parameter for the task in the workflow definition.\
 - `FAILED_WITH_TERMINAL_ERROR`: the task won't be retried.\
If an `output` is provided, it'll be merged with the original output of the task.

**リクエストボディ**:

**レスポンス**:

- `200`: Updated task

---

### /workflows/triggers

#### GET

**概要**: List triggers



> **Required capabilities:** `workflowOrchestrationACL:READ`

List all triggers. The results are sorted by `externalId` in ascending order.

**パラメータ**:

- `limit` (unknown, 任意)
- `cursor` (unknown, 任意)

**レスポンス**:

- `200`: List of triggers

---

#### POST

**概要**: Create or update triggers



> **Required capabilities:** `workflowOrchestrationACL:WRITE`

Create or update a trigger.

**リクエストボディ**:

**レスポンス**:

- `200`: List of created or updated triggers

---

### /workflows/triggers/delete

#### POST

**概要**: Delete triggers



> **Required capabilities:** `workflowOrchestrationACL:WRITE`

Delete a trigger.

**リクエストボディ**:

**レスポンス**:

- `200`: 

---

### /workflows/triggers/{triggerExternalId}/history

#### GET

**概要**: Get the run history of a trigger



> **Required capabilities:** `workflowOrchestrationACL:READ`

Get information about every run of a trigger. This includes timing information and a reference to the started execution (or an explanation as to why it failed). The returned items are sorted by `fireTime` in descending order (newest first), followed by `externalId` in ascending order.

**パラメータ**:

- `triggerExternalId` (unknown, 必須)
- `limit` (unknown, 任意)
- `cursor` (unknown, 任意)

**レスポンス**:

- `200`: A list of runs of the trigger
- `404`: 

---

### /workflows/triggers/{triggerExternalId}/pause

#### POST

**概要**: Pause a trigger



> **Required capabilities:** `workflowOrchestrationACL:WRITE`

Pauses a trigger. When paused, the trigger will not fire until it is resumed. Note: For data modelling triggers, processing continues where the last trigger run left off at pause time, not necessarily starting again on the freshest data. In addition, the data modelling trigger can be invalid if paused too long.

**パラメータ**:

- `triggerExternalId` (unknown, 必須)

**レスポンス**:

- `200`: 
- `404`: The trigger was not found

---

### /workflows/triggers/{triggerExternalId}/resume

#### POST

**概要**: Resume a trigger



> **Required capabilities:** `workflowOrchestrationACL:WRITE`

Resumes a trigger. Once resumed, the trigger will fire according to its configuration. Note: For data modelling triggers, processing continues where the last trigger run left off at pause time, not necessarily starting again on the freshest data. In addition, the data modelling trigger can be invalid if paused too long.

**パラメータ**:

- `triggerExternalId` (unknown, 必須)

**レスポンス**:

- `200`: 
- `404`: The trigger was not found

---

### /workflows/validate

### /workflows/versions

#### POST

**概要**: Create or update a workflow version



> **Required capabilities:** `workflowOrchestrationACL:WRITE`

Create or update a workflow version. Limited to a single workflow version per request.

**リクエストボディ**:

**レスポンス**:

- `200`: Workflow version created/updated

---

### /workflows/versions/delete

#### POST

**概要**: Delete workflow versions



> **Required capabilities:** `workflowOrchestrationACL:WRITE`

Delete specific versions of one or more workflows.

**パラメータ**:

- `ignoreUnknownIds` (boolean, 任意)
  - If `true`, ignore unknown version ids. If `false`, a `404 Not Found` error is returned and none of the versions are deleted if any of the version ids are unknown.

**リクエストボディ**:

**レスポンス**:

- `200`: 
- `404`: 

---

### /workflows/versions/list

#### POST

**概要**: Filter workflow versions



> **Required capabilities:** `workflowOrchestrationACL:READ`

List workflow versions matching a given filter.

**リクエストボディ**:

**レスポンス**:

- `200`: List of workflow versions

---

### /workflows/workers/tasks/extendlease

### /workflows/workers/tasks/poll

### /workflows/workers/tasks/update

### /workflows/{workflowExternalId}

#### GET

**概要**: Retrieve a workflow



> **Required capabilities:** `workflowOrchestrationACL:READ`

Retrieve a workflow by its external id.

**レスポンス**:

- `200`: Information about the workflow
- `404`: 

---

### /workflows/{workflowExternalId}/versions/{version}

#### GET

**概要**: Retrieve a workflow version



> **Required capabilities:** `workflowOrchestrationACL:READ`

Retrieve a version of a given workflow.

**レスポンス**:

- `200`: Specific version of a workflow
- `404`: 

---

### /workflows/{workflowExternalId}/versions/{version}/run

#### POST

**概要**: Run a workflow



> **Required capabilities:** `workflowOrchestrationACL:WRITE`

Start an execution of a specific version of a workflow.

**リクエストボディ**:

**レスポンス**:

- `202`: Information about the workflow execution

---

### /writeback/sap/endpoints

#### GET

**概要**: List SAP Endpoints



> **Required capabilities:** `SapWritebackAcl:READ`

List all SAP endpoints in a given CDF project. If more instances exist than what the limit specifies, a pagination cursor is returned in the response body.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: List of endpoints and an optional cursor.
- `400`: 
- `422`: Validation Error

---

#### POST

**概要**: Create SAP Endpoints



> **Required capabilities:** `SapWritebackAcl:WRITE`

Create up to 100 SAP endpoints.

**リクエストボディ**:

Endpoint to create.

**レスポンス**:

- `200`: List of created endpoints
- `400`: Response for a failed request
- `422`: 

---

### /writeback/sap/endpoints/byids

#### POST

**概要**: Retrieve SAP Endpoints



> **Required capabilities:** `SapWritebackAcl:READ`

Retrieve a list of up to 100 SAP endpoints by their external ID, optionally ignoring unknown IDs.

**リクエストボディ**:

List of external IDs of SAP endpoint configurations to retrieve.

**レスポンス**:

- `200`: List of retrieved SAP endpoint configurations.
- `400`: 
- `422`: 

---

### /writeback/sap/endpoints/delete

#### POST

**概要**: Delete SAP Endpoints



> **Required capabilities:** `SapWritebackAcl:WRITE`

Delete a list of SAP endpoint configurations by their external ID. This operation will fail if any endpoint in the request is associated to a SAP instance configuration.

**リクエストボディ**:

A list of External IDs for SAP endpoint configurations to delete.

**レスポンス**:

- `200`: Empty response
- `400`: 
- `422`: 

---

### /writeback/sap/endpoints/verify

#### POST

**概要**: Verify SAP Endpoint



> **Required capabilities:** `SapWritebackAcl:WRITE`

Verify the connectivity between the writeback API, and the SAP endpoint destination.

**リクエストボディ**:

SAP Endpoint external ID which the connection test should be verified.

**レスポンス**:

- `200`: Connection Check Result
- `400`: 
- `422`: 

---

### /writeback/sap/instances

#### GET

**概要**: List SAP Instances



> **Required capabilities:** `SapWritebackAcl:READ`

List all SAP instances in a given CDF project. If more instances exist than what the limit specifies, a pagination cursor is returned in the response body.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: List of SAP instances, and an optional cursor.
- `400`: 
- `422`: Validation Error

---

#### POST

**概要**: Create SAP Instances



> **Required capabilities:** `SapWritebackAcl:WRITE`

Create up to 100 SAP instances.

**リクエストボディ**:

Instance to create.

**レスポンス**:

- `200`: List of created instances
- `400`: Response for a failed request
- `422`: 

---

### /writeback/sap/instances/byids

#### POST

**概要**: Retrieve SAP Instances



> **Required capabilities:** `SapWritebackAcl:READ`

Retrieve a list of up to 100 SAP instances by their external ID, optionally ignoring unknown IDs.

**リクエストボディ**:

List of external IDs of SAP instances to retrieve.

**レスポンス**:

- `200`: List of retrieved instances.
- `400`: 
- `422`: 

---

### /writeback/sap/instances/delete

#### POST

**概要**: Delete SAP Instances



> **Required capabilities:** `SapWritebackAcl:WRITE`

Delete a list of SAP instance configurations by their external ID. This operation will fail if any destination in the request is associated with an SAP endpoint configuration, unless the `force` query parameter is set to `true`.

**リクエストボディ**:

List of SAP instance configuration external IDs to delete.

**レスポンス**:

- `200`: Empty response
- `400`: 
- `422`: 

---

### /writeback/sap/mappings

#### GET

**概要**: List Mappings



> **Required capabilities:** `SapWritebackAcl:READ`

List all mappings in a given CDF project. If more instances exist than what the limit specifies, a pagination cursor is returned in the response body.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `200`: List of mappings and an optional cursor.
- `400`: 
- `422`: 

---

#### POST

**概要**: Create Mappings



> **Required capabilities:** `SapWritebackAcl:WRITE`

Create a maximum of 100 mappings.

**リクエストボディ**:

Mappings to create.

**レスポンス**:

- `200`: List of created mappings
- `400`: 
- `422`: 

---

### /writeback/sap/mappings/byids

#### POST

**概要**: Retrieve Mappings



> **Required capabilities:** `SapWritebackAcl:READ`

Retrieve a list of up to 100 mappings by their external ID, optionally ignoring unknown IDs.

**パラメータ**:

- `cdf-version` (string, 任意)

**リクエストボディ**:

List of external IDs of mappings to retrieve.

**レスポンス**:

- `200`: List of retrieved mappings.
- `400`: 
- `422`: 

---

### /writeback/sap/mappings/delete

#### POST

**概要**: Delete Mappings



> **Required capabilities:** `SapWritebackAcl:WRITE`

Delete a list of mappings by their external ID.

**リクエストボディ**:

List of external IDs of mappings to delete.

**レスポンス**:

- `200`: Empty response
- `400`: 
- `422`: 

---

### /writeback/sap/requests

#### GET

**概要**: List Writeback Requests



> **Required capabilities:** `SapWritebackRequestsAcl:LIST`

List all writeback requests in a given CDF project. If more instances exist than what the limit specifies, a pagination cursor is returned in the response body.

**パラメータ**:

- `` (unknown, 任意)
- `` (unknown, 任意)

**レスポンス**:

- `202`: List of endpoints and an optional cursor.
- `400`: 
- `422`: Validation Error

---

#### POST

**概要**: Create Writeback Requests



> **Required capabilities:** `SapWritebackRequestsAcl:WRITE`

Create a writeback request

**リクエストボディ**:

Request to create.

**レスポンス**:

- `200`: List of created writeback requests
- `400`: Response for a failed request
- `422`: 

---

### /writeback/sap/requests/byids

#### POST

**概要**: Retrieve Writeback Requests



> **Required capabilities:** `SapWritebackRequestsAcl:WRITE`

Retrieve a list of up to 100 writeback requests by their external ID, optionally ignoring unknown IDs.

**リクエストボディ**:

List of external IDs of writeback requests to retrieve.

**レスポンス**:

- `200`: List of retrieved writeback requests.
- `400`: 
- `422`: 

---

## データモデル

### 429Error

Cognite API throttling.

**プロパティ**:

- `code` (integer, 必須)
  - 429 HTTP status code.
- `message` (string, 必須)
  - The error message specifies which kind of throttling happened:
  - Too many concurrent requests for a single project or an identity.
  - Too high rate of requests for a single project or an identity.

See more [here](https://docs.cognite.com/dev/concepts/request_throttling/ "requests throttling").

---

### APIError

**プロパティ**:

- `message` (string, 必須)
- `code` (integer, 必須)

---

### AWSCognitoIdP

---

### ActiveAtTimeFilter

Event is considered active from its startTime to endTime inclusive. If startTime is null, event is never active. If endTime is null, event is active from startTime onwards. activeAtTime filter will match all events that are active at some point from min to max, from min, or to max, depending on which of min and max parameters are specified.

---

### AddClaimNames

**プロパティ**:

- `add` (unknown, 必須)

---

### AddJobResultRejections

**プロパティ**:

- `job` (unknown, 必須)
- `result` (unknown, 必須)
- `add` (array, 必須)

---

### AdvancedJoin

---

### AdvancedJoinJobId

ID of an advanced join job. These are always UUIDs.

---

### AdvancedJoinMatch

---

### AdvancedJoinPatch

Matchers can be added, removed, or replaced (set).

**プロパティ**:

- `matchers` (unknown, 必須)

---

### AdvancedJoinPatchAddRemoveMatchers

**プロパティ**:

- `add` (array, 任意)
  - The matchers to add to the advanced join's matchers.
They will be added if they aren't already assigned.
- `remove` (array, 任意)
  - The matchers to remove from the advanced join's matchers.
They will be removed if they exist.

---

### AdvancedJoinPatchSetMatchers

**プロパティ**:

- `set` (array, 任意)
  - The matchers to replace the advanced join's matchers entirely.

---

### AdvancedJoinsUpdateItem

**プロパティ**:

- `externalId` (unknown, 必須)
- `update` (unknown, 必須)

---

### Aggregate

---

### AggregateCount

**プロパティ**:

- `count` (integer, 必須)
  - Number of items

---

### AggregateInFilter

**プロパティ**:

- `in` (object, 必須)
  - Matches items where the given property is **exactly** equal to one of the given values. This filter can only be applied to single-valued properties.


---

### AggregateIntegerValues

**プロパティ**:

- `count` (integer, 必須)
  - Number of items in this aggregation bucket.
- `values` (array, 必須)
  - An array of unique integer values in the property.
- `value` (integer, 任意)
  - A unique integer value in the field.

---

### AggregatePrefixFilter

**プロパティ**:

- `prefix` (object, 必須)
  - Matches aggregate values that contain a specific prefix.


---

### AggregateProperty

Property you want to aggregate.
Use a list of strings to specify nested properties.
The same way the properties are represented in aggregate responses.

<u>Example:</u>

You have the object
```
{
  "metadata": {
    "id": "b53"
  },
  "source": "a23"
}
```

To address "id" metadata key use `["metadata", "id"]` property.\
You can also aggregate the value(s) for the standalone property `source` with `["source"]`.


---

### AggregatePropertyDms

Property you want to aggregate. Use a list of strings to specify nested properties.

<u>Example:</u>

You have the object
```
{
  "room": {
    "id": "b53"
  },
  "roomId": "a23"
}
```

Use `["room", "id"]` to return the value in the nested `id` property, which is a part of the `room` object.

You can also read the value(s) in the standalone property `roomId` with `["roomId"]`.


---

### AggregatePropertyIla

Property a client wants to aggregate.
To represent fully qualified properties, Records use arrays of strings.

The aggregate supports only container level properties.
So the property array always has 3 segments:
  - the first segment is the space that the container belongs to,
  - the second segment is the container external id,
  - the third segment is the property id inside the container.

<u>Example:</u>

You have the schema
```
{
  "mySpace": {
    "myContainer": {
      "myProperty": {...}
    }
  }
}
```

Use `["mySpace", "myContainer", "myProperty"]` array to aggregate the values for `myProperty` property.


---

### AggregatePropertyValues

A single bucket to represent `uniqueProperties` aggregate result.


**プロパティ**:

- `count` (integer, 必須)
  - Number of items in this aggregation bucket.
- `values` (array, 必須)
  - An array of unique properties for UniqueProperties aggregate.

- `value` (object, 任意)
  - A unique property for UniqueProperties aggregate.


---

### AggregateRangeFilter

**プロパティ**:

- `range` (object, 必須)
  - Matches items that contain terms within the provided range.
It's not allowed to specify both inclusive and exclusive bounds (such as `gte`, `gt`) together.
`gte`: Greater than or equal to.
`gt`: Greater than.
`lte`: Less than or equal to.
`lt`: Less than.


---

### AggregateRecordsRequest

Defines an aggregation request. This will let you aggregate supported data types. The request
supports filters, and allows optional search matching.


**プロパティ**:

- `lastUpdatedTime` (unknown, 任意)
- `filter` (unknown, 任意)
- `aggregates` (unknown, 必須)
  - A dictionary of requested aggregates with client defined names/identifiers.

<u>Example:</u>

```
{
  "my_aggr_1": {
    "min": {"property": ["space1", "container1", "property1"]}
  },
  "my_aggr_2": {
    "uniqueValues": {
      "property": ["space1", "container2", "property1"],
      "aggregates": {
        "my_sub_aggr": {
          "avg": {"property": ["space2", "container1", "property3"]}
        }
      }
    }
  },
}
```

Max number of (sub-)aggregate trees on a level is 5, Max aggregate tree depth is 5. Max number of aggregates in the forest is 16.


---

### AggregateResult

---

### AggregateResultItem

Aggregated metrics of the asset.

**プロパティ**:

- `childCount` (integer, 任意)
  - Number of direct descendants for the asset.
- `depth` (integer, 任意)
  - Asset path depth (number of levels below root node).
- `path` (array, 任意)
  - IDs of assets on the path to the asset.

---

### AggregateSimulatorModelsQuery

**プロパティ**:

- `filter` (unknown, 任意)
- `aggregate` (string, 必須)
  - Aggregate type

---

### AggregateSimulatorRoutinesQuery

**プロパティ**:

- `filter` (unknown, 任意)
- `aggregate` (string, 必須)
  - Aggregate type

---

### AggregateStringValues

**プロパティ**:

- `count` (integer, 必須)
  - Number of items in this aggregation bucket.
- `values` (array, 必須)
  - An array of unique string values in the property.
- `value` (string, 任意)
  - A unique string value in the field.

---

### AggregateValue

---

### AggregatedHistogramValue

**プロパティ**:

- `aggregate` (string, 必須)
- `interval` (number, 必須)
- `property` (string, 必須)
- `buckets` (array, 必須)
  - List (array) of buckets to use for histogram aggregates.

---

### AggregatedNumberValue

**プロパティ**:

- `aggregate` (string, 必須)
- `property` (string, 任意)
  - The property the aggregate was calculated from
- `value` (number, 任意)
  - Value returned by the aggregate function

---

### AggregatedProperties

**プロパティ**:

- `aggregatedProperties` (array, 任意)
  - Set of aggregated properties to include

---

### AggregatedProperty

---

### AggregatedResultItem

**プロパティ**:

- `instanceType` (unknown, 必須)
- `group` (object, 任意)
- `aggregates` (array, 必須)

---

### AggregatedResultItemCollection

**プロパティ**:

- `items` (array, 必須)

---

### AggregatedValueItem

---

### AggregatesDefinition

A dictionary of requested aggregates with client defined names/identifiers.

<u>Example:</u>

```
{
  "my_aggr_1": {
    "min": {"property": ["room", "size"]}
  },
  "my_aggr_2": {
    "max": {"property": ["room", "size"]}
  },
}
```

---

### AggregatesDefinitionIla

---

### AggregatesResultDefinition

A dictionary of the results for the requested aggregates mapped by the aggregates identifiers.

<u>Example:</u>

```
{
  "my_aggr_1": {
    "avg": 42
  },
  "my_aggr_2": {
    "max": 69
  },
}
```

---

### AggregatesResultDefinitionIla

A dictionary of the results for the requested aggregates mapped by the aggregates identifiers.

<u>Example:</u>

```
{
  "my_aggr_1": {
    "avg": 42
  },
  "my_aggr_2": {
    "max": 69
  },
}
```


---

### AggregationDefinition

An aggregate.  It consists of a name, an aggregator function, and the field to use for the function.

---

### AggregationRequest

---

### AggregationRequestV2

---

### AllOfFileId

**プロパティ**:

- `fileId` (unknown, 必須)
- `fileExternalId` (unknown, 任意)
- `fileInstanceId` (unknown, 任意)

---

### AllOfFileIdWithPageRange

**プロパティ**:

- `fileId` (unknown, 必須)
- `fileExternalId` (unknown, 任意)
- `fileInstanceId` (unknown, 任意)
- `pageRange` (unknown, 任意)
- `pageCount` (unknown, 任意)

---

### AllVersionsFlag

If all versions of the entity should be returned. Defaults to false which returns the latest version, attributed to the newest 'createdTime' field

---

### AnnotatedResourceId

The internal ID of the annotated resource.

---

### AnnotatedResourceType

The annotated CDF resource type. Files as well as 3d-Models are supported.

---

### AnnotationData

The annotation information. The format of this object is decided by and validated against the `annotationType`
attribute.

---

### AnnotationId

Server-generated identifier for the annotation

---

### AnnotationType

The type of the annotation. This uniquely decides what the structure of the `data` block will be.

---

### Annotations.AssetRef

A reference to an asset. Either the internal ID or the external ID must be provided (exactly one).

**プロパティ**:

- `id` (integer, 任意)
  - The internal ID of the referenced resource
- `externalId` (string, 任意)
  - The external ID of the referenced resource

---

### Annotations.AttributesMixin

Mixin that can be used to add attributes to a thing

**プロパティ**:

- `attributes` (object, 任意)
  - Additional attributes data for a compound.

---

### Annotations.Boolean

The boolean value of something

**プロパティ**:

- `description` (string, 任意)
  - The description of a primitive
- `type` (string, 必須)
- `value` (boolean, 必須)
  - The boolean value

---

### Annotations.BoundingBox

A plain rectangle

**プロパティ**:

- `confidence` (number, 任意)
  - The confidence score for the primitive. It should be between 0 and 1.
- `xMin` (number, 必須)
  - Minimum abscissa of the bounding box (left edge). Must be strictly less than x_max.
- `xMax` (number, 必須)
  - Maximum abscissa of the bounding box (right edge). Must be strictly more than x_min.
- `yMin` (number, 必須)
  - Minimum ordinate of the bounding box (bottom edge). Must be strictly less than y_max.
- `yMax` (number, 必須)
  - Maximum ordinate of the bounding box (top edge). Must be strictly more than y_min.

---

### Annotations.BoundingVolume

A bounding volume represents a region in a point cloud

**プロパティ**:

- `label` (string, 任意)
  - The label describing what type of object it is
- `confidence` (number, 任意)
  - The confidence score for the primitive. It should be between 0 and 1.
- `instanceRef` (unknown, 任意)
  - The Data Modeling Instance this annotation is pointing to
- `assetRef` (unknown, 任意)
  - The asset this annotation is pointing to
- `region` (array, 必須)
  - The region of the annotation defined by a list of geometry primitives (cylinder and box).

---

### Annotations.Box

A box in 3D space, defined by a 4x4 row-major homogeneous transformation matrix that rotates,
translates and scales a box centered at the origin to its location and orientation in 3D space.
The box that is transformed is the axis-aligned cube spanning the volume between
the points (-1, -1, -1) and (1, 1, 1).

**プロパティ**:

- `label` (string, 任意)
  - The label describing what type of object it is
- `confidence` (number, 任意)
  - The confidence score for the primitive. It should be between 0 and 1.
- `matrix` (array, 必須)
  - The homogeneous transformation matrix

---

### Annotations.Classification

Models an image classification represented by a label, and optionally a
confidence value.

**プロパティ**:

- `label` (string, 必須)
  - The label describing what type of object it is
- `confidence` (number, 任意)
  - The confidence score for the primitive. It should be between 0 and 1.

---

### Annotations.ConfidenceMixin

Mixin that can be used to add confidence score to a thing

**プロパティ**:

- `confidence` (number, 任意)
  - The confidence score for the primitive. It should be between 0 and 1.

---

### Annotations.Cylinder

A cylinder in 3D space, defined by the centers of the two end surfaces and the radius.

**プロパティ**:

- `label` (string, 任意)
  - The label describing what type of object it is
- `confidence` (number, 任意)
  - The confidence score for the primitive. It should be between 0 and 1.
- `centerA` (array, 必須)
  - The center of the first cap.
- `centerB` (array, 必須)
  - The center of the second cap.
- `radius` (number, 必須)
  - The radius of the cylinder.

---

### Annotations.DescriptionMixin

Mixin that can be used to add a description to a thing

**プロパティ**:

- `description` (string, 任意)
  - The description of a primitive

---

### Annotations.Detection

Represents a detection of a field value in a form.
A field is identified by a field_name, optionally component_name and component_type if the field belongs to a subcomponent.
The bounding_box indicates the position of the detection. The content of the field is given by the value, and optionally
an unnormalized_value and the unit.

**プロパティ**:

- `confidence` (number, 任意)
  - The confidence score for the primitive. It should be between 0 and 1.
- `pageNumber` (integer, 任意)
  - The number of the page on which this annotation is located. The first page has number 1.
- `boundingBox` (unknown, 必須)
  - Bounding box of the detection area
- `componentType` (string, 任意)
  - Type of subcomponent that the detection belongs to
- `componentName` (string, 任意)
  - Name of subcomponent that the detection belongs to
- `fieldName` (string, 必須)
  - Name of field that has been detected
- `value` (string, 必須)
  - The value that has been detected
- `valueUnnormalized` (string, 任意)
  - The value that has been detected, before normalization. Optional.
- `unit` (string, 任意)
  - The unit of the value field. Optional.

---

### Annotations.ExtractedText

Represents text extracted from a document. Annotations of this type are low-level and not specific to any domain.

**プロパティ**:

- `pageNumber` (integer, 任意)
  - The number of the page on which this annotation is located. The first page has number 1.
- `textRegion` (unknown, 必須)
  - The location of the extracted text
- `extractedText` (string, 必須)
  - The extracted text

---

### Annotations.FileLink

Models a link to a CDF File referenced in an engineering diagram

**プロパティ**:

- `description` (string, 任意)
  - The description of a primitive
- `pageNumber` (integer, 任意)
  - The number of the page on which this annotation is located. The first page has number 1.
- `fileRef` (unknown, 必須)
  - The file this annotation is pointing to
- `symbolRegion` (unknown, 任意)
  - The location of the symbol representing the file
- `textRegion` (unknown, 必須)
  - The location of the text mentioning the file
- `text` (string, 任意)
  - The extracted text
- `symbol` (string, 任意)
  - The symbol found in the file

---

### Annotations.FileRef

A reference to a file. Either the internal ID or the external ID must be provided (exactly one).

**プロパティ**:

- `id` (integer, 任意)
  - The internal ID of the referenced resource
- `externalId` (string, 任意)
  - The external ID of the referenced resource

---

### Annotations.GeometryMixin

A mixin that can be used to add a geometry to a thing.
A is geometry represented by exactly *one of* ` bounding_box`, `polygon` and
`polyline` which, respectively, represents a BoundingBox, Polygon and
PolyLine.

**プロパティ**:

- `boundingBox` (unknown, 任意)
- `polygon` (unknown, 任意)
- `polyline` (unknown, 任意)

---

### Annotations.InstanceRef

A reference to an DMS instance,
defined by the instance itself and the sources (views)
that define how the data should be interpreted

**プロパティ**:

- `sources` (array, 必須)
  - References to views
- `instanceType` (string, 必須)
  - The type of instance, an edge or a node.
- `externalId` (string, 必須)
  - External id of the instance
- `space` (string, 必須)
  - Id of the space that the instance belongs to

---

### Annotations.IsoPlanAnnotation

Model a custom link in a engineering diagram where it capture details from the texts and linked to assets when necessary

**プロパティ**:

- `pageNumber` (integer, 任意)
  - The number of the page on which this annotation is located. The first page has number 1.
- `fileRef` (unknown, 任意)
  - The asset this annotation is pointing at. Store the id of the file assets to generate downloadable URL.
- `text` (string, 任意)
  - The pattern identified by the detection API results.
- `textRegion` (unknown, 任意)
  - The location of the hotspot represented with a bounding box.
- `indirectRelation` (string, 任意)
  - Relation connecting this hotspot to a tag in case the hotspot has no tag. E.g. 'second valve upstreams of'. This references the 'indirectExternalId'.
- `indirectExternalId` (unknown, 任意)
  - The indirectExternalId is the external id of the equipment used to identify the hotspot indirectly. Exa. this is the <indirectRelation> of <indirectExternalId>.
- `lineExternalId` (unknown, 任意)
  - The id of the Pipe that the hotspot belongs to.
- `detail` (string, 任意)
  - Detail describing the equipment.
- `subDetail` (string, 任意)
  - Use to save the fluid code of pipes Exa. LO for Lube oil and etc.
- `sourceType` (string, 任意)
  - Use to identify whether the annotation is user created one or ditected via pipeline.
- `linkedResourceInternalId` (integer, 任意)
  - Stores Functional Locations' (FLOC) ID linked to the annotation.
- `linkedResourceExternalId` (string, 任意)
  - Stores Functional Locations (FLOC) external ID linked to the annotation.
- `linkedResourceType` (string, 任意)
  - Stores Functional Location (FLOC) type the annotation linked to.
- `type` (string, 任意)
  - Type of equipment, valve, pump etc.
- `relativePosition` (string, 任意)
  - Indicate the relative position of an annotation.
- `revision` (string, 任意)
  - Keeps track of the modification to an annotation.
- `sizeAndClass` (unknown, 任意)
  - Stores the dimensions of a valve or spade.
- `oldAnnotationId` (string, 任意)
  - Keep track of data link with old annotations.
- `vertices` (unknown, 任意)
  - Stores the (x,y) coordinate pairs of a line or polyline.

---

### Annotations.Junction

Models a junction between lines in an engineering diagram

**プロパティ**:

- `pageNumber` (integer, 任意)
  - The number of the page on which this annotation is located. The first page has number 1.
- `position` (unknown, 必須)
  - The point representing the junction

---

### Annotations.Keypoint

A point attached with additional information such as a confidence value and
various attribute(s).

**プロパティ**:

- `attributes` (object, 任意)
  - Additional attributes data for a compound.
- `confidence` (number, 任意)
  - The confidence score for the primitive. It should be between 0 and 1.
- `point` (unknown, 必須)
  - The position of the keypoint

---

### Annotations.KeypointCollection

Models a collection of keypoints represented by a label, a dictionary of
keypoints (mapping from a (unique) label name to a keypoint), and
optionally a confidence value and an attributes dictionary.

**プロパティ**:

- `attributes` (object, 任意)
  - Additional attributes data for a compound.
- `label` (string, 必須)
  - The label describing what type of object it is
- `confidence` (number, 任意)
  - The confidence score for the primitive. It should be between 0 and 1.
- `keypoints` (object, 必須)
  - The detected keypoints

---

### Annotations.Line

Models a line in an engineering diagram

**プロパティ**:

- `label` (string, 必須)
  - The label describing what type of object it is
- `pageNumber` (integer, 任意)
  - The number of the page on which this annotation is located. The first page has number 1.
- `polyline` (unknown, 必須)
  - The polyline representing the line

---

### Annotations.Numerical

The numerical value of something

**プロパティ**:

- `description` (string, 任意)
  - The description of a primitive
- `type` (string, 必須)
- `value` (unknown, 必須)
  - The numerical value

---

### Annotations.ObjectDetection

Models an image object detection represented by a label, a geometry, and
optionally a confidence value.

**プロパティ**:

- `attributes` (object, 任意)
  - Additional attributes data for a compound.
- `boundingBox` (unknown, 任意)
- `polygon` (unknown, 任意)
- `polyline` (unknown, 任意)
- `label` (string, 必須)
  - The label describing what type of object it is
- `confidence` (number, 任意)
  - The confidence score for the primitive. It should be between 0 and 1.

---

### Annotations.OptionalLabelMixin

Mixin that can be used to add an *optional* label string to a thing

**プロパティ**:

- `label` (string, 任意)
  - The label describing what type of object it is

---

### Annotations.PageInformationMixin

Mixin that can be used to add page number information

**プロパティ**:

- `pageNumber` (integer, 任意)
  - The number of the page on which this annotation is located. The first page has number 1.

---

### Annotations.Point

Point in a 2D-Cartesian coordinate system with origin at the top-left corner of the page

**プロパティ**:

- `confidence` (number, 任意)
  - The confidence score for the primitive. It should be between 0 and 1.
- `x` (number, 必須)
  - The abscissa of the point in a coordinate system with origin at the top-left corner of the page. Normalized in (0,1).
- `y` (number, 必須)
  - The ordinate of the point in a coordinate system with origin at the top-left corner of the page. Normalized in (0,1).

---

### Annotations.PolyLine

A polygonal chain consisting of _n_ vertices

**プロパティ**:

- `confidence` (number, 任意)
  - The confidence score for the primitive. It should be between 0 and 1.
- `vertices` (array, 必須)

---

### Annotations.Polygon

A _closed_ polygon represented by _n_ vertices. In other words, we assume
that the first and last vertex are connected.

**プロパティ**:

- `confidence` (number, 任意)
  - The confidence score for the primitive. It should be between 0 and 1.
- `vertices` (array, 必須)

---

### Annotations.RequiredLabelMixin

Mixin that can be used to add a *required* label string to a thing

**プロパティ**:

- `label` (string, 必須)
  - The label describing what type of object it is

---

### Annotations.SizeAndClassType

Store the dimension, units and class of a given annotation

**プロパティ**:

- `size` (number, 任意)
  - The size of the valve or spade
- `unit` (string, 任意)
  - The units of the size (mm/inches)
- `classType` (string, 任意)
  - The class type of the valve or spade

---

### Annotations.TextRegion

Models an extracted text region in an image

**プロパティ**:

- `confidence` (number, 任意)
  - The confidence score for the primitive. It should be between 0 and 1.
- `text` (string, 必須)
  - The extracted text
- `textRegion` (unknown, 必須)
  - The location of the extracted text

---

### Annotations.UnhandledSymbolObject

Models an extracted symbol region in an engineering diagram

**プロパティ**:

- `description` (string, 任意)
  - The description of a primitive
- `pageNumber` (integer, 任意)
  - The number of the page on which this annotation is located. The first page has number 1.
- `symbolRegion` (unknown, 必須)
  - The location of the symbol
- `symbol` (string, 必須)
  - The symbol found in the file

---

### Annotations.UnhandledTextObject

Models an extracted text region in an engineering diagram

**プロパティ**:

- `description` (string, 任意)
  - The description of a primitive
- `pageNumber` (integer, 任意)
  - The number of the page on which this annotation is located. The first page has number 1.
- `textRegion` (unknown, 必須)
  - The location of the text
- `text` (string, 必須)
  - The extracted text

---

### Annotations.View

Defines a DMS source (e.g. view)

**プロパティ**:

- `type` (string, 必須)
- `space` (string, 必須)
  - Id of the space that the view belongs to
- `externalId` (string, 必須)
  - External id of the view
- `version` (string, 必須)
  - Version of the view

---

### Annotations.cogmono__annotation_types__diagrams__AssetLink

Models a link to a CDF Asset referenced in an engineering diagram

**プロパティ**:

- `description` (string, 任意)
  - The description of a primitive
- `pageNumber` (integer, 任意)
  - The number of the page on which this annotation is located. The first page has number 1.
- `assetRef` (unknown, 必須)
  - The asset this annotation is pointing to
- `symbolRegion` (unknown, 任意)
  - The location of the symbol representing the asset
- `textRegion` (unknown, 必須)
  - The location of the text mentioning the asset
- `text` (string, 任意)
  - The extracted text
- `symbol` (string, 任意)
  - The symbol representing the asset

---

### Annotations.cogmono__annotation_types__diagrams__InstanceLink

Models a link to an FDM instance referenced in an engineering diagram

**プロパティ**:

- `description` (string, 任意)
  - The description of a primitive
- `pageNumber` (integer, 任意)
  - The number of the page on which this annotation is located. The first page has number 1.
- `instanceRef` (unknown, 必須)
  - The FDM instance this annotation is pointing to
- `symbolRegion` (unknown, 任意)
  - Optional location of a symbol
- `symbol` (string, 任意)
  - The symbol found in the file
- `text` (string, 必須)
  - The extracted text
- `textRegion` (unknown, 必須)
  - The location of the text mentioning the file

---

### Annotations.cogmono__annotation_types__images__AssetLink

Models a link to a CDF Asset referenced in an image

**プロパティ**:

- `confidence` (number, 任意)
  - The confidence score for the primitive. It should be between 0 and 1.
- `assetRef` (unknown, 必須)
  - The asset this annotation is pointing to
- `text` (string, 必須)
  - The extracted text
- `textRegion` (unknown, 必須)
  - The location of the text mentioning the asset
- `objectRegion` (unknown, 任意)
  - 
    A geometry represented by exactly *one of* ` bounding_box`, `polygon` and
    `polyline` which, respectively, represents a BoundingBox, Polygon and
    PolyLine.
    

---

### Annotations.cogmono__annotation_types__images__InstanceLink

Models a link to an Data Modeling Instance referenced in an image

**プロパティ**:

- `confidence` (number, 任意)
  - The confidence score for the primitive. It should be between 0 and 1.
- `instanceRef` (unknown, 必須)
  - The Data Modeling Instance this annotation is pointing to
- `text` (string, 必須)
  - The extracted text
- `textRegion` (unknown, 必須)
  - The location of the text mentioning the Data Modeling Instance
- `objectRegion` (unknown, 任意)
  - 
    A geometry represented by exactly *one of* ` bounding_box`, `polygon` and
    `polyline` which, respectively, represents a BoundingBox, Polygon and
    PolyLine.
    

---

### Annotations.cogmono__annotation_types__primitives__geometry2d__Geometry

A geometry represented by exactly *one of* ` bounding_box`, `polygon` and
`polyline` which, respectively, represents a BoundingBox, Polygon and
PolyLine.

**プロパティ**:

- `boundingBox` (unknown, 任意)
- `polygon` (unknown, 任意)
- `polyline` (unknown, 任意)

---

### Annotations.cogmono__annotation_types__primitives__geometry3d__Geometry

A 3D geometry model represented by exactly *one of* `cylinder` and `box`.

**プロパティ**:

- `cylinder` (unknown, 任意)
- `box` (unknown, 任意)

---

### AnnotationsV2CreateSchema

---

### AnnotationsV2CursoredListResponseSchema

A cursored list of existing annotations

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)

---

### AnnotationsV2FilterSchema

A filter to apply on annotations

**プロパティ**:

- `annotatedResourceType` (unknown, 必須)
- `annotatedResourceIds` (array, 必須)
- `annotationType` (unknown, 任意)
- `creatingApp` (unknown, 任意)
- `creatingAppVersion` (unknown, 任意)
- `creatingUser` (unknown, 任意)
- `status` (unknown, 任意)
- `data` (object, 任意)
  - Filter annotations by their data keys and values (case-sensitive). Concretely, by providing this field, we check for all annotations that contains the data filter as a subset.
If `annotationType` is not specified, the data filter will applied across all annotation types.

---

### AnnotationsV2ListResponseSchema

**プロパティ**:

- `items` (array, 必須)
  - A list of annotations

---

### AnnotationsV2ReferenceSchema

A reference to an existing annotation

**プロパティ**:

- `id` (unknown, 必須)

---

### AnnotationsV2ResponseSchema

**プロパティ**:

- `id` (unknown, 必須)
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)
- `annotatedResourceType` (unknown, 必須)
- `annotatedResourceId` (unknown, 必須)
- `annotationType` (unknown, 必須)
- `creatingApp` (unknown, 必須)
- `creatingAppVersion` (unknown, 必須)
- `creatingUser` (unknown, 必須)
- `data` (unknown, 必須)
- `status` (unknown, 必須)

---

### AnnotationsV2ReverseLookupFilterSchema

A filter for reverse lookups

**プロパティ**:

- `annotatedResourceType` (unknown, 必須)
- `annotationType` (unknown, 任意)
- `creatingApp` (unknown, 任意)
- `creatingAppVersion` (unknown, 任意)
- `creatingUser` (unknown, 任意)
- `status` (unknown, 任意)
- `data` (object, 任意)
  - Filter annotations by their data keys and values (case-sensitive). Concretely, by providing this field, we check for all annotations that contains the data filter as a subset.
If `annotationType` is not specified, the data filter will applied across all annotation types.

---

### AnnotationsV2ReverseLookupResponseSchema

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
- `annotatedResourceType` (unknown, 必須)

---

### AnnotationsV2SuggestSchema

**プロパティ**:

- `annotatedResourceType` (unknown, 必須)
- `annotatedResourceId` (unknown, 必須)
- `annotationType` (unknown, 必須)
- `creatingApp` (unknown, 必須)
- `creatingAppVersion` (unknown, 必須)
- `creatingUser` (unknown, 必須)
- `data` (unknown, 必須)

---

### AnnotationsV2UpdateDataSchema

Each fields represent a "delta" that will be applied onto the corresponding field of the original
annotation.


### Updating Annotation Type or Data
Ensure that the new annotation data conforms to the new annotation type, otherwise the call will fail.

**プロパティ**:

- `annotationType` (object, 任意)
- `data` (object, 任意)
- `status` (object, 任意)

---

### AnnotationsV2UpdateItemSchema

An update object to apply to an annotation

**プロパティ**:

- `id` (unknown, 必須)
- `update` (unknown, 必須)

---

### Answer

**プロパティ**:

- `content` (array, 必須)
  - The content of an answer consists of one or more parts. Each part can have a different set of document locations connected to it.

---

### Application

The application name or identifier. This is neither checked nor enforced.

---

### ArrayPatchLong

Change that will be applied to the array.

---

### ArrayPatchLongAddOrRemove

**プロパティ**:

- `add` (array, 任意)
- `remove` (array, 任意)

---

### ArrayPatchLongSet

**プロパティ**:

- `set` (array, 必須)

---

### ArrayPatchString

Change that will be applied to the array.

---

### ArrayPatchStringAddOrRemove

**プロパティ**:

- `add` (array, 任意)
- `remove` (array, 任意)

---

### ArrayPatchStringSet

**プロパティ**:

- `set` (array, 必須)

---

### Artifact

An artifact for an extractor release.

**プロパティ**:

- `name` (string, 必須)
  - Filename of the artifact.
- `displayName` (string, 任意)
  - Human readable name of the artifact.
- `link` (string, 必須)
  - Link to obtain a temporary download link for the artifact.
- `platform` (string, 必須)
  - Platform the extractor runs on. One of "windows", "linux", "macos", "docs", or "all".

---

### Asset

---

### AssetAdvancedFilter

**プロパティ**:

- `advancedFilter` (object, 任意)
  - A filter DSL (Domain Specific Language) to define advanced filter queries.

See more information about filtering DSL [here](https://docs.cognite.com/dev/concepts/resource_filtering_dsl/ "filtering DSL").

Supported properties:

  | Property                        | Type                                    |
  |---------------------------------|-----------------------------------------|
  | `["labels"]`                    | array of [string]                       |
  | `["createdTime"]`               | number                                  |
  | `["dataSetId"]`                 | number                                  |
  | `["id"]`                        | number                                  |
  | `["lastUpdatedTime"]`           | number                                  |
  | `["parentId"]`                  | number                                  |
  | `["rootId"]`                    | number                                  |
  | `["description"]`               | string                                  |
  | `["externalId"]`                | string                                  |
  | `["metadata"]`                  | string                                  |
  | `["metadata", "someCustomKey"]` | string                                  |
  | `["name"]`                      | string                                  |
  | `["source"]`                    | string                                  |

  Note: Filtering on the `["metadata"]` property has the following logic:
  If a value of any metadata keys in an asset matches the filter, the asset matches the filter.

---

### AssetAggregateBoolFilter

A query that matches items matching boolean combinations of other queries.
It's built using one or more boolean clauses of the following types: `and`, `or`, or `not`.


---

### AssetAggregateFilter

A filter DSL (Domain Specific Language) to define aggregate filter queries.

See more information about filtering DSL [here](https://docs.cognite.com/dev/concepts/resource_filtering_dsl/ "filtering DSL").


---

### AssetAggregateKeys

**プロパティ**:

- `keys` (array, 任意)
  - For `metadataValues`, `aggregateType` sets the metadata key(s) to apply the aggregation on. Currently supports exactly one key per request.


---

### AssetAggregateLeafFilter

Aggregate leaf filter.

---

### AssetAggregateProperties

**プロパティ**:

- `properties` (array, 必須)
  - The property name(s) to apply the aggregation on. Currently limited to one property per request.

---

### AssetAggregateRequest

Aggregation request of assets. Filters behave the same way as for the `filter` endpoint. Default aggregation is `count`.

---

### AssetBoolFilter

A query that matches items matching boolean combinations of other queries.
It is built using one or more boolean clauses of the following types: `and`, `or`, or `not`.

---

### AssetCardinalityPropertiesAggregate

---

### AssetCardinalityValuesAggregate

---

### AssetCentricDataSource

**プロパティ**:

- `type` (string, 必須)
  - The type of the destination resource.

---

### AssetChange

---

### AssetChangeByExternalId

---

### AssetChangeById

---

### AssetCountAggregate

---

### AssetDataIds

---

### AssetDescription

The description of the asset.

---

### AssetExternalId

**プロパティ**:

- `externalId` (unknown, 必須)

---

### AssetExternalIds

Only include files that reference these specific asset external IDs.

---

### AssetFilter

**プロパティ**:

- `filter` (object, 任意)
  - Filter on assets with strict matching.

---

### AssetIdEither

---

### AssetIdentifier

**プロパティ**:

- `id` (unknown, 必須)

---

### AssetIds

Only include files that reference these specific asset IDs.

---

### AssetInstanceId

**プロパティ**:

- `assetInstanceId` (unknown, 必須)

---

### AssetInstanceIdMappingsFilterRequest

---

### AssetInternalId

**プロパティ**:

- `id` (unknown, 必須)

---

### AssetLeafFilter

Leaf filter.

---

### AssetLimit

**プロパティ**:

- `limit` (integer, 任意)
  - Limits the number of results to return.

---

### AssetListScope

---

### AssetMapping3D

**プロパティ**:

- `nodeId` (integer, 必須)
  - The ID of the node.
- `assetId` (integer, 任意)
  - The ID of the associated asset (Cognite's Assets API).
- `assetInstanceId` (unknown, 任意)
- `treeIndex` (unknown, 任意)
- `subtreeSize` (unknown, 任意)

---

### AssetMapping3DAssetFilter

**プロパティ**:

- `assetIds` (array, 必須)

---

### AssetMapping3DAssetInstanceIdFilter

**プロパティ**:

- `assetInstanceIds` (array, 必須)

---

### AssetMapping3DFilter

**プロパティ**:

- `getDmsInstances` (boolean, 任意)
  - If true, the response will include only the mappings with assetInstanceId values, for DMS based assets.
- `filter` (unknown, 任意)
- `limit` (integer, 任意)
  - Limits the number of results to return.

---

### AssetMapping3DFilterRequest

---

### AssetMapping3DList

**プロパティ**:

- `items` (array, 必須)

---

### AssetMapping3DNodeFilter

**プロパティ**:

- `nodeIds` (array, 必須)

---

### AssetMapping3DTreeIndexFilter

**プロパティ**:

- `treeIndexes` (array, 必須)

---

### AssetMapping3DWithCursorResponse

---

### AssetMetadata

Custom, application specific metadata. String key -> String value. Limits: Maximum length of key is 128 bytes, value 10240 bytes, up to 256 key-value pairs, of total size at most 10240.

---

### AssetMetadataKeysAggregate

---

### AssetMetadataValuesAggregate

---

### AssetName

The name of the asset.

---

### AssetParentExternalId

The external ID of the parent. This will be resolved to an internal ID and stored as `parentId`.

---

### AssetPatch

Changes applied to asset

**プロパティ**:

- `update` (object, 必須)

---

### AssetQuery

Whitespace-separated terms to search for in assets. Does a best-effort fuzzy search in relevant fields (currently name and description) for variations of any of the search terms, and orders results by relevance. Uses a different search algorithm than the name and description parameters, and will generally give much better results. Matching and ordering is not guaranteed to be stable over time, and the fields being searched may be extended.

---

### AssetSearch

**プロパティ**:

- `search` (object, 任意)
  - Fulltext search for assets. Primarily meant for for human-centric use-cases, not for programs. The query parameter uses a different search algorithm than the deprecated name and description parameters, and will generally give much better results.

---

### AssetSearchFilter

Search request with filter capabilities.

---

### AssetSort

**プロパティ**:

- `sort` (array, 任意)
  - Sort by array of selected properties.


---

### AssetSortProperty

**プロパティ**:

- `property` (array, 必須)
  - Property to sort on.
Sorting can be done on the following properties:
  | Property                        |
  |---------------------------------|
  | `["createdTime"]`               |
  | `["dataSetId"]`                 |
  | `["description"]`               |
  | `["externalId"]`                |
  | `["lastUpdatedTime"]`           |
  | `["metadata", "someCustomKey"]` |
  | `["name"]`                      |
  | `["source"]`                    |
  | `["_score_"]`                   |
- `order` (string, 任意)
  - The `order` attribute is optional and defaults to `desc` for `_score_` and `asc` for all other properties.
- `nulls` (string, 任意)
  - The `nulls` attribute is optional and defaults to `auto`.
`auto` is translated to `last` for the `asc` order and to `first` for the `desc` order by the service.

---

### AssetSource

The source of the asset.

---

### AssetSubtreeIds

Only include files that have a related asset in a subtree rooted at any of these assetIds (including the roots given). If the total size of the given subtrees exceeds 100,000 assets, an error will be returned.

---

### AssetTagDetection

Detect external ID or name of assets (from your CDF projects) in images. Usage of this feature requires `['assetsAcl:READ']` capability.

---

### AssetTagDetectionParameters

Parameters for asset tag detection.

**プロパティ**:

- `threshold` (unknown, 任意)
- `partialMatch` (boolean, 任意)
  - Allow partial (fuzzy) matching of detected external IDs in the file.
Will only match when it is possible to do so unambiguously.

- `assetSubtreeIds` (array, 任意)
  - Search for external ID or name of assets that are in a subtree rooted at one of
the assetSubtreeIds (including the roots given).


---

### AssetUniquePropertiesAggregate

---

### AssetUniqueValuesAggregate

---

### AssetWithPropertyCountAggregate

---

### Auth0Idp

---

### AuthCertificate

Authentication certificate (if configured) used to authenticate to source.

**プロパティ**:

- `key` (string, 必須)
  - The key for the certificate
- `keyPassword` (string, 任意)
  - The password for the certificate key
- `type` (unknown, 必須)
- `certificate` (string, 必須)
  - Base 64 encoded der certificate, or a pem certificate with headers.

---

### Authentication

**プロパティ**:

- `nonce` (string, 必須)
  - The nonce of a sessions token, which can be obtained using client credentials authentication flow. The nonce's session must be a root session, i.e. it must not be a child of another session. See [Sessions API](https://api-docs.cognite.com/20230101/tag/Sessions) documentation for more information.

---

### AvgAggregate

**プロパティ**:

- `avg` (object, 必須)
  - Calculates the average from the data stored by the specified property. This aggregation uses an average mean calculation, and not an integral mean.

---

### AvgAggregateFunctionV3

Calculates the average from the data stored by the specified property. This aggregation uses an average mean calculation, and not an integral mean.

**プロパティ**:

- `avg` (object, 必須)

---

### AvgAggregateIla

**プロパティ**:

- `avg` (object, 必須)
  - Calculates the average from the data stored by the specified property. This
aggregation uses an average mean calculation, and not an integral mean.

<u>Example:</u>

```
{
  "my_player_age_avg": {
    "avg": { "property": [ "football", "game_statistics", "player_age" ] }
  }
}
```


---

### AvgAggregateResult

**プロパティ**:

- `avg` (number, 必須)
  - The average value from the data stored by the specified in the request property.

---

### AvgAggregateResultIla

**プロパティ**:

- `avg` (number, 必須)
  - The average value from the data stored by the specified in the request property.


---

### AzureAdIdp

---

### BackfillSort

Optionally sort synced instances during the backfill phase of a two-phase sync. The properties sorted on must have a cursorable index on them. Can only be specified with `mode: twoPhase`.

---

### BasePrincipalDto

A principal

**プロパティ**:

- `id` (unknown, 必須)
- `name` (unknown, 必須)
- `pictureUrl` (unknown, 必須)

---

### BasePropertyIla

---

### BasicAuthentication

**プロパティ**:

- `type` (string, 必須)
  - Authentication type.
- `username` (string, 必須)
  - User name to use for basic authentication

---

### BasicGetSequenceColumnInfo

Column information returned on data requests.

**プロパティ**:

- `externalId` (string, 任意)
  - User provided column identifier (unique for a given sequence).
- `name` (string, 任意)
  - Human readable name of the column.
- `valueType` (unknown, 任意)

---

### BatchDownloadRequestFilter

**プロパティ**:

- `surveyId` (integer, 任意)
  - A survey's internal ID. All seismic objects included will be part of this survey.
- `partitionId` (integer, 任意)
  - A partition's internal ID. All seismic objects included will be part of this partition.
- `metadataFilter` (object, 任意)
  - All seismic objects included will have metadata that is a superset of the specified metadata filter.

---

### BatchDownloadRequestSeismic

**プロパティ**:

- `items` (array, 必須)
  - The list of seismic objects to include in the ZIP archive, specified by internal id.

---

### BatchJobStatus

The status of the job.

---

### BatchRefreshSessionsRequest

**プロパティ**:

- `items` (array, 任意)

---

### BatchRefreshSessionsResponse

A list of refreshed sessions. If the session could not be refreshed, it will not appear in this list.

**プロパティ**:

- `items` (array, 任意)
- `failed` (array, 任意)

---

### BatchUpdateFailure



**プロパティ**:

- `id` (number, 必須)
  - ID of the session.
- `error` (string, 必須)
  - User-friendly error description.
- `code` (number, 必須)
  - HTTP-code corresponding to this error.

---

### BindSessionRequest

Bind a newly created session.

**プロパティ**:

- `nonce` (string, 必須)
  - Nonce returned by session creation endpoint
- `maxExpirationSeconds` (integer, 任意)
  - Maximum number of seconds that the session may stay valid for, from the time it's bound. Even if the session is refreshed, it may not stay valid beyond this.


---

### BodyPaginationConfig

**プロパティ**:

- `type` (string, 必須)
- `value` (string, 必須)
  - Expression yielding next message body. Note that body-based pagination is not allowed to be used if `method` is not set to `post`.


---

### BoolFilterIla

Build a new query by combining other queries, using boolean operators. We support the `and`, `or`, and
`not` boolean operators.


---

### BoundingBox

**プロパティ**:

- `xMin` (unknown, 必須)
- `xMax` (unknown, 必須)
- `yMin` (unknown, 必須)
- `yMax` (unknown, 必須)
- `zMin` (unknown, 必須)
- `zMax` (unknown, 必須)

---

### BoundingBox3D

The bounding box of the subtree with this sector as the root sector. Is null if there are no geometries in the subtree.

**プロパティ**:

- `max` (array, 必須)
- `min` (array, 必須)

---

### BoxPointCloudVolumeV1

**プロパティ**:

- `type` (string, 必須)
- `version` (string, 必須)
- `data` (array, 必須)
  - An array of 16 floats defining the box volume.
Scale and position values are specified in meters and use a right-handed coordinate system with X right, Y forward, Z up.
The array should contain the following values, in the specified order:

```json
[ scale[0],    0,        0,        position[0],
  0,           scale[1], 0,        position[1],
  0,           0,        scale[2], position[2],
  0,           0,        0,        1 ]
```


---

### BtreeIndexCreateDefinition

**プロパティ**:

- `properties` (array, 必須)
  - List of properties to define the index across
- `indexType` (string, 任意)
  - The B-tree index supports the following operations;

 * less than,
 * less than or equal,
 * equality (is equal),
 * larger than or equal, and
 * larger than.


By enabling the index to be cursorable, you can use it to efficiently query with custom sort options, and queries will emit cursors that can be used to paginate through the results. 
- `cursorable` (boolean, 任意)
  - Whether the index can be used for cursor-based pagination
- `bySpace` (boolean, 任意)
  - Whether to make the index space-specific

---

### BtreeIndexDefinition

---

### CACertificate

Custom certificate authority certificate to let the source use a self signed certificate.

**プロパティ**:

- `type` (unknown, 必須)
- `certificate` (string, 必須)
  - Base 64 encoded der certificate, or a pem certificate with headers.

---

### CDFExternalIdReference

An external-id reference to an existing CDF resource type item, such as a time series.

An example could be that for an existing time series stored in the CDF time series resource type with the
external-id 21PT0293, then the value would be set to "21PT029", and the type would be set to "timeseries". There
are no referential integrity guarantees for this, and the client should handle if the time series has been removed
or the client may not have access to it.

Currently, time series, sequence and file references are supported.


**プロパティ**:

- `type` (string, 必須)
- `list` (boolean, 任意)
  - Specifies that the data type is a list of values. The ordering of values is preserved.

- `maxListSize` (integer, 任意)
  - Specifies the maximum number of values in the list

---

### CPURange

The number of CPU cores per function exectuion (i.e. function call).

**プロパティ**:

- `min` (number, 必須)
  - The minimum value you can request when creating a function.
- `max` (number, 必須)
  - The maximum value you can request when creating a function.
- `default` (number, 必須)
  - The default value when creating a function.

---

### CallbackUpdateRequest

---

### CancelExecution

**プロパティ**:

- `reason` (string, 任意)
  - Human-readable reason for the cancellation.

---

### Capabilities

---

### Capability

---

### CdfTaskOutput

**プロパティ**:

- `response` (unknown, 任意)
  - The body of the response. Will be a a JSON object if content-type is application/json, otherwise it will be a string.
- `statusCode` (integer, 任意)
  - The HTTP status code of the response.

---

### CdfTaskParameters

Parameters for the CDF Request task type, which can be used to make a request to any CDF API.

**プロパティ**:

- `cdfRequest` (object, 任意)

---

### CertificateDetails

Excerpt of certificate details

**プロパティ**:

- `thumbprint` (string, 必須)
  - A thumbprint of the certificate provided
- `expiresAt` (integer, 必須)
  - The expiry date of the certificate

---

### CertificateType

Type of certificate in the `certificate` field.

---

### Changelog

Extractor changelog, uses the [keep-a-changelog](https://keepachangelog.com/en/1.1.0/) format

**プロパティ**:

- `added` (array, 任意)
  - Features added to this release.
- `changed` (array, 任意)
  - Changes made to the extractor in this release.
- `deprecated` (array, 任意)
  - Features that have been deprecated, but not yet removed.
- `fixed` (array, 任意)
  - Bugs fixed in this release.
- `removed` (array, 任意)
  - Features that have been removed.
- `security` (array, 任意)
  - Changes that affect security.

---

### ClaimName

**プロパティ**:

- `claimName` (string, 必須)

---

### ClaimNamesArray

---

### Classifier

The classifier used in the model. Only relevant if there are trueMatches/labeled data and a supervised model is fitted.

---

### ClientCredentialsAuthentication

**プロパティ**:

- `type` (string, 必須)
  - Authentication type.
- `clientId` (string, 必須)
  - Client ID for for the service principal used by the extractor
- `tokenUrl` (string, 必須)
  - URL to fetch authentication tokens from
- `scopes` (string, 必須)
  - A space separated list of scopes
- `defaultExpiresIn` (string, 任意)
  - Default value for the expires_in OAuth 2.0 parameter. If the identity provider does not return expires_in in token requests, this parameter must be set or the request will fail.

---

### ClusterName

A CDF cluster name

---

### ClustersListRequestResponse

**プロパティ**:

- `clusters` (array, 必須)

---

### CogniteCapability

---

### CogniteExtId

The external ID provided by the client. Must be unique for the resource type.

**プロパティ**:

- `externalId` (string, 必須)

---

### CogniteExternalId

The external ID provided by the client. Must be unique for the resource type.

---

### CogniteExternalIdPrefix

Filter by this (case-sensitive) prefix for the external ID.

---

### CogniteFormat

Convert from the special cognite format to CDF datapoints.

**プロパティ**:

- `type` (string, 必須)
  - Format type.
- `encoding` (unknown, 任意)
- `compression` (unknown, 任意)
- `prefix` (unknown, 任意)
- `dataModels` (unknown, 任意)

---

### CogniteInstanceId

The ID of an [instance in Cognite Data Models](https://docs.cognite.com/cdf/dm/dm_concepts/dm_spaces_instances#instance).

**プロパティ**:

- `space` (string, 必須)
- `externalId` (string, 必須)

---

### CogniteIntId

A server-generated ID for the object.

**プロパティ**:

- `id` (integer, 必須)

---

### CogniteInternalId

A server-generated ID for the object.

---

### ColumnType

Details of a column in a given schema.

**プロパティ**:

- `name` (string, 必須)
  - Column name.
- `sqlType` (string, 必須)
  - Type of the column in sql format.
- `type` (unknown, 必須)
  - Type of the column in json format.
- `nullable` (boolean, 必須)
  - Values for the column can be null or not.

---

### Column_

Foreign table columns.

---

### CommaPos

Number of digits after comma in a digital gauge.

---

### CommonAggregateFunctions

**プロパティ**:

- `count` (integer, 任意)
  - The number of data points in the aggregate period.
- `countGood` (integer, 任意)
  - The number of data points in the aggregate period that have a _Good_ [status code](<https://developer.cognite.com/dev/concepts/reference/status_codes>). _Uncertain_ does not count, irrespective of `treatUncertainAsBad` parameter.
- `countUncertain` (integer, 任意)
  - The number of data points in the aggregate period that have an _Uncertain_ [status code](<https://developer.cognite.com/dev/concepts/reference/status_codes>).
- `countBad` (integer, 任意)
  - The number of data points in the aggregate period that have a _Bad_ [status code](<https://developer.cognite.com/dev/concepts/reference/status_codes>). _Uncertain_ does not count, irrespective of `treatUncertainAsBad` parameter.
- `durationGood` (integer, 任意)
  - The duration the aggregate is defined and marked as good (regardless of `ignoreBadDataPoints` parameter). Measured in milliseconds. Equivalent to duration that the previous data point is _good_ and in range.
- `durationUncertain` (integer, 任意)
  - The duration the aggregate is defined and marked as uncertain (regardless of `ignoreBadDataPoints` parameter). Measured in milliseconds. Equivalent to duration that the previous data point is _uncertain_ and in range.
- `durationBad` (integer, 任意)
  - The duration the aggregate is defined but marked as bad (regardless of `ignoreBadDataPoints` parameter). Measured in milliseconds. Equivalent to duration that the previous data point is _bad_ and in range.

---

### CommonAggregationRequest

Defines an aggregation request. This will let you group, and aggregate supported data types. The request supports filters, and allows optional search matching.

**プロパティ**:

- `query` (string, 任意)
  - Optional query string.  The API will parse the query string, and use it to match the text properties on elements to use for the aggregate(s).
- `properties` (array, 任意)
  - Optional list (array) of properties you want to apply the query above to. If you do not list any properties, you search through text fields by default.
- `limit` (integer, 任意)
  - Limit the number of results returned. The default limit is currently at 100 items.
- `aggregates` (array, 任意)
- `groupBy` (array, 任意)
  - The selection of fields to group the results by when doing aggregations. You can specify up to 5 items
to group by.

When you do not specify any aggregates, the fields listed in the `groupBy` clause will return the unique
values stored for each field. The property types supported for `groupBy` are `text`, `direct`, `int32`, `int64`, `float32`, `float64`, `boolean`, and `enum`.

- `filter` (unknown, 任意)
- `operator` (unknown, 任意)

---

### CommonAggregationRequestV2

Defines an aggregation request. This will let you aggregate supported data types. The request supports filters, and allows optional search matching.

**プロパティ**:

- `query` (string, 任意)
  - Optional query string. The API will parse the query string, and use it to match the text properties on elements to use for the aggregate(s).
- `properties` (array, 任意)
  - Optional list (array) of properties you want to apply the query above to. If you do not list any properties, you search through text fields by default.
- `aggregates` (unknown, 必須)
- `filter` (unknown, 任意)

---

### CompleteMultiPartUpload

---

### CompressionType

The compression applied to incoming messages. The messages are decompressed before being passed to transformations.
This is usually not relevant for REST, where this is handled automatically, but MQTT, Kafka, and EventHub have no such mechanisms.

---

### ConfigResponse

**プロパティ**:

- `externalId` (string, 必須)
  - External ID of the extraction pipeline this configuration revision belongs to.
- `config` (string, 任意)
  - Configuration revision contents.
- `revision` (integer, 必須)
  - Revision number of this configuration.
- `createdTime` (unknown, 任意)
- `description` (string, 任意)
  - A description of this config revision.

---

### ConfigurationUpdateSet

**プロパティ**:

- `set` (unknown, 任意)

---

### ConfirmedMatches

List of objects of pairs of sourceId or sourceExternalId and targetId or targetExternalId, that corresponds to entities in source and target respectively, that indicates a confirmed match by the user. A source and target pair in this list will override results from a model or rules and will therefore always be returned as a match. The matches are also used as training data when the entity matcher model is created.

---

### ConflictMode

Behavior when the data already exists.`upsert` - Create or Update,`abort` - Create and fail when already exists,`update` - update and fail if it does not exist,`delete` - delete the matched rows.

---

### ConnectionCheckContent

Cognite API error.

**プロパティ**:

- `status` (string, 必須)
  - Connectivity status between the writeback API and the SAP destination endpoint. `success` means that the CDF writeback API has successfully connected to the SAP endpoint destination.
- `detail` (string, 任意)
  - Description of the connection check

---

### ConnectionCreateDraft

**プロパティ**:

- `diagramId` (unknown, 必須)
  - The external ID of a diagram the connection belongs to
- `endNodeId` (unknown, 必須)
  - External ID of a diagram entity to connect to another diagram entity
- `isUserDetected` (boolean, 必須)
  - Whether or not the connection was created by a user
- `startNodeId` (unknown, 必須)
  - The external ID of a diagram entity to connect to another diagram entity

---

### ConnectionDefinition

---

### ConnectionDefinitionRead

---

### ConnectionDto

Connection between two entities in a diagram. The connected entities can either be in the same or different diagrams.

**プロパティ**:

- `createdTime` (unknown, 必須)
- `diagramId` (unknown, 必須)
  - The external ID of a diagram the connection belongs to
- `endNodeId` (unknown, 必須)
  - The external ID of a diagram entity with a connection to the other diagram entity
- `externalId` (unknown, 必須)
  - The external ID of the connection
- `isUserDetected` (boolean, 必須)
  - Checks whether the connection was detected by a user or not. Returns a boolean value.
- `lastUpdatedTime` (unknown, 必須)
- `startNodeId` (unknown, 必須)
  - The external ID of a diagram entity with a connection to the other diagram entity

---

### ConstraintCreateDefinition

Defines a constraint across the properties you include.


You can use constraints to enforce that;
 * Certain properties must be present,
 * A value must be unique across a single or several properties.

A 'uniqueness' constraint can only apply to properties that are in the same container. Up to 10 'uniqueness' constraints can be added per container. 


A 'requires' constraint can reference other containers. As a result, the properties in those other containers must then also be set. Up to 25 'requires' constraints can be added per container. 

---

### ConstraintDefinition

Describes a constraint across the properties included.


Constraints enforce that;
 * Certain properties must be present,
 * A value must be unique across a single or several properties.

A 'uniqueness' constraint can only apply to properties that are in the same container. Up to 10 'uniqueness' constraints can be added per container. 


A 'requires' constraint can reference other containers. As a result, the properties in those other containers must then also be set. Up to 25 'requires' constraints can be added per container. 

---

### ConstraintOrIndexState

---

### ConstraintState

**プロパティ**:

- `state` (unknown, 必須)
  - This field describes the validity of the constraint.

Possible values are:

* `"failed"`: The constraint is violated. This can occur if there exists data violating the constraint when the constraint was created. New data violating the constraint will still be rejected.
* `"current"`: The constraint is satisfied.
* `"pending"`: The constraint validity has not yet been computed.


---

### Contact

**プロパティ**:

- `name` (string, 任意)
  - Contact name
- `email` (string, 任意)
  - Contact email
- `role` (string, 任意)
  - Contact role
- `sendNotification` (boolean, 任意)
  - True, if contact receives email notifications

---

### ContactPerson

A contact person for an organization

**プロパティ**:

- `id` (integer, 必須)
- `email` (string, 必須)
- `name` (string, 任意)
- `phone` (string, 任意)
- `note` (string, 任意)

---

### ContactsUpdate

Updates the resource's assigned contacts. Contacts can be added, removed or replaced (set). 

---

### ContactsUpdateAddRemove

**プロパティ**:

- `add` (array, 任意)
  - Contacts to add
- `remove` (array, 任意)
  - Contacts to remove

---

### ContactsUpdateSet

**プロパティ**:

- `set` (array, 任意)
  - New contacts list

---

### ContainerCommon

Container for properties you can access through views.  Container specifications give details about storage related details. For instance, how to index the data, and what constraints should be present.     You can define a single container to only contain nodes (```node```), only contain edges (```edge```), or to contain both (```all```).

**プロパティ**:

- `space` (unknown, 必須)
  - Id of the space the container belongs to
- `externalId` (unknown, 必須)
  - External-id of the container. The values ```Query```, ```Mutation```, ```Subscription```, ```String```, ```Int32```, ```Int64```, ```Int```, ```Float32```, ```Float64```, ```Float```, ```Timestamp```, ```JSONObject```, ```Date```, ```Numeric```, ```Boolean```, ```PageInfo```, ```File```, ```Sequence``` and ```TimeSeries``` are reserved.
- `name` (string, 任意)
  - Readable name for container meant for use in UIs
- `description` (string, 任意)
  - Description of what the property contains, and how you intend to use it.
- `usedFor` (unknown, 任意)

---

### ContainerCreateCollection

**プロパティ**:

- `items` (array, 必須)
  - List of containers to create/update

---

### ContainerCreateDefinition

---

### ContainerDefinition

---

### ContainerInspectRequest

**プロパティ**:

- `inspectionOperations` (object, 必須)
- `items` (array, 必須)

---

### ContainerInspectResultItem

**プロパティ**:

- `externalId` (unknown, 必須)
  - External ids for the requested items
- `space` (unknown, 必須)
- `inspectionResults` (object, 必須)

---

### ContainerPropertyCreateDefinition

Defines a property of a container.  You can reference this property in views.

---

### ContainerPropertyDefinition

Defines a property of a container.  You can reference this property in views.

---

### ContainerPropertyTypeDefinition

**プロパティ**:

- `type` (unknown, 必須)
  - The type of data you can store in this property. Most properties can also hold a list of values, for example, a list of integers or a list of timestamps. The supported storage types are:


* text (allows user control of collation),
 * boolean,
 * 32-bit, and 64-bit float,
 * 32-bit, and 64-bit integer,
 * timestamp,
 * date,
 * JSON fragment,
 * direct relation to an existing node,
 * time series external-id reference,
 * file external-id reference,
 * sequence external-id reference,
 * enum.


The JSON fragments have to be valid JSON objects. If JSON arrays are to be stored, these must be wrapped in a JSON object. JSON values (such as string, number, boolean) should use their respective primitive form instead. The maximum allowed size for a JSON object is 40960 bytes. For lists of json values, the size of the entire list must be within this limit. The maximum allowed length of a key is 128, while the maximum allowed size of a value is 10240 bytes and you can have up to 256 key-value pairs. The  byte sizes are computed by representing the data as a UTF-8 encoded JSON string.
Direct relations use a space external-id, the external-id for an existing node, and the external-id for the data container. E.g. ```s-spaceExternalId.uniqueNodeExternalId.containerExternalId```
Timestamps must be a string in the format 'YYYY-MM-DDTHH:MM:SS[.millis][Z|time zone]' with optional milliseconds having precision of 1-3 decimal digits and optional timezone with format ±HH:MM, ±HHMM, ±HH or Z, where Z represents UTC, Year must be between 0001 and 9999.
Dates must be a date string in the format 'YYYY-MM-DD'. Year must be between 1 and 9999. 

---

### ContainerReference

Reference to an existing container

**プロパティ**:

- `type` (string, 必須)
- `space` (unknown, 必須)
  - Id of the space hosting (containing) the container
- `externalId` (unknown, 必須)
  - External-id of the container

---

### ContainerReferenceIla

Reference to a container.


---

### ContainerSubObjectIdentifier

**プロパティ**:

- `space` (unknown, 必須)
- `containerExternalId` (unknown, 必須)
  - External id for the container
- `identifier` (string, 必須)

---

### ContainerWithData

Container holding properties.


---

### ContainersWithoutIndexesInvolvedNotice

**プロパティ**:

- `code` (string, 必須)
- `category` (string, 必須)
- `level` (string, 必須)
- `hint` (string, 必須)
- `grade` (string, 任意)
- `resultExpression` (string, 任意)
  - Identifier for the result set expression that the notice applies to.
- `containers` (array, 必須)
  - List of containers that the notice applies to.

---

### ContainsAllFilter

**プロパティ**:

- `containsAll` (object, 必須)
  - Matches items where the property contains all the given values.
This filter can only be applied to multivalued properties.


---

### ContainsAllFilterIla

**プロパティ**:

- `containsAll` (object, 必須)
  - Matches records where the property contains all the given values.
Only apply this filter to multivalued properties.


---

### ContainsAllFilterV3

**プロパティ**:

- `containsAll` (object, 必須)
  - Matches items where the property contains all the given values. Only apply this filter to multivalued properties.

---

### ContainsAny

**プロパティ**:

- `containsAny` (array, 任意)
  - Filter the transformations having any of these tags.

---

### ContainsAnyFilter

**プロパティ**:

- `containsAny` (object, 必須)
  - Matches items where the property contains one or more of the given values.
This filter can only be applied to multivalued properties.


---

### ContainsAnyFilterIla

**プロパティ**:

- `containsAny` (object, 必須)
  - Matches records where the property contains one or more of the given values.
Only apply this filter to multivalued properties.


---

### ContainsAnyFilterV3

**プロパティ**:

- `containsAny` (object, 必須)
  - Matches items where the property contains one or more of the given values. Only apply this filter to multivalued properties.

---

### Content

**プロパティ**:

- `references` (array, 任意)
  - The document locations that this part of the answer is sourced from.
- `text` (string, 必須)
  - A part of the answer from the LLM.

---

### ContextualizeImage360AnnotationBody

**プロパティ**:

- `items` (unknown, 必須)
- `dmsContextualizationConfig` (unknown, 必須)

---

### ContextualizeImage360AnnotationRCBody

**プロパティ**:

- `items` (unknown, 必須)
- `dmsContextualizationConfig` (unknown, 必須)

---

### ContextualizePointCloudVolumeBody

**プロパティ**:

- `items` (unknown, 必須)
- `dmsContextualizationConfig` (unknown, 必須)

---

### ContextualizePointCloudVolumeRCBody

**プロパティ**:

- `items` (unknown, 必須)
- `dmsContextualizationConfig` (unknown, 必須)

---

### CopyLibraryInput

**プロパティ**:

- `name` (string, 必須)
  - The name of the copied library

---

### CorePropertyCreateDefinition

**プロパティ**:

- `immutable` (boolean, 任意)
  - Should updates to this property be rejected after the initial population?
- `nullable` (boolean, 任意)
  - Does this property need to be set to a value, or not?
- `autoIncrement` (boolean, 任意)
  - When set to ```true```, the API will increment the property based on its highest current value (max value).  You can only use this functionality to increment properties of type `int32` or `int64`. If the property has a different data type, the API will return an error.
- `defaultValue` (unknown, 任意)
  - Default value to use when you do not specify a value for the property.  The default value must be of the same type as what you defined for the property itself. 


We do not currently support using default values for array/list types. 
- `description` (string, 任意)
  - Description of the content and suggested use for this property.
- `name` (string, 任意)
  - Readable property name.

---

### CorePropertyDefinition

---

### CountAggregate

**プロパティ**:

- `count` (object, 必須)
  - Counts the number of items. When you specify a property, it returns the number of non-null values for that property.

---

### CountAggregateFunctionV3

Counts the number of items.  When you specify a property, it returns the number of non-null values for that property.

**プロパティ**:

- `count` (object, 必須)

---

### CountAggregateIla

**プロパティ**:

- `count` (object, 必須)
  - Counts the number of items. When you specify a property, it returns the number of non-null values for that property.

<u>Example:</u>

To get number of records with non-null value in "nullable_property":
```
{
  "my_nullable_property_count": {
    "count": { "property": [ "football", "game_statistics", "nullable_property" ] }
  }
}
```


---

### CountAggregateResult

Common aggregate structure to represent aggregate result which have one count for filter resultset (Documents Count, Property Cardinality, etc).

**プロパティ**:

- `items` (array, 必須)

---

### CountAggregateResultDms

**プロパティ**:

- `count` (integer, 必須)
  - The number of non-null values for specified in the request property.

---

### CountAggregateResultIla

**プロパティ**:

- `count` (integer, 必須)
  - The number of non-null values for specified in the request property.


---

### CountAndLimit

Current count and associated limit for a specific resource in a project

**プロパティ**:

- `count` (integer, 必須)
- `limit` (integer, 必須)

---

### Create360ContextualizationJobItem

---

### Create360ContextualizationJobRequest

**プロパティ**:

- `items` (array, 必須)

---

### CreateAdvancedJoinMatchesRequestSchema

**プロパティ**:

- `items` (array, 必須)

---

### CreateAdvancedJoinMatchesResponseSchema

**プロパティ**:

- `items` (array, 必須)

---

### CreateAdvancedJoinsRequestSchema

**プロパティ**:

- `items` (array, 必須)

---

### CreateAdvancedJoinsResponseSchema

**プロパティ**:

- `items` (array, 必須)

---

### CreateAssetMapping3D

**プロパティ**:

- `nodeId` (integer, 必須)
  - The ID of the node.
- `assetId` (integer, 任意)
  - The ID of the associated asset (Cognite's Assets API).
- `assetInstanceId` (unknown, 任意)

---

### CreateAssetMapping3DClassicBody

**プロパティ**:

- `items` (array, 必須)

---

### CreateAssetMapping3DDms

**プロパティ**:

- `nodeId` (integer, 必須)
  - The ID of the node.
- `assetInstanceId` (unknown, 必須)

---

### CreateAssetMapping3DDmsBody

**プロパティ**:

- `items` (array, 必須)
- `dmsContextualizationConfig` (unknown, 必須)

---

### CreateBasicAuthentication

**プロパティ**:

- `type` (string, 必須)
  - Authentication type.
- `username` (string, 必須)
  - User name to use for basic authentication
- `password` (string, 必須)
  - Password to use for basic authentication

---

### CreateClientCredentialsAuthentication

**プロパティ**:

- `type` (string, 必須)
  - Authentication type.
- `clientId` (string, 必須)
  - Client ID for for the service principal used by the extractor
- `clientSecret` (string, 必須)
  - Client secret for for the service principal used by the extractor
- `tokenUrl` (string, 必須)
  - URL to fetch authentication tokens from
- `scopes` (string, 必須)
  - A space separated list of scopes
- `defaultExpiresIn` (string, 任意)
  - Default value for the expires_in OAuth 2.0 parameter. If the identity provider does not return expires_in in token requests, this parameter must be set or the request will fail.

---

### CreateConfigRequest

**プロパティ**:

- `externalId` (string, 必須)
  - External ID of the extraction pipeline this configuration revision belongs to.
- `config` (string, 任意)
  - Configuration content.
- `description` (string, 任意)
  - A description of this configuration revision.

---

### CreateConnectionsRequest

List of connections to create

**プロパティ**:

- `items` (array, 必須)

---

### CreateDestination

**プロパティ**:

- `externalId` (unknown, 必須)
- `credentials` (unknown, 任意)
- `targetDataSetId` (integer, 任意)
  - Data set ID the created items are inserted into, if applicable.

---

### CreateEndpoint

**プロパティ**:

- `externalId` (unknown, 必須)
- `endpointType` (string, 必須)
  - SAP OData endpoint
- `instanceId` (string, 必須)
  - External ID of the SAP instance the endpoint should connect to.
- `mappingId` (string, 任意)
  - External ID of the schema mapping this endpoint should use to transform the writeback request.

---

### CreateEntitiesRequest

List of diagram entities to create

**プロパティ**:

- `items` (array, 必須)

---

### CreateEntityRequest



**プロパティ**:

- `externalId` (unknown, 必須)
- `visibility` (unknown, 任意)
- `data` (object, 任意)

---

### CreateEntityRequestList



**プロパティ**:

- `items` (array, 任意)

---

### CreateEntityResponseList



**プロパティ**:

- `items` (array, 任意)

---

### CreateEventHubSource

**プロパティ**:

- `type` (string, 必須)
  - Source type.
- `externalId` (unknown, 必須)
- `host` (string, 必須)
  - Host name or IP address of the event hub consumer endpoint.
- `eventHubName` (string, 必須)
  - Name of the event hub
- `keyName` (string, 任意)
  - The name of the Event Hub key to use for authentication. This parameter is deprecated, use `username` under basic authentication instead.
- `keyValue` (string, 任意)
  - Value of the Event Hub key to use for authentication. This parameter is deprecated, use `password` under basic authentication instead.
- `authentication` (object, 任意)
  - Authentication details for eventhub. Basic authentication uses a shared access key, where the `username` is the key name, and the `password` is the key value.
- `consumerGroup` (string, 任意)
  - The event hub consumer group to use. Microsoft recommends having a distinct consumer group for each application consuming data from event hub. If left out, this uses the default consumer group.

---

### CreateExtPipe

**プロパティ**:

- `externalId` (string, 必須)
  - External Id provided by client. Should be unique within the project.
- `name` (string, 必須)
  - Name of Extraction Pipeline
- `description` (string, 任意)
  - Description of Extraction Pipeline
- `dataSetId` (integer, 必須)
  - DataSet ID
- `rawTables` (array, 任意)
  - Raw tables
- `schedule` (string, 任意)
  - Possible values: “On trigger”, “Continuous” or cron expression. If empty then null
- `contacts` (array, 任意)
  - Contacts list.
- `metadata` (object, 任意)
  - Custom, application specific metadata. String key -> String value. Limits: Key are at most 128 bytes. Values are at most 10240 bytes. Up to 256 key-value pairs. Total size is at most 10240.
- `source` (string, 任意)
  - Source for Extraction Pipeline
- `documentation` (string, 任意)
  - Documentation text field, supports Markdown for text formatting.
- `notificationConfig` (unknown, 任意)
- `createdBy` (string, 任意)
  - Extraction Pipeline creator. Usually user email is expected here

---

### CreateExtPipeRunResponse

---

### CreateFdmTable

---

### CreateGeometriesRequest

List of geometries to create

**プロパティ**:

- `items` (array, 必須)

---

### CreateHeaderValueAuthentication

**プロパティ**:

- `type` (string, 必須)
  - Authentication type.
- `key` (string, 必須)
  - Key for the header to place the authentication token in
- `value` (string, 必須)
  - Value of the authentication token

---

### CreateImage360AnnotationContextualizationItem

**プロパティ**:

- `asset` (unknown, 必須)
- `image360` (unknown, 必須)
- `polygon` (unknown, 必須)

---

### CreateImage360AnnotationContextualizationList

---

### CreateInputConfig

Optionally set input mapping input type. This defaults to json if left out. Each input type is converted to JSON before being passed to the mapping expression.

---

### CreateInstance

**プロパティ**:

- `externalId` (unknown, 必須)
- `gatewayUrl` (string, 必須)
  - SAP NetWeaver Gateway URL
- `client` (integer, 必須)
  - SAP client number
- `username` (string, 必須)
  - SAP username to use when connecting to the SAP NetWeaver Gateway URL
- `password` (string, 必須)
  - SAP password to use when connecting to the SAP NetWeaver Gateway URL

---

### CreateJob

**プロパティ**:

- `externalId` (unknown, 必須)
- `destinationId` (string, 必須)
  - ID of the destination this job should write to.
- `sourceId` (string, 必須)
  - ID of the source this job should read from.
- `format` (object, 必須)
  - The format of the messages from the source. This is used to convert messages coming from the source system to a format that can be inserted into CDF.
- `config` (unknown, 必須)

---

### CreateJobResponse

**プロパティ**:

- `items` (array, 必須)

---

### CreateKafkaSource

**プロパティ**:

- `type` (string, 必須)
  - Source type.
- `externalId` (unknown, 必須)
- `bootstrapBrokers` (unknown, 必須)
- `authentication` (unknown, 任意)
- `useTls` (boolean, 任意)
  - If true, use TLS when connecting to the broker
- `caCertificate` (unknown, 任意)
- `authCertificate` (unknown, 任意)

---

### CreateLibrariesRequest

List of libraries to create

**プロパティ**:

- `items` (array, 必須)

---

### CreateMapping

**プロパティ**:

- `externalId` (unknown, 必須)
- `mapping` (unknown, 必須)
- `input` (unknown, 任意)
- `published` (boolean, 必須)
  - Whether this mapping is published and should be available to be used in jobs.

---

### CreateModel3D

**プロパティ**:

- `name` (string, 必須)
  - The name of the model.
- `dataSetId` (unknown, 任意)
- `metadata` (unknown, 任意)

---

### CreateModel3DClassicBody

**プロパティ**:

- `items` (array, 必須)

---

### CreateModel3DDmsOnly

**プロパティ**:

- `name` (string, 必須)
  - The name of the model.
- `space` (unknown, 必須)
- `thumbnailReference` (unknown, 任意)
- `type` (unknown, 必須)

---

### CreateModel3DDmsOnlyBody

**プロパティ**:

- `items` (array, 必須)

---

### CreateMqttBrokerSource

Create a source that lets users write data to CDF using the MQTT protocol.

**プロパティ**:

- `type` (string, 必須)
  - Source type.
- `externalId` (unknown, 必須)

---

### CreateMqttV3Source

**プロパティ**:

- `type` (string, 必須)
  - Source type.
- `externalId` (unknown, 必須)
- `host` (string, 必須)
  - Host or IP address of the MQTT broker to connect to.
- `port` (integer, 任意)
  - Port on the MQTT broker to connect to.
- `authentication` (unknown, 任意)
- `useTls` (boolean, 任意)
  - If true, use TLS when connecting to the broker.
- `caCertificate` (unknown, 任意)
- `authCertificate` (unknown, 任意)

---

### CreateMqttV5Source

**プロパティ**:

- `type` (string, 必須)
  - Source type.
- `externalId` (unknown, 必須)
- `host` (string, 必須)
  - Host or IP address of the MQTT broker to connect to.
- `port` (integer, 任意)
  - Port on the MQTT broker to connect to.
- `authentication` (unknown, 任意)
- `useTls` (boolean, 任意)
  - If true, use TLS when connecting to the broker.
- `caCertificate` (unknown, 任意)
- `authCertificate` (unknown, 任意)

---

### CreateOptimizerJobItem

---

### CreateOptimizerJobRequest

**プロパティ**:

- `items` (array, 必須)

---

### CreatePointCloudVolumeContextualizationItem

**プロパティ**:

- `assetInstanceId` (unknown, 必須)
- `volumeReference` (string, 必須)
  - A unique reference for the volume within a revision.
- `volume` (unknown, 必須)

---

### CreatePointCloudVolumeContextualizationItemRC

**プロパティ**:

- `asset` (unknown, 必須)
- `volumeReference` (string, 必須)
  - A unique reference for the volume within a revision.
- `volume` (unknown, 必須)

---

### CreatePointCloudVolumeContextualizationList

---

### CreatePointCloudVolumeContextualizationRCList

---

### CreatePreview

Create object for a preview job.

**プロパティ**:

- `externalId` (unknown, 必須)
- `sourceId` (string, 必須)
  - ID of the source this preview job should read from.
- `config` (unknown, 必須)
- `format` (object, 任意)
  - Input format parameters.
- `input` (unknown, 任意)

---

### CreateProtoFile

Definition of a protobuf file used for loading input to the mapping.

**プロパティ**:

- `fileName` (string, 必須)
  - Name of protobuf file. Must contain only letters, numbers, underscores, and hyphens, and must end with '.proto'.
- `content` (string, 必須)
  - Protobuf file content. Must be a valid protobuf file.

---

### CreateProtobufConfig

List of protobuf files used to define input to the mapping. This will be compiled to a collection of definitions which will be used to convert the input to JSON.

**プロパティ**:

- `type` (string, 必須)
  - Input type
- `messageName` (string, 必須)
  - Name of root message in the protobuf files.
- `files` (array, 必須)
  - List of protobuf files as text.

---

### CreateQueryParamAuthentication

**プロパティ**:

- `type` (string, 必須)
  - Authentication type.
- `key` (string, 必須)
  - Key for the query parameter to place the authentication token in
- `value` (string, 必須)
  - Value of the authentication token

---

### CreateRawTable

---

### CreateRequest

**プロパティ**:

- `endpointId` (string, 必須)
  - External ID of the SAP endpoint the writeback request should be sent to.
- `request` (unknown, 必須)

---

### CreateRequestItem

**プロパティ**:

- `key` (string, 任意)
  - External object key related to this request.
- `fileId` (string, 任意)
  - CDF File external Id. Only use when the SAP endpoint type is 'attachment'
- `payload` (object, 必須)
  - Payload to send to the SAP endpoint

---

### CreateRequestItemResponse

**プロパティ**:

- `payload` (object, 必須)
  - Payload to send to the SAP endpoint

---

### CreateRequestResponse

**プロパティ**:

- `requestId` (string, 必須)
  - Unique request ID generated by the CDF writeback API.
- `request` (unknown, 必須)
- `status` (string, 必須)
  - Writeback request status
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)

---

### CreateRestSource

**プロパティ**:

- `type` (string, 必須)
  - Source type.
- `externalId` (unknown, 必須)
- `host` (string, 必須)
  - Host or IP address to connect to.
- `scheme` (string, 任意)
  - Type of connection to establish
- `port` (integer, 任意)
  - Port on server to connect to. Uses default ports based on the scheme if omitted.
- `caCertificate` (unknown, 任意)
- `authCertificate` (unknown, 任意)
- `authentication` (unknown, 任意)
  - Authentication details for source

---

### CreateRevision3D

Create a 3D revision for a model in a Hybrid projects, using the CreateRevision3DClassicAndHybridBody.
In DataModelOnly projects, please use the 3d/jobs endpoint to create processing jobs for 3D source data, which will also create revisions.


**プロパティ**:

- `published` (boolean, 任意)
  - True if the revision is marked as published.
- `rotation` (array, 任意)
- `scale` (array, 任意)
- `translation` (array, 任意)
- `metadata` (unknown, 任意)
- `camera` (unknown, 任意)
- `fileId` (integer, 必須)
  - The file id to a file uploaded to Cognite's Files API. Can only be set on revision creation, and can never be updated.

---

### CreateRevision3DClassicAndHybridBody

**プロパティ**:

- `items` (array, 必須)

---

### CreateRevision3DDmsOnly

**プロパティ**:

- `nonce` (string, 必須)
  - Session nonce for recently created CDF session.
- `fileId` (integer, 必須)
  - The file id to a file uploaded to Cognite's Files API. Can only be set on revision creation, and can never be updated.

---

### CreateRevision3DDmsOnlyBody

**プロパティ**:

- `items` (array, 必須)

---

### CreateSchemaMapping

**プロパティ**:

- `externalId` (unknown, 必須)
- `expression` (string, 必須)
  - Custom transform expression written in the Cognite mapping language.

---

### CreateScramSha256

**プロパティ**:

- `type` (string, 必須)
  - Authentication type
- `username` (string, 必須)
  - Username for authentication
- `password` (string, 必須)
  - Password for authentication

---

### CreateScramSha512

**プロパティ**:

- `type` (string, 必須)
  - Authentication type
- `username` (string, 必須)
  - Username for authentication
- `password` (string, 必須)
  - Password for authentication

---

### CreateSessionRequest



---

### CreateSessionRequestList



**プロパティ**:

- `items` (array, 任意)

---

### CreateSessionResponse

A response with the ID, nonce and other information related to the session. The nonce
is short-lived and should be immediately passed to the endpoint that will use
the session.


**プロパティ**:

- `id` (number, 必須)
  - ID of the session
- `type` (string, 任意)
  - Values reserved for future use
- `status` (string, 必須)
  - Current status of the session
- `nonce` (string, 必須)
  - Nonce to be passed to the internal service that will bind the session
- `clientId` (string, 任意)
  - Client ID in identity provider. Returned only if the session was created using client credentials

---

### CreateSessionResponseList



**プロパティ**:

- `items` (array, 任意)

---

### CreateSessionWithClientCredentialsRequest

Credentials for a session using client credentials from an identity provider.


**プロパティ**:

- `clientId` (string, 必須)
  - Client ID in identity provider
- `clientSecret` (string, 必須)
  - Client secret in identity provider

---

### CreateSessionWithOneshotTokenExchangeRequest

Credentials for a session using one-shot token exchange to reuse the user's credentials.
One-shot sessions are short-lived sessions that are not refreshed and do not require support for token exchange from the identity provider.


**プロパティ**:

- `oneshotTokenExchange` (boolean, 必須)
  - Use one-shot token exchange for the session. Must be `true`.

---

### CreateSessionWithTokenExchangeRequest

Credentials for a session using token exchange to reuse the user's credentials.


**プロパティ**:

- `tokenExchange` (boolean, 必須)
  - Use token exchange for the session. Must be `true`.

---

### CreateStreamSettings

Stream settings which should be applied to a stream.


**プロパティ**:

- `template` (object, 必須)
  - Reference to a template which should be used to define initial settings for the stream.


---

### CreateSymbolsRequest

List of symbols to create

**プロパティ**:

- `items` (array, 必須)

---

### CreateTable

---

### CreateTable_

**プロパティ**:

- `type` (string, 必須)
- `tablename` (string, 必須)
  - Name of the foreign table.\
__CANNOT__ be any of the the built-in table names found [here](https://docs.cognite.com/cdf/integration/guides/interfaces/ingest_into_raw#create-a-table)


---

### CreateUser

**プロパティ**:

- `credentials` (unknown, 必須)

---

### CreateUserHistoryAppsRequest

**プロパティ**:

- `items` (array, 必須)

---

### CreateUserHistoryResourcesRequest

**プロパティ**:

- `items` (array, 必須)

---

### CreateViewProperty

**プロパティ**:

- `name` (string, 任意)
  - Readable property name.
- `description` (string, 任意)
  - Description of the content and suggested use for this property.
- `container` (unknown, 必須)
- `containerPropertyIdentifier` (unknown, 必須)
  - The unique identifier for the property (Unique within the referenced container).
- `source` (unknown, 任意)
  - Indicates on what type a referenced direct relation is expected to be (although not required). Only applicable for direct relation properties.

---

### CreatedTime

The creation time of the resource, in milliseconds since January 1, 1970 at 00:00 UTC.

---

### CronTrigger

**プロパティ**:

- `triggerType` (string, 必須)
- `cronExpression` (string, 必須)
  - A cron expression (UNIX format) specifying when the trigger should be executed. Use https://crontab.guru/ to create a cron expression. The API may adjust the exact timing of cron job executions to distribute the backend load more evenly. However, it will aim to maintain the overall frequency of executions as specified in the cron expression.
- `timezone` (string, 任意)
  - Specifies the IANA time zone in which the cron expression is evaluated. Time zones must be valid as listed in https://docs.oracle.com/cd/E72987_01/wcs/tag-ref/MISC/TimeZones.html.

---

### CronValue

Cron expression describes when the job should run.

---

### CsvConfig

Transform the input from a CSV (Comma Separated Values) file to a list of JSON objects. The input to the mapping will be a list on the form `[{\"header1\": \"value1\", ...}, ...]`.


**プロパティ**:

- `type` (string, 必須)
  - Input type
- `delimiter` (string, 任意)
  - A single ASCII character used as the separator in the CSV file. Defaults to `,`
- `customKeys` (array, 任意)
  - List of headers. If this is not set, the headers will be retrieved from the CSV file.

---

### Cursor

Cursor for paging through results. In general, if a response contains a `nextCursor`
property, it means that there may be more results, and you should pass that value as the
`cursor` parameter in the next request.

Note that the cursor may or may not be encrypted, but either way, it is not intended to be
decoded. Its internal structure is not a part of the public API, and may change without
notice. You should treat it as an opaque string and not attempt to craft your own cursors.


**プロパティ**:

- `cursor` (string, 任意)

---

### CursoringNotice

---

### CustomFormat

**プロパティ**:

- `type` (string, 必須)
  - Format type.
- `mappingId` (string, 必須)
  - The ID of the mapping used for this format.
- `encoding` (unknown, 任意)
- `compression` (unknown, 任意)
- `expression` (string, 必須)
  - The transformation expression of the mapping given by `mappingId`.

---

### CustomMapping

**プロパティ**:

- `expression` (string, 必須)
  - Custom transform expression written in the Cognite transformation language.

---

### CustomizeFuzziness

Additional requirements for the fuzzy matching algorithm. The fuzzy match is allowed if any of these are true for each match candidate. The overall minFuzzyScore still applies, but a stricter fuzzyScore can be set here, which would not be enforced if either the minChars or maxBoxes conditions are met, making it possible to exclude detections using replacements if they are either short, or combined from many boxes.

**プロパティ**:

- `minChars` (integer, 任意)
  - The minimum number of characters that must be present in the candidate match string.
- `maxBoxes` (integer, 任意)
  - Maximum number of text boxes the potential match is composed of.
- `fuzzyScore` (number, 任意)
  - The minimum fuzzy score of the candidate match.

---

### CylinderPointCloudVolumeV1

**プロパティ**:

- `type` (string, 必須)
- `version` (string, 必須)
- `data` (array, 必須)
  - An array of 7 floats defining the cylinder volume.
The array should contain the following values, in the specified order:

```json
[ pointAx, pointAy, pointAz,
  pointBx, pointBy, pointBz,
  radius ]
```


---

### DMSExistsFilter

**プロパティ**:

- `exists` (object, 必須)
  - Will match items that have a value in the specified property.


---

### DMSExternalId

---

### DMSFileChange

Changes will be applied to file.

**プロパティ**:

- `update` (object, 必須)

---

### DMSFilterProperty

A reference to either a DMS base property or a container property.
For nodes the DMS base properties are `instanceType`, `version`,
`space`, `externalId`, `type`, `createdTime`, `lastUpdatedTime`,
and `deletedTime`. For edges the DMS base properties
are all of the node base properties with the addition of
`startNode` and `endNode`.

References to DMS base properties are on the form
`["node", "identifier"]` or `["edge", "identifier"]` where
`identifier` is a DMS base property.

Container properties can be referenced either through a view
 or through the container where it is defined.
- To reference a property through the container where it
    is defined the format is
    `["space", "externalId", "identifier"]` where `space`,
    `externalId` is the space and
    externalId of the container and `identifier` is one of the
    property identifiers defined in the container.
- To reference a property through one of the views mapping it the format
  is `["space", "externalId/version", "identifier"]`
  where `space`, `externalId`, `version` is the space, externalId,
  and version of the view respectively, and `identifier` is one of the
  property identifiers defined in the view.


---

### DMSVersion

---

### DataAsset

**プロパティ**:

- `items` (array, 必須)

---

### DataAssetChange

**プロパティ**:

- `items` (array, 必須)

---

### DataDataSetAggregate

**プロパティ**:

- `items` (array, 必須)

---

### DataDmsIdentifiers

**プロパティ**:

- `items` (array, 必須)
  - List of ID objects

---

### DataEvent

**プロパティ**:

- `items` (array, 必須)

---

### DataEventAggregate

**プロパティ**:

- `items` (array, 必須)

---

### DataEventChange

**プロパティ**:

- `items` (array, 必須)

---

### DataExternalAsset

**プロパティ**:

- `items` (array, 必須)

---

### DataExternalAssetItem

---

### DataExternalEvent

**プロパティ**:

- `items` (array, 必須)

---

### DataExternalFileMetadata

**プロパティ**:

- `items` (array, 任意)

---

### DataFileChange

**プロパティ**:

- `items` (array, 必須)

---

### DataFileMetadata

**プロパティ**:

- `items` (array, 任意)

---

### DataFileUploadFileMetadata

**プロパティ**:

- `items` (array, 任意)

---

### DataFilesAggregate

**プロパティ**:

- `items` (array, 必須)

---

### DataGetSequence

**プロパティ**:

- `items` (array, 必須)

---

### DataGetTimeSeriesMetadataDTO

List of responses. The order matches the requests order.

**プロパティ**:

- `items` (array, 必須)

---

### DataGroup

**プロパティ**:

- `items` (array, 必須)

---

### DataGroupSpec

**プロパティ**:

- `items` (array, 必須)

---

### DataIdentifier

**プロパティ**:

- `id` (unknown, 必須)

---

### DataIdentifiers

**プロパティ**:

- `items` (array, 必須)
  - List of ID objects

---

### DataLong

**プロパティ**:

- `items` (array, 必須)

---

### DataModel

---

### DataModelCore

**プロパティ**:

- `space` (unknown, 必須)
  - Id of the space that the data model belongs to
- `externalId` (unknown, 必須)
  - External id that uniquely identifies this data model
- `name` (string, 任意)
  - Readable name meant for use in UIs
- `description` (string, 任意)
  - Description of the content and intended use of the data model
- `version` (unknown, 必須)
  - Data model version (opaque string controlled by client applications)

---

### DataModelCreate

---

### DataModelCreateCollection

**プロパティ**:

- `items` (array, 必須)
  - List of data models to create/update

---

### DataModelCreateProperty

---

### DataModelInfo

Target data model info

**プロパティ**:

- `space` (string, 必須)
  - Space of the Data Model
- `externalId` (string, 必須)
  - External ID of the Data Model
- `version` (string, 必須)
  - Version of the Data Model
- `destinationType` (string, 必須)
  - External ID of the type(view) in the data model
- `destinationRelationshipFromType` (string, 任意)
  - Property Identifier of the connection definition in destinationType

---

### DataModelProperty

---

### DataModelReference

Data model reference

**プロパティ**:

- `space` (unknown, 必須)
  - Id of the space that the data model belongs to
- `externalId` (unknown, 必須)
  - External-id of the data model
- `version` (unknown, 必須)
  - Version of the data model

---

### DataModelSource

**プロパティ**:

- `type` (string, 必須)
  - The type of the destination resource indicating that the transformation will write to data models
- `dataModel` (unknown, 必須)
- `instanceSpace` (string, 任意)
  - The space where the instances will be created.

---

### DataModelingStatus

Status of data modeling for a project.

---

### DataModelingTrigger

**プロパティ**:

- `triggerType` (string, 必須)
  - A trigger that starts a workflow execution based on the result of a data modeling query. The query is executed at regular interval and its output is handled incrementally. The batchSize controls the maximum number of items to pass as input to a workflow execution. The batchTimeout controls the maximum time to wait for the batch to be filled before passing it to a workflow execution. A partial batch will be passed to the workflow execution after the batchTimeout has passed. A full batch will be passed to the workflow execution without further delay.
- `dataModelingQuery` (object, 必須)
  - Data Modeling Sync query definition. When used as part of a trigger, the `limit` specified in each `with` clause that is referenced by the `select` must exactly match the trigger’s `batchSize` parameter. There is no default `limit`; users must include it explicitly in their query.

- `batchSize` (integer, 必須)
  - The maximum number of items to pass to a workflow execution. When a dataModeling query is used, the `limit` in each referenced `with` clause must exactly match this `batchSize` value.
- `batchTimeout` (integer, 必須)
  - The maximum time in seconds to wait for the batch to be filled before passing it to a workflow execution.

---

### DataModels

Data models configuration to specify the space for all instances.

**プロパティ**:

- `space` (string, 必須)
  - The data models space where time series will be created.

---

### DataModelsBoolFilter

Build a new query by combining other queries, using boolean operators. We support the `and`, `or`, and
`not` boolean operators.


---

### DataModelsLeafFilter

Leaf filter

---

### DataModelsListRequest

---

### DataModelsNestedFilter

**プロパティ**:

- `nested` (object, 必須)
  - Use `nested` to apply the properties of the direct relation as the filter.  `scope` specifies the direct
relation property you want use as the filtering property.

Example:
```
  {
    "nested": {
      "scope": ["some", "direct_relation", "property"],
      "filter": {
        "equals": {
          "property": ["node", "name"],
          "value": "ACME"
        }
      }
    }
  }
```


---

### DataModelsSort

**プロパティ**:

- `property` (string, 必須)
  - The property we should base sorting on when we return ordered data
- `direction` (string, 任意)
  - The sort order for the returned information
- `nullsFirst` (boolean, 任意)
  - Should we list nulls first, or last (default) in the returned dataset

---

### DataMultiPartUploadFileMetadata

**プロパティ**:

- `items` (array, 任意)

---

### DataPointsAggregate

---

### DataPostSequence

**プロパティ**:

- `items` (array, 必須)

---

### DataProduct

**プロパティ**:

- `externalId` (unknown, 必須)
- `name` (string, 必須)
  - Human-readable name of the data product.
- `schemaSpace` (unknown, 必須)
  - The schema space where the data product's data model and associated views are located. This space is exclusively owned by this data product.

- `description` (string, 任意)
  - A description of the data product.
- `isGoverned` (boolean, 必須)
  - Indicates whether the data product follows governance policies and standards.
- `tags` (array, 必須)
  - A list of distinct tags for categorization and filtering.
- `domains` (array, 必須)
  - List of data domain external IDs that this data product is derived from. This field will be empty while the data domains feature is in development.
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)

---

### DataProductChange

**プロパティ**:

- `externalId` (unknown, 必須)
- `update` (unknown, 必須)

---

### DataProductCreate

**プロパティ**:

- `externalId` (unknown, 必須)
- `name` (string, 必須)
  - Human-readable name of the data product.
- `schemaSpace` (unknown, 任意)
  - The schema space where the data product's data model(s) and associated views will be located.
This space will be exclusively owned by this data product.
If this field is specified, and the space does not exist, it will be created.
If this field is not specified, we will use this data product's externalId. If the space does not exist, it will be created, otherwise, the data product will claim ownership of the space.

- `description` (string, 任意)
  - A description of the data product.
- `isGoverned` (boolean, 任意)
  - Indicates whether the data product follows governance policies and standards.
- `tags` (array, 任意)
  - A list of distinct tags for categorization and filtering. If the list contains duplicates, only one occurrence will be kept. The order of tags is not guaranteed to be preserved.

---

### DataProductCreateRequest

**プロパティ**:

- `items` (array, 必須)
  - Array of data products to create.

---

### DataProductDeleteRequest

**プロパティ**:

- `items` (array, 必須)
  - List of ID objects

---

### DataProductExternalId

Data product external identifier

---

### DataProductPatch

Changes to apply to a data product

**プロパティ**:

- `name` (unknown, 任意)
- `description` (unknown, 任意)
- `isGoverned` (unknown, 任意)
- `tags` (unknown, 任意)

---

### DataProductUpdateRequest

**プロパティ**:

- `items` (array, 必須)
  - List of data products to update.

---

### DataProductVersion

Version of a data product with immutable data model reference. A data product can have up to 10 versions.


**プロパティ**:

- `versionId` (integer, 必須)
  - The version id of this data product version. Together, the data product external id (in the request path) and the version id uniquely identifies this data product version.
The version id is an auto-incremented integer starting on 1 for each data product. Note that even if a data product version is deleted,
its version id will never be reused within its owning data product.

- `dataModel` (object, 必須)
  - An immutable reference to the data model version that will be associated with this data product version.
- `status` (unknown, 必須)
- `description` (string, 任意)
  - A detailed description of this specific version of the data product. The text will be interpreted as markdown.
- `terms` (object, 必須)
  - Terms and conditions for using this data product version.
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)

---

### DataProductVersionChange

**プロパティ**:

- `versionId` (integer, 必須)
  - The version id to update.
- `update` (unknown, 必須)

---

### DataProductVersionCreate

Version of a data product with immutable data model reference. A data product can have up to 10 versions.


**プロパティ**:

- `dataModel` (object, 必須)
  - An immutable reference to a data model version that will be associated with this data product version. Only externalId and version are required to identify the data model, as the space is the data product's schema space. The referenced data model must already exist in the data product's schema space.
- `status` (unknown, 任意)
- `description` (string, 任意)
  - A detailed description of this specific version of the data product. The text will be interpreted as markdown.
- `terms` (object, 任意)
  - Terms and conditions for using this data product version.

---

### DataProductVersionCreateRequest

**プロパティ**:

- `items` (array, 必須)
  - Array of data product versions to create.

---

### DataProductVersionDataModelPatch

Modify nested dataModel object fields.

**プロパティ**:

- `modify` (object, 任意)
  - Modify dataModel fields

---

### DataProductVersionDeleteRequest

**プロパティ**:

- `items` (array, 必須)
  - Array of version ids (of this data product) to delete.

---

### DataProductVersionPatch

Changes to apply to a data product version

**プロパティ**:

- `status` (unknown, 任意)
- `description` (unknown, 任意)
- `terms` (unknown, 任意)
- `dataModel` (unknown, 任意)

---

### DataProductVersionStatus

The status of this data product version.

---

### DataProductVersionTermsPatch

Modify nested terms object fields.

**プロパティ**:

- `modify` (object, 任意)
  - Modify terms fields

---

### DataProductVersionUpdateRequest

**プロパティ**:

- `items` (array, 必須)
  - List of data product versions to update.

---

### DataProductVersionViewsPatch

Change that will be applied to the views array.

---

### DataProductVersionViewsSet

**プロパティ**:

- `set` (array, 必須)
  - List of view spaces

---

### DataProjectSpec

**プロパティ**:

- `items` (array, 必須)

---

### DataRawDB

**プロパティ**:

- `items` (array, 任意)

---

### DataRawDBRow

**プロパティ**:

- `items` (array, 任意)

---

### DataRawDBRowKey

**プロパティ**:

- `items` (array, 任意)

---

### DataRawDBTable

**プロパティ**:

- `items` (array, 任意)

---

### DataRawDBTableCursors

A list of cursors

**プロパティ**:

- `items` (array, 任意)

---

### DataResourceIds

**プロパティ**:

- `items` (array, 必須)

---

### DataResourceIdsWithIgnoreUnknownIds

---

### DataSecurityCategorySpecDTO

**プロパティ**:

- `items` (array, 必須)

---

### DataSequenceChange

**プロパティ**:

- `items` (array, 必須)

---

### DataSequenceDataDeleteRequest

**プロパティ**:

- `items` (array, 必須)

---

### DataSequencePostData

**プロパティ**:

- `items` (array, 必須)

---

### DataSet

---

### DataSetAggregate

Aggregation group of data sets

**プロパティ**:

- `count` (integer, 必須)
  - Size of the aggregation group

---

### DataSetAggregateRequest

Aggregation request of data sets. Filters exact field matching or timestamp ranges inclusive min and max.

---

### DataSetChangeByExternalId

---

### DataSetChangeById

---

### DataSetExternalId

**プロパティ**:

- `externalId` (unknown, 必須)

---

### DataSetFilter

Filter on data sets with strict matching.

**プロパティ**:

- `metadata` (unknown, 任意)
- `createdTime` (unknown, 任意)
- `lastUpdatedTime` (unknown, 任意)
- `externalIdPrefix` (unknown, 任意)
- `writeProtected` (boolean, 任意)

---

### DataSetFilterRequest

---

### DataSetId

The dataSet Id for the item.

---

### DataSetIdEither

---

### DataSetIdEitherList

---

### DataSetIdEithers

---

### DataSetIdUpdateField

Set a new value for dataSetId.

**プロパティ**:

- `set` (integer, 任意)
  - DataSet ID

---

### DataSetInternalId

**プロパティ**:

- `id` (unknown, 必須)

---

### DataSetList

**プロパティ**:

- `items` (array, 必須)

---

### DataSetListWithCursor

---

### DataSetMetadata

Custom, application specific metadata. String key -> String value. Limits: Maximum length of key is 128 bytes, value 10240 bytes, up to 256 key-value pairs, of total size at most 10240.

---

### DataSetPatch

Update applied to single data set

**プロパティ**:

- `update` (object, 必須)

---

### DataSetSpec

**プロパティ**:

- `externalId` (unknown, 任意)
- `name` (string, 任意)
  - The name of the data set.
- `description` (string, 任意)
  - The description of the data set.
- `metadata` (unknown, 任意)
- `writeProtected` (boolean, 任意)
  - To write data to a write-protected data set, you need to be a member of a group that has the "datasets:owner" action for the data set. To learn more about write-protected data sets, follow this [guide](https://docs.cognite.com/cdf/data_governance/concepts/datasets/#write-protection).

---

### DataSetSpecList

**プロパティ**:

- `items` (array, 必須)

---

### DataSetUpdate

---

### DataSetUpdateList

**プロパティ**:

- `items` (array, 必須)

---

### DataWithCursor

A list of objects along with possible cursors to get the next page of results

**プロパティ**:

- `items` (array, 任意)
- `nextCursor` (string, 任意)
  - Cursor to get the next page of results (if available).

---

### DataWithCursorAsset

A list of objects along with possible cursors to get the next or previous page of results.

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
  - The cursor to get the next page of results (if available).

---

### DataWithCursorEvent

A list of objects along with possible cursors to get the next or previous page of results.

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
  - Cursor to get the next page of results (if available).

---

### DataWithCursorGetTimeSeriesMetadataDTO

A list of objects and possible cursors to get the next results page.

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
  - The cursor to get the next page of results (if available).

---

### DataWithCursorRawDB

A list of objects along with possible cursors to get the next, or previous, page of results

**プロパティ**:

- `items` (array, 任意)
- `nextCursor` (string, 任意)
  - Cursor to get the next page of results (if available).

---

### DataWithCursorRawDBRow

A list of objects along with possible cursors to get the next, or previous, page of results

**プロパティ**:

- `items` (array, 任意)
- `nextCursor` (string, 任意)
  - Cursor to get the next page of results (if available).

---

### DataWithCursorRawDBTable

A list of objects along with possible cursors to get the next, or previous, page of results

**プロパティ**:

- `items` (array, 任意)
- `nextCursor` (string, 任意)
  - Cursor to get the next page of results (if available).

---

### DatapointStatus

The [status code](<https://developer.cognite.com/dev/concepts/reference/status_codes>) of the datapoint.

The most common status codes are:\
_Good_ (0) (Default)\
_Uncertain_ (1073741824)\
_Bad_ (2147483648)

Only one of code and symbol is required. If both are defined, they must match.

**プロパティ**:

- `code` (integer, 任意)
  - The numeric status code of the data point.
- `symbol` (string, 任意)
  - The status name of the data point.

---

### DatapointsDeleteQuery

**プロパティ**:

- `items` (array, 必須)
  - List of delete filters.

---

### DatapointsDeleteRange

**プロパティ**:

- `inclusiveBegin` (unknown, 必須)
- `exclusiveEnd` (unknown, 任意)

---

### DatapointsDeleteRequest

Select time series and data points to delete.

---

### DatapointsGetAggregateDatapoint

---

### DatapointsGetDatapoint

---

### DatapointsGetDoubleDatapoint

---

### DatapointsGetStateDatapoint

---

### DatapointsGetStringDatapoint

---

### DatapointsInsertProperties

**プロパティ**:

- `datapoints` (array, 必須)
  - The list of data points. The total number of data points in a single request is limited to 100000, across all time series.

---

### DatapointsInsertQuery

**プロパティ**:

- `items` (array, 必須)

---

### DatapointsLatestQuery

---

### DatapointsMetadata

**プロパティ**:

- `id` (unknown, 必須)
- `externalId` (string, 任意)
  - The external ID of the time series the data points belong to.
- `instanceId` (unknown, 任意)

---

### DatapointsMultiQuery

---

### DatapointsOrAggregatesResponse

The list of responses. The order matches the requests order.

**プロパティ**:

- `items` (array, 必須)

---

### DatapointsPostDatapoint

---

### DatapointsQuery

Parameters describing a query for data points.

---

### DatapointsQueryProperties

**プロパティ**:

- `start` (unknown, 任意)
- `end` (unknown, 任意)
- `limit` (integer, 任意)
  - Returns up to this number of data points. The maximum is 100000 non-aggregated data points and 10000 aggregated data points in total across all queries in a single request.
- `aggregates` (array, 任意)
  - Specify the aggregates to return. Omit to use top-level value.
- `granularity` (string, 任意)
  - The granularity size and granularity of the aggregates. Omit to use top-level value.
- `targetUnit` (string, 任意)
  - The [unit](<https://developer.cognite.com/dev/concepts/resource_types/units/>) externalId of the data points returned. If the time series does not have a unitExternalId that can be converted to the targetUnit, an error will be returned. Cannot be used with targetUnitSystem.
- `targetUnitSystem` (string, 任意)
  - The [unit system](<https://developer.cognite.com/dev/concepts/resource_types/units/>) of the data points returned. Cannot be used with targetUnit.
- `includeOutsidePoints` (boolean, 任意)
  - Defines whether to include the last data point before the requested time period and the first one after. This option can be useful for interpolating data. It's not available for aggregates or cursors.
Note: If there are more than `limit` data points in the time period, we will omit the excess data points and then append the first data point after the time period, thus causing a gap with omitted data points. When this is the case, we return up to `limit+2` data points.
When doing manual paging (sequentially requesting smaller intervals instead of requesting a larger interval and using cursors to get all the data points) with this field set to true, the `start` of the each subsequent request should be one millisecond more than the `timestamp` of the _second-to-last_ data point from the previous response. This is because the last data point in most cases will be the extra point from outside the interval.

- `includeStatus` (boolean, 任意)
  - Show the [status code](<https://developer.cognite.com/dev/concepts/reference/status_codes>) for each data point in the response.
_Good_ (code = 0) status codes are always omitted.
Only relevant for raw data points queries and 'minDatapoint'/'maxDatapoint' aggregates.
- `ignoreBadDataPoints` (boolean, 任意)
  - Treat data points with a _Bad_ status code as if they do not exist.

If set to false, raw queries will include bad data points in the response, and
aggregates will in general omit the time period between a bad data point and the next good data point.
Also, the period between a bad data point and the previous good data point will be considered constant.

Note that bad data points may contain the string values 'NaN', 'Infinity', or '-Infinity', or be null (omitted).
- `treatUncertainAsBad` (boolean, 任意)
  - Treat data points with _Uncertain_ status codes as _Bad_. If false, treat data points with _Uncertain_ status codes as _Good_.
Used for both raw queries and aggregates.
- `cursor` (string, 任意)
  - To retrieve next page, insert the `nextCursor` from a previous response. Be sure to match with the corresponding time series. Not compatible with `includeOutsidePoints`.

- `timeZone` (string, 任意)
  - Which time zone to align aggregates to. Omit to use top-level value.


---

### DatapointsResponse

The list of responses. The order matches the requests order.

**プロパティ**:

- `items` (array, 必須)

---

### DeadAngle

The angle between the start and end point on the bottom part of an analog gauge, measured in degrees.

---

### DebugNotice

---

### DebugParameters

Return query debug notices.

**プロパティ**:

- `emitResults` (boolean, 任意)
  - Include the query result in the response. emitResults=false is required for advanced query analysis features.
- `timeout` (integer, 任意)
  - Query timeout in milliseconds. Can be used to override the default timeout when analysing queries. Requires emitResults=false.
- `profile` (boolean, 任意)
  - Most thorough level of query analysis. Requires emitResults=false.

---

### DebugResponse

Contains debug notices if debug flag is set in the query.

**プロパティ**:

- `notices` (array, 任意)
  - A list of notices that provide insights into the query's execution. These can highlight potential performance issues, offer optimization suggestions, or explain aspects of the query processing. Each notice falls into a category, such as indexing, sorting, filtering, or cursoring, to help identify areas for improvement.

---

### DefaultError

Cognite API error

**プロパティ**:

- `code` (integer, 任意)
  - HTTP status code
- `message` (string, 任意)
  - Error message
- `missing` (array, 任意)
  - List of lookup objects that do not match any results.
- `duplicated` (array, 任意)
  - List of objects that are not unique.

---

### DeleteAdvancedJoinMatchesRequestSchema

**プロパティ**:

- `items` (array, 必須)

---

### DeleteAdvancedJoinsRequestSchema

**プロパティ**:

- `items` (array, 必須)

---

### DeleteAssetMapping3D

**プロパティ**:

- `nodeId` (integer, 必須)
  - The ID of the node.
- `assetId` (integer, 任意)
  - The ID of the associated asset (Cognite's Assets API).
- `assetInstanceId` (unknown, 任意)

---

### DeleteAssetMapping3DClassicBody

**プロパティ**:

- `items` (array, 必須)

---

### DeleteAssetMapping3DDms

**プロパティ**:

- `nodeId` (integer, 必須)
  - The ID of the node.
- `assetInstanceId` (unknown, 必須)

---

### DeleteAssetMapping3DDmsBody

**プロパティ**:

- `items` (array, 必須)
- `dmsContextualizationConfig` (unknown, 必須)

---

### DeleteContextualizedImage360AnnotationsBody

**プロパティ**:

- `items` (unknown, 必須)
- `dmsContextualizationConfig` (unknown, 必須)

---

### DeleteContextualizedImage360AnnotationsRCBody

**プロパティ**:

- `items` (unknown, 必須)
- `dmsContextualizationConfig` (unknown, 必須)

---

### DeleteContextualizedPointCloudVolumesBody

**プロパティ**:

- `items` (unknown, 必須)
- `dmsContextualizationConfig` (unknown, 必須)

---

### DeleteContextualizedPointCloudVolumesRCBody

**プロパティ**:

- `items` (unknown, 必須)
- `dmsContextualizationConfig` (unknown, 必須)

---

### DeleteImage360AnnotationContextualizationItem

**プロパティ**:

- `asset` (unknown, 必須)
- `image360` (unknown, 必須)

---

### DeleteImage360AnnotationContextualizationList

---

### DeleteJobsRequest

**プロパティ**:

- `items` (array, 必須)

---

### DeletePointCloudVolumeContextualizationItem

**プロパティ**:

- `assetInstanceId` (unknown, 必須)
- `volumeReference` (string, 必須)
  - A unique reference for the volume within a revision.

---

### DeletePointCloudVolumeContextualizationItemRC

**プロパティ**:

- `asset` (unknown, 必須)
- `volumeReference` (string, 必須)
  - A unique reference for the volume within a revision.

---

### DeletePointCloudVolumeContextualizationList

---

### DeletePointCloudVolumeContextualizationRCList

---

### DeleteRawDB

**プロパティ**:

- `items` (array, 任意)
- `recursive` (boolean, 任意)
  - When true, tables of this database are deleted with the database.

---

### DeleteRecordsRequest

**プロパティ**:

- `items` (array, 必須)
  - List of records to delete.


---

### DeleteRequest

---

### DeleteUserHistoryResourcesRequest

**プロパティ**:

- `items` (array, 必須)

---

### DeletedInstances

List of deleted instances

---

### DeletionReport

**プロパティ**:

- `projectId` (integer, 必須)
  - Project id of the report
- `resource` (string, 必須)
  - Resource identifier
- `state` (unknown, 必須)
- `failure` (string, 任意)
  - Optional failure reason
- `createdTime` (integer, 必須)
  - milliseconds since epoch

---

### DeletionReportRequest

**プロパティ**:

- `projectId` (integer, 必須)
  - Project id of the report
- `resource` (string, 必須)
  - Resource identifier
- `state` (unknown, 必須)
- `failure` (string, 任意)
  - Optional failure reason

---

### DeletionReportRequestList

**プロパティ**:

- `items` (array, 必須)

---

### DeletionReportsResponse

Deletion Report

**プロパティ**:

- `items` (array, 必須)

---

### DeletionReportsWithCursorResponse

A list of objects along with possible cursors to get the next page of results

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
  - Cursor to get the next page of results (if available).

---

### DescriptionUpdateField

Set a new value for description.

**プロパティ**:

- `set` (string, 任意)
  - Description of Extraction Pipeline

---

### Destination

**プロパティ**:

- `externalId` (unknown, 必須)
- `sessionId` (integer, 任意)
  - ID of the session tied to this destination.
- `targetDataSetId` (integer, 任意)
  - Data set ID the created items are inserted into, if applicable.
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)

---

### DestinationUpdateItem

**プロパティ**:

- `externalId` (unknown, 必須)
- `update` (unknown, 必須)

---

### DetachSessionRequest

**プロパティ**:

- `items` (array, 任意)

---

### DetachSessionResponse

**プロパティ**:

- `items` (array, 任意)

---

### DetectTagsResourceFilter

**プロパティ**:

- `Asset` (object, 任意)
- `File` (object, 任意)

---

### DetectTagsResourceType

Type of resources loaded for tag detection.

---

### DiagramAnnotation

Annotation representing a detected entity.

---

### DiagramConvertConfig

**プロパティ**:

- `grayscale` (unknown, 任意)

---

### DiagramConvertRequestSchema

An array of files and annotations to create interactive diagrams.

---

### DiagramConvertResultSchema

An array of converted results, returned when the job finished or failed partially.

---

### DiagramDetectBetaConfig

Configuration for diagram detect that is only available in beta.

**プロパティ**:

- `annotationExtract` (boolean, 任意)
  - Read SHX text embedded in the diagram file. If present, this text will override overlapping OCR text. Cannot be used at the same time as read_embedded_text.
- `caseSensitive` (boolean, 任意)
  - Case sensitive text matching.
- `connectionFlags` (array, 任意)
  - Connection flags for token graph. Two flags are supported thus far: `no_text_inbetween` and `natural_reading_order`.
- `customizeFuzziness` (unknown, 任意)
- `directionDelta` (number, 任意)
  - Maximum angle between the direction of two text boxes for them to be connected. Directions are currently multiples of 90 degrees.
- `directionWeights` (unknown, 任意)
- `minFuzzyScore` (number, 任意)
  - For each detection, this controls to which degree characters can be replaced from the OCR text with similar characters, e.g. I and 1. A value of 1 will disable character replacements entirely. See also 'substitutions' for the characters that may be replaced.
- `readEmbeddedText` (boolean, 任意)
  - Read text embedded in the PDF file. If present, this text will override overlapping OCR text.
- `removeLeadingZeros` (boolean, 任意)
  - Disregard leading zeroes when matching tags (e.g. "A0001" will match "A1").
- `substitutions` (object, 任意)
  - Override the default mapping of characters to an array of allowed substitute characters. The default mapping contains characters commonly confused by OCR. Provide your custom mapping in the format like so: `{"0": ["O", "Q"], "1": ["l", "I"]}`. This means: `0` (zero) is allowed to be replaced by uppercase letter `O` or `Q`, and `1` (one) is allowed to be replaced by lowercase letter `l` or uppercase letter `I`. No other replacements are allowed.

The substitutions are performed on the raw OCR text. For example, if we want to match a tag "T10" and OCR recognized the text as "TI0", mapping `{"I": ["1"]}` would allow the match. This substitution would happen given the default mapping. On the contrary, if the OCR accuracy is high, you may want to disable such substitutions to reduce false positive matches by overriding the mapping with `{"I": ["I"]}`.

Currently the default substitution mapping is as follows (it may be updated without notice):
```
{
    "0": ["C", "D", "O"],
    "1": ["T", "I"],
    "3": ["B"],
    "6": ["G"],
    "8": ["B"],
    "Ä": ["A"],
    "Ã": ["A"],
    "Å": ["A"],
    "B": ["8", "3"],
    "C": ["0"],
    "D": ["1)", "O", "0"],
    "G": ["6"],
    "I": ["T", "1", "l"],
    "Í": ["I", "T", "l", "1"],
    "K": ["|X"],
    "O": ["C", "D", "0"],
    "Q": ["0", "O"],
    "T": ["1", "I"],
    "Ø": ["0"],
    "l": ["I"],
    "p": ["P", "0"],
    "v": ["V"],
    "ø": ["0", "Ø"],
    "?": ["2"],
    "×": ["x", "X"]
}
```

---

### DiagramDetectConfig

**プロパティ**:

- `searchField` (unknown, 任意)
- `partialMatch` (unknown, 任意)
- `minTokens` (unknown, 任意)

---

### DiagramDetectEntities

A list of entities to look for. For example, all the assets under a root node. The `searchField` determines the strings that identify the entities.

---

### DiagramDetectResultSchema

An array of detected results, returned when the job finished or failed partially.

---

### DiagramDetectedEntities

A list of entities detected per annotation.

---

### DiagramDto

**プロパティ**:

- `createdTime` (unknown, 必須)
- `externalId` (unknown, 必須)
  - The external ID of the diagram
- `fileId` (unknown, 必須)
  - DMS identifier of a file the diagram is parsed from
- `lastUpdatedTime` (unknown, 必須)
- `libraryId` (unknown, 必須)
  - The external ID of a library the diagram is parsed with
- `message` (string, 任意)
  - Message containing errors or other information
- `pageNumber` (integer, 必須)
  - The page number of a file the diagram is parsed from
- `status` (unknown, 必須)
  - Parsing status of a diagram

---

### DiagramFileExternalId

The external ID of a file in CDF. The file must have mime_type application/pdf, image/jpeg, image/png or image/tiff.

---

### DiagramFileId

The ID of a file in CDF. The file must have mime_type application/pdf, image/jpeg, image/png or image/tiff.

---

### DiagramFilter

List of properties used to filter diagrams

**プロパティ**:

- `fileId` (array, 任意)
  - DMS identifier of a file the diagram is parsed from
- `libraryId` (array, 任意)
  - The external ID of the library the diagram is parsed with
- `pageNumber` (array, 任意)
  - The page number of a file the diagram is parsed from
- `status` (array, 任意)
  - Parsing status of a diagram

---

### DiagramFilters

List of filters used to filter diagrams

---

### DiagramInstanceId

The instance id of a file in CDF. The file must have mime_type application/pdf, image/jpeg, image/png or image/tiff.

**プロパティ**:

- `space` (unknown, 必須)
- `externalId` (unknown, 必須)

---

### DiagramMinTokens

Each detected item must match the detected entity on at least this number of tokens. A token is a substring of consecutive letters or digits.

---

### DiagramOcrAnnotationSchema

An annotation describing a detected text string with location.

**プロパティ**:

- `text` (string, 任意)
  - Detected text.
- `boundingBox` (object, 任意)
- `confidence` (unknown, 任意)
  - A number indicating the confidence in the detected text as determined by the ocr provider.
- `direction` (number, 任意)
  - The angle of the text direction. 0 means horizontal left to right, 90 means vertical downwards.

---

### DiagramOcrBoundingBoxSchema

A normalized bounding box describing a rectangular region aligned with the x-y-axes.

**プロパティ**:

- `xMin` (unknown, 任意)
  - The lowest value of x within the box.
- `yMin` (unknown, 任意)
  - The lowest value of y within the box.
- `xMax` (unknown, 任意)
  - The highest value of x within the box.
- `yMax` (unknown, 任意)
  - The highest value of y within the box.

---

### DiagramOcrPageSchema

Contains OCR data for a single page.

**プロパティ**:

- `height` (number, 任意)
  - The height of the page in pixels.
- `width` (number, 任意)
  - The width of the page in pixels.
- `annotations` (array, 任意)
  - Annotations representing detected text with location.
- `page` (integer, 任意)
  - The page number of the page.
- `updatedTimestamp` (integer, 任意)
  - The time in milliseconds since epoch when the ocr result was last updated.

---

### DiagramOcrRequestSchema

File id and page range to get ocr result for.

**プロパティ**:

- `fileId` (integer, 必須)
  - File id to get ocr results for.
- `startPage` (integer, 任意)
  - The first page in the range to get ocr results from.
- `limit` (integer, 任意)
  - The maximum number of pages to get results for. With a limit of 10 and start page of 1, results for pages 1-10 are returned if they are available.

---

### DiagramOcrResponseSchema

A list of ocr results by page for a single file.

---

### DiagramParseInput

**プロパティ**:

- `documents` (array, 必須)
- `libraryId` (unknown, 必須)
  - The external ID of a library for parsing
- `nonce` (string, 必須)
  - Nonce parameter

---

### DiagramPartialMatch

Allow partial (fuzzy) matching of entities in the engineering diagrams. Creates a match only when it is possible to do so unambiguously.

---

### DiagramRegion

Shape and coordinates of the detected entity in the image.

**プロパティ**:

- `shape` (string, 必須)
  - The geometrical shape of the image region to which a detected entity belongs.
- `vertices` (array, 必須)
  - List of vertices representing the image region to which a detected entity belongs.

---

### DiagramSearchField

This field determines the string to search for and to identify object entities.

---

### DiagramSvgPngResultSchema

---

### DialGaugeDetection

Detect and read value of dial gauges in images. In beta. Available only when the `cdf-version: beta` header is provided.

---

### DialGaugeDetectionParameters

Parameters for dial gauge detection and reading. In beta. Available only when the `cdf-version: beta` header is provided.

**プロパティ**:

- `minLevel` (unknown, 任意)
- `maxLevel` (unknown, 任意)
- `deadAngle` (unknown, 任意)
- `nonLinAngle` (unknown, 任意)
- `threshold` (unknown, 任意)

---

### DictionaryUpdate

**プロパティ**:

- `set` (object, 必須)
  - Value to set

---

### DigitalGaugeDetection

Detect and read value of digital gauges in images. In beta. Available only when the `cdf-version: beta` header is provided.

---

### DigitalGaugeDetectionParameters

Parameters for digital gauge detection and reading. In beta. Available only when the `cdf-version: beta` header is provided.

**プロパティ**:

- `commaPos` (unknown, 任意)
- `minNumDigits` (unknown, 任意)
- `maxNumDigits` (unknown, 任意)
- `threshold` (unknown, 任意)

---

### DirectNodeRelation

Direct node relation

**プロパティ**:

- `type` (string, 必須)
- `container` (unknown, 任意)
  - The (optional) required type for the node the direct relation points to. If specified, the node must exist before the direct relation is referenced and of the specified type. If no container specification is used, the node will be auto created with the built-in ```node``` container type, and it does not explicitly have to be created before the node that references it.
- `list` (boolean, 任意)
  - Specifies that the data type is a list of values. The ordering of values is preserved.

- `maxListSize` (integer, 任意)
  - Specifies the maximum number of values in the list

---

### DirectRelationImprovementSuggestion

**プロパティ**:

- `type` (string, 任意)
- `space` (unknown, 任意)
- `originExternalId` (unknown, 任意)
- `propertyName` (string, 任意)
  - The name of the property

---

### DirectRelationReference

Reference to the node pointed to by the direct relation. The reference consists of a space and an external-id.

**プロパティ**:

- `space` (unknown, 必須)
- `externalId` (unknown, 必須)

---

### DirectRelationTarget

**プロパティ**:

- `type` (string, 必須)
- `space` (unknown, 必須)
- `viewExternalId` (unknown, 必須)
- `viewVersion` (unknown, 必須)
- `propertyName` (string, 必須)
  - The name of the property that holds the direct relation

---

### DirectionWeights

Direction weights that control how far subsequent ocr text boxes can be from another in a particular direction and still be combined into the same detection. Lower value means larger distance is allowed. The direction is relative to the text orientation.

**プロパティ**:

- `left` (number, 任意)
- `right` (number, 任意)
- `up` (number, 任意)
- `down` (number, 任意)

---

### DmsCADContextualizationConfig

**プロパティ**:

- `object3DSpace` (unknown, 必須)
- `cadNodeSpace` (unknown, 必須)

---

### DmsContextualizationConfig

**プロパティ**:

- `object3DSpace` (unknown, 必須)
- `contextualizationSpace` (unknown, 必須)
- `revisionInstanceId` (unknown, 必須)

---

### DmsContextualizationConfigRC

**プロパティ**:

- `object3DSpace` (unknown, 必須)
- `contextualizationSpace` (unknown, 必須)
- `revision` (unknown, 必須)

---

### DmsId

A DMS Identifier using the space and externalId

**プロパティ**:

- `space` (string, 必須)
- `externalId` (unknown, 必須)

---

### DmsInstanceId

**プロパティ**:

- `space` (unknown, 必須)
- `externalId` (unknown, 必須)

---

### Document

A document

**プロパティ**:

- `id` (unknown, 必須)
  - The unique identifier for the document. This is automatically generated by CDF, and will be the same as the corresponding value in the Files API.
- `externalId` (unknown, 任意)
  - The external ID for the document. This field will be the same as the value set in the Files API.
- `instanceId` (unknown, 任意)
  - The instance ID for documents created through Data Modeling. This field will be the same as the value set in the Files API.
- `title` (string, 任意)
  - The title of the document
- `author` (string, 任意)
  - The author of the document
- `producer` (string, 任意)
  - The producer of the document. Many document types contain metadata indicating what software or system was used to create the document.
- `createdTime` (unknown, 必須)
  - When the document was created, measured in milliseconds since 00:00:00 Thursday, 1 January 1970. We do a best effort to determine the created time for the document, and it will be derived from either the document metadata, the user-specified created time provided when uploading the file or as a last resort the creation timestamp of the underlying file resource.
- `modifiedTime` (unknown, 任意)
  - When the document was last modified, measured in milliseconds since 00:00:00 Thursday, 1 January 1970. This holdes the user-specified modified time provided for the underlying file resource, but might in the future also be derived from document metadata.
- `lastIndexedTime` (unknown, 任意)
  - When the document was last indexed in the documents search engine, measured in milliseconds since 00:00:00 Thursday, 1 January 1970.
- `mimeType` (string, 任意)
  - Detected mime type for the document
- `extension` (string, 任意)
  - Extension of the file (always in lowercase)
- `pageCount` (integer, 任意)
  - Number of pages for multi-page documents
- `type` (string, 任意)
  - Detected type of document
- `language` (string, 任意)
  - The detected language used in the document
- `truncatedContent` (string, 任意)
  - The textual content of the document. Truncated to 155 characters but subject to change
- `assetIds` (array, 任意)
  - The ids of any assets referred to in the document
- `labels` (unknown, 任意)
- `sourceFile` (unknown, 必須)
- `geoLocation` (unknown, 任意)

---

### DocumentAggregateFilter

A JSON based filtering language. See detailed documentation above.


---

### DocumentAggregateFilterBool

A query that matches items matching boolean combinations of other queries.
It is built using one or more boolean clauses, which can be of types: `and`, `or` or `not`


---

### DocumentAggregateFilterLeaf

Leaf filter

---

### DocumentAggregateFilterPrefix

**プロパティ**:

- `prefix` (object, 必須)
  - Matches items that contain a specific prefix in the provided property.

---

### DocumentAggregateValue

---

### DocumentCellElementWithElementGranularity

---

### DocumentCellElementWithLineGranularity

A cell in a table. A cell contains zero or more lines of text.

**プロパティ**:

- `lines` (array, 必須)

---

### DocumentCellElementWithWordGranularity

A cell in a table. A cell contains zero or more lines of text.

**プロパティ**:

- `lines` (array, 必須)

---

### DocumentContentExternalId

**プロパティ**:

- `externalId` (unknown, 必須)

---

### DocumentContentInstanceId

**プロパティ**:

- `instanceId` (unknown, 必須)

---

### DocumentContentInternalId

**プロパティ**:

- `id` (unknown, 必須)

---

### DocumentContentRequest

---

### DocumentCursor

**プロパティ**:

- `cursor` (string, 任意)
  - Cursor for paging through results.

---

### DocumentElementWithElementGranularity

A single document layout element. Can be a title, a paragraph or a table.

---

### DocumentElementWithLineGranularity

A single document layout element. Can be a title, a paragraph or a table.

---

### DocumentElementWithWordGranularity

A single document layout element. Can be a title, a paragraph or a table.

---

### DocumentElements

---

### DocumentElementsRequest

**プロパティ**:

- `granularity` (string, 任意)
  - Adjust the level of detail in the response.

---

### DocumentExternalId

**プロパティ**:

- `externalId` (string, 必須)
  - The external ID for the document. The value matches the externalId set in the Files API.

---

### DocumentFilter

A JSON based filtering language. See detailed documentation above.


---

### DocumentFilterBool

A query that matches items matching boolean combinations of other queries.
It is built using one or more boolean clauses, which can be of types: `and`, `or` or `not`


---

### DocumentFilterContainsAll

**プロパティ**:

- `containsAll` (object, 必須)
  - Matches items where the property contains all the given values

---

### DocumentFilterContainsAny

**プロパティ**:

- `containsAny` (object, 必須)
  - Matches items where the property contains one or more of the given values

---

### DocumentFilterEquals

**プロパティ**:

- `equals` (object, 必須)
  - Matches items that contain the exact value in the provided property.

---

### DocumentFilterExists

**プロパティ**:

- `exists` (object, 必須)
  - Matches items that contain a value for the provided property.

---

### DocumentFilterGeoJsonDisjoint

**プロパティ**:

- `geojsonDisjoint` (object, 必須)
  - Matches items with geolocations that are disjoint from the provided geometry

---

### DocumentFilterGeoJsonIntersects

**プロパティ**:

- `geojsonIntersects` (object, 必須)
  - Matches items with geolocations that intersect the provided geometry

---

### DocumentFilterGeoJsonWithin

**プロパティ**:

- `geojsonWithin` (object, 必須)
  - Matches items with geolocations that are within the provided geometry

---

### DocumentFilterIn

**プロパティ**:

- `in` (object, 必須)
  - Matches items where the property matches one of the given values

---

### DocumentFilterInAssetSubtree

**プロパティ**:

- `inAssetSubtree` (object, 必須)
  - Matches items where the property contains one or more assets in a subtree rooted at any of the given values

---

### DocumentFilterLeaf

Leaf filter

---

### DocumentFilterLexicalSearch

**プロパティ**:

- `lexicalSearch` (object, 必須)
  - Matches passages that contains specified keywords.

---

### DocumentFilterPrefix

**プロパティ**:

- `prefix` (object, 必須)
  - Matches items that contain a specific prefix in the provided property.

---

### DocumentFilterProperty

Property you wish to filter. It's a list of strings to allow specifying nested properties.
For example, If you have the object `{"foo": {"../bar": "baz"}, "bar": 123}`, you can refer to the nested property as `["foo", "../bar"]` and the un-nested one as `["bar"]`.


---

### DocumentFilterRange

**プロパティ**:

- `range` (object, 必須)
  - Matches items that contain terms within the provided range.
Range must include both an upper and a lower bound. It is not allowed to specify both inclusive and exclusive
bounds (like `gte`, `gt`) together.
`gte`: Greater than or equal to.
`gt`: Greater than.
`lte`: Less than or equal to.
`lt`: Less than.


---

### DocumentFilterRangeValue

Value you wish to find in the provided property using a range clause.

---

### DocumentFilterSearch

**プロパティ**:

- `search` (object, 必須)
  - Matches items that contains the search query.

---

### DocumentFilterSemanticSearch

**プロパティ**:

- `semanticSearch` (object, 必須)
  - Matches passages that have similar semantic meaning as the search query.

---

### DocumentFilterValue

Value you wish to find in the provided property.

---

### DocumentFilterValueList

One or more values you wish to find in the provided property.

---

### DocumentGeoJsonGeometry

GeoJSON Geometry.

**プロパティ**:

- `type` (string, 必須)
  - Type of the GeoJSON Geometry. When filtering there is a limit of specifying up to 100 positions in the data.
- `coordinates` (unknown, 任意)
  - Coordinates of the geometry.
- `geometries` (array, 任意)
  - List of geometries for a GeometryCollection. Nested GeometryCollection is not supported

---

### DocumentHighlight

Highlighted snippets from name and content fields which show where the query matches are. The matched terms will be placed inside <em> tags

**プロパティ**:

- `name` (array, 必須)
  - Matches in name.
- `content` (array, 必須)
  - Matches in content.

---

### DocumentIdentifier

**プロパティ**:

- `fileId` (unknown, 必須)
  - DMS identifier of a file to be parsed
- `pageNumber` (integer, 必須)
  - Page number of the file to be parsed

---

### DocumentInstanceId

**プロパティ**:

- `instanceId` (unknown, 必須)
  - The data modeling instance ID for the document.

---

### DocumentInternalId

**プロパティ**:

- `id` (integer, 必須)
  - The file ID for the document.

---

### DocumentLeafElement

**プロパティ**:

- `text` (string, 必須)
  - The text contained in this element.
- `page` (integer, 必須)
  - The page number where the element can be found. Pages start at 1.
- `left` (number, 必須)
- `right` (number, 必須)
- `top` (number, 必須)
- `bottom` (number, 必須)

---

### DocumentLineElementWithLineGranularity

---

### DocumentLineElementWithWordGranularity

A single line containing a list of words

**プロパティ**:

- `words` (array, 必須)

---

### DocumentListElementWithElementGranularity

---

### DocumentListElementWithLineGranularity

An ordered or unordered list.

**プロパティ**:

- `type` (string, 必須)
- `lines` (array, 必須)

---

### DocumentListElementWithWordGranularity

An ordered or unordered list.

**プロパティ**:

- `type` (string, 必須)
- `lines` (array, 必須)

---

### DocumentListFilter

Filter with exact match

**プロパティ**:

- `filter` (unknown, 任意)

---

### DocumentListLimit

**プロパティ**:

- `limit` (integer, 任意)
  - Maximum number of items per page. Use the cursor to get more pages.

---

### DocumentListRequest

---

### DocumentParagraphElementWithElementGranularity

---

### DocumentParagraphElementWithLineGranularity

A paragraph in a document. A paragraph can consist of one or more lines of text.

**プロパティ**:

- `type` (string, 必須)
- `lines` (array, 必須)

---

### DocumentParagraphElementWithWordGranularity

A paragraph in a document. A paragraph can consist of one or more lines of text.

**プロパティ**:

- `type` (string, 必須)
- `lines` (array, 必須)

---

### DocumentParserByJobIdsRequest

**プロパティ**:

- `items` (array, 必須)

---

### DocumentParserFileExternalId

The external ID of a file in CDF.

---

### DocumentParserFileId

The ID of a file in CDF.

---

### DocumentParserFileReference

An external-id or instance-id reference to the referenced file.

---

### DocumentParserFilter

**プロパティ**:

- `validationStatus` (unknown, 任意)

---

### DocumentParserInstanceId

The instance id of a file in CDF.

**プロパティ**:

- `space` (unknown, 必須)
- `externalId` (unknown, 必須)

---

### DocumentParserJob

**プロパティ**:

- `jobId` (unknown, 必須)
- `createdTimestamp` (unknown, 必須)
- `updatedTimestamp` (unknown, 必須)
- `viewConfig` (unknown, 必須)
- `node` (unknown, 必須)
- `status` (unknown, 必須)
- `files` (array, 必須)
- `result` (unknown, 必須)
- `errorMessage` (string, 任意)
  - Normalized error message if the job failed.

---

### DocumentParserListRequest

**プロパティ**:

- `limit` (integer, 任意)
- `filter` (unknown, 任意)
- `cursor` (string, 任意)
  - Cursor for paging through results.

---

### DocumentParserListResponse

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
  - Cursor to get the next page of results (if available).

---

### DocumentParserPageRange

An inclusive range of up to 10 pages, starting at 1. For example, the first 10 pages are given by begin=1, end=10. Page ranges only apply to PDF files.

**プロパティ**:

- `begin` (integer, 必須)
  - The first page of the page range.
- `end` (integer, 必須)
  - The last page of the page range, must be greater than or equal to begin.

---

### DocumentParserResult

**プロパティ**:

- `resultSchema` (object, 任意)
  - A dictionary of extracted key-value pairs.
- `viewConfig` (unknown, 任意)
- `scores` (unknown, 任意)
- `rawResponses` (unknown, 任意)
- `data` (unknown, 任意)

---

### DocumentParserStartJobRequest

**プロパティ**:

- `viewConfig` (unknown, 必須)
- `files` (array, 必須)
- `node` (unknown, 任意)
- `useVision` (boolean, 任意)
  - Whether to use vision-based parsing.
- `userPrompt` (string, 任意)
  - An optional user prompt to guide the document parsing process.


---

### DocumentParserStartJobResponse

**プロパティ**:

- `createdTimestamp` (unknown, 必須)
- `jobId` (unknown, 必須)
- `status` (unknown, 必須)

---

### DocumentParserStatus

The status field containing information about the job status, view status, and validation status.


**プロパティ**:

- `job` (unknown, 任意)
- `view` (unknown, 任意)
- `validation` (unknown, 任意)

---

### DocumentParserUpdateRequest

**プロパティ**:

- `items` (array, 必須)

---

### DocumentParserUpdateRequestItem

**プロパティ**:

- `jobId` (unknown, 任意)
- `validationStatus` (unknown, 任意)
- `data` (unknown, 任意)

---

### DocumentParserUpdateResponse

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
  - Cursor to get the next page of results (if available).

---

### DocumentParserUpdateResponseItem

**プロパティ**:

- `jobId` (unknown, 任意)
- `updatedTimestamp` (unknown, 必須)
- `status` (unknown, 任意)

---

### DocumentParserWriteResultsRequest

**プロパティ**:

- `jobId` (unknown, 必須)
- `data` (unknown, 必須)
- `node` (unknown, 任意)

---

### DocumentParserWriteResultsResponse

**プロパティ**:

- `items` (array, 必須)

---

### DocumentPassageLocation

Insight about a search result

**プロパティ**:

- `pageNumber` (number, 任意)
- `left` (number, 任意)
- `right` (number, 任意)
- `up` (number, 任意)
- `bottom` (number, 任意)

---

### DocumentPassagesFilter

A JSON based filtering language. See detailed documentation above.


---

### DocumentPassagesFilterBool

A query that matches items matching boolean combinations of other queries.
Currently only supports `and` clause.


---

### DocumentPassagesFilterEquals

**プロパティ**:

- `equals` (object, 必須)
  - Matches items that contain the exact value in the provided property.

---

### DocumentPassagesFilterIn

**プロパティ**:

- `in` (object, 必須)
  - Matches items where the property matches one of the given values

---

### DocumentPassagesFilterLeaf

Leaf filter

---

### DocumentPassagesFilterValue

Value you wish to find in the provided property.

---

### DocumentPassagesFilterValueList

One or more values you wish to find in the provided property.

---

### DocumentPassagesSearchFilter

Narrow down search results. You must specify atleast one filter of type `semanticSearch`, `lexicalSearch` or both.

**プロパティ**:

- `filter` (unknown, 必須)

---

### DocumentPassagesSearchItem

Each item contains the semantic match and the relevant document it belongs to.

**プロパティ**:

- `text` (string, 必須)
  - The text representing the document passage that was related to your search query.
- `locations` (array, 必須)
- `document` (unknown, 必須)

---

### DocumentPassagesSearchLimit

**プロパティ**:

- `limit` (integer, 任意)
  - Maximum number of items.

---

### DocumentPassagesSearchPassageExpansion

**プロパティ**:

- `expansionStrategy` (unknown, 必須)
  - A expansion strategy to to increase the text view for each passage returned. Helpful to increase context for an LLM.

---

### DocumentPassagesSearchPassageExpansionSymmetric

**プロパティ**:

- `strategy` (string, 必須)
  - Expand the passage with adjacent passages that exists before and after a passage.
- `chunk_count` (integer, 必須)
  - Number of passages to expand a given passage by

---

### DocumentPassagesSearchRequest

---

### DocumentQARequest

**プロパティ**:

- `additionalContext` (string, 任意)
  - Optional additional context that the model can use to improve its answer
- `fileIds` (array, 必須)
  - A list of file IDs, external IDs, or instance IDs pointing to PDF documents
- `ignoreUnknownIds` (boolean, 任意)
  - If `true`, the API will not fail if any documents are missing or not fully processed, but generate an answer based on available documents.
- `language` (unknown, 任意)
  - The language in which the answer should be provided.
- `question` (string, 必須)
  - The question to ask about the documents.

---

### DocumentSearch

**プロパティ**:

- `search` (object, 任意)

---

### DocumentSearchAggregate

**プロパティ**:

- `name` (string, 必須)
  - User defined name for this aggregate
- `groups` (array, 必須)
- `total` (integer, 必須)
  - Total number of results for this aggregate

---

### DocumentSearchAggregateGroup

**プロパティ**:

- `group` (array, 必須)
- `count` (integer, 必須)
  - The number of documents in this group.

---

### DocumentSearchAggregateGroupIdentifier

**プロパティ**:

- `property` (unknown, 必須)
  - The property that is being aggregated on.
- `value` (unknown, 必須)
  - The value of the property for this group.

---

### DocumentSearchAggregates

**プロパティ**:

- `aggregates` (array, 任意)

---

### DocumentSearchCountAggregate

**プロパティ**:

- `name` (string, 必須)
  - User defined name for this aggregate
- `aggregate` (string, 必須)
  - count
- `groupBy` (array, 任意)
  - List of properties to group the count by. It's currently only possible to group by 0 or 1 properties. If grouping by 0 properties, the aggregate value is the total count of all documents.

---

### DocumentSearchCountAggregatesGroup

**プロパティ**:

- `property` (unknown, 必須)
  - A property to group by.

---

### DocumentSearchFilter

Filter with exact match

**プロパティ**:

- `filter` (unknown, 任意)

---

### DocumentSearchHighlight

**プロパティ**:

- `highlight` (boolean, 任意)
  - Whether or not matches in search results should be highlighted.

---

### DocumentSearchInAggregate

**プロパティ**:

- `search` (object, 任意)

---

### DocumentSearchItem

**プロパティ**:

- `highlight` (unknown, 任意)
- `item` (unknown, 必須)

---

### DocumentSearchLimit

**プロパティ**:

- `limit` (integer, 任意)
  - Maximum number of items. When using highlights the maximum value is reduced to 20.

---

### DocumentSearchRequest

---

### DocumentSemanticFilter

A JSON based filtering language. See detailed documentation above.


---

### DocumentSemanticFilterBool

A query that matches items matching boolean combinations of other queries.
Currently only supports `and` clause.


---

### DocumentSemanticFilterLeaf

Leaf filter

---

### DocumentSemanticMatch

Semantic insight about a search result

**プロパティ**:

- `text` (string, 必須)
- `locations` (array, 任意)

---

### DocumentSemanticSearchFilter

Narrow down search results. You must specify exactly one `semanticSearch` filter.

**プロパティ**:

- `filter` (unknown, 必須)

---

### DocumentSemanticSearchItem

Each item contains the semantic match and the relevant document it belongs to.

**プロパティ**:

- `match` (unknown, 必須)
- `item` (unknown, 必須)

---

### DocumentSemanticSearchLimit

**プロパティ**:

- `limit` (integer, 任意)
  - Maximum number of items.

---

### DocumentSemanticSearchPassageExpansion

**プロパティ**:

- `expansionStrategy` (unknown, 必須)
  - A expansion strategy to to increase the text view for each passage returned. Helpful to increase context for an LLM.

---

### DocumentSemanticSearchPassageExpansionSymmetric

**プロパティ**:

- `strategy` (string, 必須)
  - Expand the passage with adjacent passages that exists before and after a passage.
- `chunk_count` (integer, 必須)
  - Number of passages to expand a given passage by

---

### DocumentSemanticSearchRequest

---

### DocumentSemanticSearchVectorspace

Limit search to namespace

**プロパティ**:

- `namespace` (string, 必須)

---

### DocumentSort

**プロパティ**:

- `sort` (array, 任意)
  - List of properties to sort by. Currently only supports 1 property.

---

### DocumentSortItem

**プロパティ**:

- `order` (string, 任意)
- `property` (unknown, 必須)

---

### DocumentSourceFile

The source file that this document is derived from.

**プロパティ**:

- `name` (string, 必須)
  - Name of the file.
- `directory` (string, 任意)
  - The directory the file can be found in
- `source` (string, 任意)
  - The source of the file
- `mimeType` (string, 任意)
  - The mime type of the file
- `size` (number, 任意)
  - The size of the source file in bytes
- `hash` (string, 任意)
  - The hash of the source file. This is a SHA256 hash of the original file. The hash only covers the file content, and not other CDF metadata.
- `assetIds` (array, 任意)
  - The ids of the assets related to this file
- `labels` (unknown, 任意)
- `geoLocation` (unknown, 任意)
- `datasetId` (unknown, 任意)
  - The id if the dataset this file belongs to, if any
- `securityCategories` (array, 任意)
  - The security category IDs required to access this file
- `metadata` (object, 任意)

---

### DocumentStatus

**プロパティ**:

- `status` (string, 必須)
  - The status of this specific document collection or endpoint. The collection is fully synced with the files api once the status is `completed`. However, when a file is being updated (eg. re-uploaded), the older version of the Document may still be available for search or other read operations (see the available field).
- `available` (boolean, 必須)
  - Whether or not the collection/endpoint has available information. Note that this does not guarentee that the data is up to date, refer to `status` for the current progression.
- `reason` (string, 任意)
  - Provides additional insight when `status` is `failed`.

---

### DocumentStatusItem

Status information of different Documents collections.

**プロパティ**:

- `id` (unknown, 任意)
- `externalId` (unknown, 任意)
- `instanceId` (unknown, 任意)
- `passages` (unknown, 必須)
- `elements` (unknown, 必須)
- `content` (unknown, 必須)

---

### DocumentStatusItemOld

Each item contains the semantic match and the relevant document it belongs to.

**プロパティ**:

- `id` (unknown, 必須)
- `semanticSearch` (object, 必須)
  - Status of the document in the semantic search system.
- `elements` (object, 必須)
  - Status of the document layout analysis.
- `statistics` (object, 任意)
  - Statistics about the document. This is only available when setting `includeStatistics` to `true` in the request.

---

### DocumentStatusOldRequest

List of document ids to check the status for.

**プロパティ**:

- `items` (array, 必須)
- `includeStatistics` (boolean, 任意)
  - Whether or not to include statistics about the document. This gives additional insight such as the number of pages, number of vectors, etc.

---

### DocumentStatusRequest

---

### DocumentSummarizationRequest

**プロパティ**:

- `ignoreUnknownIds` (boolean, 任意)
  - If `true`, the API will not fail if any documents are missing, but summaries for the missing documents will be excluded from the response.
- `items` (array, 必須)
  - A list of documents to summarize.
- `language` (unknown, 任意)
  - The language in which the answer should be provided.

---

### DocumentSummarizationResponse

**プロパティ**:

- `items` (array, 必須)
  - A list of documents with summaries.

---

### DocumentSummarizationResponseItemExternalId

**プロパティ**:

- `externalId` (string, 必須)
  - The external ID for the document. The value matches the externalId set in the Files API.
- `summary` (string, 必須)
  - The summary of the document.

---

### DocumentSummarizationResponseItemInstanceId

**プロパティ**:

- `instanceId` (unknown, 必須)
  - The data modeling instance ID for the document.
- `summary` (string, 必須)
  - The summary of the document.

---

### DocumentSummarizationResponseItemInternalId

**プロパティ**:

- `id` (integer, 必須)
  - The file ID for the document.
- `summary` (string, 必須)
  - The summary of the document.

---

### DocumentTableElementWithElementGranularity

A table in a document. A table consists of a list of rows where each row is a list of
cells.

**プロパティ**:

- `type` (string, 必須)
- `columnHeaderCount` (number, 任意)
  - Number of header rows (column headers) at the top of the table.
- `rowHeaderCount` (number, 任意)
  - Number of header columns (row headers) at the left of the table.
- `rows` (array, 必須)

---

### DocumentTableElementWithLineGranularity

A table in a document. A table consists of a list of rows where each row is a list of
cells.

**プロパティ**:

- `type` (string, 必須)
- `columnHeaderCount` (number, 任意)
  - Number of header rows (column headers) at the top of the table.
- `rowHeaderCount` (number, 任意)
  - Number of header columns (row headers) at the left of the table.
- `rows` (array, 必須)

---

### DocumentTableElementWithWordGranularity

A table in a document. A table consists of a list of rows where each row is a list of
cells.

**プロパティ**:

- `type` (string, 必須)
- `columnHeaderCount` (number, 任意)
  - Number of header rows (column headers) at the top of the table.
- `rowHeaderCount` (number, 任意)
  - Number of header columns (row headers) at the left of the table.
- `rows` (array, 必須)

---

### DocumentTableOfContentsElementWithElementGranularity

---

### DocumentTableOfContentsElementWithLineGranularity

A table of contents.

**プロパティ**:

- `type` (string, 必須)
- `lines` (array, 必須)

---

### DocumentTableOfContentsElementWithWordGranularity

A table of contents.

**プロパティ**:

- `type` (string, 必須)
- `lines` (array, 必須)

---

### DocumentTitleElementWithElementGranularity

---

### DocumentTitleElementWithLineGranularity

A title in a document. A title can consist of one or more lines of text.

**プロパティ**:

- `type` (string, 必須)
- `level` (number, 任意)
  - The level of this header, from 1 to 3, where smaller numbers mean larger headers.
- `lines` (array, 必須)

---

### DocumentTitleElementWithWordGranularity

A title in a document. A title can consist of one or more lines of text.

**プロパティ**:

- `type` (string, 必須)
- `level` (number, 任意)
  - The level of this header, from 1 to 3, where smaller numbers mean larger headers.
- `lines` (array, 必須)

---

### DocumentWordElement

---

### DocumentationUpdateField

Set a new value for documentation.

**プロパティ**:

- `set` (string, 任意)
  - Documentation text field

---

### DocumentsAggregateAllUniquePropertiesItem

**プロパティ**:

- `count` (integer, 必須)
  - Number of properties with this name
- `values` (array, 必須)
  - A property name

---

### DocumentsAggregateAllUniquePropertiesRequest

Find all metadata property names

---

### DocumentsAggregateAllUniquePropertiesResponse

Response for the allUniqueProperties aggregate.

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
  - The cursor to get the next page of results (if available).

---

### DocumentsAggregateAllUniqueValuesItem

**プロパティ**:

- `count` (integer, 必須)
  - Number of items in this aggregation group.
- `values` (array, 必須)
  - A unique value found in the specified properties. Each item is a value for the specified property with same index.

---

### DocumentsAggregateAllUniqueValuesRequest

Paginated list of all unique values for given properties.

---

### DocumentsAggregateAllUniqueValuesResponse

Response for allUniqueValues aggregate.

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
  - The cursor to get the next page of results (if available).

---

### DocumentsAggregateCardinalityPropertiesItem

**プロパティ**:

- `count` (integer, 必須)
  - Number of items in this aggregation group.

---

### DocumentsAggregateCardinalityPropertiesRequest

Find approximate number of unique properties.

---

### DocumentsAggregateCardinalityPropertiesResponse

Response for cardinalityProperties aggregate.

**プロパティ**:

- `items` (array, 必須)

---

### DocumentsAggregateCardinalityValuesItem

**プロパティ**:

- `count` (integer, 必須)
  - Number of items in this aggregation group.

---

### DocumentsAggregateCardinalityValuesRequest

Find approximate number of unique values.

---

### DocumentsAggregateCardinalityValuesResponse

Response for cardinalityValues aggregate.

**プロパティ**:

- `items` (array, 必須)

---

### DocumentsAggregateCountItem

**プロパティ**:

- `count` (integer, 必須)
  - Number of items in this aggregation group.

---

### DocumentsAggregateCountRequest

Count of documents.

---

### DocumentsAggregateCountResponse

Response for count aggregate.

**プロパティ**:

- `items` (array, 必須)

---

### DocumentsAggregateUniquePropertiesItem

**プロパティ**:

- `count` (integer, 必須)
  - Number of properties with this name
- `values` (array, 必須)
  - A property name

---

### DocumentsAggregateUniquePropertiesRequest

Top unique metadata property names

---

### DocumentsAggregateUniquePropertiesResponse

Response for the uniqueProperties aggregate.

**プロパティ**:

- `items` (array, 必須)

---

### DocumentsAggregateUniqueValuesItem

**プロパティ**:

- `count` (integer, 必須)
  - Number of items in this aggregation group.
- `values` (array, 必須)
  - A unique value found in the specified properties. Each item is a value for the specified property with same index.

---

### DocumentsAggregateUniqueValuesRequest

Top unique values for given properties.

---

### DocumentsAggregateUniqueValuesResponse

Response for uniqueValues aggregate.

**プロパティ**:

- `items` (array, 必須)

---

### DocumentsLanguage

---

### DuplicatedIdsInRequestResponse

**プロパティ**:

- `error` (object, 必須)
  - Error details

---

### DynamicTaskOutput

**プロパティ**:

- `dynamicTasks` (array, 任意)

---

### DynamicTaskParameters

Dynamic tasks allow the dynamic creation of additional tasks during a workflow execution.

There are two things needed to execute a dynamic task.
- A list of task definitions (similar to the way tasks are defined in the workflow definition when creating a workflow version).
- A workflow task prior to the dynamic task that outputs the above list

Note: a dynamic task cannot start other dynamic tasks or subworkflows.


**プロパティ**:

- `dynamic` (object, 任意)
  - Tasks should reference a list of task definitions, defined exactly like they are during version creation.

---

### EdgeConnection

Describes the edge(s) that are likely to exist to aid in discovery and documentation of the view. A listed edge is not required. i.e. It does not have to exist when included in this list. A connection has a max distance of one hop.

**プロパティ**:

- `connectionType` (string, 任意)
  - The type of connection, either a single or multi edge connections are expected to exist.
- `name` (string, 任意)
  - Readable property name.
- `description` (string, 任意)
  - Description of the content and suggested use for this property.
- `type` (unknown, 必須)
- `source` (unknown, 必須)
  - The target node(s) of this connection can be read through the view specified in 'source'.
- `edgeSource` (unknown, 任意)
  - The edge(s) of this connection can be read through the view specified in 'edgeSource'.
- `direction` (string, 任意)

---

### EdgeDefinition

Edge

**プロパティ**:

- `instanceType` (string, 必須)
- `version` (integer, 必須)
  - Current version of the edge
- `type` (unknown, 必須)
  - Edge type
- `space` (unknown, 必須)
  - Id of the space the edge belongs to
- `externalId` (unknown, 必須)
  - Unique identifier for the edge. Can also be a null character.
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)
- `deletedTime` (unknown, 任意)
  - Timestamp when the edge was soft deleted. Note that deleted edges are filtered out of query results, but present in sync results. This means that this value will only be present in sync results.
- `properties` (object, 任意)
  - Spaces for the requested view and its containers
- `startNode` (unknown, 必須)
- `endNode` (unknown, 必須)

---

### EdgeOrNodeData

Property values for the identified/specified view or container

**プロパティ**:

- `source` (unknown, 必須)
- `properties` (unknown, 任意)

---

### EdgeType

Target type of the connection definition

**プロパティ**:

- `space` (string, 必須)
  - Space of the type
- `externalId` (string, 必須)
  - External ID of the type

---

### EdgeWrite

Edge to create or update

**プロパティ**:

- `instanceType` (string, 必須)
- `existingVersion` (integer, 任意)
  - Fail the ingestion request if the edge's version is greater than this value. If no existingVersion is specified, the ingestion will always overwrite any existing data for the edge (for the specified container or view). If existingVersion is set to 0, the upsert will behave as an insert, so it will fail the bulk if the item already exists. If skipOnVersionConflict is set on the ingestion request, then the item will be skipped instead of failing the ingestion request.
- `type` (unknown, 必須)
  - Edge type (direct relation)
- `space` (unknown, 必須)
  - Id of the space that the edge belongs to. This id cannot be updated.
- `externalId` (unknown, 必須)
  - Unique alphanumeric identifier for the edge
- `startNode` (unknown, 必須)
- `endNode` (unknown, 必須)
- `sources` (array, 任意)
  - Properties to write to in a view or container, for the edge.

---

### EitherId

---

### EmptyResponse

**プロパティ**:


---

### EncodingType

The type of encoding to convert from.
- `utf8`, UTF-8, default.
- `utf16`, UTF-16 big endian.
- `utf16le`, UTF-16 little endian.
- `latin1`, Latin 1, Specifically Windows CP 1252.

---

### EndTimeFilter

Either range between two timestamps or isNull filter condition.

---

### EndTimeIsNull

---

### EndTimeMinMax

---

### Endpoint

**プロパティ**:

- `externalId` (unknown, 必須)
- `endpointType` (string, 必須)
  - SAP OData endpoint type
- `instanceId` (string, 必須)
  - ID of the SAP instance the endpoint should connect to.
- `mappingId` (string, 任意)
  - ID of the schema mapping used by this endpoint to transform the writeback request.
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)

---

### EngineExecutionId

Additional UUIDv4 identifier for an execution. Useful for Cognite support to diagnose issues.

---

### EntitiesLookupById

Schema for fetching the list of entities byIds using externalIds

**プロパティ**:

- `items` (array, 必須)

---

### Entity

**プロパティ**:

- `externalId` (unknown, 必須)
- `visibility` (unknown, 任意)
- `data` (object, 任意)

---

### EntityCreateDraft

**プロパティ**:

- `annotationId` (unknown, 任意)
  - DMS identifier of a Diagram Annotation used to determine the asset to which the diagram entity is mapped
- `assetId` (unknown, 任意)
  - DMS identifier of the asset to which the diagram entity is mapped
- `diagramId` (unknown, 必須)
  - The external ID of a diagram the entity belongs to
- `externalId` (unknown, 必須)
  - The external ID of the diagram entity
- `isAssetVerified` (boolean, 必須)
  - Determines if a diagram entity has a verified asset mapping
- `isUserDetected` (boolean, 必須)
  - Determines if a diagram entity was detected manually
- `pathIds` (array, 必須)
- `symbolId` (unknown, 必須)
  - The external ID of the symbol this diagram entity is detected as

---

### EntityDto

**プロパティ**:

- `annotationId` (unknown, 任意)
  - DMS identifier of a linked Diagram Annotation
- `assetId` (unknown, 任意)
  - DMS identifier of a mapped asset
- `createdTime` (unknown, 必須)
- `diagramId` (unknown, 必須)
  - The external ID of a diagram this entity belongs to
- `externalId` (unknown, 必須)
  - The external ID of the diagram entity
- `isAssetVerified` (boolean, 必須)
  - Determines if a diagram entity has a verified asset mapping
- `isUserDetected` (boolean, 必須)
  - Determines if a diagram entity was detected manually
- `lastUpdatedTime` (unknown, 必須)
- `pathIds` (array, 必須)
- `symbolId` (unknown, 必須)
  - The external ID of the symbol this diagram entity is detected as

---

### EntityListFilter

---

### EntityListRequest

**プロパティ**:

- `filter` (unknown, 任意)
- `limit` (integer, 任意)
  - Maximum number of items that the client want to get back.

---

### EntityMatcherResponseSchema

---

### EntityMatchingFeatureSchema

**プロパティ**:

- `name` (unknown, 任意)
- `description` (unknown, 任意)
- `featureType` (unknown, 任意)
- `matchFields` (unknown, 任意)
- `ignoreMissingFields` (unknown, 任意)
  - If True, missing fields in `sources` or `targets` entities set in `matchFields`, are replaced with empty strings.
- `classifier` (unknown, 任意)
  - Name of the classifier used in the model, "Unsupervised" if unsupervised model.
- `originalId` (integer, 任意)
  - The ID of original model, only relevant when the model is a retrained model.

---

### EntityMatchingFilterSchema

**プロパティ**:

- `featureType` (unknown, 任意)
- `classifier` (unknown, 任意)
- `originalId` (integer, 任意)
  - The ID of original model, only relevant when the model is a retrained model.
- `name` (unknown, 任意)
- `description` (unknown, 任意)

---

### EntityMatchingPredictFeatureSchema

**プロパティ**:

- `sources` (unknown, 任意)
  - List of source entities to predict matches for, for example, time series. If omitted, will use `sources` from create and `assetsAcl:READ` capability will be required for the request.
- `targets` (unknown, 任意)
  - List of potential target entities to match to one or more of the source entities, for example, assets. If omitted, will use `targets` from create and `assetsAcl:READ` capability will be required for the request.
- `numMatches` (integer, 任意)
  - The maximum number of results to return for each source entity.
- `scoreThreshold` (number, 任意)
  - Only return matches with score above this threshold.

---

### EntityMatchingPredictSchema

---

### EntityMatchingRefitFeatureSchema

**プロパティ**:

- `newExternalId` (unknown, 任意)
  - ExternalId for the new refitted model provided by client. Must be unique within the project.
- `trueMatches` (unknown, 必須)
  - List of additional confirmed matches used to train the model. The new model uses a combination of this and trueMatches from the orginal model. If there are identical match-from ids, the pair from the original model is dropped.
- `sources` (unknown, 任意)
  - List of source entities, for example, time series. If omitted, will use data from fit.
- `targets` (unknown, 任意)
  - List of target entities, for example, assets. If omitted, will use data from fit.

---

### EntityMatchingRefitSchema

---

### EntityResponse

---

### EntityResponseByIds



**プロパティ**:

- `items` (array, 任意)

---

### EntityResponseList



**プロパティ**:

- `items` (array, 任意)

---

### EntityUpdateDraft

**プロパティ**:

- `annotationId` (unknown, 任意)
  - DMS identifier of a linked diagram annotation
- `assetId` (unknown, 任意)
  - DMS identifier of a mapped asset
- `isAssetVerified` (boolean, 任意)
  - Determines if a diagram entity has a verified asset mapping
- `isUserDetected` (boolean, 任意)
  - Determines if a diagram entity was detected manually

---

### EntityUpdateItem

**プロパティ**:

- `externalId` (unknown, 必須)
  - The external ID of the diagram entity
- `update` (unknown, 必須)
  - The properties to be updated

---

### EntityWithPathsDto

Diagram entity with its paths joined by pathIds

**プロパティ**:

- `annotationId` (unknown, 任意)
  - DMS identifier of a linked diagram annotation
- `assetId` (unknown, 任意)
  - DMS identifier of a mapped asset
- `createdTime` (unknown, 必須)
- `diagramId` (unknown, 必須)
  - The external ID of a diagram the entity belongs to
- `externalId` (unknown, 必須)
  - The external ID of the diagram entity
- `isAssetVerified` (boolean, 必須)
  - Determines if a diagram entity has a verified asset mapping
- `isUserDetected` (boolean, 必須)
  - Determines if a diagram entity was detected manually
- `lastUpdatedTime` (unknown, 必須)
- `paths` (array, 必須)
- `symbolId` (unknown, 必須)
  - The external ID of the symbol this diagram entity is detected as

---

### EnumProperty

An enum type property.

An enum property can only consist for predefined values.


**プロパティ**:

- `type` (string, 必須)
- `unknownValue` (string, 任意)
  - The value to use when the enum value is unknown.
This can optionally be used to provide forward-compatibility, Specifying what value to use if the client does not recognize the returned value. It is not possible to ingest the unknown value, but it must be part of the allowed values.
- `values` (object, 必須)
  - A set of all possible values for the enum property. The enum value identifier has to have a length of between 1 and 127 characters.  It must also match the pattern ```^[_A-Za-z][_0-9A-Za-z]{0,127}$```, and not be `"true"`, `"false"` or `"null"`.

Example: ```{ "value1": { "name": "Value 1", "description": "This is value 1" }, "value2": { } }```

---

### EnumValueProperties

Metadata of the enum value.

**プロパティ**:

- `name` (string, 任意)
  - Name of the enum value.
- `description` (string, 任意)
  - Description of the enum value.

---

### EpochTimestamp

The number of milliseconds since 00:00:00 Thursday, 1 January 1970, Coordinated Universal Time (UTC), minus leap seconds.

---

### EpochTimestampRange

Range between two timestamps (inclusive).

**プロパティ**:

- `max` (integer, 任意)
  - Maximum timestamp (inclusive). The timestamp is represented as number of milliseconds since 00:00:00 Thursday, 1 January 1970, Coordinated Universal Time (UTC), minus leap seconds.
- `min` (integer, 任意)
  - Minimum timestamp (inclusive). The timestamp is represented as number of milliseconds since 00:00:00 Thursday, 1 January 1970, Coordinated Universal Time (UTC), minus leap seconds.

---

### EqualsFilter

**プロパティ**:

- `equals` (object, 必須)
  - Matches items where the given property is **exactly** equal to the given value.


---

### EqualsFilterIla

**プロパティ**:

- `equals` (object, 必須)
  - Matches records that contain the exact value in the provided property.
Only apply this filter to properties containing a single value.


---

### EqualsFilterV3

**プロパティ**:

- `equals` (object, 必須)
  - Matches items that contain the exact value in the provided property.

---

### Error

Cognite API error.

**プロパティ**:

- `code` (integer, 必須)
  - HTTP status code.
- `message` (string, 必須)
  - Error message.
- `missing` (array, 任意)
  - List of lookup objects that do not match any results.
- `duplicated` (array, 任意)
  - List of objects that are not unique.

---

### ErrorBase

**プロパティ**:

- `error` (unknown, 必須)

---

### ErrorMessage

Error message returned by the model if it fails.

---

### EstimateQualityRequestSchema

**プロパティ**:

- `advancedJoinExternalId` (unknown, 必須)
- `dummyResponse` (boolean, 任意)
  - Whether to return a bogus response that complies with the expected schema.
This will be removed in a future iteration.
- `matcher` (unknown, 必須)

---

### EstimateQualityResponseSchema

---

### Event

---

### EventAdvancedFilter

A filter DSL (Domain Specific Language) to define advanced filter queries.

See more information about filtering DSL [here](https://docs.cognite.com/dev/concepts/resource_filtering_dsl/ "filtering DSL").

Supported properties:

  | Property                        | Type                                    |
  |---------------------------------|-----------------------------------------|
  | `["assetIds"]`                  | array of [number]                       |
  | `["createdTime"]`               | number                                  |
  | `["dataSetId"]`                 | number                                  |
  | `["endTime"]`                   | number                                  |
  | `["id"]`                        | number                                  |
  | `["lastUpdatedTime"]`           | number                                  |
  | `["startTime"]`                 | number                                  |
  | `["description"]`               | string                                  |
  | `["externalId"]`                | string                                  |
  | `["metadata"]`                  | string                                  |
  | `["metadata", "someCustomKey"]` | string                                  |
  | `["source"]`                    | string                                  |
  | `["subtype"]`                   | string                                  |
  | `["type"]`                      | string                                  |

  Note: Filtering on the `["metadata"]` property has the following logic:
  If a value of any metadata keys in an event matches the filter, the event matches the filter.

---

### EventAggregate

Aggregation group of events

**プロパティ**:

- `count` (integer, 必須)
  - Size of the aggregation group

---

### EventAggregateBoolFilter

A query that matches items matching boolean combinations of other queries.
It is built using one or more boolean clauses of the following types: `and`, `or`, or `not`.


---

### EventAggregateFilter

A filter DSL (Domain Specific Language) to define aggregate filter queries.

See more information about filtering DSL [here](https://docs.cognite.com/dev/concepts/resource_filtering_dsl/ "filtering DSL").


---

### EventAggregateLeafFilter

Aggregate leaf filter.


---

### EventAggregateProperties

**プロパティ**:

- `properties` (array, 必須)
  - The property name(s) to apply the aggregation on. Currently, limited to one property per request.


---

### EventAggregateRequest

Aggregation request of events. Filters behave the same way as for the `filter` endpoint. Default aggregation is `count`.

---

### EventBoolFilter

A query that matches items matching boolean combinations of other queries.
It's built using one or more boolean clauses of the following types: `and`, `or`, or `not`


---

### EventCardinalityPropertiesAggregate

---

### EventCardinalityValuesAggregate

---

### EventChange

---

### EventChangeByExternalId

---

### EventChangeById

---

### EventCountAggregate

Request aggregate to count the number of Events matching the filters. Default aggregate for the endpoint.

**プロパティ**:

- `aggregate` (string, 任意)
  - Type of aggregation to apply.
`count`: Get an approximate number of Events matching the filters.
- `advancedFilter` (unknown, 任意)
- `filter` (unknown, 任意)

---

### EventDataIds

---

### EventDescription

Textual description of the event.

---

### EventFilter

Filter on events filter with exact match

**プロパティ**:

- `startTime` (unknown, 任意)
- `endTime` (unknown, 任意)
- `activeAtTime` (unknown, 任意)
- `metadata` (unknown, 任意)
- `assetIds` (array, 任意)
  - Asset IDs of equipment that this event relates to.
- `assetExternalIds` (array, 任意)
  - Asset External IDs of equipment that this event relates to.
- `assetSubtreeIds` (array, 任意)
  - Only include events that have a related asset in a subtree rooted at any of these assetIds (including the roots given). If the total size of the given subtrees exceeds 100,000 assets, an error will be returned.
- `dataSetIds` (array, 任意)
- `source` (unknown, 任意)
- `type` (unknown, 任意)
- `subtype` (unknown, 任意)
- `createdTime` (unknown, 任意)
- `lastUpdatedTime` (unknown, 任意)
- `externalIdPrefix` (unknown, 任意)

---

### EventFilterRequest

---

### EventHubSource

**プロパティ**:

- `type` (string, 必須)
  - Source type.
- `externalId` (unknown, 必須)
- `host` (string, 必須)
  - URL of the event hub consumer endpoint.
- `eventHubName` (string, 必須)
  - Name of the event hub.
- `keyName` (string, 任意)
  - The name of the Event Hub key to use. This parameter is deprecated. Use `username` under basic authentication instead.
- `authentication` (object, 任意)
  - Authentication details for eventhub. Basic authentication uses a shared access key, where the `username` is the key name, and the `password` is the key value.
- `consumerGroup` (string, 任意)
  - The event hub consumer group to use. Microsoft recommends having a distinct consumer group for each application consuming data from event hub. If left out, this uses the default consumer group.
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)

---

### EventLeafFilter

Leaf filter.

---

### EventMetadata

Custom, application specific metadata. String key -> String value. Limits: Maximum length of key is 128 bytes, value 128000 bytes, up to 256 key-value pairs, of total size at most 200000.

---

### EventPatch

Changes will be applied to event.

**プロパティ**:

- `update` (object, 必須)

---

### EventResponse

---

### EventSearch

**プロパティ**:

- `description` (string, 任意)
  - text to search in description field across events

---

### EventSearchRequest

Filter on events filter with exact match

**プロパティ**:

- `filter` (unknown, 任意)
- `search` (unknown, 任意)
- `limit` (integer, 任意)
  - <- Limits the maximum number of results to be returned by single request. Request may contain less results than request limit.

---

### EventSource

The source of this event.

---

### EventSubType

SubType of the event, e.g 'electrical'.

---

### EventType

Type of the event, e.g 'failure'.

---

### EventUniquePropertiesAggregate

---

### EventWithCursorResponse

---

### EventWithPropertyCountAggregate

---

### ExcessiveTimeoutNotice

**プロパティ**:

- `code` (string, 必須)
- `category` (string, 必須)
- `level` (string, 必須)
- `hint` (string, 必須)
- `timeout` (integer, 必須)
  - The specified timeout for the query.

---

### ExecutionInput

Input data to the workflow. The content of the input data can be used as input to the workflow tasks using references. The input data should be in JSON format, and is limited to 100KB in size.

---

### ExistsFilter

**プロパティ**:

- `exists` (object, 必須)
  - Will match items that have a value in the specified property.


---

### ExistsFilterIla

**プロパティ**:

- `exists` (object, 必須)
  - Will match records that have a value in the specified property.


---

### ExtPipe

---

### ExtPipeExternalId

**プロパティ**:

- `externalId` (string, 必須)
  - External Id provided by client. Should be unique within the project.

---

### ExtPipeId

---

### ExtPipeInternalId

**プロパティ**:

- `id` (integer, 必須)
  - A server-generated ID for the object.

---

### ExtPipeNotificationConfiguration

**プロパティ**:

- `allowedNotSeenRangeInMinutes` (integer, 任意)
  - Notifications configuration value. Time in minutes to pass without any Run. Null if extraction pipeline is not checked.

---

### ExtPipeRunRequest

Status of the extraction pipeline.

**プロパティ**:

- `externalId` (string, 必須)
  - Extraction pipeline external Id provided by client. Should be unique within the project.
- `status` (unknown, 必須)
- `message` (string, 任意)
  - Error message.
- `createdTime` (integer, 任意)
  - The number of milliseconds since 00:00:00 Thursday, 1 January 1970, Coordinated Universal Time (UTC), minus leap seconds.

---

### ExtPipeRunResponse

Extraction Pipeline Run. Contains extraction pipeline status and message for a moment of time

**プロパティ**:

- `id` (integer, 任意)
  - A server-generated ID for the object.
- `status` (string, 必須)
  - Extraction Pipeline status.
- `message` (string, 任意)
  - Error message.
- `createdTime` (unknown, 任意)

---

### ExtPipeRunStatus

---

### ExtPipeUpdate

---

### ExtPipeUpdateByExternalId

**プロパティ**:

- `externalId` (string, 任意)
  - External Id provided by client. Should be unique within the project.
- `update` (unknown, 任意)

---

### ExtPipeUpdateById

**プロパティ**:

- `id` (integer, 任意)
  - A server-generated ID for the object.
- `update` (unknown, 任意)

---

### ExtPipeUpdateData

List of updates for Extraction Pipeline

**プロパティ**:

- `externalId` (unknown, 任意)
- `name` (unknown, 任意)
- `description` (unknown, 任意)
- `dataSetId` (unknown, 任意)
- `schedule` (unknown, 任意)
- `rawTables` (unknown, 任意)
- `contacts` (unknown, 任意)
- `metadata` (unknown, 任意)
- `source` (unknown, 任意)
- `documentation` (unknown, 任意)
- `notificationConfig` (unknown, 任意)

---

### ExtPipes

List of extraction pipelines

**プロパティ**:

- `items` (array, 任意)

---

### ExtPipesFilter

**プロパティ**:

- `externalIdPrefix` (string, 任意)
  - Filter by this (case-sensitive) prefix for the external ID.
- `name` (string, 任意)
  - Name of Extraction Pipeline
- `description` (string, 任意)
  - Description of Extraction Pipeline
- `dataSetIds` (array, 任意)
  - DataSetId list
- `schedule` (string, 任意)
  - Possible values: “On trigger”, “Continuous” or cron expression. If empty then “Null“
- `contacts` (array, 任意)
  - Contacts list.
- `rawTables` (array, 任意)
  - Raw tables
- `metadata` (object, 任意)
  - Custom, application specific metadata. String key -> String value. Limits: Key are at most 128 bytes. Values are at most 10240 bytes. Up to 256 key-value pairs. Total size is at most 10240.
- `source` (string, 任意)
  - Source for Extraction Pipeline
- `documentation` (string, 任意)
  - Documentation text field
- `createdBy` (string, 任意)
  - Extraction Pipeline creator. Usually user email is expected here
- `createdTime` (unknown, 任意)
- `lastUpdatedTime` (unknown, 任意)

---

### ExtPipesFilterRequest

**プロパティ**:

- `filter` (unknown, 任意)
- `limit` (integer, 任意)
  - Limits the number of results to return.
- `cursor` (string, 任意)

---

### ExtPipesWithCursor

List of extraction pipelines

**プロパティ**:

- `items` (array, 任意)
- `nextCursor` (string, 任意)
  - The cursor to get the next page of results (if available).

---

### ExtendLeaseResponse

**プロパティ**:

- `responseTimeoutSeconds` (integer, 必須)
  - The number of seconds granted to the worker to respond to the task after the lease extension. Responding to the task is done through the `update` or `extendlease` endpoints.
- `respondBefore` (unknown, 任意)
  - The timestamp representing the response deadline for the task after the lease extension. The worker must either update the task status or extend the task lease before this deadline.

---

### ExtendLeaseUpdateRequest

---

### ExtendedDiagramDto

Diagram with all of its entities and connections joined

**プロパティ**:

- `connections` (array, 必須)
- `createdTime` (unknown, 必須)
- `entities` (array, 必須)
  - List of entities of this diagram
- `externalId` (unknown, 必須)
  - The external ID of the diagram
- `fileId` (unknown, 必須)
  - ID of a file the diagram is parsed from
- `lastUpdatedTime` (unknown, 必須)
- `libraryId` (unknown, 必須)
  - The external ID of a library the diagram is parsed with
- `pageNumber` (integer, 必須)
  - The page number of a file the diagram is parsed from
- `message` (string, 任意)
  - Message containing errors or other information for a diagram
- `status` (unknown, 必須)
  - Parsing status of a diagram

---

### ExtendedDiagramWithPathsDto

Diagram with its entities, connections, and SVG paths data

**プロパティ**:

- `connections` (array, 必須)
- `createdTime` (unknown, 必須)
- `entities` (array, 必須)
- `externalId` (unknown, 必須)
  - The external ID of the diagram
- `fileId` (unknown, 必須)
  - ID of a file the diagram is parsed from
- `height` (string, 必須)
  - Height attribute of the SVG diagram
- `lastUpdatedTime` (unknown, 必須)
- `libraryId` (unknown, 必須)
  - The external ID of a library the diagram is parsed with
- `message` (string, 任意)
  - Message containing errors or other information
- `pageNumber` (integer, 必須)
  - The page number of a file the diagram is parsed from
- `status` (unknown, 必須)
  - Parsing status of a diagram
- `viewBox` (string, 必須)
  - ViewBox attribute of the SVG diagram
- `width` (string, 必須)
  - Width attribute of the SVG diagram

---

### ExtendedItemsRequest_ExtPipeId_

**プロパティ**:

- `items` (array, 必須)
- `ignoreUnknownIds` (boolean, 任意)
  - Ignore IDs and external IDs that are not found

---

### ExtendedLibraryDto

Library with all of its symbols and geometries joined

**プロパティ**:

- `createdTime` (unknown, 必須)
- `externalId` (unknown, 必須)
  - The external ID of the library
- `lastUpdatedTime` (unknown, 必須)
- `name` (string, 必須)
  - The name of the library
- `scope` (unknown, 必須)
  - Global or project scope where the library is available
- `symbols` (array, 必須)
  - List of symbols of this library

---

### ExtendedSymbolDto

Symbol with all of its geometries joined

**プロパティ**:

- `assetTypeId` (unknown, 必須)
- `createdTime` (unknown, 任意)
- `externalId` (unknown, 必須)
  - The external ID of the symbol
- `geometries` (array, 必須)
  - List of geometries of this symbol
- `lastUpdatedTime` (unknown, 必須)
- `libraryId` (unknown, 必須)
  - The external ID of a library the symbol belongs to

---

### ExternalAsset

A representation of a physical asset, for example a factory or a piece of equipment.

**プロパティ**:

- `externalId` (unknown, 任意)
- `name` (unknown, 必須)
- `parentId` (unknown, 任意)
  - The parent node's ID used to specify parent-child relationship.

You should not use this field in combination with the parentExternalId field.

- `parentExternalId` (unknown, 任意)
  - The parent node's external ID used to specify the parent-child relationship.
When specifying this field, the API will resolve the external ID into an internal ID and use the internal ID to store the parent-child relation.
As a result, a later change to update the parent's external ID will not affect this parent-child relationship as it is based on internal ID.

You should not use this field in combination with the parentId field.

- `description` (unknown, 任意)
- `dataSetId` (unknown, 任意)
  - The id of the dataset this asset belongs to.
- `metadata` (unknown, 任意)
- `source` (unknown, 任意)
- `labels` (unknown, 任意)
- `geoLocation` (unknown, 任意)

---

### ExternalEvent

An event represents something that happened at a given interval in time, e.g a failure, a work order, etc.

**プロパティ**:

- `externalId` (unknown, 任意)
- `dataSetId` (unknown, 任意)
  - The id of the dataset this event belongs to.
- `startTime` (unknown, 任意)
- `endTime` (unknown, 任意)
- `type` (unknown, 任意)
- `subtype` (unknown, 任意)
- `description` (unknown, 任意)
- `metadata` (unknown, 任意)
- `assetIds` (array, 任意)
  - Asset IDs of equipment that this event relates to.
- `source` (unknown, 任意)

---

### ExternalFilesMetadata

**プロパティ**:

- `externalId` (unknown, 任意)
- `name` (unknown, 必須)
- `directory` (unknown, 任意)
- `source` (unknown, 任意)
- `mimeType` (unknown, 任意)
- `metadata` (unknown, 任意)
- `assetIds` (array, 任意)
- `dataSetId` (unknown, 任意)
- `sourceCreatedTime` (unknown, 任意)
- `sourceModifiedTime` (unknown, 任意)
- `securityCategories` (array, 任意)
  - The security category IDs required to access this file.
- `labels` (unknown, 任意)
- `geoLocation` (unknown, 任意)

---

### ExternalGroupId

The ID of a group managed by the external identity provider

---

### ExternalId

**プロパティ**:

- `externalId` (unknown, 必須)

---

### ExternalIdPrefixFilter

filter external ids starting with the prefix specified

---

### ExternalIdRef

**プロパティ**:

- `externalId` (string, 必須)
  - String-based ID

---

### ExternalIdUpdateField

Set a new value for the externalId. Must be unique for the resource type.

**プロパティ**:

- `set` (string, 必須)
  - External Id provided by client. Should be unique within the project.

---

### ExternalIdWrapper

**プロパティ**:

- `externalId` (unknown, 必須)

---

### ExternalIdsAlreadyExistResponse

**プロパティ**:

- `error` (object, 必須)
  - Error details

---

### ExternalIdsRequest

**プロパティ**:

- `items` (array, 必須)

---

### ExternalLabelDefinition

A label definition is a globally defined label that can later be attached to resources (e.g., assets). For example, can you define a "Pump" label definition and attach that label to your pump assets.


**プロパティ**:

- `externalId` (unknown, 必須)
- `name` (string, 必須)
  - Name of the label.
- `description` (string, 任意)
  - Description of the label.
- `dataSetId` (unknown, 任意)
  - The id of the dataset this label belongs to.

---

### ExternalLabelDefinitionList

**プロパティ**:

- `items` (array, 必須)

---

### ExtraAuthParameter

Extra parameters to send to the external IdP

**プロパティ**:

- `name` (string, 必須)
  - The name of the parameter
- `value` (string, 必須)
  - The value of the parameter

---

### Extractor

An extractor instance

**プロパティ**:

- `externalId` (unknown, 必須)
- `name` (string, 必須)
  - Name of this extractor.
- `description` (string, 必須)
  - Short description of the extractor.
- `documentation` (string, 任意)
  - Longer markdown documentation.
- `imageUrl` (string, 任意)
  - Image URL for extractor logo.
- `latestVersion` (string, 任意)
  - The last released version of the extractor.
- `links` (array, 任意)
  - A list of external links.
- `tags` (array, 任意)
  - A list of tags, used for searching and filtering.
- `type` (string, 必須)
  - Type of extractor. "Global" means that it is globally available and maintained by cognite. "Community" means that it is unofficial and community maintained. "Unreleased" means that it is not yet publicly available and it is visible only as a prerelease or private beta. "Hosted" means it is a hosted extractor that runs as part of the CDF platform.

---

### ExtractorId

Identifier for an extractor

**プロパティ**:

- `externalId` (unknown, 必須)

---

### ExtractorsItemsWithIgnoreUnknownIds_ExternalId_

List of externalIds with optional ignore unknown ids

**プロパティ**:

- `ignoreUnknownIds` (boolean, 任意)
- `items` (array, 必須)

---

### FailedBatch

List of the items and the corresponding error message(s) per failed batch.

**プロパティ**:

- `errorMessage` (string, 任意)
  - The error message(s) of the failed batch.
- `items` (array, 任意)
  - List of the items in the failed batch.

---

### FdmTableOptions

Fdm table options

**プロパティ**:

- `space` (string, 必須)
- `externalId` (string, 必須)
- `version` (string, 任意)

---

### FdwUser

A user

**プロパティ**:

- `username` (unknown, 必須)
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 任意)
- `sessionId` (integer, 任意)
  - ID of the session tied to this user.

---

### FeatureParameters

Feature-specific parameters. New feature extractor parameters may appear.

**プロパティ**:

- `textDetectionParameters` (unknown, 任意)
- `assetTagDetectionParameters` (unknown, 任意)
- `peopleDetectionParameters` (unknown, 任意)
- `industrialObjectDetectionParameters` (unknown, 任意)
- `personalProtectiveEquipmentDetectionParameters` (unknown, 任意)
- `dialGaugeDetectionParameters` (unknown, 任意)
- `levelGaugeDetectionParameters` (unknown, 任意)
- `digitalGaugeDetectionParameters` (unknown, 任意)
- `valveDetectionParameters` (unknown, 任意)

---

### FeatureType

Each feature type defines the combination of features that will be created and used in the entity matcher model.

---

### FileChange

Changes will be applied to file.

**プロパティ**:

- `update` (object, 必須)

---

### FileChangeUpdate

---

### FileChangeUpdateByExternalId

---

### FileChangeUpdateById

---

### FileChangeUpdateByInstanceId

---

### FileDataIds

**プロパティ**:

- `items` (array, 任意)

---

### FileDataIdsWithIgnoreUnknownIds

---

### FileDataUploadIds

**プロパティ**:

- `items` (array, 必須)

---

### FileDeleteByIdEither

---

### FileDeleteByIds

---

### FileDirectory

Directory containing the file. Must be an absolute, unix-style path.

---

### FileExternalId

**プロパティ**:

- `externalId` (unknown, 任意)

---

### FileExternalIdEither

---

### FileFilter

Filter on files with exact match

**プロパティ**:

- `filter` (object, 任意)

---

### FileFilterRequest

---

### FileIdEither

---

### FileInstanceId

**プロパティ**:

- `instanceId` (unknown, 必須)

---

### FileInternalId

**プロパティ**:

- `id` (unknown, 任意)

---

### FileLimit

**プロパティ**:

- `limit` (integer, 任意)
  - <- Maximum number of items that the client want to get back.

---

### FileLink

**プロパティ**:

- `downloadUrl` (string, 任意)

---

### FileLinkIds

**プロパティ**:

- `items` (array, 任意)

---

### FileName

Name of the file.

---

### FileReference

An external-id or instance-id reference to the referenced file.

---

### FileReferenceWithPageRange

Either file id, external id or instance id, and optionally a page range. At most 50 pages can be queried.

**プロパティ**:

- `pageRange` (unknown, 任意)

---

### FileSelectByExternalId

**プロパティ**:

- `externalId` (unknown, 任意)

---

### FileSelectById

**プロパティ**:

- `id` (unknown, 任意)

---

### FileSelectByInstanceId

**プロパティ**:

- `instanceId` (unknown, 任意)

---

### FileSelectEither

---

### FileSource

The source of the file.

---

### FilesAggregate

Aggregation results for files

**プロパティ**:

- `count` (integer, 必須)
  - Number of filtered items included in aggregation

---

### FilesMetadata

---

### FilesMetadataField

Custom, application specific metadata. String key -> String value. Limits: Maximum length of key is 128 bytes, value 10240 bytes, up to 256 key-value pairs, of total size at most 10240.

---

### FilesSearchFilter

Filter on files with exact match

---

### Filter

**プロパティ**:

- `name` (string, 任意)
  - Filter on name.
- `unit` (string, 任意)
  - Filter on unit.
- `unitExternalId` (string, 任意)
  - Filter on unitExternalId.
- `unitQuantity` (string, 任意)
  - Filter on unitQuantity.
- `isString` (boolean, 任意)
  - Filter on isString.
- `isStep` (boolean, 任意)
  - Filter on isStep.
- `metadata` (unknown, 任意)
- `assetIds` (array, 任意)
  - Only includes time series that reference these specific asset IDs.
- `assetExternalIds` (array, 任意)
  - Asset External IDs of related equipment that this time series relates to.
- `rootAssetIds` (array, 任意)
  - Only includes time series that have a related asset in a tree rooted at any of these root `assetIds`.
- `assetSubtreeIds` (array, 任意)
  - Only includes time series that are related to an asset in a subtree rooted at any of these `assetIds` (including the roots given). If the total size of the given subtrees exceeds 100,000 assets, an error will be returned.
- `dataSetIds` (array, 任意)
  - Only includes time series that reference these specific data set IDs.
- `externalIdPrefix` (unknown, 任意)
- `createdTime` (unknown, 任意)
- `lastUpdatedTime` (unknown, 任意)

---

### FilterDefinition

A filter Domain Specific Language (DSL) used to create advanced filter queries.

---

### FilterDefinitionIla

A filter Domain Specific Language (DSL) used to create advanced filter queries.

**Note:** the max number of nodes in the filter tree is 100 and the max tree depth is 10.


---

### FilterMatchesBrokenCursorableIndexNotice

**プロパティ**:

- `code` (string, 必須)
- `category` (string, 必須)
- `level` (string, 必須)
- `hint` (string, 必須)
- `grade` (string, 必須)
- `sort` (array, 任意)
- `index` (unknown, 任意)
  - Identifier for the broken index
- `resultExpression` (string, 必須)
  - Identifier for the result set expression that the notice applies to.

---

### FilterMatchesCursorableSortNotice

**プロパティ**:

- `code` (string, 必須)
- `category` (string, 必須)
- `level` (string, 必須)
- `hint` (string, 必須)
- `grade` (string, 必須)
- `sort` (array, 任意)
- `resultExpression` (string, 必須)
  - Identifier for the result set expression that the notice applies to.

---

### FilterNodesByBoundingBox

**プロパティ**:

- `boundingBox` (unknown, 必須)
- `nodeIds` (array, 必須)

---

### FilterNodesByBoundingBoxRequest

**プロパティ**:

- `model` (unknown, 必須)
- `revision` (unknown, 必須)
- `filterNodesByBoundingBox` (unknown, 必須)

---

### FilterNodesByBoundingBoxResponse

---

### FilterProperty

Property you want to filter. Use a list of strings to specify nested properties.

<u>Example:</u>

You have the object
```
{
  "room": {
    "id": "b53"
  },
  "roomId": "a23"
}
```

Use `["room", "id"]` to return the value in the nested `id` property, which is a part of the `room` object.

You can also read the value(s) in the standalone property `roomId` with `["roomId"]`.


---

### FilterPropertyAllTopLevelIla

Property a client wants to use in the filter.
To represent fully qualified properties, Records use arrays of strings.
The filter supports container level properties and all top level properties
(`["space"]`, `["externalId"]`, `["createdTime"]` and `["lastUpdatedTime"]`).

So the property array has 1 or 3 segments.
1 segment property is top level property. And currently, `space`, `externalId`,
`createdTime` and `lastUpdatedTime` top level properties are supported in the filter.
3 segment property is container level property.
The property array for container level property has always 3 segments:
- the first segment is the space that the container belongs to,
- the second segment is the container external id,
- the third segment is the property id inside the container.

<u>Example:</u>

You have the schema
```
{
  "mySpace": {
    "myContainer": {
      "myProperty": {...}
    }
  }
}
```

Use `["mySpace", "myContainer", "myProperty"]` array to filter the records based on `myProperty` property.


---

### FilterPropertyCreatedAndLastUpdatedTimeTopLevelIla

Property a client wants to use in the filter.
To represent fully qualified properties, Records use arrays of strings.
The filter supports container level properties and two top level properties (`["createdTime"]` and `["lastUpdatedTime"]`).

So the property array has 1 or 3 segments.
1 segment property is top level property. And currently, only `createdTime` and `lastUpdatedTime`
top level properties are supported in the filter.
3 segment property is container level property.
The property array for container level property has always 3 segments:
- the first segment is the space that the container belongs to,
- the second segment is the container external id,
- the third segment is the property id inside the container.

<u>Example:</u>

You have the schema
```
{
  "mySpace": {
    "myContainer": {
      "myProperty": {...}
    }
  }
}
```

Use `["mySpace", "myContainer", "myProperty"]` array to filter the records based on `myProperty` property.


---

### FilterPropertyNoTopLevelIla

Property a client wants to use in the filter.
To represent fully qualified properties, Records use arrays of strings.
The filter supports container level properties only.

So the property array always has 3 segments:
 - the first segment is the space that the container belongs to,
 - the second segment is the container external id,
 - the third segment is the property id inside the container.

<u>Example:</u>

You have the schema
```
{
  "mySpace": {
    "myContainer": {
      "myProperty": {...}
    }
  }
}
```

Use `["mySpace", "myContainer", "myProperty"]` array to filter the records based on `myProperty` property.


---

### FilterPropertySpaceAndExternalIdTopLevelIla

Property a client wants to use in the filter.
To represent fully qualified properties, Records use arrays of strings.
The filter supports container level properties and two top level properties (`["space"]` and `["externalId"]`).

So the property array has 1 or 3 segments.
1 segment property is top level property. And currently, only `space` and `externalId`
top level properties are supported in the filter.
3 segment property is container level property.
The property array for container level property has always 3 segments:
- the first segment is the space that the container belongs to,
- the second segment is the container external id,
- the third segment is the property id inside the container.

<u>Example:</u>

You have the schema
```
{
  "mySpace": {
    "myContainer": {
      "myProperty": {...}
    }
  }
}
```

Use `["mySpace", "myContainer", "myProperty"]` array to filter the records based on `myProperty` property.


---

### FilterRecordsRequest

---

### FilterUsers

**プロパティ**:

- `limit` (integer, 任意)
- `cursor` (string, 任意)
  - Cursor for pagination

---

### FilterValue

---

### FilterValueList

---

### FilterValueRange

---

### FilteringNotice

---

### FiltersAggregateIla

**プロパティ**:

- `filters` (object, 必須)
  - A multi-bucket aggregation where each bucket contains the records that match a filter from `filters list`.

**Note:** The only 30 such buckets (filter defined) are allowed across all Filters aggregates in a single request.

<u>Example:</u>

To find the maximum scores earned by a player in all games before 2025-05-15T18:00:00.00Z and after it:
```
{
  "my_filters": {
    "filters": {
      "filters": [
        {
          "range": {
            "property": [ "football", "game_statistics", "game_date" ],
            "gte": "2025-05-15T18:00:00.00Z"
          }
        },
        {
          "range": {
            "property": [ "football", "game_statistics", "game_date" ],
            "lt": "2025-05-15T18:00:00.00Z"
          }
        }
      ],
      "aggregates": {
        "my_player_scores_max": {
          "max": { "property": [ "football", "game_statistics", "points_scored" ] }
        }
      }
    }
  }
}
```


---

### FiltersAggregateResultIla

**プロパティ**:

- `filterBuckets` (array, 任意)
  - Filter buckets of the specified in the request filters.


---

### FlatOidcCredentials

**プロパティ**:

- `clientId` (string, 必須)
- `clientSecret` (string, 必須)
- `scopes` (string, 任意)
- `tokenUri` (string, 必須)
- `cdfProjectName` (string, 必須)
- `audience` (string, 任意)

---

### FlatOidcCredentialsUpdate

**プロパティ**:

- `clientId` (string, 任意)
- `clientSecret` (string, 任意)
- `scopes` (string, 任意)
- `tokenUri` (string, 任意)
- `cdfProjectName` (string, 任意)
- `audience` (string, 任意)

---

### Float

---

### FullParseInput

**プロパティ**:

- `documents` (array, 必須)
- `filters` (object, 必須)
  - Map of filters for DMS list operations used to load assets and files
- `libraryId` (unknown, 必須)
  - The externalId of a library to use for parsing
- `minTokens` (integer, 任意)
  - Each detected item must match the detected entity on at least this number of tokens. A token is a substring of consecutive letters or digits.
- `nonce` (string, 必須)
  - Session nonce value
- `partialMatch` (boolean, 任意)
  - Allow partial (fuzzy) matching of entities in the engineering diagrams. Creates a match only when it is possible to do so unambiguously.
- `searchField` (string, 任意)
  - This field determines the string to search for and to identify object entities.

---

### Function

**プロパティ**:

- `id` (unknown, 必須)
- `createdTime` (unknown, 必須)
- `status` (unknown, 必須)
- `name` (unknown, 必須)
- `externalId` (unknown, 任意)
- `fileId` (unknown, 必須)
- `owner` (unknown, 任意)
- `description` (string, 任意)
  - Description of the function.
- `metadata` (unknown, 任意)
- `secrets` (object, 任意)
  - Object with additional secrets as key/value pairs. These can e.g. password to simulators or other data sources. Keys must be lowercase characters, numbers or dashes (-) and at most 15 characters. You can create at most 30 secrets, all keys must be unique, and cannot be `token`, `indexUrl`, or `extraIndexUrls`. For each secret, the combined size of the secret name and secret value is limited by 25 kB. The secrets are returned scrambled if set.
- `functionPath` (string, 任意)
  - Relative path from the root folder to the file containing the `handle` function. Defaults to `handler.py`. Must be on POSIX path format.
- `envVars` (object, 任意)
  - Object with environment variables as key/value pairs. Keys can contain only letters, numbers or the underscore character. You can create at most 100 environment variables.
- `cpu` (number, 任意)
  - Number of CPU cores per function. Allowed range and default value are given by the [limits endpoint](https://developer.cognite.com/api#tag/Functions/operation/functionsLimits). On Azure, only the default value is used.
- `memory` (number, 任意)
  - Memory per function measured in GB. Allowed range and default value are given by the [limits endpoint](https://developer.cognite.com/api#tag/Functions/operation/functionsLimits). On Azure, only the default value is used.
- `runtime` (string, 任意)
  - The runtime of the function. For exmple, runtime "py312" translates to the latest version of the Python 3.12 series.
- `runtimeVersion` (string, 任意)
  - The complete specification of the function runtime with major, minor and patch version numbers.
- `error` (unknown, 任意)
- `indexUrl` (string, 任意)
  - Specify a different python package index, allowing for packages published in private repositories.
Supports basic HTTP authentication as described in [pip basic authentication](https://pip.pypa.io/en/stable/topics/authentication/#basic-http-authentication).
See the
[documentation](https://docs.cognite.com/cdf/functions/#additional-arguments)
for additional information related to the security risks of using this
option.
- `extraIndexUrls` (array, 任意)
  - Extra package index URLs to use when building the function, allowing for packages published in private repositories.
Supports basic HTTP authentication as described in [pip basic
authentication](https://pip.pypa.io/en/stable/topics/authentication/#basic-http-authentication).
See the
[documentation](https://docs.cognite.com/cdf/functions/#additional-arguments)
for additional information related to the security risks of using this
option.

---

### FunctionBuildError

Cognite Function API error.

**プロパティ**:

- `code` (integer, 必須)
  - HTTP status code.
- `message` (string, 必須)
  - Error message.

---

### FunctionCall

**プロパティ**:

- `id` (unknown, 必須)
- `status` (unknown, 必須)
- `startTime` (unknown, 必須)
- `endTime` (unknown, 任意)
- `error` (unknown, 任意)
- `scheduleId` (unknown, 任意)
- `functionId` (unknown, 必須)
- `scheduledTime` (unknown, 任意)

---

### FunctionCallError

Error occuring due to user's function code.

**プロパティ**:

- `trace` (string, 任意)
  - Stack trace of exception, useful for debugging.
- `message` (string, 必須)
  - Error message.

---

### FunctionCallFilter

**プロパティ**:

- `filter` (object, 任意)
  - 

---

### FunctionCallId

**プロパティ**:

- `id` (unknown, 必須)

---

### FunctionCallIds

---

### FunctionCallListScope

---

### FunctionCallLogEntry

**プロパティ**:

- `timestamp` (unknown, 任意)
- `message` (string, 任意)
  - Single line from stdout / stderr.

---

### FunctionCallRequest

**プロパティ**:

- `data` (unknown, 任意)
- `nonce` (unknown, 必須)

---

### FunctionCallStatus

Status of the function call.

---

### FunctionCalls

**プロパティ**:

- `items` (array, 必須)

---

### FunctionCallsWithCursor

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
  - Cursor to get the next page of results (if available).

---

### FunctionDeleteRequest

---

### FunctionErrorBasic

Cognite Function API error.

**プロパティ**:

- `code` (integer, 必須)
  - HTTP status code.
- `message` (string, 必須)
  - Error message.

---

### FunctionExternalId

The external ID of the function. Should be unique for the project.

**プロパティ**:

- `externalId` (unknown, 必須)

---

### FunctionFileId

The file ID to a file uploaded to Cognite's Files API. This file must be a zip file and contain a file called `handler.py` in the root folder (unless otherwise specified in the `functionPath` argument). This file must contain a function named `handle` with any of the following arguments: `data`, `client`, `secrets` and `function_call_info`, which are passed into the function. The zip file can contain other files as well (model binary data, libraries etc).

Custom packages can be pip installed by providing a requirements.txt file in the root of the zip file. The latest version of the Cognite Python SDK is automatically installed. If a specific version is needed, please specify this in the requirements.txt file.

---

### FunctionFilter

**プロパティ**:

- `filter` (object, 任意)

---

### FunctionId

The ID of the function.

**プロパティ**:

- `id` (unknown, 必須)

---

### FunctionIdEither

---

### FunctionIdEitherList

---

### FunctionListScope

---

### FunctionName

The name of the function.

---

### FunctionOwner

Owner of this function. Typically used to know who created it.

---

### FunctionSchedule

---

### FunctionScheduleCronExpression

Cron expression describes when the function should be called. Use http://www.cronmaker.com to create a cron expression.

---

### FunctionScheduleDescription

Description of function schedule.

---

### FunctionScheduleFilter

**プロパティ**:

- `filter` (object, 任意)
  - 

---

### FunctionScheduleId

The ID of the function schedule.

**プロパティ**:

- `id` (unknown, 必須)

---

### FunctionScheduleIdArray

**プロパティ**:

- `items` (array, 必須)

---

### FunctionScheduleIds

---

### FunctionScheduleName

Name of function schedule.

---

### FunctionScheduleScope

---

### FunctionScheduleWhen

When the schedule will trigger, in human readable text.

---

### FunctionStatus

Status of the function. It starts in a Queued state, is then Deploying before it is either Ready or Failed. If the function is Ready, it can be called.

---

### FunctionTaskOutput

**プロパティ**:

- `callId` (integer, 任意)
  - The callId of the Cognite Function call instance.
- `functionId` (integer, 任意)
  - The functionId of the Cognite Function called.
- `response` (object, 任意)
  - The response of the function call. Limited to 100KB in size.

---

### FunctionTaskParameters

Parameters for the Cognite Function task type.

**プロパティ**:

- `function` (object, 任意)
- `isAsyncComplete` (boolean, 任意)
  - Defines if the execution of the task should be completed asynchronously.

  - If `false`, the status of the task will be set to `COMPLETED` when the Cognite Function call completes successfully.\
  - If `true`, the task status will remain `IN_PROGRESS` even when the Cognite Function call completes successfully.\
  It will then wait for an external process to update the task status directly using the task update endpoint.\
  The task id required for the callback to update the task status is included in the input data of the Function with key "cogniteOrchestrationTaskId".

---

### GenerateRules

Whether to generate match rules automatically

---

### GeneratedRule

An entity matching rule matches source entities with target entities based on a pattern defined by the rule.
A rule has extractors that both require entities to follow a certain format, and produce lists of features.

For a regex extractor, a specific field must match a pattern, and parts of the pattern become the features.
The conditions of a rule express which extracted features must be equal for two entities to match.

Since extractors restrict the scopes of rules to entities that conform with patterns, two rules will often not
have entities in common that they apply to. But if they do, the more specific rule is given higher priority.

If one rule is not a special case of the other, they may be inconsistent (conflict) or redundant (overlap).

**プロパティ**:

- `matches` (array, 任意)
- `numConflicts` (integer, 任意)
- `numOverlaps` (integer, 任意)

---

### GenericProperties-Output

**プロパティ**:

- `additionalProperties` (unknown, 任意)
  - Whether the parameter can have additional properties. If false, no additional Properties are allowed. If true, any additional properties are accepted. If a json schema, the schema should specify the type(s) permissible for additional properties
- `anyOf` (array, 任意)
  - The anyOf values of the parameter.
- `default` (unknown, 任意)
  - The default value of the parameter.
- `description` (string, 任意)
  - A description of the parameter.
- `enum` (array, 任意)
  - The enum values of the parameter.
- `format` (string, 任意)
  - The format of the data. Supported formats: for 'number' type: 'float', 'double' for 'integer' type: 'int32', 'int64' for 'string' type: 'email', 'byte', etc.
- `items` (unknown, 任意)
  - The items of the parameter for type 'array'.
- `maxItems` (integer, 任意)
  - The maximum number of the elements for type 'array'.
- `maxLength` (integer, 任意)
  - The maximum length of the parameter for type 'string'.
- `maximum` (integer, 任意)
  - The maximum value for the parameter for type 'number' and 'integer'.
- `minItems` (integer, 任意)
  - The minimum number of the elements for type 'array'.
- `minLength` (integer, 任意)
  - The minimum length of the parameter for type 'string'.
- `minimum` (integer, 任意)
  - The minimum value for the parameter for type 'number' and 'integer'.
- `nullable` (boolean, 任意)
  - Specifies whether the parameter can be null.
- `pattern` (string, 任意)
  - The regular expression pattern of the parameter for type 'string'.
- `properties` (object, 任意)
  - The properties of the parameter for type 'object'.
- `propertyOrdering` (array, 任意)
  - The order of the properties.
- `required` (array, 任意)
  - The required properties of the parameter for type 'object'.
- `title` (string, 任意)
  - The title of the schema.
- `type` (string, 任意)
  - The type of the parameter.

---

### GenericRangeFilter

**プロパティ**:

- `range` (object, 必須)
  - Matches items that contain terms within the provided range.
It is not allowed to specify both inclusive and exclusive bounds (such as `gte`, `gt`) together.
`gte`: Greater than or equal to.
`gt`: Greater than.
`lte`: Less than or equal to.
`lt`: Less than.


---

### GeoLocation

Geographic metadata.

**プロパティ**:

- `type` (string, 必須)
  - One of the GeoJSON types. Currently only the 'Feature' type is supported.
- `geometry` (unknown, 必須)
- `properties` (object, 任意)
  - Additional properties in a String key -> Object value format.

---

### GeoLocationFilter

Only include files matching the specified geographic relation.

**プロパティ**:

- `relation` (string, 必須)
  - One of the supported queries.
- `shape` (object, 必須)
  - Represents the points, curves and surfaces in the coordinate space.

---

### GeoLocationGeometry

Represents the points, curves and surfaces in the coordinate space.

---

### GeometryCreateDraft

**プロパティ**:

- `externalId` (unknown, 必須)
  - The external ID of the geometry
- `paths` (array, 必須)
- `symbolId` (unknown, 必須)
  - The external ID of a symbol the geometry belongs to

---

### GeometryDto

**プロパティ**:

- `createdTime` (unknown, 必須)
- `externalId` (unknown, 必須)
  - The external ID of the geometry
- `lastUpdatedTime` (unknown, 必須)
- `paths` (array, 必須)
  - List of paths of this geometry
- `symbolId` (unknown, 必須)
  - The external ID of a symbol the geometry belongs to

---

### GeometryUpdateDraft

**プロパティ**:

- `paths` (array, 任意)

---

### GeometryUpdateItem

**プロパティ**:

- `externalId` (unknown, 必須)
  - The external ID of the geometry
- `update` (unknown, 必須)
  - The properties to be updated

---

### GeospatialAggregate

---

### GeospatialAggregateOutput

A list of aggregations which are requested.

---

### GeospatialAggregateOutputSpec

---

### GeospatialAllowCrsTransformation

Optional parameter indicating if input geometry properties should be transformed into the respective Coordinate Reference Systems defined in the feature type specification. If the parameter is true, then input geometries will be transformed when the input and output Coordinate Reference Systems differ. When it is false, then requests with geometries in Coordinate Reference System different from the ones defined in the feature type will result in bad request response code. Transformations apply to property geometries in case of create and update feature, as well as to geometries in spatial filters in search endpoints.

---

### GeospatialAllowDimensionalityMismatch

Optional parameter indicating if the spatial filter operators allow input geometries with a different dimensionality than the properties they are applied to. For instance, when set to true, if a feature type has a property of type POLYGONZM (4D), its features can be filtered using the `stContains` operator and a POLYGON (2D) value. This option defaults to false if not specified.

---

### GeospatialAvgAggregationOutput

**プロパティ**:

- `avg` (unknown, 必須)

---

### GeospatialCentroidAggregationOutput

**プロパティ**:

- `stCentroid` (unknown, 必須)

---

### GeospatialCollectAggregationOutput

**プロパティ**:

- `stCollect` (unknown, 必須)

---

### GeospatialComputeFunction

---

### GeospatialComputeRequest

---

### GeospatialComputedItem

---

### GeospatialComputedItems

**プロパティ**:

- `items` (array, 任意)

---

### GeospatialConvexHullAggregationOutput

**プロパティ**:

- `stConvexHull` (unknown, 必須)

---

### GeospatialCoordinateReferenceSystem

**プロパティ**:

- `srid` (unknown, 必須)
- `wkt` (unknown, 必須)
- `projString` (unknown, 必須)

---

### GeospatialCoordinateReferenceSystemList

**プロパティ**:

- `items` (array, 必須)

---

### GeospatialCoordinateReferenceSystems

**プロパティ**:

- `items` (array, 必須)

---

### GeospatialCountAggregationOutput

**プロパティ**:

- `count` (unknown, 必須)

---

### GeospatialCreatedFeature

---

### GeospatialCustomCoordinateReferenceSystem

**プロパティ**:

- `srid` (unknown, 必須)
- `wkt` (unknown, 必須)
- `projString` (unknown, 必須)
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)

---

### GeospatialCustomCoordinateReferenceSystemSpecs

**プロパティ**:

- `items` (array, 必須)

---

### GeospatialDeleteFeatureType

**プロパティ**:

- `recursive` (unknown, 任意)
- `items` (array, 必須)

---

### GeospatialError

Cognite API error.

**プロパティ**:

- `code` (integer, 必須)
  - HTTP status code.
- `message` (string, 必須)
  - Error message.
- `missing` (array, 任意)
  - List of lookup objects that do not match any results.
- `duplicated` (array, 任意)
  - List of objects that are not unique.
- `invalid` (array, 任意)
  - List of objects that are not valid.
- `dependencies` (array, 任意)
  - List of dependencies.

---

### GeospatialExtendedWellKnownText

Well-known text of the geometry prefixed with a spatial reference identifier, see https://docs.geotools.org/stable/javadocs/org/opengis/referencing/doc-files/WKT.html

---

### GeospatialFeature

---

### GeospatialFeatureAggregateRequest

Feature Aggregation Request

**プロパティ**:

- `allowDimensionalityMismatch` (unknown, 任意)
- `filter` (unknown, 任意)
- `aggregates` (array, 任意)
  - This parameter is deprecated. Use `output` instead. Names of aggregate functions that are requested.
- `property` (string, 任意)
  - This parameter is deprecated. Use `output` instead. Property name.
- `outputSrid` (unknown, 任意)
- `groupBy` (array, 任意)
  - names of properties to be used for grouping by
- `sort` (array, 任意)
  - Sort result by the selected fields (properties or aggregates). Default sort order is ascending if not specified. Available sort direction: ASC, DESC, ASC_NULLS_FIRST, DESC_NULLS_FIRST, ASC_NULLS_LAST, DESC_NULLS_LAST.
- `output` (unknown, 任意)

---

### GeospatialFeatureAggregates

**プロパティ**:

- `items` (array, 必須)

---

### GeospatialFeatureAndFilter

**プロパティ**:

- `and` (array, 任意)

---

### GeospatialFeatureContainsAnyFilter

**プロパティ**:

- `containsAny` (unknown, 任意)

---

### GeospatialFeatureEqualsFilter

**プロパティ**:

- `equals` (unknown, 任意)

---

### GeospatialFeatureFilter

---

### GeospatialFeatureIds

**プロパティ**:

- `items` (array, 必須)

---

### GeospatialFeatureIdsWithOutput

---

### GeospatialFeatureInFilter

**プロパティ**:

- `in` (unknown, 任意)

---

### GeospatialFeatureLikeFilter

Simple pattern maching. Use `_` for single character and `%` for string of any length. Backslash `\` is an escape character.

**プロパティ**:

- `like` (unknown, 任意)

---

### GeospatialFeatureListRequest

Feature List Request

---

### GeospatialFeatureMissingFilter

**プロパティ**:

- `missing` (unknown, 任意)

---

### GeospatialFeatureNotFilter

**プロパティ**:

- `not` (unknown, 任意)

---

### GeospatialFeatureOrFilter

**プロパティ**:

- `or` (array, 任意)

---

### GeospatialFeatureProperties

---

### GeospatialFeatureRangeFilter

**プロパティ**:

- `range` (unknown, 任意)

---

### GeospatialFeatureRegexFilter

Regular expression pattern matching.

**プロパティ**:

- `regex` (unknown, 任意)

---

### GeospatialFeatureSearchRequest

Feature Search Request

**プロパティ**:

- `allowCrsTransformation` (unknown, 任意)
- `allowDimensionalityMismatch` (unknown, 任意)
- `filter` (unknown, 任意)
- `limit` (unknown, 任意)
- `output` (unknown, 任意)
- `sort` (array, 任意)
  - Sort result by selected fields. Syntax: sort:["field_1","field_2:ASC","field_3:DESC"]. Default sort order is ascending if not specified. Available sort direction: ASC, DESC, ASC_NULLS_FIRST, DESC_NULLS_FIRST, ASC_NULLS_LAST, DESC_NULLS_LAST.


---

### GeospatialFeatureSearchResult

**プロパティ**:

- `items` (array, 必須)

---

### GeospatialFeatureSearchStreamingRequest

Search and streaming feature request

**プロパティ**:

- `allowCrsTransformation` (unknown, 任意)
- `allowDimensionalityMismatch` (unknown, 任意)
- `filter` (unknown, 任意)
- `limit` (number, 任意)
- `output` (unknown, 任意)

---

### GeospatialFeatureSpec

---

### GeospatialFeatureSpecs

---

### GeospatialFeatureStContainsFilter

**プロパティ**:

- `stContains` (unknown, 任意)

---

### GeospatialFeatureStContainsProperlyFilter

**プロパティ**:

- `stContainsProperly` (unknown, 任意)

---

### GeospatialFeatureStIntersects3dFilter

**プロパティ**:

- `stIntersects3d` (unknown, 任意)

---

### GeospatialFeatureStIntersectsFilter

**プロパティ**:

- `stIntersects` (unknown, 任意)

---

### GeospatialFeatureStWithinDistance3dFilter

**プロパティ**:

- `stWithinDistance3d` (unknown, 任意)

---

### GeospatialFeatureStWithinDistanceFilter

**プロパティ**:

- `stWithinDistance` (unknown, 任意)

---

### GeospatialFeatureStWithinFilter

**プロパティ**:

- `stWithin` (unknown, 任意)

---

### GeospatialFeatureStWithinProperlyFilter

**プロパティ**:

- `stWithinProperly` (unknown, 任意)

---

### GeospatialFeatureType

---

### GeospatialFeatureTypeExternalId

---

### GeospatialFeatureTypeExternalIds

**プロパティ**:

- `items` (array, 必須)

---

### GeospatialFeatureTypeProperty

**プロパティ**:

- `type` (unknown, 必須)
- `description` (string, 任意)
  - The description of the property
- `srid` (unknown, 任意)
- `size` (integer, 任意)
  - Maximal length of string. Only valid for string type.
- `optional` (boolean, 任意)
  - Indicate if property is nullable. Default is false.

---

### GeospatialFeatureTypeSpec

**プロパティ**:

- `externalId` (unknown, 必須)
- `dataSetId` (unknown, 任意)
- `properties` (object, 必須)
  - Each property name has to match the following regexp pattern `/^[A-Za-z][A-Za-z0-9_]{0,31}$/`.\ The number of properties is limited to 200 per feature type.
- `searchSpec` (object, 任意)
  - Each index name has to match the following regexp pattern `/^[A-Za-z][A-Za-z0-9_]{0,31}$/`.\ The number of indexes is limited to 10 per feature type.

---

### GeospatialFeatureTypeSpecs

**プロパティ**:

- `items` (array, 必須)

---

### GeospatialFeatureTypes

**プロパティ**:

- `items` (array, 必須)

---

### GeospatialFeatures

---

### GeospatialFeaturesWithCursor

A list of objects along with possible cursors to get the next page of results

---

### GeospatialGeometryComputeFunction

---

### GeospatialGeometryTransformComputeFunction

**プロパティ**:

- `stTransform` (object, 必須)

---

### GeospatialGeometryValueComputeFunction

**プロパティ**:

- `ewkt` (unknown, 必須)

---

### GeospatialIndexSpec

**プロパティ**:

- `properties` (array, 必須)

---

### GeospatialIntersectionAggregationOutput

**プロパティ**:

- `stIntersection` (unknown, 必須)

---

### GeospatialItemExternalId

**プロパティ**:

- `externalId` (unknown, 必須)

---

### GeospatialJsonComputeOutput

**プロパティ**:

- `output` (object, 必須)

---

### GeospatialMaxAggregationOutput

**プロパティ**:

- `max` (unknown, 必須)

---

### GeospatialMinAggregationOutput

**プロパティ**:

- `min` (unknown, 必須)

---

### GeospatialOutput

Desired output specification.

**プロパティ**:

- `geometryFormat` (string, 任意)
  - Desired format for geometry output. If GEOJSON is the output format, SRID 4326 must be specified explicitly for all geometrical properties. Defaults to WKT.
- `properties` (object, 任意)
  - A property selection with specification of output format (e.g. desired output Coordinate Reference System into which geometries should be transformed). All properties will be returned when the selection is empty or contains the wildcard `*` character. When the selection contains only some of the properties, the result will contain only requested properties.

---

### GeospatialOutputStreaming

Desired output specification for streaming.

**プロパティ**:

- `geometryFormat` (string, 任意)
  - Desired format for geometry output. If GEOJSON is the output format, SRID 4326 must be specified explicitly for all geometrical properties. Defaults to WKT.
- `jsonStreamFormat` (string, 任意)
  - optional parameter to indicate response format in search streaming endpoint. Defaults to LENGTH_PREFIXED
- `properties` (object, 任意)
  - A property selection with specification of output format (e.g. desired output Coordinate Reference System to which geometries should be transformed). All properties will be returned when the selection is empty or contains the wildcard `*` character. When the selection contains only some of the properties, the result will contain only requested properties.

---

### GeospatialProjString

The projection specification string as described in https://proj.org/usage/quickstart.html

---

### GeospatialProperty

**プロパティ**:

- `property` (string, 任意)

---

### GeospatialPropertyAndPattern

**プロパティ**:

- `property` (unknown, 任意)
- `pattern` (string, 任意)

---

### GeospatialPropertyAndValue

**プロパティ**:

- `property` (unknown, 任意)
- `value` (unknown, 任意)

---

### GeospatialPropertyAndValues

**プロパティ**:

- `property` (unknown, 任意)
- `values` (array, 任意)

---

### GeospatialPropertyName

---

### GeospatialPropertyReference

References a feature property in the context of a filter The support for arrays is forward looking for when the geospatial api will have complex properties.

**プロパティ**:

- `property` (unknown, 任意)

---

### GeospatialPropertyType

Property type

---

### GeospatialPropertyValueAndDistance

**プロパティ**:

- `property` (unknown, 任意)
- `value` (unknown, 任意)
- `distance` (number, 任意)

---

### GeospatialRange

---

### GeospatialRangeCondition

Range condition can be open (i.e. having one of the properties) or closed (i.e. having two opposite properties). Can be applied to numeric types as well as string.

**プロパティ**:

- `gt` (unknown, 任意)
- `lt` (unknown, 任意)
- `gte` (unknown, 任意)
- `lte` (unknown, 任意)

---

### GeospatialRasterCommonOutputSpec

**プロパティ**:

- `srid` (unknown, 任意)
- `scaleX` (number, 任意)
- `scaleY` (number, 任意)

---

### GeospatialRasterGtiffOutputSpec

GTiff specification for the output raster

**プロパティ**:

- `format` (string, 必須)
- `options` (object, 任意)

---

### GeospatialRasterMetadata

**プロパティ**:

- `srid` (integer, 任意)
- `width` (integer, 任意)
- `height` (integer, 任意)
- `numBands` (integer, 任意)
- `scaleX` (number, 任意)
- `scaleY` (number, 任意)
- `skewX` (number, 任意)
- `skewY` (number, 任意)
- `upperLeftX` (number, 任意)
- `upperLeftY` (number, 任意)

---

### GeospatialRasterOutputSpec

---

### GeospatialRasterXyzOutputSpec

XYZ specification for the output raster

**プロパティ**:

- `format` (string, 必須)
- `options` (object, 任意)

---

### GeospatialRecursiveDelete

Indicates if feature types should be deleted together with all related features. Optional parameter, defaults to false.

---

### GeospatialReferenceId

EPSG code, e.g. 4326. Only valid for geometry types. See https://en.wikipedia.org/wiki/Spatial_reference_system

---

### GeospatialReferenceIds

**プロパティ**:

- `items` (array, 必須)

---

### GeospatialSumAggregationOutput

**プロパティ**:

- `sum` (unknown, 必須)

---

### GeospatialUnionAggregationOutput

**プロパティ**:

- `stUnion` (unknown, 必須)

---

### GeospatialUpdateFeatureTypeSpec

**プロパティ**:

- `externalId` (unknown, 必須)
- `update` (object, 必須)

---

### GeospatialUpdateFeatureTypeSpecs

**プロパティ**:

- `items` (array, 必須)

---

### GeospatialVarianceAggregationOutput

**プロパティ**:

- `variance` (unknown, 必須)

---

### GeospatialWellKnownText

Well-known text of the geometry, see https://docs.geotools.org/stable/javadocs/org/opengis/referencing/doc-files/WKT.html

---

### GetDatapointMetadata

**プロパティ**:

- `timestamp` (unknown, 必須)

---

### GetDoubleDatapoint

---

### GetNumericAggregateDatapoint

---

### GetSequenceColumnDTO

Information about a column stored in the database.

**プロパティ**:

- `name` (string, 任意)
  - Human readable name of the column.
- `externalId` (string, 任意)
  - User provided column identifier (unique for a given sequence).
- `description` (string, 任意)
  - Description of the column.
- `valueType` (unknown, 必須)
- `metadata` (object, 任意)
  - Custom, application specific metadata. String key -> String value.
- `createdTime` (integer, 必須)
  - Time when this asset was created in CDF in milliseconds since Jan 1, 1970.
- `lastUpdatedTime` (integer, 必須)
  - The last time this asset was updated in CDF, in milliseconds since Jan 1, 1970.

---

### GetSequenceDTO

Information about the sequence stored in the database.

**プロパティ**:

- `id` (integer, 必須)
  - Unique Cognite-provided identifier for the sequence.
- `name` (string, 任意)
  - Name of the sequence.
- `description` (string, 任意)
  - Description of the sequence.
- `assetId` (integer, 任意)
  - Optional asset this sequence is associated with.
- `externalId` (unknown, 任意)
- `metadata` (object, 任意)
  - Custom, application specific metadata. String key -> String value. Maximum length of key is 128 bytes, up to 256 key-value pairs, up to a total size of 10000 bytes across all keys and values.
- `columns` (array, 必須)
  - List of column definitions.
- `createdTime` (integer, 必須)
  - Time when this sequence was created in CDF in milliseconds since Jan 1, 1970.
- `lastUpdatedTime` (integer, 必須)
  - The last time this sequence was updated in CDF, in milliseconds since Jan 1, 1970.
- `dataSetId` (integer, 任意)
  - Data set this sequence belongs to.

---

### GetStateAggregateDatapoint

---

### GetStateDatapoint

---

### GetStringDatapoint

---

### GetTimeSeriesForSubscription

**プロパティ**:

- `id` (unknown, 必須)
- `externalId` (unknown, 任意)
- `instanceId` (unknown, 任意)
- `isString` (boolean, 必須)
  - True if the time series contains string values; false if it contains numeric values.

- `type` (unknown, 必須)

---

### GetTimeSeriesMetadataDTO

**プロパティ**:

- `id` (unknown, 必須)
- `externalId` (string, 任意)
  - The externally supplied ID for the time series.
- `instanceId` (unknown, 任意)
- `name` (string, 任意)
  - The display short name of the time series.
- `isString` (boolean, 必須)
  - True if the time series contains string values; false if it contains numeric values.

- `type` (unknown, 必須)
- `metadata` (unknown, 任意)
- `unit` (string, 任意)
  - The physical unit of the time series (free-text field)
- `unitExternalId` (string, 任意)
  - The physical unit of the time series as [represented in the unit catalog](<https://developer.cognite.com/dev/concepts/resource_types/units/>).
- `assetId` (unknown, 任意)
  - Asset ID of equipment linked to this time series.
- `isStep` (boolean, 必須)
  - Defines whether the time series is a step series or not.
- `description` (string, 任意)
  - Description of the time series.
- `securityCategories` (array, 任意)
  - The required security categories to access this time series.
- `dataSetId` (unknown, 任意)
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)

---

### Grayscale

Return the SVG version in grayscale colors only (reduces the file size).

---

### Group

---

### GroupAttributes

Set Attributes for the group.

**プロパティ**:

- `token` (unknown, 任意)

---

### GroupBase

**プロパティ**:

- `name` (unknown, 必須)
- `capabilities` (unknown, 任意)
- `metadata` (unknown, 任意)
- `attributes` (unknown, 任意)

---

### GroupCallbackEnabled

A group callback occurs when a user has too many groups attached. This property indicates whether the group callback functionality should be supported for this project. This is only supported for AAD hosted IdPs.


---

### GroupMembershipManagedExternallyOrInCdf

---

### GroupMetadata

Custom, immutable application specific metadata. String key -> String value. Limits: Key are at most 32 bytes. Values are at most 512 bytes. Up to 16 key-value pairs. Total size is at most 4096.

---

### GroupName

Name of the group

---

### GroupResponse

---

### GroupSourceId

ID of the group in the source. If this is the same ID as a group in the IdP, a principal in that group will implicitly be a part of this group as well.

---

### GroupSpec

A specification for creating a new group

---

### GroupTokenAttributes

Attributes derived from access token.

**プロパティ**:

- `appIds` (array, 任意)
  - List of applications (represented by their application ID) this group is valid for. If present, must contain at least one item. If not set or null, the group will be valid for all apps.


---

### HTTPValidationError

**プロパティ**:

- `detail` (array, 任意)

---

### HasDataFilterIla

**プロパティ**:

- `hasData` (array, 必須)
  - Matches records where data is present in the referenced containers.


---

### HasExistingDataFilterV3

**プロパティ**:

- `hasData` (array, 必須)
  - Matches instances that have data in the referenced views or containers.

---

### HeaderValueAuthentication

**プロパティ**:

- `type` (string, 必須)
  - Authentication type.
- `key` (string, 必須)
  - Key for the header to place the authentication token in

---

### HeaderValueConfig

**プロパティ**:

- `type` (string, 必須)
- `key` (string, 必須)
  - Key to insert the generated value into
- `value` (string, 必須)
  - Expression that will be evaluated, and its result used as a header value

---

### HistogramAggregateFunctionV3

A histogram aggregator function.  This function will generate a histogram from the values of the specified property.  It uses the specified interval as defined in your `interval` argument.

**プロパティ**:

- `histogram` (object, 必須)

---

### IdEither

Either an internal ID, or an external ID.

---

### IdRef

**プロパティ**:

- `id` (integer, 必須)
  - ID

---

### IdentityProvider

Configuration for an external OIDC-compliant IdP.

---

### IdentityType

The identity type field indicates the type of principal.
- `USER`: Human user.
- `SERVICE_PRINCIPAL`: Service account.
- `INTERNAL_SERVICE`: Internal CDF service.


---

### IdentityTypeFilter

The identity type filter field indicates the type of principal the request should be filtered to show.
If no value is specified, the default value is `USER`.
- `ALL`: All types of principals.
- `USER`: Human user.
- `SERVICE_PRINCIPAL`: Service account.
- `INTERNAL_SERVICE`: Internal CDF service.


---

### IdpBase

**プロパティ**:

- `issuer` (string, 必須)
  - The issuer for the external IdP.

---

### IgnoreMissingFields

If True, replaces missing fields in `sources` or `targets` entities, for fields set in set in `matchFields`, with empty strings. Else, returns an error if there are missing data.

---

### IgnoreUnknownIdsField

**プロパティ**:

- `ignoreUnknownIds` (boolean, 任意)
  - Ignore IDs and external IDs that are not found

---

### Image360AnnotationV2

**プロパティ**:

- `version` (string, 必須)
- `data` (array, 必須)
  - List of polygons separated by the number of vertices per polygon.
The phi (Φ) theta (Θ) pairs for a polygon is defined in a spherical coordinate system.
Each pair represents a vertex position on the sphere. Phi (φ) is the azimut angle and theta (θ) is the polar angle.
The angles are defined in radians. There must be at least one polygon in the array.
The polygons may have minimum 3 vertices, 6 values, and a total maximum of 1000 values in the array.

The polygon exists in a specific 360-degree image in the revision referenced by the dmsContextualizationConfig.

- 2.0.0: Order of rotation x, y, z (CDF coordinates)


---

### ImageTextDetectionJob

---

### ImprovementSuggestion

---

### InFilter

**プロパティ**:

- `in` (object, 必須)
  - Matches items where the given property is **exactly** equal to one of the given values. This filter can only be applied to single-valued properties.


---

### InFilterIla

**プロパティ**:

- `in` (object, 必須)
  - Matches records where the property **exactly** matches one of the given values.
Only apply this filter to properties containing a single value.


---

### InFilterV3

**プロパティ**:

- `in` (object, 必須)
  - Matches items where the property **exactly** matches one of the given values.

---

### IncludeGlobalFlag

If the global items of the entity should be returned. Defaults to false which excludes global items.

---

### IncludeTyping

Should we return property type information as part of the result?

---

### IndexCreateDefinition

You can optimize query performance by defining an index to apply to a container.  You can only create an index across properties belonging to the same container.


Ordering of the properties included in the index definition list is significant.  The order should match the queries you expect. The properties of an index cannot be updated after creation. If you need to change the index, it must be recreated.


Indexes have different requirements for the different property data types.  As a result, the create index operation will fail if you specify an invalid combination.


Up to 10 indexes can be added on a container. 

---

### IndexDefinition

Indexes applied to a container.  The properties of an index always belong to the same container.


Ordering of the properties included in the index definition list is significant.  The order should match the queries you expect. The properties of an index cannot be updated after creation. If you need to change the index, it must be recreated.


Up to 10 indexes can be added on a container. 

---

### IndexState

**プロパティ**:

- `state` (unknown, 必須)
  - This field describes the validity of the index.

Possible values are:

* `"failed"`: The index can't be built.
  This can occur if data existing before the index was created data prevents the index from being built.
  For example, property values in a btree index have a size limit on them.
  Use `maxListSize` and `maxTextSize` to bound the size of btree indexed properties.
* `"current"`: The index is built successfully.
* `"pending"`: The index is still building.


---

### IndexingNotice

---

### IndustrialObjectDetection

Detect industrial objects such as gauges and valves in images. In beta. Available only when the `cdf-version: beta` header is provided.

---

### IndustrialObjectDetectionParameters

Parameters for industrial object detection. In beta. Available only when the `cdf-version: beta` header is provided.

**プロパティ**:

- `threshold` (unknown, 任意)

---

### IngestRecordsRequest

**プロパティ**:

- `items` (array, 必須)
  - List of records to write.


---

### InputConfig

Optionally set input mapping input type. This defaults to json if left out. Any input type is converted to JSON before being passed to the mapping expression.

---

### InputItems20IdRefOrExternalIdRef

**プロパティ**:

- `items` (array, 必須)
  - A list of items

---

### InputItemsIdRef

**プロパティ**:

- `items` (array, 必須)
  - A list of items

---

### InputItemsIdRefOrExternalIdRef

**プロパティ**:

- `items` (array, 必須)
  - A list of items

---

### Instance

**プロパティ**:

- `externalId` (unknown, 必須)
- `gatewayUrl` (string, 必須)
  - SAP NetWeaver Gateway URL
- `client` (integer, 必須)
  - SAP client number
- `username` (string, 任意)
  - SAP username to use when connecting to the SAP NetWeaver Gateway URL
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)

---

### InstanceBulk

**プロパティ**:

- `items` (array, 必須)
  - List of nodes and edges to create/update
- `delete` (array, 任意)
  - List of nodes and edges to delete
- `autoCreateDirectRelations` (boolean, 任意)
  - Should we create missing target nodes of direct relations? If the target-container constraint has been specified for a direct relation, the target node cannot be auto-created. If you want to point direct relations to a space where you have only read access, this option must be set to false.
- `autoCreateStartNodes` (boolean, 任意)
  - Should we create missing start nodes for edges when ingesting?  By default, the start node of an edge must exist before we can ingest the edge.
- `autoCreateEndNodes` (boolean, 任意)
  - Should we create missing end nodes for edges when ingesting?  By default, the end node of an edge must exist before we can ingest the edge.
- `skipOnVersionConflict` (boolean, 任意)
  - If existingVersion is specified on any of the nodes/edges in the input, the default behaviour is that the entire ingestion will fail when version conflicts occur. If skipOnVersionConflict is set to true, items with version conflicts will be skipped instead. If no version is specified for nodes/edges, it will do the write directly.
- `replace` (boolean, 任意)
  - How do we behave when a property value exists? Do we replace all matching and existing values with the supplied values (`true`)?  Or should we merge in new values for properties together with the existing values (`false`)?  Note: This setting applies for all nodes or edges specified in the ingestion call.

---

### InstanceDelete

Instance to delete

**プロパティ**:

- `instanceType` (string, 必須)
- `space` (unknown, 必須)
  - Id of the space that the instance belongs to
- `externalId` (unknown, 必須)
  - Unique identifier for the instance
- `existingVersion` (integer, 任意)
  - Fail the ingestion request if the instance's version is greater than this value. If no existingVersion is specified, the ingestion will always delete the instance. If skipOnVersionConflict is set on the ingestion request, then the item will be skipped instead of failing the ingestion request.

---

### InstanceExternalId

---

### InstanceId

**プロパティ**:

- `space` (unknown, 必須)
- `externalId` (unknown, 必須)

---

### InstanceIdAI

**プロパティ**:

- `externalId` (string, 必須)
  - The data modeling external ID of the document.
- `space` (string, 必須)
  - The data modeling space of the document.

---

### InstanceInspectRequest

**プロパティ**:

- `inspectionOperations` (object, 必須)
- `items` (array, 必須)

---

### InstanceInspectResultItem

**プロパティ**:

- `instanceType` (unknown, 必須)
  - The type of instance being returned, an edge or a node.
- `externalId` (unknown, 必須)
  - External ids for the requested items
- `space` (unknown, 必須)
- `inspectionResults` (object, 必須)

---

### InstanceReference

**プロパティ**:

- `instanceId` (unknown, 必須)

---

### InstanceReferenceFilterV3

**プロパティ**:

- `instanceReferences` (array, 必須)
  - Matches instances with any of the specified space/externalId pairs.

---

### InstanceSpace

---

### InstanceType

The type of instance

---

### InstancesCountsAndLimits

Statistics and limits of the number of instances in a project

**プロパティ**:

- `edges` (integer, 必須)
  - Number of edges in the project
- `softDeletedEdges` (integer, 必須)
  - Number of soft-deleted edges in the project
- `nodes` (integer, 必須)
  - Number of nodes in the project
- `softDeletedNodes` (integer, 必須)
  - Number of soft-deleted nodes in the project
- `instances` (integer, 必須)
  - Total number of instances in the project
- `instancesLimit` (integer, 必須)
  - Maximum number of instances allowed in the project
- `softDeletedInstances` (integer, 必須)
  - Total number of soft-deleted instances in the project
- `softDeletedInstancesLimit` (integer, 必須)
  - Maximum number of soft-deleted instances allowed in the project

---

### InstantParseInput

**プロパティ**:

- `document` (object, 必須)
- `libraryId` (unknown, 必須)
  - The external ID of a library for parsing

---

### InstantParseResult

Temporary diagram entity data resulting from running instant parsing with a library on a file.

**プロパティ**:

- `entities` (array, 必須)
- `message` (string, 任意)
- `status` (unknown, 必須)

---

### InternalEvent

An event represents something that happened at a given interval in time, for example, a failure, a work order, etc.

**プロパティ**:

- `externalId` (unknown, 任意)
- `dataSetId` (unknown, 任意)
  - The id of the dataset this event belongs to.
- `startTime` (unknown, 任意)
- `endTime` (unknown, 任意)
- `type` (unknown, 任意)
- `subtype` (unknown, 任意)
- `description` (string, 任意)
  - Textual description of the event.
- `metadata` (unknown, 任意)
- `assetIds` (array, 任意)
  - Asset IDs of equipment that this event relates to.
- `source` (string, 任意)
  - The source of this event.

---

### InternalId

**プロパティ**:

- `id` (unknown, 必須)

---

### IntractableDirectRelationsCursorNotice

**プロパティ**:

- `code` (string, 必須)
- `category` (string, 必須)
- `level` (string, 必須)
- `grade` (string, 必須)
- `hint` (string, 必須)
- `resultExpression` (string, 必須)

---

### InvalidDebugOptionsNotice

---

### InvertedIndexCreateDefinition

**プロパティ**:

- `properties` (array, 必須)
  - List of properties to define the index across
- `indexType` (string, 任意)
  - An inverted index can be used to index composite values, and the queries to be handled by the index need to search for element values that appear within the composite items. So if for example you have a property X of type `int[]` and you want to efficiently query for all instances where X contains some value Y, you can create an inverted index on X. 

---

### InvertedIndexDefinition

---

### IsNull

**プロパティ**:

- `isNull` (boolean, 任意)
  - Set to true if you want to search for data with field value not set, false to search for cases where some value is present.

---

### ItemType

Status of this item. "Global" means that it is maintained by Cognite and considered official. "Community" means that it is community made and maintained. "Unreleased" means that it is not yet released and it is visible only as a preview or closed beta.

---

### ItemsRequest_CreateExtPipe_

**プロパティ**:

- `items` (array, 必須)

---

### ItemsRequest_ExtPipeRunRequest_

**プロパティ**:

- `items` (array, 必須)

---

### ItemsRequest_ExtPipeUpdate_

**プロパティ**:

- `items` (array, 必須)

---

### ItemsResponse_CreateExtPipeRunResponse_

**プロパティ**:

- `items` (array, 任意)

---

### ItemsResponse_ExtPipeRunResponse_

Response with a list of elements.

**プロパティ**:

- `items` (array, 任意)
- `nextCursor` (string, 任意)
  - The cursor to get the next page of results (if available).

---

### ItemsResponse_Extractor_

List of extractors

**プロパティ**:

- `items` (array, 必須)

---

### ItemsResponse_ListConfigResponse_

**プロパティ**:

- `items` (array, 任意)

---

### ItemsResponse_Release_

List of releases

**プロパティ**:

- `items` (array, 必須)

---

### ItemsResponse_Solution_

List of source systems

**プロパティ**:

- `items` (array, 必須)

---

### ItemsResponse_SourceSystem_

List of source systems

**プロパティ**:

- `items` (array, 必須)

---

### ItemsWithByIdsFlags_CogniteId

All provided IDs and external IDs must be unique.

**プロパティ**:

- `items` (array, 任意)
- `ignoreUnknownIds` (boolean, 任意)
  - Ignore IDs and external IDs that are not found. Defaults to false.
- `withJobDetails` (boolean, 必須)
  - Whether the transformations will be returned with running job and last created job details.

---

### ItemsWithCursor_Destination_

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
  - Cursor for pagination

---

### ItemsWithCursor_Endpoint_

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
  - Cursor for pagination

---

### ItemsWithCursor_Instance_

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
  - Cursor for pagination

---

### ItemsWithCursor_JobRead

**プロパティ**:

- `items` (array, 任意)
- `nextCursor` (string, 任意)
  - Cursor to get the next page of results (if available).

---

### ItemsWithCursor_ListRequestMinimal_

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
  - Cursor for pagination

---

### ItemsWithCursor_Mapping_

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
  - Cursor for pagination

---

### ItemsWithCursor_MinimalJob_

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
  - Cursor for pagination

---

### ItemsWithCursor_NotificationRead

**プロパティ**:

- `items` (array, 任意)
- `nextCursor` (string, 任意)
  - Cursor to get the next page of results (if available).

---

### ItemsWithCursor_Schedule

**プロパティ**:

- `items` (array, 任意)
- `nextCursor` (string, 任意)
  - Cursor to get the next page of results (if available).

---

### ItemsWithCursor_SchemaMapping_

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
  - Cursor for pagination

---

### ItemsWithCursor_Source_

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
  - Cursor for pagination

---

### ItemsWithCursor_Table_

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
  - Cursor for pagination

---

### ItemsWithCursor_TransformationRead

Array of objects (Transformation).

**プロパティ**:

- `items` (array, 任意)
- `nextCursor` (string, 任意)
  - Cursor to get the next page of results (if available).

---

### ItemsWithCursor_UserCreated_

**プロパティ**:

- `items` (array, 必須)

---

### ItemsWithCursor_User_

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
  - Cursor for pagination

---

### ItemsWithIgnoreUnknownIdsAndForce_ExternalId_

**プロパティ**:

- `items` (array, 必須)
- `ignoreUnknownIds` (boolean, 任意)
  - Ignore IDs and external IDs that are not found.
- `force` (boolean, 任意)
  - Delete any jobs associated with each item.

---

### ItemsWithIgnoreUnknownIds_CogniteId

All provided IDs and external IDs must be unique.

**プロパティ**:

- `items` (array, 任意)
- `ignoreUnknownIds` (boolean, 任意)
  - Ignore IDs and external IDs that are not found. Defaults to false.

---

### ItemsWithIgnoreUnknownIds_CogniteInternalId

All provided IDs and external IDs must be unique.

**プロパティ**:

- `items` (array, 任意)
- `ignoreUnknownIds` (boolean, 任意)
  - Ignore IDs and external IDs that are not found. Defaults to false.

---

### ItemsWithIgnoreUnknownIds_ExternalId_

**プロパティ**:

- `items` (array, 必須)
- `ignoreUnknownIds` (boolean, 任意)
  - Ignore IDs and external IDs that are not found

---

### ItemsWithIgnoreUnknownIds_ReleaseId_

List of release ids with optional ignore unknown ids

**プロパティ**:

- `ignoreUnknownIds` (boolean, 任意)
- `items` (array, 必須)

---

### ItemsWithIgnoreUnknownIds_RequestId_

**プロパティ**:

- `items` (array, 必須)
- `ignoreUnknownIds` (boolean, 任意)
  - Ignore IDs and external IDs that are not found

---

### ItemsWithIgnoreUnknown_Tablename_

**プロパティ**:

- `items` (array, 必須)
- `ignoreUnknownIds` (boolean, 任意)
  - Ignore table names not found

---

### ItemsWithIgnoreUnknown_Username_

**プロパティ**:

- `items` (array, 必須)
- `ignoreUnknownIds` (boolean, 任意)
  - Ignore usernames that are not found

---

### ItemsWithIgnoreUnknown_Username_1_

**プロパティ**:

- `items` (array, 必須)
- `ignoreUnknownIds` (boolean, 任意)
  - Ignore usernames that are not found

---

### ItemsWithoutCursor_TableCreated_

**プロパティ**:

- `items` (array, 必須)

---

### ItemsWithoutCursor_Table_

**プロパティ**:

- `items` (array, 必須)

---

### ItemsWithoutCursor_User_

**プロパティ**:

- `items` (array, 必須)

---

### Items_CogniteInternalId

**プロパティ**:

- `items` (array, 任意)

---

### Items_ColumnType

**プロパティ**:

- `items` (array, 任意)

---

### Items_CreateDestination_

**プロパティ**:

- `items` (array, 必須)

---

### Items_CreateEndpoint_

**プロパティ**:

- `items` (array, 必須)

---

### Items_CreateInstance_

**プロパティ**:

- `items` (array, 必須)

---

### Items_CreateJob_

**プロパティ**:

- `items` (array, 必須)

---

### Items_CreateMapping_

**プロパティ**:

- `items` (array, 必須)

---

### Items_CreateRequestResponse_

**プロパティ**:

- `items` (array, 必須)

---

### Items_CreateRequest_

**プロパティ**:

- `items` (array, 必須)

---

### Items_CreateSchemaMapping_

**プロパティ**:

- `items` (array, 必須)

---

### Items_CreateSource_

**プロパティ**:

- `items` (array, 必須)

---

### Items_CreateTable_

**プロパティ**:

- `items` (array, 必須)

---

### Items_CreateUser_

**プロパティ**:

- `items` (array, 必須)

---

### Items_DestinationUpdateItem_

**プロパティ**:

- `items` (array, 必須)

---

### Items_Destination_

**プロパティ**:

- `items` (array, 必須)

---

### Items_Endpoint_

**プロパティ**:

- `items` (array, 必須)

---

### Items_Instance_

**プロパティ**:

- `items` (array, 必須)

---

### Items_JobLogEntry_

**プロパティ**:

- `items` (array, 必須)

---

### Items_JobMetrics_

**プロパティ**:

- `items` (array, 必須)

---

### Items_JobRead

**プロパティ**:

- `items` (array, 任意)

---

### Items_JobUpdateItem_

**プロパティ**:

- `items` (array, 必須)

---

### Items_JsonObject

Query result in json format.

**プロパティ**:

- `items` (array, 任意)

---

### Items_MappingUpdateItem_

**プロパティ**:

- `items` (array, 必須)

---

### Items_Mapping_

**プロパティ**:

- `items` (array, 必須)

---

### Items_MetricCounter

**プロパティ**:

- `items` (array, 任意)

---

### Items_MinimalJob_

**プロパティ**:

- `items` (array, 必須)

---

### Items_NotificationCreate

**プロパティ**:

- `items` (array, 任意)

---

### Items_NotificationRead

**プロパティ**:

- `items` (array, 任意)

---

### Items_RequestFull_

**プロパティ**:

- `items` (array, 必須)

---

### Items_RequestMinimal_

**プロパティ**:

- `items` (array, 必須)

---

### Items_Request_

**プロパティ**:

- `items` (array, 必須)

---

### Items_Schedule

**プロパティ**:

- `items` (array, 任意)

---

### Items_ScheduleParameters

**プロパティ**:

- `items` (array, 任意)

---

### Items_SchemaMapping_

**プロパティ**:

- `items` (array, 必須)

---

### Items_Source_

**プロパティ**:

- `items` (array, 必須)

---

### Items_TransformationCreate

**プロパティ**:

- `items` (array, 任意)

---

### Items_TransformationRead

Array of objects (Transformation).

**プロパティ**:

- `items` (array, 任意)

---

### Items_UpdateItem_ScheduleUpdate

**プロパティ**:

- `items` (array, 任意)

---

### Items_UpdateItem_TransformationUpdate

**プロパティ**:

- `items` (array, 任意)

---

### Items_UpdateSource_

**プロパティ**:

- `items` (array, 必須)

---

### Items_UserUpdate_

**プロパティ**:

- `items` (unknown, 必須)

---

### Job

**プロパティ**:

- `space` (unknown, 必須)
- `externalId` (unknown, 必須)
- `name` (string, 必須)

---

### JobConfig

Source specific job configuration. The type depends on the type of source, and is required for some sources.


---

### JobExternalId

---

### JobId

Contextualization job ID.

---

### JobIdentifier

**プロパティ**:

- `space` (unknown, 必須)
- `externalId` (unknown, 必須)

---

### JobItem

---

### JobLogEntry

**プロパティ**:

- `jobExternalId` (string, 必須)
  - External ID of the job this log entry belongs to.
- `type` (string, 必須)
  - Type of log entry.
`paused` indicates that the job has been stopped manually,
`startup_error` indicates that the job failed to start at all and requires changes to configuration,
`connected` indicates that the job has connected to the source but did not yet receive any data,
`transform_error` indicates that the job received data, but it failed to transform,
`cdf_write_error` indicates that ingesting the data to CDF failed,
`ok` means that data was successfully ingested to CDF.
- `message` (string, 任意)
  - Log message. Not all log entries have messages.
- `createdTime` (unknown, 必須)

---

### JobMetrics

**プロパティ**:

- `jobExternalId` (string, 必須)
  - External ID of the job this metrics batch belongs to.
- `timestamp` (integer, 必須)
  - The number of milliseconds since 00:00:00 Thursday, 1 January 1970, Coordinated Universal Time (UTC), minus leap seconds.
Metrics are from the UTC hour this timestamp is ingest. For example, if this timestamp is at 01:43:15, the metrics batch contains metrics from 01:00:00 to 01:43:15.
- `sourceMessages` (integer, 必須)
  - Messages received from the source.
- `cdfInputValues` (integer, 必須)
  - Destination resources successfully transformed and passed to CDF.
- `cdfRequests` (integer, 必須)
  - Requests made to CDF containing data produced by this job.
- `transformFailures` (integer, 必須)
  - Source messages that failed to transform.
- `cdfWriteFailures` (integer, 必須)
  - Times the destination received data from transformations, but failed to produce a valid request to CDF.
- `cdfSkippedValues` (integer, 必須)
  - Values the destination received from the source, then decided to skip due to data type mismatch, invalid content, or other.
- `cdfFailedValues` (integer, 必須)
  - Values the destination was unable to upload to CDF.
- `cdfUploadedValues` (integer, 必須)
  - Values the destination successfully uploaded to CDF.

---

### JobRead

Details for the last finished job of this transformation

**プロパティ**:

- `id` (integer, 必須)
  - Job numeric ID.
- `uuid` (string, 必須)
  - UUID id of the job.
- `transformationId` (integer, 必須)
  - ID of the transformation that created this job.
- `transformationExternalId` (string, 必須)
  - External ID of the transformation that created this job.
- `sourceProject` (string, 必須)
  - CDF project which is used as source for reading data.
- `destinationProject` (string, 必須)
  - CDF project which is used as destination for writing data.
- `destination` (unknown, 必須)
  - Destination data type.
- `conflictMode` (unknown, 必須)
- `query` (string, 必須)
  - The SQL query being executed.
- `createdTime` (integer, 必須)
  - Time when the job was created: The number of milliseconds since 00:00:00 Thursday, 1 January 1970, Coordinated Universal Time (UTC), minus leap seconds.
- `startedTime` (integer, 任意)
  - Time when this job started executing: The number of milliseconds since 00:00:00 Thursday, 1 January 1970, Coordinated Universal Time (UTC), minus leap seconds.
- `finishedTime` (integer, 任意)
  - Time when this job finished running: The number of milliseconds since 00:00:00 Thursday, 1 January 1970, Coordinated Universal Time (UTC), minus leap seconds.
- `lastSeenTime` (integer, 任意)
  - Time when this job last registered running: The number of milliseconds since 00:00:00 Thursday, 1 January 1970, Coordinated Universal Time (UTC), minus leap seconds.
- `error` (string, 任意)
  - If the job failed, this will be a string describing why. When null, the job did not fail.
- `ignoreNullFields` (boolean, 必須)
  - How NULL values are handled on updates: ignore or setNull.
- `status` (unknown, 必須)

---

### JobStatus

The status of the job.

---

### JobTargetStatus

The target status of a job. Set this to start or stop the job.

---

### JobToken

A string token that can be attached to the header of a request fetching the job status. Authenticates the user fetching the job status as the same one who originally posted the job.

---

### JobUpdate

**プロパティ**:

- `destinationId` (unknown, 任意)
- `format` (unknown, 任意)
- `sourceId` (unknown, 任意)
- `targetStatus` (unknown, 任意)
- `config` (unknown, 任意)

---

### JobUpdateItem

**プロパティ**:

- `externalId` (unknown, 必須)
- `update` (unknown, 必須)

---

### Jobs

---

### JobsList

**プロパティ**:

- `items` (array, 必須)

---

### JsonArrayInt64

---

### JsonArrayString

---

### JsonConfig

Treat the input as UTF-8 encoded JSON.

**プロパティ**:

- `type` (string, 必須)
  - Input type

---

### KafkaAuthentication

Method used for authenticating with the kafka brokers.
This may be used together with auth certificate.

---

### KafkaAuthenticationWrite

Method used for authenticating with the kafka brokers.
This may be used together with auth certificate.

---

### KafkaBroker

**プロパティ**:

- `host` (string, 必須)
  - Host name or IP address of the bootstrap broker.
- `port` (integer, 必須)
  - Port on the bootstrap broker to connect to.

---

### KafkaBrokerList

List of redundant kafka brokers to connect to.

---

### KafkaJobConfig

**プロパティ**:

- `topic` (string, 必須)
  - Kafka topic to connect to
- `partitions` (integer, 任意)
  - Number of partitions on the topic.

---

### KafkaSource

**プロパティ**:

- `type` (string, 必須)
  - Source type.
- `externalId` (unknown, 必須)
- `bootstrapBrokers` (unknown, 必須)
- `authentication` (unknown, 任意)
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)
- `caCertificate` (unknown, 任意)
- `authCertificate` (unknown, 任意)
- `useTls` (boolean, 任意)
  - Indicates whether TLS will be used for connection to broker.

---

### KeycloakIdp

---

### Label

A label assigned to a resource.

**プロパティ**:

- `externalId` (unknown, 必須)
  - An external ID to a predefined label definition.

---

### LabelContainsAllFilter

**プロパティ**:

- `containsAll` (array, 必須)
  - The resource item contains at least all the listed labels.

---

### LabelContainsAnyFilter

**プロパティ**:

- `containsAny` (array, 必須)
  - The resource item contains at least one of the listed labels.

---

### LabelDefinition

---

### LabelDefinitionCreateList

**プロパティ**:

- `items` (array, 必須)

---

### LabelDefinitionExternalId

**プロパティ**:

- `externalId` (unknown, 必須)

---

### LabelDefinitionExternalIdList

**プロパティ**:

- `items` (array, 必須)

---

### LabelDefinitionFilter

**プロパティ**:

- `filter` (object, 任意)
  - Filter on labels definitions with strict matching.

---

### LabelDefinitionList

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
  - The cursor to get the next page of results (if available).

---

### LabelDefinitionListScope

---

### LabelFilter

Return only the resource matching the specified label constraints.

---

### LabelList

A list of the labels associated with this resource item.

---

### LabelSelectByExternalId

**プロパティ**:

- `externalId` (unknown, 任意)

---

### LabelsAddRemove

**プロパティ**:

- `add` (array, 任意)
  - A list of the labels to add to a resource.
- `remove` (array, 任意)
  - A list of the labels to remove from a resource.

---

### LabelsIdsWithIgnoreUnknownIds

---

### LabelsPatch

Updates the resource's assigned labels.

Labels can be added, removed or replaced (set). Adding an already attached label is an idempotent operation. Removing a label with no matching externalId is silently ignored.

---

### LabelsSet

**プロパティ**:

- `set` (array, 任意)
  - A list of the labels to replace (set) to a resource.

---

### LastUpdatedTimeFilter

Matches records with the last updated time within the provided range.
This attribute is mandatory for immutable streams but it's optional for **mutable** streams.
The maximum time interval that can be defined by the attribute
is limited by the stream settings. If more data needs to be queried than allowed by the stream
settings, it should be done with multiple requests. For example, if stream allows querying
up to 1 month of data, but a quarterly report is needed, the solution is to make 3 requests, one for
each month, and then aggregate the responses.

The range must include at least a left (`gt` or `gte`) bound.
It is not allowed to specify two upper or lower bounds, e.g. `gte` and `gt`, in the same filter.

  `gte`: Greater than or equal to.
  `gt`: Greater than.
  `lte`: Less than or equal to.
  `lt`: Less than.

**Note:** `lastUpdatedTime` is a CDF generated timestamp that marks the moment a record is stored in the API

The range bounds can be specified in two formats:
- **String**: An ISO-8601 formatted date-time string (e.g., `2030-05-15T18:00:00.00Z`).
- **Integer**: The number of milliseconds since the Unix epoch (e.g., `1705341600000`).


**プロパティ**:

- `gte` (unknown, 任意)
- `gt` (unknown, 任意)
- `lte` (unknown, 任意)
- `lt` (unknown, 任意)

---

### LastUpdatedTimeRangeValue

Timestamp value you wish to find in the ```record``` last updated time using a range clause.


---

### LatestDataBeforeRequest

Describes the latest query.

---

### LatestDataPropertyFilter

**プロパティ**:

- `before` (string, 任意)
  - Get data points before this time. The format is N[timeunit]-ago where timeunit is w,d,h,m,s.
Example: '2d-ago' gets data that is up to two days old. You can also specify time in milliseconds since epoch.
- `targetUnit` (string, 任意)
  - The [unit](<https://developer.cognite.com/dev/concepts/resource_types/units/>) externalId of the data points returned. If the time series does not have a unitExternalId that can be converted to the targetUnit, an error will be returned. Cannot be used with targetUnitSystem.
- `targetUnitSystem` (string, 任意)
  - The [unit system](<https://developer.cognite.com/dev/concepts/resource_types/units/>) of the data points returned. Cannot be used with targetUnit.
- `includeStatus` (boolean, 任意)
  - Show the [status code](<https://developer.cognite.com/dev/concepts/reference/status_codes>) for each data point in the response.
_Good_ (code = 0) status codes are always omitted.
- `ignoreBadDataPoints` (boolean, 任意)
  - Treat data points with a _Bad_ status code as if they do not exist.
Set to false to include all data points.

Note that bad data points may contain the string values 'NaN', 'Infinity', or '-Infinity', or be null (omitted).
- `treatUncertainAsBad` (boolean, 任意)
  - Treat data points with _Uncertain_ status codes as _Bad_.
Set to false to to include uncertain data points.

---

### LeafFilterIla

Leaf filter which is used in boolean filters to build advanced queries to filter out some records.


---

### LegacyAggregationFields

**プロパティ**:

- `fields` (array, 必須)
  - This field is deprecated. Use the `properties` field instead.\
The field name(s) to apply the aggregation on. Currently limited to one field.
Accepts the following field names: `type`, `subtype`, `dataSetId` and metadata fields, for example, `metadata.FooBar`.


---

### LevelGaugeDetection

Detect and read value of level gauges in images. In beta. Available only when the `cdf-version: beta` header is provided.

---

### LevelGaugeDetectionParameters

Parameters for level gauge detection and reading. In beta. Available only when the `cdf-version: beta` header is provided.

**プロパティ**:

- `minLevel` (unknown, 任意)
- `maxLevel` (unknown, 任意)
- `threshold` (unknown, 任意)

---

### LibraryCreateDraft

**プロパティ**:

- `externalId` (unknown, 必須)
  - The external ID of the library
- `name` (string, 必須)
  - The name of the library

---

### LibraryDto

**プロパティ**:

- `createdTime` (unknown, 必須)
- `externalId` (unknown, 必須)
  - The external ID of the library
- `lastUpdatedTime` (unknown, 必須)
- `name` (string, 必須)
  - The name of the library
- `scope` (unknown, 必須)
  - Global or project scope where the library is available

---

### LibraryScope

Global or project scope where library is available

---

### LibraryUpdateDraft

**プロパティ**:

- `name` (string, 任意)
  - The name of the library

---

### LibraryUpdateItem

**プロパティ**:

- `externalId` (unknown, 必須)
  - The external ID of the library
- `update` (unknown, 必須)
  - The properties to be updated

---

### Limit

**プロパティ**:

- `limit` (integer, 任意)
  - Limits the number of results to return.

---

### LimitList

**プロパティ**:

- `limit` (integer, 任意)
  - Limits the number of results to be returned.

---

### LimitWithDefault10

**プロパティ**:

- `limit` (integer, 任意)
  - Limits the number of results to return.


---

### LimitWithDefault1000

**プロパティ**:

- `limit` (integer, 任意)
  - Limits the number of results to return.

---

### LineString

**プロパティ**:

- `type` (string, 必須)
- `coordinates` (unknown, 必須)

---

### LineStringCoordinates

Coordinates of a line described by a list of two or more points.
Each point is defined as a pair of two numbers in an array, representing coordinates of a point in 2D space.

Example: `[[30, 10], [10, 30], [40, 40]]`


---

### Link

Link to an external resource for an extractor

**プロパティ**:

- `name` (string, 必須)
  - Human readable link name.
- `type` (unknown, 必須)
- `url` (string, 必須)
  - Link URL.

---

### LinkType

Enumeration of link types.

---

### ListAdvancedJoinMatchesResponseSchema

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
  - A cursor that can be passed to this endpoint to get the next page of items.

---

### ListAdvancedJoinsResponseSchema

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
  - A cursor that can be passed to this endpoint to get the next page of items.

---

### ListAggregateCount

**プロパティ**:

- `items` (array, 必須)

---

### ListConfigResponse

**プロパティ**:

- `externalId` (string, 必須)
  - External ID of the extraction pipeline this configuration revision belongs to.
- `revision` (integer, 必須)
  - Revision number of this configuration.
- `createdTime` (unknown, 任意)
- `description` (string, 任意)
  - A description of this configuration revision.

---

### ListExecutionsFilter

**プロパティ**:

- `workflowFilters` (array, 任意)
  - Allows filtering executions by their workflows (and optionally version identifiers). If no workflowFilters are specified, all executions for all workflows will be included, ordered by createdTime.
- `createdTimeStart` (integer, 任意)
  - epoch timestamp in milliseconds
- `createdTimeEnd` (integer, 任意)
  - epoch timestamp in milliseconds
- `status` (array, 任意)
  - workflow execution status

---

### ListExecutionsQuery

**プロパティ**:

- `filter` (unknown, 任意)
- `limit` (unknown, 任意)
- `cursor` (unknown, 任意)

---

### ListJobResultRejectionsRequest

---

### ListJobResultRejectionsWithCursorResponse

---

### ListJobResultsRequest

---

### ListJobResultsWithCursorResponse

---

### ListJobsRequest

---

### ListJobsWithCursorResponse

---

### ListOfAllVersionsReferences

**プロパティ**:

- `items` (array, 必須)

---

### ListOfContainerSubObjectIdentifierRequest

**プロパティ**:

- `items` (array, 必須)

---

### ListOfSpaceExternalIdsRequest

**プロパティ**:

- `items` (array, 必須)

---

### ListOfSpaceExternalIdsRequestWithTyping

**プロパティ**:

- `sources` (unknown, 任意)
  - The node/edge must have data in all the sources defined in the list
- `items` (array, 必須)
- `includeTyping` (unknown, 任意)

---

### ListOfSpaceIdsRequest

**プロパティ**:

- `items` (array, 必須)

---

### ListOfVersionReferences

**プロパティ**:

- `items` (array, 必須)

---

### ListSimulationRunDataItem

**プロパティ**:

- `items` (array, 必須)

---

### ListSimulationRunView

**プロパティ**:

- `items` (array, 必須)

---

### ListSimulationRunsFilters

**プロパティ**:

- `status` (unknown, 任意)
  - Filter by simulation run status
- `runType` (unknown, 任意)
  - Filter by simulation run type
- `simulatorIntegrationExternalIds` (array, 任意)
  - Filter by simulator integration external ids
- `simulatorExternalIds` (array, 任意)
  - Filter by simulator external ids
- `modelExternalIds` (array, 任意)
  - Filter by simulator model external ids
- `routineExternalIds` (array, 任意)
  - Filter by simulator routine external ids
- `routineRevisionExternalIds` (array, 任意)
  - Filter by routine revision external ids
- `modelRevisionExternalIds` (array, 任意)
  - Filter by model revision external ids
- `createdTime` (unknown, 任意)
  - Filter by created time range
- `simulationTime` (unknown, 任意)
  - Filter by simulation time range

---

### ListSimulationRunsQuery

**プロパティ**:

- `filter` (unknown, 任意)
- `limit` (integer, 任意)
- `cursor` (string, 任意)
  - Cursor for pagination
- `sort` (array, 任意)
  - Only supports sorting by one property at a time

---

### ListSimulator

**プロパティ**:

- `items` (array, 必須)

---

### ListSimulatorIntegration

**プロパティ**:

- `items` (array, 必須)

---

### ListSimulatorIntegrationsFilters

**プロパティ**:

- `simulatorExternalIds` (array, 任意)
  - Filter by simulator external ids
- `active` (boolean, 任意)
  - Filter by whether the simulator integration is active or not. Connector is considered active if the heartbeat was reported within the last minute

---

### ListSimulatorIntegrationsQuery

**プロパティ**:

- `filter` (unknown, 任意)
- `limit` (integer, 任意)

---

### ListSimulatorLog

**プロパティ**:

- `items` (array, 必須)

---

### ListSimulatorModel

**プロパティ**:

- `items` (array, 必須)

---

### ListSimulatorModelRevision

**プロパティ**:

- `items` (array, 必須)

---

### ListSimulatorModelRevisionDataView

**プロパティ**:

- `items` (array, 必須)

---

### ListSimulatorModelRevisionsFilters

**プロパティ**:

- `modelExternalIds` (array, 任意)
  - Filter by model external ids
- `simulatorExternalIds` (array, 任意)
  - Filter by simulator external ids
- `allVersions` (boolean, 任意)
  - If all versions of the entity should be returned. Defaults to false which returns only the latest revision for each model.
- `createdTime` (unknown, 任意)
  - Filter by created time range
- `lastUpdatedTime` (unknown, 任意)
  - Filter by last updated time range

---

### ListSimulatorModelRevisionsQuery

**プロパティ**:

- `filter` (unknown, 任意)
- `limit` (integer, 任意)
- `cursor` (string, 任意)
  - Cursor for pagination
- `sort` (array, 任意)
  - Only supports sorting by one property at a time

---

### ListSimulatorModelsFilters

**プロパティ**:

- `simulatorExternalIds` (array, 任意)
  - Filter by simulator external ids
- `externalIdPrefix` (string, 任意)
  - Filter by prefix on simulator model external ids
- `dataSetIds` (array, 任意)
  - Filter by data set IDs

---

### ListSimulatorModelsQuery

**プロパティ**:

- `filter` (unknown, 任意)
- `limit` (integer, 任意)
- `cursor` (string, 任意)
  - Cursor for pagination
- `sort` (array, 任意)
  - Only supports sorting by one property at a time

---

### ListSimulatorRoutineBase

**プロパティ**:

- `items` (array, 必須)

---

### ListSimulatorRoutineRevision

**プロパティ**:

- `items` (array, 必須)

---

### ListSimulatorRoutineRevisionsFilters

**プロパティ**:

- `routineExternalIds` (array, 任意)
  - Filter by simulator routine external ids
- `allVersions` (boolean, 任意)
  - If all versions of the entity should be returned. Defaults to false which returns only the latest revision for each routine.
- `modelExternalIds` (array, 任意)
  - Filter by simulator model external ids
- `simulatorIntegrationExternalIds` (array, 任意)
  - Filter by simulator integration external ids
- `simulatorExternalIds` (array, 任意)
  - Filter by simulator external ids
- `createdTime` (unknown, 任意)
  - Filter by created time range

---

### ListSimulatorRoutineRevisionsQuery

**プロパティ**:

- `filter` (unknown, 任意)
- `limit` (integer, 任意)
- `cursor` (string, 任意)
  - Cursor for pagination
- `sort` (array, 任意)
  - Only supports sorting by one property at a time
- `includeAllFields` (boolean, 任意)
  - If all fields should be included in the response. Defaults to false which does not include script, configuration.inputs and configuration.outputs in the response. If true - all fields are returned, but routines with kind 'long' will be excluded from the results.

---

### ListSimulatorRoutinesFilters

**プロパティ**:

- `modelExternalIds` (array, 任意)
  - Filter by simulator model external ids
- `simulatorIntegrationExternalIds` (array, 任意)
  - Filter by simulator integration external ids
- `simulatorExternalIds` (array, 任意)
  - Filter by simulator external ids

---

### ListSimulatorRoutinesQuery

**プロパティ**:

- `filter` (unknown, 任意)
- `limit` (integer, 任意)
- `cursor` (string, 任意)
  - Cursor for pagination
- `sort` (array, 任意)
  - Only supports sorting by one property at a time

---

### ListSimulatorsFilters

**プロパティ**:


---

### ListSimulatorsQuery

**プロパティ**:

- `filter` (unknown, 任意)
- `limit` (integer, 任意)

---

### ListSubscriptionDTO

List of subscriptions.

**プロパティ**:

- `items` (array, 必須)

---

### ListSubscriptionDTOWithCursor

A list of subscriptions along with possible cursors to get the next page of result

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
  - The cursor to get the next page of results (if available).

---

### ListSubscriptionMemberDTOWithCursor

A list of member time series along with possible cursors to get the next page of result

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
  - The cursor to get the next page of results (if available).

---

### ListVersionsFilter

**プロパティ**:

- `workflowFilters` (array, 任意)
  - Allows filtering versions by their specific workflows (and optionally version identifiers). If no workflowFilters are specified, all versions for all workflows will be included, ordered by createdTime.

---

### ListVersionsQuery

**プロパティ**:

- `filter` (unknown, 任意)
- `limit` (unknown, 任意)
- `cursor` (unknown, 任意)

---

### ListWithCursorSimulationRunView

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)

---

### ListWithCursorSimulatorModel

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)

---

### ListWithCursorSimulatorModelRevisionView

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)

---

### ListWithCursorSimulatorRoutineBase

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)

---

### ListWithCursorSimulatorRoutineRevision

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)

---

### ListWithCursorSimulatorRoutineRevisionView

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)

---

### List_CreateRequestItemResponse_

---

### List_CreateRequestItem_

---

### List_RequestItemFull_

---

### List_RequestItemMinimal_

---

### List_RequestItem_

---

### LocationAI

**プロパティ**:

- `bottom` (number, 必須)
- `left` (number, 必須)
- `pageNumber` (integer, 必須)
  - The page number within the file. Page numbers start at 1.
- `right` (number, 必須)
- `top` (number, 必須)

---

### LocationFilterAssetCentric

The filter definition for asset centric resource types

**プロパティ**:

- `assets` (unknown, 任意)
- `events` (unknown, 任意)
- `files` (unknown, 任意)
- `timeseries` (unknown, 任意)
- `sequences` (unknown, 任意)

---

### LocationFilterAssetCentricBaseFilter

**プロパティ**:

- `dataSetIds` (array, 任意)
  - The list of data set IDs
- `assetSubtreeIds` (array, 任意)
  - The list of asset subtree IDs
- `externalIdPrefix` (string, 任意)
  - The external ID prefix

---

### LocationFilterDataModel

**プロパティ**:

- `externalId` (string, 必須)
  - The external ID of the data model
- `space` (string, 必須)
  - The space of the data model
- `version` (string, 必須)
  - The version of the data model

---

### LocationFilterRead

**プロパティ**:

- `id` (unknown, 必須)
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)

---

### LocationFilterScene

The scene config for the location filter

**プロパティ**:

- `externalId` (string, 必須)
  - The external ID of the scene
- `space` (string, 必須)
  - The space that the scene is in

---

### LocationFilterView

The view mappings for the location filter

**プロパティ**:

- `externalId` (string, 必須)
  - The external ID of the view
- `space` (string, 必須)
  - The space that the view belongs to
- `version` (string, 必須)
  - The version of the view
- `representsEntity` (string, 必須)

---

### LocationFilterWrite

**プロパティ**:

- `externalId` (unknown, 必須)
- `name` (string, 必須)
  - The name of the location filter
- `parentId` (unknown, 任意)
  - The ID of the parent location filter
- `description` (string, 任意)
  - The description of the location filter
- `dataModels` (array, 任意)
  - The data models in the location filter
- `instanceSpaces` (array, 任意)
  - The list of spaces that instances are in
- `scene` (unknown, 任意)
- `assetCentric` (unknown, 任意)
- `views` (array, 任意)
  - The list of view mappings

---

### LoginSessionDto

**プロパティ**:

- `id` (unknown, 必須)
- `createdTime` (integer, 必須)
  - The Unix timestamp (in milliseconds) when the login session was created (during the user's initial login).
- `status` (string, 必須)
  - Login session status.

- `ACTIVE`: The login session is active. The access token that was issued during login may be used to send
  requests to the Cognite API, the refresh token may be used to obtain a new access token, and any
  background jobs started from this login session will keep running.
- `LOGGED_OUT`: The user logged themselves out. The access token that was issued during login may no longer
  be used to send requests to the Cognite API, and the refresh token may no longer be used to obtain a new
  access token. However, any background jobs started from this login session will keep running.
- `EXPIRED`: Cognite failed to refresh the user's session in the organization's external identity provider
  (the customer's Entra ID tenant or other identity provider service), and this login session is now
  completely unusable. Like for `LOGGED_OUT`, the access and refresh tokens may no longer be used. Unlike
  for `LOGGED_OUT`, any background jobs started from this login session will stop running.
- `REVOKED`: The login session was revoked by an organization administrator via the
  `/api/v1/orgs/{org}/principals/{principal}/sessions/revoke` endpoint, and this login session is now
  completely unusable. Like for `EXPIRED`, the access and refresh tokens may no longer be used, and any
  background jobs started from this login session will stop running.
- `deactivatedTime` (integer, 任意)
  - The Unix timestamp (in milliseconds) when the login session was deactivated,
or `null` or absent if `status` is `ACTIVE`.

---

### LoginSessionId

Unique identifier of a login session.

---

### LoginSessionsListPaginatedDto

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (unknown, 任意)

---

### MQTTJobConfig

**プロパティ**:

- `topicFilter` (string, 必須)
  - Topic filter.

---

### MapUpdate

Custom, application specific metadata. String key -> String value. Limits of updated extraction pipeline: Maximum length of key is 128 bytes, value 10240 bytes, up to 256 key-value pairs, of total size at most 10240.

---

### MapUpdateAddRemove

**プロパティ**:

- `add` (object, 任意)
  - Add the key-value pairs. Values for existing keys will be overwritten.
- `remove` (array, 任意)
  - Remove the key-value pairs with the specified keys.

---

### MapUpdateSet

**プロパティ**:

- `set` (object, 任意)
  - Custom, application specific metadata. String key -> String value. Limits: Key are at most 128 bytes. Values are at most 10240 bytes. Up to 256 key-value pairs. Total size is at most 10240.

---

### Mapping

**プロパティ**:

- `externalId` (unknown, 必須)
- `mapping` (unknown, 必須)
- `input` (unknown, 必須)
- `published` (boolean, 必須)
  - Whether this mapping is published and should be available to be used in jobs.
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)

---

### MappingUpdateItem

**プロパティ**:

- `externalId` (unknown, 必須)
- `update` (unknown, 必須)

---

### Match

A pair of source ID and target ID, that indicates a match between two entities in the source and target spaces.
Internal and external IDs are supported.

---

### MatchAllFilter

**プロパティ**:

- `matchAll` (object, 必須)
  - All the listed items must match the clause.

---

### MatchCondition

**プロパティ**:

- `conditionType` (string, 必須)
  - The type of condition. Currently, we only support the 'equals' condition. It requires all features referenced by its arguments to be equal.
- `arguments` (array, 必須)
  - References to features. Each argument is a list with two zero indexed indices. E.g. [0, 1] refers to the 1th (second) feature produced by the 0th (first) extractor of the rule.
- `config` (unknown, 任意)

---

### MatchConditionConfig

Configuration of the condition not captured in its arguments and type.

**プロパティ**:

- `synonyms` (array, 任意)
  - Entries describing tokens that are equivalent.

---

### MatchFields

List of pairs of fields from the target and source items, used to calculate features. All source and target items should have all the `source` and `target` fields specified here.

---

### MatchRule

An entity matching rule matches source entities with target entities based on a pattern defined by the rule.
A rule has extractors that both require entities to follow a certain format, and produce lists of features.

For a regex extractor, a specific field must match a pattern, and parts of the pattern become the features.
The conditions of a rule express which extracted features must be equal for two entities to match.

Since extractors restrict the scopes of rules to entities that conform with patterns, two rules will often not
have entities in common that they apply to. But if they do, the more specific rule is given higher priority.

If one rule is not a special case of the other, they may be inconsistent (conflict) or redundant (overlap).

**プロパティ**:

- `extractors` (array, 必須)
  - An extractor produces a list of features based on a source entity or a target entity. In particular a regex extractor defines which entities (e.g. sources) it acts on, a field (e.g. name) and a regular expression. The extractor will only produce features if the field of an entity matches the pattern. The returned features are then substrings of the field as defined in the pattern. Entities in the relevant set that do not match the pattern will not be matched by the rule, but may be matched by other rules.
- `conditions` (array, 必須)
  - List of conditions that must be met for the rule to link two entities. The conditions refer to features extracted by the extractors. A condition of type "equals" that refers to features extracted from a source entity and a target entity requires these features to be equal for the entities to match.
- `priority` (number, 任意)
  - In case different rules provide matches for the same source entities, only the highest priority rules are applied to these entities.

---

### MatchRules

A collection of match rules

---

### Matcher

---

### MaxAggregate

**プロパティ**:

- `max` (object, 必須)
  - The function will calculate, and return, the highest - max - value for the property.

---

### MaxAggregateFunctionV3

The function will calculate, and return, the highest - max - value for the property.

**プロパティ**:

- `max` (object, 必須)

---

### MaxAggregateIla

**プロパティ**:

- `max` (object, 必須)
  - The function will calculate, and return, the highest - max - value for the property.

<u>Example:</u>

```
{
  "my_player_scores_max": {
    "max": { "property": [ "football", "game_statistics", "points_scored" ] }
  },
  "my_player_age_max": {
    "max": { "property": [ "football", "game_statistics", "player_age" ] }
  }
}
```


---

### MaxAggregateResult

**プロパティ**:

- `max` (number, 必須)
  - The highest - max - value for the specified in the request property.

---

### MaxAggregateResultIla

**プロパティ**:

- `max` (number, 必須)
  - The highest - max - value for the specified in the request property.


---

### MaxLevel

The max value of the gauge.

---

### MaxNumDigits

Maximum number of digits on a digital gauge.

---

### MeasureMappedPercentageRequestSchema

**プロパティ**:

- `space` (unknown, 必須)
- `viewExternalId` (unknown, 必須)
- `viewVersion` (unknown, 必須)
- `dummyResponse` (boolean, 任意)
  - Whether to return a bogus response that complies with the expected schema.
This will be removed in a future iteration.

---

### MeasureMappedPercentageResponseSchema

---

### MemoryRange

The amount of available memory in GB per function execution (i.e. function call).

**プロパティ**:

- `min` (number, 必須)
  - The minimum value you can request when creating a function.
- `max` (number, 必須)
  - The maximum value you can request when creating a function.
- `default` (number, 必須)
  - The default value when creating a function.

---

### MetaData

Custom, application specific metadata. String key -> String value. Limits: Maximum length of key is 32, value 512 characters, up to 16 key-value pairs. Maximum size of entire metadata is 4096 bytes.

---

### Metadata3D

Custom, application specific metadata. String key -> String value. Limits: Maximum length of key is 32 bytes, value 512 bytes, up to 16 key-value pairs.

---

### MetricCounter

**プロパティ**:

- `timestamp` (integer, 必須)
  - Time when this metric was recorded: The number of milliseconds since 00:00:00 Thursday, 1 January 1970, Coordinated Universal Time (UTC), minus leap seconds.
- `name` (string, 必須)
  - Name of the metric. Metrics like `assets.read` mean how many items were fetched from CDF;`assets.create` and `assets.update` inform how many resources were created or updated (note that we count objects as updated even when no field is changed). `requests` says how many HTTP request were made to CDF to complete the job. `requestWithoutRetries` does not count retried requests. Normally, these two metrics should be almost equal, if there is a big difference, it may indicate a problem with rate-limiting.
- `count` (integer, 必須)
  - The value of this metric

---

### MigrationStatus

This attribute will be removed in a future version, and it is recommended to not send it in requests and to ignore it in responses.
If it is present, the single valid value is equivalent to the attribute being null or absent.

---

### MimeType

File type. E.g. text/plain, application/pdf, ..

---

### MinAggregate

**プロパティ**:

- `min` (object, 必須)
  - The function will calculate, and return, the lowest - min - value for a property.

---

### MinAggregateFunctionV3

The function will calculate, and return, the lowest - min - value for a property.

**プロパティ**:

- `min` (object, 必須)

---

### MinAggregateIla

**プロパティ**:

- `min` (object, 必須)
  - The function will calculate, and return, the lowest - min - value for a property.

<u>Example:</u>

```
{
  "my_player_age_min": {
    "min": { "property": [ "football", "game_statistics", "player_age" ] }
  }
}
```


---

### MinAggregateResult

**プロパティ**:

- `min` (number, 必須)
  - The lowest - min - value for the specified in the request property.

---

### MinAggregateResultIla

**プロパティ**:

- `min` (number, 必須)
  - The lowest - min - value for the specified in the request property.


---

### MinLevel

The min value of the gauge.

---

### MinMax

Range between two timestamps (inclusive).

**プロパティ**:

- `min` (integer, 任意)
  - Minimum timestamp (inclusive).
The number of milliseconds since 00:00:00 Thursday, 1 January 1970, Coordinated Universal Time (UTC), minus leap seconds.
- `max` (integer, 任意)
  - Maximum timestamp (inclusive).
The number of milliseconds since 00:00:00 Thursday, 1 January 1970, Coordinated Universal Time (UTC), minus leap seconds.

---

### MinMaxAggregatePropertyIla

Property a client wants to aggregate.
To represent fully qualified properties, Records use arrays of strings.

The aggregate supports container level properties, as well as `["createdTime"]`
and `["lastUpdatedTime"]` top level properties.

A container level property is defined by 3 segments:
  - the first segment is the space that the container belongs to,
  - the second segment is the container external id,
  - the third segment is the property id inside the container.

<u>Example:</u>

You have the schema
```
{
  "mySpace": {
    "myContainer": {
      "myProperty": {...}
    }
  }
}
```

Use `["mySpace", "myContainer", "myProperty"]` array to aggregate the values for `myProperty` property.


---

### MinNumDigits

Minimum number of digits on a digital gauge.

---

### MinimalJob

**プロパティ**:

- `externalId` (unknown, 必須)
- `destinationId` (string, 必須)
  - ID of the destination this job writes to.
- `sourceId` (string, 必須)
  - ID of the source this job reads from.
- `format` (object, 必須)
  - The format of the messages from the source. This is used to convert messages coming from the source system to a format that can be inserted into CDF.
- `targetStatus` (unknown, 必須)
- `status` (string, 必須)
  - Status of this job.
- `config` (unknown, 必須)
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)

---

### MinimalPreview

Preview create response

**プロパティ**:

- `externalId` (unknown, 必須)
- `sourceId` (string, 必須)
  - ID of the source this preview job should read from.
- `config` (unknown, 必須)
- `format` (object, 任意)
  - Input format parameters.
- `input` (unknown, 必須)
- `createdTime` (unknown, 必須)

---

### Model3D

**プロパティ**:

- `name` (string, 必須)
  - The name of the model.
- `id` (integer, 必須)
  - The ID of the model.
- `createdTime` (unknown, 必須)
- `dataSetId` (unknown, 任意)
- `space` (unknown, 任意)
- `metadata` (unknown, 任意)

---

### Model3DList

**プロパティ**:

- `items` (array, 必須)

---

### Model3DOutputResponseList

**プロパティ**:

- `items` (array, 必須)
  - A list of named and versioned outputs for the model. Note that the list is not sorted.

---

### Model3DWithCursorResponse

---

### Model3DWithRevisionInfo

---

### Model3DWithRevisionInfoAndCursorResponse

---

### ModelChange

---

### ModelChangeByExternalId

---

### ModelChangeById

---

### ModelDescription

User defined description.

---

### ModelId

The ID of the model.

---

### ModelName

User defined name.

---

### ModelParameters

The parameters to use in the entity matching model.

**プロパティ**:

- `externalId` (unknown, 任意)
- `name` (string, 任意)
- `description` (string, 任意)
- `featureType` (unknown, 任意)
- `classifier` (unknown, 任意)
- `matchFields` (unknown, 任意)

---

### ModelPatch

Changes applied to model

**プロパティ**:

- `update` (object, 必須)

---

### ModelType

---

### ModifyOidcConfigurationUpdateDTO

**プロパティ**:

- `modify` (unknown, 必須)

---

### ModifyPatchBoolean

Set a new value for the boolean, or remove the value

---

### ModifyPatchClaimNames

---

### ModifyPatchInteger

Set a new value for the integer, or remove the value

---

### ModifyReservedPrefix

**プロパティ**:

- `set` (string, 必須)
  - Reserves a prefix that can only be used by projects that are children of the project hierachy starting at
this project (reserved prefixes *MUST* only be specified for non-leaf projects).

Projects outside this project hierachy will be rejected to create projects with url names starting with
this reserved prefix.

Only allowed for non-leaf projects. Currently only permitted to be updated by cluster administrators.


---

### ModifyUserProfilesConfigurationDTO

**プロパティ**:

- `modify` (unknown, 必須)

---

### MovingFunctionAggregateIla

**プロパティ**:

- `movingFunction` (object, 必須)
  - Given an ordered series of data, the Moving Function aggregation will slide a window across the data and allow the user to specify a function
that is executed on each window of data.
A number of common functions are predefined such as min/max, moving averages, etc. Customer defined functions are not allowed now.
The aggregate must be embedded inside of a numberHistogram or timeHistogram aggregate. It can be embedded like any other metric aggregate.

The idea of this pipeline aggregate is borrowed from Elasticsearch.
The information about the concept can be read [here](https://www.elastic.co/guide/en/elasticsearch/reference/8.18/search-aggregations-pipeline-movfn-aggregation.html).

<u>Example:</u>

Imagine there is an `error_log` container which represents errors in a service and has the following model:
```
{
  "timestamp": Instant,
  "severity": Long,
  "message": String,
  ...
}
```

Let's assume you have a daily error log count aggregate
```
{
  "my_daily_error_logs": {
    "timeHistogram": {
      "calendarInterval": "1d",
      "property": [ "logs", "error_log", "timestamp" ]
    }
  }
}
```

which returns the following result:
```
{
  "my_daily_error_logs": {
    "timeHistogramBuckets": [
      {
        "intervalStart": "2025-05-25T00:00:00Z",
        "count": 10
      },
      {
        "intervalStart": "2025-05-26T00:00:00Z",
        "count": 5
      },
      {
        "intervalStart": "2025-05-27T00:00:00Z",
        "count": 12
      },
      {
        "intervalStart": "2025-05-28T00:00:00Z",
        "count": 1
      },
      {
        "intervalStart": "2025-05-29T00:00:00Z",
        "count": 20
      },
      ...
    ]
  }
}
```

If you would like to get the average number of error logs during the last 3 days,
you may add a ```movingFunction``` pipeline aggregate to your request:
```
{
  "my_daily_error_logs": {
    "timeHistogram": {
      "calendarInterval": "1d",
      "property": [ "logs", "error_log", "timestamp" ],
      "aggregates": {
        "my_moving_last_3days_average_errors_count": {
          "movingFunction": {
            "bucketsPath": "_count",
            "window": 3,
            "function": "MovingFunctions.unweightedAvg"
          }
        }
      }
    }
  }
}
```

which would return the following result:
```
{
  "my_daily_error_logs": {
    "timeHistogramBuckets": [
      {
        "intervalStart": "2025-05-25T00:00:00Z",
        "count": 10,
        "aggregates": {
          "my_moving_last_3days_average_errors_count": {
            // Note: in case of `MovingFunctions.unweightedAvg`,
            //  the first bucket always contains 0 as `fnValue`
            //  because to calculate the average at least 2 buckets are necessary.
            "fnValue": 0
          }
        }
      },
      {
        "intervalStart": "2025-05-26T00:00:00Z",
        "count": 5,
        "aggregates": {
          "my_moving_last_3days_average_errors_count": {
            // Note: the window is 3 but to calculate the average value for the second bucket
            //  only 2 buckets are used.
            "fnValue": 7.5
          }
        }
      },
      {
        "intervalStart": "2025-05-27T00:00:00Z",
        "count": 12,
        "aggregates": {
          "my_moving_last_3days_average_errors_count": {
            "fnValue": 9
          }
        }
      },
      {
        "intervalStart": "2025-05-28T00:00:00Z",
        "count": 1,
        "aggregates": {
          "my_moving_last_3days_average_errors_count": {
            "fnValue": 6
          }
        }
      },
      {
        "intervalStart": "2025-05-29T00:00:00Z",
        "count": 20,
        "aggregates": {
          "my_moving_last_3days_average_errors_count": {
            "fnValue": 11
          }
        }
      },
      ...
    ]
  }
}
```

If you also would like get the average daily maximum severity value of error logs during the entire period (covered by buckets),
you may add one more ```movingFunction``` pipeline aggregate and one ```max``` metric sub-aggregate to your request:
```
{
  "my_daily_error_logs": {
    "timeHistogram": {
      "calendarInterval": "1d",
      "property": [ "logs", "error_log", "timestamp" ],
      "aggregates": {
        "my_max_daily_severity": {
          "max": { "property": [ "logs", "error_log", "severity" ] }
        },
        "my_moving_average_max_daily_errors_severity": {
          "movingFunction": {
            "bucketsPath": "my_max_daily_severity",
            "window": 100000, // all buckets must be inside the single window
            "function": "MovingFunctions.unweightedAvg"
          }
        },
        "my_moving_last_3days_average_errors_count": {
          "movingFunction": {
            "bucketsPath": "_count",
            "window": 3,
            "function": "MovingFunctions.unweightedAvg"
          }
        }
      }
    }
  }
}
```

which would return the following result:
```
{
  "my_daily_error_logs": {
    "timeHistogramBuckets": [
      {
        "intervalStart": "2025-05-25T00:00:00Z",
        "count": 10,
        "aggregates": {
          "my_max_daily_severity": {
            "max": 13
          },
          "my_moving_average_max_daily_errors_severity": {
            // for the chosen moving function the first bucket always contains 0
            "fnValue": 0
          },
          "my_moving_last_3days_average_errors_count": {
            // for the chosen moving function the first bucket is always contains 0
            "fnValue": 0
          }
        }
      },
      {
        "intervalStart": "2025-05-26T00:00:00Z",
        "count": 5,
        "aggregates": {
          "my_max_daily_severity": {
            "max": 7
          },
          "my_moving_average_max_daily_errors_severity": {
            "fnValue": 10
          },
          "my_moving_last_3days_average_errors_count": {
            "fnValue": 7.5
          }
        }
      },
      {
        "intervalStart": "2025-05-27T00:00:00Z",
        "count": 12,
        "aggregates": {
          "my_max_daily_severity": {
            "max": 100
          },
          "my_moving_average_max_daily_errors_severity": {
            "fnValue": 40
          },
          "my_moving_last_3days_average_errors_count": {
            "fnValue": 9
          }
        }
      },
      {
        "intervalStart": "2025-05-28T00:00:00Z",
        "count": 1,
        "aggregates": {
          "my_max_daily_severity": {
            "max": 1
          },
          "my_moving_average_max_daily_errors_severity": {
            "fnValue": 30.25
          },
          "my_moving_last_3days_average_errors_count": {
            "fnValue": 6
          }
        }
      },
      {
        "intervalStart": "2025-05-29T00:00:00Z",
        "count": 20,
        "aggregates": {
          "my_max_daily_severity": {
            "max": 1
          },
          "my_moving_average_max_daily_errors_severity": {
            "fnValue": 24.4
          },
          "my_moving_last_3days_average_errors_count": {
            "fnValue": 11
          }
        }
      },
      ...
    ]
  }
}
```

*Note*: The last bucket contains the average value across all previous buckets
because the sliding window is bigger than the number of buckets returned.
This trick can be used with all supported moving functions when it's necessary
to use a pipeline aggregate which is not supported by Records API now.
For instance, ```sumBucket``` aggregate has not been supported yet by Records
but the ```movingFunction::MovingFunctions.sum``` with big enough ```window``` can be used instead.


---

### MovingFunctionAggregateResultIla

**プロパティ**:

- `fnValue` (number, 必須)
  - The moving function value from the parent histogram aggregate buckets data
located by the specified in the request buckets path.


---

### MqttAuthentication

Method used for authenticating with the mqtt broker.
This may be used together with auth certificate.

---

### MqttAuthenticationWrite

Method used for authenticating with the mqtt broker.
This may be used together with auth certificate.

---

### MqttBrokerSource

Description of a source that lets users write data to CDF using the MQTT protocol.

**プロパティ**:

- `type` (string, 必須)
  - Source type.
- `externalId` (unknown, 必須)
- `host` (string, 必須)
  - Hostname of the MQTT broker in CDF. Clients should connect to this host.
- `port` (integer, 必須)
  - Port number of the MQTT broker in CDF. Clients should connect to this port.
- `authentication` (object, 必須)
  - Authentication details for connecting to the MQTT broker in CDF.

- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)

---

### MqttV3Source

**プロパティ**:

- `type` (string, 必須)
  - Source type.
- `externalId` (unknown, 必須)
- `host` (string, 必須)
  - Host or IP address of the MQTT broker to connect to.
- `port` (integer, 任意)
  - Port to connect to on the MQTT broker.
- `authentication` (unknown, 任意)
- `useTls` (boolean, 任意)
  - If true, use TLS when connecting to the broker.
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)
- `caCertificate` (unknown, 任意)
- `authCertificate` (unknown, 任意)

---

### MqttV5Source

**プロパティ**:

- `type` (string, 必須)
  - Source type.
- `externalId` (unknown, 必須)
- `host` (string, 必須)
  - Host or IP address of the MQTT broker to connect to.
- `port` (integer, 任意)
  - Port to connect to on the MQTT broker.
- `authentication` (unknown, 任意)
- `useTls` (boolean, 任意)
  - If true, use TLS when connecting to the broker.
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)
- `caCertificate` (unknown, 任意)
- `authCertificate` (unknown, 任意)

---

### MultiLineString

**プロパティ**:

- `type` (string, 必須)
- `coordinates` (unknown, 必須)

---

### MultiLineStringCoordinates

List of lines where each line (LineString) is defined as a list of two or more points.
Each point is defined as a pair of two numbers in an array, representing coordinates of a point in 2D space.

Example: `[[[30, 10], [10, 30]], [[35, 10], [10, 30], [40, 40]]]`


---

### MultiNextCursorV3

Cursors to paginate to the next page of results.  The cursor applies to each result set expression.  We only provide a cursor when more data is available.

**プロパティ**:

- `additionalProperties` (unknown, 任意)

---

### MultiPartUploadFileMetadata

---

### MultiPoint

**プロパティ**:

- `type` (string, 必須)
- `coordinates` (unknown, 必須)

---

### MultiPointCoordinates

List of Points. Each Point is defined as an array of 2 numbers, representing coordinates of a point in 2D space.

Example: `[[35, 10], [45, 45]]`


---

### MultiPolygon

**プロパティ**:

- `type` (string, 必須)
- `coordinates` (unknown, 必須)

---

### MultiPolygonCoordinates

List of multiple polygons.

Each polygon is defined as a list of one or more linear rings representing a shape.

A linear ring is the boundary of a surface or the boundary of a hole in a surface. It is defined as a list consisting of 4 or more Points, where the first and last Point is equivalent.

Each Point is defined as an array of 2 numbers, representing coordinates of a point in 2D space.

Example: `[[[[30, 20], [45, 40], [10, 40], [30, 20]]], [[[15, 5], [40, 10], [10, 20], [5, 10], [15, 5]]]]`


---

### NameUpdateField

Set a new value for name.

**プロパティ**:

- `set` (string, 必須)
  - Name of Extraction Pipeline

---

### NewAnnotation

**プロパティ**:

- `text` (string, 必須)
  - The text and entities detected by the service.
- `confidence` (number, 任意)
  - The confidence for the detection.
- `region` (unknown, 必須)

---

### NewProjectSpec

A specification for creating a new project

**プロパティ**:

- `name` (unknown, 必須)
- `urlName` (unknown, 必須)
- `adminSourceGroupId` (string, 任意)
  - ID of the group in the source. If this is the same ID as a group in the IdP, a principal in that group will implicitly be a part of this group as well.
- `oidcConfiguration` (unknown, 任意)
- `userProfilesConfiguration` (unknown, 任意)
- `parentProjectUrlName` (string, 必須)
  - The URL name of the project from which the new project is being created- this project must already exist.


---

### NextCursor

Cursor to get the next page of results (if available).

---

### NextCursorData

**プロパティ**:

- `nextCursor` (unknown, 任意)

---

### NextCursorV3

The cursor value used to return (paginate to) the next page of results, when more data is available.

---

### NextPageCursor

A cursor to get the next page of results (if available).
If you want the next page of results, call the endpoint with the same parameters, and set `cursor`
to this value.

---

### NextUrlConfig

**プロパティ**:

- `type` (string, 必須)
- `value` (string, 必須)
  - Expression yielding the next URL to call

---

### NoTimeoutWithResultsNotice

**プロパティ**:

- `code` (string, 必須)
- `category` (string, 必須)
- `level` (string, 必須)
- `hint` (string, 必須)

---

### Node

The node ID where the job results.


**プロパティ**:

- `externalId` (unknown, 任意)
- `space` (unknown, 任意)

---

### Node3D

**プロパティ**:

- `id` (integer, 必須)
  - The ID of the node.
- `treeIndex` (unknown, 必須)
- `parentId` (integer, 任意)
  - The parent of the node, null if it is the root node.
- `depth` (integer, 必須)
  - The depth of the node in the tree, starting from 0 at the root node.
- `name` (string, 必須)
  - The name of the node.
- `subtreeSize` (unknown, 必須)
- `properties` (unknown, 任意)
- `boundingBox` (unknown, 任意)

---

### Node3DAssetMapping

**プロパティ**:

- `modelId` (integer, 必須)
  - The ID of the associated model
- `revisionId` (integer, 必須)
  - The ID of the associated revision
- `nodeId` (integer, 必須)
  - The ID of the node.

---

### Node3DId

**プロパティ**:

- `id` (integer, 必須)
  - The ID of the node.

---

### Node3DIds

**プロパティ**:

- `items` (array, 必須)

---

### Node3DList

**プロパティ**:

- `items` (array, 必須)

---

### Node3DNameFilter

Filter used in searching for nodes by name.

**プロパティ**:

- `names` (array, 任意)

---

### Node3DNameFilterBody

Filter request for nodes. Filters nodes with properties matching ones in a list of alternatives.

---

### Node3DPropertyFilter

Filters used in the search.

**プロパティ**:

- `properties` (object, 任意)
  - Contains one or more categories (namespaces), each of which contains one or more properties. Each property is associated with a list of values. The list of values acts as an OR-clause, so that if a node's corresponding property value equals ANY of the strings in the list, it satisfies the condition for that property. The different properties are concatenated with AND-operations, so that a node must satisfy the condition for ALL properties from all categories to be part of the returned set. The allowed number of property values is limited to 1000 values in total.

---

### Node3DPropertyFilterBody

Filter request for nodes. Filters nodes with properties matching ones in a list of alternatives.

---

### Node3DWithCursorResponse

---

### NodeDefinition

Node

**プロパティ**:

- `instanceType` (string, 必須)
- `version` (integer, 必須)
  - Current version of the node
- `space` (unknown, 必須)
  - Id of the space that the node belongs to
- `externalId` (unknown, 必須)
  - Unique identifier for the node
- `type` (unknown, 任意)
  - Node type
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)
- `deletedTime` (unknown, 任意)
  - Timestamp when the node was soft deleted. Note that deleted nodes are filtered out of query results, but present in sync results. This means that this value will only be present in sync results.
- `properties` (object, 任意)
  - Spaces for the requested view and its containers

---

### NodeOrEdge

---

### NodeOrEdgeCreate

---

### NodeOrEdgeDeleteRequest

**プロパティ**:

- `items` (array, 必須)

---

### NodeOrEdgeExternalId

---

### NodeOrEdgeListRequestV3

---

### NodeOrEdgeSearchRequest

Searching nodes or edges using properties from a view

---

### NodeProperties3D

Properties extracted from 3D model, with property categories containing key/value string pairs.

---

### NodeTableExpressionThrough

---

### NodeWrite

Node to create or update

**プロパティ**:

- `instanceType` (string, 必須)
- `existingVersion` (integer, 任意)
  - Fail the ingestion request if the node's version is greater than this value. If no existingVersion is specified, the ingestion will always overwrite any existing data for the node (for the specified container or view). If existingVersion is set to 0, the upsert will behave as an insert, so it will fail the bulk if the item already exists. If skipOnVersionConflict is set on the ingestion request, then the item will be skipped instead of failing the ingestion request.
- `space` (unknown, 必須)
  - Id of the space that the node belongs to. This space-id cannot be updated.
- `externalId` (unknown, 必須)
  - Unique identifier for the node
- `type` (unknown, 任意)
  - Node type (direct relation)
- `sources` (array, 任意)
  - List of source properties to write. The properties are from the view and/or the container(s) making up this node.

---

### NodesWithBoundingBox

**プロパティ**:

- `id` (integer, 必須)
  - The ID of the node.
- `treeIndex` (unknown, 必須)
- `depth` (integer, 必須)
  - The depth of the node in the tree, starting from 0 at the root node.
- `name` (string, 必須)
  - The name of the node.
- `subtreeSize` (unknown, 必須)
- `boundingBox` (unknown, 必須)

---

### NodesWithBoundingBoxList

**プロパティ**:

- `items` (array, 必須)

---

### NonLinAngle

If the gauge is nonlinear, the non-linear angle from the metadata is used to part the scale in two separate linear scales. The first scale goes from min to 0. The second from 0 to max. The needle angle determines which scale is used.

---

### NonceCredentials

**プロパティ**:

- `sessionId` (integer, 必須)
- `nonce` (string, 必須)
- `cdfProjectName` (string, 必須)
- `clientId` (string, 任意)

---

### NotFoundResponse

**プロパティ**:

- `error` (object, 必須)
  - Error details.

---

### NotFoundResponseWithoutInstanceId

**プロパティ**:

- `error` (object, 必須)
  - Error details.

---

### NotificationCreateWithExternalId

**プロパティ**:

- `destination` (string, 必須)
  - Email address where notifications should be sent.
- `transformationExternalId` (string, 必須)
  - Transformation external ID to subscribe.

---

### NotificationCreateWithId

**プロパティ**:

- `destination` (string, 必須)
  - Email address where notifications should be sent.
- `transformationId` (integer, 必須)
  - Transformation ID to subscribe.

---

### NotificationRead

**プロパティ**:

- `id` (integer, 必須)
  - Id of the notification subscription. Required for deleting the subscription.
- `createdTime` (integer, 必須)
  - Time when the notification subscription was created: The number of milliseconds since 00:00:00 Thursday, 1 January 1970, Coordinated Universal Time (UTC), minus leap seconds.
- `lastUpdatedTime` (integer, 必須)
  - Time when the notification subscription was last updated: The number of milliseconds since 00:00:00 Thursday, 1 January 1970, Coordinated Universal Time (UTC), minus leap seconds.
- `transformationId` (integer, 必須)
  - Id of the transformation for which the notifications are sent.
- `destination` (string, 必須)
  - Email address where notifications should be sent.

---

### NullableExternalGroupId

The ID of a group managed by the external identity provider.

---

### NullableSinglePatchLong

The change that will be applied to the key.

---

### NullableSinglePatchString

The change that will be applied to the key.

---

### NumberHistogramAggregate

**プロパティ**:

- `numberHistogram` (object, 必須)
  - A histogram aggregator function. This function will generate a histogram from the values of the specified
property. It uses the specified interval as defined in your `interval` argument.

---

### NumberHistogramAggregateIla

**プロパティ**:

- `numberHistogram` (object, 必須)
  - A histogram aggregator function. This function will generate a histogram from the values of the specified
property. It uses the specified interval as defined in your `interval` argument.

<u>Example:</u>

To get explicit count of players with salaries grouped by $1K and the money which a club spend on each the group:
```
{
  "my_salaries_histogram": {
    "numberHistogram": {
      "interval": "1000",
      "property": [ "football", "player", "salary" ],
      "aggregates": {
        "my_salaries_sum": {
          "sum": { "property": [ "football", "player", "salary" ] }
        }
      }
    }
  }
}
```


---

### NumberHistogramAggregateResult

**プロパティ**:

- `buckets` (array, 任意)
  - The histogram from the values of the specified in the request property.

---

### NumberHistogramAggregateResultIla

**プロパティ**:

- `numberHistogramBuckets` (array, 任意)
  - The histogram from the values of the specified in the request property.


---

### NumericAggregateFunctions

**プロパティ**:

- `average` (number, 任意)
  - The integral average value in the aggregate period.
- `max` (number, 任意)
  - The maximum value in the aggregate period.
- `maxDatapoint` (object, 任意)
  - Timestamp and value of the (first) maximum data point in the aggregate period.
- `min` (number, 任意)
  - The minimum value in the aggregate period.
- `minDatapoint` (object, 任意)
  - Timestamp and value of the (first) minimum data point in the aggregate period.
- `sum` (number, 任意)
  - The sum of the data points in the aggregate period.
- `interpolation` (number, 任意)
  - The interpolated value of the series in the beginning of the aggregate.
- `stepInterpolation` (number, 任意)
  - The last value before or at the beginning of the aggregate.
- `continuousVariance` (number, 任意)
  - The variance of the interpolated underlying function.
- `discreteVariance` (number, 任意)
  - The variance of the data point values.
- `totalVariation` (number, 任意)
  - The total variation of the interpolated underlying function.

---

### ObjectPatch

Custom, application specific metadata. String key -> String value.

---

### ObjectPatchAddRemove

**プロパティ**:

- `add` (object, 任意)
  - Add the key-value pairs. Values for existing keys will be overwritten.
- `remove` (array, 任意)
  - Remove the key-value pairs with the specified keys.

---

### ObjectPatchAddRemoveAsset

**プロパティ**:

- `add` (object, 任意)
  - Add the key-value pairs. Values for existing keys will be overwritten.
- `remove` (array, 任意)
  - Remove the key-value pairs with the specified keys.

---

### ObjectPatchAddRemoveDataSet

**プロパティ**:

- `add` (object, 任意)
  - Add the key-value pairs. Values for existing keys will be overwritten.
- `remove` (array, 任意)
  - Remove the key-value pairs with the specified keys.

---

### ObjectPatchAsset

Custom, application specific metadata. String key -> String value. Limits of updated asset: Maximum length of key is 128 bytes, value 10240 bytes, up to 256 key-value pairs, of total size at most 10240.

---

### ObjectPatchDataSet

Custom, application specific metadata. String key -> String value. Limits of updated asset: Maximum length of key is 128 bytes, value 10240 bytes, up to 256 key-value pairs, of total size at most 10240.

---

### ObjectPatchEvent

Custom, application specific metadata. String key -> String value. Limits of updated event: Maximum length of key is 128 bytes, value 128000 bytes, up to 256 key-value pairs, of total size at most 200000.

---

### ObjectPatchEventAddRemove

**プロパティ**:

- `add` (object, 任意)
  - Add the key-value pairs. Values for existing keys will be overwritten.
- `remove` (array, 任意)
  - Remove the key-value pairs with the specified keys.

---

### ObjectPatchEventSet

**プロパティ**:

- `set` (object, 必須)
  - Set the key-value pairs. All existing key-value pairs will be removed.

---

### ObjectPatchSet

**プロパティ**:

- `set` (object, 必須)
  - Set the key-value pairs. All existing key-value pairs will be removed.

---

### ObjectPatchSetAsset

**プロパティ**:

- `set` (object, 必須)
  - Set the key-value pairs. All existing key-value pairs will be removed.

---

### ObjectPatchSetDataSet

**プロパティ**:

- `set` (object, 必須)
  - Set the key-value pairs. All existing key-value pairs will be removed.

---

### OidcConfigurationDTO

**プロパティ**:

- `jwksUrl` (string, 必須)
  - The URL where the signing keys used to sign tokens from the identity provider are located
- `tokenUrl` (string, 任意)
  - The URL of the OAuth 2.0 token endpoint
- `issuer` (string, 必須)
  - The expected issuer value
- `audience` (string, 必須)
  - The expected audience value (for CDF)
- `skewMs` (integer, 任意)
  - Permitted clock skew (ms)
- `accessClaims` (unknown, 必須)
  - Which claims to link CDF groups to, in order to grant access
- `scopeClaims` (unknown, 必須)
  - Which claims to use when scoping access granted by access claims
- `logClaims` (unknown, 必須)
  - Which token claims to record in the audit log
- `isGroupCallbackEnabled` (unknown, 任意)
  - A group callback occurs when a user has too many groups attached. This property indicates whether
the group callback functionality should be supported for this project. This is only supported for AAD hosted IdPs.

- `identityProviderScope` (string, 任意)
  - The scope sent to the identity provider when a session is created. The default value is the value required for a default Azure AD IdP configuration.

---

### OidcConfigurationUpdate

---

### OidcConfigurationUpdateDTO

**プロパティ**:

- `jwksUrl` (unknown, 任意)
  - The URL where the signing keys used to sign tokens from the identity provider are located
- `tokenUrl` (unknown, 任意)
  - The URL of the OAuth 2.0 token endpoint
- `issuer` (unknown, 任意)
  - The expected issuer value
- `audience` (unknown, 任意)
  - The expected audience value (for CDF)
- `skewMs` (unknown, 任意)
  - Permitted clock skew (ms)
- `accessClaims` (unknown, 任意)
  - Which claims to link CDF groups to, in order to grant access
- `scopeClaims` (unknown, 任意)
  - Which claims to use when scoping access granted by access claims
- `logClaims` (unknown, 任意)
  - Which token claims to record in the audit log
- `isGroupCallbackEnabled` (unknown, 任意)
  - A group callback occurs when a user has too many groups attached. This property indicates whether
the group callback functionality should be supported for this project. This is only supported for AAD hosted IdPs.

- `identityProviderScope` (unknown, 任意)
  - The scope sent to the identity provider when a session is created. The default value is the value required for a default Azure AD IdP configuration.

---

### OktaIdP

---

### OneDataDmsIdentifier

**プロパティ**:

- `items` (array, 必須)
  - List of ID objects

---

### OneOfFileId

An object containing file (external/instance) id. The file can have at most 50 pages.

---

### OneOfId

List of ids or externalIds of models.

---

### OptimizerJob

---

### OrgId

The ID of an organization

---

### OrgUser

**プロパティ**:

- `id` (unknown, 必須)
- `email` (string, 任意)
- `name` (string, 任意)
- `givenName` (string, 任意)
- `middleName` (string, 任意)
- `familyName` (string, 任意)
- `pictureUrl` (string, 必須)

---

### Organization

An organization

**プロパティ**:

- `id` (unknown, 必須)
- `parentId` (unknown, 必須)
- `idp` (unknown, 必須)
- `adminGroupId` (unknown, 任意)
- `adminsCanCreateOrgsInSubtree` (boolean, 必須)
  - Whether admins of the new organization are allowed to create organizations in the subtree of the
organization.
- `adminsCanCreateProjectsInSubtree` (boolean, 必須)
  - Whether admins of the new organization are allowed to create CDF projects in the subtree of the
organization.
- `allowedClusters` (array, 必須)
  - The clusters on which the admins of the organization will be able to create projects.
This must be a (non-strict) subset of the `allowedClusters` set of the parent organization.

---

### OrganizationRequestItem

**プロパティ**:

- `id` (unknown, 必須)
- `idp` (unknown, 必須)
- `adminGroupId` (unknown, 任意)
- `adminsCanCreateOrgsInSubtree` (boolean, 任意)
  - Whether admins of the new organization are allowed to create organizations in the subtree of the
organization.
- `adminsCanCreateProjectsInSubtree` (boolean, 任意)
  - Whether admins of the new organization are allowed to create CDF projects in the subtree of the
organization.
- `allowedClusters` (array, 任意)
  - The clusters on which the admins of the organization will be able to create projects.
This must be a (non-strict) subset of the `allowedClusters` set of the parent organization.

---

### OrganizationResponseItem

**プロパティ**:

- `id` (unknown, 必須)
- `idp` (unknown, 必須)
- `adminGroupId` (unknown, 任意)
- `adminsCanCreateOrgsInSubtree` (boolean, 任意)
  - Whether admins of the new organization are allowed to create organizations in the subtree of the
organization.
- `adminsCanCreateProjectsInSubtree` (boolean, 任意)
  - Whether admins of the new organization are allowed to create CDF projects in the subtree of the
organization.
- `allowedClusters` (array, 任意)
  - The clusters on which the admins of the organization will be able to create projects.
This must be a (non-strict) subset of the `allowedClusters` set of the parent organization.

---

### OrganizationWithContactPersons

---

### OverlapsFilterV3

**プロパティ**:

- `overlaps` (object, 必須)
  - Matches items where the range made up of the two properties overlap with the provided range.
Not available for search queries.

---

### Page

The page of the file where the annotations in `annotations` were detected.

---

### PageCount

The total number of pages in the file, returned if page range was provided.

---

### PageRange

An inclusive range of up to 50 pages, starting at 1. For example, the first 10 pages are given by begin=1, end=10. Page ranges only apply to PDF files.

**プロパティ**:

- `begin` (integer, 必須)
  - The first page of the page range.
- `end` (integer, 必須)
  - The last page of the page range, must be greater than or equal to begin.

---

### ParameterizedPropertyValueV3

A parameterized value

**プロパティ**:

- `parameter` (string, 必須)

---

### ParseStatus

Status of a parsing job

---

### Partition

Splits the data set into `N` partitions.
The attribute is specified as a "M/N" string, where `M` is a natural number in the interval of `[1, N]`.
You need to follow the cursors within each partition in order to receive all the data.

To prevent unexpected problems and maximize read throughput, you should at most use 10 (N <= 10) partitions.

When using more than 10 partitions, CDF may reduce the number of partitions silently.
For example, CDF may reduce the number of partitions to `K = 10` so if you specify an `X/N` `partition` value where `X = 8` and `N = 20` - i.e. `"partition": "8/20"`- then
CDF will change `N` to `N = K = 10` and process the request.
But if you  specify the `X/N` `partition` value where `X = 11` (`X > K`) and `N = 20` - i.e. `"partition": "11/20"`- then
CDF will reply with an empty result list and no cursor in the response.\

In future releases of the resource APIs, Cognite may reject requests if you specify more than 10 partitions.
When Cognite enforces this behavior, the requests will result in a 400 Bad Request status.


---

### PartitionLimited10

Splits the data set into `N` partitions.
The attribute is specified as a "M/N" string, where `M` is a natural number in the interval of `[1, N]`.
You need to follow the cursors within each partition in order to receive all the data.

The maximum number of allowed partitions (`N`) is 10.

Cognite rejects requests if you specify more than 10 partitions.
When Cognite enforces this behavior, the requests result in a 400 Bad Request status.


---

### PartitionObject

**プロパティ**:

- `partition` (unknown, 任意)

---

### PartitionObjectLimited10

**プロパティ**:

- `partition` (unknown, 任意)

---

### PassageDocument

A document

**プロパティ**:

- `id` (unknown, 必須)
  - The unique identifier for the document. This is automatically generated by CDF, and will be the same as the corresponding value in the Files API.
- `externalId` (unknown, 任意)
  - The external ID for the document. This field will be the same as the value set in the Files API.
- `instanceId` (unknown, 任意)
  - The instance ID for documents created through Data Modeling. This field will be the same as the value set in the Files API.
- `sourceFile` (unknown, 必須)

---

### PassageSourceFile

The source file that this document is derived from.

**プロパティ**:

- `name` (string, 必須)
  - Name of the file.
- `hash` (string, 任意)
  - The hash of the source file. This is a SHA256 hash of the original file. The hash only covers the file content, and not other CDF metadata.

---

### PatchConfirmedMatches

Set a new value for confirmed matches.

**プロパティ**:

- `set` (unknown, 必須)

---

### PatchMatchRules

**プロパティ**:

- `set` (unknown, 必須)

---

### PatchModelParameters

Set a new value for modelParameters.

**プロパティ**:

- `set` (unknown, 必須)

---

### PatchPipelineDescription

Set a new value for description.

**プロパティ**:

- `set` (unknown, 必須)

---

### PatchPipelineName

Set a new value for name.

**プロパティ**:

- `set` (unknown, 必須)

---

### PatchPipelineReplacements

Set a new value for replacements.

**プロパティ**:

- `set` (unknown, 必須)

---

### PatchPipelineScoreThreshold

Set a new value for score threshold.

**プロパティ**:

- `set` (unknown, 必須)

---

### PatchPipelineSources

Set a new value for sources.

**プロパティ**:

- `set` (unknown, 必須)

---

### PatchPipelineTargets

Set a new value for targets.

**プロパティ**:

- `set` (unknown, 必須)

---

### PatchRejectedMatches

Set a new value for rejected matches.

**プロパティ**:

- `set` (unknown, 必須)

---

### PatchTrueMatches

Set a new value for true matches.

**プロパティ**:

- `set` (unknown, 必須)

---

### Path

SVG path in a custom format

**プロパティ**:

- `id` (string, 必須)
- `d` (string, 必須)
  - The d attribute of an SVG path
- `styleId` (string, 任意)
  - The id of the style used for this path

---

### PathStyle

Representation of an SVG path style attribute

**プロパティ**:

- `fill` (string, 任意)
- `fillOpacity` (string, 任意)
- `fillRule` (string, 任意)
- `stroke` (string, 任意)
- `strokeDasharray` (string, 任意)
- `strokeDashoffset` (string, 任意)
- `strokeLinecap` (string, 任意)
- `strokeLinejoin` (string, 任意)
- `strokeMiterlimit` (string, 任意)
- `strokeOpacity` (string, 任意)
- `strokeWidth` (string, 任意)

---

### PathStylesDict

Map linking styleIds to SVG path style properties

---

### PathsDict

Map representation of paths

---

### PatternMode

Only available in beta. If true, entities are not required to contain the search field. Instead they require a field called 'sample'. The sample field can be string, or list of alternative strings. Each string defines a pattern. E.g. 21-PT-1019 enables detecting tags consisting of 2 digits, 2 letters and 4 digits. Special characters are not necessary for detecting, but will be included in the detected string. It is possible to mark parts of the sample as constant strings by enclosing them in square brackets. Within square brackets, a | character can be used to separate alternative constants. Alternative constants must be either all digits or all letters. If false, regular diagram detect is performed, searching for occurrences of the search strings of the entities.

---

### PeopleDetection

Detect people in images.

---

### PeopleDetectionParameters

Parameters for people detection.

**プロパティ**:

- `threshold` (unknown, 任意)

---

### PersonalProtectiveEquipmentDetection

Detect personal protective equipment, such as helmet, protective eyewear, and mask in images. In beta. Available only when the `cdf-version: beta` header is provided.

---

### PersonalProtectiveEquipmentDetectionParameters

Parameters for industrial personal protective equipment detection. In beta. Available only when the `cdf-version: beta` header is provided.

**プロパティ**:

- `threshold` (unknown, 任意)

---

### PipelineConfigurationCad

Currently no processing configuration options are available for CAD models. The object can be empty or not specified.

**プロパティ**:


---

### PipelineConfigurationPointCloud

**プロパティ**:

- `version` (string, 必須)
  - Version of the pipeline configuration schema. Currently only the string value "v1" is supported. Any breaking changes to the configuration schema will result in a new version.
- `indexingOptions` (object, 任意)
  - Configuration options for point cloud indexing. The indexingOptions option can be specified as an empty object `{}`, which will enable indexing to run, and use the default values for all options.

- `processTilesOptions` (object, 任意)
  - Configuration options for point cloud tiles processing steps, for point classification, pipe detection with volume suggestions output.
This is run after the point cloud indexing steps.
The processTilesOptions property can be specified as an empty object `{}`, which will enable classification and pipe detection to run, and use the default values for all options.


---

### PipelineCreateItem

Pipeline to create

**プロパティ**:

- `externalId` (unknown, 任意)
- `name` (unknown, 任意)
- `description` (unknown, 任意)
- `modelParameters` (unknown, 任意)
- `sources` (unknown, 必須)
- `targets` (unknown, 必須)
- `trueMatches` (unknown, 任意)
- `confirmedMatches` (unknown, 任意)
- `rejectedMatches` (unknown, 任意)
- `useExistingMatches` (unknown, 任意)
- `generateRules` (unknown, 任意)
- `replacements` (unknown, 任意)
- `rules` (unknown, 任意)
- `rejectedRules` (unknown, 任意)
- `scoreThreshold` (unknown, 任意)
- `scheduleInterval` (unknown, 任意)

---

### PipelineCreatedItem

---

### PipelineDescription

User-defined description of the pipeline.

---

### PipelineMatch

A match between a source and a target

**プロパティ**:

- `matchType` (string, 任意)
- `score` (number, 任意)
  - A confidence score output by the pipeline
- `source` (object, 任意)
  - The source item
- `target` (object, 任意)
  - The target item

---

### PipelineName

User-defined name of the pipeline.

---

### PipelineReplacements

Replace strings in entity fields. You can use this field to add input naming variations to the entity matching
model to improve the suggested matches, for example, 'pmp' for 'pump' and 'bhp' for 'bottom hole pressure'.
To avoid false positives, we recommend using the longer string as `string` and the shorter as `replacement`.

---

### PipelineResourceType

CDF resource type.

---

### PipelineResponseItem

---

### PipelineRunItem

---

### PipelineRunItemWithResults

---

### PipelineScoreThreshold

Only return model matches with score above this threshold.

---

### PipelineSources

Source of the pipeline

---

### PipelineTargets

Targets for the pipeline

---

### PipelineUpdateData

**プロパティ**:

- `name` (unknown, 任意)
- `description` (unknown, 任意)
- `modelParameters` (unknown, 任意)
- `sources` (unknown, 任意)
- `targets` (unknown, 任意)
- `trueMatches` (unknown, 任意)
- `rejectedMatches` (unknown, 任意)
- `confirmedMatches` (unknown, 任意)
- `useExistingMatches` (unknown, 任意)
- `generateRules` (unknown, 任意)
- `replacements` (unknown, 任意)
- `rules` (unknown, 任意)
- `rejectedRules` (unknown, 任意)
- `scoreThreshold` (unknown, 任意)
- `scheduleInterval` (unknown, 任意)

---

### PipelineUpdateItem

Update patch for a pipeline

**プロパティ**:

- `update` (unknown, 必須)

---

### PipelineUpdatedItem

---

### PipelineUseExistingMatches

Use existing (id/assetId) links on the CDF resources as training data when the entity matching model is created.

---

### Point

**プロパティ**:

- `type` (string, 必須)
- `coordinates` (unknown, 必須)

---

### PointCoordinates

Coordinates of a point in 2D space, described as an array of 2 numbers.

Example: `[4.306640625, 60.205710352530346]`


---

### PollRequest

**プロパティ**:

- `limit` (integer, 必須)
  - The maximum number of tasks to return
- `taskType` (string, 必須)
  - Type of task to poll for. The type is left as a string to allow for any future task types without requiring an update of the API. The list of supported task types is defined in ...<TODO: add link to the list of supported task types in jazz-api in github>...

---

### PollResponse

**プロパティ**:

- `items` (array, 任意)

---

### Polygon

**プロパティ**:

- `type` (string, 必須)
- `coordinates` (unknown, 必須)

---

### PolygonCoordinates

List of one or more linear rings representing a shape.

A linear ring is the boundary of a surface or the boundary of a hole in a surface. It is defined as a list consisting of 4 or more Points, where the first and last Point is equivalent.

Each Point is defined as an array of 2 numbers, representing coordinates of a point in 2D space.

Example: `[[[35, 10], [45, 45], [15, 40], [10, 20], [35, 10]], [[20, 30], [35, 35], [30, 20], [20, 30]]]`


---

### PostDatapoint

---

### PostSequenceColumnDTO

Describes a new column.

**プロパティ**:

- `name` (string, 任意)
  - Human readable name of the sequence.
- `externalId` (string, 必須)
  - User provided column identifier (unique for a given sequence).
- `description` (string, 任意)
  - Description of the column.
- `valueType` (unknown, 任意)
- `metadata` (object, 任意)
  - Custom, application specific metadata. String key -> String value. Maximum length of key is 128 bytes, up to 256 key-value pairs, up to a total size of 10000 bytes across all keys and values.


---

### PostSequenceDTO

Describes a new sequence.

**プロパティ**:

- `name` (string, 任意)
  - Name of the sequence.
- `description` (string, 任意)
  - Description of the sequence.
- `assetId` (integer, 任意)
  - Optional asset this sequence is associated with.
- `externalId` (unknown, 任意)
- `metadata` (object, 任意)
  - Custom, application specific metadata. String key -> String value. Maximum length of key is 128 bytes, up to 256 key-value pairs, up to a total size of 10000 bytes across all keys and values.

- `columns` (array, 必須)
  - List of column definitions. Maximum number of numeric columns is 400. Maximum number of string columns is 200. Maximum total number of columns is 400.
- `dataSetId` (unknown, 任意)

---

### PostTimeSeriesMetadataDTO

**プロパティ**:

- `externalId` (string, 任意)
  - Externally provided ID for the time series (optional, but recommended).
- `name` (string, 任意)
  - Human readable name of the time series.
- `legacyName` (string, 任意)
  - This field is deprecated and ignored.
- `isString` (boolean, 任意)
  - Defines whether the time series contains string values (true) or numeric values (false).
Not updatable - this field cannot be changed after the time series has been created.

- `metadata` (unknown, 任意)
- `unit` (string, 任意)
  - The physical unit of the time series (free-text field)
- `unitExternalId` (string, 任意)
  - The physical unit of the time series as [represented in the unit catalog](<https://developer.cognite.com/dev/concepts/resource_types/units/>). Only available for numeric time series.
- `assetId` (unknown, 任意)
  - Asset ID of equipment linked to this time series.
- `isStep` (boolean, 任意)
  - Defines whether the time series is a step series or not.
- `description` (string, 任意)
  - A description of the time series.
- `securityCategories` (array, 任意)
  - The required security categories to access this time series.
- `dataSetId` (unknown, 任意)

---

### PrefixConfig

Generate a prefix for resources created using this format. If both `prefix` and `fromTopic` are set, the generated ID will be on the form `[prefix][topic][id]`

**プロパティ**:

- `fromTopic` (boolean, 任意)
  - Generate the prefix based on the topic of the received message.
- `prefix` (string, 任意)
  - A fixed prefix to the generated IDs.

---

### PrefixFilter

**プロパティ**:

- `prefix` (object, 必須)
  - Matches items that contain a specific prefix in the provided property.


---

### PrefixFilterIla

**プロパティ**:

- `prefix` (object, 必須)
  - Matches records that have the prefix in the identified property. This filter is only supported for
single value text properties.


---

### PrefixFilterV3

**プロパティ**:

- `prefix` (object, 必須)
  - Matches items that have the prefix in the identified property. This filter is only supported for single value text properties.

---

### PreviewResult

Result of a preview job.

---

### PreviewResultError

Result of a failed preview.

**プロパティ**:

- `type` (string, 必須)
  - Result type
- `externalId` (unknown, 必須)
- `kind` (string, 必須)
  - The type of error that occured. If the type is transform_error, raw data is usually available in the /download endpoint.
- `message` (string, 任意)
  - Error message.
- `createdTime` (unknown, 必須)

---

### PreviewResultPayload

Result of a successful preview.

**プロパティ**:

- `type` (string, 必須)
  - Result type
- `externalId` (unknown, 必須)
- `json` (unknown, 任意)
  - Arbitrary json that is the data that would be passed to the mapping. Note that if you have specified a custom `input` type, or custom `format` parameters, this is the data _after_ it has passed through those.
- `context` (object, 任意)
  - Message context that would be passed to the mapping.
- `createdTime` (unknown, 必須)

---

### PreviewResultPending

A result indicating the preview is still running.

**プロパティ**:

- `type` (string, 必須)
  - Result type
- `externalId` (unknown, 必須)

---

### PrimitiveProperty

Primitive types for the property.

We expect dates to be in the ISO-8601 format, while timestamps are expected to be an epoch value with
millisecond precision. JSON values have to be valid JSON fragments. The maximum allowed size for a JSON
object is 40960 bytes. For list of json values, the size of the entire list must be within this limit.
The maximum allowed length of a key is 128, while the maximum allowed size of a value is 10240 bytes
and you can have up to 256 key-value pairs.


**プロパティ**:

- `type` (string, 必須)
- `unit` (object, 任意)
  - The unit of the data stored in this property, can only be assign to type float32 or float64.
ExternalId needs to match with a unit in the Cognite unit catalog.

- `list` (boolean, 任意)
  - Specifies that the data type is a list of values. The ordering of values is preserved.

- `maxListSize` (integer, 任意)
  - Specifies the maximum number of values in the list

---

### PrincipalDto

---

### PrincipalId

Unique identifier of a principal

---

### PrincipalListByIdsRequestDto

**プロパティ**:

- `items` (array, 必須)
- `ignoreUnknownIds` (boolean, 任意)
  - If `true`, IDs that do not match existing principals will be ignored.

If `false`, the request will fail if any of the IDs do not match existing principals.
This is the default behavior.

---

### PrincipalName

Human-readable name of the principal

---

### PrincipalPictureUrl

URL to a picture of the principal

---

### PrincipalType

The type of a principal

---

### PrincipalsListPaginatedDto

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (unknown, 任意)

---

### PrincipalsListResponseDto

**プロパティ**:

- `items` (array, 必須)

---

### ProjectApiUrl

The base API URL for the project

---

### ProjectAssetMapping3DList

---

### ProjectCreateRequest

**プロパティ**:

- `name` (unknown, 必須)
- `clusterName` (unknown, 必須)
- `projectAdminGroupId` (unknown, 任意)

---

### ProjectDeletionReportState

The state field indicates the state of deletion.

- `RECEIVED`: The service acknowledges it has received a notification about the project’s deletion.
- `STARTED`: The service has started to delete data for the resource in the project.
- `COMPLETED`: The service has deleted all data for the resource in the project.
- `FAILED`: The service failed to delete all data for the resource in the project. The failure field should also be set containing additional details.
- `NOT_APPLICABLE`: No data deletion is needed for the resource in the project.



---

### ProjectDeletionReportStateNotCompleted

The state field indicates the state of deletion.

- `RECEIVED`: The service acknowledges it has received a notification about the project’s deletion.
- `STARTED`: The service has started to delete data for the resource in the project.
- `FAILED`: The service failed to delete all data for the resource in the project. The failure field should also be set containing additional details.



---

### ProjectInternalV0Response

**プロパティ**:

- `name` (unknown, 必須)
- `apiUrl` (unknown, 必須)
- `dataModelingStatus` (unknown, 任意)

---

### ProjectName

The user-friendly name of the project.

---

### ProjectPendingDeletion

**プロパティ**:

- `projectId` (integer, 必須)
  - Project id of the report
- `resource` (string, 必須)
  - Resource identifier
- `state` (unknown, 任意)
- `failure` (string, 任意)
  - Optional failure reason
- `lastUpdatedTime` (integer, 任意)
  - milliseconds since epoch

---

### ProjectResponse

**プロパティ**:

- `name` (unknown, 必須)
- `apiUrl` (unknown, 必須)

---

### ProjectScope

---

### ProjectState

The state field indicates the state of a project.

- `ACTIVE`: The project is active.
- `LOCKED`: The project is deleted but still within its grace period.
- `DELETED`: The project and its resources are permanently deleted.


---

### ProjectStatistics

Statistics and limits for data modeling related resources in a project

**プロパティ**:

- `spaces` (unknown, 必須)
  - Usage and limits for spaces in the project
- `containers` (unknown, 必須)
  - Usage and limits for containers in the project
- `views` (unknown, 必須)
  - Usage and limits for views including all versions in the project
- `dataModels` (unknown, 必須)
  - Usage and limits for data models including all versions in the project
- `containerProperties` (unknown, 必須)
  - Usage and limits for sum of container properties in the project
- `instances` (unknown, 必須)
  - Usage and limits for number of instances in the project
- `concurrentReadLimit` (integer, 必須)
  - Maximum number of concurrent read operations allowed in the project
- `concurrentWriteLimit` (integer, 必須)
  - Maximum number of concurrent write operations allowed in the project
- `concurrentDeleteLimit` (integer, 必須)
  - Maximum number of concurrent delete operations allowed in the project

---

### ProjectUpdateObjectDTO

Contains the instructions on how to update the project.


**プロパティ**:

- `name` (unknown, 任意)
- `oidcConfiguration` (unknown, 任意)
- `userProfilesConfiguration` (unknown, 任意)

---

### ProjectUrlName

The URL name of the project. This is used as part of the request path in API calls.

Valid URL names contains between 3 and 32 characters, and may only contain
English letters, digits and hyphens, must contain at least one letter
and may not start or end with a hyphen.


---

### ProjectWithAdminPropertiesResponse

---

### Projects

---

### ProjectsPendingDeletionResponse

List of projects pending deletion along with possible cursors to get the next page of results

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
  - Cursor to get the next page of results (if available).

---

### PropertyIdentifierV3

---

### PropertySort

Sorting spec for a property.


**プロパティ**:

- `property` (unknown, 必須)
- `direction` (string, 任意)

---

### PropertySortV3

**プロパティ**:

- `property` (unknown, 必須)
- `direction` (string, 任意)
- `nullsFirst` (boolean, 任意)

---

### PropertyValueGroupV3

Group of property values indexed by a local unique identifier. The identifier has to have a length of between 1 and 255 characters.  It must also match the pattern ```^[a-zA-Z0-9][a-zA-Z0-9_-]{0,253}[a-zA-Z0-9]?$``` , and it cannot be any of the following reserved identifiers: ```space```, ```externalId```, ```createdTime```, ```lastUpdatedTime```, ```deletedTime```, and ```extensions```. The maximum number of properties depends on your subscription, and is by default 100.

---

### ProtoFile

Description of a protobuf file used for loading input to the mapping.

**プロパティ**:

- `fileName` (string, 必須)
  - Name of protobuf file. Must contain only letters, numbers, underscores, and hyphens, and must end with '.proto'.

---

### ProtobufConfig

List of protobuf files used to define input to the mapping. This will be compiled to a collection of definitions which will be used to convert the input to JSON.

**プロパティ**:

- `type` (string, 必須)
  - Input type
- `messageName` (string, 必須)
  - Name of root message in the protobuf files.
- `files` (array, 必須)

---

### QueryEdgeTableExpressionV3

Specifies a result set of edges.

**プロパティ**:

- `sort` (array, 任意)
- `postSort` (array, 任意)
- `limit` (unknown, 任意)
- `edges` (object, 必須)

---

### QueryIntersectionTableExpressionV3

Find the common elements in the returned result set. Excludes the elements from the optional `except` result set.

**プロパティ**:

- `intersection` (array, 必須)
- `except` (array, 任意)
- `limit` (unknown, 任意)

---

### QueryNodeTableExpressionV3

Specifies a result set of nodes.

**プロパティ**:

- `sort` (array, 任意)
- `limit` (unknown, 任意)
- `nodes` (object, 必須)

---

### QueryParamAuthentication

**プロパティ**:

- `type` (string, 必須)
  - Authentication type.
- `key` (string, 必須)
  - Key for the query parameter to place the authentication token in

---

### QueryParameterConfig

**プロパティ**:

- `type` (string, 必須)
- `key` (string, 必須)
  - Key to insert the generated value into
- `value` (string, 必須)
  - Expression that will be evaluated, and its result used as a query parameter

---

### QueryRequest

**プロパティ**:

- `with` (object, 必須)
- `cursors` (object, 任意)
  - Cursors returned from the previous query request. These cursors match the result set expressions you specified in the ```with``` clause for the query.
- `select` (object, 必須)
  - Select properties for each result set.
- `parameters` (object, 任意)
  - Values in filters can be parameterised. Parameters are provided as part of the query object, and referenced in the filter itself.
- `includeTyping` (unknown, 任意)
- `debug` (unknown, 任意)

---

### QueryRequestBody

Query object to run a query to preview results.

**プロパティ**:

- `query` (string, 必須)
  - SQL query to run for preview.
- `convertToString` (boolean, 必須)
  - Stringify values in the query results.
- `limit` (integer, 任意)
  - End-result limit of the query.
- `sourceLimit` (integer, 任意)
  - Limit for how many rows to download from the data sources.
- `inferSchemaLimit` (integer, 任意)
  - Limit for how many rows that are used for inferring schema. Default is 10,000.
- `timeout` (integer, 任意)
  - Number of seconds to wait before cancelling a query. The default, and maximum, is 240.

---

### QueryResultsBody

Response object containing result schema and data.

**プロパティ**:

- `schema` (unknown, 必須)
- `results` (unknown, 必須)

---

### QuerySelectV3

Define which view to return properties for, and the properties to return. Up to 10 views can be specified per query.

**プロパティ**:

- `sources` (unknown, 任意)
- `sort` (array, 任意)
- `limit` (integer, 任意)

---

### QuerySetOperationTableExpressionV3

---

### QueryTableExpressionV3

---

### QueryUnionAllTableExpressionV3

Return the union of the specified result sets.  We will remove the results that match the optional exception
result sets.  Note: The operation may return duplicate results since we do not perform deduplication.


**プロパティ**:

- `unionAll` (array, 必須)
- `except` (array, 任意)
- `limit` (unknown, 任意)

---

### QueryUnionTableExpressionV3

Return the union of the specified result sets. We will remove the results that match the optional
exception result sets.  But this operation does not result in duplicate results as it performs
deduplication.

Note: You should use the `unionAll` operation whenever possible. Using it enables a built-in optimization.
I.e. `A unionAll B` with `limit: n` will execute set operation B, if and only if, A returns less than
n records.


**プロパティ**:

- `union` (array, 必須)
- `except` (array, 任意)
- `limit` (unknown, 任意)

---

### RAWMatcher

The Raw matcher can be used when you already have a collection of known matches, stored in a Raw table.

**プロパティ**:

- `type` (string, 必須)
- `dbName` (string, 必須)
  - Name of the Raw database
- `tableName` (string, 必須)
  - Name of the Raw database table
- `fromColumnKey` (string, 必須)
  - The column containing the external ids of the node at the start of the relationship.
- `toColumnKey` (string, 必須)
  - The column containing the external ids of the node at the end of the relationship.

---

### RangeFilterIla

**プロパティ**:

- `range` (object, 必須)
  - Matches records that contain terms within the provided range.

The range must include an upper and/or a lower bound. It is not allowed to specify two upper or lower bounds, e.g. `gte` and `gt`, in the same filter.

  `gte`: Greater than or equal to.
  `gt`: Greater than.
  `lte`: Less than or equal to.
  `lt`: Less than.


---

### RangeFilterV3

**プロパティ**:

- `range` (object, 必須)
  - Matches items that contain terms within the provided range.

The range must include both an upper and a lower bound. It is not permitted to specify both inclusive
and exclusive bounds together.  E.g. `gte` and `gt`.

  `gte`: Greater than or equal to.
  `gt`: Greater than.
  `lte`: Less than or equal to.
  `lt`: Less than.


---

### RangeValue

Value you wish to find in the provided property using a range clause.

---

### RawDB

A NoSQL database to store customer data.

**プロパティ**:

- `name` (string, 必須)
  - Unique name of a database.

---

### RawDBRow

**プロパティ**:

- `key` (string, 必須)
  - Unique row key
- `columns` (object, 必須)
  - Row data stored as a JSON object.
- `lastUpdatedTime` (unknown, 必須)

---

### RawDBRowInsert

**プロパティ**:

- `key` (string, 必須)
  - Unique row key
- `columns` (object, 必須)
  - Row data stored as a JSON object.

---

### RawDBRowKey

A row key

**プロパティ**:

- `key` (string, 必須)
  - Unique row key

---

### RawDBTable

A NoSQL database table to store customer data

**プロパティ**:

- `name` (string, 必須)
  - Unique name of the table

---

### RawDataSource

**プロパティ**:

- `type` (string, 必須)
  - The type of the destination resource indicating that the transformation will write to RAW.
- `database` (string, 必須)
  - The database name.
- `table` (string, 必須)
  - The table name.

---

### RawPropertyValueListIla

A list of values describing the type of the defined property

---

### RawPropertyValueListV3

A list of values

---

### RawPropertyValueV3

A value matching the data type of the defined property

---

### RawResponseItem

---

### RawResponseItemCore

**プロパティ**:

- `fileId` (integer, 必須)
  - The internal ID for the File.
- `pageNum` (integer, 必須)
  - Page number the answer is in.
- `propertyId` (string, 必須)
  - property_id of the View
- `spatialData` (unknown, 必須)
  - The relative position in the document where the data was found.
- `value` (object, 必須)
  - The extracted value returned in the expected data type.
- `selectedAnswerIndex` (integer, 任意)
  - This field is the index of the selected answer. If index is -1, it means that the main answer is selected. If index is 0 or greater, it means that the answer at that index in the other_answers list is selected.

---

### RawResponses

A dictionary where each key represents a property name (e.g., 'operatingPressure') and the value is a 'RawResponseItem' schema.


---

### RawRowCSV

Raw row result written in CSV format, with column columnHeaders.

**プロパティ**:

- `columnHeaders` (array, 任意)
  - Headers for the different columns in the response.
- `rows` (array, 任意)
  - Rows of column values, in same order as columnHeaders.

---

### RawTable

**プロパティ**:

- `dbName` (string, 必須)
  - Database name
- `tableName` (string, 必須)
  - Table name

---

### RawTableOptions

Raw rows options

**プロパティ**:

- `database` (string, 必須)
- `table` (string, 必須)
- `primaryKey` (string, 任意)

---

### RawTablesUpdate

Updates the resource's assigned rawTables. RawTables can be added, removed or replaced (set). 

---

### RawTablesUpdateAddRemove

**プロパティ**:

- `add` (array, 任意)
- `remove` (array, 任意)

---

### RawTablesUpdateSet

**プロパティ**:

- `set` (array, 任意)

---

### ReasonForIncompletion

Human-readable reason for terminal failure of a workflow task.

---

### Record

Record item.


**プロパティ**:

- `space` (unknown, 必須)
- `externalId` (unknown, 必須)
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)
- `properties` (object, 必須)
  - Spaces to containers to properties and their values for the requested containers.


---

### RecordData

Property values for the identified/specified container.


**プロパティ**:

- `source` (unknown, 必須)
- `properties` (unknown, 必須)

---

### RecordDelete

Record to delete.


**プロパティ**:

- `space` (unknown, 必須)
- `externalId` (unknown, 必須)

---

### RecordExternalId

The identifier (in scope of a space) of a ```record``` which is stored in a ```stream```.
**Note:** For *mutable* records, the property value must be unique within the scope of the space the record belongs to.
**Note:** Duplicate ```space + externalId``` pair values are allowed for *immutable* records, even within a single batched create records request.


---

### RecordIngest

Record to write.


**プロパティ**:

- `space` (unknown, 必須)
- `externalId` (unknown, 必須)
- `sources` (array, 必須)
  - List of source properties to write. The properties are from the container(s) making up this ```record```.


---

### RecordOperationResult

Result of processing an individual record from the request


**プロパティ**:

- `space` (unknown, 必須)
- `externalId` (unknown, 必須)
- `code` (integer, 必須)
  - The code which matches an HTTP status that can represent record operation result.
A 202 code means that the operation was successful, while any other code means that
the operation failed.

- `message` (string, 任意)
  - The error message which describes the issue. Only present when the operation failed.


---

### RecordSpace

Id of the space that the ```record``` belongs to.


---

### RecordsPartialSuccessResponse

Contains generic description of the result as well as per record detailed results.


**プロパティ**:

- `error` (object, 必須)
  - Error details including HTTP status code, message, and per-record operation results.


---

### ReducedLimit

Limits the number of results returned. The greatest number of results returned by the server is 1000. This is true even when you specify a higher limit.

**プロパティ**:

- `limit` (integer, 任意)

---

### Reference

A Reference is an expression that allows dynamically injecting input to a task during execution. References can be used to reference the input of the Workflow, the output of a previous task in the Workflow, or the input of a previous task in the Workflow. Note that the injected value must be valid in the context of the property it is injected into.
Example Task reference: ${myTaskExternalId.output.someKey} Example Workflow input reference: ${workflow.input.myKey}

---

### ReferenceAI

**プロパティ**:

- `externalId` (string, 任意)
  - The external ID of the file.
- `fileId` (integer, 必須)
  - The ID of the file.
- `fileName` (string, 必須)
  - The name of the file.
- `instanceId` (unknown, 任意)
  - The instance ID of the file.
- `locations` (array, 必須)
  - The locations in the document that contain the relevant text.

---

### ReferenceByExternalId

**プロパティ**:

- `externalId` (unknown, 必須)

---

### ReferenceById

**プロパティ**:

- `id` (unknown, 必須)

---

### ReferenceByIdOrExternalId

Either a principal ID, or a principal external ID

---

### ReferencedPropertyValueV3

A property reference value

**プロパティ**:

- `property` (array, 必須)

---

### RefreshSessionRequest

Refresh an active session.

**プロパティ**:

- `sessionKey` (string, 必須)
  - Session key for the session.
- `sessionId` (number, 必須)
  - ID of the session

---

### RegexExtractor

An extractor that acts on entities from 'entitySet'. An entity must have a value of 'field' that matches the regular expression 'pattern' to get features. The features are the catching groups of the pattern, that is the parts that are in parenthesis. E.g. ([A-Z])-00([0-9]) applied to A-007 produces [A, 7] as features.

**プロパティ**:

- `entitySet` (string, 必須)
  - Which set of entities the extractor will extract features from.
- `extractorType` (string, 必須)
  - The type of extractor. Currently,  we only support "regex".
- `field` (string, 必須)
  - The entity field that the extractor will get features from.
- `pattern` (string, 必須)
  - A regular expression without nested catching groups.

---

### RejectedKey

**プロパティ**:

- `rejectedKey` (string, 必須)

---

### RejectedMatches

List of objects of pairs of sourceId or sourceExternalId and targetId or targetExternalId, that corresponds to entities in source and target respectively, that indicates a match rejected by the user. A source and target pair in this list will override results from a model or rules and will never be returned as a match.

---

### Release

A release of an extractor

**プロパティ**:

- `artifacts` (array, 必須)
  - List of artifacts included in this release.
- `changelog` (unknown, 任意)
- `createdTime` (unknown, 任意)
- `description` (string, 任意)
  - Short description of this release. Details are found in the changelog.
- `externalId` (unknown, 必須)
- `version` (string, 必須)
  - Release version number, uses semantic versioning.

---

### ReleaseId

Identifier for a release

**プロパティ**:

- `externalId` (unknown, 必須)
- `version` (string, 必須)
  - Release version number, uses semantic versioning.

---

### RemoveClaimNames

**プロパティ**:

- `remove` (unknown, 必須)

---

### RemoveField

**プロパティ**:

- `setNull` (boolean, 必須)

---

### RemoveJobResultRejections

**プロパティ**:

- `job` (unknown, 必須)
- `result` (unknown, 必須)
- `remove` (array, 必須)

---

### RemoveOidcConfiguration

**プロパティ**:

- `setNull` (boolean, 必須)

---

### RemoveReservedPrefix

**プロパティ**:

- `setNull` (boolean, 必須)
  - Removes the reserved prefix.
Only allowed for non-leaf projects. Currently only permitted to be updated by cluster administrators.


---

### Request

**プロパティ**:

- `endpointId` (string, 必須)
  - ID of the SAP endpoint the writeback request should be sent to.
- `requestId` (string, 必須)
  - Unique request ID generated by the CDF writeback API.
- `request` (unknown, 必須)
- `status` (string, 必須)
  - Status of a writeback request
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)

---

### RequestFull

**プロパティ**:

- `requestId` (string, 必須)
  - Unique request ID generated by the CDF writeback API.
- `request` (unknown, 必須)
- `status` (string, 必須)
  - Writeback request status
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)
- `errorMessage` (string, 任意)
  - Writeback request error message. This field only contains data when the request status is set to `failed`

---

### RequestIdWrapper

**プロパティ**:

- `requestId` (unknown, 任意)

---

### RequestItem

**プロパティ**:

- `key` (string, 任意)
  - External object key related to this request
- `payload` (object, 必須)
  - Payload to send to the SAP endpoint

---

### RequestItemFull

**プロパティ**:

- `key` (string, 任意)
  - External object key related to this request
- `payload` (object, 必須)
  - Payload to send to the SAP endpoint
- `sapObjectId` (string, 任意)
  - Object key of the created records in the SAP destination.

---

### RequestItemMinimal

**プロパティ**:

- `key` (string, 任意)
  - External object key related to this request
- `sapObjectId` (string, 任意)
  - Object key of the created records in the SAP destination.

---

### RequestMinimal

**プロパティ**:

- `requestId` (string, 必須)
  - Unique request ID generated by the CDF writeback API.
- `request` (unknown, 必須)
- `status` (string, 必須)
  - Status of the writeback request
- `createdTime` (unknown, 必須)
- `errorMessage` (string, 任意)
  - Writeback request error message. This field only contains data when the writeback request status is set to `failed`
- `lastUpdatedTime` (unknown, 必須)

---

### RequiresConstraintCreateDefinition

**プロパティ**:

- `constraintType` (string, 任意)
- `require` (unknown, 必須)

---

### RequiresConstraintDefinition

---

### ResetReportRequest

**プロパティ**:

- `projectIds` (array, 必須)
- `resource` (string, 必須)
  - Resource identifier

---

### ResetReportRequestList

**プロパティ**:

- `items` (array, 必須)

---

### ResetReportResponse

**プロパティ**:

- `items` (array, 必須)

---

### ResourceDescription

The description of the resource type.

---

### ResourceReference

A reference to another CDF resource. _Either_ the internal _or_ the external ID _must_ be provided (not both).

**プロパティ**:

- `id` (unknown, 任意)
- `externalId` (unknown, 任意)

---

### RestInterval

---

### RestJobConfig

**プロパティ**:

- `interval` (unknown, 必須)
- `path` (string, 必須)
  - Path of resource to access on the server, without query.
- `method` (string, 任意)
  - HTTP method to use for each request.
- `body` (unknown, 任意)
  - Initial JSON body to send with request. Only applicable if method is `post`. Maximum of 10000 bytes total.

- `query` (object, 任意)
  - Query parameters to include in request. String key -> String value. Limits: Maximum 255 characters per key, 2048 per value, and at most 32 pairs.

- `headers` (object, 任意)
  - Headers to include in request. String key -> String value. Limits: Maximum 255 characters per key, 2048 per value, and at most 32 pairs.

- `incrementalLoad` (object, 任意)
  - The format of the messages from the source. This is used to convert messages coming from the source system to a format that can be inserted into CDF.
- `pagination` (object, 任意)
  - The format of the messages from the source. This is used to convert messages coming from the source system to a format that can be inserted into CDF.

---

### RestSource

**プロパティ**:

- `type` (string, 必須)
  - Source type.
- `externalId` (unknown, 必須)
- `host` (string, 必須)
  - Host or IP address to connect to.
- `scheme` (string, 必須)
  - Type of connection to establish
- `port` (integer, 任意)
  - Port on server to connect to. Uses default ports based on the scheme if omitted.
- `caCertificate` (unknown, 任意)
- `authCertificate` (unknown, 任意)
- `authentication` (unknown, 任意)
  - Authentication details for source
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)

---

### RestrictedItemList_ExternalIdRef_

**プロパティ**:

- `items` (array, 必須)
- `ignoreUnknownIds` (boolean, 任意)
  - Ignore external IDs that are not found. If set to true, no error will be thrown if an external ID is not found.

---

### ResultList

**プロパティ**:

- `items` (array, 必須)

---

### ResultType

---

### ResultWithType

---

### RetryExecution

**プロパティ**:

- `authentication` (unknown, 必須)

---

### ReverseDirectRelationConnection

Describes the direct relation(s) pointing to instances read through this view. This connection type is used to aid in discovery and documentation of the view.

**プロパティ**:

- `connectionType` (string, 必須)
  - The type of connection. The ```single_reverse_direct_relation``` type is used to indicate that only a single direct relation is expected to exist. The ```multi_reverse_direct_relation``` type is used to indicate that multiple direct relations are expected to exist.
- `name` (string, 任意)
  - Readable property name.
- `description` (string, 任意)
  - Description of the content and suggested use for this property.
- `source` (unknown, 必須)
  - The node(s) containing the direct relation property can be read through the view specified in 'source'.
- `through` (unknown, 必須)
  - The view or container of the node containing the direct relation property.

---

### ReverseDirectRelationConnectionRead

---

### ReverseLookupResourceReference

A reference to another CDF resource. Internal and external ID are both provided if available.

**プロパティ**:

- `id` (unknown, 任意)
- `externalId` (unknown, 任意)

---

### RevertConfigRequest

**プロパティ**:

- `externalId` (string, 必須)
  - External ID of the extraction pipeline to revert configurations for.
- `revision` (integer, 任意)
  - Revision number of this configuration.

---

### Revision3D

**プロパティ**:

- `id` (integer, 必須)
  - The ID of the revision.
- `fileId` (integer, 必須)
  - The file id.
- `published` (boolean, 必須)
  - True if the revision is marked as published.
- `rotation` (array, 任意)
- `scale` (array, 任意)
- `translation` (array, 任意)
- `camera` (unknown, 任意)
- `status` (string, 必須)
  - The status of the revision.
- `metadata` (unknown, 任意)
- `thumbnailThreedFileId` (integer, 任意)
  - The threed file ID of a thumbnail for the revision. Use `/3d/files/{id}` to retrieve the file.
- `thumbnailURL` (string, 任意)
  - The URL of a thumbnail for the revision.
- `assetMappingCount` (integer, 必須)
  - The number of asset mappings for this revision.
- `createdTime` (unknown, 必須)

---

### Revision3DList

**プロパティ**:

- `items` (array, 必須)

---

### Revision3DWithCursorResponse

---

### RevisionCameraProperties

Initial camera position and target.

**プロパティ**:

- `target` (array, 必須)
  - Initial camera target.
- `position` (array, 必須)
  - Initial camera position.

---

### RevisionLog3D

**プロパティ**:

- `timestamp` (unknown, 必須)
- `severity` (integer, 必須)
  - How severe is the message (3 = INFO, 5 = WARN, 7 = ERROR).
- `type` (string, 必須)
  - Main computer parsable log entry type
- `info` (string, 任意)
  - Optional extra information related to the log entry

---

### RevisionLog3DResponse

**プロパティ**:

- `items` (array, 必須)

---

### RevisionStatus

**プロパティ**:

- `status` (string, 任意)
  - The status of the revision.
- `revisionId` (integer, 任意)
  - The ID of the revision model.
- `createdTime` (unknown, 任意)
- `revisionCount` (integer, 任意)
- `types` (array, 任意)

---

### RevokeSessionRequest



**プロパティ**:

- `id` (number, 必須)
  - ID of the session

---

### RevokeSessionRequestList



**プロパティ**:

- `items` (array, 任意)

---

### RockwellFormat

Transform data on the Rockwell FactoryTalk format into CDF timeseries

**プロパティ**:

- `type` (string, 必須)
  - Format type.
- `encoding` (unknown, 任意)
- `compression` (unknown, 任意)
- `prefix` (unknown, 任意)
- `dataModels` (unknown, 任意)

---

### RootAssetIds

Only include files that have a related asset in a tree rooted at any of these root assetIds.

---

### RoutineKind

---

### RuleMatch

**プロパティ**:

- `sourceKeyField` (string, 任意)
- `targetKeyField` (string, 任意)
- `source` (object, 任意)
- `target` (object, 任意)
- `existingMatchType` (string, 任意)
- `consistentMatch` (boolean, 任意)

---

### RunAdvancedJoinRequestSchema

**プロパティ**:

- `advancedJoinExternalId` (unknown, 必須)

---

### RunAdvancedJoinResponseSchema

---

### RunIdRef

**プロパティ**:

- `runId` (integer, 必須)
  - Simulation run id

---

### RunsFilter

**プロパティ**:

- `externalId` (string, 必須)
  - Extraction pipeline external Id provided by client.
- `statuses` (array, 任意)
  - Extraction pipeline statuses list. Expected values: success, failure, seen.
- `createdTime` (unknown, 任意)
- `message` (unknown, 任意)

---

### RunsFilterRequest

**プロパティ**:

- `filter` (unknown, 必須)
- `limit` (integer, 任意)
  - Limits the number of results to return.
- `cursor` (string, 任意)

---

### SAuthIdp

---

### Schedule

Details for the schedule if the transformation is scheduled

**プロパティ**:

- `id` (integer, 必須)
  - Transformation ID
- `externalId` (string, 必須)
  - Transformation externalId
- `createdTime` (integer, 必須)
  - Time when the schedule was created: The number of milliseconds since 00:00:00 Thursday, 1 January 1970, Coordinated Universal Time (UTC), minus leap seconds.
- `lastUpdatedTime` (integer, 必須)
  - Time when the schedule was last updated: The number of milliseconds since 00:00:00 Thursday, 1 January 1970, Coordinated Universal Time (UTC), minus leap seconds.
- `interval` (unknown, 必須)
- `isPaused` (boolean, 必須)
  - If true, the transformation is not scheduled.

---

### ScheduleInterval

How often to schedule the pipeline, in seconds. The interval can be between 1 hour and 31 days.

---

### ScheduleParametersWithExternalId

**プロパティ**:

- `interval` (unknown, 必須)
- `isPaused` (boolean, 任意)
  - If true, the transformation is not scheduled.
- `externalId` (string, 必須)
  - External ID of the scheduled transformation

---

### ScheduleParametersWithId

**プロパティ**:

- `interval` (unknown, 必須)
- `isPaused` (boolean, 任意)
  - If true, the transformation is not scheduled.
- `id` (integer, 必須)
  - ID of the scheduled transformation

---

### ScheduleUpdate

**プロパティ**:

- `interval` (object, 任意)
- `isPaused` (unknown, 任意)
  - If true, the transformation is not scheduled.

---

### ScheduleUpdateField

Set a new value for schedule.

**プロパティ**:

- `set` (string, 任意)
  - Possible values: “On trigger”, “Continuous” or cron expression. If empty then null

---

### SchemaMapping

**プロパティ**:

- `externalId` (unknown, 必須)
- `expression` (string, 必須)
  - Custom transform expression written in the Cognite mapping language.
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)

---

### ScoreItem

Represents a scoring item, including the count of errors, a numeric score, and a status message. The score is a float value, while the status message provides additional details or context about the score.


**プロパティ**:

- `error_count` (integer, 必須)
- `score` (number, 必須)
- `status` (string, 必須)

---

### Scores

Currently supports two scoring metrics: 'completenessScore' and 'typeScore'.


**プロパティ**:

- `completenessScore` (unknown, 必須)
- `typeScore` (unknown, 必須)

---

### ScramSha256

**プロパティ**:

- `type` (string, 必須)
  - Authentication type
- `username` (string, 必須)
  - Username for authentication

---

### ScramSha512

**プロパティ**:

- `type` (string, 必須)
  - Authentication type
- `username` (string, 必須)
  - Username for authentication

---

### Search

**プロパティ**:

- `name` (string, 任意)
  - Prefix and fuzzy search on name.
- `description` (string, 任意)
  - Prefix and fuzzy search on description.
- `query` (string, 任意)
  - Whitespace-separated terms to search for in time series. Does a
best-effort fuzzy search in relevant fields (currently name and
description) for variations of any search terms and
orders results by relevance. Uses a different search algorithm
than the name and description parameters and will generally give
much better results. Matching and ordering aren't guaranteed to
be stable over time, and the fields being searched may be
extended.

---

### SearchConfigView

The configuration for one specific view

**プロパティ**:

- `externalId` (string, 必須)
  - The external ID of the view
- `space` (string, 必須)
  - The space that the view is in

---

### SearchConfigViewProperty

Array of configurations per property

---

### SearchConfigViewRead

**プロパティ**:

- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)

---

### SearchConfigViewWrite

**プロパティ**:

- `id` (integer, 任意)
  - A server-generated ID for the object. When updating, this property needs to be provided. Optional when creating a new object.
- `view` (unknown, 必須)
- `useAsName` (string, 任意)
  - The name of property to use for the name column in the UI.
- `useAsDescription` (string, 任意)
  - The name of property to use for the description column in the UI.
- `columnsLayout` (unknown, 任意)
- `filterLayout` (unknown, 任意)
- `propertiesLayout` (unknown, 任意)

---

### SearchFilter

**プロパティ**:

- `search` (object, 必須)
  - Fuzzy search in the specified property. Introduced to provide functional parity with `/search` endpoints.


---

### SearchLimit

Limits the number of results to be returned.

---

### SearchOperator

Controls how multiple search terms are combined when matching documents.
- **OR** (default): A document matches if it contains *any* of the query terms
  in the searchable fields. This typically returns more results but with lower precision.

- **AND**: A document matches only if it contains *all* of the query terms
  across the searchable fields. This typically returns fewer results but with higher relevance.


---

### SearchRequestV3

---

### SearchSort

**プロパティ**:

- `property` (unknown, 必須)
- `direction` (string, 任意)

---

### SecurityCategoryDTO

**プロパティ**:

- `name` (string, 必須)
  - Name of the security category
- `id` (integer, 必須)
  - ID of the security category

---

### SecurityCategoryResponse

**プロパティ**:

- `items` (array, 任意)

---

### SecurityCategorySpecDTO

**プロパティ**:

- `name` (string, 必須)
  - Name of the security category

---

### SecurityCategoryWithCursorResponse

A list of objects along with possible cursors to get the next page of results

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
  - Cursor to get the next page of results (if available).

---

### SegmentBoundingBox

Box prompt.

**プロパティ**:

- `xMin` (number, 必須)
- `yMin` (number, 必須)
- `xMax` (number, 必須)
- `yMax` (number, 必須)

---

### SegmentPoint

**プロパティ**:

- `x` (number, 必須)
- `y` (number, 必須)
- `label` (number, 任意)
  - Label for the input point prompt. 0 is a negative input point (background) while 1 is a positive input point (foreground).

---

### SelectiveExternalIDFilterNotice

**プロパティ**:

- `code` (string, 必須)
- `category` (string, 必須)
- `level` (string, 必須)
- `hint` (string, 必須)
- `grade` (string, 必須)
- `viaFrom` (string, 任意)
  - Identifier for a result set. Indicates that the notice is inherited from this result expression.
- `resultExpression` (string, 必須)
  - Identifier for the result set expression that the notice applies to.

---

### SemanticVersion

A version number in the SemVer sense. See [semver.org](https://semver.org/) for the specification.

---

### SequenceChangeDTO

**プロパティ**:

- `update` (object, 必須)
  - A description of changes that should be done to the sequence.

---

### SequenceColumnChangeDTO

Add, remove, or modify sequence columns. After the update, the number of numeric columns and
deleted columns should be ≤ 400; the number of string columns and deleted columns should
be ≤ 200; and the number of numeric columns, string columns, and deleted columns should
be ≤ 400.


**プロパティ**:

- `modify` (array, 任意)
  - List of single column updates.
- `add` (array, 任意)
  - List of column definitions to add.
- `remove` (array, 任意)
  - List of columns to remove.

---

### SequenceDataDeleteRequestDTO

Rows to delete from a sequence.

**プロパティ**:

- `rows` (array, 必須)

---

### SequenceDataInsertion

Data from a sequence.

**プロパティ**:

- `columns` (array, 必須)
  - Column external IDs in the same order as the values for each row.
- `rows` (array, 必須)
  - List of rows. The number of rows per request is limited to 10000. The total number of values, including nulls, in a single request is limited to 100000.

---

### SequenceDataRequest

Parameters describing a query for datapoints.

---

### SequenceDataRequestDTO

A request for datapoints stored.

**プロパティ**:

- `start` (integer, 任意)
  - Lowest row number included.
- `end` (integer, 任意)
  - Get rows up to, but excluding, this row number. Default - No limit.
- `limit` (integer, 任意)
  - Maximum number of rows returned in one request. API might return less even if there's more data, but it will provide a cursor for continuation. If there's more data beyond this limit, a cursor will be returned to simplify further fetching of data.
- `cursor` (string, 任意)
  - Cursor for pagination returned from a previous request. Apart from this cursor, the rest of the request object is the same as for the original request.
- `columns` (array, 任意)
  - Columns to include. Specified as a list of the `externalId` of each column to include.
If this filter isn't set, all available columns will be returned.


---

### SequenceDeleteDataRequest

Parameters describing datapoints to be deleted.

---

### SequenceFilter

**プロパティ**:

- `name` (string, 任意)
  - Returns only sequences with this name.
- `externalIdPrefix` (unknown, 任意)
- `metadata` (object, 任意)
  - Filters the sequences by metadata fields and values. Format is {'key1':'value1','key2':'value2'}.
- `assetIds` (array, 任意)
  - Returns only sequences linked to one of the specified assets.
- `rootAssetIds` (array, 任意)
  - Only includes sequences that have a related asset in a tree rooted at any of these root `assetIds`.
- `assetSubtreeIds` (array, 任意)
  - Only includes sequences that have a related asset in a subtree rooted at any of these `assetIds` (including the roots given). If the total size of the given subtrees exceeds 100,000 assets, an error will be returned.
- `createdTime` (unknown, 任意)
- `lastUpdatedTime` (unknown, 任意)
- `dataSetIds` (array, 任意)
  - Only includes sequences that belong to these datasets.

---

### SequenceGetData

Data from a sequence.

**プロパティ**:

- `id` (unknown, 必須)
- `externalId` (unknown, 任意)
- `columns` (array, 必須)
  - Column information in the order given by data.
- `rows` (array, 必須)
  - List of row information.

---

### SequenceGetDataWithCursor

---

### SequenceItemDTO

---

### SequenceLatestDataRequest

Parameters describing a query for the last row in a sequence.

---

### SequenceLatestDataRequestDTO

A request for the last row.

**プロパティ**:

- `columns` (array, 任意)
  - Columns to include. Specified as a list of the `externalId` of each column to include.
If this filter isn't set, all available columns will be returned.

- `before` (integer, 任意)
  - Get rows up to, but not including, this row number.

---

### SequencePostData

---

### SequenceRowDTO

A single row of datapoints.

**プロパティ**:

- `rowNumber` (integer, 必須)
  - The row number for this row.
- `values` (array, 必須)
  - List of values in the order defined in the columns field. Number of items must match. Null is accepted for missing values. String values must be no longer than 256 characters.

---

### SequenceRowDataSource

**プロパティ**:

- `type` (string, 必須)
  - The type of the destination resource indicating that the transformation will write to Sequence Rows.
- `externalId` (string, 必須)
  - The externalId of sequence

---

### SequenceSearch

**プロパティ**:

- `name` (string, 任意)
  - Prefix and fuzzy search on name.
- `description` (string, 任意)
  - Prefix and fuzzy search on description.
- `query` (string, 任意)
  - Searches on name and description using wildcard search on each of the words (separated by spaces). Retrieves results where at least one word must match. For example, '*some other*'.

---

### SequenceValueTypeEnum

What type the datapoints in a column will have. DOUBLE is restricted to the range [-1E100, 1E100]

---

### SequenceWithCursorResponse

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
  - The cursor to get the next page of results (if available). Learn more about [pagination](https://developer.cognite.com/dev/concepts/pagination.html).

---

### SequencesAdvancedAggregateDTO

---

### SequencesAdvancedListDTO

---

### SequencesAggregateFilter

A filter DSL (Domain Specific Language) to define aggregate filters.


---

### SequencesAggregatePath

**プロパティ**:

- `path` (array, 必須)
  - The scope within which properties should be aggregated. The only value that is currently allowed is `['metadata']`, which will aggregate metadata keys.

---

### SequencesAggregateProperties

**プロパティ**:

- `properties` (array, 必須)
  - The properties to which the aggregation should be applied. While this parameter is a list, it
currently only accepts one element. Each element is an object with a single field called `property`,
whose value is another list (in order to accommodate nested properties). Thus, a top-level property
`name` must be specified as `[{"property": ["name"]}]`, and a metadata property `tag` must be
specified as `[{"property": ["metadata", "tag"]}]`.

The supported top-level properties are:
- `assetId`
- `assetRootId`
- `createdTime`
- `dataSetId`
- `description`
- `externalId`
- `id`
- `lastUpdatedTime`
- `name`


---

### SequencesBoolAggregateFilter

A query that matches items matching boolean combinations of other queries.
It's built by nesting one or more Boolean clauses, each of which is one of `and`, `or`, and `not`.
Each such clause contains one or more child clauses (though `not` can only have one).
Each child clause can be either another Boolean clause or a leaf filter.


---

### SequencesBoolFilter

A query that matches items matching boolean combinations of other queries.
It is built by nesting one or more Boolean clauses, each of which is one of `and`, `or`, and `not`.
Each such clause contains one or more child clauses (though `not` can only have one).
Each child clause can be either another Boolean clause or a leaf filter.


---

### SequencesCardinalityPropertiesAggregate

---

### SequencesCardinalityPropertiesAggregateResponse

**プロパティ**:

- `items` (array, 必須)

---

### SequencesCardinalityValuesAggregate

---

### SequencesCardinalityValuesAggregateResponse

**プロパティ**:

- `items` (array, 必須)

---

### SequencesContainsAllFilter

**プロパティ**:

- `containsAll` (object, 必須)
  - Matches items where the property contains all the given values.
This filter can only be applied to multivalued properties.


---

### SequencesContainsAnyFilter

**プロパティ**:

- `containsAny` (object, 必須)
  - Matches items where the property contains one or more of the given values.
This filter can only be applied to multivalued properties.


---

### SequencesCountAggregate

**プロパティ**:

- `aggregate` (string, 任意)
  - The `count` aggregation gets the number of sequences that match the filter(s). This is the
default aggregation, which will also be applied if `aggregate` is not specified.


---

### SequencesCountAggregateResponse

**プロパティ**:

- `items` (array, 必須)

---

### SequencesEqualsFilter

**プロパティ**:

- `equals` (object, 必須)
  - Matches items where the given property is **exactly** equal to the given value.


---

### SequencesExistsFilter

**プロパティ**:

- `exists` (object, 必須)
  - Matches items that have a value for the specified property.


---

### SequencesFilterLanguage

A filter DSL (Domain Specific Language) to define advanced filter queries.

At the top level, an `advancedFilter` expression is either a single Boolean filter or a
single leaf filter. Boolean filters contain other Boolean filters and/or leaf filters. The
total number of filters may be at most 100, and the depth (the greatest number of times
filters have been nested inside each other) may be at most 10. The `search` leaf filter may
at most be used twice within a single `advancedFilter`, but all other filters can be used
as many times as you like as long as the other limits are respected.


---

### SequencesFilterProperty

The property on which you want to filter. May be either:
- A single-element list that contains the name of one of the predefined top-level
  properties, in which case the filter will be applied to that top-level property.
- A single-element list that contains `'metadata'`, in which case the filter will be applied
  to _all_ metadata properties.
- A two-element list where the first element is `'metadata'` and the second one is any
  string, in which case the filter will be applied to metadata properties with that name.


---

### SequencesInAggregateFilter

**プロパティ**:

- `in` (object, 必須)
  - Matches intermediate aggregation results where the property key or the property value is _exactly_
equal to one of the given values. When the `aggregate` operation is `uniqueProperties` or
`cardinalityProperties`, property _keys_ will be targeted; when it is `uniqueValues` or
`cardinalityValues`, property _values_ will be targeted.


---

### SequencesInAggregateValue

The property key or property value (depending on the `aggregate` operation) on which you want to
filter the intermediate aggregate results.


---

### SequencesInAggregateValues

The property keys or property values (depending on the `aggregate` operation) on which you want to
filter the intermediate aggregate results.


---

### SequencesInFilter

**プロパティ**:

- `in` (object, 必須)
  - Matches items where the given property is **exactly** equal to one of the given values.
This filter can only be applied to single-valued properties.


---

### SequencesLeafAggregateFilter

Leaf filter.


---

### SequencesLeafFilter

Leaf filter.


---

### SequencesPrefixAggregateFilter

**プロパティ**:

- `prefix` (object, 必須)
  - Matches intermediate aggregation results where the property key or the property value begins with
the given text. When the `aggregate` operation is `uniqueProperties` or `cardinalityProperties`,
property _keys_ will be targeted; when it is `uniqueValues` or `cardinalityValues`, property
_values_ will be targeted.


---

### SequencesPrefixAggregateValue

A string value that represents either a property key or a property value, depending on the
`aggregate` operation.


---

### SequencesPrefixFilter

**プロパティ**:

- `prefix` (object, 必須)
  - Matches items where the provided property begins with the given text.


---

### SequencesRangeAggregateFilter

**プロパティ**:

- `range` (object, 必須)
  - Matches intermediate aggregation results where the property key or the property value is within the
provided range. When the `aggregate` operation is `uniqueProperties` or `cardinalityProperties`,
property _keys_ will be targeted; when it is `uniqueValues` or `cardinalityValues`, property 
_values_ will be targeted.

At least one of the following bounds must be specified:
- `gte`: Greater than or equal to.
- `gt`: Greater than.
- `lte`: Less than or equal to.
- `lt`: Less than.

It is not valid to specify both inclusive and exclusive bounds "on the same side" together:
at most one of `lte` and `lt` may be specified, and at most one of `gte` and `gt`.

In the case of `aggregate` being `uniqueProperties` or `cardinalityProperties`,
`gte`/`gt`/`lte`/`lt` must be set to a string. In the case of `aggregate` being `uniqueValues` or
`cardinalityValues`, this filter may only be applied to string properties or number properties,
with `gte`/`gt`/`lte`/`lt` containing values of the corresponding type.


---

### SequencesRangeAggregateValue

An upper or lower bound for a property key or property value (depending on which `aggregate` was
specified at the top level of the aggregation request).


---

### SequencesRangeFilter

**プロパティ**:

- `range` (object, 必須)
  - Matches items that contain terms within the provided range.

One upper bound and/or one lower bound must be specified. It's not allowed to specify both inclusive and exclusive
bounds "on the same side" together (at most one of `lte` and `lt` may be specified, and at most one of `gte` and `gt`).
- `gte`: Greater than or equal to.
- `gt`: Greater than.
- `lte`: Less than or equal to.
- `lt`: Less than.

May only be applied to string properties and number properties.


---

### SequencesRangeValue

An upper or lower bound in a `range` filter.

---

### SequencesSearchDTO

**プロパティ**:

- `filter` (unknown, 任意)
- `search` (unknown, 任意)
- `limit` (integer, 任意)
  - Returns up to this many results.

---

### SequencesSearchFilter

**プロパティ**:

- `search` (object, 必須)
  - Matches items where the provided string property contains the given value according to a fuzzy search.
The value may exist anywhere in the string, even as a part of a word or with some grammatical
variations (e.g., searching for "run" will also find "ran").

The supported properties are `name` and `description`. It's also possible to set `property`
to `["query"]`, in which case both `name` and `description` will be searched.

When the `search` filter is being used, and it's not nested inside a `not` filter (directly or indirectly),
and no `sort` is specified, the results will be ranked according to how good the match is.
When the `query` 'property' is specified, matches in `name` will rank higher than matches in `description`.
The `search` filter may be used at most twice within the same `advancedFilter` expression.


---

### SequencesSort

**プロパティ**:

- `sort` (array, 任意)
  - Sort by array of selected properties.


---

### SequencesSortItem

**プロパティ**:

- `property` (array, 必須)
  - Property to sort on.
Sorting can be done on the following properties:
  | Property                          |
  |-----------------------------------|
  | `['assetId']`                     |
  | `['createdTime']`                 |
  | `['dataSetId']`                   |
  | `['description']`                 |
  | `['externalId']`                  |
  | `['lastUpdatedTime']`             |
  | `['metadata', '<someCustomKey>']` |
  | `['name']`                        |
  | `['_score_']`                     |
- `order` (string, 任意)
  - The `order` attribute is optional and defaults to `desc` for `_score_` and `asc` for all
other properties.
- `nulls` (string, 任意)
  - The `nulls` attribute is optional and defaults to `auto`. `auto` is translated to `last`
for the `asc` order and to `first` for the `desc` order.

---

### SequencesStringValue

A value that you wish to find in the provided property.


---

### SequencesUniquePropertiesAggregate

---

### SequencesUniquePropertiesAggregateResponse

**プロパティ**:

- `items` (array, 必須)

---

### SequencesUniqueValuesAggregate

---

### SequencesUniqueValuesAggregateResponse

**プロパティ**:

- `items` (array, 必須)

---

### SequencesUpdate

---

### SequencesUpdateByExternalId

---

### SequencesUpdateById

---

### SequencesValue

A value that you wish to find in the provided property.


---

### SequencesValues

One or more values that you wish to find in the provided properties.


---

### ServiceAccountCreator

The principal that created the entity.

---

### ServiceAccountCreatorUser

The user that created the entity.

**プロパティ**:

- `orgId` (unknown, 必須)
- `userId` (unknown, 必須)

---

### ServiceAccountDescription

Longer description of a service account

---

### ServiceAccountDto

---

### ServiceAccountId

Unique identifier of a service account

---

### ServiceAccountName

Human-readable name of a service account

---

### ServicePrincipal

**プロパティ**:

- `userIdentifier` (string, 必須)
  - Uniquely identifies the principal the profile is associated with.

- `displayName` (string, 必須)
  - The display name for the service account
- `pictureUrl` (string, 必須)
  - The avatar of the service account
- `identityType` (string, 必須)

---

### Session

**プロパティ**:

- `id` (number, 任意)
  - ID of the session
- `type` (string, 任意)
  - Values reserved for future use
- `status` (string, 任意)
  - Current status of the session
- `creationTime` (unknown, 任意)
  - Session creation time, in milliseconds since 1970
- `expirationTime` (unknown, 任意)
  - Session expiry time, in milliseconds since 1970. This value is updated on refreshing a token
- `clientId` (string, 任意)
  - Client ID in identity provider. Returned only if the session was created using client credentials

---

### SessionByIds

**プロパティ**:

- `items` (array, 任意)

---

### SessionCredentials

Credentials for authenticating towards CDF using a CDF session.

**プロパティ**:

- `nonce` (string, 必須)
  - Session nonce for a recently created CDF Session.

---

### SessionId

Id of the session.

---

### SessionInfo

Details for the session used to read from the source project.

**プロパティ**:

- `clientId` (string, 任意)
  - Idp client ID
- `sessionId` (integer, 任意)
  - CDF session ID
- `projectName` (string, 任意)
  - CDF project name

---

### SessionList

**プロパティ**:

- `items` (array, 任意)
- `nextCursor` (string, 任意)
  - Cursor to get the next page of results (if available).
- `previousCursor` (string, 任意)
  - Cursor to get the previous page of results (if available).

---

### SessionReferenceIds

**プロパティ**:

- `items` (array, 必須)

---

### SessionTokenRequest



---

### SessionTokenResponse



**プロパティ**:

- `id` (number, 必須)
  - ID of the session.
- `accessToken` (string, 必須)
  - Internal access token, for use by the internal service against CDF.
- `expiresIn` (number, 必須)
  - Expiration time for the access token, seconds into the future.
- `sessionKey` (string, 任意)
  - Session key for the session, to be used to refresh the session. Only provided when binding a session.
- `sessionExpirationTime` (integer, 必須)
  - Unix timestamp in milliseconds of when the session will expire if it is not refreshed.

- `sessionIsExtendable` (unknown, 必須)
  - If true, sessionExpirationTime can be extended on refresh. If false, sessionExpirationTime will remain the same on refresh.


---

### SetAssetSource

**プロパティ**:

- `set` (unknown, 必須)

---

### SetBooleanField

**プロパティ**:

- `set` (boolean, 必須)

---

### SetClaimNames

**プロパティ**:

- `set` (unknown, 必須)

---

### SetDataSetDescription

**プロパティ**:

- `set` (string, 必須)
  - Set a new value for the string, or remove the value.

---

### SetDataSetId

**プロパティ**:

- `set` (unknown, 必須)

---

### SetDataSetName

**プロパティ**:

- `set` (string, 必須)
  - Set a new value for the string, or remove the value.

---

### SetDescription

**プロパティ**:

- `set` (unknown, 必須)

---

### SetDirectRelationReference

**プロパティ**:

- `set` (unknown, 必須)

---

### SetEventSource

**プロパティ**:

- `set` (unknown, 必須)

---

### SetEventSubType

**プロパティ**:

- `set` (unknown, 必須)

---

### SetEventType

**プロパティ**:

- `set` (unknown, 必須)

---

### SetExternalId

**プロパティ**:

- `set` (unknown, 必須)

---

### SetGeoLocation

**プロパティ**:

- `set` (unknown, 必須)

---

### SetGroupCallbackEnabled

**プロパティ**:

- `set` (unknown, 必須)

---

### SetIntegerField

**プロパティ**:

- `set` (integer, 必須)

---

### SetLongField

**プロパティ**:

- `set` (integer, 必須)

---

### SetModelNameField

**プロパティ**:

- `set` (string, 任意)

---

### SetNull_FlatOidcCredentialsUpdate

**プロパティ**:

- `setNull` (boolean, 必須)

---

### SetNull_Long

**プロパティ**:

- `setNull` (boolean, 必須)

---

### SetNull_NonceCredentials

**プロパティ**:

- `setNull` (boolean, 必須)

---

### SetOidcConfigurationUpdateDTO

**プロパティ**:

- `set` (unknown, 必須)

---

### SetRevisionCameraProperties

**プロパティ**:

- `set` (unknown, 任意)

---

### SetRevisionRotation

**プロパティ**:

- `set` (array, 任意)

---

### SetRevisionScale

**プロパティ**:

- `set` (array, 任意)

---

### SetRevisionTranslation

**プロパティ**:

- `set` (array, 任意)

---

### SetStringField

**プロパティ**:

- `set` (string, 必須)

---

### SetUserProfilesConfigurationDTO

**プロパティ**:

- `set` (unknown, 必須)

---

### SetValue_ConflictMode

Set a new value for the transformation conflictMode (action).

**プロパティ**:

- `set` (unknown, 必須)

---

### SetValue_DataSource

Set a new value for the transformation destination type. Indicates result resource type.

**プロパティ**:

- `set` (unknown, 必須)

---

### SetValue_FlatOidcCredentialsUpdate

**プロパティ**:

- `set` (unknown, 必須)

---

### SetValue_Long

**プロパティ**:

- `set` (integer, 必須)

---

### SetValue_NonceCredentials

**プロパティ**:

- `set` (unknown, 必須)

---

### SetValue_Seq_String

**プロパティ**:

- `set` (array, 任意)
  - List of tags for the Transformation.

---

### SignificantHasDataFiltersNotice

**プロパティ**:

- `code` (string, 必須)
- `category` (string, 必須)
- `level` (string, 必須)
- `hint` (string, 必須)
- `grade` (string, 必須)
- `resultExpression` (string, 必須)
  - Identifier for the result set expression that the notice applies to.
- `containers` (array, 必須)
  - List of containers that the notice applies to.

---

### SignificantPostFilteringNotice

**プロパティ**:

- `code` (string, 必須)
- `category` (string, 必須)
- `level` (string, 必須)
- `hint` (string, 必須)
- `grade` (string, 必須)
- `resultExpression` (string, 必須)
  - Identifier for the result set expression that the notice applies to.
- `limit` (integer, 必須)
  - The specified limit for the result expression.
- `maxInvolvedRows` (integer, 必須)
  - Number of rows of data that is internally processed. Value gives an indication of the complexity of evaluating the query.

---

### SimpleBasicAuthentication

Username and password authentication.

**プロパティ**:

- `type` (string, 必須)
  - Authentication type
- `username` (string, 必須)
  - Username used for authenticating with the broker.

---

### SimpleBasicAuthenticationWrite

Username and password authentication.

**プロパティ**:

- `type` (string, 必須)
  - Authentication type
- `username` (string, 必須)
  - Username used for basic authentication.
- `password` (string, 任意)
  - Password used for basic authentication.

---

### SimpleError

Cognite API error.

**プロパティ**:

- `code` (integer, 必須)
  - HTTP status code.
- `message` (string, 必須)
  - Error message.

---

### SimulationInputOverride

**プロパティ**:

- `referenceId` (string, 必須)
  - Reference id of the value to override
- `value` (unknown, 必須)
  - Override the value used for simulation run
- `unit` (unknown, 任意)
  - Override the unit of the value

---

### SimulationRunCallbackCommand

**プロパティ**:

- `id` (integer, 必須)
  - Simulation run id
- `status` (unknown, 必須)
  - Status value to set for the given simulation run
- `statusMessage` (string, 任意)
  - Status message, useful especially when status is 'failure'
- `simulationTime` (integer, 任意)
  - Simulation time in milliseconds. Timestamp when the input data was sampled. Used for indexing input and output time series.
- `inputs` (array, 任意)
  - List of input values used for the simulation run
- `outputs` (array, 任意)
  - List of output values generated by the simulation run

---

### SimulationRunCommandByRoutine

**プロパティ**:

- `runTime` (integer, 任意)
  - Run time in milliseconds. Reference timestamp used for data pre-processing and data sampling. If not provided, the creation time of the simulation run is used.
- `queue` (boolean, 任意)
  - Queue the simulation run when connector is down.
- `logSeverity` (unknown, 任意)
  - Override the minimum severity level for the simulation run logs. If not provided, the minimum severity is read from the connector logger configuration.
- `inputs` (array, 任意)
  - List of input overrides
- `routineExternalId` (string, 必須)
  - Routine external id

---

### SimulationRunCommandByRoutineRevision

**プロパティ**:

- `runTime` (integer, 任意)
  - Run time in milliseconds. Reference timestamp used for data pre-processing and data sampling. If not provided, the creation time of the simulation run is used.
- `queue` (boolean, 任意)
  - Queue the simulation run when connector is down.
- `logSeverity` (unknown, 任意)
  - Override the minimum severity level for the simulation run logs. If not provided, the minimum severity is read from the connector logger configuration.
- `inputs` (array, 任意)
  - List of input overrides
- `routineRevisionExternalId` (string, 必須)
  - Routine revision external id
- `modelRevisionExternalId` (string, 必須)
  - Model revision external id

---

### SimulationRunDataItem

**プロパティ**:

- `runId` (integer, 必須)
- `inputs` (array, 必須)
- `outputs` (array, 必須)

---

### SimulationRunStatus

---

### SimulationRunType

---

### SimulationRunView

**プロパティ**:

- `dataSetId` (integer, 必須)
  - Dataset id of the resource
- `id` (integer, 必須)
  - A unique id of a simulation run
- `simulatorExternalId` (string, 必須)
  - External id of the simulator. Null for the legacy runs
- `simulatorIntegrationExternalId` (string, 必須)
  - External id of the simulator integration. Null for the legacy runs
- `modelExternalId` (string, 必須)
  - External id of the simulation model. Null for the legacy runs
- `modelRevisionExternalId` (string, 必須)
  - External id of the simulator model revision. Null for the legacy runs
- `routineExternalId` (string, 必須)
  - External id of the simulation routine. Null for the legacy runs
- `routineRevisionExternalId` (string, 必須)
  - External id of the simulation routine revision. Null for the legacy runs
- `runTime` (integer, 任意)
  - Run time in milliseconds. Reference timestamp used for data pre-processing and data sampling.
- `simulationTime` (integer, 任意)
  - Simulation time in milliseconds. Timestamp when the input data was sampled. Used for indexing input and output time series.
- `status` (unknown, 必須)
  - Status of the simulation run
- `statusMessage` (string, 任意)
  - Message status of the simulation run
- `runType` (unknown, 必須)
  - Type of the simulation run
- `userId` (string, 必須)
  - User id of the simulation run
- `logId` (integer, 必須)
  - Log ID
- `createdTime` (integer, 必須)
  - The number of milliseconds since epoch
- `lastUpdatedTime` (integer, 必須)
  - The number of milliseconds since epoch

---

### SimulationValue

**プロパティ**:

- `referenceId` (string, 必須)
  - Reference id of the value
- `value` (unknown, 必須)
  - Value used for simulation. Can be string, double, array of strings or array of doubles. Maximum length is 1024 for strings and 1000 for arrays.
- `valueType` (unknown, 必須)
  - Type of the value
- `unit` (unknown, 任意)
  - Unit of the value
- `simulatorObjectReference` (unknown, 任意)
  - Where the value was written to, and for the outputs it is where the value was read from
- `timeseriesExternalId` (string, 任意)
  - External id of the time series where the value was written to
- `overridden` (boolean, 任意)
  - Whether the value was overridden by for this simulation run or used directly from the routine configuration

---

### SimulationValueBase

**プロパティ**:

- `referenceId` (string, 必須)
  - Reference id of the value
- `value` (unknown, 必須)
  - Value used for simulation. Can be string, double, array of strings or array of doubles. Maximum length is 1024 for strings and 1000 for arrays.
- `valueType` (unknown, 必須)
  - Type of the value
- `unit` (unknown, 任意)
  - Unit of the value
- `simulatorObjectReference` (unknown, 任意)
  - Where the value was written to, and for the outputs it is where the value was read from
- `timeseriesExternalId` (string, 任意)
  - External id of the time series where the value was written to

---

### SimulationValueType

---

### SimulationValueUnit

**プロパティ**:

- `name` (string, 必須)
  - Name of the unit
- `externalId` (string, 任意)
  - External id of the unit

---

### SimulationValueUnitInput

**プロパティ**:

- `name` (string, 必須)
  - Name of the unit
- `quantity` (string, 必須)
  - Quantity of measure for the unit

---

### SimulationValueUnitName

**プロパティ**:

- `name` (string, 必須)
  - Name of the unit

---

### SimulationValueUnitQuantity

**プロパティ**:

- `name` (string, 必須)
  - Name of the unit
- `externalId` (string, 任意)
  - External id of the unit
- `quantity` (string, 任意)
  - Quantity of measure for the unit

---

### Simulator

**プロパティ**:

- `id` (integer, 必須)
  - A unique id of a simulator
- `externalId` (string, 必須)
  - External id of the simulator
- `name` (string, 必須)
  - Name of the simulator
- `fileExtensionTypes` (array, 必須)
  - File extension types allowed as a model dependency.
- `modelTypes` (array, 任意)
  - Model types supported by the simulator
- `stepFields` (array, 任意)
  - Step types supported by the simulator when creating routines
- `unitQuantities` (array, 任意)
  - Quantities and their units supported by the simulator
- `createdTime` (integer, 必須)
- `lastUpdatedTime` (integer, 必須)

---

### SimulatorCreateCommand

**プロパティ**:

- `externalId` (string, 必須)
  - External id of the simulator
- `name` (string, 必須)
  - Name of the simulator
- `fileExtensionTypes` (array, 必須)
  - File extension types supported by the simulator
- `modelTypes` (array, 必須)
  - Model types supported by the simulator
- `stepFields` (array, 必須)
  - Step types supported by the simulator when creating routines
- `unitQuantities` (array, 任意)
  - Quantities and their units supported by the simulator

---

### SimulatorFileDependency

**プロパティ**:

- `file` (unknown, 必須)
  - Reference to a file in CDF.
- `arguments` (object, 必須)
  - Simulator-specific arguments for the file dependency. These are used to link the file to a specific object in the target model.

---

### SimulatorFileDependencyFileField

**プロパティ**:

- `id` (integer, 必須)
  - File id of the file dependency. This is used to reference a file in CDF.

---

### SimulatorInput

**プロパティ**:

- `referenceId` (string, 必須)
  - Reference id of the value to override
- `value` (unknown, 必須)
  - Override the value used for a simulation run
- `unit` (unknown, 任意)
  - Override the unit of the value

---

### SimulatorInputsUnit

**プロパティ**:

- `name` (string, 任意)
  - Name of the unit.

---

### SimulatorIntegration

**プロパティ**:

- `dataSetId` (integer, 必須)
  - Dataset id of the resource
- `id` (integer, 必須)
  - A unique id of a simulator integration
- `externalId` (string, 必須)
  - External id of the simulator integration
- `simulatorExternalId` (string, 必須)
  - External id of the simulator
- `heartbeat` (integer, 必須)
  - When was the last time the simulator integration was active
- `connectorVersion` (string, 必須)
  - Connector version of the simulator integration
- `licenseStatus` (unknown, 任意)
  - Whether the license is valid or not
- `simulatorVersion` (string, 任意)
  - Simulator version of the simulator integration
- `licenseLastCheckedTime` (integer, 任意)
  - Timestamp when license was last checked
- `connectorStatus` (unknown, 任意)
  - Status of the connector (indicates whether a calculation is running or not)
- `connectorStatusUpdatedTime` (integer, 任意)
  - Timestamp when status of the connector was last updated
- `active` (boolean, 必須)
  - Connector is considered active if the heartbeat was reported within the last minute
- `logId` (integer, 必須)
  - Log ID
- `createdTime` (integer, 必須)
- `lastUpdatedTime` (integer, 必須)

---

### SimulatorIntegrationConnectorStatus

---

### SimulatorIntegrationConnectorStatusUpdate

**プロパティ**:

- `set` (unknown, 必須)
  - Value to set

---

### SimulatorIntegrationCreateCommand

**プロパティ**:

- `externalId` (string, 必須)
  - External id of the simulator integration
- `simulatorExternalId` (string, 必須)
  - External id of the simulator
- `heartbeat` (integer, 必須)
  - When was the last time the simulator integration was active
- `dataSetId` (integer, 必須)
  - Data set id of the simulator integration
- `connectorVersion` (string, 必須)
  - Connector version of the simulator integration
- `simulatorVersion` (string, 必須)
  - Simulator version of the simulator integration
- `licenseStatus` (unknown, 任意)
  - Whether licese is valid or not
- `licenseLastCheckedTime` (integer, 任意)
  - Timestamp when license was last checked
- `connectorStatus` (unknown, 任意)
  - Status of the connector (indicates whether a calculation is running or not)
- `connectorStatusUpdatedTime` (integer, 任意)
  - Timestamp when status of the connector was last updated

---

### SimulatorIntegrationLicenseStatus

---

### SimulatorIntegrationLicenseStatusUpdate

**プロパティ**:

- `set` (unknown, 必須)
  - Value to set

---

### SimulatorIntegrationUpdate

**プロパティ**:

- `heartbeat` (unknown, 任意)
  - Update when was the last time the simulator integration was active
- `dataSetId` (unknown, 任意)
  - Update data set id of the simulator integration
- `connectorVersion` (unknown, 任意)
  - Update connector version of the simulator integration
- `simulatorVersion` (unknown, 任意)
  - Update simulator version of the simulator integration
- `licenseStatus` (unknown, 任意)
  - Update whether the license is valid or not
- `licenseLastCheckedTime` (unknown, 任意)
  - Update timestamp when license was last checked
- `connectorStatus` (unknown, 任意)
  - Update status of the connector
- `connectorStatusUpdatedTime` (unknown, 任意)
  - Update timestamp when status of the connector was last updated

---

### SimulatorIntegrationUpdateById

**プロパティ**:

- `id` (integer, 必須)
  - Id of the simulator integration
- `update` (unknown, 必須)

---

### SimulatorLog

**プロパティ**:

- `dataSetId` (integer, 必須)
  - Dataset id of the resource
- `id` (integer, 必須)
  - A unique id of a simulator resource log
- `data` (array, 必須)
  - Log data of the simulator resource
- `severity` (unknown, 任意)
  - Minimum severity level of the log data. This overrides connector configuration minimum severity level and can be used for more granular control, e.g. to debug a specific simulation run.
- `createdTime` (integer, 必須)
  - The number of milliseconds since epoch
- `lastUpdatedTime` (integer, 必須)
  - The number of milliseconds since epoch

---

### SimulatorLogData

**プロパティ**:

- `timestamp` (integer, 必須)
  - Timestamp of the log message
- `message` (string, 必須)
  - Log message
- `severity` (unknown, 必須)
  - Log severity level

---

### SimulatorLogDataListUpdateAdd

**プロパティ**:

- `add` (array, 必須)
  - Value to add

---

### SimulatorLogSeverityLevel

---

### SimulatorLogUpdate

**プロパティ**:

- `data` (unknown, 必須)
  - Log data of the simulator resource

---

### SimulatorLogUpdateCommand

**プロパティ**:

- `id` (integer, 必須)
  - Id of the simulator resource log
- `update` (unknown, 必須)

---

### SimulatorModel

**プロパティ**:

- `dataSetId` (integer, 必須)
  - Dataset id of the resource
- `id` (integer, 必須)
  - A unique id of a simulation model
- `externalId` (string, 必須)
  - External id of the simulation model
- `simulatorExternalId` (string, 必須)
  - External id of the simulator
- `name` (string, 必須)
  - Name of the simulation model
- `description` (string, 任意)
  - Description of the simulation model
- `type` (string, 任意)
  - Model type of the simulation model. List of available types is available in the simulator resource.
- `createdTime` (integer, 必須)
- `lastUpdatedTime` (integer, 必須)

---

### SimulatorModelCreateCommand

**プロパティ**:

- `externalId` (string, 必須)
  - External id of the simulation model
- `simulatorExternalId` (string, 必須)
  - External id of the simulator
- `name` (string, 必須)
  - Name of the simulation model
- `description` (string, 任意)
  - Description of the simulation model
- `dataSetId` (integer, 必須)
  - Data set id of the simulation model
- `type` (string, 必須)
  - Model type of the simulation model. List of available types is available in the simulator resource.

---

### SimulatorModelDependency

**プロパティ**:

- `fileExtensionTypes` (array, 必須)
  - File extension types allowed as a model dependency.
- `fields` (array, 必須)
  - List of simulator specific fields for the dependency. Used to link the file to a simulator object in the model.

---

### SimulatorModelDependencyField

**プロパティ**:

- `name` (string, 必須)
  - Name of the field
- `label` (string, 必須)
  - Label of the field. Used to render the field label in the GUI.
- `info` (string, 必須)
  - Info of the field. Used to render a tooltip for the field in the GUI.

---

### SimulatorModelDependencyListUpdate

**プロパティ**:

- `set` (array, 必須)
  - List value to set

---

### SimulatorModelRevision

**プロパティ**:

- `dataSetId` (integer, 必須)
  - Dataset id of the resource
- `id` (integer, 必須)
  - A unique id of a simulation model revision
- `externalId` (string, 必須)
  - External id of the simulation model revision
- `simulatorExternalId` (string, 必須)
  - External id of the simulator
- `modelExternalId` (string, 必須)
  - External id of the simulation model
- `description` (string, 任意)
  - Description of the simulation model revision
- `fileId` (integer, 必須)
  - Source file id of the simulation model revision
- `createdByUserId` (string, 必須)
  - Author of the simulation model revision
- `status` (unknown, 任意)
  - Status of the simulation model revision
- `statusMessage` (string, 任意)
  - Status message of the simulation model revision
- `versionNumber` (integer, 必須)
  - Version number of the simulation model revision. Unique per simulation model.
- `logId` (integer, 必須)
  - Log ID
- `createdTime` (integer, 必須)
- `lastUpdatedTime` (integer, 必須)

---

### SimulatorModelRevisionCreateCommand

**プロパティ**:

- `externalId` (string, 必須)
  - External id of the simulation model revision
- `modelExternalId` (string, 必須)
  - External id of the simulation model
- `description` (string, 任意)
  - Description of the simulation model revision
- `fileId` (integer, 必須)
  - Source file id of the simulation model revision

---

### SimulatorModelRevisionDataConnectionType

---

### SimulatorModelRevisionDataFlowsheet

**プロパティ**:

- `simulatorObjectNodes` (array, 必須)
  - List of flowsheet nodes
- `simulatorObjectEdges` (array, 必須)
  - List of flowsheet edges
- `thermodynamics` (unknown, 必須)
  - Thermodynamic data

---

### SimulatorModelRevisionDataFlowsheetListUpdate

**プロパティ**:

- `set` (array, 必須)
  - List value to set

---

### SimulatorModelRevisionDataGraphicalObject

**プロパティ**:

- `position` (unknown, 任意)
  - Position of the graphical object
- `height` (number, 任意)
  - Height of the graphical object
- `width` (number, 任意)
  - Width of the graphical object
- `scaleX` (number, 任意)
  - Horizontal scale factor, negative if object is flipped along y-axis
- `scaleY` (number, 任意)
  - Vertical scale factor, negative if object is flipped along x-axis
- `angle` (number, 任意)
  - Rotation angle
- `active` (boolean, 任意)
  - Whether the object is active

---

### SimulatorModelRevisionDataModelExternalId

**プロパティ**:

- `modelRevisionExternalId` (string, 必須)
  - External id of the model revision

---

### SimulatorModelRevisionDataObjectEdge

**プロパティ**:

- `id` (string, 必須)
  - Edge identifier
- `name` (string, 任意)
  - Edge name
- `sourceId` (string, 必須)
  - Source node identifier
- `targetId` (string, 必須)
  - Target node identifier
- `connectionType` (unknown, 必須)
  - Type of connection

---

### SimulatorModelRevisionDataObjectNode

**プロパティ**:

- `id` (string, 必須)
  - Node identifier
- `name` (string, 任意)
  - Node name
- `type` (string, 必須)
  - Node type
- `graphicalObject` (unknown, 任意)
  - Graphical representation
- `properties` (array, 必須)
  - List of node properties

---

### SimulatorModelRevisionDataPosition

**プロパティ**:

- `x` (number, 必須)
  - X coordinate
- `y` (number, 必須)
  - Y coordinate

---

### SimulatorModelRevisionDataProperty

**プロパティ**:

- `name` (string, 必須)
  - Property name
- `referenceObject` (object, 必須)
  - Reference to the simulator object from which the value is read
- `valueType` (unknown, 必須)
  - Type of the value
- `value` (unknown, 必須)
  - Property value
- `unit` (unknown, 任意)
  - Unit of the property
- `readOnly` (boolean, 任意)
  - Whether the property is read-only

---

### SimulatorModelRevisionDataThermodynamic

**プロパティ**:

- `propertyPackages` (array, 必須)
  - List of property packages
- `components` (array, 必須)
  - List of components

---

### SimulatorModelRevisionDataUpdate

**プロパティ**:

- `flowsheets` (unknown, 任意)
  - Model revision list of flowsheets
- `info` (unknown, 任意)
  - Additional simulator-specific information

---

### SimulatorModelRevisionDataUpdateCommand

**プロパティ**:

- `modelRevisionExternalId` (string, 必須)
  - External id of the model revision
- `update` (unknown, 必須)

---

### SimulatorModelRevisionDataView

**プロパティ**:

- `dataSetId` (integer, 必須)
  - Dataset id of the resource
- `modelRevisionExternalId` (string, 必須)
  - External id of the model revision
- `flowsheets` (array, 任意)
  - List of flowsheets of the model revision
- `info` (object, 任意)
  - Additional simulator-specific information
- `createdTime` (integer, 必須)
- `lastUpdatedTime` (integer, 必須)

---

### SimulatorModelRevisionStatus

---

### SimulatorModelRevisionStatusUpdate

**プロパティ**:

- `set` (unknown, 必須)
  - Value to set

---

### SimulatorModelRevisionUpdate

**プロパティ**:

- `status` (unknown, 任意)
  - Status of the simulation model revision
- `statusMessage` (unknown, 任意)
  - Status message of the simulation model revision

---

### SimulatorModelRevisionUpdateCommand

**プロパティ**:

- `id` (integer, 必須)
  - Id of the simulator model revision
- `update` (unknown, 必須)

---

### SimulatorModelRevisionView

**プロパティ**:

- `dataSetId` (integer, 必須)
  - Dataset id of the resource
- `id` (integer, 必須)
  - A unique id of a simulation model revision
- `externalId` (string, 必須)
  - External id of the simulation model revision
- `simulatorExternalId` (string, 必須)
  - External id of the simulator
- `modelExternalId` (string, 必須)
  - External id of the simulation model
- `description` (string, 任意)
  - Description of the simulation model revision
- `fileId` (integer, 必須)
  - Source file id of the simulation model revision
- `createdByUserId` (string, 必須)
  - Author of the simulation model revision
- `status` (unknown, 任意)
  - Status of the simulation model revision
- `statusMessage` (string, 任意)
  - Status message of the simulation model revision
- `versionNumber` (integer, 必須)
  - Version number of the simulation model revision. Unique per simulation model.
- `logId` (integer, 必須)
  - Log ID
- `createdTime` (integer, 必須)
- `lastUpdatedTime` (integer, 必須)

---

### SimulatorModelType

**プロパティ**:

- `name` (string, 必須)
  - Name of the model type
- `key` (string, 必須)
  - Key of the model type

---

### SimulatorModelTypeListUpdate

**プロパティ**:

- `set` (array, 必須)
  - List value to set

---

### SimulatorModelUpdate

**プロパティ**:

- `name` (unknown, 任意)
  - Name of the model
- `description` (unknown, 任意)
  - Description of the simulation model

---

### SimulatorModelUpdateCommand

**プロパティ**:

- `id` (integer, 必須)
  - Id of the simulator model
- `update` (unknown, 必須)

---

### SimulatorObjectRef

**プロパティ**:


---

### SimulatorQuantity

**プロパティ**:

- `name` (string, 必須)
  - Name of the quantity
- `label` (string, 必須)
  - Label of the quantity. For display purposes
- `units` (array, 必須)
  - Units of measure supported by the simulator for the given quantity

---

### SimulatorQuantityListUpdate

**プロパティ**:

- `set` (array, 必須)
  - List value to set

---

### SimulatorRoutineBase

**プロパティ**:

- `dataSetId` (integer, 必須)
  - Dataset id of the resource
- `id` (integer, 必須)
  - A unique id of a simulation routine
- `externalId` (string, 必須)
  - External id of the simulation routine
- `simulatorExternalId` (string, 必須)
  - External id of the simulator
- `modelExternalId` (string, 必須)
  - External id of the simulation model
- `simulatorIntegrationExternalId` (string, 必須)
  - External id of the simulator integration this routine belongs to
- `name` (string, 必須)
  - Name of the simulation routine
- `description` (string, 任意)
  - Description of the simulation routine
- `createdTime` (integer, 必須)
  - The number of milliseconds since epoch
- `lastUpdatedTime` (integer, 必須)
  - The number of milliseconds since epoch

---

### SimulatorRoutineConfigDisabled

**プロパティ**:

- `enabled` (boolean, 必須)
  - When disabled other fields are not allowed

---

### SimulatorRoutineConfiguration

**プロパティ**:

- `schedule` (unknown, 必須)
- `dataSampling` (unknown, 必須)
- `logicalCheck` (array, 必須)
  - List of logical check rules. Used to find out data points which satisfy the logical check.
- `steadyStateDetection` (array, 必須)
  - List of steady state detection rules. Used to find out data points which are in steady state.
- `inputs` (array, 任意)
  - List of input constants and input time series. Used to define the inputs of the simulation routine.
- `outputs` (array, 任意)
  - List of outputs for the simulation routine.

---

### SimulatorRoutineConfigurationBase

**プロパティ**:

- `schedule` (unknown, 必須)
- `dataSampling` (unknown, 必須)
- `logicalCheck` (array, 必須)
  - List of logical check rules. Used to find out data points which satisfy the logical check.
- `steadyStateDetection` (array, 必須)
  - List of steady state detection rules. Used to find out data points which are in steady state.

---

### SimulatorRoutineConfigurationCreate

**プロパティ**:

- `schedule` (unknown, 必須)
- `dataSampling` (unknown, 必須)
- `logicalCheck` (array, 必須)
  - List of logical check rules. Used to find out data points which satisfy the logical check.
- `steadyStateDetection` (array, 必須)
  - List of steady state detection rules. Used to find out data points which are in steady state.
- `inputs` (array, 任意)
  - List of input constants and input time series. Used to define the inputs of the simulation routine.
- `outputs` (array, 任意)
  - List of outputs for the simulation routine.

---

### SimulatorRoutineCreateCommand

**プロパティ**:

- `externalId` (string, 必須)
  - External id of the simulator routine
- `modelExternalId` (string, 必須)
  - External id of the simulation model
- `simulatorIntegrationExternalId` (string, 必須)
  - External id of the simulator integration this routine belongs to
- `name` (string, 必須)
  - Name of the routine
- `description` (string, 任意)
  - Description of the simulation routine

---

### SimulatorRoutineDataSamplingEnabled

**プロパティ**:

- `enabled` (boolean, 必須)
  - Whether the data sampling is enabled or not
- `validationWindow` (integer, 任意)
  - Validation window of the data sampling. Represented in minutes. Used when either logical check or steady state detection is enabled.
- `samplingWindow` (integer, 必須)
  - Sampling window of the data sampling. Represented in minutes
- `granularity` (integer, 必須)
  - Granularity of the data sampling in minutes

---

### SimulatorRoutineInputConstant

**プロパティ**:

- `name` (string, 必須)
  - Constant name.
- `referenceId` (string, 必須)
  - A unique id of the input to be used in the routine.
- `value` (unknown, 必須)
  - Constant value. For the list, the maximum length is 1000, and the minimum length is 1.
- `valueType` (unknown, 任意)
  - Constant value type.
- `unit` (unknown, 任意)
  - Constant unit.
- `saveTimeseriesExternalId` (string, 任意)
  - Time series external ID to use when saving the input sample in CDF.

---

### SimulatorRoutineInputTimeseries

**プロパティ**:

- `name` (string, 必須)
  - Input/output name.
- `referenceId` (string, 必須)
  - A unique id of the input to be used in the routine.
- `sourceExternalId` (string, 必須)
  - External id of the source time series to read from.
- `aggregate` (unknown, 任意)
  - Aggregate type of the sensor data. Only used if data sampling is enabled. Otherwise, the latest data point value is used.
- `saveTimeseriesExternalId` (string, 任意)
  - Time series external ID to use when saving the input sample in CDF.
- `unit` (unknown, 任意)
  - Time series unit.

---

### SimulatorRoutineLogicalCheckEnabled

**プロパティ**:

- `enabled` (boolean, 必須)
  - Whether the logical check is enabled or not
- `timeseriesExternalId` (string, 必須)
  - External ID of the time series to run the logical check against
- `aggregate` (unknown, 必須)
  - Data points aggregate to be used for the time series
- `operator` (unknown, 必須)
  - Operator of the logical check
- `value` (number, 必須)
  - Value to compare with

---

### SimulatorRoutineOperator

---

### SimulatorRoutineOutput

**プロパティ**:

- `name` (string, 必須)
  - Output name.
- `referenceId` (string, 必須)
  - A unique identifier of the output in a given routine.
- `unit` (unknown, 任意)
  - Output unit.
- `valueType` (unknown, 任意)
  - Output value type.
- `saveTimeseriesExternalId` (string, 任意)
  - External id of the output time series.

---

### SimulatorRoutineRevision

**プロパティ**:

- `dataSetId` (integer, 必須)
  - Dataset id of the resource
- `id` (integer, 必須)
  - A unique id of a simulation routine revision
- `externalId` (string, 必須)
  - External id of the simulation routine revision
- `simulatorExternalId` (string, 必須)
  - External id of the simulator
- `routineExternalId` (string, 必須)
  - External id of the simulation routine
- `simulatorIntegrationExternalId` (string, 必須)
  - External id of the simulator integration this routine belongs to
- `modelExternalId` (string, 必須)
  - External id of the simulation model
- `createdByUserId` (string, 必須)
  - Author of the simulation routine revision
- `versionNumber` (integer, 必須)
  - Version number of the simulation routine revision. Unique per simulation routine.
- `createdTime` (integer, 必須)
  - The number of milliseconds since epoch
- `configuration` (unknown, 必須)
- `script` (array, 必須)
  - Script of the simulation routine revision

---

### SimulatorRoutineRevisionCreateCommand

**プロパティ**:

- `externalId` (string, 必須)
  - External id of the simulator routine revision
- `routineExternalId` (string, 必須)
  - External id of the simulation routine
- `configuration` (unknown, 必須)
  - When creating a simulator routine revision, be mindful of the following:

- The amount and size of the inputs you provide to the simulator routine.
- The size of the outputs you expect from the simulator routine.

The total size of the simulator routine revision request body can be a maximum of 50.00 KB.

The total size of the data sent by a simulator connector cannot exceed 100.00 KB per simulation run.
- `script` (array, 必須)
  - Script of the simulation routine revision

---

### SimulatorRoutineRevisionView

**プロパティ**:

- `dataSetId` (integer, 必須)
  - Dataset id of the resource
- `id` (integer, 必須)
  - A unique id of a simulation routine revision
- `externalId` (string, 必須)
  - External id of the simulation routine revision
- `simulatorExternalId` (string, 必須)
  - External id of the simulator
- `routineExternalId` (string, 必須)
  - External id of the simulation routine
- `simulatorIntegrationExternalId` (string, 必須)
  - External id of the simulator integration this routine belongs to
- `modelExternalId` (string, 必須)
  - External id of the simulation model
- `createdByUserId` (string, 必須)
  - Author of the simulation routine revision
- `versionNumber` (integer, 必須)
  - Version number of the simulation routine revision. Unique per simulation routine.
- `createdTime` (integer, 必須)
  - The number of milliseconds since epoch
- `configuration` (unknown, 必須)

---

### SimulatorRoutineScheduleEnabled

**プロパティ**:

- `enabled` (boolean, 必須)
  - Whether the schedule is enabled or not
- `cronExpression` (string, 必須)
  - Cron expression representing the schedule.
Examples:
- @annually: Run once a year at midnight of January 1st.
- @daily: Run once a day at midnight.
- */5 * * * *: Run every 5 minutes.
- 0 0 * * *: Run once a day at midnight.
- 0 0 * * MON-FRI: Run once a day at midnight, Monday through Friday.

---

### SimulatorRoutineStage

**プロパティ**:

- `order` (integer, 必須)
  - Stage number
- `description` (string, 任意)
  - Stage description
- `steps` (array, 必須)
  - Stage steps

---

### SimulatorRoutineSteadyStateDetectionEnabled

**プロパティ**:

- `enabled` (boolean, 必須)
  - Whether the steady state detection is enabled or not
- `timeseriesExternalId` (string, 任意)
  - External ID of the time series to be evaluated
- `aggregate` (unknown, 必須)
  - Aggregate type of the steady state detection
- `minSectionSize` (integer, 必須)
  - Min section size of the steady state detection
- `varThreshold` (number, 必須)
  - Var threshold of the steady state detection
- `slopeThreshold` (number, 必須)
  - Slope threshold of the steady state detection

---

### SimulatorRoutineStepArgumentsCommand

**プロパティ**:


---

### SimulatorRoutineStepArgumentsGet

**プロパティ**:

- `referenceId` (string, 必須)
  - A unique id of the output defined in the routine configuration.

---

### SimulatorRoutineStepArgumentsSet

**プロパティ**:

- `referenceId` (string, 必須)
  - A unique id to the input time series (or a constant) defined in the routine configuration.

---

### SimulatorRoutineStepCommand

Used to run a command in a simulation routine. For example, to run a solver.

**プロパティ**:

- `order` (integer, 必須)
  - Step number
- `description` (string, 任意)
  - Step description
- `stepType` (string, 必須)
  - Step type
- `arguments` (unknown, 必須)

---

### SimulatorRoutineStepGet

Used to extract the output of a simulation routine and write it back into the Cognite Data Fusion.

**プロパティ**:

- `order` (integer, 必須)
  - Step number
- `description` (string, 任意)
  - Step description
- `stepType` (string, 必須)
  - Step type
- `arguments` (unknown, 必須)
  - Step arguments

---

### SimulatorRoutineStepSet

Used to set the input of a simulation routine from timeseries or constant fields.

**プロパティ**:

- `order` (integer, 必須)
  - Step number
- `description` (string, 任意)
  - Step description
- `stepType` (string, 必須)
  - Step type
- `arguments` (unknown, 必須)
  - Step arguments

---

### SimulatorStep

**プロパティ**:

- `stepType` (string, 必須)
  - Step type
- `fields` (array, 必須)
  - Fields of the step

---

### SimulatorStepField

**プロパティ**:

- `name` (string, 必須)
  - Name of the field
- `label` (string, 必須)
  - Label of the field. Used to render the field label in the GUI.
- `info` (string, 必須)
  - Info of the field. Used to render a tooltip for the field in the GUI.
- `options` (array, 任意)
  - Options to show in the GUI dropdown of the routine builder

---

### SimulatorStepListUpdate

**プロパティ**:

- `set` (array, 必須)
  - List value to set

---

### SimulatorStepOption

**プロパティ**:

- `label` (string, 必須)
  - Label that will be shown in the dropdown in the GUI of the routine builder
- `value` (string, 必須)
  - Name of the dropdown item

---

### SimulatorTaskOutput

Simulation run execution results.

**プロパティ**:

- `runId` (integer, 任意)
  - The ID of the simulation run instance.
- `logId` (integer, 任意)
  - The log ID of the simulation run.
- `statusMessage` (string, 任意)
  - The status message of the simulation run instance.

---

### SimulatorTaskParameters

Parameters for the Simulation run task type.

**プロパティ**:

- `simulation` (object, 任意)

---

### SimulatorUnitEntry

**プロパティ**:

- `label` (string, 必須)
  - Label of the unit. For display purposes
- `name` (string, 必須)
  - Name of the unit

---

### SimulatorUpdate

**プロパティ**:

- `name` (unknown, 任意)
  - Name of the simulator
- `fileExtensionTypes` (unknown, 任意)
  - File extension types supported by the simulator
- `modelTypes` (unknown, 任意)
  - Model types supported by the simulator
- `stepFields` (unknown, 任意)
  - Step types supported by the simulator when creating routines
- `unitQuantities` (unknown, 任意)
  - Quantities and their units supported by the simulator

---

### SimulatorUpdateCommand

**プロパティ**:

- `id` (integer, 必須)
  - Id of the simulator resource
- `update` (unknown, 必須)

---

### SingleItemIdRef

**プロパティ**:

- `items` (array, 必須)
  - A list with a single item

---

### SingleItemIdRefOrExternalIdRef

**プロパティ**:

- `items` (array, 必須)
  - A list with a single item

---

### SingleItemRunIdRef

**プロパティ**:

- `items` (array, 必須)
  - A list with a single item

---

### SingleItemSimulationRunCallbackCommand

**プロパティ**:

- `items` (array, 必須)
  - A list with a single item

---

### SingleItemSimulationRunCommandByRoutineOrByRoutineRevision

**プロパティ**:

- `items` (array, 必須)
  - A list with a single item

---

### SingleItemSimulatorCreateCommand

**プロパティ**:

- `items` (array, 必須)
  - A list with a single item

---

### SingleItemSimulatorIntegrationCreateCommand

**プロパティ**:

- `items` (array, 必須)
  - A list with a single item

---

### SingleItemSimulatorIntegrationUpdateById

**プロパティ**:

- `items` (array, 必須)
  - A list with a single item

---

### SingleItemSimulatorLogUpdateCommand

**プロパティ**:

- `items` (array, 必須)
  - A list with a single item

---

### SingleItemSimulatorModelCreateCommand

**プロパティ**:

- `items` (array, 必須)
  - A list with a single item

---

### SingleItemSimulatorModelRevisionCreateCommand

**プロパティ**:

- `items` (array, 必須)
  - A list with a single item

---

### SingleItemSimulatorModelRevisionDataModelExternalId

**プロパティ**:

- `items` (array, 必須)
  - A list with a single item

---

### SingleItemSimulatorModelRevisionDataUpdateCommand

**プロパティ**:

- `items` (array, 必須)
  - A list with a single item

---

### SingleItemSimulatorModelRevisionUpdateCommand

**プロパティ**:

- `items` (array, 必須)
  - A list with a single item

---

### SingleItemSimulatorModelUpdateCommand

**プロパティ**:

- `items` (array, 必須)
  - A list with a single item

---

### SingleItemSimulatorRoutineCreateCommand

**プロパティ**:

- `items` (array, 必須)
  - A list with a single item

---

### SingleItemSimulatorRoutineRevisionCreateCommand

**プロパティ**:

- `items` (array, 必須)
  - A list with a single item

---

### SingleItemSimulatorUpdateCommand

**プロパティ**:

- `items` (array, 必須)
  - A list with a single item

---

### SinglePatchAssetSource

Set a new value for the source, or remove the value.

---

### SinglePatchBoolean

**プロパティ**:

- `set` (boolean, 必須)

---

### SinglePatchDataModelingStatus

Sets the data modeling status of the project. Only permitted to be updated by cluster administrators.

**プロパティ**:

- `set` (unknown, 必須)

---

### SinglePatchDataProductVersionStatus

Set a new value for the data product version status.

**プロパティ**:

- `set` (unknown, 必須)

---

### SinglePatchDataSetDescription

---

### SinglePatchDataSetId

---

### SinglePatchDataSetName

---

### SinglePatchDirectRelationReference

---

### SinglePatchEventSource

Set a new value for the source, or remove the value.

---

### SinglePatchEventSubType

---

### SinglePatchEventType

---

### SinglePatchExternalId

Set a new value for the externalId, or remove the value. Must be unique for the resource type.

---

### SinglePatchGeoLocation

Set a new value for the geoLocation, or remove the value.

---

### SinglePatchIsOidcEnabled

Sets if OIDC should be enabled for the project. If set to `false`, the project is considered "locked".

**プロパティ**:

- `set` (boolean, 必須)

---

### SinglePatchLeafProject

Sets if the project should be a leaf project or not. Currently only permitted to be updated by cluster administrators.

Cluster administrators should exercise caution when updating the `leafProject` status of a project, as it has the potential
to break the invariants that only leaf projects can contain industrial data (assets, timeseries, etc.), or that leaf projects
should not have child projects.

Typically the `leafProject` state should only be updated if a project has been mistakenly created with the wrong status.


**プロパティ**:

- `set` (boolean, 必須)

---

### SinglePatchLong

Set a new value for the long, or remove the value.

---

### SinglePatchParentProjectId

Change the ID of the parent project. Currently only permitted to be updated by cluster administrators.

**プロパティ**:

- `set` (integer, 必須)

---

### SinglePatchRequiredExternalId

Change the external ID of the object.

**プロパティ**:

- `set` (unknown, 必須)

---

### SinglePatchRequiredInternalId

Change the ID of the object.

**プロパティ**:

- `set` (unknown, 必須)

---

### SinglePatchRequiredModelDescription

Set a new value for description.

**プロパティ**:

- `set` (unknown, 必須)

---

### SinglePatchRequiredModelName

Set a new value for name.

**プロパティ**:

- `set` (unknown, 必須)

---

### SinglePatchRequiredName

Set a new value for the asset name.

**プロパティ**:

- `set` (unknown, 必須)

---

### SinglePatchRequiredParentExternalId

Change the external ID of the object.

**プロパティ**:

- `set` (unknown, 必須)

---

### SinglePatchRequiredString

Set a new value for the string.

**プロパティ**:

- `set` (string, 必須)

---

### SinglePatchReservedPrefix

---

### SinglePatchResourceDescription

---

### SinglePatchString

Set a new value for the string, or remove the value.

---

### SlimEdgeDefinition

Edge

**プロパティ**:

- `instanceType` (string, 必須)
- `version` (integer, 必須)
  - Current version of the edge
- `wasModified` (boolean, 必須)
  - Whether or not the edge was modified by this ingestion. We only update the edges if the input differs from the existing state.
- `space` (unknown, 必須)
  - Id of the space that the edge belongs to
- `externalId` (unknown, 必須)
  - Unique alphanumeric identifier for the edge
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)

---

### SlimNodeDefinition

Node

**プロパティ**:

- `instanceType` (string, 必須)
- `version` (integer, 必須)
  - Current version of the node
- `wasModified` (boolean, 必須)
  - Whether or not the node was modified by this ingestion. We only update the nodes if the input differs from the existing state.
- `space` (unknown, 必須)
  - Id of the space that the node belongs to
- `externalId` (unknown, 必須)
  - Unique identifier for the node
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)

---

### SlimNodeOrEdge

---

### Solution

A solution represents a connection between an extractor and a source system it can be configured to read from.

**プロパティ**:

- `externalId` (unknown, 必須)
- `name` (string, 必須)
  - Solution name.
- `documentation` (string, 任意)
  - Long-form documentation of the solution, describing how to use the referenced extractor to connect to the referenced source.
- `type` (unknown, 任意)
- `sourceSystemExternalId` (string, 必須)
  - External ID of the source system of this solution.
- `extractorExternalId` (string, 任意)
  - External ID of the extractor of this solution.

---

### Sort

**プロパティ**:

- `sort` (array, 任意)
  - Ordered list of sorting specifications (property, direction, etc) to sort on.


---

### SortByCreatedTime

**プロパティ**:

- `order` (unknown, 必須)
  - Sort order
- `property` (string, 必須)
  - Name of the property to sort by

---

### SortBySimulationTime

**プロパティ**:

- `order` (unknown, 必須)
  - Sort order
- `property` (string, 必須)
  - Name of the property to sort by

---

### SortNotBackedByIndexNotice

**プロパティ**:

- `code` (string, 必須)
- `category` (string, 必須)
- `level` (string, 必須)
- `hint` (string, 必須)
- `grade` (string, 必須)
- `sort` (array, 必須)
- `resultExpression` (string, 必須)
  - Identifier for the result set expression that the notice applies to.

---

### SortProperty

**プロパティ**:

- `property` (array, 必須)
  - Property to sort on.
Sorting can be done on the following properties:
| Property                        |
|---------------------------------|
| `["createdTime"]`               |
| `["dataSetId"]`                 |
| `["description"]`               |
| `["endTime"]`                   |
| `["externalId"]`                |
| `["lastUpdatedTime"]`           |
| `["metadata", "someCustomKey"]` |
| `["source"]`                    |
| `["startTime"]`                 |
| `["subtype"]`                   |
| `["type"]`                      |
| `["_score_"]`                   |
- `order` (string, 任意)
  - The `order` attribute is optional and defaults to `desc` for `_score_` and `asc` for all other properties.

- `nulls` (string, 任意)
  - The `nulls` attribute is optional and defaults to `auto`.
`auto` is translated to `last` for the `asc` order and to `first` for the `desc` order by the service.

---

### SortPropertyIla

Property a client wants to sort on.
To represent fully qualified properties, Records use arrays of strings.
The Records sorting supports both container and top level properties.

The property array has 1 or 3 segments.
1 segment property is a top level property. The following top level properties are supported:
`["space"]`, `["externalId"]`, `["createdTime"]`, `["lastUpdatedTime"]`.
3 segment property is a container level property.
The property array for container level property always has 3 segments:
- the first segment is the space that the container belongs to,
- the second segment is the container external id,
- the third segment is the property id inside the container.

<u>Example:</u>

You have the schema
```
{
  "mySpace": {
    "myContainer": {
      "myProperty": {...}
    }
  }
}
```

Use `["mySpace", "myContainer", "myProperty"]` array to sort the records on `myProperty` property.


---

### SortV3

**プロパティ**:

- `sort` (array, 任意)

---

### SortingNotice

---

### SourceExternalId

The external id for the source-object of the match.

---

### SourceId

The id for the from-object of the match.

---

### SourceReference

Reference to a view, or a container

---

### SourceSelector

List of containers and their properties which values should be selected for the response.


---

### SourceSelectorV3

---

### SourceSelectorWithoutPropertiesV3

Retrieve properties from the listed - by reference - views. The node/edge must have data in all the sources defined in the list.

---

### SourceSystem

A source system representing a source extractors may read from.

**プロパティ**:

- `externalId` (unknown, 必須)
- `name` (string, 必須)
  - Source system name.
- `description` (string, 必須)
  - Short description of the source system.
- `documentation` (string, 任意)
  - Long-form description of the source system.
- `imageUrl` (string, 任意)
  - URL of a publicly hosted logo for the source system.
- `type` (unknown, 必須)
- `tags` (array, 任意)
  - A list of tags, used for searching and filtering.

---

### SourceUpdateField

Set a new value for source.

**プロパティ**:

- `set` (string, 任意)
  - Source for Extraction Pipeline

---

### Sources

List of custom source object to match from, for example, time series. String key -> value. Only string values are considered in the matching. Both `id` and `externalId` fields are optional, only mandatory if the item is to be referenced in `trueMatches`.

---

### Space

---

### SpaceCreateCollection

**プロパティ**:

- `items` (array, 必須)
  - List of spaces to create/update

---

### SpaceCreateDefinition

**プロパティ**:

- `space` (string, 必須)
  - The Space identifier (id).


Note that we have reserved the use of certain space ids.  These reserved spaces are:
 * `space`
 * `cdf`
 * `dms`
 * `pg3`
 * `shared`
 * `system`
 * `node`
 * `edge`
 
- `description` (string, 任意)
  - Used to describe the space you're defining.
- `name` (string, 任意)
  - Human-readable name for the space.

---

### SpaceDefinition

---

### SpaceSpecification

---

### SpaceStatistics

Space-id with associated data modeling statistics

**プロパティ**:

- `space` (string, 必須)
  - The space name
- `containers` (integer, 必須)
  - Number of containers in the space
- `views` (integer, 必須)
  - Number of views including all versions in the space
- `dataModels` (integer, 必須)
  - Number of data models including all versions in the space
- `edges` (integer, 必須)
  - Number of edges in the space
- `softDeletedEdges` (integer, 必須)
  - Number of soft-deleted edges in the space
- `nodes` (integer, 必須)
  - Number of nodes in the space
- `softDeletedNodes` (integer, 必須)
  - Number of soft-deleted nodes in the space

---

### SpaceStatisticsResponseList

**プロパティ**:

- `items` (array, 必須)

---

### SpatialData

**プロパティ**:

- `xMax` (number, 必須)
  - Normalized x coordinate.
- `xMin` (number, 必須)
  - Normalized x coordinate.
- `yMax` (number, 必須)
  - Normalized x coordinate.
- `yMin` (number, 必須)
  - Normalized x coordinate.

---

### StateAggregateFunctions

**プロパティ**:

- `stateAggregates` (array, 任意)
  - Aggregates per state value

---

### Status

The status of the annotation

---

### StatusSchema

**プロパティ**:

- `status` (unknown, 必須)
- `createdTime` (unknown, 必須)
- `startTime` (unknown, 必須)
- `statusTime` (unknown, 必須)
- `errorMessage` (string, 任意)
  - If the job failed, some more information about the error cause.

---

### StrCursor

A cursor pointing to another page of results

---

### StrListUpdate

**プロパティ**:

- `set` (array, 必須)
  - List value to set

---

### StreamCreateConflict

**プロパティ**:

- `error` (object, 必須)
  - Details about the duplicated stream id.


---

### StreamCreateConflictItem

Stream creation conflict info.


**プロパティ**:

- `externalId` (string, 必須)
  - Stream identifier.


---

### StreamDeleteItem

Stream identifier to delete.


**プロパティ**:

- `externalId` (string, 必須)
  - Stream identifier. The identifier must be a valid stream identifier.
Stream identifiers can only consist of alphanumeric characters, hyphens, and underscores.
It must not start with cdf_ or cognite_, as those are reserved for Cognite internal use.
Stream id cannot be logs or records. Max length is 100 characters.


---

### StreamDeleteRequest

Request to delete a stream.


**プロパティ**:

- `items` (array, 必須)
  - List of streams to delete. Currently limited to 1 item.


---

### StreamLifecycleSettings

Data lifecycle settings. These settings are populated from the stream creation template
and there is no easy way to change them. This information is meant to be human-readable
and is not really meant for machine consumption.


**プロパティ**:

- `dataDeletedAfter` (string, 任意)
  - This setting is available only for immutable streams.
ISO-8601 formatted time specifying how long to retain a record in this stream.
After this time passes, records are scheduled to be removed from the stream.
Record removal happens in batches, and some time may pass between a record
being scheduled for deletion and it actually being deleted. During this time,
the record will remain available, and contributing to
consumption of limits.

- `retainedAfterSoftDelete` (string, 必須)
  - Time until the soft deleted stream will actually be deleted by the system,
in an ISO-8601 compliant date-time format. Data cannot be ingested to,
or queried from, a soft-deleted stream. If a stream has been deleted by mistake,
it can still be recovered from soft-deleted state.


---

### StreamLimit

**プロパティ**:

- `provisioned` (number, 必須)
  - Amount of resource provisioned.

- `consumed` (number, 任意)
  - Amount of resource consumed.

This value will be returned only in a Retrieve stream response,
and only if ```includeStatistics``` request parameter is ```true```.
Currently, calculation of this value has been implemented for only a subset
of stream limits.


---

### StreamLimitSettings

Limits and usage.


**プロパティ**:

- `maxRecordsTotal` (unknown, 必須)
  - Maximum number of records that can be stored in the stream.

- `maxGigaBytesTotal` (unknown, 必須)
  - Maximum amount of data that can be stored in the stream, in gigabytes.

- `maxFilteringInterval` (string, 任意)
  - Maximum length of time that the ```lastUpdatedTime``` filter can retrieve records for,
in ISO-8601 format. This setting is only available for immutable streams.


---

### StreamRequestItem

Stream object.


**プロパティ**:

- `externalId` (string, 必須)
  - Stream identifier. The identifier must be unique within the project and must be a valid stream identifier.
Stream identifiers can only consist of alphanumeric characters, hyphens, and underscores.
It must not start with cdf_ or cognite_, as those are reserved for Cognite internal use.
Stream id cannot be logs or records. Max length is 100 characters.

- `settings` (unknown, 必須)

---

### StreamResponse

Stream response.


**プロパティ**:

- `items` (array, 必須)
  - List of streams.


---

### StreamResponseItem

Stream object.


**プロパティ**:

- `externalId` (string, 必須)
  - Stream identifier.

- `createdTime` (unknown, 必須)
- `createdFromTemplate` (string, 必須)
  - Name of the template used for creating this stream.
**Note:** This value is for information only. The template might have been modified
or even entirely deleted after the stream has been created.

- `type` (string, 必須)
  - Defines type of the stream. Both ```Immutable``` and ```Mutable``` streams are available.

Records in an ```Immutable``` stream cannot be updated or deleted by the user.
Instead, depending on the specific template, they can have a limited life span
and be automatically deleted once ```dataDeletedAfter``` interval has passed.
```Immutable``` streams allow ingestion of very large amounts of data.
These streams are most suitable for event or log type data or as a "staging area"
for data that needs to be normalized before ingestion into a more permanent storage.
Immutable streams are optimized for long-term retention and high-volume ingestion,
which results in lower query performance compared to mutable streams.

Records in a ```Mutable``` stream can be updated and deleted by the user,
but total number of records that can be ingested into such a stream is limited.
Mutable streams provide better query performance.

- `settings` (unknown, 必須)

---

### StreamResponseItemSettings

Stream settings.


**プロパティ**:

- `lifecycle` (unknown, 必須)
- `limits` (unknown, 必須)

---

### StringFilter

**プロパティ**:

- `substring` (string, 任意)
  - Substring to find strings, that contains it ignoring case.

---

### StringValue

A unique string value in the field.

**プロパティ**:

- `value` (string, 必須)

---

### SubscriptionDTO

**プロパティ**:

- `externalId` (unknown, 必須)
- `name` (string, 任意)
  - The display name of this subscription.
- `description` (string, 任意)
  - Description of this subscription.
- `dataSetId` (unknown, 任意)
- `partitionCount` (integer, 必須)
  - The number of partitions in this subscriptions
- `timeSeriesCount` (integer, 任意)
  - The number of time series in the subscription.
- `filter` (unknown, 任意)
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)

---

### SubscriptionFilterLanguage

A filter DSL (Domain Specific Language) to define advanced filter queries.

At the top level, a `filter` expression is either a single Boolean filter or a
single leaf filter. Boolean filters contain other Boolean filters and/or leaf filters. The
total number of filters may be at most 100, and the depth (the greatest number of times
filters have been nested inside each other) may be at most 10.


---

### SubscriptionLeafFilter

Leaf filter.


---

### SubscriptionMemberDTO

**プロパティ**:

- `externalId` (unknown, 任意)
- `id` (unknown, 任意)
- `instanceId` (unknown, 任意)

---

### SubscriptionsByIdsRequest

---

### SubscriptionsCreateRequest

**プロパティ**:

- `items` (array, 必須)
  - <details>
<summary>
Subscription definitions. A subscription may be configured explicitly with a list
of time series, or it may be a configured dynamically through a filter. The
subscription cannot change type later.

An explicit list must be manually updated by the user, while a filter will be updated
automatically whenever a time series is added/deleted/updated (eventually consistent).

The filter subscriptions uses the same syntax as advanced filters in the _Filter time series_
endpoint, with two exceptions: The field is named &#x60;filter&#x60; instead of &#x60;advancedFilter&#x60;,
and we do not support the &#x60;search&#x60; LeafFilter.
</summary>

### Advanced filtering

The `filter`
field lets you create complex filtering expressions that combine simple operations,
such as `equals`, `prefix`, and `exists`, by using the Boolean operators `and`, `or`, and `not`.
Filtering applies to basic fields as well as metadata. See the `advancedFilter` syntax in the request example.



#### Supported leaf filters

| Leaf filter    | Supported fields       | Description and example                                                                                                                                                            |
|----------------|------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `containsAll`  | Array type fields      | Only includes results which contain all of the specified values. <br /> `{"containsAll": {"property": ["property"], "values": [1, 2, 3]}}`                                         |
| `containsAny`  | Array type fields      | Only includes results which contain at least one of the specified values. <br /> `{"containsAny": {"property": ["property"], "values": [1, 2, 3]}}`                                |
| `equals`       | Non-array type fields  | Only includes results that are equal to the specified value. <br /> `{"equals": {"property": ["property"], "value": "example"}}`                                                   |
| `exists`       | All fields             | Only includes results where the specified property exists (has a value). <br /> `{"exists": {"property": ["property"]}}`                                                           |
| `in`           | Non-array type fields  | Only includes results that are equal to one of the specified values. <br /> `{"in": {"property": ["property"], "values": [1, 2, 3]}}`                                              |
| `prefix`       | String type fields     | Only includes results which start with the specified text. <br /> `{"prefix": {"property": ["property"], "value": "example"}}`                                                     |
| `range`        | Non-array type fields  | Only includes results that fall within the specified range. <br /> `{"range": {"property": ["property"], "gt": 1, "lte": 5}}` <br /> Supported operators: `gt`, `lt`, `gte`, `lte` |

#### Supported properties

| Property                          | Type               |
|-----------------------------------|--------------------|
| `["description"]`                 | string             |
| `["externalId"]`                  | string             |
| `["metadata", "<someCustomKey>"]` | string             |
| `["name"]`                        | string             |
| `["unit"]`                         | string              |
| `["unitExternalId"]`               | string              |
| `["unitQuantity"]`                 | string              |
| `["instanceId", "space"]`          | string              |
| `["instanceId", "externalId"]`     | string              |
| `["assetId"]`                      | number              |
| `["assetRootId"]`                  | number              |
| `["createdTime"]`                  | number              |
| `["dataSetId"]`                    | number              |
| `["id"]`                           | number              |
| `["lastUpdatedTime"]`              | number              |
| `["isStep"]`                       | Boolean             |
| `["isString"]`                     | Boolean             |
| `["type"]`                         | string              |

#### Limits

- Filter query max depth: 10.
- Filter query max number of clauses: 100.
- `and` and `or` clauses must have at least one element (and at most 99, since each element counts
  towards the total clause limit, and so does the `and`/`or` clause itself).
- The `property` array of each leaf filter has the following limitations:
  - Number of elements in the array is 1 or 2.
  - Elements must not be null or blank.
  - Each element max length is 256 characters.
  - The `property` array must match one of the existing properties (static top-level property or dynamic metadata property).
- `containsAll`, `containsAny`, and `in` filter `values` array size must be in the range [1, 100].
- `containsAll`, `containsAny`, and `in` filter `values` array must contain elements of number or string type (matching the type of the given property).
- `range` filter must have at lest one of `gt`, `gte`, `lt`, `lte` attributes.
  But `gt` is mutually exclusive to `gte`, while `lt` is mutually exclusive to `lte`.
- `gt`, `gte`, `lt`, `lte` in the `range` filter must be of number or string type (matching the type of the given property).
- The maximum length of the `value` of a leaf filter that is applied to a string property is 256.

</details>


---

### SubscriptionsDataDeletesDTO

List of time ranges in which all data points were deleted.

---

### SubscriptionsDataPartitionRequestDTO

**プロパティ**:

- `index` (integer, 必須)
  - Partition index to fetch data from. Between 0 and `partitions-1` inclusive.
- `cursor` (string, 任意)
  - Position in the partition stream to start fetching data from. Defaults to start of stream.

---

### SubscriptionsDataUpdateResponseDTO

**プロパティ**:

- `timeSeries` (unknown, 任意)
- `upserts` (unknown, 任意)
- `deletes` (unknown, 任意)

---

### SubscriptionsDataUpsertsDTO

List of data points inserted into the time series (possibly overwriting existing data).

---

### SubscriptionsDeleteRequest

---

### SubscriptionsListDataRequest

**プロパティ**:

- `externalId` (unknown, 任意)
- `partitions` (array, 必須)
  - Pairs of (partition, cursor) to fetch from.
- `limit` (integer, 任意)
  - Approximate number of results to return across all partitions. We will batch together groups of updates, where each group come from the same ingestion request. Thus, if a single group is large, it may exceed limit, otherwise we will return up to limit results. To check whether you have reached the end, do not rely on the count. Instead, check the `hasNext` field.
- `initializeCursors` (string, 任意)
  - If `partitions.cursor` is not set, the default behaviour is to start from the beginning of the stream.
`initializeCursors` can be used to override this behaviour.

The format is "N[timeunit]-ago", where timeunit is w,d,h,m (week, day, hour, minute).
For instance, "2d-ago" will give a stream of changes ingested up to 2 days ago.
You can also use "now" to jump straight to the end of the stream.

Note that initializeCursors is not exact; a deviation of some seconds can occur.
- `pollTimeoutSeconds` (integer, 任意)
  - The maximum time to wait for data to arrive, in seconds. As soon as data is available,
the request will return immediately with the data. If the timeout is reached while
waiting for data, the request will return an empty data response.
- `includeStatus` (boolean, 任意)
  - Show the [status code](<https://developer.cognite.com/dev/concepts/reference/status_codes>) for each data point in the response.
_Good_ (code = 0) status codes are always omitted.
- `ignoreBadDataPoints` (boolean, 任意)
  - Treat data points with a _Bad_ status code as if they do not exist.
Set to false to include all data points.
- `treatUncertainAsBad` (boolean, 任意)
  - Treat data points with _Uncertain_ status codes as _Bad_.
Set to false to include uncertain data points.

---

### SubscriptionsListDataResponse

Subscription data along with cursors.

**プロパティ**:

- `updates` (array, 必須)
  - List of updates from the subscription, sorted by point in time they were applied to the time series. Every update contains a time series along with a set of changes to that time series.
- `subscriptionChanges` (object, 任意)
  - If present, this object represents changes to the subscription definition. The
subscription will now start/stop listening to changes from the time series
listed here.

These changes can be triggered by explicit changes through the _Update subscriptions_
endpoint, or they can be caused by changes in time series, in that they start/stop
matching the filter for the subscription.

Time series are added to these lists when the change takes effect, which may be
later than the actual trigger.

The object is partitioned - it will only be present in the response for the relevant
partition, from which the time series was added/removed.
- `partitions` (array, 必須)
  - List of partition/cursor pairs to use for the next request.
- `hasNext` (boolean, 必須)
  - Whether there is more data available at the time of the query.
In rare cases, we may return true, even if there is no data available. If that is
the case, just continue to query with the updated cursors,
and it will eventually return false.

---

### SubscriptionsUpdateRequest

**プロパティ**:

- `items` (array, 必須)

---

### SubtreeSize

The number of descendants of the node, plus one (counting itself).

---

### SubworkflowTaskParameters

**プロパティ**:

- `subworkflow` (unknown, 任意)

---

### SuggestImprovementsRequestSchema

**プロパティ**:

- `advancedJoinExternalId` (unknown, 必須)
- `limit` (integer, 任意)

---

### SuggestImprovementsResponseSchema

---

### SuggestedRule

**プロパティ**:

- `matches` (array, 任意)
- `inputPattern` (string, 任意)
- `predictPattern` (string, 任意)
- `numMatches` (integer, 任意)
  - The number of matches belonging to the rule
- `avgScore` (number, 任意)
- `matchIndex` (array, 任意)

---

### SumAggregate

**プロパティ**:

- `sum` (object, 必須)
  - Calculates the sum from the values of the specified property.

---

### SumAggregateFunctionV3

Calculates the sum from the values of the specified property.

**プロパティ**:

- `sum` (object, 必須)

---

### SumAggregateIla

**プロパティ**:

- `sum` (object, 必須)
  - Calculates the sum from the values of the specified property.

<u>Example:</u>

```
{
  "my_player_scores_sum": {
    "sum": { "property": [ "football", "game_statistics", "points_scored" ] }
  }
}
```


---

### SumAggregateResult

**プロパティ**:

- `sum` (number, 必須)
  - The sum from the values of the specified in the request property.

---

### SumAggregateResultIla

**プロパティ**:

- `sum` (number, 必須)
  - The sum from the values of the specified in the request property.


---

### SvgData

SVG data extracted for a file.

**プロパティ**:

- `height` (number, 必須)
  - Height attribute of the SVG diagram
- `paths` (unknown, 必須)
- `pathStyles` (unknown, 必須)
- `viewBox` (string, 必須)
  - ViewBox attribute of the SVG diagram
- `width` (number, 必須)
  - Width attribute of the SVG diagram

---

### SymbolCreateDraft

**プロパティ**:

- `assetTypeId` (unknown, 必須)
  - The DMS identifier of the asset type that the symbol represents
- `externalId` (unknown, 必須)
  - The external ID of the symbol
- `libraryId` (unknown, 必須)
  - The external ID of a library the symbol belongs to

---

### SymbolDto

**プロパティ**:

- `assetTypeId` (unknown, 必須)
- `createdTime` (unknown, 必須)
- `externalId` (unknown, 必須)
  - The external ID of the symbol
- `lastUpdatedTime` (unknown, 必須)
- `libraryId` (unknown, 必須)
  - The external ID of a library the symbol belongs to

---

### SymbolUpdateDraft

**プロパティ**:

- `assetTypeId` (unknown, 任意)

---

### SymbolUpdateItem

**プロパティ**:

- `externalId` (unknown, 必須)
  - The external ID of the symbol
- `update` (unknown, 必須)
  - The properties to be updated

---

### SyncEdgeTableExpressionV3

The synchronization query to use when we listen for changes to edges.  The edges must also match the specified filter.

**プロパティ**:

- `limit` (unknown, 任意)
- `skipAlreadyDeleted` (boolean, 任意)
  - If set to 'false' the API will return instances that have been soft deleted before sync was initiated. Soft deletes that happen after the sync is initiated and a cursor generated, are always included in the result. Soft deleted instances are identified by having ```deletedTime``` set. This flag has no effect with `mode: noBackfill`.
- `mode` (unknown, 任意)
- `backfillSort` (unknown, 任意)
- `edges` (object, 必須)

---

### SyncMode

Specify whether to sync all instances in a single phase (`onePhase` - default) or two phases (`twoPhase`), or to skip the backfill phase `noBackfill`.
With `onePhase`, all matching instances are streamed until there are no more, after which new instances and soft-deleted instances can be fetched by following the cursor.
With `twoPhase`, syncing is split into a backfill phase, and a streaming phase. In the backfill phase, only instances that existed at the time of the first sync request are guaranteed to be returned. These can optionally be sorted by specifying `backfillSort` in order to take advantage of cursorable indexes in the backfill phase. When the number of instances returned is smaller than `limit` (including 0), the backfill phase is over. Following the cursor will now return instances created and deleted after the first sync call, but no longer ordered by `backfillSort`.
With `noBackfill`, existing instances are not returned, but following the cursor will return instances created and deleted after the first sync call.

---

### SyncNodeTableExpressionV3

The synchronization query to use when we listen for changes to nodes.  The nodes must also match the specified filter.

**プロパティ**:

- `limit` (unknown, 任意)
- `skipAlreadyDeleted` (boolean, 任意)
  - If set to 'false' the API will return instances that have been soft deleted before sync was initiated. Soft deletes that happen after the sync is initiated and a cursor generated, are always included in the result. Soft deleted instances are identified by having ```deletedTime``` set. This flag has no effect with `mode: noBackfill`.
- `mode` (unknown, 任意)
- `backfillSort` (unknown, 任意)
- `nodes` (object, 必須)

---

### SyncRecord

Sync record item.


**プロパティ**:

- `space` (unknown, 必須)
- `externalId` (unknown, 必須)
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)
- `properties` (object, 任意)
  - Spaces to containers to properties and their values for the requested containers.
**Note:** The attribute is omitted for deleted records in mutable streams.

- `status` (string, 必須)
  - The attribute represents the current record status.


---

### SyncRecordsRequest

---

### SyncRequest

**プロパティ**:

- `with` (object, 必須)
- `cursors` (object, 任意)
  - Cursors returned from the previous sync request. These cursors match the result set expressions you specified in the ```with``` clause for the sync.
- `select` (object, 必須)
- `parameters` (object, 任意)
  - Parameters to return
- `includeTyping` (unknown, 任意)
- `allowExpiredCursorsAndAcceptMissedDeletes` (boolean, 任意)
  - Sync cursors will expire after 3 days. This is because soft-deleted instances are cleaned up after this grace period, so a client using a cursor older than that risks missing deletes. If this option is set to `true`, the API will allow the use of expired cursors.
- `debug` (unknown, 任意)

---

### SyncSelectV3

Specify the container or view to return properties for. Also specify the properties for those containers/views to return. Up to 10 views can be specified.

**プロパティ**:

- `sources` (unknown, 任意)

---

### SyncTableExpressionV3

---

### SynonymEntry

List of sources and targets words that all mean the same.

**プロパティ**:

- `sources` (array, 任意)
  - List of tokens that have the same meaning when appearing in a source entity.
- `targets` (array, 任意)
  - List of tokens that have the same meaning when appearing in a target entity.

---

### SyntheticDataError

**プロパティ**:

- `timestamp` (unknown, 必須)
- `error` (string, 必須)
  - Human readable string with description of what went wrong

---

### SyntheticDataPoint

---

### SyntheticDataValue

**プロパティ**:

- `timestamp` (unknown, 必須)
- `value` (number, 必須)
  - the data value

---

### SyntheticMultiQuery

**プロパティ**:

- `items` (array, 必須)

---

### SyntheticQuery

Synthetic query description

**プロパティ**:

- `expression` (string, 必須)
  - query definition. For limits, see the [guide to synthetic time series](https://developer.cognite.com/dev/concepts/resource_types/synthetic_timeseries.html#limits).
- `start` (unknown, 任意)
- `end` (unknown, 任意)
- `limit` (integer, 任意)
  - Return up to this number of datapoints
- `timeZone` (string, 任意)
  - For aggregates of granularity 'hour' and longer, which [time zone](<https://developer.cognite.com/dev/concepts/aggregation/calendar>) should we align to. Align to the start of the hour, start of the day or start of the month. For time zones of type Region/Location, the aggregate duration can vary, typically due to daylight saving time. For time zones of type UTC+/-HH:MM, use increments of 15 minutes.


---

### SyntheticQueryResponse

**プロパティ**:

- `isString` (boolean, 任意)
  - whether the returned data points are of string type or floating point type. Currently it will always be false.
- `datapoints` (array, 必須)
  - list of data points

---

### SyntheticQueryResponses

**プロパティ**:

- `items` (array, 必須)

---

### Table

Foreign tables

**プロパティ**:

- `columns` (unknown, 必須)
- `options` (unknown, 任意)
- `type` (unknown, 必須)
- `tablename` (unknown, 必須)

---

### TableCreated

Foreign tables

**プロパティ**:

- `columns` (unknown, 必須)
- `options` (unknown, 任意)
- `createdTime` (unknown, 必須)
- `type` (string, 必須)
- `tablename` (unknown, 必須)

---

### TableExpressionChainToDefinition

Default: `"destination"`. Applicable when `from` is an edge result expression. Control which side of the edges in `from` to chain to. The behavior depends on the `direction` setting in the `from` result expression:
  - If `from` follows edges outwards, `direction="outwards"` (default), then `"source"` selects `startNode` and `"destination"` selects `endNode`.
  - If `from` follows edges inwards, `direction="inwards"`, then `"source"` selects `endNode` and `"destination"` selects `startNode`.

---

### TableExpressionContainsAllFilterV3

**プロパティ**:

- `containsAll` (object, 必須)
  - Matches items where the property contains all the given values. Only apply this filter to multivalued properties.

---

### TableExpressionContainsAnyFilterV3

**プロパティ**:

- `containsAny` (object, 必須)
  - Matches items where the property contains one or more of the given values. Only apply this filter to multivalued properties.

---

### TableExpressionDataModelsBoolFilter

Build a new query by combining other queries, using boolean operators. We support the `and`, `or`, and
`not` boolean operators.


---

### TableExpressionEqualsFilterV3

**プロパティ**:

- `equals` (object, 必須)
  - Matches items that contain the exact value in the provided property.

---

### TableExpressionFilterDefinition

A filter Domain Specific Language (DSL) used to create advanced filter queries.

---

### TableExpressionFilterValue

---

### TableExpressionFilterValueList

---

### TableExpressionFilterValueRange

---

### TableExpressionInFilterV3

**プロパティ**:

- `in` (object, 必須)
  - Matches items where the property **exactly** matches one of the given values.

---

### TableExpressionLeafFilter

Leaf filter

---

### TableExpressionOverlapsFilterV3

**プロパティ**:

- `overlaps` (object, 必須)
  - Matches items where the range made up of the two properties overlap with the provided range.

---

### TableExpressionPrefixFilterV3

**プロパティ**:

- `prefix` (object, 必須)
  - Matches items that have the prefix in the identified property.

---

### TableExpressionQueryLimit

Limits the number of instances in the result set generated by this result expression.

---

### TableExpressionRangeFilterV3

**プロパティ**:

- `range` (object, 必須)
  - Matches items that contain terms within the provided range.

The range must include both an upper and a lower bound. It is not permitted to specify both inclusive
and exclusive bounds together.  E.g. `gte` and `gt`.

  `gte`: Greater than or equal to.
  `gt`: Greater than.
  `lte`: Less than or equal to.
  `lt`: Less than.


---

### TableExpressionSyncLimit

Limits the number of instances in the result set generated by this table expression. We recommend to to not use a value lower than 100, as this will result in excessive API calls to fetch the data.

---

### TableOptions

Table options

---

### TableType

The type of table (usually built_in) but could be any of raw_rows, nodes or edges when custom.

---

### Tablename

---

### TablenameWrapper

**プロパティ**:

- `tablename` (unknown, 必須)

---

### TargetExternalId

The external id for the to-object of the match.

---

### TargetId

The id for the target-object of the match.

---

### TargetUnit

Describes a target unit for a property.

**プロパティ**:

- `property` (string, 必須)
- `unit` (unknown, 必須)
  - The external id of the target unit or unit system to convert to.

---

### TargetUnits

Properties to convert to another unit. The API can only convert to another unit, if a unit has been defined as part of the type on the underlying container being queried.

---

### Targets

List of custom target object to match to, for example, assets. String key -> value. Only string values are considered in the matching. Both `id` and `externalId` fields are optional, only mandatory if the item is to be referenced in `trueMatches`.

---

### TaskDefinition

**プロパティ**:

- `externalId` (unknown, 必須)
- `type` (unknown, 必須)
- `name` (string, 任意)
  - Readable name meant for use in UIs
- `description` (string, 任意)
  - Description of the intention of the task
- `parameters` (unknown, 必須)
- `retries` (number, 任意)
  - Number of times to retry the task if it fails. If set to 0, the task will not be retried. The behavior for timeouts and retries is defined by the `onFailure` parameter, refer to it for more information.
- `timeout` (number, 任意)
  - Timeout in seconds. After this time, the task will be marked as `TIMED_OUT`. By default, the task won't be retried upon timeout. Use the `onFailure` parameter to change this behavior.
- `onFailure` (string, 任意)
  - Defines the policy to handle failures and timeouts.
- `skipTask`: For both failures and timeouts, it will retry until the retries are exhausted. After that, the Task is marked as COMPLETED_WITH_ERRORS and the subsequent tasks are executed.
- `abortWorkflow`:
  - In case of failures, retries will be performed until exhausted, after which the task is marked as FAILED and the Workflow is marked the same.
  - In the event of a timeout, no retries are undertaken; the task is marked as TIMED_OUT and the Workflow is marked as FAILED.

- `dependsOn` (array, 任意)
  - The tasks that must be completed before this task can be executed.

---

### TaskDepends

**プロパティ**:

- `externalId` (unknown, 任意)

---

### TaskExecution

**プロパティ**:

- `id` (unknown, 必須)
- `externalId` (unknown, 必須)
- `parentTaskExternalId` (unknown, 任意)
  - The external ID of the parent task when this task is part of a dynamic task or subworkflow.
- `status` (string, 必須)
- `taskType` (string, 必須)
- `startTime` (unknown, 任意)
- `endTime` (unknown, 任意)
- `input` (unknown, 必須)
  - The input to the task with the references present in the definition resolved.
- `output` (unknown, 必須)
- `reasonForIncompletion` (unknown, 任意)

---

### TaskExecutionId

UUIDv4 identifier for the execution of a workflow task.

---

### TaskExternalId

Identifier for the task. Must be unique within the version. No trailing or leading whitespace and no null characters allowed. Task external IDs cannot be exactly 'workflow' or start with '__' (double underscore) as these are reserved.

---

### TaskType

---

### TaskUpdateBase

**プロパティ**:

- `taskId` (string, 必須)
  - The id of the task to update

---

### TextDetection

Detect text in images.

---

### TextDetectionParameters

Parameters for text detection

**プロパティ**:

- `threshold` (unknown, 任意)

---

### TextProperty

Text type

**プロパティ**:

- `type` (string, 必須)
- `list` (boolean, 任意)
  - Specifies that the data type is a list of values. The ordering of values is preserved.

- `maxListSize` (integer, 任意)
  - Specifies the maximum number of values in the list
- `maxTextSize` (integer, 任意)
  - Specifies the maximum size in bytes of the text property, when encoded with utf-8
- `collation` (string, 任意)
  - Collation - the set of language specific rules - used when sorting text fields

---

### ThresholdParameter

The confidence threshold returns predictions as positive if their confidence score is the selected value or higher.
A higher confidence threshold increases precision but lowers recall, and vice versa.


---

### ThroughReference

Traverse from another table expression using a direct relation. Only applicable when `from` is specified. The view or container property to use when we traverse direct relations. Has to reference a direct relation property.

**プロパティ**:

- `source` (unknown, 必須)
- `identifier` (unknown, 必須)

---

### TimeHistogramAggregateIla

**プロパティ**:

- `timeHistogram` (object, 必須)
  - A time histogram aggregator function. This function will generate a histogram from the values of the specified
property. It uses the specified calendar or fixed interval as defined in the request arguments.
The request attribute calendarInterval or fixedInterval must be specified but not both.

<u>Example:</u>

```
{
  "my_daily_histogram": {
    "timeHistogram": {
      "calendarInterval": "1d",
      "property": [ "football", "game_statistics", "game_date" ],
      "aggregates": {
        "my_group_by_player_name": {
          "uniqueValues": {
            "property": [ "football", "game_statistics", "player_name" ],
            "aggregates": {
              "my_player_daily_scores_sum": {
                "sum": {
                  "property": [ "football", "game_statistics", "points_scored" ]
                }
              }
            }
          }
        }
      }
    }
  }
}
```


---

### TimeHistogramAggregatePropertyIla

Property a client wants to aggregate.
To represent fully qualified properties, Records use arrays of strings.

The aggregate supports container level properties, as well as `["createdTime"]`
and `["lastUpdatedTime"]` top level properties.

A container level property is defined by 3 segments:
  - the first segment is the space that the container belongs to,
  - the second segment is the container external id,
  - the third segment is the property id inside the container.

<u>Example:</u>

You have the schema
```
{
  "mySpace": {
    "myContainer": {
      "myProperty": {...}
    }
  }
}
```

Use `["mySpace", "myContainer", "myProperty"]` array to aggregate the values for `myProperty` property.


---

### TimeHistogramAggregateResultIla

**プロパティ**:

- `timeHistogramBuckets` (array, 任意)
  - The histogram from the values of the specified in the request property.


---

### TimeSeriesAdvancedAggregateDTO

---

### TimeSeriesAggregateDTO

**プロパティ**:

- `filter` (unknown, 任意)
- `advancedFilter` (unknown, 任意)

---

### TimeSeriesAggregateFilter

A filter DSL (Domain Specific Language) to define aggregate filters.


---

### TimeSeriesAggregatePath

**プロパティ**:

- `path` (array, 必須)
  - The scope within which properties should be aggregated. The only value that is currently allowed is `["metadata"]`, which will aggregate metadata keys.

---

### TimeSeriesAggregateProperties

**プロパティ**:

- `properties` (array, 必須)
  - The properties to which the aggregation should be applied. While this parameter is a list, it
currently only accepts one element. Each element is an object with a single field called `property`,
whose value is another list (in order to accommodate nested properties). Thus, a top-level property
`name` must be specified as `[{"property": ["name"]}]`, and a metadata property `tag` must be
specified as `[{"property": ["metadata", "tag"]}]`.

The supported top-level properties are:
- `assetId`
- `assetRootId`
- `createdTime`
- `dataSetId`
- `description`
- `externalId`
- `id`
- `isStep`
- `isString`
- `lastUpdatedTime`
- `name`
- `securityCategories`
- `unit`


---

### TimeSeriesBoolAggregateFilter

A query that matches items matching boolean combinations of other queries.
It's built by nesting one or more Boolean clauses, each of which is one of `and`, `or`, and `not`.
Each such clause contains one or more child clauses (though `not` can only have one).
Each child clause can be either another Boolean clause or a leaf filter.


---

### TimeSeriesBoolFilter

A query that matches items matching boolean combinations of other queries.
It is built by nesting one or more Boolean clauses, each of which is one of `and`, `or`, and `not`.
Each such clause contains one or more child clauses (though `not` can only have one).
Each child clause can be either another Boolean clause or a leaf filter.


---

### TimeSeriesCardinalityPropertiesAggregate

---

### TimeSeriesCardinalityPropertiesAggregateResponse

**プロパティ**:

- `items` (array, 必須)

---

### TimeSeriesCardinalityValuesAggregate

---

### TimeSeriesCardinalityValuesAggregateResponse

**プロパティ**:

- `items` (array, 必須)

---

### TimeSeriesContainsAllFilter

**プロパティ**:

- `containsAll` (object, 必須)
  - Matches items where the property contains all the given values.
This filter can only be applied to multivalued properties.


---

### TimeSeriesContainsAnyFilter

**プロパティ**:

- `containsAny` (object, 必須)
  - Matches items where the property contains one or more of the given values.
This filter can only be applied to multivalued properties.


---

### TimeSeriesCountAggregate

**プロパティ**:

- `aggregate` (string, 任意)
  - The `count` aggregation gets the number of time series that match the filter(s). This is the
default aggregation, which will also be applied if `aggregate` is not specified.


---

### TimeSeriesCountAggregateResponse

**プロパティ**:

- `items` (array, 必須)

---

### TimeSeriesCreateRequest

**プロパティ**:

- `items` (array, 必須)

---

### TimeSeriesCursorResponse

---

### TimeSeriesEpochTimestamp

The number of milliseconds since 00:00:00 Thursday, 1 January 1970, Coordinated Universal Time (UTC), minus leap seconds. Can be negative to define dates before 1970, up to 1900.

---

### TimeSeriesEqualsFilter

**プロパティ**:

- `equals` (object, 必須)
  - Matches items where the given property is **exactly** equal to the given value.


---

### TimeSeriesExistsFilter

**プロパティ**:

- `exists` (object, 必須)
  - Matches items that have a value for the specified property.


---

### TimeSeriesFilterLanguage

A filter DSL (Domain Specific Language) to define advanced filter queries.

At the top level, an `advancedFilter` expression is either a single Boolean filter or a
single leaf filter. Boolean filters contain other Boolean filters and/or leaf filters. The
total number of filters may be at most 100, and the depth (the greatest number of times
filters have been nested inside each other) may be at most 10. The `search` leaf filter may
at most be used twice within a single `advancedFilter`, but all other filters can be used
as many times as you like as long as the other limits are respected.


---

### TimeSeriesFilterProperty

The property on which you want to filter. May be either:
- A single-element list that contains the name of one of the predefined top-level
  properties, in which case the filter will be applied to that top-level property.
- A single-element list that contains `'metadata'`, in which case the filter will be applied
  to _all_ metadata properties.
- A two-element list where the first element is `'metadata'` and the second one is any
  string, in which case the filter will be applied to metadata properties with that name.
- A two-element list where the first element is `'instanceId'` and the second element is
  either `'space'` or `'externalId'`.


---

### TimeSeriesInAggregateFilter

**プロパティ**:

- `in` (object, 必須)
  - Matches intermediate aggregation results where the property key or the property value is _exactly_
equal to one of the given values. When the `aggregate` operation is `uniqueProperties` or
`cardinalityProperties`, property _keys_ will be targeted; when it is `uniqueValues` or
`cardinalityValues`, property _values_ will be targeted.


---

### TimeSeriesInAggregateValue

The property key or property value (depending on the `aggregate` operation) on which you want to
filter the intermediate aggregate results.


---

### TimeSeriesInAggregateValues

The property keys or property values (depending on the `aggregate` operation) on which you want to
filter the intermediate aggregate results.


---

### TimeSeriesInFilter

**プロパティ**:

- `in` (object, 必須)
  - Matches items where the given property is **exactly** equal to one of the given values.
This filter can only be applied to single-valued properties.


---

### TimeSeriesLeafAggregateFilter

Leaf filter.


---

### TimeSeriesLeafFilter

Leaf filter.


---

### TimeSeriesListDTO

Filter request for time series. Filters exact field matching or timestamp ranges inclusive min and max.

---

### TimeSeriesLookupById

---

### TimeSeriesLookupByIdWithoutInstanceId

---

### TimeSeriesMetadata

Custom, application specific metadata. String key -> String value. Maximum length of key is 128 bytes, up to 256 key-value pairs, of total size of at most 10000 bytes across all keys and values.

---

### TimeSeriesPatch

Changes will be applied to time series.

**プロパティ**:

- `update` (object, 必須)

---

### TimeSeriesPatchDM

Changes will be applied to time series. Note that these changes will not be visible in data modeling.

**プロパティ**:

- `update` (object, 必須)

---

### TimeSeriesPrefixAggregateFilter

**プロパティ**:

- `prefix` (object, 必須)
  - Matches intermediate aggregation results where the property key or the property value begins with
the given text. When the `aggregate` operation is `uniqueProperties` or `cardinalityProperties`,
property _keys_ will be targeted; when it is `uniqueValues` or `cardinalityValues`, property
_values_ will be targeted.


---

### TimeSeriesPrefixAggregateValue

A string value that represents either a property key or a property value, depending on the
`aggregate` operation.


---

### TimeSeriesPrefixFilter

**プロパティ**:

- `prefix` (object, 必須)
  - Matches items where the provided property begins with the given text.


---

### TimeSeriesRangeAggregateFilter

**プロパティ**:

- `range` (object, 必須)
  - Matches intermediate aggregation results where the property key or the property value is within the
provided range. When the `aggregate` operation is `uniqueProperties` or `cardinalityProperties`,
property _keys_ will be targeted; when it is `uniqueValues` or `cardinalityValues`, property 
_values_ will be targeted.

At least one of the following bounds must be specified:
- `gte`: Greater than or equal to.
- `gt`: Greater than.
- `lte`: Less than or equal to.
- `lt`: Less than.

It is not valid to specify both inclusive and exclusive bounds "on the same side" together:
at most one of `lte` and `lt` may be specified, and at most one of `gte` and `gt`.

In the case of `aggregate` being `uniqueProperties` or `cardinalityProperties`,
`gte`/`gt`/`lte`/`lt` must be set to a string. In the case of `aggregate` being `uniqueValues` or
`cardinalityValues`, this filter may only be applied to string properties or number properties,
with `gte`/`gt`/`lte`/`lt` containing values of the corresponding type.


---

### TimeSeriesRangeAggregateValue

An upper or lower bound for a property key or property value (depending on which `aggregate` was
specified at the top level of the aggregation request).


---

### TimeSeriesRangeFilter

**プロパティ**:

- `range` (object, 必須)
  - Matches items that contain terms within the provided range.

One upper bound and/or one lower bound must be specified. It's not allowed to specify both inclusive and exclusive
bounds "on the same side" together (at most one of `lte` and `lt` may be specified, and at most one of `gte` and `gt`).
- `gte`: Greater than or equal to.
- `gt`: Greater than.
- `lte`: Less than or equal to.
- `lt`: Less than.

May only be applied to string properties and number properties.


---

### TimeSeriesRangeValue

An upper or lower bound in a `range` filter.

---

### TimeSeriesResponse

---

### TimeSeriesSearchDTO

**プロパティ**:

- `filter` (unknown, 任意)
- `search` (unknown, 任意)
- `limit` (integer, 任意)
  - Return up to this many results.

---

### TimeSeriesSearchFilter

**プロパティ**:

- `search` (object, 必須)
  - Matches items where the provided string property contains the given value according to a fuzzy search.
The value may exist anywhere in the string, even as a part of a word or with some grammatical
variations (e.g., searching for "run" will also find "ran").

The supported properties are `name` and `description`. It's also possible to set `property`
to `["query"]`, in which case both `name` and `description` will be searched.

When the `search` filter is being used, and it's not nested inside a `not` filter (directly or indirectly),
and no `sort` is specified, the results will be ranked according to how good the match is.
When the `query` 'property' is specified, matches in `name` will rank higher than matches in `description`.
The `search` filter may be used at most twice within the same `advancedFilter` expression.


---

### TimeSeriesSort

**プロパティ**:

- `sort` (array, 任意)
  - Sort by array of selected properties.


---

### TimeSeriesSortItem

**プロパティ**:

- `property` (array, 必須)
  - Property to sort on.
Sorting can be done on the following properties:
  | Property                          |
  |-----------------------------------|
  | `['assetId']`                     |
  | `['createdTime']`                 |
  | `['dataSetId']`                   |
  | `['description']`                 |
  | `['externalId']`                  |
  | `['lastUpdatedTime']`             |
  | `['metadata', '<someCustomKey>']` |
  | `['name']`                        |
  | `['_score_']`                     |
  | `['instanceId', 'space']`         |
  | `['instanceId', 'externalId']`    |
- `order` (string, 任意)
  - The `order` attribute is optional and defaults to `desc` for `_score_` and `asc` for all
other properties.
- `nulls` (string, 任意)
  - The `nulls` attribute is optional and defaults to `auto`. `auto` is translated to `last`
for the `asc` order and to `first` for the `desc` order.

---

### TimeSeriesStateSet

Reference to the state set used in time series with type `state`.

---

### TimeSeriesStringValue

A value that you wish to find in the provided property.


---

### TimeSeriesType

The type of the time series. Currently, time series can either be of type `numeric` or `string`. ** The available types of time series may be extended in the future. **


---

### TimeSeriesUniquePropertiesAggregate

---

### TimeSeriesUniquePropertiesAggregateResponse

**プロパティ**:

- `items` (array, 必須)

---

### TimeSeriesUniqueValuesAggregate

---

### TimeSeriesUniqueValuesAggregateResponse

**プロパティ**:

- `items` (array, 必須)

---

### TimeSeriesUpdate

---

### TimeSeriesUpdateByExternalId

---

### TimeSeriesUpdateById

---

### TimeSeriesUpdateByInstanceId

---

### TimeSeriesUpdateRequest

**プロパティ**:

- `items` (array, 必須)

---

### TimeSeriesValue

A value that you wish to find in the provided property.


---

### TimeSeriesValues

One or more values that you wish to find in the provided properties.


---

### TimestampOrStringEnd

Get datapoints up to, but excluding, this point in time. Same format as for start. Note that when using aggregates, the end will be rounded up such that the last aggregate represents a full aggregation interval containing the original end, where the interval is the granularity unit times the granularity multiplier. For granularity 2d, the aggregation interval is 2 days, if end was originally 3 days after the start, it will be rounded to 4 days after the start.

---

### TimestampOrStringStart

Get datapoints starting from, and including, this time. The format is N[timeunit]-ago where
timeunit is w,d,h,m,s. Example: '2d-ago' gets datapoints that are up to 2 days
old. You can also specify time in milliseconds since epoch. Note that for aggregates, the start time is rounded down to a whole granularity unit (in UTC timezone). Daily granularities (d)
are rounded to 0:00 AM; hourly granularities (h) to the start of the hour, etc.

---

### TokenInspectionResponse

**プロパティ**:

- `subject` (string, 必須)
  - Subject (sub claim) of JWT
- `projects` (unknown, 必須)
- `capabilities` (unknown, 必須)

---

### TransformBlockedInfo

Provides reason and time if the transformation is blocked.

**プロパティ**:

- `reason` (string, 必須)
  - Indicates the reason if the transformation is blocked.
- `createdTime` (integer, 必須)
  - Indicates the blocked time if the transformation is blocked: The number of milliseconds since 00:00:00 Thursday, 1 January 1970, Coordinated Universal Time (UTC), minus leap seconds.

---

### TransformationAdvancedList

**プロパティ**:

- `filter` (unknown, 必須)
- `limit` (integer, 任意)
- `cursor` (string, 任意)

---

### TransformationCogniteExternalId

**プロパティ**:

- `externalId` (string, 必須)
  - The external ID provided by the client. Must be unique for the resource type.

---

### TransformationCogniteInternalId

**プロパティ**:

- `id` (integer, 必須)
  - A server-generated ID for the object.

---

### TransformationCreate

**プロパティ**:

- `name` (string, 必須)
- `query` (string, 任意)
- `destination` (unknown, 任意)
  - Destination data type.
- `conflictMode` (unknown, 任意)
- `isPublic` (boolean, 任意)
- `sourceOidcCredentials` (unknown, 任意)
- `destinationOidcCredentials` (unknown, 任意)
- `sourceNonce` (unknown, 任意)
- `destinationNonce` (unknown, 任意)
- `externalId` (string, 必須)
  - The external ID provided by the client. Must be unique for the resource type.
- `ignoreNullFields` (boolean, 必須)
  - How NULL values are handled on updates: ignore or setNull.
- `dataSetId` (integer, 任意)
- `tags` (array, 任意)
  - List of tags for the Transformation.

---

### TransformationFilter

**プロパティ**:

- `isPublic` (boolean, 任意)
  - Whether public transformations should be included in the results. The default is true.
- `nameRegex` (string, 任意)
  - Regex expression to match the transformation name.
- `queryRegex` (string, 任意)
  - Regex expression to match the transformation query.
- `destinationType` (string, 任意)
  - Transformation destination resource name to filter by.
- `conflictMode` (unknown, 任意)
- `hasBlockedError` (boolean, 任意)
  - Whether only the blocked transformations should be included in the results.
- `cdfProjectName` (string, 任意)
  - Project name to filter by configured source and destination project.
- `createdTime` (unknown, 任意)
- `lastUpdatedTime` (unknown, 任意)
- `dataSetIds` (array, 任意)
  - Return only transformations in the specified data sets.
- `tags` (unknown, 任意)
  - Return the transformations depending on the tags filter provided.

---

### TransformationJobStatus

Status of the job at the request time.

---

### TransformationRead

**プロパティ**:

- `id` (integer, 必須)
  - Transformation ID.
- `name` (string, 必須)
  - Transformation name.
- `query` (string, 必須)
  - Transformation query.
- `destination` (unknown, 必須)
  - Destination data type.
- `conflictMode` (unknown, 必須)
- `isPublic` (boolean, 必須)
  - Indicates if the transformation is visible to all in project or only to the owner.
- `blocked` (unknown, 任意)
- `createdTime` (integer, 必須)
  - Time when the transformation was created: The number of milliseconds since 00:00:00 Thursday, 1 January 1970, Coordinated Universal Time (UTC), minus leap seconds.
- `lastUpdatedTime` (integer, 必須)
  - Time when the transformation was last updated: The number of milliseconds since 00:00:00 Thursday, 1 January 1970, Coordinated Universal Time (UTC), minus leap seconds.
- `owner` (string, 必須)
  - Owner of the transformation: requester's identity.
- `ownerIsCurrentUser` (boolean, 必須)
  - Indicates if the transformation belongs to the current user.
- `hasSourceOidcCredentials` (boolean, 必須)
  - Indicates if the transformation is configured with a source oidc credentials set.
- `hasDestinationOidcCredentials` (boolean, 必須)
  - Indicates if the transformation is configured with a destination oidc credentials set.
- `sourceSession` (unknown, 任意)
- `destinationSession` (unknown, 任意)
- `lastFinishedJob` (unknown, 任意)
- `runningJob` (unknown, 任意)
- `schedule` (unknown, 任意)
- `externalId` (string, 必須)
  - The external ID provided by the client. Must be unique for the resource type.
- `ignoreNullFields` (boolean, 必須)
  - How NULL values are handled on updates: ignore or setNull.
- `dataSetId` (integer, 任意)
  - Gets the transformation dataset id which can be used for access control.
- `tags` (array, 任意)
  - List of tags for the Transformation.

---

### TransformationRunWithExternalId

**プロパティ**:

- `externalId` (string, 必須)
- `nonce` (unknown, 任意)

---

### TransformationRunWithId

**プロパティ**:

- `id` (integer, 必須)
- `nonce` (unknown, 任意)

---

### TransformationTaskOutput

**プロパティ**:

- `jobId` (integer, 任意)
  - Job ID of the Transformation called.

---

### TransformationTaskParameters

**プロパティ**:

- `transformation` (object, 任意)
  - Parameters for the CDF Transformation task type.

---

### TransformationUpdate

**プロパティ**:

- `name` (unknown, 任意)
  - Set a new value for the transformation name.
- `externalId` (unknown, 任意)
  - Set a new value for the transformation External ID.
- `destination` (unknown, 任意)
- `conflictMode` (unknown, 任意)
- `query` (unknown, 任意)
  - Set a new value for the transformation query.
- `sourceOidcCredentials` (unknown, 任意)
  - Set a new value for the transformation source OIDC credentials, or remove it.
- `destinationOidcCredentials` (unknown, 任意)
  - Set a new value for the transformation destination OIDC credentials, or remove it.
- `sourceNonce` (unknown, 任意)
  - Bind to a new source CDF session, or remove it
- `destinationNonce` (unknown, 任意)
  - Bind to a new destination session, or remove it
- `isPublic` (unknown, 任意)
  - Set a new value for the transformation isPublic flag to change who can see the transformation.
- `ignoreNullFields` (unknown, 任意)
  - Set a new value for the transformation ignoreNullFields flag to change to change how nulls are handled on updates.
- `dataSetId` (unknown, 任意)
  - Set a new value for the transformation dataset id which is used for access control.
- `tags` (unknown, 任意)
  - List of tags for the Transformation.

---

### TreeIndex

The index of the node in the 3D model hierarchy, starting from 0. The tree is traversed in a depth-first order.

---

### TriggerExternalId

Identifier for a trigger. Must be unique for the project. No trailing or leading whitespace and no null characters allowed.

---

### TriggerRule

---

### TriggerView

**プロパティ**:

- `externalId` (unknown, 任意)
- `triggerRule` (unknown, 任意)
- `input` (unknown, 任意)
- `metadata` (unknown, 任意)
- `workflowExternalId` (unknown, 任意)
- `workflowVersion` (unknown, 任意)
- `createdTime` (unknown, 任意)
- `lastUpdatedTime` (unknown, 任意)
- `isPaused` (boolean, 任意)

---

### TrueMatches

A list of confirmed source/target matches, which will be used to train the model. If omitted, an unsupervised
model is trained.

---

### TypeInformation

Type information for the returned properties (if requested)

---

### TypeInformationOuter

Spaces for the requested view and containers

---

### TypePropertyDefinition

Describes the type and configuration of a property included in the result.

---

### TypingViewOrContainer

View or container holding properties

---

### UnindexedThroughNotice

**プロパティ**:

- `code` (string, 必須)
- `category` (string, 必須)
- `level` (string, 必須)
- `hint` (string, 必須)
- `grade` (string, 必須)
- `resultExpression` (string, 必須)
  - Identifier for the result set expression that the notice applies to.
- `property` (array, 必須)
  - Reference to the property that the notice applies to.

---

### UniqueValues

---

### UniqueValuesAggregate

**プロパティ**:

- `uniqueValues` (object, 必須)
  - Request unique value buckets aggregate on of the specified property.
Each bucket is defined by `values` array and has the number of the `values` occurrences.

---

### UniqueValuesAggregateIla

**プロパティ**:

- `uniqueValues` (object, 必須)
  - Request unique value buckets aggregate on the specified property.
Each bucket is defined by `value` attribute and has the records `count` in the bucket.

<u>Example:</u>

```
{
  "my_group_by_player_name": {
    "uniqueValues": {
      "property": [ "football", "game_statistics", "player_name" ],
      "aggregates": {
        "my_player_scores_sum": {
          "sum": { "property": [ "football", "game_statistics", "points_scored" ] }
        },
        "my_player_scores_maximum": {
          "max": { "property": [ "football", "game_statistics", "points_scored" ] }
        }
      }
    }
  }
}
```


---

### UniqueValuesAggregatePropertyIla

Property a client wants to aggregate.
To represent fully qualified properties, Records use arrays of strings.

The aggregate supports container level properties, as well as `["space"]`
top level property.

A container level property is defined by 3 segments:
  - the first segment is the space that the container belongs to,
  - the second segment is the container external id,
  - the third segment is the property id inside the container.

<u>Example:</u>

You have the schema
```
{
  "mySpace": {
    "myContainer": {
      "myProperty": {...}
    }
  }
}
```

Use `["mySpace", "myContainer", "myProperty"]` array to aggregate the values for `myProperty` property.


---

### UniqueValuesAggregateResult

**プロパティ**:

- `buckets` (array, 任意)
  - Unique value buckets on of the specified in the request property.

---

### UniqueValuesAggregateResultIla

**プロパティ**:

- `uniqueValueBuckets` (array, 任意)
  - Unique value buckets on of the specified in the request property.


---

### UniquenessConstraintCreateDefinition

**プロパティ**:

- `constraintType` (string, 任意)
- `properties` (array, 必須)
  - List of properties included in the constraint. The order you list the properties in is significant.
- `bySpace` (boolean, 任意)
  - Whether to make the constraint space-specific

---

### UniquenessConstraintDefinition

---

### Unit

Unit: This represents a standard measure of a given quantity.

Examples:

- Temperature: Degrees Celsius, Degrees Fahrenheit

- Pressure: Bar, Psi

**プロパティ**:

- `externalId` (string, 必須)
  - Unique identifier of the unit. Usually in a form of unit_quantity_name:unit_name
- `name` (string, 必須)
  - A compact name of the unit.
- `longName` (string, 必須)
  - A more verbose name of the unit.
- `symbol` (string, 必須)
  - The symbol for the unit.
- `aliasNames` (array, 必須)
  - A list of alternative aliases (names) for the unit.
- `quantity` (string, 必須)
  - Specifies the physical quantity the unit measures.
- `conversion` (unknown, 必須)
  - An object containing multiplier and offset values for converting between units
- `source` (string, 任意)
  - Source of the unit specification
- `sourceReference` (string, 任意)
  - Reference to the source of the unit specification

---

### UnitConversion

**プロパティ**:

- `multiplier` (number, 必須)
- `offset` (number, 必須)

---

### UnitIntervalNumber

A number in the range [0, 1].

---

### UnitReference

Target unit reference.

**プロパティ**:

- `externalId` (unknown, 必須)

---

### UnitSystem

Unit System: A set of default units designated per quantity.

An example being:

- SI: Temperature in Kelvin, Pressure in Pa

- SI (Engineering): Temperature in degrees Celsius, Pressure in bar

- Imperial: Temperature in degrees Fahrenheit, Pressure in psi

**プロパティ**:

- `name` (string, 必須)
  - Name of the unit system
- `quantities` (array, 必須)
  - List of quantities and their default units

---

### UnitSystemQuantity

**プロパティ**:

- `name` (string, 必須)
  - Name of the quantity
- `unitExternalId` (string, 必須)
  - Default unit for the quantity

---

### UnitSystemReference

Target system to convert data to. Can be used instead of targetUnit to identify the unit to convert to.


**プロパティ**:

- `unitSystemName` (string, 必須)

---

### UnprocessableRecords

**プロパティ**:

- `error` (object, 必須)
  - Details about the unprocessable records.


---

### UpdateAdvancedJoinMatchersRequestSchema

**プロパティ**:

- `items` (array, 必須)

---

### UpdateArray_String

**プロパティ**:

- `add` (array, 任意)
  - List of tags to add to the Transformation.
- `remove` (array, 任意)
  - List of tags to remove from the Transformation.

---

### UpdateDestination

**プロパティ**:

- `credentials` (unknown, 任意)
  - Set a new session, or remove the value.
- `targetDataSetId` (unknown, 任意)
  - Set a new value for the dataset ID, or remove the value.

---

### UpdateEntitiesRequest

List of diagram entities to update

**プロパティ**:

- `items` (array, 必須)

---

### UpdateEventHubSource

**プロパティ**:

- `host` (unknown, 任意)
- `eventHubName` (object, 任意)
  - Set a new value for the event hub name.
- `keyName` (object, 任意)
  - Set a new value for the key name. This parameter is deprecated, use `username` under basic authentication instead.
- `keyValue` (object, 任意)
  - Set a new value for the key value. This parameter is deprecated, use `password` under basic authentication instead.
- `authentication` (object, 任意)
  - Update authentication details for the eventhub source.
- `consumerGroup` (unknown, 任意)
  - Set a new value for the consumer group, or remove the value.

---

### UpdateEventHubSourceItem

**プロパティ**:

- `type` (string, 必須)
  - Source type.
- `externalId` (unknown, 必須)
- `update` (unknown, 必須)

---

### UpdateFormat

Set a new format.

**プロパティ**:

- `set` (object, 必須)
  - The format of the messages from the source. This is used to convert messages coming from the source system to a format that can be inserted into CDF.

---

### UpdateGeometriesRequest

List of geometries to update

**プロパティ**:

- `items` (array, 必須)

---

### UpdateItemWithExternalId_ScheduleUpdate

**プロパティ**:

- `update` (unknown, 必須)
- `externalId` (string, 必須)
  - The external ID provided by the client. Must be unique for the resource type.

---

### UpdateItemWithExternalId_TransformationUpdate

**プロパティ**:

- `update` (unknown, 必須)
- `externalId` (string, 必須)
  - The external ID provided by the client. Must be unique for the resource type.

---

### UpdateItemWithId_ScheduleUpdate

**プロパティ**:

- `update` (unknown, 必須)
- `id` (integer, 必須)
  - A server-generated ID for the object.

---

### UpdateItemWithId_TransformationUpdate

**プロパティ**:

- `update` (unknown, 必須)
- `id` (integer, 必須)
  - A server-generated ID for the object.

---

### UpdateItem_AuthCertificate_

Set a new authentication certificate.

**プロパティ**:

- `set` (unknown, 必須)

---

### UpdateItem_CACertificate_

Set a new certificate authority.

**プロパティ**:

- `set` (unknown, 必須)

---

### UpdateItem_CustomMapping_

Set a new mapping value.

**プロパティ**:

- `set` (unknown, 必須)

---

### UpdateItem_DataSetId_

Set a new dataset ID.

**プロパティ**:

- `set` (integer, 必須)

---

### UpdateItem_DestinationId_

Set a new destination this job should write to.

**プロパティ**:

- `set` (string, 必須)

---

### UpdateItem_Host_

Set a new host or IP address.

**プロパティ**:

- `set` (string, 必須)

---

### UpdateItem_InputConfig_

Set a new mapping input.

**プロパティ**:

- `set` (unknown, 必須)

---

### UpdateItem_JobConfig_

Set a new source specific job config.

**プロパティ**:

- `set` (unknown, 任意)

---

### UpdateItem_JobTargetStatus_

Set a new job target status.

**プロパティ**:

- `set` (unknown, 必須)

---

### UpdateItem_KafkaAuthentication_

Set new credentials for authenticating with the broker.

**プロパティ**:

- `set` (unknown, 必須)

---

### UpdateItem_KafkaBrokers_

Set a new list of redundant kafka brokers.

**プロパティ**:

- `set` (unknown, 必須)

---

### UpdateItem_MqttAuthentication_

Set new credentials for authenticating with the broker.

**プロパティ**:

- `set` (unknown, 必須)

---

### UpdateItem_Partitions_

Set a new number of kafka partitions.

**プロパティ**:

- `set` (integer, 必須)
  - The number of partitions in the broker.

---

### UpdateItem_Port_

Set a new port this source should connect to.

**プロパティ**:

- `set` (integer, 必須)

---

### UpdateItem_RestScheme_

Set a new scheme for this source to use

**プロパティ**:

- `set` (string, 必須)

---

### UpdateItem_SessionCredentials

Set a new set of session credentials for writing to CDF.

**プロパティ**:

- `set` (unknown, 必須)

---

### UpdateItem_SessionCredentials_

Set a new set of session credentials for writing to CDF.

**プロパティ**:

- `set` (unknown, 必須)

---

### UpdateItem_SourceId_

Set a new source this job should write to.

**プロパティ**:

- `set` (string, 必須)

---

### UpdateJobResultRejectionsRequest

---

### UpdateKafkaSource

**プロパティ**:

- `bootstrapBrokers` (unknown, 任意)
- `authentication` (unknown, 任意)
  - Set or remove the credentials used for authentication.
- `useTls` (object, 任意)
  - Enable or disable TLS to the Kafka broker.
- `caCertificate` (unknown, 任意)
- `authCertificate` (unknown, 任意)

---

### UpdateKafkaSourceItem

**プロパティ**:

- `type` (string, 必須)
  - Source type.
- `externalId` (unknown, 必須)
- `update` (unknown, 必須)

---

### UpdateLibrariesRequest

List of libraries to update

**プロパティ**:

- `items` (array, 必須)

---

### UpdateMapping

**プロパティ**:

- `mapping` (unknown, 任意)
- `input` (unknown, 任意)
  - Set or remove mapping input. When removed this defaults to json.
- `published` (object, 任意)
  - Publish or unpublish the mapping.

---

### UpdateModel3D

---

### UpdateModel3DClassicBody

**プロパティ**:

- `items` (array, 必須)

---

### UpdateModel3DDmsOnly

---

### UpdateModel3DDmsOnlyBody

**プロパティ**:

- `items` (array, 必須)

---

### UpdateMqttBrokerSource

Update an MQTT broker source.

**プロパティ**:

- `password` (object, 任意)
  - Reset the password used to connect to the MQTT broker in CDF. This will quickly disconnect any existing clients using the old password.


---

### UpdateMqttBrokerSourceItem

**プロパティ**:

- `type` (string, 必須)
  - Source type.
- `externalId` (unknown, 必須)
- `update` (unknown, 必須)

---

### UpdateMqttV3Source

**プロパティ**:

- `host` (unknown, 任意)
- `port` (unknown, 任意)
  - Set or remove the port on the MQTT broker.
- `authentication` (unknown, 任意)
  - Set or remove the credentials used for authentication.
- `useTls` (object, 任意)
  - Enable or disable TLS to the MQTT broker.
- `caCertificate` (unknown, 任意)
- `authCertificate` (unknown, 任意)

---

### UpdateMqttV3SourceItem

**プロパティ**:

- `type` (string, 必須)
  - Source type.
- `externalId` (unknown, 必須)
- `update` (unknown, 必須)

---

### UpdateMqttV5Source

**プロパティ**:

- `host` (unknown, 任意)
- `port` (unknown, 任意)
  - Set or remove the port on the MQTT broker.
- `authentication` (unknown, 任意)
  - Set or remove the credentials used for authentication.
- `useTls` (object, 任意)
  - Enable or disable TLS to the MQTT broker.
- `caCertificate` (unknown, 任意)
- `authCertificate` (unknown, 任意)

---

### UpdateMqttV5SourceItem

**プロパティ**:

- `type` (string, 必須)
  - Source type.
- `externalId` (unknown, 必須)
- `update` (unknown, 必須)

---

### UpdateResponse

---

### UpdateRestSource

**プロパティ**:

- `host` (unknown, 任意)
- `scheme` (unknown, 任意)
- `port` (unknown, 任意)
- `authCertificate` (unknown, 任意)

---

### UpdateRestSourceItem

**プロパティ**:

- `type` (string, 必須)
  - Source type.
- `externalId` (unknown, 必須)
- `update` (unknown, 必須)

---

### UpdateRevision3D

---

### UpdateRevision3DClassicBody

**プロパティ**:

- `items` (array, 必須)

---

### UpdateRevision3DDmsOnly

---

### UpdateRevision3DDmsOnlyBody

**プロパティ**:

- `items` (array, 必須)

---

### UpdateRevision3DThumbnail

Request body for the updateModelRevisionThumbnail endpoint.

**プロパティ**:

- `fileId` (integer, 必須)
  - File ID of thumbnail file in Files API. _Only JPEG and PNG files are supported_.

---

### UpdateSetNull

**プロパティ**:

- `setNull` (boolean, 必須)
  - Clear this value

---

### UpdateSetNumber

**プロパティ**:

- `set` (integer, 必須)
  - Value to set

---

### UpdateSetShortStr

**プロパティ**:

- `set` (string, 必須)
  - Value to set

---

### UpdateSetString

**プロパティ**:

- `set` (string, 必須)
  - Value to set

---

### UpdateSymbolsRequest

List of symbols to update

**プロパティ**:

- `items` (array, 必須)

---

### UpdateUser

**プロパティ**:

- `credentials` (unknown, 任意)

---

### UploadFileMetadata

---

### UpsertConflict

**プロパティ**:

- `error` (object, 必須)
  - Details about the error caused by the upsert/update.

---

### UsedFor

Should this operation apply to nodes, edges, records, or both nodes and edges. **NB!** Currently ```all``` applies to nodes and edges, but not records.

---

### User

A username, or email, or name. This is not checked nor enforced. If the value is null, it means the
annotation was created by a service.

---

### UserCreated

A user

**プロパティ**:

- `host` (string, 必須)
  - The connection host for the postgres instance.
- `username` (unknown, 必須)
- `password` (string, 必須)
  - Password to authenticate the user on the DB.
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 任意)
- `sessionId` (integer, 任意)
  - ID of the session tied to this user.

---

### UserDto

---

### UserHistoryAppItem

**プロパティ**:

- `app` (string, 必須)
  - Unique app identifier

---

### UserHistoryResourceItem

**プロパティ**:

- `resourceId` (unknown, 必須)
- `app` (string, 必須)
  - Unique app identifier
- `action` (string, 必須)
  - The action performed on the resource
- `name` (string, 必須)
  - Resource name
- `path` (string, 必須)
  - Path to the resource

---

### UserId

The ID of an organization user

---

### UserIdentifier

**プロパティ**:

- `userIdentifier` (string, 必須)

---

### UserListResponse

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (unknown, 任意)

---

### UserPreferencesDateFormatField

---

### UserPreferencesLanguageField

---

### UserPreferencesRead

**プロパティ**:

- `general` (object, 任意)

---

### UserPreferencesTimeFormatField

---

### UserPreferencesTimezoneField

---

### UserPreferencesUpdate

**プロパティ**:

- `general` (object, 任意)

---

### UserPrincipal

**プロパティ**:

- `userIdentifier` (string, 必須)
  - Uniquely identifies the principal the profile is associated with.

- `displayName` (string, 必須)
  - The display name for the user.
- `givenName` (string, 任意)
  - The user's first name.
- `surname` (string, 任意)
  - The user's last name.
- `email` (string, 任意)
  - The user's email address (if any). The email address is is returned directly from the identity provider and not guaranteed to be verified.
It should
_not_ be used to uniquely identify as a user. Use the `userIdentifier` property instead.

- `pictureUrl` (string, 必須)
  - The avatar of the service account
- `identityType` (string, 必須)
- `lastUpdatedTime` (unknown, 必須)

---

### UserProfileItem

**プロパティ**:

- `userIdentifier` (string, 必須)
  - Uniquely identifies the principal the profile is associated with.
This property is _guaranteed_ to be immutable.

- `givenName` (string, 任意)
  - The user's first name.
- `surname` (string, 任意)
  - The user's last name.
- `email` (string, 任意)
  - The user's email address (if any). The email address is is returned directly from
the identity provider and not guaranteed to be verified.
Note that the email is mutable and can be updated in the identity provider. It should
_not_ be used to uniquely identify as a user. Use the `userIdentifier` property instead.

- `displayName` (string, 任意)
  - The display name for the user.
- `jobTitle` (string, 任意)
  - The user's job title.
- `identityType` (unknown, 任意)
- `lastUpdatedTime` (unknown, 必須)

---

### UserProfilesByIdsRequest

Array of user identifiers

**プロパティ**:

- `items` (array, 任意)

---

### UserProfilesByIdsResponse

**プロパティ**:

- `items` (array, 任意)

---

### UserProfilesConfiguration

---

### UserProfilesConfigurationDTO

**プロパティ**:

- `enabled` (boolean, 必須)
  - Should collection of user profiles be enabled for the project.

---

### UserProfilesConfigurationUpdateDTO

**プロパティ**:

- `enabled` (boolean, 任意)
  - Should collection of user profiles be enabled for the project.

---

### UserProfilesErrorResponse

**プロパティ**:

- `error` (object, 必須)

---

### UserProfilesListResponse

**プロパティ**:

- `items` (array, 必須)
- `nextCursor` (string, 任意)
  - Cursor to get the next page of results (if available).

---

### UserProfilesNotFoundResponse

**プロパティ**:

- `error` (object, 必須)

---

### UserProfilesSearchRequest

**プロパティ**:

- `search` (object, 任意)
- `limit` (integer, 任意)
  - <- Limits the maximum number of results to be returned by single request. The default is 25.

---

### UserProfilesSearchResponse

**プロパティ**:

- `items` (array, 任意)

---

### UserUpdateItem

**プロパティ**:

- `username` (unknown, 必須)
- `update` (unknown, 必須)

---

### Username

Username to authenticate the user on the DB.

---

### UsernameWrapper

**プロパティ**:

- `username` (unknown, 必須)

---

### ValidationError

**プロパティ**:

- `loc` (array, 必須)
- `msg` (string, 必須)
- `type` (string, 必須)

---

### ValidationErrorContent

Cognite API error.

**プロパティ**:

- `code` (integer, 必須)
  - HTTP status code.
- `message` (string, 必須)
  - Error message.

---

### ValidationErrorContent_

Cognite API error.

**プロパティ**:

- `code` (integer, 必須)
  - HTTP status code.
- `message` (string, 必須)
  - Error message.

---

### ValidationStatus

The status of document parser validation, which can be not ready, ready for review, approved, or rejected.


---

### Value

Value you wish to find in the provided property.

---

### ValueFormat

Assume the source data is on the form `some string` or `123.456`, and convert these into CDF datapoints, using prefix config to determine the timeseries external ID, and current time as timestamp.
If no prefix config is set, use the topic name as timeseries ID.

**プロパティ**:

- `type` (string, 必須)
  - Format type.
- `encoding` (unknown, 任意)
- `compression` (unknown, 任意)
- `prefix` (unknown, 任意)
- `dataModels` (unknown, 任意)

---

### Values

One or more values you wish to find in the provided property.

---

### ValuesAggregateResult

Common aggregate structure to represent aggregate result which have count buckets for filter resultset (Unique Property Values, Not Null Document Properties, etc).

**プロパティ**:

- `items` (array, 必須)

---

### ValveDetection

Detect and read state of a valve in an image. In beta. Available only when the `cdf-version: beta` header is provided.

---

### ValveDetectionParameters

Parameters for detecting and reading the state of a valve. In beta. Available only when the `cdf-version: beta` header is provided.

**プロパティ**:

- `threshold` (unknown, 任意)

---

### Version

Identifier for a version. Must be unique for the workflow. No trailing or leading whitespace and no null characters allowed.

---

### Versioned3DFile

The file ID of the data file for this resource, with multiple versions supported. Use /3d/files/{id} to retrieve the file.

**プロパティ**:

- `version` (integer, 必須)
  - Version of the file format.
- `fileId` (integer, 必須)
  - File ID. Use `/3d/files/{id}` to retrieve the file.

---

### Vertex

A vertex represents a 2D point in the image. The vertex coordinates are normalized.

**プロパティ**:

- `x` (number, 必須)
  - Normalized x coordinate.
- `y` (number, 必須)
  - Normalized y coordinate.

---

### ViewCommon

**プロパティ**:

- `space` (unknown, 必須)
  - Id of the space that the view belongs to
- `name` (string, 任意)
  - Readable name, meant for use in UIs
- `description` (string, 任意)
  - Description. Intended to describe the content, and use, of this view.
- `filter` (unknown, 任意)
- `implements` (array, 任意)
  - References to the views from where this view will inherit properties - both mapped properties (properties pointing to container properties like text, integers, direct relations) and connection properties (like reverse direct relations). 


Note: The order you list the views in is significant. We use this order to deduce the priority when we encounter duplicate property references.


If you do not specify a view version, we will use the most recent version available at the time of creation. 
- `version` (unknown, 必須)

---

### ViewConfig

The view the job is based on.


**プロパティ**:

- `space` (unknown, 任意)
- `externalId` (unknown, 任意)
- `version` (unknown, 任意)

---

### ViewConfigPlus

The view the job is based on.


**プロパティ**:

- `space` (unknown, 任意)
- `externalId` (unknown, 任意)
- `version` (unknown, 任意)
- `description` (string, 任意)
  - Description of the View
- `name` (string, 任意)
  - Name of the View
- `title` (string, 任意)
  - Title of the View

---

### ViewCorePropertyDefinition

---

### ViewCreateCollection

**プロパティ**:

- `items` (array, 必須)
  - List of views to create/update

---

### ViewCreateDefinition

---

### ViewCreateDefinitionProperty

A reference to a container property or a connection describing edges that are expected to
exist (ConnectionDefinition).

If the referenced container property is a direct relation, a view of the node can be specified. The view is
a hint to the consumer on what type of data is expected to be of interest in the context of this view.

A connection describes the edges that are likely to exist to aid in discovery and documentation of the view.
A listed edge is not required. i.e. It does not have to exist when included in this list. The target nodes of
this connection will match the view specified in the source property. A connection has a max distance of one hop
in the underlying graph.


---

### ViewDataSource

**プロパティ**:

- `type` (string, 必須)
  - The type of the destination resource indicating the instance type that the transformation will write to flexible data models.
- `view` (unknown, 任意)
- `edgeType` (unknown, 任意)
- `instanceSpace` (string, 任意)
  - The space where the instances(nodes/edges) will be created.

---

### ViewDefinition

---

### ViewDefinitionProperty

---

### ViewDirectNodeRelation

Direct node relation. Can include a hint to specify the view that this direct relation points to. This hint is optional.

---

### ViewInfo

Target view info

**プロパティ**:

- `space` (string, 必須)
  - Space of the View
- `externalId` (string, 必須)
  - External ID of the View
- `version` (string, 必須)
  - Version of the View

---

### ViewList_UnitSystem_

**プロパティ**:

- `items` (array, 必須)

---

### ViewList_Unit_

**プロパティ**:

- `items` (array, 必須)

---

### ViewOrContainer

View or container holding properties

---

### ViewPropertyDefinition

Property definition

---

### ViewPropertyReference

**プロパティ**:

- `view` (unknown, 必須)
  - Reference to a view - this is deprecated, use `source` with ViewReference instead
- `identifier` (unknown, 必須)
  - The unique identifier, from the view, for the property

---

### ViewReference

Reference to a view

**プロパティ**:

- `type` (string, 必須)
- `space` (unknown, 必須)
  - Id of the space that the view belongs to
- `externalId` (unknown, 必須)
  - External-id of the view
- `version` (unknown, 必須)
  - Version of the view

---

### ViewStatus

The status of the instance written to the View of Data Models.


---

### VirtualEntity

Diagram entity with its paths joined by pathIds

**プロパティ**:

- `externalId` (unknown, 必須)
  - The external ID of the diagram entity
- `pathIds` (array, 必須)
- `symbolId` (unknown, 必須)
  - The external ID of the symbol this diagram entity is detected as

---

### Visibility

---

### VisionAllOfFileId

**プロパティ**:

- `fileId` (unknown, 必須)
- `fileExternalId` (unknown, 任意)
- `fileInstanceId` (unknown, 任意)

---

### VisionExtractFeature

---

### VisionExtractItem

**プロパティ**:

- `fileId` (unknown, 必須)
- `fileExternalId` (unknown, 任意)
- `predictions` (unknown, 必須)

---

### VisionExtractPredictions

Detected features in images. New fields may appear in case new feature extractors are add.

**プロパティ**:

- `textPredictions` (array, 任意)
- `assetTagPredictions` (array, 任意)
- `peoplePredictions` (array, 任意)
- `industrialObjectPredictions` (array, 任意)
  - In beta. Available only when the `cdf-version: beta` header is provided.
- `personalProtectiveEquipmentPredictions` (array, 任意)
  - In beta. Available only when the `cdf-version: beta` header is provided.
- `dialGaugePredictions` (array, 任意)
  - In beta. Available only when the `cdf-version: beta` header is provided.
- `levelGaugePredictions` (array, 任意)
  - In beta. Available only when the `cdf-version: beta` header is provided.
- `digitalGaugePredictions` (array, 任意)
  - In beta. Available only when the `cdf-version: beta` header is provided.
- `valvePredictions` (array, 任意)
  - In beta. Available only when the `cdf-version: beta` header is provided.

---

### VisionFileExternalId

The external ID of a file in CDF.

---

### VisionFileId

The ID of a file in CDF.

---

### VisionInstanceId

The instance id of a file in CDF.

**プロパティ**:

- `space` (unknown, 必須)
- `externalId` (unknown, 必須)

---

### VisionSegmentEmbeddingItem

**プロパティ**:

- `fileId` (unknown, 必須)
- `fileExternalId` (unknown, 任意)
- `embedding` (string, 必須)

---

### VisionSegmentItem

**プロパティ**:

- `fileId` (unknown, 必須)
- `fileExternalId` (unknown, 任意)
- `segments` (unknown, 必須)

---

### VisionSegmentPredictions

Detected segments in the image, returned as polygons.

---

### WorkflowDefinition

**プロパティ**:

- `description` (string, 任意)
- `tasks` (array, 必須)

---

### WorkflowDefinitionResponse

**プロパティ**:

- `hash` (string, 必須)
- `description` (string, 任意)
- `tasks` (array, 必須)

---

### WorkflowExecution

**プロパティ**:

- `id` (unknown, 必須)
- `workflowExternalId` (unknown, 必須)
- `workflowDefinition` (unknown, 必須)
- `version` (unknown, 任意)
- `status` (unknown, 必須)
- `engineExecutionId` (unknown, 必須)
- `executedTasks` (array, 必須)
- `input` (unknown, 任意)
- `metadata` (unknown, 必須)

---

### WorkflowExecutionId

UUIDv4 identifier for a workflow execution.

---

### WorkflowExecutionResponse

**プロパティ**:

- `id` (unknown, 必須)
- `workflowExternalId` (unknown, 必須)
- `version` (unknown, 任意)
- `status` (unknown, 必須)
- `engineExecutionId` (unknown, 必須)
- `createdTime` (unknown, 必須)
- `startTime` (unknown, 任意)
- `endTime` (unknown, 任意)
- `reasonForIncompletion` (unknown, 任意)
- `metadata` (unknown, 必須)

---

### WorkflowExternalId

Identifier for a workflow. Must be unique for the project. No trailing or leading whitespace and no null characters allowed.

---

### WorkflowFilter

If the version is not specified, all versions for the workflow will be included, ordered by createdTime.

**プロパティ**:

- `externalId` (unknown, 必須)
- `version` (unknown, 任意)

---

### WorkflowStatus

---

### WorkflowVersionView

**プロパティ**:

- `workflowExternalId` (unknown, 任意)
- `version` (unknown, 任意)
- `workflowDefinition` (unknown, 任意)
- `createdTime` (unknown, 任意)
- `lastUpdatedTime` (unknown, 任意)

---

### WorkflowView

**プロパティ**:

- `externalId` (unknown, 必須)
- `description` (string, 任意)
- `createdTime` (unknown, 必須)
- `lastUpdatedTime` (unknown, 必須)
- `dataSetId` (unknown, 任意)

---

### WriteCustomFormat

Format the source data using a custom mapping.

**プロパティ**:

- `type` (string, 必須)
  - Format type.
- `encoding` (unknown, 任意)
- `compression` (unknown, 任意)
- `mappingId` (string, 必須)
  - ID of the mapping this format should be tied to.

---

### WritebackExternalIdWrapper

**プロパティ**:

- `externalId` (unknown, 必須)

---

### WritebackItemsWithIgnoreUnknownIdsAndForce_ExternalId_

**プロパティ**:

- `items` (array, 必須)
- `ignoreUnknownIds` (boolean, 任意)
  - Ignore IDs and external IDs that are not found.
- `force` (boolean, 任意)
  - Delete any jobs associated with each item.

---

### WritebackItemsWithIgnoreUnknownIds_ExternalId_

**プロパティ**:

- `items` (array, 必須)
- `ignoreUnknownIds` (boolean, 任意)
  - Ignore IDs and external IDs that are not found

---

### WritebackRequestId

Unique request ID generated by the CDF writeback API.

---

### WritebackValidationErrorContent

Cognite API error.

**プロパティ**:

- `code` (integer, 必須)
  - HTTP status code.
- `message` (string, 必須)
  - Error message.

---

### XmlConfig

Transform the input from an XML file to a JSON object.


**プロパティ**:

- `type` (string, 必須)
  - Input type

---

### advancedListFilter

Filter on relationships with exact match. Multiple filter elements in one property, for example `sourceExternalIds: [ "a", "b" ], returns all relationships where the sourceExternalId field is either `a` or `b`. Filters in multiple properties return relationships that match all criteria. If the filter is not specified, it defaults to an empty filter.

**プロパティ**:

- `sourceExternalIds` (array, 任意)
  - Include relationships that have any of these values in their `sourceExternalId` field
- `sourceTypes` (array, 任意)
  - Include relationships that have any of these values in their `sourceType` field
- `targetExternalIds` (array, 任意)
  - Include relationships that have any of these values in their `targetExternalId` field
- `targetTypes` (array, 任意)
  - Include relationships that have any of these values in their `targetType` field
- `dataSetIds` (array, 任意)
- `startTime` (unknown, 任意)
- `endTime` (unknown, 任意)
- `confidence` (unknown, 任意)
- `lastUpdatedTime` (unknown, 任意)
- `createdTime` (unknown, 任意)
- `activeAtTime` (unknown, 任意)
  - Limits results to those active within the specified time range, that is, if there is any overlap in the intervals [`activeAtTime.min`, `activeAtTime.max`] and [`startTime`, `endTime`], where both intervals are inclusive. If a relationship does not have a `startTime`, it is regarded as active from the beginning of time by this filter. If it does not have an `endTime` is is regarded as active until the end of time. Similarly, if a `min` is not supplied to the filter, the `min` is implicitly set to the beginning of time. If a `max` is not supplied, the `max` is implicitly set to the end of time.
- `labels` (unknown, 任意)
- `sourcesOrTargets` (array, 任意)
  - Include relationships that match any of the resources in either their source- or target-related fields.

---

### agents_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### agents_aclAction

---

### agents_aclScope

**プロパティ**:

- `all` (unknown, 任意)

---

### annotations_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### annotations_aclAction

---

### annotations_aclScope

---

### byIdsRequest

**プロパティ**:

- `items` (unknown, 必須)
- `ignoreUnknownIds` (unknown, 任意)
- `fetchResources` (unknown, 任意)

---

### cogniteWorkflowOrchestration_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### cogniteWorkflowOrchestration_aclAction

---

### cogniteWorkflowOrchestration_aclScope

---

### cogniteanalytics_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### cogniteanalytics_aclAction

---

### cogniteanalytics_aclScope

**プロパティ**:

- `all` (unknown, 任意)

---

### cogniteassets_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### cogniteassets_aclAction

---

### cogniteassets_aclScope

---

### cognitedatamodelInstances_aclAction

---

### cognitedatamodelinstances_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### cognitedatamodels_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### cognitedatamodels_aclAction

---

### cognitedatamodels_aclScope

---

### cognitedatamodels_aclSpaceIdScope

**プロパティ**:

- `spaceIds` (array, 任意)

---

### cognitedatasets_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### cognitedatasets_aclAction

---

### cognitedatasets_aclIdScope

**プロパティ**:

- `ids` (array, 任意)

---

### cognitedatasets_aclScope

---

### cognitediagramparsing_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### cognitediagramparsing_aclAction

---

### cognitediagramparsing_aclScope

---

### cognitedigitaltwin_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### cognitedigitaltwin_aclAction

---

### cognitedigitaltwin_aclScope

**プロパティ**:

- `all` (unknown, 任意)

---

### cogniteevents_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### cogniteevents_aclAction

---

### cogniteevents_aclScope

---

### cognitefiles_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### cognitefiles_aclAction

---

### cognitefiles_aclScope

---

### cognitegeospatial_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### cognitegeospatial_aclAction

---

### cognitegeospatial_aclScope

**プロパティ**:

- `all` (unknown, 任意)

---

### cognitegeospatialcrs_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### cognitegeospatialcrs_aclAction

---

### cognitegeospatialcrs_aclScope

**プロパティ**:

- `all` (unknown, 任意)

---

### cognitegroups_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### cognitegroups_aclAction

---

### cognitegroups_aclScope

---

### cogniteilainstances_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### cogniteilainstances_aclAction

---

### cognitelabels_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### cognitelabels_aclAction

---

### cognitelabels_aclScope

---

### cogniteprojects_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### cogniteprojects_aclAction

---

### cogniteprojects_aclScope

**プロパティ**:

- `all` (unknown, 任意)

---

### cogniteraw_aclAcl

Set access control on RAW

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### cogniteraw_aclAction

---

### cogniteraw_aclDbsToTablesScope

Scope access to certain tables within a database

**プロパティ**:

- `dbsToTables` (object, 任意)
  - Scopes access to tables within a database. { database1: [table1, table2] } will give access to table1 and table2 within database1.

---

### cogniteraw_aclScope

---

### cogniterelationships_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### cogniterelationships_aclAction

---

### cogniterelationships_aclScope

---

### cogniterobotics_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### cogniterobotics_aclAction

---

### cogniterobotics_aclScope

---

### cognitesecuritycategories_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### cognitesecuritycategories_aclAction

---

### cognitesecuritycategories_aclIdScope

**プロパティ**:

- `ids` (array, 任意)

---

### cognitesecuritycategories_aclScope

---

### cogniteseismic_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### cogniteseismic_aclAction

---

### cogniteseismic_aclScope

**プロパティ**:

- `all` (unknown, 任意)

---

### cognitesequences_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### cognitesequences_aclAction

---

### cognitesequences_aclScope

---

### cognitethreed_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### cognitethreed_aclAction

---

### cognitethreed_aclScope

---

### cognitetimeseries_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### cognitetimeseries_aclAction

---

### cognitetimeseries_aclAssetRootIdScope

**プロパティ**:

- `rootIds` (array, 任意)

---

### cognitetimeseries_aclIdScope

**プロパティ**:

- `ids` (array, 任意)

---

### cognitetimeseries_aclScope

---

### cognitetimeseriessubscriptions_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### cognitetimeseriessubscriptions_aclAction

---

### cognitetimeseriessubscriptions_aclScope

---

### cognitetransformations_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### cognitetransformations_aclAction

---

### cognitetransformations_aclScope

---

### cognitetypes_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### cognitetypes_aclAction

---

### cognitetypes_aclScope

**プロパティ**:

- `all` (unknown, 任意)

---

### confidence

Confidence value of the existence of the relationship. Generated relationships provide a score of the likelihood of the relationship existing. Relationships without a confidence value can be interpreted at the discretion of each project.

---

### cursor

Cursor to use for paging through results. This cursor is returned in the response of a previous request as `nextCursor`. If not specified, start from the first page of results.

---

### cursorObject

**プロパティ**:

- `nextCursor` (string, 任意)
  - The cursor to get the next page of results (if available).

---

### data

Input data to the function (only present if provided on the schedule). This data is passed deserialized into the function through one of the arguments called `data`. **WARNING:** Secrets or other confidential information should not be passed via the `data` object. There is a dedicated `secrets` object in the request body to "Create functions" for this purpose.'

---

### dataSetId

The ID of the dataset the relationship belongs to.

---

### dataSetIdWorkflows

The unique identifier (ID) of the dataset that this workflow is associated with. \
A user must have access to this dataset to perform any actions on the workflow, such as viewing, updating, or deleting it. \
Additionally, to manage any resources connected to the workflow (such as triggers, versions, or executions), the user must also have access to the dataset for viewing, updating, creating, and deleting these resources.


---

### deleteRequest

**プロパティ**:

- `items` (unknown, 必須)
- `ignoreUnknownIds` (unknown, 任意)

---

### endTime

The time, in milliseconds since Jan. 1, 1970, when the relationship became inactive. If there is no endTime, the relationship is active from startTime until the present or any point in the future. If endTime and startTime are set, the endTime must be greater than startTime.

---

### enrichedRelationship

**プロパティ**:

- `source` (unknown, 任意)
- `target` (unknown, 任意)

---

### enrichedRelationshipResponse

---

### enrichedRelationshipResponseWrapper

**プロパティ**:

- `items` (array, 必須)

---

### entitymatching_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### entitymatching_aclAction

---

### entitymatching_aclScope

---

### externalIdObject

**プロパティ**:

- `externalId` (unknown, 必須)

---

### extractionconfigs_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### extractionconfigs_aclAction

---

### extractionconfigs_aclScope

---

### extractionpipelines_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### extractionpipelines_aclAction

---

### extractionpipelines_aclIdScope

**プロパティ**:

- `ids` (array, 任意)

---

### extractionpipelines_aclScope

---

### extractionruns_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### extractionruns_aclAction

---

### extractionruns_aclScope

---

### fetchResources

If true,
will try to fetch the resources referred to in the relationship,
based on the users access rights.
Will silently fail to attatch the resources if the user lacks access to some of them.


---

### floatRange

Range to filter the field for (inclusive).

**プロパティ**:

- `min` (number, 任意)
- `max` (number, 任意)

---

### functions_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### functions_aclAction

---

### functions_aclScope

**プロパティ**:

- `all` (unknown, 任意)

---

### generalError

Cognite API error

**プロパティ**:

- `code` (integer, 必須)
  - HTTP status code
- `message` (string, 必須)
  - Error message
- `missing` (array, 任意)
  - List of lookup objects that do not match any results.
- `duplicated` (array, 任意)
  - List of objects that are not unique.

---

### generalErrorWrapper

Error wrapper Error message

**プロパティ**:

- `error` (unknown, 必須)

---

### generic_aclAllScope

---

### generic_aclCurrentUserScope

---

### hostedextractors_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### hostedextractors_aclAction

---

### hostedextractors_aclScope

---

### ignoreUnknownIds

Ignore external IDs that are not found.

---

### itemsArray

---

### jobId

ID for Document Parser job

---

### limit

The maximum number of results to return.

---

### limits_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### limits_aclAction

---

### limits_aclScope

**プロパティ**:

- `all` (unknown, 任意)

---

### locationfilters_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### locationfilters_aclAction

---

### locationfilters_aclIdScope

**プロパティ**:

- `ids` (array, 任意)

---

### locationfilters_aclScope

---

### metadata

Custom, application-specific metadata. String key -> String value.
Keys have a maximum length of 32 characters, values a maximum of 255,
and there can be a maximum of 10 key-value pairs.


---

### nextCursor

Cursor to get the next page of results. If not present, no more results are available.

---

### nonce

Nonce retrieved from sessions API when creating a session. This will be used to bind the session before executing the function. The corresponding access token will be passed to the function and used to instantiate the client of the handle() function. You can create a session via the [Sessions API](#operation/createSessions). When using the Python SDK, the session will be created behind the scenes when creating the schedule.

---

### pagedEnrichedRelationshipResponseWrapper

---

### pagedRelationshipResponseWrapper

---

### persistedObject

**プロパティ**:

- `externalId` (unknown, 必須)
- `createdTime` (unknown, 必須)
  - The time, in milliseconds since Jan. 1, 1970, when the relationship was created.
- `lastUpdatedTime` (unknown, 必須)
  - The time, in milliseconds since Jan. 1, 1970, when the relationship was last updated.

---

### relationship

The representation of a relationship consists of a source and a target and additional parameters.

**プロパティ**:

- `externalId` (unknown, 必須)
  - External ID of the relationship, must be unique within the project.
- `sourceExternalId` (unknown, 必須)
- `sourceType` (unknown, 必須)
- `targetExternalId` (unknown, 必須)
- `targetType` (unknown, 必須)
- `startTime` (unknown, 任意)
- `endTime` (unknown, 任意)
- `confidence` (unknown, 任意)
- `dataSetId` (unknown, 任意)
- `labels` (unknown, 任意)

---

### relationshipExternalId

The external ID of the relationship.

---

### relationshipRequestWrapper

**プロパティ**:

- `items` (array, 必須)

---

### relationshipResponse

---

### relationshipResponseWrapper

**プロパティ**:

- `items` (array, 必須)

---

### relationshipUpdate

**プロパティ**:

- `externalId` (unknown, 必須)
- `update` (unknown, 必須)

---

### relationshipUpdateContent

**プロパティ**:

- `sourceType` (unknown, 任意)
- `sourceExternalId` (unknown, 任意)
- `targetType` (unknown, 任意)
- `targetExternalId` (unknown, 任意)
- `confidence` (unknown, 任意)
- `startTime` (unknown, 任意)
- `endTime` (unknown, 任意)
- `dataSetId` (unknown, 任意)
- `labels` (unknown, 任意)

---

### relationshipsAdvancedListRequest

**プロパティ**:

- `filter` (unknown, 任意)
- `limit` (unknown, 任意)
- `cursor` (unknown, 任意)
- `fetchResources` (unknown, 任意)
- `partition` (unknown, 任意)

---

### resourceExternalId

---

### resourceReferenceWithExternalId

**プロパティ**:

- `type` (unknown, 任意)
- `externalId` (unknown, 任意)

---

### resourceType

---

### sessions_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### sessions_aclAction

---

### sessions_aclScope

---

### setConfidence

**プロパティ**:

- `set` (unknown, 必須)

---

### setDataSetId

**プロパティ**:

- `set` (unknown, 必須)

---

### setEndTime

**プロパティ**:

- `set` (unknown, 必須)

---

### setStartTime

**プロパティ**:

- `set` (unknown, 必須)

---

### simulators_aclAcl

**プロパティ**:

- `actions` (array, 必須)
- `scope` (unknown, 必須)

---

### simulators_aclAction

---

### simulators_aclScope

---

### sourceExternalId

The external ID of the resource that is the relationship source.

---

### sourceType

The resource type of the relationship source. Must be one of the specified values.

---

### startTime

The time, in milliseconds since Jan. 1, 1970, when the relationship became active. If there is no startTime, the relationship is active from the beginning of time until endTime.

---

### targetExternalId

The external ID of the resource that is the relationship target.

---

### targetType

The resource type of the relationship target. Must be one of the specified values.

---

### updateConfidence

Set a new value for the confidence, or remove the value.

---

### updateDataSetId

Set a new value for the dataSet Ids, or remove the value.

---

### updateEndTime

---

### updateRelationshipWrapper

**プロパティ**:

- `items` (array, 必須)

---

### updateSourceExternalId

Set a new value for the relationship source external ID.

**プロパティ**:

- `set` (unknown, 必須)

---

### updateSourceType

Set a new value for the relationship source type.

**プロパティ**:

- `set` (unknown, 必須)

---

### updateStartTime

Set a new value for the start time, or remove the value.

---

### updateTargetExternalId

Set a new value for the relationship target external ID.

**プロパティ**:

- `set` (unknown, 必須)

---

### updateTargetType

Set a new value for the relationship target type.

**プロパティ**:

- `set` (unknown, 必須)

---
