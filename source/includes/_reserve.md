# Reserve
> **Definition**

```
POST https://demo.ticketswitch.com/f13/reserve.v1
```

<aside class="notice">
The reserve, release and purchase calls change state so you must use POST. 
GET can be used for all other calls.
</aside>

Before purchasing tickets, you must first `reserve` them. Reserving tickets will
put them on hold for a period of time, ensuring they cannot be purchased by
anyone else until you explicitly [release](#release) them or until the hold time
expires when the tickets are automatically released. Reserve is typically called
immediately before displaying a checkout page to customers to collect contact 
details. These details are then used as parameters when
[purchasing the reservation](#purchase).

<aside class="notice">
Our current reserve hold lasts 15 minutes.
</aside>

There are two main use cases for calling `reserve`:

1. **You only want to purchase a single order at a time**: call `reserve` with a
`perf_id`, `ticket_type_code`, `price_band_code`, `no_of_seats`, and other
optional parameters if necessary.

2. **You want to purchase multiple orders in a single transaction**: first add 
orders to your [trolley](#trolley), then `reserve` the trolley by
specifying a `trolley_token`.

As with [trolley](#trolley) there are restrictions on reserving individual seats: 

- If the [availability](#availability) response includes 
  `contiguous_seat_selection_only` = `false` then any seats can be selected, 
  provided they are within a single `ticket_type_code` and `price_band_code`.

- If the [availability](#availability) response includes 
  `contiguous_seat_selection_only` = `true` then only contiguous seats within a 
  single `ticket_type_code` and `price_band_code` can be selected. Seats are 
  contiguous based on the order of the id_details seat array.

If you would like to allow your customers to select seats without restriction
across price bands and ticket types, you need to add multiple orders to a
[trolley](#trolley), one order for each ticket type / price band. However there
are currently some restrictions enforced so if you want to do this you will need
to contact us first techsupport@ingresso.co.uk.

### Request

> **Example request - reserving best available tickets, specifying specific discount codes**

```shell
curl https://demo.ticketswitch.com/f13/reserve.v1 \
    -u "demo:demopass" \
    -d "perf_id=6IF-B1S" \
    -d "ticket_type_code=CIRCLE" \
    -d "price_band_code=C/pool" \
    -d "no_of_seats=3" \
    -d "disc0=ADULT" \
    -d "disc1=CHILD" \
    -d "disc2=CHILD" \
    --compressed \
    -X POST
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
reservation, meta = client.make_reservation(
    performance_id='6IF-A7N',
    ticket_type_code='CIRCLE',
    price_band_code='C/pool',
    number_of_seats=3,
    discounts=['ADULT', 'CHILD', 'CHILD']
)
```

> **Example request - reserving specific seats**

```shell
curl https://demo.ticketswitch.com/f13/reserve.v1 \
    -u "demo:demopass" \
    -d "perf_id=7AB-5" \
    -d "ticket_type_code=STALLS" \
    -d "price_band_code=A/pool" \
    -d "no_of_seats=2" \
    -d "seat0=A1" \
    -d "seat1=A2" \
    --compressed \
    -X POST
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
reservation, meta = client.make_reservation(
    performance_id='7AB-5',
    ticket_type_code='STALLS',
    price_band_code='A/pool',
    number_of_seats=2,
    seats=['A1', 'A2']
)
```


> **Example request - reserving orders previously added to a trolley**

```shell
curl https://demo.ticketswitch.com/f13/reserve.v1 \
    -u "demo:demopass" \
    -d "trolley_token=s2--pFMaAzbxn3wG3zY-OAuclGgU9zzsUFJOLXpOquDKNGpwGn205i4_XzD6O6i8ZCqo2qxmA5QdvETbr7DlqTsbLayzHrvvf9zrz2NdGZuggwXHdx3cgPdbJzFeexIylGsxo7d3T9FWUkViv76Rz7qUH8qXeb9nWjF7ahrNsuHA8w_R63XmOQVNDIvJFS1hC6vFO3sD3t0MqKKguqRWuP4mM2vRN6BimgWYqrNqQw5D_-bfumE1Xl2vXu3FgSEp_N9dpsQ1fXK3qfoOiH-Hsd0F2Zh84IyMcrJkGZH8dlhdEWbvaMlu1rQ8Kw6hJMUigc31jbfHjuquCpDgI-OKyV8LQuQGz8wZsxj3jwWmLClcl50W7p1dTVLEYIW52jWqALRYFAresEGqzct0xDCeaoAjZ5vagMS2KXVmZ" \
    --compressed \
    -X POST
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
reservation, meta = client.make_reservation(
    token='s2--pFMaAzbxn3wG3zY-OAuclGgU9zzsUFJOLXpOquDKNGpwGn205i4_XzD6O6i8ZCqo2qxmA5QdvETbr7DlqTsbLayzHrvvf9zrz2NdGZuggwXHdx3cgPdbJzFeexIylGsxo7d3T9FWUkViv76Rz7qUH8qXeb9nWjF7ahrNsuHA8w_R63XmOQVNDIvJFS1hC6vFO3sD3t0MqKKguqRWuP4mM2vRN6BimgWYqrNqQw5D_-bfumE1Xl2vXu3FgSEp_N9dpsQ1fXK3qfoOiH-Hsd0F2Zh84IyMcrJkGZH8dlhdEWbvaMlu1rQ8Kw6hJMUigc31jbfHjuquCpDgI-OKyV8LQuQGz8wZsxj3jwWmLClcl50W7p1dTVLEYIW52jWqALRYFAresEGqzct0xDCeaoAjZ5vagMS2KXVmZ'
)
```

Note that the request parameters and response attributes are similar to
[trolley](#trolley).


Parameter | Description
--------- | -----------
`discX` | Specify a discount code for ticket number X, with zero-based numbering (so to specify the CHILD discount code on the second ticket use `disc1=CHILD`). Note that illegal discount codes are replaced with legal ones. It is optional to specify discount codes, however note that if you choose to specify them you must specify all of them. If you do not specify discount codes you will receive the default discount code - this will match what you are shown when you request availability.
`no_of_seats` | The number of tickets you want to reserve.
`perf_id` | The performance identifier for the tickets that you want to reserve.
`price_band_code` | The price band identifier for the tickets that you want to reserve.
`remove_items_list` | A comma separated list of order `item_number`s that you want to remove from the trolley before reserving.
`seatX` | Specify a specific seat for ticket number X, with zero-based numbering (so to specify seat A12 as the first ticket use `seat0=A12`). If seat numbers are not specified then you will receive best available seats.
`X_send_code` | Specify a send / despatch method for supplier system X. If this is not present it will default to the first send method. For example, to specify the POST send method for the nimax supplier system, use `nimax_send_code=POST`.
`ticket_type_code` | The ticket type identifier for the tickets that you want to add to your trolley.
`trolley_token` | The identifier for a trolley. This is used to reserve multiple items at once.
`req_predicted_commission`| *Optional*. Include to retrieve [commission](#commission) data. For most partners this will include `predicted_user_commission` only (the predicted amount you earn per ticket). Some partners will also see `predicted_gross_commission`, which is the total commission available to be shared between Ingresso and our partner. By default you will see `predicted_user_commission` only - if you think you need to see `predicted_gross_commission` as well then please get in touch.


Additional parameters, primarily for internal use:

Parameter | Description
--------- | -----------
`departure_date` | Specify a departure date for example `departure_date=20170214`.
`duration` | Specify a duration for hotel product, for example `duration=3`.
`promo_code` | Specifying a promo codes can unlock a special offer discount code. If you have a promo code it can be specified like this: `promo_code=FOO`. Note that this feature is not commonly used with partners.

All of the following parameters can also be used to request additional data for the event and performance in each order. These are described in more detail in the Events section [here](#additional-parameters).

Parameter | Description
--------- | -----------
`req_avail_details` | Returns [availability details](#availability-details) - a cached list of unique ticket types and price bands available for the event and performance in each order. **This parameter is not commonly used.**
`req_avail_details_with_perfs` | This will add the list of available performance dates to each avail detail object. *Only valid if used alongside req_avail_details*.
`req_cost_range` | Returns [cost ranges](#cost-range) - a from price and offer detail for the event and performance in each order.
`req_cost_range_best_value_offer` | Returns the offer with the highest percentage saving.
`req_cost_range_details` | Returns a list of unique ticket types and price bands and their cost ranges across all performances. **This parameter is not commonly used.**
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

> **Example response - reserving orders previously added to a trolley**

```shell
{
  "allowed_countries": {
    "ad": "Andorra",
    "ae": "United Arab Emirates",
    "af": "Afghanistan",
    "ag": "Antigua and Barbuda",
    ....
    "uk": "United Kingdom",
    "um": "The United States Minor Outlying Islands",
    "us": "United States of America",
    "uy": "Uruguay"
  },
  "can_edit_address": true,
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
  "minutes_left_on_reserve": 15,
  "needs_agent_reference": false,
  "needs_email_address": false,
  "needs_payment_card": false,
  "prefilled_address": {
    "country_code": "uk"
  },
  "trolley_contents": {
    "bundle": [
      {
        "bundle_order_count": 1,
        "bundle_source_code": "ext_test0",
        "bundle_source_desc": "External Test Backend 0",
        "bundle_total_cost": 76.5,
        "bundle_total_seatprice": 75,
        "bundle_total_send_cost": 1.5,
        "bundle_total_surcharge": 0,
        "currency_code": "gbp",
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
            "got_requested_seats": false,
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
              "send_type": "collect"
            },
            "ticket_orders": {
              "ticket_order": [
                {
                  "discount_code": "ADULT",
                  "discount_desc": "Adult",
                  "no_of_seats": 3,
                  "sale_seatprice": 25,
                  "sale_surcharge": 0,
                  "seats": [
                    {
                      "col_id": "424",
                      "full_id": "GL424",
                      "is_restricted_view": false,
                      "row_id": "GL"
                    },
                    {
                      "col_id": "421",
                      "full_id": "GL421",
                      "is_restricted_view": false,
                      "row_id": "GL"
                    },
                    {
                      "col_id": "418",
                      "full_id": "GL418",
                      "is_restricted_view": false,
                      "row_id": "GL"
                    }
                  ],
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
        ]
      }
    ],
    "transaction_uuid": "e18c20fc-042e-11e7-975c-002590326962",
    "trolley_bundle_count": 1,
    "trolley_order_count": 1
  },
  "unreserved_orders": []
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
        transaction_uuid='6d080a78-3011-11e7-b228-0025903268dc',
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

The response will include an identifier for the reserve, detail of the reserved
items, detail of items that weren't able to be reserved, and a set of flags 
that confirm the information you need to collect from your customer when 
purchasing tickets.

<aside class="notice">
If you are allowing customers to select their own seats it is important to check
`seat_request_status` - if this is not `got_all` then we weren't able to reserve
the requested seats. If you don't want to offer these seats to your customer 
then you must release them. Note also that it is possible for reserving specific 
seats to consistently fail - it is therefore advisable to warn your customer of 
this to avoid them receiving errors consistently.
</aside>


Attribute | Description
--------- | -----------
`accepted_payment_cards` | This should not be present for partners and can be ignored.
`allowed_countries` | A list of country codes and human-readable descriptions. The country codes are [ISO 3166-1 alpha-2 codes](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2). This list is relevant when selecting a despatch method that is restricted to certain countries, for example a local post despatch method may only be available in the supplier's country. These are typically presented to customers in a dropdown list on a checkout page. The customer's country code is a required parameter when [purchasing](#purchase) tickets.
`can_edit_address` | Indicates whether it is possible to change the non-blank `prefilled_address` data. This is primarily for internal use and can be ignored for normal use cases (it only applies to partners who have asked to have a prefilled address held in the Ingresso system). In most cases this will be `true`. If it is `false` the non-blank `prefilled_address` fields will be used when tickets are purchased and we will ignore the values in these fields if they are passed to us.
`input_contained_unavailable_order` | This is a flag that will get set to `true` if part of the reservation request could not be reserved. This occurs when we weren't able to find any available products that matched the request and it usually means the user has passed in invalid parameters or is requesting an item that is sold out.
`needs_agent_reference` | For the vast majority of partners this will be `false` and can be ignored. However some partners ask to require that they specify their own reference when purchasing tickets - if this is `true` then the reference must be passed in.
`needs_email_address` | If `true` you must pass in an email address when purchasing tickets. We recommend that you always send us the customer's email address. We guarantee to only use this if our customer service team need to contact your customer in an emergency or if our supplier needs to directly email confirmation to your customer (this is not used for any events as at Jan 2017, but is possible in future). If you cannot send the customer's email address then we recommend that you don't send an email address if `needs_email_address` is `false`; if it is `true` then send an internal email address (for example your customer service email address) and it will be your responsibility to contact the customer or pass on booking confirmation.
`needs_payment_card` | This will be `false` for all partners so can be ignored. If this is `true` you need to collect card details from the customer and include them when purchasing.
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
`supports_billing_address` | For the vast majority of partners this can be ignored (it isn't relevant to partners who purchase on-credit). When it is `true` a separate address may be provided when purchasing tickets, and this will be used to validate the payment card address for venue systems that perform address verification checks.
`unreserved_orders` | A list of orders that could not be reserved.


**`reserved_trolley` attributes:**

Attribute | Description
--------- | -----------
`bundle` | See below for object detail.
`minutes_left_on_reserve` | A numeric representation of the number of minutes that your reserve will be held open for. For example, 12.5 is 12 minutes and 30 seconds.
`transaction_uuid` | The unique reference for this reservation. This is needed for any further interactions with the reservation.
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
`currency_code` | The currency code for the price - further detail for the currency can be found in the `currency_details` object, described below.
`debitor_choices` | A list of debitors. See below for object detail. 
`desired_currency_code` | Most partners will not see a `desired_currency_code`, but it is possible for a partner to request to always view prices in a specific currency. For example if a product is supplied in GBP, but your `desired_currency_code` is set to USD, we will return prices in GBP along with `_desired` prices in USD. If you are taking payment using Ingresso's Stripe connection, then payment will be taken in the default currency (GBP, in the example), and if you are being invoiced by Ingresso we will invoice you in the default currency (GBP, in the example). So the `_desired` prices are typically used to just display an indicative amount to the customer (in USD, in the example). Currency conversion rates are taken from a daily European Central Bank feed.
`order` | See below for object detail.


Debitor data is returned when payment should be taken via Stripe. Stripe 
integration is described in detail within [purchase](#purchasing-with-stripe).

**`debitor` attributes:**

Attribute | Description
--------- | -----------
`debitor_desc` | A human-readable description of the debitor.
`debitor_integration_data` | Data you will need t
`debitor_name` | The name of the debitor. For Stripe transactions this will be `stripe`.
`debitor_type` | The type of the debitor. For Stripe transactions this will be `stripe`.



**`order` attributes:**

Attribute | Description
--------- | -----------
`event` | The [event](#event-object) for this order.
`item_number` | A unique sequential number for the `order`. The second order added to the trolley will have `item_number` 2. The `item_number` remains constant as orders are added or removed, so if `item_number` 1 is removed, the first order will be `item_number` 2.
`performance` | The [performance](#performance-object) for this order.
`price_band_code` | The code for a price band, for example "C/pool". The price band code is generally made up of the code from the underlying supplier system, e.g. "C", followed by a "/" separator then "pool" or "alloc", indicating whether the price band is taken from the general pool of tickets or is from a ring-fenced allocation.
`seat_request_status` | The status of your tickets after they have been reserved. Possible values are `not_requested` (specific seats not requested), `got_none` (you requested A13 and A14 but we gave you A15 and A16), `got_partial` (you requested A13 and A14 but we gave you A14 and A15), `got_all` (you requested A13 and A14 and you got A13 and A14 - by far the most common response when requesting specific seats). 
`ticket_orders` | An array of ticket_order objects, one for each discount code. See below for detail.
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
`no_of_seats` | The number of tickets in this ticket_order.
`sale_seatprice` | The face value per ticket in this ticket_order.
`sale_seatprice_in_desired` | The face value per ticket in this ticket_order, converted to your `desired_currency`. This field will not be present for most partners.
`sale_surcharge` | The booking fee per ticket in this ticket_order.
`sale_surcharge_in_desired` | The booking fee per ticket in this ticket_order, converted to your `desired_currency`. This field will not be present for most partners.
`seats` | For seated events, an array of reserved seats. See below for detail.
`total_sale_seatprice` | The total face value of all tickets in this ticket_order.
`total_sale_seatprice_in_desired` | The total face value of all tickets in this ticket_order, converted to your `desired_currency`. This field will not be present for most partners.
`total_sale_surcharge` | The total booking fee of all tickets in this ticket_order.
`total_sale_surcharge_in_desired` | The total booking fee of all tickets in this ticket_order, converted to your `desired_currency`. This field will not be present for most partners.


**`seats` attributes:**

Attribute | Description
--------- | -----------
`col_id` | The column identifier of the seat.
`full_id` | The ID of the seat - comprised of the `col_id` and the `row_id`, sometimes with a separator between them.
`is_restricted_view` | `true` if the seat is classified as having a restricted view.
`row_id` | The row identifier of the seat.
`seat_text` | A message about the seat that must be displayed to customers.
`seat_text_code` | An identifier for the seat text (only unique within the current `bundle_source_code`). Not useful for most partners.


The outer object includes a **`currency_details`** object containing one currency
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
