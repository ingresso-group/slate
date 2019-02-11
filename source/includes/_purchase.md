# Purchase
> **Definition**

```
POST https://demo.ticketswitch.com/f13/purchase.v1
```

To purchase tickets you must first [reserve](#reserve) them. You can then
attempt to purchase the reserved tickets at any time within the reserve time
(`minutes_left_on_reserve`). A successful result from `purchase` means that the
order has been confirmed in the supplier ticketing system. Note that if you have
attempted to purchase multiple events from a single trolley it is possible for a
purchase to partially succeed, with some events unable to be purchased from the
corresponding supplier ticketing system.

If a purchase fails due to incomplete or incorrect customer data then you can
reattempt the purchase call with corrected data. If a purchase fails due to the
reservation having timed out, then it is necessary to [reserve](#reserve)
tickets again. The output data is structured such that permanent failures are
not reported as errors, but are reported as an actual result from a successful
operation.

For purchases where a despatch method with a type of `selfprint` was selected,
the URL returned should be presented to the customer.


Your [payment option](#payment-options) determines how you should call purchase.
The two main options are:

### On-credit purchasing

You take payment using a payment provider of your own choosing. When calling
purchase you should just provide customer data, and the purchase call will
return a success or failure.

### Stripe 

Stripe is a developer-friendly payment provider that is simple to integrate
with. The Ingresso API provides a payment processing engine that gives you
integration support for Stripe and other payment sources. You pass us a Stripe
token or 3D Secure source generated on your checkout page, and we handle the
rest. We support partners collecting payment via their own Stripe account or
into Ingresso's Stripe account.

In either case, you need integrate against
[Stripe.js](https://stripe.com/docs/stripe-js/reference) and collect a Stripe
token after your customer enters their payment details.
The token or source information is passed to Ingresso before making the
purchase call.

If you want to collect payment via your own Stripe account you will need to get
an agreement with us to sell tickets on credit, and we will invoice you for
the ticket price less your commission. You will need to provide Ingresso your
Stripe keys to allow us to process payment on your behalf. 

If you wish to use Ingresso's Stripe account we have some requirements 
to reduce fraud including implementing 3D Secure support and passing in the 
customer's IP address, so please get in touch with us first. We however encourage 
partners to open their own Stripe account rather than use ours.

In the examples below we will describe how to purchase using the on-credit and
Stripe options in more detail. We then describe how to handle generic redirects
which are needed for other supported payment providers such as Global Collect.
We recommend that all partners that wish to use Stripe also implement generic
redirects - this is a small amount of additional development work that ensures 
you will be able to accept alternate payment methods both now and in future.


### Notes on sending customer data

* You must pass in the customer name, phone number and address for the purchase
to succeed, and in some cases the email address is also required. Some partners
do not wish to share customer data. At a minimum you should send us the customer
name (first and last name) and phone number.

* The customer name is used by the venue, in particular as a security measure 
when admitting customers, so this is essential.

* The phone number can be used by Ingresso or the venue in emergency
circumstances to contact the customer (for example if an evening performance is
cancelled in the morning - this is not uncommon for theatre shows). It will not
be used for any other reason. We therefore highly recommend that you provide the
customer's phone number to avoid a bad customer experience should they need to
be contacted.

* The email address is only required if `needs_email_address` = `true` in the
response from [reserve](#reserve). We recommend that you always send us the
customer's email address. We guarantee to only use this if our customer service
team need to contact your customer or if our supplier needs to directly email
confirmation to your customer (this is not used for any events as at March 2017,
but is possible in future). If you cannot send the customer's email address then
we recommend that you don't send any email address if `needs_email_address` is
`false`; if it is `true` then send an internal email address (for example your
customer service email address) and it will be your responsibility to contact
the customer or pass on booking confirmation.

* Please note that some vendor systems require customer email addresses to be
  unique, as they create an internal customer object connected to the booking.
  In this case, subsequent purchases using the same email address may fail or
  overwrite customer names from previous purchases, so please let us know if you
  plan to use an internal email address and we will ensure that the purchases
  for these vendor systems use a unique email address per-transaction.

* The address fields (for example `address_line_one`) need to be passed to us.
The only use for these is for tickets that will be posted by Ingresso or the
supplier, so it is possible to only pass the customer's address when the
customer has selected a despatch method with `send_type` = `post`. In all other
cases you can pass a default address such as your company headquarters (in case
it turns out that tickets need to be unexpectedly posted). Note that if you are 
purchasing with Stripe and are using 
[AVS](https://support.stripe.com/questions/what-is-avs) (this is common) then 
you may wish to allow your customer to enter separate post or zip codes - one to
submit to Ingresso (postal address) and one to submit to Stripe (billing address).

* If the `send_type` = `post` and you are purchasing with Stripe you may 
wish to give customers the option of entering a separate billing address (if 
the zip or postal code passed to Stripe is not where the customer wants the 
tickets to be posted to). Note that you may not wish to support separate
addresses to discourage fraud.


### Confirmation Emails

After a successful purchase it is generally a good idea to send your customer a
confirmation email reminding them of the items that they have purchased and
including any relevant information such as directions or self print vouchers.

Ingresso can do this for you via the `send_confirmation_email` flag on the
purchase call, or alternatively you can implement your own confirmation emails
using the results of the `status.v1` call.

Ingresso can style these emails to conform with your businesses branding. For
more information drop us an email at
[affiliate@ingresso.co.uk](mailto:affiliate@ingresso.co.uk).


## Purchasing on credit

### Request

> **Example request - purchasing on-credit**

```shell
curl https://demo.ticketswitch.com/f13/purchase.v1 \
    -u "demo:demopass" \
    -d "transaction_uuid=61cfd4eb-1f5b-11e7-b228-0025903268dc" \
    -d "first_name=Test" \
    -d "last_name=Tester" \
    -d "address_line_one=Metro Building" \
    -d "address_line_two=1 Butterwick" \
    -d "town=London" \
    -d "county=London" \
    -d "postcode=W6 8DL" \
    -d "country_code=uk" \
    -d "phone=0203 137 7420" \
    -d "user_can_use_customer_data=true" \
    -d "email_address=testing@gmail.com" \
    -d "send_confirmation_email=true" \
    --compressed \
    -X POST
```

```python
from pyticketswitch import Client
from pyticketswitch.customer import Customer


client = Client('demo', 'demopass')

customer = Customer(
    first_name='Test',
    last_name='Tester',
    address_lines=['Metro Building', '1 Butterwick'],
    town='London',
    county='London',
    post_code='W6 8DL',
    country_code='uk',
    phone='0203 137 7420',
    email='testing@gmail.com',
    user_can_use_customer_data=True,
)

status, callout, meta = client.make_purchase(
    '61cfd4eb-1f5b-11e7-b228-0025903268dc',
    customer,
    send_confirmation_email=True,
)
```

This section describes how to purchase on credit. 

Parameter | Description
--------- | -----------
`address_line_one` | The first line of the customer's address - required. Either pass your customer's address or your head office address (see customer data note above). 
`address_line_two` | The second line of the customer's address. *Optional if `address_line_one` is provided.*
`agent_reference` | Partners can pass their own transaction reference to be stored in the Ingresso platform. *Optional.*
`country_code` | The [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) country code of the customer's address - required. The country code must have been present in the `allowed_countries` list from [reserve](#reserve).
`county` | The county of region of the customer's address. *Optional.*
`email_address` | The customer's email address. Required when `needs_email_address` was `true` from the [reserve](#reserve) response. We recommend this is provided - see customer data note above.
`first_name` | The customer's first name - required. Used by venues as a security measure when admitting customers.
`home_phone` | The customer's home phone number. *Optional if `phone` is provided.*
`initials` | The customer's initials for their name. *Optional.*
`last_name` | The customer's last name - required. Used by venues as a security measure when admitting customers.
`phone` | The customer's phone number - required. Used by venues and Ingresso as a means to contact customers (see customer data note above). It is optional if you specify the `work_phone` and `home_phone` separately instead.
`postcode` | The ZIP or postal code of the customer's address. *Optional.*
`suffix` | The suffix of the customer's name, for example "Jr." or "CPA" *Optional.*
`send_confirmation_email` | When set to `true` Ingresso will send your customer a confirmation email with details of their purchase (including a link to a self print voucher when applicable). If you would prefer to send your own confirmation emails then do not specify this flag. *Requries the `email_address` parameter to be provided.*
`supplier_can_use_customer_data` | Data protection question - set this to `true` if the customer has opted in to receiving marketing emails from the ticket supplier. *Optional - it will default to false.*
`title` | The title of the customer's name, for example "Mr." or "Dr." *Optional.*
`town` | The city or town of the customer's address. *Optional.*
`transaction_uuid` | The unique reference for the reserved tickets, taken from the [reserve](#reserve) response.
`user_can_use_customer_data` | Data protection question - set this to `true` if the customer has opted in to receiving marketing emails from you. Most partners manage marketing opt-ins themselves so do not provide this. Ingresso will never send marketing communications to your customers based on this parameter. *Optional - it will default to false.*
`work_phone` | The customer's work phone number. *Optional if `phone` is provided.*
`world_can_use_customer_data` | Data protection question - set this to `true` if the customer has opted in to receiving marketing emails communication from you and third-parties. *Optional - it will default to false.*

All of the parameters used to request [additional data for events](#additional-parameters) can also be added.


### Response

> **Example response - purchasing on-credit**

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


Attribute | Description
--------- | -----------
`customer` | See below for object detail.
`language_list` | Not useful for most partners, can be ignored.
`purchase_iso8601_date_and_time` | The purchase time in ISO 8601 format.
`reserve_iso8601_date_and_time` | The reserve time in ISO 8601 format.
`transaction_status` | `purchased`, `failed` or `attempting` (will only be seen if the customer is being redirected to a payment page - most partners will not see this).
`trolley_contents` | See below for object detail.


**`customer` attributes:**

Attribute | Description
--------- | -----------
`addr_line_one` | The first line of the customer's address.
`addr_line_one_latin` | The first line of the customer's address, converted to Latin characters (this should allow a European latin alphabet language speaker to read an address containing only Japanese or Chinese characters, for example).
`addr_line_two` | The second line of the customer's address, if provided.
`addr_line_two_latin` | The second line of the customer's address, if provided, converted to Latin characters.
`agent_ref` | A partner transaction reference, if this was passed in as a parameter.
`country` | The country from the customer's address.
`country_code` | The [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) country code of the customer's address.
`country_latin` | The country, converted to Latin characters.
`county` | The county from the customer's address, if provided.
`county_latin` | The county, converted to Latin characters.
`dp_supplier` | Has the customer opted in to receiving marketing emails from the ticket supplier.
`dp_user` | Has the customer opted in to receiving marketing emails from you.
`dp_world` | Has the customer opted in to receiving marketing emails communication from you and third-parties.
`email_addr` | The customer's email address, if provided.
`first_name` | The customer's first name.
`first_name_latin` | The customer's first name, converted to Latin characters.
`home_phone` | The customer's home phone number.
`initials` | The initials of the customer's name, if provided.
`initials_latin` | The customer's initials, converted to Latin characters.
`last_name` | The customer's surname.
`last_name_latin` | The customer's surname, converted to Latin characters.
`postcode` | The customer's postcode, if provided.
`postcode_latin` | The customer's postcode, converted to Latin characters.
`suffix` | The suffix of the customer's name, if provided.
`suffix_latin` | The customer's suffix, converted to Latin characters.
`title` | The title of the customer's name, if provided.
`title_latin` | The customer's title, converted to Latin characters.
`town` | The customer's town or city, if provided.
`town_latin` | The customer's town, converted to Latin characters.
`work_phone` | The customer's work phone number.


**`trolley_contents` attributes:**

Attribute | Description
--------- | -----------
`bundle` | An array of bundles. A bundle  is a group of orders purchased from a single supplier ticketing system. See below for object detail.
`purchase_result.is_partial` | `true` if you attempted to purchase items from more than one supplier ticketing system (therefore you attempted to purchase multiple bundles), but only some of the purchases were successful.
`purchase_result.success` | `true` if the purchase succeeded if at least one of the bundles succeeded.
`transaction_id` | The primary Ingresso transaction reference.
`transaction_uuid` | The secondary Ingresso transaction reference.
`trolley_bundle_count` | The number of bundles in the trolley (a bundle is a group of orders purchased from a single supplier ticketing system).
`trolley_order_count` | The number of orders in the trolley (an order is tickets for a single event).


**`bundle` attributes:**

Attribute | Description
--------- | -----------
`bundle_order_count` | The number of orders (i.e. events) in this bundle.
`bundle_source_code` | The code Ingresso use to identify the supplier ticketing system.
`bundle_source_desc` | The description of the supplier ticketing system.
`bundle_total_cost` | `bundle_total_seatprice` + `bundle_total_surcharge` + `bundle_total_send_cost`.
`bundle_total_cost_in_desired` | `bundle_total_seatprice_in_desired` + `bundle_total_surcharge_in_desired` + `bundle_total_send_cost_in_desired`.
`bundle_total_seatprice` | The total face value.
`bundle_total_seatprice_in_desired` | The total face value in your desired currency. This field won't be present unless you have requested a desired currency.
`bundle_total_send_cost` | The total cost for despatching tickets.
`bundle_total_send_cost_in_desired` | The total despatch cost in your desired currency. 
`bundle_total_surcharge` | The total booking fee.
`bundle_total_surcharge_in_desired` | The total booking fee in your desired currency.
`currency` | The currency of the costs.
`desired_currency` | Your desired currency, if this has been requested.
`order` | See below for object detail.
`purchase_result.is_semi_credit` | **Legacy - can be ignored.**
`purchase_result.success` | `true` if the purchase succeeded for this bundle.


**`order` attributes:**

Attribute | Description
--------- | -----------
`backend_purchase_reference` | The purchase reference from the supplier ticketing system. *This reference should be included on customer confirmation emails and self-print vouchers* as this is the only reference that the venue will recognise.
`event` | The event object, with the same format as for the [events endpoint](#events-by-id).
`gross_commission` | The total commission available to both Ingresso and the partner. This will only be visible for partners on margin share agreements or where Ingresso take a fixed fee. If you believe you should have gross_commission visible please contact us techsupport@ingresso.co.uk.
`gross_commission.amount_excluding_vat` | The total commission amount excluding sales tax.
`gross_commission.amount_including_vat` | The total commission amount including sales tax.
`gross_commission.commission_currency` | The commission currency (note that this can be different to the currency seen by the customer).
`item_number` | Each order item within a transaction has an item_number, starting at 1.
`performance` | The performance object, with the same format as for the [performances endpoint](#performances-by-id).
`price_band_code` | The code for a price band, for example "C/pool". The price band code is generally made up of the code from the underlying supplier system, e.g. "C", followed by a "/" separator then "pool" or "alloc", indicating whether the price band is taken from the general pool of tickets or is from a ring-fenced allocation.
`seat_request_status` | The status of your tickets after they have been reserved. Possible values are `not_requested` (specific seats not requested), `got_none` (you requested A13 and A14 but we gave you A15 and A16), `got_partial` (you requested A13 and A14 but we gave you A14 and A15), `got_all` (you requested A13 and A14 and you got A13 and A14 - by far the most common response when requesting specific seats).
`send_method` | See below for object detail.
`ticket_orders` | An array of ticket_order objects, one for each discount code. See below for detail.
`ticket_type_code` | The unique identifier for the ticket type. For seated events this refers to a part of house / seating area such as Grand Circle.
`ticket_type_desc` | The description for the ticket type. This should be displayed to the customer
`total_no_of_seats` | The number of seats for this order.
`total_sale_seatprice` | The total face value for this order.
`total_sale_seatprice_in_desired` | The total face value for this order, converted to your `desired_currency`. This field will not be present for most partners.
`total_sale_surcharge` | The total booking fee for this order.
`total_sale_surcharge_in_desired` | The total booking fee for this order, converted to your `desired_currency`. This field will not be present for most partners.
`user_commission` | The commission you earn from this transaction. 
`user_commission.amount_excluding_vat` | Your commission excluding sales tax.
`user_commission.amount_excluding_vat` | Your commission excluding sales tax.
`user_commission.amount_including_vat` | Your commission including sales tax.
`user_commission.commission_currency` | The commission currency (note that this can be different to the currency seen by the customer).


**`send_method` attributes:**

Attribute | Description
--------- | -----------
`can_generate_self_print` | `true` if you have been set up to produce your own self print vouchers (eTickets), `false` if you are using Ingresso-provided vouchers.
`has_html_page` | `true` if an HTML voucher is returned.
`self_print_voucher_url` | The voucher / eTicket URL. This should be displayed to your customer.
`send_code` | The code for this send method.
`send_cost` | The despatch cost for the chosen send method.
`send_cost_in_desired` | The despatch cost for the chosen send method, converted to your desired currency if you have one (in most cases this won't be present).
`send_desc` | The description for this send method.
`send_final_comment` | . Will not always be present.
`send_final_type` | The classification of send method: one of `selfprint`, `collect` or `post`. The final_type should be the same as the type.
`send_type` | The classification of send method: one of `selfprint`, `collect` or `post`.


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


## Purchasing with Stripe

Purchasing with Stripe requires your API user to be set up to use the Ingresso
Payments service, which will require contacting us. If you are interested in
testing this functionality, the `demo-stripe` user has been created which is
already set up this way. The password is the same as for the `demo` user.
After creating a reservation with this user, you can proceed to test purchasing
with Stripe, as outlined below.

In order to purchase with Stripe you need the following steps:

1. Retrieve a Stripe token or payment source (further detail below).
2. Save the Stripe token or payment source in our payment processing engine,
   which will return a unique token as a reference
3. Call `purchase.v1` passing in the payment engine reference from step 2.

Note that you need to retrieve and pass in one payment engine token for each `bundle` that you
reserve. If you only support the purchase of one item at a time, or if you don't
use the Ingresso API to help manage basketing then you can ignore this.

### Generating a Stripe token
Stripe collects payment information on your behalf, and returns a single-use 
token. The payment information can either be collected on your own checkout page
in a PCI-compliant manner ("Elements"), with a mobile-friendly form 
("Checkout"), or via their mobile SDKs. See [Stripe's Quickstart guide](https://stripe.com/docs/quickstart).

You will need to provide Stripe with the appropriate publishable key -
this is returned by Ingresso in the [reserve call](#reserve) as
bundle.debitor.debitor_integration_data.stripe.publishable_key.

For anti-fraud reasons, we recommend generating a 3D Secure payment source
as per the [Stripe docs](https://stripe.com/docs/sources/three-d-secure), and
this is one of the requirements for using the Ingresso Stripe account.

### Saving the Stripe token in the Ingresso Payments system

> **Example saving payment details**

```shell
curl https://payments.ingresso.co.uk/api/save-details \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer key_XYZ789123" \
    -X POST \
    -d @- << EOF
{
    "stripe_source": "src_ABCD1234",
    "stripe_source_amount": 6250,
    "stripe_meta_email_address": "customer@example.com",
    "stripe_meta_event_ids": "6IF",
    "stripe_meta_send_methods": "collect",
    "stripe_meta_days_to_performance": 37,
    "stripe_meta_user_id": "demo",
    "stripe_meta_ip_address": "101.102.103.104"
}
EOF
```

```python
# We suggest Javascript integration on the front end, but you can use
# a HTTP requests library if you'd prefer.
```

```javascript
const integration_data = {
    cider_api_endpoint: "https://payments.ingresso.co.uk/api",
    cider_api_token: "key_XYZ789123",
};

// get Stripe token
let stripeToken = "tok_visa";

const http = new XMLHttpRequest();
http.open('POST', integration_data.cider_api_endpoint + '/save-details', true);
http.setRequestHeader('Content-type', 'application/json');
http.setRequestHeader('Authorization', 'Bearer ' + integration_data.cider_api_key);
http.onload = () => {
    if (http.status === 200) {
        // get token from response
    } else {
        // handle error
    }
};
http.onerror = () => console.log("Unable to connect");
http.ontimeout = () => console.log("Connection timed out.");
http.send(JSON.stringify({ stripe_token: stripeToken }));
```

> **Example response**

```shell
{"token": "cider_XYZ789123"}
```

Once you have the Stripe token, you must save it in Ingresso's payment
processing Engine (called Cider). The API endpoint you can use is also found in
the [reserve call](#reserve) integration data, as
`bundle.debitor.debitor_integration_data.cider_api_endpoint`. You will need to
use a unique key per reservation, found at
`debitor_integration_data.cider_api_token`.

The API endpoint will look something like `https://payments.ingresso.co.uk/api`
and you can save the stripe token or source generated above by making a HTTP
`POST` request to this endpoint + `/save-details`, and authenticating with the
key provided in the integration data.

In addition to saving stripe tokens and payment sources, you can also save
metadata about the purchase to this endpoint, which will be saved in Stripe
against the purchase record. This is very important for anti-fraud rules when
using the Ingresso Stripe account, and may be useful for your own account too.

You must pass in the actual customer's `remote_ip` if you are taking payment via 
Ingresso's Stripe account - we use this for fraud checks. It is also useful to
pass in the `remote_site` so we know which website the customer purchased on.

One primary advantage of saving the details in this way is that it will not
appear in Ingresso's logs or database, and is only used as metadata in Stripe,
so it reduces the spread of personally identifiable information. Please note
that the call must include a valid Authorization header with the key included in
the reservation response debitor integration data, and should be well-formed
JSON, as per the examples.

### Making the purchase call
Retrieving the Stripe token is the first half of the payment process - further
server-side code is required to complete the second half. You don't need to 
worry about this as Ingresso's payment processor takes care of it. Once you 
have saved the Stripe token and retrieved the cider token, we will handle the
following steps:

* Authorise the payment with Stripe.

* Purchase your reserved tickets with the supplier ticketing system. *If this 
step fails we automatically refund the Stripe payment.*

* Capture the payment with Stripe.


### Purchase request

> **Example purchase request - Stripe**

```shell
curl https://demo.ticketswitch.com/f13/purchase.v1 \
    -u "demo-stripe:demopass" \
    -d "transaction_uuid=2e740db5-1b65-11e7-9e97-002590326932" \
    -d "first_name=Test" \
    -d "last_name=Tester" \
    -d "address_line_one=Metro Building" \
    -d "address_line_two=1 Butterwick" \
    -d "town=London" \
    -d "county=London" \
    -d "postcode=W6 8DL" \
    -d "country_code=uk" \
    -d "phone=0203 137 7420" \
    -d "email_address=tester@gmail.com" \
    -d "ext_test0_callback/cider_token=cider_XYZ789123" \
    -d "send_confirmation_email=yes" \
    -d "remote_ip=101.102.103.104" \
    -d "remote_site=www.myticketshop.com" \
    --compressed \
    -X POST
```

```python
from pyticketswitch import Client
from pyticketswitch.customer import Customer
from pyticketswitch.payment_methods import CiderDetails


client = Client('demo-stripe', 'demopass')

customer = Customer(
    first_name='Test',
    last_name='Tester',
    address_lines=['Metro Building', '1 Butterwick'],
    town='London',
    county='London',
    post_code='W6 8DL',
    country_code='uk',
    phone='0203 137 7420',
    email='testing@gmail.com',
    user_can_use_customer_data=True,
)

cider_details = CiderDetails(
    {'cider_token': 'cider_XYZ789123'},
    ['ext_test0'],
)

status, callout, meta = client.make_purchase(
    '7150d25f-301b-11e7-bede-0025903268a0',
    customer,
    payment_method=cider_details,
    send_confirmation_email=True,
)
```


To complete a test Stripe purchase:

1. Call [reserve](#reserve)
2. Enter test card details into the [Stripe Elements example form](https://stripe.com/docs/elements) and copy the Stripe token returned.
3. Save the Stripe token in the Ingresso Payments processing service (Cider)
4. Call purchase, replacing the transaction_uuid and ext_test0_callback/cider_token with the values from the 3 steps above.

<aside class="notice">If you are using our Postman examples, the relevant calls are "reserve - best available - stripe user" and "purchase - stripe user".</aside>

All of the parameters mentioned above in the 
[purchasing on credit](#purchasing-on-credit) section are used. In addition the 
following parameter should be specified:

Parameter | Description
--------- | -----------
`remote_ip` | The end customer's IP address.
`remote_site` | The website the end customer is purchasing from, not including `https://` or `http://`.
`X_callback/cider_token` | The single-use token retrieved from Cider after saving the stripe token or payment source being used. You should replace `X` with the `source_code` returned by `reserve`. So for `source_code=ext_test0` the parameter name used is `ext_test0_callback/stripeToken`. If you support basketing and your customer is attempting to purchase items across multiple bundles, you should provide the same Cider token for each bundle (each bundle has a unique `source_code`).


### Purchase response
> **Example purchase response - Stripe**

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
    "dp_supplier": false,
    "dp_user": false,
    "dp_world": false,
    "email_addr": "tester@gmail.com",
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
    "title": "",
    "title_latin": "",
    "town": "London",
    "town_latin": "London",
    "work_phone": "0203 137 7420"
  },
  "language_list": "en-gb,en,en-us,nl",
  "purchase_iso8601_date_and_time": "2017-03-03T15:58:26Z",
  "reserve_iso8601_date_and_time": "2017-03-03T15:58:01Z",
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
            "backend_purchase_reference": "PURCHASE-17BB2-1",
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
                      "col_id": "419",
                      "full_id": "OR419",
                      "is_restricted_view": false,
                      "row_id": "OR"
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
                      "col_id": "416",
                      "full_id": "OR416",
                      "is_restricted_view": false,
                      "row_id": "OR"
                    },
                    {
                      "col_id": "413",
                      "full_id": "OR413",
                      "is_restricted_view": false,
                      "row_id": "OR"
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
              "amount_excluding_vat": 0,
              "amount_including_vat": 0,
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
    "transaction_id": "T000-0000-8N66-K30B",
    "transaction_uuid": "2dac0d00-002a-11e7-975c-002590326962",
    "trolley_bundle_count": 1,
    "trolley_order_count": 1
  }
}
```

```python
from pyticketswitch.customer import Customer
from pyticketswitch.trolley import Trolley
from pyticketswitch.debitor import Debitor
from pyticketswitch.status import Status
from pyticketswitch.seat import Seat
from pyticketswitch.performance import Performance
from pyticketswitch.order import TicketOrder
from pyticketswitch.purchase_result import PurchaseResult
from pyticketswitch.event import Event
from pyticketswitch.send_method import SendMethod
from pyticketswitch.order import Order
from pyticketswitch.bundle import Bundle

# stripe purchases should always return a status object, and no callout.
Status(
    status='purchased',
    reserved_at=datetime.datetime(2017, 5, 3, 16, 13, 28, tzinfo=tzutc()),
    purchased_at=datetime.datetime(2017, 5, 3, 16, 14, 19, tzinfo=tzutc()),
    trolley=Trolley(
        transaction_uuid='7150d25f-301b-11e7-bede-0025903268a0',
        transaction_id='T000-0000-8ZW5-68A8',
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
                                        id='JM189',
                                        column='189',
                                        row='JM',
                                        separator='',
                                        is_restricted=False,
                                    ),
                                    Seat(
                                        id='JM190',
                                        column='190',
                                        row='JM',
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
                        backend_purchase_reference='PURCHASE-11F22-1',
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
                debitor=Debitor(
                    type='cider',
                    name='cider',
                    description='Cider',
                    integration_data={
                        'cider_api_token': 'key_XYZ789123',
                        'cider_api_endpoint': 'https://payments.ingresso.co.uk/api',
                        'cider_js': 'https://payments.ingresso.co.uk/static/js/cider/cider.js',
                        'merchant_group_country_code': 'gb',
                        'stripe': {
                            'publishable_key': 'pk_test_b7N9DOwbo4B9t6EqCf9jFzfa',
                            'payment_sources_enabled': true,
                            'required': true,
                        },
                    },
                ),
            )
        ],
        discarded_orders=[
            
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
        email='testing@gmail.com',
        home_phone='0203 137 7420',
        work_phone='0203 137 7420',
        agent_reference='',
    ),
)
```

The response is identical to the `purchase` response described in [purchasing on credit](#purchasing-on-credit).



## Purchasing with redirect

This section describes how to handle generic redirects which are needed for
other supported payment providers such as Global Collect. The vast majority of
partners are either on-credit or Stripe, and so will use the payment methods
mentioned above, but this supporting this method is recommended where possible.

Redirects are required when:

- Payment is taken on a third party payment page such as PayPal (customers are redirected to that page)
- 3D Secure is used (Verified by Visa or MasterCard SecureCode)

To support generic redirects you should use the following sequence:

1. Call the `purchase` endpoint
2. The Ingresso platform will determine whether you need to redirect your customer. If so the response will include a `callout` section with the information you need to redirect your customer.
3. Redirect your customer to `callout_destination_url`, including the `callout_parameters` in the query string (they must be URL encoded). 
4. After the redirect your customer will arrive back at your `return_url`, and you may receive query string or post parameters.
5. You should then call the `callback` endpoint, passing in verbatim all query string and post parameters you have received. These are needed by Ingresso to for example finalise the payment collection.
6. If any further redirects are required, the `callback` response will include a `callout` section. You should continue to follow steps 3, 4 and 5 until the `callback` response no longer includes a `callout` section. At this point the purchase will be complete (it will have either succeeded or failed). 


### Purchase request

> **Example purchase request - generic redirects**

```shell
curl https://demo.ticketswitch.com/f13/purchase.v1 \
    -u "demo-redirect:demopass" \
    -d "transaction_uuid=a75a03c3-efc2-11e6-a96d-d0bf9c45f5c0" \
    -d "first_name=Test" \
    -d "last_name=Tester" \
    -d "address_line_one=Metro Building" \
    -d "address_line_two=1 Butterwick" \
    -d "town=London" \
    -d "county=London" \
    -d "postcode=W6 8DL" \
    -d "country_code=uk" \
    -d "phone=0203 137 7420" \
    -d "email_address=tester@gmail.com" \
    -d "return_token=FIRST_RANDOM_TOKEN" \
    -d "return_url=https://www.yourticketingsite.com/token.FIRST_RANDOM_TOKEN/return.php" \
    -d "client_http_user_agent=Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/602.4.8 (KHTML, like Gecko) Version/10.0.3 Safari/602.4.8" \
    -d "client_http_accept=text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8" \
    -d "send_confirmation_email=yes" \
    -X POST
```

```python
import uuid
from pyticketswitch import Client
from pyticketswitch.customer import Customer
from pyticketswitch.payment_methods import RedirectionDetails


client = Client('demo-redirect', 'demopass')

customer = Customer(
    first_name='Test',
    last_name='Tester',
    address_lines=['Metro Building', '1 Butterwick'],
    town='London',
    county='London',
    post_code='W6 8DL',
    country_code='uk',
    phone='0203 137 7420',
    email='testing@gmail.com',
    user_can_use_customer_data=True,
)

token = uuid.uuid4()

redirect_details = RedirectionDetails(
    token=token,
    url='https://www.fromtheboxoffice.com/callback/{}'.format(token),
    user_agent='Mozilla/5.0 (X11; Linux x86_64; rv:51.0) Gecko/20100101 Firefox/51.0',
    accept='text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
    remote_site='www.fromtheboxoffice.com',
)

status, callout, meta = client.make_purchase(
    '7150d25f-301b-11e7-bede-0025903268a0',
    customer,
    payment_method=redirect_details,
    send_confirmation_email=True,
)
```



All of the parameters mentioned above in the 
[purchasing on credit](#purchasing-on-credit) section are used. In addition the 
following parameters must be specified:

Parameter | Description
--------- | -----------
`return_token` | This token must be alphanumeric with `-`, `_` and `.` also allowed. We recommend that partners generate a UUID for this token.
`return_url` | The URL that your customer should arrive back to after being redirected. The `return_token` also has to be present in the path for the return_url and no query string parameters are allowed in the return_url.
`client_http_user_agent` | The user agent, taken from the headers of your customer's web browser. This and `client_http_accept` are required because the payment provider that generates the callout may need to know detail of the browser, for example to serve a mobile-optimised page.
`client_http_accept` | The accept content types, from the headers of your customer's web browser.


### Purchase response

> **Example purchase response - generic redirects**

``` shell
{
  "callout": {
    "callout_destination_url": "https://demo.ticketswitch.com/tickets/dummy_redirect.buy/demo-redirect",
    "callout_parameters": {
      "return_url": "https://www.yourticketingsite.com/token.FIRST_UNIQUE_TOKEN/return.php",
      "title": "Dummy external card details page for debit on system 'ext_test0'"
    },
    "callout_parameters_order": [],
    "callout_type": "get",
    "debitor_integration_data": {
      "debit_amount": 62.5,
      "debit_base_amount": 6250,
      "debit_currency": "gbp",
      "debitor_specific_data": {
        "is_dummy_3d_secure": false
      },
      "debitor_type": "dummy"
    }
  },
  "currency_details": {
    "gbp": {
      "currency_code": "gbp",
      "currency_factor": 100,
      "currency_number": 826,
      "currency_places": 2,
      "currency_post_symbol": "",
      "currency_pre_symbol": "£"
    }
  }
}
```

```python
from pyticketswitch.debitor import Debitor
from pyticketswitch.callout import Callout

# redirect purchases may return either a status object or a callout object
# when they requirement more information from the user.
Callout(
    code='ext_test0',
    description='External Test Backend 0',
    total=49.5,
    type='get',
    destination='https://www.fromtheboxoffice.com/tickets/dummy_redirect.buy/demo-redirect',
    parameters={
        'return_url': 'https://www.fromtheboxoffice.com/callback/64e8a0d8-ca3a-4a3b-8a39-597ee66acdd0',
        'title': "Dummy external card details page for debit on system 'ext_test0'"
    },
    integration_data={
        'is_dummy_3d_secure': False
    },
    debitor=Debitor(
        type='dummy',
        name='dummy',
        description='The Dummy Card Debitor',
        integration_data={},
    ),
    currency_code='gbp',
    return_token='64e8a0d8-ca3a-4a3b-8a39-597ee66acdd0',
)
```


Attribute | Description
--------- | -----------
`callout` | See below for object detail.
`currency_details` | Further details of the currencies found in the callout data.

**`callout` attributes:**

Attribute | Description
--------- | -----------
`callout_destination_url` | The URL you should redirect your customer to.
`callout_parameters` | The parameters that should be passed. The `callout_type` will specify whether to use GET or POST.
`callout_parameters_order` | In most cases this will be blank, and it can be ignored. In very rare cases, for `callout_type=get`, it will include all `callout_parameters` in a specific order - you should then specify the parameters in that order.
`callout_type` | Whether you should make a GET or POST request.
`debitor_integration_data` | This data is not needed when redirecting (so is not of use to most partners). It provides information needed to avoid redirecting.


### Callback request

> **Example callback request - generic redirects**

```shell
curl https://demo.ticketswitch.com/f13/callback.v1/this.FIRST_UNIQUE_TOKEN/next.NEXT_UNIQUE_TOKEN \
    -u "demo-redirect:demopass" \
    -d "param1=asdfasdfsdfasdff" \
    -r "https://www.thepaymentpage.com/asdfsadfsdafasdf" \
    --compressed
    -X POST
```

```python
import uuid
from pyticketswitch import Client

client = Client('demo-redirect', 'demopass')

returned_data = {'success': 'yes'}
next_token = uuid.uuid4()
status, callout, meta = client.next_callout(
    '64e8a0d8-ca3a-4a3b-8a39-597ee66acdd0',
    next_token,
    returned_data,
)
```

If the `purchase` response received includes `callout` data then you need to
redirect your customer and afterwards use the `callback` endpoint.

Note that the URL structure is 
`callback.v1/this.[previous token used]/next.[new token]`.
If `purchase` was the last endpoint hit, then the "previous token used" is
the `return_token` that was passed in to `purchase`.  If the last endpoint hit
was `callback` then "previous token used" is the "new token" you generated for
the previous `callback`.

You should pass the payment page URL you have been referred from as the
Referrer in the headers.

You should pass in verbatim all query string and post parameters you have
received. These are needed by Ingresso to for example finalise the payment
collection.

The `callback` response will either:

- include further `callout` data, as in the `purchase` response example above,
  in which case you should redirect your customer and call `callback` again;
  or 
- include similar data to the `purchase` response described in 
  [purchasing on credit](#purchasing-on-credit) - the purchase is complete and
  has either succeeded or failed. 

You should continue to call `callback` until you no longer see `callout` data.
