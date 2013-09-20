---
layout: page
title: Overview
---

You can easily offer the Tiramizoo delivery service to your customers, within your current
e-commerce platform. Either download and install our [OXID plugin](https://github.com/tiramizoo/oxid-plugin) or do a custom integration by
programming against the public REST/JSON API. The API is "eat your own
dogfood" style - tiramizoo uses it internally for the "book a courier"
website, administration and plugins, so you can count that it is well maintained.

For a detailed overview on how the workflow with tiramizoo.com looks like, please go to [our website](https://www.tiramizoo.com).

For testing purposes use the [tiramizoo.com sandbox](/sandbox.html). It's a full copy of the production environment
except no delivery you trigger there is dispatched to our couriers.

The tiramizoo.com API uses the **HTTP** protocol for communication and **JSON**
for serialization of request and response bodies.  All URLs start with
`https://api.tiramizoo.com/`. **SSL** is mandatory.

Any programming language used in a web development context will usually
feature libraries for conveniently accessing a [restful](http://en.wikipedia.org/wiki/Representational_state_transfer) [JSON](http://en.wikipedia.org/wiki/JSON) web service - so do
PHP, Ruby, Java, .Net and Python to name only a few. Integrating restful
JSON services usually is also much faster and easier than working with
[SOAP](http://en.wikipedia.org/wiki/SOAP) and other XML based or stateful services.

If you encounter a problem when using the API, please
[let us know](https://github.com/tiramizoo/tiramizoo.github.com/issues/new).

### API Tokens

For accessing the API - either via a plugin or via the API directly - you
will need an API token.

The API token on the sandbox and production systems is different - you will need to register separate accounts on both systems.

Please register a test account at [sandbox.tiramizoo.com](https://sandbox.tiramizoo.com)
with username and password or via facebook or google in order to get one. The API token
can be found via Profile / Edit profile when logged in.

When ready with the integration please register an account on our production system at [tiramizoo.com](https://tiramizoo.com).

To simplify integration, in some examples the test user's API token is used.

### Versioning

All API urls have to be prefixed with the API version, e.g. `/v1/orders`.


### Authentication

Some API calls require authentication with an API token. We accept API
tokens as a URL param named `api_token`. You must keep the API token
secret. You can always request a new API token.
In case of providing a wrong token, an `401 Unauthorized` response will be returned.


### Making Requests

The following examples use the `curl` shell command and is rendered as a
single copy-pastable line with a multiline representation for easy
reading.

A minimal `POST` request to the **quotes** resource which doesn't require
authentication:

{% highlight bash %}
curl -v -H 'Content-Type: application/json' -d '{
  "pickup_postal_code": "14195",
  "delivery_postal_code": "12437"
}' "https://api-sandbox.tiramizoo.com/v1/quotes"
{% endhighlight %}

##### Try it #####
{% highlight bash %}
curl -v -H 'Content-Type: application/json' -d '{"pickup_postal_code":"14195","delivery_postal_code":"12437"}' "https://api-sandbox.tiramizoo.com/v1/quotes"
{% endhighlight %}


### Airbrake support

Additionaly you can provide your AIRBRAKE_API_KEY ([More about Airbrake](https://airbrake.io/pages/home) ) on [www.tiramizoo.com/dashboard/account/edit](https://sandbox.tiramizoo.com/dashboard/account/edit) and in a case a web hook notification fails we will send you an error notification to your airbrake account


Orders
=======
---------------------------------------

The orders API provides a simple and quick way of order creation.
You need to specify the time when delivery should take place by giving a time window with `before`
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

* `delivery` `before` and `after` required date and time defining time window provided as UTC in [ISO 8601](http://en.wikipedia.org/wiki/ISO_8601) format. Valid values can be retrieved from [Service Areas API](/service_areas.html). If different values are provied they will be changed to first matching by `delivery` `before`. Information about the change will be logged in `courier_information` field.
To deliver as fast as possible using **standard delivery**, set `delivery` `after` to 1 hour from now and
`delivery` `before` to 3 hours from now.
To delivery as fast as possible using **express delivery**, set `delivery` `after` to now and `delivery` `before` to 2 hours from now.

* `pickup` `after` optional date and time defining when package is ready for pickup

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
          "barcode": {
            "symbology": "Code 128",
            "data": "3E39C50E40226436",
            "url": "https://api-sandbox.tiramizoo.com/v1/barcodes/3E39C50E40226436.png"
          }
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
      "barcode": {
        "symbology": "Code 128",
        "data": "3E39C50E40226436",
        "url": "https://api-sandbox.tiramizoo.com/v1/barcodes/3E39C50E40226436.png"
      }
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



Service Areas
======
------------------

### List Service Areas

The API returns postal codes and time windows for next 7 days of covered areas.


```
GET /v1/service_areas?api_token=API_TOKEN
```

* `api_token` - API TOKEN is optional. When given system checks if user has custom time windows specified. If yes, user's custom time windows are returned otherwise fallbacks to default time windows.
* `express_from` - **\[ISO8601 Date URI escaped\]** needs to be combined with `express_to` parameter. Allows to specify start time for express time windows.
* `express_to` - **\[ISO8601 Date URI escaped\]** does not need to be combined with `express_from` parameter, in this case `express_from` is set to current time. Allows to specify end time for express time windows. Can not exceed 7 days limit.
* `standard_from` - **\[ISO8601 Date URI escaped\]** needs to be combined with `standard_to` parameter. Allows to specify start time for standard time windows.
* `standard_to` - **\[ISO8601 Date URI escaped\]** does not need to be combined with `standard_from` parameter, in this case `standard_from` is set to current time. Allows to specify end time for express time windows. Can not exceed 7 days limit.

#### Response

* `200 OK`

Example response:

{% highlight javascript %}
[
  {
    "postal_codes": ["50667", "50667", ...],
    "time_windows": [{
      "delivery_type": "express",
      "pickup":{
        "from":"2013-02-20T08:00:00Z",
        "to":"2013-02-20T09:30:00Z"
      },
      "delivery":{
        "from":"2013-02-20T08:00:00Z",
        "to":"2013-02-20T09:30:00Z"
      }
    },
    {...}]
  },
  {...}
]
{% endhighlight %}

* `postal_codes` - all supported postal codes from an area.
* `time_windows["delivery_type"]` - delivery types are [**standard**, **weekend**, **standard_weekend**, **express_weekend**]. Delivery type has influence on price. Therefore, it is useful for final customer price calculation. By default only one **express time window** per area is returned. If you need more please use `express_from` and `express_to` parameters described above.

#### Errors ####

* `404 Not Found` - Area with given postal code is not supported
* `400 Bad Request` - Given range date is invalid or range is over 7 days limit

##### Try it yourself #####
{% highlight bash %}
curl -v https://api-sandbox.tiramizoo.com/v1/service_areas?api_token=5715edce6630959b0e9c5659d323eae4
{% endhighlight %}

{% highlight bash %}
# without api_token
curl -v https://api-sandbox.tiramizoo.com/v1/service_areas
{% endhighlight %}

{% highlight bash %}
# express_from and express_to
# `EXPRESS_FROM` - date in range: "now..1 week", format example: 2023-04-07T10%3A00%3A00.000Z
# `EXPRESS_TO` - date in range: "now..1 week", format example: 2023-04-07T10%3A00%3A00.000Z
curl -v https://api-sandbox.tiramizoo.com/v1/service_areas?express_from=EXPRESS_FROM&express_to=EXPRESS_TO
{% endhighlight %}

{% highlight bash %}
# only express_to - express_from will be set to current time by default
# `EXPRESS_FROM` - date in range: "now..1 week", format example: 2023-04-07T10%3A00%3A00.000Z
# `EXPRESS_TO` - date in range: "now..1 week", format example: 2023-04-07T10%3A00%3A00.000Z
curl -v https://api-sandbox.tiramizoo.com/v1/service_areas?express_to=EXPRESS_TO
{% endhighlight %}

{% highlight bash %}
# invalid date for range
curl -v https://api-sandbox.tiramizoo.com/v1/service_areas?express_to=INVALID_DATE
{% endhighlight %}

{% highlight bash %}
# range exceeds limit
curl -v https://api-sandbox.tiramizoo.com/v1/service_areas?express_to=2050-04-07T10%3A00%3A00.000Z
{% endhighlight %}

### Show Service Area ###

Return area containing given postal code.

```
GET /v1/service_areas/:postal_code?api_token=API_TOKEN
```

* `api_token` - API TOKEN is optional. When given system checks if user has custom time windows specified. If yes, user's custom time windows are returned otherwise fallbacks to default time windows.
* `postal_code` - postal code which belongs to desired service area
* `express_from` - **\[ISO8601 Date URI escaped\]** needs to be combined with `express_to` parameter. Allows to specify start time for express time windows.
* `express_to` - **\[ISO8601 Date URI escaped\]** does not need to be combined with `express_from` parameter, in this case `express_from` is set to current time. Allows to specify end time for express time windows. Can not exceed 7 days limit.
* `standard_from` - **\[ISO8601 Date URI escaped\]** needs to be combined with `standard_to` parameter. Allows to specify start time for standard time windows.
* `standard_to` - **\[ISO8601 Date URI escaped\]** does not need to be combined with `standard_from` parameter, in this case `standard_from` is set to current time. Allows to specify end time for express time windows. Can not exceed 7 days limit.

#### Response

* `200 OK`

Example response:

{% highlight javascript %}
{
  "postal_codes": ["50667", "50667", ...],
  "time_windows": [{
    "delivery_type": "express",
    "pickup": {
      "from": "2013-02-20T08:00:00Z",
      "to": "2013-02-20T09:30:00Z"
    },
    "delivery": {
      "from": "2013-02-20T08:00:00Z",
      "to": "2013-02-20T09:30:00Z"
    }
  },
  {...}]
}
{% endhighlight %}

#### Errors ####

* `404 Not Found` - Area with given postal code is not supported
* `400 Bad Request` - Given range date is invalid or range is over 7 days limit

##### Try it yourself #####
{% highlight bash %}
curl -v https://api-sandbox.tiramizoo.com/v1/service_areas/14195?api_token=5715edce6630959b0e9c5659d323eae4
{% endhighlight %}

##### Try it yourself #####
{% highlight bash %}
curl -v https://api-sandbox.tiramizoo.com/v1/service_areas/INVALID
{% endhighlight %}

{% highlight bash %}
# express_from and express_to
# `EXPRESS_FROM` - date in range: "now..1 week", format example: 2023-04-07T10%3A00%3A00.000Z
# `EXPRESS_TO` - date in range: "now..1 week", format example: 2023-04-07T10%3A00%3A00.000Z
curl -v https://api-sandbox.tiramizoo.com/v1/service_areas/14195?express_from=EXPRESS_FROM&express_to=EXPRESS_TO
{% endhighlight %}



Web Hooks
======
-----------------

You can easily avoid polling by giving an additional `web_hook_url` parameter when
creating an order. When you do so, we will `POST` you the response back to the
provided URL. To ensure that your API has processed the hook, we'll check the
response code of the `POST` request. If you return a response code other than
`2xx` we assume that the hook wasn't processed correctly and it will be
retried in a short period of time. We will retry with an
exponential backoff using the formula
`retry_count ** 4 + 15` (i.e. 15, 16, 31, 96, 271, ... seconds).
It will perform 25 retries over approximately 20 days.

Currently we support web hooks for Orders only. The response body of such web hook
request contains exactly the same fields as the response body from Get /orders/:order_uuid request.

Keep in mind that the order in which we `POST`
those status changes is not guaranteed, though usually they are ordered by the
timestamp of the status change.




Package labelling process
========
------------------

On [order creation process](/orders.html) tiramizoo service generates unique identifier for each package.

![Generate identifier for a package](/assets/images/barcode_generation_process.png)


##### Packages representation #####
{% highlight javascript %}
"packages": [
  {
    "width": 2,
    "height": 8.2,
    "length": 5,
    "weight": 2,
    "quantity": 1,
    "description": "chunky bacon",
    "identifier": "3E39C50E40226436",
    "barcode:": {
      "symbology": "Code 128",
      "data": "3E39C50E40226436",
      "url": "https://api-sandbox.tiramizoo.com/v1/barcodes/3E39C50E40226436.png"
    }
  },
  ...
]
{% endhighlight %}

 * `barcode.symbology` - Barcode symbology applied ([Code 128](http://en.wikipedia.org/wiki/Code_128))
 * `barcode.data`      - Data encoded in barcode
 * `barcode.url`       - URL to download barcode label


##### Example barcode #####
![Barcode](https://www.tiramizoo.com/api/v1/barcodes/3E39C50E40226436.png)




Packing Service
=======
---------------

Tiramizoo offers a packing service when creating orders.

The reasoning behind this is that tiramizoo.com will deliver and charge based on the number and size of packages sent. Therefore it is vital to submit the correct number of packages to tiramizoo.

To help our customers with packing, we provide a tool that will try to pack products into customizable packages.

There are two different settings influencing the pricing and packing:

* The account's package sizes contain dimensions, weights and prices that were negotiated in the contracts. Those are set up by your key account manager and can not be changed by the account owner.
* The account's package presets contain user specific package sizes and dimensions that can be given a special name. Those presets will be used in the user interface and also be used to pack products if the packing service is used. The price for the package presets will be taken from the smallest package size where it fits into. Package presets can be customized by the account owner in the user interface.

Just mark packages (in reality probably single products) which are supposed to be repacked and the system will pack them into the account's package presets and return the packing information.

We will then charge according to those packages, but the courier will also only expect to get those packages to deliver, so make sure they match.

### Create Order

```
POST /orders?api_token=API_TOKEN
```
{% highlight javascript %}
{
  "pickup": {
    ...
  },
  "delivery": {
    ...
  },
  ...
  "packages": [
    {
      "width": 2,
      "height": 8.2,
      "length": 5,
      "weight": 2,
      "bundle": true,
      "description": "Package 1 to be repacked"
    },
    {
      "width": 2,
      "height": 8.2,
      "length": 5,
      "weight": 2,
      "bundle": true,
      "description": "Package 2 to be repacked"
    },
    {
      "width": 2,
      "height": 8.2,
      "length": 5,
      "weight": 100,
      "bundle": true,
      "description": "Package 3 to be repacked, heavy"
    },
    {
      "width": 4,
      "height": 8.2,
      "length": 5,
      "weight": 4,
      "description": "Package 4 to NOT be repacked"
    }
  ]
}
{% endhighlight %}

##### Expected packages structure in response #####
{% highlight javascript %}
{
...
  "packages": [
    {
      "width": 4.0,
      "height": 8.2,
      "length": 5.0,
      "weight": 4.0,
      "quantity": 1,
      "description": "Package 4 to NOT be repacked",
      "identifier": "43906409963AAFC1",
      "barcode": {
        "symbology": "Code 128",
        "data": "43906409963AAFC1",
        "url": "https://api-sandbox.tiramizoo.com/v1/barcodes/43906409963AAFC1.png"
      }
    },
    {
      "width": 2.0,
      "height": 8.2,
      "length": 5.0,
      "weight": 30.0,
      "quantity": 1,
      "description": "Package 3 to be repacked, heavy",
      "identifier": "4D13BB4F4C64267F",
      "barcode": {
        "symbology": "Code 128",
        "data": "4D13BB4F4C64267F",
        "url": "https://api-sandbox.tiramizoo.com/v1/barcodes/4D13BB4F4C64267F.png"
      },
      "unpackable": true
    },
    {
      "width": 34.0,
      "height": 18.0,
      "length": 32.0,
      "weight": 5.0,
      "quantity": 1,
      "description": "Medium package",
      "identifier": "E1CCBE8E8EB1AEBB",
      "barcode": {
        "symbology": "Code 128",
        "data": "E1CCBE8E8EB1AEBB",
        "url": "https://api-sandbox.tiramizoo.com/v1/barcodes/E1CCBE8E8EB1AEBB.png"
      },
      "packages_inside": [
        {
          "width": 2,
          "height": 8.2,
          "length": 5,
          "weight": 2,
          "quantity": 1,
          "description": "Package 1 to be repacked"
        },
        {
          "width": 2,
          "height": 8.2,
          "length": 5,
          "weight": 2,
          "quantity": 1,
          "description": "Package 2 to be repacked"
        }
      ]
    }
  ]
...
}
{% endhighlight %}

*\*INFO: please notice that package 3 is not repacked even though it has the `bundle` parameter in the request. In case when the system is not able to find a package preset big enough to fit the package, an `unpackable` parameter is added.*

##### Try it yourself #####
{% highlight bash %}
curl -v -H 'Content-Type: application/json' -d '{"pickup":{"address_line":"Im Dol 1","postal_code":"14195","country_code":"de","name":"Alice Icealay","phone_number":"+491234567890","email":"alice@icealay.de","before":"2023-04-06T10:00:00.000Z","after":"2023-04-06T10:00:00.000Z"},"delivery":{"address_line":"Thujaweg 1","postal_code":"12437","country_code":"de","name":"Bob Obbay","phone_number":"+490987654321","email":"bob@obbay.de","before":"2023-04-06T11:30:00.000Z","after":"2023-04-06T11:30:00.000Z"},"description":"rubber chickens and chunky bacon","web_hook_url":"http://api.myshop.com/deliveries/update_state","external_id":"123-456-789","packages":[{"width":2,"height":8.2,"length":5,"weight":2,"bundle":true,"description":"Package 1 to be repacked"},{"width":2,"height":8.2,"length":5,"weight":2,"bundle":true,"description":"Package 2 to be repacked"},{"width":2,"height":8.2,"length":5,"weight":30,"bundle":true,"description":"Package 3 to be repacked, heavy"},{"width":4,"height":8.2,"length":5,"weight":4,"description":"Package 4 to NOT be repacked"}]}' "https://api-sandbox.tiramizoo.com/v1/orders?api_token=5715edce6630959b0e9c5659d323eae4"
{% endhighlight %}




Tracking Page
=========
----------------


When you successfully create an order, the response contain tracking_url

{% highlight javascript %}
[
  ...
  { "tracking_url" : "https://sandbox.tiramizoo.com/orders/TRACKING_CODE/tracking_status" }
  ...
]
{% endhighlight %}

* `TRACKING_CODE` - uniq tracking code, you are able to specify uniq 5 chars prefix called `short_code`. To do so please go to [user dashboard](https://sandbox.tiramizoo.com/dashboard/user/edit)


#### Tracking page
On tracking page you can check current status of your order

![Tracking url webpage](/assets/images/tracking_status.png)

[Tracking Page example](https://sandbox.tiramizoo.com/orders/TESTFWZ8VYWU/tracking_status)




Sandbox
=====
--------------

Please use [https://sandbox.tiramizoo.com](https://sandbox.tiramizoo.com) for testing purposes.

Orders will not be dispatched to a courier, however the user will be able to try out tiramizoo service.

Please take in mind that all records on the sandbox including account data are volatile, we might purge the database anytime.

# Test data

The sandbox has same service availability as production.

## Orders

### Creation

#### Successs response

{% highlight bash %}
curl -v -X POST -H 'Content-Type: application/json' -d '{"description":"huge metal bowl", "pickup":{"address_line":"Wasser 15", "country_code":"de", "name":"Sender_company", "phone_number":"123456", "postal_code":"14195", "after":"2023-12-14T11:30:00.000Z", "before":"2023-12-14T13:00:00.000Z"}, "delivery":{"address_line":"Bahnhof 32", "country_code":"de", "name":"Receiver_company", "phone_number":"123456", "postal_code":"14195", "after":"2023-12-14T11:30:00.000Z", "before":"2023-12-14T13:00:00.000Z"}, "packages":[{"width":48, "height":39, "length":40, "weight":21, "quantity":1, "description": "metal bowl"}]}' https://api-sandbox.tiramizoo.com/v1/orders?api_token=5715edce6630959b0e9c5659d323eae4
{% endhighlight %}

* `201 Created`

{% highlight javascript %}
[
  {...}
]
{% endhighlight %}

#### Error response when package's packages are invalid

{% highlight bash %}
curl -v -X POST -H 'Content-Type: application/json' -d '{"description":"huge metal bowl", "pickup":{"address_line":"Wasser 15", "country_code":"de", "name":"Sender_company", "phone_number":"123456", "postal_code":"14195", "after":"2023-12-14T11:30:00.000Z", "before":"2023-12-14T13:00:00.000Z"}, "delivery":{"address_line":"Bahnhof 32", "country_code":"de", "name":"Receiver_company", "phone_number":"123456", "postal_code":"14195", "after":"2023-12-14T11:30:00.000Z", "before":"2023-12-14T13:00:00.000Z"}, "packages":[{"width":48, "height":39, "length":0, "weight":21, "quantity":1, "description": "metal bowl"}]}' https://api-sandbox.tiramizoo.com/v1/orders?api_token=5715edce6630959b0e9c5659d323eae4
{% endhighlight %}

* `422 Unprocessable Entity`

{% highlight javascript %}
{
  "code": "validation_error",
  "message": "validation error",
  "errors": [{
    "field": "packages",
    "code": "invalid",
    "message": "Items is invalid"
  }]
}
{% endhighlight %}

#### Error response when package is too big

{% highlight bash %}
curl -v -X POST -H 'Content-Type: application/json' -d '{"description":"huge metal bowl", "pickup":{"address_line":"Wasser 15", "country_code":"de", "name":"Sender_company", "phone_number":"123456", "postal_code":"14195", "after":"2023-12-14T11:30:00.000Z", "before":"2023-12-14T13:00:00.000Z"}, "delivery":{"address_line":"Bahnhof 32", "country_code":"de", "name":"Receiver_company", "phone_number":"123456", "postal_code":"14195", "after":"2023-12-14T11:30:00.000Z", "before":"2023-12-14T13:00:00.000Z"}, "packages":[{"width":48, "height":39, "length":400000, "weight":21, "quantity":1, "description": "metal bowl"}]}' https://api-sandbox.tiramizoo.com/v1/orders?api_token=5715edce6630959b0e9c5659d323eae4
{% endhighlight %}

* `422 Unprocessable Entity`

{% highlight javascript %}
{
  "code": "validation_error",
  "message": "validation error",
  "errors": [{
    "field": "packages",
    "code": "dont_fit",
    "message": "Items are too big"
  }]
}
{% endhighlight %}

#### Error response when couriers are outside business hours

In given example business open hours are 8:00 to 21:00

{% highlight bash %}
curl -v -X POST -H 'Content-Type: application/json' -d '{"description":"huge metal bowl", "pickup":{"address_line":"Wasser 15", "country_code":"de", "name":"Sender_company", "phone_number":"123456", "postal_code":"14195", "after":"2023-12-14T22:00:00.000Z", "before":"2023-12-14T22:00:00.000Z"}, "delivery":{"address_line":"Bahnhof 32", "country_code":"de", "name":"Receiver_company", "phone_number":"123456", "postal_code":"14195", "after":"2023-12-14T23:30:00.000Z", "before":"2023-12-14T23:30:00.000Z"}, "packages":[{"width":48, "height":39, "length":400000, "weight":21, "quantity":1, "description": "metal bowl"}]}' https://api-sandbox.tiramizoo.com/v1/orders?api_token=5715edce6630959b0e9c5659d323eae4
{% endhighlight %}

* `422 Unprocessable Entity`

{% highlight javascript %}
{
  "code": "validation_error",
  "message": "validation error",
  "errors": [{
    "field": "timestamp",
    "code": "outside_business_hours",
    "message": "Couriers are outside business hours"
  }]
}
{% endhighlight %}

#### Error response when delivery time is ahead of pickup time less than 90 minutes

{% highlight bash %}
curl -v -X POST -H 'Content-Type: application/json' -d '{"description":"huge metal bowl", "pickup":{"address_line":"Wasser 15", "country_code":"de", "name":"Sender_company", "phone_number":"123456", "postal_code":"14195", "after":"2023-12-14T23:00:00.000Z", "before":"2023-12-14T23:00:00.000Z"}, "delivery":{"address_line":"Bahnhof 32", "country_code":"de", "name":"Receiver_company", "phone_number":"123456", "postal_code":"14195", "after":"2023-12-14T11:30:00.000Z", "before":"2023-12-14T13:00:00.000Z"}, "packages":[{"width":48, "height":39, "length":40, "weight":21, "quantity":1, "quantity": "metal bowl"}]}' https://api-sandbox.tiramizoo.com/v1/orders?api_token=5715edce6630959b0e9c5659d323eae4
{% endhighlight %}

* `422 Unprocessable Entity`

{% highlight javascript %}
{
  "code": "validation_error",
  "message": "validation error",
  "errors": [{
    "field": "delivery_before",
    "code": "before_time_limit",
    "message": "Delivery before must be at least 90 minutes ahead of pickup after"
  }]
}
{% endhighlight %}

#### Error response when delivery time is ahead of pickup time more than 6 hours

{% highlight bash %}
curl -v -X POST -H 'Content-Type: application/json' -d '{"description":"huge metal bowl", "pickup":{"address_line":"Wasser 15", "country_code":"de", "name":"Sender_company", "phone_number":"123456", "postal_code":"14195", "after":"2023-12-14T11:30:00.000Z", "before":"2023-12-14T13:00:00.000Z"}, "delivery":{"address_line":"Bahnhof 32", "country_code":"de", "name":"Receiver_company", "phone_number":"123456", "postal_code":"14195", "after":"2023-12-14T23:30:00.000Z", "before":"2023-12-14T23:30:00.000Z"}, "packages":[{"width":48, "height":39, "length":400000, "weight":21, "quantity":1, "description": "metal bowl"}]}' https://api-sandbox.tiramizoo.com/v1/orders?api_token=5715edce6630959b0e9c5659d323eae4
{% endhighlight %}

* `422 Unprocessable Entity`

{% highlight javascript %}
{
  "code": "validation_error",
  "message": "validation error",
  "errors": [{
    "field": "delivery_before",
    "code": "after_time_limit",
    "message": "Delivery before must be less than 6 hours ahead of pickup after"
  }]
}
{% endhighlight %}

#### Error response when pickup or delivery postal code is invalid

{% highlight bash %}
curl -v -X POST -H 'Content-Type: application/json' -d '{"description":"huge metal bowl", "pickup":{"address_line":"Wasser 15", "country_code":"de", "name":"Sender_company", "phone_number":"123456", "postal_code":"", "after":"2023-12-14T11:30:00.000Z", "before":"2023-12-14T13:00:00.000Z"}, "delivery":{"address_line":"Bahnhof 32", "country_code":"de", "name":"Receiver_company", "phone_number":"123456", "postal_code":"14195", "after":"2023-12-14T11:30:00.000Z", "before":"2023-12-14T13:00:00.000Z"}, "packages":[{"width":48, "height":39, "length":40, "weight":21, "quantity":1, "description": "metal bowl"}]}' https://api-sandbox.tiramizoo.com/v1/orders?api_token=5715edce6630959b0e9c5659d323eae4
{% endhighlight %}

* `422 Unprocessable Entity`

{% highlight javascript %}
{
  "code": "validation_error",
  "message": "validation error",
  "errors": [{
    "field": "pickup_postal_code",
    "code": "invalid",
    "message": "Pickup postal code is invalid"
  }]
}
or
{
  "code": "validation_error",
  "message": "validation error",
  "errors": [{
    "field": "delivery_postal_code",
    "code": "invalid",
    "message": "Delivery postal code is invalid"
  }]
}
{% endhighlight %}

### Order Cancelation

Order should have first status:
{% highlight javascript %}
{
  "cancellable": true
}
{% endhighlight %}

#### Success response

{% highlight bash %}
curl -v -X PUT -H 'Content-Type: application/json' -d '{"state" : "cancelled" }' https://api-sandbox.tiramizoo.com/v1/orders/:order_uuid?api_token=5715edce6630959b0e9c5659d323eae4
{% endhighlight %}

order_uuid - uniqe order identifier

* `200 OK`

Example response:

{% highlight bash %}
{
  "uuid": "%order_uuid",
  "state": "cancelled",
  "created_at":
  ...
}
{% endhighlight %}

#### Error response when order was already canceled

{% highlight bash %}
curl -v -X PUT -H 'Content-Type: application/json' -d '{"state" : "cancelled" }' https://api-sandbox.tiramizoo.com/v1/orders/:order_uuid?api_token=API_TOKEN
{% endhighlight %}

order_uuid - uniqe order identifier

* `422 Unprocessable Entity`

{% highlight javascript %}
{
  "code": "validation_error",
  "message": "validation error",
  "errors": [{
    "field": "state",
    "code": "invalid",
    "message": "State is invalid"
  }]
}
{% endhighlight %}

### Show Orders

#### Success response

{% highlight bash %}
curl -v -H 'Content-Type: application/json' https://api-sandbox.tiramizoo.com/v1/orders?external_id=EXTERNAL_ID&page=PAGE&api_token=API_TOKEN
{% endhighlight %}

external_id - an optional param with custom id that enables connecting tiramizoo orders with your internal infrastructure. This id does not need

page - an optional param (by default first page is returned)
per_page - an optional param (by default 100 records are returned. Max number allowed is 1000)

Your orders collection is paginated, i.e. to obtain 3-th page:

{% highlight bash %}
curl -v -H 'Content-Type: application/json' https://api-sandbox.tiramizoo.com/v1/orders?page=3&api_token=API_TOKEN
{% endhighlight %}

* `200 OK`

Example response:

{% highlight javascript %}
{
  "page": 3,
  "total_pages": 5,
  "per_page": 100,
  "orders": [{
    "uuid": "71e7acb2-3ed2-4137-ad62-446fb44f4299",
    "state": "processing",
    "created_at": "2023-12-19T08:27:05+01:00",
    "description": "Original BUSH",
    "courier_information": null,
    "external_id": "44444444422222222",
    "merge_field": null,
    "web_hook_url": "http://api.myshop.com/deliveries/update_state",
    "packages_price": null,
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
      "city": "M\u00fcnchen",
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
      "city": "Nettelsee",
      "postal_code": "80331",
      "country_code": "de",
      "after": "2023-12-19T20:00:00+01:00",
      "before": "2023-12-19T21:30:00+01:00"
    },
    "packages": [{
      "width": 20.0,
      "height": 30.0,
      "length": 40.0,
      "weight": 1.0,
      "quantity": 1,
      "description": "metal bowl"
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

### Show Order

#### Success response

{% highlight bash %}
curl -v -H 'Content-Type: application/json' https://api-sandbox.tiramizoo.com/v1/orders/:order_uuid?api_token=API_TOKEN
{% endhighlight %}

order_uuid - uniqe order identifier

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
  "packages_price": 0,
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
    "city": "Berlin",
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
    "city": "Berlin",
    "postal_code": "12437",
    "country_code": "de",
    "name": "Bob Obama",
    "phone_number": "+49099999999",
    "email": "bob@obama.de"
    "after": "2023-04-06T14:00:00.000Z",
    "before": "2023-04-06T15:00:00.000Z"
  },
  "packages": [
    {
      "width": 48,
      "height": 39,
      "length": 40,
      "weight": 20,
      "quantity": 1,
      "description": "metal bowl"
    }
  ]
}
{% endhighlight %}

#### Error response when no order found

{% highlight bash %}
curl -v -H 'Content-Type: application/json' https://api-sandbox.tiramizoo.com/v1/orders/:order_uuid?api_token=API_TOKEN
{% endhighlight %}

order_uuid - uniqe order identifier

* `404 Not Found`

## Time windows

{% highlight bash %}
curl -v -H 'Content-Type: application/json' https://api-sandbox.tiramizoo.com/v1/time_windows?api_token=API_TOKEN
{% endhighlight %}

#### Successs response

{% highlight javascript %}
One example time window:

{
  "city":"berlin",
  "pickup":{
    "from":"2013-02-20T08:00:00Z",
    "to":"2013-02-20T09:30:00Z"
  },
  "delivery":{
    "from":"2013-02-20T08:00:00Z",
    "to":"2013-02-20T09:30:00Z"
  }
}
{% endhighlight %}

## Service Availability

#### Success response

{% highlight bash %}
curl -v -H 'Content-Type: application/json' https://api-sandbox.tiramizoo.com/v1/service_availability?country_code=de&delivery_postal_code=14195&pickup_postal_code=14195
{% endhighlight %}

* `200 OK`

Example response:

{% highlight bash %}
  {
    "2023-04-16" => {"from" => {"hours" => 8, "minutes" => 0}, "to" => {"hours" => 20, "minutes" => 0}},
    "2023-04-17" => {"from" => {"hours" => 8, "minutes" => 0}, "to" => {"hours" => 20, "minutes" => 0}},
    "2023-04-19" => {"from" => {"hours" => 8, "minutes" => 0}, "to" => {"hours" => 12, "minutes" => 0}},
    "2023-04-20" => {"from" => {"hours" => 8, "minutes" => 0}, "to" => {"hours" => 20, "minutes" => 0}},
    "2023-04-21" => {"from" => {"hours" => 8, "minutes" => 0}, "to" => {"hours" => 11, "minutes" => 0}},
    "2023-04-22" => {"from" => {"hours" => 9, "minutes" => 0}, "to" => {"hours" => 13, "minutes" => 0}},
    "2023-04-23" => {"from" => {"hours" => 8, "minutes" => 0}, "to" => {"hours" => 20, "minutes" => 0}}
  }
{% endhighlight %}

#### Error response when country code or postal codes incorrect

{% highlight bash %}
curl -v -H 'Content-Type: application/json' https://api-sandbox.tiramizoo.com/v1/service_availability?country_code=de&delivery_postal_code=14195&pickup_postal_code=10
{% endhighlight %}


* `422 Unprocessable Entity`

{% highlight javascript %}
{
  "message" => "record_not_found",
      "code" => "record_not_found",
      "errors" => [{
        "field" => "city",
        "code" => "not_found",
        "message" => "We are not able to identify the city by postal codes"
      }]
  }
}
{% endhighlight %}



Example Shop Integration
======
---------------

On this page you can see how the real shop integration with Tiramizoo looks like:

### Step 1 - User added product to his card/basket

![User added product to his card/basket](/assets/images/step1.png)

### Step 2 - User provide billing information

![User provide billing information](/assets/images/step2.png)

### Step 3 - User choose delivery system

![User choose delivery system](/assets/images/step3.png)

### Step 4 - User confirm order

![User confirm order](/assets/images/step4.png)



