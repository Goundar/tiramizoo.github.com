---
layout: page
title: Direct Orders
---

Direct Orders API provides a simple and quick way of of order creation.
You need to specify time when pickup should take place, then delivery will happen
in next 2-3 hours.
If you would like to have more control over pickup/delivery time windows, refer to
[Create Order API](/orders.html#create_order).

Create Direct Order
-------------------

### Request

```
POST /direct_orders
```

{% highlight javascript %}
{
  "pickup":  {
    "address_line_1": "Im Dol 1",
    "city": "Berlin",
    "postal_code": "14195",
    "country_code": "de",
    "name": "Alice Icealay",
    "phone_number": "+491234567890",
    "email": "alice@icealay.de",
    "at": "2012-04-06T10:00:00.000Z"
  },
  "delivery": {
    "address_line_1": "Thujaweg 1",
    "address_line_2": "Ground floor",
    "city": "Berlin",
    "postal_code": "12437",
    "country_code": "de",
    "name": "Bob Obbay",
    "phone_number": "+490987654321",
    "email": "bob@obbay.de"
  },
  "description": "rubber chickens and chunky bacon",
  "web_hook_url": "http://api.myshop.com/deliveries/update_state",
  "external_id": "123-456-789",
  "items": [
    {
      "width": 2,
      "height": 8.2,
      "length": 5,
      "weight": 2
    }
  ]
}
{% endhighlight %}

* `description` - a short description of the item(s) that are going to
  be delivered.
* `web_hook_url` - an optional param with an URL to be notified when the
  order's state changes. Please check
  [Web hooks API](/web_hooks.html)
  to learn more about Tiramizoo web hooks.
* `external_id` - an optional param with custom id that enables connecting
  tiramizoo orders with your internal infrastructure. This id does not need
  to be unique and can have variable length.
* `items` - an array containing measurements of at least one item.
  `width`, `height`, `length` are required dimensions of your package
  in cm and `weight` in kg. Optional `quantity` parameter contains a
  number of items to deliver (default is 1).
* `pickup`, `delivery` are required parameters cointaining addresses
   from where to pick your items up and where to deliver them and information
   about people responsible for sending and receiving the delivery.
  * `name` - required string containing name
  * `phone_number` - optional string containing phone number
  * `email` - optional string containing email address
  * `address_line_1` - required string containing street name and
    building number.
  * `address_line_2` - may contain some additional information for
    courier to locate the address, i.e. flat number, room number, etc.
  * `city` - optional string containing city name
  * `postal_code` - required string containing postal code
  * `country_code` - required string containing country code
* `pickup` `at` - required date of pickup at least 1 hour ahead. Provided as UTC in [ISO 8601](http://en.wikipedia.org/wiki/ISO_8601) format.

### Response

* `201 Created` - An Order was created, response body contains its
  representation. A Location header contains URL of the newly created
  Order. Courier will deliver the package according to specified
  params.

Response body contains the same information as in [Order Show request](/orders.html#show_order),

### Errors

* `422 Validation Error` - some of required parameters are missing or
  invalid in the request. Please fix parameters and retry the request.

{% highlight javascript %}
{
  "message": "validation error",
  "code": "validation_error",
  "errors": [
    "pickup_at is invalid"
  ]
}
{% endhighlight %}
