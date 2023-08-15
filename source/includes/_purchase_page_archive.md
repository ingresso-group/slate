# Purchase Page Archive
> **Definition**

```
GET https://api.ticketswitch.com/f13/purchase_page_archive.v1
```
This API call does not get an up to date status of the purchase, it is simply retrieving the previously sent purchase response, verbatim.
This means that you can access all the data that you would normally require from a purchase call, except that the status could be out of date (as it will be exactly the same as the original purchase response).
As this is simply returning a verbatim copy of the original purchase response, HTTP content negotiation and any of the usually recognised `req_` variables will be ignored.
If the original purchase response was an error then the same error will be returned when you request the purchase page archive.
The purchase responses are kept for at least a year.
If the purchase is not found then you will get an 400 error with `error_code: 8`.
If the purchase is no longer being stored (i.e. more than a year has passed since the original purchase was made) you will get a 410 Gone error with `error_code: 8`.


### Request

> **Example request**

```shell
curl https://api.ticketswitch.com/f13/purchase_page_archive.v1 \
    -u "demo:demopass" \
    -d "transaction_uuid=U-986E1961-F8B7-11E8-8434-AC1F6B465FBC-C7DCDC47-LDNX" \
    --compressed \
    -G
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
reservation, meta = client.get_purchase(
    transaction_uuid="U-986E1961-F8B7-11E8-8434-AC1F6B465FBC-C7DCDC47-LDNX"
)
```

