# Upsell

Some events have other events or items associated with them for the puroposes of
increasing profitability. These items can be retrieved from the
[Events by ID](#events-by-id) call by using optional flags, but add-on items can
only be added to a trolley already containing seats for an event that lists
the add-on event as an add-on.

Ingresso provides two API endpoints that accept standard [trolley](#trolley)
parameters and return a list of add-on and upsell events, as documented below.

## Add-ons

> **Definition**

```
GET https://demo.ticketswitch.com/f13/add_ons.v1
```

This resource returns detail for which add-on events may be added to a given
trolley. The trolley token must be specified, or the parameters required to
build a valid trolley must be specified, or both. This call is nullipotent, and
will not change the state of any existing trolley.

### Request

> **Example request**

```shell
curl https://demo.ticketswitch.com/f13/add_ons.v1 \
    -u "demo:demopass" \
    -d "trolley_token=M2--6TcZRZsfgZI0E8PmlGWqr5PQluhxE46Nqnno3Xa24lFuIKM_0IYJq40BDvisBRcXKsaGBdLfu8B5TfwI4s7jk7y7xRto_SwJL177u_vuvvq_Ynqnj64IfAHmItXxz_-xdTQGXh_G-BuPGxPXNY_Ycimiv1ZYlPFuPjsoVugnUnyu2F0TmpdztEtgI9PC1WhnTeHeRUDEBvVewOIf_PEJkPVR-UXFDg3gxbHYQBENW5XpkYnRhc22IqrB7eAoK0MbTLaPMk3uB1BKnz_B7fasSU_NU3nhXvmgHa4suvCT9oEaQSGvq4G0glOtyCiqL1FfVJ2R3KIgb2xC4NbAm4cNL5q22V_W0eYGmJ9DTBJ90UWvAWH7UeFLM1--Z" \
    --compressed \
    -G
```

```python
from pyticketswitch import Client

client = Client(user='demo', password='demopass')
addon_events, addon_meta = client.get_addons(token="M2--6TcZRZsfgZI0E8PmlGWqr5PQluhxE46Nqnno3Xa24lFuIKM_0IYJq40BDvisBRcXKsaGBdLfu8B5TfwI4s7jk7y7xRto_SwJL177u_vuvvq_Ynqnj64IfAHmItXxz_-xdTQGXh_G-BuPGxPXNY_Ycimiv1ZYlPFuPjsoVugnUnyu2F0TmpdztEtgI9PC1WhnTeHeRUDEBvVewOIf_PEJkPVR-UXFDg3gxbHYQBENW5XpkYnRhc22IqrB7eAoK0MbTLaPMk3uB1BKnz_B7fasSU_NU3nhXvmgHa4suvCT9oEaQSGvq4G0glOtyCiqL1FfVJ2R3KIgb2xC4NbAm4cNL5q22V_W0eYGmJ9DTBJ90UWvAWH7UeFLM1--Z")
```

Either the `trolley_token` parameter, and/or a valid set of parameters for a
[trolley](#trolley) call must be supplied.

Parameter | Description
--------- | -----------
`trolley_token` | A valid identifier for an existing trolley.
--------- | -----------
`discX` | A trolley with a discount code for added seats.
`no_of_seats` | A trolley with a specified number of seats added.
`perf_id` | A trolley with seats for the specified performance added.
`price_band_code` | A trolley with seats from the specified price band added.
`remove_items_list` | A trolley with the specified `item_numbers` removed.
`seatX` | A trolley with the specified seat ID added as one of the seats.
`X_send_code` | A trolley with the specified send method for new tickets added.
`ticket_type_code` | A trolley with seats of the specified ticket type added.

The list of optional [additional parameters](#additional-parameters) specified
can also be included and will add the details to the response.


### Response

> **Example response**

```shell
{
  "results": {
    "event": [
      {
        "city_code": "london-uk",
        "city_desc": "London",
        "classes": {
          "theatre": "Theatre"
        },
        "country_code": "uk",
        "country_desc": "United Kingdom",
        "custom_filter": [],
        "event_desc": "Unremarkable Cat Lunchbox",
        "event_id": "7AC",
        "event_path": "/7AC-unremarkable-cat-lunchbox/",
        "event_status": "live",
        "event_type": "misc_item",
        "event_uri_desc": "Unremarkable-Cat-Lunchbox",
        "geo_data": {
          "latitude": 51.49306,
          "longitude": -0.22639
        },
        "has_no_perfs": false,
        "is_add_on": true,
        "is_seated": false,
        "need_departure_date": false,
        "need_duration": false,
        "need_performance": false,
        "postcode": "W6 7ES",
        "show_perf_time": false,
        "source_code": "ext_test1",
        "source_desc": "External Test Backend 1",
        "venue_desc": "Lyric Apollo",
        "venue_uri_desc": "Lyric-Apollo"
      }
    ],
    "paging_status": {
      "page_length": 50,
      "page_number": 0,
      "pages_remaining": 0,
      "results_remaining": 0,
      "total_unpaged_results": 1
    }
  }
}
```

```python
from pyticketswitch.event import Event

[
    Event(
        min_running_time=None,
        event_type=u'misc_item',
        has_performances=True,
        venue_info_html=None,
        critic_review_percent=None,
        needs_departure_date=False,
        valid_quantities=None,
        addon_events=None,
        availability_details=[],
        postcode=u'W6 7ES',
        country_code=u'uk',
        id=u'7AC',
        city=u'London',
        filters=[],
        media={},
        is_seated=False,
        upsell_events=None,
        event_info=None,
        content={},
        source=u'External Test Backend 1',
        max_running_time=None,
        cost_range_details=[],
        needs_performance=False,
        latitude=51.49306,
        upsell_list=[],
        city_code=u'london-uk',
        venue_addr=None,
        status=u'live',
        description=u'Unremarkable Cat Lunchbox',
        venue_addr_html=None,
        cost_range=None,
        event_info_html=None,
        source_code=u'ext_test1',
        country=u'United Kingdom',
        venue_info=None,
        venue=u'Lyric Apollo',
        longitude=-0.22639,
        is_add_on=True,
        reviews=[],
        classes={
            u'theatre': u'Theatre'
        },
        fields={},
        needs_duration=False,
        no_singles_cost_range=None,
        component_events=[],
        show_performance_time=False,
    )
]
```

The format of the response is identical to that of the [events list](#events-list).

Attribute | Description
--------- | -----------
`city_code` | Code of the city where the event is taking place.
`city_desc` | Name of the city where the event is taking place.
`classes` | A collection of categories (a.k.a. "classes") that this event belongs to. The index is the class code, the value is the class description.
`country_code` | 2-digit country code (using [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2))
`country_desc` | Name of the country where the event is taking place.
`critic_review_percent` | The aggregate critic review score, e.g. `80` for 80%.
`custom_filter` | Array of custom filter codes. This can be ignored by partners.
`event_desc` | Name of the show or event e.g. `The Lion King`.
`event_id` | Unique identifier for the event.
`event_path` | If you also use a white label website this can be used to navigate to the event page, e.g. `/2J5V-la-pedrera-skip-the-line/`.
`event_status` | Will be `live` for a normal event search. Other values are `dead` and `pending` but these will only be displayed when using the `include_dead` or `include_non_live` parameters.
`event_type` | Currently all events are of type `simple_ticket`. In future we may add `hotel_room` and `misc_item`.
`event_upsell_list` | A list of event IDs that can be presented as an upsell option to customers (not always present).
`geo_data` | A block containing the following geo co-ordinates:
`geo_data.latitude` | Latitude of the event.
`geo_data.longitude` | Longitude of the event.
`has_no_perfs` | `true` if the event has no performances. For example some attraction tickets are valid for any date, so we do not present a list of performances to select.
`is_add_on` | `true` if this event is an add-on event. This means it can only be added to a trolley containing tickets for an event that lists this in its `add_ons`.
`is_seated` | `true` for seated events.
`max_running_time` | Maximum length / duration in minutes (not always present).
`min_running_time` | Minimum length / duration in minutes (not always present).
`need_departure_date` | Flag indicating whether the event needs a departure date specified. This is `false` for most events. Most partners can ignore this.
`need_duration` | Flag indicating whether the event needs duration (specific to `hotel_room` events only). Most partners can ignore this.
`need_performance` | Flag indicating if a performance must be selected in order to retrieve availability. For the vast majority of events this will be `true`.
`postcode` | Postcode of the event location.
`show_perf_time` | `false` if the performance time is not relevant, for example some events use a performance description rather than specific times.
`source_code` | Source supplier code e.g. `nimax`.
`source_desc` | Source supplier description e.g. `Nimax`.
`user_review_percent` | The aggregate user review score, e.g. `80` for 80%.
`venue_desc` | Name of the venue e.g. `Sadler's Wells`.
`venue_uri_desc` | URI encoded name of the venue e.g. `Sadler%27s-Wells`.



The outer object also contains a **paging_status** object:

Attribute | Description
--------- | -----------
`page_length` | The number of results per page.
`page_number` | The zero-based page number currently displayed.
`pages_remaining` | The number of pages that you need to request after the current page to retrieve all results.
`results_remaining` | The number of results in the remaining pages.
`total_unpaged_results` | The total number of results.


## Related Events
This resource returns detail for related events that can be added to a given
trolley. Unlike add-ons, upsell events do not require a parent event to be in
a trolley before being added, and are supplied as a way of offering other
options to customers that they may not be aware of (such as show-and-meal deals,
etc.).

> **Definition**

```
GET https://demo.ticketswitch.com/f13/upsells.v1
```

A trolley token or combination of parameters for a trolley call must be specified.
This call is nullipotent and does not change the state of any existing trolley.

### Request

> **Example request**

```shell
curl https://demo.ticketswitch.com/f13/add_ons.v1 \
    -u "demo:demopass" \
    -d "trolley_token=M2--6TcZRZsfgZI0E8PmlGWqr5PQluhxE46Nqnno3Xa24lFuIKM_0IYJq40BDvisBRcXKsaGBdLfu8B5TfwI4s7jk7y7xRto_SwJL177u_vuvvq_Ynqnj64IfAHmItXxz_-xdTQGXh_G-BuPGxPXNY_Ycimiv1ZYlPFuPjsoVugnUnyu2F0TmpdztEtgI9PC1WhnTeHeRUDEBvVewOIf_PEJkPVR-UXFDg3gxbHYQBENW5XpkYnRhc22IqrB7eAoK0MbTLaPMk3uB1BKnz_B7fasSU_NU3nhXvmgHa4suvCT9oEaQSGvq4G0glOtyCiqL1FfVJ2R3KIgb2xC4NbAm4cNL5q22V_W0eYGmJ9DTBJ90UWvAWH7UeFLM1--Z" \
    --compressed \
    -G
```

```python
from pyticketswitch import Client

client = Client(user='demo', password='demopass')
addon_events, addon_meta = client.get_upsells(token="M2--6TcZRZsfgZI0E8PmlGWqr5PQluhxE46Nqnno3Xa24lFuIKM_0IYJq40BDvisBRcXKsaGBdLfu8B5TfwI4s7jk7y7xRto_SwJL177u_vuvvq_Ynqnj64IfAHmItXxz_-xdTQGXh_G-BuPGxPXNY_Ycimiv1ZYlPFuPjsoVugnUnyu2F0TmpdztEtgI9PC1WhnTeHeRUDEBvVewOIf_PEJkPVR-UXFDg3gxbHYQBENW5XpkYnRhc22IqrB7eAoK0MbTLaPMk3uB1BKnz_B7fasSU_NU3nhXvmgHa4suvCT9oEaQSGvq4G0glOtyCiqL1FfVJ2R3KIgb2xC4NbAm4cNL5q22V_W0eYGmJ9DTBJ90UWvAWH7UeFLM1--Z")
```

Either the `trolley_token` parameter, and/or a valid set of parameters for a
[trolley](#trolley) call must be supplied.

Parameter | Description
--------- | -----------
`trolley_token` | A valid identifier for an existing trolley.
--------- | -----------
`discX` | A trolley with a discount code for added seats.
`no_of_seats` | A trolley with a specified number of seats added.
`perf_id` | A trolley with seats for the specified performance added.
`price_band_code` | A trolley with seats from the specified price band added.
`remove_items_list` | A trolley with the specified `item_numbers` removed.
`seatX` | A trolley with the specified seat ID added as one of the seats.
`X_send_code` | A trolley with the specified send method for new tickets added.
`ticket_type_code` | A trolley with seats of the specified ticket type added.

The list of optional [additional parameters](#additional-parameters) specified
can also be included and will add the details to the response.


### Response

> **Example response**

```shell
{
  "results": {
    "event": [
      {
        "city_code": "london-uk",
        "city_desc": "London",
        "classes": {
          "dance": "Ballet & Dance"
        },
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
            "6IE"
          ]
        },
        "event_uri_desc": "Matthew-Bourne%27s-Nutcracker-TEST",
        "geo_data": {
          "latitude": 51.52961137,
          "longitude": -0.10601562
        },
        "has_no_perfs": false,
        "is_add_on": false,
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
        "venue_desc": "Sadler's Wells",
        "venue_uri_desc": "Sadler%27s-Wells"
      },
      {
        "city_code": "london-uk",
        "city_desc": "London",
        "classes": {
          "opera": "Opera"
        },
        "country_code": "uk",
        "country_desc": "United Kingdom",
        "custom_filter": [],
        "event_desc": "Toy Story - The Opera",
        "event_id": "7AA",
        "event_path": "/7AA-toy-story-the-opera/",
        "event_status": "live",
        "event_type": "simple_ticket",
        "event_uri_desc": "Toy-Story-The-Opera",
        "geo_data": {
          "latitude": 51.511556,
          "longitude": -0.11975
        },
        "has_no_perfs": false,
        "is_add_on": false,
        "is_seated": true,
        "max_running_time": 90,
        "min_running_time": 90,
        "need_departure_date": false,
        "need_duration": false,
        "need_performance": true,
        "postcode": "WC2E 7RQ",
        "show_perf_time": true,
        "source_code": "ext_test0",
        "source_desc": "External Test Backend 0",
        "venue_desc": "Lyceum Theatre",
        "venue_uri_desc": "Lyceum-Theatre"
      }
    ],
    "paging_status": {
      "page_length": 50,
      "page_number": 0,
      "pages_remaining": 0,
      "results_remaining": 0,
      "total_unpaged_results": 2
    }
  }
}
```

```python
from pyticketswitch.event import Event

[
    Event(
        min_running_time=120,
        event_type=u'simple_ticket',
        has_performances=True,
        venue_info_html=None,
        critic_review_percent=100,
        needs_departure_date=False,
        valid_quantities=None,
        addon_events=None,
        availability_details=[],
        postcode=u'EC1R 4TN',
        country_code=u'uk',
        id=u'6IF',
        city=u'London',
        filters=[],
        media={},
        is_seated=True,
        upsell_events=None,
        event_info=None,
        content={},
        source=u'External Test Backend 0',
        max_running_time=120,
        cost_range_details=[],
        needs_performance=False,
        latitude=51.52961137,
        upsell_list=[
            u'6IE'
        ],
        city_code=u'london-uk',
        venue_addr=None,
        status=u'live',
        description=u"Matthew Bourne's Nutcracker TEST",
        venue_addr_html=None,
        cost_range=None,
        event_info_html=None,
        source_code=u'ext_test0',
        country=u'United Kingdom',
        venue_info=None,
        venue=u"Sadler's Wells",
        longitude=-0.10601562,
        is_add_on=False,
        reviews=[],
        classes={
            u'dance': u'Ballet & Dance'
        },
        fields={},
        needs_duration=False,
        no_singles_cost_range=None,
        component_events=[],
        show_performance_time=True,
    ),
    Event(
        min_running_time=90,
        event_type=u'simple_ticket',
        has_performances=True,
        venue_info_html=None,
        critic_review_percent=None,
        needs_departure_date=False,
        valid_quantities=None,
        addon_events=None,
        availability_details=[],
        postcode=u'WC2E 7RQ',
        country_code=u'uk',
        id=u'7AA',
        city=u'London',
        filters=[],
        media={},
        is_seated=True,
        upsell_events=None,
        event_info=None,
        content={},
        source=u'External Test Backend 0',
        max_running_time=90,
        cost_range_details=[],
        needs_performance=False,
        latitude=51.511556,
        upsell_list=[],
        city_code=u'london-uk',
        venue_addr=None,
        status=u'live',
        description=u'Toy Story - The Opera',
        venue_addr_html=None,
        cost_range=None,
        event_info_html=None,
        source_code=u'ext_test0',
        country=u'United Kingdom',
        venue_info=None,
        venue=u'Lyceum Theatre',
        longitude=-0.11975,
        is_add_on=False,
        reviews=[],
        classes={
            u'opera': u'Opera'
        },
        fields={},
        needs_duration=False,
        no_singles_cost_range=None,
        component_events=[],
        show_performance_time=True,
    )
]
```

The format of the response is identical to that of the [events list](#events-list).

Attribute | Description
--------- | -----------
`city_code` | Code of the city where the event is taking place.
`city_desc` | Name of the city where the event is taking place.
`classes` | A collection of categories (a.k.a. "classes") that this event belongs to. The index is the class code, the value is the class description.
`country_code` | 2-digit country code (using [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2))
`country_desc` | Name of the country where the event is taking place.
`critic_review_percent` | The aggregate critic review score, e.g. `80` for 80%.
`custom_filter` | Array of custom filter codes. This can be ignored by partners.
`event_desc` | Name of the show or event e.g. `The Lion King`.
`event_id` | Unique identifier for the event.
`event_path` | If you also use a white label website this can be used to navigate to the event page, e.g. `/2J5V-la-pedrera-skip-the-line/`.
`event_status` | Will be `live` for a normal event search. Other values are `dead` and `pending` but these will only be displayed when using the `include_dead` or `include_non_live` parameters.
`event_type` | Currently all events are of type `simple_ticket`. In future we may add `hotel_room` and `misc_item`.
`event_upsell_list` | A list of event IDs that can be presented as an upsell option to customers (not always present).
`geo_data` | A block containing the following geo co-ordinates:
`geo_data.latitude` | Latitude of the event.
`geo_data.longitude` | Longitude of the event.
`has_no_perfs` | `true` if the event has no performances. For example some attraction tickets are valid for any date, so we do not present a list of performances to select.
`is_add_on` | `true` if this event is an add-on event. This means it can only be added to a trolley containing tickets for an event that lists this in its `add_ons`.
`is_seated` | `true` for seated events.
`max_running_time` | Maximum length / duration in minutes (not always present).
`min_running_time` | Minimum length / duration in minutes (not always present).
`need_departure_date` | Flag indicating whether the event needs a departure date specified. This is `false` for most events. Most partners can ignore this.
`need_duration` | Flag indicating whether the event needs duration (specific to `hotel_room` events only). Most partners can ignore this.
`need_performance` | Flag indicating if a performance must be selected in order to retrieve availability. For the vast majority of events this will be `true`.
`postcode` | Postcode of the event location.
`show_perf_time` | `false` if the performance time is not relevant, for example some events use a performance description rather than specific times.
`source_code` | Source supplier code e.g. `nimax`.
`source_desc` | Source supplier description e.g. `Nimax`.
`user_review_percent` | The aggregate user review score, e.g. `80` for 80%.
`venue_desc` | Name of the venue e.g. `Sadler's Wells`.
`venue_uri_desc` | URI encoded name of the venue e.g. `Sadler%27s-Wells`.



The outer object also contains a **paging_status** object:

Attribute | Description
--------- | -----------
`page_length` | The number of results per page.
`page_number` | The zero-based page number currently displayed.
`pages_remaining` | The number of pages that you need to request after the current page to retrieve all results.
`results_remaining` | The number of results in the remaining pages.
`total_unpaged_results` | The total number of results.
