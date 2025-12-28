# cognite-sdk-python Documentation
Generated from: cognite-sdk-python



<!-- SOURCE_START: cognite-sdk-python/docs\source\3d.rst -->
## File: cognite-sdk-python/docs\source\3d.rst

3D
==

Models
^^^^^^
Retrieve a model by ID
~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.three_d.ThreeDModelsAPI.retrieve

List models
~~~~~~~~~~~
.. automethod:: cognite.client._api.three_d.ThreeDModelsAPI.list

Create models
~~~~~~~~~~~~~
.. automethod:: cognite.client._api.three_d.ThreeDModelsAPI.create

Update models
~~~~~~~~~~~~~
.. automethod:: cognite.client._api.three_d.ThreeDModelsAPI.update

Delete models
~~~~~~~~~~~~~
.. automethod:: cognite.client._api.three_d.ThreeDModelsAPI.delete


Revisions
^^^^^^^^^
Retrieve a revision by ID
~~~~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.three_d.ThreeDRevisionsAPI.retrieve

Create a revision
~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.three_d.ThreeDRevisionsAPI.create

List revisions
~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.three_d.ThreeDRevisionsAPI.list

Update revisions
~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.three_d.ThreeDRevisionsAPI.update

Delete revisions
~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.three_d.ThreeDRevisionsAPI.delete

Update a revision thumbnail
~~~~~~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.three_d.ThreeDRevisionsAPI.update_thumbnail

List nodes
~~~~~~~~~~
.. automethod:: cognite.client._api.three_d.ThreeDRevisionsAPI.list_nodes

Filter nodes
~~~~~~~~~~~~
.. automethod:: cognite.client._api.three_d.ThreeDRevisionsAPI.filter_nodes

List ancestor nodes
~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.three_d.ThreeDRevisionsAPI.list_ancestor_nodes

Files
^^^^^
Retrieve a 3D file
~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.three_d.ThreeDFilesAPI.retrieve

Asset mappings
^^^^^^^^^^^^^^
Create an asset mapping
~~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.three_d.ThreeDAssetMappingAPI.create

List asset mappings
~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.three_d.ThreeDAssetMappingAPI.list

Delete asset mappings
~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.three_d.ThreeDAssetMappingAPI.delete

..
    Reveal
    ^^^^^^
    Retrieve a revision by ID
    ~~~~~~~~~~~~~~~~~~~~~~~~~
    .. automethod:: cognite.client._api.three_d.ThreeDRevealAPI.retrieve_revision

    List sectors
    ~~~~~~~~~~~~
    .. automethod:: cognite.client._api.three_d.ThreeDRevealAPI.list_sectors

    List nodes
    ~~~~~~~~~~
    .. automethod:: cognite.client._api.three_d.ThreeDRevisionsAPI.list_nodes

    List ancestor nodes
    ~~~~~~~~~~~~~~~~~~~
    .. automethod:: cognite.client._api.three_d.ThreeDRevisionsAPI.list_ancestor_nodes

Data classes
^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.three_d
    :members:
    :show-inheritance:


<!-- SOURCE_END: cognite-sdk-python/docs\source\3d.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\agents.rst -->
## File: cognite-sdk-python/docs\source\agents.rst

======
Agents
======
Create or update an agent
^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.agents.agents.AgentsAPI.upsert

Retrieve an agent by external id
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.agents.agents.AgentsAPI.retrieve

List all agents
^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.agents.agents.AgentsAPI.list

Delete an agent by external id
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.agents.agents.AgentsAPI.delete

Chat with an agent by external id
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.agents.agents.AgentsAPI.chat

Agent Data classes
^^^^^^^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.agents
    :members:
    :show-inheritance:


<!-- SOURCE_END: cognite-sdk-python/docs\source\agents.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\ai.rst -->
## File: cognite-sdk-python/docs\source\ai.rst

AI
===============
AI API
---------------
Document Summarization
^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.ai.tools.documents.AIDocumentsAPI.summarize

Document Question Answering
^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.ai.tools.documents.AIDocumentsAPI.ask_question


<!-- SOURCE_END: cognite-sdk-python/docs\source\ai.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\appendix.rst -->
## File: cognite-sdk-python/docs\source\appendix.rst


.. _appendix-upsert:

Upsert
^^^^^^^^^^^^^^^^^^^^

Upsert methods `cognite_client.api.upsert()` for example `cognite_client.assets.upsert()` the following caveats and
notes apply:

.. warning::
    Upsert is not an atomic operation. It performs multiple calls to the update and create endpoints, depending
    on whether the items exist from before or not. This means that if one of the calls fail, it is possible
    that some of the items have been updated/created while others have not been created/updated.

.. _appendix-update:

Update and Upsert Mode Parameter
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The mode parameter controls how the update is performed. If you set 'patch', the call will only update
the fields in the Item object that are not None. This means that if the items exists from before, the
fields that are not specified will not be changed. The 'replace_ignore_null' works similarly, to
'patch', but instead of updating it will replace the fields with new values. There is not difference
between 'patch' and 'replace_ignore_null' for fields that only supports set. For example, `name` and
`description` on TimeSeries. However, for fields that supports `set` and `add/remove`, like `metadata`,
'patch` will add to the metadata, while 'replace_ignore_null' will replace the metadata with the new
metadata. If you set 'replace', all the fields that are not specified, i.e., set to None and
support being set to null, will be nulled out.

Example **patch**:

.. testsetup:: patch_update

    >>> getfixture("appendix_update_patch")  # Fixture defined in conftest.py

.. doctest:: patch_update

.. code:: python

    >>> from cognite.client import CogniteClient
    >>> from cognite.client.data_classes import TimeSeriesWrite
    >>> from pprint import pprint
    >>> client = CogniteClient()
    >>>
    >>> new_ts = client.time_series.create(
    ...     TimeSeriesWrite(
    ...         external_id="new_ts",
    ...         name="New TS",
    ...         metadata={"key": "value", "another": "one"}
    ...     )
    ... )
    >>>
    >>> updated = client.time_series.update(
    ...     TimeSeriesWrite(
    ...         external_id="new_ts",
    ...         description="Updated description",
    ...         metadata={"key": "new value", "brand": "new"}
    ...     ),
    ...     mode="patch"
    ... )
    >>> pprint(updated.as_write().dump())
    {'description': 'Updated description',
     'externalId': 'new_ts',
     'metadata': {'another': 'one', 'brand': 'new', 'key': 'new value'},
     'name': 'New TS'}

Example **replace**:

.. testsetup:: patch_replace

    >>> getfixture("appendix_update_replace")  # Fixture defined in conftest.py

.. doctest:: patch_replace

.. code:: python

    >>> from cognite.client import CogniteClient
    >>> from cognite.client.data_classes import TimeSeriesWrite
    >>> from pprint import pprint
    >>> client = CogniteClient()
    >>>
    >>> new_ts = client.time_series.create(
    ...     TimeSeriesWrite(
    ...         external_id="new_ts",
    ...         name="New TS",
    ...         metadata={"key": "value"}
    ...     )
    ... )
    >>>
    >>> updated = client.time_series.update(
    ...     TimeSeriesWrite(
    ...         external_id="new_ts",
    ...         description="Updated description",
    ...         metadata={"new": "entry"}
    ...     ),
    ...     mode="replace"
    ... )
    >>> pprint(updated.as_write().dump())
    {'description': 'Updated description',
     'externalId': 'new_ts',
     'metadata': {'new': 'entry'}}

**Note** that the `name` parameter was not specified in the update, and was therefore nulled out.

Example **replace_ignore_null**:

.. testsetup:: patch_replace_ignore_null

    >>> getfixture("appendix_update_replace_ignore_null")  # Fixture defined in conftest.py

.. doctest:: patch_replace_ignore_null

.. code:: python

    >>> from cognite.client import CogniteClient
    >>> from cognite.client.data_classes import TimeSeriesWrite
    >>> from pprint import pprint
    >>> client = CogniteClient()
    >>>
    >>> new_ts = client.time_series.create(
    ...     TimeSeriesWrite(
    ...         external_id="new_ts",
    ...         name="New TS",
    ...         metadata={"key": "value"}
    ...     )
    ... )
    >>>
    >>> updated = client.time_series.update(
    ...     TimeSeriesWrite(
    ...         external_id="new_ts",
    ...         description="Updated description",
    ...         metadata={"new": "entry"}
    ...     ),
    ...     mode="replace_ignore_null"
    ... )
    >>> pprint(updated.as_write().dump())
    {'description': 'Updated description',
     'externalId': 'new_ts',
     'metadata': {'new': 'entry'},
     'name': 'New TS'}

**Note** that the `name` parameter was not specified in the update, and was therefore not changed,
same as in `patch`

Example **replace_ignore_null** without `metadata`:

.. testsetup:: patch_replace_ignore_null2

    >>> getfixture("appendix_update_replace_ignore_null2")  # Fixture defined in conftest.py

.. doctest:: patch_replace_ignore_null2

.. code:: python

    >>> from cognite.client import CogniteClient
    >>> from cognite.client.data_classes import TimeSeriesWrite
    >>> from pprint import pprint
    >>> client = CogniteClient()
    >>>
    >>> new_ts = client.time_series.create(
    ...     TimeSeriesWrite(
    ...         external_id="new_ts",
    ...         name="New TS",
    ...         metadata={"key": "value"}
    ...     )
    ... )
    >>>
    >>> updated = client.time_series.update(
    ...     TimeSeriesWrite(
    ...         external_id="new_ts",
    ...         description="Updated description",
    ...     ),
    ...     mode="replace_ignore_null"
    ... )
    >>> pprint(updated.as_write().dump())
    {'description': 'Updated description',
     'externalId': 'new_ts',
     'metadata': {'key': 'value'},
     'name': 'New TS'}

**Note** Since `metadata` was not specified in the update, it was not changed.

.. _appendix-alpha-beta-features:

Alpha and Beta Features
^^^^^^^^^^^^^^^^^^^^^^^^
New Cognite Data Fusion API features may get support in the Python SDK before they are released for
general availability (GA). These features are marked as alpha or beta in the documentation, and will also
invoke a `FeaturePreviewWarning` when used.

Furthermore, we distinguish between maturity of the API specification and the SDK implementation. Typically,
the API specification may be in beta, while the SDK implementation is in alpha.

