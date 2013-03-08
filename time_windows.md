---
layout: page
title: Time windows
---

### List Time Windows (deprecated and will be stopped at the latest at 01.08.2013, please use [Service Areas](/service_areas.html))

The API returns scaled time windows in a scope of a city
The API take into account:
- service working hours
- special days (e.g. Christmas)

Fragment of response:

{% highlight javascript %}

...},
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
},
{...
{% endhighlight %}

[Example](/sandbox.html#time_windows)

