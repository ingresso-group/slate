<!---
# TODOs / Questions

* Can we tidy up the URLs: get rid of "json_", ".exe" and "cgi-bin"?
* API versioning is a common practice. Should we build this in now for when we need to make breaking changes in future?
* Is it useful to return the detail for previous objects? Eg in json_performances is there a need to return the event object? (excluding meta events)
* Replace seatprice and surcharge with face_value and booking_fee? This has caused confusion before.
* The Python wrapper is simplifying a number of concepts from the API. We need to be consistent across JSON API and the wrappers - if a concept is too complex we should simplify everywhere. Eg the python is using positive flags everywhere rather than negative sometimes eg has_no_perfs.
* Merge images and video into media. Can we also reduce the number of fields returned for each media instance?
* Pete to default self_print_mode to html by default - this normally confuses distributors
* Pete to add ability to return availability for non-live events - we can then use F13 to allow ops to run availability checks before the event is live and make use of the results
* Amazon suggested we return a 429 - TOO MANY REQUESTS when we return <backend_call_failed/> or <backend_call_throttling_failed/>. Should we use http status codes in this type of case?
* Should we add a flag to distinguish between no booking fee and discounted face value offers? If not then need to explain how to distinguish between these offer types in events / perfs / availability
* We have been asked to produce lists of categories and regions / cities - for now I have a google doc but we should add an API for this
* Matt to include a section describing how our caching works
* Matt to rerun examples now that bit masks are replaced / flagged (and explain how to use bit masks)
* Can we add price-based search to availability?
* Any reason not to use a single req_media rather than requesting individual items? Should we simplify the attributes returned eg just return the URL and is_secure rather than splitting up the URL into components and listing http and https variants?
* Use a test API server or some form of rate limiting for particular users? Would allow us to give open access without worrying about someone hammering our main API server.
* Allowing search on cost ranges would be useful
* A reporting API would be useful - our internal reporting apps should also use this
* Should we be more restful eg /events/25DR to get detail by event ID? What do we lose by not being true rest?
* event_type is one of `simple_ticket`, `hotel_room` or `misc_item`. Do we have any hotel_rooms or misc_items currently? If not should we hide this from the API consumer?
* Should we use HTTP authentication?
* Should we have the concept of a venue with an ID? (when the venue is enforced)
-->

# IMPORTANT NOTE

This API is a work in progress so please note that it is subject to change.


# Introduction

The Ingresso API is designed to allow you to sell from Ingresso's inventory of event tickets. 
Ingresso has connected to a large number of ticketing system APIs, allowing us to transact directly on the venue's system. 
By integrating with the Ingresso API you are able to view and purchase live inventory from a number of different venues.

