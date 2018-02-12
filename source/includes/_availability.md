# Availability

> **Definition**

```
GET https://demo.ticketswitch.com/f13/availability.v1?perf_id={performanceid}
```

**`availability`** lists the tickets that are currently available for a performance.
In most cases the availability provided is in real time (although we do
sometimes [cache](#caching) availability) and represents the tickets available
at the time the request was made. There is therefore no guarantee that the
tickets will still be available to purchase at some future time. Tickets are
only guaranteed to be held after you [reserve tickets](#reserve).

We have a single availability resource that will return best available seating
by default. This resource is described first and is important to read. We then describe
[optional parameters](#optional-parameters) that can be passed to request 
[individual seats](#individual-seats), [example seats](#example-seats), 
[commission](#commission), and [discounts](#add-discounts).

You should only send availability requests as a direct result of customers requesting 
availability. You should not automatically request availability to populate caches. 
[Performances with availability detail](#availability-detail) is provided for this type 
of use case. If this doesn't fit what you are trying to do contact us api@ingresso.co.uk.

<aside class="notice">We may disable API accounts making excessive availability calls.</aside>


## Availability

### Request

> **Example request**

```shell
curl https://demo.ticketswitch.com/f13/availability.v1 \
    -u "demo:demopass" \
    -d "perf_id=6IF-B5P" \
    --compressed \
    -G
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
ticket_types, meta = client.get_availability('6IF-B5P')
```

Attribute | Description
--------- | -----------
`perf_id` | The identifier of the performance that you wish to request availability for.
`no_of_seats` | *Optional*. The number of seats the customer would like. If this is specified then availability will only be shown for price bands with at least that many contiguous seats available.
`promo_code` | *Optional*. If the supplier supports a promo code this can be specified to unlock discounted pricing. This feature is not commonly used with partners.


These parameters can be included to request additional data:

Parameter | Description
--------- | -----------
`add_discounts` | [discounts](#discounts)
`add_example_seats` | Include to retrieve [example seats](#example-seats). These can be displayed alongside the ticket options when presenting availability to customers. The inclusion of this parameter does not guarantee that example seats data will be returned - this also depends on (a) whether the event is seated and (b) whether the supplier system returns seats at availability time.
`add_seat_blocks` | Include to retrieve [individual seats](#individual-seats) (if, for example, you wish to offer seat selection to your customer). The inclusion of this parameter does not guarantee that individual seat data will be returned - this also depends on (a) whether the event is seated and (b) whether the supplier system supports seat selection.
`req_predicted_commission` | Include to retrieve [commission](#commission) data. For most partners this will include `predicted_user_commission` only (the predicted amount you earn per ticket). Some partners will also see `predicted_gross_commission`, which is the total commission available to be shared between Ingresso and our partner. By default you will see `predicted_user_commission` only - if you think you need to see `predicted_gross_commission` as well then please get in touch. 


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
            "allows_leaving_single_seats": "always",
            "discount_code": "",
            "discount_desc": "",
            "is_offer": false,
            "non_offer_sale_seatprice": 35,
            "non_offer_sale_surcharge": 4,
            "number_available": 6,
            "percentage_saving": 0,
            "price_band_code": "A/pool",
            "sale_seatprice": 35,
            "sale_surcharge": 4
          },
          {
            "absolute_saving": 0,
            "allows_leaving_single_seats": "always",
            "discount_code": "",
            "discount_desc": "",
            "is_offer": false,
            "non_offer_sale_seatprice": 30,
            "non_offer_sale_surcharge": 4,
            "number_available": 6,
            "percentage_saving": 0,
            "price_band_code": "B/pool",
            "sale_seatprice": 30,
            "sale_surcharge": 4
          },
          {
            "absolute_saving": 0,
            "allows_leaving_single_seats": "always",
            "discount_code": "",
            "discount_desc": "",
            "is_offer": false,
            "non_offer_sale_seatprice": 25,
            "non_offer_sale_surcharge": 4,
            "number_available": 6,
            "percentage_saving": 0,
            "price_band_code": "C/pool",
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
            "allows_leaving_single_seats": "always",
            "discount_code": "",
            "discount_desc": "",
            "is_offer": false,
            "non_offer_sale_seatprice": 21,
            "non_offer_sale_surcharge": 3,
            "number_available": 6,
            "percentage_saving": 0,
            "price_band_code": "A/pool",
            "sale_seatprice": 21,
            "sale_surcharge": 3
          },
          {
            "absolute_saving": 0,
            "allows_leaving_single_seats": "always",
            "discount_code": "",
            "discount_desc": "",
            "is_offer": false,
            "non_offer_sale_seatprice": 18,
            "non_offer_sale_surcharge": 3,
            "number_available": 3,
            "percentage_saving": 0,
            "price_band_code": "B/pool",
            "sale_seatprice": 18,
            "sale_surcharge": 3
          }
        ],
        "ticket_type_code": "STALLS",
        "ticket_type_desc": "Stalls"
      },
      {
        "price_band": [
          {
            "absolute_saving": 0,
            "allows_leaving_single_seats": "always",
            "discount_code": "",
            "discount_desc": "",
            "is_offer": false,
            "non_offer_sale_seatprice": 47,
            "non_offer_sale_surcharge": 5,
            "number_available": 6,
            "percentage_saving": 0,
            "price_band_code": "A/pool",
            "sale_seatprice": 47,
            "sale_surcharge": 5
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
  "contiguous_seat_selection_only": true,
  "currency_code": "gbp",
  "currency_details": {
    "gbp": {
      "currency_code": "gbp",
      "currency_factor": 100,
      "currency_number": 826,
      "currency_places": 2,
      "currency_post_symbol": "",
      "currency_pre_symbol": "£"
    }
  },
  "valid_quantities": [
    1,
    2,
    3,
    4,
    5,
    6
  ]
}
```

```python
from pyticketswitch.price_band import PriceBand
from pyticketswitch.discount import Discount
from pyticketswitch.ticket_type import TicketType

[
    TicketType(
        code='CIRCLE',
        description='Upper circle',
        price_bands=[
            PriceBand(
                code='A/pool',
                allows_leaving_single_seats='always',
                default_discount=Discount(
                    code='',
                    description='',
                    price_band_code='A/pool',
                    is_offer=False,
                    seatprice=35.0,
                    surcharge=4.0,
                    non_offer_seatprice=35.0,
                    non_offer_surcharge=4.0,
                    availability=6,
                    percentage_saving=0,
                    absolute_saving=0.0,
                ),
                example_seats_are_real=True,
            ),
            PriceBand(
                code='B/pool',
                allows_leaving_single_seats='always',
                default_discount=Discount(
                    code='',
                    description='',
                    price_band_code='B/pool',
                    is_offer=False,
                    seatprice=30.0,
                    surcharge=4.0,
                    non_offer_seatprice=30.0,
                    non_offer_surcharge=4.0,
                    availability=6,
                    percentage_saving=0,
                    absolute_saving=0.0,
                ),
                example_seats_are_real=True,
            ),
            PriceBand(
                code='C/pool',
                allows_leaving_single_seats='always',
                default_discount=Discount(
                    code='',
                    description='',
                    price_band_code='C/pool',
                    is_offer=False,
                    seatprice=25.0,
                    surcharge=4.0,
                    non_offer_seatprice=25.0,
                    non_offer_surcharge=4.0,
                    availability=6,
                    percentage_saving=0,
                    absolute_saving=0.0,
                ),
                example_seats_are_real=True,
            )
        ],
    ),
    TicketType(
        code='STALLS',
        description='Stalls',
        price_bands=[
            PriceBand(
                code='A/pool',
                allows_leaving_single_seats='always',
                default_discount=Discount(
                    code='',
                    description='',
                    price_band_code='A/pool',
                    is_offer=False,
                    seatprice=21.0,
                    surcharge=3.0,
                    non_offer_seatprice=21.0,
                    non_offer_surcharge=3.0,
                    availability=6,
                    percentage_saving=0,
                    absolute_saving=0.0,
                ),
                example_seats_are_real=True,
            ),
            PriceBand(
                code='B/pool',
                allows_leaving_single_seats='always',
                default_discount=Discount(
                    code='',
                    description='',
                    price_band_code='B/pool',
                    is_offer=False,
                    seatprice=18.0,
                    surcharge=3.0,
                    non_offer_seatprice=18.0,
                    non_offer_surcharge=3.0,
                    availability=3,
                    percentage_saving=0,
                    absolute_saving=0.0,
                ),
                example_seats_are_real=True,
            )
        ],
    ),
    TicketType(
        code='BALCONY',
        description='Balcony',
        price_bands=[
            PriceBand(
                code='A/pool',
                allows_leaving_single_seats='always',
                default_discount=Discount(
                    code='',
                    description='',
                    price_band_code='A/pool',
                    is_offer=False,
                    seatprice=47.0,
                    surcharge=5.0,
                    non_offer_seatprice=47.0,
                    non_offer_surcharge=5.0,
                    availability=6,
                    percentage_saving=0,
                    absolute_saving=0.0,
                ),
                example_seats_are_real=True,
            )
        ],
    )
]
```

Availability is divided into a set of ticket types, and each ticket type is
subdivided into price bands. If you request individual seats for a seated event
each price band includes seat blocks for the contiguous seats. The structure of
the availability data returned is therefore:

<img src="https://d1wx4w35ubmdix.cloudfront.net/wl-media/images/availability-hierarchy.png" alt="Availability Hierarchy">

<aside class="notice">In the UK market, offers are typically classified as
either "discounted face value" or "no booking fee". Discounted face value offers
have an offer seatprice that is lower than the full price seatprice (and
normally they have an offer surcharge of zero). No booking fee offers have an
offer seatprice equal to the full price seatprice, and an offer surcharge equal
to zero. The standard is to not display a saving percentage for no booking fee
offers (some suppliers do not like their offers to show as percentage
discounts), and instead display "No fees" or similar.</aside>

This example response is for best available - following this there is an example
response that includes seats.

Top-level attributes:

Attribute | Description
--------- | -----------
`availability` | Contains ticket types, object described below.
`backend_is_broken` | If we see an unexpected error from the supplier system (for example a 500 error) we mark the system as "broken" for a period of time afterwards (the time can vary from nothing to 2 minutes). During this period of time this attribute will be `true` and we will return empty availability. This is an exceptional circumstance; to check if there is currently a supplier system issue you can check our [status page](https://status.ingresso.co.uk/).
`backend_is_down` | When `true` the supplier system cannot be contacted for some reason, for example they are having technical problems or scheduled maintenance. The response will include empty availability in this case. This is an exceptional circumstance; to check if there is currently a supplier system issue you can check our [status page](https://status.ingresso.co.uk/).
`backend_throttle_failed` | We allow a certain number of simultaneous requests to hit a supplier system and queue requests when the limit is reached. When this attribute is `true` your request has been sitting in our queue for a long time and we have timed out the request. This is an exceptional circumstance.
`contiguous_seat_selection_only` | If you have requested individual seats a value of `true` indicates that you can only select consecutive seats. `false` indicates that you can select seats without restriction *within a single ticket type and price band*. In most cases this will be `false`. If you would like to allow your customers to select seats without restriction across price bands and ticket types, you need to add multiple orders to a [trolley](#trolley), one order for each price band. However there are currently some restrictions enforced so if you want to do this you will need to contact us first api@ingresso.co.uk
`currency_code` | The currency code for the availability.
`currency_details` | Further detail for the currency, object described below.
`valid_quantities` | An array of valid quantities.


The **currency_details** object containing one currency
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


**Ticket type** attributes:

Attribute | Description
--------- | -----------
`price_band` | Object described below.
`ticket_type_code` | The unique identifier for the ticket type. For seated events this refers to a part of house / seating area such as Grand Circle.
`ticket_type_desc` | The description for the ticket type. This should be displayed to the customer (if you are offering seat selection to your customer then you would typically hard-code the description when drawing a seating plan).


**Price band** attributes:

Attribute | Description
--------- | -----------
`absolute_saving` | Defined as (`non_offer_sale_seatprice` + `non_offer_sale_surcharge`) - (`sale_seatprice` + `sale_surcharge`)
`allows_leaving_single_seats` | In most cases this is `always`. When set to `never`, the supplier ticketing system does not allow us to leave single seats (which are difficult to sell). If you attempt to reserve when leaving a single seat the reserve will fail, so you should prevent your customer from making a selection that leaves a single seat. The other possible value is `if_necessary`: reservations will succeed if there is no other possible seat selection with the number of desired tickets that would not also leave a single seat (e.g. two tickets are requested and all available seat blocks are of size 3 or smaller).
`discount_code` | The unique identifier of the default [discount](#discount-object). Each price band has a default discount, but additional discounts can be requested for a price band with [list discounts](#list-discounts).
`discount_desc` | The description of the default discount. We recommend to present this text to customers when `is_offer` is `true` to describe the offer.
`is_offer` | `true` if the ticket price is discounted below the full price, i.e. if `absolute_saving` is greater than zero.
`non_offer_sale_seatprice` | The per-ticket price for full-priced tickets. This will be the face value price when the market has such a concept (for example the London theatre market has this concept, but some New York theatre shows do not). This is the same as the `sale_seatprice` when the price band is not discounted.
`non_offer_sale_surcharge` | The per-ticket booking fee for full-priced tickets. To determine the total ticket price you must add together the `non_offer_sale_seatprice` and the `non_offer_sale_surcharge`.
`number_available` | This is the maximum number of contiguous seats that can be purchased. This applies to best available only - if you are using seat selection and `contiguous_seat_selection_only` is `false` it is possible to select above this number.
`percentage_saving` | Defined as `absolute_saving` / (`non_offer_sale_seatprice` + `non_offer_sale_surcharge`) * 100
`price_band_code` | The code for the price band, for example "C/pool". To uniquely identify a price band you should take the combination of `ticket_type_code` and `price_band_code`. The price band code is generally made up of the code from the underlying supplier system, e.g. "C", followed by a "/" separator then "pool" or "alloc", indicating whether the price band is taken from the general pool of tickets or is from a ring-fenced allocation. Integrating partners should just work with the full price_band_code, ignoring the constituent parts.
`price_band_desc` | The description for the price band. This will often not be present (not all supplier ticketing systems provide it) but when it is present it should be displayed to the customer.
`sale_seatprice` | The per-ticket price. This will be the face value price when the market has such a concept (for example the London theatre market has this concept, but some New York theatre shows do not). This is the same as the `non_offer_sale_seatprice` when the price band is not discounted.
`sale_surcharge` | The per-ticket booking fee. To determine the total ticket price you must add together the `sale_seatprice` and the `sale_surcharge`.


## --- Optional Parameters ---

There are several additional parameters described below that can be provided to
return additional availability data.

These additional parameters require extra processing to retrieve the requested
data, which will slow down the resource, sometimes substantially. They should
therefore only be used where necessary.


## Individual Seats

<aside class="warning">NOTE: we are going to change the format of the seats to better support large seated venues. The change will not be dramatic, but if you integrate against the current version you will need to update your integration later.</aside>

The Ingresso API can return a list of all available seats, allowing you to give
your customers choice rather than forcing them to take the best available
seats. If you have included `add_seat_blocks` in your request, the event
is seated, and the supplier system supports seat selection, then we will return
seat blocks containing seats. You can check for the presence of the seat blocks
to determine whether you can offer seat selection to your customer.

<aside class="notice">We have some options to save development effort when implementing seat selection. Please contact us for more detail api@ingresso.co.uk</aside>

### Request

> **Example request**

```shell
curl https://demo.ticketswitch.com/f13/availability.v1 \
    -u "demo:demopass" \
    -d "perf_id=7AB-5" \
    -d "add_seat_blocks" \
    --compressed \
    -G
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
ticket_types, meta = client.get_availability('7AB-5', seat_blocks=True)
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
            "allows_leaving_single_seats": "always",
            "discount_code": "NORMAL",
            "discount_desc": "Regular Ticket",
            "free_seat_blocks": {
              "blocks_by_row": {
                "A": [
                  [
                    "A1",
                    "A2",
                    "A3",
                    "A4",
                    "A5",
                    "A6",
                    "A7",
                    "A8",
                    "A9",
                    "A10"
                  ]
                ],
                "B": [
                  [
                    "B2",
                    "B3",
                    "B4",
                    "B5",
                    "B6",
                    "B7",
                    "B8",
                    "B9"
                  ]
                ]
              },
              "restricted_view_seats": [
                "A1",
                "A2",
                "A10"
              ],
              "seats_by_text_message": {
                "Great seat": [
                  "A6"
                ]
              },
              "separators_by_row": {
                "A": "",
                "B": ""
              }
            },
            "is_offer": false,
            "non_offer_sale_seatprice": 50,
            "non_offer_sale_surcharge": 5,
            "number_available": 4,
            "percentage_saving": 0,
            "price_band_code": "A/pool",
            "sale_seatprice": 50,
            "sale_surcharge": 5
          },
          {
            "absolute_saving": 0,
            "allows_leaving_single_seats": "always",
            "discount_code": "NORMAL",
            "discount_desc": "Regular Ticket",
            "free_seat_blocks": {
              "blocks_by_row": {
                "C": [
                  [
                    "C3",
                    "C4",
                    "C5",
                    "C6"
                  ]
                ],
                "D": [
                  [
                    "D2",
                    "D3",
                    "D4",
                    "D5",
                    "D6",
                    "D7"
                  ]
                ]
              },
              "restricted_view_seats": [
                "C5",
                "C6"
              ],
              "seats_by_text_message": {
                "Haunted seat": [
                  "C6"
                ]
              },
              "separators_by_row": {
                "C": "",
                "D": ""
              }
            },
            "is_offer": false,
            "non_offer_sale_seatprice": 40,
            "non_offer_sale_surcharge": 5,
            "number_available": 4,
            "percentage_saving": 0,
            "price_band_code": "B/pool",
            "sale_seatprice": 40,
            "sale_surcharge": 5
          }
        ],
        "ticket_type_code": "STALLS",
        "ticket_type_desc": "Stalls"
      },
      {
        "price_band": [
          {
            "absolute_saving": 0,
            "allows_leaving_single_seats": "always",
            "discount_code": "NORMAL",
            "discount_desc": "Regular Ticket",
            "free_seat_blocks": {
              "blocks_by_row": {
                "E": [
                  [
                    "E4",
                    "E5"
                  ],
                  [
                    "E7",
                    "E8",
                    "E9"
                  ]
                ],
                "F": [
                  [
                    "F1",
                    "F2",
                    "F3"
                  ]
                ]
              },
              "restricted_view_seats": [],
              "seats_by_text_message": {},
              "separators_by_row": {
                "E": "",
                "F": ""
              }
            },
            "is_offer": false,
            "non_offer_sale_seatprice": 35,
            "non_offer_sale_surcharge": 5,
            "number_available": 4,
            "percentage_saving": 0,
            "price_band_code": "A/pool",
            "sale_seatprice": 35,
            "sale_surcharge": 5
          },
          {
            "absolute_saving": 0,
            "allows_leaving_single_seats": "always",
            "discount_code": "NORMAL",
            "discount_desc": "Regular Ticket",
            "free_seat_blocks": {
              "blocks_by_row": {
                "G": [
                  [
                    "G7",
                    "G8",
                    "G9",
                    "G10"
                  ]
                ],
                "H": [
                  [
                    "H1",
                    "H2",
                    "H3",
                    "H4"
                  ],
                  [
                    "H7",
                    "H8",
                    "H9",
                    "H10"
                  ]
                ]
              },
              "restricted_view_seats": [],
              "seats_by_text_message": {},
              "separators_by_row": {
                "G": "",
                "H": ""
              }
            },
            "is_offer": false,
            "non_offer_sale_seatprice": 30,
            "non_offer_sale_surcharge": 5,
            "number_available": 4,
            "percentage_saving": 0,
            "price_band_code": "B/pool",
            "sale_seatprice": 30,
            "sale_surcharge": 5
          }
        ],
        "ticket_type_code": "CIRCLE",
        "ticket_type_desc": "Dress Circle"
      }
    ]
  },
  "backend_is_broken": false,
  "backend_is_down": false,
  "backend_throttle_failed": false,
  "contiguous_seat_selection_only": false,
  "currency_code": "gbp",
  "currency_details": {
    "gbp": {
      "currency_code": "gbp",
      "currency_factor": 100,
      "currency_number": 826,
      "currency_places": 2,
      "currency_post_symbol": "",
      "currency_pre_symbol": "£"
    }
  },
  "valid_quantities": [
    1,
    2,
    3,
    4
  ]
}
```

```python
from pyticketswitch.discount import Discount
from pyticketswitch.ticket_type import TicketType
from pyticketswitch.seat import Seat
from pyticketswitch.seat import SeatBlock
from pyticketswitch.price_band import PriceBand

[
    TicketType(
        code='STALLS',
        description='Stalls',
        price_bands=[
            PriceBand(
                code='A/pool',
                allows_leaving_single_seats='always',
                default_discount=Discount(
                    code='NORMAL',
                    description='Regular Ticket',
                    price_band_code='A/pool',
                    is_offer=False,
                    seatprice=50.0,
                    surcharge=5.0,
                    non_offer_seatprice=50.0,
                    non_offer_surcharge=5.0,
                    availability=4,
                    percentage_saving=0,
                    absolute_saving=0.0,
                ),
                example_seats_are_real=True,
                seat_blocks=[
                    SeatBlock(
                        length=10,
                        seats=[
                            Seat(
                                id='A1',
                                column='1',
                                row='A',
                                separator='',
                                is_restricted=True,
                                seat_text='',
                            ),
                            Seat(
                                id='A2',
                                column='2',
                                row='A',
                                separator='',
                                is_restricted=True,
                                seat_text='',
                            ),
                            Seat(
                                id='A3',
                                column='3',
                                row='A',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='A4',
                                column='4',
                                row='A',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='A5',
                                column='5',
                                row='A',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='A6',
                                column='6',
                                row='A',
                                separator='',
                                is_restricted=False,
                                seat_text='Great seat',
                            ),
                            Seat(
                                id='A7',
                                column='7',
                                row='A',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='A8',
                                column='8',
                                row='A',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='A9',
                                column='9',
                                row='A',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='A10',
                                column='10',
                                row='A',
                                separator='',
                                is_restricted=True,
                                seat_text='',
                            )
                        ],
                    ),
                    SeatBlock(
                        length=8,
                        seats=[
                            Seat(
                                id='B2',
                                column='2',
                                row='B',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='B3',
                                column='3',
                                row='B',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='B4',
                                column='4',
                                row='B',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='B5',
                                column='5',
                                row='B',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='B6',
                                column='6',
                                row='B',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='B7',
                                column='7',
                                row='B',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='B8',
                                column='8',
                                row='B',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='B9',
                                column='9',
                                row='B',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            )
                        ],
                    )
                ],
            ),
            PriceBand(
                code='B/pool',
                allows_leaving_single_seats='always',
                default_discount=Discount(
                    code='NORMAL',
                    description='Regular Ticket',
                    price_band_code='B/pool',
                    is_offer=False,
                    seatprice=40.0,
                    surcharge=5.0,
                    non_offer_seatprice=40.0,
                    non_offer_surcharge=5.0,
                    availability=4,
                    percentage_saving=0,
                    absolute_saving=0.0,
                ),
                example_seats_are_real=True,
                seat_blocks=[
                    SeatBlock(
                        length=4,
                        seats=[
                            Seat(
                                id='C3',
                                column='3',
                                row='C',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='C4',
                                column='4',
                                row='C',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='C5',
                                column='5',
                                row='C',
                                separator='',
                                is_restricted=True,
                                seat_text='',
                            ),
                            Seat(
                                id='C6',
                                column='6',
                                row='C',
                                separator='',
                                is_restricted=True,
                                seat_text='Haunted seat',
                            )
                        ],
                    ),
                    SeatBlock(
                        length=6,
                        seats=[
                            Seat(
                                id='D2',
                                column='2',
                                row='D',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='D3',
                                column='3',
                                row='D',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='D4',
                                column='4',
                                row='D',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='D5',
                                column='5',
                                row='D',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='D6',
                                column='6',
                                row='D',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='D7',
                                column='7',
                                row='D',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            )
                        ],
                    )
                ],
            )
        ],
    ),
    TicketType(
        code='CIRCLE',
        description='Dress Circle',
        price_bands=[
            PriceBand(
                code='A/pool',
                allows_leaving_single_seats='always',
                default_discount=Discount(
                    code='NORMAL',
                    description='Regular Ticket',
                    price_band_code='A/pool',
                    is_offer=False,
                    seatprice=35.0,
                    surcharge=5.0,
                    non_offer_seatprice=35.0,
                    non_offer_surcharge=5.0,
                    availability=4,
                    percentage_saving=0,
                    absolute_saving=0.0,
                ),
                example_seats_are_real=True,
                seat_blocks=[
                    SeatBlock(
                        length=2,
                        seats=[
                            Seat(
                                id='E4',
                                column='4',
                                row='E',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='E5',
                                column='5',
                                row='E',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            )
                        ],
                    ),
                    SeatBlock(
                        length=3,
                        seats=[
                            Seat(
                                id='E7',
                                column='7',
                                row='E',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='E8',
                                column='8',
                                row='E',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='E9',
                                column='9',
                                row='E',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            )
                        ],
                    ),
                    SeatBlock(
                        length=3,
                        seats=[
                            Seat(
                                id='F1',
                                column='1',
                                row='F',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='F2',
                                column='2',
                                row='F',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='F3',
                                column='3',
                                row='F',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            )
                        ],
                    )
                ],
            ),
            PriceBand(
                code='B/pool',
                allows_leaving_single_seats='always',
                default_discount=Discount(
                    code='NORMAL',
                    description='Regular Ticket',
                    price_band_code='B/pool',
                    is_offer=False,
                    seatprice=30.0,
                    surcharge=5.0,
                    non_offer_seatprice=30.0,
                    non_offer_surcharge=5.0,
                    availability=4,
                    percentage_saving=0,
                    absolute_saving=0.0,
                ),
                example_seats_are_real=True,
                seat_blocks=[
                    SeatBlock(
                        length=4,
                        seats=[
                            Seat(
                                id='G7',
                                column='7',
                                row='G',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='G8',
                                column='8',
                                row='G',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='G9',
                                column='9',
                                row='G',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='G10',
                                column='10',
                                row='G',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            )
                        ],
                    ),
                    SeatBlock(
                        length=4,
                        seats=[
                            Seat(
                                id='H1',
                                column='1',
                                row='H',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='H2',
                                column='2',
                                row='H',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='H3',
                                column='3',
                                row='H',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='H4',
                                column='4',
                                row='H',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            )
                        ],
                    ),
                    SeatBlock(
                        length=4,
                        seats=[
                            Seat(
                                id='H7',
                                column='7',
                                row='H',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='H8',
                                column='8',
                                row='H',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='H9',
                                column='9',
                                row='H',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            ),
                            Seat(
                                id='H10',
                                column='10',
                                row='H',
                                separator='',
                                is_restricted=False,
                                seat_text='',
                            )
                        ],
                    )
                ],
            )
        ],
    )
]
```

The response includes the following attributes in the `free_seat_blocks`:

Attribute | Description
--------- | -----------
`blocks_by_row` | A list of the rows that contain seats. The row identifier is the key and the value is an array of contiguous seat blocks within that row. Within each contiguous seat block there is an array of seat IDs. These seat IDs all begin with the row identifier, and include the column identifier.  The IDs should be displayed to the customer. Note that when `contiguous_seat_selection_only` = `true` then you can only reserve seats within a single seat block, and only in a consecutive order in the array. In most cases `contiguous_seat_selection_only` = `false` and you can select any seats from `free_seat_blocks`.
`restricted_view_seats` | A list of seat IDs that are classified as having a restricted view. You must indicate this to your customer before they purchase tickets. Note that it is possible for seats to be included in this array but to not have a seat message. Your application should still display a message to the customer in this case.
`seats_by_text_message` | A list of seat messages. The key is the seat message, and the value is an array of seat IDs that the message applies to. When you see a seat message you must display this to your customer. 
`separators_by_row` | The row identifier is the key, the value is a separator. Separators are between the row ID and the column ID, for example seat ID `A.10` has a separator of `.`, a row ID of `A` and a column ID of `10`. In most cases there is no separator, which is represented by an empty string. 


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
curl https://demo.ticketswitch.com/f13/availability.v1 \
    -u "demo:demopass" \
    -d "perf_id=7AB-5" \
    -d "no_of_seats=3" \
    -d "add_example_seats" \
    --compressed \
    -G
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
ticket_types, meta = client.get_availability('7AB-5', example_seats=True)
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
            "allows_leaving_single_seats": "always",
            "discount_code": "NORMAL",
            "discount_desc": "Regular Ticket",
            "is_offer": false,
            "non_offer_sale_seatprice": 50,
            "non_offer_sale_surcharge": 5,
            "number_available": 4,
            "percentage_saving": 0,
            "price_band_code": "A/pool",
            "sale_seatprice": 50,
            "sale_surcharge": 5
          },
          {
            "absolute_saving": 0,
            "allows_leaving_single_seats": "always",
            "discount_code": "NORMAL",
            "discount_desc": "Regular Ticket",
            "is_offer": false,
            "non_offer_sale_seatprice": 40,
            "non_offer_sale_surcharge": 5,
            "number_available": 4,
            "percentage_saving": 0,
            "price_band_code": "B/pool",
            "sale_seatprice": 40,
            "sale_surcharge": 5
          }
        ],
        "ticket_type_code": "STALLS",
        "ticket_type_desc": "Stalls"
      },
      {
        "price_band": [
          {
            "absolute_saving": 0,
            "allows_leaving_single_seats": "always",
            "discount_code": "NORMAL",
            "discount_desc": "Regular Ticket",
            "example_seats": [
              {
                "col_id": "1",
                "full_id": "F1",
                "is_restricted_view": false,
                "row_id": "F"
              },
              {
                "col_id": "2",
                "full_id": "F2",
                "is_restricted_view": false,
                "row_id": "F"
              }
            ],
            "example_seats_are_real": false,
            "is_offer": false,
            "non_offer_sale_seatprice": 35,
            "non_offer_sale_surcharge": 5,
            "number_available": 4,
            "percentage_saving": 0,
            "price_band_code": "A/pool",
            "sale_seatprice": 35,
            "sale_surcharge": 5
          },
          {
            "absolute_saving": 0,
            "allows_leaving_single_seats": "always",
            "discount_code": "NORMAL",
            "discount_desc": "Regular Ticket",
            "is_offer": false,
            "non_offer_sale_seatprice": 30,
            "non_offer_sale_surcharge": 5,
            "number_available": 4,
            "percentage_saving": 0,
            "price_band_code": "B/pool",
            "sale_seatprice": 30,
            "sale_surcharge": 5
          }
        ],
        "ticket_type_code": "CIRCLE",
        "ticket_type_desc": "Dress Circle"
      }
    ]
  },
  "backend_is_broken": false,
  "backend_is_down": false,
  "backend_throttle_failed": false,
  "contiguous_seat_selection_only": false,
  "currency_code": "gbp",
  "currency_details": {
    "gbp": {
      "currency_code": "gbp",
      "currency_factor": 100,
      "currency_number": 826,
      "currency_places": 2,
      "currency_post_symbol": "",
      "currency_pre_symbol": "£"
    }
  },
  "valid_quantities": [
    1,
    2,
    3,
    4
  ]
}
```

```python
from pyticketswitch.price_band import PriceBand
from pyticketswitch.discount import Discount
from pyticketswitch.ticket_type import TicketType
from pyticketswitch.seat import Seat

[
    TicketType(
        code='STALLS',
        description='Stalls',
        price_bands=[
            PriceBand(
                code='A/pool',
                allows_leaving_single_seats='always',
                default_discount=Discount(
                    code='NORMAL',
                    description='Regular Ticket',
                    price_band_code='A/pool',
                    is_offer=False,
                    seatprice=50.0,
                    surcharge=5.0,
                    non_offer_seatprice=50.0,
                    non_offer_surcharge=5.0,
                    availability=4,
                    percentage_saving=0,
                    absolute_saving=0.0,
                ),
                example_seats=[
                    Seat(
                        id='B2',
                        column='2',
                        row='B',
                        separator='',
                        is_restricted=False,
                    ),
                    Seat(
                        id='B3',
                        column='3',
                        row='B',
                        separator='',
                        is_restricted=False,
                    )
                ],
                example_seats_are_real=False,
            ),
            PriceBand(
                code='B/pool',
                allows_leaving_single_seats='always',
                default_discount=Discount(
                    code='NORMAL',
                    description='Regular Ticket',
                    price_band_code='B/pool',
                    is_offer=False,
                    seatprice=40.0,
                    surcharge=5.0,
                    non_offer_seatprice=40.0,
                    non_offer_surcharge=5.0,
                    availability=4,
                    percentage_saving=0,
                    absolute_saving=0.0,
                ),
                example_seats_are_real=True,
            )
        ],
    ),
    TicketType(
        code='CIRCLE',
        description='Dress Circle',
        price_bands=[
            PriceBand(
                code='A/pool',
                allows_leaving_single_seats='always',
                default_discount=Discount(
                    code='NORMAL',
                    description='Regular Ticket',
                    price_band_code='A/pool',
                    is_offer=False,
                    seatprice=35.0,
                    surcharge=5.0,
                    non_offer_seatprice=35.0,
                    non_offer_surcharge=5.0,
                    availability=4,
                    percentage_saving=0,
                    absolute_saving=0.0,
                ),
                example_seats_are_real=True,
            ),
            PriceBand(
                code='B/pool',
                allows_leaving_single_seats='always',
                default_discount=Discount(
                    code='NORMAL',
                    description='Regular Ticket',
                    price_band_code='B/pool',
                    is_offer=False,
                    seatprice=30.0,
                    surcharge=5.0,
                    non_offer_seatprice=30.0,
                    non_offer_surcharge=5.0,
                    availability=4,
                    percentage_saving=0,
                    absolute_saving=0.0,
                ),
                example_seats_are_real=True,
            )
        ],
    )
]
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
`id_details.seat_subdata` | Ignore - this will be removed soon.
`id_details.seat_text` | *(Optional)* A description of the seat that should be displayed to the customer. If the seat has a restricted view this text will normally be present to describe the restriction in more detail, but it should be displayed in all cases. 
`id_details.seat_text_code` | Ignore - this will be removed soon.
`id_details.separator` | Sometimes a seat ID can include a separator character such as `.`, e.g. `A.23`.


## Commission

Some partners wish to view the predicted commission they will earn on each ticket up
front, before the sale is made. This could be to support agents that want to
know what they will earn, or to support custom repricing.

<aside class="notice">Ingresso has a pricing dashboard, so there is no need for you to develop your own repricing functionality.</aside>

Availability will return the predicted per ticket commission you will earn. If you
subtract commission from the total ticket price (`sale_seatprice` +
`sale_surcharge`) you have the net price of the ticket.

### Request

> **Example request**

```shell
curl https://demo.ticketswitch.com/f13/availability.v1 \
    -u "demo:demopass" \
    -d "perf_id=6IF-B5P" \
    -d "req_predicted_commission" \
    --compressed \
    -G
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
ticket_types, meta = client.get_availability('6IF-B5P', user_commission=True)
```

Parameter | Description
--------- | -----------
`req_predicted_commission` | Include to retrieve commission data. For most partners this will include `predicted_user_commission` only (the predicted amount you earn per ticket). Some partners will also see `predicted_gross_commission`, which is the total commission available to be shared between Ingresso and our partner. By default you will see `predicted_user_commission` only - if you think you need to see `predicted_gross_commission` as well then please get in touch.


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
            "allows_leaving_single_seats": "always",
            "discount_code": "",
            "discount_desc": "",
            "predicted_gross_commission": {
              "amount_excluding_vat": 4.88,
              "amount_including_vat": 5.85,
              "commission_currency_code": "gbp"
            },
            "is_offer": false,
            "non_offer_sale_seatprice": 35,
            "non_offer_sale_surcharge": 4,
            "number_available": 6,
            "percentage_saving": 0,
            "price_band_code": "A/pool",
            "sale_seatprice": 35,
            "sale_surcharge": 4,
            "predicted_user_commission": {
              "amount_excluding_vat": 2.19,
              "amount_including_vat": 2.63,
              "commission_currency_code": "gbp"
            }
          },
          {
            "absolute_saving": 0,
            "allows_leaving_single_seats": "always",
            "discount_code": "",
            "discount_desc": "",
            "predicted_gross_commission": {
              "amount_excluding_vat": 4.25,
              "amount_including_vat": 5.1,
              "commission_currency_code": "gbp"
            },
            "is_offer": false,
            "non_offer_sale_seatprice": 30,
            "non_offer_sale_surcharge": 4,
            "number_available": 6,
            "percentage_saving": 0,
            "price_band_code": "B/pool",
            "sale_seatprice": 30,
            "sale_surcharge": 4,
            "predicted_user_commission": {
              "amount_excluding_vat": 1.91,
              "amount_including_vat": 2.3,
              "commission_currency_code": "gbp"
            }
          },
          {
            "absolute_saving": 0,
            "allows_leaving_single_seats": "always",
            "discount_code": "",
            "discount_desc": "",
            "predicted_gross_commission": {
              "amount_excluding_vat": 3.63,
              "amount_including_vat": 4.35,
              "commission_currency_code": "gbp"
            },
            "is_offer": false,
            "non_offer_sale_seatprice": 25,
            "non_offer_sale_surcharge": 4,
            "number_available": 6,
            "percentage_saving": 0,
            "price_band_code": "C/pool",
            "sale_seatprice": 25,
            "sale_surcharge": 4,
            "predicted_user_commission": {
              "amount_excluding_vat": 1.63,
              "amount_including_vat": 1.96,
              "commission_currency_code": "gbp"
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
            "allows_leaving_single_seats": "always",
            "discount_code": "",
            "discount_desc": "",
            "predicted_gross_commission": {
              "amount_excluding_vat": 3,
              "amount_including_vat": 3.6,
              "commission_currency_code": "gbp"
            },
            "is_offer": false,
            "non_offer_sale_seatprice": 21,
            "non_offer_sale_surcharge": 3,
            "number_available": 6,
            "percentage_saving": 0,
            "price_band_code": "A/pool",
            "sale_seatprice": 21,
            "sale_surcharge": 3,
            "predicted_user_commission": {
              "amount_excluding_vat": 1.35,
              "amount_including_vat": 1.62,
              "commission_currency_code": "gbp"
            }
          },
          {
            "absolute_saving": 0,
            "allows_leaving_single_seats": "always",
            "discount_code": "",
            "discount_desc": "",
            "predicted_gross_commission": {
              "amount_excluding_vat": 2.63,
              "amount_including_vat": 3.15,
              "commission_currency_code": "gbp"
            },
            "is_offer": false,
            "non_offer_sale_seatprice": 18,
            "non_offer_sale_surcharge": 3,
            "number_available": 3,
            "percentage_saving": 0,
            "price_band_code": "B/pool",
            "sale_seatprice": 18,
            "sale_surcharge": 3,
            "predicted_user_commission": {
              "amount_excluding_vat": 1.18,
              "amount_including_vat": 1.42,
              "commission_currency_code": "gbp"
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
            "allows_leaving_single_seats": "always",
            "discount_code": "",
            "discount_desc": "",
            "predicted_gross_commission": {
              "amount_excluding_vat": 6.5,
              "amount_including_vat": 7.8,
              "commission_currency_code": "gbp"
            },
            "is_offer": false,
            "non_offer_sale_seatprice": 47,
            "non_offer_sale_surcharge": 5,
            "number_available": 6,
            "percentage_saving": 0,
            "price_band_code": "A/pool",
            "sale_seatprice": 47,
            "sale_surcharge": 5,
            "predicted_user_commission": {
              "amount_excluding_vat": 2.93,
              "amount_including_vat": 3.51,
              "commission_currency_code": "gbp"
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
  "contiguous_seat_selection_only": true,
  "currency_code": "gbp",
  "currency_details": {
    "gbp": {
      "currency_code": "gbp",
      "currency_factor": 100,
      "currency_number": 826,
      "currency_places": 2,
      "currency_post_symbol": "",
      "currency_pre_symbol": "£"
    }
  },
  "valid_quantities": [
    1,
    2,
    3,
    4,
    5,
    6
  ]
}
```

```python
from pyticketswitch.commission import Commission
from pyticketswitch.ticket_type import TicketType
from pyticketswitch.discount import Discount
from pyticketswitch.price_band import PriceBand

[
    TicketType(
        code='CIRCLE',
        description='Upper circle',
        price_bands=[
            PriceBand(
                code='A/pool',
                allows_leaving_single_seats='always',
                default_discount=Discount(
                    code='',
                    description='',
                    price_band_code='A/pool',
                    is_offer=False,
                    seatprice=35.0,
                    surcharge=4.0,
                    non_offer_seatprice=35.0,
                    non_offer_surcharge=4.0,
                    availability=6,
                    percentage_saving=0,
                    absolute_saving=0.0,
                ),
                example_seats_are_real=True,
                user_commission=Commission(
                    including_vat=2.63,
                    excluding_vat=2.19,
                    currency_code='gbp',
                ),
            ),
            PriceBand(
                code='B/pool',
                allows_leaving_single_seats='always',
                default_discount=Discount(
                    code='',
                    description='',
                    price_band_code='B/pool',
                    is_offer=False,
                    seatprice=30.0,
                    surcharge=4.0,
                    non_offer_seatprice=30.0,
                    non_offer_surcharge=4.0,
                    availability=6,
                    percentage_saving=0,
                    absolute_saving=0.0,
                ),
                example_seats_are_real=True,
                user_commission=Commission(
                    including_vat=2.3,
                    excluding_vat=1.91,
                    currency_code='gbp',
                ),
            ),
            PriceBand(
                code='C/pool',
                allows_leaving_single_seats='always',
                default_discount=Discount(
                    code='',
                    description='',
                    price_band_code='C/pool',
                    is_offer=False,
                    seatprice=25.0,
                    surcharge=4.0,
                    non_offer_seatprice=25.0,
                    non_offer_surcharge=4.0,
                    availability=6,
                    percentage_saving=0,
                    absolute_saving=0.0,
                ),
                example_seats_are_real=True,
                user_commission=Commission(
                    including_vat=1.96,
                    excluding_vat=1.63,
                    currency_code='gbp',
                ),
            )
        ],
    ),
    TicketType(
        code='STALLS',
        description='Stalls',
        price_bands=[
            PriceBand(
                code='A/pool',
                allows_leaving_single_seats='always',
                default_discount=Discount(
                    code='',
                    description='',
                    price_band_code='A/pool',
                    is_offer=False,
                    seatprice=21.0,
                    surcharge=3.0,
                    non_offer_seatprice=21.0,
                    non_offer_surcharge=3.0,
                    availability=6,
                    percentage_saving=0,
                    absolute_saving=0.0,
                ),
                example_seats_are_real=True,
                user_commission=Commission(
                    including_vat=1.62,
                    excluding_vat=1.35,
                    currency_code='gbp',
                ),
            ),
            PriceBand(
                code='B/pool',
                allows_leaving_single_seats='always',
                default_discount=Discount(
                    code='',
                    description='',
                    price_band_code='B/pool',
                    is_offer=False,
                    seatprice=18.0,
                    surcharge=3.0,
                    non_offer_seatprice=18.0,
                    non_offer_surcharge=3.0,
                    availability=3,
                    percentage_saving=0,
                    absolute_saving=0.0,
                ),
                example_seats_are_real=True,
                user_commission=Commission(
                    including_vat=1.42,
                    excluding_vat=1.18,
                    currency_code='gbp',
                ),
            )
        ],
    ),
    TicketType(
        code='BALCONY',
        description='Balcony',
        price_bands=[
            PriceBand(
                code='A/pool',
                allows_leaving_single_seats='always',
                default_discount=Discount(
                    code='',
                    description='',
                    price_band_code='A/pool',
                    is_offer=False,
                    seatprice=47.0,
                    surcharge=5.0,
                    non_offer_seatprice=47.0,
                    non_offer_surcharge=5.0,
                    availability=6,
                    percentage_saving=0,
                    absolute_saving=0.0,
                ),
                example_seats_are_real=True,
                user_commission=Commission(
                    including_vat=3.51,
                    excluding_vat=2.93,
                    currency_code='gbp',
                ),
            )
        ],
    )
]
```


The response includes several attributes within the `predicted_user_commission`
dictionary. Some partners will also see `predicted_gross_commission` which includes the
same attributes.

Attribute | Description
--------- | -----------
`amount_excluding_vat` | The predicted commission you will earn per ticket, excluding sales tax.
`amount_including_vat` | The predicted commission you will earn per ticket, including sales tax.
`commission_currency_code` | The commission currency (note that this can be different to the currency of the price paid by the customer).


## Add Discounts

A `discount` represents a price type or concession that is available for a set
of available tickets.

### Request

> **Example request**

```shell
curl https://demo.ticketswitch.com/f13/availability.v1 \
    -u "demo:demopass" \
    -d "perf_id=6IF-B5P" \
    -d "add_discounts" \
    --compressed \
    -G
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
ticket_types, meta = client.get_availability('6IF-B5P', discounts=True)
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
            "allows_leaving_single_seats": "always",
            "discount_code": "",
            "discount_desc": "",
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
            "allows_leaving_single_seats": "always",
            "discount_code": "",
            "discount_desc": "",
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
            "allows_leaving_single_seats": "always",
            "discount_code": "",
            "discount_desc": "",
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
            "allows_leaving_single_seats": "always",
            "discount_code": "",
            "discount_desc": "",
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
            "allows_leaving_single_seats": "always",
            "discount_code": "",
            "discount_desc": "",
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
            "allows_leaving_single_seats": "always",
            "discount_code": "",
            "discount_desc": "",
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
  "contiguous_seat_selection_only": true,
  "currency": {
    "currency_code": "gbp"
  },
  "valid_quantities": [
    1,
    2,
    3,
    4,
    5,
    6
  ]
}
```

```python
from pyticketswitch.price_band import PriceBand
from pyticketswitch.ticket_type import TicketType
from pyticketswitch.discount import Discount

[
    TicketType(
        code='CIRCLE',
        description='Upper circle',
        price_bands=[
            PriceBand(
                code='A/pool',
                allows_leaving_single_seats='always',
                default_discount=Discount(
                    code='',
                    description='',
                    price_band_code='A/pool',
                    is_offer=False,
                    seatprice=35.0,
                    surcharge=4.0,
                    non_offer_seatprice=35.0,
                    non_offer_surcharge=4.0,
                    availability=6,
                    percentage_saving=0,
                    absolute_saving=0.0,
                ),
                example_seats_are_real=True,
                discounts=[
                    Discount(
                        code='ADULT',
                        description='Adult',
                        price_band_code='A/pool',
                        is_offer=False,
                        seatprice=35.0,
                        surcharge=4.0,
                        non_offer_seatprice=35.0,
                        non_offer_surcharge=4.0,
                        availability=6,
                        percentage_saving=0,
                        absolute_saving=0.0,
                    ),
                    Discount(
                        code='CHILD',
                        description='Child rate',
                        price_band_code='A/pool',
                        is_offer=False,
                        seatprice=18.0,
                        surcharge=3.0,
                        non_offer_seatprice=18.0,
                        non_offer_surcharge=3.0,
                        availability=6,
                        percentage_saving=0,
                        absolute_saving=0.0,
                    ),
                    Discount(
                        code='STUDENT',
                        description='Student rate',
                        price_band_code='A/pool',
                        is_offer=False,
                        seatprice=26.0,
                        surcharge=3.0,
                        non_offer_seatprice=26.0,
                        non_offer_surcharge=3.0,
                        availability=6,
                        percentage_saving=0,
                        absolute_saving=0.0,
                    ),
                    Discount(
                        code='OAP',
                        description='Senior citizen rate',
                        price_band_code='A/pool',
                        is_offer=False,
                        seatprice=28.0,
                        surcharge=3.0,
                        non_offer_seatprice=28.0,
                        non_offer_surcharge=3.0,
                        availability=6,
                        percentage_saving=0,
                        absolute_saving=0.0,
                    )
                ],
            ),
            PriceBand(
                code='B/pool',
                allows_leaving_single_seats='always',
                default_discount=Discount(
                    code='',
                    description='',
                    price_band_code='B/pool',
                    is_offer=False,
                    seatprice=30.0,
                    surcharge=4.0,
                    non_offer_seatprice=30.0,
                    non_offer_surcharge=4.0,
                    availability=6,
                    percentage_saving=0,
                    absolute_saving=0.0,
                ),
                example_seats_are_real=True,
                discounts=[
                    Discount(
                        code='ADULT',
                        description='Adult',
                        price_band_code='B/pool',
                        is_offer=False,
                        seatprice=30.0,
                        surcharge=4.0,
                        non_offer_seatprice=30.0,
                        non_offer_surcharge=4.0,
                        availability=6,
                        percentage_saving=0,
                        absolute_saving=0.0,
                    ),
                    Discount(
                        code='CHILD',
                        description='Child rate',
                        price_band_code='B/pool',
                        is_offer=False,
                        seatprice=15.0,
                        surcharge=3.0,
                        non_offer_seatprice=15.0,
                        non_offer_surcharge=3.0,
                        availability=6,
                        percentage_saving=0,
                        absolute_saving=0.0,
                    )
                ],
            ),
            PriceBand(
                code='C/pool',
                allows_leaving_single_seats='always',
                default_discount=Discount(
                    code='',
                    description='',
                    price_band_code='C/pool',
                    is_offer=False,
                    seatprice=25.0,
                    surcharge=4.0,
                    non_offer_seatprice=25.0,
                    non_offer_surcharge=4.0,
                    availability=6,
                    percentage_saving=0,
                    absolute_saving=0.0,
                ),
                example_seats_are_real=True,
                discounts=[
                    Discount(
                        code='ADULT',
                        description='Adult',
                        price_band_code='C/pool',
                        is_offer=False,
                        seatprice=25.0,
                        surcharge=4.0,
                        non_offer_seatprice=25.0,
                        non_offer_surcharge=4.0,
                        availability=6,
                        percentage_saving=0,
                        absolute_saving=0.0,
                    ),
                    Discount(
                        code='CHILD',
                        description='Child rate',
                        price_band_code='C/pool',
                        is_offer=False,
                        seatprice=13.0,
                        surcharge=3.0,
                        non_offer_seatprice=13.0,
                        non_offer_surcharge=3.0,
                        availability=6,
                        percentage_saving=0,
                        absolute_saving=0.0,
                    ),
                    Discount(
                        code='STUDENT',
                        description='Student rate',
                        price_band_code='C/pool',
                        is_offer=False,
                        seatprice=19.0,
                        surcharge=3.0,
                        non_offer_seatprice=19.0,
                        non_offer_surcharge=3.0,
                        availability=6,
                        percentage_saving=0,
                        absolute_saving=0.0,
                    ),
                    Discount(
                        code='OAP',
                        description='Senior citizen rate',
                        price_band_code='C/pool',
                        is_offer=False,
                        seatprice=20.0,
                        surcharge=3.0,
                        non_offer_seatprice=20.0,
                        non_offer_surcharge=3.0,
                        availability=6,
                        percentage_saving=0,
                        absolute_saving=0.0,
                    )
                ],
            )
        ],
    ),
    TicketType(
        code='STALLS',
        description='Stalls',
        price_bands=[
            PriceBand(
                code='A/pool',
                allows_leaving_single_seats='always',
                default_discount=Discount(
                    code='',
                    description='',
                    price_band_code='A/pool',
                    is_offer=False,
                    seatprice=21.0,
                    surcharge=3.0,
                    non_offer_seatprice=21.0,
                    non_offer_surcharge=3.0,
                    availability=6,
                    percentage_saving=0,
                    absolute_saving=0.0,
                ),
                example_seats_are_real=True,
                discounts=[
                    Discount(
                        code='ADULT',
                        description='Adult',
                        price_band_code='A/pool',
                        is_offer=False,
                        seatprice=21.0,
                        surcharge=3.0,
                        non_offer_seatprice=21.0,
                        non_offer_surcharge=3.0,
                        availability=6,
                        percentage_saving=0,
                        absolute_saving=0.0,
                    ),
                    Discount(
                        code='CHILD',
                        description='Child rate',
                        price_band_code='A/pool',
                        is_offer=False,
                        seatprice=11.0,
                        surcharge=2.0,
                        non_offer_seatprice=11.0,
                        non_offer_surcharge=2.0,
                        availability=6,
                        percentage_saving=0,
                        absolute_saving=0.0,
                    ),
                    Discount(
                        code='STUDENT',
                        description='Student rate',
                        price_band_code='A/pool',
                        is_offer=False,
                        seatprice=16.0,
                        surcharge=2.0,
                        non_offer_seatprice=16.0,
                        non_offer_surcharge=2.0,
                        availability=6,
                        percentage_saving=0,
                        absolute_saving=0.0,
                    ),
                    Discount(
                        code='OAP',
                        description='Senior citizen rate',
                        price_band_code='A/pool',
                        is_offer=False,
                        seatprice=17.0,
                        surcharge=2.0,
                        non_offer_seatprice=17.0,
                        non_offer_surcharge=2.0,
                        availability=6,
                        percentage_saving=0,
                        absolute_saving=0.0,
                    )
                ],
            ),
            PriceBand(
                code='B/pool',
                allows_leaving_single_seats='always',
                default_discount=Discount(
                    code='',
                    description='',
                    price_band_code='B/pool',
                    is_offer=False,
                    seatprice=18.0,
                    surcharge=3.0,
                    non_offer_seatprice=18.0,
                    non_offer_surcharge=3.0,
                    availability=3,
                    percentage_saving=0,
                    absolute_saving=0.0,
                ),
                example_seats_are_real=True,
                discounts=[
                    Discount(
                        code='ADULT',
                        description='Adult',
                        price_band_code='B/pool',
                        is_offer=False,
                        seatprice=18.0,
                        surcharge=3.0,
                        non_offer_seatprice=18.0,
                        non_offer_surcharge=3.0,
                        availability=3,
                        percentage_saving=0,
                        absolute_saving=0.0,
                    ),
                    Discount(
                        code='CHILD',
                        description='Child rate',
                        price_band_code='B/pool',
                        is_offer=False,
                        seatprice=9.0,
                        surcharge=2.0,
                        non_offer_seatprice=9.0,
                        non_offer_surcharge=2.0,
                        availability=3,
                        percentage_saving=0,
                        absolute_saving=0.0,
                    )
                ],
            )
        ],
    ),
    TicketType(
        code='BALCONY',
        description='Balcony',
        price_bands=[
            PriceBand(
                code='A/pool',
                allows_leaving_single_seats='always',
                default_discount=Discount(
                    code='',
                    description='',
                    price_band_code='A/pool',
                    is_offer=False,
                    seatprice=47.0,
                    surcharge=5.0,
                    non_offer_seatprice=47.0,
                    non_offer_surcharge=5.0,
                    availability=6,
                    percentage_saving=0,
                    absolute_saving=0.0,
                ),
                example_seats_are_real=True,
                discounts=[
                    Discount(
                        code='ADULT',
                        description='Adult',
                        price_band_code='A/pool',
                        is_offer=False,
                        seatprice=47.0,
                        surcharge=5.0,
                        non_offer_seatprice=47.0,
                        non_offer_surcharge=5.0,
                        availability=6,
                        percentage_saving=0,
                        absolute_saving=0.0,
                    ),
                    Discount(
                        code='CHILD',
                        description='Child rate',
                        price_band_code='A/pool',
                        is_offer=False,
                        seatprice=24.0,
                        surcharge=4.0,
                        non_offer_seatprice=24.0,
                        non_offer_surcharge=4.0,
                        availability=6,
                        percentage_saving=0,
                        absolute_saving=0.0,
                    ),
                    Discount(
                        code='STUDENT',
                        description='Student rate',
                        price_band_code='A/pool',
                        is_offer=False,
                        seatprice=35.0,
                        surcharge=4.0,
                        non_offer_seatprice=35.0,
                        non_offer_surcharge=4.0,
                        availability=6,
                        percentage_saving=0,
                        absolute_saving=0.0,
                    ),
                    Discount(
                        code='OAP',
                        description='Senior citizen rate',
                        price_band_code='A/pool',
                        is_offer=False,
                        seatprice=38.0,
                        surcharge=4.0,
                        non_offer_seatprice=38.0,
                        non_offer_surcharge=4.0,
                        availability=6,
                        percentage_saving=0,
                        absolute_saving=0.0,
                    )
                ],
            )
        ],
    )
]
```

The response includes a `possible_discounts` dictionary, that contains a list of
discounts (or concessions) that are available on each price band. Each discount
contains the same price band attributes that are returned in
[availability](#availability).
