# Events

## Event Object

> **Example Response** (TODO need a full event object with all flags switched on)

An event object describes an event, and its associated venue, within
the system. An event contains the following keys:

Parameter | Description
--------- | -----------
`event_id` | The ID used to identify this event in the system (TODO do we use event_id everywhere and never event_token?)
`event_status` | Will be `live` for a normal event search. Other values are 
`event_type` | One of `simple_ticket`, `hotel_room` or `misc_item`
`source_desc` | Despription of the source supplier e.g. `Nimax`
`venue_desc` | Name of the venue e.g. `Sadler's Wells`
`event_desc` | Name of the show or event e.g. `The Lion King`
`postcode` | Postcode of the event location
`class` | Array of class objects.
`custom_filter` | Array of custom filter codes.
`date_range_start` | A [date/time object](#date-time-object) being the start of the run
`date_range_end` | A [date/time object](#date-time-object) being the end of the run
`event_upsell_list` | Hold the list of events to upsell, if any
`city_desc` | Name of the city where the event is taking place
`country_desc` | Name of the country where the event is taking place
`country_code` | ISO3166 county code for the above country
`geo_data` | A block containign geo co-ordinates of the event
`latitude` | Latitude of the event
`longitude` | Longitude of the event
`need_departure_date` | Flag indicating the event needs a departure date
`need_duration` | Flag indicating the event needs duration (hotels)
`need_performance` | Flag indicating if a performance must be selected in order to retrieve availability
`has_no_perfs` | Set to true if the event has no performances
`is_seated` | Set to true if the event is seated
`show_perf_time` | Set to false if the performance date is not relevent
`min_running_time` | Minimum running time in minutes
`max_running_time` | Maximum running time in minutes

(TODO Theres a lot of stuff here which is mainly unnenecssary - if anyone
has any suggestions of things to throw out, or at least not add if they
have their default value (e.g. 'has_no_perfs' is always false really) then
please let me know.)


## List Events

> **Definition**

```curl
GET https://api.ticketswitch.com/cgi-bin/json_events.exe/{username}
```

This call is used to search for events. It takes filter parameters
and returns a list of [event objects](#event-object). The list is paged to avoid large volumes
of data being accidentally returned.

Typical use cases:

request all events on a regular basis (e.g. hourly) to add new events to your CMS
search for events on demand (e.g. if a user searches for 'Lion King' on your website you call event_search for the keyword 'Lion King')
get additional detail for a single event (TODO should we remove the other event call?)

> **Example Request**

```curl
curl https://api.ticketswitch.com/cgi-bin/json_events.exe/demo?
user_passwd=demopass&s_keys=nutcracker&s_coco=uk
```

### Request

The following parameters are used to filter the list of events. These are listed below, with 
what we believe are the most important filter parameters listed first.

Parameter | Description
--------- | -----------

`event_token_list` | A comma separated list of event IDs e.g. `25DR` for a single event or `1VLG,1YYO,25DR` for multiple events (TODO presumably this name is wrong)
`s_keys` | Space separated list of keywords, e.g. `lion king new york` or `paris tours`
`s_dates` | Date range in the form yyyymmdd:yyyymmdd (both are optional)
`s_coco` | [ISO 3166](http://en.wikipedia.org/wiki/ISO_3166-1) two letter country code
`s_city` | Restrict events to a particular city (TODO do we use a formal type of city code?)
`s_geo` | Geographical location. Restricts events in a circular search area. Three values are needed: latitude and longitude and radius in kilometres.  Exaxmple: `51.52961137:-0.10601562:10`
`s_geo_lat` | Alternative to `s_geo`. Specifies latitude.
`s_geo_long` | Alternative to `s_geo`. Specifies longditude.
`s_geo_rad_km` | Alternative to `s_geo`. Specifies radius from the coordinates
`include_dead` | Include dead events in the results - useful if you want to continue to display an event page after an event dies, for example as part of SEO 
`include_non_live` | Include dead, pending and new events. *Unlikely to be useful for partners*

(TODO need to check this list is ok)


These parameters can be included to return additional data for each event:

Parameter | Description
--------- | -----------
`req_cost_range` | returns [cost ranges](#cost-range-object) for each event (min and max prices, details of offers)
`req_cost_range_details` | (TODO ?)
`req_extra_info` | returns the full descriptive info for the event (TODO more detail)
`req_reviews` | returns event reviews if available
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
`req_video_iframe` | returns video iframe information if available
`req_avail_details` | returns a list of ticket types and price bands that are available for this event across all performances
`req_avail_details_with_perfs` | 
`req_meta_components` | (TODO)
`req_custom_fields` | returns any custom fields for events (TODO explain with example what a custom field is)
`req_internal_codes` | *Internal use only*
`req_sale_mode` |  *Internal use only*
`req_src_info` |  *Internal use only*
`req_collect_text` | (TODO can we get rid of this?)


These parameters are used to control the output of the search:

Parameter | Description
--------- | -----------
`s_top` | orders events by the most sales over the last 48 hour period, otherwise orders alphabetically (TODO taken from XML API - is this valid for JSON?)
`page_len` | Length of a page, default 50
`page_no` | Page number, default 0, ignored if page_len is not present


> **Example Response**

```curl
  	"results": {
    		"event": [
      			{ EVENT OBJECT },
      			{ EVENT OBJECT },
      			{ EVENT OBJECT },
			...
		]
	}
```

### Response

<p class="lang-specific curl">
The output JSON contains a `results` object which has an array called
`event` within it containing event objects. Thus the results
are accessed as `result.event[0]`, `result.event[1]` and so on. This is a
common structure for returning array data through the API.
</p>


## Cost Range object

Cost ranges are only ever returned as part of a parent object (such as event). 
They are a cached summary of the pricing that has been seen for your user. They are generated from 
availability requests made either by end-users or by scheduled processes that 
Ingresso use to update cost range data. You should not attempt to make multiple availability
requests in order to keep this data up to date - please contact us instead to discuss options api@ingresso.co.uk.

<aside class="warning">
Cost ranges are not guaranteed to be present so you should design your application with 
this in mind, e.g. by still displaying a performance even if it has no from price.
</aside>



## Date/Time object

I standardised these so that we always outout the ISO 8601 value
as iso8601_date_and_time. As sometimes we represent both a date and
a time, and sometimes only a time, then the descriptive text for the
date is split into 'date_desc' and 'time_desc'. If there is no
'time_desc' then only the date is relevent, despite the fact that the
ISO 8601 variable will always have an associated time. The timezones
should be treated as correct when parsing these objects. An exammple
is below:
(TODO: add this example and fix the paragraph above)