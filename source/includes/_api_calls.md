#API Reference

> **API endpoint**

```
https://[your-id].tsd-aff.com/cgi-bin/xml_core.exe
```

The Ticketswitch API is built on the principals of
[XML-RPC](https://en.wikipedia.org/wiki/XML-RPC). Calls are made via HTTP to our
API server, and should be understandable by standard library HTTP clients.
The calls are designed to be called in series and session information is passed 
between calls by the client via cyrpto blocks. XML is returned by all responses 
(including errors).

> The example code below will demonstrate generic use cases.

The code on the right hand side is intended to be example code. All data (such
as user information, or event information) should be considered demo data, and
will not necessarily work as is.


##API Wrappers


> For more detailed examples please refer to the documentation for the individual wrapper

Our API wrappers:

* [Python](https://github.com/ingresso-group/pyticketswitch) - fully supported
* [Go](https://github.com/ingresso-group/goticketswitch) - incomplete but in progress
* [Java](https://github.com/ingresso-group/java) - this is a bit out of date - please speak to us first (api@ingresso.co.uk) if you plan to integrate against this.

Where the raw API will return in XML, the API wrappers aim to return language-specific objects.

###Client

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

The wrappers implement a thin client over the API. The first step in most
situations will be to instantiate a client.

<p class="lang-specific python go">
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


##TicketSwitch API basic calling conventions

The API endpoint to hit is: https://api.ticketswitch.com/tickets/xml_core.exe

When using raw XML in the TicketSwitch API:

* Data must be provided in a POST request. 
* The suggested content type header is text/xml.
* Any call can contain the "lang" element for language. It should not change during a particular session.

###Fail codes

If a request fails then the returned element will contain the elements
"fail_code" and "fail_desc". - The fail code is a numeric value indicating the problem

There are also eight generic errors. If a generic error occurs then the returned element will always be “script_error” and will contain the elements “error_code” and “error_desc”.

The error codes and their meanings are as follows:

1. A bad username has been provided in the “user_id” element.
2. User authorisation has failed. Either your password or crypto_block are invalid for the given user_id.
3. A connection could not be established to the backend ticketing system.
4. Your IP address is on a network forbidden from using the TicketSwitch API.
5. A connection could not be established to the main database.
6. Membership authentication has failed. This should never happen as membership schemes are not being used with the TicketSwitch API.
7. Bad data has been supplied.

It is possible to access the error information in different formats, supplying the necessary type required in the “mime_text_type” element: “plain”, “html”, “xml” or “vnd.wap.wml”