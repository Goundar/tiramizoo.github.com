---
layout: page
title: Time windows (Deprecated)
---

## Deprecation details
The feature will be dropped at latest at 01.08.2013, please use [Service Areas](/service_areas.html)

### List Default Time Windows

The API returns time windows for next 7 days for all supported cities

To calculate effective time windows tiramizoo takes into account **service working hours per city** and **special days (e.g. Christmas).** For more details please see our system [coverage](https://www.tiramizoo.com/coverage)

```
GET /time_windows
```

#### Response

{% highlight javascript %}
[
  {
    "city": "cologne",
    "pickup": {
      "from": "2013-03-13T07:00:00Z",
      "to": "2013-03-13T08:30:00Z"
    },
    "delivery": {
      "from": "2013-03-13T07:00:00Z",
      "to": "2013-03-13T08:30:00Z"
  }
  },
  {
    "city": "cologne",
    "pickup": {
      "from": "2013-03-13T07:30:00Z",
      "to": "2013-03-13T09:00:00Z"
    },
    "delivery": {
      "from": "2013-03-13T07:30:00Z",
      "to": "2013-03-13T09:00:00Z"
    }
  },
  {
    "city": "berlin",
    "pickup": {
      "from": "2013-03-13T07:00:00Z",
      "to": "2013-03-13T08:30:00Z"
    },
    "delivery": {
      "from": "2013-03-13T07:00:00Z",
      "to": "2013-03-13T08:30:00Z"
    }
  },
  ...
]
{% endhighlight %}

##### Try it yourself #####
{% highlight bash %}
curl -v https://api-sandbox.tiramizoo.com/v1/time_windows
{% endhighlight %}