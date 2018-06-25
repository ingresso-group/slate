# Performances

A performance describes an instance of an event, for example the Saturday 21st
January 7.30pm showing of The Lion King. This is the case for the majority of
our product, however since not all events (and the systems we connect to) are
the same, there are some exceptions:

- Some events have performances that do not have a time component - they are
  valid for the entire day
- Some events have performances with a performance name rather than a time
- Some events do not have performances - they are valid on all dates
- Departure and usage dates

An event is considered live when it has one or more future performances. The API
will not return performances that are more than 2 years in the future by
calendar date.

There are two performance-related resources:

1. [Performances list](#performances-list): list performances for an event.

2. [Performances by ID](#performances-by-id): return detail for one or more 
performances by their ID.

These two resources are described below, followed by detail of the 
[extra parameters](#extra-parameters) that can be passed in to each resource.


## Performances list

> **Definition**

```
GET https://demo.ticketswitch.com/f13/performances.v1?event_id={eventid}
```

This call returns a list of performances for a particular event. The list is
paged to avoid large volumes of data being accidentally returned.

Typical use cases:

* Request a list of performances to dynamically populate the calendar on your
  website
* Request performances on a regular basis (e.g. hourly) to add them to your
  system. Note that this is not necessary or recommended - performances is one
  of our faster calls and if you cache the list of performances in your own system
  pricing and offers can be out of date. This is a particular problem when an
  offer or price change goes live at a particular time - you then need to worry
  about refreshing your cache. Our own websites dynamically request performances
  when a user needs to select on a calendar.

### Request

> **Example request**

```shell
curl https://demo.ticketswitch.com/f13/performances.v1 \
    -u "demo:demopass" \
    -d "event_id=6IF" \
    -d "page_length=10" \
    --compressed \
    -G
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
performances, meta = client.list_performances('6IF')
```

Parameter | Description
--------- | -----------
`event_id` | Identifier of the event you want to see performances for.
`page_length` | Length of a page, default 50.
`page_number` | Page number, default 0, ignored if page_len is not present.
`date_range` | Date range in the form `yyyymmdd:yyyymmdd` (both are optional)

These parameters can be passed in to request additional data for each 
performance, and are described in more detail in the 
[extra parameters](#extra-parameters) section below:

Parameter | Description
--------- | -----------
`req_avail_details` | Returns [availability details](#availability-detail) - a cached list of unique ticket types and price bands available for this performance. **This parameter is not commonly used.**
`req_cost_range` | Returns [cost ranges](#cost-ranges) - a from price and offer detail for each event. *Most partners include this parameter.*
`req_cost_range_best_value_offer` | Returns the offer with the highest percentage saving. *This is the most commonly used offer cost range.*
`req_cost_range_details` | Returns a list of unique ticket types and price bands and their cost ranges across all performances.
`req_cost_range_max_saving_offer` | Returns the offer with the highest absolute saving.
`req_cost_range_min_cost_offer` | Returns the offer with the lowest cost.
`req_cost_range_top_price_offer` | Returns the offer with the highest cost. This is the least used offer cost range.
`req_cost_range_no_singles_data` | This returns another cost range object that excludes availability with only 1 consecutive seat available. The prices in this cost range will therefore be the same or higher than the outer cost range. It has the same structure as the main cost range (so if you want to see the "best value offer" in the no singles data, you need to add `req_cost_range_best_value_offer` and you will see this data in both cost ranges).

### Response

> **Example response**

```shell
{
  "results": {
    "has_perf_names": true,
    "paging_status": {
      "page_length": 10,
      "page_number": 0,
      "pages_remaining": 10,
      "results_remaining": 94,
      "total_unpaged_results": 104
    },
    "performance": [
      {
        "cached_max_seats": 6,
        "date_desc": "Thu, 9th March 2017",
        "event_id": "6IF",
        "has_pool_seats": true,
        "is_ghost": false,
        "is_limited": false,
        "iso8601_date_and_time": "2017-03-09T19:30:00Z",
        "perf_id": "6IF-A94",
        "running_time": 120,
        "time_desc": "7.30 PM"
      },
      {
        "cached_max_seats": 6,
        "date_desc": "Fri, 10th March 2017",
        "event_id": "6IF",
        "has_pool_seats": true,
        "is_ghost": false,
        "is_limited": false,
        "iso8601_date_and_time": "2017-03-10T19:30:00Z",
        "perf_id": "6IF-A95",
        "running_time": 120,
        "time_desc": "7.30 PM"
      },
      {
        "cached_max_seats": 6,
        "date_desc": "Sun, 12th March 2017",
        "event_id": "6IF",
        "has_pool_seats": true,
        "is_ghost": false,
        "is_limited": false,
        "iso8601_date_and_time": "2017-03-12T19:30:00Z",
        "perf_id": "6IF-A97",
        "running_time": 120,
        "time_desc": "7.30 PM"
      },
      {
        "cached_max_seats": 6,
        "date_desc": "Mon, 13th March 2017",
        "event_id": "6IF",
        "has_pool_seats": true,
        "is_ghost": false,
        "is_limited": false,
        "iso8601_date_and_time": "2017-03-13T19:30:00Z",
        "perf_id": "6IF-A98",
        "running_time": 120,
        "time_desc": "7.30 PM"
      },
      {
        "cached_max_seats": 6,
        "date_desc": "Tue, 14th March 2017",
        "event_id": "6IF",
        "has_pool_seats": true,
        "is_ghost": false,
        "is_limited": false,
        "iso8601_date_and_time": "2017-03-14T19:30:00Z",
        "perf_id": "6IF-A99",
        "perf_name": "Including back stage pass",
        "running_time": 120,
        "time_desc": "7.30 PM"
      },
      {
        "cached_max_seats": 6,
        "date_desc": "Wed, 15th March 2017",
        "event_id": "6IF",
        "has_pool_seats": true,
        "is_ghost": false,
        "is_limited": false,
        "iso8601_date_and_time": "2017-03-15T19:30:00Z",
        "perf_id": "6IF-A9A",
        "running_time": 120,
        "time_desc": "7.30 PM"
      },
      {
        "cached_max_seats": 6,
        "date_desc": "Thu, 16th March 2017",
        "event_id": "6IF",
        "has_pool_seats": true,
        "is_ghost": false,
        "is_limited": false,
        "iso8601_date_and_time": "2017-03-16T19:30:00Z",
        "perf_id": "6IF-A9B",
        "running_time": 120,
        "time_desc": "7.30 PM"
      },
      {
        "cached_max_seats": 6,
        "date_desc": "Fri, 17th March 2017",
        "event_id": "6IF",
        "has_pool_seats": true,
        "is_ghost": false,
        "is_limited": false,
        "iso8601_date_and_time": "2017-03-17T19:30:00Z",
        "perf_id": "6IF-A9C",
        "running_time": 120,
        "time_desc": "7.30 PM"
      },
      {
        "cached_max_seats": 6,
        "date_desc": "Sun, 19th March 2017",
        "event_id": "6IF",
        "has_pool_seats": true,
        "is_ghost": false,
        "is_limited": false,
        "iso8601_date_and_time": "2017-03-19T19:30:00Z",
        "perf_id": "6IF-A9E",
        "running_time": 120,
        "time_desc": "7.30 PM"
      },
      {
        "cached_max_seats": 6,
        "date_desc": "Mon, 20th March 2017",
        "event_id": "6IF",
        "has_pool_seats": true,
        "is_ghost": false,
        "is_limited": false,
        "iso8601_date_and_time": "2017-03-20T19:30:00Z",
        "perf_id": "6IF-A9F",
        "running_time": 120,
        "time_desc": "7.30 PM"
      }
    ]
  }
}
```

```python
from pyticketswitch.performance import Performance

[
    Performance(
        id='6IF-B0O',
        event_id='6IF',
        date_time=datetime.datetime(2017, 5, 4, 19, 30, tzinfo=tzoffset(None, 3600)),
        date_description='Thu, 4th May 2017',
        time_description='7.30 PM',
        has_pool_seats=True,
        is_limited=False,
        cached_max_seats=6,
        no_singles_cost_range=None,
        is_ghost=False,
        running_time=120,
    ),
    Performance(
        id='6IF-B0P',
        event_id='6IF',
        date_time=datetime.datetime(2017, 5, 5, 19, 30, tzinfo=tzoffset(None, 3600)),
        date_description='Fri, 5th May 2017',
        time_description='7.30 PM',
        has_pool_seats=True,
        is_limited=False,
        cached_max_seats=6,
        is_ghost=False,
        running_time=120,
    ),
    Performance(
        id='6IF-B0R',
        event_id='6IF',
        date_time=datetime.datetime(2017, 5, 7, 19, 30, tzinfo=tzoffset(None, 3600)),
        date_description='Sun, 7th May 2017',
        time_description='7.30 PM',
        has_pool_seats=True,
        is_limited=False,
        cached_max_seats=6,
        is_ghost=False,
        running_time=120,
    ),
    Performance(
        id='6IF-B0S',
        event_id='6IF',
        date_time=datetime.datetime(2017, 5, 8, 19, 30, tzinfo=tzoffset(None, 3600)),
        date_description='Mon, 8th May 2017',
        time_description='7.30 PM',
        has_pool_seats=True,
        is_limited=False,
        cached_max_seats=6,
        is_ghost=False,
        running_time=120,
    ),
    Performance(
        id='6IF-B0T',
        event_id='6IF',
        date_time=datetime.datetime(2017, 5, 9, 19, 30, tzinfo=tzoffset(None, 3600)),
        date_description='Tue, 9th May 2017',
        time_description='7.30 PM',
        has_pool_seats=True,
        is_limited=False,
        cached_max_seats=6,
        is_ghost=False,
        running_time=120,
    ),
    Performance(
        id='6IF-B0U',
        event_id='6IF',
        date_time=datetime.datetime(2017, 5, 10, 19, 30, tzinfo=tzoffset(None, 3600)),
        date_description='Wed, 10th May 2017',
        time_description='7.30 PM',
        has_pool_seats=True,
        is_limited=False,
        cached_max_seats=6,
        is_ghost=False,
        running_time=120,
    ),
    Performance(
        id='6IF-B0V',
        event_id='6IF',
        date_time=datetime.datetime(2017, 5, 11, 19, 30, tzinfo=tzoffset(None, 3600)),
        date_description='Thu, 11th May 2017',
        time_description='7.30 PM',
        has_pool_seats=True,
        is_limited=False,
        cached_max_seats=6,
        is_ghost=False,
        running_time=120,
    ),
    Performance(
        id='6IF-B0W',
        event_id='6IF',
        date_time=datetime.datetime(2017, 5, 12, 19, 30, tzinfo=tzoffset(None, 3600)),
        date_description='Fri, 12th May 2017',
        time_description='7.30 PM',
        has_pool_seats=True,
        is_limited=False,
        cached_max_seats=6,
        is_ghost=False,
        running_time=120,
    ),
    Performance(
        id='6IF-B0Y',
        event_id='6IF',
        date_time=datetime.datetime(2017, 5, 14, 19, 30, tzinfo=tzoffset(None, 3600)),
        date_description='Sun, 14th May 2017',
        time_description='7.30 PM',
        has_pool_seats=True,
        is_limited=False,
        cached_max_seats=6,
        is_ghost=False,
        running_time=120,
    ),
    Performance(
        id='6IF-B0Z',
        event_id='6IF',
        date_time=datetime.datetime(2017, 5, 15, 19, 30, tzinfo=tzoffset(None, 3600)),
        date_description='Mon, 15th May 2017',
        time_description='7.30 PM',
        has_pool_seats=True,
        is_limited=False,
        cached_max_seats=6,
        is_ghost=False,
        running_time=120,
    ),
    Performance(
        id='6IF-B10',
        event_id='6IF',
        date_time=datetime.datetime(2017, 5, 16, 19, 30, tzinfo=tzoffset(None, 3600)),
        date_description='Tue, 16th May 2017',
        time_description='7.30 PM',
        has_pool_seats=True,
        is_limited=False,
        cached_max_seats=6,
        is_ghost=False,
        running_time=120,
    )
]
```

### Results

Attribute | Description
--------- | -----------
`has_perf_names` | Whether the performances returned have performance names (human readable descriptions of the performance). Performance names are not always present but must be displayed when they are. Performance names are typically used where performances of an event differ significantly in more than just date and time (sometimes the supplier system will not return the time to us in a structured format but it will instead be included as part of the performance name). An example performance with a performance name is 6IF-A5R.
`paging_status` | Information regarding whether the data has been paged.
`performance` | An array of performance objects, described below.


**Paging Status**

Attribute | Description
--------- | -----------
`page_length` | The page length used for the response. This will either be the value set with the `page_len` parameter
`page_number` | The zero-based page number currently displayed.
`pages_remaining` | The number of pages that you need to request after the current page to retrieve all results.
`results_remaining` | The number of results in the remaining pages.
`total_unpaged_results` | The total number of results.


**Performance**

Attribute | Description
--------- | -----------
`cached_max_seats` | This is the maximum number of contiguous seats that can be booked for this performance, based on Ingresso's cached data. Note that if the event supports seat selection you are not limited by the number of contiguous seats, so this value should be used in the context of best available only.
`date_desc` | human readable date description
`event_id` | Unique identifier for the event that this performance is an instance of
`has_pool_seats` | Whether this performance has seats available from the general pool of tickets. This is ignored by most partners.
`is_ghost` | `true` when this performance is no longer on sale. When you request performances for an event we will only return performances that are on sale (so `is_ghost` will always be `false`), however it is possible to request [performances by ID](#performances-by-id) for a ghost performance.
`is_limited` | Indicates whether the supply of tickets is known to be extremely limited. This is usually taken to be fewer than four remaining, though not all ticket sources provide this information. Where the information is not available the flag will always be set to `false`
`iso8601_date_and_time` | ISO 8601 date and time
`perf_id` | Unique identifier for the performance
`perf_name` | A human readable description of the performance. Performance names are not always present but must be displayed when they are. Performance names are typically used where performances of an event differ significantly in more than just date and time (sometimes the supplier system will not return the time to us in a structured format but it will instead be included as part of the performance name). Event ID 6IF includes some performances with names.
`running_time` | The length / duration of the performance in minutes
`time_desc` | human readable time description


## Performances by ID

> **Definition**

```
GET https://demo.ticketswitch.com/f13/performances_by_id.v1?perf_id_list={perfidlist}
```

This resource is used to return detail for one or more performances by their ID.
It returns a dictionary of performances.

If you have a need to request availability for a subset of performances in a
guaranteed fast response time then one way to achieve it is to use this call
with the `req_avail_details` parameter - this will return the list of available
price bands from Ingresso's cached data. Note that the cached data can be out of
date or not present, and doesn't return seating data, so we do not recommend
this in most cases.

### Request

> **Example request**

```shell
curl https://demo.ticketswitch.com/f13/performances_by_id.v1 \
    -u "demo:demopass" \
    -d "perf_id_list=6IF-B2E,6IF-B2F" \
    --compressed \
    -G
```

```python
from pyticketswitch import Client


client = Client('demo', 'demopass')
performances, meta = client.get_performances(['6IF-B2E', '6IF-B2F'])
```

Parameter | Description
--------- | -----------
`perf_id_list` | A comma separated list of performance IDs e.g. `6IF-A5R` for a single performance; `6IF-A5R,6IF-A5S` for multiple performances.

These parameters can be passed in to request additional data for each 
performance, and are described in more detail in the 
[extra parameters](#extra-parameters) section below:

Parameter | Description
--------- | -----------
`req_avail_details` | Returns [availability details](#availability-detail) - a cached list of unique ticket types and price bands available for this performance. **This parameter is not commonly used.**
`req_cost_range` | Returns [cost ranges](#cost-ranges) - a from price and offer detail for each event. *Most partners include this parameter.*
`req_cost_range_best_value_offer` | Returns the offer with the highest percentage saving. *This is the most commonly used offer cost range.*
`req_cost_range_details` | Returns a list of unique ticket types and price bands and their cost ranges across all performances.
`req_cost_range_max_saving_offer` | Returns the offer with the highest absolute saving.
`req_cost_range_min_cost_offer` | Returns the offer with the lowest cost.
`req_cost_range_top_price_offer` | Returns the offer with the highest cost. This is the least used offer cost range.
`req_cost_range_no_singles_data` | This returns another cost range object that excludes availability with only 1 consecutive seat available. The prices in this cost range will therefore be the same or higher than the outer cost range. It has the same structure as the main cost range (so if you want to see the "best value offer" in the no singles data, you need to add `req_cost_range_best_value_offer` and you will see this data in both cost ranges).

### Response


> **Example response**

```shell
{
  "performances_by_id": {
    "6IF-B2E": {
      "date_desc": "Wed, 5th July 2017",
      "event_id": "6IF",
      "has_pool_seats": true,
      "is_ghost": false,
      "is_limited": false,
      "iso8601_date_and_time": "2017-07-05T19:30:00+01:00",
      "perf_id": "6IF-B2E",
      "running_time": 120,
      "time_desc": "7.30 PM"
    },
    "6IF-B2F": {
      "date_desc": "Thu, 6th July 2017",
      "event_id": "6IF",
      "has_pool_seats": true,
      "is_ghost": false,
      "is_limited": false,
      "iso8601_date_and_time": "2017-07-06T19:30:00+01:00",
      "perf_id": "6IF-B2F",
      "running_time": 120,
      "time_desc": "7.30 PM"
    }
  }
}
```

```python
from pyticketswitch.performance import Performance

{
    '6IF-B2E': Performance(
        id='6IF-B2E',
        event_id='6IF',
        date_time=datetime.datetime(2017, 7, 5, 19, 30, tzinfo=tzoffset(None, 3600)),
        date_description='Wed, 5th July 2017',
        time_description='7.30 PM',
        has_pool_seats=True,
        is_limited=False,
        is_ghost=False,
        running_time=120,
    ),
    '6IF-B2F': Performance(
        id='6IF-B2F',
        event_id='6IF',
        date_time=datetime.datetime(2017, 7, 6, 19, 30, tzinfo=tzoffset(None, 3600)),
        date_description='Thu, 6th July 2017',
        time_description='7.30 PM',
        has_pool_seats=True,
        is_limited=False,
        is_ghost=False,
        running_time=120,
    )
}
```

Attribute | Description
--------- | -----------
`date_desc` | human readable date description
`event_id` | Unique identifier for the event that this performance is an instance of
`has_pool_seats` | Whether this performance has seats available from the general pool of tickets. This is ignored by most partners.
`is_ghost` | `true` when this performance is no longer on sale. When you request performances for an event we will only return performances that are on sale (so `is_ghost` will always be `false`), however it is possible to request [performances by ID](#performances-by-id) for a ghost performance.
`is_limited` | Indicates whether the supply of tickets is known to be extremely limited. This is usually taken to be fewer than four remaining, though not all ticket sources provide this information. Where the information is not available the flag will always be set to `false`
`iso8601_date_and_time` | ISO 8601 date and time
`perf_id` | Unique identifier for the performance
`perf_name` | A human readable description of the performance. Performance names are not always present but must be displayed when they are. Performance names are typically used where performances of an event differ significantly in more than just date and time (sometimes the supplier system will not return the time to us in a structured format but it will instead be included as part of the performance name). Event ID 6IF includes some performances with names.
`running_time` | The length / duration of the performance in minutes
`time_desc` | human readable time description


## --- Extra parameters ---

There are several additional parameters described below that can be provided to
return additional data for each performance.

These additional parameters require extra processing to retrieve the requested
data, which will slow down the resource, sometimes substantially. They should
therefore only be used where necessary.


## Cost ranges

Cost ranges are a cached summary of the pricing that has been seen for your
username. They are used to retrieve the minimum (or "from") price for
the performance, along with detail of any offers or discounts. They allow you to
display a from price and whether there is an offer for each performance, so most
partners request cost ranges.

Cost ranges are generated from availability requests made either by end-users or
by scheduled processes that Ingresso use to update cost range data. You should
not attempt to make multiple availability requests in order to keep this data up
to date - please contact us instead to discuss options api@ingresso.co.uk.  Cost
ranges are only ever returned as part of a parent object. 

<aside class="notice"> Cost ranges (and avail details) are not guaranteed to be
present (nor accurate) so you should design your application with this in mind,
for example by still displaying a performance even if it is missing a from
price.</aside>


### Request

> **Example request**

```shell
curl https://demo.ticketswitch.com/f13/performances_by_id.v1 \
    -u "demo:demopass" \
    -d "perf_id_list=6L9-M5P" \
    -d "req_cost_range" \
    -d "req_cost_range_best_value_offer" \
    -d "req_cost_range_max_saving_offer" \
    -d "req_cost_range_min_cost_offer" \
    -d "req_cost_range_top_price_offer" \
    -d "req_cost_range_no_singles_data" \
    --compressed \
    -G
```

```python
from pyticketswitch import Client


client = Client('demo', 'demopass')
performances, meta = client.get_performances(
    performance_ids=['6L9-M4C'],
    cost_range=True,
    best_value_offer=True,
    max_saving_offer=True,
    min_cost_offer=True,
    top_price_offer=True,
    no_singles_data=True,
)
```

On its own, `req_cost_range` will add min and max prices only, however by
adding one of the other 5 parameters below you can receive additional data
(multiple parameters are provided because there can be multiple definitions of
the "best" offer). Most API users request cost ranges.


Parameter | Description
--------- | -----------
`req_cost_range` | Returns cost ranges for each performance. 
`req_cost_range_best_value_offer` | Returns the offer with the highest percentage saving. This is the most commonly used offer cost range.
`req_cost_range_max_saving_offer` | Returns the offer with the highest absolute saving.
`req_cost_range_min_cost_offer` | Returns the offer with the lowest cost.
`req_cost_range_top_price_offer` | Returns the offer with the highest cost. This is the least used offer cost range.
`req_cost_range_no_singles_data` | This returns another cost range object that excludes availability with only 1 consecutive seat available. The prices in this cost range will therefore be the same or higher than the outer cost range. It has the same structure as the main cost range (so if you want to see the "best value offer" in the no singles data, you need to add `req_cost_range_best_value_offer` and you will see this data in both cost ranges).


### Response

> **Example response**

```shell
{
  "currency_details": {
    "usd": {
      "currency_code": "usd",
      "currency_factor": 100,
      "currency_number": 840,
      "currency_places": 2,
      "currency_post_symbol": "",
      "currency_pre_symbol": "$"
    }
  },
  "performances_by_id": {
    "6L9-M2R": {
      "cached_max_seats": 15,
      "cost_range": {
        "best_value_offer": {
          "absolute_saving": 9,
          "full_seatprice": 75,
          "full_surcharge": 5,
          "offer_seatprice": 68,
          "offer_surcharge": 3,
          "percentage_saving": 11
        },
        "max_saving_offer": {
          "absolute_saving": 9,
          "full_seatprice": 75,
          "full_surcharge": 5,
          "offer_seatprice": 68,
          "offer_surcharge": 3,
          "percentage_saving": 11
        },
        "max_seatprice": 68,
        "max_surcharge": 3,
        "min_cost_offer": {
          "absolute_saving": 9,
          "full_seatprice": 75,
          "full_surcharge": 5,
          "offer_seatprice": 68,
          "offer_surcharge": 3,
          "percentage_saving": 11
        },
        "min_seatprice": 68,
        "min_surcharge": 3,
        "no_singles_cost_range": {
          "best_value_offer": {
            "absolute_saving": 9,
            "full_seatprice": 75,
            "full_surcharge": 5,
            "offer_seatprice": 68,
            "offer_surcharge": 3,
            "percentage_saving": 11
          },
          "max_saving_offer": {
            "absolute_saving": 9,
            "full_seatprice": 75,
            "full_surcharge": 5,
            "offer_seatprice": 68,
            "offer_surcharge": 3,
            "percentage_saving": 11
          },
          "max_seatprice": 68,
          "max_surcharge": 3,
          "min_cost_offer": {
            "absolute_saving": 9,
            "full_seatprice": 75,
            "full_surcharge": 5,
            "offer_seatprice": 68,
            "offer_surcharge": 3,
            "percentage_saving": 11
          },
          "min_seatprice": 68,
          "min_surcharge": 3,
          "range_currency_code": "usd",
          "top_price_offer": {
            "absolute_saving": 9,
            "full_seatprice": 75,
            "full_surcharge": 5,
            "offer_seatprice": 68,
            "offer_surcharge": 3,
            "percentage_saving": 11
          },
          "valid_quantities": [
            1,
            2,
            3,
            4
          ]
        },
        "range_currency_code": "usd",
        "top_price_offer": {
          "absolute_saving": 9,
          "full_seatprice": 75,
          "full_surcharge": 5,
          "offer_seatprice": 68,
          "offer_surcharge": 3,
          "percentage_saving": 11
        },
        "valid_quantities": [
          1,
          2,
          3,
          4
        ]
      },
      "date_desc": "Wed, 5th April 2017",
      "event_id": "6L9",
      "has_pool_seats": true,
      "is_ghost": false,
      "is_limited": false,
      "iso8601_date_and_time": "2017-04-05T22:30:00-07:00",
      "perf_id": "6L9-M2R",
      "running_time": 120,
      "time_desc": "10.30 PM"
    }
  }
}
```

FIXME: no cost ranges on this performance

```python
from pyticketswitch.performance import Performance

{
    '6L9-M2R': Performance(
        id='6L9-M2R',
        event_id='6L9',
        date_time=datetime.datetime(2017, 4, 5, 22, 30, tzinfo=tzoffset(None, -25200)),
        date_description='Wed, 5th April 2017',
        time_description='10.30 PM',
        has_pool_seats=False,
        is_limited=False,
        cached_max_seats=None,
        cost_range=None,
        no_singles_cost_range=None,
        is_ghost=True,
        name=None,
        running_time=None,
        availability_details=[
            
        ],
    )
}
```

<aside class="notice">In the UK market, offers are typically classified as
either "discounted face value" or "no booking fee". Discounted face value
offers have an offer seatprice that is lower than the full price seatprice (and
normally they have an offer surcharge of zero). No booking fee offers have an
offer seatprice equal to the full price seatprice, and an offer surcharge equal
to zero. The standard is to not display a saving percentage for no booking fee
offers (some suppliers do not like their offers to show as percentage
discounts), and instead display "No fees" or similar.</aside>

Each performance returned includes a `cost_range` object with the following attributes:

Attribute | Description
--------- | -----------
`best_value_offer` | The offer with the highest percentage saving. This is the most commonly used offer cost range.
`max_saving_offer` | The offer with the highest absolute saving.
`max_seatprice` | The per-ticket face value for the highest price (seatprice + surcharge)
`max_surcharge` | The per-ticket booking fee for the highest price (seatprice + surcharge)
`min_cost_offer` | The offer with the lowest cost.
`min_seatprice` | The per-ticket face value for the lowest price (seatprice + surcharge)
`min_surcharge` | The per-ticket booking fee for the lowest price (seatprice + surcharge)
`no_singles_cost_range` | This returns another cost range object that excludes availability with only one consecutive seat available. The prices in this cost range will therefore be the same as or higher than the outer cost range.
`range_currency_code` | The currency code for the cost range - further detail for the currency can be found in the `currency_details` object, described below.
`top_price_offer` | The offer with the highest cost. This is the least used offer cost range.
`valid_quantities` | An array of ticket quantities with availability.

The **offer** objects contain the following attributes:

Attribute | Description
--------- | -----------
`absolute_saving` | Defined as (`full_seatprice` + `full_surcharge`) - (`offer_seatprice` + `offer_surcharge`).
`full_seatprice` | The non-offer per-ticket face value.
`full_surcharge` | The non-offer per-ticket booking fee.
`offer_seatprice` | The offer per-ticket face value.
`offer_surcharge` | The offer per-ticket booking fee.
`percentage_saving` | Defined as `absolute_saving` / (`full_seatprice` + `full_surcharge`) * 100.

The outer object includes a **currency_details** object containing one currency
object (indexed on the currency code) for every currency referenced in the
JSON response. Each currency has the following attributes:

Attribute | Description
--------- | -----------
`currency_code` | [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217) three letter code
`currency_factor` | Multiply by this number to get values in the base unit (e.g. multiplying $47.11 by the currency_factor will give 4711 cents)
`currency_number` | [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217) numeric identifier
`currency_places` | The number of decimal places to display (eg 45.5 usd should be displayed as 45.50)
`currency_post_symbol` | A symbol to display at the end of the price
`currency_pre_symbol` | A symbol to display in front of the price


## Availability detail

Availability details are a cached list of the price bands for each performance.
They are cached from previous availability requests made via your username. This
data can be useful for example if you wish to display a separate page with
pricing detail for all price bands on sale.

Availability details are generated from availability requests made either by
end-users or by scheduled processes that Ingresso use to update this data. You
should not attempt to make multiple availability requests in order to keep this
data up to date - please contact us instead to discuss options
[api@ingresso.co.uk](mailto:api@ingresso.co.uk).

### Request

> **Example request**

```shell
curl https://demo.ticketswitch.com/f13/performances_by_id.v1 \
    -u "demo:demopass" \
    -d "perf_id_list=6L9-M2R" \
    -d "req_avail_details" \
    --compressed \
    -G
```

```python
from pyticketickswitch import client

client = Client('demo', 'demopass')
performances, meta = client.get_performances(['6L9-M2R'], availability=True)
```

Parameter | Description
--------- | -----------
`req_avail_details` | Returns a list of unique ticket types and price bands that are available for this performance. **This parameter is not commonly used.**

### Response

> **Example Response**

```shell
{
  "currency_details": {
    "usd": {
      "currency_code": "usd",
      "currency_factor": 100,
      "currency_number": 840,
      "currency_places": 2,
      "currency_post_symbol": "",
      "currency_pre_symbol": "$"
    }
  },
  "performances_by_id": {
    "6L9-M2R": {
      "avail_details": {
        "ticket_type": [
          {
            "price_band": [
              {
                "avail_detail": [
                  {
                    "absolute_saving": 9,
                    "avail_currency_code": "usd",
                    "cached_number_available": 4,
                    "full_seatprice": 75,
                    "full_surcharge": 5,
                    "percentage_saving": 11,
                    "seatprice": 68,
                    "surcharge": 3,
                    "valid_quantities": [
                      1,
                      2,
                      3,
                      4
                    ]
                  }
                ],
                "price_band_code": "A",
                "price_band_desc": ""
              }
            ],
            "ticket_type_code": "PREF",
            "ticket_type_desc": "Preferred seating"
          }
        ]
      },
      "date_desc": "Wed, 5th April 2017",
      "event_id": "6L9",
      "has_pool_seats": true,
      "is_ghost": false,
      "is_limited": false,
      "iso8601_date_and_time": "2017-04-05T22:30:00-07:00",
      "perf_id": "6L9-M2R",
      "running_time": 120,
      "time_desc": "10.30 PM"
    }
  }
}
```

```python
{
    '6IF-A8K': pyticketswitch.Performance(
        id='6IF-A8K',
        availabilty_details=[
            pyticketswitch.AvailabilityDetails(
                ticket_type='BALCONY',
                ticket_type_description='Balcony',
                price_band='A',
                price_band_description='',
                seatprice=47.0,
                surcharge=0.0,
                currency=pyticketswitch.Currency(
                    code='gbp',
                ),
                valid_quantities=[2, 3, 4, 5, 6, 7],
            ),
            pyticketswitch.AvailabilityDetails(
                ticket_type='CIRCLE',
                ticket_type_description='Upper circle',
                price_band='A',
                price_band_description='',
                seatprice=35.0,
                surcharge=0.0,
                currency=pyticketswitch.Currency(
                    code='gbp',
                ),
                valid_quantities=[2, 3, 4, 5, 6, 7],
            ),
            pyticketswitch.AvailabilityDetails(
                ticket_type='CIRCLE',
                ticket_type_description='Upper circle',
                price_band='B',
                price_band_description='',
                seatprice=30.0,
                surcharge=0.0,
                currency=pyticketswitch.Currency(
                    code='gbp',
                ),
                valid_quantities=[2, 3, 4, 5, 6, 7],
            ),
            pyticketswitch.AvailabilityDetails(
                ticket_type='CIRCLE',
                ticket_type_description='Upper circle',
                price_band='C',
                price_band_description='',
                seatprice=25.0,
                surcharge=0.0,
                currency=pyticketswitch.Currency(
                    code='gbp',
                ),
                valid_quantities=[2, 3, 4, 5, 6, 7],
            ),
            pyticketswitch.AvailabilityDetails(
                ticket_type='STALLS',
                ticket_type_description='Stalls',
                price_band='A',
                price_band_description='',
                seatprice=21.0,
                surcharge=0.0,
                currency=pyticketswitch.Currency(
                    code='gbp',
                ),
                valid_quantities=[2, 3, 4, 5, 6, 7],
            ),
            pyticketswitch.AvailabilityDetails(
                ticket_type='STALLS',
                ticket_type_description='Stalls',
                price_band='B',
                price_band_description='',
                seatprice=18.0,
                surcharge=0.0,
                currency=pyticketswitch.Currency(
                    code='gbp',
                ),
                valid_quantities=[2, 3, 4, 5, 6, 7],
            ),
        ],
    )
}
```

**Ticket types** describe a part of house or location within the venue.

Attribute | Description
--------- | -----------
`price_band` | A list of price bands, object described below.
`ticket_type_code` | The identifier of the ticket type, this can be used later in the [trolley](#trolley) or [reserve](#reserve) resource.
`ticket_type_desc` | A human readable description of the price band if applicable.


**Price bands** describe the different levels of pricing that are available within a
ticket type. 

Attribute | Description
--------- | -----------
`avail_details` | A list of the prices in this price band and when they are available.
`price_band_code` | The identifier of the price band, this can be used later in the [trolley](#trolley) or [reserve](#reserve) resource.
`price_band_desc` | A human readable description of the price band if applicable.

The **avail detail** indicates what prices we have seen for this performance.

Attribute | Description
--------- | -----------
`absolute_saving` | Defined as (`full_seatprice` + `full_surcharge`) - (`seatprice` + `surcharge`)
`avail_currency_code` | The currency code for the price - further detail for the currency can be found in the `currency_details` object, described below.
`cached_number_available` | The maximum number of consecutive tickets available.
`full_seatprice` | The per-ticket face value for the non-discounted ticket price.
`full_surcharge` | The per-ticket booking fee for the non-discounted ticket price.
`percentage_saving` | Defined as `absolute_saving` / (`full_seatprice` + `full_surcharge`) * 100
`seatprice` | The per-ticket face value.
`surcharge` | The per-ticket booking fee.
`valid_quantities` | An array of available quantities we have seen for this price band.

The outer object includes a **currency_details** object containing one currency
object (indexed on the currency code) for every currency referenced in the
JSON response. Each currency has the following attributes:

Attribute | Description
--------- | -----------
`currency_code` | [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217) three letter code
`currency_factor` | Multiply by this number to get values in the base unit (e.g. multiplying $47.11 by the currency_factor will give 4711 cents)
`currency_number` | [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217) numeric identifier
`currency_places` | The number of decimal places to display (eg 45.5 usd should be displayed as 45.50)
`currency_post_symbol` | A symbol to display at the end of the price
`currency_pre_symbol` | A symbol to display in front of the price
