# Introduction

The Ingresso API is designed to allow you to sell from Ingresso's inventory of
event tickets.  Ingresso has connected to a large number of ticketing system
APIs, allowing us to transact directly on the venue's system.  By integrating
with the Ingresso API you are able to view and purchase live ticketing inventory 
from a number of different venues. 

Example partners that have integrated with the Ingresso API are 
[Amazon](https://tickets.amazon.co.uk/) and 
[lastminute.com](http://www.lastminute.com/theatre/). We also build our own 
ticketing websites entirely on top of the API (for example 
[Disney Tickets](https://www.disneytickets.co.uk) and
[From The Box Office](https://www.fromtheboxoffice.com)). 

The Ingresso API is a fully transactional API, loosely based on REST. You format
GET or POST requests in [JSON](http://www.json.org/) and you will receive either
a JSON-formatted response or an HTTP error. The API is internally known as F13, 
which is why you see /f13 in the endpoints.

We describe objects in a consistent way across different resources (for example
the output of [events](#events) and [performances](#performances) includes a
cost_range object and this is described in the same format). The idea behind
this is that standard code can be used on the client side to read all parts of
the output.

This API replaces the [Ingresso XML API](http://legacy-docs.ingresso.co.uk/). 
The XML API relies on temporary tokens from each call being passed in
to the next call; these has been removed in favour of permanent IDs. One
benefit of this change is that a call near the end of the booking process, such
as [reserve](#reserve), can be called without needing to remake the preceding
calls (such as [requesting availability](#availability)), opening up additional
potential solutions. The output is also more compact leading to faster response
times in most cases.


## Postman examples

<img src="https://d1wx4w35ubmdix.cloudfront.net/wl-media/images/postman.png" alt="Postman screenshot">

We have a public library available for [Postman](https://www.getpostman.com/) 
that closely matches the examples included in this documentation.

Click below to import our collection of API examples.

[![Run in Postman](https://run.pstmn.io/button.svg)](https://www.getpostman.com/collections/7c834c45808672b158a7)


## F13 Browser

<img src="https://d1wx4w35ubmdix.cloudfront.net/wl-media/images/f13-browser.png" alt="F13 Browser">

We have also developed the [F13 Browser](https://f13-browser.ticketswitch.io/).

- Move through the Ingresso booking flow using your own API account
- Easy to use - it can be given to non-technical people to check that the API is returning expected data
- For each step the request and response data is output in a friendly format and as JSON


## Basic booking flow

> **List events including from prices and a single image**

```shell
curl https://demo.ticketswitch.com/f13/events.v1 \
    -u "demo:demopass" \
    -d "req_cost_range" \
    -d "req_media_triplet_one" \
    --compressed \
    -G
```

<blockquote class="lang-specific shell">
gzip must be used for all requests (with curl you can use the --compressed flag). Most http libraries support compression (though you may need to enable this somehow), but if not and you are receiving gzip errors you can pass "Accept-Encoding: gzip" in the headers then unzip the response.

curl uses the -u flag to pass basic auth credentials in the format user:password.
</blockquote>

> **List performances for event ID 6IF**

```shell
curl https://demo.ticketswitch.com/f13/performances.v1 \
    -u "demo:demopass" \
    -d "event_id=6IF" \
    -d "page_len=200" \
    --compressed \
    -G
```

> **Display availability for the 9th May performance**

```shell
curl https://demo.ticketswitch.com/f13/availability.v1 \
    -u "demo:demopass" \
    -d "perf_id=6IF-B0T" \
    --compressed \
    -G
```

> **Reserve 2 tickets in price band A in the Circle**

```shell
curl https://demo.ticketswitch.com/f13/reserve.v1 \
    -u "demo:demopass" \
    -d "perf_id=6IF-B0T" \
    -d "ticket_type_code=CIRCLE" \
    -d "price_band_code=A/pool" \
    -d "no_of_seats=2" \
    --compressed \
    -G
```

> **Purchase the tickets**

```shell
curl https://demo.ticketswitch.com/f13/purchase.v1 \
    -u "demo:demopass" \
    -d "transaction_uuid=XXXXXXXX" \
    --compressed \
    -G
```

The Ingresso API supports multiple use cases and variations, but below is a 
typical workflow:

* Retrieve a list of [events](#events).

* Retrieve a list of [performances](#performances) for an event.

* Retrieve [availability](#availability) for a performance, including seat
numbers if the event is seated.

* *Optionally you can request [discounts](#discounts) for the selected tickets,
and purchase multiple items at once by adding them to a [trolley](#trolley).*

* [reserve](#reserve) tickets for a set period of time.

* [purchase](#purchase) the reserved tickets.


## Philosophy

* Returned data is as simple as possible for the task in-hand
* Transaction flow is as simple as possible for the task in hand
* Default missing parameters sensibly instead of returning an error
* Always try and complete a call in some way instead of erroring


## Types of API integration

The Ingresso API supports a variety of different integration types. The most
common is a **full integration** - this is where our partner uses all
functionality of the Ingresso API to retrieve listings of events and
performances (often in advance to populate their own database) then to request
availability and reserve and purchase tickets in real-time.

Ingresso also offer partners an individually branded "white label" ticketing
website - for example [Disney Tickets](https://www.disneytickets.co.uk) and
[From The Box Office](https://www.fromtheboxoffice.com) are fully developed by
Ingresso. It is possible to use the API in conjunction with a white label
website as a **partial integration**. Some examples of partial integrations:

* Maintain a listing of events on your own site, and use Ingresso's white label
website for booking the tickets. In this case the Ingresso API can be used to 
[list events](#events-list).

* Ingresso have invested in a high-conversion booking app (where the customer
selects their performance and seats). This page is often where most development
time is spent. It is possible to link to our booking page and we will
automatically redirect back to your checkout page once the customer has
successfully reserved their seats. You can link to our full booking page or just
embed our seat selection widget within your site.

* Use the Ingresso API to display events, performances and availability, then
send your customer to our white label website for checkout. It is possible for
Ingresso to then redirect the customer back to your confirmation page if the
purchase is successful.


## Payment Options

If you use the checkout page of Ingresso's white label website, then Ingresso
will take care of collecting payment from the customer. However if you wish to
develop your own checkout page these are the options for taking payment:

1. **You take payment**: you purchase tickets from Ingresso on credit / on
account and take payment from the customer yourself. You will be invoiced
regularly by Ingresso. This option is typically only offered to partners with 
the capability to sell a high volume of tickets.
There are two ways for you to take payment:

    a) Using a payment provider of your choosing.

    b) Using your own Stripe account. Stripe is a developer-friendly payment 
provider that is simple to integrate with. Our API provides integration support 
for Stripe. You just need to collect a Stripe token after your customer enters 
their payment details; Ingresso's Stripe integration will then ensure the 
payment is captured and all edge cases are handled.

2. **Ingresso takes payment**: for the majority of partners Ingresso will take 
payment using Stripe, so you need to collect a Stripe token and pass that in 
when purchasing tickets.

These options are discussed in more detail in [purchase](#purchase).


## How to get access to the API

All examples shown in this documentation are for the `demo` user. This user has
access to test product only - you can make purchases with this user without any
impact on live tickets, or any need to contact us first.

The process for going on sale is:

1. Start your integration using the `demo` user. This is the user that all partners use during testing

2. Once you have made good progress contact us to agree a commission deal: commercial@ingresso.co.uk

3. When you have finished the integration we will check to ensure you support the [required functionality](#required-minimum-functionality)

4. We will then give you a live account and you can go live


## Authentication

> Example request with authentication

```shell
curl https://demo.ticketswitch.com/f13/events.v1 \
    -u "demo:demopass" \
    -d "s_keys=matthew" \
    -d "s_coco=uk" \
    --compressed \
    -G
```

```python
from pyticketswitch import Client

client = Client(user='demo', password='demopass')
```

<blockquote class="lang-specific shell">
curl uses the -u flag to pass basic auth credentials in the format user:password.
</blockquote>


> Connect as the `demosub` subuser with Dutch as the preferred language

```shell
curl https://demo.ticketswitch.com/f13/events.v1/demo/demosub/nl \
    -u "demo:demopass" \
    -d "s_keys=matthew" \
    -d "req_extra_info" \
    --compressed \
    -G
```


Authenticate when using the API by including your username and password in the 
request. Authentication to the API is performed via 
[HTTP Basic Auth](https://en.wikipedia.org/wiki/Basic_access_authentication). 

<aside class="notice">Please ensure you keep your username and password secret - do not share 
these in publicly accessible areas such GitHub, client-side code, etc.</aside>



This is all you need, but there are two additional optional parameters that can 
be included:

1. Some of our partners require sub users for particular use cases such as running 
their own affiliate programme.

2. It is possible to pass in a preferred language code, overriding the language
code/s in the HTTP header. 

These parameters are included in the URL after the version number:

`/f13/events.v1/USER/SUBUSER/LANG_CODE`

Using a '-' for the subuser or language code will cause it to be ignored, so to
ignore the subuser:

`/f13/events.v1/USER/-/LANG_CODE`


## Errors

> **Example**

```shell
HTTP/1.1 400 Bad Request

{
  "error_code": 8,
  "error_desc": "Bad data supplied"
}
```

```python
APIError(
    msg='Bad data supplied',
    code=8,
    response=<Response [400]>
)

```

When an error happens the API will return a non `2XX` HTTP status code and a
message about the error in the response body. The message will include a
human readable description of the error and a code that can be used by
application to recognise the issue and handle it appropriately.

Some method calls can have partial failures. For example a reservation call
might be successful for one order in the trolley but fail to reserve a second
order, or a purchase call might be successful in that we successfully
attempted a payment but the users card was declined, or the backend system
fell over mid transaction. In these situations the API will return a 200 HTTP
status code with details of the issues in the response body.  You should
consult the documentation for the specific end point for details on
how to handle these situations.


### Status codes

Details of the general HTTP status codes can be found below:

Status Code | Description
------------|------------
`200 - OK` | The response can be interpreted as explained in the documentation.
`400 - Bad Request` | Your call was missing data required to satisfy the request. This response generally occurs due to missing parameters.
`401 - Unauthorised` | No authentication parameters were provided or the given parameters were invalid.
`403 - Forbidden` | You are trying to access something your account doesn't have access to.
`404 - Not Found` | Then the endpoint you were trying to reach does not exist.
`405 - Method Not Allowed` | You have tried to use a POST request when the endpoint was expecting a GET request (or vice versa).
`410 - Gone` | The resource you were trying to access no longer exists. This is primarily used around transactions when temporary resources (such as callouts) have expired.
`460 - Invalid Parameters` |  The parameters passed to the API were technically valid, but were rejected due to their content. Refer to the error description and error code for further details.
`5XX - Server Errors` | Something unexpectedly went wrong and the API was unable to do what you asked it to do. Refer to the error description and error code for further details.

One specific case that is worth mentioning in detail is when the API returns a
502 HTTP status code. This indicates that your request required a connection to an
upstream server (for example the supplier ticketing system, or a payment
provider), but we were unable to establish the connection. A lot of the
systems that our API connects to are very fragile; we do what we can to
mitigate this, but sometimes those upstream servers simply won't be available
to service your request. In these situations you could attempt to retry the
request in case it was just momentary issue, failing that waiting a short time
may help. Both scheduled and unscheduled major outages will be recorded on our
[status page](https://ingresso.statuspage.io/).

### Error codes

Error codes provide a computer readable classifier of an error. They will be
useful in identifying errors and handling them appropriately.

Error Code | Description
-----------|-------------
`2` | Bad channel.
`3` | User authentication failure.
`4` | Failed to create connection to the backend system.
`5` | Host is on a forbidden network.
`6` | Failed to connect to database.
`7` | Membership authentication failed.
`8` | Bad data supplied.
`2000` | Email address is invalid. This error will also provide a secondary `error_key` field that will provide a code for working out exactly what's wrong with the email address (see below).
`3000` | Unrecognised card type from number.
`3001` | Card type not accepted.
`3002` | Not a valid card number.
`3003` | Invalid expiry date.
`3004` | Invalid CV2.
`3005` | Missing issue number.
`3006` | Invalid issue number.
`3007` | Missing start date.
`3008` | Invalid start date.


### Email error keys

Email error keys break down the `2000` error code into more explicit issues.

Key | Description
----|------------
`addr_dot_before_at_must_be_quoted` | A '.' before the '@' sign should be quoted
`addr_cannot_end_in_dot` | Address cannot end in '.'
`addr_cannot_have_dot_immediately_after_at` | Cannot have '.' immediately after '@' sign
`addr_cannot_have_dot_dot_after_at` | Cannot have '..' after '@' sign
`addr_cr_within_quotes` | Carriage return within quotes
`addr_cr_not_allowed_after_at` | Carriage returns not allowed after '@' sign
`addr_may_not_be_blank` | Email addresses may not be blank
`addr_may_not_end_with_at` | Email addresses may not end with '@' sign
`addr_may_not_start_with_at` | Email addresses may not start with '@' sign
`addr_missing_at` | Missing '@' sign
`addr_needs_at_least_one_dot` | Must have at least one '.' after '@' sign
`addr_spaces_before_at_must_be_quoted` | Spaces before '@' sign must be quoted
`addr_spaces_not_allowed_after_at` | Spaces not allowed after '@' sign
`addr_tabs_not_allowed_after_at` | Tabs not allowed after '@' sign
`addr_unterminated_quote_before_at` | Unterminated quote before '@'
`addr_non_ascii_in_quotes` | Non-ASCII character in quotes
`addr_dot_expected_after_quotes` | A '.' is expected after quotes
`addr_non_ascii_before_at` | Non-ASCII character before '@' sign
`addr_control_character_before_at` | Control character before '@' sign
`addr_specials_before_at_must_be_quoted` | Special characters before '@' sign must be quoted
`addr_non_ascii_after_at` | Non-ASCII character after '@' sign
`addr_control_character_after_at` | Control character after '@' sign
`addr_specials_after_at` | Special characters not allowed after '@' sign
`addr_bad_email_domain` | This domain is not e-mailable


### Backend system errors

It's important to note that the majority of errors that you are likely to see
are caused by failures in the backend systems that we are attempting to
connect to your behalf. Generally these issues will present themselves in the
availability, reservation, and purchase calls. If you experience unexpected
results from these calls but there is no indication of an explicit error
having happened it's worth checking our status page to see if there are any
known issues ongoing with any of the backend systems: 

[https://status.ingresso.co.uk/](https://status.ingresso.co.uk/)

## Variables controlling output

The output from a given resource may be augmented beyond the
basic result in one of two ways. Firstly there are extra functions
which may be available for a given resource, which are specific to
that resource. These are triggered using variables with the prefix `add_` 
such as `add_discount_codes` on the availability resource which
is used to control whether or not discount codes are also retrieved and
returned.

The second type of augmentation is specific to the returned objects themselves
and is thus applicable to the output of any resource which returns one of these
objects. The most obvious example is an event object, which can be described
with many extra pieces of information about it. These variables are all prefixed
with `req_` as they are requests to the objects to output something extra or
different about themselves, for example `req_cost_range`.

Note that the `req_` and `add_` variables all result in extra
processing to retrieve the requested data, which will slow down the resource,
sometimes substantially. They should therefore only be used where necessary.


## API libraries / SDKs

Our API libraries implement a thin client over the API. Where the API 
will return JSON, the API wrappers return language-specific objects (at the
moment we have only implemented a single language):

* python: [Pyticketswitch](https://github.com/ingresso-group/pyticketswitch)

Examples will be included in this documentation, but if you require further
detail you can refer to the appropriate documentation for the language wrapper.

## Required Minimum Functionality

Before your application can go live, there are a number of minimum
implementation requirements it must meet. The test data available can help make
sure your implementation is working as expected and can handle various types of
errors and edge cases. To validate the functionality of your application,
Ingresso will use the following representative tests.


### List performance dates or times
A given event may have multiple performances on different days and times, and
the customer must be able to choose the one they want to attend. This can be
done by listing them, or in a calendar interface, or in any other way that is
appropriate for your application.

**Representative Test:** User selects *The Unremarkable Incident of the Cat at Lunchtime*
(event ID 7AB)<br/>
When the event is selected in the interface,<br/>
Then it should display the two available performances, 1 January {this year + 1}
and 1 January {this year + 2}, at 15:30:00 UTC.


### Support best available booking flow
At a minimum, the customer must be able to specify the number of seats they
want and for which ticket type and price band. The Ingresso backend will then
reserve the best available seats that meet the requirements.

**Representative Test:** User selects 3 tickets for event 6IF in the stalls band B<br/>
When the reservation is made<br/>
Then the application shows a reservation confirmation screen with 3 tickets
from that price band, and allows the user to proceed with the booking and
payment process


### Display the full ticket price to the customer before purchase
The customer must be shown the full price they will pay before the purchase is
confirmed and their payment taken.
<aside class="notice">If selling in the UK market, the ticket seat price and
the surcharge should be itemised separately. Make sure to follow all market
regulations for the countries and regions you are selling in.</aside>

**Representative Test:** User confirms reservation of 1 seat for event 7AB in the stalls
band A (e.g. seat B4)<br/>
When the confirmation and payment screen is displayed<br/>
Then the full price should be displayed as £55.00, showing a £50.00 seat price
and a £5.00 surcharge. Any dispatch methods that incur an additional cost
should also be listed separately. A total price should be displayed in a
highlighted font showing the price the customer will pay (by adding the prices
together).


### Display special seat conditions to customer before purchase
Some seats in venues may have restricted views of the stage, or other
information associated with them. It's important that the customers are made
aware of any special seat conditions that will apply before they purchase.
Messages can be classified as restricted view or not, but it is necessary 
in all cases to display these messages (sometimes we aren't able to 
accurately classify a restricted view message). These messages must be 
displayed to the customer before they purchase, and also in the purchase
confirmation that is sent to the customer.

**Representative Test:** User reserves any seats for a Thursday performance of
*Swan Lake* (event 6IE)<br/>
When the reservation is made<br/>
Then the application should show that these seats have restricted views before
taking payment.

**For customers implementing seat selection functionality:**

**Representative Test:** User selects seats C5 and C6 for event 7AB<br/>
When the seats are selected <br/>
Then the application should show that these seats have restricted views, and
show the seat text for C6 ("Haunted seat").

### Release seats if customer does not proceed with the booking flow
A customer may change their mind about the number or type of seats to be
reserved for a performance. If your customer selects and reserves seats, then
goes back to select and reserve additional seats or removes the reserved seats
from their basket, the initial seats should be [released](#release).

<aside class="warning">Failure to release the initial reservation will mean the
customer is unable to reserve seats (even though they are actually available),
and needlessly reduces inventory for everyone including your other customers.
Ingresso will automatically release any reserve after 15 minutes of inactivity,
but you must release seats as soon as possible. Ingresso reserves the right to
disable the API account of any partner that does not release tickets when they
should.</aside>

**Representative Test:** User makes a reservation for any ticket then clicks
"back" and makes another selection, or removes items from their basket<br/>
When the customer selects a second reservation or empties their basket<br/>
Then your application should call the `release` resource of the
Ingresso API. This must be done before any further call to `reserve`.<br/>
Optionally it should also display a message to the customer indicating the
seat reservation has been released.

### Display different send methods including eTickets
Various venues support different methods of sending tickets (including post,
collect from box office, or printable eTickets). Some of these may carry an
additional fee. Where available, different send methods should be offered to
the customer, and your application should support eTickets (either provided by
Ingresso or your own format with a barcode retrieved from the Ingresso API).

**Representative Test:** User confirms reservation of tickets for Toy Story --
The Opera (7AA)<br/>
When the customer selects the Toy Story(7AA) event<br/>
Then the available send methods of *Printable eTicket* and *Post worldwide* are
shown.

**Representative Test:** User selects *Printable eTicket* and checks out<br/>
When the customer has purchased the tickets<br/>
Then the ticket with the appropriate barcode will be sent to their email or
otherwise permanently saved within your application after purchase has been
confirmed

### Gracefully handle reservation failures and seat changes
Especially for popular shows, it is quite common that seats that are requested
are purchased by other customers before they can be purchased by the user of
your application. In this case, the Ingresso system will return a failure or,
in some cases, continue the reservation but with different seats than you may
expect.

**Representative Test:** User makes a reservation including seat H10 for event
7AA or 7AB<br/>
When the customer reserves seat H10 (by itself or with other seats),<br/>
Then the reservation will fail and your application should indicate that the
reservation for that ticket type and price band could not be completed, and
that their card has not been charged.

**Representative Test:** User reserves a number of seats including seat D7 for
event 7AA or 7AB<br/>
When the customer reserves a number of seats including D7 on 7AB,<br/>
Then the reservation process will proceed but the seat numbers will have been
changed and your application should notify the customer that the seats have
changed.

### Gracefully handle purchase failures
The purchase process can fail for various reasons. The purchase call will
usually return a reason for failure, if known, after which the reserve status
will be set to "failed" and the seats automatically released by the backend.
The reserve must be made again and the seat numbers can change, so your
application must notify the customer and show the confirmation again before
attempting to repurchase.

**Representative Test:** User attempts to purchase any number of seats for
event 6IE and enters `fail part one` in address line two<br/>
When the customer enters `fail part one` in the address line two and clicks to
purchase<br/>
Then the application should attempt the purchase through the Ingresso API. When
it returns the failure, a notification should be displayed to the customer and
a new reservation should be made, indicating the seats may have changed.

## Recommended Functionality
In addition to basic best-available booking flow and error handling outlined
above, Ingresso recommends supporting additional functionality that will
benefit your customers and help improve your user experience. The following
functionality can be validated with the representative tests:

### Support seat selection booking flow
For many events the backend system supports seat selection. When making a
reserve for a performance, requested seats can also be passed to the API and
where possible the request will be accommodated. Your application should supply
an interface to allow customers to request specific seats.

<aside class="notice">Seating chart data will be available soon through the
Ingresso API, but for now you must supply your own seating chart.</aside>

**Representative Test:** User selects any performance for event 7AB<br/>
When the customer clicks on a performance for event 7AB<br/>
Then the application shows which seats are available (either just listing them,
or using an interactive seating chart).

### (Required) Seat selection respects leaving singles policy
If seat selection is supported, your interface must respect the
`allow_leaving_single_seats` attribute set for each price band by a performance
availability call. If the seat selection chosen by your customer would leave a
single seat by itself in a block, and this is not allowed by the venue for the
price band the seats are in, your application must indicate to the customer that
this reservation will fail.

**Representative Test:** User opens event 7AA and selects seats B3 and B4<br/>
When the customer selects seats B3 and B4 (leaving B2 unselected)<br/>
Then the application should display a warning to the customer (preferably on
the seat selection screen, but definitely prior to making a reserve call to the
API).

### (Required) Seat selection respects contiguous seat selection policy
Some events allow selection of discontiguous seats, and others don't. If the
event allows contiguous seat selection only, you should display a warning if
the customer selects seats that are not next to each other. 

**Representative Test:** User opens event 7AA and selects seats B3 and B5<br/>
When the customer clicks B5<br/>
Then the application should display a notification that the seat selection is
invalid.

**Representative Test:** User opens event 7AB and selects seats B3 and B5<br/>
When the customer clicks B5<br/>
Then the booking flow should proceed as usual, because this event allows
discontiguous seat selection.

### Display special offers to customers
If a special offer is available for an event, it may help attract your
customers. Where available, Ingresso recommends that special offers be
highlighted and displayed.

**Representative Test:** User looks for special offers<br/>
When the customer interacts with your application (either in the main view or
for an event with a special offer, such as 7AB)<br/>
Then a prominent notice is seen displaying details of the available special
offers and the associated event (e.g. 7AB has 8% off CIRCLE-A seats, and no
fees for STALLS-B seats).

### Allow customers to select discount codes
Some events have discounted ticket prices for children, students, etc.
Your customers will appreciate being able to select these discount tickets
where available.

**Representative Test:** User requests a ticket for The Nutcracker (6IF)<br/>
When the customer requests tickets for event 6IF in Stalls-A<br/>
Then they should be able to choose between a normal ADULT ticket, and
discounted CHILD, STUDENT and OAP tickets.

### Display all event information to customers
Most events include additional media information. Displaying this information
to your customers will help build interest and credibility.

**Representative Test:** User clicks on The Nutcracker (6IF)<br/>
When the customer selects the main event page or information page<br/>
Then the associated media (video, images, copy text, seating plan, etc.) will
be displayed or linked.


## Testing

To help with implementation and to meet the minimum and recommended
functionality requirements above, we have a number of test events that you can
use to validate your application and make test purchases. Many of these events
also have specific conditions that can be used to test edge cases in the
reservation process - please see below for details.

For testing best available events, use "Matthew Bourne's Nutcracker TEST" (event
ID = 6IF) and "Matthew Bourne's Swan Lake test" (6IE).

- Swan Lake (6IE) has default discounts only
- Swan Lake (6IE) reserves will fail for Tuesday performances
- Swan Lake (6IE) has restricted view seats on ticket 2 and 3 on Thursdays
- The Nutcracker (6IF) has a maximum of 3 mixed discounts
- The Nutcracker (6IF) has no availability on Saturday nights
- The Nutcracker (6IF) has no discounts in the stalls on the 1st of the month
- The Nutcracker (6IF) has no OPA or STUDENT discounts in price band B
- The Nutcracker (6IF) has "collect from the venue" and "post" dispatch methods
  available

For testing events that allow selection of specific seats, use either "The
Unremarkable Incident of the Cat at Lunchtime" (event ID = 7AB) or "Toy Story
- The Opera" (7AA).

- Unremarkable Incident (7AB) allows discontiguous seat selection and leaving
  singles
- Unremarkable Incident (7AB) also has some discounts and special offers for
  some seat bands
- Toy Story (7AA) allows contiguous seat selection only and does not permit
  leaving singles

Additionally, both events have the following conditions in common:

- A reservation including seat H10 will always fail
- A reservation including seat D7 will return a different seat selection from
  the same block of seats (as if that seat had become unavailable)
- Seats A1, A2, A10 and C5 have restricted views
- Seat A6 has seat text attached to the seat
- Seat C6 has both seat text and a restricted view


There is a seating plan available for 7AB and 7AA, which will be helpful if
you decide to implement seat selection on your front end. Please email
[api@ingresso.co.uk](mailto:api@ingresso.co.uk) and we can talk you through how
to do this.

It may be useful to use our [B2B website](https://b2b.ingresso.co.uk/b2b/) as a
working example during your development.
You can sign in using your own credentials or with affiliate ID: `demo` and
password: `demopass`

We have a number of other events for different types of events such as
attractions and hotels - you will see these if you [list all events](#events).

Some of these other test events have specific conditions that you can check:

- La Femme (6L9) has blanket discounts
- La Femme (6L9) has a special offer
- V&A memberships (6KF) only allow a single seat to be selected and have a type
  of `use_last_perf`
- California Disney (9XY) is post only (can be used to generate 'no sends' if
  you select a performance date within the next few days)
- Flamingoland family passes (6KU) are not valid on Mondays

It can be useful to test different dispatch methods:

- The Unremarkable Incident (7AB) has "self-print voucher" (also known as an
  eTicket) with a barcode data attached
- Toy Story (7AA) has a "self-print voucher" option without the barcode


## Versioning

The API version number forms part of the URL, for example
https://demo.ticketswitch.com/f13/events.v1 uses version 1. We will always
endeavour to add functionality and make changes in a backward-compatible manner,
but when that is not possible we will implement a new version. We will then
introduce a deprecation schedule for the replaced version and notify all
partners that have integrated.


## API Support

We only provide support to partners that we have provided a test account to. 
If you have a test or live account and you encounter problems with the API:

1. Check the [Ingresso status page](https://status.ingresso.co.uk/). If there is 
an issue reported on the status page this indicates we are already aware.

2. If your issue does not fit the description of the current issue, or we have
not reported an issue you should contact us under the SLAs and with the contact 
details we have previously agreed.
