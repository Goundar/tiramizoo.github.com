---
layout: page
title: Service Areas
---

### List Service Areas

The API returns postal codes and time windows for next 7 days of covered areas.


```
GET /v1/service_areas?api_token=API_TOKEN
```

* `api_token` - API TOKEN is optional. When given system checks if user has custom time windows specified. If yes, user's custom time windows are returned otherwise fallbacks to default time windows.
* `express_from` - **\[ISO8601 Date URI escaped\]** needs to be combined with `express_to` parameter. Allows to specify start time for express time windows.
* `express_to` - **\[ISO8601 Date URI escaped\]** does not need to be combined with `express_from` parameter, in this case `express_from` is set to current time. Allows to specify end time for express time windows. Can not exceed 7 days limit.
* `standard_from` - **\[ISO8601 Date URI escaped\]** needs to be combined with `standard_to` parameter. Allows to specify start time for standard time windows.
* `standard_to` - **\[ISO8601 Date URI escaped\]** does not need to be combined with `standard_from` parameter, in this case `standard_from` is set to current time. Allows to specify end time for express time windows. Can not exceed 7 days limit.

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

* `postal_codes` - all supported postal codes from an area.
* `time_windows["delivery_type"]` - delivery types are [**standard**, **weekend**, **standard_weekend**, **express_weekend**]. Delivery type has influence on price. Therefore, it is useful for final customer price calculation. By default only one **express time window** per area is returned. If you need more please use `express_from` and `express_to` parameters described above.

#### Errors ####

* `404 Not Found` - Area with given postal code is not supported
* `400 Bad Request` - Given range date is invalid or range is over 7 days limit

##### Try it yourself #####
{% highlight bash %}
curl -v https://api-sandbox.tiramizoo.com/v1/service_areas?api_token=5715edce6630959b0e9c5659d323eae4
{% endhighlight %}

{% highlight bash %}
# without api_token
curl -v https://api-sandbox.tiramizoo.com/v1/service_areas
{% endhighlight %}

{% highlight bash %}
# express_from and express_to
# `EXPRESS_FROM` - date in range: "now..1 week", format example: 2023-04-07T10%3A00%3A00.000Z
# `EXPRESS_TO` - date in range: "now..1 week", format example: 2023-04-07T10%3A00%3A00.000Z
curl -v https://api-sandbox.tiramizoo.com/v1/service_areas?express_from=EXPRESS_FROM&express_to=EXPRESS_TO
{% endhighlight %}

{% highlight bash %}
# only express_to - express_from will be set to current time by default
# `EXPRESS_FROM` - date in range: "now..1 week", format example: 2023-04-07T10%3A00%3A00.000Z
# `EXPRESS_TO` - date in range: "now..1 week", format example: 2023-04-07T10%3A00%3A00.000Z
curl -v https://api-sandbox.tiramizoo.com/v1/service_areas?express_to=EXPRESS_TO
{% endhighlight %}

{% highlight bash %}
# invalid date for range
curl -v https://api-sandbox.tiramizoo.com/v1/service_areas?express_to=INVALID_DATE
{% endhighlight %}

{% highlight bash %}
# range exceeds limit
curl -v https://api-sandbox.tiramizoo.com/v1/service_areas?express_to=2050-04-07T10%3A00%3A00.000Z
{% endhighlight %}

### Show Service Area ###

Return area containing given postal code.

```
GET /v1/service_areas/:postal_code?api_token=API_TOKEN
```

* `api_token` - API TOKEN is optional. When given system checks if user has custom time windows specified. If yes, user's custom time windows are returned otherwise fallbacks to default time windows.
* `postal_code` - postal code which belongs to desired service area
* `express_from` - **\[ISO8601 Date URI escaped\]** needs to be combined with `express_to` parameter. Allows to specify start time for express time windows.
* `express_to` - **\[ISO8601 Date URI escaped\]** does not need to be combined with `express_from` parameter, in this case `express_from` is set to current time. Allows to specify end time for express time windows. Can not exceed 7 days limit.
* `standard_from` - **\[ISO8601 Date URI escaped\]** needs to be combined with `standard_to` parameter. Allows to specify start time for standard time windows.
* `standard_to` - **\[ISO8601 Date URI escaped\]** does not need to be combined with `standard_from` parameter, in this case `standard_from` is set to current time. Allows to specify end time for express time windows. Can not exceed 7 days limit.

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

#### Errors ####

* `404 Not Found` - Area with given postal code is not supported
* `400 Bad Request` - Given range date is invalid or range is over 7 days limit

##### Try it yourself #####
{% highlight bash %}
curl -v https://api-sandbox.tiramizoo.com/v1/service_areas/14195?api_token=5715edce6630959b0e9c5659d323eae4
{% endhighlight %}

##### Try it yourself #####
{% highlight bash %}
curl -v https://api-sandbox.tiramizoo.com/v1/service_areas/INVALID
{% endhighlight %}

{% highlight bash %}
# express_from and express_to
# `EXPRESS_FROM` - date in range: "now..1 week", format example: 2023-04-07T10%3A00%3A00.000Z
# `EXPRESS_TO` - date in range: "now..1 week", format example: 2023-04-07T10%3A00%3A00.000Z
curl -v https://api-sandbox.tiramizoo.com/v1/service_areas/14195?express_from=EXPRESS_FROM&express_to=EXPRESS_TO
{% endhighlight %}
