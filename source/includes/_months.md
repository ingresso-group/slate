# Months

```
GET https://api.ticketswitch.com/f13/months.v1/{username}?user_passwd={password}&event_id={eventid}
```

This section gives a summary of the availability across a calendar month

Months are provided for those who wish to display month-level summary data; for 
example listing the available months above a calendar. This method means that
you don't need to summarise full performance level data in your applications.

It is possible to request a number of additional attributes for a month - below
we have listed the default attributes first, followed by the optional
attributes:

> **Example request**

```shell
curl https://api.ticketswitch.com/f13/months.v1/demo \
    -d "user_passwd=demopass" \
    -d "event_id=6IF" \
    -G
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
client.get_months('6IF')
```

### Request

Parameter | Description
--------- | -----------
`event_id` | Identifier of the event to summarise


> **Example Response**

```shell
{
  "results": {
    "month": [
      {
        "month": "dec",
        "month_dates_bitmask": 1065254912,
        "month_desc": "December",
        "month_weekdays_bitmask": 63,
        "year": 2016
      },
      {
        "month": "jan",
        "month_dates_bitmask": 2012209087,
        "month_desc": "January",
        "month_weekdays_bitmask": 63,
        "year": 2017
      },
      {
        "month": "feb",
        "month_dates_bitmask": 251526135,
        "month_desc": "February",
        "month_weekdays_bitmask": 63,
        "year": 2017
      },
      {
        "month": "mar",
        "month_dates_bitmask": 2130574327,
        "month_desc": "March",
        "month_weekdays_bitmask": 63,
        "year": 2017
      },
      {
        "month": "apr",
        "month_dates_bitmask": 16254,
        "month_desc": "April",
        "month_weekdays_bitmask": 63,
        "year": 2017
      }
    ]
  }
}
```

```python
[
    pyticketswitch.Month(
        month=12,
        year=2016,
        description='December',
        month_bitmask=1065254912,
        weekdays_bitmask=63,
    ),
    pyticketswitch.Month(
        month=1,
        year=2017,
        description='January',
        month_bitmask=2012209087,
        weekdays_bitmask=63,
    ),
    pyticketswitch.Month(
        month=2,
        year=2017,
        description='Febuary',
        month_bitmask=251526135,
        weekdays_bitmask=63,
    ),
    pyticketswitch.Month(
        month=3,
        year=2017,
        description='March',
        month_bitmask=2130574327,
        weekdays_bitmask=63,
    ),
    pyticketswitch.Month(
        month=4,
        year=2017,
        description='April',
        month_bitmask=16254,
        weekdays_bitmask=63,
   )
]
```

### Response


<aside class="language-specific shell notice">
    This resource returns bit masks in some fields, for more information on how
    to decode this data <a href="#bitmask-fields">click here</a>
</aside>

Attribute | Description
--------- | -----------
`month` | three-character code for the month (TODO is this correct?)
`month_dates_bitmask` | indicates which dates in the month have valid performances
`month_desc` | full month name
`month_weekdays_bitmask` | indicates which weekdays have valid performances for the month
`year` | the year