* `alpha` - The feature is not yet released for general availability. There may be breaking changes to the API
  specification and/or the SDK implementation without further notice.
* `beta` - The feature is not yet released for general availability. The feature is considered stable and 'settled'.
  Learnings during the Beta period may result in a requirement to make breaking changes to API spec/SDK implementation.
  In these situations, release processes must be coordinated to minimise Beta customer disruption (for example use of
  `DeprecationWarning`).


<!-- SOURCE_END: cognite-sdk-python/docs\source\appendix.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\assets.rst -->
## File: cognite-sdk-python/docs\source\assets.rst

Assets
======
Retrieve an asset by id
^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.assets.AssetsAPI.retrieve

Retrieve multiple assets by id
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.assets.AssetsAPI.retrieve_multiple

Retrieve an asset subtree
^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.assets.AssetsAPI.retrieve_subtree

List assets
^^^^^^^^^^^
.. automethod:: cognite.client._api.assets.AssetsAPI.list

Aggregate assets
^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.assets.AssetsAPI.aggregate

Aggregate Asset Count
^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.assets.AssetsAPI.aggregate_count

Aggregate Asset Value Cardinality
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.assets.AssetsAPI.aggregate_cardinality_values

Aggregate Asset Property Cardinality
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.assets.AssetsAPI.aggregate_cardinality_properties

Aggregate Asset Unique Values
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.assets.AssetsAPI.aggregate_unique_values

Aggregate Asset Unique Properties
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.assets.AssetsAPI.aggregate_unique_properties

Search for assets
^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.assets.AssetsAPI.search

Create assets
^^^^^^^^^^^^^
.. automethod:: cognite.client._api.assets.AssetsAPI.create

Create asset hierarchy
^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.assets.AssetsAPI.create_hierarchy

Delete assets
^^^^^^^^^^^^^
.. automethod:: cognite.client._api.assets.AssetsAPI.delete

Filter assets
^^^^^^^^^^^^^
.. automethod:: cognite.client._api.assets.AssetsAPI.filter

Update assets
^^^^^^^^^^^^^
.. automethod:: cognite.client._api.assets.AssetsAPI.update

Upsert assets
^^^^^^^^^^^^^
.. automethod:: cognite.client._api.assets.AssetsAPI.upsert

Asset Data classes
^^^^^^^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.assets
    :members:
    :show-inheritance:


<!-- SOURCE_END: cognite-sdk-python/docs\source\assets.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\base_data_classes.rst -->
## File: cognite-sdk-python/docs\source\base_data_classes.rst

Base data classes
==================
CogniteResource
^^^^^^^^^^^^^^^
.. autoclass:: cognite.client.data_classes._base.CogniteResource
    :members:

CogniteResourceList
^^^^^^^^^^^^^^^^^^^
.. autoclass:: cognite.client.data_classes._base.CogniteResourceList
    :members:

CogniteResponse
^^^^^^^^^^^^^^^
.. autoclass:: cognite.client.data_classes._base.CogniteResponse
    :members:

CogniteFilter
^^^^^^^^^^^^^
.. autoclass:: cognite.client.data_classes._base.CogniteFilter
    :members:

CogniteUpdate
^^^^^^^^^^^^^
.. autoclass:: cognite.client.data_classes._base.CogniteUpdate
    :members:

<!-- SOURCE_END: cognite-sdk-python/docs\source\base_data_classes.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\cognite_client.rst -->
## File: cognite-sdk-python/docs\source\cognite_client.rst

CogniteClient
=============

.. _class_client_CogniteClient:
.. autoclass:: cognite.client.CogniteClient
    :members:
    :member-order: bysource

.. _class_client_ClientConfig:
.. autoclass:: cognite.client.config.ClientConfig
    :members:
    :member-order: bysource

.. _class_client_GlobalConfig:
.. autoclass:: cognite.client.config.GlobalConfig
    :members:
    :member-order: bysource


<!-- SOURCE_END: cognite-sdk-python/docs\source\cognite_client.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\contextualization.rst -->
## File: cognite-sdk-python/docs\source\contextualization.rst

Contextualization
=================

Entity Matching
---------------
These APIs will return as soon as possible, deferring a blocking wait until the last moment. Nevertheless, they can block for a long time awaiting results.

Fit Entity Matching Model
^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.entity_matching.EntityMatchingAPI.fit

Re-fit Entity Matching Model
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.entity_matching.EntityMatchingAPI.refit

Retrieve Entity Matching Models
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.entity_matching.EntityMatchingAPI.retrieve
.. automethod:: cognite.client._api.entity_matching.EntityMatchingAPI.retrieve_multiple
.. automethod:: cognite.client._api.entity_matching.EntityMatchingAPI.list

Delete Entity Matching Models
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.entity_matching.EntityMatchingAPI.delete

Update Entity Matching Models
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.entity_matching.EntityMatchingAPI.update

Predict Using an Entity Matching Model
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.entity_matching.EntityMatchingAPI.predict

Engineering Diagrams
--------------------

Detect entities in Engineering Diagrams
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.diagrams.DiagramsAPI.detect

Convert to an interactive SVG where the provided annotations are highlighted
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.diagrams.DiagramsAPI.convert


Vision
------

The Vision API enable extraction of information from imagery data based on
their visual content. For example, you can can extract features such as text, asset tags or industrial objects from images using this service.

**Quickstart**

Start an asynchronous job to extract information from image files stored in CDF:

.. code:: python

    from cognite.client import CogniteClient
    from cognite.client.data_classes.contextualization import VisionFeature

    client = CogniteClient()
    extract_job = client.vision.extract(
        features=[VisionFeature.ASSET_TAG_DETECTION, VisionFeature.PEOPLE_DETECTION],
        file_ids=[1, 2],
    )


The returned job object, :code:`extract_job`, can be used to retrieve the status of the job and the prediction results once the job is completed.
Wait for job completion and get the parsed results:

.. code:: python

    extract_job.wait_for_completion()
    for item in extract_job.items:
        predictions = item.predictions
        # do something with the predictions

Save the prediction results in CDF as `Annotations <https://docs.cognite.com/api/v1/#tag/Annotations>`_:

.. code:: python

    extract_job.save_predictions()

.. note::
    Prediction results are stored in CDF as `Annotations <https://docs.cognite.com/api/v1/#tag/Annotations>`_ using the :code:`images.*` annotation types. In particular, text detections are stored as :code:`images.TextRegion`, asset tag detections are stored as :code:`images.AssetLink`, while other detections are stored as :code:`images.ObjectDetection`.

Tweaking the parameters of a feature extractor:

.. code:: python

    from cognite.client.data_classes.contextualization import FeatureParameters, TextDetectionParameters

    extract_job = client.vision.extract(
        features=VisionFeature.TEXT_DETECTION,
        file_ids=[1, 2],
        parameters=FeatureParameters(text_detection_parameters=TextDetectionParameters(threshold=0.9))
        # or
        # parameters = {"textDetectionParameters": {"threshold": 0.9}}
    )

Extract
^^^^^^^

.. automethod:: cognite.client._api.vision.VisionAPI.extract

Get vision extract job
^^^^^^^^^^^^^^^^^^^^^^

.. automethod:: cognite.client._api.vision.VisionAPI.get_extract_job


Contextualization Data Classes
------------------------------

.. automodule:: cognite.client.data_classes.contextualization
    :members:
    :undoc-members:
    :show-inheritance:
    :inherited-members:


.. automodule:: cognite.client.data_classes.annotation_types.images
    :members:
    :undoc-members:
    :show-inheritance:
    :inherited-members:


.. automodule:: cognite.client.data_classes.annotation_types.primitives
    :members:
    :undoc-members:
    :show-inheritance:
    :inherited-members:


<!-- SOURCE_END: cognite-sdk-python/docs\source\contextualization.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\credential_providers.rst -->
## File: cognite-sdk-python/docs\source\credential_providers.rst

Credential Providers
====================
.. autoclass:: cognite.client.credentials.CredentialProvider
    :members:
    :member-order: bysource
.. autoclass:: cognite.client.credentials.Token
    :members:
    :member-order: bysource
.. autoclass:: cognite.client.credentials.OAuthClientCredentials
    :members:
    :member-order: bysource
.. autoclass:: cognite.client.credentials.OAuthInteractive
    :members:
    :member-order: bysource
.. autoclass:: cognite.client.credentials.OAuthDeviceCode
    :members:
    :member-order: bysource
.. autoclass:: cognite.client.credentials.OAuthClientCertificate
    :members:
    :member-order: bysource


<!-- SOURCE_END: cognite-sdk-python/docs\source\credential_providers.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\data_ingestion.rst -->
## File: cognite-sdk-python/docs\source\data_ingestion.rst

Data Ingestion
==============

Raw
---
Databases
^^^^^^^^^
List databases
~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.raw.RawDatabasesAPI.list

Create new databases
~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.raw.RawDatabasesAPI.create

Delete databases
~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.raw.RawDatabasesAPI.delete


Tables
^^^^^^
List tables in a database
~~~~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.raw.RawTablesAPI.list

Create new tables in a database
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.raw.RawTablesAPI.create

Delete tables from a database
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.raw.RawTablesAPI.delete


Rows
^^^^
Get a row from a table
~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.raw.RawRowsAPI.retrieve

List rows in a table
~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.raw.RawRowsAPI.list

Insert rows into a table
~~~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.raw.RawRowsAPI.insert

Delete rows from a table
~~~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.raw.RawRowsAPI.delete

Retrieve pandas dataframe
~~~~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.raw.RawRowsAPI.retrieve_dataframe

Insert pandas dataframe
~~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.raw.RawRowsAPI.insert_dataframe


RAW Data classes
^^^^^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.raw
    :members:
    :show-inheritance:

Extraction pipelines
--------------------
List extraction pipelines
^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.extractionpipelines.ExtractionPipelinesAPI.list

Create extraction pipeline
^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.extractionpipelines.ExtractionPipelinesAPI.create

Retrieve an extraction pipeline by ID
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.extractionpipelines.ExtractionPipelinesAPI.retrieve

Retrieve multiple extraction pipelines by ID
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.extractionpipelines.ExtractionPipelinesAPI.retrieve_multiple

