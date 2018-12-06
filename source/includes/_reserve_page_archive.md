# Reserve Page Archive
> **Definition**

```
GET https://demo.ticketswitch.com/f13/reserve_page_archive.v1
```
This API call is faster than the [status](#status) API call as it is not getting the status of the reservation, it is simply retrieving the previously sent reservation response, verbatim.
Reservation responses are held for 24 hours, this exceeds the length of time that reservations are valid for.
Do not interpret this response as meaning that a reservation is still being held as it is simply a verbatim copy of the original reservation response.
This means that you can access all the data that you would normally require from a status call, except that the status and the amount of time left on the reservation will not have been updated (as it will be exactly the same as the original reservation response).
As this is simply returning a verbatim copy of the original reservation response, HTTP content negotiation and any of the usually recognised `req_` variables will be ignored.
If the original reservation response was an error then the same error will be returned when you request the reserve page archive.
If the reservation is not found then you will get an 400 error with `error_code: 8`.
If the reservation is no longer being stored (i.e. more than 24 hours have passed since the original reservation was made) you will get a 404 error with `error_code: 8`.

### Request

> **Example request**

```shell
curl https://demo.ticketswitch.com/f13/reserve_page_archive.v1 \
    -u "demo:demopass" \
    -d "transaction_uuid=U-986E1961-F8B7-11E8-8434-AC1F6B465FBC-C7DCDC47-LDNX" \
    --compressed \
    -G
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
reservation, meta = client.get_reservation(
    transaction_uuid="U-986E1961-F8B7-11E8-8434-AC1F6B465FBC-C7DCDC47-LDNX"
)
```

Parameter | Description
--------- | -----------
`transaction_uuid` | The unique identifier returned by a previous [reserve](#reserve).

### Response

> **Example response - get a previously made reserve response**

```shell

{
   "needs_payment_card" : false,
   "unreserved_orders" : [],
   "allowed_countries" : {
      "uk" : "United Kingdom"
   },
   "reserve_iso8601_date_and_time" : "2018-12-05T18:00:05Z",
   "transaction_status" : "reserved",
   "language_list" : [
      "en"
   ],
   "input_contained_unavailable_order" : false,
   "needs_agent_reference" : false,
   "minutes_left_on_reserve" : 15,
   "currency_details" : {
      "gbp" : {
         "currency_number" : 826,
         "currency_factor" : 100,
         "currency_code" : "gbp",
         "currency_pre_symbol" : "Â£",
         "currency_post_symbol" : "",
         "currency_places" : 2
      }
   },
   "needs_email_address" : false,
   "trolley_contents" : {
      "trolley_bundle_count" : 1,
      "transaction_uuid" : "U-986E1961-F8B7-11E8-8434-AC1F6B465FBC-C7DCDC47-LDNX",
      "bundle" : [
         {
            "bundle_total_send_cost" : 1,
            "bundle_source_code" : "ext_test1",
            "currency_code" : "gbp",
            "order" : [
               {
                  "internal_reserve_sub_ref" : "",
                  "total_no_of_seats" : 3,
                  "total_sale_seatprice" : 150,
                  "item_number" : 1,
                  "ticket_orders" : {
                     "ticket_order" : [
                        {
                           "sale_surcharge" : 5,
                           "sale_seatprice" : 50,
                           "total_sale_seatprice" : 150,
                           "total_sale_surcharge" : 15,
                           "discount_desc" : "Regular Ticket",
                           "no_of_seats" : 3,
                           "discount_code" : "NORMAL",
                           "seats" : [
                              {
                                 "col_id" : "2",
                                 "full_id" : "B2",
                                 "is_restricted_view" : false,
                                 "row_id" : "B",
                                 "seat_text_code" : "B.2"
                              },
                              {
                                 "is_restricted_view" : false,
                                 "col_id" : "3",
                                 "full_id" : "B3",
                                 "seat_text_code" : "B.3",
                                 "row_id" : "B"
                              },
                              {
                                 "col_id" : "4",
                                 "full_id" : "B4",
                                 "is_restricted_view" : false,
                                 "seat_text_code" : "B.4",
                                 "row_id" : "B"
                              }
                           ]
                        }
                     ]
                  },
                  "price_band_code" : "A/pool",
                  "ticket_type_desc" : "Stalls",
                  "event" : {
                     "is_auto_quantity_add_on" : false,
                     "city_code" : "london-uk",
                     "event_type" : "simple_ticket",
                     "custom_filter" : [],
                     "need_duration" : false,
                     "event_uri_desc" : "TEST-EVENT-The-Unremarkable-Incident-of-the-Cat-at-Lunchtime",
                     "venue_uri_desc" : "Lyric-Apollo",
                     "venue_desc" : "Lyric Apollo",
                     "event_id" : "7AB",
                     "event_desc" : "TEST EVENT - The Unremarkable Incident of the Cat at Lunchtime",
                     "is_seated" : true,
                     "postcode" : "W6 7ES",
                     "event_upsell_list" : {
                        "event_id" : [
                           "7AA",
                           "6IF"
                        ]
                     },
                     "country_code" : "uk",
                     "need_departure_date" : false,
                     "classes" : {
                        "tours" : "Tours"
                     },
                     "min_running_time" : 90,
                     "event_status" : "live",
                     "source_code" : "ext_test1",
                     "city_desc" : "London",
                     "has_no_perfs" : false,
                     "show_perf_time" : true,
                     "is_add_on" : false,
                     "need_performance" : true,
                     "country_desc" : "United Kingdom",
                     "max_running_time" : 90,
                     "source_desc" : "External Test Backend 1",
                     "geo_data" : {
                        "latitude" : 51.49306,
                        "longitude" : -0.22639
                     },
                     "event_path" : "/7AB-test-event-the-unremarkable-incident-of-the-cat-at-lunchtime/"
                  },
                  "performance" : {
                     "time_desc" : "3.30 PM",
                     "has_pool_seats" : true,
                     "is_limited" : false,
                     "iso8601_date_and_time" : "2020-01-01T15:30:00Z",
                     "is_ghost" : false,
                     "event_id" : "7AB",
                     "date_desc" : "Wed, 1st January 2020",
                     "running_time" : 90,
                     "perf_id" : "7AB-6"
                  },
                  "send_method" : {
                     "can_generate_self_print" : false,
                     "send_desc" : "Printable eTicket",
                     "send_cost" : 1,
                     "has_html_page" : true,
                     "send_type" : "selfprint",
                     "send_code" : "VOUCH"
                  },
                  "seat_request_status" : "not_requested",
                  "total_sale_surcharge" : 15,
                  "ticket_type_code" : "STALLS",
                  "requested_seat_ids" : []
               }
            ],
            "bundle_total_cost" : 166,
            "bundle_total_surcharge" : 15,
            "bundle_order_count" : 1,
            "bundle_total_seatprice" : 150,
            "bundle_source_desc" : "External Test Backend 1"
         }
      ],
      "trolley_order_count" : 1
   },
   "can_edit_address" : true,
   "prefilled_address" : {
      "country_code" : "uk"
   }
}
```

```python
from pyticketswitch.send_method import SendMethod
from pyticketswitch.country import Country
from pyticketswitch.seat import Seat
from pyticketswitch.address import Address
from pyticketswitch.event import Event
from pyticketswitch.bundle import Bundle
from pyticketswitch.performance import Performance
from pyticketswitch.reservation import Reservation
from pyticketswitch.order import TicketOrder
from pyticketswitch.trolley import Trolley
from pyticketswitch.order import Order

Reservation(
    status='reserved',
    reserved_at=datetime.datetime(2017, 5, 3, 15, 1, 45, tzinfo=tzutc()),
    trolley=Trolley(
        transaction_uuid='U-986E1961-F8B7-11E8-8434-AC1F6B465FBC-C7DCDC47-LDNX',
        bundles=[
            Bundle(
                source_code='ext_test1',
                orders=[
                    Order(
                        item=1,
                        event=Event(
                            id='7AB',
                            status='live',
                            description='The Unremarkable Incident of the Cat at Lunchtime'
,
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
                        send_method=SendMethod(
                            code='VOUCH',
                            cost=0.0,
                            description='Printable eTicket',
                            type='selfprint',
                            can_generate_self_print=False,
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
        minutes_left=14.9833333333,
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
    minutes_left=14.9833333333,
)
```

The returned attributes are identical to the original reserve response, even if the reservation has timed out or has been bought, this response will stay the same.

Attribute | Description
--------- | -----------
`reserve_iso8601_date_and_time` | The time the tickets were successfully reserved.
`transaction_status` | Will always be `reserved` as that is what it was at the time of the reservation, even if the transaction has since expired or been purchased.
`trolley` | The `trolley` object, the trolley attributes are as described in the [reserve](#reserve) response. Note that the value of `minutes_left_on_reserve` will be out of date as it will be the same as it was at reserve time.
