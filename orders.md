---
layout: page
title: Orders
---

The orders API provides a simple and quick way of order creation.
You need to specify the time when pickup and delivery should take place by giving a time window with `before`
and `after`.

All requests require authentication (valid **API_TOKEN**  [More](/#api_tokens) )

### Create Order

```
POST /orders?api_token=API_TOKEN
```
{% highlight javascript %}
{
  "pickup": {
    "address_line": "Im Dol 1",
    "postal_code": "14195",
    "country_code": "de",
    "name": "Alice Icealay",
    "phone_number": "+491234567890",
    "email": "alice@icealay.de",
    "before": "2023-04-06T10:00:00.000Z",
    "after": "2023-04-06T10:00:00.000Z"

  },
  "delivery": {
    "address_line": "Thujaweg 1",
    "postal_code": "12437",
    "country_code": "de",
    "name": "Bob Obbay",
    "phone_number": "+490987654321",
    "email": "bob@obbay.de",
    "before": "2023-04-06T11:30:00.000Z"
    "after": "2023-04-06T11:30:00.000Z"
  },
  "description": "rubber chickens and chunky bacon",
  "web_hook_url": "http://api.myshop.com/deliveries/update_state",
  "external_id": "123-456-789",
  "items": [{
    "width": 2,
    "height": 8.2,
    "length": 5,
    "weight": 2,
    "description": "chunky bacon"
  }]
}
{% endhighlight %}

* `description` - a short description of the item(s) that are going to
  be delivered.
* `web_hook_url` - an optional param with an URL to be notified when the
  order's state changes. Please check the [Order Status](/order_status.html)
  to learn more about web hooks.
* `external_id` - an optional param with custom id that enables connecting
  tiramizoo orders with your internal infrastructure. This id does not need
  to be unique and can have variable length. (max length: 255 chars)
* `merge_field` - an optional string value that will be passed to your order. (max length: 65535 chars)
* `courier_information` - an optional information for courier. (max length: 255 chars)
* `items` - an array containing measurements of at least one item.
  `width`, `height`, `length` are required dimensions of your package
  in cm and `weight` in kg. Optional `quantity` parameter contains a
  number of items to deliver (default is 1).
* `pickup`, `delivery` are required parameters cointaining addresses
   from where to pick your items up and where to deliver them and information
   about people responsible for sending and receiving the delivery.
  * `name` - required string containing name (max length: 255 chars)
  * `phone_number` - required string containing phone number
  * `email` - optional string containing email address
  * `address_line` - required string containing street name and
    building number. (max length: 255 chars)
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

### List Orders

```
GET /orders?external_id=EXTERNAL_ID&page=PAGE&api_token=API_TOKEN
```

external_id - an optional param with custom id that enables connecting tiramizoo orders with your internal infrastructure. This id does not need
  to be unique and can have variable length.

page - an optional param (by default first page is returned)

#### Response

* `200 OK`

Example response:

{% highlight javascript %}
{
  "page": 1,
  "total_pages": 5,
  "per_page": 25,
  "orders": [{
    "uuid": "71e7acb2-3ed2-4137-ad62-446fb44f4299",
    "state": "processing",
    "created_at": "2023-12-19T08:27:05+01:00",
    "description": "Original BUSH",
    "courier_information": null,
    "external_id": "44444444422222222",
    "merge_field": null,
    "web_hook_url": "http://api.myshop.com/deliveries/update_state",
    "items_price": null,
    "tracking_url": "https://api-sandbox.tiramizoo.com/orders/0FAABYLJUZIWW/tracking_status",
    "tracking_code": "0FAAB-YLJU-ZIWW",
    "cancellable": true,
    "signature": {
      "requested": false,
      "url": null,
      "name": null
    },
    "pickup": {
      "name": "Germany",
      "phone_number": "485001122112",
      "email": "some@gmail.com",
      "address_line": "Guntherstrasse 16",
      "postal_code": "80639",
      "country_code": "de",
      "after": "2023-12-19T20:00:00+01:00",
      "before": "2023-12-19T22:00:00+01:00"
    },
    "delivery": {
      "name": "Your Company Name",
      "phone_number": "217-8918712",
      "email": "another@tiramizoo.com",
      "address_line": "Maple Street 2425",
      "postal_code": "80331",
      "country_code": "de",
      "after": "2023-12-19T20:00:00+01:00",
      "before": "2023-12-19T21:30:00+01:00"
    },
    "items": [{
      "width": 20.0,
      "height": 30.0,
      "length": 40.0,
      "weight": 1.0,
      "quantity": 1,
      "description": "chunky bacon"
    }]
  }, {
    "uuid": "9afb760c-7a74-4e3c-9a4e-4258d969db00",
    "state": "processing",
    "created_at": "2023-12-18T20:59:17+01:00",
    "description": "Original BUSH",
    "courier_information": null,
    ...
  }
}
{% endhighlight %}

#### Errors

* `401 Unauthorized` - Request requires authentication


### Show Order

```
GET /orders/:uuid?api_token=API_TOKEN
```

uuid - uniqe order identifier

#### Response

* `200 OK`

Example response:

{% highlight javascript %}
{
  "uuid": "14bcab1a-b81d-483a-af86-04edcacd46aa",
  "state": "processing",
  "created_at": "2023-04-06T8:00:00.000Z",
  "description": "bottle of wine",
  "courier_information": "please knock, doorbell doesn't work",
  "external_id": "11-22-33-44-55",
  "merge_field": null,
  "web_hook_url": "http://api.myshop.com/deliveries/update_state",
  "items_price": 0,
  "tracking_url": "https://tiramizoo.com/orders/BN1HMDCTYGE11/tracking_status",
  "tracking_code": "BN1HM-DCTY-GE11",
  "cancellable": false,
  "signature": {
    "requested": false,
    "url": null,
    "name": null
  },
  "pickup": {
    "address_line": "Im Dol 1",
    "postal_code": "14195",
    "country_code": "de",
    "name": "Alice Muller",
    "phone_number": "+49000222333",
    "email": "deliver@germany.de",
    "after": "2023-04-06T10:00:00.000Z",
    "before": "2023-04-06T12:00:00.000Z"
  },
  "delivery": {
    "address_line": "Thujaweg 1",
    "postal_code": "12437",
    "country_code": "de",
    "name": "Bob Obama",
    "phone_number": "+49099999999",
    "email": "bob@obama.de"
    "after": "2023-04-06T14:00:00.000Z",
    "before": "2023-04-06T15:00:00.000Z"
  },
  "items": [
    {
      "width": 48,
      "height": 39,
      "length": 40,
      "weight": 20,
      "quantity": 1,
      "description": "chunky bacon"
    }
  ]
}
{% endhighlight %}

#### Errors

* `404 Not Found` - Order with provided UUID was not found
* `401 Unauthorized` - Request requires authentication

[Examples](/sandbox.html#bulk_orders)
