# Status
> **Definition**

```
GET https://demo.ticketswitch.com/f13/status.v1?transaction_uuid={trans_uuid}
```

This resource allows you to view the details and status of a transaction. A
transaction is created when tickets are [reserved](#reserve), and it changes
status if it is [purchased](#purchase) or [released](#release). Status can be
called on transactions in any state. An example use is to retrieve information
to display on a confirmation page.

### Request

> **Example request**

```shell
curl https://demo.ticketswitch.com/f13/status.v1 \
    -u "demo:demopass" \
    -d "transaction_uuid=284d9c3a-d698-11e6-be8c-002590326962" \
    -G
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
TODO: FINISH ME NIC
```

Parameter | Description
--------- | -----------
`add_customer` | Retrieve customer data (only applies to purchased transactions)
`add_external_sale_page` | Retrieve the HTML for the confirmation page, if you requested Ingresso to save this when [purchasing](#purchase) (only applies to purchased transactions). *Primarily for internal use*.
`transaction_uuid` | The unique identifier returned by a previous [reserve](#reserve).

### Response

> **Example response**

```shell
{
  "language_list": "en-gb,en,en-us,nl",
  "minutes_left_on_reserve": 14.3,
  "moneyflow_channel_code": "demo",
  "remote_site": "",
  "reserve_iso8601_date_and_time": "2017-01-09T18:19:27Z",
  "reserve_user": {
    "backend_group": "demo_internal_credit_group",
    "content_group": "",
    "default_country_code": "uk",
    "is_b2b": true,
    "real_name": "Demonstration User",
    "statement_descriptor": "",
    "style": "fixed-tabs",
    "sub_style": "styled-aff-default",
    "sub_sub_style": "ingresso-generic",
    "sub_user": "",
    "user_id": "demo"
  },
  "transaction_status": "reserved",
  "trolley": {
    "bundle": [
      {
        "bundle_order_count": 1,
        "bundle_source_code": "ext_test0",
        "bundle_source_desc": "External Test Backend 0",
        "bundle_total_cost": 71.5,
        "bundle_total_seatprice": 70,
        "bundle_total_send_cost": 1.5,
        "bundle_total_surcharge": 0,
        "currency": {
          "currency_code": "gbp"
        },
        "order": [
          {
            "event": {
              "city_code": "london-uk",
              "city_desc": "London",
              "classes": {
                "dance": "Ballet & Dance"
              },
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
              "event_uri_desc": "Matthew-Bourne%27s-Nutcracker-TEST",
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
              "venue_desc": "Sadler's Wells",
              "venue_uri_desc": "Sadler%27s-Wells"
            },
            "item_number": 1,
            "performance": {
              "date_desc": "Tue, 9th May 2017",
              "event_id": "6IF",
              "has_pool_seats": true,
              "is_ghost": false,
              "is_limited": false,
              "iso8601_date_and_time": "2017-05-09T19:30:00+01:00",
              "perf_id": "6IF-B0T",
              "perf_name": "Including back stage pass",
              "time_desc": "7.30 PM"
            },
            "price_band_code": "A/pool",
            "seat_request_status": "not_requested",
            "ticket_orders": {
              "ticket_order": [
                {
                  "discount_code": "",
                  "discount_desc": "",
                  "no_of_seats": 2,
                  "sale_seatprice": 35,
                  "sale_surcharge": 0,
                  "seats": [
                    {
                      "col_id": "144",
                      "full_id": "FN144",
                      "is_restricted_view": false,
                      "row_id": "FN"
                    },
                    {
                      "col_id": "143",
                      "full_id": "FN143",
                      "is_restricted_view": false,
                      "row_id": "FN"
                    }
                  ],
                  "total_sale_seatprice": 70,
                  "total_sale_surcharge": 0
                }
              ]
            },
            "ticket_type_code": "CIRCLE",
            "ticket_type_desc": "Upper circle",
            "total_no_of_seats": 2,
            "total_sale_seatprice": 70,
            "total_sale_surcharge": 0
          }
        ]
      }
    ],
    "minutes_left_on_reserve": 14.3,
    "transaction_uuid": "284d9c3a-d698-11e6-be8c-002590326962",
    "trolley_bundle_count": 1,
    "trolley_order_count": 1
  }
}
```

The returned attributes are dependent on the status of the transaction. For
example a purchased transaction will appear differently to a released
transaction.

Attribute | Description
--------- | -----------
`customer` | Detail of the purchasing customer (TODO: can we refer to the purchase response for these fields?). Only included when the `add_customer` parameter is present and the `transaction_status` is `purchased`.
`external_sale_page` | The detail of the confirmation page that was displayed to customers. Only included when the `add_external_sale_page` parameter is present and the `transaction_status` is `purchased`.
`purchase_iso8601_date_and_time` | The time the tickets were successfully purchased. 
`reserve_iso8601_date_and_time` | The time the tickets were successfully reserved.
`transaction_status` | One of `reserved`, `released` or `purchased` (TODO: anything else?)
`trolley` | The `trolley` object. If the `transaction_status` is `reserved` or `released`, the trolley attributes are as described in the [reserve](#reserve) response. If the `transaction_status` is `purchased` the trolley attributes are as described in the [purchase](#purchase) response. Note that if the `transaction_status` is `released` you should ignore the value of `minutes_left_on_reserve`.
