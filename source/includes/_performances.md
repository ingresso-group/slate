# Performances

A performance describes an instance of an event, for example the Saturday 21st
January 7.30pm showing of The Lion King. This is the case for the majority of
our product, however since not all events (and the systems we connect to) are
the same, there are some exceptions:

- Some events have performances that do not have a time component - they are
  valid for the entire day
- Some events have performances with a performance name rather than a time
  (TODO: add more detail)
- Some events do not have performances - they are valid on all dates (TODO: add
  more detail)
- Departure and usage dates, (TODO do we need these?)

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
    -d "page_len=10" \
    -G
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
performances = client.list_performances('6IF')
```

Parameter | Description
--------- | -----------
`event_id` | Identifier of the event you want to see performances for.
`page_len` | Length of a page, default 50.
`page_no` | Page number, default 0, ignored if page_len is not present.
`s_dates` | Date range in the form `yyyymmdd:yyyymmdd` (both are optional)

These parameters can be passed in to request additional data for each 
performance, and are described in more detail in the 
[extra parameters](#extra-parameters) section below:

Parameter | Description
--------- | -----------
`req_avail_details` | Returns [availability details](#availability-detail) - a cached list of unique ticket types and price bands available for this performance.
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
    "performance": [
      {
        "cached_max_seats": 6,
        "date_desc": "Thu, 22nd December 2016",
        "event_id": "6IF",
        "has_pool_seats": true,
        "is_ghost": false,
        "is_limited": false,
        "iso8601_date_and_time": "2016-12-22T19:30:00Z",
        "perf_id": "6IF-A6Z",
        "running_time": 120,
        "time_desc": "7.30 PM"
      },
      {
        "cached_max_seats": 6,
        "date_desc": "Fri, 23rd December 2016",
        "event_id": "6IF",
        "has_pool_seats": true,
        "is_ghost": false,
        "is_limited": false,
        "iso8601_date_and_time": "2016-12-23T19:30:00Z",
        "perf_id": "6IF-A70",
        "running_time": 120,
        "time_desc": "7.30 PM"
      },
      {
        "cached_max_seats": 6,
        "date_desc": "Sun, 25th December 2016",
        "event_id": "6IF",
        "has_pool_seats": true,
        "is_ghost": false,
        "is_limited": false,
        "iso8601_date_and_time": "2016-12-25T19:30:00Z",
        "perf_id": "6IF-A72",
        "running_time": 120,
        "time_desc": "7.30 PM"
      },
      {
        "cached_max_seats": 6,
        "date_desc": "Mon, 26th December 2016",
        "event_id": "6IF",
        "has_pool_seats": true,
        "is_ghost": false,
        "is_limited": false,
        "iso8601_date_and_time": "2016-12-26T19:30:00Z",
        "perf_id": "6IF-A73",
        "running_time": 120,
        "time_desc": "7.30 PM"
      },
      {
        "cached_max_seats": 6,
        "date_desc": "Tue, 27th December 2016",
        "event_id": "6IF",
        "has_pool_seats": true,
        "is_ghost": false,
        "is_limited": false,
        "iso8601_date_and_time": "2016-12-27T19:30:00Z",
        "perf_id": "6IF-A74",
        "perf_name": "Including back stage pass",
        "running_time": 120,
        "time_desc": "7.30 PM"
      },
      {
        "cached_max_seats": 6,
        "date_desc": "Wed, 28th December 2016",
        "event_id": "6IF",
        "has_pool_seats": true,
        "is_ghost": false,
        "is_limited": false,
        "iso8601_date_and_time": "2016-12-28T19:30:00Z",
        "perf_id": "6IF-A75",
        "running_time": 120,
        "time_desc": "7.30 PM"
      },
      {
        "cached_max_seats": 6,
        "date_desc": "Thu, 29th December 2016",
        "event_id": "6IF",
        "has_pool_seats": true,
        "is_ghost": false,
        "is_limited": false,
        "iso8601_date_and_time": "2016-12-29T19:30:00Z",
        "perf_id": "6IF-A76",
        "running_time": 120,
        "time_desc": "7.30 PM"
      },
      {
        "cached_max_seats": 6,
        "date_desc": "Fri, 30th December 2016",
        "event_id": "6IF",
        "has_pool_seats": true,
        "is_ghost": false,
        "is_limited": false,
        "iso8601_date_and_time": "2016-12-30T19:30:00Z",
        "perf_id": "6IF-A77",
        "running_time": 120,
        "time_desc": "7.30 PM"
      },
      {
        "cached_max_seats": 6,
        "date_desc": "Sun, 1st January 2017",
        "event_id": "6IF",
        "has_pool_seats": true,
        "is_ghost": false,
        "is_limited": false,
        "iso8601_date_and_time": "2017-01-01T19:30:00Z",
        "perf_id": "6IF-A79",
        "running_time": 120,
        "time_desc": "7.30 PM"
      },
      {
        "cached_max_seats": 6,
        "date_desc": "Mon, 2nd January 2017",
        "event_id": "6IF",
        "has_pool_seats": true,
        "is_ghost": false,
        "is_limited": false,
        "iso8601_date_and_time": "2017-01-02T19:30:00Z",
        "perf_id": "6IF-A7A",
        "running_time": 120,
        "time_desc": "7.30 PM"
      }
    ]
  }
}
```

