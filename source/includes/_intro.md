<!---
# TODOs / Questions

* WHAT ABOUT THIS:
The username must be passed in explicitly as part of the path, being the
component after the '.v1'. Some of our partners require sub users for certain
use cases such as running their own affiliate programme; if a sub  user is to be
passed in  then this should be the second component, and any explicit language,
overriding the HTTP header, as the third. A '-' anywhere in this set of three
path components is treated as a placehold for the information not being passed -
i.e. it is ignored. So you can, for example, call json_events as 'demo' with a
language of 'de' like this.:

`/f13/events.v1/demo/-/de?user_passwd=demopass`

The sub user there is not set to '-', instead it is treated as unset 
and can be passed in as a query string argument if desired.

* Matt to add docs for cities and categories
* Matt to include a section describing how our caching works
* Matt to add a test meta event, and refer to this in the test intro section and wherever meta events are mentioned.
* Purchase
* Transaction Info

* Error codes need to be added everywhere by Pete.

* Pete to default self_print_mode to html - this normally confuses distributors
* Amazon suggested we return a 429 - TOO MANY REQUESTS when we return backend_call_failed or backend_call_throttling_failed. Should we use http status codes in this type of case? These cases are an exception where they don't really look like errors (initially by design...)
* There are a number of places where the JSON object nesting isn't as simple as it could be. Eg instead of "event_upsell_list": ["6IE","MH0"] we have:
  "event_upsell_list": {
    "event_id": [
      "6IE",
      "MH0"
    ]
  }

  Similarly can this:
  "class": [
    {
      "class_desc": "Arts & Culture"
    }
  ]

  be simplified to this:
  "class": ["Arts & Culture"]

