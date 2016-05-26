#Generic Output

##Cost Range Attributes

Parameter | Description
--------- | -----------
`max_combined` | The total price per ticket for the highest priced ticket seen during previous availability requests
`max_seatprice` | The face value per ticket for the highest priced ticket seen during previous availability requests
`max_surcharge` | The booking fee per ticket for the highest priced ticket seen during previous availability requests
`min_combined` | The total price per ticket for the lowest priced ticket seen during previous availability requests
`min_seatprice` | The face value per ticket for the lowest priced ticket seen during previous availability requests
`min_surcharge` | The booking fee per ticket for the lowest priced ticket seen during previous availability requests
`no_singles_cost_range` | Another cost range when single tickets are excluded (can be used e.g. when you want to avoid showing a low from price to customers when it is only available on single tickets)
`quantity_options` | The valid ticket quantities that can be purchased, as seen during previous availability requests
`range_currency` | The currency of the `cost_range` prices - see below for detail


##Currency Attributes

Parameter | Description
--------- | -----------
`currency_code` | currency code in the [ISO4217 format](https://en.wikipedia.org/wiki/ISO_4217)
`currency_factor` | multiply by this number to get values in the base unit (eg multiplying $47.11 by the currency_factor will give 4711 cents)
`currency_number` | currency number in the [ISO4217 format](https://en.wikipedia.org/wiki/ISO_4217)
`currency_places` | the number of decimal places to display (eg 45.5 usd should be displayed as 45.50)
`currency_post_symbol` | a symbol to display at the end of the price
`currency_pre_symbol` | a symbol to display in front of the price