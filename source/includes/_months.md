# Months

TODO:

- month_number has now disappeared but was in Pete's original documentation?
- does page_len apply to months?

This section describes:

- the [month object](#performance-object)
- the API call to [list all months for an event](#list-all-months-for-an-event)

## Month Object

A `month` is a summary of the availability for an event across a calendar month. It is provided primarily for those who wish to display month-level summary data, for example listing the available months above a calendar; using the month object removes the need for you to summarise performance-level data yourself.

The month object can be returned in two alternate but equivalent formats:

1. An explicit and more human-readable version where the available days are listed.
2. A more compact version using bitmasks to represent available days

We have provided examples of both formats.

### Using days explicitly listed

> **Example Response - days explicitly listed**

```json
{
    "month": "oct",
    "month_dates": {
        "day_1": false,
        "day_10": true,
        "day_11": true,
        "day_12": true,
        "day_13": true,
        "day_14": true,
        "day_15": false,
        "day_16": true,
        "day_17": true,
        "day_18": true,
        "day_19": true,
        "day_2": true,
        "day_20": true,
        "day_21": true,
        "day_22": false,
        "day_23": true,
        "day_24": true,
        "day_25": true,
        "day_26": true,
        "day_27": true,
        "day_28": true,
        "day_29": false,
        "day_3": true,
        "day_30": true,
        "day_31": true,
        "day_4": true,
        "day_5": true,
        "day_6": true,
        "day_7": true,
        "day_8": false,
        "day_9": true
    },
    "month_desc": "October",
    "month_weekdays": {
        "fri": true,
        "mon": true,
        "sat": false,
        "sun": true,
        "thu": true,
        "tue": true,
        "wed": true
    },
    "year": 2016
}
```

It is possible to request a number of additional attributes for a month - below we have listed the default attributes first, followed by the optional attributes:

Attribute | Description
--------- | -----------
`month` | three-character code for the month (TODO is this correct?)
`month_dates` | indicates which dates in the month have valid performances
`month_desc` | full month name
`month_weekdays` | indicates which weekdays have valid performances for the month
`year` | the year

These are the optional attributes - these are only returned if you add one of the `req_` parameters to your request.

Attribute | Description
--------- | -----------
`avail_details` | A list of [avail details](#avail-detail-object)
`cost_range` | A list of [cost ranges](#cost-range-object)


> **Example Response - using bitmasks**

```json
{
    "month": "aug",
    "month_date_mask": 2012209072,
    "month_desc": "August",
    "month_day_mask": 126,
    "year": 2016
}
```

### Using bitmasks

The `month_date_mask` shows which dates in the month have valid
performances, and the `month_day_mask` shows which weekdays have performances. 
Note the bitmask is indexed from the right!
Bit 0 in the `month_day_mask` is Sunday (i.e. the above event does not
have any Sunday performances), and in the `month_date_mask` bit 0
represents the first day of the month, and so on (i.e. [day number] - 1 is
the bit number). If you print the above in
binary you get this:

    1110111111011111101111110110000

so this month has performances from the 5th excluding Sundays (7th, 14th, 21st, 28th).

The attributes are the same as described above, however `month_date_mask` replaces `month_dates` and `month_day_mask` replaces `month_weekdays`.



## List months for an event

> **Definition**

```
GET https://api.ticketswitch.com/cgi-bin/json_months.exe/{username}?user_passwd={password}&event_id={eventid}
```

This call returns a list of [month objects](#month-object) for a particular event. The list is paged to avoid large volumes of data being accidentally returned.

Typical use cases:

* Request a list of months to populate the available months in a calendar on your website, optionally including pricing and offer detail. An example can be seen on [From The Box Office](https://www.fromtheboxoffice.com/book/25DR-the-lion-king/)

> **Example request**

```shell
curl https://api.ticketswitch.com/cgi-bin/json_months.exe/demo \
    -d "user_passwd=demopass" \
    -d "event_id=6IF" \
    -d "req_cost_range" \
    -d "req_cost_range_best_value_offer" \
    -G
```

### Request

These parameters can be included to request additional data for each performance:

Parameter | Description
--------- | -----------
`req_avail_details` | Returns a list of ticket types and price bands that are available for this month [see detail](#avail-detail-object). This data is retrieved from a cache of previously-seen availability data. (Not required for most use cases.)
`req_cost_range` | Returns [cost ranges](#cost-range-object) for each event. On its own, this parameter will add min and max prices only, however by adding one of the following 5 parameters below you can receive additional data (multiple parameters are provided because there can be multiple definitions of the "best" offer). Most API users request cost ranges.
`req_cost_range_best_value_offer` | Returns the offer with the highest percentage saving. This is the most commonly used offer cost range.
`req_cost_range_max_saving_offer` | Returns the offer with the highest absolute saving.
`req_cost_range_min_cost_offer` | Returns the offer with the lowest cost.
`req_cost_range_top_price_offer` | Returns the offer with the highest cost. This is the least used offer cost range.
`req_cost_range_no_singles_data` | This returns another cost range object that excludes availability with only 1 consecutive seat available. The prices in this cost range will therefore be the same or higher than the outer cost range. It has the same structure as the main cost range (so if you want to see the "best value offer" in the no singles data, you need to add `req_cost_range_best_value_offer` and you will see this data in both cost ranges).


These parameters are used to control the output if more than one month is returned:

Parameter | Description
--------- | -----------
`page_len` | Length of a page, default 50
`page_no` | Page number, default 0, ignored if page_len is not present


> **Example response**

```json
{
  "results": {
    "events_by_id": { ... },
    "month": [
      {
        MONTH_OBJECT
      },
      {
        MONTH_OBJECT
      }
    ]
  }
}
```

### Response

Attribute | Description
--------- | -----------
`events_by_id` | A list of events related to the months returned. This is necessary in the case of meta events (events that are made up of multiple component events, for example a touring show) - in that case it is useful to know the details of the component events. (TODO couldn't this just be returned at the event stage?)
`month` | A list of [month objects](#month-object)