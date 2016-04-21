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
will be unlikely to work as is.


##API Wrappers


> For more detailed examples please refer to the individual wrappers documentation

We have API wrappers in [python](https://github.com/ingresso-group/pyticketswitch),
[java](https://github.com/ingresso-group/java), and 
[go](https://github.com/ingresso-group/goticketswitch).

Where the raw API will return in XML, the API wrappers aim to return
language-specific objects.

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
situations will be to instantiate a client

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
