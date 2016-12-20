# Extra Data

This section describes the optional extra arguments that can be requested from
any call in the system to provide additional data on core objects. This data is
not displayed by default because it's slow to produce and/or might produce large
amounts of data.


## Availability details

> **Example request**

```shell
curl https://api.ticketswitch.com/f13/events_by_id.v1/demo \
    -d "user_passwd=demopass" \
    -d "event_id_list=6IF" \
    -d "req_avail_details" \
    -G
```

```python
from pyticketswitch import Client

client = Client(user='demo', password='demopass')
event = client.get_events(['6IF'], availability=True)
```
Availability details are a cached list of the price bands available for this
event across all performances. They are cached from previous availability
requests made via your username. This data can be useful for example if you wish
to display a separate page with pricing detail for all price bands on sale.

Availability details are generated from availability requests made either by
end-users or by scheduled processes that Ingresso use to update this data. You
should not attempt to make multiple availability requests in order to keep this
data up to date - please contact us instead to discuss options
api@ingresso.co.uk.

Availability details are only ever returned as part of a parent object (such as
[event](#event-object)).

Attribute | Description
--------- | -----------
`avail_currency` | description of the currency of the given price
`available_dates` | `first_yyyymmdd` and `last_yyyymmdd` for the range. If the req_avail_details_with_perfs parameter was included then a nested list of available dates is also added (year -> month -> day)
`day_mask` | which days of the week where have we seen this price band availability
`quantity_options` | the available quantities we have seen for this price band
`seatprice` | the per-ticket face value
`surcharge` | the per-ticket booking fee

> **Example response**

```shell
{
  "events_by_id": {
    "6IF": {
      "event": {
        "avail_details": {
          "ticket_type": [
            {
              "price_band": [
                {
                  "avail_detail": [
                    {
                      "avail_currency": {
                        "currency_code": "gbp"
                      },
                      "available_dates": {
                        "first_yyyymmdd": "20161221",
                        "last_yyyymmdd": "20170419"
                      },
                      "available_weekdays_bitmask": 63,
                      "quantity_options": {
                        "valid_quantity_bitmask": 126
                      },
                      "seatprice": 47,
                      "surcharge": 0
                    }
                  ],
                  "price_band_code": "A",
                  "price_band_desc": ""
                }
              ],
              "ticket_type_code": "BALCONY",
              "ticket_type_desc": "Balcony"
            },
            {
              "price_band": [
                {
                  "avail_detail": [
                    {
                      "avail_currency": {
                        "currency_code": "gbp"
                      },
                      "available_dates": {
                        "first_yyyymmdd": "20161221",
                        "last_yyyymmdd": "20170419"
                      },
                      "available_weekdays_bitmask": 63,
                      "quantity_options": {
                        "valid_quantity_bitmask": 126
                      },
                      "seatprice": 35,
                      "surcharge": 0
                    }
                  ],
                  "price_band_code": "A",
                  "price_band_desc": ""
                },
                {
                  "avail_detail": [
                    {
                      "avail_currency": {
                        "currency_code": "gbp"
                      },
                      "available_dates": {
                        "first_yyyymmdd": "20161221",
                        "last_yyyymmdd": "20170419"
                      },
                      "available_weekdays_bitmask": 63,
                      "quantity_options": {
                        "valid_quantity_bitmask": 126
                      },
                      "seatprice": 30,
                      "surcharge": 0
                    }
                  ],
                  "price_band_code": "B",
                  "price_band_desc": ""
                },
                {
                  "avail_detail": [
                    {
                      "avail_currency": {
                        "currency_code": "gbp"
                      },
                      "available_dates": {
                        "first_yyyymmdd": "20161221",
                        "last_yyyymmdd": "20170419"
                      },
                      "available_weekdays_bitmask": 63,
                      "quantity_options": {
                        "valid_quantity_bitmask": 126
                      },
                      "seatprice": 25,
                      "surcharge": 0
                    }
                  ],
                  "price_band_code": "C",
                  "price_band_desc": ""
                }
              ],
              "ticket_type_code": "CIRCLE",
              "ticket_type_desc": "Upper circle"
            },
            {
              "price_band": [
                {
                  "avail_detail": [
                    {
                      "avail_currency": {
                        "currency_code": "gbp"
                      },
                      "available_dates": {
                        "first_yyyymmdd": "20161221",
                        "last_yyyymmdd": "20170419"
                      },
                      "available_weekdays_bitmask": 63,
                      "quantity_options": {
                        "valid_quantity_bitmask": 126
                      },
                      "seatprice": 21,
                      "surcharge": 0
                    }
                  ],
                  "price_band_code": "A",
                  "price_band_desc": ""
                },
                {
                  "avail_detail": [
                    {
                      "avail_currency": {
                        "currency_code": "gbp"
                      },
                      "available_dates": {
                        "first_yyyymmdd": "20161221",
                        "last_yyyymmdd": "20170419"
                      },
                      "available_weekdays_bitmask": 63,
                      "quantity_options": {
                        "valid_quantity_bitmask": 14
                      },
                      "seatprice": 18,
                      "surcharge": 0
                    }
                  ],
                  "price_band_code": "B",
                  "price_band_desc": ""
                }
              ],
              "ticket_type_code": "STALLS",
              "ticket_type_desc": "Stalls"
            }
          ]
        },
        "city_desc": "London",
        "class": [
          {
            "class_desc": "Ballet & Dance"
          }
        ],
        "country_code": "uk",
        "country_desc": "United Kingdom",
        "critic_review_percent": 100,
        "custom_filter": [],
        "event_desc": "Matthew Bourne's Nutcracker TEST",
        "event_id": "6IF",
        "event_path": "/6IF-matthew-bourne-s-nutcracker-test/",
        "event_status": "live",
        "event_type": "simple_ticket",
        "event_upsell_list": {
          "event_id": [
            "6IE",
            "MH0"
          ]
        },
        "geo_data": {
          "latitude": 51.52961137,
          "longitude": -0.10601562
        },
        "has_no_perfs": false,
        "is_seated": true,
        "max_running_time": 120,
        "min_running_time": 120,
        "need_departure_date": false,
        "need_duration": false,
        "need_performance": true,
        "postcode": "EC1R 4TN",
        "show_perf_time": true,
        "source_code": "ext_test0",
        "source_desc": "External Test Backend 0",
        "user_review_percent": 100,
        "venue_desc": "Sadler's Wells"
      },
      "quantity_options": {
        "valid_quantity_bitmask": 126
      },
      "venue_is_enforced": true
    }
  }
}
```

```python
pyticketswitch.Event(
    event_id='6IF',
    availability_details = [
        pyticketswitch.AvailabilityDetails(
            ticket_type='BALCONY',
            price_band='A',
            ticket_type_description='Balcony',
            price_band_description='',
            seatprice=47.0,
            surcharge=0.0,
            currency=pyticketswitch.Currency(
                code='gbp',
                number=826,
                factor=100,
                places=2,
                pre_symbol="",
                post_symbol="£",
            ),
        ),
        pyticketswitch.AvailabilityDetails(
            ticket_type='CIRCLE',
            price_band='A',
            ticket_type_description='Upper circle',
            price_band_description='',
            seatprice=35.0,
            surcharge=0.0,
            currency=pyticketswitch.Currency(
                code='gbp',
                number=826,
                factor=100,
                places=2,
                pre_symbol="",
                post_symbol="£",
            ),
        ),
        pyticketswitch.AvailabilityDetails(
            ticket_type='CIRCLE',
            price_band='B',
            ticket_type_description='Upper circle',
            price_band_description='',
            seatprice=30.0,
            surcharge=0.0,
            currency=pyticketswitch.Currency(
                code='gbp',
                number=826,
                factor=100,
                places=2,
                pre_symbol="",
                post_symbol="£",
            ),
        ),
        pyticketswitch.AvailabilityDetails(
            ticket_type='CIRCLE',
            price_band='C',
            ticket_type_description='Upper circle',
            price_band_description='',
            seatprice=25.0,
            surcharge=0.0,
            currency=pyticketswitch.Currency(
                code='gbp',
                number=826,
                factor=100,
                places=2,
                pre_symbol="",
                post_symbol="£",
            ),
        ),
        pyticketswitch.AvailabilityDetails(
            ticket_type='STALLS',
            price_band='A',
            ticket_type_description='Stalls',
            price_band_description='',
            seatprice=21.0,
            surcharge=0.0,
            currency=pyticketswitch.Currency(
                code='gbp',
                number=826,
                factor=100,
                places=2,
                pre_symbol="",
                post_symbol="£",
            ),
        ),
        pyticketswitch.AvailabilityDetails(
            ticket_type='STALLS',
            price_band='B',
            ticket_type_description='Stalls',
            price_band_description='',
            seatprice=18.0,
            surcharge=0.0,
            currency=pyticketswitch.Currency(
                code='gbp',
                number=826,
                factor=100,
                places=2,
                pre_symbol="",
                post_symbol="£",
            ),
        ),
    ],
)
```

## Availability details with performances

> **Example request**

```shell
curl https://api.ticketswitch.com/f13/events_by_id.v1/demo \
    -d "user_passwd=demopass" \
    -d "event_id_list=6IF" \
    -d "req_avail_details" \
    -d "req_avail_details_with_perfs" \
    -G
```

```python
from pyticketswitch import Client

client = Client(user='demo', password='demopass')
event = client.get_events(['6IF'], availability=True)
```

The `req_avail_details_with_perfs` extends the data returned with the
`req_avail_details` flag above, and  will add a performance summary to each
ticket type and price band combination.

This is not commonly used but one possible use case is to display a matrix of
availability and performance date to show users availability in one view.

<aside class="warning">`req_avail_details_with_perfs` is only valid when used in
combination with `req_avail_details`</aside>

> **Example response**

```shell
{
  "events_by_id": {
    "6IF": {
      "event": {
        "avail_details": {
          "ticket_type": [
            {
              "price_band": [
                {
                  "avail_detail": [
                    {
                      "avail_currency": {
                        "currency_code": "gbp"
                      },
                      "available_dates": {
                        "first_yyyymmdd": "20161221",
                        "last_yyyymmdd": "20170419",
                        "year_2016": {
                          "dec_bitmask": 1064304640
                        },
                        "year_2017": {
                          "apr_bitmask": 507774,
                          "feb_bitmask": 251526135,
                          "jan_bitmask": 2012209087,
                          "mar_bitmask": 2130574327
                        }
                      },
                      "available_weekdays_bitmask": 63,
                      "quantity_options": {
                        "valid_quantity_bitmask": 126
                      },
                      "seatprice": 47,
                      "surcharge": 0
                    }
                  ],
                  "price_band_code": "A",
                  "price_band_desc": ""
                }
              ],
              "ticket_type_code": "BALCONY",
              "ticket_type_desc": "Balcony"
            },
            {
              "price_band": [
                {
                  "avail_detail": [
                    {
                      "avail_currency": {
                        "currency_code": "gbp"
                      },
                      "available_dates": {
                        "first_yyyymmdd": "20161221",
                        "last_yyyymmdd": "20170419",
                        "year_2016": {
                          "dec_bitmask": 1064304640
                        },
                        "year_2017": {
                          "apr_bitmask": 507774,
                          "feb_bitmask": 251526135,
                          "jan_bitmask": 2012209087,
                          "mar_bitmask": 2130574327
                        }
                      },
                      "available_weekdays_bitmask": 63,
                      "quantity_options": {
                        "valid_quantity_bitmask": 126
                      },
                      "seatprice": 35,
                      "surcharge": 0
                    }
                  ],
                  "price_band_code": "A",
                  "price_band_desc": ""
                },
                {
                  "avail_detail": [
                    {
                      "avail_currency": {
                        "currency_code": "gbp"
                      },
                      "available_dates": {
                        "first_yyyymmdd": "20161221",
                        "last_yyyymmdd": "20170419",
                        "year_2016": {
                          "dec_bitmask": 1064304640
                        },
                        "year_2017": {
                          "apr_bitmask": 507774,
                          "feb_bitmask": 251526135,
                          "jan_bitmask": 2012209087,
                          "mar_bitmask": 2130574327
                        }
                      },
                      "available_weekdays_bitmask": 63,
                      "quantity_options": {
                        "valid_quantity_bitmask": 126
                      },
                      "seatprice": 30,
                      "surcharge": 0
                    }
                  ],
                  "price_band_code": "B",
                  "price_band_desc": ""
                },
                {
                  "avail_detail": [
                    {
                      "avail_currency": {
                        "currency_code": "gbp"
                      },
                      "available_dates": {
                        "first_yyyymmdd": "20161221",
                        "last_yyyymmdd": "20170419",
                        "year_2016": {
                          "dec_bitmask": 1064304640
                        },
                        "year_2017": {
                          "apr_bitmask": 507774,
                          "feb_bitmask": 251526135,
                          "jan_bitmask": 2012209087,
                          "mar_bitmask": 2130574327
                        }
                      },
                      "available_weekdays_bitmask": 63,
                      "quantity_options": {
                        "valid_quantity_bitmask": 126
                      },
                      "seatprice": 25,
                      "surcharge": 0
                    }
                  ],
                  "price_band_code": "C",
                  "price_band_desc": ""
                }
              ],
              "ticket_type_code": "CIRCLE",
              "ticket_type_desc": "Upper circle"
            },
            {
              "price_band": [
                {
                  "avail_detail": [
                    {
                      "avail_currency": {
                        "currency_code": "gbp"
                      },
                      "available_dates": {
                        "first_yyyymmdd": "20161221",
                        "last_yyyymmdd": "20170419",
                        "year_2016": {
                          "dec_bitmask": 1064304640
                        },
                        "year_2017": {
                          "apr_bitmask": 507774,
                          "feb_bitmask": 251526135,
                          "jan_bitmask": 2012209087,
                          "mar_bitmask": 2130574327
                        }
                      },
                      "available_weekdays_bitmask": 63,
                      "quantity_options": {
                        "valid_quantity_bitmask": 126
                      },
                      "seatprice": 21,
                      "surcharge": 0
                    }
                  ],
                  "price_band_code": "A",
                  "price_band_desc": ""
                },
                {
                  "avail_detail": [
                    {
                      "avail_currency": {
                        "currency_code": "gbp"
                      },
                      "available_dates": {
                        "first_yyyymmdd": "20161221",
                        "last_yyyymmdd": "20170419",
                        "year_2016": {
                          "dec_bitmask": 1064304640
                        },
                        "year_2017": {
                          "apr_bitmask": 507774,
                          "feb_bitmask": 251526135,
                          "jan_bitmask": 2012209087,
                          "mar_bitmask": 2130574327
                        }
                      },
                      "available_weekdays_bitmask": 63,
                      "quantity_options": {
                        "valid_quantity_bitmask": 14
                      },
                      "seatprice": 18,
                      "surcharge": 0
                    }
                  ],
                  "price_band_code": "B",
                  "price_band_desc": ""
                }
              ],
              "ticket_type_code": "STALLS",
              "ticket_type_desc": "Stalls"
            }
          ]
        },
        "city_desc": "London",
        "class": [
          {
            "class_desc": "Ballet & Dance"
          }
        ],
        "country_code": "uk",
        "country_desc": "United Kingdom",
        "critic_review_percent": 100,
        "custom_filter": [],
        "event_desc": "Matthew Bourne's Nutcracker TEST",
        "event_id": "6IF",
        "event_path": "/6IF-matthew-bourne-s-nutcracker-test/",
        "event_status": "live",
        "event_type": "simple_ticket",
        "event_upsell_list": {
          "event_id": [
            "6IE",
            "MH0"
          ]
        },
        "geo_data": {
          "latitude": 51.52961137,
          "longitude": -0.10601562
        },
        "has_no_perfs": false,
        "is_seated": true,
        "max_running_time": 120,
        "min_running_time": 120,
        "need_departure_date": false,
        "need_duration": false,
        "need_performance": true,
        "postcode": "EC1R 4TN",
        "show_perf_time": true,
        "source_code": "ext_test0",
        "source_desc": "External Test Backend 0",
        "user_review_percent": 100,
        "venue_desc": "Sadler's Wells"
      },
      "quantity_options": {
        "valid_quantity_bitmask": 126
      },
      "venue_is_enforced": true
    }
  }
}

```

```python
pyticketswitch.Event(
    event_id='6IF',
    availability_details = [
        pyticketswitch.AvailabilityDetails(
            ticket_type='BALCONY',
            price_band='A',
            ticket_type_description='Balcony',
            price_band_description='',
            seatprice=47.0,
            surcharge=0.0,
            currency=pyticketswitch.Currency(
                code='gbp',
                number=826,
                factor=100,
                places=2,
                pre_symbol="",
                post_symbol="£",
            ),
            first_date=datetime.date(2016, 11, 29),
            last_date=datetime.data(2017, 3, 27),
            calendar_masks={
                2016: {11: 805306368, 12: 1065289163},
                2017: {1: 2012209087, 2: 251526135, 3: 117308407},
            },
            weekday_mask=63,
            valid_quanities=[2, 3, 4, 5, 6, 7]
        ),
        pyticketswitch.AvailabilityDetails(
            ticket_type='CIRCLE',
            price_band='A',
            ticket_type_description='Upper circle',
            price_band_description='',
            seatprice=35.0,
            surcharge=0.0,
            currency=pyticketswitch.Currency(
                code='gbp',
                number=826,
                factor=100,
                places=2,
                pre_symbol="",
                post_symbol="£",
            ),
            first_date=datetime.date(2016, 11, 29),
            last_date=datetime.data(2017, 3, 27),
            calendar_masks={
                2016: {11: 805306368, 12: 1065289163},
                2017: {1: 2012209087, 2: 251526135, 3: 117308407},
            },
            weekday_mask=63,
            valid_quanities=[2, 3, 4, 5, 6, 7]
        ),
        pyticketswitch.AvailabilityDetails(
            ticket_type='CIRCLE',
            price_band='B',
            ticket_type_description='Upper circle',
            price_band_description='',
            seatprice=30.0,
            surcharge=0.0,
            currency=pyticketswitch.Currency(
                code='gbp',
                number=826,
                factor=100,
                places=2,
                pre_symbol="",
                post_symbol="£",
            ),
            first_date=datetime.date(2016, 11, 29),
            last_date=datetime.data(2017, 3, 27),
            calendar_masks={
                2016: {11: 805306368, 12: 1065289163},
                2017: {1: 2012209087, 2: 251526135, 3: 117308407},
            },
            weekday_mask=63,
            valid_quanities=[2, 3, 4, 5, 6, 7]
        ),
        pyticketswitch.AvailabilityDetails(
            ticket_type='CIRCLE',
            price_band='C',
            ticket_type_description='Upper circle',
            price_band_description='',
            seatprice=25.0,
            surcharge=0.0,
            currency=pyticketswitch.Currency(
                code='gbp',
                number=826,
                factor=100,
                places=2,
                pre_symbol="",
                post_symbol="£",
            ),
            first_date=datetime.date(2016, 11, 29),
            last_date=datetime.data(2017, 3, 27),
            calendar_masks={
                2016: {11: 805306368, 12: 1065289163},
                2017: {1: 2012209087, 2: 251526135, 3: 117308407},
            },
            weekday_mask=63,
            valid_quanities=[2, 3, 4, 5, 6, 7]
        ),
        pyticketswitch.AvailabilityDetails(
            ticket_type='STALLS',
            price_band='A',
            ticket_type_description='Stalls',
            price_band_description='',
            seatprice=21.0,
            surcharge=0.0,
            currency=pyticketswitch.Currency(
                code='gbp',
                number=826,
                factor=100,
                places=2,
                pre_symbol="",
                post_symbol="£",
            ),
            first_date=datetime.date(2016, 11, 29),
            last_date=datetime.data(2017, 3, 27),
            calendar_masks={
                2016: {11: 805306368, 12: 1065289163},
                2017: {1: 2012209087, 2: 251526135, 3: 117308407},
            },
            weekday_mask=63,
            valid_quanities=[2, 3, 4, 5, 6, 7]
        ),
        pyticketswitch.AvailabilityDetails(
            ticket_type='STALLS',
            price_band='B',
            ticket_type_description='Stalls',
            price_band_description='',
            seatprice=18.0,
            surcharge=0.0,
            currency=pyticketswitch.Currency(
                code='gbp',
                number=826,
                factor=100,
                places=2,
                pre_symbol="",
                post_symbol="£",
            ),
            first_date=datetime.date(2016, 11, 29),
            last_date=datetime.data(2017, 3, 27),
            calendar_masks={
                2016: {11: 805306368, 12: 1065289163},
                2017: {1: 2012209087, 2: 251526135, 3: 117308407},
            },
            weekday_mask=63,
            valid_quanities=[2, 3, 4, 5, 6, 7]
        ),
    ],
)
```
