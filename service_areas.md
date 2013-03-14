---
layout: page
title: Service Areas
---

### List Service Areas

The API returns postal codes and time windows for next 7 days of covered areas.


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

* `api_token` - API TOKEN is optional. When given system checks if user has custom time windows specified. If yes, user's custom time windows are returned otherwise fallbacks to default time windows.

##### Try it yourself #####
{% highlight bash %}
curl -v https://api-sandbox.tiramizoo.com/v1/service_areas?api_token=5715edce6630959b0e9c5659d323eae4
{% endhighlight %}

{% highlight bash %}
curl -v https://api-sandbox.tiramizoo.com/v1/service_areas
{% endhighlight %}

### Show Service Area

Return area containing given postal code.

```
GET /v1/service_areas/:postal_code?api_token=API_TOKEN
```

* `api_token` - API TOKEN is optional. When given system checks if user has custom time windows specified. If yes, user's custom time windows are returned otherwise fallbacks to default time windows.
* `postal_code` - postal code which belongs to desired service area

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

##### Try it yourself #####
{% highlight bash %}
curl -v https://api-sandbox.tiramizoo.com/v1/service_areas/14195?api_token=5715edce6630959b0e9c5659d323eae4
{% endhighlight %}

#### Errors

* `404 Not Found` - Area with given postal code is not supported

##### Try it yourself #####
{% highlight bash %}
curl -v https://api-sandbox.tiramizoo.com/v1/service_areas/INVALID
{% endhighlight %}