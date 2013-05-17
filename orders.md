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
    "before": "2023-04-06T11:30:00.000Z",
    "after": "2023-04-06T11:30:00.000Z"
  },
  "description": "rubber chickens and chunky bacon",
  "web_hook_url": "http://api.myshop.com/deliveries/update_state",
  "recipient_email": "email.for@feedback.com"
  "external_id": "123-456-789",
  "packages": [{
    "width": 2,
    "height": 8.2,
    "length": 5,
    "weight": 2,
    "bundle": true,
    "description": "chunky bacon"
  }]
}
{% endhighlight %}

* `description` - a short description of the package(s) that are going to be delivered.
* `web_hook_url` - an optional param with an URL to be notified when the
  order's state changes. Please check the [Order Status](/order_status.html)
  to learn more about web hooks.
* `recipient_email` - an optional param, an email address used for feedback request. Feedback request is sent when order gets delivered
* `external_id` - an optional param with custom id that enables connecting
  tiramizoo orders with your internal infrastructure. This id does not need
  to be unique and can have variable length. (max length: 255 chars)
* `merge_field` - an optional string value that will be passed to your order. (max length: 65535 chars)
* `courier_information` - an optional information for courier. This can be used for giving special explanations about
  the pickup or delivery process or to specify a pickup code to let the courier prove his identity (e.g.
  "on pickup use identification code ABC" with a randomly generated code in place of ABC). (max length: 255 chars)
* `packages` (`items` will be deprecated) - an array containing measurements of at least one package.
  `width`, `height`, `length` are required dimensions of your package in cm and `weight` in kg. Optional `quantity` parameter contains a number of packages to deliver (default is 1). Optional `bundle` parameter determines if package can be pack with others into user's package presets ([more about Packing Service](/packing_service.html))
* `pickup`, `delivery` are required parameters cointaining addresses from where to pick your packages up and where to deliver them and information about people responsible for sending and receiving the delivery.
  * `name` - required string containing name (max length: 255 chars)
  * `phone_number` - required string containing phone number
  * `email` - optional string containing email address
  * `address_line` - required string containing street name and building number. (max length: 255 chars)
  * `postal_code` - required string containing postal code
  * `country_code` - required string containing country code

