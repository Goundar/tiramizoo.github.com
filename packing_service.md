---
layout: page
title: Packing Service
---

Tiramizoo offers a packing service for when creating orders. 

The reasoning behind this is that tiramizoo.com will deliver and charge based on the number and size of packages sent. Therefore it is vital to submit the correct number of packages to tiramizoo.

There are two different settings influencing the pricing and packing: 

* The account's package sizes contain dimensions, weights and prices that were negotiated in the contracts. Those are set up by your key account manager and can not be changed by the account owner.
* The account's package presets contain user specific package sizes and dimensions that can be given a special name. Those presets will be used in the user interface and also be used to pack products if the packing service is used. The price for the package presets will be taken from the smallest package size where it fits into. Package presets can be customized by the account owner in the user interface.

Just mark packages (in reality probably products) which are supposed to be repacked and the system will pack them into the account's package presets and return the packing information.

We will then charge according to those packages, but the courier will also only expect to get those packages to deliver, so make sure they match.

### Create Order

```
POST /orders?api_token=API_TOKEN
```
{% highlight javascript %}
{
  "pickup": {
    ...
  },
  "delivery": {
    ...
  },
  ...
  "packages": [
    {
      "width": 2,
      "height": 8.2,
      "length": 5,
      "weight": 2,
      "bundle": true,
      "description": "Package 1 to be repacked"
    },
    {
      "width": 2,
      "height": 8.2,
      "length": 5,
      "weight": 2,
      "bundle": true,
      "description": "Package 2 to be repacked"
    },
    {
      "width": 2,
      "height": 8.2,
      "length": 5,
      "weight": 100,
      "bundle": true,
      "description": "Package 3 to be repacked, heavy"
    },
    {
      "width": 4,
      "height": 8.2,
      "length": 5,
      "weight": 4,
      "description": "Package 4 to NOT be repacked"
    }
  ]
}
{% endhighlight %}

##### Expected packages structure in response #####
{% highlight javascript %}
{
...
  "packages": [
    {
      "width": 4.0,
      "height": 8.2,
      "length": 5.0,
      "weight": 4.0,
      "quantity": 1,
      "description": "Package 4 to NOT be repacked",
      "identifier": "43906409963AAFC1",
      "qr_code_url": "/api/v1/qr_codes/43906409963AAFC1"
    },
    {
      "width": 2.0,
      "height": 8.2,
      "length": 5.0,
      "weight": 30.0,
      "quantity": 1,
      "description": "Package 3 to be repacked, heavy",
      "identifier": "4D13BB4F4C64267F",
      "qr_code_url": "/api/v1/qr_codes/4D13BB4F4C64267F",
      "unpackable": true
    },
    {
      "width": 34.0,
      "height": 18.0,
      "length": 32.0,
      "weight": 5.0,
      "quantity": 1,
      "description": "Medium package",
      "identifier": "E1CCBE8E8EB1AEBB",
      "qr_code_url": "/api/v1/qr_codes/E1CCBE8E8EB1AEBB",
      "packages_inside": [
        {
          "width": 2,
          "height": 8.2,
          "length": 5,
          "weight": 2,
          "quantity": 1,
          "description": "Package 1 to be repacked"
        },
        {
          "width": 2,
          "height": 8.2,
          "length": 5,
          "weight": 2,
          "quantity": 1,
          "description": "Package 2 to be repacked"
        }
      ]
    }
  ]
...
}
{% endhighlight %}

*\*INFO: please notice that package 3 is not repacked even though it has `bundle` parameter in request. In case when system is not able to find package preset big enough to fit the package `unpackable` parameter is added.*

##### Try it yourself #####
{% highlight bash %}
curl -v -H 'Content-Type: application/json' -d '{"pickup":{"address_line":"Im Dol 1","postal_code":"14195","country_code":"de","name":"Alice Icealay","phone_number":"+491234567890","email":"alice@icealay.de","before":"2023-04-06T10:00:00.000Z","after":"2023-04-06T10:00:00.000Z"},"delivery":{"address_line":"Thujaweg 1","postal_code":"12437","country_code":"de","name":"Bob Obbay","phone_number":"+490987654321","email":"bob@obbay.de","before":"2023-04-06T11:30:00.000Z","after":"2023-04-06T11:30:00.000Z"},"description":"rubber chickens and chunky bacon","web_hook_url":"http://api.myshop.com/deliveries/update_state","external_id":"123-456-789","packages":[{"width":2,"height":8.2,"length":5,"weight":2,"bundle":true,"description":"Package 1 to be repacked"},{"width":2,"height":8.2,"length":5,"weight":2,"bundle":true,"description":"Package 2 to be repacked"},{"width":2,"height":8.2,"length":5,"weight":30,"bundle":true,"description":"Package 3 to be repacked, heavy"},{"width":4,"height":8.2,"length":5,"weight":4,"description":"Package 4 to NOT be repacked"}]}' "https://api-sandbox.tiramizoo.com/v1/orders?api_token=5715edce6630959b0e9c5659d323eae4"
{% endhighlight %}