The Ingresso API is a fully transactional API, very loosely based on REST. 
GET requests and basic query paremeters are used to drive the API. You format requests 
in [JSON](http://www.json.org/) and you will receive either a JSON-formatted 
response or an HTTP error. 

We describe objects in a consistent way across different calls 
(for example the output of [events](#events) and [performances](#performances)
includes a cost_range object and this is described in the 
same format. The idea of this is that standard code
can be used on the client side to read all parts of the output.
Rather than relegate these common objects to an appendix at
the end of this document we have instead chosen to describe each one where
it first occurs, which hopefully reduces the need to flip backwards and forwards.

This API replaces the [the Ingresso XML API](http://www.ingresso.co.uk/apidocs/). 
The XML API relies on temporary tokens from the preceeding call being passed in 
to the next call - these has been removed in favour of permanent IDs. One benefit 
of this change is that a call near the end of the booking process, such as 
[reserve](TODO), can be called without needing to remake the preceeding calls 
(such as requesting availability). 


## Philosophy

* Returned data is as simple as possible for the task in-hand
* Transacton flow is as simple as possible for the task in hand (TODO this isn't clear enough)
* Default missing parameters sensibly instead of erroring
* Always try and complete a call in some way instead of erroring


## Basic booking flow

The Ingresso API supports multiple use cases and variations, but below is a typical workflow:

* get a list of [events](#events) based on search criteria.
* get a list of [performances](#performances) for an event.
* get [availability](#availability) for a performance, including seat numbers if the event is seated.
* *Optional:* [](), []() and []() can be used to manage shopping baskets. (TODO)
* [reserve](#reserve) specific seats or best available tickets for a set period of time. If using basketing `reserve` will attempt to reserve all items in the basket.
* [purchase](#purchase) the reserved tickets.


## How to get access to the API

All examples shown in this documentation are for the `demo` user. 
This user has access to test product only - you can make purchases with this user without any impact on 
live tickets.

To request your own test or live API credentials email us: api@ingresso.co.uk.


## Authentication

The username and password should be passed in with every call. 
(TODO does it only need to be set once for the client libraries?)

The password should be passed in the query string with `user_passwd`.

The username is to be passed in explicitly as part of the path, being
the component after the '.exe'. Some of our partners require sub users for 
certain use cases such as running their own affiliate programme; if a sub 
user is to be passed in 
then this should be the second component, and any explicit language,
overriding the HTTP header, as the third. A '-' anywhere in this
set of three path components is treated as a placehold for the information
not being passed - i.e. it is ignored. So you can, for example,
call json_events as 'demo' with a language of 'de' like this.:

	/cgi-bin/json_events.exe/demo/-/de?user_passwd=demopass

The sub user there is not set to '-', instead it is treated as unset 
and can be pased in as query string argument if desired.


## Errors

(TODO include a list of error codes here, HTTP status codes or otherwise.)

Note that HTTP error codes are not returned as part of expected API usage - such things as no 
availability are indicated by
an empty list in a successful response, rather than a 404 which is, arguably, the
correct REST response. Even when an HTTP error *is* returned, however, there
will be a JSON block acocmpanying it which contains human readable
error text so that the caller has some idea of what they have done wrong.

To check whether there is a problem with the Ingresso API or the backend systems we connect to you can check the [Ingresso status page](https://status.ingresso.co.uk/).

## Variables controlling output

The output from a given API call may be augmented beyond the
basic result in one of two ways. Firstly there are extra functions
which may be availble for a given API call, which are specific to
that call. These are triggered using variables with the prefix `add_`
or `include_` such as `add_discount_codes` on the availability call which
is used to control whether or not discount codes are also retrieved and
returned.

The second type of augmentation is specific to the returned objects
themselves and is thus applicable to the output of any call which returns
one of these objects. The most obvious example is an event object, which
can be described with many extra pieces of information about it. These
varaibles are all prefixed with `req_` as they are requests to the objects
to output something extra or different about themselves, for example `req_cost_range`.

Note that the `req_` and `add_` / `include_` variables all result in extra
processing to retrieve the requested data, which will slow down the call,
sometimes substantially. They should therefore only be used where absolutely necessary.


## API libraries / SDKs

Our API libraries implement a thin client over the API. Where the API 
will return JSON, the API wrappers return language-specific objects. We 
have developed API libraries for the following languages:

* [Python](https://github.com/ingresso-group/pyticketswitch) (TODO replace URL) - fully supported
* [Go](https://github.com/ingresso-group/goticketswitch) (TODO replace URL) - incomplete but in progress

<blockquote class="lang-specific python go">
<p>
<strong>Definition</strong>
</p>
</blockquote>

```python
pyticketswitch.Core()
```

```go
ticketswitch.Client()
```

<blockquote class="lang-specific python go">
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

```go
import "github.com/ingresso-group/goticketswitch"

client := ticketswitch.NewClient("https://api.ticketswitch.com/tickets/xml_core.exe")
```


<p class="lang-specific python go">
The first step in most situations will be to instantiate a client. (TODO these code examples refer to XML wrappers)
<br/><br/>
<strong>Required Parameters</strong>
</p>

<table class="lang-specific python go">
<thead><tr><th>Parameter</th><th>Type</th><th>Description</th></tr></thead>
<tbody>
<tr><td><code>url</code></td><td>string</td><td>API end point that the client will communicate with</td></tr>
<tr class="lang-specific python"><td><code>username</code></td><td>string</td><td>Ticketswitch <code>user_id</code></td></tr>
<tr class="lang-specific python"><td><code>password</code></td><td>string</td><td>Ticketswitch <code>user_passwd</code></td></tr>
</tbody>
</table>

(TODO also include how to install eg with pip install?)



## Testing

We have a number of test events that you can use to view test availability and make test purchases.

For testing best available events, use "Matthew Bourne's Nutcracker TEST" (event ID = 6IF) and "Matthew Bourne's Swan Lake test" (6IE). 

For testing events that allow you to select specific seats, use any of these 
events: 3CVA, 3CVB, 3CVC, 3CVD, 3CVE, 3CVF or 3CVG.
Note that we have a seat selection seating plan available for 3CVE - this will be helpful
if you decide to implement seat selection on your front end (please email 
api@ingresso.co.uk and we can talk you through this).
The availability on these seat selection events is limited, so please keep test purchasing to a minimum.

It may be useful to use our [B2B website](https://b2b.ingresso.co.uk/b2b/) 
as a working example during your development. 
You can sign in using your own credentials or with affiliate ID: `demo` and password: `demopass`

We have a number of other events for different types of events such as attractions and hotels - you will see these if you [list all events](#events). 

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

TODO is it worth describing some high-level variations here? Freesale, allocation, seated, seat selection, no performances, no perf times eg goldentours (we should also fix this).


## API Support

We only provide support to customers that we have provided a test account to. If you have a test or live account and you encounter problems with the API:

1. Check the [Ingresso status page](https://status.ingresso.co.uk/)
2. If there is an issue reported on the status page this indicates we are already aware. 
3. If your issue does not fit the description of the current issue, or we have not reported an issue you should contact us using the contact details and under the SLAs we have previously agreed.