Update extraction pipelines
^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.extractionpipelines.ExtractionPipelinesAPI.update

Delete extraction pipelines
^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.extractionpipelines.ExtractionPipelinesAPI.delete


Extraction pipeline runs
------------------------
List runs for an extraction pipeline
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.extractionpipelines.ExtractionPipelineRunsAPI.list

Report new runs
^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.extractionpipelines.ExtractionPipelineRunsAPI.create


Extraction pipeline configs
---------------------------
Get the latest or a specific config revision
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.extractionpipelines.ExtractionPipelineConfigsAPI.retrieve

List configuration revisions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.extractionpipelines.ExtractionPipelineConfigsAPI.list

Create a config revision
^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.extractionpipelines.ExtractionPipelineConfigsAPI.create

Revert to an earlier config revision
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.extractionpipelines.ExtractionPipelineConfigsAPI.revert

Extractor Config Data classes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.extractionpipelines
    :members:
    :show-inheritance:


<!-- SOURCE_END: cognite-sdk-python/docs\source\data_ingestion.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\data_modeling.rst -->
## File: cognite-sdk-python/docs\source\data_modeling.rst

Data Modeling
=============

Data Models
------------
Retrieve data models by id(s)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.data_models.DataModelsAPI.retrieve

List data models
^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.data_models.DataModelsAPI.list

Apply data models
^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.data_models.DataModelsAPI.apply

Delete data models
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.data_models.DataModelsAPI.delete

Data model data classes
^^^^^^^^^^^^^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.data_modeling.data_models
    :members:
    :show-inheritance:

Spaces
------
Retrieve a space by id
^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.spaces.SpacesAPI.retrieve

List spaces
^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.spaces.SpacesAPI.list

Apply spaces
^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.spaces.SpacesAPI.apply

Delete spaces
^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.spaces.SpacesAPI.delete

Data classes
^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.data_modeling.spaces
    :members:
    :show-inheritance:

Views
------------
Retrieve views by id(s)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.views.ViewsAPI.retrieve

List views
^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.views.ViewsAPI.list

Apply view
^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.views.ViewsAPI.apply

Delete views
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.views.ViewsAPI.delete

View data classes
^^^^^^^^^^^^^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.data_modeling.views
    :members:
    :show-inheritance:

Containers
------------
Retrieve containers by id(s)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.containers.ContainersAPI.retrieve

List containers
^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.containers.ContainersAPI.list

Apply containers
^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.containers.ContainersAPI.apply

Delete containers
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.containers.ContainersAPI.delete

Delete constraints
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.containers.ContainersAPI.delete_constraints

Delete indexes
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.containers.ContainersAPI.delete_indexes

Containers data classes
^^^^^^^^^^^^^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.data_modeling.containers
    :members:
    :show-inheritance:

Instances
------------
Retrieve instances by id(s)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.instances.InstancesAPI.retrieve

Retrieve Nodes by id(s)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.instances.InstancesAPI.retrieve_nodes

Retrieve Edges by id(s)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.instances.InstancesAPI.retrieve_edges

List instances
^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.instances.InstancesAPI.list

Apply instances
^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.instances.InstancesAPI.apply

Search instances
^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.instances.InstancesAPI.search

Aggregate instances
^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.instances.InstancesAPI.aggregate

Query instances
^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.instances.InstancesAPI.query

Inspect instances
^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.instances.InstancesAPI.inspect

Sync instances
^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.instances.InstancesAPI.sync
.. automethod:: cognite.client._api.data_modeling.instances.InstancesAPI.subscribe

Example on syncing instances to local sqlite
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
.. code:: python

    import json
    import time
    import sqlite3

    from cognite.client import CogniteClient
    from cognite.client.data_classes.data_modeling.instances import (
        SubscriptionContext,
    )
    from cognite.client.data_classes.data_modeling.query import (
        QueryResult,
        Query,
        NodeResultSetExpression,
        Select,
    )
    from cognite.client.data_classes.filters import Equals

    client = CogniteClient()


    def sqlite_connection(db_name: str) -> sqlite3.Connection:
        return sqlite3.connect(db_name, check_same_thread=False)


    def bootstrap_sqlite(db_name: str) -> None:
        with sqlite_connection(db_name) as connection:
            connection.execute(
                """
                CREATE TABLE IF NOT EXISTS instance (
                    space TEXT,
                    external_id TEXT,
                    data JSON,
                    PRIMARY KEY(space, external_id)
                )
                """
            )
            connection.execute("CREATE TABLE IF NOT EXISTS cursor (cursor TEXT)")


    def sync_space_to_sqlite(
        db_name: str, space_to_sync: str
    ) -> SubscriptionContext:
        with sqlite_connection(db_name) as connection:
            existing_cursor = connection.execute(
                "SELECT cursor FROM cursor"
            ).fetchone()
            if existing_cursor:
                print("Found existing cursor, using that")

        query = Query(
            with_={
                "nodes": NodeResultSetExpression(
                    filter=Equals(property=["node", "space"], value=space_to_sync)
                )
            },
            select={"nodes": Select()},
            cursors={"nodes": existing_cursor[0] if existing_cursor else None},
        )

        def _sync_batch_to_sqlite(result: QueryResult):
            with sqlite_connection(db_name) as connection:
                inserts = []
                deletes = []
                for node in result["nodes"]:
                    if node.deleted_time is None:
                        inserts.append(
                            (node.space, node.external_id, json.dumps(node.dump()))
                        )
                    else:
                        deletes.append((node.space, node.external_id))
                # Updates must be done in the same transaction as persisting the cursor.
                # A transaction is implicitly started by sqlite here.
                #
                # It is also important that deletes happen first as the same (space, external_id)
                # may appear as several tombstones and then a new instance, which must result in
                # the instance being saved.
                connection.executemany(
                    "DELETE FROM instance WHERE space=? AND external_id=?", deletes
                )
                connection.executemany(
                    "INSERT INTO instance VALUES (?, ?, ?) ON CONFLICT DO UPDATE SET data=excluded.data",
                    inserts,
                )
                connection.execute(
                    "INSERT INTO cursor VALUES (?)", [result.cursors["nodes"]]
                )
                connection.commit()
            print(f"Wrote {len(inserts)} nodes and deleted {len(deletes)} nodes")

        return client.data_modeling.instances.subscribe(query, _sync_batch_to_sqlite)


    if __name__ == "__main__":
        SQLITE_DB_NAME = "test.db"
        SPACE_TO_SYNC = "mytestspace"
        bootstrap_sqlite(db_name=SQLITE_DB_NAME)
        sync_space_to_sqlite(db_name=SQLITE_DB_NAME, space_to_sync=SPACE_TO_SYNC)
        while True:
            # Keep main thread alive
            time.sleep(10)


Delete instances
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.instances.InstancesAPI.delete

Instances core data classes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.data_modeling.instances
    :members:
    :show-inheritance:

Instances query data classes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.data_modeling.query
    :members:
    :show-inheritance:

Data Modeling ID data classes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.data_modeling.ids
    :members:
    :show-inheritance:

Statistics
------------

Project
^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.statistics.StatisticsAPI.project

Retrieve space statistics
^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.statistics.SpaceStatisticsAPI.retrieve

List space statistics
^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.statistics.SpaceStatisticsAPI.list

Data modeling statistics data classes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.data_modeling.statistics
    :members:
    :show-inheritance:

GraphQL
-------
Apply DML
^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.graphql.DataModelingGraphQLAPI.apply_dml

Execute GraphQl query
^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_modeling.graphql.DataModelingGraphQLAPI.query

Core Data Model
---------------
.. automodule:: cognite.client.data_classes.data_modeling.cdm.v1
    :members:
    :show-inheritance:

Extractor Extensions
--------------------
.. automodule:: cognite.client.data_classes.data_modeling.extractor_extensions.v1
    :members:
    :show-inheritance:

<!-- SOURCE_END: cognite-sdk-python/docs\source\data_modeling.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\data_organization.rst -->
## File: cognite-sdk-python/docs\source\data_organization.rst

Data Organization
=================

Annotations
-----------

Retrieve an annotation by id
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.annotations.AnnotationsAPI.retrieve

Retrieve multiple annotations by id
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.annotations.AnnotationsAPI.retrieve_multiple

List annotation
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.annotations.AnnotationsAPI.list

Create an annotation
^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.annotations.AnnotationsAPI.create

Suggest an annotation
^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.annotations.AnnotationsAPI.suggest

Update annotations
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.annotations.AnnotationsAPI.update

Delete annotations
^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.annotations.AnnotationsAPI.delete

Annotations Data classes
^^^^^^^^^^^^^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.annotations
    :members:
    :show-inheritance:

Data sets
---------
Retrieve an data set by id
^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_sets.DataSetsAPI.retrieve

Retrieve multiple data sets by id
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_sets.DataSetsAPI.retrieve_multiple

List data sets
^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_sets.DataSetsAPI.list

Aggregate data sets
^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_sets.DataSetsAPI.aggregate

Create data sets
^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_sets.DataSetsAPI.create

Delete data sets
^^^^^^^^^^^^^^^^
This functionality is not yet available in the API.

Update data sets
^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.data_sets.DataSetsAPI.update

Data Sets Data classes
^^^^^^^^^^^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.data_sets
    :members:
    :show-inheritance:

Labels
------

List labels
^^^^^^^^^^^
.. automethod:: cognite.client._api.labels.LabelsAPI.list

Retrieve labels
^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.labels.LabelsAPI.retrieve

Create a label
^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.labels.LabelsAPI.create

Delete labels
^^^^^^^^^^^^^
.. automethod:: cognite.client._api.labels.LabelsAPI.delete

Labels Data classes
^^^^^^^^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.labels
    :members:
    :show-inheritance:

Relationships
-------------
Retrieve a relationship by external id
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.relationships.RelationshipsAPI.retrieve

Retrieve multiple relationships by external id
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.relationships.RelationshipsAPI.retrieve_multiple

List relationships
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.relationships.RelationshipsAPI.list

Create a relationship
^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.relationships.RelationshipsAPI.create

Update relationships
^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.relationships.RelationshipsAPI.update

Upsert relationships
^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.relationships.RelationshipsAPI.upsert

Delete relationships
^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.relationships.RelationshipsAPI.delete

