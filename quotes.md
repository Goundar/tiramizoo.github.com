---
layout: page
title: Quotes
---

The Quotes API provides an easy way of checking delivery prices in
tiramizoo.

### Create quote

```
POST /quotes
```

{% highlight javascript %}
{
  "pickup_postal_code": "14195",
  "delivery_postal_code": "12437",
  "items": [
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
      "quantity": 42
    }
  ]
}
{% endhighlight %}

* `pickup_postal_code` required string containing pickup postal code
* `delivery_postal_code` required string containing pickup postal code
* `items` - an array containing measurements of at least one item.
  `width`, `height`, `length` are required dimensions of your package
  in cm and `weight` in kg. Optional `quantity` parameter contains a
  number of items to deliver (default is 1).

#### Response

* `200 OK` - Quote(s) were created, response body contains the quote.
{% highlight javascript %}
{
  "net": 664,
  "gross": 790,
  "tax_rate": 19,
  "tax": 126,
  "currency": "EUR"
}
{% endhighlight %}

* `net`, `gross` and `tax` contain values in cents.
* `tax` is in percents

#### Errors

* `422 No Couriers Found` - no couriers are available at given
  locations. Your area might not yet be supported by tiramizoo.

{% highlight javascript %}
{
  "message": "no couriers found",
  "code": "no_couriers_found"
}
{% endhighlight %}
