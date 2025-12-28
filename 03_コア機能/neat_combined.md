# neat Documentation
Generated from: neat



<!-- SOURCE_START: neat/docs\authors.md -->
## File: neat/docs\authors.md


NEAT Version 1 is developed and maintained by [Nikola Vasiljević](https://thisisnikola.com) and [Anders Albert](https://www.linkedin.com/in/anders-albert-00790483/).

The following is the list of contributors to the legacy version of NEAT: [Aleksandrs Livincovs](https://www.linkedin.com/in/aleksandrslivincovs/), [Marie Solvik Lepoutre](https://www.linkedin.com/in/mslepoutre/), [Julia Graham](https://www.linkedin.com/in/julia-graham-959a78a7/), [Rogerio Júnior](https://www.linkedin.com/in/rogerio-saboia-j%C3%BAnior-087118a7/), [Bård Henning Tvedt](https://www.linkedin.com/in/bhtvedt/), [Hassan Gomaa](https://www.linkedin.com/in/dr-hassan-gomaa-232638121/), [Kristina Tomičić](https://www.linkedin.com/in/kristina-tomicic-6bb443108/)

<!-- SOURCE_END: neat/docs\authors.md -->

================================================================================


<!-- SOURCE_START: neat/docs\data_modeling\excel\data_model.md -->
## File: neat/docs\data_modeling\excel\data_model.md

# Data Modeling in Excel

The main interface for working with `NEAT` is through a Python notebook environment. To get more granular control
over the data modeling process, you can use the Excel interface of a data model either to modify an existing data
model or to create a new one. The `NeatSession` objects provides methods for reading and writing data models to
Excel files.

```python
from cognite.neat import NeatSession, get_cognite_client

client = get_cognite_client(".env")

neat = NeatSession(client)

# Reading an existing data model
neat.physical_data_model.read.excel("path/to/excel/file.xlsx")


# Writing a data model to an Excel file
neat.physical_data_model.write.excel("path/to/excel/file.xlsx")
```


<!-- SOURCE_END: neat/docs\data_modeling\excel\data_model.md -->

================================================================================


<!-- SOURCE_START: neat/docs\data_modeling\excel\physical\enum.md -->
## File: neat/docs\data_modeling\excel\physical\enum.md

# Enum

Enum (short for 'enumeration') is a text/string field that can only have a predefined set of values. You can
define an enumeration in the `enum` sheet in the physical data model spreadsheet, which you can then reference in the
`Value Type` column in the `properties` sheet.

## Defining an enumeration

To define an enumeration, you add one row for each value in the enumeration in the `enum` sheet. The example
below shows the enumeration for the `CoginteTimeseries.type` and `CogniteAnnotation.status` properties from
`CogniteCore` model.

| Collection               | Value     | Name      | Description                                         |
|--------------------------|-----------|-----------|-----------------------------------------------------|
| CogniteTimeseries.type   | numeric   | numeric   | Time series with double floating point data points. |
| CogniteTimeseries.type   | string    | string    | Time series with string data points.                |
| CogniteAnnotation.status | Approved  | Approved  |                                                     |
| CogniteAnnotation.status | Rejected  | Rejected  |                                                     |
| CogniteAnnotation.status | Suggested | Suggested |                                                     |

The `Collection` column is the unique identifier for the enumeration, in the example above, we have two
enumerations, `CogniteTimeseries.type` and `CogniteAnnotation.status`. The `Value` column contains the allowed
values for each enumeration. The `Name` and `Description` columns are optional and contain the display name
and description of the enumeration value.


## Referencing an enumeration

You can reference an enumeration in the `Value Type` column in the `properties` sheet. The syntax for referencing
an enumeration is `enum(collection=<CollectionName>)`. You can also specify an `unknownValue` parameter that will be
used when the value is unknown.

| View              | View Property | Value Type                                | Container         | Container Property |
|-------------------|---------------|-------------------------------------------|-------------------|--------------------|
| CogniteTimeSeries | type          | enum(collection=CogniteTimeSeries.type)   | CogniteTimeSeries | type               |
| CogniteAnnotation | status        | enum(collection=CogniteAnnotation.status) | CogniteAnnotation | status             |




<!-- SOURCE_END: neat/docs\data_modeling\excel\physical\enum.md -->

================================================================================


<!-- SOURCE_START: neat/docs\data_modeling\excel\physical\overview.md -->
## File: neat/docs\data_modeling\excel\physical\overview.md

# Physical Data Model

The physical data model is the implementation of the conceptual data model in Cognite Data Fusion (CDF),
specifically in the Domain Modeling Service (DMS).

For a reference of the physical data model, see the [reference](reference.md).

In addition, for more detailed explanations of the different check the menu on the left.

<!-- SOURCE_END: neat/docs\data_modeling\excel\physical\overview.md -->

================================================================================


<!-- SOURCE_START: neat/docs\data_modeling\excel\physical\properties.md -->
## File: neat/docs\data_modeling\excel\physical\properties.md

# Properties

This section contains elaborations of the different fields in the properties sheet.

There are two types of properties in physical data model (DMS): `Connection` and `Data`. The `Connection` property is used to
specify how nodes are connected, while the `Data` property is used to specify the data that is stored on the node.
These concepts match a `Entity` and `Literal` in semantic modeling. It is recommended that you use `PascalCase` for
views, neat uses `camelCase` for data properties. This makes it easier to distinguish between the two by looking
at the `Value Type` column in the `Properties` sheet.

## Data Property
A data property is used to specify the data that is stored on the node (also known as node attributes). Below is an example of a data property:

| View        | View Property | Value Type                | Container      | Container Property |
|-------------|---------------|---------------------------|----------------|--------------------|
| WindTurbine | capacity      | float64(unit=power:megaw) | GeneratingUnit | capacity           |

This data property specifies that the `WindTurbine` view has a property called `capacity` that is a holding vLUW of type float64 with the unit
`power:megaw`. The data is stored in the `GeneratingUnit` container with the property `capacity`.

To see which value types are supported, see the
[CDF API Spec for Container Creation](https://api-docs.cognite.com/20230101/tag/Containers/operation/ApplyContainers) section.
The `container.properties.type.type` field specifies the type of the property.

<img src="../../artifacts/figs/container_spec.png" height="200">

There are two `Value Type` that supports extra parameters

* `float32` and `float64` - These are used to specify floating-point numbers. The `unit` parameter is
   used to specify the unit of the number. See [Units](units.md) for available units. See example above.
* `enum` - This is used to specify an enumeration. You need to set `collection` to the name of the enumeration and,
   optionally, `unknownValue` to the value that should be used when the value is unknown. See example below. When
   enumerations are used, there is expected to be a corresponding `enum` sheet in the physical data model file with the
   enumeration values.

| View        | View Property | Value Type                                      | Container   | Container Property |
|-------------|---------------|-------------------------------------------------|-------------|--------------------|
| WindTurbine | category      | enum(collection=category, unknownValue=onshore) | WindTurbine | category           |


### Index

For data properties, you can specify an index. The index is used to speed up queries on the property. The syntax is as
follows:

| View        | View Property | Value Type | Container      | Container Property | Index                            |
|-------------|---------------|------------|----------------|--------------------|----------------------------------|
| WindTurbine | name          | text       | GeneratingUnit | name               | btree:nameIndex(cursorable=True) |

Note that you can specify whether the index is cursorable or not, see
[here](https://docs.cognite.com/cdf/dm/dm_guides/dm_performance_considerations/#pagination-cursorable-indexes).

You can also specify indices that uses multiple data properties. You can do this by specifying the same `Index` name
for multiple properties. When you specify an index on multiple properties you must specify the order these properties
should be used. See the example below:

| View        | View Property | Value Type | Container      | Container Property | Index                               |
|-------------|---------------|------------|----------------|--------------------|-------------------------------------|
| WindTurbine | name          | text       | GeneratingUnit | name               | inverted:nameCapacityIndex(order=1) |
| WindTurbine | capacity      | float64    | GeneratingUnit | capacity           | inverted:nameCapacityIndex(order=2) |

There are two available index types, `btree` which should be used for non-list properties and `inverted` which should
be used for list properties. If you do not specify the index type, it will default to `btree` for non-list properties
and `inverted` for list properties.

| View        | View Property | Value Type | Container      | Container Property | Index              |
|-------------|---------------|------------|----------------|--------------------|--------------------|
| WindTurbine | tags          | text       | GeneratingUnit | name               | inverted:tagsIndex |

### Constraint
Use constraints to restrict the values that can be stored by a property, or in a container. Constraints ensure that the data has integrity, and reflects the real world.

CDF supports the two constrain types:

- `uniqueness`: Ensures that the values of a property or a set of properties are unique within the container. Set a uniqueness constraint to bySpace, indicating that the uniqueness will apply per space.
- `requires`: Points to another container, and requires that the instance has data in that other container used to populate this container.

When setting any of these constraints in `NEAT`, similar to the indexes, the following short-form syntax is used:

```
<constraint_type>:<constraint_id>(<param1>=<value1>,<param2>=<value2>, ...)
```

In Properties sheet you are able to set `uniqueness` constraint on a property. See the example below:

| View        | View Property | Value Type | Container      | Container Property | Constraint                          |
|-------------|---------------|------------|----------------|--------------------|-------------------------------------|
| WindTurbine | serialNumber  | text       | GeneratingUnit | serialNumber       | uniqueness:serialNoUnq(bySpace=True)|

`bySpace` parameter indicates that the uniqueness will apply per space, and it is the only supported parameter for `uniqueness` constraint.

In Containers sheet you are able to set `requires` constraints on a container. See the example below:


| Container      | Name           | Description                     | Constraint                                 |
|----------------|----------------|---------------------------------|--------------------------------------------|
| GeneratingUnit | GeneratingUnit | Container for generating units.  | requires:power(container=PowerPlant)      |

For the `requires` constraint, the `container` parameter is used to specify the container that is required to have data in
order to populate this container.


## Connection Property

All connections have a `ValueType` that specifies the type of the connected node. For example:

| View          | ViewProperty       | Connection | Value Type  | Is List |
|---------------|------------------  |------------|-------------|---------|
| WindTurbine   | blades             | direct     | Blade       | True    |

This connection specifies that the `WindTurbine` view has a property called `blades` that is a direct relation to the `Blade` view.
In addition, the `Is List`, specifies that there can be multiple blades connected to a wind turbine.

#### Connection Implementation

The column `Connection` specifies how the connection is implemented in the CDF data model and can be one of the following:

* Direct relation—This is cheap in terms of storage and query time.
* Edge connection—This is more flexible, but more expensive in terms of storage and query time.
* Reverse connection—This is used to specify a connection from the other end of a direct relation or edge connection.

To get more details on the difference, see the [ data modeling documentation](https://docs.cognite.com/cdf/dm/dm_concepts/dm_spaces_instances#direct-relations-vs-edges).
Note that in addition to the mentioned differences, direct relations have an upper limit of 1000 connection per node.

The syntax for the `Connection` column is as follows:

* `direct` - This specifies a direct relation. There are no extra parameters. Note, however, that you need to
   specify `Container` and `Container Property` as `direct` connections are stored in the container.
* `edge` - This specifies an edge connection. You can, optionally, specify `type`, `properties`,
   and `direction` as extra parameters.
* `reverse` - This specifies a reverse connection. You need to specify the `property` that the connection is
   reversing.

**Edge example**:


| View        | ViewProperty | Connection                                                 | Value Type             | Is List |
|-------------|--------------|------------------------------------------------------------|------------------------|---------|
| WindTurbine | metmasts     | edge(type=distance,properties=Distance,direction=outwards) | MetMast                | True    |
| Distance    | distance     |                                                            | float64(unit=length:m) | False   |

This connection specifies that the `WindTurbine` view has a property called `metmasts` that is an edge connection
to the `MetMast` view. The edges are of type `distance` and have properties stored in the `Distance` view. The
`Distance` view has a property called `distance` that is a float64 with the unit `length:m`.

Why is both `type` and `properties` needed? The `type` specifies the type of the edge, this is used for filtering
when querying the data model. The `properties` specifies the properties that are stored on the edge. This is used
to store data on the edge. In the example above, we can, for example, write a query that returns all MetMast
(Wheather Station) that are connected to a WindTurbine with a distance less than 100 meters.

**Reverse example**:

| View       | ViewProperty   | Connection                 | Value Type   | Is List |
|------------|----------------|----------------------------|--------------|---------|
| MetMast    | windTurbines   | reverse(property=metmasts) | WindTurbine  | True    |

This connection specifies that the `MetMast` view has a property called `windTurbines` that is a reverse connection
of the `metmasts` property in the `WindTurbine` view. The `Is List` specifies that there can be multiple wind turbines
connected to a MetMast.

Connecting this example to the previous example, we see that the reverse here will be an edge that is pointing the
opposite direction of the `WindTurbine`.`metmasts` edge. The reverse enable use to easily reuse the same edge
for both directions.

<!-- SOURCE_END: neat/docs\data_modeling\excel\physical\properties.md -->

================================================================================


<!-- SOURCE_START: neat/docs\data_modeling\excel\physical\reference.md -->
## File: neat/docs\data_modeling\excel\physical\reference.md

# Physical Reference

This document is a reference for the physical data model.

The physical data model has the following sheets:

- Metadata: Contains information about the data model.
- Properties: Contains the properties of the data model.
- Views: Contains the views of the data model.
- Containers (optional): Contains the definition containers that are the physical storage of the data model.
- Enum (optional): Contains the definition of enum values.
- Nodes (optional): Contains the definition of the node types.

## Metadata Sheet

Contains information about the data model.

| Field | Description | Mandatory |
|----------------|-------------|-----------|
| space | The space where the data model is defined | Yes |
| externalId | External identifier for the data model | Yes |
| version | Version of the data model | Yes |
| name | Human readable name of the data model | No |
| description | Short description of the data model | No |
| creator | List of creators (comma separated) to the data model. | No |

## Properties Sheet

Contains the properties of the data model.

| Column Name | Description | Mandatory |
|----------------|-------------|-----------|
| View | The view identifier to which the property belongs | Yes |
| View Property | The property identifier | Yes |
| Name | Human readable name of the property | No |
| Description | Short description of the property | No |
| Connection | Only applies to connection between views. It specify how the connection should be implemented in CDF. | No |
| Value Type | Value type that the property can hold. It takes either subset of CDF primitive types or a View id | Yes |
| Min Count | Minimum number of values that the property can hold. If no value is provided, the default value is  `0`, which means that the property is optional. | No |
| Max Count | Maximum number of values that the property can hold. If no value is provided, the default value is  `inf`, which means that the property can hold any number of values (listable). | No |
| Immutable | Used to indicate whether the property is can only be set once. Only applies to primitive type. | No |
| Auto Increment | Used to indicate whether the property value is auto incremented. Only applies to primitive type. | No |
| Default | Specifies default value for the property. | No |
| Container | Specifies container where the property is stored. Only applies to primitive type. | No |
| Container Property | Specifies property in the container where the property is stored. Only applies to primitive type. | No |
| Container Property Name | Human readable name of the container property. | No |
| Container Property Description | Human readable description of the container property. | No |
| Index | The names of the indexes (comma separated) that should be created for the property. | No |
| Constraint | List of constraints (comma separated) to the data model.. | No |

## Views Sheet

Contains the views of the data model.

| Column Name | Description | Mandatory |
|----------------|-------------|-----------|
| View | View id, strongly advised to PascalCase usage. | Yes |
| Name | Human readable name of the view being defined. | No |
| Description | Short description of the view being defined  | No |
| Implements | List of parent view ids (comma separated) which the view being defined implements. | No |
| Filter | Explicitly define the filter for the view. | No |

## Containers Sheet

Contains the definition containers that are the physical storage of the data model.

| Column Name | Description | Mandatory |
|----------------|-------------|-----------|
| Container | Container id, strongly advised to PascalCase usage. | Yes |
| Name | Human readable name of the container being defined. | No |
| Description | Short description of the node being defined. | No |
| Constraint | List of required (comma separated) constraints for the container | No |
| Used For |  Whether the container is used for nodes, edges or all. | Yes |

## Enum Sheet

Contains the definition of enum values.

| Column Name | Description | Mandatory |
|----------------|-------------|-----------|
| Collection | The collection this enum belongs to. | Yes |
| Value | The value of the enum. | Yes |
| Name | Human readable name of the enum. | No |
| Description | Short description of the enum. | No |

## Nodes Sheet

Contains the definition of the node types.

| Column Name | Description | Mandatory |
|----------------|-------------|-----------|
| Node | The type definition of the node. | Yes |
| Usage | What the usage of the node is in the data model. | Yes |
| Name | Human readable name of the node being defined. | No |
| Description | Short description of the node being defined. | No |


<!-- SOURCE_END: neat/docs\data_modeling\excel\physical\reference.md -->

================================================================================


<!-- SOURCE_START: neat/docs\data_modeling\excel\physical\units.md -->
## File: neat/docs\data_modeling\excel\physical\units.md

# Units

This document contains a list of units that are available in the Cognite Fusion platform. It was last generated 2024-12-06.
For the most up-to-date list of units, please refer to the [units catalog](https://raw.githubusercontent.com/cognitedata/units-catalog/main/versions/v1/units.json).


## API Gravity

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| api_gravity:degree_api | DEGREE_API | Degree API | °API | [https://qudt.org/vocab/unit/DEGREE_API](https://qudt.org/vocab/unit/DEGREE_API) |
| api_gravity:kilo_degree_api | KILO_DEGREE_API | Kilo Degree API | k °API |  |


## Acceleration

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| acceleration:ft-per-sec2 | FT-PER-SEC2 | Foot per Square Second | ft/s² | [https://qudt.org/vocab/unit/FT-PER-SEC2](https://qudt.org/vocab/unit/FT-PER-SEC2) |
| acceleration:in-per-sec2 | IN-PER-SEC2 | Inch per Square second | in/s² | [https://qudt.org/vocab/unit/IN-PER-SEC2](https://qudt.org/vocab/unit/IN-PER-SEC2) |
| acceleration:m-per-sec2 | M-PER-SEC2 | Meter per Square Second | m/s² | [https://qudt.org/vocab/unit/M-PER-SEC2](https://qudt.org/vocab/unit/M-PER-SEC2) |


## Amount Of Substance

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| amount_of_substance:centimol | CentiMOL | CentiMole | cmol | [https://qudt.org/vocab/unit/CentiMOL](https://qudt.org/vocab/unit/CentiMOL) |
| amount_of_substance:kilomol | KiloMOL | Kilomole | kmol | [https://qudt.org/vocab/unit/KiloMOL](https://qudt.org/vocab/unit/KiloMOL) |
| amount_of_substance:micromol | MicroMOL | Micromole | μmol | [https://qudt.org/vocab/unit/MicroMOL](https://qudt.org/vocab/unit/MicroMOL) |
| amount_of_substance:millimol | MilliMOL | Millimole | mmol | [https://qudt.org/vocab/unit/MilliMOL](https://qudt.org/vocab/unit/MilliMOL) |
| amount_of_substance:mol | MOL | mole | mol | [https://qudt.org/vocab/unit/MOL](https://qudt.org/vocab/unit/MOL) |


## Amount Of Substance Per Unit Volume

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| amount_of_substance_per_unit_volume:centimol-per-l | CentiMOL-PER-L | Centimole per litre | cmol/L | [https://qudt.org/vocab/unit/CentiMOL-PER-L](https://qudt.org/vocab/unit/CentiMOL-PER-L) |
| amount_of_substance_per_unit_volume:kilomol-per-m3 | KiloMOL-PER-M3 | Kilomole Per Cubic Meter | kmol/m³ | [https://qudt.org/vocab/unit/KiloMOL-PER-M3](https://qudt.org/vocab/unit/KiloMOL-PER-M3) |
| amount_of_substance_per_unit_volume:micromol-per-l | MicroMOL-PER-L | Micromoles per litre | µmol/L | [https://qudt.org/vocab/unit/MicroMOL-PER-L](https://qudt.org/vocab/unit/MicroMOL-PER-L) |
| amount_of_substance_per_unit_volume:millimol-per-l | MilliMOL-PER-L | millimoles per litre | mmol/L | [https://qudt.org/vocab/unit/MilliMOL-PER-L](https://qudt.org/vocab/unit/MilliMOL-PER-L) |
| amount_of_substance_per_unit_volume:millimol-per-m3 | MilliMOL-PER-M3 | Millimoles per cubic metre | mmol/m³ | [https://qudt.org/vocab/unit/MilliMOL-PER-M3](https://qudt.org/vocab/unit/MilliMOL-PER-M3) |
| amount_of_substance_per_unit_volume:mol-per-decim3 | MOL-PER-DeciM3 | Mole Per Cubic Decimeter | mol/dm³ | [https://qudt.org/vocab/unit/MOL-PER-DeciM3](https://qudt.org/vocab/unit/MOL-PER-DeciM3) |
| amount_of_substance_per_unit_volume:mol-per-l | MOL-PER-L | Mole Per Liter | mol/L | [https://qudt.org/vocab/unit/MOL-PER-L](https://qudt.org/vocab/unit/MOL-PER-L) |
| amount_of_substance_per_unit_volume:mol-per-m3 | MOL-PER-M3 | Mole per Cubic Meter | mol/m³ | [https://qudt.org/vocab/unit/MOL-PER-M3](https://qudt.org/vocab/unit/MOL-PER-M3) |


## Angle

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| angle:deg | DEG | Degree | ° | [https://qudt.org/vocab/unit/DEG](https://qudt.org/vocab/unit/DEG) |
| angle:grad | GRAD | Grad | grad | [https://qudt.org/vocab/unit/GRAD](https://qudt.org/vocab/unit/GRAD) |
| angle:microrad | MicroRAD | microradian | µrad | [https://qudt.org/vocab/unit/MicroRAD](https://qudt.org/vocab/unit/MicroRAD) |
| angle:millirad | MilliRAD | milliradian | mrad | [https://qudt.org/vocab/unit/MilliRAD](https://qudt.org/vocab/unit/MilliRAD) |
| angle:rad | RAD | radian | rad | [https://qudt.org/vocab/unit/RAD](https://qudt.org/vocab/unit/RAD) |
| angle:rev | REV | Revolution | rev | [https://qudt.org/vocab/unit/REV](https://qudt.org/vocab/unit/REV) |


## Angle Per Length

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| angle_per_length:deg-per-m | DEG-PER-M | Degrees Per Meter | deg/m | [https://qudt.org/vocab/unit/DEG-PER-M](https://qudt.org/vocab/unit/DEG-PER-M) |
| angle_per_length:rad-per-ft | RAD-PER-FT | Radians Per Foot | rad/ft | [https://www.energistics.org/energistics-unit-of-measure-standard](https://www.energistics.org/energistics-unit-of-measure-standard) |
| angle_per_length:rad-per-m | RAD-PER-M | Radians Per Meter | rad/m | [https://qudt.org/vocab/unit/RAD-PER-M](https://qudt.org/vocab/unit/RAD-PER-M) |


## Angular Acceleration

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| angular_acceleration:rev-per-sec2 | REV-PER-SEC2 | Revolution per Square Second | rev/s² | [https://qudt.org/vocab/unit/REV-PER-SEC2](https://qudt.org/vocab/unit/REV-PER-SEC2) |


## Angular Velocity

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| angular_velocity:rev-per-hr | REV-PER-HR | Revolution per Hour | rev/h | [https://qudt.org/vocab/unit/REV-PER-HR](https://qudt.org/vocab/unit/REV-PER-HR) |
| angular_velocity:rev-per-min | REV-PER-MIN | Revolution per Minute | rev/min | [https://qudt.org/vocab/unit/REV-PER-MIN](https://qudt.org/vocab/unit/REV-PER-MIN) |
| angular_velocity:rev-per-sec | REV-PER-SEC | Revolution per Second | rev/s | [https://qudt.org/vocab/unit/REV-PER-SEC](https://qudt.org/vocab/unit/REV-PER-SEC) |


## Area

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| area:centim2 | CentiM2 | Square Centimeter | cm² | [https://qudt.org/vocab/unit/CentiM2](https://qudt.org/vocab/unit/CentiM2) |
| area:decim2 | DeciM2 | Square Decimeter | dm² | [https://qudt.org/vocab/unit/DeciM2](https://qudt.org/vocab/unit/DeciM2) |
| area:ft2 | FT2 | Square Foot | ft² | [https://qudt.org/vocab/unit/FT2](https://qudt.org/vocab/unit/FT2) |
| area:in2 | IN2 | Square Inch | in² | [https://qudt.org/vocab/unit/IN2](https://qudt.org/vocab/unit/IN2) |
| area:m2 | M2 | Square Meter | m² | [https://qudt.org/vocab/unit/M2](https://qudt.org/vocab/unit/M2) |
| area:millim2 | MilliM2 | Square Millimeter | mm² | [https://qudt.org/vocab/unit/MilliM2](https://qudt.org/vocab/unit/MilliM2) |
| area:yd2 | YD2 | Square Yard | yd² | [https://qudt.org/vocab/unit/YD2](https://qudt.org/vocab/unit/YD2) |


## Attenuation

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| attenuation:b | B | Bel | B | [https://qudt.org/vocab/unit/B](https://qudt.org/vocab/unit/B) |
| attenuation:decib | DeciB | Decibel | dB | [https://qudt.org/vocab/unit/DeciB](https://qudt.org/vocab/unit/DeciB) |


## Capacitance

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| capacitance:farad | FARAD | farad | F | [https://qudt.org/vocab/unit/FARAD](https://qudt.org/vocab/unit/FARAD) |
| capacitance:microfarad | MicroFARAD | microfarad | µF | [https://qudt.org/vocab/unit/MicroFARAD](https://qudt.org/vocab/unit/MicroFARAD) |
| capacitance:millifarad | MilliFARAD | Millifarad | mF | [https://qudt.org/vocab/unit/MilliFARAD](https://qudt.org/vocab/unit/MilliFARAD) |


## Density

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| density:gm-per-m3 | GM-PER-M3 | Gram Per Cubic Meter | g/m³ | [https://qudt.org/vocab/unit/GM-PER-M3](https://qudt.org/vocab/unit/GM-PER-M3) |
| density:kilogm-per-m3 | KiloGM-PER-M3 | kilogram per cubic meter | kg/m³ | [https://qudt.org/vocab/unit/KiloGM-PER-M3](https://qudt.org/vocab/unit/KiloGM-PER-M3) |
| density:lb-per-ft3 | LB-PER-FT3 | Pound per Cubic Foot | lb/ft³ | [https://qudt.org/vocab/unit/LB-PER-FT3](https://qudt.org/vocab/unit/LB-PER-FT3) |
| density:lb-per-gal | LB-PER-GAL | Pound per Gallon | lb/gal | [https://qudt.org/vocab/unit/LB-PER-GAL](https://qudt.org/vocab/unit/LB-PER-GAL) |
| density:lb-per-in3 | LB-PER-IN3 | Pound per Cubic Inch | lb/in³ | [https://qudt.org/vocab/unit/LB-PER-IN3](https://qudt.org/vocab/unit/LB-PER-IN3) |
| density:lb-per-yd3 | LB-PER-YD3 | Pound per Cubic Yard | lb/yd³ | [https://qudt.org/vocab/unit/LB-PER-YD3](https://qudt.org/vocab/unit/LB-PER-YD3) |
| density:milligm-per-m3 | MilliGM-PER-M3 | Milligram Per Cubic Meter | mg/m³ | [https://qudt.org/vocab/unit/MilliGM-PER-M3](https://qudt.org/vocab/unit/MilliGM-PER-M3) |
| density:oz-per-gal | OZ-PER-GAL | Imperial Mass Ounce per Gallon | oz/gal{US} | [https://qudt.org/vocab/unit/OZ-PER-GAL](https://qudt.org/vocab/unit/OZ-PER-GAL) |
| density:oz-per-in3 | OZ-PER-IN3 | Imperial Mass Ounce per Cubic Inch | oz/in³{US} | [https://qudt.org/vocab/unit/OZ-PER-IN3](https://qudt.org/vocab/unit/OZ-PER-IN3) |


## Dimensionless

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| dimensionless:num | NUM | Number | # | [https://qudt.org/vocab/unit/NUM](https://qudt.org/vocab/unit/NUM) |
| dimensionless:unitless | UNITLESS | Unitless |  | [https://qudt.org/vocab/unit/UNITLESS](https://qudt.org/vocab/unit/UNITLESS) |


## Dimensionless Ratio

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| dimensionless_ratio:fraction | FRACTION | Fraction |  | [https://qudt.org/vocab/unit/FRACTION](https://qudt.org/vocab/unit/FRACTION) |
| dimensionless_ratio:percent | PERCENT | Percent | % | [https://qudt.org/vocab/unit/PERCENT](https://qudt.org/vocab/unit/PERCENT) |
| dimensionless_ratio:ppb | PPB | Parts per billion | PPB | [https://qudt.org/vocab/unit/PPB](https://qudt.org/vocab/unit/PPB) |
| dimensionless_ratio:ppm | PPM | Parts per million | PPM | [https://qudt.org/vocab/unit/PPM](https://qudt.org/vocab/unit/PPM) |
| dimensionless_ratio:ppth | PPTH | Parts per thousand | PPTH | [https://qudt.org/vocab/unit/PPTH](https://qudt.org/vocab/unit/PPTH) |
| dimensionless_ratio:unitless | UNITLESS | Unitless |  | [https://qudt.org/vocab/unit/UNITLESS](https://qudt.org/vocab/unit/UNITLESS) |


## Dynamic Viscosity

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| dynamic_viscosity:centipoise | CentiPOISE | Centipoise | cP | [https://qudt.org/vocab/unit/CentiPOISE](https://qudt.org/vocab/unit/CentiPOISE) |
| dynamic_viscosity:lb-per-ft-hr | LB-PER-FT-HR | Pound per Foot Hour | lb/(ft⋅h) | [https://qudt.org/vocab/unit/LB-PER-FT-HR](https://qudt.org/vocab/unit/LB-PER-FT-HR) |
| dynamic_viscosity:lb-per-ft-sec | LB-PER-FT-SEC | Pound per Foot Second | lb/(ft⋅s) | [https://qudt.org/vocab/unit/LB-PER-FT-SEC](https://qudt.org/vocab/unit/LB-PER-FT-SEC) |
| dynamic_viscosity:millipa-sec | MilliPA-SEC | Millipascal Second | mPa⋅s | [https://qudt.org/vocab/unit/MilliPA-SEC](https://qudt.org/vocab/unit/MilliPA-SEC) |
| dynamic_viscosity:pa-sec | PA-SEC | pascal second | Pa⋅s | [https://qudt.org/vocab/unit/PA-SEC](https://qudt.org/vocab/unit/PA-SEC) |
| dynamic_viscosity:poise | POISE | Poise | P | [https://qudt.org/vocab/unit/POISE](https://qudt.org/vocab/unit/POISE) |


## Electric Current

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| electric_current:a | A | ampere | A | [https://qudt.org/vocab/unit/A](https://qudt.org/vocab/unit/A) |
| electric_current:kiloa | KiloA | kiloampere | kA | [https://qudt.org/vocab/unit/KiloA](https://qudt.org/vocab/unit/KiloA) |
| electric_current:megaa | MegaA | Megaampere | MA | [https://qudt.org/vocab/unit/MegaA](https://qudt.org/vocab/unit/MegaA) |
| electric_current:millia | MilliA | MilliAmpere | mA | [https://qudt.org/vocab/unit/MilliA](https://qudt.org/vocab/unit/MilliA) |


## Electric Potential

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| electric_potential:kilov | KiloV | Kilovolt | kV | [https://qudt.org/vocab/unit/KiloV](https://qudt.org/vocab/unit/KiloV) |
| electric_potential:megav | MegaV | Megavolt | MV | [https://qudt.org/vocab/unit/MegaV](https://qudt.org/vocab/unit/MegaV) |
| electric_potential:microv | MicroV | Microvolt | µV | [https://qudt.org/vocab/unit/MicroV](https://qudt.org/vocab/unit/MicroV) |
| electric_potential:milliv | MilliV | Millivolt | mV | [https://qudt.org/vocab/unit/MilliV](https://qudt.org/vocab/unit/MilliV) |
| electric_potential:v | V | volt | V | [https://qudt.org/vocab/unit/V](https://qudt.org/vocab/unit/V) |


## Energy

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| energy:boe | BOE | Barrel of Oil Equivalent | boe |  |
| energy:btu_it | BTU_IT | British Thermal Unit (International Definition) | Btu{IT} | [https://qudt.org/vocab/unit/BTU_IT](https://qudt.org/vocab/unit/BTU_IT) |
| energy:btu_th | BTU_TH | British Thermal Unit (Thermochemical Definition) | Btu{th} | [https://qudt.org/vocab/unit/BTU_TH](https://qudt.org/vocab/unit/BTU_TH) |
| energy:cal_it | CAL_IT | International Table calorie | cal{IT} | [https://qudt.org/vocab/unit/CAL_IT](https://qudt.org/vocab/unit/CAL_IT) |
| energy:cal_th | CAL_TH | Thermochemical Calorie | cal | [https://qudt.org/vocab/unit/CAL_TH](https://qudt.org/vocab/unit/CAL_TH) |
| energy:ft-lb_f | FT-LB_F | Foot Pound Force | ft⋅lbf | [https://qudt.org/vocab/unit/FT-LB_F](https://qudt.org/vocab/unit/FT-LB_F) |
| energy:gigaj | GigaJ | Gigajoule | GJ | [https://qudt.org/vocab/unit/GigaJ](https://qudt.org/vocab/unit/GigaJ) |
| energy:gigaw-hr | GigaW-HR | Gigawatt Hour | GW⋅h | [https://qudt.org/vocab/unit/GigaW-HR](https://qudt.org/vocab/unit/GigaW-HR) |
| energy:j | J | joule | J | [https://qudt.org/vocab/unit/J](https://qudt.org/vocab/unit/J) |
| energy:kilobtu_it | KiloBTU_IT | Kilo British Thermal Unit (International Definition) | kBtu{IT} | [https://qudt.org/vocab/unit/KiloBTU_IT](https://qudt.org/vocab/unit/KiloBTU_IT) |
| energy:kilobtu_th | KiloBTU_TH | Kilo British Thermal Unit (Thermochemical Definition) | kBtu{th} | [https://qudt.org/vocab/unit/KiloBTU_TH](https://qudt.org/vocab/unit/KiloBTU_TH) |
| energy:kilocal | KiloCAL | Kilocalorie | kcal | [https://qudt.org/vocab/unit/KiloCAL](https://qudt.org/vocab/unit/KiloCAL) |
| energy:kiloj | KiloJ | Kilojoule | kJ | [https://qudt.org/vocab/unit/KiloJ](https://qudt.org/vocab/unit/KiloJ) |
| energy:kilov-a-hr | KiloV-A-HR | Kilovolt Ampere Hour | kV⋅A/h | [https://qudt.org/vocab/unit/KiloV-A-HR](https://qudt.org/vocab/unit/KiloV-A-HR) |
| energy:kilow-hr | KiloW-HR | Kilowatthour | kW⋅h | [https://qudt.org/vocab/unit/KiloW-HR](https://qudt.org/vocab/unit/KiloW-HR) |
| energy:megaj | MegaJ | Megajoule | MJ | [https://qudt.org/vocab/unit/MegaJ](https://qudt.org/vocab/unit/MegaJ) |
| energy:megatoe | MegaTOE | Megaton of Oil Equivalent | megatoe | [https://qudt.org/vocab/unit/MegaTOE](https://qudt.org/vocab/unit/MegaTOE) |
| energy:megav-a-hr | MegaV-A-HR | Megavolt Ampere Hour | MV⋅A⋅h | [https://qudt.org/vocab/unit/MegaV-A-HR](https://qudt.org/vocab/unit/MegaV-A-HR) |
| energy:megaw-hr | MegaW-HR | Megawatt Hour | MW⋅h | [https://qudt.org/vocab/unit/MegaW-HR](https://qudt.org/vocab/unit/MegaW-HR) |
| energy:microj | MicroJ | Micro Joule | µJ | [https://qudt.org/vocab/unit/MicroJ](https://qudt.org/vocab/unit/MicroJ) |
| energy:millij | MilliJ | Millijoule | mJ | [https://qudt.org/vocab/unit/MilliJ](https://qudt.org/vocab/unit/MilliJ) |
| energy:teraj | TeraJ | Terajoule | TJ | [https://qudt.org/vocab/unit/TeraJ](https://qudt.org/vocab/unit/TeraJ) |
| energy:teraw-hr | TeraW-HR | Terawatt Hour | TW⋅h | [https://qudt.org/vocab/unit/TeraW-HR](https://qudt.org/vocab/unit/TeraW-HR) |
| energy:toe | TOE | Ton of Oil Equivalent | toe | [https://qudt.org/vocab/unit/TOE](https://qudt.org/vocab/unit/TOE) |
| energy:v-a-hr | V-A-HR | Volt Ampere Hour | V⋅A⋅h | [https://qudt.org/vocab/unit/V-A-HR](https://qudt.org/vocab/unit/V-A-HR) |
| energy:w-hr | W-HR | Watthour | W⋅h | [https://qudt.org/vocab/unit/W-HR](https://qudt.org/vocab/unit/W-HR) |
| energy:w-sec | W-SEC | Watt Second | W⋅s | [https://qudt.org/vocab/unit/W-SEC](https://qudt.org/vocab/unit/W-SEC) |


## Energy Density

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| energy_density:btu_it-per-ft3 | BTU_IT-PER-FT3 | British Thermal Unit (IT) Per Cubic Foot | Btu{IT}/ft³ | [https://qudt.org/vocab/unit/BTU_IT-PER-FT3](https://qudt.org/vocab/unit/BTU_IT-PER-FT3) |
| energy_density:btu_th-per-ft3 | BTU_TH-PER-FT3 | British Thermal Unit (TH) Per Cubic Foot | Btu{th}/ft³ | [https://qudt.org/vocab/unit/BTU_TH-PER-FT3](https://qudt.org/vocab/unit/BTU_TH-PER-FT3) |
| energy_density:j-per-m3 | J-PER-M3 | Joule per Cubic Meter | J/m³ | [https://qudt.org/vocab/unit/J-PER-M3](https://qudt.org/vocab/unit/J-PER-M3) |
| energy_density:megaj-per-m3 | MegaJ-PER-M3 | Megajoule Per Cubic Meter | MJ/m³ | [https://qudt.org/vocab/unit/MegaJ-PER-M3](https://qudt.org/vocab/unit/MegaJ-PER-M3) |
| energy_density:w-hr-per-m3 | W-HR-PER-M3 | Watthour per Cubic meter | W⋅h/m³ | [https://qudt.org/vocab/unit/W-HR-PER-M3](https://qudt.org/vocab/unit/W-HR-PER-M3) |


## Energy Per Area

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| energy_per_area:gigaj-per-m2 | GigaJ-PER-M2 | Gigajoule per Square Meter | GJ/m² | [https://qudt.org/vocab/unit/GigaJ-PER-M2](https://qudt.org/vocab/unit/GigaJ-PER-M2) |
| energy_per_area:j-per-m2 | J-PER-M2 | Joule per Square Meter | J/m² | [https://qudt.org/vocab/unit/J-PER-M2](https://qudt.org/vocab/unit/J-PER-M2) |
| energy_per_area:megaj-per-m2 | MegaJ-PER-M2 | Megajoule Per Square Meter | MJ/m² | [https://qudt.org/vocab/unit/MegaJ-PER-M2](https://qudt.org/vocab/unit/MegaJ-PER-M2) |


## Force

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| force:centin | CentiN | CentiNewton | cN | [https://qudt.org/vocab/unit/CentiN](https://qudt.org/vocab/unit/CentiN) |
| force:kilolb_f | KiloLB_F | KiloPound Force | klbf | [https://qudt.org/vocab/unit/KiloLB_F](https://qudt.org/vocab/unit/KiloLB_F) |
| force:kilon | KiloN | Kilonewton | kN | [https://qudt.org/vocab/unit/KiloN](https://qudt.org/vocab/unit/KiloN) |
| force:lb_f | LB_F | Pound Force | lbf | [https://qudt.org/vocab/unit/LB_F](https://qudt.org/vocab/unit/LB_F) |
| force:megalb_f | MegaLB_F | Mega Pound Force | Mlbf | [https://qudt.org/vocab/unit/MegaLB_F](https://qudt.org/vocab/unit/MegaLB_F) |
| force:n | N | newton | N | [https://qudt.org/vocab/unit/N](https://qudt.org/vocab/unit/N) |
| force:oz_f | OZ_F | Imperial Ounce Force | ozf | [https://qudt.org/vocab/unit/OZ_F](https://qudt.org/vocab/unit/OZ_F) |


## Frequency

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| frequency:hectohz | HectoHZ | Hectohertz | hHz |  |
| frequency:hz | HZ | hertz | Hz | [https://qudt.org/vocab/unit/HZ](https://qudt.org/vocab/unit/HZ) |
| frequency:kilohz | KiloHZ | Kilohertz | kHz | [https://qudt.org/vocab/unit/KiloHZ](https://qudt.org/vocab/unit/KiloHZ) |
| frequency:num-per-hr | NUM-PER-HR | Number per hour | #/h | [https://qudt.org/vocab/unit/NUM-PER-HR](https://qudt.org/vocab/unit/NUM-PER-HR) |
| frequency:num-per-sec | NUM-PER-SEC | Counts per second | #/s | [https://qudt.org/vocab/unit/NUM-PER-SEC](https://qudt.org/vocab/unit/NUM-PER-SEC) |
| frequency:num-per-yr | NUM-PER-YR | Number per Year | #/yr | [https://qudt.org/vocab/unit/NUM-PER-YR](https://qudt.org/vocab/unit/NUM-PER-YR) |
| frequency:per-min | PER-MIN | Reciprocal Minute | /min | [https://qudt.org/vocab/unit/PER-MIN](https://qudt.org/vocab/unit/PER-MIN) |


## Inductance

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| inductance:h | H | henry | H | [https://qudt.org/vocab/unit/H](https://qudt.org/vocab/unit/H) |
| inductance:microh | MicroH | Microhenry | µH | [https://qudt.org/vocab/unit/MicroH](https://qudt.org/vocab/unit/MicroH) |


## Inverse Length

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| inverse_length:per-m | PER-M | Reciprocal Meter | /m | [https://qudt.org/vocab/unit/PER-M](https://qudt.org/vocab/unit/PER-M) |


## Kinematic Viscosity

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| kinematic_viscosity:centist | CentiST | Centistokes | cSt | [https://qudt.org/vocab/unit/CentiST](https://qudt.org/vocab/unit/CentiST) |
| kinematic_viscosity:st | ST | Stokes | St | [https://qudt.org/vocab/unit/ST](https://qudt.org/vocab/unit/ST) |


## Length

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| length:centim | CentiM | Centimeter | cm | [https://qudt.org/vocab/unit/CentiM](https://qudt.org/vocab/unit/CentiM) |
| length:decim | DeciM | Decimeter | dm | [https://qudt.org/vocab/unit/DeciM](https://qudt.org/vocab/unit/DeciM) |
| length:ft | FT | Foot | ft | [https://qudt.org/vocab/unit/FT](https://qudt.org/vocab/unit/FT) |
| length:in | IN | Inch | in | [https://qudt.org/vocab/unit/IN](https://qudt.org/vocab/unit/IN) |
| length:kilom | KiloM | Kilometer | km | [https://qudt.org/vocab/unit/KiloM](https://qudt.org/vocab/unit/KiloM) |
| length:m | M | Meter | m | [https://qudt.org/vocab/unit/M](https://qudt.org/vocab/unit/M) |
| length:mi | MI | International Mile | mi | [https://qudt.org/vocab/unit/MI](https://qudt.org/vocab/unit/MI) |
| length:microm | MicroM | Micrometer | µm | [https://qudt.org/vocab/unit/MicroM](https://qudt.org/vocab/unit/MicroM) |
| length:milliin | MilliIN | Milli-inch | mil | [https://qudt.org/vocab/unit/MilliIN](https://qudt.org/vocab/unit/MilliIN) |
| length:millim | MilliM | Millimeter | mm | [https://qudt.org/vocab/unit/MilliM](https://qudt.org/vocab/unit/MilliM) |
| length:yd | YD | Yard | yd | [https://qudt.org/vocab/unit/YD](https://qudt.org/vocab/unit/YD) |


## Linear Density

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| linear_density:kilogm-per-m | KiloGM-PER-M | Kilogram per Meter | kg/m | [https://qudt.org/vocab/unit/KiloGM-PER-M](https://qudt.org/vocab/unit/KiloGM-PER-M) |


## Mass

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| mass:centigm | CentiGM | Centigram | cg | [https://qudt.org/vocab/unit/CentiGM](https://qudt.org/vocab/unit/CentiGM) |
| mass:gm | GM | Gram | g | [https://qudt.org/vocab/unit/GM](https://qudt.org/vocab/unit/GM) |
| mass:kilogm | KiloGM | kilogram | kg | [https://qudt.org/vocab/unit/KiloGM](https://qudt.org/vocab/unit/KiloGM) |
| mass:kilotonne | KiloTONNE | KiloTonne | kt | [https://qudt.org/vocab/unit/KiloTONNE](https://qudt.org/vocab/unit/KiloTONNE) |
| mass:lb | LB | Pound Mass | lbm | [https://qudt.org/vocab/unit/LB](https://qudt.org/vocab/unit/LB) |
| mass:milligm | MilliGM | Milligram | mg | [https://qudt.org/vocab/unit/MilliGM](https://qudt.org/vocab/unit/MilliGM) |
| mass:oz | OZ | Ounce Mass | oz | [https://qudt.org/vocab/unit/OZ](https://qudt.org/vocab/unit/OZ) |
| mass:tonne | TONNE | Tonne | t | [https://qudt.org/vocab/unit/TONNE](https://qudt.org/vocab/unit/TONNE) |


## Mass Concentration

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| mass_concentration:kilogm-per-m3 | KiloGM-PER-M3 | kilogram per cubic meter | kg/m³ | [https://qudt.org/vocab/unit/KiloGM-PER-M3](https://qudt.org/vocab/unit/KiloGM-PER-M3) |
| mass_concentration:milligm-per-millil | MilliGM-PER-MilliL | Milligram Per Milliliter | mg/mL | [https://qudt.org/vocab/unit/MilliGM-PER-MilliL](https://qudt.org/vocab/unit/MilliGM-PER-MilliL) |


## Mass Flow Rate

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| mass_flow_rate:gm-per-min | GM-PER-MIN | Gram Per Minute | g/min | [https://qudt.org/vocab/unit/GM-PER-MIN](https://qudt.org/vocab/unit/GM-PER-MIN) |
| mass_flow_rate:gm-per-sec | GM-PER-SEC | Gram Per Second | g/s | [https://qudt.org/vocab/unit/GM-PER-SEC](https://qudt.org/vocab/unit/GM-PER-SEC) |
| mass_flow_rate:kilogm-per-day | KiloGM-PER-DAY | Kilogram Per Day | kg/day | [https://qudt.org/vocab/unit/KiloGM-PER-DAY](https://qudt.org/vocab/unit/KiloGM-PER-DAY) |
| mass_flow_rate:kilogm-per-hr | KiloGM-PER-HR | Kilogram per Hour | kg/h | [https://qudt.org/vocab/unit/KiloGM-PER-HR](https://qudt.org/vocab/unit/KiloGM-PER-HR) |
| mass_flow_rate:kilogm-per-min | KiloGM-PER-MIN | Kilogram Per Minute | kg/min | [https://qudt.org/vocab/unit/KiloGM-PER-MIN](https://qudt.org/vocab/unit/KiloGM-PER-MIN) |
| mass_flow_rate:kilogm-per-sec | KiloGM-PER-SEC | Kilogram per Second | kg/s | [https://qudt.org/vocab/unit/KiloGM-PER-SEC](https://qudt.org/vocab/unit/KiloGM-PER-SEC) |
| mass_flow_rate:lb-per-hr | LB-PER-HR | Pound per Hour | lb/h | [https://qudt.org/vocab/unit/LB-PER-HR](https://qudt.org/vocab/unit/LB-PER-HR) |
| mass_flow_rate:lb-per-min | LB-PER-MIN | Pound per Minute | lb/min | [https://qudt.org/vocab/unit/LB-PER-MIN](https://qudt.org/vocab/unit/LB-PER-MIN) |
| mass_flow_rate:lb-per-sec | LB-PER-SEC | Pound (avoirdupois) Per Second | lb/s | [https://qudt.org/vocab/unit/LB-PER-SEC](https://qudt.org/vocab/unit/LB-PER-SEC) |
| mass_flow_rate:milligm-per-sec | MilliGM-PER-SEC | Milligram Per Second | mg/s | [https://qudt.org/vocab/unit/MilliGM-PER-SEC](https://qudt.org/vocab/unit/MilliGM-PER-SEC) |
| mass_flow_rate:tonne-per-day | TONNE-PER-DAY | Tonne Per Day | t/day | [https://qudt.org/vocab/unit/TONNE-PER-DAY](https://qudt.org/vocab/unit/TONNE-PER-DAY) |
| mass_flow_rate:tonne-per-hr | TONNE-PER-HR | Tonne Per Hour | t/h | [https://qudt.org/vocab/unit/TONNE-PER-HR](https://qudt.org/vocab/unit/TONNE-PER-HR) |
| mass_flow_rate:tonne-per-min | TONNE-PER-MIN | Tonne Per Minute | t/min | [https://qudt.org/vocab/unit/TONNE-PER-MIN](https://qudt.org/vocab/unit/TONNE-PER-MIN) |
| mass_flow_rate:tonne-per-sec | TONNE-PER-SEC | Tonne Per Second | t/s | [https://qudt.org/vocab/unit/TONNE-PER-SEC](https://qudt.org/vocab/unit/TONNE-PER-SEC) |


## Permeability

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| permeability:h-per-m | H-PER-M | Henry per Meter | H/m | [https://qudt.org/vocab/unit/H-PER-M](https://qudt.org/vocab/unit/H-PER-M) |


## Power

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| power:btu_it-per-hr | BTU_IT-PER-HR | BTU per Hour | Btu{IT}/h | [https://qudt.org/vocab/unit/BTU_IT-PER-HR](https://qudt.org/vocab/unit/BTU_IT-PER-HR) |
| power:btu_it-per-sec | BTU_IT-PER-SEC | BTU per Second | Btu{IT}/s | [https://qudt.org/vocab/unit/BTU_IT-PER-SEC](https://qudt.org/vocab/unit/BTU_IT-PER-SEC) |
| power:ft-lb_f-per-hr | FT-LB_F-PER-HR | Foot Pound Force per Hour | ft⋅lbf/h | [https://qudt.org/vocab/unit/FT-LB_F-PER-HR](https://qudt.org/vocab/unit/FT-LB_F-PER-HR) |
| power:ft-lb_f-per-min | FT-LB_F-PER-MIN | Foot Pound Force per Minute | ft⋅lbf/min | [https://qudt.org/vocab/unit/FT-LB_F-PER-MIN](https://qudt.org/vocab/unit/FT-LB_F-PER-MIN) |
| power:ft-lb_f-per-sec | FT-LB_F-PER-SEC | Foot Pound Force per Second | ft⋅lbf/s | [https://qudt.org/vocab/unit/FT-LB_F-PER-SEC](https://qudt.org/vocab/unit/FT-LB_F-PER-SEC) |
| power:gigaw | GigaW | Gigawatt | GW | [https://qudt.org/vocab/unit/GigaW](https://qudt.org/vocab/unit/GigaW) |
| power:hp | HP | Horsepower | HP | [https://qudt.org/vocab/unit/HP](https://qudt.org/vocab/unit/HP) |
| power:j-per-hr | J-PER-HR | Joule Per Hour | J/h | [https://qudt.org/vocab/unit/J-PER-HR](https://qudt.org/vocab/unit/J-PER-HR) |
| power:j-per-sec | J-PER-SEC | Joule Per Second | J/s | [https://qudt.org/vocab/unit/J-PER-SEC](https://qudt.org/vocab/unit/J-PER-SEC) |
| power:kilobtu_it-per-hr | KiloBTU_IT-PER-HR | Kilo British Thermal Unit (International Definition) per Hour | kBtu{IT}/h | [https://qudt.org/vocab/unit/KiloBTU_IT-PER-HR](https://qudt.org/vocab/unit/KiloBTU_IT-PER-HR) |
| power:kilocal-per-min | KiloCAL-PER-MIN | Kilocalorie Per Minute | kcal/min | [https://qudt.org/vocab/unit/KiloCAL-PER-MIN](https://qudt.org/vocab/unit/KiloCAL-PER-MIN) |
| power:kilocal-per-sec | KiloCAL-PER-SEC | Kilocalorie Per Second | kcal/s | [https://qudt.org/vocab/unit/KiloCAL-PER-SEC](https://qudt.org/vocab/unit/KiloCAL-PER-SEC) |
| power:kilov-a | KiloV-A | Kilovolt Ampere | kV⋅A | [https://qudt.org/vocab/unit/KiloV-A](https://qudt.org/vocab/unit/KiloV-A) |
| power:kilow | KiloW | Kilowatt | kW | [https://qudt.org/vocab/unit/KiloW](https://qudt.org/vocab/unit/KiloW) |
| power:megabtu_it-per-hr | MegaBTU_IT-PER-HR | Mega British Thermal Unit (International Definition) per Hour | MBtu{IT}/h | [https://qudt.org/vocab/unit/MegaBTU_IT-PER-HR](https://qudt.org/vocab/unit/MegaBTU_IT-PER-HR) |
| power:megaj-per-sec | MegaJ-PER-SEC | Megajoule Per Second | MJ/s | [https://qudt.org/vocab/unit/MegaJ-PER-SEC](https://qudt.org/vocab/unit/MegaJ-PER-SEC) |
| power:megav-a | MegaV-A | Megavolt Ampere | MV⋅A | [https://qudt.org/vocab/unit/MegaV-A](https://qudt.org/vocab/unit/MegaV-A) |
| power:megaw | MegaW | MegaW | MW | [https://qudt.org/vocab/unit/MegaW](https://qudt.org/vocab/unit/MegaW) |
| power:milliw | MilliW | MilliW | mW | [https://qudt.org/vocab/unit/MilliW](https://qudt.org/vocab/unit/MilliW) |
| power:teraw | TeraW | Terawatt | TW | [https://qudt.org/vocab/unit/TeraW](https://qudt.org/vocab/unit/TeraW) |
| power:v-a | V-A | Volt Ampere | V⋅A | [https://qudt.org/vocab/unit/V-A](https://qudt.org/vocab/unit/V-A) |
| power:w | W | watt | W | [https://qudt.org/vocab/unit/W](https://qudt.org/vocab/unit/W) |


## Power Per Area

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| power_per_area:w-per-m2 | W-PER-M2 | Watt per square meter | W/m² | [https://qudt.org/vocab/unit/W-PER-M2](https://qudt.org/vocab/unit/W-PER-M2) |


## Pressure

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| pressure:atm | ATM | Standard Atmosphere | atm | [https://qudt.org/vocab/unit/ATM](https://qudt.org/vocab/unit/ATM) |
| pressure:bar | BAR | Bar | bar | [https://qudt.org/vocab/unit/BAR](https://qudt.org/vocab/unit/BAR) |
| pressure:barg | BARg | Bar(g) | bar(g) | [https://rds.posccaesar.org/ontology/plm/rdl/PCA_100003649/](https://rds.posccaesar.org/ontology/plm/rdl/PCA_100003649/) |
| pressure:gigapa | GigaPA | Gigapascal | GPa | [https://qudt.org/vocab/unit/GigaPA](https://qudt.org/vocab/unit/GigaPA) |
| pressure:hectopa | HectoPA | Hectopascal | hPa | [https://qudt.org/vocab/unit/HectoPA](https://qudt.org/vocab/unit/HectoPA) |
| pressure:kilobar | KiloBAR | Kilobar | kbar | [https://qudt.org/vocab/unit/KiloBAR](https://qudt.org/vocab/unit/KiloBAR) |
| pressure:kilogm-per-m-sec2 | KiloGM-PER-M-SEC2 | Kilograms per meter per square second | kg/(m⋅s²) | [https://qudt.org/vocab/unit/KiloGM-PER-M-SEC2](https://qudt.org/vocab/unit/KiloGM-PER-M-SEC2) |
| pressure:kilogm_f-per-centim2 | KiloGM_F-PER-CentiM2 | Kilogram Force per Square Centimeter | kgf/cm² | [https://qudt.org/vocab/unit/KiloGM_F-PER-CentiM2](https://qudt.org/vocab/unit/KiloGM_F-PER-CentiM2) |
| pressure:kilolb_f-per-in2 | KiloLB_F-PER-IN2 | Kilopound Force Per Square Inch | kpsi | [https://qudt.org/vocab/unit/KiloLB_F-PER-IN2](https://qudt.org/vocab/unit/KiloLB_F-PER-IN2) |
| pressure:kilopa | KiloPA | Kilopascal | kPa | [https://qudt.org/vocab/unit/KiloPA](https://qudt.org/vocab/unit/KiloPA) |
| pressure:kilopag | KiloPAg | Kilopascal(g) | kPa(g) | [https://rds.posccaesar.org/ontology/plm/rdl/PCA_100005357](https://rds.posccaesar.org/ontology/plm/rdl/PCA_100005357) |
| pressure:lb_f-per-ft2 | LB_F-PER-FT2 | Pound Force per Square Foot | lbf/ft² | [https://qudt.org/vocab/unit/LB_F-PER-FT2](https://qudt.org/vocab/unit/LB_F-PER-FT2) |
| pressure:megabar | MegaBAR | Megabar | Mbar | [https://qudt.org/vocab/unit/MegaBAR](https://qudt.org/vocab/unit/MegaBAR) |
| pressure:megapa | MegaPA | Megapascal | MPa | [https://qudt.org/vocab/unit/MegaPA](https://qudt.org/vocab/unit/MegaPA) |
| pressure:micropa | MicroPA | Micropascal | μPa | [https://qudt.org/vocab/unit/MicroPA](https://qudt.org/vocab/unit/MicroPA) |
| pressure:millibar | MilliBAR | Millibar | mbar | [https://qudt.org/vocab/unit/MilliBAR](https://qudt.org/vocab/unit/MilliBAR) |
| pressure:millim_h2o | MilliM_H2O | Millimeter Of Water | mmH₂0 | [https://qudt.org/vocab/unit/MilliM_H2O](https://qudt.org/vocab/unit/MilliM_H2O) |
| pressure:millipa | MilliPA | Millipascal | mPa | [https://qudt.org/vocab/unit/MilliPA](https://qudt.org/vocab/unit/MilliPA) |
| pressure:n-per-centim2 | N-PER-CentiM2 | Newton Per Square Centimeter | N/cm² | [https://qudt.org/vocab/unit/N-PER-CentiM2](https://qudt.org/vocab/unit/N-PER-CentiM2) |
| pressure:n-per-m2 | N-PER-M2 | Newtons Per Square Meter | N/m² | [https://qudt.org/vocab/unit/N-PER-M2](https://qudt.org/vocab/unit/N-PER-M2) |
| pressure:n-per-millim2 | N-PER-MilliM2 | Newton Per Square Millimeter | N/mm² | [https://qudt.org/vocab/unit/N-PER-MilliM2](https://qudt.org/vocab/unit/N-PER-MilliM2) |
| pressure:pa | PA | pascal | Pa | [https://qudt.org/vocab/unit/PA](https://qudt.org/vocab/unit/PA) |
| pressure:psi | PSI | Pound Force per Square Inch | psi | [https://qudt.org/vocab/unit/PSI](https://qudt.org/vocab/unit/PSI) |
| pressure:psig | PSIg | Pound Force per Square Inch (gauge) | psi(g) |  |


## Pressure Gradient

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| pressure_gradient:bar-per-m | BAR-PER-M | Bar per meter | bar/m |  |
| pressure_gradient:kilopa-per-m | KiloPA-PER-M | Kilopascal per meter | kPa/m | [https://qudt.org/vocab/unit/KiloPA-PER-M](https://qudt.org/vocab/unit/KiloPA-PER-M) |
| pressure_gradient:pa-per-m | PA-PER-M | Pascal per meter | Pa/m |  |
| pressure_gradient:psi-per-ft | PSI-PER-FT | Pound Force per Square Inch per Foot | psi/ft |  |
| pressure_gradient:psi-per-m | PSI-PER-M | Pound Force per Square Inch per Meter | psi/m |  |


## Resistance

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| resistance:kiloohm | KiloOHM | Kiloohm | kΩ | [https://qudt.org/vocab/unit/KiloOHM](https://qudt.org/vocab/unit/KiloOHM) |
| resistance:milliohm | MilliOHM | Milliohm | mΩ | [https://qudt.org/vocab/unit/MilliOHM](https://qudt.org/vocab/unit/MilliOHM) |
| resistance:ohm | OHM | ohm | Ω | [https://qudt.org/vocab/unit/OHM](https://qudt.org/vocab/unit/OHM) |


## Surface Tension

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| surface_tension:ft-lb_f-per-ft2 | FT-LB_F-PER-FT2 | Foot Pound per Square Foot | ft⋅lbf/ft² | [https://qudt.org/vocab/unit/FT-LB_F-PER-FT2](https://qudt.org/vocab/unit/FT-LB_F-PER-FT2) |
| surface_tension:j-per-centim2 | J-PER-CentiM2 | Joule Per Square Centimeter | J/cm² | [https://qudt.org/vocab/unit/J-PER-CentiM2](https://qudt.org/vocab/unit/J-PER-CentiM2) |
| surface_tension:j-per-m2 | J-PER-M2 | Joule per Square Meter | J/m² | [https://qudt.org/vocab/unit/J-PER-M2](https://qudt.org/vocab/unit/J-PER-M2) |
| surface_tension:kilogm-per-sec2 | KiloGM-PER-SEC2 | Kilogram per Square Second | kg/s² | [https://qudt.org/vocab/unit/KiloGM-PER-SEC2](https://qudt.org/vocab/unit/KiloGM-PER-SEC2) |
| surface_tension:megaj-per-m2 | MegaJ-PER-M2 | Megajoule Per Square Meter | MJ/m² | [https://qudt.org/vocab/unit/MegaJ-PER-M2](https://qudt.org/vocab/unit/MegaJ-PER-M2) |
| surface_tension:n-m-per-m2 | N-M-PER-M2 | Newton Meter Per Square Meter | N⋅m/m² | [https://qudt.org/vocab/unit/N-M-PER-M2](https://qudt.org/vocab/unit/N-M-PER-M2) |
| surface_tension:w-hr-per-m2 | W-HR-PER-M2 | Watt hour per square meter | W⋅h/m² | [https://qudt.org/vocab/unit/W-HR-PER-M2](https://qudt.org/vocab/unit/W-HR-PER-M2) |
| surface_tension:w-sec-per-m2 | W-SEC-PER-M2 | Watt seconds per square meter | W⋅s/m² | [https://qudt.org/vocab/unit/W-SEC-PER-M2](https://qudt.org/vocab/unit/W-SEC-PER-M2) |


## Temperature

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| temperature:deg_c | DEG_C | degree Celsius | °C | [https://qudt.org/vocab/unit/DEG_C](https://qudt.org/vocab/unit/DEG_C) |
| temperature:deg_f | DEG_F | Degree Fahrenheit | °F | [https://qudt.org/vocab/unit/DEG_F](https://qudt.org/vocab/unit/DEG_F) |
| temperature:deg_r | DEG_R | Degree Rankine | °R | [https://qudt.org/vocab/unit/DEG_R](https://qudt.org/vocab/unit/DEG_R) |
| temperature:k | K | kelvin | K | [https://qudt.org/vocab/unit/K](https://qudt.org/vocab/unit/K) |


## Temperature Gradient

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| temperature_gradient:k-per-m | K-PER-M | Degrees Kelvin per meter | K/m | [https://qudt.org/vocab/unit/K-PER-M](https://qudt.org/vocab/unit/K-PER-M) |


## Time

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| time:day | DAY | Day | day | [https://qudt.org/vocab/unit/DAY](https://qudt.org/vocab/unit/DAY) |
| time:hr | HR | Hour | h | [https://qudt.org/vocab/unit/HR](https://qudt.org/vocab/unit/HR) |
| time:microsec | MicroSEC | microsecond | µs | [https://qudt.org/vocab/unit/MicroSEC](https://qudt.org/vocab/unit/MicroSEC) |
| time:millisec | MilliSEC | millisecond | ms | [https://qudt.org/vocab/unit/MilliSEC](https://qudt.org/vocab/unit/MilliSEC) |
| time:min | MIN | Minute | min | [https://qudt.org/vocab/unit/MIN](https://qudt.org/vocab/unit/MIN) |
| time:mo | MO | Month | mo |  |
| time:sec | SEC | second | s | [https://qudt.org/vocab/unit/SEC](https://qudt.org/vocab/unit/SEC) |
| time:wk | WK | Week | wk | [https://qudt.org/vocab/unit/WK](https://qudt.org/vocab/unit/WK) |
| time:yr | YR | Year | yr | [https://qudt.org/vocab/unit/YR](https://qudt.org/vocab/unit/YR) |


## Torque

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| torque:lb_f-ft | LB_F-FT | Pound Force Foot | lbf⋅ft | [https://qudt.org/vocab/unit/LB_F-FT](https://qudt.org/vocab/unit/LB_F-FT) |
| torque:lb_f-in | LB_F-IN | Pound Force Inch | lbf⋅in | [https://qudt.org/vocab/unit/LB_F-IN](https://qudt.org/vocab/unit/LB_F-IN) |
| torque:n-m | N-M | Newton Meter | N⋅m | [https://qudt.org/vocab/unit/N-M](https://qudt.org/vocab/unit/N-M) |


## Unknown

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| unknown:unknown | Unknown | Unknown unit |  |  |


## Velocity

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| velocity:ft-per-hr | FT-PER-HR | Foot per Hour | ft/h | [https://qudt.org/vocab/unit/FT-PER-HR](https://qudt.org/vocab/unit/FT-PER-HR) |
| velocity:ft-per-sec | FT-PER-SEC | Foot per Second | ft/s | [https://qudt.org/vocab/unit/FT-PER-SEC](https://qudt.org/vocab/unit/FT-PER-SEC) |
| velocity:in-per-sec | IN-PER-SEC | Inch per Second | in/s | [https://qudt.org/vocab/unit/IN-PER-SEC](https://qudt.org/vocab/unit/IN-PER-SEC) |
| velocity:kilom-per-hr | KiloM-PER-HR | Kilometer per Hour | km/h | [https://qudt.org/vocab/unit/KiloM-PER-HR](https://qudt.org/vocab/unit/KiloM-PER-HR) |
| velocity:kilom-per-sec | KiloM-PER-SEC | Kilometer per Second | km/s | [https://qudt.org/vocab/unit/KiloM-PER-SEC](https://qudt.org/vocab/unit/KiloM-PER-SEC) |
| velocity:kn | KN | Knot | kn | [https://qudt.org/vocab/unit/KN](https://qudt.org/vocab/unit/KN) |
| velocity:m-per-hr | M-PER-HR | Meter per Hour | m/h | [https://qudt.org/vocab/unit/M-PER-HR](https://qudt.org/vocab/unit/M-PER-HR) |
| velocity:m-per-sec | M-PER-SEC | Meter per Second | m/s | [https://qudt.org/vocab/unit/M-PER-SEC](https://qudt.org/vocab/unit/M-PER-SEC) |
| velocity:mi-per-hr | MI-PER-HR | Mile per Hour | mi/h | [https://qudt.org/vocab/unit/MI-PER-HR](https://qudt.org/vocab/unit/MI-PER-HR) |
| velocity:mi-per-sec | MI-PER-SEC | Mile per Second | mi/s | [https://qudt.org/vocab/unit/MI-PER-SEC](https://qudt.org/vocab/unit/MI-PER-SEC) |
| velocity:millim-per-hr | MilliM-PER-HR | Millimeter Per Hour | mm/h | [https://qudt.org/vocab/unit/MilliM-PER-HR](https://qudt.org/vocab/unit/MilliM-PER-HR) |
| velocity:millim-per-sec | MilliM-PER-SEC | Millimeter Per Second | mm/s | [https://qudt.org/vocab/unit/MilliM-PER-SEC](https://qudt.org/vocab/unit/MilliM-PER-SEC) |


## Volume

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| volume:bbl_us | BBL_US | Barrel (US) | bbl | [https://qudt.org/vocab/unit/BBL_US](https://qudt.org/vocab/unit/BBL_US) |
| volume:centim3 | CentiM3 | cubic centimeter | cm³ | [https://qudt.org/vocab/unit/CentiM3](https://qudt.org/vocab/unit/CentiM3) |
| volume:decil | DeciL | Decilitre | dL | [https://qudt.org/vocab/unit/DeciL](https://qudt.org/vocab/unit/DeciL) |
| volume:decim3 | DeciM3 | Cubic Decimeter | dm³ | [https://qudt.org/vocab/unit/DeciM3](https://qudt.org/vocab/unit/DeciM3) |
| volume:ft3 | FT3 | Cubic Foot | ft³ | [https://qudt.org/vocab/unit/FT3](https://qudt.org/vocab/unit/FT3) |
| volume:gal_uk | GAL_UK | Gallon (UK) | gal{UK} | [https://qudt.org/vocab/unit/GAL_UK](https://qudt.org/vocab/unit/GAL_UK) |
| volume:in3 | IN3 | Cubic Inch | in³ | [https://qudt.org/vocab/unit/IN3](https://qudt.org/vocab/unit/IN3) |
| volume:kilo-ft3 | Kilo-FT3 | Thousand Cubic Foot | k.ft³ | [https://qudt.org/vocab/unit/Kilo-FT3](https://qudt.org/vocab/unit/Kilo-FT3) |
| volume:kilol | KiloL | Kilolitre | kL | [https://qudt.org/vocab/unit/KiloL](https://qudt.org/vocab/unit/KiloL) |
| volume:l | L | Liter | L | [https://qudt.org/vocab/unit/L](https://qudt.org/vocab/unit/L) |
| volume:m3 | M3 | Cubic Meter | m³ | [https://qudt.org/vocab/unit/M3](https://qudt.org/vocab/unit/M3) |
| volume:mega-ft3 | Mega-FT3 | Million Standard Cubic Feet | MMscf |  |
| volume:mega-m3 | Mega-M3 | Million Standard Cubic Meter | MMscm |  |
| volume:megal | MegaL | Megalitre | ML | [https://qudt.org/vocab/unit/MegaL](https://qudt.org/vocab/unit/MegaL) |
| volume:microl | MicroL | Microlitre | μL | [https://qudt.org/vocab/unit/MicroL](https://qudt.org/vocab/unit/MicroL) |
| volume:microm3 | MicroM3 | Cubic micrometers (microns) | µm³ | [https://qudt.org/vocab/unit/MicroM3](https://qudt.org/vocab/unit/MicroM3) |
| volume:millil | MilliL | Millilitre | mL | [https://qudt.org/vocab/unit/MilliL](https://qudt.org/vocab/unit/MilliL) |
| volume:millim3 | MilliM3 | Cubic Millimeter | mm³ | [https://qudt.org/vocab/unit/MilliM3](https://qudt.org/vocab/unit/MilliM3) |
| volume:yd3 | YD3 | Cubic Yard | yd³ | [https://qudt.org/vocab/unit/YD3](https://qudt.org/vocab/unit/YD3) |


## Volume Flow Rate

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| volume_flow_rate:bbl_us-per-day | BBL_US-PER-DAY | Barrel (US) Per Day | bbl/day | [https://qudt.org/vocab/unit/BBL_US-PER-DAY](https://qudt.org/vocab/unit/BBL_US-PER-DAY) |
| volume_flow_rate:centim3-per-sec | CentiM3-PER-SEC | Cubic Centimeter Per Second | cm³/s | [https://qudt.org/vocab/unit/CentiM3-PER-SEC](https://qudt.org/vocab/unit/CentiM3-PER-SEC) |
| volume_flow_rate:decim3-per-min | DeciM3-PER-MIN | Cubic Decimeter Per Minute | dm³/min | [https://qudt.org/vocab/unit/DeciM3-PER-MIN](https://qudt.org/vocab/unit/DeciM3-PER-MIN) |
| volume_flow_rate:decim3-per-sec | DeciM3-PER-SEC | Cubic Decimeter Per Second | dm³/s | [https://qudt.org/vocab/unit/DeciM3-PER-SEC](https://qudt.org/vocab/unit/DeciM3-PER-SEC) |
| volume_flow_rate:ft3-per-day | FT3-PER-DAY | Cubic Foot Per Day | ft³/day | [https://qudt.org/vocab/unit/FT3-PER-DAY](https://qudt.org/vocab/unit/FT3-PER-DAY) |
| volume_flow_rate:ft3-per-hr | FT3-PER-HR | Cubic Foot Per Hour | ft³/h | [https://qudt.org/vocab/unit/FT3-PER-HR](https://qudt.org/vocab/unit/FT3-PER-HR) |
| volume_flow_rate:ft3-per-min | FT3-PER-MIN | Cubic Foot per Minute | ft³/min | [https://qudt.org/vocab/unit/FT3-PER-MIN](https://qudt.org/vocab/unit/FT3-PER-MIN) |
| volume_flow_rate:ft3-per-sec | FT3-PER-SEC | Cubic Foot per Second | ft³/s | [https://qudt.org/vocab/unit/FT3-PER-SEC](https://qudt.org/vocab/unit/FT3-PER-SEC) |
| volume_flow_rate:gal_uk-per-hr | GAL_UK-PER-HR | Gallon (UK) Per Hour | gal{UK}/h | [https://qudt.org/vocab/unit/GAL_UK-PER-HR](https://qudt.org/vocab/unit/GAL_UK-PER-HR) |
| volume_flow_rate:gal_uk-per-min | GAL_UK-PER-MIN | Gallon (UK) Per Minute | gal{UK}/min | [https://qudt.org/vocab/unit/GAL_UK-PER-MIN](https://qudt.org/vocab/unit/GAL_UK-PER-MIN) |
| volume_flow_rate:gal_uk-per-sec | GAL_UK-PER-SEC | Gallon (UK) Per Second | gal{UK}/s | [https://qudt.org/vocab/unit/GAL_UK-PER-SEC](https://qudt.org/vocab/unit/GAL_UK-PER-SEC) |
| volume_flow_rate:in3-per-sec | IN3-PER-SEC | Cubic Inch Per Second | in³/s | [https://qudt.org/vocab/unit/IN3-PER-SEC](https://qudt.org/vocab/unit/IN3-PER-SEC) |
| volume_flow_rate:kilo-ft3-per-day | Kilo-FT3-PER-DAY | Thousand Standard Cubic Feet per Day | kft³/day |  |
| volume_flow_rate:kilol-per-hr | KiloL-PER-HR | Kilolitre Per Hour | kL/h | [https://qudt.org/vocab/unit/KiloL-PER-HR](https://qudt.org/vocab/unit/KiloL-PER-HR) |
| volume_flow_rate:l-per-day | L-PER-DAY | Liter Per Day | L/day | [https://qudt.org/vocab/unit/L-PER-DAY](https://qudt.org/vocab/unit/L-PER-DAY) |
| volume_flow_rate:l-per-hr | L-PER-HR | Liter Per Hour | L/hr | [https://qudt.org/vocab/unit/L-PER-HR](https://qudt.org/vocab/unit/L-PER-HR) |
| volume_flow_rate:l-per-min | L-PER-MIN | Liter Per Minute | L/min | [https://qudt.org/vocab/unit/L-PER-MIN](https://qudt.org/vocab/unit/L-PER-MIN) |
| volume_flow_rate:l-per-sec | L-PER-SEC | Liter Per Second | L/s | [https://qudt.org/vocab/unit/L-PER-SEC](https://qudt.org/vocab/unit/L-PER-SEC) |
| volume_flow_rate:m3-per-day | M3-PER-DAY | Cubic Meter Per Day | m³/day | [https://qudt.org/vocab/unit/M3-PER-DAY](https://qudt.org/vocab/unit/M3-PER-DAY) |
| volume_flow_rate:m3-per-hr | M3-PER-HR | Cubic Meter per Hour | m³/h | [https://qudt.org/vocab/unit/M3-PER-HR](https://qudt.org/vocab/unit/M3-PER-HR) |
| volume_flow_rate:m3-per-min | M3-PER-MIN | Cubic Meter Per Minute | m³/min | [https://qudt.org/vocab/unit/M3-PER-MIN](https://qudt.org/vocab/unit/M3-PER-MIN) |
| volume_flow_rate:m3-per-sec | M3-PER-SEC | Cubic Meter per Second | m³/s | [https://qudt.org/vocab/unit/M3-PER-SEC](https://qudt.org/vocab/unit/M3-PER-SEC) |
| volume_flow_rate:mega-ft3-per-day | Mega-FT3-PER-DAY | Million Standard Cubic Feet per Day | MMscf/day | [https://rds.posccaesar.org/ontology/plm/rdl/PCA_100005446/](https://rds.posccaesar.org/ontology/plm/rdl/PCA_100005446/) |
| volume_flow_rate:millil-per-sec | MilliL-PER-SEC | Millilitre Per Second | mL/s | [https://qudt.org/vocab/unit/MilliL-PER-SEC](https://qudt.org/vocab/unit/MilliL-PER-SEC) |
| volume_flow_rate:yd3-per-day | YD3-PER-DAY | Cubic Yard Per Day | yd³/day | [https://qudt.org/vocab/unit/YD3-PER-DAY](https://qudt.org/vocab/unit/YD3-PER-DAY) |
| volume_flow_rate:yd3-per-hr | YD3-PER-HR | Cubic Yard Per Hour | yd³/h | [https://qudt.org/vocab/unit/YD3-PER-HR](https://qudt.org/vocab/unit/YD3-PER-HR) |
| volume_flow_rate:yd3-per-min | YD3-PER-MIN | Cubic Yard per Minute | yd³/min | [https://qudt.org/vocab/unit/YD3-PER-MIN](https://qudt.org/vocab/unit/YD3-PER-MIN) |
| volume_flow_rate:yd3-per-sec | YD3-PER-SEC | Cubic Yard Per Second | yd³/s | [https://qudt.org/vocab/unit/YD3-PER-SEC](https://qudt.org/vocab/unit/YD3-PER-SEC) |


## Volume Fraction

| externalId  | name       | longName                  | symbol      | source             |
|-------------|------------|---------------------------|-------------|--------------------|
| volume_fraction:centim3-per-centim3 | CentiM3-PER-CentiM3 | Cubic Centimeter Per Cubic Centimeter | cm³/cm³ | [https://qudt.org/vocab/unit/CentiM3-PER-CentiM3](https://qudt.org/vocab/unit/CentiM3-PER-CentiM3) |
| volume_fraction:centim3-per-m3 | CentiM3-PER-M3 | Cubic Centimeter Per Cubic Meter | cm³/m³ | [https://qudt.org/vocab/unit/CentiM3-PER-M3](https://qudt.org/vocab/unit/CentiM3-PER-M3) |
| volume_fraction:l-per-l | L-PER-L | Liter Per Liter | L/L | [https://qudt.org/vocab/unit/L-PER-L](https://qudt.org/vocab/unit/L-PER-L) |
| volume_fraction:m3-per-m3 | M3-PER-M3 | Cubic Meter Per Cubic Meter | m³/m³ | [https://qudt.org/vocab/unit/M3-PER-M3](https://qudt.org/vocab/unit/M3-PER-M3) |
| volume_fraction:microl-per-l | MicroL-PER-L | Microlitre Per Liter | μL/L | [https://qudt.org/vocab/unit/MicroL-PER-L](https://qudt.org/vocab/unit/MicroL-PER-L) |
| volume_fraction:millil-per-l | MilliL-PER-L | Millilitre Per Liter | mL/L | [https://qudt.org/vocab/unit/MilliL-PER-L](https://qudt.org/vocab/unit/MilliL-PER-L) |
| volume_fraction:millil-per-m3 | MilliL-PER-M3 | Millilitre Per Cubic Meter | mL/m³ | [https://qudt.org/vocab/unit/MilliL-PER-M3](https://qudt.org/vocab/unit/MilliL-PER-M3) |



<!-- SOURCE_END: neat/docs\data_modeling\excel\physical\units.md -->

================================================================================


<!-- SOURCE_START: neat/docs\data_modeling\excel\physical\views.md -->
## File: neat/docs\data_modeling\excel\physical\views.md

# View

## Filter
Filters are used to specify which nodes or edges should be returned when querying a view. We recommend having a
unique container for each view, and use the default filter set by the API.

**NEAT** physical data model are one-to-one with the CDF API specification for creating Data Models.
Currently we are not doing any deep validation of the filters except for basic syntax checking.
Therefore, use the filters with caution. A filter is set by writing raw JSON strings in the `Filter` column, e.g.:

| View              | Implements          | Filter                                                                                                                        |
|-------------------|---------------------|-------------------------------------------------------------------------------------------------------------------------------|
| Cognite360ImageStation | CogniteDescribable | {"and": [{"hasData": [{"type": "container", "space": "cdf_cdm_3d", "externalId": "Cognite3DGroup"}]}, {"equals": {"property": ["cdf_cdm_3d", "Cognite3DGroup", "groupType"], "value": "Station360"}}]}|


BEWARE to properly form the JSON string, as it is easy to make mistakes. The JSON string must be a valid JSON object!

<!-- SOURCE_END: neat/docs\data_modeling\excel\physical\views.md -->

================================================================================


<!-- SOURCE_START: neat/docs\data_modeling\modes.md -->
## File: neat/docs\data_modeling\modes.md

NEAT supports two modes of data modeling: additive and rebuild. Depending on the selected mode, the behavior of NEAT when deploying changes to a data model in Cognite Data Fusion (CDF) will vary. NEAT classifies changes to the data model into three severity levels:

- ✅ `SAFE`: allowed changes that do not impact existing data or functionality
- ⚠️ `WARNING`: allowed changes where caution is advised, as they may have implications
- ❌ `BREAKING`: disallowed changes, which would not be applied to CDF


In the following sections, we describe the characteristics of each mode and provide detailed tables outlining the allowed and disallowed changes for each severity level.


!!! warning "Changes can be done only in the data model space"
    It is important to know that NEAT will only consider changes to the data model components (aka DMS schema components) in the same space as the data model itself. Any change outside of the data model space will be ignored. The reason for this is to avoid accidental changes to data models that have different owners or are managed by different teams.


## Additive Mode

Key characteristics of the additive mode are:

- Forward compatibility: old versions of applications and services can work with the updated data model without requiring changes.
- Incremental changes: data model changes are applied incrementally, allowing for gradual evolution of the data model.
- Suitable for production environments: this mode is ideal for production systems where stability and forward compatibility are crucial.


The following table summarizes the allowed and disallowed changes in additive mode:

| Severity | Operation | Change Description | Example |
|----------|-----------|-------------------|---------|
| **SAFE** | Create | Data model | - |
| **SAFE** | Create | View | Entire views, new properties, filters |
| **SAFE** | Create | Container | Entire containers, new properties, indexes and constraints |
| **SAFE** | Create | New enums to existing enum properties | - |
| **SAFE** | Update | Data model metadata | Name, description, version |
| **SAFE** | Update | Data model's view order | - |
| **SAFE** | Update | View metadata | Name, description, version |
| **SAFE** | Update | Container metadata | Name, description |
| **SAFE** | Update | Property metadata | Name, description |
| **SAFE** | Update | Enums metadata | Name, description |
| **WARNING** | Update | View filters | - |
| **WARNING** | Update | Edge type | - |
| **WARNING** | Update | Source property for reverse connection | Also known as `through` in CDF API |
| **WARNING** | Remove | Container constraints | - |
| **WARNING** | Remove | Container indexes | - |
| **WARNING** | Update | Mutability of container properties | - |
| **WARNING** | Update | Auto-increment of container properties | - |
| **WARNING** | Update | Default value of container properties | - |
| **WARNING** | Update | Max count of listable properties | - |
| **WARNING** | Remove | Container property unit | Removing `meter` from a property |
| **WARNING** | Add | Container property unit | Adding `meter` to a property that did not have a unit before |
| **BREAKING** | Delete | Existing data model | - |
| **BREAKING** | Delete | View reference from the data model | - |
| **BREAKING** | Delete | Existing view | - |
| **BREAKING** | Add | Implements to an existing view | - |
| **BREAKING** | Delete | Property from an existing view | - |
| **BREAKING** | Update | Mapping of view (property) to container (property) | - |
| **BREAKING** | Update | Direct/reverse connection value type | Also known as `source` in CDF API |
| **BREAKING** | Update | Edge source of properties | Setting new view as source of properties |
| **BREAKING** | Update | Edge direction | - |
| **BREAKING** | Update | Connection type | From edge to direct connection |
| **BREAKING** | Update | What container is used for | Switching `usedFor` from `node` to `edge` |
| **BREAKING** | Update | Value type of container property | From `text` to `integer` |
| **BREAKING** | Update | Container constraint definitions | `uniqueness` constraint property `bySpace` cannot be changed |
| **BREAKING** | Update | Container index definitions | Adding or removing properties to which the index applies |
| **BREAKING** | Update | Nullability of container properties | - |
| **BREAKING** | Update | Container property unit | Changing from `meter` to `kilometer` |
| **BREAKING** | Update | `maxTextSize` of text properties | - |
| **BREAKING** | Delete | Enum values from existing enum properties | - |



## Rebuild Mode

Unlike additive mode, rebuild mode allows every change to be applied to the data model, as it is assumed that the entire data model will be rebuilt from scratch. This mode is suitable where there is no existing data or applications depending on the current data model, such as in development or testing environments, since every component of the data model will be first deleted and then recreated. In case when containers that are to be deleted contain data, deployment in the rebuild mode will fail, and NEAT will not accidentally remove data. However, if you are intentionally looking to delete existing data, you will have to set `drop_data=True` when deploying the data model via command `neat.physical_data_model.write.cdf(dry_run = False, drop_data = True)`.

<!-- SOURCE_END: neat/docs\data_modeling\modes.md -->

================================================================================


<!-- SOURCE_START: neat/docs\data_modeling\principles.md -->
## File: neat/docs\data_modeling\principles.md

# Data Modeling Principles

This document contains a list of principles and best practices for data modeling within an organization. These
principles are not strict rules, but it is an attempt to provide a concise set of guidelines based on
the most up-to-date experience of the **NEAT** team. Note that several of the best practices are
implemented into **NEAT** and you will get warnings if you do not follow them.

This document first presents the principles and then the best practices without justification. This is to provide
a quick reference for the reader. The justification for each principle and best practice is provided in the
[Justification](#justification) section.

**Pre-requisites**: This document assumes that the reader is familiar with the Cognite Data Fusion (CDF) data modeling
concepts `Space`, `Data Model` and `View`.

## Principles

1. Data models should be designed for real use cases, not for the sake of modeling alone.
2. The essence of data modeling is **cooperation**: It is how people work together to create a shared understanding
   of the business and data.
3. Parsimonious Model. Data models should be as simple as possible, but not simpler.

## Best Practices

1. Establish a data governance team that will ensure implementation of the best practices
2. Create one main data model, the Enterprise Data Model, that is shared across the organization.
3. Each business area can create one or more specific data models based on the Enterprise Data Model, called
   Solution Data Models.
4. Solution Data Models should always be referencing the Enterprise Data Model, not another Solution Model.
5. All data models are kept in its own `Space`, thus data models do not share the same `Space`.
6. All `View`s of a data models have the same version and `Space` as the data model.
7. Keep the Enterprise Data Model as small as possible, but not smaller.

## Justification

### Data models should be designed for real use cases

A danger with data modeling is that it can become an academic exercise with a lot of discussions about what to
name concepts and how the concepts should be related to each other. In addition, there is a danger of creating
extremely detailed data models, which are 1–1 representation of the real and digital world, but data models
which have poor performances and are only understandable to the few who build them.
Having real use cases is what grounds the modeling effort in reality and ensures that the data model is useful
for the organization. Often is a good practice start with three relevant business questions to be answered
and drive the data modeling process from them.

### The essence of data modeling is **cooperation**

Data modeling is how people with different backgrounds and perspective come together to create a shared understanding
of the business and data. You should expect that there will be friction in this process as it surfaces and makes
concrete the understanding from different parts of the organization. It is by working through this friction that
the organization creates the shared understanding that unlocks better collaboration across the organization,
resulting in solving critical business questions.

### Parsimonious Model

The principle of parsimony is a fundamental principle in science and engineering. It states that a model should be
as simple as possible, but not simpler. In the case of an Enterprise Data Model, it should contain all shared
concepts across the organization, but no concepts that are only relevant for a single business area.

### Establish a data governance team
This follows from the principle of cooperation. This team should be cross-functional and have representatives
from all relevant parts of the organization. This is important to ensure that the Enterprise Data Model
has a solid anchoring in the organization such that it will be used and maintained.

### Create one main data model, the Enterprise Data Model

This follows from the principle of cooperation. By having one main data model, it forces the organization to
cooperate. Once the Enterprise Data Model is in place, it serves as the foundation for cooperation across the
organization. It also explicitly encodes implicit knowledge, making it clearer and easier to discuss,
onboard new employees and partners, and to make decisions. The Enterprise Data Model forms a singular language
for communication among various business units and subdomains.

### Each business area can create one or more specific data models

This follows from the principle of cooperation and parsimonious model. The development of the
Enterprise Data Model will necessarily be a slower process, and it typically will become a larger data model
that can be challenging to use for practical applications. A Solution Data Model is a business area / subdomain-specific
data model using a subset of the Enterprise Data Model, which can also be extended with additional concepts that are
not part of the Enterprise Data Model. This allows business areas to move faster and to adapt the data model
to their specific needs. It is also a typical way to test new concepts before they are integrated into
the Enterprise Data Model. Also, the solution model will have higher fidelity of details specific for a business
unit / subdomain.  Furthermore, while at the Enterprise data model level all parties need to agree on an enterprise
level naming convention, which means a lot of compromises, at the Solution data model level a business unit can use
their subdomain-specific naming convention, thus the business unit has more freedom.

### Solution Data Models should always be referencing the Enterprise Data Model

This follows from the principle of cooperation and parsimonious model. The trade-off that the Enterprise and
Solution data models are trying to balance is between *cooperation* and the *ability to move fast*. If the organization
only had a single data model, development would be extremely slow and many concepts would be irrelevant for
most parts of the organization. On the other hand, if each business area had its own data model,
it would be very challenging to cooperate across the organization, a situation which we refer to as *siloing* as data,
information and knowledge among various business units would be isolated.
If Solution Data Models are allowed to build on top of other Solution Data Models, it increases the complexity
which makes it hard to maintain and understand. In addition, it can easily lead ot siloing through obfuscation.
By using the Enterprise Data Model for shared concepts, it enforces a discussion when new concepts are introduced.
The discussion will ensure that the new concepts are well anchored in the organization and can be built upon by other
business areas.

### All data models are kept in its own `Space`

This follows from the principle of parsimonious model. By keeping each data model in its own `Space`, it is clear
which concepts are part of the data model and which are not. It also makes it easier to manage access control,
which should be with the team that owns the data model. In addition, this enables establishing a clear
portfolio of data products an organization owns.

### All `View`s of a data models have the same version and `Space` as the data model

This follows from the principle of parsimonious model. Technically, a data model can contain `View`s from
different `Space`s and with different version. However, `View`s are very cheap and having `View`s with different
versions than the data model can cause a lot of confusion. Furthermore, all `View`s should be controlled by the
team that owns the data model, thus they should be in the same `Space`. If a solution models needs to use a `View`
that is identical to a `View` in the Enterprise Data Model, you should use the `implements` option to reference the
`View` in the Enterprise Data Model. An advantage of this is that if you want to extend the `View` later
in the Solution Data Model, you can do that without affecting the `View` in the Enterprise Data Model.

### Keep the Enterprise Data Model as small as possible, but not smaller

This follows from the principle of cooperation and parsimonious model. There is a cost to each concept in the
Enterprise data model as it needs to be discussed and anchored in the organization. Thus, concepts that are not
shared should not be put through this process. A side note is that concepts that are not shared can easily
elicit strong opinions without grounding in reality, and thus be an extra long cumbersome waste of time.

In addition, the organization will constantly evolve the Enterprise Data Model, and the goal is to avoid
introducing changes that will lead to a chain of changes to all Solution Data Models. If we avoid introducing
a new concept too early, it gives us the maximum flexibility in the future when we need to introduce the new concept.
If the concept is already in the Enterprise Data Model, it will be much more costly to change it.

Note that an organization that is mature when it comes to data modeling should expect to have three versions of the
Enterprise Data Model: `legacy`, `current` and `future`. The `current` is the version that most solution models
are using, the `legacy` is the version that is being phased out, and the `future` is the version that is currently
being developed thus is still subject to change. One should aim at developing data models that are forward compatible,
enabling migration to the latest version with no issues if possible.


<!-- SOURCE_END: neat/docs\data_modeling\principles.md -->

================================================================================


<!-- SOURCE_START: neat/docs\history.md -->
## File: neat/docs\history.md

NEAT emerged from years of experience in semantic tooling and knowledge graph development. The foundation was laid in 2020 with [sheet2rdf](https://github.com/nikokaoja/sheet2rdf), an Excel-based tool that trained data stewards to build domain-specific knowledge graphs and supported CI/CD processes in the Dutch Covid program and european wind energy community.

By mid of 2022, sheet2rdf was used in several POCs in Cognite. As Cognite's Data Modeling Service (DMS) development progressed, the need for simplified data modeling experience led to demonstration of proto-NEAT, known as sheet2fdm, an extension of sheet2rdf, enabling semantic data model definitions in OWL, SHACL, Python and GraphQL (see e.g., [wind energy data model](https://cognitedata.github.io/wind-energy-data-model/)) using a simplified version of sheet2rdf Excel template.

Presented in various forums in 2022, this approach paved the way for NEAT’s formal development in November 2022 to enable cost-saving and empowerment of Cognite's customers to self-sufficiently maintain and onboard knowledge graphs to Cognite Data Fusion. From the end of 2022 until the end of 2024, NEAT development was funded initially by [Statnett](https://www.statnett.no/) (2022 - 2023), and then by [Aker Solutions](https://www.akersolutions.com/) (2023 - 2024).

In autumn 2025, following the formation of dedicated team for CDF tooling, we have refactored NEAT and released its first official version 1 that is formally a product offering of Cognite. The version 1 of NEAT is scoped down to only physical data modeling capabilities. However, all the other features of legacy version are accessible, and will be over time ported to the version 1 (see Roadmap for details).

<!-- SOURCE_END: neat/docs\history.md -->

================================================================================


<!-- SOURCE_START: neat/docs\index.md -->
## File: neat/docs\index.md

# kNowlEdge grAph Transformer (NEAT)

[![[28f7f7f30ae24ac3098c65fb7174f0c5_MD5.svg]]](https://github.com/cognitedata/neat/actions/workflows/release.yaml)
[![Documentation Status](https://readthedocs.com/projects/cognite-neat/badge/?version=latest&style=for-the-badge)](https://cognite-neat.readthedocs-hosted.com/en/latest/?badge=latest)
[![[8e823a9f3048e3be7e054e6ab06fbd9a_MD5.svg]]](https://github.com/cognitedata/neat)
[![[0aef87e8e5546ee8d4b9ae8c42f8bc13_MD5.svg]]](https://pypi.org/project/cognite-neat/)
[![[11e043957bb0304e9e170a8b25aca42c_MD5.svg]]](https://pypistats.org/packages/cognite-neat)
[![[c068200262bbd2cc1582caf3aa42f0c3_MD5.svg]]](https://github.com/cognitedata/neat/blob/master/LICENSE)
[![[7de6e0fbcef452858a5c18734daf6f97_MD5.svg]]](https://github.com/ambv/black)
[![[da36a1c99cbd3b893b539373f75a785a_MD5.svg]]](https://github.com/astral-sh/ruff)
[![[2ace58c4d915aebac003624d4f2602cd_MD5.svg]]](http://mypy-lang.org)



There was no easy way to make knowledge graphs, especially data models, and onboard them to
[Cognite Data Fusion](https://www.cognite.com/en/product/cognite_data_fusion_industrial_dataops_platform), so we have built NEAT!


NEAT is great for data model development, validation and deployment. It comes with an evergrowing library of validators,
which will assure that your data model adheres to the best practices and that is performant. Unlike other solutions,
which require you to be a technical wizard or modeling expert, NEAT provides you a guiding data modeling experience.

We offer various interfaces on how you can develop your data model, where majority of our users prefer
a combination of Jupyter Notebooks, leveraging NEAT features through so called [NeatSession](reference/NeatSession/session.md),  with [a Spreadsheet data model template](excel_data_modeling/physical/overview.md).


Only Data modeling? There was more before!?
True, NEAT v0.x (legacy) offered a complete knowledge graph
tooling. Do not worry though, all the legacy features are still available and will be gradually
ported to NEAT v1.x according to the [roadmap](roadmap.md).

<!-- SOURCE_END: neat/docs\index.md -->

================================================================================


<!-- SOURCE_START: neat/docs\installation.md -->
## File: neat/docs\installation.md

# Getting Started with NEAT

NEAT is distributed as a Python package. It is used primarily in a notebook environment such as, for example, Jupyter Notebooks. Another notebook environment is the CDF notebooks. You will be soon able to plug NEAT to [CDF Toolkit](https://docs.cognite.com/cdf/deploy/cdf_toolkit), which will then leverage neat for CDF data modeling.

## CDF Notebooks Environment

CDF Notebooks are a part of the Cognite Data Fusion (CDF) platform. These notebooks are a great way to get started with NEAT, even if you have **no coding experience**.

1. Go to [Cognite Data Fusion](https://fusion.cogniteapp.com/)
2. Login to your account
3. Ensure you have selected the `Data Management` workspace.
4. Select `Build solutions` in your left menubar.
5. Click on `Jupyter Notebooks` under the expanded `Build solutions` menu.
6. Launch a new notebook.
7. Install `cognite-neat` by running `%pip install cognite-neat` in a cell.
8. Import `NeatSession` and `CogniteClient` and start using it as shown below

```python
from cognite.client import CogniteClient
from cognite.neat import NeatSession

client = CogniteClient()

neat = NeatSession(client)

# Start using neat by typing neat.<TAB>
```

## Local Notebook Environment
Running `neat` locally requires a Python environment as well as a notebook environment. The following steps will
guide you through the installation process using [Jupyter Lab](https://jupyter.org/install) as the notebook environment.

**Prerequisites**: Installed Python 3.10 or later, see [python.org](https://www.python.org/downloads/)

1. Create and enter directory for `neat` installation
1. Create a virtual environment:
2. Activate your virtual environment
3. Install `cognite-neat`
4. Install a notebook environment, `pip install jupyterlab`
5. Install tqdm for progress bars, `pip install tqdm`
6. Start your notebook environment, `jupyter lab`
7. Import `NeatSession` and `get_cognite_client` and start using it

=== "Windows"

    ```
    mkdir neat && cd neat
    ```

    ```
    python -m venv venv
    ```

    ```
    venv\Scripts\activate.bat
    ```

    ```
    pip install cognite-neat
    ```

    ```
    pip install jupyterlab
    ```

    ```
    pip install tqdm
    ```

    ```
    jupyter lab
    ```

=== "Mac/Linux"

    ```bash
    mkdir neat && cd neat
    ```

    ```bash
    python -m venv venv
    ```

    ```bash
    source venv/bin/activate
    ```

    ```bash
    pip install "cognite-neat[pyoxigraph]"
    ```

    ```bash
    pip install pip install jupyterlab
    ```

    ```bash
    jupyter lab
    ```

In a notebook cell, you can now import `NeatSession` and `get_cognite_client` and start using it as shown below

```python
from cognite.neat import NeatSession, get_cognite_client

client = get_cognite_client(".env")

neat = NeatSession(client)

# Start using neat by typing neat.<TAB>

# Reading of a physical data model from CDF CDM

neat.physical_data_model.read.cdf("cdf_cdm", "CogniteCore", "v1")
```

!!! tip "Helper get_cognite_client function"

    The `get_cognite_client` function is a helper function that reads the environment variables from a `.env` file
    and creates a `CogniteClient` instance. This is a common pattern when working with Cognite Data Fusion through
    Python. Note that if you don't have a `.env` file, it will prompt you to enter environment variables
    interactively and offer to save them to a `.env` file. You can instantiate a `CogniteClient` instance directly
    if you prefer.


<!-- SOURCE_END: neat/docs\installation.md -->

================================================================================


<!-- SOURCE_START: neat/docs\known_issues.md -->
## File: neat/docs\known_issues.md

## Ad-blockers causing NeatSession initialization errors

When initializing a `NeatSession`, you may encounter the following error if you have ad-blockers enabled:

```python
ProtocolError: ('Connection aborted.', HTTPException('Failed to fetch'))
MaxRetryError: HTTPSConnectionPool(host='api-eu.mixpanel.com', port=443): Max retries exceeded with url: /engage (Caused by ProtocolError('Connection aborted.', HTTPException('Failed to fetch')))
```

**Cause**: This error occurs because NEAT attempts to send anonymous usage analytics to Mixpanel, but ad-blockers prevent the connection.

**Solution**: You will need to disable ad-blocker or whitelist mixpanel.com in your ad-blocker settings.

<!-- SOURCE_END: neat/docs\known_issues.md -->

================================================================================


<!-- SOURCE_START: neat/docs\reference\config.md -->
## File: neat/docs\reference\config.md

# NeatConfig

`NeatConfig` allows you to configure your NEAT session with specific [data modeling modes](../data_modeling/modes.md) and [validation rules](../validation/index.md). It provides pre-defined governance profiles that combine validation settings with data modeling behavior, or you can define custom profiles via a TOML configuration file. Make sure to get yourself familiar with the available [data modeling modes](../data_modeling/modes.md) and [validation rules](../validation/index.md) before configuring your own NEAT session and custom configurations.



::: cognite.neat.NeatConfig

```python
from cognite.neat import NeatConfig

config = NeatConfig.create_predefined(profile = "legacy-additive")
```

## Custom Configuration via TOML File

::: cognite.neat.get_neat_config_from_file

You can define custom profiles in a TOML configuration file. The configuration file can be placed in your project root (e.g., `pyproject.toml` or `neat.toml`).

### Basic TOML Structure

```toml
[tool.neat]
# Reference a profile (either built-in or custom)
profile = "my-custom-profile"

[tool.neat.modeling]
# Data modeling mode
# Options: "additive", "rebuild"
mode = "additive"

[tool.neat.validation]
# Validation rules to exclude (supports wildcards with *)
exclude = []
```

Custom Profile Example
Define your own profiles with specific validation rules:

```toml
[tool.neat]
profile = "my-smart-profile"

[tool.neat.profiles.my-smart-profile.modeling]
mode = "additive"

[tool.neat.profiles.my-smart-profile.validation]
exclude = ["NEAT-DMS-AI-READINESS-*", "NEAT-DMS-CONNECTIONS-REVERSE-008"]
```

#### Validation Exclusion Patterns

The `exclude` list supports wildcard patterns:

- "NEAT-DMS-AI-READINESS-*" - Excludes all AI-readiness validation rules
- "NEAT-DMS-CONNECTIONS-002" - Excludes a specific validation rule
- "*" - Excludes all validation rules (use with caution)

#### Loading Custom Configuration

```python
from cognite.neat import get_neat_config_from_file

# Load from pyproject.toml or neat.toml
config = get_neat_config_from_file("pyproject.toml", profile="my-smart-profile")
```

!!! warning "Built-in Profiles Cannot Be Redefined"
    The pre-defined profiles:

    - `legacy-additive`
    - `legacy-rebuild`
    - `deep-additive`
    - `deep-rebuild`

    are hardcoded and cannot be overridden in TOML files. Use custom profile names for your configurations.

<!-- SOURCE_END: neat/docs\reference\config.md -->

================================================================================


<!-- SOURCE_START: neat/docs\reference\legacy\issues\base.md -->
## File: neat/docs\reference\legacy\issues\base.md

::: cognite.neat._v0.core._issues


<!-- SOURCE_END: neat/docs\reference\legacy\issues\base.md -->

================================================================================


<!-- SOURCE_START: neat/docs\reference\legacy\issues\errors.md -->
## File: neat/docs\reference\legacy\issues\errors.md

::: cognite.neat._v0.core._issues.errors


<!-- SOURCE_END: neat/docs\reference\legacy\issues\errors.md -->

================================================================================


<!-- SOURCE_START: neat/docs\reference\legacy\issues\user_model_warnings.md -->
## File: neat/docs\reference\legacy\issues\user_model_warnings.md

::: cognite.neat._v0.core._issues.warnings.user_modeling



<!-- SOURCE_END: neat/docs\reference\legacy\issues\user_model_warnings.md -->

================================================================================


<!-- SOURCE_START: neat/docs\reference\legacy\issues\warnings.md -->
## File: neat/docs\reference\legacy\issues\warnings.md

::: cognite.neat._v0.core._issues.warnings



<!-- SOURCE_END: neat/docs\reference\legacy\issues\warnings.md -->

================================================================================


<!-- SOURCE_START: neat/docs\reference\legacy\NeatSession\base.md -->
## File: neat/docs\reference\legacy\NeatSession\base.md

::: cognite.neat._v0.session.NeatSession

<!-- SOURCE_END: neat/docs\reference\legacy\NeatSession\base.md -->

================================================================================


<!-- SOURCE_START: neat/docs\reference\legacy\NeatSession\drop.md -->
## File: neat/docs\reference\legacy\NeatSession\drop.md

::: cognite.neat._v0.session._drop.DropAPI

<!-- SOURCE_END: neat/docs\reference\legacy\NeatSession\drop.md -->

================================================================================


<!-- SOURCE_START: neat/docs\reference\legacy\NeatSession\fix.md -->
## File: neat/docs\reference\legacy\NeatSession\fix.md

::: cognite.neat._v0.session._fix.FixAPI


::: cognite.neat._v0.session._fix.DataModelFixAPI

<!-- SOURCE_END: neat/docs\reference\legacy\NeatSession\fix.md -->

================================================================================


<!-- SOURCE_START: neat/docs\reference\legacy\NeatSession\inspect.md -->
## File: neat/docs\reference\legacy\NeatSession\inspect.md

::: cognite.neat._v0.session._inspect.InspectAPI

<!-- SOURCE_END: neat/docs\reference\legacy\NeatSession\inspect.md -->

================================================================================


<!-- SOURCE_START: neat/docs\reference\legacy\NeatSession\mapping.md -->
## File: neat/docs\reference\legacy\NeatSession\mapping.md

::: cognite.neat._v0.session._mapping.DataModelMappingAPI


<!-- SOURCE_END: neat/docs\reference\legacy\NeatSession\mapping.md -->

================================================================================


<!-- SOURCE_START: neat/docs\reference\legacy\NeatSession\prepare.md -->
## File: neat/docs\reference\legacy\NeatSession\prepare.md

::: cognite.neat._v0.session._prepare.PrepareAPI
::: cognite.neat._v0.session._prepare.InstancePrepareAPI
::: cognite.neat._v0.session._prepare.DataModelPrepareAPI


<!-- SOURCE_END: neat/docs\reference\legacy\NeatSession\prepare.md -->

================================================================================


<!-- SOURCE_START: neat/docs\reference\legacy\NeatSession\read.md -->
## File: neat/docs\reference\legacy\NeatSession\read.md

::: cognite.neat._v0.session._read.CDFReadAPI
::: cognite.neat._v0.session._read.CDFClassicAPI
::: cognite.neat._v0.session._read.ExcelReadAPI
::: cognite.neat._v0.session._read.YamlReadAPI
::: cognite.neat._v0.session._read.CSVReadAPI
::: cognite.neat._v0.session._read.XMLReadAPI
::: cognite.neat._v0.session._read.RDFReadAPI


<!-- SOURCE_END: neat/docs\reference\legacy\NeatSession\read.md -->

================================================================================


<!-- SOURCE_START: neat/docs\reference\legacy\NeatSession\set.md -->
## File: neat/docs\reference\legacy\NeatSession\set.md

::: cognite.neat._v0.session._set.SetAPI

<!-- SOURCE_END: neat/docs\reference\legacy\NeatSession\set.md -->

================================================================================


<!-- SOURCE_START: neat/docs\reference\legacy\NeatSession\show.md -->
## File: neat/docs\reference\legacy\NeatSession\show.md

::: cognite.neat._v0.session._show.ShowAPI


<!-- SOURCE_END: neat/docs\reference\legacy\NeatSession\show.md -->

================================================================================


<!-- SOURCE_START: neat/docs\reference\legacy\NeatSession\subset.md -->
## File: neat/docs\reference\legacy\NeatSession\subset.md

::: cognite.neat._v0.session._subset.SubsetAPI


<!-- SOURCE_END: neat/docs\reference\legacy\NeatSession\subset.md -->

================================================================================


<!-- SOURCE_START: neat/docs\reference\legacy\NeatSession\template.md -->
## File: neat/docs\reference\legacy\NeatSession\template.md

::: cognite.neat._v0.session._template.TemplateAPI

<!-- SOURCE_END: neat/docs\reference\legacy\NeatSession\template.md -->

================================================================================


<!-- SOURCE_START: neat/docs\reference\legacy\NeatSession\to.md -->
## File: neat/docs\reference\legacy\NeatSession\to.md

::: cognite.neat._v0.session._to.ToAPI
::: cognite.neat._v0.session._to.CDFToAPI



<!-- SOURCE_END: neat/docs\reference\legacy\NeatSession\to.md -->

================================================================================


<!-- SOURCE_START: neat/docs\reference\session.md -->
## File: neat/docs\reference\session.md

# NeatSession

```python
neat = NeatSession(client: CogniteClient, config: Optional[NeatConfig] = None)
```

::: cognite.neat.NeatSession.__init__
    options:
      heading_level: 3
      show_root_toc_entry: false
      show_root_heading: false
      show_source: false
      show_if_no_docstring: true

### issues
```python
neat.issues
```
Presents issues and insights found during the session operations.

### result
```python
neat.result
```
Holds the result of the last executed operation in the session.


## Physical Data Model
```python
neat.physical_data_model
```


### Reading Physical Data Models


::: cognite.neat._session._physical.ReadPhysicalDataModel
    options:
      show_root_toc_entry: false
      show_root_heading: false
      heading_level: 4
      show_source: false
      show_signature_annotations: true
      signature_crossrefs: true
      separate_signature: true
      filters:
        - "!^_"


### Writing Physical Data Models

::: cognite.neat._session._physical.WritePhysicalDataModel
    options:
      show_root_toc_entry: false
      show_root_heading: false
      heading_level: 4
      show_source: false
      show_signature_annotations: true
      signature_crossrefs: true
      separate_signature: true
      filters:
        - "!^_"

<!-- SOURCE_END: neat/docs\reference\session.md -->

================================================================================


<!-- SOURCE_START: neat/docs\roadmap.md -->
## File: neat/docs\roadmap.md

The legacy `NEAT` (v0.x) has a lot of capabilities that are not yet implemented in `NEAT` (v1.x). The focus of v1.0 was solely on the physical data modeling read, validation and write.

The roadmap below outlines the planned features packages for future releases of `NEAT` (v1.x) which will robustify and productify all the legacy capabilities while introducing new features and improvements.

Users are encouraged to contribute to the roadmap by suggesting and prioritizing features or improvements that would enhance their experience with `NEAT`. Feedback and suggestions can be submitted through:

- Slack: [#neat-help](https://app.slack.com/client/T3XCNGHJL/C049R6DG3HR)
- Jira: [NEAT Project Board](https://cognitedata.atlassian.net/jira/software/projects/THIS/boards/3021)


## Planned Feature Packages for NEAT (v1.x)

### Data Model Fix
Automatically fix validation issues in the data model based on predefined rules and best practices.


JIRA Epic: [THIS-935](https://cognitedata.atlassian.net/browse/THIS-935)

Legacy availability: Limited, see [documentation](reference/legacy/NeatSession/fix.md)


### Conceptual Data Modeling
NEAT will support the creation and management of Conceptual Data Models (CDM) that serve as high-level, business-oriented representations of data structures, which can be seamlessly converted and linked to Physical Data Models for implementation in CDF. The tooling will intelligently resolve different types of relationships—whether they should be implemented as direct relations, edges, or reverse properties—based on the modeling context and best practices. Additionally, NEAT will enable users to extend not only the Core Data Model (CDM) but any existing data model in CDF, providing flexibility to build upon and customize standard models while maintaining consistency and traceability between conceptual and physical representations.

JIRA Epic: [THIS-800](https://cognitedata.atlassian.net/browse/THIS-800)

Legacy availability: Yes, see [this tutorial](tutorials/legacy/data-modeling/from-conceptual-to-physical-via-CDM.ipynb)


### Data Model Inference
Ability to infer data model directly from data (instances) without the need to define the model explicitly.

JIRA Epic: [THIS-936](https://cognitedata.atlassian.net/browse/THIS-936)

Legacy availability: Yes, [see reference documentation](reference/legacy/NeatSession/base.md#cognite.neat.v0.session.NeatSession.infer)


### Data Model Population
Population of the CDF data model without a need for CDF Transformations and CDF RAW.

JIRA Epic: [THIS-818](https://cognitedata.atlassian.net/browse/THIS-818)

Legacy availability: Yes, [see reference documentation](reference/legacy/NeatSession/to.md#cognite.neat.v0.session._to.CDFToAPI.instances)

### Plugins
Support for plugins to extend NEAT's functionality especially for custom data model readers and writers. This feature package entails refactoring of NeatEngine, an RML engine, which provides creation of instances without the need to create custom extractors.

JIRA Epic: [THIS-805](https://cognitedata.atlassian.net/browse/THIS-805)

Legacy availability: Yes, [see CFIHOS handler plugin](https://github.com/thisisneat-io/cfihos-handler)

### Governance and Version Control
Support for user defined data model versioning and governance profiles to ensure compliance with organizational or global defined best practices.

JIRA Epic: [THIS-825](https://cognitedata.atlassian.net/browse/THIS-825)

Legacy availability: No

### Semantic Data Modeling
Lossless reading and writing of semantic data modeling in/from NeatSession including support for OWL ontologies and SHACL shapes.

JIRA Epic: [THIS-808](https://cognitedata.atlassian.net/browse/THIS-808)

Legacy availability: Partially supported, see [reference documentation](reference/legacy/NeatSession/to.md#cognite.neat.v0.session._read.RDFReadAPI.ontology)

### Instance Validation
Validation of data model instances against SHACL shapes to ensure data integrity and compliance with defined schemas.

JIRA Epic: [THIS-826](https://cognitedata.atlassian.net/browse/THIS-826)
Legacy availability: No





<!-- SOURCE_END: neat/docs\roadmap.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\index.md -->
## File: neat/docs\validation\index.md

**Neat supports 31 validation rules** for data modeling. These rules are learned
 from best practice, knowledge of the Cognite Data Fusion data modeling service, and practical experience from
 helping customers build and maintain their data models.



### Ai_Readiness (NEAT-DMS-AI-READINESS)

Validators for checking if data model is AI-ready.

| code | name | message |
|------|------|---------|
| NEAT-DMS-AI-READINESS-001 | [DataModelMissingName](neat-dms-ai-readiness-001.md) | Validates that data model has a human-readable name. |
| NEAT-DMS-AI-READINESS-002 | [DataModelMissingDescription](neat-dms-ai-readiness-002.md) | Validates that data model has a human-readable description. |
| NEAT-DMS-AI-READINESS-003 | [ViewMissingName](neat-dms-ai-readiness-003.md) | Validates that a View has a human-readable name. |
| NEAT-DMS-AI-READINESS-004 | [ViewMissingDescription](neat-dms-ai-readiness-004.md) | Validates that a View has a human-readable description. |
| NEAT-DMS-AI-READINESS-005 | [ViewPropertyMissingName](neat-dms-ai-readiness-005.md) | Validates that a view property has a human-readable name. |
| NEAT-DMS-AI-READINESS-006 | [ViewPropertyMissingDescription](neat-dms-ai-readiness-006.md) | Validates that a View property has a human-readable description. |
| NEAT-DMS-AI-READINESS-007 | [EnumerationMissingName](neat-dms-ai-readiness-007.md) | Validates that an enumeration has a human-readable name. |
| NEAT-DMS-AI-READINESS-008 | [EnumerationMissingDescription](neat-dms-ai-readiness-008.md) | Validates that an enumeration value has a human-readable description. |

### Connections (NEAT-DMS-CONNECTIONS)

Validators for connections in data model specifications.

| code | name | message |
|------|------|---------|
| NEAT-DMS-CONNECTIONS-001 | [ConnectionValueTypeUnexisting](neat-dms-connections-001.md) | Validates that connection value types exist. |
| NEAT-DMS-CONNECTIONS-002 | [ConnectionValueTypeUndefined](neat-dms-connections-002.md) | Validates that connection value types are not None, i.e. undefined. |
| NEAT-DMS-CONNECTIONS-REVERSE-001 | [ReverseConnectionSourceViewMissing](neat-dms-connections-reverse-001.md) | Validates that source view referenced in reverse connection exist. |
| NEAT-DMS-CONNECTIONS-REVERSE-002 | [ReverseConnectionSourcePropertyMissing](neat-dms-connections-reverse-002.md) | Validates that source property referenced in reverse connections exist. |
| NEAT-DMS-CONNECTIONS-REVERSE-003 | [ReverseConnectionSourcePropertyWrongType](neat-dms-connections-reverse-003.md) | Validates that source property for the reverse connections is a direct relation. |
| NEAT-DMS-CONNECTIONS-REVERSE-004 | [ReverseConnectionContainerMissing](neat-dms-connections-reverse-004.md) | Validates that the container referenced by the reverse connection source properties exist. |
| NEAT-DMS-CONNECTIONS-REVERSE-005 | [ReverseConnectionContainerPropertyMissing](neat-dms-connections-reverse-005.md) | Validates that container property referenced by the reverse connections exists. |
| NEAT-DMS-CONNECTIONS-REVERSE-006 | [ReverseConnectionContainerPropertyWrongType](neat-dms-connections-reverse-006.md) | Validates that the container property used in reverse connection is the direct relations. |
| NEAT-DMS-CONNECTIONS-REVERSE-007 | [ReverseConnectionTargetMissing](neat-dms-connections-reverse-007.md) | Validates that the direct connection in reverse connection pair have target views specified. |
| NEAT-DMS-CONNECTIONS-REVERSE-008 | [ReverseConnectionPointsToAncestor](neat-dms-connections-reverse-008.md) | Validates that direct connections point to specific views rather than ancestors. |
| NEAT-DMS-CONNECTIONS-REVERSE-009 | [ReverseConnectionTargetMismatch](neat-dms-connections-reverse-009.md) | Validates that direct connections point to the correct target views. |

### Consistency (NEAT-DMS-CONSISTENCY)

Validators checking for consistency issues in data model.

| code | name | message |
|------|------|---------|
| NEAT-DMS-CONSISTENCY-001 | [ViewSpaceVersionInconsistentWithDataModel](neat-dms-consistency-001.md) | Validates that views have consistent space and version with the data model. |

### Containers (NEAT-DMS-CONTAINER)

Validators for checking containers in the data model.

| code | name | message |
|------|------|---------|
| NEAT-DMS-CONTAINER-001 | [ExternalContainerDoesNotExist](neat-dms-container-001.md) | Validates that any container referenced by a view property, when the |
| NEAT-DMS-CONTAINER-002 | [ExternalContainerPropertyDoesNotExist](neat-dms-container-002.md) | Validates that any container property referenced by a view property, when the |
| NEAT-DMS-CONTAINER-003 | [RequiredContainerDoesNotExist](neat-dms-container-003.md) | Validates that any container required by another container exists in the data model. |

### Limits (NEAT-DMS-LIMITS)

Validators for checking if defined data model is within CDF DMS schema limits.

| code | name | message |
|------|------|---------|
| NEAT-DMS-LIMITS-CONTAINER-001 | [ContainerPropertyCountIsOutOfLimits](neat-dms-limits-container-001.md) | Validates that a container does not exceed the maximum number of properties. |
| NEAT-DMS-LIMITS-CONTAINER-002 | [ContainerPropertyListSizeIsOutOfLimits](neat-dms-limits-container-002.md) | Validates that container property list sizes do not exceed CDF limits. |
| NEAT-DMS-LIMITS-DATA-MODEL-001 | [DataModelViewCountIsOutOfLimits](neat-dms-limits-data-model-001.md) | Validates that the data model does not exceed the maximum number of views. |
| NEAT-DMS-LIMITS-VIEW-001 | [ViewPropertyCountIsOutOfLimits](neat-dms-limits-view-001.md) | Validates that a view does not exceed the maximum number of properties. |
| NEAT-DMS-LIMITS-VIEW-002 | [ViewContainerCountIsOutOfLimits](neat-dms-limits-view-002.md) | Validates that a view does not reference too many containers. |
| NEAT-DMS-LIMITS-VIEW-003 | [ViewImplementsCountIsOutOfLimits](neat-dms-limits-view-003.md) | Validates that a view does not implement too many other views. |

### Views (NEAT-DMS-VIEW)

Validators for checking containers in the data model.

| code | name | message |
|------|------|---------|
| NEAT-DMS-VIEW-001 | [ViewToContainerMappingNotPossible](neat-dms-view-001.md) | Validates that container and container property referenced by view property exist. |
| NEAT-DMS-VIEW-002 | [ImplementedViewNotExisting](neat-dms-view-002.md) | Validates that implemented (inherited) view exists. |


<!-- SOURCE_END: neat/docs\validation\index.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-ai-readiness-001.md -->
## File: neat/docs\validation\neat-dms-ai-readiness-001.md

Validates that data model has a human-readable name.

## What it does
Validates that the data model has a human-readable name.

## Why is this bad?
Often the data model ids are technical identifiers, abbreviations, etc.
A missing name makes it harder for users (humans or machines) to understand what the data model represents.
Providing a clear name improves usability, maintainability, searchability, and AI-readiness.

## Example
A data model has an id IEC61400-25-2 but no name. Users may find it difficult to understand what this data model
represents. However adding a name "Wind Energy Information Model" would increase clarity and usability.

<!-- SOURCE_END: neat/docs\validation\neat-dms-ai-readiness-001.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-ai-readiness-002.md -->
## File: neat/docs\validation\neat-dms-ai-readiness-002.md

Validates that data model has a human-readable description.

## What it does
Validates that the data model has a human-readable description.

## Why is this bad?
A missing description makes it harder for users (humans or machines) to understand the purpose and scope
of the data model. The description provides important context about what domain the data model covers,
what use cases it supports, and how it should be used.

## Example
A data model has an id CIM, with name Common Information Model, but no description. Users may find it difficult to
understand what this data model represents, unless extra context is provided. In this particualar case, name
does not provide sufficient information, as it is too generic, that this data model is focused on the
electrical power systems domain. However, providing a description such as:
"The Common Information Model (CIM) is a standard developed by IEC for representing power system
components and their relationships. It is widely used in the electrical utility industry for data
exchange and system modeling." would greatly improve clarity and usability.

<!-- SOURCE_END: neat/docs\validation\neat-dms-ai-readiness-002.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-ai-readiness-003.md -->
## File: neat/docs\validation\neat-dms-ai-readiness-003.md

Validates that a View has a human-readable name.

## What it does
Validates that each view in the data model has a human-readable name.

## Why is this bad?
A missing name makes it harder for users (humans or machines) to understand the purpose of the view.
This is important as views' external ids are often based on technical identifiers, abbreviations, etc.
Providing a clear name improves usability, maintainability, searchability, and AI-readiness.

## Example
A view has an id CFIHOS-30000038 but no name. Users may find it difficult to understand what this view represents,
unless they look up the id in documentation or other resources. Adding name "Pump" would increase clarity and
usability.

<!-- SOURCE_END: neat/docs\validation\neat-dms-ai-readiness-003.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-ai-readiness-004.md -->
## File: neat/docs\validation\neat-dms-ai-readiness-004.md

Validates that a View has a human-readable description.

## What it does
Validates that each view in the data model has a human-readable description.

## Why is this bad?
A missing description makes it harder for users (humans or machines) to understand in what context the view
should be used. The description can provide important information about the view's purpose, scope, and usage.


## Example
A view Site has no description. Users may find it difficult to understand what this view represents, unless
extra context is provided. Even if we know that Site is used in the context of wind energy developments, a
description is necessary as it can be used in various context within the same domain such as:

Option 1 — Project area
This view represents a geographical area where wind energy projects are developed and managed.

Option 2 — Lease area
The legally defined lease area allocated for offshore wind development.

Option 3 — Measurement site
A specific location where wind measurements (e.g., LiDAR, met mast) are collected.

<!-- SOURCE_END: neat/docs\validation\neat-dms-ai-readiness-004.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-ai-readiness-005.md -->
## File: neat/docs\validation\neat-dms-ai-readiness-005.md

Validates that a view property has a human-readable name.

## What it does
Validates that each view property in the data model has a human-readable name.

## Why is this bad?
A missing name makes it harder for users (humans or machines) to understand the purpose of the view property.
This is important as view property's ids are often based on technical identifiers, abbreviations, etc.
Providing a clear name improves usability, maintainability, searchability, and AI-readiness.

## Example
A view WindTurbine has a property pc which has no name. Users may find it difficult to understand what this view
property represents, unless they look up the id in documentation or other resources. Adding name "power curve"
would increase clarity and usability.

<!-- SOURCE_END: neat/docs\validation\neat-dms-ai-readiness-005.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-ai-readiness-006.md -->
## File: neat/docs\validation\neat-dms-ai-readiness-006.md

Validates that a View property has a human-readable description.

## What it does
Validates that each view property in the data model has a human-readable description.

## Why is this bad?
A missing description makes it harder for users (humans or machines) to understand in what context the view property
should be used. The description can provide important information about the view property's purpose,
scope, and usage.


## Example
A view WindTurbine has a property status with no description. Users may find it difficult to understand what this
property represents, unless extra context is provided. Even if we know that status is related to wind turbine
operations, a description is necessary as it can have different meanings in various contexts:

Option 1 — Operational status
Current operational state of the wind turbine (e.g., running, stopped, maintenance, fault).

Option 2 — Connection status
Grid connection status indicating whether the turbine is connected to the electrical grid.

Option 3 — Availability status
Availability state for production indicating whether the turbine is available for power generation.

<!-- SOURCE_END: neat/docs\validation\neat-dms-ai-readiness-006.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-ai-readiness-007.md -->
## File: neat/docs\validation\neat-dms-ai-readiness-007.md

Validates that an enumeration has a human-readable name.

## What it does
Validates that each enumeration value in the data model has a human-readable name.

## Why is this bad?
A missing name makes it harder for users (humans or machines) to understand the purpose of the enumeration value.
This is important as enumeration values are often technical codes or abbreviations, and a clear name improves
usability, maintainability, searchability, and AI-readiness.

## Example
An enumeration value with id "NOM" in a wind turbine operational mode property has no name. Users may find it
difficult to understand what this value represents. Adding name "Normal Operation" would increase clarity
and usability.

<!-- SOURCE_END: neat/docs\validation\neat-dms-ai-readiness-007.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-ai-readiness-008.md -->
## File: neat/docs\validation\neat-dms-ai-readiness-008.md

Validates that an enumeration value has a human-readable description.

## What it does
Validates that each enumeration value in the data model has a human-readable description.

## Why is this bad?
A missing description makes it harder for users (humans or machines) to understand the meaning and context
of the enumeration value. The description can provide important information about when and how the value
should be used, especially when enumeration values are technical codes or abbreviations.

## Example
An enumeration value "NOM" in a wind turbine operational mode property has no description. Users may find it
difficult to understand what this value represents without additional context. Even with a name like
"Normal Operation", the description is valuable as it can clarify specifics:

Option 1 — Basic definition
The turbine is operating normally and generating power according to its power curve.

Option 2 — Detailed operational context
The turbine is in normal operation mode, actively generating power with all systems functioning within
specified parameters and connected to the grid.

Option 3 — Contrasting with other modes
Standard operating mode where the turbine follows the power curve and responds to grid commands,
as opposed to maintenance mode or fault conditions.

<!-- SOURCE_END: neat/docs\validation\neat-dms-ai-readiness-008.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-connections-001.md -->
## File: neat/docs\validation\neat-dms-connections-001.md

Validates that connection value types exist.

## What it does
Validates that all connection value types defined in the data model exist.

## Why is this bad?
If a connection value type does not exist, the data model cannot be deployed to CDF.
This means that the connection will not be able to function.

## Example
If view WindTurbine has a connection property windFarm with value type WindFarm, but WindFarm view is not defined,
the data model cannot be deployed to CDF.

<!-- SOURCE_END: neat/docs\validation\neat-dms-connections-001.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-connections-002.md -->
## File: neat/docs\validation\neat-dms-connections-002.md

Validates that connection value types are not None, i.e. undefined.

## What it does
Validates that connections have explicitly defined value types (i.e., end connection node type).

## Why is this bad?
If a connection value type is None (undefined), there is no type information about the end node of the connection.
This yields an ambiguous data model definition, which may lead to issues during consumption of data from CDF.

## Example
Consider a scenario where we have views WindTurbine,ArrayCable and Substation. Lets say WindTurbine has a connection
`connectsTo` with value type None (undefined), then it is unclear what type of view the connection points to as
both ArrayCable and Substation are valid targets for the connection.

<!-- SOURCE_END: neat/docs\validation\neat-dms-connections-002.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-connections-reverse-001.md -->
## File: neat/docs\validation\neat-dms-connections-reverse-001.md

Validates that source view referenced in reverse connection exist.

## What it does
Checks that the source view used to configure a reverse connection exists.

## Why is this bad?
A reverse connection requires a corresponding direct connection in the source view.
If the source view doesn't exist, the reverse connection is invalid.

## Example
If WindFarm has a reverse property `turbines` through `WindTurbine.windFarm`,
but WindTurbine view doesn't exist, the reverse connection cannot function.

<!-- SOURCE_END: neat/docs\validation\neat-dms-connections-reverse-001.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-connections-reverse-002.md -->
## File: neat/docs\validation\neat-dms-connections-reverse-002.md

Validates that source property referenced in reverse connections exist.

## What it does
Checks that the direct connection property in the source view (used in the reverse connection's 'through')
actually exists in the source view.

## Why is this bad?
A reverse connection requires a corresponding direct connection property in the source view.
If this property doesn't exist, the bidirectional connection is incomplete.

## Example
If WindFarm has a reverse property `turbines` through `WindTurbine.windFarm`,
but WindTurbine view doesn't have a `windFarm` property, the reverse connection is invalid.

<!-- SOURCE_END: neat/docs\validation\neat-dms-connections-reverse-002.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-connections-reverse-003.md -->
## File: neat/docs\validation\neat-dms-connections-reverse-003.md

Validates that source property for the reverse connections is a direct relation.

## What it does
Checks that the property referenced in a reverse connection's 'through' clause
is actually a direct connection property (not a primitive or other type).

## Why is this bad?
Reverse connections can only work with direct connection properties.
Using other property types breaks the bidirectional relationship.

## Example
If WindFarm has a reverse property `turbines` through `WindTurbine.name`,
but `name` is a Text property (not a direct connection), the reverse connection is invalid.

<!-- SOURCE_END: neat/docs\validation\neat-dms-connections-reverse-003.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-connections-reverse-004.md -->
## File: neat/docs\validation\neat-dms-connections-reverse-004.md

Validates that the container referenced by the reverse connection source properties exist.

## What it does
Checks that the container holding the direct connection property (used in reverse connection) exists.

## Why is this bad?
The direct connection property must be stored in a container.
If the container doesn't exist, the connection cannot be persisted.

## Example
If WindTurbine.windFarm maps to container `WindTurbine`, but this container doesn't exist,
the reverse connection from WindFarm cannot function.

<!-- SOURCE_END: neat/docs\validation\neat-dms-connections-reverse-004.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-connections-reverse-005.md -->
## File: neat/docs\validation\neat-dms-connections-reverse-005.md

Validates that container property referenced by the reverse connections exists.

## What it does
Checks that the property in the container (mapped from the view's direct connection property)
actually exists in the container.

## Why is this bad?
The view property must map to an actual container property for data persistence.
If the container property doesn't exist, data cannot be stored.

## Example
If WindTurbine.windFarm maps to container property `WindTurbine.windFarm`,
but this container property doesn't exist, the connection cannot be stored.

<!-- SOURCE_END: neat/docs\validation\neat-dms-connections-reverse-005.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-connections-reverse-006.md -->
## File: neat/docs\validation\neat-dms-connections-reverse-006.md

Validates that the container property used in reverse connection is the direct relations.

## What it does
Checks that the container property (mapped from view's direct connection property)
has type DirectNodeRelation.

## Why is this bad?
Container properties backing connection view properties must be DirectNodeRelation type.
Other types cannot represent connections in the underlying storage.

## Example
If WindTurbine.windFarm maps to container property with type Text instead of DirectNodeRelation,
the connection cannot be stored correctly.

<!-- SOURCE_END: neat/docs\validation\neat-dms-connections-reverse-006.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-connections-reverse-007.md -->
## File: neat/docs\validation\neat-dms-connections-reverse-007.md

Validates that the direct connection in reverse connection pair have target views specified.

## What it does
Checks whether the direct connection property (referenced by reverse connection) has a value type.

## Why is this bad?
While CDF allows value type None as a SEARCH hack for multi-value relations,
it's better to explicitly specify the target view for clarity and maintainability.

## Example
If WindTurbine.windFarm has value type None instead of WindFarm,
this validator recommends specifying WindFarm explicitly.

<!-- SOURCE_END: neat/docs\validation\neat-dms-connections-reverse-007.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-connections-reverse-008.md -->
## File: neat/docs\validation\neat-dms-connections-reverse-008.md

Validates that direct connections point to specific views rather than ancestors.

## What it does
Checks whether the direct connection property points to an ancestor of the expected target view
and recommends pointing to the specific target instead.

## Why is this bad?
While technically valid, pointing to ancestors can be confusing and may lead to mistakes.
It's clearer to point to the specific target view.

## Example
If WindFarm.turbines expects WindTurbine.windFarm to point to WindFarm,
but it points to Asset (ancestor of WindFarm), this validator recommends the change.

<!-- SOURCE_END: neat/docs\validation\neat-dms-connections-reverse-008.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-connections-reverse-009.md -->
## File: neat/docs\validation\neat-dms-connections-reverse-009.md

Validates that direct connections point to the correct target views.

## What it does
Checks that the direct connection property points to the expected target view
(the view containing the reverse connection).

## Why is this bad?
The reverse connection expects a bidirectional relationship.
If the direct connection points to a different view, the relationship is broken.

## Example
If WindFarm.turbines is a reverse through WindTurbine.windFarm,
but WindTurbine.windFarm points to SolarFarm instead of WindFarm, the connection is invalid.

<!-- SOURCE_END: neat/docs\validation\neat-dms-connections-reverse-009.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-consistency-001.md -->
## File: neat/docs\validation\neat-dms-consistency-001.md

Validates that views have consistent space and version with the data model.

## What it does
Validates that all views in the data model have the same space and version as the data model.

## Why is this bad?
If views have different space or version than the data model, it may lead to more demanding development and
maintenance efforts. The industry best practice is to keep views in the same space and version as the data model.

## Example
If the data model is defined in space "my_space" version "v1", but a view is defined in the same spave but with
version "v2", this requires additional attention during deployment and maintenance of the data model.

<!-- SOURCE_END: neat/docs\validation\neat-dms-consistency-001.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-container-001.md -->
## File: neat/docs\validation\neat-dms-container-001.md

Validates that any container referenced by a view property, when the
referenced container does not belong to the data model's space, exists in CDF.

## What it does
For each view property that maps to a container in a different space than the data model,
this validator checks that the referenced external container exists in CDF.

## Why is this bad?
If a view property references a container that does not exist in CDF,
the data model cannot be deployed. The affected view property will not function, and the
deployment of the entire data model will fail.

## Example
View `my_space:WindTurbine` has a property `location` that maps to container
`other_space:WindTurbineContainer`, where `other_space` differs from `my_space`. If that
container does not exist in CDF, the model cannot be deployed.

<!-- SOURCE_END: neat/docs\validation\neat-dms-container-001.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-container-002.md -->
## File: neat/docs\validation\neat-dms-container-002.md

Validates that any container property referenced by a view property, when the
referenced container does not belong to the data model's space, exists in CDF.

## What it does
For each view property that maps to a container in a different space than the data model,
this validator checks that the referenced container property exists in that external container in CDF.
This validator only runs if the external container exists in CDF.

## Why is this bad?
If a view property references a container property that does not exist in CDF,
the data model cannot be deployed. The affected view property will not function, and the
deployment of the entire data model will fail.

## Example
View `my_space:WindTurbine` has a property `location` that maps to container property
`gpsCoordinates` in `other_space:WindTurbineContainer`. If `gpsCoordinates` does not exist
in that container in CDF, deployment will fail.

<!-- SOURCE_END: neat/docs\validation\neat-dms-container-002.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-container-003.md -->
## File: neat/docs\validation\neat-dms-container-003.md

Validates that any container required by another container exists in the data model.

## What it does
For each container in the data model, this validator checks that any container it
requires (via requires constraints) exists either in the data model or in CDF.

## Why is this bad?
If a container requires another container that does not exist in the data model or in CDF,
the data model cannot be deployed. The affected container will not function, and
the deployment of the entire data model will fail.

## Example
Container `windy_space:WindTurbineContainer` has a constraint requiring `windy_space:LocationContainer`.
If `windy_space:LocationContainer` does not exist in the data model or in CDF, deployment will fail.

<!-- SOURCE_END: neat/docs\validation\neat-dms-container-003.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-limits-container-001.md -->
## File: neat/docs\validation\neat-dms-limits-container-001.md

Validates that a container does not exceed the maximum number of properties.

## What it does
Checks that the container has no more properties than the CDF limit allows.

## Why is this bad?
CDF enforces limits on the number of properties per container to ensure optimal performance
and prevent PostGres tables that have too many columns.

## Example
If a container has 150 properties and the CDF limit is 100 properties per container,
this validator will raise a ConsistencyError issue.

<!-- SOURCE_END: neat/docs\validation\neat-dms-limits-container-001.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-limits-container-002.md -->
## File: neat/docs\validation\neat-dms-limits-container-002.md

Validates that container property list sizes do not exceed CDF limits.

## What it does
Checks that list-type properties (max_list_size) do not exceed the appropriate limit based on:
- Data type (Int32, Int64, DirectRelation, etc.)
- Presence of btree index
- Default vs maximum limits

## Why is this bad?
CDF enforces different list size limits for different data types and indexing configurations
to ensure optimal performance and prevent resource exhaustion.

## Example
If a DirectRelation property has max_list_size=2000 with a btree index, but the limit
is 1000 for indexed DirectRelations, this validator will raise a ConsistencyError issue.

## Note
Enum properties are skipped as they have a separate 32-value limit checked during read time of data model to neat
as a SyntaxError check.

<!-- SOURCE_END: neat/docs\validation\neat-dms-limits-container-002.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-limits-data-model-001.md -->
## File: neat/docs\validation\neat-dms-limits-data-model-001.md

Validates that the data model does not exceed the maximum number of views.

## What it does
This validator checks that the total number of views referenced by the data model
does not exceed the limit defined in the CDF project.

## Why is this bad?
CDF enforces limits on the number of views per data model to ensure optimal performance
and resource utilization.

## Example
If the CDF project has a limit of 100 views per data model, and the data model
references 120 views, this validator will raise a ConsistencyError issue.

<!-- SOURCE_END: neat/docs\validation\neat-dms-limits-data-model-001.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-limits-view-001.md -->
## File: neat/docs\validation\neat-dms-limits-view-001.md

Validates that a view does not exceed the maximum number of properties.

## What it does
Checks that the view has no more properties than the CDF limit allows.

## Why is this bad?
CDF enforces limits on the number of properties per view to ensure optimal performance.

## Example
If a view has 150 properties and the CDF limit is 100 properties per view,
this validator will raise a ConsistencyError issue.

<!-- SOURCE_END: neat/docs\validation\neat-dms-limits-view-001.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-limits-view-002.md -->
## File: neat/docs\validation\neat-dms-limits-view-002.md

Validates that a view does not reference too many containers.

## What it does
Checks that the view references no more containers than the CDF limit allows.

## Why is this bad?
CDF enforces limits on the number of containers per view to prevent overly complex view definitions, leading
to too many joins and performance degradation.

## Example
If a view references 20 containers and the CDF limit is 10 containers per view,
this validator will raise a ConsistencyError issue.

<!-- SOURCE_END: neat/docs\validation\neat-dms-limits-view-002.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-limits-view-003.md -->
## File: neat/docs\validation\neat-dms-limits-view-003.md

Validates that a view does not implement too many other views.

## What it does
Checks that the view implements no more views than the CDF limit allows.

## Why is this bad?
CDF enforces limits on the number of implemented views to prevent overly deep inheritance hierarchies.

## Example
If a view implements 15 other views and the CDF limit is 10 implemented views per view,
this validator will raise a ConsistencyError issue.

<!-- SOURCE_END: neat/docs\validation\neat-dms-limits-view-003.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-view-001.md -->
## File: neat/docs\validation\neat-dms-view-001.md

Validates that container and container property referenced by view property exist.

## What it does
Validates that for each view property that maps to a container and container property,
the referenced container and container property exist.

## Why is this bad?
If a view property references a container or container property that does not exist,
the data model cannot be deployed to CDF. This means that view property will not be able to function.

## Example
View WindTurbine has property location that maps to container WindTurbineContainer and property gpsCoordinates.
If WindTurbineContainer and/or property gpsCoordinates does not exist, the data model cannot be deployed to CDF.

<!-- SOURCE_END: neat/docs\validation\neat-dms-view-001.md -->

================================================================================


<!-- SOURCE_START: neat/docs\validation\neat-dms-view-002.md -->
## File: neat/docs\validation\neat-dms-view-002.md

Validates that implemented (inherited) view exists.

## What it does
Validates that all views which are implemented (inherited) in the data model actually exist either locally
or in CDF.

## Why is this bad?
If a view being implemented (inherited) does not exist, the data model cannot be deployed to CDF.

## Example
If view WindTurbine implements (inherits) view Asset, but Asset view does not exist in the data model
or in CDF, the data model cannot be deployed to CDF.

<!-- SOURCE_END: neat/docs\validation\neat-dms-view-002.md -->

================================================================================
