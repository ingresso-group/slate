# Generic objects

There are some objects that are returned as part of several calls, for example cost ranges can be retrieved when you [list events](#list-all-events-or-search-for-events) or when you [list performances](#list-performances). These objects are consistent so that you can use a single function to parse them regardless of which API call has returned them.

## Avail Detail object

> **Example Response**

```json
"avail_details": {
  "ticket_type": [
    {
      "price_band": [
        {
          "avail_detail": [
            {
              "avail_currency": {
                "currency_code": "gbp",
                "currency_factor": 100,
                "currency_number": 826,
                "currency_places": 2,
                "currency_post_symbol": "",
                "currency_pre_symbol": "£"
              },
              "available_dates": {
                "first_yyyymmdd": "20160817",
                "last_yyyymmdd": "20161214",
                "year_2016": {
                  "aug": 2079784960,
                  "dec": 15867,
                  "nov": 1039923183,
                  "oct": 1876934526,
                  "sep": 1065287163
                }
              },
              "day_mask": 63,
              "quantity_options": {
                "valid_quantity_mask": "126"
              },
              "seatprice": 35,
              "surcharge": 4
            }
          ],
          "price_band_code": "A",
          "price_band_desc": ""
        },
        {
          "avail_detail": [
            AVAIL_DETAIL_OBJECT
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
            AVAIL_DETAIL_OBJECT
          ],
          "price_band_code": "A",
          "price_band_desc": ""
        },
        {
          "avail_detail": [
            AVAIL_DETAIL_OBJECT
          ],
          "price_band_code": "B",
          "price_band_desc": ""
        }
      ],
      "ticket_type_code": "STALLS",
      "ticket_type_desc": "Stalls"
    }
  ]
}
```



Avail details are a cached list of the price bands available for this event across all performances. They are cached from previous availability requests made via your username. This data can be useful for example if you wish to display a separate page with pricing detail for all price bands on sale.

Avail details are generated from availability requests made either by end-users or by scheduled processes that 
Ingresso use to update this data. You should not attempt to make multiple availability
requests in order to keep this data up to date - please contact us instead to discuss options api@ingresso.co.uk.

Avail details are only ever returned as part of a parent object (such as [event](#event-object)).


Attribute | Description
--------- | -----------
`avail_currency` | The price [currency](#currency-object) 
`available_dates` | `first_yyyymmdd` and `last_yyyymmdd` for the range. If the req_avail_details_with_perfs parameter was included then a nested list of available dates is also added (year -> month -> day)
`day_mask` | which days of the week where have we seen this price band availability
`quantity_options` | the available quantities we have seen for this price band
`seatprice` | the per-ticket face value
`surcharge` | the per-ticket booking fee



## Cost Range object

> **Example Response**

```json
{
  "best_value_offer": {
    "absolute_saving": 44.2,
    "full_seatprice": 72.5,
    "full_surcharge": 14.2,
    "offer_seatprice": 42.5,
    "offer_surcharge": 0,
    "percentage_saving": 51
  },
  "max_saving_offer": {
    "absolute_saving": 49.8,
    "full_seatprice": 85,
    "full_surcharge": 16.8,
    "offer_seatprice": 52,
    "offer_surcharge": 0,
    "percentage_saving": 49
  },
  "max_seatprice": 85.5,
  "max_surcharge": 16.8,
  "min_cost_offer": {
    "absolute_saving": 19.8,
    "full_seatprice": 37.5,
    "full_surcharge": 7.3,
    "offer_seatprice": 25,
    "offer_surcharge": 0,
    "percentage_saving": 44
  },
  "min_seatprice": 25,
  "min_surcharge": 0,
  "no_singles_cost_range": {COST_RANGE_OBJECT},
  "quantity_options": {
    "valid_quantity_mask": "2046"
  },
  "range_currency": {
    "currency_code": "gbp",
    "currency_factor": 100,
    "currency_number": 826,
    "currency_places": 2,
    "currency_post_symbol": "",
    "currency_pre_symbol": "£"
  },
  "top_price_offer": {
    "absolute_saving": 47.3,
    "full_seatprice": 85.5,
    "full_surcharge": 16.8,
    "offer_seatprice": 55,
    "offer_surcharge": 0,
    "percentage_saving": 46
  }
}
```

Cost ranges are a cached summary of the pricing that has been seen for your username. They are primarily used to retrieve the minimum (or "from") price for the event, along with detail of any offers or discounts.

Cost ranges are generated from availability requests made either by end-users or by scheduled processes that 
Ingresso use to update cost range data. You should not attempt to make multiple availability
requests in order to keep this data up to date - please contact us instead to discuss options api@ingresso.co.uk.
Cost ranges are only ever returned as part of a parent object (such as event). 

<aside class="warning">
Cost ranges (and avail details) are not guaranteed to be present so you should design your application with 
this in mind, for example by still displaying a performance even if it is missing a from price.
</aside>


Attribute | Description
--------- | -----------
`max_seatprice` | the face value for the highest price (seatprice + surcharge)
`max_surcharge` | the booking fee for the highest price (seatprice + surcharge)
`max_seatprice` | the face value for the lowest price (seatprice + surcharge)
`max_surcharge` | the booking fee for the lowest price (seatprice + surcharge)
`no_singles_cost_range` | this returns another cost range object that excludes availability with only 1 consecutive seat available. The prices in this cost range will therefore be the same or higher than the outer cost range.
`quantity_options` | the quantities with availability
`range_currency` | the [currency](#currency-object) for the cost range

TODO: ensure I have explaining the difference between best_value_offer etc somewhere.

## Cost Range Detail object

> **Example Response**

```json
"cost_range_details": {
  "ticket_type": [
    {
      "price_band": [
        {
          "cost_range": {
            COST_RANGE_OBJECT
          },
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
          "cost_range": {
            COST_RANGE_OBJECT
          },
          "price_band_code": "A",
          "price_band_desc": ""
        },
        {
          "cost_range": {
            COST_RANGE_OBJECT
          },
          "price_band_code": "B",
          "price_band_desc": ""
        }
      ],
      "ticket_type_code": "CIRCLE",
      "ticket_type_desc": "Upper circle"
    }
  ]
}
```

Cost range details presents a cost range for every available price band. A list of ticket types are returned; nested within each ticket type are the available price bands; nested within each price band is a [cost range](#cost-range-object). Cost range details are only ever returned as part of the parent event object.



## Currency object

> **Example Response**

```json
{
  "currency_code": "gbp",
  "currency_factor": 100,
  "currency_number": 826,
  "currency_places": 2,
  "currency_post_symbol": "",
  "currency_pre_symbol": "£"
}
```

Attribute | Description
--------- | -----------
`currency_code` | [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217) three letter code
`currency_factor` | Multiply by this number to get values in the base unit (e.g. multiplying $47.11 by the currency_factor will give 4711 cents)
`currency_number` | [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217) three letter numeric identifier
`currency_places` | The number of decimal places to display (eg 45.5 usd should be displayed as 45.50)
`currency_post_symbol` | A symbol to display at the end of the price. Not always present.
`currency_pre_symbol` | A symbol to display in front of the price. Not always present.


## Date/Time object

> **Example Response**

```json
{
  "date_desc": "Fri, 12th August 2016",
  "iso8601_date_and_time": "2016-08-12T19:30:00+01:00",
  "time_desc": "10.00 AM"
}
```

The ISO 8601 value is always output, as iso8601_date_and_time. 
Since sometimes we represent both a date and
a time, and sometimes only a time, the descriptive text for the
date is split into `date_desc` and `time_desc`. If there is no
`time_desc` then only the date is relevent, despite the fact that the
ISO 8601 variable will always have an associated time. The timezones
should be treated as correct when parsing these objects. 

Attribute | Description
--------- | -----------
`date_desc` | Date description
`iso8601_date_and_time` | ISO 8601 date and time
`time_desc` | Time description


## Quantity Options object

> **Example Response**

```json
"quantity_options": {
  "valid_quantity_flags": [
    false,
    true,
    true,
    true,
    true,
    true,
    true
  ]
}
```

This represents the valid quantities that can be purchased for this event with a zero-based array. The example response indicates that you cannot purchase 0 tickets, but you can purchase 1, 2, 3, 4, 5 or 6 tickets.

TODO: consecutive??

TODO: someone needs to check this as I'm guessing