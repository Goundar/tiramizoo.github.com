---
layout: page
title: Order Status
---

The Tiramizoo API provides a simple 2 ways of tracking the status of orders you
create in the API.

### Web Hooks

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

#### Airbrake support

Additionaly you can provide your AIRBRAKE_API_KEY ([More about Airbrake](https://airbrakeapp.com/pages/home) ) on [www.tiramizoo.com/dashboard/development_settings/edit](www.tiramizoo.com/dashboard/development_settings/edit)

and in a case web hook notification fails we will send you error notification to your airbrake account

### Tracking Url

When you successfully create an order, the response contain tracking_url

On tracking_url you can check current status of your order

#### Example of creation new order:

{% highlight bash %}
curl -v -X POST -H 'Content-Type: application/json' -d '{"description":"huge metal bowl", "pickup":{"address_line":"Wasser 15", "country_code":"de", "name":"Sender_company", "phone_number":"123456", "postal_code":"14195", "after":"2012-12-14T11:30:00.000Z", "before":"2012-12-14T13:00:00.000Z"}, "delivery":{"address_line":"Bahnhof 32", "country_code":"de", "name":"Receiver_company", "phone_number":"123456", "postal_code":"14195", "after":"2012-12-14T11:30:00.000Z", "before":"2012-12-14T13:00:00.000Z"}, "items":[{"width":48, "height":39, "length":40, "weight":21, "quantity":1, "description": "bowl"}]}' https://api-sandbox.tiramizoo.com/v1/orders?api_token=API_TOKEN
{% endhighlight %}

`201 Created`

{% highlight javascript %}
[
  ...
  { "tracking_url" : "https://sandbox.tiramizoo.com/orders/TRACKING_CODE/tracking_status" }
  ...
]
{% endhighlight %}

TRACKING_CODE - uniq order identifier

#### Screenshot example of tracking_url

![Tracking url webpage](/assets/images/tracking_status.png)