Relationship Data classes
^^^^^^^^^^^^^^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.relationships
    :members:
    :show-inheritance:


<!-- SOURCE_END: cognite-sdk-python/docs\source\data_organization.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\data_workflows.rst -->
## File: cognite-sdk-python/docs\source\data_workflows.rst

Data Workflows
======================

Workflows
------------
Upsert Workflows
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.workflows.WorkflowAPI.upsert

Retrieve Workflows
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.workflows.WorkflowAPI.retrieve

List Workflows
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.workflows.WorkflowAPI.list

Delete Workflows
^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.workflows.WorkflowAPI.delete


Workflow Versions
------------------
Upsert Workflow Versions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.workflows.WorkflowVersionAPI.upsert

Retrieve Workflow Versions
^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.workflows.WorkflowVersionAPI.retrieve

List Workflow Versions
^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.workflows.WorkflowVersionAPI.list

Delete Workflow Versions
^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.workflows.WorkflowVersionAPI.delete


Workflow Executions
--------------------
Run Workflow Execution
^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.workflows.WorkflowExecutionAPI.run

Retrieve detailed Workflow Execution
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.workflows.WorkflowExecutionAPI.retrieve_detailed

List Workflow Executions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.workflows.WorkflowExecutionAPI.list

Cancel Workflow Execution
^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.workflows.WorkflowExecutionAPI.cancel

Retry Workflow Execution
^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.workflows.WorkflowExecutionAPI.retry


Workflow Tasks
------------------
Update status of async Task
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.workflows.WorkflowTaskAPI.update


Workflow Triggers
-------------------
Upsert Trigger
^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.workflows.WorkflowTriggerAPI.upsert

List Triggers
^^^^^^^^^^^^^
.. automethod:: cognite.client._api.workflows.WorkflowTriggerAPI.list

List runs for a Trigger
^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.workflows.WorkflowTriggerAPI.list_runs

Delete Triggers
^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.workflows.WorkflowTriggerAPI.delete


Data Workflows data classes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.workflows
    :members:
    :show-inheritance:


<!-- SOURCE_END: cognite-sdk-python/docs\source\data_workflows.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\deprecated.rst -->
## File: cognite-sdk-python/docs\source\deprecated.rst

Deprecated
==========

Templates
---------
Create Template groups
^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.templates.TemplateGroupsAPI.create

Upsert Template groups
^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.templates.TemplateGroupsAPI.upsert

Retrieve Template groups
^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.templates.TemplateGroupsAPI.retrieve_multiple

List Template groups
^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.templates.TemplateGroupsAPI.list

Delete Template groups
^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.templates.TemplateGroupsAPI.delete

Upsert a Template group version
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.templates.TemplateGroupVersionsAPI.upsert

List Temple Group versions
^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.templates.TemplateGroupVersionsAPI.list

Delete a Temple Group version
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.templates.TemplateGroupVersionsAPI.delete

Run a GraphQL query
^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.templates.TemplatesAPI.graphql_query

Create Template instances
^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.templates.TemplateInstancesAPI.create

Upsert Template instances
^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.templates.TemplateInstancesAPI.upsert

Update Template instances
^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.templates.TemplateInstancesAPI.update

Retrieve Template instances
^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.templates.TemplateInstancesAPI.retrieve_multiple

List Template instances
^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.templates.TemplateInstancesAPI.list

Delete Template instances
^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.templates.TemplateInstancesAPI.delete

Create Template Views
^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.templates.TemplateViewsAPI.create

Upsert Template Views
^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.templates.TemplateViewsAPI.upsert

List Template Views
^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.templates.TemplateViewsAPI.list

Resolve Template View
^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.templates.TemplateViewsAPI.resolve

Delete Template Views
^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.templates.TemplateViewsAPI.delete


<!-- SOURCE_END: cognite-sdk-python/docs\source\deprecated.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\documents.rst -->
## File: cognite-sdk-python/docs\source\documents.rst

Documents
===============
Documents API
---------------
Aggregate Document Count
^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.documents.DocumentsAPI.aggregate_count

Aggregate Document Value Cardinality
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.documents.DocumentsAPI.aggregate_cardinality_values

Aggregate Document Property Cardinality
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.documents.DocumentsAPI.aggregate_cardinality_properties

Aggregate Document Unique Values
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.documents.DocumentsAPI.aggregate_unique_values

Aggregate Document Unique Properties
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.documents.DocumentsAPI.aggregate_unique_properties

List Documents
^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.documents.DocumentsAPI.list

Retrieve Document Content
^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.documents.DocumentsAPI.retrieve_content

Retrieve Document Content Buffer
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.documents.DocumentsAPI.retrieve_content_buffer

Search Documents
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.documents.DocumentsAPI.search


Documents classes
^^^^^^^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.documents
    :members:
    :show-inheritance:

Preview
---------
Download Image Preview Bytes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.documents.DocumentPreviewAPI.download_page_as_png_bytes

Download Image Preview to Path
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.documents.DocumentPreviewAPI.download_page_as_png

Download PDF Preview Bytes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.documents.DocumentPreviewAPI.download_document_as_pdf_bytes

Download PDF Preview to Path
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.documents.DocumentPreviewAPI.download_document_as_pdf

Retrieve PDF Preview Temporary Link
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.documents.DocumentPreviewAPI.retrieve_pdf_link



<!-- SOURCE_END: cognite-sdk-python/docs\source\documents.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\events.rst -->
## File: cognite-sdk-python/docs\source\events.rst

Events
------
Retrieve an event by id
^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.events.EventsAPI.retrieve

Retrieve multiple events by id
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.events.EventsAPI.retrieve_multiple

List events
^^^^^^^^^^^
.. automethod:: cognite.client._api.events.EventsAPI.list

Aggregate events
^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.events.EventsAPI.aggregate

Aggregate Event Count
^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.events.EventsAPI.aggregate_count

Aggregate Event Values Cardinality
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.events.EventsAPI.aggregate_cardinality_values

Aggregate Event Property Cardinality
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.events.EventsAPI.aggregate_cardinality_properties

Aggregate Event Unique Values
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.events.EventsAPI.aggregate_unique_values

Aggregate Event Unique Properties
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.events.EventsAPI.aggregate_unique_properties

Search for events
^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.events.EventsAPI.search

Create events
^^^^^^^^^^^^^
.. automethod:: cognite.client._api.events.EventsAPI.create

Delete events
^^^^^^^^^^^^^
.. automethod:: cognite.client._api.events.EventsAPI.delete

Update events
^^^^^^^^^^^^^
.. automethod:: cognite.client._api.events.EventsAPI.update

Upsert events
^^^^^^^^^^^^^
.. automethod:: cognite.client._api.events.EventsAPI.upsert

Filter events
^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.events.EventsAPI.filter

Events Data classes
^^^^^^^^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.events
    :members:
    :show-inheritance:


<!-- SOURCE_END: cognite-sdk-python/docs\source\events.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\exceptions.rst -->
## File: cognite-sdk-python/docs\source\exceptions.rst

Exceptions
==========
CogniteAPIError
^^^^^^^^^^^^^^^
.. autoexception:: cognite.client.exceptions.CogniteAPIError

CogniteNotFoundError
^^^^^^^^^^^^^^^^^^^^
.. autoexception:: cognite.client.exceptions.CogniteNotFoundError

CogniteDuplicatedError
^^^^^^^^^^^^^^^^^^^^^^
.. autoexception:: cognite.client.exceptions.CogniteDuplicatedError

CogniteImportError
^^^^^^^^^^^^^^^^^^
.. autoexception:: cognite.client.exceptions.CogniteImportError

CogniteMissingClientError
^^^^^^^^^^^^^^^^^^^^^^^^^
.. autoexception:: cognite.client.exceptions.CogniteMissingClientError

<!-- SOURCE_END: cognite-sdk-python/docs\source\exceptions.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\extensions_and_optional_dependencies.rst -->
## File: cognite-sdk-python/docs\source\extensions_and_optional_dependencies.rst

Extensions and optional dependencies
====================================
Pandas integration
------------------
The SDK is tightly integrated with the `pandas <https://pandas.pydata.org/pandas-docs/stable/>`_ library.
You can use the :code:`.to_pandas()` method on pretty much any object and get a pandas data frame describing the data.

This is particularly useful when you are working with time series data and with tabular data from the Raw API.

How to install extra dependencies
---------------------------------
If your application requires the functionality from e.g. the :code:`pandas`, :code:`sympy`, or :code:`geopandas` dependencies,
you should install the SDK along with its optional dependencies. The available extras are:

- numpy: numpy
- pandas: pandas
- geo: geopanda, shapely
- sympy: sympy
- functions: pip
- all (will install dependencies for all the above)

These can be installed with the following command:

pip

.. code:: bash

    $ pip install "cognite-sdk[pandas, geo]"

poetry

.. code:: bash

    $ poetry add cognite-sdk -E pandas -E geo


<!-- SOURCE_END: cognite-sdk-python/docs\source\extensions_and_optional_dependencies.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\files.rst -->
## File: cognite-sdk-python/docs\source\files.rst

Files
=====
Retrieve file metadata by id
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.files.FilesAPI.retrieve

Retrieve multiple files' metadata by id
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.files.FilesAPI.retrieve_multiple

List files metadata
^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.files.FilesAPI.list

Aggregate files metadata
^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.files.FilesAPI.aggregate

Search for files
^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.files.FilesAPI.search

Create file metadata
^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.files.FilesAPI.create

Upload a file or directory
^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.files.FilesAPI.upload

Upload a string or bytes
^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.files.FilesAPI.upload_bytes

Upload a file content
^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.files.FilesAPI.upload_content

Upload a file content in string or bytes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.files.FilesAPI.upload_content_bytes

Upload a file in multiple parts
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.files.FilesAPI.multipart_upload_session

Upload a file content in multiple parts
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.files.FilesAPI.multipart_upload_content_session

Retrieve download urls
^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.files.FilesAPI.retrieve_download_urls

Download files to disk
^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.files.FilesAPI.download

Download a single file to a specific path
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.files.FilesAPI.download_to_path

Download a file as bytes
^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.files.FilesAPI.download_bytes

Delete files
^^^^^^^^^^^^
.. automethod:: cognite.client._api.files.FilesAPI.delete

Update files metadata
^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.files.FilesAPI.update

