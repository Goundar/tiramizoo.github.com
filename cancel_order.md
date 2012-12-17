---
layout: page
title: Order Cancellation
---

### Order Cancellation

You can cancel only orders with attribute:

{% highlight javascript %}
{
  // ...
  "cancellable": true
  // ...
}
{% endhighlight %}


```
PUT /orders/%order.uuid%
```

{% highlight javascript %}
{
  "state": "cancelled"
}
{% endhighlight %}

#### Response

* `200 OK` - An Order was updated, response body contains its
  representation.

#### Errors

* `422 Validation Error` - some of required parameters are missing or
  invalid in the request. Please fix parameters and retry the request.

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

[Example](/sandbox.html#order_cancelation)