```python
[
    pyticketswitch.Performance(
        id='6IF-A6Z',
        event_id='6IF',
        date_time=datetime.datetime(2016, 12, 22, 19, 30, 0, 0),
        has_pool_seats=True,
        is_limited=False,
        cached_max_seats=6,
    ),
    pyticketswitch.Performance(
        id='6IF-A70',
        event_id='6IF',
        date_time=datetime.datetime(2016, 12, 23, 19, 30, 0, 0),
        has_pool_seats=True,
        is_limited=False,
        cached_max_seats=6,
    ),
    pyticketswitch.Performance(
        id='6IF-A72',
        event_id='6IF',
        date_time=datetime.datetime(2016, 12, 25, 19, 30, 0, 0),
        has_pool_seats=True,
        is_limited=False,
        cached_max_seats=6,
    ),
    pyticketswitch.Performance(
        id='6IF-A73',
        event_id='6IF',
        date_time=datetime.datetime(2016, 12, 26, 19, 30, 0, 0),
        has_pool_seats=True,
        is_limited=False,
        cached_max_seats=6,
    ),
    pyticketswitch.Performance(
        id='6IF-A74',
        event_id='6IF',
        date_time=datetime.datetime(2016, 12, 27, 19, 30, 0, 0),
        has_pool_seats=True,
        is_limited=False,
        cached_max_seats=6,
    ),
    pyticketswitch.Performance(
        id='6IF-A75',
        event_id='6IF',
        date_time=datetime.datetime(2016, 12, 28, 19, 30, 0, 0),
        has_pool_seats=True,
        is_limited=False,
        cached_max_seats=6,
    ),
    pyticketswitch.Performance(
        id='6IF-A76',
        event_id='6IF',
        date_time=datetime.datetime(2016, 12, 29, 19, 30, 0, 0),
        has_pool_seats=True,
        is_limited=False,
        cached_max_seats=6,
    ),
    pyticketswitch.Performance(
        id='6IF-A77',
        event_id='6IF',
        date_time=datetime.datetime(2016, 12, 30, 19, 30, 0, 0),
        has_pool_seats=True,
        is_limited=False,
        cached_max_seats=6,
    ),
    pyticketswitch.Performance(
        id='6IF-A79',
        event_id='6IF',
        date_time=datetime.datetime(2017, 1, 1, 19, 30, 0, 0),
        has_pool_seats=True,
        is_limited=False,
        cached_max_seats=6,
    ),
    pyticketswitch.Performance(
        id='6IF-A7A',
        event_id='6IF',
        date_time=datetime.datetime(2017, 1, 2, 19, 30, 0, 0),
        has_pool_seats=True,
        is_limited=False,
        cached_max_seats=6,
    ),
]
```

### Results

Attribute | Description
--------- | -----------
`events_by_id` | A list of events related to the performances returned. This is only present for meta events (events that are made up of multiple component events, for example a touring show).
`has_perf_names` | Whether the performances returned have performance names (human readable descriptions of the performance). Performance names are not always present but must be displayed when they are. Performance names are typically used where performances of an event differ significantly in more than just date and time (sometimes the supplier system will not return the time to us in a structured format but it will instead be included as part of the performance name). An example performance with a performance name is 6IF-A5R.
`performance` | An array of performance objects, described below.

