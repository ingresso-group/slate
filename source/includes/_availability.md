
The API properly
supports simple tickets, misc items and hotles, as well as promo codes
and the 'both' allocation mode for B2B customers who are allowed to select
where they wish their reservation to come from.








# Availability

TODO:

- Are send methods returned by availability? If so how?
- What other parameters am I missing?
- promo codes?
- "both" allocation mode (we have a few "both" entries in event_allocation_modes - should probably change these to pool_alloc)

This section describes:

- the [availability object](#availability-object)
- the API call to [retrieve availability for a performance](#retrieve-availability)


## Availability Object

> **Example response - best available only**

```json
{
  "availability": {
    "ticket_type": [
      {
        "price_band": [
          {
            "absolute_saving": 0,
            "discount_code": "",
            "discount_desc": "",
            "is_alloc": false,
            "is_offer": false,
            "non_offer_sale_seatprice": 35,
            "non_offer_sale_surcharge": 4,
            "number_available": 6,
            "percentage_saving": 0,
            "price_band_code": "A",
            "sale_seatprice": 35,
            "sale_surcharge": 4
          },
          {
            "absolute_saving": 0,
            "discount_code": "",
            "discount_desc": "",
            "is_alloc": false,
            "is_offer": false,
            "non_offer_sale_seatprice": 25,
            "non_offer_sale_surcharge": 4,
            "number_available": 6,
            "percentage_saving": 0,
            "price_band_code": "C",
            "sale_seatprice": 25,
            "sale_surcharge": 4
          }
        ],
        "ticket_type_code": "CIRCLE",
        "ticket_type_desc": "Upper circle"
      },
      {
        "price_band": [
          {
            "absolute_saving": 0,
            "discount_code": "",
            "discount_desc": "",
            "is_alloc": false,
            "is_offer": false,
            "non_offer_sale_seatprice": 21,
            "non_offer_sale_surcharge": 3,
            "number_available": 6,
            "percentage_saving": 0,
            "price_band_code": "A",
            "sale_seatprice": 21,
            "sale_surcharge": 3
          }
        ],
        "ticket_type_code": "STALLS",
        "ticket_type_desc": "Stalls"
      }
    ]
  },
  "backend_is_broken": false,
  "backend_is_down": false,
  "backend_throttle_failed": false,
  "can_leave_singles": true,
  "contiguous_seat_selection_only": true,
  "currency": {
    "currency_code": "gbp",
    "currency_factor": 100,
    "currency_number": 826,
    "currency_places": 2,
    "currency_post_symbol": "",
    "currency_pre_symbol": "£"
  },
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
}
```

`Availability` lists the tickets that are currently available for a performance. Availability is divided into a set of ticket types, and each ticket type is subdivided into price bands. 

TODO add detail on:

- Pricing components
- Seat selection vs best available
- Best available: example seats
- Caching
- Restricted view
- Despatch
- Net pricing / commission

The maximum number of tickets that are allowed to be booked for that particular price band is also indicated. Potentially the method may also return a set of example seats, indicating the kind of seats which may be available for that price band. Note that these are examples only and are not intended as an indication of the actual seats which will be sold. It is possible for the method to return actual seats, however, but this must be requested explicitly, and the backend system must support it.

Some events are restricted to certain combinations of tickets, the most common being tickets which may only be purchased in pairs. A list is returned containing the set of valid possibilities for the number of tickets that may be purchased. Only values from the list will be accepted for bookings. 

A list of possible methods of despatching the tickets to the customer is also provided. Depending on the particular product, a ticket may be held for collection, or posted to the customer (subject to a number of restrictions on timing and postal areas). Each method in the list has an associated cost which will be added to the overall ticket charge.

It should be noted that the availability provided by the system is in real time and represents what was available at the time the request was made. There is thus no guarantee that the tickets will still be available to purchase at some future time. Tickets are only guaranteed to be held for a customer after you have [reserved tickets](#reservation).


Attribute | Description
--------- | -----------
`` | xx

Attribute | Description
--------- | -----------
`backend_is_broken` | 
`backend_is_down` | 
`backend_throttle_failed` | 
`can_leave_singles` | In most cases this is `true`. When `false` the backend ticketing system does not allow us to leave single seats (which are difficult to sell). TODO add more detail to this - what does an api user do when this is false?
`contiguous_seat_selection_only` | If you have requested individual seats a value of `true` indicates that you can only select consecutive seats. `false` indicates that you can select seats without restriction *within a single price band*. If you would like to allow your customers to select seats across price bands and ticket types you need to add multiple orders to a basket, one order for each price band. However there are currently some restrictions enforced so if you want to do this you will need to contact us first api@ingresso.co.uk.
`currency` | The [currency](#currency-object) of the availability
`quantity_options` | The valid [quantity options](#quantity-options-object)




> **Example response - including seat listing**

```json
{
  "availability": {
    "ticket_type": [
      {
        "price_band": [
          {
            "absolute_saving": 0,
            "discount_code": "RED/RED/1",
            "discount_desc": "FULL PRICE",
            "free_seat_blocks": {
              "seat_block": [
                {
                  "block_length": 3,
                  "id_details": [
                    {
                      "col_id": "11",
                      "full_id": "G11",
                      "is_restricted_view": false,
                      "row_id": "G",
                      "seat_subdata": "7/11",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "12",
                      "full_id": "G12",
                      "is_restricted_view": false,
                      "row_id": "G",
                      "seat_subdata": "7/12",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "13",
                      "full_id": "G13",
                      "is_restricted_view": false,
                      "row_id": "G",
                      "seat_subdata": "7/13",
                      "seat_text_code": "",
                      "separator": ""
                    }
                  ]
                },
                {
                  "block_length": 4,
                  "id_details": [
                    {
                      "col_id": "2",
                      "full_id": "H2",
                      "is_restricted_view": false,
                      "row_id": "H",
                      "seat_subdata": "8/2",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "3",
                      "full_id": "H3",
                      "is_restricted_view": false,
                      "row_id": "H",
                      "seat_subdata": "8/3",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "4",
                      "full_id": "H4",
                      "is_restricted_view": false,
                      "row_id": "H",
                      "seat_subdata": "8/4",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "5",
                      "full_id": "H5",
                      "is_restricted_view": false,
                      "row_id": "H",
                      "seat_subdata": "8/5",
                      "seat_text_code": "",
                      "separator": ""
                    }
                  ]
                }
              ]
            },
            "is_alloc": false,
            "is_offer": false,
            "non_offer_sale_seatprice": 60,
            "non_offer_sale_surcharge": 0,
            "number_available": 10,
            "percentage_saving": 0,
            "price_band_code": "A",
            "sale_seatprice": 60,
            "sale_surcharge": 0
          }
        ],
        "ticket_type_code": "STD",
        "ticket_type_desc": "Standard"
      }
    ]
  },
  "backend_is_broken": false,
  "backend_is_down": false,
  "backend_throttle_failed": false,
  "can_leave_singles": true,
  "contiguous_seat_selection_only": false,
  "currency": {
    "currency_code": "gbp",
    "currency_factor": 100,
    "currency_number": 826,
    "currency_places": 2,
    "currency_post_symbol": "",
    "currency_pre_symbol": "£"
  },
  "quantity_options": {
    "valid_quantity_flags": [
      false,
      true,
      true,
      true,
      true,
      true,
      true,
      true,
      true,
      true,
      true
    ]
  }
}
```

Seat selection STUFF

Seat selection parameters

Attribute | Description
--------- | -----------
`zz` | zz








## Retrieve availability

This call is used to return detail for one or more performances by their ID. It returns a list of [performance objects](#performance-object).

This call will not be useful for common use cases. If you have a need to request availability for a subset of performances in a guaranteed fast response time then one way to achieve it is to use this call with the req_avail_details parameter - this will return the list of available price bands from Ingresso's cached data. Not that the cached data can be out of date or not present, and doesn't return seating data, so we do not recommend this in most cases.

> **Definition**

```
GET https://api.ticketswitch.com/cgi-bin/json_availability.exe/{username}
```

This call is used to return detail for one or more performances by their ID. It returns a list of [performance objects](#performance-object).

> **Example request**

```shell
curl https://api.ticketswitch.com/cgi-bin/json_availability.exe/demo
        -d "user_passwd=demopass" \
        -d "perf_id=3CVA-6A" \
        -d "&add_seat_blocks" \
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
GET https://api.ticketswitch.com/cgi-bin/json_performances.exe/{username}?user_passwd={password}&event_id={event_id}
```

This call returns a list of [performance objects](#performance-object) for a particular event. The list is paged to avoid large volumes
of data being accidentally returned.

Typical use cases:

* Request a list of performances to dynamically populate the calendar on your website
* Request performances on a regular basis (e.g. hourly) to add them to your system. Note that this is not necessary or recommended - performances is one of our faster calls and if you cache the list of performances in your own system pricing and offers can be out of date. This is a particular problem when an offer or price change goes live at a particular time - you then need to worry about refreshing your cache. Our own websites dynamically request performances when a user needs to select on a calendar.


> **Example request**

```shell
curl https://api.ticketswitch.com/cgi-bin/json_performances.exe/demo?
user_passwd=demopass&event_id=6IF
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