# Trolley

> **Definition**

```
GET https://api.ticketswitch.com/f13/trolley.v1
```

A `trolley` allows multiple orders to be purchased in a single transaction.

If you only plan to purchase a single order at a time then there is no need to
use a trolley - you should instead go straight to [reserve](#reserve).

The trolley itself should be considered a wish list. No tickets are reserved
until the [reserve](#reserve) call is made. Having a ticket in a trolley is not
a guarantee that the ticket will still be available when you attempt to reserve
the tickets.

Trolleys are identified by a `trolley_token`, which is simply a hash of the
orders in the trolley. Every time a change is made to the trolley the hash
changes and so does the `trolley_token`. An individual `trolley_token` is
therefore an identifier for the current state of your trolley, and can not be
used to identify your trolley as you add or remove orders.


<aside class="warning">We plan to make a breaking change to the way send methods
are handled for integrations that add multiple items to a trolley. If you plan to do this we
can provide more detail if you contact us at techsupport@ingresso.co.uk. If you only plan
to reserve a single item at a time the change will not affect you.</aside>



### Request

> **Example request - adding best available tickets to a new trolley, specifying specific discount codes**

```shell
curl https://api.ticketswitch.com/f13/trolley.v1 \
    -u "demo:demopass" \
    -d "perf_id=6IF-B1S" \
    -d "ticket_type_code=CIRCLE" \
    -d "price_band_code=C/pool" \
    -d "no_of_seats=3" \
    -d "disc0=ADULT" \
    -d "disc1=CHILD" \
    -d "disc2=CHILD" \
    --compressed \
    -G
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
trolley, meta = client.get_trolley(
    performance_id='6IF-B0O',
    number_of_seats=2,
    price_band_code='A/pool',
    ticket_type_code='STALLS'
)
```

> **Example request - adding specific seats to an existing trolley**

```shell
curl https://api.ticketswitch.com/f13/trolley.v1 \
    -u "demo:demopass" \
    -d "trolley_token=eTxD00cZU3ON_j1-lBBoshxPyWY6yaCmb-pMcqlk-jeh1lAc3XnbK8l3eTuhGw9GH9rNfeTuvLcd-NQfLdZxOpibtG3g_4E5ssrtUcZErxA5Er0kcCZHKvKjTPhuRY49j3mWYZivbGGIo9bh7ASnQBNIEkl98SXjzH_hy3w16p95Zb9Tbfat0Iq5CtE7SKFgZhXTaq5zzBPDB5aqhTEAzR87BahqzoIkHRnKylxRZbxYvqZyglaJ5j1HdisF8vDZLUKCb8h4mErsUvOirXXQBLMjglD1RgQQqYGc3bZc6pXrlmOOLWyKiPv97-Nnvu7I8laXEQevemDcJWiz3W-SwV-qhbPQj9NwT3lPWzoHnR_iTT6fAWRex_LTHXCRqs65IzXqsJrLlkzuO5E1k9WQJMpk0Jkj2Zinc3WjWPZZUOBNnAhWk1bSE1FUbd0UVsq0zr8MqX3td5vzwmaiWvgRTFs5MOM2aR6BAfWKmbnV7QLgMJCC-GCP2Rm4mwb5IxyhCWTEaosblz5zDEQuqqnPpWiOtt3qXMIxe0G7UTesRA2b42JIN9tmYc9Pn5x5Apd7MggfkrPX1ulS9kA4JPMkI6U-.Z" \
    -d "perf_id=7AB-5" \
    -d "ticket_type_code=STALLS" \
    -d "price_band_code=A/pool" \
    -d "no_of_seats=2" \
    -d "seat0=A1" \
    -d "seat1=A2" \
    --compressed \
    -G
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
trolley, meta = client.get_trolley(
    trolley_token='PcCSFHBD2pd49k5mYjpoBHjE25O7isjAQkGMqZF15oHLa0W0q-UDnwrAtc6aKmNsOnSBMD77u76gdqqgfJLwnWw6RRKjKJhRHiE0GgG0dN4RYdSTP3Hrw9lix6pj-mZ2m9vLF6tTZAUMTi0HjEc2s9MLo6xu2eVvg4zPKNZ0mr_MllWtiWRc4zMt-RDZy0QEqGb9mcA0GPbNJh3pnRd2Q5_ZgBJ21n8rSgTiKYj02AztAcfF8LcHVTPuldaCgyE_VHxZyW9djxPORTLBiPytBoPURku7bd5kn2w9XeXoHocsODU2yWJpPLrikKqGNHU13uH2LewpbOEnueFi4SVxUxZTG53pN_1tJ-nI3XtGGOHvWvnhode94YB-OJ2xnKy1R6P_OYq1uFH-vRl7fgXNwXdEzfu0yrf-4YLwahQKVUIxrdyswF6XYd-7PoBU3Aj1dDfbEXbm96l8JqKxiHTUM7U-.Z',
    performance_id='7AB-5',
    ticket_type_code='STALLS',
    price_band_code='A/pool',
    number_of_seats=2,
    seats=['A1', 'A2'],
)
```


> **Example request - view the current state of the trolley**

```shell
curl https://api.ticketswitch.com/f13/trolley.v1 \
    -u "demo:demopass" \
    -d "trolley_token=eTxD00cZU3ON_j1-lBBoshxPyWY6yaCmb-pMcqlk-jeh1lAc3XnbK8l3eTuhGw9GH9rNfeTuvLcd-NQfLdZxOpibtG3g_4E5ssrtUcZErxA5Er0kcCZHKvKjTPhuRY49j3mWYZivbGGIo9bh7ASnQBNIEkl98SXjzH_hy3w16p95Zb9Tbfat0Iq5CtE7SKFgZhXTaq5zzBPDB5aqhTEAzR87BahqzoIkHRnKylxRZbxYvqZyglaJ5j1HdisF8vDZLUKCb8h4mErsUvOirXXQBLMjglD1RgQQqYGc3bZc6pXrlmOOLWyKiPv97-Nnvu7I8laXEQevemDcJWiz3W-SwV-qhbPQj9NwT3lPWzoHnR_iTT6fAWRex_LTHXCRqs65IzXqsJrLlkzuO5E1k9WQJMpk0Jkj2Zinc3WjWPZZUOBNnAhWk1bSE1FUbd0UVsq0zr8MqX3td5vzwmaiWvgRTFs5MOM2aR6BAfWKmbnV7QLgMJCC-GCP2Rm4mwb5IxyhCWTEaosblz5zDEQuqqnPpWiOtt3qXMIxe0G7UTesRA2b42JIN9tmYc9Pn5x5Apd7MggfkrPX1ulS9kA4JPMkI6U-.Z" \
    --compressed \
    -G
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
trolley, meta = client.get_trolley(
    token='PcCSFHBD2pd49k5mYjpoBHjE25O7isjAQkGMqZF15oHLa0W0q-UDnwrAtc6aKmNsOnSBMD77u76gdqqgfJLwnWw6RRKjKJhRHiE0GgG0dN4RYdSTP3Hrw9lix6pj-mZ2m9vLF6tTZAUMTi0HjEc2s9MLo6xu2eVvg4zPKNZ0mr_MllWtiWRc4zMt-RDZy0QEqGb9mcA0GPbNJh3pnRd2Q5_ZgBJ21n8rSgTiKYj02AztAcfF8LcHVTPuldaCgyE_VHxZyW9djxPORTLBiPytBoPURku7bd5kn2w9XeXoHocsODU2yWJpPLrikKqGNHU13uH2LewpbOEnueFi4SVxUxZTG53pN_1tJ-nI3XtGGOHvWvnhode94YB-OJ2xnKy1R6P_OYq1uFH-vRl7fgXNwXdEzfu0yrf-4YLwahQKVUIxrdyswF6XYd-7PoBU3Aj1dDfbEXbm96l8JqKxiHTUM7U-.Z',
)
```


> **Example request - removing the first order from the trolley**

```shell
curl https://api.ticketswitch.com/f13/trolley.v1 \
    -u "demo:demopass" \
    -d "trolley_token=eTxD00cZU3ON_j1-lBBoshxPyWY6yaCmb-pMcqlk-jeh1lAc3XnbK8l3eTuhGw9GH9rNfeTuvLcd-NQfLdZxOpibtG3g_4E5ssrtUcZErxA5Er0kcCZHKvKjTPhuRY49j3mWYZivbGGIo9bh7ASnQBNIEkl98SXjzH_hy3w16p95Zb9Tbfat0Iq5CtE7SKFgZhXTaq5zzBPDB5aqhTEAzR87BahqzoIkHRnKylxRZbxYvqZyglaJ5j1HdisF8vDZLUKCb8h4mErsUvOirXXQBLMjglD1RgQQqYGc3bZc6pXrlmOOLWyKiPv97-Nnvu7I8laXEQevemDcJWiz3W-SwV-qhbPQj9NwT3lPWzoHnR_iTT6fAWRex_LTHXCRqs65IzXqsJrLlkzuO5E1k9WQJMpk0Jkj2Zinc3WjWPZZUOBNnAhWk1bSE1FUbd0UVsq0zr8MqX3td5vzwmaiWvgRTFs5MOM2aR6BAfWKmbnV7QLgMJCC-GCP2Rm4mwb5IxyhCWTEaosblz5zDEQuqqnPpWiOtt3qXMIxe0G7UTesRA2b42JIN9tmYc9Pn5x5Apd7MggfkrPX1ulS9kA4JPMkI6U-.Z" \
    -d "remove_items_list=1" \
    --compressed \
    -G
```

```python
from pyticketswitch import Client

client = Client('demo', 'demopass')
trolley, meta = client.get_trolley(
    token='PcCSFHBD2pd49k5mYjpoBHjE25O7isjAQkGMqZF15oHLa0W0q-UDnwrAtc6aKmNsOnSBMD77u76gdqqgfJLwnWw6RRKjKJhRHiE0GgG0dN4RYdSTP3Hrw9lix6pj-mZ2m9vLF6tTZAUMTi0HjEc2s9MLo6xu2eVvg4zPKNZ0mr_MllWtiWRc4zMt-RDZy0QEqGb9mcA0GPbNJh3pnRd2Q5_ZgBJ21n8rSgTiKYj02AztAcfF8LcHVTPuldaCgyE_VHxZyW9djxPORTLBiPytBoPURku7bd5kn2w9XeXoHocsODU2yWJpPLrikKqGNHU13uH2LewpbOEnueFi4SVxUxZTG53pN_1tJ-nI3XtGGOHvWvnhode94YB-OJ2xnKy1R6P_OYq1uFH-vRl7fgXNwXdEzfu0yrf-4YLwahQKVUIxrdyswF6XYd-7PoBU3Aj1dDfbEXbm96l8JqKxiHTUM7U-.Z',
    item_numbers_to_remove=[1, 2, 3],   
)
```

The single trolley call is used for multiple use cases such as:

- Add best available tickets to a new or existing trolley
- Add specific seats to a new or existing trolley
- View the items in the trolley
- Remove an order from the trolley

Note that adding an order will remove anything in the trolley which would
prevent it being added. Removed orders are listed as `discarded_orders`. The
common reasons why two orders cannot sit alongside each other are:

- We currently don't allow a basket to contain multiple orders for the same
  performance. This is something we plan to change - if this is a problem for 
  you please let us know.

- Incompatible despatch methods for the same supplier. For example if you have
  specified "International Post" for one order, and "Collect From Box Office" for
  the next order, and they are both from the same supplier then these are
  incompatible and the first order will be removed from your trolley. This should
  be an uncommon occurrence.

- Differing currencies - for example the first order added to the trolley uses
  the USD currency while the second order added uses the GBP currency. These are
  incompatible so the first order will be removed from your trolley.

Also note that when adding individual seats to a trolley, there are restrictions
on the seats that can be added in a single order:

- If the [availability](#availability) response includes 
  `contiguous_seat_selection_only` = `false` then any seats can be selected, 
  provided they are within a single `ticket_type_code` and `price_band_code`.

- If the [availability](#availability) response includes 
  `contiguous_seat_selection_only` = `true` then only contiguous seats within a 
  single `ticket_type_code` and `price_band_code` can be selected. Seats are 
  contiguous based on the order of the id_details seat array.

If you would like to allow your customers to select seats without restriction
across price bands and ticket types, you need to add multiple orders to a
trolley, one order for each ticket type / price band. However there are
currently some restrictions enforced so if you want to do this you will need to
contact us first techsupport@ingresso.co.uk.


Parameter | Description
--------- | -----------
`discX` | Specify a discount code for ticket number X, with zero-based numbering (so to specify the CHILD discount code on the second ticket use `disc1=CHILD`). Note that illegal discount codes are replaced with legal ones.
`no_of_seats` | The number of tickets you want to add to the trolley. It is optional to specify discount codes, however note that if you choose to specify them you must specify all of them. If you do not specify discount codes you will receive the default discount code - this will match what you are shown when you request availability.
`perf_id` | The performance identifier for the tickets that you want to add to your trolley.
`price_band_code` | The price band identifier for the tickets that you want to add to your trolley.
`remove_items_list` | A comma separated list of order `item_number`s that were previously added to the trolley and that you now want to remove.
`seatX` | Specify a specific seat for ticket number X, with zero-based numbering (so to specify seat A12 as the first ticket use `seat0=A12`). If seat numbers are not specified then when the trolley is later reserved you will receive best available seats.
`X_send_code` | Specify a send / despatch method for supplier system X. If this is not present it will default to the first send method. For example, to specify the POST send method for the nimax supplier system, use `nimax_send_code=POST`.
`ticket_type_code` | The ticket type identifier for the tickets that you want to add to your trolley.
`trolley_token` | The identifier for the trolley. This is used to add additional orders to a trolley, or to remove orders in a trolley.
`req_predicted_commission`| *Optional*. Include to retrieve [commission](#commission) data. For most partners this will include `predicted_user_commission` only (the predicted amount you earn per ticket). Some partners will also see `predicted_gross_commission`, which is the total commission available to be shared between Ingresso and our partner. By default you will see `predicted_user_commission` only - if you think you need to see `predicted_gross_commission` as well then please get in touch.


Additional parameters, primarily for internal use:

Parameter | Description
--------- | -----------
`add_crypto_block` | `cypto_block`s are used in the old [Ingresso XML API](http://www.ingresso.co.uk/apidocs/). To make migration away from the XML API easier, if you include `add_crypto_block` a `crypto_block` will be added to the response. You can then take the crypto_block and trolley_token and pass these in to the XML API call make_reservation to reserve and later purchase tickets (so that the JSON API is used up to the basket stage and the XML API is used to complete the purchase).
`departure_date` | Specify a departure date for example `departure_date=20170214`.
`duration` | Specify a duration for hotel product, for example `duration=3`.
`promo_code` | Specifying a promo codes can unlock a special offer discount code. If you have a promo code it can be specified like this: `promo_code=FOO`. Note that this feature is not commonly used with partners.

All of the following parameters can also be used to request additional data for the event and performance in each order. These are described in more detail in the Events section [here](#additional-parameters).

Parameter | Description
--------- | -----------
`req_avail_details` | Returns [availability details](#availability-details) - a cached list of unique ticket types and price bands available for the event and performance in each order. **This parameter is not commonly used.**
`req_avail_details_with_perfs` | This will add the list of available performance dates to each avail detail object. *Only valid if used alongside req_avail_details*.
`req_cost_range` | Returns [cost ranges](#cost-range) - a from price and offer detail for the event and performance in each order.
`req_cost_range_best_value_offer` | Returns the offer with the highest percentage saving.
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

> **Example response - adding specific seats to an existing trolley**

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
  "discarded_orders": [],
  "trolley_token": "k5--2fvf6Qc19ceQa4b9Kx4zIhDezG2yPzN71dEUmA8r3qFFCJJAQ3M3kYft_h9rzMAEBYyT0yNdS31HOv2WuYMbAbC8i49reKFaUu3esJckOm4cDipyTgQy3iekje0zgEM0MBSx1XY6t5bgbQ170iNxU2CLjUmZAT9K5iyqI-PN3hmJEpj0s5NMb31-THH8ufud82Mh2TeBP_c_e3_sabim1ftOfrnS6qPQAo9DF-Z1gXIXKLIzqCozRUwKqSOyLKbwAgfJSMi_2fQ_MhWMNkldjrfbOPpM0qKReJm8wPVW_zZNL_px_Mmg8LEIMgd5soJAa1edNePTrvSLppbDkevea-9Lr9UWERYKoSVXCsLSaEw2dGqaNJggpU6S_ksyduog3ALwStTJ6dr8dyGY5mImdVNcZhRepdmvKbRYNzBTDSxxY2n2LefoJkxc8bg-FxPacE9DRRNY_ZPl7f3fpa4a0Oq8j2DfrGxVcw0McxLoQ1E8XUkAH99LztF0qZN9RYuFwpQhoomgNOROf2M03DigA59j5ipO4Mg8p3iLKVndHJXS1SJfItBt1h86VXXUg0pkeZGn1e6TQkwx9m6t5faz4zuzGEg8XpuOXBxBqHrvQOFaFY5tDYYGr4x-uM2VowFCpM2c3-Aal3EeyjChHTycW---Z",
  "trolley_token_contents": {
    "bundle": [
      {
        "bundle_order_count": 1,
        "bundle_source_code": "ext_test0",
        "bundle_source_desc": "External Test Backend 0",
        "bundle_total_cost": 52.5,
        "bundle_total_seatprice": 51,
        "bundle_total_send_cost": 1.5,
        "bundle_total_surcharge": 0,
        "currency_code": "gbp",
        "order": [
          {
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
            "got_requested_seats": false,
            "item_number": 1,
            "performance": {
              "date_desc": "Sun, 15th January 2017",
              "event_id": "6IF",
              "has_pool_seats": false,
              "is_ghost": true,
              "is_limited": false,
              "iso8601_date_and_time": "2017-01-15T19:30:00Z",
              "perf_id": "6IF-A7N",
              "time_desc": "7.30 PM"
            },
            "price_band_code": "C/pool",
            "ticket_orders": {
              "ticket_order": [
                {
                  "discount_code": "ADULT",
                  "discount_desc": "Adult standard",
                  "no_of_seats": 1,
                  "sale_seatprice": 25,
                  "sale_surcharge": 0,
                  "total_sale_seatprice": 25,
                  "total_sale_surcharge": 0
                },
                {
                  "discount_code": "CHILD",
                  "discount_desc": "Child rate",
                  "no_of_seats": 2,
                  "sale_seatprice": 13,
                  "sale_surcharge": 0,
                  "total_sale_seatprice": 26,
                  "total_sale_surcharge": 0
                }
              ]
            },
            "ticket_type_code": "CIRCLE",
            "ticket_type_desc": "Upper circle",
            "total_no_of_seats": 3,
            "total_sale_seatprice": 51,
            "total_sale_surcharge": 0
          }
        ]
      },
      {
        "bundle_order_count": 1,
        "bundle_source_code": "ext_test1",
        "bundle_source_desc": "External Test Backend 1",
        "bundle_total_cost": 110,
        "bundle_total_seatprice": 100,
        "bundle_total_send_cost": 0,
        "bundle_total_surcharge": 10,
        "currency_code": "gbp",
        "order": [
          {
            "event": {
              "city_code": "london-uk",
              "city_desc": "London",
              "classes": {
                "theatre": "Theatre"
              },
              "country_code": "uk",
              "country_desc": "United Kingdom",
              "custom_filter": [],
              "event_desc": "The Unremarkable Incident of the Cat at Lunchtime",
              "event_id": "7AB",
              "event_path": "/7AB-the-unremarkable-incident-of-the-cat-at-lunchtime/",
              "event_status": "live",
              "event_type": "simple_ticket",
              "event_uri_desc": "The-Unremarkable-Incident-of-the-Cat-at-Lunchtime",
              "geo_data": {
                "latitude": 51.49306,
                "longitude": -0.22639
              },
              "has_no_perfs": false,
              "is_seated": true,
              "max_running_time": 90,
              "min_running_time": 90,
              "need_departure_date": false,
              "need_duration": false,
              "need_performance": true,
              "postcode": "W6 7ES",
              "show_perf_time": true,
              "source_code": "ext_test1",
              "source_desc": "External Test Backend 1",
              "venue_desc": "Lyric Apollo",
              "venue_uri_desc": "Lyric-Apollo"
            },
            "got_requested_seats": true,
            "item_number": 2,
            "performance": {
              "date_desc": "Tue, 1st January 2019",
              "event_id": "7AB",
              "has_pool_seats": true,
              "is_ghost": false,
              "is_limited": false,
              "iso8601_date_and_time": "2019-01-01T15:30:00Z",
              "perf_id": "7AB-5",
              "running_time": 90,
              "time_desc": "3.30 PM"
            },
            "price_band_code": "A/pool",
            "requested_seats": [
              {
                "col_id": "1",
                "full_id": "A1",
                "is_restricted_view": true,
                "row_id": "A",
                "seat_text": "Restricted View",
                "seat_text_code": "A.1"
              },
              {
                "col_id": "2",
                "full_id": "A2",
                "is_restricted_view": true,
                "row_id": "A",
                "seat_text": "Restricted View",
                "seat_text_code": "A.2"
              }
            ],
            "ticket_orders": {
              "ticket_order": [
                {
                  "discount_code": "NORMAL",
                  "discount_desc": "Regular Ticket",
                  "no_of_seats": 2,
                  "sale_seatprice": 50,
                  "sale_surcharge": 5,
                  "total_sale_seatprice": 100,
                  "total_sale_surcharge": 10
                }
              ]
            },
            "ticket_type_code": "STALLS",
            "ticket_type_desc": "Stalls",
            "total_no_of_seats": 2,
            "total_sale_seatprice": 100,
            "total_sale_surcharge": 10
          }
        ]
      }
    ],
    "trolley_bundle_count": 2,
    "trolley_order_count": 2
  }
}
```

```python
from pyticketswitch.event import Event
from pyticketswitch.bundle import Bundle
from pyticketswitch.performance import Performance
from pyticketswitch.order import TicketOrder
from pyticketswitch.trolley import Trolley
from pyticketswitch.order import Order

Trolley(
    token='64--QVkNGtT6STKmvXGI1e3mnxGkt-myQ56VW_cMlAxai766x2C-ksxcll2pY7-AUFsdyzNivWoVuCVJUwUfaiSFUUPQN6ErqZeAM5hRX6KXIHy4eLuzeV6SX8PvZDsO0RScIwO54SNFcgIPHl9MEnl0F_amE0EPNPSqn7C9IZqBkERKUfoi-E9eysQlq-EiQUKZPxev24rAS9ASDZPkpQKY4-9zM-fF1DSLcic0uho5c3jFsajf8QTrD_Se9sUfy4OrATwTIVmRZXNyeTFZWTJji0HQg3HmATR5Xhe_BzuOwqvxLjyT65uEP3oStqo3RZ6o1HiMe3DxI9u5khAf3QrIrd0-3bS4JsAmm5JQiq_Vg2FqfJTf42Gipre9kcU8mnAVHjPvGgNyGpuG2fQHpTCd-Pd9jiDbC93ckOJ8DZN8FJZbbYWD82Ep_w90YlSug5uQ_vh2f54xgD2RQRyxpyTAeKegBI-YRtiolLgSnGfuVyFCWb0JHES4ZrQHBEuii5gnocURfBq9sL--Z',
    bundles=[
        Bundle(
            source_code='ext_test0',
            orders=[
                Order(
                    item=1,
                    event=Event(
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
                    performance=Performance(
                        id='6IF-B0O',
                        event_id='6IF',
                        date_time=datetime.datetime(2017, 5, 4, 19, 30, tzinfo=tzoffset(None, 3600)),
                        date_description='Thu, 4th May 2017',
                        time_description='7.30 PM',
                        has_pool_seats=True,
                        is_limited=False,
                        is_ghost=False,
                        running_time=120,
                    ),
                    price_band_code='A/pool',
                    ticket_type_code='STALLS',
                    ticket_type_description='Stalls',
                    ticket_orders=[
                        TicketOrder(
                            code='',
                            description='',
                            number_of_seats=2,
                            seatprice=21.0,
                            surcharge=3.0,
                            total_seatprice=42.0,
                            total_surcharge=6.0,
                        )
                    ],
                    number_of_seats=2,
                    total_seatprice=42.0,
                    total_surcharge=6.0,
                    requested_seat_ids=[
                        
                    ],
                )
            ],
            description='External Test Backend 0',
            total_seatprice=42.0,
            total_surcharge=6.0,
            total_send_cost=1.5,
            total=49.5,
            currency_code='gbp',
        ),
        Bundle(
            source_code='ext_test1',
            orders=[
                Order(
                    item=2,
                    event=Event(
                        id='7AB',
                        status='live',
                        description='The Unremarkable Incident of the Cat at Lunchtime',
                        source='External Test Backend 1',
                        source_code='ext_test1',
                        event_type='simple_ticket',
                        venue='Lyric Apollo',
                        classes={
                            'theatre': 'Theatre'
                        },
                        postcode='W6 7ES',
                        city='London',
                        city_code='london-uk',
                        country='United Kingdom',
                        country_code='uk',
                        latitude=51.49306,
                        longitude=-0.22639,
                        max_running_time=90,
                        min_running_time=90,
                        show_performance_time=True,
                        has_performances=True,
                        is_seated=True,
                        needs_departure_date=False,
                        needs_duration=False,
                        needs_performance=False,
                    ),
                    performance=Performance(
                        id='7AB-5',
                        event_id='7AB',
                        date_time=datetime.datetime(2019, 1, 1, 15, 30, tzinfo=tzutc()),
                        date_description='Tue, 1st January 2019',
                        time_description='3.30 PM',
                        has_pool_seats=True,
                        is_limited=False,
                        is_ghost=False,
                        running_time=90,
                    ),
                    price_band_code='A/pool',
                    ticket_type_code='STALLS',
                    ticket_type_description='Stalls',
                    ticket_orders=[
                        TicketOrder(
                            code='NORMAL',
                            description='Regular Ticket',
                            number_of_seats=2,
                            seatprice=50.0,
                            surcharge=5.0,
                            total_seatprice=100.0,
                            total_surcharge=10.0,
                        )
                    ],
                    number_of_seats=2,
                    total_seatprice=100.0,
                    total_surcharge=10.0,
                    requested_seat_ids=[
                        'A1',
                        'A2'
                    ],
                )
            ],
            description='External Test Backend 1',
            total_seatprice=100.0,
            total_surcharge=10.0,
            total_send_cost=0.0,
            total=110.0,
            currency_code='gbp',
        )
    ],
)
```

The trolley has three main structures:

* A **`bundle`** represents all orders from  a particular supplier. Orders are
grouped like this so that all items are ordered from the  supplier at once - if
the supplier posts tickets on our behalf then separate orders will be posted
together, and if the supplier processes payment all orders in the bundle will be
paid for with a single payment. Reserving and purchasing orders will also succeed
or fail together.

* An **`order`** represents a number of tickets to a particular `price band` and
`ticket type`, for a particular [performance](#performances) of an
[event](#events).

* An `order` will contain multiple **`ticket_order`**s when more than one
`discount code` is used (for example ticket 1 and 2 have the ADULT discount
code, and tickets 3 and 4 have the CHILD discount code). In this example there
will be two `ticket_order`s: one for ADULT and one for CHILD.



**`trolley` attributes:**

Attribute | Description
--------- | -----------
`discarded_orders` | As discussed above, adding an order will remove anything in the trolley which would prevent it being added. See below for detail of the `order` object.
`input_contained_unavailable_order` | This is a flag that will get set to `true` if part of the trolley request could not be added to the trolley. This occurs when we weren't able to find any available products that matched the request and it usually means the user has passed in invalid parameters or is requesting an item that is sold out.
`trolley_token_contents` | See below for object detail.
`trolley_token` | The identifier for the trolley (this is an internal hash representing the items in the trolley, so the identifier will be the same for identical trolleys).


**`trolley_token_contents` attributes:**

Attribute | Description
--------- | -----------
`bundle` | See below for object detail.
`trolley_bundle_count` | The number of bundles in the trolley.
`trolley_order_count` | The number of orders in the trolley. There will be at least one order for every bundle.


**`bundle` attributes** 

Attribute | Description
--------- | -----------
`bundle_order_count` | The number of orders in this bundle.
`bundle_source_code` | The code of the supplier system that this bundle will be purchased from.
`bundle_source_desc` | The name of the supplier system that this bundle will be purchased from.
`bundle_total_cost` | The total cost of this bundle.
`bundle_total_seatprice` | The total face value for this bundle.
`bundle_total_send_cost` | The despatch cost for this bundle (similar to a transaction fee). Orders within the same bundle will be sent together.
`bundle_total_surcharge` | The total booking fee for this bundle.
`currency_code` | The currency code for the price - further detail for the currency can be found in the `currency_details` object, described below.
`order` | See below for object detail.


**`order` attributes:**

Attribute | Description
--------- | -----------
`event` | The [event](#event-object) for this order.
`item_number` | A unique sequential number for the `order`. The second order added to the trolley will have `item_number` 2. The `item_number` remains constant as orders are added or removed, so if `item_number` 1 is removed, the first order will be `item_number` 2. 
`performance` | The [performance](#performance-object) for this order.
`price_band_code` | The code for a price band, for example "C/pool". The price band code is generally made up of the code from the underlying supplier system, e.g. "C", followed by a "/" separator then "pool" or "alloc", indicating whether the price band is taken from the general pool of tickets or is from a ring-fenced allocation.
`seat_request_status` | The status of your tickets after they have been reserved. Possible values are `not_requested` (specific seats not requested), `got_none` (you requested A13 and A14 but we gave you A15 and A16), `got_partial` (you requested A13 and A14 but we gave you A14 and A15), `got_all` (you requested A13 and A14 and you got A13 and A14 - by far the most common response when requesting specific seats). 
`ticket_orders` | A number of ticket_order objects, details below.
`ticket_type_code` | The unique identifier for the ticket type. For seated events this refers to a part of house / seating area such as Grand Circle.
`ticket_type_desc` | The description for the ticket type. This should be displayed to the customer
`total_no_of_seats` | The number of seats for this order.
`total_sale_seatprice` | The total face value for this order.
`total_sale_surcharge` | The total booking fee for this order.


**`ticket_order` attributes:**

Attribute | Description
--------- | -----------
`discount_code` | The discount code that applies to this ticket_order.
`discount_desc` | The description for the discount code.
`no_of_seats` | The number of tickets in this ticket_order.
`sale_seatprice` | The face value per ticket in this ticket_order.
`sale_surcharge` | The booking fee per ticket in this ticket_order.
`total_sale_seatprice` | The total face value of all tickets in this ticket_order.
`total_sale_surcharge` | The total booking fee of all tickets in this ticket_order.


The outer object includes a **`currency_details`** object containing one currency
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
