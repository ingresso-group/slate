# Availability

TODO:

- Are send methods returned by availability? If so how?  "A list of possible methods of despatching the tickets to the customer is also provided. Depending on the particular product, a ticket may be held for collection, or posted to the customer (subject to a number of restrictions on timing and postal areas). Each method in the list has an associated cost which will be added to the overall ticket charge."
- What other parameters am I missing?
- How are example seats handled? Do you need to request them?
- Promo codes?
- "both" allocation mode (we have a few "both" entries in event_allocation_modes - should probably change these to pool_alloc)


This section describes:

- The [availability object](#availability-object)
- The API call to [retrieve availability for a performance](#retrieve-availability)

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

`Availability` lists the tickets that are currently available for a performance. In most cases the availability provided is in real time (although we do sometimes [cache](#caching) availability) and represents the tickets available at the time the request was made. There is therefore no guarantee that the tickets will still be available to purchase at some future time. Tickets are only guaranteed to be held after you have [reserved tickets](#reservation).

Availability is divided into a set of ticket types, and each ticket type is subdivided into price bands. If you request individual seats for a seated event each price band includes seat blocks for the contiguous seats. The structure of the availability data returned is therefore:

--> availability

-----> ticket types

--------> price bands

-----------> seat blocks

--------------> seats

If you wish to retrieve individual seats and offer seat selection to your customer then you should include `add_seat_blocks` in your request. If the event is seated and the backend system supports seat selection then we will return seat blocks containing seats. You can check for the presence of the seat blocks to determine whether you can offer seat selection to your customer. Note that implementing seat selection can be quite involved because you need to integrate a third-party seat renderer or implement your own - we are happy to discuss this further with you and offer our advice. 

This example response is for best available - following this there is an example response that includes seats.

Availability-level attributes:

Attribute | Description
--------- | -----------
`backend_is_broken` | If we see an unexpected error from the backend system (for example a 500 error) we mark the system as "broken" for a period of time afterwards (the time can vary from nothing to 2 minutes). During this period of time this attribute will be `true` and we will return empty availability. This is an exceptional circumstance; to check if there is currently a backend system issue you can check our [status page](https://status.ingresso.co.uk/).
`backend_is_down` | When `true` the backend system cannot be contacted for some reason, for example they are having technical problems or scheduled maintenance. The response will include empty availability in this case. This is an exceptional circumstance; to check if there is currently a backend system issue you can check our [status page](https://status.ingresso.co.uk/).
`backend_throttle_failed` | We allow a certain number of simultaneous requests to hit a backend system and queue requests when the limit is reached. When this attribute is `true` your request has been sitting in our queue for a long time and we have timed out the request. This is an exceptional circumstance.
`can_leave_singles` | In most cases this is `true`. When `false` the backend ticketing system does not allow us to leave single seats (which are difficult to sell). TODO add more detail to this - what should an api user do when this is false?
`contiguous_seat_selection_only` | If you have requested individual seats a value of `true` indicates that you can only select consecutive seats. `false` indicates that you can select seats without restriction *within a single price band*. If you would like to allow your customers to select seats without restriction across price bands and ticket types, you need to need to add multiple orders to a basket, one order for each price band. However there are currently some restrictions enforced so if you want to do this you will need to contact us first api@ingresso.co.uk.
`currency` | The [currency](#currency-object) of the availability
`quantity_options` | The valid [quantity options](#quantity-options-object), a rare example is that some tickets can only be purchased in pairs.


Ticket type attributes:

Attribute | Description
--------- | -----------
`ticket_type_code` | The unique identifier for the ticket type. For seated events this refers to a part of house / seating area such as Grand Circle.
`ticket_type_desc` | The description for the ticket type. This should be displayed to the customer (if you are offering seat selection to your customer then you would typically hard code the description when drawing a seating plan).


Price band attributes:

Attribute | Description
--------- | -----------
`absolute_saving` | Defined as (`non_offer_sale_seatprice` + `non_offer_sale_surcharge`) - (`sale_seatprice` + `sale_surcharge`)
`discount_code` | The unique identifier of the default [discount](#discount-object). Each price band has a default discount, but additional discounts can be requested for a price band with [list discounts](#list-discounts).
`discount_desc` | The description of the default discount. We recommend to present this text to customers when `is_offer` is `true` to describe the offer.
`is_alloc` | `true` means the availability is from an allocation; `false` means the availability is from the general pool. This is normally `false` and can be ignored for most use cases.
`is_offer` | `true` if the ticket price is discounted below the full price, i.e. if `absolute_saving` is greater than zero.
`non_offer_sale_seatprice` | The per-ticket price for full-priced tickets. This will be the face value price when the market has such a concept (for example the London theatre market has this concept, but some New York theatre shows do not). This is the same as the `sale_seatprice` when the price band is not discounted. (TODO can we remove "sale" from the name?)
`non_offer_sale_surcharge` | The per-ticket booking fee for full-priced tickets. To determine the total ticket price you must add together the `non_offer_sale_seatprice` and the `non_offer_sale_surcharge`.
`number_available` | This is the maximum number of contiguous seats that can be purchased. This applies to best available only - if you are using seat selection and `contiguous_seat_selection_only` is `false` it is possible to select above this number.
`percentage_saving` | Defined as `absolute_saving` / (`non_offer_sale_seatprice` + `non_offer_sale_surcharge`) * 100
`price_band_code` | The code for a price band. To uniquely identify a price band you should take the combination of `ticket_type_code`, `price_band_code` and `is_alloc` (`is_alloc` will default in most cases if you do not specify it).
`price_band_description??` | (TODO what is the actual name of this attribute?)
`sale_seatprice` | The per-ticket price. This will be the face value price when the market has such a concept (for example the London theatre market has this concept, but some New York theatre shows do not). This is the same as the `non_offer_sale_seatprice` when the price band is not discounted.
`sale_surcharge` | The per-ticket booking fee. To determine the total ticket price you must add together the `sale_seatprice` and the `sale_surcharge`.


> **Example response - including seat listing**

```json
{
  "availability": {
    "ticket_type": [
      {
        "price_band": [
          {
            "absolute_saving": 0,
            "discount_code": "TSW/ABF/1",
            "discount_desc": "Regular Price",
            "free_seat_blocks": {
              "seat_block": [
                {
                  "block_length": 2,
                  "id_details": [
                    {
                      "col_id": "1",
                      "full_id": "A1",
                      "is_restricted_view": false,
                      "row_id": "A",
                      "seat_subdata": "84/1",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "2",
                      "full_id": "A2",
                      "is_restricted_view": false,
                      "row_id": "A",
                      "seat_subdata": "84/2",
                      "seat_text_code": "",
                      "separator": ""
                    }
                  ]
                },
                {
                  "block_length": 3,
                  "id_details": [
                    {
                      "col_id": "7",
                      "full_id": "H7",
                      "is_restricted_view": true,
                      "row_id": "H",
                      "seat_subdata": "91/1",
                      "seat_text": "Restricted view, may miss moments on raised section of set",
                      "seat_text_code": "RVI",
                      "separator": ""
                    },
                    {
                      "col_id": "8",
                      "full_id": "H8",
                      "is_restricted_view": true,
                      "row_id": "H",
                      "seat_subdata": "91/2",
                      "seat_text": "Restricted view, may miss moments on raised section of set",
                      "seat_text_code": "RVI",
                      "separator": ""
                    },
                    {
                      "col_id": "9",
                      "full_id": "H9",
                      "is_restricted_view": true,
                      "row_id": "H",
                      "seat_subdata": "91/3",
                      "seat_text": "Restricted view, may miss moments on raised section of set",
                      "seat_text_code": "RVI",
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


Seat block attributes:

Attribute | Description
--------- | -----------
`block_length` | The number of seats in the block. Seats are included in the same seat block when they are contiguous. 
`id_details` | The description of an individual seat.
`col_id` | The column identifier, normally a number.
`full_id` | The unique identifier for the seat.
`is_restricted_view` | `true` if the seat is marked as having a restricted view.
`row_id` | The row identifier, normally a letter.
`seat_subdata` | (TODO: what is this useful for?)
`seat_text` | *(Optional)* A description of the seat that should be displayed to the customer. If the seat has a restricted view this text will normally be present to describe the restriction in more detail, but it should be displayed in all cases. 
`seat_text_code` | (TODO shouldn't this only appear when seat_text appears?)
`separator` | (TODO when is this used, what is an example?)





## Retrieve availability


> **Definition**

```
GET https://api.ticketswitch.com/cgi-bin/json_availability.exe/{username}
```

This call is used to return availability for a performance. It returns a list of [availability objects](#availability-object).

> **Example request**

```shell
curl https://api.ticketswitch.com/cgi-bin/json_availability.exe/demo
        -d "user_passwd=demopass" \
        -d "perf_id=3CVA-6A" \
        -d "add_seat_blocks" \
        -G
```

(TODO should be req_seat_blocks for consistency?)

### Request

Parameter | Description
--------- | -----------
`perf_id` | The performance identifier that you want to display availability for.

These parameters can be included to request additional data for each performance:

Parameter | Description
--------- | -----------
`add_seat_blocks` | Include to retrieve individual seats (if, for example, you wish to offer seat selection to your customer). The inclusion of this parameter does not guarantee that individual seat data will be returned - this also depends on (a) whether the event is seated and (b) whether the backend supports seat selection.


### Response

Returns an [availability object](#availability-object).