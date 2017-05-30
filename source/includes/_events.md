# Events

An event describes a product within the Ingresso system such as a theatre show,
a theme park, or a sporting event. The event will also include information about
the venue and its geographic location. An event will normally have one or more
[performances](#performances). An event without available performances will be
considered `dead` and won't be turn up in searches unless explicitly requested.

Each event has a unique `event_id` that identifies the event to other parts of
the API.

There are two event-related resources:

1. [Events list](#events-list): list all events or search for events.

2. [Events by ID](#events-by-id): return detail for one or more events by 
their ID.

3. [Related events](#related-events): list related and addon events.

These three resources are described below, followed by detail of the 
[additional parameters](#additional-parameters) that can be passed to each.


## Events list

This resource lists events. It accepts a number of search parameters to
filter the list returned. The list is paged to avoid large volumes of data being
accidentally returned.

<aside class="notice">Searching is not case sensitive.</aside>

> **Definition**

```
GET https://demo.ticketswitch.com/f13/events.v1
```

### Request

> **Example request**

```shell
curl https://demo.ticketswitch.com/f13/events.v1 \
    -u "demo:demopass" \
    -d "keywords=matthew" \
    -d "country_code=uk" \
    --compressed \
    -G
```

```python
from pyticketswitch import Client

client = Client(user='demo', password='demopass')
events, meta = client.list_events(keywords=['matthew'], country_code='uk')
```

These are the available search / filter parameters:

Parameter | Description
--------- | -----------
`keywords` | Space separated list of search keywords, e.g. `lion king new york` or `paris tours`.
`date_range` | Date range in the form `yyyymmdd:yyyymmdd` (both are optional) to filter events to those with performances within the date range.
`airport_code` | Return events near an airport (specified using an [IATA airport code](https://en.wikipedia.org/wiki/International_Air_Transport_Association_airport_code))
`circle` | Return events within in a circular geographical area. Three colon-separated values are needed for **latitude**, **longitude**, and **radius in kilometres**.  Example: `51.52961137:-0.10601562:10`.
`city_code` | Return events in a particular city. The list of city codes can be retrieved using the [cities](#cities) resource.
`country_code` | 2-digit country code (using [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2)).
`include_dead` | Include dead events in the results. This could be useful if you dynamically retrieve the list of events from Ingresso and want to continue to display an event page after an event dies, for example to help with search engine optimisation.
`offers_only` | If set to `true` the response will only include events with a special offer. Note that we rely on cached data, so we cannot guarantee complete accuracy.

These parameters are used to control the output if more than one event is returned:

Parameter | Description
--------- | -----------
`page_length` | Length of a page, default 50.
`page_number` | Zero-indexed page number, default 0. Should be used in conjunction with the `page_length` parameter.
`sort_order` | Valid values are `most_popular` (based on sales across all partners over the last 48 hours), `alphabetic`, `cost_ascending` (lowest price first, based on the minimum total price [seatprice + surcharge] for the event), `cost_descending` (highest price first, based on the *maximum* total price for the event), `critic_rating` (average critic rating, highest to lowest), `recent`, `last_sale`. When there is a tie alphabetic ordering is used to break the tie. **Note: there is a slight performance impact to using `cost_ascending` or `cost_descending` if you are not also using the `req_cost_range` parameter.**

These parameters can be passed in to request additional data for each event, and 
are described in more detail in the 
[additional parameters](#additional-parameters) section below:

Parameter | Description
--------- | -----------
`req_avail_details` | Returns [availability details](#availability-details) - a cached list of unique ticket types and price bands available for this event across all performances. **This parameter is not commonly used.**
`req_avail_details_with_perfs` | This will add the list of available performance dates to each avail detail object. *Only valid if used alongside req_avail_details*.
`req_cost_range` | Returns [cost ranges](#cost-range) - a from price and offer detail for each event. *Most partners include this parameter.*
`req_cost_range_best_value_offer` | Returns the offer with the highest percentage saving. *This is the most commonly used offer cost range.*
`req_cost_range_details` | Returns a list of unique ticket types and price bands and their cost ranges across all performances. **This parameter is not commonly used.**
`req_cost_range_max_saving_offer` | Returns the offer with the highest absolute saving.
`req_cost_range_min_cost_offer` | Returns the offer with the lowest cost.
`req_cost_range_top_price_offer` | Returns the offer with the highest cost. This is the least used offer cost range.
`req_cost_range_no_singles_data` | This returns another cost range object that excludes availability with only 1 consecutive seat available. The prices in this cost range will therefore be the same or higher than the outer cost range. It has the same structure as the main cost range (so if you want to see the "best value offer" in the no singles data, you need to add `req_cost_range_best_value_offer` and you will see this data in both cost ranges).
`req_extra_info` | Returns the [descriptive info](#extra-info) for the event, returned as individual sections (`structured_info`) or as a single summary (`event_info` / `event_info_html`).
`req_media_triplet_one` | Triplet one (jpg/png 520x390). [See further detail on media](#media).
`req_media_triplet_two` | Triplet two if available (jpg/png 520x390).
`req_media_triplet_three` | Triplet three if available (jpg/png 520x390).
`req_media_triplet_four` | Triplet four if available (jpg/png 520x390).
`req_media_triplet_five` | Triplet five if available (jpg/png 520x390).
`req_media_seating_plan` | Graphical seating plan of the venue if available (jpg/png varying size).
`req_media_square` | Small square image suitable for search or event avatar (jpg/png 140x140).
`req_media_landscape` | Small landscape banner suitable for search (jpg/png 220x115).
`req_media_marquee` | Large landscape banner suitable for a page heading, if available (jpg/png 700x300).
`req_media_supplier` | Logo of the supplier/producer, if available (jpg/png varying size).
`req_meta_components` | Returns a list of meta component events. This will only add data if the event is a "meta event" such as a touring show that is comprised of individual component events.
`req_reviews` | Returns [event reviews](#reviews) if available.
`req_video_iframe` | Returns video iframe information if available.


### Response

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
            "6IE",
            "MH0"
          ]
        },
        "event_uri_desc": "Matthew-Bourne%27s-Nutcracker-TEST",
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
        "venue_desc": "Sadler's Wells",
        "venue_uri_desc": "Sadler%27s-Wells"
      },
      {
        "city_code": "london-uk",
        "city_desc": "London",
        "classes": {
          "dance": "Ballet & Dance"
        },
        "country_code": "uk",
        "country_desc": "United Kingdom",
        "custom_filter": [],
        "event_desc": "Matthew Bourne's Swan Lake test",
        "event_id": "6IE",
        "event_path": "/6IE-matthew-bourne-s-swan-lake-test/",
        "event_status": "live",
        "event_type": "simple_ticket",
        "event_upsell_list": {
          "event_id": [
            "6IF",
            "6KU",
            "MH0"
          ]
        },
        "event_uri_desc": "Matthew-Bourne%27s-Swan-Lake-test",
        "geo_data": {
          "latitude": 51.52961137,
          "longitude": -0.10601562
        },
        "has_no_perfs": false,
        "is_seated": true,
        "max_running_time": 120,
        "min_running_time": 90,
        "need_departure_date": false,
        "need_duration": false,
        "need_performance": true,
        "postcode": "EC1R 4TN",
        "show_perf_time": true,
        "source_code": "ext_test0",
        "source_desc": "External Test Backend 0",
        "venue_desc": "Sadler's Wells",
        "venue_uri_desc": "Sadler%27s-Wells"
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
        id='6IF',
        status='live',
        description="Matthew Bourne's Nutcracker TEST",
        source='External Test Backend 0',
        source_code='ext_test0',
        event_type='simple_ticket',
        venue="Sadler's Wells",
        classes={
            'dance': 'Ballet & Dance'
        },
        postcode='EC1R 4TN',
        city='London',
        city_code='london-uk',
        country='United Kingdom',
        country_code='uk',
        latitude=51.52961137,
        longitude=-0.10601562,
        max_running_time=120,
        min_running_time=120,
        show_performance_time=True,
        has_performances=True,
        is_seated=True,
        needs_departure_date=False,
        needs_duration=False,
        needs_performance=False,
        upsell_list=[
            '6IE',
            'MH0'
        ],
        critic_review_percent=100,
    ),
    Event(
        id='6IE',
        status='live',
        description="Matthew Bourne's Swan Lake test",
        source='External Test Backend 0',
        source_code='ext_test0',
        event_type='simple_ticket',
        venue="Sadler's Wells",
        classes={
            'dance': 'Ballet & Dance'
        },
        postcode='EC1R 4TN',
        city='London',
        city_code='london-uk',
        country='United Kingdom',
        country_code='uk',
        latitude=51.52961137,
        longitude=-0.10601562,
        max_running_time=120,
        min_running_time=90,
        show_performance_time=True,
        has_performances=True,
        is_seated=True,
        needs_departure_date=False,
        needs_duration=False,
        needs_performance=False,
        upsell_list=[
            '6IF',
            '6KU',
            'MH0'
        ],
    )
]
```

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


## Events by ID

> **Definition**

```
GET https://demo.ticketswitch.com/f13/events_by_id.v1?event_id_list={eventidlist}
```

This resource returns detail for one or more specific events by their ID. It
returns a dictionary of events keyed on the event's `event_id`. 

> **Example request**

```shell
curl https://demo.ticketswitch.com/f13/events_by_id.v1 \
    -u "demo:demopass" \
    -d "event_id_list=6IF,6IE" \
    --compressed \
    -G
```

```python
from pyticketswitch import Client


client = Client(user='demo', password='demopass')
events, meta = client.get_events(event_ids=['6IF'])
```
### Request

Parameter | Description
--------- | -----------
`event_id_list` | A comma separated list of event IDs e.g. `25DR` for a single event; `1VLG,1YYO,25DR` for multiple events.

These parameters can be passed in to request additional data for each event, and are described in more detail in the [additional parameters](#additional-parameters) section below:

Parameter | Description
--------- | -----------
`req_avail_details` | Returns [availability details](#availability-details) - a cached list of unique ticket types and price bands available for this event across all performances. **This parameter is not commonly used.**
`req_avail_details_with_perfs` | This will add the list of available performance dates to each avail detail object. *Only valid if used alongside req_avail_details*.
`req_cost_range` | Returns [cost ranges](#cost-range) - a from price and offer detail for each event. *Most partners include this parameter.*
`req_cost_range_best_value_offer` | Returns the offer with the highest percentage saving. *This is the most commonly used offer cost range.*
`req_cost_range_details` | Returns a list of unique ticket types and price bands and their cost ranges across all performances. **This parameter is not commonly used.**
`req_cost_range_max_saving_offer` | Returns the offer with the highest absolute saving.
`req_cost_range_min_cost_offer` | Returns the offer with the lowest cost.
`req_cost_range_top_price_offer` | Returns the offer with the highest cost. This is the least used offer cost range.
`req_cost_range_no_singles_data` | This returns another cost range object that excludes availability with only 1 consecutive seat available. The prices in this cost range will therefore be the same or higher than the outer cost range. It has the same structure as the main cost range (so if you want to see the "best value offer" in the no singles data, you need to add `req_cost_range_best_value_offer` and you will see this data in both cost ranges).
`req_extra_info` | Returns the [descriptive info](#extra-info) for the event, returned as individual sections (`structured_info`) or as a single summary (`event_info` / `event_info_html`).
`req_media_triplet_one` | Triplet one (jpg/png 520x390). [See further detail on media](#media).
`req_media_triplet_two` | Triplet two if available (jpg/png 520x390).
`req_media_triplet_three` | Triplet three if available (jpg/png 520x390).
`req_media_triplet_four` | Triplet four if available (jpg/png 520x390).
`req_media_triplet_five` | Triplet five if available (jpg/png 520x390).
`req_media_seating_plan` | Graphical seating plan of the venue if available (jpg/png varying size).
`req_media_square` | Small square image suitable for search or event avatar (jpg/png 140x140).
`req_media_landscape` | Small landscape banner suitable for search (jpg/png 220x115).
`req_media_marquee` | Large landscape banner suitable for a page heading, if available (jpg/png 700x300).
`req_media_supplier` | Logo of the supplier/producer, if available (jpg/png varying size).
`req_reviews` | Returns [event reviews](#reviews) if available.
`req_video_iframe` | Returns video iframe information if available.


### Response

> **Example response**

```shell
{
  "events_by_id": {
    "6IE": {
      "event": {
        "city_code": "london-uk",
        "city_desc": "London",
        "classes": {
          "dance": "Ballet & Dance"
        },
        "country_code": "uk",
        "country_desc": "United Kingdom",
        "custom_filter": [],
        "event_desc": "Matthew Bourne's Swan Lake test",
        "event_id": "6IE",
        "event_path": "/6IE-matthew-bourne-s-swan-lake-test/",
        "event_status": "live",
        "event_type": "simple_ticket",
        "event_upsell_list": {
          "event_id": [
            "6IF",
            "6KU",
            "MH0"
          ]
        },
        "event_uri_desc": "Matthew-Bourne%27s-Swan-Lake-test",
        "geo_data": {
          "latitude": 51.52961137,
          "longitude": -0.10601562
        },
        "has_no_perfs": false,
        "is_seated": true,
        "max_running_time": 120,
        "min_running_time": 90,
        "need_departure_date": false,
        "need_duration": false,
        "need_performance": true,
        "postcode": "EC1R 4TN",
        "show_perf_time": true,
        "source_code": "ext_test0",
        "source_desc": "External Test Backend 0",
        "venue_desc": "Sadler's Wells",
        "venue_uri_desc": "Sadler%27s-Wells"
      },
      "valid_quantities": [
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "venue_is_enforced": true
    },
    "6IF": {
      "event": {
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
            "6IE",
            "MH0"
          ]
        },
        "event_uri_desc": "Matthew-Bourne%27s-Nutcracker-TEST",
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
        "venue_desc": "Sadler's Wells",
        "venue_uri_desc": "Sadler%27s-Wells"
      },
      "valid_quantities": [
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "venue_is_enforced": true
    }
  }
}
```

```python
from pyticketswitch.event import Event

{
    '6IF': Event(
        id='6IF',
        status='live',
        description="Matthew Bourne's Nutcracker TEST",
        source='External Test Backend 0',
        source_code='ext_test0',
        event_type='simple_ticket',
        venue="Sadler's Wells",
        classes={
            'dance': 'Ballet & Dance'
        },
        postcode='EC1R 4TN',
        city='London',
        city_code='london-uk',
        country='United Kingdom',
        country_code='uk',
        latitude=51.52961137,
        longitude=-0.10601562,
        max_running_time=120,
        min_running_time=120,
        show_performance_time=True,
        has_performances=True,
        is_seated=True,
        needs_departure_date=False,
        needs_duration=False,
        needs_performance=False,
        upsell_list=[
            '6IE',
            'MH0'
        ],
        critic_review_percent=100,
        valid_quantities=None,
    )
}

```

**Attributes**

Attribute | Description
--------- | -----------
`city_desc` | Name of the city where the event is taking place.
`class` | Array of class or category objects.
`country_code` | 2-digit country code (using [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2)).
`country_desc` | Name of the country where the event is taking place.
`critic_review_percent` | The aggregate critic review score, e.g. `80` for 80%.
`custom_filter` | Array of custom filter codes. This can be ignored by partners.
`date_range_start` | A [date/time object](#date-time-object) for the start of the run.
`date_range_end` | A [date/time object](#date-time-object) for the end of the run.
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
`is_seated` | `true` for seated events.
`min_running_time` | Minimum length / duration in minutes (not always present).
`max_running_time` | Maximum length / duration in minutes (not always present).
`need_departure_date` | Flag indicating whether the event needs a departure date specified. This is `false` for most events.
`need_duration` | Flag indicating whether the event needs duration (specific to `hotel_room` events only).
`need_performance` | Flag indicating if a performance must be selected in order to retrieve availability.
`postcode` | Postcode of the event location.
`show_perf_time` | `false` if the performance time is not relevant, for example some events use a performance description rather than specific times.
`source_desc` | Despription of the source supplier e.g. `Nimax`.
`user_review_percent` | The aggregate user review score, e.g. `80` for 80%.
`venue_desc` | Name of the venue e.g. `Sadler's Wells`.


## Related Events

Many events have related events that can be displayed to customers to increase
sales. There are two classes of such events, upsell events and add-on events. 

Upsell events are events that customers may be interested in purchasing due to
their similarity with the primary event. For example, two ballet performances
may be listed as being 'upsell' related events, as if the customer is interested
in one ballet performance, they may be interested in others.

Add-on events are events or miscellaneous items that cannot be purchased
individually, but are only available to add to an existing trolley. This may
include things like merchandise that is picked up at the performance, etc.

> **Definition**

```
GET https://demo.ticketswitch.com/f13/related_events.v1
```

### Request

> **Example request – getting related events by event ID list**

```shell
curl https://demo.ticketswitch.com/f13/related_events.v1 \
    -u "demo:demopass" \
    -d "event_id_list=7AB" \
    --compressed \
    -G
```

```python
from pyticketswitch import Client

client = Client(user='demo', password='demopass')
(addon_events, addon_meta), (upsell_events, upsell_meta) = client.get_related_events(event_ids=[7AB])
```

> **Example request – getting related events by trolley token**

```shell
curl https://demo.ticketswitch.com/f13/related_events.v1 \
    -u "demo:demopass" \
    -d "trolley_token=M2--6TcZRZsfgZI0E8PmlGWqr5PQluhxE46Nqnno3Xa24lFuIKM_0IYJq40BDvisBRcXKsaGBdLfu8B5TfwI4s7jk7y7xRto_SwJL177u_vuvvq_Ynqnj64IfAHmItXxz_-xdTQGXh_G-BuPGxPXNY_Ycimiv1ZYlPFuPjsoVugnUnyu2F0TmpdztEtgI9PC1WhnTeHeRUDEBvVewOIf_PEJkPVR-UXFDg3gxbHYQBENW5XpkYnRhc22IqrB7eAoK0MbTLaPMk3uB1BKnz_B7fasSU_NU3nhXvmgHa4suvCT9oEaQSGvq4G0glOtyCiqL1FfVJ2R3KIgb2xC4NbAm4cNL5q22V_W0eYGmJ9DTBJ90UWvAWH7UeFLM1--Z" \
    --compressed \
    -G
```

```python
from pyticketswitch import Client

client = Client(user='demo', password='demopass')
(addon_events, addon_meta), (upsell_events, upsell_meta) = client.get_related_events(token="M2--6TcZRZsfgZI0E8PmlGWqr5PQluhxE46Nqnno3Xa24lFuIKM_0IYJq40BDvisBRcXKsaGBdLfu8B5TfwI4s7jk7y7xRto_SwJL177u_vuvvq_Ynqnj64IfAHmItXxz_-xdTQGXh_G-BuPGxPXNY_Ycimiv1ZYlPFuPjsoVugnUnyu2F0TmpdztEtgI9PC1WhnTeHeRUDEBvVewOIf_PEJkPVR-UXFDg3gxbHYQBENW5XpkYnRhc22IqrB7eAoK0MbTLaPMk3uB1BKnz_B7fasSU_NU3nhXvmgHa4suvCT9oEaQSGvq4G0glOtyCiqL1FfVJ2R3KIgb2xC4NbAm4cNL5q22V_W0eYGmJ9DTBJ90UWvAWH7UeFLM1--Z")
```

### Request

This endpoint can provide a list of add-on events and upsell events based
on one of the following parameters:

Parameter | Description
--------- | -----------
`event_id_list` | A comma separated list of event IDs e.g. `25DR` for a single event; `1VLG,1YYO,25DR` for multiple events.
`trolley_token` | A trolley token for a [trolley](#trolley) containing seats for a performance

Please note that only one of the parameters can be used in the request.
Add-on events will not be returned for an `event_id_list` call, as they can
only be added to an existing trolley.

As with [`events_by_id`](#events-by-id), additional parameters can be passed in
to control paging of results, and to request additional data for each event, as
described in more detail in the [additional parameters](#additional-parameters)
section below.


### Response

> **Example response – getting related events by trolley token**

```shell
{
  "add_on_results": {
    "event": [
      {
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
  },
  "upsell_results": {
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
            "6IE",
            "MH0"
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
from pyticketswitch import Event

# addon_events
[
    Event(
        min_running_time=None,
        event_type=u'misc_item',
        has_performances=True,
        venue_info_html=None,
        critic_review_percent=None,
        needs_departure_date=False,
        valid_quantities=None,
        event_info_html=None,
        raw={
            u'event_type': u'misc_item',
            u'custom_filter': [],
            u'event_desc': u'Unremarkable Cat Lunchbox',
            u'show_perf_time': False,
            u'need_performance': False,
            u'postcode': u'W6 7ES',
            u'country_code': u'uk',
            u'has_no_perfs': False,
            u'event_status': u'live',
            u'country_desc': u'United Kingdom',
            u'venue_desc': u'Lyric Apollo',
            u'need_duration': False,
            u'event_id': u'7AC',
            u'is_seated': False,
            u'need_departure_date': False,
            u'classes': {
                u'theatre': u'Theatre'
            },
            u'is_add_on': True,
            u'city_code': u'london-uk',
            u'geo_data': {
                u'latitude': 51.49306,
                u'longitude': -0.22639
            },
            u'event_path': u'/7AC-unremarkable-cat-lunchbox/',
            u'venue_uri_desc': u'Lyric-Apollo',
            u'source_code': u'ext_test1',
            u'city_desc': u'London',
            u'source_desc': u'External Test Backend 1',
            u'event_uri_desc': u'Unremarkable-Cat-Lunchbox'
        },
        availability_details=[],
        postcode=u'W6 7ES',
        country_code=u'uk',
        id=u'7AC',
        city=u'London',
        filters=[],
        media={},
        is_seated=False,
        cost_range_details=[],
        event_info=None,
        content={},
        source=u'External Test Backend 1',
        max_running_time=None,
        needs_performance=False,
        latitude=51.49306,
        upsell_list=[],
        city_code=u'london-uk',
        venue_addr=None,
        status=u'live',
        description=u'Unremarkable Cat Lunchbox',
        venue_addr_html=None,
        cost_range=None,
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

# upsell_events
[
    Event(
        min_running_time=120,
        event_type=u'simple_ticket',
        has_performances=True,
        venue_info_html=None,
        critic_review_percent=100,
        needs_departure_date=False,
        valid_quantities=None,
        event_info_html=None,
        raw={
            u'min_running_time': 120,
            u'event_type': u'simple_ticket',
            u'custom_filter': [],
            u'critic_review_percent': 100,
            u'event_desc': u"Matthew Bourne's Nutcracker TEST",
            u'event_uri_desc': u'Matthew-Bourne%27s-Nutcracker-TEST',
            u'postcode': u'EC1R 4TN',
            u'country_code': u'uk',
            u'has_no_perfs': False,
            u'event_status': u'live',
            u'need_performance': True,
            u'country_desc': u'United Kingdom',
            u'venue_desc': u"Sadler's Wells",
            u'need_duration': False,
            u'event_id': u'6IF',
            u'is_seated': True,
            u'need_departure_date': False,
            u'user_review_percent': 100,
            u'max_running_time': 120,
            u'show_perf_time': True,
            u'event_upsell_list': {
                u'event_id': [
                    u'6IE',
                    u'MH0'
                ]
            },
            u'is_add_on': False,
            u'city_code': u'london-uk',
            u'geo_data': {
                u'latitude': 51.52961137,
                u'longitude': -0.10601562
            },
            u'event_path': u'/6IF-matthew-bourne-s-nutcracker-test/',
            u'venue_uri_desc': u'Sadler%27s-Wells',
            u'source_code': u'ext_test0',
            u'city_desc': u'London',
            u'source_desc': u'External Test Backend 0',
            u'classes': {
                u'dance': u'Ballet & Dance'
            }
        },
        availability_details=[],
        postcode=u'EC1R 4TN',
        country_code=u'uk',
        id=u'6IF',
        city=u'London',
        filters=[],
        media={},
        is_seated=True,
        cost_range_details=[],
        event_info=None,
        content={},
        source=u'External Test Backend 0',
        max_running_time=120,
        needs_performance=False,
        latitude=51.52961137,
        upsell_list=[
            u'6IE',
            u'MH0'
        ],
        city_code=u'london-uk',
        venue_addr=None,
        status=u'live',
        description=u"Matthew Bourne's Nutcracker TEST",
        venue_addr_html=None,
        cost_range=None,
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
        event_info_html=None,
        raw={
            u'min_running_time': 90,
            u'event_type': u'simple_ticket',
            u'custom_filter': [],
            u'event_desc': u'Toy Story - The Opera',
            u'show_perf_time': True,
            u'need_performance': True,
            u'postcode': u'WC2E 7RQ',
            u'country_code': u'uk',
            u'has_no_perfs': False,
            u'event_status': u'live',
            u'country_desc': u'United Kingdom',
            u'venue_desc': u'Lyceum Theatre',
            u'need_duration': False,
            u'event_id': u'7AA',
            u'is_seated': True,
            u'need_departure_date': False,
            u'max_running_time': 90,
            u'classes': {
                u'opera': u'Opera'
            },
            u'is_add_on': False,
            u'city_code': u'london-uk',
            u'geo_data': {
                u'latitude': 51.511556,
                u'longitude': -0.11975
            },
            u'event_path': u'/7AA-toy-story-the-opera/',
            u'venue_uri_desc': u'Lyceum-Theatre',
            u'source_code': u'ext_test0',
            u'city_desc': u'London',
            u'source_desc': u'External Test Backend 0',
            u'event_uri_desc': u'Toy-Story-The-Opera'
        },
        availability_details=[],
        postcode=u'WC2E 7RQ',
        country_code=u'uk',
        id=u'7AA',
        city=u'London',
        filters=[],
        media={},
        is_seated=True,
        cost_range_details=[],
        event_info=None,
        content={},
        source=u'External Test Backend 0',
        max_running_time=90,
        needs_performance=False,
        latitude=51.511556,
        upsell_list=[],
        city_code=u'london-uk',
        venue_addr=None,
        status=u'live',
        description=u'Toy Story - The Opera',
        venue_addr_html=None,
        cost_range=None,
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

**Attributes**

Attribute | Description
--------- | -----------
`add_on_results` | A list of events and metadata associated with add-on events, in the same format as [`events_by_id`](#events-by-id)
`upsell_results` | A list of events and metadata associated with upsell events, in the same format

## --- Additional parameters ---

There are several additional parameters described below that can be provided to
return additional data for each event.

These additional parameters require extra processing to retrieve the requested
data, which will slow down the resource, sometimes substantially. They should
therefore only be used where necessary.


## Media

Most events include image URLs and a YouTube video. Almost all events will
include the triplet_one, square and landscape images. In most cases the 
seating_plan, triplet_two and triplet_three images are also provided, along with 
a video_iframe (typically YouTube).

### Request

There are several different types of media and they all need to be requested
independently when using the API. When using one of the language wrappers
all media requests are consolidated.


> **Example Request**

```shell
curl https://demo.ticketswitch.com/f13/events_by_id.v1 \
    -u "demo:demopass" \
    -d "event_id_list=6IF" \
    -d "req_media_triplet_one" \
    -d "req_media_triplet_two" \
    -d "req_media_triplet_three" \
    -d "req_media_triplet_four" \
    -d "req_media_triplet_five" \
    -d "req_media_seating_plan" \
    -d "req_media_square" \
    -d "req_media_landscape" \
    -d "req_media_marquee" \
    -d "req_media_supplier" \
    -d "req_video_iframe" \
    --compressed \
    -G
```

```python
from pyticketswitch import Client


client = Client('demo', 'demopass')
events, meta = client.get_events(['6IF'], media=True)
```


Parameter | Description
--------- | -----------
`req_media_triplet_one` | Triplet one (jpg/png 520x390) - this is our most commonly used event image (ignore the "triplet" in the name which no longer makes sense...)
`req_media_triplet_two` | Triplet two if available (jpg/png 520x390)
`req_media_triplet_three` | Triplet three if available (jpg/png 520x390)
`req_media_triplet_four` | Triplet four if available (jpg/png 520x390)
`req_media_triplet_five` | Triplet five if available (jpg/png 520x390)
`req_media_seating_plan` | Graphical seating plan of the venue if available (jpg/png varying size)
`req_media_square` | Small square image suitable for search or event avatar (jpg/png 140x140)
`req_media_landscape` | Small landscape banner suitable for search (jpg/png 220x115)
`req_media_marquee` | Large landscape banner suitable for a page heading, if available (jpg/png 700x300)
`req_media_supplier` | Logo of the supplier/producer, if available (jpg/png varying size)
`req_video_iframe` | Returns video iframe information if available

### Response

> **Example Response**

```shell
{
  "events_by_id": {
    "6IF": {
      "event": {
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
            "6IE",
            "MH0"
          ]
        },
        "event_uri_desc": "Matthew-Bourne%27s-Nutcracker-TEST",
        "geo_data": {
          "latitude": 51.52961137,
          "longitude": -0.10601562
        },
        "has_no_perfs": false,
        "is_seated": true,
        "max_running_time": 120,
        "media": {
          "media_asset": [
            {
              "caption": "",
              "caption_html": "",
              "host": "d1wx4w35ubmdix.cloudfront.net",
              "insecure_complete_url": "https://d1wx4w35ubmdix.cloudfront.net/shared/event_media/cropper/93/93c04bf0d24ef3d05e8fef4ba7709df0434ea13c.jpg",
              "name": "landscape",
              "path": "/shared/event_media/cropper/93/93c04bf0d24ef3d05e8fef4ba7709df0434ea13c.jpg",
              "secure_complete_url": "https://d1wx4w35ubmdix.cloudfront.net/shared/event_media/cropper/93/93c04bf0d24ef3d05e8fef4ba7709df0434ea13c.jpg",
              "supports_http": false,
              "supports_https": true
            },
            {
              "caption": "",
              "caption_html": "",
              "host": "d1wx4w35ubmdix.cloudfront.net",
              "insecure_complete_url": "https://d1wx4w35ubmdix.cloudfront.net/shared/event_media/cropper/7e/7e048c655f79d1ae01419c92420861df156b83fc.jpg",
              "name": "marquee",
              "path": "/shared/event_media/cropper/7e/7e048c655f79d1ae01419c92420861df156b83fc.jpg",
              "secure_complete_url": "https://d1wx4w35ubmdix.cloudfront.net/shared/event_media/cropper/7e/7e048c655f79d1ae01419c92420861df156b83fc.jpg",
              "supports_http": false,
              "supports_https": true
            },
            {
              "caption": "",
              "caption_html": "",
              "host": "d1wx4w35ubmdix.cloudfront.net",
              "insecure_complete_url": "http://d1wx4w35ubmdix.cloudfront.net/shared/event_media/cropper_cloud/29/2974e58797af0d91752b73da390ae6f8fa6e4862.jpg",
              "name": "seating_plan",
              "path": "/shared/event_media/cropper_cloud/29/2974e58797af0d91752b73da390ae6f8fa6e4862.jpg",
              "secure_complete_url": "https://d1wx4w35ubmdix.cloudfront.net/shared/event_media/cropper_cloud/29/2974e58797af0d91752b73da390ae6f8fa6e4862.jpg",
              "supports_http": true,
              "supports_https": true
            },
            {
              "caption": "",
              "caption_html": "",
              "host": "d1wx4w35ubmdix.cloudfront.net",
              "insecure_complete_url": "https://d1wx4w35ubmdix.cloudfront.net/shared/event_media/cropper/5e/5e004f25b5aab6f432cd7e6839b70942d8a5f17b.jpg",
              "name": "square",
              "path": "/shared/event_media/cropper/5e/5e004f25b5aab6f432cd7e6839b70942d8a5f17b.jpg",
              "secure_complete_url": "https://d1wx4w35ubmdix.cloudfront.net/shared/event_media/cropper/5e/5e004f25b5aab6f432cd7e6839b70942d8a5f17b.jpg",
              "supports_http": false,
              "supports_https": true
            },
            {
              "caption": "",
              "caption_html": "",
              "host": "d1wx4w35ubmdix.cloudfront.net",
              "insecure_complete_url": "http://d1wx4w35ubmdix.cloudfront.net/shared/event_media/ext_test0/+system_ext_test0/supplier.jpg",
              "name": "supplier",
              "path": "/shared/event_media/ext_test0/+system_ext_test0/supplier.jpg",
              "secure_complete_url": "https://d1wx4w35ubmdix.cloudfront.net/shared/event_media/ext_test0/+system_ext_test0/supplier.jpg",
              "supports_http": true,
              "supports_https": true
            },
            {
              "caption": "",
              "caption_html": "",
              "host": "d1wx4w35ubmdix.cloudfront.net",
              "insecure_complete_url": "https://d1wx4w35ubmdix.cloudfront.net/shared/event_media/cropper/f9/f9aaf4bc04477c303d78bc5107cc0754df846ac8.jpg",
              "name": "triplet_five",
              "path": "/shared/event_media/cropper/f9/f9aaf4bc04477c303d78bc5107cc0754df846ac8.jpg",
              "secure_complete_url": "https://d1wx4w35ubmdix.cloudfront.net/shared/event_media/cropper/f9/f9aaf4bc04477c303d78bc5107cc0754df846ac8.jpg",
              "supports_http": false,
              "supports_https": true
            },
            {
              "caption": "",
              "caption_html": "",
              "host": "d1wx4w35ubmdix.cloudfront.net",
              "insecure_complete_url": "https://d1wx4w35ubmdix.cloudfront.net/shared/event_media/cropper/f9/f9aaf4bc04477c303d78bc5107cc0754df846ac8.jpg",
              "name": "triplet_four",
              "path": "/shared/event_media/cropper/f9/f9aaf4bc04477c303d78bc5107cc0754df846ac8.jpg",
              "secure_complete_url": "https://d1wx4w35ubmdix.cloudfront.net/shared/event_media/cropper/f9/f9aaf4bc04477c303d78bc5107cc0754df846ac8.jpg",
              "supports_http": false,
              "supports_https": true
            },
            {
              "caption": "",
              "caption_html": "",
              "host": "d1wx4w35ubmdix.cloudfront.net",
              "insecure_complete_url": "http://d1wx4w35ubmdix.cloudfront.net/shared/event_media/cropper_cloud/0e/0e255d4f96bd02baa7bfd22fd2cab90c8f6ced34.jpg",
              "name": "triplet_one",
              "path": "/shared/event_media/cropper_cloud/0e/0e255d4f96bd02baa7bfd22fd2cab90c8f6ced34.jpg",
              "secure_complete_url": "https://d1wx4w35ubmdix.cloudfront.net/shared/event_media/cropper_cloud/0e/0e255d4f96bd02baa7bfd22fd2cab90c8f6ced34.jpg",
              "supports_http": true,
              "supports_https": true
            },
            {
              "caption": "",
              "caption_html": "",
              "host": "d1wx4w35ubmdix.cloudfront.net",
              "insecure_complete_url": "http://d1wx4w35ubmdix.cloudfront.net/shared/event_media/cropper_cloud/ca/ca2d4589a89480514f0db6fcec8a982652280093.jpg",
              "name": "triplet_two",
              "path": "/shared/event_media/cropper_cloud/ca/ca2d4589a89480514f0db6fcec8a982652280093.jpg",
              "secure_complete_url": "https://d1wx4w35ubmdix.cloudfront.net/shared/event_media/cropper_cloud/ca/ca2d4589a89480514f0db6fcec8a982652280093.jpg",
              "supports_http": true,
              "supports_https": true
            }
          ]
        },
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
        "venue_uri_desc": "Sadler%27s-Wells",
        "video_iframe": {
          "video_iframe_caption": "",
          "video_iframe_caption_html": "",
          "video_iframe_height": 315,
          "video_iframe_host": "www.youtube.com",
          "video_iframe_path": "/embed/G1JpEHGizk4?rel=0",
          "video_iframe_supports_http": false,
          "video_iframe_supports_https": true,
          "video_iframe_url_when_insecure": "https://www.youtube.com/embed/G1JpEHGizk4?rel=0",
          "video_iframe_url_when_secure": "https://www.youtube.com/embed/G1JpEHGizk4?rel=0",
          "video_iframe_width": 420
        }
      },
      "valid_quantities": [
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "venue_is_enforced": true
    }
  }
}
```

```python
from pyticketswitch.media import Media
from pyticketswitch.event import Event

{
    '6IF': Event(
        id='6IF',
        status='live',
        description="Matthew Bourne's Nutcracker TEST",
        source='External Test Backend 0',
        source_code='ext_test0',
        event_type='simple_ticket',
        venue="Sadler's Wells",
        classes={
            'dance': 'Ballet & Dance'
        },
        postcode='EC1R 4TN',
        city='London',
        city_code='london-uk',
        country='United Kingdom',
        country_code='uk',
        latitude=51.52961137,
        longitude=-0.10601562,
        max_running_time=120,
        min_running_time=120,
        show_performance_time=True,
        has_performances=True,
        is_seated=True,
        needs_departure_date=False,
        needs_duration=False,
        needs_performance=False,
        upsell_list=[
            '6IE',
            'MH0'
        ],
        media={
            'landscape': Media(
                caption='',
                caption_html='',
                name='landscape',
                url='https://d1wx4w35ubmdix.cloudfront.net/media/event/6IF/matthew-bournes-nutcracker-test-landscape-M2kz.jpg?versionId=C3UX_j98L2FWDOof4BCY15QyKrBJ0ZRR',
                secure=True,
                width=None,
                height=None,
            ),
            'marquee': Media(
                caption='',
                caption_html='',
                name='marquee',
                url='https://d1wx4w35ubmdix.cloudfront.net/media/event/6IF/matthew-bournes-nutcracker-test-marquee-bGIw.jpg?versionId=WwcyqyDnuxFwYgare_6Omy61AACpF8hG',
                secure=True,
                width=None,
                height=None,
            ),
            'seating_plan': Media(
                caption='',
                caption_html='',
                name='seating_plan',
                url='https://d1wx4w35ubmdix.cloudfront.net/media/event/6IF/matthew-bournes-nutcracker-test-seating-plan-dzJ3.jpg',
                secure=True,
                width=None,
                height=None,
            ),
            'square': Media(
                caption='',
                caption_html='',
                name='square',
                url='https://d1wx4w35ubmdix.cloudfront.net/media/event/6IF/matthew-bournes-nutcracker-test-square-Z0Jk.jpg?versionId=09daZJn.14RH7.84cRKXK2FfODDmFYpC',
                secure=True,
                width=None,
                height=None,
            ),
            'triplet_four': Media(
                caption='',
                caption_html='',
                name='triplet_four',
                url='https://d1wx4w35ubmdix.cloudfront.net/media/event/6IF/matthew-bournes-nutcracker-test-triplet-four-bndw.jpg?versionId=kz.Y.qd379E_wm2ZNWnGRMtLFu4Teqpu',
                secure=True,
                width=None,
                height=None,
            ),
            'triplet_one': Media(
                caption='',
                caption_html='',
                name='triplet_one',
                url='https://d1wx4w35ubmdix.cloudfront.net/media/event/6IF/matthew-bournes-nutcracker-test-triplet-one-eWVq.jpg?versionId=WewM3wJOwzhP.QOsJqDSBUDxBwimxNV_',
                secure=True,
                width=None,
                height=None,
            ),
            'triplet_three': Media(
                caption='',
                caption_html='',
                name='triplet_three',
                url='https://d1wx4w35ubmdix.cloudfront.net/media/event/6IF/matthew-bournes-nutcracker-test-triplet-three-U2Yz.jpg?versionId=MPJgrknmvajkTolWT55cmh7McDrI4PCD',
                secure=True,
                width=None,
                height=None,
            ),
            'triplet_two': Media(
                caption='',
                caption_html='',
                name='triplet_two',
                url='https://d1wx4w35ubmdix.cloudfront.net/media/event/6IF/matthew-bournes-nutcracker-test-triplet-two-d0xl.jpg?versionId=FpaMhCJw.gCmiOn598.m3OmtXJv5p0OM',
                secure=True,
                width=None,
                height=None,
            ),
            'video': Media(
                caption='',
                caption_html='',
                name='video',
                url='https://www.youtube.com/embed/G1JpEHGizk4?rel=0',
                secure=True,
                width=420,
                height=315,
            )
        },
        critic_review_percent=100,
    )
}

```

### Media Asset

Media assets provide both secure and insecure URLs where possible when either a
secure or and insecure URL is not available, the other URL will be substituted.
Generally speaking all our media assets at this point will be https with no http
option, however this is not a universal rule.

Attribute | Description
--------- | -----------
`name` | The name of the image, default names are `triple_{one|two|three|four|five}`, `square`, `landscape`, `marquee`, `supplier`, and `seating_plan`
`caption` | any text related to the image in plain text
`caption_html` | any text related to the image in html
`host` | the hostname of server on which the image is hosted
`path` | the absolute path of the file on the host
`insecure_complete_url` | a complete http address 
`secure_complete_url` | a complete https address 
`supports_http`| indicates that the asset is accessible via http
`supports_https`| indicates that the asset is accessible via https

### Video iframe

The video iframe is a slightly different media asset that contains information
about an embeddable video related to the event. It shares the same attributes
with a regular media asset but also contains hints for iframe sizing.

Attribute | Description
--------- | -----------
`video_iframe_height` | the suggested height of the iframe
`video_iframe_width` | the suggested width of the iframe
`video_iframe_caption` | any text related to the image in plain text
`video_iframe_caption_html` | any text related to the image in html
`video_iframe_host` | the hostname of server on which the image is hosted
`video_iframe_path` | the absolute path of the file on the host
`video_iframe_insecure_complete_url` | a complete http address 
`video_iframe_secure_complete_url` | a complete https address 
`video_iframe_supports_http`| indicates that the asset is accessible via http
`video_iframe_supports_https`| indicates that the asset is accessible via https


## Extra info

Most events will have additional information and content available. This data is
not returned by default because it can slow down response time, especially when
many events are being returned.

### Request

> **Example request**

```shell

curl https://demo.ticketswitch.com/f13/events_by_id.v1 \
    -u "demo:demopass" \
    -d "event_id_list=6IF" \
    -d "req_extra_info" \
    --compressed \
    -G
```

```python
from pyticketswitch import Client


client = Client('demo', 'demopass')
events, meta = client.get_events(['6IF'], extra_info=True)
```

Parameter | Description
--------- | -----------
`req_extra_info` | Returns the descriptive info for the event, returned as individual sections (`structured_info`) and as a single summary (`event_info` / `event_info_html`)


### Response

> **Example response**

```shell
{
  "events_by_id": {
    "6IF": {
      "event": {
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
        "event_info": "Matthew Bourne's stunning production of Nutcracker! returns in 2008 to Sadler's Wells having broken all box office records during last year's sell-out season.\n\nThis festive treat is full of his trademark style of wit, pathos and theatrical magic. Nutcracker! follows Clara's journey from a bleak Christmas Eve at Dr.Dross' Orphanage, through a shimmering ice-skating wonderland and to the spectacular candy folk of Sweetieland.\n\nOliver award-winning designer Anthony Ward and Tchaikovsky's much-loved score combined with sizzling choreography guarantee that Matthew Bourne's Nutcracker! is a fresh, lip-smacking, serving of traditional Christmas fare.\n\nMatthew Bourne has achieved both artistic and commercial success with his imaginative new versions of classical ballets. Last year his Play Without Words made for the Royal National Theatre, received two Olivier Awards and is shortly to be revived.\n\nDuration\n\nA Goldilocks duration - not too long, not too short, just the right amount of time.\n\n\nPerformance Times\n\nAnytime you like! As long as there's a show on...\n\n\nWhere Do I Go\n\nTo Sadler's Wells of course!\n\n\nWhat's Included\n\nEverything you need.\n\n\nGood To Know\n\nWarning! Men in very tight tights.\n\n\nSuitable For Children\n\nSure, just be aware of the aforementioned tights.\n\n\nOffers Information\n\nThis is some offers information\n\n",
        "event_info_html": "<div><p>Matthew Bourne's stunning production of Nutcracker! returns in 2008 to Sadler's Wells having broken all box office records during last year's sell-out season.</p>\r\n<p>This festive treat is full of his trademark style of wit, pathos and theatrical magic. Nutcracker! follows Clara's journey from a bleak Christmas Eve at Dr.Dross' Orphanage, through a shimmering ice-skating wonderland and to the spectacular candy folk of Sweetieland.</p>\r\n<p>Oliver award-winning designer Anthony Ward and Tchaikovsky's much-loved score combined with sizzling choreography guarantee that Matthew Bourne's Nutcracker! is a fresh, lip-smacking, serving of traditional Christmas fare.</p>\r\n<p>Matthew Bourne has achieved both artistic and commercial success with his imaginative new versions of classical ballets. Last year his Play Without Words made for the Royal National Theatre, received two Olivier Awards and is shortly to be revived.</p></div>\n\n<h4>Duration</h4>\n<div><p>A Goldilocks duration - not <em>too</em> long, not <em>too</em> short, just the right amount of time.</p></div>\n\n<h4>Performance Times</h4>\n<div><p>Anytime you like! As long as there's a show on...</p></div>\n\n<h4>Where Do I Go</h4>\n<div><p>To Sadler's Wells of course!</p></div>\n\n<h4>What&#39;s Included</h4>\n<div><p>Everything you need.</p></div>\n\n<h4>Good To Know</h4>\n<div><p>Warning! Men in very tight tights.</p></div>\n\n<h4>Suitable For Children</h4>\n<div><p>Sure, just be aware of the aforementioned tights.</p></div>\n\n<h4>Offers Information</h4>\n<div><p>This is some offers information</p></div>\n",
        "event_path": "/6IF-matthew-bourne-s-nutcracker-test/",
        "event_status": "live",
        "event_type": "simple_ticket",
        "event_upsell_list": {
          "event_id": [
            "6IE",
            "MH0"
          ]
        },
        "event_uri_desc": "Matthew-Bourne%27s-Nutcracker-TEST",
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
        "structured_info": {
          "address": {
            "name": "Address",
            "value": "Roseberry Avenue\r\nIslington\r\nLondon\r\nUK",
            "value_html": "<p>Roseberry Avenue\r\nIslington\r\nLondon\r\nUK</p>"
          },
          "duration": {
            "name": "Duration",
            "value": "A Goldilocks duration - not too long, not too short, just the right amount of time.\n",
            "value_html": "<p>A Goldilocks duration - not <em>too</em> long, not <em>too</em> short, just the right amount of time.</p>"
          },
          "good_to_know": {
            "name": "Good To Know",
            "value": "Warning! Men in very tight tights.\n",
            "value_html": "<p>Warning! Men in very tight tights.</p>"
          },
          "offers": {
            "name": "Offers Information",
            "value": "This is some offers information\n",
            "value_html": "<p>This is some offers information</p>"
          },
          "overview": {
            "name": "Overview",
            "value": "Matthew Bourne's stunning production of Nutcracker! returns in 2008 to Sadler's Wells having broken all box office records during last year's sell-out season.\n\nThis festive treat is full of his trademark style of wit, pathos and theatrical magic. Nutcracker! follows Clara's journey from a bleak Christmas Eve at Dr.Dross' Orphanage, through a shimmering ice-skating wonderland and to the spectacular candy folk of Sweetieland.\n\nOliver award-winning designer Anthony Ward and Tchaikovsky's much-loved score combined with sizzling choreography guarantee that Matthew Bourne's Nutcracker! is a fresh, lip-smacking, serving of traditional Christmas fare.\n\nMatthew Bourne has achieved both artistic and commercial success with his imaginative new versions of classical ballets. Last year his Play Without Words made for the Royal National Theatre, received two Olivier Awards and is shortly to be revived.\n",
            "value_html": "<p>Matthew Bourne's stunning production of Nutcracker! returns in 2008 to Sadler's Wells having broken all box office records during last year's sell-out season.</p>\r\n<p>This festive treat is full of his trademark style of wit, pathos and theatrical magic. Nutcracker! follows Clara's journey from a bleak Christmas Eve at Dr.Dross' Orphanage, through a shimmering ice-skating wonderland and to the spectacular candy folk of Sweetieland.</p>\r\n<p>Oliver award-winning designer Anthony Ward and Tchaikovsky's much-loved score combined with sizzling choreography guarantee that Matthew Bourne's Nutcracker! is a fresh, lip-smacking, serving of traditional Christmas fare.</p>\r\n<p>Matthew Bourne has achieved both artistic and commercial success with his imaginative new versions of classical ballets. Last year his Play Without Words made for the Royal National Theatre, received two Olivier Awards and is shortly to be revived.</p>"
          },
          "pricing_details_info": {
            "name": "Pricing details information",
            "value": "Book by 30th April for performances from 10 July - 10 August and pay no booking fee on this show\n",
            "value_html": "<p>Book by 30th April for performances from 10 July - 10 August and pay no booking fee on this show</p>"
          },
          "suitable_for_children": {
            "name": "Suitable For Children",
            "value": "Sure, just be aware of the aforementioned tights.\n",
            "value_html": "<p>Sure, just be aware of the aforementioned tights.</p>"
          },
          "whats_included": {
            "name": "What's Included",
            "value": "Everything you need.\n",
            "value_html": "<p>Everything you need.</p>"
          },
          "when_can_i_go": {
            "name": "Performance Times",
            "value": "Anytime you like! As long as there's a show on...\n",
            "value_html": "<p>Anytime you like! As long as there's a show on...</p>"
          },
          "where_do_i_go": {
            "name": "Where Do I Go",
            "value": "To Sadler's Wells of course!\n",
            "value_html": "<p>To Sadler's Wells of course!</p>"
          }
        },
        "user_review_percent": 100,
        "venue_addr": "Roseberry Avenue\r\nIslington\r\nLondon\r\nUK",
        "venue_addr_html": "<div><p>Roseberry Avenue\r\nIslington\r\nLondon\r\nUK</p></div>\n",
        "venue_desc": "Sadler's Wells",
        "venue_uri_desc": "Sadler%27s-Wells"
      },
      "valid_quantities": [
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "venue_is_enforced": true
    }
  }
}
```

```python
from pyticketswitch.content import Content
from pyticketswitch.event import Event

{
    '6IF': Event(
        id='6IF',
        status='live',
        description="Matthew Bourne's Nutcracker TEST",
        source='External Test Backend 0',
        source_code='ext_test0',
        event_type='simple_ticket',
        venue="Sadler's Wells",
        classes={
            'dance': 'Ballet & Dance'
        },
        filters=[
            
        ],
        postcode='EC1R 4TN',
        city='London',
        city_code='london-uk',
        country='United Kingdom',
        country_code='uk',
        latitude=51.52961137,
        longitude=-0.10601562,
        max_running_time=120,
        min_running_time=120,
        show_performance_time=True,
        has_performances=True,
        is_seated=True,
        needs_departure_date=False,
        needs_duration=False,
        needs_performance=False,
        upsell_list=[
            '6IE',
            'MH0'
        ],
        content={
            'address': Content(
                name='Address',
                value='Roseberry Avenue Islington London UK\n',
                value_html='<p>Roseberry Avenue Islington London UK</p>',
            ),
            'duration': Content(
                name='Duration',
                value='A Goldilocks duration - not too long, not too short, just the right amount of time.\n',
                value_html='<p>A Goldilocks duration - not <em>too</em> long, not <em>too</em> short, just the right amount of time.</p>',
            ),
            'good_to_know': Content(
                name='Good To Know',
                value='Warning! Men in very tight tights.\n',
                value_html='<p>Warning! Men in very tight tights.</p>',
            ),
            'offers': Content(
                name='Offers Information',
                value='This is some offers information\n',
                value_html='<p>This is some offers information</p>',
            ),
            'overview': Content(
                name='Overview',
                value="Matthew Bourne's stunning production of Nutcracker! returns in 2008 to Sadler's Wells having broken all box office records during last year's sell-out season.\n\nThis festive treat is full of his trademark style of wit, pathos and theatrical magic. Nutcracker! follows Clara's journey from a bleak Christmas Eve at Dr.Dross' Orphanage, through a shimmering ice-skating wonderland and to the spectacular candy folk of Sweetieland.\n\nOliver award-winning designer Anthony Ward and Tchaikovsky's much-loved score combined with sizzling choreography guarantee that Matthew Bourne's Nutcracker! is a fresh, lip-smacking, serving of traditional Christmas fare.\n\nMatthew Bourne has achieved both artistic and commercial success with his imaginative new versions of classical ballets. Last year his Play Without Words made for the Royal National Theatre, received two Olivier Awards and is shortly to be revived.\n",
                value_html="<p>Matthew Bourne's stunning production of Nutcracker! returns in 2008 to Sadler's Wells having broken all box office records during last year's sell-out season.</p>\r\n<p>This festive treat is full of his trademark style of wit, pathos and theatrical magic. Nutcracker! follows Clara's journey from a bleak Christmas Eve at Dr.Dross' Orphanage, through a shimmering ice-skating wonderland and to the spectacular candy folk of Sweetieland.</p>\r\n<p>Oliver award-winning designer Anthony Ward and Tchaikovsky's much-loved score combined with sizzling choreography guarantee that Matthew Bourne's Nutcracker! is a fresh, lip-smacking, serving of traditional Christmas fare.</p>\r\n<p>Matthew Bourne has achieved both artistic and commercial success with his imaginative new versions of classical ballets. Last year his Play Without Words made for the Royal National Theatre, received two Olivier Awards and is shortly to be revived.</p>",
            ),
            'pricing_details_info': Content(
                name='Pricing details information',
                value='Book by 30th April for performances from 10 July - 10 August and pay no booking fee on this show\n',
                value_html='<p>Book by 30th April for performances from 10 July - 10 August and pay no booking fee on this show</p>',
            ),
            'suitable_for_children': Content(
                name='Suitable For Children',
                value='Sure, just be aware of the aforementioned tights.\n',
                value_html='<p>Sure, just be aware of the aforementioned tights.</p>',
            ),
            'whats_included': Content(
                name="What's Included",
                value='Everything you need.\n',
                value_html='<p>Everything you need.</p>',
            ),
            'when_can_i_go': Content(
                name='Performance Times',
                value="Anytime you like! As long as there's a show on...\n",
                value_html="<p>Anytime you like! As long as there's a show on...</p>",
            ),
            'where_do_i_go': Content(
                name='Where Do I Go',
                value="To Sadler's Wells of course!\n",
                value_html="<p>To Sadler's Wells of course!</p>",
            )
        },
        event_info="Matthew Bourne's stunning production of Nutcracker! returns in 2008 to Sadler's Wells having broken all box office records during last year's sell-out season.\n\nThis festive treat is full of his trademark style of wit, pathos and theatrical magic. Nutcracker! follows Clara's journey from a bleak Christmas Eve at Dr.Dross' Orphanage, through a shimmering ice-skating wonderland and to the spectacular candy folk of Sweetieland.\n\nOliver award-winning designer Anthony Ward and Tchaikovsky's much-loved score combined with sizzling choreography guarantee that Matthew Bourne's Nutcracker! is a fresh, lip-smacking, serving of traditional Christmas fare.\n\nMatthew Bourne has achieved both artistic and commercial success with his imaginative new versions of classical ballets. Last year his Play Without Words made for the Royal National Theatre, received two Olivier Awards and is shortly to be revived.\n\nDuration\n\nA Goldilocks duration - not too long, not too short, just the right amount of time.\n\n\nPerformance Times\n\nAnytime you like! As long as there's a show on...\n\n\nWhere Do I Go\n\nTo Sadler's Wells of course!\n\n\nWhat's Included\n\nEverything you need.\n\n\nGood To Know\n\nWarning! Men in very tight tights.\n\n\nSuitable For Children\n\nSure, just be aware of the aforementioned tights.\n\n\nOffers Information\n\nThis is some offers information\n\n",
        event_info_html="<div><p>Matthew Bourne's stunning production of Nutcracker! returns in 2008 to Sadler's Wells having broken all box office records during last year's sell-out season.</p>\r\n<p>This festive treat is full of his trademark style of wit, pathos and theatrical magic. Nutcracker! follows Clara's journey from a bleak Christmas Eve at Dr.Dross' Orphanage, through a shimmering ice-skating wonderland and to the spectacular candy folk of Sweetieland.</p>\r\n<p>Oliver award-winning designer Anthony Ward and Tchaikovsky's much-loved score combined with sizzling choreography guarantee that Matthew Bourne's Nutcracker! is a fresh, lip-smacking, serving of traditional Christmas fare.</p>\r\n<p>Matthew Bourne has achieved both artistic and commercial success with his imaginative new versions of classical ballets. Last year his Play Without Words made for the Royal National Theatre, received two Olivier Awards and is shortly to be revived.</p></div>\n\n<h4>Duration</h4>\n<div><p>A Goldilocks duration - not <em>too</em> long, not <em>too</em> short, just the right amount of time.</p></div>\n\n<h4>Performance Times</h4>\n<div><p>Anytime you like! As long as there's a show on...</p></div>\n\n<h4>Where Do I Go</h4>\n<div><p>To Sadler's Wells of course!</p></div>\n\n<h4>What&#39;s Included</h4>\n<div><p>Everything you need.</p></div>\n\n<h4>Good To Know</h4>\n<div><p>Warning! Men in very tight tights.</p></div>\n\n<h4>Suitable For Children</h4>\n<div><p>Sure, just be aware of the aforementioned tights.</p></div>\n\n<h4>Offers Information</h4>\n<div><p>This is some offers information</p></div>\n",
        venue_addr='Roseberry Avenue Islington London UK\n',
        venue_addr_html='<div><p>Roseberry Avenue Islington London UK</p></div>\n',
        critic_review_percent=100,
    )
}
```


Attribute | Description
--------- | -----------
`content` | this is miscellaneous textual content that can be used to populate an event page.
`event_info` | summary description of the event as plain text
`event_info_html` | summary description of the event as html
`venue_info` | summary description of the venue as plain text
`venue_info_html` | summary description of the venue as html
`venue_addr` | the address of the venue as plain text
`venue_addr_html` | the address of the venue as html


## Reviews

A lot of events have user and critic reviews. At the moment only our critic
reviews are available via the API, but in the near future we will look to expose
our user reviews as well.


### Request


> **Example Request**

```shell
curl https://demo.ticketswitch.com/f13/events_by_id.v1 \
    -u "demo:demopass" \
    -d "event_id_list=6IF" \
    -d "req_reviews" \
    --compressed \
    -G
```

```python
from pyticketswitch import Client


client = Client('demo', 'demopass')
events, meta = client.get_events(['6IF'], reviews=True)
```

Parameter | Description
--------- | -----------
`req_reviews` | Returns event reviews if available

### Response

> **Example Response**

```shell
{
  "events_by_id": {
    "6IF": {
      "event": {
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
            "6IE",
            "MH0"
          ]
        },
        "event_uri_desc": "Matthew-Bourne%27s-Nutcracker-TEST",
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
        "reviews": {
          "review": [
            {
              "is_user_review": false,
              "review_author": "The Times",
              "review_body": "Cannot recommend this show enough!",
              "review_date_desc": "Tue, 17th November 2015",
              "review_iso8601_date_and_time": "2015-11-17T10:00:00Z",
              "review_lang": "en",
              "review_original_url": "",
              "review_time_desc": "10.00 AM",
              "review_title": "Unmissable!",
              "star_rating": 5
            },
            {
              "is_user_review": true,
              "review_author": "Matt Allpress",
              "review_body": "What a show! The mise-en-scène is magical, the choreography is wonderful, the story is magnificent and those tights... incredible. I'll be buying tickets for this show again and again",
              "review_date_desc": "Thu, 28th August 2014",
              "review_iso8601_date_and_time": "2014-08-28T10:00:00Z",
              "review_lang": "en",
              "review_original_url": "",
              "review_time_desc": "10.00 AM",
              "review_title": "Can't get enough!",
              "star_rating": 5
            }
          ]
        },
        "show_perf_time": true,
        "source_code": "ext_test0",
        "source_desc": "External Test Backend 0",
        "user_review_percent": 100,
        "venue_desc": "Sadler's Wells",
        "venue_uri_desc": "Sadler%27s-Wells"
      },
      "valid_quantities": [
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "venue_is_enforced": true
    }
  }
}
```
```python
from pyticketswitch.review import Review
from pyticketswitch.event import Event

{
    '6IF': Event(
        id='6IF',
        status='live',
        description="Matthew Bourne's Nutcracker TEST",
        source='External Test Backend 0',
        source_code='ext_test0',
        event_type='simple_ticket',
        venue="Sadler's Wells",
        classes={
            'dance': 'Ballet & Dance'
        },
        postcode='EC1R 4TN',
        city='London',
        city_code='london-uk',
        country='United Kingdom',
        country_code='uk',
        latitude=51.52961137,
        longitude=-0.10601562,
        max_running_time=120,
        min_running_time=120,
        show_performance_time=True,
        has_performances=True,
        is_seated=True,
        needs_departure_date=False,
        needs_duration=False,
        needs_performance=False,
        upsell_list=[
            '6IE',
            'MH0'
        ],
        reviews=[
            Review(
                body='Cannot recommend this show enough!',
                date_time=datetime.datetime(2015, 11, 17, 10, 0, tzinfo=tzutc()),
                star_rating=5,
                language='en',
                title='Unmissable!',
                is_user=False,
                author='The Times',
                url='',
            ),
            Review(
                body="What a show! The mise-en-scène is magical, the choreography is wonderful, the story is magnificent and those tights... incredible. I'll be buying tickets for this show again and again",
                date_time=datetime.datetime(2014, 8, 28, 10, 0, tzinfo=tzutc()),
                star_rating=5,
                language='en',
                title="Can't get enough!",
                is_user=True,
                author='Matt Allpress',
                url='',
            )
        ],
        critic_review_percent=100,
    )
}
```

Attribute | Description
--------- | -----------
`is_user_review` | `false` for critic reviews; `true` for user reviews
`review_author` | The reviewer - for critic reviews this is normally the name of a newspaper e.g. `The Evening Standard`
`review_body` | The main review content - for critic reviews this is typically a quote from the review
`review_date_desc` | Review date
`review_iso8601_date_and_time` | review date and time in ISO 8601 format
`review_lang` | The ISO 639 language code of the review
`review_original_url` | For critic reviews we sometimes record the original source of the review quote
`review_time_desc` | Review time
`review_title` | Review title, not always present
`star_rating` | A rating between 1 and 5 (5 is best)


## Cost range

Cost ranges are a cached summary of the pricing that has been seen for your
username. They are primarily used to retrieve the minimum (or "from") price for
the event, along with detail of any offers or discounts. They allow you to
display a from price and details of offers for your event, so most
partners request cost ranges.

Cost ranges are generated from availability requests made either by end-users or
by scheduled processes that Ingresso use to update cost range data. You should
not attempt to make multiple availability requests in order to keep this data up
to date - please contact us instead to discuss options api@ingresso.co.uk.  Cost
ranges are only ever returned as part of a parent object (such as event). 

<aside class="notice"> Cost ranges (and avail details) are not guaranteed to be
present (nor accurate) so you should design your application with this in mind,
for example by still displaying events even if they are missing a from
price.</aside>


### Request

> **Example request**

```shell
curl https://demo.ticketswitch.com/f13/events_by_id.v1 \
    -u "demo:demopass" \
    -d "event_id_list=6L9" \
    -d "req_cost_range" \
    -d "req_cost_range_best_value_offer" \
    -d "req_cost_range_max_saving_offer" \
    -d "req_cost_range_min_cost_offer" \
    -d "req_cost_range_top_price_offer" \
    -d "req_cost_range_no_singles_data" \
    --compressed \
    -G
```

```python
from pyticketswitch import Client


client = Client('demo', 'demopass')
events, meta = client.get_events(
    event_ids=['6IF'],
    cost_range=True,
    best_value_offer=True,
    max_saving_offer=True,
    min_cost_offer=True,
    top_price_offer=True,
    no_singles_data=True,
)
```

On its own, `req_cost_range` will add min and max prices only, however by
adding one of the other 5 parameters below you can receive additional data
(multiple parameters are provided because different partners can have different 
definitions of the "best" offer). Most API users request cost ranges.


Parameter | Description
--------- | -----------
`req_cost_range` | Returns cost ranges for each event. 
`req_cost_range_best_value_offer` | Returns the offer with the highest percentage saving. This is the most commonly used offer cost range.
`req_cost_range_max_saving_offer` | Returns the offer with the highest absolute saving.
`req_cost_range_min_cost_offer` | Returns the offer with the lowest cost.
`req_cost_range_top_price_offer` | Returns the offer with the highest cost. This is the least used offer cost range.
`req_cost_range_no_singles_data` | This returns another cost range object that excludes availability with only one consecutive seat available. The prices in this cost range will therefore be the same or higher than the outer cost range. It has the same structure as the main cost range (so if you want to see the "best value offer" in the no singles data, you need to add `req_cost_range_best_value_offer` and you will see this data in both cost ranges).


### Response

> **Example response**

```shell
{
  "currency_details": {
    "usd": {
      "currency_code": "usd",
      "currency_factor": 100,
      "currency_number": 840,
      "currency_places": 2,
      "currency_post_symbol": "",
      "currency_pre_symbol": "$"
    }
  },
  "events_by_id": {
    "6L9": {
      "event": {
        "city_code": "las_vegas-us",
        "city_desc": "Las Vegas",
        "classes": {
          "circuscabaret": "Circus, Cabaret & Variety"
        },
        "cost_range": {
          "best_value_offer": {
            "absolute_saving": 9,
            "full_seatprice": 75,
            "full_surcharge": 5,
            "offer_seatprice": 68,
            "offer_surcharge": 3,
            "percentage_saving": 11
          },
          "max_saving_offer": {
            "absolute_saving": 9,
            "full_seatprice": 75,
            "full_surcharge": 5,
            "offer_seatprice": 68,
            "offer_surcharge": 3,
            "percentage_saving": 11
          },
          "max_seatprice": 68,
          "max_surcharge": 3,
          "min_cost_offer": {
            "absolute_saving": 9,
            "full_seatprice": 75,
            "full_surcharge": 5,
            "offer_seatprice": 68,
            "offer_surcharge": 3,
            "percentage_saving": 11
          },
          "min_seatprice": 68,
          "min_surcharge": 3,
          "range_currency_code": "usd",
          "top_price_offer": {
            "absolute_saving": 9,
            "full_seatprice": 75,
            "full_surcharge": 5,
            "offer_seatprice": 68,
            "offer_surcharge": 3,
            "percentage_saving": 11
          },
          "valid_quantities": [
            1,
            2,
            3,
            4
          ]
        },
        "country_code": "us",
        "country_desc": "United States of America",
        "custom_filter": [],
        "event_desc": "La Femme",
        "event_id": "6L9",
        "event_path": "/6L9-la-femme/",
        "event_status": "live",
        "event_type": "simple_ticket",
        "event_uri_desc": "La-Femme",
        "geo_data": {
          "latitude": 36.1034,
          "longitude": -115.1721
        },
        "has_no_perfs": false,
        "is_seated": true,
        "max_running_time": 120,
        "min_running_time": 90,
        "need_departure_date": false,
        "need_duration": false,
        "need_performance": true,
        "postcode": "NV89109",
        "show_perf_time": true,
        "source_code": "ext_test1",
        "source_desc": "External Test Backend 1",
        "venue_desc": "MGM Grand Las Vegas",
        "venue_uri_desc": "MGM-Grand-Las-Vegas"
      },
      "valid_quantities": [
        1,
        2,
        3,
        4
      ],
      "venue_is_enforced": true
    }
  }
}
```

```python
from pyticketswitch.cost_range import CostRange
from pyticketswitch.event import Event

{
    '6IF': Event(
        id='6IF',
        status='live',
        description="Matthew Bourne's Nutcracker TEST",
        source='External Test Backend 0',
        source_code='ext_test0',
        event_type='simple_ticket',
        venue="Sadler's Wells",
        classes={
            'dance': 'Ballet & Dance'
        },
        postcode='EC1R 4TN',
        city='London',
        city_code='london-uk',
        country='United Kingdom',
        country_code='uk',
        latitude=51.52961137,
        longitude=-0.10601562,
        max_running_time=120,
        min_running_time=120,
        show_performance_time=True,
        has_performances=True,
        is_seated=True,
        needs_departure_date=False,
        needs_duration=False,
        needs_performance=False,
        upsell_list=[
            '6IE',
            'MH0'
        ],
        cost_range=CostRange(
            valid_quantities=[
                1,
                2,
                3,
                4,
                5,
                6
            ],
            max_seatprice=47.0,
            max_surcharge=5.0,
            min_seatprice=18.0,
            min_surcharge=3.0,
            currency='gbp',
        ),
        no_singles_cost_range=CostRange(
            valid_quantities=[
                1,
                2,
                3,
                4,
                5,
                6
            ],
            max_seatprice=47.0,
            max_surcharge=5.0,
            min_seatprice=18.0,
            min_surcharge=3.0,
            currency='gbp',
        ),
        critic_review_percent=100,
    )
}
```

<aside class="notice">In the UK market, offers are typically classified as 
either "discounted face value" or "no booking fee". Discounted face value 
offers have an offer seatprice that is lower than the full price seatprice (and
normally they have an offer surcharge of zero). No booking fee offers have
an offer seatprice equal to the full price seatprice, and an offer surcharge
equal to zero. The standard is to not display a saving percentage for no booking
fee offers (some suppliers do not like their offers to show as percentage 
discounts), and instead display "No fees" or similar.</aside>

Each event returned includes a `cost_range` object with the following attributes:

Attribute | Description
--------- | -----------
`best_value_offer` | The offer with the highest percentage saving. This is the most commonly used offer cost range.
`max_saving_offer` | The offer with the highest absolute saving.
`max_seatprice` | The per-ticket face value for the highest price (seatprice + surcharge)
`max_surcharge` | The per-ticket booking fee for the highest price (seatprice + surcharge)
`min_cost_offer` | The offer with the lowest cost.
`min_seatprice` | The per-ticket face value for the lowest price (seatprice + surcharge)
`min_surcharge` | The per-ticket booking fee for the lowest price (seatprice + surcharge)
`no_singles_cost_range` | This returns another cost range object that excludes availability with only one consecutive seat available. The prices in this cost range will therefore be the same as or higher than the outer cost range.
`range_currency_code` | The currency code for the cost range - further detail for the currency can be found in the `currency_details` object, described below.
`top_price_offer` | The offer with the highest cost. This is the least used offer cost range.
`valid_quantities` | An array of ticket quantities with availability.


The **offer** objects contain the following attributes:

Attribute | Description
--------- | -----------
`absolute_saving` | Defined as (`full_seatprice` + `full_surcharge`) - (`offer_seatprice` + `offer_surcharge`).
`full_seatprice` | The non-offer per-ticket face value.
`full_surcharge` | The non-offer per-ticket booking fee.
`offer_seatprice` | The offer per-ticket face value.
`offer_surcharge` | The offer per-ticket booking fee.
`percentage_saving` | Defined as `absolute_saving` / (`full_seatprice` + `full_surcharge`) * 100.


The outer object includes a **currency_details** object containing one currency
object (indexed on the currency code) for every currency referenced in the
JSON response. Each currency has the following attributes:

Attribute | Description
--------- | -----------
`currency_code` | [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217) three letter code
`currency_factor` | Multiply by this number to get values in the base unit (e.g. multiplying $47.11 by the currency_factor will give 4711 cents)
`currency_number` | [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217) numeric identifier
`currency_places` | The number of decimal places to display (eg 45.5 usd should be displayed as 45.50)
`currency_post_symbol` | A symbol to display at the end of the price
`currency_pre_symbol` | A symbol to display in front of the price


## Cost range details

Cost range details presents a cost range for every available price band. They
allow you for example display a list of price bands on a show page, before the
customer begins to choose their tickets. This is not a commonly used feature.

A list of ticket types are returned; nested within each ticket type are the
available price bands; nested within each price band is a 
[cost range](#cost-range). Cost range details are only ever returned as part of 
the parent event object.


### Request

> **Example request**

```shell
curl https://demo.ticketswitch.com/f13/events_by_id.v1 \
    -u "demo:demopass" \
    -d "event_id_list=6IF" \
    -d "req_cost_range_details" \
    --compressed \
    -G
```

```python
from pyticketickswitch import client

client = Client('demo', 'demopass')
events, meta = client.get_events(['6IF'], cost_range_details=True)
```

`req_cost_range_details` is the main parameter that switches on cost range details.
The other parameters listed can also be included to return additional cost 
range data.


Parameter | Description
--------- | -----------
`req_cost_range_details` | Returns a list of unique ticket types and price bands and their cost ranges across all performances
`req_cost_range_best_value_offer` | Returns the offer with the highest percentage saving. This is the most commonly used offer cost range.
`req_cost_range_max_saving_offer` | Returns the offer with the highest absolute saving.
`req_cost_range_min_cost_offer` | Returns the offer with the lowest cost.
`req_cost_range_top_price_offer` | Returns the offer with the highest cost. This is the least used offer cost range.
`req_cost_range_no_singles_data` | This returns another cost range object that excludes availability with only one consecutive seat available. The prices in this cost range will therefore be the same or higher than the outer cost range. It has the same structure as the main cost range (so if you want to see the "best value offer" in the no singles data, you need to add `req_cost_range_best_value_offer` and you will see this data in both cost ranges).


### Response

> **Example response**

```shell
{
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
  "events_by_id": {
    "6IF": {
      "event": {
        "city_code": "london-uk",
        "city_desc": "London",
        "classes": {
          "dance": "Ballet & Dance"
        },
        "cost_range_details": {
          "ticket_type": [
            {
              "price_band": [
                {
                  "cost_range": {
                    "max_seatprice": 47,
                    "max_surcharge": 5,
                    "min_seatprice": 47,
                    "min_surcharge": 5,
                    "range_currency_code": "gbp",
                    "valid_quantities": [
                      1,
                      2,
                      3,
                      4,
                      5,
                      6
                    ]
                  },
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
                  "cost_range": {
                    "max_seatprice": 35,
                    "max_surcharge": 4,
                    "min_seatprice": 35,
                    "min_surcharge": 4,
                    "range_currency_code": "gbp",
                    "valid_quantities": [
                      1,
                      2,
                      3,
                      4,
                      5,
                      6
                    ]
                  },
                  "price_band_code": "A",
                  "price_band_desc": ""
                },
                {
                  "cost_range": {
                    "max_seatprice": 30,
                    "max_surcharge": 4,
                    "min_seatprice": 30,
                    "min_surcharge": 4,
                    "range_currency_code": "gbp",
                    "valid_quantities": [
                      1,
                      2,
                      3,
                      4,
                      5,
                      6
                    ]
                  },
                  "price_band_code": "B",
                  "price_band_desc": ""
                },
                {
                  "cost_range": {
                    "max_seatprice": 25,
                    "max_surcharge": 4,
                    "min_seatprice": 25,
                    "min_surcharge": 4,
                    "range_currency_code": "gbp",
                    "valid_quantities": [
                      1,
                      2,
                      3,
                      4,
                      5,
                      6
                    ]
                  },
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
                  "cost_range": {
                    "max_seatprice": 21,
                    "max_surcharge": 3,
                    "min_seatprice": 21,
                    "min_surcharge": 3,
                    "range_currency_code": "gbp",
                    "valid_quantities": [
                      1,
                      2,
                      3,
                      4,
                      5,
                      6
                    ]
                  },
                  "price_band_code": "A",
                  "price_band_desc": ""
                },
                {
                  "cost_range": {
                    "max_seatprice": 18,
                    "max_surcharge": 3,
                    "min_seatprice": 18,
                    "min_surcharge": 3,
                    "range_currency_code": "gbp",
                    "valid_quantities": [
                      1,
                      2,
                      3
                    ]
                  },
                  "price_band_code": "B",
                  "price_band_desc": ""
                }
              ],
              "ticket_type_code": "STALLS",
              "ticket_type_desc": "Stalls"
            }
          ]
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
            "6IE",
            "MH0"
          ]
        },
        "event_uri_desc": "Matthew-Bourne%27s-Nutcracker-TEST",
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
        "venue_desc": "Sadler's Wells",
        "venue_uri_desc": "Sadler%27s-Wells"
      },
      "valid_quantities": [
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "venue_is_enforced": true
    }
  }
}
```

```python
from pyticketswitch.event import Event
from pyticketswitch.ticket_type import TicketType
from pyticketswitch.discount import Discount
from pyticketswitch.cost_range import CostRange
from pyticketswitch.price_band import PriceBand

{
    '6IF': Event(
        id='6IF',
        status='live',
        description="Matthew Bourne's Nutcracker TEST",
        source='External Test Backend 0',
        source_code='ext_test0',
        event_type='simple_ticket',
        venue="Sadler's Wells",
        classes={
            'dance': 'Ballet & Dance'
        },
        postcode='EC1R 4TN',
        city='London',
        city_code='london-uk',
        country='United Kingdom',
        country_code='uk',
        latitude=51.52961137,
        longitude=-0.10601562,
        max_running_time=120,
        min_running_time=120,
        show_performance_time=True,
        has_performances=True,
        is_seated=True,
        needs_departure_date=False,
        needs_duration=False,
        needs_performance=False,
        upsell_list=[
            '6IE',
            'MH0'
        ],
        cost_range_details=[
            TicketType(
                code='BALCONY',
                description='Balcony',
                price_bands=[
                    PriceBand(
                        code='A',
                        description='',
                        cost_range=CostRange(
                            valid_quantities=[
                                1,
                                2,
                                3,
                                4,
                                5,
                                6
                            ],
                            max_seatprice=47.0,
                            max_surcharge=5.0,
                            min_seatprice=47.0,
                            min_surcharge=5.0,
                            currency='gbp',
                        ),
                        allows_leaving_single_seats='always',
                        example_seats_are_real=True,
                    )
                ],
            ),
            TicketType(
                code='CIRCLE',
                description='Upper circle',
                price_bands=[
                    PriceBand(
                        code='A',
                        description='',
                        cost_range=CostRange(
                            valid_quantities=[
                                1,
                                2,
                                3,
                                4,
                                5,
                                6
                            ],
                            max_seatprice=35.0,
                            max_surcharge=4.0,
                            min_seatprice=35.0,
                            min_surcharge=4.0,
                            currency='gbp',
                        ),
                        allows_leaving_single_seats='always',
                        example_seats_are_real=True,
                    ),
                    PriceBand(
                        code='B',
                        description='',
                        cost_range=CostRange(
                            valid_quantities=[
                                1,
                                2,
                                3,
                                4,
                                5,
                                6
                            ],
                            max_seatprice=30.0,
                            max_surcharge=4.0,
                            min_seatprice=30.0,
                            min_surcharge=4.0,
                            currency='gbp',
                        ),
                        allows_leaving_single_seats='always',
                        example_seats_are_real=True,
                    ),
                    PriceBand(
                        code='C',
                        description='',
                        cost_range=CostRange(
                            valid_quantities=[
                                1,
                                2,
                                3,
                                4,
                                5,
                                6
                            ],
                            max_seatprice=25.0,
                            max_surcharge=4.0,
                            min_seatprice=25.0,
                            min_surcharge=4.0,
                            currency='gbp',
                        ),
                        allows_leaving_single_seats='always',
                        example_seats_are_real=True,
                    )
                ],
            ),
            TicketType(
                code='STALLS',
                description='Stalls',
                price_bands=[
                    PriceBand(
                        code='A',
                        description='',
                        cost_range=CostRange(
                            valid_quantities=[
                                1,
                                2,
                                3,
                                4,
                                5,
                                6
                            ],
                            max_seatprice=21.0,
                            max_surcharge=3.0,
                            min_seatprice=21.0,
                            min_surcharge=3.0,
                            currency='gbp',
                        ),
                        allows_leaving_single_seats='always',
                        example_seats_are_real=True,
                    ),
                    PriceBand(
                        code='B',
                        description='',
                        cost_range=CostRange(
                            valid_quantities=[
                                1,
                                2,
                                3
                            ],
                            max_seatprice=18.0,
                            max_surcharge=3.0,
                            min_seatprice=18.0,
                            min_surcharge=3.0,
                            currency='gbp',
                        ),
                        allows_leaving_single_seats='always',
                        example_seats_are_real=True,
                    )
                ],
            )
        ],
        critic_review_percent=100,
    )
}
```


The `cost_range_details` object includes an array of **ticket types** that 
describe a part of house or location within the venue.

Attribute | Description
--------- | -----------
`ticket_type_code` | the identifier of the ticket type, this can be used later in the [trolley](#trolley) or [reserve](#reserve) resource.
`ticket_type_desc` | A human readable description of the price band if applicable
`price_band` | an array of price bands


**Price bands** describe the different levels of pricing that are available within a
ticket type. It's important to note that a price band may not have a long term
set price, and as such ticket prices and surcharges might change on a performance to
performance basis. 

Attribute | Description
--------- | -----------
`price_band_code` | the identifier of the price band, this can be used later in the [trolley](#trolley) or [reserve](#reserve) resource.
`price_band_desc` | A human readable description of the price band if applicable
`cost_range` | The cost range for this price band.


The **cost range** has the following attributes:

Attribute | Description
--------- | -----------
`best_value_offer` | The offer with the highest percentage saving. This is the most commonly used offer cost range.
`max_saving_offer` | The offer with the highest absolute saving.
`max_seatprice` | The per-ticket face value for the highest price (seatprice + surcharge)
`max_surcharge` | The per-ticket booking fee for the highest price (seatprice + surcharge)
`min_cost_offer` | The offer with the lowest cost.
`min_seatprice` | The per-ticket face value for the lowest price (seatprice + surcharge)
`min_surcharge` | The per-ticket booking fee for the lowest price (seatprice + surcharge)
`no_singles_cost_range` | This returns another cost range object that excludes availability with only one consecutive seat available. The prices in this cost range will therefore be the same as or higher than the outer cost range.
`quantity_options` | The ticket quantities that have availability.
`range_currency_code` |  The currency code for the cost range - further detail for the currency can be found in the `currency_details` object, described below.
`top_price_offer` | The offer with the highest cost. This is the least used offer cost range.


The **offer** objects contain the following attributes:

Attribute | Description
--------- | -----------
`absolute_saving` | Defined as (`full_seatprice` + `full_surcharge`) - (`offer_seatprice` + `offer_surcharge`).
`full_seatprice` | The non-offer per-ticket face value.
`full_surcharge` | The non-offer per-ticket booking fee.
`offer_seatprice` | The offer per-ticket face value.
`offer_surcharge` | The offer per-ticket booking fee.
`percentage_saving` | Defined as `absolute_saving` / (`full_seatprice` + `full_surcharge`) * 100.


The outer object includes a **currency_details** object containing one currency
object (indexed on the currency code) for every currency referenced in the
JSON response. Each currency has the following attributes:

Attribute | Description
--------- | -----------
`currency_code` | [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217) three letter code
`currency_factor` | Multiply by this number to get values in the base unit (e.g. multiplying $47.11 by the currency_factor will give 4711 cents)
`currency_number` | [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217) numeric identifier
`currency_places` | The number of decimal places to display (eg 45.5 usd should be displayed as 45.50)
`currency_post_symbol` | A symbol to display at the end of the price
`currency_pre_symbol` | A symbol to display in front of the price


## Availability details

Availability details are a cached list of the price bands available for an
event across all performances. They are cached from previous availability
requests made via your username. This data can be useful for example if you wish
to display a separate page with pricing detail for all price bands on sale.

Availability details are generated from availability requests made either by
end-users or by scheduled processes that Ingresso use to update this data. You
should not attempt to make multiple availability requests in order to keep this
data up to date - please contact us instead to discuss options
[api@ingresso.co.uk](mailto:api@ingresso.co.uk).

### Request

> **Example request**

```shell
curl https://demo.ticketswitch.com/f13/events_by_id.v1 \
    -u "demo:demopass" \
    -d "event_id_list=6IF" \
    -d "req_avail_details" \
    -d "req_avail_details_with_perfs" \
    --compressed \
    -G
```

```python
from pyticketickswitch import client

client = Client('demo', 'demopass')
events, meta = client.get_events(['6IF'], availability=True, availability_with_performances=True)
```

Parameter | Description
--------- | -----------
`req_avail_details` | Returns a list of unique ticket types and price bands that are available for this event across all performances. **This parameter is not commonly used.**
`req_avail_details_with_perfs` | This will add the list of available performance dates to each avail detail object. **Only valid if used alongside req_avail_details**

### Response

> **Example Response**

```shell
{
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
                      "avail_currency_code": "gbp",
                      "available_dates": {
                        "first_yyyymmdd": "20170309",
                        "last_yyyymmdd": "20170705",
                        "year_2017": {
                          "apr_bitmask": 803192702,
                          "jul_bitmask": 30,
                          "jun_bitmask": 1065287163,
                          "mar_bitmask": 2130574080,
                          "may_bitmask": 2079846367
                        }
                      },
                      "available_weekdays_bitmask": 63,
                      "seatprice": 47,
                      "surcharge": 5,
                      "valid_quantities": [
                        1,
                        2,
                        3,
                        4,
                        5,
                        6
                      ]
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
                      "avail_currency_code": "gbp",
                      "available_dates": {
                        "first_yyyymmdd": "20170309",
                        "last_yyyymmdd": "20170705",
                        "year_2017": {
                          "apr_bitmask": 803192702,
                          "jul_bitmask": 30,
                          "jun_bitmask": 1065287163,
                          "mar_bitmask": 2130574080,
                          "may_bitmask": 2079846367
                        }
                      },
                      "available_weekdays_bitmask": 63,
                      "seatprice": 35,
                      "surcharge": 4,
                      "valid_quantities": [
                        1,
                        2,
                        3,
                        4,
                        5,
                        6
                      ]
                    }
                  ],
                  "price_band_code": "A",
                  "price_band_desc": ""
                },
                {
                  "avail_detail": [
                    {
                      "avail_currency_code": "gbp",
                      "available_dates": {
                        "first_yyyymmdd": "20170309",
                        "last_yyyymmdd": "20170705",
                        "year_2017": {
                          "apr_bitmask": 803192702,
                          "jul_bitmask": 30,
                          "jun_bitmask": 1065287163,
                          "mar_bitmask": 2130574080,
                          "may_bitmask": 2079846367
                        }
                      },
                      "available_weekdays_bitmask": 63,
                      "seatprice": 30,
                      "surcharge": 4,
                      "valid_quantities": [
                        1,
                        2,
                        3,
                        4,
                        5,
                        6
                      ]
                    }
                  ],
                  "price_band_code": "B",
                  "price_band_desc": ""
                },
                {
                  "avail_detail": [
                    {
                      "avail_currency_code": "gbp",
                      "available_dates": {
                        "first_yyyymmdd": "20170309",
                        "last_yyyymmdd": "20170705",
                        "year_2017": {
                          "apr_bitmask": 803192702,
                          "jul_bitmask": 30,
                          "jun_bitmask": 1065287163,
                          "mar_bitmask": 2130574080,
                          "may_bitmask": 2079846367
                        }
                      },
                      "available_weekdays_bitmask": 63,
                      "seatprice": 25,
                      "surcharge": 4,
                      "valid_quantities": [
                        1,
                        2,
                        3,
                        4,
                        5,
                        6
                      ]
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
                      "avail_currency_code": "gbp",
                      "available_dates": {
                        "first_yyyymmdd": "20170309",
                        "last_yyyymmdd": "20170705",
                        "year_2017": {
                          "apr_bitmask": 803192702,
                          "jul_bitmask": 30,
                          "jun_bitmask": 1065287163,
                          "mar_bitmask": 2130574080,
                          "may_bitmask": 2079846367
                        }
                      },
                      "available_weekdays_bitmask": 63,
                      "seatprice": 21,
                      "surcharge": 3,
                      "valid_quantities": [
                        1,
                        2,
                        3,
                        4,
                        5,
                        6
                      ]
                    }
                  ],
                  "price_band_code": "A",
                  "price_band_desc": ""
                },
                {
                  "avail_detail": [
                    {
                      "avail_currency_code": "gbp",
                      "available_dates": {
                        "first_yyyymmdd": "20170309",
                        "last_yyyymmdd": "20170705",
                        "year_2017": {
                          "apr_bitmask": 803192702,
                          "jul_bitmask": 30,
                          "jun_bitmask": 1065287163,
                          "mar_bitmask": 2130574080,
                          "may_bitmask": 2079846367
                        }
                      },
                      "available_weekdays_bitmask": 63,
                      "seatprice": 18,
                      "surcharge": 3,
                      "valid_quantities": [
                        1,
                        2,
                        3
                      ]
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
            "6IE",
            "MH0"
          ]
        },
        "event_uri_desc": "Matthew-Bourne%27s-Nutcracker-TEST",
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
        "venue_desc": "Sadler's Wells",
        "venue_uri_desc": "Sadler%27s-Wells"
      },
      "valid_quantities": [
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "venue_is_enforced": true
    }
  }
}
```

```python
from pyticketswitch.availability import AvailabilityDetails
from pyticketswitch.event import Event

{
    '6IF': Event(
        id='6IF',
        status='live',
        description="Matthew Bourne's Nutcracker TEST",
        source='External Test Backend 0',
        source_code='ext_test0',
        event_type='simple_ticket',
        venue="Sadler's Wells",
        classes={
            'dance': 'Ballet & Dance'
        },
        postcode='EC1R 4TN',
        city='London',
        city_code='london-uk',
        country='United Kingdom',
        country_code='uk',
        latitude=51.52961137,
        longitude=-0.10601562,
        max_running_time=120,
        min_running_time=120,
        show_performance_time=True,
        has_performances=True,
        is_seated=True,
        needs_departure_date=False,
        needs_duration=False,
        needs_performance=False,
        upsell_list=[
            '6IE',
            'MH0'
        ],
        critic_review_percent=100,
        availability_details=[
            AvailabilityDetails(
                ticket_type='BALCONY',
                ticket_type_description='Balcony',
                price_band='A',
                price_band_description='',
                seatprice=47.0,
                surcharge=5.0,
                full_seatprice=0.0,
                full_surcharge=0.0,
                percentage_saving=0.0,
                absolute_saving=0.0,
                currency='gbp',
                first_date=datetime.date(2017, 5, 3),
                last_date=datetime.date(2017, 8, 30),
            ),
            AvailabilityDetails(
                ticket_type='STALLS',
                ticket_type_description='Stalls',
                price_band='B',
                price_band_description='',
                seatprice=18.0,
                surcharge=3.0,
                full_seatprice=0.0,
                full_surcharge=0.0,
                percentage_saving=0.0,
                absolute_saving=0.0,
                currency='gbp',
                first_date=datetime.date(2017, 5, 3),
                last_date=datetime.date(2017, 8, 30),
            ),
            AvailabilityDetails(
                ticket_type='STALLS',
                ticket_type_description='Stalls',
                price_band='A',
                price_band_description='',
                seatprice=21.0,
                surcharge=3.0,
                full_seatprice=0.0,
                full_surcharge=0.0,
                percentage_saving=0.0,
                absolute_saving=0.0,
                currency='gbp',
                first_date=datetime.date(2017, 5, 3),
                last_date=datetime.date(2017, 8, 30),
            ),
            AvailabilityDetails(
                ticket_type='CIRCLE',
                ticket_type_description='Upper circle',
                price_band='C',
                price_band_description='',
                seatprice=25.0,
                surcharge=4.0,
                full_seatprice=0.0,
                full_surcharge=0.0,
                percentage_saving=0.0,
                absolute_saving=0.0,
                currency='gbp',
                first_date=datetime.date(2017, 5, 3),
                last_date=datetime.date(2017, 8, 30),
            ),
            AvailabilityDetails(
                ticket_type='CIRCLE',
                ticket_type_description='Upper circle',
                price_band='B',
                price_band_description='',
                seatprice=30.0,
                surcharge=4.0,
                full_seatprice=0.0,
                full_surcharge=0.0,
                percentage_saving=0.0,
                absolute_saving=0.0,
                currency='gbp',
                first_date=datetime.date(2017, 5, 3),
                last_date=datetime.date(2017, 8, 30),
            ),
            AvailabilityDetails(
                ticket_type='CIRCLE',
                ticket_type_description='Upper circle',
                price_band='A',
                price_band_description='',
                seatprice=35.0,
                surcharge=4.0,
                full_seatprice=0.0,
                full_surcharge=0.0,
                percentage_saving=0.0,
                absolute_saving=0.0,
                currency='gbp',
                first_date=datetime.date(2017, 5, 3),
                last_date=datetime.date(2017, 8, 30),
            )
        ],
    )

```

**Ticket types** describe a part of house or location within the venue.

Attribute | Description
--------- | -----------
`ticket_type_code` | The identifier of the ticket type, this can be used later in the [trolley](#trolley) or [reserve](#reserve) resource.
`ticket_type_desc` | A human readable description of the price band if applicable.
`price_band` | A list of price bands.


**Price bands** describe the different levels of pricing that are available within a
ticket type. It's important to note that a price band may not have a long term
set price, and as such ticket prices and surcharges might change on a performance to
performance basis. 

Attribute | Description
--------- | -----------
`price_band_code` | The identifier of the price band, this can be used later in the [trolley](#trolley) or [reserve](#reserve) resource.
`price_band_desc` | A human readable description of the price band if applicable.
`avail_details` | A list of the prices in this price band and when they are available.


The **avail details** indicate which prices we have seen and when they are
expected to be available. If the `req_avail_details_with_perfs` parameter was
included  then the availability details will include information on what
performances where available with that price.

Attribute | Description
--------- | -----------
`avail_currency_code` | The currency code for the price - further detail for the currency can be found in the `currency_details` object, described below.
`available_dates` | `first_yyyymmdd` and `last_yyyymmdd` for the range, and year_YYYY includes a bitmask for each month of the year (if, for example, the `jan_bitmask` is 1959 (i.e. "11110100111") this means there is availability on the following days of the month only: 1,2,3,6,8,9,10,11).
`available_weekdays_bitmask` | The days of the week where we have seen availability - see the example in the [bitmask fields](#bitmask-fields) explanation.
`seatprice` | The per-ticket face value.
`surcharge` | The per-ticket booking fee.
`valid_quantities` | An array of available quantities we have seen for this price band.


The outer object includes a **currency_details** object containing one currency
object (indexed on the currency code) for every currency referenced in the
JSON response. Each currency has the following attributes:

Attribute | Description
--------- | -----------
`currency_code` | [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217) three letter code
`currency_factor` | Multiply by this number to get values in the base unit (e.g. multiplying $47.11 by the currency_factor will give 4711 cents)
`currency_number` | [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217) numeric identifier
`currency_places` | The number of decimal places to display (eg 45.5 usd should be displayed as 45.50)
`currency_post_symbol` | A symbol to display at the end of the price
`currency_pre_symbol` | A symbol to display in front of the price