* `pickup` and `delivery` `before` and  `after` - required date and time defining pickup window provided as UTC in [ISO 8601](http://en.wikipedia.org/wiki/ISO_8601) format. Distance between `delivery[before]` and `pickup[after]` must be at least 90 minutes but not longer than 6 hours.

#### Response

* `201 Created` - An Order was created, response body contains its
  representation. Courier will deliver the package according to specified
  params.

Response body contains the same information as in [Order Show request](/orders.html#show_order),

##### Try it yourself #####
{% highlight bash %}
curl -v -H 'Content-Type: application/json' -d '{"pickup":{"address_line":"Im Dol 1","postal_code":"14195","country_code":"de","name":"Alice Icealay","phone_number":"+491234567890","email":"alice@icealay.de","before":"2023-04-06T10:00:00.000Z","after":"2023-04-06T10:00:00.000Z"},"delivery":{"address_line":"Thujaweg 1","postal_code":"12437","country_code":"de","name":"Bob Obbay","phone_number":"+490987654321","email":"bob@obbay.de","before":"2023-04-06T11:30:00.000Z","after":"2023-04-06T11:30:00.000Z"},"description":"rubber chickens and chunky bacon","web_hook_url":"http://api.myshop.com/deliveries/update_state","recipient_email":"email.for@feedback.com","external_id":"123-456-789","packages":[{"width":2,"height":8.2,"length":5,"weight":2,"description":"chunky bacon"}]}' "https://api-sandbox.tiramizoo.com/v1/orders?api_token=5715edce6630959b0e9c5659d323eae4"
{% endhighlight %}

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

##### Try it yourself #####
{% highlight bash %}
curl -v -H 'Content-Type: application/json' -d '{"pickup":{"address_line":"Im Dol 1","postal_code":"14195","country_code":"de","phone_number":"+491234567890","email":"alice@icealay.de","before":"2023-04-06T10:00:00.000Z","after":"2023-04-06T10:00:00.000Z"},"delivery":{"address_line":"Thujaweg 1","postal_code":"12437","country_code":"de","name":"Bob Obbay","phone_number":"+490987654321","email":"bob@obbay.de","before":"2023-04-06T11:30:00.000Z","after":"2023-04-06T11:30:00.000Z"},"description":"rubber chickens and chunky bacon","web_hook_url":"http://api.myshop.com/deliveries/update_state","external_id":"123-456-789","packages":[{"width":2,"height":8.2,"length":5,"weight":2,"description":"chunky bacon"}]}' "https://api-sandbox.tiramizoo.com/v1/orders?api_token=5715edce6630959b0e9c5659d323eae4"
{% endhighlight %}

### List Orders

```
GET /orders?external_id=EXTERNAL_ID&page=PAGE&per_page=PER_PAGE&api_token=API_TOKEN
```

external_id - an optional param with custom id that enables connecting tiramizoo orders with your internal infrastructure. This id does not need
  to be unique and can have variable length.

page     - an optional param (by default first page is returned)
per_page - an optional param (by default 100 records are returned. Max number allowed is 1000)

#### Response

* `200 OK`

Example response:

{% highlight javascript %}
{
  "page": 1,
  "total_pages": 1,
  "per_page": 100,
  "orders": [
    {
      "uuid": "a505ff78-67c5-4fbd-ab4f-a8db81f2c45a",
      "state": "processing",
      "created_at": "2013-03-12T11:13:46+01:00",
      "description": "rubber chickens and chunky bacon",
      "courier_information": null,
      "external_id": "123-456-789",
      "merge_field": null,
      "web_hook_url": "http://api.myshop.com/deliveries/update_state",
      "recipient_email": "email.for@feedback.com",
      "packages_price": null,
      "tracking_url": "https://sandbox.tiramizoo.com/orders/10018A5TBYKI4/tracking_status",
      "tracking_code": "10018-A5TB-YKI4",
      "cancellable": true,
      "hazard_index": 0,
      "city_identifier": "berlin",
      "signature": {
        "url": null,
        "name": null
      },
      "pickup": {
        "name": "Alice Icealay",
        "phone_number": "+491234567890",
        "email": "alice@icealay.de",
        "address_line": "Im Dol 1",
        "city": "Berlin",
        "postal_code": "14195",
        "country_code": "de",
        "after": "2023-04-06T12:00:00+02:00",
        "before": "2023-04-06T12:00:00+02:00"
      },
      "delivery": {
        "name": "Bob Obbay",
        "phone_number": "+490987654321",
        "email": "bob@obbay.de",
        "address_line": "Thujaweg 1",
        "city": "Berlin",
        "postal_code": "12437",
        "country_code": "de",
        "after": "2023-04-06T13:30:00+02:00",
        "before": "2023-04-06T13:30:00+02:00"
      },
      "packages": [
        {
          "width": 2,
          "height": 8.2,
          "length": 5,
          "weight": 2,
          "quantity": 1,
          "description": "chunky bacon",
          "identifier": "3E39C50E40226436",
          "qr_code_url": "/api/v1/qr_codes/3E39C50E40226436"
        }
      ],
    },
    {
      "uuid": "29ab1488-d1e8-4afb-b08c-dca142348f4d",
      "state": "delivered",
      "created_at": "2013-03-10T15:22:37+01:00",
      ...
    },
    ...
  ]
}
{% endhighlight %}

##### Try it yourself #####
{% highlight bash %}
curl -v "https://api-sandbox.tiramizoo.com/v1/orders?external_id=123-456-789&page=1&api_token=5715edce6630959b0e9c5659d323eae4"
{% endhighlight %}


#### Errors

* `401 Unauthorized` - Request requires authentication

##### Try it yourself #####
{% highlight bash %}
curl -v "https://api-sandbox.tiramizoo.com/v1/orders?external_id=123-456-789&page=1"
{% endhighlight %}

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
  "uuid": "a505ff78-67c5-4fbd-ab4f-a8db81f2c45a",
  "state": "processing",
  "created_at": "2013-03-12T11:13:46+01:00",
  "description": "rubber chickens and chunky bacon",
  "courier_information": null,
  "external_id": "123-456-789",
  "merge_field": null,
  "web_hook_url": "http://api.myshop.com/deliveries/update_state",
  "recipient_email": "email.for@feedback.com",
  "packages_price": null,
  "tracking_url": "https://sandbox.tiramizoo.com/orders/10018A5TBYKI4/tracking_status",
  "tracking_code": "10018-A5TB-YKI4",
  "cancellable": true,
  "hazard_index": 0,
  "city_identifier": "berlin",
  "signature": {
    "url": null,
    "name": null
  },
  "pickup": {
    "name": "Alice Icealay",
    "phone_number": "+491234567890",
    "email": "alice@icealay.de",
    "address_line": "Im Dol 1",
    "city": "Berlin",
    "postal_code": "14195",
    "country_code": "de",
    "after": "2023-04-06T12:00:00+02:00",
    "before": "2023-04-06T12:00:00+02:00"
  },
  "delivery": {
    "name": "Bob Obbay",
    "phone_number": "+490987654321",
    "email": "bob@obbay.de",
    "address_line": "Thujaweg 1",
    "city": "Berlin",
    "postal_code": "12437",
    "country_code": "de",
    "after": "2023-04-06T13:30:00+02:00",
    "before": "2023-04-06T13:30:00+02:00"
  },
  "packages": [
    {
      "width": 2,
      "height": 8.2,
      "length": 5,
      "weight": 2,
      "quantity": 1,
      "description": "chunky bacon",
      "identifier": "3E39C50E40226436",
      "qr_code_url": "/api/v1/qr_codes/3E39C50E40226436"
    }
  ],
}
{% endhighlight %}

##### Try it yourself #####
{% highlight bash %}
curl -v https://api-sandbox.tiramizoo.com/v1/orders/a505ff78-67c5-4fbd-ab4f-a8db81f2c45a?api_token=5715edce6630959b0e9c5659d323eae4
{% endhighlight %}


#### Errors

* `404 Not Found` - Order with provided UUID was not found
* `401 Unauthorized` - Request requires authentication

##### Try it yourself #####
{% highlight bash %}
curl -v https://api-sandbox.tiramizoo.com/v1/orders/WRONG_UUID?api_token=5715edce6630959b0e9c5659d323eae4
{% endhighlight %}


### Cancel Order

Only orders in cancellable state can be cancelled

{% highlight javascript %}
{
  // ...
  "cancellable": true
  // ...
}
{% endhighlight %}


```
PUT /orders/:uuid?api_token=API_TOKEN
```

* `uuid` - uniqe order identifier

{% highlight javascript %}
{
  "state": "cancelled"
}
{% endhighlight %}


#### Response

* `200 OK` - An Order was updated, response body contains order
  representation.

##### Try it yourself #####
* `ORDER_UUID` - replace with uuid of order in cancellable state. **Tip:** example for new order creation can be used

{% highlight bash %}
curl -v -X PUT -H 'Content-Type: application/json' -d '{"state" : "cancelled" }' https://api-sandbox.tiramizoo.com/v1/orders/ORDER_UUID?api_token=5715edce6630959b0e9c5659d323eae4
{% endhighlight %}


#### Errors

* `422 Validation Error` - order is not in cancellable state.

{% highlight javascript %}
{
  "message": "validation error",
  "code": "validation_error",
  "errors": [
    {
      "field": "state",
      "code": "invalid",
      "message": "State is invalid"
    }
  ]
}
{% endhighlight %}

##### Try it yourself #####
{% highlight bash %}
curl -v -X PUT -H 'Content-Type: application/json' -d '{"state" : "cancelled" }' https://api-sandbox.tiramizoo.com/v1/orders/351d11d3-a001-4422-9ba2-7141b69126c0?api_token=5715edce6630959b0e9c5659d323eae4
{% endhighlight %}