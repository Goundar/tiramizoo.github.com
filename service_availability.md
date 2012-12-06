---
layout: page
title: Service Availability
---

This API provides informations in what hours system operates in given city and in which 
dates in next 30 days is unavailable

### Generate question

```
GET /service_availability
```

{% highlight javascript %}
{
  "city": "berlin"
}
{% endhighlight %}

* `city` required string containing city identifier

#### Response

* `200 OK` - response body contains open hours and closed days.
{% highlight javascript %}
{
  "open_hours": [8, 20],
  "closed_days": [
    {"year": 2012, "month": 12, "day": 24},
    {"year": 2012, "month": 12, "day": 24},
    {"year": 2012, "month": 12, "day": 30}
  ]
}
{% endhighlight %}

#### Errors

* `404 Not Found Error` - City could not be found (was not present, was not correct, etc.)

{% highlight javascript %}
{
  "message" => "record_not_found",
      "code" => "record_not_found",
      "errors" => [{
        "field" => "city",
        "code" => "not_found",
        "message" => "Given city doesn't exist"
      }]
  }
}
{% endhighlight %}
