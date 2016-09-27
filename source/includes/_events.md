# Events

This section describes:

- the [event object](#event-object)
- the API call to [retrieve information about an individual event](#retrieve-an-event)
- the API call to [list all events or search for events](#list-all-events-or-search-for-events)

## Event object

> **Example Response**

```json
{
  "city_desc": "London",
  "class": [
    {
      "class_desc": "Arts & Culture"
    }
  ],
  "country_code": "uk",
  "country_desc": "United Kingdom",
  "critic_review_percent": 100,
  "custom_filter": [],
  "date_range_end": {
    "date_desc": "Fri, 9th December 2016",
    "iso8601_date_and_time": "2016-12-09T19:30:00+01:00"
  },
  "date_range_start": {
    "date_desc": "Fri, 12th August 2016",
    "iso8601_date_and_time": "2016-08-12T19:30:00+01:00"
  },
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
  "source_desc": "External Test Backend 0",
  "user_review_percent": 100,
  "venue_desc": "Sadler's Wells"
}
```

An event object describes an event, and its associated venue, within the system. It is possible to request a number of additional attributes for an event - the default attributes are listed first:


Attribute | Description
--------- | -----------
`city_desc` | Name of the city where the event is taking place
`class` | Array of class or category objects.
`country_code` | ISO3166 county code for the above country
`country_desc` | Name of the country where the event is taking place
`critic_review_percent` | The aggregate critic review score, e.g. `80` for 80%.
`custom_filter` | Array of custom filter codes. (TODO can we remove this?)
`date_range_start` | A [date/time object](#date-time-object) for the start of the run
`date_range_end` | A [date/time object](#date-time-object) for the end of the run
`event_desc` | Name of the show or event e.g. `The Lion King`
`event_id` | Unique identifier for the event
`event_path` | If you also use a white label website this can be used to navigate to the event page, e.g. `/2J5V-la-pedrera-skip-the-line/`
`event_status` | Will be `live` for a normal event search. Other values are `dead` and `pending` but these will only be displayed when using the `include_dead` or `include_non_live` parameters.
`event_type` | Currently all events are of type `simple_ticket`. In future we may add `hotel_room` and `misc_item`.
`event_upsell_list` | A list of event IDs that can be presented as an upsell option to customers (not always present)
`geo_data` | A block containing the following geo co-ordinates:
`geo_data.latitude` | Latitude of the event
`geo_data.longitude` | Longitude of the event
`has_no_perfs` | `true` if the event has no performances. For example some attraction tickets are valid for any date, so we do not present a list of performances to select.
`is_seated` | `true` for seated events
`min_running_time` | Minimum length / duration in minutes (not always present)
`max_running_time` | Maximum length / duration in minutes (not always present)
`need_departure_date` | Flag indicating whether the event needs a departure date specified. This is `false` for most events. (TODO more detail needed)
`need_duration` | Flag indicating whether the event needs duration (specific to `hotel_room` events only)
`need_performance` | Flag indicating if a performance must be selected in order to retrieve availability
`postcode` | Postcode of the event location
`show_perf_time` | `false` if the performance time is not relevant, for example some events use a performance description rather than specific times
`source_desc` | Despription of the source supplier e.g. `Nimax`
`user_review_percent` | The aggregate user review score, e.g. `80` for 80%.
`venue_desc` | Name of the venue e.g. `Sadler's Wells`



These are the optional attributes - these are only returned if you add one of the `req_` parameters to your request.

Attribute | Description
--------- | -----------
`avail_details` | A list of [avail details](#avail-detail-object)
`cost_range` | A list of [cost ranges](#cost-range-object)
`cost_range_details` | Return a list of [cost range details](#cost-range-detail-object)
`event_info` | A plain text block combining all of the `structured_info` sections.
`event_info_html` | A single block of HTML combining all of the `structured_info` sections. The section names are also included as H4 headers.
`media` | A list of [media](#media)
`reviews` | A list of [reviews](#review)
`structured_info` | A list of sections providing additional information about the event, such as `overview`, `duration` and `where_do_i_go`. Each section has a section `name`, a plain text `value` for the event, and a `value_html`. This is returned when the `req_extra_info` parameter is present.
`venue_addr` | The venue address in plain text format. This is returned by the req_extra_info parameter.
`venue_addr_html` | The venue address in HTML format. This is returned by the req_extra_info parameter.
`venue_info` | Additional venue information in plain text format. This is sometimes not provided. This is returned by the req_extra_info parameter.
`venue_info_html` | Additional venue information in HTML format. This is sometimes not provided.. This is returned by the req_extra_info parameter.


## Retrieve an event

> **Definition**

```
GET https://api.ticketswitch.com/cgi-bin/json_events_by_id.exe/{username}
```

This call is used to return detail for one or more events by their ID. It returns a list of [event objects](#event-object).

> **Example request**

```shell
curl https://api.ticketswitch.com/cgi-bin/json_events_by_id.exe/demo \
    -d "user_passwd=demopass" \
    -d "event_id_list=6IF" \
    -G
```

> **Example request including all addtional data**

```shell
curl https://api.ticketswitch.com/cgi-bin/json_events_by_id.exe/demo \
    -d "user_passwd=demopass" \
    -d "event_id_list=6IF" \
    -d "req_cost_range" \
    -d "req_cost_range_details" \
    -d "req_extra_info" \
    -d "req_reviews" \
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
    -d "req_avail_details" \
    -d "req_avail_details_with_perfs" \
    -d "req_meta_components" \
    -G
```

### Request

Parameter | Description
--------- | -----------
`event_id_list` | A comma separated list of event IDs e.g. `25DR` for a single event; `1VLG,1YYO,25DR` for multiple events


These parameters can be included to request additional data for each event:

Parameter | Description
--------- | -----------
`req_avail_details` | Returns a list of unique ticket types and price bands that are available for this event across all performances [see detail](#avail-detail-object). This is not commonly used but was originally developed to display the pricing detail for each price band on sale.
`req_avail_details_with_perfs` | This will add the list of available performance dates to each avail detail object. Only valid if used alongside req_avail_details. This is not commonly used but one possible use case is to display a matrix of availability and performance date to show users availability in one view.
`req_cost_range` | Returns [cost ranges](#cost-range-object) for each event (min and max prices, details of offers). This will normally be useful to request.
`req_cost_range_details` | [cost range details](#cost-range-detail-object) for each event (this returns the min and max prices and details of offers for every price band). This is not commonly used.
`req_extra_info` | Returns the descriptive info for the event, returned as individual sections (`structured_info`) or as a single summary (`event_info` / `event_info_html`)
`req_media_triplet_one` | 
`req_media_triplet_two` | 
`req_media_triplet_three` | 
`req_media_triplet_four` | 
`req_media_triplet_five` | 
`req_media_seating_plan` | 
`req_media_square` | 
`req_media_landscape` | 
`req_media_marquee` | 
`req_media_supplier` | (TODO is this needed?)
`req_meta_components` | (TODO)
`req_reviews` | Returns event reviews if available
`req_video_iframe` | Returns video iframe information if available
`req_custom_fields` | *Internal use only*
`req_internal_codes` | *Internal use only*
`req_sale_mode` |  *Internal use only*
`req_src_info` |  *Internal use only*
`req_collect_text` | *Internal use only* (TODO should we just get rid of this?)


These parameters are used to control the output if more than one event is returned:

Parameter | Description
--------- | -----------
`page_len` | Length of a page, default 50
`page_no` | Page number, default 0, ignored if page_len is not present
`s_top` | orders events by the most sales over the last 48 hour period, otherwise orders alphabetically (TODO taken from XML API - is this valid for JSON?)

(TODO: need example of how the paging works)


> **Example response**

```json
{
  "events_by_id": {
    "6IF": {
      "event": {EVENT_OBJECT},
      "quantity_options": {
        "valid_quantity_flags": [
          false,
          true,
          true,
          true,
          true,
          true,
          true
        ]
      },
      "venue_is_enforced": true
    }
  }
}
```

### Response

(TODO describe the output - why is quantity_options and venue_is_enforced returned here? Shouldn't they be returned per event? If necessary link to [quantity options](#quantity-options-object) )


## List all events or search for events

> **Definition**

```
GET https://api.ticketswitch.com/cgi-bin/json_events.exe/{username}
```

This call is used to search for events. It takes filter parameters
and returns a list of [event objects](#event-object). The list is paged to avoid large volumes
of data being accidentally returned.

Typical use cases:

* request all events on a regular basis (e.g. hourly) to add new events to your CMS
* search for events on demand (e.g. if a user searches for 'Lion King' on your website you could call json_events for the keyword 'Lion King')

<aside class="notice">Searching is not case sensitive.</aside>

> **Example request**

```shell
curl https://api.ticketswitch.com/cgi-bin/json_events.exe/demo \
    -d "user_passwd=demopass" \
    -d "s_keys=nutcracker" \
    -d "s_coco=uk" \
    -G
```

### Request

These are the available search / filter parameters; we've listed what we believe are the most important filters first.

Parameter | Description
--------- | -----------
`s_keys` | Space separated list of keywords, e.g. `lion king new york` or `paris tours`
`s_dates` | Date range in the form yyyymmdd:yyyymmdd (both are optional)
`s_coco` | [ISO 3166](http://en.wikipedia.org/wiki/ISO_3166-1) two letter country code
`s_city` | Restrict events to a particular city (TODO do we use a formal type of city code?)
`s_geo` | Geographical location. Restricts events in a circular search area. Three values are needed: latitude and longitude and radius in kilometres.  Exaxmple: `51.52961137:-0.10601562:10`
`s_geo_lat` | Alternative to `s_geo`. Specifies latitude.
`s_geo_long` | Alternative to `s_geo`. Specifies longditude.
`s_geo_rad_km` | Alternative to `s_geo`. Specifies radius from the coordinates
`include_dead` | Include dead events in the results - useful if you want to continue to display an event page after an event dies, for example to help with search engine optimisation
`include_non_live` | Include dead, pending and new events. *Unlikely to be useful for partners*


All other parameters are the same as [json_events_by_id](#retrieve-an-event). 

These parameters can be included to request additional data for each event:

Parameter | Description
--------- | -----------
`req_avail_details` | Returns a list of unique ticket types and price bands that are available for this event across all performances [see detail](#avail-detail-object). This is not commonly used but was originally developed to display the pricing detail for each price band on sale.
`req_avail_details_with_perfs` | This will add the list of available performance dates to each avail detail object. Only valid if used alongside req_avail_details. This is not commonly used but one possible use case is to display a matrix of availability and performance date to show users availability in one view.
`req_cost_range` | Returns [cost ranges](#cost-range-object) for each event (min and max prices, details of offers). This will normally be useful to request.
`req_cost_range_details` | [cost range details](#cost-range-detail-object) for each event (this returns the min and max prices and details of offers for every price band). This is not commonly used.
`req_extra_info` | Returns the descriptive info for the event, returned as individual sections (`structured_info`) or as a single summary (`event_info` / `event_info_html`)
`req_media_triplet_one` | 
`req_media_triplet_two` | 
`req_media_triplet_three` | 
`req_media_triplet_four` | 
`req_media_triplet_five` | 
`req_media_seating_plan` | 
`req_media_square` | 
`req_media_landscape` | 
`req_media_marquee` | 
`req_media_supplier` | (TODO is this needed?)
`req_meta_components` | (TODO)
`req_reviews` | Returns event reviews if available
`req_video_iframe` | Returns video iframe information if available
`req_custom_fields` | *Internal use only*
`req_internal_codes` | *Internal use only*
`req_sale_mode` |  *Internal use only*
`req_src_info` |  *Internal use only*
`req_collect_text` | *Internal use only* (TODO should we just get rid of this?)


These parameters are used to control the output if more than one event is returned:

Parameter | Description
--------- | -----------
`page_len` | Length of a page, default 50
`page_no` | Page number, default 0, ignored if page_len is not present
`s_top` | orders events by the most sales over the last 48 hour period, otherwise orders alphabetically (TODO taken from XML API - is this valid for JSON?)


> **Example response**

```json
{
  "results": {
    "event": [
      {EVENT_OBJECT},
      {EVENT_OBJECT},
      {EVENT_OBJECT},
    ...
    ]
  }
}
```

### Response

<p class="lang-specific shell">
The output JSON contains a `results` object which has an array called
`event` within it containing event objects. Thus the results
are accessed as `result.event[0]`, `result.event[1]` and so on. This is a
common structure for returning array data through the API.
</p>



## Media object

> **Example Response**

```json
"video_iframe": {
  "video_iframe_caption": "",
  "video_iframe_caption_html": "",
  "video_iframe_height": 315,
  "video_iframe_host": "www.youtube.com",
  "video_iframe_path": "/embed/G1JpEHGizk4",
  "video_iframe_supports_http": false,
  "video_iframe_supports_https": true,
  "video_iframe_url_when_insecure": "https://www.youtube.com/embed/G1JpEHGizk4",
  "video_iframe_url_when_secure": "https://www.youtube.com/embed/G1JpEHGizk4",
  "video_iframe_width": 420
}


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
```

(TODO: need to tidy up the example above when the video iframe is merged in with the images)

Attribute | Description
--------- | -----------
`` | 
`` | 
`` | 

(TODO: complete after we have discussed the list of fields)


## Review object

> **Example Response**

```json
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
}
```

Attribute | Description
--------- | -----------
`is_user_review` | `false` for critic reviews; `true` for user reviews
`review_author` | The reviewer - for critic reviews this is normally the name of a newspaper e.g. `The Evening Standard`
`review_body` | The main review content - for critic reviews this is typically a quote from the review
`review_date_desc` | Review date
`review_iso8601_date_and_time` | review date and time in ISO 8601 format
`review_lang` | Language code of the review (TODO what ISO etc is this?)
`review_original_url` | For critic reviews we sometimes record the original source of the review quote
`review_time_desc` | Review time
`review_title` | Review title, not always present
`star_rating` | A rating between 1 and 5 (5 is best)
