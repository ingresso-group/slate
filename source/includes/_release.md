# Release
> **Definition**

```
POST https://api.ticketswitch.com/f13/release.v1?transaction_uuid={trans_uuid}
```

This resource allows you to release previously-reserved tickets. It is important
that your integration releases tickets as soon as it is clear that they will not
be purchased, either because your customer explicitly removes them from a basket
or similar, or because they implicitly imply that they want to continue
browsing, for example by pressing back on your checkout page (when a user
presses back on sites developed by Ingresso we release their reservation then
request availability to ensure the user is also able to select the just-released
seats). This is necessary to ensure that all end users have the best possible
access to tickets (including your other customers).

<aside class="notice">We reserve the right to disable the API account of any 
partner that is not releasing tickets when they should be.</aside>

### Request

> **Example request**

```shell
curl https://api.ticketswitch.com/f13/release.v1 \
    -u "demo:demopass" \
    -d "transaction_uuid=f2158be3-d29e-11e6-8aab-0025903268dc" \
    -G
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
TODO: FINISH ME NIC
```

Parameter | Description
--------- | -----------
`transaction_uuid` | The unique identifier for a previous [reserve](#reserve).


### Response

> **Example response**

```shell
{
  "released_ok": true
}
```

This resource will return the same response when called multiple times.

Attribute | Description
--------- | -----------
`released_ok` | `true` if the release succeeded.