* For bitmasks, rather than this:
  "quantity_options": {
    "valid_quantity_bitmask": 126
  }
  We should be returning:
  "valid_quantities": [2, 3, 4, 5, 6, 7]

  Similarly for discount_disallowed_seat_no_bitmask, use "discount_disallowed_seat_no": [3, 4]

  However the bitmasks make sense for avail_details data since it is way too verbose to not use them, and it means you only need to worry about bitmasks if you use that call (which won't be commonly used). I suggest we go with that and remove the flags to use bitmasks or not.

* We should remove user_review_percent until we actually have some reviews in the core
* Use a single req_media rather than requesting individual items. Merge images and video into media. Simplify the attributes returned eg just return the URL and is_secure rather than splitting up the URL into components and listing http and https variants.

Possible V2 functionality:
* A test API server or some form of rate limiting for particular users would help. Would allow us to give open access without worrying about someone hammering our main API server.
* Allowing search on cost ranges would be useful
* Can we be more restful eg /events/25DR to get detail by event ID?
* A reporting API would be useful - our internal reporting apps should also use this
* Should we have the concept of a venue with an ID? (when the venue is enforced)
-->

# IMPORTANT NOTE

This API is currently a work in progress - until this message is removed v1 of
the API is subject to change.


# Introduction

The Ingresso API is designed to allow you to sell from Ingresso's inventory of
event tickets.  Ingresso has connected to a large number of ticketing system
APIs, allowing us to transact directly on the venue's system.  By integrating
with the Ingresso API you are able to view and purchase live ticketing inventory 
from a number of different venues. 

Example partners that have integrated with the Ingresso API are 
[Amazon](https://tickets.amazon.co.uk/) and 
[lastminute.com](http://www.lastminute.com/theatre/). We also build our own 
ticketing websites entirely on top of our API (for example 
[Disney Tickets](https://www.disneytickets.co.uk) and
[From The Box Office](https://www.fromtheboxoffice.com)). 

The Ingresso API is a fully transactional API, loosely based on REST. You format
GET or POST requests in [JSON](http://www.json.org/) and you will receive either
a JSON-formatted response or an HTTP error. The API is known as F13, which
is why you see /f13 in the endpoint.

We describe objects in a consistent way across different resources (for example the
output of [events](#events) and [performances](#performances) includes a
[cost_range object](#cost-range-object) and this is described in the same
format. The idea of this is that standard code can be used on the client side to
read all parts of the output.

This API replaces the [the Ingresso XML API](http://www.ingresso.co.uk/apidocs/). 
The XML API relies on temporary tokens from each call being passed in
to the next call; these has been removed in favour of permanent IDs. One
benefit of this change is that a call near the end of the booking process, such
as [reserve](#reserve), can be called without needing to remake the preceeding
calls (such as [requesting availability](#availability)), opening up additional
options for integrating partners.


## Postman examples

<img src="https://d1wx4w35ubmdix.cloudfront.net/wl-media/images/postman.png" alt="Postman screenshot">

We have a public library available for [Postman](https://www.getpostman.com/) 
that closely matches the examples included in this documentation.

Click below to import our collection of API examples.

[![Run in Postman](https://run.pstmn.io/button.svg)](https://www.getpostman.com/collections/7c834c45808672b158a7)


## Philosophy

* Returned data is as simple as possible for the task in-hand

* Transacton flow is as simple as possible for the task in hand (TODO this isn't
clear enough)

* Default missing parameters sensibly instead of erroring

* Always try and complete a call in some way instead of erroring


## Basic booking flow

The Ingresso API supports multiple use cases and variations, but below is a 
typical workflow:

* Retrieve a list of [events](#events).

* Retrieve a list of [performances](#performances) for an event.

* Retrieve [availability](#availability) for a performance, including seat
numbers if the event is seated.

* [reserve](#reserve) tickets for a set period of time.

* [purchase](#purchase) the reserved tickets.


## Types of API integration

The Ingresso API supports a variety of different integration types. The most
common is a **full integration** - this is where our partner uses all
functionality of the Ingresso API - to retrieve listings of events and
performance (often in advance to populate their own database) then to request
availability and reserve and purchase tickets in real-time.

Ingresso also offer partners an individually branded "white label" ticketing
website - for example [Disney Tickets](https://www.disneytickets.co.uk) and
[From The Box Office](https://www.fromtheboxoffice.com) are fully developed by
Ingresso. It is possible to use the API in conjunction with a white label
website as a **partial integration**. Some examples of partial integrations:

* Maintain a listing of events on your own site, and use Ingresso's white label
website for booking the tickets. In this case the Ingresso API can be used to 
[list events](#events-list).

* Ingresso have invested in a high-converting booking app (where the user
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

1. **On-credit**: you purchase tickets from Ingresso on-credit and take payment
from the customer yourself. You will be invoiced regularly by Ingresso.

2. **Stripe**: you use [Stripe](https://stripe.com) to take payment for all
Ingresso tickets, either into your own Stripe account (you will then be invoiced
by Ingresso) or directly into Ingresso's Stripe account. Stripe is a developer-
friendly payment provider that is simple to integrate with. Our API provides
integration support for Stripe - this is explained in more detail in
[reserve](#reserve).

3. **Venue or other payment provider**: you either pass the customer's card
details to the supplier or redirect the customer to a payment provider specified
by Ingresso. This is not typically offered to partners - if you think this is
required please contact us: api@ingresso.co.uk.


## How to get access to the API

All examples shown in this documentation are for the `demo` user. This user has
access to test product only - you can make purchases with this  user without any
impact on live tickets, or any need to contact us first.

If you would like your own API credentials in order to start selling please 
email us: api@ingresso.co.uk


## Authentication

> **Example request**

```shell
curl https://api.ticketswitch.com/f13/events.v1 \
    -u "demo:demopass" \
    -d "s_keys=matthew" \
    -d "s_coco=uk" \
    -G
```

```python
from pyticketswitch import Client

client = Client(user='demo', password='demopass')
```

Authenticate when using the API by including your username and password in the 
request. Authentication to the API is performed via 
[HTTP Basic Auth](https://en.wikipedia.org/wiki/Basic_access_authentication). 

<aside class="notice">Please ensure you keep your username and password secret - do not share 
these in publicly accessible areas such GitHub, client-side code, etc.</aside>

<blockquote class="lang-specific shell">
curl uses the -u flag to pass basic auth credentials in the format user:password.
</blockquote>


## Errors

(TODO include a list of error codes here, HTTP status codes or otherwise.)

Note that HTTP error codes are not returned as part of expected API usage - such
things as no  availability are indicated by an empty list in a successful
response, rather than a 404 which is, arguably, the correct REST response. Even
when an HTTP error *is* returned, however, there will be a JSON block
acocmpanying it which contains human readable error text so that the caller has
some idea of what they have done wrong.

To check whether there is a problem with the Ingresso API or the supplier
systems we connect to, you can check the [Ingresso status
page](https://status.ingresso.co.uk/).


## Variables controlling output

The output from a given resource may be augmented beyond the
basic result in one of two ways. Firstly there are extra functions
which may be availble for a given resource, which are specific to
that resource. These are triggered using variables with the prefix `add_`
or `include_` such as `add_discount_codes` on the availability resource which
is used to control whether or not discount codes are also retrieved and
returned.

The second type of augmentation is specific to the returned objects themselves
and is thus applicable to the output of any resource which returns one of these
objects. The most obvious example is an event object, which can be described
with many extra pieces of information about it. These varaibles are all prefixed
with `req_` as they are requests to the objects to output something extra or
different about themselves, for example `req_cost_range`.

Note that the `req_` and `add_` / `include_` variables all result in extra
processing to retrieve the requested data, which will slow down the resource,
sometimes substantially. They should therefore only be used where necessary.


## API libraries / SDKs

Our API libraries implement a thin client over the API. Where the API 
will return JSON, the API wrappers return language-specific objects. We 
have developed a [Python API library](https://github.com/ingresso-group/pyticketswitch) 
(TODO replace URL).


<blockquote class="lang-specific python">
<p>
<strong>Definition</strong>
</p>
</blockquote>

```python
pyticketswitch.Core()
```

<blockquote class="lang-specific python">
<p>
<strong>Example</strong>
</p>
</blockquote>

```python
import pyticketswitch

api = pyticketswitch.Core(
        url='https://api.ticketswitch.com/tickets/xml_core.exe',
        username='demo',
        password='demopass',
)
```


<p class="lang-specific python">
The first step in most situations will be to instantiate a client. (TODO these code examples refer to XML wrappers)
<br/><br/>
<strong>Required Parameters</strong>
</p>

<table class="lang-specific python">
<thead><tr><th>Parameter</th><th>Type</th><th>Description</th></tr></thead>
<tbody>
<tr><td><code>url</code></td><td>string</td><td>API end point that the client will communicate with</td></tr>
<tr class="lang-specific python"><td><code>username</code></td><td>string</td><td>Ticketswitch <code>user_id</code></td></tr>
<tr class="lang-specific python"><td><code>password</code></td><td>string</td><td>Ticketswitch <code>user_passwd</code></td></tr>
</tbody>
</table>

(TODO also include how to install eg with pip install?)



## Testing

We have a number of test events that you can use to view test availability and
make test purchases.

For testing best available events, use "Matthew Bourne's Nutcracker TEST" (event
ID = 6IF) and "Matthew Bourne's Swan Lake test" (6IE).

For testing events that allow you to select specific seats, use any of these
events: 3CVA, 3CVB, 3CVC, 3CVD, 3CVE, 3CVF or 3CVG. 
Note that we have a seat selection seating plan available for 3CVE - this will
be helpful if you decide to implement seat selection on your front end (please
email  api@ingresso.co.uk and we can talk you through this). The availability on
these seat selection events is limited, so please keep test purchasing to a
minimum.

It may be useful to use our [B2B website](https://b2b.ingresso.co.uk/b2b/) as a
working example during your development.
You can sign in using your own credentials or with affiliate ID: `demo` and
password: `demopass`

We have a number of other events for different types of events such as
attractions and hotels - you will see these if you [list all events](#events).

It can be useful to test different despatch methods:

- The Nutcracker (6IF) has "collect from the venue" and "post"
- Test Event - Type 1 (3CVA) has "self-print voucher" (also known as eticket)

Some of the test events have specific conditions that you can check:

- Swan Lake (6IE) has default discounts only
- Swan Lake (6IE) reserves will fail for Tuesday performances
- Swan Lake (6IE) has restricted view seats on ticket 2 and 3 on thursdays
- The Nutcracker (6IF) has a maximum of 3 mixed discounts
- The Nutcracker (6IF) has no availability on saturday nights
- The Nutcracker (6IF) has no discounts in the stalls on the 1st of the month
- The Nutcracker (6IF) has no OPA or STUDENT discounts in price band B
- La Femme (6L9) has blanket discounts
- La Femme (6L9) has a special offer
- V&A memberships (6KF) only allow a single seat to be selected and have a type of use_last_perf
- California Disney (9XY) is post only (can be used to generate 'no sends' if you select a performance date within the next few days)
- Flamingoland family passes (6KU) are not valid on Mondays


## API Variations

TODO is it worth describing some high-level variations here? Freesale,
allocation, seated, seat selection, no performances, no perf times eg
goldentours (we should also fix this).


## Versioning

The API version forms part of the URL, for example
https://api.ticketswitch.com/f13/events.v1 uses version 1. We will always
endeavour to add functionality and make changes in a backward-compatible manner,
but when that is not possible we will implement a new version. We will then
introduce a deprecation schedule for the replaced version and notify all
partners that have integrated.


## API Support

We only provide support to partners that we have provided a test account to. 
If you have a test or live account and you encounter problems with the API:

1. Check the [Ingresso status page](https://status.ingresso.co.uk/)

2. If there is an issue reported on the status page this indicates we are
already aware.

3. If your issue does not fit the description of the current issue, or we have
not reported an issue you should contact us under the SLAs and with the contact 
details we have previously agreed.
