##Event Search

Returns a list of available events based on a search criteria.


> **Request**

```http
POST https://api.ticketswitch.com/tickets/xml_core.exe HTTP/1.1
User-Agent: MyClient/1.0.0
Content-Type: text/xml
Accept: text/xml

<event_search>
<user_id>demo</user_id>
<user_passwd>demo-password</user_passwd>
<s_keys>lion king</s_keys>
</event_search>
```

###Request

**Required Parameters**

The event search must include either a crypto block or a user password. In
either case a user id must be provided

Parameter | Description
--------- | -----------
`user_id` | Client API's username provided by TicketSwitch.
`user_passwd` | Client API's password provided by TicketSwitch.
`crypto_block` | Crypto block from [Start Session](#start-session) call.  


<aside class="warning">
Crypto blocks need to be handled with care. Read more: 
<a href="#handling-crypto-blocks">Handling Crypto Blocks</a>
</aside>

**Optional Parameters**

Parameter | Description
--------- | -----------
`s_keys` | keywords separated by spaces. A keyword search within venue and event description.
`s_dates` | a date range formatted as: *Start Date, End Date* in the format: `YYYYMMDD:YYYYMMDD` (both optional).
`s_coco` | Restrict events per country using [ISO 3166-1 country codes](http://en.wikipedia.org/wiki/ISO_3166-1)
`s_city` | Restrict events per city.
`s_geo` | Geographical location. Restricts events in a circular search area. Three values are needed: latitude and longitude and radius in kilometres.  Exaxmple: `51.52961137:-0.10601562:10`
`s_geo_lat` | Alternative to `s_geo`. Specifies latitude.
`s_geo_long` | Alternative to `s_geo`. Specifies longditude.
`s_geo_rad_km` | Alternative to `s_geo`. Specifies radius from the coordinates
`s_src` | only events from a specific Backend System
`s_area` | only events with an Area
`s_ven` | only events at a Venue
`s_eve` | only events with a specific event code.
`s_top` | orders events by the most sales over the last 48 hour period, otherwise orders alphabetically
`event_token_list` | returns from a list of Event Tokens  ex. `1VLG,1YYO,209C,226B,22AS,25DR,2569`
`request_cost_range` | returns cost ranges for each event (min and max prices, details of offers)
`request_avail_details` | returns a list of ticket types and price bands that are available for this event across all performances
`request_extra_info` | returns full info for the event
`request_custom_fields` | returns any custom fields for events
`request_video_iframe` | returns video iframe information if available
`request_reviews` | returns event reviews if available
`request_media` | returns media if available. valid keys are `square`, `landscape`, `triplet_one`, `triplet_two`, `triplet_three`, `triplet_four`, `triplet_five`, `marquee`, `seating_plan`. (do not question why there are five triplets)

<aside class="warning">Event codes may not be unique by themselves, Read More: <a href="#uniqueness-of-event-ids-tokens">Uniqueness of Event Ids/Tokens</a></aside>
<aside class="notice">Searching is not case sensitive.</aside>
<aside class="success">Lists all available events for the user if no search parameters are provided.</aside>

> **Response**

```http
HTTP/1.1 200 OK
Content-Type: text/xml

<event_search_result>
  <subdomain_user_is_bad>no</subdomain_dduser_is_bad>
  <crypto_block>U_--vkpf9kC9pU5Ob-grofd1tGwcNPYj3ecsj3DY16jQkO0YWnDvmTKhsA3p_xUq7RLFyDc5-Rd1f6KqfuYIqgAFsCrPcyybcxWEO4r_Dm3LIEQyqrEjq89lr2PIn93DkwglY</crypto_block>
  <distances_are_valid>no</distances_are_valid>
  <event>
    <area_code>WE</area_code>
    <area_desc>West End</area_desc>
    <bigup_value>196</bigup_value>
    <city_code>london-uk</city_code>
    <city_desc>London</city_desc>
    <class>
      <class_code>theatre</class_code>
      <class_desc>Theatre</class_desc>
      <is_main_class>yes</is_main_class>
      <search_key>theatre/</search_key>
      <subclass>
        <is_main_subclass>yes</is_main_subclass>
        <search_key>theatre/kids</search_key>
        <subclass_code>kids</subclass_code>
        <subclass_desc>Family</subclass_desc>
      </subclass>
      <subclass>
        <is_main_subclass>no</is_main_subclass>
        <search_key>theatre/musicals</search_key>
        <subclass_code>musicals</subclass_code>
        <subclass_desc>Musicals</subclass_desc>
      </subclass>
    </class>
    <country_code>uk</country_code>
    <country_desc>United Kingdom</country_desc>
    <critic_review_percent>80</critic_review_percent>
    <date_range_end>
      <date_desc>Sun, 4th June 2017</date_desc>
      <date_utc_offset>3600</date_utc_offset>
      <date_utc_seconds>1496583000</date_utc_seconds>
      <date_yyyymmdd>20170604</date_yyyymmdd>
      <iso8601_date_and_time>2017-06-04T14:30:00+01:00</iso8601_date_and_time>
    </date_range_end>
    <date_range_start>
      <date_desc>Thu, 21st April 2016</date_desc>
      <date_utc_offset>3600</date_utc_offset>
      <date_utc_seconds>1461263400</date_utc_seconds>
      <date_yyyymmdd>20160421</date_yyyymmdd>
      <iso8601_date_and_time>2016-04-21T19:30:00+01:00</iso8601_date_and_time>
    </date_range_start>
    <event_code>175</event_code>
    <event_desc>The Lion King</event_desc>
    <event_id>25DR</event_id>
    <event_lingo>theatre</event_lingo>
    <event_status>live</event_status>
    <event_subdata />
    <event_token>25DR</event_token>
    <event_type>simple_ticket</event_type>
    <event_upsell_list>
      <event_id>26G7</event_id>
      <event_id>2GXJ</event_id>
      <event_id>2BPO</event_id>
    </event_upsell_list>
    <geo_data>
      <latitude>51.511784</latitude>
      <longitude>-0.119622</longitude>
    </geo_data>
    <has_no_perfs>no</has_no_perfs>
    <is_seated>yes</is_seated>
    <need_departure_date>no</need_departure_date>
    <need_duration>no</need_duration>
    <need_performance>yes</need_performance>
    <postcode>WC2E 7RQ</postcode>
    <search_distance_km>0.0</search_distance_km>
    <show_perf_time>yes</show_perf_time>
    <source_code>atg_disney</source_code>
    <source_desc>Ambassador Theatre Group</source_desc>
    <venue_code>LYC/T</venue_code>
    <venue_desc>The Lyceum</venue_desc>
  </event>
</event_search_result>
```

###Response

**Response Attributes**

Parameter | Description
--------- | -----------
`subdomain_user_is_bad` | **INTERNAL ONLY**
`crypto_block` | state containing user information
`distance_are_valid` | **CLARIFICATION NEEDED**
`event` | list of events, see below for more information

**Event Attributes**

Parameter | Description
--------- | -----------
`area_code` | the raw area code that the backend system uses to identify what area the event is in
`area_desc` | verbose name of the area
`bigup_value` | **DEPRECATED**
`city_code` | the city that the event is in
`city_desc` | verbose name of the city
`class` | the category of the event, see below for more information
`country_code` | [ISO 3166-1 country code](http://en.wikipedia.org/wiki/ISO_3166-1)
`country_desc` | verbose name of country
`critic_review_percent` | score from critics in range 0-100
`date_range_end` | when the event ends, date in number of formats, see below
`date_range_start` | when the event starts, date in number of formats, see below
`event_code` | the raw code that the supplier uses to identify the event
`event_description` | verbose name of event
`event_id` | ticketswitch event id. Synonymous with `event_token` **WE NEED TO PICK ONE**
`event_lingo` | used to descide what terminolgy to display to users. For example a Lingo of `theater` might use a term such as "Seating Plan" where as a lingo of `themepark` might use a term such as "Park Map".
`event_status` | current status of event, possible values are `live`, `new`, `dead`, `pending`.
`event_subdata` | additional information from the backend system that ticketswitch doesn't require, but might be used to identify the event.
`event_token` | ticketswitch event id. Synonymous with `event_id` **WE NEED TO PICK ONE**
`event_type` | **CLARIFICATION NEEDED**
`event_upsell_list` | list of `event_id` elements that are suggested as target for [upselling](https://en.wikipedia.org/wiki/Upselling) 
`geo_data` | coordinates of the event/venue
`has_no_perfs` | **CLARIFICATION NEEDED**
`is_seated` | the event is seated and will provide seat ids after reserveration and/or checkout.
`need_departure_date` | indicates that the event will require a departure date to retrieve date time information
`need_duration` | **CLARIFICATION NEEDED**
`need_performance` | indicates that a performance token will need to be supplied in order to reserve and purchase this event
`postcode` | the postcode/zipcode of the event/venue
`search_distance_km` | the distance in kilometers from the requested coordinates.
`show_perf_time` | indicates whether the event should display performance times. For example an all day event should not show performance times, however it would be important to display performance times and differentiate between afternoon and evening shows.
`source_code` | ticketswitch identifier for the backend system that originally supplied the ticket
`source_desc` | verbose name of the original supplier
`venue_code` | the raw venue code that the backend system uses to identify what venue the event is in
`venue_desc` | verbose name of the venue.

**Class/Subclass Attributes**

Parameter | Description
--------- | -----------
`[sub]class_code` | ticketswitch identifier for the class
`[sub]class_desc` | verbose name for the class
`is_main_[sub]class` | indicates if the class is the main class/subclass.
`search_key` | a key that can be provided to the `s_classes` parameters of [Event Search](#event-search) to list all events of this class **DEPRECATED**


**Date Range Attributes**

Parameter | Description
--------- | -----------
`date_desc` | verbose version of the date, suitable to display to consumers
`date_utc_offset` | number of seconds the `date_utc_seconds` is offset from UTC
`date_utc_seconds` | number of seconds since the 1st of January 1970
`date_yyyymmdd` | date in the format of `yyyymmdd`
`iso8601_date_and_time` | datetime in the [ISO8601 format](https://en.wikipedia.org/wiki/ISO_8601)

###Errors

Code | Description
---- | -----------
`1` | the supplied crypto block was not generated by "start\_session"
`2` | the requested "mime\_text\_type" was not a supported value
