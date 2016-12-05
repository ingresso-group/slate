# Shopping Trolley / Basket

TODO: 

- send method may be complex to use?

In order to reserve and purchase tickets for multiple events in a single transaction, you first need to add them to a shopping trolley.

It is important to note that because items are added to a shopping trolley *before* they are reserved, the first item added to the trolley is less likely to still be available when you reserve the trolley. So trolleys have the benefit that you can cross-sell extra items to the customer, but they have the downside that it is less likely that the customer's selected seats will still be available if you allow the customer to browse and book additional events. This trade-off is for each partner to weigh up. Note that if you do not allow customers to select seats this is less of a concern.

This section describes:

- The [shopping trolley object](#shopping-trolley-object)
- The API call to [manage the shopping trolley](#manage-the-shopping-trolley)

## Shopping Trolley Object

> **Example response**

```json
{
  "trolley_contents": {
    "bundle": [
      {
        "bundle_order_count": 1,
        "bundle_total_cost": 76.5,
        "bundle_total_seatprice": 75,
        "bundle_total_send_cost": 1.5,
        "bundle_total_surcharge": 0,
        "currency": {
          "currency_code": "gbp",
          "currency_factor": 100,
          "currency_number": 826,
          "currency_places": 2,
          "currency_post_symbol": "",
          "currency_pre_symbol": "£"
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
              "date_range_end": {
                "date_desc": "Fri, 17th March 2017",
                "iso8601_date_and_time": "2017-03-17T19:30:00Z"
              },
              "date_range_start": {
                "date_desc": "Fri, 18th November 2016",
                "iso8601_date_and_time": "2016-11-18T19:30:00Z"
              },
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
            "price_band_code": "C",
            "seat_request_status": "not_requested",
            "ticket_orders": {
              "ticket_order": [
                {
                  "discount_code": "",
                  "discount_desc": "",
                  "discount_disallowed_seat_no_bitmask": 0,
                  "no_of_seats": 3,
                  "sale_seatprice": 25,
                  "sale_surcharge": 0,
                  "total_sale_seatprice": 75,
                  "total_sale_surcharge": 0
                }
              ]
            },
            "ticket_type_code": "CIRCLE",
            "ticket_type_desc": "Upper circle",
            "total_no_of_seats": 3,
            "total_sale_seatprice": 75,
            "total_sale_surcharge": 0
          }
        ],
        "source_desc": "External Test Backend 0"
      },
      {
        "bundle_order_count": 1,
        "bundle_total_cost": 60,
        "bundle_total_seatprice": 60,
        "bundle_total_send_cost": 0,
        "bundle_total_surcharge": 0,
        "currency": {
          "currency_code": "gbp",
          "currency_factor": 100,
          "currency_number": 826,
          "currency_places": 2,
          "currency_post_symbol": "",
          "currency_pre_symbol": "£"
        },
        "order": [
          {
            "event": {
              "class": [
                {
                  "class_desc": "Attractions"
                }
              ],
              "country_code": "uk",
              "country_desc": "United Kingdom",
              "custom_filter": [],
              "date_range_end": {
                "date_desc": "Mon, 1st January 2018",
                "iso8601_date_and_time": "2018-01-01T20:00:00Z"
              },
              "date_range_start": {
                "date_desc": "Thu, 17th November 2016",
                "iso8601_date_and_time": "2016-11-17T20:00:00Z"
              },
              "event_desc": "Test Event - Type 1",
              "event_id": "3CVA",
              "event_path": "/3CVA-test-event-type-1/",
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
              "source_desc": "Ingresso",
              "venue_desc": "Belfast Tours Ltd"
            },
            "item_number": 2,
            "performance": {
              "date_desc": "Thu, 29th December 2016",
              "event_id": "3CVA",
              "has_pool_seats": true,
              "is_ghost": false,
              "is_limited": false,
              "iso8601_date_and_time": "2016-12-29T20:00:00Z",
              "perf_id": "3CVA-89",
              "time_desc": "8.00 PM"
            },
            "price_band_code": "C",
            "requested_seats": {
              "id_details": [
                {
                  "col_id": "18",
                  "full_id": "Z18",
                  "is_restricted_view": false,
                  "row_id": "Z",
                  "seat_subdata": "23/18",
                  "seat_text_code": "",
                  "separator": ""
                },
                {
                  "col_id": "19",
                  "full_id": "Z19",
                  "is_restricted_view": false,
                  "row_id": "Z",
                  "seat_subdata": "23/19",
                  "seat_text_code": "",
                  "separator": ""
                },
                {
                  "col_id": "20",
                  "full_id": "Z20",
                  "is_restricted_view": false,
                  "row_id": "Z",
                  "seat_subdata": "23/20",
                  "seat_text_code": "",
                  "separator": ""
                }
              ]
            },
            "seat_request_status": "got_none",
            "ticket_orders": {
              "ticket_order": [
                {
                  "discount_code": "RED/RED/1",
                  "discount_desc": "FULL PRICE",
                  "discount_disallowed_seat_no_bitmask": 0,
                  "no_of_seats": 3,
                  "sale_seatprice": 20,
                  "sale_surcharge": 0,
                  "total_sale_seatprice": 60,
                  "total_sale_surcharge": 0
                }
              ]
            },
            "ticket_type_code": "STD",
            "ticket_type_desc": "Standard",
            "total_no_of_seats": 3,
            "total_sale_seatprice": 60,
            "total_sale_surcharge": 0
          }
        ],
        "source_desc": "Ingresso"
      }
    ],
    "trolley_bundle_count": 2,
    "trolley_order_count": 2
  },
  "trolley_token": "U5--wXh8KU2zJEmsQOrA048HELaZi7bEWnqtODeghgpdzYzQBwQUxsdK1qIYzz-Ig1mEyKliGezJJ1HuIn_2Z5K8MaiQNI96-peHlOxqEGmIVYMTAarhcD45sVGapgESO8v9_qdtAB7g3_qOSB6s7olEgMRRILIxKX04BNJjm8Uyhh5_F1kTPvv83MwGUOE9YOUIDjM2Py5lOA0JC1gj2Gl38bhQ0IuwHYTvZ7G8IL-WqiW76wn8kgexMAQcWBG5zrMdMb88vpvFYL0rgOI_CC8sqe1JhXn82c_3lI-rfB4GgYUx9kyrZLjYKbWvRX_lhSMKbiiyjpaHaA2hupZGLC7KAOS1I8wD97lcyPwiqBugDTVIBgI_k-klz-QSVLnE2yEYHpKTFEnQJEl18BaFcVQERHYsThHQ2vrilZ-fAWhFS4fXyZ2xRtWOGPNjvtQNLTrTwgyhbCNKzk5cDngwYJD05DeRLm6EGIWu6PhNpr7j-4Wkba1tIwueYAiH43cUiVMV7NLS_QMi2gfmYGtxHvB8P4pmZCexDrbxlZt1hQgdHACdk7PaM_yLFPahOkFAtfFNDqL7eZQht95qkRxavi8Ug2WLHmdDPKHs51USVwvTGC9yMrSlis2kbxc13Epu7hTCZ"
}
```


The trolley returns several nested objects:

--> `trolley`

-----> `trolley_contents`

--------> `bundle`

-----------> `order`

--------------> `ticket_order`

A `bundle` represents all orders from a particular supplier. Orders are grouped like this so that all items are ordered from the supplier at once - if the supplier posts tickets on our behalf then separate items will be posted together, and if the supplier processes payment all items in the bundle will be paid for with a single payment. Reserving and purchasing items will also succeed or fail together. 

An `order` represents a number of tickets to a particular `price band` and `ticket type`, for a particular [performance](#performance-object) of an [event](#event-object).

An `order` will contain multiple `ticket_order`s when more than one `discount code` is used (for example ticket 1 and 2 have the ADULT discount code, and tickets 3 and 4 have the CHILD discount code). In this example there will be two `ticket_order`s: one for ADULT and one for CHILD.


**`trolley` attributes:**

Attribute | Description
--------- | -----------
`trolley_contents` | See below for object detail.
`trolley_token` | The identifier for the trolley (this is an internal hash representing the items in the trolley, so the identifier will be the same for identical trolleys).


**`trolley_contents` attributes:**

Attribute | Description
--------- | -----------
`bundle` | See below for object detail.
`trolley_bundle_count` | The number of bundles in the trolley.
`trolley_order_count` | The number of orders in the trolley. There will be at least one order for every bundle.


**`bundle` attributes** 

Attribute | Description
--------- | -----------
`bundle_order_count` | The number of orders in this bundle.
`bundle_total_cost` | The total cost of this bundle.
`bundle_total_seatprice` | The total face value for this bundle.
`bundle_total_send_cost` | The despatch cost for this bundle (similar to a transaction fee). Items within the same bundle will be sent together.
`bundle_total_surcharge` | The total booking fee for this bundle.
`currency` | The [currency](#currency-object) of the bundle.
`order` | See below for object detail.
`source_desc` | The name of the supplier system that this bundle will be purchased from.


**`order` attributes:**

Attribute | Description
--------- | -----------
`event` | The [event](#event-object) for this order.
`item_number` | This remains constant as items are added or removed. 
`performance` | The [performance](#performance-object) for this order.
`price_band_code` | The code for a price band. To uniquely identify a price band you should take the combination of `ticket_type_code`
`seat_request_status` | The status of your tickets after they have been reserved. Possible values are `not_requested` (specific seats not requested), `got_none` (you requested A13 and A14 but we gave you A15 and A16), `got_partial` (you requested A13 and A14 but we gave you A14 and A15), `got_all` (you requested A13 and A14 and you got A13 and A14 - by far the most common response when requesting specific seats). 
`ticket_orders` | A number of ticket_order objects, details below.
`ticket_type_code` | The unique identifier for the ticket type. For seated events this refers to a part of house / seating area such as Grand Circle.
`ticket_type_desc` | The description for the ticket type. This should be displayed to the customer
`total_no_of_seats` | The number of seats for this order.
`total_sale_seatprice` | The total face value for this order.
`total_sale_surcharge` | The total booking fee for this order.


**`ticket_order` attributes:**

Attribute | Description
--------- | -----------
`discount_code` | The discount code that applies to this ticket_order.
`discount_desc` | The description for the discount code.
`discount_disallowed_seat_no_bitmask` | (TODO: what is this)
`no_of_seats` | The number of tickets in this ticket_order.
`sale_seatprice` | The face value per ticket in this ticket_order.
`sale_surcharge` | The booking fee per ticket in this ticket_order.
`total_sale_seatprice` | The total face value of all tickets in this ticket_order.
`total_sale_surcharge` | The total booking fee of all tickets in this ticket_order.


## Manage the Shopping Trolley

> **Definition**

```
GET https://api.ticketswitch.com/cgi-bin/json_trolley.exe/{username}?user_passwd={password}&perf_id={perfid}&ticket_type_code={tickettype}&price_band_code={priceband}&no_of_seats={numtickets}
```

This call is used for the following use cases:

- Add best available tickets to a new or existing shopping trolley
- Add specific seats to a new or existing shopping trolley
- Remove items from the trolley

Note that adding an order will remove anything in the trolley which would prevent it being added. The common reasons why two orders cannot sit alongside each other are:

- We currently don't allow a basket to contain multiple orders for the same performance. This is something we plan to change, if this is a problem for you please let us know.
- Incompatible despatch methods for the same supplier. For example if you have specified "International Post" for one item, and "Collect From Box Office" for the next item, and they are both from the same supplier then these are incompatible and the first item will be removed from your trolley. This should be an uncommon occurence.
- Differing currencies - for example the first item added to the trolley uses the USD currency while the second item added uses the GBP currency. These are incompatible so the first item will be removed from your trolley.


> **Example request - adding best available tickets to a new trolley, specifying specific discount codes**

```shell
curl https://api.ticketswitch.com/cgi-bin/json_trolley.exe/demo \
        -d "user_passwd=demopass" \
        -d "perf_id=6IF-A7N" \
        -d "ticket_type_code=CIRCLE" \
        -d "price_band_code=C" \
        -d "no_of_seats=3" \
        -d "disc0=ADULT" \
        -d "disc1=CHILD" \
        -d "disc2=CHILD" \
        -G
```

> **Example request - adding specific seats to an existing trolley**

```shell
curl https://api.ticketswitch.com/cgi-bin/json_trolley.exe/demo \
        -d "user_passwd=demopass" \
        -d "trolley_token=k---bTur8DtD3TFVZR3ByQT-6tp2nUNjEM-Ecu16DM2wRLeKDpVncGguCbLv8Rqliw7CY" \
        -d "perf_id=3CVA-89" \
        -d "ticket_type_code=STD" \
        -d "price_band_code=C" \
        -d "no_of_seats=3" \
        -d "seat0=Z18" \
        -d "seat1=Z19" \
        -d "seat2=Z20" \
        -G
```

> **Example request - removing three orders from the trolley**

```shell
curl https://api.ticketswitch.com/cgi-bin/json_trolley.exe/demo \
        -d "user_passwd=demopass" \
        -d "trolley_token=k---bTur8DtD3TFVZR3ByQT-6tp2nUNjEM-Ecu16DM2wRLeKDpVncGguCbLv8Rqliw7CY" \
        -d "remove_items_list=2" \
        -G
```

> **Example request - view the current state of the trolley**

```shell
curl https://api.ticketswitch.com/cgi-bin/json_trolley.exe/demo \
        -d "user_passwd=demopass" \
        -d "trolley_token=k---bTur8DtD3TFVZR3ByQT-6tp2nUNjEM-Ecu16DM2wRLeKDpVncGguCbLv8Rqliw7CY" \
        -G
```

### Request

Parameter | Description
--------- | -----------
`discX` | Specify a discount code for ticket number X, with zero-based numbering (so to specify the CHILD discount code on the second ticket use `disc1=CHILD`). Note that illegal discount codes are replaced with legal ones.
`no_of_seats` | The number of tickets you want to add to the trolley.
`perf_id` | The performance identifier for the tickets that you want to add to your trolley.
`price_band_code` | The price band identifier for the tickets that you want to add to your trolley.
`remove_items_list` | A comma separated list of order `item_number`s that you want to remove.
`seatX` | Specify a specific seat for ticket number X, with zero-based numbering (so to specify seat A12 as the first ticket use `seat0=A12`). If seat numbers are not specified then when the trolley is later reserved you will receive best available seats.
`X_send_code` | Specify a send / despatch method for supplier system X. If this is not present it will default to the first send method. For example, to specify the POST send method for the nimax supplier system, use `nimax_send_code=POST`.
`ticket_type_code` | The ticket type identifier for the tickets that you want to add to your trolley.
`trolley_token` | The identifier for the trolley. This is used to add additional items to a trolley, or to reserve the items in a trolley.


Additional parameters, primarily for internal use:

Parameter | Description
--------- | -----------
`add_crypto_block` | `cypto_block`s are used in the old [Ingresso XML API](http://www.ingresso.co.uk/apidocs/). To make migration away from the XML API easier, if you include `add_crypto_block` a `crypto_block` will be added to the response. You can then take the crypto_block and trolley_token and pass these in to the XML API call make_reservation to reserve and later purchase tickets (so that the JSON API is used up to the basket stage and the XML API is used to complete the purchase).
`departure_date` | Specify a departure date for example `departure_date=20170214`.
`duration` | Specify a duration for hotel product, for example `duration=3`.
`promo_code` | Specifying a promo codes can unlock a special offer discount code. If you have a promo code it can be specified like this: `promo_code=FOO`. Note that this feature is not commonly used with partners.


### Response

Returns a [shopping trolley object](#shopping-trolley-object).