---
layout: page
title: Time windows
---

## version 2 (v2)

### List Time Windows

The API returns custom time windows when user authenticates via API token,
otherwise returns default time windows.

```
GET /v2/time_windows?api_token=API_TOKEN
```

#### Response

* `200 OK`

Example response:

{% highlight javascript %}
[
  {
    "postal_codes": ["50667", "50667", ...],
    "time_windows": [{
      "pickup":{
        "from":"2013-02-20T08:00:00Z",
        "to":"2013-02-20T09:30:00Z"
      },
      "delivery":{
        "from":"2013-02-20T08:00:00Z",
        "to":"2013-02-20T09:30:00Z"
      }
    },
    {...}]
  },
  {...}
]
{% endhighlight %}

### Show Time Windows

The API returns custom time windows when user authenticates via API token,
otherwise returns default time windows scoped to given postal code.

```
GET /v2/time_windows/:postal_code?api_token=API_TOKEN
```

postal_code - scope response to area having given postal code

#### Response

* `200 OK`

Example response:

{% highlight javascript %}
{
  "postal_codes": ["50667", "50667", ...],
  "time_windows": [{
    "pickup": {
      "from": "2013-02-20T08:00:00Z",
      "to": "2013-02-20T09:30:00Z"
    },
    "delivery": {
      "from": "2013-02-20T08:00:00Z",
      "to": "2013-02-20T09:30:00Z"
    }
  },
  {...}]
}
{% endhighlight %}

#### Errors

* `404 Not Found` - Area with given postal code is not supported

## version 1 (v1)

### List Time Windows

The API returns scaled time windows in a scope of a city
The API take into account:
- service working hours
- special days (e.g. Christmas)

Fragment of response:

{% highlight javascript %}

...},
{
  "city":"berlin",
  "pickup":{
    "from":"2013-02-20T08:00:00Z",
    "to":"2013-02-20T09:30:00Z"
  },
  "delivery":{
    "from":"2013-02-20T08:00:00Z",
    "to":"2013-02-20T09:30:00Z"
  }
},
{...
{% endhighlight %}

[Example](/sandbox.html#time_windows)

