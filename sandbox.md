---
layout: page
title: Sandbox
---

You can access [https://sandbox.tiramizoo.com](https://sandbox.tiramizoo.com) for testing purposes.
All api requests should point to [https://api-sandbox.tiramizoo.com](https://api-sandbox.tiramizoo.com)

After registering account at the sandbox (that is just a copy of the production application), the sandbox API token
can be found on the profile page ([https://sandbox.tiramizoo.com/dashboard/development_settings/edit](https://sandbox.tiramizoo.com/dashboard/development_settings/edit)).

All orders and payments will not be dispatched to a courier, however the user will be able to try out tiramizoo and use it for
testing purposes.

Please take in mind that all records on the sandbox including account data are volatile, we might purge the database anytime.

# Test data

The sandbox only has quotes for Berlin, Germany defined, so please keep that in mind when testing.

## Quotes

{% highlight bash %}
curl -H 'Content-Type: application/json' -d '{"pickup_postal_code": "14195","delivery_postal_code": "14195"}' https://api-sandbox.tiramizoo.com/v1/quotes
{% endhighlight %}

#### Successs response

{% highlight javascript %}
{}
{% endhighlight %}

#### Error response when package is too big

{% highlight bash %}
curl -H 'Content-Type: application/json' -d '{"pickup_postal_code": "14195","delivery_postal_code": "14195","items":[{"width":42,"height":59,"length":3,"weight":50000}]}' https://api-sandbox.tiramizoo.com/v1/quotes
{% endhighlight %}

* `422 Unprocessable Entity`

{% highlight javascript %}
{
  "code": "validation_error",
  "message": "validation error",
  "errors": [{
    "field": "items",
    "code": "dont_fit",
    "message": "Items are too big"
  }]
}
{% endhighlight %}

## Orders

All test requests require valid **API_TOKEN** ([https://sandbox.tiramizoo.com/dashboard/user/edit](https://sandbox.tiramizoo.com/dashboard/user/edit))

* `401 Unauthorized` - API response when no or wrong API_TOKEN

### Creation

#### Successs response

{% highlight bash %}
curl -v -X POST -H 'Content-Type: application/json' -d '{"description":"huge metal bowl", "pickup":{"address_line":"Wasser 15", "country_code":"de", "name":"Sender_company", "phone_number":"123456", "postal_code":"14195", "after":"2023-12-14T11:30:00.000Z", "before":"2023-12-14T13:00:00.000Z"}, "delivery":{"address_line":"Bahnhof 32", "country_code":"de", "name":"Receiver_company", "phone_number":"123456", "postal_code":"14195", "after":"2023-12-14T11:30:00.000Z", "before":"2023-12-14T13:00:00.000Z"}, "items":[{"width":48, "height":39, "length":40, "weight":21, "quantity":1, "description": "metal bowl"}]}' https://api-sandbox.tiramizoo.com/v1/orders?api_token=5715edce6630959b0e9c5659d323eae4
{% endhighlight %}

* `201 Created`

{% highlight javascript %}
[
  {...}
]
{% endhighlight %}

#### Error response when package's items are invalid

{% highlight bash %}
curl -v -X POST -H 'Content-Type: application/json' -d '{"description":"huge metal bowl", "pickup":{"address_line":"Wasser 15", "country_code":"de", "name":"Sender_company", "phone_number":"123456", "postal_code":"14195", "after":"2023-12-14T11:30:00.000Z", "before":"2023-12-14T13:00:00.000Z"}, "delivery":{"address_line":"Bahnhof 32", "country_code":"de", "name":"Receiver_company", "phone_number":"123456", "postal_code":"14195", "after":"2023-12-14T11:30:00.000Z", "before":"2023-12-14T13:00:00.000Z"}, "items":[{"width":48, "height":39, "length":0, "weight":21, "quantity":1, "description": "metal bowl"}]}' https://api-sandbox.tiramizoo.com/v1/orders?api_token=5715edce6630959b0e9c5659d323eae4
{% endhighlight %}

* `422 Unprocessable Entity`

{% highlight javascript %}
{
  "code": "validation_error",
  "message": "validation error",
  "errors": [{
    "field": "items",
    "code": "invalid",
    "message": "Items is invalid"
  }]
}
{% endhighlight %}

#### Error response when package is too big

{% highlight bash %}
curl -v -X POST -H 'Content-Type: application/json' -d '{"description":"huge metal bowl", "pickup":{"address_line":"Wasser 15", "country_code":"de", "name":"Sender_company", "phone_number":"123456", "postal_code":"14195", "after":"2023-12-14T11:30:00.000Z", "before":"2023-12-14T13:00:00.000Z"}, "delivery":{"address_line":"Bahnhof 32", "country_code":"de", "name":"Receiver_company", "phone_number":"123456", "postal_code":"14195", "after":"2023-12-14T11:30:00.000Z", "before":"2023-12-14T13:00:00.000Z"}, "items":[{"width":48, "height":39, "length":400000, "weight":21, "quantity":1, "description": "metal bowl"}]}' https://api-sandbox.tiramizoo.com/v1/orders?api_token=5715edce6630959b0e9c5659d323eae4
{% endhighlight %}

* `422 Unprocessable Entity`

{% highlight javascript %}
{
  "code": "validation_error",
  "message": "validation error",
  "errors": [{
    "field": "items",
    "code": "dont_fit",
    "message": "Items are too big"
  }]
}
{% endhighlight %}

#### Error response when couriers are outside business hours

In given example business open hours are 8:00 to 21:00

{% highlight bash %}
curl -v -X POST -H 'Content-Type: application/json' -d '{"description":"huge metal bowl", "pickup":{"address_line":"Wasser 15", "country_code":"de", "name":"Sender_company", "phone_number":"123456", "postal_code":"14195", "after":"2023-12-14T22:00:00.000Z", "before":"2023-12-14T22:00:00.000Z"}, "delivery":{"address_line":"Bahnhof 32", "country_code":"de", "name":"Receiver_company", "phone_number":"123456", "postal_code":"14195", "after":"2023-12-14T23:30:00.000Z", "before":"2023-12-14T23:30:00.000Z"}, "items":[{"width":48, "height":39, "length":400000, "weight":21, "quantity":1, "description": "metal bowl"}]}' https://api-sandbox.tiramizoo.com/v1/orders?api_token=5715edce6630959b0e9c5659d323eae4
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
curl -v -X POST -H 'Content-Type: application/json' -d '{"description":"huge metal bowl", "pickup":{"address_line":"Wasser 15", "country_code":"de", "name":"Sender_company", "phone_number":"123456", "postal_code":"14195", "after":"2023-12-14T23:00:00.000Z", "before":"2023-12-14T23:00:00.000Z"}, "delivery":{"address_line":"Bahnhof 32", "country_code":"de", "name":"Receiver_company", "phone_number":"123456", "postal_code":"14195", "after":"2023-12-14T11:30:00.000Z", "before":"2023-12-14T13:00:00.000Z"}, "items":[{"width":48, "height":39, "length":40, "weight":21, "quantity":1, "quantity": "metal bowl"}]}' https://api-sandbox.tiramizoo.com/v1/orders?api_token=5715edce6630959b0e9c5659d323eae4
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
curl -v -X POST -H 'Content-Type: application/json' -d '{"description":"huge metal bowl", "pickup":{"address_line":"Wasser 15", "country_code":"de", "name":"Sender_company", "phone_number":"123456", "postal_code":"14195", "after":"2023-12-14T11:30:00.000Z", "before":"2023-12-14T13:00:00.000Z"}, "delivery":{"address_line":"Bahnhof 32", "country_code":"de", "name":"Receiver_company", "phone_number":"123456", "postal_code":"14195", "after":"2023-12-14T23:30:00.000Z", "before":"2023-12-14T23:30:00.000Z"}, "items":[{"width":48, "height":39, "length":400000, "weight":21, "quantity":1, "description": "metal bowl"}]}' https://api-sandbox.tiramizoo.com/v1/orders?api_token=5715edce6630959b0e9c5659d323eae4
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
curl -v -X POST -H 'Content-Type: application/json' -d '{"description":"huge metal bowl", "pickup":{"address_line":"Wasser 15", "country_code":"de", "name":"Sender_company", "phone_number":"123456", "postal_code":"", "after":"2023-12-14T11:30:00.000Z", "before":"2023-12-14T13:00:00.000Z"}, "delivery":{"address_line":"Bahnhof 32", "country_code":"de", "name":"Receiver_company", "phone_number":"123456", "postal_code":"14195", "after":"2023-12-14T11:30:00.000Z", "before":"2023-12-14T13:00:00.000Z"}, "items":[{"width":48, "height":39, "length":40, "weight":21, "quantity":1, "description": "metal bowl"}]}' https://api-sandbox.tiramizoo.com/v1/orders?api_token=5715edce6630959b0e9c5659d323eae4
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

Your orders collection is paginated, one page returns 25 orders, i.e. to obtain 3-th page:

{% highlight bash %}
curl -v -H 'Content-Type: application/json' https://api-sandbox.tiramizoo.com/v1/orders?page=3&api_token=API_TOKEN
{% endhighlight %}

* `200 OK`

Example response:

{% highlight javascript %}
{
  "page": 3,
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
    "items": [{
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
  "items": [
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