Files Data classes
^^^^^^^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.files
    :members:
    :show-inheritance:


<!-- SOURCE_END: cognite-sdk-python/docs\source\files.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\filters.rst -->
## File: cognite-sdk-python/docs\source\filters.rst

Filters
=======

The filter language provides a set of classes that can be used to construct complex
queries for filtering data. Each filter class represents a specific filtering criterion,
allowing users to tailor their queries to their specific needs.

The filter classes can be shared both by the classic CDF resources (like Assets, Time Series, Events, Files etc) and Data Modelling (Views and Instances).

When filtering on Data Modelling, the filter can be used on any property. These can be referenced directly with a tuple notation like:
``('space', 'view_external_id/view_version', 'property')``,
or, which is usually more convenient, one can use the ``as_property_ref`` method on the View or ViewID object like:
``myView.as_property_ref('property')``.

**Base Instance Properties:** Certain properties like ``externalId``, ``space``, ``createdTime``, and ``lastUpdatedTime`` are base properties on the instance itself (not in a container/view).
To filter on these, use the syntax ``("node", "property_name")`` or ``("edge", "property"name)``. For example:

.. code-block:: python

    from cognite.client.data_classes.filters import Prefix, Equals

    # Filter nodes where externalId starts with "SomeCustomer"
    flt = Prefix(("node", "externalId"), "SomeCustomer")
    
    # Filter edges in a specific space
    flt = Equals(("edge", "space"), "my_space")

All filters inherit from the base class ``Filter`` (``cognite.client.data_classes.filters.Filter``).

Below is an overview of the available filters:


Logical Operators
-----------------

And
^^^
The `And` filter performs a logical AND operation on multiple filters, requiring all specified
conditions to be true for an item to match the filter.

.. autoclass:: cognite.client.data_classes.filters.And
    :members:
    :member-order: bysource

Not
^^^
The `Not` filter performs a logical NOT operation on a sub-filter.

.. autoclass:: cognite.client.data_classes.filters.Not
    :members:
    :member-order: bysource

Or
^^^
The `Or` filter performs a logical OR operation on multiple filters, requiring at least one
specified condition to be true for an item to match the filter.

.. autoclass:: cognite.client.data_classes.filters.Or
    :members:
    :member-order: bysource

Standard Filters
----------------
Equals
^^^^^^
The `Equals` filter checks if a property is equal to a specified value.

.. autoclass:: cognite.client.data_classes.filters.Equals
    :members:
    :member-order: bysource

Exists
^^^^^^
The `Exists` filter checks if a property exists.

.. autoclass:: cognite.client.data_classes.filters.Exists
    :members:
    :member-order: bysource

ContainsAll
^^^^^^^^^^^
The `ContainsAll` filter checks if an item contains all specified values for a given property.

.. autoclass:: cognite.client.data_classes.filters.ContainsAll
    :members:
    :member-order: bysource

ContainsAny
^^^^^^^^^^^
The `ContainsAny` filter checks if an item contains any of the specified values for a given property.

.. autoclass:: cognite.client.data_classes.filters.ContainsAny
    :members:
    :member-order: bysource

In
^^
The `In` filter checks if a property's value is in a specified list.

.. autoclass:: cognite.client.data_classes.filters.In
    :members:
    :member-order: bysource

Overlaps
^^^^^^^^
The `Overlaps` filter checks if two ranges overlap.

.. autoclass:: cognite.client.data_classes.filters.Overlaps
    :members:
    :member-order: bysource

Prefix
^^^^^^
The `Prefix` filter checks if a string property starts with a specified prefix.

.. autoclass:: cognite.client.data_classes.filters.Prefix
    :members:
    :member-order: bysource

Range
^^^^^
The `Range` filter checks if a numeric property's value is within a specified range that can be
open-ended.

.. autoclass:: cognite.client.data_classes.filters.Range
    :members:
    :member-order: bysource

Search
^^^^^^
The `Search` filter performs a full-text search on a specified property.

.. autoclass:: cognite.client.data_classes.filters.Search
    :members:
    :member-order: bysource

InAssetSubtree
^^^^^^^^^^^^^^
The `InAssetSubtree` filter checks if an item belongs to a specified asset subtree.

.. autoclass:: cognite.client.data_classes.filters.InAssetSubtree
    :members:
    :member-order: bysource


Data Modeling-Specific Filters
------------------------------
SpaceFilter
^^^^^^^^^^^^^^
The `SpaceFilter` filters instances from one or more specific space(s).

.. autoclass:: cognite.client.data_classes.filters.SpaceFilter
    :members:
    :member-order: bysource

IsNull
^^^^^^
The `IsNull` filter checks if a property is null.

.. autoclass:: cognite.client.data_classes.filters.IsNull
    :members:
    :member-order: bysource

HasData
^^^^^^^
The `HasData` filter checks if an instance has data for a given property.

.. autoclass:: cognite.client.data_classes.filters.HasData
    :members:
    :member-order: bysource

InstanceReferences
^^^^^^^^^^^^^^^^^^
The `InstanceReferences` filter matches instances with fully qualified references
(space + externalId) equal to _any_ of the provided references.

.. autoclass:: cognite.client.data_classes.filters.InstanceReferences
    :members:
    :member-order: bysource

MatchAll
^^^^^^^^
The `MatchAll` filter matches all instances.

.. autoclass:: cognite.client.data_classes.filters.MatchAll
    :members:
    :member-order: bysource

Nested
^^^^^^
The `Nested` filter applies a filter to the node pointed to by a direct relation property.

.. autoclass:: cognite.client.data_classes.filters.Nested
    :members:
    :member-order: bysource

Geo Filters
-----------
GeoJSON
^^^^^^^
The `GeoJSON` filter performs geometric queries using GeoJSON representations.

.. autoclass:: cognite.client.data_classes.filters.GeoJSON
    :members:
    :member-order: bysource

GeoJSONDisjoint
^^^^^^^^^^^^^^^
The `GeoJSONDisjoint` filter checks if two geometric shapes are disjoint.

.. autoclass:: cognite.client.data_classes.filters.GeoJSONDisjoint
    :members:
    :member-order: bysource

GeoJSONIntersects
^^^^^^^^^^^^^^^^^
The `GeoJSONIntersects` filter checks if two geometric shapes intersect.

.. autoclass:: cognite.client.data_classes.filters.GeoJSONIntersects
    :members:
    :member-order: bysource

GeoJSONWithin
^^^^^^^^^^^^^
The `GeoJSONWithin` filter checks if one geometric shape is within another.

.. autoclass:: cognite.client.data_classes.filters.GeoJSONWithin
    :members:
    :member-order: bysource


<!-- SOURCE_END: cognite-sdk-python/docs\source\filters.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\functions.rst -->
## File: cognite-sdk-python/docs\source\functions.rst

Functions
=========
Functions API
-------------
Create function
^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.functions.FunctionsAPI.create

Delete function
^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.functions.FunctionsAPI.delete

List functions
^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.functions.FunctionsAPI.list

Retrieve function
^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.functions.FunctionsAPI.retrieve

Retrieve multiple functions
^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.functions.FunctionsAPI.retrieve_multiple

Call function
^^^^^^^^^^^^^
.. automethod:: cognite.client._api.functions.FunctionsAPI.call


Function calls
--------------
List function calls
^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.functions.FunctionCallsAPI.list

Retrieve function call
^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.functions.FunctionCallsAPI.retrieve

Retrieve function call response
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.functions.FunctionCallsAPI.get_response

Retrieve function call logs
^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.functions.FunctionCallsAPI.get_logs

Function schedules
------------------
Retrieve function schedule
^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.functions.FunctionSchedulesAPI.retrieve

List function schedules
^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.functions.FunctionSchedulesAPI.list

Create function schedule
^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.functions.FunctionSchedulesAPI.create

Delete function schedule
^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.functions.FunctionSchedulesAPI.delete

Get function schedule input data
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.functions.FunctionSchedulesAPI.get_input_data

Data classes
^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.functions
    :members:
    :show-inheritance:


<!-- SOURCE_END: cognite-sdk-python/docs\source\functions.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\geospatial.rst -->
## File: cognite-sdk-python/docs\source\geospatial.rst

Geospatial
==========
.. note::
   Check https://github.com/cognitedata/geospatial-examples for some complete examples.

Feature types
-------------
Create feature types
^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.geospatial.GeospatialAPI.create_feature_types

Delete feature types
^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.geospatial.GeospatialAPI.delete_feature_types

List feature types
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.geospatial.GeospatialAPI.list_feature_types

Retrieve feature types
^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.geospatial.GeospatialAPI.retrieve_feature_types

Update feature types
^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.geospatial.GeospatialAPI.patch_feature_types

Features
--------
Create features
^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.geospatial.GeospatialAPI.create_features

Delete features
^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.geospatial.GeospatialAPI.delete_features

Retrieve features
^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.geospatial.GeospatialAPI.retrieve_features

Update features
^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.geospatial.GeospatialAPI.update_features

List features
^^^^^^^^^^^^^
.. automethod:: cognite.client._api.geospatial.GeospatialAPI.list_features

Search features
^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.geospatial.GeospatialAPI.search_features

Stream features
^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.geospatial.GeospatialAPI.stream_features

Aggregate features
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.geospatial.GeospatialAPI.aggregate_features

Reference systems
-----------------
Get coordinate reference systems
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.geospatial.GeospatialAPI.get_coordinate_reference_systems

List coordinate reference systems
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.geospatial.GeospatialAPI.list_coordinate_reference_systems

Create coordinate reference systems
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.geospatial.GeospatialAPI.create_coordinate_reference_systems


Raster data
-----------
Put raster data
^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.geospatial.GeospatialAPI.put_raster

Delete raster data
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.geospatial.GeospatialAPI.delete_raster

Get raster data
^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.geospatial.GeospatialAPI.get_raster

Compute
^^^^^^^
.. automethod:: cognite.client._api.geospatial.GeospatialAPI.compute

Geospatial Data classes
-----------------------
.. automodule:: cognite.client.data_classes.geospatial
    :members:
    :show-inheritance:

<!-- SOURCE_END: cognite-sdk-python/docs\source\geospatial.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\hosted_extractors.rst -->
## File: cognite-sdk-python/docs\source\hosted_extractors.rst

