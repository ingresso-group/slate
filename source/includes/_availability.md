# Availability

> **Definition**

```
GET https://api.ticketswitch.com/f13/availability.v1?perf_id={performanceid}
```

TODO:

- Are send methods returned by availability? If so how?  "A list of possible methods of despatching the tickets to the customer is also provided. Depending on the particular product, a ticket may be held for collection, or posted to the customer (subject to a number of restrictions on timing and postal areas). Each method in the list has an associated cost which will be added to the overall ticket charge."
- What other parameters am I missing?
- Promo codes?
- Do we need to include anything on allocation modes now that we have price bands with /pool?


**`availability`** lists the tickets that are currently available for a performance.
In most cases the availability provided is in real time (although we do
sometimes [cache](#caching) availability) and represents the tickets available
at the time the request was made. There is therefore no guarantee that the
tickets will still be available to purchase at some future time. Tickets are
only guaranteed to be held after you have [reserved tickets](#reserve).

Availability is divided into a set of ticket types, and each ticket type is
subdivided into price bands. If you request individual seats for a seated event
then each price band includes seat blocks containing contiguous seats.

We have a single availability resource that will return best available seating
by default. This resource is described first, following by 
[optional parameters](#optional-parameters) that can be passed to request 
[individual seats](#individual-seats), [example seats](#example-seats), 
[commission](#commission), and the [discounts](#discounts).


## Availability

### Request

> **Example request**

```shell
curl https://api.ticketswitch.com/f13/availability.v1 \
    -u "demo:demopass" \
    -d "perf_id=6IF-B0I" \
    -G
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
availability, meta = client.get_availability('6IF-A8B')
```

Attribute | Description
--------- | -----------
`perf_id` | The identifier of the performance that you wish to request availability for.
`no_of_seats` | *Optional*. The number of seats the customer would like. If this is specified then availability will only be shown for price bands with at least that many contiguous seats available.


These parameters can be included to request additional data:

Parameter | Description
--------- | -----------
`add_discounts` | [discounts](#discounts)
`add_example_seats` | Include to retrieve [example seats](#example-seats). These can be displayed alongside the ticket options when presenting availability to customers. The inclusion of this parameter does not guarantee that example seats data will be returned - this also depends on (a) whether the event is seated and (b) whether the supplier system returns seats at availability time.
`add_seat_blocks` | Include to retrieve [individual seats](#individual-seats) (if, for example, you wish to offer seat selection to your customer). The inclusion of this parameter does not guarantee that individual seat data will be returned - this also depends on (a) whether the event is seated and (b) whether the supplier system supports seat selection.
`add_user_commission` | Include to retrieve [commission](#commission) data. For most partners this will include user_commission only (the amount you earn per ticket). Some partners will also see gross_commission, which is the total commission available to be shared between Ingresso and our partner. By default you will see user_commission only - if you think you need to see gross_commission as well then please get in touch. 

(TODO should be req_seat_blocks for consistency?)


### Response

> **Example response**

```shell
{
  "availability": {
    "ticket_type": [
      {
        "price_band": [
          {
            "absolute_saving": 0,
            "discount_code": "",
            "discount_desc": "",
            "discount_disallowed_seat_no_bitmask": 0,
            "is_offer": false,
            "non_offer_sale_seatprice": 35,
            "non_offer_sale_surcharge": 0,
            "number_available": 6,
            "percentage_saving": 0,
            "price_band_code": "A/pool",
            "sale_seatprice": 35,
            "sale_surcharge": 0
          },
          {
            "absolute_saving": 0,
            "discount_code": "",
            "discount_desc": "",
            "discount_disallowed_seat_no_bitmask": 0,
            "is_offer": false,
            "non_offer_sale_seatprice": 30,
            "non_offer_sale_surcharge": 0,
            "number_available": 6,
            "percentage_saving": 0,
            "price_band_code": "B/pool",
            "sale_seatprice": 30,
            "sale_surcharge": 0
          },
          {
            "absolute_saving": 0,
            "discount_code": "",
            "discount_desc": "",
            "discount_disallowed_seat_no_bitmask": 0,
            "is_offer": false,
            "non_offer_sale_seatprice": 25,
            "non_offer_sale_surcharge": 0,
            "number_available": 6,
            "percentage_saving": 0,
            "price_band_code": "C/pool",
            "sale_seatprice": 25,
            "sale_surcharge": 0
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
            "discount_disallowed_seat_no_bitmask": 0,
            "is_offer": false,
            "non_offer_sale_seatprice": 21,
            "non_offer_sale_surcharge": 0,
            "number_available": 6,
            "percentage_saving": 0,
            "price_band_code": "A/pool",
            "sale_seatprice": 21,
            "sale_surcharge": 0
          },
          {
            "absolute_saving": 0,
            "discount_code": "",
            "discount_desc": "",
            "discount_disallowed_seat_no_bitmask": 0,
            "is_offer": false,
            "non_offer_sale_seatprice": 18,
            "non_offer_sale_surcharge": 0,
            "number_available": 3,
            "percentage_saving": 0,
            "price_band_code": "B/pool",
            "sale_seatprice": 18,
            "sale_surcharge": 0
          }
        ],
        "ticket_type_code": "STALLS",
        "ticket_type_desc": "Stalls"
      },
      {
        "price_band": [
          {
            "absolute_saving": 0,
            "discount_code": "",
            "discount_desc": "",
            "discount_disallowed_seat_no_bitmask": 0,
            "is_offer": false,
            "non_offer_sale_seatprice": 47,
            "non_offer_sale_surcharge": 0,
            "number_available": 6,
            "percentage_saving": 0,
            "price_band_code": "A/pool",
            "sale_seatprice": 47,
            "sale_surcharge": 0
          }
        ],
        "ticket_type_code": "BALCONY",
        "ticket_type_desc": "Balcony"
      }
    ]
  },
  "backend_is_broken": false,
  "backend_is_down": false,
  "backend_throttle_failed": false,
  "can_leave_singles": true,
  "contiguous_seat_selection_only": true,
  "currency": {
    "currency_code": "gbp"
  },
  "quantity_options": {
    "valid_quantity_bitmask": 126
  }
}
```

`Availability` lists the tickets that are currently available for a performance.
In most cases the availability provided is in real time (although we do
sometimes [cache](#caching) availability) and represents the tickets available
at the time the request was made. There is therefore no guarantee that the
tickets will still be available to purchase at some future time. Tickets are
only guaranteed to be held after you have [reserved tickets](#reserve).

Availability is divided into a set of ticket types, and each ticket type is
subdivided into price bands. If you request individual seats for a seated event
each price band includes seat blocks for the contiguous seats. The structure of
the availability data returned is therefore:

<img src="https://d1wx4w35ubmdix.cloudfront.net/wl-media/images/availability-hierarchy.png" alt="Availability Hierarchy">

If you wish to retrieve individual seats and offer seat selection to your
customer then you should include `add_seat_blocks` in your request. If the event
is seated and the supplier system supports seat selection then we will return
seat blocks containing seats. You can check for the presence of the seat blocks
to determine whether you can offer seat selection to your customer. Note that
implementing seat selection can be quite involved because you need to integrate
a third-party seat renderer or implement your own - we are happy to discuss this
further with you and offer our advice.

<aside class="notice">Particularly in the UK market, offers are classified as 
either "discounted face value" or "no booking fee". Discounted face value 
offers have an offer seatprice that is lower than the full price seatprice (and
normally they have an offer surcharge of zero). No booking fee offers have
an offer seatprice equal to the full price seatprice, and an offer surcharge
equal to zero. The standard is to not display a saving percentage for no booking
fee offers (some suppliers do not like their offers to show as percentage 
discounts), and instead display "No fees" or similar.</aside>

This example response is for best available - following this there is an example response that includes seats.

Availability-level attributes:

Attribute | Description
--------- | -----------
`backend_is_broken` | If we see an unexpected error from the supplier system (for example a 500 error) we mark the system as "broken" for a period of time afterwards (the time can vary from nothing to 2 minutes). During this period of time this attribute will be `true` and we will return empty availability. This is an exceptional circumstance; to check if there is currently a supplier system issue you can check our [status page](https://status.ingresso.co.uk/).
`backend_is_down` | When `true` the supplier system cannot be contacted for some reason, for example they are having technical problems or scheduled maintenance. The response will include empty availability in this case. This is an exceptional circumstance; to check if there is currently a supplier system issue you can check our [status page](https://status.ingresso.co.uk/).
`backend_throttle_failed` | We allow a certain number of simultaneous requests to hit a supplier system and queue requests when the limit is reached. When this attribute is `true` your request has been sitting in our queue for a long time and we have timed out the request. This is an exceptional circumstance.
`can_leave_singles` | In most cases this is `true`. When `false` the supplier ticketing system does not allow us to leave single seats (which are difficult to sell). TODO add more detail to this - what should an api user do when this is false?
`contiguous_seat_selection_only` | If you have requested individual seats a value of `true` indicates that you can only select consecutive seats. `false` indicates that you can select seats without restriction *within a single ticket type and price band*. In most cases this will be `false`. If you would like to allow your customers to select seats without restriction across price bands and ticket types, you need to add multiple orders to a [trolley](#trolley), one order for each price band. However there are currently some restrictions enforced so if you want to do this you will need to contact us first api@ingresso.co.uk
`currency` | The [currency](#currency-object) of the availability.
`quantity_options` | The valid [quantity options](#quantity-options-object), a rare example is that some tickets can only be purchased in pairs.


Ticket type attributes:

Attribute | Description
--------- | -----------
`ticket_type_code` | The unique identifier for the ticket type. For seated events this refers to a part of house / seating area such as Grand Circle.
`ticket_type_desc` | The description for the ticket type. This should be displayed to the customer (if you are offering seat selection to your customer then you would typically hard-code the description when drawing a seating plan).


Price band attributes:

Attribute | Description
--------- | -----------
`absolute_saving` | Defined as (`non_offer_sale_seatprice` + `non_offer_sale_surcharge`) - (`sale_seatprice` + `sale_surcharge`)
`discount_code` | The unique identifier of the default [discount](#discount-object). Each price band has a default discount, but additional discounts can be requested for a price band with [list discounts](#list-discounts).
`discount_desc` | The description of the default discount. We recommend to present this text to customers when `is_offer` is `true` to describe the offer.
`is_offer` | `true` if the ticket price is discounted below the full price, i.e. if `absolute_saving` is greater than zero.
`non_offer_sale_seatprice` | The per-ticket price for full-priced tickets. This will be the face value price when the market has such a concept (for example the London theatre market has this concept, but some New York theatre shows do not). This is the same as the `sale_seatprice` when the price band is not discounted. (TODO can we remove "sale" from the name?)
`non_offer_sale_surcharge` | The per-ticket booking fee for full-priced tickets. To determine the total ticket price you must add together the `non_offer_sale_seatprice` and the `non_offer_sale_surcharge`.
`number_available` | This is the maximum number of contiguous seats that can be purchased. This applies to best available only - if you are using seat selection and `contiguous_seat_selection_only` is `false` it is possible to select above this number.
`percentage_saving` | Defined as `absolute_saving` / (`non_offer_sale_seatprice` + `non_offer_sale_surcharge`) * 100
`price_band_code` | The code for a price band. To uniquely identify a price band you should take the combination of `ticket_type_code` and `price_band_code`. The price band code is generally made up of the code from the underlying supplier system, e.g. "C", followed by a "/" separator then "pool" or "alloc", indicating whether the price band is taken from the general pool of tickets or is from a ring-fenced allocation. The combined price_band_code looks like "C/pool". Integrating partners should just work with the full price_band_code, ignoring the constituent parts.
`price_band_description??` | (TODO what is the actual name of this attribute?)
`sale_seatprice` | The per-ticket price. This will be the face value price when the market has such a concept (for example the London theatre market has this concept, but some New York theatre shows do not). This is the same as the `non_offer_sale_seatprice` when the price band is not discounted.
`sale_surcharge` | The per-ticket booking fee. To determine the total ticket price you must add together the `sale_seatprice` and the `sale_surcharge`.


## --- Optional Parameters ---

There are several additional parameters described below that can be provided to
return additional availability data.

These additional parameters require extra processing to retrieve the requested
data, which will slow down the resource, sometimes substantially. They should
therefore only be used where necessary.


## Individual Seats

The Ingresso API can return a list of all available seats, allowing you to give
your customers the choice rather than forcing them to take the best available
seats. 

<aside class="notice">To save development effort, it is possible to link to
Ingresso's booking page and we will automatically redirect back to your checkout
page once the customer has successfully reserved their seats. Alternatively you
can embed our seat selection widget within your site.</aside>

### Request

> **Example request**

```shell
curl https://api.ticketswitch.com/f13/availability.v1 \
    -u "demo:demopass" \
    -d "perf_id=3CVB-22" \
    -d "add_seat_blocks" \
    -G
```

Parameter | Description
--------- | -----------
`add_seat_blocks` | Include to retrieve individual seats (if, for example, you wish to offer seat selection to your customer). The inclusion of this parameter does not guarantee that individual seat data will be returned - this also depends on (a) whether the event is seated and (b) whether the supplier system supports seat selection.

### Response

> **Example response**

```shell
{
  "availability": {
    "ticket_type": [
      {
        "price_band": [
          {
            "absolute_saving": 0,
            "discount_code": "RED/RED/1",
            "discount_desc": "FULL PRICE",
            "discount_disallowed_seat_no_bitmask": 0,
            "free_seat_blocks": {
              "seat_block": [
                {
                  "block_length": 10,
                  "id_details": [
                    {
                      "col_id": "1",
                      "full_id": "D1",
                      "is_restricted_view": false,
                      "row_id": "D",
                      "seat_subdata": "1/1",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "2",
                      "full_id": "D2",
                      "is_restricted_view": false,
                      "row_id": "D",
                      "seat_subdata": "1/2",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "3",
                      "full_id": "D3",
                      "is_restricted_view": false,
                      "row_id": "D",
                      "seat_subdata": "1/3",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "4",
                      "full_id": "D4",
                      "is_restricted_view": false,
                      "row_id": "D",
                      "seat_subdata": "1/4",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "5",
                      "full_id": "D5",
                      "is_restricted_view": false,
                      "row_id": "D",
                      "seat_subdata": "1/5",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "6",
                      "full_id": "D6",
                      "is_restricted_view": false,
                      "row_id": "D",
                      "seat_subdata": "1/6",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "7",
                      "full_id": "D7",
                      "is_restricted_view": false,
                      "row_id": "D",
                      "seat_subdata": "1/7",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "8",
                      "full_id": "D8",
                      "is_restricted_view": false,
                      "row_id": "D",
                      "seat_subdata": "1/8",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "9",
                      "full_id": "D9",
                      "is_restricted_view": false,
                      "row_id": "D",
                      "seat_subdata": "1/9",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "10",
                      "full_id": "D10",
                      "is_restricted_view": false,
                      "row_id": "D",
                      "seat_subdata": "1/10",
                      "seat_text_code": "",
                      "separator": ""
                    }
                  ]
                }
              ]
            },
            "is_offer": false,
            "non_offer_sale_seatprice": 10,
            "non_offer_sale_surcharge": 0,
            "number_available": 10,
            "percentage_saving": 0,
            "price_band_code": "D/pool",
            "sale_seatprice": 10,
            "sale_surcharge": 0
          }
        ],
        "ticket_type_code": "BAL",
        "ticket_type_desc": "Balcony"
      },
      {
        "price_band": [
          {
            "absolute_saving": 0,
            "discount_code": "RED/RED/1",
            "discount_desc": "FULL PRICE",
            "discount_disallowed_seat_no_bitmask": 0,
            "free_seat_blocks": {
              "seat_block": [
                {
                  "block_length": 10,
                  "id_details": [
                    {
                      "col_id": "1",
                      "full_id": "B1",
                      "is_restricted_view": false,
                      "row_id": "B",
                      "seat_subdata": "1/1",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "2",
                      "full_id": "B2",
                      "is_restricted_view": false,
                      "row_id": "B",
                      "seat_subdata": "1/2",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "3",
                      "full_id": "B3",
                      "is_restricted_view": false,
                      "row_id": "B",
                      "seat_subdata": "1/3",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "4",
                      "full_id": "B4",
                      "is_restricted_view": false,
                      "row_id": "B",
                      "seat_subdata": "1/4",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "5",
                      "full_id": "B5",
                      "is_restricted_view": false,
                      "row_id": "B",
                      "seat_subdata": "1/5",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "6",
                      "full_id": "B6",
                      "is_restricted_view": false,
                      "row_id": "B",
                      "seat_subdata": "1/6",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "7",
                      "full_id": "B7",
                      "is_restricted_view": false,
                      "row_id": "B",
                      "seat_subdata": "1/7",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "8",
                      "full_id": "B8",
                      "is_restricted_view": false,
                      "row_id": "B",
                      "seat_subdata": "1/8",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "9",
                      "full_id": "B9",
                      "is_restricted_view": false,
                      "row_id": "B",
                      "seat_subdata": "1/9",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "10",
                      "full_id": "B10",
                      "is_restricted_view": false,
                      "row_id": "B",
                      "seat_subdata": "1/10",
                      "seat_text_code": "",
                      "separator": ""
                    }
                  ]
                }
              ]
            },
            "is_offer": false,
            "non_offer_sale_seatprice": 40,
            "non_offer_sale_surcharge": 0,
            "number_available": 10,
            "percentage_saving": 0,
            "price_band_code": "B/pool",
            "sale_seatprice": 40,
            "sale_surcharge": 0
          }
        ],
        "ticket_type_code": "DC",
        "ticket_type_desc": "Dress Circle"
      },
      {
        "price_band": [
          {
            "absolute_saving": 0,
            "discount_code": "RED/RED/1",
            "discount_desc": "FULL PRICE",
            "discount_disallowed_seat_no_bitmask": 0,
            "free_seat_blocks": {
              "seat_block": [
                {
                  "block_length": 10,
                  "id_details": [
                    {
                      "col_id": "1",
                      "full_id": "A1",
                      "is_restricted_view": false,
                      "row_id": "A",
                      "seat_subdata": "1/1",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "2",
                      "full_id": "A2",
                      "is_restricted_view": false,
                      "row_id": "A",
                      "seat_subdata": "1/2",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "3",
                      "full_id": "A3",
                      "is_restricted_view": false,
                      "row_id": "A",
                      "seat_subdata": "1/3",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "4",
                      "full_id": "A4",
                      "is_restricted_view": false,
                      "row_id": "A",
                      "seat_subdata": "1/4",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "5",
                      "full_id": "A5",
                      "is_restricted_view": false,
                      "row_id": "A",
                      "seat_subdata": "1/5",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "6",
                      "full_id": "A6",
                      "is_restricted_view": false,
                      "row_id": "A",
                      "seat_subdata": "1/6",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "7",
                      "full_id": "A7",
                      "is_restricted_view": false,
                      "row_id": "A",
                      "seat_subdata": "1/7",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "8",
                      "full_id": "A8",
                      "is_restricted_view": false,
                      "row_id": "A",
                      "seat_subdata": "1/8",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "9",
                      "full_id": "A9",
                      "is_restricted_view": false,
                      "row_id": "A",
                      "seat_subdata": "1/9",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "10",
                      "full_id": "A10",
                      "is_restricted_view": false,
                      "row_id": "A",
                      "seat_subdata": "1/10",
                      "seat_text_code": "",
                      "separator": ""
                    }
                  ]
                }
              ]
            },
            "is_offer": false,
            "non_offer_sale_seatprice": 60,
            "non_offer_sale_surcharge": 0,
            "number_available": 10,
            "percentage_saving": 0,
            "price_band_code": "A/pool",
            "sale_seatprice": 60,
            "sale_surcharge": 0
          }
        ],
        "ticket_type_code": "STA",
        "ticket_type_desc": "Stalls"
      },
      {
        "price_band": [
          {
            "absolute_saving": 0,
            "discount_code": "RED/RED/1",
            "discount_desc": "FULL PRICE",
            "discount_disallowed_seat_no_bitmask": 0,
            "free_seat_blocks": {
              "seat_block": [
                {
                  "block_length": 10,
                  "id_details": [
                    {
                      "col_id": "1",
                      "full_id": "C1",
                      "is_restricted_view": false,
                      "row_id": "C",
                      "seat_subdata": "1/1",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "2",
                      "full_id": "C2",
                      "is_restricted_view": false,
                      "row_id": "C",
                      "seat_subdata": "1/2",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "3",
                      "full_id": "C3",
                      "is_restricted_view": false,
                      "row_id": "C",
                      "seat_subdata": "1/3",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "4",
                      "full_id": "C4",
                      "is_restricted_view": false,
                      "row_id": "C",
                      "seat_subdata": "1/4",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "5",
                      "full_id": "C5",
                      "is_restricted_view": false,
                      "row_id": "C",
                      "seat_subdata": "1/5",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "6",
                      "full_id": "C6",
                      "is_restricted_view": false,
                      "row_id": "C",
                      "seat_subdata": "1/6",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "7",
                      "full_id": "C7",
                      "is_restricted_view": false,
                      "row_id": "C",
                      "seat_subdata": "1/7",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "8",
                      "full_id": "C8",
                      "is_restricted_view": false,
                      "row_id": "C",
                      "seat_subdata": "1/8",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "9",
                      "full_id": "C9",
                      "is_restricted_view": false,
                      "row_id": "C",
                      "seat_subdata": "1/9",
                      "seat_text_code": "",
                      "separator": ""
                    },
                    {
                      "col_id": "10",
                      "full_id": "C10",
                      "is_restricted_view": false,
                      "row_id": "C",
                      "seat_subdata": "1/10",
                      "seat_text_code": "",
                      "separator": ""
                    }
                  ]
                }
              ]
            },
            "is_offer": false,
            "non_offer_sale_seatprice": 20,
            "non_offer_sale_surcharge": 0,
            "number_available": 10,
            "percentage_saving": 0,
            "price_band_code": "C/pool",
            "sale_seatprice": 20,
            "sale_surcharge": 0
          }
        ],
        "ticket_type_code": "UPP",
        "ticket_type_desc": "Upper Circle"
      }
    ]
  },
  "backend_is_broken": false,
  "backend_is_down": false,
  "backend_throttle_failed": false,
  "can_leave_singles": true,
  "contiguous_seat_selection_only": false,
  "currency": {
    "currency_code": "gbp"
  },
  "quantity_options": {
    "valid_quantity_bitmask": 2046
  }
}
```

The response includes the following attributes in the `seat_block`:

Attribute | Description
--------- | -----------
`block_length` | The number of seats in the block. Seats are included in the same seat block when they are contiguous. 
`id_details` | An array of individual seats. The array order determines whether seats are contiguous, so when `contiguous_seat_selection_only` = `true` then you can only reserve seats that are in a continuous order in the array.
`id_details.col_id` | The column identifier, normally a number.
`id_details.full_id` | The unique identifier for the seat.
`id_details.is_restricted_view` | `true` if the seat is marked as having a restricted view.
`id_details.row_id` | The row identifier, normally a letter.
`id_details.seat_subdata` | (TODO: what is this useful for?)
`id_details.seat_text` | *(Optional)* A description of the seat that should be displayed to the customer. If the seat has a restricted view this text will normally be present to describe the restriction in more detail, but it should be displayed in all cases. 
`id_details.seat_text_code` | (TODO shouldn't this only appear when seat_text appears and not useful externally anyway?)
`id_details.separator` | (TODO when is this used, what is an example?)



## Example Seats

Example seats can be requested and displayed alongside the list of available 
ticket types and price bands. This is a simpler option to show seats to your 
customer alongside the list of price bands without needing to request 
[individual seats](#individual-seats) and do the work to display them on a 
seating plan or similar. If you plan to display individual seats then there is 
no need to also request example seats.

### Request

> **Example request**

```shell
curl https://api.ticketswitch.com/f13/availability.v1 \
    -u "demo:demopass" \
    -d "perf_id=3CVB-22" \
    -d "add_example_seats" \
    -G
```

Parameter | Description
--------- | -----------
`add_example_seats` | Include to retrieve [example seats](#example-seats). These can be displayed alongside the ticket options when presenting availability to customers. The inclusion of this parameter does not guarantee that example seats data will be returned - this also depends on (a) whether the event is seated and (b) whether the supplier system returns seats at availability time.

### Response

> **Example response**

```shell
{
  "availability": {
    "ticket_type": [
      {
        "price_band": [
          {
            "absolute_saving": 0,
            "discount_code": "RED/RED/1",
            "discount_desc": "FULL PRICE",
            "discount_disallowed_seat_no_bitmask": 0,
            "example_seats": {
              "id_details": [
                {
                  "col_id": "1",
                  "full_id": "D1",
                  "is_restricted_view": false,
                  "row_id": "D",
                  "seat_subdata": "1/1",
                  "seat_text_code": "",
                  "separator": ""
                },
                {
                  "col_id": "2",
                  "full_id": "D2",
                  "is_restricted_view": false,
                  "row_id": "D",
                  "seat_subdata": "1/2",
                  "seat_text_code": "",
                  "separator": ""
                }
              ]
            },
            "example_seats_are_real": true,
            "is_offer": false,
            "non_offer_sale_seatprice": 10,
            "non_offer_sale_surcharge": 0,
            "number_available": 10,
            "percentage_saving": 0,
            "price_band_code": "D/pool",
            "sale_seatprice": 10,
            "sale_surcharge": 0
          }
        ],
        "ticket_type_code": "BAL",
        "ticket_type_desc": "Balcony"
      },
      {
        "price_band": [
          {
            "absolute_saving": 0,
            "discount_code": "RED/RED/1",
            "discount_desc": "FULL PRICE",
            "discount_disallowed_seat_no_bitmask": 0,
            "example_seats": {
              "id_details": [
                {
                  "col_id": "1",
                  "full_id": "B1",
                  "is_restricted_view": false,
                  "row_id": "B",
                  "seat_subdata": "1/1",
                  "seat_text_code": "",
                  "separator": ""
                },
                {
                  "col_id": "2",
                  "full_id": "B2",
                  "is_restricted_view": false,
                  "row_id": "B",
                  "seat_subdata": "1/2",
                  "seat_text_code": "",
                  "separator": ""
                }
              ]
            },
            "example_seats_are_real": true,
            "is_offer": false,
            "non_offer_sale_seatprice": 40,
            "non_offer_sale_surcharge": 0,
            "number_available": 10,
            "percentage_saving": 0,
            "price_band_code": "B/pool",
            "sale_seatprice": 40,
            "sale_surcharge": 0
          }
        ],
        "ticket_type_code": "DC",
        "ticket_type_desc": "Dress Circle"
      },
      {
        "price_band": [
          {
            "absolute_saving": 0,
            "discount_code": "RED/RED/1",
            "discount_desc": "FULL PRICE",
            "discount_disallowed_seat_no_bitmask": 0,
            "example_seats": {
              "id_details": [
                {
                  "col_id": "1",
                  "full_id": "A1",
                  "is_restricted_view": false,
                  "row_id": "A",
                  "seat_subdata": "1/1",
                  "seat_text_code": "",
                  "separator": ""
                },
                {
                  "col_id": "2",
                  "full_id": "A2",
                  "is_restricted_view": false,
                  "row_id": "A",
                  "seat_subdata": "1/2",
                  "seat_text_code": "",
                  "separator": ""
                }
              ]
            },
            "example_seats_are_real": true,
            "is_offer": false,
            "non_offer_sale_seatprice": 60,
            "non_offer_sale_surcharge": 0,
            "number_available": 10,
            "percentage_saving": 0,
            "price_band_code": "A/pool",
            "sale_seatprice": 60,
            "sale_surcharge": 0
          }
        ],
        "ticket_type_code": "STA",
        "ticket_type_desc": "Stalls"
      },
      {
        "price_band": [
          {
            "absolute_saving": 0,
            "discount_code": "RED/RED/1",
            "discount_desc": "FULL PRICE",
            "discount_disallowed_seat_no_bitmask": 0,
            "example_seats": {
              "id_details": [
                {
                  "col_id": "1",
                  "full_id": "C1",
                  "is_restricted_view": false,
                  "row_id": "C",
                  "seat_subdata": "1/1",
                  "seat_text_code": "",
                  "separator": ""
                },
                {
                  "col_id": "2",
                  "full_id": "C2",
                  "is_restricted_view": false,
                  "row_id": "C",
                  "seat_subdata": "1/2",
                  "seat_text_code": "",
                  "separator": ""
                }
              ]
            },
            "example_seats_are_real": true,
            "is_offer": false,
            "non_offer_sale_seatprice": 20,
            "non_offer_sale_surcharge": 0,
            "number_available": 10,
            "percentage_saving": 0,
            "price_band_code": "C/pool",
            "sale_seatprice": 20,
            "sale_surcharge": 0
          }
        ],
        "ticket_type_code": "UPP",
        "ticket_type_desc": "Upper Circle"
      }
    ]
  },
  "backend_is_broken": false,
  "backend_is_down": false,
  "backend_throttle_failed": false,
  "can_leave_singles": true,
  "contiguous_seat_selection_only": false,
  "currency": {
    "currency_code": "gbp"
  },
  "quantity_options": {
    "valid_quantity_bitmask": 2046
  }
}
```

If the event supports example seats the response includes several attributes in
`example_seats`.

Attribute | Description
--------- | -----------
`example_seats_are_real` | If `true` the example seats are taken from a list of available seats, and they are the seats the customer is likely to receive when they reserve tickets. This is the most common scenario. If `false` the example seats are taken from previous reservations and are therefore less reliable.
`id_details` | The description of an individual seat.
`id_details.col_id` | The column identifier, normally a number.
`id_details.full_id` | The unique identifier for the seat.
`id_details.is_restricted_view` | `true` if the seat is marked as having a restricted view.
`id_details.row_id` | The row identifier, normally a letter.
`id_details.seat_subdata` | (TODO: what is this useful for?)
`id_details.seat_text` | *(Optional)* A description of the seat that should be displayed to the customer. If the seat has a restricted view this text will normally be present to describe the restriction in more detail, but it should be displayed in all cases. 
`id_details.seat_text_code` | (TODO shouldn't this only appear when seat_text appears and not useful externally anyway?)
`id_details.separator` | (TODO when is this used, what is an example?)


## Commission

Some partners wish to view the commission they will earn on each ticket up
front, before the sale is made. This could be to support agents that want to
know what they will earn, or to support custom repricing.

<aside class="notice">Ingresso has a pricing dashboard, so there is no need for you to develop your own repricing functionality.</aside>

Availability will return the per ticket commission you will earn. If you
subtract commission from the total ticket price (`sale_seatprice` +
`sale_surcharge`) you have the net price of the ticket.

### Request

> **Example request**

```shell
curl https://api.ticketswitch.com/f13/availability.v1 \
    -u "demo:demopass" \
    -d "perf_id=6IF-B0I" \
    -d "add_user_commission" \
    -G
```

Parameter | Description
--------- | -----------
`add_user_commission` | Include to retrieve commission data. For most partners this will include user_commission only (the amount you earn per ticket). Some partners will also see gross_commission, which is the total commission available to be shared between Ingresso and our partner. By default you will see user_commission only - if you think you need to see gross_commission as well then please get in touch.

(TODO does add_user_commission work with reserve?)

### Response

> **Example response**

```shell
{
  "availability": {
    "ticket_type": [
      {
        "price_band": [
          {
            "absolute_saving": 0,
            "discount_code": "",
            "discount_desc": "",
            "discount_disallowed_seat_no_bitmask": 0,
            "gross_commission": {
              "amount_excluding_vat": 4.38,
              "amount_including_vat": 5.25,
              "commission_currency": {
                "currency_code": "gbp"
              }
            },
            "is_offer": false,
            "non_offer_sale_seatprice": 35,
            "non_offer_sale_surcharge": 0,
            "number_available": 6,
            "percentage_saving": 0,
            "price_band_code": "A/pool",
            "sale_seatprice": 35,
            "sale_surcharge": 0,
            "user_commission": {
              "amount_excluding_vat": 1.97,
              "amount_including_vat": 2.36,
              "commission_currency": {
                "currency_code": "gbp"
              }
            }
          },
          {
            "absolute_saving": 0,
            "discount_code": "",
            "discount_desc": "",
            "discount_disallowed_seat_no_bitmask": 0,
            "gross_commission": {
              "amount_excluding_vat": 3.75,
              "amount_including_vat": 4.5,
              "commission_currency": {
                "currency_code": "gbp"
              }
            },
            "is_offer": false,
            "non_offer_sale_seatprice": 30,
            "non_offer_sale_surcharge": 0,
            "number_available": 6,
            "percentage_saving": 0,
            "price_band_code": "B/pool",
            "sale_seatprice": 30,
            "sale_surcharge": 0,
            "user_commission": {
              "amount_excluding_vat": 1.69,
              "amount_including_vat": 2.03,
              "commission_currency": {
                "currency_code": "gbp"
              }
            }
          },
          {
            "absolute_saving": 0,
            "discount_code": "",
            "discount_desc": "",
            "discount_disallowed_seat_no_bitmask": 0,
            "gross_commission": {
              "amount_excluding_vat": 3.13,
              "amount_including_vat": 3.75,
              "commission_currency": {
                "currency_code": "gbp"
              }
            },
            "is_offer": false,
            "non_offer_sale_seatprice": 25,
            "non_offer_sale_surcharge": 0,
            "number_available": 6,
            "percentage_saving": 0,
            "price_band_code": "C/pool",
            "sale_seatprice": 25,
            "sale_surcharge": 0,
            "user_commission": {
              "amount_excluding_vat": 1.41,
              "amount_including_vat": 1.69,
              "commission_currency": {
                "currency_code": "gbp"
              }
            }
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
            "discount_disallowed_seat_no_bitmask": 0,
            "gross_commission": {
              "amount_excluding_vat": 2.63,
              "amount_including_vat": 3.15,
              "commission_currency": {
                "currency_code": "gbp"
              }
            },
            "is_offer": false,
            "non_offer_sale_seatprice": 21,
            "non_offer_sale_surcharge": 0,
            "number_available": 6,
            "percentage_saving": 0,
            "price_band_code": "A/pool",
            "sale_seatprice": 21,
            "sale_surcharge": 0,
            "user_commission": {
              "amount_excluding_vat": 1.18,
              "amount_including_vat": 1.42,
              "commission_currency": {
                "currency_code": "gbp"
              }
            }
          },
          {
            "absolute_saving": 0,
            "discount_code": "",
            "discount_desc": "",
            "discount_disallowed_seat_no_bitmask": 0,
            "gross_commission": {
              "amount_excluding_vat": 2.25,
              "amount_including_vat": 2.7,
              "commission_currency": {
                "currency_code": "gbp"
              }
            },
            "is_offer": false,
            "non_offer_sale_seatprice": 18,
            "non_offer_sale_surcharge": 0,
            "number_available": 3,
            "percentage_saving": 0,
            "price_band_code": "B/pool",
            "sale_seatprice": 18,
            "sale_surcharge": 0,
            "user_commission": {
              "amount_excluding_vat": 1.01,
              "amount_including_vat": 1.22,
              "commission_currency": {
                "currency_code": "gbp"
              }
            }
          }
        ],
        "ticket_type_code": "STALLS",
        "ticket_type_desc": "Stalls"
      },
      {
        "price_band": [
          {
            "absolute_saving": 0,
            "discount_code": "",
            "discount_desc": "",
            "discount_disallowed_seat_no_bitmask": 0,
            "gross_commission": {
              "amount_excluding_vat": 5.88,
              "amount_including_vat": 7.05,
              "commission_currency": {
                "currency_code": "gbp"
              }
            },
            "is_offer": false,
            "non_offer_sale_seatprice": 47,
            "non_offer_sale_surcharge": 0,
            "number_available": 6,
            "percentage_saving": 0,
            "price_band_code": "A/pool",
            "sale_seatprice": 47,
            "sale_surcharge": 0,
            "user_commission": {
              "amount_excluding_vat": 2.64,
              "amount_including_vat": 3.17,
              "commission_currency": {
                "currency_code": "gbp"
              }
            }
          }
        ],
        "ticket_type_code": "BALCONY",
        "ticket_type_desc": "Balcony"
      }
    ]
  },
  "backend_is_broken": false,
  "backend_is_down": false,
  "backend_throttle_failed": false,
  "can_leave_singles": true,
  "contiguous_seat_selection_only": true,
  "currency": {
    "currency_code": "gbp"
  },
  "quantity_options": {
    "valid_quantity_bitmask": 126
  }
}
```


The response includes several attributes within the `user_commission` dictionary. Some partners will also see `gross_commission` which includes the same attributes.

Attribute | Description
--------- | -----------
`amount_excluding_vat` | The commission you will earn per ticket, excluding sales tax.
`amount_including_vat` | The commission you will earn per ticket, including sales tax.
`commission_currency` | The currency of the commission amount.


## Discounts

A `discount` represents a price type or concession that is available for a set
of available tickets.

### Request

> **Example request**

```shell
curl https://api.ticketswitch.com/f13/availability.v1 \
    -u "demo:demopass" \
    -d "perf_id=6IF-B0I" \
    -d "add_discounts" \
    -G
```

Parameter | Description
--------- | -----------
`add_discounts` | 


### Response

> **Example response - including seat listing**

```shell
{
  "availability": {
    "ticket_type": [
      {
        "price_band": [
          {
            "absolute_saving": 0,
            "discount_code": "",
            "discount_desc": "",
            "discount_disallowed_seat_no_bitmask": 0,
            "is_offer": false,
            "non_offer_sale_seatprice": 35,
            "non_offer_sale_surcharge": 0,
            "number_available": 6,
            "percentage_saving": 0,
            "possible_discounts": {
              "discount": [
                {
                  "absolute_saving": 0,
                  "discount_code": "ADULT",
                  "discount_desc": "Adult standard",
                  "discount_disallowed_seat_no_bitmask": 0,
                  "is_offer": false,
                  "non_offer_sale_seatprice": 35,
                  "non_offer_sale_surcharge": 0,
                  "number_available": 6,
                  "percentage_saving": 0,
                  "price_band_code": "A/pool",
                  "sale_seatprice": 35,
                  "sale_surcharge": 0
                },
                {
                  "absolute_saving": 0,
                  "discount_code": "CHILD",
                  "discount_desc": "Child rate",
                  "discount_disallowed_seat_no_bitmask": 0,
                  "is_offer": false,
                  "non_offer_sale_seatprice": 18,
                  "non_offer_sale_surcharge": 0,
                  "number_available": 6,
                  "percentage_saving": 0,
                  "price_band_code": "A/pool",
                  "sale_seatprice": 18,
                  "sale_surcharge": 0
                },
                {
                  "absolute_saving": 0,
                  "discount_code": "STUDENT",
                  "discount_desc": "Student rate",
                  "discount_disallowed_seat_no_bitmask": 0,
                  "is_offer": false,
                  "non_offer_sale_seatprice": 26,
                  "non_offer_sale_surcharge": 0,
                  "number_available": 6,
                  "percentage_saving": 0,
                  "price_band_code": "A/pool",
                  "sale_seatprice": 26,
                  "sale_surcharge": 0
                },
                {
                  "absolute_saving": 0,
                  "discount_code": "OAP",
                  "discount_desc": "Senior citizen rate",
                  "discount_disallowed_seat_no_bitmask": 0,
                  "is_offer": false,
                  "non_offer_sale_seatprice": 28,
                  "non_offer_sale_surcharge": 0,
                  "number_available": 6,
                  "percentage_saving": 0,
                  "price_band_code": "A/pool",
                  "sale_seatprice": 28,
                  "sale_surcharge": 0
                }
              ]
            },
            "price_band_code": "A/pool",
            "sale_seatprice": 35,
            "sale_surcharge": 0
          },
          {
            "absolute_saving": 0,
            "discount_code": "",
            "discount_desc": "",
            "discount_disallowed_seat_no_bitmask": 0,
            "is_offer": false,
            "non_offer_sale_seatprice": 30,
            "non_offer_sale_surcharge": 0,
            "number_available": 6,
            "percentage_saving": 0,
            "possible_discounts": {
              "discount": [
                {
                  "absolute_saving": 0,
                  "discount_code": "ADULT",
                  "discount_desc": "Adult standard",
                  "discount_disallowed_seat_no_bitmask": 0,
                  "is_offer": false,
                  "non_offer_sale_seatprice": 30,
                  "non_offer_sale_surcharge": 0,
                  "number_available": 6,
                  "percentage_saving": 0,
                  "price_band_code": "B/pool",
                  "sale_seatprice": 30,
                  "sale_surcharge": 0
                },
                {
                  "absolute_saving": 0,
                  "discount_code": "CHILD",
                  "discount_desc": "Child rate",
                  "discount_disallowed_seat_no_bitmask": 0,
                  "is_offer": false,
                  "non_offer_sale_seatprice": 15,
                  "non_offer_sale_surcharge": 0,
                  "number_available": 6,
                  "percentage_saving": 0,
                  "price_band_code": "B/pool",
                  "sale_seatprice": 15,
                  "sale_surcharge": 0
                }
              ]
            },
            "price_band_code": "B/pool",
            "sale_seatprice": 30,
            "sale_surcharge": 0
          },
          {
            "absolute_saving": 0,
            "discount_code": "",
            "discount_desc": "",
            "discount_disallowed_seat_no_bitmask": 0,
            "is_offer": false,
            "non_offer_sale_seatprice": 25,
            "non_offer_sale_surcharge": 0,
            "number_available": 6,
            "percentage_saving": 0,
            "possible_discounts": {
              "discount": [
                {
                  "absolute_saving": 0,
                  "discount_code": "ADULT",
                  "discount_desc": "Adult standard",
                  "discount_disallowed_seat_no_bitmask": 0,
                  "is_offer": false,
                  "non_offer_sale_seatprice": 25,
                  "non_offer_sale_surcharge": 0,
                  "number_available": 6,
                  "percentage_saving": 0,
                  "price_band_code": "C/pool",
                  "sale_seatprice": 25,
                  "sale_surcharge": 0
                },
                {
                  "absolute_saving": 0,
                  "discount_code": "CHILD",
                  "discount_desc": "Child rate",
                  "discount_disallowed_seat_no_bitmask": 0,
                  "is_offer": false,
                  "non_offer_sale_seatprice": 13,
                  "non_offer_sale_surcharge": 0,
                  "number_available": 6,
                  "percentage_saving": 0,
                  "price_band_code": "C/pool",
                  "sale_seatprice": 13,
                  "sale_surcharge": 0
                },
                {
                  "absolute_saving": 0,
                  "discount_code": "STUDENT",
                  "discount_desc": "Student rate",
                  "discount_disallowed_seat_no_bitmask": 0,
                  "is_offer": false,
                  "non_offer_sale_seatprice": 19,
                  "non_offer_sale_surcharge": 0,
                  "number_available": 6,
                  "percentage_saving": 0,
                  "price_band_code": "C/pool",
                  "sale_seatprice": 19,
                  "sale_surcharge": 0
                },
                {
                  "absolute_saving": 0,
                  "discount_code": "OAP",
                  "discount_desc": "Senior citizen rate",
                  "discount_disallowed_seat_no_bitmask": 0,
                  "is_offer": false,
                  "non_offer_sale_seatprice": 20,
                  "non_offer_sale_surcharge": 0,
                  "number_available": 6,
                  "percentage_saving": 0,
                  "price_band_code": "C/pool",
                  "sale_seatprice": 20,
                  "sale_surcharge": 0
                }
              ]
            },
            "price_band_code": "C/pool",
            "sale_seatprice": 25,
            "sale_surcharge": 0
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
            "discount_disallowed_seat_no_bitmask": 0,
            "is_offer": false,
            "non_offer_sale_seatprice": 21,
            "non_offer_sale_surcharge": 0,
            "number_available": 6,
            "percentage_saving": 0,
            "possible_discounts": {
              "discount": [
                {
                  "absolute_saving": 0,
                  "discount_code": "ADULT",
                  "discount_desc": "Adult standard",
                  "discount_disallowed_seat_no_bitmask": 0,
                  "is_offer": false,
                  "non_offer_sale_seatprice": 21,
                  "non_offer_sale_surcharge": 0,
                  "number_available": 6,
                  "percentage_saving": 0,
                  "price_band_code": "A/pool",
                  "sale_seatprice": 21,
                  "sale_surcharge": 0
                },
                {
                  "absolute_saving": 0,
                  "discount_code": "CHILD",
                  "discount_desc": "Child rate",
                  "discount_disallowed_seat_no_bitmask": 0,
                  "is_offer": false,
                  "non_offer_sale_seatprice": 11,
                  "non_offer_sale_surcharge": 0,
                  "number_available": 6,
                  "percentage_saving": 0,
                  "price_band_code": "A/pool",
                  "sale_seatprice": 11,
                  "sale_surcharge": 0
                },
                {
                  "absolute_saving": 0,
                  "discount_code": "STUDENT",
                  "discount_desc": "Student rate",
                  "discount_disallowed_seat_no_bitmask": 0,
                  "is_offer": false,
                  "non_offer_sale_seatprice": 16,
                  "non_offer_sale_surcharge": 0,
                  "number_available": 6,
                  "percentage_saving": 0,
                  "price_band_code": "A/pool",
                  "sale_seatprice": 16,
                  "sale_surcharge": 0
                },
                {
                  "absolute_saving": 0,
                  "discount_code": "OAP",
                  "discount_desc": "Senior citizen rate",
                  "discount_disallowed_seat_no_bitmask": 0,
                  "is_offer": false,
                  "non_offer_sale_seatprice": 17,
                  "non_offer_sale_surcharge": 0,
                  "number_available": 6,
                  "percentage_saving": 0,
                  "price_band_code": "A/pool",
                  "sale_seatprice": 17,
                  "sale_surcharge": 0
                }
              ]
            },
            "price_band_code": "A/pool",
            "sale_seatprice": 21,
            "sale_surcharge": 0
          },
          {
            "absolute_saving": 0,
            "discount_code": "",
            "discount_desc": "",
            "discount_disallowed_seat_no_bitmask": 0,
            "is_offer": false,
            "non_offer_sale_seatprice": 18,
            "non_offer_sale_surcharge": 0,
            "number_available": 3,
            "percentage_saving": 0,
            "possible_discounts": {
              "discount": [
                {
                  "absolute_saving": 0,
                  "discount_code": "ADULT",
                  "discount_desc": "Adult standard",
                  "discount_disallowed_seat_no_bitmask": 0,
                  "is_offer": false,
                  "non_offer_sale_seatprice": 18,
                  "non_offer_sale_surcharge": 0,
                  "number_available": 3,
                  "percentage_saving": 0,
                  "price_band_code": "B/pool",
                  "sale_seatprice": 18,
                  "sale_surcharge": 0
                },
                {
                  "absolute_saving": 0,
                  "discount_code": "CHILD",
                  "discount_desc": "Child rate",
                  "discount_disallowed_seat_no_bitmask": 0,
                  "is_offer": false,
                  "non_offer_sale_seatprice": 9,
                  "non_offer_sale_surcharge": 0,
                  "number_available": 3,
                  "percentage_saving": 0,
                  "price_band_code": "B/pool",
                  "sale_seatprice": 9,
                  "sale_surcharge": 0
                }
              ]
            },
            "price_band_code": "B/pool",
            "sale_seatprice": 18,
            "sale_surcharge": 0
          }
        ],
        "ticket_type_code": "STALLS",
        "ticket_type_desc": "Stalls"
      },
      {
        "price_band": [
          {
            "absolute_saving": 0,
            "discount_code": "",
            "discount_desc": "",
            "discount_disallowed_seat_no_bitmask": 0,
            "is_offer": false,
            "non_offer_sale_seatprice": 47,
            "non_offer_sale_surcharge": 0,
            "number_available": 6,
            "percentage_saving": 0,
            "possible_discounts": {
              "discount": [
                {
                  "absolute_saving": 0,
                  "discount_code": "ADULT",
                  "discount_desc": "Adult standard",
                  "discount_disallowed_seat_no_bitmask": 0,
                  "is_offer": false,
                  "non_offer_sale_seatprice": 47,
                  "non_offer_sale_surcharge": 0,
                  "number_available": 6,
                  "percentage_saving": 0,
                  "price_band_code": "A/pool",
                  "sale_seatprice": 47,
                  "sale_surcharge": 0
                },
                {
                  "absolute_saving": 0,
                  "discount_code": "CHILD",
                  "discount_desc": "Child rate",
                  "discount_disallowed_seat_no_bitmask": 0,
                  "is_offer": false,
                  "non_offer_sale_seatprice": 24,
                  "non_offer_sale_surcharge": 0,
                  "number_available": 6,
                  "percentage_saving": 0,
                  "price_band_code": "A/pool",
                  "sale_seatprice": 24,
                  "sale_surcharge": 0
                },
                {
                  "absolute_saving": 0,
                  "discount_code": "STUDENT",
                  "discount_desc": "Student rate",
                  "discount_disallowed_seat_no_bitmask": 0,
                  "is_offer": false,
                  "non_offer_sale_seatprice": 35,
                  "non_offer_sale_surcharge": 0,
                  "number_available": 6,
                  "percentage_saving": 0,
                  "price_band_code": "A/pool",
                  "sale_seatprice": 35,
                  "sale_surcharge": 0
                },
                {
                  "absolute_saving": 0,
                  "discount_code": "OAP",
                  "discount_desc": "Senior citizen rate",
                  "discount_disallowed_seat_no_bitmask": 0,
                  "is_offer": false,
                  "non_offer_sale_seatprice": 38,
                  "non_offer_sale_surcharge": 0,
                  "number_available": 6,
                  "percentage_saving": 0,
                  "price_band_code": "A/pool",
                  "sale_seatprice": 38,
                  "sale_surcharge": 0
                }
              ]
            },
            "price_band_code": "A/pool",
            "sale_seatprice": 47,
            "sale_surcharge": 0
          }
        ],
        "ticket_type_code": "BALCONY",
        "ticket_type_desc": "Balcony"
      }
    ]
  },
  "backend_is_broken": false,
  "backend_is_down": false,
  "backend_throttle_failed": false,
  "can_leave_singles": true,
  "contiguous_seat_selection_only": true,
  "currency": {
    "currency_code": "gbp"
  },
  "quantity_options": {
    "valid_quantity_bitmask": 126
  }
}
```

The response includes a `possible_discounts` dictionary, that contains a list of
discounts (or concessions) that are available on each price band. Each discount
contains the same price band attributes that are returned in
[availability](#availability).