---
layout: page
title: Tracking Page
---

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