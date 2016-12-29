# Reserve
> **Definition**

```
POST https://api.ticketswitch.com/f13/reserve.v1/{username}?user_passwd={password}
```

Before purchasing tickets, you must first `reserve` them. Reserving tickets will
put them on hold for a period of time, ensuring they cannot be purchased by
anyone else until you explicitly [release](#release) them or until the hold time
expires when the tickets are automatically released. Reserve is typically called
immediately before displaying a checkout page to customers to collect contact 
details. These details are then used as parameters when
[purchasing the reservation](#purchase).

There are two main use cases for calling `reserve`:

1. **You only want to purchase a single order at a time**: call `reserve` with a
`perf_id`, `ticket_type_code`, `price_band_code`, `no_of_seats`, and other
optional parameters if necessary.

2. **You want to purchase multiple orders in a single transaction**: first add 
orders to your [trolley](#trolley), then `reserve` the trolley by
specifying a `trolley_token`.

(TODO: check how to post via CURL???)

### Request

> **Example request - reserving best available tickets, specifying specific discount codes**

```shell
curl https://api.ticketswitch.com/f13/reserve.v1/demo \
        -d "user_passwd=demopass" \
        -d "perf_id=6IF-A7N" \
        -d "ticket_type_code=CIRCLE" \
        -d "price_band_code=C/pool" \
        -d "no_of_seats=3" \
        -d "disc0=ADULT" \
        -d "disc1=CHILD" \
        -d "disc2=CHILD" \
        -G
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
TODO: FINISH ME NIC
```



> **Example request - reserving specific seats**

```shell
curl https://api.ticketswitch.com/f13/reserve.v1/demo \
        -d "user_passwd=demopass" \
        -d "perf_id=3CVB-22" \
        -d "ticket_type_code=UPP" \
        -d "price_band_code=C/pool" \
        -d "no_of_seats=2" \
        -d "seat0=C7" \
        -d "seat1=C8" \
        -G
```

> **Example request - reserving orders previously added to a trolley**

```shell
curl https://api.ticketswitch.com/f13/reserve.v1/demo \
        -d "user_passwd=demopass" \
        -d "trolley_token=M6--Th1yy_GMHYp_pxuZpgPYa43h-4JJKgwOsTKiSmME9f69ngTVVFnFtyBIDwWfhC59oV4RtGbj_t-hw_U75AHoSbNaxMHOWSzGFBZNceuo7AtApIckL-qbs3700lw2N9zTX12LLHVBSqhRpYEFc7twQ_k5BwieJeLLpHTM9LnB48-BbPT-0tBn9Ylq_a3Y3RHFXZChWiYmsdxsYRE-kgktxd_pdFyGTZNN_mazMGwQFxYQ99nUXVmRsRYeV29d9CKVI1fv6mR81iapKdYiEm1U0r8A5fmdCTFlLO8majLLI07ktEjXzgA63oOa5DoRHTOcD-U6gsOLied869nVXJQWkx6lvBr6InrLdEefg5sDK0WLVFNJ9WT9QsG4Y7opYcOdx6K6U0i7L9f88_d0iurc-FkpC2ils1M21OJY-8_eQvuw1SPqqvqcLqCpR6OnRRGA3vS1LIhtgavvtZ95MvQpBH3DgAwXJtiYdjsXmIlor2pqbfiaZfQXwuC1e8yoNUIBe_yBtsOQnweyoJfIyYOayiajXbOIblwwSiJEJCnDHAaE9jo9kQRu4NdDHNd-O5IoumIoxhj7NIjl6vIb_Klu1wzB7AjeNL4TNubgMMlThGO4TyNrXsww72M_fGQO2pXCkLwXGpLckCqTAUIrNKz7v6Rs-4X3TWaNIcaAbz-Zw5lIboJoPLRgBETn02GHx00gcI1-RT8zxI9-Z" \
        -G
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
TODO: FINISH ME NIC
```

Note that the request parameters and response attributes are similar to
[trolley](#trolley).


Parameter | Description
--------- | -----------
`discX` | Specify a discount code for ticket number X, with zero-based numbering (so to specify the CHILD discount code on the second ticket use `disc1=CHILD`). Note that illegal discount codes are replaced with legal ones.
`no_of_seats` | The number of tickets you want to reserve.
`perf_id` | The performance identifier for the tickets that you want to reserve.
`price_band_code` | The price band identifier for the tickets that you want to reserve.
`remove_items_list` | A comma separated list of order `item_number`s that you want to remove from the trolley before reserving.
`req_currency_details` | Include full detail in the [currency object](#currency-object). Without this only the currency_code field is returned.
`seatX` | Specify a specific seat for ticket number X, with zero-based numbering (so to specify seat A12 as the first ticket use `seat0=A12`). If seat numbers are not specified then you will receive best available seats.
`X_send_code` | Specify a send / despatch method for supplier system X. If this is not present it will default to the first send method. For example, to specify the POST send method for the nimax supplier system, use `nimax_send_code=POST`.
`ticket_type_code` | The ticket type identifier for the tickets that you want to add to your trolley.
`trolley_token` | The identifier for a trolley. This is used to reserve multiple items at once.


Additional parameters, primarily for internal use:

Parameter | Description
--------- | -----------
`departure_date` | Specify a departure date for example `departure_date=20170214`.
`duration` | Specify a duration for hotel product, for example `duration=3`.
`promo_code` | Specifying a promo codes can unlock a special offer discount code. If you have a promo code it can be specified like this: `promo_code=FOO`. Note that this feature is not commonly used with partners.


### Response

> **Example response - reserving orders previously added to a trolley**

```shell
{
  "allowed_countries": {
    "ad": "Andorra",
    "ae": "United Arab Emirates",
    "af": "Afghanistan",
    "ag": "Antigua and Barbuda",
    "uk": "United Kingdom",
    "um": "The United States Minor Outlying Islands",
    "us": "United States of America",
    "uy": "Uruguay"
  },
  "can_edit_address": true,
  "needs_agent_reference": false,
  "needs_email_address": false,
  "needs_payment_card": false,
  "prefilled_address": {
    "address_line_one": "",
    "address_line_two": "",
    "country_code": "uk",
    "county": "",
    "email_address": "",
    "home_phone": "",
    "postcode": "",
    "town": "",
    "work_phone": ""
  },
  "reserved_trolley": {
    "bundle": [
      {
        "bundle_order_count": 1,
        "bundle_source_code": "ext_test0",
        "bundle_source_desc": "External Test Backend 0",
        "bundle_total_cost": 52.5,
        "bundle_total_seatprice": 51,
        "bundle_total_send_cost": 1.5,
        "bundle_total_surcharge": 0,
        "currency": {
          "currency_code": "gbp"
        },
        "order": [
          {
            "event": {
              "city_desc": "London",
              "class": [
                {
                  "class_desc": "Ballet & Dance"
                }
              ],
              "country_code": "uk",
              "country_desc": "United Kingdom",
              "critic_review_percent": 100,
              "custom_filter": [],
              "event_desc": "Matthew Bourne's Nutcracker TEST",
              "event_id": "6IF",
              "event_path": "/6IF-matthew-bourne-s-nutcracker-test/",
              "event_status": "live",
              "event_type": "simple_ticket",
              "event_upsell_list": {
                "event_id": [
                  "6IE",
                  "MH0"
                ]
              },
              "geo_data": {
                "latitude": 51.52961137,
                "longitude": -0.10601562
              },
              "has_no_perfs": false,
              "is_seated": true,
              "max_running_time": 120,
              "min_running_time": 120,
              "need_departure_date": false,
              "need_duration": false,
              "need_performance": true,
              "postcode": "EC1R 4TN",
              "show_perf_time": true,
              "source_code": "ext_test0",
              "source_desc": "External Test Backend 0",
              "user_review_percent": 100,
              "venue_desc": "Sadler's Wells"
            },
            "item_number": 1,
            "performance": {
              "date_desc": "Sun, 15th January 2017",
              "event_id": "6IF",
              "has_pool_seats": true,
              "is_ghost": false,
              "is_limited": false,
              "iso8601_date_and_time": "2017-01-15T19:30:00Z",
              "perf_id": "6IF-A7N",
              "running_time": 120,
              "time_desc": "7.30 PM"
            },
            "price_band_code": "C/pool",
            "seat_request_status": "not_requested",
            "ticket_orders": {
              "ticket_order": [
                {
                  "discount_code": "ADULT",
                  "discount_desc": "Adult standard",
                  "discount_disallowed_seat_no_bitmask": 0,
                  "no_of_seats": 1,
                  "sale_seatprice": 25,
                  "sale_surcharge": 0,
                  "seats": {
                    "id_details": [
                      {
                        "col_id": "361",
                        "full_id": "GQ361",
                        "is_restricted_view": false,
                        "row_id": "GQ",
                        "seat_text_code": "",
                        "separator": ""
                      }
                    ]
                  },
                  "total_sale_seatprice": 25,
                  "total_sale_surcharge": 0
                },
                {
                  "discount_code": "CHILD",
                  "discount_desc": "Child rate",
                  "discount_disallowed_seat_no_bitmask": 0,
                  "no_of_seats": 2,
                  "sale_seatprice": 13,
                  "sale_surcharge": 0,
                  "seats": {
                    "id_details": [
                      {
                        "col_id": "358",
                        "full_id": "GQ358",
                        "is_restricted_view": false,
                        "row_id": "GQ",
                        "seat_text_code": "",
                        "separator": ""
                      },
                      {
                        "col_id": "355",
                        "full_id": "GQ355",
                        "is_restricted_view": false,
                        "row_id": "GQ",
                        "seat_text_code": "",
                        "separator": ""
                      }
                    ]
                  },
                  "total_sale_seatprice": 26,
                  "total_sale_surcharge": 0
                }
              ]
            },
            "ticket_type_code": "CIRCLE",
            "ticket_type_desc": "Upper circle",
            "total_no_of_seats": 3,
            "total_sale_seatprice": 51,
            "total_sale_surcharge": 0
          }
        ]
      },
      {
        "bundle_order_count": 1,
        "bundle_source_code": "ingresso_one_test",
        "bundle_source_desc": "Ingresso",
        "bundle_total_cost": 40,
        "bundle_total_seatprice": 40,
        "bundle_total_send_cost": 0,
        "bundle_total_surcharge": 0,
        "currency": {
          "currency_code": "gbp"
        },
        "order": [
          {
            "event": {
              "class": [
                {
                  "class_desc": "Theatre"
                }
              ],
              "country_code": "uk",
              "country_desc": "United Kingdom",
              "custom_filter": [],
              "event_desc": "Test Event - Type 10 (d)",
              "event_id": "3CVB",
              "event_path": "/3CVB-test-event-type-10-d/",
              "event_status": "live",
              "event_type": "simple_ticket",
              "geo_data": {
                "latitude": 10,
                "longitude": 20
              },
              "has_no_perfs": false,
              "is_seated": true,
              "need_departure_date": false,
              "need_duration": false,
              "need_performance": true,
              "postcode": "BT1 2BE",
              "show_perf_time": true,
              "source_code": "ingresso_one_test",
              "source_desc": "Ingresso",
              "venue_desc": "Belfast Tours Ltd"
            },
            "item_number": 2,
            "performance": {
              "date_desc": "Fri, 10th February 2017",
              "event_id": "3CVB",
              "has_pool_seats": true,
              "is_ghost": false,
              "is_limited": false,
              "iso8601_date_and_time": "2017-02-10T20:00:00Z",
              "perf_id": "3CVB-22",
              "time_desc": "8.00 PM"
            },
            "price_band_code": "C/pool",
            "requested_seats": {
              "id_details": [
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
                }
              ]
            },
            "seat_request_status": "got_all",
            "ticket_orders": {
              "ticket_order": [
                {
                  "discount_code": "RED/RED/1",
                  "discount_desc": "FULL PRICE",
                  "discount_disallowed_seat_no_bitmask": 0,
                  "no_of_seats": 2,
                  "sale_seatprice": 20,
                  "sale_surcharge": 0,
                  "seats": {
                    "id_details": [
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
                      }
                    ]
                  },
                  "total_sale_seatprice": 40,
                  "total_sale_surcharge": 0
                }
              ]
            },
            "ticket_type_code": "UPP",
            "ticket_type_desc": "Upper Circle",
            "total_no_of_seats": 2,
            "total_sale_seatprice": 40,
            "total_sale_surcharge": 0
          }
        ]
      }
    ],
    "minutes_left_on_reserve": 15,
    "transaction_uuid": "0a50248e-cd0c-11e6-ae47-0025903268a2",
    "trolley_bundle_count": 2,
    "trolley_order_count": 2
  },
  "unreserved_orders": []
}
```

The response will include an identifier for the reserve, detail of the reserved
items, detail of items that weren't able to be reserved, and a set of flags 
that confirm the information you need to collect from your customer when 
purchasing tickets.

Ingresso's partners split into the following categories based on how they take
payment:

* **On-credit**: those who purchase on-credit, and are regularly invoiced by 
Ingresso.

* **Stripe**: those who exclusively use Stripe to take payment, either
into their own bank account (and are invoiced by Ingresso) or directly into
Ingresso's bank account.

* **Venue or other payment provider**: those who send payment direct to the
venue or to the payment provider specified by Ingresso.

Most partners are on-credit, some are Stripe-only, and very few are venue or
other payment provider - this option is not typically offered to partners.


Partners who wish to take payment via Stripe should note the following:

* The simplest option is to use Ingresso's Stripe account, however it is
possible to use your own. Once this has been agreed you can contact Stripe, and
provide Ingresso with your Stripe keys.

* We recommend that you use Stripe.js to collect card details on your own
checkout page and pass them directly to Stripe in a secure, PCI-compliant
manner. You should follow the [Stripe.js docs](https://stripe.com/docs/custom-form) 
to implement this. When passing card details to Stripe you will need to
include a publishable key - this will be returned by Ingresso via
bundle.debitor.debitor_integration_data.publishable_key.

* If there are no errors with the card details then Stripe will return a 
single-use Stripe token. You should then pass that token to Ingresso and we 
will ask Stripe to authorise payment. We will then purchase tickets from the 
supplier, and if that is successful we will ask Stripe to capture the payment, 
otherwise we refund the authorisation. This is taken care of by the Ingresso 
API. 
(TODO: Nic to help with more detail on what is passed where.)

* You will likely wish to give customers the option of entering a separate 
billing address if they are having tickets posted. 


Attribute | Description
--------- | -----------
`accepted_payment_cards` | For the vast majority of partners this will not be present and can be ignored (it won't be present for partners who purchase on-credit or who use Stripe). For partners who send payment direct to the venue, this field presents a list of payment card types.
`allowed_countries` | A list of country codes and human-readable descriptions. The country codes are [ISO 3166-1 alpha-2 codes](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2). This list is relevant when selecting a despatch method that is restricted to certain countries, for example a local post despatch method may only be available in the supplier's country. These are typically presented to customers in a dropdown list on a checkout page. The customer's country code is a required parameter when [purchasing](#purchase) tickets.
`can_edit_address` | Indicates whether it is possible to change the non-blank `prefilled_address` data. This is primarily for internal use and can be ignored for normal use cases. In most cases this will be `true`. If it is `false` the non-blank `prefilled_address` fields will be used when tickets are purchased and we will ignore these fields if they are passed to us. 
`needs_agent_reference` | For the vast majority of partners this will be `false` and can be ignored. However some partners ask to specify their own reference when purchasing tickets - if this is `true` then the reference must be passed in.
`needs_email_address` | If `true` you must pass in an email address when purchasing tickets.
`needs_payment_card` | This will be `false` for almost all partners (it doesn't apply to partners who purchase on-credit or use Stripe). If this is `true` you need to collect card details from the customer and include them when purchasing.
`prefilled_address` | Some partners will have an address already held in the Ingresso system which should be prefilled for the user (by default this will not be enabled for partners - you will need to explicitly request it). The non-blank fields below should be prefilled. `can_edit_address` indicates whether the user can edit any of the prefilled fields.
`prefilled_address.address_line_one` | The first line of the address - if this is non-blank it should be prefilled.
`prefilled_address.address_line_two` | The second line of the address - if this is non-blank it should be prefilled.
`prefilled_address.country_code` | The country code of the address - if this is non-blank it should be prefilled.
`prefilled_address.email_address` | The email address - if this is non-blank it should be prefilled.
`prefilled_address.home_phone` | The home phone - if this is non-blank it should be prefilled.
`prefilled_address.postcode` | The postcode / zip code - if this is non-blank it should be prefilled.
`prefilled_address.town` | The town - if this is non-blank it should be prefilled.
`prefilled_address.work_phone` | The work phone - if this is non-blank it should be prefilled.
`reserved_trolley` | See below for object detail.
`supports_billing_address` | For the vast majority of partners this can be ignored (it isn't relevant to partners who purchase on-credit or who use Stripe). When it is `true` a separate address may be provided when purchasing tickets, and this will be used to validate the payment card address for venue systems that perform address verification checks.
`unreserved_orders` | A list of orders that could not be reserved.


**`reserved_trolley` attributes:**

Attribute | Description
--------- | -----------
`bundle` | See below for object detail.
`minutes_left_on_reserve` | A numeric representation of the number of minutes that your reserve will be held open for. For example, 12.5 is 12 minutes and 30 seconds. (TODO: confirm this is correct)
`transaction_uuid` | The unique reference for this reservation. This is needed for any further interactions with the 
`trolley_bundle_count` | The number of bundles in the trolley.
`trolley_order_count` | The number of orders in the trolley. There will be at least one order for every bundle.

As explained in more detail in [trolley](#trolley), a trolley contains three 
nested objects:

* A **`bundle`** represents all orders from a particular supplier. 

* An **`order`** represents a number of tickets to a particular `price band` and
`ticket type`, for a particular [performance](#performances) of an
[event](#events).

* An `order` will contain multiple **`ticket_order`**s when more than one
`discount code` is used (for example ticket 1 and 2 have the ADULT discount
code, and tickets 3 and 4 have the CHILD discount code). In this example there
will be two `ticket_order`s: one for ADULT and one for CHILD.

If you only plan to `reserve` a single item at a time then a successful reserve
will contain a single `order` within a single `bundle`.

**`bundle` attributes** 

Attribute | Description
--------- | -----------
`bundle_order_count` | The number of orders in this bundle.
`bundle_source_code` | The code of the supplier system that this bundle will be purchased from.
`bundle_source_desc` | The name of the supplier system that this bundle will be purchased from.
`bundle_total_cost` | The total cost of this bundle.
`bundle_total_cost_in_desired` | The total cost of this bundle, converted to your `desired_currency`. This field will not be present for most partners.
`bundle_total_seatprice` | The total face value for this bundle.
`bundle_total_seatprice_in_desired` | The total face value for this bundle, converted to your `desired_currency`. This field will not be present for most partners.
`bundle_total_send_cost` | The despatch cost for this bundle (similar to a transaction fee). Orders within the same bundle will be sent together.
`bundle_total_send_cost_in_desired` | The despatch cost for this bundle, converted to your `desired_currency`. This field will not be present for most partners.
`bundle_total_surcharge` | The total booking fee for this bundle.
`bundle_total_surcharge_in_desired` | The total booking fee for this bundle, converted to your `desired_currency`. This field will not be present for most partners.
`currency` | The [currency](#currency-object) of the bundle, which by default just contains the currency_code. To include full currency detail add the parameter `req_currency_details`.
`desired_currency` | Most partners will not see a `desired_currency`, but it is possible for a partner to request to always view prices in a specific currency. For example if a product is supplied in GBP, but your `desired_currency` is set to USD, we will return prices in GBP along with `_desired` prices in USD. If you are taking payment using Ingresso's Stripe connection, then payment will be taken in the default currency (GBP, in the example), and if you are being invoiced by Ingresso we will invoice you in the default currency (GBP, in the example). So the `_desired` prices are typically used to just display an indicative amount to the customer (in USD, in the example). Currency conversion rates are taken from a daily European Central Bank feed. To include the full currency object detail add the parameter `req_currency_details`.
`order` | See below for object detail.


**`order` attributes:**

Attribute | Description
--------- | -----------
`event` | The [event](#event-object) for this order.
`item_number` | A unique sequential number for the `order`. The second order added to the trolley will have `item_number` 2. The `item_number` remains constant as orders are added or removed, so if `item_number` 1 is removed, the first order will be `item_number` 2.
`performance` | The [performance](#performance-object) for this order.
`price_band_code` | The code for a price band. To uniquely identify a price band you should take the combination of `ticket_type_code`
`seat_request_status` | The status of your tickets after they have been reserved. Possible values are `not_requested` (specific seats not requested), `got_none` (you requested A13 and A14 but we gave you A15 and A16), `got_partial` (you requested A13 and A14 but we gave you A14 and A15), `got_all` (you requested A13 and A14 and you got A13 and A14 - by far the most common response when requesting specific seats). 
`ticket_orders` | A number of ticket_order objects, details below.
`ticket_type_code` | The unique identifier for the ticket type. For seated events this refers to a part of house / seating area such as Grand Circle.
`ticket_type_desc` | The description for the ticket type. This should be displayed to the customer
`total_no_of_seats` | The number of seats for this order.
`total_sale_seatprice` | The total face value for this order.
`total_sale_seatprice_in_desired` | The total face value for this order, converted to your `desired_currency`. This field will not be present for most partners.
`total_sale_surcharge` | The total booking fee for this order.
`total_sale_surcharge_in_desired` | The total booking fee for this order, converted to your `desired_currency`. This field will not be present for most partners.


**`ticket_order` attributes:**

Attribute | Description
--------- | -----------
`discount_code` | The discount code that applies to this ticket_order.
`discount_desc` | The description for the discount code.
`discount_disallowed_seat_no_bitmask` | (TODO: what is this)
`no_of_seats` | The number of tickets in this ticket_order.
`sale_seatprice` | The face value per ticket in this ticket_order.
`sale_seatprice_in_desired` | The face value per ticket in this ticket_order, converted to your `desired_currency`. This field will not be present for most partners.
`sale_surcharge` | The booking fee per ticket in this ticket_order.
`sale_surcharge_in_desired` | The booking fee per ticket in this ticket_order, converted to your `desired_currency`. This field will not be present for most partners.
`seats` | For seated events, an array of reserved seats.
`seats.col_id` | The column identifier of the seat.
`seats.full_id` | The ID of the seat - comprised of the `col_id` and the `row_id`, sometimes with a separator between them.
`seats.is_restricted_view` | `true` if the seat is classified as having a restricted view.
`seats.row_id` | The row identifier of the seat.
`seats.seat_text` | A message about the seat that must be displayed to customers.
`seats.seat_text_code` | An identifier for the seat text (only unique within the current `bundle_source_code`). Not useful for most partners.
`total_sale_seatprice` | The total face value of all tickets in this ticket_order.
`total_sale_seatprice_in_desired` | The total face value of all tickets in this ticket_order, converted to your `desired_currency`. This field will not be present for most partners.
`total_sale_surcharge` | The total booking fee of all tickets in this ticket_order.
`total_sale_surcharge_in_desired` | The total booking fee of all tickets in this ticket_order, converted to your `desired_currency`. This field will not be present for most partners.