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
    --compressed \
    -G
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
status, meta = client.get_status(transaction_uuid='6d080a78-3011-11e7-b228-0025903268dc')
```

Parameter | Description
--------- | -----------
`add_customer` | Retrieve customer data (only applies to purchased transactions)
`add_external_sale_page` | Retrieve the HTML for the confirmation page, if you requested Ingresso to save this when [purchasing](#purchase) (only applies to purchased transactions). *Primarily for internal use*.
`transaction_uuid` | The unique identifier returned by a previous [reserve](#reserve).
`req_predicted_commission`| *Optional*. Include to retrieve [commission](#commission) data. For most partners this will include `predicted_user_commission` only (the predicted amount you earn per ticket). Some partners will also see `predicted_gross_commission`, which is the total commission available to be shared between Ingresso and our partner. By default you will see `predicted_user_commission` only - if you think you need to see `predicted_gross_commission` as well then please get in touch.

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

```python
from pyticketswitch.country import Country
from pyticketswitch.trolley import Trolley
from pyticketswitch.status import Status
from pyticketswitch.seat import Seat
from pyticketswitch.performance import Performance
from pyticketswitch.order import TicketOrder
from pyticketswitch.event import Event
from pyticketswitch.send_method import SendMethod
from pyticketswitch.order import Order
from pyticketswitch.address import Address
from pyticketswitch.bundle import Bundle

Status(
    status='reserved',
    reserved_at=datetime.datetime(2017, 5, 3, 17, 2, 17, tzinfo=tzutc()),
    trolley=Trolley(
        transaction_uuid='43768b36-3022-11e7-979f-002590326962',
        bundles=[
            Bundle(
                source_code='ext_test0',
                orders=[
                    Order(
                        item=1,
                        event=Event(
                            id='6IF',
                            status='live',
                            description="Matthew Bourne's Nutcracker TEST",
                            source='External Test Backend 0',
                            source_code='ext_test0',
                            event_type='simple_ticket',
                            venue="Sadler's Wells",
                            classes={
                                'dance': 'Ballet & Dance'
                            },
                            filters=[
                                
                            ],
                            postcode='EC1R 4TN',
                            city='London',
                            city_code='london-uk',
                            country='United Kingdom',
                            country_code='uk',
                            latitude=51.52961137,
                            longitude=-0.10601562,
                            max_running_time=120,
                            min_running_time=120,
                            show_performance_time=True,
                            has_performances=True,
                            is_seated=True,
                            needs_departure_date=False,
                            needs_duration=False,
                            needs_performance=False,
                            upsell_list=[
                                '6IE',
                                'MH0'
                            ],
                            critic_review_percent=100,
                        ),
                        performance=Performance(
                            id='6IF-B0O',
                            event_id='6IF',
                            date_time=datetime.datetime(2017, 5, 4, 19, 30, tzinfo=tzoffset(None, 3600)),
                            date_description='Thu, 4th May 2017',
                            time_description='7.30 PM',
                            has_pool_seats=True,
                            is_limited=False,
                            is_ghost=False,
                            running_time=120,
                        ),
                        price_band_code='A/pool',
                        ticket_type_code='STALLS',
                        ticket_type_description='Stalls',
                        ticket_orders=[
                            TicketOrder(
                                code='',
                                seats=[
                                    Seat(
                                        id='HJ401',
                                        column='401',
                                        row='HJ',
                                        separator='',
                                        is_restricted=False,
                                    ),
                                    Seat(
                                        id='HJ402',
                                        column='402',
                                        row='HJ',
                                        separator='',
                                        is_restricted=False,
                                    )
                                ],
                                description='',
                                number_of_seats=2,
                                seatprice=21.0,
                                surcharge=3.0,
                                total_seatprice=42.0,
                                total_surcharge=6.0,
                            )
                        ],
                        number_of_seats=2,
                        total_seatprice=42.0,
                        total_surcharge=6.0,
                        seat_request_status='not_requested',
                        requested_seat_ids=[
                            
                        ],
                        send_method=SendMethod(
                            code='COBO',
                            cost=1.5,
                            description='Collect from the venue',
                            type='collect',
                        ),
                    )
                ],
                description='External Test Backend 0',
                total_seatprice=42.0,
                total_surcharge=6.0,
                total_send_cost=1.5,
                total=49.5,
                currency_code='gbp',
            )
        ],
        discarded_orders=[
            
        ],
        minutes_left=14.5,
    ),
    languages=[
        'en'
    ],
    needs_payment_card=False,
    needs_email_address=False,
    needs_agent_reference=False,
    can_edit_address=True,
    allowed_countries=[
        Country(
            code='uk',
            description='United Kingdom',
        )
    ],
    minutes_left=14.5,
)
```

The returned attributes are dependent on the status of the transaction. For
example a purchased transaction will appear differently to a released
transaction.

Attribute | Description
--------- | -----------
`customer` | Detail of the purchasing customer; only included when the `add_customer` parameter is present and the `transaction_status` is `purchased`. The fields returned are as described in [purchase](#purchasing-on-credit).
`external_sale_page` | The detail of the confirmation page that was displayed to customers. Only included when the `add_external_sale_page` parameter is present and the `transaction_status` is `purchased`.
`purchase_iso8601_date_and_time` | The time the tickets were successfully purchased. 
`reserve_iso8601_date_and_time` | The time the tickets were successfully reserved.
`transaction_status` | One of `reserved`, `released`, `purchased`, `failed`, or `attempting` (will only be seen if the customer is being redirected to a payment page - most partners will not see this).
`trolley` | The `trolley` object. If the `transaction_status` is `reserved` or `released`, the trolley attributes are as described in the [reserve](#reserve) response. If the `transaction_status` is `purchased` the trolley attributes are as described in the [purchase](#purchase) response. Note that if the `transaction_status` is `released` you should ignore the value of `minutes_left_on_reserve`.