Hosted Extractors
=================

Sources
-------
Create new source
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.hosted_extractors.SourcesAPI.create

Delete source
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.hosted_extractors.SourcesAPI.delete

List sources
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.hosted_extractors.SourcesAPI.list

Retrieve sources
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.hosted_extractors.SourcesAPI.retrieve

Update sources
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.hosted_extractors.SourcesAPI.update

Jobs
-------
Create new job
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.hosted_extractors.JobsAPI.create

Delete job
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.hosted_extractors.JobsAPI.delete

List jobs
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.hosted_extractors.JobsAPI.list

Retrieve jobs
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.hosted_extractors.JobsAPI.retrieve

Update job
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.hosted_extractors.JobsAPI.update

List job logs
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.hosted_extractors.JobsAPI.list_logs

List job metrics
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.hosted_extractors.JobsAPI.list_metrics


Destinations
-------------
Create new destinations
^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.hosted_extractors.DestinationsAPI.create

Delete destinations
^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.hosted_extractors.DestinationsAPI.delete

List destinations
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.hosted_extractors.DestinationsAPI.list

Retrieve destinations
^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.hosted_extractors.DestinationsAPI.retrieve

Update destinations
^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.hosted_extractors.DestinationsAPI.update

Mappings
-------------
Create new mappings
^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.hosted_extractors.MappingsAPI.create

Delete mappings
^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.hosted_extractors.MappingsAPI.delete

List mappings
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.hosted_extractors.MappingsAPI.list

Retrieve mappings
^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.hosted_extractors.MappingsAPI.retrieve

Update mappings
^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.hosted_extractors.MappingsAPI.update


<!-- SOURCE_END: cognite-sdk-python/docs\source\hosted_extractors.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\identity_and_access_management.rst -->
## File: cognite-sdk-python/docs\source\identity_and_access_management.rst

Identity and access management
==============================
Compare access rights (capabilities)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Verify my capabilities
~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.iam.IAMAPI.verify_capabilities

Compare capabilities
~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.iam.IAMAPI.compare_capabilities

Principals
^^^^^^^^^^^

Get the current caller's principal
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.org_apis.principals.PrincipalsAPI.me

Retrieve a principal
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.org_apis.principals.PrincipalsAPI.retrieve

List principals
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.org_apis.principals.PrincipalsAPI.list

Tokens
^^^^^^
Inspect the token currently used by the client
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.iam.TokenAPI.inspect

Groups
^^^^^^
List groups
~~~~~~~~~~~
.. automethod:: cognite.client._api.iam.GroupsAPI.list

Create groups
~~~~~~~~~~~~~
.. automethod:: cognite.client._api.iam.GroupsAPI.create

Delete groups
~~~~~~~~~~~~~
.. automethod:: cognite.client._api.iam.GroupsAPI.delete


Security categories
^^^^^^^^^^^^^^^^^^^
List security categories
~~~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.iam.SecurityCategoriesAPI.list

Create security categories
~~~~~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.iam.SecurityCategoriesAPI.create

Delete security categories
~~~~~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.iam.SecurityCategoriesAPI.delete


Sessions
^^^^^^^^^^^^^^^^^^^
List sessions
~~~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.iam.SessionsAPI.list

Create a session
~~~~~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.iam.SessionsAPI.create

Retrieve a session
~~~~~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.iam.SessionsAPI.retrieve

Revoke a session
~~~~~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.iam.SessionsAPI.revoke


User Profiles
^^^^^^^^^^^^^^^^^^^
Enable user profiles for project
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.iam.UserProfilesAPI.enable

Disable user profiles for project
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.iam.UserProfilesAPI.disable

Get my own user profile
~~~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.iam.UserProfilesAPI.me

List user profiles
~~~~~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.iam.UserProfilesAPI.list

Retrieve one or more user profiles
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.iam.UserProfilesAPI.retrieve

Search for user profiles
~~~~~~~~~~~~~~~~~~~~~~~~~~
.. automethod:: cognite.client._api.iam.UserProfilesAPI.search


Data classes
^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.iam
    :members:
    :show-inheritance:

.. automodule:: cognite.client.data_classes.user_profiles
    :members:
    :show-inheritance:

.. automodule:: cognite.client.data_classes.principals
    :members:
    :show-inheritance:


<!-- SOURCE_END: cognite-sdk-python/docs\source\identity_and_access_management.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\index.rst -->
## File: cognite-sdk-python/docs\source\index.rst

.. cognite-sdk documentation master file, created by
   sphinx-quickstart on Thu Jan 11 15:57:44 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Cognite Python SDK Documentation
================================

This is the Cognite Python SDK for developers and data scientists working with Cognite Data Fusion (CDF). The package is tightly integrated with pandas, and helps you work easily and efficiently with data in Cognite Data Fusion (CDF).

.. contents::
   :local:

Installation
^^^^^^^^^^^^
To install this package:

.. code-block:: bash

   pip install cognite-sdk

To upgrade the version of this package:

.. code-block:: bash

   pip install cognite-sdk --upgrade

.. code-block:: bash

   pip install "cognite-sdk[pandas, geo]"


Contents
^^^^^^^^
.. toctree::
   quickstart
   settings
   credential_providers
   cognite_client
   extensions_and_optional_dependencies
   identity_and_access_management
   data_modeling
   agents
   ai
   assets
   events
   files
   time_series
   sequences
   geospatial
   3d
   contextualization
   documents
   data_ingestion
   hosted_extractors
   postgres_gateway
   data_organization
   transformations
   functions
   simulators
   data_workflows
   unit_catalog
   filters
   deprecated
   base_data_classes
   exceptions
   utils
   testing
   appendix


<!-- SOURCE_END: cognite-sdk-python/docs\source\index.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\postgres_gateway.rst -->
## File: cognite-sdk-python/docs\source\postgres_gateway.rst

Postgres Gateway
=================
Postgres Gateway Users API
---------------------------
Create Postgres Gateway Users
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.postgres_gateway.UsersAPI.create

Update Postgres Gateway Users
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.postgres_gateway.UsersAPI.update

Delete Postgres Gateway Users
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.postgres_gateway.UsersAPI.delete

Retrieve Postgres Gateway Users
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.postgres_gateway.UsersAPI.retrieve

List Postgres Gateway Users
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.postgres_gateway.UsersAPI.list

Postgres Gateway Tables API
-----------------------------
Create Postgres Gateway Tables
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.postgres_gateway.TablesAPI.create

Delete Postgres Gateway Tables
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.postgres_gateway.TablesAPI.delete

Retrieve Postgres Gateway Tables
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.postgres_gateway.TablesAPI.retrieve

List Postgres Gateway Tables
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.postgres_gateway.TablesAPI.list


Postgres Gateway classes
^^^^^^^^^^^^^^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.postgres_gateway
    :members:
    :show-inheritance:


<!-- SOURCE_END: cognite-sdk-python/docs\source\postgres_gateway.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\quickstart.rst -->
## File: cognite-sdk-python/docs\source\quickstart.rst

Quickstart
==========

There are multiple ways that a CogniteClient can be configured to authenticate with Cognite Data Fusion (CDF). For the purpose of
this quickstart we'll demonstrate the most common/recommended patterns. More details and usage examples can be found in each respective
section: :ref:`CogniteClient <class_client_CogniteClient>`, :ref:`ClientConfig <class_client_ClientConfig>`,
:ref:`GlobalConfig <class_client_GlobalConfig>`, and :ref:`credential_providers:Credential Providers`.

.. warning::
    Ensure that credentials are stored and handled securely by not hard-coding it or storing them in a text file. All the below examples
    are using and referencing environment variables to store this sensitive information.

Instantiate a new client from a configuration file
--------------------------------------------------
Use this code to instantiate a client using a configuration file in order to execute API calls to Cognite Data Fusion (CDF).

.. note::
    How you read in the configuration file is up to you as the :ref:`CogniteClient <class_client_CogniteClient>` load method
    accepts both a dictionary and a YAML/JSON string. So for the purposes of this example, we will use the yaml library to read in a yaml file and
    substitute environment variables in the file string to ensure that sensitive information is not stored in the file.

See :ref:`CogniteClient <class_client_CogniteClient>`, :ref:`ClientConfig <class_client_ClientConfig>`,
:ref:`GlobalConfig <class_client_GlobalConfig>`, and :ref:`credential_providers:Credential Providers`
for more information on the configuration options.

.. code:: yaml

    # cognite-sdk-config.yaml
    client:
      project: "my-project"
      client_name: "my-special-client"
      base_url: "https://${MY_CLUSTER}.cognitedata.com"
      credentials:
        client_credentials:
          token_url: "https://login.microsoftonline.com/${MY_TENANT_ID}/oauth2/v2.0/token"
          client_id: "${MY_CLIENT_ID}"
          client_secret: "${MY_CLIENT_SECRET}"
          scopes: ["https://api.cognitedata.com/.default"]
    global:
      max_retries: 10
      max_retry_backoff: 10

.. testsetup:: client_config_file

    >>> getfixture("set_envs")  # Fixture defined in conftest.py
    >>> getfixture("quickstart_client_config_file")  # Fixture defined in conftest.py

.. doctest:: client_config_file

    >>> import os
    >>> from pathlib import Path
    >>> from string import Template

    >>> import yaml

    >>> from cognite.client import CogniteClient, global_config

    >>> file_path = Path("cognite-sdk-config.yaml")

    >>> # Read in yaml file and substitute environment variables in the file string
    >>> env_sub_template = Template(file_path.read_text())
    >>> file_env_parsed = env_sub_template.substitute(dict(os.environ))

    >>> # Load yaml file string into a dictionary to parse global and client configurations
    >>> cognite_config = yaml.safe_load(file_env_parsed)

    >>> # If you want to set a global configuration it must be done before creating the client
    >>> global_config.apply_settings(cognite_config["global"])
    >>> client = CogniteClient.load(cognite_config["client"])

.. testcode:: client_config_file
    :hide:

    >>> global_config.max_retries
    10
    >>> global_config.max_retry_backoff
    10
    >>> client.config.project
    'my-project'
    >>> client.config.client_name
    'my-special-client'
    >>> client.config.credentials.client_id
    'my-client-id'
    >>> client.config.credentials.client_secret
    'my-client-secret'
    >>> client.config.credentials.token_url
    'https://login.microsoftonline.com/my-tenant-id/oauth2/v2.0/token'
    >>> client.config.credentials.scopes
    ['https://api.cognitedata.com/.default']

