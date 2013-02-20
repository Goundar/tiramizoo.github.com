---
layout: page
title: Bulk Orders
---

The orders API provides a simple and quick way of bulk order creation.
You need to specify the time when pickup and delivery should take place by giving a time window with `before`
and `after`.

All requests require authentication (valid **API_TOKEN**  [More](/#api_tokens) )

### Create Order

```
POST /bulk/orders?api_token=API_TOKEN
```
{% highlight javascript %}
[{
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
  "hazard_index": 23,
  "packages": [{
    "width": 2,
    "height": 8.2,
    "length": 5,
    "weight": 2,
    "quantity":1, 
    "description": "chunky bacon"
  }]
}]
{% endhighlight %}

* `description` - a short description of the packages(s) that are going to
  be delivered.
* `web_hook_url` - an optional param with an URL to be notified when the
  order's state changes. Please check the [Order Status](/order_status.html)
  to learn more about web hooks.
* `external_id` - an optional param with custom id that enables connecting
  tiramizoo orders with your internal infrastructure. This id does not need
  to be unique and can have variable length. (max length: 255 chars)
* `merge_field` - an optional string value that will be passed to your order. (max length: 65535 chars)
* `courier_information` - an optional information for courier. (max length: 255 chars)
* `hazard index` - an optional number which represents Dangerous goods ADR 2013 points 
* `packages` - an array containing measurements of at least one package.
  `width`, `height`, `length` are required dimensions of your package
  in cm and `weight` in kg. Optional `quantity` parameter contains a
  number of packages to deliver (default is 1).
  `description` - optional description of the package 
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

* `201 Created`

Example response:

{% highlight javascript %}
{"page":1,
"total_pages":1,
"per_page":1,
"orders":[
  {"uuid":"ac2263ee-7c86-4d2b-9e63-0ea6552ad733",
  "state":"processing",
  "created_at":"2013-02-20T14:13:32+01:00",
  "description":"huge metal bowl",
  "courier_information":null,
  "external_id":null,
  "merge_field":null,
  "web_hook_url":null,
  "packages_price":null,
  "tracking_url":"http://api-sandbox.tiramizoo.com/orders/DRXDBQVLAO5DU/tracking_status",
  "tracking_code":"DRXDB-QVLA-O5DU",
  "cancellable":true,
  "hazard_index":0,
  "signature":{
    "url":null,
    "name":null
  },
  "pickup":
    {"name":"Sender_company",
    "phone_number":"123456",
    "email":null,
    "address_line":"Wasser 15",
    "city":null,
    "postal_code":"14195",
    "country_code":"de",
    "after":"2023-04-06T12:00:00+02:00",
    "before":"2023-04-06T12:00:00+02:00"
  },
  "delivery":
    {"name":"Receiver_company",
    "phone_number":"123456",
    "email":null,
    "address_line":"Bahnhof 32",
    "city":null,
    "postal_code":"14195",
    "country_code":"de",
    "after":"2023-04-06T13:30:00+02:00",
    "before":"2023-04-06T13:30:00+02:00"
  },
  "packages":[
    {"width":48.0,
    "height":39.0,
    "length":40.0,
    "weight":21.0,
    "quantity":1,
    "description":"metal bowl"}
  ]
  }]
}
{% endhighlight %}

#### Errors

* `404 Not Found` - Order with provided UUID was not found
* `401 Unauthorized` - Request requires authentication
* `422 Validation Error` - some of required parameters are missing or
  invalid in the request. Please fix parameters and retry the request.

In a situation when user try to save multi orders at once and just one is invalid, none of orders will be saved. 
The response for invalid order will contain error messages, for valid orders it will be empty array:
,as an example: 

{% highlight javascript %}
request: POST [valid, invalid, valid]
response: 
[
  [],
  [{
    "field" => "description",
    "code" => "blank",
    "message" => "Description can't be blank"
    }
  ],
  []
]
{% endhighlight %}



[Examples](/sandbox.html#bulk_orders)
