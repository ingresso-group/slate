# Trolley and Reservation

This section describes the method in which one or more ticket orders are created
inside a trolley, and the method used to attempt a reservation of those orders
into the source system.

The trolley has two main structures

A `bundle` represents all orders from a particular supplier. Orders are grouped
like this so that all items are ordered from the supplier at once - if the
supplier posts tickets on our behalf then separate items will be posted
together, and if the supplier processes payment all items in the bundle will be
paid for with a single payment. Reserving and purchasing items will also succeed
or fail together. 

An `order` represents a number of tickets to a particular `price band` and
`ticket type`, for a particular [performance](#performances) of an
[event](#events).

An `order` will contain multiple `ticket_order`s when more than one `discount
code` is used (for example ticket 1 and 2 have the ADULT discount code, and
tickets 3 and 4 have the CHILD discount code). In this example there will be two
`ticket_order`s: one for ADULT and one for CHILD.

The reservation 

## Creating a trolley

> **Definition**

```
GET https://api.ticketswitch.com/f13/trolley.v1/{username}?user_passwd={password}&perf_id={perfid}&ticket_type_code={tickettype}&price_band_code={priceband}&no_of_seats={numtickets}
```

The trolley it's self should be considered a wish list. No tickets are reserved
until the [reservation call](#making-a-reservation) is made. Having a ticket in
a trolley is not an indication that that ticket is actually available.

It is important to note that because items are added to a shopping trolley
*before* they are reserved, the first item added to the trolley is less likely
to still be available when you reserve the trolley. So trolleys have the benefit
that you can cross-sell extra items to the customer, but they have the downside
that it is less likely that the customer's selected seats will still be
available if you allow the customer to browse and book additional events. This
trade-off is for each partner to weigh up. Note that if you do not allow
customers to select seats this is less of a concern.



**`trolley` attributes:**

Attribute | Description
--------- | -----------
`trolley_contents` | See below for object detail.
`trolley_token` | The identifier for the trolley (this is an internal hash representing the items in the trolley, so the identifier will be the same for identical trolleys).


**`trolley_contents` attributes:**

Attribute | Description
--------- | -----------
`bundle` | See below for object detail.
`trolley_bundle_count` | The number of bundles in the trolley.
`trolley_order_count` | The number of orders in the trolley. There will be at least one order for every bundle.


**`bundle` attributes** 

Attribute | Description
--------- | -----------
`bundle_order_count` | The number of orders in this bundle.
`bundle_total_cost` | The total cost of this bundle.
`bundle_total_seatprice` | The total face value for this bundle.
`bundle_total_send_cost` | The despatch cost for this bundle (similar to a transaction fee). Items within the same bundle will be sent together.
`bundle_total_surcharge` | The total booking fee for this bundle.
`currency` | The [currency](#currency-object) of the bundle.
`order` | See below for object detail.
`source_desc` | The name of the supplier system that this bundle will be purchased from.


**`order` attributes:**

Attribute | Description
--------- | -----------
`event` | The [event](#event-object) for this order.
`item_number` | This remains constant as items are added or removed. 
`performance` | The [performance](#performance-object) for this order.
`price_band_code` | The code for a price band. To uniquely identify a price band you should take the combination of `ticket_type_code`
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
`discount_disallowed_seat_no_bitmask` | (TODO: what is this)
`no_of_seats` | The number of tickets in this ticket_order.
`sale_seatprice` | The face value per ticket in this ticket_order.
`sale_surcharge` | The booking fee per ticket in this ticket_order.
`total_sale_seatprice` | The total face value of all tickets in this ticket_order.
`total_sale_surcharge` | The total booking fee of all tickets in this ticket_order.


## Manage the Shopping Trolley

> **Definition**

```
GET https://api.ticketswitch.com/f13/trolley.v1/{username}?user_passwd={password}&perf_id={perfid}&ticket_type_code={tickettype}&price_band_code={priceband}&no_of_seats={numtickets}
```

This call is used for the following use cases:

- Add best available tickets to a new or existing shopping trolley
- Add specific seats to a new or existing shopping trolley
- Remove items from the trolley

Note that adding an order will remove anything in the trolley which would
prevent it being added. The common reasons why two orders cannot sit alongside
each other are:

- We currently don't allow a basket to contain multiple orders for the same
  performance. This is something we plan to change, if this is a problem for you
please let us know.
- Incompatible despatch methods for the same supplier. For example if you have
  specified "International Post" for one item, and "Collect From Box Office" for
  the next item, and they are both from the same supplier then these are
  incompatible and the first item will be removed from your trolley. This should
  be an uncommon occurence.
- Differing currencies - for example the first item added to the trolley uses
  the USD currency while the second item added uses the GBP currency. These are
  incompatible so the first item will be removed from your trolley.


> **Example request - adding best available tickets to a new trolley, specifying specific discount codes**

```shell
curl https://api.ticketswitch.com/f13/trolley.v1/demo \
        -d "user_passwd=demopass" \
        -d "perf_id=6IF-A7N" \
        -d "ticket_type_code=CIRCLE" \
        -d "price_band_code=C" \
        -d "no_of_seats=3" \
        -d "disc0=ADULT" \
        -d "disc1=CHILD" \
        -d "disc2=CHILD" \
        -G
```

> **Example request - adding specific seats to an existing trolley**

```shell
curl https://api.ticketswitch.com/f13/trolley.v1/demo \
        -d "user_passwd=demopass" \
        -d "trolley_token=k---bTur8DtD3TFVZR3ByQT-6tp2nUNjEM-Ecu16DM2wRLeKDpVncGguCbLv8Rqliw7CY" \
        -d "perf_id=3CVA-89" \
        -d "ticket_type_code=STD" \
        -d "price_band_code=C" \
        -d "no_of_seats=3" \
        -d "seat0=Z18" \
        -d "seat1=Z19" \
        -d "seat2=Z20" \
        -G
```

> **Example request - removing three orders from the trolley**

```shell
curl https://api.ticketswitch.com/f13/trolley.v1/demo \
        -d "user_passwd=demopass" \
        -d "trolley_token=k---bTur8DtD3TFVZR3ByQT-6tp2nUNjEM-Ecu16DM2wRLeKDpVncGguCbLv8Rqliw7CY" \
        -d "remove_items_list=2" \
        -G
```

> **Example request - view the current state of the trolley**

```shell
curl https://api.ticketswitch.com/f13/trolley.v1/demo \
        -d "user_passwd=demopass" \
        -d "trolley_token=k---bTur8DtD3TFVZR3ByQT-6tp2nUNjEM-Ecu16DM2wRLeKDpVncGguCbLv8Rqliw7CY" \
        -G
```

### Request

Parameter | Description
--------- | -----------
`discX` | Specify a discount code for ticket number X, with zero-based numbering (so to specify the CHILD discount code on the second ticket use `disc1=CHILD`). Note that illegal discount codes are replaced with legal ones.
`no_of_seats` | The number of tickets you want to add to the trolley.
`perf_id` | The performance identifier for the tickets that you want to add to your trolley.
`price_band_code` | The price band identifier for the tickets that you want to add to your trolley.
`remove_items_list` | A comma separated list of order `item_number`s that you want to remove.
`seatX` | Specify a specific seat for ticket number X, with zero-based numbering (so to specify seat A12 as the first ticket use `seat0=A12`). If seat numbers are not specified then when the trolley is later reserved you will receive best available seats.
`X_send_code` | Specify a send / despatch method for supplier system X. If this is not present it will default to the first send method. For example, to specify the POST send method for the nimax supplier system, use `nimax_send_code=POST`.
`ticket_type_code` | The ticket type identifier for the tickets that you want to add to your trolley.
`trolley_token` | The identifier for the trolley. This is used to add additional items to a trolley, or to reserve the items in a trolley.


Additional parameters, primarily for internal use:

Parameter | Description
--------- | -----------
`add_crypto_block` | `cypto_block`s are used in the old [Ingresso XML API](http://www.ingresso.co.uk/apidocs/). To make migration away from the XML API easier, if you include `add_crypto_block` a `crypto_block` will be added to the response. You can then take the crypto_block and trolley_token and pass these in to the XML API call make_reservation to reserve and later purchase tickets (so that the JSON API is used up to the basket stage and the XML API is used to complete the purchase).
`departure_date` | Specify a departure date for example `departure_date=20170214`.
`duration` | Specify a duration for hotel product, for example `duration=3`.
`promo_code` | Specifying a promo codes can unlock a special offer discount code. If you have a promo code it can be specified like this: `promo_code=FOO`. Note that this feature is not commonly used with partners.


### Response

Returns a [shopping trolley object](#shopping-trolley-object).