Instantiate a new client using ClientConfig
-------------------------------------------

Use this code to instantiate a client using the ClientConfig and global_config in order to execute API calls to Cognite Data Fusion (CDF).

Use this code to instantiate a client in order to execute API calls to Cognite Data Fusion (CDF).
The :code:`client_name` is a user-defined string intended to give the client a unique identifier. You
can provide the :code:`client_name` by passing it directly to the :ref:`ClientConfig <class_client_ClientConfig>` constructor.

The Cognite API uses OpenID Connect (OIDC) to authenticate.
Use one of the credential providers such as OAuthClientCredentials to authenticate:

.. note::
    The following example sets a global client configuration which will be used if no config is
    explicitly passed to :ref:`cognite_client:CogniteClient`.
    All examples in this documentation going forward assume that such a global configuration has been set.

.. testsetup:: client_config

    >>> getfixture("set_envs")  # Fixture defined in conftest.py

.. doctest:: client_config

    >>> from cognite.client import CogniteClient, ClientConfig, global_config
    >>> from cognite.client.credentials import OAuthClientCredentials

    >>> # This value will depend on the cluster your CDF project runs on
    >>> cluster = "api"
    >>> base_url = f"https://{cluster}.cognitedata.com"
    >>> tenant_id = "my-tenant-id"
    >>> client_id = "my-client-id"
    >>> # client secret should not be stored in-code, so we load it from an environment variable
    >>> client_secret = os.environ["MY_CLIENT_SECRET"]
    >>> creds = OAuthClientCredentials(
    ...   token_url=f"https://login.microsoftonline.com/{tenant_id}/oauth2/v2.0/token",
    ...   client_id=client_id,
    ...   client_secret=client_secret,
    ...   scopes=[f"{base_url}/.default"]
    ... )

    >>> cnf = ClientConfig(
    ...   client_name="my-special-client",
    ...   base_url=base_url,
    ...   project="my-project",
    ...   credentials=creds
    ... )

    >>> global_config.default_client_config = cnf
    >>> client = CogniteClient()

.. testcode:: client_config
    :hide:

    >>> client.config.project
    'my-project'
    >>> client.config.client_name
    'my-special-client'
    >>> client.config.credentials.client_id
    'my-client-id'
    >>> client.config.credentials.client_secret
    'my-client-secret'
    >>> client.config.credentials.token_url
    'https://login.microsoftonline.com/my-tenant-id/oauth2/v2.0/token'
    >>> client.config.credentials.scopes
    ['https://api.cognitedata.com/.default']


Examples for all OAuth credential providers can be found in the :ref:`credential_providers:Credential Providers` section.

You can also make your own credential provider:

.. code:: python

    from cognite.client import CogniteClient, ClientConfig
    from cognite.client.credentials import Token

    def token_provider():
        ...

    cnf = ClientConfig(
      client_name="my-special-client",
      base_url="https://<cluster>.cognitedata.com",
      project="my-project",
      credentials=Token(token_provider)
    )
    client = CogniteClient(cnf)

Discover time series
--------------------
For this, you will need to supply ids for the time series that you want to retrieve. You can find
some ids by listing the available time series. Limits for listing resources default to 25, so
the following code will return the first 25 time series resources.

.. code:: python

    from cognite.client import CogniteClient

    client = CogniteClient()
    ts_list = client.time_series.list()

List available spaces in your Data Modeling project
---------------------------------------------------
In the following example, we list all spaces in the project.

.. code:: python

    from cognite.client import CogniteClient

    client = CogniteClient()
    spaces = client.data_modeling.spaces.list()


<!-- SOURCE_END: cognite-sdk-python/docs\source\quickstart.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\sequences.rst -->
## File: cognite-sdk-python/docs\source\sequences.rst

Sequences
=========

Metadata
--------
Retrieve a sequence by id
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.sequences.SequencesAPI.retrieve

Retrieve multiple sequences by id
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.sequences.SequencesAPI.retrieve_multiple

List sequences
^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.sequences.SequencesAPI.list

Aggregate sequences
^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.sequences.SequencesAPI.aggregate

Aggregate Sequences Count
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.sequences.SequencesAPI.aggregate_count

Aggregate Sequences Value Cardinality
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.sequences.SequencesAPI.aggregate_cardinality_values

Aggregate Sequences Property Cardinality
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.sequences.SequencesAPI.aggregate_cardinality_properties

Aggregate Sequences Unique Values
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.sequences.SequencesAPI.aggregate_unique_values

Aggregate Sequences Unique Properties
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.sequences.SequencesAPI.aggregate_unique_properties

Search for sequences
^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.sequences.SequencesAPI.search

Create a sequence
^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.sequences.SequencesAPI.create

Delete sequences
^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.sequences.SequencesAPI.delete

Filter sequences
^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.sequences.SequencesAPI.filter


Update sequences
^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.sequences.SequencesAPI.update

Upsert sequences
^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.sequences.SequencesAPI.upsert


Rows
----
Retrieve rows
^^^^^^^^^^^^^
.. automethod:: cognite.client._api.sequences.SequencesDataAPI.retrieve

Retrieve rows in a pandas dataframe
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.sequences.SequencesDataAPI.retrieve_dataframe

Retrieve last row
^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.sequences.SequencesDataAPI.retrieve_last_row

Insert rows into a sequence
^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.sequences.SequencesDataAPI.insert

Insert a pandas dataframe into a sequence
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.sequences.SequencesDataAPI.insert_dataframe

Delete rows from a sequence
^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.sequences.SequencesDataAPI.delete

Delete a range of rows from a sequence
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.sequences.SequencesDataAPI.delete_range

Sequence Data classes
^^^^^^^^^^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.sequences
    :members:
    :show-inheritance:
    :exclude-members: Sequence

    .. autoclass:: Sequence
        :noindex:


<!-- SOURCE_END: cognite-sdk-python/docs\source\sequences.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\settings.rst -->
## File: cognite-sdk-python/docs\source\settings.rst

Settings
========
Client configuration
--------------------
You can pass configuration arguments directly to the :ref:`ClientConfig <class_client_ClientConfig>` constructor, for example
to configure the base url of your requests or any additional headers. For a list of all configuration arguments,
see the :ref:`ClientConfig <class_client_ClientConfig>` class definition.

To initialise a ``CogniteClient``, simply pass this configuration object, (an instance of ``ClientConfig``) to it:

.. code:: python

    from cognite.client import CogniteClient, ClientConfig
    from cognite.client.credentials import Token
    my_config = ClientConfig(
        client_name="my-client",
        project="myproj",
        credentials=Token("verysecret"),
    )
    client = CogniteClient(my_config)

Global configuration
--------------------
You can set global configuration options like this:

.. code:: python

    from cognite.client import global_config, ClientConfig
    from cognite.client.credentials import Token
    global_config.default_client_config = ClientConfig(
        client_name="my-client", project="myproj", credentials=Token("verysecret")
    )
    global_config.disable_pypi_version_check = True
    global_config.disable_gzip = False
    global_config.disable_ssl = False
    global_config.max_retries = 10
    global_config.max_retry_backoff = 10
    global_config.max_connection_pool_size = 10
    global_config.status_forcelist = {429, 502, 503, 504}

These must be set prior to instantiating a ``CogniteClient`` in order for them to take effect.

Concurrency and connection pooling
----------------------------------
This library does not expose API limits to the user. If your request exceeds API limits, the SDK splits your
request into chunks and performs the sub-requests in parallel. To control how many concurrent requests you send
to the API, you can either pass the :code:`max_workers` attribute to :ref:`ClientConfig <class_client_ClientConfig>` before
you instantiate the :ref:`cognite_client:CogniteClient` from it, or set the global :code:`max_workers` config option.

If you are working with multiple instances of :ref:`cognite_client:CogniteClient`, all instances will share the same connection pool.
If you have several instances, you can increase the max connection pool size to reuse connections if you are performing a large amount of concurrent requests.
You can increase the max connection pool size by setting the :code:`max_connection_pool_size` config option.

Debug logging
-------------
If you need to know the details of the http requests the SDK sends under the hood, you can enable debug logging. One way
is to pass :code:`debug=True` argument to :ref:`ClientConfig <class_client_ClientConfig>`. Alternatively, you can toggle debug
logging on and off by setting the :code:`debug` attribute on the :ref:`ClientConfig <class_client_ClientConfig>` object.

.. code:: python

    from cognite.client import CogniteClient, ClientConfig
    from cognite.client.credentials import Token
    client = CogniteClient(
        ClientConfig(
            client_name="my-client",
            project="myproj",
            credentials=Token("verysecret"),
            debug=True,
        )
    )
    print(client.config.debug)   # True, all http request details will be logged
    client.config.debug = False  # disable debug logging
    client.config.debug = True   # enable debug logging again

HTTP Request logging
--------------------
Internally this library uses the `requests <https://pypi.org/project/requests/>`_ library to perform network calls to the Cognite API service endpoints.
The ``requests`` library is in turn built on `urllib3 <https://pypi.org/project/urllib3/>`_, which means that you can enable DEBUG level logging for
the ``urllib3`` module to log HTTP requests to and from the Cognite API.

Please be advised that doing so may cause sensitive information such as authentication credentials and sensitive
data to be logged, and this is not recommended in production environments, or where credentials cannot be easily disabled or rotated, or where
log data may be accessed by others.


<!-- SOURCE_END: cognite-sdk-python/docs\source\settings.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\simulators.rst -->
## File: cognite-sdk-python/docs\source\simulators.rst

Simulators
==========

SimulatorsAPI
-------------
List simulators
^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.simulators.SimulatorsAPI.list

Simulator Integrations
-----------------------

List simulator integrations
^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.simulators.integrations.SimulatorIntegrationsAPI.list

Delete simulator integrations
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.simulators.integrations.SimulatorIntegrationsAPI.delete

Simulator Models
----------------

List simulator models
^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.simulators.models.SimulatorModelsAPI.list

Retrieve simulator models
^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.simulators.models.SimulatorModelsAPI.retrieve

