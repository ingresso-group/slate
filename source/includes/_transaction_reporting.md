# Transaction Reporting

<aside class="warning"><b>IMPORTANT:</b> Note that transaction reporting service is using different domain
than other ticketswitch endpoints - <b>.ticketswitch.io</b> as opposed to <b>.ticketswitch.com</b></aside>

> **Definition**

```
GET https://api.ticketswitch.io/transactions.v0
```

Transaction API allows for retrieval, filtering and ordering of all the transactions made for
the given account.

1. [Pagination](#pagination): Pagination options
2. [Timespan options](#timespan-options): Setting timespan for returned records
3. [Filtering the data](#filtering-options): Defining filter options
4. [Ordering options](#ordering-options): Defining order settings

## Pagination

> **Definition**

```
GET https://api.ticketswitch.io/transactions.v0?page_number={page_number}&page_length={num_items}
```
By default returned dataset is paginated and limited to 10 records per page. To iterate over the pages supply `page_number=x` query parameter to the request where `x` is unsigned integer, if this parameter is not passed the page will be set to 0 by default. To change number of records returned per page pass `page_length=y`, `y` being an integer. Maximum number of records per page is 100.

> **Example request**
 
```shell
curl https://api.ticketswitch.io/transactions.v0?page_number=2&page_length=20\
    -u "demo:demopass" \
    -X GET
```

## Timespan options

> **Definition**

```
GET https://api.ticketswitch.io/transactions.v0?from={date}&to={date}
```

Transactions API allows passing `from` and `to` query parameters that limit the time for which the data will be retrieved. The time values supplied can either be passed as UTC timestamp or as a more human readable format in simplified **ISO 8601** in a form of `<YYYY>-<MM>-<dd>T<hh>:<mm>:<ss>` (also in UTC timezone) where hours, minutes and seconds are optional and will default to either 00, 00, 00 in the case of `from` parameter or 23, 59, 59 in case of `to` if not passed.
For example assuming we want to retrieve the records `from` September 29th 2017 at midnight (00:00:00) following time parameter values are valid:
- `1506643200`
- `2017-09-29T00:00:00`
- `2017-09-29T00:00`
- `2017-09-29T00`
- `2017-09-29`

> **Example requests**
 
```shell
curl https://api.ticketswitch.io/transactions.v0?from=2016-01-02T14:33:22&to=2016-01-03T14:25:21\
    -u "demo:demopass" \
    -X GET
```

```shell
curl https://api.ticketswitch.io/transactions.v0?from=1506623211&to=1506643200\
    -u "demo:demopass" \
    -X GET
```

## Filtering the data

> **Definition**

```
GET https://api.ticketswitch.io/transactions.v0/filter:{filter_options}'
```

The API also allows filtering the data so that only records for which keys match specifified value will be returned.
Currently transactions API allows following keys to be filtered by:
- `event_id`
- `supplier_code`

To pass filter option insert `/filter:{filter_options}` path after `/transactions.0` endpoint with selected filter options, all options are passed in a form `key=value` separated by `,` for example `/filter:event_id=44AP,supplier_code=nimax`, the option separator corresponds to the `AND` logical statement where key should match json key in returned transaction record, this means that in the above example only records with `event_id` equal to `44AP` and
`supplier_code` with `nimax` value will be returned. If the value of the field option contains white space enclose it within single quotes `'`.

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

To order returned data insert `/order:<order_options>` path after `/transactions.v0` endpoint with set of fields you want to order the data by. A list of ordering fields should correspond to keys in returned transaction records separated by `,`. By default the data is ordered descending, if you wish to order it ascending prepend field name with `$` character, for example `$event_id,purchase_time_utc` will order data alphabetically by event id,
and if there are 2 records with same id those records will be ordered descending by purchase time. Currently following ordering options are supported:
- `event_id`
- `supplier_code`
- `purchase_time_utc`
- `purchase_time_london`

> **Example request**

``` shell
curl https://api.ticketswitch.io/transactions.v0/order:event_id,supplier_code\
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
            "user_uplift": 0
        }
	],
	"timestamp": "20171004150512",
	"_meta": null
}

```

### Results

#### Response JSON body

 Attribute | Description
-----------|--------------------------------------------------------------------------
 `status`  | String representing status of the response (along with HTTP status code). Can have following values: `success`, `error`, `warning`, `debug`
`message`| Optional message, usually contains detailed error information if `status` is set to `error`.
`count`| Number of transaction records returned
`timestamp` | UTC time in a form of `YYYYMMddhhmmss`
`_meta` | object containing additional data, if supplied.
`data` | list of objects containing transaction records

#### Transaction record reference

Each transaction record returned from API should have following keys set:

Name                                   | Description
---------------------------------------|----------------------------------------------------------------------------------------------------------------------------------
`transaction_id`                       | Unique identifier for transaction.
`user_id`                              | The user for which the transaction was made.
`backend_purchase_ref`                 | Purchase reference from external ticketing system.
`event_id`                             | Unique identifier for event.
`event_desc`                           | Name of the show or event e.g. The Lion King.
`event_country_code`                   | Code for the event's country.
`venue_desc`                           | Description of the event's venue.
`supplier_code`                        | Name of the backend system purchase was made with.
`purchase_time_utc`                    | Purchase UTC time.
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
`debitor_reference`                    | If using Stripe this will be Stripe's charge ID.
`user_can_use_customer_data`           | Data protection question - set to true if the customer has opted in to receiving marketing emails from you.
`num_tickets`                          | Number of purchased tickets.
`num_transactions`                     | This will be 1 for the first item in the order and 0 for the rest.
`sale_currency_code`                   | `ISO 4217` three letter code that applies to the `sale_seatprice`, `sale_surcharge`, `ticket_price`, `send_cost`.
`sale_seatprice`                       | Base price for the seats.
`sale_surcharge`                       | Total surcharge for the order.
`ticket_price`                         | Total ticket price including surcharge.
`send_cost`                            | Send method cost.
`commission_currency_code`             | `ISO 4217` three letter code that applies to all of the following commission attributes.
`user_commission_inc_vat`              | The commission earned for this transaction (all commission amounts are totals, not an amount per ticket).
`gross_commission_inc_vat`             | The total commission available, including Ingresso's commission earned.
`inside_commision_inc_vat`             | The total inside commission (commission paid by a supplier from the `sale_seatprice`).
`supplier_levy`                        | The total restoration levy or venue facility fee.
`supplier_fee`                         | A fee paid to the supplier.
`supplier_rebate`                      | Another type of fee paid to the supplier.
`surcharge_non_commissionable_postage` | A postage fee that is added as part of the `sale_surcharge` and excluded from commission calculations, not used by most partners.
`surcharge_commissionable_postage`     | A postage fee that is added as part of the `sale_surcharge` and included within commission calculations, not used by most partners.
`processing_fee`                       | A processing fee, not used by most partners.
`supplier_uplift`                      | Uplift is an additional charge on top of the ticket price for an upgraded experience. This is the uplift component earned by the supplier.
`user_uplift`                          | This is the uplift component earned by the partner.


