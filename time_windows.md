---
layout: page
title: Time windows
---

User have to specify delivery & pickup times in order to create

The gap between `delivery[before]` & `pickup[after]` must be in 90 minutes to 6 hours

The API returns scaled time windows in a scope of a city 
The Api take into account:
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

