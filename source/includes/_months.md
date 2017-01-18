# Months

```
GET https://demo.ticketswitch.com/f13/months.v1?event_id={eventid}
```

This resource gives a summary of the availability across a calendar month

Months are provided for those who wish to display month-level summary data; for 
example listing the available months above a calendar. This resource can remove
the need to summarise performance-level data in your application.

> **Example request**

```shell
curl https://demo.ticketswitch.com/f13/months.v1 \
    -u "demo:demopass" \
    -d "event_id=6IF" \
    -G
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
client.get_months('6IF')
```

### Request

Parameter | Description
--------- | -----------
`event_id` | Identifier of the event to summarise

These parameters can be passed in to request additional data for each 
performance, and are described in more detail in the 
[extra parameters](#extra-parameters) section above for performances.

Parameter | Description
--------- | -----------
`req_avail_details` | Returns [availability details](#availability-detail) - a cached list of unique ticket types and price bands available for this event across all performances in the month. **This parameter is not commonly used.**
`req_cost_range` | Returns [cost ranges](#cost-ranges) - a from price and offer detail for each event. *Most partners include this parameter.*
`req_cost_range_best_value_offer` | Returns the offer with the highest percentage saving. *This is the most commonly used offer cost range.*
`req_cost_range_details` | Returns a list of unique ticket types and price bands and their cost ranges across all performances.
`req_cost_range_max_saving_offer` | Returns the offer with the highest absolute saving.
`req_cost_range_min_cost_offer` | Returns the offer with the lowest cost.
`req_cost_range_top_price_offer` | Returns the offer with the highest cost. This is the least used offer cost range.
`req_cost_range_no_singles_data` | This returns another cost range object that excludes availability with only 1 consecutive seat available. The prices in this cost range will therefore be the same or higher than the outer cost range. It has the same structure as the main cost range (so if you want to see the "best value offer" in the no singles data, you need to add `req_cost_range_best_value_offer` and you will see this data in both cost ranges).

> **Example Response**

```shell
{
  "results": {
    "month": [
      {
        "month": "dec",
        "month_dates_bitmask": 1065254912,
        "month_desc": "December",
        "month_weekdays_bitmask": 63,
        "year": 2016
      },
      {
        "month": "jan",
        "month_dates_bitmask": 2012209087,
        "month_desc": "January",
        "month_weekdays_bitmask": 63,
        "year": 2017
      },
      {
        "month": "feb",
        "month_dates_bitmask": 251526135,
        "month_desc": "February",
        "month_weekdays_bitmask": 63,
        "year": 2017
      },
      {
        "month": "mar",
        "month_dates_bitmask": 2130574327,
        "month_desc": "March",
        "month_weekdays_bitmask": 63,
        "year": 2017
      },
      {
        "month": "apr",
        "month_dates_bitmask": 16254,
        "month_desc": "April",
        "month_weekdays_bitmask": 63,
        "year": 2017
      }
    ]
  }
}
```

```python
[
    pyticketswitch.Month(
        month=12,
        year=2016,
        description='December',
        month_bitmask=1065254912,
        weekdays_bitmask=63,
    ),
    pyticketswitch.Month(
        month=1,
        year=2017,
        description='January',
        month_bitmask=2012209087,
        weekdays_bitmask=63,
    ),
    pyticketswitch.Month(
        month=2,
        year=2017,
        description='Febuary',
        month_bitmask=251526135,
        weekdays_bitmask=63,
    ),
    pyticketswitch.Month(
        month=3,
        year=2017,
        description='March',
        month_bitmask=2130574327,
        weekdays_bitmask=63,
    ),
    pyticketswitch.Month(
        month=4,
        year=2017,
        description='April',
        month_bitmask=16254,
        weekdays_bitmask=63,
   )
]
```

### Response

<aside class="language-specific shell notice">
    This resource returns bitmasks in some fields, for more information on how
    to decode this data see the <a href="#bitmask-fields">Bitmask Fields</a> 
    section.
</aside>

Attribute | Description
--------- | -----------
`month` | Three-character code for the month.
`month_dates_bitmask` | Indicates which dates in the month have valid performances.
`month_desc` | Full month name.
`month_weekdays_bitmask` | Indicates which weekdays have valid performances for the month.
`year` | The year.