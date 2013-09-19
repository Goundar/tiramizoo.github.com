---
layout: page
title: Package labelling process
---

On [order creation process](/orders.html) tiramizoo service generates unique identifier for each package.

![Generate identifier for a package](/assets/images/barcode_generation_process.png)


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
![Barcode](https://www.tiramizoo.com/api/v1/barcodes/3E39C50E40226436.png)
