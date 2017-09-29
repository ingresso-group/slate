# Transaction Reporting System 
> **Definition**

```
POST https://transactions.ticketswitch.io/transreport
```


The service allows retrieval, filtering and ordering of all the transactions made for
the given account. 

## Authorization
Transaction reporting system uses the same basic authorization as [api.ticketswitch.io](api.ticketswtich.io) API, when retrieving data in the browser you will be prompted to enter you api credentials, when using curl you are required to supply credentials using `-u` flag, for example : ` -u "demo:demopass"`. In the case of manually prepared requests you will need to add additional header in a form: `Authorization: Basic xxx` where `xxx` is a base64 encoded string containing username and password in a form of `username:password`.

```shell
curl https://transactions.ticketswitch.io/transreport\
    -u "demo:demopass" \
    -X GET
```

## Pagination
By default returned dataset is paginated and limited to 10 records per page. To iterate over the pages supply `page_number=x` query parameter to the request where `x` is unsigned integer, if this parameter is not passed the page will be set to 0 by default. To change number of records returned per page pass `page_length=y`, `y` being an integer. Maximum number of records per page is 100.

```shell
curl https://transactions.ticketswitch.io/transreport?page_number=2&page_length=20\
    -u "demo:demopass" \
    -X GET
```

## Limiting timespan for retrieved records
Transactions API allows passing `from` and `to` query parameters that limit the time for which the data will be retrieved. The time values supplied can either be passed as UTC timestamp or as a more human readable format in simplified **ISO 8601** in a form of `<YYYY>-<MM>-<dd>T<hh>:<mm>:<ss>` where hours, minutes and seconds are optional and will default to either 00, 00, 00 in the case of `from` parameter or 23, 59, 59 in case of `to` if not passed.
For example assuming we want to retrieve the records `from` September 29th 2017 at midnight (00:00:00) following time parameter values are valid:
- 1506643200
- 2017-09-29T00:00:00
- 2017-09-29T00:00
- 2017-09-29T00
- 2017-09-29


```shell
curl https://transactions.ticketswitch.io/transreport?from=2016-01-02T14:33:22&to=2016-01-03T14:25:21\
    -u "demo:demopass" \
    -X GET
```

```shell
curl https://transactions.ticketswitch.io/transreport?from=1506623211&to=1506643200\
    -u "demo:demopass" \
    -X GET
```

## Filtering the data
The API also allows filtering the data so that only records for which keys match specifified value will be returned.
Currently transactions API allows following keys to be filtered by:
- `event_id`
- `supplier_code`

To pass filter option insert `/filter:<filter_options>` path after `/transreport` endpoint with selected filter options,
filter options are passed in a form `key=value` separated by `,` for example `/filter:event_id=44AP,supplier_code=nimax`,
the option separator corresponds to the `and` logical statement where key value should correspond to key name in returned
transaction record, this means that in the above example only records with `event_id` equal to `44AP` and
`supplier_code` with `nimax` value will be returned. If the value of the field option contains white space enclose it within single quote `'`.

``` shell
curl https://transactions.ticketswitch.io/transreport/filter:event_id='44AP'\
    -u "demo:demopass" \
    -X GET
```


``` shell
curl https://transactions.ticketswitch.io/transreport/filter:event_id='44AP',supplier_code=nimax\
    -u "demo:demopass" \
    -X GET
```



## Ordering returned dataset

To order returned data insert `/order:<order_options>` path after `/transreport` endpoint with set of fields you want to order the data by. A
list of ordering fields should correspond to keys in returned transaction records separated by `,`. By default the data is ordered descending, if you 
wish to order it ascending prepend field name with `$` character, for example `$event_id,purchase_time_utc` will order data alphabetically by event id,
and if there are 2 records with same id those records will be ordered descending by purchase time. Currently you can order fields using following fields:
- `event_id`
- `supplier_code`
- `purchase_time_utc`
- `purchase_time_london`

``` shell
curl https://transactions.ticketswitch.io/transreport/order:event_id,supplier_code\
    -u "demo:demopass" \
    -X GET
```

## Dataset schema reference

Each transaction record returned from API should have following keys set:

| Name                                 | Description                                          |
|--------------------------------------|------------------------------------------------------|
| trans_id                             | Unique identifier for transaction                    |
| user_id                              | Ticketswitch user for which the transaction was made |
| backend_purchase_ref                 | Purchase reference from external ticketing system    |
| event_id                             | Id of the transaction's event                        |
| event_desc                           | Description of the event                             |
| event_country_code                   | Code for the event's country                         |
| venue_desc                           | Description of the event's venue                     |
| supplier_code                        | Name of the backend system purchase was made with    |
| purchase_time_utc                    | Purchase UTC time                                    |
| purchase_time_london                 | Purchase BST time                                    |
| performance_local_time               | Time and date of the performance                     |
| perf_type_code                       | Internal code                                        |
| ticket_type_code                     | Ticket type code for the order                       |
| ticket_type_desc                     | Description for the ticket type                      |
| send_code                            | Send method code                                     |
| send_desc                            | Description for the send method                      |
| seat_ids                             | Array containing list of purchased seats             |
| trans_status                         | Status of the transaction                            |
| sale_mode                            | Type of debitor used for purchase                    |
| debitor_reference                    | Reference of the debitor                             |
| user_can_use_customer_data           | Whether or not account can use customer data         |
| num_tickets                          | Number of purchased tickets                          |
| num_transactions                     | Number of transactions made                          |
| sale_currency_code                   | Currency in which sale was made                      |
| sale_seatprice                       | Base price for the seats                             |
| sale_surcharge                       | Total surcharge for the order                        |
| ticket_price                         | Total ticket price including surcharge               |
| send_cost                            | Send method cost                                     |
| commision_currency_code              | Currency in which commission was made                |
| user_commission_inc_vat              | ?                                                    |
| gross_commission_inc_vat             | ?                                                    |
| inside_commision_inc_vat             | ?                                                    |
| supplier_levy                        | ?                                                    |
| supplier_fee                         | ?                                                    |
| supplier_rebate                      | ?                                                    |
| surcharge_non_commissionable_postage | ?                                                    |
| surcharge_commissionable_postage     | ?                                                    |
| processing_fee                       | ?                                                    |
| supplier_uplift                      | ?                                                    |
| user_uplift                          | ?                                                    |


