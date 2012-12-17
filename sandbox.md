---
layout: page
title: Sandbox
---

You can access [https://sandbox.tiramizoo.com](https://sandbox.tiramizoo.com) for testing purposes.
All api requests should point to [https://api-sandbox.tiramizoo.com](https://api-sandbox.tiramizoo.com)

After registering account at the sandbox (that is just a copy of the production application), the sandbox API token 
can be found on the profile page.

All orders and payments will not be dispatched to a courier, however the user will be able to try out tiramizoo and use it for
testing purposes.

Please take in mind that all records on the sandbox including account data are volatile, we might purge the database anytime.

# Test data

The sandbox only has quotes for Berlin, Germany defined, so please keep that in mind when testing.

## Quotes

{% highlight bash %}
curl -H 'Content-Type: application/json' -d '{"pickup_postal_code": "14195","delivery_postal_code": "14195","items":[{"width":42,"height":59,"length":3,"weight":5}]}' https://api-sandbox.tiramizoo.com/v1/quotes
{% endhighlight %}

#### Successs response

{% highlight javascript %}
{	
	"net" : 664,
	"tax_rate" : 19,
	"currency" : "EUR",
	"tax" : 126,
	"gross" : 790
}
{% endhighlight %}

#### Error response when package is too big

{% highlight bash %}
curl -H 'Content-Type: application/json' -d '{"pickup_postal_code": "14195","delivery_postal_code": "14195","items":[{"width":42,"height":59,"length":3,"weight":50000}]}' https://api-sandbox.tiramizoo.com/v1/quotes
{% endhighlight %}

`422` Unprocessable Entity

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

Known responses from quotes API, with given parameters:

#### Successs response

{% highlight bash %}
curl -v -X POST -H 'Content-Type: application/json' -d '{"description":"huge metal bowl", "pickup":{"address_line":"Wasser 15", "country_code":"de", "name":"Sender_company", "phone_number":"123456", "postal_code":"14195", "after":"2012-12-14T11:30:00.000Z", "before":"2012-12-14T13:00:00.000Z"}, "delivery":{"address_line":"Bahnhof 32", "country_code":"de", "name":"Receiver_company", "phone_number":"123456", "postal_code":"14195", "after":"2012-12-14T11:30:00.000Z", "before":"2012-12-14T13:00:00.000Z"}, "items":[{"width":48, "height":39, "length":40, "weight":21, "quantity":1}]}' https://api-sandbox.tiramizoo.com/v1/orders?api_token=API_TOKEN
{% endhighlight %}

`201 Created`

{% highlight javascript %}
[
  {...}
]
{% endhighlight %}

#### Error response when package's items are invalid

{% highlight bash %}
curl -v -X POST -H 'Content-Type: application/json' -d '{"description":"huge metal bowl", "pickup":{"address_line":"Wasser 15", "country_code":"de", "name":"Sender_company", "phone_number":"123456", "postal_code":"14195", "after":"2012-12-14T11:30:00.000Z", "before":"2012-12-14T13:00:00.000Z"}, "delivery":{"address_line":"Bahnhof 32", "country_code":"de", "name":"Receiver_company", "phone_number":"123456", "postal_code":"14195", "after":"2012-12-14T11:30:00.000Z", "before":"2012-12-14T13:00:00.000Z"}, "items":[{"width":48, "height":39, "length":0, "weight":21, "quantity":1}]}' https://api-sandbox.tiramizoo.com/v1/orders?api_token=API_TOKEN
{% endhighlight %}

`422` Unprocessable Entity

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
curl -v -X POST -H 'Content-Type: application/json' -d '{"description":"huge metal bowl", "pickup":{"address_line":"Wasser 15", "country_code":"de", "name":"Sender_company", "phone_number":"123456", "postal_code":"14195", "after":"2012-12-14T11:30:00.000Z", "before":"2012-12-14T13:00:00.000Z"}, "delivery":{"address_line":"Bahnhof 32", "country_code":"de", "name":"Receiver_company", "phone_number":"123456", "postal_code":"14195", "after":"2012-12-14T11:30:00.000Z", "before":"2012-12-14T13:00:00.000Z"}, "items":[{"width":48, "height":39, "length":400000, "weight":21, "quantity":1}]}' https://api-sandbox.tiramizoo.com/v1/orders?api_token=API_TOKEN
{% endhighlight %}

`422` Unprocessable Entity

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
curl -v -X POST -H 'Content-Type: application/json' -d '{"description":"huge metal bowl", "pickup":{"address_line":"Wasser 15", "country_code":"de", "name":"Sender_company", "phone_number":"123456", "postal_code":"14195", "after":"2012-12-14T22:00:00.000Z", "before":"2012-12-14T22:00:00.000Z"}, "delivery":{"address_line":"Bahnhof 32", "country_code":"de", "name":"Receiver_company", "phone_number":"123456", "postal_code":"14195", "after":"2012-12-14T23:30:00.000Z", "before":"2012-12-14T23:30:00.000Z"}, "items":[{"width":48, "height":39, "length":400000, "weight":21, "quantity":1}]}' https://api-sandbox.tiramizoo.com/v1/orders?api_token=API_TOKEN
{% endhighlight %}

`422` Unprocessable Entity

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
curl -v -X POST -H 'Content-Type: application/json' -d '{"description":"huge metal bowl", "pickup":{"address_line":"Wasser 15", "country_code":"de", "name":"Sender_company", "phone_number":"123456", "postal_code":"14195", "after":"2012-12-14T23:00:00.000Z", "before":"2012-12-14T23:00:00.000Z"}, "delivery":{"address_line":"Bahnhof 32", "country_code":"de", "name":"Receiver_company", "phone_number":"123456", "postal_code":"14195", "after":"2012-12-14T11:30:00.000Z", "before":"2012-12-14T13:00:00.000Z"}, "items":[{"width":48, "height":39, "length":40, "weight":21, "quantity":1}]}' https://api-sandbox.tiramizoo.com/v1/orders?api_token=API_TOKEN
{% endhighlight %}

`422` Unprocessable Entity

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
curl -v -X POST -H 'Content-Type: application/json' -d '{"description":"huge metal bowl", "pickup":{"address_line":"Wasser 15", "country_code":"de", "name":"Sender_company", "phone_number":"123456", "postal_code":"14195", "after":"2012-12-14T11:30:00.000Z", "before":"2012-12-14T13:00:00.000Z"}, "delivery":{"address_line":"Bahnhof 32", "country_code":"de", "name":"Receiver_company", "phone_number":"123456", "postal_code":"14195", "after":"2012-12-14T23:30:00.000Z", "before":"2012-12-14T23:30:00.000Z"}, "items":[{"width":48, "height":39, "length":400000, "weight":21, "quantity":1}]}' https://api-sandbox.tiramizoo.com/v1/orders?api_token=API_TOKEN
{% endhighlight %}

`422` Unprocessable Entity

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
curl -v -X POST -H 'Content-Type: application/json' -d '{"description":"huge metal bowl", "pickup":{"address_line":"Wasser 15", "country_code":"de", "name":"Sender_company", "phone_number":"123456", "postal_code":"", "after":"2012-12-14T11:30:00.000Z", "before":"2012-12-14T13:00:00.000Z"}, "delivery":{"address_line":"Bahnhof 32", "country_code":"de", "name":"Receiver_company", "phone_number":"123456", "postal_code":"14195", "after":"2012-12-14T11:30:00.000Z", "before":"2012-12-14T13:00:00.000Z"}, "items":[{"width":48, "height":39, "length":40, "weight":21, "quantity":1}]}' https://api-sandbox.tiramizoo.com/v1/orders?api_token=API_TOKEN
{% endhighlight %}

`422` Unprocessable Entity

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

## Order Cancelation

Order should have first status:
{% highlight javascript %}
{
  "cancellable": true 
}
{% endhighlight %}

#### Success response

{% highlight bash %}
curl -v -X PUT -H 'Content-Type: application/json' -d '{"state" : "cancelled" }' https://api-sandbox.tiramizoo.com/v1/orders/%order_uuid%?api_token=API_TOKEN
{% endhighlight %}

%order_uuid% - uniqe order identifier

`200 OK`

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
curl -v -X PUT -H 'Content-Type: application/json' -d '{"state" : "cancelled" }' https://api-sandbox.tiramizoo.com/v1/orders/%order_uuid%?api_token=API_TOKEN
{% endhighlight %}

%order_uuid% - uniqe order identifier

`422` Unprocessable Entity

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

## Service Availability

#### Success response

{% highlight bash %}
curl -v -H 'Content-Type: application/json' https://api-sandbox.tiramizoo.com/api/v1/service_availability?country_code=de&delivery_postal_code=14195&pickup_postal_code=14195
{% endhighlight %}

`200 OK`

{% highlight bash %}
{
  "open_hours": [8, 20],
  "closed_days": [
    {"year": 2012, "month": 12, "day": 24},
    {"year": 2012, "month": 12, "day": 24},
    {"year": 2012, "month": 12, "day": 30}
  ]
}
{% endhighlight %}

#### Error response when country code or postal codes incorrect 

{% highlight bash %}
curl -v -H 'Content-Type: application/json' https://api-sandbox.tiramizoo.com/api/v1/service_availability?country_code=de&delivery_postal_code=14195&pickup_postal_code=10
{% endhighlight %}


`422` Unprocessable Entity

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
