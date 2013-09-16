---
layout: page
title: Package labeling process
---

On [order creation process](/orders.html) tiramizoo service generates unique identifier for each package.

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
    "barcode:": {
      "symbology": "Code 128",
      "data": "3E39C50E40226436",
      "url": "https://api-sandbox.tiramizoo.com/v1/barcodes/3E39C50E40226436.png"
    }
  },
  ...
]
{% endhighlight %}

 * `barcode.symbology` - Barcode symbology applied ([Code 128](http://en.wikipedia.org/wiki/Code_128))
 * `barcode.data`      - Data encoded in barcode
 * `barcode.url`       - URL to download barcode label


##### Example barcode #####
![Barcode](/assets/images/3E39C50E40226436.png)
