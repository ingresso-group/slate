# Trolley

> **Definition**

```
GET https://demo.ticketswitch.com/f13/trolley.v1
```

A `trolley` allows multiple orders to be purchased in a single transaction.

If you only plan to purchase a single order at a time then there is no need to
use a trolley - you should instead go straight to [reserve](#reserve).

The trolley itself should be considered a wish list. No tickets are reserved
until the [reserve](#reserve) call is made. Having a ticket in a trolley is not
a guarantee that the ticket will still be available when you attempt to reserve
the tickets.

Trolleys are identified by a `trolley_token`, which is simply a hash of the
orders in the trolley. Every time a change is made to the trolley the hash
changes and so does the `trolley_token`. An individual `trolley_token` is
therefore an identifier for the current state of your trolley, and can not be
used to identify your trolley as you add or remove orders.


### Request

> **Example request - adding best available tickets to a new trolley, specifying specific discount codes**

```shell
curl https://demo.ticketswitch.com/f13/trolley.v1 \
    -u "demo:demopass" \
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
trolley = client.get_trolley(
    performance_id='6IF-A8N',
    number_of_seats=2,
    price_band_code='A/pool',
    ticket_type_code='STALLS'
)
```



> **Example request - adding specific seats to an existing trolley**

```shell
curl https://demo.ticketswitch.com/f13/trolley.v1 \
    -u "demo:demopass" \
    -d "trolley_token=U3--tgaXeGJ3jY8-7bxaEyEcKhrbmYsrFkLAQh2wn89J9IFevORBYwlCveIJxP51WpqbA0tih8NmBuDvAegm37cFhVZA7yIGKjbEIetqsk2z_MGFMfiY9f11oM3MOpxlcLLE7vehHd5qyL4ozVZNtTOtxrJHhntmDqb-SyNhtAD0xWakXhUPlGVqQvnqQ2feu0moVJ_Wn9ROLz8C6p65Epz6kxGYBaNg0zkAawIttK3IY1LbiRxEHS10-R-7f0FVbg9kq0X6S8oIiWwjCqYJ9OEktXo-bdasYe4wu0yfki9N4WmuDrpTBgOkS7618nMbTvpYzPPacne8hGRCmH6e3GjPRtvZAilpaol5rKX3zgjN30cpLpsXpFo7ZZkJXOr5YMj-rNTXs0xg3trhrePeOdFFjTuN06iHrX2W2ylETgQ4hS2uXZoBVNmg4R8IQqvq4xuMmvRMYz2kXkC5wJF7pAG9J0--Z" \
    -d "perf_id=3CVB-22" \
    -d "ticket_type_code=UPP" \
    -d "price_band_code=C/pool" \
    -d "no_of_seats=2" \
    -d "seat0=C7" \
    -d "seat1=C8" \
    -G
```


> **Example request - view the current state of the trolley**

```shell
curl https://demo.ticketswitch.com/f13/trolley.v1 \
    -u "demo:demopass" \
    -d "trolley_token=M6--Th1yy_GMHYp_pxuZpgPYa43h-4JJKgwOsTKiSmME9f69ngTVVFnFtyBIDwWfhC59oV4RtGbj_t-hw_U75AHoSbNaxMHOWSzGFBZNceuo7AtApIckL-qbs3700lw2N9zTX12LLHVBSqhRpYEFc7twQ_k5BwieJeLLpHTM9LnB48-BbPT-0tBn9Ylq_a3Y3RHFXZChWiYmsdxsYRE-kgktxd_pdFyGTZNN_mazMGwQFxYQ99nUXVmRsRYeV29d9CKVI1fv6mR81iapKdYiEm1U0r8A5fmdCTFlLO8majLLI07ktEjXzgA63oOa5DoRHTOcD-U6gsOLied869nVXJQWkx6lvBr6InrLdEefg5sDK0WLVFNJ9WT9QsG4Y7opYcOdx6K6U0i7L9f88_d0iurc-FkpC2ils1M21OJY-8_eQvuw1SPqqvqcLqCpR6OnRRGA3vS1LIhtgavvtZ95MvQpBH3DgAwXJtiYdjsXmIlor2pqbfiaZfQXwuC1e8yoNUIBe_yBtsOQnweyoJfIyYOayiajXbOIblwwSiJEJCnDHAaE9jo9kQRu4NdDHNd-O5IoumIoxhj7NIjl6vIb_Klu1wzB7AjeNL4TNubgMMlThGO4TyNrXsww72M_fGQO2pXCkLwXGpLckCqTAUIrNKz7v6Rs-4X3TWaNIcaAbz-Zw5lIboJoPLRgBETn02GHx00gcI1-RT8zxI9-Z" \
    -G
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
trolley = client.get_trolley(
    token='E2--nPSMAQKI8kggWwF4N5qp4zPaO2VU_3armTREADiU6s4xgp3VjDmF7TXSnD0DN100souzOPZD8COW-jLhPSAsdOY-DhoK15T4meB5-xUrXpZ2cMrLIbVbongnwLqzJdI_-FFa6XqQTrF2ncjGLssB9nC7R79FHSN12AADiZ795WDj3vQ8GJ6DGvXjTf3-bULrCpjitgZLqMf_bAlqay7hSQxfCFaD1PiUtuU7gIhi7vnsr0CJT7vtJJmLd_HXfrwgQYTNey5dia6Tx9o4Ed3QAvwctSGBGVZ6g9qabWwRnTbBg8_TFuvY0vSvqPq31b30Dey_rLBKq4X1Ay2uNx3VJCs221wGGEvmlY5JoxhmKm5-Z',
    performance_id='6IF-A8D',
    number_of_seats=4,
    price_band_code='C/pool',
    ticket_type_code='CIRCLE',
)
```


> **Example request - removing the first order from the trolley**

```shell
curl https://demo.ticketswitch.com/f13/trolley.v1 \
    -u "demo:demopass" \
    -d "trolley_token=M6--Th1yy_GMHYp_pxuZpgPYa43h-4JJKgwOsTKiSmME9f69ngTVVFnFtyBIDwWfhC59oV4RtGbj_t-hw_U75AHoSbNaxMHOWSzGFBZNceuo7AtApIckL-qbs3700lw2N9zTX12LLHVBSqhRpYEFc7twQ_k5BwieJeLLpHTM9LnB48-BbPT-0tBn9Ylq_a3Y3RHFXZChWiYmsdxsYRE-kgktxd_pdFyGTZNN_mazMGwQFxYQ99nUXVmRsRYeV29d9CKVI1fv6mR81iapKdYiEm1U0r8A5fmdCTFlLO8majLLI07ktEjXzgA63oOa5DoRHTOcD-U6gsOLied869nVXJQWkx6lvBr6InrLdEefg5sDK0WLVFNJ9WT9QsG4Y7opYcOdx6K6U0i7L9f88_d0iurc-FkpC2ils1M21OJY-8_eQvuw1SPqqvqcLqCpR6OnRRGA3vS1LIhtgavvtZ95MvQpBH3DgAwXJtiYdjsXmIlor2pqbfiaZfQXwuC1e8yoNUIBe_yBtsOQnweyoJfIyYOayiajXbOIblwwSiJEJCnDHAaE9jo9kQRu4NdDHNd-O5IoumIoxhj7NIjl6vIb_Klu1wzB7AjeNL4TNubgMMlThGO4TyNrXsww72M_fGQO2pXCkLwXGpLckCqTAUIrNKz7v6Rs-4X3TWaNIcaAbz-Zw5lIboJoPLRgBETn02GHx00gcI1-RT8zxI9-Z" \
    -d "remove_items_list=1" \
    -G
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
trolley = client.get_trolley(
    token='E2--nPSMAQKI8kggWwF4N5qp4zPaO2VU_3armTREADiU6s4xgp3VjDmF7TXSnD0DN100souzOPZD8COW-jLhPSAsdOY-DhoK15T4meB5-xUrXpZ2cMrLIbVbongnwLqzJdI_-FFa6XqQTrF2ncjGLssB9nC7R79FHSN12AADiZ795WDj3vQ8GJ6DGvXjTf3-bULrCpjitgZLqMf_bAlqay7hSQxfCFaD1PiUtuU7gIhi7vnsr0CJT7vtJJmLd_HXfrwgQYTNey5dia6Tx9o4Ed3QAvwctSGBGVZ6g9qabWwRnTbBg8_TFuvY0vSvqPq31b30Dey_rLBKq4X1Ay2uNx3VJCs221wGGEvmlY5JoxhmKm5-Z',
    item_numbers_to_remove=[1, 2, 3],   
)
```

The single trolley call is used for multiple use cases such as:

- Add best available tickets to a new or existing trolley
- Add specific seats to a new or existing trolley
- View the items in the trolley
- Remove an order from the trolley

Note that adding an order will remove anything in the trolley which would
prevent it being added. Removed orders are listed as `discarded_orders`. The
common reasons why two orders cannot sit alongside each other are:

- We currently don't allow a basket to contain multiple orders for the same
  performance. This is something we plan to change - if this is a problem for 
  you please let us know.

- Incompatible despatch methods for the same supplier. For example if you have
  specified "International Post" for one order, and "Collect From Box Office" for
  the next order, and they are both from the same supplier then these are
  incompatible and the first order will be removed from your trolley. This should
  be an uncommon occurence.

- Differing currencies - for example the first order added to the trolley uses
  the USD currency while the second order added uses the GBP currency. These are
  incompatible so the first order will be removed from your trolley.

Also note that when adding individual seats to a trolley, there are restrictions
on the seats that can be added in a single order:

- If the [availability](#availability) response includes 
  `contiguous_seat_selection_only` = `false` then any seats can be selected, 
  provided they are within a single `ticket_type_code` and `price_band_code`.

- If the [availability](#availability) response includes 
  `contiguous_seat_selection_only` = `true` then only contiguous seats within a 
  single `ticket_type_code` and `price_band_code` can be selected. Seats are 
  contiguous based on the order of the id_details seat array.

If you would like to allow your customers to select seats without restriction
across price bands and ticket types, you need to add multiple orders to a
trolley, one order for each ticket type / price band. However there are
currently some restrictions enforced so if you want to do this you will need to
contact us first api@ingresso.co.uk.


Parameter | Description
--------- | -----------
`discX` | Specify a discount code for ticket number X, with zero-based numbering (so to specify the CHILD discount code on the second ticket use `disc1=CHILD`). Note that illegal discount codes are replaced with legal ones.
`no_of_seats` | The number of tickets you want to add to the trolley.
`perf_id` | The performance identifier for the tickets that you want to add to your trolley.
`price_band_code` | The price band identifier for the tickets that you want to add to your trolley.
`remove_items_list` | A comma separated list of order `item_number`s that were previously added to the trolley and that you now want to remove.
`req_currency_details` | Include full detail in the [currency object](#currency-object). Without this only the currency_code field is returned.
`seatX` | Specify a specific seat for ticket number X, with zero-based numbering (so to specify seat A12 as the first ticket use `seat0=A12`). If seat numbers are not specified then when the trolley is later reserved you will receive best available seats.
`X_send_code` | Specify a send / despatch method for supplier system X. If this is not present it will default to the first send method. For example, to specify the POST send method for the nimax supplier system, use `nimax_send_code=POST`.
`ticket_type_code` | The ticket type identifier for the tickets that you want to add to your trolley.
`trolley_token` | The identifier for the trolley. This is used to add additional orders to a trolley, or to remove orders in a trolley.


Additional parameters, primarily for internal use:

Parameter | Description
--------- | -----------
`add_crypto_block` | `cypto_block`s are used in the old [Ingresso XML API](http://www.ingresso.co.uk/apidocs/). To make migration away from the XML API easier, if you include `add_crypto_block` a `crypto_block` will be added to the response. You can then take the crypto_block and trolley_token and pass these in to the XML API call make_reservation to reserve and later purchase tickets (so that the JSON API is used up to the basket stage and the XML API is used to complete the purchase).
`departure_date` | Specify a departure date for example `departure_date=20170214`.
`duration` | Specify a duration for hotel product, for example `duration=3`.
`promo_code` | Specifying a promo codes can unlock a special offer discount code. If you have a promo code it can be specified like this: `promo_code=FOO`. Note that this feature is not commonly used with partners.

All of the following parameters can also be used to request additional data for the event and performance in each order. These are described in more detail in the Events section [here](#additional-parameters).

Parameter | Description
--------- | -----------
`req_avail_details` | Returns [availability details](#availability-details) - a cached list of unique ticket types and price bands available for the event and performance in each order.
`req_avail_details_with_perfs` | This will add the list of available performance dates to each avail detail object. *Only valid if used alongside req_avail_details*.
`req_cost_range` | Returns [cost ranges](#cost-range) - a from price and offer detail for the event and performance in each order.
`req_cost_range_best_value_offer` | Returns the offer with the highest percentage saving.
`req_cost_range_details` | Returns a list of unique ticket types and price bands and their cost ranges across all performances.
`req_cost_range_max_saving_offer` | Returns the offer with the highest absolute saving.
`req_cost_range_min_cost_offer` | Returns the offer with the lowest cost.
`req_cost_range_top_price_offer` | Returns the offer with the highest cost. This is the least used offer cost range.
`req_cost_range_no_singles_data` | This returns another cost range object that excludes availability with only 1 consecutive seat available. The prices in this cost range will therefore be the same or higher than the outer cost range. It has the same structure as the main cost range (so if you want to see the "best value offer" in the no singles data, you need to add `req_cost_range_best_value_offer` and you will see this data in both cost ranges).
`req_extra_info` | Returns the [descriptive info](#extra-info) for the event, returned as individual sections (`structured_info`) or as a single summary (`event_info` / `event_info_html`).
`req_media_triplet_one` | Triplet one (jpg/png 520x390). [See further detail on media](#media).
`req_media_triplet_two` | Triplet two if available (jpg/png 520x390).
`req_media_triplet_three` | Triplet three if available (jpg/png 520x390).
`req_media_triplet_four` | Triplet four if available (jpg/png 520x390).
`req_media_triplet_five` | Triplet five if available (jpg/png 520x390).
`req_media_seating_plan` | Graphical seating plan of the venue if available (jpg/png varying size).
`req_media_square` | Small square image suitable for search or event avatar (jpg/png 140x140).
`req_media_landscape` | Small landscape banner suitable for search (jpg/png 220x115).
`req_media_marquee` | Large landscape banner suitable for a page heading, if available (jpg/png 700x300).
`req_media_supplier` | Logo of the supplier/producer, if available (jpg/png varying size).
`req_reviews` | Returns [event reviews](#reviews) if available.
`req_video_iframe` | Returns video iframe information if available.


### Response

> **Example response - adding specific seats to an existing trolley**

```shell
{
  "discarded_orders": [],
  "trolley_contents": {
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
            "seat_request_status": "got_none",
            "ticket_orders": {
              "ticket_order": [
                {
                  "discount_code": "RED/RED/1",
                  "discount_desc": "FULL PRICE",
                  "discount_disallowed_seat_no_bitmask": 0,
                  "no_of_seats": 2,
                  "sale_seatprice": 20,
                  "sale_surcharge": 0,
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
    "trolley_bundle_count": 2,
    "trolley_order_count": 2
  },
  "trolley_token": "M6--Th1yy_GMHYp_pxuZpgPYa43h-4JJKgwOsTKiSmME9f69ngTVVFnFtyBIDwWfhC59oV4RtGbj_t-hw_U75AHoSbNaxMHOWSzGFBZNceuo7AtApIckL-qbs3700lw2N9zTX12LLHVBSqhRpYEFc7twQ_k5BwieJeLLpHTM9LnB48-BbPT-0tBn9Ylq_a3Y3RHFXZChWiYmsdxsYRE-kgktxd_pdFyGTZNN_mazMGwQFxYQ99nUXVmRsRYeV29d9CKVI1fv6mR81iapKdYiEm1U0r8A5fmdCTFlLO8majLLI07ktEjXzgA63oOa5DoRHTOcD-U6gsOLied869nVXJQWkx6lvBr6InrLdEefg5sDK0WLVFNJ9WT9QsG4Y7opYcOdx6K6U0i7L9f88_d0iurc-FkpC2ils1M21OJY-8_eQvuw1SPqqvqcLqCpR6OnRRGA3vS1LIhtgavvtZ95MvQpBH3DgAwXJtiYdjsXmIlor2pqbfiaZfQXwuC1e8yoNUIBe_yBtsOQnweyoJfIyYOayiajXbOIblwwSiJEJCnDHAaE9jo9kQRu4NdDHNd-O5IoumIoxhj7NIjl6vIb_Klu1wzB7AjeNL4TNubgMMlThGO4TyNrXsww72M_fGQO2pXCkLwXGpLckCqTAUIrNKz7v6Rs-4X3TWaNIcaAbz-Zw5lIboJoPLRgBETn02GHx00gcI1-RT8zxI9-Z"
}
```

The trolley has three main structures:

* A **`bundle`** represents all orders from  a particular supplier. Orders are
grouped like this so that all items are ordered from the  supplier at once - if
the supplier posts tickets on our behalf then separate orders will be posted
together, and if the supplier processes payment all orders in the bundle will be
paid for with a single payment. Reserving and purchasing orders will also succeed
or fail together.

* An **`order`** represents a number of tickets to a particular `price band` and
`ticket type`, for a particular [performance](#performances) of an
[event](#events).

* An `order` will contain multiple **`ticket_order`**s when more than one
`discount code` is used (for example ticket 1 and 2 have the ADULT discount
code, and tickets 3 and 4 have the CHILD discount code). In this example there
will be two `ticket_order`s: one for ADULT and one for CHILD.



**`trolley` attributes:**

Attribute | Description
--------- | -----------
`discarded_orders` | As discussed above, adding an order will remove anything in the trolley which would prevent it being added. See below for detail of the `order` object.
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
`bundle_source_code` | The code of the supplier system that this bundle will be purchased from.
`bundle_source_desc` | The name of the supplier system that this bundle will be purchased from.
`bundle_total_cost` | The total cost of this bundle.
`bundle_total_seatprice` | The total face value for this bundle.
`bundle_total_send_cost` | The despatch cost for this bundle (similar to a transaction fee). Orders within the same bundle will be sent together.
`bundle_total_surcharge` | The total booking fee for this bundle.
`currency` | The [currency](#currency-object) of the bundle, which by default just contains the currency_code. To include full currency detail add the parameter `req_currency_details`.
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