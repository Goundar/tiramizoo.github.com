---
layout: page
title: Quotes
---
{% include JB/setup %}

The Quotes API provides an easy way of checking delivery prices in
tiramizoo. You need to have a quote to create an order when using the
[Orders API](/orders.html).

Create a quote
------------

### Request

```
POST /quotes
```

<pre>
```json
{
  "pickup": {
    "address_line_1": "Im Dol 1",
    "city": "Berlin",
    "postal_code": "14195",
    "country_code": "de",
    "delay": 15,
    "windows": [
      { "after": "2012-04-06T08:00:00.000Z", "before": "2012-04-06T12:00:00.000Z" },
      { "after": "2012-04-06T16:00:00.000Z", "before": "2012-04-06T20:00:00.000Z" }
    ]
  },
  "delivery": {
    "address_line_1": "Thujaweg 1",
    "address_line_2": "Ground floor",
    "city": "Berlin",
    "postal_code": "12437",
    "country_code": "de",
    "windows": [
      { "after": "2012-04-06T07:00:00.000Z", "before": "2012-04-06T19:00:00.000Z" }
    ]
  },
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
```
</pre>

* `pickup`, `delivery` are required parameters cointaining addresses
   from where to pick your items up and where to deliver them, and
   optionally a requested time windows of pickup and/or delivery.
  * `address_line_1` required string containing street name and
    building number.
  * `address_line_2` may contain some additional information for
    courier to locate the address, i.e. flat number, room number, etc.
  * `city` optional string containing city name
  * `postal_code` required string containing postal code
  * `country_code` required string containing country code
  * `windows` - an Array of objects containing timestamps of possible
    pickup/delivery windows. See below for other ways to specify
    possible pickup and delivery times.
  * `delay` - optional field, specified on `pickup` element
    only. `delay` (in minutes) denotes how long the item(s) are going
    to be prepared for courier to pick up.
* `items` - an array containing measurements of at least one item.
  `width`, `height`, `length` are required dimensions of your package
  in cm and `weight` in kg. Optional `quantity` parameter contains a
  number of items to deliver (default is 1).

### Alternative ways to specify pickup and delivery times

* **none** - we will try to pick up and deliver your item(s) during the next
    90 minutes.

* `intervals` - a boolean value. Specified on delivery element.
  When `intervals` is `true`, we return multiple quotes, one for
  each hourly delivery window during next 7 days according to
  specified pickup windows. This lets you create a nice UI for users
  where they pick the day and a specific hour of delivery. If possible,
  the first offered quote is the "now" quote, meaning that delivery
  will happen during the next 90 minutes. Otherwise, the first quote
  has a delivery window that is at least 2 hours from now.

### Response

* `200 OK` - Quote(s) were created, response body contains one or more
  quotes in an Array.

<pre>
```json
[
  {
    "pickup": {
      "address_line_1": "Im Dol 1",
      "city": "Berlin",
      "postal_code": "14195",
      "country_code": "de",
      "after": "2012-04-12T07:59:33.432Z",
      "before": "2012-04-12T08:02:13.816Z"
    },
    "delivery": {
      "address_line_1": "Thujaweg 1",
      "address_line_2": "Ground floor",
      "city": "Berlin",
      "postal_code": "12437",
      "country_code": "de",
      "after": "2012-04-12T08:02:13.816Z",
      "before": "2012-04-12T09:00:22.984Z"
    },
    "items": [
      {
        "width": 2,
        "height": 8.2,
        "length": 5,
        "weight": 2,
        "quantity": 1
      },
      {
        "width": 20.5,
        "height": 82,
        "length": 50,
        "weight": 7.9,
        "quantity": 42
      }
    ],
    "price": {
      "net": 590,
      "currency": "EUR",
      "tax_rate": 19,
      "tax": 112,
      "gross": 702
    },
    "created_at": "2012-04-05T09:04:02.606Z",
    "signature": "fdbb1f868a9cd0e01f239d2330fb133fa7cedd38"
  }
]
```
</pre>

* `pickup` and `delivery` contain nicely formatted addresses that you
  asked for, together with `lat` and `lng` coordinates. `before` and
  `after` will always be present, even if omitted in request.
* `items` is an array of items with `width`, `height`, `length`,
  `weight` and `quantity` (even if it was ommitted).
* `price` object contains `net` and `currency` properties, so `590`
  and `"EUR"` stands for €5.90 net. `tax_rate` property contains a tax
  rate in percents. `tax` is calulcated tax (€1.12) `gross` is actual
  gross price of the delivery (€7.02).
* `created_at` property tells when the quote was generated, please
  notice that quotes are valid only for a short period of time (10
  minutes).
* `signature` property contains a signature based on the other
  properties of the quote.

### Errors

* `422 Validation Error` - some of required parameters are missing or
  invalid in the request. Please fix parameters and retry the request.

<pre>
```json
{
  "message": "validation error",
  "code": "validation_error",
  "errors": [
    "pickup address_line_1 is missing"
  ]
}
```
</pre>

* `422 Geocoding Failed` - geocoding process of one given address
  failed. Ensure that the addresses are correct and try again.

<pre>
```json
{
  "message": "geocoding failed",
  "code": "geocoding_failed"
}
```
</pre>

* `422 No Couriers Found` - no couriers are available at given
  locations. Your area might not yet be supported by tiramizoo.

<pre>
```json
{
  "message": "no couriers found",
  "code": "no_couriers_found"
}
```
</pre>

* `422 Distance Too High` - no couriers at your area can handle a
  delivery with given distance.

<pre>
```json
{
  "message": "distance too high",
  "code": "distance_too_high"
}
```
</pre>

* `422 Item Too Big` - no couriers at your location can handle an item
  with measurements you requested.

<pre>
```json
{
  "message": "item too big",
  "code": "item_too_big"
}
```
</pre>

* `422 Outside Business Hours` - couriers at your location are not
  available at the moment. Please try again later.

<pre>
```json
{
  "message": "no couriers can handle delivery at the moment",
  "code": "outside_business_hours"
}
```
</pre>
