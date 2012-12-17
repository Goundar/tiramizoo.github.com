---
layout: page
title: Orders
---

The orders API provides a simple and quick way of order creation.
You need to specify the time when pickup and delivery should take place by giving a time window with `before`
and `after`.

### Create Order

```
POST /orders
```

{% highlight javascript %}
{
  "pickup":  {
    "address_line": "Im Dol 1",
    "city": "Berlin",
    "postal_code": "14195",
    "country_code": "de",
    "name": "Alice Icealay",
    "phone_number": "+491234567890",
    "email": "alice@icealay.de",
    "after": "2012-04-06T10:00:00.000Z",
    "before": "2012-04-06T11:00:00.000Z"
  },
  "delivery": {
    "address_line": "Thujaweg 1",
    "city": "Berlin",
    "postal_code": "12437",
    "country_code": "de",
    "name": "Bob Obbay",
    "phone_number": "+490987654321",
    "email": "bob@obbay.de",
    "after": "2012-04-06T14:00:00.000Z",
    "before": "2012-04-06T15:00:00.000Z"
  },
  "description": "rubber chickens and chunky bacon",
  "web_hook_url": "http://api.myshop.com/deliveries/update_state",
  "external_id": "123-456-789",
  "courier_information": "please knock, doorbell doesn't work",
  "merge_field": "my passed value",
  "items": [
    {
      "width": 2,
      "height": 8.2,
      "length": 5,
      "weight": 2,
      "quantity": 1
    }
  ]
}
{% endhighlight %}

* `description` - a short description of the item(s) that are going to
  be delivered.
* `web_hook_url` - an optional param with an URL to be notified when the
  order's state changes. Please check the [Order Status](/order_status.html)
  to learn more about web hooks.
* `external_id` - an optional param with custom id that enables connecting
  tiramizoo orders with your internal infrastructure. This id does not need
  to be unique and can have variable length.
* `courier_information` - an optional information for courier.
* `merge_field` - an optional string value that will be passed to your order.
* `items` - an array containing measurements of at least one item.
  `width`, `height`, `length` are required dimensions of your package
  in cm and `weight` in kg. Optional `quantity` parameter contains a
  number of items to deliver (default is 1).
* `pickup`, `delivery` are required parameters cointaining addresses
   from where to pick your items up and where to deliver them and information
   about people responsible for sending and receiving the delivery.
  * `name` - required string containing name
  * `phone_number` - required string containing phone number
  * `email` - optional string containing email address
  * `address_line` - required string containing street name and
    building number.
  * `city` - optional string containing city name
  * `postal_code` - required string containing postal code
  * `country_code` - required string containing country code

* `pickup` and `delivery` `before` and  `after` - required date and time defining pickup window provided as UTC in [ISO 8601](http://en.wikipedia.org/wiki/ISO_8601) format. Gap between `delivery[before]` and `pickup[after]` must be in 90 minutes to 6 hours.

#### Response

* `201 Created` - An Order was created, response body contains its
  representation. A Location header contains URL of the newly created
  Order. Courier will deliver the package according to specified
  params.

Response body contains the same information as in [Order Show request](/orders.html#show_order),

#### Errors

* `422 Validation Error` - some of required parameters are missing or
  invalid in the request. Please fix parameters and retry the request.

{% highlight javascript %}
{
  "message": "validation error",
  "code": "validation_error",
  "errors": [
    {
      "field": "pickup_name",
      "code": "blank",
      "message": "Pickup name can't be blank"
    }
  ]
}
{% endhighlight %}

### Show Order

```
GET /orders/uuid
```

#### Response

* `200 OK`

{% highlight javascript %}
{
  "uuid": "14bcab1a-b81d-483a-af86-04edcacd46ce",
  "state": "processing",
  "created_at": "2012-04-06T8:00:00.000Z",
  "description": "bottle of wine",
  "courier_information": "please knock, doorbell doesn't work",
  "external_id": null,
  "merge_field": null,
  "web_hook_url": null,
  "items_price": 0,
  "tracking_url": "https://tiramizoo.com/orders/BN1HMDCTYGE9K/tracking_status",
  "tracking_code": "BN1HM-DCTY-GE9K",
  "cancellable": false,
  "signature": {
    "requested": false,
    "url": null,
    "name": null
  },
  "pickup": {
    "address_line": "Im Dol 1",
    "city": "Berlin",
    "postal_code": "14195",
    "country_code": "de",
    "name": "Alice Icealay",
    "phone_number": "+491234567890",
    "email": "alice@icealay.de",
    "after": "2012-04-06T10:00:00.000Z",
    "before": "2012-04-06T12:00:00.000Z"
  },
  "delivery": {
    "address_line": "Thujaweg 1",
    "city": "Berlin",
    "postal_code": "12437",
    "country_code": "de",
    "name": "Bob Obbay",
    "phone_number": "+490987654321",
    "email": "bob@obbay.de"
    "after": "2012-04-06T14:00:00.000Z",
    "before": "2012-04-06T15:00:00.000Z"
  },
  "price": {
    "net": 664,
    "gross": 790,
    "currency": "EUR",
    "tax_rate": 19,
    "tax": 126
  },
  "items": [
    {
      "width": 48,
      "height": 39,
      "length": 40,
      "weight": 20,
      "quantity": 1
    }
  ]
}
{% endhighlight %}

#### Errors

* `404 Not Found` - Order with provided UUID was not found
* `401 Unauthorized` - Request requires authentication


[Examples](/sandbox.html#orders)