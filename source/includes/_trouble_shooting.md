#Trouble Shooting


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
  flow. As such every crypto block can't be unilaterally passed by every method.
  All methods will state from what previous method call it expects the crypto
  block to be from. For example the [Event Search](#event-search) method call
  expects a crypto block from the [Start Session](#start-session) method call,
  providing a crypto block from the [Date Time Options](#date-time-options) 
  method call instead, would result in an error.
