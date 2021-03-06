---
layout: page
title: Quotes [Deprecated]
---

The Quotes API provides an easy way to verify service availability and packages.

### Create quote

```
POST /quotes
```

{% highlight javascript %}
{
  "pickup_postal_code": "14195",
  "delivery_postal_code": "12437"
}
{% endhighlight %}

or to validate packages also:

{% highlight javascript %}
{
  "pickup_postal_code": "14195",
  "delivery_postal_code": "12437",
  "packages": [
    {
      "width": 2,
      "height": 8.2,
      "length": 5,
      "weight": 2
    },
    {
      "width": 20.5,
      "height": 82,
      "length": 50,
      "weight": 7.9,
      "quantity": 12
    }
  ]
}
{% endhighlight %}

* `pickup_postal_code` required string containing pickup postal code
* `delivery_postal_code` required string containing delivery postal code
* `packages` (`items` will be deprecated) - optional array containing measurements of at least one package.
  `width`, `height`, `length` are required dimensions of your package
  in cm and `weight` in kg. Optional `quantity` parameter contains a
  number of packages to deliver (default is 1).

#### Response

* `200 OK` - Quote was validated without any error
{% highlight javascript %}
{}
{% endhighlight %}

##### Try it yourself #####
{% highlight bash %}
curl -v -H 'Content-Type: application/json' -d '{"pickup_postal_code":"14195","delivery_postal_code":"12437","packages":[{"width":2,"height":8.2,"length":5,"weight":2},{"width":20.5,"height":82,"length":50,"weight":7.9,"quantity":12}]}' "https://api-sandbox.tiramizoo.com/v1/quotes"
{% endhighlight %}

#### Errors

* `422 Validation Error` - some of required parameters are missing or invalid in the request. Please fix parameters and retry the request.

{% highlight javascript %}
{
  "message": "validation error",
  "code": "validation_error",
  "errors": [
    {
      "field": "delivery_postal_code",
      "code": "invalid",
      "message": "Delivery postal code is invalid"
    }
  ]
}
{% endhighlight %}

##### Try it yourself #####
{% highlight bash %}
curl -v -H 'Content-Type: application/json' -d '{"pickup_postal_code":"14195","delivery_postal_code":"INVALID","packages":[{"width":2,"height":8.2,"length":5,"weight":2},{"width":20.5,"height":82,"length":50,"weight":7.9,"quantity":12}]}' "https://api-sandbox.tiramizoo.com/v1/quotes"
{% endhighlight %}

[Examples](/sandbox.html#quotes)