**Performance**

Attribute | Description
--------- | -----------
`perf_id` | Unique identifier for the performance
`perf_name` | A human readable description of the performance. Performance names are not always present but must be displayed when they are. Performance names are typically used where performances of an event differ significantly in more than just date and time (sometimes the supplier system will not return the time to us in a structured format but it will instead be included as part of the performance name). An example performance with a performance name is 6IF-A5R.
`event_id` | Unique identifier for the event that this performance is an instance of
`iso8601_date_and_time` | ISO 8601 date and time
`date_desc` | human readable date description
`time_desc` | human readable time description
`is_limited` | Indicates whether the supply of tickets is known to be extremely limited. This is usually taken to be fewer than four remaining, though not all ticket sources provide this information. Where the information is not available the flag will always be set to `false`
`has_pool_seats` | (TODO: Pete what does this mean exactly?)
`is_ghost` | (TODO: Pete in what scenario would you return perfs with is_ghost = true?)
`cached_max_seats` | This is the maximum number of contiguous seats that can be booked for this performance, based on Ingresso's cached data. Note that if the event supports seat selection you are not limited by the number of contiguous seats, so this value should be used in the context of best available only. (TODO is this optional?)
`running_time` | The length / duration of the performance in minutes


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
    -d "perf_id_list=6IF-A8J,6IF-A8K" \
    -G
```

```python
from pyticketswitch import Client


client = Client('demo', 'demopass')
performances = client.get_performances(['6IF-A5R'])
```

Parameter | Description
--------- | -----------
`perf_id_list` | A comma separated list of performance IDs e.g. `6IF-A5R` for a single performance; `6IF-A5R,6IF-A5S` for multiple performances.

These parameters can be passed in to request additional data for each 
performance, and are described in more detail in the 
[extra parameters](#extra-parameters) section below:

Parameter | Description
--------- | -----------
`req_avail_details` | Returns [availability details](#availability-detail) - a cached list of unique ticket types and price bands available for this performance.
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
    "6IF-A8J": {
      "date_desc": "Thu, 16th February 2017",
      "event_id": "6IF",
      "has_pool_seats": true,
      "is_ghost": false,
      "is_limited": false,
      "iso8601_date_and_time": "2017-02-16T19:30:00Z",
      "perf_id": "6IF-A8J",
      "running_time": 120,
      "time_desc": "7.30 PM",
      "cached_max_seats": 123
    },
    "6IF-A8K": {
      "date_desc": "Fri, 17th February 2017",
      "event_id": "6IF",
      "has_pool_seats": true,
      "is_ghost": false,
      "is_limited": true,
      "iso8601_date_and_time": "2017-02-17T19:30:00Z",
      "perf_id": "6IF-A8K",
      "running_time": 120,
      "time_desc": "7.30 PM",
      "cached_max_seats": 456
    }
  }
}
```

```python
{
    '6IF-A8J': pyticketswitch.Performance(
        id='6IF-A8J',
        event_id='6IF',
        date_time=datetime.datetime(2017, 2, 16, 19, 30, 0, 0),
        has_pool_seats=True,
        is_limited=False,
        cached_max_seats=123,
    ),
    '6IF-A8J': pyticketswitch.Performance(
        id='6IF-A8K',
        event_id='6IF',
        date_time=datetime.datetime(2017, 2, 17, 19, 30, 0, 0),
        has_pool_seats=True,
        is_limited=True,
        cached_max_seats=456,
    ),
}
```

