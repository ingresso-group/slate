#Troubleshooting

##Handling Crypto Blocks
> **Invalid**

```xml
<crypto_block>
    abc123
</crypto_block>
```

> **Valid**

```xml
<crypto_block>abc123</crypto_block>
```

Most calls to the API require passing in a crypto block. There are a few things
to take care of when passing crypto blocks to the api:

* Crypto blocks are only valid when acompanied by a `user_id` field. This is
  because each crypto block is signed by the user that created it.

* Crypto blocks can't contain any whitespace. This can be problematic when
  handling pretty printed XML (see example)

* Crypto blocks are created in series and extended as a user moves through the
  flow. You can't use any crypto block with any API method.
  All methods will state from what previous method call it expects the crypto
  block to be from. For example the [Event Search](#event-search) method call
  expects a crypto block from the [Start Session](#start-session) method call.
  Providing a crypto block from the [Date Time Options](#date-time-options) 
  method call instead would result in an error.


##Uniqueness of Event IDs / Tokens

The easiest way to uniquely identify an event is using the event_id (even_token is a synonym).

Alternatively an event can be uniquely identified by the tuple of codes
(source, area, venue, event). All four parts are required
to guarantee that an event is being uniquely specified, as the various
codes are only defined to be unique within the namespace corresponding
to the code above them in the data model. i.e. an event code is only
guaranteed to be unique within a particular venue code, though a backend
system may actually allocate unique codes for all events system-wide.