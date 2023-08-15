# Trans ID Status
> **Definition**

```
GET https://api.ticketswitch.com/f13/trans_id_status.v1?transaction_id={trans_id}
```

This resource is to provide legacy API support. In the old XML API after
reserving  tickets a transaction ID was returned. This has been replaced with
a transaction UUID. If you have made purchases using the old XML API and you
need to retrieve details of the transaction then you can use
`trans_id_status` lets you request status for a particular `transaction_id`.
If you have only used this JSON API then there is no need to use
`trans_id_status`.


### Request

> **Example request**

```shell
curl https://api.ticketswitch.com/f13/trans_id_status.v1 \
    -u "demo:demopass" \
    -d "transaction_id=T000-0000-8LZT-WA8D" \
    --compressed \
    -G
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
status, meta = client.get_status(transaction_id='T000-0000-8ZVV-2E78')

```

Parameter | Description
--------- | -----------
`add_customer` | Retrieve customer data (only applies to purchased transactions)
`add_external_sale_page` | Retrieve the HTML for the confirmation page, if you requested Ingresso to save this when [purchasing](#purchase) (only applies to purchased transactions). *Primarily for internal use*.
`transaction_id` | The unique identifier returned by a previous [reserve](#reserve).

### Response

> **Example response**

```shell
{
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
  "language_list": [
    "en"
  ],
  "purchase_iso8601_date_and_time": "2017-02-20T19:42:55Z",
  "reserve_iso8601_date_and_time": "2017-02-20T19:42:55Z",
  "transaction_status": "purchased",
  "trolley_contents": {
    "bundle": [
      {
        "bundle_order_count": 1,
        "bundle_source_code": "ext_test0",
        "bundle_source_desc": "External Test Backend 0",
        "bundle_total_cost": 71.5,
        "bundle_total_seatprice": 70,
        "bundle_total_send_cost": 1.5,
        "bundle_total_surcharge": 0,
        "currency_code": "gbp",
        "order": [
          {
            "backend_purchase_reference": "PURCHASE-1445-1",
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
            "got_requested_seats": false,
            "gross_commission": {
              "amount_excluding_vat": 0.25,
              "amount_including_vat": 0.3,
              "commission_currency_code": "gbp"
            },
            "item_number": 1,
            "performance": {
              "date_desc": "Thu, 23rd February 2017",
              "event_id": "6IF",
              "has_pool_seats": true,
              "is_ghost": false,
              "is_limited": false,
              "iso8601_date_and_time": "2017-02-23T19:30:00Z",
              "perf_id": "6IF-A8Q",
              "running_time": 120,
              "time_desc": "7.30 PM"
            },
            "price_band_code": "A/pool",
            "send_method": {
              "send_code": "COBO",
              "send_cost": 1.5,
              "send_desc": "Collect from the venue",
              "send_final_type": "collect",
              "send_type": "collect"
            },
            "ticket_orders": {
              "ticket_order": [
                {
                  "discount_code": "ADULT",
                  "discount_desc": "Adult",
                  "no_of_seats": 2,
                  "sale_seatprice": 35,
                  "sale_surcharge": 0,
                  "seats": [
                    {
                      "col_id": "140",
                      "full_id": "SQ140",
                      "is_restricted_view": false,
                      "row_id": "SQ"
                    },
                    {
                      "col_id": "139",
                      "full_id": "SQ139",
                      "is_restricted_view": false,
                      "row_id": "SQ"
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
            "total_sale_surcharge": 0,
            "user_commission": {
              "amount_excluding_vat": 0.11,
              "amount_including_vat": 0.14,
              "commission_currency_code": "gbp"
            }
          }
        ],
        "purchase_result": {
          "is_semi_credit": false,
          "success": true
        }
      }
    ],
    "purchase_result": {
      "is_partial": false,
      "success": true
    },
    "transaction_id": "T000-0000-8LZT-WA8D",
    "transaction_uuid": "c6335e88-f7a4-11e6-b286-0025903268dc",
    "trolley_bundle_count": 1,
    "trolley_order_count": 1
  }
}

```

```python
from pyticketswitch.trolley import Trolley
from pyticketswitch.status import Status
from pyticketswitch.seat import Seat
from pyticketswitch.performance import Performance
from pyticketswitch.order import TicketOrder
from pyticketswitch.purchase_result import PurchaseResult
from pyticketswitch.event import Event
from pyticketswitch.send_method import SendMethod
from pyticketswitch.order import Order
from pyticketswitch.bundle import Bundle

Status(
    status='purchased',
    reserved_at=datetime.datetime(2017, 5, 3, 15, 1, 45, tzinfo=tzutc()),
    purchased_at=datetime.datetime(2017, 5, 3, 15, 14, 5, tzinfo=tzutc()),
    trolley=Trolley(
        transaction_uuid='6d080a78-3011-11e7-b228-0025903268dc',
        transaction_id='T000-0000-8ZVV-2E78',
        bundles=[
            Bundle(
                source_code='ext_test1',
                orders=[
                    Order(
                        item=1,
                        event=Event(
                            id='7AB',
                            status='live',
                            description='The Unremarkable Incident of the Cat at Lunchtime',
                            source='External Test Backend 1',
                            source_code='ext_test1',
                            event_type='simple_ticket',
                            venue='Lyric Apollo',
                            classes={
                                'theatre': 'Theatre'
                            },
                            postcode='W6 7ES',
                            city='London',
                            city_code='london-uk',
                            country='United Kingdom',
                            country_code='uk',
                            latitude=51.49306,
                            longitude=-0.22639,
                            max_running_time=90,
                            min_running_time=90,
                            show_performance_time=True,
                            has_performances=True,
                            is_seated=True,
                            needs_departure_date=False,
                            needs_duration=False,
                            needs_performance=False,
                        ),
                        performance=Performance(
                            id='7AB-5',
                            event_id='7AB',
                            date_time=datetime.datetime(2019, 1, 1, 15, 30, tzinfo=tzutc()),
                            date_description='Tue, 1st January 2019',
                            time_description='3.30 PM',
                            has_pool_seats=True,
                            is_limited=False,
                            is_ghost=False,
                            running_time=90,
                        ),
                        price_band_code='A/pool',
                        ticket_type_code='STALLS',
                        ticket_type_description='Stalls',
                        ticket_orders=[
                            TicketOrder(
                                code='NORMAL',
                                seats=[
                                    Seat(
                                        id='A1',
                                        column='1',
                                        row='A',
                                        separator='',
                                        is_restricted=True,
                                        seat_text='Restricted View',
                                    ),
                                    Seat(
                                        id='A2',
                                        column='2',
                                        row='A',
                                        separator='',
                                        is_restricted=True,
                                        seat_text='Restricted View',
                                    )
                                ],
                                description='Regular Ticket',
                                number_of_seats=2,
                                seatprice=50.0,
                                surcharge=5.0,
                                total_seatprice=100.0,
                                total_surcharge=10.0,
                            )
                        ],
                        number_of_seats=2,
                        total_seatprice=100.0,
                        total_surcharge=10.0,
                        seat_request_status='got_all',
                        requested_seat_ids=[
                            'A1',
                            'A2'
                        ],
                        backend_purchase_reference='PURCHASE-DA6E-0',
                        send_method=SendMethod(
                            code='VOUCH',
                            cost=0.0,
                            description='Printable eTicket',
                            type='selfprint',
                            can_generate_self_print=False,
                            self_print_voucher_url='https://api.ticketswitch.com/tickets/web_self_print.buy/demo?crypto_block=U_--z_v1JesEuC-2FgKkj4LEhJdbZVhIdnBp3b0sP0uh1KqsYF_fumHhC_IFKIXh471k1Del_9Wi0W8_8Ay7xJNhbK299MEf-wLwbOmtNBdnzh8BZ9icsINHBS0w5gNCc7zbY',
                        ),
                    )
                ],
                description='External Test Backend 1',
                total_seatprice=100.0,
                total_surcharge=10.0,
                total_send_cost=0.0,
                total=110.0,
                currency_code='gbp',
            )
        ],
        purchase_result=PurchaseResult(
            success=True,
        ),
    ),
    languages=[
        'en'
    ],
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
