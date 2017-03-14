# Discounts

> **Definition**

```
GET https://demo.ticketswitch.com/f13/discounts.v1?perf_id={performanceid}&price_band_code={pricebandcode}&ticket_type_code={tickettypecode}
```

A **discount** represents a price type or concession that is available for a 
price band. These are some examples:

* Adult
* Child
* Student
* Senior citizen

When retrieving [availability](#availability) for a performance we return the
default discount. This `discounts` resource should then be used to get the full 
list of available discounts. When multiple discounts are returned non-default
discounts typically have a lower price (for example Child pricing).

When multiple `discounts` are returned, you should present the choice to your
user (we use a drop-down list). When there are multiple special offers available
on a single ticket these are also presented as `discounts` so you can present
the choice of special offer to your user (multiple offers are rare though). It
is possible for you to ignore this call and just purchase the default `discount`
even when there are multiple `discounts`, but that is not recommended.

> **Example request - price band**

```shell
curl https://demo.ticketswitch.com/f13/discounts.v1 \
    -u "demo:demopass" \
    -d "perf_id=6IF-B0I" \
    -d "price_band_code=A/pool" \
    -d "ticket_type_code=CIRCLE" \
    --compressed \
    -G
```

### Request

Parameter | Description
--------- | -----------
`perf_id` | The performance identifier.
`price_band_code` | The price band code you want to view discounts for.
`ticket_type_code` | The ticket type code you want to view discounts for.
`no_of_seats` | *Optional*. The number of seats the customer would like. If this is specified then only discounts valid for the number of seats will be shown.


### Response

> **Example response**

```shell
{
  "currency_code": "gbp",
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
  "discounts": {
    "discount": [
      {
        "absolute_saving": 0,
        "discount_code": "ADULT",
        "discount_desc": "Adult",
        "is_offer": false,
        "non_offer_sale_seatprice": 35,
        "non_offer_sale_surcharge": 4,
        "number_available": 6,
        "percentage_saving": 0,
        "price_band_code": "A/pool",
        "sale_seatprice": 35,
        "sale_surcharge": 4
      },
      {
        "absolute_saving": 0,
        "discount_code": "CHILD",
        "discount_desc": "Child rate",
        "is_offer": false,
        "non_offer_sale_seatprice": 18,
        "non_offer_sale_surcharge": 3,
        "number_available": 6,
        "percentage_saving": 0,
        "price_band_code": "A/pool",
        "sale_seatprice": 18,
        "sale_surcharge": 3
      },
      {
        "absolute_saving": 0,
        "discount_code": "STUDENT",
        "discount_desc": "Student rate",
        "is_offer": false,
        "non_offer_sale_seatprice": 26,
        "non_offer_sale_surcharge": 3,
        "number_available": 6,
        "percentage_saving": 0,
        "price_band_code": "A/pool",
        "sale_seatprice": 26,
        "sale_surcharge": 3
      },
      {
        "absolute_saving": 0,
        "discount_code": "OAP",
        "discount_desc": "Senior citizen rate",
        "is_offer": false,
        "non_offer_sale_seatprice": 28,
        "non_offer_sale_surcharge": 3,
        "number_available": 6,
        "percentage_saving": 0,
        "price_band_code": "A/pool",
        "sale_seatprice": 28,
        "sale_surcharge": 3
      }
    ]
  }
}
```


The following attributes are returned within the `discount` dictionary. These 
are the same attributes returned in the [availability](#availability) price band
dictionary.

Attribute | Description
--------- | -----------
`absolute_saving` | Defined as (`non_offer_sale_seatprice` + `non_offer_sale_surcharge`) - (`sale_seatprice` + `sale_surcharge`)
`discount_code` | The unique identifier of the `discount`. This is unique to each supplier system, so you cannot assume that if the "ADULT" discount_code is available on one event that it will be available on other events.
`discount_desc` | The description of the default discount to present to users.
`is_offer` | `true` if the ticket price is discounted below the full price, i.e. if `absolute_saving` is greater than zero.
`non_offer_sale_seatprice` | The per-ticket price for full-priced tickets. This will be the face value price when the market has such a concept (for example the London theatre market has this concept, but some New York theatre shows do not). This is the same as the `sale_seatprice` when the price band is not discounted.
`non_offer_sale_surcharge` | The per-ticket booking fee for full-priced tickets. To determine the total ticket price you must add together the `non_offer_sale_seatprice` and the `non_offer_sale_surcharge`.
`number_available` | This is the maximum number of contiguous seats that can be purchased. This applies to best available only - if you are using seat selection and `contiguous_seat_selection_only` is `false` it is possible to select above this number.
`percentage_saving` | Defined as `absolute_saving` / (`non_offer_sale_seatprice` + `non_offer_sale_surcharge`) * 100
`price_band_code` | The code for a price band. To uniquely identify a price band you should take the combination of `ticket_type_code` and `price_band_code`.
`price_band_desc` | The description for the price band. This will often not be present (not all supplier ticketing systems provide it) but when it is present it should be displayed to the customer.
`sale_seatprice` | The per-ticket price. This will be the face value price when the market has such a concept (for example the London theatre market has this concept, but some New York theatre shows do not). This is the same as the `non_offer_sale_seatprice` when the price band is not discounted.
`sale_surcharge` | The per-ticket booking fee. To determine the total ticket price you must add together the `sale_seatprice` and the `sale_surcharge`.

The outer object also includes a `currency_code` and a `currency_details` object
containing further currency detail:

Attribute | Description
--------- | -----------
`currency_code` | [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217) three letter code
`currency_factor` | Multiply by this number to get values in the base unit (e.g. multiplying $47.11 by the currency_factor will give 4711 cents)
`currency_number` | [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217) numeric identifier
`currency_places` | The number of decimal places to display (eg 45.5 usd should be displayed as 45.50)
`currency_post_symbol` | A symbol to display at the end of the price
`currency_pre_symbol` | A symbol to display in front of the price
