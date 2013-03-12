---
layout: page
title: Service Areas
---

### List Service Areas

The API returns postal codes of available areas.
It also includes custom time windows when user authenticates via API token,
otherwise includes default time windows.

```
GET /v1/service_areas?api_token=API_TOKEN
```

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

### Show Service Area

Same as list, but scoped to given postal code.

```
GET /v1/service_areas/:postal_code?api_token=API_TOKEN
```

postal_code - scope response to area having given postal code

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

#### Errors

* `404 Not Found` - Area with given postal code is not supported
