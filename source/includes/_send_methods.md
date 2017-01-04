# Send Methods

```
GET https://api.ticketswitch.com/f13/send_methods.v1/{username}?user_password={password}&perf_id={performanceid}
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
curl https://api.ticketswitch.com/f13/send_methods.v1/demo \
    -d "user_passwd=demopass" \
    -d "perf_id=6IF-B0I" \
    -G
```

```python
from pyticketswith import Client

client = Client('demo', 'demopass')
client.get_send_methods('6IF-A8B')
```

### Request

Parameter | Description
--------- | -----------
`perf_id` | The performance identifier that you want to display send methods for.

> **Example response**

```shell
{
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
        "send_type": "post"
      }
    ]
  }
}
```

```python
[
    pyticketswitch.SendMethod(
        code='COBO',
        description='Collect from the venue',
        cost=1.5,
        typ='collect'
    ),
    pyticketswitch.SendMethod(
        code='POST',
        description='Post (UK & Ireland only)',
        cost=3.5,
        type='post',
        permitted_countries=[
            pyticketswitch.Country(code='ie', description='Ireland'),
            pyticketswitch.Country(code='uk', description='United Kingdom'),
        ]
    )
]
```


### Response


Attribute | Description
--------- | -----------
`send_code` | The identifier of this send method.
`send_desc` | A human readable description of the send method.
`send_cost` | Any additional cost that the customer will have to pay if they chose this send method.
`send_type` | Can be `collect` which indicates that the ticket will have to be collected in person, either at the venue or from a confirmation email. A type of `post` indicates that a physical ticket will be posted to the delivery address given at purchase time.
`permitted_countries` | If this is returned it indicates that the send method is only available to addresses in the listed countries.
`permitted_countries.country_code` | 2-digit country code (using [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2)).
`permitted_countries.country_description` | Human readable description of the country.
