---
layout: page
title: Package Pricing
---

### Retrieve package prices

```
GET /v1/postal_code_matrices/:postal_code?api_token=API_TOKEN
```

* `api_token` - required.
* `postal_code` - required.

#### Response

* `200 OK`

Package prices depend on three factors:

* __distance__ (calculated as driving distance between centroids of two postal codes)
* __delivery_type__ (standard, express)
* __package_size__ (M, L, XL, XXL)

Response contains all supported postal codes that match requested __postal_code__ with accompanying pricing data.

Example response:

{% highlight javascript %}
[
   {
      "id":"10115_14195",
      "postal_codes":[
         "10115",
         "14195"
      ],
      "distance":13.3688,
      "prices":{
         "standard":[
            {
               "size":"M",
               "price_cents":2195
            },
            {
               "size":"L",
               "price_cents":2395
            },
            {
               "size":"XL",
               "price_cents":2795
            },
            {
               "size":"XXL",
               "price_cents":3395
            }
         ],
         "express":[
            {
               "size":"M",
               "price_cents":2895
            },
            {
               "size":"L",
               "price_cents":3195
            },
            {
               "size":"XL",
               "price_cents":3695
            }
         ]
      }
   },
   ...
]
{% endhighlight %}

* `id` - Unique identifier that allows fast lookup for given pair of postal codes, For example, if you wish to find package prices delivered from __14195__ to __10115__, you should sort them and join with underscore character __"_"__, so __id__ of this mapping becomes __10115_14195__. If there is no object with desired __id__, it means that delivery between given pair of postal codes is not possible.
* `postal_codes` - Pair of postal codes between which delivery is possible.
* `distance` - driving distance between centroids of given postal codes.
* `prices` - prices for all combinations of __package_size__ and __delivery_type__ available for you account. If price is not present, it means that delivery of given __package_size__ and __delivery_type__ is not possible. Most common use case is that we don't want support __express delivery__ of XXL packages for distance greater than specified.