Attribute | Description
--------- | -----------
`perf_id` | Unique identifier for the performance.
`perf_name` | A human readable description of the performance. Performance names are not always present but must be displayed when they are. Performance names are typically used where performances of an event differ significantly in more than just date and time (sometimes the supplier system will not return the time to us in a structured format but it will instead be included as part of the performance name). An example performance with a performance name is 6IF-A5R.
`event_id` | Unique identifier for the event that this performance is an instance of.
`iso8601_date_and_time` | ISO 8601 date and time.
`date_desc` | Human readable date description.
`time_desc` | Human readable time description.
`is_limited` | Indicates whether the supply of tickets is known to be extremely limited. This is usually taken to be fewer than four remaining, though not all ticket sources provide this information. Where the information is not available the flag will always be set to `false`.
`has_pool_seats` | (TODO: Pete what does this mean exactly?)
`is_ghost` | (TODO: Pete in what scenario would you return perfs with is_ghost = true?)
`cached_max_seats` | This is the maximum number of contiguous seats that can be booked for this performance, based on Ingresso's cached data. Note that if the event supports seat selection you are not limited by the number of contiguous seats, so this value should be used in the context of best available only. (TODO is this optional?)
`running_time` | The length / duration of the performance in minutes.


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
curl https://demo.ticketswitch.com/f13/performancess_by_id.v1 \
    -u "demo:demopass" \
    -d "perf_id_list=6L9-M2R" \
    -d "req_cost_range" \
    -d "req_cost_range_best_value_offer" \
    -d "req_cost_range_max_saving_offer" \
    -d "req_cost_range_min_cost_offer" \
    -d "req_cost_range_top_price_offer" \
    -d "req_cost_range_no_singles_data" \
    -G
```

```python
from pyticketswitch import Client


client = Client('demo', 'demopass')
events = client.get_performances(
    event_ids=['6IF-A8K'],
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
          "quantity_options": {
            "valid_quantity_bitmask": 30
          },
          "range_currency": {
            "currency_code": "usd"
          },
          "top_price_offer": {
            "absolute_saving": 9,
            "full_seatprice": 75,
            "full_surcharge": 5,
            "offer_seatprice": 68,
            "offer_surcharge": 3,
            "percentage_saving": 11
          }
        },
        "quantity_options": {
          "valid_quantity_bitmask": 30
        },
        "range_currency": {
          "currency_code": "usd"
        },
        "top_price_offer": {
          "absolute_saving": 9,
          "full_seatprice": 75,
          "full_surcharge": 5,
          "offer_seatprice": 68,
          "offer_surcharge": 3,
          "percentage_saving": 11
        }
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
pyticketswitch.Performance(
    id='6IF-A8K',
    cost_range=pyticketswitch.CostRange(
        valid_quantities=[2, 3, 4, 5, 6, 7],
        max_seatprice=47.0,
        max_surcharge=0.0,
        min_seatprice=18.0,
        min_surcharge=0.0,
        currency=pyticketswitch.Currency(
            code='gbp',
        )
    ),
    no_singles_cost_range = pyticketswitch.CostRange(
        valid_quantities=[2, 3, 4, 5, 6, 7],
        max_seatprice=47.0,
        max_surcharge=0.0,
        min_seatprice=18.0,
        min_surcharge=0.0,
        currency=pyticketswitch.Currency(
            code='gbp',
        )
    ),
)
```

<aside class="notice">Particularly in the UK market, offers are classified as 
either "discounted face value" or "no booking fee". Discounted face value 
offers have an offer seatprice that is lower than the full price seatprice (and
normally they have an offer surcharge of zero). No booking fee offers have
an offer seatprice equal to the full price seatprice, and an offer surcharge
equal to zero. The standard is to not display a saving percentage for no booking
fee offers (some suppliers do not like their offers to show as percentage 
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
`quantity_options` | The ticket quantities that have availability.
`range_currency` | The currency for the cost range.
`top_price_offer` | The offer with the highest cost. This is the least used offer cost range.

The **offer** objects contain the following attributes:

Attribute | Description
--------- | -----------
`absolute_saving` | Defined as (`full_seatprice` + `full_surcharge`) - (`offer_seatprice` + `offer_surcharge`).
`full_seatprice` | The non-offer per-ticket face value.
`full_surcharge` | The non-offer per-ticket booking fee.
`offer_seatprice` | The offer per-ticket face value.
`offer_surcharge` | The offer per-ticket booking fee.
`percentage_saving` | Defined as `absolute_saving` / (`full_seatprice` + `full_surcharge`) * 100.


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
    -d "perf_id_list=6IF-A8K" \
    -d "req_avail_details" \
    -G
```

```python
from pyticketickswitch import client

client = Client('demo', 'demopass')
client.get_events(['6IF'], availability=True, availability_with_performances=True)
```

Parameter | Description
--------- | -----------
`req_avail_details` | Returns a list of unique ticket types and price bands that are available for this performance.

