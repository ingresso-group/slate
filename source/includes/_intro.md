# TODOs / questions

* Can we tidy up the URLs: get rid of "json_", ".exe" and "cgi-bin"?
* What about versioning? Should we build this in now for when we need to make breaking changes?
* Get rid of json_event_info if redundant?
* Should we use HTTP authentication?
* Should we have the concept of a venue with an ID? (when the venue is enforced)
* Is it a problem to include passwords within the URL?
* Use a test API server or some form of rate limiting for particular users? Would allow us to give open access without worrying about someone hammering our main API server.
* A reporting API would be useful - our internal reporting apps should also use this


# Introduction

The Ingresso API is designed for partners who sell from Ingresso's inventory 
of tickets. It is a fully transactional API. 

The API is very loosely based on [REST](http://en.wikipedia.org/wiki/Representational_State_Transfer). GET 
requests and basic query paremeters are used to drive the API. You format requests 
in [JSON](http://www.json.org/) and you will receive either a JSON-formatted 
response or an HTTP error. 

We describe objects in a consistent way across different calls 
(for example the output of [events](#events) and [performances](#performances)
(TODO fix names) includes a cost_range object and this is described in the 
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

The Ingresso API supports multiple use cases, but below is a typical workflow:

* [json_events](#json-events) returns a list of events based on search criteria.
* []() returns a list of performances for an event.
* []() returns availability for a performance, including seat numbers if the event is seated.
* *Optional:* [](), []() and []() can be used to manage shopping baskets.
* [reserve]() specific seats or best available tickets for a set period of time. 
If using basketing `reserve` will attempt to reserve all items in the basket.
* [purchase]() the reserved tickets.


## How to get access to the API

All examples shown in this documentation are for the `demo` user. 
This user has access to test product only - you can make purchases with this user without any impact on 
live tickets.

To request your own test or live API credentials email us: api@ingresso.co.uk.
(TODO test the demo user - should be an on-credit user)


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


## Variables controlling output

The output from a given API call may be augmented beyond the
basic result in one of two ways. Firstly there are extra functions
which may be availble for a given API call, which are specific to
that call. These are triggered using variables with the prefix 'add_'
or 'include_' such as 'add_discount_codes' on the availability call which
is used to control whether or not discount codes are also retrieved and
returned.

The second type of augmentation is specific to the retruned objects
themselves and is thus applicable to the output of any call whcih returns
one of these obejcts. The most obvious example is an event object, which
can be described with many extra pieces of information about it. These
varaibles are all prefixed with `req_` as they are requests to the objects
to output something extra or different about themselves, for example `req_cost_range`.

Note that the 'req_' and 'add_' / 'include_' variables all result in extra
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

For best available testing, use "Matthew Bourne's 
Nutcracker TEST" (event ID = 6IF) and "Matthew Bourne's Swan Lake test" (6IE). 

To test selecting specific seats use any of these 
events: 3CVA, 3CVB, 3CVC, 3CVD, 3CVE, 3CVF, 3CVG.
Note that we have a seat selection seating plan available for 3CVE - this will be helpful
if you decide to implement seat selection on your front end (please email 
api@ingresso.co.uk and we can talk you through this). (TODO: does api@ingresso.co.uk actually work?)

It may be useful to use our [B2B website](https://b2b.ingresso.co.uk/b2b/) 
as a working example during your development. 
You can sign in using your own credentials or with agent ID: `demo` and password: `demopass`

Some of the test events have specific conditions that you can check:

- Swan Lake (6IE) has default discounts only
- Swan Lake (6IE) reserves will fail for Tuesday performances
- Swan Lake (6IE) has restricted view seats on ticket 2 and 3 on thursdays
- The Nutcracker (6IF) has a maximum of 3 mixed discounts
- The Nutcracker (6IF) has no availability on saturday nights
- The Nutcracker (6IF) has no discounts in the stalls on the 1st of the month
- The Nutcracker (6IF) has no OPA or STUDENT discounts in price band B
- La Femme has blanket discounts (TODO add event IDs for all of these events)
- La Femme has a special offer
- V&A memberships only allow a single seat to be selected and have a type of use_last_perf
- Eurodisney enforces one adult or student for a booking
- California Disney is post only (can be used to generate 'no sends') (TODO how?)
- Tokyo Disney - add junior codes for bundle failure
  - 2 - auth failure
  - 3 - auth timeout
  - 4 - booking refused
- Rambert never has any available performances
- Flamingoland family passes are not valid on mondays


## API Variations

TODO is it worth describing some high-level variations here? Freesale, allocation, seated, seat selection, no performances, no perf times eg goldentours (we should also fix this).

