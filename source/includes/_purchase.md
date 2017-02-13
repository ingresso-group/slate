# Purchase
> **Definition**

```
POST https://demo.ticketswitch.com/f13/purchase.v1
```

To purchase tickets you must first [reserve](#reserve) them. You can then
attempt to purchase the reserved tickets at any time within the reserve window.
A successful result means that the order has been confirmed in the supplier
ticketing system. If you have attempted to purchase multiple events from a
single trolley it is possible for a purchase to partially succeed, with some
events unable to be purchased from the corresponding supplier ticketing system.


The [payment option](#payment-options) you are using will determine how you use
purchase. 

1. If you purchase on credit then you just need to provide customer data.

2. If payment is taken using Stripe then you need to provide customer data and a
redirect URL. 

3. If the venue takes payment or you use other payment providers then you need 
to support both passing card details directly and redirecting the customer to
third-party payment pages. We do not typically offer this option to partners so
this has not been documented below. If you wish to use this option then contact
use at api@ingresso.co.uk.

The first two options are outlined below.


*Notes on sending customer data*

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
it turns out that tickets need to be unexpectedly posted).


If a purchase fails due to incomplete or incorrect customer data then you can
reattempt the purchase call with corrected data. If a purchase fails due to the
reservation having timed out, then it is necessary to [reserve](#reserve)
tickets again. The output data is structured such that permanent failures are
not reported as errors, but are reported as an actual result from a successful
operation.

For purchases where a despatch method with a type of `selfprint` was selected,
the URL returned should be presented to the customer.


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
    -X POST
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')

(TODO: finish)
```

Most partners will manage payment themselves - this is how to purchase if you 
use this option.

Parameter | Description
--------- | -----------
`address_line_one` | The first line of the customer's address - required. Either pass your customer's address or your head office address (see customer data note above). 
`address_line_two` | The second line of the customer's address. **Optional if `address_line_one` is provided.**
`agent_ref` | Partners can pass their own transaction reference to be stored in the Ingresso platform. **Optional.**
`country_code` | The [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) country code of the customer's address - required. The country code must have been present in the `allowed_countries` list from [reserve](#reserve).
`county` | The county of region of the customer's address. **Optional.**
`dp_supplier` | (TODO: incorrect param name) Data protection question - set to `true` if the customer has opted in to receiving marketing emails from the ticket supplier. **Optional - it will default to false.**
`dp_user` | (TODO: incorrect param name) Data protection question - set to `true` if the customer has opted in to receiving marketing emails from you. Most partners manage marketing opt-ins themselves so do not provide this. **Optional - it will default to false.**
`dp_world` | (TODO: incorrect param name) Data protection question - set to `true` if the customer has opted in to receiving marketing emails communication from you and third-parties. **Optional - it will default to false.**
`email_address` | The customer's email address. Required when `needs_email_address` was `true` from the [reserve](#reserve) response. We recommend this is provided - see customer data note above.
`first_name` | The customer's first name - required. Used by venues as a security measure when admitting customers.
`home_phone` | The customer's home phone number. **Optional if `phone` is provided.**
`initials` | The customer's initials for their name. **Optional.**
`last_name` | The customer's last name - required. Used by venues as a security measure when admitting customers.
`phone` | The customer's phone number - required. Used by venues and Ingresso as a means to contact customers (see customer data note above). It is optional if you specify the `work_phone` and `home_phone` separately instead.
`postcode` | The ZIP or postal code of the customer's address. **Optional.**
`suffix` | The suffix of the customer's name, for example "Jr." or "CPA" **Optional.**
`title` | The title of the customer's name, for example "Mr." or "Dr." **Optional.**
`town` | The city or town of the customer's address. **Optional.**
`transaction_uuid` | The unique reference for the reserved tickets, taken from the [reserve](#reserve) response.
`work_phone` | The customer's work phone number. **Optional if `phone` is provided.**

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
`language_list` | (TODO)
`purchase_iso8601_date_and_time` | The purchase time in ISO 8601 format.
`reserve_iso8601_date_and_time` | The reserve time in ISO 8601 format.
`transaction_status` | `purchased`, `failed` or `attempting` (will only be seen if the customer is being redirected to a payment page - most partners will not see this).
`trolley_contents` | See below for object detail.


**`customer` attributes:**

Attribute | Description
--------- | -----------
`addr_line_one` | The first line of the customer's address.
`addr_line_one_latin` | The first line of the customer's address, (TODO finish).
`addr_line_two` | The second line of the customer's address, if provided.
`addr_line_two_latin` | 
`agent_ref` | A partner transaction reference, if this was passed in as a parameter.
`country` | The country from the customer's address.
`country_code` | The [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) country code of the customer's address.
`country_latin` | 
`county` | The county from the customer's address, if provided.
`county_latin` | 
`dp_supplier` | Has the customer opted in to receiving marketing emails from the ticket supplier.
`dp_user` | Has the customer opted in to receiving marketing emails from you.
`dp_world` | Has the customer opted in to receiving marketing emails communication from you and third-parties.
`email_addr` | The customer's email address, if provided.
`first_name` | The customer's first name.
`first_name_latin` | 
`home_phone` | The customer's home phone number.
`initials` | The initials of the customer's name, if provided.
`initials_latin` | 
`last_name` | The customer's surname.
`last_name_latin` | 
`postcode` | The customer's postcode, if provided.
`postcode_latin` | 
`suffix` | The suffix of the customer's name, if provided.
`suffix_latin` | 
`title` | The title of the customer's name, if provided.
`title_latin` | 
`town` | The customer's town or city, if provided.
`town_latin` | 
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
`purchase_result.is_semi_credit` | **Legacy - can be ignored.** (TODO: Pete to remove)
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
`send_method` | 
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

`col_id` | The column identifier of the seat.
`full_id` | The ID of the seat - comprised of the `col_id` and the `row_id`, sometimes with a separator between them.
`is_restricted_view` | `true` if the seat is classified as having a restricted view.
`row_id` | The row identifier of the seat.
`seat_text` | A message about the seat that must be displayed to customers.
`seat_text_code` | An identifier for the seat text (only unique within the current `bundle_source_code`). Not useful for most partners.


## Purchasing with redirect

### Request

> **Example request - purchasing with a redirecting debitor such as Stripe**

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
    -d "return_token=ffa02aad-e936-11e6-8ea7-001d9263ab24" \
    -d "return_url=https://www.yourticketingsite.com/token.ffa02aad-e936-11e6-8ea7-001d9263ab24/return.php" \
    -d "client_http_user_agent=Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/602.4.8 (KHTML, like Gecko) Version/10.0.3 Safari/602.4.8" \
    -d "client_http_accept=text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8" \
    -X POST
```

If you do not purchase on credit and want Ingresso's help to manage payment, then it is necessary to support redirects. 

Redirects are necessary when:

- Payment is taken with Stripe (we have internally set Stripe up as a redirecting payment provider, even though it doesn't necessarily need to redirect)
- Payment is taken on a third party payment page such as PayPal (customers are redirected to that page)
- 3D Secure is used (Verified by Visa or MasterCard SecureCode)

To support redirects you should use the following sequence:

1. You call purchase, passing in the parameters below
2. The Ingresso platform will determine whether you need to redirect your customer. If so the response will assess 

All of the parameters mentioned above in the [purchasing on credit](#purchasing-on-credit) section are used.

Parameter | Description
--------- | -----------
`return_token` | This token must be alphanumeric with '-', '_' and '.' also allowed. We recommend that partners generate a UUID for this token.
`return_url` | The `return_token` also has to be present in the path for the return_url and no query string parameters are allowed in the return_url.
`client_http_user_agent` | The user agent, taken from the headers of your customer's web browser. This and `client_http_accept` are required because the payment provider that generates the callout may need to know detail of the browser, for example to serve a mobile-optimised page.
`client_http_accept` | The accept content types, from the headers of your customer's web browser.


### Response
> **Example response - purchasing on-credit**

``` shell
{
  "callout": {
    "debitor_integration_data": {
      "debit_amount": 76.5,
      "debit_base_amount": 7650,
      "debit_currency": "gbp",
      "debitor_specific_data": {
        "is_dummy_3d_secure": false
      },
      "debitor_type": "dummy"
    },
    "redirect_html_page_data": "<html><head>\n<script type=\"text/javascript\">\nwindow.location=\"https:\\/\\/api.ticketswitch.com\\/cgi-bin\\/dummy_redirect.buy\\/tswitch?return_url=https%3A%2F%2Fdemo.ticketswitch.com%2Ftoken.ffa02aad-e936-11e6-8ea7-001d9263ab24%2Freturn.php\\u0026title=Dummy%20external%20card%20details%20page%20for%20debit%20on%20system%20%27ext_test0%27\";\n</script></head>\n<body></body></html>"
  }
}
```

Attribute | Description
--------- | -----------
`customer` | See below for object detail.
`language_list` | (TODO)
`purchase_iso8601_date_and_time` | The purchase time in ISO 8601 format.
`reserve_iso8601_date_and_time` | The reserve time in ISO 8601 format.
`transaction_status` | `purchased`, `failed` or `attempting` (will only be seen if the customer is being redirected to a payment page - most partners will not see this).
`trolley_contents` | See below for object detail.


**`customer` attributes:**

Attribute | Description
--------- | -----------
`addr_line_one` | The first line of the customer's address.
`addr_line_one_latin` | The first line of the customer's address, (TODO finish).