Create simulator models
^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.simulators.models.SimulatorModelsAPI.create

Update simulator models
^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.simulators.models.SimulatorModelsAPI.update

Delete simulator models
^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.simulators.models.SimulatorModelsAPI.delete

Simulator Model Revisions
--------------------------

List simulator model revisions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.simulators.models_revisions.SimulatorModelRevisionsAPI.list

Retrieve simulator model revisions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.simulators.models_revisions.SimulatorModelRevisionsAPI.retrieve

Create simulator model revisions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.simulators.models_revisions.SimulatorModelRevisionsAPI.create

Simulator Routines
------------------

List simulator routines
^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.simulators.routines.SimulatorRoutinesAPI.list

Create simulator routines
^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.simulators.routines.SimulatorRoutinesAPI.create

Delete simulator routines
^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.simulators.routines.SimulatorRoutinesAPI.delete

Run simulator routines
^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.simulators.routines.SimulatorRoutinesAPI.run

Simulator Routine Revisions
----------------------------

List simulator routine revisions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.simulators.routine_revisions.SimulatorRoutineRevisionsAPI.list

Retrieve simulator routine revisions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.simulators.routine_revisions.SimulatorRoutineRevisionsAPI.retrieve

Create simulator routine revisions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.simulators.routine_revisions.SimulatorRoutineRevisionsAPI.create

Simulation Runs
---------------

List simulation runs
^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.simulators.runs.SimulatorRunsAPI.list

Retrieve simulation runs
^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.simulators.runs.SimulatorRunsAPI.retrieve

Create simulation runs
^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.simulators.runs.SimulatorRunsAPI.create

List simulation run data
^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.simulators.runs.SimulatorRunsAPI.list_run_data

Simulator Logs
--------------

Retrieve simulator logs
^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.simulators.logs.SimulatorLogsAPI.retrieve

Data classes
------------
.. automodule:: cognite.client.data_classes.simulators
    :members:
    :show-inheritance: 

<!-- SOURCE_END: cognite-sdk-python/docs\source\simulators.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\testing.rst -->
## File: cognite-sdk-python/docs\source\testing.rst

Testing
-------
Object to use as a mock for CogniteClient
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. autoclass:: cognite.client.testing.CogniteClientMock

Use a context manager to monkeypatch CogniteClient
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. autofunction:: cognite.client.testing.monkeypatch_cognite_client

<!-- SOURCE_END: cognite-sdk-python/docs\source\testing.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\time_series.rst -->
## File: cognite-sdk-python/docs\source\time_series.rst

Time Series
===========

Metadata
--------

Retrieve a time series by id
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.time_series.TimeSeriesAPI.retrieve

Retrieve multiple time series by id
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.time_series.TimeSeriesAPI.retrieve_multiple

List time series
^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.time_series.TimeSeriesAPI.list

Aggregate time series
^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.time_series.TimeSeriesAPI.aggregate

Aggregate Time Series Count
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.time_series.TimeSeriesAPI.aggregate_count

Aggregate Time Series Values Cardinality
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.time_series.TimeSeriesAPI.aggregate_cardinality_values

Aggregate Time Series Property Cardinality
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.time_series.TimeSeriesAPI.aggregate_cardinality_properties

Aggregate Time Series Unique Values
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.time_series.TimeSeriesAPI.aggregate_unique_values

Aggregate Time Series Unique Properties
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.time_series.TimeSeriesAPI.aggregate_unique_properties

Search for time series
^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.time_series.TimeSeriesAPI.search

Create time series
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.time_series.TimeSeriesAPI.create

Delete time series
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.time_series.TimeSeriesAPI.delete

Filter time series
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.time_series.TimeSeriesAPI.filter

Update time series
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.time_series.TimeSeriesAPI.update

Upsert time series
^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.time_series.TimeSeriesAPI.upsert

Time Series Data classes
^^^^^^^^^^^^^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.time_series
    :members:
    :show-inheritance:

Synthetic time series
---------------------

Calculate the result of a function on time series
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.synthetic_time_series.SyntheticDatapointsAPI.query



Datapoints
----------

Retrieve datapoints
^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.datapoints.DatapointsAPI.retrieve

Retrieve datapoints as numpy arrays
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.datapoints.DatapointsAPI.retrieve_arrays

Retrieve datapoints in pandas dataframe
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.datapoints.DatapointsAPI.retrieve_dataframe

Retrieve datapoints in time zone in pandas dataframe
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.datapoints.DatapointsAPI.retrieve_dataframe_in_tz

Iterate through datapoints in chunks
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.datapoints.DatapointsAPI.__call__

Retrieve latest datapoint
^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.datapoints.DatapointsAPI.retrieve_latest

Insert datapoints
^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.datapoints.DatapointsAPI.insert

Insert datapoints into multiple time series
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.datapoints.DatapointsAPI.insert_multiple

Insert pandas dataframe
^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.datapoints.DatapointsAPI.insert_dataframe

Delete a range of datapoints
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.datapoints.DatapointsAPI.delete_range

Delete ranges of datapoints
^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.datapoints.DatapointsAPI.delete_ranges


Datapoints Data classes
^^^^^^^^^^^^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.datapoints
    :members:
    :show-inheritance:

Datapoint Subscriptions
---------------------------

Create datapoint subscriptions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.datapoints_subscriptions.DatapointsSubscriptionAPI.create

Retrieve a datapoint subscription by id(s)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.datapoints_subscriptions.DatapointsSubscriptionAPI.retrieve

List datapoint subscriptions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.datapoints_subscriptions.DatapointsSubscriptionAPI.list

List member time series of subscription
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.datapoints_subscriptions.DatapointsSubscriptionAPI.list_member_time_series

Iterate over subscriptions data
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.datapoints_subscriptions.DatapointsSubscriptionAPI.iterate_data

Update datapoint subscription
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.datapoints_subscriptions.DatapointsSubscriptionAPI.update

Delete datapoint subscription
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.datapoints_subscriptions.DatapointsSubscriptionAPI.delete

Datapoint Subscription classes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.datapoints_subscriptions
    :members:
    :show-inheritance:


<!-- SOURCE_END: cognite-sdk-python/docs\source\time_series.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\transformations.rst -->
## File: cognite-sdk-python/docs\source\transformations.rst

Transformations
===============

TransformationsAPI
------------------
Create transformations
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.transformations.TransformationsAPI.create

Retrieve transformations by id
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.transformations.TransformationsAPI.retrieve

.. automethod:: cognite.client._api.transformations.TransformationsAPI.retrieve_multiple

Run transformations by id
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.transformations.TransformationsAPI.run
.. automethod:: cognite.client._api.transformations.TransformationsAPI.run_async

Preview transformations
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.transformations.TransformationsAPI.preview

Cancel transformation run by id
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.transformations.TransformationsAPI.cancel

List transformations
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.transformations.TransformationsAPI.list

Update transformations
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.transformations.TransformationsAPI.update

Delete transformations
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.transformations.TransformationsAPI.delete

Transformation Schedules
------------------------

Create transformation Schedules
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.transformations.schedules.TransformationSchedulesAPI.create

Retrieve transformation schedules
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.transformations.schedules.TransformationSchedulesAPI.retrieve

Retrieve multiple transformation schedules
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.transformations.schedules.TransformationSchedulesAPI.retrieve_multiple

List transformation schedules
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.transformations.schedules.TransformationSchedulesAPI.list

Update transformation schedules
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.transformations.schedules.TransformationSchedulesAPI.update

Delete transformation schedules
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.transformations.schedules.TransformationSchedulesAPI.delete

Transformation Notifications
----------------------------

Create transformation notifications
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.transformations.notifications.TransformationNotificationsAPI.create

List transformation notifications
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.transformations.notifications.TransformationNotificationsAPI.list

Delete transformation notifications
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.transformations.notifications.TransformationNotificationsAPI.delete

Transformation Jobs
-------------------

Retrieve transformation jobs
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.transformations.jobs.TransformationJobsAPI.retrieve

.. automethod:: cognite.client._api.transformations.jobs.TransformationJobsAPI.retrieve_multiple

List transformation jobs
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.transformations.jobs.TransformationJobsAPI.list

Transformation Schema
----------------------

Get transformation schema
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.transformations.schema.TransformationSchemaAPI.retrieve

Data classes
------------
.. automodule:: cognite.client.data_classes.transformations
    :members:
    :show-inheritance:
.. automodule:: cognite.client.data_classes.transformations.schedules
    :members:
    :show-inheritance:
.. automodule:: cognite.client.data_classes.transformations.notifications
    :members:
    :show-inheritance:
.. automodule:: cognite.client.data_classes.transformations.jobs
    :members:
    :show-inheritance:
.. automodule:: cognite.client.data_classes.transformations.schema
    :members:
    :show-inheritance:
.. automodule:: cognite.client.data_classes.transformations.common
    :members:
    :show-inheritance:


<!-- SOURCE_END: cognite-sdk-python/docs\source\transformations.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\unit_catalog.rst -->
## File: cognite-sdk-python/docs\source\unit_catalog.rst

Unit Catalog
======================

Units
------------
List Units
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.units.UnitAPI.list

Retrieve Unit
^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.units.UnitAPI.retrieve

Look up Unit from alias
^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.units.UnitAPI.from_alias

Unit Systems
------------------
List Unit System
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automethod:: cognite.client._api.units.UnitSystemAPI.list

Unit data classes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. automodule:: cognite.client.data_classes.units
    :members:
    :show-inheritance:


<!-- SOURCE_END: cognite-sdk-python/docs\source\unit_catalog.rst -->

================================================================================


<!-- SOURCE_START: cognite-sdk-python/docs\source\utils.rst -->
## File: cognite-sdk-python/docs\source\utils.rst

Utils
=====
Convert timestamp to milliseconds since epoch
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. autofunction:: cognite.client.utils.timestamp_to_ms

Convert milliseconds since epoch to datetime
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. autofunction:: cognite.client.utils.ms_to_datetime

Convert datetime to milliseconds since epoch
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. autofunction:: cognite.client.utils.datetime_to_ms

Convert datetime to ISO timestamp
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. autofunction:: cognite.client.utils.datetime_to_ms_iso_timestamp

<!-- SOURCE_END: cognite-sdk-python/docs\source\utils.rst -->

================================================================================
