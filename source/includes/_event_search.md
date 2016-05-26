##Event Search

Returns a list of available events based on a search criteria. 

This can either be called on a regular basis (e.g. hourly) to populate your own CMS, or you can call it to search for events on demand (e.g. if a user searches for 'Lion King' on your website you call event_search for the keyword 'Lion King').


> **Request**

```http
POST https://api.ticketswitch.com/tickets/xml_core.exe HTTP/1.1
User-Agent: MyClient/1.0.0
Content-Type: text/xml
Accept: text/xml

<event_search>
  <user_id>demo</user_id>
  <user_passwd>demopass</user_passwd>
  <s_keys>nutcracker</s_keys>
  <request_media>triplet_one</request_media>
  <request_cost_range/>
</event_search>
```

###Request

**Required Parameters**

The event search must include either a crypto block or a user password. In
either case a user id must be provided

Parameter | Description
--------- | -----------
`user_id` | Client API username
`user_passwd` | *Either* Client API password
`crypto_block` | *Or* Crypto block from [Start Session](#start-session) call.  


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
    <subdomain_user_is_bad>no</subdomain_user_is_bad>
    <crypto_block>U_--6-bsjWOjlRgre-sDbJR9OBcf8hK6xqSNfGtNSPBPb61l0yAibpFTgtBbqbYc68Cu5LBPahUSPRPuGVqnX1AJJysvJ9jQzsLLBx6ZMzcxvLjz1NN8f8-zv1ZELdGKR1XbY</crypto_block>
    <distances_are_valid>no</distances_are_valid>
    <event>
        <area_code>EW</area_code>
        <area_desc>Everywhere</area_desc>
        <city_code>london-uk</city_code>
        <city_desc>London</city_desc>
        <class>
            <class_code>arts</class_code>
            <class_desc>Arts &amp; Culture</class_desc>
            <is_main_class>yes</is_main_class>
            <search_key>arts/</search_key>
            <subclass>
                <is_main_subclass>yes</is_main_subclass>
                <search_key>arts/dance</search_key>
                <subclass_code>dance</subclass_code>
                <subclass_desc>Ballet &amp; Dance</subclass_desc>
            </subclass>
        </class>
        <cost_range>
            <max_combined>52.0</max_combined>
            <max_seatprice>47.0</max_seatprice>
            <max_surcharge>5.0</max_surcharge>
            <min_combined>21.0</min_combined>
            <min_seatprice>18.0</min_seatprice>
            <min_surcharge>3.0</min_surcharge>
            <no_singles_cost_range>
                <max_combined>52.0</max_combined>
                <max_seatprice>47.0</max_seatprice>
                <max_surcharge>5.0</max_surcharge>
                <min_combined>21.0</min_combined>
                <min_seatprice>18.0</min_seatprice>
                <min_surcharge>3.0</min_surcharge>
                <quantity_options>
                    <valid_quantity>1</valid_quantity>
                    <valid_quantity>2</valid_quantity>
                    <valid_quantity>3</valid_quantity>
                    <valid_quantity>4</valid_quantity>
                    <valid_quantity>5</valid_quantity>
                    <valid_quantity>6</valid_quantity>
                </quantity_options>
                <range_currency>
                    <currency_code>gbp</currency_code>
                    <currency_factor>100</currency_factor>
                    <currency_number>826</currency_number>
                    <currency_places>2</currency_places>
                    <currency_post_symbol/>
                    <currency_pre_symbol>£</currency_pre_symbol>
                </range_currency>
            </no_singles_cost_range>
            <quantity_options>
                <valid_quantity>1</valid_quantity>
                <valid_quantity>2</valid_quantity>
                <valid_quantity>3</valid_quantity>
                <valid_quantity>4</valid_quantity>
                <valid_quantity>5</valid_quantity>
                <valid_quantity>6</valid_quantity>
            </quantity_options>
            <range_currency>
                <currency_code>gbp</currency_code>
                <currency_factor>100</currency_factor>
                <currency_number>826</currency_number>
                <currency_places>2</currency_places>
                <currency_post_symbol/>
                <currency_pre_symbol>£</currency_pre_symbol>
            </range_currency>
        </cost_range>
        <country_code>uk</country_code>
        <country_desc>United Kingdom</country_desc>
        <critic_review_percent>100</critic_review_percent>
        <date_range_end>
            <date_desc>Fri, 23rd September 2016</date_desc>
            <date_utc_offset>3600</date_utc_offset>
            <date_utc_seconds>1474655400</date_utc_seconds>
            <date_yyyymmdd>20160923</date_yyyymmdd>
            <iso8601_date_and_time>2016-09-23T19:30:00+01:00</iso8601_date_and_time>
        </date_range_end>
        <date_range_start>
            <date_desc>Fri, 27th May 2016</date_desc>
            <date_utc_offset>3600</date_utc_offset>
            <date_utc_seconds>1464373800</date_utc_seconds>
            <date_yyyymmdd>20160527</date_yyyymmdd>
            <iso8601_date_and_time>2016-05-27T19:30:00+01:00</iso8601_date_and_time>
        </date_range_start>
        <event_code>NUTCRACKER</event_code>
        <event_desc>Matthew Bourne's Nutcracker TEST</event_desc>
        <event_id>6IF</event_id>
        <event_lingo>theatre</event_lingo>
        <event_media>
            <caption/>
            <host>d1wx4w35ubmdix.cloudfront.net</host>
            <insecure_complete_url>http://d1wx4w35ubmdix.cloudfront.net/shared/event_media/cropper_cloud/0e/0e255d4f96bd02baa7bfd22fd2cab90c8f6ced34.jpg</insecure_complete_url>
            <name>triplet_one</name>
            <path>/shared/event_media/cropper_cloud/0e/0e255d4f96bd02baa7bfd22fd2cab90c8f6ced34.jpg</path>
            <secure_complete_url>https://d1wx4w35ubmdix.cloudfront.net/shared/event_media/cropper_cloud/0e/0e255d4f96bd02baa7bfd22fd2cab90c8f6ced34.jpg</secure_complete_url>
            <supports_http>yes</supports_http>
            <supports_https>yes</supports_https>
        </event_media>
        <event_path>/6IF-matthew-bourne-s-nutcracker-test/</event_path>
        <event_status>live</event_status>
        <event_subdata/>
        <event_token>6IF</event_token>
        <event_type>simple_ticket</event_type>
        <event_upsell_list>
            <event_id>6IE</event_id>
            <event_id>MH0</event_id>
        </event_upsell_list>
        <geo_data>
            <latitude>51.52961137</latitude>
            <longitude>-0.10601562</longitude>
        </geo_data>
        <has_no_perfs>no</has_no_perfs>
        <is_seated>yes</is_seated>
        <max_running_time>120</max_running_time>
        <min_running_time>120</min_running_time>
        <need_departure_date>no</need_departure_date>
        <need_duration>no</need_duration>
        <need_performance>yes</need_performance>
        <postcode>EC1R 4TN</postcode>
        <search_distance_km>0.0</search_distance_km>
        <show_perf_time>yes</show_perf_time>
        <source_code>ext_test0</source_code>
        <source_desc>External Test Backend 0</source_desc>
        <user_review_percent>100</user_review_percent>
        <venue_code>SADLERSWELLS</venue_code>
        <venue_desc>Sadler's Wells</venue_desc>
    </event>
</event_search_result>
```

###Response

**Response Attributes**

Parameter | Description
--------- | -----------
`subdomain_user_is_bad` | **INTERNAL ONLY**
`crypto_block` | state containing user information
`distances_are_valid` | A flag to indicate whether the search_distance_km return value is valid. This will be **yes** if you have run a geographic search, otherwise it will be **no** (you should then ignore search_distance_km).
`event` | list of events, see below for more information

**Event Attributes**

Parameter | Description
--------- | -----------
`area_code` | the raw area code that the backend system uses to identify what area the event is in. You probably won't need this.
`area_desc` | verbose name of the area
`bigup_value` | **DEPRECATED**
`city_code` | the city that the event is in
`city_desc` | verbose name of the city
`class` | the category of the event, see below for more information
`cost_range` | [Cost Range Attributes](#generic-output)
`country_code` | [ISO 3166-1 country code](http://en.wikipedia.org/wiki/ISO_3166-1)
`country_desc` | verbose name of country
`critic_review_percent` | aggregate review percentage from critics
`date_range_end` | when the event ends, date in number of formats, see below
`date_range_start` | when the event starts, date in number of formats, see below
`event_code` | the raw code that the supplier uses to identify the event. This is only unique in combination with the venue, area and source code. Recommend that you use the event_id instead.
`event_description` | verbose name of event
`event_id` | ticketswitch event id
`event_lingo` | used to descide what terminolgy to display to users. For example a Lingo of `theatre` might use a term such as "Seating Plan" where as a lingo of `themepark` might use a term such as "Park Map".
`event_media` | detail of the event media (images or videos), see below for more information
`event_path` | used internally to link to a white label website
`event_status` | current status of event, possible values are `live`, `new`, `dead`, `pending`.
`event_subdata` | additional information from the backend system that ticketswitch doesn't require, but might be used to identify the event.
`event_token` | ticketswitch event id. Synonymous with `event_id` which is recommended instead of event_token
`event_upsell_list` | list of `event_id` elements that are suggested as target for [upselling](https://en.wikipedia.org/wiki/Upselling)
`geo_data` | coordinates of the event/venue
`has_no_perfs` | will be yes if the event does not support performances, normally will be no
`is_seated` | the event is seated and will provide seat ids after reserveration and/or checkout.
`max_running_time` | the maximum running time across all performances
`min_running_time` | the minimum running time across all performances
`need_departure_date` | indicates that the event will require a departure date to retrieve date time information
`need_duration` | **TO BE COMPLETED**
`need_performance` | indicates that a performance token will need to be supplied in order to reserve and purchase this event
`postcode` | the postcode/zipcode of the event/venue
`search_distance_km` | the distance in kilometers from the requested coordinates.
`show_perf_time` | indicates whether the event should display performance times. For example an all day event should not show performance times, however it would be important to display performance times and differentiate between afternoon and evening shows.
`source_code` | ticketswitch identifier for the backend system that originally supplied the ticket
`source_desc` | verbose name of the backend system
`user_review_percent` | aggregate review percentage from users / customers. As at May 2016 reviews are not being automatically collected
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


**Event Media Attributes**

Parameter | Description
--------- | -----------
`caption` | a caption that can optionally be displayed alongside the media (most media does not have this set)
`host` | the domain name
`insecure_complete_url` | the complete https URL
`name` | the event_media type (square, landscape, triplet_one, triplet_two, triplet_three, triplet_four, triplet_five, marquee, seating_plan)
`path` | the path to append after the domain name
`secure_complete_url` | the complete https URL
`supports_http` | if an http version of the URL is supported
`supports_https` | if an https version of the URL is supported


###Errors

Code | Description
---- | -----------
`1` | the supplied crypto block was not generated by "start\_session"
`2` | the requested "mime\_text\_type" was not a supported value
