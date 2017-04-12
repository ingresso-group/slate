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

### On credit purchasing

You take payment using a payment provider of your own choosing. When calling
purchase you should just provide customer data, and the purchase call will
return a success or failure.

### Stripe 

Stripe is a developer-friendly payment provider that is simple to integrate
with. The Ingresso API provides integration support for Stripe - you pass us a
Stripe token and we handle the rest. We support partners collecting payment via
their own Stripe account or into Ingresso's Stripe account.

In either case you need to collect a Stripe token after your customer enters
their payment details, and pass that to Ingresso after making the purchase
call.

If you want to collect payment via your own Stripe account you will need to get
agreement to sell tickets on credit, and you will need to provide Ingresso your 
Stripe keys to allow us to process payment on your behalf. 


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



## Purchasing on credit

### Request

> **Example request - purchasing on-credit**

```shell
curl https://demo.ticketswitch.com/f13/purchase.v1 \
    -u "demo:demopass" \
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
    -d "user_can_use_customer_data=true" \
    --compressed \
    -X POST
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')

(TODO: finish)
```

This section describes how to purchase on credit. 

Parameter | Description
--------- | -----------
`address_line_one` | The first line of the customer's address - required. Either pass your customer's address or your head office address (see customer data note above). 
`address_line_two` | The second line of the customer's address. *Optional if `address_line_one` is provided.*
`agent_ref` | Partners can pass their own transaction reference to be stored in the Ingresso platform. *Optional.*
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
  "customer": {
    "addr_line_one": "Metro Building",
    "addr_line_one_latin": "Metro Building",
    "addr_line_two": "1 Butterwick",
    "addr_line_two_latin": "1 Butterwick",
    "agent_ref": "",
    "country": "United Kingdom",
    "country_code": "uk",
    "country_latin": "United Kingdom",
    "county": "London",
    "county_latin": "London",
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
  "purchase_iso8601_date_and_time": "2017-02-10T15:40:36Z",
  "reserve_iso8601_date_and_time": "2017-02-10T15:40:12Z",
  "transaction_status": "purchased",
  "trolley_contents": {
    "bundle": [
      {
        "bundle_order_count": 1,
        "bundle_source_code": "ext_test0",
        "bundle_source_desc": "Test SystemZero for on-credit backend group",
        "bundle_total_cost": 62.5,
        "bundle_total_cost_in_desired": 73.45,
        "bundle_total_seatprice": 51,
        "bundle_total_seatprice_in_desired": 59.94,
        "bundle_total_send_cost": 1.5,
        "bundle_total_send_cost_in_desired": 1.76,
        "bundle_total_surcharge": 10,
        "bundle_total_surcharge_in_desired": 11.75,
        "currency": {
          "currency_code": "gbp",
          "currency_factor": 100,
          "currency_number": 826,
          "currency_places": 2,
          "currency_post_symbol": "",
          "currency_pre_symbol": "£"
        },
        "desired_currency": {
          "currency_code": "eur",
          "currency_factor": 100,
          "currency_number": 978,
          "currency_places": 2,
          "currency_post_symbol": "",
          "currency_pre_symbol": "€"
        },
        "order": [
          {
            "backend_purchase_reference": "PURCHASE-D65E-1",
            "event": {
              "city_code": "london-uk",
              "city_desc": "London",
              "classes": {
                "theatre": "Theatre"
              },
              "country_code": "uk",
              "country_desc": "United Kingdom",
              "critic_review_percent": 100,
              "custom_filter": [],
              "event_desc": "Matthew Bourne's Nutcracker!",
              "event_id": "6IF",
              "event_status": "live",
              "event_type": "simple_ticket",
              "event_upsell_list": {
                "event_id": [
                  "6IE",
                  "6IF",
                  "6KU"
                ]
              },
              "event_uri_desc": "Matthew-Bourne%27s-Nutcracker%21",
              "geo_data": {
                "latitude": 51.5,
                "longitude": -0.15
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
              "source_desc": "Test SystemZero for on-credit backend group",
              "venue_desc": "Sadler's Wells",
              "venue_uri_desc": "Sadler%27s-Wells"
            },
            "gross_commission": {
              "amount_excluding_vat": 7.81,
              "amount_including_vat": 9.38,
              "commission_currency": {
                "currency_code": "gbp",
                "currency_factor": 100,
                "currency_number": 826,
                "currency_places": 2,
                "currency_post_symbol": "",
                "currency_pre_symbol": "£"
              }
            },
            "item_number": 1,
            "performance": {
              "date_desc": "Mon, 5th June 2017",
              "event_id": "6IF",
              "has_pool_seats": true,
              "is_ghost": false,
              "is_limited": false,
              "iso8601_date_and_time": "2017-06-05T19:30:00+01:00",
              "perf_id": "6IF-C0J",
              "running_time": 120,
              "time_desc": "7.30 PM"
            },
            "price_band_code": "C/pool",
            "seat_request_status": "not_requested",
            "send_method": {
              "send_code": "COBO",
              "send_cost": 1.5,
              "send_cost_in_desired": 1.76,
              "send_desc": "Collect from venue",
              "send_final_comment": "Instructions for collecting tickets at the venue box office:\n- Tickets must be collected by the cardholder with valid photo identification and the payment card.\n- The cardholder’s signature will be required on receipt of these tickets.\n- Tickets are only available for collection on the day of the performance.\n- Guests are advised to arrive at least 30 minutes before the performance time.\n- If the cardholder is unable to collect these tickets please contact Guest Services on 0800 640 8101.\n\nCan we help?\nIf you require further information please contact our Guest Services team:\nLive chat or call 0800 640 8101 (Monday – Sunday, 9am – 9pm GMT/BST)",
              "send_final_type": "collect",
              "send_type": "collect"
            },
            "ticket_orders": {
              "ticket_order": [
                {
                  "discount_code": "ADULT",
                  "discount_desc": "Adult standard",
                  "no_of_seats": 1,
                  "sale_seatprice": 25,
                  "sale_seatprice_in_desired": 29.38,
                  "sale_surcharge": 4,
                  "sale_surcharge_in_desired": 4.7,
                  "seats": [
                    {
                      "col_id": "472",
                      "full_id": "PM472",
                      "is_restricted_view": false,
                      "row_id": "PM"
                    }
                  ],
                  "total_sale_seatprice": 25,
                  "total_sale_seatprice_in_desired": 29.38,
                  "total_sale_surcharge": 4,
                  "total_sale_surcharge_in_desired": 4.7
                },
                {
                  "discount_code": "CHILD",
                  "discount_desc": "Child ticket",
                  "no_of_seats": 2,
                  "sale_seatprice": 13,
                  "sale_seatprice_in_desired": 15.28,
                  "sale_surcharge": 3,
                  "sale_surcharge_in_desired": 3.53,
                  "seats": [
                    {
                      "col_id": "469",
                      "full_id": "PM469",
                      "is_restricted_view": false,
                      "row_id": "PM"
                    },
                    {
                      "col_id": "466",
                      "full_id": "PM466",
                      "is_restricted_view": false,
                      "row_id": "PM"
                    }
                  ],
                  "total_sale_seatprice": 26,
                  "total_sale_seatprice_in_desired": 30.56,
                  "total_sale_surcharge": 6,
                  "total_sale_surcharge_in_desired": 7.05
                }
              ]
            },
            "ticket_type_code": "CIRCLE",
            "ticket_type_desc": "Upper circle",
            "total_no_of_seats": 3,
            "total_sale_seatprice": 51,
            "total_sale_seatprice_in_desired": 59.94,
            "total_sale_surcharge": 10,
            "total_sale_surcharge_in_desired": 11.75,
            "user_commission": {
              "amount_excluding_vat": 6.56,
              "amount_including_vat": 7.88,
              "commission_currency": {
                "currency_code": "gbp",
                "currency_factor": 100,
                "currency_number": 826,
                "currency_places": 2,
                "currency_post_symbol": "",
                "currency_pre_symbol": "£"
              }
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
    "transaction_id": "Z000-0000-37QH-Z217",
    "transaction_uuid": "363b641f-efa7-11e6-a96d-d0bf9c45f5c0",
    "trolley_bundle_count": 1,
    "trolley_order_count": 1
  }
}
```

```python
TODO
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
`gross_commission` | The total commission available to both Ingresso and the partner. This will only be visible for partners on margin share agreements or where Ingresso take a fixed fee. If you believe you should have gross_commission visible please contact us api@ingresso.co.uk.
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

In order to purchase with Stripe you need the following steps:

1. Retrieve a Stripe token (further detail below).
2. Call `purchase` passing in the Stripe token from step 1.

Note that you need to retrieve and pass in one Stripe token for each `bundle` that you
reserve. If you only support the purchase of one item at a time, or if you don't
use the Ingresso API to help manage basketing then you can ignore this.

Stripe collects payment information on your behalf, and returns a single-use 
token. The payment information can either be collected on your own checkout page
in a PCI-compliant manner ("Elements"), with a mobile-friendly form 
("Checkout"), or via their mobile SDKs. See [Stripe's Quickstart guide](https://stripe.com/docs/quickstart).

You will need to provide Stripe with the appropriate publishable key -
this is returned by Ingresso in the [reserve call](#reserve) as
bundle.debitor.debitor_integration_data.publishable_key.

Retrieving the Stripe token is the first half of the payment process - further
server-side code is required to complete the second half. You don't need to 
worry about this as Ingresso's Stripe integration takes care of it. Once you 
have provided the Stripe token we will handle the following steps:

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
    -d "ext_test0_callback/stripeToken=tok_1A5rfVHIklODsaxBzQYBklUA" \
    --compressed \
    -X POST
```


To complete a test Stripe purchase:

1. Call [reserve](#reserve)
2. Enter test card details into the [Stripe Elements example form](https://stripe.com/docs/elements) and copy the Stripe token returned.
3. Call purchase, replacing the transaction_uuid and ext_test0_callback/stripeToken with the values from the 2 steps above.

<aside class="notice">If you are using our Postman examples, the relevant calls are "reserve - best available - stripe user" and "purchase - stripe user".</aside>

All of the parameters mentioned above in the 
[purchasing on credit](#purchasing-on-credit) section are used. In addition the 
following parameter should be specified:

Parameter | Description
--------- | -----------
`X_callback/stripeToken` | The single-use token retrieved from Stripe. You should replace `X` with the `source_code` returned by `reserve`. So for `source_code=ext_test0` the parameter name used is `ext_test0_callback/stripeToken`. If you support basketing and your customer is attempting to purchase items across multiple bundles, you should provide one stripe token per bundle (each bundle has a unique `source_code`).


### Purchase response
> **Example purchase response - Stripe**

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

The response is identical to the `purchase` response described in [purchasing on credit](#purchasing-on-credit).



## Purchasing with redirect

This section describes how to handle generic redirects which are needed for
other supported payment providers such as Global Collect. The vast majority of
partners are either on-credit or Stripe, and so will not  need to read this
section.

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
    -X POST
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
      "return_url": "https://www.yourticketingsite.com/token.FIRST_RANDOM_TOKEN/return.php",
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
curl https://demo.ticketswitch.com/f13/callback.v1/this.FIRST_RANDOM_TOKEN/next.NEXT_RANDOM_TOKEN \
    -u "demo-redirect:demopass" \
    -d "param1=asdfasdfsdfasdff" \
    -r "https://www.thepaymentpage.com/asdfsadfsdafasdf" \
    --compressed
    -X POST
```

If the `purchase` response received includes `callout` data then you need to redirect your customer and afterwards use the `callback` endpoint.

Note that the URL structure is `callback.v1/this.[previous token used]/next.[new token]`. If `purchase` was the last endpoint hit, then the "previous token used" is the `return_token` that was passed in to `purchase`. If the last endpoint hit was `callback` then "previous token used" is the "new token" you generated for the previous `callback`.

You should pass the payment page URL you have been referred from as the Referrer in the headers.

You should pass in verbatim all query string and post parameters you have received. These are needed by Ingresso to for example finalise the payment collection.

The `callback` response will either:

- include further `callout` data, as in the `purchase` response example above, in which case you should redirect your customer and call `callback` again; or 
- include similar data to the `purchase` response described in [purchasing on credit](#purchasing-on-credit) - the purchase is complete and has either succeeded or failed. 

You should continue to call `callback` until you no longer see `callout` data.
