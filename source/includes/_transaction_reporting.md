# Transaction Reporting

<aside class="notice"><b>IMPORTANT:</b> Note that transaction reporting service is using different domain
than other ticketswitch endpoints - <b>.ticketswitch.io</b> as opposed to <b>.ticketswitch.com</b></aside>

<aside class="notice"><b>NOTE:</b>Transaction records are not live data, so a delay of between a few seconds and a few minutes can be expected between the transaction being completed and becoming visible in the reports</aside> 

> **Definition**

```
GET https://api.ticketswitch.io/transactions.v0?from=2017-06-01
```

The transaction reporting resource allow you to retrieve, filter and order your transactions.
This can be used, for example, to regularly populate your own database of Ingresso transactions.

1. [Pagination](#pagination)
2. [Time filters](#time-filters)
3. [Other filters](#other-filters)
4. [Ordering](#ordering-options)


## Pagination

> **Definition**

```
GET https://api.ticketswitch.io/transactions.v0?page_number={page_number}&page_length={num_items}
```
By default the returned dataset is paginated and limited to 10 records per page. To iterate over the pages supply a `page_number=x` query parameter where `x` is an unsigned integer. If this parameter is not passed the page will be set to 0 by default. To change number of records returned per page pass `page_length=y`, `y` being an integer. The maximum number of records per page is 100.

> **Example request**
 
```shell
curl https://api.ticketswitch.io/transactions.v0?page_number=2&page_length=20&from=2017-06-01\
    -u "demo:demopass" \
    -X GET
```

## Time Filters

> **Definition**

```
GET https://api.ticketswitch.io/transactions.v0?from={date}&to={date}
```


The transactions API allows `from` and `to` query parameters to be passed to filter by the purchase time (`purchase_time_utc`). The time values supplied can either be passed as UTC timestamp or as a more human readable format in simplified **ISO 8601** in a form of `<YYYY>-<MM>-<dd>T<hh>:<mm>:<ss>` (also in UTC timezone) where hours, minutes and seconds are optional and will default to either 00, 00, 00 in the case of `from` parameter or 23, 59, 59 in case of `to` if not passed.

For example to retrieve the records `from` September 29th 2017 at midnight (00:00:00) the following time parameter values are valid:

- `1506643200`
- `2017-09-29T00:00:00`
- `2017-09-29T00:00`
- `2017-09-29T00`
- `2017-09-29`


<aside class="notice">By default if no <b>to</b> parameter is passed it will default to (current_time - 10 minutes). Note that you can set it to time earlier than that but we can't guarantee that list will contain all transaction records made during last few minutes.</aside>

<aside class="notice">If no <b>from</b> parameter is passed it will default to (<b>to</b> parameter -  120 minutes)</aside>

For partners using this resource to maintain a full database of Ingresso transactions we recommend that you:

- Look up the maximum `purchase_time_utc` in your database, add one second, and pass this as the `from` parameter. If you haven't previously downloaded transactions then set the `from` date well in the pass, for example 01/01/2000
- Do not pass a `to` parameter - this will ensure all missing records up to 10 minutes ago are returned
- Order by purchase time ascending (`$purchase_time_utc`)
- Loop through the pages and results adding them to your database


> **Example requests**
 
```shell
curl https://api.ticketswitch.io/transactions.v0?from=2016-01-02T14:33:22&to=2016-06-03T14:25:21\
    -u "demo:demopass" \
    -X GET
```

```shell
curl https://api.ticketswitch.io/transactions.v0?from=1506623211&to=1507643200\
    -u "demo:demopass" \
    -X GET
```

## Other Filters

> **Definition**

```
GET https://api.ticketswitch.io/transactions.v0/filter:{filter_options}'
```

The API allows you to use filters besides the purchase time. Currently the following filters are available:


- `event_id` - filter by Event's ID
- `supplier_code` - Filter by name of supplier's code
- `billable` - (boolean `true` or `false`) If set to `true` will only return records for which will be applied a charge by Ingresso (or was applied in the past), if `false` only not billable records will be returned. By default both types of transactions are returned in the query. 


If you would find other filters useful please contact us: api@ingresso.co.uk.

To pass a filter option insert `/filter:{filter_options}` after `/transactions.0`. All options are passed as `key=value` separated by `,`. For example: `/filter:event_id=44AP,supplier_code=nimax`. The comma separator corresponds to the `AND` logical statement; so in the above example only records with `event_id` equal to `44AP` and
`supplier_code` equal to `nimax` will be returned. If the value contains white space then enclose it in single quotes `'`.

> **Example request**
 
``` shell
curl https://api.ticketswitch.io/transactions.v0/filter:event_id='44AP'\
    -u "demo:demopass" \
    -X GET
```


``` shell
curl https://api.ticketswitch.io/transactions.v0/filter:event_id='44AP',supplier_code=nimax\
    -u "demo:demopass" \
    -X GET
```



## Ordering options

> **Definition**

```
GET https://api.ticketswitch.io/transactions.v0/order:{order_options}
```

To order returned data insert `/order:<order_options>` after `/transactions.v0` using the set of fields you want to order by. A list of ordering fields should correspond to keys in the returned transaction records separated by `,`. By default the data is ordered descending, if you wish to order it ascending prepend the field name with a `$` character, for example `$event_id,purchase_time_utc` will order the data alphabetically by event id. If there are 2 records with same id those records will be ordered descending by purchase time. The following ordering options are supported:


- `event_id`
- `supplier_code`
- `purchase_time_utc`
- `purchase_time_london`



> **Example request**

``` shell
curl https://api.ticketswitch.io/transactions.v0/order:event_id,supplier_code?from=2017-06-01\
    -u "demo:demopass" \
    -X GET
```

## Response

> **Example API reponse**

``` json
{
    "status": "success",
    "count": 10,
    "message": "",
    "data": [
    {
            "transaction_id": "T000-0000-ABCD-EFGH",
            "item_number": 1,
            "user_id": "demo",
            "backend_purchase_reference": "123456",
            "event_id": "2GXJ",
            "event_desc": "Wicked",
            "event_country_code": "uk",
            "venue_desc": "Apollo Victoria",
            "supplier_code": "atg",
            "purchase_time_utc": "2017-01-01T09:58:40Z",
            "purchase_time_london": "2017-01-01T09:58:40Z",
            "performance_local_time": "2017-01-02T19:30:00Z",
            "perf_type_code": "748418",
            "ticket_type_code": "CIR",
            "ticket_type_desc": "Dress Circle",
            "price_band_code": "A",
            "discount_desc": "Regular Price",
            "send_code": "H",
            "send_desc": "E-ticket (print at home yourself)",
            "seat_ids": [
                "J20",
                "J21",
                "J22",
                "J23"
            ],
            "trans_status": "normal",
            "sale_mode": "internal_credit",
            "debitor_reference": null,
            "user_can_use_customer_data": false,
            "supplier_can_use_customer_data": false,
            "num_tickets": 4,
            "num_transactions": 1,
            "sale_currency_code": "gbp",
            "sale_seatprice": 278,
            "sale_surcharge": 13.8,
            "ticket_price": 291.8,
            "send_cost": 0,
            "commision_currency_code": "gbp",
            "user_commission_inc_vat": 9.66,
            "gross_commission_inc_vat": 13.8,
            "inside_commision_inc_vat": 0,
            "supplier_levy": 7,
            "supplier_fee": 0,
            "supplier_rebate": 0,
            "surcharge_non_commissionable_postage": 0,
            "surcharge_commissionable_postage": 0,
            "processing_fee": 0,
            "supplier_uplift": 0,
            "user_uplift": 0,
            "customer": {
                "first_name": "John",
                "last_name": "Doe",
                "address_line_one": "Butterwick 1",
                "address_line_two": "Hammersmith",
                "town": "London",
                "county": "Greater London",
                "country_code": "uk",
                "postcode": "W68DL",
                "work_phone": 1234567,
                "home_phone": 1234567,
                "email": "customerservices@ingresso.co.uk"
            }
        }
	],
	"timestamp": "20171004150512",
	"_meta": null
}

```


### Results

 Attribute | Description
-----------|--------------------------------------------------------------------------
`status`  | String representing the status of the response (along with an HTTP status code). Can have following values: `success`, `error`, `warning`, `debug`
`message`| Optional message, usually contains detailed error information if `status` is set to `error`.
`count`| Number of transaction records returned.
`timestamp` | UTC time in a form of `YYYYMMddhhmmss`.
`_meta` | Object containing additional data, if supplied.
`data` | List of objects containing transaction records, defined below.


Each transaction record returned from the API will have the following keys set:

Name                                   | Description
---------------------------------------|----------------------------------------------------------------------------------------------------------------------------------
`transaction_id`                       | The primary Ingresso transaction reference.
`item_number`                          | A unique sequential number for the order. The second order added to the trolley will have item_number 2. The combination of `transaction_id` and `item_number` is unique.
`user_id`                              | The user for which the transaction was made.
`backend_purchase_ref`                 | The purchase reference from the supplier ticketing system.
`event_id`                             | Unique identifier for the event.
`event_desc`                           | Name of the show or event e.g. The Lion King.
`event_country_code`                   | Code for the event's country.
`venue_desc`                           | Description of the event's venue.
`supplier_code`                        | Name of the backend system purchase was made with.
`purchase_time_utc`                    | Purchase time (UTC).
`purchase_time_london`                 | Purchase time (London timezone).
`performance_local_time`               | Performance time in the timezone local to the event.
`perf_type_code`                       | Internal code.
`ticket_type_code`                     | The unique identifier for the ticket type. For seated events this refers to a part of house / seating area such as Grand Circle.
`ticket_type_desc`                     | The description for the ticket type displayed to the customer.
`send_code`                            | Send method code.
`send_desc`                            | Description for the send method.
`seat_ids`                             | Array containing list of purchased seats.
`trans_status`                         | Status of the transaction - one of `normal`, `test`, `refunded`, `partially-refunded`, `exchanged-original`, `exchanged-replacement` (for exchanges the old booking is marked `exchanged-original` and the new booking marked `exchanged-replacement`), `cancelled-performance`, `exchanged-venue-system-only` (this is rare but covers the case when a booking is exchanged within the venue system so there is no record of the new tickets within the Ingresso Platform).
`sale_mode`                            | The mode used to purchase tickets. Will be internal_credit if the sale was made on credit or external_redirect if the sale was made using Stripe or with a redirect.
`debitor_reference`                    | If using Stripe this will be Stripe's Charge ID.
`user_can_use_customer_data`           | Data protection question - set to true if the customer has opted in to receiving marketing emails from you.
`supplier_can_use_customer_data`       | Data protection question - set to true if the customer has opted in to receiving marketing emails from the event provider.
`num_tickets`                          | Number of purchased tickets.
`num_transactions`                     | This will be 1 for the first item in the order and 0 for the rest.
`sale_currency_code`                   | `ISO 4217` three letter code that applies to the `sale_seatprice`, `sale_surcharge`, `ticket_price`, `send_cost`.
`sale_seatprice`                       | Base price for the seats.
`sale_surcharge`                       | Total surcharge for the order.
`ticket_price`                         | Total ticket price including surcharge.
`send_cost`                            | Send method cost.
`commission_currency_code`             | `ISO 4217` three letter code that applies to all of the following commission attributes.
`user_commission_inc_vat`              | The commission earned for this transaction (all commission amounts are totals, not an amount per ticket).
`billable`                             | Whether or not a charge was or will be applied to the transaction

The following fields are only returned for users that have permission to view detailed commission information:

Name                                   | Description
---------------------------------------|----------------------------------------------------------------------------------------------------------------------------------
`gross_commission_inc_vat`             | The total commission available (Ingresso's commission + user_commission_inc_vat).
`inside_commision_inc_vat`             | The total inside commission (commission paid by a supplier from the `sale_seatprice`).
`supplier_levy`                        | The total restoration levy or venue facility fee.
`supplier_fee`                         | A fee paid to the supplier.
`supplier_rebate`                      | Another type of fee paid to the supplier.
`surcharge_non_commissionable_postage` | A postage fee that is added as part of the `sale_surcharge` and excluded from commission calculations, not used by most partners.
`surcharge_commissionable_postage`     | A postage fee that is added as part of the `sale_surcharge` and included within commission calculations, not used by most partners.
`processing_fee`                       | A processing fee, not used by most partners.
`supplier_uplift`                      | Uplift is an additional charge on top of the ticket price for an upgraded experience. This is the uplift component earned by the supplier.
`user_uplift`                          | This is the uplift component earned by the partner.


The customer fields are only returned for users which have permissions to see detailed customer data - by default these fields won't be included - if you would like them included please contact us: api@ingresso.co.uk.

For testing you can use the user `demo-customerdata` (password `demopass`). You can purchase with this user the same way you can with `demo`, however note that any customer data you enter when purchasing will be visible by everyone, so **do not enter any data you do not want the internet to see**. 

Name                                   | Description
---------------------------------------|----------------------------------------------------------------------------------------------------------------------------------
`first_name`                           | First name of the customer
`last_name`                            | Last name of the customer
`address_line_one`                     | First line of customer's address
`address_line_two`                     | Second line of customer's address
`town`                                 | Address' city
`county`                               | Address' county
`country`                              | Customer's country
`postcode`                             | Customer's postcode
`home_phone`                           | Customer's home phone
`work_phone`                           | Customer's work phone
`email`                                | Customer's email