Parameter | Description
--------- | -----------
`transaction_uuid` | The unique identifier returned by a previous [purchase](#purchase).


### Response

> **Example response - a previously purchased transaction**

``` shell
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
  "customer": {
    "addr_line_one": "Metro Building",
    "addr_line_one_latin": "Metro Building",
    "addr_line_two": "1 Butterwick",
    "addr_line_two_latin": "1 Butterwick",
    "agent_ref": "",
    "country": "United Kingdom",
    "country_code": "uk",
    "country_latin": "United Kingdom",
    "county": "",
    "county_latin": "",
    "email_addr": "testing@gmail.com",
    "first_name": "Test",
    "first_name_latin": "Test",
    "home_phone": "0203 137 7420",
    "initials": "",
    "initials_latin": "",
    "last_name": "Tester",
    "last_name_latin": "Tester",
    "postcode": "W6 8DL",
    "postcode_latin": "W6 8DL",
    "suffix": "",
    "suffix_latin": "",
    "supplier_can_use_customer_data": false,
    "title": "",
    "title_latin": "",
    "town": "London",
    "town_latin": "London",
    "user_can_use_customer_data": true,
    "work_phone": "0203 137 7420",
    "world_can_use_customer_data": false
  },
  "language_list": [
    "en-gb",
    "en",
    "en-us",
    "nl"
  ],
  "purchase_iso8601_date_and_time": "2017-04-12T08:38:35Z",
  "reserve_iso8601_date_and_time": "2017-04-12T08:38:20Z",
  "transaction_status": "purchased",
  "trolley_contents": {
    "bundle": [
      {
        "bundle_order_count": 1,
        "bundle_source_code": "ext_test0",
        "bundle_source_desc": "External Test Backend 0",
        "bundle_total_cost": 62.5,
        "bundle_total_seatprice": 51,
        "bundle_total_send_cost": 1.5,
        "bundle_total_surcharge": 10,
        "currency_code": "gbp",
        "order": [
          {
            "backend_purchase_reference": "PURCHASE-6710-1",
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
              "amount_excluding_vat": 7.13,
              "amount_including_vat": 8.55,
              "commission_currency_code": "gbp"
            },
            "item_number": 1,
            "performance": {
              "date_desc": "Tue, 13th June 2017",
              "event_id": "6IF",
              "has_pool_seats": true,
              "is_ghost": false,
              "is_limited": false,
              "iso8601_date_and_time": "2017-06-13T19:30:00+01:00",
              "perf_id": "6IF-B1S",
              "perf_name": "Including back stage pass",
              "running_time": 120,
              "time_desc": "7.30 PM"
            },
            "price_band_code": "C/pool",
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
                  "no_of_seats": 1,
                  "sale_seatprice": 25,
                  "sale_surcharge": 4,
                  "seats": [
                    {
                      "col_id": "386",
                      "full_id": "NM386",
                      "is_restricted_view": false,
                      "row_id": "NM"
                    }
                  ],
                  "total_sale_seatprice": 25,
                  "total_sale_surcharge": 4
                },
                {
                  "discount_code": "CHILD",
                  "discount_desc": "Child rate",
                  "no_of_seats": 2,
                  "sale_seatprice": 13,
                  "sale_surcharge": 3,
                  "seats": [
                    {
                      "col_id": "383",
                      "full_id": "NM383",
                      "is_restricted_view": false,
                      "row_id": "NM"
                    },
                    {
                      "col_id": "380",
                      "full_id": "NM380",
                      "is_restricted_view": false,
                      "row_id": "NM"
                    }
                  ],
                  "total_sale_seatprice": 26,
                  "total_sale_surcharge": 6
                }
              ]
            },
            "ticket_type_code": "CIRCLE",
            "ticket_type_desc": "Upper circle",
            "total_no_of_seats": 3,
            "total_sale_seatprice": 51,
            "total_sale_surcharge": 10,
            "user_commission": {
              "amount_excluding_vat": 3.21,
              "amount_including_vat": 3.85,
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
    "transaction_id": "T000-0000-8RGW-3619",
    "transaction_uuid": "61cfd4eb-1f5b-11e7-b228-0025903268dc",
    "trolley_bundle_count": 1,
    "trolley_order_count": 1
  }
}
```

```python
from pyticketswitch.send_method import SendMethod
from pyticketswitch.purchase_result import PurchaseResult
from pyticketswitch.seat import Seat
from pyticketswitch.event import Event
from pyticketswitch.status import Status
from pyticketswitch.customer import Customer
from pyticketswitch.bundle import Bundle
from pyticketswitch.performance import Performance
from pyticketswitch.order import TicketOrder
from pyticketswitch.trolley import Trolley
from pyticketswitch.order import Order

# An on credit purchase should always return a status object and will never
# return a callout.
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
                            date_time=datetime.datetime(2019, 1, 1, 15, 30, tzinfo=tzutc()) ,
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
                            self_print_voucher_url='https://api.ticketswitch.com/tickets/web_self_print.buy/demo?crypto_block=M_--R-aJRIR74rjx8gM2szdo9BEJZ8oV6wfGukxyZGDA2_DU2K3w-Y8L_3LWvIgKO_QxwMN8WmasaUuL_WpzKS-q2roXIWxXqjTxyDYZdge-wjDI0RZ8NpBAS0--Y',
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
    customer=Customer(
        first_name='Test',
        first_name_latin='Test',
        last_name='Tester',
        last_name_latin='Tester',
        address_lines=[
            'Metro Building',
            '1 Butterwick'
        ],
        address_lines_latin=[
            'Metro Building',
            '1 Butterwick'
        ],
        title='',
        title_latin='',
        initials='',
        initials_latin='',
        suffix='',
        suffix_latin='',
        country_code='uk',
        post_code='W6 8DL',
        post_code_latin='W6 8DL',
        town='London',
        town_latin='London',
        county='London',
        county_latin='London',
        country='United Kingdom',
        country_latin='United Kingdom',
        email='',
        home_phone='0203 137 7420',
        work_phone='0203 137 7420',
        agent_reference='',
        supplier_can_use_data=False,
        user_can_use_data=True,
        world_can_use_data=False,
    ),
)
```

The returned attributes are identical to the original purchase response, even if the purchase has since been cancelled, this response will still be the same verbatim copy of the original purchase call.

Attribute | Description
--------- | -----------
`customer` | The `customer` object, the customer attributes are as described in the [purchase](#purchase) response.
`language_list` | Not useful for most partners, can be ignored.
`purchase_iso8601_date_and_time` | The purchase time in ISO 8601 format.
`reserve_iso8601_date_and_time` | The reserve time in ISO 8601 format.
`transaction_status` | This will be the original status that was reported at purchase, time (i.e. `purchased`, `failed` or `attempting`) and it will not change because this is a verbatim copy of the original purchase call.
`trolley_contents` | The `trolley` object, the trolley attributes are as described in the [purchase](#purchase) response.