### Response

> **Example Response**

```shell
{
  "performances_by_id": {
    "6IF-A8K": {
      "avail_details": {
        "ticket_type": [
          {
            "price_band": [
              {
                "avail_detail": [
                  {
                    "avail_currency": {
                      "currency_code": "gbp"
                    },
                    "cached_number_available": 6,
                    "quantity_options": {
                      "valid_quantity_bitmask": 126
                    },
                    "seatprice": 47,
                    "surcharge": 0
                  }
                ],
                "price_band_code": "A",
                "price_band_desc": ""
              }
            ],
            "ticket_type_code": "BALCONY",
            "ticket_type_desc": "Balcony"
          },
          {
            "price_band": [
              {
                "avail_detail": [
                  {
                    "avail_currency": {
                      "currency_code": "gbp"
                    },
                    "cached_number_available": 6,
                    "quantity_options": {
                      "valid_quantity_bitmask": 126
                    },
                    "seatprice": 35,
                    "surcharge": 0
                  }
                ],
                "price_band_code": "A",
                "price_band_desc": ""
              },
              {
                "avail_detail": [
                  {
                    "avail_currency": {
                      "currency_code": "gbp"
                    },
                    "cached_number_available": 6,
                    "quantity_options": {
                      "valid_quantity_bitmask": 126
                    },
                    "seatprice": 30,
                    "surcharge": 0
                  }
                ],
                "price_band_code": "B",
                "price_band_desc": ""
              },
              {
                "avail_detail": [
                  {
                    "avail_currency": {
                      "currency_code": "gbp"
                    },
                    "cached_number_available": 6,
                    "quantity_options": {
                      "valid_quantity_bitmask": 126
                    },
                    "seatprice": 25,
                    "surcharge": 0
                  }
                ],
                "price_band_code": "C",
                "price_band_desc": ""
              }
            ],
            "ticket_type_code": "CIRCLE",
            "ticket_type_desc": "Upper circle"
          },
          {
            "price_band": [
              {
                "avail_detail": [
                  {
                    "avail_currency": {
                      "currency_code": "gbp"
                    },
                    "cached_number_available": 6,
                    "quantity_options": {
                      "valid_quantity_bitmask": 126
                    },
                    "seatprice": 21,
                    "surcharge": 0
                  }
                ],
                "price_band_code": "A",
                "price_band_desc": ""
              },
              {
                "avail_detail": [
                  {
                    "avail_currency": {
                      "currency_code": "gbp"
                    },
                    "cached_number_available": 3,
                    "quantity_options": {
                      "valid_quantity_bitmask": 14
                    },
                    "seatprice": 18,
                    "surcharge": 0
                  }
                ],
                "price_band_code": "B",
                "price_band_desc": ""
              }
            ],
            "ticket_type_code": "STALLS",
            "ticket_type_desc": "Stalls"
          }
        ]
      },
      "date_desc": "Fri, 17th February 2017",
      "event_id": "6IF",
      "has_pool_seats": true,
      "is_ghost": false,
      "is_limited": true,
      "iso8601_date_and_time": "2017-02-17T19:30:00Z",
      "perf_id": "6IF-A8K",
      "running_time": 120,
      "time_desc": "7.30 PM"
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
`ticket_type_code` | The identifier of the ticket type, this can be used later in the [trolley](#trolley) or [reserve](#reserve) resource.
`ticket_type_desc` | A human readable description of the price band if applicable.
`price_band` | A list of price bands.


**Price bands** describe the different levels of pricing that are available within a
ticket type. 

Attribute | Description
--------- | -----------
`price_band_code` | The identifier of the price band, this can be used later in the [trolley](#trolley) or [reserve](#reserve) resource.
`price_band_desc` | A human readable description of the price band if applicable.
`avail_details` | A list of the prices in this price band and when they are available.

The **avail details** indicate what prices we have seen for this performance.

Attribute | Description
--------- | -----------
`avail_currency` | The price [currency](#currency-object).
`cached_number_available` | The maximum number of consecutive tickets available.
`quantity_options.valid_quantity_bitmask` | the available quantities we have seen for this price band.
`seatprice` | The per-ticket face value.
`surcharge` | The per-ticket booking fee.
