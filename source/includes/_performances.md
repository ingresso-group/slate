# Performances

TODO:

- are we using usage date? do we have any live examples? From old API docs: "For products such as theme park entrance tickets then the estimated date of usage of the ticket may be required as not all tickets are valid on all dates"
- are we using departure date? do we have any live examples? From old API docs: "to aid ticket despatch it may be necessary to know what date the customer is intending to depart, in order that the tickets may be shipped to them before they leave"
- "earliest_date" and "latest_date" work in XML core - is there an equivalent?

This section describes:

- the [performance object](#performance-object)
- the API call to [retrieve information about an individual performance](#retrieve-a-performance)
- the API call to [list all performances for an event](#list-all-performances-for-an-event) (this is the most commonly used performance-related call)

## Performance Object

> **Example Response**

```json
{
  "cached_max_seats": 6,
  "date_desc": "Tue, 13th September 2016",
  "event_id": "6IF",
  "has_pool_seats": true,
  "is_ghost": false,
  "is_limited": false,
  "iso8601_date_and_time": "2016-09-13T19:30:00+01:00",
  "perf_id": "6IF-A46",
  "perf_name": "Including back stage pass",
  "running_time": 120,
  "time_desc": "7.30 PM"
}
```

A `performance` is an instance of an event, for example the Saturday 21st January 7.30pm showing of The Lion King. This is the case for the majority of our product, however since not all events (and the systems we connect to) are the same, there are some exceptions:

- Some events have performances that do not have a time component - they are valid for the entire day
- Some events have performances with a performance name rather than a time (TODO: add more detail)
- Some events do not have performances - they are valid on all dates (TODO: add more detail
- Departure and usage dates, (TODO do we need these?)

It is possible to request a number of additional attributes for a performance - below we have listed the default attributes first, followed by the optional attributes:

Attribute | Description
--------- | -----------
`cached_max_seats` | This is the maximum number of contiguous seats that can be booked for this performance, based on Ingresso's cached data. Note that if the event supports seat selection you are not limited by the number of contiguous seats, so this value should be used in the context of best available only. (TODO is this optional?)
`date_desc` | Date description
`event_id` | Unique identifier for the event that this performance is an instance of
`has_pool_seats` | (TODO: Pete what does this mean exactly?)
`is_ghost` | (TODO: Pete in what scenario would you return perfs with is_ghost = true?)
`is_limited` | Indicates whether the supply of tickets is known to be extremely limited. This is usually taken to be fewer than four remaining, though not all ticket sources provide this information. Where the information is not available the flag will always be set to `false`
`iso8601_date_and_time` | ISO 8601 date and time
`perf_id` | Unique identifier for the performance
`perf_name` | (Optional) A human readable description of the performance. Performance names are not always present but must be displayed when they are. Performance names are typically used where performances of an event differ significantly in more than just date and time (sometimes the backend system will not return the time to us in a structured format but it will instead be included as part of the performance name). An example performance with a performance name is 6IF-A5R.
`running_time` | (Optional) The length / duration of the performance in minutes
`time_desc` | (Optional) Time description



These are the optional attributes - these are only returned if you add one of the `req_` parameters to your request.


Attribute | Description
--------- | -----------
`avail_details` | A list of [avail details](#avail-detail-object)
`cost_range` | A list of [cost ranges](#cost-range-object)


## Retrieve a performance

This call is used to return detail for one or more performances by their ID. It returns a list of [performance objects](#performance-object).

This call will not be useful for common use cases. If you have a need to request availability for a subset of performances in a guaranteed fast response time then one way to achieve it is to use this call with the req_avail_details parameter - this will return the list of available price bands from Ingresso's cached data. Not that the cached data can be out of date or not present, and doesn't return seating data, so we do not recommend this in most cases.

> **Definition**

```
GET https://api.ticketswitch.com/cgi-bin/json_performances_by_id.exe/{username}
```

This call is used to return detail for one or more performances by their ID. It returns a list of [performance objects](#performance-object).

> **Example request**

```shell
curl https://api.ticketswitch.com/cgi-bin/json_performances_by_id.exe/demo \
    -d "user_passwd=demopass" \
    -d "perf_id_list=6IF-A5R" \
    -d "req_cost_range" \
    -G
```

### Request

Parameter | Description
--------- | -----------
`perf_id_list` | A comma separated list of performance IDs e.g. `6IF-A5R` for a single performance; `6IF-A5R,6IF-A5S` for multiple performances


These parameters can be included to request additional data for each performance:


Parameter | Description
--------- | -----------
`req_avail_details` | Returns a list of ticket types and price bands that are available for this performance [see detail](#avail-detail-object). This data is retrieved from a cache of previously-seen availability data. If you wish to display availability for a particular performance our recommendation is to make a separate [availability request](#availability) rather than using this data which can be quite out of date. However if you have a use case requiring you to quickly return availability for all performances this is the best way to achieve it.
`req_cost_range` | Returns [cost ranges](#cost-range-object) for each performance (min and max prices, details of offers). This will normally be useful to request.


These parameters are used to control the output if more than one performance is returned:





Parameter | Description
--------- | -----------
`page_len` | Length of a page, default 50
`page_no` | Page number, default 0, ignored if page_len is not present


(TODO: need example of how the paging works or link to description in events when that is added)


> **Example response**

```json
{
  "performances_by_id": {
    "6IF-A48": {
      PERFORMANCE_OBJECT
    }
  }
}
```

### Response

A list of performance objects are returned for each perf_id requested.



## List performances for an event

> **Definition**

```
GET https://api.ticketswitch.com/cgi-bin/json_performances.exe/{username}
```

This call returns a list of [performance objects](#performance-object) for a particular event. The list is paged to avoid large volumes
of data being accidentally returned.

Typical use cases:

* Request a list of performances to dynamically populate the calendar on your website
* Request performances on a regular basis (e.g. hourly) to add them to your system. Note that this is not necessary or recommended - performances is one of our faster calls and if you cache the list of performances in your own system pricing and offers can be out of date. This is a particular problem when an offer or price change goes live at a particular time - you then need to worry about refreshing your cache. Our own websites dynamically request performances when a user needs to select on a calendar.


> **Example request**

```shell
curl https://api.ticketswitch.com/cgi-bin/json_performances.exe/demo \
    -d "user_passwd=demopass" \
    -d "&event_id=6IF" \
    -G
```

### Request


These parameters can be included to request additional data for each performance:


Parameter | Description
--------- | -----------
`req_avail_details` | Returns a list of ticket types and price bands that are available for this performance [see detail](#avail-detail-object). This data is retrieved from a cache of previously-seen availability data. If you wish to display availability for a particular performance our recommendation is to make a separate [availability request](#availability) rather than using this data which can be quite out of date. However if you have a use case requiring you to quickly return availability for all performances this is the best way to achieve it.
`req_cost_range` | Returns [cost ranges](#cost-range-object) for each performance (min and max prices, details of offers). This will normally be useful to request.


These parameters are used to control the output if more than one performance is returned:

Parameter | Description
--------- | -----------
`page_len` | Length of a page, default 50
`page_no` | Page number, default 0, ignored if page_len is not present



> **Example response**

```json
{
  "results": {
    "events_by_id": { ... },
    "has_perf_names": true,
    "performance": [
      {
        PERFORMANCE_OBJECT
      },
      {
        PERFORMANCE_OBJECT
      }
    ]
  }
}
```

### Response

Attribute | Description
--------- | -----------
`events_by_id` | A list of events related to the performances returned. This is necessary in the case of meta events (events that are made up of multiple component events, for example a touring show) - in that case it is useful to know the details of the component events. (TODO couldn't this just be returned at the event stage?)
`has_perf_names` | Whether the performances returned have performance names (human readable descriptions of the performance). Performance names are not always present but must be displayed when they are. Performance names are typically used where performances of an event differ significantly in more than just date and time (sometimes the backend system will not return the time to us in a structured format but it will instead be included as part of the performance name). An example performance with a performance name is 6IF-A5R.
`performance` | A list of [performance objects](#performance-object)