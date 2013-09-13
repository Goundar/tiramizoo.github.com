---
layout: page
title: Package labeling process
---

On [order creation process](/orders.html) tiramizoo service generates uniq identifier for each package.

![Generate identifier for a package](/assets/images/qr_step1.png)


##### Packages representation #####
{% highlight javascript %}
"packages": [
  {
    "width": 2,
    "height": 8.2,
    "length": 5,
    "weight": 2,
    "quantity": 1,
    "description": "chunky bacon",
    "identifier": "3E39C50E40226436",
    "qr_code_url": "/api/v1/qr_codes/3E39C50E40226436"
  },
  ...
]
{% endhighlight %}

 * `identifier` - 16 chars long, uniq identifier, contains only A-Z and 0-9
 * `qr_code_url` - URL to generate qr code label


QR code label needs to be generated for each package using package's `identifier` as a content.

![Generate QR code image for a package](/assets/images/qr_step2.png)

##### Notice: We encourage you to implement your own solution and not relay on QR codes API. Under high load it may not be as fast and reliable as dedicated solution #####

## QR code API ##

The QR codes API provides a simple way to generate QR code label for given identifier.

#### Get QR code ####

```
GET /qr_codes/:identifier
```

* `identifier` - Package identifier generated on [order creation process](/orders.html#show_order)

#### Response ####

QR code image in 'png' format

![QR code image response](/assets/images/qr_code.png)

##### Try it yourself #####

{% highlight bash %}
wget https://api-sandbox.tiramizoo.com/v1/qr_codes/3E39C50E40226436
{% endhighlight %}
