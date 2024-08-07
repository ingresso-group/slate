# Orders Sold
> **Definition**

```
GET https://api.ticketswitch.com/f13/orders_sold.v1
```

### Request

> **Example request**

```shell
curl https://api.ticketswitch.com/f13/orders_sold.v1 \
    -u "demo:demopass" \
    -d "page_length=10" \
    -d "page_number=0" \
    -d "include_purchased=true" \
    -d "include_failed=false" \
    -d "exclude_tests=false" \
    -d "exclude_refunds=true" \
    -d "exclude_non_live=true" \
    -d "exclude_add_ons=true" \
    -d "earliest_purchase_time=2022-05-10T00:00:00Z" \
    -d "latest_purchase_time=2022-05-12T00:00:00Z" \
    --compressed \
    -G
```

<!-- TODO: document exclude_tests=true  -->
