---
layout: page
title: Orders
---

Orders API provides a convenient interface to create an
order. To create an order, you need to have a valid quote created
using
[Quotes API](/quotes.html),
and pass a few additional details.

### Create Order

```
POST /orders
```

{% highlight javascript %}
{
  "quote": { ... },
  "pickup": {
    "name": "Alice Icealay",
    "phone_number": "+491234567890",
    "email": "alice@icealay.de"
  },
  "delivery": {
    "name": "Bob Obbay",
    "phone_number": "+490987654321",
    "email": "bob@obbay.de"
  },
  "description": "rubber chickens and chunky bacon",
  "web_hook_url": "http://api.myshop.com/deliveries/update_state",
  "external_id": "123-456-789",
  "items_price": 1999
}
{% endhighlight %}

* `quote` - a valid, unmodified quote object fetched from
  [Quotes API](/quotes.html).
* `pickup`, `delivery` - information about the people responsible for
  sending and receiving the delivery. `name` is required,
  `phone_number`, `company` and `email` are optional.
* `description` - a short description of the item(s) that are going to
  be delivered.
* `web_hook_url` - an optional param with an URL to be notified when the
  order's state changes. Please check
  [Web hooks API](/web_hooks.html)
  to learn more about Tiramizoo web hooks.
* `external_id` - an optional param with custom id that enables connecting
  tiramizoo orders with your internal infrastructure. This id does not need
  to be unique and can have variable length.
* `items_price` - an optional param with price in cents of items to be delivered.

#### Response

* `201 Created` - An Order was created, response body contains its
  representation. A Location header contains URL of the newly created
  Order. Courier will deliver the package according to specified
  params.

Response body contains the same information as in Show request.

#### Errors

* `422 Validation Error` - some of required parameters are missing or
  invalid in the request. Please fix parameters and retry the request.

{% highlight javascript %}
{
  "message": "validation error",
  "code": "validation_error",
  "errors": [
    "quote is invalid",
    "quote has expired"
  ]
}
{% endhighlight %}

### Show Order

```
GET /orders/:order_uuid
```

#### Response

* `200 Ok` - Returns order representation.

{% highlight javascript %}
{
  "uuid": "b966a74b-32a0-4ade-b864-e72472230cf2",
  "state": "awaiting_payment",
  "external_id": "123-456-789",
  "web_hook_url": "http://api.myshop.com/deliveries/update_state",
  "description": "rubber chickens and chunky bacon",
  "items_price": 1999,
  "pickup": {
    "name": "Alice Icealay",
    "phone_number": "+491234567890",
    "email": "alice@icealay.de"
  },
  "delivery": {
    "name": "Bob Obbay",
    "phone_number": "+490987654321",
    "email": "bob@obbay.de"
  },
  "quote": { ... }
}
{% endhighlight %}

#### Possible states

* `awaiting_payment` - initial state
* `payment_failed`
* `dispatched`
* `picked_up`
* `delivered`
* `pickup_unreachable`
* `delivery_unreachable`

#### Errors

* `404 Not Found`

### List Orders

#### Request

Request is authenticated. Orders are returned in descending order of creation date. Response includes 25 orders maximum, rest of results can be accessed using `page` param.

```
GET /orders
```

Parameters:

* `external_id` - Return results containing `external_id` only.
* `page` - Specifies the page of results to retrieve.

#### Response

* `200 Ok` - Returns orders list and pagination information.

{% highlight javascript %}
{
  "total_pages": 5,
  "per_page": 25,
  "page": 1,
  "orders": [
    { ... }
  ]
}
{% endhighlight %}
