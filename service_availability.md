---
layout: page
title: Service Availability
---

This API provides informations:
- about working service hours
- closed days in next week

### Generate question

```
GET /api/v1/service_availability?country_code=COUNTRY_CODE&delivery_postal_code=DELIVERY_POSTAL_CODE&pickup_postal_code=PICKUP_POSTAL_CODE
```

COUNTRY_CODE, DELIVERY_POSTAL_CODE, PICKUP_POSTAL_CODE are required

#### Response

* `200 OK` 

Example response:

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
        "message" => "We are not able to identify the city by postal codes"
      }]
  }
}
{% endhighlight %}

[Examples](/sandbox.html#service_availability)
