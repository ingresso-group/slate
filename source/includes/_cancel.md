# Cancel
> **Definition**

```
POST https://demo.ticketswitch.com/f13/cancel.v1?transaction_uuid={trans_uuid}&cancel_items_list=1,2
```

This endpoint allows users to attempt to cancel [purchased](#purchase) transactions (there is no guarantee that cancellations will succeed).
By default, users are unable to cancel transactions.
If you believe that you should be able to cancel transactions then contact us techsupport@ingresso.co.uk.

If you would like to attempt to cancel a transaction, follow these steps:
1. Check if the order items within the transaction are cancellable by looking at the `cancellation_status` of each of the order items in the [purchase](#purchase) or [status](#status) response (they are cancellable if `cancellation_status` is `possible`).
2. Attempt the cancellation of the `"cancellation_status": "possible"` order items. You can specify which order items to cancel in the transaction with the optional list, `cancel_items_list`. If you don't specify any order items to cancel then it will be assumed that the whole transaction should be cancelled.
3. Check the response to see if the cancellation worked as you expected, for example some order items may not succeed in getting cancelled, whereas others might. Cancelling some items may cause other items to be cancelled. Check both the high level `cancelled_item_numbers` and the updated `cancellation_status` on each of the orders within the trolley. There might be a charge for the cancellation.

### Request

> **Example request - cancel entire transaction**

```shell
curl https://demo.ticketswitch.com/f13/cancel.v1 \
    -u "demo:demopass" \
    -d "transaction_uuid=284d9c3a-d698-11e6-be8c-002590326962" \
    --compressed \
    -G
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
status, meta = client.cancel_purchase(transaction_uuid='284d9c3a-d698-11e6-be8c-002590326962')
```

> **Example request - specify order items to cancel by item number**

```shell
curl https://demo.ticketswitch.com/f13/cancel.v1 \
    -u "demo:demopass" \
    -d "transaction_uuid=284d9c3a-d698-11e6-be8c-002590326962" \
    -d "cancel_items_list=1,3" \
    --compressed \
    -G
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
status, meta = client.cancel_purchase(transaction_uuid='284d9c3a-d698-11e6-be8c-002590326962', cancel_items_list=[1,3])
```

Parameter | Description
--------- | -----------
`transaction_uuid` | The unique identifier returned by a previous [purchase](#purchase).
`cancel_items_list` | An optional parameter that may be included in the request. It is a list of items to cancel in the transaction (note that cancelling some items may cause other items to be cancelled as well).

### Response

> **Example response -- successful cancellation**

```shell
{
   "trolley_contents" : {
      "trolley_bundle_count" : 1,
      "trolley_order_count" : 1,
      "bundle" : [
         {
            "bundle_total_seatprice" : 32,
            "bundle_total_send_cost" : 1.5,
            "order" : [
               {
                  "price_band_code" : "A/pool",
                  "internal_reserve_sub_ref2" : "NUT SUB 2",
                  "backend_cancellation_reference" : "ATTEMPT-482281",
                  "total_sale_surcharge" : 5,
                  "item_number" : 1,
                  "performance" : {
                     "running_time" : 120,
                     "has_pool_seats" : true,
                     "event_id" : "6IF",
                     "time_desc" : "7.30 PM",
                     "is_limited" : false,
                     "perf_id" : "6IF-D9P",
                     "is_ghost" : false,
                     "iso8601_date_and_time" : "2019-04-26T19:30:00+01:00",
                     "date_desc" : "Fri, 26th April 2019"
                  },
                  "backend_purchase_reference" : "PURCHASE-286D",
                  "user_commission" : {
                     "amount_including_vat" : 11.4,
                     "commission_currency_code" : "gbp",
                     "amount_excluding_vat" : 9.5
                  },
                  "send_method" : {
                     "send_cost" : 1.5,
                     "send_desc" : "Collect from venue",
                     "send_final_comment" : "Instructions for collecting tickets at the venue box office:\n- Tickets must be collected by the cardholder with valid photo identification and the payment card.\n- The cardholderâs signature will be required on receipt of these tickets.\n- Tickets are only available for collection on the day of the performance.\n- Guests are advised to arrive at least 30 minutes before the performance time.\n- If the cardholder is unable to collect these tickets please contact Guest Services on 0800 640 8101.\n\nCan we help?\nIf you require further information please contact our Guest Services team:\nLive chat or call 0800 640 8101 (Monday â Sunday, 9am â 9pm GMT/BST)",
                     "send_type" : "collect",
                     "send_code" : "COBO",
                     "send_final_type" : "collect"
                  },
                  "ticket_type_desc" : "Stalls",
                  "gross_commission" : {
                     "amount_including_vat" : 11.4,
                     "amount_excluding_vat" : 9.5,
                     "commission_currency_code" : "gbp"
                  },
                  "cancellation_status" : "cancelled",
                  "ticket_orders" : {
                     "ticket_order" : [
                        {
                           "total_sale_seatprice" : 21,
                           "sale_surcharge" : 3,
                           "total_sale_combined" : 24,
                           "sale_seatprice" : 21,
                           "seats" : [
                              {
                                 "full_id" : "YG167",
                                 "is_restricted_view" : false,
                                 "col_id" : "167",
                                 "row_id" : "YG"
                              }
                           ],
                           "discount_code" : "ADULT",
                           "discount_desc" : "Adult standard",
                           "sale_combined" : 24,
                           "total_sale_surcharge" : 3,
                           "no_of_seats" : 1
                        },
                        {
                           "total_sale_surcharge" : 2,
                           "sale_combined" : 13,
                           "discount_desc" : "Adult 18+ including behind the scenes tour",
                           "discount_code" : "CHILD",
                           "no_of_seats" : 1,
                           "sale_surcharge" : 2,
                           "total_sale_seatprice" : 11,
                           "seats" : [
                              {
                                 "row_id" : "YG",
                                 "col_id" : "168",
                                 "is_restricted_view" : false,
                                 "full_id" : "YG168"
                              }
                           ],
                           "sale_seatprice" : 11,
                           "total_sale_combined" : 13
                        }
                     ]
                  },
                  "cancellation_comment" : "",
                  "total_sale_combined" : 37,
                  "total_no_of_seats" : 2,
                  "price_band_desc" : "Top price (band A)",
                  "seat_request_status" : "not_requested",
                  "ticket_type_code" : "STALLS",
                  "total_sale_seatprice" : 32,
                  "event" : {
                     "has_no_perfs" : false,
                     "min_running_time" : 120,
                     "need_departure_date" : false,
                     "need_duration" : false,
                     "city_code" : "london-uk",
                     "show_perf_time" : true,
                     "is_seated" : true,
                     "event_uri_desc" : "Matthew-Bourne%27s-Nutcracker%21",
                     "source_code" : "ext_test0",
                     "source_desc" : "Test SystemZero for on-credit backend group",
                     "event_desc" : "Matthew Bourne's Nutcracker!",
                     "event_type" : "simple_ticket",
                     "event_status" : "live",
                     "event_upsell_list" : {
                        "event_id" : [
                           "6IE",
                           "6KU"
                        ]
                     },
                     "venue_uri_desc" : "Sadler%27s-Wells",
                     "critic_review_percent" : 80,
                     "country_desc" : "United Kingdom",
                     "venue_desc" : "Sadler's Wells",
                     "max_running_time" : 120,
                     "geo_data" : {
                        "latitude" : 51.5,
                        "longitude" : -0.15
                     },
                     "event_id" : "6IF",
                     "need_performance" : true,
                     "postcode" : "EC1R 4TN",
                     "city_desc" : "London",
                     "is_add_on" : false,
                     "country_code" : "uk",
                     "classes" : {
                        "theatre" : "Theatre"
                     },
                     "is_auto_quantity_add_on" : false,
                     "custom_filter" : []
                  },
                  "internal_reserve_sub_ref" : "NUT SUB 1",
                  "requested_seat_ids" : [],
                  "internal_purchase_sub_ref" : "SUBREF-1:NUT SUB 2:NUT SUB 1"
               }
            ],
            "purchase_result" : {
               "can_cancel_individual_orders" : false,
               "success" : true,
               "backend_purchase_reference" : "PURCHASE-286D",
               "is_semi_credit" : false
            },
            "bundle_total_surcharge" : 5,
            "bundle_order_count" : 1,
            "bundle_source_code" : "ext_test0",
            "bundle_total_cost" : 38.5,
            "bundle_source_desc" : "Test SystemZero for on-credit backend group",
            "currency_code" : "gbp"
         }
      ],
      "transaction_uuid" : "284d9c3a-d698-11e6-be8c-002590326962",
      "purchase_result" : {
         "success" : true,
         "is_partial" : false
      },
      "transaction_id" : "U000-0000-2M96-DEVX"
   },
   "currency_details" : {
      "gbp" : {
         "currency_pre_symbol" : "Â£",
         "currency_post_symbol" : "",
         "currency_factor" : 100,
         "currency_code" : "gbp",
         "currency_number" : 826,
         "currency_places" : 2
      }
   },
   "cancelled_item_numbers" : [
      1
   ]
}
```

```python
from pyticketswitch.trolley import Trolley
from pyticketswitch.cancellation import CancellationResult
from pyticketswitch.seat import Seat
from pyticketswitch.performance import Performance
from pyticketswitch.purchase_result import PurchaseResult
from pyticketswitch.order import TicketOrder
from pyticketswitch.event import Event
from pyticketswitch.send_method import SendMethod
from pyticketswitch.order import Order
from pyticketswitch.bundle import Bundle

CancellationResult(
    cancelled_item_numbers=[1],
    trolley=Trolley(
        transaction_uuid='284d9c3a-d698-11e6-be8c-002590326962',
        bundles=[
            Bundle(
                source_code='ext_test0',
                orders=[
                    Order(
                        item=1,
                        backend_cancellation_reference='ATTEMPT-482281',
                        backend_purchase_reference='PURCHASE-286D',
                        cancellation_comment='',
                        cancellation_status='cancelled',
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
                            id='6IF-D9P',
                            event_id='6IF',
                            date_time=datetime.datetime(2019, 4, 26, 19, 30, tzinfo=tzoffset(None, 3600)),
                            date_description='Fri, 26th April 2019',
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
                                        id='YG167',
                                        column='167',
                                        row='YG',
                                        separator='',
                                        is_restricted=False,
                                    ),
                                ],
                                description='',
                                number_of_seats=1,
                                seatprice=21.0,
                                surcharge=3.0,
                                total_seatprice=21.0,
                                total_surcharge=3.0,
                            ),
                            TicketOrder(
                                code='',
                                seats=[
                                    Seat(
                                        id='YG168',
                                        column='168',
                                        row='YG',
                                        separator='',
                                        is_restricted=False,
                                    ),
                                ],
                                description='',
                                number_of_seats=1,
                                seatprice=11.0,
                                surcharge=2.0,
                                total_seatprice=11.0,
                                total_surcharge=2.0,
                            )
                        ],
                        number_of_seats=2,
                        total_seatprice=32.0,
                        total_surcharge=5.0,
                        seat_request_status='not_requested',
                        send_method=SendMethod(
                            code='COBO',
                            cost=1.5,
                            description='Collect from the venue',
                            type='collect',
                        ),
                    )
                ],
                description='External Test Backend 0',
                total_seatprice=32.0,
                total_surcharge=5.0,
                total_send_cost=1.5,
                total=38.5,
                currency_code='gbp',
                purchase_result=PurchaseResult(
                    success=True,
                    can_cancel_individual_orders=False,
                    backend_purchase_reference="PURCHASE-286D",
                    is_semi_credit=False
                ),
            )
        ],
        purchase_result=PurchaseResult(
            success=True,
            is_partial=False,
        ),
    ),
)
```

> **Example response -- must also cancel**

```shell
{
   "must_also_cancel" : [
      {
         "cancellation_comment" : "",
         "send_method" : {
            "send_cost_in_desired" : 8.76,
            "send_type" : "post",
            "send_final_type" : "post",
            "send_desc" : "Post worldwide",
            "send_cost" : 10,
            "send_code" : "POST"
         },
         "ticket_orders" : {
            "ticket_order" : [
               {
                  "seats" : [
                     {
                        "is_restricted_view" : false,
                        "row_id" : "YC",
                        "full_id" : "YC472",
                        "col_id" : "472"
                     },
                     {
                        "full_id" : "YC473",
                        "col_id" : "473",
                        "row_id" : "YC",
                        "is_restricted_view" : false
                     },
                     {
                        "col_id" : "474",
                        "full_id" : "YC474",
                        "row_id" : "YC",
                        "is_restricted_view" : false
                     }
                  ],
                  "sale_combined_in_desired" : 135.75,
                  "sale_surcharge_in_desired" : 13.14,
                  "sale_seatprice" : 140,
                  "total_sale_surcharge" : 45,
                  "total_sale_combined" : 465,
                  "discount_desc" : "",
                  "total_sale_combined_in_desired" : 407.24,
                  "sale_surcharge" : 15,
                  "total_sale_surcharge_in_desired" : 39.41,
                  "no_of_seats" : 3,
                  "total_sale_seatprice" : 420,
                  "sale_combined" : 155,
                  "sale_seatprice_in_desired" : 122.61,
                  "total_sale_seatprice_in_desired" : 367.83,
                  "discount_code" : ""
               }
            ]
         },
         "total_sale_combined_in_desired" : 407.24,
         "total_sale_seatprice" : 420,
         "seat_request_status" : "not_requested",
         "backend_cancellation_reference" : "",
         "requested_seat_ids" : [],
         "internal_purchase_sub_ref" : "",
         "ticket_type_desc" : "Menu French Cancan",
         "internal_reserve_sub_ref" : "",
         "price_band_code" : "A/pool",
         "user_commission" : {
            "commission_currency_code" : "eur",
            "amount_including_vat" : 0,
            "amount_excluding_vat" : 0
         },
         "total_sale_combined" : 465,
         "backend_purchase_reference" : "PURCHASE-3AFD",
         "event" : {
            "custom_filter" : [],
            "min_running_time" : 120,
            "geo_data" : {
               "latitude" : 0,
               "longitude" : 0
            },
            "postcode" : "75018",
            "event_id" : "BPT",
            "venue_uri_desc" : "Bal-du-Moulin-Rouge",
            "has_no_perfs" : false,
            "need_performance" : true,
            "source_code" : "ext_test1",
            "event_uri_desc" : "Moulin-Rouge-%28Dinner-Show%29",
            "event_type" : "simple_ticket",
            "is_auto_quantity_add_on" : false,
            "country_code" : "fr",
            "venue_desc" : "Bal du Moulin Rouge",
            "show_perf_time" : true,
            "classes" : {
               "attract" : "Attractions",
               "theatre" : "Theatre"
            },
            "source_desc" : "Test System One",
            "max_running_time" : 120,
            "event_status" : "live",
            "need_departure_date" : false,
            "need_duration" : false,
            "event_desc" : "Moulin Rouge (Dinner Show)",
            "country_desc" : "France",
            "is_seated" : true,
            "is_add_on" : false
         },
         "gross_commission" : {
            "amount_including_vat" : 60,
            "amount_excluding_vat" : 50,
            "commission_currency_code" : "eur"
         },
         "total_no_of_seats" : 3,
         "ticket_type_code" : "CANCAN",
         "performance" : {
            "is_limited" : false,
            "running_time" : 120,
            "iso8601_date_and_time" : "2019-04-01T19:00:00+02:00",
            "event_id" : "BPT",
            "perf_id" : "BPT-E6P",
            "date_desc" : "Mon, 1st April 2019",
            "has_pool_seats" : true,
            "is_ghost" : false,
            "time_desc" : "7.00 PM"
         },
         "total_sale_surcharge_in_desired" : 39.41,
         "internal_reserve_sub_ref2" : "",
         "cancellation_status" : "possible",
         "total_sale_seatprice_in_desired" : 367.83,
         "item_number" : 2,
         "total_sale_surcharge" : 45
      }
   ],
   "currency_details" : {
      "eur" : {
         "currency_pre_symbol" : "â¬",
         "currency_post_symbol" : "",
         "currency_number" : 978,
         "currency_code" : "eur",
         "currency_places" : 2,
         "currency_factor" : 100
      }
   }
}
```

```python
from pyticketswitch.cancellation import CancellationResult
...

CancellationResult(
    must_also_cancel=[Order(
        item=2,
        backend_purchase_reference='PURCHASE-286D',
        cancellation_comment='',
        cancellation_status='possible',
        event=Event(
            id='BPT',
            status='live',
            description="Moulin Rouge (Dinner Show)",
            source='External Test Backend 1',
            source_code='ext_test1',
            event_type='simple_ticket',
            venue='Bal du Moulin Rouge',
            classes={
               'attract' : 'Attractions',
               'theatre' : 'Theatre'
            },
            postcode='75018',
            country='France',
            country_code='fr',
            latitude=0,
            longitude=0,
            max_running_time=120,
            min_running_time=120,
            show_performance_time=True,
            has_performances=True,
            is_seated=True,
            needs_departure_date=False,
            needs_duration=False,
            needs_performance=False,
        ),
        performance=Performance(
            id='BPT-E6P',
            event_id='BPT',
            date_time=datetime.datetime(2019, 4, 01, 19, 00, tzinfo=tzoffset(None, 7200)),
            date_description='Mon, 1st April 2019',
            time_description='7.00 PM',
            has_pool_seats=True,
            is_limited=False,
            is_ghost=False,
            running_time=120,
        ),
        price_band_code='A/pool',
        ticket_type_code='CANCAN',
        ticket_type_description='Menu French Cancan',
        ticket_orders=[
            TicketOrder(
                code='',
                seats=[
                    Seat(
                        id='YC472',
                        column='472',
                        row='YC',
                        separator='',
                        is_restricted=False,
                    ),
                    Seat(
                        id='YC473',
                        column='473',
                        row='YC',
                        separator='',
                        is_restricted=False,
                    ),
                    Seat(
                        id='YC474',
                        column='474',
                        row='YC',
                        separator='',
                        is_restricted=False,
                    ),
                ],
                description='',
                number_of_seats=3,
                seatprice=140.0,
                surcharge=15.0,
                total_seatprice=420.0,
                total_surcharge=15.0,
            ),
        ],
        number_of_seats=3,
        total_seatprice=420.0,
        total_surcharge=45.0,
        seat_request_status='not_requested',
        send_method=SendMethod(
            code='POST',
            cost=10,
            description='Post worldwide',
            type='post',
        ),
    )]
)
```


The `cancellation_status` for each order item will become `cancelled` if it has been cancelled.
Some items are not cancellable, their `cancellation_status` will be `not_permitted` in the API requests before cancellation (e.g. [purchase](#purchase) and [status](#status)) and after the cancel API request.
If the cancellation of an order item is tried and fails then the order item `cancellation_status` will become `tried_and_failed`.
In some circumstances the call might return the `must_also_cancel` list, which means that you must also specify the items in that list in order to cancel the items that you requested to cancel originally.



Attribute | Description
--------- | -----------
`cancelled_items_list` | This will list the items that were cancelled in this API request (note that this is not idempotent: items will only appear in this list the first time they are cancelled).
`must_also_cancel` | This is a list of order items that must also be cancelled in order to sucessfully cancel the transaction.
`trolley_contents` | The `trolley` object. The trolley attributes are as described in the [purchase](#purchase) response. Note that if the `transaction_status` is `released` you should ignore the value of `minutes_left_on_reserve`.

