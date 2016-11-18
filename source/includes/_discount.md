# Discounts

- The [discount object](#discount-object)
- The API call to [retrieve a list of discounts for given availability](#list-discounts) (either a price band and ticket type, or an individual seat) (TODO is that correct)

## Discount object

> **Example response - best available only**

```json
{
    "absolute_saving": 0,
    "discount_code": "ADULT",
    "discount_desc": "Adult standard",
    "is_alloc": false,
    "is_offer": false,
    "non_offer_sale_seatprice": 35,
    "non_offer_sale_surcharge": 4,
    "number_available": 6,
    "percentage_saving": 0,
    "price_band_code": "A",
    "sale_seatprice": 35,
    "sale_surcharge": 4
}
```

A `discount` represents a price type or concession that is available for a set of available tickets. For example seat A12 in the Royal Circle may have the following `discounts` available, each with their own price: 

* Adult
* Child
* Student

When multiple `discounts` are returned, you should present the choice to your user (it is possible for you to just purchase the default `discount` even when there are multiple `discounts` but this is not recommended). When there are multiple offers available on a single ticket these are also presented as `discounts` so you can present the choice to your users (multiple offers are rare though).

When [retrieving availability for a performance](#retrieve-availability) we return the default `discount`, you then call [list discounts](#list discounts) for the full list of `discounts`.

The following attributes are returned - these are the same as for the [availability object](#availability-object).

Attribute | Description
--------- | -----------
`absolute_saving` | Defined as (`non_offer_sale_seatprice` + `non_offer_sale_surcharge`) - (`sale_seatprice` + `sale_surcharge`)
`discount_code` | The unique identifier of the [discount](#discount-object). This is unique to each supplier system, so you cannot assume that if the "ADULT" discount_code is available on one event that it will be available on other events.
`discount_desc` | The description of the default discount to present to users.
`is_alloc` | `true` means the availability is from an allocation; `false` means the availability is from the general pool. This is normally `false` and can be ignored for most use cases.
`is_offer` | `true` if the ticket price is discounted below the full price, i.e. if `absolute_saving` is greater than zero.
`non_offer_sale_seatprice` | The per-ticket price for full-priced tickets. This will be the face value price when the market has such a concept (for example the London theatre market has this concept, but some New York theatre shows do not). This is the same as the `sale_seatprice` when the price band is not discounted. (TODO can we remove "sale" from the name?)
`non_offer_sale_surcharge` | The per-ticket booking fee for full-priced tickets. To determine the total ticket price you must add together the `non_offer_sale_seatprice` and the `non_offer_sale_surcharge`.
`number_available` | This is the maximum number of contiguous seats that can be purchased. This applies to best available only - if you are using seat selection and `contiguous_seat_selection_only` is `false` it is possible to select above this number.
`percentage_saving` | Defined as `absolute_saving` / (`non_offer_sale_seatprice` + `non_offer_sale_surcharge`) * 100
`price_band_code` | The code for a price band. To uniquely identify a price band you should take the combination of `ticket_type_code`, `price_band_code` and `is_alloc` (`is_alloc` will default in most cases if you do not specify it).
`price_band_description??` | (TODO what is the actual name of this attribute?)
`sale_seatprice` | The per-ticket price. This will be the face value price when the market has such a concept (for example the London theatre market has this concept, but some New York theatre shows do not). This is the same as the `non_offer_sale_seatprice` when the price band is not discounted.
`sale_surcharge` | The per-ticket booking fee. To determine the total ticket price you must add together the `sale_seatprice` and the `sale_surcharge`.

## List discounts

> **Definition**

```
GET https://api.ticketswitch.com/cgi-bin/json_discounts.exe/{username}?user_passwd={password}&perf_id={perfid}&price_band_code={pricebandcode}&ticket_type_code={tickettypecode}
```

This call is used to return availability for a performance. It returns a list of [availability objects](#availability-object).

> **Example request - price band**

```shell
curl https://api.ticketswitch.com/cgi-bin/json_discounts.exe/demo \
        -d "user_passwd=demopass" \
        -d "perf_id=6IF-A5W" \
        -d "price_band_code=A" \
        -d "ticket_type_code=CIRCLE" \
        -G
```

### Request

Parameter | Description
--------- | -----------
`perf_id` | The performance identifier
`price_band_code` | The price band code you want to view discounts for
`ticket_type_code` | The ticket type code you want to view discounts for


### Response

Returns a list of [discount objects](#discount-object).


ADD DISCOUNTS