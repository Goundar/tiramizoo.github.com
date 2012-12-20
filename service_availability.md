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
  "2012-04-16" => {"from" => {"hours" => 8, "minutes" => 0}, "to" => {"hours" => 20, "minutes" => 0}},
  "2012-04-17" => {"from" => {"hours" => 8, "minutes" => 0}, "to" => {"hours" => 20, "minutes" => 0}},
  "2012-04-19" => {"from" => {"hours" => 8, "minutes" => 0}, "to" => {"hours" => 12, "minutes" => 0}},
  "2012-04-20" => {"from" => {"hours" => 8, "minutes" => 0}, "to" => {"hours" => 20, "minutes" => 0}},
  "2012-04-21" => {"from" => {"hours" => 8, "minutes" => 0}, "to" => {"hours" => 11, "minutes" => 0}},
  "2012-04-22" => {"from" => {"hours" => 9, "minutes" => 0}, "to" => {"hours" => 13, "minutes" => 0}},
  "2012-04-23" => {"from" => {"hours" => 8, "minutes" => 0}, "to" => {"hours" => 20, "minutes" => 0}}
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
