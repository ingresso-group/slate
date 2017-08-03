# Send Methods

```
GET https://demo.ticketswitch.com/f13/send_methods.v1?perf_id={performanceid}
```

This section describes the available methods of delivery for a given
performance. Each method in the list has an associated cost which will be added
to the overall charge.

Send methods might change between performances and depending on times when they
are requested. For example there might not be enough time to post a physical
ticket to a customer before the performance starts, so those performances would
not have that option available. As such it's important that send methods are
called as needed to ensure the user isn't seeing options that are out of date.


> **Example request**

```shell
curl https://demo.ticketswitch.com/f13/send_methods.v1 \
    -u "demo:demopass" \
    -d "perf_id=6IF-B0I" \
    --compressed \
    -G
```

```python
from pyticketswith import Client

client = Client('demo', 'demopass')
send_methods, meta = client.get_send_methods('6IF-B0O')
```

### Request

Parameter | Description
--------- | -----------
`perf_id` | The performance identifier that you want to display send methods for.

> **Example response**

```shell
{
  "currency_code": "gbp",
  "currency_details": {
    "gbp": {
      "currency_code": "gbp",
      "currency_factor": 100,
      "currency_number": 826,
      "currency_places": 2,
      "currency_post_symbol": "",
      "currency_pre_symbol": "£"
    }
  },
  "send_methods": {
    "send_method": [
      {
        "send_code": "COBO",
        "send_cost": 1.5,
        "send_desc": "Collect from the venue",
        "send_type": "collect"
      },
      {
        "permitted_countries": {
          "country": [
            {
              "country_code": "ie",
              "country_desc": "Ireland"
            },
            {
              "country_code": "uk",
              "country_desc": "United Kingdom"
            }
          ]
        },
        "send_code": "POST",
        "send_cost": 3.5,
        "send_desc": "Post (UK & Ireland only)",
        "send_type": "post",
        "send_final_comment": "Please allow 3 working days to recieve your tickets."
      }
    ]
  }
}
```

```python
from pyticketswitch.country import Country
from pyticketswitch.send_method import SendMethod

[
    SendMethod(
        code='COBO',
        cost=1.5,
        description='Collect from the venue',
        type='collect',
    ),
    SendMethod(
        code='POST',
        cost=3.5,
        description='Post (UK & Ireland only)',
        type='post',
        final_comment='Please allow 3 working days to receive your tickets.'
        permitted_countries=[
            Country(
                code='ie',
                description='Ireland',
            ),
            Country(
                code='uk',
                description='United Kingdom',
            )
        ],
    )
]
```


### Response

Attribute | Description
--------- | -----------
`send_code` | The identifier of this send method.
`send_cost` | Any additional cost that the customer will have to pay if they chose this send method.
`send_desc` | A human readable description of the send method.
`send_final_comment` | A human readable string containing information about posting times.
`send_type` | Can be `collect` which indicates that the ticket will have to be collected in person, either at the venue or from a confirmation email. A type of `post` indicates that a physical ticket will be posted to the delivery address given at purchase time.
`permitted_countries` | If this is returned it indicates that the send method is only available to addresses in the listed countries.
`permitted_countries.country_code` | 2-digit country code (using [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2)).
`permitted_countries.country_description` | Human readable description of the country.


The outer object also includes a `currency_code` and a `currency_details` object
containing further currency detail:

Attribute | Description
--------- | -----------
`currency_code` | [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217) three letter code
`currency_factor` | Multiply by this number to get values in the base unit (e.g. multiplying $47.11 by the currency_factor will give 4711 cents)
`currency_number` | [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217) numeric identifier
`currency_places` | The number of decimal places to display (eg 45.5 usd should be displayed as 45.50)
`currency_post_symbol` | A symbol to display at the end of the price
`currency_pre_symbol` | A symbol to display in front of the price
