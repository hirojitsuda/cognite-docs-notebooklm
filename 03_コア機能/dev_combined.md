# DEV Documentation
Generated from: docs.cognite.com (dev)



<!-- SOURCE_START: docs.cognite.com/docs\dev\API_versioning.mdx -->
## File: docs.cognite.com/docs\dev\API_versioning.mdx

---
title: 'API versions'
description: 'Learn about Cognite API versioning, the policy for backwards compatibility, and the end-of-life schedule for the APIs.'
content-type: 'reference'
audience: 'developer'
experience-level: 100
lifecycle: 'use'
article-type: article
---
## Stable API versions

<Note>
We recommend stable API versions for production environments.
</Note>

All of Cognite's stable REST APIs are prefixed with `v1`. This version prefix is a long-term solution and there are no plans to increase it in the near future.

Within the `v1` version, we use a date-based versioning scheme.
By passing a date in the format `YYYYMMDD`, you will get API versions released on the given date or earlier.
Pass the desired calendar version to the API using the `Cdf-Version` header.

For example: The below version header will retrieve the API versions released on the 5th of February, 2023 and earlier.

```
Cdf-Version: 20230205
```

Any requests without a calendar version header will default to the date version `20230101`. This ensures that already deployed clients, applications, and extractors that don't request a specific version will continue to function as we roll out new breaking changes. Non-breaking changes will be available to all clients of the API, while breaking changes require each client to be upgraded to request newer versions.

We can publish new changes to existing calendar versions, but these will always be [backwards-compatible](#backwards-compatible-non-breaking-changes).
If we have to change the API in a [backwards-incompatible](#backwards-incompatible-breaking-changes) way, we will release those changes as a new calendar version.

<Note>
Stable API versions are supported by JavaScript SDK 2.x.x and Python SDK 1.x.x or newer.
</Note>

After publishing a new calendar version, we can mark the older calendar versions as _deprecated_.

<Info>
Older calendar versions will be **available for 12 months after being marked deprecated** to provide a migration period for the users.
Extension to this support period can be granted in special cases. Contact [support@cognite.com](mailto:support@cognite.com).
</Info>

## Beta versions

<Warning>
We do not recommend beta versions for production environments.
</Warning>

The beta versions provide an early peek into the latest features that will soon be available in the stable versions.
Every time a new feature is released, you can develop solutions early and be ready for deployment.
We typically promote beta features to the stable version within three months.

To request a beta version from the REST API, use the `Cdf-Version` header with a date and the suffix `-beta`.

For example: The below version header will request the beta API versions released on the 5th of February, 2023 or earlier.

```
Cdf-Version: 20230205-beta
```

We can publish new changes to the existing beta calendar versions, but these will always be [backwards-compatible](#backwards-compatible-non-breaking-changes).

<Note>
Beta features are supported by the JavaScript Beta SDK 3.x.x and Python SDK 2.x.x(using the Beta client).
</Note>

## Playground (deprecated)

<Warning>
Never use playground in production environments.
</Warning>

We have stopped publishing the documentation for Playground API and will gradually remove the API endpoints.
Most of the Playground APIs were removed by 2023-06-01, and the remaining will be removed subsequently.

If you have any questions or issues moving to API v1 and the latest date version, contact [support@cognite.com](mailto:support@cognite.com).

## Older API versions (removed)

The following API versions have been removed. If you make direct API calls to these versions or use SDKs or other tools that depend on them, you will receive errors.

| API version | SDK version                                                       | Removed            |
| ----------- | ----------------------------------------------------------------- | ------------------ |
| API 0.5     | Python SDK 0.x.x or earlier<p></p>JavaScript SDK 1.x.x or earlier | March 22, 2021     |
| API 0.6     | Python SDK 0.x.x or earlier<p></p>JavaScript SDK 1.x.x or earlier | April 1, 2020      |
| API 0.4     | Python SDK 0.11.x or earlier                                      | September 10, 2019 |
| API 0.3     | Python SDK 0.11.x or earlier                                      | September 10, 2019 |

## Backwards compatibility

If we change the current stable API in a backwards-incompatible way, we will release those changes as a new calendar version.

A backwards-incompatible change is a change that causes a previously successful request or successful parsing of the response to fail or give different results after the change is released.

### Backwards-compatible (non-breaking) changes

The following are the examples of backwards-compatible (non-breaking) changes:

- Increasing the request limits.
- Adding properties to the response body.
- Adding properties to the request body (as long as it doesn't affect earlier requests).
- Adding support for more query parameters (as long as it doesn't affect earlier requests).

### Backwards-incompatible (breaking) changes

The following are the examples of backwards-incompatible (breaking) changes:

- Removing an endpoint.
- Removing a property in the response.
- Changing the error response format (not the status code).
- Changing the status code from 2xx to another 2xx.
- Reducing the limit on a request (not the response). Example: the number of assets to retrieve by `assetIds`.
- Changing the name of query parameters and body properties.
- Changing the sort order if the original order was expected by the user.


<!-- SOURCE_END: docs.cognite.com/docs\dev\API_versioning.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\concepts\aggregation\calendar.mdx -->
## File: docs.cognite.com/docs\dev\concepts\aggregation\calendar.mdx

---
title: "Calendar and time zone"
description: "Learn how to use Cognite Data Fusion in a local time zone with queries that span over calendar entities like months."
content-type: "concept"
audience: [developer, dataEngineer]
experience-level: 200
lifecycle: use
---
Calendar and time zones applies only to **aggregate** queries for time series data points, and affects only the intervals we aggregate over. The data points are ingested, stored, and returned in **UTC** time.

We support all time zones in the common IANA time zone database, including time zones with daylight saving time (DST), like `Europe/Oslo` or `America/New_York`. We also support time zones with half-hour offsets, like `Asia/Kolkata` (UTC+05:30) or 15 minute offsets, like `Asia/Kathmandu` (UTC+05:45). You can also enter a custom time zone, like `UTC+02:30`. If nothing is specified, we default to `UTC`.

<Warning>
  Queries over time zones with non-zero minute offsets may be slower than time
  zone queries with whole hour offsets. If queries start to fail, try to use a
  smaller time range or use a lower limit.
</Warning>

## Aggregate granularities

Calendars can be used with three granularities, `hour`, `day`, and `month`. The `minutes`and `second` aggregates do not change with the chosen calendar.

### Month

**Month** is denoted by `month` or `mo` (not to be confused with `m` for **minute**). The month starts on the first day in the month, at 00:00 in the given time zone, and ends (exclusively) on the first day of the next month.

Month aggregates take DST transitions into account, which means that, for instance, the length of March and October may vary, depending on the time zone.

Just like other aggregates, you can prefix the granularity with a number, like `3month` or `12mo`. This way, you can retrieve aggregates for a **quarter** or a **year**. The offset is determined by the start time. For example, if you query for `3month` starting at `2022-02-01` (in the given time zone), you will get the aggregates for February through April, May through July, August through October, and November through January.

### Day

**Day** is denoted by `day` or `d`, starts at 00:00 in the given time zone, and ends (exclusively) at 00:00 the next day.

Day aggregates will take DST transitions into account, in which case the length of the day can be more or less than 24 hours.

### Hour

**Hour** aggregates (denoted by `hour` or `h`) will in general behave the same as when the time zone is not set. The only relevant change is when the time zone has an offset that is not a whole hour, like `Asia/Kolkata`. In this case, we will round the start time down to the nearest whole hour in the given time zone.

Hour aggregates do **not** take regular DST transitions into account. `24h` is, in general, always 24 hours, even if the day is 23 or 25 hours long.

## Synthetic time series

In synthetic time series, time zones and calendar queries are analogous to regular aggregate queries.

We support the exact same granularities and time zones.

The time zone is provided for the query as a whole, while the granularities are specified for each time series in the query. The alignment of each time series is rounded down to the nearest whole granularity unit in the given time zone. For instance, `7d` will align to local midnight of the day of the alignment timestamp.

<Warning>
The default alignment is epoch, or January 1st 1970 **UTC**. If you use a time zone with a negative offset, like America/Chicago (UTC-05:00), epoch corresponds to December 31st 1969 local time, which for month aggregates will round down to December 1st 1969.

If this is not the intended behavior, please specify the correct alignment in the expression.

</Warning>

## Special cases

<Warning>
  We only support queries with offsets that are multiple of 15 minutes, not like
  `UTC+05:21:10` (`Asia/Kolkata` until 1905). Such unaligned offsets were more
  common at the start of the 20th century, and were abolished after 1980. Use a
  later start time, or use a different time zone, for instance fixed
  `UTC+05:30`.
</Warning>

There are some special cases where `24h` is not 24 hours, during transitions that are not a whole
hour.

If there are two midnights on a given day (DST transition from 01:00 to 00:00), we will use the
first midnight as the dividing point.

## Example queries

### Retrieve data points

```json wrap
    POST /api/v1/projects/{project}/timeseries/data/list
    Content-Type: application/json

    {
        "items": [
            {
            "limit": 100,
            "externalId": "your external id",
            "aggregates": ["count"],
            "granularity": "3mo",
            "timeZone": "Australia/Adelaide",
            "start": 1580500000000
            }
        ]
    }

    Response:
    {
        "items": [
            {
                "id": 123,
                "externalId": "your external id",
                "isString": false,
                "isStep": false,
                "datapoints": [
                    {
                        "timestamp": 1580477400000,
                        "count": 2
                    },
                    {
                        "timestamp": 1588257000000,
                        "count": 1
                    },
                    {
                        "timestamp": 1604151000000,
                        "count": 5
                    }
                ]
            }
        ]
    }
```

The start time, 1580500000000, corresponds to Jan 31 2020 19:46:40 UTC. In Australia/Adelaide, however, the local time is Feb 01 2020 06:16:40, in other words, in February.

The timestamp of the returned data points, 1580477400000, 1588257000000 and 1604151000000 corresponds to local timestamps Feb 01 2020 00:00:00, May 01 2020 00:00:00, and Nov 01 2020 00:00:00, respectively. In this example, we assume the aggregate from August through October was empty and omitted.

Note that the UTC timestamps in the response vary between 13:30 and 14:30, depending on the local
time zone.

```json wrap
    POST /api/v1/projects/{project}/timeseries/data/list
    Content-Type: application/json

    {
        "items": [
            {
                "externalId": "your external id",
                "granularity": "1day"
            },
            {
                "externalId": "your external id",
                "granularity": "6h"
            }
        ],
        "start": 1582144200000,
        "aggregates": ["count"],
        "timeZone": "UTC+01:00",
        "limit": 1
    }

    Response:
    {
        "items": [
            {
                "id": 123,
                "externalId": "your external id",
                "isString": false,
                "isStep": false,
                "datapoints": [
                    {
                        "timestamp": 1582066800000,
                        "count": 2
                    }
                ]
            },
            {
                "id": 123,
                "externalId": "your external id",
                "isString": false,
                "isStep": false,
                "datapoints": [
                    {
                        "timestamp": 1582142400000,
                        "count": 5
                    }
                ]
            }
        ]
    }
```

In this example, we use a common `start`, `aggregates`, and `timeZone` for all queries.

The start time, 1582144200000, corresponds to Feb 19 2020 21:30:00 UTC+01:00. For the day aggregate, we round down to the start of the day, for the 6h aggregate, we round down to the nearest hour, or 21:00 UTC+01:00.

Note that the count may be higher for the 6h aggregate, as it may include data points from the next day.

### Retrieve synthetic data points

```http
    POST /api/v1/projects/{project}/timeseries/synthetic/query
    Content-Type: application/json
    {
        "items": [
            {
            "expression": "TS{id:123, aggregate:'stepinterpolation', granularity:'3mo', alignment:0} + TS{id:234}",
            "timeZone": "Europe/Oslo"
            }
        ]
    }

    Response:
    {
        "items": [
            {
                "datapoints": [
                    {
                        "timestamp":7772400000,
                        "value":15.0
                    }
                    ,
                    {
                        "timestamp":13478400000,
                        "value":16.0
                    }
                    ,
                    {
                        "timestamp":15634800000,
                        "value":21.0
                    }
                    ,
                    {
                        "timestamp":20003696000,
                        "value":22.0
                    }
                ],
                "isString":false
            }
        ]
    }
```

In this synthetic example, we see that some of the datapoint timestamps are aligned to start of month in Europe/Oslo, due to the 3mo granularity. Those data points that correspond to timestamps in TS:234 are not aligned at all.


<!-- SOURCE_END: docs.cognite.com/docs\dev\concepts\aggregation\calendar.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\concepts\aggregation\index.mdx -->
## File: docs.cognite.com/docs\dev\concepts\aggregation\index.mdx

---
title: 'Aggregation'
description: 'Learn how Cognite Data Fusion aggregates and interpolates time series data, and see the details about the available aggregation functions.'
content-type: 'concept'
audience: [developer, dataEngineer, dataScientist]
experience-level: 100
article-type: 'article'
lifecycle: use
---
Learn how Cognite Data Fusion **aggregates** and **interpolates** time series data, and see the details about the available [aggregation functions](#aggregation-functions).

## Aggregation in Cognite Data Fusion

To improve **performance** and to reduce the amount of data transferred in query responses, Cognite Data Fusion **pre-calculates** the most common **aggregates** for numerical data points in time series. These aggregates are available with **millisecond response time** even when you are querying across large data sets.

In your queries, you can specify one or more aggregates (for example `average`, `min` and `max`) and also the **time granularity** for the aggregates (for example `1h` for one hour).

Aggregates are aligned to the start time modulo of the granularity unit. If you ask for daily average temperatures since Monday afternoon last week, the first aggregated data point will contain averages for the whole of Monday, the second for Tuesday, etc.

<Note>
Cognite Data Fusion determines aggregate alignment based only on the granularity unit. If you specify hour aggregates, and the start time of the request is in the middle of the hour, the start time will be rounded down to the start time of the hour.

As a result, you can get different results if you aggregate over 60 minutes than if you aggregate over 1 hour because the two queries are aligned differently. For example, if the start time is `3:43:25`:

<Frame>
<img className="illustration dark:opacity-80 dark:brightness-90" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/aggregation/granularity_hour_vs_60min_v2.png" alt="1h vs 60 min granularity"/>
</Frame>

</Note>

### Aggregating data points

Aggregation is to group together the values of many data points to form a single summary value. For example, the `count` aggregate gives the number of data points for a time range. The timestamp of the aggregate marks the beginning of the time range.

### Interpolating data points

Interpolation is to construct new data points within the range of a discrete set of known data points. The returned data points have a timestamp and a value, where the value represents the interpolated value at the time of the timestamp. The interpolation method depends on whether the time series is **stepwise** or **continuous**. Interpolated data points aren't stored and are only visible as the aggregation/interpolation results.

### Stepwise vs continuous

Interpolation and aggregation depend on how the time series is interpreted between the stored data points. A stepwise time series is assumed to keep its last reported value until a new value comes in, and then immediately jump to that new value. A continuous time series is assumed to gradually change between the stored data points and is modeled with linear interpolation.

<table>
 <thead></thead>
  <tr>
    <th>Data points <Frame><img class="illustration" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/aggregation/datapoints_v2.png" alt="Data points" width="100%"/></Frame></th>
    <th>Stepwise interpretation <Frame><img class="illustration" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/aggregation/stepwise_v2.png" alt="Stepwise interpretation" width="100%"/></Frame></th>
    <th>Continuous interpretation <Frame><img class="illustration" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/aggregation/continuous_v2.png" alt="Data points" width="100%"/></Frame></th>
  </tr>
</table>

How the times series is interpreted affects the value of aggregates. For example, the `average` aggregate, which is based on the average distance to zero, will be calculated as the area below the curve, divided by the size of the time range.

<table>
<thead></thead>
  <tr>
    <th>Stepwise interpretation <Frame><img class="illustration" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/aggregation/stepwise_aggregates_v2.png" alt="Stepwise interpretation"/></Frame></th>
    <th>Continuous interpretation <Frame><img class="illustration" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/aggregation/continuous_aggregates_v2.png" alt="Continuous interpretation"/></Frame></th>
  </tr>
</table>

### Granularity

Granularity defines the time range that each aggregate is calculated from. It consists of a time unit and a size. Valid time units are `day` or `d`, `hour`or `h`, `minute` or `m` and `second` or `s`, for example, `2h` means that each time range should be 2 hours wide, `3m` means 3 minutes.

The value of an aggregate for a time range may also depend on data outside of the time range because lines have to be drawn to the edge of the time range to compute the aggregates.

<table>
<thead></thead>
  <tr>
    <th>Data points <Frame><img class="illustration" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/aggregation/datapoints_range_v2.png" alt="Data points"/></Frame></th>
    <th>Stepwise interpretation <Frame><img class="illustration" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/aggregation/stepwise_range_v2.png" alt="Stepwise interpretation"/></Frame></th>
    <th>Continuous interpretation <Frame><img class="illustration" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/aggregation/continuous_range_v2.png" alt="Continuous interpretation"/></Frame></th>
  </tr>
</table>

{/* TODO:

#### Time window

Do we need to define this? Maybe mention inclusive begin, exclusive end?*/}

### Missing data

CDF doesn't return aggregates or interpolations for time ranges that have no data points, even if there are previous and next data points present for that period. As a result, the returned aggregates may skip large periods of time if the underlying data is sparse.

{/* TODO: what do we do if there are data points within the range, but no outside points? */}

### Previous and next data point

To interpolate a time series to the edges of the time range, many aggregates and interpolations depend on knowing the last data point before the time range, and the first data point after.

For **continuous** time series, CDF doesn't use the previous and next data points if they're more than one hour away from the time range. This is to avoid interpolating data when the underlying sensor has been down for an extended period of time.

For **stepwise** series, CDF uses the previous and next data points regardless of how distant they are.

We do not _extrapolate_ backward from the first point in a time series or forward from the last point.
This is also the case in stepwise time series, even though they are assumed to continuously maintain the value of the previous data point until the next point appears.
The rationale behind this is that we can not know the reason that the sensor isn't sending new data: it could be because the value is unchanged, or because the sensor is down.
We want to avoid implying that the sensor is always up.

## Aggregation functions

To use the aggregation functions, you construct requests that look like this:

```json wrap
POST /api/v1/projects/{project}/timeseries/data/list
Content-Type: application/json

{
  "items": [
    {
      "limit": 10000,
      "externalId": "your external id",
      "aggregates": ["aggregate function 1","aggregate function 2"],
      "granularity": "1h",
      "start": 1541424400000,
      "end":"now"
    }
  ]
}
```

Cognite Data Fusion (CDF) supports the aggregation functions described below.

{/*vale on */}

| Function                                     | How it's calculated                                                                                | When to use                                                                |
|----------------------------------------------|----------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------|
| [average](#average)                          | Integral of time series divided by the size of time range.                                         | Downsampling many noisy RAW data points.                                   |
| [max](#max)                                  | The highest value of all stored data points.                                                       |                                                                            |
| [maxDatapoint](#maxDatapoint)                | The highest value, along with its timestamp, of all stored data points.                            |                                                                            |
| [min](#min)                                  | The lowest value of all stored data points.                                                        |                                                                            |
| [minDatapoint](#minDatapoint)                | The lowest value, along with its timestamp, of all stored data points.                             |                                                                            |
| [count](#count)                              | The count of stored data points.                                                                   |                                                                            |
| [sum](#sum)                                  | The sum of values of all stored data points.                                                       |                                                                            |
| [interpolation](#interpolation)              | The interpolated value at the start of each time range.                                            | Interpolating sparse irregular data to regularly spaced time series.       |
| [stepInterpolation](#stepinterpolation)      | The interpolated value at the start of each time range, treating time series as stepwise.          |                                                                            |
| [continuousVariance](#continuousvariance)    | The variance of the underlying function when assuming linear or step behavior between data points. | Uneven spacing between data points, if interpolation is a good assumption. |
| [discreteVariance](#discretevariance)        | The variance of the discrete set of data points, no weighting for density of points in time.       | Evenly spaced data points.                                                 |
| [totalVariation](#totalvariation)            | The sum of absolute differences between neighboring data points in a period.                       | Data quality checks or outlier detection.                                  |
| [countGood](#status-code-aggregates)         | The count of stored data points with a good status code.                                           |                                                                            |
| [countUncertain](#status-code-aggregates)    | The count of stored data points with an uncertain status code.                                     |                                                                            |
| [countBad](#status-code-aggregates)          | The count of stored data points with a bad status code.                                            |                                                                            |
| [durationGood](#status-code-aggregates)      | Duration of data points with a good status code.                                                   |                                                                            |
| [durationUncertain](#status-code-aggregates) | Duration of data points with an uncertain status code.                                             |                                                                            |
| [durationBad](#status-code-aggregates)       | Duration of data points with a bad status code.                                                    |                                                                            |
{/* vale off */}

### average

<table>
<thead></thead>
  <tr>
    <th>Data points <Frame><img class="illustration" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/aggregation/datapoints_range_v2.png" alt="Data points"/></Frame></th>
    <th>Stepwise interpretation <Frame><img class="illustration" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/aggregation/stepwise_range_v2.png" alt="Stepwise interpretation"/></Frame></th>
    <th>Continuous interpretation <Frame><img class="illustration" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/aggregation/continuous_range_v2.png" alt="Continuous interpretation"/></Frame></th>
  </tr>
</table>

The `average` function computes the time-weighted average value of the time series, for each time range.
The value is defined as the integral of the time series divided by the length of the time range.
In the figures, this is represented as the average height of the grey area.

<Note>
To retrieve the average value of the _individual_ data points (arithmetic mean), use the `sum` function, divided by the `count` function.
The `average` function interpolates between data points, also if these are outside the time range,
and can thus differ significantly from the average of the individual data points. It can even be greater/smaller than `max`/`min`.
</Note>

When calculating the average, there is a difference between the stepwise and continuous data.

The stepwise data always extends to the previous/next data point, no matter the distance. It also extends to end of the period after the last data point.

For a continuous time series, if there is no data previous/next to the time range, or that data is more than 1 hour away,
CDF doesn't interpolate backwards/forwards, and the integral is only done on parts of the time range.
Continuous data ends at the last data point.

<table>
<thead></thead>
  <tr>
    <th>Continuous time series with no previous data points <Frame><img class="illustration" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/aggregation/continuous_range_no_prev_next_v2.png" alt="No next/previous data points"/></Frame></th>
  </tr>
</table>

### max

For each time range, the `max` function returns the highest value of the stored data points in the time range.

<table>
<thead></thead>
  <tr>
    <th>max function <br /><Frame><img class="illustration" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/aggregation/datapoints_max_interpolate_v2.png" alt="Interpolated values at the edge not included"/></Frame></th>
  </tr>
</table>

The function doesn't include interpolated values at the edges of the time range. This means that the average can be greater than max.

### maxDatapoint
`maxDatapoint` is the same as `max`, but it returns an object with the highest value and its timestamp. If there are multiple data points with the same maximum value, the one with the earliest timestamp is returned. If `includeStatus` is true, we also return the status code where it is not `Good`.


### min

For each time range, the `min` function returns the lowest value of all stored data points.

<table>
<thead></thead>
  <tr>
    <th>min function <br /><Frame><img class="illustration" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/aggregation/datapoints_min_interpolate_v2.png" alt="Interpolated values at the edge not included"/></Frame></th>
  </tr>
</table>

The function doesn't include interpolated values at the edges of the time range.

### minDatapoint
`minDatapoint` is the same as `min`, but it returns an object with the lowest value and its timestamp. If there are multiple data points with the same minimum value, the one with the earliest timestamp is returned. If `includeStatus` is true, we also return the status code where it is not `Good`.

### count

The `count` function returns the number of data points for each time range. If there are no data points in a time range, this function returns no data.

<table>
<thead></thead>
  <tr>
    <th>count function <br /><Frame><img class="illustration" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/aggregation/count_v2.png" alt="the count function"/></Frame></th>
  </tr>
</table>

### sum

The `sum` function returns the sum of the values of all data points in the time range, or nothing if there are no data points.

<table>
<thead></thead>
  <tr>
    <th>sum function <br /><Frame><img class="illustration" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/aggregation/sum_v2.png" alt="the sum function"/></Frame></th>
  </tr>
</table>

### interpolation

The `interpolation` function interpolates the value of the time series at the start of each time range. The method of interpolation is based on whether the time series is continuous or stepwise.

**Note:** For stepwise time series this is the same as the `stepInterpolation` function.

<table>
<thead></thead>
  <tr>
    <th>Data points <Frame><img class="illustration" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/aggregation/datapoints_interpolation_v2.png" alt="Data points"/></Frame></th>
    <th>Stepwise interpretation <Frame><img class="illustration" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/aggregation/stepwise_interpolation_v2.png" alt="Stepwise interpretation"/></Frame></th>
    <th>Continuous interpretation <Frame><img class="illustration" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/aggregation/continuous_interpolation_v2.png" alt="Continuous interpretation"/></Frame></th>
  </tr>
</table>

### stepInterpolation

Same as `interpolation`, but always treats the time series as stepwise.

### continuousVariance

The variance of a function _f_ is the expectation value of _f_ squared, minus the square of the expectation value of _f_.

If CDF only has the value of _f_ in a finite number of points, there are different approaches to approximate the variance. The continuous variance aggregate is intended for situations where the piecewise linear function that interpolates between the data points is a good approximation. If this function is _f_, CDF defines the continuous variance in a time period from _t_=_a_ to _t_=_b_ as:

$$
V_c = \frac{1}{b-a}\int_a^b f(t)^2 dt - \left(\frac{1}{b-a}\int_a^b f(t) dt\right)^2
$$


The time intervals between data points can vary due to a sampling setting that tries to capture the behavior of _f_ with a piecewise linear function (or a step function) using relatively few data points. These are cases when the continuous variance is a meaningful variance for the function. On the other hand, if the data points are sampled at even time intervals, independently of the value of _f_, the piecewise linear function will cut away extremal points, and CDF will get a variance lower than the actual variance.

### discreteVariance

The `discreteVariance`function is for cases where the data points are measured at regular time intervals, independently of the values they measure. In these cases, CDF can regard the data points as a random sampling of the values in the time period. CDF defines the variance as:

$$
V_d = \frac{1}{n}\sum_{i=1}^n f(t_i)^2 - \left(\frac{1}{n}\sum_{i=1}^n f(t_i)\right)^2
$$

### totalVariation

The `totalVariation` function returns the total absolute change in the function values within a time interval. If the time interval goes from _t_=_a_ to _t_=_b_ with _n_ data points, the total variation is defined as:


$$
V = |f(t_1) - f(a)| + \sum_{i=1}^{n-1} |f(t_{i+1}) - f(t_i)| + |f(b) - f(t_n)|
$$


CDF uses the interpolated values for _f_ at _a_ and _b_.

### status code aggregates
The `count<status>` and `duration<status>` aggregates use the [status code](/dev/concepts/reference/status_codes) of the data points, not the values. Only the [main status codes](/dev/concepts/reference/status_codes#main-status-codes), `Good`, `Uncertain`, and `Bad` are used.

The `count<status>` aggregates returns the number of data point in each interval with the given status.

The `duration<status>` aggregates adds up the duration (milliseconds) the time series has in the given status. Equivalent, the duration that the previous data point has the given status, and is in range.

These aggregates do not take `treatUncertainAsBad` or `ignoreBadDatapoints` into account, [unlike](/dev/concepts/reference/status_codes#aggregate-behavior) the other aggregates.

### state aggregates

<Note>
These aggregates are only available for [state time series](/dev/concepts/resource_types/state_timeseries).
</Note>

State time series support specialized aggregations for analyzing discrete operational states and state transitions:

| Function                              | How it's calculated                                        | When to use                                           |
|---------------------------------------|------------------------------------------------------------|-------------------------------------------------------|
| [stateCount](#statecount)             | The number of data points for each state in the time range | Analyzing state frequency within periods              |
| [stateTransitions](#statetransitions) | The number of transitions into each state                  | Detecting state change patterns and equipment cycling |
| [stateDuration](#stateduration)       | The total time (milliseconds) spent in each state          | Calculating uptime, downtime, or state-based KPIs     |

#### stateCount

For each time range and each state, the `stateCount` aggregate returns the number of data points with that state. This only counts data points with a `Good` status (or `Uncertain` if `treatUncertainAsBad` is false).

The results are returned in the `stateAggregates` array, with one entry per state present in the time range.

#### stateTransitions

For each time range and each state, the `stateTransitions` aggregate counts the number of times the equipment transitioned **into** that state from a different state (or bad status).

<Note>
The first data point of a time series is counted as a transition if it has a `Good` status (or `Uncertain` if `treatUncertainAsBad` is false). Transitioning from no state to an initial state counts as a transition.
</Note>

This is useful for detecting:

- Equipment cycling patterns (frequent `ON`/`OFF` transitions).
- State instability.
- Equipment behavior anomalies.

#### stateDuration

For each time range and each state, the `stateDuration` aggregate returns the total time (in milliseconds) the equipment was in that state.

<Note>
State durations only extend to the end of an aggregate period if there is a subsequent data point after the period ends. 

For example, if a state changes from `CLOSED` to `OPEN` at 08:00, the `CLOSED` duration will be calculated up to 08:00, and the `OPEN` duration will be zero. This applies regardless of the current time or the end of the aggregate period, unless there is a new data point after the aggregate period ends.
</Note>

This is useful for:

- Calculating uptime/downtime percentages.
- State-based performance metrics.
- Compliance reporting (time in specific operational modes).

<Warning>
Traditional numeric aggregations (like `min`, `max`, `average`, `sum`, `variance`) are not applicable to state time series and will return an error if requested.
</Warning>

<!-- SOURCE_END: docs.cognite.com/docs\dev\concepts\aggregation\index.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\concepts\data_point_subscriptions\index.mdx -->
## File: docs.cognite.com/docs\dev\concepts\data_point_subscriptions\index.mdx

---
title: 'Data point subscriptions'
description: 'Learn how to subscribe to time series updates in real-time using data point subscriptions to monitor changes across large sets of time series.'
content-type: 'concept'
audience: [developer, dataEngineer]
experience-level: 100
article-type: 'article'
lifecycle: use
---
For more information on how to work with data point subscriptions, see the [Data point subscriptions API documentation](https://api-docs.cognite.com/20230101/tag/Data-point-subscriptions).

## How data point subscriptions differ from time series API
Subscriptions will provide you the **changes** to a given set of time series, while the time series API will give you the **current state** of the time series.

This is especially important if you ingest data out of order or delete data point ranges. Subscriptions can retrieve all changes, no
matter the time stamp of the data points, while the time series API requires you to specify a time range to query.

Another significant difference is that subscriptions can query data from a large amount of time series,
while the time series API is limited to 100 time series per request.

### Use cases for data point subscriptions
Consider using data point subscriptions in the following scenarios:

- When you need to listen to updates that arrive out of order.
- When you want to monitor changes across a large set of time series.
- When you require a constantly updated local copy of time series data.
- When you aim to keep track of changes in a dynamic set of time series.

### Limitations of data point subscriptions
However, there are several limitations to keep in mind when using data point subscriptions:

- Subscriptions tend to be slower than regular endpoints for time series data points and aggregates, especially when data in small batches gets ingested, deleted, or overwritten.
- Subscriptions only maintain order based on ingestion time; you cannot filter by time series or data point timestamps.
- Subscriptions can return data that was subsequently deleted or overwritten.
- Subscriptions do not store client state on the server; it is necessary to track progress locally.

## Set up a subscription
The first step is to decide on what you want to subscribe to.
You can subscribe to data points for a specific set of time series or a set of time series that match a filter.

### Subscribe to a set of time series
To subscribe to a set of time series, you need to provide a list of external IDs. You can update this list anytime, and it will immediately take effect for the subscription.

### Create a filter subscription
To create a filter subscription, you need to provide a search filter. This filter will be evaluated against all time series in the project. Time series that match the filter will automatically be added to the subscription. Conversely, time series that no longer match the filter or have been deleted will be removed from the subscription. Note that there may be a short delay between the time a series is created or updated to match the filter and the time it is added to the subscription.

It's important to note that only data points ingested into a time series after it becomes a member of the subscription will be included in the subscription. Therefore, data points ingested immediately after the creation of a time series might be omitted from the subscription.

With a filter subscription, you can subscribe to all time series that match specific criteria, such as a particular asset, data set, metadata value prefix, or a combination of these. The filter language is similar to that used in the list endpoint of the time series API.

## Query the subscription
Once you have created a subscription, we will start recording changes to its time series, and you can query these changes through the `/data/list` endpoint.

The query will return the following information:
- A **list of data point changes** that includes either data point upserts or data point range deletes for a given time series.
- A **list of subscription changes** that includes the list of time series added to or removed from the subscription. When you create a subscription, all time series in the subscription will be marked as added.
- A **cursor** to query the next page of results.
- A **hasNext** field indicates if more results are available.

### Long polling

The `/data/list` endpoint will by default use _long polling_.
- If there are any pending changes, the API will respond immediately.
- If there are no pending changes, the API will wait for changes for up to 5 seconds.

The long polling period can be changed by setting the `pollTimeoutSeconds` property in the request body.
Long polling can be disabled by setting the property to 0.

### Cursor
If you consider a subscription a stream of data, the cursor is the position in the stream where you are currently reading. It is very similar to an offset in Kafka.

The cursor is a string that is opaque to the user and keeps track of the position in the stream. The user must store the cursor. It is not stored in Cognite Data Fusion. It does not expire, and you may use it multiple times.

#### Initializing the cursor
When you create the subscription, the cursor gets initialized to the creation time of the subscription. The first query will return all the changes since the subscription was created.

To start from a different point in time, you can specify a start time in the query (e.g. "1d-ago"), and the cursor will be initialized to approximately that time.

### HasNext
The hasNext field indicates if there are more results available. If true, you can query the next results page using the next cursor.
If it is false, no more results are available, so you should wait a little while for more data to come in and try again using the next cursor.

## Retention
The retention time for data point subscriptions is 7 days. Data older than 7 days will not be returned in the query.
This applies both to data points and subscription changes.

## How to create and maintain a local copy of your CDF time series data
A subscription will provide you with changes to a set of time series. Depending on the change type,
you apply changes to the local copy in one of three ways:
1. If the subscription says a time series has been deleted, delete it from your local copy.
2. If the subscription says a time series has been added, create it in your local copy and then query the time series API to get the current state of the time series.
3. If the subscription says data points have been updated, apply the updates to your local copy in the same order as they appear in the subscription.

Each change has to be applied in full, in the order they appear in the subscription.

Every time you have reached the end of the subscription stream, your local copy will be up-to-date.

Make sure that you never fall more than 7 days behind, or you will have to re-create your local copy of all time series in the subscription, and then apply subscription changes from a time before the re-creation.

Also, if a time series is marked as both deleted and added, you may have to check which is correct.

## Advanced usage
### Partitions
Subscriptions with many high-volume time series will return a lot of data. If this data is too much for a single client to handle,
you can use partitions to split the subscription data into multiple partitions (streams).

When you create or update a subscription, each time series will be assigned to **exactly one** partition. For instance,
a subscription with 6 time series could be divided into 3 partitions as follows: (1,6), (4), (2,3,5).
A partition can contain zero time series and thus no data.

Each partition will have its cursor, and you query each partition independently.


<!-- SOURCE_END: docs.cognite.com/docs\dev\concepts\data_point_subscriptions\index.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\concepts\external_id.mdx -->
## File: docs.cognite.com/docs\dev\concepts\external_id.mdx

---
title: 'Define unique IDs with externalId'
description: 'Learn how to use the externalId field to define unique IDs for data objects in CDF and maintain relationships between source systems.'
content-type: 'concept'
audience: [developer, dataEngineer]
experience-level: 100
article-type: 'article'
lifecycle: use
---
The external ID field, (`externalId`), is available in API v1 for time series, assets, events, files, and sequences.
It lets you define a unique ID for each data object in Cognite Data Fusion (CDF) as an optional extension to the existing `id` field.

The purpose of external IDs is to:

- Avoid duplicates when you insert data.
- Avoid extra lookups when you update data.
- Maintain relationships between data objects from source systems.
- Use the time series `name` field for display instead of lookup.
- Give you a way of performing key-value lookups with keys you control that works the same across resources.

Make sure to set an `externalId` on all CDF resources for better performance.

## Properties of external IDs

{/* vale off */}

The external ID is unique per resource type. An **asset** with `externalId=123` will **not** interfere with a **time series** with `externalId=123`.

{/* vale on */}

If you try to create a data object with an existing external ID for a resource type, you'll cause an error.

The field is **optional** and **mutable**, so users can choose to add external IDs to data objects that already exist in CDF. If you try to update the external ID to an existing external ID, you'll cause an error.

An empty external ID isn't treated as a unique value.

The external ID limits to 255 characters, and leading and trailing white spaces aren't allowed. The external ID is case-sensitive.

## Migrate from earlier API versions

For **time series** created in earlier versions of the API, the `name` field was **unique**, and the `externalId` field automatically populated with the value of the `name` field. The external ID is mutable, and you can override this value.

All **other resource types** created in earlier API versions will start with an empty `externalId` field.

## Key collisions

When you populate the external IDs, it's important that you build a structure in each ID that avoids key collisions. For example, some databases use incrementing integers as row keys, and it's probably a good idea to use for instance `<database name>/<table name>/<row key>` as the external ID instead of just the row key.

## ID vs. external ID

The existing `id` field, which is a pseudo-random integer between 1 and 2^53-1, remains a valid way of doing a key-value lookup on data objects in CDF. All data objects still have an `id`, and `externalId` is an optional extension that lives side-by-side with it.

You can't specify both the `id` and the `externalId` in the same object when you do **lookups**. However, batch lookups in the same request, for example `/byids`, can mix `id` and `externalId` if each object has only one of the fields.

{/* vale off */}
CDF does **not** support external IDs in GET requests to `/resource/:externalId`, as it does with the existing `id` field. You must use the `POST resource/byids` endpoint instead.



<!-- SOURCE_END: docs.cognite.com/docs\dev\concepts\external_id.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\concepts\pagination.mdx -->
## File: docs.cognite.com/docs\dev\concepts\pagination.mdx

---
title: 'Pagination'
description: 'Learn how to paginate CDF resources using cursors, including parallel retrieval techniques and datapoint pagination strategies.'
content-type: 'concept'
audience: [developer, dataEngineer]
experience-level: 200
article-type: 'article'
lifecycle: use
---
Most resource types in Cognite Data Fusion (CDF) can be paginated, as indicated by the `nextCursor` field in the response. Pass the value of `nextCursor` as the cursor to get the next page of limit results. Note that all parameters except `cursor` have to stay the same.

A request can return fewer results than its limits, as long as there is a `nextCursor` that indicates that there are more data to fetch.

## Simple cursor example

```json wrap
HTTP GET /assets?limit=1

{
  "nextCursor": "8ZiApWzGe5RnTAE1N5SABLDNv7GKkUGiVUyUjzNsDvM",
  "items": [
    {
      "name": "23-TE-96116-04",
      "parentId": 3117826349444493,
      "description": "VRD - PH 1STSTGGEAR THRUST BRG OUT",
      "metadata": {
        "ELC_STATUS_ID": "1211",
        "RES_ID": "525283"
      },
      "id": 702630644612,
      "createdTime": 0,
      "lastUpdatedTime": 0,
      "rootId": 6687602007296940
    }
  ]
}
```

Getting the next page of assets.

```http wrap
HTTP GET /assets?limit=1&cursor=8ZiApWzGe5RnTAE1N5SABLDNv7GKkUGiVUyUjzNsDvM
```

<Note>
When you send more parameters to `/assets`, make sure that you also include them in all further requests in addition to the `cursor` parameter.
</Note>


## Parallel retrieval

**Parallel retrieval** is a technique used to improve data retrieval performance in cases where, due to query complexity, data retrieval speeds are lower than they would be with a fast, simple query. Use parallel retrieval to retrieve large data sets up to the capacity limits defined for an API service.

The CDF APIs support parallel retrieval through the `partition` parameter, where the parameter is in the format `m/n` and `n` represents the number of partitions to split the entire data set into. CDF supports a maximum of 10 concurrent partitions. Attempts to retrieve a higher number of parallel partitions may be detrimental to client data retrieval performance due to throttling.

<Note>
Parallel retrieval doesn't act as a speed multiplier on optimally running queries. If adding more partitions doesn't improve data retrieval performance, don't add them.
</Note>

### Example

This example downloads an entire data set in parallel by splitting it into 10 partitions and doing the following requests with `m` running from 1 to 10:

```http
HTTP GET /events?type=WORKORDER&partition=1/10
HTTP GET /events?type=WORKORDER&partition=2/10
HTTP GET /events?type=WORKORDER&partition=3/10
```

Each of the results looks similar to this:

```json wrap
{
  "items": [
    {
      "startTime": 1266130800000,
      "endTime": 1284328234000,
      "type": "Workorder",
      "subtype": "VAL",
      "assetIds": [4650652196144007],
      "source": "cdf-tenant",
      "id": 206950949774808,
      "lastUpdatedTime": 1539151642457,
      "createdTime": 1533747616899
    }
  ],
  "nextCursor": "bU-qc7X1jKMUbXgOYdONu2kRvpsoc60v9qh0Votm4MT0LaH0J0VVhVLoorSh8j_j"
}
```

Follow the `nextCursor` in each response until each partition is drained.

<Note>
Make sure that you send the filter parameters to each request that follows.
</Note>

{/*- How should we spell data point: datapoint or data point? -*/}

## Datapoint pagination

Datapoints, including aggregates, can use pagination similar to other endpoints. The first datapoint on the second page will follow immediately after the last datapoint on the first page.

The main difference to other endpoints is that you will receive a `nextCursor` for each time series that has more data and that the `nextCursor` fields are *inside* the elements of the `items` array. In further requests, you must combine each cursor alongside its corresponding time series.

If the other parameters are equal, you may combine `(time series, cursor)` pairs from different requests.

You may also drop the time series you've completed and increase the limit on the remaining time series. Requests with smaller time series with a higher limit will be more effective than paging.

**Datapoint cursors** are _opaque_ and not designed to be meaningful to users. The cursors aren't encrypted but shouldn't be reverse-engineered to construct a new cursor to supply to the API.

Cursors are valid for a reasonable time after the original API call, also if they've been used in a follow-up request. Still, we don't guarantee a minimum validity time, and you shouldn't store cursors to use them after a protracted time.

Cursors from different requests don't affect each other, and you can concurrently submit distinct series of cursor-based requests even if they cover the same period.

In general, distinct API calls aren't atomic with each other, also not when using cursors. The cursor doesn't "remember" the state of the data points at the time of the original request.

An API call that provides a cursor that was returned by a previous call will only see points with a timestamp after the last timestamp in the previous call. However, the second call **might see** some data points that didn't exist at the time of the first call and **might not see** some data points that did exist at the time of the first call. Also, the second call may let you see a partial result from an unrelated, concurrent request. If the unrelated request adds or deletes data points in a range that straddles the timestamp that the cursor points to, the second cursor-based request might see the result of the other request even though the first request that produced the cursor didn't.

### Example

```json wrap
{
  "items": [
    {
      "id": 154872395793457,
      "externalId": "thermometer",
      "isString": false,
      "isStep": false,
      "unit": "kelvin",
      "datapoints": [
        {
          "timestamp": 427629600000,
          "value": 183.95
        }
      ],
      "nextCursor": "jkewklavngoatOPari4nsLGNKd453sa-asl"
    },
    {
      "id": 1897391123,
      "externalId": "CO2",
      "isString": false,
      "isStep": false,
      "unit": "ppm",
      "datapoints": [
        {
          "timestamp": 1654027200000,
          "average": 420.99
        }
      ],
      "nextCursor": "awnl2ao42aiNawKa24Lk4vab021lnkaj"
    }
  ]
}
```

Now, take a look at the example request. Each time series includes the corresponding cursor.

```http
HTTP POST /timeseries/data/list
```

```json
{
  "items": [
    {
      "externalId": "thermometerA",
      "cursor": "jkewklavngoatOPari4nsLGNKd453sa-asl"
    },
    {
      "externalId": "CO2",
      "cursor": "awnl2ao42aiNawKa24Lk4vab021lnkaj",
      "aggregates": ["average"],
      "granularity": "1d"
    }
  ],
  "start": 400000000000,
  "end": 1700000000000,
  "limit": 1
}
```

<Accordion title="Click to view the full example">

All requests are sent to this endpoint:

```http
HTTP POST /timeseries/data/list
```

#### Regular query

Query for common datapoints without cursors:

```json wrap
{
  "items": [
    {
      "externalId": "thermometerA"
    },
    {
      "externalId": "thermometerB"
    }
  ],
  "start": 400000000000,
  "end": 1700000000000,
  "limit": 1
}
```

Response from the first query:

```json wrap
{
  "items": [
    {
      "id": 154872395793457,
      "externalId": "thermometerA",
      "isString": false,
      "isStep": false,
      "unit": "kelvin",
      "datapoints": [
        {
          "timestamp": 427629600000,
          "value": 183.95
        }
      ],
      "nextCursor": "jkewklavngoatOPari4nsLGNKd453sa-asl"
    },
    {
      "id": 254872395793458,
      "externalId": "thermometerB",
      "isString": false,
      "isStep": false,
      "unit": "kelvin",
      "datapoints": [
        {
          "timestamp": 427629600000,
          "value": 183.95
        }
      ]
    }
  ]
}
```

- **thermometerA** may have more datapoints in the range. Use the cursor to retrieve them.
- **thermometerB** has reached the latest datapoint in the range. Increase `start`/`end` to retrieve more datapoints.

#### Aggregate query

```json wrap
{
  "items": [
    {
      "externalId": "CO2-A",
      "aggregates": ["average"],
      "granularity": "1d"
    },
    {
      "externalId": "CO2-B",
      "aggregates": ["average"],
      "granularity": "1d"
    }
  ],
  "start": 400000000000,
  "end": 1700000000000,
  "limit": 1
}
```

Aggregate response:

```json wrap
{
  "items": [
    {
      "id": 1897391123,
      "externalId": "CO2-A",
      "isString": false,
      "isStep": false,
      "unit": "ppm",
      "datapoints": [
        {
          "timestamp": 1654027200000,
          "average": 420.99
        }
      ],
      "nextCursor": "awnl2ao42aiNawKa24Lk4vab021lnkaj"
    },
    {
      "id": 2897391124,
      "externalId": "CO2-B",
      "isString": false,
      "isStep": false,
      "unit": "ppm",
      "datapoints": [
        {
          "timestamp": 1654027200000,
          "average": 420.99
        }
      ]
    }
  ]
}
```

- **CO2-A** have more aggregate datapoints in the range. Use the cursor to retrieve them.
- **CO2-B** has reached the latest interval in the range with at least one aggregate. Increase start/end to fetch more aggregates.

#### Request with cursors

Each **externalId** is paired with the corresponding cursor. CDF requests time series from the first and second requests because the parameters are compatible. In this case, the start and end are equal.

```http
HTTP POST /timeseries/data/list
```

```json wrap
{
  "items": [
    {
      "externalId": "thermometerA",
      "cursor": "jkewklavngoatOPari4nsLGNKd453sa-asl"
    },
    {
      "externalId": "CO2-A",
      "cursor": "awnl2ao42aiNawKa24Lk4vab021lnkaj",
      "aggregates": ["average"],
      "granularity": "1d"
    }
  ],
  "start": 400000000000,
  "end": 1700000000000,
  "limit": 1
}
```

Response from request with cursors:

```json wrap
{
  "items": [
    {
      "id": 154872395793457,
      "externalId": "thermometerA",
      "isString": false,
      "isStep": false,
      "unit": "kelvin",
      "datapoints": [
        {
          "timestamp": 1263769200000,
          "value": 249.15
        }
      ],
      "nextCursor": "NIaoitj4jwlkrafjalkdjfalgkjladkjflgawa"
    },
    {
      "id": 1897391123,
      "externalId": "CO2-A",
      "isString": false,
      "isStep": false,
      "unit": "ppm",
      "datapoints": [
        {
          "timestamp": 1656626400000,
          "average": 420.95
        }
      ]
    }
  ]
}
```

</Accordion>


<!-- SOURCE_END: docs.cognite.com/docs\dev\concepts\pagination.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\concepts\reference\status_codes.mdx -->
## File: docs.cognite.com/docs\dev\concepts\reference\status_codes.mdx

---
title: 'Status codes'
description: 'Learn how status codes determine the quality of time series data points and impact data retrieval, aggregate computation, and visualization.'
content-type: 'reference'
audience: [developer, dataEngineer]
experience-level: 200
article-type: 'article'
lifecycle: use
---
Status codes are used to determine the quality of time series data points.
The status code impacts data retrieval, aggregate compuation, and how the time series should be visualized.
The implementation follows the OPC UA standard, and both the [Cognite OPC UA][opcua-extractor] and [PI extractors][pi-extractor] support this feature.

By default, the time series API only includes data points where the status code is _Good_ and disregards other data points.
To include data points with other status codes, you must pass additional parameters to the API.

[opcua-extractor]: /cdf/integration/guides/extraction/opc_ua/
[pi-extractor]: /cdf/integration/guides/extraction/pi

## Main status codes
All status codes fall into one of the main categories: _Good_, _Uncertain_, or _Bad_.
If status code is missing, it is assumed to be _Good_ (with code = 0).

The API uses decimal numbers to represent status codes, but we have included the hexadecimal representation for reference.

| Symbolic representation | Code (decimal) | Code (hexadecimal) |
|------------------------|----------------|-------------------|
| Good                   | 0              | 0x00000000        |
| Uncertain              | 1073741824     | 0x40000000        |
| Bad                    | 2147483648     | 0x80000000        |


### Good
_Good_ status codes are the default, and indicate that the data point is valid. If status
code is missing, it is assumed to be _Good_ with code = 0.

### Bad
_Bad_ status codes are by default omitted from the responses, and indicate that the value cannot be trusted.

### Uncertain
_Uncertain_ status codes may be treated as _Bad_ (default) or _Good_, depending on the user.

## Status code structure
A status code consists of a 32-bit unsigned integer _code_, and a string _symbolic representation_.

A permitted status code consists of a _category_ and a set of _modifier flags_ or _info flags_.
Use the category as is, or combine it with the modifier flags to get the full status code.

To obtain the full numeric code,
you add (or bitwise OR) the values for the category and the modifier flags.
To obtain the full symbolic representation, you concatenate the representations for the category and the modifier flags.

The code fields can be added together, while the representations can be concatenated,
starting with the category and then the modifiers in order.

For instance is `"BadBrowseNameInvalid, StructureChanged, Low"` = 2153775104 + 32768 + (1024) + 256 = 2153809152.
The 1024 is added when _info flags_ are used. Here we have set Limit to Low.

## Subcategories

<Accordion title="Click to see full list of subcategories">
| Category symbolic representation | Code (decimal) | Code (hexadecimal) |
|----------------------------------|----------------|-------------------|
| **Good** | | **0x0...0000** |
| Good | 0 | 0x00000000 |
| GoodCallAgain | 11075584 | 0x00A90000 |
| GoodCascade | 67698688 | 0x04090000 |
| GoodCascadeInitializationAcknowledged | 67174400 | 0x04010000 |
| GoodCascadeInitializationRequest | 67239936 | 0x04020000 |
| GoodCascadeNotInvited | 67305472 | 0x04030000 |
| GoodCascadeNotSelected | 67371008 | 0x04040000 |
| GoodClamped | 3145728 | 0x00300000 |
| GoodCommunicationEvent | 10944512 | 0x00A70000 |
| GoodCompletesAsynchronously | 3014656 | 0x002E0000 |
| GoodDataIgnored | 14221312 | 0x00D90000 |
| GoodDependentValueChanged | 14680064 | 0x00E00000 |
| GoodEdited | 14417920 | 0x00DC0000 |
| GoodEdited_DependentValueChanged | 18219008 | 0x01160000 |
| GoodEdited_DominantValueChanged | 18284544 | 0x01170000 |
| GoodEdited_DominantValueChanged_DependentValueChanged | 18350080 | 0x01180000 |
| GoodEntryInserted | 10616832 | 0x00A20000 |
| GoodEntryReplaced | 10682368 | 0x00A30000 |
| GoodFaultStateActive | 67567616 | 0x04070000 |
| GoodInitiateFaultState | 67633152 | 0x04080000 |
| GoodLocalOverride | 9830400 | 0x00960000 |
| GoodMoreData | 10878976 | 0x00A60000 |
| GoodNoData | 10813440 | 0x00A50000 |
| GoodNonCriticalTimeout | 11141120 | 0x00AA0000 |
| GoodOverload | 3080192 | 0x002F0000 |
| GoodPostActionFailed | 14483456 | 0x00DD0000 |
| GoodResultsMayBeIncomplete | 12189696 | 0x00BA0000 |
| GoodRetransmissionQueueNotSupported | 14614528 | 0x00DF0000 |
| GoodShutdownEvent | 11010048 | 0x00A80000 |
| GoodSubscriptionTransferred | 2949120 | 0x002D0000 |
| **Uncertain** | | **0x4...0000** |
| Uncertain | 1073741824 | 0x40000000 |
| UncertainConfigurationError | 1108279296 | 0x420F0000 |
| UncertainDataSubNormal | 1084489728 | 0x40A40000 |
| UncertainDependentValueChanged | 1088552960 | 0x40E20000 |
| UncertainDominantValueChanged | 1088290816 | 0x40DE0000 |
| UncertainEngineeringUnitsExceeded | 1083441152 | 0x40940000 |
| UncertainInitialValue | 1083310080 | 0x40920000 |
| UncertainLastUsableValue | 1083179008 | 0x40900000 |
| UncertainNoCommunicationLastUsableValue | 1083113472 | 0x408F0000 |
| UncertainNotAllNodesAvailable | 1086324736 | 0x40C00000 |
| UncertainReferenceNotDeleted | 1086062592 | 0x40BC0000 |
| UncertainReferenceOutOfServer | 1080819712 | 0x406C0000 |
| UncertainSensorCalibration | 1107951616 | 0x420A0000 |
| UncertainSensorNotAccurate | 1083375616 | 0x40930000 |
| UncertainSimulatedValue | 1107886080 | 0x42090000 |
| UncertainSubNormal | 1083506688 | 0x40950000 |
| UncertainSubstituteValue | 1083244544 | 0x40910000 |
| UncertainTransducerInManual | 1107820544 | 0x42080000 |
| **Bad** | | **0x8...0000** |
| Bad | 2147483648 | 0x80000000 |
| BadAggregateConfigurationRejected | 2161770496 | 0x80DA0000 |
| BadAggregateInvalidInputs | 2161508352 | 0x80D60000 |
| BadAggregateListMismatch | 2161377280 | 0x80D40000 |
| BadAggregateNotSupported | 2161442816 | 0x80D50000 |
| BadAlreadyExists | 2165637120 | 0x81150000 |
| BadApplicationSignatureInvalid | 2153250816 | 0x80580000 |
| BadArgumentsMissing | 2155216896 | 0x80760000 |
| BadAttributeIdInvalid | 2150957056 | 0x80350000 |
| BadBoundNotFound | 2161573888 | 0x80D70000 |
| BadBoundNotSupported | 2161639424 | 0x80D80000 |
| BadBrowseDirectionInvalid | 2152529920 | 0x804D0000 |
| BadBrowseNameDuplicated | 2153840640 | 0x80610000 |
| BadBrowseNameInvalid | 2153775104 | 0x80600000 |
| BadCertificateChainIncomplete | 2165112832 | 0x810D0000 |
| BadCertificateHostNameInvalid | 2148925440 | 0x80160000 |
| BadCertificateInvalid | 2148663296 | 0x80120000 |
| BadCertificateIssuerRevocationUnknown | 2149318656 | 0x801C0000 |
| BadCertificateIssuerRevoked | 2149449728 | 0x801E0000 |
| BadCertificateIssuerTimeInvalid | 2148859904 | 0x80150000 |
| BadCertificateIssuerUseNotAllowed | 2149122048 | 0x80190000 |
| BadCertificatePolicyCheckFailed | 2165571584 | 0x81140000 |
| BadCertificateRevocationUnknown | 2149253120 | 0x801B0000 |
| BadCertificateRevoked | 2149384192 | 0x801D0000 |
| BadCertificateTimeInvalid | 2148794368 | 0x80140000 |
| BadCertificateUntrusted | 2149187584 | 0x801A0000 |
| BadCertificateUriInvalid | 2148990976 | 0x80170000 |
| BadCertificateUseNotAllowed | 2149056512 | 0x80180000 |
| BadCommunicationError | 2147811328 | 0x80050000 |
| BadConditionAlreadyDisabled | 2157445120 | 0x80980000 |
| BadConditionAlreadyEnabled | 2160852992 | 0x80CC0000 |
| BadConditionAlreadyShelved | 2161180672 | 0x80D10000 |
| BadConditionBranchAlreadyAcked | 2161049600 | 0x80CF0000 |
| BadConditionBranchAlreadyConfirmed | 2161115136 | 0x80D00000 |
| BadConditionDisabled | 2157510656 | 0x80990000 |
| BadConditionNotShelved | 2161246208 | 0x80D20000 |
| BadConfigurationError | 2156462080 | 0x80890000 |
| BadConnectionClosed | 2158886912 | 0x80AE0000 |
| BadConnectionRejected | 2158755840 | 0x80AC0000 |
| BadContentFilterInvalid | 2152202240 | 0x80480000 |
| BadContinuationPointInvalid | 2152333312 | 0x804A0000 |
| BadDataEncodingInvalid | 2151153664 | 0x80380000 |
| BadDataEncodingUnsupported | 2151219200 | 0x80390000 |
| BadDataLost | 2157772800 | 0x809D0000 |
| BadDataTypeIdUnknown | 2148597760 | 0x80110000 |
| BadDataUnavailable | 2157838336 | 0x809E0000 |
| BadDeadbandFilterInvalid | 2156789760 | 0x808E0000 |
| BadDecodingError | 2147942400 | 0x80070000 |
| BadDependentValueChanged | 2162360320 | 0x80E30000 |
| BadDeviceFailure | 2156593152 | 0x808B0000 |
| BadDialogNotActive | 2160918528 | 0x80CD0000 |
| BadDialogResponseInvalid | 2160984064 | 0x80CE0000 |
| BadDisconnect | 2158821376 | 0x80AD0000 |
| BadDiscoveryUrlMissing | 2152792064 | 0x80510000 |
| BadDominantValueChanged | 2162229248 | 0x80E10000 |
| BadDuplicateReferenceNotAllowed | 2154168320 | 0x80660000 |
| BadEdited_OutOfRange | 2165899264 | 0x81190000 |
| BadEdited_OutOfRange_DominantValueChanged | 2166095872 | 0x811C0000 |
| BadEdited_OutOfRange_DominantValueChanged_DependentValueChanged | 2166226944 | 0x811E0000 |
| BadEncodingError | 2147876864 | 0x80060000 |
| BadEncodingLimitsExceeded | 2148007936 | 0x80080000 |
| BadEndOfStream | 2159017984 | 0x80B00000 |
| BadEntryExists | 2157903872 | 0x809F0000 |
| BadEventFilterInvalid | 2152136704 | 0x80470000 |
| BadEventIdUnknown | 2157576192 | 0x809A0000 |
| BadEventNotAcknowledgeable | 2159738880 | 0x80BB0000 |
| BadExpectedStreamToBlock | 2159280128 | 0x80B40000 |
| BadFilterElementInvalid | 2160328704 | 0x80C40000 |
| BadFilterLiteralInvalid | 2160394240 | 0x80C50000 |
| BadFilterNotAllowed | 2152005632 | 0x80450000 |
| BadFilterOperandCountMismatch | 2160263168 | 0x80C30000 |
| BadFilterOperandInvalid | 2152267776 | 0x80490000 |
| BadFilterOperatorInvalid | 2160132096 | 0x80C10000 |
| BadFilterOperatorUnsupported | 2160197632 | 0x80C20000 |
| BadHistoryOperationInvalid | 2154889216 | 0x80710000 |
| BadHistoryOperationUnsupported | 2154954752 | 0x80720000 |
| BadIdentityChangeNotSupported | 2160459776 | 0x80C60000 |
| BadIdentityTokenInvalid | 2149580800 | 0x80200000 |
| BadIdentityTokenRejected | 2149646336 | 0x80210000 |
| BadIndexRangeInvalid | 2151022592 | 0x80360000 |
| BadIndexRangeNoData | 2151088128 | 0x80370000 |
| BadInitialValue_OutOfRange | 2165964800 | 0x811A0000 |
| BadInsufficientClientProfile | 2155610112 | 0x807C0000 |
| BadInternalError | 2147614720 | 0x80020000 |
| BadInvalidArgument | 2158690304 | 0x80AB0000 |
| BadInvalidSelfReference | 2154233856 | 0x80670000 |
| BadInvalidState | 2158952448 | 0x80AF0000 |
| BadInvalidTimestamp | 2149777408 | 0x80230000 |
| BadInvalidTimestampArgument | 2159869952 | 0x80BD0000 |
| BadLicenseExpired | 2165178368 | 0x810E0000 |
| BadLicenseLimitsExceeded | 2165243904 | 0x810F0000 |
| BadLicenseNotAvailable | 2165309440 | 0x81100000 |
| BadMaxAgeInvalid | 2154823680 | 0x80700000 |
| BadMaxConnectionsReached | 2159476736 | 0x80B70000 |
| BadMessageNotAvailable | 2155544576 | 0x807B0000 |
| BadMethodInvalid | 2155151360 | 0x80750000 |
| BadMonitoredItemFilterInvalid | 2151874560 | 0x80430000 |
| BadMonitoredItemFilterUnsupported | 2151940096 | 0x80440000 |
| BadMonitoredItemIdInvalid | 2151809024 | 0x80420000 |
| BadMonitoringModeInvalid | 2151743488 | 0x80410000 |
| BadNoCommunication | 2150694912 | 0x80310000 |
| BadNoContinuationPoints | 2152398848 | 0x804B0000 |
| BadNoData | 2157641728 | 0x809B0000 |
| BadNoDataAvailable | 2159083520 | 0x80B10000 |
| BadNoDeleteRights | 2154364928 | 0x80690000 |
| BadNoEntryExists | 2157969408 | 0x80A00000 |
| BadNoMatch | 2154758144 | 0x806F0000 |
| BadNoSubscription | 2155413504 | 0x80790000 |
| BadNoValidCertificates | 2153316352 | 0x80590000 |
| BadNodeAttributesInvalid | 2153906176 | 0x80620000 |
| BadNodeClassInvalid | 2153709568 | 0x805F0000 |
| BadNodeIdExists | 2153644032 | 0x805E0000 |
| BadNodeIdInvalid | 2150825984 | 0x80330000 |
| BadNodeIdRejected | 2153578496 | 0x805D0000 |
| BadNodeIdUnknown | 2150891520 | 0x80340000 |
| BadNodeNotInView | 2152595456 | 0x804E0000 |
| BadNonceInvalid | 2149842944 | 0x80240000 |
| BadNotConnected | 2156527616 | 0x808A0000 |
| BadNotExecutable | 2165374976 | 0x81110000 |
| BadNotFound | 2151546880 | 0x803E0000 |
| BadNotImplemented | 2151677952 | 0x80400000 |
| BadNotReadable | 2151284736 | 0x803A0000 |
| BadNotSupported | 2151481344 | 0x803D0000 |
| BadNotTypeDefinition | 2160590848 | 0x80C80000 |
| BadNotWritable | 2151350272 | 0x803B0000 |
| BadNothingToDo | 2148466688 | 0x800F0000 |
| BadNumericOverflow | 2165440512 | 0x81120000 |
| BadObjectDeleted | 2151612416 | 0x803F0000 |
| BadOperationAbandoned | 2159214592 | 0x80B30000 |
| BadOutOfMemory | 2147680256 | 0x80030000 |
| BadOutOfRange | 2151415808 | 0x803C0000 |
| BadOutOfRange_DominantValueChanged | 2166030336 | 0x811B0000 |
| BadOutOfRange_DominantValueChanged_DependentValueChanged | 2166161408 | 0x811D0000 |
| BadOutOfService | 2156724224 | 0x808D0000 |
| BadParentNodeIdInvalid | 2153447424 | 0x805B0000 |
| BadProtocolVersionUnsupported | 2159935488 | 0x80BE0000 |
| BadQueryTooComplex | 2154692608 | 0x806E0000 |
| BadReferenceLocalOnly | 2154299392 | 0x80680000 |
| BadReferenceNotAllowed | 2153512960 | 0x805C0000 |
| BadReferenceTypeIdInvalid | 2152464384 | 0x804C0000 |
| BadRefreshInProgress | 2157379584 | 0x80970000 |
| BadRequestCancelledByClient | 2150367232 | 0x802C0000 |
| BadRequestCancelledByRequest | 2153381888 | 0x805A0000 |
| BadRequestHeaderInvalid | 2150236160 | 0x802A0000 |
| BadRequestInterrupted | 2156134400 | 0x80840000 |
| BadRequestNotAllowed | 2162425856 | 0x80E40000 |
| BadRequestNotComplete | 2165506048 | 0x81130000 |
| BadRequestTimeout | 2156199936 | 0x80850000 |
| BadRequestTooLarge | 2159542272 | 0x80B80000 |
| BadRequestTypeInvalid | 2152923136 | 0x80530000 |
| BadResourceUnavailable | 2147745792 | 0x80040000 |
| BadResponseTooLarge | 2159607808 | 0x80B90000 |
| BadSecureChannelClosed | 2156265472 | 0x80860000 |
| BadSecureChannelIdInvalid | 2149711872 | 0x80220000 |
| BadSecureChannelTokenUnknown | 2156331008 | 0x80870000 |
| BadSecurityChecksFailed | 2148728832 | 0x80130000 |
| BadSecurityModeInsufficient | 2162556928 | 0x80E60000 |
| BadSecurityModeRejected | 2152988672 | 0x80540000 |
| BadSecurityPolicyRejected | 2153054208 | 0x80550000 |
| BadSempahoreFileMissing | 2152857600 | 0x80520000 |
| BadSensorFailure | 2156658688 | 0x808C0000 |
| BadSequenceNumberInvalid | 2156396544 | 0x80880000 |
| BadSequenceNumberUnknown | 2155479040 | 0x807A0000 |
| BadServerHalted | 2148401152 | 0x800E0000 |
| BadServerIndexInvalid | 2154430464 | 0x806A0000 |
| BadServerNameMissing | 2152726528 | 0x80500000 |
| BadServerNotConnected | 2148335616 | 0x800D0000 |
| BadServerUriInvalid | 2152660992 | 0x804F0000 |
| BadServiceUnsupported | 2148204544 | 0x800B0000 |
| BadSessionClosed | 2149974016 | 0x80260000 |
| BadSessionIdInvalid | 2149908480 | 0x80250000 |
| BadSessionNotActivated | 2150039552 | 0x80270000 |
| BadShelvingTimeOutOfRange | 2161311744 | 0x80D30000 |
| BadShutdown | 2148270080 | 0x800C0000 |
| BadSourceNodeIdInvalid | 2154037248 | 0x80640000 |
| BadStateNotActive | 2160001024 | 0x80BF0000 |
| BadStructureMissing | 2152071168 | 0x80460000 |
| BadSubscriptionIdInvalid | 2150105088 | 0x80280000 |
| BadSyntaxError | 2159411200 | 0x80B60000 |
| BadTargetNodeIdInvalid | 2154102784 | 0x80650000 |
| BadTcpEndpointUrlInvalid | 2156068864 | 0x80830000 |
| BadTcpInternalError | 2156003328 | 0x80820000 |
| BadTcpMessageTooLarge | 2155872256 | 0x80800000 |
| BadTcpMessageTypeInvalid | 2155741184 | 0x807E0000 |
| BadTcpNotEnoughResources | 2155937792 | 0x80810000 |
| BadTcpSecureChannelUnknown | 2155806720 | 0x807F0000 |
| BadTcpServerTooBusy | 2155675648 | 0x807D0000 |
| BadTicketInvalid | 2166358016 | 0x81200000 |
| BadTicketRequired | 2166292480 | 0x811F0000 |
| BadTimeout | 2148139008 | 0x800A0000 |
| BadTimestampNotSupported | 2158034944 | 0x80A10000 |
| BadTimestampsToReturnInvalid | 2150301696 | 0x802B0000 |
| BadTooManyArguments | 2162491392 | 0x80E50000 |
| BadTooManyMatches | 2154627072 | 0x806D0000 |
| BadTooManyMonitoredItems | 2161836032 | 0x80DB0000 |
| BadTooManyOperations | 2148532224 | 0x80100000 |
| BadTooManyPublishRequests | 2155347968 | 0x80780000 |
| BadTooManySessions | 2153119744 | 0x80560000 |
| BadTooManySubscriptions | 2155282432 | 0x80770000 |
| BadTypeDefinitionInvalid | 2153971712 | 0x80630000 |
| BadTypeMismatch | 2155085824 | 0x80740000 |
| BadUnexpectedError | 2147549184 | 0x80010000 |
| BadUnknownResponse | 2148073472 | 0x80090000 |
| BadUserAccessDenied | 2149515264 | 0x801F0000 |
| BadUserSignatureInvalid | 2153185280 | 0x80570000 |
| BadViewIdUnknown | 2154496000 | 0x806B0000 |
| BadViewParameterMismatch | 2160721920 | 0x80CA0000 |
| BadViewTimestampInvalid | 2160656384 | 0x80C90000 |
| BadViewVersionInvalid | 2160787456 | 0x80CB0000 |
| BadWaitingForInitialData | 2150760448 | 0x80320000 |
| BadWaitingForResponse | 2159149056 | 0x80B20000 |
| BadWouldBlock | 2159345664 | 0x80B50000 |
| BadWriteNotSupported | 2155020288 | 0x80730000 |
</Accordion>

## Modifier flags
<Accordion title="Click to see full list of modifiers">
| Modifier symbolic representation | Code (decimal) | Code (hexadecimal) |
|----------------------------------|----------------|---------------------|
| , StructureChanged | 32768 | 0x8000 |
| , SemanticsChanged | 16384 | 0x4000 |
| Add the following number if and only if any of the below *info flags* are added | | |
| | 1024 | 0x0400 |
| Only one of the following 3 limit flags may be set. | | |
| , Constant | 768 | 0x0300 |
| , High | 512 | 0x0200 |
| , Low | 256 | 0x0100 |
| Other info flags: | | |
| , Overflow | 128 | 0x0080 |
| , MultipleValues | 16 | 0x0010 |
| , ExtraData | 8 | 0x0008 |
| , Partial | 4 | 0x0004 |
| Only one of interpolated and calculated can be set. | | |
| , Interpolated | 2 | 0x0002 |
| , Calculated | 1 | 0x0001 |
</Accordion>

## API Examples

### Ingestion

The following request ingest has five data points: three good, one uncertain, and one bad:

```json wrap
POST /api/v1/projects/{project}/timeseries/data
Content-Type: application/json

{
    "items": [
        {
            "externalId": "outside-temperature",
            "datapoints": [
                {
                    "timestamp": 1620000000000,
                    "value": 1
                },
                {
                    "timestamp": 1620000000001,
                    "value": 2,
                    "status": {
                        "code": 0
                    }
                },
                {
                    "timestamp": 1620000000002,
                    "value": 3,
                    "status": {
                        "symbol": "GoodClamped"
                    }
                },
                {
                    "timestamp": 1620000000003,
                    "value": 4,
                    "status": {
                        "symbol": "Uncertain"
                    }
                },
                {
                    "timestamp": 1620000000004,
                    "value": 5,
                    "status": {
                        "code": 2153809152
                    }
                }
            ]
        }
    ]
}
```

### Retrieving raw data points

To be able to see the status codes on the data points we retrieve, we must set the `includeStatus` parameter to true.

```json wrap
POST /api/v1/projects/{project}/timeseries/data/list
Content-Type: application/json

{
    "items": [
        {
            "externalId": "outside-temperature",
            "includeStatus": true,
        }
    ]
}
```

With the example data from above, the response looks like this:

```json wrap
{
    "items": [
        {
            // ...
            "datapoints": [
                {
                    "timestamp": 1620000000000,
                    "value": 1.0
                },
                {
                    "timestamp": 1620000000001,
                    "value": 2.0
                },
                {
                    "timestamp": 1620000000002,
                    "value": 3.0,
                    "status": {
                        "code": 3145728,
                        "symbol": "GoodClamped"
                    }
                }
            ]
        }
    ]
}
```

Notice that we don't get the status code for the first two data points because they are exactly `Good` with `code = 0`.
They are omitted as a performance optimization since most status codes are 0.
For the last data point, we get both the code and the symbolic string representation.

#### List all data points

To list all data points, we can set the `ignoreBadDataPoints` parameter to `false`.
Because the API treats _uncertain_ data points as _bad_ by default, this parameter includes both _uncertain_ and _bad_ data points.

```json wrap
POST /api/v1/projects/{project}/timeseries/data/list
Content-Type: application/json

{
    "items": [
        {
            "externalId": "outside-temperature",
            "includeStatus": true,
            "ignoreBadDataPoints": false,
        }
    ]
}
```

Using the example data from above, the response looks like this:

```json wrap
{
    "items": [
        {
            // ...
            "datapoints": [
                {
                    "timestamp": 1620000000000,
                    "value": 1.0
                },
                {
                    "timestamp": 1620000000001,
                    "value": 2.0
                },
                {
                    "timestamp": 1620000000002,
                    "value": 3.0,
                    "status": {
                        "code": 3145728,
                        "symbol": "GoodClamped"
                    }
                },
                {
                    "timestamp": 1620000000003,
                    "value": 4.0,
                    "status": {
                        "code": 1073741824,
                        "symbol": "Uncertain"
                    }
                },
                {
                    "timestamp": 1620000000004,
                    "value": 5.0,
                    "status": {
                        "code": 2153809152,
                        "symbol": "BadBrowseNameInvalid, StructureChanged, Low"
                    }
                }
            ]
        }
    ]
}
```

### Retrieving aggregates

The example below requests seven aggregates.
The first aggregate is the number of data points included that are used in the calculations.
The next three are simple counts of the number of data points with each status code present.
The last three are the average, min, and max for only the good data points.

```json wrap
POST /api/v1/projects/{project}/timeseries/data/list
Content-Type: application/json

{
    "items": [
        {
            "externalId": "outside-temperature",
            "granularity": "1s",
            "aggregates": [
                "count",
                "countGood", "countUncertain", "countBad",
                "average", "min", "max"
            ]
        }
    ]
}
```

Using the example data from above, the response looks like this:

```json
{
    "items": [
        {
            // ...
            "datapoints": [
                {
                    "timestamp": 1620000000000,
                    "count": 3,
                    "countGood": 3,
                    "countUncertain": 1,
                    "countBad": 1,
                    "average": 2.0,
                    "min": 1.0,
                    "max": 3.0
                }
            ]
        }
    ]
}
```

<Note>
The example doesn't include parameters to take uncertain and bad datapoints into account.
Therefore, the count, average, min, and max calculations are performed ignoring uncertain and bad data points.
</Note>

#### Treating uncertain data points as good

To also include the uncertain data points as if they were good, you can set the `treateUncertainAsBad` parameter to `false`.
This parameter is also available for raw data points.

```json
POST /api/v1/projects/{project}/timeseries/data/list
Content-Type: application/json

{
    "items": [
        {
            "externalId": "outside-temperature",
            "granularity": "1s",
            "treatUncertainAsBad": false,
            "aggregates": [
                "count",
                "countGood", "countUncertain", "countBad",
                "average", "min", "max"
            ]
        }
    ]
}
```

Using the example data from above, the response looks like this:

```json wrap
{
    "items": [
        {
            // ...
            "datapoints": [
                {
                    "timestamp": 1620000000000,
                    "count": 4,
                    "countGood": 3,
                    "countUncertain": 1,
                    "countBad": 1,
                    "average": 2.5,
                    "min": 1.0,
                    "max": 4.0
                }
            ]
        }
    ]
}
```

Treating the _uncertain_ data point as _good_ increases the total count to four.
Notice that also the `count`, `average`, `min`, and `max` values have changed because of the additional good data point in the calculation.

## Aggregate behavior

There are three boolean parameters that deterimine how aggregates are calculated:
`isStep`, `treatUncertainAsBad` and `ignoreBadDataPoints`.
Together, they result in 8 different combinations that gives different aggregate behavior.

- If `treatUncertainAsBad` is true, uncertain data points are treated as being bad (the default).
- If `ignoreBadDataPoints` is true, data points that are treated as bad will be ignored (the default).
  If this parameter is set to false, interpolation will stop at the beginning of each bad period.
- if the time series is configured with `isStep` set to true, then there will be no interpolation between data points.
   The last value is always _carried forward_ until the next data point.

The image below illustrates how we interpolate between data points with different status codes in each of the 8 combinations.

<Frame>
<img class="illustration" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/aggregation/drawing_with_status_codes.png" alt="Interpolation between data points with different status codes" />
</Frame>



<!-- SOURCE_END: docs.cognite.com/docs\dev\concepts\reference\status_codes.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\concepts\reference\ts_plotting_charts.mdx -->
## File: docs.cognite.com/docs\dev\concepts\reference\ts_plotting_charts.mdx

---
title: 'Best practices for plotting charts with data points'
description: 'Learn how to use the Time Series API to render precise, high fidelity charts with proper aggregation and data point handling.'
content-type: 'reference'
audience: [developer, dataScientist, dataEngineer]
experience-level: 200
article-type: 'article'
lifecycle: use
---
This best practice guide focuses on giving developers the ability to use the powerful and flexible Time Series API to render the most precise, high fidelity charts possible.  Whilst the guide focuses on high fidelity charts, the techniques outlined herein may be adapted to suit different use cases where lower granularity/ lower fidelity is preferred (such as weekly or monthly reports).

## Plot Area Resolution

It is important to know the resolution of the plot area, in order to be able to determine the maximum data fidelity it is possible to draw in the plot area.  If a single pixel represents a single data point, then the horizontal resolution of the plot area represents the maximum number of data points it is possible to plot.  This is true of both raw data points, and aggregations.  When drawing a plot where the number of data points approaches the number of pixels available, we can call this the highest data fidelity approach to plotting. 

Starting with the highest possible fidelity chart we can draw.  Let’s assume we have a 1080p monitor with a horizontal resolution of 1920 pixels.  Let’s assume that our chart plot area is 1500 pixels wide, leaving some space either side for other GUI elements.  If we fully ‘zoom in’ to the data, we can plot 1500 raw data points on the screen at any one time.

However, when we ‘zoom out’ and view the data over a longer period of time, we will begin to have more data points in the desired plot than there are pixels to plot on.  In this situation, aggregation is required to happen in order to fit the data on the screen.

Aggregation can happen in several ways.  If the chart retrieves more raw data points than there are pixels, aggregation happens within the graphical layer, where the graphics driver of the machine rendering the plot will aggregate / interpolate based on the algorithms built into the graphics driver.  Whilst this form of client-side aggregation may render a reasonable approximation of the chart we wish to plot, we lose control over how we want the plot to behave to the graphics driver, which in many data science scenarios may result in a loss of precision we wish to avoid. This approach also risks slowing down client applications, due the client first having to download an excessive number of raw data points, and then due to excessive RAM utilisation (holding all the raw data points in memory).

A better way to render aggregated data points in the client application, is to use the aggregate function within the Time Series API.  The aggregate function allows the user to more precisely define the size of the aggregate buckets we wish to plot (i.e. the number of data points in each aggregation bucket).  Using the Time Series API aggregate function also improves client system performance, as the user retrieves only the number of aggregated data points required to plot on the screen and no more (consuming far less network bandwidth, RAM, and other system resources).

In summary, when the number of data points we need to plot exceeds the number of pixels available in the plot area, the Time Series API aggregate function should be used.  

## Average, Min, Max and Standard Variance

When plotting charts where the data is aggregated, a natural first choice is to calculate the average value of the data points for each bucket.  However, if there are outlier values in a given bucket, using only the average calculated values will lose potentially valuable information.  Therefore when plotting data using aggregate buckets, consider whether to also include plotting of min and max values along with the average to provide the fuller picture.

Further insight may be gained by including the variance aggregate values.  For example, by calculating the square root of the **continuousVariance** value for a given aggregation period, it will tell you the standard deviation.  This could be plotted on a chart to indicate to the viewer, the proportion of outliers from the mean there are in that time period.

<Note>
For the most common cases when analysing time series data, **continuousVariance** is the most appropriate aggregate.  For time series with a fixed frequency, **discreteVariance** may be used.
</Note>

When maximising the use of the screen real estate to provide the highest possible data fidelity (i.e. plotting aggregated data points per pixel), using the format illustrated in the chart below may be desirable.

<Frame>
<img class="illustration" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/resource_types/high_fidelity_datapoints_avg_min_max.png" alt="High fidelity plot with average, min and max values " width="100%"/>
</Frame>

With less granular plots, with fewer aggregate buckets along the X-axis, the scatter plot format may be more appropriate such as depicted below:

<Frame>
<img class="illustration" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/resource_types/LoFi_daily_avg_min_max.png" alt="Daily granularity plot with average, min and max values " width="100%"/>
</Frame>

## How to choose aggregate bucket size

Let’s use a worked example to illustrate how the Time Series API may be used to select the most appropriate aggregate bucket size to render the most precise view of the data.  This example uses the average aggregation. The example is also valid for use of Min and Max aggregate types.  

<Note>
This example does not suggest that plotting the highest fidelity graph is best practice in all cases.  The mode of visualisation should be chosen appropriate to the customer use case.  In some scenarios, a lower granularity, scatter type plot may be more useful than the high granularity example illustrated here. 
</Note>

Example 1:
* A sensor produces temperature readings at a frequency of 1hz, or 60 data points per minute.
* We wish to plot the reading data on a chart, with a horizontal resolution of 1500 px.
* The initial timespan of the plot will be 30 days.

In this scenario, there will be 2592000 raw data points to plot on the graph, but with only 1500 pixels to plot against, we will need to put these data points into aggregated buckets in order to render them.

As the X-axis will represent time in days, each pixel can represent 28.8 minutes (total number of minutes in plot - 43200 / 1500).  It seems sensible to round each aggregate bucket up to 30 minutes in this case. Rounding up ensures that the number of aggregate buckets is less than the number of pixels available in the plot area.

With each pixel therefore containing a 30 minute aggregation, there are 1440, 30 minute buckets in 30 days, therefore we can plot our data within our plot area.

The Time Series API supports time granularities expressed in Days, Hours, Minutes or Seconds.  So in our example here, a sample API call might look like this:

```json wrap
{
  "items": [
    {
      "start": 2023-04-01T00:00:00,
      "end": 2023-04-30T23:59:59,
      "limit": 0,
      "aggregates": [
        "average"
      ],
      "granularity": "30m",
      "targetUnit": "temperature:deg_f",
      "targetUnitSystem": "imperial",
      "includeOutsidePoints": false,
      "cursor": "string",
      "id": 1
    }
  ],
  "start": 0,
  "end": 0,
  "limit": 100,
  "aggregates": [
    "average"
  ],
  "granularity": "30m",
  "includeOutsidePoints": false,
  "ignoreUnknownIds": false
}
```

### Changing the plot time span

Now let’s say we want to zoom out, and look at a calendar quarter of data.  From 1st April to June 30th.
This quarter will contain 91 days, which in turn is 131040 minutes.

Dividing the amount of minutes into the number of pixels available to plot gives us an aggregate bucket size of 87.36 minutes per bucket.  Let us round this up to 90 mins.  This will result in 1456 aggregate buckets in the plot area, so just within our pixel count.

### Changes in plot resolution

In modern web based applications, it is likely that the browser window will be resized to fit different screen sizes and resolutions.  Display horizontal resolutions may range from 1024px in the case of older industrial laptops through to 7680px for modern 8K resolution screens.  In video wall applications, resolutions may be even greater (e.g. three 4K monitors side by side provides a total of 11520px). In addition, users may dynamically resize the browser window.  In such cases, the plot area will also change in resolution, and thus it becomes useful to calculate the aggregate bucket sizes where the plot area pixel count is a variable.

# Summary
Calculate the appropriate aggregate bucket size, taking into account the following variables

* The frequency of the time series data points
* The desired time span to visualise the data over
* The horizontal resolution available in the plot area
* The use case requirements for high vs. low fidelity (managing information overload)

Round up aggregate bucket size to the nearest ‘sensible’ number, ensuring the data will fit within the plot area.

<!-- SOURCE_END: docs.cognite.com/docs\dev\concepts\reference\ts_plotting_charts.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\concepts\resource_filtering_dsl\index.mdx -->
## File: docs.cognite.com/docs\dev\concepts\resource_filtering_dsl\index.mdx

---
title: 'Filtering'
description: 'Learn how to use boolean logic and complex queries to filter CDF resources using AND, OR, NOT operators and attribute filters.'
content-type: 'concept'
audience: [developer, dataEngineer]
experience-level: 200
article-type: 'article'
toc_max_heading_level: 4
lifecycle: use
---
Cognite Data Fusion (CDF) supports boolean logic using _AND_, _OR_, and _NOT_ to build complex queries. You can also combine resource item attribute filters, such as _equals_, _prefix_, and _range_.

The default set of CDF filters lets you filter all CDF resources in these contexts:

- **CDF resources**, for example, assets and events, by primary fields, metadata keys, and values.

- **Full-text search results**. Use filters to narrow down the returned search results. For example, you can use a filter to return only "PDFs from last week" from your full-text search query.

- **Aggregate results**. Use filters to reduce the number of returned aggregate buckets. For example, you can use a filter to return "metadata key names and their count for all keys with the prefix character '\_'.

## Serialization/deserialization

You can express filters as JSON. The root must always be an object.

## Properties

CDF uses the same grammar to express a reference everywhere the API uses a resource attribute: a JSON array of strings to express the attribute/property. This is true for _leaf filters_, _aggregate_, _sort_, _etc_.

There are no naming constraints for the allowed property names. CDF uses "property" for **leaf filters** to refer to the field(s) the filter operates on.

### Example: nesting and sorting

In the example object below, you can refer to the nested values as `["metadata", "../type"]`. Or, you can use another nested property, `["metadata", "type"]`, and then address the un-nested property as `["type"]`.

```json
{
  "id": 1234,
  "metadata": {
    "../type": "critical",
    "type": "high"
  },
  "type": "alert"
}
```

To sort the `/list` result on _metadata.type_ and _type_ properties, send the following request:

```json
{
  "sort": [
    {
      "property": ["metadata", "type"],
      "order": "asc",
      "nulls": "last"
    },
    {
      "property": ["type"],
      "order": "desc"
    }
  ]
}
```

<Note>
You can sort by several properties in the same query.
</Note>

### Example: filtering

This example defines a filter using JSON:

```json JSON
{
  "or": [
    {
      "not": {
        "and": [
          {
            "equals": {
              "property": ["type"],
              "value": "alert"
            }
          },
          {
            "in": {
              "property": ["subtype"],
              "values": ["critical", "warning"]
            }
          },
          {
            "range": {
              "property": ["rating"],
              "gte": 5,
              "lt": 10
            }
          }
        ]
      }
    },
    {
      "and": [
        {
          "equals": {
            "property": ["metadata", "priority"],
            "value": "highest"
          }
        },
        {
          "equals": {
            "property": ["subtype"],
            "value": "critical"
          }
        }
      ]
    }
  ]
}
```

A simplified, less verbose version could look like this:

```
(
    ("metadata.priority"=highest AND subtype=critical)
    OR
    (
        NOT (type=alert AND subtype IN (critical, warning) AND 5 <= rating < 10)
    )
)
```

Even if the JSON object is more verbose, the object is easy to express and handle programmatically. JSON is also **unambiguous** and doesn't impose rules on naming attributes/properties. You also can't mistake a property name for a keyword in JSON.

Below is another example of using JSON to express a simple intent: "_filter items where the attribute `source` is equal to the value `git`_."

```json
{
  "equals": {
    "property": ["source"],
    "value": "git"
  }
}
```

## Filter language specification

| Filter      | Syntax                                                                                                    |
| ----------- | --------------------------------------------------------------------------------------------------------- |
| filter       | `<bool_filter> \| <leaf_filter>`                                                                            |
| bool_filter  | `<and> \| <or> \| <not>`                                                                                  |
| and         | `{"and": [<filter>, â€¦]}`                                                                                   |
| or          | `{"or": [<filter>, â€¦]}`                                                                                    |
| not         | `{"not": <filter>}`                                                                                        |
| property    | `<list_of_strings>`                                                                                       |
| leaf_filter  | `<equals> \| <in> \| <range> \| <prefix> \| <exists> \| <containsAny> \| <containsAll>`                    |
| equals      | `{"equals": {"property": <property>, "value": <string_value>}}`                                           |
| in          | `{"in": {"property": <property>, "values": <list_value>}}`                                                |
| range       | `{"range": {"property": <property>, "(gte\|gt\|lte\|lt)": <value1> [, "(gte\|gt\|lte\|lt)": <value\2>]}}` |
| prefix       | `{"prefix": {"property": <property>, "value": <string_value>}}`                                            |
| exists      | `{"exists": {"property": <property>}}`                                                                    |
| containsAny | `{"containsAny": {"property": <property>, "values": <list_value>}}`                                       |
| containsAll | `{"containsAll": {"property": <property>, "values": <list_value>}}`                                       |

<Info>
Different CDF resources may support a different subset of the existing leaf filters. Refer to the documentation for the specific CDF resource for details.
</Info>

## Boolean filters

### AND

To express boolean **AND** logic, use the `and` filter expressed in JSON:

`{"and": [ filter1, filter2, â€¦, filterN]}`

### OR

To express boolean **OR** logic, use the `or` filter (expressed in JSON):

`{"or": [ filter1, filter2, â€¦, filterN]}`

### NOT

To express the negated logic of a boolean value, use the `not` filter (expressed in JSON):

`{"not": filter}`

## Leaf filters

<Note>
The supported leaf filters may differ depending on the CDF resource.
</Note>

### Equals

Filter items where the _property_ (named value) equals the specified value. This filter works for properties with values that aren't arrays/lists.

Filter items where the _property_ (name) equals the given name and has a specific value. This filter works for properties with values that aren't arrays/lists.

```json
{
  "equals": {
    "property": ["rootId"],
    "value": 12432432423
  }
}
```

### In

Filter items where the _property_ value equals one of the values specified in the list. This filter expects the value stored by the property to be a single value (not a list).

```json
{
  "in": {
    "property": ["datasetId"],
    "values": [1, 2, 3]
  }
}
```

### Range

Filter items where the _property_ value is in the range specified by the filter boundaries: _gt_|_gte_,|_lt_|_lte_.

```json
{
  "range": {
    "property": ["count"],
    "gte": 1,
    "lte": 2
  }
}
```

To compare the property, you must specify _gt_, _lt_, _lte_, or _gte_, and at least one boundary value.

Range behavior depends on the type of the property you are comparing with. The filter language does not require that typing must exist. If you store all the values as strings, the range filter will behave as if comparing string values.

This filter works for properties not typed as arrays/lists.

<Note>
Some CDF resources don't support open-range filters. To avoid errors, specify both upper and lower boundaries. See the API documentation for details.
</Note>

### Prefix

Filter items where the _property_ value starts with the specified string. This filter works for properties that are not of type array/list.

```json
{
  "prefix": {
    "property": ["metadata", "vendor"],
    "value": "si"
  }
}
```

### Exists

Filter items where the _property_ value is something other than `null` (empty).
This filter works for properties of any type.

```json
{
  "exists": {
    "property": ["externalId"]
  }
}
```

### ContainsAny

Filter items where the _property_ values equal one or more of the specified values from the filter.
This filter works for properties that are of type array/list.

```json
{
  "containsAny": {
    "property": ["labels"],
    "values": ["pump", "tube"]
  }
}
```

### ContainsAll

Filter items where the values from the _property_ match all specified values.
This filter works for properties that are of type array/list.

```json
{
  "containsAll": {
    "property": ["assetIds"],
    "values": [23234324, 2423423]
  }
}
```

## Advanced Query filters

Advanced queries provide more robust data discovery and retrieval tools using `Search`, `Filter`, and `Aggregation` methods. Advanced search and filter API supports complex queries that combine simple operations, such as `equals`, `prefix`, `exists`, etc., using boolean operators `and`, `or`, and `not`. These methods apply to standard fields as well as metadata.

In the following section, we will outline the concepts and applications of different types of query filters with their API specifications for each supported service.

Since the filter operations leverage the CDF search architecture, they are consistent, expensive, and slower than CRUD operations. Consult with a professional on resource allocation and consumption.

### /filter

Use `/filter` for human and analytical queries, not bulk read or system-to-system sync operations.

### /list

Since `/list` provides a subset of the functionality available in `/filter`, use the `/filter` endpoint rather than `/list`.

For more information, see [List](https://api-docs.cognite.com/20230101/tag/Assets/operation/getAssets).

### /search

`/search` returns results sorted by relevance score. Relevance score is automatically calculated by the CDF search architecture using a combination of text matching and scoring algorithms to determine the relevance of search results. The text-matching algorithm checks if the search query terms appear in the item's fields, such as title, description, or content.

<Note>
Searches can return high numbers of results.
</Note>

<Tip>
The `/filter` operation supersedes the`/search` functionality since it provides a fuzzy search operation within itself. Use the `/filter` operation for these types of queries.
</Tip>

For more information on how search works for Docs API, see [Search for documents](https://api-docs.cognite.com/20230101/tag/Documents/operation/documentsSearch).

### /aggregate

Advanced aggregation APIs provide more powerful tools for data discovery and enhance the creation of better user interfaces and experiences. The supported aggregation types include the following:

| **Aggregation Type**   |      **Description**     |
|----------|:-------------|
| count |  Returns the count of items in the data set (i.e., number of Assets, Events, Time Series, etc.) |
| count (with properties) |  Returns the number of items in the data set with associated properties. It is useful if items in the data set have no associated properties.  |
| cardinalityValues |  Returns the number of instances that a property occurs in the data set |
| cardinalityProperties |  Returns the number of unique properties per metadata key that occur in the data set |
| uniqueValues |  Returns an array of the unique instances of metadata values per metadata key and the associated count of their occurrence |
| uniqueProperties |  Returns the number of unique properties that occur in the data set |

For more information, see [Aggregations in CDF](/dev/concepts/aggregation).


<!-- SOURCE_END: docs.cognite.com/docs\dev\concepts\resource_filtering_dsl\index.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\concepts\resource_throttling.mdx -->
## File: docs.cognite.com/docs\dev\concepts\resource_throttling.mdx

---
title: 'Request throttling'
description: 'Learn about rate limiting, concurrency limiting, and conflicting requests in CDF and how to handle HTTP 429 responses.'
content-type: 'concept'
audience: [developer, dataEngineer]
experience-level: 100
article-type: 'article'
lifecycle: use
---
Cognite Data Fusion (CDF) returns an HTTP `429 Too many requests` response status code in the following cases:

- A user sends too many requests in a given amount of time ([rate limiting](#rate-limiting)).
- A user sends too many concurrent requests ([concurrency limiting](#concurrency-limiting)).
- A user issues conflicting requests concurrently ([conflicting requests](#conflicting-requests)).
  Each of these cases requires different solving measures. The response body explains the cause of a particular 429 response.

All requests contribute to throttling equally regardless of their source
(extractors, functions, transformation, UI, SDK, etc).

<Warning>
When you [register applications](/cdf/access/entra/guides/configure_apps_oidc), do **NOT** share client IDs and secrets across multiple applications, even if the applications have common authentication requirements in CDF. It may cause the applications to be subject to [rate-limiting](#rate-limiting) and may also cause issues with audit logs, with events from multiple entities being identified under a common client ID.
</Warning>

The best practice is to avoid throttling, because throttled requests still consume some resources and therefore may be counted. For example, a request that was throttled due to concurrency may still consume rate capacity. However, if throttling happens, Cognite recommends using a retry strategy based on the [truncated exponential backoff](https://en.wikipedia.org/wiki/Exponential_backoff) to handle sessions with the HTTP response codes 429 (or 503). The strategy lets clients slow down the request frequency to maximize productivity without having to re-submit failing requests.

**Learn more** about exponential backoff:

- [Microsoft](https://docs.microsoft.com/en-us/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff)
- [Google](https://cloud.google.com/storage/docs/retry-strategy)

## Rate limiting

Rate limiting happens when a user sends requests too fast. Refer to a particular resource's documentation for more details as different endpoints can have different rate limits. The limit scopes are the following:

- By user (identity)
- By project

Some endpoints consider request "weight" (for example, how many objects/items a particular request modifies or retrieves.

To avoid throttling due to rate limiting:

- Reduce the number of parallel workers sending these requests.
- Reduce "weight" of individual requests (for endpoints that have a "weighted" rate limit).
- Introduce delays between requests.

## Concurrency limiting

Number of concurrent requests that a particular endpoint can process is limited. Refer to a particular resource's documentation for more details as different endpoints can have
different concurrency limits. The limit scopes are the following:

- By user (identity)
- By project

To avoid throttling due to concurrency limiting:

- Reduce the number of parallel workers sending these requests.

## Conflicting requests

Requests are considered conflicting when they're concurrently trying to modify the same resource. For example,
if 2 concurrent requests update the same asset, they're considered conflicting, and one of them may get throttled.

To avoid conflicting requests:

- Instead of "change log"-style updates, where each modification made in the source system is replicated to CDF,
  replicate only the latest state.
- Reduce the number of parallel workers sending these requests.


<!-- SOURCE_END: docs.cognite.com/docs\dev\concepts\resource_throttling.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\concepts\resource_types\3dmodels.mdx -->
## File: docs.cognite.com/docs\dev\concepts\resource_types\3dmodels.mdx

---
title: '3D models'
description: 'Learn how CDF stores and organizes 3D models in models and revisions to provide visual and geometrical context to assets.'
content-type: 'concept'
audience: [developer, dataEngineer]
experience-level: 100
article-type: 'article'
lifecycle: use
---
In Cognite Data Fusion, the 3D model **resource type** stores files that provide **visual** and **geometrical data** and **context** to assets. For example, CDF can connect a pump asset with a 3D model of the plant floor where it's placed. Seeing asset data rendered in 3D helps you find the sensor data of your interest faster.

## About 3D models

CDF organizes 3D data in **models** and **revisions**:

- **Models** are placeholders for a set of revisions.

- **Revisions** contain the 3D data.

For example, you can have a model named `Compressor` and can upload a revision under that model.

When you create a revision you need to attach a 3D file. For each new version of the 3D model, you upload a new revision under the same model. A revision can have the status `published` or `unpublished` used by applications to decide whether to list the revision. Many revisions can be published at the same time since they don't necessarily represent the time evolution of the 3D model, but rather different versions (high detail vs low detail).

When you upload a new revision, Cognite needs to process the 3D data to optimize it for the renderer. Depending on the complexity of the 3D file, this can take some time. A revision can have the status `Queued`, `Processing`, `Done`, or `Failed`, which can be tracked during processing.

3D data is built up by a hierarchical structure. This is similar to how CDF organizes its internal asset hierarchy. Each node in the 3D hierarchy is automatically assigned a unique `nodeId` value.

If a user clicks an object on the screen, the application can get a callback containing `nodeId` of the clicked object. CDF supports endpoints to extract the full 3D node hierarchy, and endpoints to create mapping between 3D nodes and nodes in the asset hierarchy. You can then use the `nodeId` to connect the 3D data to asset information such as metadata and time series.

You can also use the [web based 3D viewer](https://cognitedata.github.io/reveal-docs/docs) to embed the 3D model in a web page.

<Tip>
See the [3D API documentation](https://api-docs.cognite.com/20230101/#tag/3D-Models/operation/create3DModels) for more information about how to work with 3D models.
</Tip>


<!-- SOURCE_END: docs.cognite.com/docs\dev\concepts\resource_types\3dmodels.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\concepts\resource_types\assets.mdx -->
## File: docs.cognite.com/docs\dev\concepts\resource_types\assets.mdx

---
title: 'Assets'
description: 'Learn how assets in CDF store digital representations of physical objects and connect related data from different sources for contextualization.'
content-type: 'concept'
audience: [developer, dataEngineer]
experience-level: 200
article-type: 'article'
lifecycle: use
---
In Cognite Data Fusion, the asset **resource type** stores the **digital representations** of objects or groups of **objects from the physical world**, such as water pumps, heart rate monitors, machine rooms, and production lines.

## About assets

Assets **connect related data from different sources** and are core to identifying all the data relevant to an entity (**contextualization**) in Cognite Data Fusion.

All other resource types, such as time series, events, and files, should be connected to **at least one** asset. You can connect each asset to **many resources and resource types**. For example, you can connect a pump asset to the time series that measures the pressure within the pump, to events that record maintenance operations, and to a file with a pump diagram.

Assets themselves are organized into **asset hierarchies**. For example, one asset can represent a **water pump** that's part of a larger **subsystem** asset on an **oil platform** asset.

At the top of each asset hierarchy is a **root asset**, for example, an oil platform. Each project can have several root assets, and all assets under the root asset must have a **parent** asset.

<Tip>
See the [assets API documentation](https://api-docs.cognite.com/20230101/#tag/Assets) for more information about how to work with assets.
</Tip>

## Structure an asset hierarchy

The  example shows you how to structure the asset hierarchy for the fictional **SYSTEM 11**:

```txt
SYSTEM 11
└───Pump
│   └───Heating cable
└───Pump
```

Outlined in CSV format, the system looks like this:

| name          | description               | externalId    | parentExternalId |
| ------------- | ------------------------- | ------------- | ---------------- |
| SYSTEM 11     | Sea water system          | SYSTEM_11     |                  |
| Pump          | Main pump for system 11   | PUMP_A        | SYSTEM_11        |
| Heating cable | Heating cable for pump A  | HEATING_CABLE | PUMP_A           |
| Pump          | Backup pump for system 11 | PUMP_B        | SYSTEM_11        |

You can post all assets in one request when you structure an asset hierarchy.

1. Post the following request:

   ```http wrap
   POST /api/v1/projects/<project>/assets
   Host: api.cognitedata.com
   ```

   With this request body:

   ```json wrap
   {
     "items": [
       {
         "name": "SYSTEM 11",
         "description": "Sea water system",
         "externalId": "SYSTEM_11"
       },
       {
         "name": "Pump",
         "description": "Main pump for system 11",
         "externalId": "PUMP_A",
         "parentExternalId": "SYSTEM_11"
       },
       {
         "name": "Pump",
         "externalId": "Pump_B",
         "description": "Backup pump for system 11",
         "parentExternalId": "SYSTEM_11"
       },
       {
         "name": "Heating cable",
         "externalId": "HEATING_CABLE",
         "description": "Heating cable for pump A",
         "parentExternalId": "PUMP_A"
       }
     ]
   }
   ```

   The response body will look similar to this:

   ```json wrap
   {
     "items": [
       {
         "externalId": "SYSTEM_11",
         "name": "SYSTEM 11",
         "description": "Sea water system",
         "metadata": {},
         "id": 4181031623333192,
         "createdTime": 1562764416913,
         "lastUpdatedTime": 1562764416913,
         "rootId": 4181031623333192
       },
       {
         "externalId": "PUMP_A",
         "name": "Pump",
         "parentId": 4181031623333192,
         "description": "Main pump for system 11",
         "metadata": {},
         "id": 2975365566518130,
         "createdTime": 1562764416913,
         "lastUpdatedTime": 1562764416913,
         "rootId": 4181031623333192
       },
       {
         "externalId": "Pump_B",
         "name": "Pump",
         "parentId": 4181031623333192,
         "description": "Backup pump for system 11",
         "metadata": {},
         "id": 1366019363753734,
         "createdTime": 1562764416913,
         "lastUpdatedTime": 1562764416913,
         "rootId": 4181031623333192
       },
       {
         "externalId": "HEATING_CABLE",
         "name": "Heating cable",
         "parentId": 2975365566518130,
         "description": "Heating cable for pump A",
         "metadata": {},
         "id": 3816457134628307,
         "createdTime": 1562764416913,
         "lastUpdatedTime": 1562764416913,
         "rootId": 4181031623333192
       }
     ]
   }
   ```

In addition to `externalId`, you get a unique `id` field and value. References within CDF use that id, so `parentExternalId` is translated into that new unique identifier. Also, you have a new `rootId` field that gives you the id of the root asset.

## Rate and concurrency limits

There are limits on the rate of requests (RPS) and the number of parallel requests. A request exceeding the limits will result in a 429 error response: Too Many Requests.

Define limits at both the API service and endpoint levels. Every request has a different budget due to the varying resource consumption.
For example, there are two types of requests: CRUD (Create, Retrieve, Request ByIDs, Update, and Delete) and Analytical (List, Search, and Filter). CRUD requests are less resource-intensive than Analytical requests. Among all Analytical requests, Aggregates are the most resource-intensive, so they receive their request budget within the overall Analytical request budget.

The limits for the API service and its endpoints are shown in the diagram below. These limits are subject to change based on consumption patterns and resource availability over time.  Changes to limits will be notified in the changelog.

<Frame>
<img src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/api-docs/AssetsLimitsFeb2023.png" alt="Assets API rate and concurrency limits diagram" width="80%"/>
</Frame>

### Translate RPS to data speed
A single request can retrieve up to 1000 items, where 1 item is an asset record. The top API service level has a maximum theoretical data speed of 200,000 items per second for all consumers and 150,000 for a single identity or client in a project.

### Use of parallel retrieval

**Parallel retrieval** is a technique used to improve data retrieval performance in cases where due to query complexity, data retrieval speeds are lower than they would normally be with a fast, simple query. Use parallel retrieval to retrieve large data sets up to the capacity limits defined for an API service.

For example, the Assets API request has the following limits:

* A single request can retrieve up to 1000 items.
* Up to 23 requests per second may be issued for an analytical query (per identity), such as when using /list or /filter API endpoints (see above diagram).

Resulting in:

* A theoretical maximum of 23,000 items read per second per identity.

Additionally, complex analytical queries may return data slower than the theoretical maximum. Typically, the more complex the query, the slower the data rate.

Resulting in:

* A single request taking longer than 1s to read or write 1000 items.

Therefore, for complex 'analytical' queries that return data slower than the theoretical maximum, the query should retrieve fewer items per request and more in parallel until the theoretical maximum performance of 23,000 items per second is reached.

<Note>
Use parallel retrieval only when a single request flow provides data retrieval speeds significantly less than the theoretical maximum.
The overall requests per second limit still apply regardless of the number of concurrent requests issued. For example, if a request returns data at 18,000 items per second, adding a second parallel request provides little benefit as only 5,000 more items can be returned before the budget limit is reached.
</Note>


<!-- SOURCE_END: docs.cognite.com/docs\dev\concepts\resource_types\assets.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\concepts\resource_types\entity_matching.mdx -->
## File: docs.cognite.com/docs\dev\concepts\resource_types\entity_matching.mdx

---
title: 'Entity matching'
description: 'Learn how to use entity matching to contextualize data with machine learning by matching entities like time series, files, and sequences to assets.'
content-type: 'concept'
audience: [developer, dataEngineer]
experience-level: 200
article-type: 'article'
lifecycle: use
---
{/*What are entity matching?  Generic overview information*/}

Use **entity matching** to contextualize your data with machine learning (ML) and rules engines, and then let domain experts validate and fine-tune the results.

Different sources of industrial data can use different naming standards when they refer to the same **entity**. With CDF, you can match all entities like time series, files, and sequences, along with 3d from different source systems to assets.

## About entity matching

[Assets](/dev/concepts/resource_types/assets) in CDF connect related data from different sources, such as time series, files, sequences, and events. The data often use different naming conventions, even when they refer to the same **entity**. Entity matching applies artificial intelligence techniques to automatically match the different resources according to name, description, etc.

<Tip>
See the [entity matching API documentation](https://api-docs.cognite.com/20230101/#tag/Entity-matching) for more information on working with relationships.
</Tip>

## Example data

{/*create dummy data for entity matching  */}

This tutorial uses a small example data set to show how to do entity matching. The data is in Python format, and you can convert it to other formats that fits your programming language.

```python wrap
sources = [
    {"id":0, "name" : "KKL_21AA1019CA.PV", "description": "correct"},
    {"id":1, "name" : "KKL_13FV1234BU.VW", "description": "ok"}
]
targets = [
    {"id":0, "name" : "21AA1019CA", "description": "correct"},
    {"id":1, "name" : "21AA1019CA", "description": "wrong"},
    {"id":2, "name" : "13FV1234BU"},
    {"id":3, "name" : "13FV1234BU", "description": "ok"}
]
true_matches =  [{"sourceId": 0,"targetId": 0}]
```

## Fit a supervised ML model and predict for the same data


The supervised model calculates one or more similarity measures between match-to and match-from items. Then it uses these calculated similarity measures as features and fits a classification model using the labeled data.

Note that a set of candidate matches is selected before calculating the similarity measures and training a model. To consider a pair of match-to and match-from items a candidate, the items should have at least one token in common. Only the candidate match-from and match-to combinations are used in the training. This is done to reduce computing time - calculating similarity measures for all possible combinations can be extremely heavy (10.000 time series and 30.000 assets -> 300.000.000 combinations).

### When to use a supervised ML model?


A supervised ML model is applicable when you have a lot of labeled data items. The more labeled data you have, the better results you can achieve. Make sure not to apply a supervised model if you have fewer than 500 labeled data items.

### Create a model

```http
POST /api/v1/projects/publicdata/context/entitymatching
Content-Type: application/json

{
   "sources":sources,
   "targets":targets,
   "trueMatches":true_matches,
}
```

### Predict

When `predict` is called without any data, predictions are on the training data.

`num_matches` determines the number of matches to return for each `matchFrom` item. The default is 1.

```http wrap
POST /api/v1/projects/publicdata/context/entitymatching/predict
Content-Type: application/json

{
  "id": modelId,
  "numMatches": 3,
  "scoreThreshold": 0.7
}
```

### Get matching results

Replace `job_id` in the following GET body with the `jobId` returned by the response above.

```http wrap
GET /api/v1/projects/publicdata/context/entitymatching/jobs/<job_id>
Content-Type: application/json
```

**Example output:**

```json wrap
[
  {
    "matchFrom": {
      "description": "correct",
      "id": 0,
      "name": "KKL_21AA1019CA.PV"
    },
    "matches": [
      {
        "matchTo": { "description": "correct", "id": 0, "name": "21AA1019CA" },
        "score": 0.35,
        "target": { "description": "correct", "id": 0, "name": "21AA1019CA" }
      },
      {
        "matchTo": { "description": "wrong", "id": 1, "name": "21AA1019CA" },
        "score": 0.35,
        "target": { "description": "wrong", "id": 1, "name": "21AA1019CA" }
      }
    ],
    "source": { "description": "correct", "id": 0, "name": "KKL_21AA1019CA.PV" }
  },
  {
    "matchFrom": { "description": "ok", "id": 1, "name": "KKL_13FV1234BU.VW" },
    "matches": [
      {
        "matchTo": { "id": 2, "name": "13FV1234BU" },
        "score": 0.35,
        "target": { "id": 2, "name": "13FV1234BU" }
      },
      {
        "matchTo": { "description": "ok", "id": 3, "name": "13FV1234BU" },
        "score": 0.35,
        "target": { "description": "ok", "id": 3, "name": "13FV1234BU" }
      }
    ],
    "source": { "description": "ok", "id": 1, "name": "KKL_13FV1234BU.VW" }
  }
]
```

<Note>
For both `matchFrom` items, the two matches have an equal score, and the model isn't able to distinguish between the correct and incorrect match. Also, the scores are relatively low. When the data set is small, [unsupervised](#fit-unsupervised-model) learning makes more sense.
</Note>

### Refit

Refit lets you retrain a model, using the same parameters, with extra labels/true-matches. The new `true_matches` (1,3) are added to the `true_matches` list from the original model.

To fit a model using only the (1,3) label. A new model must be trained using fit.

```python wrap
true_matches=[{"sourceId": 0,"targetId": 3}]
```

```http wrap
POST /api/v1/projects/publicdata/context/entitymatching/refit
Content-Type: application/json

{
   "id": modelId
   "trueMatches":true_matches,
}
```

```http wrap
POST /api/v1/projects/publicdata/context/entitymatching/predict
Content-Type: application/json

{
  "id": modelId,
  "numMatches": 3,
  "scoreThreshold": 0.7
}
```

```http wrap
GET /api/v1/projects/publicdata/context/entitymatching/jobs/<job_id>
Content-Type: application/json
```

<Note>
In this example, the results are the same. The new `true-match` follows the exact same pattern as the original.
</Note>

## Fit an unsupervised ML model

If there are no `true_matches` included in the `fit` call, an unsupervised model is applied.

As with supervised models, candidates are selected and similarity measures between the candidates are calculated. Instead of training a classification model, the average of the similarity measures is calculated and returned as the score.

### When to use an unsupervised ML model?

Use an unsupervised ML model when there are no or few true matches (labeled data).

### Create a model

```http wrap
POST /api/v1/projects/publicdata/context/entitymatching/
Content-Type: application/json

{
   "sources":sources,
   "targets":targets,
   "name":"simple_model_1",
   "description":"Simple model 1",
   "featureType":"simple",
   "classifier":"randomforest"
}
```

**Example response:**

```json wrap
{
  "classifier": "randomforest",
  "createdTime": 1606151476539,
  "description": "Simple model 1",
  "externalId": "None",
  "featureType": "simple",
  "id": 7895111848480381,
  "ignoreMissingFields": True,
  "matchFields": [
    {
      "source": "name",
      "target": "name"
    },
    {
      "source": "description",
      "target": "description"
    }
  ],
  "name": "simple_model_1",
  "startTime": "None",
  "status": "Queued",
  "statusTime": 1606151476539
}
```

### Create a matching job

In the following POST body, replace `id` with the `id` returned by the response above. You can set maximum matching items and threshold to filter valid matching.

```http wrap
POST /api/v1/projects/publicdata/context/entitymatching/predict
Content-Type: application/json

{
  "id": 7895111848480381,
  "numMatches": 3,
  "scoreThreshold": 0.7
}
```

**Example response:**

```python wrap
{
   "createdTime":1606151691970,
   "jobId":6147120367590349,
   "startTime":"None",
   "status":"Queued",
   "statusTime":1606151691970
}
```

### Get matching results

In the following GET body, replace `job_id` with the `jobId` return by the response above.

```http wrap
GET /api/v1/projects/publicdata/context/entitymatching/jobs/<job_id>
Content-Type: application/json
```

**Example response:**

```python wrap
{'createdTime': 1606216545060,
 'items': [{'matchFrom': {'description': 'correct',
    'id': 0,
    'name': 'KKL_21AA1019CA.PV'},
   'matches': [{'matchTo': {'description': 'correct',
      'id': 0,
      'name': '21AA1019CA'},
     'score': 1.0,
     'target': {'description': 'correct', 'id': 0, 'name': '21AA1019CA'}},
    {'matchTo': {'description': 'wrong', 'id': 1, 'name': '21AA1019CA'},
     'score': 1.0,
     'target': {'description': 'wrong', 'id': 1, 'name': '21AA1019CA'}}],
   'source': {'description': 'correct', 'id': 0, 'name': 'KKL_21AA1019CA.PV'}},
  {'matchFrom': {'description': 'ok', 'id': 1, 'name': 'KKL_13FV1234BU.VW'},
   'matches': [{'matchTo': {'id': 2, 'name': '13FV1234BU'},
     'score': 1.0,
     'target': {'id': 2, 'name': '13FV1234BU'}},
    {'matchTo': {'description': 'ok', 'id': 3, 'name': '13FV1234BU'},
     'score': 1.0,
     'target': {'description': 'ok', 'id': 3, 'name': '13FV1234BU'}}],
   'source': {'description': 'ok', 'id': 1, 'name': 'KKL_13FV1234BU.VW'}}],
 'jobId': 6147120367590349,
 'startTime': 1606216546372,
 'status': 'Completed',
 'statusTime': 1606216546552}
}
```

### Add additional key `match_fields`

By default, only `name` in sources and `name` in targets are used to calculate similarity measures. The `match_fields` parameter lets you specify all combinations of fields in sources and targets that should be used to calculate features.

In this example, it looks like comparing the `description` field for both targets and sources could improve the model.

<Note>
Calculating similarity measures can be time-consuming. It's recommended that you don't use `match_fields` combinations that add little or no information to the model.
</Note>

```http wrap
POST /api/v1/projects/publicdata/context/entitymatching/
Content-Type: application/json

{
   "sources":sources,
   "targets":targets,
   "name":"simple_model_1",
   "description":"Simple model 1",
   "featureType":"simple",
   "matchFields":[
      {
         "source":"name",
         "target":"name"
      },
      {
         "source":"description",
         "target":"description"
      }
   ],
   "classifier":"randomforest",
}
```

The request above results in an error because one of the items in `match_to` is missing `description`. If `complete_missing` is set to `True`, empty strings replace missing values.

### Add `ignore_missing_fields`

To avoid errors if items in sources or targets have missing values, add `ignore_missing_fields=True`.

```http wrap
POST /api/v1/projects/publicdata/context/entitymatching/
Content-Type: application/json

{
   "sources":sources,
   "targets":targets,
   "name":"simple_model_1",
   "description":"Simple model 1",
   "featureType":"simple",
   "matchFields":[
      {
         "source":"name",
         "target":"name"
      },
      {
         "source":"description",
         "target":"description"
      }
   ],
   "classifier":"randomforest",
   "ignoreMissingFields":true
}
```

<Note>
The model gives the correct matches a score of 1 and the incorrect matches a score of 0.5.
</Note>

### Using feature types

By default, `feature_type` is set to `simple`. The different feature types are created to improve the model's accuracy for different types of input data. Which feature type works best for your model varies based on what your data look like. The options for `feature_type` are: "simple", "bigram", "frequency-weighted-bigram", "bigram-extra-tokenizers", "bigram-combo". This section illustrates the strengths and weaknesses of the different feature types.

#### When to use `feature_type=simple`?

"Simple" is the default feature type and is preferred when one string is a substring of the other and the other characters don't appear in the other string. For example, "BCDEF" is a substring of "ABCDEFG" and the other characters "AG" don't appear in the string "BCDEF". This feature type is also the fastest option.

#### Limitations of the simple feature type

The data below is the same as in the earlier examples, except for two new items in the targets.
_Id 10_ and _13_ are similar to _0_ and _3_, respectively. The first letter combination ("AA" and "FV") is swapped with the prefix for the `match_from` items (KKL).

This leads to difficulties with using the "simple" feature type.

```python wrap
sources = [
    {"id":0, "name" : "KKL_21AA1019CA.PV", "description": "correct"},
    {"id":1, "name" : "KKL_13FV1234BU.VW", "description": "ok"}
]
targets = [
    {"id":0, "name" : "21AA1019CA", "description": "correct"},
    {"id":10, "name" : "21KKL1019CA", "description": "correct"},
    {"id":1, "name" : "21AA1119CA", "description": "wrong"},
    {"id":2, "name" : "13FV1234BU"},
    {"id":3, "name" : "13FV1334BU", "description": "ok"},
    {"id":13, "name" : "13KKL1234BU", "description": "ok"}
]
```

```http wrap
POST /api/v1/projects/publicdata/context/entitymatching/
Content-Type: application/json

{
   "sources":sources,
   "targets":targets,
   "name":"simple_model_1",
   "description":"Simple model 1",
   "featureType":"simple",
   "matchFields":[
      {
         "source":"name",
         "target":"name"
      }
   ],
   "classifier":"randomforest",
}
```

**Example results:**

```python wrap
{'createdTime': 1606153318209,
 'items': [{'matchFrom': {'description': 'correct',
    'id': 0,
    'name': 'KKL_21AA1019CA.PV'},
   'matches': [{'matchTo': {'description': 'correct',
      'id': 0,
      'name': '21AA1019CA'},
     'score': 0.8944271909999159,
     'target': {'description': 'correct', 'id': 0, 'name': '21AA1019CA'}},
    {'matchTo': {'description': 'correct', 'id': 10, 'name': '21KKL1019CA'},
     'score': 0.8944271909999159,
     'target': {'description': 'correct', 'id': 10, 'name': '21KKL1019CA'}}],
   'source': {'description': 'correct', 'id': 0, 'name': 'KKL_21AA1019CA.PV'}},
  {'matchFrom': {'description': 'ok', 'id': 1, 'name': 'KKL_13FV1234BU.VW'},
   'matches': [{'matchTo': {'id': 2, 'name': '13FV1234BU'},
     'score': 0.8944271909999159,
     'target': {'id': 2, 'name': '13FV1234BU'}},
    {'matchTo': {'description': 'ok', 'id': 13, 'name': '13KKL1234BU'},
     'score': 0.8944271909999159,
     'target': {'description': 'ok', 'id': 13, 'name': '13KKL1234BU'}}],
   'source': {'description': 'ok', 'id': 1, 'name': 'KKL_13FV1234BU.VW'}}],
 'jobId': 5049964554908808,
 'startTime': 1606153318586,
 'status': 'Completed',
 'statusTime': 1606153318777}
```

<Note>
The new target items have identical scores to the correct matches. This is because with `feature_type="simple"`, only the number of matching tokens are considered.
</Note>

Target items with `id` _0_ _21_, _AA_, _1019_, and _CA_ match a token in sources items with `id` _0_. Target items with `id` _10_ _21_, _KKL_, _1019_, and _CA_ matches a token in source items with `id` _0_. Thus, the same number of tokens matches.

The model doesn't consider that the target items with `id` _0_ have more and longer contiguous sequences of tokens.

#### When to use `feature_type=bigram`?

The "bigram" feature type does account for sequences of tokens. In addition to counting the number of matching tokens, it looks at the number of matching bigrams. that's the number of matching tokens when two and two adjacent tokens are combined, BCDEF and ABBCDE1F, for example.

```python wrap
sources = [
    {"id":0, "name" : "KKL_21AA1019CA.PV", "description": "correct"},
    {"id":1, "name" : "KKL_13FV1234BU.VW", "description": "ok"}
]
targets = [
    {"id":0, "name" : "21AA1019CA", "description": "correct"},
    {"id":10, "name" : "21KKL1019CA", "description": "correct"},
    {"id":1, "name" : "21AA1119CA", "description": "wrong"},
    {"id":2, "name" : "13FV1234BU"},
    {"id":3, "name" : "13FV1334BU", "description": "ok"},
    {"id":13, "name" : "13KKL1234BU", "description": "ok"}
]
```

```http wrap
POST /api/v1/projects/publicdata/context/entitymatching/
Content-Type: application/json

{
   "sources":sources,
   "targets":targets,
   "name":"bigram_model_1",
   "description":"bigram model 1",
   "featureType":"bigram",
   "matchFields":[
      {
         "source":"name",
         "target":"name"
      }
   ],
   "classifier":"randomforest",
}
```

**Example results:**

```python wrap
{'createdTime': 1606153733720,
 'items': [{'matchFrom': {'description': 'correct',
    'id': 0,
    'name': 'KKL_21AA1019CA.PV'},
   'matches': [{'matchTo': {'description': 'correct',
      'id': 0,
      'name': '21AA1019CA'},
     'score': 0.9149207688467006,
     'target': {'description': 'correct', 'id': 0, 'name': '21AA1019CA'}}],
   'source': {'description': 'correct', 'id': 0, 'name': 'KKL_21AA1019CA.PV'}},
  {'matchFrom': {'description': 'ok', 'id': 1, 'name': 'KKL_13FV1234BU.VW'},
   'matches': [{'matchTo': {'id': 2, 'name': '13FV1234BU'},
     'score': 0.9149207688467006,
     'target': {'id': 2, 'name': '13FV1234BU'}}],
   'source': {'description': 'ok', 'id': 1, 'name': 'KKL_13FV1234BU.VW'}}],
 'jobId': 6677803085358639,
 'startTime': 1606153733995,
 'status': 'Completed',
 'statusTime': 1606153734183}
```

#### When to use `feature_type=Frequency-Weighted-Bigram`?

The "Frequency-Weighted-Bigram" feature type calculates a similarity score based on the sequence of the terms. It also gives higher weights to less commonly occurring tokens. This can be helpful when the "simple" feature type doesn't return useful results.

```python wrap
sources = [
    {"id":0, "name" : "KKL_21AA1019CA.PV", "description": "correct"},
    {"id":1, "name" : "KKL_13FV1234BU.VW", "description": "ok"}
]
targets = [
    {"id":0, "name" : "21AA1019CA", "description": "correct"},
    {"id":10, "name" : "21KKL1019CA", "description": "correct"},
    {"id":1, "name" : "21AA1119CA", "description": "wrong"},
    {"id":2, "name" : "13FV1234BU"},
    {"id":3, "name" : "13FV1334BU", "description": "ok"},
    {"id":13, "name" : "13KKL1234BU", "description": "ok"}
]
```

```http wrap
POST /api/v1/projects/publicdata/context/entitymatching/
Content-Type: application/json

{
   "sources":sources,
   "targets":targets,
   "name":"fbw_model_1",
   "description":"fwb model 1",
   "featureType":"Frequency-Weighted-Bigram",
   "matchFields":[
      {
         "source":"name",
         "target":"name"
      }
   ],
   "classifier":"randomforest",
}
```

**Example results:**

```python wrap
{'createdTime': 1606153836538,
 'items': [{'matchFrom': {'description': 'correct',
    'id': 0,
    'name': 'KKL_21AA1019CA.PV'},
   'matches': [{'matchTo': {'description': 'correct',
      'id': 0,
      'name': '21AA1019CA'},
     'score': 0.9149207688467006,
     'target': {'description': 'correct', 'id': 0, 'name': '21AA1019CA'}}],
   'source': {'description': 'correct', 'id': 0, 'name': 'KKL_21AA1019CA.PV'}},
  {'matchFrom': {'description': 'ok', 'id': 1, 'name': 'KKL_13FV1234BU.VW'},
   'matches': [{'matchTo': {'id': 2, 'name': '13FV1234BU'},
     'score': 0.9149207688467006,
     'target': {'id': 2, 'name': '13FV1234BU'}}],
   'source': {'description': 'ok', 'id': 1, 'name': 'KKL_13FV1234BU.VW'}}],
 'jobId': 5204435061404568,
 'startTime': 1606153836790,
 'status': 'Completed',
 'statusTime': 1606153837071}
```

#### When to use `feature_type=Bigram-Extra-Tokenizers`?

The "Bigram-Extra-Tokenizers" feature type is similar to bigram but can learn that leading zeros and spaces should be ignored in matching. For example ABCDE and 000ABBCDE1F.

```python wrap
sources = [
    {"id":0, "name" : "KKL_21AA1019CA.PV", "description": "correct"},
    {"id":1, "name" : "KKL_13FV1234BU.VW", "description": "ok"}
]
targets = [
    {"id":0, "name" : "000021AA1019CA", "description": "correct"},
    {"id":10, "name" : "21KKL1019CA", "description": "correct"},
    {"id":1, "name" : "21AA1119CA", "description": "wrong"},
    {"id":2, "name" : "000013FV1234BU"},
    {"id":3, "name" : "13FV1334BU", "description": "ok"},
    {"id":13, "name" : "13KKL1234BU", "description": "ok"}
]
```

```http wrap
POST /api/v1/projects/publicdata/context/entitymatching/
Content-Type: application/json

{
   "sources":sources,
   "targets":targets,
   "name":"BET_model_1",
   "description":"BET model 1",
   "featureType":"Bigram-Extra-Tokenizers",
   "matchFields":[
      {
         "source":"name",
         "target":"name"
      }
   ],
   "classifier":"randomforest",
}
```

**Example results:**

```python wrap
{'createdTime': 1606154023578,
 'items': [{'matchFrom': {'description': 'correct',
    'id': 0,
    'name': 'KKL_21AA1019CA.PV'},
   'matches': [{'matchTo': {'description': 'correct',
      'id': 0,
      'name': '000021AA1019CA'},
     'score': 0.8477338488399959,
     'target': {'description': 'correct', 'id': 0, 'name': '000021AA1019CA'}}],
   'source': {'description': 'correct', 'id': 0, 'name': 'KKL_21AA1019CA.PV'}},
  {'matchFrom': {'description': 'ok', 'id': 1, 'name': 'KKL_13FV1234BU.VW'},
   'matches': [{'matchTo': {'id': 2, 'name': '000013FV1234BU'},
     'score': 0.8477338488399959,
     'target': {'id': 2, 'name': '000013FV1234BU'}}],
   'source': {'description': 'ok', 'id': 1, 'name': 'KKL_13FV1234BU.VW'}}],
 'jobId': 6675842531673047,
 'startTime': 1606154023915,
 'status': 'Completed',
 'statusTime': 1606154024190}
```

#### When to use `feature_type=Bigram-Combo`?

The "Bigram-Combo" feature type calculates all the above options relying on the model to find the appropriate features to use. This is the slowest option and is mostly suitable for supervised models.

```http wrap
POST /api/v1/projects/publicdata/context/entitymatching/
Content-Type: application/json

{
   "sources":sources,
   "targets":targets,
   "name":"BC_model_1",
   "description":"BC model 1",
   "featureType":"Bigram-Combo",
   "matchFields":[
      {
         "source":"name",
         "target":"name"
      }
   ],
   "classifier":"randomforest",
}
```

**Example results:**

```python wrap
{'createdTime': 1606154392343,
 'items': [{'matchFrom': {'description': 'correct',
    'id': 0,
    'name': 'KKL_21AA1019CA.PV'},
   'matches': [{'matchTo': {'description': 'correct',
      'id': 0,
      'name': '21AA1019CA'},
     'score': 0.8195704160778803,
     'target': {'description': 'correct', 'id': 0, 'name': '21AA1019CA'}}],
   'source': {'description': 'correct', 'id': 0, 'name': 'KKL_21AA1019CA.PV'}},
  {'matchFrom': {'description': 'ok', 'id': 1, 'name': 'KKL_13FV1234BU.VW'},
   'matches': [{'matchTo': {'id': 2, 'name': '13FV1234BU'},
     'score': 0.8195704160778803,
     'target': {'id': 2, 'name': '13FV1234BU'}}],
   'source': {'description': 'ok', 'id': 1, 'name': 'KKL_13FV1234BU.VW'}}],
 'jobId': 4397635623636246,
 'startTime': 1606154392670,
 'status': 'Completed',
 'statusTime': 1606154392848}
```

#### About the score

A score above 0.8 indicates that the source and the target are matched with high probability.

Note that a score below 0.8 and above 0.5 doesn't indicate that the source and target are matched with more than 50% probability. The example below illustrates that even if the source and the target don't match at all, they can still receive a score over 0.6:

```python
sources = [{"id":0, "name" : "J04_ONSTREAM_HOUR_AVG", "description": "correct"}]
targets = [{"id":0, "name" : "87-JB-004-J04", "description": "correct"}]
```

```http wrap
POST /api/v1/projects/publicdata/context/entitymatching/
Content-Type: application/json

{
   "sources":sources,
   "targets":targets,
   "name":"bigram_model_1",
   "description":"bigram model 1",
   "featureType":"bigram",
   "matchFields":[
      {
         "source":"name",
         "target":"name"
      }
   ],
   "classifier":"randomforest",
}
```

```http wrap
POST /api/v1/projects/publicdata/context/entitymatching/predict
Content-Type: application/json

{
  "id": 6147120367590349,
  "numMatches": 3,
  "scoreThreshold": 0.5
}
```

```http wrap
GET /api/v1/projects/publicdata/context/entitymatching/jobs/<job_id>
Content-Type: application/json
```

Example matching results

```python wrap
{'createdTime': 1606154525247,
 'items': [{'matchFrom': {'description': 'correct',
    'id': 0,
    'name': 'J04_ONSTREAM_HOUR_AVG'},
   'matches': [{'matchTo': {'description': 'correct',
      'id': 0,
      'name': '87-JB-004-J04'},
     'score': 0.6049029006116509,
     'target': {'description': 'correct', 'id': 0, 'name': '87-JB-004-J04'}}],
   'source': {'description': 'correct',
    'id': 0,
    'name': 'J04_ONSTREAM_HOUR_AVG'}}],
 'jobId': 1019931106940009,
 'startTime': 1606154525580,
 'status': 'Completed',
 'statusTime': 1606154525758}
```

## Get model info

If you have a `model_id` and want to know which parameters you used when training the model, use the retrieve method.

```http wrap
GET /api/v1/projects/publicdata/context/entitymatching/<model_id>
Content-Type: application/json
```


<!-- SOURCE_END: docs.cognite.com/docs\dev\concepts\resource_types\entity_matching.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\concepts\resource_types\events.mdx -->
## File: docs.cognite.com/docs\dev\concepts\resource_types\events.mdx

---
title: 'Events'
description: 'Learn how CDF stores events as time-based information with start and end times that can be related to multiple assets.'
content-type: 'concept'
audience: [developer, dataEngineer]
experience-level: 100
article-type: 'article'
lifecycle: use
---
In Cognite Data Fusion, the event **resource type** stores complex information that happens over **a period**. Events have a **start time** and an **end time** and can be related to **multiple assets**. For example, Events API includes Alarms, Process Data, and Logs.

The `startTime` and `endTime` for the event's period are represented in Unix Epoch time in milliseconds. CDF doesn't support fractional milliseconds and doesn't count leap seconds. You can specify both the start and end times in the future, but the end timestamp must be greater than or equal to the start timestamp for the input to be valid.

To describe and classify events, you can add arbitrary string values for `description`, `metadata`, `type`, and `subtype`. All the data is stored in string format when you store event information in metadata.

<Note>
Use the Data Modeling service to store manually generated, schedulable activities with low volumes, such as maintenance schedules, work orders, or other appointment-type activities.
</Note>

For more information about how to work with events, see [Events API](https://api-docs.cognite.com/20230101/#tag/Events).

<Info>
**Events** and **Time Series** data are high-volume data types that can record data in microsecond resolutions. Avoid using Events as a Time Series store, especially when the data flow is from a single instance of sensors (temperature, pressure, voltage), simulators, or state machines (on, off, disconnected). The Time Series API provides very low latency read and write performance and specialized filters and aggregations designed to analyze time series data.
</Info>

## Rate and concurrency limits

There are limits on the rate of requests (RPS) and the number of parallel requests. A request exceeding the limits will result in a 429 error response: Too Many Requests. 

Define limits at both the API service and endpoint levels. Every request has a different budget due to the varying resource consumption. 
For example, there are two types of requests: CRUD (Create, Retrieve, Request ByIDs, Update, and Delete) and Analytical (List, Search, and Filter). CRUD requests are less resource-intensive than Analytical requests. Among all Analytical requests, Aggregates are the most resource-intensive, so they receive their request budget within the overall Analytical request budget.

The limits for the API service and its endpoints are shown in the diagram below. These limits are subject to change based on consumption patterns and resource availability over time.  Changes to limits will be notified in the changelog.

<Frame>
<img src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/api-docs/EventsLimitsNov23.png" alt="Events API rate and concurrency limits diagram" width="80%"/>
</Frame>

### Translate RPS to data speed
A single request can retrieve up to 1000 items, where 1 item is an event record. The top API service level has a maximum theoretical data speed of 200,000 items per second for all consumers and 150,000 for a single identity or client in a project.

### Use of parallel retrieval

**Parallel retrieval** is a technique used to improve data retrieval performance in cases where due to query complexity, data retrieval speeds are lower than they would normally be with a fast, simple query. Use parallel retrieval to retrieve large data sets up to the capacity limits defined for an API service.

For example, the Events API request has the following limits:

* A single request can retrieve up to 1000 items.
* Up to 23 requests per second may be issued for an analytical query (per identity), such as when using /list or /filter API endpoints (see above diagram).

Resulting in:

* A theoretical maximum of 23,000 items read per second per identity.

Additionally, complex analytical queries may return data slower than the theoretical maximum. Typically, the more complex the query, the slower the data rate.

Resulting in:

* A single request taking longer than 1s to read or write 1000 items.

Therefore, for complex 'analytical' queries that return data slower than the theoretical maximum, the query should retrieve fewer items per request and more in parallel until the theoretical maximum performance of 23,000 items per second is reached.

<Note>
Use parallel retrieval only when a single request flow provides data retrieval speeds significantly less than the theoretical maximum.
The overall requests per second limit still apply regardless of the number of concurrent requests issued. For example, If a request returns data at 18,000 items per second, adding a second parallel request provides little benefit as only 5,000 more items can be returned before the budget limit is reached.
</Note>


<!-- SOURCE_END: docs.cognite.com/docs\dev\concepts\resource_types\events.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\concepts\resource_types\files.mdx -->
## File: docs.cognite.com/docs\dev\concepts\resource_types\files.mdx

---
title: 'Files'
description: 'Learn how CDF stores files and documents related to assets, including metadata, geographic locations, and GeoJSON support.'
content-type: 'concept'
audience: [developer, dataEngineer]
experience-level: 200
article-type: 'article'
lifecycle: use
---
In Cognite Data Fusion, the file **resource type** stores **files** and **documents** that are related to one or more assets. For example, a file can contain a piping and instrumentation diagram (P&ID) that shows how several assets connect.

## About files

Files are created in two steps where the first step **stores the metadata** in a file object, and the second step **uploads the file contents**. This means that files can exist in Cognite Data Fusion without actually being uploaded.

Each file has a unique `id` that's generated at file creation. Specify a `fileName` when the file is created.

If you want to be in control of the file identifier, you can specify an `externalId` which must be unique within a project.

A file can also have `metadata` key-value fields that are searchable. You can use these fields to store source system IDs and other information.

Additionally, files can have `labels` attached to them, making it easier to organize and categorize files.

You can retrieve the information for a file, both standard and dynamic metadata fields, using the files list or searching REST API calls. You can download the file contents with the file download REST API call.

<Tip>
See the [Files API documentation](https://api-docs.cognite.com/20230101/#tag/Files) to learn more about working with the files API.

See the [Labels API documentation](https://api-docs.cognite.com/20230101/#tag/Labels/operation/createLabelDefinitions) to learn more about managing Labels.
</Tip>

## Geographic location of files

Specify a file's geographic location, for example, its geometric features and coordinates, in the `geoLocation` field. Data in this field needs to follow the [GeoJSON specification](https://geojson.org), explained in detail in [RFC 7946](https://tools.ietf.org/html/rfc7946). The [coordinate reference system](https://tools.ietf.org/html/rfc7946#section-4) for all GeoJSON coordinates is a geographic coordinate reference system that uses the [World Geodetic System 1984 (WGS84)](https://tools.ietf.org/html/rfc7946#ref-WGS84).

### GeoJSON types

A GeoJSON object has one of 3 types:

- **Feature** - Geometric objects with (optional) extra features.
- **FeatureCollection** - A collection of Features.
- **GeometryCollection** - A collection of Geometry objects (see below).

Currently, **Feature** is the only type supported by Cognite Data Fusion.

To describe a geographic feature, the Geometry object needs a `type` and a corresponding array of `coordinates`. Below are the supported Geometry types:

{/* vale off */}

| Type            | Description                                                       | Example                                                                                         |                                                                                                                                            |
| --------------- | ----------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| Point           | Only one exact point.                                             | <Icon icon="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/resource_types/point.png" size={48}/>                     | `{"type": "Point", "coordinates": [30, 10]}`                                                                                               |
| MultiPoint      | Multiple points.                                                  | <Icon icon="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/resource_types/multipoint.png" size={48}/>          | `{"type": "MultiPoint", "coordinates": [[10, 40], [40, 30], [20, 20], [30, 10]]}`                                                          |
| LineString      | A line.                                                           | <Icon icon="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/resource_types/linestring.png" size={48}/>           | `{"type": "LineString", "coordinates": [[30, 10], [10, 30], [40, 40]]}`                                                                    |
| MultiLineString | Multiple lines.                                                   | <Icon icon="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/resource_types/multilinestring.png" size={48}/>      | `{"type": "MultiLineString", "coordinates": [[[10, 10], [20, 20], [10, 40]], [[40, 40], [30, 30], [40, 20], [30, 10]]]}`                   |
| Polygon         | A closed shape. Can have inner holes of arbitrary shapes.         | <Icon icon="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/resource_types/polygon.png" size={48}/> | `{"type": "Polygon", "coordinates": [[[35, 10], [45, 45], [15, 40], [10, 20], [35, 10]], [[20, 30], [35, 35], [30, 20], [20, 30]]]}` |
| MultiPolygon    | Multiple closed shapes. Can have inner holes of arbitrary shapes. | <Icon icon="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/resource_types/multipolygon.png" size={48}/>           | `{"type": "MultiPolygon", "coordinates": [[[[30, 20], [45, 40], [10, 40], [30, 20]]], [[[15, 5], [40, 10], [10, 20], [5, 10], [15, 5]]]]}` |

{/* vale on */}

### Adding geoLocation to a file

The `geoLocation` field requires the following properties:

#### type

The type of GeoJSON. Cognite Data Fusion only supports the `Feature` type.

#### geometry

Represents the points, curves, and surfaces in coordinate space. The property consists of:

- `type` - Must be one of the following geometry types: `Point`, `MultiPoint`, `LineString`, `MultiLineString`, `Polygon`, and `MultiPolygon`. See [GeoJSON types](#geojson-types) above.

- `coordinates` - An array describing the specified geometry type. The type of geometry determines the shape of this array. For instance, a `Point` geometry type will contain a `coordinate` array consisting of just a single _x_ and a single _y_ coordinate. See [example 1](#example1) below.

- A `LineString` geometry type will contain a `coordinate` array with two or more points, as shown in [example 2](#example2). A `Polygon` geometry type will need to contain an array of closed `LineStrings` with four or more points, as shown in [example 3](#example3). See [the GeoJSON spec](https://tools.ietf.org/html/rfc7946#section-3.1.1) for more details on the various shapes of the `coordinates` field.

#### properties

An optional field specifying extra information to enrich the `Feature`.

**Example 1** <a name="example1"></a>

```json
{
  "type": "Feature",
  "geometry": {
    "type": "Point",
    "coordinates": [10.727414488792418, 6059.91713955864316]
  },
  "properties": {
    "name": "Norwegian Royal Palace"
  }
}
```

**Example 2** <a name="example2"></a>

```json
{
  "type": "Feature",
  "geometry": {
    "type": "LineString",
    "coordinates": [
      [35, 10],
      [45, 45],
      [15, 40],
      [10, 20],
      [35, 10]
    ]
  }
}
```

**Example 3** <a name="example3"></a>

Note that in this example, the `Polygon` specifies an outer and inner `LineString`.

A `Polygon` can (but doesn't have to) contain several of these `LineStrings`, where the first must be the exterior ring, and the next `LineStrings` are interior rings. This is how you would define a surface with holes.

```json
{
  "type": "Feature",
  "geometry": {
    "type": "Polygon",
    "coordinates": [
      [
        [35, 10],
        [45, 45],
        [15, 40],
        [10, 20],
        [35, 10]
      ],
      [
        [20, 30],
        [35, 35],
        [30, 20],
        [20, 30]
      ]
    ]
  }
}
```

### Upload example

```json
POST /api/v1/projects/publicdata/files
Content-Type: application/json

{
  "name": "file1",
  "externalId": "file",
  "geoLocation": {
    "type": "Feature",
    "geometry": {
      "type": "point",
      "coordinates": [
        10.727414488792418,
        6059.91713955864316
      ]
    },
    "properties": {
      "name": "Norwegian Royal Palace"
    }
  }
}
```

### Update example

```json
POST /api/v1/projects/publicdata/files/update
Content-Type: application/json

{
  "items": [
    {
      "id": 123454321,
      "update": {
        "geoLocation": {
          "set": {
            "type": "Feature",
            "geometry": {
              "type": "Point",
              "coordinates": [
                133.2,
                2.5
              ]
            },
            "properties": {
              "name": "Another place"
            }
          }
        }
      }
    }
  ]
}
```

## GeoLocation filtering

Filtering on, or searching for files matching a certain `geoLocation` requires two properties:

- `relation` - The geographic relation, either `INTERSECTS`, `WITHIN` , or `DISJOINT`.

- `shape` - The `geometry`, as described in [the geometry section](#adding-geolocation-to-a-file). Filtering is **not** available for the `MultiPoint` type.

### Filter example

```json
POST /api/v1/projects/publicdata/files/list
Content-Type: application/json

{
   "filter": {
       "geoLocation": {
         "relation": "intersects",
         "shape": {
           "type": "MultiPolygon",
           "coordinates": [[
            [[35, 10], [45, 45], [15, 40], [10, 20], [35, 10]],
            [[20, 30], [35, 35], [30, 20], [20, 30]]
            ], [
          [[36, 11], [46, 46], [16, 41], [11, 21], [36, 11]],
          [[21, 31], [36, 36], [31, 21], [21, 31]]
          ]]
         }
       }

   }
}
```


## Rate and concurrency limits

There are limits on the rate of requests (RPS) and the number of parallel requests. A request exceeding the limits will result in a 429 error response: Too Many Requests.

Define limits at both the API service and endpoint levels. Every request has a different budget due to the varying resource consumption.
For example, there are two types of requests: CRUD (Create(Upload/Upload multipart/Complete multipart), Retrieve, Request ByIDs, Get icon, Download, Update, and Delete) and Analytical (Aggregate, List, Search, and Filter). CRUD requests are less resource-intensive than Analytical requests. Among all Analytical requests, Aggregates are the most resource-intensive, so they receive their request budget within the overall Analytical request budget.

The limits for the API service and its endpoints are shown in the diagram below. These limits are subject to change based on consumption patterns and resource availability over time.  Changes to limits will be notified in the changelog.

<Frame>
<img src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/api-docs/FilesLimitsFeb2023.png" alt="Files API rate and concurrency limits diagram" width="80%"/>
</Frame>

### Translate RPS to data speed
A single request can retrieve up to 1000 items, where 1 item is a file record. The top API service level has a maximum theoretical data speed of 160,000 items per second for all consumers and 120,000 for a single identity or client in a project.

### Use of parallel retrieval

**Parallel retrieval** is a technique used to improve data retrieval performance in cases where due to query complexity, data retrieval speeds are lower than they would normally be with a fast, simple query. Use parallel retrieval to retrieve large data sets up to the capacity limits defined for an API service.

For example, the Files API request has the following limits:

* A single request can retrieve up to 1000 items.
* Up to 23 requests per second may be issued for an analytical query (per identity), such as when using /list or /filter API endpoints (see above diagram).

Resulting in:

* A theoretical maximum of 23,000 items read per second per identity.

Additionally, complex analytical queries may return data slower than the theoretical maximum. Typically, the more complex the query, the slower the data rate.

Resulting in:

* A single request taking longer than 1s to read or write 1000 items.

Therefore, for complex 'analytical' queries that return data slower than the theoretical maximum, the query should retrieve fewer items per request and more in parallel until the theoretical maximum performance of 23,000 items per second is reached.

<Note>
Use parallel retrieval only when a single request flow provides data retrieval speeds significantly less than the theoretical maximum.
The overall requests per second limit still apply regardless of the number of concurrent requests issued. For example, if a request returns data at 18,000 items per second, adding a second parallel request provides little benefit as only 5,000 more items can be returned before the budget limit is reached.
</Note>


<!-- SOURCE_END: docs.cognite.com/docs\dev\concepts\resource_types\files.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\concepts\resource_types\geospatial.mdx -->
## File: docs.cognite.com/docs\dev\concepts\resource_types\geospatial.mdx

---
title: 'Geospatial'
description: 'Learn how to store and query geospatial features with spatial representations, coordinate reference systems, and complex filtering in CDF.'
content-type: 'concept'
audience: [developer, dataEngineer]
experience-level: 200
article-type: 'article'
lifecycle: use
---
In Cognite Data Fusion (CDF), the geospatial endpoint is the base entry point for many resource types that enable storing and querying item collections that have a geospatial nature. Those items, features in Geographic Information System (GIS) terminology, are grouped in feature types. Features of the same type share several properties (a name, a type, and a value) and have more than one spatial representation stored in configurable properties.

In addition, and following the Open Geospatial Consortium recommendations, each spatial representation should have an associated Coordinate Reference System (CRS), but this isn't mandatory. The Geospatial API also enables feature search using filters of arbitrary complexity, with a boolean expression of conditions on the properties. Defining a feature type is up to the client, and CDF geospatial doesn't come with pre-configured schemas and provides excellent flexibility for modeling the client problem domain.

<Tip>
See the [Geospatial API documentation](https://api-docs.cognite.com/20230101/#tag/Geospatial) for more information.

See [Geospatial Examples](https://github.com/cognitedata/geospatial-examples) for Python SDK examples.
</Tip>

## Authentication and authorization

All HTTP endpoints served by the Geospatial API require a valid ticket from the Auth service.

Feature type and feature related endpoints require the GEOSPATIAL capability (READ or WRITE).

CRS related endpoints require the GEOSPATIAL_CRS capability to be enabled (READ or WRITE). Note that reading the predefined CRSs is possible with the GEOSPATIAL capability.

### Data sets

[Feature types](#feature-types) and [Features](#features) support using data sets. Since feature types define the specification of features, you must set these access capabilities:

- `READ` access for the feature type to do any operations (create, update, get by ids, search, and aggregate) on the corresponding features.
- `WRITE` access for all features of a feature type to delete the feature type.

However, there are no constraints on data set specified on a feature and its corresponding feature type. Features of a feature type can belong to different data sets.

#### Example

Assuming that

- data set 1234 isn't write-protected
- data set 5678 is write-protected
- feature type FT1 has data set ID 1234
- feature type FT2 has data set ID 5678
- feature type FT3 doesn't have data set ID.

`geospatial:read:1234` indicates READ capability of geospatial objects within data set ID 1234.

`geospatial:read:all` indicates READ capability of geospatial objects within all scopes.

`dataset:owner:5678` indicates OWNER capability of data set ID 5678.

| Scenario                                                 | Required access                                                                                                                    |
| -------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| Read feature type FT1                                    | `geospatial:read:1234` or `geospatial:read:all`                                                                                    |
| Read feature type FT3                                    | `geospatial:read:all`                                                                                                              |
| Create feature type FT4 with data set id 1234            | `geospatial:write:1234` or `geospatial:write:all`                                                                                  |
| Create feature type FT4 with data set id 5678            | `dataset:owner:5678` and (`geospatial:write:5678` or `geospatial:write:all`)                                                       |
| Create feature type FT4 without data set id              | `geospatial:write:all`                                                                                                             |
| Read feature X (data set id 5678) of feature type FT1    | (`geospatial:read:1234` and `geospatial:read:5678`) or `geospatial:read:all`                                                       |
| Read feature X (data set id 1234) of feature type FT3    | `geospatial:read:all`                                                                                                              |
| Create feature Y (data set id 1234) of feature type FT1  | (`geospatial:read:1234` or `geospatial:read:all`) and (`geospatial:write:1234` or `geospatial:write:all`)                          |
| Create feature Y (data set id 5678) of feature type FT1  | (`geospatial:read:1234` or `geospatial:read:all`) and (`geospatial:write:5678` or `geospatial:write:all`) and `dataset:owner:5678` |
| Create feature Y without data set id of feature type FT1 | (`geospatial:read:1234` or `geospatial:read:all`) and (`geospatial:write:all`)                                                     |

<Note>
CRSs don't support data sets.
</Note>

## Feature types

A CDF feature type defines a class of spatial features with a common specification. The specification defines the property names and their types and the indexes to support to fulfill performance requirements a client might have. Indexing properties come at a price when creating features, and it's crucial to consider whether an index is necessary or not. The choice depends on the size and number of entries for a given feature type and the frequency of access to that particular property.

## Features

A feature is an item following the specification given by its corresponding feature type. A parallel with a class and an object is valid.

## Properties

### Default properties

#### Feature types predefined properties

Feature types have predefined properties that enable close integration with the rest of the CDF. These properties are:

{/* vale off */}

| Name            | Type        | Description                                     |
| --------------- | ----------  | ----------------------------------------------- |
| externalId      | String      | The standard CDF external id.                    |
| createdTime     | Timestamp   | The standard CDF resource item created time.     |
| lastUpdatedTime | Timestamp   | The standard CDF resource item last update time. |
| dataSetId       | Long        | The standard CDF data set id.                    |

#### Feature predefined properties

Features have the same predefined properties and in addition the `assetIds` property which has a list of internal asset ids of linked assets:

| Name            | Type        | Description                                     |
| --------------- | ----------- | ----------------------------------------------- |
| externalId      | String      | The standard CDF external id.                    |
| createdTime     | Timestamp   | The standard CDF resource item created time.     |
| lastUpdatedTime | Timestamp   | The standard CDF resource item last update time. |
| dataSetId       | Long        | The standard CDF data set id.                    |
| assetIds        | Array[Long] | The standard CDF internal asset ids (links).     |

{/* vale on */}

#### Reserved property names

The following property names are reserved for future use:

- id
- metadata
- defaultGeometry
- defaultRaster
- labels

### Property types

You can find the GeoJSON specification at [GeoJSON](https://geojson.org) and the Well-Known-Text specification at [Open Geospatial Consortium](https://www.ogc.org/standards/sfa).

### 2D

The Geospatial service supports the following vector types, located in a 2-dimensional space, each as an X and a Y coordinate:
{/* vale off */}
| Type            | Description                                                                                                      | Example WKT                                                                            | Example Geojson                                                                                                                      |
| --------------- | ---------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| POINT           | A simple `point`.                                                                                                | `POINT(1 -1)`                                                                          | `{"type": "Point", "coordinates": [1, -1]}`                                                                                          |
| LINESTRING      | A simple linestring specified by two or more distinct POINTs.                                                    | `LINESTRING(0 0, 1 1, 1 -1, 3 4)`                                                      | `{"type": "LineString", "coordinates": [[0, 0], [1, 1], [1, -1], [3, 4]]}`                                                           |
| POLYGON         | A set of closed LINESTRINGs. A polygon must have exactly one exterior ring and can have one or more inner rings. | `POLYGON(`<p>`((35 10, 45 45, 15 40, 10 20, 35 10), (20 30, 35 35, 30 20, 20 30))`</p> | `{"type": "Polygon", "coordinates": [[[35, 10], [45, 45], [15, 40], [10, 20], [35, 10]], [[20, 30], [35, 35], [30, 20], [20, 30]]]}` |
| MULTIPOINT      | A set of POINTs.                                                                                                 | `MULTIPOINT(-1 1, 0 0, 2 3)`                                                           |                                                                                                                                      |
| MULTILINESTRING | A set of LINESTRINGs.                                                                                            | `MULTILINESTRING((0 0,0 1,1 1), (-1 1,-1 -1))`                                         |                                                                                                                                      |
| MULTIPOLYGON    | A set of POLYGONs.                                                                                               | `MULTIPOLYGON(((2.25 0,1.25 1,1.25 -1,2.25 0)),((1 -1,1 1,0 0,1 -1)))`                 |                                                                                                                                      |
{/* vale on */}
### 3D

The Geospatial service supports the following vector types, located in a 3-dimensional space, each as an X, Y, and Z coordinate. The GeoJSON notation don't support the following vector types:
{/* vale off */}
| Type            | Description                                                                                                      | Example WKT                                                                                      |
| --------------- | ---------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| POINTZ          | A simple point.                                                                                                  | `POINTZ(1 -1 1)`                                                                                 |
| LINESTRINGZ     | A linestring in 2D specified by two or more distinct POINTZs                                                     | `LINESTRINGZ(0 0 2, 1 1 3, 1 -1 4, 3 4 5)`                                                       |
| POLYGONZ        | A set of closed LINESTRINGZ. A polygon must have exactly one exterior ring and can have one or more inner rings. | `POLYGONZ(((35 10 3, 45 45 4, 15 40 4, 10 20 5, 35 10 3), (20 30 3, 35 35 4, 30 20 5, 20 30 3))` |
| MULTIPOINTZ     | A set of POINTZs.                                                                                                | `MULTIPOINTZ(-1 1 1, 0 0 3, 2 3 2)`                                                              |
| MULTILINESTRING | A set of LINESTRINGZs.                                                                                           | `MULTILINESTRINGZ((0 0 1, 0 1 2, 1 1 3), (-1 1 2, -1 -1 2))`                                     |
| MULTIPOLYGON    | A set of POLYGONZs.                                                                                              | `MULTIPOLYGONZ(((2.25 0,1.25 1,1.25 -1,2.25 0)),((1 -1,1 1,0 0,1 -1)))`                          |
{/* vale on */}
The fact that a geometry has a Z coordinate doesn't make it a volumetric geometry. It remains a planar 2D geometry described in a 3D space.

### 2D + measurement / time

The Geospatial service supports the following vector types, located in a 2-dimensional space plus an extra measure, each as an X, Y, and M coordinates. The GeoJSON notation doesn't support the below vector types:
{/* vale off */}
| Type             | Description                                                                                                       | Example WKT                                                                                      |
| ---------------- | ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| POINTM           | A simple point.                                                                                                   | `POINTM(1 -1 1122)`                                                                              |
| LINESTRINGM      | A linestring in 2D specified by two or more distinct POINTMs.                                                     | `LINESTRINGM(0 0 2, 1 1 3, 1 -1 4, 3 4 5)`                                                       |
| POLYGONM         | A set of closed LINESTRINGMs. A polygon must have exactly one exterior ring and can have one or more inner rings. | `POLYGONM(((35 10 3, 45 45 4, 15 40 4, 10 20 5, 35 10 3), (20 30 3, 35 35 4, 30 20 5, 20 30 3))` |
| MULTIPOINTM      | A set of points.                                                                                                  | `MULTIPOINTM(-1 1 1, 0 0 3, 2 3 2)`                                                              |
| MULTILINESTRINGM | A set of LINESTRINGMs.                                                                                            | `MULTILINESTRINGM((0 0 1, 0 1 2, 1 1 3), (-1 1 2, -1 -1 2))`                                     |
| MULTIPOLYGONM    | A set of POLYGONMs.                                                                                               | `MULTIPOLYGONM(((2.25 0,1.25 1,1.25 -1,2.25 0)),((1 -1,1 1,0 0,1 -1)))`                          |
{/* vale on */}
The M coordinate is convenient to associate a measure with a point in space. Without the M coordinate, you'd require an extra property (of array type, not supported by the service) to store the measurement. The M coordinate doesn't require a spatial interpretation, and its unit isn't related to the X, Y, and Z coordinates. The M coordinate remains unchanged when the geometry gets transformed.

### 3D + measurement / time

The Geospatial service supports the following vector types, located in a 3-dimensional space plus an extra measure, each as an X, Y, Z, and M coordinates. The GeoJSON notation doesn't support the following vector types:
{/* vale off */}
| Type              | Description                                                                                                        | Example WKT                                                                                                                           |
| ----------------- | ------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------- |
| POINTZM           | A simple point.                                                                                                    | `POINTZM(1 -1 11 22)`                                                                                                                 |
| LINESTRINGZM      | A linestring in 3D specified by two or more distinct POINTZMs.                                                     | `LINESTRINGZM(0 0 2 233, 1 1 3 566, 1 -1 4 788, 3 4 5 988)`                                                                           |
| POLYGONZM         | A set of closed LINESTRINGZMs. A polygon must have exactly one exterior ring and can have one or more inner rings. | `POLYGONZM(((35 10 3 122, 45 45 4 211, 15 40 4 344, 10 20 5 566, 35 10 3 544), (20 30 3 233, 35 35 4 455, 30 20 5 677, 20 30 3 899))` |
| MULTIPOINTZM      | A set of POINTZMs.                                                                                                 | `MULTIPOINTZM(-1 1 1 0, 0 0 3 0, 2 3 2 0)`                                                                                            |
| MULTILINESTRINGZM | A set of LINESTRINGZMs.                                                                                            | `MULTILINESTRINGZM((0 0 1 1, 0 1 2 2, 1 1 3 3), (-1 1 2 1, -1 -1 2 2))`                                                               |
| MULTIPOLYGONZM    | A set of POLYGONZMs.                                                                                               | `MULTIPOLYGONZM(((2.25 0 1 1,1.25 1 1 1,1.25 -1 1 1,2.25 0 1 1)),((1 -1 1 1,1 1 1 1,0 0 1 1,1 -1 1 1)))`                              |
{/* vale on */}
### Geometry collections

The Geospatial service finally supports collections of the earlier vector types. It's a collection of heterogeneous vector types. The GeoJSON notation doesn't support the below vector types:

| Type                 | Description                                       | Example WKT                                                                                                                                                                                                                                                                                  |
| -------------------- | ------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| GEOMETRYCOLLECTION   | A collection of 2D geometries.                    | `GEOMETRYCOLLECTION(MULTIPOINT(-1 1, 0 0, 2 3), MULTILINESTRING((0 0, 0 1, 1 1),(-1 1, -1 -1)),POLYGON((-0.25 -1.25, -0.25 1.25, 2.5 1.25, 2.5 -1.25, -0.25 -1.25)))`                                                                                                                        |
| GEOMETRYCOLLECTIONZ  | A collection of 3D geometries.                    | `GEOMETRYCOLLECTION Z (MULTIPOINT Z(-1 1 4, 0 0 2, 2 3 2), MULTILINESTRING Z ((0 0 1, 0 1 2, 1 1 3), (-1 1 1, -1 -1 2)), POLYGON Z(`<p>`(-0.25 -1.25 1, -0.25 1.25 2, 2.5 1.25 3, 2.5 -1.25 1, -0.25 -1.25 1),(2.25 0 2, 1.25 1 1, 1.25 -1 1, 2.25 0 2),(1 -1 2, 1 1 2, 0 0 2,1 -1 2)))`</p> |
| GEOMETRYCOLLECTIONM  | A collection of 2D geometries with a measurement. | `GEOMETRYCOLLECTION M (MULTIPOINT Z(-1 1 4, 0 0 2, 2 3 2), MULTILINESTRING M ((0 0 1, 0 1 2, 1 1 3), (-1 1 1, -1 -1 2)), POLYGON M(`<p>`(-0.25 -1.25 1, -0.25 1.25 2, 2.5 1.25 3, 2.5 -1.25 1, -0.25 -1.25 1),(2.25 0 2, 1.25 1 1, 1.25 -1 1, 2.25 0 2),(1 -1 2, 1 1 2, 0 0 2,1 -1 2)))`</p> |
| GEOMETRYCOLLECTIONZM | A collection of 3D geometries with a measurement. | `GEOMETRYCOLLECTION ZM (MULTIPOINT ZM(-1 1 4 5, 0 0 2 5, 2 3 2 5))`                                                                                                                                                                                                                          |
{/* vale off */}
### Simple types

In addition to spatial property types, the following versatile types are supported:

| Type        | Description                                         | Example                 |
| ----------- | --------------------------------------------------- | ----------------------- |
| String      | A character string.                                 | â€œPowered by geospatialâ€ |
| Long        | A 64-bit signed integer.                            | 1234567                 |
| Double      | A 64-bit double precision floating point number.    | 12345.6789              |
| Boolean     | True or False.                                      | true                    |
| Timestamp   | Unix timestamp, number of milliseconds since epoch. | 1637764395456           |
| TimestampTz | ISO-8601 timestamp                                  | 2022-08-30T07:28:24.91Z |

{/* vale on */}

### More on properties

By default, the feature type properties are non-null. it's possible to define optional properties simply by adding an _optional_ field with true value in the property definition. In addition to this, it's possible to add a note on a property using the _description_ field.

```json
...
{
    ...
    "properties": {
        "temperature": {
            "type": "DOUBLE",
            "optional": true,
            "description": "air temperature in Celsius"
        },
    },
    ...
}
...
```

It's legal to define several geometry properties, each one with its SRID constraint.

You can use it to store different levels of details for a given feature type, or store geometries of varying nature:

```json
 ...
{
    "externalId": "multiple_geometries",
    "properties": {
        "centerOfGravity": {
            "type": "POINT",
            "srid": 4326
        },
        "centroid": {
            "type": "POINT",
            "srid": 4326
        },
        "shape": {
            "type": "POLYGON",
            "srid": 4326
        },
        "shapeOrig": {
            "type": "POLYGON",
            "srid": 2001
        }
    }
}
  ...
```

Defining a feature type with geometry properties without SRID constraints is also legal. In this case, the server lets mix geometries located in different CRSs. The drawback is that the property can't be indexed and used in search constraints; it can store and retrieve the geometry. When a modeling choice is made, the common practice is to project the geometries and keep these copies in another geometry property with a fixed CRS, making it possible to apply search constraints on that extra geometry property.

```json
...
{
    "externalId": "multiple_geometries",
    "properties": {
        ...
        "trajectoryOrig": {
            "type": "LINESTRING"
        },
        "trajectory": {
            "type": "LINESTRING",
            "srid": 4326
        },
        ...
    }
    ...
}
...
```

## Searching for features

It's possible to retrieve a feature if you know its external id. But the typical use case with geospatial data is searching for data using a geospatial filter. The following operators are currently supported:

- stWithin
- stWithinProperly
- stIntersects
- stContains
- stContainsProperly
- stWithinDistance
- stIntersects3d
- stWithinDistance3d

The meaning of those operators follows the [OpenGIS specification](https://www.ogc.org/standards/sfs).

A filter expression example:

```json
...
{
    "stWithin": {
        "property": "location",
        "value": {
            "wkt": "POLYGON((60.547602 -5.423433, 60.547602 -6.474416, 60.585858 -6.474416, 60.585858 -5.423433, 60.547602 -5.423433))"
        }
    }
}
...
```

A filter expression can combine several property constraints:

```json
...
{
    "and": [
        {
            "range": {
                "property": "temperature",
                "gt": 3.14
            }
        },
        {
            "not": {
                "stWithin": {
                    "property": "location",
                    "value": {
                        "wkt": "POLYGON((60.547602 -5.423433, 60.547602 -6.474416, 60.585858 -6.474416, 60.585858 -5.423433, 60.547602 -5.423433))"
                    }
                }
            }
        }
    ]
}
...
```

The spatial filtering isn't required:

```json
...
{
    "and": [
        {
            "range": {
                "property": "temperature",
                "gt": 3.14
            }
        },
        {
            "range": {
                "property": "pressure",
                "lt": 129.4
            }
        }
    ]
}
...
```

The filter specification for feature search offers arbitrary complexity and depth:

```json
...
{
    "or": [
        {
            "and": [
                {
                    "range": {
                        "property": "attr1",
                        "gt": 3.14
                    }
                },
                {
                    "range": {
                        "property": "attr1",
                         "lt": 5.03
                    }
                }
            ]
        },
        {
            "not": {
                "range": {
                    "property": "attr2",
                    "lt": 6.28
                }
            }
        }
    ]
}
...
```

You can also filter features by linked asset IDs. The following filter lets you search for features linked to the asset IDs you specify.

```json
...
{
    "containsAny": {
        "property": "assetIds",
        "value": [1, 2, 3, 4]
    }
}
...
```

The full filter language specification is available in [the API specification](https://api-docs.cognite.com/20230101/#tag/Geospatial).

## Paginate features

The Geospatial service supports cursors to paginate through the features of a feature type. The `nextCursor` property of the response returns the cursor value.

```json
{
    "items": {
        ...
    },
    "nextCursor": "3zZmOn50qL9Kkhjwmrz602sHfQifGypzdqYEtQG3ajuU"
}

```

To retrieve the next page of features, pass the same request with the `cursor` field changed to the value returned in `nextCursor` as in the example below.

`https://...cognitedata.com/api/v1/projects/.../geospatial/featuretypes/.../features/list`

```json
{
    "filter": {
        ...
    },
    "cursor": "3zZmOn50qL9Kkhjwmrz602sHfQifGypzdqYEtQG3ajuU"
}
```

## Spatial filter operators

1. **stIntersects**

   This is a 2D operator.
   Geometry A intersects geometry B if and only if there are points belonging to both A and B. In particular, any geometry intersects itself.

   The green geometry intersects the orange geometry in each of the examples below.

   <img  src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/resource_types/intersects.svg" alt=" " width="60%"/>


2. **stWithin**

   This is a 2D operator.
   Geometry A is within geometry B if and only if all the points of A also belong to B. In particular, every geometry is within itself. In each of the examples below, the green geometry is within the orange geometry. In each of the examples below, the green geometry isn't within the orange geometry. {/* How's this possible? Is it within or not within the geometry? */}

   <img  src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/resource_types/within.svg" alt=" " width="60%"/>

3. **stWithinProperly**

   This is a 2D operator.
   Geometry A is completely within geometry B if and only if all the points of A lie in the interior of B. Geometry A is completely within geometry B if and only if A is within B and the intersection of A and the boundary of B is empty. Any geometry A isn't completely within itself.

   <img  src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/resource_types/completelyWithin.svg" alt=" " width="60%"/>

4. **stContains**

   This is a 2D operator.
   The relation "contains" is an opposite relation to "within": geometry A contains geometry B if and only if B is within A.

5. **stContainsProperly**

   This is a 2D operator.
   The relation "contains properly" is the opposite relation to "within properly": geometry A properly contains geometry B if and only if B is properly within A.

6. **stWithinDistance**

   This is a 2D operator.
   Geometry A is within the distance of geometry B if and only if there is a pair of points _a_ in A and _b_ in B and the distance between _a_ and _b_ is less than or equal to the given distance. The distance is specified in units defined by the coordinate reference system (CRS).
   In the example below, the geometries are within distance d from each other.

   <img  src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/resource_types/withinDistance.svg" alt=" " width="60%"/>

7. **stIntersects3d**

   This is a 3D operator.
   Geometry A intersects geometry B if and only if there are points belonging to both A and B. In particular, any geometry intersects itself.

8. **stWithinDistance3d**

   This is a 3D operator.
   Geometry A is within the distance of geometry B if and only if there is a pair of points _a_ in A and _b_ in B and the distance between _a_ and _b_ is less than or equal to the given distance. The distance is specified in units defined by the coordinate reference system (CRS).

## Indexes

By default, features index their `externalId`, `createdTime`, and `lastUpdatedTime`.

Several features in a given feature type impact the filtering features. Associating an index with a property allows for more efficient filtering when the property is present in the filter expression. However, this isn't a guarantee since it will depend on the index's selectivity, which is data-dependent.

Example of specifying an index on the point2d property:

```json
...
{
    ...
    "properties": {
        ...
        "point2d": {
            "type": "POINT",
            "srid": 4326
        },
        ...
    },
    "searchSpec": {
        ...
        "point2d_idx": {
              "properties": [ "point2d" ]
        },
        ...
    }
}
...
```

indexes can span many properties, possibly of different types:

```json
...
{
    ...
    "properties": {
        ...
        "point2d": {
            "type": "POINT",
            "srid": 4326
        },
        "temperature": {
          "type": "DOUBLE"
        },
        ...
    },
    "searchSpec": {
        "point2d_temperature_idx": {
            "properties": [ "point2d", "temperature" ]
        }
    }
}
...
```

## Coordinate Reference Systems

A Coordinate Reference System (CRS) defines a 2D or 3D reference somewhere on or above/below the Earth's surface. Coordinates are usually defined in East/North/Height order, but this can vary from one CRS to the next. Most CRSs are only valid over a limited part of the globe, so for global mapping purposes, it's common to use GPS latitude/longitude values  defined by WGS84 and encoded as the EPSG/SRID (Spatial Reference ID) number 4326.

### Predefined CRSs

CDF currently supports about 8500 well known CRSs, all defined by [European Petroleum Survey Group (EPSG)](https://epsg.io).

### Custom CRS

If a user needs an EPSG entry that's currently missing, you can add it as a custom CRS.

Any API user can create a custom CRS; there will be a limit on how many such definitions each customer can make. A custom CRS is defined by a reference number (a positive integer), by a [wkt (Well Known Text)](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) string, and by a projString. The projString defines the geometrical projection needed to convert coordinates in this CRS into one of the standard CRSs. The projString needs to be in the format defined by the industry-standard [PROJ](https://proj.org) library ([https://proj.org/](https://proj.org/usage/quickstart.html)).

For example, the string `+proj=ortho +lat_0=59.123456 +lon_0=4.7654321 +x_0=0 +y_0=0 +ellps=intl` defines an orthogonal 3D CRS located at a point in the North Sea, oriented so that geographic east is the x-direction, y is north, and z points up. The ellipsoid used (International 1924) means that the lat/long values are according to that older standard, resulting in an offset (in this particular region) of about 30 m east-west from the GPS coordinate with the same numeric latitude/longitude values.

### Enable CRS transformations

By default, when working with a feature type defining a geometry property with specified CRS, for example, the SRID field is set, you can't use a different CRS when you create or update features or search or aggregate with geometry filters on that property.

Change this behavior by setting the **allowCrsTransformation** flag to true. The API will then transform the input geometry coordinates from the input geometry CRS into the feature type property CRS.

Consider the feature type extract below.

`https://...cognitedata.com/api/v1/projects/.../geospatial/featuretypes`

```json
...
{
    ...
    "properties": {
        ...
        "geom": {
            "type": "POINT",
            "srid": 4326
        },
        ...
    },
    ...
}
...
```

In the following example, the input geometry given in EPSG:3857 will be converted into EPSG:4326.

`https://...cognitedata.com/api/v1/projects/.../geospatial/featuretypes/.../features`

```json
{
    "items": [
        {
            ...
            "geom": {
                "wkt": "POINT(261443.300621 6249935.722823)",
                "srid": 3857
            }
            ...
        }
    ],
    "allowCrsTransformation": "true"
}
```

With **allowCrsTransformation** set to **false** or unspecified, the API returns an HTTP error code 400.

Set the same **allowCrsTransformation** option to **true**, when defining a spatial filter for search/aggregation as in the example below.

`https://...cognitedata.com/api/v1/projects/.../geospatial/featuretypes/.../features/search`

```json
{
    "filter": {
        "stWithin": {
            "property": "geom",
            "value": {
                "wkt": "POLYGON((261443 6249935, 261444 6249935, 261444 6249936, 261443 6249936, 261443 6249935))',
                "srid": 3857
            }
        }
    },
    "allowCrsTransformation": "true"
}
```

Here again, with **allowCrsTransformation** set to **false** or unspecified, the API returns an HTTP error code 400.

### Geometry properties without a CRS specified

Consider a feature type with a geometry property `geomOriginal` without a specified CRS, for example, the SRID field isn't set. The different features in this feature type will have `geomOriginal` geometries in different CRS (EPSG:4326, EPSG:3857, EPSG:4230, etc.).

The API doesn't allow spatial filtering on that `geomOriginal` property since it would require transforming all the geometries in a common CRS before the filtering can be applied. In addition to the performance implications, the choice of such a common CRS depends on the use case. Therefore, the API can't decide which one to use.

In addition, any CRS conversion implies a potential loss of precision and possibly some distortion. You should retain geometries in their original CRS. Concretely, you will ingest the geometries twice, once in the original CRS, once in the specified common CRS of your choice.

Back to the example above with `geomOriginal`, we create an additional geometry property with a specified CRS, here EPSG:4326, for example, `geom4326`. The property complements the initial `geomOriginal` one, and will allow spatial filtering.

The feature type creation looks the following way.

`https://...cognitedata.com/api/v1/projects/.../geospatial/featuretypes`

```json
...
{
    ...
    "properties": {
        ...
        "geomOriginal": {
          "type": "POINT"
        },
        "geom4326": {
            "type": "POINT",
            "srid": 4326
        },
        ...
    },
    ...
}
...
```

As mentioned earlier, to enable the transformation of the input geometry into a geometry in the EPSG:4326 CRS, set the **allowCrsTransformation** flag to **true** when creating or updating features.

`https://...cognitedata.com/api/v1/projects/.../geospatial/featuretypes/.../features`

```json
{
    "items": [
        {
            ...
            "geomOriginal": {
                "wkt": "POINT(261443.300621 6249935.722823)",
                "srid": 3857
            },
            "geom4326": {
                "wkt": "POINT(261443.300621 6249935.722823)",
                "srid": 3857
            }
            ...
        }
    ],
    "allowCrsTransformation": "true"
}
```

Spatial filtering on the `geom4326` column can then be achieved for search/aggregation as shown here.

`https://...cognitedata.com/api/v1/projects/.../geospatial/featuretypes/.../features/search`

```json
{
    "filter": {
        "stWithin": {
            "property": "geom4326",
            "value": {
                "wkt": "POLYGON((38 27, 39 27, 39 28, 38 28, 38 27))',
                "srid": 4326
            }
        }
    },
}
```

Again, with **allowCrsTransformation** set to **false** or unspecified, the API returns an HTTP error code 400.

## Raster data

{/* vale off */}

Rasters organize data into pixels or cells and can be split into rectangular tiles. The number of horizontal and vertical pixels defines the size of a raster tile. Each pixel is identified by a row and column in a tile and represents a position. A pixel is also a placeholder for data. The data elements rest in bands, also known as channels or dimensions. Rasters can be georeferenced, where each pixel corresponds to a defined area on earth, in a defined [Coordinate Reference System](/dev/concepts/resource_types/geospatial#coordinate-reference-systems).

An example raster image from Global Wind Atlas

<Frame>
<img class="illustration" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/resource_types/raster.png" alt="Global Wind Atlas raster image example" width="100%"/>
</Frame>


Typical metadata associated with a raster:

- upperleftx: the upper left X coordinate of the raster.
- upperlefty: the upper left Y coordinate of the raster.
- width: the width of the raster in pixels.
- height: the height of the raster in pixels.
- scalex: the X component of the pixel width in the coordinate reference system units.
- scaley: the Y component of the pixel height in the coordinate reference system units.
- skewx: the georeference X skew.
- skewy: the georeference Y skew.
- srid: the spatial reference identifier of the raster.
- numbands: the number of bands in the raster object.
{/* vale on */}
You can store raster data in a database (embedded) or outside a database, for instance as a CDF File or using cloud storage. If the data is stored outside a database, the raster field has metadata defining the width, the height, the geometric bounding box, and a reference to the file and the file region that the raster tile references.

The geospatial endpoint supports georeferenced rasters.

The supported raster data format is defined by the GDAL ([documentation](https://gdal.org)) library.

### Raster properties

A CDF Geospatial feature type can have zero, one, or many raster properties.

<Note>
A feature type raster property must be optional and require a fixed SRID.
</Note>

You can set the storage type for the raster to:

- embedded: the raster will be stored in the database
- file: point to a cloud storage file for the raster (currently not supported)

```json
{
    "externalId": "surveys",
    "properties": {
        ...
        "bathymetry": {
            "description": "the bathymetry rasters",
            "type": "raster",
            "storage": "embedded",
            "formats": [ "XYZ" ],
            "srid": "32631",
            "optional": "true"
        },
        ...
    }
}
```

### Ingesting raster

Due to the size (typically at least megabytes) and nature of the data (often binary), the raster data input and output are served from a different endpoint.

#### Embedded storage

To insert raster data:

1. Insert the feature without raster information.

    ```json
    POST /geospatial/featuretypes/<id>/features
    {
    "items": [
        {
        "externalId": "some_survey",
        ... <regular properties> ...,
        }
    ]
    }
    ```

2. Push the raster RAW data.

```bash
PUT /geospatial/featuretypes/<id>/features/<id>/rasters/<id>?format=XYZ&srid=4326
<raster data>
```

<Note>
Depending on the GDAL driver used to read GDAL input content, there is potential precision loss when ingesting the raster. For example, ASCII Gridded XYZ driver only supports a single precision floating point. When getting XYZ raster, SIGNIFICANT_DIGITS option can be used to specify the number of significant digits of the output raster.
</Note>

## Computation

The geospatial API provides a `/compute` endpoint to run arbitrary complex geospatial computations. Currently, the endpoint only supports direct geometry coordinate transformations.

The pseudo JSON structure of a `/compute` request looks like this:

```json
{
  "output": {
    "<output-property-name-1>": {
      "<function-name>": {
        "function-argument-1": ...,
        "function-argument-2": ...
      }
    },
    "<output-property-name-2>": {
        ...
    },
    ...
  }
}
```

Where:

- `<output-property-name-...>` represents the name of an output JSON property chosen by the client making the request.
- `<function-name>` is the name of a registered function for the `/compute` endpoint. A function might have one or many arguments, represented as `<function-argument-...>`.

The pseudo JSON structure of a `/compute` response looks like this:

```json
[
    {
        "<output-property-name-1>": ...,
        "<output-property-name-2>": ...,
        ...
    }
]
```

### Coordinate transformation

An example of a direct geometry transformation:

```json
POST /geospatial/compute
{
  "output": {
    "value": {
      "stTransform": {
        "geometry": {
          "ewkt": "SRID=4326;POINT(2.353295 48.850908)"
        },
        "srid": 23031
      }
    }
  }
}
```

The response to the query will have this structure:

```json
[
  {
    "from4326to23031": {
      "wkt": "POINT(452650.3970115797 5411291.746826155)",
      "srid": 23031
    }
  }
]
```


<!-- SOURCE_END: docs.cognite.com/docs\dev\concepts\resource_types\geospatial.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\concepts\resource_types\index.mdx -->
## File: docs.cognite.com/docs\dev\concepts\resource_types\index.mdx

---
title: 'About resource types'
description: 'Learn about the different resource types in CDF including assets, time series, events, files, 3D models, sequences, labels, and more.'
content-type: 'map'
audience: [developer, dataEngineer, businessUser]
experience-level: 100
article-type: 'map'
lifecycle: use
---
A resource is an individual data entity with a unique identifier. This article introduces the different **resource types** you can interact with using the Cognite API.

## Assets

The [assets](https://api-docs.cognite.com/20230101/#tag/Assets) resource type stores **digital representations** of objects or groups of **objects from the physical world**. Assets are organized in **hierarchies**. For example, a **water pump** asset can be part of a **subsystem** asset on an **oil platform** asset.

Assets **connect related data from different sources** and are core to identifying all the data relevant to an entity (**contextualization**) in Cognite Data Fusion. All other resource types, for example, time series, events, and files should be connected to **at least one** asset, and each asset can be connected to many resources and resource types.

[Learn more](/dev/concepts/resource_types/assets) about assets.

## Time series

The [time series](https://api-docs.cognite.com/20230101/#tag/Time-series) resource type stores a series of **data points** in **time order**. Examples of a time series are the temperature of a water pump asset, the monthly precipitation in a location, and the daily average number of manufacturing defects.

[Learn more](/dev/concepts/resource_types/timeseries) about time series.

## Events

The [events](https://api-docs.cognite.com/20230101/#tag/Events) resource type stores information that happens over **a period**. Events have a **start time** and an **end time** and can be related to **multiple assets**. For example, an event can describe two hours of maintenance on a water pump and associated pipes or a future period for when the pump is scheduled for inspection.

[Learn more](/dev/concepts/resource_types/events) about events.

## Files

The [files](https://api-docs.cognite.com/20230101/#tag/Files) resource type stores documents that contain information related to **one or more assets**. For example, a file can contain a P&ID diagram that shows how many assets are connected.

[Learn more](/dev/concepts/resource_types/files) about files.

## 3D models

The [3D models](https://api-docs.cognite.com/20230101/#tag/3D-Models/operation/create3DModels) resource type stores files that offer **visual** and **geometrical data** and **context** to assets. For example, you can connect a pump asset with a 3D model of the plant floor where it's placed. Seeing asset data rendered in 3D helps you find the sensor data you are interested in faster.

[Learn more](/dev/concepts/resource_types/3dmodels) about 3D models.

## Sequences

The [Sequences](https://api-docs.cognite.com/20230101/#tag/Sequences) resource type stores a series of rows indexed by row number. Each row has one or more columns with either string or numeric data. Examples of sequences are performance curves and various types of logs, for example, depth logs in drilling.

[Learn more](/dev/concepts/resource_types/sequences) about sequences.

## Labels

With [labels](https://api-docs.cognite.com/20230101/#tag/Labels), you can create a predefined set of **managed terms** that you can use to annotate and group **assets**. You can organize the labels in a way that makes sense in your business and use them to make it easier to find what you want.

For example, you can create a label called _pump_ and apply it to all asset resources that represent pumps and then filter assets to see only pumps.

[Learn more](/dev/concepts/resource_types/labels) about labels.

## Relationships

The [Relationships](https://api-docs.cognite.com/20230101/#tag/Relationships) resource type represents connections between resource objects in Cognite Data Fusion (CDF). Each relationship is between a source and a target object and is defined by a **relationship type** and the **external IDs** and **resource types** of the source and target objects. Optionally, a relationship can be time-constrained with a start and end time.

{/* What can I use it for? */}

[Learn more](/dev/concepts/resource_types/relationships) about relationships.

## RAW

The [RAW](https://api-docs.cognite.com/20230101/#tag/Raw) resource type is the staging area in CDF. You can ingest data in its original form into RAW resource type to reduce source system queries for the same data for different use cases and minimize the data extractors' logic. This makes it easy to re-run transformations on data in the cloud.

[Learn more](/dev/concepts/resource_types/raw) about CDF RAW.

## Entity matching

Different sources of industrial data can use different naming standards when they refer to the same entity. With [Entity matching](https://api-docs.cognite.com/20230101/#tag/Entity-matching), you can match entities like time series, files, and sequences from different source systems to assets. With the mapped information, you can build applications where users, for example, can click a component in a 3D model to see all the related time series data or ask for all the pressure readings along a flow line.

[Learn more](/dev/concepts/resource_types/entity_matching) about entity matching.

## Geospatial

The [Geospatial](https://api-docs.cognite.com/20230101/#tag/Geospatial) resource type represents data with a geospatial nature. This data, called _features_ in Geographic Information System (GIS) terminology, are grouped into _feature types_ that share several properties, such as name, type, and value, and have more than one spatial representation stored in configurable properties. Feature types are user-defined, and the CDF geospatial resource type doesn't come with pre-configured schemas to enable flexibility for modeling the client domain.

[Learn more](/dev/concepts/resource_types/geospatial) about geospatial data.



<!-- SOURCE_END: docs.cognite.com/docs\dev\concepts\resource_types\index.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\concepts\resource_types\labels.mdx -->
## File: docs.cognite.com/docs\dev\concepts\resource_types\labels.mdx

---
title: 'Labels'
description: 'Learn how to use labels to create managed terms for annotating and grouping assets, files, and relationships in CDF.'
content-type: 'concept'
audience: [developer, dataEngineer]
experience-level: 200
article-type: 'article'
lifecycle: use
---
{/*What are Labels?  Generic overview information*/}

With **labels** you can create a predefined set of **managed terms** that you can use to annotate and group **assets**, **files**, and **relationships**.

You can organize the labels in a way that makes sense in your business and use the labels to make it easier to find what you want. For example, you can create a label called _pump_ and apply it to all asset resources that represent pumps, and then filter assets to see only pumps.

You can also use labels to define and manage the available **types** for [relationship](/dev/concepts/resource_types/relationships) resources. For example, you can define labels like these and use them as relationship types:

- `flowsTo` - to describe the flow between assets.
- `belongsTo` - to describe that a file resource belongs to a particular asset resource.
- `isParentOf` - to build a hierarchy of assets.
- `implements` - to describe that a physical item implements a functional asset at a specific point in time.

## About labels

{/*What can I do with Labels?  */}

Each asset/file can have up to ten labels connected to it. You can filter assets/files by their labels to find a group of assets/files.

Typical use cases for labels include:

- Categorize assets into equipment types.
- Locate assets by equipment type.
- Define [relationship](/dev/concepts/resource_types/relationships) types.
- Assign a document type label to files.

<Tip>
See the [labels API documentation](https://api-docs.cognite.com/20230101/#tag/Labels) for more information about how to work with labels.
</Tip>

{/*Add examples to support and illustrate the concepts above */}

## Create a label

To create a label, you need to give it an ID and a name. The label ID must be an [`externalId`](/dev/concepts/external_id).

This example request creates a label:

```http wrap
POST /api/v1/projects/publicdata/labels
Content-Type: application/json

{
  "items": [
    {
      "externalId": "rotating-equipment",
      "name": "Rotating equipment",
      "description": "Pumps, compressors, turbines"
    },
    {
      "externalId": "PUMP",
      "name": "Pump",
      "description": "Pumps"
    }
  ]
}
```

## List labels

To list existing labels, use:

```http wrap
POST /api/v1/projects/publicdata/labels/list
Content-Type: application/json

{
  "filter": {
    "name": "string",
    "externalIdPrefix": "string"
  },
  "limit": 10,
  "cursor": "ABC"
}
```

## Attach a label when you create a resource

To attach a label when you create a resource, you reference the labels through the [`externalId`](/dev/concepts/external_id) of the label.

For example, to create a new asset resource and attach the labels `pump` and `rotating-equipment`, use this request:

```http wrap
POST /api/v1/projects/publicdata/assets
Content-Type: application/json

{
  "items": [
    {
      "externalId": "MY_PUMP_ASSET",
      "labels": [
        { "externalId": "PUMP" },
        { "externalId": "rotating-equipment" }
      ]
    }
  ]
}
```

<Note>
You can attach a maximum of 10 labels to each resource.
</Note>

## Attach or remove labels to an existing resource

To attach (or remove) labels to an existing resource, you reference the labels through the [`externalId`](/dev/concepts/external_id) of the label when you update the resource.

For example, to attach the label `pump` and remove label `rotating-equipment` to an existing asset, use the request:

```http wrap
POST /api/v1/projects/publicdata/assets/update
Content-Type: application/json

{
  "items": [
    {
      "update": {
        "labels": {
          "add": [ { "externalId": "PUMP" } ],
          "remove": [ { "externalId": "rotating-equipment" } ]
        }
      },
      "externalId": "MY_PUMP_ASSET"
    }
  ]
}
```

The API ignores the request if you try to attach a label that's already attached to the resource.

<Note>
You can attach a maximum of 10 labels to each resource.
</Note>

## Remove labels from a resource

To remove labels from a resource, you reference the labels through the [`externalId`](/dev/concepts/external_id) of the label when you update the resource.

For example, to remove the label `rotating-equipment` from an asset, use the request:

```http wrap
POST /api/v1/projects/publicdata/assets/update
Content-Type: application/json

{
  "items": [
    {
      "externalId": "MY_PUMP_ASSET",
      "update": {
        "labels": {
          "remove": [
            { "externalId": "rotating-equipment" }
          ]
        }
      }
    }
  ]
}
```

The API ignores the request if you try to remove a label that's not attached to the resource.

## Filter resources by labels

To filter assets by a label, you reference the label through the [`externalId`](/dev/concepts/external_id) of the label when you filter the assets.

For example, to retrieve only assets that have the label `rotating-equipment`, use the request:

```http wrap
POST /api/v1/projects/publicdata/assets/list
Content-Type: application/json

 {
  "filter": {
    "labels": {
      "containsAny": [{ "externalId": "rotating-equipment" }]
    }
  }
}
```

To retrieve only assets that have the label `rotating-equipment` **or** `pump`:

```http wrap
POST /api/v1/projects/publicdata/assets/list
Content-Type: application/json

  "filter": {
    "labels": {
      "containsAny" : [
        { "externalId": "rotating-equipment" },
        { "externalId": "PUMP" }
      ]
    }
  }
}
```

To retrieve only assets that have the labels `rotating-equipment` **and** `pump`:

```http wrap
POST /api/v1/projects/publicdata/assets/list
Content-Type: application/json

  "filter": {
    "labels": {
      "containsAll" : [
        { "externalId": "rotating-equipment" },
        { "externalId": "PUMP" }
      ]
    }
  }
}
```

## Delete labels

To delete a label, you need to specify the [`externalId`](/dev/concepts/external_id) of the label to delete.

This example request deletes a `rotating-equipment` label:

```http wrap
POST /api/v1/projects/publicdata/labels/delete
Content-Type: application/json
{
  "items": [
    {
      "externalId": "rotating-equipment"
    }
  ]
}
```


<!-- SOURCE_END: docs.cognite.com/docs\dev\concepts\resource_types\labels.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\concepts\resource_types\raw.mdx -->
## File: docs.cognite.com/docs\dev\concepts\resource_types\raw.mdx

---
title: 'RAW'
description: 'Learn how to use CDF RAW as a staging area to store unstructured data in its original form before transforming it into the CDF data model.'
content-type: 'concept'
audience: [developer, dataEngineer]
experience-level: 100
article-type: 'article'
lifecycle: use
---
In Cognite Data Fusion (CDF), the RAW resource type stores unstructured data. The RAW database and tables hold the source data in its _original form_ to reduce source system queries for the same data for different use cases and minimize the data extractors' logic. This makes it easy to re-run transformations on data in the cloud.

Alternatively, you can transform the data in your cloud and bypass CDF RAW to integrate the data directly into the CDF data model.

## About RAW data

Use the RAW data to spot anomalies in your tabular data or identify which transformations you need to do on your data before ingesting it into the CDF data model. Navigate to [Manage staged data](/cdf/integration/guides/extraction/raw_explorer) in the CDF portal application to view the ingested tabular data in a table or as a data profiling report in the **RAW explorer**.

A CDF project can have a variable number of RAW databases with a variable number of tables with a variable number of key-value objects. You can query the keys using the [RAW API](https://api-docs.cognite.com/20230101/#tag/Raw) and post a maximum of 1000 databases per request.

## Primary row key

When you insert rows in a RAW table, you must set a primary row key that only contains unique values. You can't change this key when it's set. In the **RAW explorer**, you can select **Generate a new key column** to generate a unique key per row.

If you're unsure which primary key to use and want to simulate different scenarios, upload the same file to different tables using separate tabs in your browser.

<Warning>
You may risk losing data if you use a non-unique column as the primary key.
</Warning>


<!-- SOURCE_END: docs.cognite.com/docs\dev\concepts\resource_types\raw.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\concepts\resource_types\relationships.mdx -->
## File: docs.cognite.com/docs\dev\concepts\resource_types\relationships.mdx

---
title: 'Relationships'
description: 'Learn how to use relationships to connect resource objects in CDF beyond the standard hierarchical asset structure.'
content-type: 'concept'
audience: [developer, dataEngineer]
experience-level: 200
article-type: 'article'
lifecycle: use
---
{/*What are Relationships?  Generic overview information*/}

The **Relationships** resource type represents connections between resource objects in Cognite Data Fusion (CDF). Each relationship is between a source and a target object and is defined by a **relationship type** and the **external IDs** and **resource types** of the source and target objects. Optionally, a relationship can be time-constrained with a start and end time.

The `externalId` field uniquely identifies each relationship.

To define and manage the available relationship types, use the [labels](/dev/concepts/resource_types/labels) resource type. For example, you can define and use the following labels as relationship types:

- `flowsTo` - to describe the flow between assets.
- `belongsTo` - to describe that a file resource belongs to a particular asset resource.
- `isParentOf` - to build a hierarchy of assets.
- `implements` - to describe that a physical item implements a functional asset at a specific point in time.

<Note>
These labels are informational only, and they're case-sensitive, for example, `flowsTo` and `FlowsTo` aren't the same.
</Note>

## About relationships

{/*What can I do with relationships?  */}

Typically, asset resources originate from a maintenance system, and the hierarchical structure of the maintenance system often defines how the asset resources are organized in Cognite Data Fusion (CDF).

The **relationships** resource type lets you organize the assets in other structures **in addition to** the standard hierarchical asset structure.

For example, you can organize the assets by their physical **location**, where the grouping nodes in the hierarchy are buildings and floors rather than systems and functions. Another example is to build a graph structure that lets you navigate assets by mimicking their physical **connections** through wires or pipes. 

Before you create a relationship, make sure that the relationship type exists as a [label](/dev/concepts/resource_types/labels). When you create a relationship, you must specify its type using the `externalId` of the relevant label.

When you create a relationship, it's good practice to add them to a data set for grouping and governance.

It's not a requirement that the source or target resource exist when you create a relationship. This lets you create relationships between objects that don't yet exist in CDF.

<Tip>
See the [relationships API documentation](https://api-docs.cognite.com/20230101/#tag/Relationships) for more information about how to work with relationships.
</Tip>

{/*Add examples to support and illustrate the concepts above */}

## Create a relationship between two assets

To create a relationship, you need to give it an ID and a type and specify the source and target resource objects the relationship connects. The relationship ID must be an [`externalId`](/dev/concepts/external_id). The relationship type must be the `externalId` of a [label](/dev/concepts/resource_types/labels).

Create relationships within a data set for logical grouping and governance.

This example request creates a relationship:

```http wrap
POST /api/v1/projects/publicdata/relationships
Host: api.cognitedata.com
Authorization: Bearer <token>
Content-Type: application/json

{
  "items": [
    {
      "externalId": "relationship_1",
      "dataSetId": 5514071318856557,
      "sourceExternalId": "asset_1",
      "sourceType": "asset",
      "targetExternalId": "asset_2",
      "targetType": "asset",
      "labels": [
        {
          "externalId": "flowsTo"
        }
      ]
    }
  ]
}
```

## Create a relationship between a file and an asset

You can use relationships to create links between any resources by specifying the `sourceType` and `targetType`.

This example shows how to create a relationship of type `belongsTo` between a file and an asset resource.

```http wrap
POST /api/v1/projects/publicdata/relationships
Host: api.cognitedata.com
Authorization: Bearer <token>
Content-Type: application/json

{
  "items": [
    {
      "externalId": "relationship_2",
      "dataSetId": 5514071318856557,
      "sourceExternalId": "file_1",
      "sourceType": "file",
      "targetExternalId": "asset_1",
      "targetType": "asset",
      "labels": [
        {
          "externalId": "belongsTo"
        }
      ]
    }
  ]
}
```

## Create a time-ranged relationship between two assets

When you have physical equipment as part of the asset resources, you can use relationships to capture how physical equipment serves at different functional locations over time. You can specify the timespan a relationship is valid for by specifying the `startTime` and `endTime` properties.

This example shows how to create a relationship between two assets with a time range.

```http wrap
POST /api/v1/projects/publicdata/relationships
Host: api.cognitedata.com
Authorization: Bearer <token>
Content-Type: application/json

{
  "items": [
    {
      "externalId": "relationship_3",
      "dataSetId": 5514071318856557,
      "sourceExternalId": "asset_1",
      "sourceType": "asset",
      "targetExternalId": "asset_2",
      "targetType": "asset",
      "startTime": 1514768406,
      "endTime": 1577840406,
      "labels": [
        {
          "externalId": "flowsTo"
        }
      ]
    }
  ]
}
```

## List relationships

To list existing relationships, use:

```http wrap
POST /api/v1/projects/publicdata/relationships/list
Host: api.cognitedata.com
Authorization: Bearer <token>
Content-Type: application/json

{}
```

## List relationships for a particular asset

Relationships are directional. Therefore, we split this into two calls, one where the asset is the source and one where the asset is the target.

```http wrap
POST /api/v1/projects/publicdata/relationships/list
Host: api.cognitedata.com
Authorization: Bearer <token>
Content-Type: application/json

{
  "filter": {
    "sourceTypes": ["asset"],
    "sourceExternalIds": ["asset_1"]
  }
}
```

```http
POST /api/v1/projects/publicdata/relationships/list
Host: api.cognitedata.com
Authorization: Bearer <token>
Content-Type: application/json

{
  "filter": {
    "targetTypes": ["asset"],
    "targetExternalIds": ["asset_1"]
  }
}
```

## List relationships of a particular type for a particular asset

To retrieve all `flowsTo` relationships of `asset_1`.

```http wrap
POST /api/v1/projects/publicdata/relationships/list
Host: api.cognitedata.com
Authorization: Bearer <token>
Content-Type: application/json

{
  "filter": {
    "sourceTypes": ["asset"],
    "sourceExternalIds": ["asset_1"],
    "labels": {"containsAll": [{"externalId": "flowsTo"}]}
  }
}
```

## List relationship for an asset at a specific point in time

To list all time-ranged relationships of an asset with type `implements` and valid at a specific point in time, use:

```http wrap
POST /api/v1/projects/publicdata/relationships/list
Host: api.cognitedata.com
Authorization: Bearer <token>
Content-Type: application/json

{
  "filter": {
    "sourceTypes": ["asset"],
    "sourceExternalIds": ["asset_1"],
    "activeAtTime": {
      "min": 1601284751,
      "max": 1601284751
    },
    "labels": {"containsAll": [{"externalId": "implements"}]}
  }
}
```

{/* ## Update a relationship */}

## Delete a relationship

To delete a relationship, use:

```http wrap
POST /api/v1/projects/publicdata/relationships/delete
Host: api.cognitedata.com
Authorization: Bearer <token>
Content-Type: application/json

{
  "items": [{"externalId": "relationship_1"}]
}
```


<!-- SOURCE_END: docs.cognite.com/docs\dev\concepts\resource_types\relationships.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\concepts\resource_types\sequences.mdx -->
## File: docs.cognite.com/docs\dev\concepts\resource_types\sequences.mdx

---
title: 'Sequences'
description: 'Learn how to use sequences in CDF to index series of rows with string or numeric data, such as performance curves and logs.'
content-type: 'concept'
audience: [developer, dataEngineer]
experience-level: 200
article-type: 'article'
lifecycle: use
---
{/*What are sequences?  Generic overview information*/}

In Cognite Data Fusion (CDF), a sequence is a generic **resource type** for indexing a series of **rows** by **row number**. Each **row** has one or more **columns** with either string or numeric data. Performance curves and various types of logs are examples of sequences.

## About sequences

{/*What can I do with sequences?  */}

An asset can have one or more sequences connected to it. You can analyze and visualize sequences to **draw inferences** from the data, for example, to visualize the expected performance of a generator given its load. Another common way to use sequences is to store large amounts of data index of which isn't linked to time.

{/*What is a row? More details + specific information for the Cognite implementation*/}

A **row** is a piece of information associated with a **row number**. Each **row** has a number of **columns**, each containing an **integer**, **floating point**, or **string** value. When you create the sequence, you define the column types, and each sequence can have up to 200 columns. The rows are identified by their **row number**, a searchable positive 64-bit integer. You can't search by other columns than the row number. The rows don't have to be consecutive or start at a specific number.

Typical use cases for sequences include:

- Storing and retrieving an entire curve with an x-y dependency. In this case, use two floating-point columns so that the integer type on the row number doesn't restrict you..
- Storing a log based on distance or depth, with information in columns of different types for each entry. If you store the distance or depth as the row number (an integer), you may lose precision but can search for all events that occurred at a particular distance or depth.

<Tip>
See the [sequences API documentation](https://api-docs.cognite.com/20230101/#tag/Sequences) for more information about how to work with sequences.
</Tip>

{/*Add examples to support and illustrate the concepts above */}

## Create a sequence

To create a sequence, you need to define the columns. It's optional, but highly recommended, to give the sequence itself an [`externalId`](/dev/concepts/external_id). For columns, specify an `externalId` and a `valueType`.

This example request creates a sequence:

```http wrap
POST /api/v1/projects/publicdata/sequences
Content-Type: application/json

{
    "items": [
        {
            "externalId": "XV_123/performance_curve",
            "name": "performance curve XV_123",
            "description": "Performance curve for the so-and-so compressor",
            "metadata":
                {
                    "source": "unliberated data store 42"
                },
            "assetId": 4324823473421,
            "columns": [
                {
                    "externalId": "load",
                    "valueType": "DOUBLE",
                    "metadata": {"unit": "kPa"}
                },
                {
                    "externalId": "performance",
                    "valueType": "LONG",
                    "description": "expected performance as % of maximum",
                    "metadata": {
                        "unit": "%",
                        "some other field": "some value"
                    }
                },
                {
                    "externalId": "quality",
                    "valueType": "STRING",
                    "name": "optional human readable name"
                }
            ]
        }
    ]
}
```

## Add rows to a sequence

To add rows to a sequence, you need to specify the sequence `id` or `externalId` along with a list of the columns you are inserting data into.

For example, to insert two rows in the sequence created in the previous example, use:

```http wrap
POST /api/v1/projects/publicdata/sequences/data
Content-Type: application/json

{
    "items": [
        {
            "externalId": "XV_123/performance_curve",
            "columns": ["load","performance","quality"],
            "rows": [
                    {"rowNumber":1, "values":[123.45, 80, "POOR"] }
                    {"rowNumber":2, "values":[20.1, 95, "GOOD"] }
            ]
        }
    ]
}
```

Nulls can represent missing data.

## Retrieve rows from a sequence

To get the rows from a sequence, you can use the [`externalId`](/dev/concepts/external_id) or the `id` of the sequence.
Optionally, specify the columns you want to retrieve. The default is to return all columns.

For example, to get the first 5 rows from a sequence by using the `externalId`, in this case `performance_curve`, use the request:

```http wrap
POST /api/v1/projects/publicdata/sequences/data/list
Content-Type: application/json

{
    "limit": 5,
    "externalId": "example",
    "start": 0,
    "end": 5
}
```

<Note>

- `end` is exclusive. Even if you specify a higher limit, the request returns a maximum of five rows.

- Unlike the endpoints for creating sequences and inserting rows, you can only request rows from a single sequence here.

- We skip rows where all column values are null/missing.

</Note>

The response will look similar to this:

```json wrap
{
  "externalId": "example",
  "id": 7137123130454053,
  "columns": [
    {
      "externalId": "load",
      "valueType": "DOUBLE"
    },
    {
      "externalId": "performance",
      "valueType": "LONG"
    }
  ],
  "rows": [
    {
      "rowNumber": 1,
      "values": [0.0, 100]
    },
    {
      "rowNumber": 2,
      "values": [10.0, 95]
    },
    {
      "rowNumber": 3,
      "values": [20.0, 92]
    },
    {
      "rowNumber": 4,
      "values": [25.0, 85]
    },
    {
      "rowNumber": 5,
      "values": [30.0, 65]
    }
  ]
}
```

## Add (or modify) columns to a sequence

To add columns to a sequence, you need to use the [sequences update endpoint](https://api-docs.cognite.com/20230101/#operation/updateSequences).
This endpoint can also remove or modify existing columns.

Data in removed columns are lost, whereas data in new columns default to null.

You can modify the name, externalId, description, or metadata fields. To modify and change the data type,
you need to delete and recreate the column. Modification won't affect the default ordering of columns.

For example, to update the metadata and externalId of a column, remove and add a column,
use the request:

```http wrap
POST /api/v1/projects/publicdata/sequences/update
Content-Type: application/json

{
    "items": [
        {
            "externalId": "XV_123/performance_curve",
            "update": {
                "columns": {
                    "modify": [
                        {
                            "externalId": "load",
                            "update": {
                                "metadata": {
                                    "add": {
                                        "accuracy": "+/- 0.1 kPa"
                                    }
                                },
                                "externalId": {
                                    "set": "pressure"
                                }
                            }
                        }
                    ],
                    "remove": [
                        {
                            "externalId": "quality"
                        }
                    ],
                    "add": [
                        {
                            "externalId": "errors",
                            "description": "human readable error messages",
                            "valueType": "STRING"
                        }
                    ]
                }
            }
        }
    ]
}
```

## Paginate retrieved rows

When you get too many rows, you can use cursors to paginate through the results.

For example, to get a single specific column from all rows with row numbers between 0 and 999999, use the request:

```http wrap
POST /api/v1/projects/publicdata/sequences/data/list
Content-Type: application/json

{
    "limit": 10000,
    "externalId": "big_example",
    "start": 0,
    "end": 1000000,
    "columns": ["performance"],
    "cursor": null
}
```

<Note>
10000 is the maximum limit per request.
</Note>

The response will look similar to this:

```json wrap
{
    "externalId": "big_example",
    "id": 21312313130454053,
    "columns": [
        {
            "externalId": "performance",
            "valueType":"LONG"
        }
    ],
    "rows": [
        {
            "rowNumber": 0,
            "values": [100]
        },
        {
            "rowNumber": 1,
            "values": [99]
        },
        <9997 rows ommitted>,
        {
            "rowNumber": 9999,
            "values": [42]
        }
    ],
    "nextCursor": "3zZmOn50qL9Kkhjwmrz602sHfQifGypzdqYEtQG3ajuU"
}
```

To retrieve the next 10000 rows, pass the same request with the `cursor` field changed to the value returned in `nextCursor`.


<!-- SOURCE_END: docs.cognite.com/docs\dev\concepts\resource_types\sequences.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\concepts\resource_types\state_timeseries.mdx -->
## File: docs.cognite.com/docs\dev\concepts\resource_types\state_timeseries.mdx

---
title: 'State time series'
description: 'Track discrete operational states of industrial equipment with predefined valid states and specialized aggregations optimized for analyzing state changes.'
content-type: 'concept'
audience: [developer, dataEngineer]
experience-level: 200
lifecycle: use
article-type: article
---
<Note>
The features described in this section are currently in [private beta](/cdf/product_feature_status). For more information and to sign up, contact your Cognite representative.
</Note>

State time series are a specialized time series type designed for tracking discrete operational states of industrial equipment. Unlike numeric or string time series, state time series have predefined valid states and support specialized aggregations optimized for analyzing state changes over time.

States represent the discrete operational conditions of equipment at any point in time. They provide an unambiguous way to track, query, and analyze the operational state of equipment by managing machine states as a native element of time series data.

Some common use cases for state time series include:

- Equipment states: `ON/OFF`, `OPEN/CLOSED`, `IDLE/RUNNING/ERROR/MAINTENANCE`
- Process states: `HEATING/COOLING/STABLE`, `WARMING_UP/ACTIVE/STANDBY/COOLING_DOWN/OFF`
- Network equipment: `UP/DOWN/DISABLE/ERROR`
- Environmental conditions: `SUNNY/OVERCAST/NIGHT` (for solar panels), `CLEAR/RAINY/SNOWY` (for weather stations)
- Quality grades: `A/B/C/D/E/F`, `PASS/FAIL`, `EXCELLENT/GOOD/FAIR/POOR`

## Core concepts

Understanding state time series requires familiarity with how they differ from numeric and string time series, how state sets define valid states, and how state transitions are tracked over time.

### State vs. other time series types

| Aspect               | Numeric                 | String          | State                                |
|----------------------|-------------------------|-----------------|--------------------------------------|
| **Values**           | Continuous measurements | Free-form text  | Predefined state set                 |
| **Value validation** | None                    | None            | Validated against state set          |
| **Aggregations**     | min/max/avg/sum         | Not supported   | duration/transitions/count per state |
| **Typical use case** | Sensor measurements     | Labels/comments | Equipment operational states         |
| **Visualization**    | Line/scatter charts     | Not plotted     | Step charts with state labels        |

### State sets

A **state set** is a named collection of valid state value pairs (integer, string) that defines the possible states for a time series. State sets are managed through the data modeling API and enable:

- **Shared definitions**: Multiple time series can reference the same state set.
- **Validation**: Data points are validated against the state set during ingestion.
- **Meaningful labels**: Numeric state codes are automatically mapped to human-readable strings.

```json title="Example state set" wrap
{
  "name": "Standard Pump States",
  "states": [
    {
      "numericValue": 0,
      "stringValue": "OFF",
      "description": "Pump is powered off"
    },
    {
      "numericValue": 1,
      "stringValue": "RUNNING",
      "description": "Pump is actively operating"
    },
    {
      "numericValue": 2,
      "stringValue": "STANDBY",
      "description": "Pump is ready but not running"
    },
    {
      "numericValue": 3,
      "stringValue": "ERROR",
      "description": "Pump has encountered an error"
    }
  ]
}
```


Each state can optionally include a `description` field. Descriptions are stored in the data modeling service and are not returned by the time series API.

### State constraints

State sets have the following constraints:

- Maximum 100 unique states per state set. Fewer states present in aggregate periods result in smaller response payloads and faster retrieval.
- Numeric values must be 32-bit integers (-2,147,483,648 to 2,147,483,647).
- String values must be up to 255 UTF-8 characters.
- Both numeric and string values must be unique within a state set.

### State transitions

A state transition occurs when equipment changes from one state to another. During aggregation, the system tracks the number of transitions into each state. To retrieve the exact timestamps when state changes occurred or to determine which state the equipment transitioned from, fetch the raw data points directly.

## API usage

State time series are created and managed through the data modeling API, while data ingestion and retrieval use the time series API. The following examples demonstrate how to work with state time series and state sets.

### Create state time series

State time series can only be created through data modeling by setting the `type` property to `state` and linking to a state set:

<AccordionGroup>
<Accordion title="Example: create state time series">

```http wrap
POST /api/v1/projects/{project}/models/instances
Content-Type: application/json

{
  "items": [{
    "space": "industrial_assets",
    "externalId": "valve_001_state",
    "instanceType": "node",
    "sources": [{
      "source": {
        "type": "view",
        "space": "cdf_cdm",
        "externalId": "CogniteTimeSeries",
        "version": "v1"
      },
      "properties": {
        "name": "Valve 001 Position",
        "type": "state",
        "stateSet": {
          "space": "state_definitions",
          "externalId": "valve_states"
        }
      }
    }]
  }]
}
```
</Accordion>
</AccordionGroup>

### Create state sets

State sets are created through data modeling using the `CogniteStateSet` view:

<AccordionGroup>
<Accordion title="Example: create state set">

```http wrap
POST /api/v1/projects/{project}/models/instances
Content-Type: application/json

{
  "items": [{
    "space": "state_definitions",
    "externalId": "valve_states",
    "instanceType": "node",
    "sources": [{
      "source": {
        "type": "view",
        "space": "cdf_cdm",
        "externalId": "CogniteStateSet",
        "version": "v1"
      },
      "properties": {
        "name": "Valve Position States",
        "description": "Standard position states for industrial valves",
        "states": [
          {"numericValue": 0, "stringValue": "CLOSED"},
          {"numericValue": 1, "stringValue": "OPEN"},
          {"numericValue": 2, "stringValue": "TRANSITIONING"}
        ]
      }
    }]
  }]
}
```
</Accordion>
</AccordionGroup>

### Ingest state data

State data points can include both numeric and string values. If both are provided, they must correspond to the same state:

<AccordionGroup>
<Accordion title="Example: ingest state data points">

```http wrap
POST /api/v1/projects/{project}/timeseries/data
Content-Type: application/json

{
  "items": [{
    "instanceId": {"space": "industrial_assets", "externalId": "valve_001_state"},
    "datapoints": [
      {
        "timestamp": 1609459200000,
        "numericValue": 0,
        "stringValue": "CLOSED"
      },
      {
        "timestamp": 1609462800000,
        "numericValue": 1,
        "stringValue": "OPEN"
      },
      {
        "timestamp": 1609466400000,
        "numericValue": 0,
        "stringValue": "CLOSED"
      }
    ]
  }]
}
```
</Accordion>
</AccordionGroup>

### Retrieve raw data points

When retrieving raw data points, the `numericValue` is returned if present. For data points with a `Bad` status, the `numericValue` may be `null` or omitted. If the numeric value exists in the current state set, the response also includes the `stringValue`:

<AccordionGroup>
<Accordion title="Example: retrieve raw state data points">

```http wrap
POST /api/v1/projects/{project}/timeseries/data/list
Content-Type: application/json

{
  "items": [{
    "instanceId": {"space": "industrial_assets", "externalId": "valve_001_state"},
    "limit": 10,
    "start": 1609459200000,
    "end": 1609545600000
  }]
}
```

**Response:**
```json wrap
{
  "items": [{
    "instanceId": {"space": "industrial_assets", "externalId": "valve_001_state"},
    "type": "state",
    "stateSet": {
      "space": "state_definitions",
      "externalId": "valve_states"
    },
    "datapoints": [
      {
        "timestamp": 1609459200000,
        "numericValue": 0,
        "stringValue": "CLOSED"
      },
      {
        "timestamp": 1609462800000,
        "numericValue": 1,
        "stringValue": "OPEN"
      }
    ]
  }]
}
```
</Accordion>
</AccordionGroup>


### Retrieve state aggregations

State time series support specialized aggregations for analyzing operational patterns:

- **stateCount**: The total number of data points per state in the time period.
- **stateTransitions**: The number of transitions into each state.
- **stateDuration**: The total time (milliseconds) spent in each state.

<AccordionGroup>
<Accordion title="Example: retrieve state aggregations">

```http wrap
POST /api/v1/projects/{project}/timeseries/data/list
Content-Type: application/json

{
  "items": [{
    "instanceId": {"space": "industrial_assets", "externalId": "valve_001_state"},
    "aggregates": ["count", "countGood", "countUncertain", "stateCount", "stateTransitions", "stateDuration"],
    "granularity": "1h",
    "start": 1609459200000,
    "end": 1609545600000,
    "treatUncertainAsBad": false
  }]
}
```

**Response:**
```json wrap
{
  "items": [{
    "instanceId": {"space": "industrial_assets", "externalId": "valve_001_state"},
    "datapoints": [{
      "timestamp": 1609459200000,
      "count": 67,
      "countGood": 59,
      "countUncertain": 8,
      "stateAggregates": [
        {
          "numericValue": 0,
          "stringValue": "CLOSED",
          "stateCount": 45,
          "stateTransitions": 3,
          "stateDuration": 2700000
        },
        {
          "numericValue": 1,
          "stringValue": "OPEN",
          "stateCount": 22,
          "stateTransitions": 3,
          "stateDuration": 900000
        }
      ]
    }]
  }]
}
```
</Accordion>
</AccordionGroup>

### Supported aggregates

State time series support both top-level status aggregates and state-specific aggregates:

**Top-level aggregates** (apply to all data points):

- `count`, `countGood`, `countUncertain`, `countBad`
- `durationGood`, `durationUncertain`, `durationBad`

**State-level aggregates** (within `stateAggregates`, only for Good data points):
- `stateCount`: The number of data points in this state.
- `stateTransitions`: The number of transitions into this state.
- `stateDuration`: The total time spent in this state in milliseconds.

<Tip>
For detailed explanations of each aggregate function, including examples and best practices, see [State time series aggregates](/dev/concepts/aggregation/#state-aggregates).
</Tip>

## State set management

State sets can be modified after creation, but changes to state definitions affect how historical data is interpreted in queries and aggregations. Understanding these implications is important when managing state sets over time.

### Update state sets

State sets are mutable and can be updated after creation. However, changes affect how historical data is interpreted:

<AccordionGroup>
<Accordion title="Example: update state set">

```http wrap
POST /api/v1/projects/{project}/models/instances
Content-Type: application/json

{
  "items": [{
    "space": "state_definitions",
    "externalId": "valve_states",
    "instanceType": "node",
    "sources": [{
      "source": {
        "type": "view",
        "space": "cdf_cdm",
        "externalId": "CogniteStateSet",
        "version": "v1"
      },
      "properties": {
        "states": [
          {"numericValue": 0, "stringValue": "CLOSED"},
          {"numericValue": 1, "stringValue": "OPEN"},
          {"numericValue": 2, "stringValue": "TRANSITIONING"},
          {"numericValue": 3, "stringValue": "FAULT"}
        ]
      }
    }]
  }]
}
```
</Accordion>
</AccordionGroup>

**Impact of state set changes:**

- **Adding states**: New states can be added without affecting existing data points.
- **Removing states**: Historical data points with removed states retain their numeric values but may not have string values in queries.
- **Changing mappings**: If you change the string value for a numeric value, historical data displays with the new string value.

### Delete state sets

Deleting a state set through data modeling causes all time series referencing it to have an empty state set. The time series and data points remain intact. Without a state set, the only data points that can be ingested are those with a `Bad` status and a `null` numeric value.

## Limitations

The following limitations apply to state time series:

- Maximum 100 unique states per state set.
- State time series can only be created through data modeling (not via the time series API).
- Traditional numeric aggregations (min, max, avg) are not applicable to state time series.
- No SDK support in the initial private beta.
- State sets are not immutable (changes affect historical data interpretation).



<!-- SOURCE_END: docs.cognite.com/docs\dev\concepts\resource_types\state_timeseries.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\concepts\resource_types\synthetic_timeseries.mdx -->
## File: docs.cognite.com/docs\dev\concepts\resource_types\synthetic_timeseries.mdx

---
title: 'Synthetic time series'
description: 'Learn how to combine time series with constants, operators, and functions to create new synthetic time series in CDF.'
content-type: 'concept'
audience: [developer, dataEngineer]
experience-level: 100
article-type: 'article'
lifecycle: use
---
Combine several time series, constants, and operators to create new synthetic time series.

For example, use the expression `3.6 * TS{externalId='wind-speed'}` to convert units from m/s to k/h.

Also, you can **combine** time series: `TS{id=123} + TS{externalId='my_external_id'} / TS{space='data modeling space', externalId='dm id'}`, use **functions** with time series `sin(pow(TS{id=123}, 2))`, and **aggregate** time series `TS{id=123, aggregate='average', granularity='1h'}+TS{id=456}`. See below for a list of supported functions and aggregates.

<Tip>
See the [synthetic time series API documentation](https://api-docs.cognite.com/20230101/#tag/Synthetic-Time-Series) for more information about how to work with synthetic time series.
</Tip>

## Functions

Synthetic time series support these functions:

- Inputs with internal ID, external ID, or data modeling instance ID (space and externalId). Single or double quotes must surround the external ID and space.
- Mathematical operators +,-,\*,/.
- Grouping with brackets ().
- Trigonometrics. sin(x), cos(x) and pi().
- ln(x), pow(base, exponent), sqrt(x), exp(x), abs(x).
- Variable length functions. max(x1, x2, ...), min(...), avg(...).
- round(x, decimals) (`-10<decimals<10`).
- `on_error(expression, default)`, for handling errors like overflow or division by zero.
- `map(expression, [list of strings to map from], [list of values to map to], default)`.

### Convert string time series to doubles

The `map()` function can handle time series of type _string_ and convert strings to doubles. If, for example, a time series for a valve can have the values `"OPEN"` or `"CLOSED"`, you can convert it to a number with:

```json wrap
map(TS{externalId='stringstate'}, ['OPEN', 'CLOSED'], [1, 0], -1)
```

`"OPEN"` is mapped to 1, `"CLOSED"` to 0, and everything else to -1.

Aggregates on string time series are currently not supported. All string time series are considered to be stepped time series.

### Error handling: on_error()

There are three possible errors:

- **TYPE_ERROR**: You're using an invalid type as input. For example, a string time series with the division operator.
- **BAD_DOMAIN**: You're using invalid input ranges. For example, division by zero, or sqrt of a negative number.
- **OVERFLOW**: The result is more than 10^100 in absolute value.

Instead of returning a value for these cases, Cognite Data Fusion (CDF) returns an error field with an error message. To avoid these, you can wrap the (sub)expression in the `on_error()` function, for example. `on_error(1/TS{externalId='canBeZero'}, 0)`. Note that this can happen because of interpolation, even if none of the RAW data points are zero.

## Aggregation

You define aggregates similar to time series inputs, but aggregates have extra parameters: `aggregate`, `granularity`, and (optionally) `alignment`. Aggregate must be one of **interpolation**, **stepinterpolation**, or **average**.

You must enclose the value of aggregate and granularity in quotes.

```json wrap
ts{id=12356, aggregate="average", granularity="2h", alignment=3600000}
```

See also: [Aggregating time series data](/dev/concepts/aggregation).

### Granularity

The granularity decides the length of the intervals CDF takes the aggregate over. they're specified as a number plus a granularity unit, like `120s`, `15m`, `48h`, or `365d`.

Intervals are half-open, including the start time and excluding the end time. `[start, start+granularity)`

Granularity must be on the form NG where N is a number, and G is _s_, _m_, _h_, or _d_ (second, minute, hour, day). The maximum number you can specify is 120, 120, 100.000, and 100.000 for seconds, minutes, hours, and days, respectively.

### Output granularity

We return data points at any point in time where:

- Any input time series has an input.
- All time series are defined (between the first and last data point, inclusive)

For example, if time series A has data at time 15, 30, 45, 60, and time series B has data at times 30, 40, 50, A+B will have data at 30, 40, 45, and 50.

<Tip>
Aggregates have data at every _granularity_ time, rounded to multiples of _granularity_ since epoch. For example, `60m` aggregates have data points at `00.00`, `01.00` even if the start time is `00.15`. This **differs from retrieving aggregate data points from the non-synthetic endpoint**, where CDF rounds to multiples of the granularity _unit_ and uses an arbitrary offset.
</Tip>

### Interpolation

If there is no input data, CDF interpolates. As a general rule, CDF uses **linear** interpolation: finds the previous and next data point and draws a straight line between these. The other case is **step** interpolation: CDF uses the value of the previous data point.

CDF interpolates _any_ interval. If there are data points in 1971 and 2050, CDF defines the time series for all timestamps between.

Most aggregates are constant for the whole duration of _granularity_. The only exception is the _interpolation_ aggregate on non-step time series.

### Alignment

{/* vale off */}

The alignment decides the start of the intervals and is specified in milliseconds.

The `alignment` parameter lets you align weekly aggregates to start on specific days of the week, for example, Saturday or Monday. By default, aggregation with synthetic time series is aligned to _Thursday 00:00:00 UTC, 1 January 1970_, for instance, `alignment=0`.

You can also use alignment to create aggregates that are aligned to different time zones. For example, the chart below shows the daily average chlorine indicator for city water aligned to two different time zones: GMT+0 and GMT+3.

- To align to UTC/GMT+0 (aligned to: Sep 09 2020 00:00:00 UTC)

  `ts{externalId='houston.ro.REMOTE_AI[34]', alignment=1599609600000, aggregate='average', granularity='24h'}`

- To align to UTC/GMT+3 (aligned to: Sep 09 2020 03:00:00 UTC)

  `ts{externalId='houston.ro.REMOTE_AI[34]', alignment=1599620400000, aggregate='average', granularity='24h'}`

<Frame>
<img class="illustration" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/concepts/resource_types/chlorine_alignment.png" alt="Daily average chlorine indicator aligned to different time zones" width="x%"/>
</Frame>


<Tip>
The alignment must be a multiple of the granularity unit. In the example above, the alignment needs to be a multiple of a whole hour, since the granularity is 24h.
</Tip>

{/* vale on */}

All intervals are on the form `(N*granularity + alignment, (N+1)*granularity + alignment)`, where `N` is an integer.

## Unit conversion

If a time series has a defined `unitExternalId`, you can convert its values to a different unit within the same quantity.

```json wrap
ts{externalId='temperature_f_houston', aggregate='average', granularity='24h', targetUnit='temperature:deg_c'}
ts{id='123', targetUnitSystem='Imperial'}
```

For each time series/aggregate, you may specify either `targetUnit` or `targetUnitSystem`, but not both. The chosen target unit must be compatible with the original unit.
When querying data points using synthetic time series, the values will no longer retain their 
unit information.

For instance, despite it not being physically accurate, it is possible to add values from a **temperature** time series to those from a **distance** time series.

## Limits

In a single request, you can ask for:

- 10 expressions (10 synthetic time series).
- 10.000 data points, summed over all expressions.
- 100 input time series referrals, summed over all expressions.
- 2000 tokens in each expression. Similar to 2000 characters, except that words and numbers are counted as a single character.

If you use time series aggregates as inputs, and you get slow responses or a 503 error code, try again or reduce the limit. This is the expected behavior when you have recently updated the data points, and CDF needs to recalculate aggregates.


<!-- SOURCE_END: docs.cognite.com/docs\dev\concepts\resource_types\synthetic_timeseries.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\concepts\resource_types\timeseries.mdx -->
## File: docs.cognite.com/docs\dev\concepts\resource_types\timeseries.mdx

---
title: 'Time series'
sidebar_title: 'Introduction'
description: 'Learn how to use time series in CDF to index data points in time order, with support for aggregation, interpolation, and status codes.'
content-type: 'concept'
audience: [developer, dataEngineer, dataScientist]
experience-level: 200
article-type: 'article'
lifecycle: use
---
{/*What is a time series?  Generic overview information*/}

In Cognite Data Fusion (CDF), a time series is the **resource type** for indexing a series of **data points** in **time order**. Examples of a time series are the temperature of a water pump asset, the monthly precipitation in a location and the daily average number of manufacturing defects.

## About time series

An asset can have several time series connected to it. For example, a water pump asset can have time series that measure the pump temperature, the pressure within the pump, rpm, flow volume, power consumption, and more.

{/*What can I do with time series?  */}

Time series can be analyzed and visualized to **draw inferences** from the data, for example, to identify trends, seasonal movements, and random fluctuations. Other common uses of time series analysis include **forecasting** future values, for example, scheduling maintenance and controlling the series by adjusting parameters to **optimize** equipment performance.

{/*What is a data point? More details + specific information for the Cognite implementation*/}

A **data point** is a piece of information associated with a specific time, stored as a **numerical**, **string**, or **state** value. Timestamps, defined in milliseconds in Unix Epoch time identify data points. We don't support fractional milliseconds, and don't count leap seconds.

### Time series types

Time series in CDF support these types:

- **Numerical** data points can be **aggregated** to reduce the amount of data transferred in query responses and improve performance. You can specify one or more aggregates, for example, `average`, `minimum`, and `maximum`, and also the time **granularity** for the aggregates, for example, `1h` for one hour.

  See [Aggregating time series data](/dev/concepts/aggregation/index) to learn more about how Cognite Data Fusion aggregates and interpolates time series data, and see the details about the available aggregation functions.

- **String** data points can store arbitrary information like states, for example, `open` or `closed`, or more complex information in JSON format. Cognite Data Fusion can **not** aggregate string data points.

- **State** data points _(Private Beta)_ represent discrete operational states of equipment with predefined valid states and specialized aggregations (duration, transitions, count per state). State time series provide an unambiguous way to track and analyze equipment operational states. 

  See [State time series](/dev/concepts/resource_types/state_timeseries) for more information. For details about supported aggregations, see [State time series aggregates](/dev/concepts/aggregation/index#state-aggregates).

Cognite Data Fusion stores discrete data points, but the underlying process measured by the data points can vary continuously. To **interpolate** between data points, use the `isStep` flag on the [time series object](https://api-docs.cognite.com/20230101/#tag/Time-series) to assume that each value stays the same until the next measurement (`isStep`), or that it linearly changes between the two measurements (not `isStep`).

Each data point in a time series can have a _status code_ that describes the quality of the data point.
There are three categories of status codes: `Good`, `Uncertain`, and `Bad`.
By default, only `Good` data points are returned, but the you opt-in to take all data points into account.
For more information, see the [status code](/dev/concepts/reference/status_codes) page.

<Tip>
See the [time series API documentation](https://api-docs.cognite.com/20230101/#tag/Time-series) for more information about how to work with time series.
</Tip>

{/*The examples below should support and illustrate the concepts above */}

## Get the data points from a time series

You can get data points from a time series by using the `externalId` or the `id` of the time series.

<AccordionGroup>
<Accordion title="Example: get data points by externalId">

To get data points from a time series by using the `externalId`, in this case `outside_temperature`, enter:

```json wrap
POST /api/v1/projects/publicdata/timeseries/data/list
Content-Type: application/json

{
  "items": [
    {
      "limit": 5,
      "externalId": "outside-temperature"
    }
  ]
}
```

The response will look similar to this:

```json wrap
{
  "items": [
    {
      "isString": false,
      "id": 44435358976768,
      "externalId": "outside-temperature",
      "datapoints": [
        {
          "timestamp": 1349732232902,
          "value": 31.62889862060547
        },
        {
          "timestamp": 1349732244888,
          "value": 31.59380340576172
        },
        {
          "timestamp": 1349732245888,
          "value": 31.62889862060547
        },
        {
          "timestamp": 1349732258888,
          "value": 31.59380340576172
        },
        {
          "timestamp": 1349732259888,
          "value": 31.769287109375
        }
      ],
      "nextCursor": "wpnaLqNvdkOrsPd"
    }
  ]
}
```
</Accordion>
</AccordionGroup>

## Get aggregate values between two points in time

To visualize or analyze a longer period, you can extract the aggregate values between two points in time. See [Retrieve data points](https://api-docs.cognite.com/20230101/#operation/getMultiTimeSeriesDatapoints) for valid aggregate functions and granularities.

<AccordionGroup>
<Accordion title="Example: get hourly average aggregates">

For example, to return the hourly average aggregate with a granularity of 1 hour, for the last 5 hours, for the `outside_temperature` time series, enter:

```json wrap
POST /api/v1/projects/publicdata/timeseries/data/list
Content-Type: application/json

{
  "items": [
    {
      "limit": 5,
      "externalId": "outside-temperature",
      "aggregates": ["average"],
      "granularity": "1h",
      "start": 1541424400000,
      "end":"now"
    }
  ]
}
```

The response will look similar to this:

```json wrap
{
  "items": [
    {
      "id": 44435358976768,
      "externalId": "outside-temperature",
      "datapoints": [
        {
          "timestamp": 1541422800000,
          "average": 26.3535328292538
        },
        {
          "timestamp": 1541426400000,
          "average": 26.34716274449083
        },
        {
          "timestamp": 1541430000000,
          "average": 26.35558703492914
        },
        {
          "timestamp": 1541433600000,
          "average": 26.36287845690146
        },
        {
          "timestamp": 1541437200000,
          "average": 26.36948613080317
        }
      ],
      "nextCursor": "wpnaLqNvdkOrsPd"
    }
  ]
}
```
</Accordion>
</AccordionGroup>

## Count number of time series matching filtering criteria

Count number of time series that match selected filtering criteria, such as being part of specific data set and follow naming convention for externalId.

<AccordionGroup>
<Accordion title="Example: count time series by filter criteria">

```json wrap
POST /api/v1/projects/daitya/timeseries/aggregate HTTP/1.1
content-type: application/json

{
  "filter": {
    "dataSetIds": [
      {
        "externalId": "Cognite data quality monitoring alerts and metrics"
      }
    ],
    "externalIdPrefix": "dq_monitor"
  }
}
```

The response will look similar to this:

```json wrap
{
  "items": [
    {
      "count": 273
    }
  ]
}
```
</Accordion>
</AccordionGroup>

## Best practices

Use the tips and best practices below to increase the throughput and query performance.

### Message size/batching

Send many data points from the same time series in the same request. If you have room for more data points in the request, you can add more time series.

We prefer batch sizes up to 100k **numeric** data points or 10-100k **string** data points, depending on the string length (around 1 MB is good).

For each time series, group the data points in time.
Ideally, different requests shouldn't have overlapping time series ranges. If data changes, update existing ranges.

### Error handling

There are three error categories.

#### 429 - Too many requests

To ensure that the API isn't overloaded and has enough capacity for other users on the server, you receive a 429 error code when you have too many concurrent requests to the API.

If you receive this error, reduce the number of concurrent threads or _retry with capped exponential backoff_. Try again after, for instance, 1 second.
If you still get the error, try again after 2 seconds, followed by 4, 8, 10, 10 seconds (cap = 10 seconds).

You can try to scale up when you're no longer rate-limited with a 429.

<Note>
There's no fixed limit of requests before you receive 429. The limit depends on the complexity of the requests, other users on the server, and service performance.
</Note>

#### 5xx - Server errors

If you receive these errors, reduce the number of concurrent threads or _retry with capped exponential backoff_ as described for [429 errors](#429---too-many-requests).

You may have found a bug if you receive repeated 500 - Internal Server Error. Notify [support@cognite.com](mailto:support@cognite.com) and include the request ID in the error message. You can scale up to regular operations when you no longer get the 5xx errors.

#### 4xx - User errors

Usually, you don't want to retry 4xx errors. Instead, ensure that you're using the API correctly, that you've signed in with the correct user, and that the resources you try to access haven't been modified in a separate request.

### Retries and idempotency

Most of the API endpoints, including all datapoint requests, are idempotent. You can send the same request several times without any effect beyond the first request.

When you modify or delete time series, next requests can fail harmlessly if the references have become invalid.

The only endpoint that's not idempotent is creating a time series **without externalId**. For each successful request, you generate a new time series. The recommendation is to always use _externalId_ on your time series.

<Note>
We always apply the complete request or nothing at all. A 200 response indicates that we applied the complete request.
</Note>


<!-- SOURCE_END: docs.cognite.com/docs\dev\concepts\resource_types\timeseries.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\concepts\resource_types\units\changelog.mdx -->
## File: docs.cognite.com/docs\dev\concepts\resource_types\units\changelog.mdx

---
title: 'Unit catalog changelog'
url: "https://github.com/cognitedata/units-catalog/blob/main/CHANGELOG.md"
---

<!-- SOURCE_END: docs.cognite.com/docs\dev\concepts\resource_types\units\changelog.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\concepts\resource_types\units\contribute.mdx -->
## File: docs.cognite.com/docs\dev\concepts\resource_types\units\contribute.mdx

---
title: 'Contributing to the unit catalog'
description: 'Learn how to contribute new units and unit systems to the Cognite global unit catalog through GitHub.'
content-type: 'procedure'
audience: developer
experience-level: 100
lifecycle: use
article-type: article
---
At Cognite, we maintain a global unit catalog filled with units and unit systems that are accessible to all CDF projects. We believe in the power of open-source and collaboration, so we've made our unit catalog open to contributions from our users.

While we encourage contributions to enhance the unit catalog, it is important to note that the catalog is made available to all Cognite customers. Therefore, we will carefully evaluate each submission to ensure that the additions are suitable for our broad customer base.

## How to contribute

<Steps>
<Step title="Access the unit catalog repository">
  Visit [cognitedata/units-catalog](https://github.com/cognitedata/units-catalog) to access the public repository.
  
  <Info>
  You need a GitHub account to contribute.
  </Info>
</Step>

<Step title="Familiarize yourself with the project">
  Read the **README** file to understand the project structure, contribution guidelines, and validation requirements.
</Step>

<Step title="Make your changes">
  1. Edit the `units.json` and/or `unitSystems.json` files to add your new units or unit systems. Ensure that your additions conform to the validation rules and tests outlined in the README.
  2. Provide a reliable source for the unit conversion factors. Accepted sources include UoM standards (e.g., Energistics, qudt.org), books, or journals. Google or Wikis are not considered valid sources.
</Step>

<Step title="Submit a pull request">
  1. Save your changes and raise a Pull request (PR).
  2. To expedite the review process, we recommend creating one PR per unit, or bundling up to 5 units per PR. Larger PRs may take longer to review.
</Step>

<Step title="Review and approval">
  Your PR will be reviewed by Cognite engineering to ensure it meets the contribution guidelines and is suitable for inclusion in the unit catalog.
  
  The review process may result in a request for changes or clarification, or the PR may be accepted and merged directly.
</Step>

<Step title="Release and deployment">
  1. After reviewing and merging approved PRs, Cognite creates new releases of the unit catalog at most every 2 months, depending on contributions.
  2. Critical bug fixes may be released outside of this schedule as needed.
  3. Once a release is issued on GitHub, the changes are deployed across all CDF clusters within the following two weeks.
</Step>
</Steps>

<Note>
Cognite reserves the right to evaluate and refuse additions that are not suitable for our broad customer base. We appreciate your understanding and cooperation in this regard.
</Note>

<!-- SOURCE_END: docs.cognite.com/docs\dev\concepts\resource_types\units\contribute.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\concepts\resource_types\units\units.mdx -->
## File: docs.cognite.com/docs\dev\concepts\resource_types\units\units.mdx

---
title: 'Units in Cognite Data Fusion'
sidebar_title: 'Introduction'
description: 'Learn how to use Units in CDF to standardize measurement data and enable seamless unit conversion across time series and data models.'
content-type: 'concept'
toc:
  min-heading-level: 2
  max-heading-level: 3
audience: developer
experience-level: 200
lifecycle: use
article-type: article
---
Cognite Data Fusion (CDF) uses Units to standardize measurement data across your industrial data resources. Whether you're working with time series or data models, Units help ensure consistent measurement representation and enable seamless unit conversion.

## Understanding Units
The units service in CDF consists of three key components:
1. __Quantity__: A specific, measurable property or characteristic of a physical system or substance. For example, temperature, pressure, mass, and length.
2. __Unit__: A standard amount or value of a particular quantity by which other amounts of the same quantity can be measured or expressed. For example, `°C` and `°F` are used for temperature, and `bar` and `psi` are used for pressure.
3. __Unit system__: A collection of default units for given quantities. Examples include the `SI` unit system (the default value is `K` for temperature and `Pascal` for pressure) and the `Imperial` unit system (the default value is `°F` for temperature and `psi` for pressure).

### Unit structure

Every CDF project comes with a list of standard industry-relevant units. Each unit contains:
- `externalId`: A unique identifier structured as `{quantity}:{unit}` (for example, `temperature:deg_c`).
- `name`: The primary name of the unit, such as `DEG_C`.
- `longName`: A descriptive name for the unit, like `degree Celsius`.
- `symbol`: The symbol represents the unit, like `°C`.
- `aliasNames`: An array of alternative names or aliases for the unit.
- `quantity`: The physical quantity the unit measures like `Temperature`.
- `conversion`: An object containing conversion factors (`multiplier` and `offset`) for unit conversion.
- `source`: The primary source of the unit's definition, typically a standard organization like `qudt.org`.
- `sourceReference`: A URL reference to the external source for the unit's definition.

<Tip>
For more information on how to work with units, visit the [Units API documentation](https://api-docs.cognite.com/20230101/#tag/Units).
</Tip>


## Accessing units

You can access units through two primary APIs:

1. The **Units API**: The Units API is the primary source of truth for all unit definitions in your CDF project. This API provides direct access to the unit catalog, where units are defined and managed.

   ```python wrap
   from cognite.client import CogniteClient
   client = CogniteClient()

   units = client.units.list()
   ```

2. The **Data Modeling API**: A copy of each unit is stored in the `cdf_cdm_units` space in the Data Modeling API. These nodes are used to assign direct relations from `CogniteTimeSeries` to a valid unit in the catalog. You can access units through the Data Modeling API as well.

   ```python wrap
   from cognite.client import CogniteClient
   import cognite.client.data_classes.data_modeling as dmc
   import cognite.client.data_classes.filters as dmf
   client = CogniteClient()

   unit_instances = client.data_modeling.instances.list(
      sources=[
         dmc.ViewId(
            space="cdf_cdm",
            external_id="CogniteUnit",
            version="v1"
         )
      ],
      filter=dmf.Equals(
         property=["node", "space"],
         value="cdf_cdm_units"
      ),
      limit=1000
   )
   ```

## Integration with time series

### Creating time series with units

CDF supports two methods for creating time series with units. The choice between these methods depends on whether you want your time series to be part of the knowledge graph. For both methods, you can:

- Assign units during time series creation
- Add or update units after the time series has been created
- Change units at any time during the time series lifecycle

<Warning>
For both APIs, updating the unit (`unit` in the Data Modeling API or `unitExternalId` in the TimeSeries API) will not automatically convert previously ingested data points. The unit assignment provides typing information and enables unit conversion when querying data points, but the underlying data remains in its original unit. Always ensure you ingest data in the correct unit, regardless of when the unit is assigned.
</Warning>

#### Method 1: Using the data modeling API

Use this method when you want your time series to be part of the CDF knowledge graph. Note that the `unit` field needs to be a direct relation reference to a unit from Cognite's unit catalog stored in the `cdf_cdm_units` space.

```python wrap
from cognite.client import CogniteClient
import cognite.client.data_classes.data_modeling as dmc
from cognite.client.data_classes.data_modeling.cdm.v1 import CogniteTimeSeriesApply
client = CogniteClient()

ts_instances = client.data_modeling.instances.apply(
    nodes=[
        CogniteTimeSeriesApply(
            space="cdm-test",
            external_id="test_unit_ts_1",
            name="Temperature Sensor 1",
            description="Temperature measured in degrees Celsius",
            is_step=False,
            time_series_type="numeric",
            unit=dmc.DirectRelationReference("cdf_cdm_units", "temperature:deg_c"),
            source_unit="C",
        ),
        CogniteTimeSeriesApply(
            space="cdm-test",
            external_id="test_unit_ts_2",
            name="Temperature Sensor 2",
            description="Temperature measured in degrees Fahrenheit",
            is_step=False,
            time_series_type="numeric",
            unit=dmc.DirectRelationReference("cdf_cdm_units", "temperature:deg_f"),
            source_unit="F",
        )
    ]
)
```

#### Method 2: Using the time series API

Use this method when you need standalone time series that don't require integration with the knowledge graph. The `unitExternalId` field is used to assign a unit from the unit catalog to the time series.

```python wrap
from cognite.client import CogniteClient
from cognite.client.data_classes import TimeSeriesWrite
client = CogniteClient()

ts_list = client.time_series.create(
    time_series = [
        TimeSeriesWrite(
            external_id="test_unit_ts_1",
            name="Temperature Sensor 1",
            unit="deg C",
            unit_external_id="temperature:deg_c",
            description="Temperature measured in degrees Celsius"
        ),
        TimeSeriesWrite(
            external_id="test_unit_ts_2",
            name="Temperature Sensor 2",
            unit="F",
            unit_external_id="temperature:deg_f",
            description="Temperature measured in degrees Fahrenheit"
        )
    ]
)
```

<Tip>
The different field names between the APIs exist for historical reasons. The TimeSeries API's naming is maintained for backwards compatibility. The fields serve identical purposes:

- In the TimeSeries API (legacy naming):
  - `unit`: A free-text string that stores the unit name as it is defined in the source system (e.g., `"deg C"`)
  - `unitExternalId`: Reference to a unit from the unit catalog (e.g., `"temperature:deg_c"`)

- In the Data Modeling API:
  - `sourceUnit`: A free-text string that stores the unit name as it is defined in the source system (e.g., `"deg C"`)
  - `unit`: Direct relation reference to a unit from the catalog (e.g., `("cdf_cdm_units", "temperature:deg_c")`)
</Tip>

### Querying time series with units

#### Filtering time series

Once a time series is created (or updated) with units from the catalog, it's possible to filter both on `unitExternalId` and `unitQuantity` when using the TimeSeries API.

The example below shows valid time series queries in the Python SDK:

```python wrap
from cognite.client import CogniteClient
client = CogniteClient()

# List time series assigned to degree Fahrenheit
client.time_series.list(
   unit_external_id="temperature:deg_f"
)

# List time series assigned to units of quantity Temperature
client.time_series.list(
   unit_quantity="Temperature"
)
```

When using the Data Modeling API, you can filter on the `unit` field, which is a direct relation reference to a unit from Cognite's unit catalog stored in the `cdf_cdm_units` space.

The example below shows how to filter `CogniteTimeSeries` instances in DMS based on the direct relation reference to a unit.

```python wrap
from cognite.client import CogniteClient
import cognite.client.data_classes.data_modeling as dmc
import cognite.client.data_classes.filters as dmf
client = CogniteClient()

client.data_modeling.instances.list(
    sources=[
        dmc.ViewId(
            space="cdf_cdm",
            external_id="CogniteTimeSeries",
            version="v1"
        )
    ],
    filter=dmf.Equals(
        property=["cdf_cdm", "CogniteTimeSeries/v1", "unit"],
        value=dmc.DirectRelationReference("cdf_cdm_units", "temperature:deg_c"),
    )
)
```

The aggregate endpoints in the TimeSeries API also support `unitExternalId` and `unitQuantity`, so you can get counts of the time series according to units and quantities.
The example below shows valid time series aggregations in the Python SDK.

```python wrap
# Get the number of unique unitExternalIds associated with time series
client.time_series.aggregate_cardinality_values(
   property="unitExternalId"
)

# Get the number of unique unitQuantities associated with time series
client.time_series.aggregate_cardinality_values(
   property="unitQuantity"
)

# Get the count per unitExternalIds associated with time series
client.time_series.aggregate_unique_values(
   property="unitExternalId"
)

# Get the count per unitQuantities associated with time series
client.time_series.aggregate_unique_values(
   property="unitQuantity"
)
```

#### Retrieving data with unit conversion

For time series with associated units from the catalog, you can query data points in a compatible unit (units from the same quantity).

The example below displays the data point queries with unit conversion in the Python SDK.

```python wrap
from cognite.client import CogniteClient
from cognite.client.data_classes import DatapointsQuery
import cognite.client.data_classes.data_modeling as dmc
client = CogniteClient()

# Retrieve data points using external_id
dps1 = client.time_series.data.retrieve(
   external_id=[
     {
       "external_id": "test_unit_ts_1",
       "target_unit": "temperature:deg_c"
     },
     {
       "external_id": "test_unit_ts_3",
       "target_unit": "length:m"
     }
   ],
   start="2w-ago",
   end="now"
)

# retrieve data points using instance_id
dps2 = client.time_series.data.retrieve(
    instance_id=[
        DatapointsQuery(
            instance_id=dmc.NodeId(
                space="cdm-test",
                external_id="test_unit_ts_1"
            ),
            target_unit="temperature:k"
        ),
        DatapointsQuery(
            instance_id=dmc.NodeId(
                space="cdm-test",
                external_id="test_unit_ts_3"
            ),
            target_unit="length:m"
        )
    ],
    start="2w-ago",
    end="now"
)
dps2
```

You can specify a target __unit system__ instead of specific units. The API will target the default unit for the allocated unit system.

The example below shows the data points queries with unit conversion to a unit system in the Python SDK:

```python wrap
from cognite.client import CogniteClient
import cognite.client.data_classes.data_modeling as dmc
client = CogniteClient()

client.time_series.data.retrieve(
   external_id=["test_unit_ts_1", "test_unit_ts_3"],
   instance_id=[dmc.NodeId(space="cdm-test", external_id="test_unit_ts_2")],
   target_unit_system="SI",
   start="2w-ago",
   end="now"
)
```

## Integration with data models

### Creating data models with units

#### 1. Define containers with units

You can associate float properties with specific units from the unit catalog within data models. This relationship occurs when you create or update a container.
The example below demonstrates creating a container with float properties assigned to a specific unit.

```python wrap
from cognite.client import CogniteClient
import cognite.client.data_classes.data_modeling as dmc
client = CogniteClient()

client.data_modeling.containers.apply(
    dmc.ContainerApply(
        space="pumps_space",
        external_id="PumpSpecificationsContainer",
        name="PumpSpecifications",
        description="Container storing pump specifications",
        used_for="node",
        properties={
            "maxPressure": dmc.ContainerProperty(
                nullable=True,
                description="Maximum Pump Pressure",
                name="maxPressure",
                type=dmc.Float64(
                    unit=dmc.data_types.UnitReference(
                        external_id="pressure:bar",
                        source_unit="BAR"
                    )
                )
            ),
            "maxTemperature": dmc.ContainerProperty(
                nullable=True,
                description="Maximum Pump Temperature",
                name="maxTemperature",
                type=dmc.Float64(
                    unit=dmc.data_types.UnitReference(
                        external_id="temperature:deg_c"
                    )
                )
            ),
            "rotationConfigurations": dmc.ContainerProperty(
                nullable=True,
                description="Rotation Configurations",
                name="rotationConfigurations",
                type=dmc.Float64(
                    is_list=True,
                    unit=dmc.data_types.UnitReference(
                        external_id="angular_velocity:rev-per-min"
                    )
                )
            )
        }
    )
)
```

Assigning `unit.externalId` and `unit.sourceUnit` to a float-type container property is optional. While `unit.externalId` must correspond to an entry in the unit catalog, `unit.sourceUnit` is a free text string where users can store the original name or alias used for the unit in the source system.

<Warning>
Updating the `unit.externalId` on a container property will not automatically convert the values ingested to the container. The `unit.externalId` provides typing information and enables unit conversion when querying the data. Make sure you ingest data in the correct unit.
</Warning>

#### 2. Create views

After you create the container, creating or updating views to map container properties is straightforward. For example, to map properties from the `PumpSpecificationsContainer` to a `PumpSpecificationsView`, see the Python code below:

```python wrap
from cognite.client import CogniteClient
import cognite.client.data_classes.data_modeling as dmc
client = CogniteClient()

client.data_modeling.views.apply(
    dmc.ViewApply(
        space="pumps_space",
        external_id="PumpSpecificationsView",
        name="PumpSpecifications",
        description="View pointing to pump specifications",
        version="1",
        properties={
            "maxPressure": dmc.MappedPropertyApply(
                name="maxPressure",
                description="Maximum Pump Pressure",
                container=dmc.ContainerId("pumps_space", "PumpSpecificationsContainer"),
                container_property_identifier="maxPressure"
            ),
            "maxTemperature": dmc.MappedPropertyApply(
                name="maxTemperature",
                description="Maximum Pump Temperature",
                container=dmc.ContainerId("pumps_space", "PumpSpecificationsContainer"),
                container_property_identifier="maxTemperature"
            ),
            "rotationConfigurations": dmc.MappedPropertyApply(
                name="rotationConfigurations",
                description="Rotation Configurations",
                container=dmc.ContainerId("pumps_space", "PumpSpecificationsContainer"),
                container_property_identifier="rotationConfigurations"
            )
        }
   )
)
```

The view definition doesn't contain unit details. The unit information is stored in the container definition and automatically inherited by the view.

#### 3. Create data model

We use data models to organize collections of views. Here is an example of code to create a data model using the Python SDK:

```python wrap
from cognite.client import CogniteClient
import cognite.client.data_classes.data_modeling as dmc
client = CogniteClient()

client.data_modeling.data_models.apply(
    dmc.DataModelApply(
        space="pumps_space",
        external_id="PumpManagementModel",
        name="Pump Management Model",
        version="1",
        views=[
            dmc.ViewId(
                space="pumps_space",
                external_id="PumpSpecificationsView",
                version="1"
            )
        ]
    )
)
```

Even though the data modeling service (DMS) API is the recommended way to manage containers, views and data models, it's possible to create them using the **Data modeling extension for GraphQL** (DML).

The equivalent representation in DML for this container/view definition would be:

```graphql wrap
type PumpSpecifications {
  "Maximum Pump Pressure"
  maxPressure: Float @unit(externalId: "pressure:bar", sourceUnit: "BAR")
  "Maximum Pump Temperature"
  maxTemperature: Float @unit(externalId: "temperature:deg_c")
  "Rotation Configurations"
  rotationConfigurations: [Float64] @unit(externalId: "angular_velocity:rev-per-min")
}
```

<Note>
The `@unit` directive associates a property with a specific unit. (This works precisely like assigning units using DMS; the `externalId` argument must correspond to an entry in the unit catalog.)
</Note>

See the [**Data modeling extension for GraphQL** (DML) documentation](/cdf/dm/dm_graphql/dm_data_modeling_language#specification-of-directives) for more information on using DML.

### Querying data models with units

#### Using the data modeling API

When querying data, set `includeTyping` to `true` to retrieve information about the units associated with all properties included in the response.

To add unit conversion into a response, specify either `unit.externalId` or `unit.unitSystemName` for each property requiring conversion within the `targetUnits` argument. When you set `includeTyping` to `true`, the typing information will reflect the units of the data included in the request. For example, if `maxPressure` was stored as `pressure:bar`, a request to convert to `pressure:pa` will result in both the data and typing information being in `pressure:pa`.

When applying a filter to a property included in `targetUnits`, the filter will be unit-aware, ensuring that the value passed on the filter aligns with the unit of the data. For instance, if `maxPressure` was stored as `pressure:bar` and a request to convert to `pressure:pa` with a filter `greater than or equal to 3520000.0` will filter the data to instances where `maxPressure` is greater than or equal to `3520000.0 Pascal`.

<Danger>
Converting values across various units may introduce a minor conversion error in the data. For example, when you convert `1.69999999999` `meters` to `centimeters`, it results in `170` `centimeters`. This discrepancy becomes particularly evident with the application of filters. For instance, when applying a filter for `height less than 170 cm`, one might anticipate results including the original height value of `1.69999999999`, but due to rounding errors in conversion, these results might get excluded.
</Danger>

<Warning>
Be aware of the potential ambiguities when setting target units with filters. For instance, consider a scenario where two different views map to the same property, `propertyA`, but each view uses a separate unit for the property. When you apply a filter on `propertyA` and reference the property through its container (for example, `[space, containerId, property]`), the system cannot determine the correct `source` and, therefore, the appropriate `targetUnit` for the filter. To avoid this ambiguity, explicitly reference `propertyA` in the filter by specifying the view (for example, `[space, viewId/viewVersion, property]`).
</Warning>

All the data modeling query endpoints are updated to support typing information, unit conversion, and unit-aware filtering.

The following section will explain some examples of valid queries.

#### List instances with unit conversion and unit-aware filtering

The example below shows a list instance with unit conversion and unit-aware filtering.

```python wrap
from cognite.client import CogniteClient
import cognite.client.data_classes.data_modeling as dmc
import cognite.client.data_classes.filters as dmf
client = CogniteClient()

instances = client.data_modeling.instances.list(
    instance_type="node",
    sources=dmc.query.SourceSelector(
        source=dmc.ViewId(
            space="pumps_space",
            external_id="PumpSpecificationsView",
            version="1"
        ),
        target_units=[
            dmc.query.TargetUnit(
                property="maxPressure",
                unit=dmc.data_types.UnitReference(
                    external_id="pressure:pa"
                )
            ),
            dmc.query.TargetUnit(
                property="maxTemperature",
                unit=dmc.data_types.UnitSystemReference(
                    unit_system_name="SI"
                )
            ),
            dmc.query.TargetUnit(
                property="rotationConfigurations",
                unit=dmc.data_types.UnitSystemReference(
                    unit_system_name="SI"
                )
            )
        ]
    ),
    filter=dmf.Range(
        property=("pumps_space", "PumpSpecificationsView/1", "maxPressure"),
        gte=3520000.0
    ),
    include_typing=True
)
```

#### Query instances with unit conversion and unit-aware filtering

The example below shows a query instance with unit conversion and unit-aware filtering.

```python wrap
from cognite.client import CogniteClient
import cognite.client.data_classes.data_modeling as dmc
import cognite.client.data_classes.filters as dmf
client = CogniteClient()

instances = client.data_modeling.instances.query(
    query=dmc.query.Query(
        with_ = {
            "pumpsHighPressure": dmc.query.NodeResultSetExpression(
                filter=dmf.Range(
                    property=("pumps_space", "PumpSpecificationsView/1", "maxPressure"),
                    gte=3520000.0
                ),
                limit=1000
            )
        },
        select = {
            "pumpsHighPressure": dmc.query.Select(
                sources=[
                    dmc.query.SourceSelector(
                        source=dmc.ViewId("pumps_space", "PumpSpecificationsView", "1"),
                        properties=["maxPressure", "maxTemperature", "rotationConfigurations"],
                        target_units=[
                            dmc.query.TargetUnit(
                                property="maxPressure",
                                unit=dmc.data_types.UnitReference(
                                    external_id="pressure:pa"
                                )
                            ),
                            dmc.query.TargetUnit(
                                property="maxTemperature",
                                unit=dmc.data_types.UnitSystemReference(
                                    unit_system_name="SI"
                                )
                            ),
                            dmc.query.TargetUnit(
                                property="rotationConfigurations",
                                unit=dmc.data_types.UnitSystemReference(
                                    unit_system_name="SI"
                                )
                            )
                        ]
                    )
                ],
                limit=1000
            )
        }
    ),
    include_typing=False
)
```

#### Aggregate data across nodes/edges with unit conversion and unit-aware filtering

The example below shows aggregate data across nodes/edges with unit conversion and unit-aware filtering.

```python wrap
from cognite.client import CogniteClient
import cognite.client.data_classes.data_modeling as dmc
import cognite.client.data_classes.filters as dmf
client = CogniteClient()

aggregates = client.data_modeling.instances.aggregate(
    instance_type="node",
    view=dmc.ViewId(space="pumps_space", external_id="PumpSpecificationsView", version="1"),
    aggregates=[
        dmc.aggregations.Avg("maxPressure"),
        dmc.aggregations.Avg("maxTemperature"),
        dmc.aggregations.Count("rotationConfigurations")
    ],
    filter=dmf.Range(
        property=("pumps_space", "PumpSpecificationsView/1", "maxPressure"),
        gte=3520000.0
    ),
    target_units=[
        dmc.query.TargetUnit(
            property="maxPressure",
            unit=dmc.data_types.UnitReference(
                external_id="pressure:pa"
            )
        ),
        dmc.query.TargetUnit(
            property="maxTemperature",
            unit=dmc.data_types.UnitSystemReference(
                unit_system_name="SI"
            )
        ),
        dmc.query.TargetUnit(
            property="rotationConfigurations",
            unit=dmc.data_types.UnitSystemReference(
                unit_system_name="SI"
            )
        )
    ],
    limit=100
)
```

<Warning>
Query parameter values are not unit-aware and are considered in the same unit as the underlying property. No unit conversion will occur if you provide a filter property through query parameters.
</Warning>

#### Using GraphQL

Apart from querying data using the DMS endpoints, use GraphQL to fetch data from views and time series data points. The GraphQL API is updated to support unit conversion and unit-aware filtering. See the below examples below for some valid queries.

#### List query with unit conversion and unit-aware filtering

The example below shows a list query with unit conversion and unit-aware filtering.

```graphql wrap
query MyQuery {
  # filter on maxPressure -> only 1 entry has maxPressure >= 3520000.0 Pa
  listPumpSpecifications(filter: { maxPressure: { gte: 3520000.0 } }) {
    items {
      # return maxPressure and convert its values and the filter to Pa
      maxPressure(targetUnit: "pressure:pa")
      # return maxTemperature and convert its values to the SI unitSystem
      maxTemperature(targetUnitSystem: "SI")
      # return rotationConfigurations and convert its values to the SI unitSystem
      rotationConfigurations(targetUnitSystem: "SI")
    }
    # the units which are associated with the values returned in the request
    unitInfo {
      maxPressure {
        # externalId of the unit
        unitExternalId
        # free text unit alias
        sourceUnit
      }
      maxTemperature {
        unitExternalId
      }
      rotationConfigurations {
        unitExternalId
      }
    }
  }
}
```

#### Aggregate query with unit conversion and filtering

The example below shows an aggregate query with unit conversion and filtering.

```graphql wrap
query MyQuery {
  # filter on maxPressure -> only 1 entry has maxPressure >= 3520000.0 Pa
  aggregatePumpSpecifications(filter: {maxPressure: {gte: 3520000.0}}) {
    items {
      avg {
        maxPressure(targetUnit: "pressure:pa")
        maxTemperature(targetUnitSystem: "SI")
      }
      count {
        rotationConfigurations
      }
    }
    unitInfo {
      avg {
        maxPressure {
          unitExternalId
          sourceUnit
        }
        maxTemperature {
          sourceUnit
          unitExternalId
        }
        rotationConfigurations {
          sourceUnit
          unitExternalId
        }
      }
    }
  }
}
```

#### Time series data points query with unit conversion

The example below shows time series data points query with unit conversion.

```graphql wrap
query MyQuery($startTime: Int64!, $endTime: Int64!) {
  # select only one instance based on the externalId
  listSensorList( filter: { externalId: { eq: "TI-FORNEBU-02" } } ) {
    items {
      # externalId of the node
      externalId
      # property included in the view
      name
      sensor {
        # externalId of the "sensor" time series
        externalId
        # name of the "sensor" time series
        name
        # free text unit assigned to the "sensor" time series
        unit
        # unitExternalId of the "sensor" time series where the data is stored
        unitExternalId
        getDataPoints(targetUnitSystem: "SI", start: $startTime, end: $endTime, granularity: "1h", aggregates: AVERAGE ) {
          # unitExternalId of the datapoints returned in the request
          unitExternalId
          # datapoint items
          items {
            timestamp
            average
          }
        }
      }
    }
  }
}
```

For this query, we are using the following view definition:

```graphql wrap
type SensorList @view(version: "1") {
    name: String
    sensor: TimeSeries
}
```

<Note>
You need to define the variables `startTime` and `endTime` before executing the query.
</Note>

For more detailed information, visit:
- [Units API documentation](https://api-docs.cognite.com/20230101/#tag/Units)
- [Data models API documentation](https://api-docs.cognite.com/20230101/#tag/Data-Modeling)
- [Time teries API documentation](https://api-docs.cognite.com/20230101/#tag/Time-series)

<!-- SOURCE_END: docs.cognite.com/docs\dev\concepts\resource_types\units\units.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\guides\advanced-query\advanced_query_intro.mdx -->
## File: docs.cognite.com/docs\dev\guides\advanced-query\advanced_query_intro.mdx

---
title: 'Advanced querying'
description: 'Learn how to use advanced query language (AQL) for search, filter, sort, and aggregation operations on CDF resources.'
content-type: 'concept'
audience: [developer, dataEngineer, dataScientist]
experience-level: 100
article-type: 'article'
lifecycle: use
---
Advanced query language (AQL) consists of a set of features that provides additional functionality to `Search`, `Filter`, `Sort`, and `Aggregations` for the following core CDF resource types:

- [Assets](/dev/concepts/resource_types/assets)
- [Events](/dev/concepts/resource_types/events)
- [Time Series](/dev/concepts/resource_types/timeseries)
- [Sequences](/dev/concepts/resource_types/sequences)
- [Documents](https://api-docs.cognite.com/20230101/tag/Documents)

<Info>
This article covers the improvements made to query functionality for Assets, Events, Time Series, Sequences, and Documents.
</Info>

With the introduction of AQL, you can perform advanced query operations against the JSON-based metadata associated with these resource types.

Use advanced queries for human interaction operations such as Data Exploration, Review, and Analysis. Advanced queries leverage a search engine, which is eventually consistent. It takes some time for the uploaded data to be available for search and filter operations and is relatively slow compared with CRUD endpoints. We do not recommend using them for large-scale batch read jobs (for example, to synchronize data from CDF to another database).

The CRUD operations like `/create`, `/retrieve : /getbyid`, `/update`, and `/delete` do not involve a search engine and are conducted against the database backends of the services and are fast and immediately consistent. You can retrieve the uploaded data instantly via these endpoints.


<!-- SOURCE_END: docs.cognite.com/docs\dev\guides\advanced-query\advanced_query_intro.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\guides\advanced-query\aggregation_types.mdx -->
## File: docs.cognite.com/docs\dev\guides\advanced-query\aggregation_types.mdx

---
title: 'Aggregation types'
description: 'Learn about advanced aggregation APIs including count, cardinality, unique values, and unique properties for contextualizing data.'
content-type: 'reference'
audience: [developer, dataEngineer]
experience-level: 200
article-type: 'article'
lifecycle: use
---
Advanced aggregation APIs built on top of extended advanced filtering APIs support complex queries that combine simple operations, such as `equals`, `prefix`, `exists`, etc., using boolean operators `and`, `or`, and `not`. It also supports aggregate result filtering. The main goal is to contextualize user experience (for example, update available filters based on search queries) and provide the following capabilities to basic properties and metadata.

Below is an overview of aggregations and explicit examples of each type.

## Overview

Let's use the simplified input data set below:

```js
{"a" : "1", b : "1", c: "2"}
{"a" : "2", c : "2"}
{"c" : "2", d : "1"}
{"a" : "3", d : "2"}
```

Now, apply different aggregation types to the input data:

```js
Count:                  4
Count for property:     {"a": 3, "b": 1, "c": 3, "d": 2}

Cardinality properties: 4
Cardinality values:     {"a": 3, "b": 1, "c": 1, "d": 2}

Unique properties:      {"a": 3, "b": 1, "c": 3, "d": 2}
Unique values:          {
                          "a": {"1": 1, "2": 1, "3": 1},
                          "b": {"1": 1},
                          "c": {"2": 3},
                          "d": {"1": 1, "2": 1}
                        }
```

<Note>
Bucket aggregations (`uniqueProperties`, `uniqueValues`) return properties and values with frequency (how many times a property appears in the input set or how many properties with a value are in the input set).
</Note>

## Types

Let's look at the examples for each aggregation type.

### count
Returns the count of items in the data set (i.e., number of Assets, Events, Time Series, etc.).
For example, four items, A, B, C, and D, are present in the data set, so the count = 4.

```js wrap
========== REQUEST ===========
{
  "filter": { ... },
  "advancedFilter": { ... },
  "aggregate": "count"
}

========== RESPONSE ==========
{
  "items": [
    {
      "count": 986294
    }
  ]
}
```

<Note>
Count supports optional *properties* attribute as well.
</Note>

### cardinalityValues
Returns the number of instances that a property occurs in the data set.

```js wrap
========== REQUEST ===========
{
  "filter": { ... },
  "advancedFilter": { ... },
  "aggregate": "cardinalityValues",
  "properties": [
    {
      "property": ["type"]
    }
  ]
}
========== RESPONSE ==========
{
  "items": [
    {
      "count": 18
    }
  ]
}
```

### cardinalityProperties
Returns the number of unique properties per metadata key in the data set.

```js wrap
========== REQUEST ===========
{
  "filter": { ... },
  "advancedFilter": { ... },
  "aggregate": "cardinalityProperties",
  "path": ["metadata"]
}

========== RESPONSE ==========
{
  "items": [
    {
      "count": 13
    }
  ]
}
```

### uniqueValues
Returns an array of the unique instances of metadata values per metadata key and the associated count of their occurrence.

```js wrap
========== REQUEST ===========
{
  "filter": { ... },
  "advancedFilter": { ... },
  "aggregate": "uniqueValues",
  "properties": [
    {
      "property": ["metadata"]
      // The 'filter' attribute has a higher priority than 'aggregateFilter'.
      "filter": {"prefix": {"value": "A"}} 
    }
  ],
  "aggregateFilter": {"prefix": {"value": "whatever"}}
}

========== RESPONSE ==========
{
  "items": [
    {
      "count": 7014, 
      "values": ["A01"]
    },
    ...,
    {
      "count": 22,
      "values": ["A27"]
    }
  ]
}
```

{/*_Note: Count is the number of properties (in all items) with the values. It's different than the number of items as soon as one item can have multiple properties (metadata keys) with the same value. The values are ordered by frequency, e.g., how many documents have a particular value._*/}

### uniqueProperties
Returns the number of unique properties that occur in the data set.

```js wrap
========== REQUEST ===========
{
  "filter": { ... },
  "advancedFilter": { ... },
  "aggregate": "uniqueProperties",
  "path": ["metadata"],
  "aggregateFilter": {"or":[
    {"prefix": {"value": "n"}},
    {"prefix": {"value": "w"}},
  ]}
}

========== RESPONSE ==========
{
  "items": [
    {
      "count": 8014,
      "values": [{"property": ["metadata", "notificationType"]}]
    },
    ...,
    {
      "count": 14,
      "values": [{"property": ["metadata", "workOrderNumber"]}]
    }
  ]
}
```


<!-- SOURCE_END: docs.cognite.com/docs\dev\guides\advanced-query\aggregation_types.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\guides\advanced-query\best_practices_and_limits.mdx -->
## File: docs.cognite.com/docs\dev\guides\advanced-query\best_practices_and_limits.mdx

---
title: 'Best practices and limits'
description: 'Learn about best practices, pagination, rate limiting, and restrictions for advanced query operations in CDF.'
content-type: 'reference'
audience: [developer, dataEngineer]
experience-level: 100
article-type: 'article'
lifecycle: use
---
This page summarizes the best development practices about existing limits and restrictions for advanced queries.

## Pagination and partitions

CDF supports a throughput of 1,000 items per request. For increased concurrency, use the appropriate number of partitions required, up to a maximum of 10. 

For more information, see [Pagination](/dev/concepts/pagination).

## Rate and concurrency limiting

CDF enforces rate and concurrency limits to provide service protection and guarantee allocated capacity and fair usage policy per project.

For more information, see [Request throttling](/dev/concepts/resource_throttling).

## Limits and restrictions

Search, filtering, and aggregation requests are computationally expensive compared to  CRUD operations. Hence, they are subject to lower resource limits. 

For more detailed information on rate and concurrency limits, see below:

- [Assets](https://api-docs.cognite.com/20230101/#tag/Assets)
- [Events](https://api-docs.cognite.com/20230101/#tag/Events)
- [Aggregate time series](https://api-docs.cognite.com/20230101/#tag/Time-series/operation/aggregateTimeSeries)
- [Aggregate sequences](https://api-docs.cognite.com/20230101/#tag/Sequences/operation/aggregateSequences)
- [Search for documents](https://api-docs.cognite.com/20230101/#tag/Documents/operation/documentsSearch)



<!-- SOURCE_END: docs.cognite.com/docs\dev\guides\advanced-query\best_practices_and_limits.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\guides\advanced-query\filtering.mdx -->
## File: docs.cognite.com/docs\dev\guides\advanced-query\filtering.mdx

---
title: 'Filtering'
description: 'Learn how to build complex queries with boolean logic, leaf filters, and advanced query operations in CDF.'
content-type: 'reference'
audience: [developer, dataEngineer]
experience-level: 200
article-type: 'article'
lifecycle: use
---
Cognite Data Fusion (CDF) supports boolean logic using _AND_, _OR_, and _NOT_ to build complex queries. You can also combine resource item attribute filters, such as _equals_, _prefix_, and _range_.

The default set of CDF filters lets you filter all CDF resources in these contexts:

- **CDF resources**, for example, assets and events, by primary fields, metadata keys, and values.

- **Full-text search results**. Use filters to narrow down the returned search results. For example, you can use a filter to return only "PDFs from last week" from your full-text search query.

- **Aggregate results**. Use filters to reduce the number of returned aggregate buckets. For example, you can use a filter to return "metadata key names and their count for all keys with the prefix character '\_'.

## Serialization/deserialization

You can express filters as JSON. The root must always be an object.

## Properties

CDF uses the same grammar to express a reference everywhere the API uses a resource attribute: a JSON array of strings to express the attribute/property. This is true for _leaf filters_, _aggregate_, _sort_, _etc_.

There are no naming constraints for the allowed property names. CDF uses "property" for **leaf filters** to refer to the field(s) the filter operates on.

### Example: nesting and sorting

In the example object below, you can refer to the nested values as `["metadata", "../type"]`. Or, you can use another nested property, `["metadata", "type"]`, and then address the un-nested property as `["type"]`.

```json wrap
{
  "id": 1234,
  "metadata": {
    "../type": "critical",
    "type": "high"
  },
  "type": "alert"
}
```

To sort the `/list` result on _metadata.type_ and _type_ properties, send the following request:

```json wrap
{
  "sort": [
    {
      "property": ["metadata", "type"],
      "order": "asc",
      "nulls": "last"
    },
    {
      "property": ["type"],
      "order": "desc"
    }
  ]
}
```

<Note>
You can sort by several properties in the same query.
</Note>

### Example: filtering

This example defines a filter using JSON:

```json wrap
{
  "or": [
    {
      "not": {
        "and": [
          {
            "equals": {
              "property": ["type"],
              "value": "alert"
            }
          },
          {
            "in": {
              "property": ["subtype"],
              "values": ["critical", "warning"]
            }
          },
          {
            "range": {
              "property": ["rating"],
              "gte": 5,
              "lt": 10
            }
          }
        ]
      }
    },
    {
      "and": [
        {
          "equals": {
            "property": ["metadata", "priority"],
            "value": "highest"
          }
        },
        {
          "equals": {
            "property": ["subtype"],
            "value": "critical"
          }
        }
      ]
    }
  ]
}
```

A simplified, less verbose version could look like this:

```json wrap
(
    ("metadata.priority"=highest AND subtype=critical)
    OR
    (
        NOT (type=alert AND subtype IN (critical, warning) AND 5 `<= rating `< 10)
    )
)
```

Even if the JSON object is more verbose, the object is easy to express and handle programmatically. JSON is also **unambiguous** and doesn't impose rules on naming attributes/properties. You also can't mistake a property name for a keyword in JSON.

Below is another example of using JSON to express a simple intent: "_filter items where the attribute `source` is equal to the value `git`_."

```json wrap
{
  "equals": {
    "property": ["source"],
    "value": "git"
  }
}
```

## Filter language specification

| Filter      | Syntax                                                                                                    |
| ----------- | --------------------------------------------------------------------------------------------------------- |
| filter       | `<bool_filter>` |                                                                            |
| bool_filter  | `<and>` | |                                                                                |
| and         | `{"and": [<filter>, …]}`                                                                                   |
| or          | `{"or": [<filter>, …]}`                                                                                    |
| not         | `{"not": <filter>}`                                                                                        |
| property    | `<list_of_strings>`                                                                                       |
| leaf_filter  | `<equals>` \| `<in>` \| `<range>` \| `<prefix>` \| `<exists>` \| `<containsAny>` \| `<containsAll>`        |
| equals      | `{"equals": {"property": <property>, "value": <string_value>}}`                                           |
| in          | `{"in": {"property": <property>, "values": <list_value>}}`                                                |
| range       | `{"range": {"property": <property>, "(gte\|gt\|lte\|lt)": <value1> [, "(gte\|gt\|lte\|lt)": <value\2>]}}` |
| prefix       | `{"prefix": {"property": <property>, "value": <string_value>}}`                                            |
| exists      | `{"exists": {"property": <property>}}`                                                                    |
| containsAny | `{"containsAny": {"property": <property>, "values": <list_value>}}`                                       |
| containsAll | `{"containsAll": {"property": <property>, "values": <list_value>}}`                                       |

<Info>
Different CDF resources may support a different subset of the existing leaf filters. Refer to the documentation for the specific CDF resource for details.
</Info>

## Boolean filters

### AND

To express boolean **AND** logic, use the `and` filter expressed in JSON:

`{"and": [ filter1, filter2, …, filterN]}`

### OR

To express boolean **OR** logic, use the `or` filter (expressed in JSON):

`{"or": [ filter1, filter2, …, filterN]}`

### NOT

To express the negated logic of a boolean value, use the `not` filter (expressed in JSON):

`{"not": filter}`

## Leaf filters

<Note>
The supported leaf filters may differ depending on the CDF resource.
</Note>

### Equals

Filter items where the _property_ (named value) equals the specified value. This filter works for properties with values that aren't arrays/lists.

Filter items where the _property_ (name) equals the given name and has a specific value. This filter works for properties with values that aren't arrays/lists.

```json
{
  "equals": {
    "property": ["rootId"],
    "value": 12432432423
  }
}
```

### In

Filter items where the _property_ value equals one of the values specified in the list. This filter expects the value stored by the property to be a single value (not a list).

```json
{
  "in": {
    "property": ["datasetId"],
    "values": [1, 2, 3]
  }
}
```

### Range

Filter items where the _property_ value is in the range specified by the filter boundaries: _gt_|_gte_,|_lt_|_lte_.

```json
{
  "range": {
    "property": ["count"],
    "gte": 1,
    "lte": 2
  }
}
```

To compare the property, you must specify _gt_, _lt_, _lte_, or _gte_, and at least one boundary value.

Range behavior depends on the type of the property you are comparing with. The filter language does not require that typing must exist. If you store all the values as strings, the range filter will behave as if comparing string values.

This filter works for properties not typed as arrays/lists.

<Note>
Some CDF resources don't support open-range filters. To avoid errors, specify both upper and lower boundaries. See the API documentation for details.
</Note>

### Prefix

Filter items where the _property_ value starts with the specified string. This filter works for properties that are not of type array/list.

```json
{
  "prefix": {
    "property": ["metadata", "vendor"],
    "value": "si"
  }
}
```

### Exists

Filter items where the _property_ value is something other than `null` (empty).
This filter works for properties of any type.

```json
{
  "exists": {
    "property": ["externalId"]
  }
}
```

### ContainsAny

Filter items where the _property_ values equal one or more of the specified values from the filter.
This filter works for properties that are of type array/list.

```json
{
  "containsAny": {
    "property": ["labels"],
    "values": ["pump", "tube"]
  }
}
```

### ContainsAll

Filter items where the values from the _property_ match all specified values.
This filter works for properties that are of type array/list.

```json
{
  "containsAll": {
    "property": ["assetIds"],
    "values": [23234324, 2423423]
  }
}
```

## Advanced Query filters

Advanced queries provide more robust data discovery and retrieval tools using `Search`, `Filter`, and `Aggregation` methods. Advanced search and filter API supports complex queries that combine simple operations, such as `equals`, `prefix`, `exists`, etc., using boolean operators `and`, `or`, and `not`. These methods apply to standard fields as well as metadata.

In the following section, we will outline the concepts and applications of different types of query filters with their API specifications for each supported service.

Since the filter operations leverage the CDF search architecture, they are consistent, expensive, and slower than CRUD operations. Consult with a professional on resource allocation and consumption.

### /filter

Use `/filter` for human and analytical queries, not bulk read or system-to-system sync operations.

### /list

Since `/list` provides a subset of the functionality available in `/filter`, use the `/filter` endpoint rather than `/list`.

For more information, see [List](https://api-docs.cognite.com/20230101/tag/Assets/operation/getAssets).

### /search

`/search` returns results sorted by relevance score. Relevance score is automatically calculated by the CDF search architecture using a combination of text matching and scoring algorithms to determine the relevance of search results. The text-matching algorithm checks if the search query terms appear in the item's fields, such as title, description, or content. Searches can return high numbers of results.


<Tip>
The `/filter` operation supersedes the`/search` functionality since it provides a fuzzy search operation within itself. Use the `/filter` operation for these types of queries.
</Tip>

For more information on how search works for Docs API, see [Search for documents](https://api-docs.cognite.com/20230101/tag/Documents/operation/documentsSearch).

### /aggregate

Advanced aggregation APIs provide more powerful tools for data discovery and enhance the creation of better user interfaces and experiences. The supported aggregation types include the following:

| **Aggregation type**   |      **Description**     |
|----------|:-------------|
| count |  Returns the count of items in the data set (i.e., number of Assets, Events, Time Series, etc.) |
| count (with properties) |  Returns the number of items in the data set with associated properties. It is useful if items in the data set have no associated properties.  |
| cardinalityValues |  Returns the number of instances that a property occurs in the data set |
| cardinalityProperties |  Returns the number of unique properties per metadata key that occur in the data set |
| uniqueValues |  Returns an array of the unique instances of metadata values per metadata key and the associated count of their occurrence |
| uniqueProperties |  Returns the number of unique properties that occur in the data set |

For more information, see [Aggregations in CDF](/dev/concepts/aggregation).


<!-- SOURCE_END: docs.cognite.com/docs\dev\guides\advanced-query\filtering.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\guides\ai\document_qa.mdx -->
## File: docs.cognite.com/docs\dev\guides\ai\document_qa.mdx

---
title: 'Document question answering'
description: 'Learn how to use the AI-powered Document question answering API to ask questions about PDF documents uploaded to CDF.'
content-type: 'procedure'
audience: [developer, aiEngineer, dataScientist]
experience-level: 200
article-type: 'article'
lifecycle: use
---
The Document question answering is an AI-powered assistant that, with the help of the [Semantic search API](/dev/guides/ai/semantic_search), helps you find relevant information for any question and passes the retrieved information into an LLM for interpretation into a natural language answer.

This article shows you how to implement the Document question answering API, upload a PDF to Cognite Data Fusion (CDF), and use this API to ask questions about the uploaded document. All PDF documents uploaded to CDF automatically pass through a Retrieval Augmented Generation (RAG) pipeline. The documents are parsed and OCRed, and all the contained information is indexed for the Semantic search API.


## Implement Document question answering

You can implement the Document question answering API in the following way:

  1. Pass the question and the list of file IDs to the Semantic search API and get a list of passages back.

  2. Construct a prompt from the original question and the list of passages.

  3. Pass the prompt to an LLM and get a natural language answer.

  4. Return the answer from the LLM.

<Steps>
<Step title="Upload PDF">
  You can upload a PDF file to CDF one of the following ways:

  * Go to **_CDF_** > **_Industrial tools_** > **_Canvas_** and drag your PDF file to the canvas or upload existing files by selecting **_+ Add data_**.  
   
  * Go to **_CDF_** > **_Industrial tools_** > **_Data explorer_** > **_Files_** and select **_Upload_**.

  * Use the Python code.

  ```python wrap
  response1 = client.files.upload(path="./well_report.pdf")
  document_id = response1.id
  print(document_id)
  ```
</Step>

<Step title="Ask questions">
  Once the document is uploaded, we can start asking our questions.

  It may take some time before the document has been fully processed
  and ready so we wrap the API call in a while loop, so that we can
  retry until we get our answer.

  ```python wrap
  import json
  import time

  ask_path = f"/api/v1/projects/{client.config.project}/ai/tools/documents/ask"

  body = {
      "question": "Where is the Volve field located?",
      "fileIds": [
          {
              "id": document_id
          }
      ]
  }

  while True:
      try:
          response2 = client.post(ask_path, json=body).json()
          break

      except CogniteAPIError as e:
          if e.code == 422 and len(e.missing) > 0:
              print("Not ready yet, waiting 5 seconds...")
              time.sleep(5)
              continue

          # re-raise any unexpected exceptions
          raise

  print(json.dumps(response2, indent=2))
  ```

  See the response for the test file.

  ```json wrap
{
  "content": [
    {
      "text": "The Volve field is located in the southern part of the North Sea, approximately eight kilometers north of Sleipner.",
      "references": [
        {
          "fileId": 7743081064762478,
          "fileName": "well_report.pdf",
          "locations": [
            {
              "pageNumber": 4,
              "left": 57.59,
              "right": 60.66,
              "top": 43.58,
              "bottom": 53.54
            }
          ]
        }
      ]
    }
    ]
  }
  ```

  The response is more than a simple textual answer. The response structure allows for a multi-part answer, where each part of the answer can have one or more references to the document locations that were used to build the answer. If you are not interested in showing these references, you can iterate over the `content` array and combine all the `text` fields.
</Step>
</Steps>


<!-- SOURCE_END: docs.cognite.com/docs\dev\guides\ai\document_qa.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\guides\ai\document_summarization.mdx -->
## File: docs.cognite.com/docs\dev\guides\ai\document_summarization.mdx

---
title: 'Document summary'
description: 'Learn how to use the AI-powered Document summary API to create summaries for PDF documents uploaded to CDF.'
content-type: 'procedure'
audience: [developer, aiEngineer, dataScientist]
experience-level: 200
article-type: 'article'
lifecycle: use
---
Document summary is an AI-powered assistant that helps you create a summary for one or a maximum of 100 documents in .pdf format. You can upload PDF files to Cognite Data Fusion (CDF) and use the Document summary API to create a document summary.


<Steps>
<Step title="Upload PDF">
  You can upload a PDF file to CDF one of the following ways:

  * Go to **_CDF_** > **_Industrial tools_** > **_Canvas_** and drag your PDF file to the canvas or upload existing files by selecting **_+ Add data_**.  
    

  * Go to **_CDF_** > **_Industrial tools_** > **_Data explorer_** > **_Files_** and select **_Upload_**.

  * Use the Python code.

  ```python wrap
  response1 = client.files.upload(path="./well_report.pdf")
  document_id = response1.id
  print(document_id)
  ```
</Step>

<Step title="Create summary">
  Once the document is uploaded, we can start creating the summary.

  It may take some time before the document has been fully processed and ready,
  so we wrap the API call in a while-loop, so that we can retry until we get our answer.

  ```python wrap
  import json
  import time

  ask_path = f"/api/v1/projects/{client.config.project}/ai/tools/documents/summarize"

  body = {
      "items": [
          {
              "id": document_id
          }
      ]
  }

  while True:
      try:
          response2 = client.post(ask_path, json=body).json()
          break

      except CogniteAPIError as e:
          if e.code == 422 and len(e.missing) > 0:
              print("Not ready yet, waiting 5 seconds...")
              time.sleep(5)
              continue

          # re-raise any unexpected exceptions
          raise

  print(json.dumps(response2, indent=2))
  ```

  See the result of the test file.

  ```json wrap
  {
    "items": [
      {
        "id": 6716860071641521,
        "summary": "This document is a well summary report for the Volve F well in Norway. It provides information on the site position, well position, wellpath survey depths, and wellpath plan depths. The report includes details such as latitude, longitude, position uncertainty, water depth, wellhead depth, and survey dates. It also mentions the survey tools used for each survey. The document is dated April 11, 2018."
      }
    ]
  }
  ```
</Step>
</Steps>


<!-- SOURCE_END: docs.cognite.com/docs\dev\guides\ai\document_summarization.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\guides\ai\semantic_search.mdx -->
## File: docs.cognite.com/docs\dev\guides\ai\semantic_search.mdx

---
title: 'Semantic search'
description: 'Learn how to use the Semantic search API to find texts with similar semantic meaning in PDF documents using vector embeddings.'
content-type: 'procedure'
audience: [developer, aiEngineer, dataScientist]
experience-level: 200
article-type: 'article'
lifecycle: use
---
The Semantic search API helps you build AI systems that use data from documents. With the API, you can search for texts with similar semantic meaning, unlike the search where you search for keywords, and find relevant texts written in other languages.

The Semantic search API calculates Vector Embeddings for all PDF documents uploaded to Cognite Data Fusion (CDF). You can read more about [embeddings](https://platform.openai.com/docs/guides/embeddings) and [AIs multitool vector embeddings](https://cloud.google.com/blog/topics/developers-practitioners/meet-ais-multitool-vector-embeddings).

Vector embeddings are created as part of the built-in Retrieval Augmented Generation (RAG) pipeline. All PDF documents you upload to CDF are parsed and OCRed, and the extracted text is divided into suitably sized passages that are indexed into a vector store.

This article explains how to upload a PDF and use the Semantic search API to find relevant passages for a user question.


<Steps>
<Step title="Upload PDF">
  You can upload a PDF file to CDF one of the following ways:

  * Go to **_CDF_** > **_Industrial tools_** > **_Canvas_** and drag your PDF file to the canvas or upload existing files by selecting **_+ Add data_**.  
    

  * Go to **_CDF_** > **_Industrial tools_** > **_Data explorer_** > **_Files_** and select **_Upload_**.

  * Use the Python code.

  ```python wrap
  response1 = client.files.upload(path="./well_report.pdf")
  document_id = response1.id
  print(document_id)
  ```
</Step>

<Step title="Processing">
  Once you've uploaded the file, wait for it to pass through the RAG pipeline. You can use the Document status API to poll the status.

  ```python wrap
  import time

  status_path = f"/api/v1/projects/{client.config.project}/documents/status"

  body = {
      "items": [
          {
              "id": document_id
          }
      ]
  }

  while True:
      response2 = client.post(status_path, json=body, headers={"cdf-version": "beta"}).json()

      status = response2["items"][0]["passages"]["status"]
      print(f"status: {status}")

      if status in {"waiting", "running"}:
          time.sleep(5)
          continue

      break
  ```
</Step>

<Step title="Search">
  Once the document is fully indexed, search for the the relevant pasages with the Python code.

  ```python wrap
  import json

  search_path = f"/api/v1/projects/{client.config.project}/documents/passages/search"

  body = {
      "limit": 3,
      "filter": {
          "and": [
              {
                  "equals": {
                      "property": ["document", "id"],
                      "value": document_id
                  }
              },
              {
                  "semanticSearch": {
                      "property": ["content"],
                      "value": "Where is the Volve field located?"
                  }
              }
          ]
      }
  }

  response3 = client.post(search_path, json=body).json()

  print(json.dumps(response3, indent=2))
  ```

  See the response for the test file.

  ```json wrap
  {
    "items": [
      {
        "document": {
          "id": 8482943812996165,
        },
      "text": " 1. Introduction\n Well: 15/9-F-14\n 1.1. Purpose of the project\n Volve is an oil field located in the southern part of the North Sea approximately eight kilometres north of Sleipner \u00d8st. The sea depth is approximately 90 metres. The development concept is a jack-up processing and drilling facility and a vessel for storing stabilized oil.\n The reservoir contains oil in a combined stratigraphic and structural trap with Jurassic and Triassic sandstones in the Hugin formation. The western part of the structure is heavily faulted and it is uncertain if there is communication across the faults.\n Volve will be recovered by water injection.\n The rich gas will be transported to Sleipner A and exported onwards from there.\n Well F-14 is designed as an oil producer in the Volve development drilling program. The F-14 well is the second oil producer on the Volve Field. The well is located in a structural high position on the crest of the structure 800m up flank of the NO 15/9-19A discovery well.\n Main objectives:\n To establish an oil production well draining the northern and northwest flank of the Volve structure, the reservoir being the Hugin Formation.\n The upper fault block will be pressure supported by water injection in the F-4 and the F-5 Injection wells, whereas the lower fault block will receive support only from F-5.\n Other objectives:\n Collect data for planning of future wells and optimalization of production and well design.\n \ufffd \ufffd Collect pressure data.\n \ufffd \ufffd Logs, inclusive velocity.\n \ufffd \ufffd Stratigraphy, lithology, reservoir data.\n 3",
      "locations": [
        {
          "pageNumber": 3,
          "left": 57.59,
          "right": 60.66,
          "top": 43.58,
          "bottom": 53.54
        }
      ]
    },
    ...
    ]
  }
  ```

  The response is a list of matching passages from the given documents. In addition to the relevant text, they contain the page numbers and bounding boxes where you can find the text. The response returns the passages in the order of relevance, where the top passage is the most relevant.
</Step>
</Steps>


<!-- SOURCE_END: docs.cognite.com/docs\dev\guides\ai\semantic_search.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\guides\iam\authentication.mdx -->
## File: docs.cognite.com/docs\dev\guides\iam\authentication.mdx

---
title: 'Authentication'
description: 'Learn how authentication validates user identity in Cognite Data Fusion using OAuth2 tokens and identity providers.'
content-type: 'concept'
audience: [developer, administrator]
experience-level: 200
article-type: 'article'
lifecycle: use
---
Authentication is the process of validating claims of identity, and is the first half of establishing whether a user is a user in Cognite Data Fusion (CDF) and what they should have access to.

Requests to Cognite Data Fusion are authenticated as submitted by a user using OAuth2 tokens.

Once a token is validated and the associated Cognite Data Fusion user is determined, [Authorization](/dev/guides/iam/authorization) takes place.

## Tokens

When a user logs in through a web browser, they're sent to the Identity Provider (IdP) configured for the project (an OAuth2 provider, almost always Entra ID or Google) using the authorization code grant flow. See [external application integration](/dev/guides/iam/external-application).

Adding a `Authorization` header with the token as follows will authenthicate the request:

```sh wrap
$ curl -X GET \
  'https://api.cognitedata.com/api/v1/token/inspect' \
  -H 'Accept: application/json' \
  -H 'Authorization: Bearer ewogICJhbGciOiAiUlMyNTYiLAogICJ0eXAiOiAiSldUIgp9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWUsImlhdCI6MTUxNjIzOTAyMn0.TCYt5XsITJX1CxPCT8yAV-TVkIEq_PbChOMqsLfRoPsnsgw5WEuts01mq-pQy7UJiN5mgRxD-WUcX16dUEMGlv50aqzpqh4Qktb3rk-BuQy72IFLOqV0G_zS245-kronKb78cPN25DGlcTwLtjPAYuNzVBAh4vGHSrQyHUdBBPM'
```


<!-- SOURCE_END: docs.cognite.com/docs\dev\guides\iam\authentication.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\guides\iam\authorization.mdx -->
## File: docs.cognite.com/docs\dev\guides\iam\authorization.mdx

---
title: 'Authorization'
description: 'Learn how role-based access control governs access to resources in Cognite Data Fusion using groups, capabilities, and security categories.'
content-type: 'concept'
audience: [developer, administrator]
experience-level: 100
article-type: 'article'
lifecycle: use
---
Access to resources in Cognite Data Fusion (CDF) is governed by role-based access control. Principals are members of groups ([IAM](/dev/guides/iam/index)), which confer capabilities. These capabilities are used to determine if an action should succeed or fail.

## Group membership

A principal is associated with, or determined to be a member of, one or more groups are managed in Entra ID:

1. Administrators manage users, groups, and users' group membership in their Entra ID instance.
2. In Cognite Data Fusion, administrators have created a group with a sourceId matching the Entra ID GUID of each Entra ID group intended to give a user access to Cognite Data Fusion.
3. A user makes a request against Cognite Data Fusion, the user is authenticated against Entra ID, and then:

   - Entra ID is queried for which groups the user is currently member of.
   - The groups are resolved against groups in Cognite Data Fusion. Usually not all groups the user is member of will be in Cognite Data Fusion, only those relevant for Cognite Data Fusion access control.
   - The user will be recognized as a member of any Cognite Data Fusion group whose sourceId matches the a GUID of a group they're in Entra ID.

## Capabilities

Through capabilities, users and services are granted access to perform a particular operation on some data, for example, to read a time series or delete an asset. A resource type, actions, and scope define a capability. The resource type and scope define **what data** the capability is for, while the action defines **which operations** are allowed.

The capabilities of a user allow for zero or more operations in Cognite Data Fusion. A user with zero capabilities will only be able to call those API endpoints that don't require particular access.

1. **Resource type** - Access is granted to a particular resource type, such as events, assets, or groups.

2. **Action** - Access is granted to perform a particular operation, such as READ, LIST, or WRITE. The meaning of the action is specific to the resource type.

3. **Scope** -Access is granted to a specific set of resources of the given type. The means of specifying the subset vary by resource type, but include:

   - All - All resources within the resource-type.
   - Scoping by asset subtree - Access is granted to resources associated with a particular subtree of the asset hierarchy. For example, the data points of a time series are only available when linked to an asset in the relevant asset subtree the user has access to.

### Security categories

Some resource types allow requiring an additional access level above and beyond standard capabilities on the resource by resource basis. These resources are tagged with Security Categories. A user must have the capability of accessing the particular security category a resource is tagged with, AND have the correct resource type capability providing access to that resource otherwise, in order to access that resource. A user that doesn't have a specific security category in the set of capabilities won't be able to perform any operations on resources that are tagged with that specific security category.

### Example

**Data setup:**

1. Some files (such as file 44), assets, etc exist
2. Time series 123 is associated with asset 555 and is labeled with security category 36.
3. Time series 456 is associated with asset 555 and not labeled with a security category.

**Group setup:**

1. Group A: _capabilities_: [read on time series associated with asset subtrees 555 or 55,...]
2. Hypothetical Group A.2: _capabilities_: [write on time series 123,...]
3. Group B: _capabilities_: [member of security category 36,...]
4. Group C: _capabilities_: [read on time series 456,...]

**User setup:**

1. Johnny is a member of groups A and B only.
2. Bobby is a member of A only.
3. Carl is a member of group B only.
4. No one is a member of group C.

**Request and response consequences:**

1. Johnny tries to read time series 123, and succeeds.
    - Johnny has a capability designating him a member of security category 36 AND read on the time series (it's associated with asset 555, which he has read access to).
2. Johnny tries to read time series 456, and succeeds.
    - Johnny does have a capability with read access covering the time series
3. Johnny tries to read File 44, and fails.
    - Johnny has no capability providing access to files, whether associated with asset sub-trees 55, 555, or not; or whether the file has a security category or not.
4. Bobby is a member of group A, and can't read time series 123.
    - Bobby has read access that would normally cover the time series via group A, but it also carries a security category designation which he doesn't have. Denied!
5. Carl is a member of group B, and can't read time series 123.
    - Carl does carry the security category designation Bobby lacks in (III), but doesn't have read access to the time   series normally. Denied!
    - If Carl was a member of group A.2 that offered write on that time series, he would be able to write to it, without being able to read from it, without it affecting the permissions of any of the other users described.


<!-- SOURCE_END: docs.cognite.com/docs\dev\guides\iam\authorization.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\guides\iam\external-application.mdx -->
## File: docs.cognite.com/docs\dev\guides\iam\external-application.mdx

---
title: 'Authenticate an external application'
description: 'Learn how to integrate external applications with Cognite Data Fusion using token-based authentication and OAuth2 authorization code flow.'
content-type: 'procedure'
audience: [developer]
experience-level: 100
article-type: 'article'
lifecycle: use
---
This guide will assist in integrating an application with Cognite Data Fusion (CDF) with respect to authentication. If you are developing an application that utilizes access to Cognite Data Fusion, token-based authentication is used for obtaining access to data.

## Tokens

Obtaining access to Cognite Data Fusion with a token implies that an application is accessing Cognite Data Fusion on behalf of a specific user. To obtain such a token, the user must sign in to an authorized identity provider for the project.

When a user logs in through a web browser, they're sent to the Identity Provider (IdP) configured for the project (an OpenID Connect enabled provider, such as Entra ID) using an authorization code grant flow (see [IAM](/dev/guides/iam/index#projects)).

Following authentication with the IdP, the browser will redirect you to the specified target with an authorization code that is then exchanged for an `id_token` and an `access_token`. These are valid only against the Cognite Data Fusion API.

The access token should be treated as an opaque token (subject to format change and/or encrypted at any time), and claims in the associated id token help obtain user information.

<Frame>
<img class="illustration" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/guides/3d/iam/authn-1.png" alt="CDF OAuth login flow diagram showing authentication steps" />
</Frame>

Steps taken:

1. Obtain the login URL, passing in the (URL name of the) project the user should sign in to, and the destinations if the sign-in succeeds or fails.

2. A login URL is returned, specific to the project and the specific flow. If no IdP is configured for the project, no URL will be returned.

3. GET the return URL. This will direct the browser to the IdP configured for the project.

4. The user logs in to, and established an identity with the IdP, under whichever requirements are set by the IdP.

5. The IdP redirects the browser to Cognite Data Fusion with an authorization grant and state.

6. The browser is directed to Cognite Data Fusion with the IdP authorization grant and state.

7. Cognite Data Fusion validates the existing flow and authorization grant, possibly retrieving group membership or other user details from the IdP, as configured. Lack of cookie support may cause this step to fail, as session details established when retrieving the login URL are checked to prevent interception.

8. The browser is redirected to the target specified in step 1 with id and access tokens in the parameter, and a session is established.


<!-- SOURCE_END: docs.cognite.com/docs\dev\guides\iam\external-application.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\guides\iam\iam_limits.mdx -->
## File: docs.cognite.com/docs\dev\guides\iam\iam_limits.mdx

---
title: "Limitations on IAM resources"
description: "Learn about the limits on IAM resources in Cognite Data Fusion including groups and security categories per project."
content-type: "reference"
audience: [administrator, developer]
experience-level: 100
article-type: "article"
lifecycle: use
---

Cognite Data Fusion (CDF) limits the number of IAM (Identity and Access Management) resources you can create. If you exceed the limits, you need to remove existing resources before you can create new ones.

| Resource                                   | Limit | Comment |
| ------------------------------------------ | ----- | ------- |
| **Groups** (per&nbsp;project)              | 500   |         |
| **Security categories** (per&nbsp;project) | 1000  |         |


<!-- SOURCE_END: docs.cognite.com/docs\dev\guides\iam\iam_limits.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\guides\iam\index.mdx -->
## File: docs.cognite.com/docs\dev\guides\iam\index.mdx

---
title: 'About identity and access management'
description: 'Learn how identity and access management controls which users and applications have access to data in Cognite Data Fusion.'
content-type: 'concept'
audience: [administrator, developer]
experience-level: 100
article-type: 'article'
lifecycle: use
---
Identity and access management is a framework that lets you control which users and applications have access to data in Cognite Data Fusion.

See also [authentication](/dev/guides/iam/authentication) and [authorization](/dev/guides/iam/authorization) for more details.

## Terms

- **Principal**:

   A principal is a user or service recognizable by the system as an individual entity. Each entity has a unique identity that makes the system able to distinguish between individual principals.

- **Request**:

   A request is carrying an operation a particular principal wants to run against Cognite Data Fusion. Each request has information about what operation, such as read or update, which principal is requesting to have the operation carried out, and on which resource. A resource could, for example, be a particular file.

- **Authentication**:

   When a principal sends a request to Cognite Data Fusion, the claimed principal must be checked so that Cognite Data Fusion knows which principal the request is from. Users are authenticated directly or indirectly through an identity provider (such as Entra ID). Services are authenticated by presenting a token. Requests from principals that can't be authenticated are denied. See [authentication](/dev/guides/iam/authentication) for more details.

- **Authorization**:

   After the principal of the request is authenticated, Cognite Data Fusion will assess whether the principal has access to perform the requested operation and allow or deny the request accordingly. Based on the identity of the principal, the relevant capabilities will be resolved and evaluated against the operation in the request and the applicable resources. See [authorization](/dev/guides/iam/authorization) for more details.

- **Identity provider**:

   An identity provider is a service to authenticate users. This is also the service where organizations typically manage the users of their organization. The most common identity provider is Entra ID (Entra ID).

## Projects

Identity management lets you manage which users and services are able to connect to your project in Cognite Data Fusion. In other words, which principals Cognite Data Fusion will be able to authenticate. Only authenticated principals will be able to interact with Cognite Data Fusion to retrieve or store data there.

## Identity Provider

Cognite Data Fusion authenticates users against the organization's Identity Provider, and the organization controls which users are authenticated. Any user they, for example, disable in Entra ID, won't be able to sign in to applications and see data from Cognite Data Fusion. They can also create guest users in the identity provider to let users from other organizations be authenticated.

## Users

Users are managed by the existing identity provider of the organization. Typically, this will be Microsoft's Entra ID (Entra ID). To start using Cognite Data Fusion, you need to configure the connection to the identity provider so that Cognite Data Fusion can authenticate all the users from that identity provider. The identity provider configuration consists of which service instance to talk to.

Cognite Data Fusion must successfully authenticate the user against the identity provider before the user is allowed to access Cognite Data Fusion. This requires that the application the user is leveraging has implemented the required authentication flow. The Asset Data Insight application is an application that implements the required authentication flow. This makes users of the Asset Data Insight application interact with Cognite Data Fusion, with their own principal, and therefore able to see all the data they have access to from Cognite Data Fusion in the application.

## Groups

Groups are defined per project and provide additional information about the principals in a group. They're primarily used for authorization, as they include a set of capabilities that principals in the group are provided. Group membership is dynamically determined from Entra ID group memberships if the `sourceId` for a Cognite Data Fusion group matches the object ID of a group in Entra ID.


<!-- SOURCE_END: docs.cognite.com/docs\dev\guides\iam\index.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\guides\postman.mdx -->
## File: docs.cognite.com/docs\dev\guides\postman.mdx

---
title: 'Set up Postman with Cognite API'
description: 'Step-by-step guide to configure Postman with OpenID Connect authentication to test Cognite API requests and verify responses.'
content-type: 'procedure'
audience: ['developer']
experience-level: '100'
lifecycle: 'setup'
article-type: article
---
We recommend downloading, installing, and using **[Postman](https://www.getpostman.com)** to test API requests and verify responses.

## Before you start

To use the different grant types (Implicit, Authorization code (with PKCE)) with your requests in Postman, you need to grant access to a multi-tenant app in Entra ID to use CDF with Postman. To grant access, you must be an **Entra ID tenant administrator**.

Follow the steps in [How to register Cognite API](/cdf/access/entra/guides/configure_cdf_azure_oidc#step-11-permit-the-cognite-api-to-access-user-profiles-in-azure-ad) to register the app. When you have registered the app, you can sign in with your Entra ID credentials.

Before you set up authorization in Postman, configure your **Entra ID** application:

<Steps>
<Step title="Locate your tenant and application IDs">
Go to **Entra ID** and find your **Tenant ID** and **Application (client) ID** in the overview page.

<Frame>
<img src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/quickstart/aad-tenant-id.png" alt="Entra ID Tenant ID overview" width="500px"/>
</Frame>

<Tip>
Save these IDs as you'll need them when configuring Postman authorization.
</Tip>
</Step>

<Step title="Create a client secret">
In the **App registrations** section, create a **New client secret** under **Certificates & secrets** in the left menu.

Select **+ New client secret**, enter a description, choose an expiry period, and select **Add**.

<Frame>
<img src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/quickstart/add-client-secret.png" alt="Add client secret in Entra ID" width="500px"/>
</Frame>

<Warning>
Copy the client secret value immediately after creation. It won't be visible again once you navigate away from this page.
</Warning>
</Step>

<Step title="Configure the redirect URL">
Add the **Redirect URL** in your Entra ID application settings to allow Postman to receive authentication callbacks.

<Check>
Your Entra ID application is now configured for use with Postman.
</Check>
</Step>
</Steps>

## Set up Postman

<Steps>
<Step title="Import your Postman collection">

Download the [**Cognite OpenAPI specification**](https://api-docs.cognite.com).

In Postman, select **Import** and drag the file to the import modal.

In **View Import Settings**, configure the import:
- Set **Folder organization** to **Tags**
- Turn **off** the **Enable optional parameters** option
- Turn **on** the **Always inherit authentication** option

<Frame>
<img src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/quickstart/how-to-import-api.png" alt="Import Cognite API settings in Postman" width="75%"/>
</Frame>

Select **Continue** > **Import** to complete the import.

<Check>
The Cognite API collection is now available in your Postman workspace.
</Check>
</Step>

<Step title="Set up environment variables">

Navigate to **Environments** on the left sidebar and select **+ Create new Environment**. Give your environment a descriptive name.

<Frame>
<img src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/quickstart/create-environment.png" alt="Create new environment in Postman" width="350px"/>
</Frame>

Add the following variables to your environment:

- **tenant-id**: Your Directory (tenant) ID from Entra ID.

- **token**: Leave this blank. OAuth 2.0 will populate it automatically when you authenticate.

- **baseUrl**: Set to `https://{{cluster}}.cognitedata.com/api/v1/projects/{{project}}` where *cluster* is your CDF instance location. If you don't know the cluster name, contact [Cognite support](mailto:support@cognite.com). For Open Industrial Data, use `api`.

- **project**: Your CDF project name.

<Note>
We recommend working with the current value of variables to prevent sharing sensitive information with your team.
</Note>

<Check>
Your environment is configured and ready to use with the Cognite API collection.
</Check>
</Step>

<Step title="Configure OAuth 2.0 authorization">

With OAuth 2.0, you retrieve an API access token and use it to authenticate future API requests.

Navigate to the **Authorization** tab in the collection overview and configure:
- Set **Type** to **OAuth 2.0**
- Set **Add authorization data to** to **Request Headers**

<Info>
Choose the OAuth 2.0 grant type that matches your use case: [**Implicit**](#implicit) or [**Authorization Code (With PKCE)**](#authorization-code-with-pkce). For more details on authentication flows, see [Configure applications and the authentication flows](/cdf/access/entra/guides/configure_apps_oidc).
</Info>

### Implicit

Select **Configure New Token** and specify these configuration options:

- **Token Name**: Enter a descriptive name for your token.
- **Grant Type**: Select **Implicit**.
- **Callback URL**: Enter `https://postman.cogniteapp.com/loggedin`.

<Note>
If you select the **Authorise using browser** checkbox, the Callback URL auto-populates. Once your application is authorized, you'll be redirected to this URL.
</Note>

- **Auth URL**: Enter `https://login.microsoftonline.com/{{tenant-id}}/oauth2/v2.0/authorize` (replace `{{tenant-id}}` with your tenant ID).
- **Client ID**: Enter `https://postman.cogniteapp.com`.
- **Scope**: Enter `https://{{cluster}}.cognitedata.com/` followed by one of: `default`, `user_impersonation`, `DATA.VIEW`, or `IDENTITY`.

<Info>
The `user_impersonation` scope grants all permissions assigned to the user. The `DATA.VIEW` scope grants read-only access to CDF resources like files, time series, and RAW. Learn more about [Access token scopes](/cdf/access/concepts/access_token_scopes).
</Info>

<Tip>
When using a scope for the first time, an admin must define it explicitly and grant consent.
</Tip>

- **Client Authentication**: Select **Send as Basic Auth header**.

<Frame>
<img src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/quickstart/implicit-grant-oidc.png" alt="OAuth 2.0 implicit grant configuration in Postman" width="750px"/>
</Frame>

Select **Get New Access Token** > **Proceed** > **Use Token**.

<Check>
You have configured a new token using the **Implicit** grant type.
</Check>

### Authorization Code (With PKCE)

Select **Configure New Token** and specify these configuration options:

- **Token Name**: Enter a descriptive name for your token.
- **Grant Type**: Select **Authorization Code (With PKCE)**.
- **Callback URL**: Enter `https://oauth.pstmn.io/v1/callback`.
- **Auth URL**: Enter `https://login.microsoftonline.com/{{tenant-id}}/oauth2/v2.0/authorize` (replace `{{tenant-id}}` with your tenant ID).
- **Access Token URL**: Enter `https://login.microsoftonline.com/{{tenant-id}}/oauth2/v2.0/token` (replace `{{tenant-id}}` with your tenant ID).
- **Client ID**: Enter your application's client ID from Entra ID.
- **Client Secret**: Enter the client secret you created earlier.
- **Code Challenge Method**: Choose either `SHA-256` or `Plain` algorithm.
- **Code Verifier**: Leave blank to auto-generate, or enter a 43-128 character string to connect the authorization request to the token request.
- **Scope**: Enter `https://{{cluster}}.cognitedata.com/` followed by one of: `default`, `user_impersonation`, `DATA.VIEW`, or `IDENTITY`.
- **State**: Enter a random value to prevent cross-site request forgery attacks.
- **Client Authentication**: Select **Send as Basic Auth header**.

<Frame>
<img src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/quickstart/authorization-code-pkce.png" alt="OAuth 2.0 Authorization code PKCE configuration in Postman" height="550px" width="500px"/>
</Frame>

Select **Get New Access Token** > **Proceed** > **Use Token**.

<Check>
You have configured a new token using **Authorization Code (With PKCE)** grant type. You're now ready to use Postman with OIDC as the authentication method.
</Check>
</Step>
</Steps>


<!-- SOURCE_END: docs.cognite.com/docs\dev\guides\postman.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\guides\upload-3d.mdx -->
## File: docs.cognite.com/docs\dev\guides\upload-3d.mdx

---
title: 'Upload 3D models programmatically'
description: 'Learn how to upload multiple 3D models to CDF using the Cognite API and SDKs for automated updates.'
content-type: 'procedure'
audience: 'developer'
experience-level: 100
lifecycle: 'use'
article-type: article
---
From the Cognite Console, you can [upload a single 3D model](/cdf/integration#upload-3d-models) to Cognite Data Fusion (CDF) without writing any code. This article explains how you can upload several 3D models using the Cognite API and SDKs, for instance, for nightly updates.

## Prerequisites

To upload 3D models, you need a **service account** with an **API key** with the `threed:write` capability for your CDF project. See [Manage access for services](/cdf/access#manage-access-for-services) to learn more.

You also need:

- Your **CDF project name**. You will use this as the _[projectname]_ below.
- The **file name(s)** of the model(s) you will be uploading. You will use this as the _[filename]_ below.

## Upload 3D models

<Steps>
<Step title="Create a model that can hold several revisions">
  ```bash wrap
  curl -H "Authorization: Bearer <YOUR_OpenID Connect or OAuth2 token_HERE>" -H "Content-type: application/json" -X POST "https://api.cognitedata.com/api/v1/projects/[projectname]/3d/models" -d '{"items": [{"name":"model name"}]}'
  ```

  API reference: [/api#tag/3D-Models/operation/create3DModels](https://api-docs.cognite.com/20230101/#tag/3D-Models/operation/create3DModels)
</Step>

<Step title="Copy and make a note of the id field">
  Copy and make a note of the `id` field in the response. You will use this as the _[modelId]_ below.
</Step>

<Step title="Create a file placeholder to make the file available for processing later">
  ```bash wrap
  curl -H "Authorization: Bearer <YOUR_OpenID Connect or OAuth2 token_HERE>" -H "Content-type: application/json" -X POST "https://api.cognitedata.com/api/v1/projects/[projectname]/files" -d '{"name":"[filename]"}'
  ```

  API reference: [/api#tag/Files/operation/initFileUpload](https://api-docs.cognite.com/20230101/#tag/Files/operation/initFileUpload)
</Step>

<Step title="Copy and make a note of the id field and uploadUrl">
  Copy and make a note of the `id` field in the response. You will use this as the _[fileId]_ below. Also, copy and make a note of `uploadUrl`. You will use this as the _[uploadUrl]_ below.
</Step>

<Step title="Upload the file">
  ```bash wrap
  curl --upload-file [filename] [uploadUrl]
  ```

  Google API reference: [https://cloud.google.com/storage/docs/json_api/v1/how-tos/resumable-upload#example_uploading_the_file](https://cloud.google.com/storage/docs/json_api/v1/how-tos/resumable-upload#example_uploading_the_file)
</Step>

<Step title="Create a versioned model and link the file from the first step to the revision">
  This also starts the processing of the model.

  ```bash wrap
  curl -H "Authorization: Bearer <YOUR_OpenID Connect or OAuth2 token_HERE>" -H "Content-type: application/json" -X POST "https://api.cognitedata.com/api/v1/projects/[projectname]/3d/models/[modelId]/revisions" -d '{"items": [{"fileId":"[fileId]"}]}'
  ```

  API reference: [/api#tag/3D-Model-Revisions/operation/create3DRevisions](https://api-docs.cognite.com/20230101/#tag/3D-Model-Revisions/operation/create3DRevisions)
</Step>

<Step title="Monitor the processing of the model">
  API reference: [/api#tag/3D-Model-Revisions/operation/get3DRevision](https://api-docs.cognite.com/20230101/#tag/3D-Model-Revisions/operation/get3DRevision)
</Step>
</Steps>


<!-- SOURCE_END: docs.cognite.com/docs\dev\guides\upload-3d.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\index.mdx -->
## File: docs.cognite.com/docs\dev\index.mdx

---
title: 'Building with Cognite Data Fusion'
description: 'Use the Cognite API and SDKs to retrieve data, add context, and develop applications that match your operational needs.'
content-type: 'map'
audience: 'developer'
experience-level: '100'
lifecycle: 'investigate'
mode: 'wide'
article-type: map
---
<CardGroup cols={2}>
<Card
  title="Development quickstart"
  icon="rocket"
  href="/dev/quickstart">
  Get up and running with the CDF API in a few simple steps using Postman and OpenID Connect authentication.
</Card>

<Card
  title="Use the API"
  icon="book-open"
  href="/dev/use_the_API">
  Learn how to construct requests to access resources using the RESTful web API, including HTTP methods and versioning.
</Card>

<Card
  title="API versions"
  icon="split"
  href="/dev/API_versioning">
  Understand API versioning, backwards compatibility policy, and the end-of-life schedule for API versions.
</Card>

<Card
  title="About resource types"
  icon="database"
  href="/dev/concepts/resource_types">
  Explore the different resource types available in CDF, including assets, time series, files, and events.
</Card>

<Card
  title="Software Development Kits"
  icon="code"
  href="/dev/sdks">
  Access official SDKs for Python, JavaScript, and other languages to interact with the CDF API.
</Card>

<Card
  title="Set up Postman"
  icon="test-tube-diagonal"
  href="/dev/guides/postman">
  Configure Postman with OpenID Connect to test API requests and verify responses.
</Card>

<Card
  title="Identity and access management"
  icon="key"
  href="/dev/guides/iam">
  Learn about authentication, authorization, and access control in Cognite Data Fusion.
</Card>

<Card
  title="API reference"
  icon="book"
  href="https://api-docs.cognite.com/20230101/">
  Browse the complete API reference documentation with detailed endpoint specifications.
</Card>
</CardGroup>


<!-- SOURCE_END: docs.cognite.com/docs\dev\index.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\quickstart.mdx -->
## File: docs.cognite.com/docs\dev\quickstart.mdx

---
title: 'Development quickstart'
sidebarTitle: 'Quickstart'
description: 'Get up and running with the Cognite Data Fusion API libraries in a few simple steps using Postman and OpenID Connect authentication.'
content-type: 'quickstart'
audience: 'developer'
experience-level: 200
lifecycle: 'setup'
article-type: article
---
We recommend downloading, installing, and using **[Postman](https://www.getpostman.com)** to test API requests and verify responses.

## Prerequisites

To use the **Implicit** grant type with your requests in Postman, you need to grant access to a multi-tenant app in Entra ID to use CDF with Postman. To grant access, you need to be an Entra ID tenant administrator.

Follow the steps in [How to register Cognite API](/cdf/access/entra/guides/configure_cdf_azure_oidc#step-11-permit-the-cognite-api-to-access-user-profiles-in-azure-ad) to register the app.

When you have registered the app, you will be able to sign in with your Entra ID credentials.

<Steps>
<Step title="Import your Postman collection">

1. In Postman, select **Import** > **Link** and enter the URL to import your latest API V1 Postman collection:
`https://storage.googleapis.com/cognite-postman-collections/v1.json`

   <Frame>
   <img src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/quickstart/import-collection-link.png" alt="Import collection"/>
   </Frame>

1. Select **Continue** > **Import** to import the collection.
</Step>

<Step title="Set up environment variables">

1. To create a new environment, navigate to **Environments** on the left sidebar. Click **+ Create new Environment** and give it a name.

   <Frame>
   <img src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/quickstart/create-environment.png" alt="Create environment" width="350px"/>
   </Frame>

2. Add the variables:

   - **tenant-id**: This is your Directory (tenant) ID. To find the tenant ID, go to your Entra ID. You can find your Tenant ID on the Overview page.

     <Frame>
     <img src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/quickstart/aad-tenant-id.png" alt="Tenant ID" width="500px"/>
     </Frame>

   <Note>
   The recommendation is to work with the current value of a variable to prevent sharing sensitive and confidential information with your team.
   </Note>

   - **token**: Using OAuth 2.0, we'll generate a new token. It will populate automatically, so you will leave it blank as an environment variable.

   - **baseURL**: This is your `<clustername>` and the `cognitedata.com` domain, as in `https://<clustername>.cognitedata.com`. You can find your base URL in [Cognite clusters and regions](/cdf/admin/clusters_regions), under **Cognite API URL**.

   - **project**: This is your CDF project name.

     <Frame>
     <img src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/quickstart/project-name-cdf.png" alt="Project name" width="350px"/>
     </Frame>

3. Click **Save** to save the environment variables you have added.

4. If necessary, select your newly created environment in the dropdown menu in the top right corner.

<Frame>
<img src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/quickstart/select-environment.png" alt="List all assets" width="300px"/>
</Frame>
</Step>

<Step title="Update authorization">

1. To update the authorization, navigate to the **Authorization** tab in the collection overview.

2. Select **OAuth 2.0** as Type and **Request Headers** as Add auth data to.

3. Select **Configure New Token** and specify these configuration options:

   - Enter a **Token Name**.
   - Select the **Grant Type** as **Implicit**.
   - Input the **Callback URL** as `https://postman.cogniteapp.com/loggedin`.

     <Note>
     If you don't select the checkbox **Authorise using browser**, you can input the Callback URL. Otherwise, the Callback URL gets auto-populated on selection. You will be redirected to the **Callback URL** once your application is authorized.
     </Note>

   - Enter the **Auth URL** as `https://login.microsoftonline.com/$tenant-id/oauth2/v2.0/authorize`. Replace the **tenant-id** obtained from the previous step.
   - Input the **Client ID** as `https://postman.cogniteapp.com`.
   - The **Scope** is `$baseUrl/$scope`, where **$baseUrl** is as above in 2.2, and **$scope** is `IDENTITY`, `DATA.VIEW`, `user_impersonation` etc.

   <Tip>
   While using a scope for the first time, the admin has to define the scope explicitly. The admin must then consent to use this scope for the authorization process.
   </Tip>

   `User_impersonation` grants all permissions to the user assigned to access the API. The `DATA.VIEW` scope grants read-only access to data in CDF, for example, to view files, time series, RAW, and other CDF resources. To know more about CDF's scopes, see the different [Access token scopes](/cdf/access/concepts/access_token_scopes).

   - Select **Client Authentication** as **Send as Basic Auth header**.

      <Frame>
      <img src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/quickstart/implicit-grant-oidc.png" alt="OAuth 2.0 implicit"/>
      </Frame>

4. Select **Get New Access Token** > **Proceed** > **Use Token**.

You are now ready to use Postman with OIDC as the authentication method.
</Step>

<Step title="Send your API request">

1. Make a test API request to check that your integration is working correctly:

    - Click the **Cognite API** collection in the left-hand menu, and select **Assets** > **List assets**.

       <Frame>
       <img src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/dev/quickstart/cogniteapi-collection.png" alt="List assets" width="350px"/>
       </Frame>

    - Click **Send**.

Cognite Data Fusion returns a list of all [asset objects](https://api-docs.cognite.com/20230101/#tag/Assets) in response to your API request. For the [Open Industrial Data Project](https://www.youtube.com/watch?v=g4DAAo55IS8) the JSON results look similar to this:

```json wrap
{
    "nextCursor": "8ZiApWzGe5RnTAE1N5SABLDNv7GKkUGiVUyUjzNsDvM",
    "items": [
        {
            "name": "23-TE-96116-04",
            "parentId": 3117826349444493,
            "description": "VRD - PH 1STSTGGEAR THRUST BRG OUT",
            "metadata": {
                "ELC_STATUS_ID": "1211",
                "RES_ID": "525283",
                "SOURCE_DB": "workmate",
                "SOURCE_TABLE": "wmate_dba.wmt_tag",
                "WMT_AREA_ID": "1600",
                "WMT_CATEGORY_ID": "1116",
                "WMT_CONTRACTOR_ID": "1686",
                "WMT_FUNC_CODE_ID": "4564",
                "WMT_LOCATION_ID": "1004",
                "WMT_PO_ID": "8309",
                "WMT_SAFETYCRITICALELEMENT_ID": "1060",
                "WMT_SYSTEM_ID": "4440",
                "WMT_TAG_CREATED_DATE": "2009-06-26 15:36:37",
                "WMT_TAG_CRITICALLINE": "N",
                "WMT_TAG_DESC": "VRD - PH 1STSTGGEAR THRUST BRG OUT",
                "WMT_TAG_GLOBALID": "1000000000681024",
                "WMT_TAG_HISTORYREQUIRED": "Y",
                "WMT_TAG_ID": "346434",
                "WMT_TAG_ID_ANCESTOR": "345637",
                "WMT_TAG_ISACTIVE": "1",
                "WMT_TAG_ISOWNEDBYPROJECT": "0",
                "WMT_TAG_LOOP": "96116",
                "WMT_TAG_MAINID": "681760",
                "WMT_TAG_NAME": "23-TE-96116-04",
                "WMT_TAG_UPDATED_BY": "8137",
                "WMT_TAG_UPDATED_DATE": "2014-07-11 09:25:15"
            },
            "id": 702630644612,
            "createdTime": 0,
            "lastUpdatedTime": 0,
            "rootId": 6687602007296940
        },
...
```

Once you have successfully made an API request, you're ready to begin interacting with the Cognite Data Fusion API through the Software Development Kits (SDKs).
</Step>

{/* Note that this headline is linked to from docs/dev/index.mdx, so if you reword or renumber it, you need to update that link. */}
<Step title="Install a Software Development Kit (SDK)">

We provide official Software Development Kits (SDKs) for [Python](/dev/sdks/python) and [JavaScript](/dev/sdks/js) with libraries that interact with the Cognite Data Fusion API.

### The Python Software Development Kit (SDK)

The Cognite Python SDK requires Python 3.5+ and provides access to the Cognite Data Fusion API from applications written in the Python language. See the [Cognite Python SDK Documentation](/dev/sdks/python) for more details.

<Info>
To download and install Python, visit [Python.org](https://python.org).
</Info>

To install the Cognite Python library:

```sh
pip install cognite-sdk
```

### The JavaScript Software Development Kit (SDK)

The Javascript SDK provides access to the Cognite Data Fusion API from applications written in client-side or server-side JavaScript. The library supports authentication through API keys (for server-side applications) and bearer tokens (for web applications). See the [Cognite JavaScript documentation](/dev/sdks/js) for more details.

Install the Cognite JavaScript library:

```sh
npm install @cognite/sdk --save
```
</Step>
</Steps>

## Next steps

- [Quickstart tutorial with Python SDK](/dev/sdks/python)
- [Quickstart tutorial with JavaScript SDK](/dev/sdks/js)


<!-- SOURCE_END: docs.cognite.com/docs\dev\quickstart.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\dev\use_the_API.mdx -->
## File: docs.cognite.com/docs\dev\use_the_API.mdx

---
title: 'Use the API'
description: 'Learn how to construct requests to access resources in Cognite Data Fusion using the RESTful web API, including HTTP methods, versioning, and resource types.'
content-type: 'reference'
audience: 'developer'
experience-level: 100
lifecycle: 'use'
article-type: article
---
To read or write to a resource, you construct requests that look like this:

```http wrap
https://api.cognitedata.com/api/{version}/projects/{project}/{resource}?query-parameters/
```

Where:

- [HTTP method](#http-methods) - The HTTP method used on the request to Cognite Data Fusion.
- [\{version\}](#version) - The version of the API your application is using.
- [\{project\}](#project) - The project identifier for the resources in Cognite Data Fusion that you're referencing.
- [\{resource\}](#resources) - The resource in Cognite Data Fusion that you're referencing.
- query-parameters - An optional set of parameters to change the request or response.

When you make a request, Cognite Data Fusion returns a response that includes:

- Status code - An HTTP status code that indicates success or failure.
- Response message - The data that you requested or the result of the operation. The response message can be empty for some operations.
- Pagination - If your request returns a lot of data, you need to page through it by passing the value of `nextCursor` as the cursor to get the next page of limit results. For details, see [Paging](/dev/concepts/pagination).

## HTTP methods

Cognite Data Fusion uses the HTTP method on your request to learn what your request is doing. The API supports the following methods.

| Method | Description                              |
| ------ | ---------------------------------------- |
| GET    | Read data from a resource.               |
| POST   | Create a resource, or perform an action. |
| DELETE | Remove a resource.                       |
| PUT    | Replace a resource with a new one.       |

| Standard method | HTTP mapping                  | Required | HTTP request body        | HTTP response body |
| --------------- | ----------------------------- | -------- | ------------------------ | ------------------ |
| Create          | POST \<collection URL\>        | Yes      | Resource list            | Resource list      |
| List            | GET \<collection URL\>         | Yes      | N/A                      | Resource list      |
| Filter          | POST \<collection URL\>/list   | No       | Filter object            | Resource list      |
| Search          | POST \<collection URL\>/search | No       | Fuzzy search for objects | Resource list      |
| Get             | GET \<collection URL\>/:id     | No       | N/A                      | Resource           |
| Retrieve        | POST \<collection URL\>/byids  | No       | List of IDs              | Resource list      |
| Update          | POST \<collection URL\>/update | No       | Resource list            | Resource list      |
| Delete          | POST \<collection URL\>/delete | Yes      | List of IDs              | N/A                |

## Version

See [API versioning](/dev/API_versioning) to learn more about supported API versions, policy for backwards compatibility, and the end-of-life schedule for the APIs.

## Project

Projects separate customer environments from one another, and all resources in Cognite Data Fusion are linked to a project. A customer can have one or more projects. The project object contains configuration for how to authenticate users.

Automatically assigned object IDs are **unique only within each project**.

## Resources

Your URL will include the resource or resources you are interacting with in the request, such as `assets`, `files`, `events`, and `timeseries`.

See [About resource types](/dev/concepts/resource_types/index) and the resource reference topics for more information about resource types.

## IP addresses for the API

The IP addresses for the APIs aren't static and will change as the architecture evolves and the service offerings extend. We will do our best to keep the changes to a minimum.

If you want to limit outbound traffic to Cognite Data Fusion only, the IP addresses for the current multi-tenant Cognite Data Fusion clusters are:

| Cluster              | URL                          | IP addresses                  |
|----------------------|------------------------------|-------------------------------|
| Europe-west1-1 (GCP) | `api.cognitedata.com`          | `35.187.184.117, 34.76.254.249` |
| Westeurope-1 (Azure) | `westeurope-1.cognitedata.com` | `20.73.203.191`                 |
| Eastus-1 (Azure)     | `az-eastus-1.cognitedata.com`  | `52.151.200.252`                |
| Asia-Northeast1-1 (GCP) | `asia-northeast1-1.cognitedata.com`  | `35.200.31.145`        |

For the IP addresses of dedicated Cognite Data Fusion clusters, contact [Cognite Support](https://cognite.zendesk.com/hc/en-us/requests/new).


<!-- SOURCE_END: docs.cognite.com/docs\dev\use_the_API.mdx -->

================================================================================
